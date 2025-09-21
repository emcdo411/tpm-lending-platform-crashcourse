# Day 3 — APIs & Ingestion (Evaluate & Prototype)

[![License: DACR](https://img.shields.io/badge/License-DACR-blue.svg)](../LICENSE.md)
![Focus](https://img.shields.io/badge/Focus-APIs%20%7C%20Data%20Ingestion%20%7C%20Python%20%7C%20Reliability-1E90FF)
![Timebox](https://img.shields.io/badge/Time-120%E2%80%93180%20min-informational)

**Goal:** Prove you can **evaluate external APIs**, **prototype requests**, **handle auth/pagination/rate limits/retries/idempotency**, and **ingest data** into a database—then **automate smoke tests** in GitHub Actions.

---

## Table of Contents
- [Outcomes](#outcomes)
- [Workflow](#workflow)
- [What You Will Build](#what-you-will-build)
- [Files in This Lesson](#files-in-this-lesson)
- [Quick Start](#quick-start)
  - [Configure environment](#configure-environment)
  - [Run the Python API demo](#run-the-python-api-demo)
  - [Ingest into SQLite](#ingest-into-sqlite)
  - [Run tests locally](#run-tests-locally)
- [CI and Manual Approval](#ci-and-manual-approval)
- [Submission](#submission)
- [Mermaid Troubleshooting](#mermaid-troubleshooting)

---

## Outcomes
By the end of Day 3 you will:
- Compare APIs using a **vendor scorecard** (auth, schema, latency, limits, SLAs).
- Prototype a **resilient client** (timeouts, retries with backoff, pagination, idempotency keys).
- **Validate payloads** (shape & required fields) before ingestion.
- **Ingest** responses into a **SQLite** table with basic upsert semantics.
- Prove repeatability with **tests** and a **CI workflow**.

---

## Workflow

```mermaid
flowchart TD
A[Define Data Needs & NFRs] --> B[Review API Docs & OpenAPI]
B --> C[Prototype Requests - Auth/Timeouts]
C --> D[Handle Pagination & Rate Limits]
D --> E[Validate Schema & Errors]
E --> F[Ingest to SQLite - Upsert]
F --> G[Vendor Scorecard]
G --> H[CI Smoke Tests]
H --> I[PR + Optional Env Approval]
````

*Why a diagram?* It keeps Day-3 focused on **defensive ingestion**: from requirements → resilient client → validated data → automated proof.

---

## What You Will Build

* **`python_api_demo.py`** — resilient client using `requests` (timeouts, retries/backoff, idempotency header).
* **`ingest_to_sqlite.py`** — writes normalized rows into `ingestion_demo.db`.
* **`openapi.yaml`** — example/spec stub to document expected fields.
* **`vendor_scorecard.md`** — comparison template (auth, schema stability, latency, errors, cost).
* **Tests** using `pytest` + `requests-mock` (no external network needed).
* **CI workflow** that runs the tests on push/PR.
* **Optional gated “deploy”** workflow using Environments (manual approval).

---

## Files in This Lesson

```text
Day3_API_Data_Ingestion/
├── README.md
├── lesson.md
├── api_basics.md
├── python_api_demo.py
├── ingest_to_sqlite.py
├── openapi.yaml
├── vendor_scorecard.md
├── prompts/
│   └── day3_prompt.md
├── tests/
│   ├── test_client.py
│   └── test_ingest.py
└── .github/
    └── workflows/
        ├── api-ci.yml
        └── deploy-ingestion.yml
```

---

## Quick Start

### Configure environment

Create a simple `.env` (or export env vars). The demo defaults to `https://httpbin.org` if `BASE_URL` is not set.

```bash
# .env.example
BASE_URL=https://httpbin.org
API_KEY=demo-key  # for real vendors store as secret, not in git
REQUEST_TIMEOUT_SECS=5
MAX_RETRIES=3
BACKOFF_SECS=0.5
```

> In CI, set secrets via GitHub **Settings → Secrets and variables → Actions**.

### Run the Python API demo

From repo root:

```bash
cd Day3_API_Data_Ingestion
# Windows PowerShell:
$env:BASE_URL="https://httpbin.org"; $env:API_KEY="demo-key"; python python_api_demo.py
# macOS/Linux bash:
BASE_URL="https://httpbin.org" API_KEY="demo-key" python python_api_demo.py
```

**Expect:** JSON printed with status code, measured latency, and safe handling of timeouts/rate-limit simulations.

### Ingest into SQLite

```bash
# Create / refresh the local DB and ingest a small batch
python ingest_to_sqlite.py
# Inspect the tables
python - << 'PY'
import sqlite3
con=sqlite3.connect("ingestion_demo.db")
print(con.execute("select name from sqlite_master where type='table'").fetchall())
print(con.execute("select count(*) from api_events").fetchone())
con.close()
PY
```

### Run tests locally

```bash
python -m pip install --upgrade pip
python -m pip install pytest requests requests-mock python-dotenv
pytest -q Day3_API_Data_Ingestion
```

**Expect:** All tests pass without making real network calls.

---

## CI and Manual Approval

* **CI:** `.github/workflows/api-ci.yml` installs Python deps and runs the tests on every push/PR.
* **Optional Manual Approval:** `.github/workflows/deploy-ingestion.yml` uses `environment: staging`.
  In GitHub → **Settings → Environments → New environment: `staging`** → add **Required reviewers** (you).
  The deploy job will **pause** until approved in the PR Checks UI.

---

## Submission

Open a PR titled **“Day 3 – APIs & Ingestion”**. Ensure:

* CI is ✅ green.
* (Optional) **staging** job shows **Waiting** until you approve it; then it runs.
* Merge when complete.

That’s interview-ready proof of resilient API ingestion.

---

## Mermaid Troubleshooting

* Fence Mermaid at column 1: start with three backticks + `mermaid`, end with three backticks.
* Keep **only Mermaid syntax** inside the block (no text).
* One edge per line; avoid parentheses in labels if your renderer is picky.

```

Want me to also generate the **`lesson.md`**, a **requests-mock test suite**, and the **CI workflows** (`api-ci.yml`, `deploy-ingestion.yml`) to match this README?
```

