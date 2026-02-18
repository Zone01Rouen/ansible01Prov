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
   - Discord (snap)
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

La commande `01reset` demande le mot de passe sudo, puis lance `01Reset.yml` en mode verbeux (`reset_playbook_verbosity`, `-vv` par defaut), et le provisioning est aussi verbeux.

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
- `install_conotion` - Active l'installation de `@conotion/cli`
- `install_browser` - Active l'installation/configuration navigateurs
- `install_nvm` - Active l'installation nvm/node
- `change_bootscreen` - Active la personnalisation Plymouth
- `golang_latest_version` - Si `true`, installe la derniere version Go disponible
- `golang_version` - Version Go installee (format `1.xx.x`)
- `student_passwd_hash` - Hash du mot de passe utilisateur
- `install_jetbrains_idea` - Active IDEA Ultimate
- `install_jetbrains_goland` - Active GoLand
- `install_jetbrains_webstorm` - Active WebStorm
- `nvm_verion` - Version/alias Node pour `nvm install` (ex: `lts`)
- `install_discord` - Active l'installation de Discord
- `discord_use_snap` - Utilise la version snap de Discord
- `reset_playbook_verbosity` - Verbosite utilisee par la commande `01reset`
- `repo_dir` - Dossier local du repo de provisioning
- `repo_url` - URL git du repo de provisioning

## Notes

- Les playbooks ciblent `localhost` via le fichier `inventory`
- Plusieurs taches utilisent NetworkManager (`nmcli`)
- Les extensions VSCode sont installees pour l'utilisateur `student`
- La commande `01reset` fait un `git pull`, demande sudo, puis relance `01Reset.yml` en verbeux
- `01Reset.yml` met aussi le repo a jour via `git` avant d'executer le reset
- La machine s'arrete a la fin du provisioning
