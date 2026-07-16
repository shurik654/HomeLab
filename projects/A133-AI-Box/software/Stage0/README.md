A133 Stage-0 Boot Monitor

Purpose:
Minimal ARM32 monitor executed through AW_FEL_EXEC.

Status:
Experimental prototype.

Architecture:
- ARM32
- SRAM execution
- Entry point: 0x00020000
- FEL return supported

Implemented:
- ARM32 execution
- memory write tests
- scratchpad communication
- BMP protocol concept

Next:
- command parser
- memory read/write commands
- host communication
