# Day 3 — Lesson Plan: APIs & Ingestion (Evaluate & Prototype)

**Objective:** Prove you can evaluate external APIs, prototype a resilient client (auth, timeouts, pagination, rate limits, retries, idempotency), validate payloads, ingest into SQLite with upsert semantics, and automate smoke tests in GitHub Actions (with an optional manual approval gate).

---

## Prerequisites

- Git installed and authenticated with GitHub.
- Python 3.10+ available (for client, ingestion, and tests).
- Optional: `sqlite3` CLI (nice to have, not required).
- (Recommended) `python -m pip install --upgrade pip`

Repo layout expected (matches README):

```

Day3\_API\_Data\_Ingestion/
├── README.md
├── lesson.md                <-- this file
├── api\_basics.md
├── python\_api\_demo.py
├── ingest\_to\_sqlite.py
├── openapi.yaml
├── vendor\_scorecard.md
├── prompts/
│   └── day3\_prompt.md
├── tests/
│   ├── test\_client.py
│   └── test\_ingest.py
└── .github/
└── workflows/
├── api-ci.yml
└── deploy-ingestion.yml

````

---

## Step 0 — Environment & Secrets

Create `.env` (or export env vars). The demo defaults to `https://httpbin.org` if `BASE_URL` is not set.

```bash
# .env.example
BASE_URL=https://httpbin.org
API_KEY=demo-key          # For real vendors store as a GitHub Actions secret
REQUEST_TIMEOUT_SECS=5
MAX_RETRIES=3
BACKOFF_SECS=0.5
````

> In CI: set secrets via **GitHub → Settings → Secrets and variables → Actions**.

---

## Step 1 — Create a Resilient API Client

Create `Day3_API_Data_Ingestion/python_api_demo.py`:

```python
# python_api_demo.py
import os, time, uuid, json
import requests
from typing import Any, Dict, Optional
try:
    from dotenv import load_dotenv
    load_dotenv()
except Exception:
    pass  # dotenv is optional for local dev

BASE_URL = os.getenv("BASE_URL", "https://httpbin.org")
API_KEY = os.getenv("API_KEY", "")
REQUEST_TIMEOUT = float(os.getenv("REQUEST_TIMEOUT_SECS", "5"))
MAX_RETRIES = int(os.getenv("MAX_RETRIES", "3"))
BACKOFF_SECS = float(os.getenv("BACKOFF_SECS", "0.5"))

SESSION = requests.Session()

def _headers() -> Dict[str, str]:
    h = {
        "User-Agent": "tpm-demo-client/1.0",
        "X-Idempotency-Key": str(uuid.uuid4()),
        "Accept": "application/json",
    }
    if API_KEY:
        h["Authorization"] = f"Bearer {API_KEY}"
    return h

def request_json(path: str, params: Optional[Dict[str, Any]] = None, method: str = "GET", data: Optional[Dict[str, Any]] = None) -> Dict[str, Any]:
    """
    Resilient request with:
      - timeouts
      - retries with exponential backoff
      - handling of 429 and 5xx
    Returns dict including status_code, latency_ms, json_payload.
    """
    url = BASE_URL.rstrip("/") + "/" + path.lstrip("/")
    attempt = 0
    while True:
        attempt += 1
        t0 = time.perf_counter()
        try:
            resp = SESSION.request(
                method=method.upper(),
                url=url,
                headers=_headers(),
                params=params,
                json=data,
                timeout=REQUEST_TIMEOUT,
            )
            latency_ms = int((time.perf_counter() - t0) * 1000)

            # Retry policy: 429 or 5xx -> retry (except 501/505)
            if resp.status_code in (429,) or (500 <= resp.status_code < 600 and resp.status_code not in (501, 505)):
                if attempt <= MAX_RETRIES:
                    time.sleep(BACKOFF_SECS * attempt)
                    continue

            try:
                payload = resp.json()
            except Exception:
                payload = {"text": resp.text}

            return {
                "status_code": resp.status_code,
                "latency_ms": latency_ms,
                "json_payload": payload,
                "url": url,
                "attempts": attempt,
            }

        except requests.Timeout:
            if attempt <= MAX_RETRIES:
                time.sleep(BACKOFF_SECS * attempt)
                continue
            return {"status_code": 0, "latency_ms": int((time.perf_counter() - t0) * 1000), "json_payload": {"error": "timeout"}, "url": url, "attempts": attempt}
        except requests.RequestException as e:
            # non-timeout errors: retry a few times
            if attempt <= MAX_RETRIES:
                time.sleep(BACKOFF_SECS * attempt)
                continue
            return {"status_code": -1, "latency_ms": int((time.perf_counter() - t0) * 1000), "json_payload": {"error": str(e)}, "url": url, "attempts": attempt}

