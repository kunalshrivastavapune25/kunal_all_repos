# Interview Preparation Notes – DevOps / Platform Engineering

This repository contains my personal notes, talking points, and resources for preparing for a senior DevOps / Platform Engineering interview (focus on microservices migration, GitOps, Kubernetes, and AWS EKS). The content is organized for quick revision and includes real-world challenges, solutions, best practices, and references to courses, projects, and documentation.

---

## Table of Contents

- [1. Introduction / About Myself](#1-introduction--about-myself)
- [2. Best Practices & Daily / Weekly Work](#2-best-practices--daily--weekly-work)
- [3. Notable Fixes / Achievements](#3-notable-fixes--achievements)
- [4. Major Challenges Faced & Solutions](#4-major-challenges-faced--solutions)
- [5. Monitoring and Observability](#5-monitoring-and-observability)
- [6. Pod Troubleshooting](#6-pod-troubleshooting)
- [7. Handwritten Notes](#7-handwritten-notes)
- [8. Kubernetes App – Useful Links](#8-kubernetes-app--useful-links)
- [9. Veeramala Interview Questions](#9-veeramala-interview-questions)
- [10. Veeramala Udemy Project](#10-veeramala-udemy-project)
- [11. Aman Pathak Tetris Project](#11-aman-pathak-tetris-project)
- [12. SonarQube – 70% Passing](#12-sonarqube--70-passing)
- [13. RBAC / Control Tower](#13-rbac--control-tower)
- [14. EKS Disaster Recovery (DR)](#14-eks-disaster-recovery-dr)
- [15. Kubernetes Upgrade](#15-kubernetes-upgrade)
- [16. Cost Reduction in Cloud](#16-cost-reduction-in-cloud)
- [17. Blue Green and Canary Deployment in EKS](#17-blue-green-and-canary-deployment-in-eks)
- [18. Security and Code Quality](#18-security-and-code-quality)
- [19. Karpenter and ArgoCD](#19-karpenter-and-argocd)
- [20. Agentic AI DevOps](#20-agentic-ai-devops)
- [21. Check CloudThat OneNote](#21-check-cloudthat-onenote)
- [22. Ansible & Terraform (Stack, Dependencies, Output Variables)](#22-ansible--terraform-stack-dependencies-output-variables)
- [23. AWS Well-Architected Frameworks](#23-aws-well-architected-frameworks)
- [24. AWS Cross-Account Questions](#24-aws-cross-account-questions)
- [25. ITIL 4 & PM – Incident & Problem Management](#25-itil-4--pm--incident--problem-management)
- [26. ITIL 4 & PM – Costing and Resourcing](#26-itil-4--pm--costing-and-resourcing)
- [27. DevOps Management](#27-devops-management)
- [28. Lead Roles](#28-lead-roles)
- [29. Major Incident in Production](#29-major-incident-in-production)
- [30. How to Create a Helm Chart](#30-how-to-create-a-helm-chart)

---

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

---

## 2. Best Practices & Daily / Weekly Work

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

---

## 3. Notable Fixes / Achievements

**Problem:** ReplicaSets suddenly not creating new pods → pods stuck in Pending state  
**Root cause:** IP exhaustion in the subnet (EKS node/ENI limits reached)  
**Action taken:**  
- Identified subnet IP utilization spike  
- Worked with AWS / networking team (AM = Account Manager?) to increase secondary CIDR or subnet size  
- Added monitoring alerts for IP usage  
- Documented the issue & mitigation steps for the team

---

## 4. Major Challenges Faced & Solutions

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

---

## 5. Monitoring and Observability

- GitHub repo: [observability-zero-to-hero](https://github.com/kunalshrivastavapune25/observability-zero-to-hero)
- Udemy course reference: [Ultimate DevOps and Cloud Interview Guide](https://tcsglobal.udemy.com/course/ultimate-devops-and-cloud-interview-guide/learn/lecture/50809923#overview)

> **Personal note:**  
> JUST MAKE SURE U ADD A POINT IN UR INTRODUCTION THAT I MADE PROMQL FOR MY CUSTOM MONITORING  
> AND CHATGPT HOW IT IS DONE

> **Agentic AI idea:**  
> JUST CHECK IG THIS CAN BE DONE FROM AGENTIC AI I.E INCIDENT MANAGEMENT LIKE CREATING JIRA TICKETS AND ALL THROUGH MCPS  
> LIKE LETS SAY ALERT MANAGER SENDS SOME ALERT THEN E2E INCIDENT HANDLING CAN BE DONE FROM AGENTIC AI AUTOMATICALLY IN NO TIME  
> JUST GO TROUGH PAGERDUTY ALL GYAN SAME THING THINK OF IMPLEMENTING IN AGENTIC AI

---

## 6. Pod Troubleshooting

- Udemy lecture: [Troubleshooting Pods](https://tcsglobal.udemy.com/course/ultimate-devops-and-cloud-interview-guide/learn/lecture/50809923#overview)

---

## 7. Handwritten Notes

Location: `C:\kunal\LINKEDIN IMAGES\` (or local path) – scan/photos of handwritten Kubernetes/DevOps notes.

---

## 8. Kubernetes App – Useful Links

- [Ahmed Ali Butt LinkedIn](https://www.linkedin.com/in/ahmedalibutt/)
- [LinkedIn Post – Kubernetes App](https://www.linkedin.com/feed/update/urn:li:activity:7433489248076374016/)
- Local images: `C:\kunal\LINKEDIN IMAGES\`

---

## 9. Veeramala Interview Questions

- [Udemy – Ultimate DevOps and Cloud Interview Guide](https://tcsglobal.udemy.com/course/ultimate-devops-and-cloud-interview-guide/learn/lecture/50809923#overview)

---

## 10. Veeramala Udemy Project

- [Ultimate DevOps Project with Resume Preparation](https://tcsglobal.udemy.com/course/ultimate-devops-project-with-resume-preparation/learn/lecture/48207855?start=15#overview)
- GitHub: [ultimate-devops-project-demo](https://github.com/iam-veeramalla/ultimate-devops-project-demo)
- GitHub: [ultimate-devops-project-aws](https://github.com/iam-veeramalla/ultimate-devops-project-aws)

---

## 11. Aman Pathak Tetris Project

- GitHub: [End-to-End Kubernetes DevSecOps Tetris Project](https://github.com/AmanPathak-DevOps/End-to-End-Kubernetes-DevSecOps-Tetris-Project/tree/master)
- Blog: [DevSecOps Mastery – Deploy Tetris on EKS with Jenkins & ArgoCD](https://blog.stackademic.com/devsecops-mastery-a-step-by-step-guide-to-deploying-tetris-on-aws-eks-with-jenkins-and-argocd-3adcf21b3120)

---

## 12. SonarQube – 70% Passing

Aim to achieve at least 70% quality gate passing in SonarQube scans.

---

## 13. RBAC / Control Tower

Placeholder for notes on AWS RBAC, IAM, and AWS Control Tower.

---

## 14. EKS Disaster Recovery (DR)

*Comprehensive guide from a Principal AWS Solutions Architect (simulated).*

### Core Concepts

**What “DR” actually means for an EKS cluster**  
Disaster Recovery (DR) for an Amazon EKS cluster is about ensuring your applications remain available and your data is recoverable in the event of a catastrophic failure (beyond a single AZ, possibly entire region). DR focuses on:

- Application Data (databases, persistent volumes, S3)
- Kubernetes Application State (Deployments, Services, ConfigMaps, etc.)
- EKS Worker Nodes
- Associated Infrastructure (load balancers, VPC, DNS)

DR is distinct from High Availability (HA). HA is within a region; DR is cross-region recovery.

**Typical RTO/RPO targets** (business-driven):

- **Tier 0/1 (Mission-Critical):** RPO < 15 min, RTO < 1 hour → Active-Active or Hot Standby
- **Tier 2 (Important):** RPO 1-4 hours, RTO 2-4 hours → Warm Standby / Pilot Light
- **Tier 3 (Non-Critical):** RPO 24h, RTO 8-24h → Backup & Restore

### Modern DR Approaches in 2026

- **Application-First + GitOps:** IaC (Terraform/CloudFormation) + GitOps (ArgoCD/Flux) for rapid cluster recreation and app sync.
- **Data replication is paramount:** Use RDS cross-region replicas, Aurora Global Database, DynamoDB Global Tables, S3 CRR.

**Common Patterns:**

1. **Backup and Restore (Cold Standby)** – simplest, use AWS Backup for EKS or Velero.
2. **Pilot Light (Warm Standby)** – minimal DR cluster running, data replicated, apps scaled to zero.
3. **Warm Standby (Active-Passive)** – fully provisioned DR cluster, apps ready, data replicated.
4. **Multi-Site Active-Active** – both regions serve traffic, requires stateless apps or complex data sync.

**Most popular now:** Pilot Light / Warm Standby – balance of cost, RTO/RPO, and complexity.

**New AWS features (2024–2026) impacting DR:**
- AWS Backup for EKS (mature, policy-driven backup of PVs and K8s resources)
- CDK / Terraform Cloud for multi-region IaC
- Aurora Global Database enhancements (sub-second replication)
- S3 Multi-Region Access Points
- GitOps maturity (ArgoCD/Flux)

### Diagram Descriptions

- **Multi-region Velero DR flow** – shows backup of K8s objects and PVs to S3, cross-region replication, and restore in DR region, plus separate DB replication.
- **Active-passive EKS architecture** – both regions have EKS clusters, GitOps sync, data replication, global traffic management (Route53 failover).

---

## 15. Kubernetes Upgrade

**References:**
- [AWS EKS Update Cluster Docs](https://docs.aws.amazon.com/eks/latest/userguide/update-cluster.html)
- [YouTube: EKS Upgrade Guide](https://www.youtube.com/watch?v=Cfznp8jRh7I&t=5s)

**Key Points:**
- Regular upgrades needed (new K8s versions every 3 months).
- Prerequisites: cordon nodes, review release notes, test in lower environments, check version compatibility (control plane, kubelet, cluster autoscaler), ensure available IP addresses.
- Upgrade process: control plane (manual via AWS UI/eksctl) → node groups (managed or custom) → add-ons (kube-proxy, VPC CNI).
- Node upgrade approaches: managed node groups (AWS handles), custom nodes (manual), hybrid.
- Use rolling updates for zero downtime.
- Post-upgrade: test controllers (Helm, ArgoCD, Prometheus) and run functional tests.

---

## 16. Cost Reduction in Cloud

Placeholder for notes on cloud cost optimization strategies (e.g., right-sizing, spot instances, savings plans, resource cleanup).

---

## 17. Blue Green and Canary Deployment in EKS

Placeholder for strategies to implement blue/green and canary deployments on EKS using ArgoCD, Flagger, or AWS App Mesh.

---

## 18. Security and Code Quality

Placeholder for notes on security best practices (image scanning, pod security standards, network policies) and code quality tools (SonarQube, Snyk, etc.).

---

## 19. Karpenter and ArgoCD

Placeholder for notes on Karpenter (Kubernetes node autoscaler) and integration with ArgoCD for GitOps.

---

## 20. Agentic AI DevOps

Placeholder for exploring AI-driven incident management, automated Jira ticket creation via MCPS, and integration with PagerDuty.

---

## 21. Check CloudThat OneNote

Personal reference: notes stored in CloudThat OneNote.

---

## 22. Ansible & Terraform (Stack, Dependencies, Output Variables)

Placeholder for Terraform concepts: managing stack dependencies, using output variables, remote state, and Ansible integration.

---

## 23. AWS Well-Architected Frameworks

Placeholder for review of AWS Well-Architected pillars (Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, Sustainability).

---

## 24. AWS Cross-Account Questions

Common scenarios:
- EC2 in Account A accessing EC2 in Account B
- S3 cross-account access using IAM roles
- Cross-account VPC peering / Transit Gateway

---

## 25. ITIL 4 & PM – Incident & Problem Management

Placeholder for notes on ITIL 4 practices for incident, problem, and change management, aligned with project management (PM) best practices.

---

## 26. ITIL 4 & PM – Costing and Resourcing

Placeholder for notes on financial management, cost allocation, and resource planning in ITIL 4 context.

---

## 27. DevOps Management

Placeholder for notes on leading DevOps teams, culture, metrics (DORA), and continuous improvement.

---

## 28. Lead Roles

Placeholder for responsibilities and expectations of a DevOps Lead / Tech Lead role.

---

## 29. Major Incident in Production

Placeholder for handling major incidents: communication, root cause analysis, blameless postmortems, and preventive measures.

---

## 30. How to Create a Helm Chart

- [YouTube Tutorial: Helm Chart Creation](https://www.youtube.com/watch?v=jUYNS90nq8U)

---

> **Note:** This README is a compilation of personal study notes, interview preparation points, and resource links. Content is preserved as originally written, with minor reorganization for clarity.
