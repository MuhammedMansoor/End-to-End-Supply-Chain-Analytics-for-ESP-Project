# End-to-End-Supply-Chain-Analytics-for-ESP-Project
An end-to-end analytics framework tracking the ESP (Electrical Submersible Pump) lifecycle—from procurement and sub-inventory management to rig deployment and repair. Built with Power Automate, and Power BI, it forecasts spare parts demand, automates dynamic safety stock, reduces inventory variances, and prevents costly rig downtime.
#saved 1000 hrs of manual work in report generation per year
*An enterprise-grade, self-service analytics platform for tracking equipment inventory, aging, purchasing, and material movements across multiple oilfield service projects and regional operations.*

---

## 1. Project Overview

This analytics platform consolidates operational data from **Oracle Fusion Cloud**—processing 20 tables containing approximately 10 million rows—into an optimized Power BI semantic model. The solution delivers cross-functional visibility into inventory health, purchasing, material aging, and demand forecasting across multiple regional teams, departments, and project sub-inventories.

The model answers critical operational questions:
- What is our current stock holding, and how does month-over-month movement compare against targets?
- What is the aging profile of held inventory, and where is capital locked in slow-moving buckets?
- How does actual material issuance compare against project forecasts across departments?
- What is our pending procurement exposure across open POs and items undergoing quality inspection?

---

## 2. ETL Architecture & Pipeline

Data ingestion is orchestrated using **Power Automate** and **Power BI Dataflows** to extract 20 core transactional and master tables directly from **Oracle Fusion**, scaling up to ~10M records.

┌──────────────────────────────────────┐
│        ORACLE FUSION CLOUD           │
│   (20 Tables / ~10M Total Rows)     │
└──────────────────┬───────────────────┘
│
▼
┌──────────────────────────────────────┐
│       POWER AUTOMATE PIPELINE        │
│   - Automated extract generation     │
│   - Scheduled file export & staging  │
└──────────────────┬───────────────────┘
│
▼
┌──────────────────────────────────────┐
│       POWER QUERY (M) / DATAFLOWS    │
│   - Header trimming & schema alignment│
│   - Data type casting & de-duplication│
│   - Rolling master/stage merging     │
│   - Composite key construction       │
└──────────────────┬───────────────────┘
│
▼
┌──────────────────────────────────────┐
│      POWER BI SEMANTIC MODEL         │
│   - High-performance Star Schema     │
│   - VertiPaq memory-optimized tables │
│   - Department & Region RLS Layer    │
└──────────────────────────────────────┘


**Key Transformation & Ingestion Logic:**
- **Rolling Master/Stage Merge:** Historical trend tables (Stock & Aging) use incremental updates where closed monthly snapshots remain locked and only current-period delta extracts process, preserving query performance over 10M rows.
- **Composite Key Generation:** Concatenates Organization and Sub-inventory codes to establish clean 1:M joins between conformed dimensions and high-volume fact tables.
- **Transaction Standardization:** Maps granular Oracle Fusion transaction codes into standardized business actions (Receipts, Issues, Transfers, Adjustments).

---

## 3. Data Model & Security Architecture

### Fact & Transactional Tables (~10M Rows Consolidated)
- **Material Transactions (Final):** Primary fact table capturing all historical inventory movements.
- **Stock Trend (Final):** Monthly snapshots of on-hand inventory balances and valuations.
- **Aging Trend (Final):** Time-bucketed snapshots quantifying inventory holding duration.
- **On-Hand Inventory:** Real-time point-in-time stock availability and cost valuation.
- **Open Purchase Orders & Inspection Queue:** Pending inbound supply and items in QA inspection.
- **Forecast vs. Actual Issuance:** Aggregated monthly issuance against annual project estimates.

### Row-Level Security (RLS) Implementation
The model enforces dynamic, granular security roles at the published dataset level to restrict data access based on user credentials:
- **Department-Level RLS:** Restricts visibility so functional teams (e.g., Procurement, Quality, Field Operations) access only department-relevant material categories and transactions.
- **Region-Wise Team RLS:** Filters fact tables based on user region mappings, ensuring field teams only view sub-inventories and projects within their geographic jurisdiction.

---

## 4. Key DAX Measures & KPIs

- **Inventory Valuation & Variance:** Dynamic measures calculating stock value, prior-month variances (KPI directional indicators with dynamic color formatting), and variance against target stock levels.
- **Stock Adequacy & Run-Rate:** Blended 6-month vs. 12-month consumption run-rates, calculating dynamic dynamic safety stock, reorder thresholds, and adequacy status (*Sufficient*, *Replenish*, *Excess*).
- **Cost Analytics:** Dynamic switch measures allowing users to toggle between Minimum, Average, and Maximum purchase costs across item groups.

---

## 5. Automations & Refresh Design

- **Automated Extraction:** Power Automate flows handle scheduled data extraction from Oracle Fusion to ensure pipeline reliability and power bi service runs on scheduled refresh at specific time.
- **Performance Optimization:** Star schema modeling, relationship optimization, and column cardinality reduction keep the ~10M row dataset lightweight and fast in Power BI Service.
- **Dynamic Context Titles:** Visual titles automatically update based on active slicer selections (Region, Department, Period).

---

## 6. Skills & Technologies Demonstrated

- **Orchestration & ETL:** Power Automate, Power Query (M), Oracle Fusion Cloud extraction (20 tables, 10M+ rows).
- **Big Data Optimization:** Incremental updates, VertiPaq compression, star-schema data modeling.
- **Enterprise Governance:** Dynamic Row-Level Security (RLS) for multi-department and region-based access control.
- **Advanced DAX:** Time-intelligence, dynamic parameters, conditional formatting, and pr
