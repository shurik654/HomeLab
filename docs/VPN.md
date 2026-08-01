# 🔐 HomeLab VPN Architecture

Version: 3.0  
Updated: 2026-08-01

## Target state

HomeLab uses one Internet VPN endpoint in Amsterdam. Remote access is independent and reaches NanoPi through the Russia VPS transit path.

```text
LAN / Wi-Fi ───────────────┐
                           │
Remote client → Russia VPS │
              → wg_transit ├→ NanoPi R5S
                           │      ├→ WAN for Russian destinations
                           │      └→ wg_ams for other Internet traffic
                           │
Local Home VPN → wg_home ──┘
```

## Interfaces

### `wg_ams`

- role: Internet egress;
- peer: Amsterdam VPS;
- tunnel subnet: `10.30.0.0/24`;
- NanoPi address: `10.30.0.2/24`;
- peer endpoint uses UDP 51820;
- peer `AllowedIPs`: `0.0.0.0/0`;
- `route_allowed_ips=0` so PBR controls routing.

### `wg_transit`

- role: remote-access transit through Russia VPS;
- subnet: `10.101.0.0/24`;
- NanoPi address: `10.101.0.2/24`;
- actual remote client traffic arrives through this interface.

### `wg_home`

- role: Home VPN overlay;
- subnet: `10.100.0.0/24`;
- remains separate from the Internet VPN.

### `wg_sg`

Singapore experiment. Deprecated. It is not part of the target architecture and must not receive new policies.

---

# PBR Policies

Required logical policies:

| Policy | Source / destination | Interface |
|---|---|---|
| `HOME_WG` | destination `10.100.0.0/24` | `wg_home` |
| `TRANSIT_WG` | destination `10.101.0.0/24` | `wg_transit` |
| `LAN_TO_TRANSIT` | LAN to `10.101.0.0/24` | `wg_transit` |
| `LAN_WORLD` | source `192.168.2.0/24` | `wg_ams` |
| `TRANSIT_WORLD` | source `10.101.0.0/24` | `wg_ams` |

Specific local policies must be evaluated before broad world policies.

The Russian-prefix include inserts WAN decisions before the broad world rules.

---

# Firewall

Required Amsterdam zone:

```text
name: vpn_ams
network: wg_ams
input: REJECT
output: ACCEPT
forward: REJECT
masq: 1
mtu_fix: 1
```

Required forwarding:

- `lan → vpn_ams`;
- `transit → vpn_ams`.

Without `transit → vpn_ams`, remote users can reach local devices and Russian sites but cannot reach foreign sites.

---

# Validation

## NanoPi tunnel

```sh
wg show wg_ams
ping -I wg_ams -c 3 10.30.0.1
curl --interface wg_ams -4 https://ipinfo.io/ip
```

Expected Amsterdam public IP: `178.236.247.247`.

## Home client

- Russian site opens directly;
- YouTube or another foreign site opens;
- public IP test for a foreign destination shows Amsterdam egress.

## Remote client

- local HomeLab device is reachable;
- Russian site opens;
- foreign site opens through Amsterdam.

---

# Security

Never commit WireGuard private keys or preshared keys to GitHub. Documentation may contain only public architecture data and placeholders.
