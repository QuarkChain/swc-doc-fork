## Motivation


The "L1 Header Hash History" feature allows direct access to recent L1 header hash on L2.

It is useful for two primary applications:
1. Random number generator.
2. Verification of L1 state on L2.

This feature is similar to the recent [EIP-2935](https://eips.ethereum.org/EIPS/eip-2935), which finally lands after 5 years.

## How It Works


We implemented it by extending the L1Block a bit [here](https://github.com/QuarkChain/optimism/blob/48d461b7ba7b3f1aa599f52b7b8a17d25e6d12d1/packages/contracts-bedrock/src/L2/L1Block.sol#L175-L198), which is used by OP Stack to store information about L1 block.

When L1 block information is saved, it is now also stored in one slot of the 8192-window buffer.

The slot for block `N` can be computed as:

```
N % HISTORY_SIZE
```


The stored block hash can be retrieved by calling the [`blockHash(uint256 _historyNumber)`](https://github.com/QuarkChain/optimism/blob/48d461b7ba7b3f1aa599f52b7b8a17d25e6d12d1/packages/contracts-bedrock/src/L2/L1Block.sol#L182) method.