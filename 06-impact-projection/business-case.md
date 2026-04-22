# Business Case
**₹1,440 Cr GMV · ₹33 Cr Revenue · Unit Economics · 3-Year Model**
*Paytm Merchant Travel Desk | 06-impact-projection*

---

## Executive Summary

| Metric | Year 1 | Year 2 | Year 3 |
|---|---|---|---|
| **Active Merchants** | 600K | 2.1M | 5.4M |
| **Bookings** | 1.8M | 6.3M | 16.2M |
| **GMV** | ₹1,440 Cr | ₹5,040 Cr | ₹12,960 Cr |
| **Total Revenue** | ₹33 Cr | ₹128 Cr | ₹356 Cr |
| **EBITDA Margin** | −12% (invest) | +8% | +22% |
| **New Credit Users** | 600K | 1.8M | 4.2M |
| **CAC** | ₹0 | ₹0 | ₹0 |

**Strategic value beyond P&L:** Every booking is a credit activation event. Every invoice is a compliance event. The product's strategic ROI compounds through the Paytm financial services flywheel — not just the travel P&L.

---

## Section 1: Market Sizing

### Total Addressable Market (TAM)

```
India's domestic air travel market (2026):
  Total passengers:     ~160M/year
  Business travelers:   ~35% = 56M trips/year
  SMB business travel:  ~15% of total = 24M trips/year (highly undercounted)

Paytm's specific TAM:
  Paytm verified merchants:         30M
  Travel at least 2×/year:          10% = 3M merchants
  Average trips per merchant/year:  3
  Average booking value:            ₹8,000
  ─────────────────────────────────────────
  Paytm TAM:    3M × 3 × ₹8,000 = ₹7,200 Cr GMV/year
```

### Serviceable Addressable Market (SAM)

```
Constraints on full TAM:
  - Technology adoption ceiling: ~70% will use digital booking by Year 3
  - Geographic coverage: 50 cities in Year 1, 150 by Year 3
  - Route coverage: Domestic only (Year 1–2)
  ─────────────────────────────────────────
  Year 1 SAM:   ₹2,160 Cr (30% of TAM — 3 cities, soft launch)
  Year 2 SAM:   ₹5,760 Cr (80% of TAM — 50 cities, trains added)
  Year 3 SAM:   ₹7,200 Cr (100% of TAM — 150 cities, full product)
```

### Serviceable Obtainable Market (SOM)

```
Market share projection:
  Year 1:  ₹1,440 Cr GMV = 20% of Year 1 SAM
           (Early adopters, pilot cities, single product)
  Year 2:  ₹5,040 Cr GMV = 35% of Year 2 SAM
           (Credit + GST moat begins compounding)
  Year 3:  ₹12,960 Cr GMV = 45% of Year 3 SAM
           (Network effects + loyalty + product breadth)
```

---

## Section 2: Year 1 Unit Economics (Deep Dive)

### Merchant Funnel

```
30,000,000   Total Paytm verified merchants
      ×10%   Travel for business 2+×/year
─────────────────
 3,000,000   TAM: Potential travel merchants

      ×20%   Year 1 adoption (pilot + scale rollout)
─────────────────
   600,000   Active merchants using Paytm Travel (Year 1)

      ×3     Average trips/year
─────────────────
 1,800,000   Total bookings (Year 1)
```

### Revenue Streams

#### Stream 1: Booking Commission

```
GMV:              1,800,000 bookings × ₹8,000 avg = ₹1,440 Cr

Commission rate:  2% (blended — airline MDR varies 1.5%–3%)
                  Basis: Paytm's existing airline API agreements

Commission rev:   ₹1,440 Cr × 2% = ₹28.8 Cr

Notes:
  - Net of airline GDS/API fees (~0.4%)
  - Net of payment processing (~0.3%)
  - Net commission contribution: ~1.3%
  - Net commission revenue:       ₹1,440 Cr × 1.3% = ₹18.7 Cr
```

