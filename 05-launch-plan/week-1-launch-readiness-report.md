# Week 1 Launch Readiness Report
**CTO Status to Product GM — End of Dogfood**
*Paytm Merchant Travel Desk | 05-launch-plan*

---

## TL;DR

All 10 Week −1 dogfood milestones (D01–D10) are closed. 47 real bookings completed by 20 internal staff. 18 issues surfaced; all Sev-1/Sev-2 either fixed or mitigated. CA panel unanimously approved invoice format. NPS from dogfooders: **58**. **Recommendation: proceed to Week 1 soft launch with 50 merchants on Monday at 09:00 IST.**

---

## Dogfood Milestone Status

| # | Milestone | DRI | Status | Notes |
|---|---|---|---|---|
| D01 | 20 internal staff onboarded | Program Manager | ✅ GREEN | All 20 have helpline number + scenario briefs |
| D02 | 40+ bookings attempted | All dogfooders | ✅ GREEN | 47 attempts (118% of target) |
| D03 | All 5 stages exercised ≥10× each | QA Lead | ✅ GREEN | Stage 1: 47, Stage 2: 44, Stage 3: 42, Stage 4: 42, Stage 5: 42 |
| D04 | 5 major failure modes triggered | QA Lead | ✅ GREEN | FM-02, FM-09, FM-10, FM-14, FM-17 all tested |
| D05 | Handoff tested from 5 trigger types | QA Lead | ✅ GREEN | Explicit, AI-3-fail, emotion, complexity, refund all triggered |
| D06 | WA companion verified Android + iOS | Mobile QA | ✅ GREEN | Both platforms confirmed; iOS cosmetic bug BUG-D04 fixed |
| D07 | GST invoice approved by 3 CAs | Finance | ✅ GREEN | See `ca-invoice-approval-packet.md`. All 3 signed. |
| D08 | Bugs triaged + Sev-1 fixed | Product | ✅ GREEN | 18 bugs; Sev-1 = 0 open, Sev-2 = 0 open (1 mitigated) |
| D09 | Avg call duration baseline | Data | ✅ GREEN | 3 min 22 sec (target ≤ 4 min for dogfood, ≤ 3 min for pilot) |
| D10 | NPS from dogfooders | Product | ✅ GREEN | NPS = 58 (target > 40 internal) |

---

## Carried-Over Findings From Week −2 (F1–F4)

| # | Finding | Status | Notes |
|---|---|---|---|
| F1 | Hinglish NLU mis at 7.2% | ✅ RESOLVED | Retrained (BUG-D03); now 94.6% |
| F2 | Cloud Run NLU cold-start on spike | ✅ RESOLVED | minInstances=3; no cold-start observed in dogfood |
| F3 | p95 latency near ceiling when Credit API slow | ✅ RESOLVED | Parallel wallet-prefetch deployed |
| F4 | STT WER <85% in noise | ✅ RESOLVED | Noise gate tightened + adaptive bandpass. Verified in S8 dogfood (CTO personally) |

All four P12 findings closed inside Week −1 as planned.

---

## Exit Gate Check (from 4-week-timeline.md Week −1 gates)

| Gate | Required | Actual | Status |
|---|---|---|---|
| D01–D10 complete | All | All ✅ | ✅ |
| Zero Sev-1 bugs open | 0 | 0 | ✅ |
| Avg call duration | ≤ 4 min | 3m 22s | ✅ |
| CA invoice approval | Yes | 3/3 CAs | ✅ |

**All Week −1 exit gates passed.**

---

## Week 1 Soft Launch Preparation Status

