# 🚗 A133 AI Box

Project to transform Allwinner A133 AI Box into an open Linux platform.

## Goal

Research and replace the original software stack with:

- U-Boot
- Linux
- Buildroot

## Hardware

SoC:

- Allwinner A133

Storage:

- eMMC 32GB

RAM:

- 2GB

## Research

Completed:

- FEL access confirmed
- BootROM research
- eGON header analysis
- ARM32 execution via AW_FEL_EXEC
- Stage-0 Boot Monitor experiments
  
## Future

Planned development:

- Linux boot;
- custom firmware;
- device role selection:

  - VPN gateway;
  - TV Box;
  - IoT platform.

  ## Architecture

System architecture:

- [A133 Architecture](Architecture.md)
  
## Decisions

Architecture decisions:

- [ADR-002 — A133 Boot Architecture](../../decisions/ADR-002-A133-boot-architecture.md)
  
## Experiments

Research log:

- [EXP-001 ARM32 execution](experiments/EXP-001-ARM32-execution.md)
- [EXP-002 AArch64 test](experiments/EXP-002-AArch64-test.md)
- [EXP-003 Stage0 monitor](experiments/EXP-003-Stage0-monitor.md)
