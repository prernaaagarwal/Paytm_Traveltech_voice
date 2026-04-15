# Post-MVP Roadmap
**Trains · Hotels · International · Enterprise · AI Upgrades**
*Paytm Merchant Travel Desk | 06-impact-projection*

---

## Roadmap Philosophy

> **The MVP proves the wedge. The roadmap builds the fortress.**

Each post-MVP feature is evaluated on three criteria:
1. **Merchant JTBD served** — does this solve a real additional pain?
2. **Credit activation** — does this generate more credit utilization?
3. **Moat deepening** — does this make the product harder for competitors to replicate?

Features that don't satisfy at least 2 of 3 criteria go to the backlog.

---

## Roadmap Overview

```
                    MVP             PHASE 2          PHASE 3         PHASE 4
                    (Months 1–4)    (Months 5–12)    (Months 13–24)  (Months 25–36)
                    ─────────────   ─────────────    ─────────────   ─────────────
TRAVEL INVENTORY    Domestic        + Train          + Hotel         + International
                    Flights         Booking          Booking         Flights

PAYMENT             Credit +        + EMI options    + Corporate     + Multi-currency
                    Wallet          + Split pay      cards (SME)     FX credit

VOICE / AI          Hindi +         + 4 more         + Proactive     + AI travel
                    English         languages        itinerary       agent (LLM)
                    Code-switch     (Guj, Mar,       planning
                                    Tamil, Telugu)

GST / COMPLIANCE    Invoice PDF     + Expense        + GST return    + Automated
                    Auto-gen        report           filing assist   tax filing

MERCHANT TOOLS      —               + Travel policy  + Approval      + Company
                    —               for 2-person     workflows       travel dashboard
                    —               businesses

GEOGRAPHY           3 cities        50 cities        150 cities      All India +
                    (pilot)         (domestic)       (full domestic) Intl. routes
```

---

## Phase 2: Months 5–12 (Post-GO Scale)

### Feature 2.1: Train Booking
**Priority: HIGH** | **JTBD: Speed + GST for train travel** | **Credit: Medium** | **Moat: Medium**

```
Why trains matter:
  - 8 billion rail journeys/year in India (vs. 160M air)
  - Business-class merchants (Kanpur, Surat, Jaipur) use trains for
    medium-distance routes (3–8 hr trips, ₹500–₹2,000)
  - IRCTC integration is complex but Paytm already has IRCTC API access
    from existing Paytm Travel consumer product

What changes in the call flow:
  - Stage 2: NLU must distinguish "flight" vs "train" intent
  - Stage 2: IRCTC API has different search parameters (class: SL/3A/2A/1A, quota)
  - Stage 3: Payment via credit — avg ticket ₹1,200; less credit value
  - Stage 4: GST invoice format differs slightly for rail (HSN: 996411 same, but
             operator is IRCTC not airline)

What doesn't change:
  - 5-stage call flow structure
  - WhatsApp companion
  - GST invoice generation
  - Human handoff logic

New NLU intents required:
  - BOOK_TRAIN (new)
  - SELECT_CLASS (SL/3A/2A/1A/CC/EC)
  - TATKAL_REQUEST (premium booking for same-day travel)
  - WAITLIST_ACCEPT (IRCTC-specific: accept waitlisted ticket)

Revenue impact:
  - Lower GMV per booking (₹1,500 vs ₹8,000 for flights)
  - Commission: ₹15–30 per ticket (vs ₹160 for flights)
  - BUT: 3× higher volume (merchants take trains more often)
  - Credit attach: Lower — most merchants pay cash for train tickets
  - Net: Adds ~15% GMV, ~8% revenue. Strategic value: loyalty + retention.

Build estimate: 6–8 weeks (IRCTC API integration + NLU expansion)
```

---

### Feature 2.2: 4 New Languages (Gujarati, Marathi, Tamil, Telugu)
**Priority: HIGH** | **JTBD: Language accessibility** | **Credit: High (new segment unlock)** | **Moat: Medium**

