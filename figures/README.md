# Figures

This folder contains all nine figures from the paper "Calibration Collapse
and Recovery Dynamics in ICU Mortality Models Under Dataset Shift".

All figures were generated in notebook 11_cross_dataset_evaluation.ipynb
using matplotlib and seaborn. Resolution: 300 DPI.

---

## Figure List

| File | Figure | Description |
|---|---|---|
| figure01_cohort.png | Figure 1 | Cohort comparison — patient count and mortality rate for MIMIC-IV vs eICU |
| figure02_shift.png | Figure 2 | Feature distribution shift — boxplots for shared features across datasets |
| figure03_auroc.png | Figure 3 | AUROC comparison — all four models on MIMIC-IV internal vs eICU external |
| figure04_auprc.png | Figure 4 | AUPRC comparison — all four models on MIMIC-IV internal vs eICU external |
| figure05_calibration_raw.png | Figure 5 | Calibration curves — LightGBM raw model on MIMIC-IV vs eICU with perfect calibration diagonal |
| figure06_calibration_fix.png | Figure 6 | Recalibration comparison — Raw vs Platt vs Isotonic on eICU evaluation split |
| figure07_pr.png | Figure 7 | Precision-recall curves — LightGBM on MIMIC-IV vs eICU |
| figure08_distribution.png | Figure 8 | Risk score distribution — KDE of raw MIMIC, raw eICU, and calibrated eICU probabilities |
| figure09_shap_shift.png | Figure 9 | SHAP feature importance shift — paired bars for top 15 features on MIMIC-IV vs eICU |

---

## Key Observations

**Figure 1**: eICU is 3× larger than MIMIC-IV. Mortality rates are similar
(10.84% vs 9.06%), confirming comparable outcome prevalence.

**Figure 2**: MAP shows the largest distribution shift (79.18 vs 89.15 mmHg),
reflecting different hemodynamic management protocols across institutions.

**Figures 3 and 4**: Discrimination drops are modest. LightGBM AUROC 0.870 → 0.816.
Logistic Regression shows no drop, consistent with its simpler decision boundary.

**Figure 5**: The eICU calibration curve deviates substantially from the diagonal
while the MIMIC-IV curve tracks it closely — visual evidence of calibration collapse.

**Figure 6**: Both Platt and isotonic recalibration bring the curve back to the
diagonal. Isotonic achieves near-perfect calibration (ECE 0.004).

**Figure 7**: Precision-recall gap confirms reduced positive case identification
precision on eICU even when ranking is partially preserved.

**Figure 8**: eICU raw probabilities compress toward zero — characteristic
overconfidence in the negative class under prevalence shift. Recalibration
redistributes mass to match the 9.06% eICU base rate.

**Figure 9**: Age and creatinine dominate both datasets. WBC mean and
heart rate max show the largest importance increase in eICU (+0.064 and
+0.041 respectively). Sodium importance decreases in eICU.
