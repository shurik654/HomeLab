# A133 BootROM Research

## SoC

Allwinner A133

## FEL Access

Confirmed:

- USB FEL access available
- VID/PID detected
- sunxi-fel communication works

## Memory

Important addresses:

- SPL address: 0x20000
- Scratch address: 0x21000
- Thunk address: 0x40400

## AW_FEL_EXEC

Tests:

### ARM32

Status:

SUCCESS

Executed custom ARM32 code from SRAM.

Confirmed:

- code execution;
- memory write;
- return to FEL.

### AArch64

Status:

FAILED

Execution caused FEL disconnect.

Conclusion:

BootROM execution environment currently confirmed as ARM32.

## eGON Header

Research completed:

- boot_file_head structure analyzed;
- header size confirmed: 96 bytes;
- b_instruction formula verified;
- checksum algorithm researched.
