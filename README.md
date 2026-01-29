# Simple C Compiler

A lightweight compiler for a simplified subset of the C programming language that translates source code into x86-64 assembly language.

## Repository Notice

This repository serves as a documentation of the project's architecture, design decisions, and final results. While the primary codebase is not public to maintain compliance with institutional policies, a full code review can be arranged upon request through [Linkedin](https://www.linkedin.com/in/neel-vachhani/) or [Email](mailto:vachhani.neel12@gmail.com).

## Overview

This project implements a compiler for a "simple C" language using Lex (lexical analyzer) and Yacc (parser generator). The compiler parses C-like source code and generates corresponding x86-64 assembly code that can be assembled and linked to create executable programs.

## Tech Stack

- **Flex** - Lexical analyzer generator for tokenization
- **Bison/Yacc** - Parser generator for syntax analysis
- **C** - Implementation language
- **x86-64 Assembly** - Target architecture
- **GCC** - GNU Compiler Collection for assembly and linking
- **System V AMD64 ABI** - Calling convention

## Features

### Supported Language Constructs

#### Data Types
- `long` - 64-bit integer type
- `long*` - pointer to long
- `char*` - character pointer/string
- `char**` - pointer to character pointer
- `void` - void type for functions

#### Control Flow
- **Conditional statements**: `if`, `if-else`
- **Loops**: 
  - `while` loops
  - `do-while` loops
  - `for` loops
- **Jump statements**: `break`, `continue`, `return`

#### Operators
- **Arithmetic**: `+`, `-`, `*`, `/`, `%`
- **Relational**: `<`, `>`, `<=`, `>=`
- **Equality**: `==`, `!=`
- **Logical**: `&&`, `||`
- **Assignment**: `=`
- **Address-of**: `&`

#### Other Features
- Global and local variables
- Function definitions with up to 6 parameters
- Function calls
- Arrays (indexing and assignment)
- String constants
- Integer constants

## Architecture

### Components

1. **Lexical Analyzer (`simple.l`)**
   - Tokenizes input source code
   - Recognizes keywords, identifiers, operators, and constants
   - Tracks line numbers for error reporting

2. **Parser (`simple.y`)**
   - Implements grammar rules for the simple C language
   - Generates x86-64 assembly code during parsing
   - Manages symbol tables for variables and strings
   - Handles register allocation and stack frame management

### Code Generation

The compiler generates x86-64 assembly code with the following characteristics:

- **Register Usage**:
  - Stack registers: `rbx`, `r10`, `r13`, `r14`, `r15`
  - Argument registers: `rdi`, `rsi`, `rdx`, `rcx`, `r8`, `r9`
  - Byte registers: `bl`, `r10b`, `r13b`, `r14b`, `r15b`

- **Memory Management**:
  - Local variables stored on the stack (relative to `rbp`)
  - Global variables allocated in data section
  - Stack frame properly aligned to 16 bytes

- **Function Prologue/Epilogue**:
  - Saves and restores callee-saved registers
  - Manages stack pointer and frame pointer
  - Follows System V AMD64 ABI calling convention

## Building and Usage

### Prerequisites
- Flex (lexical analyzer generator)
- Bison or Yacc (parser generator)
- GCC (GNU Compiler Collection)
- x86-64 compatible system

### Compilation

```bash
# Generate lexer
flex simple.l

# Generate parser
bison -d simple.y

# Compile the compiler
gcc -o simple lex.yy.c y.tab.c -lfl

# Use the compiler
./simple program.c

# This generates program.s (assembly file)

# Assemble and link
gcc program.s -o program

# Run the executable
./program
```

## Example

### Input (`example.c`)
```c
long factorial(long n) {
    if (n <= 1) {
        return 1;
    }
    return n * factorial(n - 1);
}

long main() {
    long result;
    result = factorial(5);
    return result;
}
```

### Output
The compiler generates `example.s` containing x86-64 assembly code that implements the factorial function.

## Limitations

- Maximum 5 arguments per function call
- Maximum 100 global variables
- Maximum 32 local variables per function
- Maximum 100 string constants
- Limited type system (no structs, unions, or custom types)
- No pointer arithmetic beyond array indexing
- No preprocessor directives

## Implementation Details

### Symbol Tables
- **Global Variables**: Tracked in `global_vars_table[]`
- **Local Variables**: Tracked in `local_vars_table[]`
- **String Constants**: Tracked in `string_table[]`
- **Type Information**: Stored alongside variable names

### Register Stack
The compiler uses a register stack for expression evaluation:
- `top` variable tracks current stack position
- Operands pushed onto register stack
- Operations performed using top registers
- Results stored back in registers

### Label Generation
The compiler generates unique labels for control flow:
- `nlabel` counter ensures unique label names
- Labels for if-else branches
- Labels for loop constructs (start, end, body)
- Labels for string constants

## Error Handling

- Line number tracking for error reporting
- Parse errors reported with file name and line number
- Invalid tokens detected by lexer

## Future Enhancements

Potential improvements for the compiler:
- Support for more data types (float, double, structs)
- Enhanced optimization (constant folding, dead code elimination)
- Better error messages and recovery
- Support for more C language features
- Improved register allocation
- Code optimization passes
