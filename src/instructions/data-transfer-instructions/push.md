# `PUSH`
Push Word, Doubleword or Quadword Onto the Stack

| Instruction  | Description  |
| :----------- | :----------- |
| `PUSH r/m16` | Push `r/m16` |
| `PUSH r/m32` | Push `r/m32` |
| `PUSH r/m64` | Push `r/m64` |
|              |              |
| `PUSH r16`   | Push `r16`   |
| `PUSH r32`   | Push `r32`   |
| `PUSH r64`   | Push `r64`   |
|              |              |
| `PUSH imm8`  | Push `imm8`  |
| `PUSH imm16` | Push `imm16` |
| `PUSH imm32` | Push `imm32` |

## Description
Decrements the stack pointer and then stores the source operand on the top of the stack. Address and operand sizes are determined and used as follows:
* Address size. The `D` flag in the current code-segment descriptor determines the default address size; it may be overridden by an instruction prefix (`67H`).\
  The address size is used only when referencing a source operand in memory.
* Operand size. The `D` flag in the current code-segment descriptor determines the default operand size; it may be overridden by instruction prefixes (`66H` or `REX.W`).

  The operand size (16, 32, or 64 bits) determines the amount by which the stack pointer is decremented (2, 4 or 8).

  If the source operand is an immediate of size less than the operand size, a sign-extended value is pushed on the stack. If the source operand is a segment register (16 bits) and the operand size is 64-bits, a zero-extended value is pushed on the stack; if the operand size is 32-bits, either a zero-extended value is pushed on the stack or the segment selector is written on the stack using a 16-bit move. For the last case, all recent Core and Atom processors perform a 16-bit move, leaving the upper portion of the stack location unmodified.
* Stack-address size. Outside of 64-bit mode, the B flag in the current stack-segment descriptor determines the size of the stack pointer (16 or 32 bits); in 64-bit mode, the size of the stack pointer is always 64 bits.

  The stack-address size determines the width of the stack pointer when writing to the stack in memory and when decrementing the stack pointer. (As stated above, the amount by which the stack pointer is decremented is determined by the operand size.)

  If the operand size is less than the stack-address size, the `PUSH` instruction may result in a misaligned stack pointer (a stack pointer that is not aligned on a doubleword or quadword boundary).
The `PUSH ESP` instruction pushes the value of the `ESP` register as it existed before the instruction was executed. If a `PUSH` instruction uses a memory operand in which the `ESP` register is used for computing the operand address, the address of the operand is computed before the `ESP` register is decremented.

If the `ESP` or `SP` register is 1 when the `PUSH` instruction is executed in real-address mode, a stack-fault exception (#SS) is generated (because the limit of the stack segment is violated). Its delivery encounters a second stackfault exception (for the same reason), causing generation of a double-fault exception (#DF). Delivery of the double-fault exception encounters a third stack-fault exception, and the logical processor enters shutdown mode. See the discussion of the double-fault exception in Chapter 6 of the Intel® 64 and IA-32 Architectures Software Developer’s Manual, Volume 3A.

## Operation
See Description section for possible sign-extension or zero-extension of source operand and for a case in which the size of the memory store may be smaller than the instruction’s operand size

`SRC`: operand

### OperandSize = 16
```rust,ignore
// push word
RSP = RSP - 2;
Memory[SS:RSP] = SRC;
```

### OperandSize = 32
```rust,ignore
// push dword
RSP = RSP - 4;
Memory[SS:RSP] = SRC;
```

### OperandSize = 64
```rust,ignore
// push quadword
RSP = RPS - 8;
Memory[SS:RSP] = SRC;
```

## Flags Affected
None.
