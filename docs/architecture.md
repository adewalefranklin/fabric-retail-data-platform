Source
  ↓
REST GET /products

Ingestion
  ↓
Fabric Copy Job

Bronze
  ↓
Raw JSON / OneLake

Transformation
  ↓
PySpark
  ↓
Schema validation
Nested JSON processing
Data-quality gates

Silver
  ↓
products
product_reviews

Business transformation
  ↓
Aggregations
Joins
Business rules
Window functions
Dimensional modelling

Gold
  ↓
5 analytics-ready Delta tables

Consumption
  ↓
Power BI
