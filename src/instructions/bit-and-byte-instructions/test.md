# `TEST`
Logical Compare

The same operation as [`AND`](../logical-instructions/and.md), but do not save the result in a register, only keep the flags.

| Instruction         | Description                                            |
| :------------------ | :----------------------------------------------------- |
| `TEST r/m8, imm8`   | AND `imm8` with `r/m8`; only set Flags                 |
| `TEST r/m16, imm16` | AND `imm16` with `r/m16`; only set Flags               |
| `TEST r/m32, imm32` | AND `imm32` with `r/m32`; only set Flags               |
| `TEST r/m64, imm32` | AND sign-extended `imm32` with `r/m64`; only set Flags |
|                     |                                                        |
| `TEST r/m8, r8`     | AND `r8` with `r/m8`; only set Flags                   |
| `TEST r/m16, r16`   | AND `r16` with `r/m16`; only set Flags                 |
| `TEST r/m32, r32`   | AND `r32` with `r/m32`; only set Flags                 |
| `TEST r/m64, r64`   | AND `r64` with `r/m64`; only set Flags                 |

## Description
Computes the bit-wise logical `AND` of first operand (source 1 operand) and the second operand (source 2 operand) and sets the `SF`, `ZF`, and `PF` status flags according to the result. The result is then discarded.

In 64-bit mode, using a `REX` prefix in the form of `REX.R` permits access to additional registers (`R8`-`R15`). Using a `REX` prefix in the form of `REX.W` promotes operation to 64 bits. See the summary chart at the beginning of this section for encoding data and limits.

## Operation
`SRC1`: first operand\
`SRC2`: second operand
```rust,ignore
TEMP = SRC1 & SRC2;
SF = MSB(TEMP);

if TEMP == 0 {
    ZF = 1;
} else {
    ZF = 0;
}

PF = BitwiseXNOR(TEMP[0..=7]);
CF = 0;
OF = 0;
// AF is undefined
```

## Flags Affected
The `OF` and `CF` flags are set to 0. The `SF`, `ZF`, and `PF` flags are set according to the result (see the [Operation section](#operation) above). The state of the `AF` flag is undefined.
