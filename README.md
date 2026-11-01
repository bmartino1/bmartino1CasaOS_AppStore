# bmartino1 CasaOS App Store

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
| [RetroAssembly](Apps/retroassembly/docker-compose.yml) | [arianrhodsandlot/retroassembly](https://github.com/arianrhodsandlot/retroassembly) | [arianrhodsandlot/retroassembly](https://hub.docker.com/r/arianrhodsandlot/retroassembly) | [retroassembly.xml](https://github.com/bmartino1/unraid-docker-templates/blob/main/templates/retroassembly.xml) |

RetroAssembly is an **upstream third-party project**, not maintained by bmartino1. Refer to the [upstream Docker self-hosting documentation](https://github.com/arianrhodsandlot/retroassembly#option-2-self-host-with-docker) for any settings beyond the simple Unraid template.

## Configuration, paths, and safety

- The example host paths use CasaOS-style `/DATA/AppData/<app>` rather than Unraid's `/mnt/user/appdata/<app>`. Adjust them for your host **before installation**, especially the scan target and SFTP shared directory.
- **NetProbe:** persistent `/config`, HTTPS 8443, optional HTTP 8080, `NET_RAW`, SQLite by default and optional separately managed PostgreSQL. Its *newer* single-container Compose is the reference; first-boot variables seed settings but saved WebUI settings take priority. The optional Ookla CLI is **not bundled** and requires user acceptance and separate installation.
- **SFTP-Fail2ban:** `ADMIN_PASS` is intentionally empty; supply a strong unique password or configure `/config/sshd/users.conf` and keys before starting. Ensure `/home/admin/sftp` matches the intended account name, user permissions and host folder. The upstream Debian-based source is [sftp-debian](https://github.com/bmartino1/sftp-debian); Docker image is `sftp2`. Exposing SSH publicly requires additional hardening and port/firewall planning.
- **ClamAV-clamdscan:** the `/scan` mount is **read-only**. Signature DB, custom config and logs persist in separate locations. It scans once and exits. Review logs; the lack of a running container afterward is expected. Do not disable signature updates without understanding the consequences.
- **PlexDBRepair:** requires `/var/run/docker.sock`, which grants host-level Docker control. It modifies Plex databases; maintain separate verified backups, inspect the target Plex container, and note that some repair failures leave Plex stopped. One-shot restart policy is `no`. A working network connection to GitHub is required for the official DBRepair runtime download; see the [project documentation](https://github.com/bmartino1/plex-dbrepair-docker).
- **RetroAssembly:** persist `/app/data`; upstream is independently maintained. Default WebUI port 8000. Only upload game content you have rights to use.

The CasaOS application definitions include `x-casaos` metadata and supported extra Compose settings. **CasaOS may not expose every option as a distinct form control**; additional settings can be adjusted in the imported Compose editor. Some upstream optional behaviors are intentionally documented rather than enabled automatically, especially privileged access, credentials, and alternate infrastructure.

## Validation status

Source definitions have been cross-referenced with upstream project documentation and Unraid templates. **An actual CasaOS App Store ZIP import, image architecture compatibility, and installation on a target host have not yet been tested.** Review all settings before enabling applications. The project is independent from the official CasaOS App Store and does not claim official support.

