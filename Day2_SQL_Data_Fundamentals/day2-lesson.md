# Day 2 — Lesson Plan: SQL Fundamentals (Build & Prove It)

**Objective:** Prove you can design a normalized schema, build and seed a SQLite database, answer product questions with SQL, and automate verification via GitHub Actions with a manual approval gate.

---

## Prerequisites

- Git installed and authenticated with GitHub.
- Python 3.10+ available (used as a portable `sqlite3` fallback and for tests).
- Optional: `sqlite3` CLI (nice to have, not required).

> Repo layout this lesson expects:
>
> ```
> Day2_SQL_Data_Fundamentals/
> ├── README.md
> ├── lesson.md           <-- this file
> ├── schema.sql
> ├── seed.sql
> ├── queries.sql
> ├── tests/
> │   └── test_sql.py
> └── .github/
>     └── workflows/
>         ├── sql-ci.yml
>         └── deploy-staging.yml
> ```

---

## Step 1 — Design a Minimal Lending Schema

Create/confirm `schema.sql` with four tables:

- **borrowers** — one row per borrower
- **applications** — FK to borrowers; device/channel/status/timestamps
- **credit_events** — bureau result/score/latency for each application
- **kyc_checks** — provider result/risk flags for each application

**Constraints you must include**
- Foreign keys (`applications.borrower_id` → `borrowers.borrower_id`, etc.)
- `CHECK` constraints on categorical columns (`device`, `channel`, `status`)
- Useful indexes (`applications(status)`, `applications(borrower_id)`, etc.)

> The provided `schema.sql` in this repo already matches these requirements.

---

## Step 2 — Seed Realistic Demo Data

Create/confirm `seed.sql`:

- At least 5 borrowers
- 6 applications across mobile/web and paid/organic/referral
- Credit events with mixed results (ok/timeout/rate_limited) and varied latency
- KYC checks with pass/fail/review and some `risk_flags`

> The provided `seed.sql` includes a realistic mix to make the queries meaningful.

---

## Step 3 — Write Product-Focused Queries

Use `queries.sql` to answer core PM questions:

1) **Approval rate** by device/channel  
2) **Abandonment rate**  
3) **Time-to-approval** (minutes) for approved apps  
4) **Flagged ratio** where KYC failed/reviewed or credit event errored/timed out  
5) **Perf check** (worst/95th) credit API latency (demo uses a proxy)

> The provided `queries.sql` includes reference queries aligned to the README.

---

## Step 4 — Build Locally (Option A: sqlite3 CLI)

From repo root:

```bash
cd Day2_SQL_Data_Fundamentals
sqlite3 lending_demo.db ".read schema.sql"
sqlite3 lending_demo.db ".read seed.sql"
sqlite3 lending_demo.db ".read queries.sql"
````

If you want to peek:

```bash
sqlite3 lending_demo.db ".tables"
sqlite3 lending_demo.db "select status, count(*) from applications group by 1;"
```

---

## Step 4 (Alt) — Build Locally (Option B: Python only)

If you don’t have the `sqlite3` CLI:

```bash
# From repo root
cd Day2_SQL_Data_Fundamentals
python - << 'PY'
import sqlite3, pathlib
db = "lending_demo.db"
sql = lambda p: pathlib.Path(p).read_text(encoding="utf-8")
con = sqlite3.connect(db)
con.executescript(sql("schema.sql"))
con.executescript(sql("seed.sql"))
for row in con.execute("select status, count(*) from applications group by 1"):
    print(row)
con.close()
PY
```

---

## Step 5 — Add Lightweight Tests

`tests/test_sql.py` (already provided) verifies:

* Row counts (>=5 borrowers, >=2 approved applications)
* Foreign key integrity (invalid borrower\_id insert fails)

Run locally:

```bash
python -m pip install pytest
pytest -q
```

Expected: all tests pass.

---

## Step 6 — Wire CI (sql-ci.yml)

`/.github/workflows/sql-ci.yml` (provided) will:

* Check out the repo
* Set up Python
* Build `lending_demo.db` from `schema.sql` + `seed.sql`
* Run `pytest` against `tests/test_sql.py`

Trigger: every push and pull request.

**Local dry run (optional)**
You can run the same Python build steps locally (see Step 4 Alt) and `pytest -q`.

---

## Step 7 — Require Manual Approval for “Deploy”

`/.github/workflows/deploy-staging.yml` (provided) uses a **GitHub Environment** gate.

1. In your GitHub repo: **Settings → Environments → New environment: `staging`**
2. Add **Required reviewers** (your account)
3. Open a PR; you’ll see the “deploy-staging” job **waiting for approval**
4. Approve it in the PR **Checks** tab to proceed

> This mimics a safe, auditable promotion step—even if the “deploy” here is just an echo. In a real system, this job might upload artifacts or apply DB migrations.

---

## Step 8 — Commit, Push, PR

Create a branch and open a PR so CI runs and the **staging** job waits for approval.

```bash
git checkout -b day2-sql
git add Day2_SQL_Data_Fundamentals
git commit -m "Day 2: schema, seed, queries, tests, CI"
git push -u origin day2-sql
# open a PR in GitHub UI, or:
# gh pr create --fill
```

In the PR:

* Ensure **sql-ci** workflow is ✅ green
* Approve the **deploy-staging** job in **Checks** (since it uses the `staging` environment)
* Merge when all checks pass

---

## Acceptance Checklist (what reviewers look for)

* **Schema**

  * Foreign keys enforced (`PRAGMA foreign_keys = ON`)
  * Categorical columns protected by `CHECK`
  * Indexes present on common filters/joins
* **Seed**

  * Realistic enough to make queries meaningful
  * Includes success + failure modes (timeouts, rate limits, review/fail KYC)
* **Queries**

  * Answer the KPIs stated in the README
  * Use correct time math for **time-to-approval**
  * Handle NULLs sensibly (e.g., only approved apps have `decided_at`)
* **Tests**

  * Pass locally and in CI
  * Catch basic relational integrity mistakes
* **CI**

  * Rebuilds DB from scratch on each run
  * Runs tests on push/PR
* **Governance**

  * `deploy-staging` job is gated by **Environment** approval
  * Reviewer can see the approval event in the PR checks

---

## Troubleshooting

* **Mermaid diagram not rendering in README**

  * Keep the Mermaid block fenced with \`\`\`mermaid and close it before any prose.
  * One edge per line; avoid parentheses in labels if the renderer is picky.

* **SQLite foreign keys not enforced**

  * Ensure `PRAGMA foreign_keys = ON;` is present before inserts in tests or sessions.

* **CI fails with missing Python/pytest**

  * The `sql-ci.yml` installs Python and `pytest`. If you changed versions, update the workflow’s `setup-python` step.

* **Deploy job never starts**

  * Confirm your PR branch targets `main`/`master` per the workflow, and the repo has an Environment named `staging` with you as a required reviewer.

---

## What to Submit

Open a PR titled **“Day 2 – SQL Fundamentals”**. It should include:

* `schema.sql`, `seed.sql`, `queries.sql`
* `tests/test_sql.py`
* `.github/workflows/sql-ci.yml` and `.github/workflows/deploy-staging.yml`

**Pass CI → Approve staging → Merge.** That’s your end-to-end proof of SQL fundamentals with CI/CD governance.

```
```
