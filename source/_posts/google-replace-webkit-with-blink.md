---
title: Chromium 用 Blink 引擎代替 Webkit
date: 2013-04-04 09:40:28
tags: ["Tech", "科技", "Google", "Webkit"]
---

对用户的影响：

 - 这一改进主要在技术层次上，会使得 Chrome 和 Chromium 更高更快更强。
 - 有性能的支持，Google 才能拿出足够好的 WebApp。

看，非技术的部分两句话说完了，也就是整个分析的结论，对用户只好不坏。

<!--more-->

技术部分，换引擎的理由倒是很显然：

- 因为 Webkit 性能不够
- 因为不适合 Chromium/Chrome 的多进程架构
- 清晰的 codebase 有利于以后 Chromium 的发展

对生态圈的影响：

Google 的目标就是让 Web 更快。

1. 最初是觉得浏览器都太烂于是坑了 Mozilla，出了 Chrome
2. 其次是觉得 Javascript 的 VM 太慢，所以出了 V8 引擎
3. 然后觉得 V8 还是不给力啊，本质上还是 Javascript 太渣了，搞个 Dart 语言看看；
4. 之后 Google 发现好像是 ISP 跟不上我们的脚步了，于是[开始给用户铺光纤](https://fiber.google.com/about/)
5. 觉得 HTTP 太低效了于是推出 SPDY
6. 实验性的 Chrome OS 和 Chromebook
7. 攻击性很强的[新 Chrome App API](http://lyric.im/more-about-new-chrome-packaged-apps/)发布
8. 再到今天的 Blink 代替 Webkit

总之就是要让网速不是障碍，让网络服务速度不是障碍，最后让 WebApp 速度赶上本地程序，然后就可以跟各大 OS 正面开战了。

对开发者的影响：

对所有 WebApp 来说也是利好消息，因为目前针对新引擎的野望中，有 3 项的改进会让 DOM 操作变得更快，并且明确说明会重写整个 Webkit的 DOM 实现。考虑到现在 DOM 操作速度如此之慢，已经是 WebApp 发展的瓶颈了，Google 拿它开刀是理所当然。

1. 提升 DOM 3 Event 和 UI Event 的性能
2. 解决目前 Webkit 对 DOM 的向后兼容性所导致的性能问题
3. DOM 移到 Javascript heap

前端工程师不需要太担心：

1. Blink fork 自 Webkit，以后也会兼容已经成为标准的 `-webkit-*` 私有特性。
2. Chromium 团队渐进性更新很靠谱。
3. 考虑到 Google 的操性，如果要往 Chromium/Chrome 加私货，一定会加到 Chrome App 和 Chrome extension 里；如果实在要往通用 Web 里加，一定会先折腾成标准或者至少是标准草案，然后自己先拿出实现来；因此不会形成IE里的 ActiveX 那种东西。
