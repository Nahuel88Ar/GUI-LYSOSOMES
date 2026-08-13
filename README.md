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

## Detection lysosomes(Channel 1)

<table>
  <tr>
    <td align="center">
      <img src="IMAGES/L3-6 LYSOSOMES.png" width="300" height="300"><br>
      <b>L3-6 LYSOSOMES</b>
    </td>
    <td align="center">
      <img src="IMAGES/L3-6 LYSOSOMES LABEL.png" width="300" height="300"><br>
      <b>L3-6 LYSOSOMES WITH LABEL</b>
    </td>
  </tr>

  <tr>
    <td align="center">
      <img src="IMAGES/0H-1 LYSOSOMES.png" width="300" height="300"><br>
      <b>0H-1 LYSOSOMES</b>
    </td>
    <td align="center">
      <img src="IMAGES/0H-1 LYSOSOMES LABEL.png" width="300" height="300"><br>
      <b>0H-1 LYSOSOMES WITH LABEL</b>
    </td>
  </tr>

  <tr>
    <td align="center">
      <img src="IMAGES/3H-1 LYSOSOMES.png" width="300" height="300"><br>
      <b>3H-1 LYSOSOMES</b>
    </td>
    <td align="center">
      <img src="IMAGES/3H-1 LYSOSOMES LABEL.png" width="300" height="300"><br>
      <b>3H-1 LYSOSOMES WITH LABEL</b>
    </td>
  </tr>
</table>

## Segmentation without lysosomes(Channel 2)

<table>
  <tr>
    <td align="center">
      <img src="IMAGES/L3-6 CELLS.png" width="300" height="300"><br>
      <b>L3-6 CELLS</b>
    </td>
    <td align="center">
      <img src="IMAGES/L3-6 SEGMENTATION WITHOUT LYSOSOMES.png" width="300" height="300"><br>
      <b>L3-6 SEGMENTATION WITHOUT LYSOSOMES</b>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="IMAGES/0H-1 CELLS.png" width="300" height="300"><br>
      <b>0H-1 CELLS</b>
    </td>
    <td align="center">
      <img src="IMAGES/0H-1 SEGMENTATION WITHOUT LYSOSOMES.png" width="300" height="300"><br>
      <b>0H-1 SEGMENTATION WITHOUT LYSOSOMES</b>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="IMAGES/3H-1 CELLS.png" width="300" height="300"><br>
      <b>3H-1 CELLS</b>
    </td>
    <td align="center">
      <img src="IMAGES/3H-1 SEGMENTATION WITHOUT LYSOSOMES.png" width="300" height="300"><br>
      <b>3H-1 SEGMENTATION WITHOUT LYSOSOMES</b>
    </td>
  </tr>
</table>

## Segmentation with lysosomes(Detection + Segmentation)

<table>
  <tr>
    <td align="center">
      <img src="IMAGES/L3-6 LYSOSOMES.png" width="300" height="300"><br>
      <b>L3-6 LYSOSOMES</b>
    </td>
    <td align="center">
      <img src="IMAGES/L3-6 CELLS.png" width="300" height="300"><br>
      <b>L3-6 CELLS</b>
    </td>
    <td align="center">
      <img src="IMAGES/L3-6 SEGMENTATION WITH LYSOSOMES.png" width="300" height="300"><br>
      <b>L3-6 SEGMENTATION WITH LYSOSOMES</b>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="IMAGES/0H-1 LYSOSOMES.png" width="300" height="300"><br>
      <b>0H-1 LYSOSOMES</b>
    </td>
    <td align="center">
      <img src="IMAGES/0H-1 CELLS.png" width="300" height="300"><br>
      <b>0H-1 CELLS</b>
    </td>
    <td align="center">
      <img src="IMAGES/0H-1.png" width="300" height="300"><br>
      <b>0H-1 SEGMENTATION WITH LYSOSOMES</b>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="IMAGES/3H-1 LYSOSOMES.png" width="300" height="300"><br>
      <b>3H-1 LYSOSOMES</b>
    </td>
    <td align="center">
      <img src="IMAGES/3H-1 CELLS.png" width="300" height="300"><br>
      <b>3H-1 CELLS</b>
    </td>
    <td align="center">
      <img src="IMAGES/3H-1 SEGMENTATION WITH LYSOSOMES.png" width="300" height="300"><br>
      <b>3H-1 SEGMENTATION WITH LYSOSOMES</b>
    </td>
  </tr>
</table>

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