```
Why language expansion matters:
  - Surat pilot: 30% of merchants speak Gujarati-dominant Hinglish
  - Post-scale cities: Pune (Marathi), Chennai (Tamil), Hyderabad (Telugu)
  - Language is not just UX — it's a trust signal:
    "Paytm speaks my language → Paytm understands my business"

Technical changes:
  - STT: Add hi-IN-enhanced + gu-IN + mr-IN + ta-IN + te-IN language models
  - NLU: Fine-tune on 500 examples per language (2,000 new training examples)
  - TTS: Google Neural2 voices for each language (available)
  - Dialogue: Language detection on first utterance → maintain language throughout

New phonetic variants to add:
  Gujarati: "Mambai" (Mumbai), "Amravati" (Amravati), "Surati" price inflections
  Marathi:  "Punyala jaायचंय" (want to go to Pune), "udya" (tomorrow)
  Tamil:    "Chennai ku" (to Chennai), "naalaiku" (tomorrow)
  Telugu:   "Hyderabadki" (to Hyderabad), "repu" (tomorrow)

Expected impact: +25% conversion in Gujarati/Marathi regions (language is conversion driver)
Build estimate: 8–10 weeks per language (parallelizable across teams)
```

---

### Feature 2.3: EMI on Travel Credit
**Priority: MEDIUM** | **JTBD: Cash flow for high-value trips** | **Credit: Very High** | **Moat: High**

```
Current state:
  Travel credit = 30-day repayment cycle (pay in full)
  Limits access for ₹10K+ bookings (multi-city, business class)

With EMI:
  Merchant books ₹15,000 multi-city trip
  Agent: "₹15,000 — 3 EMI mein kar sakte hain: ₹5,200/month.
          Credit mein apply karein?"
  → Merchant books trips they wouldn't otherwise book
  → Higher GMV per booking
  → Higher credit disbursed → higher interest income

EMI product design:
  Tenors: 3, 6, 9 months
  Interest: 15–20% APR on EMI (vs. 18% revolving)
  Eligibility: Merchants with > 6 months repayment history on Paytm credit
  Underwriting: Travel EMI score = repayment history × booking frequency × credit limit used

Revenue impact:
  Avg booking value increases: ₹8,000 → ₹11,000 (merchants book upgrades)
  Credit disbursed per booking: +37%
  Interest income: significantly higher (6-month EMI at 18% APR > 30-day revolving)

Build estimate: 10–14 weeks (credit product design + risk framework + call flow changes)
```

---

### Feature 2.4: Expense Report Export (Tally / Zoho / Excel)
**Priority: MEDIUM** | **JTBD: Post-trip accounting** | **Credit: Low** | **Moat: Medium**

```
Current pain:
  Merchants get GST invoice PDF on WhatsApp
  CA still manually re-enters data into Tally / Zoho Books / Excel
  → friction persists at the accounting layer

Solution:
  After booking, WA card offers: "📊 Send to your accounting software?"
  Options: [Tally XML] [Zoho Books] [Excel CSV] [Email to CA]

  Tally: Generate Tally XML voucher with correct HSN, GST, ledger structure
  Zoho:  API integration → auto-create expense entry
  Excel: CSV with standard columns (Date, Amount, GSTIN, HSN, Purpose)
  Email: PDF + machine-readable CSV to CA's email (from KYC)

Why this matters:
  - Removes last manual step in the merchant's workflow
  - Priya's JTBD fully solved: "My CA gets the file automatically"
  - NPS driver: post-booking friction is the #2 complaint after GST errors

Build estimate: 6–8 weeks (Tally XML spec is complex; Zoho API is simple)
```

---

## Phase 3: Months 13–24

### Feature 3.1: Hotel Booking
**Priority: HIGH** | **JTBD: Complete trip booking** | **Credit: High** | **Moat: High**

