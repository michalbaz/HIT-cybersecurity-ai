# Lab 2 — Anomaly Detection
This lab compares two anomaly-detection approaches, Isolation Forest and Autoencoder with categorical embeddings.

## Contents
- `AnomalyDetectionComparison.ipynb` — Main notebook containing synthetic data generation, EDA, both models, quantitative comparison, PCA visualizations, SOC alert/disagreement analysis, and conclusion.

## How to Run (Google Colab / Kaggle)
Instead of running Docker locally, this notebook can be executed directly in the cloud:

1. **Google Colab:** Upload `AnomalyDetectionComparison.ipynb` to [Google Colab](https://colab.research.google.com/), then click **Runtime → Restart session and run all**.
2. **Kaggle:** Upload `AnomalyDetectionComparison.ipynb` as a new notebook on [Kaggle](https://www.kaggle.com/) and click **Run All**.

## Key Findings
- On this dataset and with these settings, the **Autoencoder with embeddings** outperformed **Isolation Forest** (higher precision, recall, and F1 score, with fewer false positives on legitimate overnight `backup_job` events).
- Both models missed stealthy `mimic_attack` events designed to blend into normal working hours, highlighting why a SOC analyst should combine multiple detectors with human context rather than relying on a single model.
