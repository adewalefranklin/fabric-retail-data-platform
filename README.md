# Microsoft Fabric Retail Data Engineering Platform

End-to-end retail data engineering platform built with Microsoft Fabric, PySpark, Delta Lake, OneLake, Fabric Data Factory and Power BI.

The project demonstrates the ingestion, transformation, validation, orchestration and analytical consumption of semi-structured REST API data using a Medallion Architecture.

## Architecture


                         ┌─────────────────────┐
                         │   DummyJSON REST API │
                         │     /products        │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │  Fabric Copy Job    │
                         │  REST Ingestion     │
                         └──────────┬──────────┘
                                    │
                                    ▼
┌──────────────────────────────────────────────────────────────┐
│                     BRONZE LAYER                             │
│                    lh_retail_bronze                          │
│                                                              │
│   Files/raw/products/products.json                           │
│   Raw API payload preserved                                  │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               │ nb_bronze_to_silver_products
                               │ PySpark
                               ▼
┌──────────────────────────────────────────────────────────────┐
│                     SILVER LAYER                             │
│                    lh_retail_silver                          │
│                                                              │
│   dbo.products                                               │
│   dbo.product_reviews                                        │
│                                                              │
│   • Array explosion                                          │
│   • Struct flattening                                        │
│   • Schema standardization                                   │
│   • Null handling                                            │
│   • Data-quality validation                                  │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               │ nb_silver_to_gold_retail
                               │ PySpark
                               ▼
┌──────────────────────────────────────────────────────────────┐
│                      GOLD LAYER                              │
│                     lh_retail_gold                           │
│                                                              │
│   dbo.dim_product                                            │
│   dbo.brand_category_performance                             │
│   dbo.inventory_risk                                         │
│   dbo.customer_review_performance                            │
│   dbo.category_performance_kpi                               │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │      Power BI        │
                    │ Retail Product       │
                    │ Performance Dashboard│
                    └──────────────────────┘


ORCHESTRATION
──────────────────────────────────────────────────────────────

Copy Job
   │ Success
   ▼
Bronze_to_Silver
   │ Success
   ▼
Silver_to_Gold

The complete workflow is orchestrated through a Microsoft Fabric Data Pipeline:

```text
Copy Job
   ↓
Bronze_to_Silver
   ↓
Silver_to_Gold
```

Downstream activities execute only after the preceding activity succeeds.

## Technology Stack

* Microsoft Fabric
* Fabric Data Factory
* OneLake
* Fabric Lakehouse
* Apache Spark / PySpark
* Delta Lake
* REST API
* Fabric Notebooks
* Fabric Data Pipelines
* Power BI

## Data Source

Retail product data is retrieved from the DummyJSON REST API.

The source contains product information including:

* Product identifiers
* Product names
* Categories
* Brands
* Prices
* Discounts
* Ratings
* Inventory levels
* Product dimensions
* Metadata
* Customer reviews

The API response contains nested structures and arrays, providing a realistic semi-structured ingestion and transformation scenario.

## Bronze Layer

The Bronze layer preserves the raw API response with minimal modification.

**Lakehouse**

`lh_retail_bronze`

**Landing path**

```text
Files/raw/products/products.json
```

Fabric Copy Job retrieves the REST API payload and stores the original JSON response in OneLake.

Keeping the original source representation provides traceability and allows transformations to be reproduced from the raw data.

## Silver Layer

The Silver layer converts the nested JSON into structured, validated Delta tables.

**Lakehouse**

`lh_retail_silver`

**Notebook**

`nb_bronze_to_silver_products`

### Transformations

PySpark is used to:

* Read the raw JSON payload
* Validate the expected source schema
* Explode the `products` array
* Flatten nested product structures
* Flatten product dimensions and metadata
* Standardize column names
* Handle missing brand values
* Explode customer reviews
* Preserve `product_id` for product-review relationships
* Validate critical business and technical rules

### Silver Tables

#### `dbo.products`

Grain:

> One row per product.

Contains cleaned and flattened product information.

#### `dbo.product_reviews`

Grain:

> One row per customer review.

Each review retains its parent `product_id`, producing a one-to-many relationship between products and reviews.

## Data Quality

Critical data-quality checks are implemented before Silver data is persisted.

