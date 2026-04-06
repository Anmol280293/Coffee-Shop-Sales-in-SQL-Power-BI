
---

#  Coffee Shop Sales Analytics Dashboard (SQL + Power BI)

##  Project Overview

This project simulates a **real-world BI pipeline** where raw transactional data is transformed using SQL and visualized through Power BI to drive business decisions.

 **End-to-End Flow:**
`Raw Data → SQL Transformation → Data Modeling → Power BI Dashboard → Business Insights`

---

##  Business Problem

The company lacked:

* Centralized reporting
* Visibility into **sales trends & product performance**
* Insights into **customer buying behavior**

This resulted in:

* Poor inventory planning
* Missed revenue opportunities
* Inefficient staffing

---

##  Solution Architecture

```
         ┌──────────────┐
         │  Raw Data    │ (Excel / CSV)
         └──────┬───────┘
                │
        (Data Cleaning)
                │
         ┌──────▼───────┐
         │   SQL Layer  │  ← Data Transformation
         └──────┬───────┘
                │
        (Star Schema Model)
                │
         ┌──────▼────────┐
         │ Power BI      │  ← Visualization Layer
         └───────────────┘
```

---

##  SQL Layer (Data Transformation)

### Key Tables Created

* `sales` → Transaction-level data
* `products` → Product details
* `stores` → Store location data
* `calendar` → Date intelligence

---

###  Data Cleaning

```sql
-- Remove null values
SELECT *
FROM sales
WHERE transaction_id IS NOT NULL;

-- Convert date format
SELECT 
    CAST(order_date AS DATE) AS order_date
FROM sales;
```

---

###  Feature Engineering

```sql
-- Extract time features
SELECT 
    order_date,
    DATENAME(WEEKDAY, order_date) AS day_name,
    DATEPART(HOUR, order_time) AS hour
FROM sales;
```

---

###  KPI Queries

####  Total Sales

```sql
SELECT SUM(total_amount) AS total_sales
FROM sales;
```

####  Total Orders

```sql
SELECT COUNT(DISTINCT transaction_id) AS total_orders
FROM sales;
```

####  Total Quantity Sold

```sql
SELECT SUM(quantity) AS total_quantity
FROM sales;
```

---

###  Business Analysis Queries

####  Sales by Product Category

```sql
SELECT 
    category,
    SUM(total_amount) AS revenue
FROM sales
GROUP BY category
ORDER BY revenue DESC;
```

---

####  Peak Sales Hours

```sql
SELECT 
    DATEPART(HOUR, order_time) AS hour,
    SUM(total_amount) AS revenue
FROM sales
GROUP BY DATEPART(HOUR, order_time)
ORDER BY revenue DESC;
```

---

####  Store Performance

```sql
SELECT 
    store_location,
    SUM(total_amount) AS revenue
FROM sales
GROUP BY store_location
ORDER BY revenue DESC;
```

---

##  Power BI Layer (Visualization)

###  Data Model

* Built using **Star Schema**

  * Fact Table: `sales`
  * Dimension Tables:

    * `products`
    * `stores`
    * `calendar`

---

###  Key DAX Measures

####  Total Sales

```DAX
Total Sales = SUM(sales[total_amount])
```

####  Total Orders

```DAX
Total Orders = DISTINCTCOUNT(sales[transaction_id])
```

####  Sales Growth

```DAX
Sales Growth % = 
DIVIDE(
    [Total Sales] - CALCULATE([Total Sales], PREVIOUSMONTH(calendar[date])),
    CALCULATE([Total Sales], PREVIOUSMONTH(calendar[date]))
)
```

---

###  Dashboard Features

* KPI Cards (Sales, Orders, Quantity)
* Sales Trend (Line Chart)
* Product Category Analysis (Bar Chart)
* Top Products Ranking
* Store Performance Comparison
* Heatmap (Day vs Hour Analysis)
* Interactive Filters (Month, Category)

---

##  Key Insights Generated

*  Coffee contributes the highest revenue (~38%)
*  Weekdays drive ~74% of total sales
*  Peak hours: **8 AM – 11 AM**
*  Store performance is evenly distributed
*  Slight dip at month-end → promotion opportunity

---

##  Business Impact

 Improved **decision-making with real-time insights**
 Enabled **staff optimization during peak hours**
 Identified **high-performing products for upselling**
Highlighted **weekend growth opportunities**

---
