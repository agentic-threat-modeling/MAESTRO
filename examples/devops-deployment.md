# Worked Example: Multi-Agent DevOps Deployment System

**Version:** 1.0.0

A complete MAESTRO threat model walkthrough for a multi-agent CI/CD deployment system — an AI-driven pipeline that automates code review, infrastructure provisioning, and production deployment. This example demonstrates how to apply the full MAESTRO framework to a multi-agent operational workflow.

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Business Context (Phase 1)](#business-context-phase-1)
3. [MAESTRO Layer Mapping (Phase 2)](#maestro-layer-mapping-phase-2)
4. [Threat Actors (Phase 3)](#threat-actors-phase-3)
5. [Trust Boundaries (Phase 4)](#trust-boundaries-phase-4)
6. [Asset Flow Analysis (Phase 5)](#asset-flow-analysis-phase-5)
7. [Top Threats (Phase 6)](#top-threats-phase-6)
8. [Mitigations (Phase 7)](#mitigations-phase-7)
9. [Code Validation (Phase 8)](#code-validation-phase-8)
10. [Cross-Layer Attack Chains](#cross-layer-attack-chains)
11. [Residual Risk Analysis (Phase 9)](#residual-risk-analysis-phase-9)
12. [Output Summary (Phase 10)](#output-summary-phase-10)
13. [Lessons Learned](#lessons-learned)

> **Note on Threat IDs:** Threat identifiers in this example (T1-T8) are local to this worked example and do not correspond to the ASI taxonomy numbering. See the ASI Mapping field in each threat card for the canonical ASI reference.

> **Threat ID Cross-Reference:** The following maps this example's local threat IDs to the canonical ASI taxonomy.
>
> | Local ID | ASI Mapping | ASI Threat Name |
> |----------|-------------|-----------------|
> | T1 | T6, T2 | Intent Breaking & Goal Manipulation, Tool Misuse |
> | T2 | T2, T16 | Tool Misuse, Model Inconsistency |
> | T3 | T8, T3 | Repudiation & Untraceability, Privilege Compromise |
> | T4 | T10, T15 | Overwhelming HITL, Human Trust Manipulation |
> | T5 | T1, T13 | Memory Poisoning, Rogue Agents |
> | T6 | T11 | Unexpected RCE / Code Attacks |
> | T7 | T12 | Agent Communication Poisoning |
> | T8 | T3, T14 | Privilege Compromise, Human Attacks on MAS |

---

## System Overview

### What It Does

The Multi-Agent DevOps Deployment System automates the software delivery lifecycle for a platform engineering team. Three AI agents collaborate to move code changes from pull request through production deployment:

1. **Code Review Agent** — Analyzes pull requests for code quality, security vulnerabilities, test coverage, and compliance with coding standards using an LLM
2. **Infrastructure Agent** — Provisions and configures cloud infrastructure based on deployment manifests, running infrastructure-as-code plans (Terraform, Pulumi, etc.) and applying changes
3. **Deployment Agent** — Orchestrates the deployment pipeline: runs tests, builds artifacts, deploys to staging, validates health checks, and promotes to production

### Technology Stack

| Component | Technology |
|-----------|-----------|
| Foundation Model | Cloud-hosted LLM API |
| Agent Framework | Python-based agent framework with MCP tool integration |
| Data Store | Vector database for policy/runbook retrieval, git repository for code and configs |
| Orchestration | Event-driven message queue connecting the three agents |
| Infrastructure | Cloud compute (containerized), container registry, secrets manager, DNS |
| Observability | Centralized logging, deployment dashboard, approval queue UI |
| External Systems | Git hosting platform, cloud provider APIs, container registry, monitoring platform |

### Conceptual Architecture

```
+------------------+     +---------------------+     +------------------+
|  Code Review     |---->|  Infrastructure     |---->|  Deployment      |
|      Agent       |     |      Agent          |     |      Agent       |
+--------+---------+     +--------+------------+     +-------+----------+
         |                        |                          |
    [Git Platform]          [Cloud APIs]              [Container Registry]
    [Vector DB:             [State Store]             [Monitoring Platform]
     Policies/Runbooks]     [Secrets Manager]         [DNS Provider]
```

---

## Business Context (Phase 1)

| Field | Value |
|-------|-------|
| Application Name | Multi-Agent DevOps Deployment System |
| Business Domain | Platform Engineering / DevOps |
| Data Classification | Confidential (source code, infrastructure configs, secrets) |
| Regulatory Requirements | SOC 2, internal change management policy |
| Criticality | Critical (production deployment capability) |
| User Base | Internal (engineering teams, SREs, platform engineers) |
| Agent Type | Multi-Agent (3 agents) |
| Autonomy Level | Human-in-loop (approval required for production deployments) |

### Critical Business Rules

1. **No production deployment without human approval**: All production deployments require explicit approval from at least one authorized reviewer.
2. **Infrastructure changes require plan review**: Infrastructure-as-code plans must be reviewed before apply. Destructive changes (resource deletion) require additional approval.
3. **Secrets never in logs or outputs**: Agent outputs, logs, and deployment artifacts must never contain plaintext secrets, API keys, or credentials.
4. **Rollback capability required**: Every production deployment must have a tested rollback path. The Deployment Agent must verify rollback capability before promoting to production.
5. **Audit trail for all changes**: Every code review, infrastructure change, and deployment must be logged with who initiated it, what changed, and when.

---

## MAESTRO Layer Mapping (Phase 2)

| Layer | Components | Notes |
|-------|-----------|-------|
| **L1 - Foundation Model** | Cloud LLM API, prompt templates for code review and deployment analysis | Single model provider; no fine-tuning |
| **L2 - Data Operations** | Vector DB (coding policies, runbooks, incident history), git repository, infrastructure state files | RAG pipeline for policy compliance checking |
| **L3 - Agent Frameworks** | 3 agents (Code Review, Infrastructure, Deployment), MCP server with cloud/git tools, message queue | Inter-agent communication via message queue |
| **L4 - Deployment Infrastructure** | Cloud containers, container registry, secrets manager, DNS, cloud provider APIs | IAM roles per agent with different privilege levels |
| **L5 - Evaluation & Observability** | Centralized logging, deployment dashboard, approval queue, health check monitoring | HITL review for production deployments |
| **L6 - Security & Compliance** | IAM policies, RBAC, secrets management, code signing, change management policies | SOC 2 compliance controls |
| **L7 - Agent Ecosystem** | Git hosting platform, cloud provider APIs, container registry, monitoring platform, human reviewers | Trust relationships with 4+ external platforms |

---

## Threat Actors (Phase 3)

| Actor | Capability | Motivation | Relevance |
|-------|-----------|-----------|-----------|
| **External attacker** | Moderate — can submit malicious pull requests to public repos, exploit exposed endpoints | Service disruption, data access, crypto mining on provisioned infrastructure | High |
| **Malicious insider** | High — has legitimate code commit access and may have deployment permissions | Backdoor insertion, infrastructure misuse, data access | High |
| **Compromised dependency** | Moderate — malicious code in a third-party library or container image | Supply chain access, persistent backdoors | High |
| **Compromised agent** | High — if one agent is compromised, it can influence downstream agents | Depends on attacker's initial goal | Medium |
| **Rogue LLM output** | Low-Moderate — hallucinated or manipulated LLM responses | Not intentional; emergent risk from non-determinism | Medium |

---

## Trust Boundaries (Phase 4)

### Trust Zones

| Zone | Trust Level | Components |
|------|------------|-----------|
| **External** | Untrusted | Pull request authors, external dependencies, container images |
| **Agent Processing** | Medium | All 3 agents, message queue, MCP server |
| **Infrastructure Control Plane** | High | Cloud provider APIs, Terraform state, secrets manager |
| **Production Environment** | Critical | Production workloads, databases, customer-facing services |
| **Human Review** | High | Deployment approval UI, code review interface |

### High-Risk Boundary Crossings

| Crossing | Risk | Why |
|----------|------|-----|
| External → Agent Processing | **Critical** | Untrusted code enters the LLM for review — prompt injection via code comments or commit messages |
| Agent Processing → Infrastructure Control Plane | **Critical** | Agents execute Terraform apply and container deployments with cloud credentials |
| Agent Processing → Production Environment | **Critical** | Deployment Agent pushes artifacts to production — highest blast radius |
| Agent → Agent (via message queue) | **High** | Code Review Agent's approval signal triggers Infrastructure and Deployment Agents |

### The Confused Deputy Problem

The Deployment Agent holds credentials to push to production and modify DNS records. If the Code Review Agent is compromised (via a malicious pull request containing prompt injection) and sends a false "approved" signal through the message queue, the Deployment Agent deploys malicious code to production using its own legitimate credentials. The Deployment Agent cannot distinguish between a legitimate approval and a manipulated one without independent verification.

---

## Asset Flow Analysis (Phase 5)

The following table identifies critical assets in the DevOps deployment system, tracing how each asset is created, stored, transmitted, and where exposure risks exist.

| Asset | Classification | Created | Stored | Transmitted | Exposure Risk |
|-------|---------------|---------|--------|------------|---------------|
| Source code | Confidential | Developers | Git platform | HTTPS to Code Review Agent | Prompt injection via code content |
| Infrastructure credentials | Restricted | Secrets manager | Agent runtime memory | TLS to cloud APIs | Agent memory dump, log leakage |
| Terraform state files | Confidential | Infrastructure Agent | Cloud state store | TLS | Contains resource IDs, sometimes secrets |
| Container images | Internal | Build pipeline | Container registry | HTTPS | Supply chain poisoning |
| Deployment approval tokens | Restricted | Approval UI | Agent session | Message queue | Replay attacks, token theft |
| Agent service account keys | Restricted | IAM provisioning | Secrets manager, agent runtime | TLS | Confused deputy exploitation |
| Audit logs | Confidential | All agents | Centralized logging | Internal network | Log tampering, incomplete coverage |
| System prompts | Confidential | Configuration | Agent framework config | Memory only | Prompt extraction attacks |

### Key Observations

1. **Infrastructure credentials and agent service account keys are the highest-value assets.** Compromise of either grants direct access to the cloud control plane and production environment. These assets exist in both persistent stores (secrets manager) and transient memory (agent runtime), creating two distinct attack surfaces.
2. **Terraform state files often contain embedded secrets despite best practices.** Even when secrets management is used correctly, state files may include resource IDs, endpoint URLs, and occasionally plaintext values that were passed as resource attributes. The state store requires the same protection level as the secrets manager.
3. **Agent memory is a transient but critical exposure point.** During execution, agents hold credentials, approval tokens, and intermediate results in memory. A memory dump, debug log, or crash report from an agent process can expose multiple restricted assets simultaneously.

---

## Top Threats (Phase 6)

### Threat 1: Prompt Injection via Pull Request (L1, L3)

**Description**: An attacker submits a pull request containing adversarial content in code comments, docstrings, commit messages, or specially crafted file names. When the Code Review Agent processes the PR with the LLM, the injected instructions override the review prompt, causing the agent to mark the PR as approved despite containing vulnerabilities or backdoors.

**ASI Mapping**: T6 (Intent Breaking & Goal Manipulation), T2 (Tool Misuse)
**Severity**: Critical | **Likelihood**: Likely
**Agentic Factors**: Non-Determinism (review varies per invocation), Autonomy (agent acts on review outcome)

### Threat 2: Infrastructure Over-Provisioning (L3, L4)

**Description**: The Infrastructure Agent, through hallucination or prompt manipulation, provisions excessive cloud resources (oversized instances, unnecessary network endpoints, open security groups). This expands the attack surface and increases costs without triggering existing budget alerts that have generous thresholds.

**ASI Mapping**: T2 (Tool Misuse), T16 (Model Inconsistency)
**Severity**: High | **Likelihood**: Possible
**Agentic Factors**: Non-Determinism (infrastructure plans vary), Autonomy (agent can provision resources)

### Threat 3: Secrets Leakage in Agent Outputs (L1, L5, L6)

**Description**: The LLM includes plaintext secrets, API keys, or credentials in its code review comments, deployment logs, or error messages. These secrets are then visible in the git platform (review comments), centralized logs, or the deployment dashboard.

**ASI Mapping**: T8 (Repudiation & Untraceability), T3 (Privilege Compromise)
**Severity**: High | **Likelihood**: Likely
**Agentic Factors**: Non-Determinism (LLM may unpredictably include sensitive data in outputs)

### Threat 4: HITL Bypass via Approval Fatigue (L5, L7)

**Description**: During high-velocity release cycles, the approval queue fills with deployment requests. Reviewers, facing pressure to keep pace, approve deployments without meaningful review of the infrastructure plan or deployment manifest.

**ASI Mapping**: T10 (Overwhelming HITL), T15 (Human Trust Manipulation)
**Severity**: High | **Likelihood**: Likely
**Agentic Factors**: Autonomy (system generates approvals faster than humans can review)

### Threat 5: Supply Chain Poisoning via Container Images (L2, L4, L7)

**Description**: A malicious or compromised container image is pulled from an external registry during the build process. The Deployment Agent builds and deploys artifacts based on this image, introducing persistent backdoors into production workloads.

**ASI Mapping**: T1 (Memory Poisoning), T13 (Rogue Agents)
**Severity**: Critical | **Likelihood**: Possible
**Agentic Factors**: Identity Management (agent pulls images using service credentials)

### Threat 6: Unexpected Code Execution via Code Review (L1, L3)

**Description**: The Code Review Agent processes code that contains executable payloads embedded in test files, build scripts, or CI configuration. If the agent framework evaluates or executes code as part of the review (e.g., running linters or tests), malicious code achieves RCE within the agent's container.

**ASI Mapping**: T11 (Unexpected RCE / Code Attacks)
**Severity**: Critical | **Likelihood**: Possible
**Agentic Factors**: Autonomy (agent executes code as part of review process)

### Threat 7: Inter-Agent Message Queue Poisoning (L3, L4)

**Description**: An attacker who gains access to the message queue (via compromised credentials or network access) injects or modifies messages between agents. A forged "code review approved" message sent to the Infrastructure Agent triggers unauthorized infrastructure provisioning.

**ASI Mapping**: T12 (Agent Communication Poisoning)
**Severity**: Critical | **Likelihood**: Possible
**Agentic Factors**: Agent-to-Agent Communication (agents trust queue messages implicitly), Identity Management (messages may lack per-message authentication)

### Threat 8: Terraform State Manipulation (L4, L6)

**Description**: An attacker gains access to the Terraform state store and modifies state to either (a) introduce drift that the Infrastructure Agent will "fix" by provisioning attacker-controlled resources, or (b) remove security resources from state so the next apply deletes them.

**ASI Mapping**: T3 (Privilege Compromise), T14 (Human Attacks on MAS)
**Severity**: High | **Likelihood**: Unlikely
**Agentic Factors**: Autonomy (Infrastructure Agent automatically applies plans to converge with state)

---

## Mitigations (Phase 7)

### Threat 1 Mitigations: Prompt Injection via Pull Request

| ID | Type | Control | Status |
|----|------|---------|--------|
| M1 | Preventive | Sanitize PR content before LLM processing: strip code comments and commit messages from the review prompt where feasible | Not Implemented |
| M2 | Preventive | Constrain LLM output to a structured schema (approve/reject/comment) that cannot include arbitrary tool calls | Partially Implemented |
| M3 | Detective | Compare LLM review outcome against static analysis tool results; flag divergence | Not Implemented |
| M4 | Detective | Log all PR review decisions with full prompt/response for forensic review | Implemented |

### Threat 2 Mitigations: Infrastructure Over-Provisioning

| ID | Type | Control | Status |
|----|------|---------|--------|
| M5 | Preventive | Enforce infrastructure policies via policy-as-code (e.g., Open Policy Agent) that reject plans exceeding defined resource limits | Partially Implemented |
| M6 | Preventive | Require explicit human approval for any Terraform plan that creates or modifies security groups, IAM roles, or public endpoints | Not Implemented |
| M7 | Detective | Automated drift detection comparing provisioned infrastructure against approved baselines | Not Implemented |

### Threat 3 Mitigations: Secrets Leakage

| ID | Type | Control | Status |
|----|------|---------|--------|
| M8 | Preventive | Output filtering: scan all agent outputs for patterns matching known secret formats before publishing | Partially Implemented |
| M9 | Preventive | Use environment variable references instead of literal secrets in all agent-accessible configurations | Implemented |
| M10 | Detective | Automated secret scanning on all log streams and git commits with immediate alerting | Implemented |
| M11 | Corrective | Automated secret rotation triggered when a leak is detected | Not Implemented |

### Threat 4 Mitigations: HITL Bypass

| ID | Type | Control | Status |
|----|------|---------|--------|
| M12 | Preventive | Cap approval queue at 10 items per reviewer per hour; overflow routes to next reviewer | Not Implemented |
| M13 | Preventive | Mandatory infrastructure plan diff review before approval button activates | Not Implemented |
| M14 | Detective | Random spot-check: 15% of approved deployments flagged for secondary SRE review | Not Implemented |

### Threat 5 Mitigations: Supply Chain Poisoning

| ID | Type | Control | Status |
|----|------|---------|--------|
| M15 | Preventive | Pin all base images by digest (not tag); require signed images from trusted registries only | Partially Implemented |
| M16 | Preventive | Run container vulnerability scanning in the build pipeline; block deployment on critical findings | Implemented |
| M17 | Detective | Monitor for unexpected network connections from production containers | Not Implemented |

### Additional Corrective and Deterrent Controls

The following controls supplement the Preventive and Detective mitigations above with Corrective (automated response and recovery) and Deterrent (discouraging attack attempts) capabilities.

| ID | Threat | Type | Control | Status |
|----|--------|------|---------|--------|
| M18 | Threat 1 | Corrective | Automated rollback: if post-deploy health checks fail within 15 minutes of a deployment triggered by a flagged PR, auto-revert | Not Implemented |
| M19 | Threat 1 | Deterrent | Publish weekly PR injection attempt report to security team; visible audit trail discourages repeat attempts | Not Implemented |
| M20 | Threat 2 | Corrective | Infrastructure auto-remediation: policy engine detects drift from approved baseline and reverts unauthorized changes | Not Implemented |
| M21 | Threat 3 | Corrective | Automated secret rotation triggered on leak detection, plus automated purge of leaked secrets from logs | Partially Implemented |
| M22 | Threat 5 | Corrective | Container quarantine: isolate affected containers and trigger forensic snapshot before termination | Not Implemented |
| M23 | All | Deterrent | Immutable audit trail with cryptographic chaining; tamper-evident logging for all agent actions | Not Implemented |

### Threat 6 Mitigations: Unexpected Code Execution via Code Review

| ID | Type | Control | Status |
|----|------|---------|--------|
| M24 | Preventive | Run code review in a sandboxed container with no network access and read-only filesystem | Not Implemented |
| M25 | Preventive | Disable code execution during review; use static analysis only | Not Implemented |
| M26 | Detective | Monitor agent container for unexpected process spawning or outbound network connections | Not Implemented |

### Threat 7 Mitigations: Inter-Agent Message Queue Poisoning

| ID | Type | Control | Status |
|----|------|---------|--------|
| M27 | Preventive | Implement per-message signing with agent-specific keys; verify signature before processing | Not Implemented |
| M28 | Preventive | Restrict message queue access to agent service accounts only; no shared credentials | Partially Implemented |
| M29 | Detective | Anomaly detection on message queue: alert on unexpected message patterns, frequency, or source | Not Implemented |

### Threat 8 Mitigations: Terraform State Manipulation

| ID | Type | Control | Status |
|----|------|---------|--------|
| M30 | Preventive | Enable versioning and access logging on state store; require MFA for direct state access | Partially Implemented |
| M31 | Preventive | State file encryption at rest with customer-managed keys | Implemented |
| M32 | Detective | State integrity checks: compare planned state changes against approved change requests before apply | Not Implemented |

---

## Code Validation (Phase 8)

> **Note:** Phase 8 (Code Validation) is not applicable to this worked example, which uses
> a hypothetical system without a real codebase. In a real engagement, this phase validates
> that mitigations identified in Phase 7 are correctly implemented in the actual code. See the
> Code Validation template in [07 - Templates](../playbook/07-templates.md) for the field structure.

---

## Cross-Layer Attack Chains

### Chain 1: PR Injection to Production Deployment

```
Malicious Pull Request (External)
    |
    v
[L1] Code Review Agent processes PR; prompt injection in comments
     causes LLM to output "approved" verdict
    |
    v
[L3] Approval message sent to Infrastructure Agent via queue
    |
    v
[L4] Infrastructure Agent provisions staging environment
     (with any infra changes embedded in the malicious PR)
    |
    v
[L3] Deployment Agent receives "staging validated" signal
    |
    v
[L5] Approval queue shows "Code Review: Approved, Staging: Healthy"
     Human reviewer approves based on these signals
    |
    v
[L4] Deployment Agent pushes to production with legitimate credentials

RESULT: Malicious code running in production, with a complete
        "legitimate" approval trail at every stage
```

**Why cross-layer analysis is essential**: Single-layer analysis at L1 catches the prompt injection risk but misses the cascading deployment. The attack succeeds because each downstream agent trusts the upstream agent's output without independent verification.

### Chain 2: Infrastructure Drift to Persistent Access

```
[L1] Infrastructure Agent hallucinates an extra security group rule
     (allowing inbound access on an unusual port)
    |
    v
[L4] Terraform apply creates the permissive rule in production
    |
    v
[L5] Deployment dashboard shows "infrastructure provisioned successfully"
     No alert because the change was part of an approved plan
    |
    v
[L6] The permissive rule persists across subsequent deployments
     because it is now in the Terraform state
    |
    v
[L7] External attacker discovers the open port via scanning
     and gains network access to production

RESULT: Persistent infrastructure misconfiguration caused by
        a single hallucinated output, undetected for weeks
```

**Why cross-layer analysis is essential**: The hallucination at L1 creates a real infrastructure change at L4 that persists in state at L6 and is exploitable at L7. No single-layer analysis connects the LLM output to the eventual network exposure.

### Chain 3: Dependency Compromise Through the Pipeline

```
[L7] Attacker publishes a compromised version of a popular library
     to a public package registry
    |
    v
[L2] Code Review Agent's RAG pipeline retrieves the library's
     documentation (which describes legitimate-looking APIs)
    |
    v
[L1] Code Review Agent approves a PR that adds the compromised
     dependency (documentation looks legitimate)
    |
    v
[L4] Build pipeline pulls the compromised package and bakes it
     into the container image
    |
    v
[L3] Deployment Agent deploys the image to production
    |
    v
[L4] Compromised code executes within production containers
     using the container's service account credentials

RESULT: Persistent backdoor in production, introduced through
        the standard development workflow
```

**Why cross-layer analysis is essential**: The supply chain compromise originates externally (L7), influences the LLM's analysis via RAG (L2 + L1), passes through the build pipeline (L4), and executes in production (L4). Detection requires observability (L5) that correlates dependency changes with runtime behavior — a cross-layer detective control.

---

## Residual Risk Analysis (Phase 9)

After applying current mitigations, the following residual risks remain.

| Threat | Inherent Risk | Mitigation Coverage | Residual Risk | Disposition |
|--------|-------------|-------------------|---------------|-------------|
| T1: Prompt Injection | Critical | Partial (M2 partial, M4 implemented) | **High** | Accepted with monitoring |
| T2: Over-Provisioning | High | Partial (M5 partial) | **High** | Deferred to next quarter |
| T3: Secrets Leakage | High | Moderate (M8-M10 partial/implemented) | **Medium** | Mitigated with monitoring |
| T4: HITL Bypass | High | None (M12-M14 not implemented) | **High** | Accepted — highest priority |
| T5: Supply Chain | Critical | Moderate (M15 partial, M16 implemented) | **High** | Accepted with compensating controls |
| T6: Code Execution | Critical | None (M24-M26 not implemented) | **Critical** | Deferred — immediate remediation needed |
| T7: Queue Poisoning | Critical | Minimal (M28 partial) | **Critical** | Deferred — immediate remediation needed |
| T8: State Manipulation | High | Moderate (M30 partial, M31 implemented) | **Medium** | Mitigated |

Two threats (T6, T7) remain at Critical residual risk with minimal mitigations. These should be the immediate priority. The overall system residual risk posture is **High**, driven by the Critical residual risks and the number of Not Implemented mitigations.

---

## Output Summary (Phase 10)

- **Total threats identified:** 8
- **Critical threats:** 4 (T1, T5, T6, T7)
- **High threats:** 4 (T2, T3, T4, T8)
- **Total mitigations:** 32
- **Implemented:** 5 | **Partially implemented:** 5 | **Not implemented:** 22
- **Overall risk posture:** **High**
- **Recommended immediate actions:**
  1. Sandbox code review agent (M24/M25)
  2. Implement message signing (M27)
  3. Deploy HITL queue caps (M12/M13)
- **Next review trigger:** Any architecture change, new agent addition, or security incident

---

## Lessons Learned

### 1. Production Credentials Are the Ultimate Target

Every attack chain in this system converges on the same goal: using the agents' legitimate production credentials to make unauthorized changes. The Deployment Agent's credentials are the highest-value target. **Design principle**: The highest-privilege agent should have the most independent verification and the tightest constraints, not the least.

### 2. Inter-Agent Trust Must Be Verified, Not Assumed

When the Code Review Agent says "approved," the downstream agents treat this as authoritative. But if the Code Review Agent is compromised, this trust relationship becomes the attack vector. **Design principle**: Each agent should independently verify critical signals rather than trusting upstream agents unconditionally.

### 3. Infrastructure-as-Code Does Not Mean Infrastructure-as-Reviewed

Infrastructure-as-code plans and templates are complex. When an LLM generates or modifies infrastructure code, the changes may include subtle misconfigurations that are difficult for human reviewers to catch. **Design principle**: Use policy-as-code tools to enforce infrastructure constraints programmatically rather than relying solely on human review of infrastructure plans.

### 4. LLM Hallucinations Have Real-World Consequences

In a DevOps context, a hallucinated security group rule or an incorrect container configuration becomes a real infrastructure change when applied. Unlike a chatbot where hallucinations are inconvenient, in agentic systems they create persistent, exploitable states. **Design principle**: Validate all LLM-generated configurations against policy constraints before applying them.

### 5. HITL Degrades Under Velocity Pressure

DevOps teams optimize for deployment speed. When the AI system generates deployment requests faster than humans can meaningfully review them, the HITL control becomes a rubber stamp. **Design principle**: Enforce review quality controls (queue caps, mandatory review times, spot-checks) that maintain the integrity of human review even under load.

### 6. Supply Chain Is the Blind Spot

The agents' RAG pipelines, MCP server dependencies, container base images, and third-party libraries all represent supply chain attack surfaces. A compromised dependency operates within the agent's legitimate permission boundary, making it nearly invisible to runtime security controls. **Design principle**: Pin dependencies, verify signatures, scan continuously, and monitor for behavioral anomalies.

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

## Navigation

- [Example: Generic RAG Agent](generic-rag-agent.md)
- [MCP Integration Guide](../guides/mcp-integration.md)
- [Quickstart: 30-Minute Minimum Viable Threat Model](../guides/quickstart.md)
- [Playbook Overview](../playbook/00-overview.md)
- [Glossary](../GLOSSARY.md)
