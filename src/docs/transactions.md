# Transactions
All transactions MUST transfer value (value > 0). The value is always transferred from transaction sender to transaction recipient.
The hash of the transaction does not include the signature/proof.

## Basis Transaction
Size-Optimized format (138 bytes) for simple value transfer from basic to basic account.

| Element               | Data type    | Bytes | Description               |
|-----------------------|--------------|-------|---------------------------|
| Type                  | unsigned int | 1     | `0`                       |
| sender                | raw          | 32    | Public key of sender      |
| recipient             | Address      | 20    | Recipient's address       |
| recipient type        | unsigned int | 1     | Account type of recipient |
| value                 | unsigned int | 8     | in Satoshi                |
| fee                   | unsigned int | 8     | Miner fee                 |
| validity start height | unsigned int | 4     | Delay by blocks           |
| signature             | raw          | 64    | By sender's private key   |


## Extended Transaction
Each extended transaction is at least 68 (3+|data|+65+|proof|) bytes long.

| Element               | Data type    | Bytes        | Description                                                                  |
|-----------------------|--------------|--------------|------------------------------------------------------------------------------|
| Type                  | unsigned int | 1            | `1`                                                                          |
| data length           | unsigned int | 2            |                                                                              |
| data                  | raw          | data length  | intended for recipient                                                       |
| sender                | Address      | 20           |                                                                              |
| sender type           | unsigned int | 1            | Account type of sender                                                       |
| recipient             | Address      | 20           |                                                                              |
| recipient type        | unsigned int | 1            | Account type of recipient                                                    |
| value                 | unsigned int | 8            |                                                                              |
| fee                   | unsigned int | 8            | Fee for miner to include tx                                                  |
| validity start height | unsigned int | 4            | Delay by blocks                                                              |
| flags                 | unsigned int | 1            | Unknown flags must be 0; Contract Creation (0x1)                             |
| proof length          | unsigned int | 2            |                                                                              |
| proof                 | raw          | proof length | validity depends on sender account type, account state, current block height |
