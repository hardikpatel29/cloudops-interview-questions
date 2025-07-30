# Kubernetes & AWS DevOps Interview Q&A

This document contains factual, production-grade answers for common DevOps interview questions covering Kubernetes, AWS, CI/CD, and infrastructure automation.

---

## 📌 Tell Me About Yourself

I'm an AWS Cloud/DevOps engineer focused on **containerized workloads on EKS**, **GitOps with Argo CD**, and **Terraform (module-based) infrastructure**. I design and manage:

- EKS clusters (Karpenter for node scaling, AWS ALB/NLB, OIDC integration)
- CI/CD pipelines with Jenkins/GitHub Actions (build → ECR → Argo CD sync)
- Observability (Prometheus, Grafana, Sentry, CloudWatch)
- Secure access (Cognito, Entra ID), secrets management
- Infrastructure automation with reusable Terraform modules

---

## 👤 What Is Your Role in Your Company?

- Platform engineering for Kubernetes (EKS, Helm, ALB, Argo CD)
- Build/maintain reusable Terraform modules for VPC, EKS, OIDC, etc.
- CI/CD pipeline management (Jenkins, GitHub Actions)
- Troubleshooting app deployments and cluster/network-level issues
- Infrastructure observability, autoscaling, and cost optimization

---

## 🌐 What Is Ingress in Kubernetes?

- A Kubernetes object + controller to expose HTTP/HTTPS traffic to internal services
- Supports:
  - TLS termination
  - Host/path-based routing
  - Rate limiting, WAF, auth (with 3rd-party plugins)

---

## 🔐 ConfigMap vs Secret

| Feature        | ConfigMap                            | Secret                                  |
|----------------|--------------------------------------|------------------------------------------|
| Purpose        | Non-sensitive config                 | Sensitive data (passwords, tokens, certs)|
| Storage        | Plaintext                            | Base64 encoded; can be encrypted at rest |
| Size Limit     | ~1 MiB                               | ~1 MiB                                   |
| Usage          | Mounted as file or env var           | Same                                     |
| Security       | Low                                  | RBAC + Encryption options                |

---

## 🚀 How To Deploy App Using Argo CD?

1. Push Helm/manifests/Kustomize to Git repo
2. Create `Application` resource pointing to repo + cluster + namespace
3. Argo CD syncs the desired state to cluster
4. Use auto-sync or manual sync mode
5. Jenkins or GitHub Actions updates image tag in Git

Example CLI:
```bash
argocd app create myapp \
  --repo https://git.example.com/org/repo.git \
  --path charts/myapp \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace prod
argocd app sync myapp
