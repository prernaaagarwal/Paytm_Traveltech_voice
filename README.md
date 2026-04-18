# Paytm Merchant Travel Desk: Voice-First Travel Concierge for 30M+ SMB Merchants

> **AI Product Manager Case Study** | Voice Agent + Embedded Fintech | B2B2C Product Design

[![Product Type](https://img.shields.io/badge/Product-Voice%20AI%20%2B%20Fintech-blue)]()
[![Stage](https://img.shields.io/badge/Stage-0→1%20MVP-green)]()
[![Domain](https://img.shields.io/badge/Domain-Travel%20%2B%20Credit-orange)]()

---

## 📋 Table of Contents
- [Executive Summary](#executive-summary)
- [The Problem](#the-problem)
- [The Solution](#the-solution)
- [Why Paytm Wins](#why-paytm-wins)
- [How It Works](#how-it-works)
- [MVP Scope](#mvp-scope)
- [Success Metrics](#success-metrics)
- [AI Architecture](#ai-architecture)
- [Projected Impact](#projected-impact)
- [Portfolio Navigation](#portfolio-navigation)
- [About This Case Study](#about-this-case-study)

---

## 🎯 Executive Summary

### The 30-Second Pitch

**Problem:**  
Paytm's 30M merchants lose 15+ minutes per business trip juggling OTAs, GST invoicing, credit applications, and expense filing across 4 disconnected tools. **Every corporate travel platform targets large enterprises** — nobody serves the 3M SMB merchants who travel regularly.

**Solution:**  
A **voice-first travel helpline** where merchants call (or get called proactively), book flights/trains in under 3 minutes, auto-receive GST invoices tied to their GSTIN, and pay via Paytm Business Credit with zero upfront cash. **Phone number = authentication** (no login). **WhatsApp = companion screen** (no app download).

**Why Paytm Can Win:**  
Only company with **merchant KYC + credit rail + GST infrastructure + travel inventory** already operational. Competitors would need to build a payments company from scratch to replicate this stack.

**MVP:**  
500 merchants × 3 cities (Kanpur, Surat, Jaipur) × 4 weeks. Domestic flights only. Inbound + proactive calls.

**Key Metrics:**
- **Primary:** 40% of bookings via Paytm Business Credit (credit activation wedge)
- **Secondary:** 25% call→booking conversion, <3 min avg call time, 50+ NPS

**Projected Impact (Year 1):**  
1.8M bookings × ₹8K avg = **₹1,440 Cr GMV** (~$175M USD). **₹33 Cr revenue** (commission + credit interest). **600K new active credit users** with zero customer acquisition cost.

---

## 🔴 The Problem

### The SMB Travel Gap: 3 Million Underserved Merchants

#### Who Are We Serving?

### Persona 1: Ramesh Kumar — Kirana Owner, Kanpur
- Age 42, runs 2 grocery stores, ₹15L annual revenue
- Travels 3×/year to Delhi for FMCG supplier meetings
- **Current pain:** Books on MMT (20 min), manually creates GST invoice, arranges cash advance
- **Quote:** *"Half the time I forget the receipt and lose the tax deduction"*
- **Tech comfort:** Low (uses WhatsApp, struggles with apps)
- **JTBD:** Book trip + get GST invoice in <5 min without juggling tools

### Persona 2: Priya Shah — Boutique Owner, Surat
- Age 35, textile boutique, ₹40L revenue
- Travels 6×/year to Mumbai/Bangalore for fabric sourcing
- Has Paytm Business Credit (₹50K limit), active WhatsApp user
- **Current pain:** Books on Cleartrip, manually types invoice into Excel for CA
- **Quote:** *"I make mistakes typing GSTIN, then my CA calls me to fix it"*
- **JTBD:** One-call booking with auto-correct GST invoice

#### #### Quantified Pain Points (Synthesised from public sources + hypothetical primary research)
> *Methodology note: Pain point estimates below are extrapolated from NASSCOM SMB digitisation reports, Paytm investor presentations (FY24), and hypothetical interviews modelled on semi-urban merchant archetypes in Tier-2 cities. Figures are directional, not statistically validated.*)

| Pain Point | Current State | Impact |
|------------|---------------|---------|
| **Time waste** | 17 min avg (OTA search → booking → GST invoice → credit arrangement) | 30% abandon trips due to friction |
| **GST errors** | 40% file incorrect invoices (wrong HSN, missing GSTIN) | Lose tax deduction, CA rework |
| **Credit access** | 60% use personal savings (no business credit) | Cash flow strain, miss growth opportunities |
| **Tool fragmentation** | 3.2 tools on average (MMT + Excel + bank app) | Cognitive overhead, error-prone |
| **Post-trip hassle** | 50% never file expenses properly | Money left on table |

#### Why No One Else Solves This

**Enterprise Travel Platforms** (MMT for Business, Cleartrip Corporate):
- Target: Companies with 50+ employees, IT teams
- Minimum contract: ₹10L/year + SAML SSO integration
- **Miss SMBs entirely** — a kirana owner can't sign enterprise contracts

**Consumer OTAs** (MakeMyTrip, Goibibo, Cleartrip):
- No GST invoice auto-generation (merchants screenshot and manually edit)
- No business credit integration  
- Treat merchants like leisure travelers

**Result:** 3M traveling merchants = **underserved greenfield** [**Confidence: High**]

---

## ✅ The Solution

### Voice-First Travel Concierge: Call, Book, Get Invoice, Pay via Credit — All in 3 Minutes

#### Core Design Principle
> **"Voice for speed, screen for verification"** — Voice drives the conversation, WhatsApp companion screen shows options when visual comparison matters.

#### What Happens on a Call

**The merchant calls** `1800-XXX-XXXX` **or gets a proactive call.**  

The AI agent — **knowing who they are from KYC** — greets by name, asks where they need to go and when, searches available flights/trains in real-time, presents 2-3 options by voice, confirms the booking, **auto-generates a GST invoice** tied to their GSTIN, and offers to charge it to their **Paytm Business Credit line** so there's no upfront cash needed.

**Post-trip,** the same agent can be called to close the expense.

**The whole booking flow takes under 3 minutes.**

---

## 🏆 Why Paytm Wins

### The Only Player with All Four Pieces of the Stack

#### Paytm's Unique Assets

1. **30M verified merchant relationships** — KYC complete, GSTIN on file, phone number linked
2. **Paytm Business Credit** — ₹5K-₹50K credit lines pre-approved for 10M+ merchants
3. **GST infrastructure** — Invoice generation, GSTIN validation, CA-approved formats
4. **Travel inventory** — Airline/aggregator partnerships (Amadeus, direct contracts)

#### What Competitors Would Need to Build

| Competitor | What They Have | What's Missing | Time to Replicate |
|------------|----------------|----------------|-------------------|
| **MakeMyTrip** | Travel inventory | Merchant KYC, credit rail, GST infra | **3+ years** (build payments company) |
| **PhonePe** | Merchant relationships, payments | Travel inventory, GST invoicing | **18 months** (travel partnerships) |
| **Razorpay** | Merchant payments, some credit | Travel inventory, voice agent | **2 years** |
| **Cleartrip** | Travel inventory | Merchant KYC, credit, GST | **3+ years** |

**Paytm's Moat:** Only company where **all 4 rails are already operational**. [**Confidence: High**]

#### Strategic Positioning: The SMB Whitespace

**Every other corporate travel tool targets large enterprises.**  
- MMT for Business, Yatra Corporate, Cleartrip for Business → 50+ employee companies
- Require IT integration (SAML, Concur APIs), ₹10L minimum contracts

**No one has gone after the 30M SMB merchants** who travel regularly but have zero tooling for it.

Paytm is positioned to **own this segment with zero competition** because replicating the stack requires becoming a full payments company. [**Confidence: High**]

---

## 🔄 How It Works

### The 5-Stage Call Flow (Under 3 Minutes)

```
┌─────────────────────────────────────────────────────────────┐
│  STAGE 1: Identity & Intent (0-15 sec)                      │
│  ─────────────────────────────────────────────────────      │
│  Merchant calls 1800-XXX-XXXX                               │
│  → AI Agent: "Namaste Ramesh bhai, kahan jaana hai?"       │
│  → Phone number = Auth (no OTP, no login)                  │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│  STAGE 2: Trip Search (15-45 sec)                           │
│  ─────────────────────────────────────────────────────      │
│  Merchant: "Kal Kanpur se Delhi, subah ki flight"          │
│  → Agent searches Paytm Travel API                          │
│  → Presents 2 options by voice                             │
│  → WhatsApp companion shows flight cards                    │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│  STAGE 3: Booking Confirmation (45-75 sec)                  │
│  ─────────────────────────────────────────────────────      │
│  Merchant: "Air India wala theek hai"                       │
│  → Agent: "Credit se karein ya wallet se?"                 │
│  → Merchant: "Credit se" → ₹3,800 deducted                 │
│  → Passenger + GSTIN auto-filled from KYC                   │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│  STAGE 4: GST + Invoice (75-90 sec)                         │
│  ─────────────────────────────────────────────────────      │
│  → Booking confirmed, PNR generated                         │
│  → GST invoice auto-created (GSTIN validated)              │
│  → PDF sent to WhatsApp (e-ticket + invoice)               │
│  → Agent confirms business name verbally                    │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│  STAGE 5: Summary (90 sec)                                  │
│  ─────────────────────────────────────────────────────      │
│  → Agent reads booking summary                              │
│  → "PNR aur invoice WhatsApp par aa gaya"                  │
│  → Call ends: "Safe travels!"                              │
└─────────────────────────────────────────────────────────────┘
```

### Voice-First, Screen-Second Design

#### Why This Hybrid Model?

| Task | Interface | Rationale |
|------|-----------|-----------|
| Initial intent capture | **Voice** | Fastest — "Book me to Mumbai tomorrow" vs. 6 taps |
| Flight comparison | **WhatsApp screen** | Need to see price/timing matrix, not listen to 8 options serially |
| Payment confirmation | **WhatsApp + Voice** | High-stakes action requires visual verification |
| GST details | **Auto-filled (WhatsApp)** | Pre-populated from KYC, editable if needed |

**Why WhatsApp (Not Dedicated App):**
- ✅ Zero download friction — 95% of merchants already have WhatsApp
- ✅ Familiar UX — chat-based, not new interface to learn
- ✅ WhatsApp Business API — supports interactive cards, payment integrations
- ✅ Async reference — merchant can check booking later without calling back

---

## 🎯 MVP Scope

### 500 Merchants × 3 Cities × 4 Weeks

#### ✅ What's In Scope

**Call Flow:**
- Inbound: Merchant calls helpline → 5-stage booking flow
- Outbound: Proactive calls for seasonal travel patterns  
- Languages: Hindi + English (code-switching supported)
- Target: <3 min avg call time

**Travel Inventory:**
- Domestic flights only (top 20 city pairs)
- Economy class, single traveler
- One-way or round-trip

**Payment:**
- Paytm Business Credit (primary nudge)
- Paytm Wallet (fallback if credit declined)
- No UPI/debit/credit card (forces credit activation)

**GST & Invoice:**
- Auto-generate invoice using merchant GSTIN from KYC
- Validate GSTIN via GST portal API
- Deliver PDF via WhatsApp within 10 sec
- CA-approved format (tested with 3 CAs pre-launch)

**Merchant Segment:**
- 500 merchants: Kanpur (200), Surat (150), Jaipur (150)
- Criteria: 2+ trips/year, credit pre-approved (₹20K+ limit), opted-in

#### ❌ What's Out of Scope (Post-MVP)

- Multi-city trips, international flights
- Trains, hotels, cabs
- Group bookings (>1 traveler)
- UPI/debit payment (defeats credit goal)
- Enterprise features (travel policy, approvals, dashboards)
- Post-trip expense closing

**Why This Scope?**
- Covers **70% of use cases** with **20% of engineering effort**
- Validates hypothesis: *Does voice + auto-GST + credit bundling drive bookings?*
- Forces credit activation (no UPI escape hatch)
- Achievable in 4 weeks [**Confidence: High**]

---
## Edge Cases & Handling (15 Critical Scenarios)

| Edge Case | Frequency | Detection | Handling | User Experience |
|-----------|-----------|-----------|----------|-----------------|
| **Credit limit insufficient** | 15% | Pre-check before options | Agent: "Credit ₹2K hai, flight ₹4K. Wallet se (₹5K available)?" | Offer wallet fallback |
| **Flight sold out mid-booking** | 3% | API error during payment | "Yeh flight sold out. Dusra: AI 11 AM, ₹4K?" | Immediate alternative |
| **GSTIN invalid/suspended** | 3% | GST validation API | "GST suspended. Update karein: [link]. Draft saved." | Block + save draft |
| **STT confidence <80%** | 20% | Google STT score | "Samajh nahi aaya, phir se?" → 2x → SMS with GUI link | Graceful fallback |
| **No flights available** | 12% | Empty API response | "Direct nahi. Connecting dikhaun ya dusra date?" | Alternatives |
| **Call drops mid-booking** | 5% | Connection lost | SMS: "Booking pending ₹3,800. Continue: [link]" | Resume at last stage |
---

## Competitive Landscape

### Why No One Else Owns This Space

**Enterprise Tools (MMT for Business, Cleartrip Corporate):**
- Target: 50+ employee companies
- Min contract: ₹10L/year + IT integration
- **Miss SMBs** — kirana owners can't sign ₹10L deals

**Consumer OTAs (MMT, Goibibo):**
- No GST auto-generation (merchants screenshot & edit)
- No business credit integration
- Treat merchants like leisure travelers

**Fintech Players (PhonePe, Razorpay):**
- Have merchant relationships, payments
- **Missing:** Travel inventory, GST invoicing
- Would take 18 months to build

### Paytm's Unique Stack
Only company with **all 4 pieces operational:**
1. 30M merchant KYC + GSTIN
2. Paytm Business Credit (₹5-50K lines)
3. GST invoice infrastructure
4. Travel inventory (Amadeus partnerships)

**Replication Time for Competitors:** 2-3 years (need to build payments company)

---

## Success Metrics

### North Star Metric
**% of Bookings via Paytm Business Credit: 40%+ target**
- Why: This is a credit activation play, not travel GMV
- Credit interest (12% APR) >> booking commission (2%)

### Tier 1: Product Health
| Metric | Target | Measurement |
|--------|--------|-------------|
| Call→Booking Conversion | 25%+ | (Completed bookings / Calls with intent) |
| Avg Call Duration | <3 min | P50 duration for successful bookings |
| Credit Attachment Rate | 40%+ | (Credit payments / Total bookings) |
| NPS | 50+ | Post-booking survey (0-10 scale) |
| Booking Failure Rate | <5% | (Payment/API errors / Total attempts) |

### Tier 2: AI Performance
| Metric | Target | Why It Matters |
|--------|--------|----------------|
| STT Accuracy | >85% WER | Voice input quality |
| NLU Intent Capture | >90% | Destination+date extraction success |
| Voice Fallback Rate | <20% | Sessions requiring GUI handoff |
| TTS Latency | <1 sec P95 | Keeps conversation flowing |

### Kill Criteria (No-Go Triggers)
- Conversion <15% after 4-week pilot → Voice adds friction, ship GUI instead
- Credit attach <25% → Not driving strategic goal, deprioritize
- NPS <30 → Merchants hate it, stop

**Key Drop-Off Points to Monitor:**
1. Search → Options (50%): No flights available, pricing too high
2. Options → Confirm (70%): Merchant changes mind, wants to compare competitors

---
## Analytics Event Specification

**Funnel Events (Track Drop-Off):**

| Event | Trigger | Properties | Why Track |
|-------|---------|------------|-----------|
| `call_initiated` | Merchant dials 1800-XXX | `merchant_id`, `timestamp`, `call_type` | Volume |
| `identity_verified` | KYC lookup succeeds | `merchant_id`, `credit_limit` | Auth success |
| `intent_captured` | NLU extracts destination+date | `origin`, `dest`, `confidence`, `retry_count` | NLU accuracy |
| `flight_search_completed` | API returns results | `results_count`, `api_latency_ms` | Inventory health |
| `options_presented` | Agent reads 2 flights | `flight_1_id`, `flight_2_price` | Offer quality |
| `flight_selected` | Merchant confirms | `flight_id`, `time_to_decide_sec` | Decision speed |
| `payment_method_selected` | Credit or wallet | `method`, `credit_balance_before` | **Credit activation** |
| `booking_completed` | Payment success | `booking_id`, `gmv`, `total_call_time_sec` | **Core conversion** |
| `invoice_delivered` | WhatsApp send OK | `invoice_id`, `delivery_time_sec` | GST delivery |
| `voice_fallback_triggered` | STT <80% confidence | `failure_reason`, `audio_sample_id` | Debug AI |

**Dashboard Views:**
- Real-time: Calls in progress, bookings/hour
- Daily: Conversion funnel, credit attach %
- Weekly: NPS, repeat booking rate
  
---

## 🤖 AI Architecture

### Conversational AI Stack

```
┌──────────────────────────────────────────────────────────┐
│  VOICE INPUT (STT)                                       │
│  Provider: Google Cloud Speech-to-Text                   │
│  Languages: Hindi + English (code-switching)             │
│  Target: >85% accuracy in noisy kirana shops             │
└──────────────────────────────────────────────────────────┘
                         ↓
┌──────────────────────────────────────────────────────────┐
│  NATURAL LANGUAGE UNDERSTANDING (NLU)                    │
│  Base: BERT fine-tuned on 1,000 merchant travel queries │
│  Extracts: Origin, Destination, Date, Time, Payment     │
│  Confidence threshold: >80% to proceed                   │
└──────────────────────────────────────────────────────────┘
                         ↓
┌──────────────────────────────────────────────────────────┐
│  DIALOGUE MANAGEMENT                                     │
│  Framework: Custom state machine (5 stages)              │
│  Slot-filling: Ask for missing info (destination→date)   │
│  Error recovery: 2 failures → switch to GUI              │
└──────────────────────────────────────────────────────────┘
                         ↓
┌──────────────────────────────────────────────────────────┐
│  BACKEND INTEGRATIONS                                    │
│  • Paytm Merchant API (KYC, credit balance)             │
│  • Paytm Travel API (flight search, booking)            │
│  • GST Validation API (GSTIN verification)              │
│  • WhatsApp Business API (card rendering, PDF)          │
└──────────────────────────────────────────────────────────┘
                         ↓
┌──────────────────────────────────────────────────────────┐
│  VOICE OUTPUT (TTS)                                      │
│  Provider: Google Cloud Text-to-Speech                   │
│  Profile: Conversational, warm, 1.2x speed              │
│  Latency: <1 sec from response → audio playback         │
└──────────────────────────────────────────────────────────┘
```
## Technical Architecture

[User Phone] 
    ↓ (Call)
[Twilio Voice Gateway]
    ↓
[STT: Google Cloud Speech API]
    ↓
[NLU: BERT fine-tuned on 1K merchant queries]
    ↓
[Dialogue Manager: State machine (5 stages)]
    ↓
[Backend APIs]
├── Paytm Merchant API (KYC, GSTIN)
├── Paytm Credit API (balance check, disbursal)
├── Paytm Travel API (flights, booking)
├── GST Validation API (real-time GSTIN check)
└── WhatsApp Business API (cards, PDF)
    ↓
[TTS: Google Cloud TTS]
    ↓
[User Hears Response]

**Key Decisions:**
- Why Google STT (not custom): Hindi+English code-switching out-of-box, 85% accuracy
- Why BERT (not GPT): Lower latency (<500ms), fine-tunable on 1K examples
- Why WhatsApp (not app): 95% merchant adoption, zero download friction
- ----

### Fallback Architecture: When AI Fails

**Design Principle:** **Never lose the merchant** — every failure has a recovery path.

| Failure Mode | Detection | Fallback | User Experience |
|--------------|-----------|----------|-----------------|
| **STT confidence <80%** | Google STT score | Ask once: "Phir se bataiye?" → SMS with text input link | 1 retry, then graceful handoff |
| **NLU extraction fails** | Missing entities after 2 prompts | "Screen par options dikhata hoon" → WhatsApp form | Switch to GUI |
| **Flight API timeout** | >5 sec response | "Thoda time lag raha, hold kijiye..." → Show loading on WhatsApp | Transparent wait |
| **No flights available** | Empty API response | "Direct nahi hai, connecting dikhaun?" → GUI alternatives | Proactive solutions |
| **Credit declined** | Credit API rejection | "Wallet se pay karein? ₹5K available" → Fallback payment | Alternative path |
| **Call drops mid-booking** | Connection lost | SMS: "Booking pending, continue: [link]" | Resume at last stage |

### Human Agent Handoff

**When to Escalate:**
- AI fails 3× on same query
- Merchant asks: "Kisi insaan se baat karni hai"
- Complex cases: Refunds, name changes, group bookings
- Emotional distress: "My flight cancelled, I'm stuck"

**Handoff Flow:**
- Agent: "Specialist se connect kar raha hoon..." [30 sec avg wait]
- Human sees: Full transcript + merchant profile + booking draft
- Seamless transition (no "repeat your issue" friction)

---

## 💰 Projected Impact

### Year 1 Business Case

**Assumptions:**
- 30M Paytm merchants → 10% travel for business = **3M TAM**
- Of 3M, 20% adopt in Year 1 = **600K merchants**
- Avg 3 trips/year per merchant
- Avg booking value: ₹8K
- 30% use Paytm Business Credit

**Year 1 Projections:**

| Assumption | Conservative | Base | Optimistic |
|---|---|---|---|
| % of 3M TAM who adopt Year 1 | 5% (150K merchants) | 20% (600K) | 35% (1.05M) |
| Trips/merchant/year | 2 | 3 | 4 |
| Avg booking value | ₹6,500 | ₹8,000 | ₹9,500 |
| Credit attachment rate | 20% | 30% | 45% |
| **GMV** | **₹195 Cr** | **₹1,440 Cr** | **₹3,990 Cr** |
| **Revenue** | **₹5.4 Cr** | **₹33 Cr** | **₹102 Cr** |

**Conservative:** 5% adoption assumes significant voice-UX friction and low initial awareness among semi-urban merchants. Benchmark: comparable B2B fintech products in India have seen 4–8% Year 1 adoption in new merchant cohorts.

**Base:** 20% adoption assumes smooth GTM execution via existing Paytm merchant app push notifications, in-app banners, and a small outbound sales team targeting pre-approved credit holders.

**Optimistic:** 35% adoption assumes a merchant referral loop activates after Month 2 (e.g., "refer a fellow merchant, get ₹200 travel credit") combined with a Paytm-led seasonal campaign (peak travel: Oct–Dec).

**Strategic Impact:**
- ✅ **600K new active credit users** (40% credit attachment × 1.8M bookings ÷ 3 trips)
- ✅ **Zero CAC** — activate existing merchant relationships, no acquisition cost
- ✅ **Data flywheel** — Travel patterns → Better credit underwriting → Lower defaults
- ✅ **Merchant retention** — 15% higher 90-day retention vs. non-travel merchants (hypothesis)

---

### The Credit Activation Flywheel

```
┌─────────────────────────────────────────┐
│   Merchant books travel via credit      │
│              ↓                          │
│   Paytm collects repayment data         │
│              ↓                          │
│   Better credit underwriting            │
│   (behavioral signals: travel = growth) │
│              ↓                          │
│   Higher credit limits approved         │
│              ↓                          │
│   Merchant books bigger trips           │
│   (multi-city, premium class)           │
│              ↓                          │
│   More repayment data collected         │
│   (Loop strengthens over time)          │
└─────────────────────────────────────────┘
```

**Key Insight:** Travel isn't the product — **credit is the product, travel is the wedge.** [**Confidence: High**]

---
## 📁 Portfolio Navigation

The repo is organized as a 0→1 product paper trail — from problem research to the post-pilot scale decision. Each folder contains the artifacts that phase actually produces in a real product org.

### Top-Level Folders

| Folder | What's Inside | Start Here |
|---|---|---|
| `01-problem-research/` | User interviews synthesis, JTBD, competitive analysis | `jobs-to-be-done.md` |
| `02-solution-design/` | 5-stage call flow, voice-GUI orchestration, conversation scripts, proactive triggers | `5-stage-call-flow.md` |
| `03-ai-architecture/` | Conversational AI stack, NLU intent examples, fallback architecture, human handoff logic | `conversational-ai-stack.md` |
| `04-mvp-specification/` | PRD, scope in-vs-out, pilot design, edge-cases register | `prd.md` |
| `05-launch-plan/` | 4-week timeline + 20+ execution artifacts from kickoff through Go/No-Go | `README.md` |
| `06-impact-projection/` | Business case, 3-year model, competitive moat, post-MVP roadmap | `business-case.md` |
| `07-appendix/` | Travel-fintech fusion thesis, interview prep, references | `travel-fintech-fusion-thesis.md` |
| `07-demo/` | Interactive D3 visualization of the 5-stage call flow + 18 failure modes | `call-flow-diagram.html` |

### The Execution Paper Trail (`05-launch-plan/`)

The launch-plan folder reconstructs what a CTO, PM, and supporting leads actually produce to ship this product. 23 artifacts across 8 phases:

| Phase | Representative Artifact |
|---|---|
| **Kickoff** | `engineering-kickoff-agenda.md` — P01–P12 DRIs, decisions, dependency map |
| **Week −2 Infra** | `load-test-plan-and-results.md` — 10 scenarios, 4 findings carried to Week −1 |
| **Week −1 Dogfood** | `dogfood-bug-log.md` — 18 issues, Sev-1 response tested at 27 min |
| **Gate Verification** | `go-live-gate-verification.md` — 18 PRD §7 gates, 7 co-signers |
| **Parallel Workstreams** | `legal-compliance-package.md`, `ops-dashboard-spec.md`, `human-agent-training-curriculum.md`, `crm-segmentation-500.md` |
| **Week 1 Exit** | `week-1-exit-review.md` — 5 gates passed; BUG-W1-03 Sev-1 fixed |
| **Week 7 Decision** | `week-7-go-no-go-memo.md` — GO WITH CONDITIONS; scale to 5,000 × 13 cities |
| **Retrospective** | `biggest-risks-retrospective.md` — 3 of 4 pre-launch risks contained; STT noise was the real bottleneck |

See `05-launch-plan/README.md` for the full index.

### Suggested Reading Paths

**If you have 5 minutes** → Read the Executive Summary above + `04-mvp-specification/prd.md` §1–2.

**If you have 20 minutes (PM perspective)** → Above + `02-solution-design/5-stage-call-flow.md` + `04-mvp-specification/scope-in-vs-out.md` + `05-launch-plan/week-7-go-no-go-memo.md`.

**If you have 45 minutes (engineering/architecture perspective)** → Above + `03-ai-architecture/conversational-ai-stack.md` + `05-launch-plan/infrastructure-runbook.md` + `05-launch-plan/load-test-plan-and-results.md` + `05-launch-plan/biggest-risks-retrospective.md`.

**If you have 90 minutes (full 0→1 narrative)** → Follow the folder numbers sequentially: 01 → 02 → 03 → 04 → 05 → 06.

---

## Strategic Context

### Why Paytm Can Win (Competitive Moat)
- Only player with merchant KYC + credit + GST + travel in one stack
- 30M existing relationships = zero CAC
- Competitors (MMT, PhonePe) would need 2-3 years to replicate

### Why Voice (Not Just GUI)
- Target users: Low digital literacy merchants in Tier 2/3 cities
- User research: 60% prefer speaking over app navigation for transactional tasks
- Speed advantage: 3 min voice booking vs. 8-12 min app flow

### Business Model
- Primary: Credit activation (12% APR on ₹432 Cr disbursed = ₹4.3 Cr)
- Secondary: Booking commission (2% of ₹1,440 Cr GMV = ₹29 Cr)
- **This is a credit product, not a travel product**
**TL;DR**

-Problem: 3M traveling merchants juggle OTAs + manual GST + separate credit — 17 min per booking, 40% invoice errors
-Solution: Voice-first helpline where merchants call, book in <3 min, get auto-GST invoice, pay via Paytm Business Credit
-Moat: Only company with merchant KYC + credit + GST + travel in one stack — competitors need 3 years to replicate
-MVP: 500 merchants, 3 cities, 4 weeks. Target: 25% conversion, 40% credit attach, 50+ NPS
-Impact: ₹1,440 Cr GMV, ₹33 Cr revenue (Year 1). 600K new active credit users. Zero CAC.

---
## Launch Plan

### Phase 1: Internal Dogfooding (Week 1-2)
- 20 Paytm employees book real trips
- Goal: Find blocking bugs, refine Hindi prompts

### Phase 2: Closed Pilot (Week 3-6)
- 500 merchants: Kanpur (200), Surat (150), Jaipur (150)
- Selection: 2+ trips/year, credit pre-approved, opted-in
- **Success criteria:** 100+ bookings, 25% conversion, 50+ NPS

### Phase 3: Go/No-Go (Week 7)
- **GO if:** Conversion >15%, credit attach >25%, NPS >40
- **NO-GO if:** Conversion <10% → Ship GUI instead of voice

### Phase 4: Scale (Week 8+)
- 10K merchants, 15 cities
- Add trains, multi-city (if data supports)

### Merchant Discovery
- In-app banner: "Book business travel via call: 1800-XXX"
- WhatsApp broadcast to credit-eligible merchants
- Proactive calls to merchants with seasonal travel patterns
- -----

### Author's Note

This case study was designed to demonstrate:
- ✅ End-to-end product thinking (problem → solution → impact)
- ✅ AI-specific skills (voice design, fallbacks, conversation flow)
- ✅ Business acumen (unit economics, competitive moats, GTM)
- ✅ Execution rigor (MVP scoping, metrics, edge cases, launch plan)

---

### Connect

**Portfolio:** [Click here](https://prernaaagarwal.framer.website/)  
**LinkedIn:** [Click here](https://www.linkedin.com/in/prernaaagarwal/)  
**Email:** agarwalprerna19@gmail.com

---

### License

This case study is a portfolio project for demonstration purposes.  
Paytm, MakeMyTrip, and other company names are used for illustrative context only.

---

**Last Updated:** April 2026  
**Status:** Case Study / Portfolio Project  
**Feedback Welcome:** Open to discussion on product approach, AI architecture, or business strategy.
