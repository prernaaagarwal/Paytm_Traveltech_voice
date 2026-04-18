# Infrastructure Runbook
**Deployment Topology · Service Specs · Scaling · Rollback**
*Paytm Merchant Travel Desk | 05-launch-plan | CTO-Owned*

---

## Purpose

This runbook is the authoritative description of what is deployed where, how it scales, how to roll it back, and who gets paged when it breaks. It is the operational contract between Engineering and Ops for the pilot.

---

## Deployment Topology

```
                   ┌───────────────────────────┐
                   │   Telecom Vendor (SIP)    │
                   │   1800-XXX-XXXX           │
                   └─────────────┬─────────────┘
                                 │ SIP trunk
                   ┌─────────────▼─────────────┐
                   │   GKE Cluster (asia-south1)│
                   │   Namespace: travel-voice  │
                   └─────────────┬─────────────┘
                                 │
     ┌───────────────────────────┼───────────────────────────┐
     │                           │                           │
 ┌───▼─────┐  ┌────────▼────────┐  ┌───────▼────────┐  ┌───────▼────────┐
 │ IVR     │  │ Dialogue        │  │ STT Adapter    │  │ TTS Adapter    │
 │ Gateway │◄─┤ Manager (FSM)   ├─►│ (GCP STT v2)   │  │ (GCP TTS)      │
 │         │  │ StatefulSet × 3 │  │ Deployment × 4 │  │ Deployment × 2 │
 └─────────┘  └────┬────────────┘  └────────────────┘  └────────────────┘
                   │
       ┌───────────┼───────────────────┬────────────────┐
       │           │                   │                │
   ┌───▼────┐  ┌───▼─────┐         ┌───▼────┐      ┌────▼───────┐
   │ NLU    │  │ Redis   │         │ Paytm  │      │ WA Service │
   │ (Cloud │  │ Cluster │         │ APIs   │      │ (templates │
   │  Run)  │  │ 3 nodes │         │ (mTLS) │      │  + PDF)    │
   └────────┘  └─────────┘         └────────┘      └────────────┘
                                        │
                              ┌─────────┼──────────┬──────────┐
                              │         │          │          │
                          Merchant    Travel    Credit      GST
                            API        API        API     Validation
```

---

## Service Inventory

| Service | Runtime | Scaling | Image Tag | Purpose |
|---|---|---|---|---|
| `ivr-gateway` | GKE Deployment (3 replicas) | HPA on SIP sessions | v0.9.0-rc1 | SIP ingress, audio multiplexing |
| `dialogue-manager` | GKE StatefulSet (3 pods) | HPA on concurrent sessions (5/pod) | v0.9.0-rc1 | 5-stage FSM, session orchestration |
| `stt-adapter` | GKE Deployment (4 replicas) | HPA CPU 60% | v0.9.0-rc1 | Google Cloud STT v2 wrapper |
| `tts-adapter` | GKE Deployment (2 replicas) | HPA CPU 60% | v0.9.0-rc1 | Google Cloud TTS + phrase cache |
| `nlu-service` | Cloud Run | 0 → 20 instances, 80 concurrency | v1.0-bert-ft | BERT intent + entity extraction |
| `travel-adapter` | GKE Deployment (2 replicas) | Static | v0.9.0-rc1 | Paytm Travel API wrapper |
| `credit-adapter` | GKE Deployment (2 replicas) | Static | v0.9.0-rc1 | Paytm Credit API + idempotency |
| `gst-service` | GKE Deployment (2 replicas) | Static | v0.9.0-rc1 | GSTIN validation + PDF generation |
| `wa-service` | GKE Deployment (2 replicas) | Static | v0.9.0-rc1 | WhatsApp Business API, PDF dispatch |
| `analytics-sink` | GKE Deployment (1 replica) | Static | v0.9.0-rc1 | Kafka producer (20 event types) |
| `redis-session` | Redis Cluster (3 primaries + 3 replicas) | Manual | 7.2-alpine | Session state, 30-min TTL |

All services: mTLS via Istio sidecar; images signed; rolled out via Argo CD sync.

---

## Environment Matrix

| Environment | Purpose | URL Prefix | Traffic |
|---|---|---|---|
| `dev` | Engineer sandboxes | `dev.travel-voice.internal` | Synthetic only |
| `staging` | Week −2 integration | `stg.travel-voice.internal` | Dogfood + load test |
| `prod-pilot` | 500-merchant pilot | `prod.travel-voice.internal` | Real merchants (Week 1+) |

Promotion: dev → staging (PR merge) → prod-pilot (manual Argo CD sync, requires CTO + Eng Lead approval).

---

## Scaling Configuration

### Horizontal Pod Autoscaling (HPA)

```yaml
# dialogue-manager HPA (session-count-based)
minReplicas: 3
maxReplicas: 30
metric: custom/concurrent_sessions_per_pod
targetValue: 5

# stt-adapter HPA (CPU-based, as STT is I/O bound via GCP)
minReplicas: 4
maxReplicas: 24
metric: resource/cpu
targetAverageUtilization: 60%
```

