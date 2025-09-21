## `Day4_Python_R_for_Product/lesson.md`

```markdown
# Day 4 — Lesson Plan: Python & R for PMs (Experiment & Explain)

**Objective:** Analyze a tiny A/B test in Python, visualize KPIs in R, and write an exec-ready summary that makes a clear decision (ship / hold / need more data).

---

## Prerequisites

- Python 3.10+ and R installed.
- Git set up with your GitHub repo.
- (Nice to have) `venv` for an isolated Python environment.

Repo layout:

```

Day4\_Python\_R\_for\_Product/
├── README.md
├── lesson.md                 <-- this file
├── data/
│   ├── sample\_ab.csv
│   └── sample\_kpis.csv
├── ab\_test.py
├── plot\_kpis.R
├── exec\_summary.md
├── figures/
│   ├── ab\_lift.png
│   └── kpi\_trend.png
├── tests/
│   └── test\_ab.py
└── .github/workflows/
├── py-r-ci.yml
└── deploy-readout.yml

````

---

## Step 1 — Set up Python

From repo root:

```bash
cd Day4_Python_R_for_Product
python -m venv .venv
# Windows PowerShell
. .venv/Scripts/Activate.ps1
# macOS/Linux
source .venv/bin/activate

python -m pip install --upgrade pip
pip install pandas numpy scipy matplotlib pytest
````

**Success looks like:** no errors; `pip list` shows the packages.

---

## Step 2 — Run the A/B analysis (Python)

This script accepts either **aggregated** data (impressions & conversions per variant) or **per-user** rows.

```bash
# From Day4_Python_R_for_Product
python ab_test.py --input data/sample_ab.csv --out figures/ab_lift.png --min_lift 5
```

**You should see:** control vs treatment conversion, **lift %**, **p-value**, a **recommendation**, and a chart saved to `figures/ab_lift.png`.

> PM tip: “Significant” is not the same as “important.” Look for practical lift (e.g., ≥5%) and risk by segment before you ship.

---

## Step 3 — Plot KPIs (R)

Install R packages (first time):

```r
install.packages(c("readr","dplyr","ggplot2"))
```

Then render the trend chart:

```bash
Rscript plot_kpis.R data/sample_kpis.csv figures/kpi_trend.png
```

**You should see:** `figures/kpi_trend.png` (weekly KPI line chart).

---

## Step 4 — Exec summary (communicate the decision)

Open `exec_summary.md` and fill in:

* **Goal & primary metric**
* **Result** (effect size, CI or p-value, practical impact)
* **Decision** (ship / hold / need more data)
* **Risks & guardrails**
* **Next steps** (e.g., segment follow-ups, instrumentation gaps)

Keep it to **1–2 pages**. Lead with the decision.

---

## Step 5 — Tests (prove it)

```bash
pytest -q Day4_Python_R_for_Product
```

**Success looks like:** tests pass (0 failed). This proves your analysis function returns sane numbers on the sample data.

---

## Step 6 — CI (optional but recommended)

Create a PR so CI runs the Python test and the R script:

```bash
git checkout -b day4-python-r
git add Day4_Python_R_for_Product
git commit -m "Day 4: Python A/B analysis, R KPIs, CI"
git push -u origin day4-python-r
```

Open the PR → verify **py-r-ci** is green.

---

## Step 7 — Optional approval gate

If you added a gated “deploy” step:

1. GitHub → **Settings → Environments → New: `staging`**
2. Add yourself as **Required reviewer**
3. In the PR **Checks**, approve **deploy-readout** (it uploads `figures/` + `exec_summary.md` as an artifact)

---

## Acceptance Checklist

* **Analysis**: conversion rates, lift %, p-value, clear recommendation
* **Visualization**: KPI trend renders
* **Summary**: decision, risks, next steps
* **Tests/CI**: passing locally and in PR

---

## Troubleshooting

* **Matplotlib backend errors on CI:** We run headless; the script saves PNGs (no GUI).
* **R packages on CI:** The workflow installs them; if versions change, update `py-r-ci.yml`.
* **Numbers look odd:** Confirm `sample_ab.csv` is in **aggregated** format (see data file).

---

````

---

## `Day4_Python_R_for_Product/ab_test.py`

```python
#!/usr/bin/env python
import argparse, sys, math, json, os
from typing import Tuple, Dict
import pandas as pd
import numpy as np
from scipy.stats import norm
import matplotlib.pyplot as plt

