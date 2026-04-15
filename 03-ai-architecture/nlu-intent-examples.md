# NLU Intent Examples
**Training Data Samples · Entity Extraction · Edge Cases**
*Paytm Merchant Travel Desk | 03-ai-architecture*

---

## Training Data Overview

```
Total training examples:    1,200 (initial training set)
  Synthetic (generated):    1,000
  Real (pilot transcripts):   200  [added after pilot Week 2]

Languages:
  Pure Hindi:               380 examples (32%)
  Pure English:             240 examples (20%)
  Hindi-English mixed:      480 examples (40%)
  With regional variants:    100 examples (8%)

Data format: JSONL
Labeling: Intent (single label) + Entities (span labels)
Validation split: 80/20 train/test
```

---

## Format Reference

```json
{
  "utterance": "raw text from STT",
  "language": "hi" | "en" | "mixed",
  "intent": "INTENT_LABEL",
  "entities": [
    {
      "text": "matched text",
      "label": "ENTITY_TYPE",
      "value": "normalized value",
      "confidence_note": "optional — for ambiguous cases"
    }
  ],
  "notes": "edge case or training annotation"
}
```

---

## Intent: BOOK_FLIGHT

### Pure Hindi
```json
{"utterance": "Delhi jaana hai", "language": "hi", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Delhi", "label": "DESTINATION", "value": "DEL"}]}

{"utterance": "Mumbai ki flight book karo", "language": "hi", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Mumbai", "label": "DESTINATION", "value": "BOM"},
              {"text": "flight book karo", "label": "BOOKING_ACTION", "value": "true"}]}

{"utterance": "kal Bangalore jaana hai subah", "language": "hi", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "kal", "label": "DATE", "value": "tomorrow"},
              {"text": "Bangalore", "label": "DESTINATION", "value": "BLR"},
              {"text": "subah", "label": "TIME_BAND", "value": "morning"}]}

{"utterance": "Kanpur se Delhi kal ka ticket chahiye", "language": "hi", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Kanpur", "label": "ORIGIN", "value": "KNU"},
              {"text": "Delhi", "label": "DESTINATION", "value": "DEL"},
              {"text": "kal", "label": "DATE", "value": "tomorrow"}]}

{"utterance": "Dilli jaana hai parson shaam ko", "language": "hi", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Dilli", "label": "DESTINATION", "value": "DEL",
               "notes": "phonetic variant — Dilli = Delhi"},
              {"text": "parson", "label": "DATE", "value": "day_after_tomorrow"},
              {"text": "shaam ko", "label": "TIME_BAND", "value": "evening"}]}

{"utterance": "Bambai flight dhoondo kal ke liye", "language": "hi", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Bambai", "label": "DESTINATION", "value": "BOM",
               "notes": "regional name — Bambai = Mumbai"},
              {"text": "kal", "label": "DATE", "value": "tomorrow"}]}

{"utterance": "agle hafte Chennai jaana hai", "language": "hi", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "agle hafte", "label": "DATE", "value": "next_week"},
              {"text": "Chennai", "label": "DESTINATION", "value": "MAA"}]}

{"utterance": "15 tarikh ko Pune", "language": "hi", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "15 tarikh ko", "label": "DATE", "value": "2026-04-15",
               "confidence_note": "year inferred from current month context"},
              {"text": "Pune", "label": "DESTINATION", "value": "PNQ"}]}
```

### Pure English
```json
{"utterance": "I need to fly to Delhi tomorrow", "language": "en", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Delhi", "label": "DESTINATION", "value": "DEL"},
              {"text": "tomorrow", "label": "DATE", "value": "tomorrow"}]}

{"utterance": "Book a flight to Mumbai this Friday morning", "language": "en", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Mumbai", "label": "DESTINATION", "value": "BOM"},
              {"text": "this Friday", "label": "DATE", "value": "2026-04-17"},
              {"text": "morning", "label": "TIME_BAND", "value": "morning"}]}

{"utterance": "I want to go to Bangalore next Tuesday", "language": "en", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Bangalore", "label": "DESTINATION", "value": "BLR"},
              {"text": "next Tuesday", "label": "DATE", "value": "2026-04-21"}]}

{"utterance": "Flight from Surat to Delhi on Wednesday", "language": "en", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Surat", "label": "ORIGIN", "value": "STV"},
              {"text": "Delhi", "label": "DESTINATION", "value": "DEL"},
              {"text": "Wednesday", "label": "DATE", "value": "2026-04-15"}]}

{"utterance": "Can you find me a cheap flight to Hyderabad?", "language": "en", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Hyderabad", "label": "DESTINATION", "value": "HYD"},
              {"text": "cheap", "label": "PRICE_PREFERENCE", "value": "lowest_fare"}]}
```

