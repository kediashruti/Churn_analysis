
# Customer Churn Analysis & Customer Intelligence

This project focuses on analyzing customer churn for an OTT subscription platform. It looks at customer details, subscription plans, support interactions, revenue, and churn risk.

The project uses customer, subscription, and support data stored in a SQLite database. Python, SQL, Pandas, NumPy, Matplotlib, and Seaborn are used for data cleaning, analysis, and visualization.

## 📌 Project Overview

Customer churn is an important problem for subscription-based businesses. This project analyzes the customer data to understand:

- **Who** is churning
- **Why** customers may be leaving
- **When** churn is happening
- Which plans and customer groups have higher churn
- How churn affects revenue and Customer Lifetime Value (CLTV)
- Which customers may need to be targeted for retention

The project covers the complete analysis process, starting with data extraction and cleaning, followed by feature engineering, data integration, KPI analysis, and visualization.

## 🎯 Business Objectives

The main objectives of this project are:

1. Calculate the overall customer churn rate.
2. Calculate the customer retention rate.
3. Analyze churn across different subscription plans.
4. Analyze churn by state.
5. Analyze churn by subscription type.
6. Compare revenue and customer counts across different segments.
7. Calculate Average Revenue Per User (ARPU).
8. Calculate average customer tenure.
9. Calculate revenue associated with churned customers.
10. Analyze customer complaints and support escalations.
11. Check the relationship between support escalation and churn.
12. Divide customers into different churn-risk groups.
13. Identify high-risk customer segments.
14. Provide recommendations that can help improve customer retention.

## 🗄️ Database Structure

The project uses a SQLite database:

`customer_churn.db`

The database contains three main tables:

### 1. `db_customer`

This table contains customer information.

| Column | Description |
|---|---|
| customerid | Unique customer identifier |
| name | Customer name |
| country | Customer country |
| state | Customer state |
| gender | Customer gender |
| dob | Date of birth |
| interests | Customer interests |
| pincode | Customer pincode |

### 2. `db_subscription`

This table contains subscription and financial information.

| Column | Description |
|---|---|
| customerid | Unique customer identifier |
| subscription_start_date | Subscription start date |
| subscription_type | Subscription type |
| renewal_date | Renewal date |
| plan_type | Basic / Standard / Premium |
| contract_type | Monthly / Annual |
| cancellation_date | Cancellation date |
| cancellation_reason | Reason for cancellation |
| monthly_charges | Monthly subscription charges |
| cltv | Customer Lifetime Value |
| churn_score | Customer churn score |

### 3. `db_support`

This table contains customer support information.

| Column | Description |
|---|---|
| customerid | Unique customer identifier |
| complaint_date | Date of complaint |
| escalations | Support escalation indicator |
| csat_score | Customer satisfaction score |
| comment | Customer support comment |

# 🛠️ Tech Stack

### Programming & Data Analysis

- Python
- Pandas
- NumPy

### Database

- SQLite
- SQL
- Python `sqlite3`

### Data Visualization

- Matplotlib
- Seaborn

### Environment

- Jupyter Notebook

# 🔄 Project Workflow

```text
SQLite Database
       ↓
SQL Data Extraction
       ↓
Python + Pandas
       ↓
Data Inspection
       ↓
Data Cleaning
       ↓
Feature Engineering
       ↓
Data Integration
       ↓
KPI Analysis
       ↓
Churn Risk Segmentation
       ↓
Data Visualization
       ↓
Business Insights
````

# 1. 📥 Data Extraction

Python is connected to the SQLite database using:

```python
conn = sqlite3.connect('customer_churn.db')
```

SQL is used to check the tables available in the database.

The tables are then loaded into Pandas DataFrames using:

```python
pd.read_sql()
```

The following DataFrames are created:

```text
df_db_customer
df_db_subscription
df_db_support
```

This makes it possible to use SQL for data extraction and Pandas for further analysis.

# 2. 🧹 Data Cleaning

Different cleaning steps are performed on the three tables.

### Customer Data

The customer table is cleaned by:

* Removing unnecessary `interests` and `pincode` columns
* Renaming `name` to `customer_name`
* Converting `dob` to datetime
* Converting categorical columns to suitable data types
* Standardizing gender values
* Handling missing country values

For example:

```text
Men → Male
Women → Female
```

Missing country values are filled using the corresponding state information.

### Subscription Data

The subscription table is cleaned by:

* Converting date columns to datetime
* Converting categorical columns to suitable data types
* Preparing the data for churn analysis

The main date columns are:

```text
subscription_start_date
renewal_date
cancellation_date
```

### Support Data

The support table is cleaned by:

* Removing unnecessary columns
* Converting `complaint_date` to datetime
* Standardizing data types
* Checking for multiple support records for the same customer

# 3. 🔧 Feature Engineering

New columns are created from the existing data to make the analysis easier.

## Churn Flag

A `churn flag` is created using the cancellation date:

```python
#create a new col using existing col- churn flag
df_db_subscription['churn flag'] = np.where(df_db_subscription['cancellation_date'].notna(),1,0)
```

The logic is:

```text
Cancellation date exists → 1 → Churned

