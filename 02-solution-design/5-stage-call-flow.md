# 5-Stage Call Flow
**Identity → Search → Confirm → Invoice → Summary**
*Paytm Merchant Travel Desk | Detailed Conversation Design*

---

## Design Principles

| Principle | What It Means |
|---|---|
| **3-minute hard cap** | Every stage has a time budget; if exceeded, graceful fallback triggers |
| **Voice-first, screen-second** | Voice drives the flow; WhatsApp companion is verification layer, not primary UI |
| **Phone = auth** | Caller ID + KYC lookup = zero-friction identity. No OTP, no password |
| **2 options maximum** | Never present more than 2 flight options by voice (cognitive load) |
| **Never lose the merchant** | Every failure mode has a recovery path — SMS, WhatsApp, human handoff |
| **Hindi-English code-switching** | Agent follows the merchant's language; doesn't force a choice |

---

## Full Call Flow: Bird's Eye View

```
Merchant dials 1800-XXX-XXXX
           │
           ▼
   ┌───────────────────┐
   │  IVR: Welcome     │  < 5 sec
   │  "Connecting..."  │
   └────────┬──────────┘
            │
            ▼
╔═══════════════════════════════╗
║  STAGE 1: IDENTITY & INTENT  ║  0–15 sec
║  Phone → KYC lookup → Greet  ║
╚═══════════════╤═══════════════╝
                │
                ▼
╔═══════════════════════════════╗
║  STAGE 2: TRIP SEARCH        ║  15–45 sec
║  NLU extract → API → 2 opts  ║
╚═══════════════╤═══════════════╝
                │
                ▼
╔═══════════════════════════════╗
║  STAGE 3: CONFIRMATION       ║  45–75 sec
║  Select → Payment → Confirm  ║
╚═══════════════╤═══════════════╝
                │
                ▼
╔═══════════════════════════════╗
║  STAGE 4: GST + INVOICE      ║  75–90 sec
║  PNR → Invoice gen → WA PDF  ║
╚═══════════════╤═══════════════╝
                │
                ▼
╔═══════════════════════════════╗
║  STAGE 5: SUMMARY & EXIT     ║  90 sec
║  Recap → Links → Goodbye     ║
╚═══════════════════════════════╝
```

---

## Stage 1: Identity & Intent (0–15 seconds)

### What Happens

```
Merchant calls → IVR plays 3-sec welcome → AI agent picks up
```

The moment the agent answers, a **KYC lookup fires in the background** (< 200ms): phone number → merchant name, city, GSTIN, credit balance, last booking history.

**Agent greeting (Hindi):**
> *"Namaste Ramesh bhai, Paytm Business Travel se bol raha hoon. Aapko kahan jaana hai?"*
> *(Hello Ramesh, this is Paytm Business Travel. Where do you need to go?)*

**Agent greeting (English):**
> *"Hello Ramesh, this is Paytm Business Travel. Where would you like to travel?"*

**Agent greeting (code-switch detected):**
> *"Namaste Ramesh bhai! Hi, this is Paytm Business Travel — where do you need to go?"*

### How Identity Works

| Signal | Source | Latency |
|---|---|---|
| Caller phone number | Telecom network | Instant |
| Merchant name | Paytm KYC DB | < 200ms |
| GSTIN | Paytm Merchant API | < 200ms |
| Credit balance | Paytm Credit API | < 300ms |
| Last booking city-pair | Booking history DB | < 300ms |

**Auth logic:**
- Phone number on file + active merchant status = **authenticated**
- Phone number not found = guest flow (collect name, offer credit signup)
- Merchant flagged (credit default, account hold) = graceful decline + human handoff

### Screen State (WhatsApp companion — sent simultaneously)

```
📲 [WhatsApp Business Message]
──────────────────────────────
Paytm Business Travel 🛫

Hi Ramesh! Your booking assistant
is live right now.

Follow along or tap to choose:
[View Options →]

──────────────────────────────
```

### Failure Modes

| Scenario | Detection | Recovery |
|---|---|---|
| KYC lookup fails (API timeout) | > 500ms no response | Continue with "merchant" (no name), retry async |
| Phone not in merchant DB | Empty lookup result | Guest flow: "Aapka naam kya hai?" |
| Merchant account suspended | Status flag in KYC | "Aapke account mein kuch issue hai" → human handoff |

### Stage 1 Exit Criteria
- ✅ Merchant identity confirmed (name retrieved or provided)
- ✅ WhatsApp companion message sent
- ✅ Intent captured: merchant indicates travel need OR agent asks for it

---

## Stage 2: Trip Search (15–45 seconds)

