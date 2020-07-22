# How an arithmetic logic unit (ALU) works

This is divided into two halves. An arithmetic unit, and a logic unit.

## Arithmetic Unit

This will be based on logic gates.

Single bit arithmetic is done like an XOR gate, with and AND gate
used to carry the 1 for `1+1`:

```
0+0 = 0
1+0 = 1
0+1 = 1
1+1 = 0 (carry the 1)
```

This results in the following circuit:

```
A --|----|XOR\_____ SUM
B --|----|___/
    |
    |-----|AND\____ CARRY
    |_____|___/
```

This is called a _Half Adder_.

To make a full adder it takes two half adders. This takes three
numbers as input, though. The `OR` gate is used to check if either one
of the carry bits is true:  

```
A ---|HALF ADDER|C-------------------|OR\_____ 1st digit of result
B ---|__________|S----|HALF ADDER|C--|__/
C --------------------|__________|S----------- 2nd digit of result
```

e.g. for `1+1`, the outcome would be `10`, which is binary for `2`.
To explain, the input is `A=0` `B=0` and `C=0` the half adder
for `A` and `B` gives a sum of `0` and a carry of `1`. The second
half adder takes the sum of the first and `C`. This makes
a carry of `0` and a sum of `0`. The `OR` gate that follows takes the
carry units of both half adders. If there ever was a carry for any
half adder, the first digit of the output is `1`. The sum of the
second half adder, `0` is taken as the second digit of the output.
Technically, the first digit is a carry and the second is the sum. 
This allows to chain adders for larger computations.

Here's a table of all possible sums for overview:

|---|---|---|-------|-----|
| A | B | C | Carry | Sum |
|---|---|---|-------|-----|
| 0 | 0 | 0 |   0   |  0  |
|---|---|---|-------|-----|
| 0 | 0 | 1 |   0   |  1  |
|---|---|---|-------|-----|
| 0 | 1 | 0 |   0   |  1  |
|---|---|---|-------|-----|
| 1 | 0 | 0 |   0   |  1  |
|---|---|---|-------|-----|
| 0 | 1 | 1 |   1   |  0  |
|---|---|---|-------|-----|
| 1 | 1 | 0 |   1   |  0  |
|---|---|---|-------|-----|
| 1 | 1 | 1 |   1   |  1  |
|---|---|---|-------|-----|


As mentioned, adders can be chained to compute larger numbers. 
Here's an example for 8 bits called an '8-bit ripple carry adder'.

```
A0 ---|HALF ADDER|----------------------------------------------------------------------------------------------- sum0 
B0 ---|__________|-|FULL ADDER|
A1 ----------------|          |---------------------------------------------------------------------------------- sum1
B1 ----------------|__________|-|FULL ADDER|
A2 -----------------------------|          |--------------------------------------------------------------------- sum2
B2 -----------------------------|__________|-|FULL ADDER|
A3 ------------------------------------------|          |-------------------------------------------------------- sum3
B3 ------------------------------------------|__________|-|FULL ADDER|
A4 -------------------------------------------------------|          |------------------------------------------- sum4
B4 -------------------------------------------------------|__________|-|FULL ADDER|
A5 --------------------------------------------------------------------|          |------------------------------ sum5
B5 --------------------------------------------------------------------|__________|-|FULL ADDER|
A6 ---------------------------------------------------------------------------------|          |----------------- sum6
B6 ---------------------------------------------------------------------------------|__________|-|FULL ADDER|
A7 ----------------------------------------------------------------------------------------------|          |---- sum7
B7 ----------------------------------------------------------------------------------------------|__________|---- carry
```

The last adder has a carry bit. This can result in outputs with more 
than 8-bit. This is called an overflow as the adder can only
support 8-bits. To avoid this, more full adders can be added to 16 
or 32 bit. This makes overflows less likely to happen, but requires
more gates and adds more time for each of the carries to ripple
forward through all the extra gates. This may only be a billionth of a 
second, but it adds up with large computational processes. 

To deal with overflow efficiently, a carry look ahead adder is used.

Notice that no multiply and divide operators were mentioned.
Simple ALUs are simply given multiple instructions to undergo multiple
additions as a form of multiplication. It's slower but gets the job
done. Simple CPUs use such ALUs, such as for microwaves, dish washers,
etc. Phones and PCs on the other hand have ALUs with dedicated
circuits for multiplication. There's nothing special about them. They
just require an ALU to have more logic gates. That's why expensive 
processors don't have them.

## Logic Unit

This performs logical operations. Like `AND`, `OR`, and `NOT`. 
It also performs simple numerical tests like checking if a number
is negative or if it is 0. 

To check if a number is `0`, a bunch of `OR` gates and a `NOT` gate
are used to see if the output is `1`. If any `1` is present in the
number, it is greater than `0`. Therefore the `OR` gates will return
a `1` if any `1` is present. The `NOT` gate then reverses the result
to indicate if the number is greater than `0`.

Here's a circuit diagram to illustrate.

```
A0 = 0 --|OR|
A1 = 0 --|__|--0-|OR|
A2 = 1 --|OR|--1-|__|--|
A3 = 0 --|__|          |--1-|OR|__1__|NOT|__0___ Not zero
A4 = 1 --|OR|          |--1-|__|     |___|
A5 = 0 --|__|--1-|OR|--|
A6 = 1 --|OR|--1-|__|
A7 = 0 --|__|
```

## ALU symbols and schematics

8bit ALUs use hundreds of logic gates. This is too much to have in 
schematic diagrams. For this reason a symbol is used to abbreviate:

```
      A    B
      |    |
OP -- \\  // -- O
       \\// --- Z
        |   --- N
        C
```

It's simply a capital `V` with two 8-bit inputs (`A` and `B` in this
example) and one 4-bit operation code (`OP` in this example). The
operation code indicates if addition or subtraction or multiplication
should be performed. The result of the operation is an 8-bit output
(`C` in this example). Lastly, the ALU also emits flags as 1-bit
outputs. An overflow bit (`O` in this example), a zero bit (`Z` in 
this example), and a negative bit (`N` in this example).

These are useful in various scenarios. For example, when subtracting
two numbers, the `Z` flag would be set to `1`, indicating the two 
numbers were equal! Another example, when subtracting two numbers, the
`N` flag would be `1` if `B` were greater than `A`. 

This is all super neat!

