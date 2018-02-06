---
category: "Higher level concepts"
---

# Constants

## Policy

### Block Parameters

|**Parameters**						|**Value**		|**Description**													|
|----------------------------------	|---------------|------------------------------------------------------------------	|
|`BLOCK_TIME`						|60				|Targeted block time in seconds.									|
|`BLOCK_SIZE_MAX`					|1e6 (1 MB)		|Maximum block size in bytes.										|
|`DIFFICULTY_BLOCK_WINDOW`			|120			|Number of blocks we take into account to calculate next difficulty.|
|`DIFFICULTY_MAX_ADJUSTMENT_FACTOR`	|2				|Limits the rate at which the difficulty is adjusted min/max.		|
|`TRANSACTION_VALIDITY_WINDOW`		|120			|Number of blocks a transaction is valid.							|

### Supply & Emission Parameters

|**Parameter**				|**Value**		|**Description**																		|
|--------------------------	|--------------	|--------------------------------------------------------------------------------------	|
|`SATOSHIS_PER_COIN`		|1e5			|Number of Satoshis per Nimiq.															|
|`TOTAL_SUPPLY`				|21e14			|Targeted total supply in satoshis.														|
|`INITIAL_SUPPLY`			|0				|Initial supply before genesis block in satoshis.										|
|`EMISSION_SPEED`			|2^22			|Emission speed.																		|
|`EMISSION_TAIL_START`		|48692960		|First block using constant tail emission until total supply is reached.				|
|`EMISSION_TAIL_REWARD`		|4000			|Constant tail emission in satoshis until total supply is reached.						|
|`M`						|240			|NIPoPoW Security parameter `M`															|
|`K`						|120			|NIPoPoW Security parameter `K`															|
|`DELTA`					|0.1			|NIPoPoW Security parameter `DELTA`														|
|`NUM_BLOCKS_VERIFICATION`	|250			|Number of blocks the light client downloads to verify the `AccountsTree` construction.	|
|`NUM_SNAPSHOTS_MAX`		|20				|Maximum number of snapshots.															|

## Miner

|**Parameter**				|**Value**		|**Description**																		|
|--------------------------	|--------------	|--------------------------------------------------------------------------------------	|
|`MIN_TIME_ON_BLOCK`		|10000<br>*(10 sec)	|Minimum time in milliseconds a miner needs to wait before starting work on a block.	|
|`MOVING_AVERAGE_MAX_SIZE`	|10				|Number of blocks considered in moving average for the hashrate calculation.			|

## Network

|**Parameter**				|**Value**							|**Description**														|
|--------------------------	|----------------------------------	|----------------------------------------------------------------------	|
|`PEER_COUNT_DESIRED`		|6									|Number of peers the node aims to connect to.|
|`PEER_COUNT_RELAY`			|4									|When new addresses are learned, pick `PEER_COUNT_RELAY` random peers and relay addresses to them if number of addresses <= 10|
|`CONNECTING_COUNT_MAX`		|2									|Maximum number of ongoing outbound connection attempts					|
|`SIGNAL_TTL_INITIAL`		|3									|Considered to check validity of the signals.							|
|`ADDRESS_UPDATE_DELAY`		|1000								|Delay added before checking peer count and connecting to new peers. This allows RTC peer addresses to be sent to the peer in question.	|
|`CONNECT_BACKOFF_INITIAL`	|1000									|Backoff for peer count check in seconds.							|
|`CONNECT_BACKOFF_MAX`		|300000<br>(5 min)						|Maximum Backoff time before a new backoff is triggered.		|
|`SIGNAL_MAX_AGE`			|10									|Maximum age in seconds a signal needs to have to be valid.				|
|`PEER_COUNT_MAX`			|15/50000	|Maximum amount of peers. Browsers are limited to 15, other platforms to 50k.					|
|`PEER_COUNT_PER_IP_WS_MAX`	|1/25		|Maximum WebSocket connections from the same IP Address. Browsers are limited to 1, other platforms to 25.|
|`PEER_COUNT_PER_IP_RTC_MAX`|2									|Maximum RTC connections for a peer's IP Address.						|

## NetUtils

