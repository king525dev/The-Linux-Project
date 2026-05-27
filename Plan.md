# Home Lab Project - Initial Plan

## Overview

This project describes how I plan to set up a two-node HomeLab using Proxmox VE on two Dell OptiPlex 7010 (Ultra Small Form Factor) systems.  

The goal is to self-host simple services like synced note-taking, file sharing, media streaming, and development environments; all using Free and Open Source Software (FOSS).

---

&nbsp;
## Hardware

### Computers

| Device | CPU | RAM | Storage |
|--------|-----|-----|----------|
| Dell OptiPlex 7010 (USFF) | Intel Core i5 (3rd Gen) | 8 GB | 256 GB SSD |
| Dell OptiPlex 7010 (USFF) | Intel Core i5 (3rd Gen) | 8 GB | 256 GB SSD |

### Other Devices

| Device Name | Device Type | Note |
|--------------------|------------------|-----|
| GL.iNet GL-SFT1200 | Router | Connect Personal Network to Internet |

---

&nbsp;
## Terminology Used

| Term                   | Meaning                                                                                                                                                          |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Proxmox VE (PVE)**   | A Debian-based **hypervisor** OS installed on each server. It manages VMs and containers and provides a single web interface to control all nodes in your setup. |
| **Virtual Machine (VM)** | A full simulation of a computer (CPU, RAM, storage, OS). Resource-heavy because it runs an entire operating system inside Proxmox.                              |
| **Container (CT / LXC)** | A lightweight environment that isolates applications but shares the host kernel. Ideal for running apps efficiently without the overhead of a full OS.        |
| **Hypervisor**         | Software that creates and manages virtual machines and containers—in this case, **Proxmox VE**.                                                                   |
| **Node**               | A physical machine (e.g., each Dell OptiPlex) running Proxmox VE. Each one becomes part of your cluster.                                                          |
| **Proxmox Cluster**    | A group of Proxmox nodes linked together. It provides **one unified management interface** and allows **migration** of VMs/containers between nodes.              |
| **Cluster / Node**     | (Merged into definitions above — see **Node** and **Proxmox Cluster**.)                                                                                          |
| **Shared Storage**     | A storage system accessible by multiple nodes, allowing features like live migration of VMs/containers.                                                           |
| **NAS (Network Attached Storage)** | A central storage device on your network where files (photos, videos, backups, etc.) are stored and accessed by other devices.                       |
| **Self-Hosted**        | Running and managing your own applications or services instead of relying on cloud platforms.                                                                     |
| **FOSS**               | Free and Open Source Software — can be used, modified, and distributed freely.                                                                                    |

---

&nbsp;
## FOSS Stack

| Service Request | FOSS Application | What It Does | How We'll Run It |
| :--- | :--- | :--- | :--- |
| **Hypervisor** | **Proxmox VE** | The base OS for both PCs. Manages all your other services. | Bare Metal (Main OS) |
| **NAS / File Sharing** | **Samba** (in a CT) | The standard protocol for file sharing. This makes your "NAS" visible as a network drive on Windows, macOS, and Linux. | **Debian CT** |
| **Movie/Video Streaming** | **Jellyfin** | A fantastic media server (like a free, self-hosted Netflix). It scans your movies/shows and streams them to your devices. | **Debian CT** |
| **Song Streaming** | **Navidrome** | A lightweight music server. It scans your music, organizes it, and streams to your phone or web browser (Subsonic-compatible). | **Debian CT** |
| **Developer OS** | **Ubuntu Server** | The full Ubuntu operating system you requested for development work. | **VM (Virtual Machine)** |
| **Synced Note-taking** | **Joplin Server** | The backend server for the Joplin note-taking app. This will sync your notes between your phone, laptop, etc. | **Debian CT** |
| **Remote Access** | **Tailscale** | You're already using it! It creates a secure virtual network (a VPN) so you can access your lab from anywhere, *without* router access. | On Proxmox Hosts & in CTs |
| **Remote Access via Internet** | Access services from anywhere | Tailscale + Traefik / Nginx | Safe HTTPS access |
| **AI (Experimental)** | **Ollama** | A tool to run open-source large language models (LLMs) locally on your own hardware. | **Debian CT** |
| **File Conversion** | **Pandoc** | A tool to convert between file formats | **Developer OS** |
| **Photos Backup** | **Immich** | Backs up photos and videos from all devices | **Debian CT** |

---

&nbsp;
##  Additional Recommended Services

