# Load Test Plan & Results — P12
**50 Concurrent Calls · Scenarios · Acceptance · Findings**
*Paytm Merchant Travel Desk | 05-launch-plan | CTO-Owned*

---

## Purpose

P12 is the single most critical exit gate of Week −2. If the stack cannot handle 50 concurrent calls below 3% error rate, dogfood does not start. This document defines the test plan, records results, and captures findings for remediation before Week −1.

---

## Acceptance Criteria (from 4-week-timeline.md)

- ✅ **Error rate < 3%** at 50 concurrent sessions (all categories combined)
- ✅ **End-to-end booking latency p95 < 180 sec** (matches FR-08 SLA)
- ✅ **No data loss** on call-drop simulation — session state recoverable via Redis
- ✅ **Graceful degradation** verified on each dependency failure

---

## Test Environment

| Component | Value |
|---|---|
| Environment | `staging` (mirrors prod-pilot topology) |
| Region | `asia-south1` |
| Traffic source | `k6` drivers + synthetic IVR caller bot (SIP UAC) |
| NLU test corpus | 200-sample holdout (not used in training) |
| Synthetic merchants | 100 seeded KYC records, realistic distributions |
| External APIs | Live staging endpoints (Paytm Travel/Credit/GST), WA sandbox |
| Test duration | 30 min per scenario |

---

## Test Tooling

### SIP Caller Bot
A small Go binary (`ivr-stress-bot`) that:
1. Opens a SIP call to the IVR gateway
2. Streams pre-recorded WAV utterances at configurable turn timing
3. Parses TTS responses via passive STT for assertions
4. Emits per-call metrics to Prometheus

### k6 Driver
For API-tier load (bypassing SIP) — used for isolated service testing and regression runs.

### Chaos Injection
`litmuschaos` for dependency failure simulation (kill Redis primary, throttle Credit API, drop WA).

---

## Scenarios

| # | Scenario | Concurrency | Duration | Expected Outcome |
|---|---|---|---|---|
| **S1** | Steady-state happy path (Hindi) | 50 calls | 30 min | <1% error, p95 <180s |
| **S2** | Steady-state happy path (English) | 50 calls | 30 min | <1% error |
| **S3** | Mixed language (Hinglish) | 50 calls | 30 min | <2% error (NLU sensitivity) |
| **S4** | Spike: 0 → 80 calls in 30 sec | 80 calls | 10 min | HPA scales; <5% transient error |
| **S5** | Sustained 2× pilot load | 100 calls | 60 min | Validates post-pilot headroom |
| **S6** | Credit API throttled (800ms added latency) | 50 calls | 20 min | Graceful; fallback to wallet flow |
| **S7** | Redis primary killed mid-run | 50 calls | 15 min | Failover <10s; no session loss |
| **S8** | WA API rejection (50% template failure injected) | 50 calls | 20 min | SMS fallback fires; calls complete |
| **S9** | STT degraded (noise-injected audio) | 50 calls | 20 min | Fallback flow triggers appropriately |
| **S10** | Call-drop flood (random disconnect 20% of calls) | 50 calls | 20 min | Resume-link delivery <10s; US-010 SLA |

---

## Results

### S1 — Steady-State Hindi (Happy Path)

| Metric | Target | Actual | Status |
|---|---|---|---|
| Error rate | <3% | 0.4% (6 of 1,432 calls) | ✅ |
| p50 booking latency | — | 92s | ✅ |
| p95 booking latency | <180s | 148s | ✅ |
| p99 booking latency | — | 172s | ✅ |
| Credit attach rate | — | 43% | ✅ |

### S2 — Steady-State English

| Metric | Target | Actual | Status |
|---|---|---|---|
| Error rate | <3% | 0.3% | ✅ |
| p95 booking latency | <180s | 141s | ✅ |

### S3 — Hinglish Code-Switch

| Metric | Target | Actual | Status |
|---|---|---|---|
| Error rate | <3% | 1.8% | ✅ |
| NLU misclassification | — | 7.2% | ⚠️ Over 5% internal target — see Finding F1 |
| p95 booking latency | <180s | 161s | ✅ |

### S4 — Spike (0 → 80 calls)

| Metric | Target | Actual | Status |
|---|---|---|---|
| Error rate | <5% | 3.1% | ✅ |
| HPA scale-up time | <90s | 74s | ✅ |
| Cold-start errors (NLU) | — | 18 of 24 spike calls affected | ⚠️ Finding F2 |

### S5 — 100 Concurrent (Post-Pilot Headroom)

