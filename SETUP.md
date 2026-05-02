# Setup Guide

Step-by-step walkthrough of how this NAS was configured from scratch.

---

## 1. Base OS Install

- Downloaded Debian 12 (Bookworm) minimal ISO
- Flashed to USB, installed with no desktop environment
- Enabled SSH during install for headless access

```bash
# Verify SSH is running
sudo systemctl status ssh
```

---

## 2. Storage Configuration

Identified drives, created partitions, formatted, and configured persistent mounting.

```bash
# List drives
lsblk

# Format storage drive
sudo mkfs.ext4 /dev/sdb1

# Get UUID for fstab
sudo blkid /dev/sdb1

# Add to /etc/fstab for persistent mount
UUID=your-uuid /mnt/storage ext4 defaults 0 2

# Mount all
sudo mount -a
```

---

## 3. Docker Install

Jellyfin runs in Docker for clean isolation from the host system.

```bash
# Install Docker
sudo apt update
sudo apt install docker.io docker-compose -y

# Add user to docker group (avoid using sudo every time)
sudo usermod -aG docker $USER

# Verify
docker --version
```

---

## 4. Jellyfin (Docker)

```bash
# Pull and run Jellyfin container
docker run -d \
  --name jellyfin \
  --restart unless-stopped \
  -p 8096:8096 \
  -v /mnt/storage/media:/media \
  -v /home/your-user/jellyfin/config:/config \
  -v /home/your-user/jellyfin/cache:/cache \
  jellyfin/jellyfin
```

```bash
# Check it's running
docker ps

# View logs
docker logs jellyfin
```

Access at `http://[server-ip]:8096` on local network or via Tailscale IP remotely.

---

## 5. Samba (SMB File Sharing)

```bash
sudo apt install samba -y

# Edit config
sudo nano /etc/samba/smb.conf
```

Add at bottom of smb.conf:
```ini
[NAS]
   path = /mnt/storage
   browseable = yes
   read only = no
   valid users = your-username
```

```bash
# Set Samba password
sudo smbpasswd -a your-username

# Enable and start
sudo systemctl enable smbd
sudo systemctl restart smbd
```

**Connecting to the share:**
- Windows: `\\[server-ip]\NAS`
- macOS: `smb://[server-ip]/NAS`

---

## 6. Tailscale

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

Authenticate via the link shown in terminal. The NAS gets a stable Tailscale IP — use this IP for remote access to both SMB and Jellyfin exactly as you would locally.

```bash
# Check status and assigned IP
tailscale status
```

---

## 7. Firewall (UFW)

```bash
sudo apt install ufw -y

sudo ufw allow ssh
sudo ufw allow samba
sudo ufw allow 8096    # Jellyfin
sudo ufw enable

# Verify rules
sudo ufw status
```

---

## Verifying Everything Works

```bash
# All running services
sudo systemctl status smbd
sudo systemctl status tailscaled
docker ps

# Storage mounts
df -h
```
