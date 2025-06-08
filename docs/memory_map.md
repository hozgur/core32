# Core32 Memory Map

This document describes the memory organization of the Core32 virtual console platform. The system uses a 32-bit address space (4GB) with specific regions allocated for different purposes.

## Memory Map Overview

| Address Range           | Size     | Region               | Description                              | Access  |
|-------------------------|----------|----------------------|------------------------------------------|---------|
| `0x00000000`-`0x000003FF` | 1 KB     | Interrupt Vector Table (IVT) | Contains pointers to ISRs (256 entries) | R/X     |
| `0x00000400`-`0x0000FFFF` | ~63.75 KB| Boot ROM             | Initial boot code and firmware            | R/X     |
| `0x00010000`-`0x3FFFFFFF` | ~1 GB    | Program Memory       | Application code and data                 | R/W/X   |
| `0x40000000`-`0x7FFFFFFF` | 1 GB     | Reserved             | Future expansion                         | -       |
| `0x80000000`-`0xBFFFFFFF` | 1 GB     | I/O Space            | Memory-mapped I/O devices                | R/W     |
| `0xC0000000`-`0xDFFFFFFF` | 512 MB   | Video Memory         | Framebuffer and graphics memory          | R/W     |
| `0xE0000000`-`0xE0FFFFFF` | 16 MB    | System Control       | System registers and control             | R/W     |
| `0xE1000000`-`0xE1FFFFFF` | 16 MB    | Audio                | Audio controller and buffers             | R/W     |
| `0xE2000000`-`0xE2FFFFFF` | 16 MB    | Storage              | Storage controller and buffers           | R/W     |
| `0xE3000000`-`0xE30FFFFF` | 1 MB     | Interrupt Controller | Interrupt control registers              | R/W     |
| `0xE3100000`-`0xE31FFFFF` | 1 MB     | Timer                | System timers                           | R/W     |
| `0xE3200000`-`0xE32FFFFF` | 1 MB     | UART                 | Serial console                          | R/W     |
| `0xE3300000`-`0xE33FFFFF` | 1 MB     | RTC                  | Real-time clock                         | R/W     |
| `0xE4000000`-`0xEFFFFFFF` | 192 MB   | Reserved             | Future I/O devices                      | -       |
| `0xF0000000`-`0xFFFFFFFF` | 256 MB   | System RAM          | Kernel and system data                  | R/W     |


## Detailed I/O Map

### System Control (0xE0000000 - 0xE0FFFFFF)
- `0xE0000000` - System Status Register (RO)
- `0xE0000004` - System Control Register (RW)
- `0xE0000008` - Reset Control (WO)
- `0xE000000C` - Clock Control (RW)
- `0xE0000010` - Power Management (RW)

### Interrupt Controller (0xE3000000 - 0xE30FFFFF)
- `0xE3000000` - IVT Base Address (RW)
- `0xE3000004` - Interrupt Enable (RW)
- `0xE3000008` - Interrupt Pending (RO)
- `0xE300000C` - Interrupt Acknowledge (WO)
- `0xE3000010` - Software Interrupt Trigger (WO)

### Video Controller (0xC0000000 - 0xDFFFFFFF)
- `0xC0000000` - Framebuffer (16MB)
- `0xD0000000` - Video Control Registers
  - `0xD0000000` - Display Control (RW)
  - `0xD0000004` - Resolution (RW)
  - `0xD0000008` - Color Depth (RW)
  - `0xD000000C` - VSync Control (RW)

### Audio Controller (0xE1000000 - 0xE1FFFFFF)
- `0xE1000000` - Audio Control (RW)
- `0xE1000004` - Audio Status (RO)
- `0xE1000008` - Sample Rate (RW)
- `0xE100000C` - Audio Buffer (RW)

### Storage Controller (0xE2000000 - 0xE2FFFFFF)
- `0xE2000000` - Storage Control (RW)
- `0xE2000004` - Status (RO)
- `0xE2000008` - Sector Address (RW)
- `0xE200000C` - Data Buffer (RW)

## Memory Access Rules

1. **Alignment**:
   - All memory accesses must be 16-byte aligned
   - Unaligned accesses will generate an alignment fault

2. **Permissions**:
   - R: Readable
   - W: Writable
   - X: Executable
   - -: Reserved/Invalid

3. **Special Regions**:
   - The first 64KB (Boot ROM) is read-only and contains the initial boot code
   - I/O space requires specific access patterns (e.g., 32-bit aligned word access)
   - Video memory has specific access patterns for optimal performance

## Memory Initialization

1. **Power-on Reset**:
   - All memory is initialized to 0x00000000
   - Boot ROM is mapped to 0x00000000
   - Stack pointer is set to 0xFFFFFFFF (top of system RAM)
   - PC is set to 0x00000000

2. **Boot Process**:
   - CPU starts execution at 0x00000000
   - Boot loader initializes essential hardware
   - System memory is tested and initialized
   - Control is transferred to the loaded program

## Memory Protection

- Certain memory regions are protected and cannot be accessed from user mode
- Attempting to access protected memory will generate a protection fault
- The memory protection unit (MPU) controls access permissions

## Notes

- All addresses are 32-bit values
- Memory-mapped I/O uses little-endian byte order
- Unused address space is reserved for future expansion
- Accessing unmapped memory will generate a bus fault
