# devshop-gitops

GitOps repository for DevShop — ArgoCD installation and Kubernetes manifests for GKE.

## Architecture

```
devshop-gitops (this repo)
    ↓ watched by ArgoCD
GKE Private Cluster
    └── namespace: argocd      → ArgoCD
    └── namespace: production  → DevShop app (coming soon)
    └── namespace: monitoring  → Prometheus + Grafana (coming soon)
```

## Stack

| Layer | Tool |
|---|---|
| GitOps | ArgoCD |
| Orchestration | GKE (private cluster) |
| Package manager | Helm |
| CI/CD | GitHub Actions |
| Access | Bastion host |

## Repository structure

```
devshop-gitops/
├── argocd/
│   ├── install/    → Helm values for ArgoCD installation
│   └── apps/       → ArgoCD Application manifests
├── apps/
│   └── devshop/    → Kubernetes manifests (coming soon)
└── .github/
    └── workflows/
        └── install-argocd.yml
```

## Access ArgoCD UI

```bash
# SSH tunnel + port-forward
ssh -L 8080:localhost:8080 devops@<bastion-ip>
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Open https://localhost:8080
# Get initial password:
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```

## Prerequisites

- GKE cluster running (devshop-infra)
- Bastion host configured with kubectl + helm
- GitHub Secrets: `SSH_PRIVATE_KEY`, `GOOGLE_CREDENTIALS`
- GitHub Variables: `GCP_PROJECT_ID`, `GCP_ZONE`, `BASTION_NAME`