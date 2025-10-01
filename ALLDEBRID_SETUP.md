# AllDebrid Setup Guide - FOOLPROOF METHOD

## Overview
Using RDTClient as the most reliable AllDebrid integration. This downloads content locally and works with all your existing tools.

## How It Works
```
Radarr/Sonarr → Sends torrents → RDTClient → AllDebrid → Downloads to ./data/downloads/
                                                            ↓
                                                    Jellyfin reads from local files
```

## Setup Steps

### 1. Start RDTClient
```bash
docker-compose up -d rdtclient
```

### 2. Configure RDTClient
1. Open http://localhost:6500
2. Go to **Settings** → **General**
3. Set **Download Client** to **AllDebrid**
4. Enter your **AllDebrid API Key**: `fYq5urYC1PLYg0a7UPlg`
5. Set **Download Path** to `/data/downloads`
6. Save settings

### 3. Configure Radarr (Movies)
1. Open http://localhost:7878
2. Go to **Settings** → **Download Clients**
3. Click **Add** → **rTorrent/ruTorrent**
4. Configure:
   - **Name**: RDTClient
   - **Host**: rdtclient
   - **Port**: 6500
   - **URL Base**: /
   - **Username/Password**: (leave blank)
5. Test and save

### 4. Configure Sonarr (TV Shows)
1. Open http://localhost:8989
2. Go to **Settings** → **Download Clients**
3. Add the same RDTClient configuration as Radarr

### 5. Configure Jellyfin
1. Open http://localhost:8096
2. Add media libraries:
   - **Movies**: `/data/media/movies`
   - **TV Shows**: `/data/media/tv`

### 6. Set Up Automatic Moving
In RDTClient settings:
- Enable **Auto Delete** after download
- Set **Completed Downloads** path to `/data/downloads`
- Use Radarr/Sonarr to move files to `/data/media/`

## Benefits of This Method
- ✅ **Foolproof**: RDTClient is actively maintained for AllDebrid
- ✅ **Local Storage**: Fast playback, works offline
- ✅ **Full Integration**: Works with all your existing tools
- ✅ **No Mount Issues**: Uses regular Docker volumes
- ✅ **macOS Compatible**: No FUSE/mounting problems

## Workflow
1. Add movie/show to Radarr/Sonarr
2. Radarr/Sonarr finds torrent and sends to RDTClient
3. RDTClient adds torrent to AllDebrid
4. AllDebrid downloads the file
5. RDTClient downloads from AllDebrid to local storage
6. Radarr/Sonarr moves file to media folder
7. Jellyfin detects new content and adds to library

## Troubleshooting

### RDTClient can't connect to AllDebrid
- Verify API key in RDTClient settings
- Check AllDebrid account status
- Ensure API key has proper permissions

### Downloads not appearing in Jellyfin
- Check file permissions (PUID/PGID)
- Verify paths: `/data/downloads` → `/data/media/`
- Manual library scan in Jellyfin

### Slow downloads
- AllDebrid download speed depends on your account type
- Check RDTClient logs for any errors
- Verify internet connection

## Storage Requirements
This method downloads files locally, so ensure you have adequate disk space:
- Movies: ~2-8GB each
- TV Episodes: ~200MB-2GB each
- Recommend at least 500GB free space

## Alternative: Streaming Only
If you prefer streaming without local storage, consider:
1. Using AllDebrid's web interface directly
2. Browser-based streaming from AllDebrid
3. Mobile apps that support AllDebrid streaming

But for full automation with Radarr/Sonarr/Jellyfin, the RDTClient method is most reliable.