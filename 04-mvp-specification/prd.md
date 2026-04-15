# Product Requirements Document
**Paytm Merchant Travel Desk — MVP v1.0**
*Voice-First Travel Concierge for SMB Merchants*
*04-mvp-specification | Status: READY FOR ENGINEERING*

---

## Document Control

| Field | Value |
|---|---|
| **Product** | Paytm Merchant Travel Desk |
| **Version** | MVP v1.0 |
| **Status** | Ready for Engineering |
| **PM Owner** | [Your Name] |
| **Engineering Lead** | TBD |
| **Target Launch** | Week 1 (post Week −1 dogfood) |
| **Last Updated** | April 2026 |
| **Reviewers** | Engineering, Finance, Legal, Customer Ops, Data |

---

## 1. Purpose & Background

### Problem Statement

Paytm's 30M verified merchants lose 15+ minutes per business trip juggling consumer OTAs (no GST auto-generation), manual invoice creation, and separate credit arrangements. 3M of these merchants travel for business 2+ times per year. No product in the market serves this segment: enterprise tools require ₹10L+/year contracts; consumer OTAs have no business-specific features.

### Solution

A voice-first travel helpline (1800-XXX-XXXX) where a Paytm merchant calls, books a domestic flight in under 3 minutes, auto-receives a GSTIN-linked GST invoice on WhatsApp, and pays via Paytm Business Credit — all using phone number as authentication.

### Strategic Framing

This is not a travel product competing with OTAs. It is a **credit activation product** using travel as the wedge. The north star metric is credit attachment rate, not GMV.

---

## 2. Goals & Non-Goals

### Goals (MVP v1.0)
- ✅ Enable any Paytm merchant to book a domestic flight by voice in < 3 minutes
- ✅ Auto-generate CA-compliant GST invoice delivered to WhatsApp within 10 seconds of booking
- ✅ Surface Paytm Business Credit as the default payment option at booking
- ✅ Authenticate merchants via phone number (zero login friction)
- ✅ Handle 18 documented failure modes with defined recovery paths
- ✅ Support Hindi, English, and Hindi-English code-switching

### Non-Goals (MVP v1.0 — explicitly out of scope)
- ❌ Train booking (different API, different NLU complexity, lower credit value)
- ❌ Hotel booking (requires separate conversation flow, blows 3-min constraint)
- ❌ International flights (passport handling, forex credit, GSTN zero-rating complexity)
- ❌ Group bookings (> 1 traveler)
- ❌ UPI / debit / credit card payment (defeats credit activation goal)
- ❌ EMI options (requires separate underwriting flow)
- ❌ Multi-city itineraries
- ❌ Post-trip expense closing via voice
- ❌ Enterprise approval workflows / travel policy enforcement
- ❌ Web portal or native app
- ❌ Gujarati / Marathi / Tamil / Telugu (Phase 2)

---

## 3. User Personas

### Persona 1: Ramesh Kumar (Primary)
- Kirana owner, Kanpur, age 42
- Travels 3×/year to Delhi for FMCG supplier meetings
- No Paytm Business Credit; pays cash/savings
- Low-moderate digital literacy
- **Core need:** Book trip + get GST invoice + preserve cash flow in < 5 min

### Persona 2: Priya Shah (Primary)
- Textile boutique owner, Surat, age 35
- Travels 6×/year to Mumbai/Bangalore for sourcing
- Has ₹50K Paytm Business Credit (unused for travel)
- High digital literacy, heavy WhatsApp user
- **Core need:** Auto-GST invoice + use existing credit line, zero manual entry

---

## 4. User Stories

### Epic 1: Core Booking Flow

