# `AND`
Logical AND

| Instruction        | Description                         |
| :----------------- | :---------------------------------- |
| `AND r/m8, imm8`   | `r/m8` AND `imm8`                   |
| `AND r/m16, imm16` | `r/m16` AND `imm16`                 |
| `AND r/m32, imm32` | `r/m32` AND `imm32`                 |
| `AND r/m64, imm32` | `r/m64` AND `imm32` (sign-extended) |
|                    |                                     |
| `AND r/m16, imm8`  | `r/m16` AND `imm8` (sign-extended)  |
| `AND r/m32, imm8`  | `r/m32` AND `imm8` (sign-extended)  |
| `AND r/m64, imm8`  | `r/m64` AND `imm8` (sign-extended)  |
|                    |                                     |
| `AND r/m8, r8`     | `r/m8` AND `r8`                     |
| `AND r/m16, r16`   | `r/m16` AND `r16`                   |
| `AND r/m32, r32`   | `r/m32` AND `r32`                   |
| `AND r/m64, r64`   | `r/m64` AND `r64`                   |
|                    |                                     |
| `AND r8, r/m8`     | `r8` AND `r/m8`                     |
| `AND r16, r/m16`   | `r16` AND `r/m16`                   |
| `AND r32, r/m32`   | `r32` AND `r/m32`                   |
| `AND r64, r/m64`   | `r64` AND `r/m64`                   |

## Description
Performs a bitwise `AND` operation on the destination (first) and source (second) operands and stores the result in the destination operand location. The source operand can be an immediate, a register, or a memory location; the destination operand can be a register or a memory location. (However, two memory operands cannot be used in one instruction.) Each bit of the result is set to 1 if both corresponding bits of the first and second operands are 1; otherwise, it is set to 0.

This instruction can be used with a `LOCK` prefix to allow the it to be executed atomically.

In 64-bit mode, the instruction's default operation size is 32 bits. Using a `REX` prefix in the form of `REX.R` permits access to additional registers (`R8`-`R15`). Using a `REX` prefix in the form of `REX.W` promotes operation to 64 bits. See the summary chart at the beginning of this section for encoding data and limits.

## Operation
`DEST`: first operand\
`SRC`: second operand
```rust,ignore
DEST = DEST & SRC;
```

## Flags Affected
The `OF` and `CF` flags are cleared; the `SF`, `ZF`, and `PF` flags are set according to the result. The state of the `AF` flag is undefined.
