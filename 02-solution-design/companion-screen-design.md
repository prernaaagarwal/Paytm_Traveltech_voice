# Companion Screen Design
**WhatsApp UX Specification | All Stages + States**
*Paytm Merchant Travel Desk | 02-solution-design*

---

## Design Philosophy

The WhatsApp companion screen is **not the primary interface** — it is the verification and delivery layer that supports the voice call. Every design decision flows from one rule:

> **The screen should be useful if you glance at it. It should never be required to complete the booking.**

This means:
- All booking actions are doable by voice alone
- The screen adds value for comparison, verification, and document delivery
- No screen element should require a long press, scroll, or learning curve
- Designed for a merchant glancing at their phone mid-call, possibly in a noisy shop

---

## Technology: WhatsApp Business API

### Why WhatsApp (Not a Dedicated App)
| Criterion | WhatsApp | Dedicated App |
|---|---|---|
| Install friction | Zero (95% of merchants already use it) | High (requires 80MB download, app store, account creation) |
| Familiarity | Very high — merchants use daily | Low — new UI to learn |
| Rich media support | Cards, PDFs, quick-reply buttons | Yes, but requires installation |
| Business messaging API | Native | Custom |
| Offline reference | Messages stay in chat history | Requires app launch |
| Push notification | Native, high open rate | Requires push permission grant |
| Data usage | Low (cards = ~5KB) | Higher (full app assets) |

### WhatsApp Business API Capabilities Used
```
Message types used:
  ✅ Text messages (confirmations, PNR readout)
  ✅ Interactive buttons (quick-reply, CTA buttons)
  ✅ List messages (flight options, payment choice)
  ✅ Document messages (GST invoice PDF, e-ticket PDF)
  ✅ Template messages (booking confirmation, summary)

Message types NOT used in MVP:
  ❌ Location sharing (not relevant)
  ❌ Image carousels (airline logos — post-MVP visual upgrade)
  ❌ Payment within WhatsApp (uses voice/Paytm app for payment)
```

---

## Screen States: Stage by Stage

### State 0: Pre-Call (Not Yet Called)
*No screen shown. No proactive message before call is established.*

---

### State 1: Identity Confirmed (Stage 1)
*Sent: Immediately on call pickup, within 3 seconds of agent greeting.*

```
┌────────────────────────────────────────┐
│  🛫  Paytm Business Travel             │
│  ────────────────────────────────────  │
│                                        │
│  Namaste Ramesh! ✈️                    │
│                                        │
│  Your booking assistant is live        │
│  right now on the call.                │
│                                        │
│  Listen for flight options —           │
│  I'll show them here too.              │
│                                        │
│  ● Booking in progress...              │
│                                        │
└────────────────────────────────────────┘
```

**Spec:**
- Message type: `text` (template)
- Variables: `{merchant_first_name}`
- Purpose: Anchors merchant to the companion experience; sets expectation
- No action required from merchant

---

### State 2A: Searching for Flights (Stage 2 — Loading)
*Sent: When flight API call fires (before results return).*

```
┌────────────────────────────────────────┐
│  ✈️  Searching flights...              │
│  ────────────────────────────────────  │
│                                        │
│  Kanpur → Delhi                        │
│  Tomorrow · Morning                    │
│                                        │
│  ⏳  Finding best options...           │
│                                        │
└────────────────────────────────────────┘
```

**Spec:**
- Sent at same time as API call fires
- Confirms NLU understood the route correctly (merchant can correct if wrong)
- Loading state shows within 200ms of slot confirmation
- Replaced by State 2B when API returns (< 5 sec)

---

### State 2B: Flight Options (Stage 2 — Results)
*Sent: When API returns, simultaneously with agent reading options.*

```
┌────────────────────────────────────────┐
│  ✈️  Kanpur → Delhi                    │
│  Tomorrow · Morning · Economy          │
│  ────────────────────────────────────  │
│                                        │
│  ┌──────────────────────────────────┐  │
│  │  IndiGo  6E-123                  │  │
│  │  08:00 → 10:30  (2h 30m)        │  │
│  │  Non-stop                        │  │
│  │  ₹4,200                          │  │
│  │                  [Select →]      │  │
│  └──────────────────────────────────┘  │
│                                        │
│  ┌──────────────────────────────────┐  │
│  │  Air India  AI-456               │  │
│  │  09:30 → 12:00  (2h 30m)        │  │
│  │  Non-stop                        │  │
│  │  ₹3,800                          │  │
│  │                  [Select →]      │  │
│  └──────────────────────────────────┘  │
│                                        │
│  🎤  Or tell me your choice            │
└────────────────────────────────────────┘
```