```
Why hotels in Phase 3 (not Phase 2):
  - Hotel NLU is significantly more complex (star rating, locality, amenities)
  - Hotel booking call adds 90+ sec to call time (violates 3-min constraint)
  - Design decision: separate voice interaction for hotel after flight confirmed

Hotel booking flow design:
  [After flight booking confirmation]
  Agent: "Flight book ho gayi. Delhi mein hotel bhi chahiye? 1–2 raat?"
  → If yes: separate 2-minute hotel booking call (or WA-native booking for MVP)
  → WA card: location preference + star rating + price range → confirm

  Hotel search: OYO Rooms, Treebo, Fab Hotels API (budget-focused for SMB merchants)
  GST: Hotel GST rate 18% (> ₹7,500/night) or 12% (₹2,500–₹7,500) — auto-calculated
  Invoice: Hotel GST invoice auto-generated + bundled with flight invoice in WA

Revenue impact:
  Hotels add ~60% more GMV per trip (₹4,000–₹8,000 per hotel booking)
  GMV per merchant/trip: ₹8,000 → ₹13,000 (flight + hotel bundle)
  Commission: 8–12% on hotels (vs. 2% on flights) → massive revenue upgrade
  Hotels drive Year 2–3 revenue acceleration significantly

Build estimate: 14–18 weeks (hotel API + NLU + pricing complexity)
```

---

### Feature 3.2: Proactive Itinerary Planning (AI Agent Upgrade)
**Priority: MEDIUM** | **JTBD: Anticipation + planning** | **Credit: Medium** | **Moat: High**

```
Current proactive:
  Paytm calls merchant based on seasonal patterns
  Agent: "Going to Delhi again this March?"

Enhanced proactive (AI agent upgrade):
  Agent synthesizes: booking history + supplier meeting patterns + credit utilization
  → Generates multi-stop itinerary suggestion

  Example:
  Agent: "Priya ji, March mein fabric sourcing trip ka time hai.
          Aap usually Mumbai aur Bangalore dono jaati hain us mahine.
          Dono ek trip mein book karein? Mumbai + Bangalore 4-din trip:
          IndiGo Mumbai + SpiceJet Bangalore return = ₹11,200 total.
          Abhi book karein — fares March mein ₹15K+ ho sakte hain."

  → Merchant says yes → multi-city booking executed in one call

Technology:
  LLM-based travel planning (GPT-4 or Claude fine-tuned on merchant travel patterns)
  Input: 12 months booking history + seasonal calendar + credit availability
  Output: Personalized trip suggestion with flight options + cost estimate

Revenue impact:
  Multi-city trips: 2× GMV of single-city (₹16,000 vs ₹8,000)
  Proactive multi-city booking conversion: estimated 25–35% vs 15% for standard inbound

Build estimate: 16–20 weeks (LLM integration + personalization engine + multi-city booking API)
```

---

### Feature 3.3: International Flights
**Priority: MEDIUM** | **JTBD: Cross-border sourcing trips** | **Credit: Very High** | **Moat: Medium**

```
Target segment:
  Surat textile merchants: Dubai, Guangzhou, Bangkok sourcing trips
  Jaipur handicraft exporters: Frankfurt, London trade fairs
  Mumbai wholesalers: Singapore, UAE business meetings

Why international is Phase 3 (not earlier):
  - Passport validation required (collect passport number — new PII handling)
  - Forex credit: requires Paytm to enable international credit card use
    or partner with forex card provider
  - GST for international: zero-rated (no GST on international air) → simpler invoice
    but currency conversion complicates invoice amount
  - GSTN compliance: LUT (Letter of Undertaking) required for export service providers
  - NLU: Much larger airport and city name vocabulary

Revenue impact:
  International avg booking: ₹35,000–₹80,000 (vs. ₹8,000 domestic)
  Commission: 2–3% on international (higher than domestic due to GDS fees)
  Credit: High — merchants need large credit lines for international travel
  Year 3 contribution if successful: ₹800–₹1,200 Cr additional GMV

Build estimate: 20–28 weeks (passport handling + forex credit + international GDS)
```

---

## Phase 4: Months 25–36

### Feature 4.1: Company Travel Dashboard (Enterprise Lite)
**Priority: LOW-MEDIUM** | **JTBD: Multi-employee SME management** | **Credit: High** | **Moat: Medium**

