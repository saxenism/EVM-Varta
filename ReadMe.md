# Yul Yoga (The best zero to hero Yul guide)

<p align="center">
  <img src="https://user-images.githubusercontent.com/32522659/169563005-e9751444-dedf-41ce-8763-16cadccfc7ba.png" />
</p>

## About Yul (picked from documentation itself)

1. Yul provides for loops, if and switch statements
2. No explicit statements for SWAP, DUP, JUMPDEST, JUMP and JUMPI are provided (as they obfuscate data flow and control flow)
3. Statements such as mul(add(x, y),z) are preferred over 7 y x add mul, because it becomes easier to see which opcode is being used for which operand
4. Yul is desigend for a stack based machine (EVM) but it does not expose the programmer to the complexity of the stack itself
5. Yul is statically typed, but also there is a default type (integer word of the target machine) that can be omitted.
6. Yul does not have any built-in operations, functions or types in its pure form.
  6.1 There exists only one specified dialect of Yul and that uses the EVM opcodes as builtin functions and defines only the type u256 (native 256-bit type of EVM)
