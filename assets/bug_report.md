---
name: Bug Report
about: Report a bug in the LocalPulse Payment Gateway™
title: '[BUG] '
labels: ['bug', 'triage-needed']
assignees: ''
---

> ⚠️ **STOP — Read before submitting**
>
> - **Security vulnerabilities:** Do NOT file here. See [SECURITY.md](../../SECURITY.md) and email security@localpulse.pro
> - **Production payment outages:** Contact support immediately at support@localpulse.pro or call the emergency line (see your dashboard)
> - **Billing disputes:** Email billing@localpulse.pro
>
> This issue tracker is for internal engineering bugs only. Unauthorized access to this repository is prohibited.

---

## Bug Description

<!-- A clear and concise description of what the bug is -->

## Environment

| Field | Value |
|-------|-------|
| Gateway version | <!-- e.g., 2.4.1 --> |
| SDK and version | <!-- e.g., @localpulse/payment-gateway 2.4.1 --> |
| Language / runtime | <!-- e.g., Node.js 20.10 / Python 3.11 --> |
| Environment | <!-- sandbox / production --> |
| Affected endpoint | <!-- e.g., POST /v2/charges --> |

## Steps to Reproduce

1.
2.
3.

## Expected Behavior

<!-- What should happen? -->

## Actual Behavior

<!-- What actually happens? -->

## Error Details

<!-- Include the full error response. REMOVE any API keys, card data, or PII before pasting -->

```json
{
  "error": {
    "code": "",
    "message": "",
    "request_id": ""
  }
}
```

**LP-Request-Id header value:** <!-- Helps us trace the specific request -->

## Logs / Stack Trace

<!-- Paste relevant log output. REMOVE any sensitive data -->

```
[paste logs here]
```

## Impact

- **Severity:** [ ] Critical (complete failure) / [ ] High (major degradation) / [ ] Medium (workaround exists) / [ ] Low
- **Frequency:** [ ] Always / [ ] Intermittent (___% of requests) / [ ] Once
- **Affected transactions:** <!-- Approximate number or scope -->
- **Is there a workaround?** <!-- Yes/No; if yes, describe -->

## Additional Context

<!-- Any other context, related issues, or recent changes that might be relevant -->

---

**⚠️ Reminder:** Do not include API keys, card numbers, CVVs, customer PII, or any sensitive data in this issue.