def analyze_ab_counts(n_c: int, x_c: int, n_t: int, x_t: int) -> Dict[str, float]:
    """Two-proportion z-test + basic metrics from aggregated counts."""
    if min(n_c, n_t) <= 0:
        raise ValueError("Sample sizes must be positive.")
    p_c = x_c / n_c
    p_t = x_t / n_t
    lift = (p_t / p_c - 1.0) * 100.0 if p_c > 0 else float("inf")

    # pooled standard error
    p_pool = (x_c + x_t) / (n_c + n_t)
    se = math.sqrt(p_pool * (1 - p_pool) * (1 / n_c + 1 / n_t))
    z = 0.0 if se == 0 else (p_t - p_c) / se
    p_value = 2 * (1 - norm.cdf(abs(z)))

    return {
        "n_control": n_c, "x_control": x_c, "p_control": p_c,
        "n_treat": n_t, "x_treat": x_t, "p_treat": p_t,
        "lift_pct": lift, "z": z, "p_value": p_value,
    }

def read_ab_file(path: str) -> Tuple[int, int, int, int]:
    """
    Supports:
      A) Aggregated: variant,impressions,conversions (2 rows)
      B) Per-user:  variant,converted (0/1)
    """
    df = pd.read_csv(path)
    cols = {c.lower() for c in df.columns}
    if {"variant", "impressions", "conversions"}.issubset(cols):
        # Aggregated
        c = df[df["variant"].str.lower() == "control"].iloc[0]
        t = df[df["variant"].str.lower() == "treatment"].iloc[0]
        return int(c["impressions"]), int(c["conversions"]), int(t["impressions"]), int(t["conversions"])
    elif {"variant", "converted"}.issubset(cols):
        # Per-user
        g = df.groupby(df["variant"].str.lower())["converted"].agg(["count", "sum"])
        n_c, x_c = int(g.loc["control", "count"]), int(g.loc["control", "sum"])
        n_t, x_t = int(g.loc["treatment", "count"]), int(g.loc["treatment", "sum"])
        return n_c, x_c, n_t, x_t
    else:
        raise ValueError("CSV must be aggregated (variant,impressions,conversions) or per-user (variant,converted).")

def plot_lift(p_c: float, p_t: float, out_path: str):
    os.makedirs(os.path.dirname(out_path), exist_ok=True)
    labels = ["Control", "Treatment"]
    vals = [p_c * 100, p_t * 100]
    fig, ax = plt.subplots(figsize=(5,4), dpi=120)
    bars = ax.bar(labels, vals)
    ax.set_ylabel("Conversion (%)")
    ax.set_ylim(0, max(vals)*1.3 if max(vals) > 0 else 1)
    for b, v in zip(bars, vals):
        ax.text(b.get_x() + b.get_width()/2, v + max(vals)*0.05, f"{v:.1f}%", ha="center", va="bottom")
    ax.set_title("A/B Conversion")
    fig.tight_layout()
    fig.savefig(out_path)
    plt.close(fig)

def main():
    ap = argparse.ArgumentParser(description="Simple A/B analysis for PMs.")
    ap.add_argument("--input", required=True, help="CSV path (aggregated or per-user).")
    ap.add_argument("--out", required=True, help="PNG path for conversion chart.")
    ap.add_argument("--min_lift", type=float, default=5.0, help="Practical lift threshold in %.")
    args = ap.parse_args()

    n_c, x_c, n_t, x_t = read_ab_file(args.input)
    res = analyze_ab_counts(n_c, x_c, n_t, x_t)

    # Recommendation
    rec = "hold"
    reason = "not significant"
    if res["p_value"] < 0.05 and res["lift_pct"] >= args.min_lift:
        rec, reason = "ship", "significant & practical"
    elif res["p_value"] < 0.05 and res["lift_pct"] < args.min_lift:
        rec, reason = "need more data", "significant but not practical"
    elif res["p_value"] >= 0.05 and res["lift_pct"] >= args.min_lift:
        rec, reason = "need more data", "promising but not significant"

    summary = {
        "control_conv_pct": res["p_control"]*100,
        "treat_conv_pct": res["p_treat"]*100,
        "lift_pct": res["lift_pct"],
        "p_value": res["p_value"],
        "z": res["z"],
        "recommendation": rec,
        "reason": reason,
        "counts": res,
    }
    print(json.dumps(summary, indent=2))

    plot_lift(res["p_control"], res["p_treat"], args.out)

if __name__ == "__main__":
    main()
````

---

## `Day4_Python_R_for_Product/plot_kpis.R`

