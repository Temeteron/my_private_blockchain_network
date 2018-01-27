# my_private_blockchain_network
Set up your private blockchain network. This tutorial will show you how to create a private blockchain network based on ethereum. Multiple nodes will be added through different machines.

Assume that we have 2 Virtual machines and we want to set a private blockchain network between them.

## 1) Install dependencies (Linux) in all of your PCs/VMs
```
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository -y ppa:ethereum/ethereum
$ sudo apt-get update
$ sudo apt-get install ethereum
```

## Get started with the first node (VM 1)
## 2) Create you CustomGenesis.json file:
This file will create the first block in your chain. It should be the same for all of your nodes
```
{
    "config": {
        "chainId": 13,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "difficulty": "200000000",
    "gasLimit": "2100000",
}
```

## 3) Setup first node
In a terminal type:
```
$ geth init CustomGenesis.json
```
This will init your blockchain exit.

## 4) Run and setup first account
In a terminal type:
```
$ geth console
```
This will open the geth console. Now we must create an account:
```
> personal.newAccount()
```
it will request a password which you should remember. Now you can take the info of your node:
```
> admin.nodeInfo
```
This will result something like that:
```
{
  enode: "enode://b8aa27806af660bfbb51493efb9269e507fd8471e0d53f66f8f7008b4afe5838febc32b3af7274b2939d2864365f86380465e7a2e28c3b94b83aa2d243fb29d9@[::]:30299",
  id: "b8aa27806af660bfbb51493efb9269e507fd8471e0d53f66f8f7008b4afe5838febc32b3af7274b2939d2864365f86380465e7a2e28c3b94b83aa2d243fb29d9",
  ip: "::",
  listenAddr: "[::]:30299",
  name: "Geth/v1.7.2-stable-1db4ecdc/linux-amd64/go1.9",
  ports: {
    discovery: 0,
    listener: 30299
  },
  protocols: {
    eth: {
      difficulty: 1226991947,
      genesis: "0xb6b377d2962f9f9a0a9b3959efbed175fe56e7228b1ecf00973c7bf2cb9bd916",
      head: "0x855f26c9869c61876b6eca8687cb7dce087bd6d11123e7a95ed51e449a8ed0a6",
    }
  }
}
```
where enode is the name of your node, copy that in a txt.

## 5) Complete first node
Stop this terminal with ctrl-c and enter the following command:
```
$ geth --nodiscover --bootnodes enode://b8aa27806af660bfbb51493efb9269e507fd8471e0d53f66f8f7008b4afe5838febc32b3af7274b2939d2864365f86380465e7a2e28c3b94b83aa2d243fb29d9@[::]:30299 --identity mynode-00 --port 30299 --rpc --rpcapi="db,eth,net,web3,admin,personal" --rpcport 8100 --rpcaddr "127.0.0.1" --rpccorsdomain "*" --networkid 13 console
```
with "--nodiscover" we don't let nodes to discover eachother, "--bootnodes" contains the initial node we created, "--networkid 13" is the id that this node is connected, which will be needed to connect other nodes on this blockchain.

## 6) Init second node in another Virtual Machine (VM 2)
Obviously you must install the dependencies in that VM too.

