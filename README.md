# KnowledgeFlow
### Enterprise Knowledge Graph Architecture · v2.1

**Siddharth Rao** · TOGAF Enterprise Architect · Google Cloud Certified Architect · ML Engineer · Gen AI Leader

---

> *The organisational knowledge graph is the most valuable and least governed data asset in the enterprise. It has no schema, no owner, no SLA, and no recovery plan. KnowledgeFlow fixes that — structurally, not culturally.*

---

## What this is

KnowledgeFlow is a four-layer agentic AI architecture that instruments, governs, and accelerates enterprise knowledge flow at regulated-industry scale. It constructs a **governed, queryable, access-controlled knowledge graph** from the unstructured and semi-structured knowledge assets of a large professional services firm, then uses **LangGraph-orchestrated agents** on Google Cloud Platform to retrieve, synthesise, and deliver that knowledge — with every consequential decision interceptable, auditable, and explainable by design.

This repository contains the **full architecture portfolio** — an interactive HTML document covering the problem framing, system design, ontology specification, agent architecture, governance model, economic analysis, implementation roadmap, and day-2 operations model.

---

## The problem it solves

| Signal | Source |
|--------|--------|
| Knowledge workers spend **20% of their week** searching for information | McKinsey MGI, 2012 |
| Large firms lose **~$47M/year** from unrealised knowledge-driven innovation | Deloitte Insights / HBR, 2019 |
| **35% productivity reduction** attributable to poorly governed knowledge | Forrester, 2022 |
| **30% of senior engineers** in manufacturing projected to retire within 5 years | SME, 2023 |

The root cause is architectural: knowledge graphs are implicit, unstructured, and non-transferable. Every tool deployed against this problem (SharePoint, Confluence, Notion, Guru) treats the symptom — information retrieval — without the structural cause: absence of a governed, schema-defined, access-controlled knowledge architecture.

---

## Architecture overview

```
┌─────────────────────────────────────────────────────────┐
│  LAYER 1 · EXPERIENCE                                   │
│  Next.js SPA · Apigee X · Identity-Aware Proxy · CDN   │
├─────────────────────────────────────────────────────────┤
│  LAYER 2 · AGENT ORCHESTRATION                         │
│  LangGraph 0.2.x · Vertex AI Agent Builder             │
│  Auth → Policy → Retrieval → [HITL] → Synthesis → Audit│
├─────────────────────────────────────────────────────────┤
│  LAYER 3 · INTELLIGENCE                                 │
│  Neo4j Aura Enterprise · Vertex AI Search               │
│  text-embedding-004 · Vertex AI Pipelines · BigQuery    │
├─────────────────────────────────────────────────────────┤
│  LAYER 4 · INFRASTRUCTURE                              │
│  GKE Autopilot · VPC Service Controls · Cloud KMS       │
│  Chronicle SIEM · OPA v0.60 · Terraform 1.7.x           │
└─────────────────────────────────────────────────────────┘
```

**Multi-tenancy model:** One GCP project per enterprise client. No shared Neo4j instance. Cryptographic tenant isolation via dedicated Cloud KMS key ring per client. Cross-tenant access is structurally impossible by project boundary design.

---

## Key design decisions (ADRs)

| ADR | Decision | Rejected alternatives |
|-----|----------|-----------------------|
| 001 | **LangGraph** for agent orchestration | Temporal.io, custom FSM, CrewAI |
| 002 | **Neo4j Aura Enterprise 5.x** for graph store | Amazon Neptune, Spanner Graph |
| 003 | **text-embedding-004** (multilingual) as canonical embedding model | OpenAI text-embedding-3-large, E5-large-v2 |
| 004 | **OPA v0.60** with Rego policy bundles for access control | Casbin, custom IAM service, Cedar |
| 005 | **Chronicle SIEM** as forensic audit layer + BigQuery as analytical layer | BigQuery-only, Splunk, Elastic |
| 006 | **HITL as a LangGraph interrupt node**, not a UI feature | Post-synthesis feedback button, async-only queue |

