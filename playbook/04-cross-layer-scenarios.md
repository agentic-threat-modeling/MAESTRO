# 04 - Cross-Layer Threat Scenarios

**Version:** 1.0.0

---

Cross-layer threats are the **highest-value findings** in MAESTRO analysis. They exploit interactions between layers that single-layer analysis misses. Every threat model must include explicit cross-layer analysis using the patterns and checklist below.

---

## Cross-Layer Patterns

### Pattern 1: Hallucination -> RAG -> Tool Misuse (L1 + L2 + L3)

LLM hallucinates a rule -> RAG retrieves the hallucinated rule -> Agent autonomously acts on it using tools.

### Pattern 2: Framework Exploit -> Infrastructure -> Compliance Bypass (L3 + L4 + L6)

Agent framework vulnerability -> Modify agent workflow -> Exploit weak infrastructure controls -> Bypass approval process.

### Pattern 3: Data Poisoning -> Agent Action -> Ecosystem Propagation (L2 + L3 + L7)

Poisoned knowledge base -> Agent makes incorrect decisions -> Shares incorrect decisions with other agents/systems.

### Pattern 4: Log Manipulation -> Security Evasion -> Undetected Fraud (L3 + L5 + L6)

Compromised agent -> Selectively delete audit entries -> Actions remain within "normal" thresholds -> Security controls bypassed.

### Pattern 5: Cascading Trust Failure (All Layers)

Compromise one component -> Use its trust relationships to access next component -> Chain across layers until full system compromise.

### Pattern 6: Confused Deputy / Excessive Agency (L3 + L6 + L7)

User authenticates at L6 -> Agent uses service account at L3 -> External system (L7) trusts agent's service account -> User performs actions beyond their authorization level.

### Pattern 7: HITL Overwhelm + Trust Manipulation (L5 + L7)

Agent produces high volume -> Human reviewers can't keep up -> Rubber-stamp decisions -> Automation bias sets in -> Incorrect/fraudulent outputs pass undetected.

---

## Detailed Walkthrough: Hallucination-to-RAG-to-Tool Chain

This walkthrough is based on the RPA Expense Reimbursement case study from the OWASP Multi-Agentic System Threat Modelling Guide. It demonstrates Pattern 1 in concrete detail and illustrates why cross-layer analysis discovers threats that single-layer analysis misses.

### Scenario Context

An organization deploys an AI agent to automate expense reimbursement processing. The agent uses an LLM (L1) for reasoning, a RAG pipeline backed by a vector database (L2) for policy lookup, and tools (L3) to approve or reject expense claims. A human-in-the-loop reviewer (L5) spot-checks a sample of decisions.

### Step 1 -- L1 Foundation Model: The Hallucination

The LLM, due to its inherent non-deterministic behavior, hallucinates a policy rule that does not exist in any real corporate document. During a reasoning chain, it generates the assertion:

> "Per company policy section 4.2.1: All expenses under $1,000 require no receipts and are auto-approved."

This hallucinated rule has no basis in the actual expense policy (which requires receipts for all expenses over $25). However, the LLM produces this output with high confidence and no hedging language. The hallucination may be triggered by ambiguous prompting, training data that included similar policies from other organizations, or simply the stochastic nature of next-token prediction.

**Single-layer assessment at L1**: A foundation model review might flag hallucination as a general risk and recommend output validation. However, it would not predict what happens when this specific hallucinated output enters downstream systems.

### Step 2 -- L2 Data Operations: The RAG Stores and Retrieves the Hallucination

The agent's architecture uses a conversational memory or scratchpad that feeds back into the RAG pipeline. The hallucinated "policy rule" from Step 1 is stored in the agent's working memory or conversation context. On subsequent queries about expense approval thresholds, the RAG pipeline's similarity search retrieves this hallucinated rule because it is semantically close to the query "what are the receipt requirements for expenses?"

The vector database now treats the hallucinated content as if it were a legitimate policy document. There is no provenance tracking to distinguish LLM-generated assertions from actual source documents. The hallucination has been laundered into the knowledge base.

**Single-layer assessment at L2**: A data operations review would check for data poisoning of source documents and access controls on the vector database. It would likely not consider that the LLM itself is a source of poisoned data flowing into the pipeline.

### Step 3 -- L3 Agent Framework: Autonomous Action on False Information

The agent framework receives the next expense claim: an employee submits a $950 expense with no receipt. The agent queries its tools and RAG for the relevant policy. The RAG returns the hallucinated rule ("expenses under $1,000 require no receipts"). The agent's reasoning engine concludes the claim is compliant with policy and invokes its approval tool to approve the expense claim and trigger payment.

