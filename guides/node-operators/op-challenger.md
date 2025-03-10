# Configure challenger for Super World Computer(SWC)

This guide provides a walkthrough of setting up the configuration and monitoring options for op-challenger. See the [OP-Challenger Explainer](https://docs.optimism.io/stack/fault-proofs/challenger) for a general overview of this fault proofs feature.

## Beta Testnet

### 1. Build the executable and prepare the prestate

```bash
git clone -b beta_testnet https://github.com/QuarkChain/optimism.git
cd optimism && make op-challenger && make op-program && make cannon && make reproducible-prestate
```

### 2. Configure challenger

```bash
PRIVATE_KEY=<Your private key or use something else like op-signer>
L1_RPC_URL=<HTTP provider URL for a standard L1 node, can be a full node>
L1_BEACON_URL=<HTTP provider URL for a L1 beacon node>
L2_RPC_URL=<HTTP provider URL for your L2 archive node, must be an archive node>
ROLLUP_RPC_URL=<HTTP provider URL for your L2 op-node>
DATADIR=<A directory that op-challenger can write to and store whatever data it needs>
DISPUTE_GAME_FACTORY_PROXY_ADDRESS=0x4b2215d682208b2a598cb04270f96562f5ab225f
```
> See [here](https://docs.optimism.io/operators/chain-operators/tools/op-challenger#configure-challenger) for a detailed explanation of the configurations.

### 3. Execute challenger

```bash
op-challenger/bin/op-challenger --l1-eth-rpc $L1_RPC_URL --l1-beacon $L1_BEACON_URL  --l2-eth-rpc $L2_RPC_URL --rollup-rpc $ROLLUP_RPC_URL  --datadir $DATADIR --cannon-server ./op-program/bin/op-program --cannon-bin ./cannon/bin/cannon --cannon-prestate $(realpath ./op-program/bin/prestate.bin.gz) --private-key $PRIVATE_KEY  --cannon-rollup-config $(realpath ./op-program/chainconfig/configs/3335-rollup.json)  --cannon-l2-genesis $(realpath ./op-program/chainconfig/configs/3335-genesis-l2.json)  --game-factory-address $DISPUTE_GAME_FACTORY_PROXY_ADDRESS --trace-type cannon,permissioned  --selective-claim-resolution
```
> **`--selective-claim-resolution`** (default: `false`): Only resolve claims for the configured claimants.  
>    - Enabled in the above command to save gas, as resolving the same claim fails for all but the first resolver.  Set `--additional-bond-claimants` to add other addresses to claim bonds for, in addition to the configured transaction sender.
>    - At least one honest challenger should disable this to resolve malicious claims, as bad actors might won’t resolve their own dishonest claims. 

### 4.Test and debug challenger (optional)
  This is an optional step to use `op-challenger` subcommands, which allow chain operators to interact with the Fault Proof System onchain for testing and debugging purposes. For example, it is possible to test and explore the system in the following ways:

  *   create games yourself, and it doesn't matter if the games are valid or invalid.
  *   perform moves in games and then claim and resolve things.

  Here's the list of op-challenger subcommands:

  | subcommand      | description                                              |
  | --------------- | -------------------------------------------------------- |
  | `list-games`    | List the games created by a dispute game factory         |
  | `list-claims`   | List the claims in a dispute game                        |
  | `list-credits`  | List the credits in a dispute game                       |
  | `create-game`   | Creates a dispute game via the factory                   |
  | `move`          | Creates and sends a move transaction to the dispute game |
  | `resolve`       | Resolves the specified dispute game if possible          |
  | `resolve-claim` | Resolves the specified claim if possible                 |