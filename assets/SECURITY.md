# Security Policy

> **LocalPulse Payment Gateway — Enterprise Edition**  
> Authored & Maintained by **Ciprian Stefan Plesca** — Cybersecurity Expert & Enterprise Architect  
> [localpulse.pro](https://localpulse.pro) · [contact@localpulse.pro](mailto:contact@localpulse.pro)  
> Classification: **CONFIDENTIAL — ENTERPRISE**

---

## ⚠️ Critical Notice — Payment Security

LocalPulse Payment Gateway processes financial transactions and handles sensitive cardholder data. Security vulnerabilities in payment systems can result in direct financial harm, regulatory penalties, and loss of PCI DSS certification. **We treat all security reports with the utmost urgency and seriousness.**

**If you believe you have discovered a vulnerability, do not attempt to exploit it or access any payment data under any circumstances. Report it immediately through the private channels below.**

---

## Table of Contents

- [Supported Versions](#supported-versions)
- [Reporting a Vulnerability](#reporting-a-vulnerability)
- [Security Response SLA](#security-response-sla)
- [Responsible Disclosure Policy](#responsible-disclosure-policy)
- [Security Architecture](#security-architecture)
- [PCI DSS Compliance](#pci-dss-compliance)
- [Out-of-Scope Items](#out-of-scope-items)
- [Bug Bounty Program](#bug-bounty-program)

---

## Supported Versions

| Version | Status | Security Patches | PCI DSS Certified | End of Support |
|---------|--------|------------------|-------------------|----------------|
| 2.x     | ✅ **ACTIVE** | Full support | ✅ Yes | TBD |
| 1.5.x   | ⚠️ Critical only | Critical CVEs only | ⚠️ Expired | 2025-06-30 |
| 1.x     | ❌ End of Life | None | ❌ No | 2024-12-31 |

> Only version 2.x is eligible for PCI DSS-compliant deployment. Deploying unsupported versions in a payment environment is a compliance violation.

---

## Reporting a Vulnerability

**Never open a public GitHub issue for security vulnerabilities.** Payment system vulnerabilities require immediate private handling.

### Primary Reporting Channel

| Channel | Details |
|---------|---------|
| **Security Email** | [contact@localpulse.pro](mailto:contact@localpulse.pro) |
| **Subject Line** | `[SECURITY] <severity> — <brief description>` |
| **Response Time** | See SLA table below |
| **Encryption** | PGP key available on request |
| **Enterprise Consultation** | [cal.com/ciprian-stefan-plesca](https://cal.com/ciprian-stefan-plesca) |

### What to Include

```
1. Vulnerability classification (CWE / OWASP category if known)
2. Affected component (UI layer, API endpoint, integration)
3. Severity self-assessment (Critical / High / Medium / Low)
4. Step-by-step reproduction instructions
5. Proof-of-concept (code, screenshots, HTTP traces)
6. Data or systems potentially exposed
7. Recommended remediation
8. Whether live payment data was accessed (critical — disclose immediately)
9. Your contact information for follow-up
```

---

## Security Response SLA

| Severity | Criteria | Initial Response | Patch Target | Disclosure |
|----------|----------|-----------------|--------------|------------|
| **P0 — Emergency** | Live payment data exposed; active exploitation in production | **30 minutes** | 4 hours | Post-remediation only |
| **P1 — Critical** | Authentication bypass; cardholder data exposure; RCE | **1 hour** | 24 hours | 30 days post-patch |
| **P2 — High** | Privilege escalation; payment manipulation; PII leak | **4 hours** | 72 hours | 60 days post-patch |
| **P3 — Medium** | Logic flaw; insecure config; data leakage | **24 hours** | 14 days | 90 days post-patch |
| **P4 — Low** | Best-practice deviation; informational | **72 hours** | 30 days | Coordinated |

> P0 and P1 incidents trigger automatic notification to our PCI DSS QSA (Qualified Security Assessor) and legal counsel.

---

## Responsible Disclosure Policy

We operate a **coordinated disclosure** model with the following commitments:

**We commit to:**
- Acknowledging your report within the SLA above — guaranteed.
- Keeping you informed of validation, remediation progress, and deployment status.
- Not pursuing legal action against good-faith researchers following this policy.
- Publicly crediting researchers who disclose responsibly (with their consent).
- Processing any applicable bug bounty reward within 30 days of fix deployment.

**We ask researchers to:**
- Report through the private channel above — never publicly before a fix is deployed.
- **Never access, modify, or exfiltrate any payment or cardholder data** beyond what is strictly necessary to demonstrate the vulnerability. Any unauthorized access to financial data may trigger legal obligations regardless of research intent.
- Not attempt to execute transactions, initiate refunds, or manipulate payment flows in any production environment.
- Allow the full response SLA period before escalating or disclosing.
- Comply with all applicable laws in your jurisdiction.

---

## Security Architecture

### Cardholder Data Environment (CDE)

The CDE is governed by PCI DSS Level 1 requirements and is entirely separate from this repository. This repository contains **only the UI / presentation layer** and does not include any code that processes, stores, or transmits cardholder data.

### Core Security Controls

| Control Domain | Implementation |
|----------------|---------------|
| **Encryption at Rest** | AES-256 for all sensitive data; HSM-managed keys |
| **Encryption in Transit** | TLS 1.3 exclusively; TLS 1.2 and below rejected |
| **Authentication** | OAuth 2.0 · API key + HMAC · mTLS for service-to-service |
| **Tokenization** | PAN tokenization — card numbers never stored in application layer |
| **Secrets Management** | HashiCorp Vault · AWS KMS — no hardcoded credentials, ever |
| **WAF** | Cloudflare Enterprise WAF with custom payment-specific rulesets |
| **DDoS Protection** | Multi-layer, volumetric + application-layer protection |
| **Fraud Detection** | Real-time ML scoring — TensorFlow · Scikit-learn · <10ms inference |
| **Audit Logging** | Immutable audit trail for all transactions, API calls, and admin actions |
| **Access Control** | Zero-trust, least-privilege RBAC; MFA enforced for all personnel |
| **Penetration Testing** | Quarterly external pen tests by approved PCI DSS QSA vendors |
| **SIEM** | Real-time anomaly detection with PagerDuty alerting |

### Webhook Security

All outbound webhooks are secured with:
- HMAC-SHA256 payload signature (`X-LocalPulse-Signature-256` header)
- Timestamp included in signature to prevent replay attacks
- TLS 1.3 minimum on delivery endpoint
- Idempotency keys to prevent duplicate processing
- Retry with exponential back-off and dead letter queue

---

## PCI DSS Compliance

LocalPulse Payment Gateway maintains **PCI DSS Level 1** certification — the highest tier, required for processors handling over 6 million transactions annually.

| PCI DSS Requirement | Status | Notes |
|---------------------|--------|-------|
| Req 1–2: Network Security | ✅ Certified | Segmented CDE, WAF enforced |
| Req 3–4: Data Protection | ✅ Certified | Tokenization, AES-256, TLS 1.3 |
| Req 5–6: Vulnerability Management | ✅ Certified | Quarterly scans, patch SLAs |
| Req 7–8: Access Control | ✅ Certified | RBAC, MFA, PAM |
| Req 9: Physical Security | ✅ Certified | SOC 2 Type II data centers |
| Req 10: Logging & Monitoring | ✅ Certified | Immutable audit logs, SIEM |
| Req 11: Security Testing | ✅ Certified | Quarterly pen tests, ASV scans |
| Req 12: Policy Management | ✅ Certified | Annual QSA audit cycle |

> Report on Compliance (RoC) and Attestation of Compliance (AoC) available to enterprise partners under NDA. Contact [contact@localpulse.pro](mailto:contact@localpulse.pro).

---

## Out-of-Scope Items

The following are **explicitly excluded** from security review scope:

- Social engineering, phishing, or physical attacks against personnel.
- Denial-of-service or stress-testing attacks against any endpoint.
- Attacks requiring man-in-the-middle interception of third-party payment networks (Visa, Mastercard, SWIFT).
- Vulnerabilities in third-party processors or banking partners outside our control.
- Rate limiting bypass without demonstrated financial impact.
- Missing security headers on non-sensitive, public-facing static content only.
- Findings from automated scanners submitted without a verified, manual proof-of-concept.
- Versions designated End of Life (see Supported Versions table).
- Testing conducted in production environments without prior written authorization.

---

## Bug Bounty Program

LocalPulse operates a private bug bounty program for authorized security researchers.

| Severity | Reward Range |
|----------|-------------|
| P0 — Emergency | €5,000 – €25,000 |
| P1 — Critical | €2,000 – €10,000 |
| P2 — High | €500 – €2,000 |
| P3 — Medium | €100 – €500 |
| P4 — Low | Recognition + swag |

> To apply for bug bounty authorization, contact [contact@localpulse.pro](mailto:contact@localpulse.pro) with your security research background. Unauthorized testing is not eligible for rewards and may violate applicable law.

---

## Hall of Acknowledgement

We thank the following security researchers for responsible disclosure:

*No entries yet — be the first.*

---

*© 2026 Ciprian Stefan Plesca · LocalPulse Payment Gateway · All Rights Reserved*  
*PCI DSS Level 1 · SOC 2 Type II · ISO 27001*
