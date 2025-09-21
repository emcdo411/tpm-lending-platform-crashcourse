# Day 5 — Interview Readiness (Practice & Prove)

[![License: DACR](https://img.shields.io/badge/License-DACR-blue.svg)](../LICENSE.md)
![Focus](https://img.shields.io/badge/Focus-TPM%20Interview%20%7C%20Behavioral%20%7C%20Product%20Case%20%7C%20Tech-9370DB)
![Timebox](https://img.shields.io/badge/Time-120%E2%80%93240%20min-informational)

**Goal:** Turn the last four days into **interview-ready artifacts**: a tight **2-minute pitch**, **3–5 STAR stories**, a **product case readout**, a concise **tech recap** (SQL, APIs, A/B), and a **mock-interview dry run** with checklists and feedback.

---

## Table of Contents
- [Outcomes](#outcomes)
- [Workflow](#workflow)
- [What You Will Build](#what-you-will-build)
- [Files in This Lesson](#files-in-this-lesson)
- [Quick Start](#quick-start)
  - [Craft your pitch](#craft-your-pitch)
  - [Write STAR stories](#write-star-stories)
  - [Build the product case](#build-the-product-case)
  - [Tech recap](#tech-recap)
  - [Run a mock interview](#run-a-mock-interview)
- [CI and Optional Approval](#ci-and-optional-approval)
- [Submission](#submission)
- [Mermaid Troubleshooting](#mermaid-troubleshooting)

---

## Outcomes
By the end of Day 5 you will:
- Deliver a confident **2-minute pitch** matched to a lending-platform TPM role.
- Tell **3–5 STAR stories** covering leadership, conflict, delivery, failure, and influence.
- Present a crisp **product case** with problem, metrics, roadmap, risks, and trade-offs.
- Summarize **technical fluency**: SQL KPIs, API ingestion reliability, A/B interpretation.
- Complete a **mock interview** using structured checklists and capture feedback.

---

## Workflow

```mermaid
flowchart TD
A[Role research and self audit] --> B[Craft 2 minute pitch]
B --> C[Write STAR stories]
C --> D[Build product case readout]
D --> E[Technical recap SQL APIs AB]
E --> F[Mock interview runbook]
F --> G[Feedback and iterate]
G --> H[Finalize portfolio and checklist]
````

*Why a diagram?* Day 5 is about **story and signal**: align to role → craft narrative → demonstrate product and technical judgment → rehearse → refine.

---

## What You Will Build

* **`pitch.md`** — your 2-minute pitch aligned to BorrowWorks-style TPM role.
* **`star_stories.md`** — 3–5 STAR stories with prompts and anti-ramble guardrails.
* **`case_study.md`** — product case readout (lending platform) with metrics and roadmap.
* **`tech_recap.md`** — SQL KPIs, API ingestion reliability, A/B takeaways (links to Days 2–4).
* **`mock_runbook.md`** — interviewer checklist, candidate checklist, timing plan, feedback form.
* **`portfolio.md`** — one-pager index linking all artifacts and your repo highlights.
* **Validation script** (`validate_readiness.py`) to ensure files exist and have content.
* **CI workflow** to run the validation on push/PR.

---

## Files in This Lesson

```text
Day5_Interview_Readiness/
├── README.md
├── lesson.md
├── pitch.md
├── star_stories.md
├── case_study.md
├── tech_recap.md
├── mock_runbook.md
├── portfolio.md
├── prompts/
│   └── day5_prompt.md
├── scripts/
│   └── validate_readiness.py
└── .github/
    └── workflows/
        ├── readiness-ci.yml
        └── deploy-portfolio.yml
```

---

## Quick Start

### Craft your pitch

Open `pitch.md` and fill in:

* **Who you are** (1 line), **what you do** (impact themes), **why this company** (lending platform tie-in).
* **Three proof points** that map to consumer product, data ingestion, and roadmap leadership.
* **Close** with your goal: “I’d like to help scale BorrowWorks’ lending platform with reliable data ingestion and measurable conversion lift.”

> Tip: Aim for **150–200 words**. Time yourself to **< 120 seconds**.

### Write STAR stories

Open `star_stories.md` and complete **3–5** stories:

* **Leadership through influence**
* **Ambiguity to clarity** (roadmap, backlog, user stories)
* **Technical decision** (API vendor trade-off, latency, rate limits)
* **Failure and learning**
* **Delivery under pressure**

> Each story **≤ 2 minutes**: Situation (20s) → Task (10s) → Action (60–90s) → Result (20s).

### Build the product case

Open `case_study.md`:

* **Problem**: Improve approval conversion without raising fraud loss.
* **Users**: borrowers, operations, risk.
* **Metrics**: approval %, abandonment %, time-to-approval, fraud flags.
* **Solution outline**: UX changes + API vendor plan + scoring iteration.
* **Roadmap**: now / next / later; risks and mitigations.
* **Exec slide**: 5 bullets + 1 chart (reuse Day 4’s KPI trend if helpful).

### Tech recap

Open `tech_recap.md`:

* **SQL**: list the KPI queries you ran on Day 2 and what each tells you.
* **APIs & ingestion**: how you handle timeouts, retries, idempotency, and schema checks.
* **A/B**: how you decide ship/hold, practical vs statistical significance, and guardrails.

### Run a mock interview

Open `mock_runbook.md`:

* Use the **interviewer checklist** to simulate the session (45–60 min).
* Use the **candidate checklist** to self-grade (signal areas, clarity, time).
* Capture **feedback** and **iterate** your pitch and stories.

---

## CI and Optional Approval

* **CI:** `.github/workflows/readiness-ci.yml` calls `scripts/validate_readiness.py`
  It checks required files exist and are non-empty (e.g., ≥ 120 characters each).
* **Optional Manual Approval:** `.github/workflows/deploy-portfolio.yml` uses `environment: staging`.
  It zips `pitch.md`, `star_stories.md`, `case_study.md`, `tech_recap.md`, `portfolio.md` and uploads as an artifact after approval.

---

## Submission

Open a PR titled **“Day 5 – Interview Readiness”**. Ensure:

* All artifacts are complete and proof-read.
* CI is ✅ green.
* (Optional) **deploy-portfolio** job shows **Waiting** until you approve it; then it runs and uploads the bundle.
* Merge when complete.

That’s interview-ready proof you can present, defend trade-offs, and ship a narrative.

---

## Mermaid Troubleshooting

* Fence Mermaid at column 1: start with three backticks + `mermaid`, end with three backticks.
* Keep **only Mermaid syntax** inside the block (no text).
* One edge per line; avoid parentheses in labels if your renderer is picky.

```