```
US-001 [MUST HAVE]
As a Paytm merchant,
I want to book a domestic flight by calling a helpline number,
So that I can book without opening any app or website.
Acceptance criteria:
  - AC1: Merchant calls 1800-XXX-XXXX and hears IVR within 2 rings
  - AC2: AI agent greets merchant by first name within 5 seconds of call pickup
  - AC3: Agent correctly identifies merchant from phone number in > 99% of calls
  - AC4: Complete booking achievable without any screen interaction

US-002 [MUST HAVE]
As a Paytm merchant,
I want the AI agent to understand where and when I want to travel,
So that I don't have to spell out every detail in a specific format.
Acceptance criteria:
  - AC1: NLU extracts destination from natural utterances ("Delhi jaana hai", "Mumbai flight") with > 90% accuracy
  - AC2: NLU handles relative dates ("kal", "parson", "agle hafte") and resolves to absolute dates
  - AC3: Origin inferred from merchant KYC city if not stated; confirmed by agent
  - AC4: Slot-filling: if key info missing, agent asks for it naturally (max 3 turns)
  - AC5: NLU handles Hindi, English, and mixed-language utterances

US-003 [MUST HAVE]
As a Paytm merchant,
I want to see exactly 2 flight options presented clearly,
So that I can make a quick decision without being overwhelmed.
Acceptance criteria:
  - AC1: Agent presents exactly 2 options by voice (never more, never fewer unless only 1 available)
  - AC2: Each option states: airline name, departure time, price only (no filler info)
  - AC3: WhatsApp companion card rendered with both options before agent finishes reading
  - AC4: Merchant can select option by voice OR by tapping the WA card — both paths functional
  - AC5: Flight ranking: composite score (price 40%, time match 30%, reliability 20%, availability 10%)
```

### Epic 2: Payment & Credit

```
US-004 [MUST HAVE]
As a Paytm merchant with Business Credit,
I want to be explicitly offered my credit line at booking,
So that I don't default to cash/savings out of habit.
Acceptance criteria:
  - AC1: Agent explicitly mentions credit as first option: "Credit se karein ya wallet se?"
  - AC2: Credit balance shown on WA confirmation screen before final confirm
  - AC3: If credit available and >= flight price: credit pre-selected on WA screen
  - AC4: Merchant can override to wallet via voice or WA tap
  - AC5: If no credit: agent offers wallet only (no credit mention to avoid confusion)

US-005 [MUST HAVE]
As a Paytm merchant,
I want to confirm my booking with a single verbal "yes",
So that I don't have to tap any buttons to complete the purchase.
Acceptance criteria:
  - AC1: Verbal confirmations accepted: "haan", "yes", "theek hai", "karo", "confirmed", "chalega", "done"
  - AC2: Single confirmation required (agent does not ask twice)
  - AC3: Verbal confirm and WA button tap both trigger identical booking action
  - AC4: Idempotency key prevents double booking if both channels fire simultaneously
  - AC5: Booking API call fires within 500ms of confirmation signal
```

### Epic 3: GST Invoice

```
US-006 [MUST HAVE]
As a Paytm merchant,
I want a GST invoice automatically generated and sent to WhatsApp,
So that I never have to manually create or type invoice details.
Acceptance criteria:
  - AC1: Invoice PDF delivered to merchant WhatsApp within 10 seconds of PNR generation
  - AC2: Invoice contains: merchant legal name, GSTIN, HSN 996411, base fare, CGST, SGST, total
  - AC3: Invoice format reviewed and approved by minimum 3 CAs before launch
  - AC4: GSTIN validated in real-time via GST portal API before invoice generation
  - AC5: If GSTIN suspended: booking completes, invoice flagged for async generation, merchant notified
  - AC6: E-ticket sent in same WhatsApp message as invoice
  - AC7: Invoice PDF is < 500KB (optimized for 2G delivery)
  - AC8: Agent verbally confirms business name before ending call ("Kumar General Store — sahi hai?")
```

### Epic 4: WhatsApp Companion

```
US-007 [MUST HAVE]
As a Paytm merchant on the call,
I want to see flight options and booking details on my WhatsApp simultaneously,
So that I can verify information without having to remember everything the agent said.
Acceptance criteria:
  - AC1: WA companion message sent within 200ms of same information being read by voice
  - AC2: WA flight option cards display: airline, flight number, times, duration, price
  - AC3: WA payment confirmation card shows: selected flight, payment method + balance, auto-filled passenger + GSTIN
  - AC4: WA booking confirmed card shows: PNR, full flight details, payment charged, remaining credit
  - AC5: WA post-call summary card shows: booking ID, action buttons, NPS rating row
  - AC6: All WA cards functional on both Android and iOS WhatsApp

US-008 [SHOULD HAVE]
As a Paytm merchant after the call,
I want self-serve options on WhatsApp for common post-booking needs,
So that I don't have to call back for simple tasks.
Acceptance criteria:
  - AC1: Post-call WA card shows: [Change/Cancel], [Return Flight], [Re-send Invoice], [Call Support]
  - AC2: [Return Flight] tap initiates new call to helpline (deep link or call trigger)
  - AC3: [Re-send Invoice] tap sends invoice PDF to WA immediately (no call required)
  - AC4: [Change/Cancel] tap routes to human agent queue (calls the helpline with pre-routed reason)
```

