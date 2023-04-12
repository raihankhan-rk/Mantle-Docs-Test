# Decentralized Sequencer Architecture - Mantle Tech Docs

This standard document specifies the decentralized sequencers, which includes the selection mechanism of the sequencer set and the consensus algorithm for the sequencer set to produce L2 blocks.

At present, L2 rollups almost all run the sole sequencer. Although the sequencer cannot censor the user's transactions, it is still desirable to decentralize the sequencer. There are two main goals here:

- 1.

  Anyone can participate in the sequencer set as an L2 block producer.

- 2.

  Reduce MEV, each sequencer participating in the consensus process is able to determine transaction ordering.

To hit the first goal, there is no additional work on L2, we just need to implement a leader election mechanism on L1. Proof of Stake (PoS) is a good option.

For the second goal, we have to additionally implement a BFT consensus mechanism on L2. Two mainstream BFT algorithm implementations are **Deepest Branch Merge** and **PBFT**. Under the premise of the same number of nodes, PBFT has faster finality but the performance is difficult to meet L2 requirements. Conversely, Deepest Branch Merge has higher performance but slower finality.

- POA: A block can be confirmed until at least N/2+1 blocks are produced, where N denotes the number of nodes.

- POS: There is a need to wait for nodes with > 1/2 staked tokens to finish producing blocks.

We try to strike a balance between finality and performance and try to introduce a centralized scheduler to decide which sequencer to produce blocks in the next epoch.

The scheduler and the sequencers maintain a heartbeat to judge the activity of the sequencers and decide the producer of blocks in the next epoch. The sequencer produces a block and transfers it to the scheduler, which then broadcasts it to other nodes. If the sequencer does not transfer the block to the scheduler on time, the scheduler will choose another sequencer as the next block producer.

There are two roles in the L2 network: scheduler and sequencer.

- **Scheduler:** At any moment, there is only one node that participates in L2 network as a scheduler. The scheduler is responsible for appointing a leader from the sequencer set according to a certain leadership election mechanism, and the duty of the leader will be defined in the description of the next role. In addition, it will also validate the blocks produced by sequencers and determine the finality of the blocks.

- **Sequencer:** In L2 network, there are two kinds of sequencers — the leader of sequencers and the sequencer follower. From the sequencer's point of view, an L2 network consists of only one leader of sequencers and several sequencer followers, which means there is only one node that will take on the leadership of sequencer.

  - **Sequencer Leader:** The leader is responsible for rolling up the transactions in L2, producing an L2 block and broadcasting the new block in the sequencer network.

  - **Sequencer Follower:** Followers receive the new block from the leader in the correct epoch, and execute those transactions in the new block.

###

How to Join the Decentralized Sequencer Set?

[](#how-to-join-the-decentralized-sequencer-set)

Our decentralized Sequencer cohort is on our roadmap. Please reach out to the Mantle team via our

[Telegram](https://t.me/mantlenetwork)

if you're interested in contributing/joining.
