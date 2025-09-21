# Day 4 — Python & R for PMs (Experiment & Explain)

[![License: DACR](https://img.shields.io/badge/License-DACR-blue.svg)](../LICENSE.md)
![Focus](https://img.shields.io/badge/Focus-Python%20%7C%20R%20%7C%20A%2FB%20Testing%20%7C%20Visualization-20B2AA)
![Timebox](https://img.shields.io/badge/Time-120%E2%80%93180%20min-informational)

**Goal:** Prove you can **analyze an A/B test**, **visualize KPIs**, and **communicate results** like a PM.  
You’ll run a small A/B analysis in **Python**, chart KPIs in **R**, and package a short **exec-ready readout**.

---

## Table of Contents
- [Outcomes](#outcomes)
- [Workflow](#workflow)
- [What You Will Build](#what-you-will-build)
- [Files in This Lesson](#files-in-this-lesson)
- [Quick Start](#quick-start)
  - [Set up Python](#set-up-python)
  - [Run the A/B analysis in Python](#run-the-ab-analysis-in-python)
  - [Plot KPIs in R](#plot-kpis-in-r)
  - [Generate an executive summary](#generate-an-executive-summary)
- [Interpreting Results](#interpreting-results)
- [CI and Optional Approval](#ci-and-optional-approval)
- [Submission](#submission)
- [Mermaid Troubleshooting](#mermaid-troubleshooting)

---

## Outcomes
By the end of Day 4 you will:
- Load and clean **experiment data** and compute **lift**, **confidence intervals**, and a **simple significance test**.
- Create **publication-grade charts** (weekly KPIs, funnel, conversion by segment).
- Produce an **exec summary** that focuses on **decision & risk**, not math for math’s sake.
- Run everything via a **repeatable script** and (optionally) a **CI workflow**.

---

## Workflow

```mermaid
flowchart TD
A[Define question and metric] --> B[Load and clean data]
B --> C[Analyze A B outcomes]
C --> D[Visualize KPIs and segments]
D --> E[Summarize insights and risks]
E --> F[Recommend decision and next steps]
````

*Why a diagram?* Day 4 is about turning **data → decision**. This keeps you focused on the PM flow: question → metrics → analysis → narrative → decision.

---

## What You Will Build

* **`ab_test.py`** — Python script that reads `sample_ab.csv`, computes conversion, lift, CI, and a simple significance test.
* **`plot_kpis.R`** — R script that reads `sample_kpis.csv` and renders a line chart plus a small funnel plot.
* **`exec_summary.md`** — 1–2 pages: key insights, decision, risks, next steps.
* **`/figures`** — charts exported as PNGs.
* **Data**: `sample_ab.csv`, `sample_kpis.csv` (toy but realistic).
* **CI** (optional): run Python unit tests and R plotting on push/PR.

---

## Files in This Lesson

```text
Day4_Python_R_for_Product/
├── README.md
├── lesson.md
├── data/
│   ├── sample_ab.csv
│   └── sample_kpis.csv
├── ab_test.py
├── plot_kpis.R
├── exec_summary.md
├── figures/
│   ├── ab_lift.png
│   └── kpi_trend.png
├── tests/
│   └── test_ab.py
└── .github/
    └── workflows/
        ├── py-r-ci.yml
        └── deploy-readout.yml
```

---

## Quick Start

### Set up Python

```bash
# From repo root
cd Day4_Python_R_for_Product
python -m venv .venv
# Windows PowerShell
. .venv/Scripts/Activate.ps1
# macOS/Linux
source .venv/bin/activate

python -m pip install --upgrade pip
pip install pandas numpy scipy matplotlib
```

### Run the A/B analysis in Python

```bash
# From Day4_Python_R_for_Product
python ab_test.py --input data/sample_ab.csv --out figures/ab_lift.png
```

**Expect:**

* Terminal output with **control vs treatment conversion**, **lift %**, **p-value**, and a call like **“ship”, “hold”, or “more data”**.
* A chart saved to `figures/ab_lift.png`.

### Plot KPIs in R

Install packages once in R (or Rscript):

```r
install.packages(c("readr","dplyr","ggplot2"))
```

Then run:

```bash
Rscript plot_kpis.R data/sample_kpis.csv figures/kpi_trend.png
```

**Expect:** `figures/kpi_trend.png` with a weekly KPI trend (and optional mini funnel).

### Generate an executive summary

Open `exec_summary.md` and fill in:

* **Goal & primary metric**
* **Result** (effect size, p-value or CI)
* **Decision** (ship/hold/need more data)
* **Risks & guardrails** (monitoring, variants to roll back, follow-ups)

---

## Interpreting Results

* **Significant and positive** lift with practical value → propose **gradual rollout** with monitoring.
* **Not significant** but promising → outline **power & duration**; consider **follow-up** segmentation.
* **Negative or risk signals** (variance by device/channel, regression in KPI) → recommend **hold**, document **next test**.

> PM tip: Use **plain language** for the decision. Keep stats in the appendix unless asked.

---

## CI and Optional Approval

* **CI:** `.github/workflows/py-r-ci.yml` can run:

  * `python -m pytest Day4_Python_R_for_Product/tests -q`
  * `python ab_test.py ...` to ensure it completes
  * `Rscript plot_kpis.R ...` to ensure charts render
* **Optional manual approval:** `.github/workflows/deploy-readout.yml` can require an approver before publishing a readout artifact (mimics stakeholder sign-off).

---

## Submission

Open a PR titled **“Day 4 – Python & R for PMs”**. Ensure:

* Figures exist in `/figures` and look reasonable.
* `exec_summary.md` has a clear decision and next steps.
* CI (if enabled) is green. Approve the optional deploy if you added the gate.
* Merge when complete.

That’s interview-ready proof that you can move from **data to decision** with Python and R.

---

## Mermaid Troubleshooting

* Fence Mermaid at column 1: start with three backticks + `mermaid`, end with three backticks.
* Keep **only Mermaid syntax** inside the block (no extra text).
* One edge per line; avoid parentheses in labels if your renderer is picky.

```

