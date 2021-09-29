# Condition Codes
| Condition Code   | Name                                | Definition              |
| :--------------- | :---------------------------------- | :---------------------- |
| `e`, `z`         | Equal, Zero                         | `ZF == 1`               |
| `ne`, `nz`       | Not Equal, Not Zero                 | `ZF == 0`               |
| `o`              | Overflow                            | `OF == 1`               |
| `no`             | No Overflow                         | `OF == 0`               |
| `s`              | Signed                              | `SF == 1`               |
| `ns`             | Not Signed                          | `SF == 0`               |
| `p`              | Parity                              | `PF == 1`               |
| `np`             | No Parity                           | `PF == 0`               |
|                  |                                     |                         |
| `c`, `b`, `nae`  | Carry, Below, Not Above or Equal    | `CF == 1`               |
| `nc`, `nb`, `ae` | No Carry, Not Below, Above or Equal | `CF == 0`               |
| `a`, `nbe`       | Above, Not Below or Equal           | `CF == 0` & `ZF == 0`   |
| `na`, `be`       | Not Above, Below or Equal           | `CF == 1` \| `ZF == 1`  |
|                  |                                     |                         |
| `ge`, `nl`       | Greater or Equal, Not Less          | `SF == OF`              |
| `nge`, `l`       | Not Greater or Equal, Less          | `SF != OF`              |
| `g`, `nle`       | Greater, Not Less or Equal          | `ZF == 0` & `SF == OF`  |
| `ng`, `le`       | Not Greater, Less or Equal          | `ZF == 1` \| `SF != OF` |
