# DNSBlocker — AdGuard Home + Unbound

This is **one CasaOS app** with **two Compose services**: AdGuard Home (the browser-facing service) and Unbound (the internal upstream resolver).

## Default network topology

```text
LAN / clients -> HOST:53 (TCP + UDP) -> AdGuard Home -> 172.30.53.2:53 -> Unbound -> upstream Internet DNS
                                             |
                                        172.30.53.3
```

The store definition uses an isolated Docker **bridge** network `172.30.53.0/24`. These are internal Docker addresses, **not** your Raspberry Pi or CasaOS host LAN addresses. Unlike the provided personal example, this configuration does **not** require an existing external `br0` macvlan network, a specific physical NIC, fixed physical MAC addresses or hardcoded LAN device DNS overrides.

**Check that `172.30.53.0/24` does not conflict** with your existing Docker or LAN/VPN networks. Change the subnet and both services' addresses together if it does.

## Required first-run setup

1. Confirm that host TCP/UDP **53** is free. CasaOS, systemd-resolved, AdGuard, Pi-hole, Unbound, dnsmasq or another DNS server may already use it.
2. Install this app in CasaOS. Open `http://HOST:3000` for AdGuard's initial setup.
3. In the setup wizard, bind DNS to port **53** and the web interface to container port **80**. The published later web UI is `http://HOST:8088`. If choosing another web port, update its Compose mapping as well.
4. Under **Settings > DNS settings > Upstream DNS servers**, set **`172.30.53.2`** (UDP/TCP port 53), then test and save. This value is the Unbound service's address on the isolated Compose network. Do not set it to the host's public DNS address.
5. Set a unique administrator password, then test DNS from a separate LAN client: `nslookup example.com HOST_IP`. Only after it works should you configure router/DHCP clients to use the CasaOS host as DNS.

**Why not automatically overwrite AdGuardHome.yaml?** AdGuard's own first-run wizard manages its persistent configuration. Compose alone does not offer an officially supported `UPSTREAM_DNS` container variable. This template provides deterministic internal network addressing and the exact upstream value, without replacing user-managed TLS, authentication, or DNS settings.

Unbound's **default** `mvance/unbound` image configuration forwards DNS to Cloudflare over TLS; if you want actual full recursive resolution, deploy a carefully checked custom Unbound configuration instead. When mounting a custom `/opt/unbound/etc/unbound` directory, put working configuration files in it **before** enabling the volume in Compose. An empty mount can hide the image defaults.

## Port and network cautions

- TCP and UDP **53** are needed for LAN DNS service. Do not expose an unauthenticated public DNS resolver.
- Port **3000** serves the initial setup wizard; host **8088** maps to AdGuard's standard web port 80 once configured.
- Do not use the provided example's public DNS and private DNS host overrides indiscriminately. Configure AdGuard's **DNS rewrites** or Unbound local records separately for your own network.
- Docker bridge mode may show a Docker gateway address rather than each original client IP depending on host NAT. If you require per-client LAN IP visibility, use a carefully configured macvlan/ipvlan or another network topology as a separate advanced deployment.
- The default deployment does not publish DNS-over-TLS, DNS-over-HTTPS, DNSCrypt, or DHCP ports. Add only the ports and credentials you intend to use.
- Unbound is not publicly exposed; its service remains reachable from the AdGuard container over `dns_backend`. The `depends_on` condition only enforces startup order, not DNS health.
- Store app updates should not delete the persisted AdGuard `conf` and `work` directories. Back them up before significant upgrades.

## Upstream projects

- [AdGuard Home](https://github.com/AdguardTeam/AdGuardHome) / [Docker instructions](https://adguard-dns.io/kb/adguard-home/docker/)
- [mvance Unbound](https://github.com/MatthewVance/unbound-docker)

The user's original Compose included a custom macvlan `br0`, separate LAN IPs for AdGuard and Unbound, host-specific DNS overrides, local CA certificates, and larger resource allocations. Those **are not automatically transferable** to arbitrary CasaOS hosts. Copy them into a separately reviewed Compose variant only when those exact dependencies exist.
