# Security Review Checklist — P10
**PII · mTLS · Data Retention · Compliance · Sign-Off**
*Paytm Merchant Travel Desk | 05-launch-plan | CTO + CISO Co-Owned*

---

## Purpose

P10 gates go-live. No launch without CISO sign-off. This document enumerates every item reviewed, the status, the remediation owner if open, and the final signatures.

---

## Review Scope

- All services in the voice stack (per `infrastructure-runbook.md`)
- Data flows: merchant phone number → STT → NLU → booking APIs → GST invoice → WhatsApp
- Third-party integrations: Google Cloud STT/TTS, Meta WhatsApp, Paytm internal APIs, GSTN validation
- Call recordings, session state, analytics events

---

## Checklist

### 1. Transport Security

| # | Item | Target | Status | Evidence |
|---|---|---|---|---|
| T1 | All internal service calls use mTLS | Istio-enforced | ✅ | Istio `PeerAuthentication: STRICT` applied to namespace |
| T2 | External APIs called over TLS 1.3+ | Required | ✅ | Egress audit via `curl --tls-version`; all targets support 1.3 |
| T3 | SIP trunk uses SIP over TLS (SIPS) | Required | ✅ | Vendor confirmation letter attached |
| T4 | Cert rotation < 90 days | Automated | ✅ | cert-manager with LetsEncrypt auto-rotation |

### 2. Authentication & Authorization

| # | Item | Target | Status | Evidence |
|---|---|---|---|---|
| A1 | Merchant auth via caller ID + KYC lookup only (no OTP) | Per FR-01 | ✅ | Verified in dialogue-manager flow tests |
| A2 | Internal API mTLS + per-service ServiceAccounts | Required | ✅ | Kubernetes RBAC audit complete |
| A3 | No shared API keys for Paytm internal APIs | Required | ✅ | Per-service credentials via Secret Manager |
| A4 | Human agent console SSO (Paytm IdP) | Required | ✅ | OIDC integration live |
| A5 | CTO/Eng Lead approval for prod Argo CD sync | Enforced | ✅ | Argo CD policy applied |

### 3. PII Handling

| # | Item | Target | Status | Evidence |
|---|---|---|---|---|
| P1 | Phone number never logged in application logs | Per FR-07 | ✅ | Logging interceptor redacts E.164 patterns |
| P2 | Full GSTIN never in analytics events (last-4 only) | Per FR-07 | ✅ | Event schema linter enforces |
| P3 | Merchant name, address, business details encrypted at rest | AES-256 | ✅ | GCP CMEK verified on all DBs |
| P4 | PII masked in error traces | Required | ✅ | OpenTelemetry attribute scrubbing rule deployed |
| P5 | No PII in STT transcript logs (only intent + entity labels) | Required | ⚠️ | **F5 — partial: raw transcript retained 7 days for NLU retrain. Approved with CISO note.** |

### 4. Call Recording

| # | Item | Target | Status | Evidence |
|---|---|---|---|---|
| R1 | Recordings encrypted at rest (AES-256) | Per NFR-S1 | ✅ | GCS CMEK-enabled bucket |
| R2 | Deletion after 30 days | Per NFR-S1 | ✅ | GCS lifecycle policy verified |
| R3 | Consent disclosure at call start | Per NFR-C1 | ✅ | TTS disclosure line played in Stage 1: "Yeh call quality ke liye record ho sakti hai." |
| R4 | Stereo split (agent/merchant) for QA | Required | ✅ | SIP vendor delivers stereo file |
| R5 | Access-controlled (break-glass only; audit logged) | Required | ✅ | IAM role `voice-recording-auditor` with approvals |

### 5. Data Retention

| # | Item | Target | Status | Evidence |
|---|---|---|---|---|
| D1 | Call recordings: 30 days | Per NFR-S1 | ✅ | Policy live |
| D2 | Session state in Redis: 30-minute TTL | Per design | ✅ | Redis key inspection confirms |
| D3 | Raw STT transcripts: 7 days (with PII scrubbing pre-storage) | Per CISO note | ✅ | Retention policy applied |
| D4 | Analytics events in BigQuery: 13 months | Per data governance | ✅ | Partition expiration configured |
| D5 | GST invoices: 7 years (per GST law) | Legal requirement | ✅ | Separate long-term bucket with tax-compliant retention |