## 7) Init CustomGenesis.json file:
copy the previous genesis file here, then execute the same command:
```
$ geth init CustomGenesis.json
```
## 8) Run and setup account for this node
In a terminal type:
```
$ geth console
```
This will open the geth console. Now we must create an account:
```
> personal.newAccount()
```
it will request a password which you should remember. Now you can take the info of your node:
```
> admin.nodeInfo
```
write down the enode url. This will result something like that:
```
{
  enode: "enode://63ccd1e5d2550e51deda2f01ec6fe3e383972abbcd3d32e667b606671997a3d847dfcde440498b8e5fbe7be70e6e85990f7590e2e04a717fb6dc5020e06ca8de@[::]:30299",
  id: "63ccd1e5d2550e51deda2f01ec6fe3e383972abbcd3d32e667b606671997a3d847dfcde440498b8e5fbe7be70e6e85990f7590e2e04a717fb6dc5020e06ca8de",
  ip: "::",
  listenAddr: "[::]:30299",
  name: "Geth/v1.7.2-stable-1db4ecdc/linux-amd64/go1.9",
  ports: {
    discovery: 0,
    listener: 30299
  },
  protocols: {
    eth: {
      difficulty: 1226991947,
      genesis: "0xb6b377d2962f9f9a0a9b3959efbed175fe56e7228b1ecf00973c7bf2cb9bd916",
      head: "0x855f26c9869c61876b6eca8687cb7dce087bd6d11123e7a95ed51e449a8ed0a6",
    }
  }
}
```

## 9) Complete second node
Stop this terminal with ctrl-c and enter the following command:
```
$ geth --nodiscover --bootnodes enode://b8aa27806af660bfbb51493efb9269e507fd8471e0d53f66f8f7008b4afe5838febc32b3af7274b2939d2864365f86380465e7a2e28c3b94b83aa2d243fb29d9@[::]:30299 --identity mynode-00 --port 30299 --rpc --rpcapi="db,eth,net,web3,admin,personal" --rpcport 8100 --rpcaddr "127.0.0.1" --rpccorsdomain "*" --networkid 13 console
```

## 10) Connect your nodes manually:
If your VMs are connected to a private network you must first get the external ip with:
```
ip addr | awk '/inet / {sub(/\/.*/, "", $2); print $2}'
```
This will result something like this:
```
127.0.0.1
83.212.103.216
```
where the second address is the ip we want.
Thus, to connect those 2 VMs execute in the terminal of the second VM (or in the first with different enode url and ip):
```
admin.addPeer("enode://b8aa27806af660bfbb51493efb9269e507fd8471e0d53f66f8f7008b4afe5838febc32b3af7274b2939d2864365f86380465e7a2e28c3b94b83aa2d243fb29d9@83.212.103.216:30299")
```
If your machines were in the same local router, then you should execute:
```
admin.addPeer("enode://b8aa27806af660bfbb51493efb9269e507fd8471e0d53f66f8f7008b4afe5838febc32b3af7274b2939d2864365f86380465e7a2e28c3b94b83aa2d243fb29d9@[::]:30299")
```

## Check peers and mine
You can check if the nodes are now connected with:
```
$ admin.peers
```
You can start mining at any node with:
```
miner.start()
```
and stop the mining with:
```
miner.stop()
```

## Create more nodes in the same VMs
You can create more nodes in the same VMs but you must specify a different dir where all the data will be saved.
The extra args you need to use are the datadir: 
```
--datadir mydir
```
and the port which should be different from the default 30303:
```
--port 30304
```

An example:
```
$ geth --datadir mydir init CustomGenesis.json
$ geth --datadir mydir console
> personal.newAccount()
> admin.nodeInfo
Ctrl-C (stop terminal
$ geth --datadir mydir --nodiscover --bootnodes enode://b8aa27806af660bfbb51493efb9269e507fd8471e0d53f66f8f7008b4afe5838febc32b3af7274b2939d2864365f86380465e7a2e28c3b94b83aa2d243fb29d9@[::]:30299 --identity mynode-00 --port 30299 --rpc --rpcapi="db,eth,net,web3,admin,personal" --rpcport 8100 --rpcaddr "127.0.0.1" --rpccorsdomain "*" --networkid 13 console
```
If you are in the same machine with the node you want to connect you could use this:
```
admin.addPeer("enode://b8aa27806af660bfbb51493efb9269e507fd8471e0d53f66f8f7008b4afe5838febc32b3af7274b2939d2864365f86380465e7a2e28c3b94b83aa2d243fb29d9@[::]:30299)
```
or you can change the [::] with the external ip of the node.
