# TEST
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

## Operation
`SRC1`: first operand\
`SRC2`: second operand
```rust,no_run,noplayground,ignore
TEMP = SRC1 & SRC2;
SF = MSB(TEMP);

if TEMP == 0 {
    ZF = 1;
} else {
    ZF = 0;
}

PF = BitwiseXNOR(TEMP[0:7]);
CF = 0;
OF = 0;
// AF is undefined
```

## Flags Affected
The `OF` and `CF` flags are set to 0. The `SF`, `ZF`, and `PF` flags are set according to the result (see the [Operation section](#operation) above). The state of the `AF` flag is undefined.

## Description
Adds the destination operand (first operand) and the source operand (second operand) and then stores the result in the destination operand. The destination operand can be a register or a memory location; the source operand can be an immediate, a register, or a memory location. (However, two memory operands cannot be used in one instruction.) When an immediate value is used as an operand, it is sign-extended to the length of the destination operand format.

The ADD instruction performs integer addition. It evaluates the result for both signed and unsigned integer operands and sets the OF and CF flags to indicate a carry (overflow) in the signed or unsigned result, respectively. The SF flag indicates the sign of the signed result.

This instruction can be used with a LOCK prefix to allow the instruction to be executed atomically.