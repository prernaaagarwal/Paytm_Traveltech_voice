# 4-Week Launch Timeline
**Week-by-Week Milestones · Owners · Exit Gates**
*Paytm Merchant Travel Desk | 05-launch-plan*

---

## Timeline Overview

```
PRE-LAUNCH                          PILOT                           DECISION
─────────────────────────────────────────────────────────────────────────────
  Week -2        Week -1        Week 1        Week 2-5       Week 6    Week 7
  ─────────      ─────────      ─────────     ──────────     ───────   ───────
  Infra &        Dogfood        Soft          Full 500-      Analysis  Go /
  Integration    (20 internal   launch        merchant       Sprint    No-Go
  complete       staff)         (50           pilot                    Decision
                                merchants)
```

**Pilot clock starts at Week 1 (soft launch). Total tracked pilot window: 4 weeks.**

---

## Week −2: Infrastructure & Integration Complete
*2 weeks before merchant-facing launch*

### Goals
All systems integrated, tested, and holding load. No merchant exposure yet.

### Milestones

| # | Milestone | Owner | Done When |
|---|---|---|---|
| P01 | STT/TTS pipeline integrated end-to-end | AI Engineering | Round-trip voice call completes in < 3 sec |
| P02 | NLU model v1 deployed on Cloud Run | ML Engineering | Intent accuracy > 90% on 200-sample test set |
| P03 | Paytm Travel API integration live | Platform Engineering | Flight search returns results in < 3 sec for top 20 routes |
| P04 | Paytm Credit API integration live | Platform Engineering | Credit charge + decline handling tested with synthetic accounts |
| P05 | GST validation + invoice PDF pipeline live | Finance Engineering | GSTIN validation < 1 sec; PDF generation < 5 sec |
| P06 | WhatsApp Business API templates approved | Product + WA | All 8 message templates approved by Meta (7-day SLA) |
| P07 | Helpline number 1800-XXX-XXXX provisioned | Telecom Ops | IVR live, calls route to AI agent |
| P08 | Redis session state + call-drop recovery tested | Engineering | Session survives simulated drop; WA resume link delivered < 10 sec |
| P09 | Analytics pipeline live | Data Engineering | All 20 events logging to Kafka → BigQuery in < 5 min |
| P10 | Security review complete | Security | PII handling, mTLS, data retention policy signed off |
| P11 | Human agent queue integrated | Customer Operations | Context packet renders on agent screen; warm transfer tested |
| P12 | Load test: 50 concurrent calls | QA | < 3% error rate at 50 concurrent sessions |

### Exit Gate for Week −2
- ✅ All P01–P12 milestones complete
- ✅ Zero Sev-1 open issues
- ✅ Security sign-off received
- 🚦 If any P-critical blocker open → delay dogfood by 1 week (do not skip)

---

## Week −1: Dogfooding
*Internal staff only — 20 Paytm employees booking real trips*

### Goals
Find what automated testing missed. Real money, real bookings, real friction points.

### Who Dogfoods
- 5 from Product team (own the scenarios they designed)
- 5 from Engineering (technical edge cases)
- 5 from Business Operations (realistic merchant behavior)
- 5 from Customer Support (understand what breaks and why)

**Rules:** Use real Paytm Business Credit or wallet. Book actual trips (or cancel within free window). File actual GST invoices. The dogfood only counts if it's real.

### Milestones

| # | Milestone | Owner | Done When |
|---|---|---|---|
| D01 | 20 internal staff onboarded to dogfood | Program Manager | All 20 have helpline number + instructions |
| D02 | 40+ bookings attempted (2 per person minimum) | All dogfooders | Booking logs show ≥ 40 attempts |
| D03 | All 5 stages exercised at least 10× each | QA Lead | Stage completion logs verified |
| D04 | All major failure modes triggered intentionally | QA Lead | FM-01, FM-08, FM-10, FM-14, FM-17 all tested |
| D05 | Human handoff tested from all 5 trigger types | QA Lead | Handoff context packet verified for each trigger |
| D06 | WA companion sync verified on Android + iOS | Mobile QA | Cards render correctly on both platforms |
| D07 | GST invoices validated by CA | Finance | 3 CAs confirm invoice format is acceptable |
| D08 | Dogfood debrief: bugs catalogued + prioritized | Product | Sev-1/2/3 list created, Sev-1 fixed before Week 1 |
| D09 | Avg call duration measured | Data | Baseline avg call duration from real sessions |
| D10 | NPS collected from dogfooders | Product | 20 responses, qualitative feedback documented |

### Dogfood Scenarios (Scripted)

