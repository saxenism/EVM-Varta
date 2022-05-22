# Yul Yoga (The best zero to hero Yul guide)

<p align="center">
  <img src="https://user-images.githubusercontent.com/32522659/169563005-e9751444-dedf-41ce-8763-16cadccfc7ba.png" />
</p>

# EVM

Jet:  
> Yul is part of a greater family of EVM assembly languages
>
> its basically EVM assembly wrapped into a language with some Ssyntactic sugar
>
> What's great about that is that the only thing you need to write Yul is an understanding of the EVM. Looking at Yul contracts won't really get you to that point

So, let's get a bit of a deeper understanding of the EVM

## What is an EVM

Consider Ethereum to be a global computer (with each node having its own permanent data store) and EVM is the processor. It basically handles smart contract deployment and execution.

1. A Turing machine is a finite (there are a limited number of states, such as a coin toss will have only two states: HEADS or TAILS) state machine that has an unlimited supply of paper tape that it can write on and read back.

2. The EVM is a quasi-Turing complete state machine. 

  * *Quasi* because all execution processes are limited to a finite number of computational steps by the amount of gas available for any given smart        contract execution.

3. This property: the requirement of a particular amount of gas to execute transactions on the EVM (absence of which leads to halting of execution) helps us do away with the **Halting Problem**

> **Note** What is the halting problem?
>
>  Halting means that the program on certain input will accept it and halt or reject it and halt, but it would never go into an infinite loop. So essentially halting == terminating.


## About Yul (picked from documentation itself)

1. Yul provides for loops, if and switch statements
2. No explicit statements for SWAP, DUP, JUMPDEST, JUMP and JUMPI are provided (as they obfuscate data flow and control flow)
3. Statements such as mul(add(x, y),z) are preferred over 7 y x add mul, because it becomes easier to see which opcode is being used for which operand
4. Yul is desigend for a stack based machine (EVM) but it does not expose the programmer to the complexity of the stack itself
5. Yul is statically typed, but also there is a default type (integer word of the target machine) that can be omitted.
6. Yul does not have any built-in operations, functions or types in its pure form.
  6.1 There exists only one specified dialect of Yul (EVM dialect) and that uses the EVM opcodes as builtin functions and defines only the type u256 (native 256-bit type of EVM)