### Epic 5: Fallback & Error Handling

```
US-009 [MUST HAVE]
As a Paytm merchant in a noisy environment,
I want the system to recover gracefully when it can't hear me clearly,
So that I don't get stuck in retry loops or abandon the call.
Acceptance criteria:
  - AC1: After 1 failed STT attempt: agent rephrases and asks again
  - AC2: After 2 failed STT attempts: agent offers WA form fallback
  - AC3: After 3 failed attempts: agent offers SMS link with booking form
  - AC4: Recovery path offered within 4 seconds of detected failure
  - AC5: Merchant never hears: "I did not understand your request" — agent rephrases positively

US-010 [MUST HAVE]
As a Paytm merchant whose call drops mid-booking,
I want to receive a recovery link immediately,
So that I don't lose my booking progress or held seats.
Acceptance criteria:
  - AC1: On call disconnect (non-SUMMARY stage): session state saved to DB within 1 second
  - AC2: WA resume message sent within 10 seconds of call drop detection
  - AC3: Resume link pre-fills all confirmed slots (origin, destination, date, selected flight)
  - AC4: Payment details NOT pre-filled (security; merchant must re-confirm payment)
  - AC5: Held seats maintained for 15 minutes from call drop time
  - AC6: If WA delivery fails: SMS fallback sent within 30 seconds
```

### Epic 6: Human Handoff

```
US-011 [MUST HAVE]
As a Paytm merchant with a complex issue,
I want to be transferred to a human agent who already knows my situation,
So that I don't have to repeat everything from the beginning.
Acceptance criteria:
  - AC1: Human agent screen shows full context packet before first word is spoken
  - AC2: Context packet includes: merchant profile, call transcript, current stage, extracted slots, handoff reason
  - AC3: Human agent warm transfer SLA: < 90 seconds wait (URGENT: < 45 sec)
  - AC4: Merchant hears hold music during transfer (not silence)
  - AC5: Human agent opens with merchant's name and situation ("I see you're trying to book Delhi...")
  - AC6: Agent never says "AI failed" or "system error" — frames as specialist escalation

US-012 [MUST HAVE]
As a Paytm merchant,
I want to be able to request a human agent at any point in the call,
So that I always feel in control of the interaction.
Acceptance criteria:
  - AC1: Human handoff triggered by: "insaan se baat karni hai", "human agent", "manager", "real person", "transfer me"
  - AC2: NLU intent HUMAN_AGENT detected with confidence > 0.70 (partial match sufficient)
  - AC3: Handoff initiated within 2 seconds of intent detection
  - AC4: No re-prompting or confirmation required ("Are you sure?") — immediate handoff
```

---

## 5. Functional Requirements

### FR-01: Authentication
- System MUST authenticate merchants via caller ID phone number lookup in Paytm KYC DB
- Authentication MUST complete within 300ms of call pickup
- If phone not found: system MUST initiate guest flow (collect name, offer credit signup)
- If account suspended: system MUST decline gracefully and route to human agent
- System MUST NOT require OTP, password, or any additional input for KYC-matched merchants

### FR-02: Language Handling
- System MUST support Hindi (hi-IN), English (en-IN), and Hindi-English code-switching
- Language detection MUST occur from first merchant utterance (within 500ms)
- System MUST NOT force merchant to declare language preference upfront
- Once detected, system MUST maintain same language for full session
- City names in regional phonetic variants (Bambai, Dilli, Calcutta) MUST be mapped to canonical IATA codes

### FR-03: Flight Search
- Search MUST return results within 5 seconds (p95)
- If API timeout: system MUST serve cached results (< 15 min old) or notify merchant transparently
- Search MUST cover minimum: top 35 domestic routes from pilot cities (Kanpur, Surat, Jaipur)
- Results MUST be filtered to economy class, single traveler, direct + 1-stop options
- System MUST present exactly 2 options by voice (ranked by composite score; see FR-03a)
- If only 1 flight available: present 1 option with explicit "only option available" disclosure
- If 0 flights: immediately offer connecting flights, alternate dates, or clean exit

