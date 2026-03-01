## AWS Secrets Manager vs Parameter Store

Both are AWS services for storing configuration and sensitive data, but they serve different primary purposes.

---

### Parameter Store (SSM Parameter Store)

A hierarchical key-value store for configuration data. Think app config, feature flags, database URLs, non-secret settings, and secrets if you're budget-conscious.

**Types:**

- **Standard** – Free, up to 4KB per parameter, 10,000 parameters
- **Advanced** – $0.05/parameter/month, up to 8KB, 100,000 parameters, parameter policies (TTL/expiry)
- **SecureString** – Encrypts value with KMS. This is how you store secrets in Parameter Store.

**Use it when:**

- You need a free or cheap config store
- You want hierarchical organisation (`/prod/app/db_host`)
- You're storing non-secret config alongside secrets
- You don't need automatic rotation

---

### Secrets Manager

Purpose-built for secrets. More expensive, but has features Parameter Store simply doesn't.

**Key differentiators:**

- **Automatic rotation** – Native integration with RDS, Redshift, DocumentDB. Rotates credentials on a schedule without downtime
- **Cross-account access** – Easier to share secrets across AWS accounts
- **Versioning** – Keeps `AWSCURRENT` and `AWSPREVIOUS` versions natively, making rotation safe
- **Higher cost** – $0.40/secret/month + $0.05 per 10,000 API calls

**Use it when:**

- You need automatic credential rotation (especially DB passwords)
- You're storing high-sensitivity secrets (API keys, OAuth tokens, private keys)
- You need audit trails specifically for secret access
- Cross-account secret sharing is required

---

### Direct Comparison

||Parameter Store|Secrets Manager|
|---|---|---|
|Cost|Free (standard)|$0.40/secret/month|
|Auto-rotation|No|Yes|
|Max size|4KB / 8KB|64KB|
|Cross-account|Awkward|Native|
|Versioning|Manual|Built-in|
|Primary use|Config + secrets|Secrets only|

---

### The Honest Rule of Thumb

**Use Parameter Store** for anything that isn't a credential requiring rotation — environment config, feature flags, ARNs, non-rotating API keys.

**Use Secrets Manager** the moment you need automatic rotation, or you're storing database credentials in production and want that safety net. The cost is negligible compared to the operational risk of manually rotating secrets.

Many teams use both: Parameter Store for config, Secrets Manager for credentials. That's the correct pattern.