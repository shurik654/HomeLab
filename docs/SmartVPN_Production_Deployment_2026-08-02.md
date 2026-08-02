# HomeLab SmartVPN Production Deployment

**Date:** 2026-08-02

## Status

Production deployment completed successfully.

## Objective

Move HomeLab routing control to SmartVPN without changing the working VPN architecture.

## Final Routing Model

```
RU destinations
    |
    +--> WAN (direct)

WORLD destinations
    |
    +--> wg_ams (Amsterdam WireGuard)

Home VPN
    |
    +--> wg_home

Transit
    |
    +--> wg_transit

IPTV
    |
    +--> wanb
```

## Migration Completed

Previous test model:

```
youtube.com -> wg_ams
google.com  -> wg_ams
openai.com  -> wg_ams
```

was removed.

New model:

```
Category based routing:

RU    -> WAN
WORLD -> wg_ams
```

## SmartVPN Pipeline

```
DNS Observer
      |
      v
Domain Feed
      |
      v
Decision Engine
      |
      v
Policy Preview
      |
      v
Builder
      |
      v
Audit
      |
      v
Apply Engine
      |
      v
/etc/config/pbr
```

## Apply Result

```
[ OK ] Policy compilation completed
[ OK ] Builder completed
[ OK ] AUDIT PASSED
[ OK ] Backup created
[ OK ] Configuration installed
[ OK ] Verification successful

Status : SUCCESS
```

## Verified Components

- wg_ams handshake active
- PBR service operational
- firewall4 integration active
- existing HOME_WG preserved
- TRANSIT_WG preserved
- IPTV routing preserved
- RU include preserved

## Backup

Rollback created during deployment:

```
/HomeLab/SmartVPN/rollback/2026-08-02_163151
```

## Production PBR Rules

Active policy groups:

- HOME_WG
- TRANSIT_WG
- HOMEVPN_CLIENTS
- LAN_TO_TRANSIT
- IPTV_BEELINE
- LAN_WORLD
- TRANSIT_WORLD

## Notes

Automatic VPN selection was intentionally not enabled.
The current stable production architecture uses fixed routing:

- Russian resources: direct WAN
- World resources: Amsterdam VPN

Future work:

- SmartVPN health monitoring improvements
- optional automatic VPN decision engine
- additional documentation and recovery procedures
