# `CALL`
Call Procedure

| Instruction     | Description                                                                                                                                                                                                                                                   |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `CALL rel16`    | Call near, relative, displacement realtive to next instruction                                                                                                                                                                                                |
| `CALL rel32`    | Call near, realtive, displacement relative to next instruction; 32-bit displacement sign extended to 64-bits in 64-bit mode                                                                                                                                   |
|                 |                                                                                                                                                                                                                                                               |
| `CALL r/m16`    | Call near, absolute indirect, address given in `r/m16`                                                                                                                                                                                                        |
| `CALL r/m32`    | Call near, absolute indirect, address given in `r/m32`                                                                                                                                                                                                        |
| `CALL r/m64`    | Call near, absolute indirect, address given in `r/m64`                                                                                                                                                                                                        |
|                 |                                                                                                                                                                                                                                                               |
| `CALL ptr16:16` | Call far, absolute, address given in operand                                                                                                                                                                                                                  |
| `CALL ptr16:32` | Call far, absolute, address given in operand                                                                                                                                                                                                                  |
|                 |                                                                                                                                                                                                                                                               |
| `CALL m16:16`   | Call far, absolute indirect address given in `m16:16`; In 32-bit mode: If selector points to a gater, then `RIP` = 32-bit zero-extended displacement taken from gate; else `RIP` = zero-extended 16-bit offset from far pointer referenced in the instruction |
| `CALL m16:32`   | In 64-bit mode: If selector points to a gate, then `RIP` = 64-bit displacement taken from gate; else `RIP` = zero-extended 32-bit offset from far pointer referenced in the instruction                                                                       |
| `CALL m16:64`   | In 64-bit mode: If selector points to a gate, then `RIP` = 64-bit displacement taken from gate; else `RIP` = 64-bit offset from far pointer referenced in the instruction                                                                                     |

## Description
Saves procedure linking information on the stack and branches to the called procedure specified using the target operand. The target operand specifies the address of the first instruction in the called procedure. The operand can be an immediate value, a general-purpose register, or a memory location.

This instruction can be used to execute four types of calls:
* **Near Call**\
  A call to a procedure in the current code segment (the segment currently pointed to by the `CS` register), sometimes referred to as an intra-segment call.
* **Far Call**\
  A call to a procedure located in a different segment than the current code segment, sometimes referred to as an inter-segment call.
* **Inter-privilege-level far call**\
  A far call to a procedure in a segment at a different privilege level than that of the currently executing program or procedure.
* **Task switch**\
  A call to a procedure located in a different task.
The latter two call types (inter-privilege-level call and task switch) can only be executed in protected mode. See "Calling Procedures Using Call and RET" in Chapter 6 of the Intel® 64 and IA-32 Architectures Software Developer's Manual, Volume 1, for additional information on near, far, and inter-privilege-level calls. See Chapter 7, "Task Management," in the Intel® 64 and IA-32 Architectures Software Developer's Manual, Volume 3A, for information on performing task switches with the `CALL` instruction.

### Near Call
When executing a near call, the processor pushes the value of the `EIP` register (which contains the offset of the instruction following the `CALL` instruction) on the stack (for use later as a return-instruction pointer). The processor then branches to the address in the current code segment specified by the target operand. The target operand specifies either an absolute offset in the code segment (an offset from the base of the code segment) or a relative offset (a signed displacement relative to the current value of the instruction pointer in the `EIP` register; this value points to the instruction following the `CALL` instruction). The `CS` register is not changed on near calls.

For a near call absolute, an absolute offset is specified indirectly in a general-purpose register or a memory location (`r/m16`, `r/m32`, or `r/m64`). The operand-size attribute determines the size of the target operand (16, 32 or 64 bits). When in 64-bit mode, the operand size for near call (and all near branches) is forced to 64-bits. Absolute offsets are loaded directly into the `EIP`(`RIP`) register. If the operand size attribute is 16, the upper two bytes of the `EIP` register are cleared, resulting in a maximum instruction pointer size of 16 bits. When accessing an absolute offset indirectly using the stack pointer [`ESP`] as the base register, the base value used is the value of the `ESP` before the instruction executes.

A relative offset (`rel16` or `rel32`) is generally specified as a label in assembly code. But at the machine code level, it is encoded as a signed, 16- or 32-bit immediate value. This value is added to the value in the `EIP`(`RIP`) register. In 64-bit mode the relative offset is always a 32-bit immediate value which is sign extended to 64-bits before it is added to the value in the `RIP` register for the target calculation. As with absolute offsets, the operand-size attribute determines the size of the target operand (16, 32, or 64 bits). In 64-bit mode the target operand will always be 64-bits because the operand size is forced to 64-bits for near branches.

