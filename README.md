# Analysis of Academic Success at INSA Toulouse

**Research internship (Stage 4A) — INSA Toulouse, 2024–2025**
**Author: Phuc Luan NGUYEN**

---

## Overview

This project investigates the school and extracurricular factors that influence the academic success of engineering students at INSA Toulouse. Motivated by the institution's goal of supporting all admitted students — regardless of social, geographic, or academic background — in obtaining their degree, the study analyses survey data to identify predictors of academic outcomes and provide actionable recommendations.

The analysis is based on a survey conducted in May 2024 via LimeSurvey, covering more than 40 variables across housing, commuting, study habits, class engagement, social life, and student well-being.

---

## Research Questions

- Which behavioral, social, and environmental factors best explain students' academic outcomes?
- How do predictors differ across student subgroups (all students, non-freshmen, 4th-year students)?
- What concrete measures can INSA Toulouse take to improve student success rates?

---

## Dataset & Target Variables

Three datasets and two target variables were used, reflecting the constraints of the survey design:

| Dataset | Population | Target variable | Rationale |
|---|---|---|---|
| Dataset 1 | All students | `SENSATIONREUSSITE` (perceived success) | Only universal variable applicable to 1st-year students who lacked academic history at survey time |
| Dataset 2 | Non-freshmen, non-new entrants | `RATTRAPAGES` (resit exams per semester) | More objective academic measure, available for students with at least one full year |
| Dataset 3 | 4th-year students (4A) | `RATTRAPAGES` | Inspired by NSSE/NSS surveys; 4A cohort provides the most stable and representative responses (350+ replies) |

> Variables `REDOUBLEMENT` (year repetition) and `CLASSEMENTDERNIER` (class ranking) were considered and rejected: the former carries little information, the latter had ~1/3 "don't know" responses.

---

## Methodology

### Data Preparation
- Exploratory data analysis (structure, distributions, missing values)
- Type conversion: character → ordered factor
- Removal of incomplete rows; validation of cleaned dataset
- Aggregation of correlated course-engagement variables (presence, concentration, preparation, note-taking) into composite variables `IMPLICATION_CM`, `IMPLICATION_TD`, `IMPLICATION_TP`

### Statistical Analysis (R)

For each dataset, two complementary models were applied:

**1. Random Forest** (`randomForest` package)
- Used to rank variable importance via `MeanDecreaseAccuracy` and `MeanDecreaseGini`
- Applied before logistic regression to handle multicollinearity between predictors
- Parameters tuned per dataset (`mtry`, `ntree`, `nodesize`)

**2. Stepwise Multinomial Logistic Regression** (`nnet` + `MASS::stepAIC`)
- Both-direction stepwise AIC selection to identify a parsimonious model
- Chi-square tests (`chisq.test`) and Fisher exact tests used to validate associations
- Stacked bar plots produced for each significant predictor

### Key Variables Analyzed

| Category | Variables |
|---|---|
| Living situation | `TRAJET` (commute time), `LOGEMENTTYPE`, `LOGEMENTSURFACE`, `LOGEMENTBIEN` |
| Student profile | `BOURSIER` (scholarship), `HANDICAP`, `TIERSTEMPS` (extended exam time), `ACT_PRO` (part-time work) |
| Study habits | `ETUDE_SEM`, `ETUDEWE` (weekly/weekend study hours), `ETUDEGROUPE`, `ETUDEDEHORS`, `ETUDEDOM` |
| Class engagement | `IMPLICATION_CM/TD/TP` (aggregated attendance, concentration, preparation, note-taking) |
| Personal work | `REVISIONSDEB`, `CONCENTRATIONPERSO`, `CONDITIONSTRAVAIL` |
| Resources | `RESSOURCES_SUP` |
| Social life | `AMIS`, `FAMILLE`, `ISOLEMENT`, `ASSOCIATIF`, `SPORTTEMPS`, `SPECTACLES`, `SORTIEBAR`, `DETENTE`, `ECRANS`, `SOMMEIL`, `ALIMENTATION` |
| Well-being | `STRESS`, `MOTIVATION` |
| Academic profile | `BACTYPE`, `PROMO`, `SPECIALITE_PO`, `COHORTE`, `DOUBLEDIPLOME`, `INTEGRATION` |
| Academic outcomes | `SENSATIONREUSSITE`, `RATTRAPAGES`, `REDOUBLEMENT`, `CLASSEMENTDERNIER`, `CLASSEMENT1A` |

---

## Key Results

### Dataset 1 — All students (target: `SENSATIONREUSSITE`)

Random Forest top predictors: `MOTIVATION`, `STRESS`, `ISOLEMENT`, `CONCENTRATIONPERSO`, `SPECIALITE_PO`, `PROMO`

Logistic regression selected model:
```
SENSATIONREUSSITE ~ STRESS + ETUDEDEHORS + CONCENTRATIONPERSO +
                    CONDITIONSTRAVAIL + ISOLEMENT + ALIMENTATION +
                    MOTIVATION + PROMO
```

