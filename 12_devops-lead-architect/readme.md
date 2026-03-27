Below are concise, professional notes covering AWS IAM, Organizations, Control Tower, Landing Zones, cross‑account access, shared accounts, and Kubernetes RBAC.

---

### AWS Identity and Access Management (IAM)

- **Core components**: Users, Groups, Roles, Policies.
- **Policies**: JSON documents defining permissions.  
  - *Identity‑based*: attached to users, groups, roles.  
  - *Resource‑based*: attached directly to AWS resources (e.g., S3 bucket policies).  
- **Roles** are assumed by trusted entities (AWS services, users in other accounts, external identities).  
- **Permission boundaries**: delegate administration by limiting the maximum permissions a role/user can be granted.

**Best practices**:
- Use least‑privilege access.
- Prefer roles over long‑term credentials.
- Enable multi‑factor authentication (MFA) for privileged users.

---

### AWS Organizations

- **Management account** (formerly master account) centralizes control over member accounts.
- **Service Control Policies (SCPs)** allow you to centrally guard allowed services and actions across accounts. SCPs do **not** grant permissions; they restrict what can be granted.
- **Consolidated billing** simplifies cost management.
- **Organizational units (OUs)** group accounts for hierarchical governance.

**Use cases**:
- Enforce security baselines via SCPs.
- Separate workloads (dev, test, prod) into different accounts.

---

### AWS Control Tower & Landing Zone

- **AWS Control Tower** automates the setup of a **landing zone** – a well‑architected multi‑account AWS environment.
- **Landing zone** includes:
  - A management account (for billing, user management).
  - Two OUs: `Security` and `Sandbox` (by default).
  - Log archive and audit accounts.
  - Guardrails (preventive via SCPs, detective via AWS Config rules).
- Control Tower ensures ongoing governance as you add new accounts.

**Key benefits**:
- Accelerate setup of secure, compliant environments.
- Standardized account structure and networking.

---

### Cross‑Account Access

- **IAM roles** with trust policies that allow principals from another account to assume the role.
- **Resource‑based policies** (e.g., S3 bucket policy) can grant access to other accounts directly.

**Example – assuming a role from another account**:
1. Create role `CrossAccountRole` in target account with a trust policy allowing source account.
2. In source account, user assumes the role via `sts:AssumeRole`.
3. Use `aws sts assume-role --role-arn ...` or configure CLI profiles.

**Best practices**:
- Use roles, never share credentials.
- Audit cross‑account role usage with AWS CloudTrail.

---

### Shared Accounts (Common Patterns)

- **Security account**: centralised IAM, CloudTrail, GuardDuty.
- **Log archive account**: stores all logs (S3, CloudWatch).
- **Shared services account**: hosts networking (VPC, VPN), CI/CD tools, etc.
- **Application accounts** (dev, test, prod) separated from shared infrastructure.

---

### Kubernetes RBAC

- **Role**: grants permissions within a specific namespace.
- **ClusterRole**: grants permissions cluster‑wide (e.g., node management).
- **RoleBinding**: attaches a Role to a subject (user, group, or ServiceAccount) within a namespace.
- **ClusterRoleBinding**: attaches a ClusterRole to a subject cluster‑wide.

**Subjects**:
- **Users**: typically managed externally (e.g., via x509 certs, OIDC). Kubernetes does not have user objects.
- **Groups**: logical sets of users.
- **ServiceAccounts**: in‑cluster identities for pods to interact with the API.

**Service Accounts**:
- Namespaced.
- Can be associated with IAM roles (via IRSA on EKS) to grant AWS permissions to pods.

**Example – granting namespace‑level read access**:
```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

**Best practices**:
- Use namespaced Roles whenever possible; limit use of ClusterRoles.
- Grant least privilege.
- Regularly audit bindings with `kubectl get rolebindings,clusterrolebindings -A`.

---

### Comparison: AWS IAM vs Kubernetes RBAC

| Aspect            | AWS IAM                                   | Kubernetes RBAC                            |
|-------------------|-------------------------------------------|--------------------------------------------|
| **Entities**      | Users, Groups, Roles                      | Users (external), Groups, ServiceAccounts  |
| **Policies**      | JSON documents; identity‑ or resource‑based | Rules (verbs, resources)                   |
| **Scope**         | Global (region/account) or resource‑level | Namespace or cluster‑wide                  |
| **Authentication**| IAM (signature v4)                        | X.509, OIDC, webhooks, etc.                |
| **Authorization** | IAM policies                              | RoleBindings / ClusterRoleBindings         |

---

*These notes provide a high‑level overview suitable for interview preparation or architectural discussions. For hands‑on practice, refer to the official documentation of AWS and Kubernetes.*
