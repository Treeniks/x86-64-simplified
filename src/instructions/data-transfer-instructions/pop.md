# POP
Pop a Value from the Stack

| Instruction | Description                                            |
| :---------- | :----------------------------------------------------- |
| `POP r/m16` | Pop top of stack into `r/m16`; increment stack pointer |
| `POP r/m32` | Pop top of stack into `r/m32`; increment stack pointer |
| `POP r/m64` | Pop top of stack into `r/m64`; increment stack pointer |
|             |                                                        |
| `POP r16`   | Pop top of stack into `r16`; increment stack pointer   |
| `POP r32`   | Pop top of stack into `r32`; increment stack pointer   |
| `POP r64`   | Pop top of stack into `r64`; increment stack pointer   |

## Description
Loads the value from the top of the stack to the location specified with the destination operand (or explicit opcode) and then increments the stack pointer. The destination operand can be a general-purpose register, memory location, or segment register.

Address and operand sizes are determined and used as follows:
* Address size. The `D` flag in the current code-segment descriptor determines the default address size; it may be overridden by an instruction prefix (`67H`).

  The address size is used only when writing to a destination operand in memory.
* Operand size. The `D` flag in the current code-segment descriptor determines the default operand size; it may be overridden by instruction prefixes (`66H` or `REX.W`).

  The operand size (16, 32, or 64 bits) determines the amount by which the stack pointer is incremented (2, 4 or 8).
* Stack-address size. Outside of 64-bit mode, the B flag in the current stack-segment descriptor determines the size of the stack pointer (16 or 32 bits); in 64-bit mode, the size of the stack pointer is always 64 bits.

  The stack-address size determines the width of the stack pointer when reading from the stack in memory and when incrementing the stack pointer. (As stated above, the amount by which the stack pointer is incremented is determined by the operand size.)
If the destination operand is one of the segment registers `DS`, `ES`, `FS`, `GS`, or `SS`, the value loaded into the register must be a valid segment selector. In protected mode, popping a segment selector into a segment register automatically causes the descriptor information associated with that segment selector to be loaded into the hidden (shadow) part of the segment register and causes the selector and the descriptor information to be validated (see the [Operation section](#operation) below).

A `NULL` value (0000-0003) may be popped into the `DS`, `ES`, `FS`, or `GS` register without causing a general protection fault. However, any subsequent attempt to reference a segment whose corresponding segment register is loaded with a `NULL` value causes a general protection exception (#GP). In this situation, no memory reference occurs and the saved value of the segment register is `NULL`.

The `POP` instruction cannot pop a value into the `CS` register. To load the `CS` register from the stack, use the `RET` instruction.

If the `ESP` register is used as a base register for addressing a destination operand in memory, the `POP` instruction computes the effective address of the operand after it increments the `ESP` register. For the case of a 16-bit stack where `ESP` wraps to `0H` as a result of the `POP` instruction, the resulting location of the memory write is processor-family-specific.

The `POP ESP` instruction increments the stack pointer (`ESP`) before data at the old top of stack is written into the destination.

Loading the `SS` register with a `POP` instruction suppresses or inhibits some debug exceptions and inhibits interrupts on the following instruction boundary. (The inhibition ends after delivery of an exception or the execution of the next instruction.) This behavior allows a stack pointer to be loaded into the `ESP` register with the next instruction (`POP ESP`) before an event can be delivered. See Section 6.8.3, "Masking Exceptions and Interrupts When Switching Stacks," in Intel® 64 and IA-32 Architectures Software Developer’s Manual, Volume 3A. Intel recommends that software use the LSS instruction to load the `SS` register and `ESP` together.

In 64-bit mode, using a `REX` prefix in the form of `REX.R` permits access to additional registers (`R8`-`R15`). When in 64-bit mode, `POP`s using 32-bit operands are not encodable and `POP`s to `DS`, `ES`, `SS` are not valid. See the summary chart at the beginning of this section for encoding data and limits.

## Operation
`DEST`: operand

### OperandSize = 64
```rust,ignore
DEST = SS:RSP;
RSP = RSP + 8;
```

### OperandSize = 32
```rust,ignore
DEST = SS:RSP;
RSP = RSP + 4;
```

### OperandSize = 16
```rust,ignore
DEST = SS:RSP;
RSP = RSP + 2;
```

## Flags Affected
None.
