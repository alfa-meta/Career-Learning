## API Gateway vs ALB — When and Why

These two are frequently confused because both sit in front of your backend, but they solve different problems.

---

### ALB (Application Load Balancer)

**What it is:** A Layer 7 load balancer. Its job is routing HTTP/HTTPS traffic to targets (EC2, ECS, Lambda, IPs).

**Use when:**

- You have microservices or containers and need to route traffic based on path/host to different target groups
- You need high-throughput, low-latency traffic distribution (millions of RPS cheaply)
- You're running WebSockets or HTTP/2 at scale
- You already have your own auth/middleware layer
- Cost is a concern — ALB is significantly cheaper per request than API Gateway

**Strengths:** Raw performance, simple routing rules, native ECS/EKS integration, cheaper at scale, supports gRPC.

**Weaknesses:** No built-in auth, no request throttling per consumer, no API key management, no usage plans, no request/response transformation, minimal observability out of the box.

---

### API Gateway (REST / HTTP API)

**What it is:** A fully managed API management layer. It's not just a load balancer — it's a control plane for APIs.

**Use when:**

- You need **auth baked in** (Cognito, JWT, Lambda authorizers, IAM)
- You need **rate limiting and throttling** per client/API key
- You're building **serverless** (Lambda) backends — API GW + Lambda is the canonical pattern
- You need **usage plans** (monetization, quotas per consumer)
- You need **request/response transformation** without writing middleware
- You want **built-in caching** at the edge
- You're exposing a public API to third-party developers

**HTTP API vs REST API (within API Gateway):**

- HTTP API: cheaper (~70% less), lower latency, supports JWT auth — use this for most new builds
- REST API: full feature set (caching, request transformation, WAF integration, usage plans) — use only when you need those features

**Weaknesses:** More expensive at scale, cold starts can affect perceived latency (especially with Lambda), 29-second hard timeout, more complex configuration.

---

### Decision Rule

|Need|Use|
|---|---|
|Route traffic to containers/EC2 at scale|ALB|
|Serverless backend (Lambda)|API Gateway|
|Public API with auth + rate limiting|API Gateway|
|Internal service-to-service traffic|ALB|
|WebSockets at high scale|ALB (or API GW WebSocket but it's expensive)|
|Cheap high-throughput|ALB|
|API keys, usage plans, developer portal|API Gateway|

**They're not mutually exclusive.** A common pattern is API Gateway in front for auth/rate-limiting, forwarding to an ALB that distributes to your actual services. You get the control plane of API GW without paying per-request for all internal traffic.