# Day 2 — SQL Fundamentals (Build & Prove It)

[![License: DACR](https://img.shields.io/badge/License-DACR-blue.svg)](../LICENSE.md)
![Focus](https://img.shields.io/badge/Focus-SQL%20%7C%20SQLite%20%7C%20GitHub%20Actions-2E8B57)
![Timebox](https://img.shields.io/badge/Time-120%E2%80%93180%20min-informational)

**Goal:** Prove you can **design a schema**, **build a database**, **seed data**, and **answer product questions** with SQL—then automate the proof with **GitHub Actions**.

---

## Table of Contents
- [Outcomes](#outcomes)
- [Workflow](#workflow)
- [What You Will Build](#what-you-will-build)
- [Files in This Lesson](#files-in-this-lesson)
- [Quick Start](#quick-start)
- [CI and Manual Approval](#ci-and-manual-approval)
- [Submission](#submission)

---

## Outcomes
By the end of Day 2 you will:
- Create a **normalized schema** for a lending-style app.
- Seed realistic demo data.
- Write **KPI queries** (conversion, fraud flags, time-to-approval).
- Prove it with a **repeatable build + test** in GitHub Actions.
- Gate a “deploy” job behind **manual approval** using Environments.

---

## Workflow
```mermaid
flowchart TD
A[Plan Schema] --> B[Create schema.sql]
B --> C[Seed seed.sql]
C --> D[Run queries.sql]
D --> E[Automated Tests - CI]
E --> F[Pull Request]
F --> G[Staging Deploy - Env Approval]
G --> H[Merge]

Why a diagram? It keeps Day-2 focused on prove-ability: a straight line from schema → data → queries → automated checks → human approval.

What You Will Build

SQLite database (lending_demo.db) built from schema.sql + seed.sql.

Queries that answer core PM questions (conversion, fraud, time-to-approval).

Python tests to validate counts/constraints.

CI workflow that rebuilds and tests on every push/PR.

Staging workflow that requires manual approval before “deploy”.

Files in This Lesson
Day2_SQL_Data_Fundamentals/
├── README.md
├── lesson.md
├── schema.sql
├── seed.sql
├── queries.sql
├── tests/
│   └── test_sql.py
└── .github/
    └── workflows/
        ├── sql-ci.yml
        └── deploy-staging.yml

Quick Start
Using sqlite3 CLI (if installed)
# From repo root
cd Day2_SQL_Data_Fundamentals
sqlite3 lending_demo.db ".read schema.sql"
sqlite3 lending_demo.db ".read seed.sql"
sqlite3 lending_demo.db ".read queries.sql"

Using Python only (no sqlite3 CLI needed)
# From repo root
cd Day2_SQL_Data_Fundamentals
python - << 'PY'
import sqlite3, pathlib
db = "lending_demo.db"
sql = lambda p: pathlib.Path(p).read_text(encoding="utf-8")
con = sqlite3.connect(db)
con.executescript(sql("schema.sql"))
con.executescript(sql("seed.sql"))
for row in con.execute("select count(*) from borrowers"):
    print(("borrowers", row[0]))
con.close()
PY

Git basics (commit/push this lesson)
git add Day2_SQL_Data_Fundamentals
git commit -m "Day 2: schema, seed, queries, tests, CI"
git push

CI and Manual Approval

CI: .github/workflows/sql-ci.yml builds lending_demo.db and runs tests/test_sql.py on every push/PR.

Manual Approval: .github/workflows/deploy-staging.yml uses environment: staging.
In GitHub → Settings → Environments → New environment: staging → add Required reviewers (you).
The deploy job will pause until approved in the PR Checks UI.

Submission

Open a PR titled “Day 2 – SQL Fundamentals”. Ensure:

CI passes.

Staging job shows Waiting until you approve it in the PR checks.

After approval, the job runs; then Merge.

That’s interview-ready proof.
