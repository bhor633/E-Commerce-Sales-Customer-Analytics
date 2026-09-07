# E-Commerce-Sales-Customer-Analytics
# E-Commerce Sales & Customer Analytics

## Project Overview

This project demonstrates an end-to-end **Data Analytics workflow** using:

- **Microsoft Excel** – initial data inspection and cleaning
- **PostgreSQL** – data storage, validation, SQL analysis, and business queries
- **Power BI** – data modeling, DAX measures, and interactive dashboards

The objective was to transform a raw e-commerce dataset into a clean analytical dataset and answer important business questions related to **sales, profitability, customers, products, locations, sales channels, payment methods, and order performance**.

---

# 1. Business Problems

Businesses often collect large amounts of sales data but face several problems before they can use it for decision-making.

### Main Problems Identified

1. **Raw and inconsistent data**
   - Missing values
   - Possible duplicate records
   - Inconsistent formatting
   - Data quality issues

2. **No centralized analytical workflow**
   - Raw data was initially stored in Excel.
   - It needed to be moved into a database for structured analysis.

3. **Limited visibility into business performance**
   - Difficult to quickly understand total revenue, profit, orders, customers, and units sold.

4. **Difficulty identifying profitable products and categories**
   - Business teams need to know which categories and products generate the most revenue and profit.

5. **Customer performance was not easily visible**
   - High-value customers and purchasing behavior needed to be identified.

6. **Sales performance needed to be analyzed across multiple dimensions**
   - Time
   - Category
   - Product
   - Customer
   - State
   - City
   - Sales channel
   - Payment method
   - Order status

7. **No interactive reporting layer**
   - Decision-makers needed a dashboard where they could filter and explore the data easily.

---

# 2. What I Did to Solve These Problems

I created an end-to-end analytics pipeline:

```text
Raw Excel Data
      ↓
Data Inspection & Cleaning
      ↓
Clean CSV Dataset
      ↓
PostgreSQL Database
      ↓
SQL Data Validation & Analysis
      ↓
Power BI Connection
      ↓
Data Model & DAX Measures
      ↓
Interactive Business Dashboards
```

This workflow converts raw transactional data into useful business insights.

---

# 3. Project Architecture / Connections

```text
┌──────────────────────┐
│     Excel Dataset    │
│   Raw E-Commerce     │
└──────────┬───────────┘
           │
           │ Data Cleaning
           ▼
┌──────────────────────┐
│   Cleaned Dataset    │
│   Excel / CSV        │
└──────────┬───────────┘
           │
           │ Import / Load
           ▼
┌──────────────────────┐
│     PostgreSQL       │
│    ecommerce_sales   │
└──────────┬───────────┘
           │
           │ SQL Queries
           ▼
┌──────────────────────┐
│ Data Validation &    │
│ Business Analysis    │
└──────────┬───────────┘
           │
           │ PostgreSQL Connection
           ▼
┌──────────────────────┐
│      Power BI        │
│ Data Model + DAX     │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Interactive Business │
│      Dashboards      │
└──────────────────────┘
```

---

# 4. Step 1 — Understanding the Raw Data

The project started with a raw e-commerce dataset containing order-level transactional information.

### Main Columns

- `order_id`
- `order_date`
- `customer_id`
- `product_id`
- `product_name`
- `category`
- `city`
- `state`
- `customer_age`
- `gender`
- `sales_channel`
- `payment_method`
- `quantity`
- `unit_price`
- `discount`
- `sales`
- `cost`
- `profit`
- `order_status`

### Screenshot — Raw Dataset

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/e6bd182564ead7ebd0929e3c2c8a42cbacf3d5de/EXCEL%20DATA/1%20row%20data.png" width="100%">
<br>

---

# 5. Step 2 — Data Cleaning in Excel

The raw dataset was reviewed before loading it into PostgreSQL.

### Data Cleaning Activities

- Checked column names and structure
- Reviewed blank and missing values
- Checked for duplicate records
- Standardized the dataset structure
- Verified numeric columns
- Reviewed order status values
- Reviewed categories and location fields
- Converted the cleaned dataset into CSV format for database loading

### Goal

The goal was to ensure that the dataset was suitable for database storage and analytical queries.

### Screenshot — Cleaned Excel Dataset

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/EXCEL%20DATA/cleard%20data.png" width="100%">

<br>

---

# 6. Step 3 — Loading Data into PostgreSQL

After cleaning, the dataset was prepared for PostgreSQL.

