---
title: Mixin Bot Development Tutorial 3 - Trade Crypto
date: 2021-05-02 21:30:18
tags: ["Tutorial", "Mixin Development"]
---

In the [previous post](https://gitpress.io/@lyric/mixin-bot-development-tutorial-2-interact-with-users), I rewrote a bot that only sends back an echo of user's input into a bot, which responds correctly.

To achieve that, the bot must manage user sessions, to keep tracking the state of conversation with users.

However, the work is not complete yet, the function `handleTransfer` is still not implemented, and we still can not trade crypto in the bot. So let's do the rest in this post.

Before we continue, we need to make some preparations:

## Prepare for Transfers

Because of the complexity of the distributed system, to interact with a distributed ledger and handle the crypto on it is difficult. In Mixin's world, we have the [Mixin Wallet APIs](https://gitpress.io/@lyric/a-quick-glance-into-mixin-development#key-concepts) to simplify the way to handle crypto transfers.

The keystore file you generated from the "Developer dashboard" is the "key" to access Mixin Wallet API. For the full documents of it, please visit [here](https://developers.mixin.one/).

In the keystore file, there is a field named `PIN`, a 6-digits number. It's a basically the same functionality as the PIN for your Mixin Messenger account. PIN is a part of [D3M-PIN](https://vec.io/posts/mixin-messenger-d3m-pin), which is a protection mechanism in Mixin Wallet APIs. There are 2 important advises for it:

1. Don't store PIN in plain-text.
2. Sending transfer with incorrect PIN 4 times in one day will cause the account be locked for 24 hours.

In the bot, I remove the PIN from my store file, and pass it as a command-line parameter.

```go
var (
  ...
	pin    = flag.String("pin", "", "pin of keystore")
)
```
In a real project, you may want to encrypt the PIN and the keystore file in a secret storage, and decrypt them each time you are using them.

And then, we need to add this parameter to execute the bot:

```go
$ ./mixin-tutorial -config ./YOUR_KEYSTORE_FILE -pin YOUR_PIN
```

## Receive Crypto

In the messaging loop, the `h` function uses two conditions to identify the "transfer messages" the bot received.

The first condition is that the category of messages. The second is that the comparison of message.UserID and the bot's clientID (the Client ID is the userID of the bot, you can see it in the keystore file).

```go
if msg.Category == mixin.MessageCategorySystemAccountSnapshot {
  // if the message is a transfer message
  // and it is sent by other users, then handle it
  if msg.UserID != client.ClientID {
    return handleTransfer(ctx, msg)
  }
  // or just drop it
  return nil
} else if ...
```

The reason for the second condition is that in Mixin Messenger, all transfers related this bot will be received in the messaging loop, no matter it comes from other person or sent by the bot itself. So we compare the message's userID and ignore the transfer sent by the bot.

The `handleTransfer` function is called in the message loop if there is a new transfer message. If the bot recognised the purpose of users and all information are seated properly, it will try to trade for you in [4swap](https://4swap.org)'s protocol. If not, the bot will refund the crypto to you.

```go
func handleTransfer(ctx context.Context, msg *mixin.MessageView) error {
	data, err := base64.StdEncoding.DecodeString(msg.Data)
	if err != nil {
		return err
	}

	// Decode the transfer view from message's content
	var view mixin.TransferView
	err = json.Unmarshal(data, &view)
	if err != nil {
		return err
	}

	session := getSession(msg.UserID)
	if session != nil && session.State == UserSessionStateSpecifiedSymbol {
		// has already specified an asset symbol in session
		// send a message and refund
		incomingAsset, err := client.ReadAsset(ctx, view.AssetID)
		if err != nil {
			return err
		}
		todo := fmt.Sprintf(
			"%s -> %s, swap at 4swap.\nPlease check @7000103537 for swap result",
			incomingAsset.Symbol, session.Symbol,
		)
		respond(ctx, msg, mixin.MessageCategoryPlainText, []byte(todo), 1)
		// swap the asset at 4swap.
		if err := mtgSwap(msg.UserID, incomingAsset.AssetID, session.AssetID, view.Amount); err != nil {
			log.Println(err)
		}
		return nil
	}
	// refund directly
	return transferBack(ctx, msg, &view, *pin)
}
```

Here the bot decodes a new object named `TransferView` from the the message's content, that contains all required information for each transfer. In which:

- Type: The type of the transfer.
- SnapshotID: The unique UUID that indicates a transfer. You can search it at [Mixin Network Explorer](https://mixin.space)
- CounterUserID: The opponent ID of this transfer must be a valid user UUID.
- AssetID: The asset's ID of the transfer.
- Amount: The amount of the asset in the transfer.
- TraceID: The unique UUID used by transfer tracking. Only the sender has the permission to see this field.
- Memo: The memo of transfer, we usually use it to store data. By default, only the sender and the receiver will see this field.
- CreatedAt: The time the transfer created.

After the decoding, the bot calls `client.ReadAsset` to read the asset's symbol from Mixin Wallet API, which used in the response. That's all we did to receive the crypto.

Next, let's look at how to send crypto.

## Send Crypto

In Mixin Network, there are two kinds of transactions we can send:

- One is the normal transaction which between two user IDs. It's a transaction wrapper by Mixin Wallet API, we can use `client.Transfer` to send it.
- Another is the "raw" transaction send to the Kernel, which's receiver could be a "Multiple-Signature" address. We can use `client.Transaction` to send it.

Coincidentally, we will use both of them soon. When the bot refund the crypto to the user, it'll create a normal transaction; when it trades at 4swap, it'll create a "raw" transaction.

### Refund the Crypto

Implementing of `transferBack` function is a transfer version of the "echo". The logic of the function is very similar to the logic we used in the first post of this tutorial: The bot will simply send every penny back when it receives some crypto:

```go
func transferBack(ctx context.Context, msg *mixin.MessageView, view *mixin.TransferView, pin string) error {
	amount, err := decimal.NewFromString(view.Amount)
	if err != nil {
		return err
	}

	id, _ := uuid.FromString(msg.MessageID)

	input := &mixin.TransferInput{
		AssetID:    view.AssetID,
		OpponentID: msg.UserID,
		Amount:     amount,
		TraceID:    uuid.NewV5(id, "refund").String(),
		Memo:       "refund",
	}

	if _, err := client.Transfer(ctx, input, pin); err != nil {
		return err
	}
	return nil
}
```

The only important thing of the function is that never use a random UUID in the `TraceID` field of `mixin.TransferInput`. For the reason, you can read [this article](1-using-arbitrary-trace-id-in-transfers) I wrote before:

> If we assign `traceID`  with a random UUID at the beginning, the bot will continuedly send me BTC because it fail to update the schedule in the database. So the loop won't stop and will exhaust all BTC in the bot's account. It could lead to grave consequences in a real project.

Be careful of it!

### Trading Crypto at 4swap

[4swap](https://4swap.org) is a decentralized protocol implement for automated liquidity provision on Mixin Network. It's an efficient trading protocol. By using it, you can "swap" one crypto into another with no gas fee.

4swap provides a Golang SDK, that's make it easy to integrate. I create a new source file named `swap.go` and import `github.com/fox-one/4swap-sdk-go` as the new dependence:

```go
package main

import (
	"context"
	"log"

	fswap "github.com/fox-one/4swap-sdk-go"
	mtg "github.com/fox-one/4swap-sdk-go/mtg"
	"github.com/fox-one/mixin-sdk-go"
	"github.com/gofrs/uuid"
	"github.com/shopspring/decimal"
)

func mtgSwap(receiverID, payAssetID, fillAssetID, amount string) error {
	ctx := context.Background()
	// use the 4swap's MTG api endpoint
	fswap.UseEndpoint(fswap.MtgEndpoint)

	// read the mtg group, you can save it for later using.
	group, err := fswap.ReadGroup(ctx)
	if err != nil {
		return err
	}

	// the ID to trace the orders at 4swap
	followID, _ := uuid.NewV4()

	// build a swap action, specified the swapping parameters
	action := mtg.SwapAction(
		// the user ID to receive the money
		receiverID,
		// an UUID get trace the order
		followID.String(),
		// the asset's ID you are swapping for.
		fillAssetID,
		// leave empty to let 4swap decide the routes.
		"",
		// the minimum amount of asset you will get.
		decimal.NewFromFloat(0.00000001),
	)

	// the action will be sent to 4swap in the memo
	memo, err := action.Encode(group.PublicKey)
	if err != nil {
		return err
	}
	log.Println("memo", memo)

	// send a transaction to a multi-sign address which specified by `OpponentMultisig`
	// the OpponentMultisig.Receivers are the MTG group members of 4swap
	tx, err := client.Transaction(ctx, &mixin.TransferInput{
		AssetID: payAssetID,
		Amount:  decimal.RequireFromString(amount),
		TraceID: mixin.RandomTraceID(),
		Memo:    memo,
		OpponentMultisig: struct {
			Receivers []string `json:"receivers,omitempty"`
			Threshold uint8    `json:"threshold,omitempty"`
		}{
			Receivers: group.Members,
			Threshold: uint8(group.Threshold),
		},
	}, *pin)

	if err != nil {
		return err
	}

	log.Println("tx", tx)

	return nil
}
```

The first step of swapping is switching the endpoint of 4swap. There are [two endpoints](https://github.com/fox-one/4swap-sdk-go/tree/master/docs) in 4swap, we are using the "MTG" version, which is better than another in liquidity.

> [!NOTE]
> **MTG** is a group of node which arrive a consensus on the data or actions. For example, there are 5 bots in 4swap which confirm each trade and manage all assets in 4swap together. It's a way to provide decentralized services among several bots which controlled by different participants. For more information about MTG, please read [this article](https://developers.mixin.one/document/mainnet/mtg).

The second step is reading the MTG receivers and the threshold from 4swap's API. The information will not change frequently, you may want to save it to reduce the number of requests.

The third step is building a swap action. The swap action is like a command tell 4swap what you wanna do. You can simply use `mtg.SwapAction` to build it.

The last two parameters of the `mtg.SwapAction` is the "swap routes" and the minimum of the amount of asset you swap for, I'd like to explain a little here.

- routes: The routes of swapping, which means the "way" this trade will "go though". You may specify own routes for each swapping for different purposes. If it's empty, then 4swap will decide the routes.
- minimum amount: The minimum amount of asset you will get, in decimal type. In a real project, you need to calculate it rather than use the minimum number of Mixin Network (1 satoshi unit).

To put it simple, I just leave the two parameters with an empty string and 1 satoshi unit (0.00000001). But don't do that in a real project.

The last step is invoking the `client.Transaction` to send a raw transaction to 4swap's multi-sig address. It may cost seconds or one minute, depends on the queue size of 4swap.

When 4swap finishes processing your trade, you will receive messages from the bot like this:

![](/mixin-bot-development-tutorial-3-trade-crypto/screenshot.1.png)

![](/mixin-bot-development-tutorial-3-trade-crypto/screenshot.2.png)

## Summary

In this post, I present that how to receive crypto and send crypto in 2 ways.

I simplify the entire process and ignore a lot of conditions and details which make the code robust. So please do not use it in your real project unless you certainly know what you are doing.

But don't worry. I will improve the bot and add some dense code to make it better in future posts.

If you want to contribute to this tutorial and make it better, your help is very welcome. Please fork [this source code](https://github.com/lyricat/mixin-tutorial/) and discuss with me at [Github discussions](https://github.com/lyricat/mixin-tutorial/discussions).