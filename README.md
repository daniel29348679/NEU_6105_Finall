# Layoff Risk Classification — Project Guide

This repo follows the course guide (Extract → Learn → Predict) for a layoff-risk binary classifier.

## Environment
- Python 3.12+ recommended.
- Install deps locally (no virtual env needed): `python3 -m pip install -r requirements.txt`
- Data lives in `Data/`; notebooks in `Notebooks/`.

## Part 1 — Extract (EDA + Data)
- EDA notebook: `Notebooks/EDA.ipynb` — inspects `Data/tech_layoffs.csv`, shows missingness, layoff timeline, target balance, and industry risk rates. Run in Jupyter or headless:
```bash
python3 - <<'PY'
import nbformat
from nbclient import NotebookClient
nb = nbformat.read('Notebooks/EDA.ipynb', as_version=4)
client = NotebookClient(nb, kernel_name='python3', resources={'metadata': {'path': 'Notebooks'}})
client.execute()
nbformat.write(nb, 'Notebooks/EDA.ipynb')
print('EDA notebook executed')
PY
```
- ETL: `Data/data_cleaning_script.ipynb` builds `Data/cleaned_dataset.csv` (adds `target_high_risk`). Steps:
  1) Put raw CSV in `Data/tech_layoffs.csv` (optionally add `news_features.csv` / `finance_features.csv` keyed by company).
  2) Run the notebook to regenerate `cleaned_dataset.csv`.

## Part 2 — Learn (Modeling)
Notebook: `Notebooks/Modeling.ipynb`
- Loads `Data/cleaned_dataset.csv`.
- Preprocess: one-hot encode categoricals; train/test split with stratify.
- Models: Decision Tree (core), Random Forest (core). Metrics: accuracy, confusion matrix, F1/ROC for bonus.
- Error analysis: inspects FP/FN, notes older layoff cases become FN for the tree.
How to run:
```bash
python3 - <<'PY'
import os, nbformat
from nbclient import NotebookClient
nb = nbformat.read('Notebooks/Modeling.ipynb', as_version=4)
client = NotebookClient(nb, kernel_name='python3', resources={'metadata': {'path': 'Notebooks'}})
client.execute()
nbformat.write(nb, 'Notebooks/Modeling.ipynb')
print('done')
PY
```
(Or open in Jupyter/VS Code and Run All.)

## Part 3 — Predict (Demo)
Notebook: `Notebooks/Predict_Demo.ipynb`
- Trains the best model (Decision Tree) on full cleaned data, then scores `Data/prediction_sample.csv`.
- Output column: `pred_high_risk` (1 = high layoff risk).
- To score your own CSV: ensure columns match `cleaned_dataset.csv` minus `target_high_risk`, replace `Data/prediction_sample.csv`, rerun notebook.

## Paths & Outputs
- Data inputs/outputs: `Data/`
- Modeling/prediction notebooks: `Notebooks/Modeling.ipynb`, `Notebooks/Predict_Demo.ipynb`
- Summary slide: `summary/summary.pdf` (one-page report: Problem → Data → Model → Results → Limits → Next Steps)

## Notes
- No streamlit app included (bonus optional).
- Keep column names lowercase with underscores; target column is `target_high_risk`.

## Flow check
- Install deps with `python3 -m pip install -r requirements.txt` (no virtual env).
- Run `Notebooks/EDA.ipynb` to review raw data quality and visuals.
- Run `Data/data_cleaning_script.ipynb` to rebuild `cleaned_dataset.csv`.
- Train/evaluate via `Notebooks/Modeling.ipynb`.
- Score new CSVs with `Notebooks/Predict_Demo.ipynb`.