### Hindi-English Mixed (Hinglish)
```json
{"utterance": "Delhi flight kal book karo morning wali", "language": "mixed", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Delhi", "label": "DESTINATION", "value": "DEL"},
              {"text": "kal", "label": "DATE", "value": "tomorrow"},
              {"text": "morning wali", "label": "TIME_BAND", "value": "morning"}]}

{"utterance": "Mumbai ke liye flight search karo Sunday ko", "language": "mixed", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Mumbai", "label": "DESTINATION", "value": "BOM"},
              {"text": "Sunday ko", "label": "DATE", "value": "2026-04-19"}]}

{"utterance": "Next week Bangalore jaana hai for supplier meeting", "language": "mixed", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "next week", "label": "DATE", "value": "next_week"},
              {"text": "Bangalore", "label": "DESTINATION", "value": "BLR"},
              {"text": "supplier meeting", "label": "TRIP_PURPOSE", "value": "business"}]}

{"utterance": "Kal ki flight chahiye Delhi-Mumbai, early morning", "language": "mixed", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Kal", "label": "DATE", "value": "tomorrow"},
              {"text": "Delhi", "label": "ORIGIN", "value": "DEL"},
              {"text": "Mumbai", "label": "DESTINATION", "value": "BOM"},
              {"text": "early morning", "label": "TIME_BAND", "value": "early_morning"}]}

{"utterance": "Book karo na, Kolkata next Monday", "language": "mixed", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Kolkata", "label": "DESTINATION", "value": "CCU"},
              {"text": "next Monday", "label": "DATE", "value": "2026-04-20"}]}
```

---

## Intent: SELECT_OPTION_1 / SELECT_OPTION_2

```json
{"utterance": "pehla wala", "language": "hi", "intent": "SELECT_OPTION_1", "entities": []}
{"utterance": "first one", "language": "en", "intent": "SELECT_OPTION_1", "entities": []}
{"utterance": "IndiGo wala", "language": "mixed", "intent": "SELECT_OPTION_1",
 "entities": [{"text": "IndiGo", "label": "AIRLINE", "value": "6E"}],
 "notes": "Airline name disambiguates option when user remembers brand"}

{"utterance": "doosra", "language": "hi", "intent": "SELECT_OPTION_2", "entities": []}
{"utterance": "second option", "language": "en", "intent": "SELECT_OPTION_2", "entities": []}
{"utterance": "Air India wala theek hai", "language": "mixed", "intent": "SELECT_OPTION_2",
 "entities": [{"text": "Air India", "label": "AIRLINE", "value": "AI"}]}

{"utterance": "jo sasta ho", "language": "hi", "intent": "SELECT_OPTION_2",
 "notes": "If option 2 is cheaper (common ranking), 'sasta wala' = SELECT_OPTION_2. Validate against current results."}

{"utterance": "8 baje wali", "language": "hi", "intent": "SELECT_OPTION_1",
 "entities": [{"text": "8 baje", "label": "DEPARTURE_TIME", "value": "08:00"}],
 "notes": "Time reference disambiguates — match to option with 08:00 departure"}

{"utterance": "the cheaper one please", "language": "en", "intent": "SELECT_OPTION_2",
 "notes": "Requires flight results context — resolve to lower-priced option"}
```

---

## Intent: PAYMENT_CREDIT / PAYMENT_WALLET

```json
{"utterance": "credit se", "language": "hi", "intent": "PAYMENT_CREDIT", "entities": []}
{"utterance": "business credit", "language": "en", "intent": "PAYMENT_CREDIT", "entities": []}
{"utterance": "Paytm credit use karo", "language": "mixed", "intent": "PAYMENT_CREDIT", "entities": []}
{"utterance": "credit card se nahi, Paytm credit se", "language": "mixed", "intent": "PAYMENT_CREDIT",
 "notes": "Negative contrast — clarifying they want Paytm credit NOT external credit card"}

{"utterance": "wallet se", "language": "hi", "intent": "PAYMENT_WALLET", "entities": []}
{"utterance": "from wallet", "language": "en", "intent": "PAYMENT_WALLET", "entities": []}
{"utterance": "Paytm wallet use karo", "language": "mixed", "intent": "PAYMENT_WALLET", "entities": []}
{"utterance": "cash se hoga kya", "language": "hi", "intent": "PAYMENT_WALLET",
 "notes": "Cash = wallet in merchant mental model. Respond: 'Paytm wallet se pay ho jayega'"}
```

---

## Intent: CONFIRM

