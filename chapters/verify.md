---
category: "Higher level concepts"
---

# Verification
Or: What's a valid Nimiq blockchain?

## Blockchain verification

Every block is either a valid successor of another block or the genesis block.

### Verify block order

* Is this block a valid successor
    * Either as the [immediate successor](#verify-immediate-successor) of the known previous block (And the interlink is valid, cf. (verify interlink)[#verify-interlink]); or
    * As the [interlink successor](#verify-interlink-successor) of the previous block

#### Verify immediate successor
Each block needs to be an immediate successor of the previous block.

Rules comparing this block with the previous block:

* This height = previous height + 1
* This timestamp is >= previous time stamp
* This previous hash == hash of previous block
* Generating the next interlink on the previous block == interlink of this block.

#### Verify interlink successor

Rules to verify if this block has a valid predecessor in the interlink:

* This height > predecessor height
* This timestamp is >= previous time stamp
* Verify that the previous block is contained in this block's interlink
    * and it's difficulty fits it's position in the interlink
    * and if the previous interlink block is also the previous block of this one
        * make sure that previous height == this height + 1
        * the interlink is valid
    * otherwise, if not the immediate previous block make sure the previous block height is not exactly one less than this block's height
    * then, check
        * that the number of hashes that have been added to this block's interlink compared to the interlink in the previous block is at maximum the height difference
        * That the interlink is long enough to fit the previous block according to it's target // TODO validate
        * // TODO common block

## Verify

### Verify Block

See [block](block.md) for all the fields of a block.

Rules
* The [header is valid](#verify-header)
* The block is not longer than 1MB (1e6 bytes)
* The interlink is valid
* And if it's a full block with body, also [verify the body](#verify-body)

### Verify header

* Time stamp must not be more than 10 minutes into the future compared to the local clock
* Verify that proof of work suit's the current difficulty
* Hash of the body equals body hash

### Verify interlink

* if this block is the genesis block (height = 1), then the interlink needs to be the hash of null, i.e. 32 bytes of `0`
* verify that the hash of the interlink (in body) == interlink hash of the header

### Verfiy body

* verify each transaction
    * is ordered correctly, cf. [transaction order](#transaction-order)
    * is valid
* and each accountd
    * is ordered
    * is to be pruned

### Transaction order

Transaction fields are compared by in following order

1. recipient
2. validity start height: low first
3. fee: higher first
4. value: higher first
5. sender
6. recipient type
7. sender type
8. flags

As soon as one field is not the same, the block can be ordered accordingly. Unless the field is a paricular number, the order is simply detemind by the bytes in the field, lower first (e.g. addresses, types, flags).

### Verify transaction

* Sender must not be recipient
* Both sender's and recipient's account types must be valid account types
* This transaction is a valid outgoing transaction for the sender's account type
* And a valid incoming transaction for the recipient's account type

### Valid incoming and outgoing transactions

Basic accounts accept any kind of transaction.
Outgoing transactions must have a [valid signature](#verify-signature-proof) for all the fields of a [extended transaction](transactions.md#extended-transaction), without `type`, `proof length`, and `proof`.

### Verify signature proof

* verify it's at most the length of public key + merkle path + signature
* the sender must be the signer
* that the signature is present
* that the signature matches the public key
