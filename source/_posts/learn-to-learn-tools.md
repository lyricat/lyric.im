---
title: 学习学习：知识管理工具
date: 2018-09-26 23:05:22
tags: ["Productivity", "生产力", "Tech", "科技", "Knownledge Management", "知识管理"]
---

[上一篇文章](https://gitpress.io/@lyric/learn-to-learn/)列举了帮助我保持学习几个重要因素。这一篇文章将会介绍如何运用工具，帮助克服在心理和生理上的弱点。

<!-- more -->

---

## 原则

我在文章“[系统导向的工作与生活方式](//lyric.im/how-to-fail-at-almost-everything-and-still-win-big/)”中有提到一个 energy system 的观点

> energy 是一种可再生资源。它会在一些日常活动中消耗，在另一些日常活动中恢复。当 energy 充沛时，做事情更有效率，情绪更愉悦，对外在表现和内在洞察力都有积极的影响。

基于这个观点，我的知识管理流程有这样的一个原则：**让程序分担我的工作，减少自己的 energy 消耗。**

程序擅长存储数据，就不去背诵文档；程序擅长做规划，就不去记住每天的任务；程序擅长管理记忆曲线，就让它去提醒什么时候应该做回顾，回顾什么东西。

简而言之，使用工具——或者程序来拓展自己，避免掉了不必要的精力消耗，就可以保留能量去做更有价值的事情。

## 流程概览

最重要的三个软件是：DEVONThink Pro、Things、Anki，如下图所示：

![flow-to-build-knowledge-base](https://gitpress.io/@lyric/learn-to-learn-tools/flow-to-build-knowledge-base.png)

其中，

- 中间的 DEVONthink 是外脑，负责收集和存储，帮我记住那些不容易记住的，繁琐的，完整的知识。
- 左上角的 Things 负责任务的规划和执行，释放维持在规划上的注意力。
- 右上角的 Anki 会帮我做好复习计划，负责强化记忆和对知识的索引。

除去这三个软件之外，还使用 Dropbox 来同步所有的知识，并使用其他程序来完成特定的任务。接下来就让我来介绍他们。

---

## DEVONthink

### **支持任何格式的文件**

很多人会纠结笔记软件是否支持 Markdown，如果支持 Markdown，又纠结多媒体和表格的支持程度。

类似的种种，是个没什么意义的纠结心态。知识适合用什么格式就用什么格式好了啊，用 Markdown 写博客，用 PDF 做标记，用 Web Archive 归档网页，剩下的事情交给 DEVONthink 好了。

![image-20180926221439107](https://gitpress.io/@lyric/learn-to-learn-tools/devonthink-overview.png)

### **全文检索**

因为 DEVONthink 会索引每一个文件的内容，可以进行全文检索。不过，DEVONthink 不支持中文分词，所以对中文内容是没有效果的。算是一个不足——可以通过多用英文来解决。

![image-20180926221739905](https://gitpress.io/@lyric/learn-to-learn-tools/devonthink-search.png)

说起来这个功能真的很强，我知道一些朋友是用它来管理文献的。

### **魔法分类**

只要点击文件上的魔法帽子图标，就会自动展示这篇文档的相关文档，并能推荐到对应的分类中去。

是不是很魔法？但是在我们程序员看来，只不过是因为 DEVONthink 在索引文件时会进行 token 计数，然后在多维空间里对文本向量进行了夹角计算而已（其实只是做文本相似度计算）。

![image-20180926221937177](https://gitpress.io/@lyric/learn-to-learn-tools/devonthink-magic.png)

使用时，经常会先把文档先丢到 INBOX，处理完了后再自动放到对应的分类中去，非常便利。

## Things

### 面向心智的任务组织

本来因为朋友介绍，我有半年都在使用 OmniFocus（简称 OF）。后来用了 Things，就把 OF 完全丢掉了。原因就是 OF 的任务组织形式是“面向任务”的，而Things 的任务组织形式是“面向心智”的。

比如说，在 Things 中，你可以使用 **Heading** 来标记一组任务。这个 Heading 对逻辑上的任务组织结构没有影响，但在使用上会得到一种秩序的感觉。

![image-20180926223238128](https://gitpress.io/@lyric/learn-to-learn-tools/things-heading.png)

### 没有负担

另一个选择 Things 的原因是使用它归档任务没有负担。在整个流程中，我知道真正有价值的东西最终都会进入 DEVONthink，需要被索引的内容都会进 Anki，因此不需要担心在 Things 中任务被标记完成后是否会被遗漏。

## Anki

### 好的记忆曲线管理

作为一个来自日本的开源软件，Anki 身上到处都是 “日本”和“开源” 的影子——就是**外观不好看又难用**的意思。但是，这个一点儿花里胡哨都没有的 Anki 的记忆曲线管理做得非常好。

![image-20180926224034808](https://gitpress.io/@lyric/learn-to-learn-tools/anki-main-view.png)

作为一个记忆工具，能做好这一件事就已经很好了……

### 自定义卡片

Anki 可以使用 HTML 和 CSS 定制记忆卡片，所以可以根据要记忆的东西，定制不一样的样式，而不仅仅是背单词。

![image-20180926223839164](https://gitpress.io/@lyric/learn-to-learn-tools/anki-word-view.png)

### 学习报告

Anki 有提供很好的统计报表功能，可以很容易看到最近自己有没有努力。

![image-20180926224149221](https://gitpress.io/@lyric/learn-to-learn-tools/anki-report-view.png)

## Dropbox, Keynote, Skim, Typora...

选择这些软件的原因是他们是这个领域里最好用的 App... 没有之一。

比如说……

- Skim 是 macOS 下最好用的 PDF 阅读器
- Typora 是最好的 Markdown 编辑器
- Dropbox 是最好的数据同步软件
- Keynote 是最好的 Slide 软件
- 等等啦，我不接受反驳的。

---

## 适配移动设备

当不使用电脑时，我有一半的时间在使用我的手机。所以知识管理流程必须能够在手机上完整进行才行。能做到吗？当然是完·全·可·以。

以上核心软件都有可用的移动端版本，并且通过 iOS/Android 系统的机制，有效完成程序之间的互动和操作。

以收集知识为例：

- 在 Safari 上阅读到的文章，可以很容易通过 Extension 复制到 DEVONthink 移动版中，并且生成 PDF 或者 Archive。
- 桌面版 DEVONthink 完成同步，可以在桌面版打开该文章，完成阅读和笔记。
- 将关键概念和文章概要加入 Anki。
- 回到手机场景，移动版 DEVONthink 中，可以随时查阅文章和笔记。

---

## 价格和替代品

如果完全按照我的配置来使用这些软件，大概要花费……

- DEVONthink Pro: **$79.95**
- DEVONthink to Go: **$14.99** + $7.99 Pro package
- Things macOS: **$49.99**
- Things iPhone: **$9.99**
- Anki: $0

共计 **$162.91**，折合人民币 **¥1,115.90**。

这些软件如果每天都有在用的话，那就把开销平摊到每天看看，每天只需要 3 块钱，并不算贵的。不过我还是讲一下替代品好了。

DEVONthink 没有特别好的替代品，不过也可以用 Finder + Spotlihgt 来替换一下就是了。

Things 就是一个 TODO list，当然可以用任何一个待办事项 App 来替换它。

Anki 的话，去 App Store 搜索 Memo 应该能得到很多记忆工具；但是 Anki 是免费的耶，为什么不用呢。

如果你用视窗操作系统，我不是很熟悉了，可能需要你自己去找替代品了。

---

## 后记

虽然我们说“**重剑无锋**，**大巧不工**”，但是我们也说“**人类社会的发展由劳动工具的演变所主导**”对吗。而且作为一个致力于创造更好的产品的职业，追求更好的工具更应当是自我修养才对。