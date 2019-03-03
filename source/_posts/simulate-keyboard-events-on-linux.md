---
title: Simulate keyboard events on Linux
date: 2010-10-05 15:09:19
tags: ["Tech", "Linux", "Coding"]
---

High level GUI Library/Framework(like QT, GTK+) make it easier to develop GUI programs in Linux. But both of them are lack of some features because their design purpose.

So it seems, I have no thing to do unless programming with Xlib if i want to to simulate keyboard events.

Fortunately, I got XTest(the X test extension), a library to test X11 server without user intervention, which can be used to simulate mouse and keyboard events in three ways.

<!--more-->

```
XTestFakeMotionEvent()
XTestFakeButtonEvent()
XTestFakeKeyEvent()
```

To use it, as easy as doing﻿ simulation in MS Windows(<em>SendMessage()</em> with <em>KEY_PRESS</em> Message). Examine X11/extensions/XTest.h for more details. And [this article](http://docsrv.sco.com:507/en/man/html.Xext/XTEST.Xext.html) explain the functions and arguments of XTest.

There is a more convenient approach to fake keyboard event for python users, just like me :). It's [virtkey](https://launchpad.net/virtkey), python extension for emulating keypresses and getting current keyboard layout. In fact, this package is a wrapper for most used functions in XTest, but only keyboard simulation.

Usage:

```python
import virtkey
import time

def simulate():
    v = virtkey.virtkey()
    time.sleep(3)
    v.press_unicode(ord("a"))
    v.release_unicode(ord("a"))
    v.press_keysym(65363)
    v.release_keysym(65363)
    pass

if __name__=='__main__':
    simulate()

```

Whether it's used as a part of a program or just some automatic test-cases for GUI programs, are not bad. The virtual keyboard in Ubuntu, OnBoard, written with it.
