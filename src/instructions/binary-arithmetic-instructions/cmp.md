# `CMP`
Compare Two Operands

The same operation as [`SUB`](../binary-arithmetic-instructions/sub.md), but do not save the result in a register, only keep the flags.

| Instruction        | Description                                      |
| :----------------- | :----------------------------------------------- |
| `CMP r/m8, imm8`   | Compare `imm8` with `r/m8`                       |
| `CMP r/m16, imm16` | Compare `imm16` with `r/m16`                     |
| `CMP r/m32, imm32` | Compare `imm32` with `r/m32`                     |
| `CMP r/m64, imm32` | Compare sign-extended `imm32` with `r/m64`       |
|                    |                                                  |
| `CMP r/m16, imm8`  | Compare (not sign-extended)* `imm8` with `r/m16` |
| `CMP r/m32, imm8`  | Compare (not sign-extended)* `imm8` with `r/m32` |
| `CMP r/m64, imm8`  | Compare (not sign-extended)* `imm8` with `r/m64` |
|                    |                                                  |
| `CMP r/m8, r8`     | Compare `r8` with `r/m8`                         |
| `CMP r/m16, r16`   | Compare `r16` with `r/m16`                       |
| `CMP r/m32, r32`   | Compare `r32` with `r/m32`                       |
| `CMP r/m64, r64`   | Compare `r64` with `r/m64`                       |
|                    |                                                  |
| `CMP r8, r/m8`     | Compare `r/m8` with `r8`                         |
| `CMP r16, r/m16`   | Compare `r/m16` with `r16`                       |
| `CMP r32, r/m32`   | Compare `r/m32` with `r32`                       |
| `CMP r64, r/m64`   | Compare `r/m64` with `r64`                       |

\* Not mentioned in the Intel Manual, could be wrong.

## Description
Compares the first source operand with the second source operand and sets the status flags in the `EFLAGS` register according to the results. The comparison is performed by subtracting the second operand from the first operand and then setting the status flags in the same manner as the SUB instruction. When an immediate value is used as an operand, it is sign-extended to the length of the first operand.

The condition codes used by the `Jcc`, `CMOVcc`, and `SETcc` instructions are based on the results of a `CMP` instruction. Appendix B, "EFLAGS Condition Codes," in the Intel® 64 and IA-32 Architectures Software Developer’s Manual, Volume 1, shows the relationship of the status flags and the condition codes.

In 64-bit mode, the instruction’s default operation size is 32 bits. Use of the `REX.R` prefix permits access to additional registers (`R8`-`R15`). Use of the `REX.W` prefix promotes operation to 64 bits. See the summary chart at the beginning of this section for encoding data and limits.

## Operation
`SRC1`: first operand
`SRC2`: second operand
```rust,ignore
temp = SRC1 - SignExtend(SRC2);
ModifyStatusFlags(); // modify status flags in the same manner as the SUB instruction
```

## Flags Affected
The `CF`, `OF`, `SF`, `ZF`, `AF`, and `PF` flags are set according to the result.
