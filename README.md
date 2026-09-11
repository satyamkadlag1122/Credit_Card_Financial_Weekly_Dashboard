# 💳 Credit Card Financial Weekly Dashboard

An end-to-end **Power BI Data Analytics project** that transforms credit card customer and transaction data into an interactive financial dashboard.

The dashboard provides insights into **revenue, transactions, customer behavior, activation rate, spending patterns, and financial performance**.

---

## 🚀 Live Project

🔗 **[View Live Power BI Dashboard](https://app.powerbi.com/reportEmbed?reportId=0899b516-01ab-49a3-8dbd-af759877548d&autoAuth=true&ctid=56c1d497-700b-49cf-8f8d-3dd6b20d522f)**

> Click the link above to open the interactive Power BI dashboard.

---


## Project Objective

To develop a comprehensive credit card dashboard that provides real-time insights into key performance metrics and trends — enabling stakeholders to monitor and analyze credit card operations effectively on a recurring (weekly) basis.

## Tech Stack

- **SQL (MySQL)** — data storage and structuring
- **Power BI** — data modeling, DAX measures, and dashboard design
- **DAX** — calculated columns and time-intelligence measures

## 📂 Project Structure
```text
Credit_Card_Financial_Weekly_Dashboard
│
├── 📁 data
│   ├── cc_add.csv
│   ├── credit_card.csv
│   ├── cust_add.csv
│   └── customer.csv
│
├── 📁 images
│   ├── credit_card_customer_report.png
│   └── credit_card_transaction_report.png
│
├── 📊 Credit_Card_Financial_Weekly_Dashboard.pbix
├── 🗄️ credit_card financial_dashboard_data.sql
└── 📄 README.md
```
## Data Pipeline

1. Raw data (`credit_card.csv`, `customer.csv`) prepared and loaded into a **MySQL database** using `credit_card financial_dashboard_data.sql`
2. Power BI connected directly to the SQL database
3. Data modeled with a relationship on `Client_Num` between the transaction and customer tables
4. **DAX** used to build calculated columns (age/income segmentation) and measures (revenue, week-over-week comparisons)
5. Two report pages designed for two different stakeholder audiences

## Dashboard Pages

### 1. Transaction Report
Tracks overall business performance — revenue, transaction volume, and spending patterns.


[![Transaction Report](./images/credit_card_transaction_report.png)](./images/credit_card_transaction_report.png)

**Key visuals:**
- Revenue, Total Interest, Transaction Amount, Transaction Count KPIs
- Quarterly revenue & transaction count trend
- Revenue by card category, expenditure type, education level, customer job, and payment channel (swipe/chip/online)

### 2. Customer Report
Profiles the customer base — who they are and how they contribute to revenue.

[![Customer Report](./images/credit_card_customer_report.png)](./images/credit_card_customer_report.png)


**Key visuals:**
- Revenue, Total Interest, Income, and CSS (Customer Satisfaction Score) KPIs
- Weekly revenue trend by gender
- Revenue by age group, marital status, income group, dependents, education level, and top 5 states

## Key DAX Measures

**Customer segmentation (calculated columns):**
```dax
AgeGroup = SWITCH(TRUE(),
    'cust_detail'[Customer_Age] < 30, "20-30",
    'cust_detail'[Customer_Age] >= 30 && 'cust_detail'[Customer_Age] < 40, "30-40",
    'cust_detail'[Customer_Age] >= 40 && 'cust_detail'[Customer_Age] < 50, "40-50",
    'cust_detail'[Customer_Age] >= 50 && 'cust_detail'[Customer_Age] < 60, "50-60",
    'cust_detail'[Customer_Age] >= 60, "60+",
    "unknown"
)

IncomeGroup = SWITCH(TRUE(),
    'cust_detail'[Income] < 35000, "Low",
    'cust_detail'[Income] >= 35000 && 'cust_detail'[Income] < 70000, "Med",
    'cust_detail'[Income] >= 70000, "High",
    "unknown"
)
```

**Revenue & week number (calculated columns):**
```dax
Revenue = 'cc_detail'[Annual_Fees] + 'cc_detail'[Total_Trans_Amt] + 'cc_detail'[Interest_Earned]

week_num2 = WEEKNUM('cc_detail'[Week_Start_Date])
```

**Week-over-week revenue (measures):**
```dax
Current_week_Revenue = CALCULATE(
    SUM('cc_detail'[Revenue]),
    FILTER(ALL('cc_detail'), 'cc_detail'[week_num2] = MAX('cc_detail'[week_num2]))
)

Previous_week_Revenue = CALCULATE(
    SUM('cc_detail'[Revenue]),
    FILTER(ALL('cc_detail'), 'cc_detail'[week_num2] = MAX('cc_detail'[week_num2]) - 1)
)

WoW_Revenue = DIVIDE(
    [Current_week_Revenue] - [Previous_week_Revenue],
    [Previous_week_Revenue]
)
```

## Key Insights

- **Total revenue: 57M**, with 45.5M from transactions and 8M from interest earned
- **Card tier concentration:** Blue + Silver cards drive ~93% of total revenue (Blue alone contributes 47M) — Gold and Platinum together make up less than 7%
- **Channel usage:** Swipe is the dominant payment method (36M), while online payments remain the smallest channel (4M) — a potential digital-adoption opportunity
- **Spending patterns:** Bills is the top expenditure category (14M), ahead of discretionary categories like Travel (6M)
- **High-value segments:** Businessmen (18M) and Graduates (23M) are the highest-contributing job and education segments respectively
- **Customer profile:** Revenue skews toward middle-aged (40–50), high-income, and married customers
- **Geographic concentration:** Texas, New York, and California together contribute over two-thirds of total revenue
- **Risk indicators:** ~57.5% card activation rate and ~6.06% delinquency rate, both worth monitoring by segment

## How to Reproduce

1. Set up a MySQL database and run `credit_card financial_dashboard_data.sql` to create tables and import the CSVs from the `data/` folder
2. Open `Credit_Card_Financial_Weekly_Dashboard` in Power BI Desktop
3. Update the SQL Server connection details (Home → Transform Data → Data Source Settings) to point to your own database
4. Refresh the data to load it into the report

## Possible Future Improvements

- Build a customer profitability view comparing acquisition cost against revenue earned
- Add drill-through pages for deeper segment-level analysis
- Set up scheduled refresh for real-time/near-real-time reporting

