helm repo add argo <https://argoproj.github.io/argo-helm>
helm repo update
k apply -f bootstrap/app-of-apps.yaml
k -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
