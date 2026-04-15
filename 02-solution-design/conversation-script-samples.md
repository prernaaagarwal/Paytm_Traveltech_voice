# Conversation Script Samples
**Hindi + English Dialogues | All Stages + Edge Cases**
*Paytm Merchant Travel Desk | 02-solution-design*

---

## Reading Guide

```
[A] = Agent (AI voice)
[M] = Merchant
[SYS] = System event (background, not spoken)
[WA] = WhatsApp companion message sent
⏱️ = Timestamp from call start
🔴 = Failure / edge case path
✅ = Happy path
```

---

## Script 1: Ramesh — Happy Path (Hindi)
**Profile:** Kirana owner, Kanpur. No Paytm credit. Wallet payment.
**Route:** Kanpur → Delhi | Tomorrow morning
**Duration:** 2 min 34 sec

---

```
⏱️ 0:00
[SYS] Merchant dials 1800-XXX-XXXX
[SYS] IVR plays: "Paytm Business Travel mein aapka swagat hai. Kripya hold karein."
[SYS] KYC lookup fires: phone → Ramesh Kumar, Kanpur, no credit, last trip 6 months ago

⏱️ 0:05
[A]  "Namaste Ramesh bhai! Paytm Business Travel se bol raha hoon.
      Aapko kahan jaana hai?"
[WA] → Sends companion greeting card

⏱️ 0:10
[M]  "Kal Delhi jaana hai, subah ki flight chahiye."

⏱️ 0:13
[SYS] NLU extracts: dest=DEL, date=tomorrow, time=morning
[SYS] Origin inferred from KYC: Kanpur (KNU)
[SYS] Flight API call fires

[A]  "Theek hai — kal subah Kanpur se Delhi. Flights dhoond raha hoon..."

⏱️ 0:18
[SYS] API returns: 2 flights — IndiGo 6E-123 08:00 ₹3,900 / SpiceJet SG-456 09:00 ₹3,400
[WA] → Sends flight comparison card

[A]  "Do options hain. Pehla: IndiGo, subah 8 baje, ₹3,900.
      Doosra: SpiceJet, 9 baje, ₹3,400. Kaunsa theek rahega?"

⏱️ 0:30
[M]  "SpiceJet wala le lo, sasta hai."

⏱️ 0:33
[SYS] NLU: selection = SpiceJet SG-456
[SYS] Credit check: no credit account
[A]  "SpiceJet, 9 baje, ₹3,400. Paytm wallet se pay karein?
      Aapke wallet mein ₹5,200 available hai."

⏱️ 0:40
[M]  "Haan, wallet se kar do."

⏱️ 0:42
[A]  "Theek hai — SpiceJet 9 baje, ₹3,400, wallet se. Confirm karein?"

⏱️ 0:46
[M]  "Haan, confirm."

⏱️ 0:48
[SYS] Booking API call fires
[SYS] PNR generated: SG7R2K
[SYS] GST invoice generated, WhatsApp PDF sent
[WA] → Sends booking confirmation card + GST invoice PDF + e-ticket

[A]  "Booking ho gayi! PNR hai S-G-7-R-2-K.
      Invoice aur ticket WhatsApp par bhej diya hai.
      Business naam 'Ramesh General Store' — sahi hai?"

⏱️ 1:08
[M]  "Haan, sahi hai."

[A]  "Perfect. Kal subah 9 baje ki flight, SpiceJet,
      Kanpur se Delhi. PNR aur GST invoice WhatsApp par aa gaya.
      Kuch aur chahiye?"

⏱️ 1:18
[M]  "Nahi, bas. Shukriya."

[A]  "Dhanyavaad Ramesh bhai. Safe travels!
      Paytm Business Travel — kabhi bhi call kariye."

⏱️ 1:24
[SYS] Call ends. Analytics: booking_completed, gmv=3400, method=wallet, duration=84s
[SYS] Post-booking survey queued (5-min delay)
```

---

## Script 2: Priya — Happy Path + Credit (Hindi-English Mix)
**Profile:** Boutique owner, Surat. Has ₹50K Paytm credit.
**Route:** Surat → Mumbai | This Friday morning
**Duration:** 2 min 51 sec ✅

---

