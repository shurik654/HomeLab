# HomeLab Smart Routing — NanoPi R5S Project

Version: 1.0  
Status: design complete, implementation not started  
Updated: 2026-08-01

## Goal

Replace the coarse model `RU → WAN, other destinations → VPN` with a lightweight adaptive mechanism that learns whether a service works better through WAN or Amsterdam VPN and remembers the decision.

This is not an AI platform. It is a small, auditable home-network routing service.

---

# Problem

Modern applications use distributed APIs, CDN, authentication, analytics and QUIC endpoints. A single application may therefore leave through multiple external IP addresses.

Observed examples:

- Kinopoisk detects VPN on Wi-Fi;
- Meta Horizon does not load over the current split-routing path;
- the same applications work over the phone mobile network;
- the issue existed before the latest Amsterdam rebuild.

Country registration of an IP address is not enough to decide the correct route.

---

# Core Principle

```text
unknown service
      │
      ▼
use current baseline route
      │
      ▼
observe repeated success/failure
      │
      ▼
probe WAN and wg_ams separately
      │
      ▼
store preferred route and confidence
      │
      ▼
apply to future connections
```

The engine must never change route after one isolated failure.

---

# Technical Limitation

A router cannot reliably replay an arbitrary client TCP/TLS/QUIC request over another route after the original connection has failed.

The realistic model is:

- observe DNS and connection outcomes;
- perform independent controlled probes;
- learn a decision;
- apply that decision to subsequent connections;
- ask the user to reopen the application when testing a changed route.

---

# Target Architecture

```text
LAN and remote clients
        │
        ▼
DNS Observer on NanoPi
        │
        ▼
Smart Routing Engine
 ├─ service database
 ├─ WAN probe
 ├─ wg_ams probe
 ├─ confidence scoring
 └─ decision expiry
        │
        ▼
nftables sets
 ├─ route_wan_v4
 └─ route_vpn_v4
        │
        ▼
PBR → WAN / wg_ams
```

---

# Components

## DNS Observer

- reads domains actually requested through NanoPi dnsmasq;
- stores `domain → IPv4 addresses → TTL`;
- does not decrypt HTTPS;
- does not record credentials, cookies or request bodies.

## Probe Engine

Controlled checks through WAN and `wg_ams`:

- DNS resolution;
- TCP connect to port 443;
- TLS handshake with certificate validation;
- limited HTTP check where safe;
- latency and timeout recording;
- optional reachability check for UDP/443.

## Decision Store

SQLite fields:

```text
service
route
confidence
wan_success
vpn_success
failures
last_seen
last_checked
locked
reason
```

## Route Compiler

- compiles high-confidence decisions into nftables sets;
- respects DNS TTL;
- applies changes atomically;
- keeps the current baseline PBR as fallback.

---

# Decision Rules

1. A manually locked decision always wins.
2. A fresh high-confidence decision is reused.
3. Unknown services start on the current baseline route.
4. At least three observations are required before testing a change.
5. Three consecutive failures trigger an alternative-route probe.
6. The alternative must succeed at least two times out of three.
7. Automatic application requires confidence of at least 0.80.
8. Old decisions are re-evaluated after seven days.
9. If both routes fail, keep status `unknown` and do not modify routing.

Initial values are deliberately conservative and may be adjusted after monitor-only testing.

---

# Baseline Routing

Until Smart Routing learns a service:

- local and service networks use existing dedicated policies;
- Russian IPv4 prefixes use WAN;
- remaining Internet traffic uses `wg_ams`;
- remote clients use the same logic through `wg_transit`.

Smart Routing decisions must have higher priority than the broad RU and world rules.

---

# Implementation Phases

## Phase 0 — Protect the Stable Network

- verify both NanoPi backups;
- do not change current PBR/firewall/WireGuard;
- create `/opt/homelab-smart-routing`;
- define a one-command rollback.

## Phase 1 — Monitor Only

- collect DNS observations;
- populate SQLite;
- measure resource usage;
- do not create nftables sets;
- rotate logs daily.

## Phase 2 — Active Probes

- add rate-limited WAN and `wg_ams` checks;
- store results only;
- no automatic routing changes.

## Phase 3 — Recommendations

- show proposed route and confidence;
- require manual confirmation;
- test only one selected device.

## Phase 4 — Conservative Automation

- apply only high-confidence decisions;
- atomically replace nftables sets;
- preserve immediate rollback;
- never touch WireGuard endpoint routes or remote-access infrastructure.

---

# Planned Directory Layout

```text
/opt/homelab-smart-routing/
├── bin/
│   ├── homelab-route
│   ├── observer
│   ├── probe
│   └── compiler
├── config/
│   └── config.json
├── data/
│   └── decisions.db
├── state/
│   ├── route_wan_v4.txt
│   └── route_vpn_v4.txt
├── logs/
│   └── smart-routing.log
├── init/
│   └── homelab-smart-routing
└── README.md
```

---

# Planned CLI

```text
homelab-route status
homelab-route list
homelab-route show <service>
homelab-route lock <service> wan|vpn
homelab-route unlock <service>
homelab-route forget <service>
homelab-route monitor on|off
homelab-route apply
homelab-route rollback
```

---

# Safety Requirements

- no private keys in code, logs or GitHub;
- no full URL, token, cookie or HTTPS payload logging;
- daily log rotation and bounded retention;
- SQLite integrity checks;
- if the service fails, baseline PBR continues to work;
- changes are atomic;
- remote access and VPS endpoint routes are protected;
- one failure never changes routing.

---

# Acceptance Criteria

- disabling Smart Routing leaves the current network unchanged;
- monitor-only mode has low CPU and storage overhead;
- decisions persist across reboot;
- LAN and remote clients share the same decision database;
- manual lock is respected;
- rollback restores baseline behavior immediately;
- Kinopoisk and Meta Horizon can be assigned a consistent path after evidence is collected.

---

# Out of Scope for v1

- LLM or AI analysis;
- TLS interception;
- NFQUEUE proxying of all traffic;
- seamless replay of failed application connections;
- multiple VPN endpoint selection;
- IPv6 automation;
- web dashboard.

---

# Start Message for a New Chat

```text
Continue the HomeLab project on NanoPi R5S. The current network is stable and backed up. Do not modify active PBR, firewall, wg_ams, wg_transit or remote access without explicit approval and rollback. Implement HomeLab Smart Routing from Phase 0 and Phase 1 only: monitor-only DNS Observer and SQLite Decision Store. Give one short step at a time, specify the execution host, avoid large dumps and do not enable automatic routing changes.
```
