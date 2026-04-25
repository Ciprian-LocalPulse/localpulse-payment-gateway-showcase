# Governance

## LocalPulse Payment Gateway™ — Repository Governance

This document defines the governance model, decision-making processes, and roles for the LocalPulse Payment Gateway™ repository.

---

## Overview

The LocalPulse Payment Gateway™ is proprietary software owned by Ciprian Stefan Plesca. Governance is structured to ensure:

- Security and PCI DSS compliance are never compromised
- Codebase quality and reliability meet enterprise standards
- Decisions are made transparently and with appropriate oversight
- Authorized contributors have clear processes for proposing and implementing changes

---

## Repository Structure & Ownership

### Ultimate Authority

**Ciprian Stefan Plesca** — Owner, LocalPulse.pro™

The Owner holds final authority over all decisions affecting the product, architecture, security posture, and licensing of the LocalPulse Payment Gateway™.

### Engineering Leadership

| Role | Responsibilities |
|------|-----------------|
| **Engineering Lead** | Day-to-day technical direction, architecture decisions, release approvals |
| **Security Lead** | Security review authority, vulnerability management, PCI DSS compliance oversight |
| **QA Lead** | Test coverage standards, release quality gates, regression management |
| **Infrastructure Lead** | Deployment, scalability, and operational reliability |

### Code Ownership

Ownership of specific modules is defined in [CODEOWNERS](CODEOWNERS). Owners are automatically requested as reviewers for PRs affecting their modules and have veto authority over changes to their domain.

---

## Decision-Making Framework

### Tier 1: Routine Decisions

**Scope:** Bug fixes, dependency updates, minor features, documentation  
**Process:** Standard PR review with 1 approval from a relevant CODEOWNER  
**Timeline:** Merge when approved and CI passes  

### Tier 2: Significant Changes

**Scope:** New features, API changes, new integrations, performance optimizations, non-critical refactors  
**Process:**
1. Open a GitHub Issue with the "RFC" label describing the proposal
2. 5-business-day comment period for stakeholder input
3. Engineering Lead approval required
4. Standard PR process (2 approvals)

**Timeline:** RFC discussion + standard PR timeline

### Tier 3: Architecture Decisions

**Scope:** Core architecture changes, new payment processors, changes to the CDE boundary, breaking API changes, new compliance requirements  
**Process:**
1. Author an Architecture Decision Record (ADR) in `docs/adr/`
2. 10-business-day review period
3. Approval required from: Engineering Lead + Security Lead + Owner
4. Full security review required

**Timeline:** ADR process + extended PR timeline

### Tier 4: Security & Emergency Changes

**Scope:** Security vulnerability fixes, critical incident response, emergency hotfixes  
**Process:**
1. Coordinated through private Security Advisory or directly with Security Lead
2. Expedited review: Security Lead + Engineering Lead
3. Can bypass standard RFC process; ADR written post-deployment if applicable

**Timeline:** As fast as safely possible; P0 targets < 24 hours

---

## Branch Protection Rules

### `main` Branch

- Direct push: ❌ Disabled (Owner only in emergency)
- Required reviews: 2 minimum
- Require review from CODEOWNERS: ✅
- Dismiss stale reviews on new commits: ✅
- Require status checks: ✅ (CI, security scan, secrets scan, coverage)
- Require signed commits: ✅
- Require linear history: ✅ (squash merge only)
- Allow force push: ❌

### `release/*` Branches

- Direct push: Engineering Lead only
- Required reviews: 2 (must include Security Lead for security releases)
- Require signed commits: ✅

---

## Release Governance

### Release Authority

| Release Type | Authority |
|--------------|-----------|
| Patch (x.x.Z) | Engineering Lead |
| Minor (x.Y.0) | Engineering Lead + Owner sign-off |
| Major (X.0.0) | Owner approval required |
| Security hotfix | Security Lead + Engineering Lead |

### Release Checklist

Before any production release:

- [ ] All CI checks passing on release branch
- [ ] Security scan clean (no High/Critical findings unaddressed)
- [ ] Test coverage at or above thresholds
- [ ] Changelog entry complete and reviewed
- [ ] Migration guide written (if breaking changes)
- [ ] Customer communications drafted (if breaking changes or security fixes)
- [ ] Rollback plan documented
- [ ] Security Lead sign-off
- [ ] Engineering Lead sign-off

### Versioning Policy

We follow [Semantic Versioning](https://semver.org/):

- **PATCH** — Backward-compatible bug fixes and security patches
- **MINOR** — New backward-compatible features
- **MAJOR** — Breaking changes (rare; requires migration guide and extended deprecation period)

API breaking changes require a minimum **6-month deprecation notice** with the legacy version continuing to function in parallel.

---

## Architecture Decision Records (ADRs)

Significant architectural decisions are documented as ADRs in `docs/adr/`. Each ADR follows the format:

```markdown
# ADR-NNNN: Title

**Status:** Proposed | Accepted | Deprecated | Superseded by ADR-XXXX  
**Date:** YYYY-MM-DD  
**Authors:** @username  
**Reviewers:** @username, @username  

## Context
[What is the situation that motivated this decision?]

## Decision
[What is the change being proposed or made?]

## Rationale
[Why was this decision made over alternatives?]

## Consequences
[What are the positive and negative consequences?]

## Security & PCI Considerations
[How does this affect our security posture or PCI DSS scope?]
```

Current ADRs: `docs/adr/README.md`

---

## PCI DSS Governance

Given our PCI DSS Level 1 certification, certain governance controls are mandatory:

### Change Management

All changes to the Cardholder Data Environment (CDE) must:

1. Be documented with a change ticket referencing business justification
2. Undergo security review before deployment
3. Be tested in a non-production environment with equivalent controls
4. Have a documented rollback procedure
5. Be logged in the change management system for audit purposes

CDE scope is defined in `docs/PCI_SCOPE.md`.

### Separation of Duties

- Developers cannot deploy directly to production
- The same individual cannot both approve and merge a PR to `main`
- The Security Lead must not be the sole reviewer of their own security changes
- Production database access requires dual authorization

### Annual Reviews

The following are reviewed and updated annually:

- CODEOWNERS assignments
- Access control lists and repository permissions
- Security policies and standards
- PCI DSS compliance controls
- Third-party component security review

---

## Conflict Resolution

In cases of disagreement:

1. **Technical disputes** — Escalate to Engineering Lead; Engineering Lead decision is final on technical matters
2. **Security disputes** — Escalate to Security Lead; Security Lead has veto authority over any change deemed a security risk
3. **Product/business disputes** — Escalate to Owner; Owner decision is final

---

## Governance Review

This governance document is reviewed annually (or following a significant incident or organizational change) by the Engineering Lead and Owner.

Last reviewed: **April 2026**  
Next scheduled review: **April 2027**

---

## Contact

| Role | Contact |
|------|---------|
| Owner | https://cal.com/ciprian-stefan-plesca |
| Engineering | engineering@localpulse.pro |
| Security | security@localpulse.pro |

---

*© 2026 Ciprian Stefan Plesca · LocalPulse Payment Gateway™ · All Rights Reserved*
