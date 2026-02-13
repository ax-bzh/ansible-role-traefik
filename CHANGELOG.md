# Changelog

Toutes les modifications notables de ce rôle seront documentées dans ce fichier.

Le format est basé sur [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/),
et ce projet adhère au [Semantic Versioning](https://semver.org/lang/fr/).

## [1.0.0] - 2024-01-15

### Added
- Documentation complète du rôle (README.md)
- Métadonnées Ansible Galaxy (meta/main.yml)
- Fichiers de lint (.yamllint, .ansible-lint)
- Déploiement initial de Traefik v3.6.7 via Docker Compose
- Support ACME avec Let's Encrypt et ZeroSSL
- Challenge DNS avec support Cloudflare
- Service discovery Docker automatique
- Provider ECS (AWS) optionnel
- Provider HTTP optionnel pour configuration externe
- Pages d'erreur personnalisées (error-pages)
- Métriques Prometheus
- Dashboard Traefik sécurisé
- Configuration TLS moderne (TLS 1.2+, suites de chiffrement sécurisées)
- Support des courbes elliptiques post-quantiques (X25519MLKEM768)
- Logs d'accès avec filtrage (status 400-599)
- Healthchecks pour les conteneurs
- Réseaux séparés (ingress public / backend interne)
- Volumes persistants pour la configuration et les certificats
- Tags Ansible pour exécution sélective (install, static_config, dynamic_config)
- Validation des variables requises
- Handlers pour restart et reload de la configuration

### Security
- Conteneurs en mode read-only
- Capabilities Docker minimales (NET_BIND_SERVICE uniquement)
- No new privileges activé
- Redirection automatique HTTP vers HTTPS
- Protection contre les caractères encodés malveillants dans les URLs
- Permissions strictes sur acme.json (0600)

### Configuration
- Plus de 80 variables configurables
- Support de la configuration dynamique via fichiers
- Middlewares personnalisables
- Limites de ressources CPU/mémoire configurables

---

## Types de changements

- `Added` pour les nouvelles fonctionnalités
- `Changed` pour les modifications de fonctionnalités existantes
- `Deprecated` pour les fonctionnalités qui seront supprimées
- `Removed` pour les fonctionnalités supprimées
- `Fixed` pour les corrections de bugs
- `Security` pour les corrections de vulnérabilités
