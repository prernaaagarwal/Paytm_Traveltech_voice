# Human Agent Training Curriculum
**5-Day Program · Context Packet Spec · 5 Handoff Scenarios · Certification**
*Paytm Merchant Travel Desk | 05-launch-plan | Customer-Ops-Owned*

---

## Purpose

Human agents handle the 10–15% of calls the AI cannot complete. A bad handoff destroys trust faster than an AI failure. This curriculum turns 8 customer-support specialists into calm, informed escalation handlers who resolve — not repeat — the merchant's issue.

Training ran Monday–Friday of Week −1. Certification completed by Thursday EOD.

---

## Cohort

| # | Role | Tenure at Paytm | Language | City Desk |
|---|---|---|---|---|
| A1 | Senior agent (team lead) | 4 yr | Hindi + English | All 3 |
| A2 | Senior agent | 3 yr | Hindi + Bhojpuri | Kanpur |
| A3 | Senior agent | 3 yr | Hindi + Gujarati | Surat |
| A4 | Senior agent | 2 yr | Hindi + Marwari | Jaipur |
| A5 | Agent | 2 yr | Hindi + English | All 3 |
| A6 | Agent | 2 yr | Hindi + English | All 3 |
| A7 | Agent | 1 yr | Hindi + English | All 3 |
| A8 | Agent | 1 yr | Hindi + English | All 3 |

Coverage: 09:00–21:00 IST, 2 agents on any given shift. Post-21:00 handoff requests route to a callback queue processed next morning.

---

## 5-Day Curriculum

### Day 1 — Product Foundation (4 hours)

| Block | Content |
|---|---|
| 09:00–10:00 | Product overview: why this product exists, who the merchant is, why voice + credit matters |
| 10:00–11:00 | Demo: 3 happy-path calls (Hindi, English, Hinglish) |
| 11:00–12:00 | The 5-stage call flow + AI capabilities and limits |
| 13:00–14:00 | GST invoice basics for agents (what merchants ask, what CAs need) |
| 14:00–15:00 | Paytm Business Credit primer (limits, auth vs. capture, decline reasons) |
| 15:00–17:00 | Agents try the flow themselves as merchants (place 2 test calls each) |

**Exit:** Each agent produces a one-page "what surprised me" reflection. Reviewed by team lead.

### Day 2 — Context Packet Walk-Through (4 hours)

| Block | Content |
|---|---|
| 09:00–11:00 | Anatomy of the context packet (see Section below). Walk through 10 real dogfood packets. |
| 11:00–12:00 | How to read the packet in < 15 seconds before greeting |
| 13:00–15:00 | Pair exercise: partner reads a packet, you state the merchant's situation in 3 sentences |
| 15:00–17:00 | Role-play: senior agent plays merchant, trainee picks up handoff |

**Exit:** Mini-exam — 20 context packets shown at 15-sec intervals; agent writes merchant situation from memory. Pass: 16/20.

### Day 3 — The 5 Handoff Scenarios (4 hours)

See Section "Handoff Scenarios" below. Full 4 hours on role-plays; 3 trainers rotating.

**Exit:** Each agent role-plays all 5 scenarios. Trainer scores each on a rubric (greeting quality, context use, resolution time, merchant composure, wrap-up). Pass: 4/5 scenarios at "meets expectations" or higher.

### Day 4 — Edge Cases + De-Escalation (4 hours)

| Block | Content |
|---|---|
| 09:00–10:00 | Angry merchant: script is empathy first, then facts |
| 10:00–12:00 | Refund handling: policies, timelines, what to promise (and not) |
| 13:00–15:00 | Data privacy scenarios: merchant asks "why do you have my data?" — correct responses |
| 15:00–17:00 | Escalation to Tier 2 / PM / CTO — when, how, what documentation |

**Exit:** 3 surprise role-plays including 1 angry-merchant scenario.

### Day 5 — Shadow + Certification (4 hours)

