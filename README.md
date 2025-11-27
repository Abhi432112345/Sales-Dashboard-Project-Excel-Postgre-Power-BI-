# Sales Dashboard Project 📊

A comprehensive data analytics project featuring sales data analysis across multiple countries, SQL-based data processing in PostgreSQL, and an interactive Power BI dashboard.

![Sales Dashboard](https://img.shields.io/badge/Power_BI-Dashboard-yellow) ![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Data_Processing-blue) ![Status](https://img.shields.io/badge/Status-Complete-success)

## 📑 Table of Contents
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Technologies Used](#technologies-used)
- [Database Schema](#database-schema)
- [Data Processing Pipeline](#data-processing-pipeline)
- [Key Features](#key-features)
- [SQL Queries & Analysis](#sql-queries--analysis)
- [Dashboard Insights](#dashboard-insights)
- [Setup Instructions](#setup-instructions)
- [Future Enhancements](#future-enhancements)

## 🎯 Project Overview

This project analyzes sales data from six countries (Canada, China, India, Nigeria, UK, and US) to provide actionable business insights. The pipeline includes data cleaning, transformation in PostgreSQL, and visualization in Power BI.

**Key Metrics Tracked:**
- Total Sales: 4,135K
- Total Profit: 3,539K
- Total Orders: 3K
- Total Discount: 77K
- Average Order Value: 1K

## 📊 Data Source

- **Origin**: Kaggle Dataset
- **Coverage**: 6 countries (Canada, China, India, Nigeria, UK, US)
- **Records**: Multiple sales transactions across different product categories
- **Format**: CSV files processed through PostgreSQL

## 🛠 Technologies Used

- **Database**: PostgreSQL
- **Visualization**: Power BI Desktop
- **Data Source**: Kaggle
- **Languages**: SQL

## 🗃 Database Schema

### Main Table: `Sales Data`

| Column Name | Data Type | Description |
|------------|-----------|-------------|
| Transaction ID | VARCHAR | Unique transaction identifier |
| Date | DATE | Transaction date |
| Country | VARCHAR | Country of sale |
| Product ID | VARCHAR | Unique product identifier |
| Product Name | VARCHAR | Name of product |
| Category | VARCHAR | Product category |
| Price Per Unit | NUMERIC | Unit price |
| Quantity Purchased | INTEGER | Number of units sold |
| Cost Price | NUMERIC | Cost per unit |
| Discount Applied | NUMERIC | Discount amount |
| Payment Method | VARCHAR | Payment type |
| Customer Age Group | VARCHAR | Age segment |
| Customer Gender | VARCHAR | Customer gender |
| Store Location | VARCHAR | Store city |
| Sales Representative | VARCHAR | Sales rep name |
| **Total Amount** | NUMERIC(10,2) | *Calculated: (Price × Quantity) - Discount* |
| **Profit** | NUMERIC(10,2) | *Calculated: Total Amount - (Cost Price × Quantity)* |

## 🔄 Data Processing Pipeline

### Step 1: Data Collection & Preparation
```text
1. Downloaded datasets from Kaggle
2. Cleaned column names and standardized formats
3. Converted to CSV format for PostgreSQL import
```

### Step 2: Data Consolidation
```sql
-- Merge all 6 country datasets into one table
CREATE TABLE public."Sales Data" AS
SELECT * FROM public."Sales Canada"
UNION ALL
SELECT * FROM public."Sales China"
UNION ALL
SELECT * FROM public."Sales India"
UNION ALL
SELECT * FROM public."Sales Nigeria"
UNION ALL
SELECT * FROM public."Sales UK"
UNION ALL
SELECT * FROM public."Sales US";
```

### Step 3: Data Quality Checks

**Check for Missing Values:**
```sql
SELECT *
FROM public."Sales Data"
WHERE "Country" IS NULL
   OR "Price Per Unit" IS NULL
   OR "Quantity Purchased" IS NULL
   OR "Cost Price" IS NULL
   OR "Discount Applied" IS NULL;
```

**Check for Duplicate Records:**
```sql
SELECT "Transaction ID", COUNT(*)
FROM public."Sales Data"
GROUP BY "Transaction ID"
HAVING COUNT(*) > 1;
```

### Step 4: Data Cleaning

**Update Missing Quantity:**
```sql
UPDATE public."Sales Data"
SET "Quantity Purchased" = 3
WHERE "Transaction ID" = '00a30472-89a0-4688-9d33-67ea8ccf7aea';
```

**Impute Missing Price with Average:**
```sql
UPDATE public."Sales Data"
SET "Price Per Unit" = (
    SELECT AVG("Price Per Unit")
    FROM public."Sales Data"
    WHERE "Price Per Unit" IS NOT NULL
)
WHERE "Transaction ID" = '001898f7-b696-4356-91dc-8f2b73d09c63';
```

### Step 5: Feature Engineering

**Add Total Amount Column:**
```sql
-- Create new column
ALTER TABLE public."Sales Data" 
ADD COLUMN "Total Amount" NUMERIC(10,2);

-- Calculate Total Amount = (Price × Quantity) - Discount
UPDATE public."Sales Data"
SET "Total Amount" = ("Price Per Unit" * "Quantity Purchased") - "Discount Applied";
```

**Add Profit Column:**
```sql
-- Create new column
ALTER TABLE public."Sales Data" 
ADD COLUMN "Profit" NUMERIC(10,2);

-- Calculate Profit = Total Amount - (Cost Price × Quantity)
UPDATE public."Sales Data"
SET "Profit" = "Total Amount" - ("Cost Price" * "Quantity Purchased");
```

## 📈 SQL Queries & Analysis

### Query 1: Sales Revenue & Profit by Country
```sql
SELECT
    "Country",
    SUM("Total Amount") AS "Total Revenue",
    SUM("Profit") AS "Total Profit"
FROM public."Sales Data"
WHERE "Date" BETWEEN '2025-02-10' AND '2025-02-14'
GROUP BY "Country"
ORDER BY "Total Revenue" DESC;
```

### Query 2: Top 5 Best-Selling Products
```sql
SELECT
    "Product Name",
    SUM("Quantity Purchased") AS "Total Units Sold"
FROM public."Sales Data"
WHERE "Date" BETWEEN '2025-02-10' AND '2025-02-14'
GROUP BY "Product Name"
ORDER BY "Total Units Sold" DESC
LIMIT 5;
```

### Query 3: Best Sales Representatives
```sql
SELECT
    "Sales Representative",
    SUM("Total Amount") AS "Total Sales"
FROM public."Sales Data"
WHERE "Date" BETWEEN '2025-02-10' AND '2025-02-14'
GROUP BY "Sales Representative"
ORDER BY "Total Sales" DESC
LIMIT 5;
```

### Query 4: Top Store Locations by Sales
```sql
SELECT
    "Store Location",
    SUM("Total Amount") AS "Total Sales",
    SUM("Profit") AS "Total Profit"
FROM public."Sales Data"
WHERE "Date" BETWEEN '2025-02-10' AND '2025-02-14'
GROUP BY "Store Location"
ORDER BY "Total Sales" DESC
LIMIT 5;
```

### Query 5: Key Sales & Profit Statistics
```sql
SELECT
    MIN("Total Amount") AS "Min Sales Value",
    MAX("Total Amount") AS "Max Sales Value",
    AVG("Total Amount") AS "Avg Sales Value",
    SUM("Total Amount") AS "Total Sales Value",
    MIN("Profit") AS "Min Profit",
    MAX("Profit") AS "Max Profit",
    AVG("Profit") AS "Avg Profit",
    SUM("Profit") AS "Total Profit"
FROM public."Sales Data"
WHERE "Date" BETWEEN '2025-02-10' AND '2025-02-14';
```

## 🎨 Dashboard Insights

The Power BI dashboard includes:

### Key Performance Indicators (KPIs)
- **Total Discount**: 77K
- **Total Orders**: 3K
- **Total Profit**: 3,539K
- **Total Sales**: 4,135K
- **Average Order Value**: 1K

### Visualizations

<img width="1225" height="611" alt="image" src="https://github.com/user-attachments/assets/71a85a58-ed78-468e-9dc4-fc0b820268c7" />

1. **Sales by Location** (Interactive Map)
   - Geographic distribution across Canada, China, India, Nigeria, UK, and US

2. **Month Sales** (Line Chart)
   - Trending sales performance from January to December
   - Peak sales visible around mid-year

3. **Total Sales by Category** (Horizontal Bar Chart)
   - Home & Kitchen: 0.73M
   - Clothing: 0.71M
   - Electronics: 0.68M
   - Sports: 0.68M
   - Beauty: 0.67M
   - Toys: 0.67M

4. **Sales by Payment Method** (Pie Chart)
   - Credit Card: 52.62% (1.42M)
   - Cash: 33.07% (1.37M)
   - Mobile Payment: 22.67% (1.35M)

5. **Total Profit by Day Name** (Bar Chart)
   - Daily profit distribution showing weekly patterns

### Interactive Filters
- Country
- Store Location
- Category
- Payment Method
- Date Range (with slider)

## 🚀 Setup Instructions

### Prerequisites
```text
- PostgreSQL 12 or higher
- Power BI Desktop
- Basic knowledge of SQL
```

### Installation Steps

**1. Clone the Repository**
```bash
git clone https://github.com/yourusername/sales-dashboard-project.git
cd sales-dashboard-project
```

**2. Set Up PostgreSQL Database**
```sql
-- Create database
CREATE DATABASE sales_analytics;

-- Connect to the database
\c sales_analytics

-- Create individual country tables and import CSV files
-- (Use PostgreSQL COPY command or pgAdmin import tool)
```

**3. Run SQL Scripts in Order**
```bash
# Navigate to sql folder
cd sql

# Execute scripts in order
psql -U your_username -d sales_analytics -f 01_create_tables.sql
psql -U your_username -d sales_analytics -f 02_merge_datasets.sql
psql -U your_username -d sales_analytics -f 03_data_cleaning.sql
psql -U your_username -d sales_analytics -f 04_feature_engineering.sql
psql -U your_username -d sales_analytics -f 05_analysis_queries.sql
```

**4. Connect Power BI to PostgreSQL**
```text
1. Open Power BI Desktop
2. Click "Get Data" → "PostgreSQL database"
3. Enter server: localhost
4. Enter database: sales_analytics
5. Select "Sales Data" table
6. Click "Load"
```

**5. Open the Dashboard**
```text
1. Navigate to powerbi folder
2. Open Sales_Dashboard.pbix
3. Refresh data if needed
4. Explore the visualizations
```


## 🔮 Future Enhancements

- [ ] Add customer segmentation analysis using RFM model
- [ ] Implement predictive sales forecasting with time series
- [ ] Create automated ETL pipeline with Python/Apache Airflow
- [ ] Add real-time data refresh capabilities
- [ ] Include customer retention and churn metrics
- [ ] Develop mobile-responsive dashboard version
- [ ] Add product recommendation engine
- [ ] Integrate with CRM systems (Salesforce, HubSpot)
- [ ] Implement advanced analytics with Python integration
- [ ] Add drill-through reports for detailed analysis

## 📊 Key Learnings

- Data consolidation from multiple international sources
- SQL-based data cleaning and transformation techniques
- Feature engineering for business metrics calculation
- Creating interactive and insightful dashboards in Power BI
- Data quality management and validation
- Best practices for ETL processes
- Business intelligence visualization techniques

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a new branch (`git checkout -b feature/improvement`)
3. Make your changes
4. Commit your changes (`git commit -am 'Add new feature'`)
5. Push to the branch (`git push origin feature/improvement`)
6. Create a Pull Request


## 📧 Contact

For questions, feedback, or collaboration opportunities:

- LinkedIn:[ [Abhishek](https://linkedin.com/in/yourprofile](https://www.linkedin.com/in/abhishek-r-131054224/))
- Email: abhi147741@gmail.com

## 🙏 Acknowledgments

- Kaggle for providing the dataset
- PostgreSQL community for excellent documentation
- Power BI community for visualization inspiration

---

**Made with ❤️ by a Data Analytics Aspirant**

*Last Updated: November 2025*
