# Fallback Architecture
**15+ Failure Modes · Detection · Recovery Paths · User Experience**
*Paytm Merchant Travel Desk | 03-ai-architecture*

---

## Core Design Principle

> **"Never lose the merchant."**

Every failure mode — no matter how deep in the stack — has a defined recovery path. The merchant either stays in the flow, gets handed to a human, or receives an async link to complete their booking. There is no dead end.

**Failure philosophy:**
- Fail **gracefully** — merchant should not feel the system broke
- Fail **forward** — each recovery path moves toward booking completion
- Fail **transparently** — merchant knows what's happening, never left in silence
- **Never retry silently** — if something fails, acknowledge it in < 4 seconds

---

## Failure Mode Registry

### Category 1: Voice / STT Failures

---

#### FM-01: STT Confidence Below Threshold
**Trigger:** Speech-to-text returns confidence score < 0.80
**Frequency:** ~15% of utterances in noisy environments

```
Detection:  google.cloud.speech.SpeechRecognitionResult.confidence < 0.80
            OR word_error_rate estimated > 25%

Recovery path:
  Attempt 1:
    Agent: "Maaf kijiye, thoda shor aa raha hai.
            Phir se bataiye — kahan jaana hai?"
    [Re-listen with extended silence_timeout: 2.0 sec]

  Attempt 2 (if confidence still < 0.80):
    Agent: "Aawaz sahi nahi aa rahi. Screen pe options dikha raha hoon —
            WhatsApp par tap karke choose karein."
    [Send WhatsApp interactive form for current stage]
    [Continue call — merchant can respond via WA tap OR voice]

  Attempt 3 (if WA also fails or no response in 60 sec):
    Agent: "Main aapko SMS bhej raha hoon booking link ke saath."
    [Send SMS with deep link to resume booking]
    [Call can end or continue — merchant's choice]

User experience: "Thoda shor hai" feels natural, not like system failure
Recovery SLA: Merchant back on track within 20 sec
```

---

#### FM-02: Background Noise Overload (SNR < 6 dB)
**Trigger:** Signal-to-noise ratio below usable threshold
**Frequency:** ~5% of calls (outdoor calls, busy shops during peak hours)

```
Detection:  Real-time audio SNR monitoring
            SNR < 6 dB for > 3 consecutive seconds

Recovery path:
  Immediate:
    Agent: "Bahut shor hai abhi. Main aapko WhatsApp link bhejta hoon —
            wahan se booking complete kar sakte hain, ya baad mein call kariye."
    [Send WA link: current stage deep link]
    [Agent stays on line for 10 sec in case noise clears]

  If no response after 10 sec:
    [Graceful call end]
    WA message: "Aapka booking draft save hai. Complete karein: [link]"
    SMS backup: "Paytm Travel: Complete your booking [short URL]"

User experience: Proactive offer to switch channels — not a failure
```

---

#### FM-03: Merchant Speaks in Unsupported Language
**Trigger:** NLU detects non-Hindi/English input
**Frequency:** ~8% (Gujarati, Tamil, Marathi dominant regions)

```
Detection:  STT language detection returns language_code != hi-IN or en-IN
            OR NLU intent confidence < 0.40 (misparse of regional input)

Recovery path:
  Agent: "Maaf kijiye — main sirf Hindi aur English samajh sakta hoon abhi.
          Hindi ya English mein batayein — kahan jaana hai?"
  [Single prompt, then listen again]

  If 2nd attempt also fails:
    [Push to WhatsApp form with simple text inputs]
    Agent: "Screen pe form fill karein — wahan choose kar sakte hain."

  Post-MVP: Add Gujarati, Marathi, Tamil support
  Roadmap entry: Created if 3+ merchants attempt regional language in pilot

User experience: Polite boundary, not a hard error
```

---

#### FM-04: Merchant Interrupts Agent Mid-Speech
**Trigger:** Voice activity detection fires while agent is speaking
**Frequency:** ~15% of calls (natural in conversation)

```
Detection:  Real-time voice activity detection (VAD)
            Agent audio playing AND merchant speaking simultaneously

Recovery path:
  IMMEDIATE: Stop agent audio playback
  Listen for merchant's utterance
  Process new input as if agent had finished speaking

  This is NOT a failure — it's natural conversation
  Design goal: Feel like talking to a person, not an IVR

User experience: Seamless — merchant doesn't notice anything
```

