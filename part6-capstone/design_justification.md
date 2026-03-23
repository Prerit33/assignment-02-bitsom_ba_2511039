## Storage Systems

The architecture uses a combination of storage systems tailored to the four core goals of the hospital network.

For **predicting patient readmission risk**, a **centralized data warehouse** is used to store historical treatment data, e.g., columnar storage like BigQuery/Redshift/Snowflake. This is ideal because it supports large-scale analytical queries, joins across multiple datasets and efficient feature extraction for machine learning models.

For **natural language querying of patient history**, a **hybrid approach** is used. Structured patient data resides in the data warehouse, while a **document-oriented database** or a **vector database** is used to enable semantic search. This allows doctors to ask questions in plain English, which are translated into queries or embeddings for fast retrieval of relevant patient records.

For **monthly reporting**, a **data mart layer** is used. This contains aggregated, precomputed metrics such as bed occupancy rates and department-wise costs. It ensures fast query performance for dashboards and reporting tools (e.g., Power BI, Tableau), without overloading the core warehouse.

For **real-time ICU vitals streaming**, a **time-series database** (e.g., InfluxDB, TimescaleDB) or a **streaming platform with storage** (e.g., Kafka + Cassandra) is used. These systems are optimized for high-ingestion rates, time-indexed queries, and real-time monitoring, making them suitable for continuous streams of patient vitals.

## OLTP vs OLAP Boundary

The **OLTP (Online Transaction Processing)** layer consists of operational systems such as the EHR system, lab systems, ICU monitoring devices, and billing systems. These systems handle real-time transactions—patient admissions, test results, and live vitals—and require high availability, low latency, and data integrity. The boundary between OLTP and OLAP is established at the **data ingestion and ETL/stream processing layer**. This layer extracts data from transactional systems and transforms it into formats suitable for analytics.

## Trade-offs

A key trade-off in this design is between **real-time processing and system complexity**.

Supporting real-time ICU data streaming alongside batch analytics introduces architectural complexity. Maintaining both streaming pipelines (for vitals and alerts) and batch pipelines (for historical analysis and reporting) increases operational overhead, requires more infrastructure, and complicates data consistency. To mitigate this, a **unified data processing framework** (such as a Lambda or Kappa architecture) can be adopted. For example, using a streaming-first approach (Kappa architecture) with tools like Kafka and stream processors can reduce duplication between batch and streaming pipelines.



