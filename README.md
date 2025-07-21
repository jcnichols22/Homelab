# 👨🏻‍💻 Homelab

**Last Updated:** July 11th, 2025  
**Primary Purpose:**  
To provide a secure, self-hosted environment for network management, automation, and media streaming. This homelab leverages Proxmox virtualization, Docker containers, and a flat network architecture to deliver critical services (DNS, ad-blocking, monitoring, automation) and a robust media stack, all managed with strong security (Tailscale VPN), centralized documentation, and automated backups and updates.

---
<a name="homelab"></a>
## 📑 Table of Contents

| [Relevant Repositories](#relevant-repositories) | [Hardware Configuration](#hardware-configuration) | [Tailscale VPN](#secure-connectivity-tailscale-mesh-vpn) | [Network Architecture](#network-architecture) | [Proxmox Services](#proxmox) | [Media Server Stack](#media) | [Backup & Maintenance](#backup--maintenance) | [Docs & Change Mgmt](#documentation) | [Future Plans](#future-plans) |

---

<br>

<a name="relevant-repositories"></a>
## 📦 Relevant Repositories

[⬆️ Return to Top](#homelab)

- [media](https://github.com/jcnichols22/media): Docker Compose files and configuration for the media server stack.
- [dotfiles](https://github.com/jcnichols22/dotfiles): Personal dotfiles for shell, editor, and system configuration.
- [Ansible](https://github.com/jcnichols22/Ansible): Main Ansible playbooks for automating homelab provisioning and updates.
- [ansible_scripts](https://github.com/jcnichols22/ansible_scripts): Additional Ansible scripts and roles for specialized automation tasks.

<br>

<a name="hardware-configuration"></a>
## 🔧 Hardware Configuration

[⬆️ Return to Top](#homelab)

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
- **Access Points:** 2× Eero Pro 6 (Bridge mode)
- **ISP:** AT&T Modem in bridge mode

<br>

<a name="secure-connectivity-tailscale-mesh-vpn"></a>
## 🔒 Secure Connectivity: Tailscale Mesh VPN

[⬆️ Return to Top](#homelab)

All core devices, services, and management interfaces are securely connected via Tailscale, creating a private WireGuard-based mesh network overlay across the entire homelab infrastructure.
This enables:

- Secure remote access to all services from anywhere, without exposing ports to the public internet.
- Seamless inter-device communication across physical locations and networks.
- Simple device onboarding and access control via Tailscale ACLs.

<br>

<a name="network-architecture"></a>
## 🌐 Network Architecture

[⬆️ Return to Top](#homelab)

ISP Modem (Passthrough)  
|  
TP-Link ER-605 (Router)  
|
Tp-Link TL-SG1016 (Switch) 
| 
Eero Routers for wireless AP's (Bridge)

- **Subnet:** 192.168.1.0/24 (Flat network)
- **DNS/Adblocking:** Pihole (LXC 100)
- **DHCP:** ER-605 handling leases

<br>

<a name="proxmox"></a>
## 🖥️ Proxmox Services (LXC Containers & VM)

[⬆️ Return to Top](#homelab)

### PVE Node:

| CT/VM ID   | Service             | Functionality                        |
| ---------- | ------------------- | ------------------------------------ |
| `101` (VM) | Home Assistant      | Smart home automation                |
| `102`      | Homarr              | Service dashboard                    |
<!-- | `104`      | Monica              | Personal CRM                         | -->
| `105`      | MySpeed             | Network speed tests                  |
| `108`      | Nextcloud           | File sharing & collaboration         |
| `109`      | NetBox              | Network documentation                |
| `110`      | phpIPAM             | IP address management                |
| `111`      | Reactive Resume     | Resume management                    |
| `112`      | Stirling PDF        | PDF management                       |
| `114`      | Prometheus          | Monitoring and alerting              |
| `115`      | Grafana             | Visualization dashboard              |
| `117`      | Prometheus Exporter | Exports server metrics to Prometheus |
| `124`      | ITSM-NG             | IT Service Management                |
### PVE1 Node:

| CT/VM ID | Service          | Functionality                |
| -------- | ---------------- | ---------------------------- |
<!-- | `100`    | AdGuard Home     | Ad blocking and DNS          | -->
| `103`    | Omada Controller | Router management            |
| `104`    | Pihole           | DNS and ad-blocking          |
| `106`    | RustDesk Server  | Remote access hub            |
| `107`    | Uptime Kuma      | Service monitoring           |
| `113`    | Ansible          | Automation and orchestration |
| `116`    | iVentoy          | Network booting and ISO management |
| `120`    | Docker           | Container management running Netbot.xyz        | 
| `122`    | InfluxDB        | Time-series database for metrics |
| `123`    | Semaphore       | CI/CD pipeline management    |
| `125`    |Nginx Proxy Manager | Reverse proxy management      |
| `126`    | Apache Guacamole | Remote desktop gateway       |
| `127`    | Pialert         | Personal alerting system     |

<br>

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

<br>

<a name="backup--maintenance"></a>
## 🔄 Backup & Maintenance

[⬆️ Return to Top](#homelab)

- **Proxmox Backups**

  - Daily snapshots of critical containers
  - VM backups to external NAS weekly

- **Media Server Protections**

  - With only 2 TB of media Currently no backups for Media

- **Update Schedule:**

  - Every 1st Sunday at midnight cron job updates nodes, vm's, containers using Ansible playbooks

- **Automation:**

  - Ansible playbooks for configuration management

- **Monitoring & Alerts:**
  - Prometheus alerts for critical service failures
  - Grafana dashboards for real-time monitoring
  - Uptime Kuma for service availability checks

<br>

<a name="documentation"></a>
## 📜 Documentation & Change Management

[⬆️ Return to Top](homelab)

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

<br>

---

\*\*This setup combines enterprise networking features with self-hosted services, using Proxmox for virtualization and Docker for container management. The flat network simplifies administration while providing essential redundancy through dual
