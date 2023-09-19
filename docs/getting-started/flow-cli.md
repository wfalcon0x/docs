---
title: Flow CLI Quickstart
sidebar_label: Flow CLI Quickstart
sidebar_position: 2
---

# Flow CLI Quickstart

The Flow CLI (Command Line Interface) is a set of tools that developers can use to interact with the Flow blockchain by managing accounts, sending transactions, deploying smart contracts, running the emulator, and more. This quickstart will get you familiar with its main concepts and functionality.

## Installation

The first thing you'll need to do is install the Flow CLI. If you have [homebrew](https://brew.sh/) installed you can run:

```
brew install flow-cli
```

For other ways of installing, please [refer to the installation guide](../tools/flow-cli/install.md).

## Configuration

Let's first create a directory to setup a project we can work from.

```
mkdir cli-quickstart
```

And then let's navigate to it:

```
cd cli-quickstart
```

Let's first create a `flow.json` file for our project (we won't use it till a later step, but this is a good spot to learn it). The `flow.json` file is a configuration file used by the Flow CLI when interacting with the Flow blockchain. It helps manage various project-specific settings, such as network configurations, account details, contract names and source files, and deployment targets. To create one, run:

```
flow init
```

If you look at `flow.json` now you'll see it's listed access nodes for networks and accounts. The default `emulator-account` create will come in handy when we want to run the emulator.

## Running Scripts

On Flow, scripts are used to read from the chain. There is no state modification. In our case, we are going to read a greeting from a contract deployed to `testnet` called `HelloWorld`. (You can [view the contract here](https://f.dnz.dev/0x9dca641e9a4b691b/HelloWorld))

Let's create a script file:

```
touch script.cdc
```

Then in the script file, let's put the following code:

```
import HelloWorld from 0x9dca641e9a4b691b

pub fun main(): String {
  return HelloWorld.greeting
}

```

In the above example, `0x9dca641e9a4b691b` is the address of the account on `testnet` that the `HelloWorld` contract has been deployed to. (Note: if you'll like to learn more about writing scripts, please [read here](../build/key-concepts/scripts.md)).

To run the script, we'll run this from the CLI:

```
flow scripts execute script.cdc --network testnet
```

You should see the result of the greeting.

## Running the Contract Locally on Emulator

The Flow Emulator is a local, lightweight, and standalone version of the Flow blockchain. It's designed to provide developers with a local environment for testing and development purposes without having to interact with the `mainnet` or `testnet`. This makes it easier and faster to develop, test, and debug smart contracts and dApps.

In order to use it, let's catch our project configuration up with where we are at so far.

Let's create a local version of this contract for the emulator. Run:

```
touch HelloWorld.cdc
```

Then let's put the contract into our project. Copy this to `HelloWorld.cdc`:

```
pub contract HelloWorld {

  pub var greeting: String

  pub fun changeGreeting(newGreeting: String) {
    self.greeting = newGreeting
  }

  init() {
    self.greeting = "Hello, World!"
  }
}

```

The next thing we'll do is add a contracts section to our `flow.json` configuration that will describe our setup. We'll state where the file for the contract lives with `source` and then define `aliases` for the addresses of the deployed contracts.

```
// flow.json

{
...
"contracts": {
  "HelloWorld": {
    "source": "HelloWorld.cdc",
    "aliases": {
      "testnet": "0x9dca641e9a4b691b"
    }
  }
}
...
}
```

We're also going to change the imports of our script so that there are no hardcoded addresses. We'll let CLI figure out how to interact with what network based on our configuration.

```
// script.cdc

import "HelloWorld"

pub fun main(): String {
  return HelloWorld.greeting
}

```

Next, we'll add a deployments section to `flow.json` and define what account we'd like what contract deployed to and on what network. In this case, let's deploy the `HelloWorld` contract to the `emulator` network and on `emulator-account` provided.

```
// flow.json

{
...
"deployments": {
  "emulator": {
    "emulator-account": ["HelloWorld"]
}
...
}
```

Next let's run the emulator in a new terminal with:

```
flow emulator start
```

And then deploy the contract with:

```
flow project deploy
```

Now if we run the following script we should see the result of the script against our emulator deployed contract.

```
flow scripts execute script.cdc --network emulator
```

## Creating an Account and Running a Transaction

To change state on the Flow Blockchain, you need to run a transaction. Let's create a simple transaction file we can use to modify the greeting on the `HelloWorld` contract.

First, create file called `transaction.cdc` from the root of your project:

```
touch transaction.cdc
```

Then copy the following code:

```
import "HelloWorld"

transaction(greeting: String) {

  prepare(acct: AuthAccount) {
    log(acct.address)
  }

  execute {
    HelloWorld.changeGreeting(newGreeting: greeting)
  }
}
```

This will log the account signing the transaction and call the `changeGreeting` method of the `HelloWorld` contract, and pass in the new greeting we want to set. (If you want to learn more about writing transactions, please [read here](../build/key-concepts/transactions.md)).

But in order to run a transaction, an account needs to pay for it. And while we could run a transaction on emulator using the `emulator-account`, let's learn one more command for creating accounts.

The easiest way to create an account using CLI is by running (remember, your emulator should still be running at this point in another terminal):

```
flow accounts create
```

Once that runs, select `Emulator` as the network and give your account the name `emulator-tester`. You'll now see this account in your `flow.json`.

To run a transaction with this new account, you can run the following:

```
flow transactions send ./transaction.cdc "Hello, me" --signer emulator-tester --network emulator
```

You've just modified the state of the Flow Blockchain!

## Next Steps

A great way to dive in deeper is to check out the scaffolds generated by the Flow CLI. They can serve as a great starting point for any project you're trying to create. See how to [create a scaffold here](../tools/flow-cli/index.md).