## DynamoDB vs RDS — When to Use Each

### Use DynamoDB when:

**Access patterns are known and simple.** DynamoDB is a key-value/document store. If you know exactly how you'll query your data upfront and those queries are simple (get by partition key, maybe sort key), it's excellent.

**You need massive scale with low operational overhead.** It auto-scales, has no servers to manage, and handles millions of requests/sec without tuning. RDS requires you to think about instance sizes, read replicas, and vertical scaling limits.

**You need single-digit millisecond latency at scale.** DynamoDB is consistently fast regardless of table size.

**Your data is sparse or schema-less.** Different items can have different attributes. No migrations needed.

**Workloads are bursty or unpredictable.** On-demand capacity mode handles spikes without pre-provisioning.

**You're building event-driven systems.** DynamoDB Streams integrates naturally with Lambda.

---

### Use RDS when:

**Your data is relational.** You have multiple entities with complex relationships, foreign keys, joins across tables — this is what SQL was built for.

**Access patterns are unknown or ad-hoc.** SQL lets you query any way you want. DynamoDB punishes you hard for queries outside your designed access patterns (full scans are expensive and slow).

**You need ACID transactions across multiple tables.** DynamoDB has transactions but they're limited and costly. RDS handles complex transactional workloads natively.

**You're doing reporting, analytics, or complex aggregations.** GROUP BY, window functions, CTEs — RDS handles this; DynamoDB doesn't.

**Your team knows SQL and the domain is traditional CRUD.** No reason to fight the tool.

**Data integrity is enforced at the DB level.** Constraints, triggers, cascades — RDS has them, DynamoDB doesn't.

---

### The honest summary:

Most applications default to RDS out of familiarity and it's usually fine. DynamoDB shines specifically when you need **web-scale throughput with predictable access patterns** — think user session stores, leaderboards, IoT data, shopping carts. If you're building a typical business application with complex relationships and varied queries, RDS is the right choice and DynamoDB will cause you pain. The biggest mistake people make is picking DynamoDB because it sounds modern, then discovering their access patterns don't fit the model after they've already built half the system.