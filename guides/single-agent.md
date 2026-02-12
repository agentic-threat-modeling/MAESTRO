# MAESTRO for Single-Agent Systems

**Version:** 1.0.0

Guidance for applying the MAESTRO threat modeling framework to systems with a single AI agent rather than a multi-agent system (MAS). MAESTRO was designed with MAS in mind, but its layered approach is equally valuable for single-agent systems -- with some adjustments.

---

## All Seven Layers Still Apply

A common misconception is that single-agent systems only need a subset of MAESTRO layers. In fact, **all seven layers remain relevant**:

| Layer | Relevance to Single-Agent Systems |
|-------|----------------------------------|
| **L1 - Foundation Model** | Fully applies. The foundation model has the same integrity, alignment, and poisoning risks regardless of how many agents use it. |
| **L2 - Data Operations** | Fully applies. RAG pipelines, vector databases, and data sources carry the same risks. |
| **L3 - Agent Frameworks** | Fully applies. Tool definitions, MCP servers, workflow logic, and autonomy boundaries are present in any agent system. |
| **L4 - Deployment Infrastructure** | Fully applies. Compute, networking, storage, and IAM are always present. |
| **L5 - Evaluation & Observability** | Fully applies. Logging, monitoring, and human-in-the-loop interfaces are equally important. |
| **L6 - Security & Compliance** | Fully applies. Authentication, authorization, secrets management, and compliance requirements do not change. |
| **L7 - Agent Ecosystem** | **Simplified but not empty.** The agent still interacts with external APIs, MCP servers, databases, human users, and third-party services. See below. |

### L7 Is Never Truly Empty

Even a single agent operates within an ecosystem. It calls external APIs. It uses MCP servers that connect to databases and services. It receives input from human users. It may integrate with third-party SaaS platforms. All of these are L7 concerns.

What L7 does NOT include for single-agent systems:
- Other AI agents
- Inter-agent orchestration protocols
- Agent-to-agent trust relationships

What L7 still includes:
- External API integrations and their trust assumptions
- MCP server connections and the services they expose
- Human user interactions and social engineering risks
- Third-party service dependencies and supply chain risks
- Callback mechanisms and webhook endpoints

---

## What to Skip

The following threat patterns are **MAS-specific** and can be safely deprioritized (not analyzed) for single-agent systems:

### Inter-Agent Communication Threats
- **ASI T12 (Agent Communication Poisoning)**: No agent-to-agent communication channels exist, so the multi-agent aspects of this threat can be deprioritized.
- Man-in-the-middle attacks between agents
- Agent message replay and spoofing between cooperating agents
- Protocol-level vulnerabilities in agent communication frameworks (A2A, etc.)

### Agent Collusion
- **ASI T38 (Emergent Collusion)**: The multi-agent emergent behavior and collusion aspects of T38 are not relevant for single agents. T15 (Human Trust Manipulation / automation bias) remains a valid single-agent threat -- see the "What to Emphasize" section.
- Emergent behaviors arising from agent interactions
- Coordinated manipulation by multiple compromised agents
- Unintended cooperation patterns between agents

### Distributed State Synchronization
- **ASI T21 (Inconsistent Workflow State)**: Distributed state synchronization issues (T21) are not relevant when only one agent exists. T14 (Human Attacks on MAS) remains relevant for single agents -- humans can craft adversarial requests against a single agent.
- State consistency attacks across agent instances
- Race conditions in shared agent memory
- Conflicting agent decisions based on stale state

**Important caveat:** If the single agent has multiple concurrent invocations (e.g., a serverless agent handling many requests simultaneously), some distributed state concerns may resurface. Evaluate whether shared resources (database tables, storage buckets, cached state) can be manipulated through concurrent access.

---

## What to Emphasize

For single-agent systems, the following areas deserve **increased focus** because they represent the primary attack surface:

### L1 - Foundation Model Integrity (Elevated Priority)

With only one agent, the foundation model is a single point of failure. There is no diversity of models to provide cross-checking or redundancy.

- **Prompt injection**: The primary attack vector for most single-agent systems. Direct and indirect injection should be thoroughly analyzed.
- **Model poisoning and manipulation**: If the model is fine-tuned, the training pipeline is a critical target.
- **Hallucination impact**: No second agent to validate outputs. Hallucinated data flows directly to downstream systems.

### L3 - Tool Access and Framework Security (Elevated Priority)

The agent's tools define its blast radius. A compromised agent with broad tool access can cause maximum damage.

- **Excessive agency**: Does the agent have access to tools it does not need? Apply least-privilege to tool registration.
- **Tool input validation**: Can the agent be tricked into passing malicious inputs to tools?
- **MCP server security**: Each MCP server is an extension of the agent's capabilities. Secure them as you would secure the agent itself.
- **Autonomy boundaries**: What can the agent do without human approval? Where are the guardrails?

### L6 - Authorization Propagation (Elevated Priority)

The single agent acts on behalf of users and systems. Its authorization model must be carefully designed to prevent confused deputy attacks.

- **Credential scope**: Does the agent use a single set of credentials for all operations, or are credentials scoped per-user and per-operation?
- **Privilege escalation paths**: Can the agent be prompted to perform actions that exceed the requesting user's authorization?
- **Secrets management**: How are API keys, database credentials, and tokens stored, rotated, and accessed?
- **Audit trail**: Can every action be traced back to the originating user request?

---

## Simplified Process

For single-agent systems, several phases of the MAESTRO process can be streamlined without sacrificing coverage:

### Phase 3: Threat Actors (Simplified)

