# High-Level Machine Code (HLMC) Instruction Set Architecture

## 1. Instruction Format

```
 31          24 23     20 19     16 15                    0
+--------------+---------+---------+---------------------+
|   Opcode    |   Dst   |   Src1  |      Immediate      |
+--------------+---------+---------+---------------------+
      8            4          4              16 bits
```

For immediate instructions, the Src2 field is combined with the Immediate field to form a 16-bit immediate value.

- **Opcode (8 bits)**: Operation code
- **Dst (4 bits)**: Destination register (R0-R15)
- **Src1 (4 bits)**: First source register (R0-R15)
- **Src2 (4 bits)**: Second source register (R0-R15)
- **Immediate (16 bits)**: Immediate value or address offset (uses Src2 + Immediate fields)

## 2. Register Set
- **R0-R12**: General purpose registers
- **R13 (SP)**: Stack Pointer
- **R14 (LR)**: Link Register (return address)
- **R15 (PC)**: Program Counter (read-only)
- **FLAGS**: Status register

## 3. Opcode Reference

### 3.1 Arithmetic Operations

| Opcode | Mnemonic | Format | Operation |
|--------|----------|--------|-----------|
| 0x01   | ADD      | R, R, R | Dst = Src1 + Src2 |
| 0x02   | ADDI     | R, R, I | Dst = Src1 + Imm (16-bit, sign-extended) |
| 0x04   | SUBI     | R, R, I | Dst = Src1 - Imm (16-bit, sign-extended) |
| 0x06   | MULI     | R, R, I | Dst = Src1 * Imm (16-bit, sign-extended) |
| 0x05   | MUL      | R, R, R | Dst = Src1 * Src2 |
| 0x07   | DIV      | R, R, R | Dst = Src1 / Src2 |
| 0x08   | MOD      | R, R, R | Dst = Src1 % Src2 |
| 0x09   | INC      | R       | Dst = Dst + 1 |
| 0x0A   | DEC      | R       | Dst = Dst - 1 |

### 3.2 Logical and Bit Operations

| Opcode | Mnemonic | Format | Operation |
|--------|----------|--------|-----------|
| 0x10   | AND      | R, R, R | Dst = Src1 & Src2 |
| 0x11   | OR       | R, R, R | Dst = Src1 \| Src2 |
| 0x12   | XOR      | R, R, R | Dst = Src1 ^ Src2 |
| 0x13   | NOT      | R, R   | Dst = ~Src1 |
| 0x14   | SHL      | R, R, R | Dst = Src1 << Src2 |
| 0x15   | SHLI     | R, R, I | Dst = Src1 << Imm (16-bit) |
| 0x17   | SHRI     | R, R, I | Dst = Src1 >> Imm (logical, 16-bit) |
| 0x19   | SARI     | R, R, I | Dst = Src1 >> Imm (arithmetic, 16-bit) |
| 0x18   | SAR      | R, R, R | Dst = Src1 >> Src2 (arithmetic) |

### 3.3 Memory Operations

| Opcode | Mnemonic | Format | Operation |
|--------|----------|--------|-----------|
| 0x20   | LOAD     | R, [R] | Dst = [Src1] (16-byte aligned) |
| 0x21   | LOADI    | R, [R+I] | Dst = [Src1 + Imm] (16-bit offset, 16-byte aligned) |
| 0x23   | STOREI   | [R+I], R | [Dst + Imm] = Src1 (16-bit offset, 16-byte aligned) |
| 0x24   | PUSH     | R      | [--SP] = Src1 |
| 0x25   | POP      | R      | Dst = [SP++] |
| 0x26   | MOV      | R, R   | Dst = Src1 |
| 0x27   | LDI      | R, I   | Dst = Imm (16-bit, sign-extended to 32-bit) |

### 3.4 Control Flow

| Opcode | Mnemonic | Format | Operation |
|--------|----------|--------|-----------|
| 0x30   | JMP      | R      | PC = Src1 |
| 0x31   | JMPI     | I      | PC = PC + (16-bit sign-extended Imm) |
| 0x33   | JZI      | R, I   | if (Src1 == 0) PC = PC + (16-bit sign-extended Imm) |
| 0x35   | JNZI     | R, I   | if (Src1 != 0) PC = PC + (16-bit sign-extended Imm) |
| 0x34   | JNZ      | R, R   | if (Src1 != 0) PC = Src2 |
| 0x36   | CALL     | R      | LR = PC + 4; PC = Src1 |
| 0x37   | CALLI    | I      | LR = PC + 4; PC = PC + (sign-extended Imm) |
| 0x38   | RET      |        | PC = LR |
| 0x39   | CMP      | R, R   | Set flags based on (Src1 - Src2) |
| 0x3A   | CMPI     | R, I   | Set flags based on (Src1 - Imm) |

