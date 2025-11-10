# India Energy Demand Forecasting

Forecasting India’s national aggregate power demand (NA_MW) with gradient boosting models.  
Historical hourly load is pulled from the `data_india_ENRG*.xlsx` workbooks, cleaned, featurized, split into train/validation/test windows, and modeled with tuned XGBoost, LightGBM, CatBoost, plus a weighted ensemble.

## Repository Tour

| Path | Description |
| --- | --- |
| `Project_replica.ipynb` | Primary notebook: data cleaning, feature engineering, Optuna tuning, evaluation, plotting, and artifact export. |
| `TSAIP.ipynb`, `TSAIP-2.ipynb` | Earlier exploratory notebooks (feature tries, model baselines). |
| `data_india_ENRG*.xlsx`, `actual_jan1_2025.xlsx` | Input datasets (hourly load). Keep only if redistribution is permitted. |
| `best_xgBoost.pkl`, `best_LGBM_model.pkl`, `best_CAT_model.pkl` | Serialized fitted models (joblib). |
| `CAT_comparison_predictions.xlsx`, `LGB_comparison_predictions.xlsx`, `XGB_comparison_predictions.xlsx` | Model prediction exports for reporting. |
| `fig*`, `heatmap_*`, `shap_*`, `worst_day_*`, `top_error_context_*` | Publication-ready figures emitted from the notebook. |
| `Project_replica_text.txt` | Text export of `Project_replica.ipynb` (handy for quick diffs/reviews). |

> **Note:** The repo also contains PDF/DOCX drafts of the accompanying report (`Report_time_series.pdf`, `dp_VINAY.docx`, etc.). Remove private material before pushing to a public remote.

## Environment & Installation

```bash
python -m venv .venv
source .venv/bin/activate        # On Windows: .venv\Scripts\activate
pip install --upgrade pip
pip install pandas numpy matplotlib seaborn scikit-learn \
            optuna xgboost lightgbm catboost joblib
```

Optional (for SHAP/plot styling used in TSAIP notebooks):

```bash
pip install shap seaborn==0.13.2
```

## Reproducing the Analysis

1. **Place data**  
   Ensure at least one of the `data_india_ENRG*.xlsx` files is in the repo root. The notebook currently reads `data_india_ENRG-3.xlsx` (cleaned hourly load).

2. **Open `Project_replica.ipynb`**  
   Run cells sequentially. Major stages:
   - Excel preprocessing (`Cell 1–18`).
   - Train/val/test split + feature generation (`Cell 19–27`).
   - Optuna tuning for XGBoost/LightGBM/CatBoost (`Cell 27`, `36`, `40` blocks).
   - Final model training + evaluation (`Cell 28–33`, `37–43`).
   - Ensemble search + diagnostics (`Cell 45+`).

3. **Artifacts**  
   Running the notebook regenerates:
   - `best_*.pkl` model weights.
   - Comparison spreadsheets (`*_comparison_predictions.xlsx`).
   - Figures saved under `fig*.png`, `heatmap_*.png`, `shap_*.png`, etc.
   - `ensemble_weights.npy` with Optuna-derived blend weights.

## Key Test Results (Jan–Feb 2024 holdout)

| Metric | Naïve Persistence | XGBoost (tuned) | LightGBM (tuned) | CatBoost (tuned) |
| --- | ---:| ---:| ---:| ---:|
| RMSE (MW) | 6 237.21 | 3 864.18* | 3 517.18 | **2 787.72** |
| MAE (MW)  | 4 892.38 | 2 886.86* | 2 686.24 | **2 034.46** |
| MAPE (%)  | 2.69 | 1.57* | 1.48 | **1.07** |
| R²        | 0.8964 | 0.9602* | 0.9670 | **0.9793** |

`*` Markdown tables in the notebook still show an older XGBoost run (3 719.11 / 2 782.76 / 1.51 / 0.9632); rerunning the notebook updates the printed metrics and plots.

## Notes Before Publishing

- **Data Rights:** Verify that the Excel data (and any DOCX/PDF reports) may be shared publicly. Remove anything proprietary.  
- **Large binaries:** Model pickles and PDFs can bloat the repo; consider Git LFS if they must stay.  
- **Secrets:** The repository contains no keys/tokens, but double-check notebooks before pushing.

With the README in place and sensitive assets accounted for, the project is ready to be pushed to GitHub.
