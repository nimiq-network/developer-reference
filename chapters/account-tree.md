# Accounts Tree
A tree storing addresses and account (i.e. balances and further fields).
This tree's root is stored in each [block's header](block.md#header).
The account tree can be used to verify that an account in a certain state was part of a certain block.
And account tree is composed of [account tree nodes](#account-tree-node) using Ethereum's [Merkle Patricia Trie Specification](https://github.com/ethereum/wiki/wiki/Patricia-Tree).

## Accounts and Transactions

Rules:
* It forbidden to spend funds received in the same block
* Transaction ordering: in general block order, but currently ignored because it is irrelevant when one can only spend funds one has received before the current block already.

### Adding Accounts to the Accounts Tree

Procedure:
1. subtract from all sender accounts
2. add to all recipient accounts
3. create contract accounts
4. prune contract accounts (must be pruned when empty)

Position in tree is unrelated to position in block body.

## Accounts Tree Nodes
An accounts tree node can be of two types: branch or terminal.
Branch nodes build the tree structure while the terminal nodes are the leaves of the tree.

### Accounts Tree Node Branch
The tree structure is composed of prefixes to decend and find the correct account in a terminal node.
A branch is composed of a prefix and children:

| Element       | Data type    | Bytes  | Description                                                        |
|---------------|--------------|--------|--------------------------------------------------------------------|
| type          | uint8        | 1      | `0`                                                                |
| prefix length | uint8        | 1      |                                                                    |
| prefix        | string       | length | Common part of the hashes of all accounts on this side of the tree |
| child count   | uint8        | 1      | Number of immediate tree sub nodes                                 |

And then for each child a structure as follows is attached:

| Element             | Data type    | Bytes  | Description                               |
|---------------------|--------------|--------|-------------------------------------------|
| child suffix length | uint8        | 1      |                                           |
| child suffix        | string       | length | Parent prefix + suffix compose new prefix |
| child hash          | Hash         | 32     | Hash of part of the tree below            |

### Accounts Tree Node Terminal
The terminal node is the leave node of the account tree, it contains the actual account.

| Element       | Data type    | Bytes  | Description                         |
|---------------|--------------|--------|-------------------------------------|
| type          | uint8        | 1      | `255`                               |
| prefix length | uint8        | 1      | `20`                                |
| prefix        | string       | length | The address of the account          |
| account       | Account      | >= 9   | The actual account, can be any kind |


