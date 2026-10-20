# Baseline Notebooks — Building Age Classification from Satellite Imagery

## Overview

This directory documents the **four original starter notebooks** supplied for the building-age classification hackathon hosted by **Hack4Dev in association with DARA**. They introduce the challenge and demonstrate an initial workflow for analysing Landsat observations, preparing pixel-level features, and evaluating building-age classification across cities.

The notebooks are kept separate from the project's developed solution in [`../notebooks/`](../notebooks/) so that readers can distinguish the **provided starting point** from subsequent experiments and improvements. The baseline code and its original explanations are not presented as the repository author's independent implementation.

## Challenge context

The task is to predict one of **four building-age classes** for each **30 m × 30 m Landsat pixel** containing buildings. The data cover **Madrid** (source city for model development) and **Amsterdam** (target city for transfer evaluation). The input time series contain six spectral bands: **Blue, Green, Red, NIR, SWIR1, and SWIR2**, with up to three candidate observations per pixel and year.

Evaluation emphasises **macro-averaged F1**, including Madrid cross-validation and generalisation to Amsterdam with limited labelled examples. The classification boundaries include city-specific historical construction dates and shared boundaries at 1984 and 2004.

## Notebook guide

| Notebook | Purpose | Main contents |
|---|---|---|
| [`1-Introduction.ipynb`](1-Introduction.ipynb) | Define the problem and evaluation protocol | Challenge background, Landsat data, age classes, Madrid-to-Amsterdam transfer objective, submission expectations |
| [`2-Reading_Data.ipynb`](2-Reading_Data.ipynb) | Explore the raw datasets | Parquet loading, quality checks, pixel coverage, class distributions, spectral time-series plots, temporal statistics, band correlations |
| [`3-Preprocessing.ipynb`](3-Preprocessing.ipynb) | Build model-ready pixel features | Age-class labelling, QA-valid observation selection, temporal gap filling, spectral indices, aggregated temporal features, standardisation, saved feature arrays |
| [`4-Modelling.ipynb`](4-Modelling.ipynb) | Establish a reference modelling workflow | Random Forest cross-validation, confusion matrices, feature importance, zero-shot Amsterdam evaluation, few-shot prototype classification and spatial uncertainty plots |

> **Filename note:** These are clean repository filenames. If your downloaded starter notebooks have extra `(1)` or similar suffixes, rename them to match these links before uploading.

## Workflow and inputs

Run the notebooks **in numerical order**. The introduction is explanatory; the later notebooks expect the original hackathon datasets and, in the modelling stage, outputs saved by preprocessing.

```text
project-root/
├── baseline/
│   ├── README.md
│   ├── 1-Introduction.ipynb
│   ├── 2-Reading_Data.ipynb
│   ├── 3-Preprocessing.ipynb
│   └── 4-Modelling.ipynb
├── notebooks/                 # Developed project notebooks
└── data/                      # Local input data; not committed unless permitted
    ├── madrid_train.parquet
    ├── amsterdam_data.parquet
    └── preprocessed/          # Created by Notebook 3
```

The starter notebooks use paths relative to their working directory, including `../data` and `../data/preprocessed/preprocessed_data.pkl`. Adjust paths if running the notebooks in a different directory or environment. Dataset files and generated artifacts are **not included** in this folder.

## Baseline methods at a glance

1. **Data exploration:** Inspect city-level observations and building-age labels, including temporal reflectance patterns and quality indicators.
2. **Preprocessing:** Select quality-controlled spectral observations, fill missing yearly values, derive indices and per-pixel temporal statistics, then prepare feature matrices.
3. **Source-city validation:** Assess a Random Forest classifier using repeated stratified cross-validation on Madrid.
4. **Transfer evaluation:** Apply the Madrid-trained classifier to Amsterdam without adaptation, then compare with prototype-based few-shot adaptation using labelled Amsterdam samples.

These steps describe the supplied notebooks, **not an independent benchmark against the final project**. For the developed experiments and their reported results, see the repository's main [`README.md`](../README.md) and [`notebooks/`](../notebooks/) directory.

## Attribution

**Special recognition** goes to **Hack4Dev**, in association with **DARA**, for hosting the hackathon and providing the starter materials used as the baseline for this project.

The original notebooks are retained for educational context, traceability, and attribution. Redistribution is subject to the hackathon organisers' applicable permissions or licence; including files here does not imply that a new licence has been granted for the supplied code or data.
