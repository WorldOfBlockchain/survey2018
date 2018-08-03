# [Tendermint](https://tendermint.com)

## What is Tendermint?

- A software for security.

- Aim to guarantee every non-faulty machine sees the same TX log and computes the same state.

- Traits
	- Consistently replicating an application on many machines.
	- BFT: Tolerating up to 1/3 failure rate, including Byzantine failures.

- Two chief components
	- BC consensus engine: Tendermine Core
	- Generic application interface: Application BlockChain Interface (ABCI)

### Tendermind vs. X

- Broadly similar to two classes of software.
	- Distributed key-value stores using non-BFT consensus.
	- BC technology, consists of both cryptocurrency and alternative distributed ledger.

#### Distributed key-value stores
- Example: Zookeeper, etcd, consul
	- Zookeeper: using a version of [Paxos](https://en.wikipedia.org/wiki/Paxos_(computer_science)) (Graphic explanation on work)
	- etc and consul: using [Raft](https://en.wikipedia.org/wiki/Raft_(computer_science))

- Can tolerate to 1/2 crashing-type failure rate, but one Byzantine fault can destroy the system.

- Differences:
	- Only tolerate up to 1/3 failure rate.
			But that "failure" includes arbitrary behavior (including hacking and attacks).
	
	- Does not specify a particular application (like a fancy key-value store?)

#### Cryptocurrencies
- Tendermint originally had a currency built in, but then evolved to be a general purpose BC consensus engine.
- The famous [Cosmos Network](http://cosmos.network/) is also built on Tendermint.

#### Other Blockchain Projects
-  	[Fabric](https://github.com/hyperledger/fabric) takes a similar approach to Tendermint, but is more opinionated about how the state is managed.
- [Burrow](https://github.com/hyperledger/burrow) is an implementation of the EVM and Ethereum TX mechanics, with additional features for a name-registry, permissions, and native contracts, and an alternative BC API.

### ABCI Overview

-  Allows BFT replication of applications written in any programming language.

#### Motivation

( ... skipped some... )

## Consensus Overview

- All participants are called "**Validators**", taking turns proposing blocks of TXs and voting.

- Blocks are committed in a chain, with one block at each **height**.

- If a block fail to be committed, the protocol moves to the next round, and a new validator gets to propose a block for that height.

- When more than 2/3 of the validator pre-vote for the same block, we call that a **polka**.
	(Because validators are doing something like a polka dance.)

- Successful block commission need 2 stages of voting: **pre-vote** and **pre-commit**
	- A block is committed when more than 2/3 of validator pre-commit for the same block in the same round.

- Validator may fail to commit a block for a number of reason:
	- Offline proposer
	- Slow network connection.
	- etc.
		which are all allowed under Tedermint.

- Validators wait a small amount of time to receive a complete proposal block from the proposer before voting.

- All validators only make progress after hearing from more than two-thirds of the validator set.

- Tendermint uses the same mechanism to commit a block as it does to skip.

- Given less than 1/3 of the validators are Byzantine, Tendermint guarantees that validators will never comiit conflicting blocks at the same height.

- Once a validator precommits a block, it is *lock* on that block. Then,
	1. it must prevote for the block it is locked on
	2. it can only unlock, and precommite for a new block, if there is a polka for that block in a later round.
