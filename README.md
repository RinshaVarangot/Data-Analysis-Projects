# Titanic Survival Analysis & Prediction

A complete data science project on the Titanic dataset — covering data cleaning, feature engineering, exploratory data analysis, and an interactive Power BI dashboard. The project investigates what factors determined survival and builds a foundation for machine learning classification.

> **891 passengers · 342 survivors (38.38%) · Average fare $32.20 · Average age 29.1 years**



## Project Overview

The sinking of the Titanic in 1912 is one of history's most studied disasters. This project applies the full data science workflow to understand the demographic and socioeconomic patterns behind survival outcomes. The analysis reveals that survival was far from random — class, gender, age, and fare were all strong determinants of who made it off the ship.

---

## Dataset

**Source:** Titanic passenger manifest (Excel format)  
**Rows:** 891 passengers | **Columns:** 12 (raw) → cleaned and enriched to 14

| Column | Description |
|--------|-------------|
| `PassengerId` | Unique passenger identifier |
| `Survived` | Target variable — 0 = No, 1 = Yes |
| `Pclass` | Ticket class — 1 = First, 2 = Second, 3 = Third |
| `Name` | Passenger name (used to extract `Title`) |
| `Sex` | Gender |
| `Age` | Age in years (imputed where missing) |
| `SibSp` | Number of siblings / spouses aboard |
| `Parch` | Number of parents / children aboard |
| `Fare` | Ticket fare paid (USD) |
| `Embarked` | Port of embarkation — C = Cherbourg, Q = Queenstown, S = Southampton |

Columns dropped: `Ticket` (low predictive value), `Cabin` (>77% missing).

---

## Project Structure

```
Titanic_Survival.ipynb              # Main notebook — cleaning, EDA, feature engineering
Titanic Dataset.xlsx                # Raw dataset
cleaned_Titanic_Dataset.xlsx        # Cleaned and feature-engineered dataset
Screenshot_2026-06-08_144814.png    # Power BI dashboard screenshot
```

---

## Workflow

### 1. Data Cleaning

**Missing value handling:**

| Column | Missing | Treatment |
|--------|---------|-----------|
| `Age` | ~20% | Imputed by passenger class median (Class 1 → 37, Class 2 → 29, Class 3 → 24) |
| `Embarked` | 2 rows | Filled with `'S'` (Southampton — the mode) |
| `Cabin` | >77% | Dropped entirely |

**Columns removed:** `Ticket`, `Cabin`, `Name` (after title extraction)

No duplicate rows were found.

### 2. Feature Engineering

Five new features were derived from the raw data to improve analytical depth:

| Feature | Method | Purpose |
|---------|--------|---------|
| `Title` | Extracted from `Name` via regex | Captures social status and gender signal beyond the `Sex` column |
| `FamilyStatus` | `Alone` if `SibSp == 0` and `Parch == 0`, else `Family` | Identifies whether a passenger travelled solo or with relatives |
| `FareBand` | `pd.qcut(Fare, 4)` — quartile bins | Groups fare into four equal bands for categorical analysis |
| `AgeGroup` | `pd.cut(Age, bins=[0,12,18,35,60,100])` | Segments passengers into Child, Teen, Adult, MiddleAge, Senior |
| Cleaned `Title` | Rare titles consolidated to `Rare`; `Mlle`/`Ms` → `Miss`; `Mme` → `Mrs` | Reduces noise from low-frequency title variants |

Categorical columns (`Sex`, `Embarked`, `Title`, `FamilyStatus`) were label-encoded for correlation analysis and downstream modelling.

### 3. Exploratory Data Analysis

Analysis was conducted across three levels:

- **Univariate** — distributions of `Age`, `Fare`, `SibSp`, `Parch`, and all categorical variables
- **Bivariate** — survival rate broken down by every key feature
- **Correlation** — heatmap of all numeric features against `Survived`

Key correlations with survival:
- `Fare` — **positive** (higher fare → higher survival)
- `Pclass` — **negative** (higher class number → lower survival)
- `Sex` — **strong signal** (female passengers survived at a much higher rate)

---

## Key Insights

### Survival by Class

| Pclass | Passengers | Survivors | Fatalities | Survival Rate | Avg Fare | Avg Age |
|--------|-----------|-----------|------------|---------------|----------|---------|
| 1 | 216 | 136 | 80 | **62.96%** | $84.2 | 38.06 |
| 2 | 184 | 87 | 97 | **47.28%** | $20.7 | 29.83 |
| 3 | 491 | 119 | 372 | **24.24%** | $13.7 | 24.82 |
| **Total** | **891** | **342** | **549** | **38.38%** | **$32.2** | **29.07** |

Passenger class was one of the strongest predictors of survival. First-class passengers were more than 2.5× more likely to survive than third-class passengers. This reflects both their physical proximity to the deck and lifeboats, and the socioeconomic prioritisation that occurred during the evacuation.

### Survival by Gender

Female passengers survived at a dramatically higher rate than males, consistent with the "women and children first" evacuation protocol enforced by crew. Titles associated with women (`Miss`, `Mrs`) showed the highest survival counts, while `Mr` had the lowest survival rate among all title groups.

### Survival by Age Group

Children had a comparatively higher survival rate, reflecting evacuation priority. Adults (18–35) made up the largest passenger group. Senior passengers (60+) had lower survival rates, likely due to limited mobility during the evacuation. The majority of passengers were adults, with the fewest being children and seniors.

### Survival by Embarkation Port

Most passengers boarded at Southampton (S), which also produced the most fatalities in absolute terms. Passengers embarking at Cherbourg (C) had a notably better survival rate, partly because a higher proportion of Cherbourg passengers travelled in First Class.

### Survival by Family Status

Passengers travelling with family members survived at a higher rate than those travelling alone. Solo travellers made up the majority of passengers but had a lower proportional survival rate — likely because family groups received collective assistance during the evacuation.

### Survival by Title

| Title | Survival Pattern |
|-------|-----------------|
| `Mrs` | High survival — married women prioritised |
| `Miss` | High survival — young women prioritised |
| `Master` | Relatively high — young boys benefited from child priority |
| `Mr` | Lowest survival — adult males were last to board lifeboats |
| `Rare` | Mixed — includes doctors, military officers, clergy |

Title proved to be a more nuanced predictor than raw gender, capturing both social class and age signals that `Sex` alone cannot distinguish.

### Fare & Socioeconomic Status

Passengers who paid higher fares survived more. This is not coincidental — fare was directly correlated with passenger class, and First Class cabins were located on upper decks with easier access to lifeboats. The fare distribution is heavily right-skewed: most passengers paid low fares (Third Class), while a small number paid premium rates.

---

## Dashboard

An interactive Power BI dashboard was built on the cleaned dataset, featuring:

- Key metric cards: total passengers, survivors, fatalities, average fare, survival rate, average age
- Survival breakdown by class, gender, age group, embarkation port, title, and family status
- Filter panel by `Pclass`, `Sex`, `AgeGroup`, and `Embarked`
- Summary table with per-class survival rate, average fare, and average age


## Requirements

- Python 3.8+
- pandas
- numpy
- matplotlib
- seaborn
- scikit-learn
- openpyxl



