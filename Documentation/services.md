# Services

---

## Jellyfin

**Purpose:** Video streaming server (personal Netflix).  
**Location:** `jellyfin-ct` on `pve-1` (LXC container).  
**Access:** `https://jellyfin.local` (to be configured via Tailscale or local DNS).  
**Storage:** Bind‑mount `/mnt/storage/Movies`, `/mnt/storage/TV` from the Proxmox host.  
**Dependencies:** Samba share or direct file management for media ingestion.  
**Backups:** Nightly config export (container snapshot via Proxmox).

---

## Navidrome

**Purpose:** Lightweight music streaming (Subsonic‑compatible).  
**Location:** `navidrome-ct` on `pve-1`.  
**Access:** `https://music.local` (or via Homepage link).  
**Storage:** Bind‑mount `/mnt/storage/Music`.  
**Dependencies:** –  
**Backups:** Container snapshots; playlists exported periodically.

---

## Samba (File Sharing)

**Purpose:** Network file access (NAS‑like).  
**Location:** `samba-ct` on `pve-1`.  
**Access:** `\\192.168.50.110\ServerData` (SMB).  
**Storage:** Bind‑mount `/mnt/storage` from host.  
**Dependencies:** –  
**Backups:** Important folders backed up to external drive (manual or rsync).

---

## Joplin Server

**Purpose:** Private sync backend for Joplin notes.  
**Location:** `joplin-ct` on `pve-1`.  
**Access:** `https://joplin.local` (mobile & desktop apps point here).  
**Dependencies:** Reverse proxy (Traefik/Nginx) for HTTPS – planned.  
**Backups:** Daily database dump + container snapshot.

---

## Homepage

**Purpose:** Clean dashboard showing all self‑hosted services with status.  
**Location:** `homepage-ct` on `pve-1`.  
**Access:** Set as browser start page.  
**Dependencies:** Reads service URLs from config.  
**Backups:** Configuration file only.

---

## Pi‑hole

**Purpose:** Network‑wide ad and tracker blocking.  
**Location:** `pihole-ct` on `pve-2`.  
**Access:** Admin panel at `http://192.168.50.115/admin`.  
**Dependencies:** Must be set as primary DNS in router DHCP.  
**Backups:** Teleporter export and container snapshot.

---

## Ollama

**Purpose:** Run open‑source LLMs locally (experimental).  
**Location:** `ollama-ct` on `pve-2`.  
**Access:** CLI or API endpoint (e.g., `http://192.168.50.116:11434`).  
**Dependencies:** –  
**Backups:** Not required; models can be re‑downloaded.

---

## Ubuntu Server (Dev VM)

**Purpose:** Linux development environment.  
**Location:** `dev-vm` on `pve-2`.  
**Access:** SSH + Cockpit at `https://192.168.50.176:9090`.  
**Dependencies:** Tailscale for remote access.  
**Backups:** Proxmox VM backup schedule.

---

## Future Services

- **Immich** – Photo backup, Google Photos alternative.
- **Vaultwarden** – Bitwarden‑compatible password manager.
- **Gitea** – Lightweight Git server.
- **Paperless‑ngx** – Document scanning and OCR.
- **WireGuard** – Traditional VPN server (alternative to Tailscale).
- **ConvertX** – Web‑based file converter.
