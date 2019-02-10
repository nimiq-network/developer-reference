# Messages

All message contain following basic fields:

| Element   | Data type    | Bytes      | Description                |
|-----------|--------------|------------|----------------------------|
| magic     | uint32       | 4	 		    | `0x42042042`, Indicating that this is a message. |
| type      | [VarInt](primitives.md#variable-integer) | >=1, <= 8 | Usually 1 bytes |
| length    | uint32       | 4	 		    | Length of the message. Currently ignored. |
| checksum  | uint32       | 4	 		    | CRC32 checksum. |

All messages have a fixed [message type](constants.md#message-types).
Additional fields of specialized messages will be added behind.

## Version Message

This message `type = 0` sends out the status of this node, including it's protocol version, peer address, genesis block hash, and the hash of the latest block in its blockchain (the head).
It must be the first message a node sends to another node.

| Element         | Data type  | Bytes | Description
|-----------------|------------|-------|---
| version         | uint32     | 4     | Version of the protocol
| peer address    | uint32     | 4     | Three address types; see below
| genesis hash    | Hash       | 32    | Hash of Genesis block
| head hash       | Hash       | 32    | Hash of latest block in node's blockchain.
| challenge nonce | raw        | 32    | Peer needs to sign to authenticate.

## Version Acknowledgement Message

Immediately after receiving the initial version message, a node answers with the version acknowledgement message.

| Element      | Data type      | Bytes  | Description
|--------------|----------------|--------|---
| public key   | raw            | 32     | Public key of peer
| signature    | raw            | 64     | Signature on received address concatinated with challenge nonce

## Addresses

Three addresses are being used: Web Socket address, Web RTC address, and plain/dumb address.

All three share following fields:

| Element      | Data type      | Bytes  | Description
|--------------|----------------|--------|---
| protocol     | uint8          | 1      | `0..2`: dumb, RTC, WS address. See below.
| service mask | uint32         | 4      | `0` none, `1` nano, `2` light, `4` full
| timestamp    | uint64         | 8      | Used by other nodes to calculate offset.
| net address  | string         | 1 - 17 | IPv4 address as string unless the IP is private (then empty string), or `<unknown>`; first byte specifies length.
| public key   | raw            | 32     | Peer's public key
| signature    | raw            | 64     | Peer signs net address to make sure it can not be modified by someone else
| distance     | uint8          | 1      | Number of hops to this peer, `1` meaning the peers are connected directly.

Each peer is identified by it's peer ID, which is obtained by truncating the public key down to 16 bytes.
The RTC peer address uses the peer ID as signaling ID.

A WS peer address has following additional fields:

| Element      | Data type      | Bytes | Description
|--------------|----------------|-------|---
| host         | string         | >10   | Hostname
| port         | number         | 2     | Port number for Web Socket Connector to listen to.

## Request and Receive Inventory

A typical situation:
1. Node A enters the network and needs to synchronize
1. B sends out a `get blocks` message
1. B receives and sends an `relay inventory` with hashes of blocks that B considers useful for A
1. B compares the list with the blocks it has already
1. B uses `get data` to request the data for the blocks it doesn't have yet.

Similarily
1. Node A wants to update it's mempool and
1. A sends out a `mempool` message
1. Node B receives and sends an `relay inventory` message with blocks and transaction they have in their mempool
1. ... same like previous

### Get Blocks

| Element   | Data type      | Bytes | Description            |
|-----------|----------------|-------|------------------------|
| count     | uint16   | 2     | Number of hashes requested
| locators  | [Hash]   | count * 32     | List of block hashes from where to start sending more blocks. The receiving node will start sending from the first block it finds in it's blockchain.
| max inventory size | uint8   | 2     | The number of blocks that are been asked.  |
| direction | uint8   | 1     | `1` forward (blocks afterwards); `2` backwards (previous blocks).

The response can be `type = 6` for blocks, `7` for header, `8` for transaction, or `type = 4` if the requested inventory could not be found.

### Relay Inventory

Announcing it's blocks and transmissions, a node sends out an inventory message:

| Element   | Data type   | Bytes      | Description
|-----------|-------------|------------|---
| count     | uint16      | 2          |
| hashes    | [Type+Hash] | count * 36 | Depending on type, hashes of transactions or blocks

### Requesting Data
Request headers via `type = 3`, and block or transactions data via `type = 2`

| Element   | Data type   | Bytes      | Description
|-----------|-------------|------------|---
| count     | uint16      | 2          |
| hashes    | [Type+Hash] | count * 36 | Depending on type, hashes of transactions or blocks

The type for each element in the hashes list can be `0..2`: error, transaction, and block. `0` is not being used.

### Block Message

This message is used to send one block at a time (in serialized form).

| Element   | Data type | Bytes  | Description            |
|-----------|-----------|--------|------------------------|
| block     | Block     | <= 1MB | [Full block](block.md#block) |

### Header Message

This message is used to send one header at a time (in serialized form).

| Element   | Data type | Bytes | Description            |
|-----------|-----------|-------|------------------------|
| header    | Header    | 162   | [Block header](block.md#header) |

### TX Message

This message is used to send one header at a time (in serialized form).

| Element     | Data type   | Bytes | Description            |
|-------------|-------------|-------|------------------------|
| transaction | Transaction | XXX   | [Transaction](transaction.md#header) |

## Mempool Message

This message is used to request peers to relay their inventory/mempool content. No additional fields, `type = 9`, cf. [message types](constants.md#message-types).

## Reject Message


This message is used to signal to a peer that something they sent to the node was rejected and the reason why it was rejected.

| Element            | Data type                               | Bytes  | Description
|--------------------|-----------------------------------------|--------|---
| message type       | [VarInt](primitives.md#variable-integer) | 1      | [Message type](/constants.md#message-types)  |
| code               | uint8                                   | 1      | [Reject message codes](/constants.md#reject-message-code)  |
| reason length      | uint8                                   | 1      |
| reason             | string                                  | length | The reason why.  |
| extra data length  | uint16                                  | 2      |
| extra data         | raw                                     | length | Extra information that might be useful to the requester.

## Subscribe Message

This message is used to subscribe to messages from the node that this message is sent to, two version exist, "addresses" and "min fee".

[//]: # (FIXME what's the effect of being subscribed? how to unsubscribe?)

| Element     | Data type      | Bytes | Description       			        |
|-------------|----------------|-------|------------------------------------|
| subscription type | uint8    | 1     | `2` for "addresses" or `3` for "min fee"

Type "addresses" adds:

| Element     | Data type      | Bytes | Description       			        |
|-------------|----------------|-------|------------------------------------|
| address count | uint16    | 2     | Number of addresses
| addresses | [Address]    | count * 20 | List of addresses to subscribe to

Type "min fee" adds:

| Element | Data type | Bytes | Description
|---------|-----------|-------|---
| fee     | uint64    | 8     | Min fee per byte in Luna required to subscribe

## Relay Addresses

This message is used to relay the addresses to other peers.

### Get Addresses Message

Ask for for addresses to peers that use the specified protocol and provide the specified services.

| Element       | Data type | Bytes | Description
|---------------|-----------|-------|---
| protocol mask | uint8     | 1     | `0` dumb, `1` ws, `2` rtc
| service mask  | uint32    | 4     | `0` none, `1` nano, `2` light, `4` full

### Addresses Message

A list of address in return for a `get addresses` message.

| Element       | Data type | Bytes      | Description
|---------------|-----------|------------|---
| address count | uint16    | 2          | Number of addresses
| addresses     | [Address] | count * 20 | List of addresses of other peers in the network.

## Heart Beat Messages

This messages are used to make sure that the other peer node is available still.

| Element | Data type | Bytes | Description
|---------|-----------|-------|---
| type    | uint16    | 2     | `22` for ping, `23` for pong (response)
| ...
| nonce   | uint32    | 4     | Make pings and pongs unique, avoid replay  |

## Signal Message

[//]: # (FIXME What's the effect of this message? Answers?)

Signaling is needed for the [browser clients](nodes-and-clients.md#browser-client) to establish a connection with other clients of the same type.

| Element            | Data type | Bytes  | Description
|--------------------|-----------|--------|---
| sender id          | raw 	     | 16     | 16 bytes address of sender
| recipient id       | raw 	     | 16     | 16 bytes address of recipient
| nonce	             | uint32    | 4      | To avoid replay
| time to live       | uint8     | 1      | TTL
| flags		           | uint8     | 1      | Unroutable: `0x1`, TTL exceeded: `0x2`
| payload length     | uint16    | 2      |
| payload            | raw       | length | Signed data, cf. `signature`
| sender public key  | uint32    | 32     | If payload payload length > 0
| signature          | raw       | 64     | If payload payload length > 0

## Relay Proofs

### Chain Proof
To get chain proof, sent a basic message with type = `40`.

[//]: # (FIXME More details on the reponse. Any fields in get?)

The response (`type = 41`) contains blocks and headers.

| Element            | Data type                 | Bytes           | Description
|--------------------|---------------------------|-----------------|---
| block count        | uint16                    | 2               | Amount of blocks attached
| blocks             | [Block](block.md#block)   | max count * 1MB | Full blocks
| header count       | uint16                    | 2               | Amount of headers attached
| header chain       | [Header](block.md#header) | count * 162     | Headers

### Accounts Proof

A node can ask (`type = 42`) for proofs for certain accounts by using the hash of it's [accounts tree root](block.md#header).

| Element            | Data type                  | Bytes          | Description
|--------------------|----------------------------|----------------|---
| block hash         | Hash                       | 32             | Hash of block these accounts should be in
| address count      | uint16                     | 2              |
| addresses          | [Address](block.md#header) | count * 20     | Addresses to be checked

To get a response (`type = 43`) with following data:

| Element            | Data type                  | Bytes          | Description
|--------------------|----------------------------|----------------|---
| block hash         | Hash                       | 32             | Block these accounts are in
| has proof          | uint8                      | 1   | `1` means the proof is attached, otherwise `0`
| proof count        | uint16                     | 2              |
| proofs             | [AccountsTreeNode](account-tree.md#account-tree-node) | count * variable size | Proofs for the requested accounts as nodes from the Merkle tree.

To request a paricular chunk of of account tree nodes (`type = 44`)

| Element             | Data type                  | Bytes          | Description
|---------------------|----------------------------|----------------|---
| block hash          | Hash                       | 32             | Hash of block these accounts should be in
| start prefix length | uint8                      | 1              |
| start prefix        | string                     | length         | Defining the part of the tree where accounts are needed from.

The node should respond a message `type = 45`:

| Element             | Data type                  | Bytes  | Description
|---------------------|----------------------------|--------|---
| block hash          | Hash                       | 32     | Hash of block these accounts should be in
| has chunks          | uint8                      | 1      | `1` means chunks are attached
| node count          | uint16                     | 2      |
| nodes               | [AccountsTreeNode](account-tree.md#account-tree-node) | count * variable size | Account tree nodes from the requested part of the tree.
| proof count         | uint16                     | 2      |
| proofs              | [AccountsTreeNode](account-tree.md#account-tree-node) | count * variable size | Account tree nodes as proof

[//]: # (FIXME both nodes and proof are acount tree nodes?)

### Transactions Proof

[//]: # (FIXME can not find a transaction message, only a transaction proof message)
This message is used to send one transaction at a time (in serialized form) but can also contain an [`accountsProof`](/messages.md#accounts-proof-message).

#### Request Transaction Proofs

| Element   | Data type | Bytes | Description            |
|-----------|-----------|-------|------------------------|
| block hash    | Hash    | 32   | Hash of [block](block.md#block) the transactions belong to |
| transactions count | uint16    | 2   | Count of transactoins being requested
| transactions hash    | [Transaction]    | count * 20   | Hashes of transactions being requested

#### Receive Transaction Proofs

[//]: # (FIXME how do the tx hashes of the get relate to this response?)

| Element   | Data type | Bytes | Description            |
|-----------|-----------|-------|------------------------|
| block hash    | Hash    | 32   | Hash of [block](block.md#block) the transactions belong to |
| has proof | uint8    | 1   | `1` means the proof is attached, otherwise `0`
| proof length          | uint16       | 2            | If `has proof` was set                                                       |
| proof                 | raw          | proof length | The proof if `has proof` was set                                                       |

### Transaction Receipts

Get transactions receipts via message `type = 49`

| Element   | Data type                        | Bytes | Description
|-----------|----------------------------------|-------|---
| address   | [Address](primitives.md#address) | 20    | Address of transaction

Receive transactions receipts in a `type = 50` message:

| Element       | Data type | Bytes | Description            |
|---------------|-----------|-------|------------------------|
| receipt count | uint16    | 2     |
| receipts      | 2x Hash   | 64    | Transaction hash and block hash


