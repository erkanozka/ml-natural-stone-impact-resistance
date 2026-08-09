# PHy-CPP — physics-informed hybrid ML for impact resistance of carbonate natural building stones

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19788669.svg)](https://doi.org/10.5281/zenodo.19788669)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.12](https://img.shields.io/badge/python-3.12-blue.svg)](https://www.python.org/)
[![Version 2.0.1](https://img.shields.io/badge/version-2.0.2-informational.svg)](#)

Reproducible code and data for **PHy-CPP** (Physics-informed Hybrid with a Dual Gauss-weighted
Compensatory Prediction Principle), a model that predicts the **impact resistance (IR, BS EN 14158)**
of carbonate natural building stones without data leakage.

Associated with a peer-reviewed research article by **Özkan, Sarıışık & Kundak**.

- **Zenodo DOI:** https://doi.org/10.5281/zenodo.19788669
- **Raw experimental data** originally reported by Sarıışık et al. (2016).

---

## Contents

```
pap_17stone_resimp_dual_ML.ipynb   Single, top-to-bottom reproducible pipeline (the code)
res_imp_51obs_v4.xlsx              Dataset — 51 observations, 17 stone types x 3 thicknesses
requirements.txt                   Pinned environment (Python 3.12, scikit-learn 1.6.1)
README.md · LICENSE · CITATION.cff · .zenodo.json · .gitignore
outputs/                           Pre-computed canonical results (so reviewers need not re-run)
  res_imp_tables_final.xlsx          Tables 1-8 (+ VIF, Boruta)
  res_imp_nested_final.xlsx          HEADLINE: nested LOGO tables + robustness (k/seed/HPO/LC)
  res_imp_optB_perfoldSMI_final.xlsx Option B (per-fold, train-only SMI_mul)
  res_imp_reference_tables_final.xlsx Reference tables (Path-A)
  res_imp_figdata_final.xlsx         Figure source data (fed to the plotting scripts)
  res_imp_acq_benchmark_curves.csv   Supplementary Fig. S2 curve (acquisition benchmark)
  res_imp_identifiability.csv        Supplementary Table S6 (NN dist, local IR spread; corr 0.66 / 0.62)
```

The notebook produces **no figures** by design — only analysis tables and figure-data files — so a
plotting/backend problem can never break the analysis run. Figure-drawing scripts are archived
separately; they read the Excel files in `outputs/`.

## How to run

Local:

```bash
pip install -r requirements.txt
jupyter notebook pap_17stone_resimp_dual_ML.ipynb   # Kernel -> Restart & Run All
```

Google Colab: open the notebook; Part 0 mounts Drive automatically if a
`MyDrive/pap_res_imp_final` folder is present, otherwise it falls back to the repository root
(the dataset `res_imp_51obs_v4.xlsx` next to the notebook). Set `PAP_BASE_DIR` to override.

Re-running writes fresh copies of every file in `outputs/`. The pre-computed files shipped here are
the canonical results the manuscript reports; a clean re-run reproduces them at the reported precision.

## Pipeline structure

- **Part 0** — Environment (Colab *or* local) and dependencies.
- **Part 1** — Data, feature engineering, feature selection, statistics (Tables 1-4, VIF, Boruta).
  `FEATS` is *selected from data*, never hardcoded.
- **Part 2** — *Reported* reference pipeline (feature-selection-global). Intentionally optimistic;
  kept **only** to quantify the optimism against Part 3.
- **Part 3** — **Nested** pipeline (feature selection re-run inside every outer fold). This is the
  **leakage-free headline** the manuscript reports.
- **Part 4** — Option B (per-fold, train-only `SMI_mul`) + robustness (k / seed / HPO / learning curve).
- **Part 5** — Figure-data **contract**: every figure/table source sheet + columns is asserted to exist.
- **Part 6** — Automated **verification** asserts (headline reproduced at reported precision).
- **Appendix A** — Acquisition benchmark for Supplementary Fig. S2 (stochastic; archived curve is canonical).
- **Appendix B** — Local-identifiability diagnostic reproducing Supplementary Table S6 (writes `outputs/res_imp_identifiability.csv`).

## Headline result (leakage-free, nested LOGO 17-fold CV, n = 51)

| Model                          | R²     | MAPE (%) | MAE (kPa) | RMSE (kPa) |
|--------------------------------|--------|----------|-----------|------------|
| Pure Extra Trees               | 0.9045 | 10.86    | 1.083     | 1.580      |
| + IS-gated sigmoid             | 0.9536 | 8.99     | 0.828     | 1.102      |
| + single CPP (Fe₂O₃, RC)       | 0.9602 | 8.35     | 0.781     | 1.020      |
| **+ dual CPP (Fe₂O₃+RC, LoI)** | **0.9623** | **7.83** | **0.717** | **0.993** |

Bootstrap mean R² = 0.9615, 95% CI [0.948, 0.972]; Wilcoxon bias p = 0.41 (no systematic bias).
Option B reproduces every value above **exactly**, confirming the per-fold `SMI_mul` normalisation is
inert (monotone rescaling ⇒ identical Extra-Trees splits).

## Model, in one line

Extra Trees baseline on `SMI_mul, Size, RC, KH`
→ IS-gated sigmoid blend (k = 5) for the extrapolation regime
→ dual Gauss-gated compensatory correction (Channel A: Fe₂O₃+RC; Channel B: LoI).
Validation: Leave-One-Group-Out 17-fold CV, bootstrap 95% CI, Wilcoxon, permutation test.
`SMI_mul = minmax(KH) × minmax(CS) × minmax(BS)`.

## Runtime and determinism

Parts 1-2 and 4 run in a few minutes. The two nested-selection cells in Part 3 (forward, backward)
each take roughly 25-40 min on a single CPU core (17 independent inner-LOGO feature searches);
per-fold progress is printed. All estimators use `random_state = 42`; bootstraps and permutation tests
re-seed before each block, so a re-run reproduces every reported digit. The Appendix A acquisition
benchmark is stochastic by nature — the archived curve in `outputs/` is the canonical Fig. S2 data.

## Data dictionary (key columns of `res_imp_51obs_v4.xlsx`)

`Type` stone-type id (1-17) · `Ft_IR` impact resistance IR (kPa, target) · `RC` restitution
coefficient · `KH` micro-hardness · `CS` compressive strength · `BS` flexural strength ·
`IS` impact strength · `Density`, `Por` porosity · `Size` plate-thickness code (1=20, 2=30, 3=40 mm) ·
`Fe2O3`, `LoI`, `K2O`, `MgO` chemistry. Variables algebraically embedded in IR
(`Wt, Height, RE, n, FS, RC2, v0RC, RE_RC2`) are excluded as leakage and never enter the model.

## Citation

See `CITATION.cff`. Please cite both the associated article and this archived repository
(DOI [10.5281/zenodo.19788669](https://doi.org/10.5281/zenodo.19788669)).

## License

Released under the **MIT License** (see `LICENSE`).
