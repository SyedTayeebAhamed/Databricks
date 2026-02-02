# NYC Taxi Data Pipeline (Bronze → Silver → Gold)

This project implements a robust data engineering pipeline in Databricks for processing NYC taxi trip data. It follows a multi-layered architecture using Delta Lake tables and Git-integrated notebooks.

---

---

## 🔄 Pipeline Overview

### 🟫 Bronze Layer
- Raw ingestion from external sources
- Stored as Delta table: `workspace.raw_data_taxi.bronze_taxi_data`

### 🟪 Silver Layer
- Cleans and validates data:
  - Removes nulls, negative values, and zero distances
  - Enforces schema: casts numeric fields (`passenger_count`, `trip_distance`, etc.)
  - Parses timestamps with proper format: `yyyy-MM-dd'T'HH:mm:ss.SSS`
  - Adds derived columns: `trip_duration_minutes`, `trip_date`, `pickup_hour`
  - Generates `trip_id` using SHA-256 hash
- Deduplicates trips and normalizes `payment_type`
- Stores output in: `workspace.raw_data_taxi.silver_taxi_data`

### 🟨 Gold Layer
- Aggregates and summarizes data for BI consumption
- Optimized for Power BI dashboards
- Stored in: `workspace.raw_data_taxi.gold_taxi_data`

---

## 🧠 Metadata Checkpointing

- Table: `workspace.raw_data_taxi.pipeline_metadata_checkpoint`
- Tracks `last_processed_ts` for incremental loads
- Updated after each successful Silver → Gold run

---
## ⏰ Scheduler Trigger
- The pipeline is automated with a **daily scheduler trigger** in Databricks Jobs.
- Every 24 hours, the workflow runs:
  1. Bronze ingestion
  2. Silver cleaning and schema enforcement
  3. Gold aggregation and dashboard refresh
- Ensures data is always up-to-date for analytics and BI reporting.
