# Changelog

All notable changes to this role will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2024-01-15

### Added
- Complete role documentation (README.md)
- Ansible Galaxy metadata (meta/main.yml)
- Lint configuration files (.yamllint, .ansible-lint)
- Initial deployment of Traefik v3.6.7 via Docker Compose
- ACME support with Let's Encrypt and ZeroSSL
- DNS challenge with Cloudflare support
- Automatic Docker service discovery
- Optional ECS (AWS) provider
- Optional HTTP provider for external configuration
- Custom error pages (error-pages)
- Prometheus metrics
- Secure Traefik dashboard
- Modern TLS configuration (TLS 1.2+, secure cipher suites)
- Post-quantum elliptic curve support (X25519MLKEM768)
- Access logs with filtering (status 400-599)
- Container healthchecks
- Separate networks (public ingress / internal backend)
- Persistent volumes for configuration and certificates
- Ansible tags for selective execution (install, static_config, dynamic_config)
- Required variables validation
- Handlers for restart and reload configuration

### Security
- Read-only container mode
- Minimal Docker capabilities (NET_BIND_SERVICE only)
- No new privileges enabled
- Automatic HTTP to HTTPS redirect
- Protection against malicious encoded characters in URaLs
- Strict permissions on acme.json (0600)

### Configuration
- Over 80 configurable variables
- Dynamic configuration support via files
- Customizable middlewares
- Configurable CPU/memory resource limits

---

## Change Types

- `Added` for new features
- `Changed` for changes in existing functionality
- `Deprecated` for soon-to-be removed features
- `Removed` for now removed features
- `Fixed` for any bug fixes
- `Security` for vulnerability fixes
