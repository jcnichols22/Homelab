# Homelab README

**Last Updated:** May 31, 2025  
**Primary Purpose:** Network management, self-hosted services, and media streaming

---

## 🔧 Hardware Configuration

### Core Devices

- **PVE**

  - HP ProDesk G3 Mini
  - **Specs:** i5-6500T | 32GB RAM | 512GB SSD
  - **Role:** Hosts different non critical services for productivity and monitoring in LXC containers

- **PVE1**

  - HP ProDesk G3 Mini
  - **Specs:** i5-6500T | 16GB RAM | 512GB SSD
  - **Role:** Hosts critical network services in LXC containers

- **Media Server**
  - Ubuntu Server (HP ProDesk G3 SFF)
  - **Specs:** i5-6500 | 16GB RAM | 1TB Storage
  - **Role:** Media stack via Docker containers

### Networking

- **Router:** TP-Link ER-605 (Omada managed)
- **Access Points:** 2× Eero Pro 6 (Bridge mode)
- **ISP:** AT&T Modem in bridge mode

---

## 🔒 Secure Connectivity: Tailscale Mesh VPN

All core devices, services, and management interfaces are securely connected via Tailscale, creating a private WireGuard-based mesh network overlay across the entire homelab infrastructure.
This enables:

- Secure remote access to all services from anywhere, without exposing ports to the public internet.

- Seamless inter-device communication across physical locations and networks.

- Simple device onboarding and access control via Tailscale ACLs.

---

## 🌐 Network Architecture

ISP Modem (Bridge Mode)
|
TP-Link ER-605
|
Eero Routers for wireless AP's (Bridge)

- **Subnet:** 192.168.1.0/24 (Flat network)
- **DNS/Adblocking:** AdGuard (CT100)
- **DHCP:** ER-605 handling leases

---

## 🖥️ Proxmox Services (LXC Containers & VM)

| CT/VM ID   | Service             | Functionality                        |
| ---------- | ------------------- | ------------------------------------ |
| `100`      | AdGuard Home        | Primary DNS/DHCP                     |
| `101` (VM) | Home Assistant      | Smart home automation                |
| `102`      | Homarr              | Service dashboard                    |
| `103`      | Omada Controller    | Router management                    |
| `105`      | MySpeed             | Network speed tests                  |
| `106`      | RustDesk Server     | Remote access hub                    |
| `107`      | Uptime Kuma         | Service monitoring                   |
| `108`      | Nextcloud           | File sharing & collaboration         |
| `109`      | NetBox              | Network documentation                |
| `110`      | phpIPAM             | IP address management                |
| `111`      | Reactive Resume     | Resume management                    |
| `112`      | Stirling PDF        | PDF management                       |
| `114`      | Prometheus          | Monitoring and alerting              |
| `115`      | Grafana             | Visualization dashboard              |
| `117`      | Prometheus Exporter | Exports server metrics to Prometheus |

---

## 🎥 Media Server Stack

**Docker Compose Services:**

**version: 3.8**

# Movie Management

**Radarr:** <br>
**image:** linuxserver/radarr

# TV-Show management

**Sonarr:** <br>
**image:** linuxserver/sonarr
image: linuxserver/sonarr

# Music

**Lidarr:** <br>
**image:** linuxserver/lidarr

# Books

**Readarr:** <br>
**image:** linuxserver/readarr

# Indexer Aggregator

**Prowlarr:** <br>
**image:** linuxserver/prowlarr

# Media streaming

**Jellyfin:** <br>
**image:** jellyfin/jellyfin

**Plex:** <br>
**image:** linuxserver/plex

**Data Flow:**
Content Requests → \*Arr Apps → Download Clients → Media Library → Jellyfin/Plex

## 🔄 Backup & Maintenance

- **Proxmox Backups**

  - Daily snapshots of critical containers
  - VM backups to external NAS weekly

- **Media Server Protections**

  - With only 1 TB of media Currently no backups for Media

<!-- - **Network Resilience** -->

- **Update Schedule:**

  - Every 2 weeks on Sunday at midnight cron job updates nodes, vm's, containers

- **Monitoring:**

  - Uptime Kuma checks all services every 5 minutes
  - Prometheus collects metrics from all containers
  - Grafana dashboards for visualization

- **Documentation:**
  - Network changes logged in NetBox
  - IP assignments tracked in phpIPAM
  - Change log via Github

## 🚀 Future Plans

- Implement VLAN segmentation
- Unraid for mass storage
- Transition to Ubiquiti (Router, POE Switch, 2 AP)
- Offsite Backup
- Remove as many paid cloud services as possible
- Learning Ansible for automation and easy deployment

---

**This setup combines enterprise networking features with self-hosted services, using Proxmox for virtualization and Docker for container management. The flat network simplifies administration while providing essential redundancy through dual DNS and AP configurations.**

---
