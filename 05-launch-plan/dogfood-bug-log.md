# Dogfood Bug Log — Week −1
**Issues Found · Triage · Resolution Status**
*Paytm Merchant Travel Desk | 05-launch-plan | QA + Eng-Owned*

---

## Summary

Over 5 days of dogfood, 20 Paytm employees placed **47 real booking calls** (target: 40+). 18 issues were surfaced. As of Friday EOD:

| Severity | Found | Fixed | Open | Mitigated |
|---|---|---|---|---|
| **Sev-1** | 2 | 2 | 0 | — |
| **Sev-2** | 6 | 5 | 0 | 1 |
| **Sev-3** | 10 | 3 | 7 | — |
| **Total** | **18** | **10** | **7** | **1** |

All Sev-1 and Sev-2 either closed or mitigated with a documented plan. Seven Sev-3 items queued for Week 2 deploy.

---

## Severity Definitions

| Severity | Criteria | Fix SLA |
|---|---|---|
| **Sev-1 (P0)** | Blocks booking completion; data loss; financial error; security exposure | Same day |
| **Sev-2 (P1)** | Degrades UX or reliability but booking still completes; workaround exists | Within 48 hours |
| **Sev-3 (P2)** | Minor UX, wording, polish | Next sprint |

---

## Sev-1 Issues (2 — both fixed)

### BUG-D01: Double-charge on rapid verbal-then-tap confirmation
- **Finder:** Priya (Eng), Tuesday call
- **Trigger:** Said "haan" verbally at same time as tapping "Confirm" on WA card
- **Impact:** Credit line debited ₹3,800 twice; PNR generated once
- **Root cause:** Idempotency key used timestamp rounded to 60 sec; two events fired within same bucket but the booking API's dedupe window was 30 sec, not 60 sec
- **Fix:** Aligned rounding to 30 sec + added application-layer mutex on `session.confirming` flag
- **Owner:** Platform Engineer (Arun)
- **Resolution:** Committed Tuesday 18:40; verified with regression replay Wednesday AM
- **Status:** ✅ FIXED. Duplicate charge reversed for affected tester.

### BUG-D02: STT transcript retained past 7-day CISO limit
- **Finder:** Security audit (Wednesday)
- **Trigger:** GCS retention policy was set at 14 days (dev default), not 7 days per CISO residual risk F5
- **Impact:** Potential compliance breach; low actual exposure since all dogfood data was internal staff
- **Fix:** GCS lifecycle updated to 7 days; retroactive purge of objects 8–14 days old
- **Owner:** Platform Engineer (Meera)
- **Resolution:** Fixed Wednesday; CISO re-audit confirmed Thursday
- **Status:** ✅ FIXED. No external data exposed.

---

## Sev-2 Issues (6 — 5 fixed, 1 mitigated)

### BUG-D03: Hinglish code-switch in the middle of a time phrase misfires NLU
- **Example:** *"Delhi kal subah 9 baje ki flight"* — NLU extracted time=morning but missed "9 baje" specificity
- **Impact:** Not blocking, but ranking picks cheapest morning flight instead of closest to 9 AM
- **Fix:** Added 32 training examples with embedded exact times; retrained NLU (v1.0-b)
- **Owner:** ML Engineer (Rohan)
- **Status:** ✅ FIXED Wednesday. Intent accuracy on Hinglish holdout rose from 92.8% → 94.6%.

### BUG-D04: WA payment card on iOS renders credit balance with extra decimal (₹47,000.00)
- **Impact:** Cosmetic but inconsistent with voice agent ("saintaalees hazaar rupaye")
- **Fix:** Template variable formatter updated to strip trailing `.00`
- **Owner:** Platform Engineer (Arun)
- **Status:** ✅ FIXED Wednesday. Resubmitted template #3 to Meta; approved within 6 hours.

### BUG-D05: Agent pronounces "6E" in PNR as "six-e" instead of spelling "six E"
- **Example:** PNR 6E2F4G pronounced as "sixy-too-ef-forg"
- **Impact:** Merchant confusion; had to request repeat
- **Fix:** SSML `<say-as interpret-as="characters">` applied to PNR rendering (was already in spec; regression)
- **Owner:** AI/ML Engineer (Karan)
- **Status:** ✅ FIXED Tuesday.

### BUG-D06: Warm transfer hold music played only on caller side, not agent side
- **Impact:** Human agent heard silence for 40 sec; thought call dropped
- **Fix:** SIP bridge config corrected to play music-on-hold to both legs
- **Owner:** Platform Engineer + Telecom Ops
- **Status:** ✅ FIXED Thursday.

### BUG-D07: Invoice PDF file size 620 KB (> 500 KB limit for 2G delivery)
- **Cause:** Embedded logo in invoice not optimized; PNG instead of SVG
- **Fix:** Replaced with SVG; re-ran PDF generation. New size: 180 KB.
- **Owner:** Finance Engineer (Nikhil)
- **Status:** ✅ FIXED Thursday.

