# Week 2 - SQL Data Analysis

## Task
Learn SQL basics using PostgreSQL and perform data exploration, filtering, aggregation, and validation on a dataset. Steps: 1.Load a CSV dataset into a PostgreSQL table. 2.Explore table (schema, sample data, distinct counts). 3.Apply WHERE filters (region, category, date, sales). 4.Use GROUP BY for aggregations (sales, quantity, averages). 5.Sort and limit results (top products, top categories). 6.Solve use cases (monthly trends, top customers, duplicates). 7.Validate results (row counts, null checks, data quality). Output: SQL Notebook (.sqlnb) + brief summary.

## Dataset
- **Source:** [Sample Superstore](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final?select=Sample+-+Superstore.csv)
- **Rows:** 9994
- **Columns:** 21 (Row ID, Order ID, Order Date, Ship Date, Ship Mode, Customer ID, Customer Name, Segment, Country, City, State, Postal Code, Region, Product ID, Category, Sub-Category, Product Name, Sales, Quantity, Discount, Profit)

## Setup
- Ran PostgreSQL inside a Docker container (`docker run --name pg-local ...`).
- Created a database called `week2`.
- Copied the CSV into the container and imported it using `COPY` with `WIN1252` encoding (file had some non-UTF8 characters).

## What I Did

### 1. Table Creation & Data Import
- Created the `superstore` table with proper data types (VARCHAR, INT, NUMERIC).
- Loaded all 9994 rows from the CSV using PostgreSQL's `COPY` command.

### 2. Explored the Table
- Checked column names, data types, and nullability using `information_schema.columns`.
- Previewed first 10 rows.
- Counted total rows (9994).
- Found distinct values: 5009 unique orders, 793 customers, 1862 products, 3 categories, 17 sub-categories, 4 regions, 49 states.
- Got min/max/avg for sales, profit, quantity, and discount.

### 3. Applied WHERE Filters
- Filtered by region (West), category (Technology), date (year 2017), and high-value sales (>1000).
- Combined multiple filters (Technology + East + profit > 100).
- Found loss-making orders (negative profit) — mostly caused by heavy discounts.

### 4. Used GROUP BY for Aggregations
- Grouped by category, sub-category, region, segment, and ship mode.
- Calculated total sales, total profit, average discount, and order counts for each group.

### 5. Sorted and Limited Results
- Top 10 products by total sales.
- Top 10 most profitable products.
- Bottom 10 products (biggest losses) — mostly Machines and Tables with high discounts.
- Top 10 states by revenue.

### 6. Solved Use Cases
- Monthly and yearly sales trends using `EXTRACT()` and `TO_DATE()`.
- Top 10 customers by total spend.
- Repeat customers (more than 10 orders).
- Checked for duplicate rows (none found — data is clean).
- Checked for duplicate order-product combinations.

### 7. Validated Results
- Confirmed row count is 9994.
- Checked for NULL values in critical columns (order_id, customer_id, sales, etc.) — no nulls found.
- Cross-verified that sum of regional sales equals total sales (PASS).
- Validated date range of the dataset.

## Key Findings
- **Technology** has the highest avg order value but also the biggest losses (due to heavy discounts on Machines).
- **West** region leads in total sales, **Central** has the lowest profit.
- No missing values or duplicate rows in the dataset.

## Output
- Query results with screenshots: [`week2_output.pdf`](week2_output.pdf)

## How to Run
1. Start PostgreSQL (Docker or local install).
2. Create a database called `week2`.
3. Copy the CSV file into the PostgreSQL server.
4. Open `week2.sqlnb` in VS Code (needs a SQL notebook extension).
5. Connect to the `week2` database and run cells top to bottom.
