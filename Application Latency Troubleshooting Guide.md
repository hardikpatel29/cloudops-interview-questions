
# 🛠️ 3-Tier Application Latency Troubleshooting Guide

This guide outlines a step-by-step process to troubleshoot **increased latency** in a 3-tier application (Frontend → Backend/API → Database).  
**Scenario**: Latency increases from 10ms to 40ms after 2 hours of runtime.

---

## 📌 Architecture Overview

```
Client
  |
Frontend (React/Angular)
  |
Backend/API (Node.js/Spring/FastAPI)
  |
Database (PostgreSQL/MySQL/MongoDB)
```

---

## 🚨 Problem Statement

- Initial latency: ~10ms
- After 2 hours: ~40ms
- Objective: Identify the **bottleneck** and **root cause** across frontend, backend, infrastructure, or DB.

---

## 🔍 Step-by-Step Troubleshooting Checklist

### 1. 📊 Monitor and Baseline Metrics

Use observability tools like:
- **Prometheus + Grafana**
- **ELK/EFK stack**
- **New Relic / Datadog / Azure Monitor / AWS CloudWatch**

Monitor:
- Response time per tier
- CPU/memory usage (Pods/VMs)
- GC activity
- DB query performance
- Network RTT

```bash
# Example: Prometheus Query
rate(http_server_requests_seconds_sum[5m]) / rate(http_server_requests_seconds_count[5m])
```

---

### 2. ⚙️ Frontend Tier (Browser & Client)

- [ ] Use Chrome DevTools → **Network tab** to isolate:
  - TTFB (Time to First Byte)
  - Long JS execution
  - Blocking assets (CSS, images)
- [ ] CDN/Caching effective?
- [ ] Increase in JS memory usage over time?

```bash
# Example (Chrome):
Lighthouse > Performance > "Largest Contentful Paint" increase?
```

---

### 3. ⚙️ Backend/API Tier

- [ ] Enable **application tracing** (OpenTelemetry, Jaeger)
- [ ] Profile endpoints: Which API is slow?
- [ ] Memory/CPU usage over time (possible leak?)
- [ ] Number of concurrent requests/connections?
- [ ] GC logs: Is GC pausing frequently?

```bash
# Node.js (memory leak check)
node --inspect server.js
# Java (heap dump)
jmap -dump:live,format=b,file=heap.bin <pid>
```

---

### 4. ⚙️ Database Tier

- [ ] Check **slow query logs**
- [ ] Are queries being cached?
- [ ] Indexes used? (Use `EXPLAIN` plan)
- [ ] Connection pool exhaustion?
- [ ] Locks or deadlocks?

```sql
-- PostgreSQL
SELECT * FROM pg_stat_activity;
EXPLAIN ANALYZE SELECT ...;
```

---

### 5. 🔁 Networking

- [ ] Any **intermittent packet loss** or latency spikes?
- [ ] Load balancer response time?
- [ ] DNS resolution delays?
- [ ] Health check flaps or retries?

```bash
# Ping services
ping <backend-service>
curl -w "@curl-format.txt" -o /dev/null -s http://<service-url>
```

---

### 6. 📦 Kubernetes (If applicable)

- [ ] Check HPA/CPU throttling
- [ ] Are pods restarted (OOMKilled, CrashLoopBackOff)?
- [ ] Is resource allocation sufficient?
- [ ] Logs: `kubectl logs -n <ns> <pod>`

```bash
kubectl top pods -n <namespace>
kubectl describe pod <pod> -n <namespace>
```

## ✅ Suggested Fixes (Based on Symptoms)

| Symptom                              | Fix Recommendation                                      |
|-------------------------------------|----------------------------------------------------------|
| GC pause or memory leak             | Tune heap size, fix unbounded caches                    |
| Slow DB queries                     | Add indexes, optimize queries, tune pool size           |
| Backend saturation                  | Scale backend, investigate thread/connections exhaustion|
| Frontend loading slow               | Optimize assets, use CDN                                |
| Latency increases under load        | Load test, autoscaling, caching                         |

---

