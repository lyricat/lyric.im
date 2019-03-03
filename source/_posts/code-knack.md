---
title: Code-Knack - 把网页上的代码变成可执行的框框
date: 2019-02-01 21:26:44
tags: ["Code-Knack"]

---

我做了个小东西叫 [Code-Knack](https://github.com/lyricat/code-knack)。它能把网页上的代码变成可执行的编辑器。

<!--more-->

比如说我在博客上写了一段代码：

```c
#include <cstdio>
int main() {
    printf("hello %s\n", "Code-Knack");
    return 0;
}
```

它能把代码变成可执行的状态。点“Run”可以运行，点“Copy”可以复制。有代码高亮，你的读者也可以修改这个代码再看它的运行结果。

除了 [Cpp](https://github.com/felixhao28/JSCPP) 和 Javascript 以外，现在还支持 [Python 2.7](https://github.com/lyricat/blog/blob/master/skulpt.org)、[Ruby](https://opalrb.com/#)、[Scheme](https://www.biwascheme.org/)，以后肯定要支持更多语言，和更多库的。

除了我这个博客，你也可以去 Github Page 看[简单的 Demo](https://lyricat.github.io/code-knack/demo/)。

---

做完了我才知道 [klipse](https://github.com/viebel/klipse) 这个项目…… 不过还是有一些优势：

- 好看点
- 容易集成，自动插入依赖
- 支持客户端运行，也保留服务端运行的能力
- 移动端适配

总之，喜欢 Code-Knack 的话，就[点这里去 Github 帮我加个 ★](https://github.com/lyricat/code-knack) 吧