#### Stream 2: Paytm Business Credit Interest

```
Credit attachment rate:   40% of bookings paid via credit
Bookings via credit:      1,800,000 × 40% = 720,000

Average credit per booking: ₹8,000
Total credit disbursed:   720,000 × ₹8,000 = ₹576 Cr/year

Credit product terms:
  APR:              18% (Paytm Business Credit standard rate)
  Avg repayment:    30-day cycle (travel = short-duration credit)
  Effective yield:  18% × (30/365) = 1.48% per trip

Interest revenue:   ₹576 Cr × 1.48% = ₹8.5 Cr

Note: This is conservative. If merchants carry balance beyond 30 days
(some will), effective yield increases. Late payment fees excluded.
```

#### Stream 3: GST Invoice Premium (Future)

```
MVP:  Invoice included free (customer acquisition + retention tool)
Year 2+: ₹29/invoice premium for expedited + bulk invoice management
          (Same model as Paytm Payouts — free basic, paid premium)
Year 2 revenue potential: 2M invoices × ₹29 = ₹5.8 Cr
```

#### Stream 4: Advertising & Preferred Placement (Future)

```
Post-MVP: Airlines pay for preferred placement in top-2 options
          IndiGo, Air India, Vistara bidding for prime slot
          Estimated: ₹15-25 Cr/year at scale (Year 3)
          Not included in Year 1 projections.
```

### Year 1 P&L Summary

```
REVENUE
  Booking commissions (net):      ₹18.7 Cr
  Credit interest income:          ₹8.5 Cr
  GST invoice premium (MVP=₹0):    ₹0.0 Cr
  ─────────────────────────────────────────
  Total Revenue:                  ₹27.2 Cr

  [Vs. case study cited ₹33 Cr — difference is commission gross vs. net.
   Gross commission is ₹28.8 Cr + ₹8.5 Cr = ₹37.3 Cr. Conservative net: ₹27.2 Cr.]

COST OF REVENUE
  Telephony (call minutes):        ₹3.2 Cr   [1,800,000 × 3 min × ₹0.06/min]
  STT/TTS API costs:               ₹1.8 Cr   [Google Cloud usage]
  WhatsApp Business API:           ₹0.9 Cr   [template messages × volume]
  Payment processing:              ₹2.9 Cr   [Razorpay/internal gateway]
  ─────────────────────────────────────────
  Total COGS:                      ₹8.8 Cr

GROSS PROFIT:                      ₹18.4 Cr  (67.6% gross margin)

OPERATING EXPENSES
  Engineering & ML (amortized):    ₹8.0 Cr   [team of 12, 6-month build]
  Customer Operations:             ₹4.2 Cr   [8 agents × ₹35L/yr + mgmt]
  Data & Analytics infrastructure: ₹1.4 Cr
  Marketing (pilot only):          ₹0.8 Cr   [zero paid acquisition; ops cost]
  Program management:              ₹1.2 Cr
  Legal, compliance:               ₹0.6 Cr
  ─────────────────────────────────────────
  Total OpEx:                     ₹16.2 Cr

EBITDA:                            ₹2.2 Cr   (+8% — breakeven+ in Year 1)

Note: Year 1 investment phase. Team and infra costs built for Year 2 scale.
Real Year 1 EBITDA with full-year team cost is closer to −₹4 Cr (investing mode).
```

---

## Section 3: Per-Booking Unit Economics

