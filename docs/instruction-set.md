# Instruction Encoding and Control

This document describes the encoding in `registerFile2_content.hex` and the accumulator connections in the Part 2 circuit. Mnemonics are labels for explaining the machine words; no assembler is included.

## Word layout

Each instruction occupies one 16-bit instruction-RAM word.

| Bits | Field | Meaning |
| --- | --- | --- |
| 15 through 8 | Immediate | Character, addition operand, or signed jump offset |
| 7 through 6 | Unused in this program | Encoded as zero |
| 5 through 3 | Register address | Selects Reg-0 through Reg-7 |
| 2 through 0 | Opcode | Selects one of eight decoder outputs |

The fields can be decoded with:

```text
opcode    = word & 0x7
register  = (word >> 3) & 0x7
immediate = (word >> 8) & 0xFF
```

An instruction using the register field can be formed with:

```text
word = (immediate << 8) | (register << 3) | opcode
```

## Operations

| Opcode | Binary | Mnemonic | Effect |
| --- | --- | --- | --- |
| 1 | `001` | `ADD Rr` | `ACC = (Rr + ACC) mod 256` |
| 3 | `011` | `ADDI Rr, imm` | `ACC = (Rr + imm) mod 256` |
| 5 | `101` | `OUT character` | Print the immediate's ASCII character |
| 6 | `110` | `JMP offset` | `PC = (PC + signed_offset) mod 65536` |
| 7 | `111` | `STORE Rr` | `Rr = ACC` |

Instructions other than JMP advance the program counter by one. The jump offset is relative to the address of the JMP instruction itself. An immediate from `80` through `FF` is interpreted as a negative two's-complement offset for JMP.

## Control signals

The ALU input mux chooses the immediate when its select input is 0 and ACC when the select input is 1. Both ADD operations load ACC. Only STORE enables writing to the register-file RAM.

| Instruction | ALU mux select | ACC load | Register-file store | TTY output | Jump select |
| --- | --- | --- | --- | --- | --- |
| ADD | 1 | 1 | 0 | 0 | 0 |
| ADDI | 0 | 1 | 0 | 0 | 0 |
| STORE | n/a | 0 | 1 | 0 | 0 |
| OUT | n/a | 0 | 0 | 1 | 0 |
| JMP | n/a | 0 | 0 | 0 | 1 |

The ACC load signal combines decoder outputs 1 and 3. Decoder output 7 controls register-file storage. The original TTY and jump operations remain part of the instruction set.

## Examples from the RAM image

| Word | Decode | Purpose |
| --- | --- | --- |
| `5905` | `OUT 'Y'` | First character of the initial name |
| `0103` | `ADDI R0, 0x01` | Prepare the first Fibonacci seed while R0 is zero |
| `000F` | `STORE R1` | Save ACC into Reg-1 |
| `0813` | `ADDI R2, 0x08` | Calculate the next surname count |
| `0017` | `STORE R2` | Save that count into Reg-2 |
| `0009` | `ADD R1` | Add Reg-1 to ACC |
| `001F` | `STORE R3` | Save the next Fibonacci value |
| `EF06` | `JMP -17` | At address `0021`, return to address `0010` |

The loop uses ADDI with an immediate of zero to copy a register into ACC, then STORE to copy ACC into another register.
