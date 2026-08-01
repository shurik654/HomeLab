# 🌐 HomeLab 2026 — Network

Version: 2.0  
Updated: 2026-08-01

---

# Network Core

## NanoPi R5S

Operating system: OpenWrt 24.10.4.

Role: main gateway for LAN, dual WAN, VPN, remote access, IPTV and policy routing.

Physical interfaces:

- `eth0` — `wan`;
- `eth1` — `wanb`;
- `eth2` — LAN / `br-lan`.

LAN subnet: `192.168.2.0/24`.

## Xiaomi BE7000

Mode: access point and Layer-2 switch.

Routing, PBR and VPN decisions are handled by NanoPi.

---

# Dual WAN

mwan3 manages `wan` and `wanb`.

The Internet VPN must not replace endpoint reachability routes or interfere with remote access tunnels.

---

# WireGuard Interfaces

| Interface | Purpose | Subnet |
|---|---|---|
| `wg_ams` | Amsterdam Internet VPN | `10.30.0.0/24` |
| `wg_transit` | Remote access transit | `10.101.0.0/24` |
| `wg_home` | Home VPN overlay | `10.100.0.0/24` |
| `wg_sg` | Deprecated Singapore test | `10.31.0.0/24` |

The target architecture uses one Internet VPN: `wg_ams`.

---

# Routing Policy

## LAN clients

Source: `192.168.2.0/24`.

- local and service networks → dedicated routes;
- Russian IPv4 prefixes → WAN;
- other Internet traffic → `wg_ams` through policy `LAN_WORLD`.

## Remote clients

Actual remote-access traffic arrives through `wg_transit`.

Required behavior:

- `10.101.0.0/24` → LAN for local resources;
- Russian Internet destinations → WAN;
- remaining Internet destinations → `wg_ams` through policy `TRANSIT_WORLD`;
- firewall forwarding `transit → vpn_ams` must exist.

## Important rule order

Specific local and service policies must be placed before broad Internet policies.

The old custom rule that sent non-Russian LAN traffic to mark `0x030000` was removed because that mark belongs to `wg_home`, not Amsterdam.

---

# Russian Prefix Loader

Script:

```text
/usr/share/pbr/pbr.user.ru
```

Source:

```text
https://ftp.ripe.net/pub/stats/ripencc/delegated-ripencc-latest
```

The script loads Russian IPv4 prefixes into the PBR WAN destination set.

Current limitation: it runs when PBR starts or restarts. A separate scheduled updater has not yet been finalized.

The script must not contain a broad `LAN → not RU → wg_home` rule.

---

# Firewall Zones

Required zones and forwarding include:

- `lan → wan`;
- `lan → vpn_ams`;
- `transit → lan`;
- `transit → wan`;
- `transit → vpn_ams`;
- relevant `homevpn` forwarding for the Home overlay.

The `vpn_ams` zone uses masquerading and MTU fixing.

---

# IPTV

Beeline IPTV remains a separate policy-routing function.

- Cisco receiver: `192.168.2.238`;
- unicast traffic through `wanb`;
- multicast proxy from `wanb` to LAN;
- policy `IPTV_BEELINE` must remain before broad policies.

See [IPTV](IPTV.md).

---

# Known Application Limitation

Country-based IP routing does not guarantee correct behavior for applications that use mixed API, CDN, authentication and QUIC endpoints.

Observed examples include Kinopoisk and Meta Horizon.

The planned solution is [HomeLab Smart Routing](Smart_Routing_Project.md), initially in monitor-only mode.

---

# Change Safety

Before changing PBR, firewall or WireGuard:

1. create or verify a backup;
2. record current targeted configuration;
3. change one component only;
4. verify LAN, Russian sites, foreign sites and remote access;
5. keep a tested rollback command.
