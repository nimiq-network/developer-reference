# Blockchain

## Block
A Nimiq block can exist in two modes: full and light. A light block is equal to a full block, but does not have a body.
A Nimiq block can be at most 100kB (100 thousand bytes) maximum and is composed of (body optional):

| Element           | Bytes   | Description
|-------------------|---------|--------
| Header            | 146     |
| Interlink         | <= 8193 |
| Full/Light Switch | 1       | `0` or `1`
| Body              | >= 117  | If switch is `1`


## Header
The header has total size of 146 bytes and is composed of:

| Element       | Data type | Bytes | Description                                                       |
|---------------|-----------|-------|-------------------------------------------------------------------|
| version       | uint16    | 2     | Protocol version                                                  |
| previous hash | Hash      | 32    | Hash of previous block                                            |
| interlink     | Hash      | 32    | Cf. [interlink](#interlink)                                       |
| body hash     | Hash      | 32    | Cf. [body hash](#body-hash) and [body](#body)                     |
| accounts hash | Hash      | 32    | Root hash of PM tree storing accounts state, cf. [account tree](accounts-tree.md) |
| nBits         | bits      | 4     | Minimum difficulty for Proof-of-Work                              |
| height        | uint32    | 4     | Blockchain height when created                                    |
| timestamp     | uint32    | 4     | When the block was created                                        |
| nonce         | uint32    | 4     | Needs to fulfill Proof-of-Work difficulty required for this block |

At main net launch time, version will be "1" and hashes in the header will be based on Blake2b.

### Previous Hash

The expressions "block hash" and "block header hash" refer to the same hash.
The hash is used to refer to the previous block in the blockchain.
It's created using [Blake2b](#hash) on the serialized block header of the previous block as pre-image.


## Interlink
The interlink implements the [Non Interactive Proofs of Proof of Work (NiPoPow)](https://eprint.iacr.org/2017/963.pdf) and contains links to previous blocks.

An interlink is composed of:

| Element     | Data type    | Bytes         | Description                              |
|-------------|--------------|---------------|------------------------------------------|
| count       | uint8        | 1             | Up to 255 blocks can be referred         |
| repeat bits | bit map      | ceil(count/8) | So duplicates can be skipped. See below. |
| hashes      | [Hash]       | <= count * 32 | Hashes of referred blocks                |

Repeat bits is a bit mask corresponding to the list of hashes,
a 1-bit indicating that the hash at that particular index is the same as the previous one,
i.e. repeated, and thus the hash will not be stored in the hash list again to reduce size.
As each hash is represented by one bit the size is ceil(count/8).

`hashes` are a list of up to 255 block hashes of 32 bytes each.

Thus, an interlink can be up to 1+ceil(255/8)+255*32 = 8193 bytes.

### Interlink Construction
The concept of [Non-Interactive Proofs of Proof-of-Work](https://eprint.iacr.org/2017/963.pdf) are used to create the interlink.

An interlink contains hashes to previous blocks with a higher-than-target difficulty. The position of a hash in the interlink correlates to how much higher the difficulty was compared to the target difficulty.

An interlink is created based on the interlink of the previous block plus putting the current hash into place.

1. The block's hash will be placed into the beginning of the new interlink as many times as it is more difficult than the required difficulty.
2. The remaining places of the new interlink will be filled by the hashes of the previous interlink, keeping the position.

Finally, the interlink is compressed by counting and skipping repeating hashes.

## Body
The body part is 25 bytes plus data, transactions, and pruned accounts:

| Element               | Data type                     | Bytes             | Description                                         |
|-----------------------|-------------------------------|-------------------|-----------------------------------------------------|
| miner address         | Address                       | 20                | Recipient of the mining reward                      |
| extra data length     | uint8                         | 1                 |                                                     |
| extra data            | raw                           | extra data length | For future use                                      |
| transaction count     | uint16                        | 2                 |                                                     |
| transactions          | [Transaction]                 | ~150 each         | Transactions mined in this block                    |
| pruned accounts count | uint16                        | 2                 |                                                     |
| pruned accounts       | [Pruned Account](accounts.md) | each >= 20+8      | Accounts with balance `0`; so they will be dropped  |

[Transactions](./transactions) can be basic or extended.
Basic uses 138 bytes, extended more than 68 bytes.
Transactions need to be in block order (first recipient, then validityStartHeight, fee, value, sender).
Pruned accounts by their address.

### Body Hash
The body hash is generated by calculating the root of a [Merkle Tree](https://en.wikipedia.org/wiki/Merkle_tree) with following leaves: `miner address`, `extra data`, each `Transaction`, and each `Pruned Account` (in this order).

### Pruned Account
A pruned account is composed of an account of any type with an address:

| Element | Data type | Bytes | Description                                       |
|---------|-----------|-------|---------------------------------------------------|
| address | Address   | 20    | Address of account                                |
| account | Account   | >9    | Can be a basic account, vesting contract, or HTLC |

This type is used in the body of a block to communicate the accounts to be pruned with this block.