### What Happens

Agent extracts trip intent via NLU and executes a real-time flight search.

**Agent prompt:**
> *"Kahan se kahan jaana hai aur kab?"*
> *(Where to, where from, and when?)*

**Merchant utterance examples NLU must handle:**

| Utterance | NLU Extraction |
|---|---|
| "Kal Kanpur se Delhi, subah ki flight" | origin=KNU, dest=DEL, date=tomorrow, time=morning |
| "Mumbai next week, Wednesday" | dest=BOM, date=next Wednesday (relative) |
| "Delhi flight Sunday morning" | dest=DEL, date=Sunday, time=morning, origin=inferred from KYC city |
| "Bambai jaana hai" | dest=BOM (phonetic variant mapping) |
| "Day after tomorrow Bangalore" | dest=BLR, date=D+2 |
| "Jaipur se Delhi, aaj" | origin=JAI, dest=DEL, date=today |

**Slot-filling sequence (if incomplete):**
1. Missing origin → *"Kahan se? Kanpur se hi jaana hai?"* (infer from KYC city)
2. Missing date → *"Kab jaana hai?"*
3. Missing time preference → proceed with best options (morning default)

**API call fires after slots filled:**
```
Paytm Travel API → GET /flights
params: origin, destination, date, class=economy, pax=1
timeout: 5 sec
```

**Agent presents results:**
> *"Do options hain. Pehla: IndiGo, 8 baje, ₹4,200. Doosra: Air India, 9:30 baje, ₹3,800. Kaunsa theek rahega?"*
> *(Two options. First: IndiGo, 8 AM, ₹4,200. Second: Air India, 9:30 AM, ₹3,800. Which works?)*

### Ranking Logic (2 Options Only)

```
Score = (Price rank × 0.4) + (Time match × 0.3) + (Airline reliability × 0.2) + (Availability × 0.1)

Time match:
  - "subah" (morning): score flights 6:00–10:00 highest
  - "dopahar" (afternoon): score 12:00–16:00 highest
  - "shaam" (evening): score 17:00–21:00 highest
  - No preference: score by price

Show top 2 by composite score
```

### Screen State (WhatsApp companion)

```
✈️ Kanpur → Delhi | Tomorrow Morning
──────────────────────────────────────

┌─────────────────────────────────┐
│ IndiGo  6E-123                  │
│ 08:00 → 10:30  (2h 30m)        │
│ Non-stop   ·   Economy          │
│ ₹4,200                          │
│                    [Select →]   │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│ Air India  AI-456               │
│ 09:30 → 12:00  (2h 30m)        │
│ Non-stop   ·   Economy          │
│ ₹3,800                          │
│                    [Select →]   │
└─────────────────────────────────┘

🎤 Listening...  ░░░░░░░
(Or tap above to select)
```

### Failure Modes

| Scenario | Frequency | Detection | Recovery |
|---|---|---|---|
| No flights found | 12% | Empty API response | "Direct nahi hai. Connecting dikhaun? Ya doosra date?" |
| API timeout > 5 sec | 3% | Latency monitor | "Thoda time lag raha, hold kijiye..." + show loading on WA |
| Only 1 flight available | 8% | Single result | Present 1 option: "Sirf ek option hai — IndiGo 8 AM, ₹4,200. Theek hai?" |
| Price spike (> ₹12K) | 2% | Price threshold | Flag: "Is route ka fare aaj ₹11,500 hai. Continue karein?" |
| NLU fails 2× | 5% | Confidence < 80% | "Screen par options dikhata hoon" → WA dropdown form |

### Stage 2 Exit Criteria
- ✅ Origin, destination, date confirmed by NLU
- ✅ Flights retrieved from API
- ✅ 2 options presented via voice + WhatsApp cards
- ✅ Merchant is considering options

---

## Stage 3: Booking Confirmation (45–75 seconds)

### What Happens

Merchant selects a flight; agent confirms details and captures payment method.

**Merchant selects:**
> *"Air India wala theek hai"* / *"The second one"* / *"Air India"*

**Agent confirmation + payment prompt:**
> *"Air India, 9:30 baje, ₹3,800. Paytm Business Credit se karein ya wallet se?"*
> *(Air India, 9:30 AM, ₹3,800. Pay with Paytm Business Credit or wallet?)*

**Why credit is offered first:** Explicit nudge increases credit attachment from 30% (passive) to 60%+ (prompted).

**Merchant responds:**
> *"Credit se"* / *"Wallet se"* / *"Credit"* / *"Wallet"*