```
⏱️ 0:00
[SYS] Merchant dials. KYC: Priya Shah, Surat, credit ₹50K limit, ₹47K available.
[SYS] Last booking: Surat-Mumbai 3 months ago

⏱️ 0:05
[A]  "Namaste Priya ji! Paytm Business Travel se hoon.
      Kahan jaana hai aapko?"

⏱️ 0:10
[M]  "Mumbai, this Friday. Morning flight."

⏱️ 0:13
[SYS] NLU: dest=BOM, date=this Friday, time=morning, origin inferred=STV (Surat)
[SYS] API call fires

[A]  "This Friday, Surat to Mumbai, morning — searching now..."

⏱️ 0:19
[SYS] API: IndiGo 6E-441 06:30 ₹4,100 / Air India AI-809 08:15 ₹3,700
[WA] → Sends flight cards

[A]  "Do options hain. Pehla: IndiGo, 6:30 subah, ₹4,100.
      Doosra: Air India, 8:15, ₹3,700. Which one?"

⏱️ 0:31
[M]  "Air India wala. 8 baje zyada convenient hai."

⏱️ 0:34
[A]  "Air India, 8:15, ₹3,700. Paytm Business Credit se karein?
      Aapke credit mein ₹47,000 available hai."

⏱️ 0:41
[M]  "Yes, credit se karo."

⏱️ 0:43
[A]  "Air India 8:15, ₹3,700, Paytm Business Credit se.
      Confirm karein?"

⏱️ 0:48
[M]  "Confirmed."

⏱️ 0:50
[SYS] Booking API. PNR: AI4P9Q. GSTIN validated. Invoice generated.
[WA] → Booking confirmed card + Invoice PDF + E-ticket

[A]  "Done! PNR hai A-I-4-P-9-Q.
      GST invoice aur ticket WhatsApp pe bhej diya.
      Business naam 'Priya Fashion House' — correct hai?"

⏱️ 1:08
[M]  "Yes, correct."

[A]  "This Friday, Air India 8:15 AM, Surat to Mumbai.
      ₹3,700 credit se. Invoice aur ticket WhatsApp par aa gaya.
      Kuch aur chahiye?"

⏱️ 1:20
[M]  "Nahi, that's it. Thanks."

[A]  "Thank you Priya ji. Safe travels!
      Paytm Business Travel — call anytime."

⏱️ 1:27
[SYS] Call ends. Analytics: method=credit, credit_attached=true, duration=87s
```

---

## Script 3: Credit Nudge Sequence (English)
**Scenario:** Merchant has credit but was about to choose wallet.
**Teaching moment:** How the credit nudge converts passively.

---

```
⏱️ 0:45 (entering Stage 3)

[M]  "Book the IndiGo one."

[A]  "IndiGo, 8 AM, ₹4,200. You have ₹45,000 in Paytm Business Credit.
      Shall I use credit, or would you prefer to pay from wallet?"

[M]  "Oh — I have credit? Use the credit."
       [Without nudge: merchant would have said "wallet" automatically]

[A]  "Using Paytm Business Credit. ₹4,200 — confirm?"

[M]  "Yes."

✅ Credit attached. Nudge converted passive wallet choice → credit.
```

**Variant — merchant is unaware of credit limit:**
```
[M]  "Kitna credit hai mere paas?"

[A]  "Aapke paas ₹45,000 Business Credit available hai.
      Is booking ke baad ₹40,800 bachega."

[M]  "Achha! Toh credit use karo."
```

---

## Script 4: No Flights Available (Hindi)
**Scenario:** No direct flights on requested date. Agent offers alternatives.

---

```
⏱️ 0:15
[M]  "Kal Kanpur se Bangalore jaana hai."

[SYS] NLU: origin=KNU, dest=BLR, date=tomorrow
[SYS] API returns: 0 direct flights. Connecting options: 2 (via Delhi, via Mumbai)
[WA] → Sends "no direct flights" card with connecting options

[A]  "Kal Kanpur se Bangalore direct flight nahi hai.
      Do options hain — connecting flights via Delhi ya via Mumbai.
      Delhi via: IndiGo, 6:30 AM, total 5 ghante, ₹6,200.
      Mumbai via: Air India, 9:00 AM, total 4.5 ghante, ₹5,800.
      Koi chalega? Ya alag date lein?"

[M]  "Doosra din kab hai?"

[A]  "Parson — direct nahi hai, connecting hi hain.
      Actually, Delhi via wala theek hai — subah ki flight, kam stop."

[M]  "Theek hai, Delhi via le lo."

[A]  "IndiGo, Kanpur se Delhi 6:30, Delhi se Bangalore 10:00 — ₹6,200.
      Paytm credit se karein?"
      [... continues to Stage 3]
```

---

## Script 5: Mid-Call Price Change (Hindi-English)
**Scenario:** Flight price changed between search and confirmation.

---

