# PneumonAI

**Detect. Predict. Breathe Easy.**

PneumonAI, a Pneumonia classifier developed at Stanford AIMI Summer Internship Program. Built Multimodal DenseNet-121 pneumonia classifier with metadata fusion, Monte Carlo Dropout uncertainty estimation, and Grad-CAM analysis. Awarded 1st Place (out of 7 teams) with a perfect methodology score from 5 Stanford AIMI faculty judges.

Stanford Center for Artificial Intelligence in Medicine and Imaging (AIMI)  
High School Internship Research Project

**Team:** Ashok, Bharat, Matthew, Rachel, Saanvi, Shankar, Sharan  
**Team Lead:** Natasha

---

## Overview

PneumonAI is a machine learning pipeline for reliable detection and localization of pneumonia from chest X-rays. The project prioritizes minimizing false negatives and explores multimodal approaches that combine imaging with radiology reports to support clinical decision-making.

Pneumonia is a leading cause of mortality worldwide, accounting for approximately **2.5 million deaths annually**. Early and accurate detection remains challenging due to subtle radiographic findings, class imbalance in datasets, and the difficulty of precisely localizing fuzzy disease boundaries.

### Project Goal

> Build ML models to reliably detect pneumonia and its location using chest X-rays while mitigating false negatives.

---

## Key Contributions

- **Classification** — Compared multiple CNN and Vision Transformer architectures (ResNet-18/50, DenseNet-121, Swin-Tiny/Base, MaxViT) with systematic ablation studies.
- **Localization** — Implemented Fast R-CNN with a ResNet-50 backbone for bounding-box detection of pneumonia regions.
- **Multimodal Learning** — Combined ResNet-50 (vision) + ClinicalBERT (text) via late fusion, achieving up to **93%** accuracy when radiology reports are available.
- **Class Imbalance Mitigation** — Explored 3-class formulations, inverse class weighting, data augmentation, and dropout to reduce bias and improve generalization.
- **Explainability & Failure Analysis** — Used Grad-CAM to identify model reliance on non-clinical artifacts (e.g., view-position labels).
- **Uncertainty Quantification** — Investigated Bayesian CNNs with Monte Carlo Dropout to flag ambiguous cases for human review.

---

## Datasets

Two supervised datasets were provided:

| Dataset | Content | Annotations |
|---------|---------|-------------|
| **Dataset 1** | Chest X-rays | Binary labels (pneumonia / normal) + bounding boxes |
| **Dataset 2** | Chest X-rays + Radiology reports | Binary labels + free-text impressions |

Class imbalance was significant (many more normal than pneumonia cases), which motivated weighting and multi-class experiments.

---

## Methodology

### Classification Models

We evaluated:

- **CNNs**: ResNet-18, ResNet-50, DenseNet-121
- **Transformers**: Swin V2 (Tiny & Base)
- **Hybrid**: MaxViT

**Ablation studies** systematically varied:

- Data augmentation (horizontal flips, small rotations, resizing)
- Class imbalance handling (class weighting)
- Learning rate
- Training duration (epochs)

**Best classification result (ablation):**  
**Swin-Tiny** — no augmentation, learning rate `1e-4`, 4 epochs → **AUC 0.862**

### Localization

- Backbone: **ResNet-50 + Fast R-CNN**
- Metric: Intersection over Union (IoU)
- Baseline F1 ≈ 41%
- Improvements: data augmentation (flips + color jitter) and learning-rate scheduling over 7 epochs

### Multimodal (Vision + Text)

```
Chest X-ray → ResNet-50 (spatial features)
Radiology Report → ClinicalBERT (clinical context)
                ↓
          Late Fusion
                ↓
        Dense Classifier → Pneumonia probability
```

Results (when reports are available and the model is forced to use visual evidence):

| Setting              | Accuracy |
|----------------------|----------|
| Vision-only          | ~85%     |
| Multimodal (filtered)| **93%**  |

### Additional Explorations

| Approach          | Purpose                              | Outcome |
|-------------------|--------------------------------------|---------|
| **3-Class Learning** | Separate normal / non-pneumonia abnormal / pneumonia | +4.82% accuracy; AUC ≈ 87% |
| **Bayesian CNN**  | Uncertainty estimation via MC Dropout | Flags low-confidence cases |
| **MedSAM**        | Segmentation masks                   | Correct location but poor box scaling |
| **MedGemma**      | Automated report generation          | Dead-end (frequent disagreements with radiologist labels) |

---

## Key Results Summary

| Task                        | Best Model / Setting              | Metric          | Score    |
|-----------------------------|-----------------------------------|-----------------|----------|
| Binary Classification       | Swin-Tiny (ablation)              | AUC             | 0.862    |
| Multimodal Classification   | ResNet-50 + ClinicalBERT (late fusion) | Accuracy     | **93%**  |
| Transfer Learning (Notebook 4) | Dataset-2 pretrained ResNet-50 | AUROC / Acc / F1 | +0.022 / +1.57% / +0.029 |
| 3-Class Formulation         | Weighted + Augmented              | AUC             | ~0.87    |
| Localization                | Fast R-CNN + ResNet-50            | F1 (baseline)   | ~41%     |

---

## Model Failure Analysis (Grad-CAM)

Grad-CAM visualizations of the best Swin-Tiny model revealed:

- Heavy attention on image borders / sides
- Reliance on textual artifacts (view-position labels)

**Recommendation for future work:** Crop image edges or apply masks to remove non-clinical text and force the model to focus on lung parenchyma.

---

## Broader Impacts & Ethical Considerations

- Models must **not** be used as standalone diagnostic systems without external validation.
- Class imbalance and site-specific imaging equipment can introduce fairness issues across patient populations.
- Clear communication of model uncertainty is essential to avoid over-reliance and missed diagnoses (false negatives).
- The intended role is a **“second pair of eyes”** that assists radiologists and potentially reduces cognitive load / burnout.

---

## Future Work

1. Crop or mask view-position labels and other edge artifacts.
2. External validation of Swin-Tiny and ResNet pipelines on multi-institution datasets.
3. Pilot studies measuring real-world impact of the clinical assistance pipeline on physician workload.
4. Further refinement of localization (box scaling) and uncertainty-aware deployment.

---

## Project Structure (Suggested)

```
PneumonAI/
├── notebooks/          # Classification, localization, multimodal, ablation studies
├── models/             # Saved weights / configs
├── data/               # (not included — follow data use agreements)
├── figures/            # Grad-CAM, performance plots, etc.
├── presentation/       # Final research presentation (PDF)
└── README.md
```

---

## References

- Cilloniz, C., et al. (2024). World Pneumonia Day 2024: Fighting Pneumonia and Antimicrobial Resistance. *PMC*.
- Stanford AIMI. Localizing Pneumonia with Object Detection (High School Internship Research Materials).
- Additional architecture references for Vision Transformers and DenseNet as used in the project.

---

## Acknowledgments

This work was conducted as part of the **Stanford AIMI High School Internship**. We thank the AIMI team, our mentors, and the clinical collaborators who provided guidance and datasets.

---

*PneumonAI — Detect. Predict. Breathe Easy.*
```
