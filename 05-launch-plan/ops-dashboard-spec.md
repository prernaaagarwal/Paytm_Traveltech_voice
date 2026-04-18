# Ops Dashboard Specification
**Looker Dashboard · Event Schema · Alert Rules · Access**
*Paytm Merchant Travel Desk | 05-launch-plan | Data-Eng-Owned*

---

## Purpose

The ops dashboard is the single source of truth for real-time pilot performance. PM, CTO, Eng Lead, Customer Ops, and Finance each have different questions; the dashboard is structured so each role gets their answer in under 30 seconds.

Dashboard name: `travel-voice-ops-pilot`
Location: Looker → Paytm instance → Folder `travel-voice`
Access: 12 named users (see Access section)

---

## Dashboard Layout (7 Panels)

### Panel 1: Call Funnel (Headline Metric)

Real-time funnel for today + rolling 7-day overlay.

```
┌───────────────────────────────────────┐
│ TODAY                 (as of 14:32)   │
│                                       │
│ Calls answered        ██████████  142 │
│ Intent captured       █████████   135 │
│ Options presented     ███████     101 │
│ Bookings initiated    █████        68 │
│ Bookings completed    ████         59 │
│ Invoices delivered    ████         58 │
│                                       │
│ Conversion: 41.5% (+3.2 vs 7d avg)   │
└───────────────────────────────────────┘
```

**Filters:** city, date range, inbound vs. outbound, language
**Refresh:** 2 min

### Panel 2: North Star — Credit Attach Rate

| Window | Rate | Δ |
|---|---|---|
| Today | 43.2% | +1.1 pp |
| Rolling 7-day | 42.1% | — |
| Pilot target | 40.0% | — |

Sparkline: daily values for the full pilot to date.

### Panel 3: Call Quality & AI Performance

4 tiles:
- **STT WER usable rate** (target > 85%) — gauge
- **NLU intent confidence p50** (target > 0.85) — gauge
- **Voice fallback rate** (target < 20%) — gauge
- **Avg call duration** (target < 3 min) — gauge

All gauges are color-coded green/amber/red per target thresholds.

### Panel 4: Failure Mode Breakdown

Bar chart of triggered failure modes today, vs. 7-day baseline:

```
FM-09 (no flights)       ████ 12  (baseline 8)
FM-10 (credit decline)   ███  9   (baseline 11)
FM-14 (GSTIN issue)      ██   5   (baseline 4)
FM-17 (call drop)        ██   4   (baseline 3)
FM-02 (STT noise)        █    3   (baseline 5)
Other                    █    2   (baseline 2)
```

Clicking a bar drills into the transcripts of those calls.

### Panel 5: Operations Health

| Signal | Current | Status |
|---|---|---|
| Active concurrent calls | 18 / 50 cap | 🟢 |
| Booking API latency p95 | 2.4 sec | 🟢 |
| Credit API latency p95 | 1.6 sec | 🟢 |
| GST API latency p95 | 640 ms | 🟢 |
| WA delivery rate | 98.7% | 🟢 |
| Redis session count | 18 active | 🟢 |
| Human handoff queue | 1 waiting | 🟢 |
| Human handoff avg wait | 32 sec | 🟢 |

Any ❌ (breach of target) auto-pages on-call + CTO.

### Panel 6: Merchant Cohort View

Breakdown by city + by new-vs-returning caller:

```
                KANPUR   SURAT   JAIPUR
Calls today      58       42      42
Conversion       40.2%   44.8%   40.1%
Credit attach    45.1%   42.0%   40.9%
Avg duration    3m 08s  2m 51s  3m 14s
Handoff rate     9.8%    7.2%   11.1%
```

### Panel 7: Financial Rollup (Week-to-Date)

For Finance + Product GM:
- GMV: ₹X lakh
- Commission accrued: ₹Y
- Credit disbursed: ₹Z
- Credit interest accrued (projected 30d): ₹W
- Cost per completed booking: ₹P

---

## Event Schema (20 Events)

All events → Kafka topic `travel-voice-events` → BigQuery `travel_voice.events` (partitioned by date).

Event logging is fire-and-forget: never blocks the call flow (per FR-07).

### Call Lifecycle Events (7)

| Event | Triggered When | Key Attributes |
|---|---|---|
| `call_started` | IVR picks up | `call_id, merchant_id_hash, channel (in/out), phone_mask` |
| `kyc_resolved` | KYC lookup completes | `call_id, resolution_latency_ms, hit (y/n)` |
| `stage_transition` | FSM advances | `call_id, from_stage, to_stage, elapsed_ms` |
| `call_dropped` | Connection lost | `call_id, last_stage, session_saved (y/n)` |
| `call_ended` | Normal end | `call_id, final_stage, duration_s, outcome` |
| `resume_link_sent` | Drop recovery | `call_id, delivery_latency_ms, channel (WA/SMS)` |
| `resume_link_tapped` | Merchant returns | `call_id, delta_from_drop_s` |

### AI Performance Events (4)

