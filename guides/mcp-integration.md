# MCP Integration Guide for Agentic AI Threat Modeling

**Version:** 1.0.0

A practical guide covering Model Context Protocol (MCP) security principles and their application to threat modeling workflows, including design patterns for building MCP servers that support structured security analysis.

---

## Table of Contents

1. [MCP Architecture Overview](#mcp-architecture-overview)
2. [MCP Security Principles](#mcp-security-principles)
3. [Threat Considerations for MCP in MAESTRO Context](#threat-considerations-for-mcp-in-maestro-context)
4. [Designing an MCP Server for Security Workflows](#designing-an-mcp-server-for-security-workflows)
5. [Phase-Based Orchestration Pattern](#phase-based-orchestration-pattern)
6. [Example Workflow](#example-workflow)
7. [Best Practices](#best-practices)

---

## MCP Architecture Overview

The Model Context Protocol (MCP), as defined in the official specification (2025-03-26), establishes a standardized communication layer between AI applications and external tools or data sources. Understanding this architecture is essential for threat modeling any system that uses MCP.

### Core Architecture

MCP follows a layered client-server architecture:

```
+----------+     +----------+     +----------+
|          |     |          |     |          |
|   Host   |---->|  Client  |---->|  Server  |
|          |     |          |     |          |
+----------+     +----------+     +----+-----+
                                       |
                          +------------+------------+
                          |            |            |
                     Resources       Tools       Prompts
```

**Host**: The application that embeds the MCP client (e.g., an AI-powered IDE or coding assistant). The host manages the lifecycle of client connections, enforces security policies, and acts as the trust anchor for the user.

**Client**: A protocol-level component that maintains a 1:1 connection to a specific MCP server. The client translates host requests into MCP protocol messages, manages capability negotiation, and routes responses back to the host.

**Server**: A lightweight process that exposes capabilities to the client. Servers provide three primary capability types:

| Capability | Description | Direction |
|-----------|-------------|-----------|
| **Resources** | Structured data the server makes available (files, database records, API responses) | Server -> Client |
| **Tools** | Executable functions the server exposes (API calls, computations, data transformations) | Client -> Server |
| **Prompts** | Pre-defined prompt templates the server offers for common interactions | Server -> Client |

### Transport Mechanisms

MCP supports multiple transport mechanisms:

- **stdio**: Server runs as a subprocess of the client. Communication over standard input/output. This is the most common transport for local MCP servers.
- **Streamable HTTP**: Server exposes an HTTP endpoint. Communication over HTTP requests/responses with optional Server-Sent Events (SSE) for streaming.

---

## MCP Security Principles

The MCP specification (2025-03-26) establishes several security principles that are directly relevant to threat modeling agentic AI systems.

### 1. Tool Annotations Are Untrusted

Tool annotations (metadata describing a tool's behavior, such as whether it is read-only or destructive) **MUST be treated as untrusted** unless the server is from a trusted source. Tool annotations are informational hints, not security controls. A malicious server could annotate a destructive tool as "read-only" to bypass human review.

This is because tool annotations are self-reported metadata: a server operator can annotate a destructive tool (e.g., `deleteAllRecords`) as safe and read-only to bypass human review gates. Since the LLM uses annotations to make invocation decisions, a malicious or compromised server can influence agent behavior by misrepresenting tool capabilities. Always validate tool behavior through testing and code review, not through annotations alone.

**Implication for threat modeling**: Never rely on tool annotations as a security boundary. Always validate tool behavior independently.

### 2. Human-in-the-Loop for Tool Invocations

The MCP specification emphasizes that hosts **SHOULD** implement human-in-the-loop (HITL) controls for tool invocations, especially for tools that have side effects. Users should be presented with tool inputs before the tool is called, giving them the opportunity to review and approve or reject the invocation.

**Implication for threat modeling**: Any threat model for an MCP-based system must account for the quality and effectiveness of HITL controls. Auto-approving all tools (as some configurations allow) removes this safeguard entirely.

### 3. Server Input Validation and Rate Limiting

MCP servers **MUST**:
- Validate all inputs received from clients before processing
- Implement rate limiting to prevent abuse
- Sanitize outputs to prevent injection attacks in downstream consumers

**Implication for threat modeling**: Each MCP tool represents an attack surface. Input validation failures at the server level can lead to injection, privilege escalation, or data corruption.

### 4. Client-Side Transparency

Clients **SHOULD** show tool inputs to users before calling tools. This transparency requirement ensures that automated tool invocations do not execute without user awareness.

**Implication for threat modeling**: If a client bypasses this requirement (e.g., via auto-approve configurations), the user loses visibility into what operations are being performed on their behalf.

### 5. Consent and Data Privacy

Servers **MUST NOT** send data to external services without explicit user consent. User data transmitted through MCP should be handled in accordance with applicable data protection requirements.

**Implication for threat modeling**: Verify that MCP servers do not exfiltrate data to external endpoints, especially when processing sensitive information like financial data or PII.

---

## Threat Considerations for MCP in MAESTRO Context

When applying the MAESTRO framework to systems that use MCP, several threat categories require special attention. These map to both the OWASP ASI threat taxonomy and MCP-specific attack vectors.

### Tool Description Poisoning

**Description**: A malicious or compromised MCP server provides misleading tool descriptions that cause the LLM to invoke tools in unintended ways. For example, a tool described as "read invoice" might actually modify or delete records.

**MAESTRO Layer**: L3 (Agent Frameworks and Orchestration)

**Attack Vector**: The LLM reads tool descriptions to decide which tool to call and what parameters to pass. If descriptions are manipulated, the LLM's tool selection and parameter construction become unreliable.

**Mitigations**:
- Use only trusted, verified MCP servers
- Implement tool allowlisting at the client/host level
- Validate tool behavior independently of descriptions
- Monitor tool invocation patterns for anomalies

### Rogue Servers (ASI T47)

**Description**: An attacker deploys a malicious MCP server that impersonates a legitimate one, or modifies an existing server's configuration to point to an attacker-controlled endpoint.

**MAESTRO Layer**: L3 (Agent Frameworks and Orchestration), L4 (Deployment Infrastructure)

**Attack Vector**: MCP server configurations (typically in JSON config files like `mcp.json`) specify the command and arguments used to launch the server. An attacker who gains write access to these configurations can redirect the client to a rogue server.

**Mitigations**:
- Protect MCP configuration files with appropriate filesystem permissions
- Use code signing or integrity verification for MCP server binaries
- Monitor for unexpected changes to MCP configurations
- Restrict which MCP servers can be loaded via policy

### Client Impersonation (ASI T40)

**Description**: An attacker impersonates a legitimate MCP client to interact with a server, potentially accessing tools or resources that should be restricted.

**MAESTRO Layer**: L6 (Security, Compliance, and Governance)

**Attack Vector**: If the MCP server does not authenticate the client, any process that can connect to the server's transport (stdio pipe, HTTP endpoint) can invoke tools.

**Mitigations**:
- Implement authentication between MCP clients and servers
- Use transport-level security (TLS for HTTP transports)
- For stdio transports, ensure the server process is only accessible to the intended client
- Log and audit all client connections

### Cross-Client Interference (ASI T42)

**Description**: In multi-client environments where multiple MCP clients connect to the same server (or share state), one client's actions affect another client's state or security.

**MAESTRO Layer**: L3 (Agent Frameworks and Orchestration)

**Attack Vector**: If an MCP server maintains shared state across client sessions (e.g., a shared threat model), one client could read, modify, or corrupt another client's data.

**Mitigations**:
- Implement session isolation in MCP servers
- Use per-client state management
- Apply access controls to shared resources
- Audit cross-client state access patterns

### Network Exposure (ASI T43)

**Description**: MCP servers using HTTP transport expose network endpoints that can be discovered and attacked by network-adjacent adversaries.

**MAESTRO Layer**: L4 (Deployment Infrastructure)

**Attack Vector**: An HTTP-based MCP server listens on a network port. If improperly configured (e.g., binding to 0.0.0.0 instead of localhost), it becomes accessible to other machines on the network.

**Mitigations**:
- Prefer stdio transport for local-only servers
- For HTTP transport, bind to localhost only unless remote access is required
- Implement TLS for all HTTP MCP servers
- Use network segmentation and firewalls to restrict access
- Require authentication for all HTTP-based MCP servers

---

## Designing an MCP Server for Security Workflows

MCP is well suited for building structured, multi-phase security workflows such as threat modeling. This section describes design patterns for creating MCP servers that guide analysts through complex security analysis processes.

### Architecture Considerations

When designing an MCP server for security workflows:

- **Prefer stdio transport** for local analysis. It eliminates network exposure entirely — the server communicates only via stdin/stdout as a subprocess of the client.
- **Store state locally** in a project-scoped directory (e.g., `.threatmodel/`). This keeps the threat model co-located with the code it describes and enables version control.
- **Leverage the client's existing LLM** rather than making additional API calls. The MCP server provides structure and state management; the LLM provides analysis.
- **Do not send data to external services** unless explicitly required and consented to by the user.

### Applying MCP Principles to Security Tooling

| MCP Principle | How a Security MCP Server Should Implement It |
|---|---|
| Tool annotations | Categorize tools by workflow phase (context gathering, architecture analysis, threat identification, etc.) with descriptive annotations |
| HITL controls | Allow users to require manual approval for write operations while auto-approving read-only tools |
| Input validation | Validate all parameters — required fields for threat statements, valid component types, enumerated severity levels |
| Rate limiting | Process one tool invocation at a time via stdio, or enforce per-client rate limits for HTTP transport |
| Output sanitization | Return structured output (JSON/Markdown) to prevent injection into downstream processing |

---

## Phase-Based Orchestration Pattern

A well-designed security MCP server organizes its tools into a phased methodology. This ensures analysts follow a structured process rather than jumping between ad hoc tool calls. The MAESTRO 10-phase process provides a natural structure.

### Tool Categories

| Category | Purpose | Examples |
|----------|---------|---------|
| **Data Collection Tools** | Capture information about the system under analysis | Set business context, add components, add connections, add data stores |
| **Analysis Tools** | Apply structured analysis to collected data | Identify threat actors, detect trust boundaries, map asset flows |
| **Assessment Tools** | Score and prioritize findings | Add threats with severity/likelihood, validate security controls, calculate residual risk |
| **Reporting Tools** | Generate output artifacts | Export threat model, generate remediation reports, produce executive summaries |
| **Orchestration Tools** | Manage workflow progression | Get current phase status, advance to next phase, get phase-specific guidance |

### Phase-to-Tool Category Mapping

| Phase | Phase Name | Primary Tool Categories |
|-------|-----------|------------------------|
| 1 | Business Context Analysis | Data Collection |
| 2 | Architecture Analysis | Data Collection |
| 3 | Threat Actor Analysis | Data Collection, Analysis |
| 4 | Trust Boundary Analysis | Analysis |
| 5 | Asset Flow Analysis | Data Collection, Analysis |
| 6 | Threat Identification | Assessment |
| 7 | Mitigation Planning | Assessment |
| 8 | Code Validation Analysis | Assessment (automated) |
| 9 | Residual Risk Analysis | Assessment |
| 10 | Output Generation | Reporting |

### State Management Patterns

Security workflows are inherently stateful — data collected in Phase 1 informs analysis in Phase 6. Key patterns:

- **Cumulative state**: Each phase adds to a shared data model. Components added in Phase 2 are referenced when adding threats in Phase 6.
- **Phase gating**: Require minimum completeness thresholds before advancing. For example, require at least one component per MAESTRO layer before allowing threat identification.
- **Validation hooks**: Automatically check data consistency at phase transitions (e.g., verify that every threat references at least one component).
- **Persistence**: Save state to disk after each tool invocation so work is not lost if the session ends.

### Orchestration Tools

In addition to phase-specific tools, an effective security MCP server provides orchestration tools:

| Tool Pattern | Purpose | When to Use |
|------|---------|------------|
| Get methodology plan | Returns the full phased methodology with guidance | At the start of a new analysis |
| Follow plan step-by-step | Guides the LLM through the current phase | For automated phase progression |
| Advance phase | Moves to the next phase after validation | After completing required steps |
| Get phase status | Reports completion percentage and remaining tasks | To check progress at any time |
| Get phase guidance | Returns detailed instructions for a specific phase | When working on a specific phase |
| Get overall progress | Shows completion across all phases | For a dashboard view of the entire analysis |

### Phase Progression

A structured progression ensures thoroughness:

```
Phase 1 (Business Context)
    |
    v
Phase 2 (Architecture)
    |
    v
Phase 3 (Threat Actors)
    |
    v
Phase 4 (Trust Boundaries)
    |
    v
Phase 5 (Asset Flows)
    |
    v
Phase 6 (Threat Identification)
    |
    v
Phase 7 (Mitigation Planning)
    |
    v
Phase 8 (Code Validation) -- automatic if code is detected
    |
    v
Phase 9 (Residual Risk)
    |
    v
Phase 10 (Export)
```

Each phase should reach a validation threshold before advancing. The orchestration layer reports what percentage of the phase is complete and what tasks remain.

**Note:** Phases 3, 4, and 5 can run in parallel after Phase 2 completes. The sequential representation above is for clarity; see [06 - Modeling Process](../playbook/06-modeling-process.md) for the full dependency diagram.

---

## Example Workflow

The following demonstrates a typical workflow for threat modeling a project using an MCP-based threat modeling server.

### Starting a Threat Model

Use a prompt with your MCP-compatible client:

```
"Threat model this project using the threat modeling MCP server"
```

Being specific about using the MCP server ensures the client follows the structured phase methodology rather than taking shortcuts that could introduce hallucinations.

### Typical Progression

1. The client retrieves the full methodology plan from the server
2. The server detects whether code exists in the project directory
3. Phase 1 begins: the client reads project files and records the business context
4. The server generates clarification questions about business features (industry, data sensitivity, regulatory requirements)
5. The client answers the questions and the business context is established
6. The client progresses through phases 2-9, calling the appropriate tools at each phase
7. At Phase 8, if code is detected, the server validates security controls against the codebase
8. Phase 10 produces the final threat model report in Markdown and JSON formats

### Scoping to a Subdirectory

For large projects, scope the threat model to a specific subdirectory:

```
"Threat model this subfolder using the threat modeling MCP server"
```

This limits the scope and saves results within that subfolder.

### Iterating After Code Changes

After implementing mitigations in code, re-run validation:

```
"Update the threat model based on the code fixes that mitigated the reported threats"
```

---

## Best Practices

### For MCP Server Operators

1. **Prefer stdio transport** for local threat modeling. It eliminates network exposure entirely.
2. **Review auto-approve lists carefully**. Auto-approving all tools removes HITL controls. Consider auto-approving read-only tools and requiring approval for write operations.
3. **Protect MCP configuration files**. The configuration files control which servers are loaded and how. Unauthorized modifications can redirect the client to rogue servers.
4. **Keep servers updated**. Use the latest version of any MCP server to get security fixes and capability improvements.

### For Analysts Using MCP-Based Threat Modeling

1. **Be explicit about using the MCP server** in your prompts. This prevents the LLM from taking shortcuts or hallucinating threat data.
2. **Review tool invocations** when processing sensitive systems. The tool inputs show exactly what data is being recorded in the threat model.
3. **Validate the output** against your own knowledge. The MCP server structures the analysis, but the quality depends on the LLM's understanding of your system.
4. **Store threat models in version control**. Co-locating the threat model with code enables threat model evolution as the codebase changes.

### For Security Teams Reviewing MCP Deployments

1. **Inventory all MCP servers** deployed in your environment. Each server is a potential attack surface.
2. **Audit tool capabilities**. Understand what each MCP tool can do, especially tools that write data, invoke APIs, or access credentials.
3. **Monitor for configuration drift**. Changes to MCP configuration files should be tracked and alerted on.
4. **Apply MAESTRO L3 analysis** to any system using MCP. The agent framework layer is where tool misuse, description poisoning, and orchestration attacks occur.

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

## Navigation

- [Quickstart: 30-Minute Minimum Viable Threat Model](quickstart.md)
- [Example: Multi-Agent DevOps Deployment](../examples/devops-deployment.md)
- [Example: Generic RAG Agent](../examples/generic-rag-agent.md)
- [Playbook Overview](../playbook/00-overview.md)
- [Glossary](../GLOSSARY.md)