def get_paginated(path: str, page_param: str = "page", start: int = 1, max_pages: int = 1) -> Dict[str, Any]:
    """
    Simple pagination helper: fetch up to max_pages. Real vendors return
    'next' cursors; this demo drives pages explicitly for simplicity.
    """
    pages = []
    for p in range(start, start + max_pages):
        res = request_json(path, params={page_param: p})
        pages.append(res)
        # If a real 'next' cursor exists in json, you would use it instead.
    return {"pages": pages}

if __name__ == "__main__":
    # Demo call
    out = request_json("/get", params={"hello": "world"})
    print(json.dumps(out, indent=2))
```

**Run it**

```bash
# From repo root
cd Day3_API_Data_Ingestion
# PowerShell:
$env:BASE_URL="https://httpbin.org"; $env:API_KEY="demo-key"; python python_api_demo.py
# bash:
BASE_URL="https://httpbin.org" API_KEY="demo-key" python python_api_demo.py
```

**Expect:** JSON with `status_code`, `latency_ms`, `json_payload`, and `attempts`.

---

## Step 2 — Ingest into SQLite (Upsert)

Create `Day3_API_Data_Ingestion/ingest_to_sqlite.py`:

```python
# ingest_to_sqlite.py
import json, os, sqlite3, pathlib, time
from typing import Dict, Any
from python_api_demo import request_json

DB_PATH = pathlib.Path("ingestion_demo.db")

def ensure_db():
    con = sqlite3.connect(DB_PATH)
    con.execute("""
    CREATE TABLE IF NOT EXISTS api_events (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      request_id TEXT UNIQUE,
      status_code INTEGER NOT NULL,
      latency_ms INTEGER NOT NULL,
      payload_json TEXT NOT NULL,
      created_at TEXT NOT NULL DEFAULT (datetime('now'))
    );
    """)
    # Unique index on request_id ensures idempotency
    con.execute("CREATE UNIQUE INDEX IF NOT EXISTS idx_api_events_request ON api_events(request_id);")
    con.commit()
    con.close()

def upsert_event(event: Dict[str, Any]):
    # Generate a deterministic request_id from URL + attempts + time bucket
    request_id = f"{event.get('url','')}|{event.get('attempts',1)}|{int(time.time())//60}"
    payload_json = json.dumps(event.get("json_payload", {}), separators=(",", ":"))
    con = sqlite3.connect(DB_PATH)
    con.execute("""
    INSERT INTO api_events (request_id, status_code, latency_ms, payload_json)
    VALUES (?, ?, ?, ?)
    ON CONFLICT(request_id) DO UPDATE SET
      status_code=excluded.status_code,
      latency_ms=excluded.latency_ms,
      payload_json=excluded.payload_json
    ;
    """, (request_id, int(event.get("status_code", -1)), int(event.get("latency_ms", 0)), payload_json))
    con.commit()
    con.close()
    return request_id

def main():
    ensure_db()
    # Fetch a small batch (simulate pagination = 2 pages)
    events = []
    for page in (1, 2):
        res = request_json("/get", params={"page": page})
        events.append(res)
    # Upsert them
    ids = [upsert_event(e) for e in events]
    print("Upserted rows:", len(ids))

if __name__ == "__main__":
    main()
