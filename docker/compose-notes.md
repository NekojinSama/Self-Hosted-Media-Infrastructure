services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    user: 1000:1000
    ports:
      - "8096:8096/tcp"
    volumes:
      - ${CONFIG_LOCATION}/JellyFin/config:/config
      - ${CONFIG_LOCATION}/JellyFin/cache:/cache
      - ${DATA_LOCATION}/JellyFin/media:/media
    restart: unless-stopped
    environment:
      - TZ=${TIMEZONE}
    # This properly passes your CPU's integrated graphics to Jellyfin for transcoding
    devices:
      - /dev/dri:/dev/dri
    extra_hosts:
      - "host.docker.internal:host-gateway"
    dns:
      - 1.1.1.1
      - 8.8.8.8


  gluetun:
    image: qmcgaw/gluetun
    container_name: gluetun
    cap_add:
      - NET_ADMIN
    dns:
      - 1.1.1.1
      - 8.8.8.8
    devices:
      - /dev/net/tun
    ports: # Expose qBittorrent's web UI and torrent ports through VPN
      # - 8700:8096   # Jellyfin
      - 8701:8701   # qBittorrent Web UI
      - 6881:6881   # torrent port
      - 6881:6881/udp
      - 7878:7878   # Radarr
      - 8989:8989   # Sonarr
      - 8686:8686   # Lidarr
      - 6767:6767   # Bazarr
      - 9696:9696   # Prowlarr
      - 8191:8191   # FlareSolverr
      # - 5055:5055   # Jellyseerr
    volumes:
      - ${CONFIG_LOCATION}/Glueten:/gluetun
      # - ./gluetun/us-den.conf:/gluetun/wireguard/wg0.conf
    environment:
      - VPN_TYPE=wireguard
      - VPN_SERVICE_PROVIDER=nordvpn
      - OPENVPN_USER=${OPENVPN_USER}
      - OPENVPN_PASSWORD=${OPENVPN_PASSWORD}
      - WIREGUARD_PRIVATE_KEY=${OPENVPN_PRIVATE_KEY}
      - WIREGUARD_MTU=1320
      #- VPN_PORT_FORWARDING=on
      - SERVER_COUNTRIES=Singapore,Hong Kong,India
      - TZ=${TIMEZONE}
      - FIREWALL_OUTBOUND_SUBNETS=192.168.1.0/24,100.64.0.0/10
      #- DNS_KEEP_NAMESERVERS=on
      #- VPN_ENDPOINT_DNS=1.1.1.1
    restart: unless-stopped

  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent
    container_name: qbittorrent
    network_mode: "service:gluetun"  # Routes all traffic through Gluetun
    depends_on:
      - gluetun
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=${TIMEZONE}
      - WEBUI_PORT=8701
      - WEBUI_ADDRESS=0.0.0.0
      - WEBUI_EXTERNAL_ACCESS=true
    volumes:
      - ${CONFIG_LOCATION}/QBitTorrent:/config
      - ${DATA_LOCATION}:/data
    restart: unless-stopped

  prowlarr:
    image: lscr.io/linuxserver/prowlarr
    container_name: prowlarr
    restart: unless-stopped
    network_mode: "service:gluetun"  # Routes all traffic through Gluetun
    depends_on:
      - gluetun
    # ports:
    #   - 9696:9696
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=${TIMEZONE}
    volumes:
      - ${CONFIG_LOCATION}/Prowlarr:/config

  flaresolverr:
    image: ghcr.io/flaresolverr/flaresolverr:latest
    container_name: flaresolverr
    network_mode: "service:gluetun"  # Routes all traffic through Gluetun
    depends_on:
      - gluetun
    environment:
      - TZ=${TIMEZONE}
    # ports:
    #   - 8191:8191
    restart: unless-stopped

  radarr:
    image: lscr.io/linuxserver/radarr
    container_name: radarr
    restart: unless-stopped
    network_mode: "service:gluetun"  # Routes all traffic through Gluetun
    depends_on:
      - gluetun
    # ports:
    #   - 7878:7878
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=${TIMEZONE}
    volumes:
      - ${CONFIG_LOCATION}/radarr:/config
      - ${DATA_LOCATION}:/data

  sonarr:
    image: lscr.io/linuxserver/sonarr
    container_name: sonarr
    restart: unless-stopped
    network_mode: "service:gluetun"  # Routes all traffic through Gluetun
    depends_on:
      - gluetun
    # ports:
    #   - 8989:8989
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=${TIMEZONE}
    volumes:
      - ${CONFIG_LOCATION}/sonarr:/config
      - ${DATA_LOCATION}:/data

  lidarr:
    image: lscr.io/linuxserver/lidarr:latest
    container_name: lidarr
    network_mode: "service:gluetun"  # Routes all traffic through Gluetun
    depends_on:
      - gluetun
    # ports:
    #   - 8686:8686
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=${TIMEZONE}
    volumes:
      - ${CONFIG_LOCATION}/lidarr:/config
      - ${DATA_LOCATION}:/data
    restart: unless-stopped

  bazarr:
    image: lscr.io/linuxserver/bazarr
    container_name: bazarr
    restart: unless-stopped
    network_mode: "service:gluetun"  # Routes all traffic through Gluetun
    depends_on:
      - gluetun
    # ports:
    #   - 6767:6767
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=${TIMEZONE}
    volumes:
      - ${CONFIG_LOCATION}/bazarr:/config
      - ${DATA_LOCATION}:/data
      
  jellyseerr:
    image: ghcr.io/seerr-team/seerr:latest
    container_name: jellyseerr
    environment:
      - TZ=${TIMEZONE}   
      - LOG_LEVEL=info
    ports:
      - "5055:5055"
    volumes:
      - ${CONFIG_LOCATION}/jellyseerr:/app/config
    restart: unless-stopped
    dns:
      - 1.1.1.1
      - 8.8.8.8
    depends_on:
      - gluetun
      - jellyfin
      - sonarr
      - radarr
