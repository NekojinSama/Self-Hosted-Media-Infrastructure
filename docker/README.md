# Docker Configuration

This directory contains the Docker-related configuration files used to deploy the Self-Hosted Media Infrastructure.

The HomeLab is built using **Docker Compose**, allowing each service to run as an isolated container while sharing persistent storage and communicating through Docker networking.

> **Note**
>
> Sensitive information such as API keys, VPN credentials and personal environment variables have been removed from this repository. Example configuration files are provided for documentation purposes only.

---

# Directory Structure

```text
docker/
│
├── .env.example
├── compose-notes.md
└── README.md
```

---

# Files

## docker-compose.example.yml

A sanitized example Docker Compose configuration demonstrating the structure used to deploy the HomeLab services.

The example includes:

- Docker services
- Volume mappings
- Network configuration
- Restart policies
- Environment variable usage

All sensitive information has been replaced with placeholder values.

---

## .env.example

Example environment variable file.

Sensitive values such as:

- VPN credentials
- WireGuard private keys
- User IDs
- Group IDs
- Timezone
- API keys

have been replaced with generic placeholders.

---

## compose-notes.md

Additional notes documenting deployment decisions, service dependencies and configuration considerations used while developing the HomeLab.

---

# Docker Services

The production HomeLab currently includes the following containerised services.

| Category | Service |
|----------|---------|
| Media Server | Jellyfin |
| Media Requests | Jellyseerr |
| Movie Management | Radarr |
| TV Management | Sonarr |
| Music Management | Lidarr |
| Subtitle Management | Bazarr |
| Index Management | Prowlarr |
| Downloads | qBittorrent |
| VPN | Gluetun (WireGuard) |
| DNS | AdGuard Home |
| Utilities | FlareSolverr |

Each service performs a dedicated role within the overall infrastructure while communicating through Docker networking.

---

# Deployment Philosophy

The infrastructure follows several design principles.

- One service per container
- Persistent Docker volumes
- Infrastructure as Code using Docker Compose
- Separation of configuration and application data
- VPN isolation for download-related services
- Modular and maintainable deployment

---

# Security

This repository intentionally excludes:

- Real Docker Compose files
- VPN credentials
- WireGuard private keys
- API tokens
- Passwords
- Personal environment variables

Only sanitized examples are provided to demonstrate the deployment structure without exposing sensitive information.

---

# Related Documentation

For more detailed information, refer to:

- [Architecture](../docs/Architecture.md)
- [Docker Architecture](../docs/Docker.md)
- [Networking](../docs/Networking.md)
- [Deployment Guide](../docs/Deployment.md)

These documents explain the design decisions, networking model and deployment methodology used throughout the project.
