# Installing EdSeg

EdSeg is a desktop app for learning medical-image segmentation. There are three ways to get it:

| I want to… | Go to |
|---|---|
| Just use it (Windows, no setup) | **[A. Download the ready-made app](#a-download-the-ready-made-app-windows)** |
| Run it from the source code (Windows, Mac or Linux) | **[B. Run from source](#b-run-from-source)** |
| Build my own desktop app (`.exe` / `.app`) | **[C. Build the desktop app yourself](#c-build-the-desktop-app-yourself)** |

**Which version do I need?**

| Your computer | Choose | Extra download | Notes |
|---|---|---|---|
| Any Windows PC | **CPU-only** | none | Works everywhere. Small 2D models train in seconds to minutes; 3D models take longer. |
| Windows or Linux PC with an **NVIDIA** graphics card | **CUDA** | about **2.6 GB** (PyTorch) | Much faster for big models and 3D volumes. If you don't have an NVIDIA GPU it gives you nothing. |
| **Mac** (Apple Silicon: M1/M2/M3/M4) | **Standard Mac install** | about 0.1–0.2 GB | Uses the Mac's GPU automatically (called MPS). There is no separate CUDA version on Mac. |

Sizes you should expect:

| Build | Download (zip) | On disk |
|---|---|---|
| Windows CPU-only app (ready-made) | about 165 MB | about 470 MB |
| Windows CUDA app (you build it) | about 2.8 GB *(estimate)* | about 5.6 GB *(estimate)* |
| Running from source, CPU | about 0.5 GB (Python packages) | |
| Running from source, CUDA | about 3–5 GB (Python packages) | |

> **Honest status of these instructions.** The Windows CPU-only route was tested end to end. The **CUDA** and **Mac**
> routes use the standard PyTorch and PyInstaller procedures but were **not** tested on real NVIDIA or Mac hardware by the
> authors of this guide. If a step fails on your machine, please open an issue and include the output.

---

## A. Download the ready-made app (Windows)

1. Go to the project's **Releases** page on GitHub and download **`EdSeg-CPU-only-Windows.zip`** (about 165 MB).
2. **Extract the whole zip** to a normal folder such as `Documents\EdSeg`. Don't run it from inside the zip.
3. Open the folder and double-click **`EdSeg.exe`**.
4. Windows may say **"Windows protected your PC"** because the app isn't code-signed. Click **More info → Run anyway**.
   This only happens the first time.
5. Keep `EdSeg.exe` next to its `_internal` folder. Your trained models and uploaded data are saved beside `EdSeg.exe`.

If the window opens in your web browser instead of its own window, your PC lacks Microsoft's free *WebView2 Runtime*.
EdSeg still works: keep the small "EdSeg is running" box open, and click OK when you want to quit.

---

## B. Run from source

Works the same way on Windows, macOS and Linux. Install **Python 3.10, 3.11 or 3.12** first (tested on 3.11) from
<https://www.python.org/downloads/>. On Windows, tick **"Add python.exe to PATH"** in the installer.

Download the source (green **Code → Download ZIP** button on GitHub, or `git clone`), open a terminal in that folder, then
follow **one** of the three recipes below. Every recipe has the same shape: *make an isolated environment → install the
common packages → install PyTorch (this is the step that differs) → run.*

### B1. Windows, CPU-only

```powershell
python -m venv venv
venv\Scripts\activate
python -m pip install --upgrade pip
pip install -r requirements.txt
pip install torch --index-url https://download.pytorch.org/whl/cpu
python launcher.py
```

### B2. Windows (or Linux) with an NVIDIA GPU — CUDA

1. Make sure the NVIDIA driver is installed. In a terminal run `nvidia-smi`. Look at the **"CUDA Version"** at the top right,
   e.g. `12.7`. That is the *highest* CUDA your driver supports.
2. Pick the PyTorch download address that matches. As of 25 Oct 2026, PyTorch 2.14.1 (the version EdSeg was tested with)
   is published for **two** CUDA flavours only:

   | `nvidia-smi` says | Use this `--index-url` ending |
   |---|---|
   | 13.0 or higher | `cu130` (or `cu126`) |
   | 12.6 – 12.9 | `cu126` |
   | lower than 12.6 | update your NVIDIA driver first (or use an older PyTorch from <https://pytorch.org/get-started/previous-versions/>) |

   Newer PyTorch versions will publish other endings (`cu128`, …). If `pip` says *"no matching distribution"*, check which
   endings exist today at <https://pytorch.org/get-started/locally/> and pick the highest your driver supports.
3. Install (this example uses `cu126`; the PyTorch download is **about 2.6 GB**, so it takes a while):

```powershell
python -m venv venv
venv\Scripts\activate
python -m pip install --upgrade pip
pip install -r requirements.txt
pip install torch --index-url https://download.pytorch.org/whl/cu126
python -c "import torch; print('CUDA available:', torch.cuda.is_available(), torch.version.cuda)"
python launcher.py
```

On Linux use `source venv/bin/activate` instead of `venv\Scripts\activate`.
The check line must print `CUDA available: True`. In EdSeg, open **System info** (top right): *NVIDIA GPU (CUDA)* should say
*Available*, and **Compute device → Auto** will then train on the GPU.

### B3. macOS (Apple Silicon: M1, M2, M3, M4)

```bash
python3 -m venv venv
source venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
pip install torch
python launcher.py
```

- The standard PyTorch download for Mac already includes GPU support (MPS). In EdSeg, **System info** should show
  *Apple Silicon GPU (MPS): Available*, and **Auto** uses it.
- Recent PyTorch versions are published for **Apple Silicon only**. On an old Intel Mac you would need an older PyTorch release
  (this has not been tried).
- If macOS says Python isn't installed or `python3` is missing, install it from python.org or with Homebrew (`brew install python`).

### Using EdSeg in your browser instead of its own window

Handy if the app window won't open. Run `python backend/app.py` and open the address it prints (it ends in `?k=…`;
use exactly that link, because EdSeg only talks to the page that carries its one-time key).

### Check that it works

```bash
python launcher.py --selftest report.json
```

This builds and runs all 13 models, trains 2D and 3D examples and checks the security settings. Open `report.json`: every
step should say `"ok": true`. It takes about a minute on a CPU.

---

## C. Build the desktop app yourself

PyInstaller **cannot cross-compile**: build on Windows for the Windows app, on a Mac for the Mac app. Use a **separate
virtual environment for each build flavour** so the wrong PyTorch can never end up in the wrong build.

### C1. Windows CPU-only app (about 165 MB zip)

```powershell
python -m venv build_cpu
build_cpu\Scripts\activate
python -m pip install --upgrade pip
pip install -r requirements-build.txt
pip install torch --index-url https://download.pytorch.org/whl/cpu
pyinstaller launcher.spec --noconfirm --clean
python build_tools\finalize_build.py --dist dist\EdSeg --flavor cpu --out release --scratch scratch
```

### C2. Windows CUDA app (about 2.8 GB zip, estimate)

```powershell
python -m venv build_cuda
build_cuda\Scripts\activate
python -m pip install --upgrade pip
pip install -r requirements-build.txt
pip install torch --index-url https://download.pytorch.org/whl/cu126
pyinstaller launcher.spec --noconfirm --clean
python build_tools\finalize_build.py --dist dist\EdSeg --flavor cuda --out release --scratch scratch
```

Use the `cu…` ending that matches your driver (see B2; `cu126` is the safe choice for any driver that reports CUDA 12.6 or higher). Building takes noticeably longer and needs about **15 GB of free disk space**.
A **CUDA build is far larger than GitHub's 2 GB limit per release file**, so host it elsewhere or split it.
Anyone running a CUDA build still needs an NVIDIA driver; the build is only "CUDA-capable" on PCs that have one,
and works as CPU-only on PCs that don't.

### C3. Mac app

```bash
python3 -m venv build_mac
source build_mac/bin/activate
python -m pip install --upgrade pip
pip install -r requirements-build.txt
pip install torch
pyinstaller launcher.spec --noconfirm --clean
codesign --force --deep --sign - dist/EdSeg.app        # free ad-hoc signature; avoids the "damaged" message
ditto -c -k --sequesterRsrc --keepParent dist/EdSeg.app EdSeg-Mac.zip
```

`finalize_build.py` is written for the Windows `.exe`, so on a Mac run the self-test by hand:
`dist/EdSeg.app/Contents/MacOS/EdSeg --selftest report.json`.

**Opening the app on another Mac:** macOS blocks apps it can't verify. **Right-click `EdSeg.app` → Open → Open** the first time.
If it says the app is *"damaged"*, run `xattr -cr /path/to/EdSeg.app` once, then open it again.

What `finalize_build.py` does (Windows): removes C++ header files, **self-tests the packaged app (the build fails if any step fails)**,
measures the real sizes, writes `build_info.json` (shown in the app under *System info → About this build*) and makes the zip.
It also copies `LICENSE` and `THIRD_PARTY_NOTICES.txt` next to the app, as the licences of the bundled libraries require.

---

## Troubleshooting

| Problem | What to do |
|---|---|
| "Windows protected your PC" | Normal for an unsigned app. **More info → Run anyway.** |
| White or blank window / opens in the browser | Install Microsoft's free *Edge WebView2 Runtime*, then start EdSeg again. Until then it works in your browser. |
| `CUDA available: False` after the CUDA install | Check `nvidia-smi` works; make sure the `cu…` index matches your driver; reinstall PyTorch in a **fresh** venv (installing a second PyTorch over the first often leaves the CPU one in place). |
| `pip` can't find a matching PyTorch | Try the next lower `cu…` index, or a supported Python version (3.10–3.12). |
| "Address already in use" / port busy | EdSeg picks a free port automatically; close any other copy of EdSeg and try again. |
| Mac: "app is damaged" / unidentified developer | Right-click → Open, or `xattr -cr EdSeg.app`. |
| PowerShell says "running scripts is disabled" when activating the venv | Use Command Prompt (`venv\Scriptsctivate.bat`) or run `Set-ExecutionPolicy -Scope Process Bypass` first. |
| Antivirus quarantines `EdSeg.exe` | Unsigned PyInstaller apps are sometimes flagged. Build it yourself (section C) or allow the folder. |
| Out of disk space during a build | CPU build needs about 3 GB free; CUDA about 15 GB. |

## Where your files go, and how to uninstall

Everything EdSeg creates (trained models, reports, uploaded datasets, a small log) is saved in folders **next to `EdSeg.exe`**
(or in the project folder when running from source). EdSeg makes **no network connections** other than to itself.
To uninstall, delete the folder (and the `venv` folder if you ran from source). Use only public or fully de-identified images.
EdSeg is an educational tool, **not a medical device**.

## Licence

MIT – see [`LICENSE`](./LICENSE). Licences of the bundled third-party libraries: [`THIRD_PARTY_NOTICES.txt`](./THIRD_PARTY_NOTICES.txt).
