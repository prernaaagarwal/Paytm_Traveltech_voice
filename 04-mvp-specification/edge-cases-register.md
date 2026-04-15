# Edge Cases Register
**23 Scenarios · Detection · Handling · User Experience · Owner**
*Paytm Merchant Travel Desk | 04-mvp-specification*

---

## Register Format

Each entry documents:
- **Scenario ID:** EC-XX
- **Category:** Payment / Travel / GST / Voice / Identity / Session
- **Frequency:** Estimated occurrence rate in production
- **Detection:** How the system knows this scenario is occurring
- **Handling:** Exact system behavior and agent script
- **User Experience:** What the merchant sees/hears
- **Owner:** Which team is responsible for this edge case

---

## CATEGORY 1: IDENTITY & AUTHENTICATION

### EC-01: Merchant Calls from Unregistered Phone Number
**Frequency:** ~3%
**Detection:** KYC lookup returns null for caller phone number

```
System behavior:
  KYC lookup fires on ring → null response
  Agent does NOT greet by name

Agent script:
  "Namaste! Paytm Business Travel se bol raha hoon.
   Aapka number mere paas nahi hai — naam bata sakte hain?"
  [Collect name → Guest flow: search flights, wallet payment only]
  [No credit available without KYC]
  [Post-call: CRM flag for merchant onboarding team to follow up]

User experience: Guest booking possible; credit unavailable; suggest Paytm Business signup
Owner: Platform Engineering + Merchant Ops
```

---

### EC-02: Merchant Account Suspended (Credit Default / KYC Issue)
**Frequency:** ~1%
**Detection:** KYC lookup returns `account_status: SUSPENDED`

```
System behavior:
  Detect suspension on identity lookup
  Do NOT proceed with booking flow

Agent script (SUSPENDED — credit default):
  "Namaste {name}. Aapke account mein kuch issue hai.
   Main aapko specialist se connect karta hoon jo help kar sakenge."
  [Immediate human handoff — reason: account_suspension]

Agent script (SUSPENDED — KYC issue):
  "Namaste {name}. Aapka account verify karna hai.
   Paytm app mein KYC complete kar sakte hain — phir travel booking ho jayegi."
  [Send WA deep link to KYC completion]

User experience: Sensitive handling — never say "suspended" bluntly; offer a path
Owner: Customer Ops + Credit Team
```

---

### EC-03: Merchant Calls from Different Number (SIM Change / Alternate Phone)
**Frequency:** ~2%
**Detection:** KYC lookup for new number returns null; but merchant states their Paytm ID

```
System behavior:
  Guest flow initiated
  Agent: "Aapka Paytm registered number bataiye — usi se verify kar sakte hain"
  [Merchant states their Paytm-registered number]
  [Agent: "Aapko usi number se call karna hoga, ya Paytm app se number update karein"]

User experience: Can't book with alternate number in MVP; clear path to resolution
Owner: Platform Engineering
```

---

## CATEGORY 2: TRAVEL INVENTORY

### EC-04: No Direct Flights on Requested Route
**Frequency:** ~12%
**Detection:** Flight API returns `flights: []` for origin/destination/date combination

```
System behavior:
  API returns empty → check connecting flights API
  If connecting flights exist: present them
  If no connecting flights: offer alternate dates

Agent script (connecting available):
  "Is route pe direct flight nahi hai kal.
   Connecting option hai via Delhi — total 5 ghante, ₹6,200.
   Dikhaun? Ya doosra date lein?"
  [WA: Connecting flight card with via city, total time, price]

Agent script (no flights at all):
  "Is route pe kal koi flight nahi hai.
   16 April ya 17 April pe hai — kaunsa chalega?"
  [WA: Calendar picker for alternate dates]

Agent script (route truly not served):
  "Is city pair pe flight service nahi hai.
   Nearest alternate: [city X] se — woh dekhen?"

User experience: Always offer an alternative; never a dead end
Owner: Travel Partnerships + Platform Engineering
```

---

### EC-05: Flight Sold Out Between Search and Booking Confirmation
**Frequency:** ~3%
**Detection:** Booking API returns `SEAT_NOT_AVAILABLE` error code

