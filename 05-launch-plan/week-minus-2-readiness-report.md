# Week −2 Readiness Report
**CTO Status to Product GM — End of Infrastructure Sprint**
*Paytm Merchant Travel Desk | 05-launch-plan*

---

## TL;DR

All 12 Week −2 milestones (P01–P12) are closed. Load test passed with a 3.1 pp buffer against the 3% error-rate SLA. Four findings (F1–F4) have remediation plans dated within Week −1. CISO approved with one documented residual risk (F5). **Recommendation: proceed to Week −1 dogfood on schedule.**

---

## Milestone Status

| # | Milestone | DRI | Status | Notes |
|---|---|---|---|---|
| P01 | STT/TTS pipeline integrated end-to-end | AI/ML Lead | ✅ GREEN | Round-trip voice 2.4 sec (target <3). Phrase cache hit rate 38%. |
| P02 | NLU v1 deployed on Cloud Run | ML Engineer | ✅ GREEN | 91.4% intent accuracy on 200-sample holdout. Hinglish at 92.8% (mis at 7.2% — see F1). |
| P03 | Paytm Travel API integration live | Platform Eng | ✅ GREEN | Flight search p95 2.6 sec. Top 20 routes verified. |
| P04 | Paytm Credit API integration live | Platform Eng | ✅ GREEN | 15-min idempotency window confirmed (OQ-2 resolved). Auth-then-capture flow implemented (OQ-5 resolved). |
| P05 | GST validation + invoice PDF pipeline live | Finance Eng | ✅ GREEN | Validation p95 630ms. PDF p95 3.8 sec. CA panel approval dated Week −2 Thursday. |
| P06 | WhatsApp Business API templates approved | Platform Eng | ✅ GREEN | All 8 templates approved (6 on first submission; 2 revised once for MARKETING misclassification risk). |
| P07 | Helpline 1800 number provisioned | Telecom Ops | ✅ GREEN | Tata Communications delivered in 5.5 weeks. Number: 1800-XXX-0100 (placeholder). SIPS trunk tested. |
| P08 | Redis session state + call-drop recovery | Platform Eng | ✅ GREEN | Failover 6.8 sec. 99.1% recovery in S10. |
| P09 | Analytics pipeline live | Data Eng | ✅ GREEN | All 20 events → Kafka → BigQuery. End-to-end lag p95 3.4 min. |
| P10 | Security review complete | Security | ✅ GREEN (with F5 note) | See `security-review-checklist.md`. CISO signed. |
| P11 | Human agent queue integrated | Customer Ops | ✅ GREEN | Context packet rendered; warm transfer p95 41 sec (under 45-sec URGENT SLA). |
| P12 | Load test: 50 concurrent calls | QA Lead | ✅ GREEN | See `load-test-plan-and-results.md`. 0.4% error rate on S1. |

---

## Open Questions Retrospective

| # | Question | Outcome |
|---|---|---|
| OQ-1 | KNU airline partners | IndiGo direct + Air India 1-stop confirmed; launching with both. Vistara deferred (no KNU service). |
| OQ-2 | Credit idempotency window | 15-min window confirmed by Credit Eng; matches held-seat design. |
| OQ-3 | 1800 provisioning feasibility | 5.5 weeks actual (Tata). Primary path worked; Exotel fallback not needed. |
| OQ-4 | WA invoice template | PDF attachment template accepted by Meta on first submission. |
| OQ-5 | Credit charge on pending PNR | Two-phase auth-then-capture implemented. Release-on-fail tested. |
| OQ-6 | GSTIN API rate limit | 100 req/sec confirmed; 15-min Redis cache cuts effective load >95%. Headroom validated. |

---

## Findings Carried Into Week −1

From `load-test-plan-and-results.md`:

| # | Finding | Owner | Due | Blocks Dogfood? |
|---|---|---|---|---|
| F1 | Hinglish NLU mis at 7.2% | ML Engineer | Week −1 Tue | No |
| F2 | Cloud Run NLU cold-start on spike | Platform Eng | Week −1 Mon | No |
| F3 | p95 booking latency near ceiling under Credit API slowdown | Platform Eng | Week −1 Wed | No |
| F4 | STT WER <85% in synthetic noise | AI/ML Eng | Week −1 Thu | No |
| F5 | Raw STT transcript retention 7 days | Already approved | — | No (CISO-noted residual) |

