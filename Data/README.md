# 📂 Data

Dataset and data documentation for the [Telecom Customer Churn Analysis](../README.md).

**File:** [`02 Churn-Dataset.xlsx`](02%20Churn-Dataset.xlsx) — 7,043 rows × 23 columns, one row per customer, no duplicate
`customerID`, no rows dropped during cleaning. The notebook reads it from this folder, as
`Data/02 Churn-Dataset.xlsx` relative to the repository root.

**Data source.** Columns 1–21 plus `Churn` are the **IBM Telco Customer Churn** sample dataset,
originally published by IBM as accelerator sample data:

- IBM Community (original publication): https://community.ibm.com/community/user/blogs/steven-macko/2019/07/11/telco-customer-churn-1113
- Kaggle (the commonly used mirror): https://www.kaggle.com/datasets/blastchar/telco-customer-churn

The two support-activity columns, `numAdminTickets` and `numTechTickets`, are **not** present in
either source — they were added by the programme for this case study, and no data dictionary was
supplied for them. See *Limitations* in the [root README](../README.md#%EF%B8%8F-limitations).

**Snapshot semantics.** A single point-in-time extract with no date column and no customer history,
so `Churn` is a state at snapshot time, not an event with a timestamp. Every "churn rate" reported in
the analysis is a cross-sectional share, not a hazard rate over a defined window.

**Assumptions carried through the analysis.**

1. `Churn = Yes` is a completed departure, and the recorded attributes describe the customer
*before* leaving. Nothing in the file proves this ordering.
2. Ticket counts cover a recent window rather than the full customer lifetime — undocumented, see
Limitations.
3. `MonthlyCharges` is a stable proxy for recurring revenue, so summing it across a segment gives
that segment's MRR. Ignores expiring discounts, downgrades and one-off charges.

Terms of use: [Licence & Attribution](../README.md#-licence--attribution) in the root README.

## Data dictionary

| Column | Type | Values / range | Notes |
|---|---|---|---|
| `customerID` | id | 7,043 unique | no duplicates |
| `gender` | cat | Female, Male | |
| `SeniorCitizen` | bin | 0, 1 | 1 = 65+; only age signal in the file |
| `Partner` | cat | Yes, No | |
| `Dependents` | cat | Yes, No | |
| `tenure` | int | 0–72 months | 11 customers at 0 = signed up in the current cycle |
| `PhoneService` | cat | Yes, No | |
| `MultipleLines` | cat | Yes, No, No phone service | third level is a structural "not applicable" |
| `InternetService` | cat | DSL (2,421), Fiber optic (3,096), No (1,526) | |
| `OnlineSecurity` | cat | Yes, No, No internet service | third level = not applicable |
| `OnlineBackup` | cat | Yes, No, No internet service | as above |
| `DeviceProtection` | cat | Yes, No, No internet service | as above |
| `TechSupport` | cat | Yes, No, No internet service | as above |
| `StreamingTV` | cat | Yes, No, No internet service | as above |
| `StreamingMovies` | cat | Yes, No, No internet service | as above |
| `Contract` | cat | Month-to-month, One year, Two year | |
| `PaperlessBilling` | cat | Yes, No | |
| `PaymentMethod` | cat | Electronic check, Mailed check, Bank transfer (automatic), Credit card (automatic) | |
| `MonthlyCharges` | float | 18.25–118.75 | current recurring charge |
| `TotalCharges` | float | 0–8,684.80 | stored as text in the source; 11 blanks, all `tenure == 0` |
| `numAdminTickets` | int | 0–5 (5,842 zeros) | **undocumented window**; counts for 1–5 are near-uniform (223 / 243 / 262 / 228 / 245), consistent with synthetic generation |
| `numTechTickets` | int | 0–9 (6,073 zeros) | **undocumented window**; decays smoothly, behaves like real ticket data |
| `Churn` | target | Yes (1,869), No (5,174) | 26.5% churn |

## Cleaning decisions

| Step | Rows before → after | Decision and reason |
|---|---|---|
| Load | — → 7,043 | 23 columns, `customerID` unique, no duplicate rows |
| `TotalCharges` type | 7,043 → 7,043 | stored as text with blanks; coerced to numeric, producing 11 NaN |
| `TotalCharges` blanks | 7,043 → 7,043 | all 11 are `tenure == 0`, i.e. billed nothing yet. Filled with 0 rather than dropped — these are genuine new customers, and dropping them would bias the shortest-tenure bucket, which has the highest churn |
| Target encoding | 7,043 → 7,043 | `Churned = (Churn == 'Yes')` |
| Outliers | 7,043 → 7,043 | none removed; `tenure` 0–72, `MonthlyCharges` 18.25–118.75, `TotalCharges` 0–8,684.80 all sit within plausible product ranges |
| Structural nulls | — | "No internet service" / "No phone service" kept as their own level rather than recoded to "No" — they mark non-applicability, not a customer choice (see the bundling finding) |

**No rows were dropped at any stage: 7,043 in, 7,043 out.**
