# Human Agent Handoff Logic
**When to Escalate · How to Transfer · Context Packet · SLAs**
*Paytm Merchant Travel Desk | 03-ai-architecture*

---

## Philosophy

Human handoff is **not a failure state** — it's a feature. The AI agent's job is to handle 80% of cases at machine speed. The human agent's job is to handle the 20% where empathy, judgment, or authority beyond the AI's scope is needed.

Two design rules:
1. **Never trap the merchant** — they should be able to reach a human at any point by saying "insaan se baat karni hai"
2. **Never cold-transfer** — the human agent always receives full context before speaking to the merchant

---

## Escalation Triggers

### Trigger Type 1: Merchant-Initiated (Explicit Request)

```
Detected utterances → HUMAN_AGENT intent:
  Hindi:   "insaan se baat karni hai"
           "agent se connect karo"
           "manager chahiye"
           "real person"
           "kisi insaan ko do"
  English: "human agent"
           "speak to a person"
           "transfer me"
           "I want a real person"
           "manager please"

Confidence threshold: > 0.70 (allow partial matches — "insaan" alone is sufficient)
Action: Immediate escalation, no retry attempts
```

**Agent response:**
> *"Bilkul — main aapko abhi specialist se connect karta hoon. 30 second mein hoga."*
> *(Of course — connecting you to a specialist right now. 30 seconds.)*

---

### Trigger Type 2: AI Failure — Repeated NLU Breakdown

```
Condition: nlu_failures_in_session >= 3
           (3 consecutive low-confidence responses in same stage)

Definition of "NLU failure":
  - Intent confidence < 0.45 after retry
  - Slot extraction fails after 2 prompts
  - Merchant explicitly says "tu samajh nahi raha" / "you don't understand"

Action: Escalate with reason code: "nlu_failure"
```

