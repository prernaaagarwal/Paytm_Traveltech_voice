# Proactive Outreach Triggers
**Seasonal Patterns · Credit Drop · Competitor Activity**
*Paytm Merchant Travel Desk | 02-solution-design*

---

## Overview

30% of Paytm Merchant Travel bookings are expected to come from **Paytm calling the merchant** — not the merchant calling Paytm. This is the moat that consumer OTAs can never replicate: using behavioral and financial data to reach out at the exact moment a merchant has travel intent, before that intent leaks to a competitor.

**Why proactive works:**
- Right-time intervention → catches intent early → 40% conversion vs. 15% inbound cold
- Personalized context → "I know you traveled to Delhi last March" → trust, not cold calling
- Credit activation → drives usage of idle credit lines → core business goal

---

## Trigger Architecture

```
Data Sources:
  ┌─────────────────────────────────────────────┐
  │  Booking History DB  │  Credit API          │
  │  Merchant KYC DB     │  (optional) 3rd-party│
  └──────────────┬──────────────────────────────┘
                 │
                 ▼
       [Trigger Evaluation Engine]
       Runs nightly at 2 AM IST
       Outputs: priority queue of merchants to call
                sorted by: intent_confidence × credit_available
                           × days_since_last_booking
                           × time_since_last_contact
                 │
                 ▼
       [Outbound Call Scheduler]
       Respects: DND registry, opt-out list,
                 calling hours (9 AM–7 PM IST),
                 max 1 call per merchant per 30 days
                 │
                 ▼
       [AI Agent makes outbound call]
       Personalized opening based on trigger type
```

---

## Trigger 1: Seasonal Pattern Detection

### What It Is
Merchant has traveled to the same destination during the same month in 2+ consecutive years. Paytm calls ~6 weeks before the expected travel window to offer early booking at lower fares.

### Signal Calculation
```
Conditions:
  - booking_history.trips >= 2
  - route consistency: same origin + same destination in same month (±2 weeks)
  - upcoming_month = historical_travel_month - 6 weeks
  - last_outreach > 60 days ago

Confidence score:
  - 2 consecutive years, same month: 0.6
  - 3 consecutive years, same month: 0.8
  - 2 years + same week of month: 0.85
  - Triggered if confidence >= 0.60
```

### Example Merchants & Triggers

| Merchant | Historical Pattern | Trigger Date | Outreach Message |
|---|---|---|---|
| Ramesh Kumar (Kanpur) | Delhi trip every March (2024, 2025) | Feb 12, 2026 | "Pichle 2 saalon mein March mein Delhi gaye — is saal bhi plan hai? Abhi book karein, sasta milega." |
| Priya Shah (Surat) | Mumbai + Bangalore in Jan, April, July (sourcing seasons) | Dec 20 / Mar 15 / Jun 14 | "Aapki January sourcing trip — fabric market Mumbai ke liye flight book karein?" |
| Rajiv Textile (Surat) | Surat → Delhi every Nov (trade fair) | Oct 8 | "Delhi Trade Fair ka season aa raha hai. IndiGo ke fares abhi ₹3,200 hain — baad mein ₹5,000+ ho sakte hain." |

### Call Script
```
[A] "Namaste {first_name} ji! Paytm Business Travel se call kar raha hoon.
     Pichle saal aap {month} mein {destination} gaye the {purpose_hint}.
     Is saal bhi trip plan hai?
     Abhi book karein toh fare acha milega —
     usually {month} mein ₹{price_estimate} hota hai."

[If YES] → Directly into Stage 2 (search)
[If NO]  → "Koi baat nahi. Zaroorat ho toh 1800-XXX-XXXX pe call kariye."
            [CRM: mark declined, no retry for 45 days]
[If LATER] → "Kab ka soch rahe hain? Main note kar leta hoon."
             [CRM: schedule follow-up for stated date - 7 days]
```

### Personalization Variables
```
{first_name}       → From KYC
{month}            → Historical travel month ("March", "January")
{destination}      → Historical destination city
{purpose_hint}     → Inferred from merchant type:
                      Kirana/FMCG → "supplier meetings ke liye"
                      Textile → "sourcing ke liye"
                      Unknown → "" (omit)
{price_estimate}   → Current fare API estimate for the route
```

---

## Trigger 2: Credit Utilization Drop

### What It Is
Merchant has an active Paytm Business Credit line but hasn't used it in 45+ days. Paytm calls to surface the credit as a travel payment option, activating idle credit and preventing the line from going stale.

### Signal Calculation
```
Conditions:
  - credit_limit >= ₹20,000
  - credit_utilization_last_45_days < 10% of limit
  - credit_status = ACTIVE (not suspended, not defaulted)
  - account_age >= 6 months (trust signal)
  - no recent travel booking in last 60 days

Priority score:
  - credit_limit × (1 - utilization_rate) × days_since_last_use
  - Higher score = call earlier
```

### Why This Works (Business Case)
```
Idle credit line cost to Paytm:
  - Capital allocated but not deployed → opportunity cost
  - Merchant may think "I never use this" → risk of limit reduction request

Value of activation:
  - One booking: ₹4,000-₹8,000 credit used → 12% APR interest revenue
  - Repayment data → better underwriting → potential limit increase
  - Merchant adoption → lifetime travel bookings via Paytm
```

