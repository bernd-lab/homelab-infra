# Homelab Infrastructure

**Erstellt**: 2025-11-27
**Status**: In Aufbau
**Zweck**: Kompletter Neuaufbau des Homelabs mit Best Practices

## Vision

1. **Private Services**: Jellyfin für Media-Konsum
2. **CKA-Lernen**: Kubernetes-Zertifikat vorbereiten
3. **DevOps-Praxis**: Best Practices für Rechenzentrum ausprobieren
4. **KI-Optimierung**: Infrastruktur für optimale Zusammenarbeit mit KI-Assistenten

## Services

- **GitLab**: GitOps üben, Code-Hosting, CI/CD
- **Jellyfin**: Media-Server im Heimnetz
- **Pi-hole**: DNS + Ad-Blocking
- **ArgoCD**: GitOps-Deployment
- **Netbox**: CMDB für Autodiscovery/Inventory, KI-Kontext

## Repository-Struktur

```
homelab-infra/
├── infrastructure/kubernetes/
├── services/ (GitLab, Netbox, Jellyfin, etc.)
├── gitops/argocd/applications/
├── ansible/ (Inventory, Playbooks)
└── docs/
```

## GitOps-Workflow

GitHub (Single Source of Truth) → GitLab (CI/CD) → ArgoCD → Kubernetes

