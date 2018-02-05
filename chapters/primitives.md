---
category: "Primitives"
---

# Primitives

## Public Key
32 bytes, derived using the [Ed25519](https://ed25519.cr.yp.to/) algorithm.

## Signature
64 bytes, created from private and public key and a data component using
 [Ed25519](https://ed25519.cr.yp.to/).

## Address
Serialized in 20 bytes usually created by keeping the first 20 bytes of a hash or a public key.

## Hash
A hash can be of three types, but only two are used: [Blake2b](https://en.wikipedia.org/wiki/BLAKE_%28hash_function%29#Blake2b_algorithm) and [Argon2d](https://en.wikipedia.org/wiki/Argon2) - SHA256 can be used for contracts, Blake2b is the default.
When creating a light hash, Blake2b is used while when creating a hard hash as a proof of work, Argon2d ([memory-hard](https://en.wikipedia.org/wiki/Memory_bound_function)) is being used.
Indepenendtly, a hash is always 32 bytes long.
