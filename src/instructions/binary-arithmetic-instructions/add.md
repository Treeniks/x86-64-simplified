# ADD
Add

| Instruction          | Description                            |
| :------------------- | :------------------------------------- |
| `ADD r/m8, imm8`     | Add `imm8` to `r/m8`                   |
| `ADD r/m16, imm16`   | Add `imm16` to `r/m16`                 |
| `ADD r/m32, imm32`   | Add `imm32` to `r/m32`                 |
| `ADD r/m64, imm32`   | Add sign-extended `imm32` to `r/m64`   |
|                      |                                        |
| `ADD r/m16-64, imm8` | Add sign-extended `imm8` to `r/m16-64` |
|                      |                                        |
| `ADD r/m, r`         | Add `r` to `r/m`                       |
| `ADD r, r/m`         | Add `r/m` to `r`                       |

## Operation
`DEST`: first operand\
`SRC`: second operand
```
DEST := DEST + SRC;
```

## Flags Affected
`OF`, `SF`, `ZF`, `AF`, `CF`, `PF`

## Description
Adds the destination operand (first operand) and the source operand (second operand) and then stores the result in the destination operand. The destination operand can be a register or a memory location; the source operand can be an immediate, a register, or a memory location. (However, two memory operands cannot be used in one instruction.) When an immediate value is used as an operand, it is sign-extended to the length of the destination operand format.

The ADD instruction performs integer addition. It evaluates the result for both signed and unsigned integer operands and sets the OF and CF flags to indicate a carry (overflow) in the signed or unsigned result, respectively. The SF flag indicates the sign of the signed result.

This instruction can be used with a LOCK prefix to allow the instruction to be executed atomically.
