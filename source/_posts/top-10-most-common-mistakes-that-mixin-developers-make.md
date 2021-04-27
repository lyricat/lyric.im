---
title: Top 10 Most Common Mistakes That Mixin Developers Make
date: 2021-04-22 11:51:19
tags: ["Mixin", "Development"]
---

Although Mixin core team and other teams have simplified the approach to access distributed ledger, unfortunately, the segmentation between original software development and Mixin development has still existed.

Here's a tutorial to address the 10 most common mistakes that many developers make.

## #1: Using arbitrary trace ID in transfers

Mixin Wallet API uses trace ID to make sure the kernel handle each transfer only one time.

Suppose that we have a bot which will transfer 1 satoshi to your Mixin Messenger per minute and the pseudo-code may be like that:

```go
for schedule = db.getSchedule(MY_USER_ID); schedule.onTime() {
  // do not use a random number
  // traceID = UUID.v4()
  // a better approach: calculate it with userID and schedule ID
  traceID = calculate(MY_USER_ID, schedule.Time)
  // do transfer
  snapshot, err = mixin.transfer(BTC_ASSET_ID, '0.00000001', MY_USER_ID, traceID)
  ...
  if err != nil {
    // update the record in database for next schedule
    schedule.next()
    db.update(schedule)
    ...
  }
  ...
}

```

Here, the `db.update` part may fail for some reasons.

If we assign `traceID`  with a random UUID at the beginning, the bot will continuedly send me BTC because it fail to update the schedule in the database. So the loop won't stop and will exhaust all BTC in the bot's account. It could lead to grave consequences in a real project.

The correct way is always calculate the `traceID` with something related with your business. In this example, they are the schedule and user's ID, which always gives out the same `traceID` for each transfer.

## #2: Not set resource patterns properly

