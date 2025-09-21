# Day 5 — Lesson Plan: Interview Readiness (Practice & Prove)

**Objective:** Convert your Day 1–4 work into interview-ready artifacts: a tight pitch, STAR stories, a product case, a tech recap, and a mock interview run. Validate everything with a tiny CI check.

---

## Prerequisites
- Days 1–4 complete (or reasonable drafts).
- Git set up with your GitHub repo.
- Python 3.10+ available (for the validator script).

Repo layout:

```

Day5\_Interview\_Readiness/
├── README.md
├── lesson.md                 <-- this file
├── pitch.md
├── star\_stories.md
├── case\_study.md
├── tech\_recap.md
├── mock\_runbook.md
├── portfolio.md
├── prompts/
│   └── day5\_prompt.md
│   └── day5\_prompt\_cheatsheet.md
├── scripts/
│   └── validate\_readiness.py
└── .github/workflows/
├── readiness-ci.yml
└── deploy-portfolio.yml

````

---

## Step 1 — Craft your 2-minute pitch
Open `pitch.md` and fill it in:
- **Who you are (1 line)** and **why you’re relevant** to a lending platform TPM role.
- **3 proof points** mapped to: consumer product, data ingestion/API, roadmap leadership.
- **Close** with the value you want to drive (e.g., conversion lift with safe risk).

**Guardrails:** 150–200 words, present-tense, no jargon, say it out loud in <120s.

---

## Step 2 — Write 3–5 STAR stories
Open `star_stories.md`. Complete at least **three** different stories:
- **Leadership / influence**
- **Ambiguity → clarity** (roadmap/backlog/user stories)
- **Technical decision** (API vendor choice, latency/rate limits/retries)
- **Failure & learning** (what changed next time)
- **Delivery under pressure**

**Guardrails:** ≤2 minutes each. Situation (20s) → Task (10s) → Action (60–90s) → Result (20s).  
End with **measurable outcomes**.

---

## Step 3 — Build the product case readout
Open `case_study.md` and outline:
- **Problem & users** (borrowers, ops, risk)
- **Metrics** (approval %, abandonment %, time-to-approval, fraud flags)
- **Solution**: UX + API ingestion + scoring iteration
- **Roadmap**: now / next / later
- **Risks & trade-offs**: latency, rate limits, false positives, ops load
- **One-slide exec summary**: 5 bullets + 1 chart (reuse Day 4 trend if helpful)

---

## Step 4 — Technical recap (fluent, not flashy)
Open `tech_recap.md`. Summarize:
- **SQL KPIs** from Day 2 and the product meaning of each.
- **API ingestion reliability** from Day 3 (timeouts, retries, idempotency, schema checks).
- **A/B** from Day 4 (practical vs statistical significance, guardrails, rollout).

Add **links** to the exact files you built in Days 2–4.

---

## Step 5 — Mock interview runbook
Open `mock_runbook.md`. Use the templates:
- **Interviewer checklist** (timing, probing, signal areas)
- **Candidate checklist** (clarity, structure, depth, outcomes)
- **Timing plan** (45–60 min) and **feedback form**
- **Question bank** (behavioral, product, technical sanity, trade-offs)

Do a 45–60 min dry run with a partner or your AI coach.

---

## Step 6 — Portfolio one-pager
Open `portfolio.md` and list:
- Links to **pitch**, **STAR stories**, **case**, **tech recap**
- Top **3 artifacts** from Days 2–4 (charts, queries, CI runs)
- **Contact & availability**

---

## Step 7 — Validate with CI
Run the validator locally or via GitHub Actions:
```bash
# From repo root
python Day5_Interview_Readiness/scripts/validate_readiness.py
````

Expect green checks. Fix any items the script flags (missing/too short/etc.).

---

## Step 8 — PR & optional approval

Create a PR titled **“Day 5 – Interview Readiness”**.
Ensure **readiness-ci** is green. (Optional) Approve **deploy-portfolio** in the PR’s Checks to upload a zipped bundle of your artifacts.