### Far Calls in Real-Address or Virtual-8086 Mode
When executing a far call in realaddress or virtual-8086 mode, the processor pushes the current value of both the `CS` and `EIP` registers on the stack for use as a return-instruction pointer. The processor then performs a "far branch" to the code segment and offset specified with the target operand for the called procedure. The target operand specifies an absolute far address either directly with a pointer (`ptr16:16` or `ptr16:32`) or indirectly with a memory location (`m16:16` or `m16:32`). With the pointer method, the segment and offset of the called procedure is encoded in the instruction using a 4-byte (16-bit operand size) or 6-byte (32-bit operand size) far address immediate. With the indirect method, the target operand specifies a memory location that contains a 4-byte (16-bit operand size) or 6-byte (32-bit operand size) far address. The operand-size attribute determines the size of the offset (16 or 32 bits) in the far address. The far address is loaded directly into the `CS` and `EIP` registers. If the operand-size attribute is 16, the upper two bytes of the `EIP` register are cleared.

### Far Calls in Protected Mode
When the processor is operating in protected mode, the `CALL` instruction can be used to perform the following types of far calls:
* Far call to the same privilege level
* Far call to a different privilege level (inter-privilege level call)
* Task switch (far call to another task)
In protected mode, the processor always uses the segment selector part of the far address to access the corresponding descriptor in the `GDT` or `LDT`. The descriptor type (code segment, call gate, task gate, or `TSS`) and access rights determine the type of call operation to be performed.

If the selected descriptor is for a code segment, a far call to a code segment at the same privilege level is performed. (If the selected code segment is at a different privilege level and the code segment is non-conforming, a general-protection exception is generated.) A far call to the same privilege level in protected mode is very similar to one carried out in real-address or virtual-8086 mode. The target operand specifies an absolute far address either directly with a pointer (`ptr16:16` or `ptr16:32`) or indirectly with a memory location (`m16:16` or `m16:32`). The operand- size attribute determines the size of the offset (16 or 32 bits) in the far address. The new code segment selector and its descriptor are loaded into `CS` register; the offset from the instruction is loaded into the `EIP` register.

A call gate (described in the next paragraph) can also be used to perform a far call to a code segment at the same privilege level. Using this mechanism provides an extra level of indirection and is the preferred method of making calls between 16-bit and 32-bit code segments.

When executing an inter-privilege-level far call, the code segment for the procedure being called must be accessed through a call gate. The segment selector specified by the target operand identifies the call gate. The target operand can specify the call gate segment selector either directly with a pointer (`ptr16:16` or `ptr16:32`) or indirectly with a memory location (`m16:16` or `m16:32`). The processor obtains the segment selector for the new code segment and the new instruction pointer (offset) from the call gate descriptor. (The offset from the target operand is ignored when a call gate is used.)

On inter-privilege-level calls, the processor switches to the stack for the privilege level of the called procedure. The segment selector for the new stack segment is specified in the `TSS` for the currently running task. The branch to the new code segment occurs after the stack switch. (Note that when using a call gate to perform a far call to a segment at the same privilege level, no stack switch occurs.) On the new stack, the processor pushes the segment selector and stack pointer for the calling procedure's stack, an optional set of parameters from the calling procedures stack, and the segment selector and instruction pointer for the calling procedure's code segment. (A value in the call gate descriptor determines how many parameters to copy to the new stack.) Finally, the processor branches to the address of the procedure being called within the new code segment.

Executing a task switch with the `CALL` instruction is similar to executing a call through a call gate. The target operand specifies the segment selector of the task gate for the new task activated by the switch (the offset in the target operand is ignored). The task gate in turn points to the `TSS` for the new task, which contains the segment selectors for the task's code and stack segments. Note that the `TSS` also contains the `EIP` value for the next instruction that was to be executed before the calling task was suspended. This instruction pointer value is loaded into the `EIP` register to re-start the calling task.

The CALL instruction can also specify the segment selector of the TSS directly, which eliminates the indirection of the task gate. See Chapter 7, "Task Management," in the Intel® 64 and IA-32 Architectures Software Developer's Manual, Volume 3A, for information on the mechanics of a task switch.

