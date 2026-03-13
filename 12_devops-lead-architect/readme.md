
# DevOps Notes – Interview Quick Revision

	Personal notes / talking points for DevOps / Platform Engineering interview  
	(Dev Lead + DevOps responsibilities – Microservices migration & GitOps environment)

## 1. Introduction / About Myself

	- Currently working at **Netcracker** on a large-scale **multi-microservice architecture**  
	  → Key domains: Billing, Customers, GSM, Locations, Accounts, Provisioning, etc.

	- Multiple dedicated **development** and **DevOps** teams per microservice domain

	- My current role:  
	  - **Dev Lead** for migration from legacy monolith → modern microservices target architecture  
	  - Active member of **DevOps team** responsible for **Products**, **Customers**, **Accounts**, and related microservices

	- Core responsibilities:  
	  - Implementing and improving **DevOps practices & processes**  
	  - **Containerization** (Docker)  
	  - **Orchestration** (Kubernetes – EKS)  
	  - **CI/CD + GitOps** (GitHub Actions + ArgoCD)  
	  - **Infrastructure as Code** (Terraform, Ansible)

## 2. Best Practices I Follow & Typical Daily / Weekly Work

	- Closely collaborate with multiple development teams using **Agile methodology**  
	  - Attend sprint planning, backlog refinement, daily stand-ups  
	  - Understand weekly sprints + monthly roadmaps  
	  → Helps me anticipate upcoming infrastructure & deployment needs

	- Common requests I handle from dev teams:

	  - Provision new environments / clusters (IaC-first approach)
	  - Create modular, reusable **EKS clusters** via Terraform
	  - Kubernetes manifests & deployment troubleshooting
	  - Containerization support (especially for junior developers)
	  - Writing / reviewing **Dockerfiles**
	  - Git branching strategy enforcement & repository hygiene
	  - Setting up **GitHub webhooks**, repository settings, branch protection rules

	- Documentation & knowledge sharing:  
	  - Create & maintain best-practice guides (e.g., "How to containerize a microservice")  
	  - Share documentation across teams (especially those without dedicated DevOps engineers)

## 3. One of My Notable Fixes / Achievements

	**Problem:** ReplicaSets suddenly not creating new pods → pods stuck in Pending state  
	**Root cause:** IP exhaustion in the subnet (EKS node/ENI limits reached)  
	**Action taken:**  
	- Identified subnet IP utilization spike  
	- Worked with AWS / networking team (AM = Account Manager?) to increase secondary CIDR or subnet size  
	- Added monitoring alerts for IP usage  
	- Documented the issue & mitigation steps for the team

## 4. Major Challenges Faced & How I Solved Them

	### Challenge 1 – Inconsistent & fragmented CI/CD pipelines

	**Before:**
	- Every microservice had different CI/CD setup (or none)
	- Some had partial GitHub Actions CI, no proper CD to Kubernetes
	- Manual deployments were common → error-prone & slow

	**What I did:**
	- Studied existing pipelines
	- Designed **standardized template** (GitHub Actions + ArgoCD)
	- Built **Proof of Concept** on one critical service → demonstrated **zero-touch** deployment
	- Gradually rolled out standardized pipeline to **all services** (~3–4 months)
	- Result: Consistent, reliable, fully automated deployments across the organization

	### Challenge 2 – Terraform without remote state & locking

	**Before:**
	- Local state files → frequent conflicts, lost state, manual copy-paste mess
	- No versioning, no collaboration safety

	**What I did:**
	- Migrated everything to **remote backend** (S3 + DynamoDB for locking)
	- Enforced state locking
	- Refactored configurations into **modular reusable structure**
	- Result: Stable, collaborative IaC → fewer errors, higher team confidence

	### Bonus – Handling QA requests for manual pre-validation (while keeping GitOps purity)

	**Goal:** Allow QA to test manifests before automatic ArgoCD sync, without breaking GitOps principles

	**Approaches I use (in order of preference):**

	1. **Preferred (GitOps way)**  
	   - Keep environment-specific configs in Git (`dev/`, `qa/`, `prod/`)  
	   - Create separate ArgoCD Application for QA with **auto-sync disabled**  
	   - QA tests → after approval → merge to main → prod syncs automatically

	2. **Temporary / emergency validation**  
	   - Manually apply manifest with `kubectl apply -f ...` in QA cluster  
	   - Immediately document & clean up after testing  
	   - Never allow long-term drift

	**Guiding principle:**  
	Minimize direct cluster changes → maximize **traceability, auditability & security** through GitOps.

