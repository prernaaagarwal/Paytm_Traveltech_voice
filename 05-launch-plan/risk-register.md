# Risk Register
**13 Risks · Likelihood · Impact · Mitigations · Owners · Status**
*Paytm Merchant Travel Desk | 05-launch-plan*

---

## Risk Scoring Framework

```
Likelihood:   1 = Rare (<5%)   2 = Unlikely (5–20%)   3 = Possible (20–40%)
              4 = Likely (40–60%)   5 = Almost Certain (>60%)

Impact:       1 = Negligible   2 = Minor   3 = Moderate
              4 = Major        5 = Critical (kills product or causes legal action)

Risk Score:   Likelihood × Impact
              1–5: Low (green)   6–12: Medium (yellow)   13–25: High (red)
```

---

## Risk Register

### R-01: STT Accuracy Degrades in Noisy Kirana Shops
**Category:** AI / Technical
**Risk Score:** 4 × 4 = **16 (High 🔴)**

| Field | Detail |
|---|---|
| **Description** | Hindi STT models trained on clean audio perform poorly in high-noise environments (fan noise, customer chatter, street sounds). Kirana shops in Tier-2 cities are among the noisiest environments for voice tech. |
| **Trigger** | WER > 20% on calls from shop environments. Voice fallback rate > 25%. |
| **Impact if unmitigated** | Merchants stuck in retry loops → abandon call → NPS collapses → kill criterion K6 triggered |
| **Likelihood (pre-mitigation)** | 4 — Highly likely in Tier-2 shop environments |
| **Impact** | 4 — Major: directly kills conversion rate |
| **Mitigation 1** | Deploy real-time audio pre-processing (noise gate, echo cancellation, SNR monitor) before STT input. Target: -15 dB noise reduction. |
| **Mitigation 2** | STT confidence threshold dynamically adjusted when SNR < 12 dB — lower threshold triggers WA fallback earlier (fewer failed retries). |
| **Mitigation 3** | Collect 200 real kirana shop audio samples in Week 1 dogfood → fine-tune STT model on noisy data before Week 2 scale-up. |
| **Mitigation 4** | WA form fallback triggered at 2nd STT failure — merchant can complete booking without voice. |
| **Residual score post-mitigation** | 2 × 3 = **6 (Medium 🟡)** |
| **Owner** | ML Engineering Lead |
| **Review cadence** | Daily during Week 1; Weekly thereafter |
| **Early warning signal** | Voice fallback rate > 18% on daily dashboard |

---

### R-02: Credit Attachment Rate Misses 40% Target
**Category:** Product / Business
**Risk Score:** 3 × 4 = **12 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | Merchants may default to wallet even with credit prompt, due to distrust of credit for travel, mental accounting ("credit is for supplies, not flights"), or lack of credit awareness. Credit attach is the north star metric — missing it undermines the business case. |
| **Trigger** | Credit attach rate < 30% after Week 2 |
| **Impact if unmitigated** | Revenue drops from ₹33 Cr → ~₹20 Cr (Year 1); strategic value of product questioned |
| **Likelihood (pre-mitigation)** | 3 — Possible; credit-for-travel is a new behavior |
| **Impact** | 4 — Major: kills unit economics |
| **Mitigation 1** | Stage 3 credit nudge: "Credit se karein? ₹47,000 available hai" — explicit prompt before wallet option. A/B test nudge language in Week 2. |
| **Mitigation 2** | Proactive credit education: WA message to all pilot merchants before pilot starts — "Did you know you can use your ₹{limit} Business Credit for flights?" |
| **Mitigation 3** | If 2-week credit attach < 30%: escalate to credit team — consider 1% cashback on first travel credit booking as activation incentive. |
| **Mitigation 4** | CRM flag merchants who choose wallet: trigger post-booking WA message — "Next time, use Business Credit — pays in 30 days, keeps cash free." |
| **Residual score post-mitigation** | 2 × 4 = **8 (Medium 🟡)** |
| **Owner** | Product Manager (PM) + Credit Business Team |
| **Review cadence** | Every 3 days during pilot |
| **Early warning signal** | Credit attach < 32% in first 100 bookings |