| Block | Content |
|---|---|
| 09:00–12:00 | Shadow a senior agent on 3 real dogfood handoff calls |
| 13:00–16:00 | Certification role-play panel: 2 trainers + 1 PM observer; agent does 5 back-to-back scenarios drawn at random |
| 16:00–17:00 | Debrief + certification decision |

**Exit:** Certified or not. All 8 agents certified on first pass.

---

## Context Packet Specification

What the human agent sees on screen the moment a call is transferred. Must render within 2 sec of transfer trigger.

### Layout

```
┌──────────────────────────────────────────────────────────────┐
│  🔴 URGENT HANDOFF · Waiting: 00:28                          │
│  Ramesh Kumar  (Kanpur)  ·  KYC verified                     │
│  Credit: ₹47,000 · Wallet: ₹2,100 · GSTIN: 09XXXXX5678       │
├──────────────────────────────────────────────────────────────┤
│  HANDOFF REASON:                                              │
│  Credit API declined twice (RISK_HOLD). Merchant said         │
│  "yeh toh use karo" (frustrated). Wallet has insufficient     │
│  balance for trip (₹3,800 needed, ₹2,100 available).          │
├──────────────────────────────────────────────────────────────┤
│  CURRENT STATE:                                               │
│  Stage: CONFIRM (payment step)                                │
│  Selected flight: Air India AI-456, Kanpur → Delhi,          │
│                   Tomorrow 09:30, ₹3,800                     │
│  Seats held until: 14:47 IST (8 min remaining)               │
├──────────────────────────────────────────────────────────────┤
│  RECENT TRANSCRIPT (last 3 turns):                            │
│  14:38 Agent: "Credit approve nahi hua..."                   │
│  14:39 Merchant: "Abe kal wallet se kiya tha, aaj kyun..."  │
│  14:39 Agent: "Specialist se connect kar raha hoon..."       │
├──────────────────────────────────────────────────────────────┤
│  SUGGESTED ACTIONS:                                           │
│  [1] Check credit decline reason + offer partial-credit      │
│  [2] Offer add-funds-to-wallet flow                          │
│  [3] Offer to hold seat + call back after repayment          │
├──────────────────────────────────────────────────────────────┤
│  QUICK COMMANDS:                                              │
│  [Release held seat] [Check credit decline code] [Refund]    │
└──────────────────────────────────────────────────────────────┘
```

### Data Sources

| Field | Source | Freshness |
|---|---|---|
| Merchant profile | Paytm Merchant API | Real-time |
| Credit + wallet balance | Paytm Credit + Wallet API | < 2 sec old |
| Handoff reason | Dialogue Manager | Set at trigger |
| Current state | Redis session store | Real-time |
| Recent transcript | Dialogue Manager + STT | < 5 sec lag |
| Held seat timer | Paytm Travel API | Real-time |
| Suggested actions | Rule engine based on handoff reason | Pre-computed at trigger |

### Render SLA

- Full packet renders within 2 sec of handoff trigger
- Warm transfer hold music continues until agent clicks "I'm ready"
- Packet refreshes automatically every 30 sec while handoff is active

---

## The 5 Handoff Scenarios

### Scenario 1: Explicit Request ("Insaan se baat karni hai")

**Triggered by:** NLU intent `HUMAN_AGENT` with confidence > 0.70

**Agent must:**
1. Greet by name within 5 sec of pickup
2. Acknowledge the merchant asked for a human — never ask "why did you want a human?"
3. Read the transcript snippet; pick up the booking from current state
4. Complete the booking OR explain why it can't be completed and offer alternatives

**Certification rubric:** Greets by name ✓ · Uses packet ✓ · Resolves within 4 min ✓

### Scenario 2: AI Fails 3× on Same Query

**Triggered by:** Dialogue Manager counts 3 NLU/STT failures on same slot

**Agent must:**
1. Open with the merchant's stated intent, not a greeting fluff ("I see you're trying to book from Kanpur to Delhi tomorrow morning")
2. Complete slot-filling manually by asking clearly
3. Use the flight search tool (same as AI) to present options
4. Complete booking

