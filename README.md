# Lysosomes-Detector-GUI
Interactive Python GUI for automated 3D lysosome detection, cell segmentation, signal quantification, and visualization from multichannel TIFF/CZI microscopy datasets. Includes Napari-based editing, per-cell analysis, video generation, and export of quantitative results.

The software provides an intuitive graphical interface for analyzing TIFF and Zeiss CZI image stacks, producing quantitative measurements, publication-quality visualizations, videos, and editable results through Napari.

---

## Features

- 🔬 Automatic 3D lysosome detection
- Cell segmentation using adaptive thresholding and watershed algorithms
- 📂 Supports TIFF and Zeiss CZI microscopy files
- 📏 Automatic extraction of voxel dimensions from image metadata
- 🖥️ User-friendly graphical interface (Tkinter)
- ✏️ Interactive Napari editor for manual correction of lysosomes
- 📊 Quantification of:
  - Lysosome diameter
  - Lysosome volume
  - Cell volume
  - Individual lysosome fluorescence
  - Total cellular fluorescence
  - Lysosome-associated fluorescence
  - Residual cytoplasmic fluorescence
  - Distance-dependent fluorescence distribution
  - Cell-by-cell statistics
- Automatic generation of:
  - CSV result tables
  - Overlay TIFF stacks
  - MP4/GIF videos
  - Debug images for quality control
-  Visualization of lysosome-cell relationships
- 📐 Diameter-based filtering of detected lysosomes
- 📈 Export of publication-ready quantitative datasets

---

# Supported Input Files

The program accepts:

- `.tif`
- `.tiff`
- `.czi`

Expected channels:

| Channel | Content |
|----------|---------|
| Channel 1 | Lysosome signal |
| Channel 2 | Cell signal |

If voxel dimensions are stored in the image metadata, they are read automatically. Otherwise, the GUI will request them.

---
# Examples

<table>
  <tr>
    <td align="center">
      <img src="IMAGES/L3-6/L3-6 all process.png" width="300" height="300"><br>
      <b>L3-6</b>
    </td>
    <td align="center">
      <img src="IMAGES/0H-1/0H-1 all process.png" width="300" height="300"><br>
      <b>0H-1</b>
    </td>
    <td align="center">
      <img src="IMAGES/3H-1/3H-1 all process.png" width="300" height="300"><br>
      <b>3H-1</b>
    </td>
  </tr>
</table>

#How do I obtain the diameter of lysosomes?
## 🔬 Lysosome Diameter Measurement: Blob LoG + FWHM

This README explains how the `NOT_L3_GUI_raw_channels_colored` workflow estimates **lysosome diameter from fluorescence microscopy data**.

The workflow combines **Blob LoG detection** with **FWHM (Full Width at Half Maximum)** measurement.

---

## 📌 Overview

The complete workflow is:

```text
Channel 1 fluorescence
        ↓
Gaussian smoothing
        ↓
Blob LoG candidate detection
        ↓
Candidate center (z, y, x)
        ↓
Local 3D Gaussian refinement
        ↓
Radial intensity profile
        ↓
Half-maximum threshold
        ↓
Left and right crossings
        ↓
FWHM
        ↓
Radius = FWHM / 2
        ↓
Diameter = 2 × radius
```

> **Key idea:**  
> **Blob LoG tells the script WHERE the lysosome is.**  
> **FWHM determines HOW WIDE the lysosome fluorescence peak is.**

The Blob LoG sigma is **not used as the final lysosome diameter**. The detected position is retained and the size is subsequently refined using the fluorescence intensity profile. :contentReference[oaicite:0]{index=0}

---

# 1. Gaussian Smoothing

Before detecting lysosomes, **Channel 1** is Gaussian-smoothed.

The purpose is to reduce small intensity fluctuations and noise before Blob LoG detection.

### Relevant parameter

```python
CH1_SMOOTH_SIGMA
```

This controls the Gaussian smoothing applied before detecting candidate lysosomes.

---

# 2. Blob LoG Detection

The script uses:

```python
blob_log
```

Blob LoG means:

**Blob detection using the Laplacian of Gaussian.**

It searches for bright, approximately spherical structures over a range of spatial scales.

### Main parameters

```python
BLOB_MIN_SIGMA
BLOB_MAX_SIGMA
BLOB_NUM_SIGMA
BLOB_THRESHOLD
```

