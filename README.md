# GitOps CI/CD with ArgoCD on Kubernetes

Automated Kubernetes deployments using GitOps principles — 
GitHub is the single source of truth, ArgoCD auto-syncs 
every change to the cluster.

## Architecture

GitHub Repo (manifests) → ArgoCD watches → Kubernetes cluster
     ↑                                            ↓
  Git push                                  Auto deploys

## Stack

| Tool       | Role                                      |
|------------|-------------------------------------------|
| Minikube   | Local Kubernetes cluster                  |
| ArgoCD     | GitOps continuous delivery controller     |
| GitHub     | Single source of truth for K8s manifests  |
| kubectl    | Cluster management CLI                    |
| nginx      | Demo application                          |

## Project Structure

gitops-argocd-demo/
├── k8s/
│   ├── dev/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── staging/
│   └── prod/
└── screenshots/

## Key Features

- Auto-sync — push to GitHub triggers automatic deployment
- Self-healing — ArgoCD restores deleted/modified resources
- Zero-downtime rolling updates via Kubernetes Deployment
- Multi-environment structure (dev/staging/prod)
- Least-privilege RBAC via ArgoCD project policies

## Setup

### Prerequisites
- Docker Desktop
- Minikube
- kubectl
- ArgoCD CLI

### Run it

# Start cluster
minikube start --driver=docker

# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Access UI
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Create app
argocd app create observability-app \
  --repo https://github.com/YOUR_USERNAME/gitops-argocd-demo.git \
  --path k8s/dev \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --sync-policy automated \
  --auto-prune \
  --self-heal

## GitOps Flow Demonstrated

1. Changed replicas: 2 → 3  — ArgoCD scaled automatically
2. Upgraded nginx: 1.25 → 1.26 — rolling update, zero downtime
3. Deleted pod manually — ArgoCD self-healed in seconds

## Outcomes

| Metric                  | Result              |
|-------------------------|---------------------|
| Deployment trigger      | Git push            |
| Sync detection time     | < 3 minutes         |
| Rollout strategy        | Rolling update      |
| Self-heal time          | < 30 seconds        |
| Manual kubectl applies  | Zero                |
