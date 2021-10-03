# `Jcc`
Jump if Condition Is Met

| Instruction | Description                                     |
| :---------- | :---------------------------------------------- |
| `Jcc rel8`  | Jump short if `cc`                              |
| `Jcc rel16` | Jump near if `cc`; Not supported in 64-bit mode |
| `Jcc rel32` | Jump near if `cc`                               |

## Description
Checks the state of one or more of the status flags in the `EFLAGS` register (`CF`, `OF`, `PF`, `SF`, and `ZF`) and, if the flags are in the specified state (condition), performs a jump to the target instruction specified by the destination operand. A condition code (`cc`) is associated with each instruction to indicate the condition being tested for. If the condition is not satisfied, the jump is not performed and execution continues with the instruction following the `Jcc` instruction.

The target instruction is specified with a relative offset (a signed offset relative to the current value of the instruction pointer in the `EIP` register). A relative offset (`rel8`, `rel16`, or `rel32`) is generally specified as a label in assembly code, but at the machine code level, it is encoded as a signed, 8-bit or 32-bit immediate value, which is added to the instruction pointer. Instruction coding is most efficient for offsets of –128 to +127. If the operand-size attribute is 16, the upper two bytes of the `EIP` register are cleared, resulting in a maximum instruction pointer size of 16 bits.

The conditions for each `Jcc` mnemonic are given in the "Description" column of the table on the preceding page. The terms "less" and "greater" are used for comparisons of signed integers and the terms "above" and "below" are used for unsigned integers.

Because a particular state of the status flags can sometimes be interpreted in two ways, two mnemonics are defined for some opcodes. For example, the `JA` (jump if above) instruction and the `JNBE` (jump if not below or equal) instruction are alternate mnemonics for the opcode `0x77`.

The `Jcc` instruction does not support far jumps (jumps to other code segments). When the target for the conditional jump is in a different segment, use the opposite condition from the condition being tested for the `Jcc` instruction, and then access the target with an unconditional far jump (`JMP` instruction) to the other segment. For example, the following conditional far jump is illegal:
```x86asm
jz FARLABEL
```
To accomplish this far jump, use the following two instructions:
```x86asm
jnz BEYOND
jmp FARLABEL
BEYOND:
```
The `JRCXZ`, `JECXZ` and `JCXZ` instructions differ from other `Jcc` instructions because they do not check status flags. Instead, they check `RCX`, `ECX` or `CX` for 0. The register checked is determined by the address-size attribute. These instructions are useful when used at the beginning of a loop that terminates with a conditional loop instruction (such as `LOOPNE`). They can be used to prevent an instruction sequence from entering a loop when `RCX`, `ECX` or `CX` is 0. This would cause the loop to execute 264, 232 or 64K times (not zero times).

All conditional jumps are converted to code fetches of one or two cache lines, regardless of jump address or cacheability.

In 64-bit mode, operand size is fixed at 64 bits. `JMP` Short is `RIP` = `RIP` + 8-bit offset sign extended to 64 bits. `JMP` Near is `RIP` = `RIP` + 32-bit offset sign extended to 64 bits.

## Operation
```rust,ignore
if condition {
    tempEIP = EIP + SignExtend(DEST);
    if OperandSize == 16 {
        tempEIP = tempEIP && 0x0000_FFFF;
    }
    if /* tempEIP is not within code segment limit */ {
        ##GP(0);
    } else {
        EIP = tempEIP;
    }
}
```

## Flags Affected
None.
