# Assembly Language
The basic job of a CPU is to execute a lot of **instructions**. Instructions are primitive operations. Different CPUs implement different sets of instructions, where the set of instructions of a particular CPI is an *Instruction Set Architecture* (ISA). 

Command Summary:
<img src="https://">

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

### Psuedo-instructions
**Psuedo-instructions** are shorthand syntax for common assembly idioms.
```
mv rd, rs 	# addi rd, rs, 0
li rd, 13 	# addi rd, x0, 13
nop 		# addi x0, x0, 0
```

### Function Calls
There are 6 fundamental steps in calling a function:
1. Put arguments in a place where the function can access them.
2. Transfer control to the function.
3. Acquire local storage resources needed for the function.
4. Perform the desired task of the function.
5. Put the return value in a place where the calling code cna access it. Also restore any registers used, releasing local storage. 
6. Return control to the point of origin, since a function can be called from several points in a program. 

The eight *argument registers* `a0`-`a7` pass parameters and two return values. The `ra` is the *return address* registers, which is `x1`. <br>
`jal` links an address to that points to a calling site to allow functions to return to the proper address. Subsequently, we can jump to the address and simultaneously save the address of the following instruction in the `ra` register. Returning from a function is handled by the `jr` or **jump register** instruction. The psuedo-instruction `ret` is equivalent to `jr ra`. <br>
The `sp` or **stack pointer** points to the stack, allowing old values to be saved before calling functions and restored upon return. 

#### Function Calls Example
```
\\ C:
int Leaf(int g, int h, int i, int j) {
	int f;
	f = (g + h) - (i + J);
	return f;
}
```
```
# RISC-V
addi sp, sp, -8 	# adjust stack for 2 items
sw s1, 4(sp) 		# save s1 for future use
sw s0, 0(sp) 		# save s1 for future use

add s0, a0, a1		# f = g + h
add s1, a2, a3		# s1 = i + j
add a0, a0, s1		# return value (g + h) - (i + j)

lw s0, 0(sp)		# restore register for s0
lw s1, 4(sp)		# restore register for s1
addi sp, sp, 8		# adjust stack to delete 2 items
ret 				# return
```

### Nested Calls and Register Conventions
Consider the falling C code:
```
\\ C
int sumSquare(int x, int y) {
	return mult(x, x) + y;
}
```
Registers will be clobbered, meaning the return address will be unclear. This is solved by pushing important registers onto the stack. **Register conventions** are a set of generally accepted rules as to which registers will be unchanged after a procedure call (`jal`) and which may be changed.
* *Saved* registers:
	* Caller can rely on the values being unchanged across register called
	* `s0`-`s11` (`s0` is the *stack pointer*)
* *Temporary* registers:
	* Caller cannot rely on values being unchanged
	* `a0`-`a7`, `ra`, `t0`-`t6`

C has 2 types of variables: *automatic* (discarded on function exit) and *static* (exist across exits). 
The above C code translates to the following RISC code:
```
sumSquare:
	addi sp, sp, -8	# create stack space
	sw ra, 4(sp)	# save the return address
	sw a1, 0(s0)	# save the value of y
	mv a1, a0		# copy x
	jal mult		# call multiply
	lw a1, 0(sp)	# restore y
	add a0, a0, a1 	# add result of mult (stored in a0) with y
	lw ra, 4(sp)	# restore return address
	addi sp, sp, 8	# clean up
	jr ra 			# return
mult:
	...
```

## RISC-V Instruction Formats
The machine cannot understand RISC-V instructions, and they must be converted into bit strings. As a result, programs are largely distributed in binary form, meaning programs are bound to specific instruction sets. Similarly, new binaries are required for new machines, encouraging backwards compatibility. 

#### Instructions as Numbers
All RISC instructions are read as a bitstring, meaning instructions are simply numbers. For simplicity, these are generally 32-bit numbers. One work is 32 bits, so we divide instruction word in fields. 

### R-format
