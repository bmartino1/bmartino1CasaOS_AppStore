# bmartino1 CasaOS App Store

Native CasaOS third-party app store providing Compose configurations corresponding to selected [bmartino1 Unraid templates](https://github.com/bmartino1/unraid-docker-templates).

## Add to CasaOS

In CasaOS App Store, choose **Add Source** and paste:

```text
https://github.com/bmartino1/bmartino1CasaOS_AppStore/archive/refs/heads/main.zip
```

CasaOS 0.4.4 or newer supports third-party app stores. This URL serves the current main branch as a ZIP source (not a single YAML or raw HTTP compose file).

## Included apps

| App | Docker image | Notes |
| --- | --- | --- |
| NetProbe | `bmmbmm01/netprobe:latest` | Monitor internet quality and connectivity |
| SFTP-Fail2ban | `bmmbmm01/sftp2:latest` | SFTP access with brute-force protection |
| ClamAV-clamdscan | `bmmbmm01/clamav-alpine:latest` | On-demand read-only malware scanning |
| PlexDBRepair | `bmmbmm01/plex-dbrepair` | Plex database checks and repair |
| RetroAssembly | `arianrhodsandlot/retroassembly` | Browser-based retro gaming |

Each app has a standalone `Apps/<slug>/docker-compose.yml` definition, including `x-casaos` install-form hints, persistent mounts, settings, descriptions, and icons.

## Important deployment notes

- Host paths are CasaOS-style defaults under `/DATA`, **not** the Unraid `/mnt/user` paths. Adjust them in the CasaOS installation dialog.
- SFTP-Fail2ban's `ADMIN_PASS` placeholder **must** be replaced with a unique password before first start; verify the username-specific share mount.
- ClamAV-clamdscan and PlexDBRepair are **one-shot containers** with restart policy `no`. A stopped container following a scan or maintenance run is expected.
- PlexDBRepair mounts `/var/run/docker.sock` and therefore has **host-level Docker privileges**; verify backups and target before running.
- NetProbe uses HTTPS on 8443 with a self-signed certificate initially. Ookla CLI is not bundled and requires separate acceptance/install.
- RetroAssembly data persists in `/DATA/AppData/retroassembly`.
- Existing Unraid image URLs are reused so the visuals stay aligned; not all apps have a web dashboard.
- CPU image availability and actual CasaOS installation/runtime functionality must be verified on the target host. Architecture metadata describes intended platforms, not a tested image manifest.

## Source configurations

- [NetProbe](Apps/netprobe/docker-compose.yml)
- [SFTP-Fail2ban](Apps/sftp-fail2ban/docker-compose.yml)
- [ClamAV-clamdscan](Apps/clamdscan/docker-compose.yml)
- [PlexDBRepair](Apps/plex-dbrepair/docker-compose.yml)
- [RetroAssembly](Apps/retroassembly/docker-compose.yml)

Upstream template details: https://github.com/bmartino1/unraid-docker-templates/tree/main/templates

