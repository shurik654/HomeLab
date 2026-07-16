# ADR-002 — A133 Boot Architecture

Status: Accepted

Date: 2026-07-14


## Context

Allwinner A133 AI Box provides FEL access through BootROM.

Initial research confirmed:

- FEL communication works;
- AW_FEL_EXEC works;
- ARM32 code execution is confirmed;
- Stage-0 monitor prototype works.


## Decision

The boot chain will be developed as:

1. BootROM
2. FEL
3. Stage-0 Boot Monitor
4. U-Boot
5. Linux Kernel
6. Buildroot RootFS


## Reasons

Stage-0 provides:

- controlled execution environment;
- hardware experiments;
- memory tests;
- communication protocol development.


U-Boot provides:

- DRAM initialization;
- peripheral setup;
- Linux loading;
- boot management.


Linux provides:

- operating system;
- drivers;
- networking;
- applications.


## Current Status

Implemented:

- FEL access;
- ARM32 execution;
- Stage-0 prototype;
- HostTool architecture.


Pending:

- Stage-0 command parser;
- DRAM initialization research;
- U-Boot port;
- Linux boot.


## Consequences

Positive:

- safe incremental development;
- no dependency on vendor firmware;
- reproducible experiments.


Negative:

- longer development process;
- hardware initialization must be researched manually.
