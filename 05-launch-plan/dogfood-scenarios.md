# Dogfood Scenario Scripts
**10 Scripted Tests · Step-by-Step Assertions · Pass/Fail Criteria**
*Paytm Merchant Travel Desk | 05-launch-plan | QA + CTO-Owned*

---

## Purpose

Week −1 dogfood exposes the product to real human behavior before any merchant sees it. This document turns the 10 scenarios listed in `4-week-timeline.md` into test scripts that any of the 20 dogfooders can execute without ambiguity.

Each scenario has: a tester profile, exact preconditions, step-by-step actions, expected behavior, and the assertions that mark pass or fail.

---

## Ground Rules

1. **Real money.** Use actual Paytm Business Credit or Wallet. Book real flights (cancel within the free window if the trip isn't needed).
2. **Real data.** Your real phone number, real GSTIN. No synthetic merchant IDs.
3. **Record everything.** Every call is auto-recorded; add free-text notes in the dogfood form within 10 minutes of hanging up.
4. **No coaching.** Do not prompt the agent with perfectly-formed sentences. Speak the way you'd speak to a human helpline at home.
5. **Raise blockers immediately.** Sev-1 issues get paged to the CTO, not held for the 5 PM debrief.

---

## Scenario S1 — Happy Path Hindi, Wallet Payment

**Testers:** 5 (mix of Product, Eng, Ops)

**Precondition:** Wallet balance ≥ ₹5,000. No active credit (use a staging merchant ID with credit disabled, or opt out of credit in the flow).

**Script:**
1. Dial `1800-XXX-0100` from your registered number.
2. Wait for greeting. Respond only in Hindi throughout the call.
3. When asked, say: *"Kal Delhi jaana hai, subah ki flight."*
4. Listen to both options. Pick the cheaper one verbally.
5. When asked about payment, say: *"Wallet se."*
6. Confirm with *"Haan, karo."*
7. Let the agent read the PNR + business name. Say *"Sahi hai."* when asked to confirm business name.
8. End the call with *"Dhanyavaad."*

**Assertions:**
- Greeting uses your first name within 5 sec of pickup
- NLU captured destination = DEL, date = tomorrow, time = morning without re-prompting
- Exactly 2 options presented by voice
- WhatsApp card arrived within 200ms of agent saying option 1
- Business name verbally confirmed before call ended
- GST invoice PDF in WhatsApp within 10 sec of PNR
- Total call duration ≤ 4 min (dogfood budget, vs. 3-min target)

**Fail conditions:** Any assertion above missed, or agent re-prompted more than once for the same slot, or invoice never arrived.

---

## Scenario S2 — Happy Path English, Credit Payment

**Testers:** 5

**Precondition:** Paytm Business Credit ≥ ₹20,000 available.

**Script:**
1. Dial helpline.
2. Respond only in English throughout.
3. Say: *"I need to fly from Mumbai to Bangalore day after tomorrow, afternoon flight."*
4. Pick option 2 when presented.
5. Say *"Charge it to my credit line."*
6. Confirm with *"Yes."*
7. Hang up after summary.

**Assertions:**
- Agent switched to English after first English utterance
- Date resolved correctly (today + 2 days)
- "Day after tomorrow" handled as a relative date, not re-asked
- Credit offered FIRST in payment prompt (before wallet)
- WhatsApp payment card pre-selected credit (not wallet)
- Credit charge succeeded, remaining balance shown correctly

---

## Scenario S3 — No Flights Available → Alternate Date

**Testers:** 2 (pick obscure routes to force empty results)

**Precondition:** Choose a route with low frequency (e.g., Kanpur → Coimbatore).

**Script:**
1. Dial helpline.
2. Ask for a route you know has no direct options today.
3. Let the agent handle it. Accept whatever alternate it proposes.

**Assertions (FM-09 recovery):**
- Agent does NOT say "no flights found" and hang up
- Agent offers: connecting flight OR alternate date OR alternate airport
- Recovery triggered within 4 sec of empty API response
- If you accept an alternate, booking completes normally

---

## Scenario S4 — Call Drop at Stage 3 (Payment Initiated)

**Testers:** 2 (Eng — highest criticality)

**Precondition:** About to confirm payment but hang up before saying "yes."

**Script:**
1. Dial helpline, progress through Stage 1 + 2 + part of Stage 3.
2. When agent says "Confirm karein?", **hang up immediately** (don't confirm).
3. Wait for recovery message.

**Assertions (US-010, FM-17 — most critical):**
- WhatsApp resume message arrives within 10 sec of hang-up
- Resume link pre-fills origin, destination, date, and selected flight
- Payment details are NOT pre-filled (security requirement)
- Tapping resume link places a new call OR opens a web form (either acceptable)
- No double charge; credit line shows no authorization hold > 15 min after hang-up
- Seats released after 15-min held-seat window

---

## Scenario S5 — GSTIN Manually Suspended

**Testers:** 1 (Finance Eng — has test merchant with suspended GSTIN flag)

**Precondition:** Use test merchant with `gst_status = SUSPENDED` in staging KYC.

**Script:**
1. Dial helpline as usual.
2. Complete a booking end-to-end.
3. Listen for invoice handling.

**Assertions (FM-14 recovery):**
- Booking completes despite GSTIN suspension
- Agent verbally notes that invoice will be generated once GSTIN is updated
- WhatsApp message flags: "GST invoice pending — update GSTIN to receive."
- E-ticket still delivered (unrelated to GST)
- Async queue retries invoice generation after GSTIN refresh

---

## Scenario S6 — Credit Declined → Wallet Fallback

**Testers:** 2 (Eng + Ops)

**Precondition:** Test merchant with `credit_status = RISK_HOLD` (synthetic decline). Wallet balance ≥ ₹5,000.

**Script:**
1. Dial helpline.
2. Complete booking to payment stage.
3. Pick credit. Credit API will return DECLINED.
4. Accept the fallback.

**Assertions (FM-10 recovery):**
- Agent says: *"Credit approve nahi hua. Wallet se ₹X available hai — chalega?"*
- Agent does NOT say "AI failed" or "system error"
- Wallet payment proceeds normally
- Booking completes with wallet; invoice still generated
- Analytics event `credit_declined_wallet_fallback` fired

---

## Scenario S7 — Explicit Human Handoff Request

**Testers:** 2 (Ops — they know what good handoff feels like)

**Precondition:** None.

**Script:**
1. Dial helpline.
2. At any point after Stage 1, say one of: *"Insaan se baat karni hai"* / *"Transfer me to a human"* / *"I want to speak to a manager."*
3. Do not repeat the request — just wait.

**Assertions (US-012):**
- Handoff initiated within 2 sec of intent detection
- Hold music plays (not silence) during transfer
- Warm transfer completes in < 45 sec (URGENT SLA)
- Human agent opens with your name and a summary of where you were: *"I see you're trying to book Delhi..."*
- Human agent does NOT ask you to repeat your issue from scratch
- Context packet on human console shows: profile, transcript, current stage, extracted slots, handoff reason

---

## Scenario S8 — Background Noise Simulation

**Testers:** 1 (CTO personally — highest visibility check)

**Precondition:** Play loud Bollywood music (approx 70 dB) from a laptop near the phone.

**Script:**
1. Dial helpline with music playing.
2. Try to complete a booking.
3. If STT fails twice, accept the WA form fallback.

**Assertions (FM-02, US-009):**
- After 1st STT failure, agent rephrases (does not repeat the same question verbatim)
- After 2nd STT failure, WA form fallback offered within 4 sec
- Form pre-fills any slots already captured
- Completion via form results in the same booking + invoice flow as voice
- Agent never says *"I did not understand"*

---

## Scenario S9 — Mid-Booking Price Change

**Testers:** 1 (Eng — uses staging tool to inject price bump)

**Precondition:** Use staging price-injection endpoint to raise the fare by ₹400 between search and confirm.

**Script:**
1. Dial helpline.
2. Select a flight; progress toward confirm.
3. Staging tool fires: price increase injected.

**Assertions (FM-13):**
- Agent catches price change before charging
- Agent says: *"Price ₹3,800 se ₹4,100 ho gayi. Confirm karein?"*
- Merchant gets a second confirm moment (not silently charged)
- If confirmed, new price applied; invoice reflects new amount
- If cancelled, no charge fires

---

## Scenario S10 — Proactive Outbound Call

**Testers:** 2 (Product — experience the outbound flow)

**Precondition:** PM places outbound call to tester's number using internal outbound test tool.

**Script:**
1. Answer the call. Agent opens with: *"Namaste [Name], main Paytm Business Travel se call kar raha hoon..."*
2. Listen to the seasonal pattern pitch.
3. Decline politely, or accept and book.

**Assertions:**
- Agent self-identifies as Paytm within first 3 sec
- Opt-out path works if you say *"Koi baat nahi, abhi nahi"*
- If declined twice in outbound history, merchant is marked DND (no third call)
- If accepted, flow transitions seamlessly into Stage 2 search (no repeat of Stage 1 greeting)
- TRAI DND compliance check confirmed in pre-call (analytics event `dnd_checked_outbound`)

---

## Dogfood Coverage Matrix

Every dogfooder receives 2 scenarios — one happy path and one failure mode — ensuring full coverage.

| Tester Role | Count | Assigned Scenarios |
|---|---|---|
| Product | 5 | S1 (×2), S2 (×1), S7 (×1), S10 (×1) |
| Engineering | 5 | S1 (×1), S2 (×2), S4 (×2), S9 (×1), S8 (CTO) |
| Business Ops | 5 | S1 (×1), S2 (×1), S3 (×1), S6 (×1), S10 (×1) |
| Customer Support | 5 | S1 (×1), S2 (×1), S5 (×1), S6 (×1), S7 (×1) |

Total scenario executions: 40+ (matches D02 target). Each scenario hit by at least 2 unique testers.

---

## Feedback Capture

After each call, dogfooders submit a form with:

- Scenario ID + tester ID
- Booking PNR (or "no booking") + call duration
- Any assertion that failed (free-text)
- 1–5 rating: voice quality, intent understanding, speed, invoice quality, overall
- Open field: "One thing I'd change"

Form auto-populates from call logs where possible; dogfooder confirms and adds notes.

---

## 5 PM Daily Debrief

Every day Monday–Friday of Week −1 at 17:00 IST:
- 30 min with CTO, PM, Eng Lead, QA Lead
- Agenda: Today's calls → top 3 issues → triage Sev-1 same day, Sev-2 by EOD next day
- Output: updated `dogfood-bug-log.md`

---

*Owner: QA + CTO · Last updated: April 2026*
