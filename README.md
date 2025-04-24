# hp-ams Helm Chart

This is a Helm Chart for deploying `hp-ams` in an Openshift cluster (v4.14) or any k8 cluster that is running over an HPE iLO 6 baremetal infrastructure using a `DaemonSet`. This chart includes required configurations for RBAC, SecurityContextConstraints (SCC), and other customizations.

## 📌 Requirements

- Access to a Bastion node if it's necessary
- OpenShift 4.14
- HPE iLO 6
- Helm 3+
- Proper permissions to create `DaemonSets`, `ServiceAccounts`, `ClusterRoleBindings`, and `SecurityContextConstraints` (SCC in OpenShift)

---

## 🚀 Installation

### **1️⃣ Download the Chart from GitHub**
Clone the repository where the Helm Chart is hosted:
```sh
git clone https://github.com/fertab/hp-ams.git
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
## 🐳 Containerfile (Dockerfile)

### The **HP AMS** container image is available for download from **quay.io** at the following repository:
```
quay.io/fernando_taboada/hp_ams
```
### However, if you prefer to build your own image, you can use the following Containerfile and upload it to your preferred repository:

```FROM registry.access.redhat.com/ubi9/ubi:latest

ENV AMS_VERSION=3.8.0-1869.3
ENV AMS_RPM_URL=https://downloads.linux.hpe.com/SDR/repo/spp-gen11/2025.01.00.00/packages/amsd-${AMS_VERSION}.rhel9.x86_64.rpm

# Install required dependencies
RUN yum install -y \
    glibc \
    bash \
    coreutils \
    libstdc++ \
    systemd \
    pciutils \
    lsof \
    curl \
    rpm-libs \
    rpm --allowerasing && \
    yum clean all

# Download and install AMSD
RUN curl -fLo /tmp/amsd.rpm ${AMS_RPM_URL} && \
    rpm -Uvh /tmp/amsd.rpm && \
    rm -f /tmp/amsd.rpm

# Create a systemd service for AMSD
RUN echo -e "[Unit]\nDescription=HPE AMS Daemon\nAfter=network.target\n\n[Service]\nExecStart=/sbin/amsd\nRestart=always\nType=simple\n\n[Install]\nWantedBy=multi-user.target" > /etc/systemd/system/amsd.service

# Start systemd as the default process
CMD ["/usr/sbin/init"]
```

### Then follow the next steps in your cli (This example use quay.io as the main repository)

```
podman login quay.io
```
```
username: your_username
```
```
password: your_password
```
```
export AMS_VERSION=3.8.0-1869.3
```
```
podman build -t hp-ams:${AMS_VERSION} -f HP-AMS.containerfile .
```
```
podman tag hp-ams:${AMS_VERSION} quay.io/your_repo/hp_asm:${AMS_VERSION}
```
```
podman push quay.io/your_repo/hp_asm:${AMS_VERSION}
```
---
## 📖 References
- [Helm Documentation](https://helm.sh/docs/)

---

🚀 **Ready to deploy `hp-ams`**