Key findings:
- `SENSATIONREUSSITE` is **positively** associated with `MOTIVATION`, `CONCENTRATIONPERSO`, `CONDITIONSTRAVAIL`, `ALIMENTATION`
- `SENSATIONREUSSITE` is **inversely** associated with `STRESS` and `ISOLEMENT`
- 1st-year students and students in PO programs report the lowest sense of success; specialty MIC is the weakest, GP3E also notably lower
- `IMPLICATION_CM` and `IMPLICATION_TD` show only weak associations — subjective well-being dominates

### Dataset 2 — Non-freshmen (target: `RATTRAPAGES`)

First model confirmed strong correlation between `SENSATIONREUSSITE` and `RATTRAPAGES`, validating the use of the former as a proxy for all students.

Logistic regression selected model (after removing outcome-related variables):
```
RATTRAPAGES ~ BACTYPE + CONCENTRATIONPERSO + SPECIALITE_PO +
              INTEGRATION + ALIMENTATION + ASSOCIATIF + ACT_PRO + ECRANS + ETUDEDEHORS
```

Key findings:
- **International students** (foreign baccalaureate) have significantly more resits than French students
- Emotional variables — `ISOLEMENT`, `STRESS`, `AMIS` — have a significant impact on resit rates
- Resit count is **inversely** associated with personal concentration, work conditions, and nutrition
- Students who integrated INSA after 1st or 2nd year tend to have more resits than those entering post-bac
- Part-time work (`ACT_PRO`) and disability (`HANDICAP`) are positively correlated with resits
- MIC and IC specialties have the highest resit rates

### Dataset 3 — 4th-year students (target: `RATTRAPAGES`)

Random Forest top predictors: `IMPLICATION_CM`, `IMPLICATION_TD`, `BACTYPE`, `ISOLEMENT`, `FAMILLE`, `ECRANS`

Logistic regression selected model:
```
RATTRAPAGES ~ IMPLICATION_TD + INTEGRATION + DETENTE +
              IMPLICATION_CM + FAMILLE + COMMENTAIRE + HANDICAP
```

Key findings:
- `IMPLICATION_TD`: regular TD attendance is associated with **fewer** resits
- `IMPLICATION_CM`: students with both occasional and systematic CM attendance have more resits, suggesting struggling students attend CM to catch up without improving outcomes
- Foreign-equivalent baccalaureate students show markedly higher resit rates
- Moderate effects: `FAMILLE`, `ISOLEMENT`, `ECRANS`, `INTEGRATION`, `HANDICAP`

---

## Descriptive Statistics Highlights

- **80%** of students commute in under 15 minutes; **95%+** are satisfied with their living conditions
- **61.7%** report being often or always stressed — **17.7%** say *always* stressed
- **36.2%** are unsatisfied with their sleep quality
- **91.2%** of students involved in campus clubs report a **positive impact on overall well-being**, yet **48.7%** do not participate in any club activity

---

## Recommendations

1. **Promote club participation**: given the strong well-being benefit, encourage broader student engagement in associations
2. **Random group assignment**: in group-work sessions, mix international students with French peers to reduce isolation and help them build social networks
3. **Integration support programs**: organize activities specifically for students who joined INSA in their 3rd or 4th year
4. **Attendance monitoring**: track TD/TP attendance to identify at-risk students early — regular absence may indicate physical or mental health issues
5. **Regular well-being surveys**: monitor stress and living conditions to intervene before academic performance deteriorates

---

## Repository Structure

```
.
├── notebooks/
│   └── analysis_R.ipynb                  # Full statistical analysis notebook (R)
├── reports/
│   ├── internship_report.pdf             # Full internship report
│   ├── poster.pdf                        # Academic poster (summary of findings)
│   └── project_brief.pdf                 # Original project specification
└── outputs/
    ├── distributions_full_sample.pdf     # Variable distributions — full cohort (pre-model)
    ├── distributions_full_sample_v2.pdf  # Variable distributions — post-cleaning
    ├── distributions_group1.pdf          # Distributions for dataset 1 (all students)
    ├── distributions_group2.pdf          # Distributions for dataset 2 (non-freshmen)
    ├── distributions_4th_year.pdf        # Distributions for 4th-year students
    └── analysis_group2.pdf               # Detailed analysis output for group 2
```

> **Note on data:** Raw survey data (Excel files) are excluded from this repository to protect student privacy in compliance with GDPR regulations.

---

## How to Reproduce

Open `notebooks/analysis_R.ipynb` in JupyterLab with the IRkernel. Install the required packages:

```r
install.packages(c(
  "tidyverse",
  "ggplot2",
  "gridExtra",
  "randomForest",
  "caret",
  "nnet",
  "MASS"
))
```

Place the survey data files (`.xlsx`) in the project root directory before running.

---

## Technologies

![R](https://img.shields.io/badge/R-276DC3?style=flat&logo=r&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=flat&logo=jupyter&logoColor=white)

**R packages:** `tidyverse` · `ggplot2` · `gridExtra` · `randomForest` · `caret` · `nnet` · `MASS`

---

## Context

This project was carried out as part of the **4th-year research internship (Stage 4A)** at [INSA Toulouse](https://www.insa-toulouse.fr/). It continues prior exploratory work initiated by a student project group (4GMM).

- Institution: INSA Toulouse — Génie Mathématique et Modélisation (GMM)
- Academic year: 2023–2024 (internship period: July–September 2024)
- Type: Applied research internship in educational data analysis
