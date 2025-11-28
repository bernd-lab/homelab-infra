# Ansible Infrastructure Provisioning

**Zweck**: Automatisierte Infrastruktur-Provisionierung für Homelab Cluster

## Voraussetzungen

### Auf Workstation (Windows Desktop oder Fedora Laptop)

1. **Ansible installieren**:
   - Windows (WSL2): `sudo apt install ansible`
   - Fedora: `sudo dnf install ansible`

2. **SSH-Keys kopieren**:
   - `~/.ssh/infra_ed25519` (für Master-Node)
   - `~/.ssh/ansible_pi_key` (für Worker-Nodes)

3. **Repository klonen**:
   ```bash
   git clone https://github.com/bernd-lab/homelab-infra.git
   cd homelab-infra/infrastructure/ansible
   ```

## Verwendung

### Phase 0: Manuelle Execution (VOR GitLab)

#### System-Vorbereitung
```bash
ansible-playbook playbooks/system-prep.yml
```

#### Storage-Konfiguration
```bash
ansible-playbook playbooks/storage.yml
```

#### Kubernetes Master
```bash
ansible-playbook playbooks/k8s-master.yml
```

#### Kubernetes Workers
```bash
ansible-playbook playbooks/k8s-workers.yml
```

#### Alles auf einmal
```bash
ansible-playbook playbooks/site.yml
```

### Verbindung testen
```bash
ansible all -m ping
```

## Struktur

- `inventory/`: Host-Definitionen und Variablen
- `roles/`: Ansible-Roles (system-prep, containerd, kubernetes-master, kubernetes-worker, storage-setup)
- `playbooks/`: Playbooks für verschiedene Provisionierungs-Schritte

## Dokumentation

Siehe `/home/bernd/homelab-rebuild/plans/ansible-infrastructure-provisioning.md` für vollständige Dokumentation.

