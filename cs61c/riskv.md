# Assembly Language
The basic job of a CPU is to execute a lot of **instructions**. Instructions are primitive operations. Different CPUs implement different sets of instructions, where the set of instructions of a particular CPI is an *Instruction Set Architecture* (ISA). 

## Instruction Set Architecturers
The early trend was to add more and more instructs to new CPUs to elaborate operations. The RISC philosophy reducted the ISA by:
1. Keeping the instruction set small and simple amkes it easier to build fast hardware.
2. Let software do complicated operations by composing simpler ones.

### RISK-V Architecutre
**RISK-V** is a new, open-sourced, license-free ISA specification supported by growing a shared software ecosystem. It is approprate for all levels of computing systems with many bit variants.

## Assembly Variables
**Registers** are assembly operands (like variables). There are a limited number at special locations built directly into hardware, allowing them to be very fast. Registers lie *within* the processor. <br>
Despite their speed, since registers lie in hardware, there are only a limited number of them. There are **32 registers** in RISC-V. In RV32, each reigster is 32 bits wide, whereas in RV64, each register is 64 bits wide. <br>
Registers are numbered from `x0` to `x31`. `x0` is special and always holds the value `0`. Reach register can be referred to by number *or* name. 

### Variables vs Registers
In C, variables must be declared first and given a type. Each variable can only present a value of that type. In assembly, registers *have no type*. The operation determinds how register contents are treated. 

### Assembly Intructions
In assembly, each statement is called an **instruction**, which executes *exactly one* of a short list of simple commands. Unlike in C, each line of assembly code contains at most one instruction, which can be thought of as operations. 

## Risk-V Instructions
Instructions follows the same rigid syntax of 1 operator and 3 operands:
```
one two, three, four
```
`one` is the operation name, `two` is the destination, `three` is the 1st operand, and `four` is the second operand.<br>
1. Addition in assembly: `add x1, x2, x3` is equalivant to `x1 <- x2 + x3`
2. Subtraction in assembly: `sub x1, x2, x3` is equalivant to `x1 <- x2 - x3`
More complcated instructions must be broken down. For example, `a = b + c + d - e` is:
```
add x10, x1, x2				#a = b + c
add x10, x10, x3 			#a = a + d
sub x10, x10, x4			#a = a - e
```

#### Immediates
**Immediates** are numerical constants that appear often in code so are alloted a special instruction. 
```
addi x3, x4, 10 # f = g + 10
```
There is no subtract immediate. 

### Storing Data
Data is typically smaller than 32 bits, but rarely smaller than bits. Data is stored in memory in a **little-endian** convention, meaning the word address is the same as the address of the rightmost (least-significant) byte. 

### Transfering Data
Registers are 50-500 times faster in terms of **latecy**. <br>
`lw` or **load word** loads an element from memory into the register:
```
lw x10, 12(x15)
```
The opposite, `sw`, or **save word**, loads an element from the register into memory:
```
sw x10, 40(x15)
```
Byte counterparts of `lw` and `sw` exist as `lb` and `sb`, or **load byte** and **save byte**, respectively. To preserve the sign, numbers are copied to *sign-extend*, which essentially smears the most singificant bit to fill the memory address. RISC-V supports `lbu`, or **load byte unsigned** to *zero-extend* the register. 

### Computer Decision Making
The *if* statement is essential to programming. A RISC-V equalivalent instruction is `beg`, or **branch if equal**:
```
beg reg1, reg2, L1  #go to L1 if (value in reg1) == (value in reg2)
```
`bne`, or **branch if not equal** is the opposite. `blt` and `bge` are **branch if tless than** and **branch if greater than or equal**, respectively. Both have an unsigned alternate in `bltu` and `bgeu`. <br>
`j` or **jump** is an unconfitional branch. 

### Logical Instructions
Operator|C|RISC-V
---|---|---
AND|&|`and`
OR|\||`or`
XOR|^|`xor`
left logical shift|<<|`ssl`
right logical shift|>>|`srl `
right arthmetic shift|N/A|`sra` 
`sra` moves n bits to the right and *inserts high-order sign bits into empty bits*. This is not the same as dividing by 2<sup>n</sup>, as it fails for odd negative numbers. C arthmetic semantics mandate that division should round towards 0.  