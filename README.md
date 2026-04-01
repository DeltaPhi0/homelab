
# Raspberry Pi Headless Server Setup Guide  
*Last Updated: May 20, 2025*  
![Raspberry Pi](https://img.shields.io/badge/-Raspberry%20Pi%203-CC3542?logo=raspberrypi&logoColor=white)

## Table of Contents  
[Why This Matters](#-why-this-matters)  

[Hardware Specifications](#-hardware-specifications)  

[Initial Setup](#%EF%B8%8F-initial-setup)  

[Security & SSH](#-ssh-security-setup)  
   
   - [VPN Setup](#-vpn-configuration)  
   
[Media Server](#-media-server)  

[Backup & Recovery](#-backup--recovery)  

[Important Security Notes](#-important-security-notes)  

[Pro Tips](#-pro-tips)

## Why this matters  
Turn a **€40 Raspberry Pi** into a **secure homelab server** that:  
- Self-hosts services (VPN/media/files) *without cloud fees*  
- has solid security (SSH keys, VPN+local access only)  
- Teaches real sysadmin skills 

## Hardware specifications
- **Device**: Raspberry Pi 3 Model B (1GB RAM)
- **Storage**: 64GB SD Card
- **Connection**: USB-to-Micro-USB power + Ethernet

## Initial setup

### 1. OS installation
1. Use [Raspberry Pi Imager](https://www.raspberrypi.com/software/)
2. **Choose Raspberry Pi OS Lite (64-bit) - Debian Bookworm based** version
3. Write to SD card

*Note: Failed Ubuntu Server installation due to HDMI issues led to this choice*

### 2. Network configuration
```bash
# Find your Pi's IP (after first boot)
nmap -sn 192.168.1.1/24
```
### 3. SSH into server
**Ready to access your server for the first time?**
```bash
ssh username@pi-ip
# Test connectivity
ping google.com
```
### 4. Router settings
1. Set static IP via `nmtui`:
```bash
sudo nmtui
```
   - Select "Edit a connection"
   - Configure manual IP (e.g., `192.168.0.100`)  
     *it's important to do first, if you mix up steps you will not be able to use your pi since it will be cut off from the network*
2. Access router at `192.168.1.1` (may vary)
3. Change subnet to e.g. `192.168.0.1/24` (be sure to not choose a conflicting subnet)


## SSH security setup

### 1. Hostname resolution
```bash
sudo nano /etc/hosts
```
Add line:  
`192.168.0.100 your-hostname`
### 2. SSH key authentication
**Client machine:**
```bash
ssh-keygen -t rsa -b 2048
ssh-copy-id username@hostname
```
**Server security:**
```bash
sudo nano /etc/ssh/sshd_config
```
Uncomment and modify:  
`#PasswordAuthentication yes` → to → `PasswordAuthentication no`

## VPN Configuration

### Why Set Up a VPN?

1. **Lock Down Your Network**
   - Adds an extra security layer to keep unwanted visitors out of your homelab
2. **Access Your Lab Securely from Anywhere**
   - Safely connect to your servers, files, or services remotely without exposing them to the open internet
3. **A Practical Tech Flex**
   - It’s not just for show, self-hosting a VPN demonstrates real world networking and security skills!

### Prerequisites
1. **Static DNS setup** (I used [DuckDNS](https://www.duckdns.org/))
2. **Port forwarding #1(OpenVPN)** in router settings:
   - Protocol: UDP
   - WAN Port: 1194
   - LAN Host: [Your Pi's local IP]
   - LAN Port: 1194
3. **Port forwarding #2(WireGuard)** in router settings:
   - Protocol: UDP
   - WAN Port: 51820
   - LAN Host: [Your Pi's local IP]
   - LAN Port: 51820

### WireGuard setup (Mobile access)
```bash
# Install PiVPN
curl -L https://install.pivpn.io | bash

# Configure PiVPN (choose WireGuard and DDNS domain)
sudo pivpn add
sudo pivpn -qr
```

1. Scan QR code with WireGuard mobile app
2. Test connection:
   ```bash
   pivpn -c  # Monitor connections
   ```

### OpenVPN setup (Main machine)
**Server:**
```bash
wget https://raw.githubusercontent.com/angristan/openvpn-install/master/openvpn-install.sh
chmod +x openvpn-install.sh
AUTO_INSTALL=y ./openvpn-install.sh

# Fix TUN if needed:
sudo mkdir -p /dev/net
sudo mknod /dev/net/tun c 10 200
sudo chmod 666 /dev/net/tun
```

**Client:**
```bash
scp user@hostname:path/to/client.ovpn ~/
sudo apt install network-manager-openvpn
```
1. Import `client.ovpn` in Network Manager
2. Connect via GUI interface  
[If you need additional help](https://www.youtube.com/watch?v=CBJMl9MILbg&t=560s)

## Media Server

**This section in about my self hosted media and development environment, powered by Docker and a mix of awesome tools. Below are the key components, along with links to my tutorials for setting them up!**  

---

### Media & Entertainment

- **Jellyfin** – Personal media streaming for movies and TV shows  
- **Navidrome** (`deluan/navidrome`) – Lightweight music server for streaming across devices  
- **Podgrab** (`akhilrex/podgrab`) – Podcast downloader for offline listening  

---

### Reading & Productivity

- **Calibre** – eBook management and server for my digital library  

---

### Development & Learning

- **NGINX** – Local web development/deployment and reverse proxying  
- **Dockerized Kali Linux** – Isolated security testing environment *(I avoid running Kali as my main OS. Sticking with Linux Mint/Debian for stability!)*  

---

### Experimented With (But Retired)

- **SambaShare** – Retired after switching to direct file access, but great for multi user cloud setups  
- **Home Assistant + Portainer** – Briefly used for smart home automation *(successfully turned on a smart lightbulb and made people freak out by turning off my smart TV!)*  

---

### Why Docker?

> **I use Docker for isolation, scalability, and easy management. Check out my full Docker setup guide to replicate this stack!**

---

### Media installation guide

**For step-by-step guides to recreate this setup:**  

- [Jellyfin](media/jellyfin/INSTALL.md) Server setup  
- [Samba](media/samba/INSTALL.md) Multi user shares  
- [Docker](media/docker/NOTES.md) Basics  
- [Navidrome](media/navidrome/INSTALL.md) Music streaming  
- [Calibre](media/calibre/INSTALL.md) eBook server  

*Note: Please follow these steps from top to bottom. I arranged them from easiest to hardest*

## Backup & recovery
1. Before backing up, be sure to shrink partition to minimum possible using either gparted, parted or fdisk
   - e.g. If you have 12.5GB storage, be sure to round it up to the next whole number (in this case, 13GB)
2. Use **Clonezilla** for system backups, very intuitive and user friendly
3. Update fstab after restoration if needed (personally, that has always been my case)
4. Store backups on external drive. ALWAYS remember about the [3-2-1](https://www.veeam.com/blog/321-backup-rule.html) rule. <br>
[For visual learners!](https://www.youtube.com/watch?v=yQ9NpWZ74BU&t=349s)

## Important security notes
- Never share your private SSH key
- Regularly update packages:
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```
- Use fail2ban for intrusion prevention
- **Warning:** Only share VPN access with trusted users!!
- (e.g. I personally share it with my brother and a friend of mine, so they can access to my media too.)


## Pro tips
- `man [command]` is your best friend
- Use `grep` for quick searches:  
  ```bash
  command | grep -i error
  ```
- Always verify commands from untrusted sources
- Keep a setup journal for future reference

---
  
**Special Thanks to Leonard for the hardware gift!**  
