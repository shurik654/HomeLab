# A133 AI Box — Decisions

Version: 1.0

---

# ADR-001 — Use FEL as initial development interface

Decision:

Use Allwinner FEL BootROM interface as the first development and recovery channel.

Reason:

- no UART access required;
- no hardware modification required;
- allows safe experiments;
- original firmware remains untouched.

---

# ADR-002 — Do not modify eMMC initially

Decision:

Avoid writing to internal eMMC during early research.

Reason:

- device recovery remains possible;
- boot chain can be studied safely;
- experiments are performed in RAM first.

---

# ADR-003 — Stage-0 before U-Boot

Decision:

Develop minimal Stage-0 monitor before attempting full U-Boot boot.

Reason:

Stage-0 provides:

- code execution testing;
- memory access;
- communication protocol;
- controlled transition to next boot stage.

---

# ADR-004 — ARM32 execution path selected

Decision:

Use ARM32 execution for initial BootROM experiments.

Reason:

Confirmed:

- AW_FEL_EXEC works;
- ARM32 code executes correctly;
- return to FEL is possible.

AArch64 execution requires additional investigation.

---

# ADR-005 — Main software stack

Decision:

Target open-source stack:

- U-Boot;
- Linux kernel;
- Buildroot.

Reason:

Provides:

- full control;
- reproducible builds;
- long-term maintainability.

---

# ADR-006 — Documentation first

Decision:

All important experiments and architecture decisions are documented in GitHub.

Reason:

- preserve project history;
- avoid losing knowledge;
- simplify continuation in future chats.