```
System behavior:
  Pre-check seat availability 500ms before firing booking API
  If unavailable: fetch Flight 2 availability immediately

Agent script:
  "Oh — yeh seat abhi sold out ho gayi.
   [Flight 2 detail] abhi available hai ₹{price2} mein — book karein?"
  [WA: Updated card showing available flight only]

If both sold out:
  Agent: "Dono options chali gayein. Fresh search kar raha hoon..."
  [Re-run search API; present 2 new results]

User experience: Fast pivot — merchant doesn't restart from scratch
Owner: Platform Engineering
```

---

### EC-06: Price Changes > 5% Between Search and Booking
**Frequency:** ~5%
**Detection:** Price validation call before booking fires: `abs(new_price - search_price) / search_price > 0.05`

```
System behavior:
  Price re-validated at Stage 3 before charging
  If increase < 10%: inform and confirm
  If increase > 10%: flag and suggest stable alternate

Agent script (small increase):
  "Ek second — price ₹{old} se ₹{new} ho gayi, ₹{diff} zyada.
   Confirm karein ya doosra option lein?"

Agent script (large increase):
  "Price ₹{old} se ₹{new} ho gayi — zyada change hai.
   [Alt flight] abhi bhi ₹{stable_price} pe stable hai — woh better hai."
  [WA: Updated price card with new amounts highlighted]

User experience: Transparent — trust through honesty; merchant feels informed, not trapped
Owner: Platform Engineering
```

---

### EC-07: All Flights Fully Booked for Date (High-Traffic Days)
**Frequency:** ~2% (peaks: long weekends, festival seasons)
**Detection:** All results from flight search API have `seats_available: 0`

```
System behavior:
  Check ± 1 day for availability
  Suggest alternate travel date with price comparison

Agent script:
  "Kal ke sabhi flights full ho gaye hain — Diwali season hai.
   16 April: ₹4,200 (kuch seats hain)
   17 April: ₹3,800 (acha availability hai)
   Kaunsa better hai?"
  [WA: Calendar with available dates highlighted, prices shown]

User experience: Helpful context (why), clear alternatives, price comparison on screen
Owner: Platform Engineering
```

---

## CATEGORY 3: PAYMENT & CREDIT

### EC-08: Credit Limit Insufficient for Flight Price
**Frequency:** ~15%
**Detection:** `credit_available < flight_price` returned by Credit API

```
System behavior:
  Calculate gap: how much short
  If wallet covers the remainder: offer combined (post-MVP) or wallet-only
  If wallet also insufficient: offer top-up path

Agent script (wallet covers):
  "Credit mein ₹{available} hai, ₹{gap} kam hai.
   Wallet se pay kar sakte hain — ₹{wallet_balance} available hai.
   Wallet se karein?"
  [WA: Payment screen showing wallet as selected option]

Agent script (neither sufficient):
  "Abhi payment nahi ho sakti — credit aur wallet dono short hain.
   Booking draft save karta hoon — payment arrange kar ke WhatsApp link se complete kar lena."
  [WA: Draft booking link valid 24hr + wallet top-up link]

User experience: Never say "declined" — always give a concrete next step
Owner: Credit Team + Platform Engineering
```

---

### EC-09: Credit Declined — Risk Hold
**Frequency:** ~5%
**Detection:** Credit API returns `status: DECLINED, reason: RISK_HOLD`

```
System behavior:
  Do NOT disclose "risk hold" to merchant (sensitive)
  Offer wallet as immediate fallback
  Post-call: flag account for credit team review

Agent script:
  "Credit abhi temporary hold pe hai — 24–48 ghante mein resolve ho jayega.
   Wallet se book karein abhi? ₹{wallet_balance} available hai."
  [WA: Payment screen with wallet pre-selected]

Post-call WA (sent separately):
  "Aapki credit line ke baare mein: ek temporary hold hai.
   Resolve karne ke liye: [Credit Support →]"

User experience: Sensitive — never reveal risk reason; immediate path forward
Owner: Credit Team
```

---

### EC-10: Payment Gateway Timeout (Neither Confirmed Nor Failed)
**Frequency:** ~2%
**Detection:** Payment API no response in 10 seconds; status unknown

