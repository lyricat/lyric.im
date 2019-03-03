---
title: Web App 开发的最佳实践
date: 2013-04-27 16:01:20
tags: ["Tech", "科技", "Coding", "开发", "Web"]
---

现在精通 Web 开发门槛挺高的。关键在于要面对一系列从 Web 诞生至今延续下来的开发技术。换言之，假如在 Windows 下开发需要从 MS DOS 开发实模式程序开始学起，学到 .Net 4 的所有 API 结束，会让程序员哭出来。

但是 W3C 做到了。

前段时间 [forecast.io](http://forecast.io) 的 App 很火（请用 iOS Safari 打开链接 [http://forecast.io](http://forecast.io)，然后添加到桌面看效果）。接着官方博客释出了[一篇文章](http://blog.forecast.io/its-not-a-web-app-its-an-app-you-install-from-the-web/)，可以认为是当前 Mobile Web App 开发的最佳实践：

<!--more-->

> 1. 不要刻意模仿 iOS 的默认样式和交互。
>    - 毕竟 WebApp 缺乏原生支持，肯定有模仿不到位的地方，会很快露出马脚。
>    - 用自己的风格和交互则不会有这样的问题，没有参照物也就不容易察觉错误。
> 2. 也不要做得像个网站。
> 3. 只用硬件加速的 CSS3 属性做动画效果。
>    - 只使用 `translate3d` 系属性，并且在使用 `transition` 时，也不可以应用到没有硬件加速的属性（比如 `margin-left`）上。
> 4. 不要自己实现滚动条。
>    - 使用 `-webkit-overflow-scrolling: touch` 而不是采用类似 [iScroll](http://cubiq.org/iscroll) 的解决方案。
> 5. Web 技术上还做不好的事情就别强行去做。
> 6. 用 [FastClick](https://github.com/ftlabs/fastclick) 和 [Application Cache](https://developer.mozilla.org/en-US/docs/Web/HTML/Using_the_application_cache) 提升用户体验。
> 7. 多吃自己狗粮。

<!-- more -->

对于第 3 点，在 Chrome Dev Tools 里打开 `Show paint rectangles` 和 `Show composited layer borders` 选项，就能直观地看到浏览器是怎么渲染你的 WebApp 的。这对于观察硬件加速的优化效果来说很有帮助。

对于第 4 点，目前仅对 Mobile Web App 适用。桌面 WebApp 使用大数据的动态列表还是用 [iScroll](http://cubiq.org/iscroll) 这样的实现吧，性能和效果都比原生滚动强得多。

接下来几点是我总结的，大型 WebApp 的优化实践。

 1. 不要写……大型……WebApp……
 2. Model 的观察者要小要短.
 3. 高频事件（例如 WebSocket ）的回调函数中需要对 View 进行修改的，用 throttle 控制事件频度。
 4. 尽量不要直接操作 DOM，交给框架。
 5. 局部性原理：操作 DOM 的语句尽可能连续地放在一起。
 6. 多条语句连续地操作 DOM 时，用 setTimeout 分割它们可以得到明显效果。
 7. 程序很慢，但是人类更慢。除非有明显的性能问题，否则不要过度优化（例如使用以上优化方式），只会搞得更糟。

当然了，如果选择 React.js，可以解决上述的大部分问题。

Angular.js 特有的：

1. 一个页面中不要超过 2000 条 Angular 表达式。
2. Angular.js 使用 dirty check 实现数据绑定，因此在 digest cycle 中尽量减少深度比较，仅仅包含会导致 View 变化的操作。

话说回来，总算看到了 WebApp 的一线曙光，所以先从 HybridApp 开始吧。别说你讨厌 HybridApp，其实它早就潜入我们身旁你都不一定感觉得到。例如 Tweetbot（最初的 OS X 版），SpeedTao 还有 …… 微信。

说不喜欢的，大多数只不过是技术性偏执罢了。
