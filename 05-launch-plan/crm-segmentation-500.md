# CRM Segmentation — Full 500-Merchant Pilot
**Segmentation Rules · Staged Rollout · Activation Logic**
*Paytm Merchant Travel Desk | 05-launch-plan | Merchant-Ops-Owned*

---

## Purpose

The Week 1 soft launch uses 50 carefully-chosen merchants. The full pilot scales to 500 across Weeks 2–5. This document defines the selection rules for the remaining 450 merchants, the staged-rollout sequence, and the activation playbook.

The 500 number is the upper limit for the pilot per the 4-week timeline. Scaling beyond 500 requires explicit Go decision at Week 7.

---

## Segmentation Rules for Full 500 Cohort

### Hard Filters (all 500 must meet)

Identical to the 50-merchant soft-launch cohort:

1. Paytm-verified merchant with active KYC
2. GSTIN on file and currently active (not suspended/cancelled)
3. Paytm Business Credit pre-approved with ≥ ₹20,000 limit
4. ≥ 2 business trips (any channel) in previous 12 months
5. No account suspension, credit default, or fraud flag in last 6 months
6. Active WhatsApp verified in last 14 days
7. Opted in via signed digital consent
8. Not a Paytm employee or their family

### Soft Filters (preference)

- Paytm Business app active-user (weekly sessions)
- Phone reachability ≥ 80%
- Hindi OR English spoken fluency
- Not currently in any other Paytm pilot (avoid cross-pilot contamination)

### Intentional Diversity (quota enforcement)

| Dimension | Target Distribution | Enforcement |
|---|---|---|
| **City** | Kanpur 200, Surat 150, Jaipur 150 | Hard quota |
| **Credit usage history** | 60% new-to-travel-credit, 40% returning | Soft quota (±10 pp) |
| **Business type (Kanpur)** | 70% kirana, 15% pharmacy, 10% hardware, 5% other | Soft quota |
| **Business type (Surat)** | 80% textile, 10% small manufacturing, 10% other | Soft quota |
| **Business type (Jaipur)** | 55% handicraft, 25% tourism service, 10% F&B, 10% other | Soft quota |
| **Digital literacy** | 30% high, 50% medium, 20% low | Soft quota |
| **Credit limit band** | ≥20K ≤30K: 35%, 30–50K: 45%, >50K: 20% | Soft quota |

Digital literacy: low-literacy cohort enters only in Weeks 3–4 (after product has stabilized on medium/high).

### Exclusions

- Merchants who declined the Week 1 opt-in (preserve goodwill)
- Merchants in any current customer-support open ticket
- Merchants flagged by Risk as active review

---

## Staged Rollout Sequence

Weeks 2–5 scale up per the 4-week timeline. Each week adds a cohort; no cohort is pulled in before the previous week's exit gate passes.

### Week 2 — Kanpur Expansion (+150 → 200 total Kanpur)

| Field | Value |
|---|---|
| **New merchants** | 150 Kanpur |
| **Onboarding starts** | Monday Week 2 |
| **Prerequisite** | Week 1 exit gate passed (≥25 bookings, ≥15% conversion, zero Sev-1, NPS ≥40) |
| **Daily activation cap** | 30 new merchants/day (volume-control for ops) |
| **Communication** | Welcome SMS Monday; WA reminder Wednesday; no PM personal calls (scale-up phase) |

**Proactive outbound begins Week 2:** 30 calls placed across the Kanpur cohort for seasonal pattern trigger (Trigger 1 only). Requires DND gate live + consent captured.

### Week 3 — Surat Expansion (+150 → 150 total Surat)

| Field | Value |
|---|---|
| **New merchants** | 150 Surat |
| **Onboarding starts** | Monday Week 3 |
| **Prerequisite** | Week 2 exit: Kanpur cohort conversion ≥ 20% |
| **Daily activation cap** | 30/day |

### Week 4 — Jaipur Expansion (+150 → 150 total Jaipur)

| Field | Value |
|---|---|
| **New merchants** | 150 Jaipur |
| **Onboarding starts** | Monday Week 4 |
| **Prerequisite** | Week 3 exit: Surat cohort conversion ≥ 22% AND credit attach ≥ 35% |
| **Daily activation cap** | 30/day |

### Week 5 — Stabilize (no additions)

All 500 active. No new merchants. Data collection for Go/No-Go week 6 analysis.

---

## Activation Workflow (Per Merchant)

```
Day -2: Pulled from segmentation engine into activation queue
Day -1: SMS opt-in request → track response
Day  0: On YES reply, consent form sent via WA
Day  0: Within 1 hour of consent: welcome message + tutorial video + helpline number
Day +1: Reminder WA if no call placed (soft nudge, not pushy)
Day +3: If still no call, log in CRM as "opted-in-unengaged"; do not further nudge in Week 2 onwards
Day +7: If still unengaged, flag for replacement consideration (activate standby merchant)
```

