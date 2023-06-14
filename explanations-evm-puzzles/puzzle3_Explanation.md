### Puzzle 3

```assembly
############
# Puzzle 3 #
############


00      36        CALLDATASIZE  # Get the size of the input data
01      6000      PUSH1 00      # Push 0 onto the stack
03      6000      PUSH1 00      # Push 0 onto the stack
05      37        CALLDATACOPY  # Copy input data to memory
06      36        CALLDATASIZE  # Get the size of the input data
07      6000      PUSH1 00      # Push 0 onto the stack
09      6000      PUSH1 00      # Push 0 onto the stack
0B      F0        CREATE        # Create a new contract with input data
0C      6000      PUSH1 00      # Push 0 onto the stack
0E      80        DUP1          # Duplicate the top stack item
0F      80        DUP1          # Duplicate the top stack item
10      80        DUP1          # Duplicate the top stack item
11      93        SWAP4         # Swap the top and 5th stack items
12      5A        GAS           # Get the amount of remaining gas
13      F4        DELEGATECALL  # Call a contract code in the context of the current contract
14      6005      PUSH1 05      # Push 5 onto the stack
16      54        SLOAD         # Load from storage slot 5
17      60AA      PUSH1 AA      # Push 170 (0xAA) onto the stack
19      14        EQ            # Check if the top two stack items are equal
1A      601E      PUSH1 1E      # Push 30 (0x1E) onto the stack
1C      57        JUMPI         # Jump to the address in stack item 0 if stack item 1 is not zero
1D      FE        INVALID       # Throw an exception
1E      5B        JUMPDEST      # Set a potential jump destination
1F      00        STOP          # Stop execution
```

- Target => Index 1E (30) where 'JUMPDEST' is located

# Solution

The key to this puzzle is to ensure that the `SLOAD` operation, which loads from the fifth storage slot `(0x05)`, returns `0xAA`. We accomplish this by deploying a contract with `0xAA` stored at the fifth storage slot.

The runtime bytecode for this new contract is:

```assembly
00      60AA      PUSH1 AA        # Push 170 (0xAA) onto the stack
02      6005      PUSH1 05        # Push 5 onto the stack
04      55        SSTORE          # Store 170 (0xAA) at storage slot 5
```

And the construction bytecode is:

```assembly
00      6460AA600555      PUSH5 60AA600555        # Push the runtime bytecode onto the stack
06      6000              PUSH1 00                # Push 0 onto the stack
08      52                MSTORE                  # Store the runtime bytecode at memory slot 0
00      600A              PUSH1 0A                # Push 10 (0x0A) onto the stack
02      601B              PUSH1 1B                # Push 27 (0x1B) onto the stack
04      F3                RETURN                  # Return
```

When we enter the calldata `0x6460AA600555600052600A601BF3`, we deploy this new contract. The runtime bytecode of this contract is then `delegate-called`, writing `0xAA` to the fifth storage slot of the puzzle's contract. The `SLOAD` operation checks this value, and since it's the expected `0xAA`, the `EQ` comparison succeeds, enabling us to jump to the `JUMPDEST` and successfully solve the puzzle.

Input solution:
`{"data":"0x6460AA600555600052600A601BF3","value":0}`
