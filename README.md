helm repo add argo <https://argoproj.github.io/argo-helm>
helm repo update
kubectl apply -f bootstrap/app-of-apps.yaml

### 🔐 Kilo VPN Client Setup

**1. Generate Keys (Local)**

```bash
brew install wireguard-tools
wg genkey | tee privatekey | wg pubkey > publickey
```

**2. Update Cluster (GitOps)**
Insert your `publickey` into `apps/kilo/templates/peer.yaml` and push to Git. ArgoCD will auto-sync.

**3. Generate Config & Connect (Local)**

```bash
go install github.com/squat/kilo/cmd/kgctl@latest
kgctl showconf peer <PEER_NAME> > <PEER_NAME>.conf
```

* Edit `<PEER_NAME>.conf`: Add `PrivateKey = <YOUR_PRIVATE_KEY>` under the `[Interface]` section.
* Import `<PEER_NAME>.conf` into the WireGuard app and activate.
