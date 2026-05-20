# NRD_2017
Nonlinear Association of Length of Stay With In-Hospital Mortality in Alzheimer’s Disease Hospitalizations: Admission-Only vs Inpatient-Course Prediction Using Explainable Machine Learning

```markdown
# Nonlinear Length of Stay–Mortality Patterns in Alzheimer’s Disease Hospitalizations  
**Admission-Only vs Inpatient-Course Prediction Using Explainable Machine Learning (NRD 2017)**

## Overview
This repository contains the analysis code used to study **in-hospital mortality** among older adults hospitalized with **Alzheimer’s disease (AD)** using the **HCUP Nationwide Readmissions Database (NRD) 2017**. The study focuses on:

1. The **nonlinear (U-shaped)** relationship between **length of stay (LOS)** and **in-hospital mortality**.
2. A prespecified **two-model framework** distinguishing:
   - **Model A (Admission-only):** predictors available at admission; excludes inpatient-course proxies.
   - **Model B (Inpatient-course):** adds inpatient-course variables (LOS, procedures, total charges).
3. **Explainable machine learning** using **XGBoost + SHAP** with cross-validation and feature-stability summaries.

> **Important:** This repo does **not** include HCUP/NRD data. Users must obtain NRD legally through HCUP and follow the NRD Data Use Agreement (DUA).

---

## Key Study Elements (Conceptual)
### Outcomes
- **Primary outcome:** In-hospital mortality (NRD `DIED` / `died`).

### LOS Analyses
- **Unadjusted** mortality across clinically interpretable LOS bins (e.g., 0–1, 2–3, 4–6, …, ≥22 days).
- **Adjusted** LOS–mortality relationship using **restricted cubic splines** and **sensitivity exclusions** (excluding LOS ≤1 and ≤2 days).

### Prediction Framework
- **Model A (Admission-only):** excludes inpatient-course proxies (LOS, procedure count, charges).
- **Model B (Inpatient-course):** includes LOS, procedure count (e.g., `I10_NPR`), and total charges (e.g., `TOTCHG`).

### Explainability
- **SHAP beeswarm** and **mean(|SHAP|)** bar plots for Model A and Model B.
- Fold-level stability summaries for top-ranked SHAP features across 5-fold CV.

---

## Repository Contents (Suggested)
You can structure your repo like this:

```

.
├── notebooks/
│   ├── NRD_2017_analysis.ipynb
│   ├── modelA_modelB_shap.ipynb
│   └── figures_tables.ipynb
├── scripts/
│   ├── preprocess.py
│   ├── modeling.py
│   └── shap_utils.py
├── outputs/
│   ├── figures/
│   └── tables/
├── docs/
│   ├── figure_legends.md
│   └── supplementary_methods.md
├── requirements.txt
└── README.md

````

If you only use notebooks, that’s fine—just place them under `notebooks/` and keep outputs under `outputs/`.

---

## How to Run (Google Colab recommended)
### 1) Obtain NRD 2017
Purchase/download NRD 2017 from HCUP and extract according to HCUP instructions. Keep raw data local/private and do not upload it to public repositories.

### 2) Upload your analytic dataset to Colab
Upload your prepared analytic file (e.g., `.csv`, `.parquet`, or `.dta`) into Colab runtime storage, or mount Google Drive.

### 3) Install dependencies
In Colab:
```bash
pip install -q numpy pandas scikit-learn statsmodels xgboost shap matplotlib
````

### 4) Run the notebooks in order

A typical order:

1. `NRD_2017_analysis.ipynb` (cohort creation, LOS bins, descriptive results)
2. `modelA_modelB_shap.ipynb` (Model A/B training, CV AUROC, SHAP plots)
3. `figures_tables.ipynb` (export final figures/tables)

---

## Variables and Naming Notes

HCUP variable names can differ by extract and preprocessing. Common names used in this project include:

* `died` / `DIED` (in-hospital mortality indicator)
* `los` / `LOS` (length of stay, days)
* `totchg` / `TOTCHG` (total charges)
* `i10_npr` / `I10_NPR` (number of procedures)
* `i10_ndx` / `I10_NDX` (number of diagnoses)
* `pay1`, `zipinc_qrtl`, `pl_nchs`, `hcup_ed`, `aweekend`, `elective` (contextual variables)

If your file uses different names, update the configuration cells in the notebooks accordingly.

---

## Figures and Tables (What this repo produces)

* **Figure 1:** Unadjusted mortality (%) by LOS category with 95% CIs.
* **Figure 2:** ROC curves comparing Model A vs Model B (5-fold CV AUROC ± SD).
* **Figure 3A/3B:** SHAP summary (beeswarm) plots for Model A and Model B (publishable labels).
* **Figure 4:** Adjusted spline-predicted mortality vs LOS with sensitivity exclusions (exclude LOS ≤1 and ≤2 days).
* **Table 1:** Baseline characteristics by survival status.
* **Table 2:** Multivariable regression (Model A vs Model B).
* **Supplementary Table S1:** Unadjusted LOS-bin mortality table.
* **Supplementary Table S2:** Clinical comorbidity patterns (short LOS ≤5 vs prolonged LOS ≥25), excluding Z-codes.

---

## Reproducibility Notes

* Cross-validation uses a fixed random seed (e.g., `RANDOM_STATE = 42`) to improve reproducibility.
* SHAP plots and stability rankings may vary slightly with:

  * different feature sets,
  * different preprocessing,
  * different cohort filters,
  * changes in random splits.

To avoid inconsistencies:

* Generate **Figure 2 AUROC values** from the **same run** used for the final SHAP figures.
* Keep Model A/Model B feature definitions consistent across regression and ML sections.

---

## Data Use and Privacy

NRD is a restricted dataset governed by the HCUP **Data Use Agreement (DUA)**.
This repository contains **code only** and does not distribute any HCUP data.

Do not upload:

* raw NRD files,
* discharge-level records,
* any derived datasets that violate the DUA.

---

## Citation

If you use or adapt this code, please cite the associated manuscript (add once available):

> Alkam T, et al. *Nonlinear Length of Stay and In-Hospital Mortality in Alzheimer’s Disease Hospitalizations: Admission-Only vs Inpatient-Course Prediction Using Explainable Machine Learning.* (Manuscript in preparation/submitted).

Also cite HCUP NRD appropriately per HCUP guidance.

---

## License

Choose one and add a LICENSE file (common options: MIT, BSD-3, Apache-2.0).
Suggested: **MIT License** for code sharing.

---

## Contact

**Tursun Alkam, MD/PhD**
Email: [tursun.alkam@gmail.com](mailto:tursun.alkam@gmail.com)

---

## Acknowledgments

This research uses data from the Healthcare Cost and Utilization Project (HCUP), Agency for Healthcare Research and Quality (AHRQ). The interpretations and conclusions are those of the authors and do not necessarily represent the views of AHRQ or HCUP.

```
```
