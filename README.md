Tenders
Code, notebooks and results for the article
> **Analysis of public tenders with rule-based systems to improve decision-making**
> Submitted to *Algorithms* (MDPI), manuscript algorithms-4553273.
The repository is supplementary material for the article. Its purpose is to let reviewers and readers check how each reported figure was obtained.
Summary
We evaluate supervised classification models on European Union public tenders published on TED (Tenders Electronic Daily), using Random Forest and Gradient Boosting together with LIME explanations and if-then rules derived from them.
Experiment A predicts the main activity of the contracting authority (`MAIN_ACTIVITY`, 21 classes). Random Forest reaches F1-macro = 0.3550 against a majority-class baseline of 0.0228; the score drops to 0.2203 on later years and 0.1443 on countries absent from training.
Experiment B predicts whether an SME wins the contract (`SME_WIN`, binary). Random Forest reaches F1-macro = 0.5863 (AUC = 0.7100) against a baseline of 0.4500, and 0.5515 with only the features known before bidding. That restricted model has no discriminative ability on unseen countries (AUC = 0.51).
Random Forest outperforms Gradient Boosting and a Decision Tree baseline in every fold of both experiments (one-sided Wilcoxon test, p = 0.001).
LIME fidelity is low (R² = 0.43 and 0.16). Evaluated on held-out data, a third of the Experiment B rules are no more precise than an empty rule. TreeSHAP agrees with the global feature ranking but not with the local LIME explanations.
Repository structure
```
tenders/
├── data/                 TED dataset used in all notebooks (parquet) and its description
├── notebooks/
│   ├── review_1/         modeling pipeline and analyses added in the first review round
│   └── review_2/         analyses added in the second review round
├── results/
│   └── review_2/         JSON outputs of the review_2 notebooks
├── images/               figures used in the article
├── requirements.txt      pip dependencies
└── environment.yml       conda environment
```
See `notebooks/README.md` for what each notebook does and how to run it, and `data/README.md` for the dataset.
Where each result comes from
Table numbers refer to the revised manuscript (second review round).
Manuscript element	Notebook
Table 9 (per-class results, Experiment A), majority-class baseline 0.0228	`review_1/PROCESO_MODELADO_CV_EN_perclass`
Table 10 (prospective-feature ablation, Experiment B)	`review_2/round2_reviewer1_recompute`
Table 12 (feature importance), confusion matrices	`review_1/PROCESO_MODELADO_CV_EN_perclass`
Tables 15 and 16 (model comparison, paired differences, Wilcoxon)	`review_2/round2_reviewer1_recompute` (Experiment B); `review_1/PROCESO_MODELADO_CV_EN_perclass` and `review_1/PROCESO_ESTADISTICO_EN` (Experiment A)
Tables 17 and 18 (temporal and country-based validation, fold-specific baselines)	`review_2/round2_reviewer1_recompute`; Experiment A per-fold values from `review_1/PROCESO_MODELADO_CV_EN_perclass`
Table 19 (per-country analysis, seven largest markets)	`review_2/expB_country_shift_test`
Table 20 (prospective model on later years and unseen countries)	`review_2/reviewer4_fast_analyses`
Tables 21 and 22 (aggregated metrics)	`review_1/PROCESO_MODELADO_CV_EN_perclass`; Experiment B macro-F1 from `review_1/expB_corrected_macro_f1`
Table 23 (LIME fidelity, stability, geographic consistency) and LIME figures	`review_1/PROCESO_XAI_figures_EN`
TreeSHAP cross-check (Spearman ρ = 0.846 and 0.947)	`review_1/PROCESO_MODELADO_CV_EN_perclass`, Step 7e
Table 24 (if-then rules evaluated as rules)	`review_2/reviewer4_fast_analyses`
Hyperparameter sensitivity (Section 3.7)	`review_2/reviewer4_hparam_sensitivity`
`WIN_COUNTRY_CODE` diagnostic (Section 3)	`review_1/win_country_code_diagnostic`
Running the notebooks
All notebooks were run on Google Colab with the dataset in `data/`. Each one starts with a setup cell that copies the parquet file from Google Drive; to run locally, point `LOCAL_PARQUET` (or `ruta_parquet`) to `data/part-00000-...snappy.parquet` and skip the Drive cell. Install the dependencies with
```bash
pip install -r requirements.txt
```
or `conda env create -f environment.yml`.
License
See `LICENSE`. The TED data are published by the European Commission under its open data policy.