### 6. Compliance

| # | Item | Target | Status | Evidence |
|---|---|---|---|---|
| C1 | PDPA-compliant consent captured at onboarding | Per NFR-C1 | ✅ | Legal-approved language in onboarding SMS/WA flow |
| C2 | TRAI DND compliance for outbound calls | Per NFR-C2 | ✅ | DND registry API integrated; calls blocked for listed numbers |
| C3 | GST invoice format tax-counsel approved | Per NFR-C3 | ✅ | Tax counsel letter on file (dated Week −2) |
| C4 | RBI compliance: credit within NBFC license scope | Per NFR-C4 | ✅ | Credit product lineage confirmed by Credit Risk team |
| C5 | PCI-DSS Level 1 inheritance for payment ops | Per NFR-S4 | ✅ | Payment path delegated to Paytm PCI-DSS environment |
| C6 | Call recording disclosure compliant with Telecom Consumers Protection rules | Required | ✅ | Legal sign-off |

### 7. Vulnerability Management

| # | Item | Target | Status | Evidence |
|---|---|---|---|---|
| V1 | Pen test: external perimeter | Completed | ✅ | Report: 0 critical, 2 medium (remediated) |
| V2 | Pen test: internal voice stack | Completed | ✅ | Report: 0 critical, 1 medium (remediated) |
| V3 | Dependency scanning (Snyk) | Blocking on CI | ✅ | 0 critical CVEs in images |
| V4 | Container image signing | Required | ✅ | Cosign verification enforced at admission |
| V5 | Secret rotation (API keys, DB creds) | 90-day SLA | ✅ | Secret Manager rotation policies applied |

### 8. Incident Response

| # | Item | Target | Status | Evidence |
|---|---|---|---|---|
| I1 | Runbook: PII leak scenario | Required | ✅ | Runbook reviewed + CISO-signed |
| I2 | Runbook: call recording breach | Required | ✅ | Same |
| I3 | 24/7 on-call rotation | Required | ✅ | PagerDuty schedule live |
| I4 | Notification path: DPO + Legal within 6 hours of confirmed breach | Per policy | ✅ | Playbook tested in tabletop Week −2 |

### 9. Third-Party Risk

| # | Item | Status | Notes |
|---|---|---|---|
| TP1 | Google Cloud (STT/TTS/BigQuery) | ✅ | DPA on file; SOC 2 Type II |
| TP2 | Meta WhatsApp Business | ✅ | DPA on file; EU-GDPR compliant (India data localized) |
| TP3 | Telecom vendor (Tata/Exotel) | ✅ | DPA signed; recording handling reviewed |
| TP4 | GSTN portal | ✅ | Government integration; signed agreement on file |

### 10. Residual Risks Accepted

| Risk | Severity | Mitigation | Accepted By |
|---|---|---|---|
| Single-region deployment (no DR) | Medium | Recovery plan: <4 hr RTO from snapshot restore; acceptable for pilot scale | CTO + CISO |
| Raw STT transcripts retained 7 days | Low | PII scrubbing pre-storage; access audited; documented in F5 | CISO |
| Voice biometric spoofing (e.g., cloned voice impersonates merchant) | Low | Phone-number-based auth + 1st-utterance behavioral baseline; post-pilot evaluation for voice-biometric second factor | CTO |

---

## Sign-Off

| Signatory | Role | Decision | Date |
|---|---|---|---|
| CISO | Paytm Security Office | **APPROVED with F5 residual-risk note** | Week −2 Friday |
| DPO | Data Protection | **APPROVED** | Week −2 Friday |
| Legal Counsel | Compliance | **APPROVED** | Week −2 Friday |
| CTO | Engineering | **APPROVED** | Week −2 Friday |

This approval is valid for the 500-merchant pilot scope. Any material expansion (scale beyond 500, new geographies, new data types) requires re-review.

---

*Owner: CTO + CISO · Last updated: April 2026 · Repo: paytm-merchant-travel-desk/05-launch-plan*
