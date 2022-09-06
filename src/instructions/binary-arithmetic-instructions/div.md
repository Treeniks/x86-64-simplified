# `DIV`
Unsigned Divide

| Instruction | Description                                                               |
| :---------- | :------------------------------------------------------------------------ |
| `DIV r/m8`  | Unsigned divide `AX` by `r/m8`; `AL` = Quotient, `AH` = Remainder         |
| `DIV r/m16` | Unsigned divide `DX:AX` by `r/m16`; `AX` = Quotient, `DX` = Remainder     |
| `DIV r/m32` | Unsigned divide `EDX:EAX` by `r/m32`; `EAX` = Quotient, `EDX` = Remainder |
| `DIV r/m64` | Unsigned divide `RDX:RAX` by `r/m64`; `RAX` = Quotient, `RDX` = Remainder |

$$
\begin{aligned}
\texttt{AL} &= \frac{\texttt{AX}}{\texttt{r/m8}} & \texttt{AH} &= \texttt{AX} \mod \texttt{r/m8} \\
\texttt{AX} &= \frac{\texttt{DX:AX}}{\texttt{r/m16}} & \texttt{DX} &= \texttt{DX:AX} \mod \texttt{r/m16} \\
\texttt{EAX} &= \frac{\texttt{EDX:EAX}}{\texttt{r/m32}} & \texttt{EDX} &= \texttt{EDX:EAX} \mod \texttt{r/m32} \\
\texttt{RAX} &= \frac{\texttt{RDX:RAX}}{\texttt{r/m64}} & \texttt{RDX} &= \texttt{RDX:RAX} \mod \texttt{r/m64}
\end{aligned}
$$

## Description
Divides unsigned the value in the `AX`, `DX:AX`, `EDX:EAX`, or `RDX:RAX` registers (dividend) by the source operand (divisor) and stores the result in the `AX` (`AH:AL`), `DX:AX`, `EDX:EAX`, or `RDX:RAX` registers. The source operand can be a general-purpose register or a memory location. The action of this instruction depends on the operand size (dividend/divisor). Division using 64-bit operand is available only in 64-bit mode.

Non-integral results are truncated (chopped) towards 0. The remainder is always less than the divisor in magnitude. Overflow is indicated with the `#DE` (divide error) exception rather than with the `CF` flag.

In 64-bit mode, the instruction's default operation size is 32 bits. Use of the `REX.R` prefix permits access to additional registers (`R8`-`R15`). Use of the `REX.W` prefix promotes operation to 64 bits. In 64-bit mode when `REX.W` is applied, the instruction divides the unsigned value in `RDX:RAX` by the source operand and stores the quotient in `RAX`, the remainder in `RDX`.

See the summary chart at the beginning of this section for encoding data and limits. See the table below.

| Operand Size            | Dividend  | Divisor | Quotient | Remainder | Maximum Quotient   |
| :---------------------- | :-------- | :------ | :------- | :-------- | :----------------- |
| Word/byte               | `AX`      | `r/m8`  | `AL`     | `AH`      | 255                |
| Doubleword/word         | `DX:AX`   | `r/m16` | `AX`     | `DX`      | 65,535             |
| Quadword/doubleword     | `EDX:EAX` | `r/m32` | `EAX`    | `EDX`     | 2<sup>32</sup> - 1 |
| Doublequadword/quadword | `RDX:RAX` | `r/m64` | `RAX`    | `RDX`     | 2<sup>64</sup> - 1 |

## Operation
`SRC`: operand

### OperandSize = 8
```rust,ignore
if SRC == 0 {
    #DE; // divide error
}

temp = AX / SRC;
if temp > 0xFF {
    #DE; // divide error
} else {
    AL = temp;
    AH = AX % SRC;
}
```

### OperandSize = 16
```rust,ignore
if SRC == 0 {
    #DE; // divide error
}

temp = DX:AX / SRC;
if temp > 0xFFFF {
    #DE; // divide error
} else {
    AX = temp;
    DX = DX:AX % SRC;
}
```

### OperandSize = 32
```rust,ignore
if SRC == 0 {
    #DE; // divide error
}

temp = EDX:EAX / SRC;
if temp > 0xFFFF_FFFF {
    #DE; // divide error
} else {
    EAX = temp;
    EDX = EDX:EAX % SRC;
}
```

### OperandSize = 64
```rust,ignore
if SRC == 0 {
    #DE; // divide error
}

temp = RDX:RAX / SRC;
if temp > 0xFFFF_FFFF_FFFF_FFFF {
    #DE; // divide error
} else {
    RAX = temp;
    RDX = RDX:RAX % SRC;
}
```

## Flags Affected
The `CF`, `OF`, `SF`, `ZF`, `AF`, and `PF` flags are undefined.
