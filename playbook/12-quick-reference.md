# 12 - Quick Reference

**Version:** 1.0.0

A condensed reference card for practitioners who are already familiar with the MAESTRO framework. Print this or keep it open alongside your working documents.

---

## Table of Contents

1. [Quick Reference Card](#quick-reference-card)
2. [STRIDE at a Glance](#stride-at-a-glance)
3. [Phase Checklist](#phase-checklist)
4. [Minimum Viable Threat Model (MVTM) Checklist](#minimum-viable-threat-model-mvtm-checklist)
5. [Decision Tree: Which Workflow?](#decision-tree-which-workflow)
6. [ASI Taxonomy Quick Map](#asi-taxonomy-quick-map)

---

## Quick Reference Card

```
MAESTRO LAYERS:
L1 Foundation Model    -> T1, T7           -> Model integrity, alignment, poisoning
L2 Data Operations     -> T1, T12          -> RAG, vectors, data pipeline integrity
L3 Agent Frameworks    -> T2, T5, T6       -> Tools, workflow, autonomy boundaries
L4 Deployment Infra    -> T3, T4, T13, T14 -> Runtime, networking, orchestration
L5 Eval & Observability -> T8, T10         -> Logging, HITL, anomaly detection
L6 Security & Compliance -> T3, T7         -> RBAC, policy, regulatory (VERTICAL)
L7 Agent Ecosystem     -> T9, T13, T14, T15 -> External systems, humans, other agents
Cross-Layer            -> T6, T12, T13, T15 -> Cascading, emergent, chained threats

4 AGENTIC FACTORS (check at every layer):
1. Non-Determinism    - Same input, different output
2. Autonomy           - Acts without per-step human approval
3. Identity Management - Agent credentials and permissions
4. Agent-to-Agent Comm - Inter-agent/tool communication channels

TOP CROSS-LAYER PATTERNS:
1. Hallucination -> RAG -> Tool Misuse (L1+L2+L3)
2. Framework Exploit -> Infra -> Compliance Bypass (L3+L4+L6)
3. Data Poisoning -> Agent Action -> Ecosystem Propagation (L2+L3+L7)
4. Log Manipulation -> Security Evasion -> Undetected Fraud (L3+L5+L6)
5. Cascading Trust Failure (All Layers)
6. Confused Deputy / Excessive Agency (L3+L6+L7)
7. HITL Overwhelm + Automation Bias (L5+L7)
```

---

## STRIDE at a Glance

| Category | Question to Ask | Agentic Relevance |
|----------|----------------|-------------------|
| **S**poofing | Can an attacker impersonate an agent, user, or service? | Agent identity management, credential theft |
| **T**ampering | Can an attacker modify data, prompts, or tool responses? | RAG poisoning, prompt injection, tool response manipulation |
| **R**epudiation | Can actions be performed without an audit trail? | Non-deterministic outputs, missing logging |
| **I**nformation Disclosure | Can sensitive data leak through agent outputs or logs? | Context window leakage, verbose error messages |
| **D**enial of Service | Can an attacker exhaust resources or block operations? | Unbounded token consumption, infinite loops |
| **E**levation of Privilege | Can an attacker gain unauthorized access via the agent? | Confused deputy, privilege escalation via tool use |

---

## Phase Checklist

| Phase | Key Activity | Key Output |
|-------|-------------|------------|
| 1. Business Context | Document what the system does and why it matters | Business Context Template (filled) |
| 2. Architecture Analysis | Map components to MAESTRO layers | Layer Mapping Template (filled) |
| 3. Threat Actors | Identify who would attack and why | Prioritized threat actor list |
| 4. Trust Boundaries | Draw boundaries between trust zones | Trust boundary diagram |
| 5. Assets & Flows | Identify what is valuable and how it moves | Asset inventory, data flow diagrams |
| 6. Threat Identification | Per-layer and cross-layer threat analysis | Threat Cards (filled) |
| 7. Mitigation Planning | Plan countermeasures for each threat | Mitigation Cards, remediation roadmap |
| 8. Code Validation | Validate mitigations against actual codebase (skip if no source access) | Code validation findings, implementation gap analysis |
| 9. Residual Risk Analysis | Assess remaining risk after mitigations | Residual risk register, risk acceptance docs |
| 10. Output Generation | Export final deliverable and archive | Threat model report, executive summary |

---

## Minimum Viable Threat Model (MVTM) Checklist

The bare minimum for a useful threat model. If any of these are missing, the threat model has significant gaps.

- [ ] Business context documented (what the system does, who uses it, why it matters)
- [ ] Architecture mapped to MAESTRO layers
- [ ] Top 3 threat actors identified
- [ ] Trust boundaries drawn
- [ ] Critical assets and flows identified
- [ ] Per-layer threats assessed (at minimum L1, L2, L3, L4, L6)
- [ ] Cross-layer threat patterns reviewed
- [ ] Top 5 mitigations planned
- [ ] Residual risks documented
- [ ] Output exported for review

**When to go beyond MVTM:** If the system is Critical criticality, handles Confidential/Restricted data, is externally facing, or operates with full autonomy, you should complete the full 10-phase process rather than stopping at the minimum.

---

## Decision Tree: Which Workflow?

Use this decision tree to select the appropriate analysis depth based on system characteristics.

```
START
  |
  v
Is the system business-critical or safety-related?
  |
  YES --> FULL ANALYSIS (all 10 phases, all layers, all cross-layer patterns)
  |
  NO
  |
  v
Does the system process Confidential or Restricted data?
  |
  YES --> FULL ANALYSIS
  |
  NO
  |
  v
Is the system externally facing (public users, partner APIs)?
  |
  YES --> STANDARD ANALYSIS (all 10 phases, focus on L1, L2, L3, L4, L6, L7)
  |
  NO
  |
  v
Does the system operate with full autonomy (no human-in-the-loop)?
  |
  YES --> STANDARD ANALYSIS
  |
  NO
  |
  v
Is this a multi-agent system?
  |
  YES --> STANDARD ANALYSIS (all layers including L7 and cross-layer)
  |
  NO
  |
  v
LIGHTWEIGHT ANALYSIS (MVTM checklist above, focus on L1, L2, L3, L4, L6)
```

**Analysis Depth Summary:**

| Workflow | Phases | Layers | Estimated Effort |
|----------|--------|--------|-----------------|
| **Full Analysis** | All 10 phases, thorough depth | All 7 layers + Cross-Layer | 2-5 days |
| **Standard Analysis** | All 10 phases, moderate depth | Priority layers (L1, L2, L3, L4, L6, L7) + Cross-Layer; all layers if multi-agent | 1-2 days |
| **Lightweight (MVTM)** | Phases 1-2 (light), 6-10 (focused) | L1, L2, L3, L4, L6 minimum | 2-4 hours |

---

## ASI Taxonomy Quick Map

| ASI ID | Threat Name | Primary Layer(s) |
|--------|------------|-------------------|
| T1 | Memory Poisoning | L2, Cross-Layer |
| T2 | Tool Misuse | L3, Cross-Layer |
| T3 | Privilege Compromise | L4, L6, Cross-Layer |
| T4 | Resource Overload | L4, Cross-Layer |
| T5 | Cascading Hallucinations | L1, L3, Cross-Layer |
| T6 | Intent Breaking & Goal Manipulation | L3, Cross-Layer |
| T7 | Misaligned & Deceptive Behaviour | L1, L6, Cross-Layer |
| T8 | Repudiation & Untraceability | L5, Cross-Layer |
| T9 | Identity Spoofing | L7, Cross-Layer |
| T10 | Overwhelming HITL | L5 |
| T11 | Unexpected RCE / Code Attacks | L1, L3, Cross-Layer |
| T12 | Agent Communication Poisoning | L2, Cross-Layer |
| T13 | Rogue Agents | L4, L7, Cross-Layer |
| T14 | Human Attacks on MAS | L4, L7, Cross-Layer |
| T15 | Human Trust Manipulation | L7, Cross-Layer |

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

| Previous | Up | Next |
|----------|-----|------|
| [11 - Framework Integration](./11-framework-integration.md) | [00 - Overview](./00-overview.md) | -- |

See also: [07 - Templates](./07-templates.md) | [Risk Scoring](../guides/risk-scoring.md) | [Single-Agent Systems](../guides/single-agent.md)