| Metric | Target | Actual | Status |
|---|---|---|---|
| Error rate | <3% | 2.1% | ✅ |
| p95 booking latency | <180s | 167s | ✅ |
| Note | Pilot cap is 50 — this validates 2× burst headroom | | |

### S6 — Credit API Throttled (+800ms latency)

| Metric | Target | Actual | Status |
|---|---|---|---|
| Error rate | <3% | 1.2% | ✅ |
| Fallback-to-wallet triggered | — | 22% (matches expected) | ✅ |
| p95 booking latency | <180s | 176s (close to ceiling) | ⚠️ Finding F3 |

### S7 — Redis Primary Kill

| Metric | Target | Actual | Status |
|---|---|---|---|
| Session loss | 0 | 0 | ✅ |
| Failover time | <10s | 6.8s | ✅ |
| Calls ongoing at kill | 50 | All 50 completed | ✅ |

### S8 — WA Template Failure (50% injected)

| Metric | Target | Actual | Status |
|---|---|---|---|
| SMS fallback fire rate | 50% (match injection) | 49.6% | ✅ |
| Booking completion rate | >95% | 97.8% | ✅ |
| Invoice delivery (SMS link) | <30s | p95 24s | ✅ |

### S9 — STT Degraded (Noise-Injected)

| Metric | Target | Actual | Status |
|---|---|---|---|
| STT WER | >85% usable | 82.4% | ⚠️ Finding F4 |
| Fallback-to-GUI trigger rate | 5–20% | 14.3% | ✅ |
| Call completion rate | >85% | 89.1% | ✅ |

### S10 — Call-Drop Flood

| Metric | Target | Actual | Status |
|---|---|---|---|
| Resume-link delivery p95 | <10s | 6.2s | ✅ |
| Session recovery rate | >98% | 99.1% | ✅ |
| Double-booking incidents | 0 | 0 (idempotency held) | ✅ |

---

## Findings & Remediation Before Week −1

### F1 — Hinglish NLU Misclassification at 7.2%
- **Cause:** Synthetic training data underweights `en → hi` within same utterance (only 18% of mixed examples)
- **Fix:** Generate 120 additional Hinglish examples focused on mid-sentence code-switches; retrain NLU v1.0-b
- **Owner:** ML Engineer
- **Due:** Week −1 Tuesday
- **Blocker for dogfood?** No (still under 8% threshold for go) — but fix before Week 1

### F2 — Cloud Run NLU Cold-Start on Spike
- **Cause:** minInstances=1 insufficient when 80-call spike hits simultaneously
- **Fix:** Raise `minInstances` from 1 to 3; add 60-sec HPA cooldown
- **Owner:** Platform Engineer
- **Due:** Week −1 Monday
- **Blocker for dogfood?** Not for dogfood (no spike load), but required before prod-pilot

### F3 — p95 Booking Latency Approaching SLA Ceiling When Credit API Slow
- **Cause:** Credit API fallback path serially retries (500ms × 2) before falling to wallet
- **Fix:** Parallelize wallet-balance prefetch with credit-charge attempt; drop to wallet if credit not responsive in 1.2s
- **Owner:** Platform Engineer
- **Due:** Week −1 Wednesday
- **Blocker for dogfood?** No — degraded-API scenario is unlikely during dogfood

### F4 — STT WER Drops Below 85% in Noise-Injected Scenarios
- **Cause:** Noise gate threshold too permissive; bandpass filter attenuation insufficient on kirana shop background
- **Fix:** Tighten noise gate to -40 dBFS (from -45 dBFS); add adaptive bandpass based on SNR estimate
- **Owner:** AI/ML Engineer
- **Due:** Week −1 Thursday
- **Blocker for dogfood?** No (dogfood runs from quiet offices); required before Week 1 soft launch

---

## Go/No-Go Recommendation for Dogfood (Week −1)

**Recommendation: ✅ GO.**

- Core acceptance (S1, S2) passed with margin
- All Sev-1 chaos scenarios (S7, S10) passed
- Four findings (F1–F4) have owners and dates — none block dogfood
- Dogfood will itself validate real-merchant noise conditions (gap to S9 synthetic coverage)

Signed off: CTO, Engineering Lead, QA Lead, PM. Dated: Week −2 Friday.

---

## Deferred / Out-of-Scope for P12

- **1,000 concurrent calls** — reserved for post-pilot scale test
- **Multi-region failover** — single-region in pilot; DR strategy deferred to Phase 2
- **Sustained 24-hour soak test** — ran 6 hours; extend to 24h in post-pilot
- **Mobile network variability** — tested on 4G only; 2G/3G variance observed anecdotally in dogfood

---

*Owner: CTO · QA Lead: signed · Last updated: April 2026*
