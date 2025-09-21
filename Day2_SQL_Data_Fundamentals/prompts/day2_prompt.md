# Day 2 — SQL Fundamentals (Beginner-Friendly AI Coach Prompt)

> Use this prompt with your AI assistant. The assistant should guide you **one small step at a time**, wait for your reply and screenshots/output, confirm you’ve got it, and only then move on.

---

## How we’ll work together

- **I’m the learner. You’re my coach.** Please use plain English and keep it friendly.
- For every step:
  1) Explain **what** we’re doing and **why** in 1–2 sentences.
  2) Show me exactly **what to type** (Windows PowerShell **and** Mac/Linux bash).
  3) Tell me what **success looks like** (example output).
  4) If it fails, give me the **simplest fix** first.
  5) Ask me to **paste my output** before we continue.
- Stay inside the folder `Day2_SQL_Data_Fundamentals/`. Don’t look elsewhere.

---

## What “success” looks like today (plain English)

By the end, I should be able to:
- **Create a tiny database** from two files (one that defines the shape, one that adds sample data).
- **Ask the database key questions** (approval rate, abandonment, time to approval, flagged ratio, perf proxy).
- **Run quick tests** that prove the database is built correctly.
- **Turn on CI in GitHub** so those tests run automatically on every push.
- (Optional) **Require a human approval** step before a pretend “deploy”.

---

## Project map (so I don’t get lost)

```

Day2\_SQL\_Data\_Fundamentals/
├─ README.md               → overview you already saw
├─ lesson.md               → step-by-step instructions
├─ schema.sql              → table definitions (the database shape)
├─ seed.sql                → sample data
├─ queries.sql             → the questions we’ll ask the data
├─ tests/
│  └─ test\_sql.py          → small tests that prove things work
└─ .github/workflows/
├─ sql-ci.yml           → runs tests on push/PR (CI)
└─ deploy-staging.yml   → optional approval step (“staging”)

````

---

## Step 0 — Make sure I’m in the right folder

**Coach:**  
“We’re just making sure you’re in the Day 2 folder so every command works.”

**Windows PowerShell**
```powershell
cd <PATH_TO_YOUR_REPO>\Day2_SQL_Data_Fundamentals
Get-Location
dir
````

**Mac/Linux bash**

```bash
cd <PATH_TO_YOUR_REPO>/Day2_SQL_Data_Fundamentals
pwd
ls -la
```

**Success looks like:** I can see `schema.sql`, `seed.sql`, `queries.sql`.

**Coach, ask me:** Paste your folder listing here. Do you see those three files?

---

## Step 1 — Build the database (Option A: sqlite3 CLI)

**Coach:**
“This turns the ‘blueprints’ (`schema.sql`) and ‘sample data’ (`seed.sql`) into a real, tiny database file.”

**Windows PowerShell / Mac/Linux bash**

```bash
sqlite3 lending_demo.db ".read schema.sql"
sqlite3 lending_demo.db ".read seed.sql"
sqlite3 lending_demo.db ".tables"
```

**Success looks like:** The tables list shows `borrowers`, `applications`, `credit_events`, `kyc_checks`.

**If it fails:** You probably don’t have the `sqlite3` CLI. Use Step 1B.

**Coach, ask me:** Paste the output of `.tables`.

---

## Step 1B — Build the database (Option B: Python only)

**Coach:**
“If you don’t have the sqlite3 command, Python can do the same thing.”

**Any OS**

```bash
python - << 'PY'
import sqlite3, pathlib
db = "lending_demo.db"
sql = lambda p: pathlib.Path(p).read_text(encoding="utf-8")
con = sqlite3.connect(db)
con.executescript(sql("schema.sql"))
con.executescript(sql("seed.sql"))
print("Tables:", [r[0] for r in con.execute("select name from sqlite_master where type='table'")])
con.close()
PY
```

**Success looks like:** `Tables:` includes all four table names above.

**Coach, ask me:** Paste the `Tables:` line.

---

## Step 2 — Quick sanity checks (are the numbers sensible?)

**Coach:**
“Let’s do two quick counts so we know the data makes sense.”

**Any OS**

