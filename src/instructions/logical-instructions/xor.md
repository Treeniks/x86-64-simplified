# `XOR`
Logical Exclusive OR

| Instruction        | Description                         |
| :----------------- | :---------------------------------- |
| `XOR r/m8, imm8`   | `r/m8` XOR `imm8`                   |
| `XOR r/m16, imm16` | `r/m16` XOR `imm16`                 |
| `XOR r/m32, imm32` | `r/m32` XOR `imm32`                 |
| `XOR r/m64, imm32` | `r/m64` XOR `imm32` (sign-extended) |
|                    |                                     |
| `XOR r/m16, imm8`  | `r/m16` XOR `imm8` (sign-extended)  |
| `XOR r/m32, imm8`  | `r/m32` XOR `imm8` (sign-extended)  |
| `XOR r/m64, imm8`  | `r/m64` XOR `imm8` (sign-extended)  |
|                    |                                     |
| `XOR r/m8, r8`     | `r/m8` XOR `r8`                     |
| `XOR r/m16, r16`   | `r/m16` XOR `r16`                   |
| `XOR r/m32, r32`   | `r/m32` XOR `r32`                   |
| `XOR r/m64, r64`   | `r/m64` XOR `r64`                   |
|                    |                                     |
| `XOR r8, r/m8`     | `r8` XOR `r/m8`                     |
| `XOR r16, r/m16`   | `r16` XOR `r/m16`                   |
| `XOR r32, r/m32`   | `r32` XOR `r/m32`                   |
| `XOR r64, r/m64`   | `r64` XOR `r/m64`                   |

## Description
Performs a bitwise exclusive `OR` (`XOR`) operation on the destination (first) and source (second) operands and stores the result in the destination operand location. The source operand can be an immediate, a register, or a memory location; the destination operand can be a register or a memory location. (However, two memory operands cannot be used in one instruction.) Each bit of the result is 1 if the corresponding bits of the operands are different; each bit is 0 if the corresponding bits are the same.

This instruction can be used with a `LOCK` prefix to allow the instruction to be executed atomically.

In 64-bit mode, using a `REX` prefix in the form of `REX.R` permits access to additional registers (`R8`-`R15`). Using a
`REX` prefix in the form of `REX.W` promotes operation to 64 bits. See the summary chart at the beginning of this
section for encoding data and limits.

## Operation
`DEST`: first operand\
`SRC`: second operand
```rust,ignore
DEST = DEST ^ SRC;
```

## Flags Affected
The `OF` and `CF` flags are cleared; the `SF`, `ZF`, and `PF` flags are set according to the result. The state of the `AF` flag is undefined.
