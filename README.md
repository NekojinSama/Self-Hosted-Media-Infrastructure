# Self-Hosted Media Infrastructure

> A personal HomeLab project documenting the design, deployment and management of a self-hosted media infrastructure built using **Debian 13**, **OpenMediaVault 8** and **Docker Compose**.

![Architecture](diagrams/homelab.png)

---

## Overview

This repository documents my self-hosted HomeLab infrastructure, developed over approximately six months to gain practical experience with Linux administration, Docker containerisation, networking, storage management and service orchestration.

Originally started as a centralised media server for my family, the project gradually evolved into a personal infrastructure laboratory where I could safely deploy, configure, troubleshoot and document enterprise-style technologies.

The infrastructure currently supports three users and provides secure local and remote access while automating media acquisition, organisation and streaming through an integrated ecosystem of Docker services.

Rather than functioning as a simple media server, this project represents a practical learning platform that has strengthened my understanding of:

- Linux system administration
- Docker Compose orchestration
- Container networking
- VPN isolation using WireGuard
- Storage management
- Secure remote networking
- Service integration
- Infrastructure troubleshooting

---

# Project Motivation

The objective of this project was not simply to host media.

I wanted to create an environment where I could continuously develop practical infrastructure skills beyond university coursework by building, maintaining and troubleshooting a production-like server running on repurposed hardware.

Throughout the project I intentionally avoided relying entirely on tutorials. Instead, I spent significant time researching Linux documentation, Docker networking, VPN routing, community forums and official project documentation in order to understand how each technology worked and how different services communicate with one another.

This approach allowed me to experience many of the same challenges encountered in real infrastructure administration, including hardware failures, container networking issues, VPN routing problems and storage migration.

---

# Objectives

The primary objectives of this project were:

- Build a reliable self-hosted media platform
- Learn Linux server administration
- Gain experience with Docker Compose
- Understand container networking
- Implement secure remote access
- Automate media acquisition and organisation
- Document deployment decisions
- Improve troubleshooting and problem-solving skills
- Create a platform for future experimentation with enterprise technologies

---

# Key Features

- Debian 13 server
- OpenMediaVault 8 NAS platform
- Docker Compose based deployment
- VPN-isolated download services using Gluetun
- WireGuard VPN integration
- Automated media management
- Secure remote access through Tailscale
- Centralised media streaming using Jellyfin
- Persistent Docker volumes
- Organised configuration management
- Automated media requests through Jellyseerr
- Automatic importing and library management
- Periodic container updates
- Fully documented infrastructure

---

# Hardware Specifications

| Component | Specification |
|------------|---------------|
| Host Machine | Repurposed Lenovo Laptop |
| CPU | Intel Core i5-4200M @ 2.50 GHz |
| Memory | 12 GB DDR3 RAM |
| Primary Storage | 1 TB External SSD |
| Previous Storage | 1 TB Internal SSD (Hardware Failure) |
| Operating System | Debian 13 |
| NAS Platform | OpenMediaVault 8 |
| Container Platform | Docker Compose |

---

# Technology Stack

## Operating System

- Debian 13
- OpenMediaVault 8

## Container Platform

- Docker
- Docker Compose

## Networking

- Tailscale
- WireGuard
- Gluetun VPN
- Docker Networks

## Media

- Jellyfin
- Jellyseerr

## Automation

- Sonarr
- Radarr
- Lidarr
- Bazarr
- Prowlarr

## Downloads

- qBittorrent

## Utilities

- FlareSolverr
- AdGuard Home

---

# System Architecture

The HomeLab follows a modular Docker Compose architecture where each application runs inside its own container while sharing persistent storage volumes.

VPN-sensitive applications communicate exclusively through a dedicated Gluetun container using Docker's `network_mode: service:gluetun`, ensuring download traffic remains isolated while media streaming services remain accessible locally and remotely.

Insert architecture diagram here.

---

# Engineering Decisions

| Technology | Reason |
|------------|--------|
| Debian | Stable and lightweight Linux server platform |
| OpenMediaVault | Simplified NAS and storage management |
| Docker Compose | Reproducible container deployments |
| Jellyfin | Open-source media streaming platform |
| Tailscale | Secure remote access without exposing ports |
| Gluetun | VPN isolation using WireGuard |
| Docker Volumes | Persistent configuration management |
| External SSD | Reliable storage after internal drive failure |

---

# Storage Structure

The storage layout is intentionally separated into configuration and application data.