---

### R-03: Merchant Trust in Voice Booking for ₹5K+ Transactions
**Category:** Product / Behavioral
**Risk Score:** 3 × 3 = **9 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | Merchants are accustomed to seeing a screen confirmation before spending ₹5K+. Committing ₹5,000–₹8,000 based solely on hearing an AI voice may feel risky, especially for first-time users. |
| **Trigger** | Stage 3 → Stage 4 drop-off > 40% (merchants confirm verbally but don't follow through) |
| **Impact if unmitigated** | Conversion collapses at confirmation stage; bookings made then immediately cancelled |
| **Likelihood** | 3 — Plausible for first-time users; likely decreases with repeat usage |
| **Impact** | 3 — Moderate: recoverable with UX changes |
| **Mitigation 1** | WhatsApp screen shows booking details + credit balance + GSTIN before verbal confirm is required — visual verification reduces hesitation. |
| **Mitigation 2** | Agent reads total amount clearly + confirms payment method + pauses for merchant to look at screen before prompting "Confirm karein?" |
| **Mitigation 3** | First-booking trust-building: "Paytm ka 100% money-back guarantee — agar koi problem aayi toh full refund milega." (Within 24 hours for cancelled bookings.) |
| **Mitigation 4** | Collect NPS specifically on "How confident did you feel about the booking?" — track this sub-metric to identify trust signals. |
| **Residual score post-mitigation** | 2 × 3 = **6 (Medium 🟡)** |
| **Owner** | Product Manager |
| **Review cadence** | Weekly; check conversion funnel Stage 3→4 specifically |
| **Early warning signal** | Stage 3 → Stage 4 drop-off > 35% in first 50 bookings |

---

### R-04: WhatsApp Business API Template Rejection by Meta
**Category:** Technical / Dependency
**Risk Score:** 2 × 4 = **8 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | Meta must approve all WhatsApp Business API message templates. Approval takes 7–10 days and can be rejected for policy reasons (financial content, Indian regulations). If templates are rejected, the companion screen cannot function. |
| **Trigger** | Any of the 8 WA templates rejected or delayed past Week −2 |
| **Impact if unmitigated** | No WA companion → voice-only flow → lower trust → lower conversion → may need to delay launch |
| **Likelihood** | 2 — Unlikely with careful template design, but plausible |
| **Impact** | 4 — Major: delays launch by 2–3 weeks |
| **Mitigation 1** | Submit all templates 3 weeks before launch (Week −4 target). |
| **Mitigation 2** | Template design reviewed by WhatsApp Business partner (avoid sensitive financial language that triggers rejection). |
| **Mitigation 3** | SMS fallback designed and tested independently — if WA templates rejected, SMS-only flow is the fallback for pilot. |
| **Mitigation 4** | Templates staged: submit booking + invoice templates first (critical path). Options and summary templates are lower priority. |
| **Residual score post-mitigation** | 1 × 4 = **4 (Low 🟢)** |
| **Owner** | Product Manager + Platform Engineering |
| **Review cadence** | Daily during template approval window |
| **Early warning signal** | Template not approved within 7 days of submission |

---

### R-05: GST Invoice Compliance Failure
**Category:** Legal / Regulatory
**Risk Score:** 2 × 5 = **10 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | Auto-generated GST invoices must comply exactly with Indian GST regulations (correct HSN code 996411, correct tax rate, legal entity name matching GSTIN registration, sequential invoice numbering). A compliance error could expose Paytm and merchants to tax liability. |
| **Trigger** | CA review flags non-compliance; or any merchant receives a GST notice related to Paytm-generated invoice |
| **Impact if unmitigated** | Regulatory action; merchant financial harm; feature must be disabled; trust destroyed |
| **Likelihood** | 2 — Unlikely if designed correctly; possible due to edge cases |
| **Impact** | 5 — Critical: legal/regulatory exposure |
| **Mitigation 1** | Invoice format reviewed and approved by 3 independent CAs before launch (completed in Dogfood week). |
| **Mitigation 2** | Hardcode HSN 996411 (domestic air transport) — no dynamic HSN selection in MVP. |
| **Mitigation 3** | GSTIN real-time validation before booking confirms — never generate invoice for suspended/invalid GSTIN. |
| **Mitigation 4** | Invoice numbering: sequential per merchant, never reused. Test for idempotency on retry flows. |
| **Mitigation 5** | Legal sign-off from Paytm's in-house tax counsel on invoice template before dogfood. |
| **Residual score post-mitigation** | 1 × 4 = **4 (Low 🟢)** |
| **Owner** | Finance Engineering + Tax Counsel |
| **Review cadence** | One-time pre-launch; monthly audit post-launch |
| **Early warning signal** | Any CA feedback during dogfood flagging format issues |

---

### R-06: Paytm Travel API Inventory Gaps on Tier-2 Routes
**Category:** Product / Dependency
**Risk Score:** 4 × 3 = **12 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | Tier-2 city routes (Kanpur–Delhi, Surat–Mumbai, Jaipur–Delhi) may have limited inventory, price parity issues, or API gaps compared to major metro routes. If merchants consistently get "no flights" or prices higher than MMT, the product fails the value test. |
| **Trigger** | No-flights rate > 20% on top 10 merchant routes OR prices consistently > 5% above competing OTAs |
| **Impact if unmitigated** | Merchants compare prices with MMT, find Paytm more expensive → churn → negative word of mouth |
| **Likelihood** | 4 — Likely; Tier-2 routes have less competition and fewer API partners |
| **Impact** | 3 — Moderate: recoverable by expanding inventory partnerships |
| **Mitigation 1** | Pre-pilot route audit: check inventory coverage for top 20 city pairs used by pilot merchants. Confirm coverage > 90% before launch. |
| **Mitigation 2** | Price parity check: spot-check top 5 routes daily vs. MMT consumer. Escalate immediately if > 5% gap. |
| **Mitigation 3** | Add Amadeus GDS API as secondary inventory source (in addition to direct airline APIs) to fill coverage gaps. |
| **Mitigation 4** | For "no flights on this route" cases, agent offers connecting flights — maintains booking possibility even if direct is unavailable. |
| **Residual score post-mitigation** | 2 × 3 = **6 (Medium 🟡)** |
| **Owner** | Travel Partnerships + Platform Engineering |
| **Review cadence** | Daily no-flights rate monitoring |
| **Early warning signal** | No-flights rate > 15% on any single city pair for 3 consecutive days |

---

### R-07: DND / TRAI Compliance on Proactive Outbound Calls
**Category:** Legal / Regulatory
**Risk Score:** 3 × 4 = **12 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | Proactive outbound calls to merchants must comply with TRAI's Do Not Disturb (DND) regulations. Calling DND-registered numbers, calling outside permitted hours, or calling too frequently could result in TRAI fines, telecom operator blacklisting, and reputational damage. |
| **Trigger** | Any merchant complaint to TRAI about unwanted calls; or telecom operator blocks outbound number |
| **Impact if unmitigated** | Regulatory fine; outbound calling number blacklisted (kills proactive feature permanently) |
| **Likelihood** | 3 — Possible; DND compliance has many edge cases |
| **Impact** | 4 — Major: kills outbound feature; potential regulatory action |
| **Mitigation 1** | DND registry check before every outbound call — block if merchant on TRAI DND list. |
| **Mitigation 2** | Calling hours strictly enforced: 9 AM–7 PM only, no weekends/holidays. |
| **Mitigation 3** | Opt-in requirement: only call merchants who explicitly consented to "travel alerts" during onboarding. |
| **Mitigation 4** | Easy opt-out: "Don't call me again" → immediate CRM DND flag, no retry. 90-day suppression on any declined call. |
| **Mitigation 5** | Legal review of TPAI (Telemarketing Prior Approval Intent) consent language in onboarding flow before launch. |
| **Residual score post-mitigation** | 1 × 4 = **4 (Low 🟢)** |
| **Owner** | Legal + Customer Operations |
| **Review cadence** | One-time pre-launch legal review; monthly compliance audit |
| **Early warning signal** | First opt-out complaint received — audit all recent outbound calls immediately |

---

### R-08: Human Agent Capacity Insufficient for Pilot Scale
**Category:** Operations
**Risk Score:** 3 × 3 = **9 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | If AI handoff rate is higher than expected, or call volume exceeds projections, the human agent queue will overflow. Long waits destroy merchant experience and generate negative word-of-mouth among the pilot cohort. |
| **Trigger** | Human handoff rate > 20%; queue wait time > 5 min sustained for > 1 hour |
| **Impact if unmitigated** | Merchants wait > 5 min → hang up → negative NPS → trust damage in pilot cohort |
| **Likelihood** | 3 — Possible in Week 1 (learning curve) and during city scale-ups |
| **Impact** | 3 — Moderate: recoverable if detected fast |
| **Mitigation 1** | Pre-staff: 8 dedicated agents for pilot (vs. shared pool). 2 agents on standby at all times during calling hours. |
| **Mitigation 2** | Real-time queue dashboard with PagerDuty alert if wait > 3 min. |
| **Mitigation 3** | Train agents on all 5 handoff trigger types and context packet interpretation before Week −1. |
| **Mitigation 4** | Callback option always offered when wait > 2 min — reduces abandonment without requiring more agents. |
| **Mitigation 5** | If AI nlu_failure rate decreases over time (feedback loop), handoff rate should also decrease week-over-week. |
| **Residual score post-mitigation** | 2 × 2 = **4 (Low 🟢)** |
| **Owner** | Head of Customer Operations |
| **Review cadence** | Daily queue metrics review |
| **Early warning signal** | Handoff rate > 15% in first 3 days of any new city cohort |

---

### R-09: Merchant Data Privacy / PII Breach
**Category:** Legal / Regulatory / Trust
**Risk Score:** 2 × 5 = **10 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | The product processes highly sensitive data: voice recordings, travel itineraries, GSTIN, credit balances, and business patterns. A breach of call recordings or merchant financial data would be catastrophic. |
| **Trigger** | Any unauthorized access to call recordings, merchant profiles, or booking data |
| **Impact if unmitigated** | Kill criterion K4 triggered immediately. Legal/regulatory action. Trust destroyed permanently. |
| **Likelihood** | 2 — Unlikely with proper controls; but high-value data makes it a target |
| **Impact** | 5 — Critical: no recovery path |
| **Mitigation 1** | Call recordings: encrypted at rest (AES-256), deleted after 30 days (regulatory minimum), access restricted to security-cleared roles only. |
| **Mitigation 2** | API authentication: mTLS for all internal APIs; no API keys in code; secrets managed via GCP Secret Manager. |
| **Mitigation 3** | PII minimization: agent screen shows last 4 of GSTIN, masked phone numbers; full data only accessible via secure admin portal. |
| **Mitigation 4** | Security penetration test completed before Week −2. All critical findings remediated before launch. |
| **Mitigation 5** | PDPA (India Personal Data Protection Act) compliance review: data processing consent in merchant onboarding; right to deletion workflow. |
| **Mitigation 6** | Incident response plan documented: breach detected → isolate system → notify DPO → regulatory disclosure within 72 hours. |
| **Residual score post-mitigation** | 1 × 5 = **5 (Low 🟢)** |
| **Owner** | Security Lead + DPO |
| **Review cadence** | Pre-launch security review; quarterly thereafter |
| **Early warning signal** | Any anomalous access log pattern; any security alert from GCP |

---

### R-10: Competitor Launches Similar Feature During Pilot
**Category:** Market / Strategic
**Risk Score:** 2 × 3 = **6 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | MakeMyTrip, PhonePe, or another player announces or launches an SMB-focused travel feature during our 4-week pilot, reducing the uniqueness of the product and potentially cherry-picking our pilot merchants. |
| **Trigger** | Any competitor press release or product launch targeting SMB travel/GST during pilot period |
| **Impact if unmitigated** | Pilot merchants have an alternative to compare; reduces urgency to adopt Paytm; raises investor questions about defensibility |
| **Likelihood** | 2 — Unlikely in a 4-week window; but strategic timing risk is real |
| **Impact** | 3 — Moderate: manageable if Paytm's moat (credit + GST + voice + KYC) is genuinely differentiated |
| **Mitigation 1** | IP moat is structural, not first-mover. Even if a competitor announces, they cannot replicate all 4 rails (credit + GST + KYC + travel) in months. |
| **Mitigation 2** | Monitor competitor product announcements weekly (PR Newswire, TechCrunch India, LinkedIn). |
| **Mitigation 3** | Accelerate pilot if competitive signal detected — move Go/No-Go decision earlier. |
| **Mitigation 4** | Merchant lock-in during pilot: ensure merchants have active bookings and positive experience before any competitor can reach them. |
| **Residual score post-mitigation** | 1 × 3 = **3 (Low 🟢)** |
| **Owner** | Product Manager + Strategy Team |
| **Review cadence** | Weekly competitive scan |
| **Early warning signal** | Any competitor funding announcement or product teaser in SMB travel space |

---

### R-11: Flight API Price Parity — Merchants Comparison-Shop Mid-Call
**Category:** Product / Conversion
**Risk Score:** 3 × 3 = **9 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | Merchants may simultaneously check MMT or Cleartrip on their phone while on the Paytm call, discover a cheaper price, and abandon the call. This is the "leakage" scenario. |
| **Trigger** | Stage 2 → Stage 3 drop-off > 35%; qualitative: merchants citing "found cheaper elsewhere" |
| **Impact if unmitigated** | Conversion lower than baseline; core value proposition undermined |
| **Likelihood** | 3 — Tech-savvy merchants (Priya persona) are likely to do this |
| **Impact** | 3 — Moderate: affects conversion, not catastrophic |
| **Mitigation 1** | Price parity goal: within 3% of best available OTA price. Travel partnerships team to negotiate. |
| **Mitigation 2** | Value framing: agent explicitly mentions GST invoice and credit as part of the price — "₹3,800 with GST invoice and 30-day credit. MMT nahi deta yeh." |
| **Mitigation 3** | Speed is the moat: 3-min booking vs. 8-min MMT booking — for time-sensitive merchants, speed beats price. |
| **Mitigation 4** | Post-call survey question: "Did you check prices elsewhere?" — quantify this behavior. |
| **Residual score post-mitigation** | 2 × 2 = **4 (Low 🟢)** |
| **Owner** | Product Manager + Travel Partnerships |
| **Review cadence** | Weekly Stage 2→3 drop-off analysis |
| **Early warning signal** | Stage 2→3 drop-off > 30% for 5+ consecutive days |

---

### R-12: NLU Model Bias — Underperforms for Certain Merchant Profiles
**Category:** AI / Fairness
**Risk Score:** 2 × 3 = **6 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | If training data is skewed (e.g., mostly male voices, mostly Kanpur Hindi dialect, mostly younger speakers), the NLU model may underperform for merchants who don't fit the majority profile — creating an unfair product experience. |
| **Trigger** | NLU failure rate > 15% for any demographic subset identified in pilot data |
| **Impact if unmitigated** | Certain merchant segments get worse service → biased product → ethical and business issue |
| **Likelihood** | 2 — Possible; training data diversity is hard to guarantee |
| **Impact** | 3 — Moderate: fixable with targeted data collection |
| **Mitigation 1** | Training data diversity: ensure synthetic examples cover multiple Hindi dialects (UP, Rajasthani, Gujarati-accented Hindi), multiple age groups, multiple shop noise levels. |
| **Mitigation 2** | Pilot demographic tracking: log age range, city, gender where available — monitor NLU failure rate by group. |
| **Mitigation 3** | Monthly bias audit: test NLU model against demographically balanced test set. |
| **Mitigation 4** | WA fallback ensures no merchant is permanently excluded — even if voice NLU fails, booking can complete. |
| **Residual score post-mitigation** | 1 × 2 = **2 (Low 🟢)** |
| **Owner** | ML Engineering Lead |
| **Review cadence** | Weekly during pilot; monthly post-launch |
| **Early warning signal** | NLU failure rate more than 5 points higher in one city vs. others |

---

### R-13: Merchant Dropout — Pilot Merchants Stop Using After First Call
**Category:** Product / Retention
**Risk Score:** 3 × 3 = **9 (Medium 🟡)**

| Field | Detail |
|---|---|
| **Description** | Pilot merchants try the product once, have a neutral-to-negative experience, and revert to MMT for subsequent trips. This is especially risky for infrequent travelers (2–3 trips/year) — they may not have a second chance to try within the pilot window. |
| **Trigger** | Repeat booking rate < 8% within 4-week pilot; or > 30% of merchants who booked once never call back |
| **Impact if unmitigated** | Retention signal is weak → scale-up economics don't work → GO WITH CONDITIONS at best |
| **Likelihood** | 3 — Possible; first-time voice booking is a high-friction behavior change |
| **Impact** | 3 — Moderate: affects retention projection but not immediate survival |
| **Mitigation 1** | Post-first-booking WA message (24 hours after booking): "Booking ready — here's how to use your GST invoice. Next trip, call us again." (Reinforcement.) |
| **Mitigation 2** | Proactive outbound at 30 days: "Aapki pichli trip kaisi rahi? Agli trip ke liye abhi book karein." (Re-engagement.) |
| **Mitigation 3** | Trip reminder: if merchant's travel date is known, send WA on morning of flight — "Safe travels! Your PNR is XXX." (Builds habitual association with Paytm.) |
| **Mitigation 4** | Track and investigate: PM personally calls 10 non-repeat merchants at 3 weeks to understand why they haven't called back. |
| **Residual score post-mitigation** | 2 × 2 = **4 (Low 🟢)** |
| **Owner** | Product Manager + CRM Team |
| **Review cadence** | Weekly repeat-booking cohort analysis |
| **Early warning signal** | Less than 5% of Week 1 merchants have booked a second trip by end of Week 3 |

---

## Risk Heat Map Summary

```
    IMPACT
    5 ─── R-09(post) ─────────────────────── R-05(post)
    4 ─── R-08(post) ─── R-07(post) ──────── R-02 ──── R-04
    3 ─── R-12(post) ─── R-13(post) ─── R-03 R-06 R-11 R-10
    2 ─────────────────────────────────────── R-01(post)
    1 ─────────────────────────────────────────────────
          1         2         3         4         5
                         LIKELIHOOD
    
    PRE-MITIGATION:
    🔴 High (score 13–25):  R-01
    🟡 Medium (score 6–12): R-02, R-03, R-04, R-05, R-06, R-07,
                            R-08, R-09, R-10, R-11, R-12, R-13
    🟢 Low (score 1–5):     None pre-mitigation

    POST-MITIGATION:
    🔴 High: None
    🟡 Medium: R-01, R-02, R-06
    🟢 Low: R-03, R-04, R-05, R-07, R-08, R-09, R-10, R-11, R-12, R-13
```

---

## Top 3 Risks Requiring Weekly Review

| Priority | Risk | Why Top-3 |
|---|---|---|
| 1 | **R-01: STT Noise** | Most likely to hit (noisy shops are the target segment) |
| 2 | **R-02: Credit Attach** | Business model depends on it; hard to fix mid-pilot |
| 3 | **R-06: Inventory Gaps** | Directly visible to merchants; kills trust fast |

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/05-launch-plan*
