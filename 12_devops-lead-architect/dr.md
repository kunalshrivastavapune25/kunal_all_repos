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

## 14. kubernetes upgrade

- GitHub: https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/03_IAC/dr%20and%20upgrade.md


---

## 15. rbac  control tower

 - https://www.youtube.com/watch?v=ECTxTONWgw8&t=466s

### Authentication and Authorization
Accessing Kubernetes resources requires both authentication and authorization:
- **Authentication**: Validates the user’s identity. A successful authentication returns a 200 response, while failure results in a 401 error.
- **Authorization**: Determines if the authenticated user can perform specific actions (e.g., create, update, delete). If unauthorized, a 403 error is returned.

#### Authorization Models
The primary models for authorization include:
- Role-Based Access Control (RBAC)
- Attribute-Based Access Control (ABAC)
- Node Authorization

RBAC is the most widely used model, allowing permissions based on user roles.

### Creating a User in Kubernetes
Kubernetes does not manage users directly; instead, it relies on external identity platforms. The steps to create a user include:
1. Generate a private key using OpenSSL.
2. Create a Certificate Signing Request (CSR).
3. Sign the CSR with a Certificate Authority (CA).
4. Add the user to the cluster using `kubectl`.
5. Create a context for the user.

#### Example Commands
```bash
# Generate private key
openssl genrsa -out user.key 2048

# Create CSR
openssl req -new -key user.key -out user.csr -subj "/CN=Pawan/O=example.org"

# Sign the CSR
openssl x509 -req -in user.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out user.crt -days 365

# Add user to cluster
kubectl config set-credentials Pawan --client-certificate=user.crt --client-key=user.key

# Create context
kubectl config set-context Pawan-context --cluster=minikube --user=Pawan --namespace=default
```

### Role and Role Binding
To grant permissions, roles and role bindings are created:
- **Role**: Defines permissions for a specific namespace.
- **Role Binding**: Connects a user to a role.

#### Example Role and Role Binding
```yaml
# Role
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]

# Role Binding
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: Pawan
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

#### Applying the Role and Role Binding
```bash
kubectl apply -f role.yaml
kubectl apply -f rolebinding.yaml
```

### Cluster Roles and Cluster Role Bindings
For broader access across namespaces, cluster roles and bindings are used:
- **Cluster Role**: Similar to a role but applies at the cluster level.
- **Cluster Role Binding**: Connects a user to a cluster role.

#### Example Cluster Role and Binding
```yaml
# Cluster Role
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]

# Cluster Role Binding
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-pods
subjects:
- kind: User
  name: Pawan
roleRef:
  kind: ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

#### Applying the Cluster Role and Binding
```bash
kubectl apply -f clusterrole.yaml
kubectl apply -f clusterrolebinding.yaml
```

### Service Accounts
Service accounts are special users created in each namespace for applications to access Kubernetes resources.

#### Creating a Service Account
```bash
kubectl create serviceaccount my-service-account
```

#### Using a Service Account in a Pod
Specify the service account in the pod definition:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: my-service-account
  containers:
  - name: my-container
    image: my-image
