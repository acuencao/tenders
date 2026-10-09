# Notebooks

All notebooks were run on Google Colab and read the parquet file in [`../data/`](../data/README.md). Run cells one at a time rather than with *Run All*: several cells train models under 10-fold cross-validation and take from minutes to hours.

Common settings across notebooks:

- Stratified 10-fold cross-validation (`shuffle=True, random_state=42`), with `RandomOverSampler` applied only to the training part of each fold.
- Random Forest: `criterion='gini'` (Experiment A) or `'entropy'` (Experiment B), `max_features='sqrt'`, `max_samples=2/3`, `n_estimators=100`, `random_state=42`.
- Gradient Boosting: `max_features='sqrt'`, `learning_rate=0.1`, `n_estimators=100`, `random_state=42`, other parameters at scikit-learn defaults.
- Decision Tree baseline: `max_depth=5`, not tuned.
- Experiment A uses 15 predictors; Experiment B uses 16 (14 in the prospective model, without `NUMBER_OFFERS` and `NUMBER_AWARDS`).

## review_1 — modeling pipeline and first review round

| Notebook | What it does | Approx. runtime |
|---|---|---|
| `PROCESO_MODELADO_CV_EN_perclass.ipynb` | Main modeling pipeline: feature construction, 10-fold CV for both experiments, per-class results for Experiment A (Step 7c), temporal and country-based validation (Step 7d), TreeSHAP cross-check against Gini importance (Step 7e), Decision Tree baseline (Step 7b), confusion matrices and feature importance. | ~4 h on Colab (Gradient Boosting on Experiment A takes about 3 h) |
| `PROCESO_XAI_figures_EN.ipynb` | LIME explanations on fold 10: figures, local fidelity (R²), stability across 10 seeds, geographic consistency, XAI summary table. | ~25 min |
| `PROCESO_ESTADISTICO_EN.ipynb` | One-sided Wilcoxon signed-rank tests over the 10 paired folds and Decision Tree baseline for Experiment A. | ~15 min |
| `expB_corrected_macro_f1.ipynb` | Reruns Experiment B with `average='macro'` (the first version reported positive-class F1 under the macro-F1 label) and confirms the hyperparameters selected by the original search. | ~10 min |
| `expB2_country_validation_standalone.ipynb` | Country-based `GroupKFold` (k = 5) for Experiment B, run on its own so that it fits in one Colab session. | a few minutes |
| `win_country_code_diagnostic.ipynb` | Compares `WIN_COUNTRY_CODE` with `ISO_COUNTRY_CODE` (multi-lot values, share of domestic winners). | < 1 min |

Suggested order: `PROCESO_MODELADO_CV_EN_perclass` first, then the others in any order. Each notebook rebuilds its own features from the parquet file, so none depends on objects created by another; a few copy per-fold values from `PROCESO_MODELADO_CV_EN_perclass` and say so in their text.

## review_2 — second review round

These notebooks are stored without cell outputs. Their full results are in [`../results/review_2/`](../results/review_2/), one JSON file per notebook.

| Notebook | What it does | Results file | Approx. runtime |
|---|---|---|---|
| `round2_reviewer1_recompute.ipynb` | Experiment B under macro-F1 for the prospective-feature ablation, temporal and country-based validation; fold-specific majority-class baselines for both experiments; Decision Tree baseline, paired differences and Wilcoxon tests recomputed on macro-F1. | `round2_results.json` | ~10 min |
| `expB_country_shift_test.ipynb` | Experiment B in each of the seven largest national markets: trained within the country, pooled with the country included, and pooled with the country excluded. | `country_shift_results.json` | ~12 min |
| `reviewer4_fast_analyses.ipynb` | Prospective (14-feature) model under temporal, country-based and per-country validation; if-then rules from `convert_to_if_then()` evaluated as rules on held-out data (coverage, precision, base rate). An optional Anchors run failed to install in Colab and is not used in the paper. | `reviewer4_fast_results.json` | ~15 min |
| `reviewer4_hparam_sensitivity.ipynb` | Shows that `sqrt` and `log2` give the same `max_features` for 15 and 16 predictors, evaluates both Random Forest criteria under the same folds, and checks that the worse one still beats Gradient Boosting in every fold. | `reviewer4_hparam_results.json` | ~30 min |
