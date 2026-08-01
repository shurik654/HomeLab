# 🌐 HomeLab 2026 — Network

Version: 1.1

---

# Internet Providers

HomeLab uses two independent ISP connections.

## ISP 1 — MTS

Technology:

- GPON

Equipment:

- ONT C-DATA FD511G-X-APC A1

Features:

- Static IPv4 address

Role:

- Primary Internet connection

---

## ISP 2 — Beeline

Features:

- Static IPv4 address
- IPTV service

Equipment:

- Cisco IPTV receiver

Role:

- Secondary Internet connection
- IPTV provider

---

# Main Router

## NanoPi R5S

Operating system:

- OpenWrt

Role:

Main network gateway.

Functions:

- Multi-WAN
- Failover
- Firewall
- VPN routing
- Policy routing
- QoS
- DHCP
- DNS
- Monitoring
- Beeline IPTV gateway

Physical interfaces:

- `eth0` — MTS / `wan`;
- `eth1` — Beeline / `wanb`;
- `eth2` — LAN / `br-lan`.

---

# IPTV

Beeline IPTV is handled directly by NanoPi R5S.

Working design:

- Cisco receiver IP: `192.168.2.238`;
- receiver unicast traffic is routed through `wanb`;
- multicast is proxied from `wanb` to `lan` by `igmpproxy`;
- `omcproxy` is disabled;
- dedicated firewall rules allow IGMP and multicast UDP;
- PBR policy `IPTV_BEELINE` is placed before `LAN_WORLD`.

Detailed configuration and recovery guide:

[IPTV](IPTV.md)

---

# Wireless Network

## Xiaomi BE7000

Mode:

Access Point + Layer 2 switch

Routing:

Disabled

All routing functions are handled by NanoPi R5S.

---

# BE7000 Port Map

| Port | Device | Purpose |
|---|---|---|
| LAN1 | NanoPi R5S | Main uplink |
| LAN2 | Gaming PC | HomeLab Server |
| LAN3 | Cisco IPTV | IPTV |
| LAN4 | Reserved | LAB / Future devices |

---

# Network Philosophy

- Router functions are separated from Wi-Fi.
- NanoPi is the network brain.
- BE7000 provides wireless connectivity.
- New devices are tested in LAB first.
- IPTV is isolated by routing policy rather than an unverified VLAN.

---

# Future

Planned:

- VLAN segmentation
- IPv6
- IoT isolation
- Network monitoring