### FR-03a: Flight Ranking Algorithm
```
Score = (Price_rank × 0.40) + (Time_match × 0.30) + (Reliability × 0.20) + (Availability × 0.10)

Price_rank:  Normalize fares in result set; lowest = 1.0, highest = 0.0
Time_match:  Score based on merchant's stated time preference:
             "subah" (morning) → peak score for 06:00–10:00 departures
             "dopahar" → peak for 12:00–16:00
             "shaam" → peak for 17:00–21:00
             No preference → uniform distribution
Reliability: Airline on-time performance (IndiGo, Air India, Vistara ranked historically)
Availability: Seat count on flight; < 5 seats = 0.5 penalty factor

Present: Top 2 by score
```

### FR-04: Booking Confirmation
- Passenger name MUST be auto-filled from KYC (editable post-booking only)
- GSTIN MUST be auto-filled from KYC (verified before booking, not after)
- Payment method MUST be explicitly offered: credit FIRST if available, wallet SECOND
- Booking API call MUST use idempotency key (format: `{merchant_id}_{flight_id}_{date}_{timestamp_rounded_60s}`)
- Booking MUST NOT fire until explicit confirmation signal (voice OR WA button tap)
- System MUST validate final price before payment (handle dynamic pricing between search and confirm)

### FR-05: GST Invoice Generation
- GSTIN MUST be validated via GSTN API before invoice is generated
- HSN code: 996411 (domestic air transport) — hardcoded, no dynamic selection
- Tax calculation: Base fare + CGST (5%) + SGST (5%) = total [economy class domestic]
- Invoice number MUST be sequential per merchant, never reused (per GST law)
- Invoice MUST be delivered to merchant's registered WhatsApp within 10 seconds of PNR
- Invoice PDF MUST be < 500KB
- Invoice format MUST be reviewed by minimum 3 CAs before production launch
- If GSTN API down: proceed with booking, queue invoice for async generation, notify merchant

### FR-06: WhatsApp Integration
- All 8 message templates MUST be pre-approved by Meta before launch
- WA messages MUST be delivered before or simultaneous with voice (max delta: 200ms)
- WA companion MUST function on Android WhatsApp 2.23+ and iOS WhatsApp 23.x+
- System MUST handle WA delivery failure: retry × 2 (10-sec backoff) → SMS fallback
- Post-call summary WA MUST be sent 30 seconds after call disconnection

### FR-07: Analytics
- All 20 defined analytics events MUST be logged in real-time to Kafka → BigQuery
- Event logging MUST NOT block the call flow (fire-and-forget async)
- Session state MUST be persisted to DB on every stage transition (call-drop recovery)
- PII in analytics: masked (last 4 of GSTIN, no full phone number in event logs)

### FR-08: Performance SLAs
| Component | P95 Latency | Availability |
|---|---|---|
| KYC lookup | < 300ms | 99.9% |
| STT transcription | < 1,500ms | 99.5% |
| NLU inference | < 150ms | 99.9% |
| Flight search API | < 3,000ms | 99.0% |
| Credit check | < 2,000ms | 99.5% |
| Booking API | < 3,000ms | 99.5% |
| GST validation | < 1,000ms | 99.0% |
| Invoice PDF generation | < 5,000ms | 99.0% |
| WA message delivery | < 5,000ms | 98.5% |
| TTS first audio | < 1,000ms | 99.5% |
| **End-to-end booking** | **< 180,000ms** | **98.0%** |

---

## 6. Non-Functional Requirements

### Security
- NFR-S1: All call recordings encrypted at rest (AES-256); deleted after 30 days
- NFR-S2: PII (GSTIN, phone, name) transmitted only over TLS 1.3+
- NFR-S3: API authentication via mTLS for all internal service calls
- NFR-S4: Payment operations MUST pass PCI-DSS Level 1 compliance (inherited from Paytm payments infra)
- NFR-S5: No PII logged in application logs; structured event logs only

