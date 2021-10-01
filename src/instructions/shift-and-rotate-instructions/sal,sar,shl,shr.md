# `SAL`|`SAR`|`SHL`|`SHR`
Shift

`SAL`: Shift Arithmetic Left\
`SAR`: Shift Arithmetic Right

`SHL`: Shift Logical Left\
`SHR`: Shift Logical Right

Right Arithmetic Shifts preserve the sign by filling with the same value as the sign bit.\
Right Logical Shift always fill with 0s.

Left Shifts always fill with 0s, thus `SAL` and `SHL` are synonymous.

## `SAL`|`SHL`
`SAL` and `SHL` are synonymous.

| Instruction           | Description                                             |
| :-------------------- | :------------------------------------------------------ |
| `SAL/SHL r/m8, imm8`  | Shift `r/m8` to the left, `imm8` times, filling with 0  |
| `SAL/SHL r/m16, imm8` | Shift `r/m16` to the left, `imm8` times, filling with 0 |
| `SAL/SHL r/m32, imm8` | Shift `r/m32` to the left, `imm8` times, filling with 0 |
| `SAL/SHL r/m64, imm8` | Shift `r/m64` to the left, `imm8` times, filling with 0 |
|                       |                                                         |
| `SAL/SHL r/m8, CL`    | Shift `r/m8` to the left, `CL` times, filling with 0    |
| `SAL/SHL r/m16, CL`   | Shift `r/m16` to the left, `CL` times, filling with 0   |
| `SAL/SHL r/m32, CL`   | Shift `r/m32` to the left, `CL` times, filling with 0   |
| `SAL/SHL r/m64, CL`   | Shift `r/m64` to the left, `CL` times, filling with 0   |

## `SAR`
| Instruction       | Description                                                   |
| :---------------- | :------------------------------------------------------------ |
| `SAR r/m8, imm8`  | Shift `r/m8` to the right, `imm8` times, preserving the sign  |
| `SAR r/m16, imm8` | Shift `r/m16` to the right, `imm8` times, preserving the sign |
| `SAR r/m32, imm8` | Shift `r/m32` to the right, `imm8` times, preserving the sign |
| `SAR r/m64, imm8` | Shift `r/m64` to the right, `imm8` times, preserving the sign |
|                   |                                                               |
| `SAR r/m8, CL`    | Shift `r/m8` to the right, `CL` times, preserving the sign    |
| `SAR r/m16, CL`   | Shift `r/m16` to the right, `CL` times, preserving the sign   |
| `SAR r/m32, CL`   | Shift `r/m32` to the right, `CL` times, preserving the sign   |
| `SAR r/m64, CL`   | Shift `r/m64` to the right, `CL` times, preserving the sign   |

## `SHR`
| Instruction       | Description                                              |
| :---------------- | :------------------------------------------------------- |
| `SHR r/m8, imm8`  | Shift `r/m8` to the right, `imm8` times, filling with 0  |
| `SHR r/m16, imm8` | Shift `r/m16` to the right, `imm8` times, filling with 0 |
| `SHR r/m32, imm8` | Shift `r/m32` to the right, `imm8` times, filling with 0 |
| `SHR r/m64, imm8` | Shift `r/m64` to the right, `imm8` times, filling with 0 |
|                   |                                                          |
| `SHR r/m8, CL`    | Shift `r/m8` to the right, `CL` times, filling with 0    |
| `SHR r/m16, CL`   | Shift `r/m16` to the right, `CL` times, filling with 0   |
| `SHR r/m32, CL`   | Shift `r/m32` to the right, `CL` times, filling with 0   |
| `SHR r/m64, CL`   | Shift `r/m64` to the right, `CL` times, filling with 0   |

## Operation
`DEST`: first operand\
`COUNT`: second operand\
`countMASK`: `0b00111111`<sup>=64</sup> for 64-bit operations, `0b00011111`<sup>=31</sup> otherwise

### `SAL`|`SHL`
```rust,no_run,noplayground,ignore
tempCOUNT = COUNT & countMASK;
while tempCOUNT != 0 {
    DEST = DEST * 2;
    tempCOUNT = tempCOUNT - 1;
}
```

