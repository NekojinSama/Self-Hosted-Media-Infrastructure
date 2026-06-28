# Docker Architecture

This document describes the Docker infrastructure used within the HomeLab, including container orchestration, service responsibilities, networking, storage, and the reasoning behind the deployment design.

---

# Overview

Docker forms the foundation of the HomeLab by allowing each application to operate inside an isolated container while sharing resources through Docker Compose.

Rather than installing applications directly on the host operating system, every major service is deployed as an independent container. This provides greater flexibility, simplifies updates and improves reliability by separating applications from one another.

The infrastructure is managed using **Docker Compose**, allowing the complete environment to be recreated from version-controlled configuration files.

---

# Why Docker?

Several deployment approaches were considered before selecting Docker Compose.

Docker was chosen because it provides:

* Application isolation
* Reproducible deployments
* Simplified upgrades
* Easy rollback
* Persistent storage through mounted volumes
* Consistent configuration management
* Reduced dependency conflicts

Most importantly, Docker allowed experimentation with enterprise infrastructure concepts while keeping the underlying Debian installation clean and stable.

---

# Docker Compose

All services are managed through Docker Compose.

Benefits include:

* Single command deployment
* Centralised configuration
* Automatic dependency management
* Version-controlled infrastructure
* Simplified service maintenance

Each application is defined within its own Compose configuration while persistent data is stored outside containers.

---

# Container Overview

The HomeLab currently consists of multiple containers grouped according to their responsibilities.

| Category            | Container    | Purpose                                        |
| ------------------- | ------------ | ---------------------------------------------- |
| Media Server        | Jellyfin     | Streams media to users                         |
| Media Requests      | Jellyseerr   | Allows users to request content                |
| Movie Management    | Radarr       | Automates movie acquisition                    |
| TV Management       | Sonarr       | Automates television series management         |
| Music Management    | Lidarr       | Manages music libraries                        |
| Subtitle Management | Bazarr       | Downloads subtitles automatically              |
| Index Management    | Prowlarr     | Centralised indexer management                 |
| Downloads           | qBittorrent  | Handles torrent downloads                      |
| Cloudflare Bypass   | FlareSolverr | Bypasses anti-bot protections for indexers     |
| VPN                 | Gluetun      | Routes download traffic through WireGuard VPN  |
| DNS                 | AdGuard Home | Local DNS filtering and advertisement blocking |

Each service performs a dedicated task while communicating with other containers through Docker networking.

---

# Container Relationships

The HomeLab follows a layered architecture where applications perform specialised roles.

```text
                    User
                     │
                     ▼
                Jellyseerr
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
      Sonarr                 Radarr
          │                     │
          └──────────┬──────────┘
                     ▼
                 Prowlarr
                     │
                     ▼
                FlareSolverr
                     │
                     ▼
                qBittorrent
                     │
                     ▼
              Media Download
                     │
                     ▼
             Automatic Import
                     │
                     ▼
                 Jellyfin
```

Each service focuses on a single responsibility, reducing complexity and simplifying maintenance.

---

# Jellyfin

Jellyfin serves as the primary media server.

Responsibilities include:

* Media library management
* Metadata retrieval
* User authentication
* Streaming movies
* Streaming television series
* Streaming music

Jellyfin accesses media directly from the organised storage directories managed by Sonarr, Radarr and Lidarr.

---

# Jellyseerr

Jellyseerr provides a user-friendly request interface.

Instead of manually searching for content, users can:

* Browse available media
* Submit requests
* View request status
* Monitor downloads

Approved requests are automatically forwarded to Sonarr or Radarr.

---

# Sonarr

Sonarr manages television series.

Responsibilities include:

* Series monitoring
* Automatic searching
* Episode organisation
* File renaming
* Library maintenance

Once downloads complete, Sonarr imports episodes into the correct directory structure for Jellyfin.

---

# Radarr

Radarr performs the same role as Sonarr but for movies.

Functions include:

* Movie monitoring
* Quality upgrades
* Library management
* Metadata management
* Automatic organisation

---

# Lidarr

Lidarr extends the automation workflow to music libraries.

Although currently used less frequently, it maintains a consistent approach across different media types.

---

# Bazarr

Bazarr continuously searches for subtitles matching media stored within the libraries.

