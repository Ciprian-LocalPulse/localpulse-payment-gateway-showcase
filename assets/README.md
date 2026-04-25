# LocalPulse Payment Gateway™

<p align="center">
  <img src="https://img.shields.io/badge/version-2.4.1-blue?style=for-the-badge" alt="Version" />
  <img src="https://img.shields.io/badge/license-Proprietary-red?style=for-the-badge" alt="License" />
  <img src="https://img.shields.io/badge/PCI%20DSS-Level%201-green?style=for-the-badge" alt="PCI DSS" />
  <img src="https://img.shields.io/badge/coverage-97%25-brightgreen?style=for-the-badge" alt="Coverage" />
  <img src="https://img.shields.io/badge/uptime-99.99%25-brightgreen?style=for-the-badge" alt="Uptime" />
</p>

<p align="center">
  <strong>Enterprise-grade payment processing infrastructure for the LocalPulse.pro™ Intelligence Platform</strong><br/>
  Secure · Scalable · PCI DSS Level 1 Certified
</p>

---

## Overview

The **LocalPulse Payment Gateway™** is the financial transaction backbone of the LocalPulse.pro™ Enterprise Intelligence Platform. It provides a unified, secure, and highly available payment processing layer supporting multi-currency transactions, subscription billing, usage-based invoicing, and enterprise procurement workflows.

Built to handle the demands of Fortune 500 clients and high-frequency API billing, the gateway processes millions of transactions per month with sub-200ms authorization times and 99.99% SLA uptime.

---

## Table of Contents

