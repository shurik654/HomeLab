# A133 Stage-0 Boot Monitor

## Goal

Create minimal monitor running from SRAM via FEL.

Purpose:

- execute commands;
- read/write memory;
- communicate with host tool.

## Current Status

Implemented:

- ARM32 startup;
- linker script;
- SRAM execution;
- scratchpad communication.

## Host Tool

Created:

- host.py
- protocol.py
- fel.py

Protocol:

BMP (Boot Monitor Protocol)

## Experiments

### Experiment 1

Minimal infinite loop.

Result:

Executed successfully.

### Experiment 2

Return to BootROM.

Result:

FEL remained operational.

### Experiment 3

Memory write test.

Result:

Successful.
