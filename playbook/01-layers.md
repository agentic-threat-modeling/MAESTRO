# 01 - MAESTRO Layers

**Version:** 1.0.0

This document describes the seven MAESTRO architectural layers plus the Cross-Layer dimension. Each layer isolates a specific category of components and concerns, enabling systematic threat analysis. For the threats that map to each layer, see the [Threat Taxonomy](./02-threat-taxonomy.md).

---

## Table of Contents

1. [Layer 1: Foundation Model](#layer-1-foundation-model)
2. [Layer 2: Data Operations](#layer-2-data-operations)
3. [Layer 3: Agent Frameworks](#layer-3-agent-frameworks)
4. [Layer 4: Deployment Infrastructure](#layer-4-deployment-infrastructure)
5. [Layer 5: Evaluation and Observability](#layer-5-evaluation-and-observability)
6. [Layer 6: Security and Compliance (Vertical)](#layer-6-security-and-compliance-vertical)
7. [Layer 7: Agent Ecosystem](#layer-7-agent-ecosystem)
8. [Cross-Layer](#cross-layer)

---

## Layer 1: Foundation Model

| Attribute | Details |
|-----------|---------|
| **Focus** | Integrity of LLMs and pretrained models; model alignment; poisoning and manipulation |
| **Components** | LLM/foundation model, model API, inference endpoint, model weights, fine-tuning pipeline |
| **Key Questions** | Is the model managed (API) or self-hosted? Can it be fine-tuned? What alignment guarantees exist? How non-deterministic are outputs? |
| **Primary ASI Threats** | T1 (Memory Poisoning, if memory used for training), T7 (Misaligned & Deceptive Behaviour) |

For detailed threats at this layer, see [Foundation Model threats in the Threat Taxonomy](./02-threat-taxonomy.md#layer-1-foundation-model---extended-threats).

---

## Layer 2: Data Operations

| Attribute | Details |
|-----------|---------|
| **Focus** | Vector store integrity, prompt management, retrieval attacks, data pipeline security |
| **Components** | Vector databases, RAG pipeline, embedding models, document stores (object storage, etc.), prompt templates, data ingestion pipelines |
| **Key Questions** | What data feeds the RAG? Who can modify source documents? How are embeddings updated? Is shared memory used between agents? |
| **Primary ASI Threats** | T1 (Memory Poisoning), T12 (Agent Communication Poisoning) |

For detailed threats at this layer, see [Data Operations threats in the Threat Taxonomy](./02-threat-taxonomy.md#layer-2-data-operations---extended-threats).

---

## Layer 3: Agent Frameworks

| Attribute | Details |
|-----------|---------|
| **Focus** | Execution logic, workflow control, autonomy boundaries, tool orchestration |
| **Components** | Agent framework (Strands, LangChain, CrewAI, etc.), MCP servers, tool definitions, workflow state machine, system prompts, planning/reflection loops |
| **Key Questions** | What tools does the agent have access to? How is tool selection controlled? What are the autonomy boundaries? Can the agent modify its own workflow? |
| **Primary ASI Threats** | T2 (Tool Misuse), T5 (Cascading Hallucinations), T6 (Intent Breaking) |

For detailed threats at this layer, see [Agent Frameworks threats in the Threat Taxonomy](./02-threat-taxonomy.md#layer-3-agent-frameworks---extended-threats).

---

## Layer 4: Deployment Infrastructure

| Attribute | Details |
|-----------|---------|
| **Focus** | Runtime security, container/serverless security, orchestration, networking, MLSecOps |
| **Components** | Compute (serverless functions, VMs, containers), orchestration (event buses, workflow engines), networking (VPC, load balancers), storage (document databases, object storage), messaging (email services, message queues) |
| **Key Questions** | What is the deployment model? What IAM roles exist? Is there network segmentation? Are service accounts properly scoped? |
| **Primary ASI Threats** | T3 (Privilege Compromise), T4 (Resource Overload), T13 (Rogue Agents), T14 (Human Attacks on MAS) |

For detailed threats at this layer, see [Deployment Infrastructure threats in the Threat Taxonomy](./02-threat-taxonomy.md#layer-4-deployment-infrastructure---extended-threats).

---

## Layer 5: Evaluation and Observability

| Attribute | Details |
|-----------|---------|
| **Focus** | Monitoring, alerting, logging, HITL interfaces, audit trails, anomaly detection |
| **Components** | Logging systems (cloud monitoring services, etc.), dashboards, HITL review interfaces, anomaly detection, audit trails, performance monitoring |
| **Key Questions** | Can agent decisions be traced end-to-end? Is there immutable audit logging? Can human reviewers meaningfully oversee agent volume? Are there behavioral drift alerts? |
| **Primary ASI Threats** | T8 (Repudiation & Untraceability), T10 (Overwhelming HITL) |

For detailed threats at this layer, see [Evaluation and Observability threats in the Threat Taxonomy](./02-threat-taxonomy.md#layer-5-evaluation--observability---extended-threats).

---

## Layer 6: Security and Compliance (Vertical)

| Attribute | Details |
|-----------|---------|
| **Focus** | Access controls, policy enforcement, regulatory constraints, identity management |
| **Components** | IAM/RBAC systems, authentication (identity providers, OAuth), secrets management, compliance policies (SOX, GDPR, HIPAA), policy enforcement engines, service control policies |
| **Key Questions** | Is there RBAC with separation of duties? Does authorization propagate end-to-end through the agent? Are there runtime guardrails? What regulations apply? |
| **Primary ASI Threats** | T3 (Privilege Compromise), T7 (Misaligned & Deceptive Behaviour) |
| **Note** | This is a **vertical layer** that spans all other layers |

For detailed threats at this layer, see [Security and Compliance threats in the Threat Taxonomy](./02-threat-taxonomy.md#layer-6-security--compliance---extended-threats).

---

## Layer 7: Agent Ecosystem

| Attribute | Details |
|-----------|---------|
| **Focus** | Interaction with humans, external tools, other agents, external systems |
| **Components** | External APIs, third-party services, human users, other AI agents, agent registries, A2A protocols, MCP ecosystem |
| **Key Questions** | What external systems does the agent interact with? Can users manipulate the agent through social engineering? Is there agent-to-agent communication? Are there trust assumptions about external systems? |
| **Primary ASI Threats** | T9 (Identity Spoofing), T13 (Rogue Agents), T14 (Human Attacks on MAS), T15 (Human Trust Manipulation) |

For detailed threats at this layer, see [Agent Ecosystem threats in the Threat Taxonomy](./02-threat-taxonomy.md#layer-7-agent-ecosystem---extended-threats).

---

## Cross-Layer

| Attribute | Details |
|-----------|---------|
| **Focus** | Emergent behaviors from multi-layer interaction, cascading failures, privilege chains |
| **Key Questions** | Can a compromise in one layer cascade to others? Are there confused deputy patterns? Can trust relationships be chained to escalate access? Do biases amplify across layers? |
| **Primary ASI Threats** | T6 (Intent Breaking), T12 (Agent Communication Poisoning), T13 (Rogue Agents), T15 (Human Trust Manipulation) |

Cross-layer analysis is where the highest-value findings typically emerge. Threats that span multiple layers exploit interactions that single-layer analysis misses. See [Threat Taxonomy](./02-threat-taxonomy.md) for the extended threat catalog at each layer.

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

| Previous | Up | Next |
|----------|-----|------|
| [00 - Overview](./00-overview.md) | [00 - Overview](./00-overview.md) | [02 - Threat Taxonomy](./02-threat-taxonomy.md) |
