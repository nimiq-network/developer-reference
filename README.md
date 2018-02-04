# Nimiq Blockchain [![Build Status](https://travis-ci.org/nimiq-network/core.svg)](https://travis-ci.org/nimiq-network/core)
[Nimiq](https://nimiq.com/) is a frictionless payment protocol for the web.

This technical documentation contains:

* [Data schemas](#data-schema)
* [High level concepts and architecture](#higher-level-concepts)
* [Examples](#examples)

For a high-level introduction please read the [Nimiq White Paper](https://medium.com/nimiq-network/nimiq-a-peer-to-peer-payment-protocol-native-to-the-web-ffd324bb084).

The source code is available on [GitHub](https://github.com/nimiq-network/core).

# Data schemas

Data schemas as used on the wire, focusing on fields, bytes sizes and short explanations.

* [Transactions](chapters/transactions.md): basic and extended
* [Blockchain](chapters/block.md): block, header, interlink, body
* [Accounts and Contracts](chapters/accounts-and-contracts.md): basic account, vesting and hashed time-locked contracts
* [Account tree](chapters/account-tree.md): details on the PM tree used to store accounts
* [Primitives](chapters/primitives.md): none-composed elements, e.g. hashes, addresses
* [Messages](chapters/messages.md): All p2p-related inter-node messages

# Higher level concepts

Higher level concepts used in and underlying Nimiq.

* [Constants](chapters/constants.md): Configuration of the NIMIQ core
* [Nodes and clients](chapters/nodes-and-clients.md): Node.js and browser, full, small, and nano.
* [Supply and reward](chapters/supply-and-reward.md): total supply, rewards, difficulty adjustment

# Examples

Check out our testnet [Browser Miner](https://nimiq.com/miner) and [Wallet](https://nimiq.com/wallet).

Read the [API Documentation](https://github.com/nimiq-network/core/blob/master/dist/API_DOCUMENTATION.md).

# Contribute

If you'd like to contribute to the development of Nimiq please follow our [Code of Conduct](https://github.com/nimiq-network/core/blob/master/.github/CODE_OF_CONDUCT.md) and [Contributing Guidelines](https://github.com/nimiq-network/core/blob/master/.github/CONTRIBUTING.md).

# License

This project is under the [Apache License 2.0](https://github.com/nimiq-network/core/blob/master/LICENSE.md).
