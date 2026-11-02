# CreditCheck: Credit Risk Analytics & Portfolio Monitoring

An end-to-end BFSI analytics project that uses **MySQL and Power BI** to analyze a synthetic loan portfolio, evaluate repayment behavior, identify credit risk patterns, and monitor lending performance across branches, regions, and borrower segments.

## 1. Business Problem

Financial institutions need to continuously monitor their loan portfolios to identify repayment issues, assess default exposure, and understand the factors associated with borrower risk.

This project simulates a banking environment and addresses the following business questions:

- How large is the loan portfolio, and what proportion is exposed to default risk?
- Which branches and regions contribute the most to portfolio risk?
- How do credit scores, income levels, and loan types relate to repayment behavior?
- How are delinquency and default rates distributed across borrower segments?
- Which loan categories contribute the highest financial losses?
- How does portfolio performance change over time?

**Objective:** Build a SQL-driven analytical layer and an interactive Power BI dashboard to support portfolio monitoring and data-driven credit risk assessment.

## 2. Dataset Overview

The project uses a synthetic banking dataset consisting of five relational tables.

| Table             |  Records | Description                                                     |
| ----------------- | -------: | --------------------------------------------------------------- |
| Branches          |       25 | Branch details, cities, regions, and managers                   |
| Customers         |    5,000 | Borrower demographics, income, and credit scores                |
| Loan Applications |   7,500+ | Loan applications, including approved and rejected applications |
| Loans             |    6,113 | Disbursed loans and associated loan information                 |
| Repayments        | 153,000+ | Individual EMI repayment records                                |

**Total Loan Portfolio Value:** ₹7.37 billion

The dataset captures the relationships between customers, loan applications, disbursed loans, branches, and repayment transactions.

## 3. Technology Stack

| Technology             | Application                                    |
| ---------------------- | ---------------------------------------------- |
| MySQL 8.0+             | Relational data analysis and SQL querying      |
| Power BI               | Interactive dashboard development              |
| DAX                    | KPI calculations and analytical measures       |
| Power BI Data Modeling | Star-schema design and relationship management |

## 4. Analytical Workflow

The project follows a SQL-to-BI workflow:

1. **Database Setup:** Created relational tables with appropriate data types, primary keys, foreign keys, and indexes.
2. **Data Loading:** Imported the five synthetic datasets into MySQL while maintaining referential integrity.
3. **SQL Analysis:** Developed 15 analytical queries using joins, CTEs, aggregations, and window functions to investigate portfolio performance and risk patterns.
4. **Data Modeling:** Designed a star-schema model in Power BI, using repayments as the central fact table and related entities as dimensions.
5. **Dashboard Development:** Created DAX measures and calculated columns to support portfolio monitoring, borrower segmentation, and branch-level comparisons.

## 5. SQL Analysis

Developed 15 SQL queries organized around key credit risk and portfolio monitoring requirements.

**Key SQL concepts implemented:**

- **CTEs:** Structured multi-step analytical queries and intermediate aggregations.
- **Window Functions:** Used `RANK()`, `DENSE_RANK()`, and `LAG()` for branch rankings and month-over-month default rate comparisons.
- **Joins:** Combined customer, loan, branch, and repayment information across multiple relational tables.
- **Conditional Aggregations:** Evaluated repayment performance and default exposure across different borrower segments.
- **NULL Handling:** Used `LEFT JOIN` and `COALESCE()` to retain loans with zero missed payments in delinquency analysis.
- **HAVING:** Identified customers holding multiple loans to examine portfolio concentration risk.

**Analysis Areas:**

- Portfolio size and disbursement trends
- Loan-type and regional performance
- EMI repayment and delinquency patterns
- Credit score and income-based risk segmentation
- Branch-level default and approval rankings
- Month-over-month default rate movements

## 6. Power BI Dashboard

Developed a two-page interactive Power BI dashboard supported by a star-schema data model.

**Data Modeling**

- Repayments configured as the central fact table.
- Customers, loans, branches, and other relevant entities modeled as dimensions.
- Single-direction relationships to support consistent filtering and aggregation.
- Dedicated Dates table with an Indian financial-year calendar (April–March).

**DAX Implementation**

- 23 analytical measures covering portfolio and repayment KPIs.
- 7 calculated columns for credit score bands, income bands, delinquency buckets, and financial quarters.
- `RANKX` for branch-level default rankings.
- `SUMX` for row-level interest income calculations.
- Layered measures for calculating financial losses and recovered amounts.

**Interactive Filters**

- Region
- Branch
- Financial Quarter
- Loan Status
- Year Range

## 7. Key Business Insights

The analysis revealed several patterns across the synthetic loan portfolio:

**1. Credit Score and Default Risk**

Default rates declined from **8.11% for borrowers with credit scores below 600 to 2.20% for borrowers with scores above 800**, indicating a strong relationship between credit score and repayment outcomes in the dataset.

**2. Loan-Type Loss Exposure**

Business loans contributed the highest absolute losses from defaults, despite home loans accounting for a larger share of the overall portfolio value. This highlights the importance of evaluating financial exposure rather than relying exclusively on portfolio volume.

**3. Income and Repayment Behavior**

Missed EMI rates declined consistently across higher income brackets, indicating a relationship between borrower income and repayment performance.

**Business Implication:** Credit risk monitoring should incorporate borrower-level characteristics, loan-type exposure, and branch-level performance rather than relying on aggregate portfolio metrics alone.

## 8. Assumptions and Limitations

- The project uses synthetic data and does not represent an actual financial institution or its customers.
- Outstanding portfolio value is approximated using the full loan amount for loans that have not been closed. The dataset does not provide an amortized outstanding principal balance.
- Credit score and income findings represent associations observed in the synthetic dataset, not causal relationships.
- The project focuses on descriptive and diagnostic analytics; it does not implement a predictive credit scoring or default classification model.

## 9. Repository Structure

```text
CreditCheck/
│
├── schema.sql                 # Database schema and indexes
├── queries.sql                # 15 analytical SQL queries
├── dax_measures.md             # DAX measures and calculated columns
├── CreditCheck.pbix            # Interactive Power BI dashboard
│
└── dashboard_screenshots/      # Dashboard screenshots
```

## 10. Getting Started

### Prerequisites

- MySQL 8.0 or later
- Power BI Desktop
- The five source CSV datasets

### Database Setup

**Step 1: Create the database and tables**

Execute `schema.sql` in MySQL to create the required database structure, tables, and indexes.

**Step 2: Import the datasets**

Load the five CSV files into their corresponding tables in the following order:

1. `branches`
2. `customers`
3. `loan_applications`
4. `loans`
5. `repayments`

Maintain this order to satisfy the foreign key dependencies.

**Step 3: Execute SQL analysis**

Run `queries.sql` to reproduce the portfolio, repayment, delinquency, and credit risk analyses.

**Step 4: Open the Power BI dashboard**

Open `CreditCheck.pbix` in Power BI Desktop and configure the MySQL connection to your local database to refresh the report.

Refer to `dax_measures.md` for the definitions and explanations of the dashboard calculations.

---

## Project Summary

CreditCheck demonstrates the application of relational database analysis, advanced SQL querying, dimensional data modeling, and Power BI reporting to a simulated BFSI credit risk use case.

The project focuses on transforming transactional loan and repayment data into actionable insights for portfolio monitoring and risk assessment.
