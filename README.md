# ml-natural-stone-impact-resistance

Reproducible code and data for the paper:

> **Impact resistance prediction of carbonate natural stones through a physics-informed hybrid machine learning framework**
> Erkan Özkan, Gencay Sarıışık, Ece Kundak (2026)
> *Submitted to Construction and Building Materials.*

A Sigmoid-Blended Physics-Informed Hybrid (SBPIH) model with Dual Compensatory Prediction Principle (CPP) channels for predicting the impact resistance of carbonate dimension stones according to BS EN 14158.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)

---

## Overview

The model predicts the impact resistance (IR, kPa) of natural stone plates from four input features: rebound hardness (`RC`), Knoop hardness (`KH`), thickness (`Size`), and a SHAP-weighted compressive index (`SMI_mul`). It combines an Extra Trees regressor with two physics-informed compensatory channels — one driven by Impact Strength (IS) and one by Fe₂O₃ content — blended through dual sigmoid functions.

**Key results** (Leave-One-Group-Out cross-validation, 17 folds, n = 51):

| Metric | Value |
|---|---|
| R² | 0.9661 |
| MAPE | 7.14% |
| MAE | 0.670 kPa |
| RMSE | 0.941 kPa |

---

## Repository contents

```
ml-natural-stone-impact-resistance/
├── pap_17stone_resimp_dual_ML.ipynb   # Main analysis notebook
├── res_imp_51obs_v4.xlsx              # Input dataset (51 observations × 26 variables)
├── requirements.txt                   # Python dependencies
├── LICENSE                            # MIT License
└── README.md                          # This file
```

---

## Dataset

The dataset contains 51 observations (17 stone types × 3 thickness levels) compiled from previously published experimental data by Sarıışık et al. (2017). Each observation includes 26 variables:

- **Identifiers:** Type, StoneName, Lithology, Size, t (thickness, m), Height (drop height, m), n (drops to failure)
- **Mechanical:** RE, RC, RC², v₀RC, RE·RC², CS (compressive strength), BS (bending strength), KH (Knoop hardness)
- **Physical:** Wt (plate weight), Density, Por (porosity), IS (impact strength)
- **Composite:** FS, IS_CS, SMI_mul (SHAP-weighted index)
- **Petrochemical:** LoI, K₂O, MgO, Fe₂O₃
- **Target:** Ft_IR (impact resistance, kPa)

> **Note on raw data provenance:** The raw experimental measurements were originally generated and reported in Sarıışık, G., Sarıışık, A., & Şentürk, A. (2016/2017). Cite the original source for the underlying experimental campaign.

---

## How to run

### Option 1 — Google Colab (recommended)

The notebook was developed in Colab and uses Google Drive for I/O.

1. Open the notebook in Colab: [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/erkanozka/ml-natural-stone-impact-resistance/blob/main/pap_17stone_resimp_dual_ML.ipynb)
2. Place `res_imp_51obs_v4.xlsx` in `MyDrive/Armoren/pap_res_imp/data/` (or update the `BASE_DIR` variable in Cell 1 to your path).
3. Run all cells (Runtime → Run all). The notebook will mount your Drive, install dependencies, and write outputs to `MyDrive/Armoren/pap_res_imp/outputs/`.

### Option 2 — Local Jupyter

Drive mount is Colab-specific. To run locally, edit Cell 1:

```python
# Replace these lines:
# from google.colab import drive
# drive.mount('/drive')
# BASE_DIR = '/drive/MyDrive/Armoren/pap_res_imp'

# With:
BASE_DIR = '.'                   # or wherever you cloned the repo
```

Then:

```bash
git clone https://github.com/erkanozka/ml-natural-stone-impact-resistance.git
cd ml-natural-stone-impact-resistance
python -m venv .venv && source .venv/bin/activate    # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook pap_17stone_resimp_dual_ML.ipynb
```

Place the dataset file in the working directory (alongside the notebook) and create `data/` and `outputs/` subfolders, or adjust `DATA_DIR` and `OUTPUT_DIR` accordingly.

---

## Reproducibility

- All randomness is seeded (`SEED = 42`).
- Cross-validation strategy is Leave-One-Group-Out (group = stone type), 17 folds.
- Bootstrap confidence intervals use 2000 stratified resamples.
- The notebook regenerates two output Excel files containing all tables and figure-source data used in the manuscript.

Tested on Python 3.10+ with the package versions in `requirements.txt`. Boruta is pinned to 0.4.3 to match the version used in the manuscript analysis.

---

## Citation

If you use this code or dataset, please cite:

```bibtex
@article{ozkan2026impact,
  title   = {Impact resistance prediction of carbonate natural stones through
             a physics-informed hybrid machine learning framework},
  author  = {{\"O}zkan, Erkan and Sar{\i}{\i}{\c{s}}{\i}k, Gencay and Kundak, Ece},
  journal = {Construction and Building Materials},
  year    = {2026},
  note    = {Submitted}
}
```

A Zenodo archive of this repository is available at: **DOI: 10.5281/zenodo.XXXXXXX** *(to be filled in once Zenodo deposit is created)*

---

## Authors

- **Erkan Özkan** — Afyon Kocatepe University ([ORCID 0000-0002-8089-2920](https://orcid.org/0000-0002-8089-2920)) — *Corresponding author* — erkanozka@gmail.com
- **Gencay Sarıışık** — Harran University ([ORCID 0000-0002-1112-3933](https://orcid.org/0000-0002-1112-3933))
- **Ece Kundak** — Eskişehir Osmangazi University ([ORCID 0000-0001-5491-7058](https://orcid.org/0000-0001-5491-7058))

---

## License

This software is released under the [MIT License](LICENSE). The dataset is provided under the same terms; the underlying raw experimental measurements are subject to the citation of Sarıışık et al. (2017).

---

## Acknowledgements

This study was supported by Afyon Kocatepe University Scientific Research Committee (Project No. 11.ISCMYO.01) and TÜBİTAK-1002 (Project No. 111M390).
