# ⎈ Helm: The Kubernetes Package Manager

> **The Gist:** Helm is the YAML modeling engine for K8s. 
> * **Charts:** Think of these as Classes.
> * **Values.yaml:** Think of these as the Variables you pass to the class.
> * **Release:** The actual Object (instance) running in the cluster.
> * **Why?** To stop maintaining three identical sets of YAML for Dev, Staging, and Prod.

Helm is the "apt-get" or "pip" of Kubernetes. It allows you to package, share, and deploy applications as a single unit called a **Chart**.

---

## 🏗️ Helm 3 Architecture (The Modern Standard)
The biggest shift in Helm's history was the move from Helm 2 to Helm 3.

* **Helm 2 (Legacy):** Used a server-side component called **Tiller**. It was a security nightmare because Tiller had full admin rights to the cluster.
* **Helm 3 (Current):** **Tiller is gone.** The Helm client talks directly to the Kubernetes API using your local `kubeconfig`. It is simpler, safer, and follows K8s native security.

---

## 📦 Key Concepts
* **Chart:** A bundle of YAML templates and a `values.yaml` file.
* **Values:** The configuration file (`values.yaml`) where you define variables (e.g., `replicaCount: 3`).
* **Release:** An instance of a chart running in your cluster. You can install the same "Nginx" chart 5 times; each becomes a unique "Release."
* **Repository:** A place to store and share charts (like Artifact Hub).

---

## 🛠️ Essential Commands
| Action | Command |
| :--- | :--- |
| **Add Repo** | `helm repo add [name] [url]` |
| **Search** | `helm search repo [keyword]` |
| **Install** | `helm install [release-name] [chart-name] -f values.yaml` |
| **Upgrade** | `helm upgrade [release-name] [chart-name]` |
| **Uninstall** | `helm uninstall [release-name]` |

---

**Pro-Tip (DRY Principle):** Use Helm to avoid repeating yourself. One chart can power `Dev`, `Staging`, and `Prod` just by switching the values file.