```
Target segment:
  SMEs with 5–50 employees (beyond solo merchant)
  Owner books for employees; needs consolidated view + policy

Features:
  - Multi-traveler booking (voice: "Book for Ravi Kumar my employee, same route")
  - Travel policy: owner sets per-trip budget cap (₹8,000 max)
  - Approval workflow: employee requests → owner approves via WhatsApp
  - Consolidated GST invoice: all employees in one monthly invoice
  - Expense dashboard: who traveled where, total spend, credit used

Why Phase 4:
  Adds significant product complexity (multi-user auth, approval chains)
  MVP must prove value for solo merchants first
  Premature complexity kills the 3-minute constraint

Revenue impact:
  SME accounts: 5× higher GMV per account (5+ travelers)
  Annual travel spend per SME: ₹1.5–₹4L vs ₹24K for solo merchant
  Potential to serve 500K SMEs (10M employee-trips/year) = additional ₹8,000 Cr GMV
```

---

### Feature 4.2: AI-Powered Voice Upgrade (LLM-Native Agent)
**Priority: HIGH by Year 3** | **JTBD: Richer conversation, fewer fallbacks** | **Moat: High**

```
Current architecture:
  BERT-based NLU (intent classification + entity extraction)
  Custom FSM dialogue manager
  Google TTS/STT
  
  Limitation: Cannot handle open-ended conversation
              Cannot reason about complex multi-stop itineraries
              Cannot explain travel options in natural language

LLM-native upgrade:
  Replace BERT + FSM with: GPT-4/Claude fine-tuned on travel domain
  + Retrieval-augmented generation (RAG) on flight inventory, pricing
  
  New capabilities:
    "Jo sabse cheap ho aur subah ki ho, woh book karo"
    (Book whichever is cheapest and in the morning)
    → LLM reasons about constraints + selects from inventory
    
    "Last time maine jo hotel liya tha, wahi chahiye"
    (I want the same hotel as last time)
    → LLM retrieves booking history + suggests same property
    
    "Mumbai trip ke liye budget ₹12,000 hai — flight + hotel ho sakta hai?"
    (I have ₹12,000 budget for Mumbai — can I do flight + hotel?)
    → LLM plans: ₹4,500 flight + ₹6,500 hotel + ₹1,000 buffer

  Fallback rate improvement: From 20% → 8% (LLM handles more edge cases natively)
  NPS improvement: +15 points (conversation feels more natural)

Build estimate: 20–30 weeks (LLM evaluation, fine-tuning, safety testing, cost optimization)
Cost consideration: LLM inference is 10–15× more expensive than BERT per call.
                    Must be economically justified by conversion improvement.
                    Break-even analysis: +3% conversion improvement covers cost at scale.
```

---

## Feature Prioritization Matrix

```
                        HIGH CREDIT IMPACT
                               │
          Hotels (Ph3)         │    EMI (Ph2)
          Intl Flights (Ph3)   │    Languages (Ph2)
                               │    Proactive LLM (Ph3)
  LOW MOAT ────────────────────┼─────────────────────── HIGH MOAT
                               │
          Train (Ph2)          │    Enterprise Dashboard (Ph4)
          Expense Export (Ph2) │    LLM Upgrade (Ph4)
                               │
                        LOW CREDIT IMPACT

Priority order:
  1. Languages + Train (Ph2)   — Fast, high reach, language = conversion driver
  2. EMI (Ph2)                 — High credit impact, direct revenue
  3. Hotels (Ph3)              — Highest revenue per booking, high moat
  4. Intl Flights (Ph3)        — High GMV, complex build
  5. Proactive LLM (Ph3)       — High moat, medium credit
  6. LLM Upgrade (Ph4)         — High moat, complex, expensive
  7. Enterprise Dashboard (Ph4)— High GMV, long sales cycle
```

---

## What We Deliberately Do NOT Build

| Feature | Why Not |
|---|---|
| **Leisure travel** | Dilutes SMB focus; competes with MMT directly (where they have inventory + brand advantage) |
| **Cab booking (Ola/Uber)** | Margins too low; no GST advantage; UX too different from flight booking |
| **Travel insurance** | Post-MVP insurance is better sold as a standalone product post-booking via WA upsell (not in the booking call) |
| **Social / group trip planning** | Merchants travel for business; group leisure = different persona |
| **Consumer travel** | Paytm consumer travel already exists. This product is merchant-only. Cannibalizing is bad strategy. |
| **Self-serve web portal** | Defeats voice-first design philosophy. WA is the screen. Web portal = another product to maintain. |

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/06-impact-projection*