**Certification rubric:** Skips small talk ✓ · Demonstrates tool use ✓ · < 5 min ✓

### Scenario 3: Emotional Distress

**Triggered by:** Keyword detection ("stuck", "cancelled", "refund") OR prosody signal

**Agent must:**
1. Empathy first: "Maaf kijiye, samajh sakta hoon."
2. Get facts in natural sequence, not an interrogation
3. Provide a commitment (resolution or escalation within defined SLA)
4. Document in CRM for follow-up

**Certification rubric:** Empathy opening ✓ · No defensive language ✓ · Clear next step ✓

### Scenario 4: Complex Case (Refund, Name Change, Group Booking Request)

**Triggered by:** NLU intent `EXISTING_BOOKING` OR keywords matching complex-case list

**Agent must:**
1. Identify which complex case it is
2. Quote policy correctly (refund fee, name-change window, group-booking out-of-scope)
3. Never promise what can't be delivered
4. Transfer to Tier 2 if outside Tier 1 scope

**Certification rubric:** Correct policy quoted ✓ · Realistic timeline ✓ · Clean Tier 2 transfer if needed ✓

### Scenario 5: AI Error / System Failure

**Triggered by:** Fatal backend error (API 5xx, booking mismatch)

**Agent must:**
1. **Never** say "our AI failed" or "system error"
2. Frame as specialist escalation: "I'll handle this personally"
3. Manually place booking using internal booking tool
4. Manually generate invoice via GST service admin panel
5. Send follow-up SMS with PNR + invoice link

**Certification rubric:** No blame language ✓ · Manual booking executed ✓ · SMS follow-up ✓

---

## Certification Results

| Agent | Day 2 Exam | Day 3 Role-Play | Day 5 Cert Panel | Status |
|---|---|---|---|---|
| A1 | 20/20 | 5/5 exceeds | ✅ Certified | Team Lead |
| A2 | 18/20 | 4/5 meets | ✅ Certified | Certified |
| A3 | 19/20 | 5/5 meets+ | ✅ Certified | Certified |
| A4 | 17/20 | 4/5 meets | ✅ Certified | Certified |
| A5 | 18/20 | 5/5 meets | ✅ Certified | Certified |
| A6 | 16/20 | 4/5 meets | ✅ Certified | Certified (pass threshold) |
| A7 | 19/20 | 5/5 meets | ✅ Certified | Certified |
| A8 | 17/20 | 4/5 meets | ✅ Certified | Certified |

**8 of 8 certified on first pass.** A6 was near the pass threshold; assigned to shadow A1 for first 2 shifts of Week 1.

---

## Ongoing Performance Monitoring

Each handoff call is scored by Customer Ops QA on:
- Time-to-greet-by-name (target < 10 sec post-transfer)
- Context packet usage (target 100%)
- Resolution time (target < 6 min Tier 1)
- Merchant NPS on handoff (target > 45)
- Language choice matches merchant (target 100%)

Monthly calibration sessions: 3 handoff calls reviewed together; recalibrate rubric.

---

## Escalation Paths (Tier 1 → Tier 2 → PM/CTO)

| Situation | Tier 1 (Agents A1–A8) | Tier 2 (Team Lead A1) | PM/CTO |
|---|---|---|---|
| Booking completion issue | Handle | Escalate if > 10 min | No |
| Refund dispute | Handle per policy | Escalate if > ₹10,000 | Escalate if regulatory |
| Data privacy complaint | Acknowledge + capture | Handle within 24h | Escalate if DPO needed |
| Fraud suspicion | Flag + hold | Coordinate with Risk | Immediate for Sev-1 |
| System error | Document + manual workaround | Escalate to Eng on-call | Immediate for Sev-1 |

---

*Owner: Customer Ops Lead · Co-signers: PM, CTO · Last updated: Week −1 Thursday*
