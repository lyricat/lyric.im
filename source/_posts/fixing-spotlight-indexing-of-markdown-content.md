---
title: 让 Spotlight 检索 Markdown 文件
date: 2016-09-13 17:32:35
tags: ["Productivity ", "生产力", "MacOS"]
---


Spotlight 是 macOS 和 OS X 上最强大的桌面工具，其中最棒的功能是对文档的全文检索。

默认 Spotlight 是不检索 Markdown 文件内容的，但只需简单三步即可支持。

<!--more-->

**第一步：关闭 Mac 的系统文件保护**
进入恢复模式，在 Terminal 里输入 `csrutil disable`，重启。

**第二步：让 Spotlight 检索 Markdown**
编辑 `/System/Library/Spotlight/RichText.mdimporter/Contents/Info.plist`，在 `LSItemContentTypes` 字段中添加 `<string>net.daringfireball.markdown</string>`。

**第三步：重载配置并重建检索**
在 Terminal 里执行 `mdimport -r /System/Library/Spotlight/RichText.mdimporter` 和 `sudo mdutil -E /`。
等待 10 分钟，让 Spotlight 建立好索引，就能检索到 Markdown 文件的内容了。

![](/fixing-spotlight-indexing-of-markdown-content/spotlight.jpg)

成功后，最好再进入恢复模式，使用 `csrutil disable` 打开系统文件保护。

## 在终端中检索

另外，由于我经常工作在终端下，使用命令行工具 `mdfind` 有时候更方便。所以特意包装了一个脚本：

```bash
#!/bin/sh
query=$1
path=$2
for file in `mdfind "$query" -onlyin $path`
do
    case "$file" in
        *.md)
            echo "\033[36m$file \033[0m"
            grep --color=auto -i -m 10 "$query" $file
            echo ""
            ;;
    esac
done
```

脚本运行结果就像 Google 搜索，会高亮显示检索到的文件路径和文件内容。使用方法如下：

![](/fixing-spotlight-indexing-of-markdown-content/mdquery.jpg)
