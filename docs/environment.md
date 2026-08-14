# Computational Environment

This document describes the hardware, software, and library versions used to run the notebooks in this repository, to support reproducibility.

## Execution platform

- **Platform:** Google Colab (hosted runtime).
- **GPU:** NVIDIA Tesla T4 (14.9 GB / ~14913 MiB).
- **Python version:** 3.12.

## Key library versions

Library versions differed slightly across notebooks because each was run in an independent Colab session, using whichever version was available from the package index at execution time. The versions actually used are noted per pipeline below.

| Library | Version(s) used | Notes |
| --- | --- | --- |
| `ultralytics` | 8.4.118 (tooth segmentation, final re-split dataset), 8.4.115 (caries detection, ablation, geometric association) | YOLO26 training/inference framework |
| `scikit-learn` | 1.6.1 | `GroupShuffleSplit` for group-based train/val/test partitioning |
| `torch` / `torchvision` | bundled with the Ultralytics install at execution time | CUDA-enabled build used during training; CPU build used for some later inference/evaluation-only sessions |
| `opencv-python` | bundled with the Ultralytics install | image I/O, Laplacian-variance sharpness filter, mask/box geometry operations |
| `imagehash` | latest available at execution time | perceptual hashing (pHash) for cross-dataset deduplication |
| `pandas` / `numpy` | latest available at execution time | dataset bookkeeping, statistics, CSV export |
| `albumentations` | latest available at execution time | targeted oversampling / augmentation for the minority "3rd Molar" class |
| `matplotlib` | latest available at execution time | training curves, distribution plots, figure generation |

## Per-pipeline notes

- **`notebooks/00_data_preparation/data_preparation_caries`**: run with `ultralytics` 8.4.92-era dependencies; uses `imagehash` + `hashlib` (SHA-256) for deduplication, `cv2.Laplacian` for sharpness filtering, and `scikit-learn` `GroupShuffleSplit` for all group-based partitioning steps.
- **`notebooks/00_data_preparation/data_preparation_tooth` (segmentation subfolder)**: participant/session-level leakage verification and group-based re-partitioning of the tooth-segmentation dataset, run before any training; excludes participants shared with the caries-detection evaluation set.
- **`notebooks/01_tooth_segmentation/`**: unified, resumable pipeline (pipeline_YOLO26_dientes.ipynb) training three YOLO26-seg variants (n/s/m) from scratch (pretrained=False) on the re-split, participant-verified dataset; periodic checkpoint sync to Drive makes each variant resumable after a Colab disconnect; batch size reduced for the medium variant only (8→4) due to GPU memory constraints.
- **`notebooks/02_caries_detection/`**: three YOLO26-det variants (n/s/m) fine-tuned from COCO-pretrained weights (`pretrained=True`); trained under `ultralytics` 8.4.115. The ablation notebook reuses the same detection architecture and hyperparameters, varying only the training set (with vs. without external negatives).
- **`notebooks/03_geometric_association/`**: inference-only pipeline (no training); combines the selected segmentation and detection checkpoints, and was run in some sessions on CPU-only Colab runtimes for evaluation, since no gradient computation is required at this stage.

## Reproducibility caveats

- **Random seeds:** fixed at `SEED = 42` across data partitioning (`GroupShuffleSplit`), training (`seed` argument in Ultralytics `.train()`), and Python's `random`/`numpy.random` modules where sampling was involved (e.g., stratified selection of external negatives).
- **Non-determinism:** exact reproduction of training curves and final metrics may vary slightly due to GPU non-determinism in CUDA kernels, differences in the exact `ultralytics` patch version installed at runtime, and Colab's variable hardware allocation across sessions.
- **Runtime resets:** all notebooks in this repository assume a fresh Colab runtime with no local execution state; Google Drive is mounted at the start of each notebook to read/write persistent data, and cached intermediate files (e.g., `blur_cache.csv`, `healthy_pool_quality_cache.csv`) are checked for and reused when present to avoid recomputation.

## How to reproduce

1. Open the desired notebook in Google Colab (or convert with `jupytext --to notebook <file>.md` if running locally with Jupyter).
2. Mount Google Drive and adjust the base paths (`BASE`, `BASE_DIENTES`, `BASE_CARIES`, etc.) to match your own directory structure.
3. Install `ultralytics` via `pip install ultralytics` (the exact version is not pinned in most notebooks; see the per-pipeline notes above for the version originally used).
4. Run notebooks in the order given by the numbered folders in `notebooks/` (`00_data_preparation` → `01_tooth_segmentation` and `02_caries_detection` → `03_geometric_association`), since later stages depend on the outputs of earlier ones.
