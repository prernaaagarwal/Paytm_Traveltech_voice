# Go / No-Go Criteria
**Success Thresholds · Kill Criteria · Decision Framework**
*Paytm Merchant Travel Desk | 05-launch-plan*

---

## Decision Framework

The Go/No-Go decision is made at the end of **Week 7**, based on 4 weeks of pilot data (Weeks 1–5) and the Week 6 analysis sprint. It is a structured decision — not a gut call.

**Decision inputs:**
1. Quantitative metrics vs. thresholds (below)
2. Qualitative merchant interview synthesis (30 interviews)
3. Engineering reliability report
4. Unit economics validation
5. Risk register status

**Decision output:** One of: GO · GO WITH CONDITIONS · PAUSE · NO-GO

---

## Metric Tiers

### Tier 1 — Primary Metrics (Must Hit All for GO)

These are the metrics that directly validate the core product hypothesis: *Does voice + auto-GST + embedded credit bundling convert merchants faster than the status quo?*

| Metric | Go Threshold | Caution Zone | Kill Criterion | Measurement |
|---|---|---|---|---|
| **Call → Booking Conversion** | ≥ 25% | 18–24% | < 15% | Bookings ÷ Calls where intent captured |
| **Credit Attachment Rate** | ≥ 40% | 30–39% | < 25% | Credit bookings ÷ Total bookings |
| **Net Promoter Score (NPS)** | ≥ 50 | 35–49 | < 30 | Post-booking survey, 1–10 scale |
| **Avg Call Duration** | ≤ 3 min | 3–4 min | > 5 min | Median duration for completed bookings |
| **Booking Failure Rate** | ≤ 5% | 5–8% | > 10% | Failed payments + API errors ÷ Attempts |

**Scoring rule:** To reach GO, all 5 Tier-1 metrics must be at or above their Go threshold. Missing even one sends the decision to GO WITH CONDITIONS (if Caution Zone) or NO-GO (if Kill Criterion).

---

### Tier 2 — Secondary Metrics (Inform Conditions and Scale Decisions)

These don't block GO but influence how fast and how wide we scale.

| Metric | Strong | Acceptable | Weak (Scale Slowly) |
|---|---|---|---|
| **STT Accuracy (WER)** | > 88% | 85–88% | < 85% |
| **NLU Intent Capture** | > 92% | 88–92% | < 88% |
| **Voice Fallback Rate** | < 15% | 15–22% | > 22% |
| **Proactive Call Conversion** | > 40% | 30–40% | < 30% |
| **Repeat Booking Rate (90-day)** | > 15% | 8–15% | < 8% |
| **Invoice Delivery Success** | > 99% | 97–99% | < 97% |
| **WA Companion Engagement** | > 70% open | 50–70% | < 50% |
| **Human Handoff Rate** | < 10% | 10–18% | > 18% |

---

### Tier 3 — Business Impact Metrics (Validate Long-Term Thesis)

Projected at pilot scale; extrapolated to Year 1 in business case.

| Metric | Pilot Target (500 merchants, 4 weeks) | Year 1 Extrapolation (if GO) |
|---|---|---|
| **Pilot GMV** | ₹1.2 Cr+ (500 × 3 trips × ₹8K avg) | ₹1,440 Cr |
| **Credit Disbursed** | ₹48 L+ (40% attach × ₹8K avg × 1,500 bookings) | ₹432 Cr |
| **New Credit Users** | 200+ (merchants using credit for first time) | 600K |
| **Booking Commission** | ₹2.4 L (2% × ₹1.2 Cr GMV) | ₹29 Cr |
| **Zero CAC** | Confirmed (no paid acquisition) | Maintained |

---

## Decision Matrix

```
             TIER-1 METRICS
             All 5 ≥ GO   1–2 Caution   Any Kill
             threshold    Zone only     Criterion
           ┌────────────┬─────────────┬──────────────┐
  TIER-2   │            │             │              │
  All      │            │  GO WITH    │              │
  Strong   │    GO ✅   │  CONDITIONS │   NO-GO ❌   │
           │            │    🟡       │              │
  TIER-2   │            │             │              │
  Mixed    │    GO ✅   │  GO WITH    │   NO-GO ❌   │
           │ (note gaps)│  CONDITIONS │              │
           │            │             │              │
  TIER-2   │            │             │              │
  Mostly   │  GO WITH   │   PAUSE ⏸  │   NO-GO ❌   │
  Weak     │ CONDITIONS │             │              │
           └────────────┴─────────────┴──────────────┘
```

---

## Kill Criteria (Immediate NO-GO, No Override)

These trigger an automatic NO-GO regardless of any other metric:

