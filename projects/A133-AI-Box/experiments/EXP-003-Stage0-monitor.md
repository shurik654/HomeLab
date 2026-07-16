# EXP-003 Stage-0 Monitor

## Goal

Create a minimal monitor running from SRAM.

## Address

Load address:

0x00020000

## Components

Host:

- host.py
- protocol.py
- fel.py

Target:

- Stage-0 firmware

## Tests

Implemented:

- SRAM execution;
- memory access;
- command protocol.

## Result

SUCCESS

Confirmed:

- custom monitor execution;
- communication concept.

## Next

Develop full Boot Monitor Protocol.
