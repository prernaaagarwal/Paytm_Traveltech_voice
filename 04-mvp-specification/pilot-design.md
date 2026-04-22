# Pilot Design
**500 Merchants · 3 Cities · 4 Weeks · Selection Criteria · Success Measurement**
*Paytm Merchant Travel Desk | 04-mvp-specification*

---

## Pilot Overview

| Parameter | Value |
|---|---|
| **Total merchants** | 500 |
| **Cities** | Kanpur (200) · Surat (150) · Jaipur (150) |
| **Duration** | 4 weeks (Weeks 1–5 per launch timeline) |
| **Scale-up** | Staged: 50 (Wk 1) → 200 (Wk 2) → 350 (Wk 3) → 500 (Wk 4) |
| **Call types** | Inbound (70%) + Proactive outbound (30%) |
| **Geographic focus** | Tier-2 cities only (intentional — harder environment = better test) |
| **Travel scope** | Domestic flights only; economy; single traveler |
| **Payment scope** | Paytm Business Credit + Paytm Wallet only |

---

## Why These Three Cities

### Kanpur (200 merchants — 40%)
**City profile:** Major FMCG distribution hub; population ~3M; strong kirana/MSME density

- Represents the **Ramesh Kumar persona** at scale — cash-constrained, lower digital literacy, high FMCG supplier travel to Delhi
- Top route: Kanpur → Delhi (frequent, multiple daily flights, well-served by IndiGo/Air India)
- Secondary routes: Kanpur → Mumbai, Kanpur → Lucknow (short-haul)
- Credit profile: Lower credit penetration → tests product with merchants who are being introduced to credit for the first time
- **Pilot learning objective:** Does voice + credit nudge convert a cash-first merchant to credit-first behavior?

**City-specific challenges to watch:**
- Higher ambient shop noise (busier markets, outdoor shops)
- Lower WhatsApp Business adoption (some merchants use personal WhatsApp only)
- Stronger regional Hindi accent (Kanpuri dialect with UP inflections)

### Surat (150 merchants — 30%)
**City profile:** India's largest textile and diamond trading hub; population ~7M; high SMB density

- Represents the **Priya Shah persona** — credit-enabled, high digital literacy, frequent sourcing trips
- Top routes: Surat → Mumbai, Surat → Bangalore (textile sourcing), Surat → Delhi (trade shows)
- Credit profile: Higher credit penetration → tests credit attach with merchants who have credit but haven't used it for travel
- Gujarati-dominant Hindi (most merchants are bilingual Hindi/Gujarati)
- **Pilot learning objective:** Does the credit nudge activate an idle credit line for merchants who have credit but default to wallet?

**City-specific challenges to watch:**
- Gujarati-accented Hindi may cause STT issues (test for WER degradation in this accent)
- High-frequency travelers (6+ trips/year) → repeat booking rate should be measurable within the 4-week window
- Strong comparison behavior (Surat merchants are price-sensitive; will comparison-shop on MMT)

### Jaipur (150 merchants — 30%)
**City profile:** Tourism, handicraft, and gemstone trading hub; population ~3.5M; mixed MSME profile

- Represents a **mixed persona** — both cash-constrained artisan merchants and credit-enabled tourism operators
- Top routes: Jaipur → Delhi, Jaipur → Mumbai, Jaipur → Kolkata (trade fair travel)
- Credit profile: Mixed — some merchants have credit (tourism operators), many don't (artisans)
- More seasonal travel patterns (trade fair calendar: Jan, March, Nov)
- **Pilot learning objective:** Does the product work across a diverse merchant profile (not just one persona)?

**City-specific challenges to watch:**
- Seasonal travel concentration (some merchants may not travel during the 4-week pilot window)
- Rajasthani Hindi dialect inflections
- Tourism merchants travel more internationally (edge case: request for international flights)

---

## Merchant Selection Criteria

### Hard Criteria (Must Meet All)

| Criterion | Threshold | Measurement | Reason |
|---|---|---|---|
| **Travel frequency** | ≥ 2 trips/year (self-reported or booking history) | CRM + merchant interview | Ensures repeat usage during 4-week window |
| **Paytm account tenure** | ≥ 1 year as active merchant | Account creation date | Trust signal; less likely to churn during pilot |
| **GSTIN status** | Active (no suspended/cancelled GSTINs) | Real-time GSTN validation at selection | Avoid invoice generation failures from day 1 |
| **WhatsApp registered** | Phone number registered on WhatsApp | WA number lookup | WA companion is core UX; SMS-only degrades experience |
| **Opt-in consent** | Explicit consent to participate in pilot AND to travel alerts | CRM consent flag | PDPA + TRAI compliance; no forced participation |
| **No active disputes** | No open chargebacks, credit disputes, or legal holds | Credit team check | Risk management |

