# 👨🏻‍💻 Homelab

**Last Updated:** January 25th, 2026  
**Primary Purpose:**  
To provide a secure, self-hosted environment for network management, automation, and media streaming. This homelab leverages Proxmox virtualization, Docker containers, and a flat network architecture to deliver critical services (DNS, ad-blocking, monitoring, automation) and a robust media stack, all managed with strong security (Tailscale VPN), centralized documentation, and automated backups and updates.

---
<a name="homelab"></a>

## 📑 Table of Contents

| [Relevant Repositories](#relevant-repositories) | [Hardware Configuration](#hardware-configuration) | [Tailscale VPN](#secure-connectivity-tailscale-mesh-vpn) | [Network Architecture](#network-architecture) | [Proxmox Services](#proxmox) | [Media Server Stack](#media) | [Backup & Maintenance](#backup--maintenance) | [Docs & Change Mgmt](#documentation) | [Future Plans](#future-plans) |

---

<a name="relevant-repositories"></a>

## 📦 Relevant Repositories

[⬆️ Return to Top](#homelab)

- [media](https://github.com/jcnichols22/media): Docker Compose files and configuration for the media server stack.
- [dotfiles](https://github.com/jcnichols22/dotfiles): Personal dotfiles for shell, editor, and system configuration.
- [Ansible](https://github.com/jcnichols22/Ansible): Main Ansible playbooks for automating homelab provisioning and updates.
- [ansible_scripts](https://github.com/jcnichols22/ansible_scripts): Additional Ansible scripts and roles for specialized automation tasks.

<a name="hardware-configuration"></a>

## 🔧 Hardware Configuration

[⬆️ Return to Top](#homelab)


### Proxmox Nodes

- **Platform:** HP ProDesk 600 G4 DM (TAA) Mini
- **OS:** Debian GNU/Linux 13 (trixie) with Proxmox VE 9.2.x
- **Kernel:** Linux 7.0.2-4-pve
- **CPU:** Intel Core i5-8500T @ 2.10GHz (6 cores)
- **RAM:** 31GB DDR4
- **Storage:** ~500GB SSD (per node)
- **Datacenter Name:** Diagon Alley
- **Nodes:**
  - **pve1:** General productivity, media, and monitoring services
  - **pve2:** Network, automation, utility, and management services

### Proxmox Backup Server

- **Hostname:** Gringotts
- **VMID:** 102
- **Platform:** Running on pve1
- **OS:** Proxmox Backup Server
- **Role:** Centralized backup for all Proxmox nodes and containers

### Media Server

- **Hostname:** thepensive
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

- **Router:** TheVeil / TP-Link ER-605 v2.20 (Omada managed)
- **Switch:** Crossroads / TP-Link TL-SG3428 v2.30 (Omada managed)
- **Access Point:** SouthGate / TP-Link EAP660 HD v1.0 (Omada managed)
- **ISP:** AT&T Modem in bridge mode


#### Wireless SSIDs

The following SSIDs are exposed by the Omada controller for site **Velaris**. Policy details (VLAN, security, band) are omitted until verified.

| SSID Name         |
|-------------------|
| Youre a WiFi Harry|
| ChosenWAN         |
| TheLeakyCauldron  |
| Azkaban           |
| mgmt              |

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
TheVeil (Router / ER-605 v2)
|
Crossroads (Switch / SG3428 v2)
|
SouthGate (EAP660 HD)

_Velaris uses Omada-managed segmentation with defined SSID-to-VLAN bindings; policy details are listed separately._

### VLANs

| VLAN ID | Name         | Subnet            | Purpose/Notes                                 |
|---------|--------------|-------------------|-----------------------------------------------|
| 1       | DEFAULT      | 192.168.0.0/24    | Core infrastructure and management plane       |
| 10      | MAIN         | 192.168.10.0/24  | User and general-purpose devices               |
| 20      | KIDS         | 192.168.20.0/24  | Kids' devices/network                          |
| 30      | IOT          | 192.168.30.0/24  | IoT devices                                    |
| 40      | GUEST        | 192.168.40.0/24  | Guest network                                  |

- **DNS/Adblocking:** AdGuard Home (LXC 118)
- **DHCP:** TheVeil handling leases
- **Inter-VLAN routing:** restricted by firewall rules; only required cross-VLAN flows are permitted for management and monitoring

<a name="proxmox"></a>


## 🖥️ Proxmox Services (LXC Containers & VM)

[⬆️ Return to Top](#homelab)


### pve1 Node

| CT/VM ID | Service | Functionality/Notes |
|----------|---------|---------------------|
| 100 | exitnode | VPN exit node |
| 102 | proxmox-backup-server | PBS / Gringotts |
| 103 | nextcloudpi | File sharing & collaboration |
| 106 | rustdeskserver | Remote access hub |
| 107 | nginxproxymanager | Reverse proxy management |
| 108 | TheOracle-omada | Omada controller |
| 112 | termix | File conversion |
| 113 | Ansible | Automation/orchestration |
| 114 | pegaprox | -- |
| 118 | adguard | DNS/ad-blocking |
| 119 | ntfy | Notifications |
| 126 | uptimekuma | Uptime monitoring |
| 127 | homelable | -- |
| 129 | wazuh | Security monitoring |

### pve2 Node

| CT/VM ID | Service | Functionality/Notes |
|----------|---------|---------------------|
| 104 | paperless-gpt | Document management |
| 105 | paperless-ngx | Document management |
| 109 | paperless-ai | Document management |
| 110 | syncthing | File sync |
| 111 | reactive-resume | Resume management |
| 115 | auto-identity-remove | Cleanup/automation |
| 116 | openwebui | LLM/web UI |
| 117 | stirling-pdf | PDF management |
| 120 | searxng | Search engine |
| 121 | homarr | Service dashboard |
| 122 | rackula | -- |
| 123 | jellyfin | Media streaming |
| 124 | plex | Media streaming |
| 128 | netbox | Network documentation |

#### Proxmox Backup Server

A dedicated Proxmox Backup Server has been added to the infrastructure for centralized, reliable backups of all nodes and containers.

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

- Remove stale peer/PBS placeholders and replace with actual host names
- Reconcile Homelab README against real Omada-derived inventory (add SouthGate/hostnames)
- Remove stale reference to `TheDoorsOfMoria`; verify/update actual guest SSID
- Expand automation with Ansible, Terraform, GitHub Actions, and NixOS
- Upgrade media server memory and increase storage to minimum 40TB (4 x 10TB HDDs)
- Upgrade nodes to 64GB RAM

---

**This setup combines enterprise networking features with self-hosted services, using Proxmox for virtualization and Docker for container management. ReadyWards VLAN and SSID segmentation, managed switching, and central Omada control reduce operational overhead, while Proxmox and Ansible automation keep the environment reproducible and scalable.**
