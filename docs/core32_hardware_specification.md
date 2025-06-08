# Core32 Virtual Console Platform Specification

## 1. Overview
The Core32 is a 32-bit virtual computing platform designed for high-level machine code (HLMC) execution. This document outlines the hardware specifications and architecture of the Core32 virtual console.

## 2. CPU Architecture

### 2.1 General Purpose Registers (32-bit)
- R0-R12: General purpose registers
- R13 (SP): Stack Pointer
- R14 (LR): Link Register (return address)
- R15 (PC): Program Counter (read-only)
- FLAGS: Status flags register (32-bit)
  - Bit 0: Zero Flag (ZF)
  - Bit 1: Carry Flag (CF)
  - Bit 2: Overflow Flag (OF)
  - Bit 3: Sign Flag (SF)
  - Bit 4: Interrupt Enable (IE)
  - Bits 5-31: Reserved

### 2.2 Special Registers
- **IVTBR** (Interrupt Vector Table Base Register): Points to the base of the IVT
- **IVTLR** (Interrupt Vector Table Limit Register): Size of the IVT
- **IVTAR** (Interrupt Vector Table Attribute Register): Access permissions and caching attributes

### 2.3 Memory Architecture
- **Address Space**: 32-bit flat address space (4GB)
- **Physical Memory**: Up to 8GB with bank switching
- **Alignment**: 16-byte memory access alignment
- **Endianness**: Little-endian
- **Memory Protection**: Per-region access control via MPU

## 3. Interrupt System

See [interrupt_handling.md](interrupt_handling.md) for complete interrupt system documentation, including:
- Interrupt types and priorities
- Interrupt Vector Table (IVT) configuration
- Interrupt control registers
- Exception handling
- Interrupt service routine (ISR) examples

### Key Features:
- Configurable interrupt priorities
- Low-latency interrupt response
- Hardware and software interrupts
- Nested interrupt support
- Power management integration

## 4. Instruction Set Architecture (HLMC)

See [hlmc_isa.md](hlmc_isa.md) for the complete instruction set reference, including:
- Instruction formats and encodings
- Detailed opcode tables
- Addressing modes
- Assembly language syntax
- Code examples

### Key Features:
- 32-bit fixed-length instructions
- 16 general-purpose registers (R0-R15)
- Load/store architecture
- Rich set of arithmetic, logical, and control flow instructions
- Support for immediate and register addressing modes
- Interrupt and exception handling instructions

## 5. Memory Map

See [memory_map.md](memory_map.md) for detailed memory map information.

## 6. Revision History

| Version | Date       | Description                         |
|---------|------------|-------------------------------------|
| 1.0     | 2025-06-08 | Initial specification               |
| 1.1     | 2025-06-08 | Added interrupt system details      |
| 1.2     | 2025-06-08 | Expanded memory management section  |

## Notes

- All specifications subject to change
- Contact architecture team for clarification
- Reference implementations available in the SDK