**Agent response:**
> *"Lagta hai mujhe samajhne mein thodi dikkat ho rahi hai. Ek specialist se baat karwaata hoon jo better help kar sakenge."*
> *(Seems like I'm having trouble understanding. Let me connect you to a specialist who can help better.)*

**Design note:** Don't say "AI failed" or "system error" — frame as specialist for better service.

---

### Trigger Type 3: Complex Transaction Types

```
Case types that auto-escalate (no AI attempt):
  CANCELLATION:   Merchant wants to cancel an existing booking
  REFUND:         Refund request for any past booking
  NAME_CHANGE:    Passenger name correction on issued ticket
  GROUP_BOOKING:  > 4 passengers on single booking
  INTERNATIONAL:  Any non-domestic flight request
  COMPLAINT:      "Mujhe bahut takleef hui", "this is terrible", "I'm very upset"
  MEDICAL:        "Emergency", "hospital mein jaana hai", any medical urgency
  LOST_DOCUMENT:  "Mera ticket nahi mila", e-ticket/invoice not received (> 1 hr old)

Detection: NLU intent classification on first utterance in Stage 1
Action:    Skip booking flow entirely → directly escalate
```

**Agent response (example — cancellation):**
> *"Cancel karne ke liye specialist se baat karni padegi — main connect karta hoon."*
> *(For cancellations, you'll need to speak to a specialist — connecting you now.)*

---

### Trigger Type 4: Emotional Distress Detection

```
Signals:
  Explicit: "main bahut pareshaan hoon", "this is a disaster"
  Implicit: Speaking pace > 40% faster than baseline for this merchant
            Repeated interruptions (> 3 in 30 sec)
            Vocal pitch elevation sustained > 10 sec

Action: Escalate with reason code: "emotional_distress"
        Priority queue: URGENT (skip normal queue, < 60 sec wait)

Rationale: An anxious merchant needs a human voice, not AI reassurance
```

**Agent response:**
> *"Main samajh sakta hoon. Abhi specialist ko connect karta hoon jo personally help karenge."*
> *(I understand. Connecting you to a specialist who will personally help.)*

---

### Trigger Type 5: API / System Failure Cascade

```
Condition: 2+ backend APIs in OPEN circuit state simultaneously
           OR booking_api returns FATAL_ERROR status
           OR session_state corruption detected

Action: Escalate with reason code: "system_failure"
        Full session data logged for diagnosis
```

**Agent response:**
> *"System mein thodi technical dikkat aa gayi. Specialist handle kar lenge — 1 minute."*
> *(There's a small technical issue. A specialist will handle this — 1 minute.)*

---

## Handoff Process Flow

```
ESCALATION DECISION
        │
        ▼
[1] BUILD CONTEXT PACKET
    (< 200ms — from session Redis store)
        │
        ▼
[2] CHECK QUEUE STATUS
    available_agents > 0?
    ├── YES → Route to available agent
    └── NO  → Wait estimate > 3 min?
              ├── YES → Offer callback
              └── NO  → Queue with wait time announcement
        │
        ▼
[3] WARM HOLD MUSIC
    + "Connecting to specialist" announcement
    + WhatsApp: "Connecting you to a travel specialist. Hold time: ~{X} sec"
        │
        ▼
[4] HUMAN AGENT PICKS UP
    Agent screen shows: FULL CONTEXT PACKET
    Agent speaks FIRST — uses merchant's name and situation
        │
        ▼
[5] AI AGENT DROPS OFF SILENTLY
    No announcement — seamless transition
        │
        ▼
[6] HUMAN AGENT HANDLES
    Resolution logged → session_outcome
    CRM updated
        │
        ▼
[7] POST-HANDOFF ACTIONS
    SMS/WA: booking confirmation or resolution summary
    Analytics: handoff_reason, resolution_time, outcome
```

---

## Context Packet (What Human Agent Sees)

The human agent's screen shows a structured context packet **before they speak their first word**. They already know everything.

```
┌─────────────────────────────────────────────────────────────┐
│  🔴  INCOMING TRANSFER — PAYTM MERCHANT TRAVEL              │
│  ─────────────────────────────────────────────────────────  │
│                                                             │
│  MERCHANT                                                   │
│  Name:         Ramesh Kumar                                 │
│  City:         Kanpur                                       │
│  Phone:        +91-98XXXXXXXX                               │
│  Paytm ID:     M12345                                       │
│  Credit limit: ₹20,000  |  Available: ₹20,000              │
│  Wallet:       ₹2,100                                       │
│  GSTIN:        09XXXXX5678                                  │
│  Account:      GOOD STANDING                                │
│                                                             │
│  HANDOFF REASON                                             │
│  🟡  nlu_failure  (3 failures in Stage 2: search)          │
│  Last merchant utterance: "Bambai nahi, Pune wala jaana..."│
│                                                             │
│  CALL PROGRESS                                              │
│  Stage: 2 — SEARCH                                         │
│  Duration so far: 2 min 14 sec                             │
│  Extracted slots: dest=? (failed), date=tomorrow, time=AM  │
│                                                             │
│  CONVERSATION TRANSCRIPT                                    │
│  [A] "Namaste Ramesh bhai..."                               │
│  [M] "Pune jaana hai kal, actually Pune ke paas..."        │
│  [A] "Kahan jaana hai exactly?"                             │
│  [M] "Bambai nahi, Pune..."                                 │
│  [full transcript link →]                                   │
│                                                             │
│  QUICK ACTIONS                                              │
│  [Search Pune flights] [View merchant history] [Escalate]  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Context Packet Fields

| Field | Source | Purpose |
|---|---|---|
| Merchant name, city | KYC DB | Personalized greeting |
| Credit + wallet balances | Credit/Wallet API | Payment resolution |
| GSTIN + business name | KYC DB | Invoice issues |
| Account standing | Credit/KYC | Risk flags |
| Handoff reason code | Session state | Agent knows what happened |
| Last merchant utterance | STT transcript | Understand where AI failed |
| Stage + extracted slots | Session state | Don't re-ask what AI already got |
| Full conversation transcript | Session log | Complete context |
| Last booking history | Booking DB | "You traveled to Delhi last time..." |

---

## Human Agent First Words

The human agent uses the context packet to speak first with full context. **Never "How can I help you today?" without context.**

**Template by handoff reason:**

| Reason | Human Agent Opening |
|---|---|
| `nlu_failure` | "Hello Ramesh ji, I'm Priya from Paytm Travel. I see you want to book a flight for tomorrow — let me help you directly. Where exactly do you need to go?" |
| `explicit_request` | "Hello Ramesh ji, I'm Priya from Paytm Travel — I see you'd like to speak to someone. How can I help?" |
| `cancellation` | "Hello Ramesh ji, I'm Priya. I see you want to cancel a booking. I have your details here — which booking should I cancel?" |
| `emotional_distress` | "Hello Ramesh ji, this is Priya from Paytm Travel. I understand there's been some trouble — I'm here to sort it out for you. Tell me what happened." |
| `system_failure` | "Hello Ramesh ji, I'm Priya. There was a small technical hiccup — but I have everything here. Let me complete your booking right now." |

---

## SLAs and Queue Management

### Wait Time SLAs

| Priority | Condition | SLA | Example |
|---|---|---|---|
| 🔴 URGENT | emotional_distress, medical | < 45 sec | Merchant in distress |
| 🟠 HIGH | explicit_request, system_failure | < 90 sec | Merchant asked for human |
| 🟡 MEDIUM | nlu_failure, complex transaction | < 3 min | AI couldn't understand |
| 🟢 NORMAL | General query | < 5 min | Non-booking questions |

### Queue Overflow Handling
```
If queue > 5 min wait:
  Option A: Callback — "Main 5 minute mein call karunga"
            → CRM creates callback task, routes to next available agent
  Option B: WhatsApp resolution — for simple issues (rebooking, invoice)
            → AI attempts WA-based resolution without call

Queue monitoring alerts:
  - Alert: queue_wait > 3 min (page on-call agent)
  - Alert: queue_wait > 5 min (escalate to supervisor)
  - Alert: queue_size > 20 (emergency staffing trigger)
```

### Resolution Time Targets

| Case Type | Target Resolution |
|---|---|
| Booking completion (AI failed) | < 5 min |
| Cancellation | < 3 min |
| Refund initiation | < 5 min |
| Invoice regeneration | < 2 min |
| Name change | < 10 min (airline API) |
| Complaint resolution | < 8 min |

---

## Post-Handoff Analytics

```
Events logged:
  handoff_initiated:
    - merchant_id, reason_code, stage_at_handoff
    - call_duration_before_handoff, retry_count
    - ai_confidence_scores (array of last 3)

  human_resolution:
    - agent_id, resolution_type
    - time_to_resolve (sec)
    - outcome: "booking_completed" | "cancelled" | "refunded" | "unresolved"
    - merchant_nps (post-call survey)

Dashboards:
  Daily: Handoff rate by reason code
  Weekly: Human resolution time trend
  Monthly: Top NLU failure patterns → training data improvements
```

---

## Feedback Loop to AI Training

Every human handoff is training data:

```
Pipeline:
  1. Handoff logged with: merchant_utterances, nlu_confidence, reason_code
  2. Human agent resolves → logs what they understood (structured input)
  3. If reason = "nlu_failure":
     → Delta: (what merchant said) + (what was actually meant) → training example
  4. Weekly: 50 highest-confidence examples added to NLU training set
  5. Monthly model retrain → nlu_failure rate should decrease over time

Target trajectory:
  Month 1: 8% nlu_failure rate
  Month 3: 5% nlu_failure rate (after first retrain)
  Month 6: 3% nlu_failure rate (with 500+ real examples)
```

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/03-ai-architecture*