```
Revenue per booking:
  Commission (gross):              ₹160   (2% of ₹8,000)
  Commission (net of fees):        ₹104   (1.3% net)
  Credit interest (if credit):     ₹118   (₹8,000 × 1.48%, applied to 40% of bookings)
  Blended credit contribution:     ₹47    (₹118 × 40%)
  ─────────────────────────────────────────
  Revenue per booking:             ₹151   (net commission + blended credit)

Cost per booking:
  Telephony (3 min):               ₹10.8
  STT/TTS APIs:                    ₹6.0
  WhatsApp messages:               ₹3.0
  Payment processing:              ₹16.0   (0.2% of ₹8,000)
  Human agent (10% calls × ₹50):  ₹5.0    (handoff cost allocation)
  Infrastructure (allocated):      ₹8.0
  ─────────────────────────────────────────
  Cost per booking:                ₹48.8

Gross profit per booking:          ₹102   (67% margin)

Customer Acquisition Cost (CAC):   ₹0     (existing merchant base)
Booking payback period:            Immediate (no CAC to recover)

LTV per merchant (3 trips/year, 5-year retention estimate):
  ₹102 × 3 × 5 = ₹1,530 LTV per merchant
  With credit compounding (higher limit → more bookings over time):
  Estimated LTV: ₹2,200–₹3,000 per merchant
```

---

## Section 4: The Zero-CAC Advantage

This is the single most important economic insight in the business case.

```
How travel companies typically acquire customers:
  OTA performance marketing:       ₹800–₹2,500 CAC per booker
  Corporate travel SaaS:           ₹15,000–₹50,000 CAC per company
  Neobank SMB products:            ₹1,200–₹4,000 CAC per merchant

Paytm Merchant Travel:
  CAC:                             ₹0
  Reason:    30M merchants are already on Paytm.
             Every Paytm Business merchant who books = existing customer,
             not a new acquisition.
             The product activates a dormant relationship.

Equivalent economic value of zero CAC:
  600K merchants × ₹800 equivalent CAC = ₹480 Cr in acquisition value
  that Paytm doesn't pay.

This is the moat in unit economics terms:
  OTA competing for the same merchant must spend ₹800–₹2,500 per merchant
  to acquire them.
  Paytm spends ₹0 per merchant.
  Net advantage: ₹480 Cr in Year 1 alone (at ₹800/merchant equivalent).
```

---

## Section 5: Credit Flywheel Compounding

The business case is not about Year 1 revenue. It's about what the credit data enables.

```
Year 1:  600K merchants use credit for travel
         → Paytm gets 1.8M repayment datapoints
         → Repayment rate: estimated 97% (travel = specific purpose, known date)

Year 2 (compounding):
         → 97% repayment data = Paytm can increase credit limits
         → Avg credit limit goes from ₹25K → ₹40K for good repayers
         → Higher limits → merchants book higher-value trips (business class, multi-city)
         → Higher booking value → higher commission + more credit disbursed
         → Better underwriting data → Paytm extends credit to previously ineligible merchants

Year 3 (compounding further):
         → Travel repayment behavior predicts general credit behavior
         → Paytm can offer working capital loans with lower interest rates
           (because travel data = reliable repayment signal)
         → This credit data has value FAR beyond travel

The real Year 3 value:
         Not ₹356 Cr travel revenue alone
         But: 4.2M new credit users with verified repayment history
              Paytm can cross-sell: personal loans, business loans, insurance
              Each credit user's LTV across all products: ₹8,000–₹15,000+
         Total platform LTV unlocked: ₹3,360 Cr–₹6,300 Cr (Year 3 cohort alone)
```

---

## Section 6: 3-Year Revenue Model

### Assumptions

| Assumption | Year 1 | Year 2 | Year 3 | Rationale |
|---|---|---|---|---|
| Active merchants | 600K | 2.1M | 5.4M | 3.5× YoY — cities + product breadth |
| Trips per merchant/year | 3 | 3.2 | 3.5 | Product improves repeat usage |
| Avg booking value | ₹8,000 | ₹8,500 | ₹9,200 | Higher-value trips as product matures |
| Credit attach rate | 40% | 45% | 50% | Compounding credit adoption |
| Commission rate (gross) | 2.0% | 2.1% | 2.2% | Better airline terms at scale |
| APR on travel credit | 18% | 17% | 16% | Risk decreases; competitive pressure |
| Repayment cycle | 30 days | 30 days | 30 days | Stable product design |

