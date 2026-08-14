# NRD 2017 Alzheimer’s Disease In-Hospital Mortality Analysis

## Overview

This repository contains the reproducible analysis code for the manuscript:

**Nonlinear Association of Length of Stay With In-Hospital Mortality in Alzheimer’s Disease Hospitalizations: Admission-Only vs Inpatient-Course Prediction Using Explainable Machine Learning**

The study uses the **2017 Healthcare Cost and Utilization Project (HCUP) Nationwide Readmissions Database (NRD)** to examine the relationship between hospital length of stay (LOS) and in-hospital mortality among older adults hospitalized with documented Alzheimer’s disease (AD).

The analysis combines nationally weighted descriptive analyses, multivariable logistic regression, restricted cubic spline modeling of LOS, patient-grouped cross-validation, XGBoost prediction models, SHapley Additive exPlanations (SHAP), decision curve analysis, diagnosis-family profiling at LOS extremes, sensitivity analyses, and regularized logistic-regression benchmarking.

The repository contains **analysis code only**. HCUP-NRD discharge-level data cannot be redistributed because of the HCUP Data Use Agreement.

---

## Study Population

Hospitalizations were eligible if they met all of the following criteria:

- Age **≥60 years**
- A documented Alzheimer’s disease diagnosis in any available diagnosis position
- At least one qualifying ICD-10-CM Alzheimer’s disease code:
  - `G30.0` — Alzheimer’s disease with early onset
  - `G30.1` — Alzheimer’s disease with late onset
  - `G30.8` — Other Alzheimer’s disease
  - `G30.9` — Alzheimer’s disease, unspecified

Broader dementia or neurocognitive-disorder codes such as `F01`, `F02`, and `F03` were **not used as substitutes for G30.x**. Hospitalizations could still contain these additional dementia-related codes if a qualifying `G30.x` diagnosis was also present.

Records with missing in-hospital mortality status (`DIED`) were excluded from the primary analytic cohort. Records with missing LOS were retained for analyses not requiring LOS and excluded only from LOS-dependent analyses.

### Final analytic cohort

- Eligible G30.x hospitalizations before outcome exclusion: **249,633**
- Missing `DIED`: **126**
- Final analytic cohort: **249,507**
- In-hospital deaths: **12,666**
- Unweighted mortality: **5.08%**
- Weighted national estimate: **455,256 hospitalizations**
- Weighted deaths: **22,612**
- Weighted mortality: **4.97%**
- Missing LOS: **69**
- LOS-complete sample: **249,438**

---

## Primary Study Design

### Model A — Admission-oriented model

Model A excludes explicit inpatient-course variables and includes demographic, admission-context, diagnostic-burden, and selected clinical-condition variables.

Examples include age, sex, weekend admission, elective admission, primary payer, ZIP-code income quartile, urban/rural classification, emergency department involvement, diagnostic burden, sepsis, acute kidney injury, stroke, urinary tract infection, congestive heart failure, chronic kidney disease, pneumonia, delirium, and chronic obstructive pulmonary disease.

### Model B — Full inpatient-course model

Model B contains the Model A variables and additionally includes:

- Length of stay (`LOS`)
- Procedure count (`I10_NPR`)
- Total hospital charges (`TOTCHG`)

The Model A versus Model B framework is intended to distinguish information available without explicit course proxies from additional prognostic information that becomes available as the hospitalization evolves.

---

## Emergency Department Variable

The original multi-category `HCUP_ED` variable was collapsed to a binary indicator to avoid sparse-category separation and to improve clinical interpretability:

- `0` — No evidence of emergency department services
- `1` — Any evidence of emergency department services (`HCUP_ED` values 1–4)

The binary variable is named:

`ed_involvement`

---

## Length-of-Stay Analysis

LOS was examined descriptively using the following clinically interpretable categories:

- 0–1 day
- 2–3 days
- 4–6 days
- 7–9 days
- 10–14 days
- 15–21 days
- ≥22 days

The **unadjusted** LOS-mortality pattern was U-shaped:

- 0–1 day: **13.00%**
- 4–6 days: **3.47%**
- ≥22 days: **7.77%**

