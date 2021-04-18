---
title: A Quick Glance into Mixin Development
date: 2021-04-17 15:09:19
tags: ["Mixin", "Development"]
---

# A Quick Glance into Mixin Development

This article summarizes the most important concepts of Mixin Development, in plain engineer language.

If you are a programmer, and you are looking for an article about Mixin development, you can’t go wrong with this one, the very first guide to Mixin development.

## Key Concepts

### Mixin Network

Mixin Network is the fundamental, provides cryptocurrencies services. In a narrow sense, Mixin Network is the main net, aka the Kernel. However, in a board sense, it should also include several components which help Kernel providing a complete service to the whole Mixin Network. They are Domain, Wallet APIs and all MTGs.

**Kernel**, aka the main net of Mixin

In functional view, Kernel is a simple distributed ledger. Therefore, the Kernal guarantees it will synchronize all raw transactions in every node of Kernel and builds network consensus among all nodes.

The Kernel is open source and have already run for 3 years.

**Domain**

A Domain is the bridge between Kernel and all supported chains. Domain handles all received deposits and let them go into Mixin Network, also submit all withdrawals requests from Mixin Network to other chains.

According to the roadmap, Mixin core team controls the only public Domain, and it’s not open source yet. If you setup a new Domain upon the Kernel, the new Domain will be incompatible with the official one.

**Wallet APIs**

Develop with distributed systems are always complex, therefore, interact with Kernel and Domain is a hard. To simplify the development, Mixin core team provide the Wallet APIs to developers.

With the APIs, developers can easily unleash the power of Mixin Network with their dApps.

The Wallet APIs couples with the Domain. Because of that, it’s not open source either. Anyone can build an alternative based on the Kernel if them don’t like the official APIs.

### Mixin Messenger

Mixin Messenger is an instant message App which using Signal protocol to send and receive end-to-end encrypted messages.

There is a built-in cryptocurrency wallet in Mixin Messenger, the connection to Mixin Network. This wallet is a basic account of Mixin Network, which can transfer and manage all assets supported by Mixin Network.

To put it simple:

- Mixin Messenger provides users secure messaging services.
- Each Messenger user has a unique account on Mixin Network.
- The account is a regular account of Mixin Network, but messaging services connect it with the users and give users a good interface.

For the wallet part, we can consider Mixin Messenger is a dApp of Mixin Network.

**Messenger Bot**

Messenger bot is a kind of special user in Messenger service. You can talk with a bot like how you talk with your friends.

Start with a bot is an excellent choice, because in most cases you don’t have to interact directly with the Kernel. Anyone can create a bot from the developer dashboard. I will talk about the bot development later.

## Where to Start

You can do a lot on Mixin Network. It all depends on what do you want.

### Dive into the Kernel

The basic logic of Kernel is simple, but it’s a bit hard to understand full details about it, especially with a lack of documentation.

If you want to build something with the kernel, or create a fork of Mixin Network with some modifications you like, you’d better read the source code.

Nevertheless, there are still some documents that useful:

