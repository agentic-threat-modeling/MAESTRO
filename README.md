# OWASP MAESTRO Threat Modeling Playbook

**Version:** 1.0.0 | **License:** [CC BY-SA 4.0](LICENSE)

A structured, repeatable framework for threat modeling agentic AI and multi-agent systems (MAS), based on the [OWASP Multi-Agentic System Threat Modelling Guide v1.0](https://genai.owasp.org/resource/multi-agentic-system-threat-modeling-guide-v1-0/) (April 2025) and the MAESTRO framework from the Cloud Security Alliance.

---

## Who This Is For

| Audience | Start Here |
|----------|------------|
| **New to MAESTRO?** | [Quickstart Guide](guides/quickstart.md) — 30-minute minimum viable threat model |
| **Security Engineers** | [Playbook Overview](playbook/00-overview.md) then [Modeling Process](playbook/06-modeling-process.md) |
| **AI/ML Engineers** | [Layers](playbook/01-layers.md) then [Risk Factors](playbook/05-agentic-risk-factors.md) then [Checklists](playbook/08-checklists.md) |
| **Single-Agent Systems** | [Single-Agent Guide](guides/single-agent.md) |
| **MCP Users** | [MCP Integration Guide](guides/mcp-integration.md) |
| **AI-Assisted Threat Modeling?** | Clone this repo and open in [Claude Code](https://claude.com/claude-code) |

---

## AI-Assisted Threat Modeling

This playbook doubles as an interactive threat modeling agent when opened in Claude Code. The agent walks you through the full 10-phase MAESTRO process, asks targeted questions about your system, and produces structured output files.

**Quick start:**

```bash
git clone https://github.com/agentic-threat-modeling/MAESTRO.git
cd MAESTRO
# Open in Claude Code, then say:
# "threat model my system"
```

The agent will determine the appropriate analysis depth, guide you through each phase, and save all outputs to `threat-models/<project>/`. Sessions can be paused and resumed — progress is tracked in `state.json`.

> **Data handling notice:** When using this playbook as an AI agent, all content you provide — system descriptions, architecture details, source code excerpts — is transmitted to the Anthropic API for LLM processing. Review your organization's data handling policies before sharing sensitive information. The playbook itself is public; only your inputs during an engagement are transmitted.

**What you'll need to provide:**

| Phase | Input Needed |
|-------|-------------|
| **Pre-Engagement** | Answers to 5 decision-tree questions; short project name |
| **Phase 1** | Business function/purpose, criticality rating, applicable regulations, data sensitivity classifications, stakeholders, risk appetite, business assumptions |
| **Phase 2** | Component inventory (LLMs, data stores, tools, APIs, agents), technology stack, how components connect, data flow descriptions |
| **Phase 3** | Relevant threat actor categories and their applicability to this system |
| **Phase 4** | Trust zone definitions, boundary crossing descriptions, security controls at each boundary |
| **Phase 5** | Critical asset inventory, asset lifecycle (where created, stored, transmitted), protection controls in place |
| **Phase 8** | Level of source code access (full / partial / none) |

Phases 6, 7, 9, and 10 are agent-driven — the agent derives outputs from the playbook and your earlier inputs. You review and confirm each phase output.

The playbook works as standalone documentation too — the agent instructions don't change any of the reference material.

> **Security notice:** Only use the official repository at `https://github.com/agentic-threat-modeling/MAESTRO`. Forks may contain modified agent instructions, altered threat IDs, or weakened mitigations. Verify your clone's remote with `git remote -v`.

---

## Documentation Map

### Core Playbook

| # | File | Description |
|---|------|-------------|
| 00 | [Overview](playbook/00-overview.md) | Scope, audience, limitations, when to use MAESTRO |
| 01 | [MAESTRO Layers](playbook/01-layers.md) | 7 architectural layers + cross-layer analysis |
| 02 | [Threat Taxonomy](playbook/02-threat-taxonomy.md) | ASI T1-T15 + extended T16-T47 + 12 blindspot vectors |
| 03 | [Mapping Matrix](playbook/03-mapping-matrix.md) | Layer-to-threat mapping with primary/secondary indicators |
| 04 | [Cross-Layer Scenarios](playbook/04-cross-layer-scenarios.md) | 7 patterns with detailed walkthroughs |
| 05 | [Agentic Risk Factors](playbook/05-agentic-risk-factors.md) | 4 factors unique to agentic AI systems |
| 06 | [Modeling Process](playbook/06-modeling-process.md) | 10-phase process aligned to MCP server orchestration |
| 07 | [Templates](playbook/07-templates.md) | Layer mapping, threat card, assumption, business context templates |
| 08 | [Checklists](playbook/08-checklists.md) | Per-layer + cross-layer threat identification checklists |
| 09 | [Mitigation Catalog](playbook/09-mitigation-catalog.md) | Per-layer mitigations: Preventive, Detective, Corrective, Deterrent |
| 10 | [Case Studies](playbook/10-case-studies.md) | RPA processing, ElizaOS, MCP tool patterns |
| 11 | [Framework Integration](playbook/11-framework-integration.md) | STRIDE, MITRE ATT&CK/ATLAS, OWASP Top 10 for LLM 2025 |
| 12 | [Quick Reference](playbook/12-quick-reference.md) | Reference card + minimum viable threat model checklist |

### Guides

| File | Description |
|------|-------------|
| [Quickstart](guides/quickstart.md) | 30-minute minimum viable threat model walkthrough |
| [Single-Agent Systems](guides/single-agent.md) | Adapting MAESTRO for non-MAS architectures |
| [MCP Integration](guides/mcp-integration.md) | MCP security principles and design patterns for security workflows |
| [Risk Scoring](guides/risk-scoring.md) | Severity x Likelihood scoring methodology |

### Worked Examples

| File | Description |
|------|-------------|
| [DevOps Deployment](examples/devops-deployment.md) | Multi-agent CI/CD deployment system |
| [Generic RAG Agent](examples/generic-rag-agent.md) | Retrieval-augmented generation agent walkthrough |

### Reference

| File | Description |
|------|-------------|
| [Glossary](GLOSSARY.md) | Terms and acronyms |
| [Changelog](CHANGELOG.md) | Version history and source dependency tracking |
| [License](LICENSE-DOCS.md) | CC BY-SA 4.0 derivative work notice |

---

## 10-Phase Modeling Process

```
Phase 1: Business Context Analysis
Phase 2: Architecture Analysis
Phase 3: Threat Actor Analysis
Phase 4: Trust Boundary Analysis
Phase 5: Asset Flow Analysis
Phase 6: Threat Identification
Phase 7: Mitigation Planning
Phase 8: Code Validation Analysis
Phase 9: Residual Risk Analysis
Phase 10: Output Generation and Documentation
```

See [Modeling Process](playbook/06-modeling-process.md) for full details on each phase.

---

## Source Documents

| Document | Version | Date |
|----------|---------|------|
| OWASP MAS Threat Modelling Guide | v1.0 | April 2025 |
| OWASP ASI Threat Taxonomy | v1.0 | February 2025 |
| OWASP Top 10 for LLM Applications | 2025 | November 2024 |
| MCP Specification | 2025-03-26 | March 2025 |
| MAESTRO Framework (CSA) | February 2025 | February 2025 |

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines, including security-sensitive artifact review requirements and branch protection setup.

This documentation is licensed under [CC BY-SA 4.0](LICENSE). Contributions must maintain attribution to the original OWASP source and use the same license.

Extended threat IDs (T16-T47+) and blindspot vectors (BV-1 through BV-12) are project-local identifiers and may diverge from future official OWASP ASI taxonomy releases.
