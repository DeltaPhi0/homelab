# 🐳 Docker Setup Guide  
**Containerization made easy**  
**Last updated**: May 26 2025  
![Docker](https://img.shields.io/badge/Docker-Supported-blue)
![Debian](https://img.shields.io/badge/Debian-Compatible-red)

<sub>This guide is intended for debian. For a more complete guide, check the official docker installation guide.</sub>

---

**Official Docker Install Guide**:  
https://docs.docker.com/engine/install/

---

## Table of Contents

- [Manual Installation](#-manual-installation)
- [Security Recommendations](#-security-recommendations)
- [Troubleshooting](#-troubleshooting)
- [Recommended Next Steps](#recommended-next-steps)


## Manual Installation  

### 1. Remove conflicting packages 
```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do
sudo apt-get remove $pkg -y
done
```
### 2. Setup Docker repositories
```bash
# Install prerequisites
sudo apt update
sudo apt install ca-certificates curl -y

# Add Docker's GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker's repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
  https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  sudo apt update
```
### 3. Install Docker 
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```
### 4. Verify installation
```bash
sudo docker run hello-world
```
*If you see the "Hello from Docker!" message, it installed correctly*
### 5. Run Docker without sudo
```bash
# Create docker group (you probably already have it, but just to be sure)
sudo groupadd docker

# Add your user to the group
sudo usermod -aG docker $USER

# Activate changes
newgrp docker

# Verify no sudo access
docker run hello-world
```
### 6. Install Docker compose
<sub>Visit https://github.com/docker/compose/releases to check for the latest version</sub>
```bash
# Download binary
curl -SL https://github.com/docker/compose/releases/download/v2.36.2/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

# Set permissions
sudo chmod +x /usr/local/bin/docker-compose

# Verify, you should see 'Docker Compose version v2.36.2'
docker-compose --version
```
### 7. Enable Docker on boot
```bash
sudo systemctl enable docker
```

---

## Security recommendations
**Avoid running containers with the --privileged flag unless necessary**

---

## Troubleshooting
 **Permission denied?**
- Log out and back in (or reboot) after adding your user to the docker group.
- Run groups to confirm you belong to the docker group.

---

## Recommended next steps:
- Create a docker-compose.yml file and launch it. If you don't know how to do it, follow the guide to the next steps as I will guide you.
- Go onto the next step in the guide : ) ( [Continue to Navidrome Setup →](../navidrome/INSTALL.md) )

*Pro Tip: Use docker ps to list all the running containers and docker ps -a to list all containers*