When you execute at task switch with a `CALL` instruction, the nested task flag (`NT`) is set in the `EFLAGS` register and the new `TSS`'s previous task link field is loaded with the old task's `TSS` selector. Code is expected to suspend this nested task by executing an `IRET` instruction which, because the `NT` flag is set, automatically uses the previous task link to return to the calling task. (See "Task Linking" in Chapter 7 of the Intel® 64 and IA-32 Architectures Software Developer's Manual, Volume 3A, for information on nested tasks.) Switching tasks with the `CALL` instruction differs in this regard from `JMP` instruction. `JMP` does not set the `NT` flag and therefore does not expect an `IRET` instruction to suspend the task.

### Mixing 16-Bit and 32-Bit Calls
When making far calls between 16-bit and 32-bit code segments, use a call gate. If the far call is from a 32-bit code segment to a 16-bit code segment, the call should be made from the first 64 KBytes of the 32-bit code segment. This is because the operand-size attribute of the instruction is set to 16, so only a 16-bit return address offset can be saved. Also, the call should be made using a 16-bit call gate so that 16-bit values can be pushed on the stack. See Chapter 20, "Mixing 16-Bit and 32-Bit Code," in the Intel® 64 and IA-32 Architectures Software Developer's Manual, Volume 3B, for more information.

### Far Calls in Compatibility Mode
When the processor is operating in compatibility mode, the `CALL` instruction can be used to perform the following types of far calls:
* Far call to the same privilege level, remaining in compatibility mode
* Far call to the same privilege level, transitioning to 64-bit mode
* Far call to a different privilege level (inter-privilege level call), transitioning to 64-bit mode
Note that a `CALL` instruction can not be used to cause a task switch in compatibility mode since task switches are not supported in IA-32e mode.

In compatibility mode, the processor always uses the segment selector part of the far address to access the corresponding descriptor in the `GDT` or `LDT`. The descriptor type (code segment, call gate) and access rights determine the type of call operation to be performed.

If the selected descriptor is for a code segment, a far call to a code segment at the same privilege level is performed. (If the selected code segment is at a different privilege level and the code segment is non-conforming, a general-protection exception is generated.) A far call to the same privilege level in compatibility mode is very similar to one carried out in protected mode. The target operand specifies an absolute far address either directly with a pointer (`ptr16:16` or `ptr16:32`) or indirectly with a memory location (`m16:16` or `m16:32`). The operand-size attribute determines the size of the offset (16 or 32 bits) in the far address. The new code segment selector and its descriptor are loaded into `CS` register and the offset from the instruction is loaded into the `EIP` register. The difference is that 64-bit mode may be entered. This specified by the `L` bit in the new code segment descriptor.

Note that a 64-bit call gate (described in the next paragraph) can also be used to perform a far call to a code segment at the same privilege level. However, using this mechanism requires that the target code segment descriptor have the `L` bit set, causing an entry to 64-bit mode.

When executing an inter-privilege-level far call, the code segment for the procedure being called must be accessed through a 64-bit call gate. The segment selector specified by the target operand identifies the call gate. The target operand can specify the call gate segment selector either directly with a pointer (`ptr16:16` or `ptr16:32`) or indirectly with a memory location (`m16:16` or `m16:32`). The processor obtains the segment selector for the new code segment and the new instruction pointer (offset) from the 16-byte call gate descriptor. (The offset from the target operand is ignored when a call gate is used.)

On inter-privilege-level calls, the processor switches to the stack for the privilege level of the called procedure. The segment selector for the new stack segment is set to `NULL`. The new stack pointer is specified in the `TSS` for the currently running task. The branch to the new code segment occurs after the stack switch. (Note that when using a call gate to perform a far call to a segment at the same privilege level, an implicit stack switch occurs as a result of entering 64-bit mode. The `SS` selector is unchanged, but stack segment accesses use a segment base of `0x0`, the limit is ignored, and the default stack size is 64-bits. The full value of `RSP` is used for the offset, of which the upper 32-bits are undefined.) On the new stack, the processor pushes the segment selector and stack pointer for the calling procedure's stack and the segment selector and instruction pointer for the calling procedure's code segment. (Parameter copy is not supported in IA-32e mode.) Finally, the processor branches to the address of the procedure being called within the new code segment.

### Near/(Far) Calls in 64-bit Mode
When the processor is operating in 64-bit mode, the `CALL` instruction can be used to perform the following types of far calls:
* Far call to the same privilege level, transitioning to compatibility mode
* Far call to the same privilege level, remaining in 64-bit mode
* Far call to a different privilege level (inter-privilege level call), remaining in 64-bit mode
Note that in this mode the `CALL` instruction can not be used to cause a task switch in 64-bit mode since task switches are not supported in IA-32e mode.

In 64-bit mode, the processor always uses the segment selector part of the far address to access the corresponding descriptor in the `GDT` or `LDT`. The descriptor type (code segment, call gate) and access rights determine the type of call operation to be performed.

If the selected descriptor is for a code segment, a far call to a code segment at the same privilege level is performed. (If the selected code segment is at a different privilege level and the code segment is non-conforming, a general-protection exception is generated.) A far call to the same privilege level in 64-bit mode is very similar to one carried out in compatibility mode. The target operand specifies an absolute far address indirectly with a memory location (`m16:16`, `m16:32` or `m16:64`). The form of `CALL` with a direct specification of absolute far address is not defined in 64-bit mode. The operand-size attribute determines the size of the offset (16, 32, or 64 bits) in the far address. The new code segment selector and its descriptor are loaded into the `CS` register; the offset from the instruction is loaded into the `EIP` register. The new code segment may specify entry either into compatibility or 64-bit mode, based on the `L` bit value.

A 64-bit call gate (described in the next paragraph) can also be used to perform a far call to a code segment at the same privilege level. However, using this mechanism requires that the target code segment descriptor have the `L` bit set.

When executing an inter-privilege-level far call, the code segment for the procedure being called must be accessed through a 64-bit call gate. The segment selector specified by the target operand identifies the call gate. The target operand can only specify the call gate segment selector indirectly with a memory location (`m16:16`, `m16:32` or `m16:64`). The processor obtains the segment selector for the new code segment and the new instruction pointer (offset) from the 16-byte call gate descriptor. (The offset from the target operand is ignored when a call gate is used.)

On inter-privilege-level calls, the processor switches to the stack for the privilege level of the called procedure. The segment selector for the new stack segment is set to `NULL`. The new stack pointer is specified in the `TSS` for the currently running task. The branch to the new code segment occurs after the stack switch.

Note that when using a call gate to perform a far call to a segment at the same privilege level, an implicit stack switch occurs as a result of entering 64-bit mode. The `SS` selector is unchanged, but stack segment accesses use a segment base of `0x0`, the limit is ignored, and the default stack size is 64-bits. (The full value of `RSP` is used for the offset.) On the new stack, the processor pushes the segment selector and stack pointer for the calling procedure's stack and the segment selector and instruction pointer for the calling procedure's code segment. (Parameter copy is not supported in IA-32e mode.) Finally, the processor branches to the address of the procedure being called within the new code segment.

Refer to Chapter 6, "Procedure Calls, Interrupts, and Exceptions" and Chapter 18, "Control-Flow Enforcement Technology (CET)" in the Intel® 64 and IA-32 Architectures Software Developer's Manual, Volume 1 for CET details.

### Instruction ordering
Instructions following a far call may be fetched from memory before earlier instructions complete execution, but they will not execute (even speculatively) until all instructions prior to the far call have completed execution (the later instructions may execute before data stored by the earlier instructions have become globally visible).

Certain situations may lead to the next sequential instruction after a near indirect `CALL` being speculatively executed. If software needs to prevent this (e.g., in order to prevent a speculative execution side channel), then an `LFENCE` instruction opcode can be placed after the near indirect `CALL` in order to block speculative execution.

## Operation
```rust,ignore
if /* near call */ {
    if /* near relative call */ {
        if OperandSize == 64 {
            tempDEST = SignExtend(DEST); // DEST is rel32
            tempRIP = RIP + tempDEST;
            if /* stack not large enough for a 8-byte return address */ {
                ##SS(0);
            }
            Push(RIP);
            if ShadowStackEnabled(CPL) && DEST != 0 {
                ShadowStackPush8B(RIP);
            }
            RIP = tempRIP;
        }
        if OperandSize == 32 {
            tempEIP = EIP + DEST; // DEST is rel32
            if /* tempEIP is not within code segment limit */ {
                ##GP(0);
            }
            if /* stack not large enough for a 4-byte return address */ {
                ##SS(0);
            }
            Push(EIP);
            if ShadowStackEnabled(CPL) && DEST != 0 {
                ShadowStackPush4B(EIP);
            }
            EIP = tempEIP;
        }
        if OperandSize == 16 {
            tempEIP = (EIP + DEST) && 0x0000_FFFF; // DEST is rel16
            if /* tempEIP is not within code segment limit */ {
                ##GP(0);
            }
            if /* stack not large enough for a 2-byte return address */ {
                ##SS(0);
            }
            Push(IP);
            if ShadowStackEnabled(CPL) && DEST != 0 {
                // IP is zero extended and pushed as a 32 bit value on shadow stack
                ShadowStackPush4B(IP);
            }
            EIP = tempEIP;
        }
    } else { // near absolute call *)
        if OperandSize == 64 {
            tempRIP = DEST; // DEST is r/m64
            if /* stack not large enough for a 8-byte return address */ {
                ##SS(0);
            }
            Push(RIP);
            if ShadowStackEnabled(CPL) {
                ShadowStackPush8B(RIP);
            }
            RIP = tempRIP;
        }
        if OperandSize == 32 {
            tempEIP = DEST; // DEST is r/m32
            if /* tempEIP is not within code segment limit */ {
                ##GP(0);
            }
            if /* stack not large enough for a 4-byte return address */ {
                ##SS(0);
            }
            Push(EIP);
            if ShadowStackEnabled(CPL) {
                ShadowStackPush4B(EIP);
            }
            EIP = tempEIP;
        }
        if OperandSize == 16 {
            tempEIP = DEST && 0x0000_FFFF; // DEST is r/m16
            if /* tempEIP is not within code segment limit */ {
                ##GP(0);
            }
            if /* stack not large enough for a 2-byte return address */ {
                ##SS(0);
            }
            Push(IP);
            if ShadowStackEnabled(CPL) {
                // IP is zero extended and pushed as a 32 bit value on shadow stack
                ShadowStackPush4B(IP);
            }
            EIP = tempEIP;
        }
    } // rel/abs
    if /* Call near indirect, absolute indirect */ {
        if EndbranchEnabledAndNotSuppressed(CPL) {
            if CPL == 3 {
                if /* no 3EH prefix */ || IA32_U_CET.NO_TRACK_EN == 0 {
                    IA32_U_CET.TRACKER = WAIT_FOR_ENDBRANCH;
                }
            } else {
                IF /* no 3EH prefix */ || IA32_S_CET.NO_TRACK_EN == 0 {
                    IA32_S_CET.TRACKER = WAIT_FOR_ENDBRANCH;
                }
            }
        }
    }
} // near

if /* far call */ && (PE == 0 || (PE == 1 && VM == 1)) // real-address or virtual-8086 mode {
    if OperandSize == 32 {
        if /* stack not large enough for a 6-byte return address */ {
            ##SS(0);
        }
        if /* DEST[31:16] is not zero */ {
            ##GP(0);
        }
        Push(CS); // padded with 16 high-order bits
        Push(EIP);
        CS = DEST[47:32]; // DEST is ptr16:32 or [m16:32]
        EIP = DEST[31:0]; // DEST is ptr16:32 or [m16:32]
    } else { // OperandSize == 16
        if /* stack not large enough for a 4-byte return address */ {
            ##SS(0);
        }
        Push(CS);
        Push(IP);
        CS = DEST[31:16]; // DEST is ptr16:16 or [m16:16]
        EIP = DEST[15:0]; // DEST is ptr16:16 or [m16:16]; clear upper 16 bits
    }
}

if /* far call */ && (PE == 1 and VM == 0) // protected mode or IA-32e Mode, not virtual-8086 mode
    if /* segment selector in target operand NULL */ {
        ##GP(0);
    }
    if /* segment selector index not within descriptor table limits */ {
        ##GP(new code segment selector);
    }
    /* Read type and access rights of selected segment descriptor; */
    if IA32_EFER.LMA == 0 {
        if /* segment type is not a conforming or nonconforming code segment, call
                gate, task gate, or TSS */ {
            ##GP(segment selector);
        }
    } else {
        if /* segment type is not a conforming or nonconforming code segment or
                64-bit call gate */ {
            ##GP(segment selector);
        }
    }
    /* Depending on type and access rights: */
        goto 'CONFORMING_CODE_SEGMENT;
        goto 'NONCONFORMING_CODE_SEGMENT;
        goto 'CALL_GATE;
        goto 'TASK_GATE;
        goto 'TASK_STATE_SEGMENT;
}

'CONFORMING-CODE-SEGMENT {
    if L-bit == 1 && D-bit == 1 && IA32_EFER.LMA == 1 {
        ##GP(new code segment selector);
    }
    if DPL > CPL {
        ##GP(new code segment selector);
    }
    if /* segment not present */ {
        ##NP(new code segment selector);
    }
    if /* stack not large enough for return address */ {
        ##SS(0);
    }
    tempEIP = DEST(Offset);
    if target mode == Compatibility mode {
        tempEIP = tempEIP && 0x0000_0000_FFFF_FFFF;
    }
    if OperandSize == 16 {
        tempEIP = tempEIP && 0x0000_FFFF; // clear upper 16 bits
    }
    if (IA32_EFER.LMA == 0 || target mode == Compatibility mode) && (/* tempEIP outside new code segment limit */)
        ##GP(0);
    }
    if /* tempEIP is non-canonical */ {
        ##GP(0);
    }
    if ShadowStackEnabled(CPL) {
        if OperandSize == 32 {
            tempPushLIP = CSBASE + EIP;
        } else {
            if OperandSize == 16 {
                tempPushLIP = CSBASE + IP;
            } else { // OperandSize == 64
                tempPushLIP = RIP;
            }
        }
        tempPushCS = CS;
    }
    if OperandSize == 32 {
        Push(CS); // padded with 16 high-order bits
        Push(EIP);
        CS = DEST(CodeSegmentSelector);
        // segment descriptor information also loaded
        CS(RPL) = CPL;
        EIP = tempEIP;
    } else {
        if OperandSize == 16 {
            Push(CS);
            Push(IP);
            CS = DEST(CodeSegmentSelector);
            // segment descriptor information also loaded
            CS(RPL) = CPL;
            EIP = tempEIP;
        } else { // OperandSize == 64
            Push(CS); // padded with 48 high-order bits
            Push(RIP);
            CS = DEST(CodeSegmentSelector);
            // segment descriptor information also loaded
            CS(RPL) = CPL;
            RIP = tempEIP;
        }
    }
    if ShadowStackEnabled(CPL) {
        if (IA32_EFER.LMA & DEST(CodeSegmentSelector).L) == 0 {
            // If target is legacy or compatibility mode then the SSP must be in low 4GB
            if SSP & 0xFFFF_FFFF_0000_0000 != 0 {
                ##GP(0);
            }
        }
        // align to 8 byte boundary if not already aligned
        tempSSP = SSP;
        /* Shadow_stack_store 4 bytes of 0 to (SSP – 4) */
        SSP = SSP & 0xFFFF_FFFF_FFFF_FFF8;
        ShadowStackPush8B(tempPushCS); // padded with 48 high-order bits of 0
        ShadowStackPush8B(tempPushLIP); // padded with 32 high-order bits of 0 for 32 bit LIP
        ShadowStackPush8B(tempSSP);
    }
    if EndbranchEnabled(CPL) {
        if CPL == 3 {
            IA32_U_CET.TRACKER = WAIT_FOR_ENDBRANCH;
            IA32_U_CET.SUPPRESS = 0;
        } else {
            IA32_S_CET.TRACKER = WAIT_FOR_ENDBRANCH;
            IA32_S_CET.SUPPRESS = 0;
        }
    }
}

'NONCONFORMING-CODE-SEGMENT {
    if L-Bit == 1 && D-BIT == 1 && IA32_EFER.LMA == 1 {
        ##GP(new code segment selector);
    }
    if (RPL > CPL) || (DPL != CPL) {
        ##GP(new code segment selector);
    }
    if /* segment not present */ {
        ##NP(new code segment selector);
    }
    if /* stack not large enough for return address */ {
        ##SS(0);
    }
    tempEIP = DEST(Offset);
    if target mode == Compatibility mode {
        tempEIP = tempEIP && 0x0000_0000_FFFF_FFFF;
    }
    if OperandSize == 16 {
        tempEIP = tempEIP && 0x0000_FFFF; // clear upper 16 bits
    }
    if (IA32_EFER.LMA == 0 || target mode == Compatibility mode) && /* tempEIP outside new code segment limit */ {
        ##GP(0);
    }
    if /* tempEIP is non-canonical */ {
        ##GP(0);
    }
    if ShadowStackEnabled(CPL) {
        if IA32_EFER.LMA && CS.L {
            tempPushLIP = RIP;
        } else {
            tempPushLIP = CSBASE + EIP;
        }
        tempPushCS = CS;
    }
    if OperandSize == 32 {
        Push(CS); // padded with 16 high-order bits
        Push(EIP);
        CS = DEST(CodeSegmentSelector);
        // segment descriptor information also loaded
        CS(RPL) = CPL;
        EIP = tempEIP;
    } else {
        if OperandSize == 16 {
            Push(CS);
            Push(IP);
            CS = DEST(CodeSegmentSelector);
            // segment descriptor information also loaded
            CS(RPL) = CPL;
            EIP = tempEIP;
        } else { // OperandSize == 64
            Push(CS); // padded with 48 high-order bits
            Push(RIP);
            CS = DEST(CodeSegmentSelector);
            // segment descriptor information also loaded
            CS(RPL) = CPL;
            RIP = tempEIP;
        }
    }
    if ShadowStackEnabled(CPL) {
        if (IA32_EFER.LMA && DEST(CodeSegmentSelector).L) == 0 {
            // if target is legacy or compatibility mode then the SSP must be in low 4GB
            if SSP & 0xFFFF_FFFF_0000_0000 != 0 {
                ##GP(0);
            }
        }
        // align to 8 byte boundary if not already aligned
        tempSSP = SSP;
        /* Shadow_stack_store 4 bytes of 0 to (SSP – 4) */
        SSP = SSP & 0xFFFF_FFFF_FFFF_FFF8;
        ShadowStackPush8B(tempPushCS); // padded with 48 high-order 0 bits
        ShadowStackPush8B(tempPushLIP); // padded 32 high-order bits of 0 for 32 bit LIP
        ShadowStackPush8B(tempSSP);
    }
    if EndbranchEnabled(CPL) {
        if CPL == 3 {
            IA32_U_CET.TRACKER = WAIT_FOR_ENDBRANCH;
            IA32_U_CET.SUPPRESS = 0;
        } else {
            IA32_S_CET.TRACKER = WAIT_FOR_ENDBRANCH;
            IA32_S_CET.SUPPRESS = 0;
        }
    }
}

'CALL-GATE {
    if call gate (DPL < CPL) || (RPL > DPL) {
        ##GP(call-gate selector);
    }
    if /* call gate not present */ {
        ##NP(call-gate selector);
    }
    if /* call-gate code-segment selector is NULL */ {
        ##GP(0);
    }
    if /* call-gate code-segment selector index is outside descriptor table limits */ {
        ##GP(call-gate code-segment selector);
    }
    /* Read call-gate code-segment descriptor; */
    if /* call-gate code-segment descriptor does not indicate a code segment */
            || call-gate code-segment descriptor DPL > CPL {
        ##GP(call-gate code-segment selector);
    }
    if IA32_EFER.LMA == 1 && (/* call-gate code-segment descriptor is
            not a 64-bit code segment */ || /* call-gate code-segment descriptor has both L-bit and D-bit set */) {
        ##GP(call-gate code-segment selector);
    }
    if /* call-gate code segment not present */ {
        ##NP(call-gate code-segment selector);
    }
    if /* call-gate code segment is non-conforming */ && DPL < CPL {
        goto 'MORE_PRIVILEGE;
    } else {
        goto 'SAME_PRIVILEGE;
    }
}

'MORE_PRIVILEGE {
    if /* current TSS is 32-bit */ {
        TSSstackAddress = (new code-segment DPL ∗ 8) + 4;
        if (TSSstackAddress + 5) > current TSS limit {
            ##TS(current TSS selector);
        }
        NewSS = 2 bytes loaded from (TSS base + TSSstackAddress + 4);
        NewESP = 4 bytes loaded from (TSS base + TSSstackAddress);
    } else {
        if /* current TSS is 16-bit */ {
            TSSstackAddress = (new code-segment DPL ∗ 4) + 2;
            if (TSSstackAddress + 3) > current TSS limit {
                ##TS(current TSS selector);
            }
            NewSS = 2 bytes loaded from (TSS base + TSSstackAddress + 2);
            NewESP = 2 bytes loaded from (TSS base + TSSstackAddress);
        } else { // current TSS is 64-bit
            TSSstackAddress = (new code-segment DPL ∗ 8) + 4;
            if (TSSstackAddress + 7) > current TSS limit {
                ##TS(current TSS selector);
            }
            NewSS = new code-segment DPL; // NULL selector with RPL = new CPL
            NewRSP = 8 bytes loaded from (current TSS base + TSSstackAddress);
        }
    }
    if IA32_EFER.LMA == 0 && /* NewSS is NULL */ {
        ##TS(NewSS);
    }
    /* Read new stack-segment descriptor; */
    if IA32_EFER.LMA == 0 && (NewSS RPL != new code-segment DPL
            || new stack-segment DPL != new code-segment DPL || /* new stack segment is not a
            writable data segment */) {
        ##TS(NewSS);
    }
    if IA32_EFER.LMA == 0 && /* new stack segment not present */ {
        ##SS(NewSS);
    }
    if CallGateSize == 32 {
        if /* new stack does not have room for parameters plus 16 bytes */ {
            ##SS(NewSS);
        }
        if /* CallGate(InstructionPointer) not within new code-segment limit */ {
            ##GP(0);
        }
        SS = newSS; // segment descriptor information also loaded
        ESP = newESP;
        CS:EIP = CallGate(CS:InstructionPointer);
        // segment descriptor information also loaded
        Push(oldSS:oldESP); // from calling procedure
        temp = /* parameter count from call gate, masked to 5 bits */;
        Push(parameters from calling procedure’s stack, temp);
        Push(oldCS:oldEIP); // return address to calling procedure
    } else {
        if CallGateSize == 16 {
            if /* new stack does not have room for parameters plus 8 bytes */ {
                ##SS(NewSS);
            }
            if /* (CallGate(InstructionPointer) && 0xFFFF) not in new code-segment limit */ {
                ##GP(0);
            }
            SS = newSS; // segment descriptor information also loaded
            ESP = newESP;
            CS:IP = CallGate(CS:InstructionPointer);
            // segment descriptor information also loaded
            Push(oldSS:oldESP); // From calling procedure
            temp = /* parameter count from call gate, masked to 5 bits */;
            Push(parameters from calling procedure’s stack, temp);
            Push(oldCS:oldEIP); // Return address to calling procedure
        } else { // CallGateSize == 64
            if /* pushing 32 bytes on the stack would use a non-canonical address */ {
                ##SS(NewSS);
            }
            if /* CallGate(InstructionPointer) is non-canonical */ {
                ##GP(0);
            }
            SS = NewSS; // NewSS is NULL
            RSP = NewESP;
            CS:IP = CallGate(CS:InstructionPointer);
            // segment descriptor information also loaded
            Push(oldSS:oldESP); // from calling procedure
            Push(oldCS:oldEIP); // return address to calling procedure
        }
    }
    if ShadowStackEnabled(CPL) && CPL == 3 {
        if IA32_EFER.LMA == 0 {
            IA32_PL3_SSP = SSP;
        } else { // adjust so bits 63:N get the value of bit N–1, where N is the CPU’s maximum linear-address width
        IA32_PL3_SSP = LA_adjust(SSP);
        }
    }
    CPL = CodeSegment(DPL)
    CS(RPL) = CPL
    if ShadowStackEnabled(CPL) {
        oldSSP = SSP;
        SSP = IA32_PLi_SSP; // where i is the CPL
        if SSP & 0x07 != 0 // if SSP not aligned to 8 bytes then #GP {
            ##GP(0);
        }
        // token and CS:LIP:oldSSP pushed on shadow stack must be contained in a naturally aligned 32-byte region
        if (SSP & ~0x1F) != ((SSP – 24) & ~0x1F) {
            ##GP(0);
        }
        if (IA32_EFER.LMA and CS.L) == 0 && SSP[63:32] != 0 {
            ##GP(0);
        }
        expected_token_value = SSP;
        // busy bit - bit position 0 - must be clear
        new_token_value = SSP | BUSY_BIT;
        // set the busy bit
        if shadow_stack_lock_cmpxchg8b(SSP, new_token_value, expected_token_value) != expected_token_value {
            ##GP(0);
        }
        if oldSS.DPL != 3 {
            // these stack pushes should not cause faults, VM exits, or data breakpoints
            // such events will apply to the earlier accesses to the token, which is in the same naturally aligned 32-byte region
            ShadowStackPush8B(oldCS); // padded with 48 high-order bits of 0
            ShadowStackPush8B(oldCSBASE+oldRIP); // padded with 32 high-order bits of 0 for 32 bit LIP
            ShadowStackPush8B(oldSSP);
        }
    }
    if EndbranchEnabled(CPL) {
        IA32_S_CET.TRACKER = WAIT_FOR_ENDBRANCH;
        IA32_S_CET.SUPPRESS = 0;
    }
}

'SAME_PRIVILEGE {
    if CallGateSize == 32 {
        if /* stack does not have room for 8 bytes */ {
            ##SS(0);
        }
        if /* CallGate(InstructionPointer) not within code segment limit */ {
            ##GP(0);
        }
        CS:EIP = CallGate(CS:EIP); // segment descriptor information also loaded
        Push(oldCS:oldEIP); // return address to calling procedure
    } else {
        if CallGateSize == 16 {
            if /* stack does not have room for 4 bytes */ {
                ##SS(0);
            }
            if /* CallGate(InstructionPointer) not within code segment limit */ {
                ##GP(0);
            }
            CS:IP = CallGate(CS:instruction pointer);
            // segment descriptor information also loaded
            Push(oldCS:oldIP); // return address to calling procedure
        } else { // CallGateSize == 64
            if /* pushing 16 bytes on the stack touches non-canonical addresses */ {
                ##SS(0);
            }
            if /* RIP non-canonical */ {
                ##GP(0);
            }
            CS:IP = CallGate(CS:instruction pointer);
            // segment descriptor information also loaded
            Push(oldCS:oldIP); // return address to calling procedure
        }
    }
    CS(RPL) = CPL;
    if ShadowStackEnabled(CPL) {
        // align to next 8 byte boundary
        tempSSP = SSP;
        /* Shadow_stack_store 4 bytes of 0 to (SSP – 4) */
        SSP = SSP & 0xFFFF_FFFF_FFFF_FFF8;
        // push cs:lip:ssp on shadow stack
        ShadowStackPush8B(oldCS); // padded with 48 high-order bits of 0
        ShadowStackPush8B(oldCSBASE + oldRIP); // padded with 32 high-order bits of 0 for 32 bit LIP
        ShadowStackPush8B(tempSSP);
    }
    if EndbranchEnabled(CPL) {
        if CPL == 3 {
            IA32_U_CET.TRACKER = WAIT_FOR_ENDBRANCH;
            IA32_U_CET.SUPPRESS = 0;
        } else {
            IA32_S_CET.TRACKER = WAIT_FOR_ENDBRANCH;
            IA32_S_CET.SUPPRESS = 0;
        }
    }
}

'TASK_GATE {
    if task gate DPL < CPL || RPL {
        ##GP(task gate selector);
    }
    if /* task gate not present */ {
        ##NP(task gate selector);
    }
    /* Read the TSS segment selector in the task-gate descriptor; */
    if /* TSS segment selector local/global bit is set to local */
            || /* index not within GDT limits */ {
        ##GP(TSS selector);
    }
    /* Access TSS descriptor in GDT; */
    if /* descriptor is not a TSS segment */ {
        ##GP(TSS selector);
    }
    if /* TSS descriptor specifies that the TSS is busy */ {
        ##GP(TSS selector);
    }
    if /* TSS not present */ {
        ##NP(TSS selector);
    }
    /* SWITCH-TASKS (with nesting) to TSS; */
    if /* EIP not within code segment limit */ {
        ##GP(0);
    }
}

'TASK_STATE_SEGMENT {
    if TSS DPL < CPL || RPL
            || /* TSS descriptor indicates TSS not available */ {
        ##GP(TSS selector);
    }
    if /* TSS is not present */ {
        ##NP(TSS selector);
    }
    /* SWITCH-TASKS (with nesting) to TSS; */ {
    if /* EIP not within code segment limit */ {
        ##GP(0);
    }
}
```

## Flags Affected
All flags are affected if a task switch occurs; no flags are affected if a task switch does not occur.
