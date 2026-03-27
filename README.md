# KnowledgeFlow

**Agentic knowledge architecture for the regulated enterprise.**  
TOGAF ADM · GCP · EU AI Act · GDPR · Human-in-the-loop by design.

> A portfolio architecture by [Siddharth Rao](https://raosiddharth.com) — Enterprise AI Architect · GCP · TOGAF 10  
> [Portfolio](https://raosiddharth.com) · [LinkedIn](https://www.linkedin.com/in/siddharthpotukuchi) · [GitHub](https://github.com/raosiddharthp)

---

## What this is

KnowledgeFlow is a reference enterprise architecture — not a product, not a prototype.

It demonstrates how a senior architect thinks through an agentic AI system end-to-end: from business motivation through infrastructure deployment, across governance, compliance, economics, and Day 2 operations. Every decision is documented. Every trade-off is argued. Every diagram traces to a TOGAF ADM phase.

The anchor scenario is Meridian Consulting Group — 12,000 consultants, 40 offices, 6 continents — using agentic AI to instrument, govern, and accelerate organisational knowledge flow at regulated-industry scale.

---

## Architecture overview

```
┌─────────────────────────────────────────────────────────┐
│  L1  Experience       React/Next.js · Apigee X · IAP    │
├─────────────────────────────────────────────────────────┤
│  L2  Orchestration    LangGraph · Vertex AI Agent Builder│
│                       Gemini 1.5 Pro · Pub/Sub           │
├─────────────────────────────────────────────────────────┤
│  L3  Intelligence     Neo4j Aura · Vertex AI Search      │
│                       Vertex AI Pipelines · BigQuery     │
├─────────────────────────────────────────────────────────┤
│  L4  Infrastructure   GKE Autopilot · Terraform          │
│                       Chronicle SIEM · VPC SC · OPA      │
└─────────────────────────────────────────────────────────┘
          ↕ cross-cutting: GDPR · EU AI Act · HITL · Audit
```

Four strict layers. Each independently deployable, independently governable, independently scalable. Cross-cutting concerns — security, compliance, HITL, audit — span all layers by design, not by integration.

---

## What the portfolio covers

### Architecture (13 diagrams)

| Diagram | Notation | TOGAF Phase | Description |
|---------|----------|-------------|-------------|
| D1 | ArchiMate 3.2 Motivation | Phase A | Business Motivation Model — drivers, assessment, goals, principles |
| D2 | ArchiMate Capability Map | Phase B | What the system does, technology-agnostic |
| D3 | Value Stream | Phase B | Current state failure points vs target state |
| D4 | ArchiMate Overview | Phase C | 30,000ft four-layer view |
| D5 | C4 Container | Phase C | Experience layer container diagram |
| D6 | C4 Component | Phase C | LangGraph agent topology with HITL conditional edge |
| D7 | C4 Component | Phase C | ML/MLOps pipelines — ingestion, embedding, RLHF |
| D8 | C4 Deployment | Phase C | Multi-region GCP deployment (3 regions) |
| D9 | Access Control | Phase C | RBAC + ABAC + OPA composable policy architecture |
| D10 | Data Flow | Phase C | GDPR lineage + Art. 17 erasure cascade |
| D11 | Decision Gates | Phase C | HITL intercept types and routing |
| D12 | Maturity Model | Phase E–H | Crawl/Walk/Run with ADM phase gates |
| D13 | Operational Runbook | Phase H | Day 2: erasure cascade, RLHF loop, cost circuit breaker |

### Decision records (6 ADRs)

| ID | Decision | Status |
|----|----------|--------|
| ADR-001 | Neo4j Aura Enterprise over relational/document model | Accepted |
| ADR-002 | Composable RBAC + ABAC + OPA over single-model access control | Accepted |
| ADR-003 | LangGraph for agent orchestration over prompt chaining | Accepted |
| ADR-004 | GCP-native managed services over self-hosted OSS | Accepted |
| ADR-005 | Chronicle SIEM as compliance audit backbone | Accepted |
| ADR-006 | HITL as first-class architectural construct, not a UI feature | Accepted |

Each ADR documents: context · decision · consequences · strongest rebuttal with structured response.

### Governance

- **EU AI Act** — dual-tier risk classification (Art. 6 high-risk + Art. 52 limited risk). All Art. 9–15 obligations mapped to structural architectural controls, not documentation retrofits.
- **GDPR** — six-principle architectural response (Art. 5). Right to erasure implemented as a four-stage cascade across Neo4j, Vertex AI Search, BigQuery, and Chronicle with mandatory DPO gate.
- **HITL** — five intercept types modelled as conditional edges in LangGraph. Confidence threshold 0.85 default. Target escalation rate <5% (Run phase). EU AI Act Art. 14 satisfaction is structural.
- **Responsible AI** — all eight Google RAI principles mapped to verifiable GCP architectural controls.

### Economics

- Cost unit: **cost per knowledge query** (not seat, not module).
- Mid-market: $0.11/query · Enterprise: $0.05/query · Global: $0.03/query
- Six architectural cost optimisation levers, each bound to an ADR.
- ROI model: 480% (mid-market year 1) → 2,700% (global enterprise year 1).

### Operations (Day 2)

- Ten operational scenarios with defined triggers, procedures, and SLOs.
- Per-layer RTO/RPO: L1 <15min/0min · L2 <30min/<5min · L3 <4h/<1h · L4 <2h/0min.
- Five AI-specific incident types with regulatory obligation mapping.
- GDPR erasure cascade SLA: <72 hours (statutory). Automated with mandatory DPO gate.

---

## Technology stack

| Layer | Component | Justification |
|-------|-----------|---------------|
| Orchestration | Vertex AI Agent Builder + LangGraph | GCP-native, observable state machine, HITL as graph edge |
| LLM | Gemini 1.5 Pro | 1M token context, native Vertex AI, multimodal |
| Knowledge graph | Neo4j Aura Enterprise (GCP Marketplace) | Property graph, Cypher, GDPR-compliant managed, VPC peering |
| Vector retrieval | Vertex AI Search | Managed ANN, SLA-backed, no index ops overhead |
| Access policy | OPA (Open Policy Agent) | Declarative Rego, version-controlled, CI/CD testable |
| MLOps | Vertex AI Pipelines | Three pipelines: ingestion, embedding, RLHF feedback loop |
| Audit | Chronicle SIEM | Sub-second forensic search, YARA-L detection, 24-month retention |
| Analytics | BigQuery + Dataplex | Structured audit log, data lineage catalogue, column-level security |
| Governance dashboard | Looker | RBAC on dashboards, native BigQuery, scheduled compliance reports |
| IaC | Terraform + Cloud Build | GitOps, no manual provisioning, drift detection |
| Runtime | GKE Autopilot | Managed Kubernetes, pod auto-provisioning, stateful agent workloads |
| API gateway | Apigee X | Auth, rate limiting, per-user quota enforcement |
| Compliance perimeter | VPC Service Controls | Data perimeter, no public egress for PII-touching services |

**Explicitly rejected (with documented rationale):** SharePoint Copilot · Confluence AI · homegrown RAG on LangChain · Glean. See Technology Radar in the portfolio.

---

## Access control model

Three composable layers. All three must pass for a knowledge node to be returned.

```
Request
  │
  ├─[1] RBAC via GCP IAM
  │       Role: KF_Viewer | KF_Curator | KF_Admin | KF_Compliance
  │       Coarse-grained service access. Cannot express node-level policy.
  │
  ├─[2] ABAC via node attributes
  │       Every Neo4j node carries: geography · sensitivity · domain · owner_org
  │       Retrieval agent filters candidate set before graph traversal.
  │
  └─[3] OPA policy engine (Rego)
          Fine-grained, composable, version-controlled rules.
          Denied-access behaviour is configurable per rule:
          anonymise | hide | restrict | audit-only
```

Example Rego rule — geo-exclusion with role override:
```rego
allow { input.user.role == "KF_Admin" }

allow {
  input.user.geography != "CN"
  input.node.geography != "NL-RESTRICTED"
}

denied_behaviour := "anonymise" { input.node.geography == "NL-RESTRICTED" }
denied_behaviour := "hide"      { input.node.sensitivity == "confidential" }
```

Policy changes require a Pull Request, 100% Rego test coverage in CI, architecture owner + DPO approval for rules touching geo-exclusion or sensitivity ceilings. Rollback: single `terraform apply`, <10 minutes.

---

## Agent topology (LangGraph)

```
Entry node
    │
    ▼
Query agent ──────── Policy agent (OPA eval)
    │                     │
    │                     ▼
    └──────────── Retrieval agent
                  (Neo4j Cypher + Vertex AI Search ANN hybrid)
                          │
                          ▼
                  Synthesis agent (Gemini 1.5 Pro)
                          │
                   confidence < 0.85?
                    ├── YES → HITL queue (async, Pub/Sub)
                    └── NO  → Response delivery
                          │
                    Audit agent (every transition → Chronicle)
```

Each agent node has a bounded tool contract. No agent has open-ended internet access or write access to external systems. The HITL conditional edge is not a UI feature — it is a typed LangGraph edge evaluated at every synthesis cycle.

---

## HITL design

Human oversight in this architecture satisfies EU AI Act Article 14 structurally, not cosmetically.

| Intercept type | Trigger | Reviewer | SLA |
|----------------|---------|----------|-----|
| Confidence | Score < 0.85 | KF_Curator | 4h business hours |
| Sensitivity escalation | confidential node or cross-geo access | KF_Admin / Data Steward | 2–24h |
| GDPR erasure gate | Art. 17 DSR received | DPO | 72h (statutory) |
| Policy change review | OPA Rego PR affecting geo/sensitivity | Architecture owner + DPO | 5 business days |
| Anomaly alert | Chronicle YARA-L rule fires | Security ops (on-call) | 30 minutes |

Reviewer fatigue is architecturally monitored: approval rate >95% over 7 days triggers an architecture owner alert. The target escalation rate is <5% of queries in the Run phase — not by suppressing HITL, but by improving the confidence model through the RLHF feedback loop until genuine uncertainty is rare.

---

## MLOps pipelines

Three Vertex AI Pipelines, each with a distinct trigger and governance model.

**Pipeline 1 — Knowledge ingestion**  
Pub/Sub event → entity extraction → ontology validation → Neo4j write → Dataplex lineage tag. Fully automated. No manual graph editing permitted in production.

**Pipeline 2 — Embedding generation**  
Weekly scheduled (Sunday 02:00 UTC, Spot VMs) + incremental on every graph write batch. Cadence is a Terraform variable — no code change required to adjust.

**Pipeline 3 — RLHF feedback loop**  
Every HITL decision (approve/edit/reject) is labelled and stored in BigQuery. Pipeline 3 consumes the labelled feedback store to retrain the synthesis model. A/B test gates every new model version at 10% of traffic before full promotion. Rollback: previous model registry version promoted in <15 minutes.

All retraining runs require a signed-off model card before the model version is promoted. No model is deployed without documented training data lineage, evaluation metrics, and bias assessment results.

---

## GDPR erasure cascade

Right to erasure (Art. 17) is a designed system capability, not a manual process.

```
DSR received
    │
    ▼
Dataplex lineage lookup (automated, <5min)
    │  ← identifies all affected nodes, embeddings, analytics rows
    ▼
DPO HITL gate (mandatory — no automated irreversible deletion)
    │
    ├── Neo4j node + edge deletion (Cypher)
    ├── Vertex AI Search embedding purge
    ├── BigQuery row anonymisation (audit columns retained)
    └── Chronicle immutable record (retained 24 months — Art. 17(3))

SLA: full cascade <72 hours (GDPR Art. 12 deadline)
```

Art. 17(3) note: the audit record of the erasure event is itself a compliance obligation. The fact that data was erased — when, by whom, on whose authority — must be retained. Chronicle holds this record. The personal data is gone; the governance record is not.

---

## Cost model

| Tier | Monthly queries | Monthly cost | Cost per query |
|------|----------------|--------------|----------------|
| Mid-market | 10,000 | $26,000 | $0.11 |
| Enterprise | 100,000 | $72,000 | $0.05 |
| Global enterprise | 1,000,000 | $140,000 | $0.03 |

**Human cost baseline:** A knowledge request consuming 45–90 minutes of combined senior consultant time at $180–240/hr fully-loaded costs $135–360 per event. KnowledgeFlow's cost per query is $0.03–$0.11. The ROI argument at scale is not a spreadsheet exercise — it is an architectural one.

Layer 2 (LLM tokens) is the dominant cost driver at all tiers (~55%). Six architectural optimisation levers address this directly: intelligent model routing (Flash vs Pro), result caching (Redis on Memorystore), Spot VM embedding regeneration, tiered cold storage, per-user query budgets, and committed use discounts at Enterprise tier.

---

## Deployment

```
europe-west4  (primary — Netherlands, GDPR data residency)
us-central1   (secondary — DR failover)
asia-east1    (tertiary — APAC read latency)
```

Three independent Terraform workspaces. State stored in GCS multi-region bucket. Secondary region is pre-configured but not fully provisioned — `terraform apply` provisions the full secondary stack in <2 hours. No snowflake configurations; every infrastructure state is a versioned Git artefact.

---

## Roadmap

| Phase | Timeline | ADM phases | Exit gate criteria |
|-------|----------|------------|--------------------|
| Crawl | 0–6 months | A–D | ≥100 active users · HITL escalation rate baselined · DPO sign-off |
| Walk | 6–15 months | E–F | ≥500 active users · escalation rate <10% · multi-region failover tested |
| Run | 15–30 months | G–H | <5% HITL escalation · cost-per-query within target band · EU AI Act conformity assessment complete |

Phase transitions require a formal TOGAF ADM Phase H gate review by the Architecture Board. Timeline is indicative — gate criteria are exit conditions, not deadlines.

---

## Key architectural positions

**On HITL:** Most enterprise AI deployments treat human oversight as a feedback button. This does not satisfy EU AI Act Article 14. Oversight must be interceptive — humans must have the ability to halt, modify, or reverse agent decisions before they take effect. In KnowledgeFlow, HITL is a LangGraph conditional edge. It cannot be bypassed by the application layer.

**On access control:** A policy of "Netherlands users cannot see China office data except when tagged non-sensitive and accessed by a Partner-level role" cannot be expressed in IAM. OPA is not additional complexity — it is the only layer that can express the access semantics the business actually requires.

**On the graph model:** Neo4j is not a retrieval optimisation. Multi-hop relationship traversal with attribute-level policy filtering is not a retrieval problem. It is a graph problem. The impedance mismatch cost of modelling graph semantics in a relational schema is borne at query time, at scale, with latency consequences that are not acceptable in an interactive system.

**On vendor lock-in:** The lock-in risk (ADR-004) is real but overstated at the architecture level. The portable surface is large: Neo4j, OPA, LangGraph, and Terraform all run on any platform. The GCP lock-in surface is the managed compute and storage layer — which is also the layer that costs most to self-manage and is least differentiating to own. The risk is accepted and documented, not ignored.

**On Day 2:** A system architecture that cannot describe its own Day 2 operating model is not production-ready. It is a proof of concept awaiting an incident.

---

## About this portfolio

This is an architecture portfolio piece, not a production deployment. The code artefact (`knowledgeflow.html`) is a single-file interactive document — no build step, no dependencies, GitHub Pages ready — containing all 13 diagrams, all 6 ADRs, the interactive knowledge graph simulation, and the complete eight-tab architecture portfolio.

The goal is to demonstrate how a senior/lead enterprise AI architect thinks: from business motivation to operational runbook, across governance frameworks, with every decision documented and every trade-off argued.

---

## Author

**Siddharth Rao**  
Enterprise AI Architect · Google Cloud Professional · TOGAF 10 Certified  
15+ years across enterprise architecture, ML engineering, and generative AI leadership.

[raosiddharth.com](https://raosiddharth.com) · [LinkedIn](https://www.linkedin.com/in/siddharthpotukuchi) · [GitHub](https://github.com/raosiddharthp)

---

*KnowledgeFlow is part of the Autonomous Series — a collection of architecture portfolio pieces demonstrating production-grade AI system design on Google Cloud Platform.*
