# spoke-torrenting

<!--
==============================================================================
README.md - spoke-torrenting module documentation
==============================================================================
Description: VPN-protected torrenting and direct download Spoke module
Author: Matt Barham
Created: 2026-02-13
Modified: 2026-04-21
Version: 1.0.1
==============================================================================
Document Type: Reference
Audience: Developer
Status: Final
==============================================================================
-->

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/E1E21U3S1R)

Spoke module for VPN-protected torrenting and direct download.

## Services

| Service      | Description                    | Port  | Network            |
|--------------|--------------------------------|-------|--------------------|
| gluetun      | WireGuard VPN tunnel           | 8000  | troxy              |
| qbittorrent  | Torrent client (VPN routed)    | 8080  | container:gluetun  |
| qsticky      | VPN killswitch for qBittorrent | -     | troxy              |
| slskd        | Soulseek P2P client (VPN)      | 5030  | container:gluetun  |

## Prerequisites

- Spoke hub deployed with `troxy` network
- Traefik available as a hub service
- ProtonVPN WireGuard private key in secrets
- `/dev/net/tun` device available on host

## Quick Start

```bash
cp .env.example .env
# Edit .env — set download paths and VPN credentials
docker compose up -d
```

## Module Environment Variables

| Variable                      | Default                                       | Description               |
|-------------------------------|-----------------------------------------------|---------------------------|
| `GLUETUN_IMAGE`               | `qmcgaw/gluetun:v3.41.1`                     | Gluetun VPN image         |
| `QBITTORRENT_IMAGE`           | `ghcr.io/hotio/qbittorrent:release-5.2.1`    | qBittorrent image         |
| `QSTICKY_IMAGE`               | `ghcr.io/monstermuffin/qsticky:sha-98c834e`   | qSticky image             |
| `SLSKD_IMAGE`                 | `slskd/slskd:0.25.1`                         | Soulseek image            |
| `GLUETUN_IP`                  | `192.168.35.80`                               | Gluetun static IP         |
| `QSTICKY_IP`                  | `192.168.35.81`                               | qSticky static IP         |
| `QBITTORRENT_WEB_UI_PORT`     | `6611`                                        | qBittorrent web UI port   |
| `GLUETUN_CONTROL_SERVER_PORT` | `6612`                                        | Gluetun control port      |
| `DL_DIR`                      | `/mnt/storage/data/downloads`                 | Downloads base directory  |
| `MUSIC_DIR`                   | `/mnt/storage/media/Music`                    | Music library (for slskd) |

## Architecture

```
Internet ─── WireGuard VPN ─── gluetun ─┬── qbittorrent (container network)
                                         └── slskd (container network)
                             qsticky ──── monitors gluetun + qbittorrent
```

## References

- [Gluetun](https://github.com/qdm12/gluetun)
- [qBittorrent (hotio)](https://hotio.dev/containers/qbittorrent/)
- [qSticky](https://github.com/MonsterMuffin/qsticky)
- [Slskd](https://github.com/slskd/slskd)
