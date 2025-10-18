# Streamlit K8s Infra

This repository contains Kubernetes manifests and Argo CD Application definitions for deploying the Streamlit dashboard 
and related infrastructure. It is designed for GitOps workflows with Argo CD and can be used with a local Minikube 
cluster or any Kubernetes cluster.

---

## Repository Structure
streamlit-k8s-infra/
├── base/ # Raw Kubernetes manifests
│ ├── deployment.yaml
│ ├── service.yaml
│ ├── ingress.yaml
│ ├── serviceaccount.yaml
│ ├── clusterrole.yaml
│ ├── clusterrolebinding.yaml
├── apps/ # Argo CD Application manifests
│ └── k8s-dashboard-app.yaml
└── README.md

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
kubectl create namespace streamlit-monitoring

### 2. Apply Argo CD Application
kubectl apply -f apps/k8s-dashboard-app.yaml -n argocd

### 3. Sync Application
Using the Argo CD UI or CLI:
#### 1. argocd login <ARGOCD_SERVER> and sync the application
#### 2. argocd app sync k8s-dashboard

Note: The Argo CD Application is configured with automatic sync and self-healing, meaning:
1. Any changes in the Git repository will automatically be applied to the cluster.
2. Resources deleted from Git will be pruned automatically.
3. Out-of-sync resources in the cluster will be reverted to match Git. 
4. Argo CD reconciliation timeout is 180 seconds- https://argo-cd.readthedocs.io/en/stable/operator-manual/high_availability/

### 4. Access the Streamlit Dashboard
- if using Minikube with Ingress, enable the ingress addon - minikube addons enable ingress
- If using an Ingress controller, access the dashboard via the configured hostname in the ingress resource file - k8s-dashboard-ingress.yaml.
- If using NodePort or LoadBalancer, retrieve the service URL accordingly.