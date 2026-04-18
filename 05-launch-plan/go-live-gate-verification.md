# Go-Live Gate Verification
**18-Item Auditable Check Against PRD §7 · Evidence · Sign-Offs**
*Paytm Merchant Travel Desk | 05-launch-plan | CTO-Owned*

---

## Purpose

PRD §7 defines 18 gates that must be closed before any merchant-facing launch. This document is the audit-grade verification: each gate, the evidence that closed it, the signatory, and the date.

Nothing is verified unless evidence is cited in this document. If an auditor asks "is this ready?", this is the answer.

---

## Overall Status

| Category | Gates | Passed | Failed | Evidence Link |
|---|---|---|---|---|
| **Technical** | 5 | 5 | 0 | Sections below |
| **Quality** | 5 | 5 | 0 | Sections below |
| **Compliance** | 4 | 4 | 0 | Sections below |
| **Business** | 4 | 4 | 0 | Sections below |
| **TOTAL** | **18** | **18** | **0** | — |

**Verdict: ALL GATES PASSED. Launch authorized for Monday 09:00 IST.**

---

## Technical Gates (5)

### T1 — All 12 P-milestones from Week −2 Complete

| Field | Value |
|---|---|
| **Requirement** | All P01–P12 closed per timeline |
| **Evidence** | `week-minus-2-readiness-report.md` — all 12 closed; every milestone has DRI + exit criterion met |
| **Verified by** | CTO + Engineering Lead |
| **Date** | Week −2 Friday |
| **Status** | ✅ PASS |

### T2 — Load Test < 3% Error Rate at 50 Concurrent Calls

| Field | Value |
|---|---|
| **Requirement** | P12 acceptance: <3% error at 50 concurrent |
| **Evidence** | `load-test-plan-and-results.md` — S1 observed 0.4% (1,432 calls, 6 errors). S5 at 100 concurrent observed 2.1% — provides 2× headroom. |
| **Verified by** | QA Lead + CTO |
| **Date** | Week −2 Friday |
| **Status** | ✅ PASS |

### T3 — Call-Drop Recovery Tested and Verified for All Stages

| Field | Value |
|---|---|
| **Requirement** | US-010 recovery flow for all 5 stages |
| **Evidence** | S7 (Redis primary kill) + S10 (drop flood) in load test; Scenario S4 in dogfood (Stage 3 call drop). Session recovery: 99.1%. WA resume link p95 6.2 sec (< 10 sec SLA). Stages 1–5 each tested independently via `ivr-stress-bot`. |
| **Verified by** | QA Lead |
| **Date** | Week −2 Fri (load test) + Week −1 Tue (dogfood S4) |
| **Status** | ✅ PASS |

### T4 — All 8 WA Templates Approved by Meta

| Field | Value |
|---|---|
| **Requirement** | 8 templates × 2 languages = 16 submissions approved |
| **Evidence** | `whatsapp-templates.md` — Meta approval log. 14 approved first round; 2 revised for MARKETING-category risk and approved on second submission. Template #3 (payment card) re-approved post-BUG-D04 cosmetic fix. |
| **Verified by** | Platform Engineer + PM |
| **Date** | Week −2 Thu (initial) + Week −1 Wed (fix) |
| **Status** | ✅ PASS |

### T5 — Security Pen Test Complete; All Critical Findings Remediated

| Field | Value |
|---|---|
| **Requirement** | External + internal pen test with zero critical open |
| **Evidence** | `security-review-checklist.md` V1+V2: external pen test = 0 critical, 2 medium (remediated). Internal = 0 critical, 1 medium (remediated). BUG-D02 (retention violation found in dogfood) fixed within 24 hours with CISO re-audit. |
| **Verified by** | CISO + CTO |
| **Date** | Week −2 Thu (pen test) + Week −1 Wed (BUG-D02 fix) |
| **Status** | ✅ PASS |

---

## Quality Gates (5)

### Q1 — Dogfood: 40+ Bookings by Internal Staff

| Field | Value |
|---|---|
| **Requirement** | ≥40 real bookings completed |
| **Evidence** | `dogfood-bug-log.md` — 47 bookings across 20 dogfooders. Completion rate 89%. Full funnel logged in BigQuery. |
| **Verified by** | QA Lead + PM |
| **Date** | Week −1 Friday |
| **Status** | ✅ PASS |

### Q2 — NLU Accuracy > 90% on 200-Sample Test Set

