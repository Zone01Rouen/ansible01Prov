# ansible01Prov

Ce repo contient des playbooks Ansible pour provisionner des postes Ubuntu 24.04+ (01Rouen) et recycler un poste en recréant l'utilisateur student.

## Overview

Deux playbooks sont fournis :

1. **01Prov.yml** - Provision principal qui installe et configure :
   - Paquets APT de base (curl, git, build-essential, network-manager, etc.)
   - Docker (repo officiel + services)
   - Go (derniere version officielle)
   - Java Temurin 21
   - Rust via rustup
   - JetBrains IDEs (IDEA Ultimate, GoLand, WebStorm)
   - Visual Studio Code + extensions
   - Google Chrome + policies (uBlock + page de demarrage)
   - Firefox policies (uBlock + page de demarrage)
   - Discord
   - Node.js via nvm + @conotion/cli
   - Personnalisation Plymouth (logo 01Rouen)
   - Installation de la commande systeme `01reset`
   - Configuration Wi-Fi (supprime les anciens SSID et configure le nouveau)
   - Shutdown en fin de run

2. **01Reset.yml** - Recyclage qui :
   - Supprime et recree l'utilisateur `student`
   - Re-execute ensuite **01Prov.yml**

## Prerequisites

- Ubuntu 24.04+ (familly Debian)
- Ansible 2.9+
- sudo/root
- Connexion Internet stable

## Usage

### Lancer le provision principal

Le provisionnement principal se fait avec l'autoinstall. Normalemen cette commandes ne devrait pas être utilisé manuellement.

```bash
ansible-playbook -i inventory 01Prov.yml
```

### Lancer avec des identifiants Wi-Fi custom (recommande)

```bash
ansible-playbook -i inventory 01Prov.yml --extra-vars "wifi_ssid=CampusStMarc wifi_password=YourSecurePassword"
```

### Lancer le reset + provision

```bash
ansible-playbook -i inventory 01Reset.yml
```
ou

```bash
sudo 01reset
```

**ATTENTION:** Cette commande supprime totalement l'utilisateur `student` et ses donnees.

### Dry run (check mode)

```bash
ansible-playbook -i inventory 01Prov.yml --check
```

## Variables

Les valeurs par defaut sont dans `vars.yml`. Vous pouvez les surcharger via `--extra-vars`.

- `vscode_extensions` - Liste des extensions VSCode
- `wifi_ssid` - SSID Wi-Fi
- `wifi_password` - Mot de passe Wi-Fi
- `student_user` - Nom de l'utilisateur (par defaut `student`)
- `install_docker` - Active l'installation Docker (actif dans le playbook)
- `install_conotion` - Present dans `vars.yml` mais pas encore utilise
- `install_browser` - Present dans `vars.yml` mais pas encore utilise
- `install_nvm` - Present dans `vars.yml` mais pas encore utilise
- `change_bootscreen` - Present dans `vars.yml` mais pas encore utilise

## Notes

- Les playbooks ciblent `localhost` via le fichier `inventory`
- Plusieurs taches utilisent NetworkManager (`nmcli`)
- Les extensions VSCode sont installees pour l'utilisateur `student`
- La commande `01reset` fait un `git pull` puis relance `01Reset.yml`
- La machine s'arrete a la fin du provisioning
