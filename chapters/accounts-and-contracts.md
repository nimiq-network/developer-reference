# Accounts and Contracts

Nimiq supports three types of accounts and contracts: [basic](#basic-account), [vesting contract](#vesting-contract), and [hashed time-locked contract](#hashed-time-locked-contract-htlc).

All accounts and contracts share the following fields:

| Element | Data type    | Bytes | Description                                                                                     |
|---------|--------------|-------|-------------------------------------------------------------------------------------------------|
| type    | uint8        | 1     | 0..2: basic, [vesting](#vesting-contract), [HTLC](#hashed-time-locked-contract-htlc); immutable |
| balance | uint64       | 8     | In Luna; mutable                                                                             |

## Account
An account controlled by a (set of) private key(s).

### Basic Account
A simple account that can hold funds (balance) and is controlled by a private key.
The type field is set to `0`. No additional properties.
No restrictions on when the funds might be used. Most common type.
Basic account will automatically be pruned when made empty. It can be replaced by a contract account via a contract creation transaction.
All addresses are initially empty basic accounts.

## Contracts
Contract-specific properties are immutable. All incoming transactions will be rejected.
Cannot be replaced by other contract accounts.
Can only be explicitly pruned (replaced by an empty basic account) when empty. When pruning, contract type & contract-specific properties must be stored in the block body (for reverts).

### Contract Creation
* If receiving account in a transaction is not to `basic` but the current recipient account type is `basic` and the contract creation flag `0x01` is set, a contract of the type defined for the recipient will be created.
* The contract address is the transaction hash with the recipient address set to all zero (because recipient is not known at that time), truncated to the address size.
* The recipient defined in the transaction must be equal to the contract address.
* The created contract inherits any balance already present in the account, i.e. the funds of the contract will be added if a balance is already available.
* All properties of the transaction are available to the contract constructor, i.e. all values are stored inside, no outside dependencies.

### Vesting Contract
A vesting contract allows money to be spent in a scheduled way. An initial amount can be made available immediately followed by a scheduled release of the remaining amount.

A vesting contract can be initialized in three different ways, from full/explicit to minimal:

#### Full
Defining all possible fields:

| Element              | Data type    | Bytes | Description                                                                |
|----------------------|--------------|-------|----------------------------------------------------------------------------|
| type                 | uint8        | 1     | Set to `1`                                                                 |
| balance              | uint64       | 8     | Must be >= `vesting total amount`.                                         |
| vesting start        | uint32       | 4     | The block height to start with.                                            |
| vesting step blocks  | uint32       | 4     | Number of blocks until next `vesting step amount` should be made available |
| vesting step amount  | uint64       | 8     | The amount to be made available each step                                  |
| vesting total amount | uint64       | 8     | The total amount to be spent in all steps added up.                        |

If `balance` > `vesting total amount`, then the difference will be made available immediately.
The first time a vesting becomes available is at block height of `vesting start` + `vesting step blocks`.
If `vesting total amount` does not match an even number of steps, the last step will make the remaining amount available.
All other steps have the exact amount as defined in `vesting step amount`.

#### Normal

| Element              | Data type    | Bytes | Description                                                                |
|----------------------|--------------|-------|----------------------------------------------------------------------------|
| type                 | uint8        | 1     | Set to `1`                                                                 |
| balance              | uint64       | 8     | Must be >= `vesting total amount`.                                         |
| vesting start        | uint32       | 4     | The block height to start with.                                            |
| vesting step blocks  | uint32       | 4     | Number of blocks until next `vesting step amount` should be made available |
| vesting step amount  | uint64       | 8     | The amount to be made available each step                                     |

The field `vesting total amount` is again initialized with `balance`.

#### Minimal

| Element              | Data type    | Bytes | Description                                                                |
|----------------------|--------------|-------|----------------------------------------------------------------------------|
| type                 | uint8        | 1     | Set to `1`                                                                 |
| balance              | uint64       | 8     | Must be >= `vesting total amount`.                                         |
| vesting step blocks  | uint32       | 4     | Number of blocks until next `vesting step amount` should be made available |

All other fields are initialized automatically: `vesting start` = 0, `vesting step amount` = `vesting total amount` = `balance`

### Hashed Time-Locked Contract
Hashed Time-Locked Contracts (HTLCs) are used for conditional transfers, off-chain payment methods, and cross-chain [atomic swaps](https://en.wikipedia.org/wiki/Atomic_swap). [HTLC](https://en.bitcoin.it/wiki/Hashed_Timelock_Contracts) are time-limited. When settling the contract, a pre-image needs to be presented that fits the hash root to transfer the balance.

| Element        | Data type    | Bytes | Description                                    |
|----------------|--------------|-------|------------------------------------------------|
| type           | uint8        | 1     | Set to `2`                                     |
| balance        | uint64       | 8     | Must be >= `vesting total amount`.             |
| sender         | Address      | 20    | Normally, the sender will provide the amount   |
| recipient      | Address      | 20    | Signs the contract, usually the receiver of the amount |
| hash algorithm | uint8        | 1     | 1 or 3, Blake2b or SHA256; Argon2d not allowed |
| hash root      | Hash         | 32    | Hash of pre-image                              |
| hash count     | uint8        | 1     | How many times the proof has been hashed. Must be > 0.       |
| timeout        | uint32       | 4     | Contract invalid after this amount of blocks   |
| total amount   | uint64       | 8     |                                                |

If `balance` > `total amount`, then the difference will go back to the sender.

Recipient signs the contract, but the outgoing transaction can go to somebody else, as long as it's signed this way, reducing the amount of transactions necessary.
