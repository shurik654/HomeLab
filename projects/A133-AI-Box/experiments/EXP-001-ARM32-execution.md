# EXP-001 ARM32 Execution Test

Date:

2026-07

## Goal

Verify that Allwinner A133 BootROM can execute custom ARM32 code through FEL.

## Environment

Host:

- Windows
- sunxi-fel.exe

Target:

- Allwinner A133 AI Box

## Procedure

1. Load binary into SRAM:

Address:

0x00020000

2. Execute using:

AW_FEL_EXEC

## Test Code

Minimal ARM32 program.

Actions:

- write value to scratchpad memory;
- return to BootROM.

## Result

SUCCESS

Confirmed:

- ARM32 code execution;
- SRAM execution;
- FEL recovery after return.

## Conclusion

Allwinner A133 BootROM execution path is available.

Next:

- develop Stage-0 monitor;
- create communication protocol.
