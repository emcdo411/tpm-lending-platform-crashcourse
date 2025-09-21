# Day 4 — Python & R for PMs (Beginner-Friendly AI Coach Prompt)

> Use this prompt with your AI assistant. The assistant should guide you **one small step at a time**, wait for your reply and screenshots/output, confirm you’ve got it, and only then move on.

---

## How we’ll work together

- **I’m the learner. You’re my coach.** Speak in plain English.
- For every step:
  1) Tell me **what** we’re doing and **why** (1–2 sentences).
  2) Show me exactly **what to type** (Windows PowerShell **and** Mac/Linux bash).
  3) Tell me what **success looks like** (example output or file).
  4) If it fails, give me the **simplest fix** first.
  5) Ask me to **paste my output** before we continue.
- Please stay inside `Day4_Python_R_for_Product/`. Don’t look elsewhere.

---

## What “success” looks like today (plain English)

By the end I should be able to:
- Run a small **A/B analysis** in Python that reports conversion, lift %, and a simple **recommendation** (ship / hold / need more data).
- Create a clean **KPI trend chart** in R and save it as a PNG.
- Write a short, **exec-ready summary** that explains the decision and risks.
- Prove the analysis works by running **tests locally** and (optionally) in **GitHub Actions CI**.

---

## Project map (so I don’t get lost)

```

Day4\_Python\_R\_for\_Product/
├─ README.md               → overview you already saw
├─ lesson.md               → step-by-step instructions
├─ data/
│  ├─ sample\_ab.csv        → tiny A/B dataset (aggregated)
│  └─ sample\_kpis.csv      → weekly KPI data
├─ ab\_test.py              → Python A/B analysis script
├─ plot\_kpis.R             → R plotting script
├─ exec\_summary.md         → your 1–2 page readout
├─ figures/
│  ├─ ab\_lift.png          → Python chart output
│  └─ kpi\_trend.png        → R chart output
├─ tests/
│  └─ test\_ab.py           → small Python unit test
└─ .github/workflows/
├─ py-r-ci.yml          → runs tests/plots on push/PR (CI)
└─ deploy-readout.yml   → optional approval step (“staging”)

````

---

## Step 0 — Make sure I’m in the right folder

**Coach:**  
“We’re just making sure you’re in the Day 4 folder so every command works.”

**Windows PowerShell**
```powershell
cd <PATH_TO_YOUR_REPO>\Day4_Python_R_for_Product
Get-Location
dir
````

**Mac/Linux bash**

```bash
cd <PATH_TO_YOUR_REPO>/Day4_Python_R_for_Product
pwd
ls -la
```

**Success looks like:** I can see `ab_test.py`, `plot_kpis.R`, and the `data/` folder.

**Coach, ask me:** Paste your folder listing here. Do you see those files?

---

## Step 1 — Set up Python once (safe sandbox)

**Coach:**
“This creates a small, clean Python ‘workbench’ so we don’t break other projects.”

**Windows PowerShell**

```powershell
python -m venv .venv
. .venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install pandas numpy scipy matplotlib pytest
```

