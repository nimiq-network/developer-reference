# Accounts

Accounts in Nimiq are used primarily to store the balance of a given address, but they also store other useful related metadata, such as the account type, its vesting parameters (for vesting accounts), or the root hash and timeout parameters (for a hashed time locked contract accounts).

There are three account types:
  * [Basic](#basic-account-type)
  * [Vesting](#vesting-account-type)
  * [Hashed Time Locked Contract](#hashed-time-locked-contract-account-type)

## Basic account type

The basic account type is used to store funds that can be transferred at any time. This is going to be the most used account type in the network.


## Vesting account type

The vesting account type is used to store funds with some time restriction, which means that they can only be transferred after a certain point in time (known as the "vesting" date). This type of account is aimed primarily for early investors and team members.

## Hashed Time Locked Contract account type

The [HTLC](https://en.bitcoin.it/wiki/Hashed_Timelock_Contracts) account type is used to store funds that can only be transferred if certain conditions (f.e. certain timeout is reached or certain data is presented) are met. This type of account will be used to enable off-chain payment methods (such as Lightning Network) and [atomic swaps](https://en.wikipedia.org/wiki/Atomic_swap) between Nimiq and other blockchains.
