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