| Item | DRI | Status |
|---|---|---|
| 50 soft-launch merchants confirmed | Merchant Ops | ✅ Kanpur 20, Surat 15, Jaipur 15 — all opted in |
| Welcome SMS + WA sent to all 50 | Merchant Ops | ✅ Sent Friday; 48 acknowledged |
| 8 human agents trained on 5 handoff types | Customer Ops | ✅ All 8 certified; warm-transfer dry-runs completed |
| Looker ops dashboard live | Data Engineering | ✅ Live; PM + CTO confirmed visibility on all 20 events |
| Daily ops standup calendar invite | Program Manager | ✅ 09:00 IST Mon–Fri for 5 weeks |
| Sev-1 incident playbook tested | Engineering | ✅ Tabletop passed Thursday; real incident simulated in dogfood |
| Regression suite green | QA | ✅ All 18 failure modes automated; 16/18 E2E verified |
| Code freeze | Engineering | ✅ Tag `v1.0.0` cut Friday 17:00 IST |

---

## Risks Entering Week 1

| Risk | Severity | Mitigation |
|---|---|---|
| First real merchant calls reveal new voice/accent patterns | Medium | Daily NLU performance review; retrain threshold set at >3 pp accuracy drop |
| WA delivery lag on 2G in Tier-2 merchant locations | Medium | SMS fallback tested in S6 and BUG-D15; monitoring dashboard tracks delivery % per network type |
| Merchants confused by "credit first" prompt (unused to business credit) | Medium | PM personally calling 10 merchants on Monday to capture first-reaction feedback |
| Human agent queue spike if AI failure rate higher than dogfood | Medium | Queue has capacity for 20 concurrent handoffs; 8 agents staffed 09:00–21:00 |
| Telecom quality issues on specific circles (BSNL peering in UP) | Low-Medium | Telecom vendor on standby; fallback trunk pre-configured |
| BUG-D08 outbound mitigation (freshKYC fetch) adds 250ms to outbound call | Low | Only affects proactive calls; inbound (primary channel Week 1) unaffected |

---

## Week 1 Observability Focus

Extra monitoring on these signals for the first 72 hours:

| Signal | Alert Threshold | On-Call Response |
|---|---|---|
| Conversion rate | < 10% on any 2-hour window | Review transcripts, identify friction |
| Credit attach rate | < 20% on any 24-hour window | Audit if nudge wording is being delivered |
| NLU confidence | p50 < 0.85 on any 100-call window | Check for unseen utterance patterns |
| Invoice delivery failures | > 2% | Escalate to GST service on-call |
| Human handoff rate | > 25% | Audit AI failure categories |
| WA delivery rate | < 96% | Escalate to WA + SMS fallback verify |

---

## CTO Posture for Week 1

- **On-call 24/7 for first 72 hours** (already committed in runbook)
- **Personally placing 3 test calls Monday 08:00** before soft launch opens at 09:00
- **Attending all 5 daily 09:00 standups** Week 1
- **5 PM Sev-1 incident review** Monday–Wednesday if any P0/P1 fires
- **Will make Week 2 scale-up decision jointly with PM** based on Week 1 exit gate criteria

---

## Asks of Product GM

1. **Approval to open 50-merchant soft launch Monday 09:00 IST.** No new open items.
2. **Joint Week 1 Friday review** to decide on Week 2 scale-up to 200 merchants (per timeline).
3. **Communications posture:** confirm continued no-PR stance during pilot. Only internal Paytm all-hands mention scheduled.

---

## Recommendation

**✅ PROCEED TO WEEK 1 SOFT LAUNCH ON SCHEDULE.**

Signed:

- **CTO** — Engineering and infrastructure ready. All findings closed. Incident response proven.
- **Engineering Lead** — Code frozen at `v1.0.0`. Zero Sev-1/Sev-2 open.
- **QA Lead** — Dogfood complete. Regression suite green.
- **Customer Ops Lead** — 8 agents trained and ready.
- **Finance Lead** — CA approval obtained. Unit economics tracking live.
- **Program Manager** — Operations standup cadence + escalation paths in place.
- **Product Manager** — Merchant onboarding complete. Dashboards live.

Distribution: Product GM, Engineering Head, Finance, Risk, Legal, Customer Ops Head, CEO Office.

---

*Author: CTO · Co-signers: Eng Lead, QA Lead, CX Lead, Finance Lead, PM · Last updated: April 2026 (EOW −1)*