In bot development, we need to send messages to users. Mixin Messenger supports different categories of messages, [some of them](https://developers.mixin.one/document/bot/api/messages/category) have links in `data` field, they are `APP_CARD`, `PLAIN_LIVE`, `APP_BUTTON_GROUP`.

To send these kinds of messages, you must make sure you have already appended prefix of the links to the "resource patterns" in Developer Dashboard.

If not, you will receive a 403 forbidden error in the response.

For example, if you are trying to send an `APP_CARD` message with the link `https://your_host.org/categories/your_post_url`, you must visit the page of the bot in Developer Dashboard, and fill `https://your_host.org` in the "Resource Patterns".

There are several conditions the 403 error occurs, but this is the most confused one. We can easily make all codes right, but often forget about the configuration.

In addition, the rule of "Resource Patterns" are prefix matching, that means you can't use wildcard or regular expression in it.

## #3: Call transfer() sequentially

To transfer money to someone, we can use [transfer API](https://developers.mixin.one/document/wallet/api/transfer). There is a misunderstanding that calling it with no multi-threads or coroutines to avoid the unexcepted “PIN is incorrect“ error, which causes you panic.

However, you can safely ignore the “PIN is incorrect“ error because it will not lock up your account in this case.

Actually, to call transfer() in coroutines is a good practice to speed up multiple transfers in real projects. According to our experience, the optimal number of concurrency is 20.

If you want to get more benefits from concurrence, you need to create sub-account and use them to send transfer requests.

1. Create sub-account, you can use [CreateUser](https://developers.mixin.one/document/wallet/api/user) API or the method with the same name in [mixin-sdk-go](https://github.com/fox-one/mixin-sdk-go/blob/master/_examples/wallet/main.go#L93).
2. Transfer money to these sub-accounts.
3. Transfer money from these sub-accounts to the opponents.

You may need to design a job management algorithm to distribute money among the sub-accounts, monitor their work queues and re-distribute the transfer jobs if something wrong happens to some sub-accounts.

It's a complexity work, but if you find that concurrency increases transfer performance, then it's worth it.

## #4: Ask for too many permissions in OAuth

Mixin Messenger API provides OAuth to enable bots or dApps to obtain limited access (OAuth scopes) to user's information.

When a bot or dApp requests permissions, the "scopes" are what you see on the authorisation screen.

To request too many permissions in OAuth scopes is not good. A bot and dApp should tell the user the usage of those permissions and only ask for the minimum permissions for running.

To do that, not only consider the privacy, but also reduce the cost of managing these sensitive data.

## #5: Send transfers with raw memo

In the Transfer API, there is a memo field in the request body. The memo field is a string, you can put anything in it theoretically.

However, when you get the snapshots from the Mixin Network and try to parse them, some special characters in the memo may cause failure, a failure hard to recover.

The best practice is that always encode your memo, especially there are user-generated content in your Memo. The user-generated content is always untrustable and should be careful to handle it.

In practice, We encode the memo with base64 to make sure there is no invisible character in the memo.

## #6: Post /messages related issues

[In this document](https://developers.mixin.one/document/bot/api/messages/send), it said we should use an array of messages as the payload. In most cases, it’s correct, except that posting messages in a Mixin encrypted group chatting.

If you are sending a message to a group chatting without a specified `recipient_id` (which means to send the message to all members in the group), you shouldn’t use an array of messages as the payload.

Instead, there are two approaches to do that: the first one is to replace the RESTfull API call with Websocket; another is to send each request for only one message, aka, to replace `[{…}]` with `{ … }` in the payload.

In addition, if you call this API with multiple messages, it does not guarantee the order of the messages’ arrival. So keep your own order management if you care about the order of the messages.

## #7: Handle PIN iterator incorrectly

According to [this document](https://github.com/MixinNetwork/developers.mixin.one/blob/main/developers/src/i18n/zh/document/wallet/get-started/pin.md), If you are using the official SDK or call bare APIs, you need to encrypt the PIN to secure the communication between the bot/dApp side and the Wallet API endpoint.

A common mistake about the encryption is that forget the `iterator` parameter must increase the value each time you send a API call.

It is recommended to use the current system time (in nano second) as the parameter, or you can count it yourself, save the counter and +1 for each call.

If you are using the [mixin-sdk-go](https://github.com/fox-one/mixin-sdk-go), you won't face that problem because the SDK itself has already [handled it properly](https://github.com/fox-one/mixin-sdk-go/blob/master/keystore.go#L150).

## #8: Forget to answer the received messages

According to [this document](https://developers.mixin.one/document/bot/api/messages/acknowledgements), Mixin's message service keeps the state of messages in the sending queue unless the client answer the received messages with an acknowledgment response.

If you forget to answer a message, the Mixin's Message service will send the message repeatedly. And same as above, you don't need to handle it manually either if you are using [mixin-sdk-go](https://github.com/fox-one/mixin-sdk-go).

This mistake and the following two are proposed by [An Lee](https://github.com/an-lee), the developer of [PrsDigg](https://prsdigg.com).

## #9: Didn't handle the "Add Contact" message

When a new user adds a bot as the contact, it will generate an auto message "Hi" and send to the bot.

At beginning, you may feel confounded. But it actually an opportunity to interact with the users and provide them a good onboarding.

There is two thing need to be aware of.

1. The content of auto message has variants in different locale. For English, it would be "Hi", but for Japanese, it would be "こんにちは✋".
2. The auto message is just `PLAIN_TEXT`, there is no difference between it and normal user input.

## #10: Send auto responses to the group

As I mentioned above, to improve the user experience, many developers add auto response feature for their bots. For example, when a new user adds the bot and then says “Hi” to the bot, and the bot will reply with a welcome message, a guideline or a hint which is a handful for new users’ onboarding.

A common mistake is that the developers are often forget to distinguish conversions: group conversation and the user conversation.

If someone adds your bot in a group, and your bot considers it as a user conversation, it may send unexcepted messages to the group members.

If there are two bots in the same group and both of them have the same issue, the situation will be very chaotic and messy: the bot will keep talking to each other unless the group admin kick them off.

To prevent others adding the bot to a group, please run the following code：

```go
func resetBotPreferences() error {
	data := `{"receive_message_source":"EVERYBODY", "accept_conversation_source":"CONTACTS"}`
	return request("POST", "/me/preferences", json.RawMessage(data), nil)
}
```

## Conclusion

Mixin is a powerful combination of distributed ledger and privacy communication service, which evolves quickly since its birth. There are so many possibilities on it.

However, getting the basics right is of the most importance before we start expanding. Hope this tutorial is helpful for you.


