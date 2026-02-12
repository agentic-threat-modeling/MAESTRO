# 00 - Overview

**Version:** 1.0.0

**Reference for Repeatable Agentic AI Threat Modeling**

Based on: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide v1.0 (April 2025)
Framework: MAESTRO (Multi-Agent Environment, Security, Threat, Risk, and Outcome)
Source: Cloud Security Alliance + OWASP Agentic Security Initiative (ASI)

---

## Table of Contents

1. [Framework Overview](#framework-overview)
2. [Scope and Audience](#scope-and-audience)
3. [Known Limitations of MAS Systems](#known-limitations-of-mas-systems)
4. [When to Use MAESTRO](#when-to-use-maestro)
5. [Companion Documents](#companion-documents)

---

## Framework Overview

MAESTRO is a **layered architectural methodology** for threat modeling multi-agent and agentic AI systems. It complements (does not replace) the OWASP ASI threat taxonomy by providing a structured way to analyze threats at each architectural layer and discover **cross-layer threats** that emerge from component interactions.

**Key Principles:**
- Analyze threats at each of 7 architectural layers independently
- Then analyze cross-layer threats that span multiple layers
- Map all threats to the ASI taxonomy (T1-T15) where applicable
- Discover **extended threat scenarios** beyond the base taxonomy
- Consider 4 agentic risk factors at every layer
- Works for both single-agent and multi-agent systems

---

## Scope and Audience

This playbook is designed for two primary audiences:

### Security Engineers
- Application security professionals conducting threat models on AI-powered systems
- Infrastructure security teams evaluating deployment architectures for agentic workloads
- GRC (Governance, Risk, and Compliance) analysts mapping agentic AI risks to regulatory frameworks (SOX, GDPR, HIPAA, etc.)
- Red team operators building attack scenarios against multi-agent systems

### AI/ML Engineers
- Developers building agentic applications using frameworks such as Strands, LangChain, CrewAI, AutoGen, and similar
- ML engineers responsible for model selection, fine-tuning, and alignment in agentic contexts
- Platform engineers designing MCP server architectures, tool registries, and agent orchestration layers
- DevOps/MLOps teams deploying and operating agent infrastructure in production

Both audiences benefit from the structured, layer-by-layer approach that MAESTRO provides. Security engineers gain AI-specific context they may lack; AI/ML engineers gain a systematic threat analysis methodology they can apply without deep security specialization.

---

## Known Limitations of MAS Systems

Multi-Agent Systems (MAS) introduce categories of risk that are absent or minimal in traditional software and even in single-model AI deployments. Understanding these inherent limitations is a prerequisite for effective threat modeling. The following limitations are drawn from and aligned with OWASP and CSA guidance:

1. **Expanded Attack Surface** -- Every agent, tool, communication channel, data store, and orchestration layer is an independent attack target. The total attack surface of a MAS grows combinatorially, not linearly, with the number of components.

2. **Trust, Bias, and Adversarial Exploitation** -- Agents inherit biases from their foundation models and training data. Adversaries can exploit these biases to manipulate agent decisions. Trust relationships between agents can be weaponized: a compromised agent that is trusted by peers can propagate malicious influence across the system.

3. **Coordination Failures** -- Multi-agent coordination depends on shared protocols, shared state, and timing assumptions. Race conditions, deadlocks, and inconsistent state are not just reliability problems -- they are exploitable security vulnerabilities when an adversary can influence timing or ordering.

4. **Inability to Verify Decision Lineage** -- When multiple agents collaborate to produce an outcome, tracing which agent contributed which reasoning step, and whether that step was legitimate, becomes extremely difficult. This is especially problematic for compliance-sensitive decisions where auditability is required.

5. **Man-in-the-Middle (MITM) Between Agents** -- Inter-agent communication channels (including MCP tool calls, A2A protocol messages, and shared memory stores) are susceptible to interception, replay, and modification. Unlike human-to-human communication, agents cannot detect social cues that indicate impersonation.

6. **Lack of Accountability** -- When an agent makes a harmful decision, accountability is diffused across the model provider, the framework developer, the tool author, the deployer, and the user who initiated the request. MAS architectures exacerbate this by adding inter-agent delegation chains that further obscure responsibility.

7. **Identity Sprawl** -- Each agent may possess its own credentials, service accounts, API keys, and OAuth tokens. In a MAS with dozens of agents, managing and rotating these identities becomes a significant operational burden, and credential compromise for any single agent can cascade through trust relationships.

---

## When to Use MAESTRO

MAESTRO is applicable to any system that matches one or more of the following criteria:

- Any system with an LLM/foundation model making autonomous decisions
- Systems with tool use (MCP, function calling, API access)
- RAG-based systems with knowledge retrieval
- Human-in-the-loop AI workflows
- Multi-agent orchestration systems

If your system is a simple stateless LLM API call with no tools, no memory, and no autonomy, MAESTRO is likely overkill. For anything beyond that threshold, the layered analysis approach will surface threats that traditional methodologies miss.

---

## Regulatory and Standards Alignment

MAESTRO-based threat models can support compliance with the following regulatory frameworks and standards. While MAESTRO is not a compliance framework, its structured layer analysis produces artifacts that map to regulatory requirements.

| Framework / Standard | Relevance to Agentic AI | MAESTRO Alignment |
|---------------------|------------------------|-------------------|
| **EU AI Act** (2024) | Classifies AI systems by risk level; high-risk systems require conformity assessments, risk management, human oversight, robustness, and transparency | MAESTRO Phases 1 (risk classification), 5 (asset flows for transparency), 7 (mitigation planning for conformity), 9 (residual risk for risk management). L5 (observability for human oversight), L6 (compliance controls) |
| **NIST AI RMF 1.0** (2023) | Provides a framework for managing AI risks across Govern, Map, Measure, Manage functions | MAESTRO's 10-phase process maps to NIST AI RMF: Phase 1 → Govern, Phase 2 → Map, Phases 3-6 → Measure, Phases 7-10 → Manage |
| **ISO/IEC 42001** (2023) | AI management system standard; requires risk assessment, AI system lifecycle management, and responsible AI practices | MAESTRO provides the threat identification and risk assessment inputs required by ISO 42001 Clause 6.1 (Actions to address risks) |
| **ISO/IEC 23894** (2023) | Guidance on AI risk management; extends ISO 31000 to AI-specific risks | MAESTRO's layered threat analysis aligns with ISO 23894's requirement for systematic AI risk identification. The 4 agentic risk factors address AI-specific risk categories |
| **NIST CSF 2.0** (2024) | Cybersecurity framework with Govern, Identify, Protect, Detect, Respond, Recover functions | L5 (Eval & Observability) → Detect/Identify; L6 (Security & Compliance) → Protect/Govern; Phase 7 mitigations → Protect/Respond/Recover |

> **Note**: These mappings are high-level guidance. Organizations should consult legal and compliance specialists to determine specific regulatory obligations. MAESTRO outputs support but do not guarantee regulatory compliance.

---

## Companion Documents

- [Glossary](../GLOSSARY.md) -- Definitions of key terms used throughout the playbook
- [Documentation License](../LICENSE-DOCS.md) -- Licensing terms for this documentation set

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

| Previous | Up | Next |
|----------|-----|------|
| -- | -- | [01 - MAESTRO Layers](./01-layers.md) |
