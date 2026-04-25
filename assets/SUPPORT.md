# Support

## LocalPulse Payment Gateway™ Support

This document describes the support resources, channels, and SLA commitments for the LocalPulse Payment Gateway™.

---

## Support Channels

Choose the right channel for your issue type:

| Issue Type | Channel | Response Time |
|------------|---------|---------------|
| 🔴 Payment processing outage | support@localpulse.pro + phone | 15 min (Enterprise) / 1 hr (Premium) |
| 🔴 Security incident | security@localpulse.pro | Immediate |
| 🟠 Integration bug | support.localpulse.pro portal | Per SLA tier |
| 🟠 API error | support.localpulse.pro portal | Per SLA tier |
| 🟡 Billing question | billing@localpulse.pro | 1 business day |
| 🟡 Documentation | docs.localpulse.pro | Self-serve |
| 🟢 Feature request | portal or Slack (Enterprise) | Triaged monthly |
| 🟢 General question | docs.localpulse.pro | Self-serve |

### Do NOT Use GitHub Issues For

- Payment processing failures (use the support portal)
- Security vulnerabilities (see [SECURITY.md](SECURITY.md))
- Billing disputes (use billing@localpulse.pro)
- Production incidents (use support phone line)
- Feature requests (use the support portal)

GitHub Issues in this repository are reserved for pre-approved internal bug tracking only.

---

## Support Tiers

### Standard Support

- **Availability:** Monday – Friday, 09:00–18:00 CET
- **Channels:** Support portal, email
- **Critical (P0) response:** 4 hours
- **High (P1) response:** 1 business day
- **Medium (P2) response:** 3 business days
- **Low (P3) response:** 5 business days

### Premium Support

- **Availability:** 24/7/365
- **Channels:** Support portal, email, phone
- **Critical (P0) response:** 1 hour
- **High (P1) response:** 4 hours
- **Medium (P2) response:** 1 business day
- **Low (P3) response:** 3 business days

### Enterprise Support

- **Availability:** 24/7/365 with dedicated support team
- **Channels:** All channels + dedicated Slack workspace
- **Critical (P0) response:** 15 minutes (named technical contact)
- **High (P1) response:** 1 hour
- **Medium (P2) response:** 4 hours
- **Low (P3) response:** 1 business day
- **Dedicated CSM:** Quarterly business reviews, proactive monitoring
- **Escalation path:** Direct line to senior engineering

---

## Severity Definitions

| Severity | Definition | Examples |
|----------|------------|---------|
| **P0 — Critical** | Complete service outage or data breach affecting payments | All charges failing; authorization endpoint down; suspected breach |
| **P1 — High** | Significant degradation affecting core payment functionality | Elevated error rates; webhook delivery failures; subscription billing errors |
| **P2 — Medium** | Partial functionality impaired; workaround available | Specific currency failing; dashboard not loading; report generation errors |
| **P3 — Low** | Minor issue or general question; no immediate business impact | Documentation question; feature inquiry; cosmetic UI issue |

---

## Self-Service Resources

Resolve common issues faster with our self-service resources:

### Documentation

- **[API Reference](https://docs.localpulse.pro/payment/api)** — Complete endpoint documentation
- **[Integration Guides](https://docs.localpulse.pro/payment/guides)** — Step-by-step integration tutorials
- **[Webhooks Guide](https://docs.localpulse.pro/payment/webhooks)** — Event handling and signature verification
- **[Error Codes](https://docs.localpulse.pro/payment/errors)** — Complete error code reference
- **[Testing Guide](https://docs.localpulse.pro/payment/testing)** — Sandbox setup and test card numbers
- **[Migration Guides](https://docs.localpulse.pro/payment/migration)** — Version upgrade instructions

### Tools

- **[Developer Portal](https://developers.localpulse.pro)** — API explorer, sandbox console
- **[Status Page](https://status.localpulse.pro)** — Real-time system status and incident history
- **[Webhook Inspector](https://developers.localpulse.pro/webhooks)** — Test and debug webhook delivery
- **[Log Explorer](https://app.localpulse.pro/logs)** — Search and analyze transaction logs

### Community

- **[Knowledge Base](https://support.localpulse.pro/kb)** — Searchable help articles and FAQs
- **[Changelog](CHANGELOG.md)** — Release notes and version history

---

## Filing a Support Request

### Required Information

To help us resolve your issue as quickly as possible, please include:

**For payment processing issues:**
- Your API key prefix (first 8 characters only — never share the full key)
- Charge ID, customer ID, or subscription ID
- Timestamp of the issue (with timezone)
- Full error response or error code received
- Steps to reproduce
- Impact: number of transactions affected, affected customer segments

**For integration issues:**
- SDK version and language/runtime
- Relevant code snippet (sanitized — no secrets)
- Full error message and stack trace
- Request ID from the `LP-Request-Id` response header
- Environment: sandbox or production

**For billing questions:**
- Account email address
- Invoice number (if applicable)
- Description of the discrepancy

### How to File

1. **Portal (preferred):** [support.localpulse.pro/new](https://support.localpulse.pro/new)
2. **Email:** support@localpulse.pro (include all required information above)
3. **Phone (P0 only):** Available on your dashboard under Settings → Support

---

## Escalation Process

If you feel your issue is not being addressed with appropriate urgency:

1. **Reply to your existing ticket** and mark it for escalation
2. **Enterprise customers:** Contact your dedicated Customer Success Manager directly
3. **Final escalation:** engineering@localpulse.pro (for unresolved P0/P1 only)

---

## Planned Maintenance

Scheduled maintenance windows are posted on the [Status Page](https://status.localpulse.pro) at least 72 hours in advance. Enterprise customers receive direct email notification 1 week ahead.

Maintenance is typically scheduled for:
- **Sundays 02:00–04:00 CET** (low-traffic window, Europe)
- **Sundays 08:00–10:00 CET** (low-traffic window, Americas)

Emergency maintenance may occur with shorter notice during critical security patches.

---

## Contact Directory

| Purpose | Contact |
|---------|---------|
| Technical support | support@localpulse.pro |
| Security incident | security@localpulse.pro |
| Billing & invoicing | billing@localpulse.pro |
| Enterprise partnerships | partnerships@localpulse.pro |
| Legal & compliance | legal@localpulse.pro |
| Schedule consultation | https://cal.com/ciprian-stefan-plesca |
| Status page | https://status.localpulse.pro |
| Support portal | https://support.localpulse.pro |

---

*© 2026 Ciprian Stefan Plesca · LocalPulse Payment Gateway™ · All Rights Reserved*
