# Moving Assets and Data - Mantle Tech Docs

The process of moving assets or data between two blockchains is generally referred to as 'bridging'. Since the Mantle is a separate Layer 2 (L2) blockchain system, apps and wallets moving assets or any another arbitrary form of data between Ethereum and Mantle can use our official bridge to achieve that.

The

[Mantle Bridge](https://bridge.testnet.mantle.xyz/)

enables all functionality necessary for the most common use case, i.e., moving tokens between Mantle and Ethereum. It also allows you to easily create Layer 2 (L2) representations of existing tokens on Ethereum.

If you need to send arbitrary data between Ethereum & Mantle, you can do so by having a contract on Ethereum trigger a contract function on Mantle and vice versa. The

[Mantle Bridge](https://bridge.testnet.mantle.xyz/)

has a simple API for triggering a cross-chain function call.

The standard Mantle bridge is essentially a set of smart contracts. The two main contracts that implement the ERC-20 asset bridging mechanism are

[`L2StandardBridge`](https://github.com/ethereum-optimism/optimism/blob/master/packages/contracts/contracts/L2/messaging/L2StandardBridge.sol)

on Mantle, and

[`L1StandardBridge`](https://github.com/mantlenetworkio/mantle/blob/main/packages/contracts/contracts/L1/messaging/L1StandardBridge.sol)

on Ethereum. Deposit or withdrawal transactions sent to the bridge by a wallet on L2 or L1 trigger specific methods in the standard bridge contracts.

For instance, when a wallet sends a deposit request for 10 $BIT on L1, these tokens get locked on L1 in the bridge contract, and the matching amount of 10 $BIT is minted on L2 and transferred to the wallet.

In case a wallet makes a withdrawal of 10 $BIT on L2, these tokens are burned and the matching amount of 10 $BIT tokens locked on L1 get released and transferred to the wallet.

You can connect your wallet to the

[Mantle Bridge UI](https://bridge.testnet.mantle.xyz/)

to make simple deposits and withdrawals directly. However, if you're looking to use the bridge API instead, refer to the [Mantle Bridge API](https://docs.mantle.xyz/tools-and-sdk/mantle-bridge-api) page for more information and tutorials on how to call the appropriate SDK methods to bridge the various kinds of tokens that are currently supported.
