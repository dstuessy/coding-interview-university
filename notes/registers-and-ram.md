# How registers and RAM work

## Looped circuits 

### Looped OR gate

```
A ---|OR|_C___ 
B ---|__| |
   |______|
```

This circuit permanently sets its output to `1` once either `A` or `B`
are `1`. Explained: the `C` will be `1` if either `A` or `B` is `1`.
Once `C` is `1`, `B` is `1`, therefore the output is `1`. This is 
permanent.

These are useful to persist a value of `1`.

### Looped AND gate

```
A ---|AND|_C___ 
B ---|___| |
   |_______|
```

This doesn't have the same degree of permanency as a looped OR gate.
`C` will only be `1` if both `A` and `B` are `1`. Now... there's
a loop hole. That once `C` is `1`, `B` will be `1`, therefore as long
as `A` remains `1` after `C` is `1`, `C` will be `1` if `B` is `0`.

These are useful to persist a value of `0`.

### AND-OR latch

This takes us to the AND-OR latch. These are made by combining a 
looped OR gate and a looped AND gate.

```    ________________________
       |                      |
       --|OR|----------|AND|__|___ OUTPUT
SET -----|__| |NOT|----|___| 
RESET --------|___|
```

In this circuit there are two inputs. `SET` and `RESET` -- the former
is to set a value, the latter is to clear it. The `OR` and `AND` gates
work together to keep a `1` stored when `SET` is `1`. Then to clear it
if `RESET` is `1`. 

The way this works is that if `SET` is `1`, the `OR` will emit `1`.
The `AND` will then receive this and if `RESET` is `0`, the `AND` will
have two `1`s (this is thanks to the `NOT` gate, flipping the `RESET`
value), making the output `1`. Then the loop back will supply the `OR`
with `1` again, permanently storing the `1`, even if `SET` is `0`.
Now. If `RESET` is `1`, the `AND` will receive `0` (again, thanks to
`NOT`) and a `1`. This sets the loop back to `0`, which in turn sets
the output to `0`.

Basically, store a `1` or a `0`.

### Gated latch

Using two wires to store a `1` or a `0` is inconvenient. For this
reason a more abstracted circuit called a gated latch is used. Instead
it requires a data input and write enable signal.

```
                          ________________________
                          |                      |
DATA                      --|OR|                 |
INPUT ------|-------|AND|---|__|\                |
        ____ç_______|___|        \               |
        |   |_|NOT|               -----|AND|_____|_____ OUTPUT
WRITE   |     |___|----|AND|--|NOT|----|___|
ENABLE ----------------|___|  |___|
```

Here the data input can either be `0` or `1`. Whether or not the value
is stored by the latch depends on if the write enable signal is `1`
or `0`.

For example, a `1` will only be stored because the latch has an `AND`
for both `DATA INPUT` and `WRITE ENABLE`.

A `0` is also stored due to the `AND` that precedes the `OR` of the
latch. This gives it a `0` to turn off, but if the latch has a `1` in
it's loop, the `OR` will remain with a `1`. To reset the loop to `0`,
the `AND` in the latch must be given a `0`, which is provided by the
preceding `NOT`, and an `AND` that connects to `WRITE ENABLE` as well
as a `NOT` that connects to `DATA INPUT`. 

So. If `DATA INPUT` is `0` and `WRITE ENABLE` is `1`, the latch will
receive a `0` at the `OR`. The second `AND` before the latch will be
`1` because it receives a `1` from `WRITE ENABLE` and `DATA INPUT` 
and its `NOT`. The following `NOT` then gives the latch's `AND` a `0`,
setting both the latch's `OR` and `AND` to `0`, making `OUTPUT` `0`.

Now. If `DATA INPUT` is `0` and `WRITE ENABLE` `0`, the latch will
receive a `0` at its `OR`. The second `AND` before the latch will be
`0` because it receives a `0` from `WRITE ENABLE` and a `1` from
`DATA INPUT` and its `NOT`. The following `NOT` gives the
latch's `AND` a `1`, setting both the latch's `OR` and `AND` to `1`,
making `OUTPUT` `1`.

Basically, turn on `WRITE ENABLE` to set the value of the gated latch
as the value of `DATA INPUT`.

## Registers

