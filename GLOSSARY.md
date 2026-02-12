# Glossary

**Version:** 1.0.0

## Terms

| Term | Definition |
|------|-----------|
| **Agent** | An autonomous software entity that uses an LLM/foundation model to make decisions and take actions toward a goal |
| **Agentic AI** | AI systems with autonomous decision-making, tool use, and goal-directed behavior |
| **Agentic Risk Factor** | One of four properties unique to agentic AI that amplify threats: Non-Determinism, Autonomy, Identity Management, and Agent-to-Agent Communication |
| **Attack Complexity** | A modifier in risk scoring that measures how difficult an attack is to execute. Ranges from Low (easily repeatable) to High (requires specialized conditions). Derived from CVSS methodology. |
| **Attack Surface** | The total set of points where an attacker can attempt to enter or extract data from a system. In agentic AI, the attack surface includes model inputs, tool interfaces, MCP connections, inter-agent channels, and human interaction points. |
| **Attack Vector** | The path or method an attacker uses to reach and exploit a vulnerability. Common vectors for agentic systems include network (remote), adjacent (same infrastructure), local (same host), and physical access. Derived from CVSS methodology. |
| **Blindspot Vector (BV)** | A threat category identified through supplemental MAESTRO analysis that is underrepresented in existing taxonomies (BV-1 through BV-12) |
| **Circuit Breaker** | A control pattern that automatically halts agent operations when predefined thresholds are exceeded (e.g., cost limits, error rates, action counts) |
| **Confused Deputy** | A security pattern where an agent with high privileges is tricked into acting on behalf of a lower-privilege entity |
| **Context Window** | The maximum amount of text (tokens) an LLM can process in a single inference call, including system prompt, conversation history, and retrieved context |
| **Corrective Control** | A security control that restores the system to a known-good state after a threat has been realized (e.g., rollback, incident response) |
| **Cross-Layer Threat** | A threat that emerges from the interaction of multiple MAESTRO layers, not visible in single-layer analysis |
| **Defense in Depth** | A security strategy employing multiple layered controls so that if one layer fails, subsequent layers provide protection |
| **Detective Control** | A security control that identifies and alerts on threats during or after occurrence (e.g., anomaly detection, log monitoring) |
| **Deterrent Control** | A security control that discourages threat actors through visibility of security measures (e.g., published monitoring capabilities, red team exercises) |
| **Embedding** | A numerical vector representation of text, used by RAG pipelines to measure semantic similarity between queries and documents |
| **Embedding Drift** | Gradual change in the semantic meaning of vector embeddings over time, caused by updates to the embedding model, changes in the document corpus, or adversarial manipulation. Can cause RAG systems to return incorrect or misleading results. |
| **Extended Threat Scenario** | A threat identified via MAESTRO analysis that goes beyond the base ASI T1-T15 taxonomy (T16-T47) |
| **Fine-Tuning** | The process of further training a foundation model on domain-specific data to specialize its behavior |
| **Foundation Model** | A large-scale pretrained model (e.g., LLM) that serves as the base intelligence for an agent |
| **Guardrail** | A runtime constraint that limits agent behavior, such as input/output filters, content classifiers, or action blocklists |
| **HITL** | Human-in-the-Loop -- a human reviewer who oversees and approves agent actions |
| **Layer (MAESTRO)** | One of the 7 architectural layers in the MAESTRO framework used for structured threat analysis |
| **MAS** | Multi-Agent System -- multiple autonomous agents coordinating to achieve shared or distributed goals |
| **MCP** | Model Context Protocol -- an open standard for connecting AI assistants with external data sources and tools |
| **MCP Host** | The application (e.g., AI-powered IDE, desktop app) that initiates MCP connections |
| **MCP Client** | A protocol client within the host, maintaining 1:1 connections with MCP servers |
| **MCP Server** | A lightweight program exposing specific capabilities (tools, resources, prompts) via MCP |
| **Mitigation** | A control or countermeasure that reduces the likelihood or impact of a threat |
| **MVTM** | Minimum Viable Threat Model -- the bare minimum analysis for a useful threat model, covering business context, layer mapping, top threats, trust boundaries, and key mitigations |
| **Non-Determinism** | The property of agentic AI systems producing different outputs for identical inputs, arising from LLM stochasticity, variable retrieval results, and changing agent state |
| **Policy-as-Code** | Encoding security and compliance policies in machine-executable format (e.g., OPA Rego, AWS SCPs) for automated enforcement |
| **Preventive Control** | A security control that stops threats before they occur (e.g., input validation, access controls, sandboxing) |
| **Prompt Injection** | An attack where adversarial instructions are inserted into an LLM's input to override its intended behavior or system prompt |
| **RAG** | Retrieval-Augmented Generation -- a pattern where external context is retrieved and provided to an LLM before it generates a response |
| **Residual Risk** | The risk remaining after mitigations have been applied |
| **Rubber-Stamping** | When HITL reviewers approve agent actions without meaningful review due to volume or automation bias |
| **Service Account** | A non-human identity (credentials, API keys, tokens) used by an agent or service to authenticate to other systems. Service account management is a critical concern at L4 (Deployment Infrastructure) and L6 (Security & Compliance). |
| **Supply Chain Attack** | Compromise of a system through its dependencies, including third-party models, libraries, MCP servers, plugins, or data sources |
| **System Prompt** | The initial instructions provided to an LLM that define its role, constraints, and behavior boundaries for a given application |
| **Tenant Isolation** | Controls ensuring that data and operations belonging to one user or organization are separated from those of others in a shared system |
| **Trust Boundary** | A boundary between zones of different trust levels where security controls must be enforced |
| **Trust Zone** | A region of the system where components share a common trust level |
| **Vector Database** | A specialized database optimized for storing and querying high-dimensional vector embeddings. Used in RAG pipelines (L2 - Data Operations) to find semantically similar documents. Examples include Pinecone, Weaviate, Chroma, and pgvector. |
| **Vertical Layer** | A MAESTRO layer that spans all other layers rather than sitting at a single level in the stack. L6 (Security & Compliance) is the primary vertical layer, as authentication, authorization, and compliance controls must be enforced at every other layer. |

