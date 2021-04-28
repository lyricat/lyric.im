---
title: Mixin bot development tutorial 1 - Run a bot from scratch
date: 2021-04-28 14:21:18
tags: ["tutorial", "mixin development"]
---

Recently, I published an article "[A Quick Glance into Mixin Development](https://gitpress.io/@lyric/a-quick-glance-into-mixin-development)" which summarizes Mixin development.

As I mentioned in that article, if you want to create something on Mixin, to start with a bot is an excellent choice. Because in most cases, you don’t have to interact directly with the Mixin Kernel, you can focus on the users, the user experiences, and the business.

I am going to arrange a series of articles about the bot development of Mixin. In the tutorial, I'll introduce the following topics to help you better understand to Mixin development:

1. How bots work on Mixin Messenger and Mixin Network
2. How to interact with users by messages.
3. How to interact with users by a web UI.
4. How to transfer money.
5. How to exchange one asset into Bitcoin.

I will present all of those topics on the real world code [at Github](https://github.com/lyricat/mixin-tutorial), so you are free to fork and modify it.

## Prerequisites

To read the tutorial, there is an only prerequisite: you must have **some programming experience.**

Though this tutorial's code is all written in [Go language](https://golang.org), but I don't think you should be familiar with the [Go language](https://golang.org). I believe any skillful programmer will can read and write in Go language by following the [official Go tutorial](https://golang.org/doc/tutorial/getting-started) in several days.

Of course you can develop Mixin bot in any language you like, and [there are several SDKs](https://github.com/lyricat/awesome-mixin) written by different languages like Javascript, Kotlin and PHP, but for me, Go is a better choice base on some reasons:

1. The quality of [mixin-sdk-go](https://github.com/fox-one/mixin-sdk-go) is pretty good, much better than the other languages. It's a full-feature SDK with good examples. Actually, this tutorial will start with an example from this SDK.
2. In the advance part, I'd like to introduce that how to trade in a bot. That means I need to use [4swap SDK](https://github.com/fox-one/4swap-sdk-go), which is also written by Go language.

I assume every reader here has already setup a development environment of Go language, includes the compiler and the tool-chain of go, a code editor, etc.

## Be a Mixin developer

The first thing you need to do is to be a developer of Mixin.

**Step 1: Visiting [Developer dashboard](https://developers.mixin.one/dashboard)**

You need to install Mixin Messenger and sign up at first. So download it from [Mixin's official website](https://mixin.one).

Visit the [developer dashboard](https://developers.mixin.one/dashboard) on a desktop browser. There will be a QRCode, scan with your Mixin Messenger App to sign in it.

**Step 2: create a dApp**

According to the rules, the first two dApps are free, so you don't need to pay for them. Click the button at the top left to create a dApp.

Name your dApp with "All to BTC" at the "Information" tab, and upload a picture as the bot icon.

Switch to "Secret" tab, click "generate a new secret" to get an **App secret**. Copy it and keep it in a safe place.

Stay in the "Secret" tab, click "Ed25519  session" to get a **keystore**, which is a json. Download it and save it in a safe place either.

Please keep the App secret and keystore file in safely because they are the only important thing to manage your bot and the money in your bot.

Search the Mixin ID of the bot in Mixin Messenger, it would start with "7000" and like "7000XXXXXX". Tap the search result and add your bot as contact.

Now you are a Mixin developer, let's start.

## Start from a simple example

I will start the whole tutorial base on a really simple example from [mixin-sdk-go](https://github.com/fox-one/mixin-sdk-go/tree/master/_examples/blaze). It is an "echo" program which simply replies everything you send to it.

![A screenshot here](/mixin-bot-development-tutorial-1-run-a-bot-from-scratch/screenshot.jpg)

Open terminal and create a directory at your workspace:

```bash
$ mkdir mixin-tutorial
$ cd mixin-tutorial
```

and initialize a Go module:

```bash
$ go mod init YOUR_GITHUB_REPO_NAME
```

for example, I run the command like that:

```bash
$ go mod init github.com/lyricat/mixin-tutorial
```

this command will generate a file named "go.mod" automatically, which contains the dependencies of our example.

Check out the example code of mixin-sdk-go [here](https://github.com/fox-one/mixin-sdk-go/blob/master/_examples/blaze/main.go), you will see a single Go file. Copy everything of the file and save it with name "main.go" in the directory.

Here is the content of "main.go" with comments:

```go
package main

import (
	"context"
	"encoding/json"
	"flag"
	"log"
	"os"
	"time"

	"github.com/fox-one/mixin-sdk-go"
	"github.com/gofrs/uuid"
)

var (
	// Specify the keystore file in the -config parameter
	config = flag.String("config", "", "keystore file path")
)

func main() {
	// Use flag package to parse the parameters
	flag.Parse()

	// Open the keystore file
	f, err := os.Open(*config)
	if err != nil {
		log.Panicln(err)
	}

	// Read the keystore file as json into mixin.Keystore, which is a go struct
	var store mixin.Keystore
	if err := json.NewDecoder(f).Decode(&store); err != nil {
		log.Panicln(err)
	}

	// Create a Mixin Client from the keystore, which is the instance to invoke Mixin APIs
	client, err := mixin.NewFromKeystore(&store)
	if err != nil {
		log.Panicln(err)
	}

	// Prepare the message loop that handle every incoming messages,
	// and reply it with the same content.
	// We use a callback function to handle them.
	h := func(ctx context.Context, msg *mixin.MessageView, userID string) error {
		// if there is no valid user id in the message, drop it
		if userID, _ := uuid.FromString(msg.UserID); userID == uuid.Nil {
			return nil
		}

		// The incoming message's message ID, which is an UUID.
		id, _ := uuid.FromString(msg.MessageID)

		// Create a request
		reply := &mixin.MessageRequest{
			// Reuse the conversation between the sender and the bot.
			// There is an unique UUID for each conversation.
			ConversationID: msg.ConversationID,
			// The user ID of the recipient.
			// Our bot will reply messages, so here is the sender's ID of each incoming message.
			RecipientID: msg.UserID,
			// Create a new message id to reply, it should be an UUID never used by any other message.
			// Create it with a "reply" and the incoming message ID.
			MessageID: uuid.NewV5(id, "reply").String(),
			// Our bot just reply the same category and the sam content of the incoming message
			// So, we copy the category and data
			Category: msg.Category,
			Data:     msg.Data,
		}
		// Send the response
		return client.SendMessage(ctx, reply)
	}

	ctx := context.Background()

	// Start the message loop.
	for {
		// Pass the callback function into the `BlazeListenFunc`
		if err := client.LoopBlaze(ctx, mixin.BlazeListenFunc(h)); err != nil {
			log.Printf("LoopBlaze: %v", err)
		}

		// Sleep for a while
		time.Sleep(time.Second)
	}
}
```

The source code is pretty straightforward, but I won't explain the detail about it. Let's run it in the real world and talk about the code later.

## Run the example

There are only two dependencies in the example. Let's install them:

```bash
$ go get github.com/fox-one/mixin-sdk-go
$ go get github.com/gofrs/uuid
```

and build the example with the command:

```bash
$ go build
```

It will generate an executable file in the current directory called "mixin-tutorial".

Assume your keytore file is "keystore.json", copy it to the directory and run:

```bash
$ ./mixin-tutorial -config ./keystore.json
```

The program will run and wait for your message.

Now leave it running and open Mixin Messenger, send some text to your bot. You'll see it respond to you with the same content you just sent to it.

## Explain the example code

The logic of the example is pretty straightforward, we can summarize the entire code as following process:

1. parse the command-line parameters
2. load keystore from file and initialise a client
3. create a function to handle all incoming messages
4. start the message loop keep the program running forever

I pre-pended some comments for every single statement in the example. You can check the source code for details.

Besides the comments, I will explain some key concepts in this article:

**Message structure**

When you are going to send or receive a message, you must construct a message structure.

In the [official document](https://developers.mixin.one/document/bot/api/messages/send), it said there are several of fields to be considered; they are:

- **conversation_id**: the unique UUID to identify each conversation. If you don't know is it existed or not, you can use `client.CreateConversation` to create one. It will create a conversation automatically once you send something to the bot, or the bot chat to you
- **recipient_id**: the user's ID to receive this message. It should be an UUID. Each Mixin Messenger user has a user ID. You can use `client.SearchUser` to search for it with your numeric Mixin ID.
- **message_id**: the unique UUID to identify a message. You must create an unused UUID for each new message you created. It's a good practise to create the message_id with the information related with your business.
  In this example, all response messages' ID are modification of the incoming's ID.
- **category**: there are [several categories](https://developers.mixin.one/document/bot/api/messages/category) supported by Mixin Messenger. But this time, I just ignore it.
- **representative_id**: If the bot giants another's permission, it's possible to send a message to someone with another user's identity. It's optional and would be useful in sometimes. But this time I just ignore it either.
- **quote_message_id**: You can quote an existed message by specifying the message's ID. It's optional too.
- **data**: the content of the message. It should be base64 encoded. For PLAIN_TEXT message, you can simply encode the text with base64 and put it in `data`.

In this example, I copy the `conversation_id`,  `category` and `data` from the incoming message.

The conversation has already existed because user will send a text to the bot so I don't need to create it.

The bot always replies the same content, so the category and data are same as the incoming message.

The message ID in Mixin Messenger is unique, so I use `uuid.NewV5` to create a new one with the incoming's message ID and the string "reply", which will always generate a new UUID based on the incoming ID, but different.

**Handle message loop**

To continue receiving messages, I called `client.LoopBlaze` with `mixin.BlazeListenFunc` in the example.

This method will establish a connection between the bot and the messaging service which you can read new messages from it and answer them.

mixin-sdk-go simplifies the process. What you need to do is that prepare a function to handle the incoming messages.

You will see the `h` function will be invoked each time the loop active. So when a new message arrivals, it will be passed to `h` as the parameter `msg`, which is a ref of `mixin.MessageView`. You can read the content of `msg` by checking each field of `mixin.MessageView`.

In the end of the function, I invoked `client.SendMessage` to send a message back. Before that, I create own message by creating a `mixin.MessageRequest` struct, which includes the information I want to respond.

## Summary

In this article, I introduce the preparations for developing a Mixin bot, and compile, run and explain an example from mixin-sdk-go.

Next time I will go further, this bot will correctly respond to user input, rather than repeating user's message.

If you want to contribute to this tutorial and make it better, your help is very welcome. Please fork [this project](https://github.com/lyricat/mixin-tutorial/) and discuss with me at [Github discussions](https://github.com/lyricat/mixin-tutorial/discussions).
