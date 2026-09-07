<div align="center">

<img src="https://i.postimg.cc/76Hrwc9F/icon.jpg" width="72" alt="EdSeg logo" />

# EdSeg

**Learn deep learning by training your own medical image segmentation model.**

A desktop app for medical imaging students to learn the fundamentals of deep learning — hands-on, no coding required.

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-CPU%20%7C%20CUDA-ee4c2c)](https://pytorch.org/)
[![Flask](https://img.shields.io/badge/Flask-backend-black)](https://flask.palletsprojects.com/)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS-lightgrey)]()
[![License](https://img.shields.io/badge/license-Open%20Source-brightgreen)]()

**[⬇️ Download for Windows CUDA+CPU support](https://drive.google.com/file/d/1ClNAU_mkHNnzlQDilBhAa9ydD4ia8BnC/view?usp=sharing)** &nbsp;·&nbsp; **[⬇️ Download for Windows Lite Version (CPU Only)](https://drive.google.com/file/d/1g2d8emD4bNysEY3YSSM8y1v1na4PBy87/view?usp=sharing)** &nbsp;·&nbsp; **[⬇️ Download for Mac](https://drive.google.com/file/d/1KKf73MwBig9xoRCB49N-I_iq6IRz55ia/view)**

</div>

---

## Contents

- [Screenshots](#screenshots)
- [Overview](#overview)
- [Features](#features)
- [Supported Model Architectures](#supported-model-architectures)
- [Installation](#installation)
  - [Windows](#-windows)
  - [Mac](#-mac)
- [Running from Source](#running-from-source)
- [Building the Desktop App Yourself](#building-the-desktop-app-yourself)
- [How It Works](#how-it-works)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Tech Stack](#tech-stack)
- [Contributing](#contributing)
- [License](#license)

---

## Screenshots

<div align="center">
<img src="https://i.postimg.cc/PqL2ZMxP/image1.webp" width="800" alt="EdSeg dataset selection screen" />
<br /><br />
<img src="https://i.postimg.cc/Bv8MDC6t/image02.webp" width="800" alt="EdSeg training screen with live charts" />
<br /><br />
<img src="https://i.postimg.cc/k5Vf8y45/image-2.webp" width="800" alt="EdSeg results screen with confusion matrix" />
<br /><br />
<img src="https://i.postimg.cc/Zq9V3LRW/image3.webp" width="800" alt="EdSeg hyperparameter tuning screen" />
</div>

---

## Overview

EdSeg is an educational platform built for medical imaging students to learn
**how deep learning works** by actually training their own 2D image
segmentation models — on real (or synthetic sample) CT, X-ray, and MRI-style
images — without writing a single line of code.

Everything runs **locally on the student's own machine** as a real desktop
app. No cloud costs, no server to maintain, no account to create. Pick a
dataset, adjust hyperparameters with sliders, watch the model train live,
and see exactly what happens when you change the learning rate, the model
architecture, or the amount of training data.

---

## Features

### 🧩 Core workflow
- 📊 **Dataset management** — use the bundled sample dataset or upload your own (`images/` + `masks/` zip)
- ⚙️ **Model & hyperparameter setup** — pick an architecture and tune learning rate, epochs, batch size, validation split, and dataset size with sliders
- 🏋️ **Live training** — real-time loss curves, Dice/IoU charts, and a live prediction preview that improves epoch by epoch
- 💾 **Checkpointing & resume** — training saves automatically every 5 epochs; pick up exactly where you left off
- 📈 **Results & metrics** — confusion matrix, precision/recall/Dice/IoU/accuracy, all with plain-language explanations of what each one means
- 🔍 **Model library & inference** — save trained models, load them later, and test them on brand-new images
- ⚖️ **Compare runs** — view two completed training runs side by side, on matching scales

### 🚀 Advanced
- 🧪 **Hyperparameter tuning (grid search)** — sweep up to 3 parameters at once, with automatic hard caps to keep runs fast
- 🖥️ **CPU / GPU toggle** — auto-detects CUDA (NVIDIA) and MPS (Apple Silicon); compare training speed across devices
- 📚 **Concepts panel** — a built-in glossary explaining loss functions, overfitting, Dice score, and more, in plain language
- 🌗 **Light & dark themes**

---

## Supported Model Architectures

| Architecture | Size | Notes |
|---|---|---|
| Mini U-Net | ~30K params | Default — fastest, ideal for a first run |
| Standard Mini U-Net | ~480K params | More capacity, still CPU-fast |
| Simple CNN Encoder-Decoder | ~106K params | No skip connections — compare against the U-Nets to see what skip connections actually do |
| Full U-Net | ~31M params | The real, unscaled architecture — noticeably slower, included for direct comparison against the lightweight models |

---

## Installation

No Python, no dependencies, no admin rights required — just download, extract, and run.

### 🪟 Windows

1. **Download** the app: **[EdSeg for Windows](https://drive.google.com/file/d/1ClNAU_mkHNnzlQDilBhAa9ydD4ia8BnC/view?usp=sharing)** (Lite version linked at top of Readme)
2. **Extract the zip fully** to a normal folder (Desktop, Documents, etc.) — don't run it from inside the zip
3. Open the extracted `EdSeg` folder and **double-click `EdSeg.exe`**
4. Windows will likely show a **"Windows protected your PC"** SmartScreen warning, since this isn't a signed commercial app:
   - Click **"More info"**
   - Click **"Run anyway"**
   
   This only appears the first time.
5. A loading screen appears briefly, then the app opens

> ⚠️ Keep `EdSeg.exe` inside its folder — it depends on the other files shipped alongside it (the `_internal` folder). Moving just the `.exe` on its own will break it.

### 🍎 Mac

1. **Download** the app: **[EdSeg for Mac](https://drive.google.com/file/d/1KKf73MwBig9xoRCB49N-I_iq6IRz55ia/view)**
2. **Extract the zip** if it isn't already
3. **Right-click** `EdSeg.app` → **Open** (don't double-click the first time)
4. macOS will warn that the app is from an "unidentified developer" — click **Open** again to confirm
   
   This only appears the first time; after that, you can open it normally.
5. A loading screen appears briefly, then the app opens

---

## Running from Source

For development, or if you'd rather not use the packaged app:

```bash
git clone https://github.com/yourusername/edseg.git
cd edseg
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt
python backend/app.py
```

Then open `http://127.0.0.1:5000` in your browser.

## Building the Desktop App Yourself

See [`BUILD_INSTRUCTIONS.md`](./BUILD_INSTRUCTIONS.md) for full Windows and
Mac build steps using PyInstaller. Short version:

```bash
python -m venv build_venv
source build_venv/bin/activate    # Windows: build_venv\Scripts\activate
pip install Flask Pillow numpy pywebview pyinstaller torch
pyinstaller launcher.spec --noconfirm
```

Your app appears at `dist/EdSeg/EdSeg.exe` (Windows) or `dist/EdSeg/EdSeg.app` (Mac).

> PyInstaller can't cross-compile — build on Windows for the `.exe`, and on a Mac for the `.app`.

---

## How It Works

EdSeg pairs a lightweight **Flask + PyTorch backend** with a browser-based
frontend, wrapped in a native desktop window via
[`pywebview`](https://pywebview.flowrl.com/). When packaged with
PyInstaller, the whole thing — Python runtime, PyTorch, the web UI, and a
bundled sample dataset — ships as a double-click app, with no
server, no cloud, and no ongoing hosting cost.

```
Browser-rendered UI  ⇄  Local Flask API  ⇄  PyTorch training/inference
        (frontend/)          (backend/)         (CPU or GPU)
```

All student data (trained checkpoints, saved models, exported reports,
uploaded datasets) is written to a folder next to the app itself, so it
persists across sessions and across app updates.

---

## Project Structure

```
edseg/
├── launcher.py              # Desktop entry point (Flask + pywebview)
├── launcher.spec            # PyInstaller build configuration
├── backend/                 # Flask API, training loop, model architectures
│   ├── app.py
│   ├── train.py
│   ├── models.py
│   ├── tuning.py
│   └── ...
├── frontend/                 # HTML / CSS / JS UI
├── datasets/sample_shapes/   # Bundled synthetic sample dataset
├── requirements.txt
└── BUILD_INSTRUCTIONS.md
```

---

## Requirements

- Python 3.10+ *(only needed if running from source or building — not for the packaged app)*
- ~2GB free disk space for a built desktop app (more if built with CUDA support)
- No GPU required — the app is designed to train quickly on CPU alone; GPU acceleration is auto-detected and optional

## Tech Stack

- **Backend:** Python, Flask, PyTorch
- **Frontend:** vanilla HTML/CSS/JavaScript (no framework, no build step)
- **Packaging:** PyInstaller, pywebview

---

## Contributing

Issues and pull requests are welcome. If you're adding a new dataset,
model architecture, or feature, please check
[`DEVELOPMENT_LOG.md`](./DEVELOPMENT_LOG.md) first — it documents the
project's full build history and the reasoning behind key design
decisions (why certain paths are resolved the way they are, why
checkpoints are stored where they are, etc.).

## License

This project is open source.

---

<div align="center">
<sub>Built for medical imaging students learning deep learning by doing.</sub>
</div>
