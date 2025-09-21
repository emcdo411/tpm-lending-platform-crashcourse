# Day 1 — Product Fundamentals (Lesson)

[![License: DACR](https://img.shields.io/badge/License-DACR-blue.svg)](../LICENSE.md)
![Focus](https://img.shields.io/badge/Focus-Consumer%20Fintech%20%7C%20Agile%20PM-8A2BE2)
![Timebox](https://img.shields.io/badge/Time-90%E2%80%93120%20min-informational)

**Objective:** Operate like a Technical Product Manager (TPM) on a **consumer lending platform**—craft a vision, shape a roadmap, decompose into epics & user stories, and prepare a sprint-ready backlog with measurable outcomes.

---

## 1) Context: Consumer Lending Platform
- **Users:** Borrowers on web/mobile; internal underwriters/ops.
- **Journey:** Pre-approval → Application → KYC/Docs → Decisioning → Funding.
- **Integrations:** Credit/KYC/Fraud **APIs**, risk services, notifications.
- **Core KPIs:** App completion rate, time-to-approval, fraud false positives, approval rate, cost per funded loan.

---

## 2) Strategy → Execution: The Through-Line (diagram)
A simple flow keeps artifacts consistent from **vision** to **delivery** (useful on Day 1 to avoid disjointed outputs).

```mermaid
flowchart TD
A[Vision and Outcomes] --> B[Roadmap Themes]
B --> C[Epic Drafts]
C --> D[User Stories - INVEST]
D --> E[Acceptance Criteria - GWT]
E --> F[Backlog Refinement - DoR]
F --> G[Sprint Planning - DoD in mind]
G --> H[Delivery and Demo]
H --> I[Measure KPIs and Iterate]
Why include the diagram? Day 1’s risk is creating artifacts that don’t connect. This visual keeps the line tight from strategy → KPIs → stories → readiness → measurement.

3) Core Concepts (TPM Fundamentals)
Product Vision (1–2 sentences)
Tie to borrower value + risk/compliance outcomes.

Example:
“Enable qualified borrowers to secure funds in minutes—while reducing fraud and ensuring compliant decisions.”

Roadmap Themes
Quarter-sized value buckets (e.g., Pre-Approval, Mobile Docs & OCR, Fraud/KYC Orchestration).

Epics
Big outcomes that move KPIs. Capture: problem, value hypothesis, in-scope/out-of-scope, risks/assumptions.

User Stories (INVEST)
Independent, Negotiable, Valuable, Estimable, Small, Testable.

Format: As a <role>, I want <capability>, so that <benefit>.

Acceptance Criteria (Gherkin GWT)
Given / When / Then (include error/timeout paths, rate-limits, degraded UX).

Non-Functional Requirements (NFRs)
Security/Compliance: PII handling, audit trails, least privilege.

Performance: p95 latency budgets (e.g., credit API < 2s), mobile perf.

Reliability/SLAs: retries/backoff, idempotency, vendor SLOs.

Observability: events, logs, metrics (funnel, error rates).

Scalability: bursts at peak times; pagination & rate-limit strategy.

Readiness & Done
DoR: Story + GWT + dependencies + perf/security notes, sized, testable.

DoD: Criteria met, tests/monitoring/docs updated, demoable.

Prioritization
RICE: Reach × Impact × Confidence ÷ Effort.

MoSCoW: Must/Should/Could/Won’t for MVP clarity.

4) Worked Example (condensed)
Epic: Loan Pre-Approval (Soft Pull)

Problem: High abandon in early flow.

Value Hypothesis: Softer upfront signal raises completion +15%.

In-Scope: Credit API soft-pull, eligibility banner, funnel eventing.

Risks: API rate-limits; inconsistent payloads; mobile network variance.

NFRs: p95 < 2s, retries with jitter, PII masked in logs.

User Story 1
As a borrower, I want a pre-approval result after a quick identity check so that I know if I should continue the full application.
Acceptance (GWT):

Given a valid SSN format and consent, when I submit pre-approval, then I see an eligibility result in under 2 seconds.

Given the credit API times out, when I retry once, then I see a friendly “try again” with no duplicate requests.

Given rate-limit responses, when I reattempt after 2s backoff, then the request succeeds or fails with guidance.

User Story 2
As an underwriter, I want to see the pre-approval decision inputs so that I can validate accuracy during review.
Acceptance: includes field provenance, timestamp, correlation id, and redacted PII.

5) Deliverables (create these today)
deliverable_vision.md — 1–2 sentence vision + 3 KPIs.

deliverable_epics.md — 2 epics (problem, value, scope, risks, NFRs).

deliverable_stories.md — 6–9 stories with GWT criteria.

deliverable_backlog.md — RICE/MoSCoW table; 2 sprints “Ready.”

6) Rubric (self-check)
Clarity (30%): Vision ties to KPIs; stories are crisp and testable.

Decomposition (30%): Epics → INVEST stories; errors and NFRs covered.

Readiness (25%): Top items meet DoR; dependencies explicit.

Prioritization (15%): RICE/MoSCoW reflects value & effort.

7) Next Step
Open the companion prompt (prompts/day1_prompt.md) and use it to draft your Day-1 deliverables quickly—then refine by hand to hit the rubric.
