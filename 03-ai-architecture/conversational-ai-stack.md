# Conversational AI Stack
**STT · NLU · Dialogue Manager · TTS · Backend Integrations**
*Paytm Merchant Travel Desk | 03-ai-architecture*

---

## Stack Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                    MERCHANT'S PHONE                             │
│              Voice In ←──────────────→ Voice Out               │
└──────────────────┬──────────────────────────┬───────────────────┘
                   │                          │
                   ▼                          ▲
┌──────────────────────────┐    ┌─────────────────────────────┐
│  SPEECH-TO-TEXT (STT)    │    │  TEXT-TO-SPEECH (TTS)       │
│  Google Cloud STT        │    │  Google Cloud TTS           │
│  Hindi + English + mix   │    │  Conversational voice       │
│  Target: >85% WER        │    │  Target: <1 sec latency     │
└──────────────┬───────────┘    └─────────────────────────────┘
               │                          ▲
               ▼                          │
┌──────────────────────────────────────────────────────────────┐
│               NATURAL LANGUAGE UNDERSTANDING (NLU)           │
│  BERT fine-tuned on 1,000+ merchant travel queries           │
│  Intent classification · Entity extraction · Confidence      │
│  Handles: Hinglish, phonetic variants, relative dates        │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│                   DIALOGUE MANAGER                           │
│  Custom state machine · 5 stages · Slot-filling              │
│  Error recovery · Fallback triggers · Handoff logic          │
└──────┬────────────────────────────────────────────┬──────────┘
       │                                            │
       ▼                                            ▼
┌─────────────────┐                    ┌────────────────────────┐
│  BACKEND APIs   │                    │  WHATSAPP COMPANION    │
│  Merchant KYC   │                    │  Card renderer         │
│  Travel (flights)│                   │  PDF delivery          │
│  Credit         │                    │  Event sync            │
│  GST validation │                    └────────────────────────┘
│  Booking engine │
└─────────────────┘
```

---

## Layer 1: Speech-to-Text (STT)

### Provider & Configuration
```yaml
provider: Google Cloud Speech-to-Text v2
model:    latest_long   # optimized for phone audio
encoding: MULAW         # standard telephony
sample_rate: 8000 Hz    # PSTN call quality
channels: 1             # mono

language_codes:
  primary:   hi-IN      # Hindi
  secondary: en-IN      # Indian English
  mode:      multi-language  # handles code-switching mid-sentence
```

### Accuracy Targets

| Environment | Target WER | Challenge |
|---|---|---|
| Quiet office | < 8% | Baseline |
| Shop with customers | < 15% | Background voices, cash register |
| Street / traffic | < 18% | Wind, horns, engine noise |
| Phone on speaker | < 12% | Echo, reverb |

**WER = Word Error Rate. Target: >85% of utterances transcribed usably.**

### Noise Handling
```
Pre-processing pipeline (runs before STT):
  1. Noise gate: suppress audio below -45 dBFS (silence detection)
  2. Echo cancellation: acoustic echo from merchant's speaker
  3. Bandpass filter: 300–3400 Hz (telephone voice band)
  4. Normalization: compress dynamic range for consistent volume

Real-time noise assessment:
  signal_to_noise_ratio = measure(audio_chunk)
  if SNR < 12 dB:
    confidence_threshold_adjusted = 0.70  # lower bar in noisy env
    trigger: "Thoda shor hai, zor se bolo?"  # ask to speak louder
  if SNR < 6 dB:
    trigger: fallback_to_sms_flow
