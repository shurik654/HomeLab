# 🌐 HomeLab 2026 — Network

Version: 1.0

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

---

# Future

Planned:

- VLAN segmentation
- IPv6
- IoT isolation
- Network monitoring