Every ADR includes context, the decision, the alternatives rejected, and the architectural consequences. See the **Architecture** tab.

---

## What makes this architecture different

### 1. Compliance by structure, not policy document

GDPR and EU AI Act obligations are satisfied by architectural controls — not by a compliance document that sits next to the system.

- **GDPR Art. 17 erasure** is implemented as a **distributed saga** with explicit compensating transactions, an idempotency key per step, and Chronicle audit at every stage — not a synchronous cascade that cannot guarantee atomicity across Neo4j, Vertex AI Search, and BigQuery simultaneously.
- **EU AI Act Art. 14 human oversight** is a **LangGraph interrupt node** — non-bypassable by design, not a UI flag that a developer can omit.
- **GDPR Art. 5 minimisation** is enforced by the ontology schema: only properties defined in the schema can be written to the graph.

### 2. HITL is calibrated, not guessed

The original architecture used a single global confidence threshold of 0.85. This is architecturally naive for two reasons:

1. Uncalibrated cosine similarity scores are not probabilities (Guo et al., "On Calibration of Modern Neural Networks," ICML 2017)
2. A query about EU financial services consultant career history carries fundamentally different epistemic risk than a documentation search

KnowledgeFlow uses a **per-category threshold matrix** with Platt-scaled confidence scores, calibrated quarterly on labelled evaluation sets. Two categories have unconditional HITL regardless of confidence: cross-geo OPA override paths and departed employee knowledge nodes.

### 3. Failure modes are first-class architecture

The agent topology is not happy-path-only. Every LangGraph edge is a failure boundary. The **FMEA table** specifies failure mode, severity, detection mechanism, fallback behaviour, SLO impact, and recovery procedure for all 7 agent nodes. Key design choices:

- Audit Agent is non-bypassable: if Chronicle is unavailable, query delivery is held — not abandoned. No unaudited response is delivered.
- OPA failure is fail-closed: if the policy engine is unreachable, zero nodes are authorised.
- Neo4j timeout triggers ANN-only fallback with automatic HITL threshold lowering during the degradation window.

### 4. Ontology is the architecture

The knowledge graph has a formal property graph schema (ONT-01): `:Person`, `:Engagement`, `:Expertise`, `:Document` — each with typed property schemas, cardinality constraints, and an explicit index strategy (BTREE + FULLTEXT + RELATIONSHIP indexes).

Without a schema, GDPR erasure cascade is architecturally incomplete. Without an MDM authority model, the ingestion pipeline creates one node per source system rather than one node per real-world person. Both are addressed.

### 5. RLHF is specified, not asserted

The feedback loop uses **Direct Preference Optimisation** (Rafailov et al., NeurIPS 2023) rather than PPO-based RLHF — eliminating the separate reward model and reducing reward hacking risk. Four gates before any model version is promoted to production: win rate, BERTScore, bias parity across geo/role dimensions, and a 48-hour A/B test on 10% of live traffic.

---

## Governance model

KnowledgeFlow targets **EU AI Act Art. 6** (high-risk AI — employment context) and **Art. 52** (limited risk — AI disclosure) simultaneously.

Conformity assessment approach: self-assessment at pre-deployment stage per ISO/IEC 42001:2023. Third-party notified body assessment required at production deployment for EU clients under Art. 43(1).

| Article | Obligation | Status |
|---------|------------|--------|
| Art. 9 — Risk management | Continuous lifecycle risk system | ✅ Structural |
| Art. 10 — Data governance | Training/input data quality | ✅ Structural |
| Art. 12 — Record keeping | Automatic logging for post-hoc reconstruction | ✅ Structural |
| Art. 13 — Transparency | AI disclosure on every response | ✅ Structural |
| Art. 14 — Human oversight | Persons able to oversee and override | ✅ Structural |
| Art. 15 — Accuracy and robustness | SLOs + drift detection + RLHF | ✅ Structural |
| Art. 11 — Technical documentation | This portfolio + model registry + ADRs | 🔵 Designed |

---

## Economics (bottom-up)