```
System behavior:
  CRITICAL: Do not retry automatically (risk of double charge)
  Query payment status API after 30 seconds
  If CONFIRMED: proceed to Stage 4
  If FAILED: retry once; if still failed → draft save + WA link
  If UNKNOWN > 60 sec: treat as failed; draft save

Agent script:
  "Payment process ho raha hai — ek minute wait karein..."
  [If > 10 sec: "Thoda delay ho raha hai — main check kar raha hoon"]
  [If confirmed after delay: normal Stage 4 flow]
  [If failed: offer retry or draft save]

User experience: No silence > 4 sec; transparent status updates
Owner: Finance Engineering
```

---

### EC-11: Double Booking Attempt (Merchant Confirms Twice)
**Frequency:** ~1%
**Detection:** Idempotency key collision: `{merchant_id}_{flight_id}_{date}_{timestamp_60s_window}`

```
System behavior:
  Second booking attempt with same idempotency key → return first booking's PNR
  No second charge
  Agent confirms only one booking exists

Agent script:
  "Booking pehle se ho gayi hai! PNR hai {pnr}.
   WA pe check karein — wahan sab details hain."
  [No new WA message needed — first booking card already sent]

User experience: Merchant doesn't notice; idempotency is silent
Owner: Platform Engineering
```

---

## CATEGORY 4: GST & INVOICE

### EC-12: GSTIN Suspended or Cancelled
**Frequency:** ~3%
**Detection:** GST validation API returns `status: SUSPENDED` or `CANCELLED`

```
System behavior:
  IMPORTANT: Do NOT block booking for GSTIN issue
  Complete booking → PNR generated → payment charged
  Queue invoice for async generation (pending GSTIN fix)

Agent script:
  "Booking ho gayi! PNR hai {pnr}.
   GST number mein ek issue hai — update ke baad invoice generate hogi.
   WhatsApp pe link bhej raha hoon."
  [WA: Booking confirmed card + GSTIN update link + "Invoice pending" notice]
  [Async job: retry invoice generation every 24hr for 7 days]

User experience: Booking not blocked; clear path to resolution; invoice promised
Owner: Finance Engineering
```

---

### EC-13: Business Name Mismatch (KYC Name ≠ GSTN Registration)
**Frequency:** ~5%
**Detection:** GST API returns `legal_name` that differs from Paytm KYC `business_name`

```
System behavior:
  Surface discrepancy to merchant for verbal resolution
  Use GST portal legal name as authoritative (for invoice)
  Update KYC in background (with merchant confirmation)

Agent script:
  "Invoice pe kaunsa naam ho — 'Kumar Store' ya 'Kumar General Store'?
   GST records mein 'Kumar General Store' hai."
  [Merchant confirms → use confirmed name]
  [WA: Preview invoice with confirmed name]
  [CRM: Update KYC business_name to match GSTN legal_name]

User experience: Merchant feels in control of their own invoice
Owner: Finance Engineering + Merchant Ops
```

---

### EC-14: Invoice PDF Generation Fails
**Frequency:** ~1%
**Detection:** PDF service returns error or times out after 10 seconds

```
System behavior:
  Queue async retry (3 attempts, exponential backoff: 30s, 2min, 10min)
  Send text-format invoice to WA as immediate fallback
  Send PDF when ready (< 30 min in most cases)

Agent script:
  "Invoice 5 minute mein WhatsApp pe aa jayegi — thoda delay hai."
  [No alarm in voice — merchant doesn't know anything failed]
  [WA text fallback immediately: booking details + key tax amounts in text format]
  [WA PDF sent when async job succeeds]

User experience: Seamless; text fallback covers the immediate need
Owner: Finance Engineering
```

---

## CATEGORY 5: VOICE & NLU

### EC-15: Merchant Speaks Entirely in Regional Language
**Frequency:** ~8%
**Detection:** STT detects non-Hindi/English language (Gujarati, Marathi, Tamil, Telugu)

```
System behavior:
  Polite redirect to Hindi/English
  If 2nd attempt also fails: WA fallback form

Agent script:
  "Maaf kijiye — main abhi sirf Hindi aur English samajh sakta hoon.
   Hindi mein batayein — kahan jaana hai?"
  [Single re-prompt; if still fails → WA form]
  [Log: regional_language_attempt = Gujarati/Tamil/etc. → product team signal]

User experience: Respectful; clear boundary; alternative offered
Owner: ML Engineering
```

