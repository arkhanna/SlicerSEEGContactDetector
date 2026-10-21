# Installation

## Option 1: Installation via Extension Manager

The recommended way to install the SEEG Contact Detector extension is through the 3D Slicer Extension Manager. This is the preferred method for end users.

1. In 3D Slicer, navigate to **View → Extension Manager**.
2. Open the **Install Extensions** tab and search for SEEG Contact Detector.
3. Click **Install**, then restart 3D Slicer when prompted.
4. The SEEG Contact Detector module will then appear in the list of available modules.

For quicker access, the module can be added to the toolbar. Navigate to **Edit → Application Settings → Modules**, locate *SEEG Contact Detector* in the module list, and drag and drop it into the **Favorite Modules** section. The module icon will then appear in the toolbar.

## Option 2: Using Source Code
This method allows you to install the extension directly **without using the Extension Manager** and is the **recommended approach for development.**

### Dependencies
SEEG Contact Detector depends on one external module that must be installed **manually via the 3D Slicer Extension Manager** before loading this extension. If this dependency is missing, SEEG Contact Detector will not load correctly.

Please ensure that the following extension is installed:

* [HDBrainExtraction](https://github.com/lassoan/SlicerHDBrainExtraction)

### Load the Extension
1. Download the source code from GitHub: [https://github.com/EpiReC-ISARG/SlicerSEEGContactDetector](https://github.com/EpiReC-ISARG/SlicerSEEGContactDetector).
It is recommended to use a [stable release](https://github.com/EpiReC-ISARG/SlicerSEEGContactDetector/releases) or the latest commit on the main branch that represents the current stable version.
2. Unzip the downloaded archive.
3. Launch 3D Slicer and drag-and-drop the folder containing the extension source code into the 3D Slicer application window.
4. When prompted, select `Add Python scripted modules to the application`.
5. The SEEG Contact Detector module will be added to the list of available modules without requiring a restart of 3D Slicer.
6. When you start detection first time, you will be prompted to install the externa

If needed, you can later modify the extension path in: Edit → Application Settings → Modules → Additional module paths.
## Apple Silicon Macs (M1/M2/M3/M4): brain mask with an external HD-BET

3D Slicer has no native Apple Silicon build yet, so on these Macs it runs as an Intel app under Rosetta. Inside that Slicer, the newest PyTorch available (2.2.2) is incompatible with Slicer's NumPy 2, and *Create from T1* fails in HD-BET ("Numpy is not available"). The rest of the module works normally.

The module can instead run HD-BET from a separate, native Python environment:

1. Create the environment once (conda shown; any Python 3.10–3.13 venv works):
   ```bash
   conda create -n hdbet python=3.12
   conda activate hdbet
   pip install hd-bet
   ```
2. Optional, to avoid the download during the first brain mask: download the HD-BET model weights once with
   `python -c "from HD_BET.checkpoint_download import maybe_download_parameters as m; m()"`.
3. In the module, open **Advanced → Brain mask (HD-BET)**:
   - **Backend:** *External HD-BET (native Python)* (the default on Apple Silicon).
   - **hd-bet command:** the `hd-bet` program in that environment, e.g. `/opt/miniconda3/envs/hdbet/bin/hd-bet` (found automatically in common conda locations).
   - **Device:** `mps` (Apple GPU) or `cpu`. **Fast mode** disables test-time augmentation (several times faster, slightly lower quality).
4. Click **Create from T1** as usual. Registration (BRAINSFit) runs in Slicer; HD-BET runs natively with progress and a **Cancel** button. The T1 is written to a temporary file for HD-BET and deleted afterwards.

On Windows and Linux the *HDBrainExtraction extension (inside Slicer)* backend still works; the external backend works there too.
