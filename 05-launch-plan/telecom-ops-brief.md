# Telecom Ops Brief — 1800 Helpline Provisioning
**Request for Information / Requirements for Vendor Selection**
*Paytm Merchant Travel Desk | 05-launch-plan*

---

## Why This Is the Critical Path

The 1800 helpline number is a 6-week lead-time item on the critical path. Every other workstream (STT/TTS, NLU, Redis, integrations) can proceed in parallel, but without a merchant-facing number, the product does not launch.

**If the number is not provisioned by end of Week −2, the pilot slips.** This brief goes to Telecom Ops this Monday with a 48-hour turnaround ask.

---

## Target Requirements

### Number Characteristics

| Requirement | Value | Rationale |
|---|---|---|
| **Number type** | 1800-series (toll-free) | Merchant should never hesitate to call over cost |
| **Memorability** | Prefer repeating digits or structured pattern | Example format: 1800-PAYTM-GO (mnemonic) or 1800-XXX-0000 |
| **Geographic scope** | Pan-India | All 3 pilot cities + scale to 150 cities |
| **Vanity vs. random** | Vanity preferred; random acceptable | Vanity drives unaided recall; fallback if 6-week cap at risk |
| **24/7 availability** | Yes | Merchants travel early morning + late evening |

### Capacity

| Metric | Pilot | Post-pilot | Year 1 Peak |
|---|---|---|---|
| **Concurrent calls** | 50 | 500 | 3,000 |
| **Daily call volume** | 100–200 | 2,000 | 30,000 |
| **Peak ratio** | 3× avg | 3× avg | 4× avg |
| **Answer SLA** | <2 rings | <2 rings | <2 rings |

### Integrations

| System | Interface | Purpose |
|---|---|---|
| **Cloud telephony** | SIP trunk → our IVR/dialogue layer | Ingest audio into STT pipeline |
| **Caller ID passthrough** | E.164 format, unsuppressed | Stage 1 phone-based KYC lookup |
| **Call recording** | Stereo (agent + merchant on separate channels) | Quality monitoring, AES-256 at rest, 30-day retention |
| **DTMF capture** | Supported | Human handoff queue routing |
| **Warm transfer** | SIP REFER to human agent queue | US-011 handoff flow |
| **Call-drop webhook** | Real-time event to our session service | US-010 recovery trigger |

### Compliance

| Requirement | Reference |
|---|---|
| TRAI DND registry integration for outbound | `04-mvp-specification/prd.md` NFR-C2 |
| PDPA-compliant recording disclosures | NFR-C1 |
| DoT 1800 series licensing | Telecom vendor owns |
| No scam-dialer blacklisting history | Validate carrier reputation before commit |

---

## Vendor Shortlist (RFI Targets)

| Vendor | Strengths | Weaknesses | Expected Lead Time |
|---|---|---|---|
| **Tata Communications** | Enterprise-grade, existing Paytm contract, SIP-ready | Premium pricing | 4–6 weeks |
| **Airtel Business** | Pan-India coverage, vanity number library | Slower provisioning | 5–7 weeks |
| **Exotel / Knowlarity** | Fastest provisioning (1–2 weeks), API-first | Smaller numbers pool, shared carrier reputation risk | 1–3 weeks |
| **Jio Business** | Aggressive pricing, large pool | Newer to 1800 enterprise; integration track record thin | 4–6 weeks |

**PM recommendation:** Issue RFI to all 4 on Monday. Decision by Wednesday. Likely primary: Tata (reliability); fallback: Exotel (speed if Tata slips).

---

## Fallback If 6-Week Lead Time Is Infeasible

See also `open-questions-tracker.md` OQ-3 fallback.

**Option A — Existing Paytm 1800 with IVR branch:**
- Use `1800-1020-1212` (existing Paytm Care) with a new menu option ("Press 3 for Paytm Business Travel")
- Adds 8–10 seconds to Stage 1 (IVR traversal)
- Risk: Paytm Care team operational ownership overlap — needs explicit carve-out
- Time to implement: 1 week

**Option B — Exotel short-term + Tata migration:**
- Launch pilot on Exotel number (provisioned in 2 weeks)
- Migrate to permanent Tata number by Month 3
- Risk: Merchants calling the old number post-migration need call forwarding (2–3% leakage)
- Time to implement: 2 weeks

**Option C — Delay launch:**
- Slip pilot by 2 weeks to accommodate Tata 6-week lead
- Risk: Every other workstream sits idle or loses cadence
- Only acceptable if OQ-3 confirms genuine infeasibility

**Decision owner:** Product GM, by end of Week −3. Default to Option A if no clear winner by decision date.

---

## Asks Delivered to Telecom Ops This Monday

1. **RFI sent to 4 vendors** with requirements above (Monday EOD)
2. **Existing Paytm 1800 IVR capacity check** — is there room to add menu option 3? (Tuesday)
3. **Contract template ready** to shorten vendor negotiation (by Wednesday)
4. **DoT licensing pre-check** — is anything about our use-case that needs fresh DoT approval? (by Wednesday)

---

## Tracking

| Milestone | Owner | Target | Status |
|---|---|---|---|
| RFI issued to 4 vendors | Telecom Ops | Monday | 🔴 Not started |
| Vendor responses received | Vendors | Wednesday | 🔴 Pending RFI |
| Vendor selection + contract signed | Telecom Ops + Legal | End of Week −3 | 🔴 Pending |
| Number provisioned in staging | Vendor | Week −2 Monday | 🔴 Pending |
| IVR + SIP trunk integration live | Platform Eng + Vendor | Week −2 Friday (P07) | 🔴 Pending |
| End-to-end call test to AI agent | QA | Week −2 Friday | 🔴 Pending |

---

## Escalation

If any of the following occur, PM escalates to Product GM within 24 hours:
- No vendor responses by Wednesday EOD
- All vendor lead times >6 weeks
- Contract negotiation blocker (pricing or terms)
- Paytm Care team blocks IVR branch option (if Option A needed)

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/05-launch-plan*
