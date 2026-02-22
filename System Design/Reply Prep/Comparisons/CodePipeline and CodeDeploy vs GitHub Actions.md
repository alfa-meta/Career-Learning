## CodePipeline + CodeDeploy vs GitHub Actions

### What are they?

**GitHub Actions** is a CI/CD platform built into GitHub. You define workflows in YAML that trigger on git events (push, PR, etc.) and run jobs in containers. It handles both CI (build/test) and CD (deploy) in one place.

**AWS CodePipeline** is an orchestration service — it chains together stages (source → build → test → deploy). It doesn't do the work itself; it coordinates other services. **CodeDeploy** is the actual deployment engine that pushes code to EC2, Lambda, or ECS, handling rollout strategies like blue/green and canary.

---

### When and why to use each

**Use GitHub Actions when:**

- Your code already lives in GitHub (zero friction)
- You want one tool for CI and CD
- You're deploying to diverse targets (not just AWS) — Kubernetes, Vercel, Azure, GCP, etc.
- Your team knows git workflows and doesn't want to learn AWS console
- You want fast setup — workflows are just YAML in the repo, no infrastructure to provision
- Cost matters: GitHub Actions has a generous free tier and is cheaper at low-to-mid scale

**Use CodePipeline + CodeDeploy when:**

- You're fully AWS-native and want tight IAM integration (no secrets leaving AWS, roles instead of API keys)
- You need CodeDeploy-specific deployment strategies on EC2 fleets — blue/green with ALB traffic shifting, in-place rolling deploys across hundreds of instances
- Compliance/audit requirements demand AWS CloudTrail logging of every pipeline execution
- You want deployments to be decoupled from your source control provider (pipeline persists even if you migrate off GitHub)
- You're already using CodeBuild and want everything in one AWS account with unified billing

---

### The honest take

GitHub Actions has largely won the CI/CD mindshare for good reason — it's simpler, more flexible, and has a massive ecosystem of community actions. Most teams default to it unless they have a specific reason not to.

CodePipeline/CodeDeploy makes sense when you're operating at scale on AWS and the operational benefits (IAM roles, no outbound secrets, native CloudWatch/CloudTrail integration, EC2 fleet deployment controls) outweigh the added complexity. For Lambda or ECS, many teams use GitHub Actions _with_ AWS CLI/CDK and skip CodeDeploy entirely.

If you're starting fresh and not forced into AWS tooling: use GitHub Actions. If you're an AWS shop with strict security controls or large EC2 fleets: CodePipeline + CodeDeploy earns its complexity.