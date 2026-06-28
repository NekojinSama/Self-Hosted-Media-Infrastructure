# Troubleshooting & Lessons Learned

This document records the major technical challenges encountered during the development of the HomeLab and the approaches used to investigate and resolve them.

The purpose of this document is not simply to record solutions, but to demonstrate the troubleshooting process, engineering decisions and lessons learned while building a self-hosted infrastructure.

---

# Philosophy

Throughout this project I adopted a simple troubleshooting methodology.

Rather than immediately searching for complete solutions, I attempted to understand:

* What changed?
* What is failing?
* What still works?
* Where does communication stop?
* Which logs contain useful information?

Most problems were solved through systematic testing, documentation and experimentation rather than trial-and-error.

---

# Troubleshooting Workflow

The following workflow became my standard approach whenever issues occurred.

```text
Problem Identified
        │
        ▼
Gather Logs
        │
        ▼
Research Documentation
        │
        ▼
Form Hypothesis
        │
        ▼
Test One Change
        │
        ▼
Observe Result
        │
        ▼
Document Solution
```

This process greatly reduced unnecessary configuration changes and helped identify root causes more efficiently.

---

# Challenge 1 — Internal SSD Failure

## Problem

During development the original internal SSD failed, making the server unreliable and preventing normal operation.

---

## Investigation

Before rebuilding the entire system, I evaluated whether Docker configurations and application data could be migrated to another storage device.

Because Docker volumes and configuration directories had already been organised separately from the operating system, migration became significantly easier than expected.

---

## Solution

The infrastructure was migrated to a 1 TB external SSD.

This involved:

* reinstalling the operating system
* reinstalling OpenMediaVault
* restoring Docker Compose configurations
* reconnecting persistent storage
* validating service functionality

---

## Lessons Learned

This experience reinforced the importance of:

* persistent Docker volumes
* organised storage layouts
* external configuration management
* maintaining documentation

It also highlighted why separating application data from container images simplifies disaster recovery.

---

# Challenge 2 — Migrating from OpenVPN to WireGuard

## Problem

The original VPN configuration used OpenVPN.

While functional, I wanted to improve performance and modernise the networking stack by migrating to WireGuard through Gluetun.

---

## Investigation

Before making changes I researched:

* WireGuard
* Gluetun documentation
* Docker networking
* WireGuard environment variables
* NordVPN configuration

Understanding how Gluetun established VPN connections was necessary before modifying the Compose configuration.

---

## Difficulties Encountered

Migration introduced several problems.

These included:

* incorrect environment variables
* authentication issues
* endpoint configuration
* routing failures
* container communication problems

Several iterations of the Compose configuration were required before the VPN established a stable connection.

---

## Solution

The deployment was updated to use WireGuard with Gluetun.

Configuration included:

* VPN credentials
* WireGuard private key
* server region selection
* Docker networking updates
* environment variable validation

Following successful deployment all download-related services shared the VPN namespace.

---

## Lessons Learned

This migration significantly improved my understanding of:

* VPN protocols
* WireGuard
* Docker networking
* Linux networking
* environment variables
* secure routing

---

# Challenge 3 — Docker Networking

## Problem

Initially I assumed Docker containers communicated similarly to applications installed directly on the host operating system.

As more services were added this assumption proved incorrect.

Applications frequently failed to communicate because networking relationships had not been fully understood.

---

## Investigation

Research focused on:

* Docker bridge networks
* service discovery
* container names
* Docker DNS
* network namespaces
* exposed ports

Understanding these concepts was essential before configuring API communication between applications.

---

## Solution

Rather than relying on IP addresses, containers communicate using Docker service names.

Docker Compose automatically manages service discovery, making deployments easier to maintain.

---

## Lessons Learned

Understanding Docker networking was one of the most valuable learning experiences during the project.

It fundamentally changed how I think about application communication within containerised environments.

---

# Challenge 4 — VPN Namespace Isolation

## Problem

Download applications needed VPN protection while media streaming services needed reliable local access.

Initially I attempted to configure VPN functionality independently inside several containers.

This quickly became difficult to maintain.

---

## Investigation

Research introduced Docker's shared network namespace.

Understanding:

```yaml
network_mode: service:gluetun
```

was a turning point in the project.

---

## Solution

Rather than configuring multiple VPN clients, Gluetun became the single VPN endpoint.

Applications requiring secure outbound traffic share Gluetun's network namespace.

Benefits included:

* simplified configuration
* consistent VPN routing
* easier maintenance
* improved reliability

---

## Lessons Learned

This approach demonstrated the value of designing infrastructure around reusable services rather than duplicating configuration.

---

# Challenge 5 — Service Integration

## Problem

The HomeLab relies on multiple independent applications working together.

Initially several API connections failed due to incorrect URLs, ports or authentication settings.

---

## Investigation

Each application was tested independently before validating communication with connected services.

Logs were examined to identify failed API requests.

Connections were then rebuilt one service at a time.

---

## Solution

Applications were integrated gradually.

Final communication chain:

```text
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
Jellyfin
```

Breaking the problem into smaller components proved significantly easier than attempting to configure every application simultaneously.

---

## Lessons Learned

Complex systems become much easier to troubleshoot when individual components are verified independently before testing full system integration.

---

# Challenge 6 — Learning Linux

## Problem

Before beginning this project I had limited practical experience administering Linux servers.

Many administrative tasks initially required significant research.

---

## Skills Developed

Over time I became comfortable with:

* SSH
* Linux filesystem navigation
* permissions
* Docker CLI
* Compose management
* system logs
* service management
* storage configuration

---

## Lessons Learned

Perhaps the biggest lesson from this project was recognising that Linux administration is not memorising commands.

Instead it involves understanding how the operating system, networking, storage and applications interact.

---

# General Lessons Learned

Over approximately six months the HomeLab evolved from a simple media server into a practical infrastructure engineering project.

The project strengthened my understanding of:

* Linux administration
* Docker Compose
* Container networking
* VPN technologies
* WireGuard
* Tailscale
* Storage management
* Service orchestration
* Infrastructure documentation
* Independent problem solving

More importantly, it taught me to approach technical problems methodically rather than relying on isolated fixes.

---

# Future Challenges

The HomeLab will continue evolving.

Future areas for exploration include:

* Grafana
* Prometheus
* Homepage dashboard
* Reverse proxy
* Infrastructure as Code
* Automated monitoring
* Centralised logging
* Backup validation

Each new addition will present opportunities to expand practical knowledge while improving the reliability and maintainability of the infrastructure.

---

# Summary

Building this HomeLab required considerably more than deploying Docker containers.

The project involved diagnosing hardware failures, understanding Linux administration, designing secure networking, integrating multiple services and systematically troubleshooting complex infrastructure problems.

These experiences have significantly improved my technical confidence and reinforced the importance of documentation, structured troubleshooting and continuous learning when managing modern infrastructure.

Rather than viewing problems as obstacles, they became valuable opportunities to deepen my understanding of enterprise technologies and develop a disciplined engineering mindset.