Because the agent operates with autonomy -- it does not require human approval for individual claims under a certain threshold -- the approval executes immediately. No human sees this decision before it takes effect.

**Single-layer assessment at L3**: An agent framework review would check tool access controls and workflow logic. It would verify that the agent calls the correct tools with valid parameters. However, the tool invocation is technically correct -- the agent is calling the approval tool as designed. The problem is not how the tool is called, but the corrupted reasoning that led to the decision.

### Step 4 -- Impact: Financial Loss and Corrupted Policy Understanding

The immediate impact is financial: a fraudulent or unsupported expense claim is approved and paid out. But the cascading effects are worse:

- **Persistent corruption**: The hallucinated policy rule remains in the agent's memory/RAG pipeline. Every future expense claim under $1,000 will be evaluated against this false rule.
- **Scale of damage**: If the agent processes hundreds of claims per day, the financial exposure compounds rapidly before anyone notices the pattern.
- **Corrupted audit trail**: The agent's decision logs show it followed "policy" -- the hallucinated rule appears as the justification. Auditors reviewing the log may not realize the cited policy does not exist.
- **HITL bypass**: If the human reviewer (L5) only spot-checks 5% of approvals, there is a 95% chance each fraudulent approval passes unreviewed. Even when reviewed, the reviewer sees a confident, well-reasoned decision citing a specific policy section.
- **Trust erosion**: Once discovered, organizational trust in the entire AI agent system is damaged, potentially undermining legitimate automation benefits.

### Agentic Risk Factors at Play

Two of the four agentic risk factors directly enable this attack chain:

1. **Non-Determinism (L1)**: The hallucination occurs because the LLM is non-deterministic. The same prompt may produce the correct policy 99 times and the hallucinated version once. This makes the threat intermittent and harder to detect through testing.

2. **Autonomy (L3)**: The agent acts on the hallucinated rule without requiring human confirmation for each decision. If a human had to approve every expense claim, the hallucinated rule would be caught on first use. Autonomy transforms a single hallucination event into a systemic policy corruption.

### Why Cross-Layer Analysis Is Essential

No single-layer analysis would have discovered this complete attack chain:

| Layer Analyzed Alone | What It Finds | What It Misses |
|---|---|---|
| L1 Foundation Model | "Hallucination is a risk" | How hallucinated content enters downstream data pipeline |
| L2 Data Operations | "Protect vector DB from external poisoning" | That the LLM itself is an internal poisoning source |
| L3 Agent Framework | "Tool invocations follow correct patterns" | That correct tool use with corrupted reasoning is still dangerous |
| L5 Eval/Observability | "Spot-check sample of decisions" | That the decision rationale itself is based on fabricated policy |

Only by tracing the data flow across L1 -> L2 -> L3 does the full threat become visible. This is the core value of MAESTRO's cross-layer analysis.

---

## Key Cross-Layer Threats Checklist

Run through this checklist during every cross-layer analysis phase:

- [ ] Cascading Trust Failures (T13)
- [ ] Emergent Bias Amplification (T1 + T7)
- [ ] Systemic Resource Starvation (T4)
- [ ] Cross-Agent Feedback Loop Manipulation (T6 + T7)
- [ ] Inter-Agent Data Leakage Cascade (T12 + T3)
- [ ] Misconfigured Inter-Agent Monitoring (T8 + T10)
- [ ] Memory Poisoning across layers (T1)
- [ ] Tool Misuse via delegation (T2)
- [ ] Privilege Compromise via chaining (T3 + T14)
- [ ] Resource Overload across layers (T4)
- [ ] Hallucination Attacks (T5)
- [ ] Agent Communication Poisoning (T12)
- [ ] Temporal Manipulation / Time-Based Attacks (T6)
- [ ] Learning Model Poisoning (T1 + T7)
- [ ] Identity Spoofing / Impersonation (T9)
- [ ] Planning and Reflection Exploitation (T6 + T7)
- [ ] Excessive Agency / Permission Bypass via chained authorization (T3 + T14)
- [ ] RCE/Code execution chains across layers (T11)

---

## Navigation

| Previous | Up | Next |
|----------|-----|------|
| [03 - Mapping Matrix](./03-mapping-matrix.md) | [00 - Overview](./00-overview.md) | [05 - Agentic Risk Factors](./05-agentic-risk-factors.md) |

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*
