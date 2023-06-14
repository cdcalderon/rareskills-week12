### Puzzle 4

```assembly
############
# Puzzle 4 #
############

00      30        ADDRESS            // Get the address of the current contract (puzzle contract)
01      31        BALANCE            // Get the balance of the current contract
02      36        CALLDATASIZE       // Get the size of the input data (calldata)
03      6000      PUSH1 00           // Push 00 onto the stack
05      6000      PUSH1 00           // Push 00 onto the stack again
07      37        CALLDATACOPY       // Copy calldata to memory
08      36        CALLDATASIZE       // Get the size of the input data (calldata)
09      6000      PUSH1 00           // Push 00 onto the stack
0B      30        ADDRESS            // Get the address of the current contract (puzzle contract)
0C      31        BALANCE            // Get the balance of the current contract
0D      F0        CREATE             // Create a new contract. Forward the entire balance to the newly created contract
0E      31        BALANCE            // Get the balance of the current contract (after the creation of the new contract)
0F      90        SWAP1              // Swap the first and second elements on the stack
10      04        DIV                // Integer division of the old balance by the new balance
11      6002      PUSH1 02           // Push 02 onto the stack
13      14        EQ                 // Check if the result of the division is equal to 2
14      6018      PUSH1 18           // Push 18 onto the stack (jump destination if EQ returns true)
16      57        JUMPI              // Jump to the address on the stack if EQ returned true
17      FD        REVERT             // Revert the execution if the jump didn't happen
18      5B        JUMPDEST           // Jump destination (if the division result was 2)
19      00        STOP               // Stop execution

```

- Target => Index 19 (25) where 'JUMPDEST' is located

In the puzzle, the `CREATE` opcode is used to create a new contract. This opcode forwards the entire balance of the puzzle contract to the newly created contract. After the new contract is created, the puzzle contract fetches its balance and the balance of the puzzle contract before the creation of the new contract. It then calculates the integer division of the old balance by the new balance.

The puzzle is designed such that this division result must be 2 for the subsequent jump to happen and the puzzle to be solved. Without additional actions, the result of this division would always be 1, because the new contract initially has the same balance as the old one.

# Solution

To solve the puzzle, the construction bytecode you send as calldata must burn half of its balance. For example, if you send 4 wei to the puzzle, your contract receiving them has to burn 2 wei so that the division `4//2 equals 2`.

The construction bytecode for this operation is `0x60008080806002815AF1600080F3`

```assembly
6000    PUSH1 00          // Push 0 onto the stack
8080    DUP1 DUP1         // Duplicate the top stack item twice
8060    PUSH1 60          // Push 60 onto the stack
02      ADD               // Add the top two stack items together
81      DUP2              // Duplicate the second stack item
5A      GAS               // Get the amount of remaining gas
F1      CALL              // Call a function, sending 2 wei to address 0x0
6000    PUSH1 00          // Push 0 onto the stack
80      DUP1              // Duplicate the top stack item
F3      RETURN            // Finish execution and return
```

This bytecode sends 2 wei to the 0x0 address in order to burn it and then returns nothing as runtime bytecode. When you send this calldata with a value of 4 wei, the puzzle is solved.

Solution Input:
`{"value":4,"data":"0x60008080806002815AF1600080F3"}`
