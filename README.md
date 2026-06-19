# CreditScope — Loan Default & Risk Analytics

A 9-page Power BI dashboard analyzing loan default risk across borrower demographics, employment, education, loan structure, and credit risk indicators. Built on a cleaned credit risk dataset of 32,580 customers and €312M in total loan volume.


## 📊 Key Insights

### Portfolio overview


Overall default rate across the portfolio: 21.82% (7.11K of 32.58K customers)
Average customer income: €66.07K; average DTI ratio: 34.52%; average interest rate: 11.01%
Non-defaulted loans total €0.24bn, more than 3x the €0.08bn held in defaulted loans


## Loan grade is the single strongest risk signal


Default rate climbs almost linearly with grade, from 9.96% (Grade A) to 98.44% (Grade G)
Grades A and B carry roughly 80% of all customers (10.8K + 10.5K of 32.58K) — the portfolio is heavily concentrated in its safest tiers
Grade G loans also carry the highest average interest rate (~20%) and the highest average loan amount (€17.2K), compounding risk with exposure


## Interest rate and DTI are leading indicators, not just outcomes


Default rate by Interest Rate Group rises from 9.40% (Low, below 8%) to 85.14% (Extremely High)
Default rate by DTI Group rises from 10.99% (0–10%) to 38.87% (40%+)
Both relationships are close to monotonic, suggesting either could anchor an early-warning score


## Loan intent matters more than expected


Debt consolidation loans default at 28.59% — the highest of any intent category, and notably higher than the portfolio average
Venture loans default least at 14.81%, despite carrying a similar average loan amount to other intents
This ~14-point spread by intent alone is larger than the spread seen across education levels (only ~1 point, see below)


## Education level has surprisingly little effect