| Parameter | Purpose |
|---|---|
| `BLOB_MIN_SIGMA` | Minimum sigma examined |
| `BLOB_MAX_SIGMA` | Maximum sigma examined |
| `BLOB_NUM_SIGMA` | Number of sigma values tested |
| `BLOB_THRESHOLD` | Detection threshold |

:contentReference[oaicite:1]{index=1}

Blob LoG returns candidate positions corresponding to approximately:

```text
(z, y, x, sigma)
```

However, in this workflow the original sigma/radius in the blob result is reset to `0`.

Therefore:

> **Blob LoG is used primarily to locate the lysosome center, not to define the final lysosome diameter.**

:contentReference[oaicite:2]{index=2}

---

# 3. Candidate Lysosome Center

After Blob LoG detection, the important information retained is the candidate center:

```text
(z, y, x)
```

where:

| Coordinate | Meaning |
|---|---|
| `z` | Z slice / depth |
| `y` | Vertical image coordinate |
| `x` | Horizontal image coordinate |

The detected center becomes the starting point for the subsequent size refinement.

---

# 4. Local Size Refinement

The script then refines the candidate lysosome.

The sequence is:

```text
Blob LoG center
       ↓
Local 3D Gaussian fit
       ↓
Radial intensity-profile refinement
```

The local **3D Gaussian fit** provides an intermediate refinement.

The final fluorescence-based diameter measurement is then obtained from the **radial intensity profile and FWHM**. :contentReference[oaicite:3]{index=3}

---

# 5. Radial Intensity Profile

Starting from the lysosome center, the script examines how fluorescence intensity changes with **physical radial distance**.

Conceptually:

```text
Intensity
   ↑
   │
   │                    PEAK
   │                     /\
   │                    /  \
   │                   /    \
   │------------------/------\---------------- I_half
   │                 /        \
   │                /          \
   │_______________/____________\______________
   │               ↑            ↑
   │            r_left       r_right
   │
   └──────────────────────────────────────────→
                    Radial position

              <------ FWHM ------>
```

The radial intensity profile is smoothed in 1D before the threshold crossings are determined. :contentReference[oaicite:4]{index=4}

### Relevant parameters

```python
RADIAL_MAX_RADIUS_NM
RADIAL_DR_NM
RADIAL_MIN_DROP_FRACTION

vx_um
vy_um
vz_um
```

| Parameter | Purpose |
|---|---|
| `RADIAL_MAX_RADIUS_NM` | Maximum radial distance examined |
| `RADIAL_DR_NM` | Radial sampling/bin spacing |
| `RADIAL_MIN_DROP_FRACTION` | Parameter passed to the radial-refinement function |
| `vx_um` | X voxel size in µm |
| `vy_um` | Y voxel size in µm |
| `vz_um` | Z voxel size in µm |

:contentReference[oaicite:5]{index=5}

---

# 6. What Does FWHM Mean?

## **FWHM = Full Width at Half Maximum**

Each letter has a specific meaning:

| Letter | Word | Meaning |
|:---:|---|---|
| **F** | **Full** | The complete distance across the fluorescence peak |
| **W** | **Width** | The distance between the two threshold crossings |
| **H** | **Half** | The measurement is made at half the peak height |
| **M** | **Maximum** | The maximum intensity of the radial profile |

Therefore:

```text
F W H M
│ │ │ │
│ │ │ └── Maximum
│ │ └──── Half
│ └────── Width
└──────── Full
```

**Full Width at Half Maximum** means:

> The complete width of the fluorescence intensity peak measured at **50% of its height relative to the baseline**.

:contentReference[oaicite:6]{index=6}

---

# 7. Find the Maximum Intensity

The script first determines the maximum of the smoothed radial profile:

```text
I_max
```

Conceptually:

```text
                   I_max
                     ●
                    / \
                   /   \
                  /     \
                 /       \
________________/_________\________________
```

`I_max` represents the peak fluorescence intensity in the radial profile. :contentReference[oaicite:7]{index=7}

---

# 8. Determine the Baseline

The workflow also determines a reference **baseline intensity**.

According to this implementation, the baseline uses `I_min` when the two sides are exactly equal; otherwise it uses the larger of the left and right reference intensities. :contentReference[oaicite:8]{index=8}

The baseline is important because the FWHM threshold is calculated relative to it.

---

# 9. Calculate Half Maximum

The half-maximum intensity is calculated as:

```text
I_half = baseline + 0.5 × (I_max - baseline)
```

This means that the script finds the intensity located halfway between:

```text
baseline
```