Cancellation date does not exist → 0 → Retained
```

This converts churn into a numerical value that can be used for further analysis.

---

## Complaint Count

A `complaint_count` column is created using:

```python
groupby('customerid')
```

and:

```python
transform('count')
```

This gives the number of support records associated with each customer.

---

## Customer Tenure

Customer tenure is calculated using the subscription start date and cancellation date.

For churned customers:

```text
Cancellation Date − Subscription Start Date
```

For customers who have not churned:

```text
Current Date − Subscription Start Date
```

The result is stored in:

```text
tenure_days
```

---

## Churn Risk

Customers are divided into three groups based on their churn score:

| Churn Score | Risk   |
| ----------- | ------ |
| < 50        | Low    |
| 50–69       | Medium |
| > 70        | High   |

This helps identify customers who may need attention from the retention team.

# 4. 🔗 Data Integration

The three tables are connected using:

```text
customerid
```

The final dataset contains:

```text

df=(df_db_subscription
    .merge(df_db_customer, on = 'customerid', how = 'left')
    .merge(df_db_support, on = 'customerid', how = 'left'))

```

A left join is used so that subscription records remain the main dataset and customer and support information are added where available.

Multiple support records can exist for the same customer. To avoid duplicate customer records after merging, the following steps are performed:

1. Calculate the complaint count.
2. Sort support records by complaint date.
3. Keep the latest support record for each customer.
4. Merge the three datasets.

This creates the final customer-level dataset used for analysis.

---

# 📊 Key Performance Indicators

The project calculates the following KPIs:

| KPI                                 | Description                                                |
| ----------------------------------- | ---------------------------------------------------------- |
| **Churn Rate**                      | Percentage of customers who churned                        |
| **Retention Rate**                  | Percentage of customers retained                           |
| **Churn by Plan**                   | Churn rate across Basic, Standard and Premium plans        |
| **Churn by State**                  | Churn rate across different states                         |
| **Churn by Subscription Type**      | Churn rate across subscription types                       |
| **ARPU**                            | Average monthly revenue per user                           |
| **Average Tenure**                  | Average number of days customers use the service           |
| **Revenue at Risk**                 | Monthly charges associated with churned customers          |
| **Escalation Rate**                 | Percentage of records associated with escalations          |
| **Average Complaints per User**     | Average number of support complaints per customer          |
| **Escalation vs Churn Correlation** | Relationship between support escalation and churn          |
| **Churn Risk**                      | Classification of customers into Low, Medium and High risk |

# 📈 Key Results

### Overall Churn

**Churn Rate: 28.6%**

**Retention Rate: 71.4%**

Around 28.6% of the analyzed customers had churned.

### Plan-Level Churn

The analysis shows that the **Basic plan had the highest number of churned customers**.

Churn is also compared across:

```text
  plan_type  churn_rate_pct
0     Basic           60.00
1   Premium           14.29
2  Standard           22.22
```

using Pandas `groupby()` and aggregation.

### Geographic Churn

The analysis identified:

* **Karnataka** as the state with the highest churn.
* **September 2024** as the month with the highest observed churn.

The state-level analysis also includes:

* Total monthly charges
* Number of unique customers
* Churn rate

### Revenue & Customer Value

| Metric                    |         Result |
| ------------------------- | -------------: |
| Total Revenue             |        **395** |
| Revenue Loss Due to Churn |         **74** |              
| Revenue Loss              |        **18%** |
| ARPU                      |      **₹18.8** |
| Average Tenure            | **1,451 days** |

# 📉 Visualizations

Matplotlib and Seaborn are used to visualize the results.

### Monthly Churn Trend

A time-series chart shows the number of churned customers by cancellation month.

```text
Cancellation Date
        ↓
Cancellation Month
        ↓
Number of Churned Customers
```

### Churn by Plan Type

A bar chart compares churn across:

```text
Basic
Standard
Premium
```

### Churn by State

A bar chart is used to compare churn rates across different states.

### Correlation Heatmap

A correlation heatmap is created after encoding categorical variables.

The analysis includes:

```text
Plan Type
Contract Type
Churn Score
Churn Flag
Escalations
Churn Risk
```

### Pairplot

A Seaborn pairplot is used to see relationships between the selected variables.

### Customer Segmentation

A categorical plot is used to compare:

```text
Plan Type
       +
Monthly Charges
       +
Gender
       +
Churn Risk
```

This helps compare monthly charges across different customer groups and churn-risk categories.

# 🔍 Business Insights

### 1. Basic Plan Customers Represent a Major Churn Segment

The Basic plan had the highest number of churned customers.

However, this does not necessarily mean that it had the highest revenue impact.


### 2. Geographic Concentration

Karnataka had the highest churn among the states analyzed.

Further investigation can focus on:

* Pricing
* Technical problems
* Customer complaints
* Service quality
* Other regional factors


### 3. September 2024 Requires Investigation

September 2024 had the highest observed churn.

Some areas that can be checked for this period are:

* Pricing changes
* Product changes
* Service issues
* Customer complaints
* Competitor activity


### 44. Support Escalations & Churn

The project also looks at the relationship between support escalations and customer churn.

The purpose is to check whether customers with support escalation issues are more likely to churn.

```

