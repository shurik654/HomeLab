# 📺 HomeLab IPTV — NanoPi R5S

Version: 1.0  
Status: **Working / verified**  
Date: 2026-08-01

---

# Purpose

Run Beeline IPTV through NanoPi R5S without affecting:

- Multi-WAN;
- VPN routing;
- remote access;
- normal LAN traffic;
- Xiaomi BE7000 access-point mode.

---

# Physical Topology

```text
Beeline
   │
eth1 / wanb (DHCP)
   │
NanoPi R5S
   │
eth2 / br-lan
   │
Xiaomi BE7000
   │ LAN3
Cisco IPTV receiver
```

NanoPi interfaces:

- `eth0` — MTS / `wan`;
- `eth1` — Beeline / `wanb`;
- `eth2` — LAN / `br-lan`.

Cisco IPTV receiver:

- IP: `192.168.2.238`;
- all unicast traffic is routed through `wanb`;
- multicast is proxied from `wanb` to `lan`.

---

# Confirmed Beeline Design

Analysis of the S1500 router configuration showed:

- WAN type: DHCP;
- DATA, MGNT and IPTV use the same WAN service;
- no separate IPTV WAN was found;
- no separate IPTV VLAN was confirmed;
- IPTV was assigned to LAN1 on the original router with `IPTVLanPortMask=1`.

Therefore, the working NanoPi implementation does **not** use an invented VLAN and does **not** bridge Beeline WAN directly into the home LAN.

---

# Multicast Proxy

`omcproxy` is disabled. `igmpproxy` is used.

`/etc/config/igmpproxy`:

```text
config igmpproxy
        option quickleave '1'

config phyint
        option network 'wanb'
        option zone 'wan'
        option direction 'upstream'
        list altnet '0.0.0.0/0'

config phyint
        option network 'lan'
        option zone 'lan'
        option direction 'downstream'
```

Service state:

```sh
/etc/init.d/omcproxy stop
/etc/init.d/omcproxy disable
/etc/init.d/igmpproxy enable
/etc/init.d/igmpproxy restart
```

`altnet 0.0.0.0/0` is required because Beeline multicast sources were previously rejected as not belonging to an allowed network.

---

# Firewall

The following rules are required:

```text
config rule 'iptv_allow_igmp'
        option name 'IPTV-Allow-IGMP-from-Beeline'
        option src 'wan'
        option proto 'igmp'
        option family 'ipv4'
        option target 'ACCEPT'

config rule 'iptv_allow_multicast'
        option name 'IPTV-Allow-Multicast-to-LAN'
        option src 'wan'
        option dest 'lan'
        option proto 'udp'
        option dest_ip '224.0.0.0/4'
        option family 'ipv4'
        option target 'ACCEPT'
```

These rules allow only IGMP and IPv4 multicast UDP. They do not create unrestricted WAN-to-LAN forwarding.

---

# PBR Policy

The decisive fix was a dedicated PBR policy for the Cisco receiver.

It must be placed **before** `LAN_WORLD`, otherwise the receiver's service traffic is routed through `wg_ams` and the receiver remains on the loading screen.

```text
config policy
        option name 'IPTV_BEELINE'
        option interface 'wanb'
        option src_addr '192.168.2.238/32'
```

Required order:

```text
HOME_WG
TRANSIT_WG
HOMEVPN_CLIENTS
LAN_TO_TRANSIT
IPTV_BEELINE
LAN_WORLD
TRANSIT_WORLD
```

Verified nftables rule:

```text
ip saddr 192.168.2.238 goto pbr_mark_0x020000 comment "IPTV_BEELINE"
```

---

# Working Result

Verified on 2026-08-01:

- Cisco receives a LAN address;
- receiver service traffic exits through Beeline `wanb`;
- IPTV completes startup;
- channels play successfully;
- LAN world traffic continues through `wg_ams`;
- Home VPN and Transit VPN remain unchanged;
- remote access remains operational.

---

# Recovery Checklist

1. Confirm Cisco receiver IP is still `192.168.2.238`.
2. Confirm `igmpproxy` is running.
3. Confirm `omcproxy` is stopped and disabled.
4. Confirm the two IPTV firewall rules exist.
5. Confirm `IPTV_BEELINE` exists before `LAN_WORLD`.
6. Restart PBR and verify:

```sh
/etc/init.d/pbr restart
nft list chain inet fw4 pbr_prerouting 2>/dev/null | grep -E 'IPTV_BEELINE|192\.168\.2\.238'
```

7. Power-cycle the Cisco receiver.

---

# Important Restrictions

- Do not create an unverified IPTV VLAN.
- Do not bridge `eth1` directly to `br-lan`.
- Do not run `igmpproxy` and `omcproxy` simultaneously.
- Do not place `IPTV_BEELINE` after `LAN_WORLD`.
- Do not alter VPN, Transit or RU routing policies while restoring IPTV.