---

## Acceptance Checklist

* Pitch ≤200 words, reads clearly out loud.
* ≥3 STAR stories with measurable outcomes.
* Case has metrics, roadmap, and trade-offs.
* Tech recap ties Days 2–4 to product decisions.
* Mock run completed; feedback incorporated.
* CI validator passes; (optional) portfolio artifact uploaded.

---

## Troubleshooting

* **Validator fails**: open the printed details; files may be missing or too short.
* **Portfolio upload not waiting**: create **staging** environment and add yourself as a required reviewer.
* **Too long answers**: trim to the core signal (problem → action → measurable result).

````

---

## Starter content files (editable)

### `Day5_Interview_Readiness/pitch.md`
```markdown
# 2-Minute Pitch (TPM — Lending Platform)

**Who I am (1 line):**  
<Your name>, TPM focused on consumer data products and API-driven platforms.

**Why I’m relevant (theme):**  
I help teams ship trustworthy, data-ingesting products that lift conversion while protecting against fraud and latency risks.

**Proof points (3 bullets):**  
- Shipped a consumer workflow that increased approval conversion by X% with clear SQL KPIs and CI.  
- Led API vendor evaluation and ingestion hardening (timeouts, retries, idempotency) → reduced errors/latency by Y%.  
- Drove roadmap + user stories with risk guardrails, aligning security, data, and engineering.

**Close (fit & goal):**  
I’m excited to help scale a lending platform with reliable data ingestion and measurable conversion lift while keeping fraud and latency in check.
````

### `Day5_Interview_Readiness/star_stories.md`

```markdown
# STAR Stories

## Story 1 — Leadership / Influence
**Situation:**  
**Task:**  
**Action (bullets, crisp verbs):**  
-  
-  
**Result (numbers, time saved, reliability):**

## Story 2 — Ambiguity → Clarity
**Situation:**  
**Task:**  
**Action:**  
**Result:**

## Story 3 — Technical Decision (APIs / Data)
**Situation:**  
**Task:**  
**Action:**  
**Result:**

## (Optional) Story 4 — Failure & Learning
**Situation:**  
**Task:**  
**Action:**  
**Result:**

## (Optional) Story 5 — Delivery Under Pressure
**Situation:**  
**Task:**  
**Action:**  
**Result:**
```

### `Day5_Interview_Readiness/case_study.md`

```markdown
# Product Case — Lending Platform

## Problem & Users
- Problem statement:
- Primary users: borrowers, operations, risk

## Metrics (what matters)
- Approval %, Abandonment %, Time-to-approval (minutes), Fraud flags

## Solution Outline
- UX flow changes:
- API ingestion plan (vendor selection, retries/backoff, idempotency, schema checks):
- Scoring iteration:

## Roadmap (Now / Next / Later)
- Now:
- Next:
- Later:

## Risks & Trade-offs
- Latency, rate limits, false positives, ops load, privacy/compliance

## One-Slide Exec Summary
- 5 bullets (problem → action → outcome)
- 1 chart (reuse Day 4 KPI trend if helpful)
```

### `Day5_Interview_Readiness/tech_recap.md`

```markdown
# Technical Recap (SQL • APIs • A/B)

## SQL (Day 2)
- KPI queries and what each tells us:
  - Approval %, Abandonment %, Time-to-approval, Flagged ratio, Perf proxy
- Link to files: [schema.sql](../Day2_SQL_Data_Fundamentals/schema.sql), [queries.sql](../Day2_SQL_Data_Fundamentals/queries.sql)

## APIs & Ingestion (Day 3)
- Reliability: timeouts, retries with backoff, 429/5xx handling, idempotency, schema validation
- Link to files: [python_api_demo.py](../Day3_API_Data_Ingestion/python_api_demo.py), [ingest_to_sqlite.py](../Day3_API_Data_Ingestion/ingest_to_sqlite.py)

