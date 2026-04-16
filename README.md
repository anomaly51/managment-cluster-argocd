# Local Cluster Bootstrap: ArgoCD & GitHub Actions Runners

## Prerequisites

- A running local Kubernetes cluster
- `kubectl` configured to your local cluster
- `helm` installed
- A GitHub Personal Access Token (PAT) with `repo` and `admin:org` scopes

## 1. Install ArgoCD

```bash
helm repo add argo https://argoproj.github.io/argo-helm
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

## Configure Kilo

```bash
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.annotations.kilo\.squat\.ai/key}{"\n"}{end}'
```

and update this config

```bash
[Interface]
PrivateKey = iOCwalS17yxK4HLTGkK3KEtlGWjaw59Q/lAxA6uU7Ug=
Address = 10.5.0.2/32
DNS = 10.43.0.10, cluster.local
MTU = 1280

[Peer]
PublicKey = CeJSuzpleCOfhbb7RWOdERZJal4OWHaCuFxqsoKp4hM=
AllowedIPs = 10.42.0.0/16, 10.43.0.0/16, 10.4.0.0/16, 10.5.0.0/24
Endpoint = 192.168.1.40:51820
PersistentKeepalive = 25
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
kubectl delete pvc -n utility-database --all
```

## Setup database

connection string: jdbc:postgresql://{internal_ip_address}:5432/utility_db?sslmode=required
username: utility-admin

```bash
kubectl get secret utility-admin.utility-database.credentials.postgresql.acid.zalan.do -n utility-database -o jsonpath="{.data.password}" | base64 -d; echo
