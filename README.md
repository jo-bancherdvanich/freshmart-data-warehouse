# 🛒 FreshMart Data Warehouse & Sales Dashboard

An end-to-end business intelligence project for **FreshMart**, a fictional Australian supermarket chain. It takes five raw operational CSV files, audits and cleans them, builds a **Kimball star schema** data warehouse, and delivers an executive **sales performance dashboard** on top of it.

Built for **ISYS6013 Business Intelligence and Analytics** at Curtin University by **Waranyu Bancherdvanich**.

---

![Power BI](https://img.shields.io/badge/Built_with-Power_BI-F2C811?logo=powerbi&logoColor=black)
![Power Query](https://img.shields.io/badge/ETL-Power_Query-217346)
![DAX](https://img.shields.io/badge/Measures-DAX-blue)
![Dimensional Modelling](https://img.shields.io/badge/Model-Star_Schema-orange)

---

![FreshMart Sales Performance Dashboard](images/dashboard-overview.png)

📖 **[Read the full design report here on GitHub](docs/full-report.md)** — no download needed. Kimball four-stage documentation, star schema, and the data quality audit.

📄 *Also available as a [PDF](docs/freshmart-data-warehouse-report.pdf), which includes the Power BI model view and Power Query screenshots.*

---

## 🧩 The Project

FreshMart's core business activity is selling products to customers. This project models that activity as a **data warehouse**, then builds analytics on top so the business can track sales by product, customer, store, promotion, and time.

It covers the full BI pipeline:
1. **Design** the dimensional model (Kimball method)
2. **Clean** the raw data (Power Query ETL)
3. **Model** it as a star schema in Power BI
4. **Analyse** it through an executive dashboard

---

## 📊 The Dashboard

An executive overview of sales, profitability, product mix, and store performance, with a slicer panel for date, year, quarter, region, store format, and product category.

Key elements:
- **KPI cards** — Total Revenue, Gross Margin %, Gross Profit
- **Store Revenue by Region** — performance across South, West, North, East
- **Revenue by Product Category and Subcategory** — what drives sales
- **Sales Volume by Day of Week and by Month** — demand patterns
- **Revenue This Year vs Same Period Last Year** — YTD vs PYTD time intelligence (DAX)

---

## ⭐ Star Schema (the foundation)

A single **Sales fact table** (17,697 transaction lines) sits at the centre, surrounded by four dimensions plus a generated Date dimension:

| Table | Role | Rows |
|-------|------|------|
| `Sales` | Fact (transaction line grain) | 17,697 |
| `Customer` | Dimension | 500 (515 raw, 15 duplicates removed) |
| `Product` | Dimension | 60 |
| `Store` | Dimension | 12 |
| `Promotion` | Dimension | 21 |
| `Date` | Dimension (generated calendar) | — |

```mermaid
erDiagram
    Customer  ||--o{ Sales : "CustomerID"
    Product   ||--o{ Sales : "ProductID"
    Store     ||--o{ Sales : "StoreID"
    Promotion ||--o{ Sales : "PromotionID"
    Date      ||--o{ Sales : "DateKey"

    Sales {
        text TransactionID
        text TransactionLineID PK
        date Date
        int DateKey FK
        text CustomerID FK
        text ProductID FK
        text StoreID FK
        text PromotionID FK
        int QuantitySold "additive"
        decimal UnitPrice "non-additive"
        decimal DiscountAmount "additive"
        decimal COGS "additive"
    }
    Customer {
        text CustomerID PK
        text AgeGroup
        text Gender
        text LoyaltyTier
        text PostCode
        date MemberSince
    }
    Product {
        text ProductID PK
        text ProductName
        text Brand
        text Category
        text Subcategory
        text SupplierID
        decimal UnitCost
        bool IsOrganic
    }
    Store {
        text StoreID PK
        text StoreName
        text City
        text State
        text Region
        text StoreFormat
        date OpeningDate
        int Store_Area_sqm
    }
    Promotion {
        text PromotionID PK
        text PromotionName
        text PromotionType
        date StartDate
        date EndDate
        decimal DiscountRate
    }
    Date {
        int DateKey PK
        date Date
        int Year
        int Quarter
        int Month
        text DayOfWeek
    }
```

*Star schema: one central `Sales` fact table joined 1:M to five dimensions. Full-size Power BI model view is in the [design report](docs/freshmart-data-warehouse-report.pdf).*

The model follows Kimball's four-stage method: **business process** (retail sales), **grain** (one row per product per customer per store per date per promotion, the most detailed level so no analytical option is lost), **dimensions** (Product, Customer, Store, Promotion, Date), and **facts** (QuantitySold, UnitPrice, DiscountAmount, COGS).

**Fact classification:** QuantitySold, DiscountAmount, and COGS are **additive**; UnitPrice is **non-additive** (used in calculations, not summed); there are no semi-additive facts, since this is transaction-level rather than snapshot data.

---

## 🧹 Data Quality Audit & ETL

Each source table was audited and cleaned in **Power Query** before modelling. The real issues found and fixed:

| Table | Problem | Fix |
|-------|---------|-----|
| Customer | 15 CustomerIDs appear twice, with slightly different membership dates — 30 rows involved | Removed 15 duplicate rows by CustomerID, leaving 500 distinct customers |
| Product | Inconsistent category spelling/casing (`dairy`, `Diary`, `bakery`) | Standardised category labels via value replacement |
| Promotion | `N/A` promotion type and blank start/end dates on the no-promotion record | Replaced `N/A` with "No Promotion"; kept blank dates as null to preserve the date type |
| Sales | Mixed date formats in the raw file | Confirmed the column imported correctly as a Date type |
| Sales | 93 rows with `C9999`, a CustomerID matching no customer | Created a `CustomerID_Clean` column mapping `C9999` to "Not Applicable" |
| Store | `SquareMetres` column name not report-friendly | Renamed to `Store Area (sqm)` |

The full audit and the Power Query Applied Steps for each table are in the [design report](docs/freshmart-data-warehouse-report.pdf).

---

## 🛠️ Tools & Techniques

**Power BI** (data model, dashboard, relationships) · **Power Query** (ETL, data cleaning) · **DAX** (KPIs and YTD/PYTD time intelligence) · **Kimball dimensional modelling** (star schema, grain, fact classification) · data quality auditing

---

## 📁 Repository Contents

- `freshmart-data-warehouse.pbix` — the Power BI file (star schema model, Power Query ETL, and the dashboard)
- `docs/full-report.md` — the full design report, readable on GitHub
- `docs/freshmart-data-warehouse-report.pdf` — the design report as a PDF (with Power BI screenshots)
- `data/` — the five raw source CSVs (customer, product, promotion, sales, store)
- `images/` — the dashboard screenshot

---

## 📊 What This Demonstrates

- Building a complete BI solution end to end: design then clean then model then analyse
- Applying a recognised industry method (Kimball) rather than ad-hoc design
- Auditing data quality and making transparent, justified cleaning decisions
- Reasoning about grain and fact additivity, the decisions that make a warehouse reusable
- Turning a clean model into an executive dashboard with DAX time intelligence

---

## 📫 Author

**Waranyu (JO) Bancherdvanich** — [Portfolio](https://jo-bancherdvanich.github.io/waranyu-CV/) · [LinkedIn](https://www.linkedin.com/in/waranyu-ban) · [GitHub](https://github.com/jo-bancherdvanich)