### Soft Criteria (Preferred, Not Required)

| Criterion | Preference | Reason |
|---|---|---|
| **Credit pre-approval** | ≥ ₹20K credit limit approved | Enables credit attach testing from day 1 |
| **Prior Paytm travel usage** | At least 1 prior booking via Paytm consumer travel | Familiarity with Paytm travel reduces onboarding friction |
| **Smartphone type** | Android preferred | Android WhatsApp has higher Business API compatibility |
| **Business type** | Kirana, textile, handicraft, hardware (high-travel categories) | Higher probability of needing travel during pilot |

### Exclusion Criteria (Automatic Disqualification)

| Criterion | Reason |
|---|---|
| Credit default in past 6 months | Risk management |
| GSTIN mismatch (KYC name ≠ GSTN legal name unresolved) | Would cause invoice failures from first booking |
| Merchant < 1 year on Paytm | Low trust; higher churn risk |
| Prior DND opt-out from Paytm communications | TRAI compliance; can't do proactive calls |
| Merchant account suspended or under review | Can't onboard |

---

## City-Level Cohort Distribution

### Kanpur Cohort: 200 Merchants

| Segment | Count | Primary Route | Credit Profile |
|---|---|---|---|
| Kirana / grocery owners | 80 | Kanpur → Delhi | Low credit (target for first-time activation) |
| Hardware / auto parts | 40 | Kanpur → Delhi, Mumbai | Low-medium credit |
| Medical / pharma distributors | 35 | Kanpur → Delhi, Hyderabad | Medium credit |
| Electronics retail | 25 | Kanpur → Mumbai, Bangalore | Medium credit |
| Other (stationery, textiles, misc.) | 20 | Mixed routes | Mixed credit |

**Kanpur success threshold:** ≥ 20% call-to-booking conversion by end of Week 2 (before Surat adds)

---

### Surat Cohort: 150 Merchants

| Segment | Count | Primary Route | Credit Profile |
|---|---|---|---|
| Fabric / saree wholesale | 55 | Surat → Mumbai, Bangalore | High credit (₹30–₹80K limits) |
| Textile garment manufacturer | 40 | Surat → Delhi, Bangalore | High credit |
| Diamond / gemstone trading | 25 | Surat → Mumbai, Delhi | High credit |
| Chemical / synthetic fabric | 20 | Surat → Mumbai, Chennai | Medium-high credit |
| Other (embroidery, accessories) | 10 | Mixed | Mixed |

**Surat success threshold:** ≥ 45% credit attachment rate (this cohort has credit — tests whether they use it for travel)

---

### Jaipur Cohort: 150 Merchants

| Segment | Count | Primary Route | Credit Profile |
|---|---|---|---|
| Handicraft / pottery exporters | 50 | Jaipur → Delhi, Mumbai | Low-medium credit |
| Tourism services / hotels | 35 | Jaipur → Delhi, Mumbai, Kolkata | Medium-high credit |
| Spice / agricultural trade | 30 | Jaipur → Delhi, Mumbai | Low credit |
| Marble / stone suppliers | 20 | Jaipur → Delhi, Mumbai | Medium credit |
| Jewelry retail | 15 | Jaipur → Delhi, Mumbai, Chennai | High credit |

**Jaipur success threshold:** ≥ 22% conversion (mixed credit profile means lower credit attach; compensated by volume)

---

## Credit-Isolation A/B Arm (Surat, 60 merchants)

### Why This Exists

The core product thesis is **credit activation** — but observed credit attach in an un-isolated pilot can't tell us whether the voice nudge *causes* activation or merely surfaces a pre-existing preference among merchants who already have (and use) Paytm Business Credit. Without isolation, a 40% attach result is ambiguous: it could mean "the product works" or "we recruited credit-friendly merchants." This arm removes that ambiguity for the single most important metric.

### Design

Two arms, stratified-randomized **within the Surat cohort** (150 merchants), drawn from the fabric, garment, and diamond segments so credit availability is roughly matched:

| Arm | n | Stage 3 payment prompt | Everything else |
|---|---|---|---|
| **A — Nudge (control group for the product as pitched)** | 30 | *"Credit se karein ya wallet se?"* (explicit credit-first framing) | Standard 5-stage flow |
| **B — Neutral** | 30 | *"Payment kaise karna hai?"* (merchant volunteers method) | Standard 5-stage flow |

Arms are assigned at onboarding; merchants and human handoff agents are blind to arm. All other Surat merchants (90) remain on the standard flow and are excluded from A/B analysis.

### Primary Measurement

| Metric | Arm A target | Arm B expected | Decision |
|---|---|---|---|
| **Credit attach rate** | ≥ 45% | Unknown (the point of the test) | **See decision rule below** |

