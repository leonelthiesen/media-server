# Media Server

A complete media server setup using Docker Compose with automated downloading, organization, and streaming capabilities.

## Services Included

- **qBittorrent** (Port 8080) - BitTorrent client for downloading content
- **Jackett** (Port 9117) - Indexer proxy/translation for torrent trackers
- **FlareSolverr** (Port 8191) - Cloudflare bypass proxy for Jackett
- **Radarr** (Port 7878) - Movie collection manager
- **Sonarr** (Port 8989) - TV show collection manager
- **Bazarr** (Port 6767) - Subtitle management for movies and TV shows
- **Plex** (Port 32400) - Media server for streaming your content

## Quick Start

### Prerequisites

- Docker
- Docker Compose
- At least 10GB of free disk space

### Installation

1. Clone this repository:
```bash
git clone https://github.com/leonelthiesen/media-server.git
cd media-server
```

2. Start all services:
```bash
docker-compose up -d
```

3. Access the services at:
   - qBittorrent: http://localhost:8080
     - Default credentials: admin / adminadmin
   - Jackett: http://localhost:9117
   - Radarr: http://localhost:7878
   - Sonarr: http://localhost:8989
   - Bazarr: http://localhost:6767
   - Plex: http://localhost:32400/web

## Configuration

### Environment Variables

The services use the following default environment variables:
- `PUID=1000` - User ID for file permissions
- `PGID=1000` - Group ID for file permissions
- `TZ=Etc/UTC` - Timezone (change to your local timezone, e.g., `America/New_York`)

To customize these values, edit the `docker-compose.yml` file.

### Volume Mappings

The configuration creates the following directory structure:
```
./config/          - Configuration files for all services
./downloads/       - Download directory for qBittorrent
./media/movies/    - Movies library for Plex
./media/tv/        - TV shows library for Plex
```

These directories will be created automatically when you start the services.

## Setup Guide

### 1. Configure qBittorrent
1. Access qBittorrent at http://localhost:8080
2. Login with default credentials (admin/adminadmin)
3. Change the default password in Tools → Options → Web UI

### 2. Configure Jackett
1. Access Jackett at http://localhost:9117
2. Add your preferred indexers
3. Copy the API key for use in Radarr and Sonarr

### 3. Configure FlareSolverr
FlareSolverr runs automatically. In Jackett, configure it as:
- FlareSolverr API URL: http://flaresolverr:8191

### 4. Configure Radarr (Movies)
1. Access Radarr at http://localhost:7878
2. Settings → Download Clients → Add qBittorrent
   - Host: qbittorrent
   - Port: 8080
   - Username: admin
   - Password: (your qBittorrent password)
3. Settings → Indexers → Add Torznab (from Jackett)
   - Use the Jackett API URL and key

### 5. Configure Sonarr (TV Shows)
1. Access Sonarr at http://localhost:8989
2. Configure similarly to Radarr

### 6. Configure Bazarr (Subtitles)
1. Access Bazarr at http://localhost:6767
2. Settings → Radarr → Add server
   - Address: http://radarr:7878
   - API Key: (from Radarr)
3. Settings → Sonarr → Add server
   - Address: http://sonarr:8989
   - API Key: (from Sonarr)

### 7. Configure Plex
1. Access Plex at http://localhost:32400/web
2. Sign in or create a Plex account
3. Add libraries:
   - Movies: /movies
   - TV Shows: /tv

## Management

### Stop all services:
```bash
docker-compose down
```

### Update all services:
```bash
docker-compose pull
docker-compose up -d
```

### View logs:
```bash
docker-compose logs -f
```

### Restart a specific service:
```bash
docker-compose restart <service-name>
```

## Troubleshooting

### Permission Issues
If you encounter permission errors, adjust the PUID and PGID values in docker-compose.yml to match your user:
```bash
id -u  # Get your PUID
id -g  # Get your PGID
```

### Port Conflicts
If any ports are already in use, modify the port mappings in docker-compose.yml. Change the left side of the mapping:
```yaml
ports:
  - "9080:8080"  # Use port 9080 instead of 8080
```

### Network Issues
Services communicate using the internal `media-network`. Ensure you use container names (e.g., `qbittorrent`, `jackett`) when configuring inter-service connections.

## Security Notes

- Change all default passwords after first login
- Consider using a reverse proxy (like Nginx Proxy Manager or Traefik) for HTTPS
- Restrict access to your local network or use a VPN
- Keep your services updated regularly

## License

This configuration is provided as-is for personal use.