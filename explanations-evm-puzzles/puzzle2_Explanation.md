### Puzzle 2

```assembly
############
# Puzzle 2 #
############

00      36        CALLDATASIZE
01      6000      PUSH1 00
03      6000      PUSH1 00
05      37        CALLDATACOPY
06      36        CALLDATASIZE
07      6000      PUSH1 00
09      6000      PUSH1 00
0B      F0        CREATE
0C      6000      PUSH1 00
0E      80        DUP1
0F      80        DUP1
10      80        DUP1
11      80        DUP1
12      94        SWAP5
13      5A        GAS
14      F1        CALL
15      3D        RETURNDATASIZE
16      600A      PUSH1 0A
18      14        EQ
19      601F      PUSH1 1F
1B      57        JUMPI
1C      FE        INVALID
1D      FE        INVALID
1E      FE        INVALID
1F      5B        JUMPDEST
20      00        STOP
```

- Target => Index 1F (31) where 'JUMPDEST' is located

Here's what the operations do:

- **CALLDATASIZE (0x36):** Provides the size of the `CALLDATA` in bytes. This is used to determine the length of the data being sent in the transaction.
- **PUSH1 (0x60):** Pushes 1 byte item onto the stack. It's followed by the data to push. For example, `PUSH1 00` pushes `0x00` onto the stack.
- **CALLDATACOPY (0x37):** Copies `CALLDATA` from the transaction into memory. It requires three parameters: the start of the memory output, start of the `CALLDATA`, and bytes to copy.
- **CREATE (0xF0):** Creates a new contract using the EVM code in memory as input. It requires three parameters: the value in Wei to send to the new contract, start of memory input, and size of input. Returns the address of the new contract.
- **DUP1 (0x80):** Duplicates the top item in the stack.
- **SWAP5 (0x94):** Swaps the 5th item from the stack with the top item.
- **GAS (0x5A):** Pushes the remaining gas available to the stack.
- **CALL (0xF1):** Executes a call to another contract. It requires seven parameters: gas to use, address to call, value to send, start of memory input, input size, start of memory output, output size. Returns 0 on failure, 1 on success.
- **RETURNDATASIZE (0x3D):** Pushes the size of the output data from the previous call to the stack.
- **EQ (0x14):** Checks if the top two stack items are equal. If they are, it pushes 1 onto the stack, otherwise it pushes 0.
- **PUSH1 (0x60):** As described above, it's used to push 1 byte item onto the stack.
- **JUMPI (0x57):** Conditional jump operation. It takes two parameters: the destination and the condition. If the condition is non-zero, it alters the program counter to the destination address.
- **INVALID (0xFE):** This is not an operation. The EVM throws an exception whenever it encounters an `INVALID` opcode.
- **JUMPDEST (0x5B):** Marks a valid destination for jumps.
- **STOP (0x00):** Stops execution, all remaining gas is refunded.

The condition for the jump operation (JUMPI) to be met is that RETURNDATASIZE must be equal to 0x0A. In other words, the size of the output data resulting from the previous CALL opcode should be exactly 10 bytes long

To solve the puzzle, we need to provide calldata that, when used as construction bytecode, will deploy a contract that will return exactly 10 bytes of data when called.

The first solution involves creating two distinct bytecodes: one for the construction phase and one for the runtime phase.

First, we build a runtime bytecode that returns 10 bytes:
00 600A PUSH1 0A
02 6000 PUSH1 00
04 F3 RETURN

This bytecode simply returns 10 zeros from memory.

Next, we build a construction bytecode that returns our runtime bytecode:
00 64600A6000F3 PUSH5 600A6000F3
06 6000 PUSH1 # Let's find the end of this bytecode
