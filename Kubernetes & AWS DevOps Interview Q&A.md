# DevOps Interview Q&A – Real-world EKS/Terraform/ArgoCD/AWS Answers

This document contains factual, experience-based answers to real interview questions encountered in DevOps/Cloud Engineer roles, especially around Kubernetes (EKS), Terraform, Argo CD, Jenkins, and AWS services.

---

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

> ✅ Use this README to prep for DevOps/AWS/K8s interviews or share as part of your GitHub profile.