```

### Phonetic Variant Mapping
STT transcribes what it hears; NLU maps variants to canonical forms:

| Heard | Canonical | Notes |
|---|---|---|
| "Bambai" | Mumbai (BOM) | Historical name, common in UP/MP |
| "Bombay" | Mumbai (BOM) | English variant |
| "Dilli" | Delhi (DEL) | Common Hindi pronunciation |
| "Calcutta" | Kolkata (CCU) | Old name still used |
| "Madras" | Chennai (MAA) | Old name still used |
| "Banglore" / "Bengaluru" | Bangalore (BLR) | Both accepted |
| "kal" | tomorrow | Relative date |
| "parson" | day after tomorrow | Relative date |
| "agle hafte" | next week | Relative date |
| "subah" | 06:00–10:00 | Time band |
| "dopahar" | 12:00–16:00 | Time band |
| "shaam" | 17:00–21:00 | Time band |

### Streaming vs. Batch
```
Mode: STREAMING (not batch)
Why: Real-time conversation requires < 1.5 sec transcription latency
     Batch processing has 3–10 sec delay → kills conversational flow

Streaming config:
  interim_results: true    # show partial transcripts to agent for pre-processing
  single_utterance: true   # stop after pause (end of merchant's turn)
  silence_timeout: 1.2 sec # merchant pause = end of utterance
```

---

## Layer 2: Natural Language Understanding (NLU)

### Architecture
```
Base model:       BERT (bert-base-multilingual-cased)
Fine-tuning:      1,000 synthetic + 200 real merchant queries
Training data:    See nlu-intent-examples.md
Inference:        < 150ms on CPU (p95)
Framework:        Hugging Face Transformers + FastAPI wrapper
Deployment:       GCP Cloud Run (auto-scales with call volume)
```

### Intent Classification

| Intent | Example Utterances | Priority |
|---|---|---|
| `BOOK_FLIGHT` | "Delhi jaana hai", "Mumbai flight book karo" | Primary |
| `BOOK_TRAIN` | "Delhi train kab hai" | Post-MVP (decline gracefully) |
| `SELECT_OPTION_1` | "Pehla wala", "First one", "IndiGo wala" | Stage 2 |
| `SELECT_OPTION_2` | "Doosra", "Air India wala", "The cheaper one" | Stage 2 |
| `PAYMENT_CREDIT` | "Credit se", "Business credit use karo" | Stage 3 |
| `PAYMENT_WALLET` | "Wallet se", "Paytm wallet" | Stage 3 |
| `CONFIRM` | "Haan", "Yes", "Karo", "Done", "Confirmed", "Theek hai" | Stage 3 |
| `CANCEL_INTENT` | "Rehne do", "Cancel", "Nahi chahiye" | Any stage |
| `REPEAT_OPTIONS` | "Phir se batao", "Again please" | Stage 2 |
| `HUMAN_AGENT` | "Insaan se baat karni hai", "Transfer me", "Manager" | Any stage |
| `EXISTING_BOOKING` | "Cancel karna hai", "Change booking", "Refund" | Stage 1 |
| `CALLBACK_LATER` | "Baad mein call karta hoon", "Not now" | Any stage |
| `CLARIFY_PRICE` | "Kitna hai?", "Price kya hai?" | Stage 2 |
| `UNCLEAR` | (confidence < 0.60) | Any stage |

### Entity Extraction

**Entity types and extraction logic:**

```python
class TravelEntities:
    origin: str           # IATA code | None (infer from KYC if None)
    destination: str      # IATA code | required
    travel_date: date     # absolute or relative → resolved to date
    time_preference: str  # "morning" | "afternoon" | "evening" | None
    trip_type: str        # "one_way" | "round_trip" (default: one_way)
    cabin_class: str      # "economy" | "business" (default: economy)
    num_passengers: int   # default: 1 (MVP is single traveler only)

# Relative date resolution (examples):
"kal"           → date.today() + timedelta(days=1)
"parson"        → date.today() + timedelta(days=2)
"is hafte"      → nearest occurrence of [day_name] this week
"agla Monday"   → next Monday
"15 April"      → date(2026, 4, 15) [current year assumed]
"next week"     → date.today() + timedelta(weeks=1)
```

### Confidence Scoring
```python
CONFIDENCE_THRESHOLDS = {
    "proceed":         0.80,   # extract entity, continue flow
    "clarify":         0.60,   # ask for clarification
    "fallback_gui":    0.45,   # push to WhatsApp form
    "escalate_human":  0.30,   # 3 failures → human handoff
}