---

### Category 2: NLU / Understanding Failures

---

#### FM-05: Entity Extraction Failure — Destination Not Understood
**Trigger:** NLU cannot extract a valid destination city
**Frequency:** ~8% (unusual city names, very noisy audio)

```
Detection:  destination entity missing after utterance
            OR extracted IATA code not in supported_airports list

Recovery path:
  Attempt 1:
    Agent: "Aap kahan jaana chahte hain?
            Seedha city ka naam bataiye — jaise 'Delhi' ya 'Mumbai'."
    [Re-attempt slot extraction]

  Attempt 2 (if still missing):
    Agent: "Screen pe city choose karein — main dikha raha hoon."
    [Send WA: city picker with search — top 20 cities by merchant's region]

  Attempt 3 (if no WA response in 60 sec):
    Agent: "Koi baat nahi. Jab ready hon toh call kariye."
    [Graceful exit + save partial session]

Supported airports list: 35 Indian cities (all major domestic routes)
```

---

#### FM-06: Ambiguous Date / Time
**Trigger:** Date entity extracted but ambiguous (e.g., "15 tarikh" without month)
**Frequency:** ~6%

```
Detection:  date.confidence < 0.80
            OR date resolves to a past date
            OR "15 tarikh" without month context

Recovery path:
  Disambiguation prompt (natural, specific):
    Agent: "Kaunsa 15? April 15 — kal, ya koi aur date?"
           [For "kal" on a Sunday night:]
           "Kal matlab Monday, April 15 — sahi hai?"

  If date resolves to past:
    Agent: "April 8 toh guzar gayi — kya aap April 15 kal ke liye book karna chahte hain?"

User experience: Confirmation feels like natural clarification, not error
```

---

#### FM-07: Intent Conflict (Merchant Changes Mind Mid-Stage)
**Trigger:** Merchant expresses a different intent than current stage
**Frequency:** ~5%

```
Examples:
  [Mid-search, Stage 2]: "Actually, train lena tha."
  [Mid-confirm, Stage 3]: "Rehne do, main baad mein book karunga."
  [Mid-confirm, Stage 3]: "Actually, Mumbai nahi, Pune jaana hai."

Recovery paths:
  CANCEL_INTENT:
    Agent: "Koi baat nahi. Jab chahiye tab call kariye — Paytm Business Travel."
    [Save partial session 24hr] [Clean exit]

  CHANGE_DESTINATION (Stage 2 only):
    Agent: "Pune ke liye search karta hoon — ek second."
    [Reset search slots, re-run Stage 2 with new destination]

  CHANGE_DESTINATION (Stage 3 — after payment initiated):
    Agent: "Payment rokta hoon. Pune ke liye naya search karta hoon."
    [Cancel pending payment] [Reset to Stage 2]

  TRAIN_REQUEST:
    Agent: "Train booking abhi available nahi hai. Flight book karein?
            Ya call kariye jab train feature launch ho."
    [Log: train_request_declined → product feedback]
```

---

### Category 3: API / Backend Failures

---

#### FM-08: Flight Search API Timeout (> 5 seconds)
**Trigger:** Paytm Travel API response time exceeds 5 seconds
**Frequency:** ~3% (peak load periods)

```
Detection:  api_latency > 5000ms OR connection_timeout
            Monitored via Prometheus gauge: travel_api_latency_p95

Recovery path:
  [0–5 sec]: Agent fills time naturally:
    "Ek second — flights dhoond raha hoon..."
    [WA shows loading state: "⏳ Searching..."]

  [5 sec timeout]:
    Check cache: if cached_results[route][date] age < 15 min:
      → Serve cached results with flag: "Ye prices 10 min purane hain, confirm karte waqt refresh hoga"
    
    If no cache:
      Agent: "Search mein thoda time lag raha hai.
              Main 2 minute baad call back karta hoon —
              ya WhatsApp pe link bhejta hoon jab ready ho."
      [WA: "Flights search ho raha hai. Ready hone par notify karenge: [link]"]

  [Async]: Complete search in background → WA push when results ready (< 5 min)

User experience: 5-sec wait feels normal; callback option is transparent
```

