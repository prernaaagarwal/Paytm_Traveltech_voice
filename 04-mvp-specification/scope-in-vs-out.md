# Scope: In vs. Out
**What's MVP · What's Post-MVP · The Reasoning Behind Every Cut**
*Paytm Merchant Travel Desk | 04-mvp-specification*

---

## Decision Framework

Every scope decision was made against three tests:

1. **3-Minute Test:** Does adding this feature push average call duration above 3 minutes?
2. **Credit Test:** Does cutting this feature meaningfully reduce credit attachment rate?
3. **Hypothesis Test:** Do we need this to validate the core hypothesis — *does voice + auto-GST + credit bundling convert merchants better than the status quo?*

If a feature fails the 3-Minute Test or fails the Hypothesis Test and passes the Credit Test, it goes to Phase 2. If it fails all three, it goes to backlog.

---

## ✅ IN SCOPE — MVP v1.0

### Core Travel

| Feature | Why In | Credit Test | 3-Min Test | Hypothesis Test |
|---|---|---|---|---|
| Domestic flights (top 35 routes) | Core product | ✅ Primary credit driver | ✅ < 30 sec search | ✅ Required to test |
| Economy class only | Simplifies pricing, 80%+ of merchant bookings | ✅ Unchanged | ✅ No complexity added | ✅ |
| Single traveler | 92%+ of merchant trips; group adds auth + GST complexity | ✅ Unchanged | ✅ | ✅ |
| One-way and round-trip | Round-trip is two one-way bookings in sequence; same flow | ✅ Double credit per trip | ✅ Round-trip adds < 60 sec | ✅ |
| Top 20 city pairs | Covers > 85% of pilot merchant routes | ✅ | ✅ | ✅ |

### Authentication & Identity

| Feature | Why In | Rationale |
|---|---|---|
| Phone number = auth | Zero friction; critical for < 3-min target | Removing OTP eliminates #1 drop-off in app booking |
| KYC auto-fill (name, GSTIN, city) | Removes all form-filling | Without this, Stage 3 alone takes 2+ min |
| Guest flow (phone not found) | 5% of calls; can't dead-end these merchants | Minimal build; big trust impact |

### Payment

| Feature | Why In | Rationale |
|---|---|---|
| Paytm Business Credit (primary) | North star metric driver | The entire business case depends on this |
| Paytm Wallet (fallback) | ~60% of merchants have no credit; need a fallback | Without wallet, 60% of calls can't convert |
| Credit explicit nudge ("Credit se karein ya wallet se?") | +15 pp credit attach vs. passive | Direct revenue impact; trivial to implement |

### GST & Invoice

| Feature | Why In | Rationale |
|---|---|---|
| Auto GST invoice (PDF) | #1 merchant pain point; table stakes for B2B | Without this, we're just another OTA |
| GSTIN validation (real-time) | Required for GST compliance | Cannot generate compliant invoice without valid GSTIN |
| WhatsApp delivery | Preferred channel for 95% of pilot merchants | Email delivery has < 40% open rate for this segment |
| Business name verbal confirmation | Catches KYC typos that would invalidate invoice | 5% of GSTINs have name mismatch in KYC |

### Voice & AI

| Feature | Why In | Rationale |
|---|---|---|
| Hindi + English + code-switching | Pilot cities are primarily Hindi-speaking | English-only product fails 60%+ of pilot merchants |
| STT noise pre-processing | Kirana shops are high-noise environments | Without noise handling, STT WER drops below 70% in shops |
| 5-stage FSM dialogue manager | Structured flow required for consistent < 3-min booking | Free-form LLM too expensive and unpredictable at MVP |
| 18 failure mode handlers | Every failure must have a recovery path ("never lose the merchant") | Without this, any API failure = abandoned booking = NPS crater |
| Human handoff (5 trigger types) | 10–15% of calls need escalation; no dead ends | Soft launch without this = customers with no escape hatch |
| Interruption handling (VAD) | Natural conversation; 15% of calls involve interruptions | Without this, product feels like IVR, not conversation |

### WhatsApp Companion