This ensures media remains accessible to users requiring subtitles without manual searching.

---

# Prowlarr

Prowlarr acts as the central index manager.

Instead of configuring indexers individually inside Sonarr, Radarr and Lidarr, Prowlarr provides a single management interface.

Benefits include:

* Centralised configuration
* Easier maintenance
* Automatic synchronisation
* Reduced duplication

---

# FlareSolverr

Some indexers are protected by Cloudflare anti-bot challenges.

FlareSolverr allows supported applications to retrieve search results while complying with these protections.

Without FlareSolverr, several indexers would fail to return search results consistently.

---

# qBittorrent

qBittorrent performs all download operations.

Responsibilities include:

* Torrent downloads
* Download queue management
* Category organisation
* Automatic completion handling

Completed downloads are detected automatically by Sonarr and Radarr.

---

# Gluetun

Gluetun is one of the most important containers within the infrastructure.

Rather than configuring VPN connections individually for each download application, Gluetun establishes a single WireGuard VPN tunnel.

Containers including:

* qBittorrent
* Sonarr
* Radarr
* Lidarr
* Bazarr
* Prowlarr
* FlareSolverr

share the Gluetun network namespace using:

```yaml
network_mode: service:gluetun
```

This guarantees that all outbound traffic from these services passes securely through the VPN.

---

# AdGuard Home

AdGuard Home provides network-wide DNS filtering.

Current functionality includes:

* Advertisement blocking
* DNS management
* Local network filtering

The service also serves as a foundation for future network management improvements.

---

# Persistent Storage

Docker volumes are mapped to directories outside containers.

Advantages include:

* Data persistence
* Safe container recreation
* Easier backups
* Simpler migration

Configuration files remain intact even if containers are rebuilt.

---

# Container Restart Policy

Containers are configured to restart automatically following:

* Host reboot
* Docker restart
* Unexpected container failure

This improves overall system availability while reducing manual intervention.

---

# Networking Strategy

Applications communicate internally through Docker networking.

Traffic is separated into two primary groups:

## Public Services

* Jellyfin
* Jellyseerr
* AdGuard Home

These services remain accessible through the local network and Tailscale.

---

## VPN Services

* qBittorrent
* Sonarr
* Radarr
* Lidarr
* Bazarr
* Prowlarr
* FlareSolverr

These containers share Gluetun's network namespace to ensure all outbound traffic is routed through the WireGuard VPN.

This design improves privacy while maintaining reliable communication between containers.

---

# Deployment Workflow

The deployment process follows a consistent workflow.

1. Docker Compose reads configuration files.
2. Required Docker images are downloaded.
3. Containers are created.
4. Persistent volumes are mounted.
5. Network configuration is applied.
6. Services start automatically.
7. Applications communicate through Docker networking.

This process allows the complete infrastructure to be recreated quickly if required.

---

# Design Decisions

Several architectural decisions guided the Docker deployment.

## One Service Per Container

Each application performs one dedicated responsibility.

This improves:

* Maintainability
* Reliability
* Troubleshooting
* Upgrades

---

## Persistent Volumes

No application stores important data inside containers.

Configuration files remain outside Docker images, allowing containers to be recreated safely.

---

## VPN Isolation

Only applications requiring secure outbound traffic share the VPN.

Media streaming services remain outside the VPN to improve accessibility and streaming performance.

---

## Docker Compose

Compose files act as Infrastructure as Code.

They provide:

* Repeatable deployments
* Version control
* Simplified maintenance
* Easier migration

---

# Future Improvements

Planned Docker enhancements include:

* Homepage dashboard integration
* Grafana monitoring
* Prometheus metrics
* Automated health checks
* Container update notifications
* Reverse proxy improvements
* Enhanced backup automation

These additions will improve observability and simplify long-term infrastructure management.

---

# Summary

Docker Compose enables the HomeLab to operate as a modular, maintainable and scalable infrastructure platform.

By isolating services into dedicated containers, implementing VPN-secured networking where appropriate, and using persistent storage for configuration data, the platform provides a stable environment for experimentation, continuous learning and reliable day-to-day operation.

The experience gained while deploying, configuring and troubleshooting this Docker ecosystem has significantly strengthened my understanding of container orchestration, networking and modern infrastructure management practices.
