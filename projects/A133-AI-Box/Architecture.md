# A133 AI Box Architecture

Status:

Experimental architecture.

Current stage:

Stage-0 development.

## Overview

The project transforms an Allwinner A133 AI Box into an open Linux platform.

Current boot architecture:

BootROM
|
v
FEL Mode
|
v
Stage-0 Boot Monitor
|
v
U-Boot
|
v
Linux Kernel
|
v
Buildroot RootFS


---

## Boot Stages

### 1. BootROM

Responsibilities:

- initial hardware startup;
- FEL USB mode;
- loading first-stage code.

Status:

Research completed.

---

### 2. Stage-0 Boot Monitor

Purpose:

Minimal custom ARM32 monitor executed through AW_FEL_EXEC.

Responsibilities:

- controlled execution;
- memory access;
- host communication;
- hardware experiments.

Status:

Experimental prototype.

---

### 3. U-Boot

Purpose:

Main bootloader.

Responsibilities:

- DRAM initialization;
- peripheral setup;
- Linux loading.

Status:

Planned.

---

### 4. Linux

Purpose:

Main operating system.

Responsibilities:

- drivers;
- networking;
- applications.

Status:

Research phase.

---

### 5. Buildroot

Purpose:

Generate embedded Linux root filesystem.

Responsibilities:

- BusyBox;
- services;
- custom packages.

Status:

Planned.

---

## Development Flow

Current development order:

1. BootROM research
2. FEL experiments
3. Stage-0 development
4. U-Boot integration
5. Linux boot
6. Application layer
