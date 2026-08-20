# End-to-End Banking Risk Data Analytics Project & Dashboard

An end-to-end data analytics project that explores retail/institutional banking client data to understand lending risk, and presents the findings through an interactive **Power BI** dashboard. The workflow covers data cleaning, exploratory data analysis (EDA) in Python, DAX-based KPI modelling, and a multi-page Power BI report.

---

## Problem Statement

Banks and financial institutions need to understand the risk profile of their clients before extending credit. This project develops a foundational understanding of **risk analytics in banking**, exploring how client, account, and lending data can be used to minimize the risk of loss while lending money to customers.

## Solution

A Power BI dashboard was built on top of the cleaned banking dataset to help decision-makers evaluate an applicant's profile — deposits, loans, credit card balances, income band, and more — to support decisions such as whether a loan should be approved. Python (Pandas, Seaborn, Matplotlib) was used beforehand for exploratory data analysis to understand distributions, categorical breakdowns, and correlations across financial variables.

---

## Repository Structure

```
├── datasets/
│   ├── banking-clients.csv          # Core client-banking fact table (3000 clients)
│   ├── banking-realtionships.csv    # Banking Relationship dimension (Retail, Institutional, Private Bank, Commercial)
│   ├── gender.csv                   # Gender dimension
│   └── investment-advisiors.csv     # Investment Advisor dimension
├── clients.csv                      # Gender lookup (duplicate of datasets/gender.csv)
├── db_creation.sql                  # MySQL script to create the banking_case database
├── BankEDA.ipynb                    # Python EDA notebook (Pandas/Seaborn/Matplotlib)
├── Banking Dashboard.pbix           # Power BI dashboard file
├── Banking Report.docx              # Written project report (problem statement, DAX measures, conclusion)
├── Banking.pptx                     # Presentation summarizing KPIs and results
├── Designs.pptx                     # Dashboard design drafts/mockups
└── README.md
```

## Dataset

The data is modelled as a small star schema with one fact table and three dimension tables, joined via `BRId`, `GenderId`, and `IAId`.

| Table | Description |
|---|---|
| **Clients - Banking** (`banking-clients.csv`) | Fact table — one row per client. Includes demographics (Age, Nationality, Occupation, Gender), banking relationship, income, superannuation savings, credit card holdings/balance, bank loans, deposits, checking/savings/foreign currency accounts, business lending, properties owned, and risk weighting. |
| **Banking Relationship** (`banking-realtionships.csv`) | Client segment: Retail, Institutional, Private Bank, Commercial. |
| **Gender** (`gender.csv`) | Gender lookup (Male/Female). |
| **Investment Advisor** (`investment-advisiors.csv`) | 22 investment advisors mapped to clients. |

`db_creation.sql` creates a MySQL database (`banking_case`) that the raw CSVs can be loaded into for querying before/alongside the Power BI model.

---

## Data Cleaning & Feature Engineering

Performed in Power BI's Power Query editor prior to modelling:

- Created **Engagement Timeframe** — a readable timeline of how long each client has been with the bank.
- Created **Engagement Days** — number of days since the client's join date.
- Created **Income Band** — bucketed `Estimated Income` into **Low** (< 100,000), **Mid** (< 300,000), and **High** tiers.
- Created **Processing Fees** — derived from `Fee Structure` (e.g., a "High" fee structure maps to a 5% processing fee).

## Key DAX Measures

| Measure | DAX |
|---|---|
| Total Clients | `DISTINCTCOUNT('Clients - Banking'[Client ID])` |
| Bank Loan | `SUM('Clients - Banking'[Bank Loans])` |
| Business Lending | `SUM('Clients - Banking'[Business Lending])` |
| Total Loan | `[Bank Loan] + [Business Lending] + [Credit Cards Balance]` |
| Bank Deposit | `SUM('Clients - Banking'[Bank Deposits])` |
| Checking Accounts | `SUM('Clients - Banking'[Checking Accounts])` |
| Savings Account | `SUM('Clients - Banking'[Saving Accounts])` |
| Foreign Currency Account | `SUM('Clients - Banking'[Foreign Currency Account])` |
| Total Deposit | `[Bank Deposit] + [Savings Account] + [Foreign Currency Account] + [Checking Accounts]` |
| Total CC Amount | `SUM('Clients - Banking'[Amount of Credit Cards])` |
| Credit Cards Balance | `SUM('Clients - Banking'[Credit Card Balance])` |
| Total Fees | `SUMX('Clients - Banking', [Total Loan] * 'Clients - Banking'[Processing Fees])` |
| Engagement Length | `SUM('Clients - Banking'[Engagment Days])` |

---

## Exploratory Data Analysis (`BankEDA.ipynb`)

The notebook uses Pandas, Seaborn, and Matplotlib to explore the `banking-clients` dataset:

- **Univariate analysis** — distribution plots (histograms/count plots) for Income Band, Banking Relationship, Gender, Investment Advisor, Nationality, Occupation, Fee Structure, Loyalty Classification, Properties Owned, and Risk Weighting.
- **Bivariate analysis** — category counts broken down by Nationality.
- **Numerical analysis** — distribution of Estimated Income, Superannuation Savings, Credit Card Balance, Bank Loans, Bank Deposits, Checking/Saving Accounts, Foreign Currency Account, and Business Lending.
- **Correlation heatmap** across all numerical financial variables.

**Key insight:** the strongest positive correlations are between **Bank Deposits** and **Checking Accounts**, **Saving Accounts**, and **Foreign Currency Account** — clients who hold large balances in one account type tend to hold substantial balances across other account types as well.

---

## Dashboard (`Banking Dashboard.pbix`)

A four-page interactive Power BI report:

1. **Home** — landing page with top-level KPIs (Total Clients, Total Engagement Account, Total CC Account) and navigation.
2. **Loan Analysis** — breakdown of Total Loan, Bank Loan by Income Band, and Bank Loan by Nationality.
3. **Deposit Analysis** — Total Deposit, Checking Amount vs. Saving Amount, and Total Fees.
4. **Summary Dashboard** — consolidated view combining loan and deposit metrics with financial goals tracking.

## Conclusion

Power BI dashboards, built on the cleaned banking dataset and DAX measures above, provide an effective way to surface key banking metrics and KPIs, giving stakeholders a fast, visual understanding of client lending and deposit behavior.

## Future Work

- Surface total loan amount and related metrics per investor at a glance.
- Compare client volume across banking relationship types (Private Bank currently has the highest client count) to inform other segments' growth strategy.
- Identify which nationalities carry the highest bank loans.
- Expand reporting on the distribution of balances across account types by investor.

---

## Tech Stack

- **Python** — Pandas, NumPy, Matplotlib, Seaborn (EDA)
- **SQL** — MySQL (data staging)
- **Power BI** — Power Query (data cleaning), DAX (measures), interactive report/dashboard
- **Jupyter Notebook**

## Getting Started

1. Clone the repository.
2. (Optional) Run `db_creation.sql` in MySQL to create the `banking_case` database, then load the CSVs from `datasets/`.
3. Open `BankEDA.ipynb` in Jupyter to reproduce the EDA (requires `pandas`, `matplotlib`, `seaborn`).
4. Open `Banking Dashboard.pbix` in Power BI Desktop to explore the interactive dashboard.

## Author

Created as part of a data analytics portfolio project on banking risk analysis.