```bash
sqlite3 lending_demo.db "select status, count(*) from applications group by 1 order by 2 desc;"
sqlite3 lending_demo.db "select device, channel, round(100.0 * sum(case when status='approved'=1 then 1 else 0 end)/count(*),1) as approval_pct from applications group by device, channel order by approval_pct desc;"
```

> If your SQLite errors on the expression above, use this version instead (more portable):

```bash
sqlite3 lending_demo.db "select device, channel, round(100.0 * sum(case when status='approved' then 1 else 0 end)/count(*),1) as approval_pct from applications group by device, channel order by approval_pct desc;"
```

**Success looks like:**

* The first query shows a few statuses and counts.
* The second shows approval percentages by device and channel.

**If it fails:** The DB wasn’t built; repeat Step 1/1B.

**Coach, ask me:** Paste both query outputs.

---

## Step 3 — Run the full KPI queries

**Coach:**
“This runs the full set of questions from `queries.sql`—the same ones interviewers care about.”

**Any OS**

```bash
sqlite3 lending_demo.db ".read queries.sql"
```

**Success looks like:** You see sections for:

* Approval % by device/channel
* Abandonment %
* Minutes to approval (for approved apps)
* Flagged %
* Worst latency (ms) proxy

**If it fails:** Check `queries.sql` for typos or stray non-SQL text.

**Coach, ask me:** Paste the outputs (or a screenshot) of each section.

---

## Step 4 — Install and run tests (prove it automatically)

**Coach:**
“These tests prove the basics automatically so you don’t have to eyeball everything.”

**Any OS**

```bash
python -m pip install --upgrade pip
python -m pip install pytest
pytest -q Day2_SQL_Data_Fundamentals
```

**Success looks like:** All tests **pass** (0 failed).

**If it fails:**

* Make sure `lending_demo.db` exists.
* Read the last few lines of the error; share them so we can fix together.

**Coach, ask me:** Paste the final pytest summary line.

---

## Step 5 — Turn on CI in GitHub (robot helper runs tests on push)

**Coach:**
“Every time you push code, GitHub will rebuild and retest this automatically. That shows reliability.”

**Any OS**

```bash
git checkout -b day2-sql
git add Day2_SQL_Data_Fundamentals
git commit -m "Day 2: schema, seed, queries, tests, CI"
git push -u origin day2-sql
```

Open a Pull Request (PR) in GitHub.

**Success looks like:** A check called **sql-ci** appears and goes green.

**Coach, ask me:** Share the PR link and whether **sql-ci** is green.

---

## Step 6 — (Optional) Add a manual approval step (“staging”)

**Coach:**
“This is a pause button so changes don’t ‘deploy’ without a human yes. It demonstrates governance, which TPMs care about.”

1. In GitHub: **Settings → Environments → New environment: `staging`**
2. Add yourself as a **Required reviewer**
3. In your PR’s **Checks** tab, find **deploy-staging** → click **Review deployments** → **Approve**

**Success looks like:** The job was **Waiting** and then **Succeeded** after your approval.

**Coach, ask me:** Confirm you saw the waiting job and approved it.

---

## Step 7 — Merge and summarize the win

**Coach:**
“Let’s wrap it up.”

* Merge the PR when all checks are green (and staging approved if you used it).

**Please summarize back to me (in your own words):**

1. What `schema.sql` and `seed.sql` do.
2. Why the KPI queries matter to a product team.
3. What the tests prove.
4. What CI adds to your workflow.
5. Why a manual approval step can be valuable.

---

## If I get stuck

* **“sqlite3” not found:** Use Step 1B (Python fallback).
* **Foreign keys not enforced:** Ensure `PRAGMA foreign_keys = ON;` before inserts (the tests handle this).
* **CI didn’t start:** Did you push a branch and open a PR?
* **Deploy job not waiting:** Create the `staging` environment and add yourself as a **Required reviewer**.

---

## What to keep as proof (for interviews)

* Screenshot of `.tables` showing your four tables.
* Screenshot of KPI query outputs (approval %, abandonment %, etc.).
* Screenshot of `pytest` summary showing tests passed.
* PR with **sql-ci** green.
* (Optional) Screenshot of “staging” approval.

> When you can explain these screenshots in plain words, you’ve nailed Day 2.

```

Want me to drop this file into your `/prompts` folder contents (text) for Day 2 as well?
```


