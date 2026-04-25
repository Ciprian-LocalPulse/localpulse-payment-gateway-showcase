# Security Policy

## LocalPulse Payment Gateway™ Security

Security is the foundation of everything we build at LocalPulse.pro™. The Payment Gateway handles financial transactions for thousands of enterprise customers, and we hold ourselves to the highest standards of security engineering, disclosure management, and incident response.

---

## Supported Versions

The following versions of the LocalPulse Payment Gateway receive active security patches:

| Version | Support Status       | End of Security Support |
|---------|----------------------|-------------------------|
| 2.4.x   | ✅ Active (current)  | TBD                     |
| 2.3.x   | ✅ Security patches  | 2026-12-31              |
| 2.2.x   | ⚠️ Critical only     | 2026-06-30              |
| 2.1.x   | ❌ End of life       | 2025-12-31              |
| < 2.1   | ❌ End of life       | Expired                 |

We strongly recommend all customers run the latest stable release within the `2.4.x` branch.

---

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub issues, pull requests, discussion threads, or social media.**

### Preferred Method: Security Advisory

Use GitHub's private [Security Advisory](../../security/advisories/new) feature to report vulnerabilities confidentially. This is our preferred channel.

### Alternative: Encrypted Email

If you cannot use GitHub Security Advisories, email us at:

**security@localpulse.pro**

Encrypt sensitive reports using our PGP key:

```
Key ID:       0xA1B2C3D4E5F6A7B8
Fingerprint:  A1B2 C3D4 E5F6 A7B8 9C0D 1E2F 3A4B 5C6D 7E8F 9A0B
Download:     https://localpulse.pro/.well-known/pgp-key.asc
```

### Responsible Disclosure Program

We operate a responsible disclosure program. We kindly ask that you:

1. **Give us reasonable time** — Allow at least 90 days for us to investigate and deploy a fix before any public disclosure
2. **Avoid accessing customer data** — Do not read, modify, or exfiltrate any real customer payment or PII data during research
3. **Do not disrupt services** — Avoid denial-of-service testing or any action that degrades service for our customers
4. **Test only in sandbox** — Conduct all research in the sandbox environment (`payments-sandbox.localpulse.pro`)
5. **Report in good faith** — Provide enough detail for us to reproduce and validate the issue

In return, we commit to:

- Acknowledging your report within **2 business days**
- Providing an initial severity assessment within **5 business days**
- Keeping you informed throughout the remediation process
- Crediting you publicly (if you wish) upon fix release
- Not pursuing legal action against good-faith researchers

---

## Bug Bounty Program

LocalPulse.pro™ runs a private bug bounty program for security researchers. Rewards are based on severity and impact:

| CVSS Score | Severity | Reward Range |
|------------|----------|--------------|
| 9.0 – 10.0 | Critical | $5,000 – $15,000 |
| 7.0 – 8.9  | High     | $1,500 – $5,000  |
| 4.0 – 6.9  | Medium   | $250 – $1,500    |
| 0.1 – 3.9  | Low / Info | $50 – $250     |

To join the private program, contact **security@localpulse.pro** with:
- Your research background and prior CVEs (if any)
- Your preferred contact method
- Confirmation that you have read and accept these guidelines

### In-Scope Targets

- `payments.localpulse.pro` — Production payment API
- `payments-sandbox.localpulse.pro` — Sandbox environment (preferred)
- `app.localpulse.pro` — Dashboard and merchant portal
- `@localpulse/payment-gateway` NPM package
- `localpulse-payments` PyPI package

### Out-of-Scope

- Third-party processor infrastructure (Stripe, Adyen, PayPal)
- Denial of service or volumetric attacks
- Physical security attacks
- Social engineering of LocalPulse employees
- Issues in unsupported versions (< 2.2.x)
- Self-XSS without a realistic attack vector
- Rate limit bypasses that require authenticated access to trigger
- Missing security headers with no demonstrated exploitability
- Issues requiring physical access to a victim's device

---

## Security Architecture

### PCI DSS Level 1 Compliance