Default rate ranges narrowly from 21.16% (PhD) to 22.17% (Master's) — essentially flat across all four education levels
This is a useful negative finding: education is not a meaningful risk differentiator in this portfolio, unlike loan grade or DTI


## Employment type tells a similar story


Default rate ranges from 21.57% (Unemployed) to 22.67% (Self-employed) — also a narrow band
Counter-intuitively, "Full-time" employment doesn't show a materially lower default rate than other employment types in this dataset


## Demographics: home ownership and age


Renters default at 31.57%, more than 4x the rate of homeowners at 7.47% — the largest gap of any single demographic cut in the dashboard
Older borrowers (55+) default more often (25.32%) than younger cohorts (35–44 at 20.55%), a mild but consistent downward trend with the 18–24 group sitting close behind at 23.22%


## Compounding risk


The riskiest identifiable segment combines Grade G/F, Extremely High interest rate, and high DTI — each of these alone pushes default risk above 60–85%, and they tend to co-occur (loan grade visibly drives the interest rate a borrower receives)



## 🛠️ Tools & Skills Used


Power BI Desktop — full report build: data modeling, DAX measures, multi-page UI design
DAX measures — default-rate calculations that recompute correctly within any filter/slicer context
Categorical binning (grouped columns) — Age Group, DTI Group, and Interest Rate Group built from continuous fields to enable cohort-style comparison
Cross-filtering & page navigation — a custom left-rail navigator and synced KPI header repeated on every page
Multi-visual layout — donut charts, treemaps, clustered/stacked column charts, scatter plots, and slicer panels combined per page
Conditional/comparative analysis design — every page isolates one risk dimension (intent, grade, education, employment, DTI, interest rate) while holding the same KPI frame constant, enabling direct page-to-page comparison



## 🧮 Derived Measures & DAX

dax-- Core volume & portfolio measures
Total Customers     = DISTINCTCOUNT(Credit_Risk_Dataset_cleaned[client_ID])
Total Loan Amount    = SUM(Credit_Risk_Dataset_cleaned[loan_amnt])
Average Income      = AVERAGE(Credit_Risk_Dataset_cleaned[person_income])
Average DTI Ratio    = AVERAGE(Credit_Risk_Dataset_cleaned[debt_to_income_ratio])
Average Interest Rate = AVERAGE(Credit_Risk_Dataset_cleaned[loan_int_rate])

-- Default Rate: the central risk measure, recalculates per filter context
Default Rate =
DIVIDE(
    CALCULATE(
        DISTINCTCOUNT(Credit_Risk_Dataset_cleaned[client_ID]),
        Credit_Risk_Dataset_cleaned[loan_status] = 1
    ),
    DISTINCTCOUNT(Credit_Risk_Dataset_cleaned[client_ID]),
    0
)

-- Loan Status Label: readable mapping from the binary status flag
Loan Status Label =
IF(
    Credit_Risk_Dataset_cleaned[loan_status] = 1,
    "Default",
    "Non Default"
)

-- Age Group: binned cohort column
Age Group =
SWITCH(
    TRUE(),
    Credit_Risk_Dataset_cleaned[person_age] < 25, "18-24",
    Credit_Risk_Dataset_cleaned[person_age] < 35, "25-34",
    Credit_Risk_Dataset_cleaned[person_age] < 45, "35-44",
    Credit_Risk_Dataset_cleaned[person_age] < 55, "45-54",
    "55+"
)

-- DTI Group: binned debt-to-income ratio
DTI Group =
SWITCH(
    TRUE(),
    Credit_Risk_Dataset_cleaned[debt_to_income_ratio] < 0.10, "Low (0 - 10%)",
    Credit_Risk_Dataset_cleaned[debt_to_income_ratio] < 0.20, "Moderate (10% - 20%)",
    Credit_Risk_Dataset_cleaned[debt_to_income_ratio] < 0.30, "High (20% - 30%)",
    Credit_Risk_Dataset_cleaned[debt_to_income_ratio] < 0.40, "Very High (30% - 40%)",
    "Extremely High (40%+)"
)

-- Interest Rate Group: binned interest rate
Interest Rate Group =
SWITCH(
    TRUE(),
    Credit_Risk_Dataset_cleaned[loan_int_rate] < 8, "Low (Below 8%)",
    Credit_Risk_Dataset_cleaned[loan_int_rate] < 12, "Medium (8%-12%)",
    Credit_Risk_Dataset_cleaned[loan_int_rate] < 16, "High (12%-16%)",
    Credit_Risk_Dataset_cleaned[loan_int_rate] < 20, "Very High (16%-20%)",
    "Extremely High (20%+)"
)


Note: bin boundaries above are inferred from the chart labels/ordering visible on the dashboard (e.g., DTI Group and Interest Rate Group axis labels). Confirm exact thresholds against your own measure definitions in Power BI, since label text was occasionally truncated in the source screenshots.




## 🗂️ Data Model

Single flat table: Credit_Risk_Dataset_cleaned — one row per loan applicant, 32,580 rows.

FieldTypeDescriptionclient_IDIDUnique applicant identifierperson_ageNumericApplicant age (binned into Age Group)genderCategoryMale / FemalecountryCategoryCanada / UK / USAperson_incomeNumericAnnual incomeperson_home_ownershipCategoryRent / Own / Mortgage / Otheremployment_typeCategoryFull-time / Part-time / Self-employed / Unemployededucation_levelCategoryHigh School / Bachelor / Master / PhDloan_intentCategoryDebt Consolidation / Education / Medical / Personal / Venture / Home Improvementloan_gradeCategoryLender-assigned risk grade, A (lowest risk) – G (highest risk)loan_amntNumericLoan amount requestedloan_int_rateNumericInterest rate on the loandebt_to_income_ratioNumericDebt-to-income ratio (binned into DTI Group)loan_statusBinary1 = Default, 0 = Non-default (mapped to Loan Status Label)

No relationships/joins required — this is a single denormalized table, unlike a typical star-schema model. All "dimensions" (loan grade, intent, education, etc.) are columns within the same table rather than separate linked tables.

## 📌 Notes


Default Rate recalculates within whatever filter context is active — filtering to Loan Grade = G shows the default rate for that grade specifically, not the portfolio overall.
Education level and employment type show minimal spread in default rate (~1 percentage point) — worth flagging in any model-building exercise, since these fields may carry less predictive value than grade, DTI, or interest rate.
Home ownership shows the single largest demographic gap in the dataset (Rent: 31.57% vs. Own: 7.47%) — a strong candidate feature for any future risk-scoring model.



Built as a portfolio project to demonstrate Power BI data modeling, DAX measures, and multi-page dashboard design for credit risk and loan default analysis.
ContentPharmacy_Data__ashiiq.xlsxxlsxPharmacy Dashboard.pbixpbixCredit Risk Dataset Dashboard.pbixpbix
