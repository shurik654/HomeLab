# EXP-002 AArch64 Execution Test

## Goal

Check whether Allwinner A133 FEL execution supports AArch64 code.

## Environment

Target:

- Allwinner A133

Loader:

- sunxi-fel.exe

Address:

- 0x30000

## Test

Loaded AArch64 code:

- write value to memory;
- enter infinite loop.

## Result

FAILED

Observed:

- FEL connection lost;
- usb_bulk_send() ERROR -7.

## Conclusion

Current BootROM execution path confirmed only for ARM32.

Further work:

- continue ARM32 Stage-0 development.
