## CloudFront vs ALB — When and Why

These solve different problems and are often used **together**, not as alternatives.

---

### ALB (Application Load Balancer)

A **Layer 7 load balancer** inside your VPC. Its job is distributing traffic across targets (EC2, ECS, Lambda, etc.).

**Use it when:**

- You need to route HTTP/S traffic to multiple backend services/containers
- You're doing path-based or host-based routing (`/api` → service A, `/web` → service B)
- You need WebSocket support
- You need sticky sessions
- Your targets are inside AWS (EC2, ECS, EKS, Lambda)
- You need gRPC support

**What it's NOT:** It's regional. It has no caching, no global PoPs, no DDoS protection beyond basic AWS Shield Standard.

---

### CloudFront

A **CDN + global edge network**. Its job is getting content closer to users and reducing load on your origin.

**Use it when:**

- You need global low-latency delivery (190+ edge locations)
- You want to cache static or dynamic content
- You need WAF integration at the edge (block bad traffic before it hits your infra)
- You want to absorb DDoS at the edge (Shield Advanced)
- You're serving S3, APIs, or any HTTP origin globally
- You want to reduce egress costs (CloudFront egress is cheaper than ALB/EC2 direct egress)
- You need geo-restriction or geo-routing
- You want Lambda@Edge or CloudFront Functions for edge compute

---

### The Common Pattern: Use Both Together

```
User → CloudFront → ALB → ECS/EC2 targets
```

CloudFront handles: caching, WAF, DDoS, global edge, SSL termination at edge, cost-efficient egress.  
ALB handles: routing traffic to the right backend service inside your VPC.

Lock down your ALB so it **only accepts traffic from CloudFront** (via CloudFront's managed prefix list or a secret header + WAF rule), otherwise you're bypassing all your edge security.

---

### When ALB Alone Makes Sense

- Internal services (not internet-facing)
- Traffic is already regional (e.g., B2B API where latency isn't a concern)
- You're early-stage and want to minimize complexity

### When CloudFront Alone Makes Sense

- Pure static site on S3
- You don't need load balancing (single origin, single target)

---

**TL;DR:** ALB = load balancing within AWS. CloudFront = global delivery, caching, and edge security. For any serious internet-facing app, use both.