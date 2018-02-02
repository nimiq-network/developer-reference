# Account
Niminq supports three types of accounts: [Basic](#basic-account), [Vesting Contract](#vesting-contract), and [Hashed Time-Locked Contract](#hashed-time-locked-contract-htlc)

Among account share the following fields:

| Element | Data type    | Bytes | Description                                                                          |
|---------|--------------|-------|--------------------------------------------------------------------------------------|
| type    | unsigned int | 1     | 0..2: basic, [vesting](#vesting-contract), [HTLC](#hashed-time-locked-contract-htlc) |
| balance | unsigned int | 8     | in Satoshi                                                                           |

# Basic account
An account with balance, the type field is set to `0`. No restrictions on when the funds might be used. Most common type.

# Pruned account
A prunded account is composed of an account of any type with an address:

| Element | Data type | Bytes | Description                                       |
|---------|-----------|-------|---------------------------------------------------|
| address | Address   | 20    | Address of account                                |
| account | Account   | >9    | Can be a basic account, vesting contract, or HTLC |

This type is used in the body of a block to communicate the accounts to be pruned with this block.

# Vesting contract
A vesting contract allows to spend money in scheduled way.

An initial amount (`balance` - `vesting total amount`) can be spend immediately followed by a scheduled distribution of the remaining amount the set total is used up.

| Element              | Data type    | Bytes | Description                                                                |
|----------------------|--------------|-------|----------------------------------------------------------------------------|
| type                 | unsigned int | 1     | Set to `1`                                                                 |
| balance              | unsigned int | 8     | Must be >= `vesting total amount`.                                         |
| vesting start        | unsigned int | 4     | The block height to start with.                                            |
| vesting step blocks  | unsigned int | 4     | Number of blocks until next `vesting step amount` should be made available |
| vesting step amount  | unsigned int | 8     | The amount to made available each step                                     |
| vesting total amount | unsigned int | 8     | The total amount to be spend in all steps added up.                        |

`Vesting total amount` defaults to balance if omitted.
The first time a vesting becomes available is at block height of `vesting start` + `vesting step blocks`.
If `vesting total amount` does not match a even number of steps, the last step will make the remaining amount available. All other steps have the exact amount defined in `vesting step amount`. >
If `balance` > `vesting total amount`, then the difference will be made available immediately.

# Hashed time-locked contract HTLC
Used for conditional transfers, off-chain payment methods, and cross-chain [atomic swaps](https://en.wikipedia.org/wiki/Atomic_swap). [HTLC](https://en.bitcoin.it/wiki/Hashed_Timelock_Contracts) are time limited. When setting the hash, a proof needs to be presented that fits the hash to transfer the balance.

| Element        | Data type    | Bytes | Description                                    |
|----------------|--------------|-------|------------------------------------------------|
| type           | unsigned int | 1     | Set to `2`                                     |
| balance        | unsigned int | 8     |                                                |
| sender         | Address      | 20    |                                                |
| recipient      | Address      | 20    |                                                |
| hash algorithm | Uint8        | 1     | 1 or 3, Blake2b or SHA256; Argon2d not allowed |
| hash root      | Hash         | 32    |                                                |
| hash count     | unsigned int | 1     |                                                |
| timeout        | unsigned int | 4     | Contract invalid after this amount of blocks   |
| total amount   | unsigned int | 8     |                                                |
