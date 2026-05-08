# System Design: B2B AI SaaS Marketing Copilot (AccountPulse)

## 1) Problem and Scope

AccountPulse is a B2B AI SaaS platform for marketing teams at mid-market and enterprise companies.  
It helps users:

- identify at-risk accounts and growth opportunities
- generate campaign and outreach recommendations
- orchestrate cross-channel actions (email, ads, CRM tasks, Slack alerts)
- measure impact and continuously improve recommendation quality

This design focuses on a production-grade, real-world architecture that supports both analytics and AI-driven decisioning.

---

## 2) Requirements

### Functional Requirements

- Ingest customer data from CRM, product analytics, ad platforms, email tools, support tools, and billing systems.
- Build unified account and contact profiles.
- Compute health/risk/opportunity features for each account.
- Serve near-real-time "next best action" recommendations to UI and API clients.
- Trigger automated workflows with approval gates for high-impact write actions.
- Track recommendation outcomes and user feedback.
- Support tenant-level isolation for multi-tenant SaaS.

### Non-Functional Requirements

| Requirement | Target |
|---|---|
| Daily active users | 25,000 marketing + CS users |
| Managed customer accounts | 10 million records |
| Peak request load | 2,500 RPS read, 400 RPS write |
| Latency | P50 < 200ms, P95 < 600ms, P99 < 1200ms |
| Availability | 99.95% for read APIs |
| Data freshness | Feature freshness <= 5 minutes for key signals |
| Security/compliance | SOC2, audit logs, encryption at rest + in transit |

---

## 3) Back-of-Envelope Estimation

Assumptions:

- 25,000 DAU
- 120 API actions per user per day
- peak multiplier: 5x over average
- average response payload: 8 KB
- raw event ingestion: 1.2B events/day at 1.2 KB/event

### Throughput

- Average RPS = (25,000 * 120) / 86,400 ~= 35 RPS
- Peak RPS ~= 35 * 5 * safety_factor(8) ~= 1,400 RPS
- Provision target with headroom: 2,500 RPS read + 400 RPS write

### Bandwidth (API egress)

- 2,500 RPS * 8 KB ~= 20,000 KB/s ~= 19.5 MB/s (~156 Mbps)

### Storage

- Event storage/day = 1.2B * 1.2 KB ~= 1.44 TB/day
- Annual raw storage ~= 525 TB/year
- With columnar compression (4:1 typical): ~= 130 TB/year hot analytics footprint

### Latency Budget (recommendation read path, P95 600ms)

- API gateway + auth: 40ms
- feature/profile fetch (cache + OLTP): 90ms
- model inference service: 180ms
- policy/rule engine + response shaping: 120ms
- network jitter + retries budget: 170ms

---

## 4) High-Level Architecture

```text
Data Sources -> Ingestion Connectors -> Stream Bus -> Feature Pipelines -> Feature Store
                                                           |                   |
                                                           v                   v
                                                     Data Lake/Warehouse   Online Feature Cache

Web App / API Clients -> API Gateway -> AuthZ -> Marketing Intelligence Service
                                                     |         |          |
                                                     v         v          v
                                               Reco Inference  Rules      Workflow Orchestrator
                                                     |                      |
                                                     v                      v
                                               Model Registry          External Actions
                                                     |            (CRM, email, ads, Slack)
                                                     v
                                               Monitoring + Audit + Feedback Loop
```

---

## 5) Component Design

### 5.1 API Gateway + Auth

- OAuth/OIDC SSO and JWT validation.
- Tenant-scoped rate limiting and request quotas.
- WAF and request schema validation.

### 5.2 Ingestion Layer

- Managed connectors (Salesforce, HubSpot, Marketo, Segment, Google Ads, LinkedIn Ads, Zendesk, Stripe).
- CDC + webhook + scheduled pulls.
- Events published to a stream bus (Kafka/PubSub).

### 5.3 Data Platform

- Raw immutable event log in object storage.
- Bronze/silver/gold transformations in batch + streaming jobs.
- Warehouse for BI and offline model training datasets.

### 5.4 Feature Platform

- Offline feature computation (hourly/daily) for robust aggregates.
- Streaming feature updates (sub-5-minute freshness for key behavioral signals).
- Online feature cache for low-latency serving.

