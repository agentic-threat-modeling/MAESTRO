# 03 - Layer-to-ASI Threat Mapping Matrix

**Version:** 1.0.0

---

## How to Read This Matrix

This matrix maps each ASI threat (T1-T15) against the 7 MAESTRO layers plus the Cross-Layer dimension. It is the primary tool for ensuring comprehensive coverage during threat analysis.

- **X** (bold/uppercase) = **Primary mapping**. The threat manifests directly and most significantly at this layer. This is where you should focus your deepest analysis and strongest mitigations.
- **x** (lowercase) = **Secondary or conditional mapping**. The threat can manifest at this layer under certain conditions, configurations, or architectures. Analyze these when the conditions apply to your system (e.g., T1 at L1 is secondary because Memory Poisoning primarily targets data operations at L2, but can affect L1 if the foundation model is fine-tunable or uses memory for training).

During analysis, walk each row left-to-right to ensure you have considered the threat at every relevant layer. Walk each column top-to-bottom to build the per-layer threat profile.

---

## Mapping Matrix

Use this matrix to ensure comprehensive coverage. Check each cell during analysis.

| ASI Threat | L1 Foundation | L2 Data Ops | L3 Agent FW | L4 Infra | L5 Eval/Obs | L6 Sec/Comp | L7 Ecosystem | Cross-Layer |
|------------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| T1 Memory Poisoning | x | **X** | | | | | | X |
| T2 Tool Misuse | | | **X** | | | | | X |
| T3 Privilege Compromise | | | | **X** | | **X** | | X |
| T4 Resource Overload | | | | **X** | | | | X |
| T5 Cascading Hallucinations | **X** | | **X** | | | | | X |
| T6 Intent Breaking | | | **X** | | | | | **X** |
| T7 Misaligned & Deceptive Behaviour | **X** | | | | | **X** | | X |
| T8 Repudiation | | | | | **X** | | | X |
| T9 Identity Spoofing | | | | | | | **X** | X |
| T10 Overwhelming HITL | | | | | **X** | | | |
| T11 RCE/Code Attacks | **X** | | **X** | | | | | **X** |
| T12 Agent Comm Poisoning | | **X** | | | | | | **X** |
| T13 Rogue Agents | | | | **X** | | | **X** | **X** |
| T14 Human Attacks on MAS | | | | **X** | | | **X** | X |
| T15 Human Trust Manipulation | | | | | | | **X** | **X** |

**X** = primary mapping, x = secondary/conditional mapping

> **Note**: This mapping matrix covers the 15 core ASI threats (T1-T15). Extended threats (T16-T47) are mapped to specific layers in the [Threat Taxonomy](./02-threat-taxonomy.md) and the [Checklists](./08-checklists.md). Use the per-layer checklists in `08-checklists.md` for comprehensive coverage including extended threats.

### T11 Cross-Layer Rationale

T11 (Unexpected RCE / Code Attacks) is marked as a primary cross-layer threat because code generation and code execution typically span multiple layers:

- **L1 (Foundation Model)**: The LLM generates the code. Prompt injection or hallucination can cause the model to produce malicious or unintended code.
- **L3 (Agent Framework)**: The agent framework orchestrates tool calls that may include code execution tools (`eval()`, shell commands, dynamic script execution).
- **L4 (Deployment Infrastructure)**: The generated code executes on deployment infrastructure, where it can access file systems, network resources, and service credentials.

This cross-layer chain means that a code injection that originates at L1 (model generates malicious code) is executed at L3/L4 (agent runs it), making T11 a threat that cannot be fully mitigated at any single layer. Effective defense requires input validation at L1, tool-use restrictions at L3, and sandboxing/least-privilege at L4.

---

## Navigation

| Previous | Up | Next |
|----------|-----|------|
| [02 - ASI Threat Taxonomy](./02-threat-taxonomy.md) | [00 - Overview](./00-overview.md) | [04 - Cross-Layer Scenarios](./04-cross-layer-scenarios.md) |

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*