| Field | Value |
|---|---|
| **Requirement** | >90% intent + entity accuracy on holdout |
| **Evidence** | P02 initial = 91.4%. After BUG-D03 retrain (NLU v1.0-b), Hinglish accuracy lifted 92.8% → 94.6%. Combined 200-sample holdout accuracy: 93.2%. |
| **Verified by** | ML Engineer + AI/ML Lead |
| **Date** | Week −2 Thu (v1.0) + Week −1 Wed (v1.0-b) |
| **Status** | ✅ PASS |

### Q3 — STT WER > 85% on 50-Call Noisy Environment Test

| Field | Value |
|---|---|
| **Requirement** | >85% usable transcription in noise |
| **Evidence** | Week −2 S9 observed 82.4% (under target). F4 remediation (noise gate tightened to -40 dBFS + adaptive bandpass) re-ran Week −1 Thu: 87.6% WER usable. CTO-personal test in S8 dogfood (70 dB Bollywood music background) confirmed real-world recovery. |
| **Verified by** | AI/ML Engineer + CTO |
| **Date** | Week −1 Thursday (post-F4 fix) |
| **Status** | ✅ PASS |

### Q4 — GST Invoice Format Approved by 3 CAs

| Field | Value |
|---|---|
| **Requirement** | ≥3 CAs sign off on invoice format |
| **Evidence** | `ca-invoice-approval-packet.md` — CA1 (Bansal, Kanpur), CA2 (Desai, Surat), CA3 (Rajeev, Jaipur) all signed. 5 design changes applied. Signed letters on file with Finance + Legal. |
| **Verified by** | Finance Lead + Tax Counsel |
| **Date** | Week −1 Tue (CA3) → Thu (CA2 final) |
| **Status** | ✅ PASS |

### Q5 — Human Handoff Context Packet Verified for 5 Trigger Types

| Field | Value |
|---|---|
| **Requirement** | All 5 handoff trigger types tested end-to-end |
| **Evidence** | D05 in dogfood — explicit request, 3-fail AI, emotion detection, complex case (refund), and group booking attempt all triggered. Context packet rendered correctly on agent console in all 5 cases. Human agent feedback: "Packet saved 30–45 sec per call." |
| **Verified by** | Customer Ops Lead + QA Lead |
| **Date** | Week −1 Thursday |
| **Status** | ✅ PASS |

---

## Compliance Gates (4)

### C1 — Legal Sign-Off on GST Invoice Template (Tax Counsel)

| Field | Value |
|---|---|
| **Requirement** | In-house tax counsel approval |
| **Evidence** | Letter from General Counsel's office dated Week −1 Thursday. NFR-C3 closed. |
| **Verified by** | Tax Counsel |
| **Date** | Week −1 Thursday |
| **Status** | ✅ PASS |

### C2 — PDPA Consent Language Approved

| Field | Value |
|---|---|
| **Requirement** | Legal-approved consent language in onboarding |
| **Evidence** | Consent copy for SMS + WA onboarding messages reviewed and approved. Welcome message sent to 48 of 50 soft-launch merchants Friday; 2 acknowledged by phone (data captured). Consent disclosure played in Stage 1 TTS: *"Yeh call quality ke liye record ho sakti hai."* |
| **Verified by** | Legal Counsel + DPO |
| **Date** | Week −2 Friday |
| **Status** | ✅ PASS |

### C3 — DND Compliance Check Tool Integrated and Tested

| Field | Value |
|---|---|
| **Requirement** | TRAI DND registry checked pre-outbound |
| **Evidence** | NFR-C2. DND check integrated into outbound-call service. Dogfood S10 (proactive call) confirmed pre-check fired. 3 of 20 dogfooders were on DND registry; system correctly blocked outbound attempts. Analytics event `dnd_checked_outbound` firing correctly. |
| **Verified by** | Legal + Platform Engineer |
| **Date** | Week −1 Wednesday |
| **Status** | ✅ PASS |

### C4 — Data Retention Policy Implemented (30-day Recording Deletion)

| Field | Value |
|---|---|
| **Requirement** | NFR-S1 — recordings deleted at 30 days |
| **Evidence** | GCS lifecycle policy active. BUG-D02 surfaced a 14-day misconfiguration (corrected Wednesday to 30 days per NFR-S1 and STT transcripts to 7 days per CISO F5 note). CISO re-audit Thursday confirmed all policies applied correctly. |
| **Verified by** | CISO + Platform Engineer |
| **Date** | Week −1 Thursday |
| **Status** | ✅ PASS |

---

## Business Gates (4)

### B1 — 8 Human Agents Trained on Context Packet + 5 Handoff Scenarios

