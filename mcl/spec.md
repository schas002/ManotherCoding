# Manother Coding Language

*Working Draft 2, as of 2017-05-25*

Specification

<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>.

* * *

## Contents

- [Syntax](#syntax)
	- [Data types](#data-types)
		- [Numbers](#numbers)
	- [Commands](#commands)
	- [Sanitization](#sanitization)
	- [Execution](#execution)
		- [Starting values](#starting-values)
		- [NOP axiom](#nop-axiom)
- [Instructions](#instructions)
	- [Outside World Ops](#outside-world-ops)
	- [Medium Ops](#medium-ops)
	- [Stack Ops](#stack-ops)
	- [Flow Control](#flow-control)

* * *

## Syntax

### Data types

The language has a meaning of data types. However, as of yet the only data type MCL operates on is numbers.

#### Numbers

A **number** is a signed integer which can go at least up to 255 (2<sup>8</sup> - 1).

In the instruction listings, they are denoted as `a`, however that can also denote any type at all.

### Commands

A **command** is either a single character command or an extended command.

A **single character command** is a character that is neither whitespace nor `x`.

An **extended-*n* command** is a combination of characters that is *n* `x` followed by *n* characters that are not whitespace, the first of which is not `x`.

### Sanitization

Before a MCL program is executed, it is first sanitized from non-code by removing these things in order:

1. Block comments. A **block comment** is a construct that starts with `x[` and ends with `x]`. Blocks that encounter the start or end of file don't require a matching `x[` or `x]`.
2. Inline comments. An **inline comment** is a construct that starts with `x\` and ends at the end of line.
3. Whitespace. A **whitespace character** is either a space, tab or newline.

### Execution

Commands are executed left-to-right, excluding changes in the flow made by individual commands, and operate on five mediums: variables, a register, a stack, a queue and a tape.

#### Starting values

- The program starts with no variables specified.
- The program starts with the register set to the number 0.
- The program starts with an empty stack.
- The program starts with an empty queue.
- The program starts with the tape pointer pointing to the first cell. All tape cells start with the number 0.

#### NOP axiom

If a command could not be executed under current conditions (invalid command, not enough items to pop, etc), it is not executed, essentially becoming a NOP.

* * *

## Instructions

### Outside World Ops

Instructions that operate with the outside world.

- `i`: read an integer from input and push to the stack
- `I`: read a character from input and push that character's ordinal to the stack
- `o`: pop a from stack: write a to output
- `O`: pop a from stack: write character at ordinal a to output

### Medium Ops

Instructions that operate with mediums other than the stack.

#### Variable Ops

Instructions that operate with variables.

- `xv`: pop a from stack: push value of variable with ident a to stack
- `xV`: pop a, b from stack: set variable with ident a to b

#### Register Ops

Instructions that operate with the register.

- `r`: push value of register to stack
- `R`: pop a from stack: set register to a

#### Queue Ops

Instructions that operate with the queue.

- `q`: pop a from queue: push a to stack
- `Q`: pop a from stack: push a to queue

The operation of these two instructions should probably be shown graphically:

```
   Stack: 1 2 3 4 [5]...
   Queue: [6] 7 8 9 10...
Q: Stack: 1 2 3 [4]...
   Queue: [6] 7 8 9 10 5...
q: Stack: 1 2 3 4 [6]...
   Queue: [7] 8 9 10 5...
```

#### Tape Ops

Instructions that operate with the tape.

- `x>`: move the tape pointer to the next cell
- `x<`: move the tape pointer to the previous cell
- `xt`: push value of current tape cell to stack
- `xT`: pop a from stack: set the current tape cell to a

### Stack Ops

The majority of instructions operate with the stack.

#### Push/Pop

- `0` `1` `2` `3` `4` `5` `6` `7` `8` `9`: push the digit signified by the instruction
- `_`: pop a: discard

#### Increment/Decrement

- `u`: pop a: push a + 1
- `d`: pop a: push a - 1

#### Arithmetic

- `+`: pop a, b: push a + b
- `-`: pop a, b: push a - b
- `*`: pop a, b: push a * b
- `/`: pop a, b: push a / b (integer division)
- `m`: pop a, b: push a mod b
- `p`: pop a, b: push a<sup>b</sup>

#### Primitives

- `$`: pop a: push a twice (DUP)
- `%`: pop a, b: push b, a (SWAP)
- `@`: rotate stack upwards by 1 (ROLL)
- `^`: pop a, b: push a, b, a (PICK)

```
      [a b c d]
DUP:  [a b c d d]
SWAP: [a b d c]
ROLL: [d a b c]
PICK: [a b c d c]
```

### Flow Control

Instructions that control flow of the program.

- `?`: nested structure: peek at a from stack: nested structure runs only if a is nonzero
- `w`: nested structure: peek at a from stack: nested structure loops while a is nonzero
- `:`: close nested structure
- `xh`: terminate the program