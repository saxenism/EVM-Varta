# EVM-Varta (Let's talk about the EVM)

Jet:  
> Yul is part of a greater family of EVM assembly languages
>
> its basically EVM assembly wrapped into a language with some Ssyntactic sugar
>
> What's great about that is that the only thing you need to write Yul is an understanding of the EVM. Looking at Yul contracts won't really get you to that point

So, let's get a bit of a deeper understanding of the EVM

## Start a Discussion

[Click Here](https://github.com/saxenism/YulYoga/discussions)


## Index

+ [What is an EVM](https://saxenism.github.io/EVM-Varta/#what-is-an-evm)
+ [The EVM Instruction Set](https://saxenism.github.io/EVM-Varta/#the-evm-instruction-set-bytecode-operations)
+ [Ethereum State](https://saxenism.github.io/EVM-Varta/#ethereum-state)
+ [Compiling Solidity to EVM Bytecode](https://saxenism.github.io/EVM-Varta/#compiling-solidity-to-evm-bytecode)
+ [Turing Completeness and Gas](https://saxenism.github.io/EVM-Varta/#turing-completeness-and-gas)
+ [Gas](https://saxenism.github.io/EVM-Varta/#gas)
+ [Gas Accounting During Execution](https://saxenism.github.io/EVM-Varta/#gas-accounting-during-execution)
+ [Gas Accounting Considerations](https://saxenism.github.io/EVM-Varta/#gas-accounting-considerations)
+ [Gas Cost vs Gas Price](https://saxenism.github.io/EVM-Varta/#gas-cost-vs-gas-price)
+ [Negative Gas Costs (Refunds)](https://saxenism.github.io/EVM-Varta/#negative-gas-costs-refund)
+ [Block Gas Limit](https://saxenism.github.io/EVM-Varta/#block-gas-limit)
+ [Decisions regarding block gas Limit](https://saxenism.github.io/EVM-Varta/#decisions-regarding-block-gas-limit)
+ [Femboy Capital](https://saxenism.github.io/EVM-Varta/#femboy-capital-definitely-not-as-detailed-as-the-ethereum-book)
+ [WIP](https://saxenism.github.io/EVM-Varta/#wip)

## What is an EVM

Consider Ethereum to be a global computer (with each node having its own permanent data store) and EVM is the processor. It basically handles smart contract deployment and execution. It is just a computation engine, and as such provides an abstraction of just computation and storage, similar to Java Virtual Machine(JVM).

The EVM executes its own bytecode intstruction set which higher level smart contract languages such as LLL, Serpent, Mutan or Solidity are compiled into. The EVM does not require to have any scheduling capability because Ethereum clients run through verified block transactions to determine which smart contract needs executing and in which order. This mechanism makes **EVM a single-threaded mechanism**.

1. A Turing machine is a finite (there are a limited number of states, such as a coin toss will have only two states: HEADS or TAILS) state machine that has an unlimited supply of paper tape that it can write on and read back.

2. The EVM is a quasi-Turing complete state machine. 

  * *Quasi* because all execution processes are limited to a finite number of computational steps by the amount of gas available for any given smart        contract execution.

3. This property: the requirement of a particular amount of gas to execute transactions on the EVM (absence of which leads to halting of execution) helps us do away with the **Halting Problem**

> **Note** What is the halting problem?
>
>  Halting means that the program on certain input will accept it and halt or reject it and halt, but it would never go into an infinite loop. So essentially halting == terminating.

4. The EVM has a stack-based architecture, storing all in-memory values on a stack. It works with a word size of 256 bits

5. EVM has a few addressable data components such as:
  - An immutable program code ROM (opcodes)
  - A volatile memory (memory variables probably includes calldata)
  - A permanent storage (keyword: storage)

## The EVM Instruction Set (Bytecode operations)

In addition to the typical bytecode operations (arithmetical, logical, memory access, flow control, logging etc), the EVM also has access to account information (address and balance) & block information (current gas price, block number etc)

1. Arithmetic Operations

```
ADD        //Add the top two stack items
MUL        //Multiply the top two stack items
SUB        //Subtract the top two stack items
DIV        //Integer division
SDIV       //Signed integer division
MOD        //Modulo (remainder) operation
SMOD       //Signed modulo operation
ADDMOD     //Addition modulo any number
MULMOD     //Multiplication modulo any number
EXP        //Exponential operation
SIGNEXTEND //Extend the length of a two's complement signed integer
SHA3       //Compute the Keccak-256 hash of a block of memory

```

> **Note** All arithmetic is performed modulo 2<sup>256</sup> (unless otherwise noted), and the also 0<sup>0</sup> is taken to be 1.


2. Stack Operations

```
POP     //Remove the top item from the stack
MLOAD   //Load a word from memory
MSTORE  //Save a word to memory
MSTORE8 //Save a byte to memory
SLOAD   //Load a word from storage
SSTORE  //Save a word to storage
MSIZE   //Get the size of the active memory in bytes
PUSHx   //Place x byte item on the stack, where x can be any integer from
        // 1 to 32 (full word) inclusive
DUPx    //Duplicate the x-th stack item, where x can be any integer from
        // 1 to 16 inclusive
SWAPx   //Exchange 1st and (x+1)-th stack items, where x can be any
        // integer from 1 to 16 inclusive

```

3. Process Flow Operations

```
STOP      //Halt execution
JUMP      //Set the program counter to any value
JUMPI     //Conditionally alter the program counter
PC        //Get the value of the program counter (prior to the increment corresponding to this instruction)
JUMPDEST  //Mark a valid destination for jumps
```

4. System Operations

```
LOGx          //Append a log record with x topics, where x is any integer
              //from 0 to 4 inclusive
CREATE        //Create a new account with associated code
CALL          //Message-call into another account, i.e. run another
              //account's code
CALLCODE      //Message-call into this account with another
              //account's code
RETURN        //Halt execution and return output data
DELEGATECALL  //Message-call into this account with an alternative
              //account's code, but persisting the current values for
              //sender and value
STATICCALL    //Static message-call into an account
REVERT        //Halt execution, reverting state changes but returning
              //data and remaining gas
INVALID       //The designated invalid instruction
SELFDESTRUCT  //Halt execution and register account for deletion
```

5. Logic Operations

```
LT     //Less-than comparison
GT     //Greater-than comparison
SLT    //Signed less-than comparison
SGT    //Signed greater-than comparison
EQ     //Equality comparison
ISZERO //Simple NOT operator
AND    //Bitwise AND operation
OR     //Bitwise OR operation
XOR    //Bitwise XOR operation
NOT    //Bitwise NOT operation
BYTE   //Retrieve a single byte from a full-width 256-bit word
```

6. Environmental Operations

```
GAS            //Get the amount of available gas (after the reduction for
               //this instruction)
ADDRESS        //Get the address of the currently executing account
BALANCE        //Get the account balance of any given account
ORIGIN         //Get the address of the EOA that initiated this EVM
               //execution
CALLER         //Get the address of the caller immediately responsible
               //for this execution
CALLVALUE      //Get the ether amount deposited by the caller responsible
               //for this execution
CALLDATALOAD   //Get the input data sent by the caller responsible for
               //this execution
CALLDATASIZE   //Get the size of the input data
CALLDATACOPY   //Copy the input data to memory
CODESIZE       //Get the size of code running in the current environment
CODECOPY       //Copy the code running in the current environment to
               //memory
GASPRICE       //Get the gas price specified by the originating
               //transaction
EXTCODESIZE    //Get the size of any account's code
EXTCODECOPY    //Copy any account's code to memory
RETURNDATASIZE //Get the size of the output data from the previous call
               //in the current environment
RETURNDATACOPY //Copy data output from the previous call to memory

```

7. Block Operations

```
BLOCKHASH  //Get the hash of one of the 256 most recently completed
           //blocks
COINBASE   //Get the block's beneficiary address for the block reward
TIMESTAMP  //Get the block's timestamp
NUMBER     //Get the block's number
DIFFICULTY //Get the block's difficulty
GASLIMIT   //Get the block's gas limit
```

## Ethereum State

The job of the EVM is to update the Ethereum state by computing valid state transactions as a result of smart contract code execution. Therefore, Ethereum can be considered a **transaction-based state machine** since external actors (ie account holders and miners) initiate state transitions by creating, accepting and ordering transactions.

At the top level, we have the Ethereum world state. The world state is a mapping of Ethereum addresses (160-bit values) to accounts. At the lower level, each Ethereum address represents an account comprising an Ether balance, a nonce (reps the number of txn successfully if EOA or the number of contracts created, if a contract), the account's storage, and account's code. An EOA will always have no code and an empty storage.

For a transaction resulting in a smart contract code execution, you can think of the EVM running on a sandboxed copy of the Ethereum world state, with this sandboxed version being discarded completely if execution cannot complete for whatever reason (Eg OOG exception). However if the execution does complete successfully, then the real-world state is updated to match the sandboxed version, including any changes to the called contract's storage data, any new contracts created, and any ether balance transfers that were initiated.

> **Note** Gas is deducted even in cases of failed execution, because as the code exection progresses, the gas gas supply is reduced according to the gas cost of the operations executed. If at any point the gas supply is reduced to zero we get an "Out of Gas" (OOG) exception; execution immediately halts and the transaction is abandoned. No changes to the Ethereum state are applied, except for the sender’s nonce being incremented and their ether balance going down to pay the block’s beneficiary for the resources used to execute the code upto the halting point.

A contract can call other contracts, with each call resulting in another EVM being instantiated around the new target of the call. Each instantiation has its sandbox world state initialized from the sandbox of the EVM at the level above.

## Compiling Solidity to EVM Bytecode

This section basically explains how the opcodes are arranged in stack and how they operate. Things like DUP1, PUSH1, JUMPI, EQ, LT, MLOAD etc are used to demonstrate how a program is executed in the EVM.

> **Note** There is an important but subtle difference between the code used when creating and deploying a new contract on the Ethereum platform and the code of the contract itself. In order to create a new contract, a special transaction is needed that has its to field set to the special 0x0 address and its data field set to the contract’s initiation code. When such a contract creation transaction is processed, the code for the new contract account is not the code in the data field of the transaction. Instead, an EVM is instantiated with the code in the data field of the transaction loaded into its program code ROM, and then the output of the execution of that deployment code is taken as the code for the new contract account. This is so that new contracts can be programmatically initialized using the Ethereum world state at the time of deployment, setting values in the contract’s storage and even sending ether or creating further new contracts.

To get examples on this, [the ethereum mastery book](https://github.com/ethereumbook/ethereumbook/blob/develop/13evm.asciidoc#contract-deployment-code) is good.

And an awesome resource to pick up how EVM opcode works is [this presentation from Ethereum Engineering Group](https://www.youtube.com/watch?v=RxL_1AfV7N4&ab_channel=EthereumEngineeringGroup)

PS: I made some notes from this YT talk. You can see them [here](https://github.com/saxenism/EVM-Varta/blob/master/Resources/YulYoga-EVMNotes.pdf)

## Turing Completeness and Gas

We have already discussed how the Ethereum Virtual Machine is a quasi-Turing-complete machine since it solves the halting problem.

One more interesting point to note is what happens when a (apparently rich af) attacker supposedly offers infinite gas and asks the EVM to do infinite computations? Well, if after a prespecified maximum amount of computation has been performed, the execution hasn’t ended, the execution of the program is halted by the EVM. That limit isn’t fixed in Ethereum—you can pay to increase it up to a maximum (called the "block gas limit"), and everyone can agree to increase that maximum over time. Nevertheless, at any one time, there is a limit in place, and transactions that consume too much gas while executing are halted.

## Gas

Gas is the cost to for on-chain computation and storage.

Each operation performed by a transaction or contract costs a fixed amount of gas. Example:
1. Addition costs 3 gas
2. Keccak-256 costs 30 gas + 6 gas for each 256 bits of data being hashed
3. Sending a transaction costs 21,000 gas

Gas serves two purposes:
1. Prevents DoS attacks, first by making it financially infeasible and then asking the tx.origin to set a limit to the gas they are willing to pay.
2. Providing reward to miners (as a hedge against volatile ETH prices)

## Gas Accounting During Execution

In the first instance, the EVM is provided with the gas supply equal to the amount specified by the gas limit, and all steps that can be performed with that amount of gas, are performed.

If all the steps were performed, any remaining gas is sent to the sender in form of Ether

```
refunded ether = remaining gas * gas price
```

And in either case, the miner gets paid (in ether) because the computations were done in both cases

```
miner fee = gas cost * gas price
```

## Gas Accounting Considerations

The objective is matching gas cost of transactions to the real-world cost of resources.

## Gas Cost vs Gas Price

- Gas cost is the number of units of gas required to perform a particular operation.

- Gas price is the amount of ether you are willing to pay per unit of gas when you send your transaction to the Ethereum network.

## Negative Gas Costs (Refund)

- Deleting a contract (SELFDESTRUCT) is worth a refund of 24,000 gas.

- Changing a storage address from a nonzero value to zero (SSTORE[x] = 0) is worth a refund of 15,000 gas.

## Block Gas Limit

The block gas limit is the maximum amount of gas that may be consumed by all the transactions in a block, and constrains how many transactions can fit into a block.

The block gas limit on the Ethereum mainnet is 8 million gas at the time of writing according to https://etherscan.io, meaning that around 380 basic transactions (each consuming 21,000 gas) could fit into a block.

## Decisions regarding block gas limit

The miners on the network collectively decide the block gas limit. Individuals who want to mine on the Ethereum network use a mining program, such as Ethminer, which connects to a Geth or Parity Ethereum client. The Ethereum protocol has a built-in mechanism where miners can vote on the gas limit so capacity can be increased or decreased in subsequent blocks. The miner of a block can vote to adjust the block gas limit by a factor of 1/1,024 (0.0976%) in either direction. The result of this is an adjustable block size based on the needs of the network at the time. This mechanism is coupled with a default mining strategy where miners vote on a gas limit that is at least 4.7 million gas, but which targets a value of 150% of the average of recent total gas usage per block (using a 1,024-block exponential moving average).

`
End of Ethereuem Book
`

## Femboy Capital (Definitely not as detailed as the Ethereum book)

> Will only include small piece of information from this source which add more clarity to already discussed topics or something that might have been missing above.

+ The EVM keeps track of the specific section of the bytecode it is currently executing with a pointer called the **Program Counter** (PC)
+ A VM execution loop looks something like this:
   + Fetch the instruction the PC points to
   + Execute the instruction
   + If the instruction jumps, set the PC to the new target
   + Otherwise, increment the PC
+ The stack can accomodate a maximum of 1024 elements and each element (word) being 256 bits long
+ This is a good image to visualize memory and storage inside of Ethereum

![Screenshot from 2022-05-28 15-00-08](https://user-images.githubusercontent.com/32522659/170819622-d6ed38fe-b9d5-4398-9b38-84e1dd82b209.png)

+ And this image is a good way to visualize how programs are executed in the EVM:
 
![Screenshot from 2022-05-28 15-04-09](https://user-images.githubusercontent.com/32522659/170819737-918ac257-50fe-44f7-9355-d1d430fca27b.png)

+ Storage in the EVM operates as a map of 32 byte keys to 32 byte values. It is persistent, meaning that the current storage state sticks around even after contract exectuion completes (STOP or RETURN is called). Any subsequent runs of a contract will have read and write access to the same storage space. There is no opportunity for data races because the EVM does not currently support concurrent execution of a single contract - all transactions are executed sequentially.

+ SSTORE cost a whopping 22100 gas! SLOAD cost 100. Reading and writing to storage is very expensive.
