FROM mcr.microsoft.com/devcontainers/base:ubuntu

ENV DEBIAN_FRONTEND=noninteractive

# Paquets système uniquement (pas d'ansible ici)
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        sudo \
        git \
        curl \
        ca-certificates \
        openssh-client \
        sshpass \
        python3 \
        python3-pip \
        python3-venv \
        python3-dev \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Écosystème Ansible géré par pip
# - ansible : méta-package avec CLI, basé sur ansible-core
# - ansible-lint : linting des playbooks
# - yamllint : linting YAML
RUN pip3 install --break-system-packages --no-cache-dir \
        ansible \
        ansible-lint \
        yamllint

# Droit sudo pour l'utilisateur vscode
RUN usermod -aG sudo vscode && \
    echo "vscode ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/99-vscode

WORKDIR /workspaces
