# 🏠 HomeLab 2026 — Architecture

Version: 1.0

## Purpose

HomeLab is a private home infrastructure project.

Main goals:

- reliable home network;
- dual ISP failover;
- VPN infrastructure;
- media server;
- family photo cloud;
- monitoring;
- smart home;
- automation.

---

# Current Architecture

## Internet

Two providers:

### ISP 1 — MTS

- GPON ONT C-DATA
- Static IPv4

### ISP 2 — Beeline

- Static IPv4
- IPTV Cisco

---

# Network Core

## NanoPi R5S

Role:

Main router.

Functions:

- OpenWrt
- Multi-WAN
- Firewall
- VPN
- Policy Routing
- QoS
- DNS
- DHCP
- Monitoring

---

# Wi-Fi Network

## Xiaomi BE7000

Role:

- Wi-Fi 7 Access Point
- Layer 2 switch

Ports:

- LAN1 → NanoPi
- LAN2 → HomeLab Server
- LAN3 → Cisco IPTV
- LAN4 → LAB / future devices

---

# Server

Main server:

Gaming PC.

Roles:

- Docker host
- Jellyfin
- Immich
- PostgreSQL
- Monitoring
- Sunshine

---

# Storage

Future:

- LSI SAS HBA
- 4 × HGST 10TB SAS HDD

---

# Future Components

- NAS
- Home Assistant
- HomeLab App
- TV gaming clients
- IPv6
