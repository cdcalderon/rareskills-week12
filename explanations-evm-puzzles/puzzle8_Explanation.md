### Puzzle 8

```assembly
############
# Puzzle 8 #
############

00      34        CALLVALUE
01      15        ISZERO
02      19        NOT
03      6007      PUSH1 07
05      57        JUMPI
06      FD        REVERT
07      5B        JUMPDEST
08      36        CALLDATASIZE
09      6000      PUSH1 00
0B      6000      PUSH1 00
0D      37        CALLDATACOPY
0E      36        CALLDATASIZE
0F      6000      PUSH1 00
11      6000      PUSH1 00
13      F0        CREATE
14      47        SELFBALANCE
15      6000      PUSH1 00
17      6000      PUSH1 00
19      6000      PUSH1 00
1B      6000      PUSH1 00
1D      47        SELFBALANCE
1E      86        DUP7
1F      5A        GAS
20      F1        CALL
21      6001      PUSH1 01
23      14        EQ
24      6028      PUSH1 28
26      57        JUMPI
27      FD        REVERT
28      5B        JUMPDEST
29      47        SELFBALANCE
2A      14        EQ
2B      602F      PUSH1 2F
2D      57        JUMPI
2E      FD        REVERT
2F      5B        JUMPDEST
30      00        STOP
```

- Target => Index `2F (47)` where `JUMPDEST` is located

Important opcodes used in this puzzle:

- **CALLVALUE**: This opcode pushes the amount of Ether sent with the transaction onto the stack.
- **ISZERO**: This opcode checks if the top element on the stack is zero.
- **NOT**: This opcode inverts all the bits of the top element on the stack.
- **PUSH1**: This opcode pushes the next 1 byte onto the stack.
- **JUMPI**: This opcode conditionally alters program flow.
- **REVERT**: This opcode stops execution and reverts state changes.
- **CALLDATASIZE**: This opcode pushes the size of the calldata onto the stack.
- **CALLDATACOPY**: This opcode copies input data in the current environment to memory.
- **CREATE**: This opcode creates a new contract.
- **SELFBALANCE**: This opcode pushes the contract's own balance onto the stack.
- **DUP7**: This opcode duplicates the 7th element on the stack.
- **GAS**: This opcode pushes the amount of gas remaining onto the stack.
- **CALL**: This opcode calls a contract.
- **EQ**: This opcode checks if the top two elements on the stack are equal.
- **STOP**: This opcode halts execution.

# The Puzzle's Flow

The puzzle checks if the Ether sent `(call value)` is zero. If so, it flips this to the largest possible number using the `NOT` opcode. Regardless of the non-zero value sent, the puzzle will always jump to another part of the code due to this inversion.

# Contract Creation and Call

The puzzle copies the input data `(calldata)` to memory and uses it to create an empty contract. It then calls this contract, attempting to send its own balance, if any. The call succeeds because it's made to an address with no code.

# The Solution

Even with invalid input data, the puzzle creates a zero-address contract. Calls to this address succeed as it's empty. The puzzle is easier to solve than fail, as failure requires a call to a contract that reverts. Lastly, the puzzle checks if its balance remains the same after the call.

Solution Input:
`{"data":"0x00","value":0}`
