# `RET`
Return from Procedure

| Instruction | Description                                                       |
| :---------- | :---------------------------------------------------------------- |
| `RET`       | Near return to calling procedure                                  |
| `RET`       | Far return to calling procedure                                   |
|             |                                                                   |
| `RET imm16` | Near return to calling procedure and pop `imm16` bytes from stack |
| `RET imm16` | Far return to calling procedure and pop `imm16` bytes from stack  |

## Description
Transfers program control to a return address located on the top of the stack. The address is usually placed on the stack by a `CALL` instruction, and the return is made to the instruction that follows the `CALL` instruction.

The optional source operand specifies the number of stack bytes to be released after the return address is popped; the default is none. This operand can be used to release parameters from the stack that were passed to the called procedure and are no longer needed. It must be used when the `CALL` instruction used to switch to a new procedure uses a call gate with a non-zero word count to access the new procedure. Here, the source operand for the `RET` instruction must specify the same number of bytes as is specified in the word count field of the call gate.

The `RET` instruction can be used to execute three different types of returns:
* **Near return**\
  A return to a calling procedure within the current code segment (the segment currently pointed to by the `CS` register), sometimes referred to as an intrasegment return.
* **Far return**\
  A return to a calling procedure located in a different segment than the current code segment, sometimes referred to as an intersegment return.
* **Inter-privilege-level far return**\
  A far return to a different privilege level than that of the currently executing program or procedure.
The inter-privilege-level return type can only be executed in protected mode. See the section titled "Calling Procedures Using Call and RET" in Chapter 6 of the Intel® 64 and IA-32 Architectures Software Developer's Manual, Volume 1, for detailed information on near, far, and inter-privilege-level returns.

When executing a near return, the processor pops the return instruction pointer (offset) from the top of the stack into the `EIP` register and begins program execution at the new instruction pointer. The `CS` register is unchanged.

When executing a far return, the processor pops the return instruction pointer from the top of the stack into the `EIP` register, then pops the segment selector from the top of the stack into the `CS` register. The processor then begins program execution in the new code segment at the new instruction pointer.

The mechanics of an inter-privilege-level far return are similar to an intersegment return, except that the processor examines the privilege levels and access rights of the code and stack segments being returned to determine if the control transfer is allowed to be made. The `DS`, `ES`, `FS`, and `GS` segment registers are cleared by the `RET` instruction during an inter-privilege-level return if they refer to segments that are not allowed to be accessed at the new privilege level. Since a stack switch also occurs on an inter-privilege level return, the `ESP` and `SS` registers are loaded from the stack.

If parameters are passed to the called procedure during an inter-privilege level call, the optional source operand must be used with the `RET` instruction to release the parameters on the return. Here, the parameters are released both from the called procedure's stack and the calling procedure's stack (that is, the stack being returned to).

In 64-bit mode, the default operation size of this instruction is the stack-address size, i.e. 64 bits. This applies to near returns, not far returns; the default operation size of far returns is 32 bits.

Refer to Chapter 6, "Procedure Calls, Interrupts, and Exceptions" and Chapter 18, "Control-Flow Enforcement Technology (CET)" in the Intel® 64 and IA-32 Architectures Software Developer's Manual, Volume 1 for CET details.

### Instruction ordering
Instructions following a far return may be fetched from memory before earlier instructions complete execution, but they will not execute (even speculatively) until all instructions prior to the far return have completed execution (the later instructions may execute before data stored by the earlier instructions have become globally visible).

Unlike near indirect `CALL` and near indirect `JMP`, the processor will not speculatively execute the next sequential instruction after a near `RET` unless that instruction is also the target of a jump or is a target in a branch predictor.