- [Sync full node](https://github.com/MixinNetwork/developers.mixin.one/blob/main/developers/src/i18n/en/document/mainnet/tutorials/sync-full-node.md) - run mixin node in sync mode, sync all snapshots from the Kernel.
- [Setup a testnet](https://github.com/mixinnetwork/mixin/) - run mixin node in lcoal machine.

### Interact with the Kernel

The Mixin Network has run steadily for 3 years, you can develop services based on it without running a custom network.

As I mentioned above, to build a custom Domain and an alternative API layer are possible. What you need to know is the mechanism of Kernel, and how to interact with Kernel: includes sending transaction, sync snapshots, sending multi-signatures, etc.

- [_examples/mixinnet](https://github.com/fox-one/mixin-sdk-go/tree/master/_examples/mixinnet) - an example that interact with the Kernel.
- [_examples/nodemonitor](https://github.com/fox-one/mixin-sdk-go/tree/master/_examples/nodemonitor) - an example that shows how to monitor specified nodes of Kernel, via the RPC interface of Kernel nodes.
- [the source code of Mixin.network](https://github.com/fox-one/mixin.network) - an open source 3rd-party explorer for Mixin Network.

### Create a Messenger bot

Messenger bot is very similar to Telegram bot. It’s some code running on a server and interact with human being users with Messenger API.

Usually, people interact with bots in a webview, or just chat with bots. Because of that, the bot should host a web service or respond to incoming messages to get its work done.

For example, if you want to build a trade bot which provides the basic trade feature between two assets, you need to understand the messaging loop of messaging services. Because this bot will send messages, receive and handle incoming messages and transfers, and get user’s information through OAuth flow.

To create a bot, the first thing you need to do is that visiting [Developer dashboard](https://developers.mixin.one/dashboard) and create a dApp. You can name your dApp at the dashboard, and you can also download the important keystore file at "app session" of the "secret" panel.

The keystore is the only thing to operate your bot, and the associated account, the money. Please consider the keystore is the private key of your bot and keep it in safely.

[Fox.ONE](https://fox.one) has some simple example codes that show the basic functions of bot, you can check their source code at GitHub:

- [_example/blaze](https://github.com/fox-one/mixin-sdk-go/tree/master/_examples/blaze) - an example that shows how to receive messages from messaging service and reply each message it received.
- [_examples/oauth](https://github.com/fox-one/mixin-sdk-go/tree/master/_examples/oauth) - an example that shows exchange the access token with the OAuth code and gets current user's profile information.
- [_examples/oauth_ed25519](https://github.com/fox-one/mixin-sdk-go/tree/master/_examples/oauth_ed25519) - an example that like the above, but use an ED25519 key to secure the OAuth flow from replay attack or corrupted connection.


### Integrate with Mixin

Another common scene is that your business runs well, but you want to make it cooler with cryptocurrency. So you need to integrate your business system with Mixin.

The first thing you need to do is same as above, creating a bot. And then, it's time to think about the way to integrate with Mixin.

For example, if you have a CMS for multiple users, you want to put a donate button at the bottom of each post and receive money from audiences; you need to learn to create accounts for each author, manage accounts' keystores, sync and handle snapshots for each author in the CMS to transfer money to them.

The following examples are also handful for your goal.

- [_examples/wallet](https://github.com/fox-one/mixin-sdk-go/blob/master/_examples/wallet/main.go) - an example that shows how to operate an account of Mixin Network, includes verifying and changing PIN, transferring assets, reading transfers and snapshots, creating accounts by a keystore.
- [_example/echo_proxy](https://github.com/fox-one/mixin-sdk-go/tree/master/_examples/echo_proxy) - an example that shows how to provide a proxy service to forward all requests to api.mixin.one

If you are familiar with how webpage connect with Ethereum via MetaMask, please try Fennec, the open source browser wallet on Mixin Network.

- [Fennec](https://github.com/fox-one/fennec) - the source and the [demos](https://github.com/fox-one/fennec#4-interact-with-your-mixin-dapp)

### Make something decentralized

By default, the single keystore controls the messenger bot’s account, and most original existed services are centralistic either. Although we benefit a lot from decentralization, to make things decentralized is hard, until MTG.

MTG is short for “Mixin Trusted Group”, is a design pattern based on Mixin Network’s multi-sign feature. Using MTG, we can not only manage assets with multiple participators but also build consensus with others.


- [_example/multisig](https://github.com/fox-one/mixin-sdk-go/tree/master/_examples/multisig) - an example that shows how to create a multi-signature, sign a multi-signature, and send raw transaction to the Kernel.
- [I'm Pando](https://github.com/fox-one/pando) - a decentralized financial network built with the MTG technology, and its underlying financial algorithm is inspired by [MakerDao](https://makerdao.com) and Synthetix.

### Play with dApps

There are many dApps provide useful services on Mixin Network, some of them also provide programmable interface except the standard UI.

For example, 4swap is a decentralized protocol implement for automated liquidity provision on Mixin Network and it provides both APIs and SDK. Integrate with 4swap, people will exchange assets in your bot or App.

Here I list some dApps with APIs or SDK.

- [4swap SDK](https://github.com/fox-one/4swap-sdk-go/) - with the SDK, you can easily swap one asset into another with swap liquidity providers.
- [Exin Core](https://github.com/exinone/exincore) - an exchange proxy allow you exchanging assets with other exchanges like Binance and Huobi.
- [PrsDigg API](https://github.com/baizhiheizi/prsdigg#api) - a simple API set to get your articles on PrsDigg and publish new articles at PrsDigg.

## Conclusion

As we saw, blockchain or distributed ledger development is hard to get started, but both Mixin core team and other teams simplifies the development of Mixin by good-design architecture and providing APIs and SDK for different level.

Knowing this information speeding up the process and ease the pressure of a developer who otherwise would need to understand everything about the complex and difficult distributed system from scratch for a simple dApp.

As a developer, I will keep updating [this article](https://github.com/lyricat/lyric.im/tree/master/source/_posts/a-quick-glance-into-mixin-development.md). Please contact me if you have any suggestion about it.