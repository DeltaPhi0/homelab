# Navidrome Music Server Setup  
**Self hosted music streaming made simple**  
*Last updated: May 26, 2025*  
<sub>Tested on Linux. Docker and Docker Compose must be installed.</sub>

## Quick installation  

### 1. Create directories & set permissions  
```bash
sudo mkdir -p /media/navidrome/{music,data}
sudo chmod -R 755 /media/navidrome
sudo chown -R $USER /media/navidrome
```
### 2. Get configuration file
```bash
cd /media/navidrome
wget https://raw.githubusercontent.com/DeltaPhi0/homelab/refs/heads/main/media/navidrome/docker-compose.yaml
sudo chmod 644 docker-compose.yaml
sudo chown $USER:$USER docker-compose.yaml
```
<details>
<summary>See docker-compose.yaml</summary>

```yaml
services:
  navidrome:
    image: deluan/navidrome:latest
    user: 1000:1000 # should be owner of volumes
    ports:
      - "4533:4533"
    restart: unless-stopped
    environment:
      ND_LOGLEVEL: debug
      ND_SCANSCHEDULE: 1h
    volumes:
      - "/media/navidrome/data:/data"
      - "/media/navidrome/music:/music:ro"
```
</details>  

### 3. Start the server
```bash
docker-compose up -d
```

---

## Access your music  
- Open in browser: http://[your ip]:4533
- First user created becomes admin  

---

## Mobile Setup (iOS)
### 1. Install [Substreamer](https://apps.apple.com/us/app/substreamer/id1012991665)
### 2. Connect using 
```
Server: http://[your-ip]:4533
Username: [your-admin-user]
Password: [your-password]
```
---

## Favorites Playlist Setup
### 1. Download this file, it will automatically save your favorite songs in a playlist of its own
```bash
wget https://raw.githubusercontent.com/DeltaPhi0/homelab/refs/heads/main/media/navidrome/favorites.json -O /media/navidrome/music/favorites.json
```
<details>
<summary>See favorites.json</summary>

```nsp
{
    "any": [
        {"is": {"loved": true}}
    ],
    "sort": "title"
}
```
</details>

### 2. Restart server
```bash
docker restart $(docker ps -qf "ancestor=deluan/navidrome")
```

## Enjoy your music!
**Recommended next steps:**
- Go onto the next step in the guide : ) ( [Continue to calibre Setup →](../calibre/INSTALL.md) )

*Note: you can change a file's metadata (e.g. artist, album name, title, etc..) using the command 'id3v2'.Run 'man id3v2' for more information*