### Call Script
```
[A] "Namaste {first_name} ji! Paytm Business Travel se call.
     Aapke Paytm Business Credit mein ₹{available_credit} available hai —
     koi upcoming business trip plan hai?
     Travel ke liye credit use karein, toh GST invoice bhi automatic milegi."

[If interested in travel] → "Kahan jaana hai? Abhi flight dhoondta hoon..."
                            → Stage 2
[If no travel plans]     → "Koi baat nahi. Credit limit shop supplies ke liye bhi use kar sakte hain."
                            → Pivot to other credit use cases (out of scope for MVP, mention only)
[If "what credit?"]      → "Aapke Paytm Business account mein ₹{limit} ki limit approved hai.
                            Paytm app mein Business Credit section mein dekh sakte hain."
```

### Pilot Scope (MVP)
```
MVP includes: Seasonal pattern detection ONLY
              Credit drop trigger: Post-MVP (requires more careful targeting to avoid spammy feel)
```

---

## Trigger 3: Competitor Activity Detection *(Aspirational — Post-MVP)*

### What It Is
Merchant shows intent to book travel via a competitor (MMT, Cleartrip, MakeMyTrip) based on behavioral signals. Paytm intercepts with a personalized call within 2 hours.

### Data Signals Required
```
Signal A (requires data partnership):
  → Merchant searches "Delhi flight" on Google
  → Google Ads API shows merchant_phone as the cookie-linked identity
  → Cross-reference with Paytm merchant DB

Signal B (Paytm-owned):
  → Merchant opens Paytm app → visits Travel section → exits without booking
  → 30-min delay → trigger call

Signal C (Paytm-owned):
  → Merchant receives Cleartrip promotional SMS (if Paytm has DLT sender data)
  → NOT viable — data partnership would be required and raises privacy concerns
```

### Why This Is Aspirational
- Signal A requires Google ad data cross-referencing → **privacy risk, regulatory scrutiny**
- Signal B is viable but limited scope (only catches Paytm app abandonment, not competitor research)
- Signal C is not viable without specific data partnerships
- GDPR/Indian PDPA compliance must be reviewed before implementation

### MVP Decision: **Do Not Implement Competitor Activity Detection**

The risk/complexity/regulatory overhead outweighs the benefit for an MVP. Signal B (Paytm app abandonment) is the only clean signal and should be treated as a **cart abandonment flow**, not a "competitor detection" trigger.

```
POST-MVP ROADMAP:
  Trigger 3A: Paytm app abandon → call within 30 min
              "Aapne abhi Paytm Travel mein flight dekhe — book kar dein?"
  Trigger 3B: Long-term — explore data clean room with partners
              Subject to legal review
```

---

## Outreach Guardrails

### Calling Hours
```
Allowed window: 9:00 AM – 7:00 PM IST (local time of merchant city)
Block: Sundays 12:00–3:00 PM (shop peak hours)
Block: National holidays
Block: First 2 days of major festivals (Diwali, Eid, Holi, Christmas)
```

### Frequency Limits
```
Max 1 proactive call per merchant per 30 days
Max 2 outreach attempts per trigger event (call + SMS if no pickup)
After 2 declines: 90-day suppression (don't call for 3 months)
Explicit opt-out: Permanent suppression + mark DND in CRM
```

### Consent & DND Compliance
```
At onboarding: Merchant opts into "travel alerts and recommendations" (TPAI consent)
During call: If merchant says "don't call" → CRM mark DND, immediate suppression
DND Registry: Check TRAI DND registry before any outbound call
PDPA: All data used is first-party Paytm data; no third-party data used in MVP
```

### What to Do When Merchant Doesn't Pick Up
```
Attempt 1: Call at trigger time
No answer → Leave voicemail (if supported) + Send SMS:
  "Paytm Business Travel se call tha. Aapke liye {destination} ke flights dekhe hain.
   Book karein ya call kariye: 1800-XXX-XXXX"

Attempt 2: Next day, different time (±3 hours from first attempt)
No answer → Close trigger. No more attempts for this trigger event.

Next trigger: Only after 30-day suppression window expires.
```

---

## Metrics for Proactive Outreach

| Metric | Target | Measurement |
|---|---|---|
| **Proactive call connect rate** | > 60% | Calls answered / Dials made |
| **Proactive → booking conversion** | > 35% | Bookings / Calls with answered intent |
| **Credit attach on proactive calls** | > 50% | Credit bookings / Total proactive bookings |
| **Opt-out rate** | < 5% | DND marks / Total outreach contacts |
| **Voicemail conversion** | > 8% | Callbacks or link taps / Voicemails left |

---

## Trigger Priority Queue (Nightly Batch)

```
Priority 1 (call within 24 hours):
  → Seasonal trigger with confidence >= 0.80 AND travel window < 14 days

Priority 2 (call within 48 hours):
  → Seasonal trigger with confidence >= 0.60 AND travel window < 30 days

Priority 3 (call within 7 days):
  → Credit drop trigger (post-MVP)

De-duplication rule:
  → Merchant can only appear in queue once per 30-day window
  → If multiple triggers fire for same merchant, use highest priority
```

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/02-solution-design*