---

#### FM-09: No Flights Available on Route/Date
**Trigger:** API returns empty results set
**Frequency:** ~12%

```
Detection:  flights.length == 0 in API response
            Distinguish: true_no_flights vs API_error (check status code)

Recovery paths:
  No direct flights:
    [Check connecting flights API]
    Agent: "Kanpur se Bangalore direct nahi hai kal.
            Connecting flight hai via Delhi — 5 ghante total, ₹6,200.
            Dikhaun? Ya doosra date lein?"

  Truly no service on route:
    [Check alternate nearby airports]
    Agent: "Is route pe flight nahi hai.
            Nearest option: Lucknow se Bangalore — 40 min drive from Kanpur.
            Woh dekhen?"

  No flights on date but available nearby dates:
    Agent: "Kal nahi hai. 16 April aur 17 April pe hai —
            kaunsi date prefer karenge?"

  No service at all (very rare):
    Agent: "Is route pe abhi service nahi hai.
            Humara team note kar lega — zaroorat ho toh call kariye."
    [Log route gap → product team for inventory expansion]

User experience: Always offer an alternative, never just "not available"
```

---

#### FM-10: Credit Payment Declined
**Trigger:** Paytm Credit API returns DECLINED status
**Frequency:** ~5%

```
Detection:  credit_api.response.status == "DECLINED"
            reason: "LIMIT_EXCEEDED" | "RISK_HOLD" | "PAYMENT_OVERDUE"

Recovery paths by reason:

  LIMIT_EXCEEDED:
    Agent: "Credit limit ₹{gap} kam pad raha hai is booking ke liye.
            Wallet mein ₹{wallet_balance} hai — wallet se karein?"
    [Offer wallet as immediate fallback]

  RISK_HOLD (temporary):
    Agent: "Credit abhi temporary hold pe hai.
            Wallet se book karein — credit theek hote hi automatically reset hoga."
    [WA: "Your credit will be available again within 24–48 hours."]

  PAYMENT_OVERDUE:
    Agent: "Credit mein kuch pending payment hai.
            Wallet se book karein, ya support se baat karein credit ke liye."
    [Do NOT mention overdue in detail — sensitive financial info]
    [CRM: flag for credit team follow-up post-call]

  All paths: wallet_fallback if wallet_balance >= flight_price
  If wallet also insufficient:
    Agent: "Abhi payment ho nahi pa raha. Booking draft save karta hoon —
            payment solve hone ke baad WhatsApp link se complete kar sakte hain."
    [WA: Draft saved 24hr + payment options link]

User experience: Sensitive handling — never say "declined" bluntly
```

---

#### FM-11: Wallet Debit Failure
**Trigger:** Wallet API returns error (insufficient balance or technical)
**Frequency:** ~2%

```
Detection:  wallet_api.response.status != "SUCCESS"

Recovery path:
  Insufficient balance:
    Agent: "Wallet mein balance kam hai — ₹{balance} hai, ₹{price} chahiye.
            Wallet top-up karein ya credit use karein?"
    [WA: [Top up wallet →] [Use Business Credit →]]

  Technical failure (retry 1x first):
    [Silent retry in background]
    If retry fails:
    Agent: "Payment process mein thodi problem aayi.
            2 minute mein dobara try karenge — ya support call karein."
    [Auto-retry after 2 min via background job]
    [WA notification when successful]
```

---

#### FM-12: Flight Sold Out Mid-Booking
**Trigger:** Booking API returns SEAT_NOT_AVAILABLE after user confirmed
**Frequency:** ~3%

```
Detection:  booking_api.response.error_code == "SEAT_NOT_AVAILABLE"
            Timing: Occurs between Stage 2 (search) and Stage 3 (payment)
            due to concurrent bookings

Recovery path:
  Agent: "Oh no — yeh seat abhi kisi aur ne le li.
          [Flight 2 detail] abhi bhi available hai — ₹{price2}.
          Ya fresh search karein?"

  If Flight 2 still available:
    [Pre-fetch Flight 2 availability before announcing]
    → Offer Flight 2 directly without new search

  If both sold out:
    [Re-run search API in background]
    Agent: "Dono options chali gayein. Fresh results aa gaye —
            [new option 1] ya [new option 2]?"

User experience: Fast pivot — merchant doesn't go back to "start"
```

