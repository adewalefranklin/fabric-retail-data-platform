# Power BI — Retail Product Performance

This directory contains the Power BI analytical layer of the Microsoft Fabric Retail Data Engineering Platform.

The report consumes analytics-ready Gold-layer data produced by the Fabric Medallion Architecture.

## Dashboard

The Retail Product Performance dashboard provides visibility into:

- Total products
- Average customer review rating
- Total inventory stock
- Critical inventory risk
- Product-level review performance
- Inventory risk by product
- Average rating by category

Interactive filters allow users to analyze performance by:

- Category
- Brand
- Availability status

## Data Source

The Power BI report consumes Gold-layer tables from the Microsoft Fabric Lakehouse.

```text
Fabric Gold Lakehouse
        │
        ▼
Power BI Semantic Model
        │
        ▼
Retail Product Performance Dashboard