```

**Run it**

```bash
# From repo root
cd Day3_API_Data_Ingestion
python ingest_to_sqlite.py
python - << 'PY'
import sqlite3
con = sqlite3.connect("ingestion_demo.db")
print(con.execute("select name from sqlite_master where type='table'").fetchall())
print(con.execute("select count(*) from api_events").fetchone())
con.close()
PY
```

**Expect:** table list includes `api_events`; row count ≥ 2.

---

## Step 3 — Document & Compare

### 3a) OpenAPI stub

Create `Day3_API_Data_Ingestion/openapi.yaml`:

```yaml
openapi: 3.0.3
info:
  title: Demo Ingestion API
  version: "0.1"
paths:
  /get:
    get:
      summary: Echo endpoint used for demo
      parameters:
        - in: query
          name: page
          required: false
          schema: { type: integer, minimum: 1 }
      responses:
        "200":
          description: ok
          content:
            application/json:
              schema:
                type: object
                properties:
                  args: { type: object, additionalProperties: true }
                  origin: { type: string }
                  url: { type: string }
```

### 3b) Vendor scorecard

Create `Day3_API_Data_Ingestion/vendor_scorecard.md`:

```markdown
# Vendor Scorecard (Demo)

| Criterion        | Vendor A | Vendor B | Notes |
|------------------|---------:|---------:|------|
| Auth method      |          |          | e.g., OAuth2 client credentials |
| Rate limits      |          |          | e.g., 60 req/min, burst 10 |
| Pagination       |          |          | e.g., cursor 'next', max page size |
| Latency p95      |          |          | expected p95 budget (ms) |
| Error semantics  |          |          | 4xx mapping, error codes, retries |
| Schema stability |          |          | versioning, deprecations |
| SLA/SLO          |          |          | uptime, RTO/RPO |
| Pricing          |          |          | per call or per account |
| Compliance       |          |          | PII handling, audit, data residency |
```

---

## Step 4 — Tests (no real network)

Install test deps and create tests.

**Install**

```bash
python -m pip install pytest requests requests-mock python-dotenv
```

**Create `Day3_API_Data_Ingestion/tests/test_client.py`:**

```python
# test_client.py
import sys, pathlib, json
sys.path.append(str(pathlib.Path(__file__).resolve().parents[1]))
import python_api_demo as client

def test_success_200(requests_mock):
    base = "https://api.test"
    client.BASE_URL = base
    requests_mock.get(base + "/get", json={"ok": True}, status_code=200)
    out = client.request_json("/get", params={"p": 1})
    assert out["status_code"] == 200
    assert "latency_ms" in out
    assert out["json_payload"]["ok"] is True

def test_retry_on_429_then_ok(requests_mock):
    base = "https://api.test"
    client.BASE_URL = base
    client.MAX_RETRIES = 2
    # first 429, then 200
    requests_mock.get(base + "/get", [{"status_code": 429}, {"json": {"ok": True}, "status_code": 200}])
    out = client.request_json("/get")
    assert out["status_code"] == 200
    assert out["attempts"] == 2
```

**Create `Day3_API_Data_Ingestion/tests/test_ingest.py`:**

```python
# test_ingest.py
import sys, pathlib, sqlite3
sys.path.append(str(pathlib.Path(__file__).resolve().parents[1]))
import ingest_to_sqlite as ingest

def test_upsert_event_creates_row(tmp_path):
    # Use a temp DB
    cwd = tmp_path
    (cwd / "ingestion_demo.db").write_bytes(b"") if (cwd / "ingestion_demo.db").exists() else None
    # Patch working directory
    import os
    prev = os.getcwd()
    os.chdir(cwd)
    try:
        ingest.ensure_db()
        event = {
            "url": "https://api.test/get",
            "status_code": 200,
            "latency_ms": 123,
            "json_payload": {"ok": True},
            "attempts": 1
        }
        rid1 = ingest.upsert_event(event)
        rid2 = ingest.upsert_event(event)  # upsert same id (should update not duplicate)
        con = sqlite3.connect("ingestion_demo.db")
        cnt = con.execute("select count(*) from api_events").fetchone()[0]
        con.close()
        assert cnt == 1
        assert rid1 == rid2
    finally:
        os.chdir(prev)
