# Stock Portfolio Tracker – GitOps Repository

This repository contains the **Kubernetes manifests** and **ArgoCD configuration** required to deploy and manage the stock-portfolio-tracker application on your Kubernetes cluster using a GitOps workflow.

---

## 📦 What’s in this Repository

| Path/File                  | Purpose                                      |
|----------------------------|----------------------------------------------|
| `/k8s/deployment.yaml`     | Kubernetes Deployment spec for the app       |
| `/k8s/service.yaml`        | Cluster networking (Service)                 |
| `/k8s/ingress.yaml`        | Ingress resource w/ (optional) TLS           |
| `argocd_application.yaml`  | ArgoCD Application definition                |
| `secrets/`                 | (Optional) K8s secrets, reference templates  |

---

## 🚀 GitOps Workflow

1. **ArgoCD monitors this repo:** Any changes pushed here (new image versions, configuration updates) are automatically applied to the cluster.
2. **CI/CD in app repo:** The stock-portfolio-tracker repo builds and pushes new Docker images and automatically bumps image tags in a manifest here using your GitHub Actions pipeline.
3. **Sync/apply via ArgoCD:**  
   - ArgoCD detects changes, syncs the manifests, and updates your live application.

---

## 🛠️ Deployment Instructions

**Pre-requisites:**
- Kubernetes cluster (K3s, kind, AKS/EKS/GKE, etc.)
- ArgoCD installed and running in the cluster

### 1. Register This Repo With ArgoCD

- Add this repo as a new ArgoCD Application, or simply apply `argocd_application.yaml`:
`kubectl apply -f argocd_application.yaml`

### 2. Structure
```
stock-portfolio-gitops/
├── README-gitops.md
├── argocd_application.yaml
└── k8s/
├── deployment.yaml
├── service.yaml
└── ingress.yaml
```

### 3. Image Tag Updates

- The image reference in `deployment.yaml` should be updated automatically by your app repo’s CI on new builds.
- Manual updates: Edit `k8s/deployment.yaml`:
```
containers:
- name: portfolio-tracker
  image: <registry>/<username>/stock-portfolio-tracker:<new-tag>
```

---

## 🔐 Managing Secrets

**Never commit real secrets directly!**
- Store secrets as Kubernetes Secrets, using templates or sealed secrets.
- Reference secrets in manifests as:

```
env:
- name: PUBLIC_API_KEY
valueFrom:
secretKeyRef:
name: portfolio-secrets
key: public-api-key
```

- To create secrets:
```
kubectl create secret generic portfolio-secrets \
  --from-literal=public-api-key=YOUR_API_KEY_HERE \
  --from-literal=db-password=YOUR_SECURE_PASSWORD_HERE \
  -n portfolio-tracker
```


---

## ⚙️ Syncing and Monitoring

- **Auto-sync:** Enabled by default in `argocd_application.yaml`. ArgoCD will automatically apply updates.
- **Force sync:**  
`argocd app sync stock-portfolio-tracker`
- **Check status:**  
`argocd app get stock-portfolio-tracker`


---

## 📝 Best Practices

- Use Pull Requests to stage and review changes (image updates, config changes)
- Keep all deployment/environment config here—no code or business logic
- Use ArgoCD RBAC to protect production deployments

---

## 📒 Further Reading

- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Kubernetes Manifests](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)

---

**Contact:**  
For infrastructure/deployment support, open an issue or contact your Kubernetes/GitOps administrator.
