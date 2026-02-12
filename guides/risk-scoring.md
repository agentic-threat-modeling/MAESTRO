# Risk Scoring Methodology

**Version:** 1.0.0

A structured methodology for scoring and prioritizing threats identified during a MAESTRO threat modeling engagement. All scales and categories in this guide are derived from the standard threat modeling data model used in MAESTRO-compatible tooling.

---

## Severity Scale (ThreatSeverity)

Severity measures the potential impact of a threat if it is successfully exploited.

| Level | Description | Examples |
|-------|-------------|----------|
| **Low** | Cosmetic or minor impact. No meaningful data loss, no financial exposure, no compliance violation. System continues to function correctly. | Minor UI glitch caused by unexpected agent output; verbose log messages exposing non-sensitive metadata |
| **Medium** | Degraded functionality. Limited data exposure, minor financial impact, or temporary service disruption. Workarounds exist. | Agent produces incorrect but non-critical calculations; temporary inability to process requests; minor data quality degradation in RAG pipeline |
| **High** | Significant data or financial impact. Confidential data exposure, material financial loss, compliance violation, or extended service outage. | Unauthorized access to confidential financial data; agent posts incorrect journal entries to ERP system; extended denial of service on critical workflow |
| **Critical** | System compromise or safety risk. Full system takeover, large-scale data breach, safety-critical failure, or regulatory enforcement action. | Agent credential compromise leading to full backend system access; exfiltration of all financial records; agent makes autonomous decisions that violate regulatory controls without any audit trail |

---

## Likelihood Scale (ThreatLikelihood)

Likelihood measures the probability of a threat being exploited given current controls and the threat landscape.

| Level | Description | Attacker Profile |
|-------|-------------|-----------------|
| **Unlikely** | Requires a sophisticated attacker AND multiple simultaneous control failures. No known exploits in the wild for this attack pattern. | Nation-state or highly skilled attacker with insider knowledge; requires chaining 3+ vulnerabilities |
| **Possible** | Requires moderate skill or a single control failure. Attack pattern is known but not trivially exploitable. | Competent external attacker or malicious insider; requires exploiting 1-2 vulnerabilities with some preparation |
| **Likely** | A straightforward attack path exists. The vulnerability is known and tools or techniques are readily available. | Moderately skilled attacker using available tooling; single misconfiguration or missing control is sufficient |
| **Very Likely** | Attack path is well-known and easily exploitable. Minimal skill required. Active exploitation may already be occurring in similar systems. | Low-skill attacker or automated scanning; default configurations are vulnerable; public exploit code exists |

---

## Risk Matrix

Risk Level is determined by the intersection of Severity and Likelihood.

| | **Unlikely** | **Possible** | **Likely** | **Very Likely** |
|---|---|---|---|---|
| **Critical** | High | Critical | Critical | Critical |
| **High** | Medium | High | Critical | Critical |
| **Medium** | Low | Medium | High | High |
| **Low** | Low | Low | Medium | Medium |

**How to read:** Find the Severity row, then the Likelihood column. The intersection is the Risk Level.

---

## Risk Level Definitions

| Risk Level | Required Action | Timeframe |
|-----------|----------------|-----------|
| **Critical** | Immediate remediation required. Block deployment or production use until mitigated. Escalate to security leadership. | Before next release / within 24 hours |
| **High** | Remediation required. Plan mitigation for current or next sprint. Track in security backlog with high priority. | Within 1-2 sprints |
| **Medium** | Remediation recommended. Plan mitigation within the quarter. Acceptable to proceed with documented risk acceptance if mitigations are in progress. | Within current quarter |
| **Low** | Monitor and address opportunistically. Document the risk. No immediate action required, but review during next threat model refresh. | Next scheduled review |

---

## Attack Vector Modifier (AttackVector)

The attack vector describes how the attacker reaches the vulnerable component. It serves as a modifier to the base risk assessment -- threats with broader attack vectors are generally higher priority because they are accessible to more attackers.