# Entity-level confidence (each entity scored independently)
if destination.confidence < 0.80:
    agent: "Aap kahan jaana chahte hain — destination confirm karein?"

if date.confidence < 0.80:
    agent: "Kab jaana hai — kal ya koi aur date?"
```

### Slot-Filling State Machine
```
REQUIRED_SLOTS = ["destination", "travel_date"]
OPTIONAL_SLOTS = ["origin", "time_preference", "trip_type"]

Filling sequence:
  1. Try to extract all slots from single utterance
  2. If destination missing: ask "Kahan jaana hai?"
  3. If date missing: ask "Kab jaana hai?"
  4. If origin missing: infer from merchant KYC city
     → Confirm: "Kanpur se hi jaana hai?"
  5. Time preference missing: default to morning for next-day trips,
     best available for same-day trips

Max slot-fill turns: 3
If slots still incomplete after 3 turns: push to WhatsApp form
```

---

## Layer 3: Dialogue Manager

### Architecture
```
Type:         Custom finite-state machine (FSM)
States:       GREETING → SEARCH → CONFIRM → INVOICE → SUMMARY → END
              + FALLBACK, HUMAN_HANDOFF, ERROR states
Storage:      Redis (session state, 30-min TTL per call)
Concurrency:  Each call gets isolated session; no cross-talk
Persistence:  State persisted to DB on each transition (call-drop recovery)
```

### State Transition Map

```
                    ┌─────────────────────────────┐
                    │          GREETING            │
                    │  KYC lookup · Auth · Intent  │
                    └──────────────┬──────────────┘
                                   │ intent captured
                    ┌──────────────▼──────────────┐
                    │           SEARCH             │◄── RETRY (max 2)
                    │  Slot-fill · API call · Opts │
                    └──────────────┬──────────────┘
                                   │ option selected
                    ┌──────────────▼──────────────┐
                    │          CONFIRM             │◄── RETRY (max 2)
                    │  Payment · Confirm · Book    │
                    └──────────────┬──────────────┘
                                   │ booking success
                    ┌──────────────▼──────────────┐
                    │           INVOICE            │
                    │  PNR · GST gen · WA delivery │
                    └──────────────┬──────────────┘
                                   │ invoice sent
                    ┌──────────────▼──────────────┐
                    │           SUMMARY            │
                    │  Recap · Exit · Analytics    │
                    └──────────────┬──────────────┘
                                   │ call ends
                                   ▼
                                  END

Parallel escape paths (from ANY state):
  CANCEL_INTENT     → SUMMARY (clean exit, no booking)
  HUMAN_AGENT       → HUMAN_HANDOFF
  3x NLU failure    → FALLBACK → HUMAN_HANDOFF
  API error (fatal) → ERROR → HUMAN_HANDOFF or SMS recovery
  Call drop         → DROPPED (save state to DB, send WA/SMS recovery)
```

### Turn Management
```python
class DialogueTurn:
    max_agent_sentence_length = 2    # sentences before listening
    max_silence_before_prompt = 4.0  # seconds before re-prompting
    re_prompt_max = 2                # times before escalation
    interruption_handling = "stop_and_listen"  # not "barge-in ignore"

    # Agent always yields after 1-2 sentences
    # Merchant can interrupt at any point → agent stops immediately
    # Dead air > 4 sec → re-prompt: "Kya aap wahan hain?"
    # Dead air > 8 sec → assume call quality issue → fallback SMS
```

### Context Carry-Forward
```python
# Within a call session:
session = {
    "merchant_id":       "M12345",
    "merchant_name":     "Ramesh Kumar",
    "credit_available":  47000,
    "wallet_balance":    2100,
    "gstin":             "09XXXXX5678",
    "last_booking":      {"route": "KNU-DEL", "date": "2025-11-12"},
    "current_stage":     "CONFIRM",
    "extracted_slots":   {"dest": "DEL", "date": "2026-04-15", "time": "morning"},
    "selected_flight":   {"id": "AI456", "price": 3800, "dep": "09:30"},
    "payment_method":    "credit",
    "retry_count":       0,
    "fallback_triggered": False,
    "wa_thread_id":      "WA_thread_abc123",
}