**None of the four open findings block dogfood.** All have owners, dates, and fit inside the Week −1 buffer.

---

## Metrics Relative to SLA Targets

| Component | Target p95 | Observed p95 | Headroom |
|---|---|---|---|
| KYC lookup | 300ms | 214ms | +29% |
| STT transcription | 1,500ms | 1,120ms | +25% |
| NLU inference | 150ms | 94ms | +37% |
| Flight search API | 3,000ms | 2,610ms | +13% |
| Credit charge | 2,000ms | 1,480ms | +26% |
| GST validation | 1,000ms | 630ms | +37% |
| Invoice PDF generation | 5,000ms | 3,820ms | +24% |
| WA message delivery | 5,000ms | 2,180ms | +56% |
| TTS first audio | 1,000ms | 710ms | +29% |
| **End-to-end booking** | **180s** | **148s** | **+18%** |

End-to-end latency has a 32-sec buffer against the SLA, which is healthy. Component headroom is tightest on flight search (airline-side dependency) — a known risk for peak hours.

---

## Costs Entering Pilot (Estimated)

| Bucket | Week −2 burn | Monthly run-rate at pilot scale |
|---|---|---|
| GCP (GKE + Cloud Run + Redis + BigQuery) | ₹3.2 L | ₹8.4 L/mo |
| Google STT/TTS | ₹0.4 L | ₹1.6 L/mo (pilot volume) |
| Telecom (Tata SIPS + 1800 rent) | ₹0.6 L | ₹1.8 L/mo |
| WhatsApp (template-based UTILITY) | ₹0.1 L | ₹0.4 L/mo |
| **Total** | **₹4.3 L** | **₹12.2 L/mo** |

At pilot scale (500 merchants × ~3 trips/year / 12 mo ≈ 125 bookings/mo), per-booking infra cost is ~₹978 — well below the ₹48.80 per-booking target in the business case because fixed-cost dominated at pilot volume. Unit economics reach target at ~5,000 merchants (Month 4 projected).

---

## Risks Entering Week −1

| Risk | Severity | Mitigation |
|---|---|---|
| NLU performance drop on real merchant accents not seen in training | Medium | Dogfood cohort spans 5 regional accents; retrain gate triggered if accuracy drops >3 pp |
| Airline API degraded during peak booking hours | Medium | 15-min cache in front of flight search; partial results acceptable |
| WA delivery failures on 2G networks in Tier-2 cities | Low-Medium | SMS fallback tested; dogfood will validate actual field conditions |
| Human agent not following context packet (skips to "repeat your issue") | Low | Mystery-call QA planned for dogfood week |
| Merchant voice biometric spoofing | Low | Residual risk accepted by CTO; post-pilot evaluation of voice-biometric 2FA |

---

## Recommendation

**✅ PROCEED TO WEEK −1 DOGFOOD ON SCHEDULE.**

Signed:

- **CTO** — Engineering readiness confirmed. All findings have owners and dates.
- **Engineering Lead** — All 12 P-milestones closed; code frozen at `v1.0.0-rc1`.
- **QA Lead** — Load test passed with margin; regression suite green.
- **CISO** — Security review approved with documented residual risk (F5).
- **Program Manager** — Daily ops standup calendar confirmed; escalation paths live.

Distribution: Product GM, Eng Head, Finance, Risk, Legal, Customer Ops Head.

---

## Open Items for Product GM Review

1. **Dogfood merchant list** — PM to confirm 20 internal staff by Monday EOD.
2. **Soft-launch merchant list** — PM to finalize 50 Tier-2 merchants by Week −1 Wednesday.
3. **NPS survey script (hi_IN + en_IN)** — final wording pending Product GM sign-off.
4. **Press/comms plan** — no external comms during pilot per agreed strategy; confirm no change.

---

*Author: CTO · Co-signers: Eng Lead, QA Lead, CISO, PM · Last updated: April 2026 · Repo: paytm-merchant-travel-desk/05-launch-plan*