```r
#!/usr/bin/env Rscript
args <- commandArgs(trailingOnly = TRUE)
if (length(args) < 2) {
  stop("Usage: Rscript plot_kpis.R <input_csv> <out_png>")
}

in_csv <- args[1]
out_png <- args[2]

suppressPackageStartupMessages({
  library(readr)
  library(dplyr)
  library(ggplot2)
})

df <- read_csv(in_csv, show_col_types = FALSE) %>%
  mutate(date = as.Date(date))

# Expect columns: date, metric, value
trend <- df %>% filter(metric == "conversion_rate")

p <- ggplot(trend, aes(x = date, y = value)) +
  geom_line(linewidth = 0.9) +
  geom_point() +
  scale_y_continuous(labels = scales::percent_format(accuracy = 0.1)) +
  labs(title = "Weekly Conversion Rate", x = "Week", y = "Conversion Rate") +
  theme_minimal()

ggsave(out_png, p, width = 7, height = 4.5, dpi = 160)
cat("Saved:", out_png, "\n")
```

---

## `Day4_Python_R_for_Product/data/sample_ab.csv`  *(aggregated example)*

```csv
variant,impressions,conversions
control,1000,200
treatment,1000,240
```

---

## `Day4_Python_R_for_Product/data/sample_kpis.csv`

```csv
date,metric,value
2025-07-06,conversion_rate,0.180
2025-07-13,conversion_rate,0.190
2025-07-20,conversion_rate,0.200
2025-07-27,conversion_rate,0.198
2025-08-03,conversion_rate,0.205
2025-08-10,conversion_rate,0.212
2025-08-17,conversion_rate,0.220
2025-08-24,conversion_rate,0.218
```

---

## `Day4_Python_R_for_Product/exec_summary.md`  *(starter skeleton)*

```markdown
# Executive Summary — Day 4 (Python & R for PMs)

## Goal & Primary Metric
- Goal:
- Primary Metric:

## Result (Effect & Significance)
- Control vs Treatment conversion:
- Lift (%):
- p-value (or CI):
- Practical impact:

## Decision
- Ship / Hold / Need more data
- Rollout plan & guardrails:

## Risks & Assumptions
- Data quality / edge cases:
- Segments to monitor:
- Dependencies:

## Next Steps
- Follow-up tests:
- Instrumentation:
- Timeline/owners:
```

---

## `Day4_Python_R_for_Product/tests/test_ab.py`

```python
import pathlib
from Day4_Python_R_for_Product.ab_test import analyze_ab_counts

# Using the same numbers as sample_ab.csv
def test_analyze_ab_counts_basic():
    res = analyze_ab_counts(n_c=1000, x_c=200, n_t=1000, x_t=240)
    # Basic sanity
    assert abs(res["p_control"] - 0.20) < 1e-6
    assert abs(res["p_treat"] - 0.24) < 1e-6
    assert 15 <= res["lift_pct"] <= 25
    assert res["p_value"] < 0.05  # should be significant for this delta
```

> If your test runner can’t import due to package layout, run from repo root:
> `pytest -q Day4_Python_R_for_Product`

---

## `.github/workflows/py-r-ci.yml`

```yaml
name: py-r-ci
on:
  push:
  pull_request:

jobs:
  test-python-and-r:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install Python deps
        run: |
          python -m pip install --upgrade pip
          pip install pandas numpy scipy matplotlib pytest

      - name: Python unit tests
        run: pytest -q Day4_Python_R_for_Product

      - name: Run A/B script (generates figure)
        working-directory: Day4_Python_R_for_Product
        run: |
          python ab_test.py --input data/sample_ab.csv --out figures/ab_lift.png

      - name: Set up R
        uses: r-lib/actions/setup-r@v2

      - name: Install R packages
        run: |
          Rscript -e 'install.packages(c("readr","dplyr","ggplot2"), repos="https://cloud.r-project.org")'

      - name: Render KPI trend (R)
        working-directory: Day4_Python_R_for_Product
        run: |
          Rscript plot_kpis.R data/sample_kpis.csv figures/kpi_trend.png

      - name: Upload figures artifact
        uses: actions/upload-artifact@v4
        with:
          name: day4-figures
          path: Day4_Python_R_for_Product/figures/
```

---

## `.github/workflows/deploy-readout.yml`  *(optional gated “deploy”)*

```yaml
name: deploy-readout
on:
  pull_request:
    branches: [ "main", "master" ]

jobs:
  stage_readout:
    runs-on: ubuntu-latest
    environment: staging   # Configure in Settings → Environments
    steps:
      - uses: actions/checkout@v4

      - name: Collect readout artifacts
        run: |
          mkdir -p out
          cp -r Day4_Python_R_for_Product/figures out/ || true
          cp Day4_Python_R_for_Product/exec_summary.md out/ || true

      - name: Upload readout
        uses: actions/upload-artifact@v4
        with:
          name: day4-readout
          path: out/
```

---

### Quick Git steps

```bash
git add Day4_Python_R_for_Product
git commit -m "Day 4: Python A/B analysis, R KPI chart, tests, CI"
git push
```
