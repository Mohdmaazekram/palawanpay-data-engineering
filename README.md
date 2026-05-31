# 💳 PalawanPay — CRM Data Engineering & Report Automation

> **End-to-end data infrastructure built for PalawanPay** (Philippines-based fintech payment platform) over 7 months (Sep 2025 – Mar 2026) — migrated from AWS to GCP BigQuery, engineered production-grade ETL pipelines, automated 10+ CRM reports, and delivered a CLTV-enabled Customer Single View.

---

## 📌 Table of Contents
- [Project Overview](#-project-overview)
- [What I Inherited vs What I Built](#-what-i-inherited-vs-what-i-built)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [Phase-wise Timeline](#-phase-wise-timeline)
- [AWS → GCP Migration](#-aws--gcp-migration)
- [ETL Pipelines](#-etl-pipelines)
- [Report Automation](#-report-automation)
- [Customer Single View & CLTV](#-customer-single-view--cltv)
- [Challenges & How I Solved Them](#-challenges--how-i-solved-them)
- [Monthly Operating Cycle](#-monthly-operating-cycle)
- [Results & Impact](#-results--impact)
- [Scope of Work](#-scope-of-work)
- [Roadmap](#-roadmap)
- [Folder Structure](#-folder-structure)

---

## 🧭 Project Overview

PalawanPay is a **Philippines-based digital payment platform** serving users across payment transactions, bill payments, remittances, and promotions.

I joined as Data Analyst but operated across **4 functions** — Data Engineering, Analytics, MIS, and Automation Development — owning the full data lifecycle from infrastructure to insight delivery.

### The Problem I Walked Into

| Area | Situation at Joining |
|---|---|
| **Reporting** | Mostly manual / copy-paste heavy — took **8–10 working days** per month |
| **Infrastructure** | 100 GB AWS Workspace — filled frequently, slow queries, historical data deleted regularly |
| **Data Access** | Single-user only, no centralized warehouse, no row-level data |
| **Automation** | None — no structured automation framework |
| **BI / ML** | Not possible on existing setup |
| **CLTV** | Not available |

---

## 🔄 What I Inherited vs What I Built

```
BEFORE (Sep 2025)                         AFTER (Mar 2026)
─────────────────────────────────         ──────────────────────────────────
❌ Manual reports — 8–10 days/month   →   ✅ Automated — 2–4 days/month
❌ 100 GB AWS (filled constantly)     →   ✅ 500+ GB scalable GCP BigQuery
❌ ~₹20,000/month infra cost          →   ✅ ₹5,000–₹6,000/month (~70% cut)
❌ No historical data (deleted)       →   ✅ 2+ years full history retained
❌ Single-user environment            →   ✅ Multi-user analytics environment
❌ No CLTV                            →   ✅ CLTV live with daily auto-refresh
❌ No warehouse / no row-level data   →   ✅ BigQuery row-level warehouse, ML-ready
❌ No automation                      →   ✅ End-to-end ETL pipelines live
```

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                          DATA SOURCES                                │
│  PalawanPay API  │  CleverTap  │  Talon.One  │  SSMS  │  KYC System │
└────────┬─────────────────────────────────────────┬───────────────────┘
         │                                         │
         ▼                                         ▼
┌──────────────────────────────────────────────────────────────────────┐
│              ETL LAYER  (Python + Cloud Run / Scheduled Jobs)        │
│                                                                      │
│   Batch Extraction   │  Schema-Drift Handling  │  Null-Fill Logic    │
│   Incremental Load   │  Duplicate Detection    │  Error Logging      │
│   Memory-Safe Pulls  │  Auto Retry             │  Audit Trail        │
└────────────────────────────────┬─────────────────────────────────────┘
                                 │
                                 ▼
┌──────────────────────────────────────────────────────────────────────┐
│                    GCP BigQuery — Data Warehouse                     │
│                                                                      │
│  ┌─────────────┐    ┌──────────────────┐    ┌─────────────────────┐ │
│  │  RAW Layer  │───▶│  STAGING Layer   │───▶│     MART Layer      │ │
│  │  (as-is)    │    │  (cleaned/typed) │    │  (business-ready)   │ │
│  └─────────────┘    └──────────────────┘    └─────────────────────┘ │
│                                                                      │
│  Tables: Transactions · CleverTap Events · Talon.One · KYC · CLTV  │
└──────────────┬──────────────────────────────────┬────────────────────┘
               │                                  │
               ▼                                  ▼
┌──────────────────────────┐       ┌──────────────────────────────────┐
│    REPORT AUTOMATION     │       │         BI & ANALYTICS           │
│                          │       │                                  │
│  Scheduled Jobs          │       │  Customer Single View (CSV)      │
│  Day 1 of every month    │       │  CLTV — live on CleverTap        │
│  BigQuery → Excel / CSV  │       │  Dashboards (in progress)        │
│  10+ reports delivered   │       │  ML-ready warehouse              │
└──────────────────────────┘       └──────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Category | Tools |
|---|---|
| **Cloud Platform** | Google Cloud Platform (GCP) |
| **Data Warehouse** | BigQuery |
| **Pipeline / Compute** | Python, Cloud Run, Scheduled Jobs |
| **Source (migrated from)** | AWS Workspace, SSMS |
| **CRM & Engagement** | CleverTap, Talon.One |
| **BI & Reporting** | Looker Studio, Excel, CSV |
| **Libraries** | Pandas, NumPy, google-cloud-bigquery |
| **Other** | Selenium (web scraping), custom SSMS → BigQuery extraction |

---

## 📅 Phase-wise Timeline

### Phase 1 — Sep to Oct 2025 | KT & Transition
> *Hit the ground running — took complete ownership of the entire reporting cycle from the outgoing resource within 2 months.*

- Learned the full monthly reporting cycle, mapped every deadline, assessed effort per report
- Took over Monthly Performance Reports, KPI, Campaign Effectiveness, Event Per User
- Owned Dashboard 1 & 2, Data Accuracy Sheets, SMS/Email Trackers
- Assumed responsibility for Finance Reports, Verified User Reports, Service-wise Data Cuts

---

### Phase 2 — Oct to Dec 2025 | GCP Setup, Migration & CLTV
> *Identified the AWS bottleneck, proposed the GCP migration, and delivered CLTV live on CleverTap by Dec 28, 2025.*

- Designed GCP architecture and built initial ingestion pipelines
- Migrated 2+ years of CleverTap historical data into BigQuery
- Automated campaign extraction — saving 30+ minutes per reporting cycle
- **Delivered CLTV live on CleverTap** — first ML-adjacent capability on the project

---

### Phase 3 — Jan to Till | Full Automation & Optimization
> *Shifted to end-to-end automation, completed AWS migration, cut infrastructure cost by ~70%.*

- Built production-grade ETL pipelines for CleverTap and Talon.One with daily refreshes
- Automated CLTV update cycle completely
- Completed full AWS → GCP migration
- Reduced monthly reporting time from 8–10 days to 2–4 days
- Infrastructure cost cut from ~₹20K to ₹5–6K/month

---

## ☁️ AWS → GCP Migration

### Why We Migrated

The AWS Workspace was a structural bottleneck — not just a performance issue.

| Parameter | AWS Workspace (Before) | GCP — BigQuery (After) |
|---|---|---|
| **Storage** | 100 GB — filled frequently | Scalable — 500+ GB supported |
| **Performance** | System lagged; slow queries | Fast columnar querying via BigQuery |
| **Historical Data** | Deleted regularly to free space | Full retention — 3 months + 2 years loaded |
| **Multi-User Access** | Single-user only | Multi-user analytics environment |
| **Automation** | No structured framework | Cloud Run + Scheduled Jobs + BigQuery ETL |
| **BI / ML Readiness** | Not possible | Row-level warehouse — dashboards, CLTV, ML-ready |
| **Cost** | ~₹20,000/month at peak | ₹5,000–₹6,000/month (~70% lower) |

### Migration Challenge — No Direct Transfer Path

There was no straightforward way to move data from SSMS to BigQuery. I built a **custom Python-based extraction pipeline** to pull data from SSMS and load directly into BigQuery tables — no third-party dependency, no data loss.

---

## ⚙️ ETL Pipelines

Each pipeline follows this flow:

```
1. EXTRACT    →  Hit source API / SSMS in date-range batches
2. VALIDATE   →  Detect schema drift, flag duplicate columns
3. TRANSFORM  →  Type cast, null-fill missing fields, standardize
4. LOAD       →  Incremental append into BigQuery staging
5. PROMOTE    →  SQL to push staging → mart layer
6. LOG        →  Write run metadata (rows, errors, duration) to audit table
```

### Key Engineering Decisions

**Schema-Drift Resilience**
CleverTap data had schema changes mid-migration — new columns appeared, old ones disappeared.
```
Pipeline behavior:
  → Auto-detect new columns → add to schema
  → Missing expected columns → null-fill
  → Duplicate columns → auto-drop
  → Result: pipeline never breaks on schema changes
```

**Memory-Safe Batch Extraction**
Large API pulls (10M+ records) caused memory overflows when pulled in one shot.
```python
# Pseudocode — batch extraction with date chunking
for batch in date_range_chunks(start_date, end_date, chunk_days=7):
    records = api.fetch(batch.start, batch.end)
    records = handle_schema_drift(records, expected_schema)
    records = auto_null_fill(records)
    load_to_bigquery(records, table="raw_transactions", mode="append")
```

**Runtime Limit Fix — Cloud Services → Scheduled Jobs**
Lightweight cloud services hit runtime limits mid-execution on large monthly reports.
Solution: shifted to **scheduled jobs** — reports now trigger automatically on Day 1 of every month, no manual kick-off.

**Accuracy Guardrails**
User-level event data showed 15–20% mismatch in key events (App Launched, Registration Complete, Notification Sent). Instead of fully automating inaccurate data, I retained **semi-automation with manual validation checkpoints** — protecting reporting accuracy.

---

## 📊 Report Automation

### Before vs After

```
BEFORE                              AFTER
──────────────────────              ──────────────────────────────────
8–10 working days                   2–4 working days
Manual / copy-paste heavy           Automated — notebooks generate
                                    naming + formatting end-to-end

Trigger: whenever analyst           Trigger: Day 1 of every month,
         had bandwidth                       immediately after data load
```

### Reports Owned & Automated (10+)

| Report | Type | Output |
|---|---|---|
| Monthly Performance Report | Recurring | Excel (multi-sheet: KPI, KPI-NEW, Campaign Effectiveness, CT Data Accuracy, Dashboard 1 & 2, Event Per Users, SMS/Email Tracker) |
| Service-wise Consumption Report | Recurring | Excel |
| Verified Users Report | Recurring | CSV |
| Finance Report | Recurring | Excel |
| Promo Performance Report | Recurring | Excel |
| Campaign Effectiveness Report | Recurring | Excel |
| Overall Campaign Dump | Recurring | CSV |
| CleverTap Data Append | Daily | BigQuery |
| Talon.One Data Append | Daily | BigQuery |
| Ad hoc Data Cuts | On-demand | Excel / CSV |

---

## 👤 Customer Single View & CLTV

### Customer Single View (CSV) — In Progress

The CSV is the foundation for all downstream analytics and ML.

```
mart_customer_single_view
├── payment_transactions    → GMV, transaction count, last active date
├── clevertap_events        → push open rate, session count, channel preference
├── talon_one_promos        → promo redemptions, discount availed
├── kyc_status              → verification level, KYC completion date
└── derived_fields          → CLTV score, segment label, churn risk flag
```

**Use Cases Unlocked:**
- 🎯 CRM Segmentation — target high-value vs at-risk users
- 💰 Campaign Optimization — right promo to right segment
- 📈 CLTV Scoring — rank customers by lifetime value
- 🔍 Churn Detection — flag users with declining engagement

### CLTV — Live on CleverTap
- CLTV scores computed in BigQuery and pushed live to CleverTap
- Daily automated refresh — scores stay current without manual intervention
- First ML-adjacent output delivered on the project

---

## 🔧 Challenges & How I Solved Them

| Challenge | What Happened | How I Solved It |
|---|---|---|
| **Data mismatch post-migration** | 15–20% user-level mismatch in key events | Retained semi-automation + manual validation checkpoints — accuracy over speed |
| **Schema drift** | CleverTap columns changed mid-migration | Dynamic schema adaptation — auto-add missing, auto-drop duplicates, null-fill unavailable fields |
| **Cloud runtime limits** | Lightweight services crashed mid-execution on large reports | Switched to scheduled jobs — stable, no manual trigger needed |
| **API memory overflow** | Large data pulls caused pipeline instability | Batch-wise extraction + append — pipeline stable at 10M+ records |
| **No direct SSMS → BigQuery path** | No out-of-box migration tool available | Built custom Python extractor — pulled from SSMS, loaded directly into BigQuery; no third-party dependency |

---

## 🔄 Monthly Operating Cycle

```
DAY 1–2    │  Data Load + Reports
           │  Incremental load completes. Notebooks auto-generate
           │  Monthly Performance, Service-wise, Verified Users & Journey reports.
           │
DAY 3–6    │  MBR Deck Prep
           │  B2C data cuts in ~10 min. Frozen client-format decks updated
           │  (3 P4B + 2 B2C). Deep-dive analysis + campaign plan per service.
           │
DAY 7–15   │  Pre-MBR + MBR
           │  Pre-MBR with client → incorporate feedback → final MBR.
           │  Showcase updated insights, deep dives, campaign roadmap.
           │
POST-MBR   │  Smoke Test + Next Month Plan
           │  Smoke tests on promos & turnover. Brainstorm next month's
           │  campaign hypotheses, target cuts, A/B setups.
```

> 💡 Deck format is frozen by the client — focus has shifted from formatting to **insight depth**.

---

## 🏆 Results & Impact

| Metric | Before | After |
|---|---|---|
| ☁️ Infrastructure cost | ~₹20,000/month | ₹5,000–₹6,000/month **(~70% reduction)** |
| 📦 Storage capacity | 100 GB (AWS) | 500+ GB scalable (GCP) — **5x uplift** |
| 📅 Monthly reporting time | 8–10 working days | 2–4 working days |
| 🕐 Days reclaimed per month | — | **~6–7 days → redirected to MBR & strategy** |
| 🗂️ Historical data retained | Deleted regularly | **2+ years full history** |
| 🤖 Automation level | Mostly manual | **80–90% automated** |
| 📊 Reports automated | 0 | **10+ reports** |
| 👥 User access | Single-user only | **Multi-user environment** |
| 📈 CLTV capability | Not available | **Live with daily auto-refresh** |
| 🧮 Data scale | Limited | **10M+ records, daily refresh** |

---

## 🚀 Roadmap

- [ ] Resolve 15–20% user-level data mismatch with CleverTap / Hitachi → achieve 100% automation
- [ ] Build master tables for faster cross-functional reporting
- [ ] Develop advanced dashboards for service-level root cause analysis
- [ ] Enable customer journey CSV outputs
- [ ] Lay groundwork for predictive models — NBA, cross-sell, upsell

---

## 📁 Folder Structure

```
palawanpay-crm-data-engineering/
│
├── etl/
│   ├── extract.py                  # Batch API extraction with date-chunking
│   ├── transform.py                # Schema drift handling, null-fill, dedup
│   ├── load.py                     # Incremental BigQuery loader
│   ├── ssms_to_bq.py               # Custom SSMS → BigQuery migration script
│   └── audit.py                    # Run logging and metadata
│
├── warehouse/
│   ├── raw/                        # Raw layer DDLs
│   ├── staging/                    # Staging layer DDLs + cleaning SQL
│   └── mart/                       # Mart layer DDLs + business logic SQL
│
├── reports/
│   ├── monthly_performance.py      # Monthly Performance (multi-sheet)
│   ├── service_consumption.py      # Service-wise Consumption
│   ├── verified_users.py           # Verified Users Report
│   ├── finance_summary.py          # Finance Report
│   ├── promo_performance.py        # Promo Performance Report
│   └── scheduler_config.yaml       # Cloud Scheduler job config
│
├── cltv/
│   ├── cltv_model.py               # CLTV computation in BigQuery
│   └── clevertap_push.py           # Push CLTV scores to CleverTap
│
├── customer_single_view/
│   └── csv_model.sql               # Full CSV mart table query
│
├── dashboards/
│   └── looker_studio_links.md
│
└── README.md
```

---

> 💼 **Built by Mohd Maaz Ekram** — Data Engineer & Data Analyst
> 📧 Mohdmaazekram@gmail.com · [LinkedIn](https://linkedin.com/in/mohdmaazekram) · [Portfolio](https://mohdmaazekram.github.io)
