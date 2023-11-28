---
title: Builders / Developers
description: FAQ
---

## What is the expected TPS (transactions per second) for the forseeable future?

Not all transactions are equal, so throughput numbers alone doesn't tell the whole story.
Flow’s transaction throughput depends on a variety of factors, such as networking bandwidth available to the nodes and optimizations in our implementation. The dominant factor is transaction complexity, impacted by the number of instructions your transaction executes, the number of ledger reads and writes your transaction performs and how many signatures have to be checked to confirm that your transaction has the required privileges. For benchmark loads of simple token balance transfers (essentially an addition and subtraction plus two signature verifications), our current implementation easily sustained a throughput of significantly more than 100 tps. However, we observed that transactions on Mainnet are generally considerably more complex (requiring several signature verifications; many ledger reads and writes; and running relatively complex computations). For the near-term future, 100tps seems like a realistic magnitude. We are working hard towards sustaining 100tps of the kind which are currently run on Mainnet. If your transactions are much simpler than the average Mainnet transaction, Flow potentially already satisfies your desired throughput. The best way to find out would be to test a benchmark set of your specific transactions on TestNet.

## Does Flow have a block explorer?

There are two block explorers live today. You can find them here:

- https://flowdiver.io/
- https://flow.bigdipper.live/

## How can I connect to and query the Access Nodes? What data is available there?

At the protocol level, you can connect to an access node via GRPC. We provide JavaScript and Golang SDKs to do this for you.

Once you have connected to an access node, you can fetch information regarding accounts, contracts, blocks, collections, transactions, and events. You can also execute scripts to query the current state of the Flow blockchain.

### Available Data

#### Accounts, Contracts, Blocks, Collections, Transactions, Events

These types are documented on the [Access API](/http-api) page.

The SDKs expose Flow data as types. For example the Go Flow Block data type implementation can be found here:

