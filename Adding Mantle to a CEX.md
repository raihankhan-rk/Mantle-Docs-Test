# Adding Mantle to a CEX - Mantle Tech Docs

Mantle shares the

[Ethereum JSON-RPC API](https://eth.wiki/json-rpc/API)

with almost no differences. You can choose to connect to Mantle through any of the following means:

- 2.

  Private endpoints from infrastructure providers

- 3.

  Or, by running your own node

Because of throughput limits, we recommend using private infrastructure providers, or running your own node for production applications.

In Mantle, the **$ETH** balance of an account is not stored as part of the account's state, but as an ERC-20 balance at address `0xdEAddEaDdeadDEadDEADDEAddEADDEAddead1111`. You can still access it using the standard APIs.

The ERC-20 contract address for a token on Mantle may be different from the address for the same token on Ethereum. The list of tokens and their addresses is

[here](https://github.com/mantlenetworkio/mantle-token-lists#mantle-token-list)

.

To get the total ERC-20 token balance of a user for a specific token we listed, you need to:

- Connect to a standard Ethereum endpoint and send a `balanceOf` query to the L1 token address.

- Connect to a Mantle endpoint and send a `balanceOf` query to address.

###

**Deposits and Withdrawals on Mantle**

[](#deposits-and-withdrawals-on-mantle)

The ERC-20 contracts on Mantle function the same way they do on Ethereum, so you can use your existing code for withdrawals and deposits. Just connect to a Mantle endpoint.

Most of the cost of a Mantle transaction is not the gas consumed by the transaction itself (which is priced at a very low level), but the cost of writing the transaction in Ethereum. That cost is deducted automatically from the user's balance on Mantle. If you charge your users the cost of withdrawals, you have to account for it.

###

**Deposits and Withdrawals Across Chains**

[](#deposits-and-withdrawals-across-chains)

As a centralized exchange, there will be times that withdrawals of **$ETH**/**$BIT** token or an ERC-20 token on either Mantle or Ethereum exceed deposits and you need to transfer assets. To do that you use a bridge or a gateway. We have a standard bridge that receives assets on Ethereum mainnet, and mints the equivalent asset on Mantle.

Note that while L1 to L2 transactions typically take minutes, L2 to L1 transactions on the gateway require a 7 day challenge period.
