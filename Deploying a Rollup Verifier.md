# Deploying a Rollup Verifier - Mantle Tech Docs

In order to build an app on Mantle, you'll need access to a Mantle node.

The current version of Mantle testnet does not support deploying verifier nodes to it

If you run a node, you need to pay attention to the latest release on our

[GitHub Release](https://github.com/mantlenetworkio/mantle/releases)

page

Nodes need to store the transaction history of Mantle and to run l2geth. Recommended specs:

- **CPU** \- min. 4 cores, 8th Gen. or higher

- **Storage** - min. 100 GB disk space free (SSD recommended)

**Deploy a Node With Docker**

[](#deploy-a-node-with-docker)

---

The recommended method to create a replica is to use

[Docker](https://www.docker.com/)

and the

[Docker images we provide](https://hub.docker.com/u/mantlenetworkio)

. They include all the configuration settings. We use these images for our own systems, and as such they will be more thoroughly tested than any other configuration.

You can find instructions to build and operate the node

[here](https://github.com/mantlenetworkio/networks/blob/main/run-node.md)

.

Deploying a Node Without Docker

[](#deploying-a-node-without-docker)

---

If you'd like to deploy a node without relying on our images you can use the following instructions.

Before you progress, please consider the following:

- This is **not** the recommended configuration. We have performed QA on the following instructions. However the docker images undergo far more extensive QA.

- These instructions were executed on a

  [GCP e2-standard-4](https://cloud.google.com/compute/docs/general-purpose-machines#e2-standard)

  virtual machine running

  [Debian 10](https://www.debian.org/News/2021/2021100902)

  with a 100 GB SSD. They should work on different operating systems with minor changes, but there are no guarantees.

- These directions will create a replica of the main network. There are slight modifications needed to create a replica for the test network.

The following packages are required to either compile the software or to run it. Note that we need `libusb-1.0` because Geth requires it to check for hardware wallets.

sudo apt install \-y git make wget gcc pkg-config libusb-1.0 jq

**The Data Transport Layer (DTL)**

[](#the-data-transport-layer-dtl)

---

This TypeScript program reads data from a Mantle endpoint and passes it over to the local instance of l2geth (

[Geth](https://geth.ethereum.org/)

with minor changes for layer 2 support).

git clone \-b main https://github.com/mantlenetworkio/mantle.git

cd ~/mantle/packages/data-transport-layer

2\. Edit `.env` to specify your own configuration. Modify these parameters:

- Parameter: DATA_TRANSPORT_LAYER\_\_NODE_ENV
  - Value: production
- Parameter: DATA_TRANSPORT_LAYER\_\_ETH_NETWORK_NAME
  - Value: testnet
- Parameter: DATA_TRANSPORT_LAYER\_\_ADDRESS_MANAGER
  - Value: 0xa29cD06D675179c9886418E2D8506AF5BA9cdBFB
- Parameter: DATA_TRANSPORT_LAYER\_\_SERVER_HOSTNAME
  - Value: localhost
- Parameter: DATA_TRANSPORT_LAYER\_\_SERVER_PORT
  - Value: 7878
- Parameter: DATA_TRANSPORT_LAYER\_\_SYNC_FROM_L1
  - Value: false
- Parameter: DATA_TRANSPORT_LAYER\_\_L1_RPC_ENDPOINT
  - Value: Get an endpoint from a service provider unless you run a node yourself
- Parameter: DATA_TRANSPORT_LAYER\_\_SYNC_FROM_L2
  - Value: true
- Parameter: DATA_TRANSPORT_LAYER\_\_L2_RPC_ENDPOINT
  - Value: See here
- Parameter: DATA_TRANSPORT_LAYER\_\_L2_CHAIN_ID
  - Value: 5001 (for testnet replica)

These directions are written with the assumption that you sync from L2, which is faster. If you prefer, you can synchronize from L1, which is more secure but slower. To use L1, keep the value of `DATA_TRANSPORT_LAYER__SYNC_FROM_L1` as `true` and `DATA_TRANSPORT_LAYER__SYNC_FROM_L2` as `false`. Also, add this line:

DATA_TRANSPORT_LAYER\_\_L1_START_HEIGHT=8040000

3\. Start the DTL (as a daemon, logging to `~/dtl.log`):

nohup yarn start \> ~/dtl.log &

Note that you cannot just close the window if you want DTL to continue running, you have to exit the shell gracefully.

4\. To verify the DTL is running correctly you can run a command.

- If synchronizing from L2:

  curl \-s http://localhost:7878/eth/syncing?backend\=l2 | jq .currentTransactionIndex

- If synchronizing from L1:

  curl \-s http://localhost:7878/eth/syncing?backend\=l1 | jq .currentTransactionIndex

It gives you the current transaction index, which should increase with time.

5\. For debugging purposes, it is sometimes useful to get a transaction's information from the DTL:

curl \-s http://localhost:7878/transaction/index/<transaction number\>?backend\=l2 | jq .transaction

Note that the transaction indexes are one below the number on Etherscan, so for example

curl \-s http://localhost:7878/transaction/index/31337?backend\=l2 | jq .transaction

Corresponds to Etherscan transaction 31338.

The DTL now needs to download the entire transaction history since regenesis, a process that takes hours. While it is running, we can get started on the client software.

**The Mantle Client Software**

[](#the-mantle-client-software)

---

The client software, called l2geth, is a minimally modified version of

[`geth`](https://geth.ethereum.org/)

. Because Geth supports hardware wallets you might get USB errors. If you do, ignore them.

These directions use `~/gethData` as the data directory. You can replace it with you own directory as long as you maintain consistency across the board.

2\. Download and verify the genesis state, the state of the blockchain during the final regenesis, December 1st, 2022.

wget \-O /tmp/genesis.json https://github.com/mantlenetworkio/networks/blob/main/genesis.json

sha256sum /tmp/genesis.json

The output of the `sha256sum` command should be:

c4b6bd554456c7878e4e97b1dae8ec607b75935490a3a322b410be17297e03f7 /tmp/genesis.json

3\. Create a file called `env.sh` (in whatever directory is convenient) with this content:

export DATADIR=~/gethData

export BLOCK_SIGNER_ADDRESS=0x00000398232E2064F896018496b4b44b3D62751F

export BLOCK_SIGNER_PRIVATE_KEY=6587ae678cf4fc9a33000cdbf9f35226b71dcc6a4684a31203241f9bcfd55d27

export ETH1_CTC_DEPLOYMENT_HEIGHT=8040000

export ETH1_SYNC_SERVICE_ENABLE=true

export ROLLUP_ADDRESS_MANAGER_OWNER_ADDRESS=0x9BA6e03D8B90dE867373Db8cF1A58d2F7F006b3A

export ROLLUP_CLIENT_HTTP=http://localhost:7878

export ROLLUP_DISABLE_TRANSFERS=false

export ROLLUP_ENABLE_L2_GAS_POLLING=false

export ROLLUP_GAS_PRICE_ORACLE_OWNER_ADDRESS=0xAe3e6f7Df1CC6Cf18Fe9F3E69BCFC3351eb4fB45

export ROLLUP_MAX_CALLDATA_SIZE=40000

export ROLLUP_POLL_INTERVAL_FLAG=120s

export ROLLUP_SYNC_SERVICE_ENABLE=true

export ROLLUP_TIMESTAMP_REFRESH=5m

export ROLLUP_VERIFIER_ENABLE=true

export RPC_API=eth,rollup,net,web3

export TARGET_GAS_LIMIT=15000000

export WS_API=eth,rollup,net,web3

**Note:** If synchronizing from L1, replace the last line with:

4\. Run the new file. This syntax (dot, space, and then the name of the script) runs the script in the context of the current shell, rather than in a new shell instance. The reason for doing this is we want to modify the current shell's environment variables, and not start a new shell, set up the environment in it, and then exit.

5\. Initialize l2geth with the genesis state. This process takes about nine minutes on my system.

mkdir ~/gethData./build/bin/geth

init \--datadir\=$DATADIR/tmp/genesis.json \--nousb

6\. Create the Geth account. The private key needs to be the one specified in the configuration. Otherwise the consensus algorithm fails and the node does not synchronize.

echo $BLOCK_SIGNER_PRIVATE_KEY \> $DATADIR/block-signer-key

import \--datadir\=$DATADIR \--password $DATADIR/password $DATADIR/block-signer-key

7\. Start Geth (logging to `~/geth.log`).

\--password\=$DATADIR/password \\

\--allow-insecure-unlock \\

\--unlock\=$BLOCK_SIGNER_ADDRESS \\

\--miner.etherbase\=$BLOCK_SIGNER_ADDRESS \> ~/geth.log &

It is possible that \`geth\` won't listen to IPC or the TCP port (8545) until it finishes the initial synchronization.

8\. To check if l2geth is running correctly, open another command line window and run these commands:

build/bin/geth attach \--datadir\=~/gethData

Wait a few seconds and then look at the block number again and exit:

If l2geth is synchronizing, the second block number is higher than the first.

9\. Wait a few hours until the entire history is downloaded by DTL and then propagated to l2geth.
