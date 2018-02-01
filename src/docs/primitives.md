# Public Key
32 bytes Uint8Array, derived using the ED25519 algorithm.

# Signature
64 bytes Uint8Array, created from private and public key and a data component using ED25519.

# Address
Serialized in 20 bytes compressed and created by keeping the first 20 bytes of a hash, e.g. of a public key.

# Hash
A hash can be of three types, but only two are used: Blake2b and Argon2d. 
When creating a light hash, Blake2b is used while when creating a hard hash, Argon2d is being used.
Indepenendtly, a hash is aways 32 bytes long.