## 4.Monitoring and Observability
	   - https://github.com/kunalshrivastavapune25/observability-zero-to-hero
	   - https://tcsglobal.udemy.com/course/ultimate-devops-and-cloud-interview-guide/learn/lecture/50809923#overview
	   - 
	   - JUST MAKE SURE U ADD A POINT IN UR INTRODUCTION THAT I MADE PROMQL FOR MY CUSTOM MONITORING
	   - AND CHATGPT HOW IT IS DONE
	   - 
	   - JUST CHECK IG THIS CAN BE DONE FROM AGENTIC AI I.E INCIDENT MANAGEMENT LIKE CREATING JIRA TICKETS AND ALL THROUGH MCPS
	   - LIKE LETS SAY ALEART MANAGER SENDS SOME ALERT THEN E2E INCIDENT HANDLING CAN BE DONE FROM AGENTIC AI AUTOMATICALLY IN NO TIME 
	   - JUST GO TROUGH PAGERDUTY ALL GYAN SAME THING THINK OF IMPLEMENTING IN AGENTIC AI

## 6. what do troble shoot pod 
	   - https://tcsglobal.udemy.com/course/ultimate-devops-and-cloud-interview-guide/learn/lecture/50809923#overview
## 7. my hand written notes 
## 8. kubernetes app  
	   - https://www.linkedin.com/in/ahmedalibutt/
	   - https://www.linkedin.com/feed/update/urn:li:activity:7433489248076374016/
	   - C:\kunal\LINKEDIN IMAGES\
## 9. veermala interview quetions 
	   - https://tcsglobal.udemy.com/course/ultimate-devops-and-cloud-interview-guide/learn/lecture/50809923#overview
## 10. veermala udemy project 
	   - https://tcsglobal.udemy.com/course/ultimate-devops-project-with-resume-preparation/learn/lecture/48207855?start=15#overview
	   - https://github.com/iam-veeramalla/ultimate-devops-project-demo
	   - https://github.com/iam-veeramalla/ultimate-devops-project-aws
## 11. aman pathak tetris project 
	   - https://github.com/AmanPathak-DevOps/End-to-End-Kubernetes-DevSecOps-Tetris-Project/tree/master
	   - https://blog.stackademic.com/devsecops-mastery-a-step-by-step-guide-to-deploying-tetris-on-aws-eks-with-jenkins-and-argocd-3adcf21b3120
## 12. pct pass in sonal qube -- 70pct
## 24. HOW TO CREATE HELM CHART
	   - https://www.youtube.com/watch?v=jUYNS90nq8U

