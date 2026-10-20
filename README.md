# Urban Development Age Classification | Cross-City Transfer Learning

**Machine learning · Remote sensing · Domain adaptation · Scikit-learn**

A reproducible notebook-based study of **urban development-age classification from multi-temporal Landsat features**. Models are developed on **Madrid** and evaluated for their ability to generalize to **Amsterdam**, including adaptation with limited labelled target-domain samples.

The key challenge is **geographic domain shift**: the spectral and temporal signatures learned in one city do not necessarily transfer directly to another. This repository benchmarks a source-only model against several few-shot adaptation strategies and analyses how performance changes as labelled Amsterdam data become available.

## Research questions

1. How accurately can Landsat-derived features distinguish development-age classes in the source city?
2. How much does performance change when a Madrid-trained classifier is applied directly to Amsterdam?
3. Which adaptation strategies work best at different target-label budgets?
4. How stable are the results across repeated support draws and across classes?

## Workflow

```text
Raw Landsat observations + labelled pixel IDs
                    |
                    v
01_feature_engineering.ipynb
  - annual time series, feature reconstruction and audits
  - 126 features and spatially grouped held-out split
                    |
                    v
02_model_selection.ipynb
  - candidate classifier screening and confirmation
  - Madrid lockbox evaluation and final source model
                    |
                    v
03_domain_adaptation.ipynb
  - zero-shot Madrid -> Amsterdam baseline
  - prototypes, calibration, target SVM and diagnostics
  - repeated few-shot experiments
                    |
                    v
04_results_and_evaluation.ipynb
  - learning curves, class-level error and stability analysis
  - confusion matrices and consolidated outputs
```

## Repository contents

| File | Purpose |
| --- | --- |
| [`01_feature_engineering.ipynb`](01_feature_engineering.ipynb) | Temporal feature construction, quality audits, and spatial partitioning |
| [`02_model_selection.ipynb`](02_model_selection.ipynb) | Madrid model screening, confirmation, and fitted source model |
| [`03_domain_adaptation.ipynb`](03_domain_adaptation.ipynb) | Amsterdam zero-shot and few-shot evaluation |
| [`04_results_and_evaluation.ipynb`](04_results_and_evaluation.ipynb) | Consolidated metrics, robustness checks, and visual analysis |
| [`requirements.txt`](requirements.txt) | Python dependencies |

## Data and feature engineering

The input data consist of multi-year Landsat observations with band measurements and validity flags, together with pixel IDs and class labels for Madrid and Amsterdam. Data inputs must be supplied separately; **the raw datasets and generated pickle artifacts are not included** in this repository.

The engineered feature set contains **126 variables**:

| Feature group | Count |
| --- | ---: |
| Baseline temporal and spectral features | 60 |
| Decade-level means | 55 |
| Local five-year volatility | 11 |
| **Total** | **126** |

Six spectral bands (Blue, Green, Red, NIR, SWIR1, SWIR2) and indices such as NDVI, NDBI, UI, MNDWI and BSI underpin the time-series summaries. The feature notebook also audits reconstruction against a provided reference representation and inspects missing-observation edge filling.

## Modeling and evaluation

- **Source domain:** Madrid. **Target domain:** Amsterdam.
- **Primary metric:** multiclass **Macro-F1**, so performance for minority classes contributes equally to the average.
- **Source-model comparison:** a lower-cost scout screen followed by five-fold stratified confirmation and an independent Madrid holdout check. The holdout is spatially grouped; the cross-validation folds should **not** be conflated with spatial CV.
- **Transfer baseline:** direct zero-shot prediction with Madrid-trained preprocessing and classifier.
- **Few-shot budgets:** **5, 25, 50, 100, and 200 labelled pixels per class**.
- **Repetitions:** **30 seeded draws** per supported label budget in the adaptation experiment.
- **Adaptation methods:** mean/median/feature-weighted/shrinkage prototypes, score calibration of the frozen source model, target RBF-SVM, and other documented comparisons.
- **Additional diagnostics:** per-class F1, variability across draws and optional spatial-buffered query evaluation.

**Leakage precautions.** Source preprocessing is fitted on training data during model evaluation. Adaptation hyperparameters are selected using support labels rather than target query labels. Optional methods that inspect the whole unlabelled Amsterdam feature distribution are disabled by default and should be disclosed if enabled.

## Illustrative findings

The saved analysis notebook documents the following approximately rounded results from the recorded experiment; figures depend on the provided data and artifacts and **have not been independently rerun for this repository cleanup**.

| Metric | Recorded result |
| --- | ---: |
| Madrid source validation Macro-F1 | ~0.6768 |
| Amsterdam zero-shot Macro-F1 | ~0.3943 |
| Absolute source-to-target difference | ~0.2825 |
| Best reported few-shot method | Target RBF-SVM |
| Average Macro-F1 over official label budgets | ~0.6098 |
| 25-shot Macro-F1 | ~0.5950 |
| Highest mean Macro-F1 across budgets | ~0.6998 |

These figures are taken from the summary embedded in the original final-analysis notebook; generated analysis artifacts are the source of truth when rerunning. The average across budgets is a descriptive summary rather than an estimate of performance at one fixed label budget.

## Setup and execution

1. Use **Python 3.10+** and install dependencies:

   ```bash
   python -m venv .venv
   # Windows: .venv\Scripts\activate
   # macOS/Linux: source .venv/bin/activate
   python -m pip install -r requirements.txt
   ```

2. Supply the original inputs referenced in `01_feature_engineering.ipynb`. Inspect the **input-discovery cells** before running. The provided notebooks were written for **Kaggle** and use `/kaggle/input` and `/kaggle/working` for data discovery and outputs; these paths must be adapted when running elsewhere.
3. Run the notebooks **in numeric order**. Subsequent stages locate saved artifacts such as `features.pkl` and `final_model.pkl`. If working across separate Kaggle sessions, attach prior outputs as input datasets.
4. Inspect generated CSV, JSON and PNG files under the stage-specific output directories (`01_features`, `track_b`, `track_c`, `track_d`). Keep output filenames unchanged unless you also update their consuming notebooks.

> **Reproducibility:** Code paths and dependency ranges are provided, but the end-to-end study cannot be reproduced without the underlying input files and generated stage artifacts. Dependencies have not been lockfile-pinned, and this repository cleanup does not claim a fresh execution of all notebooks.

## Limitations and next steps

The study focuses on two cities and limited target labels; external validity for other cities remains untested. Nearby remote-sensing pixels may be correlated, so ordinary cross-validation and few-shot query estimates can be optimistic without stronger spatial separation. A useful extension is multi-city external validation and a fully spatially independent transfer protocol, alongside sensitivity analysis for missing observation coverage.

## Technologies

Python, NumPy, pandas, SciPy, scikit-learn, Matplotlib, Jupyter and Kaggle.

## Project status

**Research prototype / portfolio case study.** Notebooks include recorded outputs where present; no model service or production deployment is claimed.
