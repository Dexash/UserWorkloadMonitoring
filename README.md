# UserWorkloadMonitoring

# 📊 Standardized Observability on OpenShift with ACM + GitOps

This repository provides a **Helm chart** to standardize observability for application teams running on OpenShift.  
It leverages **User Workload Monitoring (UWM)**, **Red Hat Advanced Cluster Management (ACM) Observability**, and **GitOps (Argo CD)** principles.

---

## 🎯 Goals
- Provide application teams with **out-of-the-box observability**.  
- Ensure **consistency and automation** via Helm + GitOps.  
- Enable **secure multi-team access** with RBAC and ACM integration.  

---

## 🚀 Features
- **Namespace creation** (per team/project)  
- **Application deployment** with `/metrics` endpoint  
- **Service + Route exposure** for metrics and HTTP traffic  
- **ServiceMonitor** to scrape metrics via OpenShift UWM  
- **RBAC configuration** for Prometheus scraping  
- **ACM Observability integration** via allowlist ConfigMap  
- **Example Grafana dashboards** ready to import  


---

## 📖 Usage
1. Fork this repo and adjust `values.yaml` for your application.  
2. Commit and push changes → Argo CD syncs automatically.  
3. Metrics appear in **OpenShift dashboards** and **ACM Grafana**.  



