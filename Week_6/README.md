# Week 6 – Spark Architecture & Efficient Data Processing

## Objective

Understand Spark architecture and perform efficient data processing using transformations, filtering, schema handling, optimized file formats (CSV vs Parquet), and complete data pipelines.

## Dataset

- **File**: `retail_customers.csv` (synthetically generated)
- **Size**: 8,000 rows × 17 columns
- Contains realistic noise: null values, outliers, invalid ages, missing IDs

### Key Columns

| Column | Description |
|--------|-------------|
| CustomerID | Unique customer identifier |
| Age | Customer age (with some invalid values) |
| Gender | Male, Female, Other, Prefer not to say |
| IncomeLevel | Low, Medium, High, Very High |
| Country / City | Geographic info |
| TotalPurchases | Number of purchases |
| AvgOrderValue | Average order amount |
| CustomerLifetimeValue | Lifetime value |
| FavoriteCategory | Preferred shopping category |
| SatisfactionScore | 1-10 rating |
| LoyaltyScore | 0-100 loyalty metric |
| PremiumMember | Yes/No |
| LastPurchaseDate | Date of last purchase |

## Setup

```bash
# Requires Java (JDK 8+) and PySpark
pip install pyspark findspark
```

## Running

1. Open `week6.ipynb` in VS Code / Jupyter
2. Select kernel **Python (.venv - Celebal)**
3. Run All or execute cell-by-cell

## Topics Covered

### 1. Spark Architecture
- Driver, Cluster Manager, Executors
- Execution modes: Local, Client, Cluster
- Job → Stage → Task execution flow

### 2. Lazy Evaluation & DAG
- Transformations build a DAG (not executed immediately)
- Actions trigger execution
- `explain()` to inspect logical and physical plans

### 3. Schema Handling
- `inferSchema` vs explicit `StructType` schema
- Performance comparison between both approaches

### 4. Filtering & Column Selection
- Age range, category, country filters
- Compound filter conditions
- Column selection with `select()`

### 5. DataFrame Modifications
- Rename columns (`withColumnRenamed`)
- Cast data types (`to_date`, `cast`)
- Add computed columns (`EstRevenue`, `ValueSegment`)

### 6. Null Value Handling
- Count nulls per column
- Drop rows with null keys
- Fix invalid values (negative ages, outliers)
- Impute with means / "Unknown"

### 7. Transformations vs Actions
- Narrow (select, filter, map) vs Wide (groupBy, join, repartition)
- Shuffle mechanics

### 8. Shuffle, Partitioning & Predicate Pushdown
- `explain()` showing Exchange (shuffle) nodes
- `repartition()` vs `coalesce()`
- Predicate pushdown with Parquet

### 9. CSV vs Parquet Performance
- File size comparison
- Read + filter speed benchmark
- Schema preservation in Parquet

### 10. Complete Pipeline
- End-to-end: Read → Clean → Transform → Filter → Aggregate → Write
- Output as Parquet (full data) and CSV (summaries)

### 11. Best Practices
- Use `show()` not `collect()`
- Explicit schemas over `inferSchema`
- Filter early, cache wisely
- Prefer Parquet for analytics

## Output Files

```
output/
├── cleaned_parquet/     # Full cleaned data (Parquet)
├── data_csv/            # CSV format (for comparison)
├── data_parquet/        # Parquet format (for comparison)
├── category_summary/    # CSV - stats per category
└── country_summary/     # CSV - stats per country
```

