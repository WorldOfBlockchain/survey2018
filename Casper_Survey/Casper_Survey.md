# Casper Survey
> [name=Gerber]

[TOC]

---
## Problem of PoS -- Nothing at Stake
![](https://i.imgur.com/r8hMYgA.png)
- 如果我是validator，我就在紅鏈跟藍鏈上面都放stake
- always win and have nothing to lose, despite how malicious the actions maybe
- Casper 就是要使用PoS但是又要避免這個問題

## PoS under Casper
- The validators stake a portion of their Ethers as stake.
- When validators discover a block which they think can be added to the chain, they will validate it by placing a bet on it.
- If the block gets appended, then the validators will get a reward proportionate to their bets.
- However, if a validator acts in a malicious manner and tries to do a “nothing at stake”, they will immediately be reprimanded, and all of their stake is going to get slashed.
- 亂離線的validator也會被處罰
- 誠實的validator在藍鏈上會拿到獎勵，惡意的validator的stake則會因為下在紅鏈上而被沒收

## Casper = FFG + CBC
### Casper the Friendly Finality Gadget (FFG)
- There is a proof-of-stake protocol overlaying on top of the normal ethash proof of work protocol.
- While blocks are still going to be mined via POW, every 50th block is going to be a POS checkpoint where finality is assessed by a network of validators.
#### finality
- Once a particular operation has been done, it will forever be etched in history and nothing can revert that operation
- Casper is guaranteed to provide stronger finality than proof-of-work because of three reasons:
    - $\frac{2}{3}$ of validators make maximum odd bets to finalize the blocks. There is very little incentive for them to collude and attack the network since they are jeopardizing their own deposits if they do so.
    - The fact that validators are pre-registered means that there is no possibility that somewhere else out there there are some other validators making the equivalent of a longer chain. If you see 2/3 of validators placing their entire stakes behind a claim, then if you see somewhere else 2/3 of validators placing their entire stakes behind a contradictory claim, that necessarily implies that the intersection (ie. at least 1/3 of validators) will now lose their entire deposits no matter what happens. This is what we mean by "economic finality": we can't guarantee that "X will never be reverted", but we can guarantee the slightly weaker claim that "either X will never be reverted or a large group of validators will voluntarily destroy millions of dollars of their own capital". 
    :::info
    ***有待討論...***
    我的解讀是說有$\frac{2}{3}$的人押藍色，$\frac{2}{3}$的人押紅色，這意味著這兩批人最少有$\frac{1}{3}$的人(交集)兩個都有押，勢必損失stake。
    :::
    - Even if a double-finality event does take place, users are not forced to accept the claim that has more stake behind it; instead, users will be able to manually choose which fork to follow along, and are certainly able to simply choose "the one that came first". A successful attack in Casper looks more like a hard-fork than a reversion, and the user community around an on-chain asset is quite free to simply apply common sense to determine which fork was not an attack and actually represents the result of the transactions that were originally agreed upon as finalized.
    :::info
    ***我的一些疑問...***
    為何會發生double-finality？
    
    Definition for [hard-fork](https://www.investopedia.com/terms/h/hard-fork.asp):
    A hard fork (or sometimes hardfork), as it relates to blockchain technology, is a radical change to the protocol that makes previously invalid blocks/transactions valid (or vice-versa). This requires all nodes or users to upgrade to the latest version of the protocol software. Put differently, a hard fork is a permanent divergence from the previous version of the blockchain, and nodes running previous versions will no longer be accepted by the newest version. This essentially creates a fork in the blockchain: one path follows the new, upgraded blockchain, and the other path continues along the old path. Generally, after a short period of time, those on the old chain will realize that their version of the blockchain is outdated or irrelevant and quickly upgrade to the latest version.
    :::