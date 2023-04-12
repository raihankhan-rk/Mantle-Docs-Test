# Data Availability - Mantle Tech Docs

Data availability is an essential topic for understanding Layer 2 (L2) scaling. In this section, we intuitively explain what data availability is, and why it's needed for L2s to operate.

One of the foundational pillars of blockchain design is _trustlessness_. When we say "trustless", we mean that a user on a blockchain should be able to accomplish their tasks _without placing unnecessary trust_ in third-parties.

This explains why blockchains use cryptography. Instead of relying on soft agreements and social contracts with other network participants (aka trust), blockchains use hard rules and code to enforce expected behavior.

Minimizing Trust in Practice

[](#minimizing-trust-in-practice)

---

What this means **practically** is that blockchains are designed so that any user can verify whether a network participant has followed the rules.

On a network like Ethereum, this means a certain reliance on **full nodes.** Full nodes will publicly store the entire history of blockchain data so that all other network participants can verify the state transition of the chain was correct.

In the case of Mantle, transactions are submitted and executed on the L2 chain. This means that Mantle nodes need access to the L2 transaction data in order to verify the state transition. This verification is enabled by having the Sequencer **publish** the full transaction data and cryptographic evidence of the state transition (a Merkle Proof) on Ethereum.

By "data availability" we are referring to the ability for any node in the Mantle network to access the transaction history posted by the Sequencer. With this information alone, any node can submit a fraud proof and thereby verify whether blocks are being produced honestly.

So the challenge of maintaining a _trustless_ L2 network is distilled into ensuring that transaction data remains available!

It turns out that guaranteeing data availability is a challenge in its own right. To understand how this is handled, you can read more about the data availability problem and erasure coding

[here](https://github.com/ethereum/research/wiki/A-note-on-data-availability-and-erasure-coding)

.
