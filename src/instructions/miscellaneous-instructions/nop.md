# `NOP`
No Operation

| Instruction | Description                         |
| :---------- | :---------------------------------- |
| `NOP`       | One byte no-operation instruction   |
| `NOP r/m16` | Multi-byte no-operation instruction |
| `NOP r/m32` | Multi-byte no-operation instruction |

## Description
This instruction performs no operation. It is a one-byte or multi-byte NOP that takes up space in the instruction stream but does not impact machine context, except for the EIP register.

The multi-byte form of NOP is available on processors with model encoding:
* `CPUID.01H.EAX[Bytes 11:8]` = `0b0110` or `0b1111`
The multi-byte `NOP` instruction does not alter the content of a register and will not issue a memory operation. The instruction's operation is the same in non-64-bit modes and 64-bit mode.

## Operation
The one-byte `NOP` instruction is an alias mnemonic for the `XCHG (E)AX, (E)AX` instruction.

The multi-byte `NOP` instruction performs no operation on supported processors and generates undefined opcode exception on processors that do not support the multi-byte `NOP` instruction.

The memory operand form of the instruction allows software to create a byte sequence of "no operation" as one instruction. For situations where multiple-byte `NOP`s are needed, the recommended operations (32-bit mode and 64-bit mode) are:

| Length  | Assembly                                       | Byte Sequence                |
| :------ | :--------------------------------------------- | :--------------------------- |
| 2 bytes | `66 nop`                                       | `66 90`                      |
| 3 bytes | `nop DWORD ptr [eax]`                          | `0F 1F 00`                   |
| 4 bytes | `nop DWORD ptr [eax + 0x00]`                   | `0F 1F 40 00`                |
| 5 bytes | `nop DWORD ptr [eax + eax*1 + 0x00]`           | `0F 1F 44 00 00`             |
| 6 bytes | `66 nop DWORD ptr [eax + eax*1 + 0x00]`        | `66 0F 1F 44 00 00`          |
| 7 bytes | `nop DWORD ptr [eax + 0x0000_0000]`            | `0F 1F 80 00 00 00 00`       |
| 8 bytes | `nop DWORD ptr [eax + eax*1 + 0x0000_0000]`    | `0F 1F 84 00 00 00 00 00`    |
| 9 bytes | `66 nop DWORD ptr [eax + eax*1 + 0x0000_0000]` | `66 0F 1F 84 00 00 00 00 00` |

## Flags Affected
None.