---

### EC-16: Merchant Names an Unsupported City/Route
**Frequency:** ~4%
**Detection:** NLU extracts city that is not in supported airports list (35 cities in MVP)

```
System behavior:
  Check if city is served by any Indian airline (even if not in Paytm's API)
  If served but not in Paytm's inventory: honest disclosure + workaround

Agent script (city in India, not in our inventory):
  "Udaipur ke liye abhi direct service nahi hai humare paas.
   Nearest available: Jaipur (2 hr drive) — flight ₹4,500.
   Ya koi aur city?"

Agent script (city doesn't exist in system at all):
  "Yeh city mujhe nahi pata — seedha bataiye: Delhi, Mumbai, Bangalore?"
  [WA: City selector with top 15 cities for merchant's region]

User experience: Honest about limitations; always an alternative
Owner: Platform Engineering + Travel Partnerships
```

---

### EC-17: Merchant Changes Mind About Destination Mid-Flow
**Frequency:** ~5%
**Detection:** NLU detects new DESTINATION entity in Stage 2 or 3 after slots already filled

```
System behavior:
  Stage 2 (pre-confirmation): Accept change, re-run search with new destination
  Stage 3 (post-select, pre-payment): Accept change, reset to Stage 2 with new destination
  Stage 3 (payment initiated): Cancel pending payment, reset to Stage 2

Agent script (Stage 2):
  "Pune ke liye search karta hoon — ek second."
  [Reset slots, re-run Stage 2]

Agent script (Stage 3, payment not yet initiated):
  "Air India cancel karta hoon — Pune ke liye search karta hoon."
  [Reset to Stage 2]

User experience: No penalty for changing mind; conversation feels natural
Owner: Dialogue Manager (Engineering)
```

---

### EC-18: Extended Silence / Merchant Pauses for > 8 Seconds
**Frequency:** ~6%
**Detection:** VAD detects silence > 4 seconds (re-prompt), > 8 seconds (assume issue)

```
System behavior:
  4 sec silence → Re-prompt: "Kya aap wahan hain?"
  8 sec silence → Assume call quality issue → WA/SMS fallback
  
Agent script (4 sec):
  "Kya aap wahan hain? Koi help chahiye?"
  
Agent script (8 sec + no response):
  "Lagta hai call mein kuch issue aa gaya.
   WhatsApp pe link bhej raha hoon — wahan se continue kar sakte hain."
  [Send WA resume link; call continues in case merchant hears this]

User experience: Patient; doesn't assume merchant hung up prematurely
Owner: Dialogue Manager (Engineering)
```

---

## CATEGORY 6: SESSION & CONNECTIVITY

### EC-19: Call Drops After Payment Initiated But Before PNR
**Frequency:** ~1% (most critical edge case)
**Detection:** Call disconnect + payment_status = IN_FLIGHT (payment sent, PNR not yet returned)

```
System behavior:
  CRITICAL PATH: Must determine final payment/booking status before communicating to merchant
  
  Step 1: Wait 30 seconds (airline API response time)
  Step 2: Query booking status API
    → If CONFIRMED: send WA with PNR + invoice (happy path despite drop)
    → If FAILED: release credit hold; send WA draft link
    → If UNKNOWN > 90 sec: treat as failed; release hold; send WA resume link

WA message (CONFIRMED):
  "✅ Booking ho gayi! Call drop ho gayi thi, lekin booking confirm hai.
   PNR: {pnr} | Invoice: [Download]"

WA message (FAILED or UNKNOWN):
  "Call drop ho gayi. Payment process nahi hui.
   Booking resume karein: [link] (15 min mein expire hoga)"

User experience: Booking never lost due to call drop; crystal clear status
Owner: Platform Engineering + Finance Engineering (HIGHEST PRIORITY edge case)
```

---

### EC-20: Merchant on 2G / Poor Data Connection (WhatsApp Delays)
**Frequency:** ~10% in Tier-2 cities
**Detection:** WA delivery receipt not received within 30 seconds of send

