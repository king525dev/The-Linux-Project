# The Linux Project

Personal infrastructure environment for self‑hosting, learning, and fun.

**Purpose:**
- Self‑host synced note‑taking, file sharing, media streaming, and development environments
- Use only Free and Open Source Software (FOSS)
- Experiment with AI, monitoring, and networking

## Environment Summary

| Item           | Count                    |
| -------------- | ------------------------ |
| Proxmox Nodes  | 2                        |
| VMs            | 1 (Ubuntu Server)        |
| LXC Containers | 7 (+ future additions)   |
| VLANs          | 1 (flat 192.168.50.0/24) |

## Core Technologies

- **Proxmox VE** – hypervisor & cluster management
- **Tailscale** – secure remote access VPN
- **Samba** – file sharing (NAS‑like)
- **Jellyfin** – media streaming
- **Navidrome** – music streaming
- **Joplin Server** – private note sync
- **Homepage** – unified service dashboard
- **Pi‑hole** – network‑wide ad blocking
- **Ollama** – local LLM experimentation
- **Ubuntu Server** – development VM

## Quick Links

| Service | Local URL | Remote (Tailscale) |
|---------|-----------|---------------------|
| Proxmox (Node1) | [https://192.168.50.100:8006](https://192.168.50.100:8006) | `100.86.165.59:8006` |
| Router admin | [https://192.168.50.1/](https://192.168.50.1/) | – |
| Homepage (dashboard) | TBD | TBD |
| Samba share | `//192.168.50.110/ServerData` | – |
| Dev server (Cockpit) | [https://192.168.50.176:9090](https://192.168.50.176:9090) | via Tailscale |
| Tailscale admin | [http://100.86.165.59:5252/](http://100.86.165.59:5252/) | `http://west-cordylus.ts.net/` |

> **Secrets and credentials are never stored in this documentation.**
> Keep them in a local password manager or `.env` files.

## Doc Links

- [[hardware]]
- [[services]]
- [[topology]]
- [[vms-and-containers]]