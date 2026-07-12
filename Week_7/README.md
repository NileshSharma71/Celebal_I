# Week 7 – Incremental Data Processing with Delta Lake

## Objective

Perform incremental data processing using Delta Lake on Databricks. Load a dataset into a managed Delta table, apply data cleaning, simulate incremental data, execute a MERGE (upsert) operation, and validate the results.

## Platform

- **Environment**: Databricks Community Edition (Serverless)
- **Runtime**: Databricks Runtime 18.x (Photon, Scala 2.13)
- **Language**: PySpark (Python)
- **Storage Format**: Delta Lake (managed tables on Unity Catalog)

## Dataset
- **Source**: [Kaggle – Superstore dataset](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final?resource=download)
- **File**: `Sample - Superstore.csv`
- **Size**: ~10,000 rows × 21 columns

### Key Columns

| Column | Description |
|--------|-------------|
| Row ID | Unique row identifier (used as merge key) |
| Order ID | Order transaction identifier |
| Customer ID | Customer identifier |
| Customer Name | Customer full name |
| Segment | Consumer, Corporate, Home Office |
| Country / City / State | Geographic info |
| Category | Furniture, Office Supplies, Technology |
| Sub-Category | Product sub-category |
| Product Name | Full product description |
| Sales | Sale amount |
| Quantity | Number of items |
| Discount | Discount applied |
| Profit | Profit from the sale |

## Steps Performed

### 1. Load Dataset into Delta Table

- Read the CSV file from Databricks workspace with proper quote/escape handling
- Performed initial cleaning: removed duplicates on `Row ID`, dropped rows with null `Row ID` or `Customer ID`
- Sanitized column names (replaced spaces and hyphens with underscores) for Delta compatibility
- Saved as a managed Delta table: `workspace.default.superstore_master`
- **Result**: 9,994 rows loaded into the Delta table

### 2. Data Cleaning

- `dropDuplicates(["Row ID"])` — removed duplicate rows based on the business key
- `dropna(subset=["Row ID", "Customer ID"])` — removed records with missing critical identifiers
- Column name sanitization — ensured all column names are Delta-safe (no spaces/hyphens)

### 3. Simulate Incremental Data

- Fetched 5 existing rows from the master table to simulate **updates**:
  - Increased `Sales` by 10% (`Sales * 1.10`)
  - Increased `Profit` by 5% (`Profit * 1.05`)
- Created 1 completely new row to simulate an **insert**:
  - `Row_ID = 99999`, `Order_ID = CA-2026-99999`, `Customer_Name = Nilesh Sharma`
  - `Sales = 550.00`, `Profit = 120.00`
- Combined into a single incremental DataFrame (6 rows total: 5 updates + 1 insert)

### 4. MERGE Operation (Upsert)

- Used `DeltaTable.forName()` to reference the live Delta table
- Applied Delta Lake's `MERGE` with `Row_ID` as the match key:
  - `whenMatchedUpdateAll()` — updates all columns for existing rows
  - `whenNotMatchedInsertAll()` — inserts new rows that don't exist in the target
- **MERGE metrics**: 5 rows updated, 1 row inserted, 0 rows deleted

### 5. Validation

| Check | Result |
|-------|--------|
| Final row count | **9,995** (9,994 original + 1 new insert) |
| Duplicate records on `Row_ID` | **0** (MERGE updated in-place, no duplicates) |
| New row verification | Row `99999` (Nilesh Sharma, Sales=550.0, Profit=120.0) present ✅ |

### 6. Transaction History

- Used `DESCRIBE HISTORY` to view the Delta table's version log:
  - **Version 0**: `CREATE OR REPLACE TABLE AS SELECT` — initial load (9,994 rows)
  - **Version 1**: `MERGE` — incremental upsert (5 updates + 1 insert)

## Project Structure

```
Week_7/
│
├── data/
│   └── Sample - Superstore.csv        # Source dataset
│
├── notebooks/
│   └── delta_incremental_processing.ipynb  # Databricks notebook (exported)
│
├── screenshots/
│   ├── 01_data_loading.png             # CSV load + Delta table creation
│   ├── 02_incremental_data.png         # Simulated incremental batch
│   ├── 03_merge_operation.png          # Delta MERGE execution
│   ├── 04_validation.png               # Row count, duplicates, new row check
│   └── 05_transaction_history.png      # DESCRIBE HISTORY output
│
└── README.md
```

## Screenshots

### Step 1 — Data Loading & Delta Table Creation
![Data Loading](screenshots/Screenshot%20from%202026-07-13%2002-51-14.png)

### Step 2 — Incremental Data Simulation
![Incremental Data](screenshots/Screenshot%20from%202026-07-13%2002-52-06.png)

### Step 3 — Delta MERGE Operation
![Merge Operation](screenshots/Screenshot%20from%202026-07-13%2002-52-53.png)

### Step 4 — Validation & Results
![Validation](screenshots/Screenshot%20from%202026-07-13%2002-57-54.png)

### Step 5 — Transaction History
![Transaction History](screenshots/Screenshot%20from%202026-07-13%2002-56-33.png)
