# 🧪 DevOps Interview Questions & Answers (Senior Role)

**Role**: Senior DevOps Engineer  
**Compensation**: ₹22 LPA  
**Rounds**: Technical Deep Dive – Round 1 & 2

---

## ✅ Questions & Answers

### 1. App on AKS randomly fails health checks
- Inspect pod logs: `kubectl logs`
- Check probe configuration: `liveness`, `readiness`
- Use `kubectl describe pod` for failure events
- Monitor with Azure Monitor, Container Insights
- Check node/network/disk pressure and DNS

---

### 2. 502 errors in Canary deployment
- 502 = Gateway issue
- Check:
  - Ingress logs (NGINX, App Gateway)
  - Endpoint mapping (`kubectl get endpoints`)
  - Pod readiness/liveness
  - Backend logs & metrics (Prometheus, App Insights)

---

### 3. CI/CD takes 40 mins for small change
- Use build and Docker layer caching
- Split pipeline stages & parallel jobs
- Optimize test execution (run only impacted tests)
- Use canary or blue/green deployments
- Use self-hosted agents or pre-built runners

---

### 4. High CPU usage but logs are clean
- Use `kubectl top pod` and `kubectl exec top`
- Profile the app with `perf`, `py-spy`, `flamegraphs`
- Investigate GC cycles, loops, or memory leaks
- Check resource limits and throttling

---

### 5. Logging for 100+ microservices across 3 regions
- Use:
  - **EFK (Elasticsearch-FluentBit-Kibana)**
  - **OpenTelemetry + Loki + Grafana**
- Aggregate via Kafka or Logstash
- Centralize in ElasticCloud, Azure Monitor, or Datadog
- Use FluentBit as a DaemonSet
- HA setup across all regions

---

### 6. 403 error for external users only
- 403 = Forbidden
- Check:
  - App Gateway or firewall rules (IP restrictions)
  - Identity/claims mismatch for external traffic
  - Authentication headers missing externally
  - Check Azure WAF logs

---

### 7. Secret rotation in Azure DevOps
- Store secrets in **Azure Key Vault**
- Integrate with Azure DevOps Variable Groups
- Use managed identity for access
- Automate rotation with Event Grid + Azure Functions

---

### 8. Azure App Gateway + WAF for banking app
- Use:
  - Azure Application Gateway v2
  - End-to-end TLS
  - WAF (OWASP rules, custom rules)
- Configure IP restriction, rate limiting
- Enable diagnostic logs to Log Analytics

---

### 9. Intermittent DNS resolution on Azure
- Causes:
  - CoreDNS pod crash/restart
  - Node-level DNS issue (`/etc/resolv.conf`)
  - Network Policies blocking kube-dns
  - Azure VNET DNS misconfiguration

---

### 10. 10-second delays every 15 mins
- Check:
  - Background jobs (cron, autoscaler)
  - External API dependencies
  - GC, memory/cpu spikes
- Use:
  - Azure Monitor, Prometheus, Application Insights

---

### 11. Jenkins fails during artifact upload
- Check:
  - Network reliability to artifact repo (S3, Artifactory)
  - Disk/Storage quotas
  - Jenkins agent CPU/memory pressure
  - Proxy/firewall between Jenkins and storage

---

### 12. Rollback strategy for Kubernetes
- Use:
  - Helm rollback (`helm rollback`)
  - ArgoCD automatic sync/rollback
  - Kubernetes deployment revision history (`kubectl rollout undo`)
  - Add health checks before promotion

---

### 13. Cost-optimized cloud architecture (reporting app)
- Use:
  - Azure Functions (scheduled)
  - Store logs in Blob Storage (Cool/Archive tier)
  - Use Log Analytics for querying
  - Retention policies for 3-year storage

---

### 14. Zero-downtime DB migration
- Use:
  - Feature flags to control new schema access
  - Shadow table for new schema
  - Flyway or Liquibase
  - Backup before migration

---

### 15. Disaster recovery for stateful containers
- Use:
  - Volume snapshot tools (Velero, Azure Backup)
  - StatefulSets with replicated PVCs
  - Cross-region failover setup
  - Documented and tested DR runbooks

---

### 16. Azure Function throttling
- Use Application Insights to find error rate
- Check function scaling and plan limits
- Use Premium Plan or dedicated app service
- Analyze invocations and cold starts
- Enable retry policies

---

### 17. Blue/Green with rollback on Azure
- Use:
  - Azure DevOps Pipelines + Terraform
  - AKS: Deploy blue and green versions
  - Azure App Service: Slot deployments
  - Monitor before DNS switch or ingress routing

---

### 18. End-to-end SLA monitoring (Payments)
- Use:
  - Distributed tracing (OpenTelemetry, Jaeger)
  - Azure Monitor + App Insights
  - Grafana for SLA dashboards
  - Setup alerts for SLO/SLA breaches

---

### 19. Scaling compute vs I/O intensive workloads
- **Compute**: F-series VMs, HPA on CPU, AKS with GPU
- **I/O**: Lsv2 VMs, Ultra Disks, Premium SSDs, disk caching
- Use Azure Advisor to select proper SKU

---

### 20. Pipeline blocked due to missing approval
- Escalate to emergency approver
- Configure fallback owner in pipeline policy
- Set auto-approval for non-prod environments
- Document escalation path
- Notify stakeholders via Teams/email integration

---

## 💡 Tip:
Prepare answers with tools, metrics, and commands you’ve used in real production environments. Keep each answer structured as:

