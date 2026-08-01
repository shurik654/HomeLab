# Codex Task — HomeLab Documentation and Smart Routing

Date: 2026-08-01

## Repository

`shurik654/HomeLab`

## Objective

Continue development of the HomeLab repository using the current NanoPi R5S architecture as the source of truth.

## Current stable architecture

- NanoPi R5S with OpenWrt 24.10.4;
- LAN `192.168.2.0/24`;
- dual WAN managed by mwan3;
- Amsterdam Internet VPN on `wg_ams`, subnet `10.30.0.0/24`;
- remote access through `wg_transit`, subnet `10.101.0.0/24`;
- Home overlay on `wg_home`, subnet `10.100.0.0/24`;
- Russian IPv4 prefixes go to WAN;
- remaining Internet traffic goes to `wg_ams`;
- remote clients preserve LAN access and use the same Internet routing logic;
- Singapore `wg_sg` is deprecated.

## Safety constraints

- do not include WireGuard private or preshared keys;
- do not change the live router automatically;
- do not remove deprecated configuration until a reviewed rollback exists;
- preserve IPTV policy ordering;
- keep remote access independent from Internet VPN experiments;
- all commands must identify the execution host;
- one change and one validation step at a time.

## Documentation tasks

1. Keep `README.md` synchronized with the current architecture.
2. Maintain:
   - `docs/Architecture.md`
   - `docs/Network.md`
   - `docs/VPN.md`
   - `docs/NanoPi_Current_Configuration.md`
   - `docs/Backup_and_Restore.md`
   - `docs/Smart_Routing_Project.md`
3. Add diagrams in Mermaid only when they improve clarity.
4. Keep secrets represented by placeholders.
5. Record significant changes in `CHANGELOG.md`.

## Smart Routing implementation plan

Implement in phases only.

### Phase 0

- create project skeleton;
- configuration schema;
- rollback plan;
- no active route changes.

### Phase 1

- monitor-only DNS observer;
- SQLite decision store;
- bounded logging and daily rotation;
- status CLI;
- no nftables updates.

### Phase 2

- controlled WAN and `wg_ams` probes;
- conservative confidence scoring;
- recommendation output only.

Do not implement automatic routing until monitor-only behavior is validated on NanoPi.

## Acceptance criteria

- documentation reflects the actual stable architecture;
- no secrets are committed;
- baseline routing remains unchanged;
- Smart Routing can be disabled without affecting PBR;
- code is suitable for OpenWrt resource limits;
- logs are concise;
- rollback is documented and tested conceptually before deployment.