## A/B (Day 4)
- Practical vs statistical significance, guardrails, rollout plan
- Link to files: [ab_test.py](../Day4_Python_R_for_Product/ab_test.py), [plot_kpis.R](../Day4_Python_R_for_Product/plot_kpis.R)
```

### `Day5_Interview_Readiness/mock_runbook.md`

```markdown
# Mock Interview Runbook (45–60 min)

## Timing Plan
- 00–05  Warm-up, role framing
- 05–07  2-minute pitch
- 07–25  Behavioral (STAR)
- 25–45  Product case (metrics, roadmap, trade-offs)
- 45–55  Technical fluency (SQL, APIs, A/B)
- 55–60  Q&A and feedback

## Interviewer Checklist (Signal Areas)
- Communication (clear, structured, concise)
- Product judgment (problem, metrics, roadmap, trade-offs)
- Technical fluency (SQL KPIs, API reliability, A/B decisions)
- Ownership & delivery; learning from failure

## Candidate Checklist (Self-Grade)
- Did I answer the asked question?
- Did I quantify outcomes?
- Did I state risks and guardrails?
- Did I keep answers ≤2 minutes?

## Feedback Form (copy/paste)
- Strengths:
- Gaps:
- Next actions:
- Hire/No-Hire:
```

### `Day5_Interview_Readiness/portfolio.md`

```markdown
# Interview Portfolio (One-Pager)

**Pitch:** [pitch.md](./pitch.md)  
**STAR Stories:** [star_stories.md](./star_stories.md)  
**Product Case:** [case_study.md](./case_study.md)  
**Tech Recap:** [tech_recap.md](./tech_recap.md)

**Highlights (Days 2–4):**
- SQL KPIs & CI proof (Day 2)
- API ingestion reliability & tests (Day 3)
- A/B analysis & KPI charts (Day 4)

**Contact & Availability:**  
- Email:  
- LinkedIn:  
- Timezone:
```

---

## Prompts

### `Day5_Interview_Readiness/prompts/day5_prompt.md` (Beginner-friendly AI coach)

````markdown
# Day 5 — Interview Readiness (Beginner-Friendly AI Coach Prompt)

> Guide me one small step at a time, in plain English. After each step: tell me exactly what to do, what success looks like, the simplest fix if it fails, and ask me to paste my output before continuing.

---

## What success looks like (today)
- A **2-minute pitch** I can say out loud, under 120 seconds.
- **3–5 STAR stories** with measurable outcomes.
- A **product case** with metrics, roadmap, risks.
- A **tech recap** tying SQL, APIs, A/B to product decisions.
- A **mock interview** completed with feedback.
- A green **readiness-ci** check (validator passes).

---

## Step 0 — Location check
**PowerShell**
```powershell
cd <PATH_TO_REPO>\Day5_Interview_Readiness
Get-Location
dir
````

**bash**

```bash
cd <PATH_TO_REPO>/Day5_Interview_Readiness
pwd
ls -la
```

**Success:** See `pitch.md`, `star_stories.md`, `case_study.md`, `tech_recap.md`, `mock_runbook.md`.

**Coach ask:** Paste your file list.

---

## Step 1 — 2-minute pitch

**What/Why:** This is your opener. It sets relevance and confidence.
**Action:** Open `pitch.md` and fill it to 150–200 words. Read it out loud in <120 seconds.
**Success:** Clear, confident, no jargon; three proof points mapped to the role.
**If stuck:** Use the bullets already in the file and replace placeholders.
**Coach ask:** Paste your final paragraph (2–3 sentences).

---

## Step 2 — 3 STAR stories

**What/Why:** Stories are memory glue—prove leadership, clarity, and delivery.
**Action:** In `star_stories.md`, complete 3 different stories.
**Success:** Each ≤2 minutes, ends with a measurable result.
**If stuck:** Write “bad first draft,” then trim to actions and outcomes.
**Coach ask:** Paste one story’s Result line.

---

## Step 3 — Product case