### Decision Rule

- **Arm A − Arm B ≥ 10 pp** → Voice nudge causally activates credit. Ship the nudge. Credit thesis validated.
- **Arm A − Arm B between 5–10 pp** → Partial causal effect. Expand to a 200-merchant A/B in Phase 2 scale-up (Weeks 8–12) before claiming credit activation.
- **Arm A − Arm B < 5 pp** → Attach is driven by merchant preference, not the nudge. **Credit-activation thesis does not hold.** Reframe the product narrative (likely toward GST-invoice-as-hero) and rebuild the business case accordingly.

### Why Surat (Not Full-Cohort Randomization)

Credit *availability* varies sharply across the three cities (Kanpur low, Surat high, Jaipur mixed). Randomizing across cities would confound the nudge effect with availability. Running the A/B inside Surat — where nearly all merchants have meaningful credit limits — isolates *willingness to use credit for travel* from *ability to use credit for travel*.

### Power and Caveats

- **n=30/30 is directional, not statistically powered.** At an assumed baseline of 40%, a 10 pp delta is detectable with ~70% power — good enough to make a Phase 2 go/no-go call, not good enough to publish. The memo must report as "directional signal, confirmed in Phase 2 scale-up."
- **No third arm removing the nudge entirely** (e.g., merchant selects payment silently on WA). That is intentionally deferred to Phase 2 — the MVP test is nudge-vs-neutral, not nudge-vs-silent.
- **Confound to monitor:** If Arm B merchants disproportionately ask *"credit chalega?"* unprompted, the "neutral" arm isn't really neutral — it just delays the nudge by one turn. Call reviewer flags any such utterance; if > 20% of Arm B calls contain merchant-initiated credit mention, the test is inconclusive.

### Gate

This A/B reports out at **Week 4 data lock**, and its result is an explicit input to `05-launch-plan/week-7-go-no-go-memo.md`. A <5 pp delta is a **Conditional-NO-GO on the credit-activation positioning** even if other metrics clear their gates.

---

## Pilot Merchant Onboarding Process

### Step 1: CRM Selection (Week −3)
```
Query: SELECT merchants WHERE
  city IN ('Kanpur', 'Surat', 'Jaipur')
  AND account_age_months >= 12
  AND gstin_status = 'ACTIVE'
  AND whatsapp_registered = TRUE
  AND travel_trips_per_year >= 2   [from KYC onboarding questionnaire or booking history]
  AND credit_default_6m = FALSE
  AND dnd_opted_out = FALSE
ORDER BY credit_available DESC, last_booking_date DESC
LIMIT 750   [select 750 to account for 33% opt-out rate → targeting 500 confirmed]
```

### Step 2: Outreach and Opt-In (Week −2)
```
Channel 1: WhatsApp message from Paytm Business account
  Template: "Namaste {name}! Paytm Business Travel launch ho raha hai —
             aapke liye exclusive early access. Flight booking < 3 min,
             GST invoice automatic, Business Credit use kar sakte hain.
             Join karein: [Opt-in link]"

Channel 2: In-app notification (Paytm Business app)
  Same message; tracks opt-in click rate by channel

Channel 3: Merchant Ops field team follow-up (for high-value merchants)
  20 top-priority merchants (high credit, frequent traveler) called personally

Consent recording:
  Opt-in link → consent form captures:
    ✅ Consent to participate in Paytm Business Travel pilot
    ✅ Consent to receive travel alerts and booking assistance calls
    ✅ Consent to voice recording for quality purposes
```

### Step 3: Onboarding Communication (Week −1 to Day 1)
```
T-3 days: Welcome SMS + WA
  "Welcome to Paytm Business Travel pilot! 🛫
   Your helpline: 1800-XXX-XXXX (save it!)
   How it works: [short video link, 45 sec]
   Your credit: ₹{credit_available} ready to use for flights"

T-1 day: Reminder WA
  "Paytm Business Travel starts tomorrow!
   For your first booking, just call: 1800-XXX-XXXX
   Tell the agent where you want to go. That's it."

Day 1: First-booking nudge (for merchants not yet called in Wk 1)
  "Aaj pehli booking try karein — sirf 3 minute! 📞 1800-XXX-XXXX"
```

---

## Pilot Measurement Framework

### Primary Metrics (Measured Weekly)

| Metric | Week 1 Target | Week 2 Target | Week 3 Target | Week 4 Target | Go Threshold |
|---|---|---|---|---|---|
| Call → Booking Conversion | ≥ 15% | ≥ 20% | ≥ 22% | ≥ 25% | ≥ 25% |
| Credit Attachment Rate | ≥ 30% | ≥ 35% | ≥ 38% | ≥ 40% | ≥ 40% |
| NPS | ≥ 35 | ≥ 40 | ≥ 45 | ≥ 50 | ≥ 50 |
| Avg Call Duration | ≤ 4 min | ≤ 3.5 min | ≤ 3.2 min | ≤ 3 min | ≤ 3 min |
| Booking Failure Rate | ≤ 8% | ≤ 6% | ≤ 5% | ≤ 5% | ≤ 5% |

