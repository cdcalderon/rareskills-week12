### Puzzle 10

```assembly
#############
# Puzzle 10 #
#############

00      6020                                                                    PUSH1 20
02      6000                                                                    PUSH1 00
04      6000                                                                    PUSH1 00
06      37                                                                      CALLDATACOPY
07      6000                                                                    PUSH1 00
09      51                                                                      MLOAD
0A      7FF0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0      PUSH32 F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0
2B      16                                                                      AND
2C      6020                                                                    PUSH1 20
2E      6020                                                                    PUSH1 20
30      6000                                                                    PUSH1 00
32      37                                                                      CALLDATACOPY
33      6000                                                                    PUSH1 00
35      51                                                                      MLOAD
36      17                                                                      OR
37      7FABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABAB      PUSH32 ABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABAB
58      14                                                                      EQ
59      605D                                                                    PUSH1 5D
5B      57                                                                      JUMPI
5C      FD                                                                      REVERT
5D      5B                                                                      JUMPDEST
5E      00                                                                      STOP
```

- Identify the target => need to get to index 5D (93) where 'JUMPDEST' is located

Important opcodes used in this puzzle:

- **PUSH1**: This opcode pushes the next 1 byte onto the stack.
- **CALLDATACOPY**: This opcode copies input data in the current environment to memory.
- **MLOAD**: This opcode loads a word from memory.
- **AND**: This opcode performs a bitwise AND operation on the top two elements on the stack.
- **OR**: This opcode performs a bitwise OR operation on the top two elements on the stack.
- **EQ**: This opcode checks if the top two elements on the stack are equal.
- **JUMPI**: This opcode conditionally alters program flow.
- **REVERT**: This opcode stops execution and reverts state changes.
- **STOP**: This opcode halts execution.

# The Puzzle's Flow

The puzzle starts by copying the first `32` bytes from calldata to the first memory slot. It then performs a bitwise `AND` operation with a mask of `0xF0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0`. This operation masks out the lower bits of each byte. The next 32 bytes from calldata are then copied to memory, overwriting the first ones. This word of bytes is loaded onto the stack and then `ORed` with the result of the `AND` operation. The puzzle is solved if the result of these operations is equal to `0xABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABAB`.

# The Solution

To solve the puzzle, we need to send calldata such that the result of the operations matches the required value. If we send zeros for the first word, the result of the `AND` operation will be a zero-word too. That way we can discard the first word completely and just send the end result as the second word. Therefore, the calldata `0x0000000000000000000000000000000000000000000000000000000000000000ABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABAB` is a solution to the puzzle.

Solution Input:
`{"data":"0xAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B","value":0}`
