<div align="center">

<img src="https://i.postimg.cc/76Hrwc9F/icon.jpg" width="72" alt="EdSeg logo" />

# EdSeg

**Learn deep learning by training your own medical image segmentation model.**

A desktop app for medical imaging students to learn the fundamentals of deep learning — hands-on, no coding required. Now with **13 lightweight models** and **3D volume segmentation**.

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-CPU%20%7C%20CUDA-ee4c2c)](https://pytorch.org/)
[![Flask](https://img.shields.io/badge/Flask-backend-black)](https://flask.palletsprojects.com/)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS-lightgrey)]()
[![License: MIT](https://img.shields.io/badge/license-MIT-brightgreen)](./LICENSE)

</div>

---

## Downloads and how big they are

| Build | Download (zip) | On disk | Notes |
|---|---|---|---|
| **Windows — CPU-only (recommended)** | **≈ 165 MB** | **≈ 470 MB** | Works on every computer. Trains on the processor. |
| **Windows — CUDA + CPU** | ≈ 2.8 GB (estimate) | ≈ 5.6 GB (estimate) | Adds NVIDIA GPU training. Only worth it with an NVIDIA GPU. |
| Mac | not rebuilt yet — PyInstaller must run on a Mac | — | The standard Mac build includes Apple Silicon GPU (MPS) support. |

The CUDA build is bigger only because it bundles NVIDIA's GPU libraries (the CUDA
PyTorch wheel alone is 2.6 GB; the CPU one is 0.12 GB). The app's code and features are
identical. The app itself tells you which build you have and what the other would cost:
open **System info → About this build**.

> The CPU-only figures above were measured on the build in `Builds/`. CUDA figures are
> estimates until that build is made.

Previous v1.0 downloads (2D only) are still available:
[Windows CUDA+CPU](https://drive.google.com/file/d/1ClNAU_mkHNnzlQDilBhAa9ydD4ia8BnC/view?usp=sharing) ·
[Windows Lite (CPU only)](https://drive.google.com/file/d/1g2d8emD4bNysEY3YSSM8y1v1na4PBy87/view?usp=sharing) ·
[Mac](https://drive.google.com/file/d/1KKf73MwBig9xoRCB49N-I_iq6IRz55ia/view)

---

## Contents

- [What's new](#whats-new)
- [Overview](#overview)
- [Features](#features)
- [Supported model architectures](#supported-model-architectures)
- [3D segmentation](#3d-segmentation)
- [Accessibility](#accessibility)
- [Installation](#installation)
- [Running from source](#running-from-source)
- [Building the desktop app yourself](#building-the-desktop-app-yourself)
- [Test datasets](#test-datasets)
- [Tests](#tests)
- [Project structure](#project-structure)
- [Requirements](#requirements)
- [Contributing](#contributing)

---

## What's new

- **13 models, not just U-Nets** — depth-wise separable, attention-gated, nested, residual, dilated-pyramid and Mamba state-space designs, all small enough for a laptop CPU.
- **3D segmentation** for CT/MRI volumes (`.nii`, `.nii.gz`, `.npy`) with a **Lightweight 3D U-Net**, **GMANet 3D** and **Light-VM3D-UNet**, plus a slice viewer.
- **Accessible UI**: full keyboard use, visible focus, screen-reader labels and announcements, text-size control, WCAG-AA colour contrast, tables behind every chart, reduced-motion support.
- **Build-size notices** in the app and docs, and a one-command build that verifies the packaged app before you ship it.
- Many bug fixes (see `DEVELOPMENT_LOG.md`, *Stage 13*).

---

## Overview

EdSeg is an educational platform built for medical imaging students to learn
**how deep learning works** by actually training their own 2D and 3D
segmentation models — on real (or synthetic sample) CT, X-ray, and MRI-style
data — without writing a single line of code.

Everything runs **locally on the student's own machine** as a real desktop
app. No cloud costs, no server to maintain, no account to create. Pick a
dataset, adjust hyperparameters with sliders, watch the model train live,
and see exactly what happens when you change the learning rate, the model
architecture, or the amount of training data.

---

## Features

### Core workflow
- **Dataset management** — bundled 2D and 3D samples, or upload your own (`images/` + `masks/` zip)
- **Model & hyperparameter setup** — architecture, learning rate, epochs, batch size, image/volume size, validation split, dataset size
- **Live training** — loss and Dice/IoU charts, a live prediction preview, and a **"Behind the scenes" animation** that shows what happens inside each training step (data → preprocess → model → prediction → loss → gradients → weight update → validation) and ends on "Final output ready". It has a Pause button and respects the system *reduce motion* setting
- **Checkpointing & resume** — saved every 5 epochs; continue exactly where you stopped
- **Results & metrics** — confusion matrix, precision/recall/Dice/IoU/accuracy with plain-language explanations
- **Model library & inference** — save trained models, test on a single new image or volume, or on a whole dataset
- **Compare runs** — two runs side by side on matching scales

### Advanced
- **Hyperparameter sweeps (grid search)** with hard caps to keep runs quick
- **CPU / GPU choice** — auto-detects CUDA and Apple Silicon (MPS); explains clearly when a GPU option isn't available
- **Concepts panel** — glossary incl. voxels, 3D segmentation, state-space models, attention
- **Light & dark themes**, adjustable text size

---

## Supported model architectures

**2D image models**

| Architecture | Parameters | Idea |
|---|---|---|
| Mini U-Net | 30 K | Smallest U-Net — fastest first run |
| Standard Mini U-Net | 483 K | More capacity, still CPU-fast |
| Mobile U-Net | 100 K | Depth-wise separable convolutions (MobileNet idea) |
| LinkNet-Lite | 180 K | Residual encoder, *additive* skips |
| Attention U-Net | 275 K | Attention-gated skip connections |
| DeepLab-Lite | 237 K | Dilated (atrous) pyramid pooling |
| U-Net++ | 139 K | Nested, densely connected skips |
| Light-VM2D-UNet | 358 K | Mamba-style state-space blocks (LightM-UNet idea) |
| Simple CNN Encoder-Decoder | 106 K | No skip connections — shows what skips add |
| Full U-Net | 31 M | The original, unscaled architecture |

**3D volume models**

| Architecture | Parameters | Idea |
|---|---|---|
| Lightweight 3D U-Net | 351 K | The standard 3D baseline, kept narrow |
| Light-VM3D-UNet | 210 K | Mamba-style state-space blocks over the volume |
| GMANet 3D | 477 K | Gated skips + multi-scale channel attention + dense local blocks |

> **Please read:** these are compact, readable re-implementations of each architecture's
> *ideas*, sized for CPU training and teaching — not the research authors' released code,
> and they ship without pretrained weights. **Light-VM-UNet** follows the layout of
> *LightM-UNet* (Liao et al., 2024: residual Vision-Mamba layers, additive skips). The
> Mamba scan is written in plain PyTorch (a parallel prefix scan, verified against a
> sequential reference) so it runs on CPU — the official `mamba_ssm` needs a CUDA-only
> kernel. **GMANet** is an educational interpretation: I could not find a published model
> of exactly that name, so it combines attention-gated skips with the multi-scale channel
> attention idea of *LGMANet* (a 2D paper). If you meant a specific paper, tell us and the
> model can be aligned to it.

---

## 3D segmentation

Pick a 3D dataset and the model list switches to the 3D models. Volumes are normalised
(outlier clipping + z-score) and resized to a cube (default 32³, up to 64³), and
cached so later epochs are fast. Results show a slice viewer (slider, ‹ ›, arrow keys)
for image / ground truth / prediction.

**Dataset format** — a zip with `images/` and `masks/` folders and matching names:

```
my_mri.zip
  images/patient_001.nii.gz
  masks/patient_001.nii.gz
```

Masks are binary (any value > 0 is foreground). Images and masks must be the same size.
A bundled synthetic dataset, **Sample Blobs 3D**, lets you try 3D immediately.
3D training is much heavier than 2D: keep the volume size and batch size small on a CPU.

---

## Accessibility

- **Keyboard**: every control is reachable and operable with the keyboard; focus is always visible; a *Skip to main content* link; `Esc` closes dialogs and panels and returns focus.
- **Screen readers**: landmarks and one heading per screen (focus moves to it on navigation); labelled controls; slider values announced in real units (e.g. "Learning rate 0.001"); live regions for training progress and results; the current page is marked in the navigation.
- **Charts** are never the only source of information: each has a text summary and a *View as table* section, and series differ by line style and marker, not just colour.
- **Visuals**: text and controls meet WCAG 2.1 AA contrast in both themes; text size can be raised up to 150 %; the layout works from phone width up; animations stop with *reduce motion*; Windows high-contrast mode is supported.
- Browser-style pop-ups were replaced by accessible dialogs.

---

## Installation

> **Full step-by-step instructions for Windows (CPU-only and NVIDIA CUDA) and Mac, for running from source or building the app yourself, are in [`INSTALL.md`](./INSTALL.md).**

No Python, no dependencies, no admin rights required — download, extract, run.

### Windows
1. Download the zip (see sizes above) and **extract it fully** to a normal folder — not from inside the zip.
2. Open the extracted `EdSeg` folder and double-click `EdSeg.exe`.
3. Windows may show a SmartScreen warning (the app isn't code-signed): **More info → Run anyway**. Only the first time.

> **Window engine:** EdSeg draws its window with Microsoft's *Edge WebView2 Runtime* (preinstalled on Windows 11 and most Windows 10). If it is missing, EdSeg opens in your normal web browser instead and shows a small box — keep it open while you work, click OK to quit. Installing the free WebView2 Runtime from Microsoft gives the normal app window.
>
> Keep `EdSeg.exe` inside its folder — it needs the `_internal` folder beside it.
> Your checkpoints, saved models and uploaded datasets are saved next to `EdSeg.exe`.

### Mac
Right-click `EdSeg.app` → **Open** the first time, then **Open** again.

---

## Running from source

```bash
python -m venv venv
venv\Scripts\activate          # macOS/Linux: source venv/bin/activate
pip install -r requirements.txt
pip install torch --index-url https://download.pytorch.org/whl/cpu   # or the CUDA index, see requirements.txt
python backend/app.py
```
Then open `http://127.0.0.1:5000`.

## Building the desktop app yourself

See [`BUILD_INSTRUCTIONS.md`](./BUILD_INSTRUCTIONS.md). In short:

```bash
pyinstaller launcher.spec --noconfirm --clean
python build_tools/finalize_build.py --dist dist/EdSeg --flavor cpu --out release --scratch scratch
```
`finalize_build.py` self-tests the packaged app, records the real sizes, and writes the zip.

---

## Test datasets

Public datasets used to test EdSeg (not bundled; download separately and respect their licences):

| Dataset | Type | Size | Licence / source |
|---|---|---|---|
| Kvasir-SEG (polyp segmentation, 1000 images) | 2D | 46 MB | Free for research & education — Simula |
| Medical Segmentation Decathlon — Hippocampus (260 MRI volumes) | 3D | 28 MB | CC-BY-SA 4.0 |
| Medical Segmentation Decathlon — Spleen (CT) | 3D | 1.6 GB | CC-BY-SA 4.0 |

`python tools/prepare_datasets.py --raw <downloads> --out <folder>` converts them into
upload-ready zips. On a 2D polyp dataset the lightweight models reach Dice ≈ 0.45–0.5
after 12 epochs on 160 training images; on hippocampus MRI the 3D models reach Dice ≈ 0.86–0.89.

## Tests

```bash
python backend/scripts/test_models.py        # every model, 2D and 3D
python backend/scripts/test_security.py      # path / upload hardening
python backend/scripts/test_real_datasets.py # end-to-end on the public datasets (if downloaded)
```
Plus the training, resume, results, tuning, safety and API suites in `backend/scripts/`.
A packaged build verifies itself with `EdSeg.exe --selftest report.json`.

---

## Project structure

```
edseg/
├── launcher.py / launcher.spec     # desktop entry point + PyInstaller config
├── backend/
│   ├── app.py  train.py  job_manager.py  tuning.py  sweeps.py
│   ├── models.py  models_lite.py  models_3d.py  ssm.py   # architectures
│   ├── dataset_utils.py  segmentation_dataset.py  volume_utils.py
│   ├── results.py  inference.py  model_library.py  build_info.py
│   └── scripts/                    # test + data-generation scripts
├── frontend/                       # index.html, style.css, js/*.js (no build step)
├── build_tools/finalize_build.py   # self-test + measure + zip
├── tools/prepare_datasets.py
├── datasets/sample_shapes/  datasets/sample_blobs_3d/
└── requirements.txt  BUILD_INSTRUCTIONS.md  DEVELOPMENT_LOG.md
```

## Requirements

- Python 3.10+ *(only to run from source or build)*
- Packaged app: ≈ 0.5 GB disk (CPU-only) or ≈ 5.6 GB (CUDA); no GPU required

## Licence

EdSeg is released under the **MIT License** (see [`LICENSE`](./LICENSE)), © 2026 Syed Mohammad Jarif Tahmid. EdSeg is an educational tool and is **not a medical device** – it must not be used to make clinical decisions.
The open-source components it bundles keep their own licences, listed with full texts in [`THIRD_PARTY_NOTICES.txt`](./THIRD_PARTY_NOTICES.txt) (also shipped inside every build).

## Contributing

Issues and pull requests are welcome. Please read [`DEVELOPMENT_LOG.md`](./DEVELOPMENT_LOG.md) first.

---

<div align="center">
<sub>Built for medical imaging students learning deep learning by doing.</sub>
</div>
