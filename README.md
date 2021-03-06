# Snake VM (virtual machine)
[![Build Status](https://travis-ci.org/travcunn/snake-vm.svg?branch=master)](https://travis-ci.org/travcunn/snake-vm) [![codecov](https://codecov.io/gh/travcunn/snake-vm/branch/master/graph/badge.svg)](https://codecov.io/gh/travcunn/snake-vm)
[![Code Health](https://landscape.io/github/travcunn/snake-vm/master/landscape.svg?style=flat)](https://landscape.io/github/travcunn/snake-vm/master)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/839593667d9742098652296b8d0f1aa1)](https://www.codacy.com/app/tcunningham/snake-vm)


A simple virtual machine and program assembler written in Python

# Table of Contents
- [Installation](#installation)
- [Testing](#testing)
- [Usage](#usage)
  - [Assembler Usage](#assembler-usage)
  - [CPU Usage](#cpu-usage)
  - [Putting it all together](#putting-it-all-together)
- [Example Program](#example-program)
- [Architecture](#architecture)
  - [VM](#vm)
  - [Instruction Set](#instruction-set)
  - [Memory](#memory)
  - [IO](#io)

# Installation
```
# python setup.py install
```

# Testing
```
# py.test
```
Code coverage:
```
# py.test --cov-report=term-missing --cov=snake tests/
```

# Usage
### Assembler Usage
```
# assembler -h
usage: assembler [-h] [-o OUTFILE] file

A 2 pass assembler.

positional arguments:
  file                  file to be assembled.

optional arguments:
  -h, --help            show this help message and exit
  -o OUTFILE, --outfile OUTFILE
                        output file (default: None)

```

### Snake VM usage
```
# snakevm -h
usage: snakevm [-h] file

A simple vm.

positional arguments:
  file                  bytecode file to be loaded.

optional arguments:
  -h, --help            show this help message and exit
```

### Putting it all together
Alternatively, data can be piped into both the VM and assembler.
```
# assembler < programs/test.src | snakevm
```

# Example Program
``` asm
a       DATA    4
b       DATA    2
result  DATA    0

        ADD     a
        ADD     b
        STO     result
        OUT     result
        HRS     a
```
Assemble the program:
```
# assembler < programs/test.src
002
800
004
4
005
2
006
0
010
204
011
205
012
606
013
506
014
904
002
810
```
Run the program:
```
# assembler < programs/test.src | snakevm
6
```

# Architecture
### VM
For performance, Snake VM uses a register-based bytecode interpreter.

The VM has 3 registers:
- Program counter register
- Instruction register
- Accumulator register

### Instruction Set

| Opcode | Mnemonic | Instruction               | Description                                                                                                                                                                                                              |
|--------|----------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 0      | INP      | Input                     | take a number from the input card and put it in a specified memory cell.                                                                                                                                                 |
| 1      | CLA      | Clear and add             | clear the accumulator and add the contents of a memory cell to the accumulator.                                                                                                                                          |
| 2      | ADD      | Add                       | add the contents of a memory cell to the accumulator.                                                                                                                                                                    |
| 3      | TAC      | Test accumulator contents | performs a sign test on the contents of the accumulator; if minus, jump to a specified memory cell.                                                                                                                      |
| 4      | SFT      | Shift                     | shifts the accumulator x places left, then y places right, where x is the upper address digit and y is the lower.                                                                                                        |
| 5      | OUT      | Output                    | take a number from the specified memory cell and write it on the output card.                                                                                                                                            |
| 6      | STO      | Store                     | copy the contents of the accumulator into a specified memory cell.                                                                                                                                                       |
| 7      | SUB      | Subtract                  | subtract the contents of a specified memory cell from the accumulator.                                                                                                                                                   |
| 8      | JMP      | Jump                      | jump to a specified memory cell. The current cell number is written in cell 99. This allows for one level of subroutines by having the return be the instruction at cell 99 (which had '8' hardcoded as the first digit. |
| 9      | HLT      | Halt                      | stop program execution.                                                                                                                                                             |
| 10     | MUL      | Multiply                  | multiply the contents of the accumulator by the memory cell and store in the accumulator                                                                                                                                 |
| 11     | DIV      | Divide                    | divide the contents of accumulator by the memory cell and store in the accumulator                                                                                                                                   |


### Memory
There are 4096 'virtual' memory locations.

### IO
Input and output are handled through stdin/stdout.