```json
{"utterance": "haan", "language": "hi", "intent": "CONFIRM", "entities": []}
{"utterance": "yes", "language": "en", "intent": "CONFIRM", "entities": []}
{"utterance": "theek hai", "language": "hi", "intent": "CONFIRM", "entities": []}
{"utterance": "kar do", "language": "hi", "intent": "CONFIRM", "entities": []}
{"utterance": "book karo", "language": "hi", "intent": "CONFIRM", "entities": []}
{"utterance": "confirmed", "language": "en", "intent": "CONFIRM", "entities": []}
{"utterance": "ha bilkul", "language": "hi", "intent": "CONFIRM", "entities": []}
{"utterance": "done karo", "language": "mixed", "intent": "CONFIRM", "entities": []}
{"utterance": "aage badho", "language": "hi", "intent": "CONFIRM",
 "notes": "Literally 'move forward' — idiomatic confirmation"}
{"utterance": "go ahead", "language": "en", "intent": "CONFIRM", "entities": []}
{"utterance": "chalega", "language": "hi", "intent": "CONFIRM",
 "notes": "'Will do / okay' — common informal confirmation"}
```

---

## Intent: CANCEL_INTENT (Abandon This Booking)

```json
{"utterance": "rehne do", "language": "hi", "intent": "CANCEL_INTENT",
 "notes": "Abandon current flow, not cancel existing booking"}
{"utterance": "nahi chahiye", "language": "hi", "intent": "CANCEL_INTENT", "entities": []}
{"utterance": "baad mein karunga", "language": "hi", "intent": "CANCEL_INTENT",
 "notes": "Save session state — merchant may call back"}
{"utterance": "cancel this", "language": "en", "intent": "CANCEL_INTENT", "entities": []}
{"utterance": "not now, I'll call back", "language": "en", "intent": "CANCEL_INTENT", "entities": []}
{"utterance": "choddo", "language": "hi", "intent": "CANCEL_INTENT",
 "notes": "Regional variant of 'chodo' — let it be"}
```

---

## Intent: EXISTING_BOOKING (Cancel/Change/Refund)

```json
{"utterance": "mera booking cancel karna hai", "language": "hi", "intent": "EXISTING_BOOKING",
 "entities": [{"text": "cancel", "label": "BOOKING_ACTION", "value": "cancel"}]}

{"utterance": "pichla ticket cancel karo", "language": "hi", "intent": "EXISTING_BOOKING",
 "entities": [{"text": "pichla ticket", "label": "BOOKING_REFERENCE", "value": "previous"},
              {"text": "cancel", "label": "BOOKING_ACTION", "value": "cancel"}]}

{"utterance": "refund chahiye", "language": "hi", "intent": "EXISTING_BOOKING",
 "entities": [{"text": "refund", "label": "BOOKING_ACTION", "value": "refund"}]}

{"utterance": "meri flight change ho gayi, naya book karna hai", "language": "hi",
 "intent": "EXISTING_BOOKING",
 "entities": [{"text": "flight change ho gayi", "label": "BOOKING_ACTION", "value": "reschedule"}],
 "notes": "Airline changed schedule — rebook, not cancel"}

{"utterance": "I want to change my name on the ticket", "language": "en",
 "intent": "EXISTING_BOOKING",
 "entities": [{"text": "change my name", "label": "BOOKING_ACTION", "value": "name_change"}]}
```

---

## Intent: REPEAT_OPTIONS

```json
{"utterance": "phir se batao", "language": "hi", "intent": "REPEAT_OPTIONS", "entities": []}
{"utterance": "again please", "language": "en", "intent": "REPEAT_OPTIONS", "entities": []}
{"utterance": "kya bola?", "language": "hi", "intent": "REPEAT_OPTIONS", "entities": []}
{"utterance": "samjha nahi", "language": "hi", "intent": "REPEAT_OPTIONS", "entities": []}
{"utterance": "price kya tha dono ka?", "language": "hi", "intent": "CLARIFY_PRICE",
 "notes": "CLARIFY_PRICE variant of REPEAT_OPTIONS — repeat prices only"}
{"utterance": "pehla flight kitne baje ka tha?", "language": "hi", "intent": "CLARIFY_DETAILS",
 "notes": "Partial repeat — repeat specific detail only"}
```

---

## Edge Cases & Hard Negatives

### Ambiguous Utterances Requiring Context

```json
{"utterance": "kal ka", "language": "hi", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "kal ka", "label": "DATE", "value": "tomorrow"}],
 "notes": "Destination missing — triggers slot-fill: 'Kahan ke liye?'"}

{"utterance": "same as last time", "language": "en", "intent": "BOOK_FLIGHT",
 "entities": [],
 "notes": "Requires booking_history lookup — extract last route + suggest same. Confidence: 0.70"}

{"utterance": "Delhi", "language": "en", "intent": "BOOK_FLIGHT",
 "entities": [{"text": "Delhi", "label": "DESTINATION", "value": "DEL"}],
 "notes": "Single word utterance — date missing. Slot-fill: 'Kab jaana hai?'"}

{"utterance": "kal nahi, parson", "language": "hi", "intent": "DATE_CORRECTION",
 "entities": [{"text": "parson", "label": "DATE", "value": "day_after_tomorrow"}],
 "notes": "Correction mid-slot-fill — override previously extracted date"}
```