**Agent final confirmation:**
> *"Theek hai — Air India 9:30, ₹3,800, Paytm Business Credit se. Confirm karein?"*
> *(Okay — Air India 9:30, ₹3,800, via Paytm Business Credit. Confirm?)*

**Merchant confirms:**
> *"Haan, karo"* / *"Yes, confirm"* / *"Done"*

→ **Booking API call fires.**

### Screen State (WhatsApp companion)

```
✅ Air India AI-456 Selected

09:30 → 12:00  |  ₹3,800  |  Tomorrow

Payment Method:
  ○  Paytm Wallet         ₹2,100 available
  ●  Paytm Business Credit  ₹47,000 available  ← pre-selected

Passenger:    Ramesh Kumar         (auto-filled)
GSTIN:        09XXXXX5678          (auto-filled)
Seat:         Auto-assign          (editable post-booking)

──────────────────────────────
        [Confirm Booking]
──────────────────────────────
```

### Credit Nudge Logic

```
if credit_available >= flight_price:
    default_select = "credit"
    voice_prompt = "Credit se karein ya wallet se?"  [credit first]
elif credit_available > 0 and credit_available < flight_price:
    voice_prompt = "Wallet se karein? Credit mein ₹{X} hai, ₹{gap} kam hai."
else:
    voice_prompt = "Wallet se pay karein — ₹{balance} available hai."
    [skip credit mention]
```

### Failure Modes

| Scenario | Frequency | Detection | Recovery |
|---|---|---|---|
| Credit declined (risk) | 5% | Credit API rejection | "Credit approve nahi hua. Wallet se ₹5K available hai — chalega?" |
| Wallet balance insufficient | 8% | Wallet API | "Wallet mein ₹500 hai. ₹3,300 add karein ya credit use karein?" |
| Flight sold out mid-confirmation | 3% | Booking API error | "Yeh seat abhi sold out ho gayi. Air India 11 AM hai ₹4,000 mein — theek hai?" |
| Price changed since search | 5% | Price validation | "Price ₹3,800 se ₹4,100 ho gayi. Confirm karein?" |
| Merchant asks "can I pay later?" | 2% | Intent detection | Explain credit = pay later + repayment terms |
| Double-tap confirm (idempotency) | 1% | Idempotency key | Block duplicate, confirm single PNR only |

### Stage 3 Exit Criteria
- ✅ Flight selected
- ✅ Payment method confirmed
- ✅ Verbal confirmation received
- ✅ Booking API call initiated

---

## Stage 4: GST + Invoice (75–90 seconds)

### What Happens

Booking confirmation received from API; GST invoice generated and delivered.

**[Backend: PNR generated, booking confirmed]**

**Agent:**
> *"Booking confirm ho gayi! Aapka PNR hai 6E2F4G."*
> *"Ab GST invoice bana raha hoon..."*
> *(Booking confirmed! Your PNR is 6E2F4G. Now generating your GST invoice...)*

**[2-second pause — invoice generation + GSTIN validation]**

**Agent:**
> *"Invoice ready hai. WhatsApp par bhej diya — business naam 'Kumar General Store' — sahi hai?"*
> *(Invoice is ready. Sent to your WhatsApp — business name 'Kumar General Store' — is that correct?)*

**Merchant:**
> *"Haan, sahi hai"* / *"Correct"*

### Invoice Generation Logic

```
Input:
  - merchant_gstin (from KYC)
  - business_name (from KYC)
  - flight details (airline, route, date, fare)
  - hsn_code = 996411 (passenger air transport — hardcoded)
  - cgst_rate = 6%, sgst_rate = 6% (economy domestic)

Process:
  1. Validate GSTIN via GST Portal API (real-time, < 1 sec)
  2. Calculate taxes: base_fare + CGST + SGST + airline charges
  3. Generate PDF (CA-approved format)
  4. Deliver via WhatsApp Business API

SLA: Invoice on merchant's WhatsApp within 10 seconds of PNR
```

### Screen State (WhatsApp companion)

```
✅ Booking Confirmed!
──────────────────────────────
PNR:       6E2F4G
Flight:    Air India AI-456
Route:     Kanpur → Delhi
Date:      15 Apr 2026, 09:30
Passenger: Ramesh Kumar

💳 Paid via Paytm Business Credit
   Amount:  ₹3,800
   Remaining credit: ₹43,200

──────────────────────────────
📄 GST Invoice
   Kumar General Store
   GSTIN: 09XXXXX5678
   [Download PDF ↓]

🎫 E-Ticket
   [Download PDF ↓]
──────────────────────────────
```

### Failure Modes

