# Layoff Risk Data (Data/README.md)

Goal: binary classification of companies into `high_risk` vs `low_risk` layoffs.

Current files:
- `Data/tech_layoffs.csv` — raw dataset you added (Kaggle tech layoffs).
- `Data/cleaned_dataset.csv` — output from ETL for modeling (with `target_high_risk`).
- `Data/data_cleaning_script.ipynb` — ETL/feature engineering pipeline tailored to `tech_layoffs.csv`.
- Optional: `Data/news_features.csv` (news counts/sentiment keyed by company), `Data/finance_features.csv` (market stats keyed by company).

Note: Prediction template is no longer stored; when you need to predict, take `cleaned_dataset.csv` columns and drop `target_high_risk` to form your input CSV.

Label definition (default):
- High risk = `layoff_ratio >= 0.05`, where `layoff_ratio = layoffs_12m / total_employees_est`; falls back to latest `impacted_pct_recent` if missing.
- You can switch to a recent-activity rule in the notebook by setting `LABEL_RULE = 'recent'` (any layoff in past 12 months = 1).

Core features (from notebook):
- Counts: `layoffs_12m`, `layoffs_last90d`, `days_since_last_layoff`.
- Scale: `total_employees_est` (layoffs ÷ impact_pct), `layoff_ratio`, `impacted_pct_recent` (latest reported pct).
- Categorical: `industry`, `headquarter_location`, `status` (lowercased strings).
- Optional merges: `news_count_30d`, `news_sentiment_30d`, `stock_volatility_30d`, `revenue_growth_qoq`, etc., if you add them via optional CSVs.

Cleaning + feature pipeline (in `data_cleaning_script.ipynb`):
1) Load `tech_layoffs.csv`; normalize text (company/industry/status), parse dates; keep recent 3 years.
2) Parse numerics: `total_layoffs` → `layoffs` (int), `impacted_workforce_percentage` → `impact_pct` (0–1). Estimate `total_employees_est = layoffs / impact_pct` with medians as fallback.
3) Rolling aggregation by company: compute `layoffs_12m`, `layoffs_last90d`, `days_since_last_layoff`, take latest snapshot per company.
4) Compute `layoff_ratio`, keep `impacted_pct_recent`; fill missing values.
5) Create `target_high_risk` using `RATIO_THRESHOLD` (default 5%).
6) Export `cleaned_dataset.csv` (with target). For predictions later, drop `target_high_risk` to create an input CSV.

How to rebuild dataset:
- Put the downloaded Kaggle file as `Data/tech_layoffs.csv`.
- (Optional) Add `news_features.csv` and/or `finance_features.csv` keyed by `company`; the notebook merges them automatically if present.
- Open `Data/data_cleaning_script.ipynb` and run all cells. The CSVs will be refreshed with the latest outputs.

Notes:
- Columns stay lowercase with underscores; new columns end with `_est`, `_ratio`, `_pct` for clarity.
- Adjust `RATIO_THRESHOLD` if class balance is too skewed; inspect the printed counts in the notebook.
- For inference, ensure your input CSV matches `cleaned_dataset.csv` columns minus `target_high_risk` and uses the same preprocessing choices.