| Service                 | Description                          | FOSS Option                                                                 |
|-------------------------|--------------------------------------|------------------------------------------------------------------------------|
| **Git Server**          | Host your code repositories          | [Gitea](https://gitea.io)                                                    |
| **Personal Wiki**       | Keep class notes & documentation     | [HedgeDoc](https://hedgedoc.org)                                             |
| **Code Wiki Generator**       | Create wikis & documentation for repos using AI    | [deepWiki](https://github.com/AsyncFuncAI/deepwiki-open)                                             |
| **Monitoring Dashboard**| Track performance and uptime         | [Grafana](https://grafana.com) + [Prometheus](https://prometheus.io)         |
| **Backup Service**      | Automate backups and snapshots       | Proxmox Backup Server or Rsync                                               |
| **Ad-blocker**   | Network-wide ad and tracker blocker for all devices; extremely lightweight and ideal for CTs. | [Pi-hole](https://pi-hole.net/)       |
| **Password Manager** | Self-hosted, secure, lightweight password manager compatible with Bitwarden clients.        | [Vaultwarden](https://www.vaultwarden.net/)   |
| **HomePage**  | A clean, minimal dashboard that provides quick access to all your self-hosted services.       | HomePage      |
| **MacOS VM**  | Mostly just for fun. Never really experienced MacOS before       | [MacOS Installer](https://github.com/Pyenb/macOS-ISOs)      |
| **Additional File Conversion**  | Another file converter alternative hosted locally       | [ConvertX](https://github.com/C4illin/ConvertX)      |
| **Wiki Creator**  | Ability to create wikis from a homepage       | [DeepWiki](https://github.com/AsyncFuncAI/deepwiki-open)      |
| **Photo backup**  | Back up pictures from all devices      | [Immich](https://immich.app/)      |
| **Home VPN**  | Be able to traverse the internet through a certain IP from any device      | [WireGaurd VPN](https://www.wireguard.com/)      |
| **Document Scanner**  | Scan, Store and Host physical documents by scanning them      | [Paperless NGX](https://docs.paperless-ngx.com/)      |

---
&nbsp;
## The Step-by-Step Implementation Timeline

### **Phase 1: The Foundation (The Cluster)**

1. **Back Up Your Data**
   Before beginning, **back up all important files** from the Ubuntu machine. Both PCs will be wiped during installation.

2. **Install Proxmox VE (Node 1)**

   * Download the Proxmox VE ISO.
   * Flash it to a USB using Balena Etcher.
   * Install it on the first Dell PC.
     This establishes your **first hypervisor node**.

3. **Install Proxmox VE (Node 2)**
   Install Proxmox on the second PC the same way as Node 1.
   This prepares it for clustering.

4. **Create the Cluster**

   * On Node 1:
     `Datacenter → Cluster → Create Cluster`
   * On Node 2:
     Join the cluster using Node 1’s join key.
     Your two machines now form a **Proxmox cluster** with unified control.

5. **Set Up Storage**

   * Use each node’s 256 GB SSD as local storage.
   * On `pve-1`, create a main storage directory such as:

     ```
     /mnt/storage
     ```

   This becomes your media and shared data root.


### **Phase 2: Essential Services**

6. **Remote Access (Tailscale)**
   Install Tailscale **directly on both Proxmox hosts**.
   This gives you secure, encrypted access to the cluster from anywhere.

7. **File Server (Samba CT)**

   * On `pve-1`, create a **Debian CT** with **512 MB RAM**.
   * Bind-mount `/mnt/storage` from the host into the container.
   * Install and configure Samba.
     You now have a simple NAS-like shared folder.

8. **Note Syncing (Joplin Server CT)**

   * On `pve-1`, create another **Debian CT** (512 MB RAM).
   * Install Docker (optional) and deploy Joplin Server.
     Your notes can now sync privately across devices.


### **Phase 3: The “Fun” Services**

9. **Dashboard (Homepage CT)**

   * Create a small **Debian CT** (256 MB RAM).
   * Install Homepage.
     This becomes your unified dashboard for all services.

10. **Music Server (Navidrome CT)**

    * Create a **Debian CT** (512 MB RAM) on `pve-1`.
    * Bind-mount:

      ```
      /mnt/storage/Music
      ```
    * Install Navidrome.
      This becomes your personal Spotify.

11. **Media Server (Jellyfin CT)**

    * Create a **Debian CT** (1 GB RAM).
    * Bind-mount your media directories, e.g.:

      ```
      /mnt/storage/Movies
      ```
    * Install Jellyfin.
      This becomes your personal Netflix.

&nbsp;
## **Phase 4: The “Work” Services**

12. **Developer OS (Ubuntu VM)**

    * On **`pve-2`**, create an Ubuntu Server VM.
    * Recommended: **2 vCPUs, 2 GB RAM, 20 GB disk**.
    * SSH into it for dev work.
      This gives you a proper Linux coding environment.

13. **Ad-Blocking (Pi-hole CT)**

    * On `pve-2`, create a **Debian CT** (512 MB RAM).
    * Install Pi-hole.
      This provides whole-network ad and tracker blocking.


### **Phase 5: The Experiment**

14. **AI Experiment (Ollama CT)**

    * On `pve-2`, create a **Debian CT** (≈2 GB RAM).
    * Install Ollama.

    **Important:**
    3rd-gen i5 is not suited for modern AI workloads.
    Small models *will* run, but responses may take minutes.
    This is purely for experimentation and learning.

&nbsp;
## Potential Hardware Upgrades

1.  **RAM:** The OptiPlex 7010 USFF has 2 slots and officially supports a maximum of 16GB (2x 8GB sticks) of **DDR3-1600** RAM. You can find used DDR3 RAM *very* cheaply on sites like eBay. Upgrading even one node to 16GB would make a *massive* difference.
2.  **Storage:** 256GB is small for a media server. A 1TB or 2TB SATA SSD is a great upgrade. A cheaper alternative is a large USB 3.0 external hard drive. You can plug it into one Proxmox node, mount it, and use that as your main `/mnt/storage` pool.

&nbsp;
## Documentation Links

* **Proxmox VE:** [Installation Guide](https://pve.proxomox.com/wiki/Installation)
* **Proxmox Cluster:** [Cluster Manager Guide](https://pve.proxomox.com/wiki/Cluster_Manager)
* **Jellyfin:** [Documentation](https://jellyfin.org/docs/)
* **Navidrome:** [Documentation](https://www.navidrome.org/docs/)
* **Joplin Server:** [Installation Guide](https://joplinapp.org/help/install/server)
* **Ollama:** [GitHub/Documentation](https://github.com/ollama/ollama)
* **Pi-hole:** [Documentation](https://docs.pi-hole.net/)
* **Vaultwarden:** [Wiki/Guides](https://github.com/dani-garcia/vaultwarden/wiki)
* **Tailscale:** [Knowledge Base](https://tailscale.com/kb/)

&nbsp;

## Useful Local Links
* **Router Local Admin**: [GLi.Net Admin](https://192.168.50.1/)
* **Proxmox Local Admin**: [Proxmox Admin](https://192.168.50.100:8006)
* **Proxmox Remote Admin**: [Proxmox Admin](https://100.86.165.59:8006/)
* **Tailscale Admin Remote Access**: [http://west-cordylus.ts.net/](http://100.100.100.100/)
* **Tailscale Admin Local Access**: [Tailscale Admin](http://100.86.165.59:5252/)
* **Linux Dev Server Local Access**: [Ubuntu Server](https://192.168.50.176:9090/system)
*  **Linux Dev Server Remote Access**: [Ubuntu Server](https://192.168.50.176:9090/system)
*  **Samba Share**: [Samba Server](//192.168.50.110/ServerData)

## Useful Tests/Commands
- Check if domain server and internet connection is working (Can I reach internet using domain name?)
```bash
ping google.com
```

- Check just internet connection (Can I reach internet using IP?)
```bash
ping -c 3 8.8.8.8
```

- Check router LAN IP (Can I talk to my router?)
```bash
ping -c 3 192.168.50.1
```

- Check router default LAN IP
```bash
ping -c 3 192.168.8.1
```


- Start GUI Dashboard
```
btop
```

- see stats
```
fastfetch
```

## Secrets

#### Linux Admin
**Username:** root
**Password:** April.ore525

#### Router Admin
**Password:** April.server525

#### Dev Server
**Username:** king525dev
**Password:** April.server525

## Starter Instructions

6. **Create a VM**

- Click “Create VM”.
- Upload an Ubuntu Server ISO to Proxmox storage.
- Assign 2 vCPUs, 4 GB RAM, 20 GB disk.
- Boot the VM and install Ubuntu.

&nbsp;

7. **Test Networking**

- Inside Ubuntu VM:

```bash
ping google.com
```

If it works, your networking bridge is correct.

&nbsp;

8. **Optional: Install Proxmox Updates**

```bash
sudo apt update \&\& sudo apt full-upgrade -y
```

&nbsp;

## Progress So Far

- --> Reset BIOS on Node-1, removing school OS
- --> Installed Ubuntu on Node-1
- --> Downloaded various Ubuntu Software on Node-1
- --> Access Node-1 Remotely using Tailscale
- --> Deleted Ubuntu and downloaded Proxmox on Node-1
- --> Bought a GL.iNet router and connected the Proxmox Server on Node-1 to the internet
- --> Installed Proxmox
- ---> Installed Light Ubuntu server
- --> Running Samba
- --> Running HomePage
- --> Running  Pihole
-  --> Debating buying an intel n100 fanless router pc as a new router

## Future Implementations

- Use molt bot to create post and updates on a different account about projects and that on Instagram or inbetablog posts

## Questions

- What is your router structure?
- Do i need a protectli router with pfsense?
- I want to host websites and webapps on my project, is that feasible?


