# 08 - Threat Identification Checklists

**Version:** 1.0.0

---

Run through these checklists for each engagement. Each section corresponds to a MAESTRO layer. Complete the per-layer checklists first, then finish with the cross-layer checklist to catch emergent and cascading threats.

---

## Table of Contents

1. [Layer 1: Foundation Model](#layer-1-foundation-model)
2. [Layer 2: Data Operations](#layer-2-data-operations)
3. [Layer 3: Agent Frameworks](#layer-3-agent-frameworks)
4. [Layer 4: Deployment Infrastructure](#layer-4-deployment-infrastructure)
5. [Layer 5: Evaluation & Observability](#layer-5-evaluation--observability)
6. [Layer 6: Security & Compliance](#layer-6-security--compliance)
7. [Layer 7: Agent Ecosystem](#layer-7-agent-ecosystem)
8. [Cross-Layer](#cross-layer)
9. [Blindspot Vectors Checklist](#blindspot-vectors-checklist)

---

## Layer 1: Foundation Model

- [ ] Prompt injection vectors (direct and indirect)
- [ ] Hallucination risk in critical decisions
- [ ] Model alignment with business rules
- [ ] Non-deterministic output consistency
- [ ] Model poisoning (if fine-tunable)
- [ ] Collaborative model poisoning via shared training data (LLM04:2025)
- [ ] Model inconsistency / non-deterministic behavior across invocations (T16)
- [ ] Model stealing via inference patterns
- [ ] Model stealing via eavesdropping on inter-agent traffic (LLM10:2025)
- [ ] Tool hijacking via prompt manipulation
- [ ] Parameter pollution attacks

## Layer 2: Data Operations

- [ ] RAG knowledge base poisoning
- [ ] Distributed data poisoning across shared data sources
- [ ] Semantic drift in embeddings (stale policies) (T17)
- [ ] RAG input manipulation for policy bypass (T18)
- [ ] RAG data exfiltration / unauthorized vector DB access (T28)
- [ ] Inter-agent data tampering in transit
- [ ] Shared memory poisoning between agents
- [ ] Source document integrity (object storage, databases)
- [ ] Email/external data injection into pipeline

## Layer 3: Agent Frameworks

- [ ] MCP tool misuse / unauthorized tool invocation
- [ ] Intent breaking / goal manipulation
- [ ] Workflow bypass (skipping validation steps)
- [ ] Unintended workflow execution / wrong-order steps (T19)
- [ ] Inconsistent workflow state among agents (T21)
- [ ] Framework code injection vulnerabilities (T20)
- [ ] Plugin/extension compromise (T29)
- [ ] Inter-agent protocol security
- [ ] Insecure inter-agent protocol / eavesdropping and tampering (T30)
- [ ] Insufficient action isolation / cross-contamination (T31)
- [ ] Negotiation hijacking of inter-agent communication
- [ ] Trust exploitation by compromised agent leveraging reputation
- [ ] Schema mismatch between client/server (T41)
- [ ] Cross-client interference on shared servers (T42)
- [ ] Runaway agent / infinite loop risk (T32)

## Layer 4: Deployment Infrastructure

- [ ] Over-privileged IAM roles / service accounts
- [ ] Service account credential exposure (T22)
- [ ] Network exposure of agent servers (T43)
- [ ] DDoS / resource overload on compute (targeted multi-agent)
- [ ] Orchestration layer compromise (event buses, workflow engines, etc.)
- [ ] Compromised orchestration deploying malicious agents
- [ ] Container/serverless function code signing
- [ ] Missing VPC isolation
- [ ] Infrastructure agent loops / infinite recursion (T33)

## Layer 5: Evaluation & Observability

- [ ] Audit trail completeness and immutability
- [ ] Non-repudiation for human approvals
- [ ] Selective log manipulation risk (T23)
- [ ] Insufficient logging detail preventing investigation (T44)
- [ ] HITL reviewer capacity vs agent volume
- [ ] Behavioral drift detection capability
- [ ] Anomaly detection on agent outputs
- [ ] Performance degradation masking
- [ ] Distributed performance degradation masking across agents

## Layer 6: Security & Compliance

- [ ] RBAC implementation and enforcement
- [ ] Separation of duties (dev/ops/finance)
- [ ] Authorization propagation through agent to backend
- [ ] Indirect privilege escalation via agent permissions
- [ ] Regulatory compliance (SOX, GDPR, HIPAA, etc.)
- [ ] Dynamic policy enforcement failure (T24)
- [ ] Runtime guardrails for agent behavior
- [ ] Real-time security violation between monitoring checks
- [ ] Insufficient server permission isolation (T45)
- [ ] Data residency / compliance violation (T46)
- [ ] Data privacy violations in inter-agent interactions
- [ ] Secrets rotation and management

## Layer 7: Agent Ecosystem

- [ ] Identity spoofing of agents or users (T9)
- [ ] Human trust manipulation / automation bias (T15)
- [ ] Rogue server in MCP ecosystem (T47)
- [ ] MCP client impersonation (T40)
- [ ] Malicious agent diffusion / rogue agent spreading corruption
- [ ] External system dependency exploitation
- [ ] Workflow disruption via dependency attack (T25)
- [ ] Agent-to-agent trust assumptions
- [ ] Third-party tool/API supply chain risk
- [ ] Cross-chain / cross-system bridge attacks
- [ ] Wallet/private key compromise for agent-controlled assets (T34)
- [ ] Cross-chain/cross-system bridge exploitation (T35)
- [ ] Malicious agent diffusion through ecosystem (T36)
- [ ] Agent registry poisoning / rogue agent discovery (T37)
- [ ] Emergent collusion from shared signal response (T38)
- [ ] Unintended resource consumption beyond budget (T39)

## Cross-Layer

- [ ] Cascading trust failure paths
- [ ] Confused deputy / excessive agency patterns (T2, T3)
- [ ] Privilege chain abuse across layers (T3)
- [ ] Data leakage cascade paths
- [ ] Hallucination propagation through system (T5)
- [ ] Feedback loop manipulation
- [ ] Temporal/timing attack vectors
- [ ] Planning and reflection exploitation
- [ ] RCE/Code execution chains across layers (T11)
- [ ] Context window overflow/poisoning attacks
- [ ] Tool description manipulation (rug pull)

## Blindspot Vectors Checklist

These vectors represent gaps not adequately covered by the core ASI taxonomy (T1-T15) or the extended catalog (T16-T47). Review these after completing per-layer and cross-layer checklists to ensure emerging and non-obvious attack surfaces are addressed.

- [ ] Context window poisoning / overflow to push out safety instructions (BV-1)
- [ ] Tool description poisoning / rug pull after trust establishment (BV-2)
- [ ] Agentic supply chain / dependency confusion (BV-3)
- [ ] Prompt leakage via tool outputs (BV-4)
- [ ] Multi-tenant agent isolation failure (BV-5)
- [ ] Cost/budget exhaustion attacks (BV-6)
- [ ] Agent memory injection via A2A (BV-7)
- [ ] Steganographic data exfiltration (BV-8)
- [ ] Time-of-check-to-time-of-use (TOCTOU) permission race conditions (BV-9)
- [ ] LLM reasoning manipulation (BV-10)
- [ ] OAuth/OIDC token relay attacks (BV-11)
- [ ] Observability overload / log flooding (BV-12)

---

## Navigation

| Previous | Up | Next |
|----------|-----|------|
| [07 - Templates](./07-templates.md) | [00 - Overview](./00-overview.md) | [09 - Mitigation Catalog](./09-mitigation-catalog.md) |

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*
