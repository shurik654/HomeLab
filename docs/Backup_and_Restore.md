# NanoPi R5S — Backup and Restore

Version: 1.0  
Updated: 2026-08-01

## Purpose

Create a recoverable snapshot of the working NanoPi configuration before further network changes.

---

# Technical Backup

Run on NanoPi R5S.

```sh
mkdir -p /root/NanoPi_Backup_2026-07-31
cp -a /etc/config /root/NanoPi_Backup_2026-07-31/
cp -a /etc/wireguard /root/NanoPi_Backup_2026-07-31/ 2>/dev/null
cp -a /usr/share/pbr /root/NanoPi_Backup_2026-07-31/
ip rule > /root/NanoPi_Backup_2026-07-31/ip_rule.txt
ip route show table all > /root/NanoPi_Backup_2026-07-31/ip_route_all.txt
wg show > /root/NanoPi_Backup_2026-07-31/wg_show.txt
uci export > /root/NanoPi_Backup_2026-07-31/uci_export.txt
tar -czf /root/NanoPi_Backup_2026-07-31.tar.gz -C /root NanoPi_Backup_2026-07-31
ls -lh /root/NanoPi_Backup_2026-07-31.tar.gz
```

This archive may contain secrets. Never commit it to GitHub.

---

# Native OpenWrt Backup

```sh
sysupgrade -b /root/openwrt-backup-2026-07-31.tar.gz
ls -lh /root/openwrt-backup-2026-07-31.tar.gz
```

---

# Download to Windows

OpenWrt may not include an SFTP server. Modern Windows `scp` uses SFTP by default, so force the legacy SCP protocol with `-O`.

Run in Windows PowerShell:

```powershell
scp -O root@192.168.2.1:/root/NanoPi_Backup_2026-07-31.tar.gz "$env:USERPROFILE\Downloads\"
scp -O root@192.168.2.1:/root/openwrt-backup-2026-07-31.tar.gz "$env:USERPROFILE\Downloads\"
```

Files will appear in:

```text
%USERPROFILE%\Downloads
```

---

# Storage Policy

Keep at least two external copies:

- main PC;
- separate disk or encrypted cloud storage;
- future NAS backup repository.

Do not keep the only backup on NanoPi.

---

# Restore Strategy

## Native OpenWrt restore

Use LuCI backup restore or the supported `sysupgrade` restore workflow for the same compatible OpenWrt build.

## Manual technical restore

Use the technical archive only for controlled reconstruction and comparison. Do not overwrite all files blindly on a different OpenWrt version.

Recommended order:

1. restore `/etc/config/network`;
2. restore `/etc/config/firewall`;
3. restore `/etc/config/pbr`;
4. restore the validated PBR include;
5. restart network components one at a time;
6. verify WireGuard endpoint reachability before enabling broad Internet policies;
7. test LAN and remote access after every major step.

---

# Validation After Restore

```sh
wg show wg_ams
wg show wg_transit
uci show pbr | grep name
uci show firewall | grep -E "vpn_ams|transit_to_vpn_ams"
logread | grep pbr | tail -20
```

Functional tests:

- LAN access;
- Russian Internet destination;
- foreign Internet destination;
- remote access to local devices;
- foreign Internet access from remote client.

---

# Security Warning

The backup contains private WireGuard keys, preshared keys and potentially provider data. Treat it as a secret credential archive.
