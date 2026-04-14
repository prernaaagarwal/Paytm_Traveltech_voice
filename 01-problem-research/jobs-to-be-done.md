# Jobs to Be Done
**Paytm Merchant Travel Desk | Functional + Emotional + Social Jobs**

---

## Framework Overview

JTBD maps what merchants are *hiring* a product to do — not what features they want, but what progress they're trying to make in their lives and businesses.

**Format:**  
> *When [situation], I want to [motivation], so I can [expected outcome].*

---

## Primary JTBD: Core Booking Job

### Job 1 — The Speed Job
> *When I realize I need to travel for business in the next few days, I want to book a flight and get a GST invoice in one go, so I can stay focused on running my shop instead of juggling tools.*

**Persona:** Both Ramesh and Priya  
**Frequency:** 2–8× per year  
**Current hire:** MakeMyTrip + manual GST + cash advance = 17 min, 3+ tools  
**Desired hire:** One call → booked + invoiced + paid via credit → <3 min  
**Success signal:** Booking confirmed before merchant hangs up  

---

### Job 2 — The Cash Flow Job
> *When I need to book a trip, I want to use my approved business credit line instead of dipping into working capital, so I can preserve cash for inventory and supplier payments.*

**Persona:** Priya Shah (and credit-enabled variants of Ramesh)  
**Frequency:** Every trip  
**Current hire:** Personal savings or cash advance from business partner  
**Desired hire:** Paytm Business Credit surfaced at point of booking  
**Success signal:** 40%+ credit attachment rate in pilot  

---

### Job 3 — The Compliance Job
> *When I return from a business trip, I want a CA-ready GST invoice already in my WhatsApp, so I can forward it immediately and claim the tax deduction without doing any manual data entry.*

**Persona:** Both personas, more acute for Priya (6+ trips/year)  
**Frequency:** After every trip  
**Current hire:** Manual invoice creation in notebook / Excel / screenshot editing  
**Desired hire:** Auto-generated PDF on WhatsApp within 10 seconds of booking  
**Success signal:** 0% GST invoice error rate vs. 40% baseline  

---

## Secondary JTBD: Proactive & Planning Jobs

### Job 4 — The Anticipation Job
> *When my annual supplier meeting season approaches, I want someone to remind me and help me book early, so I can get cheaper fares before prices spike.*

**Persona:** Ramesh (seasonal FMCG meetings), Priya (sourcing fairs)  
**Frequency:** 1–2× per year (high-value moments)  
**Current hire:** Self-reminder in notes app or partner reminds them  
**Desired hire:** Paytm proactive call with early booking nudge  
**Success signal:** 40% conversion on proactive outbound calls  

---

### Job 5 — The Credit Awareness Job
> *When I have an approved credit line I'm not using, I want to know I can deploy it for travel, so my working capital stays intact during busy business seasons.*

**Persona:** Priya and similar credit-approved merchants  
**Frequency:** Ongoing (idle credit activation)  
**Current hire:** Credit line sits unused  
**Desired hire:** Paytm proactive call + credit awareness nudge at point of travel intent  
**Success signal:** Credit utilization rate increases from baseline  

---

### Job 6 — The Interruption-Free Job
> *When I'm packing inventory or serving customers and I suddenly remember I need to book a flight, I want to do it without stopping what I'm doing, so I don't lose focus or miss the booking window.*

**Persona:** Priya primarily (high-frequency traveler with complex shop operations)  
**Frequency:** Several trips per year  
**Current hire:** Set a reminder to book later → often forgets → last-minute expensive booking  
**Desired hire:** Voice call that books while merchant multitasks  
**Success signal:** Merchants cite "didn't have to stop working" as key NPS driver  

---

## Emotional JTBD

These are the deeper motivations beneath the functional jobs.

| Emotional Job | What Merchants Actually Want to Feel |
|---|---|
| **Feel competent** | "I handled my business travel like a professional, not a confused amateur" |
| **Feel secure** | "My cash is safe, I used my credit line correctly, my taxes are covered" |
| **Feel respected** | "This service knows who I am and doesn't treat me like a random consumer" |
| **Feel in control** | "I can book a trip from wherever I am, in under 3 minutes, with no dependency on anyone else" |
| **Feel trusted** | "Paytm knows my business and proactively looks out for me" |

**Design implication:** The personalized greeting ("Namaste Ramesh bhai"), the auto-filled GSTIN, and the pre-approved credit balance shown upfront are all emotional reassurance signals — not just functional conveniences.

---

## Social JTBD

| Social Job | Context |
|---|---|
| **"I'm a serious businessperson"** | Ramesh wants his CA, partner, and suppliers to see he handles business properly — including correct GST invoices and proper expense records |
| **"I'm a modern merchant"** | Priya wants to feel she's using the best tools available, not struggling with outdated manual processes |
| **"I manage my cash well"** | Using Paytm Business Credit (vs. emergency personal cash) signals financial sophistication to partners and family |

---

## JTBD Hierarchy Map

```
JTBD Priority for MVP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔴 MUST NAIL (Threshold Jobs — hygiene to compete)
   ┣ Job 1: Speed Job — Book in <3 min
   ┣ Job 3: Compliance Job — Auto GST invoice

🟡 SHOULD EXCEL (Differentiator Jobs — reasons to choose Paytm)
   ┣ Job 2: Cash Flow Job — Seamless credit at booking
   ┣ Job 6: Interruption-Free Job — Voice while multitasking

🟢 NICE TO HAVE (Delighter Jobs — loyalty drivers)
   ┣ Job 4: Anticipation Job — Proactive seasonal reminders
   ┣ Job 5: Credit Awareness Job — Idle credit activation

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Jobs NOT Being Hired For (Out of Scope Signals)

These jobs came up in interviews but are **out of scope for MVP** — important to document so the team doesn't over-build:

| Job Mentioned | Why Out of Scope |
|---|---|
| "Book hotels for the same trip" | Separate NLU flow, different inventory, triples call complexity |
| "Book for my employee too" | Group booking = different auth + GST complexity |
| "Get train tickets instead" | Different API, lower margin, longer search time |
| "Manage a travel budget for the month" | Enterprise feature; SMBs don't have formal budgets |
| "File my full expense report" | Post-trip job; separate product surface needed |

**Principle:** The MVP does one thing brilliantly (flight + credit + GST invoice). It does not try to be an expense management platform.

---

## Connection to Product Decisions

| JTBD | Product Decision It Drives |
|---|---|
| Job 1 (Speed) | 3-minute call constraint as a hard design boundary |
| Job 2 (Cash Flow) | Credit offered before wallet — explicit nudge in Stage 3 |
| Job 3 (Compliance) | GST invoice auto-generated and WhatsApp-delivered within 10 sec |
| Job 4 (Anticipation) | Proactive outbound calls for seasonal travel patterns |
| Job 5 (Credit Awareness) | Outbound trigger on idle credit lines |
| Job 6 (Interruption-Free) | Voice-first, WhatsApp-second — no app screen required |

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/01-problem-research*
