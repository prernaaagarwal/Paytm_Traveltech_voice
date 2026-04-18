# Soft-Launch Merchant Cohort
**50 Merchants · Selection Criteria · Onboarding Sequence · Week 1 Playbook**
*Paytm Merchant Travel Desk | 05-launch-plan | PM + Merchant-Ops-Owned*

---

## Purpose

Week 1 soft launch exposes the product to 50 real merchants. The cohort must be representative enough to produce signal on the core hypothesis, small enough to support with high-touch operations, and selected in a way that avoids biasing the pilot toward early-adopter merchants.

This document captures who was selected, why, how they were onboarded, and what the first-week ops playbook looks like.

---

## Cohort Composition

| City | Count | Why This Split |
|---|---|---|
| Kanpur | 20 | Largest pilot city — primary hypothesis test for kirana merchant persona (Ramesh). Tier-2 UP merchants are the core target. |
| Surat | 15 | Textile SME segment (Priya persona). Higher digital literacy; tests credit attach hypothesis more aggressively. |
| Jaipur | 15 | Handicraft/tourism cohort — seasonal travel patterns; validates proactive outbound viability in Week 2. |
| **Total** | **50** | |

---

## Selection Criteria

Filters applied from the broader 15K-merchant universe across the 3 cities.

### Mandatory (all 50 meet these)
1. ≥ 2 Paytm-verified business trips in the previous 12 months (any channel)
2. Paytm Business Credit pre-approved with ≥ ₹20,000 limit
3. Active KYC with GSTIN on file
4. No account suspensions, credit defaults, or fraud flags in last 6 months
5. Active WhatsApp on registered number (verified via last-seen signal ≤ 14 days)
6. Signed digital opt-in consent (PDPA-compliant)

### Preferred (applied where optional)
- Comfortable speaking Hindi OR English (no third-language-only merchants in MVP)
- History of using Paytm Business app at least weekly
- Phone reachability ≥ 80% (based on historical outbound-call data from Merchant Ops)

### Intentional Diversity
- **Credit usage history:** 30 merchants have never used business credit for travel before (new-activation cohort); 20 have used it ≥ 1× (retention-test cohort).
- **Business type:** Not all kirana. Kanpur cohort includes 14 kirana + 4 pharmacy + 2 hardware. Surat is 12 textile + 3 small manufacturing. Jaipur is 8 handicraft + 4 tourism service + 3 F&B.
- **Digital literacy proxy:** Weighted 60% medium-literacy, 40% high-literacy (measured by Paytm Business app session depth). Intentionally no low-literacy merchants in soft launch — they come in Week 2 rollout.

### Exclusions
- No Paytm employees or their family members
- No merchants in ongoing disputes
- No merchants who participated in any prior travel product research (avoids priming bias)

---

## Sample Cohort Table (first 5 of each city — full list in CRM)

| Merchant ID | City | Business | Credit Limit | Last Trip | Notes |
|---|---|---|---|---|---|
| M-K-0001 | Kanpur | Kirana | ₹35K | Delhi, 3mo ago | PM personally spoke Thu |
| M-K-0002 | Kanpur | Pharmacy | ₹45K | Mumbai, 7mo ago | First-time credit use cohort |
| M-K-0003 | Kanpur | Kirana | ₹25K | Delhi, 5mo ago | — |
| M-K-0004 | Kanpur | Kirana | ₹50K | Lucknow (road), 1mo ago | No prior flights — will test for naturalness |
| M-K-0005 | Kanpur | Hardware | ₹40K | Delhi, 2mo ago | — |
| M-S-0001 | Surat | Textile | ₹50K | Mumbai, 1mo ago | High credit user; PM called Fri |
| M-S-0002 | Surat | Textile | ₹30K | Bangalore, 2mo ago | — |
| M-S-0003 | Surat | Small manufacturing | ₹35K | Ahmedabad (road + flight), 4mo ago | — |
| M-S-0004 | Surat | Textile | ₹50K | Mumbai, 6mo ago | — |
| M-S-0005 | Surat | Textile | ₹20K | Mumbai, 3mo ago | Edge-of-eligibility credit cohort |
| M-J-0001 | Jaipur | Handicraft | ₹40K | Delhi, 2mo ago | PM called Fri |
| M-J-0002 | Jaipur | Tourism service | ₹50K | Mumbai + Delhi, 1mo ago | High frequency — Week 2 proactive test |
| M-J-0003 | Jaipur | Handicraft | ₹25K | Delhi, 4mo ago | — |
| M-J-0004 | Jaipur | F&B | ₹30K | Jaipur-Delhi, 3mo ago | — |
| M-J-0005 | Jaipur | Handicraft | ₹45K | Bangalore (handicraft expo), 6mo ago | — |

Full 50-merchant list is in Paytm CRM, segment `travel_voice_pilot_week1`. Access restricted to PM + Merchant Ops + CTO.

---

## Onboarding Sequence

Onboarding ran over 3 days (Wednesday–Friday of Week −1).

### Day 1 (Wednesday) — Opt-In Confirmation

**SMS sent to all 50:**
> *"Namaste [Name], Paytm Business Travel — ek naya service aapke liye. Phone se flight book karein, GST invoice free. Interested hain? Reply YES. Details: [link]"*

**Response tracking:**
- 41 replied YES within 6 hours
- 7 replied after follow-up WA message
- 2 confirmed via Merchant Ops phone call

### Day 2 (Thursday) — Consent + Tutorial

**To the 50 opted-in:**
- Digital consent form (PDPA + recording disclosure) — one-tap acknowledgment
- 45-sec tutorial video on WhatsApp (how to call + what to expect)
- Personalized SMS with helpline number: *"Aapka Paytm Business Travel number: 1800-XXX-0100. 24/7 available. Pehli call par free GST invoice guaranteed."*

