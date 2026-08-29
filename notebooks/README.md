# Notebooks — execution guide

All three notebooks were run on Google Colab. Each one expects the parquet
dataset from `../data/` (see `data/README.md`) and installs `imbalanced-learn`
and `lime` on first run. Install the dependencies listed in
`../requirements.txt` (or `../environment.yml`) if running locally instead
of Colab.

## Execution order

Run in this order — later notebooks reuse models and features trained in
earlier ones:

1. **`PROCESO_MODELADO_CV_EN.ipynb`**
2. **`PROCESO_XAI_figures_EN.ipynb`** (requires 1 to have been run first)
3. **`PROCESO_ESTADISTICO_EN.ipynb`** (uses per-fold F1 values produced by 1)

Do not use "Run All" — several cells train models with 10-fold cross-validation
and take minutes each; run cells individually.

## What each notebook produces

### 1. `PROCESO_MODELADO_CV_EN.ipynb`
Stratified k=10 cross-validation with `RandomOverSampler` balancing applied
inside each fold (no data leakage). Trains Random Forest and Gradient
Boosting for:
- **Experiment A** — `MAIN_ACTIVITY` (21-class), 15 features
- **Experiment B2** — `SME_WIN` (binary), 16 features

Also recomputes the Decision Tree baseline for both experiments on the
leakage-free feature sets.

Outputs:
- Console summary tables (mean ± std per metric) — source for the paper's
  main results table
- Confusion matrix figures for fold 10 (RF and GB, Exp. A and B2)
- Feature importance tables
- `metricas_cv_k10.csv` — per-fold metrics for every model/experiment

### 2. `PROCESO_XAI_figures_EN.ipynb`
Retrains RF (and, for Exp. A, GB with the same hyperparameters as notebook 1)
on the fold-10 split, then runs LIME evaluation:

| Section | Produces | Paper element |
|---|---|---|
| Step 4c | `LIME_RF.png`, `LIME_GB.png` | Figures 11–12 |
| Step 5 | `LIME_B2_instancias.png` | Local explanations, Exp. B2 |
| Step 6 | `LIME_fidelidad.png` | LIME fidelity (R²) reported in the abstract |
| Step 7 | stability values (printed, not saved as CSV) | Stability discussion |
| Step 8 | `LIME_consistencia_geografica.png` | Geographic consistency figure |
| Step 9 | LaTeX table code | XAI summary table |

### 3. `PROCESO_ESTADISTICO_EN.ipynb`
Two things: the Wilcoxon signed-rank test (RF vs. GB, one-sided, k=10 paired
folds) behind the p-value quoted in the abstract, and an independent
Decision Tree baseline recomputation. Per-fold F1 values for RF/GB are
hard-coded from notebook 1's output rather than retrained here.

Outputs:
- Wilcoxon statistics and p-values (printed + LaTeX snippet)
- Decision Tree baseline metrics
- `resultados_estadisticos.csv` — per-fold F1 for RF/GB/DT, both experiments
