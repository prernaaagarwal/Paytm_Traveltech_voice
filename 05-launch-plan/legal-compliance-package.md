# Legal & Compliance Package
**Tax Counsel Sign-Off · TRAI DND Integration · PDPA Consent Copy**
*Paytm Merchant Travel Desk | 05-launch-plan | Legal-Owned*

---

## Purpose

This package consolidates the three legal deliverables required for Week 1 launch: (1) tax counsel sign-off on the GST invoice format, (2) TRAI DND compliance tool integration, and (3) approved PDPA consent copy for onboarding and in-call disclosures.

Each section is the artifact Legal hands to Engineering, Ops, and the auditor as the compliance record.

---

## Section 1: Tax Counsel Sign-Off

### Counsel of Record
| Field | Value |
|---|---|
| **Firm** | Office of the General Counsel, Paytm (in-house) |
| **Lead Counsel** | Head of Tax Advisory, VP |
| **External Review** | Crossed-signed by KPMG India GST Advisory (panel counsel) |
| **Scope** | GST invoice format, HSN classification, invoice numbering, cross-state IGST handling |

### What Was Reviewed

1. Invoice PDF template (3 variants: airline A one-way, airline B one-way, airline A round-trip = 2 invoices)
2. Tax computation logic: `base_fare + CGST(6%) + SGST(6%)` for same-state; `base_fare + IGST(12%)` for cross-state
3. HSN code 996411 for domestic scheduled passenger air transport
4. Invoice numbering scheme: `PTM/YYYY/MM/NNNNNN` with guaranteed monotonic sequence per merchant
5. CA-panel feedback document (`ca-invoice-approval-packet.md`) and 5 resulting design changes

### Counsel Sign-Off Statement

> *"I have reviewed the GST invoice templates, tax computation logic, and operational procedure for Paytm Merchant Travel Desk (MVP v1.0). The format is compliant with the CGST Act 2017 and rules thereunder as of April 2026. The HSN classification 996411 is correctly applied. The cross-state IGST handling follows Section 7 of the IGST Act. The monotonic invoice-numbering requirement under Rule 46 is operationally guaranteed. No objection to production launch for the 500-merchant pilot scope.*
>
> *This sign-off is valid through 31 December 2026 or until any of: (a) GST rate revision; (b) HSN code amendment; (c) change in invoice data fields captured. Any of these triggers re-review."*

**Signed:** Head of Tax Advisory, Office of General Counsel. Dated Week −1 Thursday. Letter on file — original with Legal, certified copy with Finance.

### External Counsel Cross-Sign

KPMG's India GST Advisory panel reviewed the same package on a consulting engagement. Written concurrence received Week −1 Wednesday; letter on file. External counsel flagged no additional concerns.

### Escalation Triggers (if any of these occur, re-engage counsel)
- GST Council rate revision affecting HSN 996411
- State-level GST compliance bulletins affecting cross-state IGST
- Change in invoice data fields (e.g., adding passenger PAN)
- Expansion to international flights (new zero-rated rules apply)

---

## Section 2: TRAI DND Compliance Tool

### Regulatory Context

TRAI's Telecom Commercial Communications Customer Preference Regulations (TCCCPR 2018, as amended) prohibit commercial communications to registered Do-Not-Disturb (DND) subscribers. This applies to outbound proactive calls; inbound helpline traffic is merchant-initiated and exempt.

For Paytm Merchant Travel Desk, the DND check applies only to:
- Stage 1 proactive outbound campaigns (seasonal pattern trigger)
- Post-declined re-contact flow (after 2 declines, merchant is auto-DND)

### Integration Architecture

```
┌──────────────────────┐
│ Outbound campaign    │
│ targeting service    │
└──────────┬───────────┘
           │ POST /outbound/v1/call-candidate
           ▼
┌──────────────────────┐      ┌──────────────────────┐
│ DND-gate service     │─────►│ TRAI DND Registry    │
│ (pre-call check)     │◄─────│ API (via aggregator) │
└──────────┬───────────┘      └──────────────────────┘
           │
      ┌────┴────┐
      │         │
  OK to call   Blocked
      │         │
      ▼         ▼
  SIP trunk   Marked in CRM
             (no retry 180d)
```

### Service Behavior

```
Request:  POST /dnd/v1/check
Body:     { phone_e164, campaign_id, campaign_category }
Response: { allowed: true|false, reason: string, checked_at: timestamp }

Cached TTL: 7 days per phone (DND state changes rarely; registry refresh weekly)
Fail-closed: If DND API unavailable, assume DND = TRUE (no call placed)
Audit log:  Every call-candidate logged with decision + reason
```

### Operational Safeguards

