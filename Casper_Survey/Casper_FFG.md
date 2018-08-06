[Hackmd version](https://hackmd.io/aIOcrjUASFumSa8aH-ZqJQ?view)
# Casper the Friendly Finality Gadget

- A mechanism which proposes blocks.
- Casper protects against finalizing two conflicting checkpoints, but **_the attackers could prevent Casper from finalizing any future checkpoints_**.

> 標記粗斜體的情況為什麼會發生？

## Casper 有的 BFT 沒有的：
- **Accountability**: 
	- If a validator violates a rule, we can detect the violation and know which validator violated the rule. 
		- solves "Nothing at stake" problem
	- The penalty for violating a rule is a validator’s entire deposit.
	- Security of PoS is based on the size of the penalty.
		- The penalty should be greater than the gains from the mining reward.
		- stronger security incentives
- **Dynamic validators**:
	- validators change overtime
- **Defenses**:
	- long range revision attacks 
	- more than 1/3 of validators drop offline
	- cost: very weak tradeoff synchronicity assumption
- **Modular overlay**:
	- Casper’s design as an overlay makes it easier to implement as an upgrade to an existing proof of work chain.

## The Casper Protocol
- Assume a fixed set of validators and a proposal mechanism.
	- produces child blocks of existing blocks
	- ever-growing blocktree
- 理論上希望： propose blocks one after the other in a linked list
- 現實中：In the case of network latency or deliberate attacks, the proposal mechanism will inevitably occassionally produce multiple children of the same parent.
- Caspers job is to choose a single child from each parent, thus choosing one canonical chain from the block tree.
- 為了效率：Casper only considers the subtree of checkpoints forming the checkpoint tree.
![](https://i.imgur.com/qfSgL5i.png)


### Some Definitions
- **checkpoint**:
	- Genesis block(root)
	- every block whose height in the block tree (or block number) is an exact multiple of 100
- checkpoint height k:
	- k = block number of checkpoint / 100
- height h\(c\) of a checkpoint c:
	- the number of elements in the checkpoint chain stretching from c all the way back to root along the parent links <br>

> 在這裡 h\(c\) = k

![](https://i.imgur.com/3pxZYJb.png)

- Security of Pos derives from the size of the deposits, not the number of validators.
	- "2/3 of validators": deposit weight fraction, that is, a set of validators whose sum deposit size equals to 2/3 of the total deposit size of the entire set of validators.
	- 簡單來說，是總錢的2/3，不是總票數2/3
- vote message 包含了：
	- two checkpoints s and t
	- h(s), h(t)
- **Invalid 的狀況**：
	- s isn't an ancestor of t
	- public key of validator v isn't in the validator set

![](https://imgur.com/6NsF4C0.png)

- **supermajority link**: 
	- an ordered pair of checkpoints (a,b), also written a → b, s.t. at least 2/3 of validators (by deposit) have published votes with source a and target b.
	- can skip checkpoints, i.e., h(b) > h(a) + 1 is fine

![](https://imgur.com/Oy4uCly.png)

- **conflicting**: Two checkpoints a and b are called conflicting iff. they are nodes in different branches, i.e., neither is an ancestor or descendant of the other.
- **justified**: A checkpoint c is justified if:
	1. it is the root
	2. there exists a supermajority link c' → c where c' is justified
	
	- Figure (c) shows a chain of four justified blocks. 
- **finalize**: A checkpoint c is called finalized if it is justified and there is a supermajority link c → c' where c' is a direct child of c.

![](https://imgur.com/87TxdJF.png)

- Impossible for two conflicting checkpoints to be finalized without ≥ 1/3 of the validators violating one of the two Casper Commandments/slashing conditions.(Figure 2)
- 違反 slashing condition的證據會被當成交易上鏈，押金會被沒收且一小部分會被拿來當作發現者/舉報者的獎勵
	- 要阻止押金被拿走必須要51%攻擊

### Proving Safety and Plausible Liveness
- Casper's two fundamental properties:
	- accountable safety: conflicting checkpoints cannot both be finalized unless ≥ 1/3 of validators violate a slashing condition (也就是說總押金會失去1/3)
	- plausible liveness: regardless of any previous events (e.g., slashing events, delayed blocks, censorship attacks, etc.), if ≥ 2/3 of validators follow the protocol, then it’s always possible to finalize a new checkpoint without any validator violating a slashing condition.

![](https://imgur.com/uWgH0AL.png)

From these two properties, we can immediately see that, for any height n:

![](https://imgur.com/BMbw0c4.png)

![](https://imgur.com/UEum9nH.png)

![](https://imgur.com/noPdcBS.png)

> 這是違反II嗎？ <br>
> 附上原文：
> ![](https://i.imgur.com/Au16OOX.png)

![](https://i.imgur.com/RbINBYI.png)

![](https://imgur.com/gzCZHIg.png)

### Casper's Fork Choice Rule
- "FOLLOW THE CHAIN CONTAINING THE JUSTIFIED CHECKPOINT OF THE GREATEST HEIGHT" instead of following the standard PoW fork-choice rule of “always build atop the longest chain” 

## Enabling Dynamic Validator Sets
- **dynasty**: The dynasty of block b is the number of finalized checkpoints in the chain from root to the parent of block b.
- When a would-be validator’s deposit message is included in a block with dynasty d, then the validator ν will join the validator set at first block with dynasty d + 2. We call d + 2 this validator’s start dynasty, DS(ν).
- To leave the validator set, a validator must send a “withdraw” message. If validator ν’s withdraw message is included in a block with dynasty d, it similarly leaves the validator set at the first block with dynasty d + 2; we call d + 2 the validator’s end dynasty, DE(ν).
- If a withdraw message has not yet been included, then DE(ν) = ∞.
- Once validator ν leaves the validator set, the validator’s public key is forever forbidden from rejoining the validator set. This removes the need to handle multiple start/end dynasties for a single identifier.
- At the start of the end dynasty, the validator’s deposit is locked for a long period of time, called the withdrawal delay (think “four months’ worth of blocks”), before the deposit is withdrawn. If, during the withdrawal delay, the validator violates any commandment, the deposit is slashed.

![](https://i.imgur.com/0pV1v0U.png)

- Note that in order for the chain to be able to “know” its own current dynasty, we need to restrict our definition of “finalization” slightly:

![](https://imgur.com/kmxwIhH.png)

![](https://i.imgur.com/Eoy6NjX.png)

![](https://imgur.com/QBQVeAD.png)

- The forward and rear validator sets will usually greatly overlap; but if the two validator sets substantially differ, this “stitching” mechanism prevents safety failure in the case when two grandchildren of a finalized checkpoint have different dynasties because the evidence was included in one chain but not the other. For an example of this, see Figure 4.

