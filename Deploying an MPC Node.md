# Deploying an MPC Node - Mantle Tech Docs
L1 chain URL that Mantle uses

Mantle state commitment chain contract address

`tss_group_contract_address`

TSS group manager contract address

`tss_staking_slash_contract_address`

TAA slash contract address

database directory will be used

The base directory to store node data

Mantle TSS manager’s websocket url

Listening port for P2P communication

Bootstrap peers the node is going to connect to once it is started

The IP made public to the network

The private key for identifying the node. It is not safe to store the raw private key here in the file, it would be nicer to set it to environment with the prefix ‘TSS\_NODE’,export TSS\_NODE\_PRIVATE\_KEY