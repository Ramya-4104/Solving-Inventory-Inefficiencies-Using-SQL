Solving Inventory Inefficiencies Using SQL

Poject Overview

This project focuses on analyzing and optimizing inventory operations for a retail company using structured SQL queries and data modeling techniques. Urban Retail Co., a mid-sized omnichannel retailer, faces recurring issues like stockouts of fast-moving products, overstocks of slow-moving ones, and a lack of integrated analytics for forecasting and inventory control. Our goal was to design a data-driven, SQL-powered solution to address these inefficiencies and support smarter inventory decisions.

We began by cleaning and exploring transactional data covering products, stores, inventory levels, forecasts, promotions, and weather. The raw dataset contained redundancies and was normalized into a well-structured schema consisting of stores, products, and inventory_facts tables to improve query efficiency and data integrity.

Key SQL implementations included calculating real-time stock levels using Common Table Expressions (CTEs) and designing reorder point (ROP) logic using the formula: ROP = (Average Daily Usage × Lead Time) + Safety Stock. We flagged products requiring urgent restocking and supported proactive inventory planning using a need_reorder field. We also performed inventory turnover analysis and classified items as slow, moderate, or fast-moving to identify overstock or underperforming products.

A series of analytical reports were generated to evaluate discount effectiveness, competitor pricing impact, seasonal performance, and weather-based demand. These insights were visualized through a Power BI dashboard to support business recommendations and forecasting strategies.

The project helped highlight actionable KPIs such as inventory turnover, stockout rate, average inventory, and inventory age. Overall, this SQL-based solution enables smarter demand forecasting, pricing strategy, and restocking decisions, reducing supply chain costs and increasing product availability.

