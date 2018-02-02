# Overview
A Nimiq block can exist in two modes: full and light. A light block is equal to a full block, but does not have a body.
A Nimiq block can be at most 1MB (1 million bytes) maximum and is composed of (body optional)

| Element           | Size [bytes]            |
|-------------------|-------------------------|
| Header            | 146                     |
| Interlink         | <= 8193                 |
| Full/Light Switch | 1                       |
| Body              | >= 117 <if switch is 1> |

# Header
The header has total size of 146 bytes and is composed of

| Element   | Data type | Size [bytes] |
|-----------|-----------|--------------|
| version   | Uint16    | 2            |
| previous  | Hash      | 32           |
| interlink | Hash      | 32           |
| body      | Hash      | 32           |
| accounts  | Hash      | 32           |
| nBits     | Uint32    | 4            |
| height    | Uint32    | 4            |
| timestamp | Uint32    | 4            |
| nonce     | Uint32    | 4            |

At main net launch time, version will be "1" and hashes in the header will be based on Blake2b.

# Interlink
An interlink is composed of 

| Element     | Data type  | Size [bytes]  |
|-------------|------------|---------------|
| count       | Uint8      | 1             |
| repeat bits | Uint8Array | ceil(count/8) |
| hashes      | [Hash]     | <= count * 32 |

Repeat bits is a bit mask corresponding to the list of hashes, 
a 1-bit indicating that the hash at that particular index is the same as the previous one, 
i.e. repeated, and thus the hash will not be stored in the hash list again to reduce size. 
As each hash is represented by one bit the size is ceil(count/8).

Hashes are a list of up to 255 block hashes of 32 bytes each.

Thus, an interlink can be up to 1+ceil(255/8)+255*32 = 8193 bytes.

# Body
The body part is 117 bytes plus data and transactions. The maximum block size is 1MB.

| Element                | Data type       | Size [bytes]      |
|------------------------|-----------------|-------------------|
| miner address          | Address         | 20                |
| extra data length      | Uint8           | 1                 |
| extra data             | Uint8Array      | extra data length |
| transaction count      | Uint16          | 2                 |
| transactions           | [Transaction]   | ~150 each         |
| prunded accounts count | Uint16          | 2                 |
| prunded accounts       | Address+Account | 20+8+64 = 92      |


[Transactions](./transactions) can be basic or extended.
Basic uses 138 bytes, extended more than 68 bytes.
Transactions need to be in block order (first recipient, then validityStartHeight, fee, value, sender).
Prunded accounts by their address.
