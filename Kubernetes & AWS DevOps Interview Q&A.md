# DevOps Interview Q&A – Real-world EKS/Terraform/ArgoCD/AWS Answers


## 🔹 Tell me about yourself

I’m an AWS Cloud/DevOps engineer focused on **containerized workloads on EKS**, **GitOps with Argo CD**, and **Infrastructure as Code using Terraform** (with modules). I design production-ready EKS clusters (Karpenter autoscaling, ALB/NLB ingress, OIDC integration, observability) and set up **CI/CD pipelines (Jenkins/GitHub Actions → ECR → EKS)**. I’ve also worked on OIDC (Cognito/Entra ID), monitoring with Prometheus/Grafana, and integrations like MLFlow and Sentry.

---

## 🔹 What is your role in your company?

- Manage EKS clusters with production-ready setups.
- Build Terraform modules for VPC, EKS, IAM, ALB/NLB, Karpenter, Argo CD, Cognito.
- Automate CI/CD with Jenkins and GitHub Actions.
- Maintain observability (Prometheus, Grafana, CloudWatch) and alerting.
- Secure infrastructure (RBAC, IAM, secrets management).
- Cost optimization, incident troubleshooting, rollout automation.

---

## 🔹 What is Ingress? Why do we use it?

- Ingress is a **Kubernetes API object and controller** that manages **external HTTP/HTTPS access** to services.
- Supports **L7 routing**, TLS termination, and traffic rules based on **host/path**.
- Allows exposing multiple services under one LoadBalancer IP or DNS.

---

## 🔹 Difference between ConfigMap and Secret

| Feature        | ConfigMap               | Secret                            |
|----------------|--------------------------|------------------------------------|
| Purpose        | Store non-sensitive config | Store sensitive data (passwords, tokens) |
| Storage        | Plain text              | Base64-encoded (can encrypt at rest) |
| Kubernetes RBAC | Standard access         | Sensitive - needs stricter access control |
| Usage          | Mounted or injected as env | Same                              |

---

## 🔹 How will you deploy an application using Argo CD?

1. Push manifests/Helm/Kustomize to Git.
2. Create an `Application` in Argo CD pointing to the repo.
3. Configure auto or manual sync.
4. Use GitOps workflow to update images/configs in Git.
5. Argo CD applies changes automatically.

```bash
argocd app create myapp \
  --repo https://git.example.com/org/repo.git \
  --path charts/myapp \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace prod
argocd app sync myapp
```

---

## 🔹 What is a Rolling Update in Kubernetes?

- The **default deployment strategy** in Kubernetes.
- Gradually replaces old Pods with new ones.
- Uses `maxUnavailable` and `maxSurge` to control update speed.
- Ensures near-zero downtime if probes are configured.

---

## 🔹 How to check logs of a Pod or application?

```bash
kubectl logs <pod-name>
kubectl logs -f <pod-name>
kubectl logs <pod-name> -c <container-name>
kubectl logs <pod-name> -c <container-name> --previous
kubectl logs deploy/<deployment-name>
```

---

## 🔹 How do you troubleshoot CrashLoopBackOff or other deployment errors?

1. Run:
   ```bash
   kubectl describe pod <pod>
   kubectl logs <pod> --previous
   ```
2. Check probes, env vars, volumes, secrets/configs.
3. Validate image name, tag, and pull permissions.
4. Use:
   ```bash
   kubectl get events --sort-by=.metadata.creationTimestamp
   ```

---

## 🔹 Explain DaemonSet, StatefulSet, Deployment

- **Deployment**: Stateless workloads, rolling updates, ReplicaSets.
- **StatefulSet**: Ordered, persistent Pods with stable identity and volume (e.g., databases).
- **DaemonSet**: Runs **one Pod per node** (log agents, monitoring agents, etc.).

---

## 🔹 What is a PDB (PodDisruptionBudget)?

