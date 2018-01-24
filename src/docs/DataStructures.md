# Data Structures

There are several data structures in Nimiq, including the ones that are used to build the blockchain (block header, block interlink, block body, and transactions), the ones used to store the different account types, the ones used to store proofs, the messages that are sent between nodes that are part of the network, etc.

## Blockchain data structures

Nimiq's blockchain (as its name implies) is built by blocks that are ordered in a linear way where every block points to exactly one previous block (except for the Genesis block, which is the first block in the chain).

A single block is composed of a block header, a block interlink, and a block body.

### Block header

The block header is composed of the following fields:

  * The hash of the header of the previous block
  * The hash of this block's interlink
  * The hash of this block's body
  * The hash of the root node of the Patricia Merkle Tree that stores this block's account state
  * The mininum difficulty that most be solved by the Proof-of-Work algorithm for this block to be considered valid by the network
  * The height of the blockchain when this block was mined (also can be seen as the position of this block in the chain)
  * The timestamp that indicates when this block was created
  * The nonce that fullfills the Proof-of-Work difficulty required for this block to be considered valid by the network
  * The version of the data structure format used when creating this block

### Block Interlink

The Block Interlink is used to implement the [Non Interactive Proofs of Proof of Work (NiPoPow)](https://eprint.iacr.org/2017/963.pdf) on which Nimiq relies in order to synchronize nano and light clients faster by only downloading and validating some parts of the complete blockchain.

The fields in this data structure are:

  * An array with the hashes of all previous block headers that had an unusual high difficulty
  * The hash of the previous (FIXME: What does prevHash refers to in the Interlink?)
  * The repeatBits (FIXME: What's this used for?)
  * The compressed array of hashes (FIXME: What's this used for?)

### Block body

The block body is composed of these fields:

  * The address where the reward for mining the block should go to (usually the miner's address)
  * An array containing all the transactions that belong to the block
  * An extra Data field for future use

## Messages

### Version

This message transmits the node's version of the protocol, peerAddress, the hash of its Genesis block, and the hash of the latest block in its blockchain (the head).

### Inventory

There are several sub-message categories related to inventory:

  * Get Data
  * Get Header
  * Not Found

### Get Blocks

There are three fields in this message:

  * Locators: The hash of the block from which to start sending blocks. This is an array, the first block that is found in the blockchain of the receiving node should be used.
  * Maximum Inventory Size: The number of blocks that are been asked
  * Direction: Defines if the blocks been asked are the ones after the locators or before the locators

### Block

This message is used to send one block at a time (in serialized form).

### Header

This message is used to send one header at a time (in serialized form).

### Transaction

This message is used to send one transaction at a time (in serialized form) but can also contain an accountsProof.

### Mempool

FIXME: What's the mempool message for?

### Reject

This message is used to signal to a peer that something they sent us was rejected and the reason why it was rejected.

### Subscribe

This message is used to subscribe to messages from the node that this message is sent to.

### Addresses

This message is used to relay the addresses we know to other peers.

### Get Addresses

Ask for the addresses that the peer knows that use the specified protocols and provide the specified services.

### Ping

This message is used to check if we can still communicate with a peer node.

### Pong

This is the response to a ping message.

### Signal

FIXME: What's this for? Something to do with WebRTC?

### Get ChainProof

This message is used to ask for the ChainProof from a peer node.

### ChainProof

This message is used to send a ChainProof to a peer node.

### Get AccountsProof

This message is used to ask for the AccountsProof for a given state of certain accounts (using the hash of the root of its Merkle Patricia Tree) from a peer node.

### AccountsProof

This message is used to send an AccountsProof to a peer node that requested it.

### Get AccountsTreeChunk

This message is used to ask for an AccountsTreeChunk for a given state of certain accounts (using the hash of the root of its Merkle Patricia Tree) from a peer node.

### AccountsTreeChunk

This message is used to send a AccountsTreeChunk to a peer node.

### Get TransactionsProof

This message is used to ask for the TransactionsProof from a peer node.

### TransactionsProof

This message is used to send a TransactionsProof to a peer node. FIXME: What's a TransactionsProof?

## FIXME: Describe other data structures and integrate documentation on the wire format of the messages
