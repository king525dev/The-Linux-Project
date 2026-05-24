# Hardware

## Computers

| Hostname | Role               | CPU                           | RAM       | Storage    | IP                       |
| -------- | ------------------ | ----------------------------- | --------- | ---------- | ------------------------ |
| pve-1    | Hypervisor (Node1) | Intel Core i5‑3470T (3rd Gen) | 8 GB DDR3 | 256 GB SSD | 192.168.50.100           |
| pve-2    | Hypervisor (Node2) | Intel Core i5‑3470T (3rd Gen) | 8 GB DDR3 | 256 GB SSD | 192.168.50.101 (planned) |

Both are Dell OptiPlex 7010 Ultra Small Form Factor.

## Networking

| Device | Model | Role |
|--------|-------|------|
| Router | GL.iNet GL‑SFT1200 (Opal) | Internet gateway, firewall, DHCP |

## Storage Layout

- **pve-1** uses its local SSD for Proxmox OS, VM/CT images, and a dedicated directory:
  `/mnt/storage` → used for media, shared files, and container bind‑mounts.

## Potential Upgrades

| Component | Upgrade | Benefit |
|-----------|---------|---------|
| RAM | 2×8 GB DDR3‑1600 (max 16 GB) | Run more VMs/CTs and heavier services |
| Storage | 1‑2 TB SATA SSD or USB 3.0 external HDD | More space for media and backups |
| Router | Intel N100 fanless PC (e.g., Protectli) | Advanced firewall, VLANs, VPN endpoint |