[https://github.com/onflow/flow-go-sdk/blob/master/block.go](https://github.com/onflow/flow-go-sdk/blob/master/block.go)

You can query historical data and fetch contract code using the SDKs.

### Scripts

Flow allows you to actively query the state of the blockchain using scripts written in the Cadence programming language. You can find out more about Cadence here:

[cadence](../../../../build/guides/smart-contracts/cadence.md)

Running scripts and parsing their output is supported by the SDKs.

Scripts can access multiple contracts and accounts, calculate values, and ensure that data is correct using Cadence's type system. They can only access the _current_ state of the blockchain.

### Using The SDKs

#### JavaScript

You can download FCL (Flow Client Library) here:

[https://github.com/onflow/fcl-js](https://github.com/onflow/fcl-js)

To connect to an access node you will need to provide a URL to the SDK.
[testnet and mainnet](../access-api.md)

Here are examples of querying an access node at the bottom of this page:

[https://github.com/onflow/fcl-js/tree/master/packages/sdk](https://github.com/onflow/fcl-js/tree/master/packages/sdk)

Here are examples of creating a script and Cadence data types:

[https://github.com/onflow/fcl-js/tree/master/packages/types#scripts](https://github.com/onflow/fcl-js/tree/master/packages/types#scripts)

And here are examples of parsing script output:

[https://github.com/onflow/fcl-js/tree/master/packages/sdk/src/decode](https://github.com/onflow/fcl-js/tree/master/packages/sdk/src/decode)

#### Go

You can download the Go SDK here:

[https://github.com/onflow/flow-go-sdk](https://github.com/onflow/flow-go-sdk)

To connect to an access node you will need to provide a URL to the SDK.
**testnet: access.devnet.nodes.onflow.org:9000
mainnet: access.mainnet.nodes.onflow.org:9000**

You can find examples of querying an access node at the bottom of this page:

[https://github.com/onflow/flow-go-sdk/blob/master/README.md](https://github.com/onflow/flow-go-sdk/blob/master/README.md)

You can find examples using the emulator that can be adapted to use an access node here:

[https://github.com/onflow/flow-go-sdk/blob/master/examples/get_events/main.go](https://github.com/onflow/flow-go-sdk/blob/master/examples/get_events/main.go)

And here is an example of using a script:

[https://github.com/onflow/flow-go-sdk#executing-a-script](https://github.com/onflow/flow-go-sdk#executing-a-script)

#### Other Programming Languages

For an example of implementing access node communication using another programming language, see "Interact with Flow using Ruby" by Daniel Podaru:
[https://www.onflow.org/post/interact-with-flow-using-ruby](https://www.onflow.org/post/interact-with-flow-using-ruby)

## How do apps consume events? How do events work?

Flow transactions can emit informative "events" containing data intended to be used by off-chain observers. Events can be used to trigger backend or UI events, for example.

Note that a single transaction may emit many events, and that the order of events may surprise you if a non-standard transaction is being used. Event parameters may be optional, which means that they might be nil in some scenarios. All of this means that you must be careful when parsing events.

### Defining Events

Events are implemented within Flow smart contracts using the Cadence programming language.

You can find out more about events in Cadence here:

[cadence/language/events/](https://cadence-lang.org/docs/language/events)

As an example of the kinds of information events can contain, see the documentation of the events that the staking protocol emits:

[staking/events](../../../../references/run-and-secure/staking/07-staking-scripts-events.md)

### Consuming Events

To consume events, you must query a Flow Access Node and specify the type of event and the range of blocks you wish to fetch those events from. You can then parse any returned events and handle the information that they contain.

### Using Go

Once you connect to an Access Node using the Go SDK you can query for events.

This is documented here:

[https://github.com/onflow/flow-go-sdk#querying-events](https://github.com/onflow/flow-go-sdk#querying-events)

And there is an example of usage here:
https://github.com/onflow/flow-go-sdk/blob/master/examples/get_events/main.go
[https://github.com/onflow/flow-go-sdk/blob/master/examples/get_events](https://github.com/onflow/flow-go-sdk/blob/master/examples/get_events)

### Using JavaScript

Once you connect to an Access Node using FCL (Flow Client Library) you can query for events.

This is documented here:

[https://github.com/onflow/fcl-js/tree/master/packages/sdk#getevents-usage](https://github.com/onflow/fcl-js/tree/master/packages/sdk#getevents-usage)

### Using Other Programming Languages

Currently, we only provide Go and FCL-JS (Flow Client Library) for Flow. For an example of accessing the Flow blockchain and consuming events from it using another programming language, see "Interact with Flow using Ruby" by Daniel Podaru:

[https://www.onflow.org/post/interact-with-flow-using-ruby](https://www.onflow.org/post/interact-with-flow-using-ruby)

## What is FCL?

FCL (Flow Client Library) enables applications to easily integrate with all FCL-compatible wallets and other services (e.g. profiles, private information, notifications). This offers developers a strong foundation to compose their apps with existing building blocks. FCL is currently supported for browser applications, and will be extended to other platforms.

With FCL, you can:

- Integrate all compatible wallets without any custom code or code injection
- Authenticate users
- Query users' Flow accounts
- Send transactions (e.g. initializing resources, sending assets, purchasing, etc.)
- Sign transactions through wallet integration to avoid key management (especially helpful for non-custodial apps)

## How do I access mainnet?

You can find details about accessing Flow mainnet here [/http-api](/http-api).

The access is via a GRPC interface. Transactions can also be submitted using the GRPC [SendTransaction call](/http-api#tag/Transactions/paths/~1transactions/post). A GRPC client written in any language should be able to talk via the Access API. We have a [Go SDK](https://github.com/onflow/flow-go-sdk) and [FCL-JS (Flow Client Library)](https://github.com/onflow/fcl-js) that can be used if you are using a Go based or a JS based client respectively.

## What's the difference between a mainnet spork and testnet spork? How do sporks affect TopShot?

The Mainnet and Testnet are two separate networks. The difference is which network will be undergoing the down time. Since the main application for NBA TopShot exists on Mainnet, only Mainnet sporks will affect the main applications uptime.

Currently NBA Top Shot runs on Mainnet and NBA Top Shot test app runs on Flow Testnet.

## How much will Gas cost on Flow?

Starting on October 16, 2021 at 7:30am PT (2:30pm UTC), there will be a flat fee of 0.00001 FLOW applied to every transaction submitted to the network. Transaction Fees are subject to change. In the event that a change does occur, the community will be informed ahead of implementation. These fees are for spam prevention and will be low and fixed for all transactions.

## How can I deploy a contract?

To deploy a contract, please follow the steps outlined in the [Dapp Deployment guide](../../../../tools/flow-cli/accounts/account-add-contract.md).

## How can I create my first account for mainnet?

New accounts can be created using Flow Port. You can [follow these guidelines to get a new mainnet account](../flow-port/index.md#creating-an-account).

## How can I deploy a contract to mainnet?

Please review the [Dapp Deployment guide](../../../../tools/flow-cli/accounts/account-add-contract.md) for all details.

## Is there a testnet/devnet?

There is an access node for you to develop against on the testnet/devnet. You can learn more about it [here](../../../../references/flow-networks)

## Is there a public node?

Yes, an access node is publicly accessible to submit transactions and read data from the blockchain. If you’d like to access the devnet access node to build against, you can do so [here](../../../../references/flow-networks/accessing-testnet.md#accessing-flow-testnet)

## Is it possible to add multiple public keys to a given account/address so that it can be controlled by more than one private key?

Yes, accounts support multiple, weighted keys, [here](https://cadence-lang.org/docs/language/accounts)
using `AuthAccount`’s `fun addPublicKey(_ publicKey: [UInt8])`and <br/>`fun removePublicKey(_ index: Int)` functions.

## How do keys and accounts work on Flow?

Accounts are created with associated keys. There can be multiple keys on an account. To execute transactions from the account, a total of 1000 weight keys need to sign. The account holds a field for FLOW balance. When transactions move flow, that balance is updated by the protocol. The account also holds place for storage and contract code.

FLOW supports a variety of signature schemes for adding keys to an account.

Details: [concepts/accounts-and-keys](../../../../build/basics/accounts.md)

## How can I see what Fungible-Tokens an account has?

If you know the /public/ storage path to the FungibleToken.Balance capability for a particular FT vault type, you can borrow that and check its balance. If you wish to know which vaults an account has, you will currently have to check a list of well-known paths. There is an issue that may help with this in future - [https://github.com/onflow/cadence/issues/208](https://github.com/onflow/cadence/issues/208)

## How can I see what NFTs an account has?

If you know the /public/ storage path to the NonFungibleToken.CollectionPublic capability for a particular NFT type, you can borrow that and call getIDs() on it. If you wish to know which token collections an account has, you will currently have to check a list of well-known paths. There is an issue that may help with this in future - [https://github.com/onflow/cadence/issues/208](https://github.com/onflow/cadence/issues/208)

## How can I see what resources an account has?

Flow doesn't yet provide functionality to inspect all of the resources on an account, but it is possible to execute a Cadence script that checks for resources at known storage paths.

## How do I create a Flow account if I do not have a service account?

Instructions to generate an address are here: [flow-go-sdk#create-accounts](../../../../tools/clients/flow-go-sdk/index.mdx#create-accounts). You don't need a service account.

## Is there a tutorial about how to access flow testnet? From scratch, getting testnet, Flow token etc..?

Yes: [testnet-deployment](../../../../build/guides/smart-contracts/deploying.md)

## Can you query events between a block range?

You can query the access API to get events for a block range. See Access API spec here: [/http-api](/http-api).

## Where can I follow feature releases on Flow?

You can follow Flow node software releases here: [https://github.com/onflow/flow-go/releases](https://github.com/onflow/flow-go/releases).