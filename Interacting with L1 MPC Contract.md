# Interacting With the L1 MPC Contract - Mantle Tech Docs

How to perform operations using the L1 MPC smart contract, such as staking, withdrawal, unjail, etc.

In order to interact with the contract, please begin by following the instructions on

[this README](https://github.com/mantlenetworkio/mantle/blob/main/packages/contracts/README.md)

to set up your environment.

Any user can apply to be a validator when the new round of elections begins. We recommend that you run the following command to view the help text for the staking command**:**

Usage: hardhat \[GLOBAL OPTIONS\] staking \--amount \--contract \--pubkey

\--contract tss staking slashing contract address \--pubkey

As the commands show, you must provide your `pubkey`, which is your public key in 64 bytes

A user can check if they are elected by calling `checkIsTssMember`. You can run `npx hardhat help checkIsTssMember` to get the help text for the `checkIsTssMember` command:

checkIsTssMember Usage: hardhat \[GLOBAL OPTIONS\] checkIsTssMember \--contract \--pubkey

\--contract tss group manager contract address \--pubkey

WithdrawToken allows users who are not elected to withdraw tokens. Run the following to get help text for the staking command.

npx hardhat help withdrawToken

If a user is jailed because of a liveness issue, they can return to normal status by manually triggering the unjail operation. Run the following to get help text for the staking command.

When unjailing, a user must ensure they have sufficient staked tokens to unjail and return as a validator, since we have a minimum amount limit.

The slashing related parameters can be fetched via `getSlashingParams` command. Run the following to get help text for the staking command.

npx hardhat help getSlashingParams

If an MPC node wants to quit the validating job, then it needs to send a request to quit the next election. Run the following to get help text for the staking command.

npx hardhat help quitRequest
