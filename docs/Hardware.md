# Hardware

This document describes the physical hardware used to build the Self-Hosted Media Infrastructure, the reasoning behind the hardware choices, storage configuration and future upgrade plans.

---

# Overview

Unlike many HomeLab environments built using enterprise servers or dedicated mini PCs, this project was intentionally developed using repurposed consumer hardware.

The primary objective was to demonstrate that a reliable, containerised infrastructure can be designed and maintained without requiring expensive enterprise equipment.

By carefully managing system resources and selecting lightweight technologies, the server has remained capable of supporting multiple Docker services while providing reliable media streaming and automated workflows.

---

# Host System

| Component        | Specification                          |
| ---------------- | -------------------------------------- |
| Device           | Lenovo Laptop (Repurposed)             |
| Processor        | Intel Core i5-4200M @ 2.50 GHz         |
| CPU Cores        | 2 Cores / 4 Threads                    |
| Memory           | 12 GB DDR3 RAM                         |
| Graphics         | NVIDIA GeForce 900 Series (Legacy GPU) |
| Operating System | Debian 13                              |
| NAS Platform     | OpenMediaVault 8                       |

Although originally designed as a personal laptop, the hardware has proven capable of supporting a lightweight HomeLab environment with multiple simultaneously running Docker containers.

---

# Why Repurpose Older Hardware?

Instead of purchasing new server hardware, an unused Lenovo laptop was selected as the host platform.

This decision provided several advantages:

* Reduced project cost
* Practical experience working with hardware limitations
* Lower power consumption than traditional servers
* Opportunity to extend the useful lifespan of existing hardware
* Real-world optimisation of system resources

Using older hardware also encouraged careful planning of CPU, memory and storage usage rather than relying on abundant resources.

---

# Processor

The HomeLab is powered by an **Intel Core i5-4200M**, a dual-core processor with Hyper-Threading.

Although modest compared to modern server processors, it is well suited for:

* Docker container hosting
* Linux administration
* Network services
* Media management
* Lightweight virtualisation tasks

CPU utilisation remains relatively low during normal operation because each Docker container performs a specialised role with minimal overhead.

---

# Memory

The server currently contains **12 GB DDR3 RAM**.

Memory is shared across:

* Debian operating system
* OpenMediaVault
* Docker Engine
* Docker containers
* System cache

Despite hosting numerous services simultaneously, memory usage remains within acceptable limits due to the lightweight nature of the deployed applications.

The project also demonstrated the importance of monitoring memory consumption when deploying additional containers.

---

# Storage

## Original Configuration

Initially the HomeLab operated using a **1 TB internal SSD**.

This drive contained:

* Operating system
* Docker configurations
* Application data
* Media libraries

---

## Hardware Failure

During development the internal SSD failed.

Although unexpected, this became an important learning experience.

Rather than abandoning the project, the infrastructure was rebuilt using a separate storage device while preserving Docker configurations and application data wherever possible.

This incident reinforced the importance of maintaining organised storage layouts and persistent Docker volumes.

---

## Current Configuration

The server now operates using a **1 TB external SSD**.

Current storage responsibilities include:

* Operating system
* Docker volumes
* Application configuration
* Media libraries
* Downloads

The migration demonstrated that containerised infrastructure can be recovered efficiently when application data is stored independently from container images.

---

# Storage Organisation

Storage is divided into two primary directories.

```text
Storage
│
├── Config
│   ├── Jellyfin
│   ├── Jellyseerr
│   ├── Sonarr
│   ├── Radarr
│   ├── Lidarr
│   ├── Bazarr
│   ├── Prowlarr
│   ├── qBittorrent
│   └── Other Services
│
└── Data
    ├── Movies
    ├── TV Shows
    ├── Music
    ├── Downloads
    └── Media Assets
```

Separating configuration files from application data simplifies:

* Backup procedures
* Migration
* Disaster recovery
* Docker volume management
* Container recreation

---

# Performance Considerations

Because the server runs on older hardware, several design decisions were made to optimise performance.

These include:

* Lightweight Linux distribution
* Docker Compose instead of virtual machines
* Persistent Docker volumes
* Container isolation
* Minimal background services
* Efficient storage organisation

This approach allows multiple services to operate simultaneously without significantly impacting system responsiveness.

---

# Power Efficiency

Repurposing a laptop provides several practical advantages over traditional server hardware.

These include:

* Lower electrical power consumption
* Integrated battery backup during brief power interruptions
* Quiet operation
* Compact physical footprint

Although not intended as a replacement for enterprise servers, the platform is well suited for continuous HomeLab experimentation.

---

# Reliability

The HomeLab has been designed with maintainability in mind.

Reliability is improved through:

* Persistent Docker volumes
* Automatic container restart policies
* Organised storage layout
* Docker Compose deployment
* Scheduled software updates
* Clear documentation

The migration following the internal SSD failure further demonstrated the resilience provided by this architecture.

---

# Monitoring

Current monitoring primarily consists of:

* OpenMediaVault dashboard
* Docker container status
* System resource utilisation
* Storage usage
* Service availability

Future improvements will include more advanced infrastructure monitoring.

---

# Future Hardware Upgrades

Potential future improvements include:

* Dedicated Mini PC or low-power server
* Additional RAM
* Larger SSD storage
* RAID storage configuration
* Network Attached Storage expansion
* Uninterruptible Power Supply (UPS)

The current hardware continues to provide an excellent platform for experimentation while these upgrades remain optional.

---

# Lessons Learned

Building the HomeLab on repurposed hardware demonstrated that successful infrastructure projects depend more on thoughtful system design than on expensive equipment.

Working within hardware limitations encouraged:

* Efficient resource management
* Careful container planning
* Storage optimisation
* Practical troubleshooting
* Incremental infrastructure development

The unexpected SSD failure further reinforced the importance of planning for hardware failures and designing systems that can be recovered with minimal disruption.

---

# Summary

The hardware used for this HomeLab reflects the philosophy that meaningful technical learning can be achieved using readily available equipment.

Rather than focusing on high-end specifications, the project emphasises careful planning, efficient resource utilisation and maintainable system design.

Despite operating on a repurposed Lenovo laptop, the HomeLab successfully supports multiple containerised services, automated workflows and secure remote access while continuing to serve as a platform for ongoing experimentation and learning.
