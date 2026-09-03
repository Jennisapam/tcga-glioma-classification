# tcga-glioma-classification
# TCGA Glioma Classification

A machine-learning project that classifies TCGA samples as **lower-grade glioma (LGG)** or **glioblastoma (GBM)** using clinical information and gene-mutation features.

Completed during my MSc **AI for Medical Device** module, this project combines model development with an evidence-generation strategy for potential healthcare implementation.

> **Research prototype only:** The models have undergone internal cross-validation but have not been externally or clinically validated. They should not be used for patient diagnosis or treatment decisions.

## Project Overview

The project includes two components:

- **Technical analysis:** Data preparation, classification, feature selection, ensemble learning, evaluation and interpretation.
- **Translational strategy:** Proposed clinical validation, care-pathway integration, stakeholder engagement, health economics and regulatory considerations.

## Dataset

The supplied TCGA glioma dataset contains **861 records and 27 original columns**.

| Detail | Value |
|---|---|
| Total records | 861 |
| LGG records | 499 — 57.96% |
| GBM records | 362 — 42.04% |
| Gene-mutation predictors | 20 |
| Clinical/demographic predictors | Age, gender and race |
| Predictors before encoding | 23 |
| Encoded predictors in the full-data fit | 25 |

The target is encoded as:

- **0 = GBM**
- **1 = LGG**

The model excludes `Case_ID`, `Project` and `Primary_Diagnosis` to avoid using identifiers and diagnosis-related information as predictors.

**Dataset format:** Although the supplied file is named `tcga_glioma_2026.xls`, it contains CSV-formatted text. Rename it to **`tcga_glioma_2026.csv`** before running the notebook.

## Data Preparation

The notebook:

1. Converts missing-value markers such as `Not Reported` and `--` to missing values.
2. Converts age strings into numerical years.
3. Removes identifier and diagnosis-related columns.
4. Imputes missing age values using the training median.
5. Imputes categorical values using the most frequent training value.
6. Standardises age and one-hot encodes categorical predictors.

| Column | Missing entries before imputation |
|---|---:|
| Race | 22 |
| Age at diagnosis | 5 |
| Gender | 4 |
| Primary diagnosis | 4 |

`Primary_Diagnosis` is removed from the predictors.

Imputation, scaling, encoding and feature selection are fitted **inside each cross-validation training fold**.

## Models Compared

Five baseline classifiers were evaluated:

- Logistic Regression
- Support Vector Machine
- Random Forest
- Gradient Boosting
- AdaBoost

Four classifiers were also evaluated with feature selection:

- Logistic Regression
- Support Vector Machine
- Random Forest
- Gradient Boosting

The notebook explores **15 combinations of three or four classifiers** using soft voting. A separate final pipeline combines **Logistic Regression, SVM and Random Forest with feature selection**.

## Feature Selection

The project uses **L1-regularised Logistic Regression with `SelectFromModel`**.

In the final full-data interpretation fit, this reduced **25 encoded predictors to 19 selected features**. Selection is repeated within each training fold during evaluation, so the selected set can vary.

PCA and autoencoders were not used in this project.

## Evaluation

Models were evaluated using **stratified five-fold cross-validation**, with shuffling and `random_state=42`.

The evaluation metrics were:

- Accuracy
- Precision
- Recall
- F1-score
- Specificity
- ROC-AUC

> **Class interpretation:** Precision, recall and F1 refer to **LGG**, the positive class in the code. Specificity measures the proportion of **GBM samples correctly classified**.

### Models With Feature Selection

| Model | Accuracy | LGG Recall | LGG F1 | ROC-AUC |
|---|---:|---:|---:|---:|
| Logistic Regression | 87.46% | 84.98% | 0.8868 | 0.9237 |
| SVM | 87.46% | 83.78% | 0.8855 | 0.9197 |
| Gradient Boosting | 85.83% | 83.58% | 0.8722 | 0.9251 |
| Random Forest | 85.02% | 84.98% | 0.8681 | 0.9165 |

Logistic Regression had the highest unrounded accuracy in this comparison, effectively tying with SVM.

### Final Soft-Voting Pipeline

The saved notebook reports these rounded mean cross-validation results for the **Logistic Regression + SVM + Random Forest ensemble with feature selection**:

