# Data Availability

This document describes the provenance, access conditions, and licensing of every dataset used in this study, complementing the Data Availability Statement published in the article.

## 1. Tooth-instance-segmentation dataset

**Source photographs:** publicly available, de-identified intraoral photographs from Ahmed et al. (2025), *Annotated intraoral image dataset for dental caries detection*, Scientific Data, 12, 1297. (DOI: https://doi.org/10.1038/s41597-025-05647-9) (Zenodo repository).

**Annotations:** the polygon instance-segmentation masks (eight structural tooth categories) were produced specifically for this study by the collaborating team, using the Roboflow platform. These annotations are a derivative product created for this project and are not published independently elsewhere.

**Access:** the source photographs are public through the original Ahmed et al. repository (link above). The polygon annotation files are available from the corresponding author upon reasonable request.

**Participant-level verification:** the received dataset was independently verified and re-partitioned at the participant/session level — see `notebooks/00_data_preparation/data_preparation_tooth/Tratado_0_Verificacion_Participante_Segmentacion.ipynb` and `Tratado_1_dataset_Segmentacion_Dientes.ipynb` — after an initial check found 45 unique identifiers shared across its own subsets and with the caries-detection evaluation set (Section 2.2.1 of the article). The evidence tables from this check are available in `results/data_preparation/data_tooth/`.

## 2. Caries-detection dataset

**Source:** Ahmed et al. (2025), *Annotated intraoral image dataset for dental caries detection*, Scientific Data, 12, 1297. The dataset was downloaded from its public Zenodo repository in YOLO bounding-box format. DOI: https://doi.org/10.1038/s41597-025-05647-9

**Initial dataset:** the downloaded collection contained 2227 valid image–annotation pairs.

**Processing applied in this study:** images were first filtered using a minimum resolution of 640 × 480 px and a sharpness criterion based on the variance of the Laplacian operator. The sharpness threshold was empirically recalibrated using a manual review of 80 images by two independent researchers. Among the evaluated thresholds, 20.0 was selected as the best-performing threshold according to the F1-Score, with balanced accuracy used as the tie-breaking criterion. After this recalibration, 1645 images remained in the quality-controlled pool.

A first cross-dataset deduplication pass, using SHA-256 and perceptual hashing (pHash), identified 180 unique images for exclusion from the caries-detection dataset because of overlap with the tooth-segmentation training or validation data. After the sharpness-threshold recalibration, a second independent verification identified 20 additional coincident images in the validation and test subsets. In addition, 27 images containing invalid bounding-box coordinates were removed.

The final Ahmed-derived caries-detection dataset contained 1445 images and 4569 valid bounding boxes. The dataset also included 36 native negative images with empty annotation files.

The images were partitioned at the participant/session level using `GroupShuffleSplit`, ensuring that no identifier was shared across the training, validation, and test subsets. The exclusion lists, deduplication records, processed split tables, and validation outputs are available in `results/data_preparation/` of this repository.

**Access:** the source photographs and original caries annotations are publicly available through the original Ahmed et al. repository. The processed files generated in this study are documented in this repository. Raw photographs are not redistributed here.

## 3. External negative images (caries-free teeth)

**Source:** Chaudhary et al. (2024), *Teeth or Dental Image Dataset*, Mendeley Data. DOI: https://doi.org/10.17632/6zsnhrds9t.1

**Initial dataset:** the original collection contained 9566 images of caries-free teeth distributed across eight intraoral views.

**Processing applied in this study:** the images were filtered using a minimum resolution of 640 × 480 px and a Laplacian-variance sharpness threshold of 2.25. This threshold corresponds to the 5th percentile of the distribution calculated within this external dataset. A total of 9087 images passed the quality filter.

From this pool, 219 images were selected through stratified sampling proportional to the eight available intraoral views. Because the Chaudhary et al. dataset does not provide explicit participant or acquisition-session identifiers, a heuristic grouping key was extracted from the numeric substring present in each filename. This procedure identified 201 inferred groups, 16 of which contained more than one image.

`GroupShuffleSplit` was applied to these inferred groups using a 70/15/15 train/validation/test split as a best-effort safeguard against cross-partition leakage. The selected images were distributed as 152 training images, 31 validation images, and 36 test images. They were incorporated as negative examples using empty YOLO annotation files.

**Access:** the original images are publicly available through the Chaudhary et al. Mendeley Data repository. The selected negative images are not redistributed in this repository.

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