### 3.5 System Operations

| Opcode | Mnemonic | Format | Operation |
|--------|----------|--------|-----------|
| 0xF0   | HALT     |        | Stop execution |
| 0xF1   | NOP      |        | No operation |
| 0xF2   | INT      | I      | Software interrupt (0-15) |
| 0xF3   | IRET     |        | Return from interrupt |
| 0xF4   | SYS      | I      | System call (0-4095) |
| 0xF5   | CLI      |        | Clear interrupt flag |
| 0xF6   | STI      |        | Set interrupt flag |

## 4. Condition Codes

Instructions that modify flags (CMP, CMPI, arithmetic/logical ops):
- **Z (Zero)**: Set if result is zero
- **C (Carry)**: Set if unsigned overflow occurred
- **V (oVerflow)**: Set if signed overflow occurred
- **N (Negative)**: Set if result is negative (MSB = 1)

## 5. Addressing Modes

1. **Register**: `R0`-`R15`
2. **Immediate**: `#value` (16-bit, sign-extended to 32-bit)
3. **Register Indirect**: `[R]`
4. **Base + Offset**: `[R + #offset]` (16-bit offset)
5. **PC-relative**: `PC + #offset` (for jumps/calls)

## 6. Assembly Syntax Example

```asm
; Simple loop to sum numbers 1 to 10
        LDI R0, 0        ; sum = 0
        LDI R1, 1        ; counter = 1
        LDI R2, 10       ; limit = 10
loop:   ADD R0, R0, R1   ; sum += counter
        ADDI R1, R1, 1   ; counter++
        CMP R1, R2       ; compare counter with limit
        JLEI loop        ; jump if counter <= limit
        HALT             ; end of program
```

## 7. Immediate Value Handling

### 7.1 Sign Extension
Immediate values in HLMC are 16 bits wide and are automatically sign-extended to 32 bits when loaded into registers. This means:

- For positive numbers (bit 15 = 0):
  - Upper 16 bits are set to 0
  - Example: `0x1234` → `0x00001234`

- For negative numbers (bit 15 = 1):
  - Upper 16 bits are set to 1
  - Example: `0xFFFF` → `0xFFFFFFFF`

### 7.2 Loading 32-bit Values

#### 7.2.1 Using LDI (for 16-bit values)
```asm
LDI R1, #0x1234     ; R1 = 0x00001234 (positive number)
LDI R2, #0xFFFF     ; R2 = 0xFFFFFFFF (negative number)
```

#### 7.2.2 Loading Full 32-bit Values
To load a full 32-bit value, use a combination of LDI, SHL, and OR:

```asm
; Example: Load 0xDEADBEEF into R1

; 1. Load lower 16 bits (0xBEEF)
LDI R1, #0xBEEF     ; R1 = 0xFFFFBEEF (sign-extended)

; 2. Load upper 16 bits (0xDEAD)
LDI R2, #0xDEAD     ; R2 = 0x0000DEAD
SHL R2, R2, #16     ; R2 = 0xDEAD0000

; 3. Combine them
OR R1, R1, R2       ; R1 = 0xDEADBEEF
```

#### 7.2.3 Loading 32-bit Constants (Alternative Method)
For better readability, you can use macros or labels:

```asm
; Using assembler macros (pseudo-instruction)
; This would be expanded by the assembler to the above sequence
LDI32 R1, 0xDEADBEEF
```

Note: The actual implementation of `LDI32` would depend on your assembler's macro support.

## 8. Encoding Examples

1. `ADD R1, R2, R3`
   ```
   00000001 0001 0010 0011 000000000000
   ```

2. `LDI R1, #0x1234`
   ```
   00100111 0001 0001 0010 00110100
   ```
   - Opcode: 0x27 (LDI)
   - Dst: 0x1 (R1)
   - Src1: 0x1 (unused, could be repurposed)
   - Immediate: 0x1234

3. `JNZI R1, #-4`
   ```
   00110101 0001 0000 1111 11111100
   ```
   - Opcode: 0x35 (JNZI)
   - Dst: 0x1 (R1)
   - Src1: 0x0 (unused, could be repurposed)
   - Immediate: 0xFFFC (-4 in two's complement)

## 9. Notes
- All memory accesses are 16-byte aligned
- Immediate values are sign-extended to 32 bits
- Unused opcodes are reserved for future expansion
- Interrupts are disabled during interrupt service routines
