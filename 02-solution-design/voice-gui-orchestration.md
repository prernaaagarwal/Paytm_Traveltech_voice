# Voice–GUI Orchestration
**When Voice, When Screen — Design Decision Framework**
*Paytm Merchant Travel Desk | 02-solution-design*

---

## The Core Principle

> **"Voice is the primary interface. Screen is the verification layer."**

This is the **inverse** of a typical mobile app experience, where the screen is primary and voice is bolted on as an assistant. Here, a merchant can complete an entire booking without ever looking at their phone — but the screen is there when they need to verify, compare, or download.

This design choice emerges directly from user research:
- *"I can't stop to tap through 5 screens while I'm serving customers"* — M08 (Mohan, Surat)
- *"I just want to say 'book me to Delhi tomorrow' and be done"* — M01 (Ramesh, Kanpur)
- *"The WhatsApp message is useful after the call — I can share the invoice with my CA"* — M02 (Priya, Surat)

---

## The Orchestration Decision Matrix

| Task | Interface | Why | Confidence |
|---|---|---|---|
| Initial intent capture | 🎤 **Voice** | Fastest for single-intent: "Book me to Mumbai" = 1 utterance vs 4 taps | High |
| Missing slot collection | 🎤 **Voice** | Back-and-forth is natural in speech, clunky in UI | High |
| Flight option comparison | 📱 **WhatsApp screen** | Comparing 2 options requires spatial layout (price, time, airline) — hard to hold in working memory | High |
| Flight selection | 🎤 **Voice OR screen tap** | Both accepted; voice faster, screen available as backup | High |
| Payment method choice | 🎤 **Voice + screen display** | Voice for decision speed, screen for balance verification before committing | High |
| Passenger details entry | 🤖 **Auto-filled (no UI)** | Pre-populated from KYC — merchant doesn't see or touch it | High |
| GSTIN entry | 🤖 **Auto-filled (no UI)** | Same — zero manual entry, zero errors | High |
| Booking confirmation | 🎤 **Voice** | One verbal "haan" = confirm. No button tap needed | High |
| Invoice download | 📱 **WhatsApp screen** | PDF must be tappable/downloadable; can't transmit file via audio | High |
| E-ticket download | 📱 **WhatsApp screen** | Same — digital artifact needs visual delivery | High |
| Cancellation request | 📱 **WhatsApp self-serve** | Async task; doesn't need a live call | Medium |
| Return booking | 📱 **WhatsApp deep link** | Can initiate a new call from the tap — hybrid trigger | Medium |
| Post-trip expense close | 🎤 **Future call flow** | Post-MVP; separate voice interaction | Low |

---

## Interface Assignment: Stage by Stage

### Stage 1: Identity & Intent

```
PRIMARY:  🎤 Voice
SCREEN:   📲 Companion send (async, not required)

Voice does:   Greet, authenticate by phone, ask "Where to?"
Screen does:  Background send of "Your assistant is live" message
              Merchant need not look at screen at all in Stage 1
```

**Why not screen-first here?**
Showing a "search form" before the agent speaks breaks the conversational paradigm. The merchant called — they expect a voice response, not a ping to open WhatsApp.

---

### Stage 2: Trip Search

```
PRIMARY:  🎤 Voice (slot extraction)
SECONDARY: 📲 Screen (options comparison)

Voice does:   Extract origin, destination, date via NLU
              Search API in background (silent 3-sec pause)
              Read 2 options: "IndiGo 8 AM ₹4,200 ya Air India 9:30 ₹3,800"
Screen does:  Show flight cards simultaneously — visual backup
              Merchant can select via tap OR voice
```

**The 2-option rule:**
Voice can present at most **2 options** — beyond that, working memory overloads and merchants default to "the first one" regardless of fit. Screen cards allow comparison for detail-sensitive decisions.

**Critical design detail — screen sends BEFORE voice reads:**
```
API returns results
  → Send WhatsApp card (200ms)
  → Agent begins reading options (500ms)

By the time voice finishes reading, screen card is already rendered.
Merchant can glance or continue listening — their choice.
```

---

### Stage 3: Confirmation

```
PRIMARY:  🎤 Voice (payment decision + verbal confirm)
SECONDARY: 📲 Screen (balance verification + booking details)

Voice does:   "Credit se karein ya wallet se?" → capture choice
              "Air India 9:30, ₹3,800, credit se — confirm karein?"
              Capture verbal "haan" → trigger booking
Screen does:  Show current credit balance (₹47,000) and wallet balance
              Auto-fill passenger name + GSTIN (visible, editable)
              Show [Confirm Booking] button as backup to voice confirm
```

**Why screen for balance display?**
High-stakes financial commitment. Merchant hears "₹3,800" but needs to trust the system. Seeing "₹47,000 credit available" on screen before committing builds confidence. Cognitive dissonance if voice says "enough credit" but merchant can't verify.

**Dual-confirm design:**
```
Verbal "haan" → triggers booking
Screen tap [Confirm] → also triggers booking
First signal wins → idempotency key blocks duplicate
```

---

### Stage 4: Invoice & Documents

```
PRIMARY:  🎤 Voice (PNR readout, name confirmation)
SECONDARY: 📲 Screen (PDF delivery — mandatory)

Voice does:   Read PNR: "Aapka PNR hai 6E2F4G"
              Confirm business name: "Kumar General Store — sahi hai?"
              Confirm invoice sent: "WhatsApp par bhej diya"
Screen does:  Deliver GST invoice PDF (cannot be done via voice)
              Deliver e-ticket PDF
              Show booking confirmation card
```

**Why screen is mandatory here (only stage):**
PDFs and downloadable files cannot be transmitted via audio. The invoice delivery is a non-negotiable screen touchpoint — but the merchant doesn't need to act; it just arrives in WhatsApp like any other message.

