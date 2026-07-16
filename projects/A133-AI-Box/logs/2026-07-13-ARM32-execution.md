# ARM32 Execution Test

Date:

2026-07-13

---

## Goal

Confirm that Allwinner A133 BootROM FEL interface can execute custom ARM32 code using AW_FEL_EXEC.

---

## Hardware

Device:

- Allwinner A133 AI Box

SoC:

- Allwinner A133

Memory:

- RAM: 2GB
- eMMC: 32GB

---

## Environment

Host:

- Windows
- sunxi-fel.exe

Toolchain:

- arm-none-eabi-gcc 14.2.1

---

## Procedure

1. Load ARM32 binary into SRAM:
0x00020000

2. Execute using:
sunxi-fel execute 0x00020000

3. Test code writes value into scratchpad memory.

---

## Result

Status:

✅ SUCCESS

Observed:

- ARM32 code executed;
- memory write completed;
- FEL communication remained available;
- BootROM returned control.

---

## Conclusion

ARM32 execution through AW_FEL_EXEC is confirmed.

This provides a working foundation for:

- Stage-0 monitor;
- custom bootloader development;
- future U-Boot integration.
