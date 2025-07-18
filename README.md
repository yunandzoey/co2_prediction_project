# CO₂ Emissions Prediction for Major Economies

**Predicting annual country‑level CO₂ emissions (2013 – 2022) with classical regression and time‑series techniques**

## Project Overview

This repository contains the code, data and report for my *Economic Modeling of Energy & Climate Systems* (EMECS) course project at RWTH Aachen.  
We modelled and predicted **total CO₂ emissions** for five of the world’s largest emitters—**China, Germany, India, Russia and the United States**—using

* **Exploratory Data Analysis**: statistical tests, normality checks, outlier detection
* **Feature Engineering**: correlation & mutual‑information ranking, PCA for multicollinearity diagnostics
* **Models**  
  * *Linear Regression* (+ LassoCV / RidgeCV & feature selection)  
  * *ARIMA/SARIMA* (with/without exogenous drivers)
* **Evaluation**: Mean Squared Error (MSE) & error %, cross‑validated on 2013‑2021, predicting 2022

The work demonstrates how a well‑tuned yet simple linear model can outperform more complex time‑series models when data volume is low and relationships are close to linear.


## Key Findings 🧑‍🔬

| Country | Best Model | MSE | Key Drivers |
|---------|-----------|------|-------------|
| China | Linear Regression | **0.0003** | Population, Gas CO₂, Coal CO₂, Flaring CO₂, Oil CO₂, Electricity Generation |
| Germany | Linear Regression | **≈ 0** | All features |
| India | Linear Regression | 0.19 | Population, Gas CO₂, Coal CO₂, Flaring CO₂, Oil CO₂, Electricity Generation |
| Russia | RidgeCV | 0.13 | Population, Methane, Gas CO₂, Flaring CO₂, Electricity Generation |
| United States | LassoCV | 36.9 | Population, GDP, Coal CO₂, Gas CO₂, Oil CO₂ |

* **Linear models beat ARIMA/SARIMA** on all five countries because the annual series is short (10 points) and largely trend‑less.
* **Population and fossil‑fuel‑specific CO₂** emissions consistently rank as the strongest predictors.
* Hyper‑parameter tuning & regularisation provided marginal gains—indicating relationships are already near‑linear.


## Repository Layout
```
├── data/                           # raw data
├── notebooks/
│   ├── 01_data_exploratory.ipynb   # full EDA workflow
│   ├── 02_predict_lr.ipynb         # linear‑regression workflow
│   └── 03_predict_ts.ipynb         # ARIMA / SARIMA workflow
├── environment.yml                 # # conda environment
└── README.md                       # you are here
```

## Quick Start 🚀
```bash
# 1. Clone your fork
$ git clone https://github.com/<your‑user>/co2‑emission‑prediction.git
$ cd co2‑emission‑prediction

# 2. Create & activate environment
$ conda env create -f environment.yml
$ conda activate co2‑predict

# 3. Open JupyterLab and execute notebooks in order
$ jupyter lab
# Run ➡ 01_data_exploratory.ipynb ➡ 02_predict_lr.ipynb ➡ 03_predict_ts.ipynb

```
> **Tip:** The notebooks are 100 % reproducible—no external API calls, all data shipped in `data/`.


## Methodology in Depth
1. **EDA**: Shapiro‑Wilk normality test, Pearson correlation heatmap, Grubbs test for outliers.
2. **Feature Analysis**: PCA scree plot & loadings, mutual‑information scores.
3. **Modelling**
   * *Linear Regression* baseline ➡ LassoCV/RidgeCV hyper‑parameter search.
   * *ARIMA/SARIMA*: `p`, `d`, `q`, `P`, `D`, `Q`, `m` tuned via `pmdarima.auto_arima` and grid search; with/without exogenous fuel‑specific CO₂ features.
4. **Evaluation**: Mean Squared Error (MSE); error % vs actual 2022 emissions.

## Why Linear Regression (outperforms) ARIMA?
* **Short annual series** (10 data points) → ARIMA can’t detect robust seasonality.
* **Weak autocorrelation** shown in ACF/PACF plots.
* Relationships between drivers & emissions are **quasi‑linear**, confirmed by scatter plots & VIF < 3.


## Interactive Dashboard 🖥️

Explore the data & model results through the live Tableau Public dashboard:

[**Carbondioxide CO₂ Emissions Prediction – Tableau Public**](https://public.tableau.com/app/profile/chiu.yunhan/viz/Book3_17527782785920/CarbondioxideCO2EmissionsPrediction)

> *Interactively filter by country, check emission and drivers trends, and compare model perforance by evaluation metrics.*

## Future Work 
* Extend dataset to **monthly** frequency for richer time‑series modelling.
* Incorporate **energy‑price indices** (oil, gas futures) & policy shocks as exogenous variables.
* Package the best model as a **REST API** (FastAPI + Docker) for real‑time forecasting.

## Built With
| Category | Stack |
|----------|-------|
| Language | Python 3.11 |
| Data Ops | pandas, numpy, scikit‑learn, statsmodels, pmdarima, openpyxl |
| Visuals | matplotlib, seaborn |
| Environment | Jupyter Lab, conda |

## Authors
* **Yunhan Chiu** – *Data Scientist & M.Sc. DA&DS ’25*  
* **Chi Wen Lee** – *M.Sc. DA&DS ’25*

> This project was completed as part of the *Economic Modeling of Energy & Climate Systems* (EMECS) course at RWTH Aachen University, 2024.

## License
Distributed under the **MIT License**. See LICENSE for details.