# How a CPU works

`INC A` is the assembly instruction to increment register `A`. This
is be represented in binary as `00111100`, which is easier to read 
in hexadecimal format as `3C`.

1. The instruction must be stored in memory. e.g. `AE00`.
    - the CPU's program counter (PC) is made aware of this.
    - **note:** register `A` is not in memory. It is in the CPU's
register, under "cell" `A`.
    - let's give `A` a value of `00H` with an `H` to denote
it's hexadecimal.
2. The PC content is transferred to the memory address register (MAR).
3. The PC is then incremented by 1
    - this ensures the next memory location is pointed to. This could
be the next instruction, or operand to be dealt with.
4. Here's where it get's more complicated. This is when the fetch part
of the fetch decode execute cycle occurs
    1. the MAR sends `AE00` to memory via the address bus.
    2. meanwhile the control unit (`CU`) sends two low signals down
two control lines (one each). One to turn on the memory, another to 
tell the memory to read from the given address `AE00`.
    3. the value `3C` is taken from `AE00` via the data bus to the
buffer register (`BR`). A copy is then sent to the instruction register (`IR`).
5. Entering the decode stage of the fetch decode execution cycle.
    - the decoder is given the value `3C` from the `IR`
    - the decoder decodes the meaning of the operation `3C`. In this
case, `INC A` -- increment the contents of register `A`.
6. Then the execute part of the fetch decode execute cycle. The `CU`
is given the instruction.
    - to increment register `A`, the `CU` passes its contents to the
arithmetic logic unit (ALU) and asks it to to add plus one to it.
    - the new value `01H` is transferred via the internal data bus
back to register `A`.

