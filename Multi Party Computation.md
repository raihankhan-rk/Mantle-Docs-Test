# Multi-Party Computation - Mantle Tech Docs

Optimistic Rollups (ORs) have a limitation in terms of imposing long waiting times for withdrawals in the form of challenge periods. In order to address this problem, we use Multi-Party Computation (MPC) to implement a distributed signing scheme for the verification of block data.

Multi-Party Computation, or MPC is a field of cryptography where computation processes and evaluation can be distributed among multiple parties without them having to reveal any private data.

![](https://2129957930-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FNHXgeqhgrznnRobzznxl%2Fuploads%2Fdn3oaFB0kb9iFn4G7bId%2FMPC%20execution%20simple.jpg?alt=media&token=3d2c7d8f-a679-4b4c-894b-e65c4dfc1b9e)

Here multiple MPC client nodes verify the block data sent by Sequencers and sign it, guaranteeing its correctness.

The process of computing digital signatures using MPC basically involves three steps:

- 1.

  **Key Generation:** The key generation function run by each MPC client generates a private key, and then generates a public key based off of the private key. It is designed such that the public key it generates the same public key for every client, and the private key is unique and never shared in any manner.

- 2.

  **Signing:** The signing function takes a public input available to all clients as the message to be signed, and then generates a signature using the private key.

- 3.

  **Verification:** A verification algorithm takes the signature data and verifies it with the public key.

This MPC paradigm of distributed key generation combined with digital signatures is more popularly known as the **Threshold Signature Scheme (TSS)**. It enables each party of the client network to generate a valid signature, and ensures the verifiability of distributed signature data as long as enough of them are honest. The private key also ceases to be a single point of failure now that every MPC client only holds a part of it.

MPC can effectively shorten the challenge period by improving the correctness of off-chain transaction execution results.

MPC nodes stake a fixed amount of **$BIT** on Ethereum as part of the deployment process, and this stake gets slashed if there is malice or failure detected on their part.

![](https://2129957930-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FNHXgeqhgrznnRobzznxl%2Fuploads%2FnHZ5MDIJmYYdFs89PoX7%2FMPC%20architecture.jpg?alt=media&token=336d0111-f52d-410d-ade8-64615a071cb0)

The components of the MPC client network that implements TSS are:

- 1.

  **MPC Core:** Uses an implementation of the

  [**tss-lib**](https://github.com/bnb-chain/tss-lib)

  as our version of the TSS.

- 2.

  **MPC Manager:** The MPC Manager is responsible for managing the MPC nodes engaged in multi-party computation, and dispatching signing tasks to them.

- 3.

  **MPC Nodes:** An **MPC node** generates its own private key, and communicates with other nodes through a peer-to-peer network to generate signatures by signing messages with its private key.

To qualify for deploying an MPC node, you will need to meet the following conditions:

- Stake a fixed amount of **$BIT** on Ethereum

- Undergo an evaluation by the Mantle core team (temporary)

MPC nodes will be elected through BitDAO governance in the future.

**Justification for an MPC Manager**

[](#justification-for-an-mpc-manager)

---

In order to maintain coordination between MPC Nodes to accomplish signing tasks, there needs to be a component that assists with:

- discovering nodes that are able to join a signing task at any given point of time

- dispatching signing tasks to the MPC nodes

Our research determined it would be more costly for each node to keep track of available node participants as compared to creating a dedicated MPC Manager.

The MPC Manager accepts WebSocket (WS) connections from MPC nodes and transmits messages to the nodes via these connections.

The signature process begins when the Batch Submitter sends a **signature request** to the MPC Manager. The signature request includes a state root that will be uploaded to L1 if verified.

The MPC Manager must now coordinate with the MPC nodes to verify that this state root is correct. This process that follows:

- 1.

  The MPC Manager determines which MPC nodes are willing to join the signing task

- 2.

  The MPC Manager dispatches the signing task along with block data to the active MPC nodes (along with data indicating which nodes joined; see: [MPC Node Slashing](https://docs.mantle.xyz/for-validators/multi-party-computation#mpc-node-slashing))

- 3.

  MPC nodes generate signatures in an attempt to verify the state root

- 4.

  The MPC Manager receives and verifies the signatures from the respective MPC nodes

- 5.

  If valid, the MPC Manager sends the signature to the batch submitter

Every MPC node are obligated to actively participate in state root verification and signing. Their staked **$BIT** will be slashed in case of failure to fulfill any of these duties, i.e., not returning signed block data under any circumstances. All slashed **$BIT** will be distributed among the functioning MPC nodes.

Because MPC nodes re-derive the state root, we alternatively refer to signed state roots generated by MPC nodes as the **execution result**.

When the execution result determines that the state root is valid — and can be uploaded to L1 — it is passed back to the Batch Submitter by the MPC Manager.

Each MPC client runs a **full node** (connected to sequencers via P2P) so it can execute transactions and arrive at the correct state root. This state root (the execution result) is used to verify the event data coming from the MPC manager.

![](https://2129957930-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FNHXgeqhgrznnRobzznxl%2Fuploads%2FhmL5sPP9H8H3YeRXPkz8%2FMPC%20sequence%201.jpg?alt=media&token=4329b5b7-c2a0-48e3-a71e-76e4604f12b9)

The following process describes how MPC nodes are elected:

- 1.

  A user must stake **$BIT** on the Ethereum mainnet

- 2.

  The smart contract on L1 records the user's stake and locks the **$BIT** tokens

- 3.

  BitDAO votes on the staked users to determine which MPC nodes are to be included as a part of the MPC node cluster

- 4.

  Elected nodes run the MPC process offline, which manages private key shares and generates the cluster public key

- 5.

  The nodes submit the generated cluster public key to L1

- 6.

  The cluster public key is confirmed once it is submitted by all MPC nodes

![](https://2129957930-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FNHXgeqhgrznnRobzznxl%2Fuploads%2FJEYy3IV6LqBbcxvm3COI%2FMPC%20sequence%202.jpg?alt=media&token=3255d376-8d4e-492f-a62e-36f11cc21bc1)

Our system needs deterrents to prevent malicious behavior on part of MPC nodes. We identify two types of malicious behavior and their respective deterrents below:

To create a deterrent against extended downtime for MPC nodes, the MPC Manager records which nodes are absent from every signing task. As the degree of absence increases, any member of the cluster can submit a proposition to slash an absent node's stake. If the proposition is signed by a proportional majority of the cohort (by stake), a portion of the absent node's stake will be slashed and allocated to other members.

The pseudocode for such a function would look like:

function slash(absentNode bytes, absentBlockWindow bytes, submitters \[\]bytes) onlyTssClusterAddress {

require(!slashedAlready, "slashed already")

var tokens \= getTokensToBeSlashed()

transferTokensToSubmitters(tokens, submitters)

recordAbsentBlockWindow(absentNode, absentBlockWindow)

Another form of malicious behavior would be if an MPC node serves fraudulent data to the MPC cluster to disrupt the signing process.

In this event, other nodes will record the node's behavior and report it to the MPC Manager. The MPC Manager will then start a proposition to penalize this malicious node and remove it from the cluster. Once signed by a proportional majority, this node will be removed from the cohort forever.
