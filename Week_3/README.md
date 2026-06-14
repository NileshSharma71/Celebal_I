# Week 3 - SQL Advanced Analytics

## Task
Apply advanced SQL concepts — Subqueries, CTEs, and Window Functions — to analyze the Superstore dataset and answer business questions. Steps: 1.Load the CSV into a staging table (superstore_raw). 2.Create normalized customers, orders, and products tables using SELECT DISTINCT. 3.Use Subqueries to find above-average sales and highest-value orders. 4.Use CTEs for customer-level metrics and intermediate aggregations. 5.Apply Window Functions (ROW_NUMBER, RANK, DENSE_RANK) for ranking and segmentation. 6.Combine JOINs, CTEs, and Window Functions for customer sales rankings. 7.Perform customer sales analysis (top/bottom customers, single-order, above-average). Output: SQL Notebook (.sqlnb) + brief insights.

## Dataset
- **Source:** [Sample Superstore](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final?select=Sample+-+Superstore.csv)
- **Rows:** 9994
- **Columns:** 21 (Row ID, Order ID, Order Date, Ship Date, Ship Mode, Customer ID, Customer Name, Segment, Country, City, State, Postal Code, Region, Product ID, Category, Sub-Category, Product Name, Sales, Quantity, Discount, Profit)

## Setup
- Same PostgreSQL Docker container from Week 2 (`pg-local`).
- Created a new database called `week3`.
- Copied the CSV into the container's `/tmp/` and loaded it with `COPY` using `WIN1252` encoding.

## What I Did

### 1. Staging Table & Data Load
- Created `superstore_raw` table with all 21 columns as the staging area.
- Loaded all 9994 rows from the CSV using PostgreSQL's `COPY` command.
- Verified the data with a quick preview and row count check.

### 2. Created Normalized Tables
- **customers** — extracted unique `customer_id`, `customer_name`, and `segment` using `SELECT DISTINCT`.
- **products** — extracted unique `product_id`, `product_name`, `category`, and `sub_category`.
- **orders** — kept all transactional data (sales, quantity, discount, profit) with foreign keys to customers and products.
- Verified row counts for all three tables.

### 3. Subqueries
- Found orders where sales exceed the overall average — shows the skewed distribution.
- Identified the single highest-sales order using a subquery with `MAX()`.
- Used a correlated subquery to find orders beating their own category's average sales.
- Found customers who placed more orders than the average customer (nested subquery).
- Got top 5 products by total sales using a subquery in the `FROM` clause.

### 4. Common Table Expressions (CTEs)
- Built a customer-level sales summary (total orders, total sales, total profit, avg order value) using a CTE for cleaner code.
- Created monthly sales trends with profit margin calculation using an intermediate CTE.

### 5. Window Functions
- Applied `ROW_NUMBER()` partitioned by customer to assign unique row numbers ordered by sales.
- Used `ROW_NUMBER()` to pick each customer's highest sale (top order per customer).
- Compared `RANK()` vs `DENSE_RANK()` vs `ROW_NUMBER()` on customer total sales — shows how they handle ties differently.
- Ranked products within each category using `DENSE_RANK()`.
- Calculated running sales totals per customer ordered by date using `SUM() OVER()` with frame clause.

### 6. Combining JOINs + CTEs + Window Functions
- Built a full customer ranking report with both overall and segment-level ranks using `RANK()` with `PARTITION BY`.
- Found top 3 customers per segment using stacked CTEs and `ROW_NUMBER()`.
- Identified each customer's best-selling product category using multi-table JOINs + CTE + window function.

### 7. Business Queries (Customer Insights)
- **Top 10 customers** by total sales — Consumer segment dominated.
- **Bottom 10 customers** — mostly single-order buyers with very small purchases.
- **Single-order customers** — a significant chunk of the customer base only ordered once, indicating retention opportunities.
- **Customer distribution** — split customers into Above Average vs Below Average groups. Most customers fall below the average (the mean is pulled up by a few big spenders).

## Key Findings
- A small percentage of orders account for above-average sales — the distribution is heavily right-skewed.
- The top 10 customers contribute way more than the bottom 10, gap is massive (100x+).
- Single-order customers make up a big portion, suggesting room for re-engagement.
- `RANK` vs `DENSE_RANK` showed very few ties in customer totals — most customers have unique spending levels.
- Running totals per customer help visualize how spending builds over time.

## How to Run
1. Start the PostgreSQL Docker container (`docker start pg-local`).
2. Create a database called `week3`.
3. Copy the CSV file into the container (`docker cp "Sample - Superstore.csv" pg-local:/tmp/superstore.csv`).
4. Open `week3.sqlnb` in VS Code (needs SQL Notebook extension).
5. Connect to `week3` database and run cells top to bottom.
