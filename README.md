# Nimiq Blockchain
[Nimiq](https://nimiq.com/) is a frictionless payment protocol for the web. For a high-level introduction please read the [Nimiq White Paper](https://medium.com/nimiq-network/nimiq-a-peer-to-peer-payment-protocol-native-to-the-web-ffd324bb084). The source code is available on [GitHub](https://github.com/nimiq-network/core).

This developer reference contains:

* [Data Schemas](#data-schemas)
* [High Level Concepts and Architecture](#high-level-concepts)

## Data Schemas

Data schemas as used on the wire, focusing on fields, bytes sizes and short explanations.

* [Transactions](chapters/transactions.md): basic and extended
* [Blockchain](chapters/block.md): block, header, interlink, body
* [Accounts and Contracts](chapters/accounts-and-contracts.md): basic account, vesting and hashed time-locked contracts
* [Accounts Tree](chapters/account-tree.md): details on the PM tree used to store accounts
* [Primitives](chapters/primitives.md): none-composed elements, e.g. hashes, addresses
* [Messages](chapters/messages.md): All p2p-related inter-node messages

## High Level Concepts

High level concepts used in and underlying Nimiq.

* [Constants](chapters/constants.md): Configuration of the Nimiq core
* [Nodes and Clients](chapters/nodes-and-clients.md): Node.js and browser, full, small, and nano
* [Supply and Reward](chapters/supply-and-reward.md): total supply, rewards, difficulty adjustment
* [Verification](chapters/verify.md): Validation rules for the Nimiq Blockchain

## Further Resources
Further resources to get an overview of the Nimiq project and ecosystem:
* [API Documentation](https://github.com/nimiq-network/core/blob/master/dist/API_DOCUMENTATION.md)
* [Browser Miner](https://nimiq.com/miner) and [Browser Wallet](https://nimiq.com/wallet)
* [Contributing Guidelines](https://github.com/nimiq-network/core/blob/master/.github/CONTRIBUTING.md) and [Code of Conduct](https://github.com/nimiq-network/core/blob/master/.github/CODE_OF_CONDUCT.md)
* This project is released under the [Apache License 2.0](https://github.com/nimiq-network/core/blob/master/LICENSE.md)


We believe in communicating our concepts and approaches as clearly as possible. The easier it is to dive into the details, the more people will do it, again resulting in a deeper peer-reviewing process which is essential for the hardening of our protocol. 
You are very welcome to improve this reference via pull requests.
