# A Gentle Introduction - Mantle Tech Docs
**Q: Hey, cool website! What’s Mantle?**

Mantle is a technology stack for scaling Ethereum, and we strive to be EVM-compatible while doing so. Being EVM-compatible means all the contracts and tools that work on Ethereum also work with Mantle with minimal modifications. Mantle users can experiment with exciting web3 apps, and developers can deploy smart contracts in an efficient, low-fee environment.

At its core, Mantle has been built with a modular architecture that uses an optimistic rollup with an innovative data availability solution. This allows Mantle to inherit security from Ethereum AND offer cheaper and more accessible data availability.

**Q: Modular blockchain? Optimistic rollups!? I'm feeling lost...**

If these terms are unfamiliar, we encourage you to visit [Optimistic Rollups](https://docs.mantle.xyz/introducing-mantle/mantle-core-concepts/optimistic-rollups) and the pages that follow where we explain blockchain, rollups, and Mantle's key innovations in a more approachable way.

On the other hand, if these terms make you excited for the future of blockchain scaling we encourage you to keep reading!

**Q: What's the difference between Mantle and Ethereum?**

Mantle is a Layer 2 scalability solution built on top of Ethereum. The validator nodes in the Mantle network collect transactions from users and commit them to Ethereum in the form of a "compressed block". This compression saves users expensive gas fees and increases the total possible throughput for transactions.

By building on top of Ethereum, Mantle inherits desirable features like industry-leading security and common developer infrastructure.

**Q: How is Mantle different from other L2 solutions out there?**

Mantle offers a number of improvements over traditional L2 solutions:

*   1.
    
    **Modular Data Availability:** Through the use of
    
    [EigenLayer](https://www.eigenlayer.com/)
    
    , Mantle is able to increase transaction throughput beyond traditional L2s. Accessible data availability also means Mantle can increase throughput without compromising on security and reducing the overhead of node operations. This decreases the processing requirement for validators and promotes increased decentralization.
    

*   2.
    
    **Multi-Party Computation (MPC):** Mantle uses MPC to minimize the trust risk of L2 execution results. Using
    
    [Threshold Signature Scheme](https://docs.mantle.xyz/for-validators/multi-party-computation)
    
    (TSS) technology, specialized nodes contribute to multi-party signatures that improve the correctness of off-chain transaction execution results. This allows us to shorten the challenge period for withdrawals.
    

*   3.
    
    **Decentralized Sequencer**: Mantle will decentralize its Sequencer to offer secure and trustless block production. By rotating through a permissionless sequencer set, Mantle will reduce the potential for a single point of failure or censorship on the network. Mantle will over time, decentralize its Sequencer to offer secure and trustless block production.
    

**Q: I'm sold! How can I get started?**

Mantle is developed in partnership with BitDAO, Bybit, and other organizations to meet the needs of the growing **$BIT** ecosystem.