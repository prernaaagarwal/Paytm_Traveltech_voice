# Biggest Risks — Post-Pilot Retrospective
**4 Identified Risks · How They Actually Played Out · Lessons**
*Paytm Merchant Travel Desk | 05-launch-plan | CTO-Owned*

---

## Purpose

Before launch, four risks were flagged as "biggest, attack first." This document audits how each actually played out through Week −2, Week −1, and the 4-week pilot. The goal is honest accounting, not a victory lap.

Format for each risk: **What we feared → What we did → What actually happened → Lesson.**

---

## Risk 1: Helpline Number Provisioning (6-Week Lead Time)

### What We Feared
A 6-week lead time on 1800-series provisioning was flagged as the single most blocking item — a slip would push the pilot back by the full slip duration. No amount of software work could compensate.

### What We Did
- Issued RFI to 4 vendors (Tata, Airtel, Exotel, Jio) on Week −6 Monday
- Pre-drafted 3 fallback options (existing 1800 with IVR branch, Exotel short-term + Tata migration, delay)
- Kept Exotel as explicit fallback on standby

### What Actually Happened
| Event | Date |
|---|---|
| RFI issued | Week −6 Mon |
| All 4 vendor responses received | Week −5 Wed |
| Tata selected + contract signed | Week −5 Fri |
| Number provisioned (staging SIP) | Week −2 Wed (5.5 weeks actual) |
| IVR integration live | Week −2 Fri |
| Production number rolled to merchants | Week 1 Mon |

Tata delivered on schedule. No fallback needed.

### Outcome
✅ **Risk did not materialize.** Vendor selection discipline (RFI + pre-drafted fallbacks) is the reason it didn't. This was not luck; it was process.

### Lesson
For any future product with a long-lead dependency, the fallback plan must be fully designed and priced before the primary path is accepted. The mere presence of the Exotel fallback gave Tata negotiating incentive to hit their date. We only used the fallback psychologically, but it paid for itself in the negotiation.

---

## Risk 2: Meta WhatsApp Template Approvals

### What We Feared
Meta's 7-day SLA is notoriously slippery in practice. A single rejected template could delay launch by a week or more. 8 templates × 2 languages = 16 submissions; any one of them landing in "re-review required" could cascade.

### What We Did
- Submitted all 16 templates on Week −3 Monday (earliest possible after language copy was ready)
- Prioritized UTILITY category to avoid MARKETING billing + stricter review
- Pre-reviewed every template against Meta's 2025 guidelines internally
- Had `Meta Business Solutions Partner` pathway identified for escalation

### What Actually Happened
| Event | Outcome |
|---|---|
| First submission (16 templates) | 14 approved within 48 hours |
| Templates #3 and #6 flagged | Meta classified as borderline MARKETING due to language (*"Book now, fares cheaper!"*) |
| Revised #3 and #6 | Removed all promotional language; resubmitted Tuesday |
| Revision approved | Same day (6 hours) |
| BUG-D04 fix to #3 in dogfood | Required Meta re-review post-change |
| Post-BUG-D04 re-approval | 6 hours |

Net: all 16 templates approved by Week −2 Wednesday. 4 total submissions/resubmissions; no delay to overall timeline.

### Outcome
✅ **Risk partially materialized but was contained.** 2 of 16 submissions required rework. The internal pre-review against Meta guidelines caught most issues before submission; the ones that slipped through were corrected fast.

### Lesson
Writing for Meta approval is a specific skill. We kept all first-round copy defensively bland on purpose. The two that failed were the ones where product tried to be a bit more marketing-friendly. Next time: the rule is "zero promotional language anywhere, ever, even if the product team thinks it's fine."

---

## Risk 3: GSTN API Rate Limits at 50 Concurrent Calls

### What We Feared
The GSTN portal is not a commercial-grade API. Published rate limits are 100 req/sec per key, but actual behavior under concurrent load is often inconsistent. If GSTN throttled during peak, we'd lose invoice generation — which is a core value prop.

### What We Did
- Built a 15-min Redis cache on GSTIN validation (same merchant → same result for 15 min)
- Designed an async-fallback path (booking completes, invoice queued) as FM-14
- Stress-tested GSTN in staging at 50 concurrent and 80 concurrent (S4 spike scenario)
- Built monitoring on GSTN success rate

### What Actually Happened
| Observation | Data |
|---|---|
| GSTN validation calls during pilot (raw) | 614 |
| GSTN validation calls after Redis cache dedup | 89 (86% cache hit rate) |
| GSTN validation failures | 4 (in a single 14-min outage during Week 1) |
| GSTN rate-limit response | Never observed |
| Async-fallback invocations | 4 (during the outage) |
| All 4 async invoices generated within 2h of outage recovery | ✅ |

### Outcome
✅ **Risk did not materialize on rate-limits; it did materialize on availability.** Rate limits were never a problem — the Redis cache crushed effective traffic by 86%. But GSTN had one planned outage (14 min) during pilot, and the async-fallback worked exactly as designed.

### Lesson
The risk we feared (rate limits) wasn't the risk that actually bit us (availability). The Redis cache was over-engineered for rate limits but exactly right for availability resilience. **Both problems had the same solution.** Convergent mitigation is good.

