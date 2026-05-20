# Nonlinear Length of Stay and In-Hospital Mortality in Alzheimer’s Disease Hospitalizations

**Admission-Only vs Inpatient-Course Prediction Using Explainable Machine Learning (NRD 2017)**

## Overview

This repository contains code and analysis notebooks for a study of **in-hospital mortality** among older adults hospitalized with **Alzheimer’s disease (AD)** using the **HCUP Nationwide Readmissions Database (NRD) 2017**. The project focuses on:

* The **nonlinear (U-shaped)** relationship between **length of stay (LOS)** and in-hospital mortality
* A prespecified **two-model framework**:

  * **Model A (Admission-only):** excludes inpatient-course proxies (LOS, procedure count, total charges)
  * **Model B (Inpatient-course):** includes inpatient-course variables (LOS, procedures, charges)
* **Explainable machine learning** using **XGBoost + SHAP**, evaluated with **5-fold cross-validation** and feature stability summaries

**Important:** This repository does **not** include HCUP/NRD data. You must obtain NRD through HCUP and comply with the NRD Data Use Agreement (DUA).

---

## Key Outputs

Main text figures/tables typically produced by this analysis:

* **Figure 1:** Unadjusted mortality (%) by LOS categories with 95% CIs
* **Figure 2:** ROC curves comparing Model A vs Model B (5-fold CV AUROC ± SD)
* **Figure 3A/3B:** SHAP summary (beeswarm) plots for Model A and Model B (publishable labels)
* **Figure 4:** Adjusted spline-predicted mortality vs LOS with sensitivity exclusions (exclude LOS ≤1 and ≤2 days)
* **Table 1:** Baseline characteristics by survival status
* **Table 2:** Multivariable regression (Model A vs Model B)
* **Supplementary Table S1:** Unadjusted LOS-bin mortality table
* **Supplementary Table S2:** Clinical comorbidity patterns (short LOS ≤5 vs prolonged LOS ≥25), excluding Z-codes

---

## Repository Structure (Suggested)

You can organize files like this:

* `notebooks/`

  * `NRD_2017_analysis.ipynb` (cohort creation, LOS bins, descriptives)
  * `modelA_modelB_shap.ipynb` (Model A/B training, CV AUROC, SHAP plots)
  * `figures_tables.ipynb` (export final figures/tables)
* `scripts/` (optional)

  * `preprocess.py`, `modeling.py`, `shap_utils.py`
* `outputs/`

  * `figures/`, `tables/`
* `docs/`

  * `figure_legends.md`, `supplementary_methods.md`

---

## Data Access and Preparation

1. Obtain **NRD 2017** through HCUP and follow HCUP instructions for loading the data.
2. Create an **analytic dataset** that includes:

   * AD cohort definition used in the study (e.g., ICD-10-CM criteria applied across diagnosis fields)
   * Outcome: in-hospital mortality (e.g., `DIED`/`died`)
   * LOS and course variables as needed (e.g., `LOS`, procedure count, total charges)
3. Keep all patient-level data private and compliant with the NRD DUA.

---

## How to Run (Google Colab recommended)

1. Upload your analytic file to Colab (or mount Google Drive).

2. Install dependencies:

   `pip install -q numpy pandas scikit-learn statsmodels xgboost shap matplotlib`

3. Run notebooks in order:

   * Cohort + descriptives (LOS bins, Table 1)
   * Modeling (Model A vs Model B, AUROC, SHAP)
   * Export figures/tables for manuscript

---

## Variable Naming Notes

HCUP extracts may use different variable names. Common names in this project include:

* Outcome: `died` / `DIED`
* LOS: `los` / `LOS`
* Charges: `totchg` / `TOTCHG` (or scaled `totchg_10k`)
* Procedures: `i10_npr` / `I10_NPR`
* Diagnostic burden: `i10_ndx` / `I10_NDX`
* Context variables: `pay1`, `zipinc_qrtl`, `pl_nchs`, `hcup_ed`, `aweekend`, `elective`

If your dataset uses different names, update the configuration cells in the notebooks/scripts.

---

## Reproducibility Notes

* Cross-validation uses a fixed random seed (e.g., 42) where possible.
* AUROC and SHAP rankings can vary if you change:

  * feature definitions, preprocessing, missingness handling, or cohort filters
* Ensure that Figure 2 AUROCs are reported from the same run that generated the final ROC plot.

---

## Data Use and Privacy

NRD is a restricted dataset governed by the HCUP DUA. This repository contains code only. Do **not** upload:

* raw NRD files
* discharge-level records
* derived datasets that violate the DUA

---

## Citation

If you use or adapt this code, please cite the associated manuscript (update once available):

Alkam T, et al. *Nonlinear Length of Stay and In-Hospital Mortality in Alzheimer’s Disease Hospitalizations: Admission-Only vs Inpatient-Course Prediction Using Explainable Machine Learning.* (Manuscript submitted/in preparation).

Also cite HCUP NRD according to HCUP guidance.

---

## License

**MIT License**.

---

## Contact

Tursun Alkam, MD/PhD
Email: [tursun.alkam@gmail.com](mailto:tursun.alkam@gmail.com)

---

## Acknowledgments

This research uses data from the Healthcare Cost and Utilization Project (HCUP), Agency for Healthcare Research and Quality (AHRQ). Interpretations and conclusions are those of the authors and do not necessarily represent the views of AHRQ or HCUP.
