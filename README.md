# CPU-Verilog
Overview

This project is the flagship of my hardware design portfolio: a fully functional CPU implemented from scratch in Verilog HDL. The design integrates all major components of a processor — program counter, instruction memory, control unit, register file, ALU, sign extender, and data memory — into a cohesive system capable of fetching, decoding, executing, and storing results.

At the core lies the Arithmetic Logic Unit (ALU), supporting arithmetic, logic, and shift operations. The control unit decodes instruction opcodes and function bits into control signals, orchestrating the datapath. A register file provides fast operand access, while data memory supports load/store instructions. The program counter and branch logic sequence execution, enabling the CPU to handle multiple instruction formats.

This project demonstrates the full hardware design workflow:

Specification → RTL design → Simulation → Debugging → Integration → System-level verification.
It highlights mastery of digital logic, Verilog RTL, modular design, and waveform-based debugging.

                 ┌────────────────┐
     clk,reset → │   Program      │
                 │   Counter (PC) │
                 └──────┬─────────┘
                        │ PC
                        ▼
                 ┌────────────────┐
                 │ Instruction     │ 32-bit instr
                 │   Memory        ├──────────────┐
                 └──────┬─────────┘              │
                        │                        │
                        │                        ▼
                        │                 ┌───────────────┐
                        │                 │   Control     │
                        │                 │ (opcode/funct)│
                        │                 └─┬───────┬─────┘
                        │                   │       │
                        │                   │       │ ALUOp
                        │                   │       ▼
                        │                   │  ┌───────────┐
                        │                   │  │ ALU Ctl   │
                        │                   │  └────┬──────┘
                        │                   │       │ alu_sel
                        │                   │       │
                        ▼                   │       │
               ┌────────────────┐           │       │
    rs,rt,rd → │ Register File  │◄──────────┘       │
  read_data1 ─►│  (2R/1W ports) │──────────────────►│
  read_data2 ─►└──────┬─────────┘                  ▼
                       │                  ┌────────────────┐
                       │  imm[15:0]       │      ALU       │
                       │───────────────┐  │ (add/and/or/   │
                       ▼               │  │  shift, etc.)  │
               ┌────────────┐          │  └───┬────────────┘
               │ SignExtend │──────────┘      │ ALU result
               └────┬───────┘                 │ zero
                    │                         │
           imm32 ───┘                         │
                                              ▼
                               ┌────────────────────────┐
                        ┌─────►│   Data Memory          │
                        │      │ (load/store, byte/word)│
                        │      └─────────┬──────────────┘
                        │                │ read_data
                        │                │
                        │         ┌──────▼──────┐
                        │         │ Writeback   │
                        └─────────┤   Mux       │───► RegFile write_data
                                  └─────────────┘

Branch/Jump path:
- PC + 4 (or + imm<<2 for branch) selected via a PC mux using Control & ALU zero.

Branch/Jump path:
- PC + 4 (or + imm<<2 for branch) selected via a PC mux using Control & ALU zero.

Module Map
- pc.v — Program counter with reset, increment, and branch/jump logic.
- InstructionMemory.v — ROM for instruction fetch.
- control.v — Decodes opcodes/funct → RegWrite, MemRead, MemWrite, Branch, Jump, ALUSrc, MemtoReg, ALUOp.
- ALU.v / adder.v — Arithmetic and logic ops with zero flag.
- RegisterFile.v — Two read ports, one write port, synchronous write.
- SignExtender.v — Extends 16-bit immediates to 32 bits.
- DataMemory.v — Load/store memory with byte/word support.
- cpu.v — Top-level CPU integration.
- define.vh — Encodings for opcodes, function bits, and widths.

Testbenches: ALU_tb.v, pc_tb.v, cpu_tb.v.

Quartus project files: cpu.qpf, cpu.qws.

Instruction Flow
- Fetch: PC feeds instruction memory → fetch 32-bit instruction.
- Decode: Control interprets opcode/funct, register file reads operands.
- Execute: ALU performs arithmetic/logical op on register/immediate inputs.
- Memory: If MemRead/Write asserted, DataMemory is accessed via ALU result.
- Write-back: Result or loaded data written back to registers.
- Next PC: Branch or jump target selected; else PC increments sequentially.

Verification & Waveform Debugging
Verification was central to this project:
- Module-level testbenches (ALU_tb, pc_tb) ensured correctness of isolated units.
- System testbench (cpu_tb) ran small programs to validate integration.

RTL Simulation & Debugging Steps:
- Used waveform viewers (ModelSim/Vivado) to trace signals across cycles.
- Checked correct propagation of control signals from control.v into ALU/DataMem.
- Verified muxes (ALUSrc, MemtoReg, PC selection) against expected instruction types.
- Observed timing of RegisterFile writes (on clock edge) vs reads.
- Debugged branch behavior by analyzing ALU zero flag + control logic → PC mux path.

Waveform Scenarios Captured:
- R-type add: operands fetched, ALU result written back.
- Load → use: memory read followed by register writeback.
- Branch taken vs not taken: PC selection visibly toggling in waveform.

Why This Project Matters
- Designing a CPU from scratch demonstrates the integration of theory and practice:
- Mathematically precise Boolean logic and finite state control.
- Engineering discipline in RTL simulation, modular verification, and waveform debugging.
- Clear connection to real hardware (synthesizable and FPGA-deployable).

This project is the main highlight of the repository, showing the ability to not only write Verilog but to design, verify, and debug a complete processor system.