# Context enables:
# - "Kanpur se hi jaana hai?" (origin inferred + confirmed, not asked)
# - "Credit line ₹47K available hai" (pre-fetched, not re-fetched)
# - WhatsApp card state sync (agent and screen always in the same stage)
```

---

## Layer 4: Text-to-Speech (TTS)

### Provider & Voice Configuration
```yaml
provider:     Google Cloud Text-to-Speech
voice_name:   hi-IN-Neural2-D   # neutral, warm Hindi voice
language:     hi-IN with en-IN fallback for English words
speaking_rate: 1.15              # 15% faster than default — efficient not robotic
pitch:         0.0               # neutral pitch
volume_gain:   +2 dB             # slight boost for phone playback

# For English-dominant responses:
voice_fallback: en-IN-Neural2-C  # Indian English, professional
```

### Latency Budget
```
Text → Audio pipeline:
  NLU/Dialogue processing:   ~150ms
  Response text generation:  ~50ms
  TTS API call:              ~400ms
  Audio stream start:        ~100ms
  ─────────────────────────────────
  Total first-audio latency: ~700ms (target < 1 sec)

Optimization: Pre-generate common phrases as cached audio:
  - "Theek hai..." (cached)
  - "Ek second..." (cached)
  - "Booking ho gayi!" (cached)
  Cache hit → latency drops to ~200ms
```

### SSML (Speech Synthesis Markup) Usage
```xml
<!-- Slower + emphasis for PNR readout — character by character -->
<speak>
  Aapka PNR hai:
  <say-as interpret-as="characters">6E2F4G</say-as>
</speak>

<!-- Pause before presenting options — gives merchant time to listen -->
<speak>
  Do options hain.
  <break time="400ms"/>
  Pehla: IndiGo, subah aath baje, char hazaar do sau rupaye.
  <break time="300ms"/>
  Doosra: Air India, saade nau baje, teen hazaar aath sau.
  Kaunsa theek rahega?
</speak>

<!-- Price in spoken Hindi — not "3800" but "teen hazaar aath sau" -->
<speak>
  <say-as interpret-as="cardinal" language="hi-IN">3800</say-as>
  rupaye.
</speak>
```

### Number Verbalization Rules
```python
def verbalize_amount(amount: int, language: str) -> str:
    # Hindi
    if language == "hi":
        if amount < 1000:    return f"{amount} rupaye"
        if amount < 100000:  return f"{amount//1000} hazaar {amount%1000 or ''} rupaye"
        # 47000 → "saintaalees hazaar rupaye"

    # English
    if language == "en":
        return f"₹{amount:,}"  # ₹3,800

# PNR / GSTIN → always spell out character by character
def verbalize_code(code: str) -> str:
    return " ".join(list(code))  # "6E2F4G" → "6 E 2 F 4 G"
```

---

## Layer 5: Backend Integrations

### Integration Map

```
Dialogue Manager
      │
      ├── Paytm Merchant API    (KYC, profile, auth)
      ├── Paytm Travel API      (flight search, booking, PNR)
      ├── Paytm Credit API      (balance check, charge, decline handling)
      ├── Paytm Wallet API      (balance check, debit)
      ├── GST Validation API    (GSTIN status, invoice generation)
      ├── WhatsApp Business API (card send, PDF delivery, status)
      └── Analytics (Kafka)     (event streaming → BigQuery)
```

### API Specifications

**Paytm Merchant API**
```
GET /merchant/v1/profile?phone={number}
Response:
  {
    merchant_id, name, city, gstin, business_name,
    credit_limit, credit_available,
    wallet_balance, account_status,
    last_booking_route, last_booking_date
  }