A database table was created to store the e-commerce transactions.

### Example Table Structure

```sql
CREATE TABLE ecommerce_sales (
    order_id VARCHAR(20),
    order_date DATE,
    customer_id VARCHAR(20),
    product_id VARCHAR(20),
    product_name VARCHAR(100),
    category VARCHAR(50),
    city VARCHAR(50),
    state VARCHAR(50),
    customer_age INT,
    gender VARCHAR(20),
    sales_channel VARCHAR(30),
    payment_method VARCHAR(30),
    quantity INT,
    unit_price NUMERIC(12,2),
    discount NUMERIC(5,2),
    sales NUMERIC(14,2),
    cost NUMERIC(14,2),
    profit NUMERIC(14,2),
    order_status VARCHAR(20)
);
```

### Screenshot — PostgreSQL Table Creation

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import.png.png" width="100%">
<br>

---

# 7. Step 4 — Data Quality Checks in SQL

Before performing business analysis, I validated the data using SQL.

### Checks Performed

#### Total Row Count

```sql
SELECT COUNT(*) AS total_rows
FROM ecommerce_sales;
```

#### Duplicate Order IDs

```sql
SELECT
    order_id,
    COUNT(*) AS duplicate_count
FROM ecommerce_sales
GROUP BY order_id
HAVING COUNT(*) > 1
ORDER BY duplicate_count DESC;
```

#### Missing Values

```sql
SELECT
    COUNT(*) FILTER (WHERE order_id IS NULL) AS missing_order_id,
    COUNT(*) FILTER (WHERE order_date IS NULL) AS missing_order_date,
    COUNT(*) FILTER (WHERE customer_id IS NULL) AS missing_customer_id,
    COUNT(*) FILTER (WHERE city IS NULL) AS missing_city,
    COUNT(*) FILTER (WHERE sales IS NULL) AS missing_sales,
    COUNT(*) FILTER (WHERE profit IS NULL) AS missing_profit
FROM ecommerce_sales;
```

#### Invalid Quantity

```sql
SELECT *
FROM ecommerce_sales
WHERE quantity <= 0;
```

#### Invalid Discount Values

```sql
SELECT *
FROM ecommerce_sales
WHERE discount < 0
   OR discount > 0.30;
```

### Screenshot — Data Quality Checks

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import.png.png" width="100%">
<br>

---

# 8. Step 5 — SQL Business Analysis

After validating the dataset, I used SQL to answer important business questions.

---

## 8.1 Overall Business Performance

I calculated:

- Total Revenue
- Total Profit
- Units Sold
- Total Orders
- Total Customers
- Profit Margin

Example:

```sql
SELECT
    SUM(sales) AS total_revenue,
    SUM(profit) AS total_profit,
    SUM(quantity) AS units_sold,
    COUNT(DISTINCT order_id) AS total_orders,
    COUNT(DISTINCT customer_id) AS total_customers,
    ROUND(
        SUM(profit) / NULLIF(SUM(sales), 0) * 100,
        2
    ) AS profit_margin
FROM ecommerce_sales
WHERE order_status = 'Delivered';
```

### Screenshot — Overall Business Analysis

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import2.png.png" width="100%">

<br>

---

## 8.2 Monthly Revenue and Growth Analysis

I analyzed revenue trends over time and calculated month-over-month growth.

SQL concepts used:

- `DATE_TRUNC`
- `CTE`
- `LAG()`
- Window functions
- Aggregations

### Business Questions

- How is revenue changing over time?
- Which months performed better or worse?
- What is the month-over-month growth percentage?

### Screenshot — Monthly Sales Analysis

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import3.png.png" width="100%">

<br>

---

## 8.3 Category Performance

I compared categories using:

- Revenue
- Profit
- Units Sold
- Profit Margin

### Business Question

> Which product categories generate the highest revenue and profit?

### Screenshot — Category Analysis

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import4.png.png" width="100%">

<br>

---

## 8.4 Product Performance

Products were analyzed to identify:

- Top revenue-generating products
- Most profitable products
- Products with high unit sales

### SQL Concepts Used

- `GROUP BY`
- `ORDER BY`
- `LIMIT`
- `SUM()`

### Screenshot — Product Analysis

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import5.png.png" width="100%">

<br>

---

## 8.5 Top Customers

Customer-level analysis was performed to identify high-value customers.

Metrics included:

- Number of orders
- Total revenue
- Total profit

