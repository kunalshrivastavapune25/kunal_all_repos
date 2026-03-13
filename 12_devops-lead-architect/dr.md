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
## 13 EKS Disaster Recovery (DR) – Comprehensive Interview Guide

- GitHub: https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/03_IAC/dr%20and%20upgrade.md

---

### 14. kubernetes upgrade

- GitHub: https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/03_IAC/dr%20and%20upgrade.md


---
## 16. Cost Reduction in Cloud
```text
2-Minute Interview Answer: Cloud Cost Optimization
Opening Statement
"Cloud cost optimization is critical because while cloud offers scalability and agility, uncontrolled usage can lead to massive bills—sometimes 30-40% of infrastructure spend goes to waste through idle resources, oversized instances, and poor architecture decisions."

7 Practical Techniques I've Used
1. Right-Sizing Resources

Use AWS Cost Explorer and Trusted Advisor to identify underutilized EC2 instances
Example: Downgraded t3.xlarge to t3.large for dev environments, saved 40% monthly
2. Reserved Instances & Savings Plans

Analyzed usage patterns and purchased 1-year RIs for predictable workloads
Achieved 30-50% savings on EC2 and RDS
3. S3 Lifecycle Policies

Implemented automatic tiering: Hot data on S3 Standard, 90-day old to S3-IA, archive to Glacier
Reduced storage costs by 60%
4. Auto-Scaling & Spot Instances

Configured EKS cluster with Cluster Autoscaler and Karpenter
Used Spot instances for non-critical batch jobs—saved 70% on compute
5. Eliminate Idle Resources

Scheduled Lambda functions to stop non-prod EC2/RDS instances after hours
Deleted unattached EBS volumes and old snapshots
6. Monitoring & Tagging Strategy

Enforced mandatory cost-center tags for chargeback
Set up CloudWatch billing alarms and budget alerts
7. Multi-Account Strategy with AWS Organizations

Consolidated billing and applied volume discounts across accounts
Two Scenarios
If Cost Tools Already Exist:
"I'd start by auditing existing dashboards in Cost Explorer, validate tagging compliance, review historical spending patterns, and prioritize quick wins like orphaned resources before tackling architectural changes."

If No Cost Governance:
"First, enable AWS Cost Explorer and Trusted Advisor. Implement a tagging policy immediately, set up budget alerts, and create a weekly cost review meeting with stakeholders. Start with low-hanging fruit: stop unused resources, then move to reserved capacity planning."

Real-World Example
"At my previous company, our monthly AWS bill was 180K. I implemented EKS right-sizing, moved 40% workloads to Spot, applied S3 lifecycle policies, and purchased RIs. Within 3 months, we reduced costs to 110K—38% savings—without impacting performance."

How Agentic AI Can Help
Modern Approach with AI: "Agentic AI systems can revolutionize cost optimization through autonomous decision-making. Using frameworks like LangChain, AutoGPT, or AWS Bedrock Agents, we can build intelligent agents that:

Continuously analyze Cost Explorer APIs and recommend optimizations in real-time
Use Model Context Protocol (MCP) to connect multiple data sources—billing, CloudWatch metrics, application logs
Automatically execute approved actions like scheduling shutdowns, modifying auto-scaling policies, or purchasing RIs
Predict future costs using machine learning on usage patterns
Chat-based interfaces where teams can ask 'Why did costs spike yesterday?' and get root-cause analysis instantly
For example, an AI agent built with LangGraph can create multi-step workflows: detect anomaly → analyze root cause → propose solution → get approval → execute—all while learning from past decisions. This shifts from reactive monthly reviews to proactive, real-time cost governance."

Total speaking time: ~2 minutes

This answer demonstrates technical depth, practical experience, business impact, and awareness of cutting-edge AI solutions—exactly what senior architect interviews look for.

```

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
