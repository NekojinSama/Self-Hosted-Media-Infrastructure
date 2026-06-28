# Deployment Guide

This document describes the deployment process used to build the Self-Hosted Media Infrastructure from a clean installation of Debian and OpenMediaVault.

The guide outlines the deployment methodology, directory structure and configuration sequence without exposing private credentials or environment-specific information.

---

# Deployment Overview

The infrastructure is deployed in layers.

Each layer builds upon the previous one to ensure the system remains stable, modular and easy to troubleshoot.

The deployment process follows this order:

```text
Prepare Hardware
        │
        ▼
Install Debian
        │
        ▼
Install OpenMediaVault
        │
        ▼
Configure Storage
        │
        ▼
Install Docker
        │
        ▼
Create Directory Structure
        │
        ▼
Deploy Docker Compose Stacks
        │
        ▼
Configure Applications
        │
        ▼
Configure VPN
        │
        ▼
Enable Remote Access
        │
        ▼
Validate System
```

Each phase can be verified independently before continuing to the next.

---

# Prerequisites

Before deployment ensure the following are available:

## Hardware

* x86-64 compatible system
* Minimum 8 GB RAM (12 GB recommended)
* SSD storage
* Reliable network connection

## Software

* Debian 13
* OpenMediaVault 8
* Docker Engine
* Docker Compose

## Accounts

Several services require user accounts or API keys.

Examples include:

* VPN provider
* Tailscale
* Jellyfin
* Indexers
* Metadata providers

Sensitive credentials should always be stored using environment variables rather than directly inside Docker Compose files.

---

# Phase 1 — Operating System Installation

Install Debian 13 on the target system.

Recommended tasks:

* Update packages
* Configure hostname
* Create administrator account
* Configure SSH
* Verify network connectivity

After installation:

```bash
sudo apt update
sudo apt upgrade
```

Confirm the system is fully updated before installing additional software.

---

# Phase 2 — OpenMediaVault Installation

Install OpenMediaVault following the official installation process.

After installation:

* Configure web administration
* Verify storage devices
* Configure shared folders
* Enable SSH
* Configure user permissions

At this stage no Docker services should be deployed.

---

# Phase 3 — Install Docker

Docker is installed using the OpenMediaVault Compose plugin.

Verify installation:

```bash
docker --version
docker compose version
```

Confirm Docker starts automatically after reboot.

---

# Phase 4 — Storage Preparation

Create two primary storage locations.

```text
Storage
│
├── Config
└── Data
```

### Config

Stores:

* Jellyfin configuration
* Sonarr configuration
* Radarr configuration
* qBittorrent configuration
* Docker application data

### Data

Stores:

* Movies
* TV Shows
* Music
* Downloads
* Media assets

Separating configuration from media simplifies migration, backup and disaster recovery.

---

# Phase 5 — Docker Directory Structure

Recommended repository layout:

```text
docker/
│
├── docker-compose.yml
├── .env
└── compose-notes.md
```

Environment variables should be stored in the `.env` file and excluded from version control.

Examples include:

* VPN credentials
* API keys
* Passwords
* User IDs
* Timezone

---

# Phase 6 — Deploy Infrastructure

Deploy the Docker stack.

```bash
docker compose pull

docker compose up -d
```

After deployment verify all containers start successfully.

Useful commands:

```bash
docker ps
```

```bash
docker compose logs
```

```bash
docker compose restart
```

---

# Phase 7 — Initial Application Configuration

Each application requires basic configuration after deployment.

## Jellyfin

Configure:

* Media libraries
* User accounts
* Metadata providers

---

## Jellyseerr

Configure:

* Administrator account
* Jellyfin connection
* User permissions

---

## Sonarr

Configure:

* Root folders
* Download client
* Quality profiles

---

## Radarr

Configure:

* Root folders
* Download client
* Movie quality profiles

---

## Prowlarr

Configure:

* Indexers
* API connections
* Sonarr integration
* Radarr integration

---

## qBittorrent

Configure:

* Download directories
* Categories
* Network interface
* Authentication

---

# Phase 8 — Configure VPN

Deploy the Gluetun container.

Configure:

* VPN provider
* WireGuard credentials
* Server region
* Environment variables

Applications requiring VPN protection should share the Gluetun network namespace.

Example:

```yaml
network_mode: service:gluetun
```

After deployment verify the VPN tunnel is active before enabling download services.

---

# Phase 9 — Configure Remote Access

Install Tailscale.

Authenticate the device.

Verify remote connectivity from another trusted device.

Do not expose management services directly through router port forwarding.

---

# Phase 10 — Validation

After deployment verify:

* Containers are running
* VPN tunnel is connected
* Jellyfin streams media
* Jellyseerr submits requests
* Sonarr imports episodes
* Radarr imports movies
* qBittorrent downloads successfully
* Docker restarts correctly after reboot

The system should continue operating without manual intervention.

---

# Updating the Infrastructure

Container updates are performed periodically.

Typical update workflow:

```bash
docker compose pull

docker compose up -d
```

After updates:

* Inspect logs
* Verify VPN connectivity
* Confirm application communication
* Test media playback

---

# Backup Strategy

Although this repository does not include automated backup scripts, the infrastructure is designed to simplify backups.

Important items include:

* Docker Compose files
* Environment variables
* Config directory
* Media library metadata

Separating configuration from media reduces recovery time following hardware failure.

---

# Recovery Procedure

In the event of hardware failure:

1. Install Debian.
2. Install OpenMediaVault.
3. Install Docker.
4. Restore directory structure.
5. Restore configuration files.
6. Restore Docker Compose files.
7. Deploy containers.
8. Verify application connectivity.

This recovery process was validated during the migration from the failed internal SSD to the current external SSD.

---

# Deployment Principles

Several principles guided the deployment process.

## Incremental Deployment

Deploy one layer at a time.

---

## Validate Before Continuing

Verify functionality before introducing additional services.

---

## Externalise Configuration

Keep credentials outside Compose files.

---

## Document Everything

Record architecture, networking and troubleshooting decisions.

---

## Keep the Host Minimal

The host operating system should provide only the services required to support Docker.

Applications belong inside containers rather than on the host whenever practical.

---

# Lessons Learned

Deploying the HomeLab demonstrated that successful infrastructure deployment depends on planning rather than speed.

Building the environment incrementally made troubleshooting significantly easier and reduced the likelihood of introducing multiple variables simultaneously.

The deployment process also reinforced the importance of:

* documentation
* repeatable configuration
* persistent storage
* version-controlled Compose files
* modular infrastructure

---

# Summary

The deployment methodology prioritises simplicity, repeatability and maintainability.

By separating the deployment into clearly defined stages and validating each layer independently, the infrastructure can be recreated with minimal effort while remaining adaptable to future hardware and software changes.

Although developed as a personal HomeLab, the deployment approach reflects many of the practices used when deploying containerised services in professional environments, including modular design, Infrastructure as Code principles and configuration management.
