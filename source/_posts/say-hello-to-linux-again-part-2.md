---
title: Say Hello to Linux again - Part 2
date: 2021-09-13 11:12:46
tags: ["Linux", "Linux Desktop"]
---

In [the last post](https://gitpress.io/@lyric/say-hello-to-linux-again), I explained that the reason I switch from macOS to Linux.

It's a smooth transition for me, so I am sure it's absolutely a good idea. The KDE plasma 5 has great user experience, and the WebApp makes it easy to migrate from macOS to Linux.

However, there are some things I wanna note about the transition, which may be helpful for the people who are still using macOS and want to switch to Linux.

## Hardware

For daily usage, we must admit that the hardware compatibility of Linux is not perfect. That's why before I buy the new laptop, I browsed the ArchLinux wiki and found that the Dell XPS has a good compatibility with Linux.

In the end, I bought the [Dell XPS 13](https://wiki.archlinux.org/title/Dell_XPS_13_(9310)).

## ArchLinux

I have been using several Linux distributions before, includes Ubuntu, Fedora, Gentoo and Debian. But I have never used ArchLinux.

ArchLinux is a rolling release distribution. Some of my friends have different options for rolling release. They think the rolling release distribution is not stable enough, and it costs a lot of time to maintain the system.

They are right, sometimes. However, I prefer to experience the latest version of applications and the packages rather than the stable system.

In the past, when I was a macOS user, I used to really care about the stability of the system. But now I change my mind. If I meet some bugs or faults, I can dive into the system implementation to solve them. That's an advantage of the open-source.

Of course, ArchLinux wiki is very well written, and the community is friendly, which will provide great help.

## Screen Flickering & Random Hangs

It's a problem that occurs on both Wayland and Xorg, a driver issue of i915. You can read the issue [here](https://wiki.archlinux.org/title/Dell_XPS_13_(9310)#Known_issues) and the [discussion here](https://wiki.archlinux.org/title/Talk:Dell_XPS_13_(9310)#Video_drivers). This problem is also described in the [wiki page](https://wiki.archlinux.org/title/intel_graphics#Screen_flickering) about Intel graphics.

The solution is to uninstall the legacy driver `xf86-video-intel` and use the Kernel's modesettings instead.

Then add `i915.enable_psr=0` to the [kernel parameters](https://wiki.archlinux.org/title/Kernel_parameter).

If you are using grub, edit `/etc/default/grub` and append your kernel options between the quotes in the `GRUB_CMDLINE_LINUX_DEFAULT` line:

```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet acpi_rev_override=1 i915.enable_psr=0"
```

and re-generate the grub configuration file with:

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

The option is referred to "Panel Self Refresh (PSR)", a power saving feature used by Intel iGPUs. I guess it's a power management feature that is used to reduce the power consumption. According to the description of the workaround, I found we can also use the option `intel_idle.max_cstate=1` to solve the problem too, because it's also an option to increase the power consumption.

I have tested both of them on my laptop and they are working fine.

## Wayland

At the first, I used Xorg just like what I did 10 years ago. Then I found the Wayland seems to be good for new hardwares and the KDE is ready for the Wayland.

I read [this blog post](https://www.fosskers.ca/en/blog/wayland) and used Wayland.

Wayland works really well with my laptop. But there are some things I wanna note about the video driver configuration.

## Application Alternatives

As I mentioned in the last post, a major problem of switching from macOS to Linux is the application compatibility. It costs some time to find excellent alternatives for the applications that I used in macOS.

As a conclusion, I have 3 recommendations for you.

**1. Using good quality crossing-platform applications**

For example, Firefox, Thunderbird, and Visual Studio Code (and the FOSS version) are open source crossing-platform applications with good community support, which are much better than most of the native competitors of Linux.

**2. Using web applications**

For example, Figma, Slack, Telegram, Google Meet and Google Docs are web applications with pleasant user experience. With them, you don't need to install any native desktop applications for designing, chatting, and office stuffs.

**3. Reduce the desire**

To be honest, the applications are just tools to make work and life easier. The work and life can be done by limited typical applications, I don't want to spend too much time on them.
