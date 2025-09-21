# Day 3 — APIs & Ingestion (Beginner-Friendly AI Coach Prompt)

> Use this prompt with your AI assistant. The assistant should guide you **one small step at a time**, wait for your reply and your screenshots/output, confirm you’ve got it, and only then move on.

---

## How we’ll work together

- **I’m the learner. You’re my coach.** Talk to me like I’m new to this, in plain English.
- For every step:
  1) Explain **what** we’re doing and **why it matters** in one or two sentences.
  2) Show me exactly **what to type** (Windows PowerShell and Mac/Linux bash).
  3) Tell me what **success looks like** (example output).
  4) If something fails, give me the **simplest fix** first.
  5) Ask me to **paste my output** before we continue.
- Please **do not skip steps** or jump ahead. Wait for my reply each time.

---

## What “success” looks like today (plain English)

By the end, I should be able to:
- **Try an API safely** (we’ll use a demo site) with a small Python script that retries gently if the internet is slow.
- **Save API results into a tiny database** on my computer so I can keep them and count them later.
- **Run quick tests** that prove my scripts work (so I don’t have to eyeball everything).
- **Turn on CI in GitHub** so the same tests run automatically whenever I push code.
- (Optional) **Require approval** before a pretend “deploy,” to show I understand safe releases.

---

## Project map (so I don’t get lost)

```

Day3\_API\_Data\_Ingestion/
├─ README.md               → overview you already saw
├─ lesson.md               → step-by-step instructions
├─ python\_api\_demo.py      → tries the API in a safe way
├─ ingest\_to\_sqlite.py     → saves API results into a file database
├─ openapi.yaml            → short spec describing the API response
├─ vendor\_scorecard.md     → simple table to compare two API vendors
├─ prompts/
│  └─ day3\_prompt.md       → (this file)
├─ tests/
│  ├─ test\_client.py       → tests for the API script
│  └─ test\_ingest.py       → tests for the database script
└─ .github/workflows/
├─ api-ci.yml           → runs tests on push/PR (CI)
└─ deploy-ingestion.yml → optional approval step (“staging”)

````

---

## Step 0 — Make sure I’m in the right folder

**Coach, say this to me plainly:**  
“We’re just making sure you’re in the folder for Day 3 so every command works.”

**Windows PowerShell**
```powershell
cd <PATH_TO_YOUR_REPO>\Day3_API_Data_Ingestion
Get-Location
dir
````

**Mac/Linux bash**

```bash
cd <PATH_TO_YOUR_REPO>/Day3_API_Data_Ingestion
pwd
ls -la
```

**Success looks like:** I can see files like `python_api_demo.py` and `ingest_to_sqlite.py`.

**Coach, ask me:** Paste your folder listing here. Do you see those files?

---

## Step 1 — Set the environment (the “knobs”)

**Coach:**
“These are just settings the scripts read. Think of them as dials: website address, key, and how patient the script is.”

Create a `.env` file (or set env vars). We’ll use a safe public demo site.

**File contents (any OS)**

```
BASE_URL=https://httpbin.org
API_KEY=demo-key
REQUEST_TIMEOUT_SECS=5
MAX_RETRIES=3
BACKOFF_SECS=0.5
```

**Success looks like:** `.env` exists and has those five lines.

**Coach, ask me:** Confirm your `.env` is created and shows those lines.

---

## Step 2 — Try the API (gently and safely)

**Coach:**
“This script knocks on the API’s door, waits a short time, and tries again politely if the internet is slow or the site asks us to wait.”

**Windows PowerShell**

```powershell
$env:BASE_URL="https://httpbin.org"; $env:API_KEY="demo-key"; python python_api_demo.py
```

**Mac/Linux bash**

```bash
BASE_URL="https://httpbin.org" API_KEY="demo-key" python python_api_demo.py
```

**Success looks like:** A small JSON blob with:

* `status_code` (should be 200),
* `latency_ms` (a number),
* `json_payload` (the API’s reply),
* `attempts` (usually 1).

**If it fails:**

* “Command not found”: install Python 3 and try again.
* SSL/network error: try again; or check your internet connection.