### BUG-D08: Proactive outbound call plays merchant's old name (changed 2 weeks ago)
- **Impact:** Feels impersonal/error-y; merchant said "that's not me."
- **Cause:** KYC data in outbound targeting DB is a nightly snapshot; 24h stale
- **Proposed fix:** Switch targeting DB to streaming CDC; estimated 3 weeks
- **Mitigation for pilot:** Pull KYC fresh at call-placement time (adds 250ms); acceptable for pilot volumes
- **Owner:** Data Engineer (Nisha)
- **Status:** ⚠️ MITIGATED for pilot. Proper fix queued for Phase 2.

---

## Sev-3 Issues (10 — 3 fixed, 7 queued)

| ID | Issue | Fix Status | Planned Release |
|---|---|---|---|
| BUG-D09 | Greeting slightly too formal for Surat Gujarati-Hindi speakers | Backlog | Phase 2 (with Gujarati support) |
| BUG-D10 | Price rendering in Hindi uses "rupaye" for ₹1 (should be "rupaya") | ✅ FIXED Wednesday | — |
| BUG-D11 | WA card button "Change/Cancel" too long on small Android screens | ✅ FIXED Thursday | — |
| BUG-D12 | Post-call survey SMS arrives at 5:00 (spec was 5:30) | Backlog | Week 2 |
| BUG-D13 | Agent sometimes pauses 200ms longer than optimal before reading options | Backlog — needs TTS tuning | Week 2 |
| BUG-D14 | "Aath" (eight) misrecognized as "saath" (with) in 4% of STT outputs | ✅ FIXED Thursday (lexicon bias added) | — |
| BUG-D15 | WhatsApp resume-link URL shortener occasionally returns 404 in first 30 sec | Backlog — race condition in link-generation service | Week 2 |
| BUG-D16 | Human agent console doesn't display merchant's preferred payment history | Backlog | Week 3 |
| BUG-D17 | "Kal" at 11 PM should default to "tomorrow" but sometimes rounds to "today" | Backlog — minor edge case | Week 2 |
| BUG-D18 | Callback request after proactive decline doesn't schedule for optimal time | Backlog — enhancement | Phase 2 |

---

## Quality Metrics From Dogfood

| Metric | Target | Observed | Status |
|---|---|---|---|
| Calls completed successfully | — | 42 / 47 | ✅ 89% |
| Avg call duration | ≤ 4 min (dogfood) | 3 min 22 sec | ✅ |
| p95 call duration | ≤ 5 min | 4 min 18 sec | ✅ |
| Credit attach rate | No target in dogfood | 36% | 📊 Informational |
| Invoice delivery within 10 sec of PNR | > 95% | 97.4% (1 failure = BUG-D07 pre-fix) | ✅ |
| NPS from 20 dogfooders | > 40 (internal) | 58 | ✅ |

### What Dogfooders Loved (Qualitative)
- "Faster than opening MMT. I'd use this every time if I had access."
- "The GST invoice just showing up was a revelation."
- "Credit offered first actually changed my behavior — I used credit instead of my debit card."
- "Agent picked up my accent within 2 sentences and stuck with Hindi."

### What Dogfooders Hated (Qualitative)
- "The 'business name confirm karein' step feels redundant when it's obviously right." (Decision: keep — catches 5% KYC mismatch per spec)
- "Wish I could say 'same as last time' for the route." (Backlog: repeat-booking shortcut — Phase 2)
- "First option always IndiGo — suggestion felt biased." (Ranking algorithm now logs per-call ranking weights for audit; investigation ongoing)
- "Hold music during warm transfer was too loud." (Telecom vendor adjusting; low-Sev)

---

## Sev-1 Incident Response Test (S11 milestone)

Simulated Sev-1 incident: Credit API returning 5xx for 8% of calls starting 14:07 IST on Thursday.

- **Alert fired:** 14:08 (PagerDuty)
- **On-call acknowledged:** 14:09
- **CTO paged:** 14:10
- **Root cause identified:** 14:18 (expired mTLS cert rotated earlier that morning)
- **Fix deployed:** 14:27
- **All-clear:** 14:34

**Total incident duration:** 27 minutes. Within Sev-1 2-hour SLA. Post-mortem filed Friday.

---

## Regression Suite Status

All 18 failure modes (edge-cases-register.md) have automated regression tests at the service level. 16 of 18 have end-to-end regression via `ivr-stress-bot`. Two (FM-11, FM-16) require manual execution during Week 1 soft launch due to environmental dependencies (specific telecom conditions).

---

## Outputs to Week 1

- All 18 bugs either fixed or documented
- No Sev-1 open; 1 Sev-2 mitigated with monitoring
- NLU v1.0-b deployed (from BUG-D03 retrain)
- Hinglish NLU accuracy lifted to 94.6% (from 92.8% at end of Week −2)
- Regression suite updated with 9 new test cases from dogfood findings
- Week 1 first-week monitoring plan: extra vigilance on BUG-D08 mitigation, BUG-D15 race condition

---

*Owner: QA Lead · Co-owned: Engineering Lead, CTO · Last updated: April 2026 (EOW −1)*
