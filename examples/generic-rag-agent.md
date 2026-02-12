# Worked Example: Generic RAG Agent Threat Model

**Version:** 1.0.0

A complete MAESTRO threat model walkthrough for a customer support AI agent using Retrieval-Augmented Generation (RAG). This example is designed as a learning guide that you can follow to build your own threat model for any RAG-based agentic system.

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Business Context (Phase 1)](#business-context-phase-1)
3. [Architecture and Layer Mapping (Phase 2)](#architecture-and-layer-mapping-phase-2)
4. [Threat Actors (Phase 3)](#threat-actors-phase-3)
5. [Trust Boundaries (Phase 4)](#trust-boundaries-phase-4)
6. [Asset Flows (Phase 5)](#asset-flows-phase-5)
7. [Threat Identification (Phase 6)](#threat-identification-phase-6)
8. [Mitigations (Phase 7)](#mitigations-phase-7)
9. [Code Validation (Phase 8)](#code-validation-phase-8)
10. [Residual Risk (Phase 9)](#residual-risk-phase-9)
11. [Export and Next Steps (Phase 10)](#export-and-next-steps-phase-10)

> **Note on Threat IDs:** Threat identifiers in this example (T01-T18) are local to this worked example and do not correspond to the ASI taxonomy numbering. See the ASI tags in each threat entry for the canonical ASI reference.

> **Threat ID Cross-Reference:** The following maps this example's local threat IDs to the canonical ASI taxonomy.
>
> | Local ID | ASI Mapping | ASI Threat Name |
> |----------|-------------|-----------------|
> | T01 | T6 | Intent Breaking & Goal Manipulation |
> | T02 | T5 | Cascading Hallucinations |
> | T03 | T1 | Memory Poisoning |
> | T04 | T12 | Agent Communication Poisoning |
> | T05 | T3 | Privilege Compromise |
> | T06 | T2 | Tool Misuse |
> | T07 | T6 | Intent Breaking & Goal Manipulation |
> | T08 | T3 | Privilege Compromise |
> | T09 | T4 | Resource Overload |
> | T10 | T10 | Overwhelming HITL |
> | T11 | T8 | Repudiation & Untraceability |
> | T12 | T3 | Privilege Compromise |
> | T13 | T7 | Misaligned & Deceptive Behaviour |
> | T14 | T15 | Human Trust Manipulation |
> | T15 | T2 | Tool Misuse |
> | T16 | T2, T6 | Tool Misuse, Intent Breaking & Goal Manipulation |
> | T17 | T1, T7 | Memory Poisoning, Misaligned & Deceptive Behaviour |
> | T18 | T3, T13 | Privilege Compromise, Rogue Agents |

---

## System Overview

### The System

A customer support AI agent for a SaaS company. The agent answers customer questions by retrieving relevant information from an internal knowledge base (product documentation, FAQs, troubleshooting guides, past support tickets) and generating natural language responses. It handles Tier 1 support requests, escalating complex issues to human agents.

### Components

| Component | Technology | Purpose |
|----------|-----------|---------|
| LLM | Cloud-hosted LLM API | Generates responses from retrieved context |
| Vector Database | Managed vector store (e.g., Pinecone, pgvector, OpenSearch) | Stores and retrieves document embeddings |
| Document Store | Cloud object storage | Stores source documents (product docs, FAQs, tickets) |
| Agent Framework | Python framework (e.g., Strands, LangChain) | Orchestrates RAG pipeline and tool usage |
| REST API | API gateway + compute (serverless or containerized) | Exposes agent to customer-facing applications |
| HITL Review Queue | Internal dashboard | Routes flagged responses to human agents for review |
| CRM Integration | External CRM API (e.g., Salesforce, Zendesk) | Creates tickets, updates customer records, logs interactions |

### Architecture Diagram (Conceptual)

```
Customer (Web Chat / Mobile App)
          |
          v
    [REST API Gateway]
          |
          v
    [Agent Framework]
     |       |       |
     v       v       v
  [LLM]  [Vector DB] [CRM API]
             |
             v
       [Document Store]

    [HITL Review Dashboard]
          |
          v
    Human Support Agent
```

---

## Business Context (Phase 1)

### System Description

Customer Support AI Agent -- an automated support system that handles Tier 1 customer inquiries for a B2B SaaS platform. The agent retrieves relevant documentation from an internal knowledge base using RAG, generates responses, and either delivers them directly to customers or routes them to human agents for review. The system integrates with an external CRM to create and update support tickets. It processes customer questions that may contain PII (names, email addresses, account IDs) and proprietary product information.

**MCP Tool**: `set_business_context(description="Customer Support AI Agent -- an automated support system...")`

### Business Features

| Feature | Value | Rationale |
|---------|-------|-----------|
| Industry Sector | Technology (SaaS) | B2B software company |
| Data Sensitivity | Confidential | Customer PII, proprietary product information |
| User Base Size | Large | Thousands of customers interacting daily |
| Geographic Scope | Global | Customers across multiple jurisdictions |
| Regulatory Requirements | GDPR, CCPA, SOC 2 | Global customer base, handling PII |
| System Criticality | Medium | Important but not life-safety; manual fallback exists |
| Financial Impact | Medium | Revenue impact from poor support, but no direct financial transactions |
| Authentication | OAuth 2.0 + API Keys | Customer-facing API with tenant isolation |
| Deployment Environment | Cloud (Public) | Managed cloud services |
| Integration Complexity | Moderate | LLM API, vector DB, CRM, document store |

### Assumptions

| ID | Assumption | Impact |
|----|-----------|--------|
| A001 | The LLM is consumed as a managed API; model weights are not accessible or modifiable | Reduces model poisoning risk; prompt injection remains primary model-layer threat |
| A002 | The system is single-agent (no agent-to-agent communication) | MAS-specific threats like agent collusion are not applicable |
| A003 | Human agents have access to the HITL dashboard but not to the agent framework or infrastructure | Limits insider threat scope to CRM and customer data, not system configuration |

**MCP Tool**: `add_assumption(description="...", category="...", impact="...", rationale="...")`

---

## Architecture and Layer Mapping (Phase 2)

### MAESTRO Layer Mapping

| Layer | Layer Name | Components | Key Observations |
|-------|-----------|-----------|-----------------|
| L1 | Foundation Model | Cloud LLM API | Managed API, no fine-tuning. Prompt injection is the primary attack vector. The model generates customer-facing text, so hallucinations directly impact customers. |
| L2 | Data Operations | Vector database, document store, embedding pipeline | RAG is the core data operation. Document quality and integrity directly determine response accuracy. Embedding pipeline runs on a schedule to index new documents. |
| L3 | Agent Frameworks | Python agent framework (Strands/LangChain) | Orchestrates the RAG retrieval, LLM call, and CRM integration. Has tools for knowledge base search, ticket creation, and escalation. |
| L4 | Deployment Infrastructure | API gateway, serverless/containers, managed vector DB, object storage, IAM | Standard cloud deployment. API gateway handles rate limiting and authentication. Compute is serverless or containerized. |
| L5 | Evaluation and Observability | Application logs, HITL review dashboard, response quality metrics | Flagged responses (low confidence, sensitive topics) routed to human review queue. Response quality tracked via customer feedback. |
| L6 | Security and Compliance | OAuth 2.0, API keys, tenant isolation, IAM, encryption | Multi-tenant system requires strict data isolation. GDPR right-to-deletion applies to customer data in the knowledge base. |
| L7 | Agent Ecosystem | Customers (chat/API), human support agents (dashboard), CRM system (Salesforce/Zendesk) | Customers are untrusted input sources. CRM is a trusted external system. Human agents are the HITL control. |

**MCP Tools**: `add_component(name, type, service_provider, description)` for each component, `add_connection(source, destination, protocol)` for each connection, `add_data_store(name, type, classification)` for each data store.

---

## Threat Actors (Phase 3)

| Actor | Type | Capability | Motivation | Relevant? | Priority |
|-------|------|-----------|-----------|-----------|----------|
| Malicious Customer | External | Low-Medium | Data theft, service abuse, competitive intelligence | Yes | High |
| Disgruntled Employee | Insider | Medium | Revenge, data theft | Yes | Medium |
| Competitor | External | Medium | Competitive intelligence, service disruption | Yes | Medium |
| External Attacker | External | Medium-High | Financial gain, data theft | Yes | High |
| Automated Bot | External | Low | Scraping, enumeration, DoS | Yes | Medium |
| Compromised Vendor | Third Party | Medium | Supply chain attack (CRM, LLM provider) | Yes | Low |

### Key Threat Actor Analysis

**Malicious Customer** is the highest-priority actor because they have direct, authenticated access to the agent via the chat interface. They can submit arbitrary text that becomes part of the LLM's input context. This makes prompt injection a first-class threat.

**External Attacker** is high priority because the REST API is internet-facing. API authentication bypass or credential theft gives full access to the agent's capabilities.

**MCP Tools**: `add_threat_actor(name, type, capability_level, motivations, resources, description)`, `set_threat_actor_relevance(id, relevant, rationale)`, `set_threat_actor_priority(id, priority)`

---

## Trust Boundaries (Phase 4)

### Trust Zones

| Zone | Trust Level | Components |
|------|-----------|-----------|
| Customer Zone | Untrusted | Customer browsers, mobile apps, API clients |
| API Gateway | Low | REST API Gateway, rate limiting, authentication |
| Application Tier | Medium | Agent framework, serverless/containers, embedding pipeline |
| Data Tier | High | Vector database, document store, application database |
| External Services | Variable | LLM API (trusted provider), CRM API (trusted partner) |
| Internal Operations | High | HITL dashboard, admin tools, monitoring |

### Trust Boundaries

| Boundary | From -> To | Controls | Key Risk |
|----------|-----------|----------|---------|
| Internet to API | Customer -> API Gateway | OAuth 2.0, API keys, rate limiting, WAF | Credential theft, API abuse, DDoS |
| API to Application | Gateway -> Agent | Input validation, request authorization | Prompt injection payloads passing through |
| Application to Data | Agent -> Vector DB / Document Store | IAM policies, encryption, tenant isolation | Cross-tenant data leakage, unauthorized data access |
| Application to LLM | Agent -> LLM API | API key authentication, TLS | Prompt injection reaching the model, response manipulation |
| Application to CRM | Agent -> CRM API | OAuth 2.0, TLS, field-level mapping | Excessive data sharing, CRM as exfiltration channel |
| Email Ingress to Data | Embedding pipeline -> Document Store -> Vector DB | Access controls on document upload, content scanning | Knowledge base poisoning via malicious documents |
| Internal to Application | HITL Dashboard -> Agent / Database | Internal auth, VPN, RBAC | Insider misuse of review capabilities |

### High-Risk Boundary: Customer Input to LLM

The path from customer input to the LLM is the highest-risk boundary crossing in this system:

```
Customer text -> API Gateway -> Agent Framework -> [Combined with RAG context] -> LLM
```

At every step, the customer's text retains its original content. The agent framework combines it with retrieved documents and a system prompt before sending to the LLM. If the customer's text contains prompt injection, it arrives at the LLM alongside trusted system instructions and retrieved documents, potentially overriding them.

**MCP Tools**: `add_trust_zone(name, trust_level, description)`, `add_trust_boundary(name, type, controls, description)`, `add_crossing_point(name, description)`

---

## Asset Flows (Phase 5)

### Critical Assets

| Asset | Classification | Location | Description |
|-------|---------------|----------|-------------|
| Customer PII | Confidential | CRM, application logs, potentially vector DB | Names, emails, account IDs in customer queries |
| Product Documentation | Internal | Document store, vector DB | Proprietary product information, troubleshooting procedures |
| Support Ticket History | Confidential | CRM, potentially vector DB | Past customer interactions including PII and issue details |
| API Credentials | Restricted | Secrets manager, environment variables | LLM API keys, CRM OAuth tokens, database credentials |
| Agent System Prompt | Internal | Agent framework configuration | Instructions that govern agent behavior and guardrails |
| Customer Conversations | Confidential | Application logs, CRM | Full conversation transcripts |

### Key Data Flows

| Flow | From | To | Data | Sensitivity |
|------|------|-----|------|------------|
| Customer Query | Customer | Agent Framework | Free-text question (may contain PII) | Confidential |
| RAG Retrieval | Agent Framework | Vector DB | Query embedding + retrieved document chunks | Internal |
| LLM Request | Agent Framework | LLM API | System prompt + RAG context + customer query | Confidential |
| LLM Response | LLM API | Agent Framework | Generated answer text | Internal |
| CRM Ticket Creation | Agent Framework | CRM API | Customer ID, query summary, agent response | Confidential |
| HITL Review | Agent Framework | Review Dashboard | Flagged response + customer query + context | Confidential |
| Document Indexing | Document Store | Embedding Pipeline -> Vector DB | Document chunks + embeddings | Internal |

**MCP Tools**: `add_asset(name, classification, description)`, `add_flow(name, source, destination, data, sensitivity)`

---

## Threat Identification (Phase 6)

### L1: Foundation Model Threats

#### T01: Prompt Injection via Customer Query

**Statement**: A malicious customer with direct access to the chat interface can craft a prompt injection payload in their support question that overrides the system prompt instructions, causing the LLM to ignore its customer support role and instead disclose system prompt contents, return data from other tenants' retrieved documents, or generate harmful content.

**Impact**: System prompt disclosure revealing guardrails and business logic, cross-tenant data leakage, generation of harmful or misleading responses to customers, reputational damage.

**Tags**: MAESTRO-L1, ASI-T6, Prompt-Injection, LLM01-2025

#### T02: Hallucinated Support Information

**Statement**: The LLM, when unable to find relevant information in the retrieved RAG context, may fabricate product features, invent troubleshooting steps, or generate incorrect technical guidance rather than acknowledging the knowledge gap.

**Impact**: Customers follow incorrect instructions leading to data loss or service disruption, erosion of trust in support quality, potential liability if hallucinated advice causes damage, increased escalation volume.

**Tags**: MAESTRO-L1, ASI-T5, Hallucination, Data-Fabrication

### L2: Data Operations Threats

#### T03: Knowledge Base Poisoning

**Statement**: An insider or attacker with write access to the document store can inject malicious or incorrect content into the knowledge base, which gets indexed into the vector database and subsequently retrieved by the RAG pipeline, causing the agent to provide incorrect, biased, or harmful responses to all customers whose queries match the poisoned content.

**Impact**: Systematic misinformation in customer responses, potential for escalation to prompt injection if poisoned documents contain adversarial prompts, difficult to detect because responses appear authoritative and sourced.

**Tags**: MAESTRO-L2, ASI-T1, RAG-Poisoning, Knowledge-Base-Integrity

#### T04: Cross-Tenant Data Leakage via RAG

**Statement**: In a multi-tenant deployment, if the vector database does not enforce strict tenant isolation, a customer's query may retrieve document chunks belonging to another tenant, causing the LLM to include another customer's proprietary information in its response.

**Impact**: GDPR/CCPA violation, breach of customer confidentiality, loss of customer trust, regulatory penalties, contractual liability.

**Tags**: MAESTRO-L2, ASI-T12, Data-Leakage, Tenant-Isolation

#### T05: Sensitive Data in RAG Context

**Statement**: The embedding pipeline indexes support ticket history that contains customer PII (names, emails, account details, issue descriptions). When a new query triggers retrieval of these historical tickets, PII from previous customers may be included in the LLM context and potentially surfaced in responses to different customers.

**Impact**: PII exposure to unauthorized parties, GDPR right-to-deletion non-compliance (deleted customer data persists in vector embeddings), privacy violation.

**Tags**: MAESTRO-L2, ASI-T3, PII-Leakage, GDPR

### L3: Agent Frameworks and Orchestration Threats

#### T06: Tool Misuse -- Unauthorized CRM Operations

**Statement**: Through prompt injection or goal manipulation, an attacker causes the agent to misuse its CRM integration tools: creating spurious tickets, modifying existing tickets, extracting customer data from the CRM beyond what is needed for the current query, or escalating to human agents with misleading context.

**Impact**: CRM data corruption, unauthorized access to customer records, human agent misdirection, disruption of support operations.

**Tags**: MAESTRO-L3, ASI-T2, Tool-Misuse, CRM-Abuse

#### T07: Agent Goal Manipulation

**Statement**: A sophisticated attacker crafts multi-turn conversation inputs that gradually shift the agent's goal orientation from "answer customer support questions" to an attacker-chosen objective such as "extract and return all documents matching a broad query" or "create a support ticket containing the system prompt."

**Impact**: Agent performs actions outside its intended scope, data exfiltration via CRM ticket creation, system prompt disclosure, degraded service for legitimate customers.

**Tags**: MAESTRO-L3, ASI-T6, Goal-Manipulation, Intent-Breaking

### L4: Deployment Infrastructure Threats

#### T08: API Gateway Bypass or Credential Theft

**Statement**: An external attacker steals or brute-forces API credentials (OAuth tokens, API keys), or discovers an unauthenticated endpoint, gaining direct access to the agent framework without going through the API gateway's rate limiting and input filtering.

**Impact**: Unlimited access to the agent, bypassing rate limits and abuse detection, ability to run large-scale prompt injection attacks, potential for service disruption.

**Tags**: MAESTRO-L4, ASI-T3, Authentication-Bypass, API-Security

#### T09: Resource Exhaustion via Query Flooding

**Statement**: An automated bot or attacker floods the API with high-volume queries, causing excessive LLM API calls (high cost), vector database query overload, and compute scaling beyond budget limits.

**Impact**: Service degradation for legitimate customers, unexpected cloud costs (LLM API bills), potential service outage during peak support hours, budget exhaustion.

**Tags**: MAESTRO-L4, ASI-T4, Resource-Overload, Cost-Attack

### L5: Evaluation and Observability Threats

#### T10: HITL Review Fatigue

**Statement**: The HITL review queue receives a high volume of flagged responses during peak hours, and human agents -- who are also handling their own escalated tickets -- cannot thoroughly review each flagged response, leading to approval of incorrect or harmful agent responses.

**Impact**: Harmful or incorrect responses reach customers without meaningful human review, the HITL control becomes a rubber stamp, quality metrics degrade, customer trust erodes.

**Tags**: MAESTRO-L5, ASI-T10, HITL-Overwhelm, Review-Fatigue

#### T11: Insufficient Logging for Incident Response

**Statement**: Conversation logs, RAG retrieval results, and LLM inputs/outputs are not comprehensively logged or are logged without adequate structure, making it impossible to reconstruct what the agent said to a customer, what context it used, and why it produced a particular response.

**Impact**: Cannot investigate customer complaints about agent responses, cannot identify root cause of incorrect information, compliance audits fail due to missing evidence, inability to detect systematic agent misbehavior.

**Tags**: MAESTRO-L5, ASI-T8, Audit-Trail, Observability-Gap

### L6: Security and Compliance Threats

#### T12: API Key Exposure

**Statement**: LLM API keys or CRM OAuth credentials are exposed in application logs, error messages, environment variables readable by unauthorized services, or committed to version control, allowing unauthorized access to these external services.

**Impact**: Unauthorized LLM API usage (cost attack), unauthorized CRM access (data breach), attacker impersonates the agent to external services.

**Tags**: MAESTRO-L6, ASI-T3, Credential-Exposure, Secret-Management

#### T13: GDPR Right-to-Deletion Non-Compliance

**Statement**: When a customer exercises their right to deletion under GDPR, their data is removed from the CRM and application database but persists in vector database embeddings and document store indexes, where it may continue to be retrieved and surfaced in future agent responses.

**Impact**: GDPR violation and regulatory penalty, customer PII persists after deletion request, potential litigation, reputational damage.

**Tags**: MAESTRO-L6, GDPR, Data-Retention, Right-to-Deletion

### L7: Agent Ecosystem Threats

#### T14: Social Engineering via Agent

**Statement**: An attacker uses the support agent as a social engineering vector by manipulating it (via prompt injection) to impersonate a company representative and instruct customers to visit phishing sites, provide credentials, or take actions that compromise their accounts.

**Impact**: Customers defrauded via trusted support channel, reputational damage, legal liability, loss of customer trust in AI-powered support.

**Tags**: MAESTRO-L7, ASI-T15, Social-Engineering, Customer-Trust

#### T15: CRM Integration Abuse

**Statement**: An attacker exploits the agent's CRM integration to exfiltrate customer data by crafting queries that cause the agent to retrieve customer records from the CRM and include them in responses, or by manipulating the agent into creating CRM tickets that contain data from other customers.

**Impact**: Customer data breach via CRM, cross-customer data exposure, GDPR/CCPA violation, abuse of trusted integration channel.

**Tags**: MAESTRO-L7, ASI-T2, CRM-Exfiltration, Data-Breach

### Cross-Layer Threats

#### T16: Prompt Injection to CRM Data Exfiltration (L1 -> L3 -> L7)

**Statement**: An attacker combines prompt injection (L1) with tool misuse (L3) to cause the agent to query the CRM for another customer's data and include it in the chat response, or to create a CRM ticket containing exfiltrated data that the attacker can access through other means.

**Impact**: Multi-step attack chain bypassing per-layer controls, data breach spanning customer support and CRM systems, difficult to detect because each individual action appears legitimate.

**Tags**: MAESTRO-Cross-Layer, ASI-T2, ASI-T6, Chained-Attack

#### T17: Knowledge Base Poisoning to Customer Harm (L2 -> L1 -> L7)

**Statement**: An attacker poisons the knowledge base with documents containing both incorrect technical guidance and embedded prompt injection payloads. When retrieved, the poisoned documents cause the LLM to deliver dangerous instructions to customers while suppressing the confidence flags that would trigger HITL review.

**Impact**: Customers receive harmful instructions from a trusted source, HITL controls bypassed by suppressing flags, systematic and difficult to detect because the poisoned content appears to come from legitimate documentation.

**Tags**: MAESTRO-Cross-Layer, ASI-T1, ASI-T7, RAG-Poisoning-Chain

#### T18: Credential Theft to Full System Compromise (L4 -> L6 -> L7)

**Statement**: An attacker exploits an infrastructure vulnerability to access environment variables or secrets manager, retrieves LLM API keys and CRM credentials, and uses them to directly interact with the LLM (bypassing all application-level controls) and the CRM (accessing customer data without audit trails).

**Impact**: Complete bypass of application security controls, unauthorized access to all customer data in CRM, ability to use LLM API without rate limits or logging, persistent undetected access.

**Tags**: MAESTRO-Cross-Layer, ASI-T3, ASI-T13, Credential-Theft-Chain

**MCP Tools**: `add_threat(source, prerequisites, action, impact, tags)` for each threat

---

## Mitigations (Phase 7)

### L1 Mitigations

| Threat | Mitigation | Type |
|--------|-----------|------|
| T01: Prompt Injection | Implement input preprocessing to detect and neutralize injection patterns. Use LLM guardrails (provider-specific safety filters) to filter outputs. Apply output validation to ensure responses stay within the customer support domain. | Preventive + Detective |
| T02: Hallucination | Implement confidence scoring on RAG retrieval. When retrieval confidence is below threshold, respond with "I don't have information on that topic" instead of generating. Require citations (source document references) in all agent responses. | Preventive |

### L2 Mitigations

| Threat | Mitigation | Type |
|--------|-----------|------|
| T03: KB Poisoning | Implement document upload approval workflow. Enable versioning and integrity checksums on document store. Run content scanning on uploaded documents before indexing. Alert on unexpected document changes. | Preventive + Detective |
| T04: Cross-Tenant Leakage | Enforce tenant-level partitioning in the vector database (separate namespaces or metadata filtering). Validate tenant context on every retrieval query. Implement post-retrieval filtering to verify all returned chunks belong to the requesting tenant. | Preventive |
| T05: PII in RAG | Implement PII detection and redaction in the embedding pipeline before indexing. Apply data retention policies to vector embeddings. Tag indexed documents with data classification metadata. | Preventive |

### L3 Mitigations

| Threat | Mitigation | Type |
|--------|-----------|------|
| T06: CRM Tool Misuse | Implement tool allowlisting with per-tool rate limits. Restrict CRM operations to read-only for the agent (ticket creation requires human approval). Validate CRM query scope against current customer context. | Preventive |
| T07: Goal Manipulation | Implement conversation length limits. Reset system prompt context periodically in long conversations. Monitor for topic drift and flag conversations that deviate from support topics. | Preventive + Detective |

### L4 Mitigations

| Threat | Mitigation | Type |
|--------|-----------|------|
| T08: API Bypass | Implement OAuth 2.0 with short-lived tokens and refresh rotation. Use API Gateway as the sole entry point with no bypass routes. Enable WAF rules for common attack patterns. | Preventive |
| T09: Resource Exhaustion | Implement per-tenant rate limiting at the API gateway. Set budget alerts on LLM API spend. Configure auto-scaling with maximum limits. Implement circuit breakers for downstream services. | Preventive + Detective |

### L5 Mitigations

| Threat | Mitigation | Type |
|--------|-----------|------|
| T10: HITL Fatigue | Implement priority-based review queuing. Set maximum batch sizes per reviewer. Establish minimum review time per response. Deploy automated quality sampling (random re-review). | Preventive + Detective |
| T11: Logging Gaps | Implement structured logging of all agent interactions: customer query, retrieved context, LLM input, LLM output, tools invoked, final response. Use immutable log storage with retention policies. | Preventive |

### L6 Mitigations

| Threat | Mitigation | Type |
|--------|-----------|------|
| T12: API Key Exposure | Store all credentials in a secrets manager. Rotate keys on a schedule. Scan logs and code for credential patterns. Use IAM roles instead of static credentials where possible. | Preventive + Detective |
| T13: GDPR Deletion | Implement a data deletion pipeline that removes customer data from vector database (delete embeddings), document store (delete associated documents), and application logs. Track deletion completeness with audit records. | Preventive |

### L7 Mitigations

| Threat | Mitigation | Type |
|--------|-----------|------|
| T14: Social Engineering | Implement output filtering to block URLs not on an approved domain list. Prohibit the agent from requesting credentials or directing customers to take security-sensitive actions. Display clear "AI-generated" labels on all responses. | Preventive |
| T15: CRM Abuse | Restrict CRM queries to the current customer's records only (enforce customer ID scoping). Log all CRM operations for audit. Implement anomaly detection on CRM query patterns. | Preventive + Detective |

### Cross-Layer Mitigations

| Threat | Mitigation | Type |
|--------|-----------|------|
| T16: Injection to Exfiltration | Defense in depth: input filtering at L1 + tool scope restrictions at L3 + CRM query scoping at L7. No single layer is sufficient; all three must be implemented. | Preventive |
| T17: KB Poisoning Chain | Document approval workflow (L2) + output validation (L1) + HITL review for low-confidence responses (L5). Breaking any link in the chain prevents the attack. | Preventive + Detective |
| T18: Credential Theft Chain | Secrets manager with rotation (L6) + infrastructure hardening (L4) + per-service credential isolation + audit logging on all credential access (L5). | Preventive + Detective |

**MCP Tools**: `add_mitigation(content)`, `link_mitigation_to_threat(mitigation_id, threat_id)`

---

## Code Validation (Phase 8)

> **Note:** Phase 8 (Code Validation) is not applicable to this worked example, which uses
> a hypothetical system without a real codebase. In a real engagement, this phase validates
> that mitigations identified in Phase 7 are correctly implemented in the actual code. See the
> Code Validation template in [07 - Templates](../playbook/07-templates.md) for the field structure.

---

## Residual Risk (Phase 9)

After applying mitigations, the following residual risks remain:

### High Residual Risk

| Risk | Why It Persists | Recommended Action |
|------|----------------|-------------------|
| Prompt injection (T01) | No input filter is 100% effective against adversarial prompts. New injection techniques continuously emerge. | Accept risk with layered controls. Monitor for new attack techniques. Update filters regularly. |
| Hallucination (T02) | LLMs are inherently non-deterministic. Confidence scoring reduces but does not eliminate fabrication. | Accept risk. Require citations. Implement customer feedback loops. Conduct periodic accuracy audits. |

### Medium Residual Risk

| Risk | Why It Persists | Recommended Action |
|------|----------------|-------------------|
| GDPR deletion completeness (T13) | Vector embeddings are derived from source documents; deleting the source does not automatically invalidate the embedding. Full re-indexing may be required. | Implement periodic re-indexing. Maintain a deletion tracking log. Accept residual risk of brief PII persistence between deletion request and re-indexing. |
| HITL review quality (T10) | Human attention is finite. Under sustained high volume, review quality will degrade. | Implement automated quality sampling. Set hard batch limits. Accept that some percentage of responses will receive cursory review. |

### Low Residual Risk

| Risk | Why It Persists | Recommended Action |
|------|----------------|-------------------|
| CRM credential compromise (T18) | Despite rotation and secrets management, a brief window exists between compromise and rotation. | Accept risk. Ensure rotation frequency is adequate. Monitor for anomalous CRM access patterns. |

**MCP Tool**: `generate_remediation_report()`

---

## Export and Next Steps (Phase 10)

### Exporting the Threat Model

If using the Threat Modeling MCP Server:

```
"Export the threat model and generate the final report"
```

This calls `export_comprehensive_threat_model` and `export_threat_model_with_remediation_status`, producing:
- A comprehensive Markdown report (similar to this document)
- A JSON export for integration with security tools
- A remediation status report tracking mitigation implementation

### Summary Statistics

| Metric | Count |
|--------|-------|
| Total Threats | 18 (15 per-layer + 3 cross-layer) |
| Total Mitigations | 18 |
| High Residual Risks | 2 |
| Medium Residual Risks | 2 |
| Low Residual Risks | 1 |
| MAESTRO Layers Covered | 7 + Cross-Layer |
| Threat Actors Analyzed | 6 |
| Trust Boundaries | 7 |

### Prioritized Next Steps

1. **Immediate**: Implement tenant isolation in vector database (T04) -- highest-impact, lowest-effort mitigation
2. **Immediate**: Deploy input preprocessing for prompt injection detection (T01) -- highest-likelihood threat
3. **Short term**: Implement PII detection and redaction in embedding pipeline (T05) -- GDPR compliance
4. **Short term**: Restrict CRM tools to read-only with human approval for writes (T06) -- reduces tool misuse risk
5. **Medium term**: Build comprehensive logging pipeline (T11) -- enables detection of all other threats
6. **Medium term**: Implement GDPR deletion pipeline for vector embeddings (T13) -- regulatory requirement
7. **Ongoing**: Monitor for new prompt injection techniques and update filters (T01) -- evolving threat

### Using This Example for Your Own System

This walkthrough demonstrates the complete MAESTRO threat modeling process from business context to export. To apply it to your own RAG-based agent:

1. **Replace the system description** with your own in Phase 1
2. **Map your actual components** to the MAESTRO layers in Phase 2
3. **Adjust threat actors** based on your user base and threat landscape in Phase 3
4. **Draw your actual trust boundaries** based on your deployment architecture in Phase 4
5. **Trace your actual data flows** in Phase 5
6. **Use the per-layer threat checklists** (from the [Quickstart Guide](../guides/quickstart.md)) as a starting point in Phase 6, then add system-specific threats
7. **Tailor mitigations** to your technology stack and risk tolerance in Phase 7
8. **Be honest about residual risk** in Phase 9 -- no system is fully mitigated
9. **Prioritize by impact and effort** in Phase 10 -- address the highest-impact, lowest-effort items first

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

## Navigation

- [MCP Integration Guide](../guides/mcp-integration.md)
- [Quickstart: 30-Minute Minimum Viable Threat Model](../guides/quickstart.md)
- [Example: Multi-Agent DevOps Deployment](devops-deployment.md)
- [Playbook Overview](../playbook/00-overview.md)
- [Glossary](../GLOSSARY.md)