The LocalPulse Payment Gateway™ is a **PCI DSS Level 1** certified service provider — the highest certification level for payment card processing. Our annual Report on Compliance (ROC) is conducted by a Qualified Security Assessor (QSA).

Key controls include:

- **No raw PANs stored** — All card data is immediately tokenized on ingress
- **Isolated cardholder data environment (CDE)** — Network-segmented from all other systems
- **Point-to-point encryption (P2PE)** — Card data encrypted from capture to processor
- **Quarterly ASV scans** — Approved Scanning Vendor external vulnerability scans
- **Annual penetration tests** — By independent third-party security firm

### Tokenization & Vaulting

Raw card numbers (PANs) never touch LocalPulse servers. Our tokenization flow:

```
Customer Card Input
       ↓
  [TLS 1.3 Encrypted]
       ↓
  LocalPulse Vault (isolated CDE)
       ↓  
  Token generated (lp_tok_xxxx)
       ↓
  Processor-specific token created
       ↓
  Application layer receives only tokens
```

Vault encryption uses **AES-256-GCM** with keys managed via HSM (Hardware Security Module). Key rotation occurs every 90 days.

### Authentication & Authorization

- **API Keys** — HMAC-SHA256 signed, scoped to specific permissions
- **Webhook signatures** — HMAC-SHA256 with timestamp anti-replay protection
- **Dashboard** — SAML 2.0 / OIDC SSO + TOTP MFA enforcement
- **Internal access** — Zero-trust network with mutual TLS (mTLS) between services
- **Service accounts** — Short-lived credentials via Vault's dynamic secrets engine

### Fraud Detection

Real-time ML-based fraud scoring on every transaction:

- Behavioral velocity checks (card, device, IP, customer)
- Device fingerprinting and browser signals
- Geographic anomaly detection
- BIN-level risk assessment
- 3D Secure 2.0 triggered automatically above configurable risk threshold
- Manual review queue for high-risk transactions

### Encryption Standards

| Data State | Standard |
|------------|----------|
| In transit | TLS 1.3 minimum; TLS 1.2 for legacy processors |
| At rest (card data) | AES-256-GCM via HSM |
| At rest (PII) | AES-256-CBC with envelope encryption |
| Backups | AES-256 with separate key hierarchy |
| API keys | PBKDF2-SHA256 with 600,000 iterations |

### Penetration Testing

We engage independent security firms for:

- **Annual full-scope penetration test** — Network, application, and social engineering
- **Quarterly application security review** — OWASP Top 10 focused
- **Continuous automated scanning** — DAST integrated into CI/CD pipeline
- **Red team exercises** — Annual adversarial simulation

Penetration test summaries are available to enterprise customers under NDA.

---

## Incident Response

### Severity Definitions

| Severity | Definition | Response Time |
|----------|------------|---------------|
| P0 — Critical | Active breach, data exfiltration, or service compromise | Immediate (24/7) |
| P1 — High | Potential breach, privilege escalation, auth bypass | 2 hours |
| P2 — Medium | Vulnerability with limited exploitability or impact | 24 hours |
| P3 — Low | Hardening opportunity or informational finding | 5 business days |

### Notification Commitments

In the event of a security incident affecting customer data:

- **Initial notification** within 72 hours of confirmed breach (GDPR requirement)
- **Detailed incident report** within 14 days
- **Post-mortem and remediation summary** within 30 days

Enterprise customers with active support agreements receive direct communication from our security team.

---

## Security Contact

| Channel | Contact |
|---------|---------|
| Security disclosures | security@localpulse.pro |
| Incident response (enterprise) | incident-response@localpulse.pro |
| PGP key | https://localpulse.pro/.well-known/pgp-key.asc |
| Bug bounty | security@localpulse.pro (subject: Bug Bounty) |
| Schedule consultation | https://cal.com/ciprian-stefan-plesca |

---

*Last reviewed: April 2026 — Security Team, LocalPulse.pro™*

*© 2026 Ciprian Stefan Plesca. All rights reserved.*
