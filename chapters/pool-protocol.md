# Mining Pool protocol

## General

### Client modes

The Nimiq Mining Pool protocol currently describes two client modes: smart and nano.

#### smart

In smart-mode, the client is fully responsible for creating blocks.
The client operates as a solo-miner would do (and is also capable of doing a graceful fallback to solo mining).
The server only describes the values to be used as a miner address and in the `extraData` field of a block.

The smart-mode Pool Miner requires a Nimiq light- or full-node.

#### nano

In nano-mode, the client only validates that the pool asks for the correct chain to be operated on.
The server provides all necessary details for the client to build the block, as long as it operates on a chain known to the client.
This mode is similar to `getblocktemplate`-based protocols on Bitcoin.

The nano-mode Pool Miner requires a Nimiq node of any kind, a nano-node is sufficient.

### Network communication

A secure websocket connection is used as a transport layer for client-server communication. All messages are exchanges as a single WebSocket frame.

## Messages

All messages follow the same structure: they consist of a JSON-encoded object with a property named `message` as well as message specific properties.

#### `register`

Send by client directly after connecting to the server.

##### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `mode`    | string, `nano` or `smart` | The mode this client uses |
| `address` | string | The address (in IBAN-style format) that should be rewarded for the miner actions |
| `deviceId` | number, uint32 | The clients device id. This ID should be unique for the device and stored, such that it stays the same after restarts |
| `genesisHash` | string | Base64 encoded hash of the genesis block used by the client |

##### Example

```json
{
    "message": "register",
    "mode": "nano",
    "address": "NQ07 0000 0000 0000 0000 0000 0000 0000 0000",
    "deviceId": 12345678,
    "genesisHash": "Jkqvik+YKKdsVQY12geOtGYwahifzANxC+6fZJyGnRI="
}
```

#### `registered`
 
Sent by the server after a succesful registration. Does not include parameters.
 
##### Example
 
 ```json
 {
     "message": "registered"
 }
 ```

#### `settings`

Sent by the server to announce new mining settings.

##### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `address` | string | The address (in IBAN-style format) that the client should use as a miner address for future shares |
| `extraData` | string | Base64 encoded buffer that the client should use in the `extraData` field for future shares |
| `target`  |number, uint256 | The maximum allowed hash value for future shares |
| `nonce`   |number, uint64 | A number used once that is associated with this connection |

##### Example
 
```json
{
    "message": "settings",
    "address": "NQ07 0000 0000 0000 0000 0000 0000 0000 0000",
    "extraData": "J8riQiN1lgXT6QYlsdWMKA+qEWxCPYycjZ19zsk1...",
    "target": 1.7668470647783843e+72,
    "nonce": 1030036789885656
}
 ```

#### `new-block` (nano)

Sent by the server to announce a new block that should be used by a client running in nano-mode.

##### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `bodyHash` | string | Base64 encoded hash of the block body |
| `accountsHash` | string | Base64 encoded hash of the accounts tree after applying the block body |
| `previousBlock` | string | Base64 encoded light block that is the predecessor of the block to be mined |

##### Example
 
```json
{
    "message": "new-block",
    "bodyHash": "rCBGsSJGOOo82af+MhRXb7Dj+lb0oRzrN0EXBg7Jh4g=",
    "accountsHash": "9GgRxMDSVUpePCyUDKuAw3hmHeMEj7Y77GsWEssd2qU=",
    "previousBlock": "AAHL9kSzaU/uVRSsBVRSlYrx7Dw7NUSFDCWIDhY/..."
}
```

#### `share`

Sent by client when a valid share was found.

##### Parameters (nano)

| Parameter | Type | Description |
|-----------|------|-------------|
| `block`   | string | Base64 encoded light block |

##### Parameters (smart)

| Parameter | Type | Description |
|-----------|------|-------------|
| `blockHeader` | string | Base64 encoded block header |
| `minerAddrProof` | string | Base64 encoded inclusion proof for the `minerAddr` field in the block body |
| `extraDataProof` | string | Base64 encoded inclusion proof for the `extraData` field in the block body |
| `block`   | string, optional | Base64 encoded full block. May only be sent if the block is a valid block so that the pool server is faster in picking it up |

##### Example (nano)
 
```json
{
    "message": "share",
    "block": "AAHL9kSzaU/uVRSsBVRSlYrx7Dw7NUSFDCWIDhY/..."
}
```

##### Example (smart)
 
```json
{
    "message": "share",
    "blockHeader": "AAHL9kSzaU/uVRSsBVRSlYrx7Dw7NUSFDCWIDhY/...",
    "minerAddrProof": "IXd9Hbms/FWTUl5YEYa5ztf6N9eIz+nIfswDOqGk...",
    "extraDataProof": "aZiypZ9S4qvSYbG4dBX4ww9TwOdKeLB+KRwNc0eP..."
}
```

#### `error`

Sent by the server if the client sent an invalid share.
The server may send this message at most once per `share`.

##### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `reason`  | string | A user-readable string explaining why the server denied the share |

##### Example
 
```json
{
    "message": "error",
    "reason": "Invalid PoW"
}
```

#### `balance`

Sent by the server to announce the balance that is currently hold by the pool for the address the user announced on `register`.

The server may send this message at any time after the client registered. A new `balance` message does not imply any balance changes.

##### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `balance` | number | The current balance of the user in the smalest possible unit. This includes funds that are not yet confirmed on the blockchain |
| `confirmedBalance` | number | The current balance of the user in the smalest possible unit. This only includes funds that operator considers confirmed and are available for payout |
| `payoutRequestActive` | boolean | `true`, if there is a payout request waiting for the user, `false` otherwise |

##### Example
 
```json
{
    "message": "balance",
    "balance": 123010221,
    "confirmedBalance": 122010202,
    "payoutRequestActive": false
}
```

#### `payout`

Sent by the client to request payout from the server. Depending on server configuration, manual payout may be suspect to an additional fee.

##### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `proof`   | string | Base64 encoded signature proof of the string `POOL_PAYOUT`, concatenated with the byte representation of the connection nonce. |

##### Example
 
```json
{
    "message": "payout",
    "proof": "J4VEpFwKNoklkyeUCvzBvixate3yPbDyYfWSusF/..."
}
```
