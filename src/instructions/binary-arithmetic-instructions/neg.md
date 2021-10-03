# `NEG`
Two's Complement Negation

| Instruction | Description                     |
| :---------- | :------------------------------ |
| `NEG r/m8`  | Two's complement negate `r/m8`  |
| `NEG r/m16` | Two's complement negate `r/m16` |
| `NEG r/m32` | Two's complement negate `r/m32` |
| `NEG r/m64` | Two's complement negate `r/m64` |

## Description
Replaces the value of operand (the destination operand) with its two's complement. (This operation is equivalent to subtracting the operand from 0.) The destination operand is located in a general-purpose register or a memory location.

This instruction can be used with a `LOCK` prefix to allow the instruction to be executed atomically.

In 64-bit mode, the instruction's default operation size is 32 bits. Using a `REX` prefix in the form of `REX.R` permits access to additional registers (`R8`-`R15`). Using a `REX` prefix in the form of `REX.W` promotes operation to 64 bits. See the summary chart at the beginning of this section for encoding data and limits.

## Operation
`DEST`: operand
```rust,ignore
if DEST == 0 {
    CF = 0;
} else {
    CF = 1;
}
DEST = -DEST;
```

## Flags Affected
The `CF` flag set to 0 if the source operand is 0; otherwise it is set to 1. The `OF`, `SF`, `ZF`, `AF`, and `PF` flags are set according to the result.
