---
category: "Data schemas"
---

# Account tree
A tree storing addresses and account (i.e. balances and further fields).
This tree's root is stored in each [block's header](block.md#header).
The account tree can be used to verify that an account in a certain state was part of a certain block.
And account tree is composed of [account tree nodes](#account-tree-node) using the standard [Merkle Patricia Trie Specification](https://github.com/ethereum/wiki/wiki/Patricia-Tree).

## Applying blocks to AccountsTree
* It forbidden to spend funds received in the same block
* Transaction ordering: in general block order, currently ignored - it's irrelevant because you can only spend funds you received before this block

Procedure:
1. subtract from all sender accounts
2. add to all recipient accounts
3. create contract accounts
4. prune contract accounts (must be pruned when empty)

Position in tree is unrelated to position in block body

## Account tree node
And account tree node can be of two types: branch or terminal.
Branch node build the tree structure while the terminal nodes are the leaves of the tree.

### Account tree node branch
The tree structure is composed of prefixes to decend efficiently and find the correct account in a terminal node.
A branch is composed of a prefix and children:

| Element       | Data type    | size in bytes | Description                                                        |
|---------------|--------------|---------------|--------------------------------------------------------------------|
| type          | unsigned int | 1             | `0`                                                                |
| prefix length | unsigned int | 1             |                                                                    |
| prefix        | string       | length        | Common part of the hashes of all accounts on this side of the tree |
| child count   | unsigned int | 1             | Number of immediate tree sub nodes                                 |

And then for each child as structure as follows:

| Element             | Data type    | size in bytes | Description                               |
|---------------------|--------------|---------------|-------------------------------------------|
| child suffix length | unsigned int | 1             |                                           |
| child suffix        | string       | length        | Parent prefix + suffix compose new prefix |
| child hash          | Hash         | 32            | Hash of part of the tree below            |

### Account tree node terminal
The terminal node is the leave node of the account tree, it contains the actual account.

| Element       | Data type    | size in bytes | Description                         |
|---------------|--------------|---------------|-------------------------------------|
| type          | unsigned int | 1             | `255`                               |
| prefix length | unsigned int | 1             | `20`                                |
| prefix        | string       | length        | The address of the account          |
| account       | Account      | >= 9          | The actual account, can be any kind |