**Spec:**
- Message type: `interactive` (list or button — 2 options = buttons)
- Card 1 button: CTA → `select_flight_1`
- Card 2 button: CTA → `select_flight_2`
- Both tap events immediately confirm that flight and trigger Stage 3
- Tapping is equivalent to merchant saying the flight name — same flow
- Flight cards show: airline name + flight number, departure time, duration, price
- Do NOT show: seat availability count, cabin crew details, meal info (noise)

---

### State 3: Confirmation + Payment (Stage 3)
*Sent: After merchant selects flight, before payment capture.*

```
┌────────────────────────────────────────┐
│  ✅  Air India AI-456 Selected         │
│  ────────────────────────────────────  │
│                                        │
│  09:30 → 12:00  |  ₹3,800             │
│  Kanpur → Delhi  |  15 Apr 2026        │
│                                        │
│  Payment:                              │
│  ○  Paytm Wallet          ₹2,100      │
│  ●  Paytm Business Credit ₹47,000  ← │
│     (pre-selected if credit available) │
│                                        │
│  Passenger:  Ramesh Kumar  (auto)     │
│  GSTIN:      09XXXXX5678   (auto)     │
│                                        │
│  ────────────────────────────────────  │
│           [Confirm Booking]            │
└────────────────────────────────────────┘
```

