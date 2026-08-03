# RappiPlus: Revenue, Retention & Conversion Analysis

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/DebbieJara/rappiplus-revenue-retention-analysis/blob/main/notebook/RappiPlus_Analisis.ipynb)
![Python](https://img.shields.io/badge/Python-pandas%20%7C%20numpy%20%7C%20statsmodels-3776AB?logo=python&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-PostgreSQL-4169E1?logo=postgresql&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-DAX-F2C811?logo=powerbi&logoColor=black)

## The Business Problem

RappiPlus was generating strong headline revenue, but leadership had no clear view of which products were actually profitable, where customers dropped off during checkout, or whether a proposed checkout redesign was worth shipping.

## What I Did

Using order, catalog, and marketing data, I built a profitability model from scratch in Python, validated the conversion funnel and cohort retention in SQL, ran an A/B test on a proposed checkout redesign, and delivered an executive Power BI dashboard with product-level drill-through detail.

## What the Data Revealed

- The business is profitable overall: $9.62M revenue and $2.92M net profit (~30% margin).
- Two products, Jacket-Winter-M and Laptop-Gaming-16GB, operate at a loss (-$57,235 and -$417,351) despite being among the top sellers by volume, a cost structure problem, not a demand problem.
- The checkout funnel loses the vast majority of users before purchase, with the sharpest drop (89.13%) occurring between entering payment info and completing the purchase.
- The proposed checkout redesign showed no statistically significant lift in conversion (p = 0.416), not enough evidence to justify shipping it.
- 45 orders (0.12% of volume) couldn't be matched to a catalog product, pointing to a data capture issue upstream.

The insight wasn't just "the business is profitable." It was: payment friction and two underpriced products were quietly eating into both conversion and margin.

## Technical Details

### Dataset

The data comes from the TripleTen (Practicum) bootcamp environment. It is fictitious, generated for educational purposes, and does not represent a real business. Two sources were used:

| Source | Tables/Files |
|---|---|
| CSV (cloud storage) | orders, catalog, marketing, experiment_checkout_ui |
| PostgreSQL (training database) | events, users, user_activity |

Raw and clean datasets, plus the exported SQL tables, are available here: [Google Drive folder](https://drive.google.com/drive/folders/1E6tyQ_wmgoAgn7QDM-e2uzmKT-6JRm_J?usp=drive_link)

### Analytical Workflow

| Step | Description |
|---|---|
| 1. Data quality & cleaning | Nulls, duplicates, text inconsistencies, and outliers reviewed and resolved across all datasets |
| 2. Profitability & sales behavior | Revenue, cost, marketing spend, and net profit KPIs; median-based average ticket after outlier removal |
| 3. Conversion funnel | Users counted only if they completed the full sequence, validated in chronological order |
| 4. Cohort retention | Weekly retention by monthly cohort |
| 5. A/B test | Two-proportion z-test on a proposed checkout redesign |
| 6. Executive dashboard | Power BI dashboard with KPIs, category/product comparisons, and drill-through detail |

### Tools

| Stage | Tool | Detail |
|---|---|---|
| Cleaning & EDA | Python | pandas, numpy |
| Exploratory visualization | Python | matplotlib, seaborn |
| Statistical test | Python | statsmodels (two-proportion z-test) |
| Funnel & cohorts | SQL (PostgreSQL) | CTEs, JOINs, CASE WHEN, date functions |
| Dashboard | Power BI | DAX measures, conditional formatting, drill-through, button navigation |

### Files
