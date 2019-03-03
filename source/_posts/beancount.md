---
title: 钱都到哪儿去了 - 使用 beancount 自动记账
date: 2018-07-27 17:31:22
tags: ["Productivity", "生产力", "Beancount", "记账"]

---

使用 beancount 记账一年多了，最早是个人私帐用它，现在公帐也用它。记账是一个好习惯，可以回答一系列财务问题：

1. 我去年在打车上花了多少钱
2. 我每年政府贡献了多少社保和税
3. 我在美股的投资中哪一支股票收益率最高，赚了多少

如果日常没有记账，这样的问题很难回答。除此之外，严肃的记账还有以下好处：

1. **更好的掌握自己的经济状况**。如果是只记流水账，买了房资产负债几百万 30 年，这样的帐就除了督促自己还钱没有别的存在意义。
2. **一次重新思考生活观念的机会**。例如三年前买的电脑归入“资产”而不是“消费”，那么在权衡其二手收益时，可以更清楚地评估其价值（Apple 的电脑更保值，折旧费更少），帮你作出选择。
3. **更好运用金融工具**。典型的就是信用卡消费。

但是记账本身是一件挺麻烦的事情，所以需要把流程自动化。Beancount 提供了开放的文件格式，很有利于自动化。

<!-- more -->

## 前置知识：复式记账法

和一般流水账不同，Beancount 使用“**复式记账法**”，也就是会计师使用了几个世纪的记账法。

在中文资料中，很多人用“借、贷”来解释复式记账，太麻烦了[^1]。我给大家讲一个形象的描述：你的账本有四个“桶”，分别为“收入(Income)”、“支出(Expenses)”、“负债(Liabilities)”、“资产(Assets)”[^2]。

支出和收入都称为“交易”，每一笔交易可以解释为资金在这四个桶之间进行流动。

例如：

- 早上公司发工资，那么从“收入(Income)”桶到“资产(Assets)”桶。
- 中午出门打出租，从“资产(Assets)”桶到“支出(Expenses)”桶。
- 下午用信用卡买了一台手机，从“负债(Liabilities)”桶到“资产(Assets)”桶。
- 下个月还信用卡，从“资产(Assets)”桶到“负债(Liabilities)”桶。
- 明年在二手市场卖掉手机，从“资产(Assets)”桶扣除折旧费用，换成所得现金，回到“资产(Assets)”桶。

这样看复式记账是不是简单很多了。再看下面一个简单的例子：

![transaction](/beancount/transaction.png)

这是一条典型的交易记录，表示我 7 月 26 日使用滴滴打车，开销为 15 元。记录解释如下：

![transaction with comment](/beancount/transaction%20with%20comment.png)

其中最重要的是 `Assets:Cash` 和 `Expenses:Traffic:Didi`。他们分别表示我的现金和滴滴打车的消费，分属于资产(Assets)”桶和“支出(Expenses)”桶。

## 使用 Beancount 和 Fava

### 安装 Beancount

首先你需要有 Python。如果没有的话，推荐去下载 [Anaconda](https://www.anaconda.com/downloads) 或者 [Miniconda](https://conda.io/miniconda.html)，里面会内置 Python。接下来就可以安装 Beancount 和他的图形界面 fava 了：

```
pip install beancount fava
```

### 编写账本

Beancount 账本由纯文本文件构成。你可以使用 `include "文件名"` 语法来自由组织账本文件。这里我提供三个文件组成的最简单账本：

accounts.bean：用来管理四个桶

```beancount
1990-01-01 open Assets:Cash
1990-01-01 open Expenses:Traffic:Didi
1990-01-01 open Equity:OpenBalance
```

main.bean：主入口文件，包含了 accounts.bean 和 2018-07.bean

```beancount
option "title" "我的账本"
option "operating_currency" "CNY"

include "accounts.bean"

; 每个月的账本
include "2018-07.bean"
```

2018-07.bean：每个月的账本，文件名就是月份

```
2018-07-26 * "小桔科技" "滴滴打车"
    Assets:Cash			       -15.00 CNY
    Expenses:Traffic:Didi       15.00 CNY

2018-07-25 * "初始化"
    Equity:OpenBalance			
    Assets:Cash			        100.00 CNY
```

### 使用 Fava 展示账本

接下来，我们在终端中运行：

```
fava main.bean 
```

然后打开 http://localhost:5000 ，你可以看到如下界面：

![image-20180727163256797](/beancount/screenshot.jpg)

Fava  会网页的形式展示你的财务情况。

实际账本比这复杂很多，Fava 提供了一个 Demo 站点，你可以在里面看看一个实际账本是如何运作的：[Example with budgets](http://fava.pythonanywhere.com/example-with-budgets/balance_sheet/)

## 自动记账

手工记账麻烦，是很多人懒得记账的原因：日常开销数量多而且繁琐，每一笔消费都手工写账本是一件非常非常痛苦的事情。

我的解决方案是信用卡 + 自动记账脚本（[下载](https://github.com/lyricat/beancount-converter)）。

1. 每月信用卡账单日，我会从银行导出一份信用卡账单（一般是 CSV 或者 Excel 文件）。
2. 运行一个自动化脚本，将账单转换为 Beancount 格式，并且根据日常收支，自动分类交易，加上备注。
3. 最后，手工处理脚本中无法处理的交易，核账。

整个过程耗时不会超过半小时，对我来说可以接受。

## 写在最后

在部署上，你可以把 Fava 和 Beancount 连同你的账本一起放到一台服务器上，便于随时远程访问。但是我自己目前只在本地计算机使用，所以没有这样做。

最后推荐 wzyboy 的文章《[Beancount —— 命令行复式簿记](https://wzyboy.im/post/1063.html)》，我是通过该文了解到 Beancount 并开始复式记账的。除了基础知识，他还提供了一些记账上的进阶技巧和实践，如果你要开始使用 Beancount 记账了，那么这篇文章值得读一读。



[^1]: 进一步了解会计财务知识，有一本简单的小书《听妈妈讲那会计的事情》可以作为科普读物看看。
[^2]: 在 Beancount 中还有 “权益(Equity)” ，简单起见本文没有提及。