and:

```text
I_max
```

Visually:

```text
Intensity
   ↑
   │
   │                   I_max
   │                     ●
   │                    / \
   │                   /   \
   │------------------●-----●---------------- I_half
   │                 /       \
   │                /         \
   │_______________/___________\_____________
   │
   │  baseline
   └────────────────────────────────────────→
```

This is a **baseline-aware half maximum**.

It is therefore not necessarily:

```text
I_max / 2
```

unless the baseline is zero. :contentReference[oaicite:9]{index=9}

---

# 10. Find the Two FWHM Crossings

The intensity profile crosses `I_half` at two positions:

```text
r_left
```

and:

```text
r_right
```

Visually:

```text
                   I_max
                     ●
                    / \
                   /   \
                  /     \
I_half ----------●-------●----------
                 ↑       ↑
              r_left   r_right

              <--- FWHM --->
```

These two positions define the limits of the FWHM measurement. :contentReference[oaicite:10]{index=10}

---

# 11. Calculate FWHM

The full width is:

```text
FWHM = r_right - r_left
```

For example:

```text
r_left  = 0.20 µm
r_right = 0.70 µm
```

then:

```text
FWHM = 0.70 - 0.20

FWHM = 0.50 µm
```

---

# 12. Calculate Lysosome Radius

The radius is calculated as half of the FWHM:

```text
radius_um = FWHM / 2
```

Using the previous example:

```text
FWHM = 0.50 µm

radius_um = 0.50 / 2

radius_um = 0.25 µm
```

---

# 13. Calculate Lysosome Diameter

The diameter is:

```text
diameter_um = 2 × radius_um
```

Therefore:

```text
diameter_um = 2 × (FWHM / 2)
```

which simplifies to:

```text
diameter_um = FWHM
```

So **before any later radius cap**, the measured lysosome diameter corresponds directly to the FWHM of the fluorescence intensity profile. :contentReference[oaicite:11]{index=11}

---

# 14. Script-Specific Radius Cap

The workflow later limits:

```text
radius_um ≤ 0.4 µm
```

After that, the script calculates:

```text
diameter_um = 2 × radius_um
```

Therefore, along this code path:

```text
maximum stored radius   = 0.4 µm

maximum stored diameter = 0.8 µm
```

:contentReference[oaicite:12]{index=12}

---

# 🔬 One Lysosome: From Image to Diameter

| Step | Operation | Result |
|:---:|---|---|
| **A** | Bright punctum in Channel 1 | Lysosome fluorescence signal |
| **B** | Blob LoG | Candidate `(z, y, x)` center |
| **C** | 3D Gaussian refinement | Local intermediate refinement |
| **D** | Radial profile | Intensity measured versus radial distance |
| **E** | Half maximum | Baseline-aware 50% threshold |
| **F** | Two crossings | `r_left` and `r_right` |
| **G** | FWHM | Width of the fluorescence peak |
| **H** | Radius | `FWHM / 2` |
| **I** | Diameter | `2 × radius` |

:contentReference[oaicite:13]{index=13}

---

# 🔵 Blob LoG vs. FWHM

It is important not to confuse the two methods.

| Method | Main Role |
|---|---|
| **Blob LoG** | Detects **WHERE** the candidate lysosome is |
| **3D Gaussian refinement** | Provides intermediate local refinement |
| **Radial FWHM** | Measures **HOW WIDE** the fluorescence peak is |

The simplest way to remember the workflow is:

```text
┌───────────────────────────────────┐
│            BLOB LoG               │
│                                   │
│   WHERE is the lysosome?          │
│                                   │
│          → (z, y, x)              │
└───────────────────────────────────┘
                 ↓
┌───────────────────────────────────┐
│              FWHM                 │
│                                   │
│   HOW WIDE is the fluorescence    │
│   peak around that center?        │
│                                   │
│          → diameter               │
└───────────────────────────────────┘
```

---

# 📐 Formula Summary

### Half maximum

```text
I_half = baseline + 0.5 × (I_max - baseline)
```

### FWHM

```text
FWHM = r_right - r_left
```

### Radius

```text
radius_um = FWHM / 2
```

### Diameter

```text
diameter_um = 2 × radius_um
```

Therefore, before the later radius cap:

```text
diameter_um = FWHM
```

---

# 🚀 Final Workflow Summary

