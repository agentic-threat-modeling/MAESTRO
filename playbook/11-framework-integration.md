# 11 - Framework Integration

**Version:** 1.0.0

MAESTRO is designed to complement, not replace, established security frameworks. This document describes how to integrate MAESTRO with STRIDE, MITRE ATT&CK/ATLAS, and the OWASP Top 10 for LLM Applications (2025).

---

## Table of Contents

1. [MAESTRO + STRIDE](#maestro--stride)
2. [MAESTRO + MITRE ATT&CK / ATLAS](#maestro--mitre-attck--atlas)
3. [MAESTRO + OWASP Top 10 for LLM Applications (2025)](#maestro--owasp-top-10-for-llm-applications-2025)
4. [MAESTRO + OWASP Agentic Security Initiative (ASI) Top 10](#maestro--owasp-agentic-security-initiative-asi-top-10)
5. [CWE Cross-References for Selected Threats](#cwe-cross-references-for-selected-threats)
6. [Choosing a Framework Combination](#choosing-a-framework-combination)

---

## MAESTRO + STRIDE

MAESTRO and STRIDE are complementary:

- **STRIDE** provides general threat categories (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege) that apply to any software system.
- **MAESTRO** provides agentic-AI-specific layer analysis that surfaces threats unique to LLM-based autonomous systems.
- **Best practice**: Run STRIDE first for broad coverage, then MAESTRO for AI-specific depth. STRIDE ensures you do not miss traditional software threats; MAESTRO ensures you capture agentic-specific threats that STRIDE was not designed to find.
- Map STRIDE findings to MAESTRO layers for additional context. For example, a STRIDE "Elevation of Privilege" finding maps naturally to MAESTRO Layer 6 (Security & Compliance) and the T3 (Privilege Compromise) ASI threat.

### STRIDE-to-MAESTRO Mapping

| STRIDE Category | Primary MAESTRO Layers | Primary ASI Threats |
|----------------|----------------------|---------------------|
| Spoofing | L7 (Ecosystem), L6 (Security) | T9 (Identity Spoofing), T40 (MCP Client Impersonation) |
| Tampering | L2 (Data Ops), L3 (Agent FW), Cross-Layer | T1 (Memory Poisoning), T12 (Agent Communication Poisoning) |
| Repudiation | L5 (Eval/Observability) | T8 (Repudiation & Untraceability) |
| Information Disclosure | L2 (Data Ops), L6 (Security), Cross-Layer | T28 (RAG Data Exfiltration), T3 (Privilege Compromise) |
| Denial of Service | L4 (Infrastructure), Cross-Layer | T4 (Resource Overload), T32 (Runaway Agent) |
| Elevation of Privilege | L4 (Infrastructure), L6 (Security), Cross-Layer | T3 (Privilege Compromise), T14 (Human Attacks on MAS) |

---

## MAESTRO + MITRE ATT&CK / ATLAS

- **ATT&CK**: Comprehensive adversary tactics and techniques matrix for enterprise systems. Provides detailed, real-world attack technique descriptions organized by tactic (Initial Access, Execution, Persistence, Privilege Escalation, etc.).
- **ATLAS**: AI-specific extension of ATT&CK focused on adversarial machine learning: data poisoning, model evasion, model extraction, adversarial examples, and AI supply chain attacks.
- **MAESTRO**: Enables structured evaluation against ATT&CK/ATLAS scenarios by providing the architectural layer decomposition. Each MAESTRO layer maps to specific ATT&CK/ATLAS tactics.
- **Use together**: Map MAESTRO threats to ATT&CK/ATLAS techniques for red team scenarios. This produces concrete, actionable attack simulations grounded in real-world adversary behavior.

### Integration Approach

1. **Start with MAESTRO layer analysis** to identify which layers and components are in scope.
2. **Map each MAESTRO layer to relevant ATT&CK tactics**:
   - L1 (Foundation Model) -> ATLAS techniques: Model Poisoning, Model Evasion, Adversarial Examples
   - L2 (Data Ops) -> ATLAS: Data Poisoning; ATT&CK: Collection, Exfiltration
   - L3 (Agent Frameworks) -> ATT&CK: Execution, Persistence; ATLAS: Technique Abuse
   - L4 (Infrastructure) -> ATT&CK: Initial Access, Lateral Movement, Privilege Escalation
   - L5 (Eval/Observability) -> ATT&CK: Defense Evasion, Impact
   - L6 (Security/Compliance) -> ATT&CK: Credential Access, Privilege Escalation
   - L7 (Ecosystem) -> ATT&CK: Initial Access, Command and Control; ATLAS: Supply Chain Compromise
3. **For each identified MAESTRO threat**, find the corresponding ATT&CK/ATLAS technique(s) and use those to develop red team test cases.
4. **Cross-layer MAESTRO threats** often correspond to ATT&CK attack chains (multiple techniques combined into a campaign).

---

## MAESTRO + OWASP Top 10 for LLM Applications (2025)

> **Note**: The OWASP Top 10 for LLM Applications v2.0 (released November 2024, commonly referred to as "2025 edition") has different numbering and content from the original v1.1 (released September 2023). The mappings below use the **2025 version** numbering. If you are working with the v1.1 list, the item numbers and descriptions differ.

Key mappings from the OWASP Top 10 for LLM 2025 to MAESTRO layers:

| LLM Top 10 (2025) | Risk | MAESTRO Layers |
|-------------------|------|----------------|
| **LLM01** | Prompt Injection | L1 (Foundation Model), L3 (Agent Frameworks) |
| **LLM02** | Sensitive Information Disclosure | L2 (Data Operations), L6 (Security & Compliance), Cross-Layer |
| **LLM03** | Supply Chain | L2 (Data Operations), L3 (Agent Frameworks), L7 (Agent Ecosystem) |
| **LLM04** | Data and Model Poisoning | L1 (Foundation Model), L2 (Data Operations) |
| **LLM05** | Improper Output Handling | L1 (Foundation Model), L3 (Agent Frameworks), L5 (Evaluation & Observability) |
| **LLM06** | Excessive Agency | L3 (Agent Frameworks), Cross-Layer |
| **LLM07** | System Prompt Leakage | L1 (Foundation Model), L3 (Agent Frameworks) |
| **LLM08** | Vector and Embedding Weaknesses | L2 (Data Operations) |
| **LLM09** | Misinformation | L1 (Foundation Model), L5 (Evaluation & Observability), L7 (Agent Ecosystem) |
| **LLM10** | Unbounded Consumption | L4 (Deployment Infrastructure), Cross-Layer |

### Integration Guidance

- **LLM01 (Prompt Injection)** maps to MAESTRO L1 and L3. Use MAESTRO's Layer 1 analysis for direct prompt injection and Layer 3 analysis for indirect injection via tools and workflows. ASI threats T6 (Intent Breaking) and T1 (Memory Poisoning) are the primary correspondences.

- **LLM02 (Sensitive Information Disclosure)** spans L2, L6, and Cross-Layer. MAESTRO's Data Operations layer analysis covers RAG-based data leakage, while the Security & Compliance vertical layer covers access control failures. Cross-layer analysis covers information disclosure through privilege chaining.

- **LLM03 (Supply Chain)** maps to L2, L3, and L7. MAESTRO analysis of Data Operations covers poisoned training data and embeddings. Agent Frameworks covers plugin/extension supply chain. Agent Ecosystem covers third-party service and MCP server trust.

- **LLM04 (Data and Model Poisoning)** maps to L1 and L2. This is directly addressed by MAESTRO's Foundation Model and Data Operations layer analyses. ASI threat T1 (Memory Poisoning) is the primary correspondence.

- **LLM05 (Improper Output Handling)** maps to L1, L3, and L5. MAESTRO's Foundation Model layer covers output validation. Agent Frameworks covers tool invocation based on unvalidated outputs. Evaluation & Observability covers detection of improper outputs.

- **LLM06 (Excessive Agency)** is a primary MAESTRO concern, mapping to L3 and Cross-Layer. This is the "confused deputy" pattern that MAESTRO's cross-layer analysis is specifically designed to detect. ASI threats T2 (Tool Misuse) and T3 (Privilege Compromise) apply.

- **LLM07 (System Prompt Leakage)** maps to L1 and L3. MAESTRO analysis covers system prompt protection in the Foundation Model layer and prompt management security in the Agent Frameworks layer.

- **LLM08 (Vector and Embedding Weaknesses)** maps directly to L2. MAESTRO's Data Operations layer analysis covers embedding poisoning, semantic drift (T17), RAG manipulation (T18), and data exfiltration (T28).

- **LLM09 (Misinformation)** maps to L1, L5, and L7. MAESTRO covers hallucination detection in L1, output monitoring in L5, and the propagation of misinformation through the ecosystem in L7. ASI threat T5 (Cascading Hallucinations) is the primary correspondence.

- **LLM10 (Unbounded Consumption)** maps to L4 and Cross-Layer. MAESTRO's Infrastructure layer covers resource limits and DDoS protection. Cross-layer analysis covers cascading resource exhaustion. ASI threat T4 (Resource Overload) and extended threat T32 (Runaway Agent) apply.

---

## MAESTRO + OWASP Agentic Security Initiative (ASI) Top 10

> **Note**: The OWASP ASI Top 10 (2025/2026) is a separate list from the OWASP Top 10 for LLM Applications. The ASI Top 10 focuses specifically on agentic AI and multi-agent system risks, using the ASI01-ASI10 numbering scheme. The ASI threat taxonomy (T1-T15) used throughout this playbook is the detailed threat enumeration that underpins the ASI Top 10 categories.

| ASI Top 10 ID | Category | ASI Taxonomy Threats | MAESTRO Layers |
|---------------|----------|---------------------|----------------|
| **ASI01** | Agent Autonomy Abuse | T2 (Tool Misuse), T6 (Intent Breaking) | L3, Cross-Layer |
| **ASI02** | Identity and Access Failures | T3 (Privilege Compromise), T9 (Identity Spoofing) | L4, L6, L7, Cross-Layer |
| **ASI03** | Unsafe Code Generation and Execution | T11 (Unexpected RCE / Code Attacks) | L1, L3, Cross-Layer |
| **ASI04** | Cascading Hallucination and Misinformation | T5 (Cascading Hallucinations), T7 (Misaligned & Deceptive Behaviour) | L1, L3, L5, Cross-Layer |
| **ASI05** | Memory and Context Manipulation | T1 (Memory Poisoning), T12 (Agent Communication Poisoning) | L2, Cross-Layer |
| **ASI06** | Supply Chain and Dependency Exploits | T13 (Rogue Agents), T29 (Plugin Vulnerability), T36 (Malicious Agent Diffusion), T37 (Agent Registry Poisoning) | L2, L3, L7, Cross-Layer |
| **ASI07** | Multi-Agent Communication Exploits | T12 (Agent Communication Poisoning), T13 (Rogue Agents) | L3, L7, Cross-Layer |
| **ASI08** | Resource and Cost Exhaustion | T4 (Resource Overload), T32 (Runaway Agent) | L4, Cross-Layer |
| **ASI09** | Audit, Compliance and Accountability Gaps | T8 (Repudiation & Untraceability), T10 (Overwhelming HITL) | L5, L6, Cross-Layer |
| **ASI10** | Human Manipulation and Social Engineering | T14 (Human Attacks on MAS), T15 (Human Trust Manipulation) | L5, L7, Cross-Layer |

### Integration Guidance

- Use the ASI Top 10 as a high-level checklist to verify coverage: for each ASI category, confirm that your MAESTRO analysis has identified and assessed the corresponding threats.
- The ASI Top 10 categories group related threats; MAESTRO's per-layer analysis provides the granular detail needed for actionable mitigations.
- When reporting to executives or non-technical stakeholders, use the ASI Top 10 categories. When working on technical mitigations, use the detailed ASI taxonomy (T1-T15) and extended threats.

---

## CWE Cross-References for Selected Threats

The following maps selected ASI threats to Common Weakness Enumeration (CWE) entries, enabling integration with vulnerability management workflows and SAST/DAST tooling.

| ASI Threat | CWE ID(s) | CWE Name | Notes |
|-----------|-----------|----------|-------|
| T3 (Privilege Compromise) | CWE-269, CWE-250 | Improper Privilege Management, Execution with Unnecessary Privileges | Agent service accounts with excessive permissions |
| T6 (Intent Breaking) | CWE-74, CWE-77 | Injection, Command Injection | Prompt injection is analogous to traditional injection flaws |
| T8 (Repudiation) | CWE-778, CWE-223 | Insufficient Logging, Omission of Security-relevant Information | Incomplete audit trails for agent actions |
| T11 (Unexpected RCE) | CWE-94, CWE-502 | Improper Control of Code Generation, Deserialization of Untrusted Data | LLM-generated code executed without sandboxing |
| T29 (Plugin Vulnerability) | CWE-829, CWE-494 | Inclusion of Functionality from Untrusted Control Sphere, Download of Code Without Integrity Check | Third-party MCP servers and plugins |
| T42 (Cross-Client Interference) | CWE-200, CWE-668 | Exposure of Sensitive Information, Exposure of Resource to Wrong Sphere | Tenant isolation failures in shared agent infrastructure |
| T1 (Memory Poisoning) | CWE-345 | Insufficient Verification of Data Authenticity | Poisoned training data, RAG documents, or agent memory accepted without provenance verification |
| T2 (Tool Misuse) | CWE-863 | Incorrect Authorization | Agent invokes tools beyond authorized scope |
| T4 (Resource Overload) | CWE-400 | Uncontrolled Resource Consumption | Unbounded token/API/compute usage |
| T5 (Cascading Hallucinations) | — | No direct CWE mapping | AI-specific; no traditional software equivalent |
| T7 (Misaligned & Deceptive Behaviour) | CWE-684 | Incorrect Provision of Specified Functionality | Agent deviates from intended behavior/policies; approximate mapping |
| T9 (Identity Spoofing) | CWE-290 | Authentication Bypass by Spoofing | Agent or user identity impersonation |
| T10 (Overwhelming HITL) | — | No direct CWE mapping | Operational/human-factors risk; no traditional software equivalent |
| T12 (Agent Communication Poisoning) | CWE-345, CWE-924 | Insufficient Verification of Data Authenticity, Improper Enforcement of Message Integrity | Corrupted inter-agent or agent-external messages accepted as authentic |
| T13 (Rogue Agents) | CWE-494 | Download of Code Without Integrity Check | Compromised or malicious agent loaded without verification |
| T14 (Human Attacks on MAS) | CWE-807 | Reliance on Untrusted Inputs in a Security Decision | Human-crafted adversarial requests exploiting agent trust |
| T15 (Human Trust Manipulation) | — | No direct CWE mapping | Human-factors/automation bias risk; no traditional software equivalent |

### Extended Threat CWE Mappings

The following maps selected extended threats (T16-T47) to CWE entries where applicable. Many extended threats are AI-specific or domain-specific and lack direct CWE equivalents.

| ASI Threat | CWE ID(s) | CWE Name | Notes |
|-----------|-----------|----------|-------|
| T16 (Model Inconsistency) | — | No direct CWE mapping | AI-specific; non-deterministic behavior is inherent to LLMs, not a software defect |
| T17 (Semantic Drift) | CWE-345 | Insufficient Verification of Data Authenticity | Embedding drift alters retrieval semantics without detection |
| T18 (RAG Input Manipulation) | CWE-20 | Improper Input Validation | Malicious documents injected into RAG pipeline |
| T19 (Unintended Workflow Execution) | CWE-691 | Insufficient Control Flow Management | Agent executes unintended workflow branches |
| T20 (Framework Code Injection) | CWE-94 | Improper Control of Generation of Code | Injection into agent framework runtime |
| T21 (Inconsistent Workflow State) | CWE-362 | Concurrent Execution Using Shared Resource with Improper Synchronization | Race conditions in shared agent state |
| T22 (Service Account Exposure) | CWE-522 | Insufficiently Protected Credentials | Agent service account credentials exposed |
| T23 (Selective Log Manipulation) | CWE-117 | Improper Output Neutralization for Logs | Attacker injects or modifies log entries to hide activity |
| T24 (Dynamic Policy Enforcement Failure) | CWE-863 | Incorrect Authorization | Policy changes not propagated to running agents |
| T25 (Workflow Disruption via Dependency) | CWE-829 | Inclusion of Functionality from Untrusted Control Sphere | External dependency compromise disrupts agent workflows |
| T26-T27 | — | Reserved | Reserved for future assignment |
| T28 (RAG Data Exfiltration) | CWE-200 | Exposure of Sensitive Information to an Unauthorized Actor | Sensitive data extracted through RAG query manipulation |
| T30 (Insecure Inter-Agent Protocol) | CWE-319 | Cleartext Transmission of Sensitive Information | Agent-to-agent messages sent without encryption or authentication |
| T31 (Insufficient Action Isolation) | CWE-653 | Improper Isolation or Compartmentalization | Agent actions not sandboxed; side effects leak across boundaries |
| T32 (Runaway Agent) | CWE-400 | Uncontrolled Resource Consumption | Agent enters infinite loop consuming unbounded resources |
| T33 (Infrastructure Agent Loops) | CWE-835 | Loop with Unreachable Exit Condition | Infrastructure-level agent recursion without termination |
| T34 (Wallet Key Compromise) | CWE-522 | Insufficiently Protected Credentials | Domain-specific to blockchain/Web3 agents |
| T35 (Cross-Chain Bridge Exploitation) | CWE-345 | Insufficient Verification of Data Authenticity | Domain-specific to blockchain/Web3 agents |
| T36 (Malicious Agent Diffusion) | CWE-506 | Embedded Malicious Code | Compromised agent propagates malicious behavior through ecosystem |
| T37 (Agent Registry Poisoning) | CWE-494 | Download of Code Without Integrity Check | Rogue agent registered in trusted directory |
| T38 (Emergent Collusion) | — | No direct CWE mapping | AI-specific emergent behavior; no traditional software equivalent |
| T39 (Unintended Resource Consumption) | CWE-400 | Uncontrolled Resource Consumption | Agent exceeds budget/resource limits without malicious intent |
| T40 (MCP Client Impersonation) | CWE-290 | Authentication Bypass by Spoofing | Attacker impersonates legitimate MCP client |
| T41 (Schema Mismatch) | CWE-20 | Improper Input Validation | Tool input/output schemas diverge from expected format |
| T42 (Cross-Client Interference) | CWE-200, CWE-668 | Exposure of Sensitive Information, Exposure of Resource to Wrong Sphere | Tenant isolation failures in shared agent infrastructure |
| T43 (Network Exposure) | CWE-668 | Exposure of Resource to Wrong Sphere | Agent infrastructure exposed to unintended networks |
| T44 (Insufficient Logging) | CWE-778 | Insufficient Logging | Agent actions not logged with adequate detail |
| T45 (Insufficient Server Permission Isolation) | CWE-269 | Improper Privilege Management | MCP server permissions too broad |
| T46 (Data Residency/Compliance Violation) | CWE-200 | Exposure of Sensitive Information to an Unauthorized Actor | Data processed in non-compliant jurisdictions |
| T47 (Rogue Server) | CWE-494, CWE-829 | Download of Code Without Integrity Check, Inclusion of Functionality from Untrusted Control Sphere | Malicious MCP server in trusted registry |

> **Note**: CWE mappings are approximate. AI-specific vulnerabilities (T5, T10, T15, T16, T38) do not map to traditional software weakness categories. Threats with CWE-345 involve data authenticity issues that manifest differently in AI contexts than in traditional software. Domain-specific threats (T34, T35) map to generic CWE categories but require specialized mitigation approaches. Use these mappings as a starting point for integration with existing vulnerability management processes.

---

## Choosing a Framework Combination

| Scenario | Recommended Combination |
|----------|------------------------|
| First threat model for an agentic AI system | MAESTRO + OWASP Top 10 for LLM (2025) |
| Comprehensive enterprise security assessment | STRIDE first, then MAESTRO for AI-specific depth |
| Red team exercise / penetration test planning | MAESTRO + MITRE ATT&CK/ATLAS |
| Regulatory compliance assessment | MAESTRO + STRIDE + OWASP Top 10 for LLM (2025) + CWE mapping |
| Multi-agent system with external integrations | Full MAESTRO (all layers) + OWASP Top 10 for LLM (2025) |
| Executive reporting on agentic AI risk | MAESTRO + ASI Top 10 for high-level categories |

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

## Navigation

| Previous | Up | Next |
|----------|-----|------|
| [10 - Case Studies](./10-case-studies.md) | [00 - Overview](./00-overview.md) | [12 - Quick Reference](./12-quick-reference.md) |

See also: [06 - Modeling Process](./06-modeling-process.md) | [09 - Mitigation Catalog](./09-mitigation-catalog.md) | [10 - Case Studies](./10-case-studies.md)
