# Day 1 — Product Fundamentals (TPM Crash Course)

[![License: DACR](https://img.shields.io/badge/License-DACR-blue.svg)](../LICENSE.md)
![Focus](https://img.shields.io/badge/Focus-Consumer%20Fintech%20%7C%20Agile%20PM-8A2BE2)
![Timebox](https://img.shields.io/badge/Time-90%E2%80%93120%20min-informational)

**Goal:** learn to think and operate like a Technical Product Manager on a **consumer lending platform**: define a vision, shape a roadmap, and write epics & user stories that are sprint-ready.

---

## Table of Contents
- [Outcomes](#outcomes)
- [Context (Lending Platform)](#context-lending-platform)
- [Workflow (Why a diagram helps today)](#workflow-why-a-diagram-helps-today)
- [What You Will Produce](#what-you-will-produce)
- [Step-by-Step Exercises](#step-by-step-exercises)
- [Templates (Copy/Paste)](#templates-copypaste)
- [Prioritization & Readiness](#prioritization--readiness)
- [Submission (Git Steps)](#submission-git-steps)
- [Quality Bar / Rubric](#quality-bar--rubric)
- [Using the /prompts Folder](#using-the-prompts-folder)

---

## Outcomes
By the end of Day 1, you will:
- Write a **1–2 sentence product vision** aligned to borrower value & risk controls.
- Draft **2 epics** and **6–9 user stories** with **clear acceptance criteria**.
- Show a **prioritized, ready** mini-backlog (enough for **~2 sprints**).
- Capture **non-functional requirements** (API, performance, security, SLAs).

---

## Context (Lending Platform)
You’re operating a consumer-facing lending app (think BorrowWorks):
- Users: prospective borrowers on web/mobile.
- Key flows: **Pre-approval → Application → KYC/Docs → Decisioning → Funding**.
- Integrations: credit/fraud/KYC **APIs**; internal risk and underwriting services.
- KPIs (examples): application completion rate, time-to-approval, fraud false positives.

---

## Workflow (Why a diagram helps today)
Day 1 is about **structuring thinking**. A workflow clarifies handoffs from *strategy to execution* and keeps today’s outputs consistent with Agile ceremonies and “ready” criteria—so yes, it **is necessary** and valuable for this topic.

```mermaid
flowchart TD
    A[Vision & Outcomes] --> B[Roadmap Themes]
    B --> C[Epic Drafts]
    C --> D[User Stories (INVEST)]
    D --> E[Acceptance Criteria (GWT)]
    E --> F[Backlog Refinement (DoR)]
    F --> G[Sprint Planning (DoD in mind)]
    G --> H[Delivery & Demo]
    H --> I[Measure KPIs & Iterate]

