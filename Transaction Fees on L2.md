# Transaction Fees on L2 - Mantle Tech Docs

Transaction fees on Mantle work similar to fees on Ethereum. As Mantle is a Layer 2 (L2) blockchain, there are new paradigms in the fee structure that cause it to differ from Ethereum's fee.

Since Mantle is constantly perfecting EVM compatibility, dApps can conveniently handle any changes with minor modifications. Let's take a look at the two sources of transaction costs on Mantle.

- 2.

  The Layer 1 (L1) data/security fee

Just like Ethereum, transactions on Mantle have to pay **gas** for the amount of computation and storage that they use. Every L2 transaction will pay some **execution fee**, equal to the amount of gas used by the transaction multiplied by the gas price attached to the transaction. This is exactly how fees work on Ethereum with the added bonus that gas prices on Mantle are incredibly low.

Here's the (simple) math:

L2_Execution_Fee = Transaction_Gas_Price \* L2_Gas_Used

The amount of L2 gas used depends on the particular transaction that you're trying to send. Thanks to EVM compatibility, transactions typically use approximately the same amount of gas on Mantle as they do on Ethereum. Gas prices fluctuate with time and congestion, but you can always check the current estimated L2 gas price on the public Mantle dashboard.

Currently, all L2 transactions that are submitted to Mantle are also published on Ethereum. This step is crucial to the security properties of Mantle because it means that all of the data you need to sync a Mantle node is always publicly available on Ethereum.

Users on Mantle have to pay for the cost of submitting their transactions to Ethereum. We call this the **L1 data fee**, and it's the primary discrepancy between Mantle (and other L2s) and Ethereum. Because the cost of gas is so expensive on Ethereum, the L1 data fee typically dominates the total cost of a transaction on Mantle. This fee is based on four factors:

- 1.

  The current gas price on Ethereum.

- 2.

  The gas cost to publish the transaction to Ethereum. This scales roughly with the size of the transaction (in bytes).

- 3.

  A fixed overhead cost denominated in gas. (This is currently set to 2100).

- 4.

  A dynamic overhead cost which scales the L1 fee paid by a fixed number. This is currently set to 1.0

L1_Data_Fee = L1_Gas_Price \* (Tx_Data_Gas + Fixed_Overhead) \* Dynamic_Overhead

Tx_Data_Gas = count_zero_bytes(tx_data) \* 4 + count_non_zero_bytes(tx_data) \* 16

You can read the parameter values from the gas oracle contract.

Once our EigenDA based data availability solution goes online (coming soon!), all the transaction data that is published to L1 Ethereum would be published to and recorded by the DA network instead. An immediate consequence of this would be the elimination of L1 data fees and a major reduction in the overall cost of sending transactions to L2. Feel free to read up more on how we're leveraging EigenDA for [Solving Data Availability](https://docs.mantle.xyz/introducing-mantle/a-gentle-introduction/solving-data-availability)​

Ethereum has limited support for adding custom transaction types. As a result, unlike the L2 execution fee, users cannot set limits for the L1 data fee that they may be charged. The L1 gas price used to charge the data fee is automatically updated when new data is received from Ethereum. Spikes in Ethereum gas prices may result in users paying a higher, or lower than the estimated L1 data fee by up to **25%** in extreme cases.

The process an L2 transaction goes through is as follows:

- 1.

  The wallet estimates the cost of the transaction

- 2.

  The user submits the transaction

- 3.

  The sequencer processes the transaction, calculates the total gas price based on the latest L1 and L2 gas prices, and deducts the gas cost from the sender's wallet

- 4.

  The transaction is written to L1

Any fluctuations after the sequencer processes a transaction do not have an impact on the gas cost the transaction sender pays. Mantle accounts and compensates for any L1 gas price spikes. Gas prices are updated every **five minutes** and generally do not vary more than 25% between the wallet estimating the transaction cost and the sequencer making the actual deduction.

All collected gas fees will be sent to BitDAO, and the BitDAO community will decide on the burning mechanism for those accumulated **$BIT** tokens.

