# Resources (Glossary, Lending Domain, Reading)

> A fast, practical companion to Days 1–5. Skim the Glossary during work, use the Lending section for context, and keep the Reading list for depth.

---

## Table of Contents
- [Glossary (TPM, Data, APIs, A/B)](#glossary-tpm-data-apis-ab)
  - [Product & Agile](#product--agile)
  - [Data & SQL](#data--sql)
  - [APIs & Ingestion](#apis--ingestion)
  - [Experimentation (A/B)](#experimentation-ab)
- [Lending Domain Essentials](#lending-domain-essentials)
  - [Lifecycle & Roles](#lifecycle--roles)
  - [Core KPIs](#core-kpis)
  - [Risk & Fraud Concepts](#risk--fraud-concepts)
  - [US Regulatory Touchpoints \[not legal advice\]](#us-regulatory-touchpoints-not-legal-advice)
- [Suggested Reading & Links](#suggested-reading--links)
  - [Product & Agile](#product--agile-1)
  - [SQL & Data](#sql--data)
  - [APIs & Reliability](#apis--reliability)
  - [Experimentation](#experimentation)
  - [Cloud, Analytics, Data Platforms](#cloud-analytics-data-platforms)
  - [Lending & Compliance](#lending--compliance)
  - [Practice Datasets](#practice-datasets)

---

## Glossary (TPM, Data, APIs, A/B)

### Product & Agile
- **Roadmap** — Sequenced outcomes tied to business goals; time-horizons (Now/Next/Later).
- **Backlog** — Ordered list of work; refined regularly for clarity, value, and sizing. See agile ceremonies for cadence. :contentReference[oaicite:0]{index=0}
- **User Story** — Customer-centric slice of value. **INVEST**: Independent, Negotiable, Valuable, Estimable, Small, Testable. :contentReference[oaicite:1]{index=1}
- **Acceptance Criteria** — Conditions that must be true for a story to be “done.”
- **SMART goals** — Specific, Measurable, Achievable/Assignable, Relevant/Realistic, Time-bound. :contentReference[oaicite:2]{index=2}
- **Agile ceremonies** — Sprint planning, daily stand-up, review/demo, retrospective; plus backlog refinement. :contentReference[oaicite:3]{index=3}

### Data & SQL
- **DDL/DML** — Define data structures (DDL) vs. manipulate data (DML).
- **Foreign Keys** — Enforce parent/child integrity (SQLite requires `PRAGMA foreign_keys=ON`). :contentReference[oaicite:4]{index=4}
- **Window functions** — Analytics over partitions (e.g., rolling, rank, lag/lead). :contentReference[oaicite:5]{index=5}
- **Denormalized vs. Normalized** — Read-optimized joins vs. integrity & flexibility trade-offs.
- **BI Tools** — Tableau/Looker et al. for dashboards (see Day 4).

### APIs & Ingestion
- **REST** — Resource-oriented over HTTP; methods have **safety** and **idempotency** properties (e.g., GET is safe & idempotent; PUT/DELETE idempotent; POST not guaranteed). :contentReference[oaicite:6]{index=6}
- **Pagination** — Break large results into pages (e.g., Link headers per Web Linking). 
- **Rate Limiting & 429** — Service throttles request rate; respect `Retry-After` and backoff. 
- **Exponential Backoff** — Increase wait time between retries to reduce load. 
- **Idempotency Keys** — Prevent duplicate effects on retries (commonly with POST). 
- **API Security** — Use least privilege, input validation, and OWASP API Top 10. :contentReference[oaicite:11]{index=11}

### Experimentation (A/B)
- **Randomization** — Assign users to variants fairly; avoid selection bias.
- **Guardrails** — Safety metrics (latency, error rate, fraud) that must not regress.
- **Peeking** — Early looks inflate false positives; stick to pre-planned analysis. 
- **Best practices** — Pre-register hypotheses, power, MDE; analyze with trusted methods. 

---

## Lending Domain Essentials

### Lifecycle & Roles
1. **Acquisition/Pre-App** → marketing, eligibility screening  
2. **Application** → borrower submits info (device/channel)  
3. **Underwriting** → **credit** checks, **KYC/AML**, fraud signals, scoring/decision  
4. **Funding** → disbursement; disclosures  
5. **Servicing** → payments, customer support  
6. **Delinquency/Collections** → cure, restructure, charge-off

### Core KPIs
- **Approval Rate** = approved / submitted  
- **Abandonment Rate** = started − submitted / started  
- **Time-to-Approval** (minutes)  
- **Fraud/KYC Flag Rate**  
- **Loss/Default Rate**, **Charge-off Rate**  
- **CAC / LTV** (business unit economics)

### Risk & Fraud Concepts
- **Identity fraud** (stolen), **synthetic identity**, **account takeover**  
- **Device & behavioral signals** (fingerprinting, velocity, geolocation)  
- **Credit risk**: **PD/LGD/EAD**; segmentation by score & features

### US Regulatory Touchpoints \[not legal advice\]
- **ECOA / Reg B** — Non-discrimination in credit; adverse action notices. 
- **FCRA** — Accuracy, permissible purpose, disputes for credit data. 
- **TILA / Reg Z** — Truthful credit terms & disclosures. 
- **GLBA** — Financial privacy & safeguards. 
- **BSA/AML (incl. CIP/KYC)** — Customer identity verification & monitoring. 

---

## Suggested Reading & Links

### Product & Agile
- **Agile ceremonies** (Atlassian Agile Coach). :contentReference[oaicite:19]{index=19}  
- **Backlog refinement** (Atlassian). :contentReference[oaicite:20]{index=20}  
- **INVEST** cheat-sheet (Agile Alliance; origin Bill Wake). :contentReference[oaicite:21]{index=21}  
- **SMART criteria** (overview). :contentReference[oaicite:22]{index=22}  
- **Books**: *Inspired* (M. Cagan), *Escaping the Build Trap* (M. Perri), *Lean Analytics* (Croll & Yoskovitz). 

### SQL & Data
- **SQLite foreign keys** & **PRAGMA** docs. :contentReference[oaicite:24]{index=24}  
- **PostgreSQL window functions** (intro + reference). :contentReference[oaicite:25]{index=25}

### APIs & Reliability
- **HTTP methods: safety & idempotency** (MDN). :contentReference[oaicite:26]{index=26}  
- **Idempotent requests** (Stripe).   
- **Backoff/retries** (Google Cloud guidance).   
- **429 Too Many Requests & Retry-After** (MDN).   
- **OWASP API Security Top 10 (2023)**. :contentReference[oaicite:30]{index=30}  
- **RFC 8288 (Web Linking)** for pagination Link headers. 

### Experimentation
- **Trustworthy Online Controlled Experiments** (Kohavi et al.).   
- **“How Not to Run an A/B Test”** (Evan Miller). 

### Cloud, Analytics, Data Platforms
- **AWS Well-Architected — Reliability Pillar**.   
- **dbt docs** (analytics engineering).   
- **Snowflake docs** (getting started). 

### Lending & Compliance
- **ECOA/Reg B** (CFPB).   
- **FCRA** (CFPB/FTC overview).   
- **TILA/Reg Z** (CFPB).   
- **GLBA privacy** (FTC).   
- **BSA/AML & CIP** (FinCEN). 

### Practice Datasets
- **Kaggle — Give Me Some Credit (credit risk)**. :contentReference[oaicite:42]{index=42}  
- **UCI — German Credit / South German Credit**. :contentReference[oaicite:43]{index=43}

---

### How to Use This Page
- **Before interviews**: rehearse terms in the Glossary, then skim Lending KPIs & regulations.  
- **During builds**: follow API reliability bullets (idempotency, retries, rate limits).  
- **When stuck**: pick one link per topic (don’t overload) and return to your Day 2–4 artifacts.

> Cross-refs:  
> Day 1 → `Day1_Product_Fundamentals/`  
> Day 2 → `Day2_SQL_Data_Fundamentals/`  
> Day 3 → `Day3_API_Data_Ingestion/`  
> Day 4 → `Day4_Python_R_for_Product/`  
> Day 5 → `Day5_Interview_Readiness/`
```