## 13 EKS Disaster Recovery (DR) – Comprehensive Interview Guide
> **YouTube Reference:** [Disaster Failover Plan – EKS DR](https://www.youtube.com/watch?v=OmASCUJEVy8)

*You are a Principal AWS Solutions Architect with 12+ years of hands-on experience running production EKS clusters for large enterprises. I am preparing for a senior DevOps / SRE / Cloud Architect interview.*

Alright, let's get you prepared. As a Principal SA who's been in the trenches, I can tell you that EKS DR is a frequent and insightful topic. Interviewers want to see you think beyond just "backing up a cluster" and consider the entire application stack.

Here's your comprehensive, interview-ready guide for EKS Disaster Recovery as of March 2026.

---

## Core Concepts

### What “DR” actually means for an EKS cluster

Disaster Recovery (DR) for an Amazon EKS cluster is about ensuring your applications remain available and your data is recoverable in the event of a catastrophic failure. This typically means an outage that extends beyond a single Availability Zone, potentially impacting an entire AWS Region, or even a critical human error leading to a cluster's destruction.

It's crucial to understand that DR for EKS is **not just about the Kubernetes control plane**. The EKS control plane itself is highly available (multi-AZ) within a region. DR focuses on:

- **Application Data:** The most critical component. This includes data in Persistent Volumes (PVCs), databases (RDS, Aurora, DynamoDB), S3 buckets, etc. Without application data, restoring the cluster is often useless.
- **Kubernetes Application State:** The configuration of your workloads (Deployments, StatefulSets, Services, ConfigMaps, Secrets, Ingresses, Custom Resources). This defines how your applications run.
- **EKS Worker Nodes:** The compute capacity where your pods run.
- **Associated Infrastructure:** Load balancers (ALB/NLB), networking (VPC, Security Groups), DNS records.

DR is distinct from **High Availability (HA)**. HA is typically about resilience within a region (e.g., running pods across multiple AZs). DR is about recovering from a regional failure or a significant data loss event, usually involving a second, separate region.

---

### Typical RTO/RPO targets that interviewers expect

When discussing RTO/RPO, always emphasize that these are **business-driven requirements** that dictate the technical architecture.

- **RTO (Recovery Time Objective):** The maximum acceptable delay between the time of a disaster and the restoration of business services.
- **RPO (Recovery Point Objective):** The maximum acceptable amount of data loss, measured in time, that can be tolerated during a disaster.

Typical ranges, categorized by application criticality:

| Tier | Application Type | RPO | RTO | Common Strategy |
|------|------------------|-----|-----|------------------|
| **Tier 0/1** | Mission-Critical (core banking, payment systems, trading platforms) | < 15 minutes (often near-zero) | < 1 hour (aiming for minutes) | Active-Active or Hot Standby |
| **Tier 2** | Important (e-commerce, internal CRM, analytics dashboards) | < 1–4 hours | < 2–4 hours | Warm Standby or Pilot Light |
| **Tier 3** | Non-Critical (dev environments, internal tooling, static sites) | < 24 hours | < 8–24 hours | Backup and Restore (Cold Standby) |

Interviewers want to see that you understand the trade-offs: **tighter RTO/RPO = higher cost, higher complexity**.

---

## Modern DR Approaches in 2026

### How DR for EKS is typically done today (real-world patterns used by most companies)

The most crucial paradigm shift for modern EKS DR is the **"Application-First" approach**, heavily leveraging GitOps:

- **Application Data Replication is Paramount:** Your EKS cluster hosts applications, but the real "state" often lives in databases (RDS, Aurora, DynamoDB), object storage (S3), or shared file systems (EFS, FSx). The DR strategy for these data stores often dictates the overall application DR strategy. You can restore an EKS cluster quickly, but if your database has hours of data loss or takes hours to restore, your RTO is limited by the database.

- **Infrastructure as Code (IaC) and GitOps:** This is foundational.
  - **IaC (Terraform/CloudFormation):** Your entire EKS cluster (VPC, subnets, EKS control plane, node groups, IAM roles) is defined as code. This allows rapid provisioning of a new cluster in a DR region.
  - **GitOps (FluxCD/ArgoCD):** Your application manifests (Deployments, Services, ConfigMaps, Secrets, Custom Resources) are stored in a Git repository. A GitOps controller in your EKS cluster continuously synchronizes the cluster state with the Git repo. This is critical for DR, as it means your application state can be rapidly deployed to a new or standby cluster with a few Git pushes.

---

## Common EKS DR Patterns

### 1. Backup and Restore (Cold Standby / Cold DR)

**Description:** The simplest and least expensive. A replica of your EKS cluster is not actively running in the DR region. In a disaster, you provision a new EKS cluster from IaC, restore Kubernetes resources and application data from backups.

**EKS Specifics:**
- **Kubernetes Resources:** Use AWS Backup for EKS (recommended) or Velero. Backups are stored in S3 and replicated cross-region.
- **Persistent Volumes (PVCs):** AWS Backup for EKS or Velero with CSI snapshots can back up and restore EBS volumes (or other CSI-provisioned volumes) cross-region.
- **Databases:** Point-in-time recovery from backups, or cross-region snapshots for RDS/Aurora.
- **Traffic Management:** Manual DNS update to a new ingress.

**RPO/RTO:** Highest (RPO: hours, RTO: many hours to a day).

---

### 2. Pilot Light (Warm Standby)

**Description:** A minimal, scaled-down version of your EKS environment exists in the DR region. Core EKS components are running, worker nodes are at minimum scale (e.g., 0 or 1), but applications are not actively serving traffic. Data is actively replicated.

**EKS Specifics:**
- **EKS Cluster:** Provisioned via IaC in both regions. DR cluster has an EKS control plane and minimal worker nodes.
- **Applications:** Application manifests are deployed via GitOps to the DR cluster, but pods may be scaled to zero replicas or configured not to serve traffic.
- **Data Replication:** Crucial. RDS Read Replicas (cross-region), Aurora Global Database, DynamoDB Global Tables, S3 Cross-Region Replication, or cross-region snapshots for block storage.
- **Traffic Management:** DNS failover (e.g., Route 53 health checks) points traffic to the DR region's ingress when activated.

**RPO/RTO:** Moderate (RPO: minutes to 1 hour, RTO: 30 minutes to a few hours).

---

### 3. Warm Standby (Active-Passive / Hot Standby)

**Description:** A fully provisioned, running EKS cluster in the DR region, often at a scale capable of handling full load, but not actively serving traffic (or serving minimal/test traffic). Data is actively replicated.

**EKS Specifics:**
- **EKS Cluster:** Fully provisioned, similar to primary, via IaC. Worker nodes are scaled to handle expected DR load.
- **Applications:** All applications deployed via GitOps, running and ready to serve traffic.
- **Data Replication:** Same as Pilot Light, but often with stricter RPO requirements (e.g., synchronous or near-synchronous replication for critical data).
- **Traffic Management:** Automated DNS failover (e.g., Route 53 failover routing, AWS Global Accelerator) to instantly shift traffic.

**RPO/RTO:** Good (RPO: minutes, RTO: minutes to 1 hour).

---

### 4. Multi-Site Active-Active (Hot-Hot)

**Description:** Two or more fully operational EKS clusters in different regions, both actively serving traffic simultaneously. Requires highly stateless applications or sophisticated distributed data consistency mechanisms.

**EKS Specifics:**
- **EKS Cluster:** Full, independent clusters in each region, managed via IaC and GitOps.
- **Applications:** Must be designed for multi-region active-active; stateless microservices are ideal. Stateful applications require complex data synchronization.
- **Data Replication:** Near-real-time synchronous or eventually consistent replication. DynamoDB Global Tables, Aurora Global Database, custom multi-region database solutions. S3 Multi-Region Access Points for shared objects.
- **Traffic Management:** Global Load Balancers (Route 53 with latency/weighted routing, AWS Global Accelerator) distribute traffic across regions.

**RPO/RTO:** Best (Near-zero RPO, Near-zero RTO).

---

## Which strategy is most popular right now and why (include cost, complexity, RTO/RPO)

The most popular and pragmatic choice for critical-to-important applications (Tier 1/2) right now is **Pilot Light (Warm Standby)**, evolving towards **Warm Standby (Active-Passive)** for highly critical applications.

**Why Pilot Light / Warm Standby is popular:**

- **Cost-Effectiveness:** While more expensive than cold backup/restore, it's significantly cheaper than active-active, as you're not paying for full-scale compute resources in the DR region 24/7. You pay for the data replication and the minimal running infrastructure.
- **Achievable RTO/RPO:** It delivers RPO in the order of minutes (due to continuous data replication) and RTO in the order of 30 minutes to 2 hours for many applications. This generally satisfies most business requirements without extreme cost.
- **Manageable Complexity:** It avoids the immense complexity of active-active data consistency issues while still leveraging modern automation (IaC, GitOps) for quick activation. Data replication often relies on well-understood AWS managed services (RDS, Aurora, DynamoDB).
- **Focus on Data:** This strategy correctly emphasizes that data replication is the hardest part of DR. Once data is replicated, activating the EKS application layer is a relatively automated process.

Backup & Restore remains popular for non-critical systems or as a lowest-cost safety net. Active-Active is aspirational and truly implemented only for specific, highly distributed, or extremely high-value SaaS platforms where the business impact justifies the immense cost and engineering effort.

---

## New/recent AWS features (2024–2026) that changed EKS DR

The landscape for EKS DR has significantly matured and improved with recent AWS services and broader industry adoption of cloud-native practices:

- **AWS Backup for EKS (Maturing):**  
  *Impact:* This is a game-changer for standardizing backup/restore. It provides a centralized, policy-driven service to backup EKS persistent volumes (PVs/PVCs) and Kubernetes resources (control plane objects) into immutable, versioned backup vaults. It supports cross-region and cross-account replication natively.  
  *DR Benefit:* Simplifies the "Backup and Restore" strategy immensely, reducing operational overhead and improving reliability for cold DR. It can also act as a strong component for restoring application state in pilot light/warm standby scenarios if a cluster-level restore is needed.

- **AWS Cloud Development Kit (CDK) / Terraform Cloud/Enterprise:**  
  *Impact:* While not EKS-specific, the continuous advancements in IaC tooling and cloud orchestration platforms mean creating and managing identical EKS clusters across regions (for pilot light/warm standby) is more robust and automated than ever. Terraform Cloud/Enterprise and CDK's support for multi-account/multi-region deployments streamline DR environment provisioning.

- **Amazon Aurora Global Database Enhancements:**  
  *Impact:* Provides near-real-time replication across AWS regions (typically < 1-second lag). This dramatically reduces RPO for critical relational databases, making active-passive and even some active-active scenarios much more feasible for the data layer. Fast regional failover (< 1 minute RTO for data plane) is critical.

- **Amazon S3 Multi-Region Access Points (MRAP):**  
  *Impact:* Simplifies global access to data stored in S3 buckets across multiple regions. Applications can use a single endpoint to access data, and S3 handles the routing to the nearest or primary bucket. In a DR scenario, this simplifies application logic and ensures data availability even if a regional bucket is impaired.

- **EKS Anywhere (Hybrid DR Considerations):**  
  *Impact:* While primarily for hybrid cloud, EKS Anywhere offers consistent Kubernetes environments on-premises. This could be leveraged for DR scenarios where an on-premises application's DR target is an EKS cluster in AWS, or vice versa, providing a consistent operational model. (Less relevant for pure cloud-to-cloud DR, but important for some enterprises.)

- **GitOps Maturity (FluxCD, ArgoCD):**  
  *Impact:* The widespread adoption and maturity of GitOps tools are perhaps the most significant enablers for EKS DR. By externalizing all Kubernetes and application configurations to Git, spinning up a new cluster and re-deploying applications is an automated, repeatable, and auditable process. This drastically reduces the RTO associated with application deployment. Configuration drift is minimized.

---

## Diagram Descriptions

### Multi-region Velero DR flow

This diagram illustrates a cold/pilot light DR approach using Velero for EKS cluster state and Persistent Volume (PV) data, combined with separate data replication for application data (like databases).

```text
PRIMARY REGION                                          DR REGION
===============                                         =========

+-------------------+                                   +-------------------+
| EKS Cluster       |                                   | EKS Cluster       |
| - Applications    |                                   | (Pilot Light      |
| - Persistent      |                                   |   / Cold Standby) |
|   Volumes (EBS)   |                                   | - Minimal Workers |
| - K8s API Server  |                                   | - No Applications |
+---------+---------+                                   +---------+---------+
          | 1. Velero backs up                          | 3. Velero restores
          |    - K8s objects (Deployments, Services, ConfigMaps, Secrets)    |
          |    - Persistent Volume Claims (PVCs data via CSI snapshots/restic) |
          v                                             |
+-------------------+                                   +-------------------+
| Velero Server     |                                   | Velero Server     |
| (running in EKS)  |                                   | (running in EKS)  |
+---------+---------+                                   +---------+---------+
          | Backup                                                ^ Restore
          | (S3 API)                                              | (S3 API)
          v                                                       |
+-----------------------------------------------------------------------------------+
| AWS S3 - Velero Backup Bucket                                                     |
|   +----------------------------------------------------------------------------+  |
|   | Primary Region S3 Bucket                   Cross-Region Replication      |  |
|   | (Contains K8s manifests, PV data) -------------------------------------> |  |
|   +----------------------------------------------------------------------------+  |
|                                                                                   |
|   +----------------------------------------------------------------------------+  |
|   | DR Region S3 Bucket                                                      |  |
|   | (Replicated K8s manifests, PV data)                                      |  |
|   +----------------------------------------------------------------------------+  |
+-----------------------------------------------------------------------------------+

+-----------------------------------------------------------------------------------+
| SEPARATE APPLICATION DATA REPLICATION (CRITICAL FOR RPO)                          |
|   +-------------------+              Active/Passive Replication             +-------------------+
|   | Primary Database  | -------------------------------------------------> | DR Database       |
|   | (e.g., Aurora     |                                                    | (e.g., Aurora     |
|   |  Global Database, |                                                    |  Global Read      |
|   |  RDS Read Replica)|                                                    |  Replica/Standby) |
|   +-------------------+                                                    +-------------------+
+-----------------------------------------------------------------------------------+
DR Event Trigger Steps:

Detect disaster in Primary Region.

Activate DR EKS cluster (scale up worker nodes, update IaC for new cluster if Cold Standby).

Point applications in DR EKS to replicated data stores.

Initiate Velero restore from DR S3 bucket (restores K8s configuration and PVs).

Update DNS to point to DR Region's Ingress/Load Balancer.

Active-passive EKS architecture
This diagram illustrates a Warm Standby or Active-Passive architecture, where both regions have EKS clusters and continuously replicated data, enabling rapid failover.


GLOBAL DNS / ROUTE 53
         |
         v
+-------------------------------------------------------------+
| AWS Global Traffic Management                               |
| (e.g., Route 53 with Failover Routing, AWS Global Accelerator) |
+-------------------------------------------------------------+
          | (Primary directs traffic)    | (DR is standby)
          |                              |
+-----------------+             +-----------------+
| PRIMARY REGION  |             | DR REGION       |
| (Active)        |             | (Passive / Warm)|
+-----------------+             +-----------------+
|                 |             |                 |
| +-------------+ |             | +-------------+ |
| | EKS Cluster | |             | | EKS Cluster | |
| | (Full scale)|<--------------| | (Reduced     | |
| | - Apps Running| |   GitOps  | |   scale/Warm | |
| | - K8s API     | |  Sync of  | |   Standby)   | |
| | - Ingress     | |  K8s Config| | - Apps Ready | |
| +-------------+ |             | | - K8s API    | |
|       |         |             | | - Ingress    | |
|       |         |             | +-------------+ |
|       v         |             |       ^         |
| +-------------+ |             |       |         |
| | Application |<---------------| | Application | |
| | Data Store  | |             | | Data Store  | |
| | (e.g., Aurora| ------------> | | (e.g., Aurora| |
| |  Global DB   | | Async/Sync  | |  Global DB   | |
| |  Primary)   | |   Data      | |  Replica)    | |
| +-------------+ | Replication | +-------------+ |
|                 |             |                 |
+-----------------+             +-----------------+
Key Characteristics:

Identical EKS clusters: Fully provisioned in both regions via IaC. DR cluster often scaled down to save cost.

GitOps: All application manifests (Deployments, Services, ConfigMaps, Secrets) are synchronized from a Git repository to both EKS clusters, ensuring they are always ready.

Active Data Replication: Data stores (databases, S3, file systems) are continuously replicated cross-region (e.g., Aurora Global Database, RDS Read Replicas, DynamoDB Global Tables). This is the foundation for low RPO.

Global Traffic Management: DNS (Route 53) or Global Accelerator directs traffic. Health checks monitor the primary region; on failure, traffic is automatically routed to the DR region.

Fast RTO: Once traffic is redirected, applications in the DR region are already running or can scale up very quickly due to pre-warmed infrastructure and synchronized application state.

```

## 14. kubernetes upgrade
	```text
	https://docs.aws.amazon.com/eks/latest/userguide/update-cluster.html
	https://www.youtube.com/watch?v=Cfznp8jRh7I&t=5s
	Regular upgrades are necessary due to the frequent release of new Kubernetes versions (every three months).
	Prerequisites for Cluster Upgrade
		Cordon Nodes: Make nodes unschedulable to prevent new deployments during the upgrade.
		Review Release Notes: Understand changes (e.g., API deprecations) to avoid service disruptions.
		Testing: Conduct upgrades in lower environments (development, staging) before production.
		Control Plane and Nodes Version Compatibility: Ensure both are on the same version before upgrading.
		Cluster Autoscaler Compatibility: Match the cluster autoscaler version with the control plane version.
		IP Address Availability: Maintain at least five available IP addresses in the subnet for the upgrade.
		Kubelet Version Sync: Ensure the kubelet version matches the control plane version.
	Upgrade Process Overview
		Upgrade Control Plane: Requires manual intervention via AWS UI, eksctl, or AWS CLI.
		Upgrade Node Group: Upgrade nodes individually or through managed node groups using a rollout approach.
		Upgrade Add-ons: Upgrade additional components like kube-proxy or VPC CNI.
	Node Upgrade Approaches
		Managed Node Groups: AWS handles scaling and upgrades automatically.
		Custom Nodes: Requires manual intervention for each node upgrade.
		Hybrid Approach: Combination of managed and custom nodes.
	Considerations During Upgrade
		Test Kubernetes controllers (Helm, Argo CD, Flux, Prometheus) in lower environments for compatibility post-upgrade.
	Preparation Before Upgrade
		Understand installed controllers, available add-ons, and networking configurations.
		Review release notes for potential impacts on the cluster.
	Using EKS Dashboard
		Check "Upgrade Insights" in the EKS dashboard for compatibility issues with add-ons.
		Execute the upgrade command:
		eksctl upgrade cluster --name <cluster-name> --region <region> --version <new-version> --approve
		The control plane upgrade may take approximately 25 minutes.
	Post-Control Plane Upgrade
		Confirm the updated version in the AWS dashboard.
		Further actions are required for compute resources and add-ons.
	Upgrading Node Groups
		Check the current AMI version; update if using the default Amazon-provided AMI.
		For custom AMIs, manual upgrades are necessary.
		Option to create a new node group with the updated version for scaling.
	Rolling Updates
		Utilize rolling updates to achieve zero downtime during the upgrade process.
	Considerations for Upgrading
		Ensure launch templates and AMIs are compatible with the upgraded version.
		Users are responsible for upgrading custom launch templates or AMIs.
	Add-ons Upgrade
		Update add-ons (e.g., AWS VPC CNI) to the latest or compatible version after upgrading nodes.
	Testing the Upgrade
		Verify the upgrade by running functional or end-to-end tests to ensure all components work seamlessly.

	Upgrading Kubernetes clusters is a critical task for maintaining system integrity and performance. The process involves careful preparation, including reviewing release notes, ensuring version compatibility, and testing in lower environments. The upgrade process consists of three main steps: upgrading the control plane, upgrading node groups, and upgrading add-ons. Utilizing rolling updates can help achieve zero downtime. Understanding the nuances of the upgrade process, including managing custom nodes and add-ons, is essential for DevOps and Cloud Engineers. Following the outlined steps and prerequisites will facilitate a smoother upgrade experience, making this knowledge valuable for technical professionals and interviews. Additional resources, such as AWS documentation, are available for further guidance.
```
## 5. rbac  control tower  

## 14. cost reduction in cloud
## 15. blue green and canary deployment in eks
## 16. security and code quality
## 17. carpenter and argocd 
## 18. agentic ai devops 
## 19. check cloudthat onenote
## 20. anisible terraform (stack and depenency output variables in terraform)
## 21. aws well architecture frameworks
## 22. aws cross account questions like ec2 made account a how can that access ec2 made by account b, or s3 cross account roles

 
## 23. considering latest itil 4 and PM best practices how is incident and problem management  -- done
## 24. considering latest itil 4 and PM best practices how is costing and resourcing  -- done
## 25. devops management  -- done
## 26. lead roles  -- done
## 27. major incident in production  -- done
 