For a better user experience and easier settlement, Mantle will subsidize the L1 data fee to get started. Mantle will regularly pull the rollup related transactions from L1, calculate the total L1 data fee, and pay to L1 from a Mantle account.

The process of sending a transaction on Mantle is identical to the process of sending a transaction on Ethereum. When sending a transaction, you should provide a gas price greater than or equal to the current L2 gas price. Like on Ethereum, you can query this gas price with the `eth_gasPrice` RPC method. Similarly, you should set your transaction gas limit in the same way that you would set your transaction gas limit on Ethereum (e.g. via `eth_estimateGas`).

###

**Responding to Gas Price Updates**

[](#responding-to-gas-price-updates)

Gas prices on L2 default to 0.001 Gwei but can increase dynamically if the network is congested. When this happens, the lowest fee that the network will accept increases. Unlike Ethereum, Mantle currently does not have a mempool to hold transactions with too low a fee. Instead, Mantle nodes will reject the transaction with the message "Fee too low". You may need to handle this case explicitly and retry the transaction with a new gas price when this happens.

Mantle does not support EIP-1559 transactions since all the transactions are are executed and included in blocks in the order they were received by the network, and thus the concept of prioritized transactions does not exist on L2.

It is recommended that you send legacy transactions only through your contracts and apps to avoid a EIP-1559 gas estimation error. An alternative workaround would be to use your account nonce as the transaction nonce value when sending EIP-1559 transactions to Mantle.

Many Ethereum applications display estimated fees to users by multiplying the gas price by the gas limit. However, as discussed earlier, users on Mantle are charged both an L2 execution fee and an L1 data fee. As a result, you should display the sum of both of these fees to give users the most accurate estimate of the total cost of a transaction. See here for a

[code sample](https://mantlenetworkio.github.io/mantle-tutorial/sdk-estimate-gas/)

using the JavaScript SDK.

####

**Estimating the L2 Execution Fee**

[](#estimating-the-l2-execution-fee)

You can estimate the L2 execution fee by multiplying the **gas price** by the **gas limit**, just like on Ethereum.

####

**Estimating the L1 data fee**

[](#estimating-the-l1-data-fee)

You can use the SDK (here's our

[tutorial](https://mantlenetworkio.github.io/mantle-tutorial/sdk-estimate-gas/)

) to do this. Alternatively, you can estimate the L1 data fee using the GasPriceOracle predeployed smart contract located at `0x420000000000000000000000000000000000000F` .

The `GasPriceOracle` contract is located at the same address on every Mantle network (mainnet and testnet). To do so, call `GasPriceOracle.getL1Fee()`.

You can estimate the total fee by combining your estimates for the L2 execution fee and L1 data fee.

Sending the maximum amount of **$BIT** that a user has in their wallet is a relatively common use case. When doing this, you will need to subtract the estimated L2 execution fee and the estimated L1 data fee from the amount of **$BIT** you want the user to send. Use the logic described above for estimating the total fee.

**Error message:** invalid transaction: insufficient funds for l1Fee + l2Fee + value

You'll get this error when attempting to send a transaction and you don't have enough **$BIT** to pay for the value of the transaction, the L2 execution fee, and the L1 data fee. You might get this error when attempting to send max **$BIT** if you aren't properly accounting for both the L2 execution fee and the L1 data fee.

Error message: gas price too low: X wei, use at least tx.gasPrice = Y wei

This is a custom RPC error that Mantle returns when a transaction is rejected because the gas price is too low. See the section on

[Responding to Gas Price Updates](https://docs.mantle.xyz/for-validators/transaction-fees-on-l2#responding-to-gas-price-updates)

for more information.

Error message: gas price too high: X wei, use at most tx.gasPrice = Y wei

This is a custom RPC error that Mantle returns when a transaction is rejected because the gas price is too high. We include this as a safety measure to prevent users from accidentally sending a transaction with an extremely high L2 gas price. See the section on [Responding to Gas Price Updates](https://docs.mantle.xyz/for-validators/transaction-fees-on-l2#responding-to-gas-price-updates) for more information.