*Week 1 targets are lower — this is a learning week. Go thresholds apply to Week 4 aggregate.*

### Secondary Metrics (Measured Weekly)

| Metric | Target | Measurement |
|---|---|---|
| STT Accuracy (WER) | > 85% | Sample of 50 calls manually reviewed for transcription quality |
| NLU Intent Capture | > 90% | Correct entity extraction in logged events |
| Voice Fallback Rate | < 20% | `voice_fallback_triggered` events / total calls |
| Invoice Delivery < 10 sec | > 95% | `invoice_delivered.delivery_time_ms` < 10,000 |
| Human Handoff Rate | < 15% | `human_handoff_initiated` / `call_initiated` |
| Proactive Call Connect | > 60% | Answered / Dials |
| Proactive → Booking | > 35% | Bookings / Answered proactive calls |
| Repeat Booking (within pilot) | > 8% | Merchants with ≥ 2 bookings in 4 weeks |

### Qualitative Data Collection

**Merchant Interviews (30 total):**
- 10 from Kanpur (5 who booked, 5 who called but didn't book)
- 10 from Surat (5 who used credit, 5 who used wallet)
- 10 from Jaipur (5 who booked, 5 who opted in but never called)

**Interview guide themes:**
1. Why did/didn't you book? What stopped you or helped you?
2. Did the agent understand you clearly? Any moments of confusion?
3. How did the GST invoice compare to your usual process?
4. Did you feel comfortable using Business Credit for a ₹5K+ purchase?
5. Would you recommend this to another merchant? Why/why not?
6. What would make you use this for every trip?

**Human Agent Feedback (weekly):**
- Top 3 most confusing merchant situations
- Most common reason for escalation
- Quality of context packet (does it actually help?)
- Suggestions for script improvements

---

## Pilot Risk Mitigations

| Risk | Mitigation |
|---|---|
| Low merchant activation (< 30% of 500 call in Wk 1) | Personal outreach from PM to 20 high-value merchants; field team follow-up for Kanpur |
| STT degrades badly in Surat due to Gujarati-accented Hindi | Monitor WER by city; if Surat WER < 78%, deploy Gujarati-accent STT model before Wk 3 |
| Kanpur inventory gaps (limited KNU routes) | Pre-audit KNU routes; confirm Amadeus GDS coverage; activate backup aggregator before launch |
| Credit attach below 30% in first 100 bookings | A/B test stronger credit nudge language immediately; PM personally reviews first 20 call recordings |
| Merchant compares prices and drops in Stage 2 | Daily spot-check: Paytm price vs. MMT for top 5 routes; escalate immediately if > 5% gap |
| High proactive call opt-out rate | If > 8% opt-out in first week, pause proactive calling until call quality/script improved |

---

## Pilot Economics (Expected)

```
Assuming 500 merchants active, 4-week pilot:

Call volume estimate:
  500 merchants × 20% call rate in 4 weeks = 100 inbound calls
  + 30 proactive outbound calls
  = ~130 total calls

Booking estimate:
  130 calls × 25% conversion = ~33 bookings

GMV estimate:
  33 × ₹8,000 avg = ₹2.64 L (₹264K)

Credit disbursed:
  33 × 40% credit attach × ₹8,000 = ₹1.06 L

Commission revenue:
  ₹2.64 L × 2% = ₹5,280

Credit interest (30-day):
  ₹1.06 L × 18% APR × 30/365 = ₹1,570

Total pilot revenue: ~₹6,850
Total pilot cost: ~₹40,000 (telephony + ops + infra for 4 weeks)

Pilot is deliberately loss-making — this is validation spend, not revenue generation.
The real ROI is the decision signal, not the 4-week P&L.
```

---

## Pilot Exit: Data Lock and Handoff

**Week 6 data lock checklist:**
```
□ All call logs downloaded and reconciled with booking records
□ 30 merchant interviews completed and transcribed
□ NPS responses downloaded from survey tool
□ STT accuracy audit: 100 calls manually reviewed
□ Credit attach calculation verified against payment logs
□ Human handoff reasons categorized and counted
□ Edge case frequency counts tabulated against EC-01 to EC-23
□ City-level cohort comparison report prepared
□ Conversion funnel final numbers locked
□ Go/No-Go recommendation memo drafted (see 05-launch-plan/go-no-go-criteria.md)
```

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/04-mvp-specification*
