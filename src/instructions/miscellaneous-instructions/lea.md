# `LEA`
Load Effective Address

| Instruction  | Description                              |
| :----------- | :--------------------------------------- |
| `LEA r16, m` | Store effective address for `m` in `r16` |
| `LEA r32, m` | Store effective address for `m` in `r32` |
| `LEA r64, m` | Store effective address for `m` in `r64` |

## Description
Computes the effective address of the second operand (the source operand) and stores it in the first operand (destination operand). The source operand is a memory address (offset part) specified with one of the processors addressing modes; the destination operand is a general-purpose register. The address-size and operand-size attributes affect the action performed by this instruction, as shown in the following table. The operand-size attribute of the instruction is determined by the chosen register; the address-size attribute is determined by the attribute of the code segment.

| Operand Size | Address Size | Action Performed                                                                                                                     |
| :----------- | :----------- | :----------------------------------------------------------------------------------------------------------------------------------- |
| 16           | 16           | 16-bit effective address is calculated and stored in requested 16-bit register destination.                                          |
| 16           | 32           | 32-bit effective address is calculated. The lower 16 bits of the address are stored in the requested 16-bit register destination.    |
| 32           | 16           | 16-bit effective address is calculated. The 16-bit address is zero-extended and stored in the requested 32-bit register destination. |
| 32           | 32           | 32-bit effective address is calculated and stored in the requested 32-bit register destination.                                      |

Different assemblers may use different algorithms based on the size attribute and symbolic reference of the source operand.

In 64-bit mode, the instruction’s destination operand is governed by operand size attribute, the default operand size is 32 bits. Address calculation is governed by address size attribute, the default address size is 64-bits. In 64-bit mode, address size of 16 bits is not encodable. See the table below.

| Operand Size | Address Size | Action Performed                                                                                                                                                               |
| :----------- | :----------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 16           | 32           | 32-bit effective address is calculated (using `0x67` prefix). The lower 16 bits of the address are stored in the requested 16-bit register destination (using `0x66` prefix).  |
| 16           | 64           | 64-bit effective address is calculated (default address size). The lower 16 bits of the address are stored in the requested 16-bit register destination (using `0x66` prefix). |
| 32           | 32           | 32-bit effective address is calculated (using `0x67` prefix) and stored in the requested 32-bit register destination.                                                          |
| 32           | 64           | 64-bit effective address is calculated (default address size) and the lower 32 bits of the address are stored in the requested 32-bit register destination.                    |
| 64           | 32           | 32-bit effective address is calculated (using `0x67` prefix), zero-extended to 64-bits, and stored in the requested 64-bit register destination (using `REX.W`).               |
| 64           | 64           | 64-bit effective address is calculated (default address size) and all 64-bits of the address are stored in the requested 64-bit register destination (using `REX.W`).          |

## Operation
`DEST`: first operand\
`SRC`: second operand

### OperandSize = 16
```rust,ignore
if AddressSize == 16 {
    DEST = EffectiveAddress(SRC); // 16-bit address
} else if AddressSize == 32 {
    temp = EffectiveAddress(SRC); // 32-bit address
    DEST = temp[0..=15]; // 16-bit address
} else if AddressSize == 64 {
    temp = EffectiveAddress(SRC); // 64-bit address
    DEST = temp[0..=15]; // 16-bit address
}
```

### OperandSize = 32
```rust,ignore
if AddressSize == 16 {
    temp = EffectiveAddress(SRC); // 16-bit address
    DEST = ZeroExtend(temp); // 32-bit address
} else if AddressSize == 32 {
    DEST = EffectiveAddress(SRC); // 32-bit address
} else if AddressSize == 64 {
    temp = EffectiveAddress(SRC); // 64-bit address
    DEST = temp[0..=31]; // 16-bit address
}
```

### OperandSize = 64
```rust,ignore
if AddressSize == 64 {
    DEST = EffectiveAddress(SRC); // 64-bit address
}
```

## Flags Affected
None.
