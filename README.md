helm repo add argo <https://argoproj.github.io/argo-helm>
helm repo update
k create namespace argocd
helm dependency update apps/argocd
helm install argocd apps/argocd -n argocd
k apply -f bootstrap/app-of-apps.yaml
k -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
-5iWkjbjAsfaMXqs