```

**Run tests locally**

```bash
pytest -q Day3_API_Data_Ingestion
```

**Expect:** all tests pass, no real HTTP calls.

---

## Step 5 — CI (GitHub Actions)

Create `Day3_API_Data_Ingestion/.github/workflows/api-ci.yml`:

```yaml
name: api-ci
on:
  push:
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install deps
        run: |
          python -m pip install --upgrade pip
          pip install pytest requests requests-mock python-dotenv

      - name: Run tests
        run: pytest -q Day3_API_Data_Ingestion
```

(Optional) Create `Day3_API_Data_Ingestion/.github/workflows/deploy-ingestion.yml`:

```yaml
name: deploy-ingestion
on:
  pull_request:
    branches: [ "main", "master" ]

jobs:
  gate_and_echo:
    runs-on: ubuntu-latest
    environment: staging   # configure in Settings → Environments
    steps:
      - uses: actions/checkout@v4
      - name: Echo deploy step
        run: |
          echo "Staging deploy step reached for Day 3 ingestion."
          echo "This job is gated by the 'staging' environment approval."
```

> Configure **Settings → Environments → New environment: `staging` → Required reviewers (you)**.
> In the PR **Checks** tab, the deploy job will **wait** for your approval.

---

## Step 6 — Quick Start (Run the Demo)

```bash
# From repo root
cd Day3_API_Data_Ingestion
# Configure env and run client
BASE_URL="https://httpbin.org" API_KEY="demo-key" python python_api_demo.py
# Ingest a small batch
python ingest_to_sqlite.py
# Inspect DB
python - << 'PY'
import sqlite3
con = sqlite3.connect("ingestion_demo.db")
print(con.execute("select count(*) from api_events").fetchone())
con.close()
PY
```

---

## Step 7 — Commit, Push, PR

```bash
git checkout -b day3-apis
git add Day3_API_Data_Ingestion
git commit -m "Day 3: resilient client, ingestion, tests, CI"
git push -u origin day3-apis
# open PR in GitHub (or gh pr create --fill)
```

Ensure CI is green; (optional) approve the **staging** job in **Checks**; merge.

---

## Acceptance Checklist (what reviewers look for)

* **Client**

  * Timeouts, retries with backoff, 429 + 5xx handling
  * Idempotency header and unique request id
  * Pagination helper present (even if demoed trivially)
* **Validation & Ingestion**

  * SQLite table with **unique** request\_id, upsert on conflict
  * No plaintext secrets in code; env usage demonstrated
* **Documentation**

  * `openapi.yaml` stub aligned to demo endpoint
  * `vendor_scorecard.md` with criteria table
* **Tests**

  * Use `requests-mock`; no real network calls
  * Ingestion test proves **upsert** (no duplicates)
* **CI**

  * Runs tests on push/PR and passes
* **Governance (optional)**

  * `deploy-ingestion` job gated by Environment approval

---

## Troubleshooting

* **Network timeouts/429s constantly:** Lower `MAX_RETRIES` / increase `BACKOFF_SECS`. For offline tests, rely on `pytest` suite.
* **SQLite locked on Windows:** Ensure prior Python process finished/closed the connection.
* **Module import errors in tests:** Tests add the lesson folder to `sys.path`; keep the structure as shown.
* **Deploy job not waiting:** Create `staging` environment and add yourself as a **Required reviewer**.

---

## What to Submit

Open a PR titled **“Day 3 – APIs & Ingestion”** including:

* `python_api_demo.py`, `ingest_to_sqlite.py`
* `openapi.yaml`, `vendor_scorecard.md`
* `tests/test_client.py`, `tests/test_ingest.py`
* `.github/workflows/api-ci.yml` (+ optional `deploy-ingestion.yml`)

**Green CI → (Optional) approve staging → Merge.** That’s your end-to-end proof for Day 3.

```
```
