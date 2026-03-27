

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
- [31. AWS Organization , control towers landing zones](31-AWS-Organization-control-towers-landing-zones)
- [32. Migration](#32-Migration)
- [33. HIGHAVAILABLE](#33-HIGHAVAILABLE)
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
- Day to day work:
  - MLOPS -- Build Training data, obsevablity, cicd, madel registry, iac, cost 
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
- Here is a more professional and clear version of your statement:
- Observability and monitoring are mainly based on three pillars: **metrics, logs, and traces.**
- The **first pillar is metrics**. Metrics help us understand system performance such as CPU usage, memory usage, and other resource utilization. Tools like **Prometheus**, **Grafana**, and **Alertmanager** are commonly used to collect and visualize these metrics. In Kubernetes environments, these tools are usually installed using Helm charts.
- The **second pillar is logs**. Logs provide detailed records of what is happening inside applications and systems. A common setup is the **EFK Stack**, where **Kibana** helps to view and analyze raw logs generated by applications and infrastructure.
- The **third pillar is traces**. Traces help track requests as they move across multiple microservices, which is very useful in distributed systems. Tools like **Jaeger** are used for this purpose.
- To collect metrics, logs, and traces from applications, we typically use **OpenTelemetry** instrumentation in the microservices. This allows applications to send observability data to the monitoring tools. In Kubernetes, some logs are already generated by default by components like the kubelet and container runtime.


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
- https://www.youtube.com/watch?v=vGab4v3RWEw&list=PLdpzxOOAlwvIrFBI1farpLS_OSUBXJMLX&index=1
- https://www.youtube.com/watch?v=aEPIlQBWBGQ&list=PLdpzxOOAlwvIrFBI1farpLS_OSUBXJMLX&index=2
- https://www.youtube.com/watch?v=O61HDmGUBJM&list=PLdpzxOOAlwvIrFBI1farpLS_OSUBXJMLX&index=3
- https://www.youtube.com/watch?v=VytIsTdmgr4&list=PLdpzxOOAlwvIrFBI1farpLS_OSUBXJMLX&index=4
- https://www.youtube.com/watch?v=uBhjymTV0ro&list=PLdpzxOOAlwvIrFBI1farpLS_OSUBXJMLX&index=5
- https://www.youtube.com/watch?v=qvBHvD4oXMc&list=PLdpzxOOAlwvIrFBI1farpLS_OSUBXJMLX&index=6
<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/d99122d8-3bf3-45e6-b3be-77e4f4eb7705" />
<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/accc361f-cf8e-4e40-b44b-b0e0ceffbee7" />
<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/6a1241c4-6ae4-4f9d-800f-f33d89f9f10b" />
<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/12ba0c4e-cfc3-4f98-84dc-c8e06d579107" />


- https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/08_containerization/k8stroubleshooting.md
---

## 7. Handwritten Notes

Location: `C:\kunal\LINKEDIN IMAGES\` (or local path) – scan/photos of handwritten Kubernetes/DevOps notes.

<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/19ba6138-9385-4b5f-a46d-65fbdde15b3d" />

<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/563d1b32-9fe1-4d1d-8700-da62e0af4269" />

<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/8610a3de-a65d-442a-9659-1de9db2df95e" />

<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/8143240c-db8a-49fd-8749-d632e13bb7c1" />

<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/ca454f12-b0e7-4cf4-86c3-674b798adfe9" />

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
<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/767f87ca-19b7-43c6-82bb-0051c3c148e5" />

- https://opentelemetry.io/docs/demo/architecture/
- https://github.com/kunalshrivastavapune25/aws-eks-kubernetes-masterclass/tree/master/11-NEW-DevOps-with-AWS-Developer-Tools-and-GitHub
- <img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/e5be2994-ed0a-480b-92ad-8be7fa7647de" />

---

## 11. Aman Pathak Tetris Project

- GitHub: [End-to-End Kubernetes DevSecOps Tetris Project](https://github.com/AmanPathak-DevOps/End-to-End-Kubernetes-DevSecOps-Tetris-Project/tree/master)
- Blog: [DevSecOps Mastery – Deploy Tetris on EKS with Jenkins & ArgoCD](https://blog.stackademic.com/devsecops-mastery-a-step-by-step-guide-to-deploying-tetris-on-aws-eks-with-jenkins-and-argocd-3adcf21b3120)
- developer makes dockerfile using docker build command , then we test it by making container of same by docker deploy and in jenkins pipeline we have have docker buuild code that makes the image deploys and 
- test it then it going to push same to ecr then we update that image to deployment.yml, inside manifest folder then argocd will sync same to prod env, this image is also goes though sonarqube, owasp and trivy
- <img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/4cd3ff32-e28f-421e-8358-55f59431c3dd" />


---

## 12. SonarQube – 70% Passing

Aim to achieve at least 70% quality gate passing in SonarQube scans.

---

## 13. RBAC / Control Tower

Placeholder for notes on AWS RBAC, IAM, and AWS Control Tower , landing zone and organisations, cross account access and shared accounts,
additionally study about k8s rbac, role, role binding , users, service account 
https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/12_devops-lead-architect/readme.md

---
## 13 EKS Disaster Recovery (DR) and Upgrade – Comprehensive Interview Guide

- GitHub: https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/03_IAC/dr%20and%20upgrade.md

---

## 14. kubernetes upgrade

- GitHub: https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/03_IAC/dr%20and%20upgrade.md


---

## 15. rbac  control tower landing zone organization role binding service account

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
<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/b29df26d-4b45-4aa2-beda-14778b0fd7c4" />

<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/5d510117-9bff-4746-aab7-13b5d362645f" />

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
 - https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/13_devsecops-security/readme.md
#### Overview
- Goal: integrate security into every stage of the CI/CD lifecycle for Kubernetes workloads on EKS.
- Key enablers: identity with IRSA, image provenance, runtime security, policy-as-code, and end-to-end observability.
<img width="480" height="720" alt="image" src="https://github.com/user-attachments/assets/6e381747-08c9-4d79-9aa9-7331d70307be" />

#### Core Principles
- Shift-left security: scan and vet artifacts early.
- Least privilege: apply only necessary permissions at each layer.
- Policy as code: enforce compliance and guardrails automatically.
- Runtime protection: detect and respond to unusual behavior in production.
- Observability: collect logs, metrics, and traces to reveal risks quickly.

#### EKS-Specific Considerations
- IRSA (IAM Roles for Service Accounts) to give pods fine-grained AWS rights without long-lived keys.
- Private API endpoints and security groups for pod networking (SGs for pods).
- Encryption at rest for secrets (KMS) and encrypted etcd data if enabled.
- Image scanning via ECR and SBOMs for supply-chain transparency.
- Pod Security Standards (PSS) and policy-driven admission controls.

#### Best Practices

- Shift-Left Security
  - Integrate SBOM generation (e.g., Syft) and vulnerability scanning (Trivy, Snyk, Grype) in CI.
  - Block deployments with high-severity findings or license violations.
  - Pin and immutably tag images; enforce provenance checks.

- Secure Supply Chain
  - Enable Amazon ECR image scanning and enforce remediation gates.
  - Sign images (e.g., cosign) and validate provenance in CI/CD.
  - Use immutable infrastructure and pull policies to prevent drift.

- Identity and Access Management
  - Use IRSA for all pod-to-AWS resource access.
  - Apply least privilege to IAM roles and Kubernetes RBAC; separate duties by namespace.
  - Regularly rotate credentials and audit IAM activity.

- Secrets and Configuration
  - Store secrets in AWS Secrets Manager or Parameter Store; fetch at runtime.
  - Avoid secrets in Kubernetes volumes or environment variables.
  - Encrypt etcd data where supported; use KMS-backed encryption providers.

- Network and Pod Hardening
  - Enforce Kubernetes Network Policies to limit pod communication.
  - Use a service mesh (Istio or Linkerd) for mTLS and policy enforcement.
  - Enable Pod Security Standards to prevent dangerous capabilities and privilege escalation.

- Run-time Security
  - Deploy Falco or similar runtime security to detect anomalous container behavior.
  - Monitor for abnormal process activity, file changes, and outgoing connections.

- Observability and Incident Response
  - Centralize logs (CloudWatch), metrics (Prometheus), and traces (Jaeger/OpenTelemetry).
  - Establish runbooks and alerting for common EKS security incidents.
  - Regularly test incident response and disaster recovery drills.

- Compliance and Governance
  - Treat security policies as code (OPA or Kyverno) and enforce at admission.
  - Maintain an SBOM, vulnerability posture, and audit trails for audits.

#### Popular Tools (by category)

- CI/CD and Source Control
  - GitHub Actions, GitLab CI, AWS CodePipeline
  - GitHub or GitLab for source control

- Image Scanning and SBOM
  - Trivy, Grype, Snyk, Anchore
  - Syft (SBOM) and SPDX

- Registry and Provenance
  - Amazon ECR (with image scanning)
  - Cosign for signing and verification

- Policy as Code / Admission
  - Kyverno, OPA (Gatekeeper)
  - Pod Security Standards (PSS)

- Runtime and Network Security
  - Falco (runtime detection)
  - Istio or Linkerd (service mesh)
  - Network Policies (Kubernetes)

- Secrets and Identity
  - AWS Secrets Manager, AWS SSM Parameter Store
  - IAM Roles for Service Accounts (IRSA)

- Observability and Telemetry
  - CloudWatch, Prometheus, Grafana
  - OpenTelemetry, Jaeger, Loki

- Compliance and Governance
  - AWS Security Hub, AWS Config
  - CloudTrail for audit logging

- Backup / DR
  - Velero (backup/restore for Kubernetes)

#### Quick Start Checklist
- Create a private EKS cluster; enable IRSA and private API endpoints.
- Enable encryption at rest for secrets (KMS) and, if applicable, etcd.
- Enable ECR image scanning and set up a gate for remediation.
- Install policy-as-code (Kyverno or OPA) and set admission controls.
- Implement runtime security (Falco) and a service mesh (optional but recommended).
- Enforce network policies and Pod Security Standards.
- Centralize logs/metrics and establish alerting for security events.


---

## 19. Karpenter and ArgoCD
- https://aws.amazon.com/blogs/big-data/use-karpenter-to-speed-up-amazon-emr-on-eks-autoscaling/
- https://blog.searce.com/argocd-gitops-continuous-delivery-approach-on-google-kubernetes-engine-2a6b3f6813c0
#### Table of contents
- What they are (at a glance)
- Prerequisites
- Karpenter: install and configure
- ArgoCD: install and configure
- Using them together (GitOps for Provisioners)
- Validation and quick checks
- References

---

#### What they are (at a glance)

- Karpenter
  - A Kubernetes cluster autoscaler that provisions compute on-demand from your cloud provider.
  - Responds to workloads by creating/destroying nodes, based on actual workloads and policies.

- ArgoCD
  - A GitOps CD tool for Kubernetes.
  - Keeps your cluster desired-state in sync with a Git repository, via Applications, Projects, and automated sync policies.

---

#### Prerequisites

- A Kubernetes cluster (EKS, AKS, GKE, etc.) with kubectl access.
- Administrative access to install cluster components.
- Cloud-provider credentials set up for Karpenter (permissions to create VMs/instances, subnets, security groups, etc.).
- A Git repository for ArgoCD Applications (or plan to point ArgoCD at one).
- Optional but recommended: a domain or load balancer setup if you plan to expose ArgoCD UI externally.

---

#### Part 1 — Karpenter: install and configure

Purpose: automatically provision and manage nodes in response to workloads.

##### 1) Install Karpenter

- Quick start (latest stable release):
  - Create the Karpenter components in your cluster:
    - kubectl apply -f https://github.com/karpenter/karpenter/releases/latest/download/karpenter.yaml
  - Note: The URL above installs the controller and a basic RBAC setup. You may need cloud-provider-specific prerequisites (IAM roles, instance profiles, subnets, security groups) per your cloud.

- Recommended next step: create a Provisioner resource to tell Karpenter how to provision nodes.

##### 2) Create a Provisioner (example)

Adjust to your cloud (AWS, GCP, Azure) and your cluster network. This is a minimal, generic Provisioner example you can customize.

```yaml
apiVersion: karpenter.sh/v1alpha5
kind: Provisioner
metadata:
  name: default
spec:
  # Cloud-provider specific provider config goes here
  provider:
    # Example placeholders; replace with real subnet and SG selectors for your cluster
    subnetSelector: "karpenter.sh/discovery=default-subnet"
    securityGroupSelector: "karpenter.sh/discovery=default-sg"
    # Instance profiles, AMI families, etc., may be required for your cloud
  requirements:
    - key: "karpenter.sh/capacity-type"
      operator: In
      values: ["on-demand", "spot"]
  limits:
    resources:
      cpu: "1000"    # adjust to your scale
      memory: "4Ti"
  ttlSecondsAfterEmpty: 30
```

- Apply it:
  - kubectl apply -f provisioner.yaml

> Cloud-provider specifics (subnets, security groups, instance profiles, IAM roles) vary. Refer to the Karpenter docs for your cloud provider and follow the required IAM/Networking steps.

##### 3) Validate Karpenter

- Check the Karpenter pods:
  - kubectl -n karpenter get pods
- See nodes being created in response to workload:
  - kubectl get nodes
- Check the Provisioner status for any issues:
  - kubectl describe provisioner default
- Optional: review Karpenter controller logs:
  - kubectl logs -n karpenter deploy/karpenter-controller

---

#### Part 2 — ArgoCD: install and configure

Purpose: manage Kubernetes apps from Git in a GitOps manner.

##### 1) Install ArgoCD

```bash
# Create the namespace and install
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

##### 2) Access ArgoCD

- Port-forward for local access (HTTPS on 443 inside, 8080 on your machine):
  - kubectl port-forward -n argocd svc/argocd-server 8080:443
- Get initial admin password:
  - kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

- Login: username `admin`, password from the secret. You can also set up SSO or other auth methods later.

##### 3) Connect a Git repo and create an Application

- Example Application manifest (points to a repo containing Kubernetes manifests or Helm/ kustomize files):

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: sample-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: 'https://github.com/your-org/your-repo'
    targetRevision: HEAD
    path: apps/sample
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

- Apply:
  - kubectl apply -f application.yaml -n argocd

- In the UI (or via CLI), verify Application status, and watch automatic syncs if you’ve enabled automated sync.

##### 4) Optional: ArgoCD Projects and RBAC

- Projects help you group Applications and restrict sources/destinations.
- RBAC policies can limit who can sync what.

---

#### Using Karpenter and ArgoCD together

- Store Provisioner YAMLs in Git and manage them with ArgoCD as code:
  - Create a separate Application or include Provisioner manifests in your repo.
  - ArgoCD will apply/update provisioners as part of your GitOps workflow.
- Benefits:
  - Repeatable, auditable autoscaler configuration.
  - All cluster-state changes come from Git, improving traceability.

Guidance:
- Start with a minimal Provisioner manifest in Git, then expand with constraints (zone/region selectors, instance types, TTLs, capacity types).
- Consider separating Karpenter-related resources into their own namespace (e.g., karpenter) to keep concerns isolated.

---

#### Validation & quick checks

- Karpenter
  - Resources: kubectl get nodes, kubectl describe provisioner default
  - Verify that new nodes appear when there’s unschedulable workload and disappear when idle (TTL after empty)
- ArgoCD
  - Access UI, confirm Application status shows Synced/Healthy
  - Check on Git changes that ArgoCD detects and auto-syncs (if enabled)
  - Validate that workloads deployed by ArgoCD reflect the apps in Git

---

#### Quick reference commands

- Karpenter install (controller):
  - kubectl apply -f https://github.com/karpenter/karpenter/releases/latest/download/karpenter.yaml
- Karpenter Provisioner apply:
  - kubectl apply -f provisioner.yaml
- ArgoCD install:
  - kubectl create namespace argocd
  - kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
- Access ArgoCD:
  - kubectl port-forward -n argocd svc/argocd-server 8080:443
  - Password: fetched from argocd-initial-admin-secret
- Create an ArgoCD Application:
  - kubectl apply -f application.yaml -n argocd

---

#### Troubleshooting quick tips

- Karpenter
  - If no nodes are created, check: Provisioner status, subnet/SG selectors, cloud permissions, and controller logs.
  - Ensure your workloads have resource requests that trigger provisioning.
- ArgoCD
  - If Application stays "OutOfSync," inspect the repo path, targetRevision, and Kubernetes manifests.
  - If the UI is not reachable, ensure the ArgoCD server service is accessible (Ingress or port-forward).

---

#### References

- Karpenter documentation: https://karpenter.sh
- ArgoCD documentation: https://argo-cd.readthedocs.io
- Kubernetes manifests for ArgoCD install: https://github.com/argoproj/argo-cd/tree/stable/manifests

If you want, I can tailor the Provisioner YAML to a specific cloud (AWS, GCP, or Azure) and add a sample Git repository layout for ArgoCD.

---

## 20. Agentic AI DevOps

Placeholder for exploring AI-driven incident management, automated Jira ticket creation via MCPS, and integration with PagerDuty.
#### What is PagerDuty?
PagerDuty is a widely-used incident management platform in the DevOps community that automates incident management processes, enabling teams to respond to and resolve issues efficiently.

#### Key Topics Covered

##### 1. Incident Management Overview
- **Definition**: Incident management involves identifying, resolving, and analyzing incidents.
- **Challenges**: Manual incident management can be cumbersome, particularly in high-pressure situations.

##### 2. PagerDuty's Features
- **End-to-End Incident Management**: PagerDuty has recently rolled out end-to-end incident management across all plans, including Professional and Business plans.
  
###### Professional Plan Features:
- Configure incident response workflows.
- Integrate with Slack and Jira for automation.
- Set up external status pages for up to 250 subscribers.
- AI-powered incident summarization.
- 1,000 advanced credits and two dedicated teams for security incidents.

###### Business Plan Features:
- Access to status pages for up to 500 subscribers.
- Custom roles and incident types.
- Enhanced incident workflow triggers.

#### Why is Incident Management Important?
Effective incident management is crucial, as illustrated by a scenario where a tech company's payment service experiences issues. A drop in successful HTTP requests below 99% can halt transactions, negatively impacting sales and customer satisfaction.

#### Manual Incident Management Challenges
In a manual setup, incident managers must gather information, create tickets, and coordinate with various stakeholders, often leading to delays and inefficiencies, especially during off-hours.

#### How PagerDuty Simplifies Incident Management
1. **Integration with Monitoring Tools**: For example, integrating Grafana with PagerDuty allows for automatic alerts when issues arise.
2. **Slack Integration**: PagerDuty’s Slack bot facilitates communication, enabling teams to acknowledge incidents, share information, and collaborate effectively.
3. **Incident Workflow Automation**: Teams can define specific workflows based on incident priority (e.g., P1 or P2), streamlining the response process.

#### Guided Demo Overview
In the guided demo, we will:
- Set up a Slack channel for incident communication.
- Acknowledge and define an incident.
- Use PagerDuty to gather relevant information and facilitate collaboration among team members.
- Resolve the incident and conduct a post-incident review.

#### Conclusion
PagerDuty enhances incident management by automating processes, improving communication, and enabling faster resolutions. I encourage you to try out the guided demo linked in the description and pinned comment. If you have any questions, feel free to ask in the comments. Thank you for watching, and see you in the next video! Take care!

---

## 21. Check CloudThat OneNote

### AWS DevOps Important Links

#### 1. Prerequisite of AWS DevOps Engineer – Professional Course
[Prerequisites_DevOps_on_AWS_Updated.pdf](https://aws-devops-course-swami-new.s3.us-east-1.amazonaws.com/Prerequisites_DevOps_on_AWS_Updated.pdf)

---

#### 2. CloudFormation Templates
[Download CloudFormation Templates](https://aws-devops-course-swami-new.s3.amazonaws.com/final_cfn.zip)

---

#### 3. First CodePipeline Code
[Download CodePipeline Code](https://aws-devops-course-swami-new.s3.us-east-1.amazonaws.com/codecommit-code-updated.zip)

---

#### 4. Instructions for CodeBuild and CodeDeploy
[CodeBuild_CodeDeploy Instructions](https://aws-devops-course-swami-new.s3.amazonaws.com/CodeBuild_CodeDeploy.txt)

---

#### 5. ECS CI/CD Demo Code
[Download ECS Fargate CI/CD Demo Code](https://aws-devops-course-swami-new.s3.us-east-1.amazonaws.com/ECS_Fargate_CICD_Demo_Code_Updated.zip)

---

### Additional Resources

The following resources can be found on **AWS Skill Builder**:

6. AWS DevOps Exam Guide  
7. Exam Domain-wise Courses  
8. AWS DevOps Official Question Set  
9. Exam Readiness: AWS Certified DevOps Engineer – Professional
 - C:\kunal\cloudthat training devops\sameer cloudthat
---

## 22. Ansible & Terraform (Stack, Dependencies, Output Variables)
 - https://github.com/kunalshrivastavapune25/aws/blob/main/vpc_cf/terraformVsAWSCf.md

---

## 23. AWS Well-Architected Frameworks

### Overview
A set of best practices for building secure, high-performing, resilient, and efficient cloud infrastructure.

### Six Pillars

| Pillar | Focus |
|--------|-------|
| **Operational Excellence** | Run and monitor systems |
| **Security** | Protect data and systems |
| **Reliability** | Recover from failures |
| **Performance Efficiency** | Use resources effectively |
| **Cost Optimization** | Avoid unnecessary costs |
| **Sustainability** | Minimize environmental impact |

### Key Benefits
- ✅ Consistent architecture decisions
- ✅ Risk identification and mitigation
- ✅ Improved workload quality
- ✅ Free review tools available

### Tool
**AWS Well-Architected Tool** - Free service to review workloads against framework best practices.


---

## 24. AWS Cross-Account Questions
 - https://www.youtube.com/watch?v=n1r9Fp7GKvk
Common scenarios:
- EC2 in Account A accessing EC2 in Account B
- S3 cross-account access using IAM roles
- Cross-account VPC peering / Transit Gateway

#### Introduction
This tutorial from Knowledge India focuses on enabling cross-account access for an IAM user in one AWS account to access resources in another AWS account. The scenario involves two AWS accounts, referred to as KI-2 (where the user exists) and KI-3 (where access is needed). The user, Gopal, a security auditor in KI-2, requires read-only access to resources in KI-3 without the need to create a new user in that account.

#### Scenario Overview
- **Accounts**: KI-2 (user's account) and KI-3 (target account).
- **User**: Gopal needs access to KI-3.
- **Objective**: Enable Gopal to view resources in KI-3 while restricting another user, Komal, from accessing KI-3.

#### Context
In large organizations, multiple AWS accounts are common, often organized by department or project. A centralized security team requires the ability to monitor resources across these accounts without managing numerous usernames and passwords.

#### Proposed Solution
1. **Single User Creation**: Maintain a single IAM user (Gopal) in KI-2.
2. **Role Creation in KI-3**: Establish an IAM role in KI-3 that Gopal can assume for access.
3. **Permissions**: Grant Gopal read-only access while restricting Komal's access to KI-3.

#### Steps to Implement Cross-Account Access

##### 1. Create IAM Role in KI-3
- **Navigate to Roles**: In the KI-3 account, create a new role.
- **Select Trusted Entity**: Choose "Another AWS Account" and input the KI-2 account ID.
- **Permissions**: Assign read-only access to the role.
- **Role Name**: Name the role (e.g., "SecurityReadOnlyRole").

##### 2. Assign Permissions to Gopal in KI-2
- **User Permissions**: Create a group (e.g., "ReadOnlyGroup") in KI-2 and add Gopal and Komal.
- **Attach Policies**: Grant read-only permissions to the group.

##### 3. Allow Gopal to Assume the Role
- **STS Permissions**: Add an inline policy in the group settings that allows Gopal to assume roles in other accounts.
- **Policy Example**: Use the `sts:AssumeRole` action with a wildcard for the role name.

##### 4. Gopal Switches Role
- **Switch Role**: Gopal can switch to the KI-3 account using the created role.
- **Verification**: Confirm that Gopal can view resources in KI-3 (e.g., S3 buckets).

##### 5. Restrict Komal's Access
- **Role Trust Relationship**: Modify the trust relationship policy in the KI-3 role to specify that only Gopal can assume the role by including only Gopal's IAM user ARN.

#### Steps to Edit Trust Relationship
1. **Edit Trust Relationship**: Access the role's trust relationship settings.
2. **Restrict Access to a Specific User**: Specify Gopal's ARN in the trust policy to limit access.
3. **Update Trust Policy**: Save changes to ensure only Gopal can assume the role.

##### Verification of Trust Relationship
1. **Switch Role**: After updating the trust policy, Gopal should successfully switch roles.
2. **Test with Another User**: Attempt to switch roles as Komal, confirming she cannot access the role, thus validating the trust relationship restrictions.

#### Conclusion
This tutorial effectively demonstrates how to set up cross-account access in AWS, allowing Gopal to view resources in KI-3 while restricting Komal's access. It emphasizes the importance of managing trust relationships to maintain security in organizations with multiple AWS accounts.

For more AWS tutorials, please subscribe to our channel at [youtube.com/knowledgeindia](https://youtube.com/knowledgeindia). Thank you for watching!

---

## 25. ITIL 4 & PM – Incident & Problem Management

 - https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/12_devops-lead-architect/readme.md
---

## 26. ITIL 4 & PM – Costing and Resourcing

 - https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/12_devops-lead-architect/readme.md
---

## 27. DevOps Management

 - https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/12_devops-lead-architect/readme.md
---

## 28. Lead Roles

 - https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/12_devops-lead-architect/readme.md

---

## 29. Major Incident in Production

 - https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/12_devops-lead-architect/readme.md
Interview Scenario

Production API latency suddenly increased.

Incident Response Steps

1️⃣ Identify impact

Which service affected?

2️⃣ Check monitoring

CloudWatch

Prometheus

3️⃣ Check infrastructure

Load balancer health

Auto scaling

4️⃣ Check database

Slow queries

CPU spikes

5️⃣ Check recent deployments

Rollback if needed

6️⃣ Mitigation

Scale infrastructure

7️⃣ Postmortem

Root cause analysis

Prevention plan
---

## 30. How to Create a Helm Chart

- [YouTube Tutorial: Helm Chart Creation](https://www.youtube.com/watch?v=jUYNS90nq8U)
- 1. **Generating a Helm Chart**:
   - Use the command `helm create <chart-name>` (e.g., `helm create web-app-1`) to generate the necessary folder structure and files for your Helm chart.
   - The generated folder includes:
     - `charts.yaml`: Metadata about the Helm chart.
     - `values.yaml`: Default configuration values.
     - `templates/`: Directory for Kubernetes resource templates.

2. **Customizing the Chart**:
   - Remove unnecessary files from the `templates/` folder and add your custom application manifests.
   - Modify the `values.yaml` file to suit your application requirements, starting with a blank file for better understanding.

3. **Deploying the Application**:
   - Navigate to the parent folder of your Helm chart and run:
     ```
     helm install <release-name> ./<chart-folder>
     ```
   - Verify successful deployment using `kubectl get all`.

4. **Using Helm Templating**:
   - Customize deployments for various environments (development, staging, production) using Helm templating.
   - Reference values in your templates with the syntax `{{ .Values.<key> }}`.

5. **Creating Environment-Specific Values Files**:
   - Create separate configuration files, such as `values-dev.yaml` and `values-prod.yaml`, to override settings for different environments.
   - Deploy with:
     ```
     helm upgrade <release-name> ./<chart-folder> --values values-dev.yaml
     ```
   - This allows for tailored configurations like namespace, image tags, and custom messages.

6. **Outputting Configuration Information**:
   - Add a `notes.txt` file in the `templates/` folder to provide users with commands to access the application post-installation, utilizing templating for dynamic content.

7. **Final Steps**:
   - Ensure the necessary namespaces for development and production are created in your Kubernetes cluster.
   - Deploy applications using the appropriate Helm commands for each environment.

### Additional Notes:
- To specify an additional values file, use the `-f` switch (e.g., `-f values-dev.yaml`) and specify the namespace with `-n dev` to ensure the Helm release aligns with the Kubernetes resources.
- An error was encountered due to an incorrect value; the correct value is `web-app-1`, which will be updated in the README file.
- After deploying to production using the `prod.yaml` file and the `prod` namespace, you can view all releases with `helm ls --all-namespaces`.
- Verify resources in each environment using `kubectl get all -n dev` for development and `kubectl get all -n prod` for production.
- Access the web application to check custom headers for both environments, confirming functionality.
---

## 31. AWS Organization , control towers landing zones

## 32. Migration

## 33. HIGHAVAILABLE
 - https://github.com/kunalshrivastavapune25/kunal_all_repos/blob/main/12_devops-lead-architect/1771921124437.jpg

By the end of this tutorial, you will have the skills to effectively customize and deploy Kubernetes applications using Helm, making it a valuable resource for job seekers and personal projects. For further learning, check the video description for additional resources and links to related content. Thank you for watching!

---

> **Note:** This README is a compilation of personal study notes, interview preparation points, and resource links. Content is preserved as originally written, with minor reorganization for clarity.
