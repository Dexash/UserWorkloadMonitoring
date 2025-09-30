# UserWorkloadMonitoring

# 📊 Standardized Observability on OpenShift with ACM + GitOps

This repository provides a **Helm chart** to standardize observability for application teams running on OpenShift.

It leverages **User Workload Monitoring (UWM)**, **Red Hat Advanced Cluster Management (ACM) Observability**, and **GitOps (Argo CD)** principles.

---

## 🎯 Goals
- Provide application teams with **out-of-the-box observability**.  
- Ensure **consistency and automation** via Helm + GitOps.  
- Integrate with ACM Observability for **centralized metrics visibility**.  

---

## 🚀 Features
- **Namespace creation** (per team/project).  
- **Application deployment** with `/metrics` endpoint (e.g., Nginx with Prometheus exporter).  
- **Service + Route exposure** for application and metrics traffic.  
- **ServiceMonitor** to scrape metrics via OpenShift UWM.  
- **ACM Observability integration** via allowlist ConfigMap.  

> ❌ **Not included:**  
> - RBAC for cluster monitoring (owned by SRE/platform teams, not developers).

---

## 📂 Repository Structure

- `Chart.yaml` – Chart metadata (name, version, description).  
- `values.yaml` – Default values (namespace, image, ports).  
- `templates/` – Helm templates that render into Kubernetes manifests:  
  - **namespace.yaml** → Creates the application namespace.  
  - **deployment.yaml** → Deploys workload + metrics exporter.  
  - **service.yaml** → Exposes application and metrics internally.  
  - **route.yaml** → Creates an OpenShift Route for external access.  
  - **servicemonitor.yaml** → Configures Prometheus scraping for UWM.  
  - **observability-metrics-custom-allow.yaml** → Adds metrics to ACM Observability allowlist.  

---

## 📖 Usage

### 1. Update `values.yaml`
Example:
```yaml
namespace: <team-namespace>
app:
  name: <app-name>
  image: <workload-image>
  port: <workload-port>
exporter:
  image: <exporter-image>
  port: <exporter-port>


```
### 2. Install with Helm

You can install the chart directly using the Helm CLI:

```bash
helm install <release-name> ./userworkloadmonitoring

```

#### Upgrade the release

If you make changes to values.yaml or templates, upgrade the release
```
helm upgrade <release-name> ./userworkloadmonitoring
```

After installation, check that all resources were created:

```bash
oc get all -n <team-namespace>
```
You should see a Deployment, Pods, Service, Route, and the ServiceMonitor.

#### Uninstall the release

```
helm uninstall <release-name>
```
### 3. Deploy via Argo CD

For GitOps workflows, manage this chart through Argo CD. 
Add this repository to Argo CD
You can add it via the Argo CD web console or CLI.

CLI example:

```bash
argocd repo add https://github.com/<your-org>/UserWorkloadMonitoring.git
```

### Create an Application CR
Point it to the Helm chart folder. Example manifest:
```

apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: <app-name>-observability        # Change: unique Argo CD app name (per workload/team)
  namespace: openshift-gitops           # Usually fixed: namespace where Argo CD runs
spec:
  project: default                      # Or use a custom Argo CD project if configured
  source:
    repoURL: 'https://github.com/<org-or-user>/<repo>.git'  # Change: customer’s GitHub repo
    path: <chart-path>                  # Change: path to Helm chart folder
    targetRevision: main                # Change if using another branch (e.g., "dev" or "release")
    helm:
      values: |                         # Inline Helm values; customer can adjust below
        namespace: <team-namespace>     # Change: target namespace for workload
        app:
          name: <app-name>              # Change: application name (will prefix resources)
          image: <workload-image>       # Change: container image for workload
          port: <workload-port>         # Change: main application port
        exporter:
          image: <exporter-image>       # Change: Prometheus exporter image
          port: <exporter-port>         # Change: exporter metrics port
  destination:
    server: https://kubernetes.default.svc   # Usually fixed: in-cluster API endpoint
    namespace: <team-namespace>         # Match the namespace above (app.namespace)
  syncPolicy:
    automated:
      prune: true                       # Recommended: clean up removed resources automatically
      selfHeal: true                    # Recommended: auto-correct drift between Git and cluster



