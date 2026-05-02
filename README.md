# Home NAS Server

Built this to stop relying on cloud storage and actually own my data. Runs on repurposed Lenovo hardware with Debian, handling local file sharing, media streaming, and remote access from anywhere via VPN. Jellyfin runs in Docker for clean service isolation. Ongoing project — adding services as I go.

---

## Hardware

| Component | Spec |
|-----------|------|
| Machine | Lenovo ThinkCentre M83 (10A8S1MA00) |
| CPU | Intel Core i5-4590 @ 3.30GHz |
| RAM | 12GB DDR3 |
| OS Drive | 120GB SSD |
| Storage | 500GB HDD |
| Network | Gigabit Ethernet |

---

## Stack

| Service | Deployment | Purpose |
|---------|-----------|---------|
| Debian 12 | Bare metal | Base OS |
| Jellyfin | Docker | Media server (movies, TV, music) |
| Portainer | Docker | Docker management UI |
| Tailscale | Bare metal | VPN — remote access without port forwarding |
| Samba (SMB) | Bare metal | Network file sharing |

---

## How It Works

The server sits on the local network and is accessible over SMB for file sharing and HTTP for Jellyfin. Jellyfin runs as a Docker container with storage volumes mounted from the NAS drives. Remote access is handled entirely through Tailscale — no open ports, no dynamic DNS. From anywhere, the NAS appears as if it's on the same local network.

```
Local Access:
[PC / Phone] ──── LAN ──── [NAS]
                             |
                        [Docker: Jellyfin]
                        [Docker: Portainer]
                        [Samba shares]

Remote Access:
[Any Device] ──── Tailscale VPN ──── [NAS]
```

---

## OS & System Configuration

Services run as systemd units or Docker containers depending on the service. Storage drives are mounted via `/etc/fstab`. User and group-based access control is configured on shared directories.

Key areas covered:
- Docker containerization and volume management
- systemd service management
- Disk partitioning and persistent mounting
- Linux user/group permissions and ACLs
- SSH hardening and key-based authentication
- UFW firewall rules

---

## Screenshots

| | |
|---|---|
| ![Jellyfin Dashboard](https://github.com/user-attachments/assets/cb4dc76c-cf76-4b42-9ac6-44b48db6b034) | ![Tailscale](https://github.com/user-attachments/assets/e3180f75-457c-477d-9bf2-9c8a0acefdf6) |
| Jellyfin running via Docker | Remote access via Tailscale |
| ![SMB Share](https://github.com/user-attachments/assets/1fe94453-9b40-4077-bae5-f47e29e1c9c8) | ![Docker Status](https://github.com/user-attachments/assets/b794b9c1-1445-4507-9ed1-cbad15fb1c7d) |
| SMB mounted as network drive | Docker containers running |

---

## Setup Guide

See [SETUP.md](./SETUP.md) for full installation and configuration walkthrough.

---

## Roadmap

- [x] Debian base install and SSH access
- [x] Samba file sharing
- [x] Jellyfin media server (Dockerized)
- [x] Portainer — Docker management UI
- [x] Tailscale remote access
- [ ] Nextcloud — personal cloud storage and file sync
- [ ] Reverse proxy (Nginx/Caddy) for cleaner service routing
- [ ] Automated backups with rsync and cron jobs
- [ ] Uptime Kuma — service health monitoring
- [ ] Homarr — unified dashboard
- [ ] RAID setup for redundancy

---

## What I Learned

Setting this up taught me more practical Linux than two semesters of coursework — specifically around Docker, systemd, networking, and how file permissions actually work at the OS level rather than in theory.

---

`debian` `linux` `docker` `self-hosted` `homelab` `nas` `jellyfin` `tailscale` `samba` `portainer` `networking` `systemd`