| Field | Value |
|---|---|
| **Requirement** | All 8 agents certified |
| **Evidence** | Customer Ops certification log: 8 of 8 passed final exam (written + shadow + role-play). 3 warm-transfer dry-runs per agent. Average agent time-to-greet (post-packet-review): 12 sec. |
| **Verified by** | Customer Ops Lead |
| **Date** | Week −1 Thursday |
| **Status** | ✅ PASS |

### B2 — Helpline Number 1800-XXX-XXXX Operational

| Field | Value |
|---|---|
| **Requirement** | Number live, routing to AI agent, IVR functional |
| **Evidence** | Tata Communications delivered `1800-XXX-0100` Week −2 Wednesday. SIPS trunk integrated; IVR < 2 ring pickup confirmed. 47 dogfood calls + 30 internal smoke tests + CTO personal 3-call test Monday morning. |
| **Verified by** | Telecom Ops + CTO |
| **Date** | Week −2 Wed (delivered), Week −1 Fri (final smoke) |
| **Status** | ✅ PASS |

### B3 — 50 Soft-Launch Merchants Selected and Onboarded via CRM

| Field | Value |
|---|---|
| **Requirement** | 50 merchants opted in across 3 pilot cities |
| **Evidence** | `soft-launch-merchant-cohort.md` — 20 Kanpur, 15 Surat, 15 Jaipur. All 50 opted in via signed digital consent. 48 of 50 acknowledged welcome SMS + WA by Friday EOD; remaining 2 confirmed by phone. PM personally spoke to 7 of the Kanpur cohort (exceeds required 5). |
| **Verified by** | Merchant Ops + PM |
| **Date** | Week −1 Friday |
| **Status** | ✅ PASS |

### B4 — Ops Dashboard Live

| Field | Value |
|---|---|
| **Requirement** | Live dashboard with conversion, error, credit attach, call volume |
| **Evidence** | Looker dashboard `travel-voice-ops-pilot` live. Panels: conversion funnel, credit attach rolling 24h, error rate per failure mode, call volume + concurrency, WA delivery %, human handoff rate, NPS rolling 7-day. Refresh: 5-min lag. 20 events from analytics pipeline (P09) feeding BigQuery. |
| **Verified by** | Data Engineering Lead + PM |
| **Date** | Week −2 Friday |
| **Status** | ✅ PASS |

---

## Residual Risks Declared Before Launch

Declared at time of go-live sign-off. Anyone consuming this document after launch should consult these as known limitations, not missed gates.

| # | Risk | Accepted By | Monitoring Plan |
|---|---|---|---|
| R1 | Raw STT transcripts retained 7 days for NLU retrain (F5 from P10) | CTO + CISO | Included in GCS retention audit monthly |
| R2 | Single-region deployment (asia-south1 only); RTO ~4h | CTO + CISO | Acceptable at pilot scale; DR plan for Phase 2 |
| R3 | Sev-2 BUG-D08 (outbound KYC staleness) mitigated not fully fixed | CTO | Pull-fresh on outbound adds 250ms; proper CDC fix in Phase 2 |
| R4 | 7 Sev-3 bugs from dogfood carried to Week 2 deploy | PM | Tracked in Jira epic `TRAVEL-W2`; no merchant-facing impact |

---

## Final Authorization

This document is the launch authorization. Signatures below constitute approval to open 50-merchant soft launch Monday 09:00 IST.

| Role | Name (placeholder) | Signature | Date |
|---|---|---|---|
| **Product GM** | [Name] | ✅ APPROVED | Week −1 Fri 17:30 IST |
| **CTO** | [Name] | ✅ APPROVED | Week −1 Fri 17:00 IST |
| **Engineering Head** | [Name] | ✅ APPROVED | Week −1 Fri 17:15 IST |
| **Finance Lead** | [Name] | ✅ APPROVED | Week −1 Fri 16:45 IST |
| **Risk Lead** | [Name] | ✅ APPROVED | Week −1 Fri 17:00 IST |
| **Legal Counsel** | [Name] | ✅ APPROVED | Week −1 Fri 16:30 IST |
| **CISO** | [Name] | ✅ APPROVED (with R1 note) | Week −1 Fri 17:00 IST |

---

## Post-Launch Re-Verification Triggers

This authorization is scoped to the 500-merchant pilot. Re-verification required if any of:
- Scale beyond 500 active merchants
- New city outside Kanpur/Surat/Jaipur
- New payment method added (UPI, card, EMI)
- New data type collected (passport for international, etc.)
- Any Sev-1 incident during pilot

---

*Owner: CTO · Compiled: Week −1 Friday 17:00 IST · Distribution: Product GM, Eng Head, Finance, Risk, Legal, CISO, Customer Ops, Audit*
