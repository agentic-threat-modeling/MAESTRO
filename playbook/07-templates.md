# 07 - Templates

**Version:** 1.0.0

This section provides reusable templates for each major artifact produced during a MAESTRO threat modeling engagement. Copy these templates into your working documents and fill them in as you progress through each phase.

---

## Table of Contents

1. [Layer Mapping Template](#layer-mapping-template)
2. [Threat Card Template](#threat-card-template)
3. [Assumption Template](#assumption-template)
4. [Business Context Template](#business-context-template)
5. [Mitigation Card Template](#mitigation-card-template)
6. [Threat Actor Template](#threat-actor-template)
7. [Trust Boundary Template](#trust-boundary-template)
8. [Asset Flow Template](#asset-flow-template)
9. [Code Validation Template](#code-validation-template)
10. [Residual Risk Template](#residual-risk-template)
11. [Executive Summary Template](#executive-summary-template)
12. [Remediation Roadmap Template](#remediation-roadmap-template)
13. [Threat Register Summary Template](#threat-register-summary-template)

---

## Layer Mapping Template

Use this template to map your system's components to MAESTRO layers:

| MAESTRO Layer | System Components & Features | Notes |
|---------------|------------------------------|-------|
| 1. Foundation Models | [LLM name, API/self-hosted, version] | [Model ownership, alignment, fine-tuning] |
| 2. Data Operations | [Vector DB, RAG pipeline, document stores, data sources] | [Data freshness, access controls, indexing] |
| 3. Agent Frameworks | [Framework name, MCP servers, tool definitions, workflow logic] | [Autonomy level, tool access, planning] |
| 4. Deployment Infrastructure | [Compute, networking, storage, orchestration, messaging] | [IAM, network segmentation, service accounts] |
| 5. Evaluation & Observability | [Logging, dashboards, HITL interfaces, anomaly detection] | [Audit completeness, review capacity] |
| 6. Security & Compliance | [Auth, RBAC, secrets mgmt, policy engines, regulations] | [Compliance requirements, enforcement gaps] |
| 7. Agent Ecosystem | [External APIs, human users, other agents, third-party services] | [Trust assumptions, communication protocols] |

**Instructions:** For each layer, list every component in your system that operates at that layer. A single component may appear in multiple layers (e.g., an MCP server appears in L3 for tool definitions and L4 for its runtime infrastructure). The Notes column should capture initial observations about security-relevant properties.

---

## Threat Card Template

Use this template for each identified threat. One card per threat.

| Field | Value |
|-------|-------|
| Threat ID | [T#] |
| Threat Name | [Name] |
| MAESTRO Layer(s) | [L1-L7, Cross-Layer] |
| ASI Mapping | [T1-T15 or Extended] |
| STRIDE Category | [S/T/R/I/D/E] |
| Description | [What the threat is] |
| Attack Vector | [Network/Adjacent/Local/Physical] |
| Attack Complexity | [Low/High] |
| Severity | [Low/Medium/High/Critical] |
| Likelihood | [Unlikely/Possible/Likely/Very Likely] |
| Agentic Factors | [Which of the 4 factors apply] |
| Affected Components | [List] |
| Prerequisites | [What attacker needs] |
| Impact | [What happens if exploited] |
| Mitigations | [Link to mitigation IDs] |

**Field Guidance:**

- **Threat ID**: Sequential identifier (e.g., T1, T2, T3). Use a prefix to distinguish from ASI taxonomy IDs if needed (e.g., PROJ-T1).
- **MAESTRO Layer(s)**: One or more layers. Cross-layer threats should list all involved layers and note "Cross-Layer."
- **ASI Mapping**: Map to the OWASP ASI taxonomy (T1-T15) where applicable. Use "Extended" for threats not covered by the base taxonomy.
- **STRIDE Category**: Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, or Elevation of Privilege.
- **Attack Vector**: Network (remotely exploitable), Adjacent (requires network adjacency), Local (requires local access), Physical (requires physical access).
- **Attack Complexity**: Low (no special conditions needed) or High (requires specific configuration, timing, or circumstances).
- **Severity**: See the [Risk Scoring Guide](../guides/risk-scoring.md) for definitions.
- **Likelihood**: See the [Risk Scoring Guide](../guides/risk-scoring.md) for definitions.
- **Agentic Factors**: The four factors are: (1) Non-Determinism, (2) Autonomy, (3) Identity Management, (4) Agent-to-Agent Communication.
- **Prerequisites**: What the attacker must already have or know to attempt this attack.
- **Impact**: Concrete consequences -- data loss, financial exposure, compliance violation, safety risk, etc.
- **Mitigations**: Reference mitigation IDs (e.g., M1, M3) that address this threat.

---

## Assumption Template

Use this template to document each assumption made during the threat model.

| Field | Value |
|-------|-------|
| Assumption ID | [A#] |
| Description | [The assumption] |
| Rationale | [Why this is assumed] |
| Impact if Wrong | [What happens if assumption is invalid] |
| Validation Method | [How to verify] |
| Status | [Validated/Unvalidated/Invalidated] |

**Field Guidance:**

- **Assumption ID**: Sequential identifier (e.g., A1, A2, A3).
- **Description**: A clear, falsifiable statement. Avoid vague assumptions. Good: "All MCP tool calls are authenticated via short-lived tokens." Bad: "The system is mostly secure."
- **Rationale**: Why this assumption was made -- based on documentation, team interviews, code review, or architecture diagrams.
- **Impact if Wrong**: What threats become relevant, or what risk scores change, if this assumption turns out to be false. This is the most important field because it drives re-evaluation priority.
- **Validation Method**: How to confirm the assumption -- code inspection, penetration testing, configuration audit, vendor documentation review, etc.
- **Status**: Validated (confirmed true), Unvalidated (not yet confirmed), or Invalidated (confirmed false -- update the threat model accordingly).

**Best Practices:**
- Document assumptions as early as Phase 1 (Business Context) and continue through all phases.
- Revisit assumptions at the end of the engagement. Invalidated assumptions should trigger re-analysis of affected threats.
- High-impact unvalidated assumptions should be flagged as risks in their own right.

---

## Business Context Template

Use this template at the start of Phase 1 to capture the business context for the system under review.

| Field | Value |
|-------|-------|
| Application Name | |
| Business Domain | |
| Data Classification | [Public/Internal/Confidential/Restricted] |
| Regulatory Requirements | [SOX/GDPR/HIPAA/etc.] |
| Criticality | [Low/Medium/High/Critical] |
| User Base | [Internal/External/Both] |
| Agent Type | [Single/Multi-Agent] |
| Autonomy Level | [Human-approval-per-action / Human-in-loop / Fully autonomous] |

**Field Guidance:**

- **Application Name**: The system or project being threat modeled.
- **Business Domain**: The industry or functional area (e.g., Finance, Healthcare, DevOps, Customer Support).
- **Data Classification**: The highest classification of data the system processes. This drives many downstream decisions about threat severity and required mitigations.
- **Regulatory Requirements**: All applicable regulations. List even those that "probably" apply -- validation is an assumption to document separately.
- **Criticality**: Business impact if the system is unavailable or compromised. Critical = revenue-impacting or safety-related; Low = internal convenience tool.
- **User Base**: Internal (employees only), External (customers/public), or Both. External-facing systems have a broader threat actor pool.
- **Agent Type**: Single-Agent or Multi-Agent. This determines which MAESTRO layers and threat patterns receive the most focus. See the [Single-Agent Guide](../guides/single-agent.md) for systems with only one agent.
- **Autonomy Level**: Human-approval-per-action (agent proposes, human approves each step), Human-in-loop (agent acts autonomously but human can intervene), or Fully autonomous (agent acts without human oversight). Higher autonomy = higher risk from agentic factors.

**Example (filled in):**

| Field | Value |
|-------|-------|
| Application Name | Financial Document Processing Agent |
| Business Domain | Finance / Accounting |
| Data Classification | Confidential |
| Regulatory Requirements | SOX, GDPR |
| Criticality | High |
| User Base | Internal |
| Agent Type | Single |
| Autonomy Level | Human-in-loop |

---

## Mitigation Card Template

Use this template alongside the Threat Card to document each mitigation.

| Field | Value |
|-------|-------|
| Mitigation ID | [M#] |
| Mitigation Name | [Name] |
| Type | [Preventive/Detective/Corrective/Deterrent] |
| Description | [What the mitigation does] |
| Addresses Threats | [List of Threat IDs] |
| Cost | [Low/Medium/High] |
| Effectiveness | [Low/Medium/High] |
| Implementation Status | [Not Implemented/Partially Implemented/Implemented] |
| Owner | [Team or individual responsible] |

**Field Guidance:**

- **Type**: Preventive (stops the attack), Detective (identifies the attack in progress or after the fact), Corrective (recovers from the attack), Deterrent (discourages the attacker).
- **Cost**: Relative implementation effort -- Low (configuration change or policy update), Medium (engineering work within a sprint), High (significant architecture change or new tooling).
- **Effectiveness**: How much this mitigation reduces risk -- Low (marginal reduction), Medium (meaningful reduction), High (largely eliminates the threat).
- **Addresses Threats**: List all Threat IDs this mitigation applies to. A single mitigation often addresses multiple threats.

---

## Threat Actor Template

Use this template during Phase 3 to profile each threat actor relevant to your system.

| Field | Value |
|-------|-------|
| Actor ID | [TA#] |
| Actor Category | [External Attacker/Malicious Insider/Compromised Agent/Nation-State/Automated Threat] |
| Capability | [Low/Moderate/High/Advanced] |
| Motivation | [Financial/Espionage/Disruption/Ideology/Opportunistic] |
| Target Assets | [List] |
| Likely Attack Vectors | [List] |
| Target MAESTRO Layers | [L1-L7] |
| Relevance | [Relevant/Partially Relevant/Not Relevant] |
| Priority | [Low/Medium/High/Critical] |

**Field Guidance:**

- **Actor ID**: Sequential identifier (e.g., TA1, TA2, TA3).
- **Actor Category**: Selected from the Phase 3 enumeration of threat actor types. External Attacker covers unauthenticated adversaries; Malicious Insider covers employees or contractors with legitimate access; Compromised Agent represents an agent within the system that has been subverted; Nation-State covers advanced persistent threats with significant resources; Automated Threat covers botnets, scanners, and autonomous exploit tools.
- **Capability**: Technical sophistication of the actor -- Low (script-level), Moderate (skilled individual), High (organized team with custom tooling), Advanced (nation-state or equivalent resources).
- **Motivation**: The primary driver behind the actor's actions. An actor may have multiple motivations; list the dominant one first.
- **Target Assets**: Specific data, credentials, models, or system components the actor is likely to pursue.
- **Likely Attack Vectors**: The methods the actor would use -- prompt injection, supply chain compromise, credential theft, social engineering, etc.
- **Target MAESTRO Layers**: Which layers the actor is most likely to attack, based on their capability and motivation.
- **Relevance**: Whether this actor category is relevant to the system under review, given its exposure, data classification, and business domain.
- **Priority**: Overall priority for this actor in the threat model, combining relevance, capability, and motivation.

**Best Practices:**
- For agentic systems, always consider "Compromised Agent" as a threat actor. An agent that has been manipulated via prompt injection or poisoned data acts as an insider threat with the agent's full set of permissions.
- Revisit actor profiles when system exposure changes (e.g., moving from internal to external deployment).

---

## Trust Boundary Template

Use this template during Phase 4 to document each trust boundary crossing in the system.

| Field | Value |
|-------|-------|
| Boundary ID | [TB#] |
| Source Zone | [Zone name and trust level] |
| Destination Zone | [Zone name and trust level] |
| Crossing Components | [Components involved] |
| Protocol | [Communication protocol] |
| Authentication | [Mechanism or "None"] |
| Authorization | [Mechanism or "None"] |
| Input Validation | [Yes/No/Partial] |
| Encryption in Transit | [Yes/No] |
| Logging | [Yes/No/Partial] |
| Boundary Strength | [Strong/Moderate/Weak] |
| Associated Threats | [Threat IDs] |

**Field Guidance:**

- **Boundary ID**: Sequential identifier (e.g., TB1, TB2, TB3).
- **Source Zone**: The lower-trust side of the boundary crossing. Include both the zone name (e.g., "Public Internet", "Agent Sandbox", "Internal Network") and its trust level.
- **Destination Zone**: The higher-trust side of the boundary crossing. Include both the zone name and its trust level.
- **Crossing Components**: The specific components on each side of the boundary (e.g., "External API Client → MCP Server", "Agent A → Agent B").
- **Protocol**: The communication protocol used for the crossing (e.g., HTTPS, gRPC, WebSocket, stdio, SSE).
- **Authentication**: The mechanism used to verify identity at the boundary -- OAuth 2.0, mTLS, API key, JWT, or "None" if no authentication is present.
- **Authorization**: The mechanism used to enforce permissions -- RBAC, ABAC, capability tokens, or "None" if no authorization is enforced.
- **Input Validation**: Whether data crossing the boundary is validated -- Yes (schema validation, sanitization), No (raw pass-through), or Partial (some fields validated).
- **Encryption in Transit**: Whether the communication channel is encrypted.
- **Logging**: Whether boundary crossings are logged for audit -- Yes (full logging), No (no logging), or Partial (some events logged).
- **Boundary Strength**: Overall assessment of the boundary's security posture -- Strong (authenticated, authorized, validated, encrypted, logged), Moderate (some controls missing), Weak (minimal or no controls).
- **Associated Threats**: Threat IDs from Phase 6 that target or exploit this boundary crossing.

**Best Practices:**
- Every agent-to-agent communication channel should be documented as a boundary crossing, even if both agents are within the same deployment.
- Pay special attention to boundaries where untrusted input (user prompts, external data) enters the system.

---

## Asset Flow Template

Use this template during Phase 5 to trace each critical asset through the system lifecycle.

| Field | Value |
|-------|-------|
| Asset ID | [AF#] |
| Asset Name | [Name] |
| Classification | [Public/Internal/Confidential/Restricted] |
| Created At | [Component/Layer] |
| Stored At | [Component(s)] |
| Transmitted Via | [Protocol/Channel] |
| Processed By | [Component(s)] |
| Destroyed | [How/When or "Never"] |
| Trust Zones Traversed | [List] |
| Exposure Points | [List] |
| Protection Controls | [Encryption/ACL/Masking/etc.] |

**Field Guidance:**

- **Asset ID**: Sequential identifier (e.g., AF1, AF2, AF3).
- **Asset Name**: A descriptive name for the asset (e.g., "User PII", "Agent Credentials", "Model Weights", "RAG Index", "Prompt Templates").
- **Classification**: Data classification level -- Public (no impact if disclosed), Internal (limited internal impact), Confidential (significant business impact), Restricted (severe regulatory or safety impact). Classification drives protection requirements throughout the asset lifecycle.
- **Created At**: The component and MAESTRO layer where the asset is first created or ingested into the system.
- **Stored At**: All components where the asset is persisted -- databases, caches, vector stores, file systems, logs, etc.
- **Transmitted Via**: The protocols and channels used to move the asset between components (e.g., HTTPS, gRPC, message queue, shared memory).
- **Processed By**: All components that read, transform, or act on the asset.
- **Destroyed**: How and when the asset is removed from the system. "Never" indicates the asset is retained indefinitely, which should be flagged for review.
- **Trust Zones Traversed**: The trust zones the asset passes through during its lifecycle, referencing boundary IDs from Phase 4 where applicable.
- **Exposure Points**: Locations where the asset is at elevated risk -- logging systems, error messages, API responses, debug interfaces, third-party services.
- **Protection Controls**: The specific controls applied to protect the asset -- encryption at rest, encryption in transit, access control lists, data masking, tokenization, etc.

**Best Practices:**
- Always trace agent credentials and tokens as critical assets. These are high-value targets that grant attackers the agent's full set of permissions.
- Pay special attention to assets that cross trust boundaries or are stored in multiple locations.
- Assets classified as Confidential or Restricted should have protection controls at every stage of the lifecycle.

---

## Code Validation Template

Use this template during Phase 8 to validate that planned mitigations are actually implemented in the codebase.

| Field | Value |
|-------|-------|
| Validation ID | [CV#] |
| Mitigation ID | [M# from Phase 7] |
| Expected Code Artifact | [File/module/config] |
| Implementation Status | [Implemented/Partially Implemented/Not Implemented] |
| Code Evidence | [File path, function, line numbers] |
| Gaps Found | [Description of gaps] |
| Anti-Patterns Detected | [List] |
| Test Coverage | [Covered/Partial/None] |
| Remediation Priority | [Low/Medium/High/Critical] |

**Field Guidance:**

- **Validation ID**: Sequential identifier (e.g., CV1, CV2, CV3).
- **Mitigation ID**: The mitigation from Phase 7 that this validation entry corresponds to. Each mitigation should have at least one validation entry.
- **Expected Code Artifact**: The file, module, configuration file, or infrastructure-as-code template where the mitigation should be implemented.
- **Implementation Status**: Implemented (fully present and functional), Partially Implemented (some aspects present but incomplete), Not Implemented (no evidence of the mitigation in the codebase).
- **Code Evidence**: Specific references to the codebase -- file paths, function names, and line numbers that demonstrate the mitigation is in place. This field should be concrete and verifiable.
- **Gaps Found**: A description of what is missing or incomplete. For example, "Input validation exists for user prompts but not for tool responses" or "Rate limiting configured for API but not for agent-to-agent calls."
- **Anti-Patterns Detected**: Security anti-patterns found during code review, including: hardcoded secrets, overly broad permissions (e.g., wildcard IAM policies), disabled security features (e.g., TLS verification turned off), TODO/FIXME comments on security items, error messages that leak sensitive information, and missing timeout or retry limits.
- **Test Coverage**: Whether automated tests exist to verify the mitigation -- Covered (dedicated security tests exist), Partial (some related tests but not comprehensive), None (no tests for this mitigation).
- **Remediation Priority**: Based on the severity of the gap -- Critical (exploitable vulnerability with no mitigation), High (significant gap in a critical control), Medium (partial implementation needing completion), Low (minor improvement or hardening opportunity).

---

## Residual Risk Template

Use this template during Phase 9 to document residual risk after mitigations are applied.

| Field | Value |
|-------|-------|
| Residual Risk ID | [RR#] |
| Threat ID | [T# from Phase 6] |
| Inherent Risk | [Critical/High/Medium/Low] |
| Mitigation Effectiveness | [High/Medium/Low/None] |
| Implementation Status | [From Phase 8] |
| Residual Risk Level | [Critical/High/Medium/Low] |
| Disposition | [Mitigated/Accepted/Transferred/Deferred] |
| Risk Owner | [Name and role] |
| Review Schedule | [Monthly/Quarterly/Semi-Annually/Annually] |
| Trigger Events | [Events requiring re-evaluation] |

**Field Guidance:**

- **Residual Risk ID**: Sequential identifier (e.g., RR1, RR2, RR3).
- **Threat ID**: The threat from Phase 6 that this residual risk entry corresponds to. Each identified threat should have a residual risk entry.
- **Inherent Risk**: The risk level before any mitigations are applied, based on the severity and likelihood from the Threat Card.
- **Mitigation Effectiveness**: How effectively the planned mitigations reduce the risk -- High (risk largely eliminated), Medium (risk meaningfully reduced), Low (marginal reduction), None (no effective mitigation in place).
- **Implementation Status**: Carried forward from Phase 8 Code Validation -- reflects whether the mitigation is actually implemented, not just planned.
- **Residual Risk Level**: The risk level after accounting for mitigation effectiveness and implementation status. This is the actual current risk to the organization.
- **Disposition**: Mitigated (risk reduced to acceptable level), Accepted (risk acknowledged and accepted by risk owner), Transferred (risk shifted via insurance, SLA, or third party), Deferred (mitigation planned but not yet implemented -- must include a target date).
- **Risk Owner**: The specific individual (name and role) accountable for monitoring and managing this residual risk. Every accepted or deferred risk MUST have a named risk owner.
- **Review Schedule**: How frequently the residual risk should be re-evaluated -- Monthly (for Critical residual risks), Quarterly (for High), Semi-Annually (for Medium), Annually (for Low). Critical residual risks require monthly review.
- **Trigger Events**: Specific events that should trigger an immediate re-evaluation of this residual risk, regardless of the scheduled review. Examples include: architecture changes, new agent capabilities, security incidents, changes in threat landscape, regulatory changes, and vendor/dependency updates.

---

## Executive Summary Template

Use this template during Phase 10 to produce a one-page summary for stakeholders.

| Field | Value |
|-------|-------|
| System Name | [Name] |
| Assessment Date | [Date] |
| Analyst(s) | [Names] |
| Scope | [What was included/excluded] |
| Overall Risk Posture | [Critical/High/Medium/Low] |
| Total Threats Identified | [#] |
| Critical/High Threats | [#] |
| Mitigations Planned | [#] |
| Mitigations Implemented | [#] |
| Top 3 Risks | [Brief descriptions] |
| Key Recommendations | [Brief list] |
| Next Review Date | [Date] |

**Field Guidance:**

- **System Name**: The name of the system or application that was threat modeled, matching the Business Context Template from Phase 1.
- **Assessment Date**: The date the threat model was completed or last updated.
- **Analyst(s)**: The names of the individuals who performed the threat modeling engagement.
- **Scope**: A concise description of what was included in the assessment and, critically, what was excluded. Exclusions should be explicitly stated so stakeholders understand the boundaries of the analysis.
- **Overall Risk Posture**: A single rating summarizing the system's current risk level, considering all identified threats, mitigations, and residual risks.
- **Total Threats Identified**: The total number of threats documented in Phase 6 Threat Cards.
- **Critical/High Threats**: The count of threats rated Critical or High severity. This is the most important metric for executive attention.
- **Mitigations Planned**: The total number of mitigations documented in Phase 7 Mitigation Cards.
- **Mitigations Implemented**: The number of mitigations confirmed as implemented during Phase 8 Code Validation. The gap between planned and implemented mitigations is a key risk indicator.
- **Top 3 Risks**: The three most significant risks, described in business-impact language rather than technical jargon. For example, "Unauthorized access to customer financial data via compromised agent credentials" rather than "Agent credential theft via prompt injection at L3."
- **Key Recommendations**: A prioritized list of the most important actions stakeholders should take, drawn from the Remediation Roadmap.
- **Next Review Date**: When the threat model should be revisited. This should align with the organization's risk review cadence and any upcoming architectural changes.

**Best Practices:**
- This is the one-page deliverable for non-technical stakeholders. Focus on business impact, not technical details.
- Use the Top 3 Risks field to drive executive decision-making and resource allocation.
- The gap between Mitigations Planned and Mitigations Implemented is often the most actionable insight in the summary.

**AI-Assisted Disclaimer (include when threat model is AI-generated):**

> **Disclaimer:** This threat model was generated with AI assistance using the OWASP MAESTRO Playbook. It must be reviewed by a qualified security professional before use in production risk decisions. AI-generated threat models may contain errors, omissions, or fabricated content.

---

## Remediation Roadmap Template

Use this template during Phase 10 to plan the sequenced implementation of mitigations.

| Field | Value |
|-------|-------|
| Remediation ID | [REM#] |
| Threat(s) Addressed | [T# list] |
| Mitigation(s) | [M# list] |
| Description | [What needs to be done] |
| Priority Phase | [Immediate (0-30d) / Short-term (30-90d) / Medium-term (90-180d) / Long-term (180d+)] |
| Effort | [Low/Medium/High] |
| Responsible Team | [Team name] |
| Dependencies | [Other REM# items] |
| Status | [Not Started/In Progress/Complete] |

**Field Guidance:**

- **Remediation ID**: Sequential identifier (e.g., REM1, REM2, REM3).
- **Threat(s) Addressed**: The threat IDs from Phase 6 that this remediation item addresses. A single remediation may address multiple threats.
- **Mitigation(s)**: The mitigation IDs from Phase 7 that will be implemented as part of this remediation item.
- **Description**: A clear, actionable description of what needs to be done -- specific enough for an engineering team to act on.
- **Priority Phase**: Based on threat severity and the implementation gap identified in Phase 8. Immediate (0-30 days) is for Critical threats with no mitigations currently in place. Short-term (30-90 days) is for High threats or Critical threats with partial mitigations. Medium-term (90-180 days) is for Medium threats or hardening improvements. Long-term (180+ days) is for architectural changes and strategic improvements.
- **Effort**: Relative implementation effort -- Low (configuration change, policy update, or simple code fix), Medium (engineering work within a sprint or two), High (significant architecture change, new tooling, or cross-team coordination).
- **Responsible Team**: The team accountable for completing this remediation item.
- **Dependencies**: Other remediation items (by REM# ID) that must be completed before this item can begin. Include dependencies to enable proper sequencing of the roadmap.
- **Status**: Current progress -- Not Started (not yet begun), In Progress (actively being worked on), Complete (implemented and verified).

**Best Practices:**
- Sequence the roadmap so that Immediate items are actionable without dependencies on other items.
- Group related remediation items to reduce context-switching for engineering teams.
- Review and update the roadmap status at regular intervals (e.g., sprint reviews or monthly security meetings).

---

## Threat Register Summary Template

Use this template to produce a summary view of all threats identified during the engagement. It provides a single-page overview for stakeholders and supports deduplication across phases.

**Register ID:** `[Unique identifier, e.g., TR-2025-001]`
**Date:** `[Date of register compilation]`
**Analyst:** `[Name/team]`
**System:** `[System name from Phase 1]`

**Total Threats Identified:** `[Count]`

### Threats by Layer

| MAESTRO Layer | Count | Critical | High | Medium | Low |
|---------------|-------|----------|------|--------|-----|
| L1 - Foundation Model | | | | | |
| L2 - Data Operations | | | | | |
| L3 - Agent Frameworks | | | | | |
| L4 - Deployment Infrastructure | | | | | |
| L5 - Evaluation & Observability | | | | | |
| L6 - Security & Compliance | | | | | |
| L7 - Agent Ecosystem | | | | | |
| Cross-Layer | | | | | |
| **Total** | | | | | |

### Threats by Severity

| Severity | Count | With Mitigation | Without Mitigation |
|----------|-------|----------------|--------------------|
| Critical | | | |
| High | | | |
| Medium | | | |
| Low | | | |

**Deduplication Notes:** `[Record any threats identified at multiple layers and how they were consolidated. Reference the primary Threat Card ID for each deduplicated threat.]`

**Key Findings Summary:** `[3-5 sentence summary of the most significant findings for executive consumption.]`

**Recommended Priority Order:** `[Ordered list of the top 5 threats requiring immediate attention, with Threat Card IDs.]`

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

| Previous | Up | Next |
|----------|-----|------|
| [06 - Modeling Process](./06-modeling-process.md) | [00 - Overview](./00-overview.md) | [08 - Checklists](./08-checklists.md) |
