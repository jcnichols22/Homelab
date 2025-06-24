# 👨🏻‍💻 Homelab

**Last Updated:** June 24th, 2025  
**Primary Purpose:**  
To provide a secure, self-hosted environment for network management, automation, and media streaming. This homelab leverages Proxmox virtualization, Docker containers, and a flat network architecture to deliver critical services (DNS, ad-blocking, monitoring, automation) and a robust media stack, all managed with strong security (Tailscale VPN), centralized documentation, and automated backups and updates.
<br>
<br>
<br>

## 🔧 Hardware Configuration

### Core Devices:

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

- **Platform:** HP ProDesk 600 G3 SFF
- **OS:** Ubuntu 24.04.2 LTS
- **Specs:** i5-8500 | 16GB RAM | 500GB NVME | 1TB Media Storage
- **Role:** Media stack via Docker containers

### Networking

- **Router:** TP-Link ER-605 (Omada managed)
- **Access Points:** 2× Eero Pro 6 (Bridge mode)
- **ISP:** AT&T Modem in bridge mode

<br>
<br>
<br>

## 🔒 Secure Connectivity: Tailscale Mesh VPN

All core devices, services, and management interfaces are securely connected via Tailscale, creating a private WireGuard-based mesh network overlay across the entire homelab infrastructure.
This enables:

- Secure remote access to all services from anywhere, without exposing ports to the public internet.

- Seamless inter-device communication across physical locations and networks.

- Simple device onboarding and access control via Tailscale ACLs.

<br>
<br>
<br>

## 🌐 Network Architecture

ISP Modem (Bridge Mode)
|
TP-Link ER-605
|
Eero Routers for wireless AP's (Bridge)

- **Subnet:** 192.168.1.0/24 (Flat network)
- **DNS/Adblocking:** AdGuard (LXC 100)
- **DHCP:** ER-605 handling leases

<br>
<br>
<br>

## 🖥️ Proxmox Services (LXC Containers & VM)

### PVE Node:

| CT/VM ID   | Service             | Functionality                        |
| ---------- | ------------------- | ------------------------------------ |
| `101` (VM) | Home Assistant      | Smart home automation                |
| `102`      | Homarr              | Service dashboard                    |
| `104`      | Monica              | Personal CRM                         |
| `105`      | MySpeed             | Network speed tests                  |
| `108`      | Nextcloud           | File sharing & collaboration         |
| `109`      | NetBox              | Network documentation                |
| `110`      | phpIPAM             | IP address management                |
| `111`      | Reactive Resume     | Resume management                    |
| `112`      | Stirling PDF        | PDF management                       |
| `114`      | Prometheus          | Monitoring and alerting              |
| `115`      | Grafana             | Visualization dashboard              |
| `117`      | Prometheus Exporter | Exports server metrics to Prometheus |

### PVE1 Node:

| CT/VM ID | Service          | Functionality                |
| -------- | ---------------- | ---------------------------- |
| `100`    | AdGuard Home     | Ad blocking and DNS          |
| `103`    | Omada Controller | Router management            |
| `106`    | RustDesk Server  | Remote access hub            |
| `107`    | Uptime Kuma      | Service monitoring           |
| `113`    | Ansible          | Automation and orchestration |

<br>
<br>
<br>

## 🎥 Media Server Stack

**Docker Compose Services:**

| Service  | Purpose            | Docker Image           |
| -------- | ------------------ | ---------------------- |
| Radarr   | Movie management   | `linuxserver/radarr`   |
| Sonarr   | TV show management | `linuxserver/sonarr`   |
| Lidarr   | Music management   | `linuxserver/lidarr`   |
| Readarr  | Book management    | `linuxserver/readarr`  |
| Prowlarr | Indexer aggregator | `linuxserver/prowlarr` |
| Jellyfin | Media streaming    | `jellyfin/jellyfin`    |
| Plex     | Media streaming    | `linuxserver/plex`     |

**Compose Version:** `3.8`

**Data Flow:**
Content Requests → \*Arr Apps → Download Clients → Media Library → Jellyfin/Plex

<br>
<br>
<br>

## 🔄 Backup & Maintenance

- **Proxmox Backups**

  - Daily snapshots of critical containers
  - VM backups to external NAS weekly

- **Media Server Protections**

  - With only 1 TB of media Currently no backups for Media

- **Update Schedule:**

  - Every 1st Sunday at midnight cron job updates nodes, vm's, containers using Ansible playbooks

- **Automation:**

  - Ansible playbooks for configuration management

- **Monitoring & Alerts:**

  - Prometheus alerts for critical service failures
  - Grafana dashboards for real-time monitoring
  - Uptime Kuma for service availability checks

<br>
<br>
<br>

## 📜 Documentation & Change Management

- **Network Documentation:**
  - NetBox for network inventory and IP management
  - phpIPAM for IP address management
- **Service Documentation:**
  - Homarr dashboard for service status and links
  - Reactive Resume for personal documentation
- **Change Management:**
  - Ansible playbooks for configuration management
  - Version control via GitHub for all scripts and configurations

<br>
<br>
<br>

## 🚀 Future Plans

- Implement VLAN segmentation
- Unraid for mass storage
- Transition to Ubiquiti (Router, POE Switch, 2 AP)
- Offsite Backup
- Remove as many paid cloud services as possible
- GitHub Actions for CI/CD
- Reverse engineer and use Ansible to deploy and configure services to PVE1 and PVE2
- Add more redundancy to the network
- Log management with Loki or Graylog
- Expand automation with Ansible, Terraform, GitHub Actions, and NixOS
- Upgrade media server to 128GB RAM and increase storage to 10TB
- Upgrade PVE and PVE1 to 64GB RAM
  <br>
  <br>
  <br>

---

**This setup combines enterprise networking features with self-hosted services, using Proxmox for virtualization and Docker for container management. The flat network simplifies administration while providing essential redundancy through dual DNS and AP configurations.**

---