---

#### FM-13: Price Changed Between Search and Booking
**Trigger:** Price validation at booking step shows > 5% increase from displayed price
**Frequency:** ~5%

```
Detection:  abs(booking_price - search_price) / search_price > 0.05
            Checked via GET /flights/{id}/price before POST /booking

Recovery path:
  Small increase (< 10%):
    Agent: "Price ₹{old} se ₹{new} ho gayi — ₹{diff} zyada.
            Confirm karein?"
    [Merchant can accept or cancel]

  Large increase (> 10%):
    Agent: "Price ₹{old} se ₹{new} ho gayi — kaafi change hua.
            [Alt flight] abhi bhi ₹{alt_price} pe stable hai — woh lein?"
    [Suggest stable alternative proactively]

User experience: Transparent price update — trust through honesty
```

---

#### FM-14: GST Validation Failure — GSTIN Suspended
**Trigger:** GST Portal API returns GSTIN status ≠ ACTIVE
**Frequency:** ~3%

```
Detection:  gst_api.validate.status in ["SUSPENDED", "CANCELLED", "NOT_FOUND"]

Recovery path:
  Booking already confirmed (PNR generated):
    → COMPLETE booking without invoice (payment already taken)
    Agent: "Booking ho gayi! PNR hai {pnr}.
            GST invoice mein ek issue hai — GSTIN update ke baad generate hoga.
            WhatsApp pe link bhej raha hoon."
    [WA: GST update card + resume invoice link]
    [Save: pending_invoice job (7-day TTL) — auto-generates when GSTIN fixed]

  Booking not yet confirmed:
    Agent: "Booking ke saath ek issue hai — GST number check karna hai.
            Draft save karta hoon. 5 minute mein update karein, phir complete karenge."
    [WA: GSTIN update link + draft resume link]
    [Hold flight availability for 15 min via travel API]

  User experience: Booking isn't blocked for GSTIN issue (that would be wrong UX)
  Rule: Never block a confirmed PNR over an invoice problem
```

---

#### FM-15: Invoice PDF Generation Failure
**Trigger:** PDF rendering service times out or returns error
**Frequency:** ~1%

```
Detection:  invoice_api.response.status == "FAILED"
            OR pdf_generation_time > 10 sec

Recovery path:
  Background retry (transparent to merchant):
    [Queue invoice generation job with 3 retries, exponential backoff]
    Agent: "Invoice 5 minute mein WhatsApp pe aa jayegi."
    [WA notification when PDF ready]

  If all retries fail (rare):
    [Generate simplified text invoice as WhatsApp message fallback]
    [Flag for manual invoice generation by finance team, SLA: 2 hours]
    [Merchant notified via WA when done]

User experience: "5 minute mein aayegi" sets expectation, not an error
```

---

#### FM-16: WhatsApp Message Delivery Failure
**Trigger:** WhatsApp Business API returns non-delivered status
**Frequency:** ~2%

```
Detection:  wa_webhook.status in ["failed", "undelivered"]
            Reasons: WhatsApp not installed, number mismatch, API error

Recovery path:
  [Auto-retry 2x with 10-sec backoff]
  [Check: is phone registered on WhatsApp?]

  If WhatsApp verified but delivery failed:
    [Retry via WA API]
    [If still failed: send compressed SMS with text version of booking details]

  If phone not on WhatsApp:
    [Fallback to SMS for all companion messages in this session]
    [Flag: merchant_wa_status = NOT_AVAILABLE]
    [Note in CRM — future calls use SMS flow]

  For invoice/e-ticket (documents):
    [Send download link via SMS instead of WA document message]
    Agent: "WhatsApp nahi gaya — SMS pe link bhej raha hoon."

User experience: Seamless switch; merchant gets their documents regardless
```

---

#### FM-17: Call Drops Mid-Booking
**Trigger:** Telephony session terminates unexpectedly
**Frequency:** ~3%

