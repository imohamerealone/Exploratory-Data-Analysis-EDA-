# Medical Appointment No-Shows: Exploratory Data Analysis

## Project Overview

This project performs an end-to-end Exploratory Data Analysis (EDA) on a Brazilian healthcare dataset to identify and quantify the key factors influencing medical appointment no-show rates. The analysis goes beyond simple statistical significance to assess **practical relevance** using effect size metrics (Cramér's V).

**Notebook:** `ExploratoryDataAnalysis.ipynb`  
**Dataset:** `KaggleV2-May-2016.csv`

---

## Dataset

The dataset was sourced from Kaggle and contains information on over **110,000 medical appointments** in Brazil.

| Property | Value |
|---|---|
| Rows | 110,527 |
| Columns | 14 |
| Missing Values | None |
| Target Variable | `No-show` (binary: Yes / No) |
| Class Imbalance | ~20% no-shows |

**Key features:** PatientId, AppointmentID, Gender, ScheduledDay, AppointmentDay, Age, Neighbourhood, Scholarship, Hypertension, Diabetes, Alcoholism, Handcap, SMS_received, No-show

---

## Project Structure

```
├── ExploratoryDataAnalysis.ipynb   # Main analysis notebook
├── KaggleV2-May-2016.csv           # Source dataset (download from Kaggle)
└── README.md
```

---

## Requirements

Install the required Python libraries before running the notebook:

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn kaggle
```

> **Note:** Downloading the dataset directly from Kaggle requires a Kaggle account and API key. Alternatively, download `KaggleV2-May-2016.csv` manually from [Kaggle](https://www.kaggle.com/datasets/joniarroba/noshowappointments) and place it in the project root.

---

## Analysis Walkthrough

### 1. Environment Setup
Library imports and configuration.

### 2. Data Loading & Initial Inspection
Dataset loaded with `ScheduledDay` and `AppointmentDay` parsed as datetime objects. Initial shape and data types confirmed.

### 3. Descriptive Analysis
- **Info & shape:** 110,527 rows × 14 columns, no null values.
- **Statistical summary:** Key observations include negative age values (data entry errors), a rare `Age=115` outlier, and multi-valued `Handcap` encoding that requires binarization.

### 4. Data Cleaning
Preprocessing steps to handle anomalies identified above and prepare features for visualization and modeling.

### 5. Exploratory Data Analysis

#### 5.1 Univariate Analysis
Individual feature distributions examined to understand data quality, detect anomalies, and assess predictive potential. Key insight: most patients are adults/seniors; the no-show class is imbalanced at ~20%.

#### 5.2 Bivariate Analysis
Feature-vs-target relationships explored through visual plots to identify behavioral and demographic patterns linked to missed appointments.

#### 5.3 Statistical Tests

**Method 1 — Two-sample t-test (continuous variables):**  
Both `Age` and `WaitingDays` show statistically significant mean differences between no-show groups (p < 0.05). Younger patients are more likely to miss appointments than older patients.

**Method 2 — Chi-Square & Cramér's V (categorical variables):**  
Cramér's V quantifies association strength (0 = none, 1 = perfect).

| Feature | Cramér's V | Interpretation |
|---|---|---|
| SMS_received | 0.1264 | Moderate (strongest predictor) |
| Age groups | 0.0803 | Weak |
| Scheduled hour | 0.0712 | Weak |
| Neighbourhood | 0.0611 | Weak |
| Gender | < 0.05 | Negligible |
| Alcoholism | < 0.05 | Negligible |

### 6. Feature Interactions

**Gender × Age:** Male infants (age 0) show a disproportionately high no-show spike. Female patients aged 20–40 represent a thicker "adult hump" in the no-show distribution.

**SMS × Age Group — The "SMS Paradox":** Across every age group, patients who *received* SMS reminders show *higher* no-show rates than those who did not. This is attributed to **lead-time confounding**: reminders are disproportionately sent to patients with longer scheduling gaps, which are themselves the primary driver of no-shows.

---

## Key Findings

Features are ranked into three tiers based on practical relevance:

**Tier 1 — Key Drivers**
- **SMS Reminders** (Cramér's V = 0.1264): Strongest predictor, but confounded by lead time. Patients sent reminders are already high-risk due to longer wait times — not because reminders are ineffective.
- **Scheduled Hour** (Cramér's V = 0.0712): Afternoon appointments have slightly higher no-show rates.
- **Neighbourhood** (Cramér's V = 0.0611): Geographic variation is statistically and practically significant.

**Tier 2 — Weak but Statistically Significant**
- Age, Hypertension, Diabetes, and Scholarship status are significant but have minimal predictive power.

**Tier 3 — Negligible**
- Gender, Alcoholism, and Disability status show no meaningful association with no-shows.

---

## Recommendations

1. **Redesign the SMS strategy by lead time:**
   - 0–7 days: single SMS
   - 8–30 days: SMS + email
   - 30+ days: SMS + email + phone call  
   *Expected impact: reduce no-show rate in the 30+ day group from ~41.6% to ~30%*

2. **Geographic targeting:** Identify high-risk neighbourhoods (>25% no-show rate) and deploy localized interventions — community health workers, improved transport access, flexible scheduling.  
   *Expected impact: 5–10% reduction in targeted areas*

3. **Optimize scheduling:** Shift high-risk afternoon slots (14:00–17:00) to morning when feasible. Apply tiered reminders to patients who must be scheduled in the afternoon.  
   *Expected impact: 2–3% overall reduction*

4. **Deprioritize demographic filters:** Gender, age, and alcoholism have statistically significant but negligibly small effect sizes. Including them as predictors adds noise to models and raises ethical concerns around discriminatory targeting.

---

## Next Steps

Predictive modeling on this dataset — using the insights from this EDA to engineer features and evaluate classification models for predicting appointment no-shows.
