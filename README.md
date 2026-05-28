# Interpretable rtMRI Vocal Tract Segmentation and Tract-Variable Extraction

This repository contains the implementation work for a capstone research project on **real-time MRI (rtMRI) vocal tract analysis**. The project develops an interpretable pipeline that converts grayscale rtMRI speech videos into anatomical masks, ordered articulator curves, Haskins-style tract variables, and, in the later stage, a tract-variable-constrained vocal tract reconstruction model.

The current implementation focuses on a USC-TIMIT rtMRI proof of concept using **SAM2-based prompt-guided video propagation** for broad vocal tract region segmentation.

---

## Project Overview

Real-time magnetic resonance imaging (rtMRI) provides midsagittal video sequences of the vocal tract during speech. Unlike acoustic analysis, which only captures the sound output, rtMRI allows direct observation of internal articulators such as the tongue, lips, velum, pharyngeal wall, palate, jaw, and airway.

However, rtMRI is difficult to analyse automatically because:

- the images are grayscale and often low contrast;
- anatomical boundaries can be ambiguous;
- articulators deform continuously during speech;
- neighbouring structures may touch or appear visually similar;
- segmentation must remain temporally stable across video frames.

This project treats segmentation as an **intermediate step**, not the final goal. The aim is to build a complete interpretable pipeline:

```text
rtMRI video frames
        ↓
SAM2-based R1/R2/R3 region segmentation
        ↓
Ordered articulator and vocal tract curve extraction
        ↓
Haskins-style tract-variable computation
        ↓
Tract-variable-constrained vocal tract shape reconstruction
```

---

## Research Motivation

The project was originally motivated by the use of **SAM3** for vocal tract articulator segmentation. Early experiments showed that SAM3, SAM3.1, and MedSAM3 did not maintain stable anatomical regions across grayscale rtMRI sequences in zero-shot use. Their concept-based segmentation behaviour was not well aligned with the need for manual-mask-guided anatomical propagation.

The project therefore shifted to **SAM2**, which is better suited because it supports:

- mask-based prompts;
- video propagation;
- memory-based temporal tracking;
- correction using additional anchor frames;
- controlled propagation of manually defined anatomical regions.

---

## Segmentation Strategy

The first-stage segmentation uses three broad anatomical regions:

| Region | Description |
|---|---|
| **R1** | Tongue, lower lip, lower teeth, chin, neck, epiglottis |
| **R2** | Arytenoid region, pharyngeal wall, back, trachea |
| **R3** | Hard palate, velum, nasal cavity, nose, upper lip |

This broad-region strategy is used because directly tracking many small articulators independently can be unstable in low-contrast rtMRI. Stable R1/R2/R3 masks provide a more reliable foundation for later curve extraction.

---

## Current Progress

The current proof-of-concept implementation uses the USC-TIMIT rtMRI sequence:

```text
usctimit_mri_f1_001_005_withaudio
```

Completed work includes:

- conversion of rtMRI video into ordered frame sequences;
- custom Plotly-based annotation tool for 14 vocal tract structures;
- manual/anchor mask preparation for R1, R2, and R3;
- SAM2 Hiera-Large propagation with overlapping 80-frame windows;
- use of anchor/correction frames including frames 57, 67, and 200;
- visual inspection of propagated masks;
- quantitative evaluation against ground-truth annotation curves;
- confirmation that SAM3, SAM3.1, and MedSAM3 were not stable in zero-shot grayscale rtMRI use;
- initial colour-coded annotation curve extraction.

### Preliminary Result

The SAM2-based R1/R2/R3 propagation achieved approximately:

```text
Mean Dice coefficient ≈ 0.89
```

on the USC-TIMIT proof-of-concept sequence.

This is comparable to supervised speech MRI segmentation approaches reported in the literature, while avoiding task-specific training at this stage.

---

## Planned Pipeline Stages

### 1. Dataset Creation

