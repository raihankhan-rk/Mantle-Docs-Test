# Optimistic Rollups - Mantle Tech Docs
### 

**What are** Optimistic Rollups?

[](#what-are-optimistic-rollups)

Optimistic Rollups (ORs) are Layer 2 (L2) protocols designed to increase the throughput of Ethereum. They reduce computation load on the main Ethereum chain by taking computation and state storage off-chain and processing transactions in batches.

There are a few different scaling solutions currently in use by various projects, such as:

*   **Sidechains:** Independent blockchains that are linked to a main chain, but maintain block data separately and use their own consensus models. For example, Polygon.
    

*   **Plasma chains:** Analogous to child chains that also maintain their own block data validation process, except they're non-custodial in nature from the perspective of assets, which means users can exit with their funds in case of an anomaly.
    

Unlike the solutions stated above, ORs derive security from the Ethereum mainnet by publishing transaction results on-chain.

ORs are considered “_optimistic_” because they assume off-chain transactions are valid and don't publish proofs of validity for transaction batches posted on-chain. But how do they detect and deal with invalid states then? This is where Fraud Proofs come in.

Network actors submit fraud proofs if they spot invalid transactions. Smart contracts on the Ethereum mainnet then execute the individual transactions (or an entire block if necessary) to check their validity. If confirmed to be invalid, the batch submitter (the _Sequencer_) gets their stake (deposited as bond at the time of batch submission) slashed, setting a disincentive in place to discourage invalid submissions.

**For more details, we recommend reading:**
[Optimism's Introduction to Optimistic Rollups.](https://medium.com/privacy-scaling-explorations/an-introduction-to-optimisms-optimistic-rollup-8450f22629e8)