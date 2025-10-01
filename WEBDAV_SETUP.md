# WebDAV Setup Guide for AllDebrid Integration

## Overview
This setup uses Debrid-Link to provide WebDAV access to your AllDebrid content, which can be directly accessed by Jellyfin and other services without FUSE mounting.

## Prerequisites
1. AllDebrid account with API key
2. Docker and Docker Compose installed
3. `.env` file with required variables

## Setup Steps

### 1. Configure Environment Variables
Add to your `.env` file:
```env
ALLDEBRID_API_KEY=your_actual_api_key_here
PUID=1000
PGID=1000
TZ=America/New_York
```

### 2. Start Services
```bash
docker-compose up -d debrid-link
docker-compose up -d jellyfin jellyseerr
docker-compose up -d prowlarr radarr sonarr
```

### 3. Configure Jellyfin for WebDAV

#### Method 1: Add as Network Location (Recommended)
1. Open Jellyfin web interface at `http://localhost:8096`
2. Go to **Dashboard** → **Libraries**
3. Click **Add Media Library**
4. Choose library type (Movies/TV Shows)
5. Click **Add** under Folders
6. Select **Add Network Location**
7. Enter WebDAV details:
   - **URL**: `http://debrid-link:9999/movies` (for movies)
   - **URL**: `http://debrid-link:9999/shows` (for TV shows)
   - No authentication required (internal Docker network)
8. Save and scan library

#### Method 2: Using Jellyfin WebDAV Plugin
1. Install WebDAV plugin from Jellyfin catalog
2. Configure with Debrid-Link endpoint: `http://debrid-link:9999/`
3. Map remote paths to library folders

### 4. Configure Radarr/Sonarr

#### Add Debrid-Link as Remote Path Mapping
1. In Radarr/Sonarr, go to **Settings** → **Download Clients**
2. Add a new download client (Manual/Custom)
3. Configure remote path mappings:
   - **Host**: `debrid-link`
   - **Remote Path**: `/movies` or `/shows`
   - **Local Path**: Point to your media folders

#### Alternative: Direct File Management
Since we're not using FUSE mounts, configure Radarr/Sonarr to:
1. Use local folders for completed downloads
2. Let Jellyfin stream directly from Debrid-Link WebDAV
3. Optionally cache popular content locally

### 5. Verify Setup

#### Check Debrid-Link Status
```bash
# Check if Debrid-Link is running
docker logs debrid-link

# Test WebDAV endpoint
curl http://localhost:9999/

# Access Web UI
http://localhost:8080
```

#### Test in Jellyfin
1. Add a test library pointing to Debrid-Link WebDAV
2. Check if content appears in library scan
3. Try playing a file to verify streaming works

## Troubleshooting

### Cannot connect to WebDAV
- Ensure Debrid-Link container is running: `docker ps | grep debrid-link`
- Check Debrid-Link logs: `docker logs debrid-link`
- Verify AllDebrid API key is correct in .env file
- Access Web UI at http://localhost:8080 for configuration

### Slow streaming/buffering
- Debrid-Link streams directly from AllDebrid
- Performance depends on your internet connection
- Consider adjusting Jellyfin transcoding settings
- Check cache settings in Debrid-Link configuration

### Library not updating
- Manual scan: Jellyfin Dashboard → Libraries → Scan All Libraries
- Check Debrid-Link refresh settings in Web UI
- Verify WebDAV paths match your AllDebrid content structure
- Ensure API key has proper permissions

## Benefits of WebDAV vs FUSE Mount
- ✅ Works on macOS Docker Desktop
- ✅ No special privileges required
- ✅ Direct HTTP streaming
- ✅ No complex mount configurations
- ✅ Can be accessed from multiple services simultaneously

## Optional Enhancements

### Local Caching
If you want to cache frequently accessed content:
1. Set up a local cache directory
2. Configure Jellyfin to prefer local files
3. Use scripts to download popular content

### CDN Integration
For better streaming performance:
1. Consider using Cloudflare tunnel
2. Set up reverse proxy with caching
3. Configure CDN for static content delivery