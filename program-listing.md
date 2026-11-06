# RAM Program Listing

The original `registerFile2_content.hex` contains 34 instruction words, at addresses `0000` through `0021`. Values in the address and word columns are hexadecimal.

## Program sections

| Address range | Work |
| --- | --- |
| `0000` through `000D` | Print `YousefAhmadzai` once |
| `000E` through `000F` | Initialize Reg-1 to 1; the other registers begin at zero |
| `0010` through `0017` | Print `Ahmadzai` |
| `0018` through `0019` | Increase Reg-2 by 8 through ACC |
| `001A` through `001C` | Add the two Fibonacci state values and store the result in Reg-3 |
| `001D` through `0020` | Move the Fibonacci state forward using ACC |
| `0021` | Jump back 17 words to `0010` |

## Machine words

| Address | Word | Instruction |
| --- | --- | --- |
| `0000` | `5905` | `OUT 'Y'` |
| `0001` | `6F05` | `OUT 'o'` |
| `0002` | `7505` | `OUT 'u'` |
| `0003` | `7305` | `OUT 's'` |
| `0004` | `6505` | `OUT 'e'` |
| `0005` | `6605` | `OUT 'f'` |
| `0006` | `4105` | `OUT 'A'` |
| `0007` | `6805` | `OUT 'h'` |
| `0008` | `6D05` | `OUT 'm'` |
| `0009` | `6105` | `OUT 'a'` |
| `000A` | `6405` | `OUT 'd'` |
| `000B` | `7A05` | `OUT 'z'` |
| `000C` | `6105` | `OUT 'a'` |
| `000D` | `6905` | `OUT 'i'` |
| `000E` | `0103` | `ADDI R0, 0x01` |
| `000F` | `000F` | `STORE R1` |
| `0010` | `4105` | `OUT 'A'` |
| `0011` | `6805` | `OUT 'h'` |
| `0012` | `6D05` | `OUT 'm'` |
| `0013` | `6105` | `OUT 'a'` |
| `0014` | `6405` | `OUT 'd'` |
| `0015` | `7A05` | `OUT 'z'` |
| `0016` | `6105` | `OUT 'a'` |
| `0017` | `6905` | `OUT 'i'` |
| `0018` | `0813` | `ADDI R2, 0x08` |
| `0019` | `0017` | `STORE R2` |
| `001A` | `0003` | `ADDI R0, 0x00` |
| `001B` | `0009` | `ADD R1` |
| `001C` | `001F` | `STORE R3` |
| `001D` | `000B` | `ADDI R1, 0x00` |
| `001E` | `0007` | `STORE R0` |
| `001F` | `001B` | `ADDI R3, 0x00` |
| `0020` | `000F` | `STORE R1` |
| `0021` | `EF06` | `JMP -17` |

## Expected loop trace

This is a reference trace derived from the instruction semantics and the supplied RAM image. It assumes a reset register file and is not a separate run of the Logisim circuit. The first completed loop is visible in the submitted video.

| Completed loop | Reg-0 | Reg-1 | Reg-2 | Reg-3 |
| --- | --- | --- | --- | --- |
| 1 | `01` | `01` | `08` | `01` |
| 2 | `01` | `02` | `10` | `02` |
| 3 | `02` | `03` | `18` | `03` |
| 4 | `03` | `05` | `20` | `05` |
| 5 | `05` | `08` | `28` | `08` |
| 6 | `08` | `0D` | `30` | `0D` |
| 7 | `0D` | `15` | `38` | `15` |
| 8 | `15` | `22` | `40` | `22` |
| 9 | `22` | `37` | `48` | `37` |
| 10 | `37` | `59` | `50` | `59` |
| 11 | `59` | `90` | `58` | `90` |
| 12 | `90` | `E9` | `60` | `E9` |
| 13 | `E9` | `79` | `68` | `79` |

Reg-2 increases by hexadecimal `08` each loop. Reg-0 and Reg-1 hold the two Fibonacci state values, and Reg-3 receives their sum.

At loop 13, the sum is decimal 377. The 8-bit result is decimal 121, displayed as hexadecimal `79`. This is the expected wrap modulo 256.

## Text output

The beginning of the TTY output is:

```text
YousefAhmadzaiAhmadzaiAhmadzai
```

The surname continues once per loop. The program does not insert spaces or newline characters between repetitions.
