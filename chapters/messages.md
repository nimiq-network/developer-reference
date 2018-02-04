# Message

Message instance. This is usually not called directly but by subclasses.

| Element   | Data type    | Bytes            | Description                |
|-----------|--------------|------------------|----------------------------|
| magic     | unsigned int | 4	 		      | [Magic String](./constants.md#message-magic)|
| type      | unsigned int | 4	 		      | [Message Type](./constants.md#message-types)|
| lenght    | unsigned int | 4	 		      | lenght of the message. |
| checksum  | unsigned int | 4	 		      | CRC32 checksum. |

# Version Message
 
This message transmits the node's version of the protocol, peerAddress, the hash of its Genesis block, and the hash of the latest block in its blockchain (the head).

| Element     | Data type    | Bytes            | Description            |
|-------------|--------------|------------------|------------------------|
| version     | unsigned int | 4	 		    | version of the protocol|
| peerAddress | unsigned int | 4	 		    | [WsPeerAddress](#WsPeerAddress) or [RtcPeerAddress](#RtcPeerAddress)|

## WsPeerAddress

| Element   | Data type      | Bytes | Description            |
|-----------|----------------|-------|------------------------|
| protocol  | unsigned int | 1     | [Protocol type](/constants.md#protocol-types)      |
| services  | unsigned int | 4     | [Service type](./constants.md#services-types)      |
| timestamp | unsigned int | 8     | Used by other nodes to calculate offset.   |
| host      | string         | (?)   | Hostname.               |
| port      | number         | 2     | Port number for WebSocketConnector to listen to.	|

## RtcPeerAddress

| Element   | Data type      | Bytes | Description            |
|-----------|----------------|-------|------------------------|
| protocol  | unsigned int   | 1     | [Protocol type](/constants.md#protocol-types)      |
| services  | unsigned int   | 4     | [Service type](./constants.md#services-types)      |
| timestamp | unsigned int   | 8     | Used by other nodes to calculate offset.   |
| signalId  | unsigned int	 | 2     | Signaling Id (Add a link to a Signaling Section)     |
| distance  | number		 | 1     | 0: self, 1: direct connection, 2: 1 hop |

# Inventory Message

The inventory message points a vector to requested data. The structure for `BaseInventoryMessage` is the following:

| Element   | Data type      | Bytes | Description            |
|-----------|----------------|-------|------------------------|
| type  | unsigned int   | 4     | [Protocol type](/constants.md#protocol-types)      |
| hash  | unsigned int   | 32     | Hash of the message  |

There are several sub-message categories related to inventory message:

  * Get Data 
  * Get Header
  * Not Found

# Get Blocks Message


| Element   | Data type      | Bytes | Description            |
|-----------|----------------|-------|------------------------|
| locators  | unsigned int   | 2     | The hash of the block from which to start sending blocks. This is an array, the first block that is found in the blockchain of the receiving node should be used. |
| maxInvSize | unsigned int   | 2     | The number of blocks that are been asked.  |
| direction | unsigned int   | 1     | Defines if the blocks been asked are the ones after the locators or before the locators.  |

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
| messageType | unsigned int   | 1     |  |
| code        | unsigned int   | 1     | Reject Message Code  |
| reason      | unsigned int   | >255  | FIXME: What is reason for?  |
| byteLength  | unsigned int   | 1     |   |
| extraData   | unsigned int   | 2     |   |

# Subscribe Message

This message is used to subscribe to messages from the node that this message is sent to.

# Addresses Message

This message is used to relay the addresses we know to other peers.

# Get Addresses Message

Ask for the addresses that the peer knows that use the specified protocols and provide the specified services.

| Element     | Data type      | Bytes | Description       			        |
|-------------|----------------|-------|------------------------------------|
| protocolMask| unsigned int   | 1     | 0:DUMB, 1:WS, 2:RTC  			    |
| serviceMask | unsigned int   | 4     | 0: NONE, 1:LIGHT, 2:LIGHT, 4:FULL  |

# Ping Message

This message is used to check if we can still communicate with a peer node.

# Pong Message

This is the response to a ping message.

# Signal Message

| Element        | Data type      | Bytes | Description       			        |
|----------------|----------------|-------|------------------------------------|
| senderId       | SignalId 	  | 16    | 									|
| recipientId    | SignalId 	  | 16    | 						   		    |
| nonce		     | unsigned int   | 4     | 								    |
| ttl	     	 | unsigned int   | 1     | 								    |
| flags		     | unsigned int   | 1     | 								    |
| payload Bytelenght | unsigned int   | 2     | 								    |
| payload        | unsigned int   | FIXME | 								    |
| senderPubKey   | unsigned int   | 32    |Only present if payload Bytelenght > 0.  |
| signature      | unsigned int   | 64    |Only present if payload Bytelenght > 0.  |

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
| blockHash      | hash 		  | 32    | 									|
| addresses length    | SignalId  | FIXME | 						   		    |

# TransactionsProof Message

This message is used to send a TransactionsProof to a peer node. FIXME: What's a TransactionsProof?

| Element        | Data type      | Bytes | Description       			        |
|----------------|----------------|-------|-------------------------------------|
| hasProof       | unsigned int   | 1     | 						   		    |
| proof          | unsigned int   | FIXME | 						   		    |

# TransactionsReceipts Message

This message is used to request a TransactionsReceipt to a peer node. 