# Architektur-Übersicht

**Datum**: 2025-11-27

## Cluster-Architektur

### Master-Node
- **Hostname**: homelab
- **IP**: 192.168.178.54
- **Architektur**: amd64
- **Ressourcen**: 4 Cores, 32Gi RAM

### Worker-Nodes
- **k8s-worker-8gb** (192.168.178.91): ARM64, 8Gi RAM
- **k8s-worker-15gb** (192.168.178.95): ARM64, 15Gi RAM
- **k8s-worker-4gb** (192.168.178.90): ARM64, 4Gi RAM
- **k8s-worker-1gb** (192.168.178.92): ARM64, 1Gi RAM

## Storage-Strategie (Hybrid)

- **SSD (sda)**: ext4 für Datenbanken
- **HDDs (sde1, sdf1)**: NTFS für Media
- **sdd**: ext4 für neue Daten
- **sdb1**: ZFS-Pool (TrueNAS System-Daten)

## Netzwerk

- **Router**: FritzBox (192.168.178.1)
- **DNS**: Pi-hole (192.168.178.90, 92)
- **Domains**: *.k8sops.online

