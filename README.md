# Zen-TMT offline bundle

Dependencies for [Zensar Content Studio](https://github.com/V-JasonEmmanuel/Zen-TMT) on machines that can reach GitHub with git but not pypi.org, download.pytorch.org or huggingface.co. Corporate proxies often allow only GitHub.

You do not use this repository directly. When `start.bat` cannot reach the Python package index, it clones this repository into `offline\`. It then reassembles the files and installs everything from it, with no other downloads.

| Folder | Contents |
|---|---|
| `wheels/` | All Python packages for **Windows x64, Python 3.12**. This includes PyTorch 2.6.0 with CUDA 12.4 (GPU build, also works on CPU) and diffusers/transformers for image generation. |
| `models/embeddings/` | `sentence-transformers/all-MiniLM-L6-v2` exported to ONNX (Apache-2.0) |
| `models/imagegen/` | `Lykon/dreamshaper-8-lcm`, a Stable Diffusion 1.5 LCM model (CreativeML OpenRAIL-M) |
| `extras/` | Microsoft Visual C++ 2015-2022 Redistributable (x64) |

GitHub refuses files over 100 MB, so large files are stored as numbered pieces (`*.part000`, ...). `manifest.json` records the size and SHA-256 of every file. Zen-TMT's `scripts/offline_bundle.py assemble` joins the pieces and verifies every checksum.

## Rebuilding (on a PC with internet)

From a Zen-TMT checkout, run these against an empty folder for this repository:

```bat
python -m pip download -r requirements.txt --platform win_amd64 --python-version 3.12 --implementation cp --only-binary=:all: -d <bundle>\wheels
python -m pip download -r requirements-images.txt --platform win_amd64 --python-version 3.12 --implementation cp --only-binary=:all: -d <bundle>\wheels
python -m pip download "torch==2.6.0+cu124" --index-url https://download.pytorch.org/whl/cu124 --platform win_amd64 --python-version 3.12 --implementation cp --only-binary=:all: -d <bundle>\wheels
python scripts\offline_bundle.py build <bundle>
```

Packages that are published only as source (for example bibtexparser 1.4) are built first with `pip wheel <pkg> --no-deps -w <bundle>\wheels`.

## Licences

Every package keeps its own licence, which is included in its wheel. PyTorch is BSD-3-Clause. The image model is distributed under the [CreativeML OpenRAIL-M licence](https://huggingface.co/spaces/CompVis/stable-diffusion-license); its use restrictions apply to anyone using it. The Visual C++ Redistributable is redistributed under Microsoft's redistribution terms.
