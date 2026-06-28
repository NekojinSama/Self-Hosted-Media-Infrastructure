# Networking

This document describes the networking architecture of the Self-Hosted Media Infrastructure, including local network communication, Docker networking, VPN integration, remote access, DNS management and the security principles guiding the overall design.

---

# Overview

Networking is one of the core components of the HomeLab architecture. The objective was to build a system that remains secure, accessible and easy to maintain while supporting both local and remote users.

Rather than exposing services directly to the Internet, the infrastructure uses a layered networking approach consisting of:

* Local Area Network (LAN)
* Docker bridge networking
* WireGuard VPN (via Gluetun)
* Tailscale mesh VPN
* Internal container communication

This design allows services to communicate efficiently while minimising unnecessary exposure to external networks.

---

# Network Architecture

Insert the network topology diagram below.

![Network Topology](../diagrams/network-topology.png)

**Figure 1.** Network communication between the host system, Docker containers and remote users.

---

# High-Level Network Flow

```text
                        Internet
                            │
                     Tailscale VPN
                            │
                            ▼
                  Debian + OpenMediaVault
                            │
                  Docker Bridge Network
          ┌─────────────────┴─────────────────┐
          │                                   │
          ▼                                   ▼
   Public Service Stack                VPN Service Stack
          │                                   │
          │                             WireGuard Tunnel
          │                                   │
          ▼                                   ▼
      Jellyfin                          Gluetun
      Jellyseerr                             │
      AdGuard Home                           │
                                             ▼
                                 qBittorrent
                                 Sonarr
                                 Radarr
                                 Lidarr
                                 Bazarr
                                 Prowlarr
                                 FlareSolverr
```

The architecture separates user-facing services from applications requiring VPN-protected outbound traffic.

---

# Local Network

When connected to the home network, services are accessed directly through the local LAN.

The local network provides access to:

* OpenMediaVault Web Interface
* Jellyfin
* Jellyseerr
* SMB File Shares
* SSH
* Docker Services

Using the LAN provides lower latency and avoids unnecessary VPN overhead when users are connected to the same network.

---

# Remote Access

Secure remote access is provided using **Tailscale**.

Rather than exposing ports through the router or configuring traditional VPN servers, Tailscale creates a secure mesh VPN between authorised devices.

Current users include:

* Primary administrator
* Family member (remote)
* Occasional secondary user

Advantages include:

* End-to-end encryption
* No router port forwarding
* Simplified remote administration
* Device-based authentication
* Reduced attack surface

This approach allows authorised users to securely access services from anywhere while keeping the infrastructure inaccessible to unauthorised Internet traffic.

---

# Docker Networking

Docker provides the internal networking required for container communication.

Each application communicates with other services using Docker's internal DNS rather than fixed IP addresses.

For example:

* Jellyseerr communicates with Sonarr and Radarr
* Sonarr communicates with Prowlarr
* Radarr communicates with Prowlarr
* Prowlarr communicates with FlareSolverr
* qBittorrent receives downloads
* Jellyfin accesses organised media directories

Because Docker provides automatic service discovery, containers communicate using service names rather than manually assigned addresses.

This improves portability and simplifies future infrastructure changes.

---

# VPN Isolation

Privacy-sensitive applications are isolated behind a dedicated **Gluetun** container configured with **WireGuard**.

Applications sharing the VPN namespace include:

* qBittorrent
* Sonarr
* Radarr
* Lidarr
* Bazarr
* Prowlarr
* FlareSolverr

These containers use Docker's shared network namespace:

```yaml
network_mode: service:gluetun
```

Instead of maintaining separate VPN configurations for each application, all traffic is routed through a single secure WireGuard tunnel managed by Gluetun.

Benefits include:

* Consistent VPN routing
* Simplified configuration
* Easier troubleshooting
* Reduced configuration duplication

---

# Public Services

Applications intended for user interaction remain outside the VPN.

These include:

* Jellyfin
* Jellyseerr
* AdGuard Home

Keeping these services outside the VPN ensures:

* Faster local streaming
* Reliable remote access through Tailscale
* Reduced network complexity

---

# Internal Service Communication

The infrastructure relies on API communication between containers.

The automated media workflow depends on continuous communication between multiple services.

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
FlareSolverr
      │
      ▼
qBittorrent
      │
      ▼
Media Storage
      │
      ▼
Jellyfin
```

Each service performs a specialised role while exchanging information through HTTP APIs and Docker networking.

---

# DNS Management

The HomeLab includes **AdGuard Home** for local DNS filtering.

Current responsibilities include:

* DNS resolution
* Advertisement blocking
* Network-wide filtering

Future plans include expanding DNS management to improve monitoring and visibility across the local network.

---

# File Sharing

OpenMediaVault provides SMB shares for persistent storage.

These shares are used for:

* Media storage
* Docker configuration
* Administrative access

Separating shared storage from container images simplifies maintenance and backup operations.

---

# SSH Administration

The server is administered remotely through SSH.

SSH is primarily used for:

* Docker management
* System updates
* Log inspection
* Troubleshooting
* Storage management

Command-line administration has been invaluable for learning Linux system management and resolving infrastructure issues efficiently.

---

# Security Considerations

Networking decisions throughout the project were guided by several security principles.

## Minimal Exposure

No unnecessary services are exposed directly to the public Internet.

---

## Encrypted Remote Access

Remote users connect through Tailscale rather than open ports.

---

## VPN Routing

Download-related services operate exclusively through WireGuard.

---

## Service Isolation

Applications are grouped according to their networking requirements.

---

## Docker Networking

Containers communicate internally without requiring unnecessary external exposure.

---

# Network Challenges

Developing the networking architecture required overcoming several technical challenges.

These included:

* Docker container communication
* VPN namespace configuration
* WireGuard migration
* API connectivity between services
* Port allocation
* Docker networking concepts
* Persistent configuration management

Researching these topics significantly improved my understanding of modern infrastructure networking and container orchestration.

---

# Lessons Learned

Designing the HomeLab networking architecture provided practical experience with:

* Docker bridge networking
* Service discovery
* Container communication
* VPN routing
* WireGuard
* Tailscale mesh networking
* Linux networking fundamentals
* Secure remote administration

One of the most valuable lessons was understanding that networking is not simply about assigning IP addresses. Reliable infrastructure depends on designing clear communication paths, separating responsibilities and reducing unnecessary exposure while maintaining accessibility.

---

# Future Improvements

The networking architecture will continue evolving as new technologies are introduced.

Planned improvements include:

* Homepage dashboard integration
* Network monitoring
* Grafana visualisation
* Prometheus metrics
* Reverse proxy implementation
* Automated service health monitoring
* Improved DNS observability

These enhancements will provide greater visibility into network performance while further strengthening practical experience with enterprise infrastructure management.

---

# Summary

The networking architecture combines Docker networking, WireGuard VPN routing, Tailscale remote access and OpenMediaVault storage services into a secure and maintainable infrastructure.

By separating user-facing services from VPN-protected applications and relying on Docker's internal networking model, the HomeLab remains both flexible and secure while supporting automated workflows and remote access for multiple users.

The experience gained through designing, configuring and troubleshooting this networking environment has substantially strengthened my understanding of Linux networking, container communication and secure infrastructure design.
