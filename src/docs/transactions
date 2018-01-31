# Transactions

## Basis Transaction
Each basic transaction is exactly 138 bytes.

Element      | Type  | sender pub key | recipient | recipient type | value  | fee    | validity start height | signature 
------------ | ----- | -------------- | --------- | -------------- | ------ | ------ | --------------------- | ----------
Data type    | Uint8 | Uint8Array     | Address   | Uint8          | Uint64 | Uint64 | Uint32                | Uint8Array
Size [bytes] | 1     | 32             | 20        | 1              | 8      | 8      | 4                     | 64

## Extended Transaction
Each extended transaction is at least 68 (3+|data|+65+|proof|) bytes long.

Element      | Type  | data length | data        | sender  | sender type | recipient | recipient type | value  | fee    | validity start height | flags | proof length | proof 
------------ | ----- | ----------- | ----------- | ------- | ----------- | --------- | -------------- | ------ | ------ | --------------------- | ----- | ------------ | --------
Data type    | Uint8 | Uint16      | Uint8Array  | Address | Uint8       | Address   | Uint8          | Uint64 | Uint64 | Uint32                | Uint8 | Uint16       | Uint8Array 
Size [bytes] | 1     | 2           | data length | 20      | 1           | 20        | 1              | 8      | 8      | 4                     | 1     | 2            | proof length
