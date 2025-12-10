📈 **Data-Driven Surveillance: Lassa Fever Forecasting for Public Health Action**
---
![4 week rolling mean .png](plots%2F1%20eda%2F4%20week%20rolling%20mean%20.png)
## Table of Contents

- Project Overview
- Dataset
- Repository Structure
- Data Processing Workflow
- Exploratory Analysis
- Models Implemented
- Final Results & Performance
- Model Explainability
- Forecasting Approach
- Key Insights
- Public Health Relevance
- Limitations
- Next Steps
- Author

---

## Project Overview

This project builds a robust time-series forecasting system for weekly Lassa fever cases in Nigeria
using NCDC data from 2020 to 2025.
It combines classical statistical models and modern machine-learning methods,
with XGBoost emerging as the most accurate.
The goal is
to support public-health decision-making by providing reliable short-term forecasts
and identifying key drivers of transmission such as weekly momentum and seasonal patterns.

The pipeline covers:

- **Data ingestion**
- **Cleaning & preprocessing**
- Baseline **forecasting** models (naive, seasonal naive, moving average)
- **Classical forecasting models (ETS, SARIMA)**
- **Machine learning models (XGBoost)**
- **Model explainability (SHAP, permutation importance, gain)**
- **Performance comparison & epidemiological interpretation**

This project demonstrates how data-driven tools can support surveillance and early-warning systems for infectious diseases in low-resource settings.

---

## Dataset
- **Source:** [NCDC Lassa Fever Timeseries (2020–2025)](https://www.kaggle.com/datasets/emmanuelniyioriolowo/ncdc-lassa-fever-timeseries-20202025)
- **Description:** Weekly confirmed Lassa fever case counts extracted from official NCDC situation reports.
- **Frequency:** Weekly
- **Period covered:** January 2020 — December 2025

---

## Repository Structure

```
lassa-forecasting/
│
├── data/
│   ├── raw/                     # Original extracted weekly data
│   ├── processed/               # Cleaned + feature-engineered dataset
│
├── notebooks/
│   ├── 01_data_ingestion.ipynb
│   ├── 02_data_cleaning.ipynb
│   ├── 03_baseline_models.ipynb
│   ├── 04_classical_models.ipynb
│   └── 05_ml_models_xgboost.ipynb
│
├── plots/                       # Figures: seasonality, forecasts, SHAP, etc.
│   ├── eda models
│   ├── baseline models
│   ├── classical models
│   ├── machine learning models
└── README.md
```

---

## Data Processing Workflow

### **1. Data Ingestion Notebook**

- Consolidated weekly NCDC situation report tables into a single dataset
- Standardized epidemiological week formats and aligned year–week indices

### **2. Data Cleaning Notebook**

- Converted ambiguous report values (`"U"`, `"—"`) to `NaN`
- Reconstructed missing epidemiological weeks
- Normalized all case counts into consistent integers

### ✔️ **3. Feature Engineering**

Final feature set included:

| Category | Features |
| --- | --- |
| Lags | `lag_1` → `lag_52` |
| Rolling windows | 4-week, 12-week, and 52-week rolling mean & std |
| Time index | `epi_week` |

---

## Exploratory Analysis

Analyses included:

- Time-series trend
- ADF Stationarity tests
- 4 & week rolling means
- ACF/PACF
- Distribution checks
- Decomposition into trend, seasonal, residual

Clear **seasonal peaks** were observed during the **dry season**, matching known epidemiology.

---

## Models Implemented

### **Baseline Models**

- Naive
- Average
- Drift
- Seasonal Naive
- Seasonal Mean

### **Classical Models**

- ETS (Exponential Smoothing)
- SARIMA

### **Machine Learning**

- **XGBoost Regressor**
    - First model: 4 lags
    - Improved model: 52 lags
    - Final model: **52 lags + rolling windows** (best)

---

## Final Results & Performance

### **Performance Table**

| Model | MAE | RMSE | MAPE |
| --- | --- | --- | --- |
| Naive | 16.33 | 26.37 | 60.30 |
| Average | 16.20 | 20.94 | 129.03 |
| Drift | 17.69 | 27.12 | 69.84 |
| Seasonal Naive | 8.74 | 15.53 | 49.24 |
| Seasonal Mean | 7.91 | 12.25 | 44.11 |
| ETS | 8.06 | 12.79 | 43.90 |
| SARIMA | 7.64 | 12.45 | 44.46 |
| ⭐ **XGB (52 lags + rolling)** | **5.90** | **8.36** | **33.28** |

### Highlights

- **XGBoost outperformed all classical models by a wide margin.**
- MAE improved from **7.64 → 5.90** (SARIMA → XGB).
- RMSE dropped from **12.45 → 8.36**.
- Over **100% improvement** compared to some baselines.

---

## Model Explainability

Three interpretability tools were used:

| Method | Role |
| --- | --- |
| Gain Importance | How splits improve model performance |
| Permutation Importance | Drop in accuracy when feature is shuffled |
| SHAP Values | Feature contribution for each prediction |

### **Consistent Findings Across Methods**

- **4-week rolling mean** was the **strongest predictor**
- `lag_1` ranked second
- Seasonal lags (`lag_49`–`lag_52`) were highly influential
- `epidemic_week` encoded strong seasonality

**Interpretation:**

Lassa fever transmission depends heavily on recent momentum, seasonality, and position within the year.

---

## Forecasting Approach

### **Recursive Multi-Step Forecasting**

Used to predict weeks **47 → 52**:

1. Predict week *t+1*
2. Append prediction into history
3. Regenerate features
4. Predict week *t+2*
5. Repeat until horizon is reached

This mimics real-world forecasting deployment.

---

## Key Insights

- Increasing from **4 → 52** lags captured annual memory and improved performance.
- Adding **rolling means (4/12/52 weeks)** dramatically boosted accuracy.
- Feature importance aligned perfectly with known Lassa epidemiology:
    - **Dry-season seasonal peak**
    - **Short-term transmission momentum**
    - **Strong lag dependence**

---

## Public Health Relevance

Accurate weekly Lassa fever forecasts support critical public-health decisions, including:

- Hospital and treatment-center resource planning during peak transmission periods
- Early warning for state and national public-health agencies
- Targeted risk communication and community engagement in high-incidence regions
- Improved allocation of diagnostics, PPE, and rapid-response teams

---

## Limitations

- Forecasting focused on **short-term (1-week-ahead)** predictions; accuracy decreases at longer horizons
- NCDC surveillance data may contain reporting delays or inconsistencies
- Environmental and behavioural drivers (rainfall, humidity, rodent activity) were **not yet included**
- Model performance may vary across outbreak intensity levels

---

## Next Steps

Planned improvements:

### **Add Rainfall + Humidity Data**

- Environmental conditions influence rodent behavior
- Lagged rainfall/humidity variables may further improve accuracy
- Will evaluate using feature importance + SHAP

### Future Models

- N-BEATS
- TFT
- LightGBM
- Prophet with environmental regressors

---

## Author

**Emmanuel Niyi-Oriolowo**

- Medical Doctor
- Backend Python Developer
- Health Data Scientist