# Hardware

## Computers

| Hostname | Role                  | Model                   | CPU                                | RAM          | Storage       | IP                       | Full Specifications                                                                                |
| -------- | --------------------- | ----------------------- | ---------------------------------- | ------------ | ------------- | ------------------------ | -------------------------------------------------------------------------------------------------- |
| node-1   | Hypervisor (Node1)    | Dell OptiPlex 7010 USFF | Intel Core i5‑3470T (3rd Gen)      | 8 GB DDR3    | 256 GB SSD    | 192.168.50.100           | [Dell OptiPlex 7010 USFF](https://www.hardware-corner.net/desktop-models/Dell-OptiPlex-7010-USFF/) |
| node-2   | Linux Desktop         | Dell OptiPlex 7010 USFF | Intel Core i5‑3470T (3rd Gen)      | 8 GB DDR3    | 512 GB SSD    | 192.168.50.101 (planned) | [Dell OptiPlex 7010 USFF](https://www.hardware-corner.net/desktop-models/Dell-OptiPlex-7010-USFF/) |
| node-3   | Hypervisor<br>(Node1) | Fujitsu Esprimo P5011   | Intel Core <br>i5-11500 (11th Gen) | 8GB <br>DDR4 | 256 GB<br>SSD | (unplanned)              | [Fujitsu-Esprimo-P5011](https://www.hardware-corner.net/desktop-models/Fujitsu-Esprimo-P5011/)     |
|          |                       |                         |                                    |              |               |                          |                                                                                                    |


## Networking

| Device | Model                                    | Role                             |
| ------ | ---------------------------------------- | -------------------------------- |
| Router | GL.iNet GL‑SFT1200 (Opal)                | Internet gateway, firewall, DHCP |
| Router | N2940 Quad Core Firewall Micro Appliance | (unplanned)                      |

## Storage Layout

- **node-1** uses its local SSD for Proxmox OS, VM/CT images, and a dedicated directory:
  `/mnt/storage` → used for media, shared files, and container bind‑mounts.

## Potential Upgrades

| Component | Upgrade | Benefit |
|-----------|---------|---------|
| RAM | 2×8 GB DDR3‑1600 (max 16 GB) | Run more VMs/CTs and heavier services |
| Storage | 1‑2 TB SATA SSD or USB 3.0 external HDD | More space for media and backups |
| Router | Intel N100 fanless PC (e.g., Protectli) | Advanced firewall, VLANs, VPN endpoint |