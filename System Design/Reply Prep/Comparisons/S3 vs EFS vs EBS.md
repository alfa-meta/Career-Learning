## S3 vs EFS vs EBS

These are three fundamentally different storage paradigms, not just different tiers of the same thing.

---

### S3 — Object Storage

**What it is:** A flat key-value store for arbitrary blobs of data. No real filesystem. You PUT and GET objects via HTTP.

**Use when:**

- Storing files that are written once and read many times (images, videos, backups, logs, datasets)
- You need storage accessible from anywhere — multiple regions, Lambda, external services
- You want near-infinite scale without managing capacity
- Static website hosting, data lakes, ML training data

**Why:** Cheapest per GB (~$0.023/GB). Highly durable (11 nines). Decoupled from compute — survives EC2 termination. Scales infinitely.

**Don't use when:** You need to modify files in-place, need low-latency random reads, or need a mountable file system.

---

### [[EBS]] — Block Storage

Elastic Block Store

**What it is:** A virtual hard drive attached to a single EC2 instance. Acts exactly like a disk — you format it, mount it, read/write at the block level.

**Use when:**

- Your EC2 instance needs a persistent disk (databases, OS volumes)
- You need low-latency, high-throughput I/O (PostgreSQL, MySQL, Kafka)
- You need to modify files in place

**Why:** Lowest latency of the three. Predictable IOPS. gp3 volumes give you tunable performance. Snapshots to S3 for backup.

**Don't use when:** You need the same data accessible from multiple EC2 instances simultaneously — EBS is **single-instance only** (with rare multi-attach exceptions for specific use cases).

**Cost:** ~$0.08–0.125/GB/month depending on type.

---

### EFS — Network File System

Elastic File System

**What it is:** A fully managed NFS share. Multiple EC2 instances (or containers, Lambda) can mount it simultaneously and read/write the same files.

**Use when:**
- You need **shared file system access** across multiple instances or containers
- Lift-and-shift apps that expect a shared NFS mount
- ECS/EKS workloads that need a shared persistent volume
- Content management, shared config, home directories

**Why:** The only AWS-native option for concurrent multi-instance read/write filesystem access. Auto-scales capacity.

**Don't use when:** You only have one instance (use EBS, it's cheaper and faster), or you're storing static assets (use S3).

**Cost:** ~$0.30/GB/month — significantly more expensive than EBS and S3.

---

### Decision Matrix

|Need|Use|
|---|---|
|Store images/videos/backups|S3|
|Database on a single EC2|EBS|
|Shared files across multiple EC2/containers|EFS|
|Data lake / analytics|S3|
|Boot volume for EC2|EBS|
|Shared NFS across microservices|EFS|
|Cost-sensitive bulk storage|S3|

**TL;DR:** EBS = your hard drive. EFS = a network share. S3 = a bucket you access over HTTP. They're not interchangeable — pick based on access pattern, not just price.