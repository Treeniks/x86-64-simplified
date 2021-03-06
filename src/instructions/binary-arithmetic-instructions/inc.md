# `INC`
Increment by 1

| Instruction | Description            |
| :---------- | :--------------------- |
| `INC r/m8`  | Increment `r/m8` by 1  |
| `INC r/m16` | Increment `r/m16` by 1 |
| `INC r/m32` | Increment `r/m32` by 1 |
| `INC r/m64` | Increment `r/m64` by 1 |

## Description
Adds 1 to the destination operand, while preserving the state of the `CF` flag. The destination operand can be a register or a memory location. This instruction allows a loop counter to be updated without disturbing the `CF` flag. (Use a `ADD` instruction with an immediate operand of 1 to perform an increment operation that does updates the `CF` flag.)

This instruction can be used with a `LOCK` prefix to allow the instruction to be executed atomically.

In 64-bit mode, `INC r16` and `INC r32` are not encodable (because opcodes `0x40` through `0x47` are `REX` prefixes). Otherwise, the instruction's 64-bit mode default operation size is 32 bits. Use of the `REX.R` prefix permits access to additional registers (`R8`-`R15`). Use of the `REX.W` prefix promotes operation to 64 bits.

## Operation
`DEST`: operand
```rust,ignore
DEST = DEST + 1;
```

## Flags Affected
The `CF` flag is not affected. The `OF`, `SF`, `ZF`, `AF`, and `PF` flags are set according to the result.