### `SAR`
```rust,no_run,noplayground,ignore
tempCOUNT = COUNT & countMASK;
while tempCOUNT != 0 {
    DEST = DEST / 2; // signed divide, rounding toward negative infinity
    tempCOUNT = tempCOUNT - 1;
}
```

### `SHR`
```rust,no_run,noplayground,ignore
tempCOUNT = COUNT & countMASK;
while tempCOUNT != 0 {
    DEST = DEST / 2; // unsigned divide
    tempCOUNT = tempCOUNT - 1;
}
```

## Flags Affected
The `CF` flag contains the value of the last bit shifted out of the destination operand; it is undefined for `SHL` and `SHR` instructions where the count is greater than or equal to the size (in bits) of the destination operand. The `OF` flag is affected only for 1-bit shifts (see [Description](#description) below); otherwise, it is undefined. The `SF`, `ZF`, and `PF` flags are set according to the result. If the count is 0, the flags are not affected. For a non-zero count, the `AF` flag is undefined.

## Description
Shifts the bits in the first operand (destination operand) to the left or right by the number of bits specified in the second operand (count operand). Bits shifted beyond the destination operand boundary are first shifted into the `CF` flag, then discarded. At the end of the shift operation, the `CF` flag contains the last bit shifted out of the destination operand.

The destination operand can be a register or a memory location. The count operand can be an immediate value or the `CL` register. The count is masked to 5 bits (or 6 bits if in 64-bit mode and `REX.W` is used). The count range is limited to 0 to 31 (or 63 if 64-bit mode and `REX.W` is used). A special opcode encoding is provided for a count of 1.

The shift arithmetic left (`SAL`) and shift logical left (`SHL`) instructions perform the same operation; they shift the bits in the destination operand to the left (toward more significant bit locations). For each shift count, the most significant bit of the destination operand is shifted into the `CF` flag, and the least significant bit is cleared (see Figure 7-7 in the Intel® 64 and IA-32 Architectures Software Developer’s Manual, Volume 1).

The shift arithmetic right (`SAR`) and shift logical right (`SHR`) instructions shift the bits of the destination operand to the right (toward less significant bit locations). For each shift count, the least significant bit of the destination operand is shifted into the `CF` flag, and the most significant bit is either set or cleared depending on the instruction type. The `SHR` instruction clears the most significant bit (see Figure 7-8 in the Intel® 64 and IA-32 Architectures Software Developer’s Manual, Volume 1); the `SAR` instruction sets or clears the most significant bit to correspond to the sign (most significant bit) of the original value in the destination operand. In effect, the `SAR` instruction fills the empty bit position’s shifted value with the sign of the unshifted value (see Figure 7-9 in the Intel® 64 and IA-32 Architectures Software Developer’s Manual, Volume 1).

The `SAR` and `SHR` instructions can be used to perform signed or unsigned division, respectively, of the destination operand by powers of 2. For example, using the `SAR` instruction to shift a signed integer 1 bit to the right divides the value by 2.

Using the `SAR` instruction to perform a division operation does not produce the same result as the `IDIV` instruction. The quotient from the `IDIV` instruction is rounded toward zero, whereas the “quotient” of the `SAR` instruction is rounded toward negative infinity. This difference is apparent only for negative numbers. For example, when the `IDIV` instruction is used to divide -9 by 4, the result is -2 with a remainder of -1. If the `SAR` instruction is used to shift -9 right by two bits, the result is -3 and the “remainder” is +3; however, the `SAR` instruction stores only the most significant bit of the remainder (in the `CF` flag).

The `OF` flag is affected only on 1-bit shifts. For left shifts, the `OF` flag is set to 0 if the most-significant bit of the result is the same as the `CF` flag (that is, the top two bits of the original operand were the same); otherwise, it is set to 1. For the `SAR` instruction, the `OF` flag is cleared for all 1-bit shifts. For the `SHR` instruction, the `OF` flag is set to the most-significant bit of the original operand.

In 64-bit mode, the instruction’s default operation size is 32 bits and the mask width for `CL` is 5 bits. Using a `REX` prefix in the form of `REX.R` permits access to additional registers (`R8`-`R15`). Using a `REX` prefix in the form of `REX.W` promotes operation to 64-bits and sets the mask width for `CL` to 6 bits. See the summary chart at the beginning of this section for encoding data and limits.
