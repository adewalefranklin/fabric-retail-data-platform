# Fabric Data Pipeline

The `pl_retail_ingestion` pipeline orchestrates the end-to-end retail data processing workflow in Microsoft Fabric.

## Pipeline Flow

```text
REST API
   │
   ▼
Copy Job
   │
   ▼
Bronze Lakehouse
   │
   ▼
Bronze-to-Silver Notebook
   │
   ▼
Silver Lakehouse
   │
   ▼
Silver-to-Gold Notebook
   │
   ▼
Gold Lakehouse
```

## Activities

### 1. Copy Job

Ingests retail product data from the REST API into the Bronze layer of the Fabric Lakehouse.

### 2. Bronze-to-Silver

Executes the PySpark transformation notebook responsible for:

- Schema validation
- Data-quality checks
- Flattening semi-structured data
- Data cleaning and transformation
- Writing curated data into the Silver Lakehouse

### 3. Silver-to-Gold

Executes the PySpark transformation notebook responsible for transforming curated Silver data into analytics-ready Gold tables used for downstream reporting and analysis.

## Orchestration

The activities are connected using success dependencies:

```text
Copy Job
    │
    ▼ success
Bronze-to-Silver
    │
    ▼ success
Silver-to-Gold
```

This ensures that downstream transformations execute only after the preceding processing stage completes successfully.

## Fabric Export

`pl_retail_ingestion.zip` contains the pipeline exported directly from Microsoft Fabric and represents the deployable pipeline artifact for this project.

## Repository Structure

```text
fabric-retail-data-platform/
│
├── README.md
│
├── notebooks/
│   ├── nb_bronze_to_silver_products.ipynb
│   └── nb_silver_to_gold_retail.ipynb
│
├── pipeline/
│   ├── README.md
│   └── pl_retail_ingestion.zip
│
├── docs/
│   └── ...
│
└── images/
    └── ...
```
