# CA Invoice Approval Packet
**3-CA Review · Feedback · Final Sign-Off**
*Paytm Merchant Travel Desk | 05-launch-plan | Finance-Owned*

---

## Purpose

D07 is a go-live blocker: minimum 3 Chartered Accountants must review and approve the GST invoice format before Week 1 soft launch. This document captures the packet sent, the CAs who reviewed, their feedback, and the final sign-off.

Once approved, this invoice format is locked for the 500-merchant pilot. Any change requires re-review.

---

## Packet Sent to CAs

Delivered Monday of Week −1. CAs had 3 business days to respond.

### Contents
1. **Cover brief** — what the product is, 2-page context on merchant segment and volumes
2. **Sample invoice PDF** — 3 variants (airline A, airline B, round-trip with return leg)
3. **Technical spec extract** — FR-05 from PRD (HSN code, tax rates, invoice numbering rules)
4. **Test invoice batch** — 10 generated invoices across edge cases (long business name, Unicode in business name, multiple tax states, GSTIN with slash, etc.)
5. **Open questions** — 4 specific items Finance flagged for CA input

### Open Questions Sent

| # | Question | Why We Asked |
|---|---|---|
| Q1 | Is HSN 996411 correct for all domestic economy, or should we differentiate by airline-operated vs. charter? | Wanted explicit CA sign-off rather than assumption |
| Q2 | For round-trip bookings on a single PNR, is a single invoice or two invoices (one per leg) preferred? | Affects invoice-numbering design |
| Q3 | If a merchant's state of registration differs from departure state, does SGST split across two states? | Cross-state GST handling |
| Q4 | What is the acceptable latency between booking and invoice for GST compliance? | Our 10-sec SLA vs. legal requirement |

---

## CA Reviewers

| # | CA | Firm | Region | Specialty |
|---|---|---|---|---|
| **CA1** | Shri R. Bansal, FCA | Bansal & Associates (Kanpur) | UP | SMB retail / kirana specialist |
| **CA2** | Smt. N. Desai, FCA | Desai Patel LLP (Surat) | Gujarat | Textile SME / GST advisory |
| **CA3** | Shri M. Rajeev, FCA, LLB | Rajeev Tax Consultants (Jaipur) | Rajasthan | Handicraft / tourism sector |

CAs were selected intentionally from the three pilot cities to get regional CA perspective, not from a central Delhi/Mumbai firm.

---

## Feedback Received

### CA1 (Bansal, Kanpur) — Approved with 2 minor changes

| Item | Feedback | Our Action |
|---|---|---|
| Layout | "Business name should be bold and larger font than GSTIN for CA readability." | ✅ Applied |
| Invoice number format | "Format `PTM/YYYY/MM/NNNNNN` is fine. Confirm sequence never resets — critical under GST law." | ✅ Confirmed — sequence is global-increment per merchant, never resets |
| Q1 (HSN) | "996411 correct for all scheduled passenger air transport, regardless of airline model." | — |
| Q2 (round-trip) | "Two separate invoices preferred for round-trip — one per leg. Easier reconciliation for merchant." | ✅ Design updated: each leg generates its own invoice |

### CA2 (Desai, Surat) — Approved with 1 clarification

| Item | Feedback | Our Action |
|---|---|---|
| State code handling | "GSTIN state code determines CGST vs. SGST vs. IGST. If merchant's home state ≠ departure state, IGST applies (not split CGST/SGST). Clarify which state governs." | ✅ Clarified — merchant GSTIN state is the "customer state"; departure state is the "supplier state" (the airline's state). If different = IGST. Invoice engine updated to compute correctly. |
| Q3 (cross-state) | Answered above | — |
| Q4 (latency) | "No regulatory time limit; airline industry standard is same-day. 10 sec is excellent." | — |
| Layout | "Add airline GSTIN and PNR explicitly — auditors will look for this." | ✅ Applied — both fields now visible in invoice body |

### CA3 (Rajeev, Jaipur) — Approved unconditionally

Rajeev had one note: *"I have reviewed the 10-invoice test batch. All edge cases including long names, special characters, and multi-leg bookings render correctly. Format is fully GST-compliant. I would accept these as filings for my clients."*

No design changes requested.

---

## Design Changes Applied After CA Review

1. **Business name** — 14pt bold, above GSTIN line (was 12pt regular)
2. **IGST computation** — when merchant home state ≠ airline supplier state, IGST replaces CGST+SGST (was computing CGST+SGST regardless)
3. **Round-trip invoicing** — two invoices for round-trip PNR (was one combined invoice)
4. **Airline GSTIN + PNR** — added as explicit fields (were embedded in fine print)
5. **Invoice numbering** — documented the global-increment guarantee in CA-facing FAQ sent to merchants

All 5 changes tested in regression batch Thursday; new PDFs shown to CA1 and CA2 for re-confirmation (no objection from either).

---

## Final Sign-Off

| CA | Decision | Conditions | Date |
|---|---|---|---|
| CA1 (Bansal) | **APPROVED** | Items addressed | Week −1 Wed |
| CA2 (Desai) | **APPROVED** | IGST clarification applied | Week −1 Thu |
| CA3 (Rajeev) | **APPROVED** | None | Week −1 Tue |

**All 3 CAs signed formally. Letters on file with Finance (soft copy) and Legal (original hard copy).**

---

## Long-Term Monitoring

CAs have agreed to quarterly reviews of anonymized invoice batches post-launch. If a regulatory change occurs (e.g., GST rate revision, HSN code update), the CA panel is the first escalation.

A 2025 monsoon-session legislative update is anticipated (HSN code harmonization); Finance Engineering has a watchlist for this and a process to push invoice-template updates within 2 weeks of any published change.

---

## Legal + Tax Counsel Cross-Sign

Separately from the CA panel, Paytm's in-house tax counsel (office of General Counsel) reviewed and signed off the invoice format on Thursday. This completes NFR-C3 requirement.

---

## Output to Week 1

Invoice format **LOCKED** for pilot scope. Any change request during pilot is triaged by Finance + PM; a change requires re-sign by at least CA1 (fastest responder).

---

*Owner: Finance Engineering Lead · Co-signers: Tax Counsel, 3 CAs · Last updated: April 2026 (EOW −1)*
