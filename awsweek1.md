# AWS Week 1 — Real-World Use Case Scenarios

> Scenario-based questions and answers covering EC2, ECS, EKS, Lambda, Load Balancers, and more.

---

## Scenario 1 — Amazon EC2 Basics

**Situation:**
A retail e-commerce startup is launching its website. The engineering team doesn't want to invest in physical servers and needs the flexibility to scale compute capacity during promotional sales events.

**Question:**
Which AWS service should the team use, and what are the key benefits?

**Answer:**
The team should use **Amazon EC2**. It provides resizable virtual servers in the cloud with no upfront hardware investment. The team can launch instances in minutes, configure security and networking, and scale up during flash sales and scale back down afterward — paying only for what they use (pay-as-you-go pricing).

---

## Scenario 2 — EC2 Instance Types

**Situation:**
A biotech company needs to process large genomic datasets in-memory for real-time analysis. Meanwhile, a media company needs to transcode thousands of video files simultaneously.

**Question:**
Which EC2 instance family should each company choose?

**Answer:**
- The **biotech company** should use **Memory Optimized instances** (e.g., `r6i`) — designed to load large datasets into RAM for fast, in-memory processing.
- The **media company** should use **Compute Optimized instances** (e.g., `c6i`) — built for CPU-heavy workloads like video encoding and transcoding.

> **Key Principle:** Match the instance family to your application's bottleneck — CPU, memory, storage, or network.

---

## Scenario 3 — EC2 Purchasing Options

**Situation:**
A fintech company runs a nightly batch job analyzing trading data. The job runs for 4–6 hours every night, four days a week, and **cannot be interrupted once started**. The team plans to run it for at least one year.

**Question:**
Which EC2 purchasing option is the best fit?

**Answer:**
**Regional Reserved Instances (1-year term).**

- The workload is **predictable and steady**, making Reserved Instances the right choice for significant cost savings (up to 72% vs. On-Demand).
- **Spot Instances are ruled out** — they can be interrupted at any time, which is not acceptable here.
- **On-Demand** would work but is the most expensive for a long-running, predictable workload.

---

## Scenario 4 — Security Groups

**Situation:**
A hospital is building a patient portal. The web application must be accessible over HTTPS from the internet, but the backend MySQL database must never be reachable from outside the VPC.

**Question:**
How should the team configure Security Groups?

**Answer:**
- **Web server Security Group:** Allow inbound **TCP port 443 (HTTPS)** from `0.0.0.0/0` (internet).
- **Database Security Group:** Allow inbound **TCP port 3306 (MySQL)** only from the web server Security Group's ID — not from the internet.

This implements the **least-privilege principle**: only required ports are open, and the database is never directly exposed.

---

## Scenario 5 — Public, Private & Elastic IPs

**Situation:**
A SaaS company is deploying a 3-tier architecture: a frontend web server, an application server, and a database server. The web server's domain name must not break when the instance is stopped and restarted.

**Question:**
Which IP types should be used for each tier?

**Answer:**
| Tier | IP Type | Reason |
|------|---------|--------|
| Frontend (web server) | **Elastic IP** | Static public IP ensures DNS records always resolve correctly, even after restarts |
| Application server | **Private IP** | Only needs to communicate internally within the VPC |
| Database server | **Private IP** | Must never be directly accessible from the internet |

---

## Scenario 6 — Placement Groups

**Situation:**
A bank runs a fraud detection system processing billions of transactions daily across hundreds of Hadoop cluster nodes. The engineering manager requires that if a single rack or piece of hardware fails, **only a small portion of the cluster is impacted** while the rest continues processing.

**Question:**
Which EC2 Placement Group type is the best fit?

**Answer:**
**Partition Placement Group.**

- Instances are spread across isolated partitions (racks). If one rack fails, only that partition is affected.
- **Cluster Placement Group** is ruled out — it places all instances close together on one rack, maximizing the blast radius of a hardware failure.
- **Spread Placement Group** is better for small, highly critical instances, not large Hadoop clusters with hundreds of nodes.

---

## Scenario 7 — Scalability & High Availability

**Situation:**
A food delivery app experiences heavy traffic at lunchtime (12–2 PM) and dinnertime (7–9 PM) and very low traffic at night. The team needs the system to remain operational even if one AWS data center fails.

**Question:**
How should the team architect this solution?

**Answer:**
- Use **EC2 Auto Scaling Groups (ASG)** with scheduled scaling policies to automatically add servers before peak hours and remove them at night.
- Deploy instances across **multiple Availability Zones (AZs)** — if one AZ goes down, traffic is automatically routed to instances in other AZs.
- Place an **Application Load Balancer (ALB)** in front to distribute traffic and automatically deregister unhealthy instances.