### 5.5 Recommendation & Inference Service

- Candidate generation + ranking model.
- Tenant-aware policy controls (e.g., channel restrictions, legal constraints).
- Confidence and explainability metadata on each recommendation.

### 5.6 Rules + Safety Policy Engine

- Deterministic guardrails around LLM/model outputs.
- Risk scoring for write actions.
- Human approval workflow for irreversible operations.

### 5.7 Workflow Orchestrator

- Executes approved actions (create CRM tasks, launch emails, adjust campaign budget suggestions, notify Slack).
- Idempotency keys and retry policies.
- Dead letter queue for failed actions.

### 5.8 Feedback + Monitoring

- Logs model predictions, actual outcomes, acceptance/rejection rates.
- Detects data drift and concept drift.
- Tracks business KPIs (pipeline influenced, conversion lift, CAC reduction).

---

## 6) Deep Dive: Hard Parts

### A) Real-Time Recommendation Serving

Challenges:

- strict latency at P95 under high multi-tenant load
- consistent tenant isolation
- graceful degradation during downstream failures

Design:

- Cache-first strategy for account profile + top features.
- Parallel fan-out: fetch features, recent touchpoints, and policy constraints concurrently.
- Circuit breakers for external systems; fallback to safe baseline recommendations.
- Model version pinning per tenant with canary rollout.

### B) Multi-Tenant Isolation + Governance

Challenges:

- strong data boundaries
- per-tenant schema customization
- auditability and compliance

Design:

- Tenant ID propagated as mandatory context in every event/request.
- Row-level security in warehouse + tenant-partitioned storage paths.
- Encryption keys scoped by tenant tier.
- Full action audit trail: who requested, model/rule decision, approved by whom, executed result.

### C) AI + Rule Hybrid Decisioning

Challenges:

- avoid over-trusting probabilistic outputs
- preserve deterministic business constraints

Design:

- Two-stage decisioning:
  1. AI model proposes ranked actions.
  2. Policy engine filters/rewrites/blocks actions by hard rules.
- Confidence thresholding:
  - high confidence: auto-suggest
  - medium confidence: suggest with warning
  - low confidence: no action, ask for more context

---

## 7) Tradeoffs

| Decision | Chosen | Alternative | Why Chosen |
|---|---|---|---|
| Architecture style | Modular services | Monolith | Team scalability and isolated deployments for data/serving workloads |
| Data processing | Hybrid streaming + batch | Batch only | Needed freshness < 5 mins for timely account signals |
| Feature serving | Online cache + offline store | Offline only | Required low-latency inference with up-to-date features |
| AI control | Model + deterministic rules | Model only | Better safety/compliance and predictable behavior |
| Tenancy model | Shared infra + strict logical isolation | Dedicated infra per tenant | Lower cost with acceptable risk controls for target segment |

---

## 8) Operational Concerns

### Reliability

- Multi-AZ deployment for stateless services.
- Active-passive DR across regions for critical metadata and model serving.
- SLO-driven alerting on latency, error rate, and saturation.

### Deployment Strategy

- Blue/green for API services.
- Canary rollout for models and rule policies.
- Backward-compatible schema migrations first, then reads/writes switch.

### Failure Handling

- If inference service degrades: fallback to rules-only baseline actions.
- If connectors fail: degrade freshness labels and show stale-data banner.
- If workflow execution fails: DLQ + replay with bounded retries.

### Cost Controls

- Autoscaling by queue depth and CPU.
- Tiered storage lifecycle for cold events.
- Model serving right-sizing and batch inference for low-priority workloads.

---

## 9) API Surface (Examples)

- `POST /v1/recommendations/generate`
- `GET /v1/accounts/{account_id}/health`
- `POST /v1/workflows/execute`
- `POST /v1/workflows/{id}/approve`
- `GET /v1/audit/actions`

---

## 10) Success Metrics

Technical:

- recommendation API P95 latency
- inference error rate
- workflow success rate
- feature freshness SLA adherence

Business:

- campaign conversion lift
- account expansion rate
- churn-risk reduction
- time saved per marketer/CSM