**Coach, ask me:** Paste your JSON output. Do you see `status_code: 200`?

---

## Step 3 — Save results into a tiny database (so we can count them)

**Coach:**
“Now we’ll store a couple of API replies in a small database file, like dropping receipts in a labeled folder. Then we’ll count them.”

**Any OS**

```bash
python ingest_to_sqlite.py
python - << 'PY'
import sqlite3
con=sqlite3.connect("ingestion_demo.db")
print("Tables:", con.execute("select name from sqlite_master where type='table'").fetchall())
print("Count:", con.execute("select count(*) from api_events").fetchone())
con.close()
PY
```

**Success looks like:**

* Tables include `api_events`
* Count is at least `2`

**If it fails:**

* Delete `ingestion_demo.db` and run again.
* Ensure you ran Step 2 successfully at least once.

**Coach, ask me:** Paste the `Tables:` and `Count:` lines.

---

## Step 4 — Describe and compare (light docs)

**Coach:**
“Short docs make future you (and your teammates) faster. We’ll keep it light.”

* Open `openapi.yaml`. Confirm it describes `/get` with a `page` parameter.
* Open `vendor_scorecard.md`. Fill a few cells (auth method, rate limits, latency guess).

**Success looks like:** Both files exist and have basic content.

**Coach, ask me:** Tell me one line you added to the scorecard.

---

## Step 5 — Run tests (so we don’t have to eyeball everything)

**Coach:**
“These tests simulate the API and the database to prove your scripts behave. No real internet needed.”

**Any OS**

```bash
python -m pip install --upgrade pip
python -m pip install pytest requests requests-mock python-dotenv
pytest -q Day3_API_Data_Ingestion
```

**Success looks like:** All tests **pass** (0 failed).

**If it fails:**

* Read the last few lines of the error; share them here and we’ll fix together.

**Coach, ask me:** Paste the final line from pytest (e.g., `2 passed`).

---

## Step 6 — Turn on CI in GitHub (robot helper runs the tests for you)

**Coach:**
“Every time you push code, GitHub will run these same tests. This shows reliability to interviewers and teammates.”

**Any OS**

```bash
git checkout -b day3-apis
git add Day3_API_Data_Ingestion
git commit -m "Day 3: resilient client, ingestion, tests, CI"
git push -u origin day3-apis
```

Open a Pull Request (PR) in GitHub.

**Success looks like:** A check called **api-ci** appears and turns green.

**Coach, ask me:** Share the PR link and whether **api-ci** is green.

---

## Step 7 — (Optional) Add a manual approval step (“staging”)

**Coach:**
“This is a pause button so changes don’t auto-deploy without a human yes.”

1. In GitHub: **Settings → Environments → New environment: `staging`**
2. Add yourself as a **Required reviewer**
3. In your PR’s **Checks** tab, find **deploy-ingestion** → click **Review deployments** → **Approve**

**Success looks like:** The job was **Waiting** and then **Succeeded** after your approval.

**Coach, ask me:** Confirm you saw the waiting job and approved it.

---

## Step 8 — Merge and summarize the win

**Coach:**
“Let’s bring it home.”

* Merge the PR when all checks are green (and staging approved if you used it).

**Please summarize back to me:**

1. What the API script does in simple words.
2. What the ingestion script does.
3. What the tests prove.
4. What CI adds to your workflow.
5. Why a manual approval step can be valuable.

---

## If I get stuck

* **Python not found:** Install Python 3 and retry.
* **Tests can’t import files:** Make sure you’re in `Day3_API_Data_Ingestion/`.
* **CI didn’t start:** Did you push the branch and open a PR?
* **Deploy job not waiting:** Create the `staging` environment with you as a Required reviewer.

---

## What to keep as proof (for interviews)

* Screenshot of `status_code: 200` from the API script.
* Screenshot of `Tables:` and `Count:` after ingestion.
* Screenshot of `pytest` saying all tests passed.
* PR showing **api-ci** green.
* (Optional) Screenshot of “staging” approval.

> When you can explain these five screenshots in plain words, you’ve nailed Day 3.

```
```