Latency SLA: < 300ms (p95)
Auth: Internal mTLS
```

**Paytm Travel API**
```
GET /travel/v2/flights/search
Params: origin, destination, date, class, pax
Response:
  {
    flights: [{
      flight_id, airline, flight_number,
      departure, arrival, duration, stops,
      fare, fare_breakdown, seats_available
    }]
  }
Latency SLA: < 3 sec (p95) — must show results in < 5 sec
Fallback: If timeout > 5 sec → serve cached results for route + date (if < 15 min old)
```

**Paytm Credit API**
```
POST /credit/v1/charge
Body: { merchant_id, amount, booking_ref, idempotency_key }
Response:
  {
    status: "SUCCESS" | "DECLINED" | "PENDING",
    transaction_id, remaining_credit,
    decline_reason: "LIMIT_EXCEEDED" | "RISK_HOLD" | "PAYMENT_OVERDUE"
  }
Latency SLA: < 2 sec (p95)
Idempotency: Required — prevents double-charge on call drop/retry
```

**GST Validation API**
```
GET /gst/v1/validate?gstin={gstin}
Response:
  {
    status: "ACTIVE" | "SUSPENDED" | "CANCELLED" | "NOT_FOUND",
    legal_name, trade_name, state_code, registration_date
  }

POST /gst/v1/invoice/generate
Body: { merchant_id, booking_id, gstin, amount, hsn_code, business_name }
Response:
  {
    invoice_id, pdf_url, invoice_number,
    generation_time_ms, status: "SUCCESS" | "FAILED"
  }
Latency SLA: Validation < 1 sec; PDF generation < 5 sec
```

**WhatsApp Business API**
```
POST /wa/v2/messages
Body: {
  to: merchant_phone,
  type: "interactive" | "document" | "text",
  template: template_name,  # pre-approved WA template
  components: [...]
}
Delivery receipt: Webhook callback → session state update
SLA: Message delivered within 5 sec on 4G; 30 sec on 2G
```

### Circuit Breakers
```python
# Each API has a circuit breaker to prevent cascade failures

class CircuitBreaker:
    failure_threshold = 5       # failures in 60-sec window → OPEN
    recovery_timeout  = 30 sec  # after OPEN, try one request
    states: CLOSED → OPEN → HALF_OPEN → CLOSED

# What to do when each API trips:
TRAVEL_API_OPEN:   serve cached results (< 15 min old) or offer callback
CREDIT_API_OPEN:   fallback to wallet; log for retry; alert ops
GST_API_OPEN:      complete booking without invoice; generate async
WA_API_OPEN:       fallback to SMS for all companion messages
MERCHANT_API_OPEN: continue with guest flow (no personalization)
```

---

## Performance Summary

| Component | Target Latency (p95) | Target Availability |
|---|---|---|
| STT transcription | < 1.5 sec | 99.5% |
| NLU inference | < 150ms | 99.9% |
| Dialogue state update | < 50ms | 99.9% |
| TTS first audio | < 1 sec | 99.5% |
| Merchant KYC lookup | < 300ms | 99.9% |
| Flight search API | < 3 sec | 99.0% |
| Credit charge | < 2 sec | 99.5% |
| GST invoice generation | < 5 sec | 99.0% |
| WhatsApp delivery | < 5 sec (4G) | 98.5% |
| **End-to-end booking (Stage 1–5)** | **< 180 sec** | **98.0%** |

---

## Scalability Design

```
Call volume assumptions:
  Pilot:      ~50 concurrent calls (500 merchants × some calling same time)
  Scale:      ~3,000 concurrent calls (Year 1 peak)
  Peak hours: 9–11 AM and 5–7 PM IST (merchant planning windows)

Auto-scaling:
  STT/TTS:      Google Cloud managed (elastic)
  NLU service:  GCP Cloud Run (scale to 0 at night, fast cold start)
  Dialogue Mgr: Kubernetes HPA (scale on concurrent session count)
  Redis:        Cluster mode (3 replicas minimum for session state)
  APIs:         Paytm internal — capacity planning shared with platform team
```

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/03-ai-architecture*
