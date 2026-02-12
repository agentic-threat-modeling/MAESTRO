# Contributing

This documentation is licensed under [CC BY-SA 4.0](LICENSE). All contributions must maintain attribution to the original OWASP source documents and use the same license.

---

## General Guidelines

1. All content is Markdown. No code files, no build steps, no dependencies.
2. Follow existing formatting conventions (table styles, heading levels, link patterns).
3. Reference the [Glossary](GLOSSARY.md) for standard terminology. Add new terms if introducing new concepts.
4. Update [CHANGELOG.md](CHANGELOG.md) with every change.

---

## Security-Sensitive Artifacts

The following artifacts have direct impact on threat model accuracy. Changes to these require review by a **security-qualified maintainer** before merging:

| Artifact | File(s) | Why It Matters |
|----------|---------|---------------|
| Threat IDs (T1-T47) | `playbook/02-threat-taxonomy.md` | Incorrect IDs propagate into every threat model produced. A changed ID, altered description, or wrong severity breaks downstream analysis. |
| Blindspot Vectors (BV-1-BV-12) | `playbook/02-threat-taxonomy.md`, `playbook/08-checklists.md` | Emerging threats that may not be caught by standard review. Changes affect Phase 6 completeness. |
| Mitigation Catalog IDs (L*-P*/D*/C*/R*) | `playbook/09-mitigation-catalog.md` | Mitigation IDs are referenced by agents and templates. Wrong IDs lead to non-existent controls being cited. |
| Risk Scoring Tables | `guides/risk-scoring.md` | Severity x Likelihood matrix and Effective Risk formula drive all risk calculations. Subtle changes (e.g., swapping "High" and "Medium" in one cell) corrupt risk assessments. |
| Checklist Items | `playbook/08-checklists.md` | Per-layer checklists are the primary threat discovery mechanism. Missing items = missed threats. |
| Agent Instructions | `CLAUDE.md` | Controls all AI agent behavior. A modified instruction can suppress threats, alter scoring, or change the workflow. |
| Templates | `playbook/07-templates.md` | Output structure for all threat model deliverables. Changes affect every engagement. |

**Review checklist for security-sensitive changes:**

- [ ] No threat IDs were added, removed, or renumbered without updating `03-mapping-matrix.md` and `08-checklists.md`
- [ ] No mitigation IDs were changed without updating `09-mitigation-catalog.md` cross-references
- [ ] Risk scoring changes are consistent with the matrix in `guides/risk-scoring.md`
- [ ] If a checklist item was removed, the rationale is documented in the PR description
- [ ] Agent instruction changes do not weaken security controls (e.g., removing verification steps, relaxing mitigation requirements)

---

## Extended Identifiers

- **T16-T47** (extended threat catalog) and **BV-1-BV-12** (blindspot vectors) are project-local identifiers.
- They may diverge from future official OWASP ASI taxonomy releases.
- When the OWASP ASI taxonomy publishes new identifiers, reconcile project-local IDs with the official numbering and document the mapping in `CHANGELOG.md`.

---

## Branch Protection Requirements

The `main` branch should have the following protections enabled. These are required to mitigate **playbook poisoning via supply chain** (an attacker modifying playbook content to suppress threats or weaken mitigations).

### Required Settings

| Setting | Value | Rationale |
|---------|-------|-----------|
| Require pull request reviews | Yes, minimum 1 reviewer | Prevents unreviewed changes to the knowledge base |
| Require status checks | Yes (when CI is configured) | Ensures changes pass validation before merge |
| Disable force push | Yes | Prevents history rewriting that could hide malicious changes |
| Disable branch deletion | Yes | Prevents accidental or malicious deletion of the main branch |
| Require linear history | Recommended | Simplifies audit trail |

### Setup via GitHub UI

1. Go to **Settings > Branches > Branch protection rules**
2. Click **Add rule**
3. Branch name pattern: `main`
4. Enable: "Require a pull request before merging"
5. Set "Required approving reviews" to **1**
6. Enable: "Dismiss stale pull request approvals when new commits are pushed"
7. Enable: "Do not allow bypassing the above settings"
8. Enable: "Restrict who can push to matching branches" (optional — limit to maintainers)
9. Click **Create**

### Setup via `gh` CLI

```bash
gh api repos/{owner}/{repo}/branches/main/protection \
  --method PUT \
  --field required_pull_request_reviews='{"required_approving_review_count":1,"dismiss_stale_reviews":true}' \
  --field enforce_admins=true \
  --field restrictions=null \
  --field required_status_checks=null
```

### Future: GPG-Signed Releases

When GPG signing infrastructure is available:

1. Generate a GPG key for release signing
2. Publish the key fingerprint in `README.md`
3. Sign all tagged releases with `git tag -s`
4. Document verification instructions for users

This mitigates **malicious fork / rogue repository attacks** by providing cryptographic proof of release authenticity.

---

*Attribution: OWASP GenAI Security Project - Multi-Agentic System Threat Modelling Guide. Licensed under CC BY-SA 4.0.*
