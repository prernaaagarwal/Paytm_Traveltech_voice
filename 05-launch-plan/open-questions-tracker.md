# Open Questions Tracker
**Resolutions, Working Assumptions, and Owners**
*Paytm Merchant Travel Desk | 05-launch-plan*

---

## Purpose

PRD Section 9 opened 6 questions that block Week −2 infrastructure work. This document captures the working assumption for each, the owner, target resolution date, and the fallback if resolution slips. Engineering proceeds on the working assumption; if the final answer differs, the delta is triaged as a scope or timeline change.

---

## Status Legend

| Status | Meaning |
|---|---|
| 🟢 RESOLVED | Confirmed answer; no further action |
| 🟡 WORKING | Provisional assumption in use; final answer pending |
| 🔴 BLOCKED | No assumption possible; must resolve before dependent work starts |

---

## OQ-1: Airline API Partners for Kanpur (KNU) Routes

| Field | Value |
|---|---|
| **Question** | Which airline API partners are confirmed for KNU routes? KNU has limited direct flights. |
| **Owner** | Travel Partnerships |
| **Status** | 🟡 WORKING |
| **Target Resolution** | Week −3 (Thursday) |
| **Working Assumption** | Route mix: KNU-DEL (IndiGo daily direct), KNU-BOM (1-stop via DEL on Air India). Use IndiGo and Air India existing Paytm Travel API inventory. Vistara not on KNU; excluded from ranking for KNU-origin searches. |
| **Dependent Work** | FR-03 flight search route coverage; ranking algorithm (FR-03a) reliability weights |
| **Fallback If Unresolved** | Launch with KNU-DEL only (single direct route); deprioritize KNU-BOM to Week 3; offer "alternate airport" (Lucknow LKO) as Week 2 enhancement |

---

## OQ-2: Credit API Idempotency Window

| Field | Value |
|---|---|
| **Question** | What is the idempotency window for the Credit API? Needed for call-drop + retry design. |
| **Owner** | Credit Engineering |
| **Status** | 🟡 WORKING |
| **Target Resolution** | Week −3 (Wednesday) |
| **Working Assumption** | 15-minute idempotency window on `POST /credit/v1/charge` keyed on `{merchant_id}_{flight_id}_{date}_{timestamp_rounded_60s}`. Matches held-seat window (US-010 AC5). |
| **Dependent Work** | US-010 call-drop recovery (AC5); FR-04 booking confirmation idempotency |
| **Fallback If Unresolved** | Assume 5-min window (conservative); extend manually if merchant reports double-charge in dogfood; instrument `credit_idempotency_rejected` event |

---

## OQ-3: 1800 Number Provisioning Feasibility

| Field | Value |
|---|---|
| **Question** | Is 1800 number provisioning within 6 weeks feasible? Critical path. |
| **Owner** | Telecom Ops |
| **Status** | 🔴 BLOCKED — resolve by EOD Monday |
| **Target Resolution** | Immediately (this week) |
| **Working Assumption** | None — cannot proceed without answer. See `telecom-ops-brief.md` for RFI sent to Tata/Airtel/Jio on Monday. |
| **Dependent Work** | P07 (helpline provisioning), dogfood cannot start without number |
| **Fallback If Unresolved** | Use existing Paytm Customer Care 1800 number with a dedicated IVR branch (menu option "3 for Business Travel") — adds 8 sec to call, but unblocks launch |

---

## OQ-4: WhatsApp Template for PDF Invoice Delivery

| Field | Value |
|---|---|
| **Question** | WA template for invoice delivery — does PDF attachment require a separate template? |
| **Owner** | Platform Engineering |
| **Status** | 🟢 RESOLVED |
| **Resolution** | Yes — Meta requires `document` message type with a separate approved template header. PDF is a content attachment on a pre-approved message template, not a free-form message. Counted in our 8-template pre-launch submission (template #5 in `whatsapp-templates.md`). |
| **Dependent Work** | FR-06 WA integration; US-006 AC1 (10-sec invoice delivery) |

---

## OQ-5: Credit Charge for Pending-PNR Bookings

| Field | Value |
|---|---|
| **Question** | If airline confirms PNR 5 minutes after our booking call, what happens to the credit charge — held, reversed, or captured? |
| **Owner** | Credit + Finance |
| **Status** | 🟡 WORKING |
| **Target Resolution** | Week −3 (Friday) |
| **Working Assumption** | Two-phase: (1) authorize credit line at booking confirm (hold, not capture); (2) capture on airline PNR confirmation webhook; (3) if airline fails within 15 min, release authorization. Merchant sees "Booking pending" on WA until PNR confirmed, then receives invoice + charge. |
| **Dependent Work** | FR-04, FR-05, Stage 4 invoice timing |
| **Fallback If Unresolved** | Synchronous charge model: charge on booking confirm; if airline fails, manual refund via Ops within 24 hours. Adds risk of 0.5–1% refund-pending cases; acceptable for 50-merchant soft launch. |

---

## OQ-6: GSTIN Validation API Rate Limit

| Field | Value |
|---|---|
| **Question** | Can the GSTIN validation API handle 50 concurrent validations? |
| **Owner** | Finance Engineering |
| **Status** | 🟡 WORKING |
| **Target Resolution** | Week −4 (by Wednesday — earliest on the list) |
| **Working Assumption** | Published GSTN rate limit: 100 requests/sec per API key. 50 concurrent calls × 1 GSTIN check each = well within limit. Add 15-min Redis cache on GSTIN validation results (merchant GSTIN rarely changes); cuts effective API load by >95% for repeat callers. |
| **Dependent Work** | FR-05 invoice generation; P05 GST pipeline |
| **Fallback If Unresolved** | If rate-limited in production: queue validation async (30-sec SLA); proceed with booking, flag invoice for async generation (already the FM-14 recovery path). |

---

## Rollup View

| # | Question | Status | Owner | Target |
|---|---|---|---|---|
| OQ-1 | KNU airline partners | 🟡 WORKING | Travel Partnerships | Week −3 Thu |
| OQ-2 | Credit idempotency window | 🟡 WORKING | Credit Engineering | Week −3 Wed |
| OQ-3 | 1800 provisioning feasibility | 🔴 BLOCKED | Telecom Ops | This week |
| OQ-4 | WA invoice template | 🟢 RESOLVED | Platform Engineering | — |
| OQ-5 | Credit hold on pending PNR | 🟡 WORKING | Credit + Finance | Week −3 Fri |
| OQ-6 | GSTIN API rate limit | 🟡 WORKING | Finance Engineering | Week −4 Wed |

**Critical path today:** OQ-3 (Telecom Ops). Every other question has a viable working assumption that unblocks engineering.

---

## Escalation

Any OQ slipping past its target resolution date is escalated to the Program Manager in the daily 9 AM ops standup. If the slip threatens the Week 1 launch date, PM briefs the Product GM within 24 hours with a revised go-live recommendation.

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/05-launch-plan*
