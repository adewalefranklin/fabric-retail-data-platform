# Power BI — Retail Product Performance

This directory contains the Power BI analytical layer of the Microsoft Fabric Retail Data Engineering Platform.

The report consumes analytics-ready Gold-layer data produced through the Fabric Medallion Architecture.

## Dashboard

The **Retail Product Performance** dashboard provides visibility into key retail performance and inventory metrics, including:

- Total products
- Average customer review rating
- Total inventory stock
- Critical inventory risk
- Product-level review performance
- Inventory risk by product
- Average rating by category

Interactive filters allow users to analyze product performance by:

- Category
- Brand
- Availability status

## Data Source

The Power BI report consumes analytics-ready Gold-layer tables from the Microsoft Fabric Lakehouse.

```text
Fabric Gold Lakehouse
        │
        ▼
Power BI Semantic Model
        │
        ▼
Retail Product Performance Dashboard
```

This keeps the analytical consumption layer separated from the ingestion and transformation layers of the platform.

## Files

### `Retail_Product_Performance.pbix`

Complete Power BI Desktop report containing the semantic model, relationships, report configuration and interactive dashboard.

### `Retail_Product_Performance.pbit`

Reusable Power BI template containing the report structure and model definition without serving as the primary project report artifact.

## Data Model

The Power BI semantic model follows a simplified star-schema design centered around the product dimension.

The model supports analysis of:

- Product performance
- Customer review performance
- Inventory levels
- Inventory risk
- Category performance
- Brand performance

## Dashboard Preview

A screenshot of the completed **Retail Product Performance** dashboard is available in the repository's `images/` directory.

## Architecture Integration

Power BI represents the final analytical consumption layer of the platform.

```text
DummyJSON REST API
        │
        ▼
Fabric Data Pipeline
        │
        ▼
Bronze Lakehouse
        │
        ▼
Bronze-to-Silver
PySpark Transformation
        │
        ▼
Silver Lakehouse
        │
        ▼
Silver-to-Gold
PySpark Transformation
        │
        ▼
Gold Lakehouse
        │
        ▼
Power BI Semantic Model
        │
        ▼
Retail Product Performance
Dashboard
```

The resulting architecture demonstrates an end-to-end Microsoft Fabric data engineering workflow covering ingestion, transformation, data quality, orchestration, analytical modelling and business intelligence consumption.
