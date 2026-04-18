# 05-launch-plan — Execution Artifacts Index
**Pre-launch gating through Week 7 Go/No-Go**

This folder holds the operational paper trail from engineering kickoff to the final scale decision. Artifacts are grouped by phase, not by owner, so a reviewer can follow the story chronologically.

Original plan documents (`4-week-timeline.md`, `go-no-go-criteria.md`, `risk-register.md`, `rollout-phases.png`) sit alongside the execution outputs and define the framework the execution artifacts were measured against.

---

## How to Read This Folder

- **Start with `4-week-timeline.md`** for the plan
- **Then `week-minus-2-readiness-report.md`** for the infrastructure snapshot
- **Then `week-1-launch-readiness-report.md`** for the end-of-dogfood state
- **Then `week-1-exit-review.md` and `week-7-go-no-go-memo.md`** for outcomes
- **Finally `biggest-risks-retrospective.md`** for the honest post-mortem

The rest are the supporting working documents for each phase.

---

## Phase 1: Plan & Framework (pre-existing)

| File | Purpose |
|---|---|
| `4-week-timeline.md` | Week-by-week milestones with DRIs and exit gates |
| `go-no-go-criteria.md` | Objective thresholds for the Week 7 decision |
| `risk-register.md` | Pre-launch risk inventory |
| `rollout-phases.png` | Visual timeline |

---

## Phase 2: Immediate (This Week) — Kickoff

| File | Purpose |
|---|---|
| `open-questions-tracker.md` | Working assumptions for OQ-1 through OQ-6 with owners and fallbacks |
| `engineering-kickoff-agenda.md` | 2-hr kickoff agenda, P01–P12 owner assignments, D1–D4 decisions |
| `whatsapp-templates.md` | 8 WhatsApp Business API templates drafted for Meta submission |
| `telecom-ops-brief.md` | 1800 helpline vendor RFI — Tata/Airtel/Exotel/Jio shortlist + 3 fallback options |

---

## Phase 3: Week −2 — Infrastructure Sprint

| File | Purpose |
|---|---|
| `infrastructure-runbook.md` | Deployment topology, 11-service inventory, HPA/Cloud Run scaling, on-call rotation, cutover plan |
| `load-test-plan-and-results.md` | P12 — 10 scenarios (steady-state, spike, chaos, degraded APIs) with results and 4 findings |
| `security-review-checklist.md` | P10 — 40+ items across transport, PII, retention, compliance; CISO sign-off |
| `week-minus-2-readiness-report.md` | CTO status to Product GM — all 12 P-milestones closed |

---

## Phase 4: Week −1 — Dogfood

| File | Purpose |
|---|---|
| `dogfood-scenarios.md` | 10 executable test scripts (S1–S10) with assertions and coverage matrix |
| `dogfood-bug-log.md` | 18 issues catalogued — 2 Sev-1, 6 Sev-2, 10 Sev-3; resolution status |
| `ca-invoice-approval-packet.md` | Regional CA panel review (Bansal-Kanpur, Desai-Surat, Rajeev-Jaipur) with 5 design changes |
| `week-1-launch-readiness-report.md` | CTO + 6 co-signers approve Week 1 soft launch |

---

## Phase 5: Pre-Launch Gating

| File | Purpose |
|---|---|
| `go-live-gate-verification.md` | Auditable check against all 18 PRD §7 gates — technical, quality, compliance, business |
| `soft-launch-merchant-cohort.md` | 50 merchants across Kanpur/Surat/Jaipur — selection criteria, onboarding sequence, Week 1 ops playbook |

---

## Phase 6: Parallel Workstreams

| File | Owner | Purpose |
|---|---|---|
| `legal-compliance-package.md` | Legal | Tax counsel sign-off + TRAI DND + PDPA consent copy |
| `human-agent-training-curriculum.md` | Customer Ops | 5-day program, context packet spec, 5-scenario certification |
| `ops-dashboard-spec.md` | Data Engineering | 7-panel Looker dashboard, 20-event schema, alert rules |
| `crm-segmentation-500.md` | Merchant Ops | Full 500-merchant segmentation with staged rollout |

---

## Phase 7: Decision Checkpoints

| File | Purpose |
|---|---|
| `week-1-exit-review.md` | Soft-launch results vs. 5 exit gates — **Proceed to Week 2 scale-up** |
| `week-7-go-no-go-memo.md` | Pilot close — **GO WITH CONDITIONS: scale to 5,000 merchants × 13 cities** |

---

## Phase 8: Retrospective

| File | Purpose |
|---|---|
| `biggest-risks-retrospective.md` | Honest audit of 4 pre-launch risks — 3 of 4 fully contained; STT noise (not NLU) was the actual bottleneck |

---

## What This Demonstrates

Every stage of a 0→1 launch — not just design, but the operational discipline to take a spec through infrastructure, testing, compliance, pilot, and a scale decision. The artifacts are audit-grade on purpose: owners, dates, evidence, sign-offs. That is the work senior AI PMs and engineering leaders actually do to ship a credit-activation wedge product at Paytm scale.

---

*Last updated: Week 7 of pilot*
