<div align="center">

<img src="https://i.postimg.cc/76Hrwc9F/icon.jpg" width="24" />

# EdSeg

**Learn deep learning by training your own medical image segmentation model.**

A desktop app for medical imaging students to learn the fundamentals of deep learning — hands-on, no coding required.

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-CPU%20%7C%20CUDA-ee4c2c)](https://pytorch.org/)
[![Flask](https://img.shields.io/badge/Flask-backend-black)](https://flask.palletsprojects.com/)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS-lightgrey)]()
[![License](https://img.shields.io/badge/license-MIT-green)]()

</div>

---

## Screenshots

<div align="center">
<img src="https://i.postimg.cc/PqL2ZMxP/image1.webp" width="800" />
<img src="https://i.postimg.cc/Bv8MDC6t/image02.webp" width="800" />
<img src="https://i.postimg.cc/k5Vf8y45/image-2.webp" width="800" />
<img src="https://i.postimg.cc/Zq9V3LRW/image3.webp" width="800" />
</div>

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

## Features


### Core workflow
- 📊 **Dataset management** — use the bundled sample dataset or upload your own (`images/` + `masks/` zip)
- ⚙️ **Model & hyperparameter setup** — pick an architecture and tune learning rate, epochs, batch size, validation split, and dataset size with sliders
- 🏋️ **Live training** — real-time loss curves, Dice/IoU charts, and a live prediction preview that improves epoch by epoch
- 💾 **Checkpointing & resume** — training saves automatically every 5 epochs; pick up exactly where you left off
- 📈 **Results & metrics** — confusion matrix, precision/recall/Dice/IoU/accuracy, all with plain-language explanations of what each one means
- 🔍 **Model library & inference** — save trained models, load them later, and test them on brand-new images
- ⚖️ **Compare runs** — view two completed training runs side by side, on matching scales

### Advanced
- 🧪 **Hyperparameter tuning (grid search)** — sweep up to 3 parameters at once, with automatic hard caps to keep runs fast
- 🖥️ **CPU / GPU toggle** — auto-detects CUDA (NVIDIA) and MPS (Apple Silicon); compare training speed across devices
- 📚 **Concepts panel** — a built-in glossary explaining loss functions, overfitting, Dice score, and more, in plain language
- 🌗 **Light & dark themes**


## Supported model architectures

| Architecture | Size | Notes |
|---|---|---|
| Mini U-Net | ~30K params | Default — fastest, ideal for a first run |
| Standard Mini U-Net | ~480K params | More capacity, still CPU-fast |
| Simple CNN Encoder-Decoder | ~106K params | No skip connections — compare against the U-Nets to see what skip connections actually do |
| Full U-Net | ~31M params | The real, unscaled architecture — noticeably slower, included for direct comparison against the lightweight models |

## Getting started

### Option 1 — Download the desktop app (recommended for students)

Grab the latest build for your platform from the [Releases](insert releases link here) page, extract the zip, and run:

- **Windows:** double-click `EdSeg.exe` inside the extracted folder
- **Mac:** right-click `EdSeg.app` → **Open** the first time (bypasses Gatekeeper's unsigned-app warning)

No Python, no installation, no admin rights required.

> **Note:** Windows may show a SmartScreen warning on first launch since this isn't a signed app — click **"More info" → "Run anyway."** This is expected for small/free tools distributed outside an app store.

### Option 2 — Run from source (for development)

```bash
git clone https://github.com/yourusername/edseg.git
cd edseg
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt
python backend/app.py
```

Then open `http://127.0.0.1:5000` in your browser.

### Option 3 — Build the desktop app yourself

See [`BUILD_INSTRUCTIONS.md`](./BUILD_INSTRUCTIONS.md) for full Windows and
Mac build steps using PyInstaller. Short version:

```bash
python -m venv build_venv
source build_venv/bin/activate    # Windows: build_venv\Scripts\activate
pip install Flask Pillow numpy pywebview pyinstaller torch
pyinstaller launcher.spec --noconfirm
```

Your app appears at `dist/EdSeg/EdSeg.exe` (Windows) or `dist/EdSeg/EdSeg.app` (Mac).

## How it works



EdSeg pairs a lightweight **Flask + PyTorch backend** with a browser-based
frontend, wrapped in a native desktop window via
[`pywebview`](https://pywebview.flowrl.com/). When packaged with
PyInstaller, the whole thing — Python runtime, PyTorch, the web UI, and a
bundled sample dataset — ships as a single double-click app, with no
server, no cloud, and no ongoing hosting cost.

```
Browser-rendered UI  ⇄  Local Flask API  ⇄  PyTorch training/inference
        (frontend/)          (backend/)         (CPU or GPU)
```

All student data (trained checkpoints, saved models, exported reports,
uploaded datasets) is written to a folder next to the app itself, so it
persists across sessions and across app updates.

## Project structure

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

## Requirements

- Python 3.10+
- ~2GB free disk space for a built desktop app (more if built with CUDA support)
- No GPU required — the app is designed to train quickly on CPU alone; GPU acceleration is auto-detected and optional

## Tech stack

- **Backend:** Python, Flask, PyTorch
- **Frontend:** vanilla HTML/CSS/JavaScript (no framework, no build step)
- **Packaging:** PyInstaller, pywebview

## Contributing

Issues and pull requests are welcome. If you're adding a new dataset,
model architecture, or feature, please check
[`DEVELOPMENT_LOG.md`](./DEVELOPMENT_LOG.md) first — it documents the
project's full build history and the reasoning behind key design
decisions (why certain paths are resolved the way they are, why
checkpoints are stored where they are, etc.).

## License

Open Source

---

<div align="center">
<sub>Built for medical imaging students learning deep learning by doing.</sub>
</div>