**Mac/Linux bash**

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install pandas numpy scipy matplotlib pytest
```

**Success looks like:** No errors; `pip list` shows those packages.

**If it fails:**

* If “python not found,” install Python 3 and try again.
* If activation fails on Windows, run PowerShell as admin and `Set-ExecutionPolicy RemoteSigned` (then retry activation).

**Coach, ask me:** Confirm your environment is active (you should see `(.venv)` in your prompt).

---

## Step 2 — Run the A/B analysis (Python)

**Coach:**
“This script compares Control vs Treatment, calculates conversion and lift, and gives a simple recommendation.”

**Any OS (run inside Day4\_Python\_R\_for\_Product)**

```bash
python ab_test.py --input data/sample_ab.csv --out figures/ab_lift.png --min_lift 5
```

**Success looks like:**

* The terminal prints a small JSON summary with:

  * `control_conv_pct`, `treat_conv_pct`
  * `lift_pct`
  * `p_value`
  * `recommendation` like `"ship"`, `"hold"`, or `"need more data"`
* A PNG chart saved to `figures/ab_lift.png`.

**If it fails:**

* Make sure the path `data/sample_ab.csv` exists.
* If matplotlib errors, just re-run; we save to PNG (no GUI needed).

**Coach, ask me:** Paste your JSON summary and confirm that `figures/ab_lift.png` exists.

---

## Step 3 — Plot KPI trend (R)

**Coach:**
“This turns weekly KPI data into a chart so you can explain the trend at a glance.”

First-time R packages (one time only, inside R):

```r
install.packages(c("readr","dplyr","ggplot2"))
```

Then run the script:

**Any OS (from Day4\_Python\_R\_for\_Product)**

```bash
Rscript plot_kpis.R data/sample_kpis.csv figures/kpi_trend.png
```

**Success looks like:** `figures/kpi_trend.png` exists and shows a line chart.

**If it fails:**

* If `Rscript` not found, install R from CRAN; then try again.
* If package error, run the `install.packages(...)` command in R and retry.

**Coach, ask me:** Confirm the chart file exists and describe what the trend looks like in one sentence.

---

## Step 4 — Write the exec summary (explain the decision)

**Coach:**
“This is what leaders read. Keep it short and specific.”

Open `exec_summary.md` and fill in:

* **Goal & primary metric** (what we’re trying to move)
* **Result** (effect size, p-value or CI, practical impact)
* **Decision** (ship / hold / need more data) and your **why**
* **Risks & guardrails** (what could go wrong and how you’ll catch it)
* **Next steps** (follow-up tests or monitoring)

**Success looks like:** 1–2 pages, with a clear recommendation and 2–3 risks you’ll monitor.

**Coach, ask me:** Paste your Decision paragraph (2–3 sentences).

---

## Step 5 — Prove it with tests (quick, automatic check)

**Coach:**
“These tests confirm the math doesn’t accidentally change later.”

**Any OS (from repo root)**

```bash
pytest -q Day4_Python_R_for_Product
```

**Success looks like:** `1 passed` (or similar), no failures.

**If it fails:**

* Read the last few lines of the error and paste here.
* Make sure you run from the **repo root** (so imports work), not inside the `tests/` folder.

**Coach, ask me:** Paste your pytest summary line.

---

## Step 6 — Turn on CI in GitHub (robot runs it for you)

**Coach:**
“Every time you push code, GitHub will run the same checks. This shows reliability.”

**Any OS**

```bash
git checkout -b day4-python-r
git add Day4_Python_R_for_Product
git commit -m "Day 4: Python A/B analysis, R KPI chart, tests, CI"
git push -u origin day4-python-r
```

Open a Pull Request (PR) in GitHub.

**Success looks like:** A check called **py-r-ci** appears and turns green.

**Coach, ask me:** Share the PR link and whether **py-r-ci** is green.

---

## Step 7 — (Optional) Add a manual approval step (“staging”)

**Coach:**
“This is a pause button—nothing gets ‘published’ without a human yes.”

1. In GitHub: **Settings → Environments → New environment: `staging`**
2. Add yourself as a **Required reviewer**
3. In the PR **Checks** tab, find **deploy-readout** → **Review deployments** → **Approve**

**Success looks like:** The job was **Waiting** and then **Succeeded** after your approval.

**Coach, ask me:** Confirm you saw the waiting job and approved it.

---

## Step 8 — Merge and summarize the win

**Coach:**
“Let’s close it out.”

* Merge the PR when all checks are green (and staging approved if you used it).

**Please summarize back to me (in your own words):**

1. What the A/B script does in simple terms.
2. What the KPI chart communicates.
3. What the tests and CI protect you from.
4. Your final decision and the top risk you’ll monitor.

---

## If I get stuck

* **Python not found / venv issues:** Install Python 3, retry Step 1; on Windows, activate with `. .venv\Scripts\Activate.ps1`.
* **Rscript not found:** Install R from CRAN; then retry Step 3.
* **Plots not generated:** Ensure the `figures/` folder exists or let the scripts create it.
* **Tests can’t import files:** Run `pytest` from the **repo root**, not inside `tests/`.
* **CI didn’t start:** Did you push the branch and open a PR?
* **Deploy job not waiting:** Create the `staging` environment and add yourself as a **Required reviewer**.

---

## What to keep as proof (for interviews)

* Screenshot of the Python JSON summary (with `lift_pct`, `p_value`, and recommendation).
* `figures/ab_lift.png` and `figures/kpi_trend.png`.
* Screenshot of `pytest` summary (tests passed).
* PR showing **py-r-ci** green.
* (Optional) Screenshot of **deploy-readout** approval in **staging**.

> When you can explain these artifacts in plain words, you’ve nailed Day 4.

```

Want me to also generate a tiny **Day 4 cheat-sheet prompt** that focuses only on verification checkpoints for live demos?
```

