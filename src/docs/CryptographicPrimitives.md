# Cryptographyc Primitives

There are several cryptographic primitives on which Nimiq relies upon for various tasks (signing and verifying signatures, hashes, PoW, etc).

## Signature algorithm

Nimiq uses [Ed25519](https://ed25519.cr.yp.to/) for transaction signing.

## Hashing algorithm
Nimiq uses [Blake2b](https://en.wikipedia.org/wiki/BLAKE_%28hash_function%29#Blake2b_algorithm) as hashing algorithm.

## Proof of Work algorithm

Nimiq uses the [memory-hard](https://en.wikipedia.org/wiki/Memory_bound_function) [Argon2d](https://en.wikipedia.org/wiki/Argon2) as its Proof of Work algorithm.