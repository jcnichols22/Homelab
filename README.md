# 👨🏻‍💻 Homelab

**Last Updated:** July 11th, 2025  
**Primary Purpose:**  
To provide a secure, self-hosted environment for network management, automation, and media streaming. This homelab leverages Proxmox virtualization, Docker containers, and a flat network architecture to deliver critical services (DNS, ad-blocking, monitoring, automation) and a robust media stack, all managed with strong security (Tailscale VPN), centralized documentation, and automated backups and updates.

---
[//]: # (homelab)

## 📑 Table of Contents

| [Relevant Repositories](#relevant-repositories) | [Hardware Configuration](#hardware-configuration) | [Secure Connectivity: Tailscale Mesh VPN](#secure-connectivity-tailscale-mesh-vpn) | [Network Architecture](#network-architecture) | [Proxmox Services](#proxmox) | [Media Server Stack](#media-server-stack) | [Backup & Maintenance](#backup--maintenance) | [Documentation & Change Management](#documentation--change-management) | [Future Plans](#future-plans) |

---

## 📦 Relevant Repositories

[⬆️ Return to Top](#👨🏻‍💻-homelab)

- [media](https://github.com/jcnichols22/media): Docker Compose files and configuration for the media server stack.
- [dotfiles](https://github.com/jcnichols22/dotfiles): Personal dotfiles for shell, editor, and system configuration.
- [Ansible](https://github.com/jcnichols22/Ansible): Main Ansible playbooks for automating homelab provisioning and updates.
- [ansible_scripts](https://github.com/jcnichols22/ansible_scripts): Additional Ansible scripts and roles for specialized automation tasks.

<a name="hardware-configuration"></a>

## 🔧 Hardware Configuration

[⬆️ Return to Top](#homelab)

### Core Devices

### PVE

- **Platform:** HP ProDesk 600 G3 Mini
- **OS:** Proxmox VE 8.0
- **Specs:** i5-7500T | 32GB RAM | 500GB SSD
- **Role:** Hosts different non critical services for productivity and monitoring in LXC containers

### PVE1

- **Platform:** HP ProDesk 600 G3 Mini
- **OS:** Proxmox VE 8.0
- **Specs:** i5-7500T | 16GB RAM | 500GB SSD
- **Role:** Hosts critical network services in LXC containers

### Media Server

- **Platform:** Rosewill RSV-Z2600U 2U Rackmount Case
- **Motherboard:** ASRock B365M-HDV
- **Processor:** Intel Core i5-8500
- **Memory:** 16GB DDR4 RAM
- **OS:** Ubuntu 24.04.2 LTS
- **Boot Drive:** 500GB NVMe SSD
- **Media Storage:** 2TB (MergerFS pool of 2 x 1TB HDDs)
- **Role:** Media stack via Docker containers (Jellyfin, Radarr, Sonarr, Prowlarr, etc.)
- **Purpose:** Purpose-built media server for 24/7 operation, scalable storage, and home/remote streaming.

### Networking

- **Router:** TP-Link ER-605 (Omada managed)
- **Switch:** TP-Link TL-SG1016 (16-port unmanaged)
- **Switch:** TP-Link TL-SG3428 (24-port managed Layer 2 Switch)
- **Access Points:** TP-Link EAP610
- **ISP:** AT&T Modem in bridge mode

<a name="secure-connectivity-tailscale-mesh-vpn"></a>

## 🔒 Secure Connectivity: Tailscale Mesh VPN

[⬆️ Return to Top](#homelab)

All core devices, services, and management interfaces are securely connected via Tailscale, creating a private WireGuard-based mesh network overlay across the entire homelab infrastructure.
This enables:

- Secure remote access to all services from anywhere, without exposing ports to the public internet.
- Seamless inter-device communication across physical locations and networks.
- Simple device onboarding and access control via Tailscale ACLs.

<a name="network-architecture"></a>

## 🌐 Network Architecture

[⬆️ Return to Top](#homelab)

ISP Modem (Passthrough)  
|  
TP-Link ER-605 (Router)  
|
Tp-Link TL-SG1016 (Switch)
|
TP-Link EAP610 (Access Points)

- **Subnet:** 192.168.1.0/24 (Flat network)
- **DNS/Adblocking:** Pihole (LXC 100)
- **DHCP:** ER-605 handling leases

<a name="proxmox"></a>

## 🖥️ Proxmox Services (LXC Containers & VM)

[⬆️ Return to Top](#homelab)

### PVE Node

| CT/VM ID | Service             | Functionality                        |
|----------|---------------------|--------------------------------------|
| 100      | knightbus           | Main utility container               |
| 102      | homarr              | Service dashboard                    |
| 105      | myspeed             | Network speed test                   |
| 108      | nextcloudpi         | File sharing & collaboration         |
| 109      | netbox              | Network documentation                |
| 110      | phpipam             | IP address management                |
| 111      | reactive-resume     | Resume management                    |
| 112      | stirling-pdf        | PDF management                       |
| 114      | prometheus          | Monitoring and alerting              |
| 115      | grafana             | Visualization dashboard              |
| 116      | docker              | Container management                 |
| 117      | prometheus-pve-exp  | Proxmox node exporter for metrics    |
| 125      | linkwarden          | Bookmark & web archiving             |
| 101 (VM) | haos15.2            | Home Assistant OS VM                 |

### PVE1 Node

| CT/VM ID | Service           | Functionality                                 |
|----------|-------------------|-----------------------------------------------|
| 103      | omada             | Omada controller for router management        |
| 104      | pihole            | DNS & ad-blocking                            |
| 106      | rustdeskserver    | Remote access hub                            |
| 107      | uptimekuma        | Service monitoring                           |
| 113      | Ansible           | Automation and orchestration                 |
| 118      | adguard           | Ad-block/DNS service (alternative to Pihole) |

<a name="media"></a>

## 🎥 Media Server Stack

[⬆️ Return to Top](#homelab)

**Docker Compose Services:**

| Service    | Purpose            | Docker Image            |
| ---------- | ------------------ | ----------------------- |
| Radarr     | Movie management   | `linuxserver/radarr`    |
| Sonarr     | TV show management | `linuxserver/sonarr`    |
| Readarr    | Book management    | `linuxserver/readarr`   |
| Prowlarr   | Indexer aggregator | `linuxserver/prowlarr`  |
| Jellyfin   | Media streaming    | `jellyfin/jellyfin`     |
| Jellyseerr | Request management | `jellyseerr/jellyseerr` |

**Compose Version:** `3.8`

**Data Flow:**  
Content Requests → \*Arr Apps → Download Clients → Media Library → Jellyfin/Plex

<a name="backup--maintenance"></a>

## 🔄 Backup & Maintenance

[⬆️ Return to Top](#homelab)

- **Proxmox Backups**

  - Daily snapshots of critical containers
  - VM backups to external NAS weekly

- **Media Server Protections**

  - Currently, there are no backups for media due to the limited 2TB storage

- **Update Schedule:**

  - Every 1st Sunday at midnight cron job updates nodes, vm's, containers using Ansible playbooks

- **Automation:**

  - Ansible playbooks for configuration management

- **Monitoring & Alerts:**
  - Prometheus alerts for critical service failures
  - Grafana dashboards for real-time monitoring
  - Uptime Kuma for service availability checks

<a name="documentation"></a>

## 📜 Documentation & Change Management

[⬆️ Return to Top](#homelab)

- **Network Documentation:**
  - NetBox for network inventory and IP management
  - phpIPAM for IP address management
- **Service Documentation:**
  - Homarr dashboard for service status and links
  - Reactive Resume for personal documentation
- **Change Management:**
  - Ansible playbooks for configuration management
  - Version control via GitHub for all scripts and configurations

<a name="future-plans"></a>

## 🚀 Future Plans

[⬆️ Return to Top](#homelab)

- Implement VLAN segmentation
- Trunas SCALE for mass storage
- Transition to Ubiquiti (Router, POE Switch, 2 AP)
- Offsite Backup
- Remove as many paid cloud services as possible
- GitHub Actions for CI/CD
- Reverse engineer and use Ansible to deploy and configure services to PVE1 and PVE2
- Add more redundancy to the network
- Log management with Loki or Graylog
- Expand automation with Ansible, Terraform, GitHub Actions, and NixOS
- Upgrade media server memory and increase storage to minimum 40TB (4 x 10TB HDDs)
- Upgrade PVE and PVE1 to 64GB RAM

---

**This setup combines enterprise networking features with self-hosted services, using Proxmox for virtualization and Docker for container management. The flat network simplifies administration while providing essential redundancy through dual Proxmox nodes, managed switches, and automated backups, ensuring reliability, scalability, and ease of management for all critical services.**