```
Storage
│
├── Config
│   ├── Jellyfin
│   ├── Radarr
│   ├── Sonarr
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

Separating configuration files from media storage simplifies backup, migration and disaster recovery.

---

# Automated Media Workflow

The media pipeline has been designed to minimise manual intervention.

```text
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

The workflow allows users to request media directly through Jellyseerr. Requests are automatically processed, downloaded, organised into the correct folder structure and made available within Jellyfin without manual interaction.

---

# Networking

The infrastructure supports both local and remote access.

### Local Access

- Local Area Network
- OpenMediaVault Dashboard
- Jellyfin
- Docker Services

### Remote Access

- Tailscale VPN
- Secure encrypted communication
- Access without exposing services to the public Internet

---

# Security

Security has been considered throughout the project.

Measures include:

- WireGuard VPN
- Gluetun container isolation
- No unnecessary public port exposure
- Persistent Docker volumes
- Docker network separation
- Secure remote access through Tailscale
- Regular system updates

---

# Challenges Encountered

Throughout development I encountered numerous technical challenges which significantly improved my troubleshooting abilities.

## Internal SSD Failure

The original internal SSD failed during development.

Instead of rebuilding the server from scratch, I migrated the infrastructure to an external SSD while preserving Docker configurations and persistent data.

---

## WireGuard Migration

Migrated from OpenVPN to WireGuard after researching performance and reliability improvements.

This required:

- Docker Compose modifications
- Environment variable configuration
- VPN endpoint configuration
- Network namespace troubleshooting

---

## Docker Networking

One of the most challenging aspects involved understanding how Docker containers communicate.

Topics explored included:

- Docker bridge networking
- Network namespaces
- Shared container networking
- Volume mapping
- Port forwarding
- Service dependencies

---

## Container Communication

Considerable effort was spent configuring communication between:

- Jellyseerr
- Sonarr
- Radarr
- Prowlarr
- qBittorrent
- Jellyfin

Achieving reliable automation required understanding API integration, networking and service discovery within Docker Compose.

---

# Lessons Learned

This project significantly improved my understanding of:

- Linux administration
- Infrastructure planning
- Docker Compose
- Container networking
- VPN configuration
- Storage management
- Service orchestration
- Technical documentation
- Infrastructure troubleshooting
- Independent learning

Perhaps the most valuable lesson was recognising that successful infrastructure engineering is not simply about deploying software, but understanding how multiple systems interact and systematically diagnosing problems when they arise.

---

# Future Improvements

Planned enhancements include:

- Grafana monitoring
- Prometheus metrics
- Homepage dashboard
- Infrastructure as Code
- Automated backups
- Docker health monitoring
- Centralised logging
- Reverse proxy improvements
- CI/CD for Docker Compose deployments

---

# Repository Structure

```
Self-Hosted-Media-Infrastructure
│
├── README.md
├── LICENSE
├── .gitignore
│
├── assets/
├── diagrams/
├── docker/
├── docs/
└── screenshots/
```

---

# Screenshots

Planned documentation includes:

- OpenMediaVault Dashboard
- Docker Compose Services
- Jellyfin
- Jellyseerr
- qBittorrent
- AdGuard Home
- Tailscale
- Network Architecture
- Storage Layout

---

# Skills Demonstrated

| Category | Skills |
|------------|--------|
| Operating Systems | Debian, OpenMediaVault |
| Linux Administration | File systems, permissions, SSH |
| Containers | Docker, Docker Compose |
| Networking | Docker Networking, Tailscale, WireGuard |
| Storage | Volume management, NAS organisation |
| Security | VPN isolation, secure remote access |
| Media Automation | Sonarr, Radarr, Prowlarr, Jellyseerr |
| Documentation | Architecture diagrams, technical documentation |
| Troubleshooting | Networking, VPN, storage migration, Docker debugging |

---

# Acknowledgements

This project was made possible through the excellent open-source communities behind:

- Debian
- OpenMediaVault
- Docker
- Jellyfin
- Gluetun
- Tailscale
- Sonarr
- Radarr
- Prowlarr
- Jellyseerr
- Bazarr
- Lidarr

whose documentation, community forums and development efforts greatly assisted throughout the learning process.

---

## Author

**Ashwin Bhanware**

This repository documents my ongoing journey in Linux system administration, self-hosting, networking and enterprise infrastructure. It serves as both a technical portfolio and a record of continuous learning, and will continue to evolve as I explore new technologies and expand the capabilities of my HomeLab.
