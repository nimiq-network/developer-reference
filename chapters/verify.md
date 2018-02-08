# Verification
Or: What's a valid Nimiq blockchain?

## Blockchain Verification

### Chainproof

Of the [three client types](nodes-and-clients.md), a full client synchonizes the entire chain, while nano and light clients can skip major parts of chain due to a chain proof based on [NIPoPoW&mdash;Non-Interactive Proofs of Proof-of-Work](https://eprint.iacr.org/2017/963.pdf).
The chain verification is split in two parts. First, during the suffix part, the client will build a complete list of 120 blocks (cf. [constant K](constants.md#supply--emission-parameters)) starting from the header block. Nano clients will also drop the body part of each block to reduce load. From the last block of the suffix, the prefix will will start by using the interlink to connect to the genesis block via the most difficult blocks in the chain and this way skipping major parts of the remaining blockchain.

### Verify Block Order
Every block needs to be a valid successor of another block (besides the genesis block).

A block is a valid successor if it is the [immediate successor](#verify-immediate-successor) of the known previous block (and the interlink is valid, cf. [verify interlink](#verify-interlink).

#### Verify Immediate Successor
Each block needs to be an immediate successor of the previous block.

Rules comparing this block with the previous block:

* This height = previous height + 1
* This timestamp is >= previous time stamp
* This previous hash == hash of previous block
* Generating the next interlink on the previous block == interlink of this block.

#### Verify Interlink Successor

Rules to verify if this block has a valid predecessor in the interlink:

* This height > predecessor height
* This timestamp is >= previous time stamp
* Verify that the predecessor is contained in this block's interlink
* And if the predecessor's difficulty fits the position in this block's interlink
    * If the predecessor block is also the immediate predecessor:
        * Make sure that previous height == this height + 1
        * Generating the next interlink on the previous block == interlink of this block.
    * Otherwise, if not the immediate previous block make sure the previous block height is not exactly one less than this block's height
    * Otherwise
        * That the number of hashes that have been added to this block's interlink compared to the interlink in the previous block is at maximum the height difference
        * That the interlink is long enough to fit the previous block according to it's target
        * If both block share a block in the interlink, then all block below this shared block must be the same.

## Verify

### Verify Block

See [block](block.md) for all the fields of a block.

Rules
* The [header is valid](#verify-header)
* The block is not longer than 1MB (1e6 bytes)
* The interlink is valid
* And if it's a full block with body, also [verify the body](#verify-body)

### Verify Header

* Time stamp must not be more than 10 minutes into the future compared to the local clock
* Verify that proof of work suit's the current difficulty
* Hash of the body equals body hash

### Verify Interlink

* If this block is the genesis block (height = 1), then the interlink needs to be the hash of null, i.e. 32 bytes of `0`
* Verify that the hash of the interlink (in body) == interlink hash of the header

### Verfiy Body

* Verify each transaction
    * Is ordered correctly, cf. [transaction order](#transaction-order)
    * Is valid
* And each accountd
    * Is ordered
    * Is to be pruned

### Transaction Order

Transaction fields are compared by in following order:

1. Recipient
2. Validity start height: low first
3. Fee: higher first
4. Value: higher first
5. Sender
6. Recipient type
7. Sender type
8. Flags

As soon as one field is not the same, the block can be ordered accordingly. Unless the field is a paricular number, the order is detemind by the bytes in the field, lower first (e.g. addresses, types, flags).

### Verify Transaction

* Sender must not be recipient
* Both sender's and recipient's account types must be valid account types
* This transaction is a valid outgoing transaction for the sender's account type
* And a valid incoming transaction for the recipient's account type

### Valid Incoming and Outgoing Transactions

Basic accounts accept any kind of transaction.
Outgoing transactions must have a [valid signature](#verify-signature-proof) for all the fields of a [extended transaction](transactions.md#extended-transaction), without `type`, `proof length`, and `proof`.

### Verify Signature Proof

* Verify it's at most the length of public key + merkle path + signature
* The sender must be the signer
* That the signature is present
* That the signature matches the public key