```

### Conclusion
This chapter covered managing access to Kubernetes resources using RBAC, including user creation, roles, role bindings, cluster roles, and service accounts. As a practical exercise, users are encouraged to create multiple users, attach them to groups, and test their access to resources. 

#### Summary of Kubernetes Service Account Permissions
1. **Context Update**: Applied `kubectl` commands to update role bindings.
2. **Listing Pods**: Verified pod listing using the service account.
3. **Testing Permissions**: Used `kubectl auth can-i create pods` to check permissions.
4. **Impersonating Service Account**: Tested permissions for a specific service account.
5. **Checking Pod Access**: Confirmed retrieval of pods using the service account.

---

## 16. cost reduction in cloud
### Compute
- Rightsize with AWS Compute Optimizer; switch to Graviton3 where suitable.
- Savings Plans (Compute) and Reserved Instances for steady workloads.
- Use Spot Instances with robust Auto Scaling or Spot Fleet.
- Leverage EC2 Hibernate where supported; schedule on/off for non-prod.

### Storage and Data Management
- S3: enable Lifecycle policies and move infrequently accessed data to Intelligent-Tiering/Glacier.
- Rightsize EBS volumes; delete unattached volumes; keep snapshots lean.
- Minimize data transfer: keep resources in same region, use VPC endpoints, and CloudFront for egress.

### Databases and Serverless
- Aurora Serverless v2 or provisioned with autoscaling; DynamoDB on-demand vs provisioned with auto-scaling.
- Choose managed serverless where workloads are irregular; enable automatic backups with lifecycle rules.

### Networking and Operations
- Use CloudFront and caching to reduce origin load; minimize cross-region transfers.
- Implement budgets, alerts, and cost reporting with Cost Explorer and Trusted Advisor checks.

### Governance and Automation
- Tagging strategy for cost allocation; enforce via automation.
- Regular automated cost-optimization checks (Compute Optimizer, anomaly detection) with alerts.

### Agentic AI for Cost Optimization (Autonomous Cost Agents)
- Concept: AI agents that monitor usage and costs and take safe, policy-driven actions.
- How it works:
  - Data sources: Cost Explorer API, CloudWatch metrics, Compute Optimizer, Trusted Advisor.
  - Orchestrator: AWS Step Functions or Lambda + Systems Manager Automation for action workflows.
  - Actions (with safeguards): auto-suspend idle dev/test resources on off-hours; auto-rightsize workloads with guardrails; propose or auto-commit to Savings Plans for predictable workloads under governance.
- Implementation tips:
  - Start with non-prod environments: schedule idle shutdowns and rightsizing recommendations.
  - Use least-privilege IAM roles, approval gates, and full audit logs.
  - Deploy in small, testable loops; monitor results before broader rollout.

Notes
- Always test changes in a staging environment.
- Revisit every 3-6 months as AWS introduces new cost-saving options.

---

## 17. blue green and canary deployment in eks

**What is a Deployment Strategy?**
A deployment strategy is essential for managing application updates in Kubernetes. Many users mistakenly think they can simply uninstall an old version and install a new one, which can lead to significant downtime. This is particularly detrimental for organizations like Amazon or Instagram, where even a few minutes of downtime can result in revenue loss and a poor user experience.

---

**Why Use a Deployment Strategy?**
1. **Minimize Downtime**: Without a deployment strategy, switching versions can lead to service outages. For instance, if it takes 10 minutes to switch versions, your service is down for that duration.
   
2. **User Experience**: Downtime can frustrate users and damage an organization's reputation. A deployment strategy helps mitigate these risks.

---

**Overview of Deployment Strategies**

1. **Rolling Update**:
   - **Definition**: The default deployment strategy in Kubernetes, which gradually replaces instances of the old version with the new one.
   - **How It Works**: For example, if you have four replicas of an application, Kubernetes will incrementally replace them, ensuring that some instances of the old version remain available to handle traffic.
   - **Configuration**: Utilizes two parameters:
     - **Max Unavailable**: The maximum number of replicas that can be unavailable during the update (default is 25%).
     - **Max Surge**: The maximum number of replicas that can be created above the desired number (default is also 25%).
   - **Readiness Probe**: Essential for ensuring that traffic is only routed to healthy instances.

2. **Canary Deployment**:
   - **Definition**: Allows testing a new version of an application in production with a small subset of users before a full rollout.
   - **Implementation**: For example, route 90% of traffic to the old version and 10% to the new version.
   - **Advantages**: Reduces risk by limiting the number of affected users if issues arise, allows for extended testing, and enables easy rollback.

3. **Blue-Green Deployment**:
   - **Definition**: Involves maintaining two identical environments (Blue and Green), where one serves live traffic while the other is idle or used for staging the new version.
   - **Benefits**: Traffic can be switched from the old environment to the new one with minimal downtime.
   - **Challenges**: This approach can be costly due to the resource consumption of maintaining identical replicas for both versions.

---

**Comparison of Deployment Strategies**
- **Canary vs. Rolling Update**: 
  - **Canary Deployment** is preferred for high-stakes applications where user experience is critical, allowing for gradual testing and easy rollback.
  - **Rolling Update** is suitable for simpler applications where some downtime is acceptable and rapid deployment is necessary.

- **Blue-Green Deployment**: While effective for industries like banking where immediate rollback capabilities are critical, it can be resource-intensive, making it less attractive for other sectors.

---

**Load Balancer Configuration**
To implement a new version, update the Ingress resource to point the load balancer from Service One to Service Two. If issues arise, simply revert the configuration back to Service One for a quick rollback.

---

**Conclusion**
In summary, using a deployment strategy in Kubernetes is essential for minimizing downtime and ensuring a smooth user experience during application updates. Each strategy—Rolling Update, Canary, and Blue-Green—has its unique advantages and use cases. Understanding these strategies will help you effectively manage application deployments in your Kubernetes environment. Thank you for watching, and see you in the next video!


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
