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

# Activities
1. Copy Job

Ingests retail product data from the REST API into the Bronze layer of the Fabric Lakehouse.

2. Bronze-to-Silver

Executes the PySpark transformation notebook responsible for schema validation, data-quality checks, flattening semi-structured data, cleaning and transforming the Bronze data into the Silver layer.

3. Silver-to-Gold

Executes the PySpark transformation notebook responsible for producing analytics-ready Gold tables used for downstream reporting and analysis.

Orchestration

### The activities are connected using success dependencies:

Copy Job
    ↓ success
Bronze-to-Silver
    ↓ success
Silver-to-Gold
The above activities ensure that downstream transformations execute only after the preceding processing stage completes successfully.

# Fabric Export

pl_retail_ingestion.zip contains the pipeline exported directly from Microsoft Fabric and can be used as the pipeline artifact for this project.


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



