![BC Tech logo](images/bcdlt-blocks-UT-500px.png)
# Making an Ethereum network

This tutorial is aimed to help you:

- Set up an environment for writing and testing smart contracts for Ethereum.
- Set up your own private Ethereum network to test your smart contracts.
- Set up a wallet you can use on your own Ethereum network.

And optionally:

- Merge your own private network with those of classmates.


## Prerequisites

This tutorial is written for `Ubuntu Linux 16.04 LTS` and should also work on other Debian based Linux distributions. It should also, without too much hassle, work on MacOS, **but without any guarantees**. If you wish to use MacOS or Windows, please know that we **might not be able to help you**.

This tutorial assumes that you already have a working installation of Ubuntu 16.04 LTS, and will not cover installation and configuration. It also assumes you are familiar with the command line. You needn't do many difficult things, but you should at least know how to use it.

## Setting up

First we need to install any missing packages (just in case they are not yet present on your system):

`sudo apt-get install git curl software-properties-common`

### Setting up Node.js 8.3

Next we need to install `Node.js` (a programming environment based around JavaScript) and it's package manager `npm`. Although there are precompiled packages are available via Ubuntu's package manager by default, these contain an old version. We therefore add Node.js' own repository and install it from there (we will be using the LTS version (8.9) of Node.js):

```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
```

## Running your network

### Setting up a local test network

