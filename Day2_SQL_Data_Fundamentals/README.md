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
D --> E[Automated Tests (CI)]
E --> F[Pull Request]
F --> G[Staging Deploy (Env Approval)]
G --> H[Merge]

