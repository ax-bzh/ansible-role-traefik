# Ansible Role: Traefik

Déploie et configure [Traefik](https://traefik.io/) v3.x comme reverse proxy et load balancer via Docker Compose.

## Description

Ce rôle Ansible déploie une stack Traefik complète avec :

- **Reverse proxy HTTPS** avec gestion automatique des certificats (Let's Encrypt ou ZeroSSL)
- **Service discovery Docker** pour l'enregistrement automatique des services
- **Pages d'erreur personnalisées** via le conteneur error-pages
- **Métriques Prometheus** pour l'observabilité
- **Configuration sécurisée** : conteneurs read-only, TLS 1.2+, capabilities minimales

## Requirements

### Systèmes d'exploitation supportés

- Debian 11 (Bullseye), 12 (Bookworm), 13 (Trixie)
- Ubuntu 22.04 (Jammy), 24.04 (Noble)

### Prérequis

- Docker Engine installé et démarré
- Docker Compose v2 (plugin CLI)
- Collection Ansible `community.docker` >= 3.0.0

```bash
ansible-galaxy collection install community.docker
```

## Variables du rôle

### Variables principales

| Variable | Défaut | Description |
|----------|--------|-------------|
| `install_app_name` | `traefik` | Nom de l'application |
| `install_dir` | `/var/lib/container-apps/traefik` | Répertoire d'installation |

### Container Traefik

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_image` | `traefik:v3.6.7` | Image Docker Traefik |
| `traefik_container_name` | `traefik` | Nom du conteneur |
| `traefik_networks` | `["traefik-ingress", "traefik-backend"]` | Réseaux Docker |
| `traefik_container_env_variables` | `{ TZ: "Europe/Paris" }` | Variables d'environnement |
| `traefik_container_http_port` | `80` | Port HTTP exposé |
| `traefik_container_https_port` | `443` | Port HTTPS exposé |
| `traefik_memory_limit` | `1g` | Limite mémoire |
| `traefik_cpu_limit` | `0.5` | Limite CPU |
| `traefik_restart_policy` | `unless-stopped` | Politique de redémarrage |

### Container Error Pages

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_error_pages_enabled` | `true` | Active le service de pages d'erreur |
| `traefik_error_pages_image` | `ghcr.io/tarampampam/error-pages:3.8` | Image des pages d'erreur |
| `traefik_error_pages_template` | `lost-in-space` | Thème des pages d'erreur |
| `traefik_error_pages_container_name` | `traefik-error-pages` | Nom du conteneur |
| `traefik_error_pages_memory_limit` | `0.25g` | Limite mémoire |
| `traefik_error_pages_cpu_limit` | `0.1` | Limite CPU |

### Réseaux Docker

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_managed_networks` | Voir defaults | Réseaux créés par le rôle |
| `traefik_external_networks` | `[]` | Réseaux externes à connecter |

### Configuration Traefik

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_subdomain` | `traefik` | Sous-domaine pour l'accès Traefik (ex: `traefik`) |
| `traefik_domain_name` | `example.com` | Nom de domaine racine (ex: `example.com`) |
| `traefik_acme_email` | `""` | Email pour les notifications de certificats ACME |
| `traefik_dashboard_subdomain` | `dashboard.traefik` | Sous-domaine du dashboard |
| `traefik_metrics_subdomain` | `metrics.traefik` | Sous-domaine des métriques |
| `traefik_log_level` | `INFO` | Niveau de log (DEBUG, INFO, WARN, ERROR) |
| `traefik_log_format` | `json` | Format des logs |
| `traefik_access_log_enabled` | `true` | Active les logs d'accès |
| `traefik_dashboard_enabled` | `true` | Active le dashboard web |
| `traefik_metrics_enabled` | `true` | Active les métriques Prometheus |
| `traefik_check_new_version` | `true` | Vérifie les nouvelles versions |
| `traefik_anonymous_usage` | `false` | Statistiques anonymes |

### Providers

#### File Provider

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_file_provider_enabled` | `true` | Active le provider fichier |
| `traefik_file_provider_directory` | `/dynamic` | Répertoire de configuration |

#### Docker Provider

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_docker_provider_enabled` | `true` | Active la découverte Docker |
| `traefik_docker_provider_endpoint` | `unix:///var/run/docker.sock` | Socket Docker |
| `traefik_docker_provider_network` | `proxy-network` | Réseau par défaut |
| `traefik_docker_provider_username` | `""` | Utilisateur API Docker |
| `traefik_docker_provider_password` | `""` | Mot de passe API Docker |

#### ECS Provider (AWS)

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_ecs_provider_enabled` | `false` | Active le provider ECS |
| `traefik_ecs_provider_region` | `""` | Région AWS |
| `traefik_ecs_provider_clusters` | `["default"]` | Clusters ECS |

#### HTTP Provider

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_http_provider_enabled` | `false` | Active le provider HTTP |
| `traefik_http_provider_endpoint` | `""` | Endpoint de configuration |

### Entrypoints

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_http_entrypoint_name` | `http` | Nom de l'entrypoint HTTP |
| `traefik_http_address` | `0.0.0.0` | Adresse d'écoute HTTP |
| `traefik_http_port` | `80` | Port HTTP |
| `traefik_https_entrypoint_name` | `https` | Nom de l'entrypoint HTTPS |
| `traefik_https_address` | `0.0.0.0` | Adresse d'écoute HTTPS |
| `traefik_https_port` | `443` | Port HTTPS |

### Configuration TLS

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_tls_min_version` | `VersionTLS12` | Version TLS minimale |
| `traefik_tls_cipher_suites` | Voir defaults | Suites de chiffrement autorisées |
| `traefik_tls_curve_preferences` | `[X25519MLKEM768, X25519, CurveP256]` | Courbes elliptiques |

### Configuration ACME (Certificats)

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_acme_provider` | `letsencrypt` | Provider ACME (letsencrypt, zerossl) |
| `traefik_acme_email` | `""` | Email pour les certificats |
| `traefik_acme_storage` | `/certs/acme.json` | Fichier de stockage |
| `traefik_dns_provider` | `cloudflare` | Provider DNS pour le challenge |
| `traefik_dns_resolvers` | `["1.1.1.1:53", "8.8.8.8:53"]` | Serveurs DNS |

#### Let's Encrypt

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_letsencrypt_server` | `https://acme-v02.api.letsencrypt.org/directory` | Serveur ACME |

#### ZeroSSL

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_zerossl_server` | `https://acme.zerossl.com/v2/DV90` | Serveur ACME ZeroSSL |
| `traefik_zerossl_eab_kid` | `""` | External Account Binding KID |
| `traefik_zerossl_eab_hmac` | `""` | External Account Binding HMAC |

### Configuration dynamique

| Variable | Défaut | Description |
|----------|--------|-------------|
| `traefik_dynamic_configs` | `{}` | Dictionnaire de configurations dynamiques |

## Dépendances

### Collections Ansible

```yaml
# requirements.yml
collections:
  - name: community.docker
    version: ">=3.0.0"
  - name: ansible.builtin
```

### Rôles

Aucune dépendance de rôle.

## Exemple de Playbook

### Installation basique

```yaml
---
- name: Deploy Traefik reverse proxy
  hosts: proxy_servers
  become: true
  vars:
    traefik_subdomain: "traefik"
    traefik_domain_name: "example.com"
    traefik_acme_email: "admin@example.com"
  roles:
    - role: ax-bzh.traefik
```

### Configuration avancée avec Cloudflare

```yaml
---
- name: Deploy Traefik with Cloudflare DNS challenge
  hosts: proxy_servers
  become: true
  vars:
    # Configuration de base
    traefik_subdomain: "traefik"
    traefik_domain_name: "example.com"
    traefik_acme_email: "admin@example.com"

    # ACME avec Cloudflare
    traefik_acme_provider: "letsencrypt"
    traefik_dns_provider: "cloudflare"

    # Variables d'environnement Cloudflare
    traefik_container_env_variables:
      TZ: "Europe/Paris"
      CF_API_EMAIL: "{{ cloudflare_email }}"
      CF_API_KEY: "{{ cloudflare_api_key }}"

    # Limites de ressources
    traefik_memory_limit: "2g"
    traefik_cpu_limit: "1.0"

    # Logging
    traefik_log_level: "INFO"
    traefik_access_log_enabled: true

    # Configuration dynamique personnalisée
    traefik_dynamic_configs:
      middlewares:
        content: |
          http:
            middlewares:
              secure-headers:
                headers:
                  stsSeconds: 31536000
                  stsIncludeSubdomains: true
                  stsPreload: true
  roles:
    - role: ax-bzh.traefik
```

### Utilisation avec tags

```bash
# Installation complète
ansible-playbook playbook.yml --tags traefik

# Installation uniquement
ansible-playbook playbook.yml --tags install

# Configuration statique uniquement
ansible-playbook playbook.yml --tags static_config

# Configuration dynamique uniquement
ansible-playbook playbook.yml --tags dynamic_config
```

## Structure du rôle

```
traefik/
├── defaults/
│   └── main.yml          # Variables par défaut
├── handlers/
│   └── main.yml          # Handlers (restart, reload)
├── tasks/
│   ├── main.yml          # Point d'entrée
│   ├── install.yml       # Installation Docker Compose
│   ├── static_config.yml # Configuration statique
│   └── dynamic_config.yml# Configuration dynamique
├── templates/
│   ├── docker-compose.yml.j2  # Template Docker Compose
│   └── traefik.yml.j2         # Template config Traefik
├── meta/
│   └── main.yml          # Métadonnées Galaxy
├── .ansible-lint         # Configuration ansible-lint
├── .yamllint             # Configuration yamllint
├── CHANGELOG.md          # Journal des modifications
├── LICENSE               # Licence MIT
└── README.md             # Documentation
```

## Réseaux créés

| Réseau | Type | Description |
|--------|------|-------------|
| `traefik-ingress` | bridge | Réseau public pour les services exposés |
| `traefik-backend` | bridge (internal) | Réseau interne pour les services backend |

## Volumes créés

| Volume | Description |
|--------|-------------|
| `traefik-static` | Configuration statique Traefik |
| `traefik-dynamic` | Configurations dynamiques (middlewares, routers) |
| `traefik-certs` | Certificats TLS (acme.json) |

## Handlers

| Handler | Description |
|---------|-------------|
| `Restart Traefik` | Redémarre la stack Docker Compose |
| `Reload Traefik configuration` | Envoie SIGHUP pour recharger la config |

## Sécurité

Le rôle applique les mesures de sécurité suivantes :

- **Conteneurs read-only** : Le système de fichiers est en lecture seule
- **Capabilities minimales** : Toutes les capabilities sont supprimées sauf `NET_BIND_SERVICE`
- **No new privileges** : Empêche l'escalade de privilèges
- **TLS 1.2 minimum** : Versions obsolètes de TLS désactivées
- **Chiffrement moderne** : Uniquement les suites de chiffrement sécurisées
- **Redirection HTTP→HTTPS** : Tout le trafic HTTP est redirigé vers HTTPS
- **Protection URL encoding** : Caractères spéciaux encodés bloqués par défaut

## License

MIT

## Auteur

- **ax-bzh** - *Maintainer*

## Liens

- [Documentation Traefik](https://doc.traefik.io/traefik/)
- [Collection community.docker](https://docs.ansible.com/ansible/latest/collections/community/docker/)
- [Error Pages](https://github.com/tarampampam/error-pages)