```
⏱️ 0:45
[M]  "Air India wala book karo."

[SYS] Booking API call fires.
[SYS] Price validation: ₹3,800 → ₹4,300 (dynamic pricing spike)

[A]  "Ek second — Air India ka price abhi ₹3,800 se ₹4,300 ho gaya.
      ₹500 zyada hai. Still confirm karein, ya IndiGo dekhe — woh ₹4,200 pe stable hai?"

[M]  "Kitna fark hai?"
[A]  "₹100 ka — IndiGo ₹4,200, Air India ₹4,300."
[M]  "Toh IndiGo lo."
[A]  "IndiGo, 8 AM, ₹4,200. Confirm?"
[M]  "Haan."
[SYS] ... continues booking
```

---

## Script 6: Credit Declined — Fallback to Wallet (Hindi)
**Scenario:** Credit check fails (risk hold). Agent gracefully falls back.

---

```
⏱️ 0:50
[SYS] Credit API: DECLINED (merchant has pending payment, temporary hold)

[A]  "Ek chota issue aa gaya — credit line abhi temporary hold pe hai.
      Wallet se book kar sakte hain — aapke wallet mein ₹6,500 hai, which covers ₹4,200.
      Wallet se karein?"

[M]  "Haan, koi baat nahi. Wallet se karo."

[A]  "Wallet se ₹4,200. Confirm?"
[M]  "Haan."
[SYS] Booking via wallet. Credit issue flagged for CRM follow-up.
[A]  "Booking ho gayi..."
      [Continues normally]

[Post-call WA message sent separately:]
"Ramesh bhai, aapki credit line ke baare mein: ek pending payment hai.
Resolve karne ke liye tap karein: [Paytm Credit Support →]"
```

---

## Script 7: GSTIN Issue — Draft Save + Recovery (Hindi)
**Scenario:** GSTIN suspended. Agent saves draft for recovery.

---

```
⏱️ 1:15 (Stage 4 — invoice generation)
[SYS] GST validation API: GSTIN 09XXXXX5678 → STATUS: SUSPENDED

[A]  "Ek chhoti problem hai — aapka GST number abhi suspend status mein hai.
      Invoice baad mein generate ho sakta hai.
      Booking draft save kar deta hoon — GST update ke baad call kariye, 2 minute mein complete karenge."

[M]  "Kaise update hoga?"

[A]  "WhatsApp pe link bhej raha hoon — Paytm merchant app se ek step mein ho jayega.
      Booking draft safe hai, expire nahi hoga."

[WA] → Sends: "GST update needed to complete booking. Update here → [link] | Resume booking → [link]"

[M]  "Theek hai."
[A]  "Koi baat nahi. Update ke baad seedha WhatsApp link se resume kar lena.
      Kuch aur?"
[M]  "Nahi."
[A]  "Dhanyavaad. Safe travels Ramesh bhai!"
```

---

## Script 8: Merchant Interrupts Mid-Speech (Natural)
**Scenario:** Merchant talks over agent. Agent stops and listens.

---

```
[A]  "Do options hain. Pehla: IndiGo, 8 baje —"

[M]  "— Air India hai kya?"
       [Interrupts mid-sentence]

[SYS] Voice activity detection: merchant speaking → agent stops immediately

[A]  "Haan, Air India bhi hai — 9:30 baje, ₹3,800.
      IndiGo 8 baje tha ₹4,200. Kaunsa lein?"

[M]  "Air India lo."
[A]  "Air India, 9:30, ₹3,800. Paytm credit se?"
      [... continues normally]
```

---

## Script 9: Human Handoff Request (Hindi)
**Scenario:** Merchant wants to cancel an existing booking mid-call.

---

```
⏱️ 0:05
[M]  "Mera kal ka booking cancel karna hai."

[SYS] NLU: intent=CANCEL (not book)
[SYS] Cancellation = human handoff required

[A]  "Cancel ke liye aapko ek specialist se baat karni padegi — main connect karta hoon.
      30 second lagenge."

⏱️ 0:12
[SYS] Call transferred to human agent queue. Context packet sent: merchant profile + booking history

[Human Agent] "Hello, Paytm Travel mein aapka swagat. Main Aarti hoon.
               Aap kal ki Kanpur-Delhi booking cancel karna chahte hain?"

[M]  "Haan."

[Human Agent] "Booking ID PTM-45234 hai? Refund ₹3,200 hoga 3-5 working days mein..."
              [Handles cancellation]
```

---

## Script 10: Proactive Outbound Call — Seasonal Pattern (Hindi)
**Scenario:** Paytm calls Ramesh in February based on March travel pattern.

---

