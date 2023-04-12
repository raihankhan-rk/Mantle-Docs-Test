# Adding a Custom Bridge to Mantle - Mantle Tech Docs

###

**Using the Standard Bridge**

[](#using-the-standard-bridge)

We suggest considering using the

[Standard Bridge](https://bridge.testnet.mantle.xyz/deposit)

first before deciding to develop a custom bridge solution.

In the standard bridge when ERC-20 is deposited on Layer 1 (L1) and transferred to the bridge contract, it remains "locked" there while the equivalent amount is minted in the Layer 2 (L2) token. For withdrawals the opposite happens, the L2 token amount is burned then the same amount of L1 tokens is transferred to the recipient. This fund can be released after the challenge period. This implementation satisfies a wide range of requirements.

Some reasons why the standard bridge might not work for you is for example when you cannot limit the L2 token mint and burn functions to the bridge alone (something we require for security). Also, certain custom bridges implement their own logic for managing the token supply which requires custom logic. Another case might be when you are bridging non-ERC-20 tokens (e.g. NFTs). Yet another case is when you are pooling deposits for cheaper transfers to L2.

Note that when you are building a custom bridge for ERC-20 tokens and planning to add these to the Mantle token list, we have specific requirements for the bridge contracts. These have to implement the IL1ERC20Bridge interface in the L1 bridge contract and IL2ERC20Bridge interface in the L2 bridge contract. This ensures the Mantle Gateway can support token deposits and withdrawals via this custom bridge.

To add your bridge and token to the token list, you must make a pull request against the Mantle

[token list repository.](https://github.com/mantlenetworkio/mantle-token-lists#mantle-token-list)

Due to the complexity of reviewing a custom bridge, you will need to deploy to Mantle Goerli first, before going to production.