**What/Why:** Show product judgment under constraints.
**Action:** Fill `case_study.md` (problem, metrics, solution, roadmap, risks, one-slide summary).
**Success:** Metrics align to approval, abandonment, time-to-approval, fraud.
**If stuck:** Write Now/Next/Later first.
**Coach ask:** Paste your Now/Next/Later.

---

## Step 4 — Tech recap

**What/Why:** Translate Day 2–4 into product decisions.
**Action:** Fill `tech_recap.md` with one paragraph each on SQL KPIs, API reliability, and A/B decisions. Link to the exact files.
**Success:** Plain English; decisions first, math second.
**Coach ask:** Paste one sentence about how retries/idempotency reduce risk.

---

## Step 5 — Mock interview

**What/Why:** Rehearsal increases clarity and timing.
**Action:** Use `mock_runbook.md`. Time each section; capture feedback.
**Success:** You finish inside 60 min; note 2 strengths, 2 improvements.
**Coach ask:** Paste your top improvement.

---

## Step 6 — Validate (CI)

**Any OS (repo root)**

```bash
python Day5_Interview_Readiness/scripts/validate_readiness.py
```

**Success:** All checks pass (or only “warnings” remain).
**If fails:** The script prints exactly what to fix.
**Coach ask:** Paste the last 10 lines of the validator output.

---

## Step 7 — PR & optional approval

**Any OS**

```bash
git checkout -b day5-readiness
git add Day5_Interview_Readiness
git commit -m "Day 5: Interview readiness artifacts + CI"
git push -u origin day5-readiness
```

Open a PR; verify **readiness-ci** green. (Optional) Approve **deploy-portfolio** in PR Checks.

**Success:** Green checks; zipped portfolio artifact uploaded (if approved).
**Coach ask:** Share the PR link and CI status.

---

## Evidence pack

* Pitch ≤200 words
* ≥3 STAR stories
* Case with metrics and roadmap
* Tech recap tying Days 2–4 to decisions
* Validator green; (optional) portfolio artifact

````

### `Day5_Interview_Readiness/prompts/day5_prompt_cheatsheet.md` (verification-only)
```markdown
# Day 5 — Interview Readiness (Cheat-Sheet Prompt: Verification Only)

> Minimal words. Exact commands. What “good” looks like. Quickest fix. Ask me to paste output.

## 0) Location
**PS**
```powershell
cd <REPO>\Day5_Interview_Readiness; Get-Location; dir
````

**bash**

```bash
cd <REPO>/Day5_Interview_Readiness; pwd; ls -la
```

**Good:** See pitch.md, star\_stories.md, case\_study.md, tech\_recap.md, mock\_runbook.md.

## 1) Pitch

Open `pitch.md`, keep 150–200 words.
**Good:** Read aloud in <120s.
**Paste:** Last paragraph.

## 2) STAR x3

Complete 3 stories in `star_stories.md`.
**Good:** Each ends with a measurable result.
**Paste:** One Result line.

## 3) Case

Fill `case_study.md` (metrics, roadmap, risks).
**Good:** Metrics include approval %, abandonment %, time-to-approval, fraud.
**Paste:** Now/Next/Later.

## 4) Tech recap

Complete `tech_recap.md`; link Day 2–4 files.
**Good:** Plain English; decisions first.
**Paste:** One sentence on retries/idempotency.

## 5) Validate (CI)

```bash
python Day5_Interview_Readiness/scripts/validate_readiness.py
```

**Good:** All checks pass (or only warnings).
**Fix:** Follow script hints.
**Paste:** Last 10 lines.

## 6) PR & (optional) approval

```bash
git checkout -b day5-readiness
git add Day5_Interview_Readiness
git commit -m "Day 5: Interview readiness artifacts + CI"
git push -u origin day5-readiness
```

Open PR. Approve **deploy-portfolio** in Checks (optional).
**Good:** **readiness-ci** green; artifact uploaded (if approved).
**Paste:** PR link + CI status.

````

---

## Validator & CI

