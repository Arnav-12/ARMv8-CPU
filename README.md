
# Pipelined CPU with Hazard Detection and Forwarding Unit

Designing a pipelined CPU based on the ARM architecture, specifically an ARMv8 (often referred to in educational contexts as "LEGv8" for learning purposes), with hazard detection and a forwarding unit, involves creating a processor that efficiently executes instructions in a pipelined manner while handling various types of hazards. This design aims to maximize performance, reduce instruction execution latency, and maintain correct program execution order.



## Pipelined CPU Architecture
A pipelined CPU architecture divides the process of executing instructions into several stages, allowing multiple instructions to be processed simultaneously, each at a different stage. A typical pipeline for a modern CPU might include stages like Fetch (IF), Decode (ID), Execute (EX), Memory Access (MEM), and Write Back (WB).
## Hazard Detection
Hazards are conditions that prevent the next instruction in the instruction stream from executing during its designated clock cycle. Hazards can be broadly categorized into three types:

Data Hazards: Occur when instructions that are close together in the instruction stream operate on the same data. For example, if one instruction writes to a register that the next instruction reads from, and the data is not yet written back, it can lead to incorrect results.
Control Hazards: Occur due to the pipelining of branch instructions, which might change the flow of which instructions are fetched before the branch direction is resolved.
Structural Hazards: Occur when two instructions require the use of the same hardware resource at the same time.
## Forwarding Unit
A forwarding unit, also known as a data hazard resolution unit, is a hardware mechanism in a pipelined CPU that mitigates data hazards by "forwarding" the result of a computation from one pipeline stage to an earlier stage where it is needed. This allows the dependent instruction to use the correct data without waiting for it to be written back to the register file, thus avoiding stalls that would otherwise be necessary for correct operation.
## Implementing Hazard Detection and Forwarding in an ARMv8 Pipelined CPU
Hazard Detection Unit: This unit's responsibility is to detect the various types of hazards. For data hazards, it checks the source operands of the currently decoding instruction against the destination operands of instructions in the execute, memory, and write-back stages. For control hazards, it must detect branches and make predictions or stall the pipeline if necessary until the branch resolution. Structural hazards are typically managed by ensuring sufficient resources are available or by stalling if conflicts occur.

Forwarding Unit: Upon detecting a data hazard that can be resolved by forwarding, this unit redirects the output from an ALU operation (or a data load from memory) directly to an input of the ALU in an earlier stage where it is needed. This is often done through multiplexers that select between the normal source of the data (e.g., the register file) and the forwarded data from a later pipeline stage.

Implementing these features in an ARMv8 pipelined CPU involves careful design to ensure that the forwarding paths cover all possible scenarios where data hazards could occur, and that the hazard detection unit can accurately identify and mitigate hazards to maintain correct program execution. This can significantly improve the performance of the CPU by reducing the number of stalls and bubbles (idle cycles) in the pipeline, thereby increasing instruction throughput.
## Test Program (instructions)

The thirteen instructions as shown in the table below is the test program used to test the functionality of the CPU.

| Line # |      ARM Assembly     |                Machine Code             | Hexadecimal|
|:------:|:----------------------|:---------------------------------------:|:----------:|
|    1   | `LDUR x0, [x2, #3]` | 1111 1000 0100 0000 0011 0000 0100 0000 | 0xf8403040 |
|    2   | `ADD x9, x0, x5`    | 1000 1011 0000 0101 0000 0000 0000 1001 | 0x8b050009 |
|    3   | `ORR x10, x1, x9`   | 1010 1010 0000 1001 0000 0000 0010 1010 | 0xaa09002a |
|    4   | `AND x11, x9, x0`   | 1000 1010 0000 0000 0000 0001 0010 1011 | 0x8a00012b |
|    5   | `SUB x12 x0 x11`    | 1100 1011 0000 1011 0000 0000 0000 1100 | 0xcb0b000c |
|    6   | `STUR x9, [x3, #6]` | 1111 1000 0000 0000 0110 0000 0110 1001 | 0xf8006069 |
|    7   | `STUR x10, [x4, #6]`| 1111 1000 0000 0000 0110 0000 1000 1010 | 0xf800608a |
|    8   | `STUR x11, [x5, #6]`| 1111 1000 0000 0000 0110 0000 1010 1011 | 0xf80060ab |
|    9   | `STUR x12, [x6, #6]`| 1111 1000 0000 0000 0110 0000 1100 1100 | 0xf80060cc |
|   10   | `B #10`             | 0001 0100 0000 0000 0000 0000 0000 1010 | 0x1400000a |