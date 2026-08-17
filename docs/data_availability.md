# Data Availability

This document describes the provenance, access conditions, and licensing of every dataset used in this study, complementing the Data Availability Statement published in the article.

## 1. Tooth-instance-segmentation dataset

**Source photographs:** publicly available, de-identified intraoral photographs from Ahmed et al. (2025), *Annotated intraoral image dataset for dental caries detection*, Scientific Data, 12, 1297. (DOI: https://doi.org/10.1038/s41597-025-05647-9) (Zenodo repository).

**Annotations:** the polygon instance-segmentation masks (eight structural tooth categories) were produced specifically for this study by the collaborating team, using the Roboflow platform. These annotations are a derivative product created for this project and are not published independently elsewhere.

**Access:** the source photographs are public through the original Ahmed et al. repository (link above). The polygon annotation files are available from the corresponding author upon reasonable request.

**Participant-level verification:** the received dataset was independently verified and re-partitioned at the participant/session level — see `notebooks/00_data_preparation/data_preparation_tooth/Tratado_0_Verificacion_Participante_Segmentacion.ipynb` and `Tratado_1_dataset_Segmentacion_Dientes.ipynb` — after an initial check found 45 unique identifiers shared across its own subsets and with the caries-detection evaluation set (Section 2.2.1 of the article). The evidence tables from this check are available in `results/data_preparation/data_tooth/`.

## 2. Caries-detection dataset

**Source:** the same public dataset of Ahmed et al. (2025), downloaded in YOLO bounding-box format from their Zenodo repository.

**Processing applied in this study:** the full 9566-image collection was subjected to the same quality filter used elsewhere in this study (minimum resolution and sharpness via Laplacian variance), with the sharpness threshold auto-calibrated at 2.25 (5th percentile of this dataset's own distribution); 9087 images (95.0%) passed this filter. Of these, 219 images were selected via stratified sampling proportional to the eight intraoral views, subjected to the same preprocessing pipeline applied to the caries set, and labeled with empty annotation files. Because this dataset does not provide explicit patient/session identifiers, a grouping key was inferred heuristically from the numeric substring present in each filename (201 unique groups, 16 with more than one image); the 219 selected images were then assigned to training/validation/test using a group-based split (`GroupShuffleSplit`, 70/15/15by group), with zero group overlap across subsets — see `notebooks/00_data_preparation/data_preparation_caries/Tratado_3_dataset_Shweta.ipynb`.

**Access:** public via the original Ahmed et al. repository. The exclusion lists and processed splits generated in this study are included in `results/data_preparation/` of this repository.

## 3. External negative images (caries-free teeth)

**Source:** Chaudhary et al. (2024), *Teeth or Dental Image Dataset*, Mendeley Data. https://doi.org/10.17632/6zsnhrds9t.1

**Processing applied in this study:** quality filtering using a minimum resolution of 640 × 480 px and a Laplacian-variance sharpness threshold of 2.25, corresponding to the 5th percentile of the external dataset's own distribution. A total of 9087 images passed this filter. From this pool, 219 images were selected through stratified sampling proportional to the eight available intraoral views. Because the dataset does not provide explicit participant or acquisition-session identifiers, a heuristic grouping key was derived from the numeric substring present in each filename. GroupShuffleSplit was then applied to these inferred groups using a 70/15/15 split as a best-effort safeguard against cross-partition leakage. The selected images were incorporated as negative examples with empty YOLO annotation files.

**Access:** public via the original Chaudhary et al. Mendeley repository.

## 4. Code, pipelines, and derived result tables

**Contents of this repository:** all data-preparation, training, ablation, and geometric-association notebooks (`notebooks/`), the CSV result tables and exclusion lists generated during the study (`results/`), and documentation of the computational environment (`docs/environment.md`).

**License:** see [`LICENSE`](LICENSE) at the repository root. The code is released under a permissive open-source license; the derived CSV/result tables are released under the same terms unless otherwise noted.

**Not included in this repository:** raw source photographs from Ahmed et al. or Chaudhary et al. (redistribution restricted by their original licenses — access them directly via the DOIs above), and trained model weights (`.pt` files), which exceed GitHub's practical size limits; see `models/README.md` for the download instructions.

## 5. Summary table

| Dataset | Original source | Redistributed here? | Access |
| --- | --- | --- | --- |
| Segmentation photographs | Ahmed et al. [15] | No | Original Zenodo DOI |
| Segmentation polygon annotations | This study (collaborating team) | No | Corresponding author, upon reasonable request |
| Caries-detection photographs + boxes | Ahmed et al. [15] | No | Original Zenodo DOI |
| External negative photographs | Chaudhary et al. | No | Original Mendeley DOI |
| Deduplication/exclusion lists, split tables, result CSVs | This study | Yes | This repository, `results/` |
| Trained model weights (.pt) | This study | No (link only) | See `models/README.md` |
| Pipelines and evaluation code | This study | Yes | This repository, `notebooks/` |
