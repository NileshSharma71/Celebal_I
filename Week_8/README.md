# Week 8 – E-Commerce Order Analytics System

## Objective

Build an end-to-end data analytics system to process and analyze e-commerce order data using Python and SQL. Generate realistic datasets with intentional inconsistencies, clean and validate data using Pandas, load into SQLite, and run analytical queries to derive business insights.

## Setup

```bash
pip install pandas faker
```

## Running

1. Open `01_data_pipeline.ipynb` — run all cells to generate and clean data
2. Open `02_sql_analysis.ipynb` — run all cells for SQL analysis, reporting, and tests

## Dataset

4 CSV files generated with fake but realistic data:

| File | Rows | Description |
|------|------|-------------|
| `customers.csv` | 200 | Customer profiles with ~2% invalid emails |
| `products.csv` | 80 | Product catalog with messy names (extra spaces, mixed case) |
| `orders.csv` | 600 | Orders with ~5% NULL customer_ids, ~5% wrong date formats |
| `order_items.csv` | 1,500 | Line items with ~3% negative quantities (returns) |

## Notebook 1 — Data Pipeline

Covers Part 1 (data generation) and Part 2 (data cleaning):

- **Data Generation**: Creates 4 CSVs using Faker with intentional data quality issues
- **clean_orders()**: Fixes date formats (DD-MM-YYYY → YYYY-MM-DD), replaces NULL customer_ids with `UNKNOWN`
- **clean_products()**: Normalizes product names — trims spaces, applies title case
- **validate_emails()**: Identifies customers with invalid email addresses
- **check_referential_integrity()**: Finds order items referencing non-existent orders
- Caps discount > 100% to 100, flags negative quantities as returns
- Saves cleaned CSVs to `data/cleaned/`

## Notebook 2 — SQL Analysis & Reporting

Covers Part 3 (16 SQL queries), Part 4 (reporting tool), and Part 5 (edge case tests).

### SQL Queries (Part 3)

| # | Query | Key Techniques |
|---|-------|---------------|
| 1 | Revenue per category | JOIN, GROUP BY |
| 2 | Top 10 customers by value | Multi-table JOIN |
| 3 | Monthly order count | STRFTIME, date filtering |
| 4 | Customers never delivered | Subquery, NOT IN |
| 5 | Products with more returns than purchases | CASE WHEN, HAVING |
| 6 | Return rate per category | Conditional aggregation |
| 7 | Running revenue total per region | Window SUM with PARTITION BY |
| 8 | Product rank by category revenue | DENSE_RANK() |
| 9 | Days between consecutive orders | LAG(), risk flagging |
| 10 | Customer segments per month | Multi-level CTE |
| 11 | Customer quartiles (Platinum/Gold/Silver/Bronze) | NTILE(4) |
| 12 | Year-over-Year revenue comparison | Self-JOIN on year offset |
| 13 | First/last purchased category | FIRST_VALUE(), LAST_VALUE() |
| 14 | Cumulative revenue distribution | Running SUM, % of total |
| 15 | Cohort retention analysis | Multi-CTE, month offsets |
| 16 | Products frequently bought together | Self-JOIN, deduplication |

### Reporting Tool (Part 4)

Command-line report generator that shows total orders, revenue, unique customers, top 3 products, and period-over-period % change. Uses only `sqlite3`.

### Edge Case Tests (Part 5)

8 test cases covering orphan references, discount > 100%, zero quantities, future dates, NULL handling, return flagging, and data type integrity. All 8 pass.

## Project Structure

```
Week_8/
├── 01_data_pipeline.ipynb       # Data generation + cleaning
├── 02_sql_analysis.ipynb        # SQL queries + reporting + tests
├── data/
│   ├── raw/                     # Generated CSVs (with issues)
│   ├── cleaned/                 # Cleaned CSVs
│   └── ecommerce.db             # SQLite database
├── reports/                     # Query result CSVs
└── README.md
```
