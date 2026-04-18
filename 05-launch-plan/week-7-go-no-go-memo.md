# Week 7 Go/No-Go Decision Memo
**4-Week Pilot Results · Full Metric Review · Scale Decision**
*Paytm Merchant Travel Desk | 05-launch-plan | PM + GM Jointly Owned*

---

## Recommendation

**GO WITH CONDITIONS.**

Scale from 500 to 5,000 merchants across the existing 3 cities + expand to 10 new cities. Two conditions attached (see Section 6). Launch window: begin Month 2 of GO-phase.

---

## 1. Pilot Headline Numbers (Weeks 1–5)

| Metric | Target | Actual | Status |
|---|---|---|---|
| **North Star — Credit attach rate** | 40% | **41.3%** | ✅ |
| Call → booking conversion | 25% | **24.1%** | ⚠️ Near miss |
| Avg call duration | < 3 min | **2 min 58 sec** | ✅ |
| NPS | 50+ | **54** | ✅ |
| Booking failure rate | < 5% | **3.8%** | ✅ |
| Active merchants | 500 | **471** | ⚠️ (94%) |
| Total bookings (pilot) | — | **584** | 📊 |
| GMV (pilot) | — | **₹46.4 L** | 📊 |
| Credit default rate | < 3% | **Too early to measure (~30-day cycle)** | 📊 |

Four of five primary metrics on target. Conversion was 0.9 pp below target, discussed in Section 3.

---

## 2. North Star Metric Deep Dive

### Credit Attach Rate by Week

| Week | Rate | Cohort effect |
|---|---|---|
| Week 1 (50 merchants) | 38% | Early-adopter cohort, more exploratory |
| Week 2 (200 merchants) | 39% | Kanpur expansion — first-time credit users |
| Week 3 (350 merchants) | 41% | Surat added; higher credit-familiarity |
| Week 4 (500 merchants) | 43% | Jaipur added; patterns stabilizing |
| Week 5 (500 merchants) | 44% | Steady state |

**Clear trajectory upward as product stabilizes and merchants realize credit is frictionless in this flow.** Statistically significant improvement (p < 0.01) between Week 1 and Week 5.

### Credit Activation Flywheel Signal

| Signal | Observed |
|---|---|
| % of attach who were first-time credit users (not just first-time-travel-credit) | 27% |
| Avg repayment cycle observed to date | 22 days (vs. 30-day assumption — faster is better) |
| Repayment rate (for ~30-day matured bookings) | 94.2% |
| Merchants increasing their credit usage after first booking | 31% within 60 days |

Flywheel signal is strong. Credit utilization data during pilot has already improved Paytm's underwriting model (per Credit Risk team) for all merchant credit products, not just travel.

---

## 3. Conversion Shortfall Analysis (24.1% vs. 25%)

### Where We Lost

```
Actual funnel (Weeks 1–5, weighted):
100 Calls
 → 94 Intent captured (94%)  [Baseline: 95%]
 → 61 Options presented (65%) [Baseline: 50% — BETTER than baseline]
 → 40 Bookings initiated (66%) [Baseline: 70% — WORSE]
 → 33 Bookings completed (82%) [Baseline: 85% — slightly worse]
 → 24 Funnel conversion (24.1%)
```

**Primary driver:** Options-to-initiated dropped to 66% from 70% baseline.

**Root cause:** 15% of merchants engaged in "comparison shopping" — they asked about the same route on a second call 1–3 days later with different times/options. These merchants eventually converted in 60% of cases (within 7 days). **If we count delayed conversions within 7 days, true conversion = 27.4% (exceeds target).**

**Recommendation:** Adopt a 7-day attribution window for scale-phase metrics. Week 1 single-call conversion remains a useful leading indicator but is not the north-star-adjacent measure.

### What Held the Funnel Together

- Credit-nudge wording resolved faster than expected (57% credit attach at the payment step, 43% averaged with wallet fallback)
- Invoice-delivery latency stayed under 5 sec p95 — this was repeatedly cited in NPS promoter feedback

---

## 4. Regional Cohort Analysis

| City | Bookings | Conversion | Credit Attach | NPS | Avg Call |
|---|---|---|---|---|---|
| Kanpur | 232 | 22.8% | 38.7% | 51 | 3m 04s |
| Surat | 188 | 26.1% | 45.2% | 57 | 2m 46s |
| Jaipur | 164 | 24.4% | 42.1% | 54 | 3m 02s |

**Surat over-indexed** on all metrics — makes sense: higher digital literacy, higher credit familiarity, merchants already borrowing against textile inventory. Kanpur under-indexed slightly on conversion but matched NPS — suggesting the product works but the conversion drag is comparison-shopping behavior more prevalent in the kirana segment. Jaipur middle-of-pack; seasonality (handicraft events) produced 2 spike days of outbound-call conversion.

No city under-performed materially. No basis for excluding any city from scale-up.

---

## 5. Qualitative Learnings

### What Merchants Repeatedly Told Us (30 interviews × PM + Customer Ops)

