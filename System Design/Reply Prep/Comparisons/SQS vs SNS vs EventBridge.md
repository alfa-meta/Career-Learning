
---

### SQS — Simple Queue Service

**What it is:** A durable message queue. Messages sit in the queue until a consumer pulls and processes them.

**Model:** Pull-based (consumers poll the queue)

**Use when:**

- You need to decouple a producer from a consumer and don't care about speed, just reliability
- You want to buffer work (e.g. image processing jobs, email sends)
- You need exactly-once or at-least-once processing guarantees
- One consumer per message (competing consumers pattern)
- You need dead-letter queues for failed messages

**Key traits:**

- Messages persist up to 14 days
- FIFO queues available for ordering guarantees
- Max message size 256KB
- No push — consumers must poll

---

### SNS — Simple Notification Service

**What it is:** A pub/sub fan-out service. One message published → many subscribers receive it simultaneously.

**Model:** Push-based (SNS pushes to subscribers)

**Use when:**
- You need to fan-out one event to multiple consumers (e.g. notify email + SQS + Lambda all at once)
- You want a simple broadcast mechanism
- Order doesn't matter, delivery guarantees are "best effort" (standard) or ordered (FIFO)

**Key traits:**
- No persistence — if a subscriber is down, the message is lost (unless you fan out to SQS)
- Supports HTTP, Lambda, SQS, email, SMS as subscribers
- Classic pattern: SNS → multiple SQS queues (gives you fan-out + durability)

---

### EventBridge — Event Bus

**What it is:** A fully managed event router with filtering, schema registry, and native integration with 200+ AWS services and SaaS tools.

**Model:** Push-based with rule-based routing

**Use when:**
- You're building event-driven architecture and need content-based routing (route event X to service A, event Y to service B based on event _content_)
- You want to react to AWS service events (e.g. EC2 state change, S3 object created, CodePipeline success)
- You're integrating SaaS events (Stripe, Zendesk, etc.)
- You need a schema registry and event discovery
- You want event replay or archiving

**Key traits:**
- Rules filter by event content (JSON pattern matching)
- Up to 5 targets per rule
- Native CloudWatch, Lambda, SQS, SNS, Step Functions targets
- More expensive and slightly higher latency than SNS
- The "grown-up" version of SNS for complex routing

---

### Decision Summary

|Need|Use|
|---|---|
|Buffer work / async job queue|SQS|
|Fan-out one message to many|SNS|
|Fan-out + durability|SNS → SQS|
|Content-based routing|EventBridge|
|React to AWS service events|EventBridge|
|Simple, cheap, fast pub/sub|SNS|
|Complex event-driven microservices|EventBridge|

---

### The honest take on when people mess this up

- People reach for EventBridge when SNS would be simpler and cheaper — EventBridge is overkill for basic fan-out.
- People use SQS when they actually need fan-out and end up duplicating queues unnecessarily.
- SNS alone is often wrong for anything requiring durability — pair it with SQS if you can't afford to drop messages.