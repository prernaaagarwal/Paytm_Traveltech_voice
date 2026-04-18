# WhatsApp Business API Templates — Meta Submission
**8 Pre-Approved Templates for Merchant Travel Desk**
*Paytm Merchant Travel Desk | 05-launch-plan*

---

## Submission Context

| Field | Value |
|---|---|
| **Meta Business Manager Account** | Paytm Travel (WABA ID pending) |
| **Submission Target** | Monday, Week −3 |
| **Meta Approval SLA** | 7 calendar days (worst case 14) |
| **Category Strategy** | Mix of UTILITY (transactional) and AUTHENTICATION (none in MVP) — no MARKETING category templates in MVP (avoids per-conversation billing surprises) |
| **Languages** | `hi_IN` (Hindi) and `en_IN` (English) — each template submitted in both |

Total templates: 8 × 2 languages = **16 template submissions**.

---

## Template 1: Call Companion Greeting

**Usage:** Sent within 200ms of call pickup (Stage 1, parallel with agent's voice greeting).

| Field | Value |
|---|---|
| **Template Name** | `travel_companion_greeting` |
| **Category** | UTILITY |
| **Language** | hi_IN + en_IN |
| **Header Type** | TEXT |
| **Body Variables** | `{{1}}` = merchant first name |

**Body (hi_IN):**
```
Namaste {{1}}! Aapka booking assistant live hai. 🛫

Call pe baat karte rahiye — options yahin screen par bhi dikhenge.
```

**Body (en_IN):**
```
Hi {{1}}! Your booking assistant is live. 🛫

Stay on the call — options will also appear here on screen.
```

---

## Template 2: Flight Options Card

**Usage:** Sent within 200ms of agent reading 2 flight options (Stage 2).

| Field | Value |
|---|---|
| **Template Name** | `travel_flight_options` |
| **Category** | UTILITY |
| **Header Type** | TEXT |
| **Body Variables** | `{{1}}` origin city, `{{2}}` destination city, `{{3}}` date, `{{4}}` option 1 line, `{{5}}` option 2 line |
| **Buttons** | Quick-reply: `Select Option 1`, `Select Option 2` |

**Body:**
```
✈️ {{1}} → {{2}}  |  {{3}}

Option 1: {{4}}
Option 2: {{5}}

Tap to select or tell the agent on call.
```

Example rendering: `Option 1: IndiGo 6E-123, 08:00–10:30, ₹4,200`

---

## Template 3: Payment Confirmation

**Usage:** Sent when merchant selects a flight; before final verbal confirm (Stage 3).

| Field | Value |
|---|---|
| **Template Name** | `travel_payment_confirm` |
| **Category** | UTILITY |
| **Body Variables** | `{{1}}` flight summary, `{{2}}` price, `{{3}}` credit available, `{{4}}` wallet balance, `{{5}}` passenger name, `{{6}}` masked GSTIN |
| **Buttons** | `Confirm & Pay with Credit`, `Pay with Wallet` |

**Body:**
```
✅ Selected: {{1}}
Amount: ₹{{2}}

Payment:
  • Paytm Business Credit — ₹{{3}} available
  • Paytm Wallet — ₹{{4}}

Passenger: {{5}}
GSTIN: {{6}}
```

---

## Template 4: Booking Confirmed

**Usage:** Sent within 2 sec of PNR generation (Stage 4).

| Field | Value |
|---|---|
| **Template Name** | `travel_booking_confirmed` |
| **Category** | UTILITY |
| **Body Variables** | `{{1}}` PNR, `{{2}}` flight, `{{3}}` route, `{{4}}` date-time, `{{5}}` amount, `{{6}}` remaining credit |

**Body:**
```
✅ Booking Confirmed!

PNR: {{1}}
Flight: {{2}}
Route: {{3}}
Date: {{4}}

💳 Paid ₹{{5}} via Paytm Business Credit
   Remaining credit: ₹{{6}}

E-ticket and GST invoice arriving next.
```

---

## Template 5: Invoice & E-Ticket Delivery (Document Message)

**Usage:** Sent within 10 sec of PNR (Stage 4). This is the OQ-4 resolution — uses `document` message type with header.

| Field | Value |
|---|---|
| **Template Name** | `travel_invoice_eticket` |
| **Category** | UTILITY |
| **Header Type** | DOCUMENT (PDF attachment) |
| **Body Variables** | `{{1}}` business name, `{{2}}` masked GSTIN, `{{3}}` invoice number, `{{4}}` amount |

**Body:**
```
📄 GST Invoice + E-Ticket

{{1}}
GSTIN: {{2}}
Invoice No: {{3}}
Amount: ₹{{4}}

Save the attached PDF for your records.
```

Note: Two document messages are sent in sequence — one with the e-ticket PDF, one with the GST invoice PDF. Both use this template with different attachments.

---

## Template 6: Post-Call Summary

**Usage:** Sent 30 sec after call disconnect (Stage 5 + post-call).

| Field | Value |
|---|---|
| **Template Name** | `travel_post_call_summary` |
| **Category** | UTILITY |
| **Body Variables** | `{{1}}` booking ID, `{{2}}` flight summary, `{{3}}` call duration |
| **Buttons** | `Change/Cancel`, `Return Flight`, `Re-send Invoice`, `Call Support` |

**Body:**
```
📋 Booking Summary

ID: {{1}}
{{2}}
Call duration: {{3}}

Need anything? Tap below — no need to call back.
Support: 1800-XXX-XXXX (24/7)
```

---

## Template 7: Call-Drop Recovery Link

**Usage:** Sent within 10 sec of detected call drop post-Stage 1 (US-010).

| Field | Value |
|---|---|
| **Template Name** | `travel_call_drop_resume` |
| **Category** | UTILITY |
| **Body Variables** | `{{1}}` merchant first name, `{{2}}` resume deep-link URL, `{{3}}` held-seat expiry (minutes) |
| **Buttons** | URL button: `Resume Booking` |

**Body:**
```
{{1}}, your call disconnected before the booking finished.

Your search and flight selection are saved. Tap below to resume — seats are held for {{3}} more minutes.

Payment details will be re-requested for security.
```

---

## Template 8: STT Fallback Form

**Usage:** Sent when STT fails 2× consecutively (US-009); offers a WA form to type what the agent can't hear.

| Field | Value |
|---|---|
| **Template Name** | `travel_voice_fallback_form` |
| **Category** | UTILITY |
| **Body Variables** | `{{1}}` merchant first name, `{{2}}` fallback form URL |
| **Buttons** | URL button: `Type It Instead` |

**Body:**
```
{{1}}, we are having trouble hearing you clearly.

No problem — tap below to type your destination and date. The agent will stay on the call and pick up from there.
```

---

## Template-Level Checks Before Submission

For every template, confirm:
- ✅ No promotional language ("great deals", "hurry") — would reclassify as MARKETING
- ✅ Every variable has a realistic example value in the submission form
- ✅ Button labels are ≤ 20 characters each (Meta limit)
- ✅ Body text is ≤ 1,024 characters (Meta limit)
- ✅ Both `hi_IN` and `en_IN` versions submitted simultaneously
- ✅ PDF attachment templates (Template 5) pre-tested on Android WA 2.23+ and iOS WA 23.x+

---

## Fallback If Approvals Slip

| Scenario | Fallback |
|---|---|
| 1–2 templates rejected | Revise wording, resubmit same day; 48-hr re-approval SLA |
| PDF document template (Template 5) rejected | Fallback: send invoice as URL link to hosted PDF (same template category, different type). Degrades UX but unblocks launch. |
| All templates delayed past Week −1 | Delay pilot by matching duration; do not launch without WA companion (voice-only violates FR-06 and US-007) |
| Meta escalation needed | Contact via Meta Business Solutions Partner; PM-to-partner escalation path documented in `7-appendix/references.md` |

---

## Template Performance Monitoring (Post-Launch)

Each template tracked in analytics:
- `wa_template_sent`: count per template
- `wa_template_delivered`: WA delivery receipt received
- `wa_template_read`: WA read receipt received
- `wa_template_tapped`: button or URL tapped

Baseline assumptions to validate in Week 1:
- Templates 1–6 delivery rate: >98%
- Template 5 (PDF): delivery rate >95% (2G/3G degradation expected)
- Template 6 button tap rate: >15% (self-serve adoption signal)
- Template 7 (call-drop) tap rate: >60% (high-intent users)

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/05-launch-plan*
