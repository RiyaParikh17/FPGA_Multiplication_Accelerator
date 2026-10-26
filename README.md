# FPGA Implementation of a RISC-V Processor Based SoC with Matrix Multiplication Accelerator

A Verilog/SystemVerilog FPGA project combining a modular RISC-V processor design with a dedicated matrix multiplication accelerator. The project is developed with AMD Xilinx Vivado.

**Status:** Work in progress  
**HDL:** Verilog HDL / SystemVerilog  
**EDA tool stated in the technical report:** AMD Xilinx Vivado 2024.2  
**Configured target part in the supplied Vivado project metadata:** `xc7s25csga225-1` (confirm the intended board/device before hardware use)

The accompanying technical report is available at [`docs/technical-report.pdf`](docs/technical-report.pdf).

## Project overview

This repository contains two related but separately maintained Vivado projects:

1. **RISC processor**: processor RTL modules and a Vivado block design.
2. **Matrix multiplication accelerator**: memory and buffer components, address generation, a Vivado block design, and testbench sources.

The intended system-level direction is a RISC-V based SoC with a dedicated matrix multiplication accelerator. The supplied archives contain separate project folders; this repository does not claim that a complete processor-to-accelerator interface has been integrated or demonstrated.

## 1. Matrix multiplication accelerator

Sources: [`projects/matrix-multiplication-accelerator/`](projects/matrix-multiplication-accelerator/)

The accelerator work focuses on matrix operand storage and access, address generation, and a matrix multiplication datapath.

### Current design context

- Matrix **A** and transposed matrix **BT** are stored in separate memories.
- The current implementation assumes matrix dimensions are multiples of four.
- The supplied RTL includes memory and buffer components, a B-to-BT buffer, buffer selection logic, and matrix address generation.
- The existing design direction is to develop a **pipelined parallel multiply-accumulate (MAC)** datapath for faster implementation. This is planned optimization work, not a claim that the pipelined parallel MAC has already been implemented or benchmarked.

Relevant source files include:

- `matrix_address_generator.sv`
- `main_memory.v`
- `generic_bram.v`
- `b_to_bt_buffer.v`
- `b_to_buffer_controller.v`
- `buffer_select_mux.v`

Testbench sources are present under the accelerator project's `Multiplication_Accelerator.srcs/sim_1/new/` directory.

### Matrix multiplication

For compatible dimensions,

\[
A_{m \times n} \times B_{n \times p} = C_{m \times p}
\]

and each output element is

\[
C_{ij} = \sum_{k=0}^{n-1} A_{ik}B_{kj}.
\]

The technical report describes BRAM-based matrix storage, address generation, multiplication and accumulation, index control, an accelerator control FSM, and result storage. The current implementation's multiple-of-four matrix-size assumption should be kept in mind when choosing test cases.

### Planned accelerator work

- [ ] Review and document the exact current RTL data path and memory interface.
- [ ] Verify the existing matrix-size assumptions and boundary cases.
- [ ] Implement a pipelined parallel MAC datapath.
- [ ] Verify arithmetic correctness against reference matrix multiplication.
- [ ] Measure latency, throughput, resource utilization, and timing from actual Vivado runs.
- [ ] Document supported matrix dimensions and data widths.

These items are a roadmap, not claims of completed work.

## 2. RISC processor

Sources: [`projects/risc-processor/`](projects/risc-processor/)

The supplied Vivado project includes RTL modules for:

- Arithmetic Logic Unit (ALU)
- Instruction decoder
- Register file
- Immediate generator
- Instruction memory and data memory
- Program counter register and PC logic
- Branch decision logic
- Controller FSM
- Multiplexers

The technical report describes a modular RISC-V processor organization with instruction fetch, decode, execute, memory access, and writeback phases. It describes a 32-entry general-purpose register file, ALU arithmetic and logical operations, load/store behavior, and FSM-based control. Refer to the report for the documented design description; instruction-set coverage and end-to-end behavior should be confirmed from the RTL and simulation.