Cost methodology: unit × quantity from GCP pricing calculator (April 2025). TCO includes infrastructure, HITL labour (15 min/review × 5% intercept rate × €80/hr blended), and engineering. **Not** a conflation of infrastructure cost with TCO.

| Tier | Users | Monthly infra | Full monthly TCO | Est. annual benefit* |
|------|-------|--------------|-----------------|----------------------|
| Pilot | 100 | €5,800 | €18,600 | €180K |
| Mid-scale | 1,000 | €14,200 | €34,700 | €1.8M |
| Enterprise | 5,000 | €38,900 | €76,400 | €9M |

**Unit economics:** Full TCO cost per query ranges from €0.135 (enterprise) to €0.364 (pilot). Value recovered per query (McKinsey productivity basis): ~€2.32. Break-even requires only 9% search-time reduction — well within the empirical evidence base for comparable interventions.

*\*Annual benefit is an architectural hypothesis based on McKinsey MGI (2012) search-time reduction research. Actual impact requires post-deployment A/B measurement. Presented as a ceiling to demonstrate economic viability, not as a commitment.*

---

## Implementation roadmap

Critical path: **P1.1 Identity federation → P1.2 GCP project → P1.3 OPA policy → P2.1 Ingestion → P2.3 Neo4j graph → P3.1 Agents → P3.2 HITL → P4.2 Pilot**

If identity federation slips by 2 weeks, first production query delivery slips by 2 weeks — every downstream phase is blocked.

| Phase | Weeks | Critical deliverable | Exit gate |
|-------|-------|---------------------|-----------|
| 1 — Foundation | 1–8 | Identity federation + OPA policy bundle v1 | First authenticated query reaches OPA · DPO sign-off on schema v1 |
| 2 — Capability | 9–18 | Ingestion pipeline + Neo4j graph population | 1,000+ nodes · Embedding pipeline SLA met |
| 3 — Agents | 19–28 | LangGraph state machine + HITL gate | 10 users querying · AG-02 threshold matrix validated |
| 4 — Production | 29–40 | Full rollout + RLHF cycle 1 | EU AI Act conformity self-assessment · NPS ≥ 35 |

---

## Portfolio contents

The interactive HTML document (`KnowledgeFlow_v2_Architecture.html`) contains:

| Tab | Contents |
|-----|----------|
| **Problem** | Business Motivation Model (ArchiMate 3.2 · TOGAF Phase A), stakeholder personas, vertical analysis, problem quantification |
| **Architecture** | C4-L1 System Context, C4-L2 Container Diagram, four-layer overview, ADRs 001–006 |
| **Ontology** | ONT-01 property graph schema, MDM authority model, entity resolution pipeline, GDPR erasure saga (ERR-01) |
| **Agents** | LangGraph state machine with failure edges, per-category HITL threshold matrix, agent FMEA, RLHF pipeline specification |
| **Governance** | EU AI Act risk tier mapping, GOV-01 conformity obligation table, GDPR six-principle grid, GOV-02 HITL intercept types, three-layer audit architecture |
| **Simulation** | Interactive knowledge graph (18 nodes, role/geo filters, query walkthrough, HITL routing demo) |
| **Economics** | Interactive bottom-up cost model (3 tiers), TCO derivation, unit economics, revenue bridge methodology |
| **Roadmap** | Dependency graph with critical path, four-phase implementation plan with stream decomposition |
| **Operations** | Eight operational scenarios, SLO definitions, C4-L3 ingestion pipeline component diagram, known limitations |

---

## Diagrams included

- **D1** — Business Motivation Model (ArchiMate 3.2 · Motivation Aspect)
- **D2** — Capability Map (ArchiMate 3.2 · TOGAF Phase B)
- **C4-L1** — System Context Diagram
- **C4-L2** — Container Diagram (all 22 containers, trust zones, multi-tenancy annotation)
- **C4-L3** — Component Diagram (ingestion pipeline internals)
- **AG-01** — LangGraph State Machine (full topology with failure edges and HITL loopback)
- **ONT-01** — Knowledge Graph Property Schema (ER diagram with typed properties and index strategy)
- **MDM-02** — Entity Resolution Pipeline
- **ERR-01** — GDPR Art.17 Erasure Saga (orchestration pattern with compensating transactions)
- **RM-DEPS** — Roadmap Dependency Graph with critical path