|**Parameter**				|**Value**							|**Description**											|
|--------------------------	|----------------------------------	|----------------------------------------------------------	|
|`IP_BLACKLIST`				|'0.0.0.0',<br>'255.255.255.255','::'|Blacklisted IP address.|
|`IPv4_PRIVATE_NETWORK`		|'10.0.0.0/8',<br>'172.16.0.0/12',<br>'192.168.0.0/16',<br>'100.64.0.0/10',<br>'169.254.0.0/16'|IP Address ranges considered within private network.|

## NetworkAgent


|**Parameter**					|**Value**			|**Description**																|
|------------------------------	|------------------	|------------------------------------------------------------------------------	|
|`HANDSHAKE_TIMEOUT`			|3000<br>(3 sec)	|Timeout {ms} the node waits before droping a peer's connection.				|
|`PING_TIMEOUT`					|10000<br>(10 sec)	|Timeout {ms} the node waits for a peer to answer with a matching pong message during connectivity check.|
|`CONNECTIVITY_CHECK_INTERVAL`	|60000<br>(1 min)	|Interval at which the node regularly checks connectivity.						|
|`ANNOUNCE_ADDR_INTERVAL`		|300000<br>(5 min)	|Interval at which the node regularly announces its address.					|
|`RELAY_THROTTLE`				|120000<br>(2 min)	|Time {ms} the node will wait to re send an address.							|
|`VERSION_ATTEMPTS_MAX`			|10					|During handshake, maximum amount of version attempts per address the node will allow before droping the connection. A version attempt checks version, network address & blockchain head hash.|
|`VERSION_RETRY_DELAY`			|500				|Time {ms} the node waits before retrying a version attempt during handshake.	|

## Peer Address

### Parameters

|**Parameter**					|**Value**		|**Description**																|
|--------------------------	|-----------------	|------------------------------------------------------------------------------	|
|`MAX_AGE_WEBRTC`			|60000<br>(1 min)	|Age {ms} of a peer address to be sent back in a WebRTC address query.			|
|`MAX_AGE_DUMB`				|4					|Age {ms} of a peer address to be sent back in a Dumb address query.			|
|`MAX_FAILED_ATTEMPTS_WS`	|3					|Maximum failed attempts for peers connecting using WebSocket					|
|`MAX_FAILED_ATTEMPTS_RTC`	|2					|Maximum failed attempts for peers connecting using WebRTC						|
|`MAX_TIMESTAMP_DRIFT`		|600000<br>(10 min)	|Maximum time allowed for a peer address's timestamp to drift into the future.	|
|`HOUSEKEEPING_INTERVAL`	|60000<br>(1 min)	|Interval {ms} at which regular housekeeping routines are executed.				|
|`DEFAULT_BAN_TIME`			|600000<br>(10 min)	|Duration {ms} a peer address remains banned.									|
|`INITIAL_FAILED_BACKOFF`	|15000<br>(15 sec)	|Initial backoff, {ms}, for failed connections.									|
|`MAX_FAILED_BACKOFF`		|600000<br>(10 min)	|Maximum backoff, {ms}, for failed connections.									|

### States

|**State**					|**Value**	|**Description**																			|
|--------------------------	|----------	|------------------------------------------------------------------------------------------	|
|`NEW`						|1			|Initial state peer addresses are initialized with.											|
|`CONNECTING`				|2			|State in which the node remains while connection to the peer address is being established	|
|`CONNECTED`				|3			|Indicates a connection to a peer address has been established.								|
|`TRIED`					|4			|State a peer address is set to when it is disconnected. 									|
|`FAILED`					|5			|Indicates a network connection to a peerAddress has failed.								|
|`BANNED`					|6			|State of a peer address that have been banned.												|


## Signal Id Serialized Size

|**Parameter**				|**Value**	|**Description**											|
|--------------------------	|----------	|----------------------------------------------------------	|
|`SERIALIZED_SIZE`			|16			|Size in bytes of the serialized signal.					|


## Transaction Receipts Message Maximum Count

|**Parameter**				|**Value**	|**Description**												|
|--------------------------	|----------	|--------------------------------------------------------------	|
|`RECEIPTS_MAX_COUNT`		|500		|Maximum amount of transaction receipts in transaction receipts.|

## Signal Message Flags

|**Flag**					|**Value**	|**Description**														|
|--------------------------	|----------	|----------------------------------------------------------------------	|
|`UNROUTABLE`				|0x1		|Indicates a signal message is unroutable.								|
|`TTL_EXCEEDED`				|0x2		|Indicates the TTL of a signal message has exceeded the maximum allowed.|


