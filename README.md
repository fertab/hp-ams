# hp-ams Helm Chart

This is a Helm Chart for deploying `hp-ams` in a Kubernetes cluster using a `DaemonSet`. This chart includes optional configurations for RBAC, SecurityContextConstraints (SCC), and other customizations.

## 📌 Requirements

- Kubernetes 1.20+
- Helm 3+
- Proper permissions to create `DaemonSets`, `ServiceAccounts`, `ClusterRoleBindings`, and `SecurityContextConstraints` (SCC in OpenShift)

---

## 🚀 Installation

### **1️⃣ Add the repository (optional)**
If the chart is available in a Helm repository, you can add it with:
```sh
helm repo add myrepo https://example.com/helm-charts
helm repo update
```

### **2️⃣ Install the Chart**
If the chart is in a Helm repository:
```sh
helm install hp-ams myrepo/hp-ams
```
If you have it locally:
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
  repository: "my-repo/image"
  tag: "latest"

nodeSelector:
  kubernetes.io/hostname: "node-1"

rbac:
  enabled: true
```

### **Install with custom values**
```sh
helm install hp-ams . --set rbac.enabled=false
```

---

## 🛑 Uninstallation
To remove the `hp-ams` installation:
```sh
helm uninstall hp-ams
```
If some resources are not removed (such as ServiceAccounts, RBAC, or SCC), you can delete them manually:
```sh
kubectl delete serviceaccount hp-ams-sa -n default
kubectl delete clusterrolebinding hp-ams-cluster-admin
kubectl delete rolebinding bind-hostpath -n default
kubectl delete scc allow-hostpath
```

To delete all related resources:
```sh
kubectl delete all --selector=app=hp-ams -n default
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
- [Kubernetes API Reference](https://kubernetes.io/docs/reference/)
- [OpenShift Security Context Constraints (SCC)](https://docs.openshift.com/container-platform/latest/authentication/managing-security-context-constraints.html)

---

🚀 **Ready to deploy `hp-ams` efficiently and flexibly in Kubernetes!**

