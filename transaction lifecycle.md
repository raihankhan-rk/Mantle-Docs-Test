# Transaction Lifecycle - Mantle Tech Docs

A wallet user, a dApp, or a script somewhere wants to use a blockchain based system to transfer funds, or perform a task.

- In the case of the user, they've already connected to the Mantle network by adding the RPC endpoint of a Sequencer node to their wallet.

- The dApp on the other hand has integrated the Mantle SDK and has already instantiated it to be able to talk to the Mantle network. They are both ready to send a request that contains a receiver's address and the amount they want to transfer.

Just like any service, this action requires a fee. And not just for making the transfer itself, but to also maintain a perpetual, immutable public record of it that can be verified by anybody. Their wallet, or dApp, must make sure they have enough balance to do this.

- For a wallet, things are simpler, since it's easier to calculate the fee required to be paid

They use the tools at hand to compose a request, sign it using their private keys, and send it to Mantle network where a Sequencer is ready to process it. This is where the transaction first enters Mantle.

[**More details here.**](https://docs.mantle.xyz/for-validators/network-roles#sequencers)

**​**

The transaction triggers a standard state verification process that is carried out by the Ethereum Virtual Machine (EVM) software running on these nodes. This is to make sure the transaction is valid in nature, has paid the necessary fees, and not doing something out of the ordinary.

This transaction updates the local state (the ledger) and awaits further processing as part of a pending block. The process up until this point is particularly fast.

In blockchain terms, this means instant transaction confirmations and state updates

Many such pending blocks are combined to form batches that will be sent to Ethereum to be finalized. The L2 chain achieves low transaction fees by spreading fixed costs over the multiple transactions in a batch.

This block data is then verified for correctness by another part of the network known as Multi-Party Computation nodes. They verify the state roots submitted by the Sequencer as part of the blocks on L2.

[**More details here.**](https://docs.mantle.xyz/for-validators/network-roles#multi-party-computation-mpc-nodes)

**​**

What if MPC nodes don't do their job?

If an MPC node fails to participate in the verification and signing process, they get their stake slashed which then gets redistributed among other functioning MPC nodes. This creates a strong disincentive to do so.

Once signed off by MPC nodes, the batched block data is ready to make the journey to Ethereum. It is transmitted across the network by the Sequencer to be received by different actors on both L2 and L1.

On L2, Rollup Verifiers are looking to sync this block data to make it accessible for other users and dApps.

[**More details here.**](https://docs.mantle.xyz/for-validators/network-roles#rollup-verifiers)

**​**

A scenario where a Sequencer commits malice

In this case, the Rollup Verifiers can spot invalid block data by verifying state roots and generate proofs of the fraud committed. By submitting this evidence to smart contracts on Ethereum, they can claim **$BIT** rewards.

On Ethereum, there's an entire trust network ready to record the transmitted block data. Once the block goes through Ethereum's consensus mechanism, it is recorded on-chain where it's secure.

Then there are Data Availability nodes, or DA nodes, who will sync the block data and guarantee access to it at any given time in exchange for the **$BIT** rewards they're going to receive for providing this service.

[**More details here.**](https://docs.mantle.xyz/introducing-mantle/a-gentle-introduction/solving-data-availability#da-nodes)

**​**
