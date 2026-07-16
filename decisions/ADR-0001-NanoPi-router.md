# ADR-0001 — NanoPi R5S as Main Router

Date: 2026-07

Status: Accepted

---

# Context

HomeLab uses two independent Internet providers:

- MTS
- Beeline

The network requires:

- automatic failover;
- VPN routing;
- firewall control;
- future VLAN support;
- monitoring;
- flexibility.

The Xiaomi BE7000 router is powerful as a wireless device, but its firmware does not provide enough control for the planned architecture.

---

# Decision

Use NanoPi R5S as the main network router.

NanoPi will run OpenWrt and will become the central network gateway.

---

# Responsibilities

NanoPi R5S:

- Multi-WAN
- Firewall
- VPN
- Policy Routing
- QoS
- DHCP
- DNS
- Monitoring
- Future VLAN

---

# Xiaomi BE7000 Role

BE7000 will operate as:

- Wi-Fi 7 Access Point
- Layer 2 switch

Routing functions are disabled.

---

# Benefits

- Independent Wi-Fi hardware replacement.
- Full network control.
- Better security.
- Easier troubleshooting.
- Future expansion.

---

# Future

Possible additions:

- IPv6
- IoT VLAN
- Guest network
- Network monitoring