---

## Scenario 8 — Load Balancers

**Situation:**
A travel booking platform hosts three microservices on ECS behind a **single load balancer**:
- `/flights` → Flight Reservation Service
- `/hotels` → Hotel Booking Service
- `/cars` → Car Rental Service

Each service runs in its own target group for independent scaling.

**Question:**
Which load balancer type and routing feature should be used?

**Answer:**
**Application Load Balancer (ALB) with Path-based Routing.**

- ALB operates at Layer 7 (HTTP/HTTPS) and can inspect the URL path to route requests to the correct target group.
- **Network Load Balancer** is not suitable here as it operates at Layer 4 and cannot inspect URL paths.
- Host-based routing would be used if each service had its own subdomain (e.g., `flights.example.com`) — not the case here.

---

## Scenario 9 — ECS EC2 Launch Type vs. Fargate

**Situation A — Regulated Enterprise:**
A government contractor must comply with strict security standards, requires custom AMIs with specific hardening, and needs full control over the underlying compute environment.

**Answer:** **ECS with EC2 Launch Type** — provides full control over the host OS, custom AMIs, instance types, and patching schedules.

---

**Situation B — ML Startup:**
A startup is building a machine learning image-recognition API on ECS. Traffic is unpredictable with occasional bursts. The team wants **per-second billing**, automatic scaling, and **zero server management**.

**Answer:** **ECS with Fargate Launch Type** — AWS manages all infrastructure, tasks scale automatically, and you pay only for the vCPU and memory consumed per second.

---

## Scenario 10 — Amazon EKS

**Situation:**
A SaaS company has been running Kubernetes on-premises for 3 years. Their DevOps team is deeply invested in Kubernetes tooling, Helm charts, and CI/CD pipelines. They now want to move to AWS without rewriting their deployment pipelines.

**Question:**
Which AWS compute service is the best fit?

**Answer:**
**Amazon EKS (Elastic Kubernetes Service).**

- EKS runs upstream open-source Kubernetes — no proprietary changes, so existing Helm charts and `kubectl` commands work as-is.
- AWS manages the **control plane** (patching, upgrades, high availability across AZs).
- Integrates natively with IAM, VPC, CloudWatch, and ECR.
- Enables cloud portability — workloads can run on AWS, on-premises, or other clouds.

---

## Scenario 11 — AWS Lambda

**Situation:**
An e-commerce company stores all sales transactions in Amazon DynamoDB. Every time a new record is inserted, the team needs to run **duplicate validation logic** automatically. The solution must be **event-driven** with **no servers to manage**.

**Question:**
Which AWS architecture best meets this need?

**Answer:**
**Enable DynamoDB Streams → Trigger an AWS Lambda function.**

- DynamoDB Streams captures every insert, update, or delete event in near real-time.
- Lambda is invoked automatically for each new record, runs the validation logic, and terminates.
- No servers to provision, patch, or manage.
- Other options (exporting to S3, using Kinesis, or AWS Glue) introduce unnecessary latency and complexity for this use case.

---

## Scenario 12 — Lambda Concurrency & Throttling

**Situation:**
An online marketplace runs a Lambda-backed checkout API. During a 5-minute flash sale, concurrent invocations spike dramatically. The sudden spike is throttling the checkout function AND causing other business-critical Lambda functions (like order confirmation emails) to fail simultaneously.

**Question:**
How should the solutions architect prevent this from happening in future sales events?

**Answer:**
Use **Reserved Concurrency** on the checkout Lambda function.

- **Reserve** a dedicated portion of the account's concurrency pool for the checkout function — guaranteeing it always has capacity during spikes.
- At the same time, this **caps** the checkout function from consuming the entire account's concurrency pool, protecting other functions.
- Additionally, **monitor** `ConcurrentExecutions` and `Throttles` metrics in CloudWatch and proactively request a **concurrency limit increase** from AWS ahead of major sale events.

---

## Quick Reference — Decision Matrix

| Scenario Type | Best AWS Service / Option |
|--------------|--------------------------|
| Unpredictable bursts, no server management | Lambda or ECS Fargate |
| Predictable, steady 1-year workload | Reserved Instances |
| Deep discounts, interruptible jobs | Spot Instances |
| Large distributed fault-tolerant clusters | Partition Placement Group |
| Low-latency HPC between nodes | Cluster Placement Group |
| URL path-based microservice routing | ALB with Path-based Routing |
| Real-time TCP/UDP, static IP, high throughput | Network Load Balancer |
| Existing Kubernetes workloads on AWS | Amazon EKS |
| Containers with full host control | ECS EC2 Launch Type |
| Event-driven serverless data processing | Lambda + DynamoDB Streams |
