# Week 1 - Pandas Data Cleaning

## Task
Clean and preprocess a raw CSV dataset using pandas.

## Dataset
- **Original file:** [Combined Dataset](https://www.kaggle.com/datasets/anvitkumar/shopping-dataset?select=Combined_dataset.csv) (Product data — 1000 rows, 24 columns)
- **Cleaned file:** `Cleaned_dataset.csv` (886 rows, 11 columns)

## What I Did

### 1. Data Exploration
- Used `head()` and `tail()` to view first and last rows.
- Checked the shape (1000 rows × 24 columns).
- Listed all column names and their data types using `dtypes`.

### 2. Handled Missing Values
- Found missing values in 6 columns: `discount`, `seller_name`, `what_customers_said`, `videos`, `seller_information`, and `variations`.
- Filled `discount` with 0 (means no discount was given) — this is the only column with missing values that we actually keep in the final dataset.
- The other 5 columns (`seller_name`, `what_customers_said`, `videos`, `seller_information`, `variations`) are dropped during column selection, so there's no need to fill their missing values.

### 3. Basic Operations (Filter Rows, Select Columns)
- **Filtered rows:** Kept only products with `rating >= 1.0` and `initial_price > 0`.
- **Selected 9 base columns:** `product_id`, `title`, `product_description`, `rating`, `ratings_count`, `initial_price`, `discount`, `currency`, `category`.

### 4. Removed Duplicates
- Checked for duplicate rows — 0 duplicates were found in this dataset.

### 5. Created Derived Columns
- Created 2 new columns from the existing data:
  - `discount_amount` = `initial_price × (discount / 100)` — the actual rupee savings on each product.
  - `value_score` = `rating × ratings_count` — a simple composite score that combines product quality with popularity. A product with 4.5 stars and 1000 reviews scores higher than a 5-star product with only 5 reviews.
- This brings the total to 11 columns.

### 6. Saved Cleaned Dataset
- Exported the final cleaned DataFrame to `Cleaned_dataset.csv`.

## Final Output
- **886 rows × 11 columns** (from original 1000 × 24)
- No missing values
- No duplicates
- 2 new derived columns added

## How to Run
```bash
pip install pandas
jupyter notebook week1.ipynb
```
Run all cells in order from top to bottom.
