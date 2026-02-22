**Lambda** is AWS's serverless function platform. You deploy code, not infrastructure. AWS runs it on-demand, bills you per invocation + execution time (milliseconds), and scales to zero when idle.

**ECS Fargate** is serverless _containers_. You define a Docker container, AWS runs it without you managing EC2 instances. You pay for vCPU/memory per second while the container is running.

---

### When to use Lambda

- Short-lived tasks (max 15 min execution limit)
- Event-driven workloads: S3 uploads, API Gateway, SQS consumers, scheduled jobs
- Spiky or unpredictable traffic — it scales to zero and back instantly
- You want zero idle cost
- Simple, stateless processing

**Avoid Lambda when:** cold starts matter at p99 (can be 500ms–2s for JVM/large runtimes), you need >15 min runtime, you need >10GB RAM or complex dependencies, or you have sustained high traffic (cost inverts badly).

---

### When to use ECS Fargate

- Long-running services (web servers, APIs, background workers)
- Workloads that need consistent low latency with no cold starts
- Containers with heavy dependencies or large images
- You need >15 min processing time
- Sustained, predictable traffic where Lambda becomes expensive
- More control over networking, sidecars, multi-container pods

**Avoid Fargate when:** your traffic is bursty/sparse (you pay even when idle unless you scale to zero, which adds latency), or the workload is simple enough Lambda handles it cheaper.

---

### Cost crossover point (rough rule)

Lambda gets expensive at sustained high throughput. A single Lambda function running continuously at ~100ms/invocation with millions of daily calls will often cost more than a Fargate task running 24/7. **The break-even is roughly when your Lambda would run >~20-30% of the time continuously** — at that point, a Fargate task at its minimum size is cheaper.

---

### Quick decision rule

|Factor|Lambda|Fargate|
|---|---|---|
|Max runtime|15 min|Unlimited|
|Cold starts|Yes|No (if always-on)|
|Scales to zero|Yes, natively|Yes, but slower|
|Traffic pattern|Spiky|Steady|
|Operational overhead|Minimal|Low (but more than Lambda)|
|Container support|Yes (limited)|Full Docker|
|Cost at low usage|Extremely cheap|Idle cost|
|Cost at high usage|Gets expensive|Predictable|

If it's event-driven and short — Lambda. If it's a service that runs continuously or needs a container — Fargate.