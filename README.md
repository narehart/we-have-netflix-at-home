# Complete Setup Guide for Media Server Stack

## Quick Links

Access the services at:

- **NZBGet**: http://localhost:6789
- **Plex**: http://localhost:32400/web
- **Portainer**: http://localhost:9000
- **Radarr**: http://localhost:7878
- **Sonarr**: http://localhost:8989

## Prerequisites

### Online Services

- A Usenet provider ([Easynews](https://www.easynews.com/) recommended)
- A Usenet indexer ([NZBGeek](https://nzbgeek.info/) recommended)
- [Docker](https://www.docker.com/) installed on your system
- A [Plex](https://www.plex.tv/) account

## Setup

- Clone the repo
- Change directory to the cloned repo
- Create the following directory structure:

```bash
.
├── config/
│   ├── radarr/         # Radarr configuration
│   ├── sonarr/         # Sonarr configuration
│   ├── nzbget/         # NZBGet configuration
│   ├── plex/           # Plex configuration
│   └── portainer/      # Portainer configuration
└── data/
    ├── downloads/      # Shared downloads folder used by Radarr, Sonarr, and NZBGet
    └── media/
        ├── movies/     # Movie library for Radarr and Plex
        └── tv/         # TV show library for Sonarr and Plex
```

## Services Configuration

Start the containers with `docker-compose up -d`.

### 1. Portainer Setup (Optional but Recommended)

1. **Initial Access**:

   - Go to http://localhost:9000
   - Create an admin user with a secure password
   - Choose "Local" environment when prompted

2. **Verifying Containers**:

   - On the Home page the Environments list should now contain a single item
   - The green power icon should have a 5 next to it

### 2. NZBGet Setup

1. **Initial Access**:

   - Go to http://localhost:6789
   - Default credentials: username `nzbget`, password `tegbzn6789`

2. **Basic Configuration**:

   - Go to Settings > News Servers
   - Add your Usenet provider details (hostname, port, username, password)
   - Enable SSL if your provider supports it

3. **Download Paths**:

   - In Settings > Paths, verify:
     - MainDir: `/downloads`
     - DestDir: `/downloads/completed`
     - InterDir: `/downloads/intermediate`
     - NzbDir: `/downloads/nzb`

4. **Categories Setup** (for Radarr/Sonarr integration):
   - Go to Settings > Categories
   - Create two categories:
     - Name: `movies`, DestDir: `/downloads/completed/movies`
     - Name: `tv`, DestDir: `/downloads/completed/tv`

### 3. Radarr Setup

1. **Initial Access**:

   - Go to http://localhost:7878
   - No default password required

2. **Add Download Client**:

   - Go to Settings > Download Clients > Add
   - Select NZBGet
   - Host: `nzbget` (container name)
   - Port: `6789`
   - Username: `nzbget` (or your custom username)
   - Password: `tegbzn6789` (or your custom password)
   - Category: `movies`
   - Test and save

3. **Add Remote Path Mappings**

   - Go Settings > Download Clients
   - Under Remote Path Mappings click the plus (+) button
   - Host: `nzbget` (container name)
   - Remote Path: `/downloads/completed/movies/`
   - Local Path: `/downloads/completed/movies`

4. **Add Movie Library**:

   - Go to Media Management
   - Enable "Use Hardlinks" if possible
   - Set "Root Folders" to `/movies`

5. **Add Indexers**:
   - Go to Settings > Indexers > Add
   - Add your preferred Usenet indexers (NZBGeek, etc.)
   - Enter API keys as required

## 4. Sonarr Setup

1. **Initial Access**:

   - Go to http://localhost:8989
   - No default password required

2. **Add Download Client**:

   - Go to Settings > Download Clients > Add
   - Select NZBGet
   - Host: `nzbget` (container name)
   - Port: `6789`
   - Username: `nzbget` (or your custom username)
   - Password: `tegbzn6789` (or your custom password)
   - Category: `tv`
   - Test and save

3. **Add Remote Path Mappings**

   - Go Settings > Download Clients
   - Under Remote Path Mappings click the plus (+) button
   - Host: `nzbget` (container name)
   - Remote Path: `/downloads/completed/tv/`
   - Local Path: `/downloads/completed/tv`

4. **Add TV Library**:

   - Go to Media Management
   - Enable "Use Hardlinks" if possible
   - Set "Root Folders" to `/tv`

5. **Add Indexers**:
   - Go to Settings > Indexers > Add
   - Add your preferred Usenet indexers (NZBGeek, etc.)
   - Enter API keys as required

## 5. Plex Setup

1. **Initial Access**:

   - Go to http://localhost:32400/web
   - You'll be guided through a first-time setup wizard

2. **Server Setup**:

   - Name your server
   - If prompted for a Plex claim token, get one from https://plex.tv/claim
   - Skip adding libraries for now (we'll add them next)

3. **Add Libraries**:

   - After initial setup, go to Settings > Libraries > Add Library
   - Add a Movies library:
     - Type: Movies
     - Folder: `/data/movies`
   - Add a TV Shows library:
     - Type: TV Shows
     - Folder: `/data/tv`

### 6. Automatically Download from Watchlist

1. **In Plex**:

   - Search for at least on movie and click `Add to Watchlist`
   - Search for at least one tv show and click `Add to Watchlist`

1. **In Radarr**:

   - Go to Settings > Connect > Add > Plex
   - Host: `plex`
   - Port: `32400`
   - Authentication: Use Plex token (find in Plex settings)
   - Test and save

1. **In Sonarr**:

   - Go to Settings > Connect > Add > Plex
   - Host: `plex`
   - Port: `32400`
   - Authentication: Use Plex token (find in Plex settings)
   - Test and save

### 7. Testing

1. **Test the Full Flow**:
   - In Radarr: Add a movie, set quality profile, and add to download queue
   - In Sonarr: Add a TV show, set quality profile, and add to download queue
   - Watch NZBGet download the files
   - Verify Radarr/Sonarr import the completed downloads
   - Check Plex to see if the media appears in your libraries