## Architecture block diagrams

The following PDFs contain the Vivado block design diagrams supplied with this project. They are included as visual references for the module connections and top-level signal interfaces.

### Matrix multiplication accelerator

[View the matrix multiplication accelerator block diagram](docs/block-diagrams/matrix-multiplication-accelerator-block-diagram.pdf)

The diagram shows the B-to-BT buffering path, buffer selection mux, controller, main memory interface, and BRAM used for storing transposed matrix data (`BT`), along with control and debug signals.

### RISC processor

[View the RISC processor block diagram](docs/block-diagrams/risc-processor-block-diagram.pdf)

The diagram shows the processor's connected RTL blocks, including the instruction decoder, immediate generator, ALU, register file, program-counter logic/register, branch decider, controller FSM, instruction memory, data memory, and writeback mux.

These diagrams document the supplied Vivado block designs. They are architectural references and do not independently establish simulation, synthesis, timing, or hardware-validation status.

## Repository layout

```text
.
├── README.md
├── .gitignore
├── docs/
│   ├── PROJECT_AUDIT.md
│   └── technical-report.pdf
└── projects/
    ├── matrix-multiplication-accelerator/
    │   ├── Multiplication_Accelerator.xpr
    │   └── Multiplication_Accelerator.srcs/
    └── risc-processor/
        ├── ALU.xpr
        └── ALU.srcs/
```

Vivado source paths and project files are retained. Generated caches, run directories, and bulky temporary implementation outputs are excluded from this curated repository so they can be regenerated by Vivado.

## Open the projects in Vivado

1. Install a compatible version of AMD Xilinx Vivado. The technical report states Vivado 2024.2.
2. Open the relevant `.xpr` file:
   - Accelerator: `projects/matrix-multiplication-accelerator/Multiplication_Accelerator.xpr`
   - Processor: `projects/risc-processor/ALU.xpr`
3. Review any missing or version-incompatible IP warnings and regenerate output products when needed.
4. Confirm the top module, source sets, target part, and constraints before simulation or synthesis.
5. Run the available testbenches and save the actual logs and reports before publishing measured results.

The project metadata identifies `xc7s25csga225-1`; confirm that this matches the intended FPGA board/device. The repository has not been independently built or simulated as part of packaging.

## Verification status

The technical report's **Verification Summary** (Chapter 12) records PASS for processor and accelerator items including PC reset/update, instruction decoding, register file, immediate generation, ALU arithmetic/logical operations, load/store, BRAM initialization, matrix address generation, multiplier, accumulator, accelerator FSM, and matrix multiplication.

These are results stated in the supplied report. They have **not been independently rerun or validated during preparation of this GitHub package**. The presence of a testbench is not by itself proof of a passing run. Add reproducible simulator logs and Vivado reports when available.

No latency, throughput, clock-frequency, LUT, flip-flop, BRAM, DSP, or timing figures are claimed here because no verified implementation report with those measurements is included in this repository.

## Technical report

See [`docs/technical-report.pdf`](docs/technical-report.pdf) for the project report, including system architecture, processor modules, memory subsystem, accelerator control flow, matrix address equations, verification methodology, and FPGA implementation flow.

## Public repository checklist

Before or after publishing:

- [ ] Confirm the target board/device and Vivado version.
- [ ] Review third-party IP and licensing terms.
- [ ] Open both `.xpr` files and regenerate missing Vivado output products if required.
- [ ] Run the testbenches and retain actual pass/fail logs.
- [ ] Add real synthesis and timing reports if performance claims are made.
- [ ] Review all committed files for secrets, credentials, and unnecessary generated files.
- [ ] Add a license only after choosing terms that fit the project and any included IP.

## License

No license is included because no reuse or distribution terms have been selected. Unless a license is added, users should not assume permission to reuse, modify, or redistribute the project.
