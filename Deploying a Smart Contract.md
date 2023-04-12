# Deploying a Smart Contract - Mantle Tech Docs

This is a step-by-step tutorial demonstrating how to deploy a contract to Mantle testnet. We'll be using

[Hardhat](https://hardhat.org/)

to demonstrate the process, but you can use any Ethereum contract development environment of your choice!

Make sure you have

[Node.js](https://nodejs.org/en/download/)

installed on your system before moving forward.

First, let's install Hardhat in your local project environment. Create a test project directory and navigate into it in your command line.

Next, run the following command to install Hardhat.

Now, run Hardhat by using the `npx hardhat` command, and create a new JavaScript project to get started. This will initialize your project directory with all the necessary files and a sample contract to work with. You can go ahead and select all the default options during this process if you're doing this for the first time.

Now, navigate to the `/contracts` folder, create a new Solidity file and name it `Greeter.sol`. This is the sample contract we'll be testing out by deploying it to the Mantle testnet. Update the file to include the following code.

//SPDX-License-Identifier: Unlicense

import "hardhat/console.sol";

constructor(string memory \_greeting) {

console.log("Deploying a Greeter with greeting:", \_greeting);

function greet() public view returns (string memory) {

function setGreeting(string memory \_greeting) public {

console.log("Changing greeting from '%s' to '%s'", greeting, \_greeting);

As you can see, this contract does a couple of things. The `setGreeting()` function updates the value of the `greeting` variable, and the `greet()` function returns whatever string value this variable contains. The constructor function at the top logs the greeting with which the contract was deployed.

Now, we need a script that we can run to deploy our `Greeter.sol` contract. We can use the sample script that Hardhat already put in the `/scripts` folder by renaming it to `deploy.js` (since that's what we're going to do by running it), and updating its contents to the following.

const hre \= require("hardhat");

const Greeter \= await hre.ethers.getContractFactory("Greeter");

const greeter \= await Greeter.deploy("Hello, Mantle!");

await greeter.deployed();

console.log("Greeter deployed to: ", greeter.address); // Logs the address to which our contract is deployed

.then(() \=> process.exit(0))

The next step is to modify the `hardhat.config.js` file to add the network configuration for Mantle testnet for this Hardhat project. We also need to create a `.env` file where we'll specify the private key of the wallet that will be used to deploy our contract.

First navigate to `hardhat.config.js` file in your project directory, and update the configuration to match the following.

require("@nomicfoundation/hardhat-toolbox");

require("dotenv").config()

/\*\* @type import('hardhat/config').HardhatUserConfig \*/

url: "https://rpc.testnet.mantle.xyz/",

accounts: \[process.env.PRIV_KEY\] // Uses the private key from the .env file

Now create a `.env` file in the main project directory, and specify your wallet private key like so.

PRIV_KEY=0x\[wallet private key\]

Before moving forward, make sure that you have some test **$BIT** available in the wallet whose private key you specify in the `.env` file to pay the (super low) gas fees for deploying your contract. There's multiple ways to acquire test **$BIT**. Find more details in the [Acquire Test $BIT and Goerli $ETH](https://docs.mantle.xyz/introducing-mantle/quick-start#acquire-test-usdbit-and-goerli-usdeth) section.

With the contract code in place, a script written to deploy your contract, and the network configuration ready, you can deploy the `Greeter.sol` contract by running the following command in the command line.

npx hardhat run scripts/deploy.js \--network mantle-testnet

If everything is configured correctly, you'll see a success response that we defined in the `deploy.js` script like this.

Greeter deployed to: \[contract address\]

Congratulations! Your contract is now deployed and ready to be invoked on Mantle. You can look it up using the contract address on

[the Explorer](https://explorer.testnet.mantle.xyz/)

.

Let's move forward and run a Hardhat console session to start making function calls to the contract.

Start a Hardhat console session by running the following command.

npx hardhat console \--network mantle-testnet

Then initialize the `Greeter.sol` contract by running this line.

const Greeter \= await ethers.getContractFactory("Greeter");

Next, establish a connection to the deployed contract by passing the contract address in the following manner.

const greeter \= await Greeter.attach("0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512");

Now try calling the `greet()` function as shown below, and look out for the output.

Because the contract you deployed has a `setGreeting()` function that can update the greeting string, run the following line and then call the `greet()` method again.

await greeter.setGreeting("Hello, Web3!");

The value gets updated in the on-chain contract when you call `setGreeting()`. To verify, exit the active console, run another instance, establish connection to the contract, and then call `greet()` again.

You can choose to write a script, import the Hardhat library by including `require("hardhat")`, and make the same function calls.
