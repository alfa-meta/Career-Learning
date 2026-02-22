Here's what you need to know, straight up.

---

## 7 Most Common Design Pattern Questions

These are the GoF (Gang of Four) patterns most likely to come up in a FinTech consultant interview. Know them cold — definition, use case, and a FinTech example for each.

**1. Strategy Pattern** Define a family of algorithms, encapsulate each one, and make them interchangeable at runtime. In FinTech: swapping between payment processors (Stripe, PayPal, BACS) without changing client code. Expect: _"How would you design a system that supports multiple payment methods?"_

**2. Observer Pattern** One-to-many dependency — when one object changes state, all dependents are notified. In FinTech: event-driven fraud alerts, balance change notifications, or audit logs reacting to transactions. Expect: _"How would you notify downstream systems when a transaction completes?"_

**3. Factory / Abstract Factory Pattern** Delegate object creation to subclasses. In FinTech: creating different account types (savings, current, ISA) or different loan product objects without the client knowing the concrete class. A common pattern question in Reply interviews specifically (confirmed via Glassdoor).

**4. Singleton Pattern** Ensure only one instance of a class exists. In FinTech: a configuration service, a database connection pool manager, or a logging service. Know the thread-safety pitfalls — interviewers often probe this.

**5. Decorator Pattern** Attach additional responsibilities to an object dynamically. In FinTech: adding interest calculation, overdraft protection, or fee logic on top of a base account object without modifying it.

**6. Circuit Breaker Pattern** Prevent cascading failures when a downstream service is unavailable. In FinTech: if the fraud detection API is down, stop hammering it — return a fallback response. This is the most FinTech-relevant resilience pattern. Expect it.

**7. CQRS (Command Query Responsibility Segregation)** Separate read and write models. In FinTech: writes go to a transactional store (high consistency), reads come from a read-optimised replica. Critical for high-throughput trading or banking platforms. Technically an architectural pattern but appears constantly in FinTech design discussions.

---

## 7 Most Common System Design Questions

Calibrated for a FinTech consultant role — these blend distributed systems knowledge with domain-specific constraints (ACID, compliance, fraud, latency).

**1. Design a Payment Processing System** The most common FinTech question. Cover: idempotency keys, ACID transactions, card network integration (Visa/Mastercard), tokenisation, PCI-DSS compliance, retry logic, and settlement flows. Know the difference between authorisation, capture, and settlement.

**2. Design a Fraud Detection System** Real-time vs batch detection. Cover: Kafka for event streaming, ML feature stores, rule engines, sub-100ms latency requirement, velocity checks (e.g. 5 transactions in 30 seconds), and the trade-off between false positives and false negatives.

**3. Design a Digital Wallet / Account Balance Service** Core challenge is consistency — no double-spends, no negative balances. Cover: distributed locks, two-phase commit or saga patterns, event sourcing for auditability, and ACID guarantees.

**4. Design a Rate Limiter** Not FinTech-specific but asked universally at consultant level. Cover: token bucket vs leaky bucket algorithms, Redis-based implementation, distributed rate limiting across nodes. Directly relevant to API gateways in FinTech.

**5. Design a Notification System (Transactional Alerts)** Cover: Kafka/SQS as message broker, push vs pull, fan-out to email/SMS/push, deduplication, guaranteed delivery, retry with exponential backoff, and ordering guarantees.

**6. Design a Loan Origination / Credit Scoring System** Relevant to FinTech consulting. Cover: async credit bureau calls, rule-based decisioning engines, microservices for eligibility, underwriting, and disbursement. Compliance and audit trail are critical here.

**7. Design a Distributed Ledger / Transaction History Service** Cover: append-only log (event sourcing), immutability, eventual consistency vs strong consistency, querying historical balances at a point in time, and regulatory audit requirements.

---

## What Reply Specifically Cares About

Based on Glassdoor feedback for Reply interviews: they run a technical round covering **design patterns, SQL, and reasoning exercises**. For the FinTech division specifically, expect the conversation to lean heavily on **architecture trade-offs** and your ability to map patterns to real financial problems — not just recite definitions. They want consultants who can explain _why_, not just _what_.

Know your CAP theorem, ACID vs BASE, and be ready to defend every architectural choice you make.