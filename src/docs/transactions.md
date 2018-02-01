# Transactions

## Basis Transaction
Each basic transaction is exactly 138 bytes.

| Element               | Data type  | Size [bytes] |
|-----------------------|------------|--------------|
| Type                  | Uint8      | 1            |
| sender pub key        | Uint8Array | 32           |
| recipient             | Address    | 20           |
| recipient type        | Uint8      | 1            |
| value                 | Uint64     | 8            |
| fee                   | Uint64     | 8            |
| validity start height | Uint32     | 4            |
| signature             | Uint8Array | 64           |


## Extended Transaction
Each extended transaction is at least 68 (3+|data|+65+|proof|) bytes long.

| Element               | Data type  | Size [bytes] |
|-----------------------|------------|--------------|
| Type                  | Uint8      | 1            |
| data length           | Uint16     | 2            |
| data                  | Uint8Array | data length  |
| sender                | Address    | 20           |
| sender type           | Uint8      | 1            |
| recipient             | Address    | 20           |
| recipient type        | Uint8      | 1            |
| value                 | Uint64     | 8            |
| fee                   | Uint64     | 8            |
| validity start height | Uint32     | 4            |
| flags                 | Uint8      | 1            |
| proof length          | Uint16     | 2            |
| proof                 | Uint8Array | proof length |
