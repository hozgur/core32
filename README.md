# Core32 Virtual Console Platform

## Overview
Core32 is a 32-bit virtual console platform featuring a custom instruction set architecture (HLMC) designed for embedded and educational use.

## Repository
GitHub: [https://github.com/hozgur/core32](https://github.com/hozgur/core32)

## Documentation

### Core Specifications
- [Hardware Specification](core32_hardware_specification.md)
- [Instruction Set Architecture (HLMC)](hlmc_isa.md)
- [Interrupt Handling](interrupt_handling.md)
- [Memory Map](memory_map.md)

## Getting Started

### Prerequisites
- GCC-based toolchain
- QEMU (for emulation)
- Make

### Building
```bash
git clone https://github.com/hozgur/core32.git
cd core32
make
```

### Running
```bash
make run
```

## Project Structure
```
core32/
├── docs/                   # Documentation
├── src/                    # Source code
│   ├── core/               # Core CPU implementation
│   ├── emulator/           # Emulator code
│   └── examples/           # Example programs
├── tests/                  # Test suite
├── Makefile
└── README.md
```

## Contributing
Contributions are welcome! Please submit a pull request or open an issue to discuss your ideas.

## License
[Specify your license here, e.g., MIT, GPL, etc.]
