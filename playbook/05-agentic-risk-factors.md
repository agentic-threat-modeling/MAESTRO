# 05 - Agentic Risk Factors

**Version:** 1.0.0

At **every layer** of the MAESTRO framework, these four factors amplify threats in agentic systems. They are not threats themselves but rather properties of agentic AI that make existing threats more dangerous, harder to detect, and more likely to cascade.

---

## Table of Contents

1. [Non-Determinism](#1-non-determinism)
2. [Autonomy](#2-autonomy)
3. [Agent Identity Management](#3-agent-identity-management)
4. [Agent-to-Agent Communication](#4-agent-to-agent-communication)
5. [Applying Risk Factors Per Layer](#applying-risk-factors-per-layer)

---

## 1. Non-Determinism

The LLM produces different outputs for identical inputs. This means:
- Security controls that work "most of the time" will eventually fail
- Identical transactions may be processed differently
- Testing can't guarantee production behavior
- **Ask**: How does non-determinism at this layer affect security guarantees?

---

## 2. Autonomy

The agent acts without human approval for each step. This means:
- Mistakes propagate before humans notice
- Compromised agents cause damage at machine speed
- "Runaway" behavior possible in loops
- **Ask**: What are the autonomy boundaries? What happens if the agent goes off-script?

---

## 3. Agent Identity Management

Agents have their own identities, credentials, and permissions. This means:
- Service accounts may be over-privileged
- User identity may not propagate through agent to backend
- Agent identity can be spoofed
- **Ask**: Does the agent act as itself or on behalf of the user? Is least-privilege enforced?

---

## 4. Agent-to-Agent Communication

Agents communicate with other agents, tools, and systems. This means:
- Communication channels can be intercepted/poisoned
- Trust between agents can be exploited
- One compromised agent can corrupt others
- **Ask**: Are communication channels authenticated and encrypted? Are there trust boundaries?

---

## Applying Risk Factors Per Layer

The following matrix shows how each of the four agentic risk factors manifests at each MAESTRO layer. Use this during per-layer analysis to ensure no amplification vector is overlooked.

| MAESTRO Layer | Non-Determinism | Autonomy | Agent Identity Management | Agent-to-Agent Communication |
|---|---|---|---|---|
| **L1 Foundation Model** | Core source: LLM outputs vary across identical prompts; temperature, sampling, and model updates all introduce variance | Model may self-select reasoning paths or tool calls without explicit instruction | Model API keys and inference endpoint credentials must be scoped; managed vs. self-hosted models have different identity surfaces | Foundation model may receive messages from other agents embedded in prompts, with no way to authenticate the source |
| **L2 Data Operations** | Embedding similarity scores are probabilistic; retrieval ranking may vary, returning different context for the same query | Agent autonomously decides which documents to retrieve, which embeddings to trust, and how to incorporate retrieved context | Data pipeline service accounts (object storage, vector DB) may be shared across agents with no per-agent scoping | Shared vector stores and memory between agents create implicit communication channels that bypass explicit protocols |
| **L3 Agent Frameworks** | Workflow branching decisions depend on LLM output; the same input can trigger different tool calls or skip validation steps | Agent executes multi-step workflows, selects tools, and modifies its own plan without per-step approval | Framework-level service accounts may have broad tool access; MCP server credentials may not distinguish between agents | Inter-agent delegation, MCP tool calls, and shared workflow state are all communication surfaces susceptible to poisoning |
| **L4 Deployment Infrastructure** | Infrastructure scaling decisions triggered by non-deterministic agent behavior may be unpredictable (e.g., burst serverless invocations) | Auto-scaling, self-healing, and orchestration may execute without human gates, amplifying compromised agent behavior | IAM roles, function execution roles, and container service accounts require strict least-privilege; credential rotation is operationally complex | Network-level communication between agent containers, serverless functions, and orchestration services must be authenticated and encrypted |
| **L5 Evaluation & Observability** | Non-deterministic outputs make it difficult to define "normal" baselines for anomaly detection; alert thresholds may produce false positives or miss true anomalies | Automated monitoring and alerting systems may take autonomous remediation actions (e.g., circuit breakers, scaling) without human review | Logging and monitoring service accounts must be protected from agent-level compromise to preserve audit integrity | Log aggregation from multiple agents creates a shared observability surface; one agent's noisy output can mask another's malicious activity |
| **L6 Security & Compliance** | Policy enforcement against non-deterministic agent behavior requires probabilistic guardrails rather than deterministic rules; compliance audits face reproducibility challenges | Agents may autonomously exceed authorization boundaries between policy checks; runtime guardrails must operate continuously, not periodically | Agent identity must map to authorization policies; confused deputy attacks arise when user identity is not propagated through agent to backend systems | Cross-agent authorization delegation chains must be validated end-to-end; OAuth token relay and credential forwarding create transitive trust risks |
| **L7 Agent Ecosystem** | External systems receive non-deterministic agent outputs and may make downstream decisions based on them; inconsistency erodes external trust | Agents autonomously interact with external APIs, human users, and other agents; compromised autonomy propagates beyond the system boundary | Agent identity must be verifiable by external parties; spoofing an agent's identity to external systems or spoofing external systems to agents are both viable attack paths | A2A protocols, MCP ecosystem interactions, and third-party API calls are all inter-system communication channels with distinct authentication and integrity requirements |
| **Cross-Layer** | Non-determinism compounds across layers: a variable LLM output (L1) triggers a different retrieval (L2), leading to a different tool call (L3), producing a different audit trail (L5) | Autonomous decisions at one layer cascade through others at machine speed; human intervention at any single layer may be too late to prevent cross-layer damage | Identity and credential chains that span layers create privilege escalation paths; a single over-privileged service account can bridge layer boundaries | Cross-layer communication (e.g., L3 agent calling L4 infrastructure via L7 external API) creates complex trust chains where a MITM at any hop compromises the entire flow |

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

| Previous | Up | Next |
|----------|-----|------|
| [04 - Cross-Layer Scenarios](./04-cross-layer-scenarios.md) | [00 - Overview](./00-overview.md) | [06 - Modeling Process](./06-modeling-process.md) |
