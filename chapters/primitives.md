# Primitives

## Public Key
32 bytes, derived using the [Ed25519](https://ed25519.cr.yp.to/) algorithm.

## Signature
64 bytes. The result of the [Ed25519](https://ed25519.cr.yp.to/) `sign: (privateKey, message) -> signature` operation.

## Address
Serialized in 20 bytes usually created by keeping the first 20 bytes of a hash or a public key.

## Hash
A hash can be of three types, but only two are used: [Blake2b](https://en.wikipedia.org/wiki/BLAKE_%28hash_function%29#Blake2b_algorithm) and [Argon2d](https://en.wikipedia.org/wiki/Argon2) - SHA256 can be used for contracts, Blake2b is the default.
When creating a light hash, Blake2b is used while when creating a hard hash as a proof of work, Argon2d ([memory-hard](https://en.wikipedia.org/wiki/Memory_bound_function)) is being used.
Indepenendtly, a hash is always 32 bytes long.

## Variable integer

The first byte (`type`) is used to determind which size should be used. Very small numbers are stored straight in the first byte, bigger numbers are stored in 2 to 8 bytes.

| Element   | Data type   | Bytes     | Description                |
|-----------|-------------|-----------|----------------------------|
| type      | uint8       | 1	 		    | if < 0xFD, type is also the value
| value     | uint16..64  | 2..8	    | depends on type; 0xFD: uint16, 0xFE: uint32, otherwise unit64