**Response tracking:**
- 50 of 50 signed consent
- 32 of 50 opened the tutorial video (64%)

### Day 3 (Friday) — PM High-Touch Calls

PM (Prerna) personally called **7 merchants** (target was 5+) from the Kanpur cohort for a 5-min check-in:
- Purpose: build rapport, surface early hesitations, offer direct escalation channel
- Outcome: 6 of 7 confirmed eager to try in Week 1; 1 expressed concern about credit ("will interest compound forever?") — PM clarified, merchant satisfied

---

## Merchant Reachability & Backup List

To maintain a 50-merchant active cohort, Merchant Ops built a **15-merchant standby list** (same selection criteria). If any of the 50 go unreachable for ≥ 7 days in Week 1, a standby merchant is activated.

Standby activation rule: only activate if the primary cohort drops below 45 active. Standby merchants are notified only upon activation, not before.

---

## Week 1 Operations Playbook

### Daily Rhythm

| Time (IST) | Activity | Owner |
|---|---|---|
| 08:00 | CTO personal smoke test — 3 calls | CTO |
| 09:00 | Daily ops standup (30 min) | Program Manager |
| 09:00–21:00 | Active pilot hours; human agents on duty | Customer Ops |
| 14:00 | Bug triage (15 min) | Engineering |
| 17:00 | End-of-day metrics review | Data + PM |
| 18:00 | PM calls 2–3 merchants for qualitative feedback | PM |
| 21:00–09:00 | On-call only; pilot quiet hours | Platform Eng |

### Escalation Ladder

| Issue | Tier 1 | Tier 2 | Tier 3 |
|---|---|---|---|
| Merchant complaint | Human agent | Customer Ops Lead | PM |
| Booking failure | On-call engineer | Engineering Lead | CTO |
| Data/privacy incident | On-call engineer + Security | CISO | CTO + Legal |
| Revenue/fraud signal | Finance on-call | Finance Lead + Risk | CTO + GM |

### Merchant Support Channels

1. **Human agent via call** — transfer from AI during booking (primary for in-call issues)
2. **WA reply to booking thread** — for post-booking questions (handled by Customer Ops Tier 1)
3. **PM direct line** — given verbally to the 7 merchants PM called Friday (bypass path for high-signal feedback)
4. **No web form, no app** — keeps support path simple and voice-first

---

## Merchant Communications Plan for Week 1

| Day | Communication | Medium |
|---|---|---|
| Monday 08:30 | Launch reminder SMS: "Aaj se service live hai — 1800-XXX-0100" | SMS to all 50 |
| Wednesday | Mid-week check-in WA (only to merchants who haven't called yet): "Tried the service yet? Any questions?" | WA |
| Friday | Week 1 close WA: "Thank you, here's your discount code for Week 2 booking" (if applicable) | WA |

No promotional pressure. The goal of Week 1 is to observe natural behavior, not maximize bookings.

---

## Week 1 Target Metrics (Internal — Not Shared with Merchants)

| Metric | Target | Rationale |
|---|---|---|
| % of 50 who call at least once | ≥ 50% (25 merchants) | Validates opt-in intent quality |
| Booking completion rate | ≥ 60% of those who call | Validates the flow works in the wild |
| Conversion rate (calls → bookings) | ≥ 15% | Exit gate for Week 2 scale-up (lower bar than final 25%) |
| Avg call duration | ≤ 4 min | Exit gate |
| Credit attach rate | No hard target | Informational only; target applies at full-pilot end |
| NPS | ≥ 40 | Exit gate for scale-up |
| Sev-1 incidents | 0 | Exit gate for scale-up |

**If any of "exit gate" metrics missed: pause Week 2 scale-up; PM + CTO + GM decision required.**

---

## Week 1 Failure Modes to Expect

Based on dogfood findings, plan for these in the first-week data:

| Likely Issue | How to Interpret | Action |
|---|---|---|
| Some merchants call, hang up on IVR welcome (confused) | Expected ~10–15% drop on first-time callers | Monitor; if > 25%, IVR welcome too long |
| Merchants prefer wallet over credit (habit) | Credit attach could be lower than dogfood | Not a failure — habits take time; PM interviews will clarify |
| Accent patterns not well-covered by NLU | Some regional accents in UP/Gujarat/Rajasthan weren't in training data | NLU retrain v1.1 gate triggered if accuracy drops > 3 pp |
| Merchants ask for things we don't support (hotel, train) | Common — tests our scope-boundary messaging | Agent should gracefully decline + not over-promise |
| Some calls place at odd hours (2 AM) | Validates 24/7 positioning | System should work the same; human agents unavailable 21:00–09:00 → AI-only |

---

## Data Collection Beyond Standard Analytics

PM is running a parallel qualitative research stream during Week 1:

1. **Post-call NPS survey** (SMS, auto-sent 5 min after call end)
2. **Scheduled 15-min calls** with 10 merchants in Week 1 (5 who booked + 5 who didn't)
3. **Merchant journal** (shared Notion doc) — PM's real-time observations
4. **Weekly qualitative synthesis** report delivered to GM every Friday

---

## Readiness Statement

50 merchants selected. 50 opted in. 48 acknowledged welcome; 2 confirmed by phone. Support escalation tested. Dashboards live. Week 1 playbook ops-tested.

**Cohort ready. Launch Monday 09:00.**

---

*Owner: PM · Co-owned: Merchant Ops, Customer Ops, CTO · Last updated: Week −1 Friday*