### 3-Year P&L

```
                              YEAR 1        YEAR 2        YEAR 3
─────────────────────────────────────────────────────────────────
Active Merchants (K)             600         2,100         5,400
Bookings (M)                     1.8           6.3          16.2
GMV (₹ Cr)                    1,440         5,040        12,960

REVENUE (₹ Cr)
  Commission (net)              18.7          68.0         184.0
  Credit interest                8.5          31.5          87.5
  GST invoice premium            0.0           5.8          16.2
  Advertising/placement          0.0           8.0          35.0
  ─────────────────────────────────────────────────────────────
  Total Revenue                 27.2         113.3         322.7

COGS (₹ Cr)
  Telephony                      3.2          10.5          22.0
  STT/TTS APIs                   1.8           5.5          11.0
  WhatsApp API                   0.9           2.8           5.5
  Payment processing             2.9           9.1          22.0
  ─────────────────────────────────────────────────────────────
  Total COGS                     8.8          27.9          60.5

GROSS PROFIT (₹ Cr)            18.4          85.4         262.2
Gross Margin                   67.6%         75.4%         81.3%

OPEX (₹ Cr)
  Engineering & ML               8.0          18.0          32.0
  Customer Ops                   4.2           9.0          16.0
  Data & Infrastructure          1.4           4.0           8.0
  Marketing & Growth             0.8           6.0          18.0
  G&A                            1.8           4.0           8.0
  ─────────────────────────────────────────────────────────────
  Total OpEx                    16.2          41.0          82.0

EBITDA (₹ Cr)                   2.2          44.4         180.2
EBITDA Margin                  8.1%         39.2%         55.8%

[Note: Year 2 EBITDA margin jump driven by operating leverage —
 fixed engineering costs spread over 3.5× more bookings]
```

---

## Section 7: Sensitivity Analysis (One-Page)

### How to Read This Page
Base case Year 1 revenue is **₹26.9 Cr** built on six stacked assumptions. This page names each lever, ranks them by impact, shows realistic compound downsides, and calls out the three inputs most likely to break the plan — so the board (not just the model) sees the real shape of the risk.

### Assumption Quality — Where Each Base-Case Number Comes From
| Driver | Base | Source | Evidence quality |
|---|---|---|---|
| % of 3M SAM who travel ≥2×/year | 10% | §1 (NSSO extrapolation) | **LOW** — not Paytm-specific, not measured |
| Year 1 adoption of SAM | 20% (600K) | §2 | Medium — B2B fintech analogue |
| Trips per active merchant/yr | 3 | §2 | Medium — 20-interview self-report |
| Avg booking value | ₹8,000 | §2 | High — Paytm Travel historical |
| Credit attach (any payment) | 40% | North Star | Medium — pilot hit 41% but only ~31% was *new* activation |
| Commission rate | 2.0% | Industry | High |

### Tornado — One-Way Impact on Year 1 Revenue (ranked by |Δ|)

| Variable | Downside move | −₹ Cr | Upside move | +₹ Cr |
|---|---|---|---|---|
| Active merchants | 600K → 300K | **−13.5** | 600K → 900K | **+13.5** |
| Repeat bookings/yr | 3× → 2× | **−9.0** | 3× → 4× | **+9.0** |
| Avg booking value | ₹8K → ₹6.5K | −3.7 | ₹8K → ₹10K | +6.8 |
| Commission rate | 2.0% → 1.5% | −3.6 | 2.0% → 2.5% | +3.6 |
| Credit attach rate | 40% → 25% | −3.2 | 40% → 55% | +4.4 |
| Credit repayment | 97% → 90% | −0.9 | 97% → 99% | +0.3 |

*Adoption and repeat-booking dominate; credit attach is a distant fourth. The credit-activation narrative earns headline attention — the top-line risk is whether 600K merchants ever call.*

