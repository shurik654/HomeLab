# 🏠 HomeLab 2026

Private home infrastructure and documentation project.

## Vision

Create a reliable, secure, recoverable and self-managed home infrastructure.

Main principles:

- full control over hardware and software;
- data ownership;
- security and rollback-first changes;
- redundancy where it is justified;
- documentation as the source of truth;
- one tested change at a time.

---

# 🏗 Current Architecture

## Network Core

- **NanoPi R5S** — main router, OpenWrt 24.10.4
- **Xiaomi BE7000** — Wi-Fi access point and Layer-2 switch
- **LAN** — `192.168.2.0/24`
- **Dual ISP** — `wan` and `wanb`, managed by mwan3

## VPN and Remote Access

- `wg_ams` — Amsterdam Internet VPN, subnet `10.30.0.0/24`
- `wg_transit` — remote-access transit, subnet `10.101.0.0/24`
- `wg_home` — Home VPN overlay, subnet `10.100.0.0/24`

Current routing policy:

- local and service networks follow dedicated policies;
- Russian IPv4 prefixes go directly through WAN;
- other Internet traffic goes through Amsterdam (`wg_ams`);
- remote clients preserve access to the local network and use the same WAN/VPN logic;
- Singapore VPN is deprecated and is not part of the target architecture.

## Storage and Services

Planned and developing components:

- NAS
- Docker services
- Jellyfin
- Immich
- Vaultwarden
- Nextcloud
- monitoring and backups
- Home Assistant

---

# 📚 Documentation

## Core

- [Architecture](docs/Architecture.md)
- [Network](docs/Network.md)
- [VPN Architecture](docs/VPN.md)
- [NanoPi Current Configuration](docs/NanoPi_Current_Configuration.md)
- [Backup and Restore](docs/Backup_and_Restore.md)
- [Smart Routing Project](docs/Smart_Routing_Project.md)
- [IPTV on NanoPi R5S](docs/IPTV.md)
- [Hardware](docs/Hardware.md)
- [Services](docs/Services.md)

---

# 🚀 Roadmap

- stabilize and document the current NanoPi configuration;
- remove deprecated Singapore VPN artifacts after a safe review;
- implement HomeLab Smart Routing in monitor-only mode;
- add VLAN segmentation and IoT isolation;
- deploy NAS and backup workflows;
- add network and service monitoring;
- evaluate IPv6 only after IPv4 architecture is fully stable.

---

# 📌 Project Status

🟢 NanoPi routing and remote access restored

🟢 Amsterdam Internet VPN operational

🟢 Russian-prefix split routing operational

🟢 Full NanoPi recovery point created

🟡 Smart Routing — design complete, implementation pending

🟡 Documentation modernization

⚪ NAS

⚪ Smart Home

---

Last update: 2026-08-01
