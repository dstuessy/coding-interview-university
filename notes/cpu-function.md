# How a CPU works

## Micro architecture

**Consists of**
- Memory (RAM)
- Registers (4 8-bit units)
- An table of instructions
- Instruction address register
- Instruction register

### Instruction table

| Instruction | Description                                          | 4-bit Opcode | Address or Registers                 |
|-------------|------------------------------------------------------|--------------|--------------------------------------|
| LOAD\_A     | Read RAM location into register A                    | 0010         | 4-bit RAM address                    |
| LOAD\_B     | Read RAM location into register B                    | 0001         | 4-bit RAM address                    |
| STORE\_A    | Write from register A into RAM location              | 0100         | 4-bit RAM address                    |
| ADD         | Add two registers, store result into second register | 1000         | 2-bit register ID, 2-bit register ID |


### Diagram of a CPU


```                       
                                        ________
    |RA| |RB| |RC| |RD|                 |      |
    |__| |__| |__| |__|          _______|      |
                                 |      | RAM  |
                        |IR|_____|      |      |
                        |__| DATA IN/OUT|      |
                         |              |      |
                        |IAR|___________|      |
                        |___|  ADDR IN  |______|
        
```


