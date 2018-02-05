---
category: "Data schemas"
---

# Message

Message instance. This is usually not called directly but by subclasses.

| Element   | Data type    | Bytes            | Description                |
|-----------|--------------|------------------|----------------------------|
| magic     | Uint4 | 4	 		      | [Magic String](./constants.md#message-magic)|
| type      | Uint4 | 4	 		      | [Message Type](./constants.md#message-types)|
| lenght    | Uint4 | 4	 		      | lenght of the message. |
| checksum  | Uint4 | 4	 		      | CRC32 checksum. |

# Version Message

This message transmits the node's version of the protocol, peer ddress, the hash of its Genesis block, and the hash of the latest block in its blockchain (the head).

| Element     | Data type    | Bytes            | Description            |
|-------------|--------------|------------------|------------------------|
| version     | Uint4 | 4	 		    | version of the protocol|
| peer address | Uint4 | 4	 		    | [Ws Peer Address](#wspeeraddress) or [Rtc Peer Address](#rtcpeeraddress)|

## Ws Peer Address

| Element   | Data type      | Bytes | Description            |
|-----------|----------------|-------|------------------------|
| protocol  | Uint1 | 1     | [Protocol type](./constants.md#protocol-types)      |
| services  | Uint4 | 4     | [Service type](./constants.md#services-types)      |
| timestamp | Uint8 | 8     | Used by other nodes to calculate offset.   |
| host      | string         | (?)   | Hostname.               |
| port      | number         | 2     | Port number for Web Socket Connector to listen to.	|

## Rtc Peer Address

| Element   | Data type      | Bytes | Description            |
|-----------|----------------|-------|------------------------|
| protocol  | Uint1   | 1     | [Protocol type](./constants.md#protocol-types)      |
| services  | Uint4   | 4     | [Service type](./constants.md#services-types)      |
| timestamp | Uint8   | 8     | Used by other nodes to calculate offset.   |
| signalId  | Uint2	 | 2     | Signaling Id (Add a link to a Signaling Section)     |
| distance  | number		 | 1     | 0: self, 1: direct connection, 2: 1 hop |

# Inventory Message

The inventory message points a vector to requested data. The structure for `BaseInventoryMessage` is the following:

| Element   | Data type      | Bytes | Description            |
|-----------|----------------|-------|------------------------|
| type  | Uint4   | 4     | [Protocol type](./constants.md#protocol-types)      |
| hash  | Uint32   | 32     | Hash of the message  |

There are several sub-message categories related to inventory message:

  * Get Data
  * Get Header
  * Not Found

# Get Blocks Message


| Element   | Data type      | Bytes | Description            |
|-----------|----------------|-------|------------------------|
| locators  | Uint2   | 2     | The hash of the block from which to start sending blocks. This is an array, the first block that is found in the blockchain of the receiving node should be used. |
| max inventory size | Uint1   | 2     | The number of blocks that are been asked.  |
| direction | Uint1   | 1     | Defines if the blocks been asked are the ones after the locators or before the locators.  |

# Block Message

This message is used to send one block at a time (in serialized form).

# Header Message

This message is used to send one header at a time (in serialized form).

# Transaction Message

This message is used to send one transaction at a time (in serialized form) but can also contain an accountsProof.

# Mempool Message

FIXME: What's the mempool message for?

# Reject Message

This message is used to signal to a peer that something they sent us was rejected and the reason why it was rejected.

| Element     | Data type      | Bytes | Description            |
|-------------|----------------|-------|------------------------|
| message type | Uint1   | 1     | [message type](/constants.md#message-types)  |
| code        | Uint1   | 1     | [Reject Message Code](./constants.md#reject-message-code)  |
| reason      | Uint   | >255  | FIXME: What is reason for?  |
| byte length  | Uint1   | 1     |   |
| extra data   | Uint2   | 2     |   |

# Subscribe Message

This message is used to subscribe to messages from the node that this message is sent to.

# Addresses Message

This message is used to relay the addresses we know to other peers.

# Get Addresses Message

Ask for the addresses that the peer knows that use the specified protocols and provide the specified services.

| Element     | Data type      | Bytes | Description       			        |
|-------------|----------------|-------|------------------------------------|
| protocol mask| Uint1   | 1     | 0:DUMB, 1:WS, 2:RTC  			    |
| service mask | Uint4   | 4     | 0: NONE, 1:LIGHT, 2:LIGHT, 4:FULL  |

# Ping Message

This message is used to check if we can still communicate with a peer node.

# Pong Message

This is the response to a ping message.

# Signal Message

| Element        | Data type      | Bytes | Description       			        |
|----------------|----------------|-------|------------------------------------|
| sender id       | SignalId 	  | 16    | 									|
| recipient id    | SignalId 	  | 16    | 						   		    |
| nonce		     | Uint4   | 4     | 								    |
| ttl	     	 | Uint1   | 1     | 								    |
| flags		     | Uint1   | 1     | 								    |
| payload byte lenght | Uint2   | 2     | 								    |
| payload        | Uint   | FIXME | 								    |
| sender public key   | Uint32   | 32    |Only present if payload Bytelenght > 0.  |
| signature      | Uint 64  | 64    |Only present if payload Bytelenght > 0.  |

FIXME: What's this for? Something to do with WebRTC?

# Get ChainProof Message

This message is used to ask for the ChainProof from a peer node.

# ChainProof Message

This message is used to send a ChainProof to a peer node.

# Get AccountsProof Message

This message is used to ask for the AccountsProof for a given state of certain accounts (using the hash of the root of its Merkle Patricia Tree) from a peer node.

# AccountsProof Message

This message is used to send an AccountsProof to a peer node that requested it.

# Get AccountsTreeChunk Message

This message is used to ask for an AccountsTreeChunk for a given state of certain accounts (using the hash of the root of its Merkle Patricia Tree) from a peer node.

# AccountsTreeChunk Message

This message is used to send a AccountsTreeChunk to a peer node.

# Get TransactionsProof Message

This message is used to ask for the TransactionsProof from a peer node.

| Element        | Data type      | Bytes | Description       			        |
|----------------|----------------|-------|-------------------------------------|
| block hash      | hash 		  | 32    | 									|
| addresses length    | SignalId  | FIXME | 						   		    |

# TransactionsProof Message

This message is used to send a TransactionsProof to a peer node. FIXME: What's a TransactionsProof?

| Element        | Data type      | Bytes | Description       			        |
|----------------|----------------|-------|-------------------------------------|
| has roof       | Uint1   | 1     | 						   		    |
| proof          | Uint    | FIXME | 						   		    |

# TransactionsReceipts Message

This message is used to request a TransactionsReceipt to a peer node.