For MAS, threat actors include compromised peer agents, malicious orchestrators, and adversarial agents in the ecosystem. For single-agent systems, the threat actor list is shorter:

- **External attackers** (prompt injection, API abuse)
- **Malicious insiders** (users with legitimate access who abuse the agent)
- **Compromised upstream services** (poisoned data sources, malicious API responses)
- **Supply chain attackers** (compromised dependencies, malicious MCP servers)

You can typically identify and document these in 15-30 minutes rather than the 1-2 hours recommended for full MAS analysis.

### Phase 4: Trust Boundaries (Simplified)

MAS trust boundaries include complex inter-agent trust zones, delegation chains, and distributed trust models. For single-agent systems, the trust boundaries are more straightforward:

- **User to Agent boundary**: Where user input enters the agent
- **Agent to Tools/MCP boundary**: Where the agent invokes external capabilities
- **Agent to Data boundary**: Where the agent reads from and writes to data stores
- **Agent to External Services boundary**: Where the agent communicates with APIs and third-party services
- **Agent to Infrastructure boundary**: Where the agent relies on platform services (IAM, secrets, compute)

Draw these boundaries explicitly. Even though they are simpler, they are where the most important security decisions are made.

### Phase 6: Threat Identification (Focused)

Skip inter-agent threat patterns. Focus the per-layer analysis on:

1. **L1**: Prompt injection (direct and indirect), hallucination impact, model integrity
2. **L3**: Tool misuse, excessive agency, MCP server vulnerabilities
3. **L4**: Infrastructure security, IAM misconfiguration, network exposure
4. **L6**: Authorization propagation, credential management, compliance gaps

Then review the cross-layer patterns that apply to single agents:
- Hallucination -> RAG -> Tool Misuse (L1+L2+L3)
- Framework Exploit -> Infra -> Compliance Bypass (L3+L4+L6)
- Confused Deputy / Excessive Agency (L3+L6+L7)
- HITL Overwhelm + Automation Bias (L5+L7)

---

## Checklist: MAS-Specific vs. Universal

Use this checklist to determine which items in the full MAESTRO playbook apply to your single-agent system.

### Universal (Always Applicable)

- [ ] Business context documented
- [ ] Architecture mapped to all 7 MAESTRO layers
- [ ] Foundation model risks assessed (L1)
- [ ] Data pipeline integrity verified (L2)
- [ ] Tool access follows least privilege (L3)
- [ ] Autonomy boundaries defined and enforced (L3)
- [ ] Infrastructure hardened (L4)
- [ ] Logging and monitoring in place (L5)
- [ ] Human-in-the-loop controls evaluated (L5)
- [ ] Authentication and authorization designed (L6)
- [ ] Secrets management implemented (L6)
- [ ] Compliance requirements mapped (L6)
- [ ] External service integrations assessed (L7)
- [ ] Trust boundaries drawn
- [ ] Critical assets identified
- [ ] Prompt injection threats analyzed
- [ ] Confused deputy / excessive agency analyzed
- [ ] Cross-layer patterns reviewed
- [ ] Mitigations planned and prioritized
- [ ] Residual risks documented

### MAS-Specific (Skip for Single-Agent)

- [ ] ~~Inter-agent communication security (T12)~~
- [ ] ~~Agent collusion / emergent behavior (T38)~~
- [ ] ~~Distributed state synchronization (T21)~~
- [ ] ~~Agent-to-agent trust relationships~~
- [ ] ~~Orchestrator compromise scenarios~~
- [ ] ~~Delegation chain analysis~~
- [ ] ~~Cross-agent identity federation~~
- [ ] ~~Agent coordination failure modes~~
- [ ] ~~Shared memory / state store consistency~~

---

## Agentic Factors for Single Agents

The four agentic factors still apply, but one is significantly reduced:

| Factor | Applicability | Notes |
|--------|--------------|-------|
| **Non-Determinism** | **Full** | Same input can produce different outputs. This affects testing, auditing, and reproducibility regardless of agent count. |
| **Autonomy** | **Full** | The agent acts with whatever autonomy level it has been granted. Single agents may actually have MORE autonomy since there is no peer agent to cross-check decisions. |
| **Identity Management** | **Full** | The agent's credentials, permissions, and service accounts need the same careful management. |
| **Agent-to-Agent Communication** | **Reduced** | No inter-agent communication. However, agent-to-tool and agent-to-MCP-server communication channels carry analogous risks. Reinterpret this factor as "agent-to-external-system communication" for single-agent analysis. |

---

## Example: Single-Agent Threat Model Scope

For a single AI agent that automates financial processes (such as an invoice processing or reconciliation agent), a reasonable threat model scope would be:

**In Scope:**
- Prompt injection via user inputs and backend data fields (L1)
- RAG data integrity if knowledge retrieval is used (L2)
- MCP server tool access and input validation (L3)
- Cloud compute security and IAM roles (L4)
- Dashboard logging and approval workflows (L5)
- Backend credential management and regulatory compliance (L6)
- ERP/API integrations and email/notification integrations (L7)
- Cross-layer: Hallucination leading to incorrect financial calculations flowing to backend systems

**Out of Scope:**
- Inter-agent communication (single agent)
- Agent collusion (single agent)
- Distributed state sync between agents (single agent)
- Orchestrator compromise (no orchestrator)

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

| Navigation | |
|---|---|
| **Risk Scoring Guide** | [Risk Scoring Methodology](./risk-scoring.md) |
| **Quick Reference** | [Quick Reference Card](../playbook/12-quick-reference.md) |
| **Templates** | [Templates](../playbook/07-templates.md) |
| **Playbook Overview** | [Playbook Overview](../playbook/00-overview.md) |