| Feature | Why In | Rationale |
|---|---|---|
| Flight options cards (2 options) | Screen verification for price/time comparison | Voice alone for comparison = wrong selections |
| Payment confirmation screen | High-stakes financial action needs visual verification | Reduces "did I confirm?" anxiety |
| Booking confirmed + invoice card | Invoice delivery mechanism; post-booking reference | Required by GST feature |
| Post-call summary + action buttons | Reduces repeat call volume for simple tasks | Each self-serve action = cost saved + NPS improved |

### Proactive Outbound

| Feature | Why In | Rationale |
|---|---|---|
| Seasonal pattern detection (Trigger 1 only) | 30% of bookings expected from proactive calls | Validates outbound as a channel; tests credit attach in non-inbound context |

---

## ❌ OUT OF SCOPE — MVP v1.0

### Travel Inventory

| Feature | Phase | Why Cut |
|---|---|---|
| **Train booking (IRCTC)** | Phase 2 (Month 5–6) | Different API, different NLU complexity (class: SL/3A/2A, quota, waitlist), avg ticket ₹1,200 vs ₹8,000 (10× lower credit value). Adds 90+ sec to call. Fails 3-Minute Test badly. |
| **Hotel booking** | Phase 3 (Month 13) | Requires entirely separate conversation flow (star rating, locality, amenity preferences). Adding to the same call would push duration to 6–8 minutes. Highest ROI in Phase 3 — 8–12% commission vs 2% on flights. Fails 3-Minute Test. |
| **International flights** | Phase 3 (Month 18) | Passport number collection (new PII type, new privacy workflow), forex credit (needs RBI FEMA compliance), zero-rated GST for international (different invoice format). Complexity is 4–5× domestic. Passes Credit Test (₹35K avg booking value) but fails Hypothesis Test — domestic must be proven first. |
| **Cab / car rental** | Backlog | Extremely low margin, different inventory system, different UX paradigm. Not a meaningful credit event. |
| **Bus booking** | Backlog | < 1% of merchant travel; extremely low ticket value; no credit activation value. |

### Payment Methods

| Feature | Phase | Why Cut |
|---|---|---|
| **UPI payment** | Backlog | Intentionally excluded in MVP. Adding UPI creates an easy escape hatch that reduces credit attachment. North star metric depends on credit being the path of least resistance. |
| **Debit / credit card** | Phase 2 | Same reasoning as UPI. Once credit behavior is established, adding card as alternate is safe. |
| **EMI on travel credit** | Phase 2 (Month 8) | Requires separate underwriting flow, different repayment schedule display in WA card, and new NLU patterns ("3 EMI mein split karo"). Meaningful revenue driver but wrong for MVP. |
| **Split payment (₹2K credit + ₹2K wallet)** | Phase 3 | Adds UI/UX complexity; merchant confusion risk. Not in the 3-minute budget. |
| **Corporate credit card** | Phase 4 | Different liability model; requires enterprise features (travel policy, receipt codes). Wrong segment for MVP. |

### Traveler Types

| Feature | Phase | Why Cut |
|---|---|---|
| **Group bookings (2–6 travelers)** | Phase 2 (Month 9) | 8% of merchant trips; adds 2× complexity (multiple passenger names, multiple GSTINs or one invoice for group, seat assignment). Fails 3-Minute Test. |
| **Booking for an employee** | Phase 2 | Requires multi-user authorization model ("book for Ravi Kumar my employee") — needs employee database, auth delegation. Critical for SME segment but wrong for solo merchant MVP. |

### Language Support

| Feature | Phase | Why Cut |
|---|---|---|
| **Gujarati** | Phase 2 (Month 6) | Surat pilot will generate demand signal; add after pilot validates the model. NLU fine-tuning requires 500+ Gujarati examples not yet collected. |
| **Marathi** | Phase 2 (Month 7) | Same as Gujarati. Pune expansion in Phase 2 will drive need. |
| **Tamil** | Phase 2 (Month 8) | Chennai expansion in Phase 2. |
| **Telugu** | Phase 2 (Month 9) | Hyderabad expansion in Phase 2. |

### Post-Trip Features