## Reject Message Code

|**Flag**					|**Value**	|
|--------------------------	|----------	|
|`REJECT_MALFORMED`			|0x01		|
|`REJECT_INVALID`			|0x10		|
|`REJECT_OBSOLETE`			|0x11		|
|`REJECT_DOUBLE`			|0x12		|
|`REJECT_DUST`				|0x41		|
|`REJECT_INSUFFICIENT_FEE`	|0x42		|

## Messages

### Message Magic
|**Type**					|**Value**	|**Description**																		|
|--------------------------	|----------	|--------------------------------------------------------------------------------------	|
|`MAGIC`					|0x42042042	|Special string that indicates the information sent should be interpreted as a message.	|

### Message Types

|**Type**					|**Value**	|**Description**											|
|--------------------------	|----------	|----------------------------------------------------------	|
|`VERSION`					|0			|	[Version message.](/messages.md#version-message)														|
|`INV`						|1			|	[Inventory message](/messages.md#inventory-message)														|
|`GET_DATA`					|2			|															|
|`GET_HEADER`				|3			|															|
|`NOT_FOUND`				|4			|															|
|`GET_BLOCKS`				|5			|	[Get Blocks message](/messages.md#get-blocks-message)														|
|`BLOCK`					|6			|	[Blocks message](/messages.md#block-message)														|
|`HEADER`					|7			|	[Header message](/messages.md#header-message)														|
|`TX`						|8			|	[Transaction message](/messages.md#transaction-message)														|
|`MEMPOOL`					|9			|	[Mempool message](/messages.md#mempool-message)														|
|`REJECT`					|10			|	[Reject message](/messages.md#reject-message)														|
|`SUBSCRIBE`				|11			| [Subscribe message](/messages.md#subscribe-message)															|
|`ADDR`						|20			|	[Addresses Message](/messages.md#addresses-message)														|
|`GET_ADDR`					|21			|	[Get Addresses Message](/messages.md#get-addresses-message)														|
|`PING`						|22			|	[Ping message](/messages.md#ping-message)														|
|`PONG`						|23			|	[Pong message](/messages.md#pong-message)														|
|`SIGNAL`					|30			|	[Signal message](/messages.md#signal-message)														|
|`GET_CHAIN_PROOF`			|40			|	[Get Chain Proof message](/messages.md#get-chain-proof-message)														|
|`CHAIN_PROOF`				|41			|	[Chain proof message](#chain-proof-message)														|
|`GET_ACCOUNTS_PROOF`		|42			| [Get accounts proof message](/messages.md#get-accounts-proof-message)															|
|`ACCOUNTS_PROOF`			|43			|	[Accounts proof message](/messages.md#accounts-proof-message)														|
|`GET_ACCOUNTS_TREE_CHUNK`	|44			|	[Gets accounts tree chunk message](/messages.md#get-accounts-tree-chunk-message)														|
|`ACCOUNTS_TREE_CHUNK`		|45			|	[Accounts tree chunk message](/messages.md#accounts-tree-chunk-message)														|
|`GET_TRANSACTIONS_PROOF`	|47			|	[Get transactions proof message](/messages.md#get-transactions-proof-message)														|
|`TRANSACTIONS_PROOF`		|48			|	[Transactions proof](/messages.md#transactions-proof-message)														|
|`GET_TRANSACTION_RECEIPTS`	|49			|											|
|`TRANSACTION_RECEIPTS`		|50			|	[Transactions receipts](/messages.md#transactions-receipts-message)														|

## `GetBlocksMessage` Directions

|**Direction**	|**Value**	|
|--------------	|----------	|
|`FORWARD`		|0x1 		|
|`BACKWARD`		|0x2 		|

## Protocol Types

|**Protocol**	|**Value**	|
|--------------	|----------	|
|`DUMP`			|0 			|
|`WS`			|1			|
|`RTC`			|2			|

## Services Types

|**Protocol**	|**Value**	|
|--------------	|----------	|
|`NONE`			|0 			|
|`NANO`			|1			|
|`LIGHT`		|2			|
|`FULL`			|4			|

## Alphabets

|**Alphabet**	|**Value**							|
|--------------	|----------------------------------	|
|`RFC4648`		|'ABCDEFGHIJKLMNOPQRSTUVWXYZ234567='|
|`RFC4648_HEX`	|'0123456789ABCDEFGHIJKLMNOPQRSTUV='|
|`NIMIQ`		|'0123456789ABCDEFGHJKLMNPQRSTUVXY'	|
|`HEX_ALPHABET`	|'0123456789abcdef'					|

## CryptoWorker

|**Paramemter**				|**Value**	|
|--------------------------	|----------	|
|`ARGON2_HASH_SIZE` 		|32 		|
|`BLAKE2_HASH_SIZE` 		|32			|
|`SHA256_HASH_SIZE` 		|32 		|
|`PUBLIC_KEY_SIZE` 			|32 		|
|`PRIVATE_KEY_SIZE` 		|32 		|
|`MULTISIG_RANDOMNESS_SIZE` |32 		|
|`SIGNATURE_SIZE` 			|64 		|
|`PARTIAL_SIGNATURE_SIZE` 	|32 		|
|`SIGNATURE_HASH_SIZE` 		|64 		|


## MerkleProof Operations

|**Operation**	|**Value**	|
|--------------	|----------	|
|`CONSUME_PROOF`|0 			|
|`CONSUME_INPUT`|1			|
|`HASH`			|2			|

## Browser Client Errors

|**Error**			|**Value**	|
|------------------	|----------	|
|`ERR_WAIT`			|-1 		|
|`ERR_UNSUPPORTED`	|-2			|
|`ERR_UNKNOWN`		|-3			|

## WebRtc Data Channel

**Parameter**				|**Value**			|**Description**																		|
|--------------------------	|------------------	|--------------------------------------------------------------------------------------	|
|`CHUNK_SIZE_MAX`			|16384<br>(16 Kb)	|Maximum size {bits} allowed for a WebRTC message before being splited into chunks.	|
|`MESSAGE_SIZE_MAX`			|10485760<br>(10Mb)	|Maximum size {bits} allowed for a message WebRTC.										|
|`CHUNK_TIMEOUT`			|5000<br>(5 sec)	|Allowed timeout between chunks for a chunked WebRTC message.							|
|`CHUNK_BEGIN_MAGIC`		|0xff				|Special string that indicates the beginning chunk of a message splitted in chunks.		|
|`CHUNK_INNER_MAGIC`		|0xfe				|Special string that indicates the middle chunks of a message splitted in chunks.		|
|`CHUNK_END_MAGIC`			|0xfd				|Special string that indicates the end chunk of a message splitted in chunks.			|

## WebSocket Connector

|**Parameter**				|**Value**	|**Description**											|
|--------------------------	|----------	|----------------------------------------------------------	|
|`CONNECT_TIMEOUT`			|5000		|Timeout for WebSocket conections in Browsers.				|

## Full Consensus

|**Parameter**				|**Value**	|**Description**											|
|--------------------------	|----------	|----------------------------------------------------------	|
|`SYNC_THROTTLE`			|1500		|Time {ms} for the node to wait for more peers to connect before start syncing. |

## FullConsensusAgent

|**Parameter**				|**Value**			|**Description**																	|
|--------------------------	|------------------	|----------------------------------------------------------------------------------	|
|`SYNC_ATTEMPTS_MAX`		|10					|Maximum number of blockchain sync retries before closing the connection.			|
|`GETBLOCKS_VECTORS_MAX`	|500				|Maximum number of inventory vectors to sent in the response for `onGetBlocks`.		|
|`RESYNC_THROTTLE`			|3000<br>(3 sec)	|Time in millisecongs to wait before triggering a blockchain re-sync with the peer.	|
|`MEMPOOL_DELAY_MIN`		|2000<br>(2 sec)	|Minimum time {ms} to wait before triggering the initial mempool request.			|
|`MEMPOOL_DELAY_MAX`		|20000<br>(20 sec)	|Maximum time {ms} to wait before triggering the initial mempool request.			|
|`MEMPOOL_THROTTLE`			|1000				|Time {ms} to wait between sending full inv vectors of transactions during Mempool request.	|
|`MEMPOOL_ENTRIES_MAX`		|10					|Maximum number Number of allowed transaction to send.								|


## FullChain

|**Parameter**		|**Value**	|**Description**																						|
|------------------	|----------	|------------------------------------------------------------------------------------------------------	|
|`ERR_ORPHAN`		|-2			|Indicates the block's immediate predecessor is not part of the chain.									|
|`ERR_INVALID`		|-1			|Idicates the block is not a full block (includes block body) or match with an intrinsic variant. (?)	|
|`OK_KNOWN`			|0			|Indicates the node already knows this block..															|
|`OK_EXTENDED`		|1			|Indicates the block extends our current main chain.													|
|`OK_REBRANCHED`	|2			|Indicates the block fork has become the hardest chain and node will rebranch to it.									|
|`OK_FORKED`		|3			|Indicates new fork was created.															|



## PartialLightChain State

|**Parameter**			|**Value**	|**Description**													|
|----------------------	|----------	|------------------	|
|`ABORTED`				|-1			|(?)				|
|`PROVE_CHAIN`			|0			|(?)				|
|`PROVE_ACCOUNTS_TREE`	|1			|(?)				|
|`PROVE_BLOCKS`			|2			|(?)				|
|`COMPLETE`				|3			|(?)				|

## LightConsensusAgent

|**Parameter**							|**Value**	|**Description**	|
|--------------------------------------	|----------	|------------------	|
|`CHAINPROOF_REQUEST_TIMEOUT`			|20000		|Maximum time {ms} to wait for `chainProof` after sending out `getChainProof` before dropping the peer.	|
|`ACCOUNTS_TREE_CHUNK_REQUEST_TIMEOUT`	|5000		|Maximum time {ms} to wait for accounts tree chunk after requesting it to a peer before dropping that peer.|
|`SYNC_ATTEMPTS_MAX`					|5			|Maximum number of blockchain sync retries before closing the connection.	|
|`GETBLOCKS_VECTORS_MAX`				|500		|Maximum number of inventory vectors to sent in the response for `onGetBlocks`.	|


## Light Consensus

|**Parameter**				|**Value**	|**Description**											|
|--------------------------	|----------	|----------------------------------------------------------	|
|`SYNC_THROTTLE`			|1000		|Time {ms} for the node to wait for more peers to connect before start syncing. |

## NanoMempool

|**Parameter**				|**Value**	|**Description**											|
|--------------------------	|----------	|----------------------------------------------------------	|
|`TRANSACTIONS_MAX_COUNT`	|50000		|Maximum lenght of the mempool, oldest transactions are evicted from the mempool if it grows too large than this value. |
|`TRANSACTIONS_EVICT_COUNT`	|5000		|Amount of transaction to be evicted each time. |

## NanoConsensusAgent

|**Parameter**					|**Value**	|**Description**											|
|------------------------------	|----------	|----------------------------------------------------------	|
|`CHAINPROOF_REQUEST_TIMEOUT`	|30000		|Maximum time {ms} to wait for `chainProof` after sending out `getChainProof` before dropping the peer. 	 |
|`ACCOUNTSPROOF_REQUEST_TIMEOUT`|5000		|Maximum time {ms} to wait for accounts proof after requesting it to a peer before dropping that peer. |
|`TRANSACTIONSPROOF_REQUEST_TIMEOUT`	|10000		|Maximum time {ms} to wait for transaction proof after requesting it to a peer before dropping that peer. |
|`TRANSACTIONS_REQUEST_TIMEOUT`	|15000		|Maximum time {ms} to wait for `transactions` after sending out `getTransactions` before dropping the peer.|

## Nano Consensus

|**Parameter**				|**Value**	|**Description**											|
|--------------------------	|----------	|----------------------------------------------------------	|
|`SYNC_THROTTLE`			|1000		|Time {ms} for the node to wait for more peers to connect before start syncing. |

## NanoChain

|**Parameter**		|**Value**	|**Description**																						|
|------------------	|----------	|------------------------------------------------------------------------------------------------------	|
|`ERR_ORPHAN`		|-2			|Indicates the block's immediate predecessor is not part of the chain.									|
|`ERR_INVALID`		|-1			|Idicates the block is not a full block (includes block body) or match with an intrinsic variant. (?)	|
|`OK_KNOWN`			|0			|Indicates the node already knows this block..															|
|`OK_EXTENDED`		|1			|Indicates the block extends our current main chain.													|
|`OK_REBRANCHED`	|2			|Indicates the block fork has become the hardest chain and node will rebranch to it.									|
|`OK_FORKED`		|3			|Indicates new fork was created.															|
