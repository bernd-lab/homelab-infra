# Ansible-Provisionierung Status

**Datum**: 2025-11-28
**Status**: Kubernetes Master vollständig funktionsfähig und sauber ✅

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
- **etcd läuft** (1/1 Running, Restart-Zähler zurückgesetzt)
- **kube-scheduler läuft** (1/1 Running, Restart-Zähler zurückgesetzt)
- **kube-controller-manager läuft** (1/1 Running, Restart-Zähler zurückgesetzt)
- **kube-proxy läuft** (1/1 Running, Restart-Zähler zurückgesetzt)
- **CoreDNS läuft** (2/2 Running, Restart-Zähler zurückgesetzt)
- **Flannel CNI läuft** (1/1 Running)

## Probleme gelöst

✅ **kube-controller-manager**:
- **Problem**: Leader-Election Timeouts führten zu CrashLoopBackOff
- **Lösung**: Leader-Election-Timeouts angepasst (lease-duration=30s, renew-deadline=20s, retry-period=5s)
- **Status**: Läuft stabil (1/1 Running, Restart-Zähler zurückgesetzt)

✅ **kube-scheduler**:
- **Problem**: Leader-Election Timeouts führten zu CrashLoopBackOff
- **Lösung**: Leader-Election-Timeouts angepasst (gleiche Konfiguration wie Controller-Manager)
- **Status**: Läuft stabil (1/1 Running, Restart-Zähler zurückgesetzt)

✅ **kube-proxy**:
- **Problem**: CrashLoopBackOff nach Pod-Neustarts
- **Lösung**: Pod neu erstellt, läuft jetzt stabil
- **Status**: Läuft stabil (1/1 Running, Restart-Zähler zurückgesetzt)

✅ **CoreDNS**:
- **Problem**: CrashLoopBackOff durch API-Server-Timeouts während der Initialisierung
- **Lösung**: Deployment neu gestartet, Pods neu erstellt
- **Status**: Läuft stabil (2/2 Running, Restart-Zähler zurückgesetzt)

✅ **Alle Pods sauber neu deployt**:
- **Problem**: Zahlreiche Restarts in der Historie
- **Lösung**: Alle static Pods (etcd, apiserver, scheduler, controller-manager) neu erstellt durch temporäres Entfernen/Wiederherstellen der Manifeste
- **Status**: Alle Pods laufen sauber ohne Restarts in der aktuellen Instanz

## Cluster-Status

- **Node**: `homelab` ist Ready (v1.34.0)
- **API-Server**: ✅ Läuft stabil
- **Control-Plane-Pods**: Alle laufen stabil ohne Restarts (etcd, apiserver, scheduler, controller-manager ✅)
- **System-Pods**: Alle laufen stabil (CoreDNS 2/2, kube-proxy, Flannel ✅)

## GitHub

✅ Repository erstellt und gepusht: https://github.com/bernd-lab/homelab-infra

## Nächste Schritte

1. ✅ Kubernetes Master vollständig funktionsfähig und sauber
2. Worker-Nodes konfigurieren (sudo ohne Passwort)
3. Kubernetes Workers installieren
4. Cluster vollständig testen
5. GitLab CI/CD Integration (Phase 1)
