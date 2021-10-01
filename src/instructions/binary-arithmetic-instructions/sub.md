# SUB
Subtract

| Instruction        | Description                                 |
| :----------------- | :------------------------------------------ |
| `SUB r/m8, imm8`   | Subtract `imm8` from `r/m8`                 |
| `SUB r/m16, imm16` | Subtract `imm16` from `r/m16`               |
| `SUB r/m32, imm32` | Subtract `imm32` from `r/m32`               |
| `SUB r/m64, imm32` | Subtract sign-extended `imm32` from `r/m64` |
|                    |                                             |
| `SUB r/m16, imm8`  | Subtract sign-extended `imm8` from `r/m16`  |
| `SUB r/m32, imm8`  | Subtract sign-extended `imm8` from `r/m32`  |
| `SUB r/m64, imm8`  | Subtract sign-extended `imm8` from `r/m64`  |
|                    |                                             |
| `SUB r/m8, r8`     | Subtract `r8` from `r/m8`                   |
| `SUB r/m16, r16`   | Subtract `r16` from `r/m16`                 |
| `SUB r/m32, r32`   | Subtract `r32` from `r/m32`                 |
| `SUB r/m64, r64`   | Subtract `r64` from `r/m64`                 |
|                    |                                             |
| `SUB r8, r/m8`     | Subtract `r/m8` from `r8`                   |
| `SUB r16, r/m16`   | Subtract `r/m16` from `r16`                 |
| `SUB r32, r/m32`   | Subtract `r/m32` from `r32`                 |
| `SUB r64, r/m64`   | Subtract `r/m64` from `r64`                 |

## Operation
`DEST`: first operand\
`SRC`: second operand
```rust,no_run,noplayground,ignore
DEST = DEST – SRC;
```

## Flags Affected
`OF`, `SF`, `ZF`, `AF`, `PF`, `CF`

## Description
Subtracts the second operand (source operand) from the first operand (destination operand) and stores the result in the destination operand. The destination operand can be a register or a memory location; the source operand can be an immediate, register, or memory location. (However, two memory operands cannot be used in one instruction.) When an immediate value is used as an operand, it is sign-extended to the length of the destination operand format.

The `SUB` instruction performs integer subtraction. It evaluates the result for both signed and unsigned integer operands and sets the `OF` and `CF` flags to indicate an overflow in the signed or unsigned result, respectively. The `SF` flag indicates the sign of the signed result.

This instruction can be used with a `LOCK` prefix to allow the instruction to be executed atomically.
