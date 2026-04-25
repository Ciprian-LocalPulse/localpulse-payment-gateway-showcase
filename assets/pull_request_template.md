## Summary

<!-- A clear, concise description of what this PR does and why. Link to the relevant issue or Jira ticket. -->

Closes #<!-- issue number -->

---

## Type of Change

<!-- Check all that apply -->

- [ ] 🐛 Bug fix (non-breaking change that resolves an issue)
- [ ] ✨ New feature (non-breaking change that adds functionality)
- [ ] ⚠️ Breaking change (fix or feature that changes existing API behavior)
- [ ] 🔒 Security improvement
- [ ] ⚡ Performance improvement
- [ ] 🔧 Refactor (no functional change)
- [ ] 📚 Documentation update
- [ ] 🏗️ Infrastructure / CI change
- [ ] 🧹 Dependency update

---

## Changes Made

<!-- Describe the specific changes. For complex PRs, list key files changed and what changed in each. -->

-
-
-

---

## Testing

### How was this tested?

- [ ] Unit tests added / updated
- [ ] Integration tests added / updated
- [ ] Manual testing in sandbox environment
- [ ] E2E test coverage

### Test cases covered

<!-- List the key scenarios tested -->

1.
2.
3.

### To reproduce / verify manually

```
# Step-by-step instructions for a reviewer to verify the change
```

---

## Security Impact

<!-- ALL fields below are required. Answer honestly — "N/A" is a valid answer where applicable. -->

**Does this PR touch authentication or authorization logic?**
- [ ] Yes — describe:
- [ ] No

**Does this PR affect the Cardholder Data Environment (CDE)?**
- [ ] Yes — describe scope of change:
- [ ] No

**Does this PR introduce new external data inputs, API parameters, or webhook payloads?**
- [ ] Yes — describe validation approach:
- [ ] No

**Does this PR change how secrets, API keys, or tokens are handled?**
- [ ] Yes — describe:
- [ ] No

**Does this PR affect fraud detection or risk scoring?**
- [ ] Yes — describe impact:
- [ ] No

**Are there any new third-party dependencies introduced?**
- [ ] Yes — list them and confirm `npm audit` shows no High/Critical issues:
- [ ] No

---

## PCI DSS Impact

**Is this change within PCI DSS scope?**
- [ ] Yes — change management ticket number: ____________
- [ ] No — justification: ____________

**Have PCI DSS development guidelines been followed?** (see [CONTRIBUTING.md](../../CONTRIBUTING.md#pci-dss-development-guidelines))
- [ ] Yes
- [ ] N/A (no PCI scope impact)

---

## Breaking Changes & Migration

<!-- If this is a breaking change, this section is REQUIRED -->

**API version affected:** <!-- e.g., v2 -->

**What breaks:**

**Migration steps for consumers:**

**Deprecation timeline:**

**Migration guide link:** <!-- docs/migration/... -->

---

## Screenshots / Recordings

<!-- If this change affects the dashboard UI or developer experience, include screenshots or a short screen recording -->

---

## Reviewer Notes

<!-- Anything specific you want reviewers to focus on, areas of uncertainty, or context that helps review -->

---

## Pre-Merge Checklist

**Author confirms:**

- [ ] PR title follows Conventional Commits format (`type(scope): description`)
- [ ] Commits are GPG-signed
- [ ] All CI checks pass (tests, lint, security scan, secrets scan, coverage)
- [ ] Test coverage is at or above threshold for changed files
- [ ] No raw PANs, CVVs, secrets, or real customer data in code or tests
- [ ] CHANGELOG.md updated (if user-facing change)
- [ ] Documentation updated (if API or behavior changed)
- [ ] Migration guide written (if breaking change)
- [ ] Sandbox-tested (if touching payment flows)

---

<!-- © 2026 Ciprian Stefan Plesca · LocalPulse Payment Gateway™ -->
