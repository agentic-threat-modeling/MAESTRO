# Changelog

All notable changes to this documentation are tracked here.

## Versioning Policy

- This playbook is versioned independently from the OWASP source documents
- Source document updates are tracked in the "Source Dependencies" section
- Extended threat IDs (T16-T47+) are project-local identifiers that may diverge from future official OWASP ASI taxonomy releases

## [1.0.0] - 2026-02-13

### Initial Release

**Core Playbook (13 modules)**
- 10-phase modeling process aligned to MAESTRO framework
- 7 MAESTRO architectural layers + cross-layer analysis
- ASI threat taxonomy T1-T15 (core OWASP ASI)
- Extended threat catalog T16-T47 (project-local)
- 12 blindspot threat vectors BV-1-BV-12
- Layer-to-threat mapping matrix with primary/secondary indicators
- 7 cross-layer threat scenarios with detailed walkthroughs
- 4 agentic risk factors (Non-Determinism, Autonomy, Identity Management, Agent-to-Agent Communication)
- Mitigation catalog: Preventive/Detective/Corrective/Deterrent per layer
- Per-layer + cross-layer threat identification checklists
- Reusable templates: business context, layer mapping, threat card, mitigation card, assumption, threat actor, trust boundary, asset flow, code validation, residual risk, executive summary, remediation roadmap, threat register summary
- STRIDE, MITRE ATT&CK/ATLAS, OWASP Top 10 for LLM 2025, ASI Top 10 framework mappings
- Quick reference card + minimum viable threat model (MVTM) checklist

**Guides (4)**
- 30-minute quickstart (minimum viable threat model)
- Single-agent system adaptation guide
- MCP integration guide with security design patterns
- Risk scoring methodology (Severity x Likelihood matrix, Effective Risk formula)

**Worked Examples (2)**
- Multi-Agent DevOps Deployment system
- Generic RAG Agent

**AI-Assisted Threat Modeling Agent**
- `CLAUDE.md` agent instructions for interactive threat modeling via Claude Code
- Pre-engagement decision tree (5 questions) to determine analysis depth (Full/Standard/Lightweight)
- Single-agent and MCP system type detection during Phase 2
- Phase 6 strategy: per-layer checklist walk, cross-layer pattern assessment, BV-1-BV-12 blindspot scan, risk scoring
- Phase 7 strategy: mitigation rules (Critical = P AND D, High = P OR D + C), gap flagging
- Phase 8 strategy: untrusted-input trust boundary, applicability gate (full/partial/no code access), context management for large codebases
- Phase 9 strategy: Effective Risk formula, residual risk disposition
- Phase 10 strategy: remediation roadmap, AI-generated disclaimer, cross-reference recommendation
- Post-phase T-ID and mitigation-ID verification (Phases 6 and 7)
- Pre-engagement repository integrity check
- Data handling consent checkpoint
- Structured output convention: `threat-models/<project>/` with per-phase markdown files
- `state.json` progress tracking with `playbook_git_hash`, `files_read`, `version_metadata`
- Session resumption protocol for multi-sitting engagements
- Iterative re-analysis guidance

**Reference**
- Glossary of 50+ terms and acronyms
- CONTRIBUTING.md with security-sensitive artifact review requirements and branch protection guidance
- CC BY-SA 4.0 derivative work license declaration

---

## Source Dependencies

| Source Document | Version | Date | Status |
|---|---|---|---|
| OWASP MAS Threat Modelling Guide | v1.0 | April 22, 2025 | Current |
| OWASP ASI Threat Taxonomy | v1.0 | February 2025 | Current |
| OWASP Top 10 for LLM Applications | 2025 | November 2024 | Current |
| MCP Specification | 2025-03-26 | March 2025 | Current |
| MAESTRO Framework (CSA) | February 2025 | February 2025 | Current |

> **Staleness advisory** (as of February 2026): The OWASP ASI project released the ASI Top 10 (ASI01-ASI10) in mid-2025. This playbook's T1-T15 taxonomy aligns with the underlying ASI threat enumeration. The ASI Top 10 category mapping has been added to [11 - Framework Integration](playbook/11-framework-integration.md). Check the [OWASP ASI project page](https://owasp.org/www-project-agentic-security-initiative/) for the latest releases.

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*
