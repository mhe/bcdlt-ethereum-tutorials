![BC Tech logo](images/bctech-logo.png)
# Ethereum smart contracts

## Smart contracts

We will now start with deploying a smart contract that is provided to you already. In order to deploy smart contracts, you need to compile them. For this we will install `solc-js` with:

```
sudo npm install -g solc
```

There is a "Hello World!" smart contract provided in the `solidity` sub-folder. Open `FreeBeer.sol` in your favorite text editor and try to understand what it does. The contract is well documented.

The contract has already been compiled. This was done using the following command to generate bith the `.bin` (bytecode) and `.abi` (Application Binary Interface) files:

```
solcjs --bin --abi FreeBeer.sol
```

We will deploy the contract using Alice, and then interact with the contract as Bob. You can use the command line to deploy the contract (instructions for this can be found [here](https://github.com/hidde-jan/eth-private-net#deploying-and-running-smart-contracts), but we will use Mist.

Open Alice's Mist instance and click `Contracts` in the top right. Click `Deploy New Contract`. Select from which account you wish to deploy the contract. For now we will use `Main account`. If you have no Ether on that account, transfer some from any other account (Alice's or Bob's) to that account.

Scroll down a little and click `Contract Byte Code`. In the empty field that appears, copy and paste the contents from `FreeBeer_sol_FreeBeer.bin`.

You can now click `Deploy` (remember, the password for the main accounts was `foobar123`) and your contract will be deployed on the blockchain. You can see it under `Wallets` and scrolling down to `Latest transactions`.

Copy the contract address and open Bob's Mist instance. Click `Contracts` in the top right. This time, click 'Watch contract'. Paste the contract address, give it a name and copy and paste the ABI (Application Binary Interface) from `FreeBeer_sol_FreeBeer.abi`.

Click on the contract and scroll down to `Write to the contract`. Select the function `Gimme Money`, select an account from where to send the money and enter an amount of Ether to enter into the contract. Click `Execute` and confirm the transaction. If you go back to `Wallets` and scroll down to `Latest transactions`, you should see an entry stating `Contract execution` with your transaction.

The Ether you sent to the contract should now appear in the `Main account` account in Alice's Mist instance.

### Testing your smart contracts

If you want to properly test your smart contracts, you can do so using the `Truffle` library. The Truffle library abstracts some of the actions of deploying smart contracts away for you. This means that if you are writing a smart contract you can test if your smart contracts work without having to deploy them every time. You can also write tests to check your smart contracts as part of a continuous integration pipeline.

To install the Truffle library, simply execute:

```
sudo npm install -g truffle
```

Now, change into a directory you wish to use to develop your smart contracts in. Normally you would initiate a Truffle project with:

```
truffle init
```

**However**, we want to have some example files available so we can see how everything works. So instead, initiate an example Truffle project using:

```
truffle unbox metacoin
```

In your Truffle folder, you will find three folders: `contracts`, `migrations` and `test`. You can ignore the `migrations` folder for now. The `contracts` folder will house the contracts you wish to test. The `test` folder will contain your tests. You can write your tests in Solidity, the same language you can write your smart contracts in.

**Note** that Truffle expects that your contract and test files all contain exactly one contract, and that the filename is equal to the name of the contract (so a contract called `ExampleContract` should live in the file `ExampleContract.sol`). If you do not do this, testing will fail.

Since we have already populated our Truffle project with an example project, we can get to testing right away:

```
truffle test
```

This test should pass. You can open the contract and tests in the `contracts` and `test` folder to see how they work. Note that by default Truffle will execute all tests in the `test` folder. If you only want to execute a specific test file, you will have to execute:

```
truffle test /path/to/TestContract.js
```

You can write tests in [JavaScript](https://truffleframework.com/docs/getting_started/javascript-tests), but since you've already written contract in Solidity this may come easier. [You can find an introduction on how to write contracts in Solidity here](https://truffleframework.com/docs/getting_started/solidity-tests).