Project Drive (SQL Queries, ERD, Dashboards):(https://drive.google.com/drive/folders/13Y-2fxllKmeJp0J2U-xuG-F98xhWfudQ)

ERD:
![image](https://github.com/user-attachments/assets/e32e4140-5212-44db-93aa-457067a15d0a)

Power BI Dahboard:
![Screenshot 2025-07-01 195131](https://github.com/user-attachments/assets/19cbcbfa-032e-4b05-810e-823da50a8f4c)

# Solving Inventory Inefficiencies Using SQL
### Urban Retail Co. — SQL Data Analysis Project

---

## Problem Statement

Urban Retail Co., a mid-sized omnichannel retailer, was facing three core inventory problems:
- **Stockouts** of fast-moving products leading to lost sales
- **Overstocks** of slow-moving products tying up capital
- **No integrated analytics** for forecasting or restocking decisions

This project builds a SQL-powered solution to fix all three.

---

## What Was Built

| # | What | Why |
|---|------|-----|
| 1 | Normalized raw data into 4 clean tables | Remove redundancy, improve query performance |
| 2 | Latest inventory snapshot using CTEs | Know current stock levels across all stores |
| 3 | Reorder Point (ROP) logic | Automatically flag products that need restocking |
| 4 | Seasonal KPI analysis | Understand which products perform in which seasons |
| 5 | KPI Summary table | Turnover, stockout rate, inventory age per store/product |
| 6 | Business analytics queries | Pricing, promotions, weather, competitor impact |

---

## Database Schema (ERD)

```
inventory_rawdata  (raw import table — source of all data)
        |
        |-- normalized into -->
        |
        +------------------+------------------+--------------------+
        |                  |                  |                    |
   [ stores ]         [ products ]    [ inventory_facts ]   [ environment ]
   ----------         ----------      -----------------     -----------
   st_no (PK)         Product_ID(PK)  date                  date
   Store_ID           Category        st_no (FK→stores)     st_no (FK→stores)
   Region                             Product_ID(FK→prod)   Weather_Condition
                                      Inventory_Level       Holiday_Promotion
                                      Units_Sold            Seasonality
                                      Units_Ordered
                                      Demand_Forecast
                                      Discount
                                      Competitor_Pricing

        |
        |-- derived tables -->
        |
   [ latest_inventory_snapshot ]     [ reorder_estimations ]     [ parameters ]
   ----------------------------       ----------------------      ------------
   st_no, Store_ID, Region            st_no, Store_ID, Region     lead_time_days
   Product_ID                         Product_ID                  safety_stock
   last_inventory_date                avg_daily_usage
   Inventory_Level                    reorder_point

        |
        |-- analytical tables -->
        |
   [ seasonal_product_sales ]    [ seasonal_product_inventory ]    [ KPI_Summary ]
   -------------------------      ----------------------------      -----------
   Product_ID                     Product_ID                        Store_ID
   Winter/Summer/Autumn/          Winter/Summer/Autumn/             Product_ID
   Spring_Sales                   Spring_Inventory                  Inventory_Turnover
   Total_Sales                    Avg_Seasonal_Inventory            Stockout_Rate_Pct
   Avg_Seasonal_Sales                                               Inventory_Age
```

---

## Key SQL Concepts Used

- **Normalization** — split one flat table into 4 related tables
- **CTEs (WITH clause)** — used to build the latest inventory snapshot step by step
- **CROSS JOIN** — generate all possible store × product combinations
- **FOREIGN KEYS** — enforce relationships between tables
- **CASE WHEN** — classify inventory health (🟥 Slow / 🟨 Moderate / 🟩 Fast)
- **Window Functions** — RANK() to find best weather condition per product
- **NULLIF / COALESCE** — handle divide-by-zero and missing values safely

---

## Reorder Point Formula

```
ROP = (Average Daily Usage × Lead Time in Days) + Safety Stock
```

- Lead time and safety stock are stored in a `parameters` table
- Default: lead_time = 1 day, safety_stock = 30 units
- Easy to change without touching the main logic

---

## Inventory Health Classification

| Status | Turnover Range | Meaning |
|--------|---------------|---------|
| 🟥 Slow | < 25 | Overstock risk — product sitting too long |
| 🟨 Moderate | 25 – 50 | Healthy — efficient stock movement |
| 🟩 Fast | > 50 | Stockout risk — needs frequent restocking |

---

## Business Analytics Queries

| Query | Business Question |
|-------|------------------|
| Discount vs Demand | Does giving higher discounts actually increase orders? |
| Competitor Pricing | Are we priced above or below competitors per category? |
| Product Profitability | Is each product making profit or loss vs competitors? |
| Holiday Promotions | Do promotions during holidays actually boost orders? |
| Weather Impact | Which weather condition drives the most orders per product? |
| Seasonal Performance | Which season is most profitable per product? |

---

## KPIs Tracked

- **Inventory Turnover** — how fast stock is sold relative to average inventory
- **Days Inventory Outstanding (DIO)** — how many days stock lasts
- **Stockout Rate %** — percentage of days a product had zero inventory
- **Inventory Age** — how old the current stock is in days
- **Promo Uplift** — difference in orders during vs outside holiday promotions

---

## Files in This Project

| File | Description |
|------|-------------|
| `C&A_PROJECT.sql` | All SQL queries — run section by section in MySQL Workbench |
| `Excel dashboards and dataset.xlsx` | Raw dataset + Power BI dashboard |
| `SQL Project.pdf` | Project report |

---

## How to Run

1. Open **MySQL Workbench** → connect to local instance (default: `root`, port `3306`)
2. Open `C&A_PROJECT.sql` via File → Open SQL Script
3. Run **Section 1** to create the database
4. Run **Section 2** to create `inventory_rawdata`
5. Right-click `inventory_rawdata` in the left panel → **Table Data Import Wizard** → import the Excel/CSV file
6. Run **Sections 3 through 10** in order — each section builds on the previous one

> Tip: Select a section and press `Ctrl + Shift + Enter` to run only that block.

---

## Dashboard

![Power BI Dashboard](https://github.com/user-attachments/assets/19cbcbfa-032e-4b05-810e-823da50a8f4c)

---

## Project Drive (Queries, ERD, Dashboards)

[Open Google Drive Folder](https://drive.google.com/drive/folders/13Y-2fxllKmeJp0J2U-xuG-F98xhWfudQ)


