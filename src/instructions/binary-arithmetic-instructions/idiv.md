# `IDIV`
Signed Divide

| Instruction  | Description                                                             |
| :----------- | :---------------------------------------------------------------------- |
| `IDIV r/m8`  | Signed divide `AX` by `r/m8`; `AL` = Quotient, `AH` = Remainder         |
| `IDIV r/m16` | Signed divide `DX:AX` by `r/m16`; `AX` = Quotient, `DX` = Remainder     |
| `IDIV r/m32` | Signed divide `EDX:EAX` by `r/m32`; `EAX` = Quotient, `EDX` = Remainder |
| `IDIV r/m64` | Signed divide `RDX:RAX` by `r/m64`; `RAX` = Quotient, `RDX` = Remainder |

## Description
Divides the (signed) value in the `AX`, `DX:AX`, or `EDX:EAX` (dividend) by the source operand (divisor) and stores the result in the `AX` (`AH:AL`), `DX:AX`, or `EDX:EAX` registers. The source operand can be a general-purpose register or a memory location. The action of this instruction depends on the operand size (dividend/divisor).

Non-integral results are truncated (chopped) towards 0. The remainder is always less than the divisor in magnitude. Overflow is indicated with the #DE (divide error) exception rather than with the `CF` flag.

In 64-bit mode, the instruction’s default operation size is 32 bits. Use of the `REX.R` prefix permits access to additional registers (`R8`-`R15`). Use of the `REX.W` prefix promotes operation to 64 bits. In 64-bit mode when `REX.W` is applied, the instruction divides the signed value in `RDX:RAX` by the source operand. `RAX` contains a 64-bit quotient; `RDX` contains a 64-bit remainder.

See the summary chart at the beginning of this section for encoding data and limits. See the table below.

| Operand Size            | Dividend  | Divisor | Quotient | Remainder | Quotient Range               |
| :---------------------- | :-------- | :------ | :------- | :-------- | :--------------------------- |
| Word/byte               | `AX`      | `r/m8`  | `AL`     | `AH`      | $-128$ to $+127$             |
| Doubleword/word         | `DX:AX`   | `r/m16` | `AX`     | `DX`      | $-32,768$ to $+32,767$       |
| Quadword/doubleword     | `EDX:EAX` | `r/m32` | `EAX`    | `EDX`     | $-2^{31}$ to ${+2^{31} - 1}$ |
| Doublequadword/quadword | `RDX:RAX` | `r/m64` | `RAX`    | `RDX`     | $-2^{63}$ to ${+2^{63} - 1}$ |

## Operation
`SRC`: operand

### OperandSize = 8
```rust,ignore
if SRC == 0 {
    #DE; // divide error
}

temp = AX / SRC; // signed division
if temp > 0x7F || temp < 0x80 {
    // if a positive result is greater than 0x7F
    // or a negative result is less than 0x80
    #DE; // divide error
} else {
    AL = temp;
    AH = AX % SRC; // signed modulus
}
```

### OperandSize = 16
```rust,ignore
if SRC == 0 {
    #DE; // divide error
}

temp = DX:AX / SRC; // signed division
if temp > 0x7FFF || temp < 0x8000 {
    // if a positive result is greater than 0x7FFF
    // or a negative result is less than 0x8000
    #DE; // divide error
} else {
    AX = temp;
    DX = DX:AX % SRC; // signed modulus
}
```

### OperandSize = 32
```rust,ignore
if SRC == 0 {
    #DE; // divide error
}

temp = EDX:EAX / SRC; // signed division
if temp > 0x7FFF_FFFF || temp < 0x8000_0000 {
    // if a positive result is greater than 0x7FFF_FFFF
    // or a negative result is less than 0x8000_0000
    #DE; // divide error
} else {
    EAX = temp;
    EDX = EDX:EAX % SRC; // signed modulus
}
```

### OperandSize = 64
```rust,ignore
temp = RDX:RAX / SRC; // signed division
if temp > 0x7FFF_FFFF_FFFF_FFFF || temp < 0x8000_0000_0000_0000 {
    // if a positive result is greater than 0x7FFF_FFFF_FFFF_FFFF
    // or a negative result is less than 0x8000_0000_0000_0000
    #DE; // divide error
} else {
    RAX = temp;
    RDX = RDX:RAX % SRC; // signed modulus
}
```

## Flags Affected
The `CF`, `OF`, `SF`, `ZF`, `AF`, and `PF` flags are undefined.