Create a frame-level USC-TIMIT dataset linking:

- rtMRI frames;
- propagated R1/R2/R3 masks;
- ordered articulator curves;
- tract-variable measurements;
- confidence flags and manual-check indicators.

### 2. Curve Extraction

Convert broad region masks into ordered anatomical curves, including:

- tongue surface curve;
- upper lip curve;
- lower lip curve;
- hard palate curve;
- velum curve;
- pharyngeal wall curve;
- airway/vocal tract boundary curve.

The planned method uses contour extraction, anatomical sub-region selection, arc-length resampling, and smoothing.

### 3. Tract-Variable Extraction

Compute Haskins-style tract variables from extracted curves:

| Variable | Meaning |
|---|---|
| **LA** | Lip aperture |
| **LP** | Lip protrusion |
| **TTCD** | Tongue tip constriction degree |
| **TTCL** | Tongue tip constriction location |
| **TBCD** | Tongue body constriction degree |
| **TBCL** | Tongue body constriction location |
| **VEL** | Velic aperture |

The planned output format is:

```text
Frame | LA | LP | TTCD | TTCL | TBCD | TBCL | VEL | Confidence | Manual_Check
```

### 4. SAM2 Fine-Tuning for Sydney Hospital Data

After the USC-TIMIT proof of concept, the pipeline will be adapted to rtMRI data from Sydney hospitals. The fine-tuning workflow will include:

1. manual mask preparation on representative hospital frames;
2. baseline off-the-shelf SAM2 evaluation;
3. SAM2 fine-tuning using manually checked hospital masks;
4. adapted-model evaluation using the same metrics.

### 5. Tract-Variable-Constrained Reconstruction

The final model-development stage will test whether tract variables provide a compact and interpretable bottleneck for vocal tract shape representation.

The proposed reconstruction pathway is:

```text
X → Y_hat → X_hat
```

where:

- `X` is the vocal tract shape represented by ordered curves;
- `Y_hat` is the predicted tract-variable vector;
- `X_hat` is the reconstructed vocal tract shape.

A second reconstruction test will use:

```text
Y → X_hat
```

to check whether true tract variables alone are sufficient to reconstruct vocal tract shape.

---

## Evaluation Plan

The project evaluates each stage separately because errors propagate through the pipeline.

### Segmentation Evaluation

Metrics include:

- Dice coefficient;
- Intersection over Union (IoU);
- Hausdorff distance;
- Average symmetric surface distance (ASSD);
- centroid displacement;
- frame-to-frame area variation;
- visual overlay inspection.

### Curve Extraction Evaluation

Metrics include:

- mean contour distance;
- Hausdorff distance;
- ASSD;
- point-order consistency;
- curve length consistency;
- frame-to-frame curve displacement;
- curve smoothness.

### Tract-Variable Evaluation

Metrics include:

- visual plausibility of measured constrictions;
- temporal smoothness;
- outlier detection;
- confidence flags;
- manual-check labels;
- comparison with visible articulatory motion.

### Reconstruction Evaluation

Planned metrics include:

- curve reconstruction error;
- mean point distance;
- tract-variable prediction error;
- reconstruction smoothness;
- comparison of predicted-variable and true-variable reconstruction.

---

## Repository Structure

The repository structure may evolve as the project develops. A suggested structure is:

```text
.
├── data/
│   ├── raw_videos/
│   ├── frames/
│   ├── masks/
│   ├── curves/
│   └── tract_variables/
│
├── notebooks/
│   ├── annotation_tool.ipynb
│   ├── sam2_propagation.ipynb
│   ├── curve_extraction.ipynb
│   └── tract_variable_analysis.ipynb
│
├── src/
│   ├── preprocessing/
│   ├── segmentation/
│   ├── curve_extraction/
│   ├── tract_variables/
│   └── reconstruction/
│
├── results/
│   ├── overlays/
│   ├── plots/
│   ├── metrics/
│   └── figures/
│
├── docs/
│   └── report/
│
├── requirements.txt
└── README.md
```

