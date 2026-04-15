# Wireframes
**WhatsApp Companion Screen — All States**
*Paytm Merchant Travel Desk | 02-solution-design/wireframes*

---

## Overview

These wireframes represent the WhatsApp Business API companion screen that runs alongside the voice call. The screen is the **verification and delivery layer** — not the primary interface.

See [`../companion-screen-design.md`](../companion-screen-design.md) for full UX specification.

---

## Files

| File | Stage | Description |
|---|---|---|
| `wireframes-overview.png` | All stages | Side-by-side composite of all 5 screens |
| `wf-stage-2-flight-options.png` | Stage 2 | Flight comparison cards with Select buttons |
| `wf-stage-3-confirmation.png` | Stage 3 | Payment method, auto-filled details, Confirm button |
| `wf-stage-4-documents.png` | Stage 4 | Booking confirmed, GST Invoice PDF, E-Ticket PDF |
| `wf-stage-5-post-call.png` | Stage 5 | Summary card, self-serve actions, NPS rating |
| `wf-error-gstin-issue.png` | Error | GSTIN suspended — draft saved, recovery flow |

---

## Design Notes

- All wireframes use WhatsApp native UI patterns (bubble layout, green CTA buttons, document cards)
- Colour accents on card left-border indicate content type: Blue = Paytm info, Green = confirmed, Orange = warning, Red = airline brand
- Button labels comply with WhatsApp Business API 20-character limit
- PDFs (Invoice + E-Ticket) are shown as document message cards, not inline images
- Emoji NPS rating row in Stage 5 maps to 1–5 scale for analytics

---

*For Figma embed links, replace these static PNGs once Figma designs are exported.*
*Last updated: April 2026*
