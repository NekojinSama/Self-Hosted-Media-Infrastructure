# Self-Hosted Media Infrastructure

> A documented HomeLab project built on **Debian 13**, **OpenMediaVault 8** and **Docker Compose** to learn Linux administration, container orchestration, networking and infrastructure engineering through hands-on deployment.

![License](https://img.shields.io/badge/license-MIT-green)
![Docker](https://img.shields.io/badge/Docker-Compose-blue)
![OS](https://img.shields.io/badge/OS-Debian%2013-red)
![OMV](https://img.shields.io/badge/OpenMediaVault-8-orange)

---

# OpenMediaVault Dashboard

![OMV Dashboard](screenshots/omv-dashboard.png)

*Overview of the HomeLab host showing running services, storage utilisation and Docker containers.*

---

# Table of Contents

- Overview
- Key Features
- Technology Stack
- Hardware
- Architecture
- Media Workflow
- Repository Structure
- Documentation
- Screenshots
- Engineering Challenges
- Lessons Learned
- Future Roadmap
- License
- Author

---

# Overview

This repository documents my personal HomeLab infrastructure developed over approximately **six months** using a repurposed Lenovo laptop.

Originally created as a self-hosted media server for my family, the project gradually evolved into a practical infrastructure laboratory where I could explore:

- Linux administration
- Docker Compose
- Container networking
- VPN networking
- Storage management
- Infrastructure documentation
- Service orchestration
- Troubleshooting

Rather than simply deploying applications, the objective was to understand **how modern infrastructure is designed, maintained and debugged**.

The platform currently supports secure access for three users both locally and remotely through Tailscale.

---

# Key Features

- Debian 13 + OpenMediaVault 8
- Docker Compose deployment
- VPN-isolated download stack using Gluetun (WireGuard)
- Automated media acquisition
- Secure remote access using Tailscale
- Jellyfin media streaming
- Centralised media requests with Jellyseerr
- Persistent Docker volumes
- Organised storage layout
- Automatic media importing
- Scheduled container updates
- Fully documented architecture

---

# Technology Stack

| Category | Technologies |
|-----------|--------------|
| Operating System | Debian 13, OpenMediaVault 8 |
| Containers | Docker, Docker Compose |
| Media | Jellyfin, Jellyseerr |
| Automation | Sonarr, Radarr, Lidarr, Bazarr, Prowlarr |
| Downloads | qBittorrent |
| Networking | Docker Networks, WireGuard, Gluetun, Tailscale |
| DNS | AdGuard Home |
| Utilities | FlareSolverr |

---

# Hardware

| Component | Specification |
|------------|---------------|
| Host | Lenovo Laptop |
| CPU | Intel Core i5-4200M |
| RAM | 12 GB DDR3 |
| Storage | 1 TB External SSD |
| Previous Storage | 1 TB Internal SSD (Failed) |
| Operating System | Debian 13 |
| NAS Platform | OpenMediaVault 8 |

Although modest by modern standards, this hardware has proven capable of running multiple containerised services simultaneously while remaining stable for continuous operation.

---

# Architecture

![Architecture Diagram](diagrams/homelab.png)

The infrastructure is organised into modular Docker containers with VPN-isolated download services and secure remote access via Tailscale.

A detailed explanation is available in:

📄 **docs/Architecture.md**

---

# Automated Media Workflow

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

Users simply request content through Jellyseerr. The remaining workflow is completely automated.

---

# Repository Structure

```text
Self-Hosted-Media-Infrastructure
│
├── README.md
├── LICENSE
├── .gitignore
│
├── docs/
│   ├── Architecture.md
│   ├── Deployment.md
│   ├── Docker.md
│   ├── Networking.md
│   ├── Hardware.md
│   ├── Troubleshooting.md
│   └── FuturePlans.md
│
├── diagrams/
│
├── docker/
│
├── screenshots/
│
└── assets/
```

---

# Documentation

| Document | Description |
|----------|-------------|
| 📐 [Architecture](docs/Architecture.md) | Infrastructure design and storage layout |
| 🐳 [Docker](docs/Docker.md) | Docker Compose deployment and container architecture |
| 🌐 [Networking](docs/Networking.md) | Docker networking, VPN and Tailscale |
| 🚀 [Deployment](docs/Deployment.md) | Complete deployment process |
| 💻 [Hardware](docs/Hardware.md) | Hardware platform and storage configuration |
| 🔧 [Troubleshooting](docs/Troubleshooting.md) | Problems encountered and solutions |
| 📈 [Future Plans](docs/FuturePlans.md) | Planned improvements |

---

# Screenshots

## OpenMediaVault

![OMV](screenshots/omv-dashboard.png)

Host operating system dashboard showing storage, CPU usage and Docker services.

---

## Jellyfin

![Jellyfin](screenshots/jellyfin-home.png)

Primary media streaming platform.

---

## Jellyseerr

![Jellyseerr](screenshots/jellyseer-dashboard.png)

Media request interface connected to Sonarr and Radarr.

---

## Tailscale

![Tailscale](screenshots/tailscale-admin.png)

Secure remote access without exposing services to the Internet.

---

## AdGuard Home

![AdGuard](screenshots/adguard-dashboard.png)

DNS filtering and local network advertisement blocking.

---

# Engineering Challenges

Throughout development I encountered numerous real-world infrastructure problems including:

- Internal SSD hardware failure
- Migration to external SSD
- Docker networking issues
- WireGuard VPN configuration
- Container communication
- API integration between services
- Storage organisation
- Persistent Docker volumes

Solving these problems significantly improved my troubleshooting and Linux administration skills.

---

# Lessons Learned

This project strengthened my understanding of:

- Linux Administration
- Docker Compose
- Container Networking
- VPN Configuration
- Infrastructure Documentation
- Service Integration
- Storage Management
- Infrastructure Planning
- Troubleshooting
- Independent Learning

More importantly, it demonstrated that reliable infrastructure depends on thoughtful system design rather than expensive hardware.

---

# Future Roadmap

Planned improvements include:

- Homepage Dashboard
- Grafana Monitoring
- Prometheus Metrics
- Reverse Proxy
- Infrastructure as Code
- Automated Backups
- Centralised Logging
- Docker Health Monitoring
- Cloud Integration

Detailed roadmap:

📄 **docs/FuturePlans.md**

---

# License

This repository is licensed under the MIT License.

See [LICENSE](LICENSE).

---

# Author

**Ashwin Bhanware**

This repository serves as both a technical portfolio and a record of my continued learning in Linux administration, networking, containerisation and infrastructure engineering.

As I continue my postgraduate studies and professional career, this HomeLab will evolve to incorporate more advanced cloud and enterprise technologies.
