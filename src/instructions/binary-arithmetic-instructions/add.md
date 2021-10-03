# `ADD`
Add

| Instruction        | Description                          |
| :----------------- | :----------------------------------- |
| `ADD r/m8, imm8`   | Add `imm8` to `r/m8`                 |
| `ADD r/m16, imm16` | Add `imm16` to `r/m16`               |
| `ADD r/m32, imm32` | Add `imm32` to `r/m32`               |
| `ADD r/m64, imm32` | Add sign-extended `imm32` to `r/m64` |
|                    |                                      |
| `ADD r/m16, imm8`  | Add sign-extended `imm8` to `r/m16`  |
| `ADD r/m32, imm8`  | Add sign-extended `imm8` to `r/m32`  |
| `ADD r/m64, imm8`  | Add sign-extended `imm8` to `r/m64`  |
|                    |                                      |
| `ADD r/m8, r8`     | Add `r8` to `r/m8`                   |
| `ADD r/m16, r16`   | Add `r16` to `r/m16`                 |
| `ADD r/m32, r32`   | Add `r32` to `r/m32`                 |
| `ADD r/m64, r64`   | Add `r64` to `r/m64`                 |
|                    |                                      |
| `ADD r8, r/m8`     | ADD `r/m8` to `r8`                   |
| `ADD r16, r/m16`   | ADD `r/m16` to `r16`                 |
| `ADD r32, r/m32`   | ADD `r/m32` to `r32`                 |
| `ADD r64, r/m64`   | ADD `r/m64` to `r64`                 |

## Description
Adds the destination operand (first operand) and the source operand (second operand) and then stores the result in the destination operand. The destination operand can be a register or a memory location; the source operand can be an immediate, a register, or a memory location. (However, two memory operands cannot be used in one instruction.) When an immediate value is used as an operand, it is sign-extended to the length of the destination operand format.

The `ADD` instruction performs integer addition. It evaluates the result for both signed and unsigned integer operands and sets the `OF` and `CF` flags to indicate a carry (overflow) in the signed or unsigned result, respectively. The `SF` flag indicates the sign of the signed result.

This instruction can be used with a `LOCK` prefix to allow the instruction to be executed atomically.

## Operation
`DEST`: first operand\
`SRC`: second operand
```rust,ignore
DEST = DEST + SRC;
```

## Flags Affected
`OF`, `SF`, `ZF`, `AF`, `CF`, `PF`
