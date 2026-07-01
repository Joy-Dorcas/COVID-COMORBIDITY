
# COVID-19 Mortality Risk Scoring in Africa: XGBoost & SHAP Explainability

Why did reported COVID-19 mortality range from **1 death/million in Burundi** to **350+ deaths/million in Zimbabwe**? This project uses an explainable machine learning approach to find out — and the answer isn't what you'd expect.

## Overview

This project applies **XGBoost regression** and **SHAP (SHapley Additive exPlanations)** to identify the structural drivers of COVID-19 mortality variation across 23 African countries, using country-level surveillance data from [Our World in Data](https://ourworldindata.org/covid-deaths) (2020–2024).

Rather than direct comorbidity burden (diabetes, cardiovascular disease, age), **healthcare infrastructure capacity and development indices** emerge as the dominant predictors — a finding consistent with systematic under-reporting of COVID-19 deaths in lower-resource settings.

The project includes a detailed **Kenya case study** and a **Low / Medium / High risk stratification framework** intended to support pandemic preparedness prioritization.

## Key Findings

- **Hospital beds per thousand** was the single dominant predictor of mortality (mean \|SHAP\| = 0.63) — over 3x more important than any other feature.
- **Median age** and **% of population 65+**, despite strong raw correlation with mortality (r = 0.76–0.77), had near-zero SHAP importance once healthcare capacity and development indices were accounted for.
- The model achieved a **Leave-One-Out CV MAE of 52.3 deaths/million** and **R² = 0.325** on the log-transformed scale.
- **Kenya** was classified as *Medium risk*, with the model predicting 109.6 deaths/million against an actual 104.9 — one of the most accurate individual predictions in the dataset.
- Eight countries were flagged **High risk** for pandemic preparedness prioritization: Sao Tome and Principe, Zimbabwe, Zambia, Egypt, Comoros, Algeria, Gambia, and Malawi.

**Interpretation:** the model likely reflects *reporting capacity* rather than true biological mortality risk — countries with fewer hospital beds and lower HDI weren't necessarily spared, they were less equipped to diagnose and register COVID-19 deaths. This aligns with WHO excess-mortality estimates suggesting true African COVID-19 mortality was several-fold higher than official counts.

## Data

- **Source:** [Our World in Data COVID-19 dataset](https://ourworldindata.org/covid-deaths) (CC BY 4.0), covering 58 African countries, January 2020–August 2024 (95,419 country-day observations).
- **Aggregation:** daily time series aggregated to one row per country; target variable (total deaths/million) taken as peak cumulative value; comorbidity/structural features computed as country-level means.
- **Sample:** 25 countries with complete data on 10 features; 2 structural outliers (Tunisia, South Africa) excluded prior to modeling, yielding a final analytical sample of **23 countries**.

## Method

| Step | Approach |
|---|---|
| Model | XGBoost Regression (`XGBRegressor`), log1p-transformed target |
| Hyperparameters | `n_estimators=100`, `max_depth=3`, `learning_rate=0.05`, `subsample=0.8`, `colsample_bytree=0.8`, `reg_alpha=0.1`, `reg_lambda=1.0` |
| Validation | Leave-One-Out Cross-Validation (23 folds) |
| Explainability | SHAP TreeExplainer — global (beeswarm, bar) and country-level (force plots) |
| Risk stratification | Tertile split of model-predicted mortality into Low / Medium / High |

## Repository Structure

```
├── covid.ipynb                              # Full analysis notebook (data prep, model, SHAP, viz)
├── owid_covid_africa.csv                    # Raw OWID data, African countries
├── owid_covid_kenya.csv                     # Raw OWID data, Kenya subset
├── covid_comorbidity.docx                   # Full written report (methods, results, discussion)
└── README.md
```

## Running the Notebook

```bash
pip install pandas numpy scikit-learn xgboost shap matplotlib seaborn
jupyter notebook covid.ipynb
```

## Limitations

- **Ecological analysis** — country-level findings cannot be extrapolated to individual patient risk (ecological fallacy).
- Analytical sample (23 countries) covers less than half of African nations, limited by covariate completeness.
- Tunisia and South Africa excluded as structural outliers — findings may not generalize to North African or middle-income contexts.
- R² of 0.325 indicates substantial unexplained variance.
- Hospital beds/HDI effect directions preclude causal interpretation — best understood as markers of reporting completeness, not biological risk.

## References

- Chen, T., & Guestrin, C. (2016). XGBoost: A scalable tree boosting system. *KDD '16*. https://doi.org/10.1145/2939672.2939785
- Lundberg, S. M., & Lee, S. I. (2017). A unified approach to interpreting model predictions. *NeurIPS 30*.
- Msemburi, W., et al. (2023). The WHO estimates of excess mortality associated with the COVID-19 pandemic. *Nature, 613*, 130–137. https://doi.org/10.1038/s41586-022-05522-2
- Our World in Data. (2024). COVID-19 dataset. Global Change Data Lab. https://ourworldindata.org/covid-deaths

## Author

**Joy Dorcas Bisieri** —  Doctor & Data Scientist
joymanyara55@gmail.com

## License

Data used under CC BY 4.0 (Our World in Data). Code and analysis © 2026 Joy Dorcas Bisieri.
