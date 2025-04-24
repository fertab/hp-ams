# hp-ams Helm Chart

This is a Helm Chart for deploying `hp-ams` in an Openshift cluster (v4.14) or any Kubernetes cluster that is running over an HPE iLO 6 baremetal infrastructure using a `DaemonSet`. This chart includes required configurations for RBAC, SecurityContextConstraints (SCC), and other customizations.

## 📌 Requirements

- OpenShift 4.14 (or Kub)
- HPE iLO 6
- Helm 3+
- Proper permissions to create `DaemonSets`, `ServiceAccounts`, `ClusterRoleBindings`, and `SecurityContextConstraints` (SCC in OpenShift)

---

## 🚀 Installation

### **1️⃣ Download the Chart from GitHub**
Clone the repository where the Helm Chart is hosted:
```sh
git clone https://repo-git/path-to-repo/hp-ams.git
cd hp-ams
```

### **2️⃣ Install the Chart**
Once inside the `hp-ams` directory, install the chart with:
```sh
helm install hp-ams .
```

---

## ⚙️ Configuration
You can customize the installation by modifying `values.yaml` or passing values using `--set`.

### **Example `values.yaml`**
```yaml
serviceAccount:
  name: "hp-ams-sa"

daemonSet:
  name: "hp-ams"
  namespace: "default"
  privileged: false
  volumePath: "/var/lib/ams"

image:
  repository: "repo/image"
  tag: "latest"

# Optional: If you want to deploy to specific nodes, use nodeSelector
# To deploy on all nodes, remove this section or set it to an empty value
nodeSelector:
  kubernetes.io/hostname: "node-1"

rbac:
  enabled: true
```

### **Install with custom values**
```sh
helm install hp-ams . --set rbac.enabled=false
```

If you want to deploy the `DaemonSet` across all nodes in the cluster, remove the `nodeSelector` section from `values.yaml` or set it to `{}`:
```sh
helm install hp-ams . --set nodeSelector={}
```

---

## 🛑 Uninstallation
To remove the `hp-ams` installation:
```sh
helm uninstall hp-ams
```
If some resources are not removed (such as ServiceAccounts, RBAC, or SCC), you can delete them manually:
```sh
oc delete serviceaccount hp-ams-sa -n default
oc delete clusterrolebinding hp-ams-cluster-admin
oc delete rolebinding bind-hostpath -n default
oc delete scc allow-hostpath
```

To delete all related resources:
```sh
oc delete all --selector=app=hp-ams -n default
```

---

## 🛠 Development and Testing
If you need to test the templates without deploying:
```sh
helm template hp-ams .
```
To test the installation in dry-run mode:
```sh
helm install hp-ams . --dry-run --debug
```

---

## 📖 References
- [Helm Documentation](https://helm.sh/docs/)

---

🚀 **Ready to deploy `hp-ams`**