| Vector | Description | Modifier Effect |
|--------|-------------|----------------|
| **Network** | Exploitable remotely over the network (internet or internal network). No special access required beyond network connectivity. | Increases effective risk. Broadest attacker pool. |
| **Adjacent** | Requires the attacker to be on the same network segment, shared physical/logical boundary (e.g., same VPC, same Bluetooth range). | Slightly increases effective risk. Narrower but still significant attacker pool. |
| **Local** | Requires local system access (e.g., shell access, local user account, physical console). | Neutral. Assumes attacker has already gained some foothold. |
| **Physical** | Requires physical access to the hardware (e.g., USB port, hardware security module). | Decreases effective risk in cloud-hosted deployments. Relevant for edge/IoT agents. |

**Application:** When two threats have the same Risk Level, prioritize the one with the broader attack vector (Network > Adjacent > Local > Physical).

---

## Attack Complexity Modifier (AttackComplexity)

Attack complexity describes the conditions beyond the attacker's control that must exist for the attack to succeed.

| Complexity | Description | Modifier Effect |
|-----------|-------------|----------------|
| **Low** | No special conditions required. The attack can be reliably reproduced whenever the attacker chooses. | Increases effective risk. Attack is repeatable and reliable. |
| **High** | Requires specific conditions that the attacker cannot reliably control -- timing windows, race conditions, specific configurations, or victim interaction. | Decreases effective risk. Attack may fail often or require patience. |

**Application:** When two threats have the same Risk Level and Attack Vector, prioritize the one with lower complexity (Low > High).

---

## Mitigation Prioritization

### Prioritization Formula

The core prioritization formula accounts for the current mitigation status:

```
Effective Risk = Risk Level x (1 - Implementation Status)
```

Where Implementation Status is:

| Status | Value | Description |
|--------|-------|-------------|
| Not Implemented | 0.0 | No mitigation in place |
| Partially Implemented | 0.5 | Mitigation exists but is incomplete, untested, or has known gaps |
| Implemented | 1.0 | Mitigation is fully deployed, tested, and operational |

**Risk Level numeric mapping for calculation:**

| Risk Level | Numeric Value |
|-----------|---------------|
| Low | 1 |
| Medium | 2 |
| High | 3 |
| Critical | 4 |

**Example:** A Critical risk (4) with a Partially Implemented mitigation (0.5) has an Effective Risk of 4 x (1 - 0.5) = 2.0 (equivalent to Medium). A Critical risk with no mitigation has an Effective Risk of 4 x (1 - 0) = 4.0 (remains Critical).

### Multiple Mitigations per Threat

When multiple mitigations address the same threat, use the **strongest (highest) Implementation Status** among all mitigations for that threat in the Effective Risk formula. If any single mitigation is fully implemented, that mitigation provides its intended risk reduction regardless of the status of other mitigations.

**Example:** Threat with Critical risk (4). Mitigation A is Partially Implemented (0.5), Mitigation B is Implemented (1.0).
- Use the strongest status: 1.0
- Effective Risk = 4 × (1 - 1.0) = 0

If the fully-implemented mitigation only provides **partial coverage** of the threat (e.g., it addresses one attack vector but not another), treat the overall Implementation Status as Partially Implemented (0.5) rather than Implemented (1.0). The Implementation Status reflects coverage breadth, not just deployment status.

Residual risk assessment should account for qualitative factors beyond the formula: attack surface complexity, attacker motivation, detection capability, and blast radius. The formula provides a starting point for prioritization, not a definitive risk rating.

### Effective Risk vs. Residual Risk

These terms are related but distinct:

- **Effective Risk** is the *quantitative* output of the formula: `Risk Level x (1 - Implementation Status)`. It measures how much risk reduction has been achieved by implemented mitigations. Use Effective Risk for prioritization and mitigation sequencing.

- **Residual Risk** is the *qualitative* assessment of remaining risk after all mitigations are applied, accounting for factors that the formula cannot capture: zero-day vulnerabilities, novel attack techniques, human error, organizational risk appetite, and environmental changes. Residual Risk is documented in Phase 9 and reviewed by stakeholders.

**Relationship:** Effective Risk is an input to Residual Risk assessment, not a replacement for it. A threat with Effective Risk of 0 (fully mitigated) may still carry Residual Risk if the mitigation depends on assumptions that could be invalidated (e.g., "the vendor will maintain this library" or "the firewall rules will not be modified").