**Spec:**
- Message type: `interactive` (button)
- Pre-select credit if credit_available >= flight_price
- Pre-select wallet if no credit or credit insufficient
- [Confirm Booking] button = same action as verbal "haan, confirm"
- Passenger + GSTIN shown as read-only (auto-filled, can't edit here — requires calling back)
- Payment selection: tapping ○ switches to wallet; agent speaks to confirm

**Payment toggle logic:**
```
If merchant taps ○ Wallet:
  → Send to agent: payment_switched_to_wallet signal
  → Agent acknowledges: "Wallet se ₹2,100 — confirm?"

If merchant taps [Confirm Booking]:
  → Trigger booking API with pre-selected payment
  → Skip verbal confirm (screen tap = confirmation)
```

---

### State 4A: Processing (Stage 4 — Booking In Progress)
*Sent: Immediately after confirm, during booking API call.*

```
┌────────────────────────────────────────┐
│  ⏳  Booking in progress...            │
│  ────────────────────────────────────  │
│                                        │
│  Air India AI-456                      │
│  Kanpur → Delhi · 09:30 AM             │
│  ₹3,800 · Paytm Business Credit        │
│                                        │
│  Creating your GST invoice...          │
│                                        │
└────────────────────────────────────────┘
```

**Spec:**
- Shown during API processing window (< 3 seconds typical)
- Prevents "did it go through?" anxiety
- Replaced by State 4B on PNR confirmation

---

### State 4B: Booking Confirmed + Documents (Stage 4 — Complete)
*Sent: On PNR generation, within 10 seconds of booking confirmation.*

```
┌────────────────────────────────────────┐
│  ✅  Booking Confirmed!                │
│  ────────────────────────────────────  │
│                                        │
│  PNR:      6E2F4G                     │
│  Flight:   Air India AI-456            │
│  Route:    Kanpur → Delhi              │
│  Date:     15 Apr 2026  ·  09:30 AM   │
│  Passenger: Ramesh Kumar               │
│                                        │
│  ──── Payment ──────────────────────  │
│  💳  Paytm Business Credit             │
│      Charged: ₹3,800                  │
│      Remaining: ₹43,200               │
│                                        │
│  ──── Your Documents ───────────────  │
│  📄  GST Invoice                       │
│      Kumar General Store               │
│      GSTIN: 09XXXXX5678               │
│      [Download PDF ↓]                  │
│                                        │
│  🎫  E-Ticket                          │
│      [Download PDF ↓]                  │
│                                        │
└────────────────────────────────────────┘
```

**Spec:**
- Message type: `document` (two sequential document messages: invoice PDF, e-ticket PDF)
- Preceded by a `text` confirmation card
- Both PDFs sent within 10 seconds of PNR generation (SLA)
- PDF format: GST invoice in CA-approved layout (A4, 1-page)
- E-ticket: Standard airline e-ticket format

---

### State 5: Post-Call Summary (Stage 5 — After Call Ends)
*Sent: 30 seconds after call disconnects.*

```
┌────────────────────────────────────────┐
│  📋  Your Booking Summary              │
│  ────────────────────────────────────  │
│                                        │
│  Air India AI-456                      │
│  Kanpur → Delhi                        │
│  15 Apr 2026 · 09:30 AM               │
│  Passenger: Ramesh Kumar               │
│  Booking ID: PTM-45678                 │
│  Call duration: 2m 47s                 │
│                                        │
│  ──── Need help? ───────────────────  │
│                                        │
│  [✏️ Change/Cancel Booking]            │
│  [↩️ Book Return Flight]               │
│  [📄 Re-send Invoice]                  │
│  [📞 Call Support: 1800-XXX-XXXX]     │
│                                        │
│  ──────────────────────────────────   │
│  ⭐ Rate your experience (1 tap):      │
│  😞  😐  🙂  😊  🤩                   │
│                                        │
└────────────────────────────────────────┘
```

**Spec:**
- Post-call summary sent 30 sec after call end (not during call — avoids distraction)
- Quick-reply buttons for self-serve actions
- Emoji rating row = NPS proxy (1–5 stars → maps to NPS scale)
- [Book Return Flight] → deeplinks into a new inbound call initiation OR a WA-native return booking flow

---

## Error States

### Error: No Flights Found
```
┌────────────────────────────────────────┐
│  ✈️  Search Complete                   │
│  ────────────────────────────────────  │
│                                        │
│  Kanpur → Delhi · Tomorrow Morning     │
│                                        │
│  ⚠️  No direct flights found.          │
│                                        │
│  Options:                              │
│  [🔄 Try Different Date]               │
│  [🔀 See Connecting Flights]           │
│                                        │
└────────────────────────────────────────┘
```

### Error: GSTIN Issue
```
┌────────────────────────────────────────┐
│  ⚠️  GST Update Needed                 │
│  ────────────────────────────────────  │
│                                        │
│  Your GSTIN needs to be updated        │
│  before we can generate your invoice.  │
│                                        │
│  Your booking draft is saved:          │
│  Air India · 09:30 · ₹3,800           │
│                                        │
│  [🔧 Update GSTIN Now]                 │
│  [↩️ Resume Booking After Update]      │
│                                        │
│  Or call us: 1800-XXX-XXXX            │
└────────────────────────────────────────┘
```

### Error: Payment Failed
```
┌────────────────────────────────────────┐
│  ⚠️  Payment Not Completed             │
│  ────────────────────────────────────  │
│                                        │
│  Your credit payment couldn't          │
│  be processed right now.               │
│                                        │
│  Flight: Air India 09:30  ·  ₹3,800   │
│  Seats held for: 8 minutes             │
│                                        │
│  [💳 Try Wallet Instead]               │
│  [🔄 Retry Credit]                     │
│  [📞 Call Us]                          │
└────────────────────────────────────────┘
```

### Error: Call Drop Mid-Booking
```
SMS sent (WhatsApp fallback):
┌────────────────────────────────────────┐
│  📲  Paytm Business Travel             │
│                                        │
│  Lagta hai call drop ho gayi.          │
│                                        │
│  Aapki Air India booking pending hai:  │
│  ₹3,800 · Kanpur → Delhi · 09:30 AM   │
│  Seats held for: 10 minutes            │
│                                        │
│  [▶️ Complete Booking Now]             │
│                                        │
│  Ya call kariye: 1800-XXX-XXXX        │
└────────────────────────────────────────┘
```

---

## Design Tokens & Style Guide

### Colors
```
Primary blue:     #002970  (Paytm brand)
Accent teal:      #00BAF2  (Paytm secondary)
Success green:    #00A651
Warning orange:   #F7941D
Error red:        #D62B2B
Text primary:     #1A1A1A
Text secondary:   #666666
Border/divider:   #E5E5E5
Card background:  #F9F9F9
```

### Typography (WhatsApp-native)
```
Bold text:     *text* (WhatsApp markdown)
Italic:        _text_
Monospace:     `PNR codes, GSTINs`
Section header: ──── Header ────  (em-dash dividers)
Emoji usage:   Sparingly — for status (✅ ⚠️ ✈️ 💳 📄) not decoration
```

### Button Labels
```
Primary CTA:    [Confirm Booking]  [Select →]  [Download PDF ↓]
Secondary CTA:  [Change/Cancel]  [Book Return]  [Re-send Invoice]
Support CTA:    [Call Support: 1800-XXX-XXXX]

Rules:
- CTA text ≤ 20 characters (WhatsApp button limit)
- One primary CTA per card maximum
- No more than 3 buttons per message
```

---

## Accessibility & Edge Cases

| Scenario | Handling |
|---|---|
| WhatsApp not installed | SMS fallback with deep links for all document downloads |
| Merchant on 2G connection | PDFs compressed to < 200KB; text confirmation sent first |
| Merchant deletes WA chat | Booking accessible via Paytm app → History section |
| Merchant changes phone | New phone number = new auth; WA chat history not migrated |
| WA message delivery failure | Retry twice; fallback to SMS with plain-text booking summary |
| Screenshot of WA card shared | Cards contain no sensitive payment data (credit balance redacted after booking) |

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/02-solution-design*
