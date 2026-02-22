You are an AWS FinOps company, How would you design a Payment System?

## Payment System Design for an AWS FinOps Company

This answer assumes you're billing customers for **cloud cost savings, optimization services, or SaaS tooling** — not a generic payment processor.

---

## Core Design Principles

**Don't build what AWS already sells you.** Use managed services aggressively. Your core competency is FinOps, not payments infrastructure.

---

## Architecture

### Payment Processing Layer

**Use Stripe. Full stop.**

- Handles PCI-DSS compliance so you don't have to
- Supports usage-based billing (critical for FinOps SaaS)
- Metered billing API maps directly to cloud spend percentages
- Stripe Tax handles VAT/GST across regions
- Webhook-driven architecture fits event-driven AWS patterns

If you're enterprise-only and need invoicing nets (Net-30/60), add **Stripe Invoicing** or evaluate **Maxio (Chargify)** for more complex B2B billing logic.

### Billing Models You'll Need

Since you're FinOps, you'll likely have hybrid pricing:

|Model|Use Case|Implementation|
|---|---|---|
|% of savings|Performance-based|Metered billing via Stripe Usage Records|
|Flat SaaS|Platform access|Stripe Subscriptions|
|Committed spend tiers|Enterprise contracts|Stripe custom pricing + manual invoices|
|One-time|Assessments/audits|Stripe Payment Intents|

---

## AWS Infrastructure Stack

```
┌─────────────────────────────────────────────────────┐
│                   Your SaaS Platform                │
├─────────────────────────────────────────────────────┤
│  API Gateway → Lambda (Billing Service)             │
│       ↓                                             │
│  EventBridge (billing events)                       │
│       ↓                                             │
│  SQS Queue (Stripe webhook ingestion)               │
│       ↓                                             │
│  Lambda (Webhook Processor)                         │
│       ↓              ↓                              │
│  DynamoDB            RDS Aurora (financial records) │
│  (idempotency keys)  (audit trail, immutable)       │
└─────────────────────────────────────────────────────┘
         ↕ Stripe API
┌─────────────────────┐
│  Stripe             │
│  - Customers        │
│  - Subscriptions    │
│  - Usage Records    │
│  - Invoices         │
└─────────────────────┘
```

**Key AWS services:**

- **API Gateway + Lambda** — billing API endpoints
- **EventBridge** — internal billing events (customer upgraded, savings threshold hit)
- **SQS** — webhook buffer (Stripe webhooks must be idempotent and acknowledged fast)
- **DynamoDB** — idempotency key store (prevent double-charges)
- **Aurora PostgreSQL** — financial ledger (immutable audit trail)
- **Secrets Manager** — Stripe API keys, never env vars
- **SNS** — payment failure alerts to ops team

---

## Critical Implementation Details

### Idempotency

Every billing operation needs an idempotency key. Double-charging a customer is unrecoverable reputationally.

```python
stripe.Invoice.create(
    customer=customer_id,
    idempotency_key=f"invoice-{customer_id}-{billing_period}"
)
```

Store all processed webhook event IDs in DynamoDB with TTL. Reject duplicates.

### The Financial Ledger

**Never use Stripe as your source of truth.** Stripe is your payment processor. You need your own immutable ledger in Aurora:

```sql
CREATE TABLE billing_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_id TEXT NOT NULL,
    event_type TEXT NOT NULL,  -- 'charge', 'refund', 'credit'
    amount_cents BIGINT NOT NULL,
    currency TEXT NOT NULL,
    stripe_payment_intent_id TEXT,
    metadata JSONB,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
    -- NO UPDATE, NO DELETE permissions on this table
);
```

Use append-only writes. Give your app user `INSERT` only, never `UPDATE`/`DELETE`.

### Usage-Based Billing for FinOps

Your savings-based billing likely looks like:

```python
# Run nightly or on savings event
def record_savings_billing(customer_id, savings_amount_usd):
    fee = savings_amount_usd * 0.20  # 20% of savings fee
    
    stripe.SubscriptionItem.create_usage_record(
        subscription_item_id=customer["stripe_si_id"],
        quantity=int(fee * 100),  # in cents
        timestamp=int(time.time()),
        action="increment",
        idempotency_key=f"savings-{customer_id}-{date.today()}"
    )
```

### Dunning (Failed Payment Recovery)

Configure Stripe's Smart Retries. Also build your own:

- Day 1: Email notification
- Day 3: Retry + email
- Day 7: Downgrade to read-only mode (don't kill access immediately — that creates churn)
- Day 14: Suspend account
- Day 30: Terminate

Implement account states in your system: `active`, `past_due`, `suspended`, `terminated`.

---

## Security

- **Never log Stripe webhook payloads** containing card data
- Validate every webhook signature: `stripe.Webhook.construct_event(payload, sig_header, endpoint_secret)`
- Stripe keys in **Secrets Manager**, rotated quarterly
- Separate Stripe accounts for prod/staging
- PCI scope minimization: you handle **no card data** if using Stripe Elements/Checkout on the frontend

---

## What to Build vs. Buy

|Capability|Build|Buy|
|---|---|---|
|Card processing|❌|Stripe|
|Tax calculation|❌|Stripe Tax / Avalara|
|Invoicing|Partial|Stripe Invoicing|
|Usage metering|✅|Your data + Stripe Usage API|
|Financial ledger|✅|Always own this|
|Dunning logic|Partial|Stripe Smart Retries + your own|
|Revenue recognition|❌|Stripe Revenue Recognition or Mosaic|
|Customer portal|❌|Stripe Customer Portal|

---

## The Honest Gotchas

1. **Stripe's metered billing has latency.** Usage records submitted near invoice close can be missed. Build a reconciliation job.
2. **Currency conversion** if you bill in local currencies — your savings calculations are in USD (AWS bills in USD), but you invoice in EUR/GBP. Decide who bears FX risk upfront.
3. **Enterprise contracts** break every automated billing assumption you have. Build a manual override path from day one.
4. **Revenue recognition** (ASC 606) gets complex with performance-based billing. Get an accountant involved early, not after you've built the system.
5. **Refunds are politically complex** in FinOps — if you claim savings that the customer disputes, you need a dispute resolution workflow before you need a refund workflow.