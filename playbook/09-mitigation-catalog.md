# 09 - Mitigation Catalog

**Version:** 1.0.0

This catalog provides mitigations organized by MAESTRO layer and by the four mitigation control types defined in the MitigationType enum: Preventive, Detective, Corrective, and Deterrent. Use this catalog during Phase 7 (Mitigation Planning) to ensure comprehensive coverage for each identified threat.

---

## Table of Contents

1. [Mitigation Control Types](#mitigation-control-types)
2. [Layer 1: Foundation Model](#layer-1-foundation-model)
3. [Layer 2: Data Operations](#layer-2-data-operations)
4. [Layer 3: Agent Frameworks](#layer-3-agent-frameworks)
5. [Layer 4: Deployment Infrastructure](#layer-4-deployment-infrastructure)
6. [Layer 5: Evaluation & Observability](#layer-5-evaluation--observability)
7. [Layer 6: Security & Compliance](#layer-6-security--compliance)
8. [Layer 7: Agent Ecosystem](#layer-7-agent-ecosystem)
9. [Cross-Layer Mitigations](#cross-layer-mitigations)
10. [Using This Catalog](#using-this-catalog)
11. [Coverage Notes](#coverage-notes)

---

## Mitigation Control Types

| Type | Purpose | Timing |
|------|---------|--------|
| **Preventive** | Stop threats from occurring | Before the attack |
| **Detective** | Identify threats in progress or after occurrence | During or after the attack |
| **Corrective** | Recover from threats that have materialized | After the attack |
| **Deterrent** | Discourage threat actors from attempting the attack | Before the attack (psychological) |

**Mitigation ID Convention:** Each mitigation ID follows the format `L[layer]-[type][number]` where:
- **P** = Preventive Control
- **D** = Detective Control
- **C** = Corrective Control
- **R** = Deterrent Control (from "Retardant/Restraint" — controls that discourage threat actors)

---

## Layer 1: Foundation Model

### Preventive Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L1-P1 | Input Guardrails | Deploy input filtering and prompt injection detection before all LLM calls. Reject or sanitize inputs that match known injection patterns. | T6 (Intent Breaking), T1 (Memory Poisoning) |
| L1-P2 | Output Validation | Validate all LLM outputs against expected schemas, value ranges, and business rules before acting on them. Reject outputs that fail validation. | T5 (Cascading Hallucinations), T7 (Misaligned & Deceptive Behaviour) |
| L1-P3 | Model Access Controls | Restrict access to model APIs, inference endpoints, and fine-tuning pipelines. Use API keys with scoped permissions. | T1 (Memory Poisoning), T11 (RCE/Code Attacks) |
| L1-P4 | System Prompt Hardening | Use structured system prompts with explicit boundaries. Employ prompt delimiters and instruction hierarchy. Prevent system prompt extraction. | T6 (Intent Breaking), T7 (Misaligned & Deceptive Behaviour) |
| L1-P5 | Temperature and Sampling Controls | Set deterministic inference parameters (low temperature, top-k constraints) for security-critical decisions to reduce non-deterministic variance. | T5 (Cascading Hallucinations), T16 (Model Inconsistency) |

### Detective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L1-D1 | Output Anomaly Detection | Monitor LLM outputs for anomalous patterns: unexpected topics, out-of-scope responses, format violations, repeated patterns indicating injection. | T5, T6, T7 |
| L1-D2 | Hallucination Detection | Implement fact-checking mechanisms that cross-reference LLM outputs against authoritative data sources. Flag unverifiable claims. | T5 (Cascading Hallucinations) |
| L1-D3 | Inference Pattern Monitoring | Monitor inference API access patterns for model extraction attempts: high-volume systematic queries, boundary-probing inputs. | Model Stealing via Eavesdropping (Extended L1 — no ASI T-ID) |
| L1-D4 | Prompt Injection Logging | Log all inputs to the model with metadata. Analyze logs for injection attempt patterns. Alert on suspicious input sequences. | T6 (Intent Breaking) |

### Corrective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L1-C1 | Model Rollback | Maintain versioned model checkpoints. If a model is found to be compromised or degraded, roll back to a known-good version. | T1 (Memory Poisoning) |
| L1-C2 | Output Quarantine and Re-processing | When a compromised output is detected, quarantine all downstream actions and re-process from a clean state. | T5 (Cascading Hallucinations) |
| L1-C3 | Emergency Model Bypass | Implement a fallback path that bypasses the LLM for critical operations when model compromise is suspected. Use rule-based logic or human processing. | T7 (Misaligned & Deceptive Behaviour) |

### Deterrent Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L1-R1 | Visible Input Monitoring Notification | Inform users that all inputs are logged and monitored. Displayed in the UI and terms of service. | T6 (Intent Breaking), T14 (Human Attacks on MAS) |
| L1-R2 | Model Watermarking | Apply watermarking to model outputs to enable attribution and detection of model extraction. | Model Stealing via Eavesdropping (Extended L1 — no ASI T-ID) |
| L1-R3 | Rate Limiting with Progressive Penalties | Apply rate limits on inference APIs with escalating restrictions for repeated suspicious patterns. | T4 (Resource Overload), T6 (Intent Breaking) |

---

## Layer 2: Data Operations

### Preventive Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L2-P1 | RAG Source Document Integrity | Enforce integrity checks (checksums, digital signatures) on all documents ingested into the RAG pipeline. Reject tampered documents. | T1 (Memory Poisoning), T12 (Agent Communication Poisoning) |
| L2-P2 | Vector Database Access Controls | Implement fine-grained access controls on vector databases. Restrict read/write by role. Isolate agent-specific vector stores. | T1 (Memory Poisoning), T28 (RAG Data Exfiltration) |
| L2-P3 | Embedding Re-indexing Schedule | Establish automated re-indexing of embeddings when source documents change. Detect and flag stale embeddings. | T17 (Semantic Drift) |
| L2-P4 | Input Sanitization for RAG Queries | Sanitize and validate all inputs to RAG similarity searches. Prevent crafted inputs from manipulating retrieval results. | T18 (RAG Input Manipulation) |

### Detective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L2-D1 | Data Pipeline Integrity Monitoring | Monitor data ingestion pipelines for unexpected changes: new sources, modified schemas, volume anomalies, unauthorized writes. | T1 (Memory Poisoning) |
| L2-D2 | Embedding Drift Detection | Periodically compare current embeddings against baseline. Alert when semantic distance exceeds threshold, indicating drift or poisoning. | T17 (Semantic Drift), T1 (Memory Poisoning) |
| L2-D3 | RAG Retrieval Auditing | Log all RAG queries and retrieved chunks. Monitor for unusual retrieval patterns that may indicate manipulation. | T18 (RAG Input Manipulation), T28 (RAG Data Exfiltration) |
| L2-D4 | Shared Memory Access Logging | Log all reads and writes to shared agent memory. Alert on unexpected access patterns or data modifications. | T1 (Memory Poisoning), T12 (Agent Communication Poisoning) |

### Corrective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L2-C1 | Knowledge Base Rollback | Maintain versioned snapshots of vector databases and document stores. Roll back to a known-good state when poisoning is detected. | T1 (Memory Poisoning) |
| L2-C2 | Contaminated Data Quarantine | Isolate and quarantine identified poisoned data. Re-process all decisions made using contaminated data. | T1, T12 |
| L2-C3 | Emergency RAG Bypass | Implement a fallback that serves curated, human-verified data when the RAG pipeline is suspected of compromise. | T17, T18 |

### Deterrent Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L2-R1 | Data Provenance Tracking | Implement full data provenance tracking from source to vector store. Make the audit trail visible to data stewards. | T1, T12 |
| L2-R2 | Document Source Authentication | Require authenticated submissions for all source documents. Reject anonymous or unauthenticated data. | T1 (Memory Poisoning) |
| L2-R3 | Tamper-Evident Storage | Use append-only or tamper-evident storage for critical data sources. Any modification is permanently visible. | T1, T12 |

---

## Layer 3: Agent Frameworks

### Preventive Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L3-P1 | Tool Allowlisting and Scoping | Maintain an explicit allowlist of tools each agent can invoke. Enforce parameter constraints and value ranges per tool. | T2 (Tool Misuse), T11 (RCE/Code Attacks) |
| L3-P2 | Workflow Validation | Enforce sequential workflow execution with mandatory validation steps. Prevent step-skipping or out-of-order execution. | T19 (Unintended Workflow), T6 (Intent Breaking) |
| L3-P3 | Agent Sandboxing | Execute agent logic in sandboxed environments with restricted filesystem, network, and process access. | T11 (RCE/Code Attacks), T20 (Framework Code Injection) |
| L3-P4 | Plugin Verification | Verify plugin integrity via signatures and checksums before loading. Maintain an approved plugin registry. Scan for known vulnerabilities. | T29 (Plugin Vulnerability), T13 (Rogue Agents) |
| L3-P5 | Schema Validation | Validate all inter-component messages and tool call parameters against strict schemas. Reject malformed or unexpected inputs. | T41 (Schema Mismatch), T42 (Cross-Client Interference) |

### Detective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L3-D1 | Tool Invocation Monitoring | Log and monitor all tool invocations: which tool, parameters, invoking agent, timestamp. Alert on unusual tool usage patterns. | T2 (Tool Misuse) |
| L3-D2 | Workflow State Monitoring | Monitor agent workflow state transitions. Alert on unexpected transitions, skipped steps, or state corruption. | T19, T21 (Inconsistent Workflow State) |
| L3-D3 | Agent Behavioral Profiling | Establish baseline behavioral profiles for each agent. Detect deviations in: tool usage frequency, response patterns, decision outcomes. | T7 (Misaligned & Deceptive Behaviour), T13 (Rogue Agents) |
| L3-D4 | Runaway Agent Detection | Monitor agent execution time, iteration count, and resource consumption. Detect and flag infinite loops or excessive processing. | T32 (Runaway Agent), T4 (Resource Overload) |

### Corrective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L3-C1 | Circuit Breaker | Implement circuit breakers that halt agent execution when anomalous behavior is detected: excessive tool calls, spending limits exceeded, error rate spike. | T32, T2, T4 |
| L3-C2 | Workflow Rollback | Enable rollback of agent workflow to a previous checkpoint when errors or compromise are detected. Undo tool actions where possible. | T19, T6 |
| L3-C3 | Agent Quarantine | Isolate a suspected compromised agent from the system. Redirect its communications to a monitored sandbox. | T13 (Rogue Agents) |
| L3-C4 | Plugin Hot-Swap | Enable runtime replacement of compromised plugins without full agent restart. Maintain fallback plugins for critical functionality. | T29 (Plugin Vulnerability) |

### Deterrent Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L3-R1 | Tool Usage Audit Trail | Maintain a comprehensive, immutable audit trail of all tool invocations. Make this visible in compliance dashboards. | T2, T8 (Repudiation) |
| L3-R2 | Agent Action Accountability | Attribute every agent action to the initiating user and the authorizing policy. Maintain clear accountability chains. | T8, T14 (Human Attacks on MAS) |
| L3-R3 | Spending and Action Limits | Enforce hard limits on financial transactions, API calls, and resource consumption per agent per session. Visible to users. | T32, T2, T4 |

---

## Layer 4: Deployment Infrastructure

### Preventive Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L4-P1 | Least-Privilege IAM | Implement IAM roles with minimum necessary permissions for each component. No wildcard permissions. Regular permission audits. | T3 (Privilege Compromise), T22 (Service Account Exposure) |
| L4-P2 | Network Segmentation | Deploy agents in VPCs with network segmentation. Restrict inter-component communication to only required paths. Use security groups and NACLs. | T43 (Network Exposure), T4 (Resource Overload) |
| L4-P3 | Secrets Management | Store all credentials in a secrets manager. Never hardcode secrets. Implement automatic rotation. | T22 (Service Account Exposure), T3 |
| L4-P4 | Code Signing and Image Verification | Sign all deployment artifacts (serverless packages, container images). Verify signatures before execution. | T13 (Rogue Agents), T20 (Framework Code Injection) |
| L4-P5 | DDoS Protection | Implement rate limiting, WAF rules, and auto-scaling with hard caps to protect against resource exhaustion. | T4 (Resource Overload) |

### Detective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L4-D1 | Infrastructure Anomaly Detection | Monitor compute, networking, and storage metrics for anomalies: unusual CPU/memory usage, unexpected network connections, storage access patterns. | T4, T3, T13 |
| L4-D2 | IAM Activity Monitoring | Monitor IAM API calls (via cloud audit logs). Alert on permission changes, role assumptions, access key usage, and unauthorized API calls. | T3 (Privilege Compromise) |
| L4-D3 | Credential Leak Scanning | Scan code repositories, logs, and configuration files for exposed credentials. Use automated secret scanning tools. | T22 (Service Account Exposure) |
| L4-D4 | Orchestration Integrity Monitoring | Monitor orchestration services (event buses, workflow engines) for unauthorized rule/workflow modifications. | T13, T3 |

### Corrective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L4-C1 | Credential Rotation on Compromise | Automatically rotate all credentials for a service when compromise is suspected. Invalidate all active sessions. | T22, T3 |
| L4-C2 | Infrastructure Isolation | Isolate compromised infrastructure components from the network. Redirect traffic to healthy replicas. | T13 (Rogue Agents), T4 |
| L4-C3 | Auto-Scaling with Circuit Breaker | Auto-scale under load but with hard caps. When caps are reached, activate circuit breaker instead of queuing or failing open. | T4 (Resource Overload) |
| L4-C4 | Disaster Recovery | Maintain infrastructure-as-code for rapid redeployment. Automated backup and restore for all stateful components. | All |

### Deterrent Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L4-R1 | Cloud Audit Logging | Enable cloud audit logging with tamper-proof log delivery to a separate account. Visible compliance reporting. | T3, T8, T22 |
| L4-R2 | Penetration Testing Program | Conduct regular penetration testing of infrastructure. Publish testing schedule and results summary to deter casual attackers. | All |
| L4-R3 | Canary Tokens and Honeypots | Deploy canary tokens in likely target locations (credential files, storage buckets). Alert on access to detect early-stage intrusions. | T3, T22, T13 |

---

## Layer 5: Evaluation & Observability

### Preventive Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L5-P1 | Immutable Audit Logging | Write audit logs to append-only, tamper-evident storage. Use write-once storage or blockchain-anchored hashes. | T8 (Repudiation), T23 (Selective Log Manipulation) |
| L5-P2 | HITL Capacity Planning | Size human review capacity to match agent throughput. Implement queue management with automatic throttling when review backlog exceeds threshold. | T10 (Overwhelming HITL) |
| L5-P3 | Non-Repudiation Controls | Require digital signatures or multi-factor confirmation for all human approval actions. Ensure approvals cannot be forged or denied. | T8 (Repudiation) |
| L5-P4 | Log Integrity Protection | Protect log integrity with cryptographic chaining (each log entry includes hash of previous entry). Detect any deletion or modification. | T23 (Selective Log Manipulation) |

### Detective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L5-D1 | Behavioral Drift Detection | Monitor agent decision patterns over time. Alert when statistical distribution of decisions shifts beyond baseline thresholds. | T7 (Misaligned & Deceptive Behaviour), T5 |
| L5-D2 | HITL Effectiveness Monitoring | Monitor human reviewer behavior: approval rates, review times, override patterns. Alert on rubber-stamping indicators. | T10 (Overwhelming HITL), T15 (Human Trust Manipulation) |
| L5-D3 | Log Completeness Verification | Periodically verify log completeness by correlating expected events (from source systems) with logged events. Flag gaps. | T23 (Selective Log Manipulation), T44 (Insufficient Logging) |
| L5-D4 | Performance Degradation Detection | Monitor system performance metrics with anomaly detection. Alert when performance degrades in patterns consistent with masking attacks. | Distributed Performance Degradation Masking |

### Corrective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L5-C1 | Log Reconstruction | Maintain redundant log streams to enable reconstruction of tampered or deleted logs from alternative sources. | T23, T8 |
| L5-C2 | HITL Overload Escalation | When HITL capacity is exceeded, automatically escalate to additional reviewers, temporarily reduce agent throughput, or pause non-critical operations. | T10 |
| L5-C3 | Audit Trail Recovery | Enable point-in-time recovery of audit trails from immutable backups. Reconstruct the complete decision history. | T8, T23 |

### Deterrent Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L5-R1 | Transparent Audit Dashboard | Provide real-time dashboards showing all agent actions, approvals, and audit trail to authorized stakeholders. Visibility deters manipulation. | T8, T23, T10 |
| L5-R2 | Audit Verification by External Party | Periodically have an independent party verify audit trail integrity. Publish verification results. | T8, T23 |
| L5-R3 | Whistleblower Channel | Provide a channel for reporting suspected agent misbehavior or audit manipulation. Protect reporters. | T7, T23 |

---

## Layer 6: Security & Compliance

### Preventive Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L6-P1 | RBAC with Separation of Duties | Implement role-based access control with strict separation of duties. No single role can approve and execute financial transactions. | T3 (Privilege Compromise), T7 (Misaligned & Deceptive Behaviour) |
| L6-P2 | End-to-End Authorization Propagation | Propagate user identity and authorization context through the entire agent call chain to backend services. Prevent privilege escalation via agent service accounts. | T3, T14 (Human Attacks on MAS) |
| L6-P3 | Runtime Guardrails | Deploy policy enforcement engines that evaluate every agent action against security policies in real time. Block policy violations before execution. | T7 (Misaligned & Deceptive Behaviour), T24 (Dynamic Policy Failure) |
| L6-P4 | Data Residency Controls | Enforce data residency requirements in code and infrastructure. Prevent data from crossing jurisdictional boundaries. | T46 (Data Residency Violation) |
| L6-P5 | MCP Server Permission Isolation | Run MCP servers with minimum host permissions. Isolate file system, network, and process access per server. | T45 (Insufficient Server Permission Isolation) |

### Detective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L6-D1 | Policy Violation Alerting | Monitor all agent actions against compliance policies. Alert in real time on any violation or near-violation. | T7, T24, T46 |
| L6-D2 | Privilege Usage Monitoring | Monitor how agents use their permissions. Alert on unusual privilege usage: first-time access to resources, access outside business hours, elevated permission usage. | T3 (Privilege Compromise) |
| L6-D3 | Compliance Drift Detection | Regularly assess system configuration against compliance baselines. Alert when drift is detected (e.g., changed IAM policies, disabled encryption). | T24, T46 |
| L6-D4 | Secrets Access Auditing | Log and monitor all access to secrets management systems. Alert on unexpected access patterns or bulk secret retrieval. | T3, T22 |

### Corrective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L6-C1 | Automatic Policy Re-enforcement | When policy drift is detected, automatically re-apply the correct policy configuration from the approved baseline. | T24 (Dynamic Policy Failure) |
| L6-C2 | Emergency Access Revocation | Enable rapid revocation of all agent credentials and permissions when compromise is confirmed. Halt all agent operations. | T3, T13, T45 |
| L6-C3 | Compliance Violation Remediation | Maintain runbooks for each type of compliance violation. Automated remediation for common violations (e.g., re-enable encryption, restore IAM policies). | T24, T46 |

### Deterrent Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L6-R1 | Compliance Reporting | Generate and publish regular compliance reports showing policy adherence, violations, and remediation status. | T7, T24, T46 |
| L6-R2 | Access Review Campaigns | Conduct regular access review campaigns where managers verify that agent permissions are appropriate. Revoke unused permissions. | T3 (Privilege Compromise) |
| L6-R3 | Regulatory Consequence Awareness | Document and communicate the regulatory consequences (fines, legal action) of compliance violations to all stakeholders. | T7, T24, T46 |

---

## Layer 7: Agent Ecosystem

### Preventive Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L7-P1 | Mutual Authentication | Require mutual TLS or equivalent authentication for all agent-to-agent and agent-to-external-system communication. Verify both parties. | T9 (Identity Spoofing), T40 (Client Impersonation) |
| L7-P2 | MCP Server Registry and Verification | Maintain a trusted registry of approved MCP servers. Verify server identity and integrity before connecting. Reject unregistered servers. | T47 (Rogue MCP Server), T13 (Rogue Agents) |
| L7-P3 | External API Input Validation | Validate all data received from external APIs and services before processing. Treat all external data as untrusted. | T12 (Agent Communication Poisoning), T25 (Dependency Exploitation) |
| L7-P4 | Human Interaction Guardrails | Implement safeguards against social engineering through the agent: limit information disclosure, require confirmation for sensitive actions, provide uncertainty indicators. | T14 (Human Attacks on MAS), T15 (Human Trust Manipulation) |

### Detective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L7-D1 | Agent Identity Verification | Continuously verify agent identities through behavioral fingerprinting in addition to credential checks. Detect identity spoofing via behavioral anomalies. | T9 (Identity Spoofing), T13 (Rogue Agents) |
| L7-D2 | External Dependency Monitoring | Monitor availability and integrity of external dependencies. Alert on unexpected changes in external API behavior, schema changes, or availability patterns. | T25 (Dependency Exploitation) |
| L7-D3 | Human Trust Calibration Monitoring | Monitor indicators of automation bias in human users: declining review times, increasing approval rates, reduced override frequency. | T15 (Human Trust Manipulation), T10 |
| L7-D4 | Inter-Agent Traffic Analysis | Monitor inter-agent communication for anomalies: unexpected message volumes, new communication patterns, data exfiltration indicators. | T12, T13, T38 (Emergent Collusion) |

### Corrective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L7-C1 | Agent De-registration | Immediately de-register and isolate agents whose identity cannot be verified. Block all communication to and from the suspect agent. | T9, T13 |
| L7-C2 | External Dependency Failover | Maintain fallback providers or cached responses for critical external dependencies. Activate failover when primary dependency is compromised. | T25 |
| L7-C3 | Trust Reset | When a trust chain is compromised, reset all trust relationships and re-establish from scratch. Revoke and re-issue all credentials in the affected trust domain. | T9, T13, Cascading Trust Failure |

### Deterrent Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| L7-R1 | Agent Reputation System | Maintain reputation scores for agents in the ecosystem. Low-reputation agents receive restricted access. Published reputation scores deter rogue behavior. | T13 (Rogue Agents) |
| L7-R2 | Supply Chain Transparency | Require and publish software bills of materials (SBOMs) for all agent components, plugins, and MCP servers. | T13, T29, T47 |
| L7-R3 | Ecosystem Participation Agreements | Require signed agreements from all ecosystem participants covering security standards, incident reporting, and liability. | T9, T13, T47 |

---

## Cross-Layer Mitigations

### Preventive Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| CL-P1 | Defense in Depth | Implement overlapping security controls at multiple layers so that no single control failure leads to compromise. | All cross-layer threats |
| CL-P2 | Trust Boundary Enforcement | Enforce security controls at every trust boundary crossing. Never trust data or commands received from a different trust zone without validation. | T6 (Intent Breaking), T3 (Privilege Compromise), T12 |
| CL-P3 | Privilege Minimization Across Layers | Ensure that permissions granted at one layer do not automatically propagate to other layers. Each layer enforces its own authorization. | T3, T14, Confused Deputy |
| CL-P4 | End-to-End Encryption | Encrypt data in transit between all layers. Use TLS 1.3 minimum for all inter-component communication. | T12, T9 |

### Detective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| CL-D1 | Cross-Layer Correlation | Correlate security events across all layers to detect multi-stage attacks that span layer boundaries. | All cross-layer patterns |
| CL-D2 | Cascading Failure Detection | Monitor for cascading patterns: a compromise in one layer triggering anomalies in adjacent layers. | Cascading Trust Failure |
| CL-D3 | Privilege Chain Monitoring | Track privilege usage across the full call chain from user to agent to tool to backend. Alert on unexpected privilege escalation paths. | T3, T14, Confused Deputy |
| CL-D4 | Data Flow Integrity Monitoring | Monitor data as it flows across layer boundaries. Verify that data is not modified, exfiltrated, or injected at crossing points. | T1, T12 |

### Corrective Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| CL-C1 | System-Wide Circuit Breaker | Implement a global circuit breaker that halts all agent operations when a cross-layer compromise is detected. | All cross-layer threats |
| CL-C2 | Cascading Rollback | When a cross-layer attack is detected, roll back affected state across all impacted layers to the last known-good checkpoint. | Cascading Trust Failure, T1 + T7 |
| CL-C3 | Incident Response Orchestration | Maintain cross-layer incident response runbooks that coordinate response actions across all affected layers simultaneously. | All |

### Deterrent Controls

| ID | Mitigation | Description | Addresses ASI Threats |
|----|-----------|-------------|----------------------|
| CL-R1 | Comprehensive Security Monitoring Visibility | Publish the scope and capability of cross-layer security monitoring to all stakeholders and potential threat actors. | All |
| CL-R2 | Regular Threat Model Updates | Conduct and publish regular threat model updates. Demonstrate that the security posture is actively maintained and evolving. | All |
| CL-R3 | Red Team Exercises | Conduct regular red team exercises that test cross-layer attack scenarios. Publish summary findings to demonstrate active defense. | All cross-layer patterns |

---

## Using This Catalog

1. During Phase 7 (Mitigation Planning), reference this catalog for each identified threat.
2. For every Critical and High severity threat, ensure you have at least one Preventive and one Detective control.
3. For threats involving financial transactions or regulatory requirements, include Corrective controls for recovery.
4. Use Deterrent controls to complement technical controls, especially for insider threats and human-mediated attacks.
5. Map selected mitigations to specific threats using the threat-to-mitigation matrix.
6. Assess implementation status against the actual codebase in Phase 8 (Code Validation).

---

## Coverage Notes

The following threats from the taxonomy are not directly addressed by mitigations in this catalog for v1.0.0. Organizations should develop custom mitigations based on their specific deployment context.

**Extended threats without dedicated mitigations:**
- T33 (Infrastructure Agent Loops) — partially addressed by L3-D4 (Runaway Agent Detection) and L3-C1 (Circuit Breaker) for T32
- T34 (Wallet Key Compromise) — domain-specific to blockchain/Web3 agents
- T35 (Cross-Chain Bridge Exploitation) — domain-specific to blockchain/Web3 agents
- T36 (Malicious Agent Diffusion) — partially addressed by L7-C1 (Agent De-registration)
- T37 (Agent Registry Poisoning) — partially addressed by L7-P2 (MCP Server Registry and Verification)
- T38 (Emergent Collusion) — L7-D4 (Inter-Agent Traffic Analysis) provides detection only
- T39 (Unintended Resource Consumption) — partially addressed by L4-P5 (DDoS Protection / rate limiting), L3-R3 (Spending and Action Limits), and L3-C1 (Circuit Breaker)
- T26-T27 (Reserved) — reserved for future threat categories; no mitigations required until assigned
- T30 (Insecure Inter-Agent Protocol) — partially addressed by CL-P4 (End-to-End Encryption), L7-P1 (Mutual Authentication), and L3-P5 (Schema Validation)
- T31 (Insufficient Action Isolation) — partially addressed by L3-P3 (Agent Sandboxing) and L4-P2 (Network Segmentation)

**Blindspot Vectors (BV-1 through BV-12):** These are identified in the threat taxonomy as emerging attack patterns. Practitioners should map BVs to the closest existing mitigations and develop supplementary controls as needed. Full BV-specific mitigations are planned for v1.1.

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

## Navigation

| Previous | Up | Next |
|----------|-----|------|
| [08 - Checklists](./08-checklists.md) | [00 - Overview](./00-overview.md) | [10 - Case Studies](./10-case-studies.md) |

See also: [06 - Modeling Process](./06-modeling-process.md) | [11 - Framework Integration](./11-framework-integration.md)