| Event | Triggered When | Key Attributes |
|---|---|---|
| `stt_result` | STT completes an utterance | `call_id, confidence, wer_estimate, noise_db` |
| `nlu_result` | NLU classifies utterance | `call_id, intent, entity_count, confidence, latency_ms` |
| `fallback_triggered` | Voice → GUI fallback | `call_id, trigger (stt_fail/nlu_fail/silence), stage` |
| `handoff_triggered` | Human handoff | `call_id, reason, wait_ms, resolved (y/n)` |

### Business Events (6)

| Event | Triggered When | Key Attributes |
|---|---|---|
| `search_executed` | Flight search fires | `call_id, route, results_count, latency_ms` |
| `option_selected` | Merchant picks flight | `call_id, option_rank, flight_id, price` |
| `payment_chosen` | Payment method picked | `call_id, method (credit/wallet), balance_at_pick` |
| `booking_attempted` | Booking API called | `call_id, idempotency_key, pnr (or null)` |
| `booking_completed` | PNR returned | `call_id, pnr, gmv, commission_net` |
| `invoice_delivered` | WA invoice receipt | `call_id, invoice_id, delivery_latency_ms` |

### System Events (3)

| Event | Triggered When | Key Attributes |
|---|---|---|
| `api_error` | External API failure | `call_id, api_name, status, retry_count` |
| `dnd_checked_outbound` | Pre-outbound DND check | `phone_hash, allowed (y/n), cache_hit (y/n)` |
| `recording_stored` | Call recording persisted | `call_id, size_bytes, retention_days` |

### PII Guarantees

- `phone_mask` = first 2 + last 4 digits (e.g., `98XXXXXX47`)
- `phone_hash` = SHA-256 of E.164 (reversible only with key held by DPO)
- `merchant_id_hash` = SHA-256 of internal merchant ID
- No full GSTIN; only last-4 digits where needed

---

## Alert Rules (Prometheus → PagerDuty)

### P0 — Immediate Page

| Alert | Condition | Notification |
|---|---|---|
| `call_failure_rate_high` | > 5% of calls failing in any 5-min window | On-call + CTO + CEO office |
| `redis_cluster_unhealthy` | Any primary/replica down > 60s | On-call + CTO |
| `credit_api_5xx` | > 10 5xx/min for 3 min | On-call + Credit Eng Lead |
| `data_breach_detected` | PII leak signal (egress monitoring) | CISO + CTO + Legal |
| `booking_double_charge` | Any idempotency violation detected | On-call + CTO + Finance |

### P1 — Page Within 15 Min

| Alert | Condition | Notification |
|---|---|---|
| `stt_wer_drop` | Rolling WER drops > 5 pp vs. 7d baseline | AI/ML Lead |
| `wa_delivery_drop` | WA delivery < 95% in 15-min window | Platform Eng |
| `handoff_queue_overflow` | Queue depth > 5 for 10 min | Customer Ops Lead |
| `credit_attach_drop` | Rolling 24h credit attach < 25% | PM |
| `booking_latency_breach` | p95 end-to-end > 180s for 10 min | Platform Eng |

### P2 — Slack Alert Only

| Alert | Condition |
|---|---|
| Pod restart (non-critical service) | Any restart event |
| Single API timeout > 3s (not p95 breach) | Per-request |
| Dashboard freshness lag > 10 min | Data pipeline signal |

---

## Access Control

| Name | Role | Access | Reason |
|---|---|---|---|
| CTO | View + admin | Full | Owner |
| PM (Prerna) | View + filter | Full | Product decisions |
| Eng Lead | View + drill | Full | Operational oversight |
| AI/ML Lead | View + drill | AI panels | NLU/STT monitoring |
| Platform Eng Lead | View + drill | System panels | Infra monitoring |
| Data Eng Lead | View + admin | Full | Dashboard owner |
| Customer Ops Lead | View | Handoff + merchant panels | Ops decisions |
| Finance Lead | View | Financial rollup | Revenue tracking |
| CISO | View | System + security events | Compliance |
| DPO | View | PII-masked only | Compliance audit |
| Risk Lead | View | Financial + fraud signals | Risk monitoring |
| Program Manager | View + filter | Full | Operational comms |

All other access: denied by default. No direct SQL access to BigQuery for non-DE roles (query through Looker).

---

## Dashboard Verification Before Launch

| Check | Status |
|---|---|
| All 20 events flowing to BigQuery | ✅ |
| Event-to-dashboard lag < 5 min | ✅ (3.4 min p95) |
| All 7 panels render < 3 sec | ✅ |
| Filters work on all panels | ✅ |
| 12 access roles tested | ✅ |
| P0/P1 alerts tested via synthetic trigger | ✅ |
| Mobile view functional (PM on the go) | ✅ |

---

## Known Limitations

1. **No cross-call session view** — current dashboard shows per-call; stitching across a merchant's call history is a Week 2 enhancement.
2. **No predictive metrics** — purely observational. Predictive (e.g., conversion-rate forecast) deferred to Phase 2.
3. **Financial rollup is end-of-day** — intraday financial accrual is estimated, not reconciled.
4. **Recording playback embedded** — available via separate `travel-voice-call-qa` tool (access-controlled; audit-logged).

---

*Owner: Data Engineering Lead · Co-signers: CTO, PM · Last updated: Week −1 Friday*
