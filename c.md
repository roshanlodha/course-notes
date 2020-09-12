# Introduction to C
### History
- 1946: ENIAC (University of Pennsylvania): the first electronic general purpose computer
	- Could multiply in 10 digit numbers at 2.8ms!
	- Needed 2-3 days to setup a new program, which would be done via patch cords and switches 
- 1949: EDSAC (Cambridge University): the first general *stored-program* computer
	- Programs held as numbers (32-bit binary two's complement) in memory

## Computer Organization
### Abstraction
![Alt][/abstraction.svg "Layers of Abstraction"]<br>
C lets us write programs that can exploit underlying features of the architecture.

### Compile vs Interpret
C **compilers** map C programs directly into *architecture-specific* machine code. In C, compilation takes two steps:
1. Compiling `.c` files to `.o` files
2. Linking `.o` files to executables.
	a. **Assembling** is also automatically done, but it is hidden.
![Alt][/compilation.svg "Compilation"]<br>
Compilation provides reasonable complication time performance and excellent run-time performance (generally significantly faster than Scheme or Java). However, compiled files, including the executable, are architecture-specific and depend on the processor type and the operating system. Thus, executables must be rebuilt on each new system. Moreover, while compilation can be done in parallel, the linker must be performed sequentially. 

### C Pre-Preocessor (CPP)
The CPP allows C source files to first pass through a macro processor before the complier sees the code, replacing comments with a single space. CPP commands (beginning in `#`) are also replaced appropriatly.
```
#include "file.h" /* inserts file.h into output */
#include <stdio.h> /* looks for file in standard location */
#define PI (3.14159) /* defines a constant */
#if{text}/#endif /* conditionally include text */
```
CPP works via string replacement, and if not used properly, the C Pre-Processor can create interesting errors.

## C Language
### C vs Java
Category | C | Java
---|---|---
Type of Language|Function Oriented|Object Oriented
Programming Unit|Function|Class (abstract data type)
Compliation|`gcc` creates machine language code|`javac` creates Java virtual machine bytecode
Execution|`a.out` loads and executes program|`java Hello` interprets bytecode
Preprocessor?|yes|no

The operators between C and Java are nearly identical. 

### C Syntax
#### True vs False
False: `0` (int), `NULL` (pointer), `FALSE` (boolean)
True: everything else 

#### Typed Variabels
Type | Description | Example
---|---|---
`int`|integers (including negative)|`0`, `78`, `-217`, `0x7337`
`unsigned int`|unsigned integers|`0`, `6`
`float`|floating point decimal|`0.0`, `3.14159`
`double`|higher precision floating point|`0.0`, `3.14159`
`char`|single character|``c``, ``\n``
`long`|longer `int`|123976128

In C, functions are typed by their return values. You must declare the type of data you plan to return from a function, which can be any C variable type. A `void` return type signals `NULL`. 

#### Structs
```
typedef struct {
	int length_in_seconds;
	int year_recorded;
} SONG;

SONG song1;
song1.length_in_seconds = 213; /* dot notation */
song1.year_recorded = 1994;
```

#### Control Flow
Control flow in C is remarkably similar to Java.
- `if-else`: `if (expression) {statement}`

- `while`: `while (expression) {statement}`

- `for`: `for (initialize; check; update) {statement}`
- `switch`: 
```
switch (expression) {
	case case1: statements
	case case2: statements
	default: statements
}
```

## Bugs and Pointers
