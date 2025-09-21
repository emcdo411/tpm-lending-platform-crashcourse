# Day 4 — Python & R for PMs (Cheat-Sheet Prompt: Verification Only)

> Use this with your AI coach during a live demo. The coach should only:  
> (1) tell me what to run, (2) what “success” looks like, (3) the quickest fix if it fails,  
> and (4) wait for my pasted output before moving on.

---

## 0) Location check (are we in the right folder?)
**Why:** Everything below assumes this path.

**Windows PowerShell**
```powershell
cd <PATH_TO_REPO>\Day4_Python_R_for_Product
Get-Location
dir
````

**macOS/Linux**

```bash
cd <PATH_TO_REPO>/Day4_Python_R_for_Product
pwd
ls -la
```

**Success:** You see `ab_test.py`, `plot_kpis.R`, `data/`.
**If fails:** `cd` into the correct repo/folder.

---

## 1) A/B analysis (Python)

**Why:** Proves data → decision pipeline starts.

**Any OS**

```bash
python ab_test.py --input data/sample_ab.csv --out figures/ab_lift.png --min_lift 5
```

**Success:** Console JSON shows `control_conv_pct`, `treat_conv_pct`, `lift_pct`, `p_value`, `recommendation`. File `figures/ab_lift.png` exists.
**If fails:** Ensure Python 3 is installed and `data/sample_ab.csv` exists.

**Paste:** JSON summary + confirm PNG exists.

---

## 2) KPI trend (R)

**Why:** Proves you can visualize KPIs for stakeholders.

**First time only (in R):**

```r
install.packages(c("readr","dplyr","ggplot2"))
```

**Any OS**

```bash
Rscript plot_kpis.R data/sample_kpis.csv figures/kpi_trend.png
```

**Success:** `figures/kpi_trend.png` exists.
**If fails:** Install R / run the `install.packages(...)` snippet above.

**Paste:** Confirm PNG exists + 1-sentence trend readout.

---

## 3) Unit test (Python)

**Why:** Proves repeatability (no hand-waving).

**Any OS (from repo root)**

```bash
pytest -q Day4_Python_R_for_Product
```

**Success:** `1 passed` (or similar).
**If fails:** Run from **repo root**; ensure dependencies installed (pandas/numpy/scipy/matplotlib/pytest).

**Paste:** Final pytest summary line.

---

## 4) CI quick check (GitHub Actions)

**Why:** Proves automation on push/PR.

**Any OS**

```bash
git checkout -b day4-python-r
git add Day4_Python_R_for_Product
git commit -m "Day 4: Python A/B analysis, R KPI chart, tests, CI"
git push -u origin day4-python-r
```

Open a PR in GitHub.

**Success:** Workflow **py-r-ci** runs and turns green.
**If fails:** Open PR to main/master; check Actions logs for missing packages.

**Paste:** PR link + CI status.

---

## 5) (Optional) Approval gate — “staging”

**Why:** Shows governance (a safe pause button).

1. GitHub → **Settings → Environments → New: `staging`** → add yourself as **Required reviewer**
2. In PR **Checks**, approve **deploy-readout**

**Success:** Job was **Waiting**, then **Succeeded** after your approval.
**If fails:** Confirm environment name matches workflow and you’re a required reviewer.

**Paste:** Screenshot/note of approval.

---

## 6) Evidence pack (show the win)

Provide:

* JSON summary from Step 1 (with `lift_pct`, `p_value`, `recommendation`)
* `figures/ab_lift.png` and `figures/kpi_trend.png`
* Pytest summary (`… passed`)
* PR link with **py-r-ci** green
* (Optional) Screenshot of **deploy-readout** approval

> When you can explain these artifacts in plain English (what, so-what, now-what), you’ve nailed Day 4.

```
