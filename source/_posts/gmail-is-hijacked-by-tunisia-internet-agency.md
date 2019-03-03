---
title: 突尼斯劫持 Gmail
date: 2011-01-08 15:09:19
tags: ["Tech", "Security"]

---

其实下三滥的手段大家都不怯于用，用得好那是行为艺术；否则就像突尼斯政府一样(via: [1](http://advocacy.globalvoicesonline.org/2010/07/05/mass-gmail-phishing-in-tunisia/), [2](http://www.cpj.org/internet/2011/01/tunisia-invades-censors-facebook-other-accounts.php))，居然还有一个﻿ Tunisia Internet Agency。相比之下我们只有“相关部门”，东方人思维方式真不知道高到哪里去了。

<!--more-->

但是，Gmail 不是那么好伪装的。因为 Gmail 登录后强制使用 https，所以这次突尼斯也只能靠 ban 掉 https 来做前戏。

虽然 Gmail 在登录后强制 https，但我们登录 Gmail 的方式上却值得商磋。

有多少人是直接输入 gmail 地址来进入 Gmail 的？

直接输入 `gmail.com`？那就是 `http://gmail.com`，服务器会通过 `301` 重定向到 `http://mail.google.com`， 再 `302` 重定向到 `https://www.google.com/accounts/ServiceLogin`。

注意到 `http://gmail.com` 和 `http://mail.google.com` 都是 http 的，像突尼斯 TIA 其实完全可以通过这劫持两个页面伪装出一个稍微像那么回事的 Gmail 登录页面。但 TIA 没有，可见他们还是太幼稚。

也给喜好到处蹭网的同学提个醒，来路不明的 Wifi 也好，酒店的免费网络也好，使用的时候一定要小心。全程 VPN/SSH tunnel 护航是最佳选择，否则就老老实实地只敲 https://mail.google.com 而且自行校验证书。

你说不登录关键网站可以不。不行，浏览器缓存是可以投毒的，没有 https 就跟裸体没什么区别。
