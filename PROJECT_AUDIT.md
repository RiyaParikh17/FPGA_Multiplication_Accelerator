# Project audit and handoff notes

## Facts documented in the supplied technical report
- Project: RISC-V processor based SoC with matrix multiplication accelerator.
- HDL: Verilog HDL.
- EDA tool stated in report: AMD Xilinx Vivado 2024.2.
- Processor modules described: PC, instruction decoder, register file, immediate generator, ALU, memory subsystem, and FSM control.
- Accelerator components described: BRAM-based matrix storage, address generation, multiplication, accumulation, index control, control FSM, and result storage.
- The report's Chapter 12 verification table marks listed processor and accelerator tests as PASS. These results have not been independently rerun while preparing this repository.

## Current accelerator implementation context from project discussion
- Matrix A and transposed matrix BT are stored in separate memories.
- Current matrix-size assumption: dimensions are multiples of four.
- Planned optimization: pipelined parallel multiply-accumulate datapath.
- Do not describe the pipelined parallel MAC as implemented or benchmarked until RTL and measured results support that claim.

## Project metadata
- The supplied Vivado project metadata identifies target part `xc7s25csga225-1`. Confirm the intended board/device.
- Project files are retained under `projects/`.
- Generated Vivado caches and run outputs are intentionally not included.

## Recommended validation before claiming a release
- [ ] Open each `.xpr` file in a compatible Vivado version.
- [ ] Resolve missing or version-incompatible IP/output products.
- [ ] Confirm top modules, constraints, and target part.
- [ ] Run processor testbenches and save logs.
- [ ] Run accelerator testbenches, including matrix sizes at the supported boundaries.
- [ ] Compare matrix results against a trusted software/reference calculation.
- [ ] Implement and benchmark the pipelined parallel MAC only after the current baseline is captured.
- [ ] Review licensing of any third-party IP before inviting reuse.

## Block diagram references
- `block-diagrams/matrix-multiplication-accelerator-block-diagram.pdf`: supplied accelerator Vivado block design diagram.
- `block-diagrams/risc-processor-block-diagram.pdf`: supplied processor Vivado block design diagram.
