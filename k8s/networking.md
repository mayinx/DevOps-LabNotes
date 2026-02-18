# 🕸️ K8s Internal Networking: DNS, ClusterIP, & Ingress

This guide covers how traffic flows *inside* the cluster and the specific logic used to route external requests to internal pods.

---

## 📞 1. The Internal Phonebook (CoreDNS)
In K8s, IP addresses are "disposable." You should **never** hardcode an IP. Instead, use the Service Name.

* **Standard Format:** `[service-name].[namespace].svc.cluster.local`
* **Short Format:** If both pods are in the same namespace, just use `[service-name]`.

**The Workflow:**
1. Pod A wants to talk to `api-service`.
2. Pod A asks the **CoreDNS** pod: "What is the IP for `api-service`?"
3. CoreDNS returns the **ClusterIP** of the Service.

---

## 🛡️ 2. ClusterIP: The Virtual Guard
`ClusterIP` is the default Service type. It is a stable, virtual IP that lives *only* inside the cluster.

* **Stable Entrance:** Even if 100 pods behind the service die and get replaced, the `ClusterIP` stays the same.
* **Load Balancing:** It automatically spreads traffic across all healthy Pods listed in the `endpoints`.

---

## 🚪 3. Ingress: The Smart Gateway
While a Service (LoadBalancer) gives you an IP, an **Ingress** gives you **URLs**. It acts as a Reverse Proxy (like Nginx) at the edge of your cluster.

| Feature | Service (LoadBalancer) | Ingress |
| :--- | :--- | :--- |
| **Routing** | One IP per Service. | One IP for *many* Services. |
| **Logic** | Simple Port forwarding. | Path-based (`/api`) or Host-based (`api.com`). |
| **Cost** | Expensive (one Cloud LB per service). | Cheap (one Cloud LB for the whole cluster). |

![K8s Internal Traffic Path](../images/k8s-traffic-path.png)
*Figure 2: The end-to-end journey from external request to internal pod.*

---

## 🛠️ Networking Troubleshooting Kit

### 🧪 The "Is DNS Working?" Test
If your pods can't find your service, check if the internal DNS is resolving. Run this from your host terminal:
`kubectl run dns-test --image=busybox:1.28 -it --rm -- nslookup [service-name]`

### 🎯 The "Endpoint Check" (The #1 Failure Point)
If DNS works but the connection times out, check if the Service actually "sees" your pods:
`kubectl get ep [service-name]`

* **Empty?** Your Pod labels don't match the Service selector.
* **Has IPs?** The network is fine; the issue is likely the App's port binding (`0.0.0.0`).

---

## 🗺️ The Traffic Flow Recap
**External User** → `api.myapp.com` (DNS)  
  → **Ingress Controller** (Routes based on `/api`)  
  → **Service** (ClusterIP)  
  → **Pod** (The Application)

---

**Pro-Tip (The FQDN):** If you are trying to reach a service in a *different* namespace (e.g., from `dev` to `prod`), you must use the Full Name: `service-name.namespace-name.svc.cluster.local`.