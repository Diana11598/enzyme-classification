## Main Libraries

- Python
- Biopython
- scikit-learn
- pandas
- NumPy
- matplotlib
- SHAP
- XGBoost

# Enzyme Classification from Protein Sequences

A binary classifier that distinguishes **enzymes from non-enzymes** using handcrafted sequence-derived descriptors and classical machine learning algorithms. (amino acid composition, dipeptide
composition, and physicochemical properties) and classical machine learning
models (Random Forest, XGBoost, and others).

## Overview
This project develops a machine learning pipeline for binary enzyme classification using handcrafted protein sequence descriptors extracted from FASTA sequences.

| | |
|---|---|
| **Task** | Binary classification: enzyme (1) vs. non-enzyme (0) |
| **Input** | Protein sequences (FASTA) |
| **Features** | Amino acid composition (20), dipeptide composition (400), physicochemical properties (6) — 426 total, reduced to 165 via feature selection |
| **Best model** | Tuned Random Forest (GridSearchCV) |
| **Result** | ROC-AUC = 0.85, Accuracy = 77% on a balanced held-out test set |

## Pipeline

1. **Data loading & balancing** — load enzyme/non-enzyme sequences from FASTA, undersample the majority class for a balanced dataset.
2. **Feature extraction** — amino acid composition (AAC), dipeptide composition (DPC), and physicochemical properties (aromaticity, instability index, isoelectric point, secondary structure fractions) via Biopython's `ProteinAnalysis`.
3. **Baseline model comparison** — Logistic Regression, KNN, Random Forest, Extra Trees, Gradient Boosting, Hist Gradient Boosting, compared via stratified cross-validation.
4. **Feature set comparison** — AAC alone vs. AAC+DPC vs. AAC+DPC+physicochemical.
5. **Feature selection** — `SelectFromModel` with a Random Forest estimator, reducing 426 → 165 features at negligible cost to ROC-AUC.
6. **Hyperparameter tuning** — `GridSearchCV` over the Random Forest on the full feature set.
7. **Evaluation** — classification report, ROC-AUC, confusion matrix, ROC curve.
8. **Error analysis**  — analyzes the misclassified samples to understand model failure cases.
9. **Interpretability** — feature importance ranking and SHAP summary/bar plots.
10. **Bonus comparison** — XGBoost vs. the tuned Random Forest on the same features (see notebook for an honest discussion of why the untuned XGBoost model underperformed here, and what a fair comparison would require).

## Data

This project expects two FASTA files in the working directory:
- `enzymes.fasta` — positive class (enzyme sequences)
- `non_enzymes.fasta` — negative class (non-enzyme sequences)

**The FASTA files are not included in this repository due to size/licensing considerations**.

## Results

| Feature Set | # Features | ROC-AUC |
|---|---|---|
| AAC only | 20 | 0.806 |
| AAC + DPC | 420 | 0.822 |
| AAC + DPC + Physicochemical | 426 | 0.822 |

| Feature Selection | # Features | ROC-AUC |
|---|---|---|
| Before | 426 | 0.822 |
| After | 165 | 0.818 |

| Final Model | Accuracy | ROC-AUC |
|---|---|---|
| Random Forest (tuned) | 0.769 | 0.845 |
| XGBoost (untuned) | 0.746 | 0.829 |

**Limitations:** these results use sequence-composition features only, with no
evolutionary (PSSM) or structural information. This caps achievable
performance — published methods that add PSSM profiles or pretrained protein
language model embeddings (ESM2, ProtBERT) typically report notably higher
accuracy on similar tasks. These approaches represent promising directions for future work.

## Setup

```bash
pip install -r requirements.txt
```

Run `Enzyme_Classification_Final.ipynb` top to bottom. Figures are saved to `figures/`.

## Project Structure

```
.
├── Enzyme_Classification_Final.ipynb   # main notebook
├── requirements.txt
├── README.md
├── enzymes.fasta                        # not included — see Data section
├── non_enzymes.fasta                    # not included — see Data section
└── figures/                             # generated plots (created on run)
```
## figures/
    ├── roc_curve.png
    ├── confusion_matrix.png
    ├── feature_importance.png
    ├── shap_summary.png
    └── shap_bar.png

    ## Citation

If you use this work, please cite this repository.


## License

This project is licensed under the [MIT License](LICENSE).

## Author

**Diana Eldeen**
Bioinformatics | Machine Learning | Python



## Future Work

Possible improvements include:

- Using the complete imbalanced dataset with class weighting instead of undersampling.
- Incorporating evolutionary descriptors such as PSSM profiles.
- Using protein language model embeddings (ESM2 or ProtBERT).
- Hyperparameter optimization for XGBoost using Optuna or RandomizedSearchCV.
- External validation on an independent benchmark dataset.

