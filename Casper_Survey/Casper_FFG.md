# Casper the Friendly Finality Gadget
- A mechanism which proposes blocks.
- Casper protects against finalizing two conflicting checkpoints, but <u> the attackers could prevent Casper from finalizing any future checkpoints </u>.

> 畫底線的情況為什麼會發生？

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
![](/Users/nobel/Desktop/螢幕快照 2018-08-02 17.18.02.png)

### Some Definitions
- checkpoint:
	- Genesis block(root)
	- every block whose height in the block tree (or block number) is an exact multiple of 100
- checkpoint height k:
	- k = block number of checkpoint / 100
- height h(c) of a checkpoint c:
	- the number of elements in the checkpoint chain stretching from c all the way back to root along the parent links <br>

> 在這裡 h(c) = k

![](/Users/nobel/Desktop/螢幕快照 2018-08-02 17.19.25.png)

