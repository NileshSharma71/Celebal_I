# Week 5 – Spark Data Processing & Analysis

## Objective

Understand Spark fundamentals and perform data cleaning, transformation, and aggregation using PySpark DataFrames on an E-Commerce Customer Engagement dataset.

## Dataset

- **Source**: [Kaggle – E-Commerce Customer Engagement](https://www.kaggle.com/datasets/noir1112/e-commerce-customer-engagement)
- **Size**: 10,000 rows × 23 columns
- **File**: `ecommerce_customer_data.csv`

### Key Columns

| Column | Description |
|--------|-------------|
| CustomerID | Unique customer identifier |
| Age | Customer age |
| Gender | Male, Female, Other, Prefer not to say |
| IncomeLevel | Low, Medium, High, Very High |
| Country / City | Geographic info |
| TotalPurchases | Number of purchases |
| AverageOrderValue | Average order amount |
| CustomerLifetimeValue | Lifetime value |
| FavoriteCategory | Preferred shopping category |
| AverageSatisfactionScore | 0-10 satisfaction rating |
| RepeatCustomer | Yes/No |
| PremiumMember | Yes/No |

## Setup

```bash
# create virtual environment
python3 -m venv venv

# activate it
source venv/bin/activate

# install pyspark
pip install pyspark
```

> **Note**: Java (JDK 8+) must be installed. Verified with OpenJDK 21.

## Running the Script

```bash
source venv/bin/activate
python spark_ecommerce_analysis.py
```

### Option B: Running the Jupyter Notebook
1. Open the folder `Week_5` in VS Code.
2. Open `spark_ecommerce_analysis.ipynb`.
3. In the top right, select the kernel **Python 3 (venv)** which corresponds to the virtual environment we created (`venv/bin/python`).
4. Click **Run All** or execute cell-by-cell.

## Topics Covered

### 1. Spark Fundamentals
Spark is much faster than MapReduce because it processes data in-memory rather than writing intermediate steps to disk. Spark DataFrames are immutable, meaning every transformation creates a new DataFrame (which ensures fault tolerance and easy parallel processing).

### 2. Data Cleaning
- Removed duplicate rows
- Converted `"nan"` strings to proper null values
- Removed rows with missing CustomerIDs
- Fixed invalid ages (negative, zero, > 100)
- Capped extreme outliers in AverageOrderValue
- Filled numeric nulls with column means
- Filled categorical nulls with "Unknown"

### 3. Schema Modifications
- Cast Age, TotalPurchases to IntegerType
- Parsed RegistrationDate to DateType
- Renamed columns for clarity (e.g., `CustomerLifetimeValue` → `CLV`)

### 4. Filtering
- Age range filtering (18-65)
- Category-based filtering (Electronics)
- Region/country filtering (USA)
- Compound conditions (Premium + High Income + High Satisfaction)

### 5. Aggregation Functions
- count, sum, avg, min, max across dataset
- Per-category and per-country statistics
- Estimated revenue calculation (AvgOrderValue × TotalPurchases)

### 6. GroupBy with Conditions
- Country-level aggregations with satisfaction thresholds
- Income level analysis
- Category engagement stats (filtered by customer count > 100)
- Gender-wise repeat customer analysis

### 7. Wide Transformations & Shuffle
- Narrow vs Wide transformation explanation
- Execution plan analysis showing shuffle stages
- Repartition vs Coalesce demonstration

### 8. Complete Pipeline
- End-to-end: Load → Clean → Transform → Filter → Aggregate → Save
- Output saved as Parquet (cleaned data) and CSV (summaries)

## Output Files

```
output/
├── cleaned_data/          # Parquet format
├── category_summary/      # CSV - stats per category
└── country_summary/       # CSV - stats per country
```

## Author

Nilesh Sharma
