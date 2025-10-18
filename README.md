# Streamlit K8s Infra

This repository contains Kubernetes manifests and Argo CD Application definitions for deploying the Streamlit dashboard and related infrastructure. It is designed for GitOps workflows with Argo CD and can be used with a local Minikube cluster or any Kubernetes cluster.

---

## Repository Structure

```
streamlit-k8s-infra/
├── base/                  # Raw Kubernetes manifests
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── serviceaccount.yaml
│   ├── clusterrole.yaml
│   ├── clusterrolebinding.yaml
├── apps/                  # Argo CD Application manifests
│   └── k8s-dashboard-app.yaml
└── README.md
```

- **base/** – Contains all raw Kubernetes manifests for the Streamlit dashboard and related resources.  
- **apps/** – Contains Argo CD `Application` manifests that point to the resources in `base/`.  

---

## Prerequisites

- [Minikube](https://minikube.sigs.k8s.io/docs/) or any Kubernetes cluster  
- [kubectl](https://kubernetes.io/docs/tasks/tools/)  
- [Argo CD](https://argo-cd.readthedocs.io/en/stable/) installed on the cluster  
- Optional: Ingress controller (for exposing services)  

---

## Deployment Steps

### 1. Create Namespace

```bash
kubectl create namespace streamlit-monitoring
```

### 2. Apply Argo CD Application

```bash
kubectl apply -f apps/k8s-dashboard-app.yaml -n argocd
```

### 3. Sync Application

- Using the Argo CD UI or CLI:

```bash
argocd login <ARGOCD_SERVER>
argocd app sync k8s-dashboard
```

> **Note:** The Argo CD Application is configured with **automatic sync** and **self-healing**, meaning:
> - Any changes in the Git repository will automatically be applied to the cluster.
> - Resources deleted from Git will be pruned automatically.
> - Out-of-sync resources in the cluster will be reverted to match Git.

### 4. Access Dashboard

- If using Minikube with `nip.io` configured in the ingress:

```
http://k8s-streamlit-dash.<MINIKUBE_IP>.nip.io
```

- Enable Minikube ingress addon if not already enabled:

```bash
minikube addons enable ingress
```

---

## GitOps Workflow

This repository is designed for a **single infra repository** containing all Kubernetes manifests and Argo CD Applications. In a typical workflow:

1. **Application Repo** – (Optional separate repository) Builds and pushes Docker images for the Streamlit app.  
2. **Infra Repo (this repo)** – Argo CD watches this repository and syncs all manifests to the cluster automatically.  

---

## Notes

- `.idea/` and `.venv/` are ignored via `.gitignore` for local development.  
- Argo CD `Application` manifests are stored in `apps/` to avoid recursive application loops.  
- All manifests in `base/` can be applied manually if needed:

```bash
kubectl apply -f base/ -n streamlit-monitoring
```

---

## References

- [Argo CD Documentation](https://argo-cd.readthedocs.io/en/stable/)  
- [Kubernetes Ingress NGINX](https://kubernetes.github.io/ingress-nginx/)  
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)  
- [Streamlit Deployment Examples](https://docs.streamlit.io/)
