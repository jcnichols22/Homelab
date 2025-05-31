# Homelab README

**Last Updated:** May 31, 2025  
**Primary Purpose:** Network management, self-hosted services, and media streaming

---

## 🔧 Hardware Configuration

### Core Devices

- **Proxmox Host**

  - HP ProDesk G3 Mini
  - **Specs:** i5-6500T | 32GB RAM | 512GB SSD
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

Note: All IPs and service URLs listed below are accessible only within the Tailscale network.

---

## 🌐 Network Architecture

ISP Modem (Bridge Mode)
|
TP-Link ER-605
/ |
Proxmox Media Eero APs
Host Server (Bridge)

- **Subnet:** 192.168.1.0/24 (Flat network)
- **DNS:** AdGuard (CT100) ↔ Pi-hole (CT104) redundancy
- **DHCP:** ER-605 handling leases

---

## 🖥️ Proxmox Services (LXC Containers & VM)

| CT/VM ID   | Service          | IP Address           | Functionality                |
| ---------- | ---------------- | -------------------- | ---------------------------- |
| `100`      | AdGuard Home     | <!--192.168.1.100--> | Primary DNS/DHCP             |
| `101` (VM) | Home Assistant   | <!--192.168.1.101--> | Smart home automation        |
| `102`      | Homarr           | <!--192.168.1.102--> | Service dashboard            |
| `103`      | Omada Controller | <!--192.168.1.103--> | Router management            |
| `104`      | Pi-hole          | <!--192.168.1.104--> | Secondary DNS/ad blocking    |
| `105`      | MySpeed          | <!--192.168.1.105--> | Network speed tests          |
| `106`      | RustDesk Server  | <!--192.168.1.106--> | Remote access hub            |
| `107`      | Uptime Kuma      | <!--192.168.1.107--> | Service monitoring           |
| `108`      | Nextcloud        | <!--192.168.1.108--> | File sharing & collaboration |
| `109`      | NetBox           | <!--192.168.1.109--> | Network documentation        |
| `110`      | phpIPAM          | <!--192.168.1.110--> | IP address management        |

---

## 🎥 Media Server Stack

**Docker Compose Services:**

version: '3.8'
services:

# Movie Management

radarr:
image: linuxserver/radarr

# TV-Show management

sonarr:
image: linuxserver/sonarr

# Music

lidarr:
image: linuxserver/lidarr

# Books

readarr:
image: linuxserver/readarr

# Indexer Aggregator

prowlarr:
image: linuxserver/prowlarr

# Media streaming

jellyfin:
image: jellyfin/jellyfin

plex:
image: linuxserver/plex

**Data Flow:**
Content Requests → \*Arr Apps → Download Clients → Media Library → Jellyfin/Plex

---

## 🔄 Backup & Maintenance

- **Proxmox Backups**

  - Daily snapshots of critical containers
  - VM backups to external NAS weekly

- **Media Server Protections**

  - With only 1 TB of media Currently no backups for Media

- **Network Resilience**

  - Dual DNS (AdGuard + Pi-hole)
  - AP redundancy with Eero mesh

- **Update Schedule:**

  - Every 2nd Sunday monthly maintenance window

- **Monitoring:**

  - Uptime Kuma checks all services every 5 minutes

- **Documentation:**
  - Network changes logged in NetBox
  - IP assignments tracked in phpIPAM
  - Change log via Github

---

<!-- ## 🔗 Access URLs

| Service          | URL                     |
| ---------------- | ----------------------- |
| Omada Controller | https://omada.lab.local |
| Homarr Dashboard | http://homarr.lab.local |
| Nextcloud        | https://cloud.lab.local |
| Jellyfin         | http://jelly.lab.local  |

--- -->

## 🚀 Future Plans

- Implement VLAN segmentation
- Add redundant Proxmox node to separate network related LXC's
- Unraid for mass storage
- Transition to Ubiquiti (Router, POE Switch, 2 AP)
- Offsite Backup
- Remove as many paid cloud services as possible
- Learning Ansible for automation and easy deployment

---

**This setup combines enterprise networking features with self-hosted services, using Proxmox for virtualization and Docker for container management. The flat network simplifies administration while providing essential redundancy through dual DNS and AP configurations.**

---