## Acronyms

| Acronym | Expansion |
|---------|-----------|
| **A2A** | Agent-to-Agent — Google's open protocol for inter-agent communication, enabling agents to discover capabilities and exchange messages across different frameworks |
| **ABAC** | Attribute-Based Access Control |
| **ASI** | Agentic Security Initiative (OWASP) |
| **ATLAS** | Adversarial Threat Landscape for AI Systems (MITRE) |
| **ATT&CK** | Adversarial Tactics, Techniques, and Common Knowledge (MITRE) |
| **BV** | Blindspot Vector |
| **CCPA** | California Consumer Privacy Act |
| **CSA** | Cloud Security Alliance |
| **CVSS** | Common Vulnerability Scoring System |
| **CWE** | Common Weakness Enumeration (MITRE) |
| **DREAD** | Damage, Reproducibility, Exploitability, Affected Users, Discoverability (risk model) |
| **FedRAMP** | Federal Risk and Authorization Management Program (US) |
| **GDPR** | General Data Protection Regulation (EU) |
| **HIPAA** | Health Insurance Portability and Accountability Act (US) |
| **IAM** | Identity and Access Management |
| **LLM** | Large Language Model |
| **MAESTRO** | Multi-Agent Environment, Security, Threat, Risk, and Outcome |
| **MVTM** | Minimum Viable Threat Model |
| **NIST AI RMF** | National Institute of Standards and Technology AI Risk Management Framework |
| **OWASP** | Open Worldwide Application Security Project |
| **PCI-DSS** | Payment Card Industry Data Security Standard |
| **RBAC** | Role-Based Access Control |
| **RCE** | Remote Code Execution |
| **SBOM** | Software Bill of Materials |
| **SCP** | Service Control Policy |
| **SOX** | Sarbanes-Oxley Act |
| **STRIDE** | Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege |
| **TOCTOU** | Time-of-Check to Time-of-Use |
| **WAF** | Web Application Firewall |

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*
