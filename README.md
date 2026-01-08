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


## Repository Layout
```
├── data/                           # Raw data (OWID)
├── 01_data_exploratory.ipynb       # EDA, Statistical Tests, and PCA
├── 02_predict_lr.ipynb             # Linear Regression & Regularization (Lasso/Ridge)
├── 03_predict_ts.ipynb             # Time Series Analysis (ARIMA/SARIMA)
├── result_lr_by_country.csv        # Regression results
├── result_ts.csv                   # Time series results
├── environment.yml                 # Conda environment configuration
└── README.md                       # Documentation
```

## Quick Start 🚀

```bash
# 1. Clone the repository
$ git clone https://github.com/yunandzoey/co2_prediction_project.git
$ cd co2_prediction_project

# 2. Create & activate environment
$ conda env create -f environment.yml
$ conda activate co2-predict

# 3. Open JupyterLab and execute notebooks in order
$ jupyter lab
# Run:
# 1. 01_data_exploratory.ipynb  (Exploration & Feature Engineering)
# 2. 02_predict_lr.ipynb        (Drivers Analysis)
# 3. 03_predict_ts.ipynb        (Temporal Forecasting)
```
> **Tip:** The notebooks are 100 % reproducible—no external API calls, all data shipped in `data/`.


## Data & Features 📊
The project utilizes data from **Our World in Data (OWID)**.
- **Target Variable**: `co2` (Annual total CO₂ emissions).
- **Exogenous Drivers**:
    - `population`: Total population.
    - `gdp`: Gross Domestic Product.
    - `coal_co2`, `gas_co2`, `oil_co2`, `flaring_co2`: Sector-specific emissions.
    - `methane`: Methane emissions.
    - `electricity_generation_twh`: Total electricity generation.

## Methodology in Depth

### Exploratory Data Analysis (EDA) 🔍
Before modelling, we perform rigorous diagnostics to understand the data's quality and underlying distributions:
- **Shapiro-Wilk Normality Test**: 
    - *Purpose*: To check if features follow a Gaussian distribution, a key assumption for classical OLS Linear Regression.
    - *Insight*: Many variables (like GDP and Population) showed significant skewness, leading us to use **StandardScaler** and regularized models (Lasso/Ridge) to handle potential non-linearities and scale differences.
- **Pearson Correlation Heatmap**: 
    - *Purpose*: To identify linear relationships between drivers and CO₂ emissions, and to detect multicollinearity.
    - *Insight*: Confirmed extremely high positive correlations (r > 0.9) between total emissions and specific fuel CO₂ types, confirming they are primary drivers.
- **Grubbs Test for Outliers**: 
    - *Purpose*: Statistically detect deviant data points that could disproportionately influence a linear model's slope.
    - *Insight*: Ensured the stability of the regression coefficients by identifying and treating anomalous shifts in the annual series.
- **Kruskal-Wallis H Test**:
    - *Purpose*: A non-parametric test used to determine if there are statistically significant differences between the distributions of CO₂ emissions across multiple countries.
    - *Insight*: Confirmed that CO₂ emission levels vary significantly by region, validating the "two-pronged" per-country modelling strategy.
    - **Results**:
      | Feature | H-Statistic | p-value | Result |
      | :--- | :--- | :--- | :--- |
      | `co2` | 47.05 | 1.48e-09 | Reject Null |
      | `gdp` | 39.55 | 5.35e-08 | Reject Null |
      | `population` | 45.92 | 2.55e-09 | Reject Null |
- **ACF & PACF Plots**: 
    - *Purpose*: To analyze autocorrelation and partial autocorrelation for time-series stationarity.
    - *Insight*: Confirmed weak autocorrelation in the short annual series, providing further justification for the underperformance of ARIMA models.

### Understanding Diagnostic Outputs 📔
To help interpret the statistical tests above, we use two key metrics:
- **Statistic**: A numerical value computed by the test (e.g., H-statistic for Kruskal-Wallis or W-statistic for Shapiro-Wilk). It measures the magnitude of the difference or deviation being tested.
- **p-value**: The probability of obtaining the observed results (or more extreme results) if the null hypothesis is true. A **p-value < 0.05** typically indicates statistical significance, leading us to reject the null hypothesis (e.g., rejecting that data is normally distributed).

### Feature Analysis & Engineering 🛠️
- **PCA (Principal Component Analysis)**: 
    - *Purpose*: To visualize high-dimensional data and diagnose multicollinearity via factor loadings.
    - *Insight*: Scree plots revealed that over 95% of total variance is captured by just the first 2-3 components, suggesting high feature redundancy which justifies the use of **Lasso (L1)** for feature selection.
    - **PC1 Loading Scores**:
      | Feature | PC1 Loading |
      | :--- | :--- |
      | `gdp` | 0.4079 |
      | `elec_gen` | 0.4066 |
      | `oil_co2` | 0.4007 |
      | `methane` | 0.3978 |
      | `coal_co2` | 0.3444 |
- **Variance Inflation Factor (VIF)**:
    - *Purpose*: To quantify the severity of multicollinearity among exogenous variables.
    - *Insight*: Confirmed manageable VIF levels (< 5) after selecting key drivers, ensuring stable coefficient estimates in the regression models.
- **Mutual Information (MI)**: 
    - *Purpose*: To capture any dependency between features and the target, including non-linear ones that Pearson might miss.
    - *Insight*: Consistently ranked `coal_co2` and `gas_co2` as the most informative features, reinforcing the strategy to include them as exogenous drivers in the time-series models.

## Modelling
- **Cross-sectional/Drivers Analysis**: Linear Regression (OLS) as a baseline, followed by LassoCV and RidgeCV for regularization.
- **Temporal Analysis**: ARIMA/SARIMA models tuned via `pmdarima.auto_arima`.
4. **Evaluation**: Models are trained on data up to 2021 and evaluated based on their Mean Squared Error (MSE) against actual **2022 emissions**.

## Why Linear Regression Outperforms ARIMA?
In this specific dataset:
- **Small Data Volume**: Annual data for only 10 years is insufficient for complex time series models like ARIMA/SARIMA to capture robust secondary patterns or seasonality.
- **Strong Linear Drivers**: CO₂ emissions are heavily tied to population, GDP, and fossil fuel consumption. These relationships are essentially linear, making OLS/Regularized Regression an extremely strong "driver-based" predictor.
- **Stationarity Issues**: The short annual series often lacks the autocorrelation structure required for ARIMA to effectively "learn" the trend beyond a simple linear projection.


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
* **Yunhan Chiu** – *M.Sc. DA&DS ’25*  
* **Chi Wen Lee** – *M.Sc. DA&DS ’25*

> This project was completed as part of the *Economic Modeling of Energy & Climate Systems* (EMECS) course at RWTH Aachen University, 2024.

## License
Distributed under the **MIT License**. See LICENSE for details.
