## Storage Systems

* **Goal 1**
    * **Chosen Storage:** Data Warehouse (e.g., Snowflake, BigQuery)
    * **Justification:** Training machine learning models requires massive volumes of structured, historical data. A columnar Data Warehouse is optimized for handling complex, heavy-read queries across millions of rows (such as aggregating years of patient treatment timelines) much faster than a traditional transactional database.
* **Goal 2**
    * **Chosen Storage:** Vector Database (e.g., Pinecone, Milvus, or pgvector)
    * **Justification:** Large Language Models (LLMs) cannot query standard relational tables directly to understand context. Patient notes and histories must be converted into mathematical representations (embeddings). A Vector DB is purpose-built to store these embeddings and perform rapid semantic similarity searches, which is the backbone of the Retrieval-Augmented Generation (RAG) system answering the doctors' queries.
* **Goal 3**
    * **Chosen Storage:** Data Warehouse (e.g., Snowflake, BigQuery)
    * **Justification:** Monthly reporting requires complex aggregations (e.g., calculating average bed occupancy, grouping costs by department). Because the Data Warehouse already acts as the central repository for cleaned historical data from the EMR and HMS, it serves as the perfect "single source of truth" to power Business Intelligence (BI) dashboards.
* **Goal 4**
    * **Chosen Storage:** Time-Series Database (TSDB) (e.g., InfluxDB, TimescaleDB)
    * **Justification:** ICU monitors generate relentless, continuous streams of high-frequency data where the exact timestamp is the most critical attribute. Traditional relational databases bottleneck under this intense write-load. A TSDB is engineered specifically for massive write-throughput and provides built-in functions for downsampling and time-based querying.

## OLTP vs OLAP Boundary

In this architecture, the boundary between the transactional system (OLTP) and the analytical system (OLAP) is firmly drawn at the Ingestion Layer. The hospital's live Electronic Medical Records (EMR), Hospital Management System (HMS), and live ICU monitors make up the **OLTP** side. These systems are optimized for fast, day-to-day, row-level operations—like admitting a patient, updating a billing record, or displaying a live heartbeat. 

Once the data is extracted via nightly ETL pipelines or streamed through Kafka into the Data Warehouse, Vector DB, and historical partitions of the TSDB, it crosses into the **OLAP** side.

## Trade-offs

**Trade-off:**
By strictly separating our OLTP and OLAP systems using standard Batch ETL pipelines for the EMR and HMS data, we introduce data latency. The AI models and the LLM querying engine will only have access to patient notes and records that are as fresh as the last ETL run (often 12 to 24 hours old). 

**Mitigation:**
For predicting readmission risk or running monthly management reports, a 24-hour delay is perfectly acceptable. However, if a doctor asks the LLM, "Did the patient have a cardiac event this morning?", stale data becomes a clinical risk. To mitigate this, we could upgrade the ingestion layer from batch ETL to **Change Data Capture (CDC)** or micro-batching. CDC would monitor the live EMR database logs and stream updates to the Vector DB and Data Warehouse in near real-time (minutes instead of hours), bridging the gap between operational stability and analytical freshness.