| Scenario | Who Tests | Why |
|---|---|---|
| Happy path Hindi — wallet payment | 5 testers | Core flow validation |
| Happy path English — credit payment | 5 testers | Credit nudge validation |
| No flights available → alternate date | 2 testers | FM-09 |
| Call drop at Stage 3 (payment initiated) | 2 testers | FM-17 most critical |
| GSTIN manually suspended before call | 1 tester | FM-14 |
| Credit declined → wallet fallback | 2 testers | FM-10 |
| Explicit "insaan se baat karni hai" | 2 testers | Human handoff |
| Background noise simulation (loud music) | 1 tester | FM-02 |
| Mid-booking price change (mock) | 1 tester | FM-13 |
| Proactive outbound call (PM places call to tester) | 2 testers | Outbound flow |

### Exit Gate for Week −1
- ✅ D01–D10 complete
- ✅ Zero Sev-1 bugs open (Sev-2 may proceed with mitigation plan)
- ✅ Avg call duration ≤ 4 min (dogfood usually slower than real merchants)
- ✅ CA invoice approval received (D07)
- 🚦 Any Sev-1 open → delay Week 1 launch until resolved

---

## Week 1: Soft Launch
*50 merchants across 3 cities · Inbound calls only · High-touch monitoring*

### Goals
First real merchant experience. Ops team monitoring every call. Fast feedback loop. Prove the hypothesis works in the wild before scaling to 500.

### Merchant Selection (50 for Soft Launch)
- Kanpur: 20 merchants
- Surat: 15 merchants
- Jaipur: 15 merchants
- Criteria: Same as full pilot (2+ trips/year, credit pre-approved, opted-in)
- Extra criterion for soft launch: **PM personally knows or has spoken to at least 5** (warmer feedback)

### Milestones

| # | Milestone | Owner | Done When |
|---|---|---|---|
| S01 | 50 merchants notified + onboarded | CRM / Merchant Ops | Welcome SMS + WA sent to all 50; helpline number shared |
| S02 | Daily ops call established (30 min, 9 AM) | Program Manager | First call held; attendance: PM, Eng lead, Ops lead |
| S03 | Real-time monitoring dashboard live | Data Engineering | Looker dashboard showing live: calls, conversions, errors |
| S04 | First 10 real bookings completed | Product | 10 confirmed bookings with PNR in system |
| S05 | First credit booking completed | Product | ≥ 1 booking paid via Paytm Business Credit |
| S06 | First GST invoice delivered | Finance | ≥ 1 PDF delivered to merchant WhatsApp, merchant confirms received |
| S07 | First human handoff in production | Customer Ops | ≥ 1 real handoff; context packet quality rated by agent |
| S08 | Week 1 conversion rate measured | Data | call→booking conversion calculated from logs |
| S09 | Week 1 NPS collected | Product | ≥ 20 survey responses from soft-launch merchants |
| S10 | Top 5 pain points from Week 1 documented | Product | Written list with frequency counts, prioritized |
| S11 | Sev-1 incident response tested | Engineering | At least 1 real or simulated incident → response < 30 min |

### Week 1 Daily Rhythm

```
Every day (Mon–Fri):
  9:00 AM   — Daily ops standup (PM + Eng + Ops, 30 min)
              Agenda: Yesterday's conversion rate, errors, qualitative feedback
  2:00 PM   — Bug triage (Engineering, 15 min)
              Sev-1: Fix same day. Sev-2: Fix within 48hr. Sev-3: Backlog.
  6:00 PM   — Merchant callback (2-3 merchants called by PM for live feedback)

Every day (automated):
  Analytics email at 8 AM: calls / bookings / conversion / credit attach / errors
  Slack alert: any Sev-1 error (immediate)
  Slack digest: hourly call volume + error rate
```

### Exit Gate for Week 1 (to proceed to full 500-merchant pilot)
- ✅ ≥ 25 bookings completed (50% of 50 merchants attempting at least once)
- ✅ Conversion rate ≥ 15% (lower bar than final 25% target — week 1 is learning)
- ✅ Zero Sev-1 open at end of week
- ✅ Avg call duration ≤ 4 min
- ✅ GST invoice delivery working (< 1% failure rate)
- ✅ No data privacy incidents
- 🚦 Miss ≥ 2 criteria → pause and investigate before scaling to 500

---

## Weeks 2–5: Full 500-Merchant Pilot
*All 500 merchants active · Inbound + proactive outbound · Normal operations*

### Goals
Validate all core metrics against Go/No-Go thresholds. Generate enough data to make the scale decision.

### Scale-Up Schedule

| Week | Active Merchants | New Additions | City Focus |
|---|---|---|---|
| Week 2 | 50 → 200 | +150 Kanpur merchants | Tier-2 kirana belt |
| Week 3 | 200 → 350 | +150 Surat merchants | Textile sourcing belt |
| Week 4 | 350 → 500 | +150 Jaipur merchants | Handicraft/tourism |
| Week 5 | 500 | No additions | Stabilize, collect data |

**Why staged scale-up?** If Kanpur shows a specific failure pattern, fix before rolling to Surat. Each city cohort is a validation, not just a scale operation.

### Weekly Milestones (Weeks 2–5)

