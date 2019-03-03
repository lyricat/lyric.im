---
title: Mixin 开发简明教程 1：写个机器人
date: 2018-11-29 11:21:18
tags: ["教程", "tutorial", "区块链", "blockchain"]
---



我是 @lyric，本教程应 Mixin COO @薄荷 邀约而写，旨在向区块链领域以外的工程师们介绍如何以依托 Mixin Network，用最简单的方式开发自己的区块链应用程序。

本文是系列教程的第一篇，如果对本文感兴趣，你可以：

- [订阅我的博客 lyric.im](https://lyric.im)
- [订阅我的 PRESS.one 专栏](https://press.one/main/p/37bb01456229cf59e14aa19c6755a8ba2947be9b)
- 关注我的公众号，搜索`iamlyricw`
- 或在文末给赞赏一些数字货币

方便获取后续更新。

<!-- more -->

---

## 为什么是 Mixin

从 BTC 到 ETH 到 EOS，相信很多工程师都对区块链有一些了解，并对其中的故事有所耳闻。就技术本身，无论是 BitCoin 的基础技术模型，还是从 ETH 开始智能合约，抑或是 EOS 独特的架构——可能让第一次接触的工程师会觉得有一些不适应，开发 Dapp 也变得困难。

实际上，每一条公链都（声称）有自己独特的应用场景，对此提出了特异性的技术。对于开发者来说，这些涉及复杂密码学的技术变成了进入门槛。而 **Mixin 做的事情是抹平了所有公链在“交易”这个层面上的差异，专注于“交易”本身**。

在 Mixin Network 中，你可以使用最熟悉的技术栈和协议，轻易地将“数字货币”属性加入到自己的项目中，实现“零售、打赏、货币交易”等所有跟“交易”相关的功能；也可以借助 Mixin Network 的 DAG 网络，将（广义的）“区块链”属性加入到自己的项目中，实现“版权、存证、追溯、信息披露”等所有跟“区块链”相关的功能。

**综上所述，使用 Mixin Network，可以让你的想法很轻松地在区块链上实现，不需要关注很多繁琐的技术细节**。

> **小贴士**
>
> 所谓 Dapp，为 Decentralized Application[^1] 的简称。一般认为 Dapp 由智能合约（即在区块链上运作的代码）编写。
>
> Mixin 对此有不同看法。Mixin 的支持者信仰 Unix 哲学[^2]中的 “让程序只做好一件事”，Mixin 只需要做好“Transaction” 这一件事，因此 Mixin 并不支持智能合约。这也是为什么你可以使用自己熟悉的技术栈来写应用，而不仅仅局限于少数程序设计语言的原因。

---

## 注册 App

在开始之前，你需要在 Mixin Network 中注册自己的 App。在 Mixin 开发者中心 https://developers.mixin.one/dashboard ，提交图标、名称、描述，URL 等基本信息即可完成注册。这很类似于常见的开放服务。

注册完成后，点击 “Click here to generate...” 生产必要的密钥等信息，各部分如下图所示：

![Register APP](/mixin-dev-tutorial-01/mixin-register-app.png)

Mixin Network 把 App 当作一个用户，因此 User Id 用于在网络中唯一标识该 App。每个 App 自带一个资产账户，直接向这个 User Id 转账会进入到账户中。

而下面的其它信息，如 Client Secret、PIN、SessionId、Pin Token、Private Key 等则用来操作账户，执行更新信息、转账、查账等操作，等会儿我们会用到。请务必记住它们，并且不要泄漏。

我预先注册了一个 Hello Bot，Mixin Id 为 `7000101423`。可以在 Mixin Messenger 中加为好友体验。

---

## Hello world.

接下来，我们来写 Hello world。

>  任务描述：用户在 Mixin Messager 中向机器人说 `sync`，机器人回复 `ack`。

简化起见，我会在项目中使用 MixinNetwork 的 Bot SDK：`github.com/MixinNetwork/bot-api-go-client`，并且使用 [Golang 语言](https://golang.org)来进行程序设计。因此，**接下来我假设你已经将 Golang 开发环境安排好了，并且知晓如何使用 Golang 编写程序**。

### 1. 准备 SDK 和项目目录

本文的开发环境为 macOS，所用的 Golang 版本为 go1.11.2。

我们先安装 Bot SDK

```bash
$ go get -u github.com/MixinNetwork/bot-api-go-client
```

接下来我们可能还需要安装其它依赖的模块，请使用 `go get` 指令安装，我不再赘述。

准备一个目录，用于存放项目和源码，我将其命名为 `hello-bot`；在其中新建 `config.yml`，用于存放配置；最后在 `hello-bot` 下新建 `main.go`，主要代码将在其中。

```bash
$ mkdir hello-bot
$ cd hello-bot
$ touch main.go
$ touch config.yml
```

### 2. 理解 Mixin Messenger 机器人的运作原理

在正式开始之前，先理解一下 Mixin Messenger 机器人的运作原理。

虽然 Mixin Messenger 的资产体系建立在 Mixin Network 之上，但用户和消息体系则是独立的。使用 Mixin Messenger 聊天的时候，由服务器提供消息的中转。

在服务器看来，机器人就像是一个参与聊天的普通用户。因此跟机器人聊天时，消息也会由服务器转发到机器人所在的机器，由机器人程序进行处理和应答。

由于机器人像一个普通用户，所以它也有自己的钱包（在 [App Dashboard](https://developers.mixin.one/dashboard) 上可以看到）。因此，如果涉及到转账，机器人可以通过 Mixin Network 来操作自己的账户，进行转账等自己操作。

### 3. 配置机器人

在上一章节"注册 App"中，我们得到了 Id 和私钥等机器人的信息，需要将它写到配置文件 `config.yml`之中：

```yaml
client_id: YOUR CLIENT ID
client_secret: YOUR CLIENT SECRET
session_id: YOUR SESSION ID
pin: YOUR PIN
pin_token: YOUR PIN TOKEN
private_key: |
  -----BEGIN RSA PRIVATE KEY-----
  ...
  -----END RSA PRIVATE KEY-----
```

请按照之前的信息挨个填写。其中私钥 `private_key` 是一个多行文本，**请确保遵从 yaml 的多行文本语法，每一行的行首都要对齐**。

在源码中，我提供了 `config.example.yaml`，可以修改它为你的配置文件。

### 4. 接收消息

一个机器人，最核心的部分是消息循环。这个消息循环会不断检查服务器的消息队列，是否有给它推送新消息。

我们在 `main.go` 的 `main` 函数中，使用 `NewBlazeClient` 方法创建机器人客户端。`client` 是一个模块级变量，方便别的函数调用：

```go
// Create a bot client
client = bot.NewBlazeClient(
    config.GetConfig().ClientID,
    config.GetConfig().SessionID,
    config.GetConfig().PrivateKey
)
```

`config` 模块会负责从` config.yml` 中读取必要的配置。

然后使用 `client.Loop` 开启消息循环。关键的处理函数被定义在接口 `handler` 之中，实现为 `OnMessage`

```go
// Start the loop
for {
	if err := client.Loop(ctx, handler); err != nil {
		log.Printf("Error: %v\n", err)
	}
	log.Println("connection loop end")
	time.Sleep(time.Second)
}
```

```go
type Handler struct{}
...
func (r Handler) OnMessage(ctx context.Context, msgView bot.MessageView, botID string) error {
	...
}
```

其中，`msgView` 是关键的参数，它包含了所有跟本次消息相关的信息。[在 SDK 源码中](https://github.com/MixinNetwork/bot-api-go-client/blob/master/blaze.go)，它的定义是这样的：

```go
type MessageView struct {
	ConversationId string // 会话 ID
	UserId         string // 对方用户 ID
	MessageId      string // 消息 ID
	Category       string // 消息类型，文本、图片还是视频等
	Data           string // 消息的数据，具体的消息内容
	Status         string // 消息状态
	Source         string 
	CreatedAt      time.Time
	UpdatedAt      time.Time
}
```

包含了一条消息所需的全部信息。

机器人处理消息时，需要对 `msgView` 进行拆解，然后作出应答。

```go
func (r Handler) OnMessage(ctx context.Context, msgView bot.MessageView, botID string) error {
	// 我们只处理 PLAIN_TEXT 类型消息，并且只应答当前对话。
	if msgView.Category == bot.MessageCategoryPlainText &&
		msgView.ConversationId == bot.UniqueConversationId(config.GetConfig().ClientID, msgView.UserId) {
		var data []byte
		var err error
		if data, err = base64.StdEncoding.DecodeString(msgView.Data); err != nil {
			log.Panicf("Error: %s\n", err)
			return err
		}
		inst := string(data)
		log.Printf("I got a message from %s, it said: `%s`\n", msgView.UserId, inst)
        }
    ...
}

```

首先我们确定只处理文本消息，因此要先对 `msgView.Category` 进行判断。然后使用 `UniqueConversationId`判断消息是否来自对方所在的会话。

然后将 `msgView.Data` 进行 Base64 解码，获取到其中的消息内容，保存到变量 `inst` 之中。

### 5. 回复消息

接着我们来对消息进行处理。在 `OnMessage` 中，对 `inst` 的内容进行判断：

```go
if "sync" == inst {
    // Sync? Ack!
    Respond(ctx, msgView, "ack")
} else {
    Respond(ctx, msgView, defaultResponse)
}
```

如果用户发来的消息是 `sync`，那么就让机器人回复 `ask`；如果不是 `sync`，则回复一句默认文字。

回复的实现在 `Respond` 函数之中，它非常简单：

```go
func Respond(ctx context.Context, msgView bot.MessageView, msg string) {
	if err := client.SendPlainText(ctx, msgView, msg); err != nil {
		log.Panicf("Error: %s\n", err)
	}
}
```

只是调用机器人客户端 `client.SendPlainText`回复一句话给对方，回复的内容由变量 `msg` 指定。

效果如下：

![mixin-bot-tut-0101](/mixin-dev-tutorial-01/mixin-bot-tut-0101.png)

这样，一个能跟你说话的机器人就完成咯。在 `OnMessage` 中添加更多逻辑，就可以让它具备更多功能，而不仅仅是回复一个单词。

---

## 小结

本章内容暂时到这里，Hello Bot 的 Mixin Id 为 `7000101423`，可以在 Mixin Messenger 中加它为好友体验。

Hello Bot 的源码可以在 https://github.com/fox-one/hello-bot 获取。

下一篇文章，我将为 Hello Bot 添加操作资金转账的功能。

---

本文的 [PRESS.one 签名](https://press.one/file/v?s=d23f325dbf173c3995e33b42393fcbc502976891bcabcc1ea0aa33392e8dd11a4d6522c1598bfa2651975cf217fe432a2f0fcc8d018e13b44948783b10a6b2df0&h=b6691b1bab1ab75e1b01ae0a8dcf68b8c856ed6690f5bc707056d31af2b5898e&a=37bb01456229cf59e14aa19c6755a8ba2947be9b&f=P1&v=2)