The pre-built FM-14 fallback handled availability exactly as spec'd. This is a case where upfront edge-case design paid off: no merchant saw degraded service, no engineer had to invent a fix at 2 AM.

---

## Risk 4: NLU Accuracy on Hinglish Code-Switching in Noisy Kirana Environments

### What We Feared
This was flagged as the "highest AI risk" pre-launch. Dogfooders from quiet offices would not produce data representative of actual merchant shops. Hinglish code-switching is statistically hard for BERT-scale models; Indian background noise is hard for STT. These two combined could collapse the product's voice-first promise in production.

### What We Did
- Over-weighted Hinglish in training data (480 of 1,000 examples = 40%)
- Ran S3 (Hinglish) and S9 (noise-injected) explicitly in P12 load test
- CTO personally placed test calls with loud music in S8 dogfood scenario
- Built fallback-to-WA-form trigger after 2 STT failures
- Retrain gate: if NLU accuracy drops >3 pp during pilot, stop and retrain

### What Actually Happened

| Observation | Pre-pilot Expectation | Actual |
|---|---|---|
| NLU accuracy (200-sample holdout) | > 90% | 93.2% (v1.0-b) |
| Hinglish accuracy specifically | Concern | 94.6% post-retrain |
| STT WER usable (quiet) | > 92% | 94.1% |
| STT WER usable (noisy — kirana) | Concern | **78% — below target** |
| Voice fallback rate (switch to WA form) | < 20% | 22% on noisy environments; 11% overall |
| Merchant "couldn't be heard" complaints in NPS | Concern | 4 of 30 interviews mentioned |

### The Part That Actually Broke

Kanpur kirana merchants calling from their shops during peak 11 AM–1 PM customer hours triggered the STT fallback 22% of the time. This is above our 20% threshold. The NLU was fine; **the STT was the problem, not the NLU.**

### What We Did Mid-Pilot
- Week 3: Tightened noise gate from −40 dBFS to −38 dBFS adaptively
- Week 4: Added a "ghost" silence-insertion in STT that asks merchant to speak after a cashier transaction completes
- Week 5: Introduced voice-biometric sanity check (does the caller sound like the registered merchant?)

Final voice fallback rate (Week 5): 19.4% — just under target.

### Outcome
⚠️ **Risk materialized. We hit the target at the end of the pilot, but only after 3 mitigations.** We misidentified which layer of the stack was at risk.

### Lesson

The critical miscalculation: we over-invested in NLU robustness and under-invested in STT noise handling. Our pre-launch mental model was "NLU is harder, so focus there." The actual reality: **STT in adverse acoustic conditions is the bottleneck of this product.**

For GO-phase scale, we are:
- Adding an STT re-training on 40 hours of real noisy kirana audio captured during pilot (opt-in merchants)
- Introducing per-merchant acoustic profiles (a merchant who calls from a consistently noisy environment gets more tolerant STT thresholds)
- Adding a pre-call "Aap kahan se baat kar rahe hain?" question — if merchant says "dukaan se" (shop), STT thresholds adjust

This was the one risk where we got the direction right but the specifics wrong. Better to learn this in a 500-merchant pilot than at 5,000.

---

## Summary Scorecard

| Risk | Pre-launch Fear | What Happened | Severity at Peak | Contained? |
|---|---|---|---|---|
| 1. Helpline provisioning | High | Vendor delivered on schedule | Low (fallback unused) | ✅ Fully |
| 2. Meta templates | Medium | 2 of 16 needed rework | Low | ✅ Fully |
| 3. GSTN API | High | Cache made it a non-issue; outage handled by FM-14 | Medium (for 14 min) | ✅ By design |
| 4. NLU Hinglish noise | High | NLU was fine; STT noise was the real problem | Medium (throughout pilot) | ⚠️ Mid-pilot fixes |

Three of four risks did not materialize because mitigations were built. The fourth materialized partially because the mitigation addressed the wrong layer. Net: the risk register was more right than wrong, and the pre-launch discipline (fallbacks, stress tests, explicit triggers) is what kept any of these from becoming launch-blockers.

---

## What to Carry Into GO Phase

1. **STT resilience is the #1 engineering priority for scale.** Not NLU, not dialog, not integrations.
2. **The FM-14 style "fallback to async" pattern should be the default for every external dependency** — cheap insurance.
3. **Meta approval discipline: zero promotional language, always.**
4. **Pre-launch vendor selection must always include a designed fallback**, even if the fallback is never used.
5. **Pilot scale is the right place to catch acoustic/accent variance.** Do not skip a pilot in new markets just because product is stable at current scale.

---

## Open Questions for Phase 2

1. Do we need a voice-biometric second factor to defend against voice cloning as merchants adopt the habit?
2. Does acoustic-profile personalization bleed into PII/discrimination risk (e.g., different experience for merchants in noisier neighborhoods)?
3. Is the "STT in a shop" problem solvable at scale by better hardware advice to merchants ("use earbuds"), or is it fundamentally a model problem?

These go into the Phase 2 product-risk register.

---

*Owner: CTO · Reviewed: PM, AI/ML Lead, Platform Lead · Last updated: Week 7*
