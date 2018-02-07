# Transactions

- All transactions MUST transfer value (value > 0).
- The value is always transferred from transaction _sender_ to transaction _recipient_.
- The hash of the transaction does not include the signature/proof.
- All transactions in Nimiq have a maximum validity window of 120 blocks, approximately two hours.
- Transactions in Nimiq do not use a nonce, reoccuring, identical transactions can be send only once the previous transaction has been invalidated or mined.

## Basis transaction
Size-Optimized format (138 bytes) for simple value transfer from basic to basic account.

| Element               | Data type    | Bytes | Description                                     |
|-----------------------|--------------|-------|-------------------------------------------------|
| Type                  | uint8        | 1     | `0`                                             |
| sender                | raw          | 32    | Public key of sender                            |
| recipient             | Address      | 20    | Recipient's address                             |
| recipient type        | uint8        | 1     | Account type of recipient                       |
| value                 | uint64       | 8     | in Satoshi                                      |
| fee                   | uint64       | 8     | Miner fee                                       |
| validity start height | uint32       | 4     | Delay by blocks, defaults to current height + 1 |
| signature             | raw          | 64    | By sender's private key                         |

To make a transaction validity shorter than the default of 120 blocks, set the start height to a value befor the current blockchain height. For example, current height - 60 will result in a remaining livetime of 60 blocks, approximately one hour.


## Extended transaction
Each extended transaction is at least 68 (3+|data|+65+|proof|) bytes long.

| Element               | Data type    | Bytes        | Description                                                                  |
|-----------------------|--------------|--------------|------------------------------------------------------------------------------|
| Type                  | uint8        | 1            | `1`                                                                          |
| data length           | uint16       | 2            |                                                                              |
| data                  | raw          | data length  | intended for recipient                                                       |
| sender                | Address      | 20           |                                                                              |
| sender type           | uint8        | 1            | Account type of sender                                                       |
| recipient             | Address      | 20           |                                                                              |
| recipient type        | uint8        | 1            | Account type of recipient                                                    |
| value                 | uint64       | 8            |                                                                              |
| fee                   | uint64       | 8            | Fee for miner to include tx in a block                                       |
| validity start height | uint32       | 4            | Delay by blocks, defaults to current height + 1                              |
| flags                 | uint8        | 1            | Unknown flags must be 0; Contract Creation (0x1)                             |
| proof length          | uint16       | 2            |                                                                              |
| proof                 | raw          | proof length | validity depends on sender account type, account state, current block height |

## Transaction hash
A hash of a transaction is created by using Blake2b on all the fields of a [extended transaction](#extended-transaction), without `type`, `proof length`, and `proof`.
