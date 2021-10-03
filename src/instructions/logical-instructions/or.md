# `OR`
Logical Inclusive OR

| Instruction       | Description                        |
| :---------------- | :--------------------------------- |
| `OR r/m8, imm8`   | `r/m8` OR `imm8`                   |
| `OR r/m16, imm16` | `r/m16` OR `imm16`                 |
| `OR r/m32, imm32` | `r/m32` OR `imm32`                 |
| `OR r/m64, imm32` | `r/m64` OR `imm32` (sign-extended) |
|                   |                                    |
| `OR r/m16, imm8`  | `r/m16` OR `imm8` (sign-extended)  |
| `OR r/m32, imm8`  | `r/m32` OR `imm8` (sign-extended)  |
| `OR r/m64, imm8`  | `r/m64` OR `imm8` (sign-extended)  |
|                   |                                    |
| `OR r/m8, r8`     | `r/m8` OR `r8`                     |
| `OR r/m16, r16`   | `r/m16` OR `r16`                   |
| `OR r/m32, r32`   | `r/m32` OR `r32`                   |
| `OR r/m64, r64`   | `r/m64` OR `r64`                   |
|                   |                                    |
| `OR r8, r/m8`     | `r8` OR `r/m8`                     |
| `OR r16, r/m16`   | `r16` OR `r/m16`                   |
| `OR r32, r/m32`   | `r32` OR `r/m32`                   |
| `OR r64, r/m64`   | `r64` OR `r/m64`                   |

## Description
Performs a bitwise inclusive `OR` operation between the destination (first) and source (second) operands and stores the result in the destination operand location. The source operand can be an immediate, a register, or a memory location; the destination operand can be a register or a memory location. (However, two memory operands cannot be used in one instruction.) Each bit of the result of the `OR` instruction is set to 0 if both corresponding bits of the first and second operands are 0; otherwise, each bit is set to 1.

This instruction can be used with a `LOCK` prefix to allow the instruction to be executed atomically.

In 64-bit mode, the instruction's default operation size is 32 bits. Using a `REX` prefix in the form of `REX.R` permits access to additional registers (`R8`-`R15`). Using a `REX` prefix in the form of `REX.W` promotes operation to 64 bits. See the summary chart at the beginning of this section for encoding data and limits.

## Operation
`DEST`: first operand\
`SRC`: second operand
```rust,ignore
DEST = DEST | SRC;
```

## Flags Affected
The `OF` and `CF` flags are cleared; the `SF`, `ZF`, and `PF` flags are set according to the result. The state of the `AF` flag is undefined.
