# Ansible-Playbooks Testen und Ausführen

**Status**: Phase 0 - Manuelle Execution

## Voraussetzungen

### Auf Workstation (Windows Desktop oder Fedora Laptop)

1. **Ansible installiert** (siehe `docs/ansible-setup-workstations.md`)
2. **SSH-Keys vorhanden**:
   - `~/.ssh/infra_ed25519` (für Master-Node)
   - `~/.ssh/ansible_pi_key` (für Worker-Nodes)
3. **Repository geklont**:
   ```bash
   git clone https://github.com/bernd-lab/homelab-infra.git
   cd homelab-infra/infrastructure/ansible
   ```

## Test-Schritte

### 1. Verbindung testen

```bash
# Alle Hosts pingen
ansible all -m ping

# Erwartete Ausgabe:
# homelab | SUCCESS => {...}
# k8s-worker-8gb | SUCCESS => {...}
# k8s-worker-15gb | SUCCESS => {...}
# k8s-worker-4gb | SUCCESS => {...}
# k8s-worker-1gb | SUCCESS => {...}
```

**Falls Fehler**:
- SSH-Keys prüfen: `ls -la ~/.ssh/`
- SSH-Verbindung manuell testen: `ssh -i ~/.ssh/infra_ed25519 bernd@192.168.178.54`
- Sudo ohne Passwort prüfen: `ssh -i ~/.ssh/infra_ed25519 bernd@192.168.178.54 "sudo whoami"`

### 2. Inventory prüfen

```bash
# Zeige alle Hosts
ansible-inventory --list

# Zeige nur Master
ansible-inventory --host homelab

# Zeige nur Workers
ansible k8s_workers --list-hosts
```

### 3. Playbook-Syntax prüfen

```bash
# System-Vorbereitung
ansible-playbook playbooks/system-prep.yml --syntax-check

# Storage
ansible-playbook playbooks/storage.yml --syntax-check

# Kubernetes Master
ansible-playbook playbooks/k8s-master.yml --syntax-check

# Kubernetes Workers
ansible-playbook playbooks/k8s-workers.yml --syntax-check
```

### 4. Dry-Run (Check-Modus)

```bash
# System-Vorbereitung (zeigt was gemacht würde, ohne es zu tun)
ansible-playbook playbooks/system-prep.yml --check

# Storage
ansible-playbook playbooks/storage.yml --check
```

## Ausführung (Schritt für Schritt)

### Schritt 1: System-Vorbereitung

**Wichtig**: Dies muss auf ALLEN Hosts ausgeführt werden (Master + Workers)

```bash
ansible-playbook playbooks/system-prep.yml
```

**Erwartete Änderungen**:
- Swap deaktiviert
- Kernel-Module (overlay, br_netfilter) geladen
- sysctl konfiguriert

**Verifizierung**:
```bash
# Auf jedem Host prüfen
ansible all -a "swapon --show"  # Sollte leer sein
ansible all -a "lsmod | grep br_netfilter"  # Sollte br_netfilter zeigen
```

### Schritt 2: Storage-Konfiguration

**Wichtig**: Nur auf Master-Node (homelab)

```bash
ansible-playbook playbooks/storage.yml
```

**Erwartete Änderungen**:
- Mount-Points erstellt (/mnt/k8s-db, etc.)
- SSD gemountet
- fstab-Einträge hinzugefügt
- Verzeichnisse für Kubernetes-Datenbanken erstellt

**Verifizierung**:
```bash
# Auf Master prüfen
ansible k8s_masters -a "mount | grep /mnt/k8s-db"
ansible k8s_masters -a "ls -la /mnt/k8s-db/"
```

### Schritt 3: Kubernetes Master

**Wichtig**: Nur auf Master-Node, dauert mehrere Minuten

```bash
ansible-playbook playbooks/k8s-master.yml
```

**Erwartete Änderungen**:
- Containerd installiert und konfiguriert
- Kubernetes-Tools installiert (kubeadm, kubelet, kubectl)
- kubeadm init ausgeführt
- kubeconfig für Benutzer erstellt
- Flannel CNI installiert

**Verifizierung**:
```bash
# Auf Master prüfen
ansible k8s_masters -a "kubectl get nodes"
ansible k8s_masters -a "kubectl get pods -n kube-system"
```

**Wichtig**: Nach diesem Schritt den `kubeadm join` Command notieren!

### Schritt 4: Kubernetes Workers

**Wichtig**: Nur auf Worker-Nodes, nach Master-Installation

```bash
ansible-playbook playbooks/k8s-workers.yml
```

**Erwartete Änderungen**:
- Containerd installiert und konfiguriert
- Kubernetes-Tools installiert
- kubeadm join ausgeführt (automatisch)

**Verifizierung**:
```bash
# Auf Master prüfen (alle Nodes sollten Ready sein)
ansible k8s_masters -a "kubectl get nodes"
```

## Alle auf einmal (NICHT empfohlen für erste Ausführung)

```bash
ansible-playbook playbooks/site.yml
```

**Warnung**: Führt alle Playbooks nacheinander aus. Nur verwenden, wenn alle einzelnen Schritte erfolgreich waren!

## Troubleshooting

### Problem: "Permission denied (publickey)"
**Lösung**: SSH-Keys prüfen, Permissions prüfen (600)

### Problem: "sudo: a password is required"
**Lösung**: Auf Ziel-Hosts `sudo` ohne Passwort für `bernd` konfigurieren

### Problem: "Failed to connect to host"
**Lösung**: Netzwerk-Verbindung prüfen, Firewall prüfen

### Problem: "kubeadm init failed"
**Lösung**: Logs prüfen: `journalctl -xeu kubelet`

### Problem: "kubeadm join failed"
**Lösung**: Token prüfen, Master-Node erreichbar?

## Nächste Schritte nach erfolgreicher Ausführung

1. ✅ Kubernetes Cluster läuft
2. ✅ Storage konfiguriert
3. → GitLab Deployment (manuell mit kubectl)
4. → GitLab Mirror Setup
5. → Phase 1: GitLab CI/CD Integration