| Scenario | Frequency | Detection | Recovery |
|---|---|---|---|
| GSTIN invalid/suspended | 3% | GST API error | "GSTIN update karna padega. Draft save kar deta hoon — update ke baad complete karenge." |
| Business name mismatch | 5% | KYC ≠ GST records | "Invoice pe naam 'Kumar Store' hai ya 'Kumar General Store'?" — voice correction |
| Invoice PDF fails | 1% | PDF generation timeout | "5 min mein WhatsApp par aayega" → async retry |
| WhatsApp delivery fails | 2% | WA delivery receipt | Fallback to SMS with download link |
| HSN code ambiguity | < 1% | Route-based check | Hardcoded 996411 for all domestic economy; no ambiguity |

### Stage 4 Exit Criteria
- ✅ PNR generated and read to merchant
- ✅ GSTIN validated
- ✅ GST invoice PDF delivered to WhatsApp
- ✅ E-ticket delivered to WhatsApp
- ✅ Business name confirmed verbally

---

## Stage 5: Summary & Exit (90 seconds)

### What Happens

Agent delivers clean summary and closes the call professionally.

**Agent:**
> *"Ramesh bhai, sab ho gaya! Kal subah 9:30, Air India, Kanpur se Delhi. PNR aur invoice WhatsApp par aa gaya hai. Kuch aur help chahiye?"*
> *(Ramesh, all done! Tomorrow 9:30 AM, Air India, Kanpur to Delhi. PNR and invoice are on WhatsApp. Need anything else?)*

**Merchant:**
> *"Nahi, bas. Dhanyavaad."* / *"No thanks, that's it."*

**Agent:**
> *"Dhanyavaad Ramesh bhai. Safe travels! Paytm Business Travel — kabhi bhi call kariye."*
> *(Thank you Ramesh. Safe travels! Paytm Business Travel — call anytime.)*

**[Call ends. Post-call events fire:]**
- Analytics event: `booking_completed` (call duration, GMV, payment method)
- Post-booking survey SMS sent (5-min delay): "Rate your experience: 1–5"
- CRM updated: last_booking_date, preferred_route, credit_utilization

### Screen State (WhatsApp companion — post-call)

```
📋 Booking Summary
──────────────────────────────
Air India AI-456
Kanpur → Delhi
15 Apr 2026 · 09:30 AM
Passenger: Ramesh Kumar
Booking ID: PTM-45678
Call duration: 2m 47s

Need help?
[✏️ Change/Cancel]   [↩️ Return Flight]
[📞 Call Support]    [⭐ Rate Experience]
──────────────────────────────
Support: 1800-XXX-XXXX · 24/7
```

### Design Choices for Exit

| Choice | Rationale |
|---|---|
| **No upsell at exit** | "Want hotel? Insurance?" → kills NPS. Trust > short-term revenue |
| **Self-serve links on WhatsApp** | Cancellation, return booking = async tasks that don't need a call |
| **Human fallback prominent** | Build trust: "We're here if you need us" |
| **Survey sent at 5-min delay** | Immediate survey = annoying. 5-min delay when merchant has processed the experience |

### Stage 5 Exit Criteria
- ✅ Booking summary read
- ✅ WhatsApp summary card delivered
- ✅ Call ended cleanly (no open loops)
- ✅ Analytics events fired
- ✅ Post-call survey queued

---

## End-to-End Time Budget

| Stage | Target | Hard Limit | Primary Lever |
|---|---|---|---|
| Stage 1: Identity | 15 sec | 20 sec | API pre-fetch on ring |
| Stage 2: Search | 30 sec | 45 sec | API timeout < 5 sec |
| Stage 3: Confirm | 30 sec | 40 sec | Auto-fill passenger + GSTIN |
| Stage 4: Invoice | 15 sec | 20 sec | PDF async, delivered < 10 sec |
| Stage 5: Summary | 10 sec | 15 sec | Short script, no upsell |
| **Total** | **100 sec** | **140 sec** | **Target: under 3 min** |

---

## Proactive Call Variant (Paytm calls merchant)

Same 5 stages, with a modified Stage 1 opening:

**Agent:**
> *"Namaste Priya ji, main Paytm Business Travel se call kar raha hoon. Pichle saal aapne March mein Mumbai gayi thi — kya is saal bhi plan hai? Abhi book karein, fare sasta milega."*
> *(Hello Priya, calling from Paytm Business Travel. Last year you traveled to Mumbai in March — any plans this year? Book now, fares are cheaper.)*

If merchant is interested → directly into Stage 2 (trip search).
If merchant is not interested → *"Koi baat nahi, zaroorat ho toh call kariye."* [mark DND preference if declined twice]

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/02-solution-design*