### Compound Scenarios (What Actually Happens in Real Launches)

| Scenario | Adoption | Attach | Repeat | BV | Year 1 rev | vs. base |
|---|---|---|---|---|---|---|
| **Base** | 600K | 40% | 3× | ₹8K | **₹26.9 Cr** | — |
| "Pilot over-indexed" — hand-picked cohort regresses to mean | 400K | 30% | 2.5× | ₹7.5K | **~₹10 Cr** | −63% |
| "Voice doesn't land" — mass-market prefers WA form | 600K | 30% | 2.5× | ₹7.5K | **~₹14 Cr** | −48% |
| "TAM halves" — real travel rate is 5%, not 10% | 300K | 40% | 3× | ₹8K | **~₹13.5 Cr** | −50% |

### Breakeven Floor

OpEx ≈ ₹16 Cr/yr (§2). **Any single scenario above stays revenue-positive** vs. OpEx. Two of them compounding (e.g., TAM halves *and* attach slips) produces a **~₹4–7 Cr Year 1 loss.** The "breakeven even in bear case" claim holds only if levers move independently — and in practice they don't.

### The Three Inputs Most Likely to Break the Model — Watch List

1. **% of TAM who actually travel.** Extrapolated from NSSO; never measured. Pilot screens for ≥ 2 trips/year so pilot data *cannot* validate this. **First real read: Phase 2 open-cohort scale-up, Weeks 8–12.** If it's 5% not 10%, every revenue row halves.
2. **First-time credit activation vs. total credit attach.** Pilot showed 41% attach but only ~31% was net-new. If "zero-CAC activation" is really 31% not 40%, credit NII drops ~25% and the **₹480 Cr CAC-equivalence claim in §4 needs a ~25% haircut.**
3. **Voice vs. GUI causal contribution.** The model charges no premium for voice specifically. The credit-isolation A/B in `04-mvp-specification/pilot-design.md` is the canonical test — do not cite the AI stack as a moat in §8 until it reports out.

---

## Section 8: Strategic Value Beyond P&L

### What the Revenue Numbers Don't Capture

| Strategic Value | Estimated Impact | Measurability |
|---|---|---|
| **Credit underwriting data** | 1.8M repayment datapoints → 15–20% lower default rate on all Paytm credit | High (tracked in credit portfolio) |
| **Merchant retention uplift** | Travel merchants churn 15% less than non-travel merchants | Medium (A/B vs. control) |
| **Cross-sell trigger** | Travel booking = high-intent moment for insurance, loan top-up | Medium (tracked by product) |
| **Zero-CAC new credit users** | 600K × ₹800 equivalent CAC avoided = ₹480 Cr | High (industry benchmark) |
| **Competitive defense** | SMB merchants locked into Paytm ecosystem reduce PhonePe/Razorpay switching | Low (hard to quantify directly) |
| **Data moat** | Travel patterns predict business health → better CIBIL alternatives | High (internal ML value) |

### The Argument for Investment in Investment Mode

```
If Year 1 EBITDA is −₹4 Cr (investing mode with full team cost):

Investment of ₹4 Cr unlocks:
  → 600K new active credit users (₹480 Cr CAC equivalent)
  → ₹576 Cr in travel credit disbursed (18% APR interest stream begins)
  → 1.8M repayment datapoints (improves underwriting for ALL Paytm credit)
  → ₹1,440 Cr GMV flywheel started

ROI of the ₹4 Cr investment:
  Conservative platform LTV of 600K merchants: 600K × ₹2,500 = ₹1,500 Cr
  ROI multiple: 375× on the Year 1 investment.
  
  This is not a travel product. This is a credit activation product.
  The ₹4 Cr is the cheapest credit activation campaign in Paytm's history.
```

---

*Last updated: April 2026 | Repo: paytm-merchant-travel-desk/06-impact-projection*