| Feature | Phase | Why Cut |
|---|---|---|
| **Expense report export (Tally/Zoho)** | Phase 2 (Month 7) | CA's biggest ask after auto-invoice. Builds on invoice infrastructure already in MVP. Excluded from MVP to reduce engineering scope. |
| **Expense closing via voice** | Phase 3 | "I'm back from my trip, close my expense" — entirely new NLU flow, new booking state machine. High value but complex. |
| **Receipt reconciliation** | Phase 3 | Requires integration with merchant's accounting software. Post-MVP after expense export is proven. |
| **Mileage tracking** | Backlog | Low merchant demand in current segment (they don't have corporate mileage programs). |

### Enterprise Features

| Feature | Phase | Why Cut |
|---|---|---|
| **Travel policy enforcement** | Phase 4 | "No flights > ₹8,000" — requires policy database, real-time policy check in booking flow. Wrong segment for solo merchant MVP. |
| **Manager approval workflow** | Phase 4 | Multi-user async flow — fundamentally different product architecture. |
| **Company travel dashboard** | Phase 4 | Web portal + analytics + multi-user management. 5–50 employee SME segment. Phase 4 strategic move. |
| **SAML SSO integration** | Never for SMB | Enterprise-only feature; explicitly excluded from SMB product positioning. |

### AI/Voice Upgrades

| Feature | Phase | Why Cut |
|---|---|---|
| **LLM-native agent (GPT-4/Claude)** | Phase 4 (Month 28) | 10–15× higher inference cost than BERT; unpredictable in structured financial flows; risk of hallucinated flight details. MVP uses structured FSM with BERT. Upgrade after credit-travel habit established. |
| **Proactive credit-drop trigger (Trigger 2)** | Phase 2 | Risk of feeling intrusive if implemented before trust is established. Pilot must prove basic proactive works first. |
| **Competitor activity detection (Trigger 3)** | Post-Phase 3 | Requires third-party data partnerships; privacy/regulatory risk. Only viable after regulatory landscape is clear. |
| **Gujarati/regional language voice** | Phase 2 | Same as language support above. |

---

## Why the 3-Minute Constraint Is Sacred

The 3-minute constraint is not arbitrary. It is the product's central design constraint and the thing that makes it different from everything else in the market.

```
What happens at different call durations:
  < 2 min: Merchant hasn't had time to verify options; rushed; lower NPS
  2–3 min: Sweet spot — fast enough to feel efficient; slow enough to verify
  3–4 min: Merchant starts losing patience; NPS drops; starts second-guessing
  4–5 min: Merchant begins comparing to MMT app mentally; "this isn't faster"
  > 5 min: Product claim of "faster than app booking" is false; trust collapses

Every feature that goes into the call must justify its time cost:
  GST invoice confirmation (10 sec): Worth it — saves 5 min of manual CA work
  Hotel booking (90 sec): Not worth it — separate call is better UX
  EMI selection (45 sec): Not worth it — separate WA flow post-booking
  Train booking (60 sec extra): Not worth it — kills the speed claim
```

The 3-minute constraint is also a **hiring and culture signal**: every engineer who joins this team knows that the product's soul is speed. Features that violate the 3-minute constraint don't just get cut — they get reconsidered entirely (can this be async? can this be on the WA screen instead of voice?).

---

## The Scope Decision Accountability Matrix

| Decision | Made By | Rationale Documented | Reversible? |
|---|---|---|---|
| No UPI payment in MVP | PM (with Credit team alignment) | Yes — above | After pilot: yes, if credit attach ≥ 40% |
| No hotels in MVP | PM | Yes — 3-min constraint | Phase 3: yes |
| No trains in MVP | PM | Yes — low credit value | Phase 2: yes |
| No EMI in MVP | PM (with Credit team) | Yes — underwriting complexity | Phase 2: yes |
| No international in MVP | PM (with Legal) | Yes — compliance complexity | Phase 3: yes |
| No LLM agent in MVP | PM + ML Lead | Yes — cost + reliability | Phase 4: yes |
| Hindi + English only | PM | Yes — pilot cities | Phase 2: yes |
| 3-min constraint | PM | Yes — above | Never |

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/04-mvp-specification*
