# Notebooks

This folder contains the six Colab notebooks that implement the full pipeline
for the paper "Calibration Collapse and Recovery Dynamics in ICU Mortality
Models Under Dataset Shift".

All notebooks are designed to run on Google Colab with Google Drive mounted
at `/content/drive/MyDrive/Plan A/`. BigQuery access to PhysioNet datasets
is required for notebooks 00 and 10.

---

## Execution Order

Run notebooks in this exact order:

| Order | Notebook | Input | Output |
|---|---|---|---|
| 1 | 00_mimiciv_data_extraction | BigQuery (MIMIC-IV) | Raw CSVs saved to Drive |
| 2 | 01_feature_merging | Raw CSVs from 00 | Merged MIMIC-IV dataset |
| 3 | 02_preprocessing | Merged dataset from 01 | mimiciv_processed.csv |
| 4 | 03_model_training | mimiciv_processed.csv | Trained models + internal metrics |
| 5 | 10_eicu_data_extraction | BigQuery (eICU) | eICU CSVs saved to Drive |
| 6 | 11_cross_dataset_evaluation | MIMIC CSVs + eICU CSVs | All results, figures, SHAP |

---

## Notebook Descriptions

### 00_mimiciv_data_extraction.ipynb
Builds the MIMIC-IV cohort from BigQuery. Extracts vitals (heart rate, MAP,
respiratory rate, SpO2), laboratory values (creatinine, lactate, WBC,
haemoglobin, platelets, sodium, potassium, BUN), and interventions
(invasive ventilation, vasopressors) over the first 24 ICU hours.
Applies physiological range filters and saves separate CSVs per feature group.

- Cohort: 65,366 adult ICU stays, first admission per patient
- Mortality: hospital_expire_flag from admissions table
- Output files: mimiciv_cohort.csv, mimiciv_vitals_24h.csv,
  mimiciv_labs_core_24h.csv, mimiciv_labs_k_bun_24h.csv,
  mimiciv_lactate_24h.csv, mimiciv_creatinine_24h.csv,
  mimiciv_interventions_24h.csv

---

### 01_feature_merging.ipynb
Merges all MIMIC-IV extracted CSVs into a single analysis dataset on
subject_id, hadm_id, and stay_id. Resolves duplicate columns from
merges. Verifies row count integrity — no row explosion.

- Input: all CSVs from notebook 00
- Output: mimiciv_final_dataset_clean.csv (65,366 × ~30 columns)

---

### 02_preprocessing.ipynb
Applies median imputation using training-set statistics. Encodes gender
as binary (M=1, F=0). Adds binary missingness indicator columns for
every numeric feature. Drops temperature (215 non-missing rows out of
65,366 — near-complete missingness). Drops SBP and DBP from cross-dataset
feature set due to incompatible encoding in eICU.

- Output: mimiciv_processed.csv (65,366 × 189 features, zero missing values)

---

### 03_model_training.ipynb
Trains four models on 70% of the MIMIC-IV processed dataset (patient-level
split on subject_id to prevent leakage, stratified by mortality).
Evaluates on held-out 30%. Computes AUROC, AUPRC, Brier score, ECE,
and utility metrics with bootstrap CIs.

- Models: Logistic Regression, Random Forest, XGBoost, LightGBM
- Best model: LightGBM AUROC 0.870 (95% CI: 0.862–0.878), ECE 0.009
- Output: mimiciv_internal_metrics.csv

---

### 10_eicu_data_extraction.ipynb
Builds the eICU cohort from BigQuery. Extracts heart rate, vitals, and
laboratory values over the first 24 ICU hours using eICU nursing chart
and lab tables. Handles eICU-specific encoding including age stored as
string with ">89" values and BP fields that do not separate systolic
and diastolic reliably.

- Cohort: 200,234 adult ICU stays
- Mortality: derived from hospitaldischargestatus
- Output files: eicu_cohort.csv, eicu_hr_24h.csv,
  eicu_vitals_24h.csv, eicu_labs_24h.csv

---

### 11_cross_dataset_evaluation.ipynb
The main evaluation notebook. Aligns MIMIC-IV and eICU feature spaces
by taking the intersection of numeric columns and dropping SBP/DBP.
Imputes eICU missing values using MIMIC-IV training medians. Retrains
models on aligned MIMIC-IV features and evaluates directly on full eICU.

Experiments performed:
- External validation with bootstrap CIs (1,000 iterations)
- Bootstrap permutation test for AUROC gap significance
- Platt scaling and isotonic recalibration on independent 50/50 eICU split
- SHAP TreeExplainer on 2,000-patient samples from each dataset
- Age-stratified subgroup analysis (four bands)
- Gender equity audit
- All nine paper figures generated

- Best external result: LightGBM AUROC 0.816, ECE 0.083 (raw),
  ECE 0.004 after isotonic recalibration

---

## Notes

- Runtime disconnects in Colab will lose all variables. If this happens,
  rerun cells in order: 0,1,2,3,4,5,6,7,8,9,10,12,14,18,19,20,25
  then proceed with experiment cells.
- All file paths reference `/content/drive/MyDrive/Plan A/` — update
  this if your Drive structure differs.
- BigQuery project used: mimic-rs — replace with your own project ID.