After multivariable adjustment using restricted cubic splines, LOS remained strongly nonlinear, but adjusted predicted mortality declined across the modeled LOS range. Therefore, the manuscript distinguishes the **crude U-shaped descriptive pattern** from the **adjusted nonlinear association**.

---

## Machine-Learning Evaluation

XGBoost models were evaluated using **patient-grouped 5-fold cross-validation** based on the NRD patient linkage identifier (`NRD_VisitLink`). This prevents hospitalizations belonging to the same patient from being split between training and validation folds.

Primary performance was summarized using pooled out-of-fold predictions.

| Model | AUROC | AUPRC |
|---|---:|---:|
| Model A | 0.780 | 0.180 |
| Model B | 0.828 | 0.329 |

Fold-level AUROC variability was low:

- Model A SD: approximately **0.001**
- Model B SD: approximately **0.004**

A regularized logistic-regression comparator was also evaluated using the same patient-grouped framework.

---

## Explainability

Model interpretation was performed using **SHapley Additive exPlanations (SHAP)**.

Across patient-grouped folds, the most stable influential predictors included:

- Sepsis
- Diagnostic burden
- Acute kidney injury
- Age
- Stroke
- Pneumonia

In Model B, LOS and procedure count emerged as prominent inpatient-course contributors.

Shared predictors are displayed in a common vertical order across SHAP panels to facilitate direct comparison between Model A and Model B.

SHAP values are interpreted as model-attribution measures and **not as evidence of causality**.

---

## Diagnosis-Family Analysis at LOS Extremes

Diagnosis-family prevalence was compared between:

- Short LOS: **≤5 days**
- Prolonged LOS: **≥25 days**

Each three-character ICD-10-CM family was counted at most once per hospitalization.

The following diagnosis families were excluded from this contextual analysis:

- `G30` — cohort-defining Alzheimer’s disease
- `F01`, `F02`, `F03` — dementia families
- `Z*` — administrative/status codes
- `V*`, `W*`, `X*`, `Y*` — external-cause codes

The final groups were:

- Short LOS: **152,991 hospitalizations**
- Prolonged LOS: **6,212 hospitalizations**

Examples of diagnoses enriched in prolonged stays included:

- Fluid/electrolyte disorders
- Dysphagia
- Delirium
- Urinary tract infection
- Respiratory failure
- Acute kidney injury
- Pressure ulcer

Pressure ulcers (`L89`) were present in:

- **5.66%** of short stays
- **13.67%** of prolonged stays

Absolute prevalence difference: **8.01 percentage points**

Prevalence ratio: **2.41**

These findings are descriptive and do not establish temporal or causal relationships.

---

## Repository Files

The main analysis notebook is:

`NRD_2017_AD_G30_Clean_Revision_Colab_v3_ED_binary.ipynb`

The notebook is organized to use a single final analytic dataframe:

`analysis_df`

Typical generated outputs include:

`Cohort_Audit.csv`

`Table1_Main_Cohort_Characteristics.csv`

`Table2_Weighted_Logistic_ModelA_vs_ModelB.csv`

`Table2_LOS_Spline_Tests.csv`

`Figure1_LOS_Mortality.png`

`Figure2_GroupKFold_Pooled_OOF_ROC.png`

`Figure3A_SHAP_ModelA.png`

`Figure3B_SHAP_ModelB.png`

`Figure4_Adjusted_RCS_Sensitivity.png`

`Figure4_Adjusted_RCS_Data.csv`

`Primary_XGBoost_GroupKFold_OOF_Performance.csv`

`Primary_XGBoost_Fold_Performance.csv`

`Supplementary_Figure_S1_Decision_Curve.png`

`Supplementary_Table_S6A_Short_LOS_Diagnosis_Families_FINAL.csv`

`Supplementary_Table_S6B_Prolonged_LOS_Diagnosis_Families_FINAL.csv`

`Supplementary_Table_S6C_LOS_Extreme_Comparison_FINAL.csv`

`Software_Versions.csv`

`Manuscript_Update_Summary.json`

Additional supplementary tables contain spline-sensitivity analyses, threshold metrics, fold-level SHAP stability, decision-curve results, and regularized logistic-regression comparisons.

---

## Software

The analysis was developed in Python/Google Colab using standard scientific-computing libraries, including:

- Python
- pandas
- NumPy
- SciPy
- statsmodels
- scikit-learn
- XGBoost
- SHAP
- matplotlib

