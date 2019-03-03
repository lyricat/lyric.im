---
title: Mixin 开发简明教程 2：第一笔数字货币转账
date: 2018-12-04 23:34:04
tags: ["区块链", "blockchain", "教程", "tutorial"]
---

本系列教程旨在向区块链领域以外的工程师们介绍如何以依托 Mixin Network，用最简单的方式开发自己的区块链应用程序。

如果对本系列感兴趣，你可以：

- [订阅我的博客 lyric.im](https://lyric.im)
- [订阅我的 PRESS.one 专栏](https://press.one/main/p/37bb01456229cf59e14aa19c6755a8ba2947be9b)
- 关注我的公众号，搜索`iamlyricw`
- 或在文末给赞赏一些数字货币

方便获取后续更新。

---

## 你的第一笔数字货币转账

在[上一期文章](https://lyric.im/mixin-dev-tutorial-01/)中，我介绍了如何用最简单的方式来接触 Mixin Network：一个机器人 `hello bot`。这个机器人能够在 Mixin Messenger 中，对用户输入进行响应。

交易是 Mixin Network 解决的核心问题。因此在本期，我将会在 `hello bot` 的基础上，进行资金操作。

<!-- more -->

>  任务描述：用户在 Mixin Messager 中向机器人说 `smtm`，机器人回复 向用户进行转账，转账备注为 `Enjoy!`。用户向机器人说 `assets`，机器人回复自己钱包的资产列表。

![mixin-bot-tut-0201](/mixin-dev-tutorial-02/mixin-bot-tut-0201.png)

通过阅读本文，你会知道如何在 Mixin Network 中进行转账等资金操作。

一样地，本教程需要你**知晓如何使用 Golang 编写程序**。

## 1. 处理转账指令 `smtm`

`smtm` 指令意为 `show me the money`，原本是一句来自北方地区的古老谚语，有招财的寓意。

因此，当用户输入 `smtm`时，我就让机器人给用户发钱。效果如下：

首先，我们要在 `OnMessage` 函数中加入对 `stsm` 指令的解析：

```go
} else if "smtm" == inst {
    // Show me the money. Okay.
    Transfer(ctx, msgView)
}
```

其中涉及到函数 `Transfer`，它负责给当前发消息的人发一笔钱，并在备注里写上 `Enjoy!`。

```go
// Transfer 1.024 CNB to the user who having a conversation with bot.
func Transfer(ctx context.Context, msgView bot.MessageView) {
	payload := bot.TransferInput{
		AssetId:     CNBAssetID,
		RecipientId: msgView.UserId,
		Amount:      number.FromString("1.024"),
		TraceId:     uuid.Must(uuid.NewV4()).String(),
		Memo:        "Hello world",
	}
	err := bot.CreateTransfer(ctx, &payload,
		config.GetConfig().ClientID,
		config.GetConfig().SessionID,
		config.GetConfig().PrivateKey,
		config.GetConfig().Pin,
		config.GetConfig().PinToken,
	)
	if err != nil {
		Respond(ctx, msgView, fmt.Sprintf("Oops, %s\n", err))
	}
}
```

转账的目标 `RecipientId`、金额 `Amount`、币种 `AssetId`、备注 `Memo`等信息，需要充填到 `TransferInput` 结构体中；而 `TrackId` 是一个随机 UUID，用来确保转账操作只会执行一次。最后，将之前我们准备好的机器人的 ID、SessionID、私钥和 PIN 等信息作为参数，调用 `bot.CreateTransfer` 这个函数，就可以直接发起转账了。

在这个函数里，bot 框架帮我们做了很多麻烦工作。你可以深入阅读 `CreateTransfer` 的源码（[这里](https://github.com/MixinNetwork/bot-api-go-client/blob/master/transfer.go#L19)）。了解为了准备一笔转账，框架帮我们做了很多额外工作。如果你想了解的话，别着急，因为接下来就会用到了。

## 2. 处理资产指令 `assets`

这个命令的作用是列出机器人钱包里面的所有资产数额，以及相关的资产信息。前几天有读者阅读了机器人的源码，询问我如何知道转账时常量 `CNBAssetID` 的值。答案就在本节了。

类似地，我们需要处理 `assets` 指令

```go
} else if "assets" == inst {
    // Show me your assets. Okay.
    ShowAssets(ctx, msgView)
}
```

然后实现 `ShowAssets` 函数：

```go
// ShowAssets show all my assets
func ShowAssets(ctx context.Context, msgView bot.MessageView) {
	var accessToken string
	var err error
	var assets []bot.Asset
	// generate an access token
	accessToken, err = bot.SignAuthenticationToken(
		config.GetConfig().ClientID,
		config.GetConfig().SessionID,
		config.GetConfig().PrivateKey, "GET", "/assets", "")
	if err != nil {
		Respond(ctx, msgView, fmt.Sprintf("Oops, %s\n", err))
		return
	}
	// get all assets.
	assets, err = bot.AssetList(ctx, accessToken)
	if err != nil {
		Respond(ctx, msgView, fmt.Sprintf("Oops, %s\n", err))
		return
	}
	var assetsBuffer bytes.Buffer
	for _, asset := range assets {
		assetsBuffer.WriteString(fmt.Sprintf("%s: %s\n", asset.Symbol, asset.Balance))
	}
	Respond(ctx, msgView, fmt.Sprintf("My Assets:\n%s", assetsBuffer.String()))
}
```

这次的函数实现比较复杂一点点，因为 `bot.AssetList` 这次没有帮我们做额外工作了，它所需要的第二个参数 `accessToken` 是函数 `SignAuthenticationToken` 的产物，需要我们手工生成。

它的主要作用是使用机器人的 Key 来生成访问受保护资源的 token，关于本函数的详细解释请查看官网文档 [Authentication Token](https://developers.mixin.one/api/alpha-mixin-network/authentication-token/) 章节。

有了 `accessToken` 后，我们就可以调用 `bot.AssetList` 来获取资产列表了。该函数会返还一个 `Asset` 结构的列表，详细的解析如下：

```go
type Asset struct {
	AssetId     string // 币的 ID
	ChainId     string // 币所在主链的 ID
	Symbol      string // 币的简称
	Name        string // 币的名字
	IconURL     string // 币的图标
	PriceBTC    string // 币的 BTC 价格
	PriceUSD    string // 币的美元价格
	Balance     string // 资产数额
	PublicKey   string // 币的地址
	AccountName string // 币的账号名（EOS系特有）
	AccountTag  string // 币的账号标签（EOS系特有）
}
```

看，如果你的机器人钱包里有某种币，你就可以通过 `AssetId` 获得对应的币 ID 了。不过，在本文我们只关心 `Symbol` 和 `Balance` ，我创建了 `assetsBuffer` 用来遍历资产列表，将二者格式化后放入 Buffer，最后输出到聊天窗口。

## 小结

本章内容暂时到这里，Hello Bot 的 Mixin Id 为 `7000101423`，可以在 Mixin Messenger 中加它为好友体验。

Hello Bot 的源码可以在 https://github.com/fox-one/hello-bot 获取。

下一章，我将为 Hello Bot 添加**赚钱**的功能。

---

本文的 [PRESS.one 签名](https://press.one/file/v?s=b6c139e0265c8e78f35c968ad4f36b8670c8a58363abb347f77f56f2a63217baefe64cf30c97663535aa5c9a9732fc1e2627fe39b5da2a35492ccaac62411ffc1&h=87b99a6fb7cdfdd461e44ab026819992b8fb893c54e41a2005bd12ab640e0ea0&a=37bb01456229cf59e14aa19c6755a8ba2947be9b&f=P1&v=2)