### NLU Cloud Run Config

```yaml
service: nlu-service
region: asia-south1
minInstances: 1            # warm baseline to avoid cold start for first call of the day
maxInstances: 20           # enough for pilot + 5× burst
concurrency: 80            # BERT inference is <150ms; 80 reqs/instance is safe
cpu: 2
memory: 4Gi
```

### Redis Cluster Sizing

- 3 primaries + 3 replicas (minimum for session-state durability per NFR-SC4)
- Memory: 4 GB per node (sessions avg 12 KB × 3,000 concurrent peak + headroom)
- Persistence: AOF every 1 sec (RDB snapshot every 5 min as backup)
- Eviction policy: `volatile-ttl` (only evict keys with TTL set)

---

## Observability

| Signal | Tool | Key Dashboards |
|---|---|---|
| Logs | GCP Cloud Logging | Per-service + unified call-flow trace |
| Metrics | Prometheus + Grafana | `voice-stack-overview`, `sla-burn`, `session-funnel` |
| Traces | OpenTelemetry → Cloud Trace | Distributed trace from SIP → STT → NLU → API → TTS |
| Alerts | Prometheus Alertmanager → PagerDuty | 24 defined alert rules |
| Call recordings | GCS (encrypted) | 30-day retention per NFR-S1 |

### Alert Severity Map

| Severity | Examples | Notification |
|---|---|---|
| **P0** | >5% of calls failing in 5 min; Redis cluster unhealthy; Credit API 5xx >10/min | PagerDuty page, on-call + CTO |
| **P1** | STT WER drop >5 pp vs. 7-day baseline; WA delivery <95% | PagerDuty page, on-call |
| **P2** | Single pod restart; non-critical integration timeout <1% | Slack `#travel-voice-alerts` |
| **P3** | Informational (deploy events, scale events) | Slack digest |

---

## Rollback Procedures

### Voice Stack (dialogue-manager, STT, TTS adapters)

```
# Argo CD rollback — known-good revision
argocd app rollback travel-voice-dialogue <revision_id>

# SLA to complete rollback: 4 minutes (no traffic loss; session drain)
# Active sessions at rollback time: finish on old version via pod disruption budget
```

### NLU (Cloud Run)

```
gcloud run services update-traffic nlu-service \
  --region=asia-south1 \
  --to-revisions=nlu-service-<last_green>=100
```

Instant rollback via traffic split; no session impact.

### Data-layer Changes

- Redis schema changes: forward-compatible only (additive fields); no breaking migrations during pilot
- BigQuery schema changes: add columns, never drop — analytics queries must tolerate missing fields

---

## On-Call Rotation

| Rotation | Hours | Primary | Secondary | Escalation |
|---|---|---|---|---|
| **Voice Stack** | 09:00–21:00 IST | Platform Eng (3-person rotation) | Engineering Lead | CTO |
| **Night** | 21:00–09:00 IST | Platform Eng shared rotation | Engineering Lead | CTO |
| **NLU/ML** | 10:00–19:00 IST | ML Engineer | AI/ML Lead | CTO |
| **Data Pipeline** | 10:00–19:00 IST | Data Eng | Data Lead | CTO |

Pilot-week override: CTO is on-call 24/7 for the first 72 hours of prod-pilot traffic.

---

## Pre-Launch Cutover Plan (Staging → Prod-Pilot)

| Step | Action | ETA | Owner | Rollback |
|---|---|---|---|---|
| 1 | Freeze staging; image tag `v1.0.0` cut | T-24h | Eng Lead | — |
| 2 | Prod-pilot cluster provisioned (Terraform apply) | T-20h | Platform | `terraform destroy` |
| 3 | Secrets synced (mTLS certs, API keys) | T-18h | Security | Re-rotate |
| 4 | Redis cluster warmed (empty, TTL verified) | T-16h | Platform | — |
| 5 | Telecom SIP trunk re-pointed to prod IVR gateway | T-12h | Telecom + Platform | Re-point to staging |
| 6 | Smoke test: CTO + PM each place 3 real calls | T-4h | CTO | Halt cutover |
| 7 | 50-merchant soft launch opens (Week 1 Monday 09:00) | T-0 | PM | Halt onboarding |

Any step with a P0/P1 issue halts cutover; next attempt scheduled 24 hours later.

---

## Known Limitations Entering Pilot

1. **NLU model is v1.0** — trained on 1,000 synthetic + 200 real queries. Retrain v1.1 planned at Week 4 with pilot transcripts.
2. **No EU data residency** — all data in `asia-south1`. Acceptable for India-only pilot.
3. **WA templates Hindi + English only** — Gujarati/Marathi deferred to Phase 2 per scope doc.
4. **50 concurrent calls capacity validated** — 500 concurrent (post-pilot target) requires re-run of P12 with higher load.
5. **No chaos engineering in pilot** — planned for post-pilot stabilization.

---

*Owner: CTO · Last updated: April 2026 · Repo: paytm-merchant-travel-desk/05-launch-plan*