**PNR read design:**
```
PNR "6E2F4G" → read as: "6, E, 2, F, 4, G" (character by character)
NOT: "six-E-two-F-four-G" (confusion between letters and phonetics)
WhatsApp also shows PNR in text — merchant can verify visually
```

---

### Stage 5: Summary & Exit

```
PRIMARY:  🎤 Voice (summary + goodbye)
SECONDARY: 📲 Screen (post-booking action card)

Voice does:   Read booking summary in 2 sentences
              "Safe travels" — clean exit
Screen does:  Show persistent booking card with action buttons
              [Change/Cancel] [Return Flight] [Call Support] [Rate]
```

**Why no voice for the action menu?**
Post-booking actions (cancel, add return) are async — they don't need to happen on this call. Listing them verbally ("If you want to cancel, press 1; for return flight, press 2...") is IVR-style and kills UX. Screen handles it silently.

---

## When to Override Voice → Screen

The voice-first model has explicit **fallback triggers** that push to screen:

### Trigger 1: STT Confidence Below Threshold
```
if stt_confidence < 0.80:
    retry_count += 1
    if retry_count == 1:
        agent: "Maaf kijiye, phir se bataiye?"
    if retry_count == 2:
        agent: "Thoda shor lag raha hai — screen pe options dikhata hoon"
        → Send WhatsApp form with text input fields
```

### Trigger 2: NLU Slot Extraction Fails Twice
```
if nlu_missing_slots AND retry > 1:
    agent: "Screen par choose karein — asaan rahega"
    → Send WhatsApp interactive card:
       [Origin dropdown] [Destination dropdown] [Date picker]
```

### Trigger 3: Comparison Requested
```
if merchant_utterance contains compare/difference/difference/price:
    → WhatsApp card already showing, prompt: "Screen pe dono options hain"
    → Don't re-read options by voice; direct to screen
```

### Trigger 4: Call Quality Degradation
```
if background_noise_level > threshold AND retry > 1:
    agent: "Bahut shor hai. SMS pe booking link bhejta hoon"
    → SMS: "Complete your booking: [deep link]"
    → Call can continue OR merchant can switch to SMS flow
```

---

## Screen-Only Flows (No Active Call)

Some journeys are **fully asynchronous** — no live call needed:

| Trigger | Screen Flow | Voice Involvement |
|---|---|---|
| Merchant taps [Return Flight] in WA | Pre-fills origin/dest from last booking, opens date picker | None; or merchant can initiate a new call |
| Cancellation request | Cancellation confirmation card with T&C, refund estimate | Only if merchant explicitly calls back |
| Invoice re-send | One-tap "Re-send invoice" from WA booking card | None |
| Credit top-up nudge | WA message: "Your credit usage is high. Apply for increase?" | None |
| Flight schedule change | WA notification from airline API | None (async notification) |

---

## Technical Architecture: Voice–Screen Sync

```
┌─────────────────────────────────────────────────────────┐
│                    CALL SESSION                         │
│                                                         │
│  Agent Speech  ──→  TTS Engine  ──→  Merchant's Ear    │
│  Merchant Speech  ←── STT Engine  ←── Merchant's Mouth │
│                                                         │
│  [Session State Manager]                                │
│    - Current stage (1–5)                               │
│    - Extracted slots (origin, dest, date, payment)      │
│    - Booking state (searching / confirming / done)     │
│    - WhatsApp thread ID (for sync)                      │
└────────────────────────┬────────────────────────────────┘
                         │ Events (< 50ms)
                         ▼
┌─────────────────────────────────────────────────────────┐
│              WHATSAPP COMPANION MANAGER                 │
│                                                         │
│  Receives: stage_change, options_ready, booking_done    │
│  Sends:    WA card templates, PDFs, action buttons      │
│                                                         │
│  Key rule: WA send BEFORE or SIMULTANEOUS with voice    │
│  Never: WA sends AFTER voice (screen lags = broken UX)  │
└─────────────────────────────────────────────────────────┘
```

**Synchronization rule:**
Screen updates must lead or match voice by at most 200ms. A merchant who glances at their phone mid-sentence should see the information being spoken, not the previous state.

---

## Anti-Patterns to Avoid

| Anti-Pattern | Why It's Wrong | Correct Approach |
|---|---|---|
| "Press 1 for Hindi, press 2 for English" | IVR stigma; kills trust in first 5 seconds | Detect language from first utterance, code-switch dynamically |
| Presenting 3+ options by voice | Working memory overload → merchant picks "the first one" | 2 options by voice max; full list on screen |
| Showing a screen form before the agent speaks | Breaks conversational paradigm — they called, expect voice | Agent speaks first; screen sends async |
| Repeating all details in the WA card that were just read by voice | Redundant, clutters WA thread | Screen shows structured summary; voice reads key highlights only |
| "Your booking is being processed, please wait" (dead air) | > 4 sec silence feels like call dropped | Agent fills time: "₹3,800 credit se process ho raha hai, ek second..." |
| Asking merchant to tap buttons during the call | Forces multitask; they may be driving/serving customers | All booking actions doable by voice; screen is supplementary |

---

## Accessibility Considerations

| User Constraint | Accommodation |
|---|---|
| Merchant driving / can't look at phone | 100% bookable by voice alone; screen confirmation not required |
| Low smartphone literacy | WA cards use simple icons + large text; no complex navigation |
| Hearing impairment | Full WhatsApp text flow available; WA interactive cards replace voice for all stages |
| Noisy shop environment | Fallback SMS flow with booking deep link; WA text form for slot input |
| No WhatsApp installed (< 5% of merchants) | SMS with plain-text booking link; core voice flow unaffected |

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/02-solution-design*