| Week | Milestone | Target |
|---|---|---|
| 2 | 200 merchants active; Kanpur cohort analysis | ≥ 20% conversion in Kanpur |
| 2 | Proactive outbound calls initiated (Scenario 1 only) | 30 proactive calls placed |
| 3 | 350 merchants active; Surat cohort added | ≥ 22% conversion (building) |
| 3 | Credit attach rate stabilizing | ≥ 35% (approaching 40% target) |
| 4 | All 500 merchants active | All cities represented |
| 4 | NLU retrain v1.1 if nlu_failure_rate > 8% | Improved model deployed |
| 5 | Full 4-week data set complete | All metrics measurable |
| 5 | 30 merchant interviews conducted | Qualitative depth on top issues |
| 5 | Merchant retention signal: repeat bookings | ≥ 10% booked twice |

### Ongoing Monitoring (Weeks 2–5)

```
Daily automated:
  - Conversion funnel (calls → intents → options → confirms → bookings)
  - Credit attach rate
  - Avg call duration
  - Error rate by failure mode category
  - NPS rolling 7-day average

Weekly review (every Friday):
  - City-level cohort comparison (Kanpur vs. Surat vs. Jaipur)
  - Top 3 NLU failure utterances → candidate training examples
  - Human handoff reasons analysis
  - Proactive call connect + conversion rates
  - Financial metrics: GMV, credit disbursed, commission accrued
```

### Incident Protocol (Ongoing)
```
Sev-1 (P0): System down / data breach / wrong charges
  → PagerDuty alert → On-call engineer + PM notified in 5 min
  → Resolution SLA: 2 hours
  → Post-mortem: 24 hours

Sev-2 (P1): > 10% booking failure rate / WA delivery broken
  → Slack alert → Fix within 24 hours
  → Root cause documented

Sev-3 (P2): NLU accuracy drop / minor UX issues
  → Backlog → Fix in next weekly deploy
```

---

## Week 6: Analysis Sprint
*No new merchants. Focus: interpret data, prepare Go/No-Go recommendation.*

### Milestones

| # | Milestone | Owner | Done When |
|---|---|---|---|
| A01 | Final 4-week conversion funnel analysis | Data | Slide with full funnel: 100 calls → bookings |
| A02 | Credit attach rate final calculation | Finance | Confirmed % of bookings via credit |
| A03 | NPS final score (4-week rolling) | Product | Weighted NPS from all survey responses |
| A04 | Booking failure rate audit | Engineering | All failed bookings categorised by root cause |
| A05 | Unit economics validation | Finance | Revenue per booking, credit interest accrued, CAC confirmed = 0 |
| A06 | 30 merchant interview synthesis | Product | Key themes: what worked, what didn't, would they recommend |
| A07 | NLU performance report | ML | Accuracy, top failure utterances, retrain plan |
| A08 | Competitive signal check | Product | Any competitor movement during pilot? |
| A09 | Go/No-Go recommendation prepared | PM + GM | 1-page memo with recommendation + supporting data |
| A10 | Stakeholder briefing | PM | Leadership briefed on results before Week 7 decision meeting |

---

## Week 7: Go/No-Go Decision

### The Meeting
- Attendees: Product GM, Engineering Head, Finance, Risk, Legal
- Input: A09 recommendation memo + A10 briefing
- Output: Go / No-Go / Go with conditions decision
- Duration: 90 minutes

### Decision Paths

| Decision | Condition | Next Step |
|---|---|---|
| **GO** | All primary metrics at or above threshold | Scale to 5,000 merchants in 3 cities + expand to 10 new cities |
| **GO WITH CONDITIONS** | Primary metrics met but secondary gaps | Fix specific gaps + re-pilot with 200 additional merchants before full scale |
| **PAUSE** | 1–2 metrics below threshold with clear root cause | Address root cause (6–8 weeks) → re-run pilot subset |
| **NO-GO** | Kill criteria triggered (see go-no-go-criteria.md) | Document learnings → archive → reassess in 6 months |

See `go-no-go-criteria.md` for full threshold definitions.

---

## RACI Summary

| Role | Responsible For |
|---|---|
| **Product Manager** | Timeline, milestones, daily ops call, merchant interviews, Go/No-Go memo |
| **AI/ML Engineering** | STT/NLU/TTS pipeline, NLU retraining, performance monitoring |
| **Platform Engineering** | API integrations, Redis, Kafka, load testing, incident response |
| **Customer Operations** | Human agent training, handoff context quality, queue management |
| **Finance** | GST invoice CA approval, unit economics tracking, credit metrics |
| **Data Engineering** | Analytics pipeline, dashboards, conversion funnel reports |
| **Program Manager** | Coordination, JIRA, weekly reports, stakeholder comms |
| **QA** | Dogfood scenario coverage, load testing, regression suite |
| **Legal / Security** | PII compliance, PDPA, security review sign-off |
| **Merchant Ops** | CRM onboarding, opt-in management, SMS/WA merchant communications |

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/05-launch-plan*