### CRM Status Flow

```
ELIGIBLE → INVITED → OPT_IN_YES → CONSENT_SIGNED → ONBOARDED
                  ↘                                       ↓
                   OPT_IN_NO                        CALLED (active)
                     (no retry 90 days)                  ↓
                                                   COMPLETED_BOOKING
                                                         ↓
                                                   REPEAT_BOOKING
```

### Replacement Policy

If an onboarded merchant does not place any call within 7 days AND has not explicitly declined:
- Week 2–3: flagged but kept in cohort (still counts toward 500)
- Week 4: if still unengaged, standby merchant activated to replace

This keeps the "500 active cohort" honest for metric calculation.

---

## Standby List

| City | Standby count | Trigger to activate |
|---|---|---|
| Kanpur | 40 | Active Kanpur cohort drops below 180 |
| Surat | 30 | Active Surat cohort drops below 135 |
| Jaipur | 30 | Active Jaipur cohort drops below 135 |

Standby merchants meet all the same filters; they are not pre-notified.

---

## Proactive Outbound Cohort (Weeks 2–5)

Not all 500 get proactive calls. The outbound cohort is a subset:

### Selection for Proactive
1. Must be in the active pilot cohort
2. Must have travel pattern signal: ≥ 1 previous trip in the same seasonal window last year
3. Must NOT be on DND (internal or TRAI)
4. Must have responded to at least 1 Paytm WA/SMS in last 30 days (reachability)

Estimated proactive-eligible: ~35% of pilot = ~175 merchants

### Call Volume Pacing
- Week 2: 30 proactive calls (Kanpur only)
- Week 3: 50 proactive calls (Kanpur + Surat)
- Week 4: 70 proactive calls (all 3 cities)
- Week 5: 50 proactive calls (stabilize)

Max 1 proactive call per merchant per 60 days. If declined, auto-DND for 180 days.

---

## Cohort Analytics (Beyond Dashboard)

Per-cohort tracking in BigQuery tables (accessed via Looker):

### `cohort_weekly_rollup`

Every Friday, computed per cohort:
- Active merchants who called at least once (week)
- Active merchants who booked at least once (week)
- Conversion rate by cohort
- Credit attach rate by cohort
- NPS rolling 7-day by cohort
- Repeat booking rate (merchants with ≥ 2 bookings in the pilot)

### `cohort_comparison`

Kanpur vs. Surat vs. Jaipur — surfaces regional differences in:
- Language mix
- Route preferences
- Payment behavior
- Time-of-day calling patterns

If Kanpur cohort behaves meaningfully differently (e.g., credit attach < 20%), PM triggers a mid-pilot investigation before Jaipur cohort adds.

---

## Do-Not-Activate List

Permanent exclusions maintained in CRM:
- Merchants in active credit default (≥ 90 days overdue)
- Merchants with active fraud investigation
- Merchants opted out of all Paytm communications
- Former Paytm employees within 1-year cooldown

Do-Not-Activate list is cross-checked at every cohort pull. Zero tolerance for accidental inclusion.

---

## Merchant Ops SLAs During Pilot

| SLA | Target | Measurement |
|---|---|---|
| Cohort activation pull from segmentation | Every Monday 08:00 IST | Automated |
| Welcome SMS/WA delivery | Within 2 hours of activation pull | Dashboard metric |
| Consent confirmation follow-up (if no response) | Within 24 hours | Automated WA reminder |
| Merchant Ops response to inbound support | < 4 hours Mon-Fri, < 8 hours weekend | CRM ticket timestamps |
| Replacement merchant activation | < 48 hours from decision | Manual + automated |

---

## Week 7 Go-Decision Dependencies

If Week 7 decision is GO to scale, the segmentation engine is extended:
- From 500 merchants → 5,000 in current 3 cities (10× scale)
- From 3 cities → 13 cities (additions: Lucknow, Agra, Indore, Ahmedabad, Pune, Nagpur, Bhopal, Jodhpur, Udaipur, Kota)

Cohort diversity rules remain the same; city-specific filters updated.

If decision is NO-GO or PAUSE, all 500 receive a thank-you SMS + offer to remain in Paytm's general merchant base (no retroactive removal).

---

## Outputs to Week 2–5 Operations

- **Segmentation ruleset** is a deployed query in Paytm CRM data platform (owned by Merchant Ops + Data Eng)
- **Activation queue automation** runs weekly (Monday 07:00 IST)
- **Cohort health monitoring** on dashboard (Panel 6)
- **Do-Not-Activate list** is a CRM source of truth; all new pilots must cross-check

---

*Owner: Merchant Ops Lead · Co-signers: PM, Data Eng Lead · Last updated: Week −1 Friday*
