# 💳 PalawanPay — CRM Data Engineering & Report Automation

> **End-to-end data infrastructure built for PalawanPay** (Philippines-based fintech payment platform) — migrated from AWS to GCP, engineered ETL pipelines, automated CRM reporting, and delivered a unified Customer Single View.

---

## 📌 Table of Contents
- [Project Overview](#-project-overview)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [Data Warehouse Design](#-data-warehouse-design)
- [ETL Pipelines](#-etl-pipelines)
- [CRM Report Automation](#-crm-report-automation)
- [Customer Single View](#-customer-single-view-csv)
- [BI Dashboards](#-bi-dashboards)
- [Results & Impact](#-results--impact)
- [Folder Structure](#-folder-structure)

---

## 🧭 Project Overview

PalawanPay is a Philippines-based **digital payment platform** serving millions of users across payment transactions, bill payments, remittances, and promotions.

### The Problem
| Issue | Impact |
|---|---|
| Data scattered across AWS S3 + Redshift | No single source of truth |
| CRM reports generated manually | Took **7–8 days** every month |
| No unified customer view | Campaign spend wasted on wrong segments |
| Infrastructure costs high on AWS | Scaling was expensive |

### The Solution
Built a **complete data infrastructure on GCP** — warehouse, pipelines, dashboards, and automated reporting — from scratch.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        DATA SOURCES                         │
│  PalawanPay API  │  CleverTap  │  TalonOne  │  KYC System  │
└────────────┬─────────────────────────────────┬──────────────┘
             │                                 │
             ▼                                 ▼
┌─────────────────────────────────────────────────────────────┐
│              ETL LAYER  (Python + Cloud Functions)          │
│   Batch Extraction │ Schema-Drift Handling │ Null-Fill      │
│   Incremental Load │ Error Logging         │ Auto-retry     │
└─────────────────────────────┬───────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                  GCP BigQuery — Data Warehouse              │
│  ┌───────────┐   ┌──────────────┐   ┌────────────────────┐ │
│  │  RAW Layer │──▶│ STAGING Layer│──▶│    MART Layer      │ │
│  │ (as-is)   │   │ (cleaned)    │   │ (business-ready)   │ │
│  └───────────┘   └──────────────┘   └────────────────────┘ │
└─────────────────────────────┬───────────────────────────────┘
                              │
              ┌───────────────┴────────────────┐
              ▼                                ▼
┌─────────────────────────┐      ┌─────────────────────────┐
│   REPORT AUTOMATION     │      │      BI DASHBOARDS       │
│  Cloud Scheduler        │      │    Looker Studio         │
│  Python → Excel/CSV     │      │  Real-time CRM tracking  │
└─────────────────────────┘      └─────────────────────────┘
```

---

## 🛠️ Tech Stack

| Category | Tools |
|---|---|
| **Cloud Platform** | Google Cloud Platform (GCP) |
| **Data Warehouse** | BigQuery |
| **Pipeline / Compute** | Python, Cloud Functions, Cloud Run |
| **Orchestration** | Cloud Scheduler |
| **Source (migrated from)** | AWS S3, Redshift |
| **CRM & Engagement** | CleverTap, TalonOne |
| **BI & Reporting** | Looker Studio, Excel, CSV |
| **Libraries** | Pandas, NumPy, google-cloud-bigquery |

---

## 🗄️ Data Warehouse Design

The warehouse is structured in **3 layers** — standard medallion architecture:

```
RAW Layer
└── Exact copy of source data — no transformations
└── Tables: raw_transactions, raw_users, raw_promos, raw_services

STAGING Layer
└── Cleaned, type-cast, deduplicated
└── Null-fills applied, schema standardized
└── Tables: stg_transactions, stg_users, stg_promos, stg_services

MART Layer  ← Business teams query here
└── Aggregated, joined, business-logic applied
└── Tables:
    ├── mart_monthly_performance
    ├── mart_service_consumption
    ├── mart_verified_users
    ├── mart_promo_performance
    ├── mart_finance_summary
    └── mart_customer_single_view  ← CSV model
```

### Key Design Decisions
- ✅ **Incremental loading** — only new/updated records processed each run
- ✅ **Schema-drift resilience** — new columns auto-detected and handled
- ✅ **Partitioned tables** — by date for cost-efficient querying
- ✅ **2+ years historical data** retained post-migration

---

## ⚙️ ETL Pipelines

Each pipeline follows this flow:

```
1. EXTRACT   → Hit PalawanPay API in batches (avoid memory overflow)
2. VALIDATE  → Check schema, detect drift, log anomalies
3. TRANSFORM → Type cast, null-fill, standardize fields
4. LOAD      → Incremental insert into BigQuery staging
5. PROMOTE   → Run SQL to push staging → mart layer
6. LOG       → Write run metadata (rows, errors, duration) to audit table
```

### Problem Solved — API Memory Overflow
PalawanPay's transaction data is large (10M+ records). Pulling everything at once caused memory crashes.

**Solution:** Batch-wise extraction with date-range chunking:
```python
# Pseudocode — batch extraction logic
for batch in date_range_chunks(start_date, end_date, chunk_days=7):
    records = api.fetch(batch.start, batch.end)
    records = handle_schema_drift(records, expected_schema)
    records = auto_null_fill(records)
    load_to_bigquery(records, table="raw_transactions", mode="append")
```

---

## 📊 CRM Report Automation

### 5 Reports Automated

| Report | Frequency | Data Source | Output |
|---|---|---|---|
| Monthly Performance | Monthly | mart_monthly_performance | Excel |
| Service-wise Consumption | Monthly | mart_service_consumption | Excel |
| Verified Users | Monthly | mart_verified_users | CSV |
| Finance Summary | Monthly | mart_finance_summary | Excel |
| Promo Performance | Monthly | mart_promo_performance | Excel |

### How It Works

```
Cloud Scheduler (trigger on 1st of each month)
        │
        ▼
Cloud Function (Python)
        │
        ├── Query BigQuery mart table
        ├── Format into Excel/CSV using Pandas
        ├── Apply business formatting (headers, totals, highlights)
        └── Deliver to stakeholder folder / email
```

### Before vs After

| Metric | Before | After |
|---|---|---|
| Report generation time | 7–8 days | 1 day |
| Manual effort | High (analyst time) | Zero |
| Error rate | Occasional manual errors | Automated, consistent |
| Frequency possible | Monthly (barely) | Any frequency |

---

## 👤 Customer Single View (CSV)

The CSV is a **single BigQuery mart table** that gives a 360° view of every customer.

### Data Sources Joined

```
mart_customer_single_view
├── payment_transactions     → transaction count, GMV, last active date
├── clevertap_events         → push open rate, session count, channel preference
├── talone_promos            → promo redemptions, discount availed
├── kyc_status               → verification level, KYC date
└── derived_fields           → CLTV score, segment label, churn risk flag
```

### Use Cases Enabled
- 🎯 **CRM Segmentation** — target high-value vs at-risk users
- 💰 **Campaign Optimization** — decide which segment gets which promo
- 📈 **CLTV Scoring** — rank customers by lifetime value
- 🔍 **Churn Detection** — flag users with declining engagement

---

## 📈 BI Dashboards

Built in **Looker Studio** — connected directly to BigQuery mart tables.

### Dashboards Delivered

**1. CRM Campaign Performance Dashboard**
- Channel-wise spend vs conversion
- Daily/weekly active users trend
- Promo redemption rate by segment

**2. Customer Single View Explorer**
- Segment distribution (high / mid / low value)
- CLTV distribution across user base
- Engagement score vs transaction frequency

---

## 🏆 Results & Impact

| Metric | Result |
|---|---|
| ☁️ Infrastructure cost reduction | **~70%** (AWS → GCP) |
| 📦 Storage scale-up | **5x** with full history retained |
| ⏱️ Report generation time | **7–8 days → 1 day** |
| 🔁 Manual effort saved | **~6–7 days/month** |
| 📊 Reports automated | **5 CRM reports** |
| 🧮 Records processed | **10M+ payment records** |

---

## 📁 Folder Structure

```
palawanpay-crm-data-engineering/
│
├── etl/
│   ├── extract.py           # API extraction with batch logic
│   ├── transform.py         # Schema drift handling, null-fill
│   ├── load.py              # BigQuery incremental loader
│   └── audit.py             # Run logging and metadata
│
├── warehouse/
│   ├── raw/                 # Raw layer DDLs
│   ├── staging/             # Staging layer DDLs + cleaning SQL
│   └── mart/                # Mart layer DDLs + business logic SQL
│
├── reports/
│   ├── monthly_performance.py
│   ├── service_consumption.py
│   ├── verified_users.py
│   ├── finance_summary.py
│   └── promo_performance.py
│
├── dashboards/
│   └── looker_studio_links.md
│
├── customer_single_view/
│   └── csv_model.sql        # Full CSV mart table query
│
└── README.md
```

---

> 💼 **Built by Mohd Maaz Ekram** — Data Engineer & Data Analyst
> 📧 Mohdmaazekram@gmail.com · [LinkedIn](https://linkedin.com/in/mohdmaazekram) · [Portfolio](https://mohdmaazekram.github.io)