Checks include:

* Required source schema validation
* Non-empty product dataset
* Null product ID detection
* Duplicate product ID detection
* Negative price detection
* Negative inventory detection
* Rating range validation
* Critical-column completeness checks

Critical failures raise exceptions within the notebook.

This causes the Bronze-to-Silver pipeline activity to fail and prevents downstream Gold transformations from executing on invalid data.

## Gold Layer

The Gold layer contains analytics-ready datasets designed around simulated retail business requirements.

**Lakehouse**

`lh_retail_gold`

**Notebook**

`nb_silver_to_gold_retail`

### `dbo.brand_category_performance`

Supports management analysis of brand performance within product categories.

Metrics include:

* Average price
* Total stock
* Average rating
* Average discount
* Dense ranking of brands within categories

PySpark window functions partition the dataset by category and rank brands according to average rating.

### `dbo.inventory_risk`

Supports inventory and replenishment monitoring.

Products are classified using business rules into:

* Critical
* Low
* Medium
* Healthy

### `dbo.customer_review_performance`

Supports customer-experience analysis.

Individual reviews are classified as Good, Fair or Bad and aggregated to product level.

Metrics include:

* Average review rating
* Total reviews
* Good reviews
* Fair reviews
* Bad reviews
* Overall review-performance classification

### `dbo.category_performance_kpi`

Provides commercial category-level KPIs including:

* Product count
* Average price
* Total stock
* Average rating
* Average discount

### `dbo.dim_product`

Provides a clean product dimension for BI filtering and analytical slicing.

Attributes include:

* Product surrogate key
* Source product ID
* SKU
* Product title
* Category
* Brand
* Availability status

A deterministic surrogate key is generated using `xxhash64`.

## Business Requirements

The Gold layer was designed around five simulated business requirements.

**Management**

Identify the strongest-performing brands and categories based on price, inventory, ratings and discount levels.

**Operations**

Identify products with low inventory and potential replenishment risk.

**Customer Experience**

Determine which products receive strong, average or weak customer reviews.

**Commercial**

Compare category-level price, stock, rating, discount and product-count KPIs.

**BI / Reporting**

Provide clean product attributes for dashboard filtering and analytical slicing.

## Orchestration

Fabric Data Pipeline orchestrates the complete workflow.

```text
REST API
   ↓
Copy Job
   ↓
Bronze
   ↓
Bronze_to_Silver Notebook
   ↓
Silver
   ↓
Silver_to_Gold Notebook
   ↓
Gold
```

Success dependencies ensure that downstream transformations execute only after upstream processing completes successfully.

A complete end-to-end pipeline execution was validated successfully.

## Power BI

Power BI Desktop consumes the Fabric Gold layer through OneLake.

The Retail Product Performance dashboard contains:

### KPI Cards

* Total Products
* Average Review Rating
* Total Stock
* Critical Inventory Risk

### Analytical Visuals

* Product Review Performance
* Inventory Risk by Product
* Average Rating by Category

### Interactive Filters

* Category
* Brand
* Availability Status

The semantic model uses `dim_product` to support product-level filtering across the analytical datasets.

## Idempotency

Silver and Gold datasets currently use overwrite semantics.

This makes repeated executions deterministic for the current full-snapshot API source and prevents duplicate accumulation between pipeline runs.

For larger production workloads, the architecture could be extended with incremental processing using:

* Delta `MERGE`
* Source watermarks
* Change timestamps
* CDC patterns
* Incremental Gold recalculation

## Future Improvements

Potential production enhancements include:

* Incremental ingestion and Delta `MERGE`
* Historical product tracking using SCD Type 2
* Pipeline scheduling
* Centralized logging and monitoring
* Data-quality audit tables
* Environment-specific DEV / TEST / PROD deployment
* CI/CD integration
* Fabric deployment pipelines
* Additional semantic-model measures

## Project Outcome

The project demonstrates an end-to-end Microsoft Fabric data engineering workflow covering:

**REST ingestion → OneLake → Medallion Architecture → PySpark → Delta Lake → Data Quality → Business Modelling → Pipeline Orchestration → Power BI**

It provides a compact implementation of a modern Lakehouse architecture while maintaining clear separation between raw ingestion, curated data and business-facing analytical models.
