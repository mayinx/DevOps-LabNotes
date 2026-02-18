# 🛠️ K8s Day-to-Day: The DevOps Task List

This guide covers the 10 most common tasks a DevOps engineer performs in a production Kubernetes environment.

---

## 🏗️ I. Deployment & Orchestration

### 1. Creating & Templating Manifests (YAML Management)
* **Goal:** Avoid hardcoding values and make deployments reusable.
* **How:** Transition from static YAML to **Helm Charts** or **Kustomize**.
* **Command:** `helm create [app-name]` (Scaffold a chart) or `kubectl apply -k ./overlays/prod` (Kustomize).
* **Pro-Tip:** 40% of DevOps work is managing these templates to support Dev, Staging, and Prod environments.

### 2. Rolling Out New Microservices
* **Goal:** Deploy a new app version without downtime.
* **How:** Update the `image` tag in your Deployment YAML.
* **Command:** `kubectl set image deployment/my-app container-name=my-repo/my-app:v2.0`
* **YAML Key:** `spec.strategy.type: RollingUpdate`

### 3. Blue/Green & Canary Deployments
* **Goal:** Route a small % of traffic to a new version to test stability.
* **How:** Use an **Ingress** with "Canary" annotations (if using Nginx Ingress).
* **YAML (Ingress):**
    ```yaml
    annotations:
      nginx.ingress.kubernetes.io/canary: "true"
      nginx.ingress.kubernetes.io/canary-weight: "10"
    ```

---

## 🛡️ II. Reliability & Scaling

### 4. Setting Resource "Guardrails"
* **Goal:** Prevent apps from crashing the Node (Noisy Neighbor).
* **How:** Define `resources` in the Container spec.
* **YAML (Pod):**
    ```yaml
    resources:
      requests:
        cpu: "250m"  # Guaranteed Floor
        memory: "64Mi"
      limits:
        cpu: "500m"  # Hard Ceiling
        memory: "128Mi"
    ```

### 5. Configuring Autoscaling (HPA & VPA)
* **Goal:** Scale Pods (Horizontal) or Resize Pods (Vertical) based on load.
* **Command (HPA):** `kubectl autoscale deployment my-app --cpu-percent=80 --min=2 --max=10`
* **Logic:** Use **HPA** for web traffic spikes; use **VPA** for memory-hungry background jobs.
* **Requirement:** Requires **Metrics Server** to be installed.

---

## 🕸️ III. Networking & Security

### 6. Managing TLS/SSL (HTTPS) & Ingress
* **Goal:** Secure the "Front Door" and route traffic to the right Service.
* **How:** Add a `tls` section to your Ingress and use a Secret to store the cert.
* **Command:** `kubectl create secret tls my-tls-secret --cert=path/to/cert --key=path/to/key`

### 7. Handling Secrets and ConfigMaps
* **Goal:** Securely inject passwords/API keys into containers.
* **How:** Store sensitive data in a Secret; store config URLs in a ConfigMap.
* **Command:** `kubectl create secret generic db-pass --from-literal=password=datascientest1234`

### 8. RBAC (Access Control)
* **Goal:** Enforce "Least Privilege" for users and automation tools.
* **How:** Create a `Role` (permissions) and a `RoleBinding` (connecting it to a user).
* **Command:** `kubectl create rolebinding dev-view --clusterrole=view --user=daniel --namespace=dev`

---

## 🩺 IV. Observability & Infrastructure

### 9. Log Aggregation & Monitoring (The "Doctor")
* **Goal:** Detect slowness or errors before users do.
* **How:** Integrate cluster with Prometheus/Grafana or an ELK stack.
* **Command:** `kubectl top pods` (Check real-time usage) or `kubectl logs -l app=my-service --tail=20`.

### 10. Storage Provisioning (PV/PVC)
* **Goal:** Give a Database a permanent "Hard Drive."
* **YAML (PVC):**
    ```yaml
    kind: PersistentVolumeClaim
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
    ```

---

## 🏁 Summary Table
| Category | Focus | Frequency |
| :--- | :--- | :--- |
| **Orchestration** | YAML, Helm, Rollouts | Daily |
| **Reliability** | Scaling, Limits, Probes | Weekly |
| **Networking** | Ingress, DNS, TLS | Setup Phase |
| **Observability** | Logs, Describe, Metrics | On Incident |
| **Security** | Secrets, RBAC | Setup/Audit |