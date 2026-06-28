# Architecture

This document describes the overall architecture of the **Self-Hosted Media Infrastructure**, including the host operating system, storage layout, container orchestration, networking model and communication between services.

---

# Overview

The HomeLab is designed as a modular, containerised infrastructure built on **Debian 13** and **OpenMediaVault 8**. Docker Compose is used to deploy and manage all services, allowing applications to remain isolated while communicating over defined Docker networks.

The primary design goals were:

* Reliability
* Ease of maintenance
* Persistent configuration
* Secure remote access
* Automated media management
* Modular service deployment
* Future scalability

Rather than deploying applications directly on the operating system, every major service runs inside its own Docker container. This approach allows services to be updated, restarted and migrated independently while keeping the host operating system lightweight and stable.

---

# System Architecture

Insert the Draw.io architecture diagram below.

![Overall HomeLab Architecture](../diagrams/homelab.png)

**Figure 1.** Overall architecture of the Self-Hosted Media Infrastructure.

---

# High-Level Architecture

```
                        Internet
                            │
                    Secure VPN Access
                      (Tailscale)
                            │
                            ▼
                Debian 13 + OpenMediaVault 8
                            │
                Docker Engine / Compose
                            │
        ┌───────────────────┴───────────────────┐
        │                                       │
        ▼                                       ▼
 VPN-Isolated Stack                      Public Services
 (Gluetun Network)                      (LAN/Tailscale)
        │                                       │
        │                                       │
 qBittorrent                           Jellyfin
 Sonarr                               Jellyseerr
 Radarr                               AdGuard Home
 Lidarr
 Bazarr
 Prowlarr
 FlareSolverr
```

This separation ensures download-related traffic remains isolated inside a WireGuard VPN while trusted services remain accessible to local and authenticated remote users.

---

# Host Operating System

The HomeLab is hosted on a repurposed Lenovo laptop running **Debian 13** with **OpenMediaVault 8**.

The host system is responsible for:

* Docker Engine
* Docker Compose
* Storage management
* SMB file sharing
* SSH administration
* Network interfaces
* Scheduled updates

OpenMediaVault simplifies NAS administration while Debian provides a stable Linux foundation.

---

# Hardware Platform

| Component        | Specification                  |
| ---------------- | ------------------------------ |
| Host             | Lenovo Laptop                  |
| CPU              | Intel Core i5-4200M @ 2.50 GHz |
| Memory           | 12 GB DDR3 RAM                 |
| Primary Storage  | 1 TB External SSD              |
| Previous Storage | 1 TB Internal SSD (Failed)     |

Although the hardware is relatively old, the project demonstrates that reliable infrastructure can be built using repurposed equipment when resources are carefully managed.

---

# Storage Architecture

Storage is organised into two primary directories.

```
Storage
│
├── Config
│   ├── Jellyfin
│   ├── Sonarr
│   ├── Radarr
│   ├── Jellyseerr
│   ├── qBittorrent
│   ├── Bazarr
│   ├── Lidarr
│   └── Prowlarr
│
└── Data
    ├── Movies
    ├── TV Shows
    ├── Downloads
    └── Music
```

Separating configuration data from application data simplifies:

* Backups
* Migration
* Disaster recovery
* Docker volume management
* Application updates

This design also made migration from the failed internal SSD to the external SSD significantly easier.

---

# Container Architecture

Each application is deployed as an independent Docker container managed through Docker Compose.

This architecture provides:

* Process isolation
* Independent updates
* Persistent storage
* Easy recovery
* Simplified troubleshooting

The infrastructure currently consists of the following major services:

| Category              | Services     |
| --------------------- | ------------ |
| Media                 | Jellyfin     |
| Requests              | Jellyseerr   |
| Downloads             | qBittorrent  |
| Indexers              | Prowlarr     |
| TV Management         | Sonarr       |
| Movie Management      | Radarr       |
| Music Management      | Lidarr       |
| Subtitle Management   | Bazarr       |
| Cloudflare Protection | FlareSolverr |
| VPN                   | Gluetun      |
| DNS                   | AdGuard Home |

---

# VPN Architecture

Security and privacy were important considerations when designing the infrastructure.

Rather than exposing download services directly to the host network, all torrent-related containers operate through a dedicated **Gluetun** container configured with **WireGuard**.

Applications using the VPN include:

* qBittorrent
* Sonarr
* Radarr
* Lidarr
* Bazarr
* Prowlarr
* FlareSolverr

Docker's shared network namespace (`network_mode: service:gluetun`) ensures all outbound traffic from these services is routed securely through the VPN without requiring individual VPN configurations inside each container.

Media streaming services remain outside the VPN to provide reliable local and remote access.

---

# Automated Media Workflow

One of the primary objectives of the HomeLab was to minimise manual interaction by automating media acquisition and organisation.

The workflow operates as follows:

```
User
   │
   ▼
Jellyseerr
   │
   ▼
Sonarr / Radarr
   │
   ▼
Prowlarr
   │
   ▼
qBittorrent
   │
   ▼
Download
   │
   ▼
Automatic Import
   │
   ▼
Jellyfin Library
```

The process enables users to request media directly through Jellyseerr. Once approved, the request is automatically processed, downloaded, organised into the correct library structure and made available within Jellyfin without manual intervention.

---

# Network Design

The HomeLab supports both local and remote connectivity.

### Local Network

Services are accessible through the local LAN while connected to the home network.

Examples include:

* Jellyfin
* OpenMediaVault
* Docker management
* SMB shares

---

### Remote Access

Secure remote access is provided through **Tailscale**, allowing authenticated users to access services without exposing them directly to the public Internet.

This approach offers:

* End-to-end encryption
* Simplified remote administration
* Reduced attack surface
* Secure access from trusted devices

---

# Service Isolation

Applications are grouped according to their function.

**Public Services**

* Jellyfin
* Jellyseerr
* AdGuard Home

**VPN Services**

* qBittorrent
* Sonarr
* Radarr
* Lidarr
* Bazarr
* Prowlarr
* FlareSolverr

Separating services in this manner improves security while maintaining reliable communication between containers.

---

# Design Principles

The HomeLab architecture follows several core engineering principles:

## Modularity

Each application operates independently and can be upgraded or replaced without affecting the remainder of the infrastructure.

## Persistence

Application configuration is stored outside containers using Docker volumes to ensure updates do not remove user data.

## Security

VPN isolation, Tailscale remote access and minimal public exposure reduce the overall attack surface.

## Scalability

New Docker services can be introduced with minimal changes to the existing infrastructure.

## Maintainability

Docker Compose provides reproducible deployments while the organised storage layout simplifies administration and backups.

---

# Future Architecture Improvements

Planned enhancements include:

* Grafana monitoring
* Prometheus metrics collection
* Homepage dashboard
* Reverse proxy improvements
* Automated Docker health monitoring
* Infrastructure as Code
* Centralised logging
* Automated backup validation

These additions will further improve observability, maintainability and operational resilience while providing additional opportunities to develop practical infrastructure engineering skills.

---

# Summary

This HomeLab demonstrates the practical application of Linux administration, Docker Compose, networking, VPN integration and infrastructure automation using readily available hardware.

Although initially developed as a self-hosted media server, the project evolved into a comprehensive learning platform that continues to support experimentation with enterprise technologies while providing a reliable service for everyday use.