We will now install `geth`, an implementation of the Ethereum protocol built in [Go](https://golang.org/). We first add its repository, and then install `geth` (which is in the `ehtereum` package in the repository you just added):

```
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
```

Next, we'll download some files that'll help you setting up your local test network. These files have been created by [Vincent Chu](https://github.com/vincentchu/eth-private-net) and improved upon by [Hidde-Jan Jongsma](https://github.com/hidde-jan/eth-private-net). Change into a directory you wish to work in and execute:

```
git clone https://github.com/hidde-jan/eth-private-net.git
```

This will create a sub-directory called `eth-private-net` in the directory you were in. Change into this directory.

The files we just downloaded provide a convenient wrapper around `geth`, so that we don have to worry about the specific executable parameters. However, we encourage you to open the bash file `eth-private-net` in a text viewer and seeing what happens under the hood. The file should be easily understandable.

The default *difficulty* that is used in this wrapper is a little low. Open the file `genesis.json` in your favorite editor (`nano` for example) and change the `difficulty` property of the JSON object to `400000`.

Now we'll need to initialize our own Ethereum test network. We do this by executing the following command:

```
./eth-private-net init
```

This will create a genesis block following the definitions in `genesis.json` (which also defines the initial account balances, you can change them if you like) and create a starting state for the network. We can now start a node with:

```
./eth-private-net start alice
```

**Note** that the `init` command we executed predefines three identities for us: `alice`, `bob` and `lily`. We can start a node for any of those identities using their name. In this case, an 'identity' is nothing more than a 'virtual user' that runs their own network. Think of it as three virtual machines, owned by Alice, Bob and Lily respectively, all running their own private network independently from each other, but with the same genesis block.

Which will show you:

```
Starting node for alice on port: 40301, RPC port: 8101. Console logs sent to ./alice/console.log
```

Followed by a lot of logging. This output may fill quickly fill your screen. The first time you do this geth will have to precompute the *directed acyclic graph* or DAG. This will take a few minutes. Afterwards the node will start mining. In the meantime, you can open a new terminal and continue with the tutorial.

### Installing a wallet

The easiest way to interact with our private blockchain is by using a wallet. We will use the Mist Ethereum wallet. Open a new terminal (so that the network you started earlier keeps running) and install the Mist wallet with:

```
wget -O /tmp/mist.deb https://github.com/ethereum/mist/releases/download/v0.9.3/Ethereum-Wallet-linux64-0-9-3.deb
sudo apt install -f /tmp/mist.deb
```

*Replace `linux64` with `linux32` if you're running a 32-bit version of Ubuntu.*

We will now start the Mist Wallet and tell it to connect to our own private network. To do this, we need to start Mist manually from the command line, telling it to connect to our private network we started with `./eth-private-net start alice`. That told us that node was listening on RPC port 8101. We can use that port to connect to our own private network like so:

```
ethereumwallet --rpc=http://localhost:8101
```

**Note** that in our case, we can ignore all security warnings.

You can click *Launch Application* as soon as the button becomes available. If, after you've started a private node for Alice, the DAG is still being computed, you will not yet see any blocks. Please wait for DAG computation to complete.

As soon as the DAG computation is complete, the node will start mining Ether. You will see this in the Wallet as a constantly increasing balance on your Ether address. Remember that nodes receive Ether for every block they mine.

You can also generate a new address using the Mist wallet, and send Ether up and down between your addresses.

**Please note** that you can set a password for any account that you create yourself. Each identity comes with one address already generated for you. To use those addresses, you can use the password `foobar123`. You will also need this password later on in the tutorial.

### Connecting two nodes

We can now add a second node to our private network. To do this, we open yet another terminal. In this terminal, change into your `eth-private-net` directory and start the node for Bob.

```
./eth-private-net start bob
```

In a fourth terminal, open the Mist Wallet for this node (we can find the RPC port in the beginning of the output of the previous command):

```
ethereumwallet --rpc=http://localhost:8102
```

At this point, you'll have two Wallet windows open, both displaying a different Ether address, and both generating Ether (since both nodes are mining). At this point, however, both nodes are working on their own chain. This is, for example, visible in the different block heights in the Wallet windows.

We will now join the two nodes together. To do this, we need to instruct one of the nodes to connect to the other node to form a network. We can do this by attaching to one of the nodes from our command line and issuing a series of commands.

Open a new terminal (this should be your fifth), change into your `eth-private-net` directory and attach to Alice's terminal:

```
./eth-private-net attach alice
```

This will open a JavaScript console with the Ether node. To connect the two nodes you will need Alice's node ID. You can get this ID with the following command, yielding an output similar to the one below, and exit the Ether (geth) console:

```
> admin.nodeInfo.enode
"enode://7d1a53e8c080c2e96bf0f24e09f1954f613e40e3a886b0d25f3b2ac761d32f2dee07c0a7313ed848d063ae4bb4444c3b1064e3ba0ac1674d5c84a2f60b7b2993@[::]:40301?discport=0"
> exit
```

Now, connect Bobs node to Alice's by opening a JavaScript console with Bob's Ether node and instruct it to connect to Alice's:

```
./eth-private-net attach bob
[...]
> admin.addPeer("enode://7d1a53e8c080c2e96bf0f24e09f1954f613e40e3a886b0d25f3b2ac761d32f2dee07c0a7313ed848d063ae4bb4444c3b1064e3ba0ac1674d5c84a2f60b7b2993@[::]:40301?discport=0")
true
> exit
```

If you now look at the Wallet windows, you will notice that one of them is synchronizing with the new blockchain. After the synchronization is complete, both Wallet windows will show the same block height, and you will notice that one of the addresses lost all its ether. Since only one of the blockchains can be the right one in our new network, all work on the other will have been lost.

You can now transfer Ether between the two addresses by making a transaction between the two.

At this point in the tutorial, both Alice and Bob are mining. We only need one miner on our network, so we'll have Alice stop mining:

```
./eth-private-net attach alice
[...]
> miner.stop()
true
> exit
```

After this, you should only see the balance of Bob's wallets increasing in Mist, since Alice is not mining anymore.

### Pointers

#### Classmate node ID

To connect your node to a classmate, you need to change the node ID you give them. Suppose your node ID is:

```
"enode://7d1a53e8c080c2e96bf0f24e09f1954f613e40e3a886b0d25f3b2ac761d32f2dee07c0a7313ed848d063ae4bb4444c3b1064e3ba0ac1674d5c84a2f60b7b2993@[::]:40301?discport=0"
```

The part behind the `@` is the machine and port where your node should try to connect in the form `ip:port`. In our example the IP is `[::]` and the port `40301`. You will need to change the IP part of your node ID to actual IP of your computer to allow other nodes on a different machine to connect to yours. Your can find your own IP using:

```
ip -4 a | grep inet | grep "130.89"
```

Select the IPv4 address (the part right after `inet` with the netmask (the part behind and including the `/`) and replace the IP part of your node ID with it. Your node ID should start looking something like this:

```
"enode://7d1a53e8c080c2e96bf0f24e09f1954f613e40e3a886b0d25f3b2ac761d32f2dee07c0a7313ed848d063ae4bb4444c3b1064e3ba0ac1674d5c84a2f60b7b2993@130.89.123.234:40301?discport=0"
```

Your classmates can use this node ID to connect with your node over the university network.