```
System behavior:
  WA retry × 2 with 30-sec backoff
  If still undelivered: SMS fallback with plain-text booking details
  PDF invoice replaced with download link (lighter than PDF push)

Agent script:
  Nothing different — agent does not know about WA delivery status during call
  [Handled silently in background]

Post-call:
  If WA confirmed delivered → normal post-call summary
  If SMS used → SMS contains: PNR, flight details, download link for invoice

User experience: Transparent fallback; merchant gets their info via whichever channel works
Owner: Platform Engineering
```

---

### EC-21: Merchant Receives Two Proactive Calls for Same Trigger
**Frequency:** ~1% (race condition in scheduler)
**Detection:** Second outbound call to same merchant within 24hr for same trigger_id

```
System behavior:
  Second call attempt: check `last_outbound_call` in CRM
  If last_outbound_call < 24hr ago for same trigger: BLOCK call; don't dial

If call somehow goes through:
  Agent: "Namaste — lagta hai thodi pehle bhi call aayi thi.
   Agar travel book karna ho toh main help kar sakta hoon."
  [If merchant says "haan karo" → proceed with booking]
  [If merchant seems confused: apologize and hang up]

User experience: Prevent annoyance; CRM dedup is the fix; graceful if it slips
Owner: CRM / Outbound Scheduler
```

---

### EC-22: Merchant's Preferred WhatsApp Number Different from Call Number
**Frequency:** ~3%
**Detection:** Merchant explicitly says "Invoice mere doosre number pe bhejo" or states different WhatsApp number

```
System behavior:
  MVP: Cannot redirect to alternate number (security; KYC-linked only)
  
Agent script:
  "Abhi invoice sirf aapke registered number pe ja sakta hai.
   Aap apne Paytm account mein alternate number add kar sakte hain —
   uske baad automatically wahan jayega."
  [WA: Send link to add alternate number in Paytm Business app]
  
Post-MVP: Allow merchant to register alternate WA number in account settings

User experience: Honest limitation with a path forward
Owner: Product (future), Platform Engineering
```

---

### EC-23: Flight PNR Confirmed But E-Ticket Generation Delayed by Airline
**Frequency:** ~4%
**Detection:** Booking confirmed, PNR received, but airline e-ticket PDF not ready within 5 minutes

```
System behavior:
  Booking: CONFIRMED (PNR exists, legally binding)
  E-ticket: async — poll airline API every 2 minutes for up to 30 minutes
  
Agent script:
  "Booking confirm ho gayi! PNR hai {pnr}.
   E-ticket 5-10 minute mein WhatsApp pe aa jayegi — airline se generate ho rahi hai."
  [WA: Send PNR + GST invoice now; e-ticket separately when ready]
  [If e-ticket not ready in 30 min: escalate to Travel Ops team]

User experience: PNR is sufficient to board; merchant knows e-ticket is coming
Owner: Platform Engineering + Travel Ops
```

---

## Priority Matrix

```
SEVERITY vs. FREQUENCY

CRITICAL (Must fix before launch):
  EC-10 (Payment gateway timeout)      — Financial risk
  EC-19 (Call drop after payment)      — Highest stakes; direct financial harm
  EC-11 (Double booking)               — Financial risk

HIGH (Fix in Week −2):
  EC-02 (Suspended account)            — Customer trust
  EC-08 (Credit insufficient)          — 15% frequency; impacts north star
  EC-04 (No flights)                   — 12% frequency; core flow
  EC-05 (Sold out mid-booking)         — 3% but high merchant impact

MEDIUM (Fix before soft launch):
  EC-06 (Price change)                 — 5% but trust-sensitive
  EC-09 (Credit risk hold)             — 5% but financially sensitive
  EC-12 (GSTIN suspended)              — 3% but compliance-sensitive
  EC-13 (Business name mismatch)       — 5% but legal invoice accuracy
  EC-17 (Destination change)           — 5% but NLU/flow reset complexity

STANDARD (Fix before full pilot):
  All remaining EC-01, EC-03, EC-07, EC-14–EC-16, EC-18, EC-20–EC-23
```

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/04-mvp-specification*
