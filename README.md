# Customer Retention & Churn Analysis: Telecom Subscribers

**Future Interns · Data Science & Analytics · Task 2**
Intern: Viniska K S · CIN: FIT/OCT26/DS23848

---

## 1. Business problem
A telecom company sells phone and internet subscriptions. Roughly 1 in 4 customers leave, and the business needs to know:

1. Why are customers leaving?
2. Which customer segments are most likely to churn?
3. How long do customers typically stay?
4. What actions can improve retention, and what are they worth?

## 2. Data
- **Source:** [Telco Customer Churn (Kaggle)](https://www.kaggle.com/datasets/blastchar/telco-customer-churn), a sample dataset originally published by IBM
- **Size:** 7,043 customers, 21 columns (contract, services, billing, tenure, monthly charges, churn flag)
- **Snapshot dataset:** each customer appears once. `tenure` is months as a customer, and `Churn` says whether they left.
- **Not in the data:** signup dates and churn reasons. Cohorts are therefore built from **contract type × tenure band**, and "reasons" are inferred from patterns and a model. The currency is not stated, so I use **$** as an assumption.

## 3. Tools
Python (pandas, NumPy, scikit-learn, Matplotlib) in Google Colab · GitHub

## 4. Data preparation
| # | What I did | Why |
|---|---|---|
| 1 | Checked duplicates: 0 duplicate customers | Data integrity |
| 2 | Converted `TotalCharges` to a number. 11 blanks were set to 0. | They are brand-new customers (tenure 0) who have not been billed yet |
| 3 | Created `ChurnFlag` (1 = left, 0 = stayed) | Needed for rates and modelling |
| 4 | Grouped tenure into bands: 0–6, 7–12, 13–24, 25–48, 49–72 months | Cohort analysis |
| 5 | Counted add-on services per customer | Segment analysis |
| 6 | Labelled senior citizens as Yes/No | Readable charts |

## 5. Key numbers
| KPI | Value |
|---|---|
| Customers | 7,043 |
| Churned | 1,869 (**26.54%**) |
| Monthly recurring revenue (MRR) | $456,117 |
| MRR lost to churn | **$139,131 (30.5% of MRR)** |
| Average monthly charge, churned vs stayed | $74.44 vs $61.27 |
| Average tenure, churned vs stayed | 18.0 vs 37.6 months |

Churned customers pay **more** than loyal ones, so revenue churn (30.5%) is higher than customer churn (26.5%).

## 6. Insights

### Who churns
1. **Contract type is the biggest factor.** Churn is **42.7%** on month-to-month contracts, **11.3%** on one-year and **2.8%** on two-year, about 15× between the extremes. Month-to-month customers are 55% of the base but account for **86.9% of lost revenue**.
2. **Fibre optic customers churn at 41.9%**, versus 19.0% for DSL and 7.4% for customers with no internet. Fibre customers also pay the most ($91.50 per month on average).
3. **Electronic check payers churn at 45.3%**, versus 15.2% for credit card autopay and 16.7% for bank transfer autopay. Paperless billing is 33.6% versus 16.3% for paper.
4. **Support services go with loyalty.** Among internet customers, those with Tech Support churn at 15.2% (vs 41.6% without), and those with Online Security at 14.6% (vs 41.8%). This is an association, not proof of cause.
5. **Senior citizens churn at 41.7%**, versus 23.6% for others.

### How long customers stay (customer lifetime)
6. **Retention curves:** after 12 months, **70.3%** of month-to-month customers are still active, against 99.1% on one-year and 100% on two-year. After 36 months it is **49.1%**, 95.9% and 99.9%. Only 29.7% of month-to-month customers remain at 60 months.
7. **The early-churn cliff:** **55.5% of all churners leave within their first 12 months**, and 41.9% within 6. 380 customers left in their very first month. Churn is 52.9% in the 0–6 month cohort and falls to 9.5% for customers with 49–72 months.
8. **Cohort view (contract × tenure):** new month-to-month customers (0–6 months) churn at **55.2%**. Even month-to-month customers with 4+ years still churn at 26.0%. Two-year customers stay close to 0–3% in every cohort.
9. **The riskiest profile:** month-to-month + fibre optic + first year. That is 916 customers (13.0% of the base), and **70.2% of them churn**.

### Predicting churn
10. A logistic regression model reaches **AUC 0.845** on unseen customers. The largest churn drivers are fibre optic (+1.18), streaming add-ons, paperless billing and electronic check. The largest protective factors are a two-year contract (−1.26), longer tenure (−0.79), a one-year contract (−0.69) and online security.
11. **530 active customers have a predicted churn risk of 50% or more**, worth **$43,043 per month**. The model puts the expected monthly revenue at risk across all 5,174 active customers at about $67,534. A ranked "who to call first" list is in `at_risk_customers.csv`.

## 7. Recommendations

| # | Recommendation | Evidence | Estimated impact (scenario) |
|---|---|---|---|
| 1 | **Move month-to-month customers to 12-month plans** with a discount or a free add-on | Month-to-month churn is 42.7% vs 11.3% on one-year and drives 86.9% of lost revenue | If 10% of month-to-month customers (387) switch and churn like one-year customers: **122 customers kept, about $8,079 per month ($96,947 per year)** |
| 2 | **Fix the first year.** Add onboarding, a check-in call at months 1 to 3, and a bundled support offer for new fibre customers. | 55.5% of churners leave within 12 months, and new month-to-month fibre customers churn at 70.2% | If first-year churn falls by 10% (104 customers) at the average first-year churner's $66.49 bill: **about $6,915 per month (about $83k per year)** |
| 3 | **Move electronic-check payers to autopay**, with a small incentive | 45.3% churn on electronic check vs about 16.0% on autopay | If 10% of electronic-check customers (236) move to autopay: **69 customers kept, about $5,274 per month ($63k per year)** |
| 4 | **Run a save campaign on the 530 highest-risk active customers** (`at_risk_customers.csv`) | $43,043 per month sits with customers the model scores at 50% risk or higher | If outreach keeps 20% of that revenue: **about $8,609 per month ($103k per year)** |

*Impact figures are scenarios built from observed rates. They assume that a customer who changes contract or payment method would then behave like current customers in that group, which is not guaranteed. They are not forecasts, and the estimates overlap, so they should not be added together.*

## 8. Charts

![Churn by contract](charts/01_churn_by_contract.png)
![Retention curves](charts/02_retention_curves.png)
![Cohort heatmap](charts/03_cohort_heatmap.png)
![Early churn cliff](charts/04_early_churn_cliff.png)
![Churn drivers](charts/05_churn_drivers.png)
![Other segments](charts/06_other_segments.png)

## 9. Repository structure
```
FUTURE_DS_02/
├── README.md
├── 02_churn_analysis.ipynb      (full analysis: cleaning, cohorts, retention curves, model, charts)
├── FUTURE_DS_02_outputs.zip     (summary tables, at-risk customer list and charts)
└── charts/                      (6 PNG charts used above)
```
**To reproduce:** download the dataset from Kaggle, open the notebook in Google Colab, upload the zip, and run the cells in order.

## 10. Limitations
- The data is a **snapshot** with no signup dates, so cohorts use contract and tenure rather than signup month.
- There is **no churn-reason field**. Drivers come from patterns and a model, and they show **association, not causation**.
- The model scores active customers using a model trained on all customers, so the call list is a prioritisation tool, not a validated forecast.
- Retention curves use the Kaplan–Meier method, treating customers who did not churn as still active at their current tenure.
- Currency is not stated in the dataset, and I assumed $.
- This is a single sample dataset. Real results would need the company's own data.
