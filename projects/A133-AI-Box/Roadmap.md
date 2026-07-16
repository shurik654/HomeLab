# A133 AI Box — Roadmap

Version: 1.0

---

# Goal

Transform Allwinner A133 AI Box into an open Linux platform.

The project focuses on replacing the original software stack with a fully controlled system.

---

# Phase 1 — Research

Status: ✅ Completed

Tasks:

- Identify hardware platform;
- Analyze BootROM behavior;
- Confirm FEL access;
- Analyze eGON image format;
- Test ARM32 execution;
- Develop initial Stage-0 monitor.

Result:

A133 BootROM execution path is understood.

---

# Phase 2 — Bootloader

Status: 🟡 In progress

Tasks:

- Improve Stage-0 monitor;
- Create reliable FEL communication;
- Prepare SPL image;
- Build U-Boot for A133;
- Test DRAM initialization.

Goal:

Boot custom software without original firmware.

---

# Phase 3 — Linux Platform

Status: Planned

Tasks:

- Build Linux kernel;
- Create Device Tree;
- Prepare Buildroot root filesystem;
- Enable networking;
- Enable storage.

Goal:

Boot minimal Linux system.

---

# Phase 4 — Final Device Role

Status: Open

Possible roles:

- VPN gateway;
- TV Box;
- HomeLab node;
- IoT controller.

Decision after Linux platform is stable.

---

# Current Status

Current achievement:

- FEL access confirmed;
- ARM32 execution confirmed;
- Stage-0 monitor prototype created;
- Host tools started.

Current focus:

A133 boot chain research.
