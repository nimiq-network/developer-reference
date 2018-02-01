# Overview
A Nimiq block can exist in two modes: full and light. A light block is equal to a full block, but does not have a body.
A Nimiq block can be at most 1MB (1 million bytes) maximum and is composed of (body optional)

Element      | Header | Interlink | Full/Light Switch | Body
------------ | ------ | --------- | ----------------- | ---------------------------
Size [bytes] | 146    | <= 1053   | 0 or 1            | >= 117 <if switch is 1>

# Header
The header has total size of 146 bytes and is composed of

Element      | version | previous | interlink | body | accounts | nBits  | height | timestamp | nonce
------------ | ------- | -------- | --------- | -----| -------- | ------ | ------ | --------- | ------
Data type    | Uint16  | Hash     | Hash      | Hash | Hash     | Uint32 | Uint32 | Uint32    | Uint32
Size [bytes] | 2       | 32       | 32        | 32   | 32       | 4      | 4      | 4         | 4     

At main net launch time, version will be "1" and hashes in the header will be based on Blake2b.

# Interlink
An interlink is composed of 

Element      | count | repeat bits   | hashes      
------------ | ----- | ------------- | ------------ 
Data type    | Uint8 | Uint8Array    | [Hash]       
Size [bytes] | 1     | ceil(count/8) | <= count * 4 

Repeat bits is a bit mask corresponding to the list of hashes, 
a 1-bit indicating that the hash at that particular index is the same as the previous one, 
i.e. repeated, and thus the hash will not be stored in the hash list again to reduce size. 
As each hash is represented by one bit the size is ceil(count/8).

Hashes are a list of up to 255 block hashes of 4 bytes each.

Thus, an interlink can be up to 1+ceil(255/8)+255*4 = 1053 bytes.

# Body
The body part is 117 bytes plus data and transactions. The maximum block size is 1MB.

Element      | Miner address | extra data length | extra data        | transaction count | transactions  | prunded accounts count | prunded accounts 
------------ | ------------- | ----------------- | ----------------- | ----------------- | ------------- | ---------------------- | ---------------- 
Data type    | Address       | Uint8             | Uint8Array        | Uint16            | [Transaction] | Uint16                 | Address+Account
Size [bytes] | 20            | 1                 | extra data length | 2                 | ~150 each     | 2                      | 20+8+64 = 92
 
Transactions can be [basic or extended](./transactions).
Basic uses 138 bytes, extended more than 68 bytes.
Transactions need to be in block order (first recipient, then validityStartHeight, fee, value, sender).
Prunded accounts by their address.
