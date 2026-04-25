# Changelog

All notable changes to the LocalPulse Payment Gateway™ are documented here.

This project adheres to [Semantic Versioning](https://semver.org/) and [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

> **Security releases** are marked with 🔒 and should be applied immediately.
> **Breaking changes** are marked with ⚠️ and require migration steps.

---

## [Unreleased]

### In Progress
- GraphQL API endpoint for complex billing queries
- Expanded ACH network support (additional bank integrations)
- Enhanced dunning analytics dashboard

---

## [2.4.1] — 2026-04-15

### 🔒 Security
- Upgraded TLS minimum version enforcement from 1.2 to 1.3 on all inbound connections from client SDKs (legacy TLS 1.2 temporarily retained only for processor egress connections)
- Patched timing side-channel in HMAC webhook signature verification — upgraded to constant-time comparison across all signature checks
- Updated `jsonwebtoken` to 9.0.2 addressing CVE-2022-23529 (dependency audit finding)

### Fixed
- Resolved race condition in subscription renewal processing that could result in duplicate charges for a small percentage of annual plan customers. Affected customers have been identified and refunds issued automatically. Reference: INC-2026-0041
- Fixed Adyen connector incorrectly mapping `REFUSED` response codes to retryable errors, causing unnecessary retry loops
- Corrected proration calculation when a subscription upgrade occurred within the first 24 hours of a billing cycle

### Performance
- Reduced BIN lookup latency by 38ms (p95) via Redis caching layer with 15-minute TTL
- Optimized subscription renewal batch job — reduced wall-clock time from 4.2 minutes to 47 seconds for 50,000-subscription batches

### Dependencies
- Stripe SDK: 14.12.0 → 14.14.1
- Adyen API library: 19.1.0 → 19.3.0

---

## [2.4.0] — 2026-03-01

### Added
- **Dispute Management API** — New `/v2/disputes` endpoints for programmatic chargeback management; includes evidence submission, status tracking, and outcome webhooks (`dispute.created`, `dispute.evidence_submitted`, `dispute.won`, `dispute.lost`)
- **Multi-currency settlement** — Merchants can now configure preferred settlement currency independently from transaction currency; FX conversion applied at daily fixing rate
- **Subscription pause/resume** — `POST /v2/subscriptions/:id/pause` and `POST /v2/subscriptions/:id/resume` with configurable pause duration and auto-resume scheduling
- **Payment link generation** — `POST /v2/payment-links` creates hosted payment pages for one-time charges; supports custom branding, expiry, and quantity limits
- **Expanded tax jurisdiction support** — Added tax calculation support for 12 additional EU VAT jurisdictions via Avalara integration
- **Webhook retry configuration** — Per-endpoint retry policies now configurable (delay, max attempts, backoff strategy) via dashboard and API

### ⚠️ Breaking Changes
- **Webhook payload v2 format** — Webhook events now use a unified envelope format. The `data.object` field replaces the previous flat payload. Migration guide: [docs.localpulse.pro/payment/migration/webhooks-v2](https://docs.localpulse.pro/payment/migration/webhooks-v2)
  - Old: `{ "type": "payment.succeeded", "charge_id": "ch_xxx", "amount": 4999 }`
  - New: `{ "type": "payment.succeeded", "data": { "object": { "id": "ch_xxx", "amount": 4999, ... } } }`
- **API key format** — New API keys issued from this version use `lp_live_` prefix (was `lpk_live_`). Existing keys remain valid but new keys will not work with SDKs < 2.3.0

### Changed
- Idempotency key requirement is now enforced strictly — previously a warning; now returns `400 Bad Request` if `LP-Idempotency-Key` header is missing on `POST` requests
- Default API timeout increased from 30s to 45s for bulk operations endpoints

### Fixed
- PayPal connector: Fixed incorrect currency conversion for IDR and JPY (zero-decimal currencies)
- Subscription trial-to-paid conversion webhook was occasionally fired twice for customers who updated their payment method during trial period
- Invoice PDF generation: Fixed encoding issue with non-Latin business names

---

## [2.3.2] — 2026-01-18

### 🔒 Security
- **Critical:** Patched authorization bypass in the `/v2/customers/:id/payment-methods` endpoint that could allow an authenticated merchant to enumerate payment methods of customers belonging to a different merchant account under specific multi-tenant configurations. CVSS 8.1 (High). All customers notified. No evidence of exploitation found. CVE-2026-10034

### Fixed
- Resolved intermittent 504 gateway timeouts on the `/v2/charges` endpoint under sustained load above 800 RPS
- Fixed Stripe connector failing to propagate `transfer_group` metadata for connected account transactions

---

## [2.3.1] — 2025-12-10

### Fixed
- Corrected VAT calculation for UK customers post-Brexit — was incorrectly applying EU VAT rules to GB-region billing addresses
- Fixed dashboard pagination for merchants with more than 10,000 customers
- Resolved `NaN` display issue in invoice PDF when line item descriptions contained special characters

### Performance
- Charge authorization p99 latency reduced from 380ms to 195ms following processor routing optimization

---

## [2.3.0] — 2025-11-01

### Added
- **3D Secure 2.0 (3DS2)** — Full SCA compliance for EU/EEA transactions per PSD2; configurable risk-based 3DS triggering
- **ACH Debit support** — Direct bank account debits for US customers; includes micro-deposit verification workflow
- **Usage-based billing** — `POST /v2/usage-records` for metered billing models; usage aggregated at billing cycle end
- **Smart retry dunning** — ML-powered retry scheduling for failed subscription payments; analyzes issuer patterns to optimize retry timing
- **Payout scheduling** — Configurable payout frequency (daily, weekly, custom); dashboard and API controls
- **Tax-exempt status** — Customer-level tax exemption with certificate storage and audit trail

### Changed
- Fraud scoring model retrained on Q3 2025 transaction data; false positive rate improved by 23%
- Rate limits increased for Enterprise tier: 10,000 → 25,000 requests/hour

### Deprecated
- `/v1/` API endpoints — Sunset date: **2026-06-01**. Migration guide available at [docs.localpulse.pro/payment/migration/v1-to-v2](https://docs.localpulse.pro/payment/migration/v1-to-v2)

---

## [2.2.0] — 2025-08-15

### Added
- **Multi-processor failover** — Automatic failover to secondary processor on primary decline or timeout
- **PO / Net terms billing** — Enterprise customers can pay by purchase order with Net-30/60/90 terms
- **Branded invoice customization** — Logo, color scheme, and custom fields via dashboard
- **Webhook delivery logs** — Full delivery history, request/response inspection via dashboard

### 🔒 Security
- Implemented Content Security Policy (CSP) headers on merchant dashboard
- Upgraded password hashing from bcrypt (12 rounds) to Argon2id

---

## [2.1.0] — 2025-05-01

### Added
- Initial Adyen connector (Europe and APAC optimized routing)
- Subscription management API (create, update, cancel, pause)
- Customer portal for self-service payment method management

---

## [2.0.0] — 2025-01-15

### Initial Release

- Core payment processing (charges, refunds, captures)
- Stripe connector
- PayPal connector
- Basic subscription billing
- Webhook infrastructure
- PCI DSS Level 1 certification achieved
- SOC 2 Type II audit completed

---

## Version Support Policy

| Version | Status | Security Patches Until |
|---------|--------|------------------------|
| 2.4.x | ✅ Current | TBD |
| 2.3.x | ✅ Supported | 2026-12-31 |
| 2.2.x | ⚠️ Critical only | 2026-06-30 |
| 2.1.x | ❌ End of life | Expired |
| 2.0.x | ❌ End of life | Expired |

---

[Unreleased]: https://github.com/localpulse-pro/payment-gateway/compare/v2.4.1...HEAD
[2.4.1]: https://github.com/localpulse-pro/payment-gateway/compare/v2.4.0...v2.4.1
[2.4.0]: https://github.com/localpulse-pro/payment-gateway/compare/v2.3.2...v2.4.0
[2.3.2]: https://github.com/localpulse-pro/payment-gateway/compare/v2.3.1...v2.3.2
[2.3.1]: https://github.com/localpulse-pro/payment-gateway/compare/v2.3.0...v2.3.1
[2.3.0]: https://github.com/localpulse-pro/payment-gateway/compare/v2.2.0...v2.3.0
[2.2.0]: https://github.com/localpulse-pro/payment-gateway/compare/v2.1.0...v2.2.0
[2.1.0]: https://github.com/localpulse-pro/payment-gateway/compare/v2.0.0...v2.1.0
[2.0.0]: https://github.com/localpulse-pro/payment-gateway/releases/tag/v2.0.0

---

*© 2026 Ciprian Stefan Plesca · LocalPulse Payment Gateway™*
