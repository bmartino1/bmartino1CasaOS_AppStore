# bmartino1 CasaOS App Store

[![Platform: CasaOS](https://img.shields.io/badge/Platform-CasaOS-2B80FF?logo=docker&logoColor=white)](https://casaos.zimaspace.com/)

> **Unofficial community-maintained third-party CasaOS App Store.** This independent source follows [CasaOS's guide to creating a custom AppStore](https://awesome.casaos.io/content/3rd-party-app-stores/create-your-first-custom-appstore.html). It is **not affiliated with, sponsored by, or endorsed by CasaOS or IceWhaleTech**. This is not a fork of the official App Store.

CasaOS-compatible Docker Compose definitions adapted from the [bmartino1 Unraid Docker templates](https://github.com/bmartino1/unraid-docker-templates). Each application links to its own project source for complete functionality, configurable settings, examples, and deployment details.

## Install this source in CasaOS

CasaOS App Store → **Add Source**, and paste the ZIP download URL:

```text
https://github.com/bmartino1/bmartino1CasaOS_AppStore/archive/refs/heads/main.zip
```

The ZIP source contains multiple `Apps/<app>/docker-compose.yml` definitions; **do not use a single raw Compose URL as a store source**. Follow the [official custom AppStore guide](https://awesome.casaos.io/content/3rd-party-app-stores/create-your-first-custom-appstore.html).

## Applications and original project repositories

| App | Project source / complete documentation | Docker image | Unraid reference |
| --- | --- | --- | --- |
| [NetProbe](Apps/netprobe/docker-compose.yml) | [bmartino1/NetProbe_Python](https://github.com/bmartino1/NetProbe_Python) | [bmmbmm01/netprobe](https://hub.docker.com/r/bmmbmm01/netprobe) | [netprobe.xml](https://github.com/bmartino1/unraid-docker-templates/blob/main/templates/netprobe.xml) |
| [SFTP-Fail2ban](Apps/sftp-fail2ban/docker-compose.yml) | [bmartino1/sftp-debian](https://github.com/bmartino1/sftp-debian) | [bmmbmm01/sftp2](https://hub.docker.com/r/bmmbmm01/sftp2) | [sftp2.xml](https://github.com/bmartino1/unraid-docker-templates/blob/main/templates/sftp2.xml) |
| [ClamAV-clamdscan](Apps/clamdscan/docker-compose.yml) | [bmartino1/clamav-alpine](https://github.com/bmartino1/clamav-alpine) | [bmmbmm01/clamav-alpine](https://hub.docker.com/r/bmmbmm01/clamav-alpine) | [clamdscan.xml](https://github.com/bmartino1/unraid-docker-templates/blob/main/templates/clamdscan.xml) |
| [PlexDBRepair](Apps/plex-dbrepair/docker-compose.yml) | [bmartino1/plex-dbrepair-docker](https://github.com/bmartino1/plex-dbrepair-docker) | [bmmbmm01/plex-dbrepair](https://hub.docker.com/r/bmmbmm01/plex-dbrepair) | [plexrepair.xml](https://github.com/bmartino1/unraid-docker-templates/blob/main/templates/plexrepair.xml) |
| [DockerManger](Apps/dockermanger/docker-compose.yml) | [bmartino1/DockerManger](https://github.com/bmartino1/DockerManger) | [bmmbmm01/dockermanger](https://hub.docker.com/r/bmmbmm01/dockermanger) | [upstream Compose](https://github.com/bmartino1/DockerManger/blob/main/compose.yaml) |
| [RetroAssembly](Apps/retroassembly/docker-compose.yml) | [arianrhodsandlot/retroassembly](https://github.com/arianrhodsandlot/retroassembly) | [arianrhodsandlot/retroassembly](https://hub.docker.com/r/arianrhodsandlot/retroassembly) | [retroassembly.xml](https://github.com/bmartino1/unraid-docker-templates/blob/main/templates/retroassembly.xml) |

RetroAssembly is an **upstream third-party project**, not maintained by bmartino1. Refer to the [upstream Docker self-hosting documentation](https://github.com/arianrhodsandlot/retroassembly#option-2-self-host-with-docker) for any settings beyond the simple Unraid template.

## Additional file sharing and Bitcoin GUI apps

| App | Source | Docker image | Notes |
| --- | --- | --- | --- |
| [Time Machine (Samba)](Apps/timemachine/docker-compose.yml) | [upstream](https://github.com/mbentley/docker-timemachine) | `mbentley/timemachine:smb` | Apple Time Machine backups via Samba |
| [NFS File Sharing](Apps/nfs-server/docker-compose.yml) | [upstream](https://hub.docker.com/r/itsthenetwork/nfs-server-alpine) | `itsthenetwork/nfs-server-alpine:latest` | Export a host folder over NFSv4 |
| [Bitcoin Knots GUI](Apps/bitcoin-knots-gui/docker-compose.yml) | [upstream](https://hub.docker.com/r/bmmbmm01/bitcoin-knots-gui) | `bmmbmm01/bitcoin-knots-gui:latest` | Bitcoin Knots wallet and node desktop |

**Time Machine SMB port conflict:** CasaOS or the host may already provide native Samba services on TCP 445 and related SMB ports. Time Machine requires these ports by default and may need a separate host IP/network configuration when the built-in Samba service is active. Bridge networking may require a manual Finder connection; Avahi auto-discovery is not guaranteed. Replace the Time Machine password before installing. Use CasaOS's integrated Samba service for generic file sharing.

**NFS stays in this store:** CasaOS does not yet provide the desired integrated NFS workflow for this use case. **NFS caution:** NFS requires privileged operation and kernel NFS support. Change the example client CIDR before starting; default export is read-only. Restrict access to a trusted LAN/VPN, never expose NFS/SMB on the internet.

**Bitcoin caution:** The requested `bmmbmm01/bitcoin-knots-gui` image is **marked archived** on Docker Hub. This is provided for compatibility rather than represented as a maintained, security-updated image. Web VNC is on port 5800 and wallet/blockchain files persist at `/config/.bitcoin`. Keep it off public networks and back up the wallet. The legacy `AUTOUPDATE` toggle may be necessary on initial installation; consult the Docker Hub image instructions.

## Combined DNS stack

| App | Sources | Description |
| --- | --- | --- |
| [DNSBlocker - AdGuard Home + Unbound](Apps/dnsblocker-adguardhome_Unbound/docker-compose.yml) | [AdGuard Home](https://github.com/AdguardTeam/AdGuardHome) + [Unbound Docker](https://github.com/MatthewVance/unbound-docker) | Two services managed as one CasaOS Compose app. See the [setup guide](Apps/dnsblocker-adguardhome_Unbound/README.md). |

The portable default uses a private Docker bridge (AdGuard `172.30.53.3`, Unbound `172.30.53.2`) instead of requiring a pre-existing macvlan `br0`. The installer must select Unbound `172.30.53.2` as AdGuard's upstream during initial configuration; Compose cannot safely initialize the entire interactive AdGuard profile. Verify port 53 availability and that the internal subnet does not conflict before installation. No private LAN DNS rewrite records are imported by default.

## MakeMKV — optional optical drive passthrough

| App | Source | Container | Defaults |
| --- | --- | --- | --- |
| [MakeMKV](Apps/makemkv/docker-compose.yml) | [jlesage/docker-makemkv](https://github.com/jlesage/docker-makemkv) | `jlesage/makemkv:latest` | Browser UI on host 7806, optional VNC 7906, persistent /config and /output; **no optical drive is mapped initially**. |

MakeMKV installs without predefined `/dev/srX` or `/dev/sgY` device mappings. The web interface can start without a disc drive. To enable ripping, identify the actual optical drive pair on the Docker host with `lsscsi -g`, then **edit the CasaOS Compose definition** to add just those devices and recreate the container. On PVE/LXC/VM hosts, pass the optical device(s) through to the CasaOS guest before doing so. A sample `devices:` section is commented out in the app Compose. Avoid blindly mapping all `/dev/sg*` devices. No privileged mode or disabled seccomp/AppArmor profiles are enabled by default.

No registration key from personal deployments is committed; `MAKEMKV_KEY=BETA` is used instead, subject to upstream beta availability. Automatic ripping is initially disabled (`AUTO_DISC_RIPPER=0`) until a drive is deliberately configured. HTTP and VNC are unencrypted and unauthenticated by default: restrict LAN/VPN access or configure upstream TLS and authentication before broader use. Only process media you are authorized to copy.

## Configuration, paths, and safety

- The example host paths use CasaOS-style `/DATA/AppData/<app>` rather than Unraid's `/mnt/user/appdata/<app>`. Adjust them for your host **before installation**, especially the scan target and SFTP shared directory.
- **NetProbe:** persistent `/config`, HTTPS 8443, optional HTTP 8080, `NET_RAW`, SQLite by default and optional separately managed PostgreSQL. Its *newer* single-container Compose is the reference; first-boot variables seed settings but saved WebUI settings take priority. The optional Ookla CLI is **not bundled** and requires user acceptance and separate installation.
- **SFTP-Fail2ban:** `ADMIN_PASS` is intentionally empty; supply a strong unique password or configure `/config/sshd/users.conf` and keys before starting. Ensure `/home/admin/sftp` matches the intended account name, user permissions and host folder. The upstream Debian-based source is [sftp-debian](https://github.com/bmartino1/sftp-debian); Docker image is `sftp2`. Exposing SSH publicly requires additional hardening and port/firewall planning.
- **ClamAV-clamdscan:** the `/scan` mount is **read-only**. Signature DB, custom config and logs persist in separate locations. It scans once and exits. Review logs; the lack of a running container afterward is expected. Do not disable signature updates without understanding the consequences.
- **PlexDBRepair:** requires `/var/run/docker.sock`, which grants host-level Docker control. It modifies Plex databases; maintain separate verified backups, inspect the target Plex container, and note that some repair failures leave Plex stopped. One-shot restart policy is `no`. A working network connection to GitHub is required for the official DBRepair runtime download; see the [project documentation](https://github.com/bmartino1/plex-dbrepair-docker).
- **DockerManger:** HTTPS port 5443, HTTP redirect 5001, persistent /data and /opt/stacks, first-run account creation. Requires unrestricted Docker socket access and must only be available on a trusted LAN/VPN. Changes to the external HTTPS host port must also update DOCKERMANGER_HTTPS_PORT.
- **RetroAssembly:** persist `/app/data`; upstream is independently maintained. Default WebUI port 8000. Only upload game content you have rights to use.

The CasaOS application definitions include `x-casaos` metadata and supported extra Compose settings. **CasaOS may not expose every option as a distinct form control**; additional settings can be adjusted in the imported Compose editor. Some upstream optional behaviors are intentionally documented rather than enabled automatically, especially privileged access, credentials, and alternate infrastructure.

## Validation status

The original six applications were confirmed present and functional by a CasaOS installation test. The four additions above have not yet undergone CasaOS installation/runtime testing. Source definitions have been cross-referenced with upstream project documentation and Unraid templates. **An actual CasaOS App Store ZIP import, image architecture compatibility, and installation on a target host have not yet been tested.** Review all settings before enabling applications. The project is independent from the official CasaOS App Store and does not claim official support.


## CasaOS import troubleshooting

If an app is missing from the catalog, validate its YAML and refresh/re-add the ZIP source. SFTP-Fail2ban and ClamAV-clamdscan had malformed YAML in an earlier revision and were corrected. DockerManger uses the published Docker Hub image, not the upstream local-build Compose recipe.
