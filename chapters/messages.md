# Version

This message transmits the node's version of the protocol, peerAddress, the hash of its Genesis block, and the hash of the latest block in its blockchain (the head).

# Inventory

There are several sub-message categories related to inventory:

  * Get Data
  * Get Header
  * Not Found

# Get Blocks

There are three fields in this message:

  * Locators: The hash of the block from which to start sending blocks. This is an array, the first block that is found in the blockchain of the receiving node should be used.
  * Maximum Inventory Size: The number of blocks that are been asked
  * Direction: Defines if the blocks been asked are the ones after the locators or before the locators

# Block

This message is used to send one block at a time (in serialized form).

# Header

This message is used to send one header at a time (in serialized form).

# Transaction

This message is used to send one transaction at a time (in serialized form) but can also contain an accountsProof.

# Mempool

FIXME: What's the mempool message for?

# Reject

This message is used to signal to a peer that something they sent us was rejected and the reason why it was rejected.

# Subscribe

This message is used to subscribe to messages from the node that this message is sent to.

# Addresses

This message is used to relay the addresses we know to other peers.

# Get Addresses

Ask for the addresses that the peer knows that use the specified protocols and provide the specified services.

# Ping

This message is used to check if we can still communicate with a peer node.

# Pong

This is the response to a ping message.

# Signal

FIXME: What's this for? Something to do with WebRTC?

# Get ChainProof

This message is used to ask for the ChainProof from a peer node.

# ChainProof

This message is used to send a ChainProof to a peer node.

# Get AccountsProof

This message is used to ask for the AccountsProof for a given state of certain accounts (using the hash of the root of its Merkle Patricia Tree) from a peer node.

# AccountsProof

This message is used to send an AccountsProof to a peer node that requested it.

# Get AccountsTreeChunk

This message is used to ask for an AccountsTreeChunk for a given state of certain accounts (using the hash of the root of its Merkle Patricia Tree) from a peer node.

# AccountsTreeChunk

This message is used to send a AccountsTreeChunk to a peer node.

# Get TransactionsProof

This message is used to ask for the TransactionsProof from a peer node.

# TransactionsProof

This message is used to send a TransactionsProof to a peer node. FIXME: What's a TransactionsProof?

# FIXME: Describe other data structures and integrate documentation on the wire format of the messages
