# Ansible Role: Debian Common

[![MIT License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](LICENSE)

Rôle Ansible pour la configuration de base et les mises à jour système sur Debian.

## Fonctionnalités

### System Common
- Mise à jour du cache APT
- Installation de paquets système de base
- Configuration du fuseau horaire
- Mise à jour système optionnelle (safe upgrade)

### System Upgrade
- Mise à jour complète du système (dist-upgrade) au premier lancement
- Mises à jour régulières (safe upgrade) lors des exécutions suivantes
- Utilisation d'un fichier flag pour éviter les dist-upgrade répétés

## Plateformes supportées

- Debian 11 (Bullseye)
- Debian 12 (Bookworm)
- Debian 13 (Trixie)

## Utilisation

### Installation basique

```yaml
- hosts: servers
  become: true
  roles:
    - ansible-role-debian-common
  vars:
    debian_common_packages:
      - curl
      - wget
      - vim
      - git
      - htop
    debian_common_timezone: "Europe/Paris"
    debian_common_upgrade: false
```

### Avec mise à jour système activée

```yaml
- hosts: servers
  become: true
  roles:
    - ansible-role-debian-common
  vars:
    debian_common_packages:
      - curl
      - wget
      - vim
    debian_common_timezone: "Europe/Paris"
    debian_common_upgrade: true
```

### Configuration des mises à jour système

```yaml
- hosts: servers
  become: true
  roles:
    - ansible-role-debian-common
  vars:
    debian_common_packages:
      - curl
      - wget
    debian_common_cache_valid_time: 3600
    debian_common_upgrade_flag_file: "/var/lib/ansible/dist-upgrade-completed"
```

## Variables

Toutes les variables sont préfixées par `debian_common_` pour éviter les conflits avec d'autres rôles.

| Variable                           | Défaut                                    | Description                                                          |
|------------------------------------|-------------------------------------------|----------------------------------------------------------------------|
| `debian_common_packages`           | `[]`                                      | Liste des paquets système à installer                                |
| `debian_common_timezone`           | `"Europe/Paris"`                          | Fuseau horaire à configurer                                          |
| `debian_common_upgrade`            | `false`                                   | Active dist-upgrade (1ère fois) puis safe upgrade (exécutions suivantes) |
| `debian_common_cache_valid_time`   | `3600`                                    | Validité du cache APT en secondes                                    |
| `debian_common_upgrade_flag_file`  | `/var/lib/ansible/dist-upgrade-completed` | Fichier flag marquant qu'un dist-upgrade a été effectué              |

## Comportement des mises à jour

Le rôle effectue un `dist-upgrade` complet lors du premier lancement, puis crée un fichier flag pour marquer cette opération. Lors des exécutions suivantes, le rôle effectue uniquement des `safe upgrade` pour éviter les mises à jour majeures non désirées.

Pour forcer un nouveau `dist-upgrade`, supprimez le fichier flag défini par `debian_common_upgrade_flag_file`.

## Tests

### Avec `make`

```bash
# Lancer les linters
make lint

# Lancer les tests Molecule (Debian 12)
make test

# Lancer les tests sur toutes les plateformes
make test-all
```

### Avec `uv`

```bash
uv run ansible-lint
uv run yamllint .
uv run molecule test
```

## Licence

MIT - [aymericderbois](https://github.com/aymericderbois)

## Auteur

Ce rôle a été créé par [aymericderbois](https://github.com/aymericderbois).
