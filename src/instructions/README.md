# Instructions
## How to read Instructions

## Instruction Set Summary
### Data Transfer Instructions
| Mnemonic          | Summary          |
| :---------------- | :--------------- |
| [`MOV`](mov.md)   | Move             |
| [`XCHG`](xchg.md) | Exchange         |
| [`PUSH`](push.md) | Push Onto Stack  |
| [`POP`](pop.md)   | Pop Off of Stack |

### Binary Arithmetic Instructions
| Mnemonic          | Summary           |
| :---------------- | :---------------- |
| [`ADD`](add.md)   | Integer Add       |
| [`SUB`](sub.md)   | Subtract          |
| [`IMUL`](imul.md) | Signed Multiply   |
| [`MUL`](mul.md)   | Unsigned Multiply |
| [`IDIV`](idiv.md) | Signed Divide     |
| [`DIV`](div.md)   | Unsigned Divide   |
| [`INC`](inc.md)   | Increment         |
| [`DEC`](dec.md)   | Decrement         |
| [`NEG`](neg.md)   | Negate            |
| [`CMP`](cmp.md)   | Compare           |

### Logical Instructions
| Mnemonic        | Summary                              |
| :-------------- | :----------------------------------- |
| [`AND`](and.md) | Perform Bitwise Logical AND          |
| [`OR`](or.md)   | Perform Bitwise Logical OR           |
| [`XOR`](xor.md) | Perform Bitwise Logical Exclusive OR |
| [`NOT`](not.md) | Perform Bitwise Logical NOT          |

### Shift and Rotate Instructions
| Mnemonic                           | Summary                                  |
| :--------------------------------- | :--------------------------------------- |
| [`SAR`](sal,sar,shl,shr.md)        | Shift Arithmetic Right                   |
| [`SHR`](sal,sar,shl,shr.md)        | Shift Logical Right                      |
| [`SAL`\|`SHL`](sal,sar,shl,shr.md) | Shift Arithmetic Left/Shift Logical Left |

### Bit and Byte Instructions
| Mnemonic          | Summary         |
| :---------------- | :-------------- |
| [`TEST`](test.md) | Logical Compare |

### Control Transfer Instructions
| Mnemonic          | Summary        |
| :---------------- | :------------- |
| [`JMP`](jmp.md)   | Jump           |
| [`Jcc`](jcc.md)   | Jump if `cc`   |
| [`CALL`](call.md) | Call Procedure |
| [`RET`](ret.md)   | Return         |

### Miscellaneous Instructions
| Mnemonic        | Summary                |
| :-------------- | :--------------------- |
| [`LEA`](lea.md) | Load Effective Address |
| [`NOP`](nop.md) | No Operation           |



