## Operation
```rust,ignore
// near return
if instruction == near return {
    if OperandSize == 32 {
        if /* top 4 bytes of stack not within stack limits */ {
            ##SS(0);
        }
        EIP = Pop();
        if ShadowStackEnabled(CPL) {
            tempSsEIP = ShadowStackPop4B();
            if EIP != TempSsEIP {
                ##CP(NEAR_RET);
            }
        }
    } else {
        if OperandSize == 64 {
            if /* top 8 bytes of stack not within stack limits */ {
                ##SS(0);
            }
            RIP = Pop();
            if ShadowStackEnabled(CPL) {
                tempSsEIP = ShadowStackPop8B();
                if RIP != tempSsEIP {
                    ##CP(NEAR_RET);
                }
            }
        } else { // OperandSize == 16
            if /* top 2 bytes of stack not within stack limits */ {
                ##SS(0);
            }
            tempEIP = Pop();
            tempEIP = tempEIP && 0x0000_FFFF;
            if /* tempEIP not within code segment limits */ {
                ##GP(0);
            }
            EIP = tempEIP;
            if ShadowStackEnabled(CPL) {
                tempSsEip = ShadowStackPop4B();
                if EIP != tempSsEIP {
                    ##CP(NEAR_RET);
                }
            }
        }
    }
    if /* instruction has immediate operand */ {
        // release parameters from stack
        if StackAddressSize == 32 {
            ESP = ESP + SRC;
        } else {
            if StackAddressSize == 64 {
                RSP = RSP + SRC;
            } else { // StackAddressSize == 16
                SP = SP + SRC;
            }
        }
    }
}

// real-address mode or virtual-8086 mode
if ((PE == 0) || (PE == 1 && VM == 1)) && instruction == far return {
    if OperandSize == 32 {
        if /* top 8 bytes of stack not within stack limits */ {
            ##SS(0);
        }
        EIP = Pop();
        CS = Pop(); // 32-bit pop, high-order 16 bits discarded
    } else { // OperandSize == 16
        if /* top 4 bytes of stack not within stack limits */ {
            ##SS(0);
        }
        tempEIP = Pop();
        tempEIP = tempEIP && 0x0000_FFFF;
        if /* tempEIP not within code segment limits */ {
            ##GP(0);
        }
        EIP = tempEIP;
        CS = Pop(); // 16-bit pop
    }
    if /* instruction has immediate operand */ {
        // release parameters from stack
        SP = SP + (SRC && 0xFFFF);
    }
}

// protected mode, not virtual-8086 mode
if (PE == 1 && VM == 0 && IA32_EFER.LMA == 0) && instruction == far return {
    if OperandSize == 32 {
        if /* second doubleword on stack is not within stack limits */ {
            ##SS(0);
        }
    } else { // OperandSize == 16
        if /* second word on stack is not within stack limits */ {
            ##SS(0);
        }
    }
    if /* return code segment selector is NULL */ {
        ##GP(0);
    }
    if /* return code segment selector addresses descriptor beyond descriptor table limit */ {
        ##GP(selector);
    }
    /* Obtain descriptor to which return code segment selector points from descriptor table; */
    if /* return code segment descriptor is not a code segment */ {
        ##GP(selector);
    }
    if return code segment selector RPL < CPL {
        ##GP(selector);
    }
    if /* return code segment descriptor is conforming */
            && return code segment DPL > return code segment selector RPL {
        ##GP(selector);
    }
    if /* return code segment descriptor is non-conforming */
            && return code segment DPL != return code segment selector RPL {
        ##GP(selector);
    }
    if /* return code segment descriptor is not present */ {
        ##NP(selector);
    }
    if return code segment selector RPL > CPL {
        goto 'RETURN_TO_OUTER_PRIVILEGE_LEVEL;
    } else {
        goto 'RETURN_TO_SAME_PRIVILEGE_LEVEL;
    }
}

'RETURN_TO_SAME_PRIVILEGE_LEVEL {
    if /* the return instruction pointer is not within the return code segment limit */ {
        ##GP(0);
    }
    if OperandSize == 32 {
        EIP = Pop();
        CS = Pop(); // 32-bit pop, high-order 16 bits discarded
    } else { // OperandSize == 16
        EIP = Pop();
        EIP = EIP && 0x0000_FFFF;
        CS = Pop(); // 16-bit pop
    }
    if /* instruction has immediate operand */ {
        // release parameters from stack
        if StackAddressSize == 32 {
            ESP = ESP + SRC;
        } else { // StackAddressSize == 16
            SP = SP + SRC;
        }
    }
    if ShadowStackEnabled(CPL) {
        // SSP must be 8 byte aligned
        if SSP && 0x7 != 0 {
            ##CP(FAR-RET/IRET);
        }
        tempSsCS = shadow_stack_load 8 bytes from SSP+16;
        tempSsLIP = shadow_stack_load 8 bytes from SSP+8;
        prevSSP = shadow_stack_load 8 bytes from SSP;
        SSP = SSP + 24;
        // do a 64 bit-compare to check if any bits beyond bit 15 are set
        tempCS = CS; // zero pad to 64 bit
        if tempCS != tempSsCS {
            ##CP(FAR-RET/IRET);
        }
        // do a 64 bit-compare; pad CSBASE+RIP with 0 for 32 bit LIP
        if CSBASE + RIP != tempSsLIP {
            ##CP(FAR-RET/IRET);
        }
        // prevSSP must be 4 byte aligned
        if prevSSP && 0x3 != 0 {
            ##CP(FAR-RET/IRET);
        }
        // in legacy mode SSP must be in low 4GB
        if prevSSP[32..=63] != 0 {
            ##GP(0);
        }
        SSP = prevSSP;
    }
}

'RETURN_TO_OUTER_PRIVILEGE_LEVEL {
    if /* top (16 + SRC) bytes of stack are not within stack limits (OperandSize == 32) */
            || /* top (8 + SRC) bytes of stack are not within stack limits (OperandSize == 16) */ {
        ##SS(0);
    }
    /* Read return segment selector; */
    if /* stack segment selector is NULL */ {
        ##GP(0);
    }
    if /* return stack segment selector index is not within its descriptor table limits */ {
        ##GP(selector);
    }
    /* Read segment descriptor pointed to by return segment selector; */
    if stack segment selector RPL != RPL of the return code segment selector
            || /* stack segment is not a writable data segment */
            || stack segment descriptor DPL != RPL of the return code segment selector {
        ##GP(selector);
    }
    if /* stack segment not present */ {
        ##SS(StackSegmentSelector);
    }
    if /* the return instruction pointer is not within the return code segment limit */ {
        ##GP(0);
    }
    if OperandSize == 32 {
        EIP = Pop();
        CS = Pop(); // 32-bit pop, high-order 16 bits discarded; segment descriptor loaded
        CS(RPL) = ReturnCodeSegmentSelector(RPL);
        if /* instruction has immediate operand */ {
            // release parameters from called procedure's stack
            if StackAddressSize == 32 {
                ESP = ESP + SRC;
            } else { // StackAddressSize == 16
                SP = SP + SRC;
            }
        }
        tempESP = Pop();
        tempSS = Pop(); // 32-bit pop, high-order 16 bits discarded; seg. descriptor loaded
    } else { // OperandSize == 16
        EIP = Pop();
        EIP = EIP && 0x0000_FFFF;
        CS = Pop(); // 16-bit pop; segment descriptor loaded
        CS(RPL) = ReturnCodeSegmentSelector(RPL);
        if /* instruction has immediate operand */ {
            // release parameters from called procedure's stack
            if StackAddressSize == 32 {
                ESP = ESP + SRC;
            } else { // StackAddressSize == 16
                SP = SP + SRC;
            }
        }
        tempESP = Pop();
        tempSS = Pop(); // 16-bit pop; segment descriptor loaded
    }
    if ShadowStackEnabled(CPL) {
        // check if 8 byte aligned
        if SSP && 0x7 != 0 {
            ##CP(FAR-RET/IRET);
        }
        if ReturnCodeSegmentSelector(RPL) != 3 {
            tempSsCS = shadow_stack_load 8 bytes from SSP+16;
            tempSsLIP = shadow_stack_load 8 bytes from SSP+8;
            tempSSP = shadow_stack_load 8 bytes from SSP;
            SSP = SSP + 24;
            // do 64 bit compare to detect bits beyond 15 being set
            tempCS = CS; // zero extended to 64 bit
            if tempCS != tempSsCS {
                ##CP(FAR-RET/IRET);
            }
            // do 64 bit compare; pad CSBASE+RIP with 0 for 32 bit LA
            if CSBASE + RIP != tempSsLIP {
                ##CP(FAR-RET/IRET);
            }
            // check if 4 byte aligned
            if tempSSP && 0x3 != 0 {
                ##CP(FAR-RET/IRET);
            }
        }
    }
    tempOldCPL = CPL;

    CPL = ReturnCodeSegmentSelector(RPL);
    ESP = tempESP;
    SS = tempSS;
    tempOldSSP = SSP;
    if ShadowStackEnabled(CPL) {
        if CPL == 3 {
            tempSSP = IA32_PL3_SSP;
        }
        if tempSSP[32..=63] != 0 {
            ##GP(0);
        }
        SSP = tempSSP;
    }
    // Now past all faulting points; safe to free the token. The token free is done using the old SSP
    // and using a supervisor override as old CPL was a supervisor privilege level
    if ShadowStackEnabled(tempOldCPL) {
        expected_token_value = tempOldSSP | BUSY_BIT; // busy bit - bit position 0 - must be set
        new_token_value = tempOldSSP; // clear the busy bit
        shadow_stack_lock_cmpxchg8b(tempOldSSP, new_token_value, expected_token_value);
    }
    for SegReg in (ES, FS, GS, and DS) {
        tempDesc = /* descriptor cache for SegReg */; // hidden part of segment register
        if SegmentSelector == NULL || (tempDesc(DPL) < CPL && tempDesc(Type) is (/* data or non-conforming code */)) {
            // segment register invalid
            SegmentSelector = 0; // segment selector becomes null
        }
    }
    if /* instruction has immediate operand */ {
        // release parameters from calling procedure's stack
        if StackAddressSize == 32 {
            ESP = ESP + SRC;
        } else { // StackAddressSize == 16
            SP = SP + SRC;
        }
    }

    // IA-32e Mode
    if (PE == 1 && VM == 0 && IA32_EFER.LMA == 1) && instruction == far return {
        if OperandSize == 32 {
            if /* second doubleword on stack is not within stack limits */ {
                ##SS(0);
            }
            if /* first or second doubleword on stack is not in canonical space */ {
                ##SS(0);
            }
        } else {
            if OperandSize == 16 {
                if /* second word on stack is not within stack limits */ {
                    ##SS(0);
                }
                if /* first or second word on stack is not in canonical space */ {
                    ##SS(0);
                }
            } else { // OperandSize == 64
                if /* first or second quadword on stack is not in canonical space */ {
                    ##SS(0);
                }
            }
        }
        if /* return code segment selector is NULL */ {
            ##GP(0);
        }
        if /* return code segment selector addresses descriptor beyond descriptor table limit */ {
            ##GP(selector);
        }
        if /* return code segment selector addresses descriptor in non-canonical space */ {
            ##GP(selector);
        }
        /* obtain descriptor to which return code segment selector points from descriptor table; */
        if /* return code segment descriptor is not a code segment */ {
            ##GP(selector);
        }
        if /* return code segment descriptor has L-bit == 1 and D-bit == 1 */ {
            ##GP(selector);
        }
        if return code segment selector RPL < CPL {
            ##GP(selector);
        }
        if /* return code segment descriptor is conforming */
                && return code segment DPL > return code segment selector RPL {
            ##GP(selector);
        }
        if /* return code segment descriptor is non-conforming */
                && return code segment DPL != return code segment selector RPL {
            ##GP(selector);
        }
        if /* return code segment descriptor is not present */ {
            ##NP(selector);
        }
        if return code segment selector RPL > CPL {
            goto 'IA_32E_MODE_RETURN_TO_OUTER_PRIVILEGE_LEVEL;
        } else {
            goto 'IA_32E_MODE_RETURN_TO_SAME_PRIVILEGE_LEVEL;
        }
    }
}

'IA_32E_MODE_RETURN_TO_SAME_PRIVILEGE_LEVEL {
    if /* the return instruction pointer is not within the return code segment limit */ {
        ##GP(0);
    }
    if /* the return instruction pointer is not within canonical address space */ {
        ##GP(0);
    }
    if OperandSize == 32 {
        EIP = Pop();
        CS = Pop(); // 32-bit pop, high-order 16 bits discarded
    } else {
        if OperandSize == 16 {
            EIP = Pop();
            EIP = EIP && 0x0000_FFFF;
            CS = Pop(); // 16-bit pop
        } else { // OperandSize == 64
            RIP = Pop();
            CS = Pop(); // 64-bit pop, high-order 48 bits discarded
        }
    }
    if /* instruction has immediate operand */ {
        // release parameters from stack
        if StackAddressSize == 32 {
            ESP = ESP + SRC;
        } else {
            if StackAddressSize == 16 {
                SP = SP + SRC;
            } else { // StackAddressSize == 64
                RSP = RSP + SRC;
            }
        }
    }
    if ShadowStackEnabled(CPL) {
        if SSP && 0x7 != 0 {
            // check if aligned to 8 bytes
            ##CP(FAR-RET/IRET);
        }
        tempSsCS = shadow_stack_load 8 bytes from SSP+16;
        tempSsLIP = shadow_stack_load 8 bytes from SSP+8;
        tempSSP = shadow_stack_load 8 bytes from SSP;
        SSP = SSP + 24;
        tempCS = CS; // zero padded to 64 bit
        if tempCS != tempSsCS {
            // 64 bit compare; CS zero padded to 64 bits
            ##CP(FAR-RET/IRET);
        }
        if CSBASE + RIP != tempSsLIP {
            // 64 bit compare
            ##CP(FAR-RET/IRET);
        }
        if tempSSP && 0x3 != 0 {
            // check if aligned to 4 bytes
            ##CP(FAR-RET/IRET);
        }
        if (CS.L == 0 && tempSSP[32..=63] != 0)
                || (CS.L == 1 && /* tempSSP is not canonical relative to the current paging mode */) {
            ##GP(0);
        }
        SSP = tempSSP;
    }
}

'IA_32E_MODE_RETURN_TO_OUTER_PRIVILEGE_LEVEL {
    if /* top (16 + SRC) bytes of stack are not within stack limits (OperandSize == 32) */
            || /* top (8 + SRC) bytes of stack are not within stack limits (OperandSize == 16) */ {
        ##SS(0);
    }
    if /* top (16 + SRC) bytes of stack are not in canonical address space (OperandSize == 32) */
            || /* top (8 + SRC) bytes of stack are not in canonical address space (OperandSize == 16) */
            || /* top (32 + SRC) bytes of stack are not in canonical address space (OperandSize == 64) */ {
        ##SS(0);
    }
    /* Read return stack segment selector; */
    if /* stack segment selector is NULL */ {
        if new CS descriptor L-bit == 0 {
            ##GP(selector);
        }
        if stack segment selector RPL == 3 {
            ##GP(selector);
        }
    }
    if /* return stack segment descriptor is not within descriptor table limits */ {
        ##GP(selector);
    }
    if /* return stack segment descriptor is in non-canonical address space */ {
        ##GP(selector);
    }
    /* Read segment descriptor pointed to by return segment selector; */
    if stack segment selector RPL != RPL of the return code segment selector
            || /* stack segment is not a writable data segment */
            || stack segment descriptor DPL != RPL of the return code segment selector {
        ##GP(selector);
    }
    if /* stack segment not present */ {
        ##SS(StackSegmentSelector);
    }
    if /* the return instruction pointer is not within the return code segment limit */ {
        ##GP(0);
    }
    if /* the return instruction pointer is not within canonical address space */ {
        ##GP(0);
    }
    if OperandSize == 32 {
        EIP = Pop();
        CS = Pop(); // 32-bit pop, high-order 16 bits discarded, segment descriptor loaded
        CS(RPL) = ReturnCodeSegmentSelector(RPL);
        if /* instruction has immediate operand */ {
            // release parameters from called procedure's stack
            if StackAddressSize == 32 {
                ESP = ESP + SRC;
            } else {
                if StackAddressSize == 16 {
                    SP = SP + SRC;
                } else { // StackAddressSize == 64
                    RSP = RSP + SRC;
                }
            }
        }
        tempESP = Pop();
        tempSS = Pop(); // 32-bit pop, high-order 16 bits discarded, segment descriptor loaded
    } else {
        if OperandSize == 16 {
            EIP = Pop();
            EIP = EIP && 0x0000_FFFF;
            CS = Pop(); // 16-bit pop; segment descriptor loaded
            CS(RPL) = ReturnCodeSegmentSelector(RPL);
            if /* instruction has immediate operand */ {
                // release parameters from called procedure's stack
                if StackAddressSize == 32 {
                    ESP = ESP + SRC;
                } else {
                    if StackAddressSize == 16 {
                        SP = SP + SRC;
                    } else { // StackAddressSize == 64
                        RSP = RSP + SRC;
                    }
                }
            }
            tempESP = Pop();
            tempSS = Pop(); // 16-bit pop; segment descriptor loaded
        } else { // OperandSize = 64
            RIP = Pop();
            CS = Pop(); // 64-bit pop; high-order 48 bits discarded; seg. descriptor loaded
            CS(RPL) = ReturnCodeSegmentSelector(RPL);
            if /* instruction has immediate operand */ {
                // Release parameters from called procedure's stack
                RSP = RSP + SRC;
            }
            tempESP = Pop();
            tempSS = Pop(); // 64-bit pop; high-order 48 bits discarded; seg. desc. loaded
        }
    }

    if ShadowStackEnabled(CPL) {
        // check if 8 byte aligned
        if SSP && 0x7 != 0 {
            ##CP(FAR-RET/IRET);
        }
        if ReturnCodeSegmentSelector(RPL) != 3 {
            tempSsCS = shadow_stack_load 8 bytes from SSP+16;
            tempSsLIP = shadow_stack_load 8 bytes from SSP+8;
            tempSSP = shadow_stack_load 8 bytes from SSP;
            SSP = SSP + 24;
            // do 64 bit compare to detect bits beyond 15 being set
            tempCS = CS; // zero padded to 64 bit
            if tempCS != tempSsCS {
                ##CP(FAR-RET/IRET);
            }
            // do 64 bit compare; pad CSBASE+RIP with 0 for 32 bit LIP
            if CSBASE + RIP != tempSsLIP {
                ##CP(FAR-RET/IRET);
            }
            // check if 4 byte aligned
            if tempSSP && 0x3 != 0 {
                ##CP(FAR-RET/IRET);
            }
        }
    }
    tempOldCPL = CPL;
    CPL = ReturnCodeSegmentSelector(RPL);
    ESP = tempESP;
    SS = tempSS;
    tempOldSSP = SSP;
    if ShadowStackEnabled(CPL) {
        if CPL == 3 {
            tempSSP = IA32_PL3_SSP;
        }
        if (CS.L == 0 && tempSSP[32..=63] != 0)
                || (CS.L == 1 && /* tempSSP is not canonical relative to the current paging mode */) {
            ##GP(0);
        }
        SSP = tempSSP;
    }
    // Now past all faulting points; safe to free the token. The token free is done using the old SSP
    // and using a supervisor override as old CPL was a supervisor privilege level
    if ShadowStackEnabled(tempOldCPL) {
        expected_token_value = tempOldSSP | BUSY_BIT; // busy bit - bit position 0 - must be set
        new_token_value = tempOldSSP; // clear the busy bit
        shadow_stack_lock_cmpxchg8b(tempOldSSP, new_token_value, expected_token_value);
    }
    for each of segment register (ES, FS, GS, and DS) {
        if /* segment register points to data or non-conforming code segment */
                && CPL > segment descriptor DPL {
            // DPL in hidden part of segment register
            SegmentSelector = 0; // SegmentSelector invalid
        }
    }
    if /* instruction has immediate operand */ {
        // release parameters from calling procedure's stack
        if StackAddressSize == 32 {
            ESP = ESP + SRC;
        } else {
            if StackAddressSize == 16 {
                SP = SP + SRC;
            } else { // StackAddressSize == 64
                RSP = RSP + SRC;
            }
        }
    }
}
```

## Flags Affected
None.