### `Day5_Interview_Readiness/scripts/validate_readiness.py`
```python
#!/usr/bin/env python3
import sys, json, re
from pathlib import Path

REQUIRED_FILES = [
    "pitch.md",
    "star_stories.md",
    "case_study.md",
    "tech_recap.md",
    "mock_runbook.md",
    "portfolio.md",
]

MIN_CHARS = {
    "pitch.md": 120,           # ~150-200 words recommended (warn below)
    "star_stories.md": 300,    # 3 stories skeleton
    "case_study.md": 300,
    "tech_recap.md": 250,
    "mock_runbook.md": 200,
    "portfolio.md": 150,
}

def count_words(text: str) -> int:
    return len(re.findall(r"\b\w+\b", text))

def main():
    script_dir = Path(__file__).resolve().parent
    day5_dir = script_dir.parent  # Day5_Interview_Readiness
    ok = True
    report = {"base": str(day5_dir), "checks": []}

    for rel in REQUIRED_FILES:
        p = day5_dir / rel
        item = {"file": rel, "exists": p.exists()}
        if not p.exists():
            ok = False
            item["error"] = "missing"
        else:
            txt = p.read_text(encoding="utf-8", errors="ignore")
            n_chars = len(txt.strip())
            n_words = count_words(txt)
            item["chars"] = n_chars
            item["words"] = n_words
            min_req = MIN_CHARS.get(rel, 1)
            if n_chars < min_req:
                ok = False
                item["error"] = f"too short (<{min_req} chars)"
            # Extra heuristics
            if rel == "pitch.md":
                if n_words < 80 or n_words > 250:
                    item["warning"] = "pitch length recommended 150–200 words"
            if rel == "star_stories.md":
                story_heads = len(re.findall(r"^##\s+Story", txt, flags=re.M))
                if story_heads < 3:
                    ok = False
                    item["error"] = (item.get("error","") + "; " if "error" in item else "") + "need ≥3 stories"

        report["checks"].append(item)

    # Basic link sanity in tech_recap.md
    tr = day5_dir / "tech_recap.md"
    if tr.exists():
        txt = tr.read_text(encoding="utf-8", errors="ignore")
        if "../Day2_SQL_Data_Fundamentals" not in txt or "../Day3_API_Data_Ingestion" not in txt or "../Day4_Python_R_for_Product" not in txt:
            ok = False
            report["tech_recap_links"] = "Add relative links to Day 2–4 files as shown in the template."

    print(json.dumps(report, indent=2))
    sys.exit(0 if ok else 1)

if __name__ == "__main__":
    main()
````

### `Day5_Interview_Readiness/.github/workflows/readiness-ci.yml`

```yaml
name: readiness-ci
on:
  push:
  pull_request:

jobs:
  validate-readiness:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Run validator
        run: |
          python Day5_Interview_Readiness/scripts/validate_readiness.py
```

### `Day5_Interview_Readiness/.github/workflows/deploy-portfolio.yml`  *(optional gated upload)*

```yaml
name: deploy-portfolio
on:
  pull_request:
    branches: [ "main", "master" ]

jobs:
  bundle-and-upload:
    runs-on: ubuntu-latest
    environment: staging   # Configure in Settings → Environments
    steps:
      - uses: actions/checkout@v4

      - name: Create portfolio bundle
        run: |
          mkdir -p out
          cp Day5_Interview_Readiness/pitch.md out/ || true
          cp Day5_Interview_Readiness/star_stories.md out/ || true
          cp Day5_Interview_Readiness/case_study.md out/ || true
          cp Day5_Interview_Readiness/tech_recap.md out/ || true
          cp Day5_Interview_Readiness/portfolio.md out/ || true
          cd out && zip -r portfolio_bundle.zip . && cd ..

      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: day5-portfolio
          path: out/portfolio_bundle.zip
```

---

### Quick Git steps

```bash
git add Day5_Interview_Readiness
git commit -m "Day 5: Interview readiness lesson, prompts, validator, and CI"
git push
```

