# Calibration Collapse and Recovery Dynamics in ICU Mortality Models Under Dataset Shift

This repository contains the code and figures for the paper:

**"Calibration Collapse and Recovery Dynamics in ICU Mortality Models Under Dataset Shift"**
Aman Chandra H, RV University, Bengaluru, India
*Submitted to IEEE Access, 2025*

---

## Overview

This study trains four machine learning models on MIMIC-IV (N = 65,366 ICU stays)
and externally validates them on eICU (N = 200,234 ICU stays). The central finding
is that calibration degrades far more severely than discrimination under
cross-institutional dataset shift, and that simple post-hoc recalibration
fully restores probability reliability without retraining.

**Key results:**
- LightGBM internal AUROC: 0.870 → external AUROC: 0.816 (modest drop)
- LightGBM internal ECE: 0.009 → external ECE: 0.083 (9× collapse)
- Isotonic recalibration restores ECE to 0.004 with AUROC unchanged
- Age subgroup AUROC declines monotonically from 0.847 (<50) to 0.759 (>80)
- No gender performance disparity observed

---

## Repository Structure
```
notebooks/   — Six Colab notebooks covering full pipeline
figures/     — All nine paper figures
requirements.txt — Python dependencies
```

---

## Notebooks

| Notebook | Description |
|---|---|
| 00_mimiciv_data_extraction | Extract cohort, vitals, labs, interventions from MIMIC-IV via BigQuery |
| 01_feature_merging | Merge all MIMIC-IV extracted CSVs into single analysis dataset |
| 02_preprocessing | Imputation, encoding, missingness indicators, final feature matrix |
| 03_model_training | Train LR, RF, XGBoost, LightGBM on MIMIC-IV, internal validation |
| 10_eicu_data_extraction | Extract cohort, vitals, labs from eICU via BigQuery |
| 11_cross_dataset_evaluation | Feature alignment, external validation, calibration, recalibration, SHAP, subgroups |

---

## Data Access

Both datasets used in this study are publicly available but require credentialed access:

- **MIMIC-IV**: https://physionet.org/content/mimiciv/3.1/
- **eICU-CRD**: https://physionet.org/content/eicu-crd/2.0/

Access requires completing the CITI Data or Specimens Only Research training
and signing the PhysioNet data use agreement. Data cannot be redistributed.

---

## Requirements

- Python 3.8+
- Google Colab (notebooks are designed to run on Colab with Google Drive mounted)
- BigQuery access to PhysioNet datasets

Install dependencies:
```bash
pip install -r requirements.txt
```

---

## Results Summary

All numerical results are reported in the paper. Key tables:

- **Table 2**: Internal validation on MIMIC-IV (all 4 models, bootstrapped CIs)
- **Table 3**: External validation on eICU (all 4 models, bootstrapped CIs)
- **Table 4**: Recalibration results — Raw vs Platt vs Isotonic on eICU
- **Table 5**: Age-stratified subgroup analysis on eICU
- **Table 6**: SHAP feature importance comparison MIMIC-IV vs eICU

---

## Citation

If you use this code, please cite:
```
@article{chandrah2025calibration,
  title={Calibration Collapse and Recovery Dynamics in ICU Mortality Models Under Dataset Shift},
  author={Chandra H, Aman},
  journal={IEEE Access},
  year={2025},
  note={Under review}
}
```

---

## License

Code is released under the MIT License.
Data is subject to PhysioNet data use agreements and cannot be redistributed.
