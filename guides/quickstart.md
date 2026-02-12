# Quickstart: 30-Minute Minimum Viable Threat Model

**Version:** 1.0.0

Get a usable threat model for your agentic AI system in 30 minutes. This guide walks you through five focused steps, producing a minimum viable threat model that you can deepen later.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Step 1: Business Context (5 min)](#step-1-business-context-5-min)
3. [Step 2: Layer Mapping (5 min)](#step-2-layer-mapping-5-min)
4. [Step 3: Trust Boundaries (5 min)](#step-3-trust-boundaries-5-min)
5. [Step 4: Top Threats (10 min)](#step-4-top-threats-10-min)
6. [Step 5: Quick Mitigations (5 min)](#step-5-quick-mitigations-5-min)
7. [What You Have Now](#what-you-have-now)
8. [Next Steps](#next-steps)

---

## Prerequisites

Before starting, you need:

- **Understanding of your system architecture**: Know what components exist, how they connect, and what data flows between them. You do not need formal architecture diagrams, but you need to be able to describe your system.
- **Access to a MAESTRO-compatible tool** (optional): An MCP-based threat modeling server can automate much of this process. See the [MCP Integration Guide](mcp-integration.md) for guidance. Alternatively, you can use pen and paper, a whiteboard, or a simple text editor.
- **30 minutes of focused time**: This guide is designed to be completed in a single sitting without interruptions.

### If Using an MCP-Based Threat Modeling Tool

You can follow along with MCP tools if you have a threat modeling MCP server configured. Each step includes example tool calls. See the [MCP Integration Guide](mcp-integration.md) for setup guidance.

### If Working Manually

Use the templates below to fill in each section. Copy them into a document and work through them sequentially.

> **Working Without MCP:** Every step in this guide can be completed manually using the templates in [07 - Templates](../playbook/07-templates.md). The MCP tool references (shown in `monospace`) are optional accelerators — they automate template population but are not required. If you do not have an MCP server configured, simply fill in the corresponding template fields by hand. The threat model output is identical regardless of method.

---

## Step 1: Business Context (5 min)

**Goal**: Establish what the system does, who it serves, and what constraints it operates under.

Answer these five questions:

### 1.1 What does the system do?

Write a 2-3 sentence description of the system's primary function. Focus on the business outcome, not the technology.

**Template**:
> This system [does what] for [whom], handling [what kind of data]. It [key business value proposition].

**Example**:
> This system automates invoice processing for the finance team, handling confidential financial data including invoice amounts, vendor information, and payment records. It reduces manual review cycles with an AI-driven validation and approval pipeline.

### 1.2 Who uses it?

List the primary user groups and their roles.

| User Group | Role | Access Level |
|-----------|------|-------------|
| | | |

### 1.3 What data does it handle?

Classify the data the system processes.

| Data Type | Sensitivity | Examples |
|----------|------------|---------|
| | Public / Internal / Confidential / Restricted | |

### 1.4 What regulations apply?

List applicable compliance requirements (SOX, GDPR, HIPAA, PCI-DSS, FedRAMP, etc.). If unsure, note "To be determined" and flag for follow-up.

### 1.5 How critical is the system?

Rate the system on these dimensions:

| Dimension | Rating (Low/Medium/High/Critical) |
|----------|----------------------------------|
| Financial Impact | |
| System Criticality | |
| Data Sensitivity | |
| Reputational Impact | |

> Throughout this guide, **MCP Tool** references show the equivalent MCP server command for each step. These are optional — see the note above about working manually.

**MCP Tool**: `set_business_context(description)` followed by answering the generated clarification questions.

---

## Step 2: Layer Mapping (5 min)

**Goal**: Map your system's components to the 7 MAESTRO architectural layers.

MAESTRO defines 7 layers, each representing a distinct attack surface. For each layer, identify the specific technologies and components in your system.

### MAESTRO Layer Mapping Template

| Layer | Layer Name | Your System's Components | Notes |
|-------|-----------|-------------------------|-------|
| L1 | Foundation Model | Which LLM(s)? API or self-hosted? Fine-tuned? | e.g., "Cloud LLM API, no fine-tuning" |
| L2 | Data Operations | RAG? Vector DB? Training data? External data sources? | e.g., "Managed knowledge base with object-storage-stored SOPs" |
| L3 | Agent Frameworks | Which framework? MCP servers? Tools? Multi-agent? | e.g., "LangGraph agent, MCP server with 12 backend tools" |
| L4 | Deployment Infrastructure | Cloud provider? Compute? Storage? Networking? | e.g., "Serverless functions, document DB, object storage, event bus" |
| L5 | Evaluation and Observability | Logging? Monitoring? HITL review? Dashboards? | e.g., "Cloud monitoring, Streamlit dashboard for HITL" |
| L6 | Security and Compliance | AuthN/AuthZ? Secrets management? Policies? | e.g., "Identity provider with MFA, IAM roles, secrets manager" |
| L7 | Agent Ecosystem | External systems? Human users? Other agents? APIs? | e.g., "ERP system, email recipients, finance approvers" |

### Tips for Layer Mapping

- **Do not force components into layers**. Some components span multiple layers. Note them in each relevant layer.
- **L1 and L3 are most critical** for agentic AI systems. The foundation model and the agent framework are where AI-specific threats concentrate.
- **L4 applies to all systems** but carries additional weight when the infrastructure runs autonomous agents.
- **If a layer is empty**, note "Not applicable" and explain why. An empty layer is a valid finding.

**MCP Tools**: `add_component(name, type)`, `add_connection(source, destination, protocol)`, `add_data_store(name, type, classification)`

---

## Step 3: Trust Boundaries (5 min)

**Goal**: Identify where data crosses trust boundaries in your system.

### 3.1 Define Trust Zones

A trust zone is a group of components that share the same trust level. Draw a box (conceptual or literal) around each group.

| Trust Zone | Trust Level | Components in Zone |
|-----------|------------|-------------------|
| External/Internet | Untrusted | End users, external APIs, email senders |
| Presentation Tier | Low | Web UI, mobile app, API gateway |
| Processing Tier | Medium | Agent framework, MCP server, compute functions |
| Data Tier | High | Databases, object storage, secrets manager |
| External Backend | High | ERP, CRM, legacy systems (independently managed) |

### 3.2 Identify Boundary Crossings

For each connection that crosses a trust zone boundary, document the crossing.

| From Zone | To Zone | Data Crossing | Current Controls |
|----------|---------|--------------|-----------------|
| External | Presentation | User requests | TLS, authentication |
| Presentation | Processing | Authenticated requests | JWT validation, input validation |
| Processing | Data | Read/write operations | IAM policies, encryption |
| Processing | External Backend | API calls | Credentials from secrets manager, HTTPS |

### 3.3 Flag High-Risk Crossings

Any boundary crossing where:
- Data sensitivity increases (e.g., processing tier accessing the data tier)
- Trust level decreases (e.g., sending data to an external system)
- Authentication changes (e.g., user auth becomes service account auth)

These crossings deserve the most attention in Step 4.

**MCP Tools**: `add_trust_zone(name, trust_level, description)`, `add_trust_boundary(name, type, controls)`, `add_crossing_point(name, description)`

---

## Step 4: Top Threats (10 min)

**Goal**: For each MAESTRO layer with components, identify the top 2-3 threats.

This is the most time-intensive step. Focus your effort on **L1 (Foundation Model)**, **L3 (Agent Frameworks)**, **L4 (Deployment Infrastructure)**, and **L6 (Security and Compliance)** -- these are where the highest-impact threats typically occur in agentic AI systems.

### Layer-by-Layer Threat Checklist

For each layer, scan the following common threats and select the ones most relevant to your system.

#### L1: Foundation Model Threats

| Threat | Description | Relevant? |
|--------|------------|-----------|
| Prompt Injection | Adversarial inputs cause the model to deviate from instructions | |
| Hallucination / Data Fabrication | Model generates plausible but false information | |
| Model Alignment Failure | Model behavior drifts from intended purpose over time | |
| Training Data Poisoning | Compromised training data affects model outputs (primarily self-hosted) | |

#### L2: Data Operations Threats

| Threat | Description | Relevant? |
|--------|------------|-----------|
| RAG Poisoning | Malicious content injected into the knowledge base | |
| Data Exfiltration via Queries | Adversarial queries extract sensitive data from the knowledge base | |
| Stale or Incorrect Data | Outdated knowledge base leads to wrong decisions | |

#### L3: Agent Frameworks and Orchestration Threats

| Threat | Description | Relevant? |
|--------|------------|-----------|
| Tool Misuse (ASI T2) | Agent invokes tools in unintended or harmful ways | |
| Goal/Intent Manipulation (ASI T6) | Adversary manipulates the agent's planning or goal orientation | |
| Excessive Agency (ASI T2) | Agent has more permissions than necessary for its task | |
| MCP Tool Description Poisoning | Misleading tool descriptions cause incorrect tool selection | |

#### L4: Deployment Infrastructure Threats

| Threat | Description | Relevant? |
|--------|------------|-----------|
| Privilege Escalation (ASI T3) | Compromised component escalates to broader access | |
| Resource Exhaustion (ASI T4) | DoS attack against compute, storage, or API quotas | |
| Infrastructure Tampering (ASI T3) | Modification of deployment configurations or code | |

#### L5: Evaluation and Observability Threats

| Threat | Description | Relevant? |
|--------|------------|-----------|
| Audit Trail Gaps (ASI T8) | Insufficient logging makes incidents untraceable | |
| HITL Overwhelm (ASI T10) | Human reviewers cannot keep up with agent output volume | |
| Monitoring Evasion | Agent behavior appears normal while deviating from policy | |

#### L6: Security and Compliance Threats

| Threat | Description | Relevant? |
|--------|------------|-----------|
| Privilege Compromise (ASI T3) | Over-scoped IAM roles or missing RBAC | |
| Credential Exposure | Secrets leaked in logs, code, or agent outputs | |
| Compliance Drift (ASI T7) | System gradually deviates from regulatory requirements | |
| Missing Separation of Duties | Same role can initiate and approve sensitive operations | |

#### L7: Agent Ecosystem Threats

| Threat | Description | Relevant? |
|--------|------------|-----------|
| Identity Spoofing (ASI T9) | Attacker impersonates a legitimate user or system | |
| Human Trust Manipulation (ASI T15) | Users over-rely on AI outputs (automation bias) | |
| External System Compromise | Compromised external system feeds malicious data to agent | |

#### Cross-Layer Threats

| Threat | Description | Relevant? |
|--------|------------|-----------|
| Cascading Trust Failure (Cross-Layer) | Compromise in one layer cascades across multiple layers | |
| Confused Deputy | Agent uses its privileges on behalf of an unauthorized user | |
| Data Leakage Cascade | Sensitive data flows across trust boundaries without protection | |

### Recording Threats

For each relevant threat, record:

1. **What**: Brief description of the threat
2. **Who**: Which threat actor would exploit this (insider, external, automated)?
3. **How**: What is the attack path?
4. **Impact**: What happens if the threat is realized? (Financial, operational, compliance, reputational)
5. **Severity**: High / Medium / Low

**MCP Tools**: `add_threat(source, prerequisites, action, impact, tags)`, `list_threats()`

---

## Step 5: Quick Mitigations (5 min)

**Goal**: For each high or critical threat from Step 4, identify one preventive and one detective control.

### Mitigation Template

For each high-severity threat:

| Threat | Preventive Control | Detective Control |
|--------|-------------------|-------------------|
| e.g., Prompt Injection | Input validation, guardrails on model outputs | Monitor for unusual output patterns |
| e.g., Tool Misuse | Tool allowlisting, parameter bounds checking | Audit log review of tool invocations |
| e.g., HITL Overwhelm | Batch size limits, mandatory review time per case | Spot-check sampling of approved decisions |

### Common Mitigation Patterns for Agentic AI

| Threat Category | Common Preventive Controls | Common Detective Controls |
|----------------|---------------------------|--------------------------|
| Model-layer (L1) | Guardrails, output validation, deterministic checks | Output anomaly detection, confidence scoring |
| Data-layer (L2) | Access controls on knowledge base, input sanitization | Knowledge base integrity monitoring, versioning |
| Agent-layer (L3) | Tool allowlisting, parameter validation, HITL approval | Tool invocation auditing, behavioral monitoring |
| Infrastructure (L4) | Least-privilege IAM, network segmentation, code signing | Audit logging, threat detection, config drift detection |
| Observability (L5) | Immutable audit logs, structured logging | Automated log analysis, alerting on gaps |
| Security (L6) | MFA, RBAC, secrets rotation, separation of duties | Access reviews, compliance scanning |
| Ecosystem (L7) | Authentication, input validation from external sources | Anomaly detection on external data feeds |

**MCP Tools**: `add_mitigation(content)`, `link_mitigation_to_threat(mitigation_id, threat_id)`

---

## What You Have Now

After 30 minutes, you have a **minimum viable threat model** containing:

1. A documented business context with data sensitivity and regulatory scope
2. A MAESTRO layer mapping showing where your components sit architecturally
3. Trust boundaries identifying your highest-risk data crossings
4. A prioritized list of 10-15 threats focused on the most critical layers
5. At least one preventive and one detective control for each high-severity threat

This is not a complete threat model. It is a starting point that captures the most important risks and gives your team actionable items to address immediately.

---

## Next Steps

### Immediate (This Week)

- **Export your threat model**: If using the MCP server, call `export_comprehensive_threat_model` to save it. If working manually, save your document in version control alongside your code.
- **Assign owners**: Each high-severity threat should have a named owner responsible for implementing its mitigations.
- **Schedule a deeper review**: Block 2-4 hours within the next sprint to expand the threat model.

### Short Term (Next Sprint)

- **Complete all MAESTRO layers**: Go deeper on layers you skimmed in the quickstart.
- **Add asset flow analysis**: Map how sensitive data flows through the system (Phase 5 in the full methodology).
- **Run code validation**: If you have an existing codebase, use the `validate_security_controls` tool to check which mitigations are already implemented.
- **Review with the security team**: Present your threat model for feedback and additional threat identification.

### Ongoing

- **Re-run after architecture changes**: Any significant change to components, data flows, or trust boundaries should trigger a threat model update.
- **Track mitigation implementation**: Use the threat model as a living document that reflects which mitigations are complete, in progress, or not started.
- **Evolve with the codebase**: Store the `.threatmodel` directory in version control and update it as part of your development lifecycle.

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

## Navigation

- [MCP Integration Guide](mcp-integration.md)
- [Example: Multi-Agent DevOps Deployment](../examples/devops-deployment.md)
- [Example: Generic RAG Agent](../examples/generic-rag-agent.md)
- [Playbook Overview](../playbook/00-overview.md)
- [Glossary](../GLOSSARY.md)