### Business Question

> Which customers contribute the most revenue to the business?

### Screenshot — Customer Analysis
<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import6.png.png" width="100%">
<br>

---

## 8.6 Geographic Analysis

Sales performance was analyzed by:

- City
- State

Metrics included:

- Revenue
- Profit
- Number of orders

### Business Question

> Which locations contribute the most to overall business performance?

### Screenshot — Geographic Analysis

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import6.png.png" width="100%">

<br>

---

## 8.7 Sales Channel Analysis

I compared performance across sales channels such as:

- Marketplace
- Mobile App
- Social Media
- Website

Metrics analyzed:

- Revenue
- Profit
- Number of orders
- Profit Margin

### Screenshot — Sales Channel Analysis
<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import2.png.png" width="100%">

<br>

---

## 8.8 Payment Method Analysis

I analyzed customer payment preferences using:

- Number of orders
- Total revenue

### Business Question

> Which payment methods are most frequently used by customers?

### Screenshot — Payment Method Analysis

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import2.png.png" width="100%">

<br>

---

## 8.9 Order Status Analysis

Order records were analyzed based on different order statuses.

Examples:

- Delivered
- Pending
- Cancelled
- Returned

### Business Question

> How does order status affect overall sales and profitability?

### Screenshot — Order Status Analysis

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/SCRIPT/postgresql_import2.png.png" width="100%">

<br>

---

# 9. Advanced SQL Analysis

Advanced SQL techniques were used to perform deeper analysis.

### Concepts Used

- Common Table Expressions (CTEs)
- Window Functions
- `LAG()`
- `DENSE_RANK()`
- Aggregations
- Conditional Filtering
- Profit Margin Calculations

### Example — Ranking Products Within Categories

```sql
WITH product_sales AS (
    SELECT
        category,
        product_id,
        product_name,
        SUM(sales) AS revenue
    FROM ecommerce_sales
    WHERE order_status = 'Delivered'
    GROUP BY category, product_id, product_name
),
ranked_products AS (
    SELECT
        *,
        DENSE_RANK() OVER (
            PARTITION BY category
            ORDER BY revenue DESC
        ) AS rank
    FROM product_sales
)
SELECT *
FROM ranked_products
WHERE rank <= 3
ORDER BY category, rank;
```



<br>

---

# 10. Step 6 — Connecting PostgreSQL to Power BI

After completing the SQL analysis, PostgreSQL was connected to Power BI.

### Connection Flow

```text
PostgreSQL Server
      ↓
Database: sales_db
      ↓
Schema: public
      ↓
Table: ecommerce_sales
      ↓
Power BI
```

### Steps

1. Open Power BI Desktop.
2. Select **Get Data**.
3. Choose **PostgreSQL Database**.
4. Enter the PostgreSQL server and database details.
5. Select the `ecommerce_sales` table.
6. Load the data into Power BI.
7. Review data types and fields.
8. Create DAX measures.
9. Build interactive dashboards.



<br>

---

# 11. Step 7 — DAX Measures

DAX measures were created to calculate key business metrics.

### Total Revenue

```DAX
Total Revenue =
CALCULATE(
    SUM('public ecommerce_sales'[sales]),
    'public ecommerce_sales'[order_status] = "Delivered"
)
```

### Total Profit

```DAX
Total Profit =
CALCULATE(
    SUM('public ecommerce_sales'[profit]),
    'public ecommerce_sales'[order_status] = "Delivered"
)
```

### Total Orders

```DAX
Total Orders =
CALCULATE(
    DISTINCTCOUNT('public ecommerce_sales'[order_id]),
    'public ecommerce_sales'[order_status] = "Delivered"
)
```

### Total Customers

```DAX
Total Customers =
CALCULATE(
    DISTINCTCOUNT('public ecommerce_sales'[customer_id]),
    'public ecommerce_sales'[order_status] = "Delivered"
)
```

### Units Sold

```DAX
Units Sold =
CALCULATE(
    SUM('public ecommerce_sales'[quantity]),
    'public ecommerce_sales'[order_status] = "Delivered"
)
```

### Profit Margin %

```DAX
Profit Margin % =
DIVIDE(
    [Total Profit],
    [Total Revenue],
    0
)
```

### Average Order Value

```DAX
Average Order Value =
DIVIDE(
    [Total Revenue],
    [Total Orders],
    0
)
```

### Screenshot — DAX Measures

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/DASHBOARDS/powerbi_Dex.png.png" width="100%">

