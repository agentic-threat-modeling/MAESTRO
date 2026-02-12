# 10 - Case Studies

**Version:** 1.0.0

The OWASP Multi-Agentic System Threat Modelling Guide provides three case studies that demonstrate how MAESTRO analysis surfaces threats in real-world agentic AI systems. This document presents the key patterns and findings from each case study.

> **Grounding Note:** These case studies are derived from the OWASP Multi-Agentic System Threat Modelling Guide v1.0 and represent plausible threat scenarios based on real-world system architectures and known attack patterns. They are not accounts of specific security incidents at named organizations. The system architectures described (RPA expense processing, ElizaOS autonomous agents, MCP protocol interactions) reflect common deployment patterns where the identified threats are most likely to manifest. Use these as templates for your own threat analysis, adapting the specifics to your system's architecture and threat landscape.

---

## Pattern A: RPA Expense Reimbursement Agent

**System**: Single agent + RAG + tools + HITL review

### Key Findings

- **T16 (Model Inconsistency)**: Identical expense claims processed differently due to LLM non-determinism. The same receipt submitted twice may be approved once and flagged once, creating inconsistency and potential for exploitation.
- **T17 (Semantic Drift)**: Outdated policy embeddings cause agent to apply old rules. When expense policies are updated but the vector database is not re-indexed, the agent enforces stale rules.
- **T18 (RAG Manipulation)**: Crafted inputs exploit similarity search to bypass policy. An attacker can construct expense descriptions that are semantically similar to approved categories, causing the RAG to retrieve permissive policy passages.
- **T19 (Unintended Workflow)**: Agent skips validation steps due to workflow flaw. The agent framework allows out-of-order step execution, enabling the agent to jump from data gathering to approval without performing the validation step.
- **T22 (Service Account Exposure)**: Credentials accidentally exposed in code repos. The service account used by the agent to access the ERP system was committed to a code repository.
- **T23 (Selective Log Manipulation)**: Specific audit entries deleted while others preserved. More sophisticated than blanket log deletion, this allows a compromised agent to cover its tracks while maintaining a plausible audit trail.
- **T24 (Dynamic Policy Failure)**: Policy engine fails to apply correct user-specific rules. The agent applies a default policy instead of the user's department-specific policy, resulting in incorrect approval thresholds.
- **Cross-Layer**: The Hallucination -> RAG -> Tool Misuse chain (L1 + L2 + L3) is the most dangerous pattern. The LLM hallucinates a rule, the RAG retrieves content that appears to support the hallucinated rule, and the agent autonomously acts on it by approving an expense claim.

---

## Pattern B: ElizaOS (Web3/Blockchain Agent)

**System**: Multi-agent + blockchain + plugins + cross-chain

### Key Findings

- **T29 (Plugin Vulnerability)**: Malicious plugins steal agent credentials. The ElizaOS plugin ecosystem allows third-party extensions that can access the agent's wallet keys and credentials.
- **T32 (Runaway Agent)**: Agent enters infinite loop submitting costly blockchain transactions. Without circuit breakers, an agent bug or manipulation causes repeated transaction submissions, each incurring gas fees and potentially executing unwanted trades.
- **T34 (Wallet Key Compromise)**: Private keys stolen, funds drained. Agent wallet private keys are stored insecurely or exposed through plugin access, allowing direct theft of cryptocurrency.
- **T38 (Emergent Collusion)**: Multiple agents accidentally create flash crash. Without coordination limits, multiple agents independently responding to market signals can create a feedback loop that crashes token prices.
- **Lesson**: Financial transaction agents need hard spending limits and circuit breakers. The immutable nature of blockchain transactions makes corrective controls nearly impossible -- preventive controls are essential.

---

## Pattern C: MCP Protocol

**System**: Client-server protocol + tools + resources + prompts

### Key Findings

- **T39 (Unintended Resource Consumption)**: Agent autonomously overuses MCP tools. Without usage limits, an agent may invoke MCP tools at a rate that exceeds cost budgets or overwhelms the tool server.
- **T40 (Client Impersonation)**: Attacker impersonates legitimate MCP client. If client authentication is weak or absent, a malicious client can connect to an MCP server and invoke tools with the server's permissions.
- **T41 (Schema Mismatch)**: Ambiguous schemas cause data misinterpretation. When the MCP tool schema is imprecise, the client and server may interpret parameters differently, leading to unintended tool behavior.
- **T42 (Cross-Client Interference)**: Multiple clients on shared server interfere. When multiple MCP clients share a server instance, one client's actions may affect another's state or resources.
- **T43 (Network Exposure)**: MCP server deployed without firewall. The MCP server is directly accessible from the network without network-level access controls, exposing it to unauthorized connections.
- **T47 (Rogue MCP Server)**: Malicious server masquerades as legitimate. An attacker deploys a fake MCP server that mimics a legitimate one, providing poisoned data or capturing sensitive information from clients.
- **Lesson**: MCP server security is critical -- treat every server as a trust boundary. The protocol must enforce mutual authentication, strict schema validation, and network isolation.

---

## Key Takeaways

### Across All Case Studies

1. **Cross-layer threats are the highest-risk findings.** Single-layer analysis misses the most dangerous attack chains. The Hallucination -> RAG -> Tool Misuse pattern (Pattern A) and the Plugin -> Wallet Compromise chain (Pattern B) both span multiple MAESTRO layers.

2. **Non-determinism creates unique agentic risks.** Traditional systems process identical inputs identically. Agentic systems with LLMs do not. This fundamental property (T16 in Pattern A) undermines security controls that assume deterministic behavior.

3. **Financial actions require hard preventive controls.** When agent actions have real-world financial consequences (blockchain transactions in Pattern B, expense approvals in Pattern A), corrective controls may be insufficient. Hard limits, circuit breakers, and mandatory human approval gates are essential.

4. **Trust boundaries must be explicit and enforced.** MCP servers (Pattern C), plugin ecosystems (Pattern B), and RAG pipelines (Pattern A) all represent trust boundaries. Every trust boundary needs authentication, authorization, input validation, and monitoring.

5. **Autonomy amplifies every threat.** In all three case studies, the autonomous nature of the agents -- acting without per-action human approval -- is what transforms moderate vulnerabilities into high-severity threats. Autonomy boundaries must be carefully designed and enforced.

6. **Extended threats (beyond T1-T15) are critical.** The MAESTRO extended threat catalog (T16-T47) captured many of the most impactful findings. Limiting analysis to the base ASI taxonomy would miss threats like Semantic Drift (T17), Runaway Agent (T32), and Rogue MCP Server (T47).

7. **Preventive controls must not rely on LLM compliance.** Since LLMs are non-deterministic and susceptible to prompt injection, preventive controls that depend on the LLM "choosing" to comply are unreliable. Controls must be enforced externally to the LLM.

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

## Navigation

| Previous | Up | Next |
|----------|-----|------|
| [09 - Mitigation Catalog](./09-mitigation-catalog.md) | [00 - Overview](./00-overview.md) | [11 - Framework Integration](./11-framework-integration.md) |

See also: [06 - Modeling Process](./06-modeling-process.md) | [09 - Mitigation Catalog](./09-mitigation-catalog.md)