- [Architecture](#architecture)
- [Features](#features)
- [Quick Start](#quick-start)
- [Integration Guide](#integration-guide)
- [API Reference](#api-reference)
- [Security](#security)
- [Compliance](#compliance)
- [Configuration](#configuration)
- [Testing](#testing)
- [Deployment](#deployment)
- [Support](#support)

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    LocalPulse Payment Gateway™                  │
├──────────────┬──────────────┬──────────────┬────────────────────┤
│  API Gateway │  Auth Layer  │  Rate Limiter│  Fraud Detection   │
├──────────────┴──────────────┴──────────────┴────────────────────┤
│                     Transaction Orchestrator                     │
├────────────┬─────────────┬──────────────┬───────────────────────┤
│  Processor │ Subscription │   Invoicing  │   Reconciliation     │
│  Routing   │   Engine    │    Engine    │      Engine          │
├────────────┴─────────────┴──────────────┴───────────────────────┤
│                      Vault & Tokenization                        │
├────────────┬─────────────┬──────────────┬───────────────────────┤
│  Stripe    │  Adyen      │   PayPal     │   Wire / ACH         │
│  Connector │  Connector  │   Connector  │   Connector          │
└────────────┴─────────────┴──────────────┴───────────────────────┘
```

The gateway follows a **processor-agnostic** architecture with intelligent routing, enabling failover across payment processors and cost optimization through smart routing rules.

---

## Features

### Core Payment Processing
- **Multi-processor routing** — Stripe, Adyen, PayPal, Braintree, and custom PSPs
- **Smart failover** — Automatic retry with alternate processors on decline
- **Multi-currency** — 135+ currencies with real-time FX conversion
- **Authorization holds** — Pre-authorization and capture workflows
- **Partial captures and refunds** — Granular transaction control

### Subscription & Recurring Billing
- **Flexible billing cycles** — Monthly, annual, quarterly, usage-based
- **Proration** — Automatic proration on plan upgrades/downgrades
- **Dunning management** — Configurable retry logic with smart scheduling
- **Trial period management** — Free trials with automatic conversion
- **Coupon & discount engine** — Percentage, fixed, and time-limited discounts

### Enterprise Features
- **Purchase order (PO) support** — Net-30/60/90 enterprise billing
- **ACH / Wire transfers** — Direct bank payment acceptance
- **Tax calculation** — Automated sales tax with Avalara integration
- **Multi-entity billing** — Parent/child account hierarchies
- **Custom invoicing** — Branded PDF invoices with line-item detail

### Security & Compliance
- **PCI DSS Level 1** certified infrastructure
- **Card tokenization** — Vault-based storage, zero raw PAN on servers
- **3D Secure 2.0** — Strong customer authentication (SCA) ready
- **Fraud scoring** — ML-based transaction risk assessment
- **Chargeback automation** — Evidence collection and dispute management

---

## Quick Start

### Prerequisites

- Node.js ≥ 18.0 or Python ≥ 3.10
- Valid LocalPulse API credentials (see [Account Setup](https://docs.localpulse.pro/payment/setup))
- TLS 1.2+ for all webhook endpoints

### Installation

**Node.js / TypeScript**
```bash
npm install @localpulse/payment-gateway
```

**Python**
```bash
pip install localpulse-payments
```

### Initialize the Client

```typescript
import { LocalPulsePayments } from '@localpulse/payment-gateway';

const gateway = new LocalPulsePayments({
  apiKey: process.env.LP_PAYMENT_API_KEY,
  environment: 'production', // or 'sandbox'
  webhookSecret: process.env.LP_WEBHOOK_SECRET,
});
```

### Process Your First Payment

```typescript
const charge = await gateway.charges.create({
  amount: 4999,          // Amount in smallest currency unit (cents)
  currency: 'usd',
  customer: 'cus_abc123',
  description: 'LocalPulse Professional — Monthly',
  metadata: {
    plan: 'professional',
    billing_period: '2026-04',
  },
});

console.log(charge.id);       // ch_1234567890
console.log(charge.status);   // succeeded
```

---

## Integration Guide

### Webhook Configuration

All payment events are delivered via signed webhooks. Configure your endpoint in the [LocalPulse Dashboard](https://app.localpulse.pro/settings/webhooks).

```typescript
import express from 'express';

const app = express();

app.post('/webhooks/localpulse', express.raw({ type: 'application/json' }), (req, res) => {
  const signature = req.headers['lp-signature'] as string;

  let event;
  try {
    event = gateway.webhooks.constructEvent(req.body, signature);
  } catch (err) {
    return res.status(400).send(`Webhook signature verification failed.`);
  }

  switch (event.type) {
    case 'payment.succeeded':
      await handleSuccessfulPayment(event.data);
      break;
    case 'payment.failed':
      await handleFailedPayment(event.data);
      break;
    case 'subscription.renewed':
      await handleSubscriptionRenewal(event.data);
      break;
    case 'invoice.payment_failed':
      await handleInvoiceFailure(event.data);
      break;
  }

  res.json({ received: true });
});
```

### Subscription Management

```typescript
// Create a subscription
const subscription = await gateway.subscriptions.create({
  customer: 'cus_abc123',
  plan: 'plan_professional_monthly',
  trial_end: Math.floor(Date.now() / 1000) + (14 * 24 * 60 * 60), // 14-day trial
  payment_method: 'pm_xyz789',
});

// Upgrade a subscription
await gateway.subscriptions.update(subscription.id, {
  plan: 'plan_enterprise_annual',
  proration_behavior: 'create_prorations',
});

// Cancel at period end
await gateway.subscriptions.cancel(subscription.id, {
  at_period_end: true,
});
```

---

## API Reference

Base URL: `https://payments.localpulse.pro/v2`

All requests must include:
```
Authorization: Bearer <API_KEY>
Content-Type: application/json
LP-Idempotency-Key: <uuid>   (required for POST requests)
```

### Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/charges` | Create a one-time charge |
| `GET` | `/charges/:id` | Retrieve a charge |
| `POST` | `/charges/:id/refund` | Issue a full or partial refund |
| `POST` | `/customers` | Create a customer record |
| `GET` | `/customers/:id` | Retrieve customer details |
| `POST` | `/subscriptions` | Create a subscription |
| `PATCH` | `/subscriptions/:id` | Update a subscription |
| `DELETE` | `/subscriptions/:id` | Cancel a subscription |
| `GET` | `/invoices` | List invoices |
| `GET` | `/invoices/:id` | Retrieve an invoice |
| `POST` | `/payment-methods` | Attach a payment method |
| `GET` | `/payment-methods/:id` | Retrieve a payment method |
| `DELETE` | `/payment-methods/:id` | Detach a payment method |
| `POST` | `/disputes/:id/evidence` | Submit dispute evidence |
| `GET` | `/balance` | Retrieve current balance |
| `GET` | `/payouts` | List payouts |

Full API documentation: [docs.localpulse.pro/payment/api](https://docs.localpulse.pro/payment/api)

---

## Security

Security is the highest priority for the LocalPulse Payment Gateway™. Please review our [SECURITY.md](SECURITY.md) for:

- Vulnerability disclosure policy
- Reporting a security issue
- Security architecture overview
- Penetration testing guidelines

**Do not report security vulnerabilities through public GitHub issues.**

---

## Compliance

| Standard | Status | Scope |
|----------|--------|-------|
| PCI DSS Level 1 | ✅ Certified | Card processing, storage, transmission |
| SOC 2 Type II | ✅ Certified | Security, availability, confidentiality |
| ISO 27001 | ✅ Certified | Information security management |
| GDPR | ✅ Compliant | EU data subject rights |
| CCPA | ✅ Compliant | California consumer privacy |
| Strong Customer Authentication (SCA) | ✅ Compliant | EU PSD2 directive |

Compliance reports and Shared Responsibility documentation available under NDA to enterprise customers.

---

## Configuration

### Environment Variables

```bash
# Required
LP_PAYMENT_API_KEY=lp_live_xxxxxxxxxxxxxxxxxxxx
LP_WEBHOOK_SECRET=whsec_xxxxxxxxxxxxxxxxxxxx
LP_VAULT_ENCRYPTION_KEY=vk_xxxxxxxxxxxxxxxxxxxx

# Processor credentials
STRIPE_SECRET_KEY=sk_live_xxxxxxxxxxxxxxxxxxxx
ADYEN_API_KEY=AQExxxxxxxxxxxxxxxxxx
PAYPAL_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxxxx
PAYPAL_CLIENT_SECRET=xxxxxxxxxxxxxxxxxxxxxxxx

# Optional
LP_ENVIRONMENT=production              # production | sandbox
LP_LOG_LEVEL=info                      # debug | info | warn | error
LP_FRAUD_THRESHOLD=75                  # Risk score 0-100; above this requires 3DS
LP_DEFAULT_CURRENCY=usd
LP_STATEMENT_DESCRIPTOR=LOCALPULSE    # Appears on customer bank statements
LP_SUPPORT_EMAIL=billing@localpulse.pro
```

### Processor Routing Rules

Configure routing logic in `config/routing.yaml`:

```yaml
routing:
  default_processor: stripe
  rules:
    - condition: currency != 'usd' && currency != 'eur'
      processor: adyen
    - condition: amount > 100000   # > $1,000 USD
      processor: adyen
      fallback: stripe
    - condition: country in ['BR', 'MX', 'CO']
      processor: paypal
  failover:
    enabled: true
    max_retries: 2
    retry_delay_ms: 500
```

---

## Testing

### Sandbox Environment

All development and testing should use the sandbox environment:

```
Base URL: https://payments-sandbox.localpulse.pro/v2
API Key prefix: lp_test_
```

### Test Card Numbers

| Card Number | Behavior |
|-------------|----------|
| `4242 4242 4242 4242` | Successful charge |
| `4000 0000 0000 0002` | Card declined |
| `4000 0025 0000 3155` | Requires 3D Secure authentication |
| `4000 0000 0000 9995` | Insufficient funds |
| `4000 0000 0000 0259` | Dispute immediately |
| `4000 0000 0000 3220` | 3DS2 authentication required |

Use expiry `12/34`, any 3-digit CVV, and any billing postal code.

### Running the Test Suite

```bash
# Unit tests
npm test

# Integration tests (requires sandbox credentials)
LP_ENVIRONMENT=sandbox npm run test:integration

# End-to-end tests
npm run test:e2e

# Coverage report
npm run test:coverage
```

---

## Deployment

### Docker

```bash
docker pull localpulse/payment-gateway:2.4.1
docker run -d \
  --name lp-payment-gateway \
  --env-file .env.production \
  -p 3000:3000 \
  localpulse/payment-gateway:2.4.1
```

### Kubernetes

Helm chart available at:
```bash
helm repo add localpulse https://charts.localpulse.pro
helm install lp-gateway localpulse/payment-gateway \
  --namespace payments \
  --values values.production.yaml
```

See [DEPLOYMENT.md](docs/DEPLOYMENT.md) for full infrastructure guide.

---

## Support

| Resource | Link |
|----------|------|
| Documentation | [docs.localpulse.pro/payment](https://docs.localpulse.pro/payment) |
| Status Page | [status.localpulse.pro](https://status.localpulse.pro) |
| Developer Portal | [developers.localpulse.pro](https://developers.localpulse.pro) |
| Enterprise Support | [support.localpulse.pro](https://support.localpulse.pro) |
| Schedule Consultation | [cal.com/ciprian-stefan-plesca](https://cal.com/ciprian-stefan-plesca) |
| Security Issues | security@localpulse.pro |
| Billing Support | billing@localpulse.pro |

See [SUPPORT.md](SUPPORT.md) for full support information and SLA details.

---

## Contributing

This is a proprietary repository. External contributions are accepted only from approved partners and contractors under active NDA and contribution agreements. See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

---

## License

Copyright © 2026 Ciprian Stefan Plesca. All rights reserved.

LocalPulse Payment Gateway™ is proprietary software. Unauthorized use, reproduction, or distribution is strictly prohibited. See [LICENSE.md](LICENSE.md) for full terms.

---

<p align="center">
  <strong>LocalPulse Payment Gateway™</strong> — Built with security first.<br/>
  © 2026 Ciprian Stefan Plesca · <a href="https://localpulse.pro">localpulse.pro</a> · <a href="https://cal.com/ciprian-stefan-plesca">Schedule Consultation</a>
</p>
