---
title: 这次参加比赛，做了一次导师
date: 2020-11-17
---

最开始写这篇文的时候营销风都快溢出来了，读了一段觉得没什么意思，干脆推了重写。先跟大家说好这篇文章能 yao 得 tui 到 xiao 什么，然后就可以放心大胆写自己的东西。所以，阅读本文你可以获得如下信息：


1. 之前的文章“[冷知识：有4000万程序员正在每天让世界变得好一点](https://mp.weixin.qq.com/s/4XUhjHDauEnCGeQmIhCRTw)”调查表的原始数据。
2. 一个致力于捐赠开源项目的开源项目：[Claps.dev]("https://claps.dev")。
3. 一种可以通过代码分析量化程序员贡献的算法。

![](https://mmbiz.qpic.cn/mmbiz_png/B21yuDVicPmGcQnRwHywgk3RwgFChku1rdueJ03iaug9INYVcbfc9XyXHXLfeyM38ssvfr4E1etKCd3GnVO6Rz0Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

---

要知道对我这种人来说，如果一个项目属于用爱发电的类型，也就是不用关心商业化和营收，让投资人脑壳疼，我就来劲。

前几年在大公司里面得闲，捣鼓过一个 App 叫 Faded，slogan 是 “Great people die, only when we forget them”，顾名思义专门纪念人类历史上璀璨的明星，当时还为此专门学了 iOS 开发。

![](/claps-dev-summer-2020/faded.jpg "Faded")


后来常跟朋友说想做游戏。有一天投资人说你要做的话我友情资助，我说您可别，做游戏大概率赚不到钱，到时候您赚不到钱我心理负担太重，会抵消用爱发电的愉悦。

所以最早 Claps 这也是个用爱发电的项目，它的起因可以追溯到 2019 年冬天，我恰好认识 Cedric 和 Basicthinker 两位朋友，现在他们已经在往大佬的路上走了，可以提前称他们为 Mixin 的 Cedric 和 Merico 的 Basicthinker。总之他们俩，一个搞了个转账很快的公链，一个搞了个能用算法评估程序员绩效的算法。

我就想把这两直接撮合一块儿，就可以造个轮子：一个给开源项目捐款的开源项目。

因为当时 Medium 上有个功能叫 claps，相当于微信公众号的点赞，我感觉寓意挺好，就跑去注册了个域名 claps.dev，大概意思就是为开发者鼓掌👏，至于怎么鼓掌，那就是用转账很快的公链来给开源项目打钱，然后用能评估绩效的算法给程序员分钱，就这么简单。

![](/claps-dev-summer-2020/photo.jpg "Claps.dev")

接着机缘巧合，另一朋友给我推荐了中科院软件所和华为联办的一个比赛，叫“开源软件供应链点亮计划 2020”的比赛，基本上是 Google Summer of Code 在国内的翻版，所以我接下来简称它为 Summer 2020。

这个比赛是干啥的呢，它的主要目的是鼓励高校的学生参加开源社区的开发，每一个成功完成项目需求的学生，都会获得来自组委会——也就是中科院软件所、华为等主办机构的经济资助。

简单来说，只要我进行项目申请，然后表现好点，说不定就有金主赞助找人来继续 Claps 了。

我朋友觉得 Claps 可以去参加一下，我就去了，然后就过了。从学生时代起，我就和 Basicthinker 打了很多比赛。没想到不做学生了，还有机会跟之前的队友一起参加比赛，不过这次是作为导师的身份。

所以 Claps 就有幸成了比赛项目，现在还可以在[官网上](https://isrc.iscas.ac.cn/summer2020/#/organisations "Summer 2020")看到 Claps 的社区介绍和[源码](https://github.com/claps-dev "Claps.dev@Github")。

## 参赛的学生

Summer 2020 的赛制跟 Google Summer of Code 很类似，我就不多赘述了。总之经过项目导师和申请学生的双向选择，我最终选择了四位学生来参加 Claps 的开发。其实当时申请者挺多的，但是考虑到我的时间成本，所有被我判断为“让我指导还不如我亲自动手”的学生都 rejected 了，最后选择的这四位总体还是满意的。

尤其是其中的三位，讲真出于私心我啊很希望他们毕业能来我的团队工作（对，我依然在招人），但是理性的建议依然是推荐没有特殊追求的年轻人去大厂或者找个好导师读个 master。

![](/claps-dev-summer-2020/faded.jpg "参赛学生 RandyLambert，照片已得到授权")

*参赛学生 RandyLambert，照片已得到授权

总之他们完成得真的很好，高于我的预期，如果他们想去哪个大厂我都愿意内推一下，也欢迎哪位大佬看了本文可以来高薪要人。

## 只支持 cryptocurrency

首先，在世界的大部分国家，使用 Bitcoin 都是合法的。捐赠当然也属于用途之一，例如 [Mozilla](https://wiki.mozilla.org/Donate_Bitcoin), [OpenSSH](https://www.openbsdfoundation.org/donations.html) 这样的大开源项目都支持 Bitcoin 捐赠；公益性的 [Wikipedia](https://donate.wikimedia.org/wiki/Ways_to_Give) 也支持 Bitcoin 捐赠；甚至[联合国儿童基金会](https://www.unicef.org/press-releases/unicef-launches-cryptocurrency-fund)也可以使用 cryptocurrency 捐赠。

其次，捐赠 cryptocurrency 有很多好处。比如说可以实现真正的匿名捐赠，做个事实的雷锋；再比如说可以无视任何地区性或者全局性的支付系统限制，不会被限制在某个国家，某个银行，某个 SWIFT 协议，或者某个微信支付，你想捐给谁就捐给谁；从基金会的角度看，接受 cryptocurrency 捐赠也有好处，例如可以自然地公示基金会的接受捐赠的财务信息，接受大家的监督等等。

再加上 cryptocurrency 和开源项目天生的亲和力，我们都打算基于此做一个新的 License 出来了。因此我认为 Claps 只支持使用 cryptocurrency 来捐赠，而不接受任何其他货币是非常合理的。

## 这算法分钱靠谱吗

其实最早跟 Basicthinker 讨论这个事的时候，就出现了很多对此的质疑。不过我的看法很简单，既然连 Facebook 都干过数代码行数算奖金的事情，那这算法只要做出哪怕稍微一点儿 make sense 的改进，也是福报。

至于大家如果真的去对算法而不是对上司吹毛求疵，不但改进了算法，还能释放职场压力（

这个算法没有想象的神秘，它最核心的思想就两个，一是衡量抽象语法树的复杂度，而不是数代码行数，避免空行、注释、括号方式带来的影响；二是类似 Google Page Rank 的算法，即基于一个假设：如果你的代码很重要，那么你可能会被频繁的引用。

你可能会说，那写基础设施的朋友岂不是占了便宜，嗨呀，大部分公司里负责基础设施的朋友捞不到好处的常态怎么没人帮他们说话呢。

当然，这个粗暴的假设并不是这个算法的全部，否则 Basicthinker 的团队就没法去发论文顺便开个叫 [Meri.co](https://meri.co/) 的公司用这个算法赚钱了，他们当然也要考虑各种场景和情况，确保这个算法的整体输出是有意义的。

所以，如果你在 Claps 里选择了使用 Meri.co 的算法进行分配，那么每个开发者收到的金额就是不一样的，在理性情况下，就是干得越棒赚得越多。

## 说说开源

开放源代码运动在国内基本上属于一个挺扭曲的状态。在十几年前的国外，开放源代码偶尔会被当作 communism 污名化，现在国内又变成了小圈子里用来寻租的“自主研发”代名词。出发点有问题结果处处有问题。

不过无论是国内还是海外，有一个始终很难解决的问题，那就是开源运动虽然实质上悄无声息地改变了每一个人的生活，但是大家给它们的回报实在是不成比例（可以看看我之前做的 [开源项目捐赠调查表](https://docs.google.com/spreadsheets/d/1yvjxoPQ5ZjNCAv13NJ4aoz3fFjMudi0He6zoOaSzDR0/edit?usp=sharing)）。

几年前 OpenSSL 的“[HeartBleed](https://en.wikipedia.org/wiki/Heartbleed)”漏洞直接影响了全球 66% 的网站服务，最大的原因是长期以来 OpenSSL 缺少资金支持，全职维护者只有两个人。

到了今年依然没啥变化，受到疫情影响营收受到重大打击，良心基金会 Mozilla [宣布裁员了 250 人](https://blog.mozilla.org/blog/2020/08/11/changing-world-changing-mozilla/)。

也许 Claps 不能改变什么，即使大家不用 Claps 捐款，也可以直接去官网使用信用卡，或者使用流行的 [Github Sponsors](https://github.com/sponsors)，只希望更多开源社区可以得到大家的重视，体现出它本来该有的价值就行了。

---

不过还是希望有条件的读者可以来了解一下 Claps.dev，可以打个赏或者贡献一下代码都是欢迎的。

现在我们只小规模地上了几个项目，还没有正式去接洽开源项目团队，但大家可以在网站的右上角 submit projects。当然如果可以去支持一些缺钱，但是做的东西又很有价值的团队，那是最好的。