A group of latches is called a Register. Each latch stores one bit, 
therefore a register can hold 8 for 8-bit, or 16 for 16-bit.
The number of bits in a register is called its width, used to store
a single number. Most modern day computers have registers that are
64-bits wide.

So. Storing a number in a register is done by having all
`WRITE ENABLE` inputs wired together. That way only one wire is 
required to enable all of its gated latches. Then `DATA INPUT` wires
for the gated latches are used individually to write a bit of data
into each.

For small registers, these latches can be arranged in series. However,
modern 64-bit registers would need 64 wires for `DATA INPUT` and
64 wires for `OUTPUT`, only one wire for `WRITE ENABLE`, making a
total of 129 wires. And then for 256-bits, 513 wires.
This adds up a lot.

To reduce the number of wires used, a matrix can be used. In this
configuration, latches are arranged in a grid, with 16x16 latches 
for a 256-bit register. Columns and rows of wires are then used to
to activate the latches. Basically as an _(x, y)_ coordinate system.

Here's how a latch looks in within a matrix.

```
   |
   |
---Ç--------|--------------------------------------------------------
   |        |                                                                                            
   |        |   DATA IN/OUT ----------|------------------|                                                  
   |        |                         |___|GATED|        |
   |        |                             |LATCH|----|T|_|
   |        |   WRITE ENABLE ---|AND|_____|_____|    |_|
   |        |           ________|___|                 |
   |        --|AND|____/                              |
   |----------|___|    \________________|AND|_________|
   |     READ ENABLE -------------------|___|
```

In this configuration, only two wires are needed for `DATA INPUT`,
`OUTPUT` and `WRITE ENABLE`. One for both `DATA IN/OUT`, another for
`WRITE ENABLE`. The latter will only activate if both the wires for
the column and the row are active. This is due to the first `AND` to
the left of the diagram, connected to both the column and row wires.
This `AND` leads to two other `AND`s for `WRITE ENABLE` and
`READ ENABLE`. These won't turn `1` unless both the coordinate wires
are active and `WRITE ENABLE` or `READ ENABLE` are `1`. These then 
lead to their respective inputs or outputs of the `GATED LATCH`,
either reading or writing data.

To address a latch, an address is used... Go figure... These are
fed to a multiplexer as binary numbers such as 1000 for 8 or 1100
for 12. The multiplexer will then activate the corresponding columns
and rows.

```
MULTIPLEXERS (one per address/coordinate)

COLUMN ADDRESS (4-bit)
 |
 `--0  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15 16
0 __|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
1 __|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
2 __|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
3 __|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
4 __|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
5 __|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
6 __|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
7 __|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
8 __|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
9 __|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
10 _|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
11 _|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
12 _|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
13 _|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
14 _|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
15 _|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|__|  
 | 
ROW ADDRESS (4-bit)

```

## Memory

A matrix of latches can be abstracted to 256-bit memory with an 8-bit
address, `WRITE ENABLE` and `READ ENABLE` wires, and finally one
`DATA` wire for input or output.

```
           ______________________
        ===|                    |
8-bit   ===|                    |
ADDRESS ===|                    |
        ===|       256-BIT      |
           |       MEMORY       |
DATA    ---|                    |
WRITE      |                    |
ENABLE  ---|                    |
READ       |                    |
ENABLE  ---|____________________|
```

To store more useful amounts of memory, we can arrange them in a row
of eight.

```  
            |M| |M| |M| |M| |M| |M| |M| |M|
        ======----   |   |   |   |   |   |
8-bit   ==============----   |   |   |   |
ADDRESS ======================----   |   |
        ==============================----
             ||| ||| ||| ||| ||| ||| ||| |||
WRITE        ||| ||| ||| ||| ||| ||| ||| |||
ENABLE -----------------------------------||
              ||  ||  ||  ||  ||  ||  ||  ||
READ          ||  ||  ||  ||  ||  ||  ||  ||
ENABLE ------------------------------------|
               |   |   |   |   |   |   |   |
               -----------------------------
                             |
                        8-bit DATA

NOTE: due to ASCII limitations, it may look like WRITE ENABLE,
READ ENABLE, and 8-bit DATA are joined. These are not. They overlap
each other.
```

Storing 8-bits of data (a byte), each 

NOTE: video left off here https://youtu.be/fpnE6UAfbtU?t=538

