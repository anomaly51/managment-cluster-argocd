# Local Cluster Bootstrap: ArgoCD & GitHub Actions Runners

## Prerequisites

- A running local Kubernetes cluster
- `kubectl` configured to your local cluster
- `helm` installed
- A GitHub Personal Access Token (PAT) with `repo` and `admin:org` scopes

## 1. Install ArgoCD

```bash
helm repo add argo [https://argoproj.github.io/argo-helm](https://argoproj.github.io/argo-helm)
helm repo update

helm install argocd argo/argo-cd \
  --namespace argocd \
  --create-namespace \
  --wait
```

## Configure GitHub Authentication

```bash
kubectl create namespace github-actions-runners

kubectl create secret generic github-auth-secret \
  --namespace=github-actions-runners \
  --from-literal=github_token="YOUR_GITHUB_PAT_HERE"
```

## Bootstrap the Cluster

```bash
kubectl apply -f bootstrap/applicationset.yaml
```

## Access ArgoCD UI

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

kubectl port-forward svc/argocd-server -n argocd 8080:443
Username: admin
URL: <https://localhost:8080>
```

## Verify Runners

```bash
kubectl get pods -n github-actions-runners

```
