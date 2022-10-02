---
title: "Murmurs.io: 一个 Web3 微博客"
description: 
date: 2022-10-02 13:49:19
---

严格按照 [ethereum.org](https://ethereum.org/zh/web3/) 的观点，[Murmurs](https://murmurs.io) 不属于 Web3。因为它说：

> Web3 是去中心化的：大部分互联网不是由中心化实体控制和拥有的，而是由构建者和用户分配所有权。
> 
> Web3 无需许可：每个人都有参与 Web3 的平等权限，没有人被排除在外。
> 
> Web3 具有原生支付功能：它使用加密货币进行线上消费和汇款，而不是依赖传统银行或第三方支付机构过时的基础设施。
> 
> Web3 无需信任：它通过激励措施和经济机制运转，而不是依赖受信任的第三方
> 
> via “[什么是 Web3](https://ethereum.org/zh/web3/#why-is-web3-important)”

但是按照目前大部分 Web3 从业者的风气，Murmurs 属于 Web3 —— 因为 Murmurs 可以用你的 Metamask 或者 imtoken 钱包登录（**Web3 无需许可**），而且内置加密货币转账（**具有原生支付功能**）。

从业者们是很聪明，琢磨琢磨，柿子一定先挑最软的捏。

1. 能用钱包登录，是方便薅用户。持币的人儿，谁没有一个 Metamask 或者手机钱包呢。
2. 能转账，是因为收钱、送钱、赚钱、亏钱、骗钱，都是真的刚需。

至于其他的，属于费劲不讨好的事情。就拿文档里的“数字所有权”来说：

> 例如，假设您正在玩一个 Web2 游戏。 如果您购买游戏内物品，它会直接与您的帐户绑定。 如果游戏创建者删除您的帐户，您将丢失这些物品。 或者如果您停止玩游戏，您将失去投资到游戏内物品的价值。
>
> Web3 允许通过非同质化代币 (NFT) 直接拥有所有权。 任何人甚至是游戏创作者，都没有权力剥夺您的所有权。 而且，如果您停止玩这个游戏，您可以在公开市场上出售或交易您的游戏内物品并收回它们的价值。

听上去很美。但目前的 Web3 游戏，即使道具和数据用 NFT 来确定所有权，一旦停止持续运营，这些 NFT 会立刻失去一切除了炒作以外的价值。因为大部分游戏的内生价值，会随着运营的停止迅速凋零。

当然，也有很多中古游戏现在依然有很庞大的玩家社群。比如我前段时间玩的， “[要塞：十字军东征](https://store.steampowered.com/app/40970/Stronghold_Crusader_HD/?l=schinese)”，2002 由 FireFly 工作室发行，到现在已经 20 年了，依然有很多玩家在给它做新的关卡和内容。

“要塞：十字军东征” 不是什么 Web3 游戏，也没有运行在合约上来标榜去中心化，也不支持加密货币钱包登录和转账，也没有发币搞经济模型做激励。它到现在还有活力，只有两个原因：

- 一是它**好玩**。好玩是游戏的“第一属性”，没有之一。
- 二是它**开放**，支持 Mod，即使官方公司停止开发了，玩家可以通过 Mod 的拓展性继续给游戏里加料

对游戏来说，好玩是最重要的。如果好玩，不需要经济模型做激励，玩家也会“用爱发电”给过气游戏增砖添瓦。不好玩，即使发币给激励，给游戏写 Mod 就变成一份赚钱的工作，和在 EA 上班没有什么区别。

所以，我觉得 Web3 怎么定义并不太重要。关键在于，若认可 Web3 的观念，开发者要通过什么技术，什么经济模型，什么产品设计来确保自己的产品首先能达成“第一属性”，得到人民的认可；然后才是实践这些 Web3 观念。

## Murmurs 是什么

在上文提到的 [Murmurs](https://murmurs.io) 是一个 Twitter 的克隆。有 Twitter 的基本功能，加上可以使用加密货币钱包登录，并且支持加密货币转账。

做 Murmurs 时没有什么特别的想法，只是因为我一直很喜欢 Twitter 这种 microblogging 形式，所以我就花了一点时间证明可以开发一个像 Twitter 的东西，再加入一点我也喜欢的加密货币，来证明一下我可以。

就像喜欢的鸡尾酒 Pinacolada，朗姆酒和椰汁和凤梨汁都好喝，混在一起也好喝。

很多产品都是这样搞出来的：开发者自己喜欢的东西，那很可能很多人都喜欢。比如之前和 [Cedric](https://github.com/cedricfung) 做的 [Mornin.fm](https://gitpress.io/@lyric/the-story-behind-mornin-zh)。

所以，如果你有 Metamask 钱包，可以在浏览器里访问 https://murmurs.io 来体验一下这个产品了。

## Murmurs 的功能有多简单

第一部分是最基本的 microblogging 功能。包括时间线，关注其它用户，发帖子，回复帖子，转发帖子，上传自拍，吸引粉丝点赞等等。

![](/murmurs-io-a-web3-minus-microblogging-service/murmurs-1.jpg)

第二部分是跟加密货币相关的，除了可以用钱包登录，其它功能目前只有一个那就是打赏。

Murmurs 里任何一个帖子（称为 Murr），都可以被打赏；任何一个用户（或者说地址），也可以被打赏。

![](/murmurs-io-a-web3-minus-microblogging-service/murmurs-2.jpg)

目前打赏支持 BTC，ETH，XIN 和 USDT。虽然技术上可以支持很多别的币也没有什么难度，但现在就只支持这四种。

我在 Murmurs 的链接是 https://murmurs.io/u/1024 。如果来关注我并且留言说点好听的，我可能会给你打赏。

## Murmurs 的未来怎么搞

这个很难讲。

最早 Twitter 不过是一个网页版本的公开的论坛，你可以通过短信发帖子。

![](/murmurs-io-a-web3-minus-microblogging-service/twitter-1.png)

![](/murmurs-io-a-web3-minus-microblogging-service/twitter-2.png)

后来 Twitter 变成现在这样，很大程度是它的用户塑造了它。

最典型的例子，就是“转推（retweet）”功能的诞生。无论是官方的 retweet 还是 后来的 Quote，完全是用户推动的。是用户先创造了“转推”的玩法和格式——也就是我们喜欢说的“协议”——然后才是 Twitter 公司提供官方支持。

但是，不要催我设计分布式协议，做去中心化节点，做经济模型发币。

## Murmurs 上的人都在干什么

我观察下来，和 Twitter 差不多，大家都在碎碎念居多。不过由于大家多少跟币沾点边，所以也会发一些加密货币相关的东西。

不过，我们每个人的生活本来就是这样，加密货币是其中的一部分，但又不是全部。因此加密货币也是 Murmurs 的一部分，但又不是全部。

就像一个人，如果所有时间只做一件事，即使这件事是很上进的，例如工作，那么跟这个工作狂的人际关系就很劝退。

Murmurs 作为一个内容方向的社交属性产品也是如此。如果它只关注币币币，那么它也很劝退。

## Murmurs 背后的技术是

基于 [Mixin Network](https://mixin.network) 和其上的 EVM 兼容虚拟机 [MVM](https://mvm.dev) 做的。快速转账和多币种多链支持就靠它。

其它技术就没有什么特别的，和 Web2 差不多。

## 体验 Murmurs 

很简单。

目前支持 [Metamask](https://metamask.io)，[Fennec](https://pando.im/wallet)，[Mixin Messenger](https://mixin.network/messenger) 三类钱包登录。

其中 Metamask 是浏览器扩展和 App，用 Brave 浏览器也可以；Fennec 是另一个浏览器扩展；Mixin Messenger 是手机 App。

拥有他们任何一款都可以，然后访问 https://murmurs.io 再点击“Connect”按钮就行。