---

## Technology stack

```
Language / runtime    Node.js (Next.js SPA) · Python (agent pods)
Orchestration         LangGraph 0.2.x · Vertex AI Agent Builder
LLM                   Gemini 1.5 Pro (synthesis) · text-embedding-004 (embeddings)
Graph database        Neo4j Aura Enterprise 5.x (Cypher · APOC)
Vector search         Vertex AI Search (ANN · 3072-dim embeddings)
Policy engine         OPA (Open Policy Agent) v0.60 · Rego
Stream processing     Cloud Pub/Sub · Dataflow
ML platform           Vertex AI Pipelines · Vertex Model Registry
Analytical store      BigQuery · Dataplex
Audit / SIEM          Chronicle SIEM (YARA-L · 24-month hot retention)
Infrastructure        GKE Autopilot · VPC Service Controls · Cloud KMS (CMEK)
IaC / GitOps          Terraform 1.7.x · Cloud Build · Artifact Registry
API gateway           Apigee X · Identity-Aware Proxy
Observability         Cloud Trace (OpenTelemetry) · Cloud Monitoring · Looker
```

---

## Frameworks and standards

- **TOGAF ADM** Phases A–H (Architecture Development Method)
- **ArchiMate 3.2** (Motivation, Business, Application, Technology aspects)
- **C4 Model** (Simon Brown) — Levels 1, 2, and 3
- **EU AI Act** (Regulation 2024/1689) — Arts. 6, 9–15, 43, 52
- **GDPR** (Regulation 2016/679) — Arts. 5, 7, 12, 13, 17, 30
- **ISO/IEC 42001:2023** — AI Management System (conformity assessment basis)
- **OWASP LLM Top 10** — LLM01 (prompt injection) · LLM02 (insecure output handling) threat model

---

## Known limitations (honest architecture)

| Area | Limitation | Resolution path |
|------|-----------|-----------------|
| Graph scalability | Performance at 500K+ nodes not empirically validated | Phase 4 load test with synthetic dataset |
| RLHF sample size | 500+ preference pairs needed per training cycle — accumulation takes ~2 months at pilot scale | Red-teaming supplementation in early cycles |
| Implicit knowledge capture | Meeting discussions and verbal knowledge not yet captured | Phase 4: Google Meet transcript integration with explicit consent gating |
| Cross-language graph quality | text-embedding-004 MTEB validated; cross-language relationship quality in production unverified | Phase 3: multilingual held-out eval set |

---

## Citation methodology

All statistics are drawn from primary research. Secondary aggregator citations (e.g., "Gartner via [blog]") are not used. Statistics dated before 2020 are flagged with their collection year and an applicability note. Where projections are made from older baselines, the assumption is stated explicitly and challengeable.

Key references:
- McKinsey Global Institute, *The Social Economy*, 2012
- Deloitte Insights / HBR, *The Case for Investing in Knowledge Management*, 2019
- Forrester, *Enterprise Knowledge Management*, 2022
- Guo et al., *On Calibration of Modern Neural Networks*, ICML 2017
- Rafailov et al., *Direct Preference Optimization*, NeurIPS 2023
- Robinson, Webber & Eifrem, *Graph Databases*, O'Reilly 2nd ed., 2015
- Richardson, *Microservices Patterns*, Manning, 2018 (saga pattern)
- Muennighoff et al., *MTEB: Massive Text Embedding Benchmark*, EACL 2023

---

## Author

**Siddharth Rao**
TOGAF Certified Enterprise Architect · Google Cloud Certified Professional Architect · Machine Learning Engineer · Generative AI Leader

*This portfolio was developed as an enterprise architecture case study for a 12,000-person professional services firm across 40 offices and 6 continents, addressing knowledge management, agentic AI governance, and EU AI Act compliance at regulated-industry scale.*

---

*v2.1 · Architecture revised and critique-resolved · April 2025*
