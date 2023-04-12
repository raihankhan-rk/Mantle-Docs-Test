# Bridging $BIT using Mantle SDK - Mantle Tech Docs

This tutorial demonstrates how to use the Mantle SDK to deposit and withdraw $BIT tokens between Mantle and Ethereum.

Make sure you have the following tools installed in your local environment.

Let's start by fetching the example JS scripts that we’ll work with and use to make SDK invocations from the Mantle Github. Clone the repository containing the sample scripts by executing the following command in your project directory.

git clone https://github.com/mantlenetworkio/mantle-tutorial.git

Next, we can use `yarn` to download the SDK along with all the necessary dependencies, as shown below. All the dependencies are defined in the `yarn.lock` file, so we can just run `yarn` in the `./cross-dom-bridge-bit` directory.

We'll need a `.env` file from where we can add and modify wallet and network settings. The main directory contains two `.env` files, where `.env.local` specifies the configuration for a local environment, while `.env.testnet` specifies the configuration to connect to testnet.

All the necessary contracts addresses are already included in the respective `.env` files, so you can specify your preferred L1 RPC endpoint and your wallet private key to start sending transactions.

L1_RPC= # L1 RPC Endpoint

L2_RPC=https://rpc.testnet.mantle.xyz

L1_BRIDGE=0xc92470D7Ffa21473611ab6c6e2FcFB8637c8f330

L2_BRIDGE=0x4200000000000000000000000000000000000010

\# crossDomainMessenger address

L1_CDM=0x7Bfe603647d5380ED3909F6f87580D0Af1B228B4

L2_CDM=0x4200000000000000000000000000000000000007

L1_BIT=0x5a94Dc6cc85fdA49d8E9A8b85DDE8629025C42be

L2_BIT=0xDeadDeAddeAddEAddeadDEaDDEAdDeaDDeAD0000

L2_ETH=0xdEAddEaDdeadDEadDEADDEAddEADDEAddead1111

PRIV_KEY= # Wallet private key

Let's take a look at the main script.

Analyzing and modifying the sample script

