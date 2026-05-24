# VMs & Containers

## Virtual Machines

| Name | Purpose | vCPU | RAM | Disk | IP | Host |
|------|---------|------|-----|------|----|------|
| dev-vm | Ubuntu Server for development | 2 | 2 GB | 20 GB | 192.168.50.176 | pve-2 |
| macOS-vm (future) | Explore macOS experience | 2 | 4 GB | 40 GB | TBD | pve-2 |

## LXC Containers

| Name | Purpose | RAM | Disk | IP | Host | Notes |
|------|---------|-----|------|----|------|-------|
| samba-ct | Samba file server (NAS) | 512 MB | 8 GB | 192.168.50.110 | pve-1 | Bind‑mount `/mnt/storage` |
| joplin-ct | Joplin Server for note sync | 512 MB | 8 GB | 192.168.50.111 (planned) | pve-1 | Docker optional |
| homepage-ct | Unified service dashboard | 256 MB | 4 GB | 192.168.50.112 (planned) | pve-1 | Homepage app |
| navidrome-ct | Music streaming (Subsonic) | 512 MB | 8 GB | 192.168.50.113 (planned) | pve-1 | Bind‑mount `/mnt/storage/Music` |
| jellyfin-ct | Video streaming | 1 GB | 12 GB | 192.168.50.114 (planned) | pve-1 | Bind‑mount `/mnt/storage/Movies`, etc. |
| pihole-ct | Network‑wide ad blocking | 512 MB | 8 GB | 192.168.50.115 (planned) | pve-2 | Set as primary DNS |
| ollama-ct | Local LLM experimentation | 2 GB | 16 GB | 192.168.50.116 (planned) | pve-2 | Expect slow inference |

## Future Additions

- **Immich CT** – photo backup
- **Vaultwarden CT** – password manager
- **WireGuard CT** – alternative VPN endpoint
- **Gitea CT** – self‑hosted Git
- **Paperless‑ngx CT** – document scanning & archiving
- **ConvertX CT** – file conversion service

Update this table as you deploy new workloads.