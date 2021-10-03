# `MUL`
Unsigned Multiply

| Instruction | Description                                 |
| :---------- | :------------------------------------------ |
| `MUL r/m8`  | Unsigned multiply (`AX = AL * r/m8`)        |
| `MUL r/m16` | Unsigned multiply (`DX:AX = AX * r/m16`)    |
| `MUL r/m32` | Unsigned multiply (`EDX:EAX = EAX * r/m32`) |
| `MUL r/m64` | Unsigned multiply (`RDX:RAX = RAX * r/m64`) |

## Description
Performs an unsigned multiplication of the first operand (destination operand) and the second operand (source operand) and stores the result in the destination operand. The destination operand is an implied operand located in register `AL`, `AX` or `EAX` (depending on the size of the operand); the source operand is located in a general-purpose register or a memory location. The action of this instruction and the location of the result depends on the opcode and the operand size as shown in the table below.

The result is stored in register `AX`, register pair `DX:AX`, or register pair `EDX:EAX` (depending on the operand size), with the high-order bits of the product contained in register `AH`, `DX`, or `EDX`, respectively. If the high-order bits of the product are 0, the `CF` and `OF` flags are cleared; otherwise, the flags are set.

In 64-bit mode, the instruction’s default operation size is 32 bits. Use of the `REX.R` prefix permits access to additional registers (`R8`-`R15`). Use of the `REX.W` prefix promotes operation to 64 bits.

See the summary chart at the beginning of this section for encoding data and limits.

| Operand Size | Source 1 | Source 2 | Destination |
| :----------- | :------- | :------- | :---------- |
| Byte         | `AL`     | `r/m8`   | `AX`        |
| Word         | `AX`     | `r/m16`  | `DX:AX`     |
| Doubleword   | `EAX`    | `r/m32`  | `EDX:EAX`   |
| Quadword     | `RAX`    | `r/m64`  | `RDX:RAX`   |

## Operation
`SRC`: operand

### OperandSize = 8
```rust,ignore
AX = AL * SRC;
```

### OperandSize = 16
```rust,ignore
DX:AX = AX * SRC;
```

### OperandSize = 32
```rust,ignore
EDX:EAX = EAX * SRC;
```

### OperandSize = 64
```rust, ignore
RDX:RAX = RAX * SRC;
```

## Flags Affected
The `OF` and `CF` flags are set to 0 if the upper half of the result is 0; otherwise, they are set to 1. The `SF`, `ZF`, `AF`, and `PF` flags are undefined.
