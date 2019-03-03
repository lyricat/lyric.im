---
title: 记一次数据恢复
date: 2011-04-24 15:09:19
tags: ["linux", "数据恢复", "Data recovery"]
---

@[sswv](http://twitter.com/sswv) 提到：

> 在以前DOS时代，备份分区表是习惯性操作来着（因为实模式嘛，病毒随便写）。但现在人们反而忘记了。

<!-- more -->

## 背景

我的笔记本上安装有一个 Linux，一个 Windows 7。其中 Win 7 是默认的操作系统。由于这个历史原因，以及当时犯懒，后来安装的 Debian Linux 就放在了扩展分区。

我想安装黑苹果，需要给 OS X 准备一个空分区。于是我将一个扩展分区划分成两个，打算用其中一个 30GB 的分区来安装黑苹果。@[Sisyphusliu](http://twitter.com/Sisyphusliu) 得知后，声称这是他这辈子见过的除了虚拟机以外，最可怜的 Mac 了。

<!--more-->

## 惨案开始

之前体验过 Linux 上 Disk Utilities 的不靠谱，于是我重启切换到 Windows 7，打算用 Windows 来给调整分区。

但是，**我忘记了 Windows 是绝对不管 Linux 分区死活的**。于是 Windows 分区结束后，扩展分区的 EBR 被改写了。扩展分区中的所有 ext 分区和 reiserfs 分区全部无法访问。

尝试恢复。先登录到 Ubuntu LiveUSB，用 `fdisk` 看看，果然分区表已经坏掉了：扩展分区中剩下的分区都不正常。

理论上重新用 fdisk 划分就可以了，但我并没有记下之前分区的偏移和大小。@sswv 让我试试数据软件软件，我试过后没有什么效果。

这时候我想起之前给 @[Paveo](http://twitter.com/paveo) 发过分区情况，于是去 Gmail 里面找到分区偏移信息，重新划分。然而结果依然不正确。

@Sisyphusliu 说，@sswv 你上回不是用 mount 暴力找回了一个分区位置么？试试那个方法啊。

参考 @sswv 的做法（他的文章已经找不到了），我写了一个 Python 版本：

```python
#!/usr/bin/python
import subprocess
import sys
base = int(sys.argv[1])
for i in range(0, 1000000):
    if 0 == subprocess.call(['/bin/mount','-o','offset='+ str((base+i)*512), '/dev/sda','/mnt']):
        print 'Find a partition! offset = %d' % (base+i)
        break
```

使用的时候在脚本后面加个起始猜测参数，单位是 512 bytes，和 `fdisk -ul` 的单位是一样的。

最后我成功地用这个脚本找到了所有分区位置。重新分区后，数据都回来了。