### Scalability
- NFR-SC1: System MUST support 50 concurrent calls (pilot requirement)
- NFR-SC2: System SHOULD support 500 concurrent calls (post-pilot scale)
- NFR-SC3: Dialogue Manager auto-scales on concurrent session count (Kubernetes HPA)
- NFR-SC4: Redis session store in cluster mode (3 replicas minimum)

### Reliability
- NFR-R1: System MUST handle individual API failures without full session failure
- NFR-R2: Circuit breakers on all external API integrations (5-failure threshold, 30-sec recovery)
- NFR-R3: Call-drop recovery MUST work for all failures after Stage 1 (identity confirmed)
- NFR-R4: No financial operation (charge, booking) executed without idempotency protection

### Compliance
- NFR-C1: PDPA compliance: explicit merchant consent captured at onboarding for data processing
- NFR-C2: TRAI DND compliance: outbound calls only to consented, non-DND merchants
- NFR-C3: GST compliance: invoice format signed off by tax counsel before production
- NFR-C4: RBI compliance: credit operations within NBFC license scope only

---

## 7. Acceptance Criteria Summary (Go-Live Gates)

All of the following MUST be true for the product to launch to Pilot merchants:

```
Technical:
  □ All 12 P-milestones from Week −2 complete (see 05-launch-plan/4-week-timeline.md)
  □ Load test: < 3% error rate at 50 concurrent calls
  □ Call-drop recovery tested and verified for all stages
  □ All 8 WA templates approved by Meta
  □ Security pen test complete; all critical findings remediated

Quality:
  □ Dogfood: 40+ bookings completed by internal staff
  □ NLU accuracy > 90% on 200-sample test set
  □ STT WER > 85% on 50-call noisy environment test
  □ GST invoice format approved by 3 CAs
  □ Human handoff context packet verified for all 5 trigger types

Compliance:
  □ Legal sign-off on GST invoice template (tax counsel)
  □ PDPA consent language in onboarding flow approved
  □ DND compliance check tool integrated and tested
  □ Data retention policy implemented (30-day call recording deletion)

Business:
  □ Human agent team trained (all 8 agents) on context packet and 5 handoff scenarios
  □ Helpline number 1800-XXX-XXXX operational
  □ 50 soft-launch merchants selected and onboarded via CRM
  □ Ops dashboard live with: conversion rate, error rate, credit attach, call volume
```

---

## 8. Dependencies

| Dependency | Owner | Status | Risk if Delayed |
|---|---|---|---|
| Paytm Travel API (flight search + booking) | Platform Engineering | In progress | Blocks core flow; critical path |
| Paytm Credit API (balance + charge) | Credit Engineering | In progress | Blocks credit attach metric |
| GST Validation API (GSTN portal) | Finance Engineering | Ready | Delay = async invoice only at launch |
| WhatsApp Business API account + templates | Product + WA | Pending Meta approval | Delay = SMS-only companion at launch |
| Google Cloud STT/TTS credentials + quotas | AI Engineering | Ready | — |
| BERT fine-tuned NLU model v1 | ML Engineering | In progress | Delay = rule-based fallback only |
| Redis cluster provisioning | Platform Engineering | Planned | Blocks session state + call-drop recovery |
| Helpline 1800 number provisioning | Telecom Ops | Planned (6-week lead) | Critical path — start immediately |
| Human agent training materials | Product + Customer Ops | Not started | Needed by Week −1 |

---

## 9. Open Questions

| # | Question | Owner | Target Resolution |
|---|---|---|---|
| OQ-1 | Which airline API partners are confirmed for Kanpur routes? (KNU has limited direct flights) | Travel Partnerships | Week −3 |
| OQ-2 | What is the idempotency window for Credit API? (Needed for call-drop + retry design) | Credit Engineering | Week −3 |
| OQ-3 | Is 1800 number provisioning within 6 weeks feasible? (Critical path) | Telecom Ops | Immediately |
| OQ-4 | WA template for invoice delivery — does PDF attachment require a separate template? | Platform Engineering | Week −3 |
| OQ-5 | Credit charge for pending-PNR bookings: what happens if airline confirms 5 min later? | Credit + Finance | Week −3 |
| OQ-6 | GSTIN validation API rate limit: can it handle 50 concurrent validations? | Finance Engineering | Week −4 |

---

*Document status: READY FOR ENGINEERING REVIEW*
*Next review: [Engineering kickoff date]*
*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/04-mvp-specification*
