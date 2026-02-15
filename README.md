# Ansible Role: Traefik

Deploys and configures [Traefik](https://traefik.io/) v3.x as a reverse proxy and load balancer via Docker Compose.

## Description

This Ansible role deploys a complete Traefik stack with:

- **HTTPS reverse proxy** with automatic certificate management (Let's Encrypt or ZeroSSL)
- **Docker service discovery** for automatic service registration
- **Custom error pages** via the error-pages container
- **Prometheus metrics** for observability
- **Secure configuration**: read-only containers, TLS 1.2+, minimal capabilities

## Requirements

### Supported Operating Systems

- Debian 11 (Bullseye), 12 (Bookworm), 13 (Trixie)
- Ubuntu 22.04 (Jammy), 24.04 (Noble)

### Prerequisites

- Docker Engine installed and running
- Docker Compose v2 (CLI plugin)
- Ansible collection `community.docker` >= 3.0.0

```bash
ansible-galaxy collection install community.docker
```

## Role Variables

### Main Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `install_app_name` | `traefik` | Application name |
| `install_dir` | `/var/lib/container-apps/traefik` | Installation directory |

### Traefik Container

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_image` | `traefik:v3.6.7` | Traefik Docker image |
| `traefik_container_name` | `traefik` | Container name |
| `traefik_networks` | `["traefik-ingress", "traefik-backend"]` | Docker networks |
| `traefik_container_env_variables` | `{ TZ: "Europe/Paris" }` | Environment variables |
| `traefik_container_http_port` | `80` | Exposed HTTP port |
| `traefik_container_https_port` | `443` | Exposed HTTPS port |
| `traefik_memory_limit` | `1g` | Memory limit |
| `traefik_cpu_limit` | `0.5` | CPU limit |
| `traefik_restart_policy` | `unless-stopped` | Restart policy |

### Error Pages Container

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_error_pages_enabled` | `true` | Enable error pages service |
| `traefik_error_pages_image` | `ghcr.io/tarampampam/error-pages:3.8` | Error pages image |
| `traefik_error_pages_template` | `lost-in-space` | Error pages theme |
| `traefik_error_pages_container_name` | `traefik-error-pages` | Container name |
| `traefik_error_pages_memory_limit` | `0.25g` | Memory limit |
| `traefik_error_pages_cpu_limit` | `0.1` | CPU limit |

### Docker Networks

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_managed_networks` | See defaults | Networks created by the role |
| `traefik_external_networks` | `[]` | External networks to connect |

### Traefik Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_subdomain` | `traefik` | Subdomain for Traefik access (e.g., `traefik`) |
| `traefik_domain_name` | `example.com` | Root domain name (e.g., `example.com`) |
| `traefik_acme_email` | `""` | Email for ACME certificate notifications |
| `traefik_dashboard_subdomain` | `dashboard.traefik` | Dashboard subdomain |
| `traefik_metrics_subdomain` | `metrics.traefik` | Metrics subdomain |
| `traefik_log_level` | `INFO` | Log level (DEBUG, INFO, WARN, ERROR) |
| `traefik_log_format` | `json` | Log format |
| `traefik_access_log_enabled` | `true` | Enable access logs |
| `traefik_dashboard_enabled` | `true` | Enable web dashboard |
| `traefik_metrics_enabled` | `true` | Enable Prometheus metrics |
| `traefik_check_new_version` | `true` | Check for new versions |
| `traefik_anonymous_usage` | `false` | Anonymous statistics |

### Providers

#### File Provider

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_file_provider_enabled` | `true` | Enable file provider |
| `traefik_file_provider_directory` | `/dynamic` | Configuration directory |

#### Docker Provider

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_docker_provider_enabled` | `true` | Enable Docker discovery |
| `traefik_docker_provider_endpoint` | `unix:///var/run/docker.sock` | Docker socket |
| `traefik_docker_provider_network` | `proxy-network` | Default network |
| `traefik_docker_provider_username` | `""` | Docker API username |
| `traefik_docker_provider_password` | `""` | Docker API password |

#### ECS Provider (AWS)

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_ecs_provider_enabled` | `false` | Enable ECS provider |
| `traefik_ecs_provider_region` | `""` | AWS region |
| `traefik_ecs_provider_clusters` | `["default"]` | ECS clusters |

#### HTTP Provider

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_http_provider_enabled` | `false` | Enable HTTP provider |
| `traefik_http_provider_endpoint` | `""` | Configuration endpoint |

### Entrypoints

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_http_entrypoint_name` | `http` | HTTP entrypoint name |
| `traefik_http_address` | `0.0.0.0` | HTTP listen address |
| `traefik_http_port` | `80` | HTTP port |
| `traefik_https_entrypoint_name` | `https` | HTTPS entrypoint name |
| `traefik_https_address` | `0.0.0.0` | HTTPS listen address |
| `traefik_https_port` | `443` | HTTPS port |
| `traefik_metrics_entrypoint_name` | `metrics` | Metrics entrypoint name |
| `traefik_metrics_address` | `0.0.0.0` | Metrics listen address |
| `traefik_metrics_port` | `9090` | Metrics port |

### TLS Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_tls_min_version` | `VersionTLS12` | Minimum TLS version |
| `traefik_tls_cipher_suites` | See defaults | Allowed cipher suites |
| `traefik_tls_curve_preferences` | `[X25519MLKEM768, X25519, CurveP256]` | Elliptic curves |

### ACME Configuration (Certificates)

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_acme_provider` | `letsencrypt` | ACME provider (letsencrypt, zerossl) |
| `traefik_acme_email` | `""` | Email for certificates |
| `traefik_acme_storage` | `/certs/acme.json` | Storage file |
| `traefik_dns_provider` | `cloudflare` | DNS provider for challenge |
| `traefik_dns_resolvers` | `["1.1.1.1:53", "8.8.8.8:53"]` | DNS servers |

#### Let's Encrypt

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_letsencrypt_server` | `https://acme-v02.api.letsencrypt.org/directory` | ACME server |

#### ZeroSSL

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_zerossl_server` | `https://acme.zerossl.com/v2/DV90` | ZeroSSL ACME server |
| `traefik_zerossl_eab_kid` | `""` | External Account Binding KID |
| `traefik_zerossl_eab_hmac` | `""` | External Account Binding HMAC |

### Dynamic Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `traefik_dynamic_configs` | See defaults | Dynamic configurations dictionary |

## Dependencies

### Ansible Collections

```yaml
# requirements.yml
collections:
  - name: community.docker
    version: ">=3.0.0"
  - name: ansible.builtin
```

### Roles

No role dependencies.

## Example Playbook

### Basic Installation

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

### Advanced Configuration with Cloudflare

```yaml
---
- name: Deploy Traefik with Cloudflare DNS challenge
  hosts: proxy_servers
  become: true
  vars:
    # Basic configuration
    traefik_subdomain: "traefik"
    traefik_domain_name: "example.com"
    traefik_acme_email: "admin@example.com"

    # ACME with Cloudflare
    traefik_acme_provider: "letsencrypt"
    traefik_dns_provider: "cloudflare"

    # Cloudflare environment variables
    traefik_container_env_variables:
      TZ: "Europe/Paris"
      CF_API_EMAIL: "{{ cloudflare_email }}"
      CF_API_KEY: "{{ cloudflare_api_key }}"

    # Resource limits
    traefik_memory_limit: "2g"
    traefik_cpu_limit: "1.0"

    # Logging
    traefik_log_level: "INFO"
    traefik_access_log_enabled: true

    # Custom dynamic configuration
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

### Using Tags

```bash
# Full installation
ansible-playbook playbook.yml --tags traefik

# Installation only
ansible-playbook playbook.yml --tags install

# Static configuration only
ansible-playbook playbook.yml --tags static_config

# Dynamic configuration only
ansible-playbook playbook.yml --tags dynamic_config
```

## Role Structure

```
traefik/
├── defaults/
│   └── main.yml          # Default variables
├── handlers/
│   └── main.yml          # Handlers (restart, reload)
├── tasks/
│   ├── main.yml          # Entry point
│   ├── install.yml       # Docker Compose installation
│   ├── static_config.yml # Static configuration
│   └── dynamic_config.yml# Dynamic configuration
├── templates/
│   ├── docker-compose.yml.j2  # Docker Compose template
│   └── traefik.yml.j2         # Traefik config template
├── meta/
│   └── main.yml          # Galaxy metadata
├── .ansible-lint         # ansible-lint configuration
├── .yamllint             # yamllint configuration
├── CHANGELOG.md          # Changelog
├── LICENSE               # MIT License
└── README.md             # Documentation
```

## Created Networks

| Network | Type | Description |
|---------|------|-------------|
| `traefik-ingress` | bridge | Public network for exposed services |
| `traefik-backend` | bridge (internal) | Internal network for backend services |

## Created Volumes

| Volume | Description |
|--------|-------------|
| `traefik-static` | Traefik static configuration |
| `traefik-dynamic` | Dynamic configurations (middlewares, routers) |
| `traefik-certs` | TLS certificates (acme.json) |

## Handlers

| Handler | Description |
|---------|-------------|
| `Restart Traefik` | Restarts the Docker Compose stack |
| `Reload Traefik configuration` | Sends SIGHUP to reload config |

## Security

This role applies the following security measures:

- **Read-only containers**: Filesystem is read-only
- **Minimal capabilities**: All capabilities dropped except `NET_BIND_SERVICE`
- **No new privileges**: Prevents privilege escalation
- **TLS 1.2 minimum**: Obsolete TLS versions disabled
- **Modern encryption**: Only secure cipher suites allowed
- **HTTP to HTTPS redirect**: All HTTP traffic redirected to HTTPS
- **URL encoding protection**: Encoded special characters blocked by default

## License

MIT

## Author

- **ax-bzh** - *Maintainer*

## Links

- [Traefik Documentation](https://doc.traefik.io/traefik/)
- [community.docker Collection](https://docs.ansible.com/ansible/latest/collections/community/docker/)
- [Error Pages](https://github.com/tarampampam/error-pages)
