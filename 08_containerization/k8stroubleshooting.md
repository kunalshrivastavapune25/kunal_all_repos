Here is a consolidated and structured summary of the Kubernetes Troubleshooting series, removing redundancy and organizing the information for easy reference.

---

### Kubernetes Troubleshooting: Zero to Hero - Consolidated Notes

This guide covers common Kubernetes errors and their solutions, including image pull issues, pod crashes, scheduling, resource management, and stateful workloads.

---

### 1. Image Pull Backoff

This error occurs when Kubernetes fails to pull a container image for a pod.

#### Common Causes
- **Invalid Image Name**: A typo or incorrect image name (e.g., `ngiNY` instead of `nginx`).
- **Non-Existent Image**: The image does not exist in the specified registry.
- **Private Container Registry**: Missing credentials for a private repository (e.g., Docker Hub, AWS ECR).

#### Troubleshooting Steps
1.  **Get Pod Details**:
    ```bash
    kubectl describe pod <pod-name>
    ```
2.  **Check Cluster Events**:
    ```bash
    kubectl get events
    ```

#### How to Fix
- **For Invalid/Non-Existent Images**: Correct the image name in your deployment YAML.
- **For Private Registries**: Create a `docker-registry` secret and reference it in your pod spec.

    **1. Create the Secret:**
    ```bash
    # For Docker Hub
    kubectl create secret docker-registry <secret-name> \
      --docker-username=<your-username> \
      --docker-password=<your-password> \
      --docker-email=<your-email>

    # For AWS ECR
    kubectl create secret docker-registry <secret-name> \
      --docker-server=<aws-account-id>.dkr.ecr.<region>.amazonaws.com \
      --docker-username=AWS \
      --docker-password=$(aws ecr get-login-password --region <region>)
    ```
    **2. Reference Secret in Pod/Deployment:**
    ```yaml
    spec:
      imagePullSecrets:
      - name: <secret-name>
      containers:
      - name: my-container
        image: my-private-image
    ```

---

### 2. CrashLoopBackOff

This status indicates a pod is repeatedly crashing after starting. Kubernetes implements an exponential backoff delay (starting at 10s, doubling up to 5 minutes) between restart attempts.

#### Common Causes
- **Incorrect Command or Arguments**: The container’s entrypoint or command fails.
- **Liveness Probe Failures**: A probe fails, causing Kubernetes to restart the container.
- **Insufficient Resources**: The pod is killed due to exceeding its memory limit (OOMKilled) or insufficient CPU.
- **Application Errors**: Internal exceptions, missing environment variables, or misconfigurations.

#### How to Fix
1.  **Check Pod Logs**:
    ```bash
    kubectl logs <pod-name> --previous
    ```
2.  **Describe the Pod**:
    ```bash
    kubectl describe pod <pod-name>
    ```
    Look for events like `Liveness probe failed` or `OOMKilled`.
3.  **Adjust Probes**: Ensure liveness and readiness probes have correct paths, ports, and initial delay.
4.  **Set Resource Limits**: Define appropriate `requests` and `limits` for CPU and memory.
5.  **Fix Application Configuration**: Correct environment variables, command-line arguments, or persistent volume mounts.

---

### 3. Node Scheduling: Selectors, Affinity, Taints & Tolerations

These features control which nodes pods are scheduled on.

| Concept | Description | Example |
| :--- | :--- | :--- |
| **Node Selector** | **Hard requirement**. Pod only runs on nodes with a specific label. | `nodeSelector: { disktype: ssd }` |
| **Node Affinity** | **Flexible**. Supports `requiredDuringScheduling` (hard) and `preferredDuringScheduling` (soft) rules. | `preferredDuringSchedulingIgnoredDuringExecution:` |
| **Taints** | **Applied to nodes**. Repels all pods unless they have a matching toleration. Types: `NoSchedule`, `NoExecute`, `PreferNoSchedule`. | `kubectl taint nodes node1 key=value:NoSchedule` |
| **Tolerations** | **Applied to pods**. Allows a pod to be scheduled on a tainted node. | `tolerations: - key: "key", operator: "Equal", value: "value", effect: "NoSchedule"` |

#### How to Fix Scheduling Issues
1.  **Check Pod Status**: If a pod is `Pending`, describe it to see scheduling failures.
    ```bash
    kubectl describe pod <pod-name>
    ```
2.  **Label Nodes if Necessary**:
    ```bash
    kubectl label node <node-name> <key>=<value>
    ```
3.  **Troubleshoot Taints**: List taints on nodes.
    ```bash
    kubectl describe nodes | grep Taints
    ```
    If a pod can't run, you may need to add a toleration or remove the taint.

---

### 4. Resource Management: Quotas, Limits & OOM Killed

#### Resource Quotas (Namespace Level)
Limits the total resource consumption for a namespace to prevent one team from starving others.
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: namespace-quota
spec:
  hard:
    requests.cpu: "4"
    requests.memory: "8Gi"
    limits.cpu: "8"
    limits.memory: "16Gi"
```

#### Resource Limits (Pod Level)
Prevents a single pod from consuming all resources on a node. A pod is terminated (OOMKilled) if it exceeds its memory limit.
```yaml
resources:
  requests:
    memory: "512Mi"
    cpu: "250m"
  limits:
    memory: "1Gi"
    cpu: "500m"
```

#### Handling OOM Killed Pods
1.  **Identify the issue**: Use `kubectl describe pod` to confirm `OOMKilled`.
2.  **Gather Dumps** (for Java apps): Collect thread and heap dumps for analysis.
3.  **Analyze**: Work with the development team to identify memory leaks or code issues.
4.  **Update**: Redeploy the application with fixes or adjusted memory limits.

---

### 5. StatefulSets & Persistent Volumes

StatefulSets manage stateful applications, providing stable network identities and persistent storage. Issues often arise from the Persistent Volume (PV) provisioner.

#### Workflow
1.  **StatefulSet** creates a **Persistent Volume Claim (PVC)** based on its `volumeClaimTemplates`.
2.  The PVC specifies a **StorageClass**.
3.  The **StorageClass** uses a **provisioner** (e.g., EBS, Azure Disk, local) to dynamically create a **Persistent Volume (PV)**.

#### Common Issue: Pending Pods with Unbound PVCs
A common problem is using a storage class that doesn't exist in the current environment (e.g., using an `ebs` storage class on Minikube or AKS).

#### How to Fix
1.  **Check PVC Status**:
    ```bash
    kubectl get pvc
    ```
    Look for a `Pending` status.
2.  **Describe the PVC**:
    ```bash
    kubectl describe pvc <pvc-name>
    ```
    The error will indicate that no persistent volume was found.
3.  **Update the StorageClass**: Edit the StatefulSet's `volumeClaimTemplates` to use a valid storage class for your environment.
    - For Minikube, use `standard`.
    - For AKS, use `managed-csi` or `default`.
4.  **Delete Old PVCs** (if any):
    ```bash
    kubectl delete pvc --all
    ```
5.  **Reapply the StatefulSet**:
    ```bash
    kubectl apply -f statefulset.yaml
    ```
