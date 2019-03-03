---
title: 以 Hexo 为中心的知识管理
date: 2016-08-27 02:27:37
tags: ["Productivity", "生产力", "Hexo", "Knownledge Management", "知识管理"]
---
身边一直有朋友们抱怨 Evernote 的笨重、格式错误、移动端同步混乱、不能添加代码和公式、充满 Bug 和崩溃。

如果你也面临这些问题，是时候考虑像我一样，放弃这些专用笔记软件了。

我现在享受着如下便利：

- 开放的存储方式，而不是私有格式数据库：我使用纯文本的 Markdown。
- 使用文件系统组织文档，享受 Spotlight 高效全文检索和 [Dropbox](http://dropbox.com) 全平台同步的便利。
- 使用喜欢的编辑器写笔记，比如 [Atom](http://atom.io)。
- 使用 [Hexo](http://hexo.io) 博客系统进行内容管理和发布；亦可输出成别的格式，方便发布到其它平台。

严格来讲，这是以文件系统为中心的知识管理。但我喜欢 Hexo，偏要说成以 Hexo 为中心的知识管理。

<!--more-->

## 为什么是 Markdown

- 简单：三分钟学会，日常使用没有任何门槛（可参考[简书的中文教程](http://www.jianshu.com/p/q81RER)）。
- 强：等价于 HTML，随意嵌入多媒体、公式和代码。
- 跨设备、跨平台、不依赖私有格式；既然是纯文本，所以在哪都能打开。
- 灵活：可以输出成多种格式，方便在不同的平台上发布。

## 为什么用文件系统而不是笔记软件？

- 像 Evernote 和 Notes.app 这样笔记软件不支持 Markdown。
- 文件系统更直观，方便与 boxcryptor, Dropbox, git 等第三方软件搭配。
- 方便管理图片、视频等多媒体文件。

## 为什么用 Atom 编辑

- 安装 Project Management 插件，自带管理功能。
- 写代码也用 Atom，保持一致的习惯和手感。
- 当然，也可以使用其它喜欢的编辑器。

![](/hexo-centric-knowledge-managment/atom.jpg)

## 让 Spotlight 检索 Markdown 文件

Spotlight 是 macOS 和 OS X 上最强大的桌面工具，其中最棒的功能是对文档的全文检索。

默认 Spotlight 是不检索 Markdown 文件内容的，但[参考这篇教程](/make-spotlight-index-markdown-files/)，只需简单三步即可支持。效果如下：

![](/fixing-spotlight-indexing-of-markdown-content/spotlight.jpg)

![](/fixing-spotlight-indexing-of-markdown-content/mdquery.jpg)

## 使用 Hexo 管理发布

我们有博客、笔记、日记、公众号等多个发布渠道。写的东西多了，有一个集中管理内容的地方会很方便。

- Hexo 是一个很好的 Blog 系统，适合公开发表的东西可以直接发布出去，或者转格式后发布到其它平台。
- Hexo 也是一个很好的文档组织系统，不适合公开发表的东西放在本地，使用 `hexo` 命令配合本地工具可以很好地管理所有文档和笔记。
- Hexo 的 `hexo server` 还可以实时预览，非常方便。

具体的使用方法，可以参阅[官方提供的教程](https://hexo.io/docs/)。