**When to use each:**
| Context | Use |
|---------|-----|
| Mitigation prioritization (Phase 7) | Effective Risk |
| Risk register for stakeholders (Phase 9) | Residual Risk |
| Executive reporting (Phase 10) | Residual Risk with Effective Risk as supporting data |
| Automated dashboards and tooling | Effective Risk |

### Cost-Effectiveness Analysis (MitigationCost and MitigationEffectiveness)

When multiple mitigations compete for limited resources, use Cost and Effectiveness to decide which to implement first.

| Cost | Description |
|------|-------------|
| **Low** | Configuration change, policy update, or minor code change. Can be done within hours to a day. |
| **Medium** | Engineering work requiring design and implementation. Fits within a single sprint. |
| **High** | Significant architecture change, new tooling procurement, or cross-team coordination. Multi-sprint effort. |

| Effectiveness | Description |
|--------------|-------------|
| **Low** | Marginal risk reduction. Addresses a narrow aspect of the threat. |
| **Medium** | Meaningful risk reduction. Substantially reduces likelihood or limits impact. |
| **High** | Largely eliminates the threat or reduces it to an acceptable level. |

### Cost-Effectiveness Matrix

Use this matrix to prioritize which mitigations to implement first:

| | **Low Cost** | **Medium Cost** | **High Cost** |
|---|---|---|---|
| **High Effectiveness** | **Implement immediately** -- best ROI | **Implement soon** -- strong ROI | **Plan carefully** -- high impact but expensive |
| **Medium Effectiveness** | **Implement soon** -- easy win | **Evaluate** -- moderate ROI | **Defer** unless risk is Critical |
| **Low Effectiveness** | **Implement opportunistically** | **Defer** -- poor ROI | **Do not implement** -- poor ROI |

---

## Mitigation Types (MitigationType)

Each mitigation falls into one of four categories. A robust defense typically includes multiple types.

| Type | Description | When to Use |
|------|-------------|-------------|
| **Preventive** | Stops the attack from succeeding. Reduces likelihood. | First line of defense. Apply to all Critical and High risks. |
| **Detective** | Identifies the attack in progress or after the fact. Does not prevent the attack but enables response. | Essential complement to preventive controls. Required for all risk levels to maintain visibility. |
| **Corrective** | Recovers from the attack. Limits damage after a successful exploit. | Required for High and Critical risks. Backup plans, rollback mechanisms, incident response procedures. |
| **Deterrent** | Discourages the attacker from attempting the attack. Does not prevent or detect. | Useful complement but never sufficient alone. Legal notices, audit logging visibility, rate limiting. |

**Defense-in-depth principle:** For Critical risks, plan at least one Preventive AND one Detective mitigation. For High risks, plan at least one Preventive OR one Detective mitigation plus a Corrective mitigation.

---

## Complete Prioritization Workflow

1. **Score each threat** using the Severity and Likelihood scales to determine the Risk Level from the matrix.
2. **Apply modifiers** -- note the Attack Vector and Attack Complexity for each threat.
3. **Check existing mitigations** -- calculate Effective Risk using the Implementation Status formula.
4. **Rank threats** by Effective Risk (descending), then by Attack Vector breadth (Network first), then by Attack Complexity (Low first).
5. **For each unmitigated or partially mitigated threat**, identify candidate mitigations and score their Cost and Effectiveness.
6. **Prioritize mitigations** using the Cost-Effectiveness Matrix. When resources are constrained, prefer High Effectiveness / Low Cost mitigations first.
7. **Document residual risk** -- after planned mitigations, re-score each threat and document the expected residual Risk Level.

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*

---

| Navigation | |
|---|---|
| **Quick Reference** | [Quick Reference Card](../playbook/12-quick-reference.md) |
| **Templates** | [Templates](../playbook/07-templates.md) |
| **Single-Agent Guide** | [Single-Agent Systems](./single-agent.md) |
| **Playbook Overview** | [Playbook Overview](../playbook/00-overview.md) |