<br>

---

# 12. Step 8 — Power BI Dashboard Development

The final stage was creating interactive dashboards for business users.

The report contains three main analytical sections.

---

## Dashboard 1 — E-Commerce Sales & Customer Analytics

### Focus Areas

- Total Revenue
- Total Profit
- Profit Margin
- Total Orders
- Total Customers
- Average Order Value
- Revenue Trend
- Category Performance
- Product Performance
- State Performance
- Sales Channel Performance

### Interactive Filters

- Order Date
- Category
- State
- Sales Channel

### Dashboard Screenshot

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/DASHBOARDS/powerbi_dashboard1.png.png" width="100%">

<br>

---

## Dashboard 2 — Customer Analytics

### Focus Areas

- Customer Value
- Purchase Behavior
- Customer Segmentation
- Top Customers by Revenue
- Revenue by Gender
- Revenue by Customer Age
- Orders and Revenue by Customer

### Interactive Filters

- Gender
- City
- Category

### Dashboard Screenshot

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/DASHBOARDS/powerbi_dashboard2.png.png" width="100%">

<br>

---

## Dashboard 3 — Product & Profitability Analytics

### Focus Areas

- Product Revenue
- Product Profit
- Profit Margin
- Units Sold
- Revenue and Profit by Category
- Top Products by Profit
- Profit by State
- Discount vs Profit Analysis

### Interactive Filters

- Category
- Sales Channel
- State

### Dashboard Screenshot

<img src="https://raw.githubusercontent.com/bhor633/E-Commerce-Sales-Customer-Analytics/161a0e581e8bc48a5cacd14597b5a1419a917ed2/DASHBOARDS/powerbi_dashboard3.png.png" width="100%">

<br>

---

# 13. Key Skills Demonstrated

### Excel

- Data inspection
- Data cleaning
- Data validation
- Dataset preparation

### PostgreSQL / SQL

- Database design
- Table creation
- Data validation
- Aggregations
- `GROUP BY`
- CTEs
- Window Functions
- `LAG()`
- `DENSE_RANK()`
- Business analysis queries

### Power BI

- PostgreSQL connection
- Data modeling
- DAX measures
- KPI cards
- Interactive filters
- Dashboard design
- Business storytelling

---

# 14. Business Questions Answered

This project helps answer questions such as:

1. What is the total revenue and total profit?
2. What is the overall profit margin?
3. How many orders and customers does the business have?
4. How does revenue change over time?
5. Which categories generate the highest revenue?
6. Which products generate the highest profit?
7. Who are the top customers?
8. Which states and cities generate the most revenue?
9. Which sales channels perform best?
10. Which payment methods are most commonly used?
11. How does discount affect profitability?
12. Which order statuses impact business performance?
13. What are the top-performing products within each category?

---

# 15. Project Workflow Summary

```text
STEP 1
Raw E-Commerce Dataset
        ↓
STEP 2
Excel Data Inspection & Cleaning
        ↓
STEP 3
Export Clean Data to CSV
        ↓
STEP 4
Create PostgreSQL Database & Table
        ↓
STEP 5
Load Dataset into PostgreSQL
        ↓
STEP 6
Perform Data Quality Checks
        ↓
STEP 7
Perform SQL Business Analysis
        ↓
STEP 8
Connect PostgreSQL with Power BI
        ↓
STEP 9
Create DAX Measures
        ↓
STEP 10
Build Interactive Dashboards
        ↓
FINAL OUTPUT
Business Insights & Decision Support
```

---

# 16. Tools Used

| Tool | Purpose |
|---|---|
| Microsoft Excel | Data inspection and cleaning |
| PostgreSQL | Database storage and SQL analysis |
| pgAdmin | PostgreSQL database management |
| Power BI | Data visualization and dashboards |
| DAX | Business metric calculations |
| GitHub | Project documentation and portfolio presentation |

---

---

# Conclusion

This project demonstrates a complete data analytics workflow from **raw data to business insights**.

The dataset was inspected and prepared in Excel, stored and analyzed using PostgreSQL, and finally transformed into interactive dashboards using Power BI.

The final dashboards allow users to explore sales, customer behavior, product performance, profitability, geography, and sales channels through interactive filters and KPIs.

---

## Author

**Your Name:** [BHOR SINGH]

**Role:** Aspiring Data Analyst

**Tools:** Excel | PostgreSQL | SQL | Power BI | DAX
