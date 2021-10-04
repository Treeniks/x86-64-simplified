# x86-64 Simplified
This is a very opinionated alternative Instruction Set Reference for the x86-64 Architecture written in [mdbook](https://github.com/rust-lang/mdBook).

The goal of this reference is to prioritize readability and simplicity over accuracy or completeness. Thus, for any project with stakes or anything that requires more complicated instructions, grab the [official Intel x86-64 Software Developer Manual](https://software.intel.com/content/www/us/en/develop/articles/intel-sdm.html) instead.

This reference is nowhere near finished. Most Instructions are missing and some of the ones that are included have only been slightly altered from the original Intel Manual. The end goal is to give simple examples and simplify the Operation Pseudocode enough to make it easy and understandable for Assembly beginners, who, for example, wouldn't care much about the specific opcode encoding of instructions.

From my personal experience using the Intel Manual, it is littered with inconsistencies and often annoyingly complicated tech-talk that will do nothing but confuse newbies, which is the reason I created this book.

[Rendered Book](https://treeniks.github.io/x86-64-simplified/)

## Summary

- [Prefix](src/prefix.md)
---
- [Registers](src/registers.md)
- [Flags](src/flags.md)
- [Condition Codes](src/condition-codes.md)
- [Instructions](src/instructions/README.md)
  - [Data Transfer Instructions](src/instructions/data-transfer-instructions/README.md)
    - [`MOV`](src/instructions/data-transfer-instructions/mov.md)
    - [`XCHG`](src/instructions/data-transfer-instructions/xchg.md)
    - [`PUSH`](src/instructions/data-transfer-instructions/push.md)
    - [`POP`](src/instructions/data-transfer-instructions/pop.md)
  - [Binary Arithmetic Instructions](src/instructions/binary-arithmetic-instructions/README.md)
    - [`ADD`](src/instructions/binary-arithmetic-instructions/add.md)
    - [`SUB`](src/instructions/binary-arithmetic-instructions/sub.md)
    - [`IMUL`](src/instructions/binary-arithmetic-instructions/imul.md)
    - [`MUL`](src/instructions/binary-arithmetic-instructions/mul.md)
    - [`IDIV`](src/instructions/binary-arithmetic-instructions/idiv.md)
    - [`DIV`](src/instructions/binary-arithmetic-instructions/div.md)
    - [`INC`](src/instructions/binary-arithmetic-instructions/inc.md)
    - [`DEC`](src/instructions/binary-arithmetic-instructions/dec.md)
    - [`NEG`](src/instructions/binary-arithmetic-instructions/neg.md)
    - [`CMP`](src/instructions/binary-arithmetic-instructions/cmp.md)
  - [Logical Instructions](src/instructions/logical-instructions/README.md)
    - [`AND`](src/instructions/logical-instructions/and.md)
    - [`OR`](src/instructions/logical-instructions/or.md)
    - [`XOR`](src/instructions/logical-instructions/xor.md)
    - [`NOT`](src/instructions/logical-instructions/not.md)
  - [Shift and Rotate Instructions](src/instructions/shift-and-rotate-instructions/README.md)
    - [`SAL`|`SAR`|`SHL`|`SHR`](src/instructions/shift-and-rotate-instructions/sal,sar,shl,shr.md)
  - [Bit and Byte Instructions](src/instructions/bit-and-byte-instructions/README.md)
    - [`TEST`](src/instructions/bit-and-byte-instructions/test.md)
  - [Control Transfer Instructions](src/instructions/control-transfer-instructions/README.md)
    - [`JMP`](src/instructions/control-transfer-instructions/jmp.md)
    - [`Jcc`](src/instructions/control-transfer-instructions/jcc.md)
    - [`CALL`](src/instructions/control-transfer-instructions/call.md)
    - [`RET`](src/instructions/control-transfer-instructions/ret.md)
  - [Miscellaneous Instructions](src/instructions/miscellaneous-instructions/README.md)
    - [`LEA`](src/instructions/miscellaneous-instructions/lea.md)
    - [`NOP`](src/instructions/miscellaneous-instructions/nop.md)
- [System V ABI](src/system-v-abi.md)
