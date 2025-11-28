# Ansible-Provisionierung Status

**Datum**: 2025-11-28
**Status**: Kubernetes Master läuft, kube-controller-manager instabil ⚠️

## Abgeschlossen

✅ **System-Vorbereitung** (homelab):
- Swap deaktiviert
- Kernel-Module geladen
- sysctl konfiguriert

✅ **Storage-Konfiguration** (homelab):
- Mount-Points erstellt
- Verzeichnisse für Kubernetes-Datenbanken erstellt

✅ **Containerd** (homelab):
- Installiert und konfiguriert
- SystemdCgroup aktiviert

✅ **Kubernetes-Tools** (homelab):
- kubeadm, kubelet, kubectl installiert
- Repository konfiguriert

✅ **Kubernetes Master** (homelab):
- kubeadm init ausgeführt
- kubeconfig vorhanden
- **API-Server läuft stabil** (Port 6443 gebunden)
- **etcd läuft** (1/1 Running)
- **kube-scheduler läuft** (1/1 Running)
- **kube-proxy läuft** (1/1 Running)
- **CoreDNS läuft** (2/2 Running)
- **Flannel CNI läuft** (1/1 Running)

## Bekannte Probleme

⚠️ **kube-controller-manager**:
- Status: CrashLoopBackOff
- Problem: Liveness-Probe schlägt fehl (Port 10257 nicht gebunden)
- Ursache: Controller-Manager bindet nicht auf Port 10257
- Impact: Controller-Manager-Funktionalität eingeschränkt (Node-Lifecycle, ReplicaSets, etc.)
- Workaround: Cluster funktioniert grundsätzlich, aber einige Controller-Funktionen fehlen

## Cluster-Status

- **Node**: `homelab` ist Ready (v1.34.0)
- **API-Server**: ✅ Läuft stabil
- **Control-Plane-Pods**: 4/5 laufen (etcd, apiserver, scheduler ✅; controller-manager ⚠️)
- **System-Pods**: Alle laufen (CoreDNS, kube-proxy, Flannel)

## GitHub

✅ Repository erstellt und gepusht: https://github.com/bernd-lab/homelab-infra

## Nächste Schritte

1. **kube-controller-manager Problem beheben**:
   - Konfiguration prüfen (Port 10257)
   - Liveness-Probe anpassen oder Controller-Manager-Konfiguration korrigieren
2. Worker-Nodes konfigurieren (sudo ohne Passwort)
3. Kubernetes Workers installieren
4. Cluster vollständig testen
5. GitLab CI/CD Integration (Phase 1)
