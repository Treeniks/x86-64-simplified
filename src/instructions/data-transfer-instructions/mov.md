# MOV
Move

| Instruction        | Description                           |
| :----------------- | :------------------------------------ |
| `MOV r/m8, r8`     | Move `r8` to `r/m8`                   |
| `MOV r/m16, r16`   | Move `r16` to `r/m16`                 |
| `MOV r/m32, r32`   | Move `r32` to `r/m32`                 |
| `MOV r/m64, r64`   | Move `r64` to `r/m64`                 |
|                    |                                       |
| `MOV r8, r/m8`     | Move `r/m8` to `r8`                   |
| `MOV r16, r/m16`   | Move `r/m16` to `r16`                 |
| `MOV r32, r/m32`   | Move `r/m32` to `r32`                 |
| `MOV r64, r/m64`   | Move `r/m64` to `r64`                 |
|                    |                                       |
| `MOV r/m8, imm8`   | Move `imm8` to `r/m8`                 |
| `MOV r/m16, imm16` | Move `imm16` to `r/m16`               |
| `MOV r/m32, imm32` | Move `imm32` to `r/m32`               |
| `MOV r64, imm64`   | Move `imm64` to `r64`                 |
| `MOV r/m64, imm32` | Move `imm32` sign extended to `r/m64` |

## Operation
`DEST`: first operand\
`SRC`: second operand
```rust,no_run,noplayground,ignore
DEST = SRC;
```

## Flags Affected
None

## Description
Copies the second operand (source operand) to the first operand (destination operand). The source operand can be an immediate value, general-purpose register, segment register, or memory location; the destination register can be a general-purpose register, segment register, or memory location. Both operands must be the same size, which can be a byte, a word, a doubleword, or a quadword.

The MOV instruction cannot be used to load the CS register. Attempting to do so results in an invalid opcode exception (#UD). To load the CS register, use the far JMP, CALL, or RET instruction.

If the destination operand is a segment register (DS, ES, FS, GS, or SS), the source operand must be a valid segment selector. In protected mode, moving a segment selector into a segment register automatically causes the segment descriptor information associated with that segment selector to be loaded into the hidden (shadow) part of the segment register. While loading this information, the segment selector and segment descriptor information is validated (see the “Operation” algorithm below). The segment descriptor data is obtained from the GDT or LDT entry for the specified segment selector.

A NULL segment selector (values 0000-0003) can be loaded into the DS, ES, FS, and GS registers without causing a protection exception. However, any subsequent attempt to reference a segment whose corresponding segment register is loaded with a NULL value causes a general protection exception (#GP) and no memory reference occurs.

Loading the SS register with a MOV instruction suppresses or inhibits some debug exceptions and inhibits interrupts on the following instruction boundary. (The inhibition ends after delivery of an exception or the execution of the next instruction.) This behavior allows a stack pointer to be loaded into the ESP register with the next instruction (MOV ESP, stack-pointer value) before an event can be delivered. See Section 6.8.3, “Masking Exceptions and Interrupts When Switching Stacks,” in Intel® 64 and IA-32 Architectures Software Developer’s Manual, Volume 3A. Intel recommends that software use the LSS instruction to load the SS register and ESP together.

When executing MOV Reg, Sreg, the processor copies the content of Sreg to the 16 least significant bits of the general-purpose register. The upper bits of the destination register are zero for most IA-32 processors (Pentium Pro processors and later) and all Intel 64 processors, with the exception that bits 31:16 are undefined for Intel Quark X1000 processors, Pentium and earlier processors.

In 64-bit mode, the instruction’s default operation size is 32 bits. Use of the REX.R prefix permits access to additional registers (R8-R15). Use of the REX.W prefix promotes operation to 64 bits. See the summary chart at the beginning of this section for encoding data and limits.
