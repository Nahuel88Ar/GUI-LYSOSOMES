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

# Workflow

The software performs the following pipeline:

1. Load microscopy image 3D
2. Read voxel metadata 3D
3. Detect lysosomes 3D
4. Estimate lysosome size 3D
5. Segment cells 3D
6. Assign lysosomes to cells
7. Quantify fluorescence and volume 3D
8. Generate overlays and videos
9. (Optional) Edit results interactively in Napari
10. Export all measurements

---

# Examples

You can access the images shown through [📄Zenodo](https://zenodo.org/records/22024614).

These examples cover the following workflow stages:

1. Load microscopy image 3D
2. Read voxel metadata 3D
3. Detect lysosomes 3D
4. Estimate lysosome size 3D
5. Segment cells 3D
6. Assign lysosomes to cells

<table>
  <tr>
    <td align="center">
      <img src="IMAGES/L3-6/L3-6 all process.png" width="300" height="300"><br>
      <b>LARVA</b>
    </td>
    <td align="center">
      <img src="IMAGES/0H-1/0H-1 all process.png" width="300" height="300"><br>
      <b>0 HOUR</b>
    </td>
    <td align="center">
      <img src="IMAGES/3H-1/3H-1 all process.png" width="300" height="300"><br>
      <b>3 HOURS</b>
    </td>
  </tr>
</table>

## How do I obtain the diameter of lysosomes?

For a complete explanation of the method, click on the link below and obtain the explanatory document:
### Lysosome_Diameter_BlobLoG_FWHM.docx
It is located in the main directory of the directory

### [📄 Download the document](Lysosome_Diameter_BlobLoG_FWHM.docx)

<p align="center">
  <img src="PLOT FHWR LYSOSOMES CORE-ASSOCIATED.png" alt="FWHM method for lysosome diameter measurement" width="850">
</p>

<p align="center">
  <b>Figure: Radial intensity profile 3D used to estimate lysosome diameter with the FWHM method.</b>
</p>

## Signal quantization 

### LysosomeS

SIGNAL LYSOSOMES = SIGNAL LYSOSOMES CORE + SIGNAL LYSOSOMES ASSOCIATED

SIGNAL LYSOSOMES CORE = X

LYSOSOMES CORE VOL = Vx

### SIGNAL LYSOSOMES CORE AVERAGE

A = X / Vx

### Membrane

SIGNAL ADJ MEMBRANE = SIGNAL MEMBRANA - SIGNAL LYSOSOMES 

SIGNAL ADJ MEMBRANE = M

ADJ MEMBRANE VOL = Vm

### SIGNAL MEMBRANE AVERAGE

B = M / Vm

### Signal intensity coefficient of lysosomes relative to the membrane

PIMI = (A-B)/(A+B)

PIMI = -1; 0% SIGNAL LYSOSOMES and 100% SIGNAL MEMBRANE

PIMI = 0; 50% SIGNAL LYSOSOMES and 50% SIGNAL MEMBRANE

PIMI = 1; 100% SIGNAL LYSOSOMES and 0% SIGNAL MEMBRANE

---
# Examples

These examples cover the following workflow stages:

7. Quantify fluorescence and volume 3D
8. Generate overlays and videos
9. (Optional) Edit results interactively in Napari
10. Export all measurements

<p align="center">
  <img src="MASKS.png" alt="MASKS" width="850">
</p>

<p align="center">
  <b>Figure: Calculate MASKS.</b>
</p>

MASK 1: It represents the cell membrane.

MASK 2: It represents the core lysosomes + difussed lysosomes.

MASK 3: It represents the RESIDUAL CELL MEMBRANE without lysosomes.

MASK 3 = MASK 1 - MASK 2

CH 1: Channel 1(lysosomes)

CH 2: Channel 2(cell)

<table>
  <tr>
    <td align="center">
      <img src="IMAGES/CONTROL MASKS/CH 1-2.png" width="400" height="300"><br>
      <b>CH 1 + CH 2</b>
    </td>
    <td align="center">
      <img src="IMAGES/CONTROL MASKS/MASK 1.png" width="400" height="300"><br>
      <b>MASK 1</b>
    </td>
  </tr>
</table>
<table>
  <tr>
    <td align="center">
      <img src="IMAGES/CONTROL MASKS/MASK 2.png" width="400" height="300"><br>
      <b>MASK 2</b>
    </td>
    <td align="center">
      <img src="IMAGES/CONTROL MASKS/MASK 3.png" width="400" height="300"><br>
      <b>MASK 3</b>
    </td>
  </tr>
</table>

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

Alternatively, download the repository from GitHub as a ZIP file and extract it to a folder on your computer.

---

## Recommended Installation: Anaconda Navigator

The easiest way to run the software is to use **Anaconda Navigator** with a dedicated Python environment. Anaconda Navigator provides a graphical interface for creating environments, managing packages, and launching Jupyter Notebook without requiring command-line setup.

### 1. Install Anaconda

Download and install **Anaconda Distribution** for your operating system:

- [Download Anaconda Distribution](https://www.anaconda.com/download)
- [Anaconda installation guide](https://www.anaconda.com/docs/getting-started/anaconda/install)

Anaconda Distribution includes Python, Jupyter Notebook/JupyterLab, conda environment management, and Anaconda Navigator.

### 2. Open Anaconda Navigator

After installation, open **Anaconda Navigator** from your operating system. Navigator can be used to create and manage isolated conda environments and launch Jupyter applications.

For an introduction to Navigator and its environment management features, see the official Anaconda documentation:

- [Anaconda Navigator](https://www.anaconda.com/products/navigator)
- [Anaconda Navigator: Environment management](https://www.anaconda.com/blog/python-environments-development-to-production)

### 3. Create a project environment

Create a new conda environment for this repository. Using a dedicated environment is recommended so that the required package versions do not interfere with other Python projects.

In **Anaconda Navigator**:

1. Open the **Environments** section.
2. Create a new environment.
3. Use Python **3.12** (the software was tested with Python **3.12.13**).
4. Give the environment a name, for example `lysosome-detector`.

### 4. Install the required packages

The repository includes a **requirements.txt** file containing the required Python packages. After creating the environment, open a terminal associated with that environment and run:

All required Python packages are listed in **requirements.txt**.

Install everything with a single command:

```bash
pip install -r requirements.txt
```

This installs the exact package versions used during development, ensuring compatibility with the software.

### 5. Open the project environment and launch Jupyter

After the environment and packages are installed:

1- Open Anaconda Navigator.
2- Go to Environments.
3- Select the lysosome-detector environment.
4- Go back to the Home tab.
5- Make sure lysosome-detector is selected as the active environment.
6- Find Jupyter Notebook or JupyterLab.
7- Click Launch.

Jupyter will open in your web browser.

### 6. Open the repository folder

In Jupyter:

1- Navigate to the folder where you downloaded or cloned the Lysosomes-Detector-GUI repository.
2- Open the repository folder.
3- You should see the following notebooks:
L3_GUI.ipynb
NOT_L3_GUI.ipynb

### 7. Choose the appropriate notebook

L3_GUI.ipynb

Use L3_GUI.ipynb for images with:

-Lower image quality and clarity.
-More noise.
-A less-clear background.
-More false-positive lysosome detections.
-Greater need for erosion.
-Greater need for morphological processing.

This version is particularly recommended for larval-stage images.

NOT_L3_GUI.ipynb

Use NOT_L3_GUI.ipynb for images with:

-Higher image quality and clarity.
-Much less noise.
-A clearer background.
-Greater contrast between cells and the background.
-Fewer false-positive lysosome detections.
-Less need for erosion.
-Less need for morphological processing.

### 8. Run the notebook

Open the appropriate notebook:

L3_GUI.ipynb

or

NOT_L3_GUI.ipynb

Then run the notebook cells from top to bottom.

## Important: Make sure the Jupyter kernel is using the lysosome-detector environment and not the default base environment.

---

# Quick Start

Use Command Prompt and you write in the command line.

After installing the dependencies, launch the program:

If you use the script L3_GUI.py:

```bash
python L3_GUI.py
```

If you use the script NOT_L3_GUI.py:

```bash
python NOT_L3_GUI.py
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