1. **"The invoice is magic."** Top quoted feature. Not "voice". Not "credit". The GST invoice is what this product is actually remembered for — it's the hook.
2. **"Credit use is natural."** When offered first, 57% chose credit at the confirmation step. This dropped to 38% when agent mentioned wallet first (A/B test in Week 3).
3. **"I don't compare."** 60% of merchants said they took the first acceptable option rather than negotiating. The "2 options max" design is validated.
4. **"3 minutes is about right."** None said too fast; 4 said too slow. Median merchant reported no perception of 3-minute speed-promise — it just felt natural.
5. **"I'd recommend to friends."** NPS 54 with 60% promoters. Merchant-referral loop is plausible for Phase 2.

### What Didn't Work

1. **Proactive outbound call uptake was lower than expected.** 180 calls placed; 54 converted to booking (30%). Baseline assumption was 40%+. Primary cause: outbound calls felt intrusive to some merchants. Mitigation post-pilot: tighter opt-in + time-of-day-personalization.
2. **Code-switched voice responses from AI occasionally mis-lined.** E.g., agent said "IndiGo flight 6E-123 sixty-two rupees" instead of "six-hundred-and-twelve rupees." Lexicon training needed.
3. **Invoice delivery to BSNL 2G connections** sometimes took 28–48 seconds (vs. 5-sec SLA on 4G). Works for merchants, but worth flagging.

---

## 6. GO Conditions

### Condition 1: Fix Conversion Attribution Before Scale

Adopt 7-day attribution window for conversion metric. Update dashboards. Update all stakeholder reporting. This is a data-engineering change, not a product change.

**Owner:** Data Engineering Lead
**Due:** Before Month 2 GO-phase kickoff

### Condition 2: Retrain NLU with Pilot Transcripts Before Scale

NLU v1.0-b was trained on 1,200 examples (1,000 synthetic + 200 real). Pilot generated 584 additional real transcripts across 3 regional accents. Retrain v1.1 to incorporate. Rollout via canary (10% → 25% → 50% → 100% over 10 days).

**Owner:** ML Engineer + AI/ML Lead
**Due:** 3 weeks before 5,000-merchant scale

---

## 7. Decision Paths Considered

| Option | Evaluation | Recommendation |
|---|---|---|
| **GO — scale to 5,000 merchants, 13 cities** | 4 of 5 metrics clearly met; north star exceeded | ✅ CHOSEN (with 2 conditions) |
| GO WITH CONDITIONS — same scope, smaller additions first | Conservative; adds months | Considered, not chosen — flywheel loses momentum |
| PAUSE — re-pilot with fixes | No root-cause gap that pilot can't solve at scale | Not chosen |
| NO-GO — kill product | Zero evidence supports this; flywheel is working | Not chosen |
| **Extend pilot another 4 weeks** | Diminishing returns; product is stable | Not chosen |

---

## 8. Post-Pilot Roadmap Preview (Months 2–6 of GO Phase)

Per `06-impact-projection/post-mvp-roadmap.md`:
- **Month 2:** Scale to 5,000 merchants; activate 10 new cities
- **Month 4:** Train booking (IRCTC); debit/credit card payment (Phase 2)
- **Month 6:** Regional language — Gujarati + Marathi

The credit-data flywheel begins compounding faster at scale. Finance projections:
- Month 3 Month 1 GMV: ₹14 Cr
- Month 6 Month 1 GMV: ₹42 Cr
- Year 1 GMV: ₹1,440 Cr (confirms business case)

---

## 9. Risks Entering GO Phase

| Risk | Severity | Mitigation |
|---|---|---|
| Scale reveals edge cases not seen in pilot | Medium | Phased 500 → 1K → 2.5K → 5K rollout; HPA already tested to 100 concurrent, will scale to 500 |
| New-city voice data degrades NLU | Medium | NLU v1.1 retrain + continuous learning pipeline |
| Competitive response (MMT for Business expansion) | Low | 3-year moat on credit stack; worth monitoring quarterly |
| Credit default rate higher than modeled (3% assumed) | Medium-High | First matured cohort (Month 2) will give first real signal; Finance has contingency plan |
| Operational capacity at 5,000 merchants | Medium | 8 agents adequate for 500; need 20+ agents for 5,000 — hiring plan already started |

---

## 10. Sign-Off

| Role | Decision | Date |
|---|---|---|
| Product GM | ✅ GO WITH CONDITIONS | Week 7 |
| CTO | ✅ GO | Week 7 |
| CFO / Finance Head | ✅ GO | Week 7 |
| Chief Risk Officer | ✅ GO | Week 7 |
| Chief Legal Officer | ✅ GO | Week 7 |
| CEO office | Noted (no objection) | Week 7 |

---

## Appendix — Pilot Economic Summary (5 Weeks)

| Metric | Value |
|---|---|
| Total bookings | 584 |
| GMV | ₹46.4 L |
| Commission revenue (net) | ₹60,320 |
| Credit disbursed | ₹19.2 L |
| Credit interest accrued (approx, 30-day) | ₹28,400 |
| Cost of infrastructure (pilot) | ₹4.3 L |
| Net contribution (pilot) | −₹3.9 L (investing mode, as expected) |

Matches the "investing Year 1" expectation from `06-impact-projection/business-case.md`. Unit economics break even at ~5,000 merchants, which is exactly the Month 2 target.

---

*Owner: PM + Product GM · Distribution: CEO office, CFO, CTO, Risk, Legal, Engineering Head, Customer Ops Head, Board packet*

*Last updated: Week 7*
