# NanoPi R5S — Current Configuration

Version: 1.0  
Updated: 2026-08-01

> This document describes the current stable design. It intentionally excludes private keys and provider credentials.

## Platform

- Device: NanoPi R5S
- OS: OpenWrt 24.10.4
- LAN: `192.168.2.0/24`
- Main components: mwan3, pbr, firewall4/nftables, WireGuard

---

# Interface Roles

| Interface | Role |
|---|---|
| `wan` | ISP uplink 1 |
| `wanb` | ISP uplink 2 |
| `lan` | Home LAN on `br-lan` |
| `wg_ams` | Amsterdam Internet VPN |
| `wg_transit` | Remote access transit |
| `wg_home` | Home VPN overlay |

---

# Verification Commands

Run on NanoPi R5S.

```sh
uci show network | grep '=interface'
wg show wg_ams
wg show wg_transit
uci show pbr | grep -E "name=|interface=|src_addr=|dest_addr="
uci show firewall | grep -E "vpn_ams|src='transit'|dest='vpn_ams'"
```

Keep output filtered. Do not use full ruleset dumps unless a targeted check is insufficient.

---

# Amsterdam Interface Template

Use placeholders for secrets.

```sh
uci set network.wg_ams='interface'
uci set network.wg_ams.proto='wireguard'
uci set network.wg_ams.private_key='<NANOPI_PRIVATE_KEY>'
uci add_list network.wg_ams.addresses='10.30.0.2/24'
uci set network.wg_ams.mtu='1420'
uci set network.wg_ams.peerdns='0'

uci set network.wg_ams_peer='wireguard_wg_ams'
uci set network.wg_ams_peer.description='Amsterdam HomeLab VPN'
uci set network.wg_ams_peer.public_key='<AMSTERDAM_PUBLIC_KEY>'
uci set network.wg_ams_peer.endpoint_host='<AMSTERDAM_PUBLIC_IP>'
uci set network.wg_ams_peer.endpoint_port='51820'
uci set network.wg_ams_peer.persistent_keepalive='25'
uci set network.wg_ams_peer.route_allowed_ips='0'
uci add_list network.wg_ams_peer.allowed_ips='0.0.0.0/0'

uci commit network
ifup wg_ams
```

Do not apply this block to an already configured router without first comparing the current sections and creating a rollback.

---

# Amsterdam Firewall Zone

```sh
uci set firewall.vpn_ams='zone'
uci set firewall.vpn_ams.name='vpn_ams'
uci add_list firewall.vpn_ams.network='wg_ams'
uci set firewall.vpn_ams.input='REJECT'
uci set firewall.vpn_ams.output='ACCEPT'
uci set firewall.vpn_ams.forward='REJECT'
uci set firewall.vpn_ams.masq='1'
uci set firewall.vpn_ams.mtu_fix='1'

uci set firewall.lan_to_vpn_ams='forwarding'
uci set firewall.lan_to_vpn_ams.src='lan'
uci set firewall.lan_to_vpn_ams.dest='vpn_ams'

uci set firewall.transit_to_vpn_ams='forwarding'
uci set firewall.transit_to_vpn_ams.src='transit'
uci set firewall.transit_to_vpn_ams.dest='vpn_ams'

uci commit firewall
/etc/init.d/firewall restart
```

---

# PBR Policies

The exact anonymous section numbers may vary. Prefer named sections when implementing from scratch.

```sh
uci set pbr.lan_world='policy'
uci set pbr.lan_world.name='LAN_WORLD'
uci set pbr.lan_world.src_addr='192.168.2.0/24'
uci set pbr.lan_world.interface='wg_ams'

uci set pbr.transit_world='policy'
uci set pbr.transit_world.name='TRANSIT_WORLD'
uci set pbr.transit_world.src_addr='10.101.0.0/24'
uci set pbr.transit_world.interface='wg_ams'

uci commit pbr
/etc/init.d/pbr restart
```

Local destination policies must appear before these broad source policies.

---

# Russian Prefix Include

Path:

```text
/usr/share/pbr/pbr.user.ru
```

Responsibilities:

1. download delegated RIPE data;
2. extract `RU` IPv4 allocations;
3. populate `pbr_wan_4_dst_ip_user`;
4. ensure transit traffic to Russian destinations is marked for WAN.

The include must not contain the removed conflicting rule:

```text
192.168.2.0/24 and destination not RU → pbr_mark_0x030000
```

`0x030000` belongs to `wg_home`, not Amsterdam.

---

# Targeted Checks

```sh
wg show wg_ams
ping -I wg_ams -c 3 10.30.0.1
curl --interface wg_ams -4 https://ipinfo.io/ip
nft list chain inet fw4 pbr_prerouting | grep -E "LAN_WORLD|TRANSIT_WORLD|10.101.0.0/24|192.168.2.0/24"
logread | grep pbr | tail -20
```

---

# Functional Acceptance

From a normal LAN client:

- local resources work;
- Russian sites work;
- foreign sites work;
- no separate desktop VPN is running during the test.

From a remote client:

- local resources work;
- Russian sites work;
- foreign sites work.

---

# Known Limitations

- IPv6 PBR is disabled.
- RU-prefix routing is not application-aware.
- Kinopoisk and Meta Horizon expose limitations of country-only routing.
- Smart Routing is designed but not yet deployed.
