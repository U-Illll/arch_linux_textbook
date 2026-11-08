# 第 35 章　Usenet 客户端

> 本章解决的是把 Hellanzb 真正用起来的问题：它是一款 Usenet 客户端，先弄清主要功能能覆盖到哪一步，再完成安装，最后把配置调整到与自己环境相符。
> 
> 本章只有一节，功能、安装、配置顺次讲完，没有可跳过的部分；若已用过同类客户端，功能部分可以略读，安装与配置仍要逐项过一遍。最容易踩的坑是把安装成功当成任务结束，装完并不等于能用，配置没对齐就始终跑不起来。先看清功能的边界再动手改配置，比事后逐条排错省力得多。

<!-- 本章 1 节，共 2 KB -->

## 35.1　Hellanzb

*难度：入门 ｜ 篇幅：1.9 KB ｜ 所属：Usenet 客户端*

将要讲到：主要功能、安装、配置

> [![](https://wiki.archlinuxcn.org/wzh/images/9/9c/User-trash-full.svg.png)](https://wiki.archlinuxcn.org/wzh/File:User-trash-full.svg.png)**本文已被弃用。**
>
> **本文所述教程或工具由于已经过时而被弃用，请使用其他替代方案：** 参见[应用程序列表/互联网#新闻组新闻阅读器](../网络应用程序/应用程序列表_互联网.md#新闻组新闻阅读器)。 (在[Talk:Hellanzb](https://wiki.archlinuxcn.org/wiki/Talk:Hellanzb)讨论)

[Hellanzb](https://github.com/pjenvey/hellanzb) 是用 Python 编写的基于控制台的 Usenet 二进制下载器。它可以在守护程序模式下运行，并且可以使用多个 GUI 或 PHP 前端。

> **注意：**尽管开发人员和测试团队认为最新版本（0.13）稳定，但 hellanzb 不再处于活动开发中。

## 主要功能

- 自动下载放置在用户指定的监视目录中的 nzb 文件。
- Par1/2 验证和修复损坏的文件。
- 取消完全下载。
- 下载完成后，删除不需要的文件。

## 安装

Hellanzb 在 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 软件包 [hellanzb-git](https://aur.archlinux.org/packages/hellanzb-git/)AUR\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\] 中可用。

## 配置

Hellanzb 可以在两种模式下运行：

- 从 CLI
- 作为守护程序

### hellanzb.conf

用户 `.config` 目录中必须存在一个名为 `hellanzb.conf` 的配置文件，hellanzb 才能正常运行。提供了一个示例配置文件。

```
mkdir -p ~/.config
cp /etc/hellanzb.conf.sample ~/.config/hellanzb.conf
```

### /etc/conf.d/hellanzb

在守护程序模式下运行时，需要修改 `/etc/conf.d/hellanzb` 文件以配置将要运行 hellanzb 的用户

```
HELLANZB_USER="username"
HELLANZB_CONF="/home/${HELLANZB_USER}/.config/hellanzb.conf"
```
