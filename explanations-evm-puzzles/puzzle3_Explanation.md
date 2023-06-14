### Puzzle 3

```assembly
############
# Puzzle 3 #
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
11      93        SWAP4
12      5A        GAS
13      F4        DELEGATECALL
14      6005      PUSH1 05
16      54        SLOAD
17      60AA      PUSH1 AA
19      14        EQ
1A      601E      PUSH1 1E
1C      57        JUMPI
1D      FE        INVALID
1E      5B        JUMPDEST
1F      00        STOP
```

- Target => Index 1E (30) where 'JUMPDEST' is located

# Solution

The key to this puzzle is to ensure that the `SLOAD` operation, which loads from the fifth storage slot `(0x05)`, returns `0xAA`. We accomplish this by deploying a contract with `0xAA` stored at the fifth storage slot.

The runtime bytecode for this new contract is:

```assembly
00      60AA      PUSH1 AA        [0xAA]
02      6005      PUSH1 05        [0x05, 0x0A] (key, value)
04      55        SSTORE          []
```

And the construction bytecode is:

```assembly
00      6460AA600555      PUSH5 60AA600555        [0x60AA600555]
06      6000              PUSH1 00                [0x0, 0x60AA600555] (offset, value)
08      52                MSTORE                  []
00      600A              PUSH1 0A                [0x0A]
02      601B              PUSH1 1B                [0x1B, 0x0A] (offset, size)
04      F3                RETURN                  []
```

When we enter the calldata `0x6460AA600555600052600A601BF3`, we deploy this new contract. The runtime bytecode of this contract is then `delegate-called`, writing `0xAA` to the fifth storage slot of the puzzle's contract. The `SLOAD` operation checks this value, and since it's the expected `0xAA`, the `EQ` comparison succeeds, enabling us to jump to the `JUMPDEST` and successfully solve the puzzle.

Input solution:
`{"data":"0x6460AA600555600052600A601BF3","value":0}`
