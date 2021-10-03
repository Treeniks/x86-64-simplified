# Flags
- `CF`: Carry flag\
  Set if an arithmetic operation generates a carry or a borrow out of the most-significant bit of the result; cleared otherwise.\
  This flag indicates an overflow condition for unsigned-integer arithmetic. It is also used in multiple-precision arithmetic.
- `PF`: Parity flag\
  Set if the least-significant byte of the result contains an even number of 1 bits; cleared otherwise.
- `AF`: Auxiliary Carry flag/Adjust flag\
  Set if an arithmetic operation generates a carry or a borrow out of bit 3 of the result; cleared otherwise.\
  This flag is used in binary-coded decimal (BCD) arithmetic.
- `ZF`: Zero flag\
  Set if the result is zero; cleared otherwise.
- `SF`: Sign flag\
  Set equal to the most-significant bit of the result, which is the sign bit of a signed integer. (0 indicates a positive value and 1 indicates a negative value.)
- `OF`: Overflow flag\
  Set if the integer result is too large a positive number or too small a negative number (excluding the sign-bit) to fit in the destination operand; cleared otherwise.\
  This flag indicates an overflow condition for signed-integer (two's complement) arithmetic.

Of these status flags, only the `CF` flag can be modified directly, using the `STC`, `CLC`, and `CMC` instructions. Also the bit instructions (`BT`, `BTS`, `BTR`, and `BTC`) copy a specified bit into the `CF` flag.
