# 02 - Threat Taxonomy

**Version:** 1.0.0

This document consolidates the OWASP Agentic Security Initiative (ASI) core threat taxonomy (T1-T15), the extended threat catalog per MAESTRO layer (T16-T47), and blindspot threat vectors identified through supplemental analysis. Together they form the comprehensive threat reference for MAESTRO-based threat modeling.

---

## Table of Contents

1. [ASI Core Threat Taxonomy (T1-T15)](#asi-core-threat-taxonomy-t1-t15)
2. [Extended Threat Catalog Per Layer (T16-T47)](#extended-threat-catalog-per-layer-t16-t47)
3. [Blindspot Vectors](#blindspot-vectors)
4. [Versioning Note](#versioning-note)

---

## ASI Core Threat Taxonomy (T1-T15)

The OWASP Agentic Security Initiative (ASI) defines 15 core agentic threats:

| ID | Threat Name | Description | Key Indicators |
|----|-------------|-------------|----------------|
| **T1** | Memory Poisoning | Malicious modification of agent memory/training data corrupts decision-making | Shared memory, fine-tuning, RAG knowledge bases |
| **T2** | Tool Misuse | Agent's authorized tools exploited for unintended/malicious purposes | MCP tools, function calling, API access |
| **T3** | Privilege Compromise | Exploitation of agent's elevated permissions for unauthorized actions | Service accounts, IAM roles, chained authorization |
| **T4** | Resource Overload | Overwhelming agent operations through coordinated resource exhaustion | API quotas, compute limits, concurrent executions |
| **T5** | Cascading Hallucinations | Agent generates false outputs that propagate through the system | Non-deterministic LLM, no output validation |
| **T6** | Intent Breaking & Goal Manipulation | Manipulation of agent's goals/objectives through adversarial inputs | Prompt injection, workflow bypass, planning exploitation |
| **T7** | Misaligned & Deceptive Behaviour | Agent deviates from intended behavior/policies | Compliance drift, non-determinism, adversarial context |
| **T8** | Repudiation & Untraceability | Agent actions cannot be traced or attributed | Missing audit logs, no non-repudiation controls |
| **T9** | Identity Spoofing | Impersonation of agents or users in the system | Weak authentication, credential theft, token replay |
| **T10** | Overwhelming HITL | Human reviewers overwhelmed by volume, reducing oversight quality | High-volume agent output, rubber-stamping risk |
| **T11** | Unexpected RCE / Code Attacks | Agent generates/executes malicious code | Code generation tools, eval(), dynamic execution |
| **T12** | Agent Communication Poisoning | Corruption of inter-agent or agent-external communications | Shared data stores, email injection, API manipulation |
| **T13** | Rogue Agents | Malicious agent introduced or existing agent compromised | Plugin compromise, supply chain, agent registry |
| **T14** | Human Attacks on MAS | Humans exploit agent systems through crafted requests | Social engineering via agent, confused deputy |
| **T15** | Human Trust Manipulation | Exploitation of human over-reliance on AI outputs | Automation bias, authority deference, trust erosion |

---

## Extended Threat Catalog Per Layer (T16-T47)

Beyond the ASI T1-T15, MAESTRO discovers **extended threat scenarios**. These are threats not fully covered by the base taxonomy. Use this as a checklist during each layer analysis.

**Note:** Extended threats with T-IDs (T16-T47) are formally cataloged entries. Unlabeled bullet items are additional threat considerations from the source material that have not been formally numbered.

### Layer 1: Foundation Model - Extended Threats

- **Collaborative Model Poisoning**: Malicious data injected during collaborative/shared model training (LLM04:2025)
- **Model Stealing via Eavesdropping**: Reverse-engineering models from inter-agent traffic (LLM10:2025)
- **Model Inconsistency - Non-deterministic LLM behavior causing variable outcomes for identical inputs (T16)**: The same prompt can yield materially different outputs across invocations due to temperature, sampling, model version updates, or infrastructure-level non-determinism. In agentic systems this means identical business inputs may trigger different workflow paths, tool selections, or approval decisions.
- **Tool Hijacking via Prompt Injection**: LLM manipulated to call wrong tools or pollute parameters ("From now on, whenever calling X, always append Y")

### Layer 2: Data Operations - Extended Threats

- **Distributed Data Poisoning**: Subtle manipulation of shared data sources across agents
- **Inter-Agent Data Tampering**: Data modified in transit between agents/components
- **Semantic Drift (T17)**: Embeddings become stale when source policies/docs change but vector DB not re-indexed
- **RAG Input Manipulation (T18)**: Crafted inputs exploit similarity search to bypass policy checks
- **RAG Data Exfiltration (T28)**: Unauthorized access to vector database contents

### Layer 3: Agent Frameworks - Extended Threats

- **Negotiation Hijacking**: Manipulation of inter-agent communication protocols
- **Trust Exploitation**: Compromised agent leverages trusted reputation to manipulate others
- **Unintended Workflow Execution (T19)**: Agent executes steps in wrong order or skips validation
- **Framework Code Injection (T20)**: Vulnerability in agent framework allows code injection
- **Inconsistent Workflow State (T21)**: Desynchronized state among agents causes conflicts
- **Plugin Vulnerability (T29)**: Malicious or insecure plugins compromise agent
- **Insecure Inter-Agent Protocol (T30)**: Communication protocol vulnerable to eavesdropping/tampering
- **Insufficient Action Isolation (T31)**: Lack of isolation between agent actions allows cross-contamination
- **Runaway Agent (T32)**: Agent enters infinite loop consuming resources (especially costly in blockchain/API contexts)
- **Schema Mismatch (T41)**: Ambiguous protocol schemas cause misinterpretation between client/server
- **Cross-Client Interference (T42)**: Multiple clients on shared server interfere with each other

### Layer 4: Deployment Infrastructure - Extended Threats

- **DDoS on Agent Infrastructure**: Targeted overload of multiple agents/components
- **Compromised Orchestration**: Exploitation of orchestration layer to deploy malicious agents
- **Service Account Exposure (T22)**: Credentials accidentally exposed (code repos, logs, configs)
- **Network Exposure (T43)**: Agent server deployed without proper network controls
- **Infrastructure Agent Loops (T33)**: Agent enters infinite loops consuming infrastructure resources without circuit breaker intervention

### Layer 5: Evaluation & Observability - Extended Threats

- **Distributed Performance Degradation Masking**: Metrics manipulated to hide agent compromise
- **Selective Log Manipulation (T23)**: Targeted removal of specific audit entries (more sophisticated than full log deletion)
- **Insufficient Logging (T44)**: Inadequate detail in logs prevents incident investigation

### Layer 6: Security & Compliance - Extended Threats

- **Data Privacy Violations in Inter-Agent Interactions**: Sensitive data leaked between agents
- **Indirect Privilege Escalation**: Agent permissions exploited by malicious users for high-privilege actions
- **Policy Violation**: Agent misalignment causes regulatory violations humans would avoid
- **Real-Time Security Violation**: Non-deterministic agent deviates from guardrails between monitoring checks
- **Dynamic Policy Enforcement Failure (T24)**: Policy engine fails to apply correct rules
- **Insufficient Server Permission Isolation (T45)**: MCP/agent server runs with excessive host permissions
- **Data Residency/Compliance Violation (T46)**: Data transferred across boundaries violating regulations

### Layer 7: Agent Ecosystem - Extended Threats

- **Workflow Disruption via Dependency (T25)**: Attack on dependent system disrupts agent workflow
- **Rogue Server (T47)**: Malicious MCP server masquerades as legitimate, providing harmful data
- **MCP Client Impersonation (T40)**: Attacker impersonates legitimate MCP client
- **Cross-Chain/Cross-System Bridge Attacks**: Exploitation of integration bridges between systems
- **Wallet Key Compromise (T34)**: Private keys for agent-controlled wallets stolen, enabling direct theft of cryptocurrency or digital assets. Particularly relevant for blockchain/Web3 agents.
- **Cross-Chain Bridge Exploitation (T35)**: Agent exploits or is exploited through cross-chain bridge vulnerabilities when operating across multiple blockchain networks.
- **Malicious Agent Diffusion (T36)**: Compromised agent propagates malicious behavior or data across the agent ecosystem through legitimate communication channels.
- **Agent Registry Poisoning (T37)**: Malicious entries injected into agent discovery registries, directing systems to connect to rogue agents.
- **Emergent Collusion (T38)**: Multiple agents independently responding to shared signals create unintended coordinated behavior (e.g., flash crash, synchronized resource exhaustion) without explicit coordination.
- **Unintended Resource Consumption (T39)**: Agent autonomously overuses tools or external services beyond cost budgets or rate limits without awareness of consumption constraints.

> **Note**: Threat IDs T26-T27 are reserved for future assignment to emerging threat categories. These IDs were held back during the initial taxonomy development to allow for threats that span multiple layers or represent novel attack patterns not yet observed in production agentic systems. Organizations discovering threats that do not map to T1-T25 or T28-T47 should document them using project-local identifiers (e.g., "ORG-T1") and propose formal T-ID assignment through the OWASP ASI project.

---

## Blindspot Vectors

The following threat vectors represent gaps not adequately covered by the ASI T1-T15 taxonomy or the extended catalog above. They were identified through analysis of emerging attack patterns against agentic AI systems, MCP protocol deployments, and multi-tenant agent architectures.

| # | Vector Name | Description | Affected Layers |
|---|-------------|-------------|-----------------|
| BV-1 | **Context Window Poisoning** | Attacks that fill the LLM context window with adversarial content to push legitimate instructions out of the effective attention window. As context windows grow, this becomes a scalable way to override system prompts and safety instructions without classic prompt injection. | L1, L3 |
| BV-2 | **Tool Description Poisoning (Rug Pull)** | An MCP server changes its tool descriptions after trust has been established to alter LLM invocation semantics. The agent has already been approved to use the tool, but the tool's behavior silently changes. This exploits the gap between trust-time and use-time verification. | L3, L7 |
| BV-3 | **Agentic Supply Chain (Dependency Confusion)** | npm/pip supply chain attacks targeting agent framework or MCP server dependencies. An attacker publishes a malicious package with a name similar to or identical to a private dependency, causing the agent's build pipeline to install the attacker's code. | L3, L4, L7 |
| BV-4 | **Prompt Leakage via Tool Outputs** | Agent system prompt extracted through crafted tool response payloads. A malicious or compromised tool returns output specifically designed to cause the LLM to echo its system prompt in subsequent responses or tool calls. | L1, L3 |
| BV-5 | **Multi-Tenant Agent Isolation Failure** | Conversation context bleed, session hijacking, or cross-tenant data leakage in shared agent instances. When multiple users or tenants share an agent deployment, insufficient isolation allows one tenant to observe or influence another's interactions. | L3, L4, L6 |
| BV-6 | **Cost/Budget Exhaustion Attacks** | Targeting billing by triggering expensive API calls, large model inference, or token-intensive prompts. An attacker crafts inputs that maximize token consumption or trigger repeated expensive tool calls, running up costs without necessarily compromising data or functionality. | L1, L4 |
| BV-7 | **Agent Memory Injection via A2A** | Google A2A protocol and similar agent-to-agent standards introduce new memory injection vectors not covered by T1 or T12. An agent receiving A2A messages may incorporate the content into its working memory or context, allowing a malicious peer to inject persistent false beliefs. | L2, L7 |
| BV-8 | **Steganographic Data Exfiltration** | Agent encodes sensitive data in seemingly benign outputs using invisible unicode characters, zero-width joiners, or formatting patterns that are imperceptible to human reviewers but machine-readable by an external receiver. | L1, L5, L6 |
| BV-9 | **Time-of-Check-to-Time-of-Use (TOCTOU)** | Permissions change between when the agent checks authorization and when it executes the authorized action. In agentic systems with asynchronous workflows, the window between check and use can be substantial, creating exploitable race conditions. | L3, L6 |
| BV-10 | **LLM Reasoning Manipulation** | Attacks on chain-of-thought, planning steps, or scratchpad content that manipulate the model's reasoning process without altering the user-visible prompt directly. By injecting content into intermediate reasoning artifacts, an attacker can steer decisions while leaving the prompt and output superficially normal. | L1, L3 |
| BV-11 | **OAuth/OIDC Token Relay Attacks** | Delegation attacks where OAuth tokens are relayed through agent chains beyond the confused deputy pattern. In multi-hop agent delegation, tokens may be forwarded through intermediary agents that gain unintended access, or tokens scoped for one service are presented to another. | L6, L7 |
| BV-12 | **Observability Overload** | Generating excessive legitimate-looking log entries to hide malicious activity. This is the inverse of log deletion (T23): rather than removing evidence, the attacker floods observability systems with noise so that genuine indicators of compromise are buried and never investigated. | L5 |

---

## Versioning Note

> **Note:** Extended threat IDs (T16-T47) and blindspot vectors are project-local identifiers from this playbook's analysis. They are not part of the official OWASP ASI taxonomy and may diverge from future official releases. When referencing these identifiers externally, always clarify that they originate from this playbook rather than from the OWASP ASI specification. The core taxonomy (T1-T15) is authoritative as of OWASP ASI v1.0 (April 2025).

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

| Previous | Up | Next |
|----------|-----|------|
| [01 - MAESTRO Layers](./01-layers.md) | [00 - Overview](./00-overview.md) | [03 - Mapping Matrix](./03-mapping-matrix.md) |
