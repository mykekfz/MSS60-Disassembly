# MSS60 V762 – A2L definitions and Ghidra analysis

Calibration definitions and reverse-engineering material for the Siemens/BMW **MSS60** engine control unit (S65 V8), production software **V762.01-B500.02**, calibration **7848136** (ZB 241E).

Created by ZiMMIe for the M5Board and Nam3Forum communities.. The goal is to make this information freely available, encourage collaboration, and let others learn from it, use it and add their own knowledge.

## Contents

| File | Description |
|---|---|
| `MSS60_V762_INJ.a2l` | ASAM MCD-2MC (A2L 1.60) description for the INJ (injection) CPU – characteristics and RAM measurements |
| `MSS60_V762_IGN.a2l` | Same for the IGN (ignition) CPU |
| `INJ.gzf`, `IGN.gzf` | Ghidra packed programs (one per CPU), fully analysed and labelled |
| `INJ.c`, `IGN.c` | Decompiled C export of both programs from Ghidra, with calibration and RAM names applied |
| `INJ_Func`, `IGN_Func` | Function lists (name, address, signature, size) exported from Ghidra (CSV) |

## ECU layout

The MSS60 has two MPC563 CPUs (INJ and IGN), each with its own program and calibration.

| | INJ | IGN |
|---|---|---|
| Calibration (CPU address) | 0x70000 – 0x7FFFF | 0x70000 – 0x7FFFF |
| Offset in a 128 KB calibration file | 0x00000 – 0x0FFFF | 0x10000 – 0x1FFFF |
| Offset in a full 5 MB read | 0x000000 | 0x280000 |
| Small-data base (r2) | 0x78280 | 0x78280 |

Data is big-endian. Tables use the Bosch layout: header `[nx][ny]`, X axis, Y axis, then values. Group curves and maps share axes (count + axis points).

## Using the A2L files

- Every long identifier ends with a status tag:
  - `[verified]` – placement and meaning checked against the program code
  - `[A2L-ref]` – placed from an earlier software version's A2L, not yet proven in V762 code
  - `[EST]` – estimated placement; check before relying on it
  - `[unassigned]` – read by the code but not yet identified (`UNK_*` names)
- Measurements (RAM variables) use names transferred from an earlier software version to the V762 addresses; the long identifier states how confident each one is.
- Entries described as "not read by any V762 code" have no effect when changed.

## Using the Ghidra files

1. Create or open a Ghidra project and use **File → Import File** on `INJ.gzf` / `IGN.gzf`.
2. Both programs are big-endian PowerPC 32-bit with the image at base address 0x0 and r2 set to 0x78280.
3. Calibration and RAM labels are already applied, so the decompiler shows names such as `KF_TZ_GRUND` and `tmot` directly. Ghidra prefixes some globals with `_` (for example `_n`) where symbols overlap.
4. Some functions are reached only through pointer tables. Their real entry point can sit one or two instructions before the `stwu`, because the compiler hoists the first instruction (for example `lis r12,7`) ahead of the stack frame.

## Background

The reference material is based on publicly available beta program software. Ghidra was used to disassemble and analyse the current V762 program. Placements were checked function by function against the code; where that was not possible, the entry is marked accordingly.

## Contributing

This is a community effort, and every contribution matters. Corrections, additional findings, technical knowledge and general feedback are all very welcome – open an issue or a pull request. When reporting a correction, please include the function address and the code that shows it.

## Disclaimer

Use at your own risk. Changing ECU calibration can damage the engine, affect emissions compliance and road legality. Always keep a backup of the original read.

Completely free to use and share.