---

## Installation

Create a Python environment:

```bash
python -m venv venv
source venv/bin/activate      # macOS/Linux
venv\Scripts\activate         # Windows
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Suggested core dependencies:

```text
numpy
opencv-python
matplotlib
plotly
pandas
scipy
torch
torchvision
tqdm
jupyter
```

SAM2 should be installed following the official SAM2 repository instructions.

---

## Usage

### 1. Extract frames from rtMRI video

```bash
python src/preprocessing/extract_frames.py \
  --video data/raw_videos/usctimit_mri_f1_001_005_withaudio.avi \
  --output data/frames/usctimit_mri_f1_001_005/
```

### 2. Prepare manual anchor masks

Use the annotation notebook or tool to create R1/R2/R3 masks on selected anchor frames.

Example anchor frames:

```text
57, 67, 200
```

### 3. Run SAM2 propagation

```bash
python src/segmentation/run_sam2_propagation.py \
  --frames data/frames/usctimit_mri_f1_001_005/ \
  --masks data/masks/anchors/ \
  --output results/masks/
```

### 4. Evaluate segmentation

```bash
python src/segmentation/evaluate_masks.py \
  --pred results/masks/ \
  --gt data/masks/ground_truth/ \
  --output results/metrics/segmentation_metrics.csv
```

### 5. Extract curves and compute tract variables

```bash
python src/curve_extraction/extract_curves.py
python src/tract_variables/compute_tract_variables.py
```

---

## Current Status

| Stage | Status |
|---|---|
| USC-TIMIT proof of concept | Completed |
| SAM2 R1/R2/R3 propagation | Completed for first sequence |
| SAM3/SAM3.1/MedSAM3 zero-shot testing | Completed |
| Custom annotation tool | Completed |
| Ground-truth curve extraction | Initial implementation completed |
| Automatic curve extraction | In progress |
| Tract-variable table generation | Planned |
| Sydney hospital adaptation | Planned |
| SAM2 fine-tuning | Planned |
| Reconstruction model | Planned |

---

## Next-Semester Timeline

| Stage | Description | Weeks |
|---|---|---|
| 1 | USC-TIMIT proof of concept | Completed |
| 2 | USC-TIMIT dataset creation: curves, reference system, tract-variable table | W1–W4 |
| 3 | Pipeline refinement: segmentation, curve and tract-variable error categories | W3–W5 |
| 4 | SAM2 fine-tuning for Sydney hospital data | W5–W7 |
| 5 | Reconstruction model development | W7–W10 |
| 6 | Evaluation, extension and final thesis writing | W10–W13 |

---

## Key Contributions

This project contributes:

1. an interpretable rtMRI vocal tract analysis pipeline;
2. a broad-region R1/R2/R3 segmentation strategy for stable SAM2 propagation;
3. a manual-mask-guided workflow for grayscale rtMRI video;
4. a custom annotation and quality-control workflow;
5. a planned frame-level tract-variable dataset;
6. a pathway from segmentation masks to curves, tract variables and vocal tract reconstruction.

---

## Limitations

Current limitations include:

- results are currently based on one USC-TIMIT sequence;
- broader validation across additional speakers and sequences is still required;
- automatic curve extraction is still under development;
- tract-variable computation has not yet been completed for the full sequence;
- Sydney hospital adaptation and fine-tuning are planned for the next stage;
- reconstruction modelling is a future development stage.

---

## Author

**Tushar Khupte**  
Master of Professional Engineering (Electrical)  
The University of Sydney

---

## Acknowledgement

This repository is part of a capstone research project on rtMRI vocal tract segmentation, articulatory analysis and tract-variable-based reconstruction.

---

## License

This repository is intended for academic research and coursework purposes. Add a formal license before public release if required.
