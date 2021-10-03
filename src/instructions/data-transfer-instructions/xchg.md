# `XCHG`
Exchange Register/Memory with Register

| Instruction       | Description                 |
| :---------------- | :-------------------------- |
| `XCHG r/m8, r8`   | Exchange `r/m8` with `r8`   |
| `XCHG r/m16 r16`  | Exchange `r/m16` with `r16` |
| `XCHG r/m32 r32`  | Exchange `r/m32` with `r32` |
| `XCHG r/m64 r64`  | Exchange `r/m64` with `r64` |
|                   |                             |
| `XCHG r8, r/m8`   | Exchange `r8` with `r/m8`   |
| `XCHG r16, r/m16` | Exchange `r16` with `r/m16` |
| `XCHG r32, r/m32` | Exchange `r32` with `r/m32` |
| `XCHG r64, r/m64` | Exchange `r64` with `r/m64` |

## Description
Exchanges the contents of the destination (first) and source (second) operands. The operands can be two general-purpose registers or a register and a memory location. If a memory operand is referenced, the processor’s locking protocol is automatically implemented for the duration of the exchange operation, regardless of the presence or absence of the `LOCK` prefix or of the value of the `IOPL`. (See the `LOCK` prefix description in this chapter for more information on the locking protocol.)

This instruction is useful for implementing semaphores or similar data structures for process synchronization. (See "Bus Locking" in Chapter 8 of the Intel® 64 and IA-32 Architectures Software Developer’s Manual, Volume 3A, for more information on bus locking.)

The `XCHG` instruction can also be used instead of the `BSWAP` instruction for 16-bit operands.

In 64-bit mode, the instruction’s default operation size is 32 bits. Using a `REX` prefix in the form of `REX.R` permits access to additional registers (`R8`-`R15`). Using a `REX` prefix in the form of `REX.W` promotes operation to 64 bits. See the summary chart at the beginning of this section for encoding data and limits.

> `XCHG (E)AX, (E)AX` (encoded instruction byte is 90H) is an alias for `NOP` regardless of data size prefixes, including `REX.W`.

## Operation
`DEST`: first operand\
`SRC`: second operand
```rust,ignore
TEMP = DEST;
DEST = SRC;
SRC = TEMP;
```

## Flags Affected
None.
