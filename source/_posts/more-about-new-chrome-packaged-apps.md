---
title: 论新 Chrome Packaged Apps
date: 2013-02-25 11:30:42
tags: ["Chrome", "Web", "Tech", "科技", "Coding", "编程"]
---

新版本的[Packaged Apps](http://developer.chrome.com/trunk/apps/about_apps.html)很有诚意。

- 硬件访问：蓝牙，串口，USB
- Socket：能使用 TCP/UDP 协议的套接字
- Push Messaging：Google 自家的云消息推送
- 热键支持：不需要自己实现热键系统，而且这样的 Apps 可以使用与 Chrome 同级别的热键（赞），就是有点苛刻，要求 hard coded 到 manifest.json 里
- 加强的 storage API：异步的 localStorage，加了 chrome.storage.sync 这个 storage，让 Chrome 同步时顺便把 Apps 的 localStorage 也同步了，不过这个仅有 100k 的 quota 也就只能用来存点配置信息。
- webstore API：从此可以在自己的站点安装 Apps 而不需要跳到 Chrome Web Store
- 文件系统 API：沙盒中的本地文件系统访问

还有一些别的特性也蛮有趣，例如 tts，runtime之类。

也有大量的[限制和禁用特性](http://developer.chrome.com/trunk/apps/app_deprecated.html)。最苛刻的当属默认的[CSP](http://developer.chrome.com/trunk/apps/app_csp.html)，不允许引用除了视频和音频以外的所有外部资源，包括图片。当然，XHR 没有任何限制。

通过这次对 Apps 策略的改动可以看出，G家是真心在实践颠覆操作系统领域的野心。没有历史拖累着造轮子就是舒服就是好。