### Hard Negatives (Must NOT Classify as BOOK_FLIGHT)

```json
{"utterance": "train hai kya Kanpur se Delhi?", "language": "hi", "intent": "BOOK_TRAIN",
 "notes": "Train request — not supported in MVP. Decline gracefully."}

{"utterance": "mera pichla booking kahan hai?", "language": "hi", "intent": "BOOKING_STATUS",
 "notes": "Status inquiry — not a new booking"}

{"utterance": "cancel karna hai kal ki flight", "language": "hi", "intent": "EXISTING_BOOKING",
 "notes": "Cancel, not new booking — even though 'kal ki flight' sounds like search"}

{"utterance": "hotel Delhi mein chahiye", "language": "hi", "intent": "BOOK_HOTEL",
 "notes": "Hotel — post-MVP. Decline: 'Hotels abhi available nahi hain. Flight book karein?'"}

{"utterance": "paisa nahi tha isliye nahi gaya tha", "language": "hi", "intent": "UNCLEAR",
 "notes": "Background conversation — not a booking intent"}
```

---

## Entity Normalization Reference

### City Name → IATA Code

| Heard (STT output) | Canonical | IATA | Notes |
|---|---|---|---|
| Delhi, Dilli, New Delhi, NCR | Delhi | DEL | |
| Mumbai, Bombay, Bambai, Bombai | Mumbai | BOM | |
| Bangalore, Bengaluru, Banglore | Bangalore | BLR | Misspelling common |
| Chennai, Madras | Chennai | MAA | |
| Kolkata, Calcutta | Kolkata | CCU | |
| Hyderabad, Hyd | Hyderabad | HYD | |
| Pune | Pune | PNQ | |
| Ahmedabad, Amdavad | Ahmedabad | AMD | Gujarati variant |
| Jaipur | Jaipur | JAI | |
| Surat | Surat | STV | |
| Kanpur | Kanpur | KNU | |
| Lucknow | Lucknow | LKO | |
| Bhopal | Bhopal | BHO | |
| Nagpur | Nagpur | NAG | |
| Coimbatore | Coimbatore | CJB | |
| Kochi, Cochin | Kochi | COK | |
| Goa | Goa | GOI | |

### Relative Date Resolution (examples at call time: April 15, 2026, Tuesday)

| Heard | Resolved Date | Notes |
|---|---|---|
| "kal" | 2026-04-16 | Tomorrow |
| "parson" | 2026-04-17 | Day after tomorrow |
| "aaj" | 2026-04-15 | Today — check if any flights remain |
| "is hafte" | 2026-04-15 → 19 | This week (nearest available) |
| "agle hafte" | 2026-04-20 → 26 | Next week |
| "agla Monday" | 2026-04-20 | Next Monday |
| "is hafte ka Monday" | 2026-04-20 | Disambiguate: already past Monday |
| "15 ko" | 2026-04-15 | Current month assumed |
| "20 ko" | 2026-04-20 | Current month |
| "1 May" | 2026-05-01 | Explicit month |
| "mahine ke end mein" | 2026-04-29 | End of current month |

### Time Band Resolution

| Heard | Time Range | Booking Preference |
|---|---|---|
| "subah" | 06:00–10:00 | Prefer 07:00–09:30 |
| "dopahar" | 12:00–16:00 | Prefer 12:30–14:00 |
| "shaam" | 17:00–21:00 | Prefer 17:30–19:00 |
| "raat" | 20:00–23:59 | Last flight preference |
| "jaldi" / "pehli flight" | 05:00–08:00 | Earliest departure |
| "late evening" | 19:00–22:00 | Prefer 19:30–21:00 |
| "morning" | 06:00–11:00 | Same as "subah" |
| None given | — | Sort by price-optimized default |

---

## Model Performance Targets

| Metric | Target | Measurement |
|---|---|---|
| Intent classification accuracy | > 92% | Test set eval |
| Entity extraction F1 | > 88% | NER benchmark |
| Relative date resolution | > 95% | Date-specific test set |
| Phonetic variant mapping | > 90% | City alias test set |
| Hinglish handling | > 85% | Mixed-language test set |
| False positive: BOOK_FLIGHT when CANCEL | < 1% | Critical error rate |

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/03-ai-architecture*