| # | Kill Criterion | Why Non-Negotiable |
|---|---|---|
| K1 | Call → Booking conversion < 15% for 2 consecutive weeks | Core value proposition is not working |
| K2 | Booking Failure Rate > 10% sustained over 1 week | Trust destroyed; merchants will not return |
| K3 | Credit Attachment Rate < 25% (3-week rolling average) | The business model doesn't work without credit revenue |
| K4 | Any confirmed data privacy breach (PII exposure) | Legal/regulatory; PDPA violation; trust collapse |
| K5 | > 3 confirmed cases of incorrect charges (wrong amount debited) | Financial harm to merchants; legal liability |
| K6 | NPS < 30 for 2 consecutive weeks | Merchant experience is fundamentally broken |
| K7 | Any regulatory action (RBI, TRAI, GSTN) related to product | Compliance violation; must halt immediately |

**K4, K5, K7 trigger immediate pause** (same day) — don't wait for the Week 7 decision.

---

## GO WITH CONDITIONS — Specific Condition Templates

When metrics are in Caution Zone, the GO WITH CONDITIONS path requires named, owned fixes:

| Gap | Condition to Scale | Fix Owner | Timeline |
|---|---|---|---|
| NPS 35–49 | Fix top 3 NPS drivers from merchant interviews before next cohort | Product | 3 weeks |
| Conversion 18–24% | Root cause analysis + 2 specific flow improvements | Product + Eng | 4 weeks |
| Credit attach 30–39% | A/B test stronger credit nudge language in Stage 3 | Product | 2 weeks |
| STT WER < 85% | Noise-specific fine-tuning for kirana shop environments | ML | 6 weeks |
| Voice fallback > 22% | Investigate top 3 failure utterances; WA form redesign | Product + Eng | 3 weeks |
| Invoice delivery < 97% | PDF generation reliability fix | Engineering | 1 week |

---

## Minimum Sample Size Requirements

For the data to be statistically valid, the pilot must achieve:

| Metric | Minimum Sample | Confidence Level |
|---|---|---|
| Conversion rate | ≥ 400 calls with captured intent | 95%, ±4% margin |
| Credit attach rate | ≥ 300 completed bookings | 95%, ±5% margin |
| NPS | ≥ 150 survey responses | 95%, ±6% margin |
| STT accuracy | ≥ 500 utterances with ground truth | 95%, ±3% |
| Human handoff rate | ≥ 400 calls total | 95%, ±4% |

**If sample sizes are not reached by end of Week 5**, extend the pilot by 1 week before making the Go/No-Go call. Do not decide on insufficient data.

---

## City-Level Cohort Analysis

The Go/No-Go is made on the aggregate, but city-level variance reveals important signals:

| City | Primary User Profile | Watch For |
|---|---|---|
| **Kanpur** | Cash-constrained kirana owners, lower digital literacy | STT noise issues, WA engagement, lower credit attach |
| **Surat** | Credit-enabled textile merchants, high digital literacy | Credit attach rate, proactive call conversion |
| **Jaipur** | Tourism/handicraft, mixed digital profile | Route diversity, seasonal patterns |

**If one city underperforms vs. others by > 10 percentage points on conversion:**
→ Do not use it as a kill criterion
→ Investigate city-specific root cause
→ Consider city as separate cohort in Go/No-Go decision

---

## Pre-Decision Checklist (Week 6, before Decision Meeting)

```
Data Quality:
  □ All 20 analytics events confirmed logging correctly
  □ No known data gaps in booking or call logs
  □ Survey responses cross-validated against booking IDs

Qualitative:
  □ 30 merchant interviews completed and synthesized
  □ Human agent feedback collected (handoff quality, common complaints)
  □ CA feedback on invoice quality documented

Sample Size:
  □ ≥ 400 calls with intent captured
  □ ≥ 300 completed bookings
  □ ≥ 150 NPS responses

Engineering:
  □ All Sev-1 incidents documented with post-mortems
  □ Reliability report: uptime %, mean time to recovery
  □ No open Sev-1 bugs at time of decision

Financial:
  □ Unit economics validated: revenue per booking, credit interest
  □ No unexpected regulatory or compliance flags

Risk Register:
  □ All Tier-1 risks assessed against actual events
  □ Any new risks identified during pilot documented
```

---

## Communication Plan by Decision Outcome

### If GO:
- Internal announcement: same day as decision
- Merchant comms: "You're among the first to use Paytm Business Travel — we're expanding!"
- Press: Optional, subject to comms team discretion
- Roadmap: Publish 90-day scale plan internally

### If NO-GO:
- Internal: Decision memo + learnings distributed to all stakeholders
- Merchants: Thank-you + ₹500 Paytm credit as goodwill gesture
- Learnings: Published as internal case study; feeds into next product cycle
- No external announcement

### If PAUSE:
- 60-day silence on external comms
- Fix-and-re-pilot plan communicated internally with target date
- Merchants kept active (they can still use the product during pause)

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/05-launch-plan*
