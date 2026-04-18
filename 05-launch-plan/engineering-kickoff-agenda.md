# Engineering Kickoff — Week −2 Execution
**Agenda, Owner Assignments for P01–P12, and Exit Criteria**
*Paytm Merchant Travel Desk | 05-launch-plan*

---

## Meeting Block

| Field | Value |
|---|---|
| **When** | This Thursday, 10:00–12:00 IST |
| **Where** | Paytm HQ — Meeting Room 4A + Google Meet |
| **Chair** | Product Manager |
| **Duration** | 2 hours (90 min walkthrough + 30 min Q&A) |
| **Required Attendees** | Engineering Lead, AI/ML Lead, Platform Lead, Finance Eng Lead, Data Eng Lead, QA Lead, Security, Customer Ops Lead, Program Manager |
| **Optional** | Travel Partnerships, Credit Product, Legal |

---

## Pre-Read (Send 24 hours before)

1. `04-mvp-specification/prd.md` — full PRD
2. `05-launch-plan/4-week-timeline.md` — milestones and exit gates
3. `05-launch-plan/open-questions-tracker.md` — working assumptions to align on
4. `03-ai-architecture/conversational-ai-stack.md` — technical stack
5. `04-mvp-specification/edge-cases-register.md` — 18 failure modes

Attendees who have not read the pre-read will be asked to reschedule their participation to the following day.

---

## Agenda

### Block 1: Context (15 min)
- Strategic framing: **credit activation product, not travel product** — North star = 40% credit attach
- 3-minute constraint is non-negotiable
- Pilot scope: 500 merchants × 3 cities × 4 weeks
- Critical path items (1800 number, Meta templates) already in flight

### Block 2: P01–P12 Milestone Walkthrough (60 min)
Each milestone: scope, owner, exit criteria, dependencies, risks. Owners confirm acceptance or raise blockers.

### Block 3: Open Questions Alignment (15 min)
Walk through `open-questions-tracker.md`. Engineering signs off on working assumptions or escalates.

### Block 4: Commitments + Risks (30 min)
- Each lead commits to their P-milestones or raises a timeline concern
- Top 3 risks per workstream surfaced
- Dependencies between workstreams made explicit (e.g., P08 Redis blocks P02 NLU session state)
- PM captures action items with owners + due dates

---

## P01–P12 Milestone Owner Assignments

| # | Milestone | DRI (Directly Responsible) | Reviewer | Exit Criterion | Dependencies |
|---|---|---|---|---|---|
| P01 | STT/TTS pipeline integrated end-to-end | AI/ML Lead | Engineering Lead | Round-trip voice call < 3 sec; cached phrases verified | Google Cloud quota (already ready) |
| P02 | NLU model v1 deployed on Cloud Run | ML Engineer | AI/ML Lead | >90% intent accuracy on 200-sample test set | P08 Redis (session store) |
| P03 | Paytm Travel API integration live | Platform Eng | Engineering Lead | Flight search <3 sec for top 20 routes | OQ-1 resolution |
| P04 | Paytm Credit API integration live | Platform Eng | Credit Eng | Credit charge + decline handling tested with synthetic accounts | OQ-2, OQ-5 resolutions |
| P05 | GST validation + invoice PDF pipeline live | Finance Eng Lead | Legal (invoice format) | GSTIN validation <1 sec; PDF <5 sec; CA review scheduled | OQ-6 resolution |
| P06 | WhatsApp Business API templates approved | PM + Platform Eng | WA liaison | All 8 templates submitted to Meta; approval log captured | See `whatsapp-templates.md` |
| P07 | Helpline number 1800-XXX-XXXX provisioned | Telecom Ops | PM | IVR live; calls route to AI agent dev environment | OQ-3 — **critical path** |
| P08 | Redis session state + call-drop recovery tested | Platform Eng | QA Lead | Session survives simulated drop; WA resume link <10 sec | — |
| P09 | Analytics pipeline live | Data Eng Lead | PM | All 20 events → Kafka → BigQuery in <5 min | — |
| P10 | Security review complete | Security | CISO | PII handling, mTLS, data retention policy signed off | P01–P09 implementations |
| P11 | Human agent queue integrated | Customer Ops Lead | PM | Context packet renders; warm transfer tested | P01, P08 |
| P12 | Load test: 50 concurrent calls | QA Lead | Engineering Lead | <3% error rate at 50 concurrent sessions | P01–P09 complete |

---

## Decisions Required at Kickoff

| # | Decision | Options | PM Recommendation |
|---|---|---|---|
| D1 | NLU fallback if BERT training data insufficient by Week −2 | (a) Rule-based regex fallback (b) Push to GUI form earlier | (a) — keeps voice-first promise; GUI is post-2-fail fallback only |
| D2 | Handling of dynamic pricing between search and confirm | (a) Re-quote on confirm if delta >5% (b) Absorb up to ₹200 gap | (a) — merchant trust > ₹200 margin |
| D3 | Redis cluster size for pilot | (a) 3-replica (spec) (b) 1 primary + 1 replica (cheaper) | (a) — spec says 3 minimum for session reliability |
| D4 | Analytics PII masking level | (a) Last-4 of GSTIN only (b) Full hash | (a) — matches FR-07 spec |

---

## Cross-Workstream Dependencies (Dependency Map)

```
P07 (1800 number) ──┐
                    ├─► Dogfood Week −1 (D01–D10)
P06 (WA templates) ─┤
                    │
P01 (STT/TTS) ──────┼─► P12 (Load test)
P02 (NLU) ──────────┤
P08 (Redis) ────────┘
                    
P03 (Travel API) ───┐
P04 (Credit API) ───┼─► E2E booking flow
P05 (GST API) ──────┘

P09 (Analytics) ────► Real-time dashboard (Week 1 S03)
P10 (Security) ─────► GO/NO-GO gate (cannot launch without)
P11 (Handoff) ──────► Soft launch (Week 1 S07 — first handoff)
```

**Critical chains:**
- OQ-3 → P07 → Dogfood (any slip in 1800 provisioning slips the entire pilot)
- P01 + P02 + P08 → P12 (load test is an integrated test of the voice stack)
- P03 + P04 + P05 → end-to-end booking flow (cannot dogfood without all three)

---

## Action Items Template (filled at kickoff)

| # | Action | Owner | Due | Status |
|---|---|---|---|---|
| A1 | Confirm OQ-1 (KNU airline partners) | Travel Partnerships | Week −3 Thu | Open |
| A2 | Confirm OQ-2 (Credit idempotency window) | Credit Engineering | Week −3 Wed | Open |
| A3 | Submit 8 WA templates to Meta | Platform Eng | Week −3 Mon | Open |
| A4 | Schedule CA panel review of invoice format | Finance | Week −2 Fri | Open |
| A5 | Daily ops standup calendar invite sent | Program Manager | Week −1 Mon | Open |
| A6 | NLU training data: 1,000 synthetic + 200 real queries finalized | ML Eng | Week −2 Wed | Open |
| A7 | Redis cluster provisioned in staging | Platform Eng | Week −2 Tue | Open |
| A8 | Human agent training materials drafted | Customer Ops | Week −1 start | Open |

---

## Kickoff Exit Criteria

The kickoff is complete only when:
- ✅ Every P-milestone has a named DRI who verbally committed
- ✅ Every D1–D4 decision has a resolution
- ✅ Every action item has an owner and due date
- ✅ Any raised blocker has a triage plan (not just surfaced — assigned)
- ✅ Program Manager posts meeting summary in #mvp-launch Slack within 2 hours

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/05-launch-plan*
