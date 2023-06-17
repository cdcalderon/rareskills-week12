### Puzzle 9

```assembly
############
# Puzzle 9 #
############

00      34        CALLVALUE
01      6000      PUSH1 00
03      52        MSTORE
04      6020      PUSH1 20
06      6000      PUSH1 00
08      20        SHA3
09      60F8      PUSH1 F8
0B      1C        SHR
0C      60A8      PUSH1 A8
0E      14        EQ
0F      6016      PUSH1 16
11      57        JUMPI
12      FD        REVERT
13      FD        REVERT
14      FD        REVERT
15      FD        REVERT
16      5B        JUMPDEST
17      00        STOP
```

- Target => Index `16 (22)` where `JUMPDEST` is located

Important opcodes used in this puzzle:

```assembly
- **CALLVALUE**: This opcode pushes the amount of Ether sent with the transaction onto the stack.
- **PUSH1**: This opcode pushes the next 1 byte onto the stack.
- **MSTORE**: This opcode saves a word to memory.
- **SHA3**: This opcode computes the Keccak-256 hash of the input.
- **SHR**: This opcode shifts a value a number of bits to the right.
- **EQ**: This opcode checks if the top two elements on the stack are equal.
- **JUMPI**: This opcode conditionally alters program flow.
- **REVERT**: This opcode stops execution and reverts state changes.
- **STOP**: This opcode halts execution.
```

# The Puzzle's Flow

The puzzle starts by storing the Ether sent with the transaction `(the call value)` in memory. It then calculates the `Keccak-256` hash of the first `32` bytes of memory. The result is then shifted right `248` times, leaving only the first byte of the hash. The puzzle is solved if this byte is equal to 0xA8.

# The Solution

To solve the puzzle, we need to find a number to send that, when hashed, starts with `0xA8`. This is done by continuously increasing the number until the first byte of its hash is `0xA8`"

Solution Input:
`{"value":47,"data":"0x"}`