- Defines **minimum availability** for Pods during **voluntary disruptions** (drains, upgrades).
- You can set:
  - `minAvailable`
  - or `maxUnavailable`

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: myapp
```

---

## 🔹 NAT Gateway vs Internet Gateway

| Feature          | NAT Gateway                          | Internet Gateway                        |
|------------------|----------------------------------------|------------------------------------------|
| Use Case         | Private subnet → Internet (egress only) | Public subnet → Internet (in/out)       |
| Direction        | Outbound only                         | Bidirectional                            |
| Attached to      | Public subnet                         | VPC                                      |
| Charges          | Hourly + data transfer                | Free                                     |

---

## 🔹 What are different Kubernetes deployment strategies?

- **RollingUpdate** (default)
- **Recreate**
- **Blue/Green**
- **Canary**
- **A/B Testing**
- **Shadow/Mirror Traffic**
- **Argo Rollouts/Flagger** for advanced deployment

---

## 🔹 Describe the VPC structure in your project

- Multi-AZ VPC with:
  - Public subnets (ALB, NAT Gateway)
  - Private subnets (EKS Nodes, apps)
  - Isolated subnets (RDS, ElastiCache)
- Route Tables per subnet tier
- Security Groups and NACLs
- VPC Endpoints (S3, ECR, STS)

---

## 🔹 Explain Kubernetes cluster architecture

- **Control Plane**:
  - `kube-apiserver`, `etcd`, `kube-controller-manager`, `kube-scheduler`
- **Nodes**:
  - `kubelet`, `kube-proxy`/`Cilium`, `containerd`
- **EKS**:
  - AWS manages control plane
  - You manage nodes, addons, ingress, autoscaling, networking

---

## 🔹 How do you integrate Argo CD with Jenkins?

- Jenkins builds and pushes Docker images to ECR.
- Jenkins updates Helm values/manifest files in Git (e.g., image tag).
- Argo CD detects changes and syncs them.
- Can also trigger `argocd app sync` from Jenkins.

---

## 🔹 Use cases of CodeBuild, CodePipeline, and CodeDeploy

- **CodeBuild**: Fully managed build service, serverless alternative to Jenkins agents.
- **CodePipeline**: CI/CD orchestration (connects Git → Build → Deploy).
- **CodeDeploy**: Handles deployments to EC2, Lambda, ECS (supports hooks, blue/green, canary).

---

## 🔹 How to autoscale in EC2?

- Use **Auto Scaling Groups (ASGs)**:
  - Target tracking scaling (CPU %, requests, etc.)
  - Step scaling based on CloudWatch alarms
  - Scheduled scaling for predictable loads
- Use **mixed instances** (On-Demand + Spot) for cost savings

---

## 🔹 What is HPA, and what does a ConfigMap contain?

- **HPA (HorizontalPodAutoscaler)**:
  - Auto scales Pod replicas based on metrics (CPU/memory/custom).
  - Requires `metrics-server` or external metrics.

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
spec:
  minReplicas: 2
  maxReplicas: 5
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

- **ConfigMap** contains **non-sensitive config** like flags, URLs, feature toggles, etc.

---

## 🔹 Errors faced while deploying in AWS using CodeDeploy

- Invalid or missing `appspec.yml`
- Script permission errors in lifecycle hooks
- IAM role missing permissions (S3, logs, EC2)
- Wrong EC2 tags or missing in Deployment Group
- Agent not installed or outdated on EC2
- Failing health checks on ALB/NLB (in blue/green)
- Timeouts on lifecycle hooks
- Missing files in S3 bucket
- File permission issues after unpacking revision

---

## CI/CD Pipelines  
**Q:** _How do you run builds in multiple operating systems in a pipeline?_

**A:**  
Use a **matrix strategy** in your CI/CD tool to run builds on different OS environments.

### GitHub Actions:
```yaml
jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
    steps:
      - uses: actions/checkout@v3
      - run: echo "Building on ${{ matrix.os }}"
```

### GitLab CI:
```yaml
linux-build:
  tags: [linux]
  script: ./build-linux.sh

windows-build:
  tags: [windows]
  script: build-windows.bat
```

### Jenkins:
Use `agent { label 'windows' }`, `agent { label 'linux' }` in declarative pipelines or run jobs on appropriately labeled nodes.

---

## Secret Management  
**Q:** _How did you access HashiCorp Vault or any secret manager located in another AWS region?_

**A:**

### HashiCorp Vault:
- Set `VAULT_ADDR` to the remote Vault endpoint.
- Ensure network connectivity (via public endpoint, VPC peering, or Transit Gateway).
```bash
export VAULT_ADDR="https://vault.ap-southeast-1.mycompany.com"
vault login <token>
vault kv get secret/myapp
```

### AWS Secrets Manager:
```bash
aws secretsmanager get-secret-value   --secret-id mysecret   --region us-west-2
```

---


## 502 Production Issue  
**Q:** _The pod is running, but logs show a 502 error — how would you debug it?_

**A:**

1. Check pod logs:
   ```bash
   kubectl logs <pod>
   ```

2. Check readiness probe:
   ```bash
   kubectl describe pod <pod>
   ```

3. Curl the internal pod port:
   ```bash
   kubectl exec -it <pod> -- curl localhost:<port>
   ```

4. Check Ingress controller logs:
   ```bash
   kubectl logs -n ingress-nginx <controller-pod>
   ```

5. Check endpoints:
   ```bash
   kubectl get endpoints <service>
   ```

---

## CrashLoopBackOff Troubleshooting  
**Q:** _How do you fix a CrashLoopBackOff issue in a pod?_

**A:**

1. View previous logs:
   ```bash
   kubectl logs <pod> --previous
   ```

2. Describe pod for events:
   ```bash
   kubectl describe pod <pod>
   ```

3. Common causes:
   - Incorrect entrypoint/command.
   - Missing configs/secrets.
   - Failing initContainers.
   - OOMKilled or probe failures.

---

## Missing Services  
**Q:** _If a service doesn’t appear in `kubectl get services`, what could be the problem?_

**A:**

- It may not have been created or applied.
- Check across namespaces:
  ```bash
  kubectl get svc -A
  ```
- Validate YAML syntax:
  ```bash
  kubectl apply -f service.yaml --dry-run=client
  ```

---

## Cluster Monitoring  
**Q:** _How do you check node-level resource consumption in Kubernetes?_

**A:**

- Using Metrics Server:
  ```bash
  kubectl top node
  ```

- Describe node for allocatable resources:
  ```bash
  kubectl describe node <node-name>
  ```

- Or use observability tools:
  - **Prometheus + Grafana**
  - **Lens**
  - **K9s**

---

## Container Crashes  
**Q:** _What steps would you take if a container crashes immediately after starting?_

**A:**

1. View logs:
   ```bash
   kubectl logs <pod> --previous
   ```

2. Describe pod for detailed event info:
   ```bash
   kubectl describe pod <pod>
   ```

3. Check:
   - Entrypoint errors
   - Missing secrets/configs
   - Port conflicts
   - Image pull or permission errors

---
