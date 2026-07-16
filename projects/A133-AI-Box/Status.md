# A133 AI Box — Status

Current project status.

---

## Overall Status

Stage-0 Boot Monitor development.

Project is in experimental bring-up phase.

---

## Completed

### BootROM / FEL

- FEL access confirmed;
- BootROM behavior researched;
- AW_FEL_EXEC execution confirmed;
- ARM32 code execution verified.

### Stage-0

- SRAM execution confirmed;
- minimal ARM32 monitor prototype created;
- scratchpad communication tested;
- FEL return behavior verified.

### Documentation

- architecture documented;
- decisions recorded;
- experiment logs created.

---

## Current Work

### Stage-0 Boot Monitor

In progress:

- command parser;
- memory read/write commands;
- host communication protocol;
- BMP packet handling.

---

## Next Steps

1. Complete Stage-0 monitor.
2. Implement HostTool communication.
3. Research DRAM initialization.
4. Prepare U-Boot handoff.
5. Boot Linux.

---

## Software Stack

Planned:

BootROM
|
FEL Mode
|
Stage-0 Boot Monitor
|
U-Boot
|
Linux Kernel
|
Buildroot RootFS


---

## Last Update

2026-07-14
