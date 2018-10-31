# Supply and Rewards

## Total and Circulating Supply

The **total supply** of NIM is 21 billion (21e9 NIM, inspired by Bitcoin but increased by a factor of 1000). While originally named a satoshi, the smallest unit of NIM was was renamed to Luna in order to prevent confusion.  There are 100,000 (1e5) lunas in 1 NIM, which makes the total supply of NIM equivalent to 21e14 luna (exactly like Bitcoin).

The Mainnet's **initial supply** will be 12% of the total supply (2.52 billion NIM) which will be included in the Genesis Block and corresponds to the NIM redeemed by NET holders during NET-to-NIM conversion.

After launch the **circulating supply** will increase in a curved fashion following our Dynamic Block Reward scheme.

## Dynamic Block Reward

To avoid the inestability caused by the modification breakpoints in a halving scheme like Bitcoin's, Nimiq uses a **dynamic block reward**. The dynamic block reward ensures a smooth decay in the emission rate. It is inspired by [Monero](https://github.com/monero-project/research-lab/blob/master/whitepaper/whitepaper.pdf) & [Cryptonote](https://cryptonote.org/whitepaper.pdf). They base their reward in the following formula.

	blockReward = (totalSupply - circulatingSupply) >>> k

We have taken a similar approach but we selected a value of `k=22` and, at a certain height (Block 48692960), switching to constant block reward of 4,000 lunas (*tail emission*) until the total supply of 21e14 lunas is reached. This has the following beneficial side effects:

 - At block 48692959 the dynamic block reward will be 4,001 lunas, causing a **fluid transition to tail emission reward** of 4,000 lunas.
 - The circulating supply at block 48692959 will be 2,099,983,215,900,000 lunas, which is **divisible by 4,000**.
 - The last block governed by dynamic block reward will be reached in aproximately 92 years.
 - Block 48692960 will mark the beggining of tail emission. Remaining supply at this point will be 16,784,100,000 lunas which results in **~7 years of a constant 4,000 reward**.
 - Total supply of 21e14 will be reached at block 52888984 (~100 years).

## Difficulty Adjustment

The difficulty for every block is calculated according to the time it took to mine the last 120 blocks. To account for sudden changes in the global hashing power of the network, a minimum/maximum adjustment rate of 2 limits the variation of the difficulty. The targeted block time is 60s.
