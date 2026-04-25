# Contributing to LocalPulse Payment Gateway™

Thank you for your interest in contributing to the LocalPulse Payment Gateway™. This is a **proprietary, closed-source repository**. External contributions are accepted exclusively from:

- Approved technology partners with active partnership agreements
- Contractors engaged under a signed Statement of Work (SOW) and NDA
- LocalPulse.pro™ employees and authorized staff

If you believe you should have contributor access and do not, contact **engineering@localpulse.pro**.

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Development Environment](#development-environment)
- [Branching Strategy](#branching-strategy)
- [Commit Standards](#commit-standards)
- [Pull Request Process](#pull-request-process)
- [Code Standards](#code-standards)
- [Testing Requirements](#testing-requirements)
- [Security Considerations](#security-considerations)
- [PCI DSS Development Guidelines](#pci-dss-development-guidelines)
- [Review & Approval Process](#review--approval-process)
- [Release Process](#release-process)

---

## Prerequisites

Before making any contribution, ensure you have:

- [ ] Signed the LocalPulse Contributor Agreement (CLA)
- [ ] Completed the Security Awareness Training (HR portal)
- [ ] Completed the PCI DSS Developer Training (required annually)
- [ ] Configured your development machine per the [Security Baseline](docs/SECURITY_BASELINE.md)
- [ ] Obtained sandbox API credentials from the engineering team
- [ ] Enabled disk encryption on your development machine
- [ ] Configured git commit signing with your GPG key

### GPG Commit Signing (Required)

All commits must be cryptographically signed:

```bash
# Generate a GPG key if you don't have one
gpg --full-generate-key

# Get your key ID
gpg --list-secret-keys --keyid-format=long

# Configure git to sign commits
git config --global user.signingkey <YOUR_KEY_ID>
git config --global commit.gpgsign true

# Export your public key to add to GitHub
gpg --armor --export <YOUR_KEY_ID>
```

Add your public key to your GitHub account under Settings → SSH and GPG keys.

---

## Development Environment

### Setup

```bash
# Clone the repository (requires authorized access)
git clone git@github.com:localpulse-pro/payment-gateway.git
cd payment-gateway

# Install dependencies
npm install

# Copy environment template
cp .env.sandbox.example .env.local

# Fill in sandbox credentials (obtain from engineering@localpulse.pro)
# NEVER use production credentials in development
nano .env.local

# Start local development server
npm run dev

# Verify setup
npm run health-check
```

### Required Tools

| Tool | Minimum Version | Purpose |
|------|-----------------|---------|
| Node.js | 18.0.0 | Runtime |
| npm | 9.0.0 | Package management |
| Docker | 24.0.0 | Local services |
| Docker Compose | 2.20.0 | Orchestration |
| git | 2.40.0 | Version control |

### Local Services

```bash
# Start all required local services (Postgres, Redis, mock processor)
docker compose up -d

# Verify services are healthy
docker compose ps
```

---

## Branching Strategy

We follow **GitHub Flow** with additional protections for payment-sensitive code:

```
main                    ← Production-ready code (protected)
  └── release/2.5       ← Release preparation branches
  └── hotfix/CVE-xxxx   ← Emergency security fixes
  └── feature/your-name/short-description
  └── fix/your-name/short-description
  └── chore/your-name/short-description
```

### Branch Naming

| Type | Pattern | Example |
|------|---------|---------|
| Feature | `feature/<name>/<description>` | `feature/jane/subscription-proration` |
| Bug fix | `fix/<name>/<description>` | `fix/john/refund-idempotency` |
| Security | `security/<name>/<description>` | `security/jane/cve-2026-1234` |
| Hotfix | `hotfix/<description>` | `hotfix/processor-timeout` |
| Chore | `chore/<name>/<description>` | `chore/john/update-stripe-sdk` |

### Branch Rules

- `main` requires 2 approvals + security review for payment-critical paths
- Force-push is disabled on all protected branches
- Branches must be up-to-date with `main` before merge
- Stale branches (> 60 days inactive) are automatically archived

---

## Commit Standards

We use [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

| Type | Usage |
|------|-------|
| `feat` | New feature or capability |
| `fix` | Bug fix |
| `security` | Security improvement or vulnerability fix |
| `perf` | Performance improvement |
| `refactor` | Code restructuring without behavior change |
| `test` | Adding or improving tests |
| `docs` | Documentation only |
| `chore` | Maintenance, dependency updates |
| `ci` | CI/CD changes |

### Scopes

Common scopes: `charges`, `subscriptions`, `invoices`, `fraud`, `routing`, `vault`, `webhooks`, `auth`, `customers`, `refunds`, `disputes`, `payouts`, `api`, `sdk`

### Examples

```bash
feat(subscriptions): add proration support for mid-cycle plan changes

fix(routing): correct Adyen failover logic when primary processor times out

security(vault): rotate encryption key derivation to PBKDF2-SHA256 600k iterations

perf(charges): add Redis caching for BIN lookup results, reduces p99 by 40ms
```

### What NOT to Include in Commits

- Real API keys, secrets, or credentials (even test/sandbox)
- Raw PANs, CVVs, or any real card data
- Real customer PII
- Internal IP addresses or infrastructure hostnames
- Processor account identifiers

---

## Pull Request Process

### Before Opening a PR

```bash
# Ensure your branch is current
git fetch origin
git rebase origin/main

# Run the full test suite
npm test

# Run linting
npm run lint

# Run security scan
npm run security-scan

# Check for secrets accidentally committed
npm run secrets-scan
```

### PR Requirements

Every PR must:

- [ ] Pass all CI checks (tests, lint, security scan, secrets scan)
- [ ] Include tests for all new code paths (minimum 90% coverage on changed files)
- [ ] Update relevant documentation
- [ ] Have a clear description of what changed and why
- [ ] Link to the relevant issue or Jira ticket
- [ ] Not decrease overall test coverage
- [ ] Pass the automated PCI control check (if touching CDE-adjacent code)

### PR Template

When you open a PR, you'll be prompted to fill in our [PR template](.github/PULL_REQUEST_TEMPLATE/pull_request_template.md). Key sections:

- **Summary** — What does this PR do?
- **Type of change** — Feature / fix / security / breaking change
- **Testing** — How was this tested? What test cases were added?
- **Security impact** — Does this touch payment flows, auth, or sensitive data?
- **PCI impact** — Does this affect the cardholder data environment?
- **Checklist** — Required items before requesting review

### Review Assignment

PRs are automatically assigned reviewers based on CODEOWNERS rules. Do not manually assign reviewers unless instructed.

**Payment-critical paths** (vault, fraud, routing, charges) require:
- 1 review from a senior payment engineer
- 1 review from the security team
- QA sign-off

---

## Code Standards

### TypeScript

We use TypeScript strictly. All code must:

- Have explicit return types on all exported functions
- Pass `tsc --noEmit` with zero errors
- Use `strict: true` TypeScript configuration
- Avoid `any` types (use `unknown` and type guards)

```typescript
// ✅ Good
async function createCharge(params: ChargeCreateParams): Promise<Charge> {
  // ...
}

// ❌ Bad — missing types
async function createCharge(params) {
  // ...
}
```

### Error Handling

```typescript
// ✅ Good — specific error types, no sensitive data in messages
throw new PaymentProcessingError({
  code: 'PROCESSOR_DECLINED',
  message: 'Card was declined by the issuing bank',
  processorCode: result.declineCode,
  // Never include: card number, CVV, full name, etc.
});

// ❌ Bad — leaking sensitive context
throw new Error(`Card ${cardNumber} declined for customer ${customerEmail}`);
```

### Logging

```typescript
// ✅ Good — structured logging, no PII or card data
logger.info('charge.created', {
  chargeId: charge.id,
  amount: charge.amount,
  currency: charge.currency,
  processorName: 'stripe',
  durationMs: elapsed,
});

// ❌ Bad — PII and card data in logs
logger.info(`Created charge for ${customer.email} card ending ${card.last4} amount ${amount}`);
```

### No Secrets in Code

Use environment variables for all sensitive values. Our secrets scanner will block PRs containing:

- Patterns matching API keys (`sk_live_`, `lp_live_`, `AQE...`)
- Credit card numbers (Luhn-valid 15-16 digit sequences)
- Private key blocks (`-----BEGIN RSA PRIVATE KEY-----`)
- AWS access key patterns (`AKIA...`)
- Common password variable names with string values

---

## Testing Requirements

### Coverage Thresholds

| Scope | Minimum Coverage |
|-------|-----------------|
| Overall project | 95% |
| New code in PRs | 90% |
| Payment-critical paths (`/src/charges`, `/src/vault`) | 98% |
| Fraud detection | 95% |

### Test Types Required

**Unit tests** — All business logic, pure functions, transformations
```bash
npm run test:unit
```

**Integration tests** — API endpoints, database interactions, processor clients
```bash
npm run test:integration  # Uses sandbox environment
```

**Contract tests** — Processor API contracts (Pact-based)
```bash
npm run test:contracts
```

**Security tests** — Auth, authorization, input validation
```bash
npm run test:security
```

### Test Data Policy

- **Never use real card numbers** in tests — use the documented test card numbers
- **Never use real customer PII** — generate synthetic test data
- **Sandbox only** — integration tests must run against sandbox endpoints
- Use factories for test data: `import { createMockCharge } from '@/test/factories'`

---

## Security Considerations

Every contributor must keep these principles top of mind:

### Sensitive Data Handling

1. **Never log card data** — No PANs, CVVs, expiry dates in any log output
2. **Minimize PII exposure** — Only pass PII to systems that require it
3. **Use tokenization** — Reference cards by token, not by number
4. **Sanitize error messages** — Don't include user data in exception messages

### Input Validation

All external input must be validated at the API boundary:

```typescript
// Use Zod for runtime validation of all API inputs
const ChargeCreateSchema = z.object({
  amount: z.number().int().positive().max(999999999), // Max ~$10M
  currency: z.string().length(3).toLowerCase(),
  customer: z.string().startsWith('cus_'),
  // ...
});
```

### Dependency Management

- Run `npm audit` before submitting any PR
- Do not add dependencies with known High/Critical vulnerabilities
- Prefer established, actively maintained packages
- All new dependencies require a brief security review comment in the PR

---

## PCI DSS Development Guidelines

As a PCI DSS Level 1 service provider, all developers working on this codebase must follow these guidelines:

### Do

- Develop and test in the sandbox environment with test credentials only
- Use parameterized queries for all database operations
- Implement proper error handling that doesn't expose system internals
- Follow the principle of least privilege for all service accounts
- Document any changes to the cardholder data environment (CDE) scope

### Never Do

- Copy production cardholder data to development/test environments
- Disable or bypass security controls (WAF, rate limiting, auth)
- Hard-code credentials, encryption keys, or secrets
- Log sensitive authentication data (full card numbers, CVVs)
- Commit code that accesses the production CDE from development tooling

Violations of PCI DSS development standards are a serious compliance matter and will be escalated to the CISO.

---

## Review & Approval Process

### Standard Review (Non-Payment-Critical)

1. Open PR → CI runs automatically
2. CODEOWNERS automatically requested
3. 1 approving review required
4. Merge via "Squash and merge"

### Payment-Critical Review

Applies to changes in: `src/charges/`, `src/vault/`, `src/fraud/`, `src/routing/`, `src/auth/`

1. Open PR → CI + security scan runs
2. Senior payment engineer review (required)
3. Security team review (required)
4. QA testing in sandbox (required)
5. 2 approving reviews minimum
6. Merge via "Squash and merge" only

### Security Fixes

1. Report via private Security Advisory (see [SECURITY.md](SECURITY.md))
2. Internal branch created by security team
3. Expedited review with security engineer lead
4. Coordinated disclosure and release

---

## Release Process

Releases follow [Semantic Versioning](https://semver.org/):

- **PATCH** (2.4.x) — Bug fixes, security patches, minor improvements
- **MINOR** (2.x.0) — New features, backward compatible
- **MAJOR** (x.0.0) — Breaking changes (rare, requires migration guide)

The release process is managed by the LocalPulse engineering team. Contributors do not directly create releases.

---

## Questions?

- **General development** — `#payment-gateway` Slack channel
- **Security questions** — security@localpulse.pro
- **Access issues** — engineering@localpulse.pro
- **Schedule consultation** — [cal.com/ciprian-stefan-plesca](https://cal.com/ciprian-stefan-plesca)

---

*© 2026 Ciprian Stefan Plesca. All rights reserved. LocalPulse Payment Gateway™ is proprietary software.*
