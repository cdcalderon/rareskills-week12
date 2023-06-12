# Original source: <https://github.com/daltyboy11/more-evm-puzzles>

The goal of those puzzles is to enter the right transaction / `(with the right data)` to get the execution not reverted.

### Puzzle 1 Solution

```assembly
############
# Puzzle 1 #
############

00      36      CALLDATASIZE
01      34      CALLVALUE
02      0A      EXP
03      56      JUMP
04      FE      INVALID
...
40      5B      JUMPDEST
41      58      PC
42      36      CALLDATASIZE
43      01      ADD
44      56      JUMP
45      FE      INVALID
...
47      5B      JUMPDEST
48      00      STOP
```

- Target => need to get to index 0x47 where 'JUMPDEST' is located

Here's what the operations do:

- **CALLDATASIZE (0x36):** Provides the size of the CALLDATA in bytes.
- **CALLVALUE (0x34):** Gets the value in Wei of the current transaction.
- **EXP (0x0A):** Exponential operation. It takes two parameters from the stack, with the first one being the base and the second being the exponent. It calculates base^exponent.
- **JUMP (0x56):** Alters the program counter to the address specified in the stack. Only jumps to JUMPDEST are allowed.
- **INVALID (0xFE):** This is not an operation. The EVM throws an exception whenever it encounters an INVALID opcode.
- **JUMPDEST (0x5B):** Marks a valid destination for jumps.
- **PC (0x58):** Gets the value of the program counter prior to the increment corresponding to this instruction.
- **ADD (0x01):** Addition operation. Pops two items from the stack and pushes their sum.
- **STOP (0x00):** Stops execution, all remaining gas is refunded.

The solution is to set CALLDATASIZE to 6 and CALLVALUE to 2. Here's why:

1. The first few lines of the puzzle represent a jump to the index equal to `CALLVALUE` raised to the power of `CALLDATASIZE`. We need this to result in 64 (0x40 in hexadecimal) to jump to the first JUMPDEST opcode. This can be achieved by setting CALLVALUE to 2 and CALLDATASIZE to 6, because 2 raised to the power of 6 is 64.

2. After this jump, the PC opcode pushes its own address (65 or 0x41 in hexadecimal) onto the stack. The ADD opcode then adds this to CALLDATASIZE, and the next JUMP opcode uses this sum to jump to the next index. We need this to result in 71 (0x47 in hexadecimal) to jump to the final JUMPDEST opcode.
   The ADD opcode then adds this address to the value of CALLDATASIZE, which we've set to 6. So, 65 (the PC opcode's address) plus 6 (the CALLDATASIZE) equals 71.

With these values, the EVM can successfully jump to both JUMPDEST opcodes and reach the STOP opcode, solving the puzzle.

{"value":2,"data":"0x010203040506"}
