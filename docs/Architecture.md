# 🏠 HomeLab 2026 — Architecture

Version: 2.0  
Updated: 2026-08-01

## Purpose

HomeLab is a private infrastructure project focused on reliability, security, recoverability and gradual expansion.

## Core principles

- NanoPi R5S is the network control point.
- Xiaomi BE7000 provides Wi-Fi and Layer-2 switching only.
- Remote access must remain independent from Internet VPN experiments.
- All major changes require backup, rollback and validation.
- Documentation is the source of truth.

---

# Current Architecture

```text
                ISP 1 / ISP 2
                     │
                     ▼
               NanoPi R5S
              OpenWrt 24.10.4
                     │
       ┌─────────────┼─────────────┐
       │             │             │
       ▼             ▼             ▼
     LAN/Wi-Fi     wg_ams       wg_transit
  192.168.2.0/24  Amsterdam      Remote access
                     │          10.101.0.0/24
                     ▼
              Foreign Internet
```

A separate `wg_home` overlay uses `10.100.0.0/24`.

---

# Network Core

## NanoPi R5S

Role: main router and policy-routing gateway.

Functions:

- OpenWrt
- dual WAN through mwan3
- firewall4 / nftables
- PBR
- WireGuard
- DHCP and DNS
- IPTV routing
- backup and recovery point

Known interfaces:

- `wan`
- `wanb`
- `lan`
- `wg_home`
- `wg_transit`
- `wg_ams`

Singapore `wg_sg` is deprecated and must not be used in the target architecture.

---

# Internet Routing

## Home clients

Source network: `192.168.2.0/24`.

- Russian IPv4 prefixes → WAN
- all remaining Internet traffic → `wg_ams`
- local and service subnets → dedicated policies

## Remote clients

Remote traffic reaches NanoPi through `wg_transit` (`10.101.0.0/24`).

- local HomeLab devices remain accessible;
- Russian Internet destinations → WAN;
- remaining Internet traffic → `wg_ams`;
- firewall forwarding `transit → vpn_ams` is required.

## Home VPN overlay

`wg_home` uses `10.100.0.0/24` and remains separate from the transit path.

---

# VPN Roles

| Interface | Role | Subnet |
|---|---|---|
| `wg_ams` | Internet egress through Amsterdam | `10.30.0.0/24` |
| `wg_transit` | Remote access transit | `10.101.0.0/24` |
| `wg_home` | Home VPN overlay | `10.100.0.0/24` |
| `wg_sg` | Deprecated Singapore experiment | `10.31.0.0/24` |

---

# Smart Routing Direction

The current RU-prefix model is stable but not sufficient for all applications. Some services use mixed-country APIs, CDN and authentication endpoints.

The next architecture layer is **HomeLab Smart Routing**:

- observe DNS and connection results;
- compare WAN and `wg_ams` using controlled probes;
- store preferred route decisions in SQLite;
- apply only high-confidence decisions to future connections;
- start in monitor-only mode;
- preserve immediate rollback to the current PBR configuration.

See [Smart Routing Project](Smart_Routing_Project.md).

---

# Server and Storage

Planned components:

- NAS
- Docker host
- Jellyfin
- Immich
- Nextcloud
- Vaultwarden
- PostgreSQL
- Grafana and Prometheus
- centralized backups

---

# Recovery Policy

The current stable NanoPi state has two backup forms:

- technical configuration archive;
- native OpenWrt `sysupgrade -b` archive.

Both must be kept outside NanoPi. See [Backup and Restore](Backup_and_Restore.md).
