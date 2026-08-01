# Changelog

All significant HomeLab architecture and documentation changes are recorded here.

## 2026-08-01

### Changed

- Updated repository overview for the current NanoPi R5S architecture.
- Replaced the planned multi-VPN Internet design with one Amsterdam Internet VPN.
- Documented `wg_ams`, `wg_transit` and `wg_home` roles.
- Documented split routing: Russian IPv4 prefixes through WAN, other Internet traffic through Amsterdam.
- Documented remote-client routing through `wg_transit` and required `transit → vpn_ams` forwarding.
- Marked Singapore `wg_sg` as deprecated.
- Updated Architecture and Network documentation.

### Added

- VPN architecture guide.
- NanoPi current configuration guide.
- Backup and restore guide.
- HomeLab Smart Routing project.
- Codex implementation task.

### Fixed

- Recorded removal of the conflicting old rule that sent non-Russian LAN traffic to PBR mark `0x030000` (`wg_home`) instead of Amsterdam.
- Recorded the missing `transit → vpn_ams` firewall forwarding that prevented foreign Internet access for remote clients.

### Recovery

- Full technical NanoPi configuration archive created.
- Native OpenWrt backup created.
- Copies saved outside NanoPi.
