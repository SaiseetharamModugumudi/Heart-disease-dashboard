# 🫀 Cardiovascular Risk & Clinical Insights Dashboard (Power BI)

An end-to-end Power BI business intelligence and analytics project built on clinical patient diagnostic records. This dashboard models, analyzes, and visualizes key biomarkers associated with cardiovascular disease, delivering clinical decision-support metrics and risk stratification.

---

## 📌 Project Overview

Cardiovascular diseases are among the leading causes of global mortality. This analytics project processes 303 patient diagnostic profiles across 14 clinical attributes to identify key indicators associated with heart disease presence (`target = 1`). 

The dashboard provides clinical stakeholders, practitioners, and healthcare administrators with an intuitive multi-page interface to explore demographic distributions, exercise stress test outcomes, and fluoroscopy findings.

---

## 📊 Key Insights & Highlights

- **Prevalence:** 54.46% (165 of 303 patients) in the cohort were diagnosed with cardiovascular disease.
- **Heart Rate & Blood Pressure:** A significant negative correlation was observed between age and maximum achievable heart rate (`thalach`), with disease patients displaying distinct clustering under stress testing.
- **Chest Pain (CP) Analysis:** Patients reporting non-anginal and atypical angina demonstrated higher incidence proportions within this cohort compared to asymptomatic profiles.
- **Fluoroscopy & Defect Staging:** Patients presenting 0 major colored vessels (`ca = 0`) and fixed/reversible thallium defects exhibited distinct risk profiles highlighted via heatmap correlation.

---

## 🏗️ Architecture & Data Model

The data was structured into an optimized **Star Schema** to ensure fast query execution, clean separation of dimensions, and scalability.[Dim_ChestPain]           [Dim_RestECG]
      (cp: 1)                   (restecg: 1)
         │                            │
         ▼ (*)                        ▼ (*)
 ┌──────────────────────────────────────────────┐
 │                 Fact_Heart                   │
 │  (PatientID, age, sex, trestbps, chol, fbs,  │
 │   thalach, exang, oldpeak, ca, target, etc.) │
 └──────────────────────────────────────────────┘
         ▲ (*)                        ▲ (*)
         │                            │
      (thal: 1)                   (slope: 1)
     [Dim_Thal]                  [Dim_Slope]


     ### Tables & Relationships
- **Fact Table (`Fact_Heart`):** Normalized clinical measures and diagnostic targets.
- **Dimension Tables:**
  - `Dim_ChestPain`: Descriptive mapping for angina classification.
  - `Dim_Thal`: Thallium stress test results (Normal, Fixed Defect, Reversible Defect).
  - `Dim_RestECG`: Resting electrocardiographic interpretations.
  - `Dim_Slope`: ST segment slopes during peak exercise.

---

## 📈 Dashboard Pages & Structure

### Page 1: Clinical Population Overview (Executive Summary)
- **KPI Summary Cards:** Total Patients (303), Disease Prevalence (54.5%), Average Resting BP (131.6 mm Hg), Average Cholesterol (246.3 mg/dl), Average Max Heart Rate (149.6 bpm).
- **Target Distribution (Donut Chart):** Healthy vs. Heart Disease patient ratio.
- **Demographic Cohort Matrix (Clustered Bar Chart):** Age groups binned (`<40`, `40–49`, `50–59`, `60–69`, `70+`) segmented by biological sex.
- **Clinical Vitals Scatter Plot:** Resting Blood Pressure (`trestbps`) vs. Maximum Achieved Heart Rate (`thalach`), categorized by disease diagnosis with dynamic threshold reference lines.

### Page 2: Diagnostic & Stress Test Deep Dive
- **Exercise Angina Impact:** Risk distribution across patients experiencing exercise-induced angina (`exang`).
- **Matrix Heatmap:** Major fluoroscopy vessel count (`ca`) vs. Thallium defects (`thal`) with gradient prevalence highlighting.
- **ST Depression Analysis:** Peak exercise ST slope (`slope`) vs. depression depth (`oldpeak`).
- **Patient Audit Table:** Granular patient ledger with conditional background alerts for Stage 2 hypertension (`trestbps > 140`) and hypercholesterolemia (`chol > 240`).

---

## 📐 Key DAX Calculations

```dax
 Total Patient Population
Total Patients = COUNTROWS('Fact_Heart')

// Disease Cohort Counts
Patients With Disease = 
CALCULATE(
    [Total Patients], 
    'Fact_Heart'[target] = 1
)

// Prevalence Percentage
Disease Prevalence % = 
DIVIDE([Patients With Disease], [Total Patients], 0)

// Average Biomarkers
Avg Resting BP = AVERAGE('Fact_Heart'[trestbps])
Avg Max Heart Rate = AVERAGE('Fact_Heart'[thalach])
Avg Cholesterol = AVERAGE('Fact_Heart'[chol])
Avg ST Depression = AVERAGE('Fact_Heart'[oldpeak])