```
Detection:  call_status == "DISCONNECTED" AND stage != "SUMMARY"
            Distinguish: merchant hung up vs. network drop

Recovery path:
  [IMMEDIATE on disconnect]:
    Save full session state to DB (already persisted on every stage transition)
    Determine last completed action:
      - Pre-payment: booking not initiated → WA/SMS with resume link
      - Post-payment-initiated: check booking API status
        - If CONFIRMED: send PNR + invoice to WA/SMS
        - If PENDING: wait 30 sec → check again → send result
        - If FAILED: release hold, offer retry link

  WA message (sends < 10 sec after drop):
    "📲 Lagta hai call drop ho gayi.
    Aapki [Flight] booking:
    ₹{price} · {route} · {date}
    Seats {X} min ke liye hold hain.
    [▶️ Complete Booking]"

  SMS backup (if WA fails):
    "Paytm Travel: Booking pending. Complete: [short URL] (valid 15 min)"

  [Auto-callback after 2 min if merchant hasn't resumed via link]:
    Agent: "Namaste {name} — call drop ho gayi. Booking continue karein?"
    [Resume from last stage]

User experience: merchant never loses a booking due to call drop
```

---

#### FM-18: Human Agent Queue Full
**Trigger:** Human agent capacity exhausted during escalation
**Frequency:** ~5% during peak hours

```
Detection:  human_agent_queue.available_agents == 0
            OR estimated_wait_time > 3 min

Recovery path:
  Transparent wait option:
    Agent: "Specialist se connect karne mein {wait_time} minute lagenge.
            Wait karein ya callback lein?"

  Callback option (preferred):
    Agent: "Main aapka number note karta hoon —
            {wait_time} minute mein specialist call karega."
    [Schedule callback in queue]
    [WA: "Callback scheduled for ~{time}. Booking reference: {id}"]

  Immediate self-serve for simple queries:
    [Detect if query is FAQ-answerable]
    Agent: "Ye common question hai — abhi bata sakta hoon..."
    [Handle without human if possible]

User experience: Never "please hold" indefinitely — always give control
```

---

## Failure Mode Summary Table

| ID | Failure | Frequency | First Recovery | Ultimate Recovery |
|---|---|---|---|---|
| FM-01 | STT confidence low | 15% | Retry once + rephrase | WA form → SMS link |
| FM-02 | Background noise overload | 5% | WA link + stay on call | SMS booking link |
| FM-03 | Unsupported language | 8% | Hindi/English prompt | WA form |
| FM-04 | Merchant interrupts | 15% | Stop + listen (normal) | N/A — not a failure |
| FM-05 | Entity extraction fails | 8% | Ask explicitly | WA city picker |
| FM-06 | Ambiguous date | 6% | Disambiguation prompt | WA date picker |
| FM-07 | Intent conflict | 5% | Adapt to new intent | Clean exit + save |
| FM-08 | Flight API timeout | 3% | Wait + serve cached | Async notify + WA |
| FM-09 | No flights found | 12% | Offer alternatives | Different date/route |
| FM-10 | Credit declined | 5% | Wallet fallback | Draft save + WA |
| FM-11 | Wallet debit failure | 2% | Retry 1x | Top-up link + WA |
| FM-12 | Seat sold out | 3% | Offer Flight 2 | Fresh search |
| FM-13 | Price changed | 5% | Transparent update | Offer stable alt |
| FM-14 | GSTIN suspended | 3% | Complete booking, invoice async | WA update link |
| FM-15 | Invoice PDF fails | 1% | Background retry | Text invoice fallback |
| FM-16 | WA delivery fails | 2% | WA retry | SMS fallback |
| FM-17 | Call drops | 3% | WA resume link | Auto-callback |
| FM-18 | Human queue full | 5% | Wait estimate | Callback scheduling |

---

## Recovery Channel Hierarchy

```
VOICE (primary)
    ↓ if voice fails
WHATSAPP INTERACTIVE (secondary)
    ↓ if WA fails or merchant not on WA
SMS WITH LINK (tertiary)
    ↓ if SMS fails (rare)
EMAIL (quaternary — from KYC email, if on file)
    ↓ for complex issues
HUMAN AGENT CALLBACK
```

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/03-ai-architecture*
