# Jellyfin Media Server Setup Guide

**Self hosted media made easy**  
*Last updated: May 26, 2025*

---
## Automatic installation
   ### For those who prefer a one-command installation, I've created an automated bash script that handles most of the process
   ### Review the code before running any script you find online!
   ```bash
   wget https://raw.githubusercontent.com/DeltaPhi0/homelab/refs/heads/main/media/jellyfin/jellyfin-setup.sh
   chmod +x jellyfin-setup.sh
   sudo ./jellyfin-setup.sh
   ```
<details>
<summary>See jellyfin-setup.sh</summary>

```bash
echo "What is your machine's username?"
read USR
sudo apt update
curl https://repo.jellyfin.org/install-debuntu.sh | sudo bash
sudo mkdir -p /media/jellyfin/{movies,shows}
sudo chown -R $USR:$USR /media/jellyfin
sudo chmod -R 755 /media/jellyfin
sudo apt upgrade -y
rm jellyfin-setup.sh
echo "Jellyfin installation complete. Visit http://[your-IP]:8096"
```
</details>

*Note : this script works only if you have a Debian based OS*
## Quick installation

### 1. Install Jellyfin server
```bash
curl https://repo.jellyfin.org/install-debuntu.sh | sudo bash
```

---

## First time setup

### 2. Access Web interface
1. Open your browser and navigate to:
```http
http://[server-IP]:8096
```
*Example:*
```http
http://192.168.0.100:8096
```
*alternatively:*
```http
[your-hostname]:8096
```

### 3. Create admin account
- **Required** for security - make this a strong password! (or not)
- This account will have full control over your Jellyfin

---

## Media library configuration

### 4. Prepare media folders
```bash
# Create base directories (adjust paths as needed)
sudo mkdir -p /media/jellyfin/{movies,shows}
sudo chown -R $USER:$USER /media/jellyfin  # Set proper permissions
```

### 5. Link libraries in Jellyfin
1. In the Jellyfin dashboard:
   - Click "Add Media Library" for each content type
   - **Movies** → `/media/jellyfin/movies`
   - **Shows** → `/media/jellyfin/shows`

> Always use absolute paths like `/media/jellyfin/movies`

---

## Post installation setup

### 6. Add your content
1. Copy media files to their respective folders:
   - Movie files → `/media/jellyfin/movies`
   - Show folders → `/media/jellyfin/shows/seriesName/SeasonXX/`
2. Library scan:
   - Dashboard → "Scheduled Tasks" → "Scan All Libraries"

### 7. (Optional) Music configuration
While technically supported, **iPhone users should note**:
```diff
- Music playback stops when screen locks due to iOS background processing shenanigans
+ Consider using Navidrome for music (link to Navidrome setup in main README)
```

---

## Troubleshooting

**Common issues:**
- **Permission Errors**:  
  ```bash
  sudo chmod -R 755 /media/jellyfin
  ```
  
- **Port conflicts**:  
  Verify nothing else is using port 8096:
  ```bash
  sudo ss -tulpn | grep 8096
  ```

- **Library not updating**:  
  Manually trigger scan from Dashboard → "Scan All Libraries"

---

## Enjoy your media!
**Recommended next steps:**
- Go onto the next step in the guide : ) ( [Continue to Samba Setup →](../samba/INSTALL.md) )

*Note: This is part of my larger media server stack - follow the guides in order from top to bottom for easiest setup!*
