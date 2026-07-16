# Stage-0 Monitor Development

Date:

2026-07-14

---

## Goal

Develop a minimal Stage-0 monitor for Allwinner A133 BootROM FEL environment.

The goal is to execute custom code through AW_FEL_EXEC and maintain communication with the host.

---

## Background

Initial experiments showed that code execution was possible, but simple programs that entered an infinite loop caused FEL communication loss.

Problem:

- code executed successfully;
- BootROM transferred control;
- host communication stopped.

A different approach was required.

---

## Stage-0 Concept

Stage-0 is a minimal runtime loaded into SRAM.

Responsibilities:

- receive commands from host;
- execute memory operations;
- return results;
- preserve FEL communication.

Architecture:
Host PC
|
| USB FEL
|
BootROM
|
AW_FEL_EXEC
|
Stage-0 Monitor
|
BMP protocol

---

## Experiments

### Test 1 — Infinite execution

Result:

❌ Failed

Observation:

- execution started;
- FEL connection became unavailable.

Conclusion:

A permanent loop is not suitable for interactive development.

---

### Test 2 — Return to BootROM

Result:

✅ Success

Observation:

- ARM32 code executed;
- memory operation completed;
- control returned;
- FEL remained available.

---

## Host Communication

A custom Boot Monitor Protocol (BMP) was designed.

Purpose:

- send commands;
- execute operations;
- receive results.

---

## Current Status

Completed:

- ARM32 execution confirmed;
- SRAM execution confirmed;
- Stage-0 concept validated;
- Host communication design started.

---

## Next Steps

- improve BMP protocol;
- automate loading process;
- expand memory operations;
- prepare U-Boot integration.