```
⏱️ 0:00
[SYS] Trigger: Ramesh traveled Delhi in March 2024 and March 2025. Feb 2026 = outreach window.
[SYS] Agent initiates outbound call

[A]  "Namaste Ramesh bhai! Main Paytm Business Travel se Pooja bol rahi hoon.
      Pichle do saalon mein aapne March mein Delhi jaate the — supplier meetings ke liye.
      Is saal bhi plan hai? Abhi book karein toh fare sasta milega."

⏱️ 0:12
[M]  "Haan yaar, soch raha tha. Abhi book karein?"

[A]  "Bilkul — kab jaana hai? March mein kaun sa week?"

[M]  "Second week mein, Monday-Tuesday."

[A]  "March 9 se 10 March — Kanpur se Delhi. Flight dhoondta hoon..."
     [Continues into Stage 2]

--- Alternative: Merchant is not interested ---

[M]  "Nahi, is saal nahi jaana."

[A]  "Koi baat nahi Ramesh bhai. Jab bhi zaroorat ho, 1800-XXX-XXXX pe call kariye.
      Paytm Business Travel — 24/7 available hai."

[SYS] Call ends. CRM: outbound_result=declined. No retry for 30 days.
```

---

## Script 11: Regional Language Fallback (Gujarati detected)
**Scenario:** Priya speaks Gujarati; agent redirects politely.

---

```
[M]  "Kal Mumbai javanu che." [Gujarati: "Need to go to Mumbai tomorrow"]

[SYS] STT detects non-Hindi/English utterance with low confidence

[A]  "Maaf kijiye — main sirf Hindi aur English samajh sakta hoon abhi.
      Hindi ya English mein bataiye — kahan jaana hai?"

[M]  "Mumbai, tomorrow morning."

[A]  "Mumbai, tomorrow morning — got it! Searching flights from Surat..."
     [Continues in English]
```

---

## Script 12: Full English Happy Path
**Scenario:** Tech-savvy merchant prefers English throughout.

---

```
⏱️ 0:00
[SYS] KYC: Arjun Bhatia, Jaipur, credit ₹35K

[A]  "Hello Arjun! This is Paytm Business Travel. Where would you like to go?"

[M]  "Delhi, this Sunday. Morning."

[A]  "Delhi this Sunday morning — searching now..."

[SYS] API: IndiGo 8 AM ₹4,500 / Vistara 9 AM ₹5,100
[WA] → Cards sent

[A]  "Two options. First: IndiGo at 8 AM, ₹4,500.
      Second: Vistara at 9 AM, ₹5,100.
      Which works for you?"

[M]  "IndiGo."

[A]  "IndiGo, 8 AM, ₹4,500. You have ₹35,000 in Paytm Business Credit —
      shall I use credit or your wallet?"

[M]  "Credit, please."

[A]  "IndiGo 8 AM, ₹4,500 via Paytm Business Credit — shall I confirm?"

[M]  "Yes, go ahead."

[SYS] Booking fires. PNR: 6E8T3R
[WA] → Confirmation + Invoice + E-ticket

[A]  "Done! PNR is 6-E-8-T-3-R. Your GST invoice and e-ticket
      are on WhatsApp now. Business name 'Bhatia Handicrafts' — correct?"

[M]  "Yes, that's right."

[A]  "You're all set — IndiGo Sunday 8 AM, Jaipur to Delhi.
      Anything else?"

[M]  "No, that's all. Thanks."

[A]  "Thank you Arjun. Safe travels! Call us anytime."

⏱️ 1:41 ✅
```

---

## Language & Tone Guidelines

### Tone Principles
| Principle | Example |
|---|---|
| **Warm, not formal** | "Koi baat nahi" > "I apologize for the inconvenience" |
| **Confident, not pushy** | "Credit use karein — ₹47K available hai" (once, not repeated) |
| **Concise, not chatty** | Never say more than 2 sentences before listening |
| **Reassuring in failures** | "Ho jayega, koi tension nahi" > "We are processing your request" |

### Forbidden Phrases
- ❌ "Your call is important to us"
- ❌ "For quality assurance, this call may be recorded" (say it once at IVR, not repeatedly)
- ❌ "I'm just an AI" (never volunteer this — answer naturally)
- ❌ "I apologize for any inconvenience" (hollow; use specific apology instead)
- ❌ "Please hold while I process your request" with > 4 sec silence

### Code-Switching Rules
```
Detect merchant's language from first utterance.
If Hindi → respond in Hindi, accept English interjections naturally.
If English → respond in English, accept Hindi interjections naturally.
If mixed → follow merchant's dominant language.
Never switch languages mid-sentence (confusing).
City names: use local pronunciation (Bambai/Mumbai both valid, match merchant's usage).
```

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/02-solution-design*