| Metric | Result |
|---|---:|
| Accuracy | Approximately **87.6%** |
| LGG precision | **93.6%** |
| LGG recall | **84.4%** |
| LGG F1-score | **0.887** |
| Specificity / GBM recall | **92.0%** |
| ROC-AUC | **0.927** |

Compared with Logistic Regression with feature selection, the ensemble showed a small increase in accuracy and ROC-AUC. F1 was almost unchanged, while LGG recall decreased slightly.

These are saved experimental results, not a newly reproduced benchmark. No statistical significance test was conducted to establish that the ensemble is superior.

## Explainability

The notebook examines:

- Logistic Regression coefficients after feature selection.
- Supplementary Gradient Boosting feature importances.

IDH1 mutation status, age and IDH2 mutation status were prominent in the Gradient Boosting analysis.

These analyses describe model behaviour and associations within this dataset. They do not establish causation or independently validate clinical usefulness.

## Healthcare Implementation Strategy

The accompanying translational report proposes:

- A target product profile.
- A role for the model within the glioma diagnostic pathway.
- External, multicentre and prospective validation.
- Assessment of clinical utility and cost-effectiveness.
- Engagement with clinicians, patients, IT teams and regulators.
- Regulatory planning and ongoing performance monitoring.

These are **proposed development activities**, not completed clinical studies. The consultancy scenario involving DAIM-ABX is hypothetical.

## Technologies Used

- Python
- Jupyter Notebook
- pandas
- NumPy
- scikit-learn
- Matplotlib

## Project Files

| File | Description |
|---|---|
| `Jenifer_Sapam_202536619_Tcga_Notebook (2).ipynb` | Analysis, saved results and figures |
| `tcga_glioma_2026.xls` | CSV-formatted dataset; rename to `.csv` before running |
| `Jenifer_Sapam_202536619_TechnicalComponent.docx (1) (1).pdf` | Technical methods, results and interpretation |
| `Jenifer_Sapam_202536619__EvidGenStrat_TranslationalComponent.docx (1).pdf` | Evidence-generation and implementation strategy |
| `README.md` | Project documentation |

## How to Run

### 1. Download the Project

Download or clone the repository. Keep the notebook and dataset in the same folder.

### 2. Rename the Dataset

Rename:

```text
tcga_glioma_2026.xls
```

to:

```text
tcga_glioma_2026.csv
```

The supplied file already contains CSV-formatted text, so no Excel conversion is needed.

### 3. Install Dependencies

```bash
python -m pip install notebook pandas numpy scikit-learn matplotlib
```

### 4. Launch Jupyter Notebook

From the project folder, run:

```bash
jupyter notebook
```

Open the TCGA notebook and run its cells in order.

The ensemble search evaluates multiple configurations across five folds and may take longer than the individual-model comparisons.

**Reproducibility note:** Exact package versions are not pinned in the supplied files. The notebook contains saved outputs, but a fresh end-to-end run has not been verified for this README.

## Limitations and Responsible Use

### Validation

Results come from one retrospective dataset. No external, prospective or independent clinical validation was performed.

Multiple configurations were compared using the same cross-validation folds. Nested cross-validation or an untouched test set would provide a stronger estimate after model selection.

### Bias and Generalisability

Stratification preserves class proportions but does not establish fairness. Performance across age, gender and race groups was not separately validated.

### Clinical Scope

The model predicts the dataset's LGG/GBM labels. It does not provide a complete clinical diagnosis.

### False Positives and False Negatives

Because LGG is the positive class:

- A **false positive** is a GBM sample predicted as LGG.
- A **false negative** is an LGG sample predicted as GBM.

Clinical interpretation must name the actual classes rather than assuming that “positive” means GBM.

### Data Privacy and Governance

Excluding identifiers from model inputs does not itself constitute anonymisation or a privacy assessment. Dataset provenance, reuse permissions and governance would need review before wider distribution or deployment.

### Human Oversight

Any future clinical application would require professional review and evidence that it improves care.

The metric definitions in this README follow the notebook's actual encoding. Where wording in the reports differs, use **GBM = 0 and LGG = 1** to interpret the results.

## Future Improvements

- Use nested cross-validation or an independent test dataset.
- Report confidence intervals and calibration.
- Evaluate subgroup performance.
- Validate externally across institutions.
- Select thresholds against clearly defined clinical error costs.
- Package the trained pipeline with versioned dependencies.
- Test clinical utility through appropriately designed studies.

## Author

**Jenifer Sapam**

[GitHub Profile](https://github.com/Jennisapam)
