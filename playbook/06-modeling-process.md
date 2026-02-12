# 06 - MAESTRO Threat Modeling Process

**Version:** 1.0.0

This document describes the complete 10-phase threat modeling process aligned to the MAESTRO framework. The process includes dedicated phases for Threat Actor Analysis (Phase 3), Trust Boundary Analysis (Phase 4), Asset Flow Analysis (Phase 5), Code Validation (Phase 8), and Residual Risk Analysis (Phase 9).

---

## Table of Contents

1. [Phase Overview](#phase-overview)
2. [Phase 1: Business Context Analysis](#phase-1-business-context-analysis)
3. [Phase 2: Architecture Analysis](#phase-2-architecture-analysis)
4. [Phase 3: Threat Actor Analysis](#phase-3-threat-actor-analysis)
5. [Phase 4: Trust Boundary Analysis](#phase-4-trust-boundary-analysis)
6. [Phase 5: Asset Flow Analysis](#phase-5-asset-flow-analysis)
7. [Phase 6: Threat Identification](#phase-6-threat-identification)
8. [Phase 7: Mitigation Planning](#phase-7-mitigation-planning)
9. [Phase 8: Code Validation Analysis](#phase-8-code-validation-analysis)
10. [Phase 9: Residual Risk Analysis](#phase-9-residual-risk-analysis)
11. [Phase 10: Output Generation and Documentation](#phase-10-output-generation-and-documentation)
12. [Phase Dependency Flow](#phase-dependency-flow)
13. [Iterative and Incremental Threat Modeling](#iterative-and-incremental-threat-modeling)

---

## Phase Overview

| Phase | Name | Focus |
|-------|------|-------|
| 1 | Business Context Analysis | Understand business value, criticality, regulatory requirements, data sensitivity |
| 2 | Architecture Analysis | Map components to MAESTRO layers, document trust boundaries, identify data flows |
| 3 | Threat Actor Analysis | Identify potential adversaries, assess capabilities, motivation, resources |
| 4 | Trust Boundary Analysis | Define trust zones, identify crossing points, validate security controls at boundaries |
| 5 | Asset Flow Analysis | Track critical assets through the system, identify where sensitive data is created/stored/transmitted/processed |
| 6 | Threat Identification | Per-layer threat analysis using ASI taxonomy, extended catalog, 4 agentic risk factors, cross-layer analysis |
| 7 | Mitigation Planning | For each threat: preventive, detective, corrective, deterrent controls. Assess implementation status |
| 8 | Code Validation Analysis | Analyze source code against identified mitigations. Classify implementation status |
| 9 | Residual Risk Analysis | Assess remaining risk after mitigations. Document accepted risks, risk owners, review schedules |
| 10 | Output Generation and Documentation | Export threat model, create remediation roadmap, archive for comparison |

---

## Phase 1: Business Context Analysis

### Objective

Establish the business context surrounding the system under review. Understanding business value, criticality, regulatory requirements, and data sensitivity is essential before any technical analysis begins. This phase ensures the threat model is grounded in real-world business impact rather than abstract technical risk.

### Steps

1. **Identify the business function** -- What does this system do for the organization? What business process does it automate or support?
2. **Determine criticality** -- How critical is this system to business operations? What is the impact of downtime or compromise? Classify as Low / Medium / High / Critical.
3. **Document regulatory requirements** -- Identify all applicable regulations: SOX, GDPR, HIPAA, PCI-DSS, industry-specific standards. Note specific sections and requirements that apply.
4. **Assess data sensitivity** -- What categories of data does the system process? PII, financial records, health data, trade secrets, credentials? Classify data sensitivity levels.
5. **Identify stakeholders** -- Who are the business owners, data owners, compliance officers, and technical leads? Document the RACI matrix for security decisions.
6. **Define risk appetite** -- What level of risk is acceptable for this system? Are there hard constraints (e.g., zero tolerance for financial data loss)?
7. **Document business assumptions** -- Record assumptions about the operating environment, user base, deployment model, and intended use.

### Expected Output

- Business context document with: system purpose, criticality rating, regulatory requirements, data sensitivity classification, stakeholder map, risk appetite statement, and documented assumptions.

### Agentic Considerations

- **Non-Determinism**: Does the business context tolerate non-deterministic behavior? Financial and compliance systems typically require deterministic outcomes.
- **Autonomy**: What level of autonomous decision-making is acceptable given the business criticality? Higher criticality demands tighter human oversight.
- **Identity Management**: Are there regulatory requirements around identity propagation (e.g., SOX requires individual accountability for financial transactions)?
- **Agent-to-Agent Communication**: Does the business context involve multi-party data sharing that introduces additional regulatory obligations?

---

## Phase 2: Architecture Analysis

### Objective

Map all system components to the 7 MAESTRO layers (plus Cross-Layer), document trust boundaries between components, and identify all data flows. This phase creates the structural foundation for all subsequent threat analysis.

### Steps

1. **Inventory all components** -- Enumerate every component in the system: LLMs, data stores, tools, APIs, human interfaces, agent frameworks, infrastructure services, external integrations.
2. **Map components to MAESTRO layers** -- Assign each component to its primary MAESTRO layer using the Layer Mapping Template:
   - Layer 1: Foundation Model (LLMs, model APIs, inference endpoints)
   - Layer 2: Data Operations (vector DBs, RAG pipelines, document stores, prompt templates)
   - Layer 3: Agent Frameworks (agent runtime, MCP servers, tool definitions, workflow logic)
   - Layer 4: Deployment Infrastructure (compute, networking, storage, orchestration, messaging)
   - Layer 5: Evaluation & Observability (logging, dashboards, HITL interfaces, anomaly detection)
   - Layer 6: Security & Compliance (auth, RBAC, secrets management, policy engines)
   - Layer 7: Agent Ecosystem (external APIs, human users, other agents, third-party services)
3. **Note cross-layer components** -- Some components span multiple layers. Document these and their layer interactions.
4. **Document trust boundaries** -- Identify where trust levels change between components. Mark boundaries between internal/external, authenticated/unauthenticated, high-privilege/low-privilege zones.
5. **Map data flows** -- Trace how data moves through the system: ingestion, processing, storage, transmission, output. Note protocol, encryption, and authentication at each hop.
6. **Identify critical paths** -- Which data flows carry the most sensitive data or have the highest business impact?
7. **Document architecture assumptions** -- Record assumptions about component behavior, availability, and security posture.

### Expected Output

- Component inventory mapped to MAESTRO layers. Trust boundary diagram. Data flow diagram with security annotations. List of critical paths. Architecture assumptions document.

### Agentic Considerations

- **Non-Determinism**: Map where non-deterministic components (LLMs) interact with deterministic components (databases, APIs). These interfaces are high-risk points.
- **Autonomy**: Identify which components operate autonomously and which require human approval. Document the autonomy boundary.
- **Identity Management**: Map how identity (both user and agent) propagates through the architecture. Identify where identity context is lost or transformed.
- **Agent-to-Agent Communication**: Document all inter-agent communication channels, protocols, and trust assumptions.

---

## Phase 3: Threat Actor Analysis

### Objective

Identify and characterize potential adversaries who might target the system. Assess their capabilities, motivation, resources, and likely attack vectors. This phase is particularly important for agentic systems because the threat actor landscape includes novel categories like compromised agents and automated threats.

### Steps

1. **Enumerate threat actor categories** -- Consider each of the following categories and assess relevance to your system:
   - **External Attackers**: Cybercriminals, hacktivists, competitors seeking financial gain, disruption, or data theft. Capabilities range from script-kiddie to advanced persistent threats.
   - **Malicious Insiders**: Employees, contractors, or partners with legitimate access who act with malicious intent. They have knowledge of internal systems and existing credentials.
   - **Compromised Agents**: AI agents within the system that have been subverted through prompt injection, memory poisoning, plugin compromise, or supply chain attacks. These are unique to agentic systems.
   - **Nation-State Actors**: State-sponsored groups with advanced capabilities, significant resources, and strategic motivations. Relevant for critical infrastructure, defense, and high-value targets.
   - **Automated Threats**: Botnets, automated exploitation tools, and AI-powered attack agents that operate at machine speed without human direction. Includes adversarial AI systems targeting your agents.
2. **Assess capabilities per actor** -- For each relevant threat actor, document: technical sophistication, available resources (financial, computational, personnel), access to specialized tools, ability to conduct sustained campaigns.
3. **Assess motivation per actor** -- Document: primary motivation (financial, espionage, disruption, ideology), target assets, acceptable risk level for the attacker, persistence and determination.
4. **Map actors to attack surfaces** -- For each threat actor, identify which MAESTRO layers and components they are most likely to target and through which vectors.
5. **Prioritize threat actors** -- Rank by combination of capability, motivation, and opportunity. Focus subsequent analysis on the highest-priority actors.
6. **Set relevance flags** -- Mark each threat actor category as Relevant / Partially Relevant / Not Relevant to this system, with justification.

### Expected Output

- Threat actor catalog with: category, capability assessment, motivation, target assets, likely attack vectors, priority ranking, and relevance flags.

### Agentic Considerations

- **Non-Determinism**: Compromised agents exploit non-determinism to hide malicious behavior within the normal variance of LLM outputs. Threat actors who understand LLM behavior can craft attacks that look like normal non-deterministic variation.
- **Autonomy**: Automated threats can exploit autonomous agent behavior to cause damage at machine speed. A compromised agent with high autonomy is effectively a threat actor itself.
- **Identity Management**: Nation-state actors and sophisticated external attackers may target agent identity systems for impersonation. Compromised agents inherit the identity and permissions of the legitimate agent they replaced.
- **Agent-to-Agent Communication**: Compromised agents can poison inter-agent communications. Automated threats can inject themselves into agent communication channels.

---

## Phase 4: Trust Boundary Analysis

### Objective

Define trust zones within the system architecture, identify all crossing points where data or control flows between zones of different trust levels, and validate that appropriate security controls exist at each boundary. Trust boundaries are where most exploitable vulnerabilities occur, especially in agentic systems where agents frequently cross boundaries.

### Steps

1. **Define trust zones** -- Group components into zones based on trust level. Common zones include:
   - Public/untrusted zone (external users, internet-facing components)
   - DMZ/semi-trusted zone (API gateways, load balancers)
   - Application zone (agent frameworks, business logic)
   - Data zone (databases, vector stores, file storage)
   - Privileged zone (secrets management, IAM, administrative interfaces)
   - External integration zone (third-party APIs, MCP servers, other agents)
2. **Identify crossing points** -- For each pair of trust zones, identify every point where data or control flows between them. Document the protocol, authentication mechanism, and authorization check at each crossing point.
3. **Validate security controls at boundaries** -- For each crossing point, verify:
   - Authentication: Is the caller's identity verified?
   - Authorization: Is the caller permitted to perform this action?
   - Input validation: Is input sanitized and validated?
   - Output encoding: Is output properly encoded to prevent injection?
   - Encryption: Is data encrypted in transit?
   - Rate limiting: Are there controls against abuse?
   - Logging: Are boundary crossings logged for audit?
4. **Identify implicit trust assumptions** -- Document where the system implicitly trusts data or control signals without verification. These are high-risk points.
5. **Map agent boundary crossings** -- Specifically trace how agents cross trust boundaries. Do they carry user identity? Do they escalate privileges? Do they access resources in higher trust zones?
6. **Assess boundary strength** -- Rate each boundary as Strong / Moderate / Weak based on the controls in place.

### Expected Output

- Trust zone map. Crossing point inventory with security control assessment. List of implicit trust assumptions. Agent boundary crossing analysis. Boundary strength ratings.

### Agentic Considerations

- **Non-Determinism**: Agents may cross trust boundaries in unpredictable ways depending on LLM output. A non-deterministic routing decision could send sensitive data to an untrusted zone.
- **Autonomy**: Autonomous agents cross trust boundaries without per-action human approval, making boundary controls the primary defense. Weak boundaries plus high autonomy equals high risk.
- **Identity Management**: The "confused deputy" problem is fundamentally a trust boundary issue. An agent authenticated with a service account may cross boundaries that the initiating user should not be able to cross.
- **Agent-to-Agent Communication**: Inter-agent communication often crosses trust boundaries. Each agent-to-agent channel is a crossing point that needs explicit security controls.

---

## Phase 5: Asset Flow Analysis

### Objective

Track critical assets (data, credentials, control signals) through the entire system lifecycle. Identify where sensitive data is created, stored, transmitted, and processed. This phase builds on the architecture and trust boundary analysis to create a comprehensive map of asset exposure.

### Steps

1. **Inventory critical assets** -- Enumerate all valuable assets in the system:
   - Sensitive data: PII, financial records, health data, credentials, API keys
   - Business logic: system prompts, workflow definitions, business rules
   - Model assets: model weights, fine-tuning data, embeddings
   - Operational data: audit logs, configuration, session state
   - Agent state: memory, context, planning artifacts
2. **Trace asset lifecycle** -- For each critical asset, document where it is:
   - **Created**: Where does the asset originate? User input, API response, LLM generation, database query?
   - **Stored**: Where is the asset persisted? Database, file system, vector store, memory, cache?
   - **Transmitted**: How does the asset move between components? API calls, message queues, shared memory, file transfer?
   - **Processed**: Where is the asset transformed, analyzed, or used for decisions? LLM inference, business logic, data pipeline?
   - **Destroyed**: How and when is the asset deleted or expired? TTL, manual deletion, never?
3. **Map assets to trust zones** -- For each asset, note which trust zones it passes through during its lifecycle. Flag cases where sensitive assets enter lower-trust zones.
4. **Identify exposure points** -- Where are assets most vulnerable? Focus on:
   - Assets in transit between trust zones without encryption
   - Assets stored in shared locations accessible to multiple agents
   - Assets logged in plaintext (credentials, PII in audit logs)
   - Assets cached longer than necessary
5. **Assess protection controls** -- For each asset at each lifecycle stage, document what protections exist: encryption at rest, encryption in transit, access controls, masking, tokenization, expiration.
6. **Identify data lineage gaps** -- Where does visibility into the asset's location and state break down? These gaps are blind spots for security monitoring.

### Expected Output

- Critical asset inventory. Asset lifecycle maps. Asset-to-trust-zone mapping. Exposure point analysis. Protection control assessment. Data lineage gap analysis.

### Agentic Considerations

- **Non-Determinism**: LLM-generated content may inadvertently include sensitive data from its context. Non-deterministic outputs make it impossible to predict which assets will be exposed in agent responses.
- **Autonomy**: Autonomous agents may create, transmit, or store assets without human oversight. An agent that autonomously decides to cache sensitive data introduces unplanned exposure points.
- **Identity Management**: Agent credentials and tokens are critical assets themselves. Track where agent identity tokens are stored, transmitted, and how they expire.
- **Agent-to-Agent Communication**: Assets shared between agents may replicate across multiple storage locations, increasing the attack surface. Shared memory and inter-agent messages carry assets across trust boundaries.

---

## Phase 6: Threat Identification

### Objective

Perform comprehensive threat identification using the MAESTRO layered approach. Analyze each of the 7 MAESTRO layers independently, apply the ASI threat taxonomy (T1-T15) and extended threat catalog, consider all 4 agentic risk factors at each layer, and then perform cross-layer analysis to discover emergent threats.

### Steps

1. **Per-layer analysis** -- For each of the 7 MAESTRO layers, perform the following:
   a. Review the ASI threats mapped to this layer (from the Layer-to-ASI Threat Mapping Matrix).
   b. Review the extended threat catalog for this layer (threats beyond T1-T15).
   c. Consider all 4 agentic risk factors (Non-Determinism, Autonomy, Identity Management, Agent-to-Agent Communication).
   d. Assess threat actors identified in Phase 3 against this layer's attack surface.
   e. Evaluate trust boundary crossings identified in Phase 4 that involve this layer.
   f. Consider asset exposure points from Phase 5 that exist within this layer.
   g. For each applicable threat, document:
      - Threat ID and name (ASI taxonomy ID where applicable)
      - Threat source and prerequisites
      - Threat action (attack scenario)
      - Affected components
      - Impact (confidentiality, integrity, availability, financial, reputational)
      - ASI taxonomy mapping
      - Severity (Low / Medium / High / Critical)
      - Likelihood (Unlikely / Possible / Likely / Very Likely)
      - Attack vector (Network / Adjacent / Local / Physical)
      - Attack complexity (Low / High)

2. **Layer-by-layer checklist** -- Use the Threat Identification Checklist:
   - **Layer 1 (Foundation Model)**: Prompt injection, hallucination risk, model alignment, non-deterministic output, model poisoning, model stealing, tool hijacking, parameter pollution.
   - **Layer 2 (Data Operations)**: RAG poisoning, semantic drift, RAG manipulation, data exfiltration, shared memory poisoning, document integrity, external data injection.
   - **Layer 3 (Agent Frameworks)**: Tool misuse, intent breaking, workflow bypass, code injection, plugin compromise, inter-agent protocol security, schema mismatch, cross-client interference, runaway agents.
   - **Layer 4 (Deployment Infrastructure)**: Over-privileged IAM, credential exposure, network exposure, DDoS, orchestration compromise, code signing, VPC isolation.
   - **Layer 5 (Evaluation & Observability)**: Audit completeness, non-repudiation, log manipulation, HITL capacity, drift detection, anomaly detection, degradation masking.
   - **Layer 6 (Security & Compliance)**: RBAC enforcement, separation of duties, authorization propagation, regulatory compliance, dynamic policy enforcement, runtime guardrails, data residency, secrets management.
   - **Layer 7 (Agent Ecosystem)**: Identity spoofing, human trust manipulation, rogue servers, dependency exploitation, agent trust assumptions, supply chain risk.

3. **Cross-layer analysis** -- After completing all per-layer analyses:
   a. Walk through each cross-layer threat pattern:
      - Pattern 1: Hallucination -> RAG -> Tool Misuse (L1 + L2 + L3)
      - Pattern 2: Framework Exploit -> Infrastructure -> Compliance Bypass (L3 + L4 + L6)
      - Pattern 3: Data Poisoning -> Agent Action -> Ecosystem Propagation (L2 + L3 + L7)
      - Pattern 4: Log Manipulation -> Security Evasion -> Undetected Fraud (L3 + L5 + L6)
      - Pattern 5: Cascading Trust Failure (All Layers)
      - Pattern 6: Confused Deputy / Excessive Agency (L3 + L6 + L7)
      - Pattern 7: HITL Overwhelm + Trust Manipulation (L5 + L7)
   b. Trace data flows across layer boundaries for privilege escalation paths.
   c. Identify trust chain paths from low-trust to high-trust zones.
   d. Look for confused deputy and excessive agency patterns.
   e. Consider cascading failure scenarios.
   f. Review the Cross-Layer Threats Checklist.

4. **Consolidate and deduplicate** -- Merge findings from per-layer and cross-layer analysis. Remove duplicates. Assign final severity and likelihood ratings.

### Expected Output

- Complete threat register with: threat ID, name, description, affected layers, affected components, ASI mapping, severity, likelihood, attack vector, attack complexity, agentic risk factors involved. Cross-layer threat scenarios with attack chains documented.

### Agentic Considerations

- **Non-Determinism**: Threats that exploit non-determinism are often the hardest to detect and reproduce. Pay special attention to threats rated "Possible" -- they may be more "Likely" than deterministic analysis suggests.
- **Autonomy**: Autonomous execution amplifies the impact of every threat. A threat that requires human intervention to propagate is less severe than one that propagates autonomously.
- **Identity Management**: Identity-based threats (T3, T9, T14) are particularly dangerous in agentic systems because agents often operate with elevated, persistent credentials.
- **Agent-to-Agent Communication**: Communication-based threats (T12, T13) can turn a single compromised agent into a system-wide compromise through lateral movement.

---

## Phase 7: Mitigation Planning

### Objective

For each identified threat, design mitigations across four control categories: Preventive (stop threats from occurring), Detective (identify threats in progress), Corrective (recover from threats), and Deterrent (discourage threat actors). Assess implementation status of each mitigation against the current system state.

### Steps

1. **Categorize mitigations** -- For each threat, identify controls in all four categories:
   - **Preventive**: Controls that prevent the threat from occurring. Examples: input validation, RBAC, encryption, sandboxing, guardrails.
   - **Detective**: Controls that detect the threat while it is in progress or after it has occurred. Examples: anomaly detection, audit logging, behavioral monitoring, integrity checks.
   - **Corrective**: Controls that enable recovery after a threat materializes. Examples: rollback mechanisms, incident response procedures, backup/restore, circuit breakers.
   - **Deterrent**: Controls that discourage threat actors from attempting the attack. Examples: visible logging/audit trails, legal/compliance frameworks, honeypots, attribution capabilities.

2. **Assess implementation status** -- For each mitigation, classify its current state:
   - **Implemented**: The control is in place and functioning.
   - **In Progress**: The control is being implemented.
   - **Identified**: The control has been identified as needed but work has not started.
   - **Will Not Action**: The control has been evaluated and the risk is accepted.

3. **Evaluate mitigation effectiveness** -- Rate each mitigation as Low / Medium / High effectiveness against the targeted threat.

4. **Estimate implementation cost** -- Rate each mitigation as Low / Medium / High cost to implement and maintain.

5. **Prioritize mitigations** -- Rank by: (threat severity x threat likelihood x implementation gap) / (implementation cost). High-severity threats with no mitigations get highest priority.

6. **Map mitigations to threats** -- Create a threat-to-mitigation mapping matrix. Ensure every Critical and High severity threat has at least one Preventive and one Detective control.

7. **Identify mitigation gaps** -- Flag threats that lack adequate mitigations, especially:
   - Critical threats with no Preventive controls
   - High threats with no Detective controls
   - Any threat with only a single mitigation (single point of failure)

### Expected Output

- Mitigation register with: mitigation ID, description, type (Preventive/Detective/Corrective/Deterrent), targeted threats, implementation status, effectiveness rating, cost estimate. Threat-to-mitigation mapping matrix. Prioritized mitigation backlog. Gap analysis.

### Agentic Considerations

- **Non-Determinism**: Preventive controls must account for non-deterministic behavior. A guardrail that works 99% of the time is not adequate for critical threats. Consider defense-in-depth with multiple overlapping controls.
- **Autonomy**: Detective controls must operate at the speed of autonomous agents. Human-in-the-loop detection is too slow for high-autonomy agents; automated detection with automated circuit breakers is needed.
- **Identity Management**: Mitigations for identity-based threats must enforce least privilege, credential rotation, and identity propagation throughout the entire call chain.
- **Agent-to-Agent Communication**: Mitigations for communication-based threats should include mutual authentication, message integrity verification, and anomaly detection on inter-agent traffic.

---

## Phase 8: Code Validation Analysis

### Applicability Gate

Before beginning Phase 8, determine whether code validation is applicable to this engagement:

| Source Code Access | Action |
|-------------------|--------|
| **Full access** to application source code and infrastructure-as-code | Proceed with full Phase 8 analysis |
| **Partial access** (configuration files, IAM policies, but not application code) | Proceed with Phase 8 scoped to configuration and infrastructure validation only |
| **No access** (vendor-supplied agents, SaaS platforms, closed-source systems) | Skip Phase 8. Document "Code Validation: Not Applicable — no source code access" and proceed to Phase 9. Residual risk assessment in Phase 9 should note that mitigations are unverified. |

If Phase 8 is skipped, all mitigations from Phase 7 should be treated as "Planned" (not "Implemented") in Phase 9 unless vendor attestations or third-party audit reports provide evidence of implementation.

### Objective

Analyze the actual source code of the system against the mitigations identified in Phase 7. Determine whether each mitigation is actually implemented in the codebase, partially implemented, or missing entirely. This phase bridges the gap between theoretical security design and actual implementation.

### Steps

1. **Map mitigations to code artifacts** -- For each mitigation, identify which source files, configuration files, or infrastructure-as-code templates should contain the implementation.

2. **Analyze source code** -- Review the identified code artifacts to determine implementation status:
   - **Implemented**: Code evidence clearly shows the mitigation is in place and functioning. Document the specific files, functions, and line numbers.
   - **Partially Implemented**: Some aspects of the mitigation exist but there are gaps. Document what is present and what is missing.
   - **Not Implemented**: No code evidence of the mitigation exists. Document where the implementation should be added.

3. **Validate configuration** -- Check infrastructure configuration, IAM policies, security group rules, environment variables, and secrets management for security controls that are configuration-based rather than code-based.

4. **Check for anti-patterns** -- Look for code patterns that undermine mitigations:
   - Hardcoded credentials or secrets
   - Overly broad IAM permissions
   - Missing input validation
   - Disabled security features
   - TODO/FIXME comments indicating deferred security work
   - Exception handling that silently swallows errors

5. **Assess test coverage** -- Determine whether security-relevant code paths have adequate test coverage. Untested security controls are unreliable controls.

6. **Document findings** -- For each mitigation, produce a code validation finding with: implementation status, specific code references, gap description, and remediation recommendation.

### Expected Output

- Code validation report with: mitigation-to-code mapping, implementation status per mitigation (Implemented / Partially Implemented / Not Implemented), specific code references, anti-pattern findings, test coverage assessment, prioritized remediation recommendations.

### Agentic Considerations

- **Non-Determinism**: Validate that code handles non-deterministic LLM outputs gracefully. Check for output validation, retry logic with different behavior on retry, and fallback paths.
- **Autonomy**: Verify that autonomy boundaries are enforced in code, not just documented. Check for human-approval gates, spending limits, and circuit breakers in the actual execution path.
- **Identity Management**: Verify that identity propagation code actually passes user context through agent calls to backend services. Check for places where user identity is dropped and replaced by service account identity.
- **Agent-to-Agent Communication**: Validate that inter-agent communication code includes authentication, integrity checks, and encryption. Check for unauthenticated endpoints or unvalidated messages.

---

## Phase 9: Residual Risk Analysis

### Objective

Assess the risk that remains after all planned mitigations are applied. Not all risk can be eliminated; this phase documents accepted risks, assigns risk owners, and establishes review schedules. This is critical for informed risk acceptance decisions by business stakeholders.

### Steps

1. **Calculate residual risk per threat** -- For each threat:
   a. Start with the inherent risk (severity x likelihood from Phase 6).
   b. Factor in the mitigation effectiveness from Phase 7.
   c. Factor in the actual implementation status from Phase 8.
   d. Determine the residual risk level (Critical / High / Medium / Low).
   - Threats with effective, implemented mitigations have lower residual risk.
   - Threats with only partially implemented or planned mitigations retain higher residual risk.
   - Threats with no mitigations retain their full inherent risk as residual risk.

2. **Classify residual risk disposition** -- For each residual risk, classify as:
   - **Mitigated**: Risk reduced to acceptable level through implemented controls.
   - **Accepted**: Risk acknowledged and accepted by an authorized risk owner. Document the business justification.
   - **Transferred**: Risk transferred to a third party (insurance, vendor SLA, shared responsibility model).
   - **Deferred**: Risk mitigation postponed to a future date. Document the timeline and interim compensating controls.

3. **Assign risk owners** -- Every accepted or deferred risk must have a named risk owner who is accountable for monitoring and reviewing the risk. Risk owners should be business stakeholders, not only technical staff.

4. **Establish review schedules** -- Set review dates for all residual risks:
   - Critical residual risks: review monthly
   - High residual risks: review quarterly
   - Medium residual risks: review semi-annually
   - Low residual risks: review annually
   - Any residual risk: review immediately when the threat landscape changes

5. **Document risk acceptance** -- For each accepted risk, document:
   - Risk description and current residual level
   - Business justification for acceptance
   - Compensating controls (if any)
   - Risk owner name and role
   - Review schedule
   - Conditions that would require re-evaluation (trigger events)

6. **Aggregate risk posture** -- Summarize the overall residual risk posture of the system. Identify the top 5 residual risks. Assess whether the aggregate residual risk is within the organization's risk appetite (defined in Phase 1).

### Expected Output

- Residual risk register with: threat ID, inherent risk, mitigation effectiveness, implementation status, residual risk level, disposition, risk owner, review schedule. Risk acceptance documentation. Aggregate risk posture summary. Top residual risks dashboard.

### Agentic Considerations

- **Non-Determinism**: Residual risk from non-deterministic behavior can never be fully eliminated. Document this as a systemic accepted risk with ongoing monitoring as the compensating control.
- **Autonomy**: High-autonomy systems inherently carry higher residual risk because the speed of autonomous action limits the effectiveness of detective and corrective controls. Quantify this residual risk explicitly.
- **Identity Management**: Residual risk from identity management gaps (confused deputy, privilege escalation) tends to be high-severity. These should rarely be accepted without strong compensating controls.
- **Agent-to-Agent Communication**: Residual risk from inter-agent communication is amplified by the number of agents and communication channels. As the system scales, residual risk in this area increases non-linearly.

---

## Phase 10: Output Generation and Documentation

### Objective

Export the complete threat model in structured formats (JSON and Markdown), create a prioritized remediation roadmap, and archive the model for future comparison. This phase produces the deliverables that stakeholders use for decision-making and that serve as the baseline for future threat model updates.

### Steps

1. **Export threat model** -- Generate the comprehensive threat model export containing:
   - Business context summary (from Phase 1)
   - Architecture overview with MAESTRO layer mapping (from Phase 2)
   - Threat actor analysis (from Phase 3)
   - Trust boundary analysis (from Phase 4)
   - Asset flow analysis (from Phase 5)
   - Complete threat register (from Phase 6)
   - Mitigation register with implementation status (from Phase 7)
   - Code validation findings (from Phase 8)
   - Residual risk register (from Phase 9)
   - Export in both JSON (machine-readable) and Markdown (human-readable) formats.

2. **Create remediation roadmap** -- Build a prioritized, time-phased remediation plan:
   - **Immediate (0-30 days)**: Critical threats with Not Implemented mitigations.
   - **Short-term (30-90 days)**: High threats with Not Implemented or Partially Implemented mitigations.
   - **Medium-term (90-180 days)**: Medium threats and remaining partially implemented mitigations.
   - **Long-term (180+ days)**: Low threats, architectural improvements, defense-in-depth enhancements.
   - For each remediation item: description, targeted threats, estimated effort, responsible team, dependencies.

3. **Generate executive summary** -- Create a one-page summary for non-technical stakeholders covering: system overview, threat landscape summary, top 5 risks, overall risk posture, key recommendations, and investment required.

4. **Produce remediation report** -- Generate a detailed remediation report with:
   - Findings grouped by severity
   - Code-level remediation guidance
   - Implementation priority and sequencing
   - Estimated effort and resource requirements

5. **Archive for comparison** -- Store the threat model with a timestamp for future comparison. Include:
   - Version number and date
   - Analyst/team identification
   - Scope definition (what was included and excluded)
   - Delta from previous threat model (if exists): new threats, resolved threats, changed risk levels

6. **Establish update triggers** -- Document events that should trigger a threat model update:
   - Major architecture changes (new agents, new tools, new integrations)
   - New regulatory requirements
   - Security incidents or near-misses
   - Significant changes to threat landscape
   - Quarterly scheduled review (at minimum)

### Expected Output

- Comprehensive threat model export (JSON + Markdown). Prioritized remediation roadmap. Executive summary. Detailed remediation report. Archived threat model with version metadata. Update trigger documentation.

### Agentic Considerations

- **Non-Determinism**: Document in the export that all risk ratings for LLM-dependent components carry an inherent uncertainty margin due to non-determinism. Recommend periodic re-testing.
- **Autonomy**: The remediation roadmap should explicitly call out autonomy-reduction measures (adding human approval gates) as quick wins for risk reduction while longer-term technical mitigations are implemented.
- **Identity Management**: Include identity architecture recommendations in the remediation roadmap. Identity improvements often have cascading positive effects across multiple threats.
- **Agent-to-Agent Communication**: As the agent ecosystem evolves, the threat model must be updated. Include agent ecosystem changes as a primary update trigger.

---

## Phase Dependency Flow

```
Phase 1 (Business Context)
    |
    v
Phase 2 (Architecture)
    |
    +---> Phase 3 (Threat Actors)  \
    +---> Phase 4 (Trust Boundaries) }-- complete all three before Phase 6
    +---> Phase 5 (Asset Flows)    /
    |
    v
Phase 6 (Threat Identification) <--- consumes outputs from Phases 1-5
    |
    v
Phase 7 (Mitigation Planning)
    |
    v
Phase 8 (Code Validation)
    |
    v
Phase 9 (Residual Risk Analysis)
    |
    v
Phase 10 (Output Generation)
```

Phases 3, 4, and 5 can be performed in **any order or in parallel** after Phase 2 is complete. All three feed into Phase 6. Phases 6 through 10 are strictly sequential.

---

## Iterative and Incremental Threat Modeling

A threat model is a living artifact. Re-enter earlier phases when the system changes:

- **New components or integrations** → restart from Phase 2 (Architecture Analysis). Map new components to MAESTRO layers and propagate through Phases 3-10.
- **New threat actors or regulatory requirements** → restart from Phase 3 (Threat Actors). Updated actor profiles may surface threats that were previously out of scope.
- **Code changes to mitigations** → re-run Phase 8 (Code Validation). Confirm that existing mitigations still pass validation after refactoring or dependency updates.
- **Significant architecture changes** → full re-run from Phase 1 if the business context or risk appetite has shifted.

For incremental updates, scope the re-analysis using the MVTM checklist from [12 - Quick Reference](./12-quick-reference.md). Walk each MVTM item and mark whether the change affects it. Only re-analyze affected items.

Maintain the threat model in version control alongside the codebase. Track deltas between versions: new threats added, threats closed, mitigations changed. Define review triggers — at minimum, re-review on major releases, after security incidents, and quarterly for high-risk systems.

**Phase dependency note:** Phase 5 (Asset Flows) may begin in parallel with Phase 4 (Trust Boundaries) but requires trust zone definitions before completing asset-to-zone mapping.

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

## Navigation

| Previous | Up | Next |
|----------|-----|------|
| [05 - Agentic Risk Factors](./05-agentic-risk-factors.md) | [00 - Overview](./00-overview.md) | [07 - Templates](./07-templates.md) |

See also: [09 - Mitigation Catalog](./09-mitigation-catalog.md) | [10 - Case Studies](./10-case-studies.md) | [11 - Framework Integration](./11-framework-integration.md)
