# Kubernetes Infrastructure for Jenkins & Argo CD

This directory contains Helm values and Kubernetes manifests to configure and deploy Jenkins and Argo CD in a Kubernetes cluster.

## 📁 Directory Structure

```
k8s-infra/
├── argocd/
│   └── values.yaml               # Helm values for Argo CD configuration
└── jenkins/
    ├── jenkins-rbac.yaml         # RBAC permissions for Jenkins service account
    └── values.yaml               # Helm values for Jenkins configuration
```

---

## ⚙️ `argocd/values.yaml`

This file customizes the Argo CD installation using Helm. It may define:

- Admin credentials
- TLS configuration
- Server service type (e.g., `LoadBalancer`)
- Repository credentials or bootstrap settings
- UI and CLI options

> Edit this file to configure how Argo CD is exposed and integrated into your CI/CD workflows.

---

## ⚙️ `jenkins/values.yaml`

This file contains custom Helm values for deploying Jenkins with:

- Custom admin user and password
- Service type (e.g., `LoadBalancer` for external access)
- Jenkins Configuration as Code (JCasC) location
- Plugin installation
- Agent configuration (Docker socket mount, etc.)

> Use this file to control Jenkins' deployment behavior and CI environment.

---

## 🔐 `jenkins/jenkins-rbac.yaml`

This manifest defines the required Role-Based Access Control (RBAC) permissions for Jenkins to interact with Kubernetes resources. It typically includes:

- **Service Account**: Used by Jenkins agents
- **Roles**: Grants access to namespaces, pods, logs, etc.
- **RoleBindings**: Binds the Jenkins service account to the appropriate roles

> Make sure this file is applied before launching Jenkins agents in Kubernetes.

---

## 🚀 How to Use

1. Customize `values.yaml` files for Argo CD and Jenkins based on your environment.
2. Deploy Jenkins and Argo CD using Helm:

```bash
# Argo CD
helm upgrade --install argocd argo/argo-cd -n argocd --create-namespace -f k8s-infra/argocd/values.yaml

# Jenkins
kubectl apply -f k8s-infra/jenkins/jenkins-rbac.yaml
helm upgrade --install jenkins jenkins/jenkins -n jenkins --create-namespace -f k8s-infra/jenkins/values.yaml
```

---

## 📜 License

MIT

---

## 🙋‍♂️ Maintainer

**Lolek / pkonieczny321**

Feel free to fork, contribute, or ask questions!
