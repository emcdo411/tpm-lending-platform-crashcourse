### `Day1_Product_Fundamentals/prompts/day1_prompt.md`

```markdown
# Day 1 Prompt — Product Fundamentals (TPM)

**Role:** You are a senior Technical Product Manager for a **consumer lending platform**.  
**Goal:** Produce Day-1 deliverables aligned with the lesson & rubric.

## Inputs (edit before running)
- **Company/Platform:** BorrowWorks (consumer lending)
- **User Segments:** New applicants (mobile/web), returning borrowers, underwriters
- **Current KPIs (baseline):** completion 42%, time-to-approval 2.8h, fraud FP 7%
- **Quarter Focus:** Increase completion, cut time-to-approval, reduce false positives

## Produce (as Markdown files)
1. **`deliverable_vision.md`**
   - 1–2 sentence **vision** aligned to borrower value & compliance.
   - **3 success KPIs** with target lifts (e.g., +15% completion).

2. **`deliverable_epics.md`**
   - **2 epics** (e.g., Pre-Approval Soft Pull; Mobile Docs & OCR).
   - For each epic include: Problem, Value Hypothesis (KPI impact), Scope (In/Out), Risks & Assumptions, **NFRs** (security, perf, reliability, observability).

3. **`deliverable_stories.md`**
   - **6–9 user stories** (INVEST) across the two epics.
   - Each story includes **GWT acceptance criteria** (cover happy path + error/timeout + rate-limit/degraded UX).
   - Note **dependencies** (APIs, SDKs, legal) and **perf targets** (e.g., p95 < 2s).

4. **`deliverable_backlog.md`**
   - **RICE** table (Reach, Impact, Confidence, Effort) for top stories.
   - **MoSCoW** labels for MVP clarity.
   - Mark at least **2 sprints** of work as **“Ready”** (meets **DoR**).
   - List **DoR** and **DoD** checklists used.

## Guardrails & Style
- Use **concise** product language; tie items to **measurable outcomes**.
- Always include **NFRs** and **observability** (events/logs/metrics).
- Prefer **mobile-first** notes where relevant (upload, latency, offline).
- Treat external services as **rate-limited**; include retries/backoff/idempotency.
- PII: **mask in logs**, least-privilege access, auditability.

## Snippets to Use

**User Story Template (INVEST)**

