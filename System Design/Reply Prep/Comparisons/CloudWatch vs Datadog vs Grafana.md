### CloudWatch

**Use when:** You're all-in on AWS and want zero-friction monitoring with minimal setup.

**Why:** Native AWS integration means no agents, no config, automatic metrics for every AWS service. Cheap if you're already paying for AWS. Alarms, dashboards, log insights, and traces all in one place.

**Weaknesses:** UI is genuinely bad. Querying logs (Insights) is slow and the query language is painful. Cross-account/cross-region visibility is clunky. Useless outside AWS.

**Best for:** Small-to-mid AWS shops that don't want to manage monitoring infrastructure. Startups. Lambda/ECS/RDS monitoring out of the box.

---

### Datadog

**Use when:** You have a complex, multi-service, polyglot stack and need serious observability — metrics, logs, traces, APM, RUM, security, all correlated.

**Why:** Best-in-class APM and distributed tracing. The correlation between logs, metrics, and traces in a single UI is genuinely powerful. Agent is mature and covers everything. Works on AWS, GCP, Azure, on-prem, k8s — doesn't care.

**Weaknesses:** Extremely expensive at scale. Pricing model (per host + per log ingested + per APM host) can generate nasty surprises. Vendor lock-in is real — their query language, dashboards, and monitors are proprietary.

**Best for:** Mid-to-large engineering orgs, SRE teams, companies with microservices at scale, anyone who needs serious APM. If you can afford it, it's the best all-in-one.

---

### Grafana

**Use when:** You want flexibility, open-source, and already have (or want) your own data sources (Prometheus, Loki, InfluxDB, etc.).

**Why:** Grafana itself is just a visualization and dashboarding layer — it connects to whatever backend you choose. Prometheus + Grafana is the de facto standard for k8s monitoring. You own your data, no per-metric or per-log pricing. Grafana Cloud offers a managed tier with a generous free plan.

**Weaknesses:** You're assembling a stack, not buying a product. Prometheus + Loki + Tempo + Grafana requires operational effort to run well. Alerting has historically been weaker (improving). No built-in APM without Tempo + instrumentation.

**Best for:** Teams comfortable running infrastructure, k8s-heavy environments, cost-sensitive orgs, anyone who wants open standards (OpenTelemetry-first workflows).

---

### Decision shortcut

|Situation|Pick|
|---|---|
|AWS-only, simple setup|CloudWatch|
|Multi-cloud/hybrid, need APM, money available|Datadog|
|k8s, self-hosted, cost control, open-source|Grafana + Prometheus|
|Already using Grafana but want managed|Grafana Cloud|
|Large AWS + need real APM|Datadog (don't fight it)|

The most common real-world answer: **CloudWatch for AWS infra baselines + Datadog or Grafana on top for app-level observability.** They're not mutually exclusive.