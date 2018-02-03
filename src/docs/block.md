# Overview
A Nimiq block can exist in two modes: full and light. A light block is equal to a full block, but does not have a body.
A Nimiq block can be at most 1MB (1 million bytes) maximum and is composed of (body optional)

| Element           | Bytes   | Description
|-------------------|---------|--------
| Header            | 146     |
| Interlink         | <= 8193 |
| Full/Light Switch | 1       | 0 or 1.
| Body              | >= 117  | if switch is 1

# Header
The header has total size of 146 bytes and is composed of

| Element   | Data type    | Bytes | Description                                                       |
|-----------|--------------|-------|-------------------------------------------------------------------|
| version   | unsigned int | 2     | protocol version                                                  |
| previous  | Hash         | 32    | hash of previous block                                            |
| interlink | Hash         | 32    | cf. [Interlink](#interlink)                                       |
| body      | Hash         | 32    | cf. [Body](#body)                                                 |
| accounts  | Hash         | 32    | root hash of PM tree storing accounts state                       |
| nBits     | bits         | 4     | minimum difficulty for Proof-of-Work                              |
| height    | unsigned int | 4     | blockchain height when created                                    |
| timestamp | unsigned int | 4     | when the block was created                                        |
| nonce     | unsigned int | 4     | needs to fulfill Proof-of-Work difficulty required for this block |

At main net launch time, version will be "1" and hashes in the header will be based on Blake2b.

# Interlink
The interlink implements the [Non Interactive Proofs of Proof of Work (NiPoPow)](https://eprint.iacr.org/2017/963.pdf) so that only some of the most difficult blocks in the current blockchain are enough to make a proof.
// TODO exact explanation of how the interlink is being built up.

An interlink is composed of

| Element     | Data type    | Bytes         | Description                              |
|-------------|--------------|---------------|------------------------------------------|
| count       | unsigned int | 1             | Up to 255 blocks can be referred         |
| repeat bits | bit map      | ceil(count/8) | So duplicates can be skipped. See below. |
| hashes      | [Hash]       | <= count * 32 | Hashes of referred blocks                |

Repeat bits is a bit mask corresponding to the list of hashes,
a 1-bit indicating that the hash at that particular index is the same as the previous one,
i.e. repeated, and thus the hash will not be stored in the hash list again to reduce size.
As each hash is represented by one bit the size is ceil(count/8).

Hashes are a list of up to 255 block hashes of 32 bytes each.

Thus, an interlink can be up to 1+ceil(255/8)+255*32 = 8193 bytes.

# Body
The body part is 25 bytes plus data, transactions, and prunded accounts.
The maximum block size is 1MB (10^6 bytes).

| Element               | Data type                     | Size in bytes     | Description                                         |
|-----------------------|-------------------------------|-------------------|-----------------------------------------------------|
| miner address         | Address                       | 20                | recipient of the mining reward                      |
| extra data length     | unsigned int                  | 1                 |                                                     |
| extra data            | raw                           | extra data length | For future use                                      |
| transaction count     | unsigned int                  | 2                 |                                                     |
| transactions          | [Transaction]                 | ~150 each         |                                                     |
| pruned accounts count | unsigned int                  | 2                 |                                                     |
| pruned accounts       | [Pruned Account](accounts.md) | each >= 20+8      | Accounts with balence `0`. So they will be dropped. |

[Transactions](./transactions) can be basic or extended.
Basic uses 138 bytes, extended more than 68 bytes.
Transactions need to be in block order (first recipient, then validityStartHeight, fee, value, sender).
Prunded accounts by their address.