```text
┌─────────────────────────────┐
│ Channel 1 fluorescence      │
└──────────────┬──────────────┘
               ↓
┌─────────────────────────────┐
│ Gaussian smoothing          │
└──────────────┬──────────────┘
               ↓
┌─────────────────────────────┐
│ Blob LoG                    │
│ Detect candidate center     │
│ (z, y, x)                   │
└──────────────┬──────────────┘
               ↓
┌─────────────────────────────┐
│ Local 3D Gaussian           │
│ refinement                  │
└──────────────┬──────────────┘
               ↓
┌─────────────────────────────┐
│ Radial intensity profile    │
└──────────────┬──────────────┘
               ↓
┌─────────────────────────────┐
│ Find I_max + baseline       │
└──────────────┬──────────────┘
               ↓
┌─────────────────────────────┐
│ Calculate I_half            │
└──────────────┬──────────────┘
               ↓
┌─────────────────────────────┐
│ Find r_left and r_right     │
└──────────────┬──────────────┘
               ↓
┌─────────────────────────────┐
│ FWHM = r_right - r_left     │
└──────────────┬──────────────┘
               ↓
┌─────────────────────────────┐
│ radius = FWHM / 2           │
└──────────────┬──────────────┘
               ↓
┌─────────────────────────────┐
│ diameter = 2 × radius       │
└─────────────────────────────┘
```

> **In one sentence:**  
> **Blob LoG detects the lysosome center, while the radial FWHM measurement determines its fluorescence-based diameter.** :contentReference[oaicite:14]{index=14}

---
# Workflow

The software performs the following pipeline:

1. Load microscopy image
2. Read voxel metadata
3. Detect lysosomes
4. Estimate lysosome size
5. Segment cells
6. Assign lysosomes to cells
7. Quantify fluorescence and volume
8. Generate overlays and videos
9. (Optional) Edit results interactively in Napari
10. Export all measurements

---

# Main Outputs

The software generates quantitative tables including:

- Lysosome coordinates
- Lysosome diameter
- Lysosome volume
- Peak fluorescence intensity
- Cell assignment
- Cell volumes
- Cell fluorescence
- Lysosome-associated fluorescence
- Residual fluorescence
- Distance-based fluorescence analysis

Visualization outputs include:

- RGB overlay TIFF stacks
- MP4 videos
- GIF animations
- Debug segmentation masks

---

# Output Files

The software automatically generates:

- Lysosome coordinates
- Cell segmentation
- Cell assignments
- Lysosome statistics
- Cell statistics
- Fluorescence quantification
- Diameter statistics
- Overlay TIFF stacks
- MP4/GIF visualization videos
- Napari-editable lysosome tables
- Debug images for quality control

Outputs are exported as CSV, TIFF, and video files.

---
# Applications

This software is suitable for:

- Cell Biology
- Lysosome Biology
- Fluorescence Microscopy
- Confocal Microscopy
- High-content Imaging
- Quantitative Image Analysis
- 3D Microscopy Analysis

---

# Installation

## Requirements

The software was developed and tested using:

| Package | Version |
|---------|---------|
| Python | **3.12.13** |
| NumPy | 1.26.4 |
| Pandas | 2.2.3 |
| OpenCV | 4.12.0 |
| ImageIO | 2.33.1 |
| AICSImageIO | 4.14.0 |
| tifffile | 2023.2.28 |
| czifile | 2019.7.2.1 |
| scikit-image | 0.24.0 |
| SciPy | 1.11.4 |
| Napari | 0.6.4 |
| Magicgui | 0.10.1 |
| Matplotlib | 3.9.2 |
| PyQt6 | 6.11.0 |

---

## Clone the repository

```bash
git clone https://github.com/YourUsername/Lysosomes-Detector-GUI.git
cd Lysosomes-Detector-GUI
```

---

## Install all dependencies

All required Python packages are listed in **requirements.txt**.

Install everything with a single command:

```bash
pip install -r requirements.txt
```

This installs the exact package versions used during development, ensuring compatibility with the software.

---

# Dependencies Included

The repository includes a **requirements.txt** file containing all required Python packages.

To recreate the software environment:

```bash
pip install -r requirements.txt
```

---

# Quick Start

After installing the dependencies, launch the program:

```bash
python Lysosomes_Detector_GUI.py
```

The graphical interface will open automatically.

---

# Citation

If you use this software in your research, please cite this repository and the associated publication (when available).

---

# License

MIT License

Copyright (c) 2026 Nahuel88Ar

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

---

# Author

**Nahuel Hernan Ramos**

