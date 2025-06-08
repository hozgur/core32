# Core32 Interrupt Handling

This document describes the interrupt architecture and handling for the Core32 platform.

## 1. Interrupt Types

### 1.1 Exceptions
- **Reset** (0x00): System reset/power-on
- **NMI** (0x01): Non-maskable interrupt
- **Hard Fault** (0x02): All classes of faults
- **Memory Fault** (0x03): Memory access violation
- **Bus Fault** (0x04): Bus error
- **Usage Fault** (0x05): Undefined instruction or illegal state

### 1.2 System Interrupts
- **SVCall** (0x0A): Supervisor call (SVC instruction)
- **PendSV** (0x0D): Pendable service call
- **SysTick** (0x0E): System tick timer

### 1.3 External Interrupts (IRQs)
- **IRQ0-IRQ239** (0x0F-0xFF): Peripheral interrupts

## 2. Interrupt Vector Table (IVT)

### 2.1 IVT Structure
- Located at 0x00000000
- 256 entries (4 bytes each)
- Each entry is a 32-bit function pointer
- First 16 entries are for system exceptions
- Remaining 240 entries for external interrupts

### 2.2 IVT Entry Format
```c
typedef void (*isr_handler_t)(void);

struct ivt_entry {
    uint32_t *initial_sp;  // Initial stack pointer (for reset only)
    isr_handler_t reset;   // Reset handler
    isr_handler_t nmi;     // NMI handler
    // ... other exception handlers
    isr_handler_t irq[240]; // External interrupt handlers
};
```

## 3. Interrupt Control Registers

### 3.1 Interrupt Controller (0xE3000000)
- **IVT_BASE** (0xE3000000): Base address of IVT
- **INT_ENABLE** (0xE3000004): Interrupt enable bits
- **INT_PENDING** (0xE3000008): Pending interrupt flags
- **INT_ACK** (0xE300000C): Acknowledge interrupt
- **SWI_TRIGGER** (0xE3000010): Trigger software interrupt

## 4. Interrupt Handling Flow

1. **Interrupt Occurs**
   - CPU completes current instruction
   - Pushes context to stack (PC, SR, registers)
   - Disables interrupts (if not NMI)
   - Fetches ISR address from IVT

2. **ISR Execution**
   - ISR should:
     1. Save any additional registers it will use
     2. Clear interrupt source (if needed)
     3. Handle the interrupt
     4. Restore saved registers
     5. Execute IRET to return

3. **Interrupt Return**
   - CPU restores context from stack
   - Restores interrupt state
   - Resumes execution

## 5. Writing ISRs

### 5.1 Basic ISR Template
```asm
isr_example:
    ; 1. Save context (if needed)
    PUSH {R0-R3, R12, LR}
    
    ; 2. Clear interrupt source (if needed)
    LDR R0, =0xE300000C    ; INT_ACK
    MOV R1, #IRQ_NUMBER
    STR R1, [R0]
    
    ; 3. Handle interrupt
    ; ... your code here ...
    
    ; 4. Restore context and return
    POP {R0-R3, R12, LR}
    IRET
```

### 5.2 Nested Interrupts
To allow nested interrupts, re-enable interrupts in the ISR:
```asm
isr_nested:
    PUSH {R0-R3, R12, LR}
    STI                    ; Re-enable interrupts
    ; ... ISR code ...
    CLI                    ; Optionally disable before return
    POP {R0-R3, R12, LR}
    IRET
```

## 6. Software Interrupts

### 6.1 Triggering a Software Interrupt
```asm
    ; Using INT instruction
    MOV R0, #SVC_NUMBER
    INT R0
    
    ; Or via memory-mapped register
    LDR R0, =0xE3000010    ; SWI_TRIGGER
    MOV R1, #SVC_NUMBER
    STR R1, [R0]
```

## 7. Interrupt Priorities

- Lower vector numbers have higher priority
- NMI (0x01) is the highest priority
- Reset (0x00) is a special case (not maskable)
- IRQs (0x0F-0xFF) have the lowest priority

## 8. Best Practices

1. Keep ISRs short and fast
2. Defer processing to main loop when possible
3. Disable interrupts only when necessary
4. Always clear interrupt sources
5. Use proper synchronization for shared data
6. Document all ISRs and their side effects