The manuscript revision environment included approximately:

`Python 3.12`

`statsmodels 0.14.6`

`scikit-learn 1.6.1`

`XGBoost 3.2.0`

`SHAP 0.51.0`

For the exact package versions used in a specific run, see:

`Software_Versions.csv`

---

## Reproducing the Analysis

### 1. Obtain the data

The 2017 NRD must be obtained directly from the **Healthcare Cost and Utilization Project (HCUP), Agency for Healthcare Research and Quality**.

Users must complete the required HCUP training and agree to the applicable Data Use Agreement.

The raw NRD files are **not included** in this repository.

### 2. Prepare the input data

The notebook expects NRD variables required for cohort definition, outcome definition, weighting, diagnosis processing, patient grouping, and modeling.

Examples include:

`AGE`

`DIED`

`LOS`

`DISCWT`

`NRD_VisitLink`

`HOSP_NRD`

`I10_DX1 ... diagnosis fields`

`I10_NDX`

`I10_NPR`

`TOTCHG`

`AWEEKEND`

`ELECTIVE`

`PAY1`

`ZIPINC_QRTL`

`PL_NCHS`

`HCUP_ED`

Variable names may need to be standardized to lowercase depending on the local preprocessing workflow.

### 3. Open the notebook in Google Colab or Jupyter

Recommended workflow:

1. Start a fresh runtime.
2. Upload or mount the prepared NRD analytic file.
3. Run the notebook from top to bottom.
4. Confirm the cohort audit.
5. Review validation checks before using generated outputs.

### 4. Verify the final cohort

A correct final run should reproduce approximately:

`Final analytic N: 249,507`

`Deaths: 12,666`

`Unweighted mortality: 5.08%`

`Missing LOS: 69`

`LOS-complete N: 249,438`

---

## Important Methodological Notes

### Outcome missingness

Missing `DIED` values are excluded and are **not recoded as survival**.

### Patient-level leakage prevention

Cross-validation uses `NRD_VisitLink` to keep all hospitalizations for the same patient within the same fold.

### LOS interpretation

LOS is time-dependent and subject to survivorship dynamics and reverse causation. It should be interpreted as a **trajectory marker**, not as a causal exposure.

### Hospital charges

`TOTCHG` represents billed hospital charges rather than standardized cost or physiologic severity. Charges may vary because of institutional billing structures, geography, service mix, and utilization patterns.

### Administrative coding

The analysis depends on documented ICD-10-CM diagnoses. Patients with clinically present but unrecognized, undocumented, or miscoded AD are not captured by the G30.x cohort definition.

---

## Data Availability

The 2017 NRD is a restricted HCUP dataset.

Because of the HCUP Data Use Agreement, discharge-level data used in this study cannot be uploaded to GitHub or otherwise redistributed.

Researchers interested in reproducing the study must obtain the NRD directly from HCUP.

---

## Code Availability

All code required to reproduce the cohort construction, descriptive analyses, regression models, machine-learning analyses, explainability analyses, sensitivity analyses, and manuscript figures/tables is intended to be available in this repository.

Please open an issue if you identify a reproducibility problem in the public code.

---

## Citation

If you use this code, please cite the associated manuscript after publication.

> Alkam T, Tarshizi E, Van Benschoten AH. *Nonlinear Association of Length of Stay With In-Hospital Mortality in Alzheimer’s Disease Hospitalizations: Admission-Only vs Inpatient-Course Prediction Using Explainable Machine Learning.*

Publication details and DOI will be added after publication.

---

## Authors

**Tursun Alkam, MD, PhD**  
Master’s Program of Applied Artificial Intelligence  
University of San Diego  
San Diego, California, USA

**Ebrahim Tarshizi, PhD, MBA**

**Andrew H. Van Benschoten, PhD**

---

## Disclaimer

This repository is provided for research and reproducibility purposes. The analyses are based on de-identified administrative data and are not intended for direct clinical decision-making without independent validation.

The findings are observational and should not be interpreted as establishing causal relationships between LOS, diagnoses, procedures, hospital charges, or mortality.

---

## License

Code may be reused subject to the repository license and all applicable HCUP Data Use Agreement restrictions.

No HCUP data are distributed with this repository.
