# [Tendermint](https://tendermint.com)

*[TX]:Transaction
*[BFT]:Byzantine Fault Tolerance
*[BC]:Blockchain
*[ABCI]:Application BlockChain Interface
*[EVM]:Ethereum Virtual Machine

## What is Tendermint?

- A software for security.

- Aim to guarantee every non-faulty machine sees the same TX log and computes the same state.

- Traits
	- Consistently replicating an application on many machines.
	- BFT: Tolerating up to 1/3 failure rate, including Byzantine failures.

- Two chief components
	- BC consensus engine: Tendermine Core
	- Generic application interface: ABCI

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
	
	- Does not specify a particular application (like a fancy key-value store? LOL)

#### Cryptocurrencies
- Tendermint originally had a currency built in, but then evolved to be a general purpose BC consensus engine.
- The famous [Cosmos Network](http://cosmos.network/) is also built on Tendermint.

#### Other Blockchain Projects
-  	[Fabric](https://github.com/hyperledger/fabric) takes a similar approach to Tendermint, but is more opinionated about how the state is managed.
- [Burrow](https://github.com/hyperledger/burrow) is an implementation of the EVM and Ethereum TX mechanics, with additional features for a name-registry, permissions, and native contracts, and an alternative BC API.

### ABCI Overview
