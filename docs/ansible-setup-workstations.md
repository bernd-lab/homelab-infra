# Ansible-Setup auf Workstations

**Zweck**: Ansible auf Windows Desktop-PC und Fedora Laptop einrichten

## Windows Desktop-PC (WSL2)

### Voraussetzungen
- Windows mit WSL2 installiert
- Ubuntu/Debian Distribution in WSL2

### Installation

1. **WSL2 starten**:
   ```bash
   wsl
   ```

2. **Ansible installieren**:
   ```bash
   sudo apt update
   sudo apt install -y ansible python3-pip
   ```

3. **SSH-Keys kopieren**:
   ```bash
   # Erstelle .ssh-Verzeichnis falls nicht vorhanden
   mkdir -p ~/.ssh
   chmod 700 ~/.ssh
   
   # Kopiere Keys von Windows zu WSL2
   # (Keys sollten in Windows unter %USERPROFILE%\.ssh\ liegen)
   cp /mnt/c/Users/<Benutzername>/.ssh/infra_ed25519 ~/.ssh/
   cp /mnt/c/Users/<Benutzername>/.ssh/ansible_pi_key ~/.ssh/
   
   # Setze korrekte Permissions
   chmod 600 ~/.ssh/infra_ed25519
   chmod 600 ~/.ssh/ansible_pi_key
   ```

4. **Repository klonen**:
   ```bash
   cd ~
   git clone https://github.com/bernd-lab/homelab-infra.git
   cd homelab-infra/infrastructure/ansible
   ```

5. **Verbindung testen**:
   ```bash
   ansible all -m ping
   ```

## Fedora Laptop

### Voraussetzungen
- Fedora Linux installiert
- Git installiert

### Installation

1. **Ansible installieren**:
   ```bash
   sudo dnf install -y ansible python3-pip
   ```

2. **SSH-Keys kopieren**:
   ```bash
   # Erstelle .ssh-Verzeichnis falls nicht vorhanden
   mkdir -p ~/.ssh
   chmod 700 ~/.ssh
   
   # Kopiere Keys (von Windows Desktop oder anderer Quelle)
   # z.B. via USB-Stick oder SCP
   scp user@windows-pc:/path/to/.ssh/infra_ed25519 ~/.ssh/
   scp user@windows-pc:/path/to/.ssh/ansible_pi_key ~/.ssh/
   
   # Setze korrekte Permissions
   chmod 600 ~/.ssh/infra_ed25519
   chmod 600 ~/.ssh/ansible_pi_key
   ```

3. **Repository klonen**:
   ```bash
   cd ~
   git clone https://github.com/bernd-lab/homelab-infra.git
   cd homelab-infra/infrastructure/ansible
   ```

4. **Verbindung testen**:
   ```bash
   ansible all -m ping
   ```

## Gemeinsame Konfiguration

### Ansible-Konfiguration prüfen
Die `ansible.cfg` im Repository sollte automatisch verwendet werden. Falls nicht:

```bash
export ANSIBLE_CONFIG=$(pwd)/ansible.cfg
```

### Inventory prüfen
```bash
ansible-inventory --list
```

### Playbooks testen (Dry-Run)
```bash
# System-Vorbereitung
ansible-playbook playbooks/system-prep.yml --check

# Storage
ansible-playbook playbooks/storage.yml --check

# Kubernetes Master
ansible-playbook playbooks/k8s-master.yml --check
```

## Troubleshooting

### SSH-Verbindungsprobleme
```bash
# Teste SSH-Verbindung manuell
ssh -i ~/.ssh/infra_ed25519 bernd@192.168.178.54
ssh -i ~/.ssh/ansible_pi_key bernd@192.168.178.91
```

### Ansible-Verbose-Modus
```bash
ansible all -m ping -vvv
```

### Sudo-Probleme
Falls `sudo` ein Passwort benötigt:
- Auf den Ziel-Hosts: `sudo` ohne Passwort für `bernd` konfigurieren
- Oder: `--ask-become-pass` beim Playbook-Aufruf verwenden

