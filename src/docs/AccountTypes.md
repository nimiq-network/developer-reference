# Accounts

Accounts in Nimiq are used primarily to store the balance of a given address, but they also store other useful related metadata, such as the last nonce that was used in a transaction from that address (for replay protection purposes) or its vesting parameters, depending on its type.

There are two account types:
  * [Basic](#basic-account-type)
  * [Vesting](#vesting-account-type)

## Basic account type

The basic account type is used to store funds that can be transferred at any time. This is going to be the most used account type in the network.


## Vesting account type

The vesting account type is used to store funds with some time restriction, which means that they can only be transferred after a certain point in time (known as the "vesting" date). This type of account is aimed primarily for early investors and team members.