[](#analyzing-and-modifying-the-sample-script)

---

The `index.js` script containing the code we need is located in the `./mantle-tutorial/cross-dom-bridge-eth` directory. By default, it is configured to run on a local test environment. You can run L1 and L2 instances on your system and start deploying contracts to test your applications. You can make a copy of the `index.js` file before we start modifying it if you want to try that out.

> Check out the
>
> [tutorial here](https://mantlenetworkio.github.io/mantle-tutorial/cross-dom-bridge-bit/)
>
> that demonstrates the same bridging functionality on a private network.

###

Importing necessary libraries

[](#importing-necessary-libraries)

require('dotenv').config()

const ethers \= require("ethers")

const mantleSDK \= require("@mantleio/sdk")

This code does not need to be changed. We import three libraries, and the `.env` configuration file we created earlier.

- `dotenv` : The `.env` file containing wallet and network configuration

- `ethers` : The Ethers.js library comes handy with wallet and contract operations

- `@mantlenetwork/sdk` : Mantle SDK instance

- `fs`: File system module to read the contract ABI from a JSON file

###

Generating contract bytecode from ABI

[](#generating-contract-bytecode-from-abi)

const L1TestERC20 \= JSON.parse(fs.readFileSync("TestERC20.json"))

We don't need to modify this either. The contents of the JSON file containing the contract ABI are stored in `TestERC20.json` which we will be using later.

###

Network configuration and wallet setup

[](#network-configuration-and-wallet-setup)

const l1BitAddr \= process.env.L1_BIT

const l2BitAddr \= process.env.L2_BIT

const key \= process.env.PRIV_KEY

const l1RpcProvider \= new ethers.providers.JsonRpcProvider(process.env.L1_RPC)

const l2RpcProvider \= new ethers.providers.JsonRpcProvider(process.env.L2_RPC)

const l1Wallet \= new ethers.Wallet(key, l1RpcProvider)

const l2Wallet \= new ethers.Wallet(key, l2RpcProvider)

We fetch the specified network and wallet configurations from the `.env` file, and create wallet objects by passing the private key and RPC addresses as parameters for L1 and L2.

###

CrossChainMessenger object

[](#crosschainmessenger-object)

const setup \= async () \=> {

ourAddr \= l1Wallet.address // Assigning wallet address

crossChainMessenger \= new mantleSDK.CrossChainMessenger({ // CrossChainMessenger object instantiation

l1ChainId: process.env.L1_CHAINID, // Assigning chain IDs from .env file

l2ChainId: process.env.L2_CHAINID,

l1SignerOrProvider: l1Wallet, // Wallets that will sign transactions

l2SignerOrProvider: l2Wallet

l1Bit \= new ethers.Contract(l1BitAddr, L1TestERC20.abi, l1Wallet) // Contract objects

l2Bit \= new ethers.Contract(l2BitAddr, L1TestERC20.abi, l2Wallet)

The `CrossChainMessenger` object calls the cross chain messenger contracts on L1 and L2 to transfer assets. Here we instantiate the object with chain IDs, wallet objects, and contract objects.

const reportBalances \= async () \=> {

const l1Balance \= (await l1Bit.balanceOf(ourAddr)).toString().slice(0, \-18)

const l2Balance \= (await l2Bit.balanceOf(ourAddr)).toString().slice(0, \-18)

console.log(\`Token on L1:${l1Balance} Token on L2:${l2Balance}\`)

The `reportBalances` function fetches L1 and L2 wallet balances and prints them out. We'll use this method to keep track balance change after deposit and withdraw operations.

const depositBIT \= async () \=> {

console.log("#################### Deposit BIT ####################")

await reportBalances() // 1. Print balance before deposit

const allowanceResponse \= await crossChainMessenger.approveERC20( // 2. Approve deposit amount

l1BitAddr, l2BitAddr, depositToken)

await allowanceResponse.wait()

console.log(\`Time so far ${(new Date() \- start) / 1000} seconds\`)

const response \= await crossChainMessenger.depositERC20( // 3. Send deposit transaction

l1BitAddr, l2BitAddr, depositToken)

console.log(\`Deposit transaction hash (on L1): ${response.hash}\`) // 4. Print L1 deposit transaction hash

console.log("Waiting for status to change to RELAYED")

console.log(\`Time so far ${(new Date() \- start) / 1000} seconds\`)

await crossChainMessenger.waitForMessageStatus(response.hash, mantleSDK.MessageStatus.RELAYED)

await reportBalances() // 5. Print updated balance after deposit

console.log(\`depositERC20 took ${(new Date() \- start) / 1000} seconds\\n\`)

The `depositBIT` function deposits 1 $BIT token to L2 via the Mantle bridge. The deposit transaction is sent using the `depositERC20` method, which is picked up by an off-chain service and relayed to L2. The asynchronous function prints out the transaction hash and waits for the message to get relayed. Finally, we display the updated $BIT balance on L1 and L2.

const withdrawBIT \= async () \=> {

console.log("#################### Withdraw BIT ####################")

await reportBalances() // 1. Print balance before withdraw

const response \= await crossChainMessenger.withdrawERC20( // 2. Send withdraw transaction

l1BitAddr, l2BitAddr, withdrawToken)

console.log(\`Transaction hash (on L2): ${response.hash}\`) // 3. Print L2 withdraw transaction hash

console.log("Waiting for status to change to IN_CHALLENGE_PERIOD")

console.log(\`Time so far ${(new Date() \- start) / 1000} seconds\`)

await crossChainMessenger.waitForMessageStatus(response.hash, // 4. Function waits for transaction to enter challenge period

mantleSDK.MessageStatus.IN_CHALLENGE_PERIOD)

console.log("In the challenge period, waiting for status READY_FOR_RELAY")

console.log(\`Time so far ${(new Date() \- start) / 1000} seconds\`)

await crossChainMessenger.waitForMessageStatus(response.hash,

mantleSDK.MessageStatus.READY_FOR_RELAY) // 5. Check whether transaction is ready for relay

console.log("Ready for relay, finalizing message now")

console.log(\`Time so far ${(new Date() \- start) / 1000} seconds\`)

await crossChainMessenger.finalizeMessage(response)

console.log("Waiting for status to change to RELAYED")

console.log(\`Time so far ${(new Date() \- start) / 1000} seconds\`)

await crossChainMessenger.waitForMessageStatus(response,

mantleSDK.MessageStatus.RELAYED) // 6. Wait for transaction to get relayed

await reportBalances() // 7. Print updated balance after withdraw

console.log(\`withdrawERC20 took ${(new Date() \- start) / 1000} seconds\\n\\n\\n\`)

Similarly, the `withdrawBIT` function withdraws 1 $BIT token from L2 via the Mantle bridge. The function prints out the transaction hash. The transaction then goes into a challenge period. Once it is ready for relay, it is picked up by an off-chain service to be relayed to L1. Finally, we display the updated $BIT balance on L1 and L2.

###

Invoking deposit and withdraw functions

[](#invoking-deposit-and-withdraw-functions)

const main \= async () \=> {

We write a `main()` where we call the functions to perform configuration, deposit, and withdraw operations.

Once the configuration is ready, you can run the script using the `yarn testnet` command. The script will automatically select the testnet configuration to perform both deposit and withdraw operations in the `index.js` script. If you want to run the script locally, you can run `yarn local`.

You can use this code to test out the token bridging mechanism via SDK on Mantle testnet and start integrating it to your applications.
