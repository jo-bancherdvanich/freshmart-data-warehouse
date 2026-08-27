# Source Data

The five raw operational CSV files provided for the FreshMart data warehouse project. These are the inputs to the Power Query ETL process; the cleaning steps applied to each are documented in the design report.

- `sales.csv` — 17,697 transaction-line records (the fact table source)
- `customer.csv` — 515 rows / 500 distinct customers (15 CustomerIDs appear twice)
- `product.csv` — 60 products
- `promotion.csv` — 21 promotions
- `store.csv` — 12 stores

Note: these files contain deliberate data-quality issues (duplicates, inconsistent casing, mixed date formats, unmatched keys) which were identified in the audit and cleaned during ETL.
