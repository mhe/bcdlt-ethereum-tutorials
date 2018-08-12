![BC Tech logo](images/bctech-logo.png)

# Tutorial Ethereum and Smart Contracts


This tutorial is aimed to help you:

- Set up an environment for writing and testing smart contracts for Ethereum.
- Set up your own private Ethereum network to test your smart contracts.
- Set up a wallet you can use on your own Ethereum network.
- Write a simple smart contract, and interact with it.
- Learn how to write tests for your smart contracts.

And optionally:

- Merge your own private network with those of classmates.

## Next steps

At this point you should be able to:

* set-up a private Ethereum node;
* use Mist wallet to connect to your private node, create address and send Ether between your addresses;
* start multiple local private nodes, and connect them together in a local private Ethereum network;
* deploy smart contracts to your private network, and interact with them;
* test your smart contracts.


You can spend the rest of the tutorial using your knowledge so far:

* writing your own smart contracts and tests;
* connecting your private network with the private network of other students.

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