1. **Internal DND list** — any merchant who explicitly opts out (verbally on a call, WA reply STOP, or merchant-app toggle) is added to an internal DND list that overrides the TRAI registry. Internal list is the source of truth.
2. **Auto-DND after 2 declines** — declining a proactive call twice in 90 days auto-adds to internal DND for 180 days.
3. **Quiet hours** — no outbound calls before 09:00 or after 21:00 IST, regardless of DND status.
4. **Campaign category** — outbound flagged `SERVICE_CONTEXTUAL` (not `PROMOTIONAL`) since calls propose travel to merchants with relevant historical patterns, not generic offers. This positioning was reviewed by Legal and deemed compliant.

### Testing & Verification

| Test | Result |
|---|---|
| Test phone on TRAI registry → blocked | ✅ Confirmed (3 of 20 dogfooders) |
| API outage simulation → fail-closed | ✅ No calls placed during simulated outage |
| Internal DND override (STOP WA reply) | ✅ Next-day proactive call blocked |
| Auto-DND after 2 declines | ✅ Third call attempt blocked |
| Quiet-hours block | ✅ Scheduled 22:00 call moved to next 09:00 |

### Audit Requirements

- All DND check results retained 24 months (TRAI requirement)
- Weekly internal audit: sample 100 outbound calls; verify DND check fired for each
- Annual external audit by TRAI-designated Access Provider

---

## Section 3: PDPA Consent Copy (Onboarding + In-Call)

### Onboarding SMS (Opt-In)

**Hindi (hi_IN):**
> *"Namaste [Name], Paytm Business Travel — ek naya voice-based service. Sirf phone karke domestic flight book karein, GST invoice free. Aapki KYC aur GSTIN details is service ke liye use ki jayengi. Interested hain? Reply YES. Opt-out: STOP bhejein. Details: [link]"*

**English (en_IN):**
> *"Hi [Name], Paytm Business Travel — a new voice-based service. Book domestic flights by phone; GST invoice included free. Your KYC and GSTIN details will be used for this service. Interested? Reply YES. To opt out: send STOP. Details: [link]"*

**Legal review:** Approved Week −2 Friday. Captures: (1) affirmative opt-in; (2) data-use disclosure; (3) opt-out mechanism. PDPA §11 compliant.

### Onboarding Consent Form (Digital)

Merchants who reply YES receive a WhatsApp message with a one-tap consent form. Form text:

> *By tapping "I agree" below, you consent to: (1) use of your Paytm KYC and GSTIN data to personalize and process bookings; (2) recording of calls for quality and audit for 30 days; (3) sharing booking details with your airline of choice; (4) generation of GST invoices in your business name. You can withdraw consent by calling 1800-XXX-0100 and asking to unsubscribe. Full privacy policy: [link]*

**Buttons:** `I agree`, `Not now`, `Privacy policy`

**Legal review:** Approved Week −2 Friday. Plain language; buttons are affirmative action. DPO sign-off on file.

### In-Call Disclosure (TTS, Stage 1)

Agent plays a 3-second disclosure immediately after the greeting on the first call of a merchant's lifetime:

**Hindi:**
> *"Yeh call quality aur service ke liye record ho sakti hai."*

**English:**
> *"This call may be recorded for quality and service."*

Subsequent calls: no disclosure (one-time; PDPA §13 allows one-time consent with persistence). Merchant can request deletion of all recordings via human agent.

### Privacy Policy Link

`paytm.com/business-travel/privacy` — dedicated landing page (not buried in general Paytm privacy policy). Contents:
- Data collected (phone, KYC, GSTIN, voice recording, booking history)
- How it is used (service delivery, fraud prevention, improvement)
- Retention: 30 days for recordings, 7 days for raw transcripts, 13 months for analytics, 7 years for GST invoices
- Sharing: airline partners for bookings; GSTN for validation; WhatsApp (Meta) for message delivery
- Rights: access, correction, deletion, opt-out
- Contact DPO: `dpo@paytm.com`

**Legal review:** Approved Week −1 Monday after plain-language rewrite.

### Withdrawal Mechanism

Any of:
1. Verbal during a call: *"Bas karo, unsubscribe karo"* → agent triggers opt-out
2. WA reply: `STOP`
3. Merchant app: toggle in Preferences → Communications
4. Email: `dpo@paytm.com`
5. Phone: 1800-XXX-0100, request human agent, ask for opt-out

SLA: opt-out processed within 24 hours; confirmation SMS sent.

---

## Sign-Off Summary

| Gate | Signatory | Status |
|---|---|---|
| Tax counsel GST invoice sign-off | Head of Tax Advisory + KPMG | ✅ |
| TRAI DND compliance tool integrated + tested | Legal + Platform Eng | ✅ |
| PDPA onboarding consent (SMS + digital form) | Legal Counsel + DPO | ✅ |
| In-call recording disclosure (TTS) | Legal Counsel | ✅ |
| Privacy policy landing page | Legal Counsel + DPO | ✅ |
| Opt-out mechanism (5 channels) | Legal + Customer Ops | ✅ |

All compliance gates for Week 1 are closed. No open items.

---

*Owner: Legal Counsel · Co-signers: DPO, Tax Counsel · Last updated: Week −1 Friday*
