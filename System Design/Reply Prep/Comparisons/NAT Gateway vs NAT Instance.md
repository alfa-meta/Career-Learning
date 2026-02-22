Both solve the same problem: **letting private subnet resources reach the internet (outbound only) without being directly reachable from the internet.**

---

### NAT Gateway

AWS-managed service. You deploy it in a public subnet, attach an Elastic IP, and point private subnet route tables at it.

**When to use it:** Almost always. It's the default correct answer.

**Why:**

- Fully managed — no patching, no maintenance, no failure handling
- Automatically scales up to 100 Gbps
- 99.99% availability SLA (deploy one per AZ for full HA)
- No need to disable source/destination checks
- Costs ~$0.045/hr + $0.045/GB data processed

**Downsides:**

- Can't use it as a bastion host
- Can't attach security groups (only NACLs control it)
- More expensive at scale for data-heavy workloads

---

### NAT Instance

A regular EC2 instance running a NAT AMI (or your own config) with source/destination check disabled.

**When to use it:** Rarely. Mostly legacy or very specific edge cases.

**Why you might still choose it:**

- **Cost** — a `t4g.nano` at ~$3/month vs NAT Gateway's minimum ~$32/month. Significant if you're running dev/test environments 24/7 with low traffic
- **Dual-purpose** — can also act as a bastion/jump host
- **Custom routing** — can do port forwarding, run custom firewall rules, VPN, etc.
- **Bandwidth-heavy workloads** — if you're pushing terabytes, a large instance can be cheaper than paying per-GB on NAT Gateway

**Downsides:**

- You manage HA yourself (no built-in redundancy)
- You manage patching, monitoring, failover
- Single point of failure unless you build around it
- Doesn't scale automatically

---

### Decision Rule

Use **NAT Gateway** unless:

1. You're cost-sensitive on a low-traffic non-prod environment
2. You need the instance to double as a bastion
3. You have specific networking requirements a managed service can't meet

For production at any serious scale, NAT Gateway's operational simplicity is worth the cost premium.