# 第 1 章　关于 Arch

> 本章把 Arch 从零散印象整理成可查、可用、可参与的对象：核心原则与历史、官方仓库与镜像源、构建与打包、AUR 与提交准则，以及它和其他发行版的取舍差异。读法上先立坐标：《Arch Linux》《官方仓库》《镜像源》《Arch 术语》必读，读过才知道 core、ABS、AUR 各指什么；再走流程：《创建软件包》《Arch 用户软件仓库》《AUR 提交准则》连读，从 PKGBUILD 一路到提交；《参与贡献》《Arch 与其他发行版的比较》值得细读；《目录》《首页》、新闻报道、愚人节笑话一类偏名录与谈资的，可以略过。
> 
> 最容易犯的错是把 AUR 当成官方仓库，以为里面的包由 Arch 团队维护；其次是跳过《镜像源》直奔软件源，没弄清镜像排序与启用特定镜像的规则，之后在仓库与包管理上反复卡壳。

<!-- 本章 18 节，共 268 KB -->

## 1.1　Arch Linux

*难度：进阶 ｜ 篇幅：10.2 KB ｜ 所属：关于 Arch*

将要讲到：核心原则、历史沿革

Arch Linux 是一个通用操作系统，它是基于 x86-64 架构的一个 [GNU](../../06_软件与应用/GNU/GNU.md)/Linux 发行版。

Arch Linux 采用滚动升级模式，尽全力为使用者提供最新的稳定版软件。初始安装完成的 Arch Linux 只是一个基本系统，随后用户可以根据自己的喜好安装需要的软件，并自己动手配置成理想的个性化系统。

## 核心原则

以下核心原则构成了我们通常所指的 Arch 之道，或者说 Arch 的哲学。对于这些原则或许最好的结词是 Keep It Simple, Stupid（对应中文为“保持简洁，且一目了然”）。

### 简洁

Arch Linux 将简洁定义为：**避免任何不必要的添加、修改和复杂化的增加**。它提供的软件包都来自原始开发者（以下简称[上游](https://en.wikipedia.org/wiki/Upstream_(software_development))），且仅进行本发行版（以下简称下游）相关的最少改动。

- 不包含上游不愿意接受的补丁。绝大部分 Arch 下游补丁都已经被上游接受，会在下一个正式版本里被包含。
- 配置文件同样来自上游，下游对它的改动仅包含必需的调整，比如特殊的文件系统路径变动。此外Arch Linux在安装软件包后不会自动启用和启动软件包提供的服务。
- 软件包通常都和一个上游项目直接对应，仅在极少数情况下才会拆分软件包。
- 官方不支持图形化配置界面，建议用户使用命令行或文本编辑器修改配置。

### 现代化

Arch Linux 尽全力使软件处于最新的稳定版本，只要没有出现系统软件包损坏，都会尽量使用最新版本的软件包。

Arch Linux 采用[滚动更新](https://en.wikipedia.org/wiki/Rolling_release)的方式，安装之后可以通过命令持续更新系统与软件包。

Arch Linux 现面向 [GNU](../../06_软件与应用/GNU/GNU.md)/Linux 用户提供了许多新特性，包括 [systemd](../../01_安装与包管理/初始化/Systemd.md) 初始化系统、现代化的[文件系统](../../02_系统管理与内核/文件系统/文件系统.md)、LVM2/EVMS、软件磁盘阵列（软 RAID）、[udev](../../04_硬件与外设/硬件检测和故障排除/Udev.md) 支持、initcpio（附带 mkinitcpio）以及最新的 Linux 内核。

### 实用性

Arch Linux 注重实用性，且尽力避免意识形态之争，最终的设计决策都是由开发者之间的共识决定。开发者依赖基于客观事实的技术分析和讨论，避免国家之间政治因素的影响，不被世俗流行观点左右。

Arch Linux 的软件仓库中包含大量的软件包和编译脚本，用户可以按需自由选择。此外仓库中既提供了开源、自由的软件，也提供了闭源软件，即**实用性大于意识形态**。

### 以用户为中心

许多 Linux 发行版都试图变得更加“用户友好”，而 Arch Linux 则一直是、且永远会是“**以用户为中心**”：

- 本发行版的目标是满足向它贡献的人的需求，而非吸引尽可能多的用户。
- 本发行版的目标用户是熟练的 GNU/Linux 用户，或者任何乐于自己动手、愿意阅读文档、解决自己的问题的人。

Arch Linux 鼓励每一个用户[参与](参与贡献.md)和贡献，报告和帮助修复 [bugs](https://gitlab.archlinux.org/groups/archlinux/packaging/-/issues)，提供软件包补丁和参加核心[项目](https://gitlab.archlinux.org/)。Arch 开发者都是志愿者，通过持续的贡献成为团队中的一员。*Archers* 还可以自行贡献软件包到 [Arch 用户仓库](Arch%20用户软件仓库.md)，提升 [ArchWiki 文档质量](首页.md)，在[论坛](https://bbs.archlinux.org/)、[邮件列表](https://lists.archlinux.org/mailman3/lists/)或者 [IRC](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md) 中给其它用户提供技术支持。

Arch Linux 已经是全球许多 GNU/Linux 用户的选择，还有许多[国际社区](../../06_软件与应用/列表/国际社区.md)在提供帮助和翻译文档。

### 通用性

Arch Linux 作为一种通用 Linux 发行版，它的初始安装仅提供命令行环境。由于 Arch 默认提供最小化安装，用户不需要删除大量不需要的软件包，而是可以从[官方软件仓库](官方仓库.md)成千上万的高质量软件包中进行选择，用于搭建自己的系统。目前仅支持 [x86-64](https://en.wikipedia.org/wiki/x86-64) 架构。( [对 i686 架构的支持已经结束](https://www.archlinuxcn.org/the-end-of-i686-support/)）

Arch Linux 使用 [Pacman](../../01_安装与包管理/包管理器/Pacman.md) [包管理系统](https://en.wikipedia.org/wiki/Package_manager)实现系统和软件包的滚动更新。并且 Arch Linux 还提供一个类似 ports 的包构建系统（[Arch Build System](Arch%20构建系统.md)），通过它可以轻松从源码构建软件包然后安装，并用一个命令完成同步。您甚至可以用一个命令重新构建整个系统。Arch 还提供 [Arch 用户仓库](Arch%20用户软件仓库.md)，它包含了成千上万个由用户自行维护的 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 脚本，配合 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 工具，从编译到打包一气呵成。此外用户还能轻松构建和维护属于自己的软件仓库。

## 历史沿革

这些年来，Arch 社区不断成长，与此同时也收到了大量的[关注和评论](Arch%20Linux%20新闻报道.md)。

除[少数特例](https://lists.archlinux.org/archives/list/arch-dev-public@lists.archlinux.org/thread/RIZSKIBDSLY4S5J2E2STNP5DH4XZGJMR)以外，Arch Linux 的开发者大部分是不领工资的志愿者与爱好者，目前也并没有通过 Arch 商业变现的计划。关于Arch Linux 开发的详细历史可以浏览 [Wayback Machine 的 Arch 部分](https://web.archive.org/web/*/archlinux.org)和 [Arch Linux 新闻存档](https://archlinux.org/news/)及[Arch Linux CN 首页上的中文版新闻列表](https://www.archlinuxcn.org/)。

### 早期

加拿大程序员、吉他手 Judd Vinet 从 2001 年早期开始开发 Arch Linux ，并于 2002 年 3 月 11 日正式发行 0.1 版。关于这段历史，他当初是受到 [Slackware](http://www.slackware.com/)、[BSD](https://en.wikipedia.org/wiki/Berkeley_Software_Distribution)、[PLD Linux](https://www.pld-linux.org/) 和 [CRUX](https://crux.nu/) 之简洁原则的启发，但在当时苦于这些发行版普遍缺少软件包管理工具，所以 Vinet编写了 [Pacman](../../01_安装与包管理/包管理器/Pacman.md) 包管理器，可以自动处理软件包的依赖解析、安装、删除和更新，后来他进一步建立了这个发行版。

### 中期

[这个图表](https://wiki.archlinux.org/images/6/64/Archstats2002-2011.svg)见证了Arch Linux 社区的稳步扩大。而且从早期开始，Arch 就树立起了[**开放、友好和社区互助**的形象](https://www.osnews.com/story/4827)。

### ArchWiki 的诞生

2005 年 7 月 8 日，由 MediaWiki 搭建的 ArchWiki [开始运行](https://wiki.archlinuxcn.org/wzh/Project:%E5%85%B3%E4%BA%8E)。

### A. Griffin 时代

2007 年下半年，Judd Vinet 退出了 Arch Linux 的开发，并[把领导权交给美国程序员 Aaron Griffin](https://bbs.archlinux.org/viewtopic.php?id=38024)，也就是 Phrakture，他一直到现在都是 Arch Linux 开发者。

### Arch 安装脚本

在 2012 年 7 月的 Arch Linux 安装介质中[弃用了](https://www.archlinuxcn.org/install-media-20120715-released/)基于菜单的*Arch 安装框架*（AIF），改为使用*Arch 安装脚本*（[arch-install-scripts](https://archlinux.org/packages/?name=arch-install-scripts)包）。

### Systemd 时代

2012 到 2013 年期间， Arch 用 Systemd 替换了 System V init ：

### 抛弃 i686 支持

鉴于在开发者和社区中对 i686 架构的使用逐渐式微，[Arch Linux 对 i686 的支持已经于2017年11月底被抛弃](https://www.archlinuxcn.org/the-end-of-i686-support/)。

### 选举项目的领导角色

2020 年初，Arch 开始启用新的流程选择未来的领袖，详情记载在 [DeveloperWiki:Project Leader](https://wiki.archlinux.org/title/DeveloperWiki:Project_Leader)。

Aaron Griffin 决定不再担任项目领导，因此 Arch 通过选举方式正式确认 Levente Polyak 为新的领导，结果记录在[这里](https://www.archlinuxcn.org/the-future-of-the-arch-linux-project-leader/)。

### GitLab 时代

[2023 年 5 月](https://www.archlinuxcn.org/git-migration-completed/)，Arch Linux 将其打包相关基础设施迁移至自建的 [GitLab 实例](https://gitlab.archlinux.org/)。除了内部更改和创新外，这也导致testing 仓库拆分为 core-testing 和 extra-testing， staging 仓库拆分为 core-staging 和 extra-staging，最终 community 合并至 extra。更多相关内容您可以到[这里](https://about.gitlab.com/blog/2023/09/11/migrating-arch-linux-packaging-infrastructure-gitlab/)阅读。

6个月后，即 [2023 年 11 月](https://lists.archlinux.org/hyperkitty/list/arch-dev-public@lists.archlinux.org/thread/WYXDTJ3TR2DWRQCDZK44BQDH67IDVGTS/)，旧的错误跟踪器（Flyspray）迁移至 GitLab，其协作功能（问题和合并请求）向公众开放。因为存档问题，原处保留一个旧错误跟踪器的静态副本，以保证指向其原有资源的链接（例如随机选择的[FS#56716](https://bugs.archlinux.org/task/56716)）仍然有效。

## 1.2　Arch Linux 愚人节笑话

*难度：进阶 ｜ 篇幅：15.7 KB ｜ 所属：关于 Arch*

将要讲到：2014–2020、2013：Pacman基于systemd、2012、2011：Canterbury、2010：愚人节挑战、2009：结束i686支持

每年4月1日，Arch Linux网站都会在头版刊登一则[愚人节](https://en.wikipedia.org/wiki/April_Fools%27_Day)笑话。本文收集了这些笑话，供本wiki的读者阅读。

## 2014–2020

这几年的愚人节笑话比较微妙，尚未有人发现，但我们可以证明的是，这些年份确实有发布愚人节笑话。有关寻找这些笑话尝试（虽然都失败了）的英文论坛帖子见下：[2014](https://bbs.archlinux.org/viewtopic.php?id=179466)、[2016](https://bbs.archlinux.org/viewtopic.php?id=210784)、[2018](https://bbs.archlinux.org/viewtopic.php?id=235878)、 [2020](https://bbs.archlinux.org/viewtopic.php?pid=1895357)。

## 2013：Pacman基于systemd

**发布于：** 2013 / **永久链接：** [Pacman 4.1 released](http://allanmcrae.com/2013/04/pacman-4-1-released/)

该版本的主要特性是PackageManager和systemd之间的紧密集成。在对如何最好地在滚动发布系统上执行更新进行了大量讨论之后，我们意识到，在执行更新时，尽可能少地运行其他进程是至关重要的。此外，更新的安全方面意味着必须尽快提供更新。我们认为实现这一点的最佳方法是在关机时执行更新。这是通过一个新的守护进程pacmand实现的，它在后台监视和下载更新。当发现更新时，它会安排重新启动系统（因此需要集成systemd）。此时，重新启动的时间不可配置，但会弹出一个计时器，允许您将其延迟预设的时间。配置可能会添加到pacman-4.2中，届时pacmanctl将可供一般使用。在该版本发布之前，Arch Linux将通过在其\[testing\]仓库中执行所有更新，并仅在一周内的某一时间推送更新，从而将影响降至最低。决定后将发布新闻。

当然，这些将使systemd成为pacman的硬依赖。我们认为这是可以接受的，因为Arch Linux已经正式改用systemd。由于此版本未在没有systemd的系统上进行测试（不太可能能够工作），因此如果Arch用户或其他使用pacman的发行版希望继续使用pacman作为其包管理器，则需要切换到systemd。在pacman-4.2中，与系统的集成将变得更加紧密，我们计划使用即将推出的kdbus消息传递接口——通过libsystemd总线——来允许其他程序与pacman交互，从而使替代前端的开发更加容易。

## 2012

看起来2012年没有愚人节笑话……

## 2011：Canterbury

**发布于：** 2011 / **永久链接：** [Arch news archive #519](https://archlinux.org/news/519/)

### 历史

在[FOSDEM 2011 发行版合作活动](https://web.archive.org/web/20110404015153/http://www.fosdem.org/2011/schedule/event/distro_collaboration)上，Arch Linux 和 Debian 的主要开发者讨论了一个想法：结合各自项目最突出的优点，共同开发出一个新的、更优秀的发行版。这个想法其实是最初在先前的一次openSUSE会议中提出的，他们相信协同努力，共同开发一个新发行版还是有可能性的。

一周后的2月16日，[Linux标准规范（Linux Standard Base）](https://www.linuxfoundation.org/collaborate/workgroups/lsb)的版本[4.1](https://wiki.linuxfoundation.org/lsb/lsb-41) 发布了。随着新规范的发布，“新的统一发行版”再度成为热门话题。这次Gentoo和Grml也参与进来了。

Dieter，双ISO/USB-IMG安装文件的策划人员，已经开始着手开始新安装程序的讨论，确定将负责进一步合并更难的 p2arts，例如其基础设施和系统工具。

### 目标

我们的目标是真正在一起努力，并能够共同对抗专有操作系统，展示自由软件社区实际上真正能够为一个共同的目标而合作，而不是创造更多的分歧。

### 特性

Canterbury发行版为用户着想，致力于将各个Linux发行版的精华集于一身，成为操作系统界新的转机：

- 像Arch一样简单——简洁且保持前沿。
- 像Debian一样稳定——高度可靠。
- 像Gentoo一样可定制——用户能得到真正想要的。
- 像Grml一样便携——随时可用。
- 像openSUSE一样开放——广泛且欢迎所有人。

## 2010：愚人节挑战

**发布于：** 2010 / **永久链接：** [Arch news archive #492](https://archlinux.org/news/492/)

又是一年愚人节，但今年愚人节我们不会“那么”愚人。“不那么”，甚至大部分人都注意不到今年的愚人节笑话，但它确实存在。在本帖发布你对本次愚人节真相的猜测吧！第一个完全解开今年愚人节笑话的人将会得到一个上网本（当初是捐给我们的）作为解开谜题的奖励。

### 关于愚人节挑战的笑话

**Arch 论坛：** [April Fools Challenge](https://bbs.archlinux.org/viewtopic.php?id=94255)

第1页：“当然是个递归笑话。”

第15页：“四月份都快过一半了，怎么这帖子还有热度呢？”

第16页：“我要再发一个‘继续往下翻，这里啥都没有’的回复，这样人们就会真的觉得有点东西在……”

第16页：“？？？啥都没有？？？？？可是有386个回复和49275次浏览欸！……我是被困在黑客帝国里面了？”

## 2009：结束i686支持

**发布于：** 2009 / **永久链接：** [Arch news archive #440](https://archlinux.org/news/440/)

近期，开发者们一直在讨论向i686 port添加一点额外的优化以改进多媒体支持，但这将导致对旧设备的兼容性下降。就跟你们部分人已经知道的一样（，谷歌机翻 ），相关讨论的最终结果是，开发者决定只关注x86_64 port。开发者的总体意见是，现阶段x86_64 port已经足够完整，这让只关注x86_64 port成为可能，并且这符合 Arch 支持当前时代硬件的理念。x86_64架构2002年就已经可用了（i686是1995年的），我们相信大部分i686用户其实都应该有兼容x86_64的硬件了。

有关正式结束i686支持的时间线还没有确定，但我们得做出这样一个官方公告，毕竟这个决定已经泄露给Arch Linux-BR社区了。然而，在不久的将来可能确实不再会为 i686 构建主要更新（GNOME、KDE、Xorg 等）。用户仍将能通过ABS构建i686软件包。由于大多数特定于体系结构的补丁都是针对 x86_64 的，因此迁移过程应该是相对轻松的。

### 关于结束i686支持笑话的实际情况

2009年4月1日，该则关于结束i686架构支持的公告发布于frontpage以及Arch-Dev-Public邮件列表。这一公告在论坛、邮件列表和IRC上引发了一系列激烈的争论。本次争论导致的最大热点事件是 Aaron 威胁要离开 Arch。Similar to previous years, various phrases on the forums and other Arch sites were filtered to make life more entertaining。

### 事后报告

**发布于：** 2009 / **永久链接：** [Arch news archive #441](https://archlinux.org/news/441/)

各位Arch Linux用户你好，我们很高兴通知您，Arch Linux对i686的支持将不会结束。有关内容全部都是愚人节笑话的一部分，所有的开发者和论坛管理员在其中都扮演了很大的角色。

值得注意的是，这个愚人节笑话确实做了点好事。这个笑话促使部分用户发现他们用的是64位处理器，但却在使用32位操作系统，于是便迁移到了Arch Linux的64位版本。我们鼓励这些已经迁移的用户继续使用64位版本，以继续为该架构做出贡献，促进广大软件厂商跟进。

一个最终接受64位社区需求的典型例子是Adobe公司。他们近期为Flash插件添加了64位Linux支持，对此我们表示感谢。

对于此笑话造成的任何不便，我们深表歉意。但我们又如何能抗拒4月1日的恶作剧呢？

### 真正结束支持

2017年1月25日，官方正式[公告](https://archlinux.org/news/phasing-out-i686-support/)，由于i686架构在开发人员和用户群中的实际使用比例下降，将逐步取消对i686架构的支持。

## 2008：Arch将采用德语作为官方语言

**发布于：** 2008 / **永久链接：** n/a

有关使用英语的Arch Linux用户的重要通知

在过去的两个月里，在私密的开发人员邮件列表上，开发者进行了热烈的讨论。而我们得出的结论是，将Arch定位为主要语言为英语的发行版对我们最大的用户群体是一种损害。因此，我们投票决定是否将 Arch Linux 的官方主要语言更改为德语，且更改为德语这一选项胜出。

请在过渡期间耐心等待——我们正在与 archlinux.de 密切合作以促进过渡，届时我们计划合并网站。不过我们很了解Arch这一伟大社区，我们确信将很快出现一个社区项目填补我们完全停止英语站点后留下的小空白。

另外，请注意，我们现在将使用“Arch”的德语发音。别担心，您会习惯的——我们 90% 的开发人员已经习惯了呢！

Happy Computenpeepers, 管理团队

### 关于Arch将采用德语作为官方语言这一笑话的实际情况

2008年4月1日，大部分Arch Linux网站（包括论坛）都被翻译为德语。上述通知也在大部分网站上出现，且论坛重写了用户发表的内容，将所有指向ArchWiki条目的链接指向一个叫“Deutschland”的页面，类似于“joke”（笑话）之类的词被“genius idea”（绝妙主意）这样的词汇重写。比如“Arch Linux April Jokes Collection（愚人节笑话合集）”被改写成“Arch Linux Deutschland genius idea Collection（绝妙主意合集）”。

## 2007：Arch Ark

**发布于：** 2007 / **永久链接：** [Arch news archive #307](https://archlinux.org/news/307/)

我们要改名啦！

这五年来，我们一直被称作Arch Linux，也有人一直把我们和Ark Linux搞混，而现在，我们郑重提出这一问题的解决方案。前几个月我们一直在尝试同Ark Linux的人员达成一个让双方发行版都受益的解决方案。今天，我们很高兴地宣布，Arch Linux将会改名，之后我们将会改叫Ark Linux！

接下来的几周内 archlinux.org 域名仍将由我们管理，这期间你们将适应改名，但最终我们会将域名也交换过来。通过改名这一方式，我们确信人们再也不会在区分Arch Linux和Ark Linux时出现问题。

Ark Linux万岁！

### Ark Linux

Ark Linux曾经是一个真实存在的Linux发行版（已经停止开发了 ）。上方的笑话对那些因不小心打错“www.arklinux.org”成“www.archlinux.org”而开始使用Arch Linux的用户来说应该是更加有趣。“Arch Linux”这一名字的“Arch”被认为应当念作 /ɑːt͡ʃ/（/a-ch/，与larch、starch等英文单词押尾韵，与单词“archer”中“arch”的读音相同）。但是好像就实际到底怎么发音这一问题上并没有真正的共识。

相同的笑话同样发表在了Ark Linux社区，让这则公告乍看上去更加真实（至少第一眼是这样。很遗憾的是，Ark Linux新闻似乎没有人存档）。

## 2006：Judd前往Google工作

**发布于：** 2006 / **永久链接：** [Arch news archive #214](https://archlinux.org/news/214/)

当前变化

我在想向大家分享一些近期流言的实际情况。

Judd正式宣布接受了来自Google的工作，因此，将不再能继续参与Arch Linux的相关工作。

项目领头人已交接给十分有能力的Jason Chu（Xentac）。Jason负责的首要任务——在我们朝着0.7.1.1版本（代号Pony）的发布不断努力的同时——是创建\[stable\]软件仓库，里面将会放置一些较旧的软件。这将成为之后工作的重点，与此同时还包括安全更新的旧版移植以及兼容性修复。因此，现在的仓库将开始缺少一点内容，但最终这些会随着我们向更“稳定”和“注重生产级质量”的发布机制而被及时移除。

几周后你们就能看到0.7.1.1啦。

此致， Aaron

### Judd's 对Arch项目的最终告别

Arch Linux的创始人Judd Vinet实际上已于2007年10月1日离开该项目，成为“Arch的头号啦啦队员”。  自此，Aaron Griffin将是项目的领导者。

### Arch-Linux及其发布机制

Arch Linux当然从未想过“稳定”和“生产级质量”的发布机制。我们是[滚动更新](https://en.wikipedia.org/wiki/Rolling_release)的系统，系统始终保持最新，没有固定的发布时间表。

## 2005：Arch与袋熊（Wombat）的相关争议

**发布于：** 2005 / **永久链接：** [Arch news archive #147](https://archlinux.org/news/147/)

CETW问题

我个人怀着极大的悲伤写下这条消息。

当我们首次发布 Arch Linux 0.7（代号Wombat）时，袋熊伦理治疗中心 (CETW) 联系了我们。 CETW不知为何认为我们发布一个代号为Wombat，袋熊，的“开源项目”是在伤害全世界的袋熊。虽然我们试图说服他们 Arch Linux 是一个 Linux 发行版，并且在我们的发行版制作过程中没有袋熊受到伤害，但他们没有接受。

所以他们威胁要起诉我们，除非我们改变对袋熊的伤害。

经过深思熟虑，我们发现我们实际上没有足够的钱在法庭上与他们抗争。因而，我们选择了庭外和解。我们解决方案的一部分是停止Arch Linux的开发。

因此，由于命名选择不当，Arch Linux将不复存在。在此我代表所有开发人员感谢社区的支持和贡献。

### Wombat（袋熊）是个啥？

引自[维基百科的“袋熊”页面](https://zh.wikipedia.org/wiki/%E8%A2%8B%E7%86%8A)：“袋熊（英语：Wombat，学名：Phascolomidae），是澳洲的有袋类动物。它们的脚短及肌肉发达，身长约有1米，尾巴亦很短。它们生活于澳洲东南部及塔斯马尼亚州的森林、山地及石楠地。袋熊的Wombat这个名字来自Eora土著社区，他们是悉尼地区的原住民。”

袋熊显然不会雇佣律师来骚扰Linux发行版。[了解袋熊与人类的关系](https://zh.wikipedia.org/wiki/%E8%A2%8B%E7%86%8A%E7%A7%91#.E4.B8.8E.E4.BA.BA.E7.B1.BB.E7.9A.84.E5.85.B3.E7.B3.BB)。

## 2004：GNOME 2.6

**发布于：** 2004 / **永久链接：** [Arch news archive #58](https://archlinux.org/news/58/)

GNOME 2.6 发布了！

正如标题所说，gnome 2.6 已经位于软件源中。没有太多需要注意的, Arjan 和 JGC 在测试阶段很好地消除了潜在问题，所以安装后体验应该是完美的。

升级：pacman -Syu 安装：pacman -S gnome

一些与GTK 2.x相关的软件包也进行了升级，以满足GNOME 2.6的要求。

### GNOME版本历史

在上述“新闻”发布前，GNOME项目在2002年才发布GNOME 2.0，而2004年4月1日前最新的发布版本也才GNOME 2.2而已。

## 1.3　常见问题

*难度：进阶 ｜ 篇幅：17.8 KB ｜ 所属：关于 Arch*

将要讲到：一般问题、安装、其他、软件包管理、关于64-bit、中文社区仓库

## 一般问题

### Q) Arch Linux 是什么？

**A)** 请阅读 [Arch Linux](Arch%20Linux.md)。

### Q) 为什么我不想用 Arch Linux ？

**A)** 您或许不想使用 Arch Linux，如果您：

- 没有经验、能力、时间、想法和愿望去打理这样一个高度可定制的 GNU/Linux 发行版。
- 需要非 x86_64 平台支持。
- 你是狂热的自由软件爱好者，只希望发行版提供 GNU 定义的自由软件。
- 你认为好的操作系统应当是已经配置好的：安装介质应默认包含一套完整的应用软件包、图形化桌面环境——达到“开箱即用”。
- 你不需要或不想使用滚动升级的发行版。
- 你对目前使用的操作系统感到满意。

### Q) 为什么我要用 Arch Linux ？

**A)** 因为 [Arch 是最好的！](Arch%20是最好的！.md)

### Q) Arch Linux 支持什么架构?

**A)** Arch Linux 现在只支持 [x86_64](https://en.wikipedia.org/wiki/x86_64)(也被称为 amd64 ) 架构，[对 i686 架构的支持已于 2017 年 11 月结束](https://archlinux.org/news/the-end-of-i686-support/)。

**非官方支持**的移植版本有： [i686](https://archlinux32.org/) 和 [ARM](https://en.wikipedia.org/wiki/ARM_architecture) 。

### Q) Arch Linux 遵循 [FHS](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html) 吗?

**A)** Arch Linux 遵循适用于 [systemd](../../01_安装与包管理/初始化/Systemd.md) 服务管理器的文件系统架构，[file-hierarchy(7)](https://man.archlinux.org/man/file-hierarchy.7) 包含了每个文件夹的解释和设计。 `/bin`, `/sbin` 和 `/usr/sbin` 现在是 `/usr/bin` 的符号链接，`/lib` (以及 `/lib64`) 是 `/usr/lib` 的符号链接。

### Q) 我是一个彻头彻尾的Linux新手，我应该用 Arch Linux 吗？

**A)** 如果您是新手，要使用 Arch Linux 就必须愿意花时间学习新系统，并接受 Arch 是一个需要有 DIY 精神及能力的系统，每个用户都是自己系统的组建者。

在您开始问任何问题之前，建议您自己先通过 Google 、Wiki 类网站、ArchWiki 或者 Linux 相关论坛进行搜索。我们为您创建了这些资源并让您可以随时访问，有成千上万的**志愿者与爱好者**为您提供大量的信息资源。

推荐阅读: [Arch术语#RTFM](Arch%20术语.md#RTFM) 。

### Q) Arch Linux 的安装和配置十分麻烦，社区的人总是让我去读那些死长死长的手册。

**A)** Arch Linux 是针对一部分特定用户群体设计的，如果您不在其中，也许 Arch Linux 并不适合您。

### Q) Arch Linux 是为服务器、桌面还是工作站设计的？

**A)** Arch Linux 并不是针对特定应用设计的，而是针对有特定需求的用户群体设计的。Arch Linux 适合那些喜欢并有动手能力的用户，以及希望打造属于自己的独一无二的系统的用户。因此，对于这些用户，Arch Linux 能胜任各种工作。也有许多人把 Arch Linux 同时当做桌面、服务器和工作站系统。此外，archlinux.org 网站也在 Arch Linux 上运行。

### Q) 我太喜欢 Arch Linux 了，希望开发团队实现一个新功能。

**A)** 您可以[参与进来](参与贡献.md)，分享代码和解决方案。如果广大用户和开发者确实认同这个功能，您的想法有机会被添加到新版本系统中。Arch 社区依靠大众贡献分享的代码和工具蓬勃发展。

### Q) 一般什么时候发布新版本？

**A)** Arch Linux 的发布版本只是安装和修复环境，包含[base](https://archlinux.org/packages/?name=base)包 [元软件包](../../01_安装与包管理/包管理/元软件包和软件包组.md)和一些[辅助软件](https://gitlab.archlinux.org/archlinux/archiso/-/blob/master/configs/releng/packages.x86_64)，一般每月上旬发布一次。

Arch Linux 使用滚动升级模式，只需执行一条命令，便可升级系统到最新版本。基于上述原因，发布新版本对 Arch Linux 来说并不重要，因为滚动升级的机制会使系统永葆青春。使用`pacman -Syu`命令升级系统，无需重装系统即可更新到最新版本。同样，Arch Linux 的新发行版本并不会提供什么新的功能，新的功能通过`pacman -Syu`命令即可立即拥有。

### Q) Arch Linux 是否稳定？会不会经常坏掉？

**A)** 每个 Arch Linux 的用户应当对自己执行滚动升级的系统稳定性负责。由用户自行决定何时升级、修改配置。Arch Linux 与其他发行版的一个不同之处在于，Arch Linux 是真正的“DIY”发行版。一味抱怨系统损坏是没有意义的，毕竟上游的改动不是 Arch 开发者的责任。

如果您想要使系统变得更加稳定，请参阅：[系统维护](../../02_系统管理与内核/系统管理/系统维护.md)，以提高系统稳定性。

### Q) Arch Linux 需要更多曝光（比如广告）。

**A)** Arch Linux 已经得到了应有的新闻媒体曝光机会。Arch Linux 的目标不是为了一步登天。我们的目标只是使它变得更好。让它自然成长吧，试图强迫它成长得更快会造成诸多问题。

### Q) Arch Linux 需要更多开发者。

**A)** 或许吧。欢迎您来帮助我们！可以来逛逛[论坛](https://bbs.archlinux.org)，[IRC channels](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md) 以及[邮件列表](https://lists.archlinux.org/mailman3/lists/)，看看您有什么需要做的。或者，看看论坛的 Community Contributions 子版面吧。

## 安装

### Q) 我感觉 Arch Linux 需要安装程序，比如带有图形界面的那种。

**A)** 从 [2021-04-01](https://archlinux.org/news/installation-medium-with-installer/) 起，Arch Linux 有了更好的安装程序。参见 [archinstall](../../01_安装与包管理/安装过程/Archinstall.md)。

### Q) 我安装了 Arch Linux，现在眼前只有一个命令行登录界面，我该怎么办？

**A)** 建议您阅读[通用建议](../建议阅读/建议阅读.md)。

### Q) 哪个桌面环境或窗口管理器比较好？

**A)** 有很多不同的选择，从中选择最适合您的即可。参见：[桌面环境](../../05_桌面与图形/桌面环境/桌面环境.md)。

### Q) Arch Linux 比起其他“小型”发行版，有何独特之处？

**A)** 建议您阅读 [Arch与其他发行版的比较](Arch%20与其他发行版的比较.md)。

## 其他

### Q) 什么是 AUR ？

**A)** 建议您参见：[Arch用户软件仓库#FAQ](Arch%20用户软件仓库.md#FAQ)。

### Q) 为什么我感觉 Arch Linux 下网速比其他系统慢？

**A)** 您的网络是否正确配置？如有疑问，您可以读一读[网络配置](../../03_网络与服务/网络/网络配置.md)。

### Q) 为什么 Arch Linux 把我的所有内存用光了？

**A)** 实际上，内存不用就浪费了。

很多新用户发现，Linux 内核对物理内存的处理机制和他们所以为的有所不同。由于访问物理内存比访问磁盘要快得多，内核会把最近访问的数据缓存到物理内存中。物理内存中缓存数据会在空闲物理内存耗尽时被释放。

而造成新用户的困惑的罪魁祸首，恐怕是`free`命令：

```
$ free -h
```

```
              total        used        free      shared  buff/cache   available
Mem:          2.8Gi       1.1Gi       283Mi       224Mi       1.4Gi       1.2Gi
Swap:         3.0Gi       881Mi       2.1Gi
```

要注意 "free" 和 "available" 的区别，即空闲和可用的区别。在上面的例子中, 电脑所有的 2.8G 内存几乎全部占用。"free"中只剩下 283 MiB 是未使用的。但是在总量 2.8G 内存中的 1.4G 是 "buff/cache" 缓存。实际上还剩下 1.2G 是可使用的（不考虑交换空间）。查看 [free(1)](https://man.archlinux.org/man/free.1) 了解更多。

这有什么用？当然是更好的性能！

如果您对此十分困惑，请参见[此文](https://www.linuxjournal.com/article/2770)。另外还有一篇文章有详细的解释：[https://www.linuxatemyram.com/](https://www.linuxatemyram.com/)。

### Q) 我的磁盘空间被什么东西占用了？

**A)** 问题的答案因系统而异。有一些[应用列表](../../06_软件与应用/应用程序/应用程序列表.md)或许能告诉您答案。

## 软件包管理

详情请参考 [pacman](../../01_安装与包管理/包管理器/Pacman.md)、[pacman/提示和技巧](../../01_安装与包管理/包管理器/Pacman_提示和技巧.md)和[官方仓库](官方仓库.md)。

### Q) 我发现了某个软件包的错误，该怎么办？

**A)** 首先您需要搞清楚 Arch 队伍是否能够解决这个错误。有时并非如此（Firefox 崩溃也许是 Mozilla 开发团队的错误）——这便是所谓的***上游错误***。如果您确信是 Arch 的问题，您可以采取以下步骤：

1.  在论坛中搜索有关信息。看看是否有人已经注意到这个情况。
2.  在 [Arch Linux Gitlab 的 Bugtracker](https://gitlab.archlinux.org/groups/archlinux/packaging/packages/-/issues) 提交具有详细信息的 [Bug 报告](../Arch%20开发/漏洞报告准则.md)。
3.  也可在论坛中发篇帖子，阐述问题细节并说明您已经报告过这个情况。这会避免其他人重复报告同样的问题。

### Q) Arch Linux 软件包需要独特的后缀名。“.pkg.tar.zst”太长了，而且令人困惑

**A)** 在 Arch 邮件列表曾有过讨论。一些用户提议使用 .pac 作为文件扩展名。但就目前情况来说，我们没有计划改变软件包的扩展名。

正如 Tobias Kieslich（一名 Arch 开发者）所说： “一个软件包就是一个 **\[压缩包\]** ！它可以被很多软件打开、研究和操作。此外，这种 mime-type 也可以被大多数软件自动地正确识别。”

### Q) Pacman应该提供函数库接口，这样其他软件就可容易地获得软件包信息。

**A)** [libalpm(3)](https://man.archlinux.org/man/libalpm.3)（Arch Linux Package Management，Arch Linux 软件包管理）是 pacman 的后端，这个库大大方便了交互式前端的编写（例如图形化前端）。

### Q) Pacman需要某某功能！

**A)** 如果您有新想法，可以在 [pacman-dev](https://lists.archlinux.org/mailman3/lists/pacman-dev.lists.archlinux.org/) 邮件列表进行讨论，或者查看[官方论坛](https://bbs.archlinux.org/)看看其他人有没有类似的想法。

也欢迎您自己在系统中实现新功能。也许您的代码或补丁不会被官方接受，但他人可能会用到。

### Q) 仓库和镜像的区别是什么？

**A)** 请您参考[软件仓库](../../01_安装与包管理/包管理器/Pacman.md#软件仓库)。

### Q) 我刚刚安装了一个软件包，怎么启动呢？

**A)** 如果您正在使用像[KDE](../../05_桌面与图形/KDE/KDE.md)和[GNOME](../../05_桌面与图形/GNOME/GNOME.md)这样的软件包，它们的程序一般会自动出现在菜单中。如果是命令行程序，则可使用如下命令查看可执行文件名称和位置：

```
$ pacman -Qlq package_name | grep /usr/bin/
```

### Q) 在官方仓库中，为什么只为每个共享链接库提供一个版本？

**A)** 某些发行版，比如 Debian，会为一个共享库提供多个版本，比如：`libfoo1`，`libfoo2`，`libfoo3`等等。这样，就可以同时安装那些依赖不同版本 libfoo 的程序。

Arch 官方只对最新版的程序提供支持。只要摆脱了过时的软件包，包的维护人员就可以全力维护新版本的程序。而只要上游提供新版本的共享库，我们就会重新编译相关程序，更新仓库。

### Q) 执行`pacman -Syu`时，终端显示某个共享库需要升级，但依赖它的程序没有升级，我该怎么做呢？

**A)** 这种情况理应不会发生。假如官方源一个名为`foobaz`的程序，成功地使用新版本共享库`libbaz`编译成功，那么它应该和`libbaz`一同更新。如果编译不成功，由于旧的`foobaz`存在依赖关系，例如：

```
libbaz=1.5
```

所以当`libbaz`升级时，它会被移除。

如果`foobaz`是您自己编译的或是从 AUR 获取的，当`libbaz`升级时您应当重新编译它。如果编译失败，还请您通知`foobaz`开发者。

### Q) 会不会出现一种情况就是，仓库中的内核主版本更新了，而某些驱动包没有一同升级的情况？

**A)** 这个绝对不可能。当内核主版本升级时，所有支持的驱动一定会一同更新。

然而，如果您安装有非内核支持的驱动程序包，比如`catalyst`，内核升级有可能导致系统挂机。您应当在内核升级时自行重编译它们。

### Q) 升级前该做什么?

**A)** 在升级之前，记得访问 [Arch Linux 新闻列表](https://archlinux.org/)、[通告邮件列表](https://lists.archlinux.org/mailman3/lists/arch-announce.lists.archlinux.org/) (有时候需要看一看[论坛](https://forum.archlinuxcn.org/)和[各个邮件列表](https://lists.archlinux.org/mailman3/lists/)）。需要特别注意的事项都会列在那里。另见[系统维护#更新系统](../../02_系统管理与内核/系统管理/系统维护.md#更新系统)。

### Q) 我知道某个包已经更新，但是 pacman 并没有发现更新

**A)** 可能是 pacman 镜像还没有同步，最好是等等看或者是换一个镜像服务器。[MirrorStatus](https://archlinux.org/mirrors/status/) 可以帮助您区分哪些镜像服务器是最新的。

### Q) 某上游项目已经发布了一个新版本，要多长时间 Arch Linux 才会更新到新版本？

**A)** 软件包将会在准备好之后发布，发布的时间取决于上游的变动和打包人员的精力。

如果发布的是小版本，可能只需要几个小时。如果是大版本更新，则可能需要数周时间。

此外，有些软件包会在 [testing 仓库](官方仓库.md#testing_仓库)中停留一段时间进行小范围测试。[软件包维护者](Arch%20术语.md#软件包维护者)会尽快将稳定的版本发布到仓库中。如果您发现官方软件仓库中的软件过旧，可以在[软件包页面](https://archlinux.org/packages/)进行标记。

### Q) 如果需要一个老的软件库，可以 symlink 到新版本吗？

**A)** 运气好的话，可以用来救急。但是这并不是一个好办法：

- 软件库不会无缘无故更新版本 - API/ABI 可能发生变动，这些变动是否影响到使用只能听天由命。
- 软件包管理器不会记录软链接，缺乏经验的新手执行这种操作，可能会引起难以恢复的错误。
- 未被记录的老版本文件很快就会被遗忘，且安全漏洞不会被修复。

最好是使用或编写[兼容软件包](https://aur.archlinux.org/packages/?SeB=n&K=compat)，用其提供需要的版本。

## 关于64-bit

### Q) 我如何确定我的处理器（CPU）是否支持 x86_64?

**A)** 运行下面的命令：

```
$ less /proc/cpuinfo
```

查找 `flags` 条目。如果你看见 `lm` 标志，那么您的处理器是支持 x86_64 的。

Windows 系统用户可以使用免费软件 [CPU-Z](https://www.cpuid.com/cpuz.php) 或商业软件 [AIDA64 中的 CPUID 工具](http://aida64.com.cn/)查看 CPU 的信息，这些软件可以通过识别 CPU 的指令集来确定处理器是否支持64位。

如果您使用带有 AMD 的 "x86-64" 指令集或者英特尔的 "EM64T" 指令集的 CPU，则可以使用 x86_64 的发行版和二进制包。

### Q) 为什么现在应该使用64位系统?

**A)** 在大多数情况下，64位系统会更快，而且更安全。更安全是因为它拥有 [Address space layout randomization (ASLR)](https://en.wikipedia.org/wiki/Address_space_layout_randomization) 、 [Position-independent code (PIC)](https://en.wikipedia.org/wiki/Position-independent_code) 特性，以及 [NX Bit](https://en.wikipedia.org/wiki/NX_Bit) (它在i686内核中，由于禁用了[物理地址扩展 (PAE)](https://en.wikipedia.org/wiki/Physical_Address_Extension)而无法使用)。

如果你的计算机在运行 4.0 GiB 或者更多的内存，那应该使用64位系统，因为多余的内存是无法被32位系统分配的。

现如今，程序员也更加倾向于忽视32位系统，因为新的 x86 CPU 通常都支持64位扩展。

还有许多其他的理由让我们抛弃32位系统。但是在内核、用户空间和单独的程序中，我们没有办法列出所有的64位系统比32位系统做得更好的地方。

## 中文社区仓库

**主条目：** [Arch Linux 中文社区仓库](../../01_安装与包管理/非官方用户仓库/Arch%20Linux%20中文社区仓库.md)

### Q) \[archlinuxcn\] 仓库和 AUR 是什么关系？

**A)** 没有特别的关系。部分软件包的 PKGBUILD 打包脚本也同时存在于 AUR 而已。然而并不是所有 \[archlinuxcn\] 提供的软件包都存在于 AUR，也不是所有同时存在于 \[archlinuxcn\] 和 AUR 的软件包就一定会使用相同的打包脚本。我们不是 AUR 的二进制版。

在使用 \[archlinuxcn\] 提供的软件包时遇到问题，也请您报告到我们的 GitHub [issues](https://github.com/archlinuxcn/repo/issues)。

### Q) 既然有 AUR，我为什么会想要使用 \[archlinuxcn\] 仓库？

**A)** \[archlinuxcn\] 仓库提供许多额外的、已经打包好的软件。相比于 AUR，您不必就地从世界各地下载软件源码然后花费时间编译，而是可以直接安装已经编译好的软件包。

如果您是中国大陆用户，您可以利用我们位于中国大陆境内的多个镜像源，来快速下载到软件，而不必处理访问国际网络可能会遇到的问题。

### Q) 我是 Manjaro （或其它 Arch Linux 衍生发行版）用户，我可以使用 \[archlinuxcn\] 仓库吗？

**A)** 我们不建议这样做。\[archlinuxcn\] 仓库只支持 Arch Linux，在衍生发行版上使用可能会出现问题。

如果您在非 Arch Linux 系统上使用 \[archlinuxcn\] 仓库遇到了问题，还请不要将问题报告给我们。

## 1.4　Arch Linux 新闻报道

*难度：高级 ｜ 篇幅：20.3 KB ｜ 所属：关于 Arch*

将要讲到：2023、2022、2021、2020、2019、2018

> **注意：**此页面上的内容可能不是最新的，请首先参阅 ArchWiki 上的 [Arch Linux press coverage](https://wiki.archlinux.org/title/Arch_Linux_press_coverage) 。

参见 [Wikipedia:Arch Linux](https://en.wikipedia.org/wiki/Arch_Linux) 与 [Arch Linux on DistroWatch](https://distrowatch.com/table.php?distribution=arch)。

## 2023

- [Migrating Arch Linux's packaging infrastructure to GitLab](https://about.gitlab.com/blog/2023/09/11/migrating-arch-linux-packaging-infrastructure-gitlab/) Levente Polyak for GitLab open source blog - 2023-09-11
- [The Numbers Don’t Lie: Arch Linux Reigns Supreme on Reddit](https://linuxiac.com/most-popular-linux-distros-according-to-reddit/) Bobby Borisov - linuxiac.com 2023-01-02

## 2022

- [Arch Linux finally adds a new installer to ease a rather challenging process](https://www.techrepublic.com/article/arch-linux-finally-adds-new-installer-ease-rather-challenging-process/) Jack Wallen - techrepublic.com 2022-05-09
- [Arch Linux turns 20: Small, simple, great documentation](https://www.theregister.com/2022/03/15/arch_linux_20/) Liam Proven - theregister.com 2022-03-15
- [20 years of Arch Linux: Skilled system with a hobbyist popularity](https://news.in-25.com/business/71803.html)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2024-03-03 ⓘ\] translation of article by Martin Gerhard Loschwitz - heise.de 2022-03-11

## 2021

- [Arch Linux's Install Media Adds "Archinstall" For Quick/Easy Installations](https://www.phoronix.com/scan.php?page=news_item&px=Arch-Linux-Does-Archinstall) Michael Larabel - Phoronix.com 2021-04-04
- [Arch Linux Developers Discuss Idea Of Providing An x86-64-v3 Port](https://www.phoronix.com/scan.php?page=news_item&px=Arch-Linux-x86-64-v3-Port-RFC) Michael Larabel - Phoronix.com 2021-03-16
- [Arch Linux Looking To Employ LTO By Default, Possibly Raise x86-64 Requirements](https://www.phoronix.com/scan.php?page=news_item&px=Arch-Linux-LTO-Proposed) Michael Larabel - Phoronix.com 2021-03-09

## 2020

- [5 Reasons to Use Arch Linux](https://anchor.fm/thelinuxcast/episodes/5-Reasons-to-Use-Arch-Linux-eevei8) Matt Weber - anchor.fm/thelinuxcast 2020-06-03
- [Arch Linux Announces New Project Leader](https://www.phoronix.com/scan.php?page=news_item&px=Arch-Linux-New-Project-Leader) Michael Larabel - Phoronix.com 2020-02-04

## 2019

- [Arch Linux Held Their First Internal Conference Last Month In Berlin](https://www.phoronix.com/scan.php?page=news_item&px=Arch-Conf-Internal-2019) Michael Larabel - Phoronix.com 2019-11-17
- [Arch Linux OS Challenge: Install Arch 'The Easy Way' With These 2 Alternative Methods](https://www.forbes.com/sites/jasonevangelho/2019/06/10/arch-linux-os-challenge-2-alternatives-install-gui-script-easy/) Jason Evangelho - Forbes.com 2019-06-10
- [Introducing The Arch Linux Summer 2019 Community Challenge](https://www.forbes.com/sites/jasonevangelho/2019/06/03/introducing-the-arch-linux-summer-2019-os-community-challenge/) Jason Evangelho - Forbes.com 2019-06-09
- [BTW, I Installed Arch](https://chooselinux.show/10) (first 19 minutes of the podcast) Jason Evangelho and Joe Ressington - chooselinux.show 2019-05-30
- [How to Conquer Your Fear of Arch Linux](https://linuxinsider.com/story/how-to-conquer-your-fear-of-arch-linux-85983.html) Jonathan Terrasi - linuxinsider.com 2019-04-28

## 2018

- [10 Reasons to Install an Arch Linux-Based OS on Your PC](https://www.makeuseof.com/tag/reasons-install-arch-linux/) Bertel King, Jr. - makeuseof.com 2018-04-02

## 2017

- [Linux Action Show Episode 461](https://www.youtube.com/watch?v=dMbQoNz2GP8) (from 00:11:00) Chris Fisher - YouTube archive from the show originally at www.jupiterbroadcasting.com 2017-03-18
- [Arch Linux: The Simple Linux](https://www.linux.com/learn/intro-to-linux/2017/2/arch-linux-simple-linux/) Carla Schroder - linux.com 2017-02-02

## 2016

- [mintCast 250 – The Return of Arch](https://mintcast.org/mintcast-250-the-return-of-arch/) (from 00:49:40) Rob and Isaac - mintcast.org 2016-07-25
- [mintCast 239 – A Friendly Look at Arch](https://mintcast.org/mintcast-239-a-friendly-look-at-arch/) (from 00:32:16) Rob, Scott, Joe and Isaac - mintcast.org 2016-01-21

## 2015

- [DistroWatch Weekly Feature Story about Arch Linux](https://distrowatch.com/weekly.php?issue=20151221#arch) Jesse Smith - distrowatch.com 2015-12-21
- [Why you should try Arch Linux](https://web.archive.org/web/20151021210506/http://www.dbailey.uk.to/2015/10/why-you-should-try-arch-linux/) David Bailey - www.dbailey.uk.to 2015-10-04
- [1+ Year Running Arch Linux on a Lenovo Yoga 2](https://keithcu.com/wordpress/?p=3641) Keith Curtis - keithcu.com 2015-04-07
- [The grumpy editor's Arch Linux experience](https://lwn.net/Articles/638069/) Jonathan Corbet - LWN.net 2015-04-01
- [Five Reasons to Use Arch Linux](https://www.itworld.com/article/2898189/five-reasons-i-roll-with-arch-linux-and-why-you-should-too/) Swapnil Bhartiya - www.itworld.com 2015-03-18

## 2014

- [Linux Distros](https://web.archive.org/web/20210119183654/https://www.linuxvoice.com/linux-distros/) Ben Everard - www.linuxvoice.com 2014-10-8
- [Arch: Shangri-La for Ubuntu Power Users?](https://www.omgubuntu.co.uk/2014/04/arch-shangri-la-ubuntu-power-users) SAM TRAN - www.omgubuntu.co.uk 2014-4-19
- [Arch Linux 2014 review – Livin’ on the edge](https://www.blog.paranoidpenguin.net/2014/03/arch-linux-2014-review-livin-on-the-edge/) paranoidpenguin.net 2014-03-29
- [Arch Linux is Becoming More Mainstream](http://techrights.org/2014/03/07/arch-linux-is-becoming-more-mainstream/) Dr. Roy Schestowitz - techrights.org 2014-03-07
- [Now Might be a Good Time to Give Arch Linux a Try](http://techrights.org/2014/02/12/arch-linux-2014/) Dr. Roy Schestowitz - techrights.org 2014-02-12
- [Opinion: Arch Linux and Stability](https://web.archive.org/web/20180624103011/http://www.thelinuxrain.com/articles/opinion-archlinux-and-stability) Andrew Powell - thelinuxrain.com 2014-02-04

## 2013

- [Arch Linux on a HiDPI Lenovo Yoga 2 Pro](https://keithcu.com/wordpress/?p=3270) Keith Curtis - keithcu.com 2013-11-11
- [Arch Linux: Succeeding on the Bleeding Edge (YouTube Video)](https://www.youtube.com/watch?v=IIkHpjhpSyA) [Allan Mcrae](https://archlinux.org/people/developers/#allan) gives a talk at XX SINFO in Lisbon, Portugal - 2013-03-01

## 2012

- [Under the Hood with Arch and Gentoo](http://www.linuxuser.co.uk/features/under-the-hood-with-arch-and-gentoo/) Richard Hillesley - linuxuser.co.uk 2012-10-20
- [linux-guide-slackware-arch-linux](https://www.engadget.com/2012/08/09/linux-guide-slackware-arch-linux/) John Browning - engadget.com 2012-08-09
- [Moving to Arch Linux from Fedora](https://www.techrepublic.com/blog/australia/moving-to-arch-linux-from-fedora/1236) Chris Duckett - techrepublic.com 2012-08-07
- [Arch Linux 2012.07.15 drops AIF](http://www.h-online.com/open/news/item/Arch-Linux-2012-07-15-drops-AIF-1649722.html) Chris von Eitzen - h-online.com 2012-07-23
- [Arch linux – not just for geeks?](https://claudecat.wordpress.com/2012/07/05/arch-linux-not-just-for-geeks/) claudecat - claudecat.wordpress.com 2012-07-05
- [KISS simplicity: Arch Linux](https://www.garron.me/linux/kiss-simplicity-arch-linux-review.html) Guillermo Garron - www.garron.me 2012-05-21
- [Five-best-linux-distributions](https://lifehacker.com/5904069/five-best-linux-distributions/) Alan Henry - lifehacker.com 2012-04-23
- [Pacman Package Manager](https://web.archive.org/web/20120411012115/http://linuxlibrary.org/command-line/pacman-package-manager/) Linux Library - linuxlibrary.org 2012-03-21
- [Arch Linux Review (Early 2012)](https://www.youtube.com/watch?v=f6mxf_Ca9Ms) InfinitelyGalactic Reviews - youtube.com 2012-01-31
- [Arch’ed in a day; a noob experience](https://justinstories.wordpress.com/2012/01/28/arched-in-a-day-a-noob-experience) Justin - justinstories.wordpress.com 2012-01-28
- [On switching to Arch Linux](https://web.archive.org/web/20120130041751/http://mikethecoder.com/post/16562751962/on-switching-to-arch-linux) Mike Crittenden - mikethecoder.com 2012-01-26
- [Arch Linux: Only the Hardcore Need Apply](http://www.geardiary.com/2012/01/26/arch-linux-only-the-hardcore-need-apply/) Joel McLaughlin - geardiary.com 2012-01-26

## 2011

- [ArchLinux, not just for the elite](http://standardsandfreedom.net/index.php/2011/11/06/archlinux-notforthe-elite/) Moved by Freedom – Powered by Standards | A weblog by Charles-H. Schulz 2011-11-06
- [An Arch Tale](https://www.linuxjournal.com/content/arch-tale) Dave Phillips - Linux Journal 2011-10-04
- [Arch Linux – "It is what you make it"](http://www.h-online.com/open/features/Arch-Linux-It-is-what-you-make-it-1343717.html) Richard Hillesley - h-online.com 2011-09-16
- [The Six Best Linux Community Server Distributions](https://www.linux.com/training-tutorials/six-best-linux-community-server-distributions/) Carla Schroder - linux.com 2011-08-08
- [Ask TWIL: Best Linux Distribution and What’s Your Favorite Distro?](https://web.archive.org/web/20120613211702/http://www.thisweekinlinux.com/2011/07/at072711/) Jordan Keyes - thisweekinlinux.com 2011-07-27
- [The Arch Way](https://www.linuxjournal.com/content/arch-way) Linux Journal - linuxjournal.com 2011-01-13

## 2010

- [Arch Linux Review](https://www.jupiterbroadcasting.com/?p=3071) The Linux Action Show - jupiterbroadcasting.com 2010-10-31
- [Distro Review: Arch Linux (Video)](https://www.youtube.com/watch?v=1ZvCqslpp6M) This Week on Linux - thisweekinlinux.com 2010-05-19
- [Rolling with Arch Linux](https://lwn.net/Articles/378030/) Ivan Jelic - LWN.net 2010-03-10
- [Interview: Arch Linux Team](https://www.osnews.com/story/22692/Arch_Linux_Team) Jordan Spencer Cunningham - OSNews 2010-01-11

## 2009

-  Dennis Wyman - Zangaroa Blog 2009-11-13
- [Arch Linux 2009.08 Benchmarks](https://www.phoronix.com/scan.php?page=article&item=arch_200908_benchmarks&num=1) Michael Larabel - Phoronix.com 2009-08-14
- [Arch Linux: The Simple, Flexible (and Fast!) Distro](https://web.archive.org/web/20190302233558/http://www.linux-mag.com/id/7469) Chris Smart - Linux-Mag.com 2009-08-12
- [Arch Linux Magazine](https://archlinux.org/news/458/) Eduardo Romero, Editor 2009-08
- [Open Source Watershed: Watching Software Flow Downstream (PDF Document)](http://tannewt.org/slides/shawcroft-oscon_presentation.pdf) Scott Shawcroft - OSWatershed.org 2009-07-20
- Arch User Magazine - Issue #3 - Daniel Griffiths, Editor 2009-06
- [Why people resort to Arch Linux](https://web.archive.org/web/20090610064413/http://www.lazytechguy.com/) LazyTechGuy.com 2009-05-19
- Arch Linux - Syahrizal - OpenSourceReview.com 2009-05-11
- [Best Linux distros for power users, gamers, newbies, and more](https://www.techradar.com/news/software/operating-systems/best-linux-distros-for-power-users-gamers-newbies-and-more-596697?artc_pg=4) TechRadar.com 2009-05-10
- Arch User Magazine - Issue #2 - Daniel Griffiths, Editor 2009-05
- Arch Linux 2009.02 - Maurice Lawles - TechieMoe.com 2009-04-28
- [Distro Review - Arch Linux 2009.02](http://danlynch.org/blog/2009/04/arch/) Dan Lynch - DanLynch.org 2009-04-14
- Arch User Magazine - Issue #1 - Daniel Griffiths, Editor 2009-04
- [Arch Linux Review](https://web.archive.org/web/20200219180407/http://go2linux.garron.me/arch-linux-review) ggarron - Go2Linux.org 2009-03-21
- [Archlinux the best way to K.I.S.S.](https://bigbrovar.wordpress.com/2009/03/16/archlinux-the-best-way-to-kiss/) Bigbrovar.WordPress.com 2009-03-16
- [Linux Review 11: Arch Linux](https://linux-exploration.blogspot.com/2009/03/linux-review-11-arch-linux.html) Open_N0DE - Linux-Exploration.BlogSpot.com 03-15-2009
- [Installing Arch Linux Part 1 (YouTube Video)](https://www.youtube.com/watch?v=BIVcF5t1kZw) silvernode - YouTube.com 2009-03-10
- [Installing Arch Linux Part 2 (YouTube Video)](https://www.youtube.com/watch?v=7l-IZe733a0) silvernode - YouTube.com 2009-03-10
- [Installing Arch Linux Part 3 (YouTube Video)](https://www.youtube.com/watch?v=zmB3VVA9EGI) silvernode - YouTube.com 2009-03-10
- [Arch Linux Revolts Against ATI Catalyst Driver](https://www.phoronix.com/scan.php?page=news_item&px=NzEwMg) Michael Larabel - Phoronix.com 2009-03-01
- [Arch User Magazine - Issue #1-Preview (PDF Document)](http://archuser.com/downloads/Arch%20User%20Magazine%20-%20Issue%201%20(Preview)) Daniel Griffiths, Editor 2009-03
- [Distribution Release: Arch Linux 2009.02](https://distrowatch.com/?newsid=05336) DistroWatch.com 2009-02-17
- [Arch Linux in review](https://distrowatch.com/weekly.php?issue=20090119#feature) DistroWatch Weekly - Issue 286 2009-01-19
- [A Weekend With Arch](https://ostatic.com/blog/a-weekend-with-arch) Kristin Shoemaker - Ostatic.com 2009-01-11
- [Arch Linux: First Impressions](https://zuttobenkyou.wordpress.com/2009/01/08/arch-linux-first-impressions/) Shinobu - Zuttobenkyou.WordPress.com 2009-01-08

## 2008

- [Review: Arch Linux - A sleek, fast Linux distribution](https://linuxhelp.blogspot.com/2008/12/review-arch-linux-sleek-fast-linux.html) Ravi - LinuxHelp.BlogSpot.com 2008-12-12
- [Arch Linux - a review](https://web.archive.org/web/20090210101036/http://evilgold.baywords.com:80/?p=516) EvilGold - EvilGold.BayWords.com 2008-10-27
- [Life on the cutting edge - ArchLinux](https://web.archive.org/web/20140725211837/http://thesmallerbang.wordpress.com/2008/10/10/life-on-the-cutting-edge-archlinux/) Gautham - TheSmallerBang.WordPress.com 2008-10-10
- [Arch Linux Installation Walkthrough (YouTube Video)](https://www.youtube.com/watch?v=UV0krka1hn4) linuxandfriends - YouTube.com 2008-09-30
- [Checking Out Arch Linux](https://www.brighthub.com/computing/linux/reviews/7804.aspx) MD Weems - BrightHub.com 2008-09-20
- [A Quick Review of Arch Linux](https://web.archive.org/web/20111018130136/http://www.linuxbasement.com/content/a-quick-review-arch-linux) yoyoned - LinuxBasement.com 2008-08-23
- [A Simple Linux Review](https://42gems.com/?p=149) chi - 42Gems.com 2008-08-21
- Linux guru? then switch to Arch Linux! - Anthony Williams - AnthonyWilliams.com 2008-07-13
- [Arch Linux 2008.06: Overlord](https://celettu.wordpress.com/2008/06/25/arch-linux-200806-overlord/) Celettu's Weblog - Celettu.WordPress.com 2008-06-25
- [Distribution Release: Arch Linux 2008.06](https://distrowatch.com/?newsid=04963) DistroWatch.com 2008-06-24
- [Arch Linux Review](http://forum.notebookreview.com/showthread.php?t=246608) blackbird - Forum.NotebookReview.com 2008-05-04
- [Arch Linux: Popular KISS distro](https://www.hardware.no/artikler/aaron_griffin_english/49507/1) Robin Heggelund Hansen - Hardware.no 2008-03-25
- [Arch: Pros Only, But Not Bad](https://distrogue.blogspot.com/2008/01/arch-pros-only-but-not-bad.html) DJ Gentoo - Distrogue.BlogSpot.com 2008-01-26
- [PLANETOSS: Arch Linux - Is this really a geek's distro?](https://web.archive.org/web/20110716125204/http://www.planetoss.com/detail.php?id=7) vasanth - PlanetOSS.com 2008-01-17
- [A Stable Arch Branch?](https://kmandla.wordpress.com/2008/01/05/a-stable-arch-branch/) K. Mandla - KMandla.WordPress.com 2008-01-05

## 2007

- [The Linux Link Tech Show](https://www.tllts.org/rsspage.php) - Interview with Aaron Griffin, lead developer of Arch Linux Episode #223 12-12-07
- [Arch Linux, really impressive. Even when virtualized.](https://web.archive.org/web/20071218174523/http://tuxtoday.wordpress.com:80/2007/11/14/arch-linux-really-impressive-even-when-virtualized/) TuxToday.WordPress.com 2007-11-14
- [Arch 2007.08-2 Review](https://simplyjat.blogspot.com/2007/11/arch-200708-2-review.html) OpenReview - SimplyJat.BlogSpot.com 2007-11-14
- [Distribution Release: Arch Linux 2007.08-2](https://distrowatch.com/?newsid=04513) DistroWatch.com 2007-10-07
- [Arch Linux Review](https://web.archive.org/web/20120829000917/http://www.dvd-guides.com:80/content/view/212/104) DVD-Guides.com 2007-09-13
- [Distribution Release: Arch Linux 2007.08](https://distrowatch.com/?newsid=04394) DistroWatch.com 2007-08-05
- Linux Reality: Podcast Episode 70 - Arch Linux Episode #70 2007-07-11
- [Arch Linux: An Observational Review](https://rohrbakn.blogspot.com/2007/06/arch-linux-observational-review.html) Rohrbakn - Rohrbakn.BlogSpot.com 2007-06-22
- [Ten Things I Love About ArchLinux](https://web.archive.org/web/20070603153136/http://www.urbanpuddle.com/articles/2007/05/31/ten-things-i-love-about-archlinux) Vince Wadhwani - UrbanPuddle.com 2007-05-31
- [Distribution Release: Arch Linux 2007-05](https://distrowatch.com/?newsid=04235) DistroWatch.com 2007-05-17
- [Fallen Under the Spell of Arch Voodoo](http://www.tuxmachines.org/node/15527) srlinuxx - TuxMachines.org 2007-04-20
- [Distribution Release: Arch Linux 0.8](https://distrowatch.com/?newsid=04143) DistroWatch.com 2007-04-01
- [ArchLinux, The Distro I wish I had found sooner.](https://web.archive.org/web/20070702071647/http://usalug.org/phpBB2/viewtopic.php?t=10256) crouse - USALug.org 2007-01-10
- [Interview with Judd Vinet, founder of Arch Linux](https://www.ossblog.it/post/1728/intervista-a-judd-vinet-fondatore-del-progetto-archlinux)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2023-10-29 ⓘ\] OSSBlog.it 2007-01-02

## 2006

- [Review: Arch Linux 0.7.2](https://web.archive.org/web/20180624103009/http://karuppuswamy.com/wordpress/2006/11/12/review-arch-linux-072/) – swamytk, KaruppuSwamy.com 2006-11-12
- [Watching the Evolution of Arch Linux](https://www.osnews.com/story/15075) – Eugenia Loli-Queru, OSNews.com 2006-07-03
- [Distribution Release: Arch Linux 0.7.2](https://distrowatch.com/?newsid=03456) – DistroWatch.com 2006-05-23
- [The Simplicity of Arch (PDF Document)](https://www.linux-magazine.com/Issues/2006/64/Arch-Linux) – Jon Kent, Linux-Magazine Issue #64 2006-03
- [The Linux Link Tech Show](https://www.tllts.org/rsspage.php) – Interview with Judd Vinet, founder of Arch Linux Episode #122 2006-02-08
- [Distribution Release: Arch Linux 0.7.1](https://distrowatch.com/?newsid=03157) – DistroWatch.com 2006-01-05

## 2005

- [The Linux Link Tech Show](https://www.tllts.org/rsspage.php) – Interview with James Rayner, creator of the Archie Live CD Episode #91 2005-07-06
- [The Big Arch Linux Interview](https://www.osnews.com/story/10142) – Eugenia Loli-Queru, OSNews.com 2005-03-29
- [Arch Linux: Why It Rocks](https://www.osnews.com/story/10047) – Eugenia Loli-Queru, OSNews.com 2005-03-21
- [Make your Arch Linux a Bluetooth Access Point](https://www.osnews.com/story/9836) – Eugenia Loli-Queru, OSNews.com 2005-02-27
- [Arch Linux for Power Users](https://lwn.net/Articles/121784/) – Ladislav Bodnar, Linux Weekly News 2005-02-02
- [A Week in the Life of an Arch Linux Newbie](https://www.osnews.com/story/9540) – Andrew Roberts, OSNews.com 2005-01-27
- [Distribution Release: Arch Linux 0.7](https://distrowatch.com/?newsid=02291) – DistroWatch.com 2005-01-25

## 2004

- [Arch Linux Vs Slackware: The Best of All Worlds](https://www.osnews.com/story/8761) – Eugenia Loli-Queru, OSNews.com 2004-11-03
- [WRBurns' Review of Arch Linux 0.6](https://web.archive.org/web/20110718040758/discuss.extremetech.com/forums/thread/536482008.aspx) – WRBurns, ExtremeTech.com 2004-04-14
- [LinuxLookup: Arch Linux version 0.6 (Widget) Review](https://www.linuxlookup.com/review/arch_linux_version_0_6_widget) – LinuxLookUp.com 2004-03-23
- [Distribution Release: Arch Linux 0.6](https://distrowatch.com/?newsid=01410) – DistroWatch.com 2004-03-01
- [Arch Linux: An End To My Distro Shuffle?](https://www.osnews.com/story/5971) – Andrew Barilla, OSNews.com 2004-02-10

## 2003

- [Arch Linux: A Better Distribution](https://www.osnews.com/story/4827) – Ben Mazer, OSNews.com 2003-10-15
- [Interview with Judd Vinet](https://distrowatch.com/dwres.php?resource=interview-arch) – Ladislav Bodnar, DistroWatch.com 2003-08-03
- [My look at Arch Linux 0.2, 0.3, and 0.4](https://lwn.net/Articles/42095/) – rabnud, Linux Weekly News 2003-08-01
- [A Look at Arch Linux](https://lwn.net/Articles/40952/) – Ladislav Bodnar, Linux Weekly News 2003-07-23
- [Distribution Release: Arch Linux 0.5](https://distrowatch.com/?newsid=00870) – DistroWatch.com 2003-07-21

## 2002

- [Distribution Release: Arch Linux 0.4](https://distrowatch.com/?newsid=00538) – DistroWatch.com 2002-12-18
- [Introduction to Arch Linux](https://www.osnews.com/story/2264) – Jon "Trekboy", OSNews.com 2002-12-01
- [Distribution Release: Arch Linux 0.3](https://distrowatch.com/?newsid=00346) – DistroWatch.com 2002-08-08
- [Distribution Release: Arch Linux 0.2](https://distrowatch.com/?newsid=00201) – DistroWatch.com 2002-04-18

## 1.5　BlackArch Linux

*难度：入门 ｜ 篇幅：2.2 KB ｜ 所属：关于 Arch*

将要讲到：安装、南京大学 (江苏南京) (ipv4, ipv6, http, https)、SJTUG 软件源镜像服务 (上海) (ipv4, ipv6, https)、清华大学 (北京) (ipv4, ipv6, http, https)、中国科学技术大学 (安徽合肥) (ipv4, ipv6, http, https)、阿里云 (Global CDN) (ipv4, ipv6, http, https)

[BlackArch](https://blackarch.org/) 是一个基于 Arch Linux 的渗透测试发行版，提供了大量的网络安全工具。它是专为渗透测试人员和安全研究人员创建的开放源代码的发行版。该存储库包含 3000 多个可以单独或成组安装的工具。BlackArch 与现有的 Arch Linux 安装相容。

## 安装

### 安装教程可能不完整，请下载blackarch 官方 [PDF](https://blackarch.org/blackarch-guide-zh.pdf)

以 `root` 身份运行 [https://blackarch.org/strap.sh](https://blackarch.org/strap.sh) 并按照说明进行操作：

```
$ curl -O https://blackarch.org/strap.sh
```

验证 `SHA1` ：

```
$ echo 26849980b35a42e6e192c6d9ed8c46f0d6d06047 strap.sh | sha1sum -c
```

设置执行权限：

```
$ chmod +x strap.sh
```

运行 strap.sh：

```
$ sudo ./strap.sh
```

添加中国大陆 blackarch 镜像提高下载速度，在 `/etc/pacman.conf` 文件末尾添加镜像：

```
## 南京大学 (江苏南京) (ipv4, ipv6, http, https)
[blackarch]
Server = https://mirrors.nju.edu.cn/blackarch/$repo/os/$arch
```

```
## SJTUG 软件源镜像服务 (上海) (ipv4, ipv6, https)
[blackarch]
Server = https://mirror.sjtu.edu.cn/blackarch/$repo/os/$arch
```

```
## 清华大学 (北京) (ipv4, ipv6, http, https)
[blackarch]
Server = https://mirrors.tuna.tsinghua.edu.cn/blackarch/$repo/os/$arch
```

```
## 中国科学技术大学 (安徽合肥) (ipv4, ipv6, http, https)
[blackarch]
Server = https://mirrors.ustc.edu.cn/blackarch/$repo/os/$arch
```

```
## 阿里云 (Global CDN) (ipv4, ipv6, http, https)
[blackarch]
Server = https://mirrors.aliyun.com/blackarch/$repo/os/$arch
```

然后请安装 `blackarch-keyring` 包以导入 GPG key：

```
$ pacman -Sy blackarch-keyring
```

按照 [wiki](官方仓库.md) 启用 multilib 并运行：

```
$ sudo pacman -Syu
```

要列出所有可用工具，请运行：

```
$ sudo pacman -Sgg | grep blackarch | cut -d' ' -f2 | sort -u
```

安装所有的工具：

```
$ pacman -S blackarch
```

按照分类批量安装工具：

```
$ sudo pacman -S blackarch-<category>
```

列出 blackarch 的所有分类：

```
$ sudo pacman -Sg | grep blackarch
```

### 工具使用方法，请下载 blackarch 官方 [PDF](https://blackarch.org/blackarch-guide-zh.pdf)

## 1.6　首页

*难度：入门 ｜ 篇幅：3.4 KB ｜ 所属：关于 Arch*

将要讲到：关于发行版、社区、阅读和编辑这个 Wiki、相关链接

**欢迎访问 [Arch Linux 中文维基](https://wiki.archlinuxcn.org/wzh/Project:%E5%85%B3%E4%BA%8E)——由社区驱动的中文 Arch Linux 知识库！**

[目录表](目录.md)包含本维基的完整目录。

## 关于发行版

**[Arch Linux](Arch%20Linux.md)**
: Arch Linux 简介：Arch Linux 是什么？能做什么？有何优势？发展历程？

**[常见问题](常见问题.md)**
: 常见问题解答，解决您的疑问。

**[Arch 与其他发行版的比较](Arch%20与其他发行版的比较.md)**
: Arch 和其他发行版的相似点和不同点。

**[安装指南](../../01_安装与包管理/安装过程/安装指南.md)**
: Arch Linux 的完整安装指南。

**[建议阅读](../建议阅读/建议阅读.md)**
: 包含安装系统后的相关教程和热门文章。

**[软件列表](../../06_软件与应用/应用程序/应用程序列表.md)**
: 官方软件仓库和 [AUR](Arch%20用户软件仓库.md) 中常用软件的分类描述列表。

## 社区

**[参与贡献](参与贡献.md)**
: 无论新手或专家，您都可以参与我们，为 Arch 社区做出贡献。

**[国际社区](../../06_软件与应用/列表/国际社区.md)**
: 汇总世界各地的 Arch Linux 社区的信息。

**[行为准则](../Arch%20社区/行为准则.md)**
: Arch 社区的行为规范。

**[中文社区](../Arch%20社区/Arch%20Linux%20中文社区.md)**
: 与中文用户交流。

## 阅读和编辑这个 Wiki

**[阅读](https://wiki.archlinuxcn.org/wzh/Help:%E9%98%85%E8%AF%BB)**
: 了解一篇 Wiki 条目的基本结构。

**[查阅](https://wiki.archlinuxcn.org/wzh/Help:%E6%9F%A5%E9%98%85)**
: 搜索 Wiki，查找相关文章和离线浏览。

**[术语](Arch%20术语.md)**
: 了解 Arch Linux 常用的术语。

**[所有页面](https://wiki.archlinuxcn.org/wzh/Special:AllPages)**
: 访问所有页面的快捷方式。

**[参与贡献](https://wiki.archlinuxcn.org/wzh/Project:%E8%B4%A1%E7%8C%AE)**
: 完善 Wiki 的第一步。

**[翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)**
: 跟踪和维护页面的中文翻译。

**[编辑指南](https://wiki.archlinuxcn.org/wzh/Help:%E7%BC%96%E8%BE%91)**
: 教您如何编辑 Wiki 文章。

**[编者注意](https://wiki.archlinuxcn.org/wzh/Project:%E7%BC%96%E8%80%85%E6%B3%A8%E6%84%8F)**
: 当前中文维基编辑的注意事项。

## 相关链接

全世界的 Arch 用户乐于帮助您，您可以通过以下方式找到他们：

### Arch Linux 官方设施

- [Arch Linux 官方论坛](https://bbs.archlinux.org/)
- [Arch Linux 官方 IRC 频道](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md)
- [ArchWiki](https://wiki.archlinux.org/title/Main_Page)
- [Arch 用户软件仓库（AUR）](Arch%20用户软件仓库.md)
- [Arch Linux Gitlab](https://gitlab.archlinux.org/archlinux/)
- [Planet Arch Linux](https://planet.archlinux.org/)

### Arch Linux 中文社区设施

- [Arch Linux 中文社区首页](https://www.archlinuxcn.org/)
- [Arch Linux 中文论坛](https://forum.archlinuxcn.org/)
- [Arch Linux 中文社区仓库](../../01_安装与包管理/非官方用户仓库/Arch%20Linux%20中文社区仓库.md)
- [Arch Linux 中文社区交流群](https://wiki.archlinuxcn.org/wzh/Project:Arch_Linux_%E4%B8%AD%E6%96%87%E7%A4%BE%E5%8C%BA%E4%BA%A4%E6%B5%81%E7%BE%A4%E6%8C%87%E5%BC%95)
- [Arch Linux 中文星球](https://forum.archlinuxcn.org/c/general/planet/35)

## 1.7　Arch 术语

*难度：进阶 ｜ 篇幅：8.9 KB ｜ 所属：关于 Arch*

将要讲到：ABS、Arch Linux、Arch Linux 档案、AUR、bbs、core/\[core\]

[Help:术语表](https://wiki.archlinuxcn.org/wzh/Help:%E6%9C%AF%E8%AF%AD%E8%A1%A8)

> **翻译状态：**
>
> - 本文（或部分内容）译自 [Arch terminology](https://wiki.archlinux.org/title/Arch_terminology)，最近一次同步于 2024-07-16，若英文版本有所[更改](https://wiki.archlinux.org/title/Arch_terminology?diff=0&oldid=812480)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/Arch_terminology_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

本页面试图揭开 Arch Linux 社区术语的神秘面纱。您可以自由的添加或更改任何术语，但是请使用某个章节的编辑选项。如果想添加新术语，请去原文界面编辑，然后翻译到此界面。

注：本文为规避后续争议，术语权且采用与原文一致的顺序。

## ABS

ABS 是 [Arch Build System](Arch%20构建系统.md) 的缩写。

## Arch Linux

应该用下面术语指代 Arch：

- **Arch Linux**
- **Arch** （省略 Linux）
- **archlinux** （UNIX 名）

Archlinux、ArchLinux、archLinux、aRcHlInUx 等称呼都是不标准的。

'Arch' 在 "Arch Linux" 中的官方读音是 /ɑːrtʃ/。"arch" 的读音不同于 "ark"：和单词 "archer" 或 "archnemesis" 中的一样，与 "archangel" 中的不同。 近似拼音 "a er qi" 的读音，不读作 "a er ke"。

## Arch Linux 档案

[Arch Linux Archive](../Arch%20项目/Arch%20Linux%20Archive.md) (ALA)，故称 Arch Linux Rollback Machine (ARM)，保存历史上的官方软件仓库快照、ISO 镜像和 boot straps 压缩包。

## AUR

[Arch User Repository](Arch%20用户软件仓库.md)（Arch 用户仓库） 是一个社区驱动的 Arch 用户仓库。其储存包的描述信息（[PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md)），后者允许您用 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md)，从源码构建包并通过 [pacman](../../01_安装与包管理/包管理器/Pacman.md#Additional_commands) 安装。AUR 的创建是为了整理和分享来自社区的新包，并促使 extra 仓库尽快收录受欢迎的包。

许多进入官方仓库的新包最先收录于 AUR。AUR 中，用户可以贡献自己的包构建（PKGBUILD 及相关文件）。AUR 社区可以对 AUR 中的包投票。如果一个包足够受欢迎——前提是它要有一个兼容的许可证，和良好的封装——它可能进入 *extra* 仓库（可以直接通过 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 或 [ABS](Arch%20构建系统.md) 访问）。

您可以[在这里](https://aur.archlinux.org)访问 *A*rch Linux *U*ser Community *R*epository。

## bbs

**B**ulletin **b**oard **s**ystem，在 Arch 中指[用户支持论坛](https://bbs.archlinux.org)。

## core/\[core\]

*core* 仓库包含 Arch linux 的最基本软件包：一个命令行系统的所需都在 \[core\] 中。

## custom/user repository

任何人都可以创建供其它人使用的在线仓库。要这么做，需要一批软件包及对应的兼容 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 的数据库文件。把这些托管到线上，其他人就可添加你的仓库并使用了。

参考 [Custom local repository](../../01_安装与包管理/包管理器/Pacman_提示和技巧.md).

## Developer

這些半神級的開發者，無償地為 Arch 持續改進。他們的地位僅次於 Arch 的神祇 Judd Vinet 和 Aaron Griffin，而這兩位神祇又只能屈居於「玉米餅（tacos）」之下。

## extra/\[extra\]

Arch 的官方软件包很精简，但是我们提供了更大更完整的 "附加" 软件仓库。其包含大量非 core 的软件包。此仓库在社区的强力支持下日益壮大。桌面软件环境，窗口管理器和常用程序都位于此仓库。

## initramfs/initrd

参见 [mkinitcpio](../../01_安装与包管理/Initramfs/Mkinitcpio.md)。

## KISS

Keep It Simple, Stupid 的简写。[简约](Arch%20Linux.md#Simplicity)是 Arch Linux 坚持的原则。

## makepkg

[makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 会读取 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 文件，然后按脚本编译软件包。所需的是 Linux 编译环境，[curl](https://archlinux.org/packages/?name=curl)包 和一些编译脚本。基于脚本构建的优点是一劳永逸。有了编译脚本后，只需执行 makepkg，它会执行剩余的工作：下载并验证源代码，检查依赖关系，配置构建时间，构建软件包，安装软件包到临时根目录，进行定制，生成元数据，然后打包供 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 使用。

## namcap

[namcap](../Arch%20项目/Namcap.md) 是软件包分析工具，可以检查 Arch Linux 软件包及其 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 文件。 可以按规则检查文件列表、文件本身或单独的 PKGBUILD 文件。

规则检查会返回三类消息：错误（error），警告（warning）或信息（information）（类似提示或注释）。错误（'E:'）是 namcap 非常确信错误并需要修复的东西。警告（'W:'）是 namcap 认为应当更改，但如果您清楚您在做什么，就可以忽略的东西。信息（'I:'）只在您使用 info 参数时显示。信息类消息提供无需更改任何东西但可能有用的的信息。

## package

请参考 [Pacman#安装软件包](../../01_安装与包管理/包管理器/Pacman.md#安装软件包)。

> **注意：**不同 GNU/Linux 发行版使用不同的包和包管理器，这意味着您不能用 pacman 在 Arch 上安装一个 Debian 软件包。

## 软件包维护者

软件包维护者的作用是：当上游新版本可用时，更新包；处理有关指定包中 bug 的支持问题。该术语适用于：

- Arch 员工，故称[受信用户（Trusted User，TU）](https://lists.archlinux.org/archives/list/aur-general@lists.archlinux.org/thread/6UPKMAL2A7FNAUNWMTHWI4EXV2KS5OSC/)，[管理](../团队/软件包维护者.md) *extra* 仓库中的包，并监督 [AUR](Arch%20用户软件仓库.md)。他们由现有软件包维护者以多数票任命，并遵循 AUR 软件包维护者指南和[软件包维护者章程](https://package-maintainer-bylaws.aur.archlinux.org/)。
- 在[官方仓库](官方仓库.md)（尤其是*core*）维护包的开发者。
- 在 AUR 维护 PKGBUILD 的所有用户。

某个包的维护者是指此人当前对这个包负责。之前的维护者应当在 PKGBUILD 中，与其他对此包有贡献的人一同，作为贡献者列出。

## pacman

请参考 [pacman](../../01_安装与包管理/包管理器/Pacman.md)。

## PKGBUILD

[PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 是构建 Arch Linux 软件包所使用的脚本。详情参考 [Creating packages](创建软件包.md)。

## 仓库/repo

软件仓库包含一个或（通常地）多个基于 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 预构建的软件包。[官方仓库](官方仓库.md)分成多个部分以便管理。Pacman 使用这些仓库查找和安装软件包。一个仓库可以是本地的（在您自己的电脑上），也可以是远程的（先下载软件包再安装）。

## RTFM

[RTFM](https://en.wikipedia.org/wiki/RTFM) 代表 "*R*ead *T*he *F*riendly *M*anual"（参阅友好文档）。很多 Linux/Arch 新用户在询问一个程序文档明确定义的问题时，会收到这个简洁回复。

通常，在用户在发问前，未曾尝试自己寻找解决方案时，这个缩写会出现。如果有人这么回复您，请注意：这并不是侮辱，只是委婉地请您更细心些，同时对您显现出的疏忽表示略微不满。

收到这个信息后，最正确的动作是阅读手册页面。要阅读某个特定程序的手册：`man *程序名称*`,。

如果没有找到需要的信息，还可以查看下面内容：

- 搜索 [wiki](https://wiki.archlinuxcn.org/wzh/Special:Search)
- 搜索[论坛](https://bbs.archlinux.org)
- 搜索[邮件列表](https://www.google.com/#hl=en&q=arch+site:archlinux.org%2Fpipermail%2F)
- 搜索 [web](https://www.google.com)

注：以上链接直接出自英语原文，限于特殊情况，某些可能无法访问。读者请自行变通。

## 测试仓库

主要的软件包/更新在正式发布前，会放在此仓库进行测试，查看是否有 bug 和升级问题。[默认禁用](官方仓库.md#Testing_repositories)，可在 `/etc/pacman.conf` 中启用。

## Arch 之道

[Arch Linux 原则](Arch%20Linux.md#Principles)的一个非正式传统说法。

## 受信用户（TU）

参见 [#软件包维护者](#软件包维护者)。

## Wiki

我们的 [wiki](https://en.wikipedia.org/wiki/Wiki) 一个存放 Arch Linux 文档的地方，任何人都可以对这些文档做贡献。

## 1.8　镜像源

*难度：进阶 ｜ 篇幅：9.3 KB ｜ 所属：关于 Arch*

将要讲到：官方镜像、启用特定的镜像、镜像排序、服务端排序、问题解决、参见

本页面说明如何选择和配置镜像，以及列出可用的镜像。

## 官方镜像

可以通过软件包 [pacman-mirrorlist](https://archlinux.org/packages/?name=pacman-mirrorlist)包 获取官方镜像列表。若要想获得更新的镜像列表，请访问 [Pacman Mirrorlist Generator](https://archlinux.org/mirrorlist/) 页面。

访问[镜像状态](https://archlinux.org/mirrors/status/)页面来检查镜像的状态。这里推荐使用最新的镜像，或者说是已同步的镜像。

如果想要将自己的镜像添加到官方列表中，参见 [arch:DeveloperWiki:NewMirrors](https://wiki.archlinux.org/title/DeveloperWiki:NewMirrors)。同时，请将其添加至[非官方镜像](../../06_软件与应用/列表/非官方镜像源.md)页面。

### 支持IPv6的镜像

[Pacman Mirrorlist Generator](https://archlinux.org/mirrorlist/?ip_version=6) 也可以用来获取支持IPv6的镜像列表。

## 启用特定的镜像

想要启用镜像，打开 `/etc/pacman.d/mirrorlist` 并定位到你的地理区域。对您想使用的镜像取消注释。例如：

```
# Any
# Server = http://mirrors.kernel.org/archlinux/$repo/os/$arch
Server = https://mirrors.kernel.org/archlinux/$repo/os/$arch
```

参见[按照速度排序](#按速度排序)查看帮助选择镜像的工具。

> **提示：**
>
> - 取消5个你最喜欢的镜像的注释，把他们放在 mirrorlist 文件最上方。这样你就很容易找到它们并且如果第一个镜像出问题可以很容易切换。这也让合并 mirrorlist 更新更容易。
> - HTTP 镜像比 FTP 快，因为 HTTP 可以[保持连接](https://en.wikipedia.org/wiki/HTTP_persistent_connection)，而使用 FTP 时 *pacman*每下载一个新软件包就需要重新建立连接，这样也就导致了短暂的停顿。
> - HTTPS 镜像比 HTTP 和 FTP 更难受到干扰和篡改，在部分地区的可用性会更高。

也可以在 `/etc/pacman.conf` 中指定镜像。对于 *\[core\]* 仓库，默认设置是：

```
[core]
Include = /etc/pacman.d/mirrorlist
```

如果想要使用 *HostEurope* 镜像作为默认镜像，请把它添加在 `Include` 行之前：

```
[core]
Server = http://ftp.hosteurope.de/mirror/ftp.archlinux.org/core/os/$arch
Include = /etc/pacman.d/mirrorlist
```

pacman 会首先尝试链接这个镜像。如果需要的话，可以继续修改*\[testing\]*, *\[extra\]*, 和 *\[community\]*部分。

> **注意：**如果镜像直接在 `pacman.conf` 中声明，记得在所有的仓库使用同样的镜像。否则不相容的包就可能被安装。如 *\[core\]* 中的 linux 和 *\[extra\]* 中的旧的内核模块不相容。

### 强制 pacman 刷新软件包列表

镜像可能不同步，并且从旧镜像中获取的的软件包列表也可能与新镜像中的软件包列表不一致，即使列表里的日期可能表明它们是一致的。

创建和编辑 `/etc/pacman.d/mirrorlist` 之后，使用下面命令强制更新镜像：

```
# pacman -Syyu
```

传入两次 `--refresh` 或 `-y` 将强制更新所有软件包列表，即使系统认为它们已经是最新。**每次修改镜像之后都应该使用 `pacman -Syyu`**。

> **警告：**在大多数情况下，如果你强制更新 pacman 数据库，你将需要强制降级任何可能太新的软件包以对应新的镜像所提供的版本。这可以避免软件包升级不一致导致的部分更新问题。
>
> ```
> # pacman -Syyuu
> ```
>
> 当正在使用[同步成功的镜像](https://archlinux.org/mirrors/status/#successful)或者检查镜像的 **lastsync** 文件来以确保软件包列表已经更新后，则没有必要使用这条命令。

## 镜像排序

当下载软件包时，pacman 按照 `/etc/pacman.d/mirrorlist` 中的顺序来使用镜像。服务器在列表中出现的顺序决定了其优先级。

由于速度最快的服务器可能是不同步的，所以仅基于速度来对镜像进行排序很可能不是最佳方案。取而代之的是，先制作一个按照[速度](#按速度排序)排序的镜像列表，然后根据他们的[状态](https://archlinux.org/mirrors/status/)来删除那些不同步的镜像。

建议定期重复这一过程，来保持镜像列表同步。

### 按速度排序

#### 排序已存在的镜像列表

软件包 [pacman-contrib](https://archlinux.org/packages/?name=pacman-contrib)包 提供了一个 [Bash](../../07_开发与工具/命令行解释器/Bash.md) 脚本，`/usr/bin/rankmirrors` 可以根据镜像建立连接的速度对其进行排序，来使用最快的本地镜像。

首先，备份已存在的 `/etc/pacman.d/mirrorlist`：

```
# cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
```

接下来准备 `/etc/pacman.d/mirrorlist.backup` 以使用 *rankmirrors* 进行排序，可执行以下操作：

- 编辑 `/etc/pacman.d/mirrorlist.backup` ，将准备测速的镜像取消注释。

- 如果文件中的服务器已按照国家或地区分组，则可通过以下命令提取特定国家或地区的所有服务器：

    ```
    $ awk '/^## Country Name$/{f=1; next}f==0{next}/^$/{exit}{print substr($0, 1);}' /etc/pacman.d/mirrorlist.backup
    ```

- 若要取消所有的注释，可执行以下 `sed` 命令：

    ```
    # sed -i 's/^#Server/Server/' /etc/pacman.d/mirrorlist.backup
    ```

- 最后，对镜像进行排序，这里使用 `-n 6` 参数来实现只获取 6 个最快的镜像：

    ```
    # rankmirrors -n 6 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist
    ```

#### 获取实时镜像列表并对其排序

可以从 [Pacman Mirrorlist Generator](https://archlinux.org/mirrorlist/) 获取一些国家/地区的最新镜像列表来使用 *rankmirrors*。 下面的命令会获取位于*中国*且支持 *https* 协议的最新镜像的列表，然后将位于列表的服务器取消注释，最后再对其排序后打印输出 5 个最快的镜像。

```
$ curl -s "https://archlinux.org/mirrorlist/?country=CN&protocol=https&use_mirror_status=on" | sed -e 's/^#Server/Server/' -e '/^#/d' | rankmirrors -n 5 -
```

> **提示：**可以通过使用任何基于文本的浏览器（比如 [elinks(1)](https://man.archlinux.org/man/elinks.1)）导航至 `[https://archlinux.org/mirrorlist](https://archlinux.org/mirrorlist)` 来以交互式方式完成这个过程。

现已存在的工具：

**Rate Mirrors** — 此工具通过获取镜像，跳过不同步或正在同步的镜像，然后使用关于海底电缆和互联网交换的信息在国家或地区之间切换，最终找到快速的镜像。并且足够快以实现在每次更新以前运行。它也支持通过标准输入自定义镜像列表。

: [https://github.com/westandskif/rate-mirrors](https://github.com/westandskif/rate-mirrors) || [rate-mirrors](https://aur.archlinux.org/packages/rate-mirrors/)AUR, [rate-mirrors-bin](https://aur.archlinux.org/packages/rate-mirrors-bin/)AUR

## 服务端排序

官方的 [Pacman Mirrorlist Generator](https://archlinux.org/mirrorlist/) 提供了一个简单的方式来获取一个排序过的镜像列表。因为所有排序是在考虑了多种因素的单一服务器上完成的，与在每个单独的客户端上进行排序相比，镜像服务器和客户端的负载量就要低很多。

**[Reflector](../../01_安装与包管理/包管理/Reflector.md)** — 从[镜像状态](https://archlinux.org/mirrors/status/)页面取回最新的镜像列表，过滤后通过速度排序再覆盖写入 `/etc/pacman.d/mirrorlist`

: [https://xyne.dev/projects/reflector/](https://xyne.dev/projects/reflector/) || [reflector](https://archlinux.org/packages/?name=reflector)包

## 问题解决

### 镜像列表缺失

如果你遇到以下错误：

```
error: config file /etc/pacman.d/mirrorlist could not be read: No such file or directory
```

可以直接从网站上获取镜像列表：

```
# curl -o /etc/pacman.d/mirrorlist https://archlinux.org/mirrorlist/all/
```

如果碰到 `$arch` 变量未定义的问题，请在 `/etc/pacman.conf` 中加入：

```
Architecture = auto
```

请确认已如[上文](#启用特定的镜像)对首选的镜像取消注释，然后执行：

```
# pacman -Syu pacman-mirrorlist
```

### 异常的镜像

如果你确定一个镜像运行不正常，并且其没有反映在[镜像状态页面](https://archlinux.org/mirrors/status/)上，更换镜像并考虑[开启一个 bug 报告](../Arch%20开发/漏洞报告准则.md#Opening_a_bug)。关于镜像的问题，issue 应该发在 Arch Linux 项目上（位于登录框下方的列表，或者通过在页面上搜索 switch）

## 参见

- [GitHub archweb mirrorlist.py](https://github.com/archlinux/archweb/blob/master/mirrors/views/mirrorlist.py) - source code of the archweb mirrorlist generator

## 1.9　Arch 构建系统

*难度：进阶 ｜ 篇幅：10.1 KB ｜ 所属：关于 Arch*

将要讲到：工具链、用例、用法、技巧

Arch 构建系统（[Arch build system](https://zh.wikipedia.org/wiki/Arch_Linux#Arch_.E7.BC.96.E8.AF.91.E7.B3.BB.E7.BB.9F)，ABS）是一套从源码构建并打包软件包的系统。在 Arch 中，[pacman](../../01_安装与包管理/包管理器/Pacman.md) 专门管理二进制软件包（包括那些由 ABS 创建的）；而 ABS 则是一系列工具，负责把源代码编译成可安装的 *.pkg.tar.zst* 软件包。

Arch 构建系统类似于 \*BSD 上的 [ports](https://zh.wikipedia.org/wiki/Ports)，其用于自动化从源码构建软件包的过程。*Port* 可以自动下载源代码、解压缩、打补丁、编译和安装软件。一个 *port* 仅仅是指用户电脑上的一个目录，该目录根据即将安装的软件来命名，它包含一些能指导源码的下载和编译安装的文件。Port 系统让你只需在 port 目录下运行 `make` 或 `make install clean` 就能安装你想要的软件。

ABS 的概念与 Ports 相似。它由为每个 Arch Linux 可用软件包提供的 git 仓库组成。每个目录中并不包含二进制包或源代码，而是包含一个 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 文件（有时也会有其它文件）。在有 `PKGBUILD` 文件的目录里运行 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 命令，系统就会在目录中下载软件的源代码、编译并打包在构建文件夹里。然后就可以通过 *pacman* 进行安装或升级了。

## 工具链

'ABS' 可以作为一个总括性术语来使用，因为它包含并依赖于若干其他部件。因此，尽管从严格意义上来讲并不精确，ABS可指代包含以下工具的完整工具集：

Arch 构建系统包含并依赖于数个组件和工具，分别用于从源码到构建出软件包的各个步骤中：

**仓库树**
: 目录树包含构建所有官方软件包所需的文件，但不包括软件包本身和源代码。这些仓库以 [Git](../../07_开发与工具/版本控制系统/Git.md) 仓库的形式托管在 [gitlab.archlinux.org](https://gitlab.archlinux.org/archlinux/packaging/packages) 上。更多详细信息请参阅[#仓库结构](#仓库结构)。

**[PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md)**
: [Bash](../../07_开发与工具/命令行解释器/Bash.md) 脚本，包含软件的源代码的 URL 和编译打包指令。

**[makepkg](../../08_安全与加密/OpenPGP/Makepkg.md)**
: 一个 shell 命令工具，会读取 PKGBUILD，自动下载源码、编译并创建 *.pkg.tar\** 包（拓展名由 `makepkg.conf` 中的 `PKGEXT` 指定）。makepkg 也可以用来从 [AUR](Arch%20用户软件仓库.md) 或第三方代码构建你自己的自定义软件包，具体细节请参考[创建软件包](创建软件包.md)。

**[pacman](../../01_安装与包管理/包管理器/Pacman.md)**
: pacman 是完全独立的一个系统，但是它在通过 makepkg 或手动安装或移除软件包、解决依赖关系时都是必需的。

**`pkgctl`/[devtools](https://archlinux.org/packages/?name=devtools)包**
: `devtools` 是用于为 Arch Linux 发行版构建和维护官方仓库软件包的一系列工具。[pkgctl(1)](https://man.archlinux.org/man/pkgctl.1) 是一个高层级工具，用于帮助从软件源码仓库构建 Arch Linux 软件包并发布到二进制软件仓库中。

**[AUR](Arch%20用户软件仓库.md)**
: Arch 用户软件仓库（AUR）的 PKGBUILD 独立于官方仓库，但其中的软件包同样可以使用 ABS 的工具进行打包构建。它包含成千上万的用户贡献的 PKGBUILD，来提供 Arch 官方仓库中没有的软件。如果需要编译官方 Arch 树之外的软件包，AUR 中已经存在的可能性非常大。

> **警告：**官方 PKGBUILD 假定包是[在干净的 chroot 环境中构建](https://wiki.archlinux.org/title/DeveloperWiki:Building_in_a_clean_chroot)的。在“脏”的环境中构建可能会失败或者在运行时有意外行为。因为如果编译系统动态检查依赖的话，编译结果会受到当前系统可用包的影响。

### 仓库结构

每个软件包在 [Arch Linux GitLab 服务器](https://gitlab.archlinux.org/)的 [archlinux/packaging/packages](https://gitlab.archlinux.org/archlinux/packaging/packages) 命名空间下都有其独立的*源码*仓库。每个仓库都包含了官方打包所使用的 PKGBUILD 等文件。另外，这里还存放了开发者在打包过程中所使用的一些文件。

例如，[acl](https://archlinux.org/packages/?name=acl)包 的目录结构是这样的：

```
acl
├── keys
│   └── pgp
│       ├── 259B3792B3D6D319212CC4DCD5BF9FEB0313653A.asc
│       ├── 600CD204FBCEA418BD2CA74F154343260542DF34.asc
│       └── B902B5271325F892AC251AD441633B9FE837F581.asc
├── PKGBUILD
└── .SRCINFO
```

源代码并不直接包含在ABS目录中，而是构建时从 `PKGBUILD` 里指定的源代码 URL 下载。

在构建完官方软件包后，它会被发布到[官方仓库](官方仓库.md)之一：*core*，*extra*，*multilib* 或是先放到测试仓库中。这些仓库存放着*二进制文件*，并不存放在 GitLab 上，而是由[镜像源](镜像源.md)进行分发。

## 用例

Arch 构建系统可以将从源码构建出软件包的一些工作自动化，常见用法包括：

- 需要编译或重新编译软件包
- 由开发者和打包人员为 Arch Linux 构建官方二进制软件包
- 从源代码编译并安装 Arch 官方源里没有的软件（详情请参照[创建软件包](创建软件包.md)）
- 定制现有的软件包以满足你的特定需求（比如开启或禁用相关选项、打补丁）
- 用你的编译器的 flags 重新构建整个系统，“就像 FreeBSD 那样”
- 干净地编译安装你自己定制的内核。（参照[内核编译](../../02_系统管理与内核/内核/内核.md#编译)）
- 使内核模块（比如某些显卡驱动）在你定制的内核下正常工作
- 修改 PKGBUILD 中的版本就能方便地编译和安装新的、老的、beta 或者开发版本的 Arch 软件包

## 用法

### 获取 PKGBUILD 源码

要想获取从源代码构建特定软件包所需的 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 文件，可以使用 *pkgctl* 工具，或者直接使用 [Git](../../07_开发与工具/版本控制系统/Git.md)。

#### 使用 pkgctl 工具

如需使用 *pkgctl*，请先安装 [devtools](https://archlinux.org/packages/?name=devtools)包。*pkgctl* 是一个帮助为 Arch Linux 构建软件包源文件的工具。

要使用 *pkgctl* 获取包含软件包 *pkgname* 最新构建文件的 git 仓库，请使用以下命令：

```
$ pkgctl repo clone pkgname
```

> **提示：**默认情况下通过 SSH 获取，如果没有在 Arch GitLab 账户中设置 SSH 密钥，则需要通过 HTTPS 获取：`pkgctl repo clone --protocol=https *pkgname*`

请注意，这里所说的构建文件是指 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md)，可能还包括一些其他必要的文件，如密钥。也就是说，是 ABS 所需的基本文件，而不是由包的开发团队编写的源代码文件，例如 C 或 Python 文件。

该命令不仅会提供当前的构建文件，还会提供它的历史版本。此外，还可以使用其他 [git](../../07_开发与工具/版本控制系统/Git.md) 命令来签出旧版本或跟踪更改。

如果想获取特定版本，可以使用以下命令：

```
$ pkgctl repo clone --switch="2:1.19.5-1" go
```

如需获取更多详细信息以及其他可用命令，请阅读 [pkgctl-repo-clone(1)](https://man.archlinux.org/man/pkgctl-repo-clone.1)

#### 直接使用 git

使用以下命令来获取软件包：

```
$ git clone https://gitlab.archlinux.org/archlinux/packaging/packages/pkgname.git
```

例如获取 Apache 的构建文件：

```
$ git clone https://gitlab.archlinux.org/archlinux/packaging/packages/apache.git
```

### 构建软件包

关于如何配置 *makepkg* 来从 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 构建软件包，请参考 [makepkg#配置](../../08_安全与加密/OpenPGP/Makepkg.md#配置)。

然后把 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 所在目录复制到新的位置。在新目录按需要进行修改。 并按照 [makepkg#使用](../../08_安全与加密/OpenPGP/Makepkg.md#使用)来构建和安装软件包。

## 技巧

### 保留修改过的软件包

[pacman](../../01_安装与包管理/包管理器/Pacman.md) 进行升级时会将修改后的软件包升级到仓库中的最新版本，可以通过下面方式避免这个行为：

在 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 中将软件包加入 `modified` 组：

```
PKGBUILD
```

```
groups=('modified')
```

然后将此组加入 `/etc/pacman.conf` 的 `IgnoreGroup`：

```
/etc/pacman.conf
```

```
IgnoreGroup = modified
```

当系统升级发现官方仓库中有新版本时，pacman 会显示软件包因为在 *IgnoreGroup* 中而被忽略的提示，这时需要从 ABS 编译更新的软件包以防止[部分升级](../../02_系统管理与内核/系统管理/系统维护.md)。

## 1.10　ASCII 艺术

*难度：进阶 ｜ 篇幅：11.4 KB ｜ 所属：关于 Arch*

将要讲到：软件、Arch ASCII 徽标、Cowsay、Ponysay、另请参见

## 软件

- **Asciiquarium** — ASCII 艺术中的水族馆/海洋动画。

: [https://robobunny.com/projects/asciiquarium/](https://robobunny.com/projects/asciiquarium/) || [asciiquarium](https://archlinux.org/packages/?name=asciiquarium)包

- **boxes** — 文本模式框和注释绘图过滤器。

: [https://boxes.thomasjensen.com/](https://boxes.thomasjensen.com/) || [boxes](https://aur.archlinux.org/packages/boxes/)AUR, [boxes-git](https://aur.archlinux.org/packages/boxes-git/)AUR

- **FIGlet** — 一种用普通文本制作大字母的程序。

: [http://www.figlet.org/](http://www.figlet.org/) || [figlet](https://archlinux.org/packages/?name=figlet)包

- **jp2a** — 一个小的实用程序，用于将 JPG 和 PNG 图像转换为 ASCII。

: [https://csl.name/jp2a/](https://csl.name/jp2a/) || [jp2a](https://archlinux.org/packages/?name=jp2a)包

- **TOIlet** — 自由的 FIGlet 实用程序替代。

: [http://caca.zoy.org/wiki/toilet](http://caca.zoy.org/wiki/toilet) || [toilet](https://aur.archlinux.org/packages/toilet/)AUR

有关图表，请访问 [https://asciiflow.com/](https://asciiflow.com/) 。

## Arch ASCII 徽标

请参阅[原始论坛主题](https://bbs.archlinux.org/viewtopic.php?pid=1476426)。

```
 \e[H\e[2J
           \e[0;36m.
          \e[0;36m/ \
         \e[0;36m/   \      \e[1;37m               #     \e[1;36m| *
        \e[0;36m/^.   \     \e[1;37m a##e #%" a#"e 6##%  \e[1;36m| | |-^-. |   | \ /
       \e[0;36m/  .-.  \    \e[1;37m.oOo# #   #    #  #  \e[1;36m| | |   | |   |  X
      \e[0;36m/  (   ) _\   \e[1;37m%OoO# #   %#e" #  #  \e[1;36m| | |   | ^._.| / \ \e[0;37mTM
     \e[1;36m/ _.~   ~._^\
    \e[1;36m/.^         ^.\ \e[0;37mTM
```

```
 \e[H\e[2J
           \e[1;36m.
          \e[1;36m/#\
         \e[1;36m/###\      \e[1;37m               #     \e[1;36m| *
        \e[1;36m/p^###\     \e[1;37m a##e #%" a#"e 6##%  \e[1;36m| | |-^-. |   | \ /
       \e[1;36m/##P^q##\    \e[1;37m.oOo# #   #    #  #  \e[1;36m| | |   | |   |  X
      \e[1;36m/##(   )##\   \e[1;37m%OoO# #   %#e" #  #  \e[1;36m| | |   | ^._.| / \ \e[0;37mTM
     \e[1;36m/###P   q#,^\
    \e[1;36m/P^         ^q\ \e[0;37mTM
```

来自 [screenfetch](https://archlinux.org/packages/?name=screenfetch)包 3.6.5-1:

````
                   -`
                  .o+`
                 `ooo/
                `+oooo:
               `+oooooo:
               -+oooooo+:
             `/:-:++oooo+:
            `/++++/+++++++:
           `/++++++++++++++:
          `/+++ooooooooooooo/`
         ./ooosssso++osssssso+`
        .oossssso-````/ossssss+`
       -osssssso.      :ssssssso.
      :osssssss/        osssso+++.
     /ossssssss/        +ssssooo/-
   `/ossssso+/:-        -:/+osssso+-
  `+sso+:-`                 `.-/+oso:
 `++:.                           `-/+/
 .`                                 `
````

来自 [alsi](https://aur.archlinux.org/packages/alsi/)AUR 0.4.7-1:

```
                  ##
                 ####
                ######
               ########
              ##########
             ############
            ##############
           ################
          ##################
         ####################
        ######################
       #########      #########
      ##########      ##########
     ###########      ###########
    ##########          ##########
   #######                  #######
  ####                          ####
 ###                              ###
```

来自 [archey3](https://archlinux.org/packages/?name=archey3)包 0.5-2:

```
               +
               #
              ###
             #####
             ######
            ; #####;
           +##.#####
          +##########
         #############;
        ###############+
       #######   #######
     .######;     ;###;`".
    .#######;     ;#####.
    #########.   .########`
   ######'           '######
  ;####                 ####;
  ##'                     '##
 #'                         `#
```

ArchWiki 的生日庆祝：

```
                                               ,- " " " -.
                                              /     _     \
                                             :     / |     ;     ,- " " "-.
                                             \     | |     /    /   ____   \
                                              \    |_|    /    ;    |  |    ;
                                                `.. ,, ..´     \    |__|    /
                                                  /✿\           \          /
                                                    )             `. ,, .´
     _             _  __        ___ _    _         (                /✿\
    / \   _ __ ___| |_\ \      / (_) | _(_)         )                (
   / _ \ | '__/ __| '_ \ \ /\ / /| | |/ / |        (                  )
  / ___ \| | | (__| | | \ V  V / | |   <| | ....... ) .*..---...*.....*
 /_/   __|  ___|_| |_|_/_/  |_|_|__| ....♫♪հձթթყ ъﻨгէհժձყ eժﻨէﻨon ♫
```

用户创作的 Arch ASCII 徽标：

```
                    -@
                   .##@
                  .####@
                  @#####@
                . *######@
               .##@o@#####@
              /############@
             /##############@
            @######@**%######@
           @######`     %#####o
          @######@       ######%
        -@#######h       ######@.`
       /#####h**``       `**%@####@
      @H@*`                    `*%#@
     *`                            `*
```

Cyllos 创作

```
\033[38;2;23;147;209m                   ▄
                  ▟█▙
                 ▟███▙
                ▟█████▙
               ▟███████▙
              ▂▔▀▜██████▙
             ▟██▅▂▝▜█████▙
            ▟█████████████▙
           ▟███████████████▙
          ▟█████████████████▙
         ▟███████████████████▙
        ▟█████████▛▀▀▜████████▙
       ▟████████▛      ▜███████▙
      ▟█████████        ████████▙
     ▟██████████        █████▆▅▄▃▂
    ▟██████████▛        ▜█████████▙
   ▟██████▀▀▀              ▀▀██████▙
  ▟███▀▘                       ▝▀███▙
 ▟▛▀                               ▀▜▙
```

LnLcFlx 创作

## Cowsay

这是一些 [cowsay](https://archlinux.org/packages/?name=cowsay)包 和 fortune 一起使用的例子：

### 带有 fortune 的默认的牛

- 结合程序 [cowsay](https://archlinux.org/packages/?name=cowsay)包：

```
$ cowthink $(fortune)
 ________________________________________
( The best cure for insomnia is to get a )
( lot of sleep. -W.C. Fields             )
 ----------------------------------------
        o   ^__^
         o  (oo)_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

- 显示潜在的进攻性 fortune：

```
$ cowsay $(fortune -o)
 _________________________________
< Chastity is its own punishment. >
 ---------------------------------
        \   ^__^
         \  (oo)_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

ASCII 图像由位于 `.cow` 中的 `/usr/share/cows` 文本文件生成，并且所有主题都可以使用 `cowsay -l` 列出。可以根据用户的喜好编辑这些文件。自定义图像也可以从头开始创建，也可以在网上找到。创建自定义 cow 文件的最简单方法是使用现有的 cow 文件作为模板。要测试自定义文件：

```
$ cowsay -f /path/to/file $(fortune)
```

### 带有 fortune 的随机的牛

```
$ fortune -c | cowthink -f $(find /usr/share/cows -type f | shuf -n 1)
  ___________________________________________
( (computers) % A language that doesn't )
( affect the way you think about                  )
( programming is not worth knowing.       )
  ------------------------------------------------------
         o      (__)
          o     /oo|
           o   (_"_)*+++++++++*
                    //I#\ \ \ \ \ \ \ \   I \
                   I[I|I | | | | |   I I `
                   I`I ' /  /  /  '  '     I I
                   I I                       I I
                  ~ ~                     ~ ~
                        Scowleton
```

### 带有 fortune 的随机定制牛

可以将复杂的命令链接起来，以产生详细的 ASCII 文字，例如此 [Futurama 示例](https://bambambambam.wordpress.com/2009/07/04/futurama-ascii-with-slashdot-header-quotes-in-your-terminal/)。

- 显示带有随机面部表情的随机牛，并包装长行的 fortune 文本：

> **注意：**随机选择的牛实际上是从具有自定义 ASCII 艺术的数据库中选择的蟾蜍

````
$ fortune -a | fmt -80 -s | $(shuf -n 1 -e cowsay cowthink) -$(shuf -n 1 -e b d g p s t w y) -f $(shuf -n 1 -e $(cowsay -l | tail -n +2)) -n
    ________________________________________
( Fry: I must be a robot. Why else would )
( human women refuse to date me?         )
    --------------------------------------------------
      o
        o
          o
             ,'``.._   ,'``.
            :,--._:)\,:,._,.:
            :`--,@@@:`...';\
             `,'@@@@@@@`---'@@`.
             /@@@@@@@@@@@@@@@@@:
            /@@@@@@@@@@@@@@@@@@@\
          ,'@@@@@@@@@@@@@@@@@@@@@:\.___,-.
         `...,---'``````-..._@@@@|:@@@@@@@\
           (                 )@@@;:@@@@)@@@\  _,-.
            `.              (@@@//@@@@@@@@@@`'@@@@\
             :               `.//@@)@@@@@@)@@@@@,@;
             |`.            _,'/@@@@@@@)@@@@)@,'@,'
             :`.`-..____..=:.-':@@@@@.@@@@@_,@@,'
            ,'\ ``--....-)='    `._,@@\    )@@@'``._
           /@_@`.       (@)      /@@@@@)  ; / \ \`-.'
          (@@@`-:`.     `' ___..'@@_,-'   |/   `.)
           `-. `.`.``-----``--,@@.'
             |/`.\`'        ,',');
                 `         (/  (/
````

## Ponysay

对于完整的 256 色类似于 cowsay 的艺术品，请使用 [ponysay](https://archlinux.org/packages/?name=ponysay)包（3.0 版本具有 422 个小马）。语法是相同的，`$ ponysay *message*` 可以让小马说些话，`ponysay -l` 显示完整的小马列表。要选择显示的小马，请运行 `$ ponysay --pony *x* *"message"*`，其中 `*x*` 是要显示的小马。要创建更多小马，请使用 [util-say-git](https://aur.archlinux.org/packages/util-say-git/)AUR 并将它们分别存储在桌面和 TTY 的 `~/.local/share/ponysay/ponies` 和 `~/.local/share/ponysay/ttyponies/` 中。

## 另请参见

- [Arch Linux 图稿](https://archlinux.org/art/)
- [/etc/issue "2.0" - 我的 ASCII 艺术版本，带有 Arch 徽标和文本](https://bbs.archlinux.org/viewtopic.php?id=50845)

## 1.11　官方仓库

*难度：进阶 ｜ 篇幅：13.5 KB ｜ 所属：关于 Arch*

将要讲到：稳定仓库、testing 仓库、staging repositories、历史背景

[软件仓库](https://en.wikipedia.org/wiki/software_repository)是获取软件包的地方。

Arch Linux **官方软件仓库**包含着基础的和较为流行的软件。他们可通过[Pacman](../../01_安装与包管理/包管理器/Pacman.md)便捷的获取。软件包由[软件包维护者](Arch%20术语.md#软件包维护者)维护。

官方软件仓库中的软件包会持续更新，即老版本将被新版本所替代。Arch 没有主版本的概念：每个软件在上游有新版本时单独更新。每一个软件仓库都是互相联系的，即，软件仓库中的所有软件都是相互兼容的。

## 稳定仓库

### core

core 仓库位于[仓库镜像](镜像源.md)的 `.../core/os/` 目录。

**core**仓库包含着用于以下用途的软件包:

- 启动 Arch 系统。
- [连接网络](../../03_网络与服务/网络/网络配置.md)
- [创建软件包](创建软件包.md)
- 检查、管理和修复所支持的[文件系统](../../02_系统管理与内核/文件系统/文件系统.md)
- 系统安装（例如[openssh](https://archlinux.org/packages/?name=openssh)包）。

以及上述软件包的依赖（不包括[构建软件包所需的依赖](../../01_安装与包管理/包开发/PKGBUILD.md#makedepends)），以及 [base](https://archlinux.org/packages/?name=base)包 [元软件包](../../01_安装与包管理/包管理/元软件包和软件包组.md)。

*core* 仓库对包有着相当严格的质量要求。软件包的更新需要经过开发者和用户的审察批准。对于低使用率的软件，适当的审核足矣：发布关于更新的通知，请求审核，根据更新的重要性在 [core-testing](#core-testing) 仓库暂留至多一周，并在确保无未解决bug报告后，由[软件包维护者](Arch%20术语.md#软件包维护者)审核通过。

> **提示：**如需在没有网络连接的情况下从 *core* （或其他软件仓库）创建本地软件仓库，见 [Pacman/Tips and tricks#Installing packages from a CD/DVD or USB stick](../../01_安装与包管理/包管理器/Pacman_提示和技巧.md#Installing_packages_from_a_CD/DVD_or_USB_stick)

### extra

*extra* 仓库位于您最喜欢的仓库镜像的 `.../extra/os/` 目录。

*extra*包含不适合在*core*仓库的所有软件包，由 [Trusted Users](../团队/软件包维护者.md) 和 Arch 开发者共同维护。 比如：[Xorg](../../05_桌面与图形/X%20服务器/Xorg.md)，[窗口管理器](../../05_桌面与图形/窗口管理器/窗口管理器.md)，[网络浏览器](../../03_网络与服务/网络应用程序/应用程序列表_互联网.md#Web_browsers)，[媒体播放器](../../06_软件与应用/应用程序/应用程序列表_多媒体.md)，程序语言支持[Python](../../07_开发与工具/编程语言/Python.md)和[Ruby](../../07_开发与工具/编程语言/Ruby.md)等等。

### multilib

*multilib* 仓库位于您最喜欢的仓库镜像的 `.../multilib/os/` 目录。

*multilib* 包含着32位的软件和链接库，可以用于在64位系统上运行和构建32位软件，例如 [wine](https://archlinux.org/packages/?name=wine)包 [steam](https://archlinux.org/packages/?name=steam)包 等。

#### 启用multilib

想使用 multilib 仓库，请在`/etc/pacman.conf`文件中取消 `[multilib]` 段落的注释：

```
/etc/pacman.conf
```

```
[multilib]
Include = /etc/pacman.d/mirrorlist
```

然后[更新系统](../../02_系统管理与内核/系统管理/系统维护.md#更新系统)

> **提示：**运行 `pacman -Sl multilib` 来列出在*multilib*仓库里的所有软件包，32位链接库的软件包以 `lib32-` 开头

#### 禁用multilib

运行下面命令可以删除所有从 *multilib* 安装的软件:

```
# pacman -R $(comm -12 <(pacman -Qq | sort) <(pacman -Slq multilib | sort))
```

如果有 gcc-libs 冲突，请重新安装 [gcc-libs](https://archlinux.org/packages/?name=gcc-libs)包和[base-devel](https://archlinux.org/packages/?name=base-devel)包的依赖：

在 `/etc/pacman.conf` 中注释掉 `[multilib]` 段落：

```
/etc/pacman.conf
```

```
#[multilib]
#Include = /etc/pacman.d/mirrorlist
```

然后[更新系统](../../02_系统管理与内核/系统管理/系统维护.md#更新系统)

## testing 仓库

*testing* 仓库的目的是提供一个即将被放入主软件仓库的软件包的集散地。软件包维护者（和普通用户）可以访问并测试这些软件包以确保软件包没有问题。当位于 *testing* 仓库的软件包被测试无问题后，即可被移入主仓库。

不是所有的包都需要经过如此的测试过程，满足如下条件的软件包会进测试仓库：

- 一切需要进入 *core* 仓库的软件包都需要先经过 *core-testing* 仓库
- 更新该软件包可能损坏系统，需要进行测试。
- 会影响到大量其它的软件包，例如 [perl](https://archlinux.org/packages/?name=perl)包 或者 [python](https://archlinux.org/packages/?name=python)包。

大的软件集合，如[GNOME](../../05_桌面与图形/GNOME/GNOME.md) 或 [KDE](../../05_桌面与图形/KDE/KDE.md) 更新时会更新大批软件包，也需要进行测试。

> **注意：**testing 库并不是“最新”软件包的仓库。它的目的是存放一些由于是\[core\]软件集合的一部分或者由于其它方面的问题，可能引起**系统崩溃**的软件包更新。我们**强烈建议**使用 testing 库的用户订阅[arch-dev-public](https://lists.archlinux.org/mailman3/lists/arch-dev-public.lists.archlinux.org/)邮件列表，监视[testing 仓库论坛](https://bbs.archlinux.org/viewforum.php?id=49)，并[报告bug](../Arch%20开发/漏洞报告准则.md)。你也应该考虑加入[Arch测试团队](../团队/Arch%20测试团队.md)

> **警告：**
>
> - 谨慎启用 *core-testing* 仓库，其中的软件包可能损坏系统。只有有足够的经验且知道如何应对问题的用户才可以启用。
> - 请同时启用 *core-testing* 仓库和 *extra-testing* 仓库。如果启用了下文的其他 *testing* 仓库，也需要启用 *core-testing* 和 *extra-testing* 仓库
> - 由于并不是所有的主仓库软件包都在测试仓库中有相应的版本，core 和 extra 主仓库应该保留，并保证相应的测试仓库在主仓库的前面

### core-testing

位于镜像的 `.../core-testing/os/` 目录。

*core-testing* 包含要进入 *core* 仓库的候选软件包。

core-testing 库是唯一可以与其它官方软件仓库有名称冲突的仓库。如果要启用，应该在 `pacman.conf` 文件里把它设置为第一个仓库。

### extra-testing

*extra-testing*库的功能类似 *core-testing* ，不过是为 *extra* 库服务的测试仓库。

### multilib-testing

*multilib-testing* 库的功能类似 *core-testing* ,不过是为 *multilib-testing* 库服务的测试仓库。

### gnome-unstable

[GNOME](../../05_桌面与图形/GNOME/GNOME.md) 桌面环境的下一个稳定版本，在进入主 *extra-testing* 仓库前，会先放到 *gnome-unstable*.

要启用它，在 `/etc/pacman.conf` 中加入:

```
/etc/pacman.conf
```

```
[gnome-unstable]
Include = /etc/pacman.d/mirrorlist
```

*gnome-unstable* 项需要在所有仓库的最上面（保证在 *core-testing* 上面），即：

```
/etc/pacman.conf
```

```
[gnome-unstable]
[core-testing]
[core]
[extra-testing]
[extra]
```

如果有软件包相关的问题，请在 [Arch bug tracker](https://bugs.archlinux.org/) 汇报，其它问题请[上游的 GNOME Gitlab](https://gitlab.gnome.org)汇报。

### kde-unstable

包含 [KDE](../../05_桌面与图形/KDE/KDE.md) Plasma 和应用程序的 *beta* 或*候选发行版本*.

要启用它，将下面内容加入 `/etc/pacman.conf`:

```
/etc/pacman.conf
```

```
[kde-unstable]
Include = /etc/pacman.d/mirrorlist
```

同样地，*kde-unstable* 项需要在所有仓库的最上面（保证在 *core-testing* 上面）。

请[报告](../Arch%20开发/漏洞报告准则.md)遇到的问题。

### 禁用测试仓库

如果要禁用之前启用的测试仓库，应该：

1.  从 `/etc/pacman.conf` 中删除(注释）掉配置
2.  执行 `pacman -Syuu` 将从这些仓库安装的软件还原到主仓库版本。

第二条为可选操作，如果在第一步后碰到任何问题，请先执行此操作。

## staging repositories

> **警告：**不要因为任何原因启用 *staging* 仓库，因为你的系统一定会在更新后损坏。这个仓库仅为后端开发人员使用

该存储库包含损坏的包，并且在一次重建多个包时仅供开发人员使用。 例如，为了重建依赖于新共享库的包，必须首先构建共享库本身并将其上传到临时存储库，以便其他开发人员可以使用。 一旦所有依赖包都被重建，这组包就会被转移到测试或主存储库，以更合适的方式.

详情请见 [staging 仓库发布通知](https://lists.archlinux.org/archives/list/arch-dev-public@lists.archlinux.org/thread/7RNII5TUKV3BJPGGBYKB7DNUK3WI26GZ/)。

## 历史背景

大部分仓库是因为历史原因分开的。原本，当这个发行版只有一小部分用户时，是只有一个软件仓库的，它就是现在的 **core** ──那时候它叫做 **official** 。这个仓库主要存放了Judd Vinet选定的应用程序，当然现在已经不是这样了：它被设计为只包含“每种类型”程序中的一个 ── 一个桌面环境、一个主浏览器等等。

后来有用户不满Judd的选择，由于[ABS系统](Arch%20构建系统.md)使用简单，所以他们就用ABS来创建自己的软件包。这些软件包被放入一个名为**unofficial**的软件仓库，而这个仓库由除Judd以外的开发人员维护。最终，新仓库获得开发人员同样的支持，所以就不再使用 official 和 unofficial 这两个名称了。大约在0.5版本的时候，两个仓库更名为 **current** 和 **extra** 。

在2007.8.1版本发布之后， current 更名为 core ，以免让人误解它包含的内容。如今，这两个仓库在开发人员和社区眼中都是相同分量的。不过 core 还是有些不同的，主要区别在于安装光盘和发布的快照中的软件包都只在 core 当中。这个仓库可以实现一个完整的Linux系统，但并不一定是你想要的Linux系统。

在0.5或者0.6版本的时候，仓库里有大量的软件包没有开发者愿意去维护。于是一位开发人员（Xentac）建立了“受信用户仓库”（Trusted User Repositories），作为存放被信任的用户（TU）自行创建的软件包的仓库。**staging**仓库里的软件包可以被Arch Linux的开发人员选拔入官方仓库，不过除此之外，开发人员和受信用户或多或少还是有所不同的。

就这样过了一段时间，逐渐的受信用户对他们的软件仓库感到厌倦，而非受信用户又期望可以将自己的软件包与大家分享。这导致了[AUR](Arch%20用户软件仓库.md)的出现。慢慢的TU们形成更为严密的组织，如今他们共同维护**community**软件仓库。TU是一个独立于Arch Linux开发人员之外的组织，两者并没太多交流。不过，热门的软件包仍然会偶尔从 community 选拔入 **extra** 。[AUR](https://aur.archlinux.org/)也允许非受信用户提交PKGBUILD给其他用户自愿使用。

在2006年，**core**仓库里未被正确编译的内核[导致许多用户系统崩溃](https://archlinux.org/news/please-avoid-kernel-261614-1/)。之后，“core仓库审核机制”引入：所有\[core\]仓库软件包更新前，必须先在一个叫做 **core-testing** 的仓库进行测试，必须在其他开发者或 [Arch Testing Team](../团队/Arch%20测试团队.md) 成员同意后，软件包才能正式移入 core 。后来， core 中出现一些低使用率的软件，审核机制对它们有所宽松。

2009年末至2010年初，出现了一些新的文件系统，人们希望在安装系统时就使用它们（即纳入 core ）。鉴于 core 从来没有给出明确的界定（只是重要的包，由开发者亲自挑选的）它有了一个更为明确的定义。

2023 年，经过几年的准备工作，Arch 将[后端服务迁移到了 git](https://lists.archlinux.org/archives/list/arch-dev-public@lists.archlinux.org/thread/SHCCCKHLIWPXPOIR5ARNJKSVWL2SSUO7/)，并同时启用了新的仓库布局。在新的布局中，*extra* 包含了之前 *community* 仓库的软件包，testing 仓库分成了 *core-testing* 和 *extra-testing*, *community-testing* 被删除。 [Trusted Users](../团队/软件包维护者.md) 也有了推送软件包到 *extra* 的权限。

## 1.12　AUR 提交准则

*难度：进阶 ｜ 篇幅：14.3 KB ｜ 所属：关于 Arch*

将要讲到：提交软件包、维护软件包、请求

用户可以通过 [AUR](Arch%20用户软件仓库.md) **分享** [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 脚本。AUR 中不包含任何二进制包，仅包含用户上传的 `PKGBUILD`，供其他用户下载使用。这些 `PKGBUILD` 都是非官方的，没有经过完整审查，使用风险自担。

## 提交软件包

> **警告：**在提交前请先熟悉 [Arch 打包准则](../../01_安装与包管理/Arch%20打包准则/Arch%20打包准则.md)及所有的“相关文章”。请**仔细检查**上传的内容是否有误，**违反相应规则**的软件包可能会**不经警告被直接删除**。

如果在反复阅读该章节后仍不清楚软件包或构建/提交流程是否正确，可以将自己的 `PKGBUILD` 贴到 [AUR 邮件列表](https://lists.archlinux.org/mailman3/lists/aur-general.lists.archlinux.org/)、[论坛 AUR 版](https://bbs.archlinux.org/viewforum.php?id=4)、[中文论坛 AUR/ABS/PKGBUILD 版](https://bbs.archlinuxcn.org/viewforum.php?id=29)或 [IRC 频道](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md)，让大家帮您检查。

### 提交软件包的规则

提交软件包时请遵循下列的规则:

- 任何提交的 `PKGBUILD` 都不能编译**已经**位于**官方**二进制软件包**仓库**的程序。可通过[官方软件包数据库](https://archlinux.org/packages/)进行查找，如果在其中发现了任意版本的程序，就**不要**进行提交。如果认为官方仓库的软件包已过期，请标记它。如果它有问题或者缺少功能，请反馈[反馈 bug 报告](../Arch%20开发/漏洞报告准则.md)。

: **唯一的例外**是和官方打包版本相比增加或开启了**新的功能**或者使用了不同的**补丁**。这时需要修改 `pkgname` 来表示新增的功能。例如加入了边栏支持的 GNU screen（`screen`）应该命名为 `screen-sidebar`。此外还需要同时添加 `conflicts=('screen')` 以避免与官方仓库中的包冲突。

- **检查 AUR** 中是否**已有**相同软件包。如果已经有人维护某软件包，可以以评论的形式将更改报告给维护人员。如果软件包无人维护或维护者无反馈，可以按需将其认领并更新。不要创建重复的包。

- 确保您的软件包**有人需要**。在提交前请仔细想想：有人会用这个软件包吗？它非常特别吗？如果不是只有少数人觉得它有用，就提交它。

: AUR 和官方软件仓库是用于放置通用软件和软件相关的内容的，包括：可执行文件、配置文件、软件的在线/离线文档和软件直接使用的媒体数据。

- 不支持 `x86_64` 架构的软件包[不允许上传到 AUR](https://lists.archlinux.org/archives/list/aur-general@lists.archlinux.org/message/GMYC74CRSFH7GGNENEUOODZUPWHOMX7A/)。

- 不要在 AUR `PKGBUILD` 中使用 `replaces`，除非这个软件包将要被重命名（例如当 *Ethereal* 变成 *Wireshark* 时）。如果这个软件包是**已经存在的软件包的另一版本**，使用 `conflicts` （或者如果这个软件包被其他软件需要时，使用 `provides`）。两者的主要区别是：在 pacman 同步（-Sy）之后，如果遇到与 `replaces` 匹配并已经安装的软件包时，pacman 会立刻想去替换它。而 `conflicts` 则在安装软件包时进行替换。

- 请为那些由版本控制系统构建，而不属于任何特定版本的软件包的包名添加合适的后缀，如 `-git`。具体细则见 [VCS 软件打包准则#软件包命名](../../01_安装与包管理/Arch%20打包准则/VCS%20软件打包准则.md#软件包命名)。

- 当源码可获得时，那些**预构建**[可以直接部署](https://en.wikipedia.org/wiki/Deliverable)的二进制包应该加上 `-bin` 后缀， [Java](../../01_安装与包管理/Arch%20打包准则/Java%20打包准则.md#在_Arch_Linux_中打_Java_包) 除外。除此之外，AUR 不应当包含 `makepkg` 创建的二进制包，也不应当包含文件列表。

- 从特定版本的源码构建的包不应该添加后缀。

- 请按照以下格式在 `PKGBUILD` 最上端加入包含当前**维护者**和过去的**贡献者**的信息**注释**，记得对邮件地址进行必要的处理以避免被垃圾邮件骚扰。然后移除所有不必要的行。例如：

> **注意：**对邮件地址进行混淆会导致别人更难联系上你。

: 如果您从其它人接手了一个 `PKGBUILD`，像这样把您的名字和邮件地址加到最上面。
: ```
# Maintainer: Your Name <address at domain dot tld>
```
: 如果在您之前有其他的维护者，请将它们添加到贡献者中。如果你不是初始上传者，也请将其加入贡献者中。如果您是协同维护者，也请加上现在的其他维护者。
: ```
# Maintainer: Your name <address at domain dot tld>
# Maintainer: Other maintainer's name <address at domain dot tld>
# Contributor: Previous maintainer's name <address at domain dot tld>
# Contributor: Original submitter's name <address at domain dot tld>
```

- 为仓库添加 `LICENSE` 文件，建议使用 [0BSD 许可证](https://spdx.org/licenses/0BSD.html)。你可以直接[从 RFC 0040 复制许可证文本](https://rfc.archlinux.page/0040-license-package-sources/#license-text)。缺少许可证或使用与 0BSD 不同许可证的软件包[不允许](https://rfc.archlinux.page/0040-license-package-sources/#aur)被加入到官方软件源中。

### 认证

要向 AUR 写入软件包，用户需要创建一个 [SSH 密钥](../../03_网络与服务/SSH/SSH%20密钥.md)。将公钥导入到用户账户的 *My Account* 一节，然后为 `aur.archlinux.org` 主机配置对应的私钥，例如：

```
~/.ssh/config
```

```
Host aur.archlinux.org
  IdentityFile ~/.ssh/aur
  User aur
```

你应当为 AUR [生成新的密钥](../../03_网络与服务/SSH/SSH%20密钥.md#生成密钥对)，而不是直接使用旧的，以在出问题时直接弃用密钥：

```
$ ssh-keygen -f ~/.ssh/aur
```

> **提示：**在输入公钥时可以通过换行的方式设置添加多个公钥。

### 创建软件包仓库

如果您正在[创建新的软件包](创建软件包.md)，请通过[克隆](../../07_开发与工具/版本控制系统/Git.md#获取_Git_仓库)所需的 [pkgbase](../../01_安装与包管理/包开发/PKGBUILD.md#pkgbase) 的方式建立一个远程 AUR 仓库和本地 [Git](../../07_开发与工具/版本控制系统/Git.md) 仓库。如果软件包还不存在，则会出现以下预料之中的警告：

```
$ git -c init.defaultBranch=master clone ssh://aur@aur.archlinux.org/pkgbase.git
```

```
Cloning into 'pkgbase'...
warning: You appear to have cloned an empty repository.
Checking connectivity... done.
```

> **注意：**如果 `*pkgbase*` 与[被删除](#请求)的软件包相同，那克隆下来的仓库会不为空。

如果您已经有一个软件包了，但不是 Git 仓库的话，需要进行[初始化](../../07_开发与工具/版本控制系统/Git.md#获取_Git_仓库)：

```
$ git -c init.defaultBranch=master init
```

并添加远程 AUR 地址：

```
$ git remote add label ssh://aur@aur.archlinux.org/pkgbase.git
```

然后向远程仓库[拉取](../../07_开发与工具/版本控制系统/Git.md#Working_with_remotes)以在 AUR 进行初始化。

> **提示：**使用[拉取和变基](https://git-scm.com/docs/git-pull#git-pull---rebasefalsetruemergespreserveinteractive)来解决 `*pkgbase*` 匹配到已删除软件包的冲突问题。

### 提交和更新软件包

> **警告：**不想以全局身份提交？记得通过 `git config user.name [...]` 和 `git config user.email [...]` 编辑自己的本地身份！

> **警告：**你的提交会以[全局 Git 名称和邮件地址](../../07_开发与工具/版本控制系统/Git.md#配置)进行署名，且在推送后很难对提交进行修改（[FS#45425](https://bugs.archlinux.org/task/45425)）。如果你想以不同的身份推送到 AUR，可以使用 `git config user.name "..."` 和 `git config user.email "..."` 对单个包进行修改。

当释出新版本的软件时，更新 [pkgver](../../01_安装与包管理/包开发/PKGBUILD.md#pkgver) 或者 [pkgrel](../../01_安装与包管理/包开发/PKGBUILD.md#pkgrel) 变量来提示所有的用户更新。如果仅仅是对 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 的小修改（例如修正笔误），请不要更新这些变量。

不要仅仅为了更新 `pkgver` 就更新 [VCS 软件包](../../01_安装与包管理/Arch%20打包准则/VCS%20软件打包准则.md)。当上游有新的提交时，他们不会被视为过时。只有当有其他变化，比如打包流程发生变化时，才应该进行更新。

无论何时 `PKGBUILD` 的元数据发生变动（例如更新了 `pkgver()`），都需要重新生成 [.SRCINFO](../../01_安装与包管理/包开发/.SRCINFO.md)。否则 AUR 不会显示更新后的版本号。

要上传或者更新一个软件包，*至少*要[添加](../../07_开发与工具/版本控制系统/Git.md#记录更改) `PKGBUILD` 和 `.SRCINFO` 和其它所有新增的或者修改过的辅助文件（例如 [*.install*](../../01_安装与包管理/包开发/PKGBUILD.md#install) 文件或者如[软件包补丁](../../01_安装与包管理/包管理/软件包补丁.md)之类的[本地源码文件](../../01_安装与包管理/包开发/PKGBUILD.md#source)），写上有意义的提交信息并[提交](../../07_开发与工具/版本控制系统/Git.md#记录更改)，最后[推送](../../07_开发与工具/版本控制系统/Git.md#远程分支)这些变动到 AUR 上。

例如:

```
$ makepkg --printsrcinfo > .SRCINFO
$ git add PKGBUILD .SRCINFO
$ git commit -m "useful commit message"
$ git push
```

> **注意：**
>
> - 如果您忘记在最后一次提交中包含 `.SRCINFO`，您可以通过 `git commit --amend` [修改最后一次提交](https://git-scm.com/docs/git-commit#Documentation/git-commit.txt---amend)来使得 AUR 接受您的推送。
> - AUR 只允许推送到 `master` 分支。如果本地分支的名称不同，需要将其[重命名](https://git-scm.com/docs/git-branch)后再次推送。

> **提示：**为了保持工作目录和提交尽可能的干净，可以创建 [gitignore(5)](https://man.archlinux.org/man/gitignore.5) 文件来排除所有文件，然后再按需添加文件。

## 维护软件包

- 阅读其他用户的反馈，并试着听从建议改进软件包，这是个学习的过程！
- 升级软件包后，不要在评论中加入版本更新信息，这些信息会冲淡更重要的用户评论。
- 不要提交软件包后就放任不管！检查更新并改进 `PKGBUILD` 是维护者的责任。
- 发觉自己不再想维护某个软件包？可以通过 AUR 网页界面 `disown` 一个软件包并/或是在 AUR 邮件列表发条消息。如果这个包的所有维护者都 disown，那么它就会变成一个[“orphaned”](https://aur.archlinux.org/packages?submit=Orphans)软件包。
- 自动化对维护人员来说很有用，但它不能取代[人工干预](https://lists.archlinux.org/archives/list/aur-general@lists.archlinux.org/message/AYISVT7SOB444HXHIF2CKA2TIHBESH2D/)（例如：项目可能会修改许可证、添加或删除依赖关系，以及其它的显著更改）。自动更新 `PKGBUILD` 的风险将由您自行承担，任何出现问题的账户与他们提交的软件包都可能被无事前通知地删除。

## 请求

删除、合并、取消维护权限请求可以通过右侧“Package actions”的“File Request”链接执行。这会给当前的维护者和 [aur-requests 邮件列表](https://lists.archlinux.org/mailman3/lists/aur-requests.lists.archlinux.org/)自动发送邮件通知，之后[软件包维护者](../团队/软件包维护者.md)会接受或拒绝请求。

### 删除

你可以请求 *unlist* AUR 中的一个 `pkgbase`。提交时请附上原因说明（务必用英语），也可以是其它有用的细节（比方说其它包提供了相同的东西，或者如果你就是这个包的维护者，需要重命名且软件包所有者同意，等等）。

> **注意：**
>
> - 只在软件包的评论区解释原因并不够。当软件包维护者采取行动前，aur-request 邮件列表是唯一能取得这些信息的地方。
> - 删除请求可能被否决。如果你是这个包的维护者，我们可能会建议你 disown 这个软件包，以便其他维护者认领。
> - 在软件包被“删除”后，其 [Git](../../07_开发与工具/版本控制系统/Git.md) 仓库仍可被[克隆](Arch%20用户软件仓库.md#获取软件包构建所需文件)。

### 合并

合并请求会删除 `pkgbase`，并将现有的投票数、评论转移到另一个 `pkgbase` 中。需要用到目标软件包的名称。

这个操作适用于上游进行了[重命名项目](https://lists.archlinux.org/archives/list/aur-requests@lists.archlinux.org/thread/CLPVKIYDKRCBOEITSWAG4FO5RBKTKGYG/#U73UG4AP6WMUIR23F7OA4ZKNILT6HZNU)等情况。

> **注意：**
>
> - 这和 `git merge` 或者 GitLab 的 merge request 没有任何关系。
> - 由于投票和评论会转移到已经存在的目标中，如果一个包本身没有投票或评论，那么其效果与[#删除](#删除)并[链接到新包](https://lists.archlinux.org/archives/list/aur-requests@lists.archlinux.org/thread/KSXL52EYMKTZXVO3WHWAUFEJI6DQAXV2/#KSXL52EYMKTZXVO3WHWAUFEJI6DQAXV2)完全相同。

### 撤销所有权（orphan）

要求撤销当前维护者对 `pkgbase` 的所有权。如果现有维护者在两周之内没有响应，请求就会通过。另外，如果一个软件被标记为过期超过 180 天，请求会被自动通过。

## 1.13　目录

*难度：进阶 ｜ 篇幅：16.0 KB ｜ 所属：关于 Arch*

[关于 Arch](https://wiki.archlinuxcn.org/wzh/Category:%E5%85%B3%E4%BA%8E_Arch) (5个分类、​18个页面)

[安装过程](https://wiki.archlinuxcn.org/wzh/Category:%E5%AE%89%E8%A3%85%E8%BF%87%E7%A8%8B) (1个分类、​24个页面)

[Arch Linux 中文维基](https://wiki.archlinuxcn.org/wzh/Category:Arch_Linux_%E4%B8%AD%E6%96%87%E7%BB%B4%E5%9F%BA) (1个分类、​6个页面)

[Arch 社区](https://wiki.archlinuxcn.org/wzh/Category:Arch_%E7%A4%BE%E5%8C%BA) (7个页面)

[团队](https://wiki.archlinuxcn.org/wzh/Category:%E5%9B%A2%E9%98%9F) (6个页面)

[Live Arch 系统](https://wiki.archlinuxcn.org/wzh/Category:Live_Arch_%E7%B3%BB%E7%BB%9F) (2个页面)

[Arch 的启动流程](../../01_安装与包管理/启动过程/Arch%20的启动流程.md)

[Arch 构建系统](Arch%20构建系统.md)

[Arch Linux](Arch%20Linux.md)

[Arch Linux 新闻报道](Arch%20Linux%20新闻报道.md)

[Arch Linux 愚人节笑话](Arch%20Linux%20愚人节笑话.md)

[Arch 是最好的！](Arch%20是最好的！.md)

[Arch 术语](Arch%20术语.md)

[Arch 用户软件仓库](Arch%20用户软件仓库.md)

[Arch 与其他发行版的比较](Arch%20与其他发行版的比较.md)

[ASCII 艺术](ASCII%20艺术.md)

[AUR 提交准则](AUR%20提交准则.md)

[参与贡献](参与贡献.md)

[常见问题](常见问题.md)

[创建软件包](创建软件包.md)

[官方仓库](官方仓库.md)

[基于 Arch 的发行版](../../06_软件与应用/列表/基于%20Arch%20的发行版.md)

[镜像源](镜像源.md)

[Makepkg](../../08_安全与加密/OpenPGP/Makepkg.md)

[硬件](https://wiki.archlinuxcn.org/wzh/Category:%E7%A1%AC%E4%BB%B6) (14个分类、​6个页面)

[笔记本电脑](https://wiki.archlinuxcn.org/wzh/Category:%E7%AC%94%E8%AE%B0%E6%9C%AC%E7%94%B5%E8%84%91) (15个分类、​7个页面)

[CPU](https://wiki.archlinuxcn.org/wzh/Category:CPU) (5个页面)

[存储](https://wiki.archlinuxcn.org/wzh/Category:%E5%AD%98%E5%82%A8) (1个分类、​10个页面)

[打印机](https://wiki.archlinuxcn.org/wzh/Category:%E6%89%93%E5%8D%B0%E6%9C%BA) (2个页面)

[蓝牙](https://wiki.archlinuxcn.org/wzh/Category:%E8%93%9D%E7%89%99) (3个页面)

[模块](https://wiki.archlinuxcn.org/wzh/Category:%E6%A8%A1%E5%9D%97) (1个页面)

[声音](https://wiki.archlinuxcn.org/wzh/Category:%E5%A3%B0%E9%9F%B3) (11个页面)

[输入设备](https://wiki.archlinuxcn.org/wzh/Category:%E8%BE%93%E5%85%A5%E8%AE%BE%E5%A4%87) (2个分类、​9个页面)

[数码影像](https://wiki.archlinuxcn.org/wzh/Category:%E6%95%B0%E7%A0%81%E5%BD%B1%E5%83%8F) (1个分类、​2个页面)

[图形](https://wiki.archlinuxcn.org/wzh/Category:%E5%9B%BE%E5%BD%A2) (30个页面)

[显示器](https://wiki.archlinuxcn.org/wzh/Category:%E6%98%BE%E7%A4%BA%E5%99%A8) (1个分类、​1个页面)

[移动设备](https://wiki.archlinuxcn.org/wzh/Category:%E7%A7%BB%E5%8A%A8%E8%AE%BE%E5%A4%87) (1个分类、​3个页面)

[硬件检测和故障排除](https://wiki.archlinuxcn.org/wzh/Category:%E7%A1%AC%E4%BB%B6%E6%A3%80%E6%B5%8B%E5%92%8C%E6%95%85%E9%9A%9C%E6%8E%92%E9%99%A4) (5个页面)

[主板和 BIOS](https://wiki.archlinuxcn.org/wzh/Category:%E4%B8%BB%E6%9D%BF%E5%92%8C_BIOS) (1个页面)

[Fwupd](../../04_硬件与外设/硬件/Fwupd.md)

[基准测试](../../04_硬件与外设/硬件/基准测试.md)

[可信平台模块](../../04_硬件与外设/硬件/可信平台模块.md)

[Linux 固件](../../04_硬件与外设/硬件/Linux%20固件.md)

[性能优化](../../04_硬件与外设/硬件/性能优化.md)

[可信平台模块/1.2](../../04_硬件与外设/硬件/可信平台模块_1.2.md)

[网络](https://wiki.archlinuxcn.org/wzh/Category:%E7%BD%91%E7%BB%9C) (24个分类、​34个页面)

[串流](https://wiki.archlinuxcn.org/wzh/Category:%E4%B8%B2%E6%B5%81) (1个分类、​6个页面)

[代理服务器](https://wiki.archlinuxcn.org/wzh/Category:%E4%BB%A3%E7%90%86%E6%9C%8D%E5%8A%A1%E5%99%A8) (9个页面)

[DNS](https://wiki.archlinuxcn.org/wzh/Category:DNS) (6个页面)

[防火墙](https://wiki.archlinuxcn.org/wzh/Category:%E9%98%B2%E7%81%AB%E5%A2%99) (11个页面)

[分布式计算](https://wiki.archlinuxcn.org/wzh/Category:%E5%88%86%E5%B8%83%E5%BC%8F%E8%AE%A1%E7%AE%97) (1个分类、​4个页面)

[FTP](https://wiki.archlinuxcn.org/wzh/Category:FTP) (1个分类、​3个页面)

[服务器](https://wiki.archlinuxcn.org/wzh/Category:%E6%9C%8D%E5%8A%A1%E5%99%A8) (6个分类、​13个页面)

[互联网应用](https://wiki.archlinuxcn.org/wzh/Category:%E4%BA%92%E8%81%94%E7%BD%91%E5%BA%94%E7%94%A8) (8个分类、​10个页面)

[即时通信](https://wiki.archlinuxcn.org/wzh/Category:%E5%8D%B3%E6%97%B6%E9%80%9A%E4%BF%A1) (2个分类、​9个页面)

[模块](https://wiki.archlinuxcn.org/wzh/Category:%E6%A8%A1%E5%9D%97) (1个页面)

[目录服务](https://wiki.archlinuxcn.org/wzh/Category:%E7%9B%AE%E5%BD%95%E6%9C%8D%E5%8A%A1) (1个页面)

[SSH](https://wiki.archlinuxcn.org/wzh/Category:SSH) (12个页面)

[VPN](https://wiki.archlinuxcn.org/wzh/Category:VPN) (6个页面)

[网络电话](https://wiki.archlinuxcn.org/wzh/Category:%E7%BD%91%E7%BB%9C%E7%94%B5%E8%AF%9D) (1个页面)

[网络服务器](https://wiki.archlinuxcn.org/wzh/Category:%E7%BD%91%E7%BB%9C%E6%9C%8D%E5%8A%A1%E5%99%A8) (8个页面)

[网络共享](https://wiki.archlinuxcn.org/wzh/Category:%E7%BD%91%E7%BB%9C%E5%85%B1%E4%BA%AB) (8个页面)

[网络监控](https://wiki.archlinuxcn.org/wzh/Category:%E7%BD%91%E7%BB%9C%E7%9B%91%E6%8E%A7) (1个页面)

[网络配置](https://wiki.archlinuxcn.org/wzh/Category:%E7%BD%91%E7%BB%9C%E9%85%8D%E7%BD%AE) (1个分类、​6个页面)

[网页](https://wiki.archlinuxcn.org/wzh/Category:%E7%BD%91%E9%A1%B5) (1个页面)

[无线网络](https://wiki.archlinuxcn.org/wzh/Category:%E6%97%A0%E7%BA%BF%E7%BD%91%E7%BB%9C) (7个页面)

[协议](https://wiki.archlinuxcn.org/wzh/Category:%E5%8D%8F%E8%AE%AE) (6个分类)

[邮件服务器](https://wiki.archlinuxcn.org/wzh/Category:%E9%82%AE%E4%BB%B6%E6%9C%8D%E5%8A%A1%E5%99%A8) (5个页面)

[邮件](https://wiki.archlinuxcn.org/wzh/Category:%E9%82%AE%E4%BB%B6) (2个页面)

[远程服务器](https://wiki.archlinuxcn.org/wzh/Category:%E8%BF%9C%E7%A8%8B%E6%9C%8D%E5%8A%A1%E5%99%A8) (5个页面)

[安全](../../08_安全与加密/安全/安全.md)

[Cloud-init](../../02_系统管理与内核/虚拟化/Cloud-init.md)

[Distcc](../../03_网络与服务/分布式计算/Distcc.md)

[DNS-over-HTTPS](../../03_网络与服务/网络/DNS-over-HTTPS.md)

[Drcom](../../03_网络与服务/网络/Drcom.md)

[FreeIPA](../../06_软件与应用/红帽/FreeIPA.md)

[I2P](../../03_网络与服务/网络/I2P.md)

[InfiniBand](../../03_网络与服务/网络/InfiniBand.md)

[IPv6](../../03_网络与服务/网络/IPv6.md)

[Isatapd](../../03_网络与服务/网络/Isatapd.md)

[ISCSI](../../02_系统管理与内核/存储/ISCSI.md)

[ISCSI/LIO](../../02_系统管理与内核/存储/ISCSI_LIO.md)

[进阶网络流量控制](../../03_网络与服务/网络/进阶网络流量控制.md)

[Kerberos](../../08_安全与加密/身份验证/Kerberos.md)

[LDAP 认证](../../08_安全与加密/认证/LDAP%20认证.md)

[MAC 地址欺骗](../../08_安全与加密/安全/MAC%20地址欺骗.md)

[MentoHUST](../../03_网络与服务/网络/MentoHUST.md)

[Netatalk](../../03_网络与服务/网络/Netatalk.md)

[Open-iSCSI](../../02_系统管理与内核/存储/Open-iSCSI.md)

[OpenStack](../../03_网络与服务/网络/OpenStack.md)

[PXE](../../01_安装与包管理/启动过程/PXE.md)

[身份管理](../../08_安全与加密/认证/身份管理.md)

[Sslh](../../03_网络与服务/网络/Sslh.md)

[Surfraw](../../03_网络与服务/搜索/Surfraw.md)

[Systemd-resolved](../../03_网络与服务/网络/Systemd-resolved.md)

[Telnet](../../03_网络与服务/网络/Telnet.md)

[USB/IP](../../02_系统管理与内核/存储/USB_IP.md)

[VLAN](../../03_网络与服务/网络/VLAN.md)

[VnStat](../../03_网络与服务/网络/VnStat.md)

[Wake-on-LAN](../../02_系统管理与内核/电源管理/Wake-on-LAN.md)

[网络配置](../../03_网络与服务/网络/网络配置.md)

[网桥](../../03_网络与服务/网络/网桥.md)

[Wireshark](../../08_安全与加密/安全/Wireshark.md)

[业余无线电](../../03_网络与服务/网络/业余无线电.md)

[软件](https://wiki.archlinuxcn.org/wzh/Category:%E8%BD%AF%E4%BB%B6) (7个分类)

[命令行](https://wiki.archlinuxcn.org/wzh/Category:%E5%91%BD%E4%BB%A4%E8%A1%8C) (3个分类、​5个页面)

[软件比较](https://wiki.archlinuxcn.org/wzh/Category:%E8%BD%AF%E4%BB%B6%E6%AF%94%E8%BE%83) (5个页面)

[软件公司](https://wiki.archlinuxcn.org/wzh/Category:%E8%BD%AF%E4%BB%B6%E5%85%AC%E5%8F%B8) (4个分类)

[软件列表](https://wiki.archlinuxcn.org/wzh/Category:%E8%BD%AF%E4%BB%B6%E5%88%97%E8%A1%A8) (29个页面)

[软件项目](https://wiki.archlinuxcn.org/wzh/Category:%E8%BD%AF%E4%BB%B6%E9%A1%B9%E7%9B%AE) (9个分类)

[文件格式](https://wiki.archlinuxcn.org/wzh/Category:%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F) (2个页面)

[应用程序](https://wiki.archlinuxcn.org/wzh/Category:%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F) (23个分类、​7个页面)

[系统管理](https://wiki.archlinuxcn.org/wzh/Category:%E7%B3%BB%E7%BB%9F%E7%AE%A1%E7%90%86) (24个分类、​18个页面)

[安全](https://wiki.archlinuxcn.org/wzh/Category:%E5%AE%89%E5%85%A8) (11个分类、​30个页面)

[包管理](https://wiki.archlinuxcn.org/wzh/Category:%E5%8C%85%E7%AE%A1%E7%90%86) (3个分类、​21个页面)

[备份](https://wiki.archlinuxcn.org/wzh/Category:%E5%A4%87%E4%BB%BD) (5个页面)

[本地化](https://wiki.archlinuxcn.org/wzh/Category:%E6%9C%AC%E5%9C%B0%E5%8C%96) (1个分类、​7个页面)

[电源管理](https://wiki.archlinuxcn.org/wzh/Category:%E7%94%B5%E6%BA%90%E7%AE%A1%E7%90%86) (1个分类、​14个页面)

[多媒体](https://wiki.archlinuxcn.org/wzh/Category:%E5%A4%9A%E5%AA%92%E4%BD%93) (4个分类、​17个页面)

[Freedesktop.org](https://wiki.archlinuxcn.org/wzh/Category:Freedesktop.org) (8个页面)

[国际化](https://wiki.archlinuxcn.org/wzh/Category:%E5%9B%BD%E9%99%85%E5%8C%96) (空)

[建议阅读](https://wiki.archlinuxcn.org/wzh/Category:%E5%BB%BA%E8%AE%AE%E9%98%85%E8%AF%BB) (4个页面)

[脚本](https://wiki.archlinuxcn.org/wzh/Category:%E8%84%9A%E6%9C%AC) (空)

[Linux 控制台](https://wiki.archlinuxcn.org/wzh/Category:Linux_%E6%8E%A7%E5%88%B6%E5%8F%B0) (4个页面)

[命令行](https://wiki.archlinuxcn.org/wzh/Category:%E5%91%BD%E4%BB%A4%E8%A1%8C) (3个分类、​5个页面)

[内核](https://wiki.archlinuxcn.org/wzh/Category:%E5%86%85%E6%A0%B8) (27个页面)

[配置文件](https://wiki.archlinuxcn.org/wzh/Category:%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6) (5个页面)

[启动过程](https://wiki.archlinuxcn.org/wzh/Category:%E5%90%AF%E5%8A%A8%E8%BF%87%E7%A8%8B) (4个分类、​17个页面)

[日志记录](https://wiki.archlinuxcn.org/wzh/Category:%E6%97%A5%E5%BF%97%E8%AE%B0%E5%BD%95) (空)

[输入](https://wiki.archlinuxcn.org/wzh/Category:%E8%BE%93%E5%85%A5) (2个分类)

[同步](https://wiki.archlinuxcn.org/wzh/Category:%E5%90%8C%E6%AD%A5) (1个分类、​2个页面)

[图形用户界面](https://wiki.archlinuxcn.org/wzh/Category:%E5%9B%BE%E5%BD%A2%E7%94%A8%E6%88%B7%E7%95%8C%E9%9D%A2) (5个分类、​4个页面)

[文件分享](https://wiki.archlinuxcn.org/wzh/Category:%E6%96%87%E4%BB%B6%E5%88%86%E4%BA%AB) (2个页面)

[文件系统](https://wiki.archlinuxcn.org/wzh/Category:%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F) (4个分类、​41个页面)

[系统恢复](https://wiki.archlinuxcn.org/wzh/Category:%E7%B3%BB%E7%BB%9F%E6%81%A2%E5%A4%8D) (13个页面)

[小提升](https://wiki.archlinuxcn.org/wzh/Category:%E5%B0%8F%E6%8F%90%E5%8D%87) (2个分类、​12个页面)

[虚拟化](https://wiki.archlinuxcn.org/wzh/Category:%E8%99%9A%E6%8B%9F%E5%8C%96) (1个分类、​18个页面)

[Anything-sync-daemon](../../02_系统管理与内核/系统管理/Anything-sync-daemon.md)

[Cron](../../02_系统管理与内核/系统管理/Cron.md)

[D-Bus](../../02_系统管理与内核/系统管理/D-Bus.md)

[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)

[IPMI](../../02_系统管理与内核/系统管理/IPMI.md)

[默认应用程序](../../02_系统管理与内核/系统管理/默认应用程序.md)

[屏幕捕获](../../06_软件与应用/多媒体/屏幕捕获.md)

[Preload](../../02_系统管理与内核/系统管理/Preload.md)

[Scx-scheds](../../02_系统管理与内核/系统管理/Scx-scheds.md)

[设备文件](../../02_系统管理与内核/系统管理/设备文件.md)

[Systemd/定时器](../../02_系统管理与内核/系统管理/Systemd_定时器.md)

[Systemd/用户](../../02_系统管理与内核/系统管理/Systemd_用户.md)

[调试](../../02_系统管理与内核/系统管理/调试.md)

[Windows PE](../../02_系统管理与内核/系统管理/Windows%20PE.md)

[系统时间](../../04_硬件与外设/主板和%20BIOS/系统时间.md)

[系统维护](../../02_系统管理与内核/系统管理/系统维护.md)

[性能优化](../../04_硬件与外设/硬件/性能优化.md)

[自动启动](../../02_系统管理与内核/系统管理/自动启动.md)

[开发](https://wiki.archlinuxcn.org/wzh/Category:%E5%BC%80%E5%8F%91) (6个分类、​27个页面)

[版本控制系统](https://wiki.archlinuxcn.org/wzh/Category:%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6%E7%B3%BB%E7%BB%9F) (1个分类、​5个页面)

[编程语言](https://wiki.archlinuxcn.org/wzh/Category:%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80) (2个分类、​19个页面)

[电子设计自动化](https://wiki.archlinuxcn.org/wzh/Category:%E7%94%B5%E5%AD%90%E8%AE%BE%E8%AE%A1%E8%87%AA%E5%8A%A8%E5%8C%96) (1个页面)

[控件工具集](https://wiki.archlinuxcn.org/wzh/Category:%E6%8E%A7%E4%BB%B6%E5%B7%A5%E5%85%B7%E9%9B%86) (3个页面)

[框架](https://wiki.archlinuxcn.org/wzh/Category:%E6%A1%86%E6%9E%B6) (1个分类、​4个页面)

[问题跟踪系统](https://wiki.archlinuxcn.org/wzh/Category:%E9%97%AE%E9%A2%98%E8%B7%9F%E8%B8%AA%E7%B3%BB%E7%BB%9F) (3个页面)

[Android](../../04_硬件与外设/Android/Android.md)

[Apache Kafka](../../06_软件与应用/Apache/Apache%20Kafka.md)

[Arduino](../../06_软件与应用/科学/Arduino.md)

[Distcc](../../03_网络与服务/分布式计算/Distcc.md)

[Eclipse](../../07_开发与工具/开发/Eclipse.md)

[Flatpak](../../07_开发与工具/开发/Flatpak.md)

[GNU Compiler Collection](../../06_软件与应用/GNU/GNU%20Compiler%20Collection.md)

[GPGPU](../../07_开发与工具/开发/GPGPU.md)

[核心转储](../../07_开发与工具/开发/核心转储.md)

[LLVM](../../07_开发与工具/开发/LLVM.md)

[Node.js](../../07_开发与工具/开发/Node.js.md)

[Ollama](../../07_开发与工具/开发/Ollama.md)

[OpenClaw](../../06_软件与应用/人工智能/OpenClaw.md)

[OpenGL](../../07_开发与工具/开发/OpenGL.md)

[User:PovShell/Sandbox](https://wiki.archlinuxcn.org/wzh/User:PovShell/Sandbox)

[Python/虚拟环境](../../07_开发与工具/开发/Python_虚拟环境.md)

[Rbenv](../../07_开发与工具/开发/Rbenv.md)

[SDL](../../07_开发与工具/开发/SDL.md)

[Snap](../../07_开发与工具/开发/Snap.md)

[调试/获取跟踪信息](../../01_安装与包管理/包开发/调试_获取跟踪信息.md)

[调试](../../02_系统管理与内核/系统管理/调试.md)

[Vulkan](../../07_开发与工具/开发/Vulkan.md)

[Xc3sprog](../../07_开发与工具/开发/Xc3sprog.md)

[XDG 基本目录](../../01_安装与包管理/配置文件/XDG%20基本目录.md)

[语言服务器协议](../../07_开发与工具/编程语言/语言服务器协议.md)

[桌面通知程序](../../05_桌面与图形/通知/桌面通知程序.md)

[Project:沙盒](https://wiki.archlinuxcn.org/wzh/Project:%E6%B2%99%E7%9B%92)

[列表](https://wiki.archlinuxcn.org/wzh/Category:%E5%88%97%E8%A1%A8) (1个分类、​5个页面)

[软件列表](https://wiki.archlinuxcn.org/wzh/Category:%E8%BD%AF%E4%BB%B6%E5%88%97%E8%A1%A8) (29个页面)

[非官方镜像源](../../06_软件与应用/列表/非官方镜像源.md)

[非官方用户仓库](../../01_安装与包管理/非官方用户仓库/非官方用户仓库.md)

[国际社区](../../06_软件与应用/列表/国际社区.md)

[基于 Arch 的发行版](../../06_软件与应用/列表/基于%20Arch%20的发行版.md)

[文件系统](../../02_系统管理与内核/文件系统/文件系统.md)

## 1.14　创建软件包

*难度：进阶 ｜ 篇幅：19.1 KB ｜ 所属：关于 Arch*

将要讲到：概述、准备工作、创建 PKGBUILD、测试 PKGBUILD 文件和软件包、把包提交给 AUR、总结

本文旨在帮助用户利用类似于 [ports](https://zh.wikipedia.org/wiki/Ports) 的 [Arch 构建系统](Arch%20构建系统.md)来创建自己的软件包，以及如何提交到 [Arch 用户软件仓库](Arch%20用户软件仓库.md)。本文讲述了如何创建 [PKGBUILD(5)](https://man.archlinux.org/man/PKGBUILD.5)，这是一个描述如何构建软件包的文件，[makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 会使用它来从源代码创建二进制软件包。

关于构建规则和提高软件包质量的方法，请参考 [Arch 打包准则](../../01_安装与包管理/Arch%20打包准则/Arch%20打包准则.md)。

## 概述

Arch Linux 中的软件包是通过 *makepkg* 工具以及存储在 `PKGBUILD` 文件中的信息编译的。运行 *makepkg* 时，它会自动在当前目录下搜索 `PKGBUILD` 文件，然后根据其中的信息下载文件，按需把软件源码重新编译打包为一个 `*[pkgname](../../01_安装与包管理/包开发/PKGBUILD.md#pkgname)*.pkg.tar.zst` 软件包文件。软件包文件中包含了 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 安装需要的二进制文件和安装指令。

一个 Arch 软件包就是一个使用 [zstd(1)](https://man.archlinux.org/man/zstd.1) 压缩的 [tar(1)](https://man.archlinux.org/man/tar.1) 压缩包，或者叫 [tarball](../../02_系统管理与内核/数据压缩与归档/归档与压缩.md#仅归档)。它包含了以下由 *makepkg* 生成的文件：

- 要安装的二进制文件；

- `.PKGINFO`: 包含所有 pacman 处理软件包所需的元数据、依赖等等；

- `.BUILDINFO`: 包含可复现编译需要的信息，参阅 [BUILDINFO(5)](https://man.archlinux.org/man/BUILDINFO.5) ；

- `.MTREE`: 包含了文件的哈希值与时间戳，pacman 能够根据这些储存在本地数据库的信息校验软件包的完整性；

- `.INSTALL`: 可选的文件，可以用来在安装/升级/删除操作之后运行命令。(本文件只有在 `PKGBUILD` 中制定才会存在)；

- `.Changelog`: 一个可选的文件，保存了包管理员描述软件变更的日志。

## 准备工作

### 必需的软件包

首先，确定你已安装必须的工具包。安装 [base-devel](https://archlinux.org/packages/?name=base-devel)包 [元软件包](../../01_安装与包管理/包管理/元软件包和软件包组.md)应当足够；它会引入 [make(1)](https://man.archlinux.org/man/make.1) 和其它一些编译源码时需要的工具。

创建包的一个关键工具是 *makepkg*（由 [pacman](https://archlinux.org/packages/?name=pacman)包 提供），它的功能请参阅 [Arch 打包准则#Makepkg 的任务](../../01_安装与包管理/Arch%20打包准则/Arch%20打包准则.md#Makepkg_的任务)。

### 下载并测试安装

下载你想打包的软件的源代码压缩包，解压，按照作者所说的步骤安装它。记录下在编译和安装软件过程中需要的所有命令或步骤。你将要在 `PKGBUILD` 文件中重复这些命令和步骤。

大多数软件作者遵循三步走的安装惯例：

```
$ ./configure
$ make
# make install
```

建议在这时测试软件的功能是否正常。

### 创建一个干净的 chroot

建议参考[创建一个干净的 chroot](../Arch%20开发/创建一个干净的%20chroot.md) 来保证你的系统中的其它软件包和配置不会影响 `PKGBUILD`。这个方法容错性更强，是一个更加恰当的构建软件包的方法，通常能够发现那些你未曾意识到需要的依赖项，因为它们已经在你的系统中存在。

## 创建 PKGBUILD

当你运行 `makepkg` 时，它会在当前工作目录寻找一个 `PKGBUILD` 文件。如果找到 `PKGBUILD` 文件，它会下载该软件的源代码，根据 `PKGBUILD` 文件中的指令编译它。PKGBUILD 中的指令必须能完全被 [Bash](../../07_开发与工具/命令行解释器/Bash.md) 解释。

编译成功后，最后的二进制文件和包的元信息（即包的版本、依赖）被一起打包在 `*pkgname*.pkg.tar.zst` 软件包文件中，这个文件包可以通过 `makepkg --install` 调用 pacman 或直接使用 `pacman -U *<package file>*` 来安装。

要开始制作一个包，你应该先创建一个空工作目录，进入该目录，创建一个 `PKGBUILD` 文件。你可以复制 `PKGBUILD` 模板（位于 /usr/share/pacman/）到工作目录，或者复制一个类似包的 `PKGBUILD` 也可以。如果你只想在别人的基础上更改一些选项的话，后一种方法比较方便。

### PKGBUILD 变量

> **注意：**主页面为 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md)，另外也请参考 [PKGBUILD(5) § OPTIONS AND DIRECTIVES](https://man.archlinux.org/man/PKGBUILD.5#OPTIONS_AND_DIRECTIVES)。

*makepkg* 预定义了以下变量，打包者需使用它们指代构建过程的临时路径：

**`srcdir`**
: *makepkg*将会把源文件解压到此文件夹，或在此文件夹中生成指向 source 数组中文件的软连接。
**`pkgdir`**
: *makepkg*会把该文件夹当成系统根目录，并将软件安装在此文件夹下。

这些变量都是*绝对路径*，即意味着只要你合适地使用这些变量，就不用担心当前工作目录的影响.

> **注意：***makepkg* 及延伸到 `build()` 和 `package()` 函数在运行过程中都应当是非交互的。在这些函数中调用交互工具或脚本可能会中断 *makepkg* 的运行，特别是启用了构建日志的时候（`--log`）。具体信息请参考 [FS#13214](https://bugs.archlinux.org/task/13214)。

### PKGBUILD 函数

在构建软件包时，`makepkg` 会在 `PKGBUILD` 中有定义的情况下调用以下五个函数。`PKGBUILD` 中必须包含 `package()` 函数并总会被调用。对于其它函数，如果其未被定义，那么 `makepkg` 会将其略过。

在构建时，函数将按下文列出的顺序执行。

另请参考 [PKGBUILD(5) § PACKAGING FUNCTIONS](https://man.archlinux.org/man/PKGBUILD.5#PACKAGING_FUNCTIONS)。

#### prepare()

此函数会执行用于预处理源文件以进行构建的命令，例如 patching。此函数执行在 [pkgver()](#pkgver()) 之前，软件包解压之后。如果解压过程被跳过（`makepkg --noextract`），那么 `prepare()` 函数就不会被执行。

> **注意：** (从 [PKGBUILD(5)](https://man.archlinux.org/man/PKGBUILD.5)) 中可以知道，该函数运行在 `bash -e` 模式下，意味着任何以非零状态退出的命令都会造成该函数中止.

如果不清楚应该将操作放到 `prepare()` 还是 `build()`，只需要记得 `prepare()` 中的步骤应在解压后仅执行一次，而 `build()` 中的步骤应在解压后文件被修改时都执行一次。

#### pkgver()

`pkgver()` 会在抓取并解压源文件，执行 [prepare()](#prepare()) 后执行此函数，可用于在 makepkg 阶段中更新 pkgver 变量。

[为使用 git/svn/hg 等工具的项目打包](../../01_安装与包管理/Arch%20打包准则/VCS%20软件打包准则.md)时，由于它们的构建过程相同，但源文件可能每天甚至每小时更新一次，这一特性将会十分有用。过去的方法是把日期写入到 pkgver 变量中，但这样一来 makepkg 会在即使软件没有更新的情况下依然重新构建软件包，因为它会认为软件包的版本改变了。其他与此有关的命令有 `git describe`，`hg identify -ni` 等等。请在提交 PKGBUILD 前做好测试，因为如果 `pkgver()` 执行失败，整个构建过程都会终止。

> **注意：**pkgver 不能含有空格或连接符 (`-`)，通常可以用 sed 来进行修正。

#### build()

现在你需要编写 `PKGBUILD` 文件中的 `build()` 函数。这个函数使用 [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) 语法的通用 shell 命令来自动编译软件，并创建名为 `pkg` 的软件安装目录。这允许 *makepkg* 无需详查你的文件系统就可以打包你的软件。

在 `build()` 函数中第一步就是进入由解压源码包所生成的目录。*makepkg* 会在执行 `build()` 函数之前将当前活动目录设为为 `$srcdir`；因此，大多数情况下第一条命令是这样的：(参考示例文件 `/usr/share/pacman/PKGBUILD.proto`）：

```
cd "$pkgname-$pkgver"
```

现在，你需要把你当时手动编译软件时用到的命令一一列上。`build()` 基本上会自动运行你当时手动输入的命令，并在伪 root 环境下编译该软件。如果要打包的软件使用了一个配置脚本，最好在配置中加上 `--prefix=/usr`。许多软件都将自己安装到 `/usr/local` 下，但你只应在手动从源码安装时这么做。所有的 Arch Linux 软件包都应当使用 `/usr` 目录。文件中接下来的两行应当如下（参考 `/usr/share/pacman/PKGBUILD.proto`）：

```
./configure --prefix=/usr
make
```

> **注意：**如果你的软件不需要构建任何东西，请不要使用 `build()` 函数，但 `package()` 函数依然是必须的。

#### check()

用于执行 `make check` 和其它一些例行测试。强烈建议使用 `check()` 确保软件正常构建，并能搭配依赖正常使用。

如果不需要或是维护者无法使软件包通过测试，可以给 `makepkg` 传入 `--nocheck` 参数来禁用它。

如果要测试的是 GUI 图形化应用，可以[在虚拟 xserver 中运行测试](../../05_桌面与图形/Xorg%20命令/Xinit.md#Running_in_a_virtual_server)。

#### package()

最后一步是将编译好的文件放到一个目录下，让 *makepkg* 可以获取并打包为软件包。默认情况下该目录为 `pkg`——一个简单的伪 root 环境。`pkg` 目录复制了根目录下软件安装路径的继承关系，如果你需要手动把文件放到根目录下，那么需要把文件放在 `pkg` 下相同的文件层级结构中。比如，你想把一个文件安装到 `/usr/bin`，那么在伪 root 环境中对应的路径为 `$pkgdir/usr/bin`。安装步骤通常不需要用户手动复制大量文件到某个地方，而只需调用 `make install` 即可。为了将软件正确地安装到 `pkg` 路径下，最后一行一般应该这样写：

```
make DESTDIR="$pkgdir" install
```

> **注意：**有时候在 `Makefile` 里没有使用`DESTDIR`；你可能需要使用 `prefix` 来替代。如果软件包是用 *autoconf*/*automake* 来构建的，那就需根据[文档](https://www.gnu.org/software/automake/manual/automake.html#Install)使用 `DESTDIR`；如果 `DESTDIR` 不起作用，可以试试 `make prefix="$pkgdir/usr" install`。如果这还不起作用的话，你就需要深入检查 `make <...> install` 具体执行的安装命令了。

`makepkg --repackage` 命令只运行 `package()` 函数,它只是将文件打包成软件包，并不运行编译过程。如果你只是更改了 `depends` 依赖变量，用这个命令来打包可以节省很多时间。

> **注意：**使用类似 `mv` 的命令从 `$srcdir` 移动由 `build()` 生成的文件到 `$pkgdir` 会破坏 *makepkg* 的 `--repackage` 选项。

## 测试 PKGBUILD 文件和软件包

在编写 `build()` 函数时，需要频繁测试更改以确保没有 bug。你可以在包含 `PKGBUILD` 的目录下运行 `makepkg` 命令来确保没有问题。如果 `PKGBUILD` 没有错误，将会生成一个软件包；但是如果 `PKGBUILD` 有问题或未完成，它将抛出一个错误。

如果运行 `makepkg` 成功，在你的工作目录下将会生成一个名为 `*pkgname-pkgver*.pkg.tar.zst` 的新文件，可以使用 `pacman -U` 进行安装。注意，成功生成了软件包不代表软件能正常运行，它有可能仅包含有空目录，也可能包含如不正确的前缀等问题。你可以使用 pacman 的 `pacman -Qlp *pkgname*` 查询软件包的文件清单，以及通过 `pacman -Qip *pkgname*` 检查依赖项是否正确。

如果包看起来是正确的，那你的工作就完成了。但是如果你打算发布这个 `PKGBUILD` 文件，你就需要确认确认再确认 `depends` 数组中的依赖是否正确。

另外也要确保包二进制文件完全正常地*运行*！如果发布出去的软件包已涵盖所有必要文件，但仅因某些选项与其他系统不兼容而崩溃会非常令人讨厌。如果你只是为自己的系统编译这个软件，就不必做这个质量保证了，因为只有你一个人需要忍受这些错误。

### 检查包的逻辑性

确定包可以正常使用后，再使用[namcap](../Arch%20项目/Namcap.md)来检查错误：

```
$ namcap PKGBUILD
$ namcap pkgname.pkg.tar.zst
```

*Namcap* 将会做以下工作：

- 检查 `PKGBUILD` 里的一些常见错误，以及包文件结构是否有不必要或错位的文件
- 用 `ldd` 扫描包中所有的 [ELF](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) 文件，自动报告 `depends` 中缺失或可去除的依赖
- 启发式搜寻缺失或冗余的依赖

等等。

要养成用 *namcap* 检查包的习惯，以避免提交包后再做修复的麻烦。

### 使用 pkgctl 在干净的 chroot 环境中进行构建

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[Arch 构建系统#构建软件包](Arch%20构建系统.md#构建软件包)。**
>
> **附注：** 本文与构建系统无关，且与 [#创建一个干净的 chroot](#创建一个干净的_chroot) 和 DeveloperWiki:Building in a clean chroot 重复。（在 [Talk:创建软件包](https://wiki.archlinuxcn.org/wiki/Talk:%E5%88%9B%E5%BB%BA%E8%BD%AF%E4%BB%B6%E5%8C%85) 中讨论）

可以使用 [devtools](https://archlinux.org/packages/?name=devtools)包 的 `pkgctl` 来检查软件包是否可以在无其它包的环境中正常构建。在包含 PKGBUILD 的目录下执行：

```
$ pkgctl build
```

然后检查输出是否包含错误或警告。如果软件包依赖于 AUR 包，则需要事先构建并带入 chroot jail 中：

```
$ pkgctl build -I path/to/somepkg.tar.gz -I ...
```

> **注意：**如果包之前是通过 AUR 助手安装的，那很可能缓存目录中已经有其 tarball 了。

更多选项请参考 [pkgctl-build(1)](https://man.archlinux.org/man/pkgctl-build.1)。

## 把包提交给 AUR

请参考 [AUR 提交准则](AUR%20提交准则.md)，里面详细介绍了提交流程。

## 总结

- 下载你希望打包的软件源代码
- 试着编译安装包到任意目录
- 复制文件模板 `/usr/share/pacman/PKGBUILD.proto` 到一个临时目录，并重命名为 `PKGBUILD`
- 根据需要修改 `PKGBUILD` 文件
- 运行 `makepkg` 看看输出的打包结果是否正确
- 如果不正确，重复前两个步骤

### 注意事项

- 除非你“很清楚”你正在做什么，否则在开始自动打包*之前*，需确保你至少已成功手动打包一次。不幸的是，虽然大多数软件作者遵循了三步走的安装惯例：`./configure`; `make`; `make install`，但事情并不都是这样的，有时候你不得不自己打补丁才能安装成功。经验是：如果你手动无法编译成功或者无法将软件安装到指定子目录下，那你就不必费心打包了。`makepkg`没有任何魔力能消除源代码的问题让你编译成功。

- 在少数情况下，软件包不提供源码，而只能使用类似 `sh installer.run` 的命令进行安装，这时就需要你自己做很多工作了（比如查看 README，安装指导，手册，或者 Gentoo 或其它软件包安装器的 ebuild 等等）。在一些很变态的情况下，你需要自己编辑源码才能正常安装。但是，`makepkg` 需要完全自主运行，不能有用户的干预。因此，如果你想修改 makefiles，需要随 `PKGBUILD` 附上一个定制的补丁，然后在 `prepare()` 函数里安装这个补丁；或者你可以在 `prepare()` 函数里通过 `sed` 来修改。

## 自动化

### 校验和

可以使用 `updpkgsums` 工具简化为新版本软件更新校验和的工作，具体信息请参考 [Makepkg#生成新校验和](../../08_安全与加密/OpenPGP/Makepkg.md#生成新校验和)。

### PKGBUILD 生成器

某些软件包的 `PKGBUILD` 可以通过工具自动生成。

> **注意：**用户需要在提交文件到 [AUR](Arch%20用户软件仓库.md) 前确保软件包满足高质量标准。

- Haskell：[cblrepo](https://github.com/magthe/cblrepo)，[arch-hs](https://archlinux.org/packages/?name=arch-hs)包
- [Node.js](../../07_开发与工具/开发/Node.js.md)：[nodejs-npm2arch](https://aur.archlinux.org/packages/nodejs-npm2arch/)AUR
- Perl：[perl-cpanplus-dist-arch](https://archlinux.org/packages/?name=perl-cpanplus-dist-arch)包
- [Python](../../07_开发与工具/编程语言/Python.md)：[pipman-git](https://aur.archlinux.org/packages/pipman-git/)AUR，[pip2arch-git](https://aur.archlinux.org/packages/pip2arch-git/)AUR，[python-pypi2pkgbuild](https://aur.archlinux.org/packages/python-pypi2pkgbuild/)AUR
- [Ruby](../../07_开发与工具/编程语言/Ruby.md)：[gem2arch](https://aur.archlinux.org/packages/gem2arch/)AUR，[pacgem](https://aur.archlinux.org/packages/pacgem/)AUR
- [Rust](../../07_开发与工具/编程语言/Rust.md)：[cargo-pkgbuild](https://aur.archlinux.org/packages/cargo-pkgbuild/)AUR，[cargo-aur-bin](https://aur.archlinux.org/packages/cargo-aur-bin/)AUR

### 新的上游版本发布

来自 [devtools](https://archlinux.org/packages/?name=devtools)包 的 *pkgctl* 以 `.nvchecker.toml` 配置文件的形式支持 [nvchecker](https://github.com/lilydjwg/nvchecker) 集成，该文件需与 `PKGBUILD` 位于同一目录下。具体示例请参考 pacman 包的 [.nvchecker.toml](https://gitlab.archlinux.org/archlinux/packaging/packages/pacman/-/blob/main/.nvchecker.toml?ref_type=heads) 配置文件。

> **提示：**`pkgctl version setup` 会通过分析 PKGBUILD 的 source 数组尝试自动创建 `.nvchecker.toml` 配置文件。

完成配置后，可以使用 `pkgctl version check` 比较 `PKGBUILD` 中的 `pkgver` 来检查上游是否发布了新版本，之后可以使用 `pkgctl version upgrade` 更新 `PKGBUILD`。详细信息请参考 [pkgctl-version(1)](https://man.archlinux.org/man/pkgctl-version.1)。

## 参考

- [如何正确创建补丁文件](https://bbs.archlinux.org/viewtopic.php?id=91408)
- [Arch Linux Classroom IRC 上关于如何创建 PKGBUILD 的教学日志](https://github.com/archwomen/classroom-media/tree/master/classlogs)
- [使用 fakeroot 方式安装软件包](https://www.linuxfromscratch.org/hints/downloads/files/fakeroot.txt)

## 1.15　参与贡献

*难度：高级 ｜ 篇幅：19.9 KB ｜ 所属：关于 Arch*

将要讲到：社区、软件包、活动、软件项目、非官方项目、怎样才能成为 Arch 开发者?

在进化生物学中，[合作](https://en.wikipedia.org/wiki/Co-operation_(evolution))是指个体付出一个小成本，可以为其他同胞带来更大的收益，而其他个体也一样提供回报，这样每个参与者都会获得非常大的收益。Arch 社区也是如此，很多成员希望为自己喜爱的 GNU/Linux 发行版做出贡献。大家的参与，不仅有利于 Archer 同胞，还能造福[自由软件和开源软件](https://zh.wikipedia.org/wiki/%E8%87%AA%E7%94%B1%E5%8F%8A%E5%BC%80%E6%94%BE%E6%BA%90%E4%BB%A3%E7%A0%81%E8%BD%AF%E4%BB%B6)的全体爱好者。

本文简单介绍了 Archer 为 Arch 社团做贡献的若干途径。开始贡献前请先熟悉一下[行为准则](../Arch%20社区/行为准则.md)。

## 社区

### 论坛发帖

参与我们的一个最简单的方法，就是逛 [Arch Linux 官方论坛](https://bbs.archlinux.org/)。您可以尽情发表您的高见、帮助菜鸟或结交新朋友。

### 完善维基

ArchWiki 是一个**社区**文档工程。内容**源自**社区，**服务**社区。贡献者奉献他们的时间和精力，在社区中共享知识和技能。我们鼓励**全体**用户[做贡献](https://wiki.archlinuxcn.org/wzh/Project:Contributing)。

### 加入聊天室

可以在 [Arch 的 IRC 频道](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md)中帮助新人解决问题。进入前请先阅读[讨论规则](../Arch%20社区/一般准则.md#IRC)。[有许多频道](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md)专门用来讨论不同的问题。

### 加入邮件列表

加入感兴趣的[邮件列表](https://lists.archlinux.org/mailman3/lists/)。注意讨论的内容要遵守列表描述。

### 艺术

共享屏保、启动屏幕、调色盘、主题等，可以加入[艺术子论坛](https://bbs.archlinux.org/viewforum.php?id=47)以及中文论坛的[美工与截图](https://bbs.archlinuxcn.org/viewforum.php?id=32)版块。

参阅 [Arch Linux Art](https://archlinux.org/art/) 和 [ASCII 艺术](ASCII%20艺术.md)。

## 软件包

### 报告安装的软件

[pkgstats](../../01_安装与包管理/包管理/Pkgstats.md) 使用 [systemd 定时器](../../02_系统管理与内核/系统管理/Systemd_定时器.md)定期将系统中安装的软件上报服务器，同时还能提供系统架构和使用镜像的信息。开发者可以通过数据统计，安排自己工作的优先级。信息是匿名发送，不会暴露用户隐私。可以通过[统计页面](https://pkgstats.archlinux.de/packages)查看信息。详情请阅读[这个论坛帖子](https://bbs.archlinux.org/viewtopic.php?id=105431)。

### 报告和修正 bug

您还可以通过到 [Arch Linux Bugtracker](https://gitlab.archlinux.org/archlinux/packaging/packages+GitLab) 汇报有关 Arch 软件包 bug 来帮忙。

但若汇报的问题无意义时，反而会浪费大家的时间。如有意参与其中，请先阅读[漏洞报告准则](../Arch%20开发/漏洞报告准则.md)和 [General guidelines#Packaging merge requests](https://wiki.archlinux.org/title/General_guidelines#Packaging_merge_requests)。

### 安全问题

新的漏洞不停出现，帮助 [Arch 安全小组](../团队/Arch%20安全团队.md)追踪新的漏洞。

### 帮助测试软件包

测试仓库中的软件包需要进行测试后才会进入主仓库。请帮助 [Arch 测试组](../团队/Arch%20测试团队.md)测试新软件包。

### 提出需求

在 [bug 系统](https://gitlab.archlinux.org/archlinux/packaging/packages+GitLab)中提出软件包需求，提交前请先阅读[漏洞报告准则#Bug 或者 Feature？](../Arch%20开发/漏洞报告准则.md#Bug_或者_Feature？)，以确保功能需求是合理的，此外还需阅读 [General guidelines#Packaging merge requests](https://wiki.archlinux.org/title/General_guidelines#Packaging_merge_requests) 。

在 AUR 软件包页面的相关页面提出 AUR 软件包需求。

### 创建或维护 AUR 软件包

[Arch 用户软件仓库](Arch%20用户软件仓库.md)是一个大众主导的软件仓库，包含了大量用户提交的 PKGBUILD。与官方软件库二进制包的形式不同，AUR 中的软件包将以 PKGBUILD 的形式供用户使用，并全由用户自行编译。用户可通过 AUR 分享软件，其中一些热门软件会选入官方的 [Extra](官方仓库.md#extra) 软件仓库。

### 成为 Trusted User

像帮助维护 [AUR](Arch%20用户软件仓库.md) 和 [extra 软件仓库](官方仓库.md#extra)中的软件包，可以申请成为[软件包维护者](../团队/软件包维护者.md)。 详情参阅[软件包维护者#我如何成为软件包维护者](../团队/软件包维护者.md#我如何成为软件包维护者？)。

## 活动

有一些社区组织的活动可以参见：

- [Bug Day](../Arch%20开发/Bug%20Day.md)
- [AUR Cleanup Day](../Arch%20开发/AUR%20扫除日.md)

## 软件项目

[Arch Linux](Arch%20Linux.md) 发行版包含了很多组件，可以单独参与到某个项目。

Discussion around the various projects (unless noted otherwise) takes place on the [arch-projects mailing list](https://lists.archlinux.org/mailman3/lists/arch-projects.lists.archlinux.org/) and in the #archlinux-projects IRC channel.

| 项目 | 描述 | 语言 | 维护者 | 其他链接 |
| --- | --- | --- | --- | --- |
| [alpm](https://gitlab.archlinux.org/archlinux/alpm/alpm) | Project that works on specifications, Rust libraries and tools for Arch Linux Package Management | Rust | dvzrv, orhun | [bugs](https://gitlab.archlinux.org/archlinux/alpm/alpm/-/issues), [documentation](https://alpm.archlinux.page/) |
| [alpm.rs](https://github.com/archlinux/alpm.rs) | Rust bindings for libalpm | Rust | Morganamilo | [bugs](https://github.com/archlinux/alpm.rs/issues) |
| [arch-boxes](https://gitlab.archlinux.org/archlinux/arch-boxes) | Virtual machine images | Bash | klausenbusk | [bugs](https://gitlab.archlinux.org/archlinux/arch-boxes/-/issues), IRC #archlinux-releng, [arch-releng mailing list](https://lists.archlinux.org/mailman3/lists/arch-releng.lists.archlinux.org/) |
| [arch-install-scripts](https://gitlab.archlinux.org/archlinux/arch-install-scripts) | Useful scripts for installing Arch Linux | Bash | Foxboron | [bugs](https://gitlab.archlinux.org/archlinux/arch-install-scripts/-/issues) |
| [arch-rebuild-order](https://gitlab.archlinux.org/archlinux/arch-rebuild-order) | A CLI tool to determine the rebuild order of provided package(s) | Rust | jelle | [bugs](https://gitlab.archlinux.org/archlinux/arch-rebuild-order/-/issues) |
| [arch-release-promotion](https://gitlab.archlinux.org/archlinux/arch-release-promotion) | Promotion and synchronization of existing releases of a project in Arch Linux's GitLab instance | Python | dvzrv | [bugs](https://gitlab.archlinux.org/archlinux/arch-release-promotion/-/issues) |
| [arch-repro-status](https://gitlab.archlinux.org/archlinux/arch-repro-status) | CLI tool for querying the reproducibility status of the Arch Linux packages | Rust | orhun | [bugs](https://gitlab.archlinux.org/archlinux/arch-repro-status/-/issues) |
| [arch-security-tracker](https://github.com/archlinux/arch-security-tracker) | The [security.archlinux.org](https://security.archlinux.org) CVE tracking platform and aids in publishing advisories | Python (Flask) | anthraxx | [bugs](https://github.com/archlinux/arch-security-tracker/issues), IRC #archlinux-security, [Advisory mailing list](https://lists.archlinux.org/mailman3/lists/arch-security.lists.archlinux.org/) |
| [arch-signoff](https://gitlab.archlinux.org/archlinux/arch-signoff) | Sign off Arch Linux test packages | Python | Jelle | [bugs](https://gitlab.archlinux.org/archlinux/arch-signoff/issues) |
| [archinstall](https://github.com/archlinux/archinstall) | Arch Linux official installer | Python | Torxed | [bugs](https://github.com/archlinux/archinstall/issues), [Discord](https://discord.gg/cqXU88y), [documentation](https://archinstall.archlinux.page/) |
| [archivetools](https://gitlab.archlinux.org/archlinux/archivetools) | A turnkey solution to snapshot Arch Linux packages repositories, ISOs images and boostrap tarballs | Bash |  | [bugs](https://gitlab.archlinux.org/archlinux/archivetools/-/issues) |
| [archiso](https://gitlab.archlinux.org/archlinux/archiso) | Scripts and configuration for building live media | bash | dvzrv, nl6720 | [bugs](https://gitlab.archlinux.org/archlinux/archiso/-/issues), IRC #archlinux-releng, [arch-releng mailing list](https://lists.archlinux.org/mailman3/lists/arch-releng.lists.archlinux.org/) |
| [archlinux-common-style](https://gitlab.archlinux.org/archlinux/archlinux-common-style) | Arch Linux common CSS styles | SCSS, HTML, JavaScript | anthraxx, jelle | [bugs](https://gitlab.archlinux.org/archlinux/archlinux-common-style/-/issues) |
| [archlinux-docker](https://gitlab.archlinux.org/archlinux/archlinux-docker) | Docker images | Bash | hashworks, sangy | [bugs](https://gitlab.archlinux.org/archlinux/archlinux-docker/-/issues), [docker hub](https://hub.docker.com/r/archlinux/archlinux), IRC #archlinux-releng, [arch-releng mailing list](https://lists.archlinux.org/mailman3/lists/arch-releng.lists.archlinux.org/) |
| [archlinux-keyring](https://gitlab.archlinux.org/archlinux/archlinux-keyring) | Arch Linux PGP keyring handling | Python | anthraxx, dvzrv | [bugs](https://gitlab.archlinux.org/archlinux/archlinux-keyring/-/issues) |
| [archlinux-repro](https://github.com/archlinux/archlinux-repro) | Tools to rebuild Arch Linux packages | Python | Foxboron, Jelle, Coderobe | [bugs](https://github.com/archlinux/archlinux-repro/issues) |
| [archmanweb](https://gitlab.archlinux.org/archlinux/archmanweb) | The [man.archlinux.org](https://man.archlinux.org/) website | Python (Django) | Lahwaacz | [bugs](https://gitlab.archlinux.org/archlinux/archmanweb/-/issues) |
| [archweb](https://github.com/archlinux/archweb) | The [archlinux.org](https://archlinux.org) website | Python (Django) | jelle | [bugs](https://github.com/archlinux/archweb/issues) |
| [aurweb](https://gitlab.archlinux.org/archlinux/aurweb) | The page and system for [aur.archlinux.org](https://aur.archlinux.org) | Python, HTML, MySQL | artafinde, lfleischer | [bugs](https://gitlab.archlinux.org/archlinux/aurweb/-/issues/), IRC #archlinux-aurweb, [aur-dev mailing list](https://lists.archlinux.org/mailman3/lists/aur-dev.lists.archlinux.org/) |
| [bugbuddy](https://gitlab.archlinux.org/archlinux/bugbuddy) | Helper daemon that watches the incoming bug reports for Arch Linux and makes sure that the right people are assigned to them | Rust | anthraxx | [bugs](https://gitlab.archlinux.org/archlinux/bugbuddy/-/issues) |
| [buildbtw](https://gitlab.archlinux.org/archlinux/buildbtw) | A service for assisting Arch Linux staff with building new versions of packages | Rust | anthraxx, sven, raffomania | [bugs](https://gitlab.archlinux.org/archlinux/buildbtw/-/issues), IRC #archlinux-buildbtw |
| [dbscripts](https://gitlab.archlinux.org/archlinux/dbscripts) | Scripts to release and manage packages into the repositories | Bash | Foxboron | [bugs](https://gitlab.archlinux.org/archlinux/dbscripts/-/issues) |
| [devtools](https://gitlab.archlinux.org/archlinux/devtools) | Packaging tools for developers and packagers | Bash | Anthraxx, Foxboron, jelle, gromit | [bugs](https://gitlab.archlinux.org/archlinux/devtools/-/issues/) |
| [gitlab-exporter](https://gitlab.archlinux.org/archlinux/gitlab-exporter) | GitLab metrics exporter | Rust | Artafinde, orhun | [bugs](https://gitlab.archlinux.org/archlinux/gitlab-exporter/-/issues) |
| [gluebuddy](https://gitlab.archlinux.org/archlinux/gluebuddy) | A secure helper daemon that watches several aspects of the Arch Linux infrastructure and makes sure that certain conditions are met | Rust | anthraxx, jelle | [bugs](https://gitlab.archlinux.org/archlinux/gluebuddy/-/issues) |
| [infrastructure](https://gitlab.archlinux.org/archlinux/infrastructure) | Arch Linux infrastructure | Ansible, Bash, Python, Packer, Terraform, Zsh | anthraxx, foutrelis, freswa, heftig, jelle, klausenbusk, svenstaro, artafinde, gromit | [bugs](https://gitlab.archlinux.org/archlinux/infrastructure/-/issues), IRC #archlinux-devops, [arch-devops mailing list](https://lists.archlinux.org/mailman3/lists/arch-devops.lists.archlinux.org/) |
| [keycloak-archlinux-theme](https://gitlab.archlinux.org/archlinux/keycloak-archlinux-theme) | Keycloak Arch Linux theme | Java | Artafinde | [bugs](https://gitlab.archlinux.org/archlinux/keycloak-archlinux-theme/-/issues/) |
| [mkinitcpio](https://gitlab.archlinux.org/archlinux/mkinitcpio/mkinitcpio) | Initrd generator | Ash, Bash | Foxboron, nl6720 | [bugs](https://gitlab.archlinux.org/archlinux/mkinitcpio/mkinitcpio/-/issues) |
| [mkinitcpio-archiso](https://gitlab.archlinux.org/archlinux/mkinitcpio/mkinitcpio-archiso) | Mkinitcpio integration for archiso | Ash, Bash | dvzrv, nl6720 | [bugs](https://gitlab.archlinux.org/archlinux/mkinitcpio/mkinitcpio-archiso/-/issues), IRC #archlinux-releng, [arch-releng mailing list](https://lists.archlinux.org/mailman3/lists/arch-releng.lists.archlinux.org/) |
| [namcap](https://gitlab.archlinux.org/pacman/namcap) | Tool for checking binary packages and source PKGBUILDs for common packaging errors | Python | alerque, dvzrv, FFY00, kgizdov | [bugs](https://gitlab.archlinux.org/pacman/namcap/-/issues) |
| [netctl](https://gitlab.archlinux.org/archlinux/netctl) | Profile based systemd network management | Bash | jwitteveen | [bugs](https://gitlab.archlinux.org/archlinux/netctl/-/issues) |
| [neoasknot](https://gitlab.archlinux.org/archlinux/neoasknot) | Contribution landing page for Arch Linux | JavaScript, Svelte | polyzen | [bugs](https://gitlab.archlinux.org/archlinux/neoasknot/-/issues), [whatcanidofor.archlinux.org](https://whatcanidofor.archlinux.org) |
| [pacman](https://gitlab.archlinux.org/pacman/pacman) | Package Manager | Bash, C | Allan, agregory | [bugs](https://gitlab.archlinux.org/pacman/pacman/-/issues), IRC #archlinux-pacman, [pacman-dev mailing list](https://lists.archlinux.org/mailman3/lists/pacman-dev.lists.archlinux.org/) |
| [pacman-contrib](https://gitlab.archlinux.org/pacman/pacman-contrib) | Contribution scripts to pacman | Bash, C, Perl | Demize, Polyzen | [bugs](https://gitlab.archlinux.org/pacman/pacman-contrib/-/issues), IRC #pacman-contrib, [pacman-contrib mailing list](https://lists.archlinux.org/mailman3/lists/pacman-contrib.lists.archlinux.org/) |
| [pkgstats-cli](https://github.com/archlinux-de/pkgstats-cli) | pkgstats client | Go | pierres | [bugs](https://github.com/archlinux-de/pkgstats.archlinux.de/issues), [pkgstats.archlinux.de](https://pkgstats.archlinux.de/) |
| [pkgstats.archlinux.de](https://github.com/archlinux-de/pkgstats.archlinux.de) | Arch Linux package statistics website | JavaScript, PHP, Vue | pierres | [bugs](https://github.com/archlinux-de/pkgstats.archlinux.de/issues), [pkgstats.archlinux.de](https://pkgstats.archlinux.de/) |
| [pyalpm](https://gitlab.archlinux.org/archlinux/pyalpm) | alpm Python bindings | Python, C | Jelle | [bugs](https://gitlab.archlinux.org/archlinux/pyalpm/-/issues) |
| [pytest-pacman](https://gitlab.archlinux.org/archlinux/pytest-pacman) | Pytest plugin for generating repository sync databases | Python | Jelle | [bugs](https://gitlab.archlinux.org/archlinux/pytest-pacman/-/issues) |
| [rebuilderd-website](https://gitlab.archlinux.org/archlinux/rebuilderd-website) | Website for [reproducible.archlinux.org](https://reproducible.archlinux.org) | JavaScript | Jelle | [bugs](https://gitlab.archlinux.org/archlinux/rebuilderd-website/-/issues), [reproducible-builds.org](https://reproducible-builds.org/), [Debian wiki](https://wiki.debian.org/ReproducibleBuilds), IRC #archlinux-reproducible |
| [releng](https://gitlab.archlinux.org/archlinux/releng) | Automation of release artifacts (installation medium, PXE boot) | Bash, Python | dvzrv | [bugs](https://gitlab.archlinux.org/archlinux/releng/-/issues), IRC #archlinux-releng, [arch-releng mailing list](https://lists.archlinux.org/mailman3/lists/arch-releng.lists.archlinux.org/) |
| [repod](https://gitlab.archlinux.org/archlinux/repod) | Tooling to maintain binary package repositories for Linux distributions using the pacman package manager | Python | dvzrv | [bugs](https://gitlab.archlinux.org/archlinux/repod/-/issues), [documentation](https://repod.archlinux.page) |
| [signstar](https://gitlab.archlinux.org/archlinux/signstar/) | A secure enclave signing solution and tooling around it | Rust | dvzrv | [bugs](https://gitlab.archlinux.org/archlinux/signstar/-/issues), [documentation](https://signstar.archlinux.page/), IRC #archlinux-signstar |

DeveloperWiki:Projects 包含了项目组成员、交流取到和所用编程语言。项目代码托管在 [gitlab.archlinux.org](https://gitlab.archlinux.org).

### 捐款

Arch Linux 的生命力源于开发者和社区成员的无私奉献。我们的工作是无偿的，没有维持服务器运营的私人基金，因此也需要您的[资金支持](https://archlinux.org/donate/)。

## 非官方项目

> **注意：**下列所有项目都是社区支持的，即**非官方**。

Arch 拥有一个充满活力的用户社区。如果您的项目想参与到社区中，**请在对应的英文页面**加入：项目地址、项目开始时间和一两句项目简介。

**[Arch Linux 中文社区](../Arch%20社区/Arch%20Linux%20中文社区.md)**
: 中文用户社区相关信息

**[Arch Linux Subreddit](https://www.reddit.com/r/archlinux/)**
: Reddit 中讨论 Arch 的地方。

**[Arch Women](https://github.com/archwomen)**
: 为 Arch 女性用户提供的团体（[论坛帖子](https://bbs.archlinux.org/viewtopic.php?id=136184)）

**[Telegram 群组](https://t.me/archlinuxgroup)**
: Arch 相关问题的 [Telegram](../../03_网络与服务/即时通信/Telegram.md) 群组。

**[Matrix 聊天室](https://matrix.to/#/#archlinux:archlinux.org)**
: Arch 相关问题的 [Matrix](../../03_网络与服务/网络电话/Matrix.md) 聊天室。

**[Community Contributions](https://bbs.archlinux.org/viewforum.php?id=27)**
: Arch 相关项目的论坛.

**[Arch Linux topic @GitHub](https://github.com/topics/archlinux)**
: Arch 相关项目的 GitHub 仓库.

## 怎样才能成为 Arch 开发者?

首先，牢记您所做的一切，都应以帮助大众为出发点。其次，牢记您是社区大众的一分子，帮助别人亦是帮助自己。

通常，新开发者是根据工作内容的增加由已有开发者选出。有时他们会提供一个职位，您可以申请并获得职位。但更多时候，开发者会邀请能力足够，适合开发团队需要的程序员加入。积累 Arch 贡献是成为团队一员的最好方法。

这里是一个列表，没有顺序之分。包含了可以为社区做贡献的方式:

- 通过及时提供帮助获得声誉
- 在论坛、IRC、和邮件列表回答问题
- 加入[软件包维护者](../团队/软件包维护者.md)，获得软件打包经验，提升技能
- 将软件包提交到 AUR
- 加入以后可能成为 Arch 主流应用的程序开发或者自己建立一个项目
- 修改 pacman、makepkg 或[GitLab](https://gitlab.archlinux.org/) 上的其它 Arch 项目，并通过 bug 管理系统提交补丁
- 浏览 [bug](https://gitlab.archlinux.org/groups/archlinux/packaging/-/issues+bug+tracker) 管理系统，修正问题
- 找到并提交新 Bug
- 修正维基错误、添加页面、清理页面并保证步骤符合最新系统
- 提交翻译

## 1.16　Arch 与其他发行版的比较

*难度：高级 ｜ 篇幅：21.9 KB ｜ 所属：关于 Arch*

将要讲到：与基于源码的发行版对比、与通用发行版的对比、与新手友好发行版对比、与BSD系列操作系统的对比、参阅

本文在 Arch Linux 、其他流行的 GNU/Linux 发行版和类 UNIX 操作系统之间做了一些比较，以帮助用户判断 Arch Linux 是否能符合他们的需要。虽然对此进行一些描述有助于用户理解这些操作系统之间的不同点，但是比较 Arch Linux 和其他操作系统的最好办法还是由用户自行安装它们并进行亲身体验。

英文维基百科的[《操作系统比较》](https://en.wikipedia.org/wiki/Comparison_of_operating_systems)和[《Linux 发行版比较》](https://en.wikipedia.org/wiki/Comparison_of_Linux_distributions)这两个页面包含了更详细的比较信息。

在接下来的部分中，只有 Arch Linux 与其他发行版做了比较。社区适配的支持 x86_64 以外的架构的版本在[基于 Arch 的发行版](../../06_软件与应用/列表/基于%20Arch%20的发行版.md)中列出。

## 与基于源码的发行版对比

基于源码的发行版具有高度可移植性，非常容易被移植到不同架构的系统上。它们可以根据特定计算机的体系架构和使用情况，控制和编译整个系统和所有软件包，其优化是最佳的。但缺点是如果计算机系统的配置性能较低，安装系统和软件包的源码编译会极为费时费力。Arch Linux 的基础软件包和其他所有软件包仅为 x86-64 架构进行编译。

### CRUX

- [CRUX](https://crux.nu/) 是让 Judd Vinet 有了创建 Arch 的灵感的、专注于 [KISS](Arch%20术语.md#KISS) 法则的一种轻量级发行版。
- CRUX 使用 BSD 风格的启动脚本，而 Arch Linux 使用 [systemd](../../01_安装与包管理/初始化/Systemd.md) 。
- 尽管 Arch Linux 使用滚动升级机制，CRUX 也差不多每年都会有发行版。
- 两者都提供了类 Ports 系统，并且和 \*BSD 一样都提供了一个基础系统以供用户在其之上进行构建。
- Arch Linux 使用 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 来进行二进制包管理，同时还使用 [Arch 构建系统](Arch%20构建系统.md)。CRUX 使用一个叫 *prt-get* 的社区开发软件和它自己的 Ports 系统来处理依赖关系解析。尽管如此，CRUX 上所有的软件包都需要从源代码进行编译，即使 CRUX 的基础系统是基于二进制包的。
- Arch Linux 和 CRUX 官方都仅支持 x86_64 架构。
- Arch Linux 的软件仓库提供大量编译好的二进制软件包。除此之外，还拥有 [Arch 用户软件仓库（AUR）](Arch%20用户软件仓库.md)。CRUX 的软件仓库则比较逊色，并且其 Ports 系统也缺乏官方支持。

### LFS

- [LFS](https://www.linuxfromscratch.org/lfs/)（Linux From Scratch）只是以文档形式存在，它提供的文档指导用户如何从零开始构建一个完全个性化的 GNU/Linux 操作系统：从如何获取构建一个基础操作系统所需的源代码，到如何手动编译，如何打补丁，如何对系统进行配置。总之，LFS 提供了一个构建和定制基础系统的良好教程。
- LFS 不提供在线软件仓库。它必须由用户手动获取源代码，然后用 *make* 编译安装它们（这需要一些软件包管理方法，而这些方法在 [LFS Hints](https://lfs.lug.org.cn/hints/) 里面提到过）。
- Arch Linux 基础系统除了提供和 LFS 一样的软件包，还包含 [systemd](../../01_安装与包管理/初始化/Systemd.md)、[pacman](../../01_安装与包管理/包管理器/Pacman.md) 等一些额外的工具，并且这些软件都已经为 x86_64 架构编译过了。Arch 社区和开发者提供了数以千计的软件包，这些软件包可以通过 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 或者 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 脚本进行安装（PKGBUILD 脚本需要和 [Arch Build System](Arch%20构建系统.md) 一起使用）。除此之外，Arch Linux 还包含了一个名叫 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 的工具来生成便于 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 使用的软件包。
- Judd Vinet 从头开始构建了 Arch，然后用 C 语言编写开发了 [pacman](../../01_安装与包管理/包管理器/Pacman.md)。所以，Arch Linux 有时候会被幽默的描述为“有一个很好的软件包管理器的 Linux”。

### Gentoo Linux

- Arch Linux 和 Gentoo Linux 都是滚动升级的发行版，所以在上游（原始开发者）软件包发布不久，两者就会有软件包可以用。
- Gentoo Linux 的基础系统和软件包都是根据用户指定的 *USE* 标识直接从源代码构建。Gentoo Linux 提供了一个类 Ports 系统（ Ports 是 BSD 系统上的一个系统工具）来从源代码编译软件包，而 Arch Linux 基础系统被设计为“直接由预构建好的 x86_64 二进制软件包组成”。一般来说，Arch Linux 更易于构建和升级，而 Gentoo Linux 更易于进行系统化的定制。
- Arch Linux 只支持 x86_64 架构，而 Gentoo Linux 对 x86（i486/i686）、x86_64、PPC/PPC64、SPARC、Alpha、ARM、MIPS、HPPA、S/390、Itanium、M68K 以及 RISC-V 架构均提供官方支持。
- Gentoo Linux 的官方包管理工具比 Arch Linux 的更加复杂和“强大”，并且 Gentoo Linux 的某些核心功能特性（比如 [USE 标记](https://wiki.gentoo.org/wiki/Handbook:X86/Working/USE/zh-cn)、[SLOTs](https://wiki.gentoo.org/wiki/Handbook:X86/Working/Portage/zh-cn#.E7.AE.80.E4.BB.8B_.3D) 等等）在 Arch Linux 中并没有相对应的替代品。究其原因，首先是因为 Arch Linux 主要是一个二进制发行版，其次是因为 Gentoo Linux 和 Arch Linux 在[设计理念](Arch%20Linux.md#Principles)上有些许差别，Arch Linux 在原则上更偏向于架构的简洁性和避免过度设计。
- 因为 Gentoo Linux 和 Arch Linux 的安装都只包含一个基本系统，所以两者都被认为是需要高度定制化的系统。一般来说，Gentoo Linux 用户如果习惯于 [systemd](../../01_安装与包管理/初始化/Systemd.md) 的话，对 Arch Linux 的大多数方面都会比较满意。

### GNU Guix System

- [GNU Guix System](https://guix.gnu.org/) 受到 [NixOS](https://nixos.org/) 的启发，类似于 Arch Linux 受到 [CRUX](https://crux.nu/) 的启发。
- Arch Linux 和 Guix System 都是滚动发布发行版，使软件包在上游发布后短时间内可供发行版使用。然而，Guix 系统主要是一个基于源代码的发行版（尽管存在预构建的二进制文件并称为“[替代品](https://guix.gnu.org/en/manual/en/guix.html#Substitutes)”），而 Arch Linux 主要是一个二进制发行版。
- Arch Linux 使用 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 作为包管理器，而 Guix System 使用 [GNU Guix](../../06_软件与应用/GNU/GNU%20Guix.md)，它支持其他发行版中不存在的[实验性打包功能](https://guix.gnu.org/manual/en/html_node/Features.html)。
- Arch Linux 只支持 x86_64，而 Guix System 正式支持[多种架构](https://guix.gnu.org/manual/en/html_node/GNU-Distribution.html)。
- Arch Linux 使用 [systemd](../../01_安装与包管理/初始化/Systemd.md) 作为 [init](../../01_安装与包管理/初始化/Init.md) 系统，而 Guix System 使用 [GNU Shepherd](https://www.gnu.org/software/shepherd/)。
- Guix System 打破了许多传统的 Unix 概念，包括[文件系统层次结构标准](https://wiki.archlinux.org/title/Arch_filesystem_hierarchy)。例如，在传统发行版中分布在不同的目录中的许多文件，在Guix系统中将位于`/gnu/store/`。
- Arch Linux 偶尔会发布非自由软件（通常是驱动程序），而 Guix System 只发布自由软件，并得到自由软件基金会的认可——尽管存在为 Guix 提供[非自由软件](https://wiki.archlinux.org/title/Free_Software_Foundation)的替代存储库。
- Arch Linux 希望用户直接配置已安装的软件包，而 Guix System 鼓励在 Scheme 中进行全局系统配置，进而[实例化](https://guix.gnu.org/manual/en/html_node/Invoking-guix-system.html)配置文件。

## 与通用发行版的对比

这些发行版的优点是受众更加广泛，可以满足绝大部分用户对计算机操作系统的需求。

### Debian GNU/Linux

- Debian 是上游最大的发行版，其社区规模更大。它提供稳定（Stable）、测试（Testing）和不稳定（Unstable）三个版本分支，包含超过51000个二进制包。而 Arch Linux 的软件仓库相对较小，但是如果算上 AUR 仓库，那么 Arch Linux 支持的软件数量也差不多。
- Debian 对自由软件更热情，但也提供非自由软件仓库。Arch Linux 对 GNU 定义的非自由（“non-free”）软件更显宽容。
- Debian 对稳定分支的测试更加详细彻底，软件基本是上冻结的，并提供[五年](https://wiki.debian.org/LTS)官方社区支持。Arch Linux 提供的的软件包会比 Debian Stable 中的软件包更新，但会与 unstable 和 testing 分支里的差不多，而且没有固定发布周期，均以滚动形式发布。
- Debian 支持许多架构，包括 amd64,arm64,armel,armhf,i386,ppc64el,riscv64与 s390x等。而 Arch Linux 仅对 x86_64 架构提供官方支持，其对 arm 架构的支持则移植自社区项目（例如对 Raspberry Pi 的支持）。
- Arch Linux 对从源码创建软件包提供更好的支持，有一个类 ports 系统。而 Debian 不提供类 Ports 系统，而是依靠它巨大的二进制软件包仓库。
- Arch Linux 安装环境只提供最小的基本系统，然后通过编辑文本文件来配置系统。而 Debian 的配置方式则更加自动化，并且还提供多种安装方式，例如使用 *apt* *任务*来安装预先选择的软件包组。
- 在启动脚本上，Debian（ 8.0 或更高版本）和 Arch Linux 均使用 [Systemd](../../01_安装与包管理/初始化/Systemd.md)，因为其总体性能是相当优异的。
- Arch Linux 一般将 lib 软件库与其头文件一同打包在一起，而在 Debian 中，头文件必须单独下载，并将 lib 软件库与头文件分别打包。
- Arch Linux 将补丁保持在最低限度，只会在迫不得已的时候才会去打补丁，以避免出现上游无法审阅的问题。而 Debian 的用户众多，所以会经常对软件包打补丁。

### Fedora

- Fedora Linux 是 Red Hat® Enterprise Linux （红帽企业版 Linux，RHEL）的上游社区发行版，也是一种技术先导发行版，对新技术的使用非常激进。红帽（Red Hat）公司是该项目的主要赞助商，但成千上万的独立开发人员也为 Fedora 做出了贡献。软件包和项目在 Fedora 上发布，通过其自己独特的测试和质量保证流程，这些功能迁移到 CentOS Stream，并最终被合并引入到 Red Hat Enterprise Linux 中，其中一些最终被其他发行版采用。Arch Linux 没有固定版本，也不作为另一个发行版的分支，即使许多其他发行版基于 Arch Linux（例如 Steam Deck 的 SteamOS）。
- Fedora 使用 [RPM 格式软件包、 DNF 软件包管理器](https://docs.fedoraproject.org/en-US/fedora/latest/system-administrators-guide/package-management/DNF/)。而 Arch Linux 使用 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 管理软件包。这两个项目的许多软件包，特别是桌面环境，都被描述为“原版”，并且没有自定义。
- Fedora 坚持开源理念，拒绝在官方仓库中包含非自由软件包，默认不提供有专利限制的软件。例如 MP3 支持（从 Fedora 25 开始已重新支持 MP3，而且一些第三方源通常也会提供这些内容）。而 Arch Linux 对于 MP3 及非自由软件则更加宽松，尊重用户的知情权和使用权。
- Fedora 提供许多不同的安装方式，包括图形化 Anaconda 安装程序、最小化安装和专家安装选项，有助于安装基本系统或是直到安装您选择的成熟桌面环境。Fedora "spins" 还提供许多不同的图形化桌面环境以供用户选择（这些桌面环境都会附带一些默认的软件包）。Arch Linux 被设计为仅提供一些简单脚本来进行最小化系统安装，并且同样可以根据用户个人需要安装使用不同的图形化桌面环境。
- Fedora 发行周期比较固定，但官方支持通过 dnf-plugin-system-upgrade（适合大部分的版本）或 rpm-ostree（适合 Fedora Atomic Host）工具进行跨版本升级。而 Arch Linux 是通过滚动升级系统。
- Arch Linux 有 ports 系统，而 Fedora 有 [Fedora Copr](https://copr.fedorainfracloud.org/)（一个类 AUR 构建系统）。
- Arch Linux 和 Fedora 都面向有经验的用户和开发人员，两者都倡导用户积极为项目开发做出贡献。
- Fedora 在 SELinux 整合，GCJ 编译包（GCJ 的目的是解除对 Oracle JRE 的依赖）等方面是走在各发行版的前列的，并且会积极为上游开发做贡献。和其他项目相比，红帽公司和 Fedora 开发人员贡献的 Linux 内核代码最多。
- ArchWiki 被认为是各发行版的Wiki中内容最丰富和最易用的。而 Fedora wiki 仅是开发者、测试者和用户间交流信息的平台，并不是和 ArchWiki 一样为最终用户准备。其实它更像一个问题追踪和合作开发的 wiki。

### Slackware

- Slackware 使用 BSD 风格的 [init](../../01_安装与包管理/初始化/Init.md) 脚本，而 Arch Linux 使用 [systemd](../../01_安装与包管理/初始化/Systemd.md)。
- Arch Linux 有一个强大完整的软件包管理系统 [pacman](../../01_安装与包管理/包管理器/Pacman.md)。与 Slackware 的标准工具不同，它可以自己处理依赖关系并提供更加自动化的系统升级方式。Slackware 的用户会更喜欢手动处理依赖关系，以尽可能控制自己的系统，而 Slackware 本身也对预编译的库和依赖提供杰出的支持。
- Arch Linux 是一个滚动升级的系统，而 Slackware 的系统版本和软件包的发布则更为保守，它会更喜欢提供经过验证的稳定软件包。在这个方面，Arch Linux 更为*“前卫”*。
- Arch Linux 官方软件仓库提供成千上万的二进制软件包，而 Slackware 官方支持的软件包就比较少了。
- Arch Linux 提供 [Arch Build System](Arch%20构建系统.md)（一个类 Ports 系统）和 [AUR](Arch%20用户软件仓库.md)（由用户贡献的数以万计的 PKGBUILD 的集合）。Slackware 提供一个类似的系统 [slackbuilds.org](https://www.slackbuilds.org)，它是一个半官方支持的 Slackbuilds（和 PKGBUILD 相似）仓库。Slackware 用户一般会对 Arch Linux 的大多数方面非常满意。

## 与新手友好发行版对比

“新手友好版”有时也被叫做“新手发行版”。这些对新手友好的发行版之间有许多相同之处，而 Arch Linux 和它们其中的任何一个发行版都不同。如果您想通过构建极简系统的方式来学习 GNU/Linux ，Arch Linux 或许是一个好选择。因为相比一些“新手发行版”，Arch Linux 只需安装很少的软件包。一些不同的“新手发行版”之间的特点如下所述：

### Ubuntu

- Ubuntu 是一个非常流行的基于 Debian 的发行版，由 Canonical 公司提供商业支持。而 Arch Linux 是由志愿者从头开始独立构建开发而成的。
- Ubuntu 和 Arch Linux 的目标不同，并且面向的用户群体也不一样。Arch Linux 为那些渴望自己动手的用户设计，而 Ubuntu 则提供图形化安装程序自动配置好了的系统，对新手用户来说更“友好”。Arch Linux 从一开始就设计了一个最小化的基础系统，非常依赖有一定知识基础的用户按自己的特定需求进行个性化定制。一般来说，开发者和动手能力强的用户更喜欢 Arch Linux 。不过有许多 Arch Linux用户是从 Ubuntu 开始学习，掌握一定的技巧后最终转向使用 Arch Linux 。
- Arch Linux 开发不偏向于任何超出其社区提供支持的特定用户界面。此外，Canonical 公司的商业性质导致他们做出了一些有争议的决定，例如在 Unity 的 *Dash* 菜单中包含广告和收集用户数据。而 Arch Linux 是一个独立的、由社区驱动的项目，没有商业议程。
- Ubuntu 开发和推广的重心似乎转移到了触摸屏设备上，而 Arch Linux 的开发依然坚持以用户为中心，鼓励社区合作开发客制化的解决方案。
- Ubuntu 通常每 6 个月发布新版本，而 Arch Linux 通过滚动升级系统。
- Arch Linux 提供类 Ports 的软件包构建系统和 [Arch User Repository](Arch%20用户软件仓库.md)，用户可以分享源代码编译脚本，然后用 [Pacman](../../01_安装与包管理/包管理器/Pacman.md) 安装管理。Ubuntu 则使用更复杂的 [apt](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool)，可以通过 [Personal Package Archives](https://launchpad.net/ubuntu/+ppas)重新分发二进制软件包。
- 这两个发行版的社区在某些方面也有所不同。Arch 社区要小的多，更倾向于鼓励用户为 Arch Linux 奉献一份力量。相比之下，Ubuntu 社区更加庞大，并能容忍其中并没有积极为开发、打包和维护做贡献的大多数用户。

### Linux Mint

- [Linux Mint](https://www.linuxmint.com/) 最初是一个 [Ubuntu](#Ubuntu) 的衍生版本。后来又衍生出 LMDE（ Linux Mint Debian Edition ），LMDE 是一个基于 [Debian](#Debian_GNU/Linux) 的版本。而 Arch Linux 是一个独立的发行版，它依赖于自己的[构建系统](Arch%20构建系统.md)和[官方仓库](官方仓库.md)。
- 为了方便系统维护，Linux Mint 包含了一些图形化工具，它们叫做 *MintTools*（Mint 工具），而 Arch Linux 仅提供简单的命令行工具，例如 [Pacman](../../01_安装与包管理/包管理器/Pacman.md)，因此 Arch Linux 的系统管理工作需要由用户自行组织安排。
- Linux Mint 主要以 [Cinnamon](../../05_桌面与图形/桌面环境/Cinnamon.md) 、 [MATE](../../05_桌面与图形/桌面环境/MATE.md) 与 [Xfce4](../../05_桌面与图形/Xfce/Xfce.md) 作为它的图形界面，不过它也可以由用户自行安装选择 [KDE](../../05_桌面与图形/KDE/KDE.md) 。Linux Mint 同时支持 Plus codecs、Flash、DVD playback 和 MP3，这其中有一些是有专利限制的私有软件。
- Linux Mint 半年发布一次新版本。也就是在新版本 Ubuntu 发布一个月之后。Linux Mint 的每一个版本都基于最新的 Ubuntu LTS（长期支持版），并且提供五年技术支持。 Mint 的 Debian 版本 (LMDE) 基于 Debian Stable分支。而且只接收来自 Mint 的软件包更新和安全更新。与之相反，Arch Linux 则完全是一个滚动更新的发行版。

### openSUSE

openSUSE 诞生于最初的 SUSE Linux，由 SUSE（SUSE Enterprise Linux 的制造商）赞助支持。SUSE Linux Enterprise Desktop （SLED） 基于 openSUSE Tumbleweed，与 openSUSE Leap 共享一个通用代码库。

- OpenSUSE 使用 Zypp 软件包管理器（终端命令为 *zypper*）、RPM 格式软件包以及备受推崇的 YaST2 图形化配置工具。Arch Linux 使用 Pacman 来管理 tar.zstd 包，没有提供此类图形配置工具。一般 openSUSE 更适合经验较少或需要使用图形界面自动完成配置的用户。

- openSUSE 提供 2 个不同的版本：
    - Leap 是 openSUSE 的长期支持版本，具有离散版本。Leap 版本一般是每年发布一个版本，软件包会比 Arch Linux 更旧。
    - Tumbleweed 是 openSUSE 的滚动发布版本。Tumbleweed 版本是滚动更新的，软件包版本的新旧和 Arch Linux 差不多。

- openSUSE 默认不提供有专利限制的软件，比如 MP3 支持。您需要添加第三方源来取得这些内容。而 Arch Linux 对于 MP3 及非自由软件更加宽松，更倾向于将决定权交给用户。
- 相比之下，Arch Linux 严格来说是一个滚动发布模型，不提供离散发布版本。Arch Linux 默认不是完整的桌面环境，只提供最小的基本系统安装。因此，openSUSE 可能更适合那些想要图形环境、自动配置或开箱即用的功能，同时仍然允许在所有发行版上进行自定义的用户。

## 与BSD系列操作系统的对比

- BSD 系列操作系统都起源于加利福尼亚大学伯克利分校所做的工作，它们致力于提供一个可以自由再分发、免费的 UNIX 系统。它们不是 GNU/Linux 发行版，而是以原始的 AT&T UNIX 系统的代码为基础演进的类 UNIX 操作系统。
- Arch Linux 和 BSD 都提供紧密集成的基本系统和 Ports 软件包管理系统。但与 GNU/Linux 系统（比如 Arch Linux）不同，BSD 操作系统的内核和用户空间的程序，如 shell 和常用工具（像 ls、cp、cat 和 ps）是集中在单一的源代码仓库中一起开发的。
- 与 GPL 许可证相反，BSD 许可证比较宽松，GPL 许可证规定衍生品需要在同一许可证下发布。而 Arch Linux 是在 GPL 许可证下发布的。
- 要获得 BSD 变体的更多信息，请参阅 [Wikipedia:Comparison of BSD operating systems](https://en.wikipedia.org/wiki/Comparison_of_BSD_operating_systems)。

## 参阅

- [DistroWatch.com- Linux 发行版新闻和评论](https://distrowatch.com/)

## 1.17　Arch 用户软件仓库

*难度：高级 ｜ 篇幅：24.2 KB ｜ 所属：关于 Arch*

将要讲到：入门、安装与更新软件包、账户状态、反馈、调试包构建过程、提交

[*Arch 用户软件仓库*](https://aur.archlinux.org)（Arch User Repository，AUR）是为 [Arch Linux](Arch%20Linux.md) 用户而建，由社区主导的软件仓库。AUR 中的软件包以软件包描述文件（[PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md)）的形式提供，用户自己通过 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 生成包，再由 [pacman](../../01_安装与包管理/包管理器/Pacman.md#其它命令) 安装。创建 AUR 的初衷是供社区管理和分享新的软件包，并由官方定期从中挑选软件包进入 [extra](官方仓库.md) 仓库。本文介绍用户访问和使用 AUR 的方法。

许多官方仓库软件包都来自 AUR。通过 AUR，大家可以相互分享新的软件包生成脚本（[PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 和其他相关文件）。用户还可以为软件包[投票](#给软件包投票)。如果一个软件包投票足够多，没有许可证问题且打包质量好，那么它就很有希望被收录进官方 *extra* 仓库（以后就可以直接通过 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 或 [Arch 构建系统](Arch%20构建系统.md)安装了）。

> **警告：**AUR 中的软件包是由其他用户编写的，这些 `PKGBUILD` 完全是非官方的，未经彻底审查。使用这些文件的风险由您自行承担。

> **注意：**如果你计划要使用 AUR，**强烈建议**关注 [AUR 常规邮件列表](https://lists.archlinux.org/mailman3/lists/aur-general.lists.archlinux.org/)，该列表被用于发布安全警告。      

## 入门

用户可以从 [AUR 网站](https://aur.archlinux.org)下载 `PKGBUILD` 文件。这些 `PKGBUILD` 可以通过 *makepkg* 生成软件包，最后由 *pacman* 进行安装。

- 确保已经安装了 [base-devel](https://archlinux.org/packages/?name=base-devel)包。
- 浏览 [#常见问题](#常见问题)一节，以获得对最常见问题的解答。
- 在从 AUR 构建软件包之前，您可能希望调整 `/etc/makepkg.conf` 以优化系统的构建过程。通过调整 `MAKEFLAGS` 变量、使用多核进行压缩或使用不同的压缩算法，可以在多核处理器的系统上显著缩短包构建时间。用户还可以通过 `CFLAGS` 变量启用特定于硬件的编译器优化。详情参见 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md#优化)。

如果您设置了 [AUR SSH 身份验证](AUR%20提交准则.md#认证)，也可以通过 SSH 连接到 AUR：运行 `ssh aur@aur.archlinux.org help` 以获得可用指令的列表。

## 安装与更新软件包

从 AUR 安装软件包并不困难。基本步骤如下：

1.  [获取构建文件](#获取构建文件)，包括 `PKGBUILD` 和所需的其他文件，比如 [systemd](../../01_安装与包管理/初始化/Systemd.md) 单元文件和补丁（往往并非软件包的实际代码）。
2.  [检验 PKGBUILD](#检验_PKGBUILD)；最重要！检验 `PKGBUILD` 及相关文件，确保其可信、无恶意。
3.  [构建软件包](#构建软件包)：在保存文件的目录下运行 `makepkg`。这将下载代码，编译并打包。
4.  [安装软件包](#安装软件包)：执行 `pacman -U *package_file*` 将软件包安装到系统上。

首先，确保安装了 [base-devel](https://archlinux.org/packages/?name=base-devel)包，该[元软件包](../../01_安装与包管理/包管理/元软件包和软件包组.md)提供了 [make](https://archlinux.org/packages/?name=make)包 等用于从源码进行编译的工具：

> **注意：**AUR 中的软件包会假定构建环境里已安装了 [base-devel](https://archlinux.org/packages/?name=base-devel)包。

然后，选择一个合适的构建目录。该目录仅用作生成软件包或从源码构建时的工作目录，因此任何空白目录都可以。下面的示例中使用 `~/builds` 作为编译目录。

### 获取构建文件

通过在 [AUR 主页](https://aur.archlinux.org/)中搜索，找到要安装的软件包。点击搜索结果中的软件名称，进入软件包信息页。阅读描述并确认是否为目标软件，并检查软件的更新时间和评论。

确认无误后，有几种方法可以获取包的构建文件：

- 克隆其 [git](../../07_开发与工具/版本控制系统/Git.md) 存储库，在其 AUR 页面的“包详细信息”中标记为“Git Clone URL”。这是首选方法，其优点是您可以通过 `git pull` 轻松获取包的更新。

: ```
$ git clone https://aur.archlinux.org/package_name.git
```

- 通过单击其 AUR 页面右侧“Package Actions”下的“Download snapshot”链接或在终端中下载快照：

: ```
$ curl -L -O https://aur.archlinux.org/cgit/aur.git/snapshot/package_name.tar.gz
```
: > **注意：**快照文件是压缩的，必须解压（最好在为 AUR 构建预留的目录中）：`tar -xvf *package_name*.tar.gz`

- 使用 [GitHub 上的 archlinux/aur](https://github.com/archlinux/aur) 只读镜像，每个包都位于一个分支中。建议只克隆一个分支（整个存储库太大，速度会很慢）。您可以使用以下两种方法之一执行此操作：
    - 使用 `git clone --single-branch`：

        ```
        $ git clone --branch package_name --single-branch https://github.com/archlinux/aur.git
        ```

    - [部分克隆](../../07_开发与工具/版本控制系统/Git.md#部分接收)这个仓库 (`git clone --depth=1`) 和选择性[添加分支](../../07_开发与工具/版本控制系统/Git.md#获取其他分支)：

: : ```
$ git clone --depth=1 https://github.com/archlinux/aur.git; cd aur$ git remote set-branches --add origin package_name$ git fetch$ git switch package_name
```

### 检验 PKGBUILD

切换到含有软件包的 `PKGBUILD` 文件的目录：

```
$ cd package_name
```

> **警告：**请仔细检查 git 代码库中的 `PKGBUILD`、所有 *.install* 文件以及其它文件，确认是否带有恶意或危险命令。如有任何怀疑，请不要构建软件包，并到论坛或邮件列表中[寻求帮助](../../02_系统管理与内核/系统恢复/常规故障排除.md#额外支援)。过去已经发现数例含恶意代码的软件包。    一些工具，比如 [traur](https://aur.archlinux.org/packages/traur/)AUR 和 [ks-aur-scanner](https://aur.archlinux.org/packages/ks-aur-scanner/)AUR，可辅助用户扫描 PKGBUILD 内容；但它们不能取代细致的手动检验。

查看所有提供的文件中的内容。例如使用 *less* 查看 `PKGBUILD`：

```
$ less PKGBUILD
```

检查 `PKGBUILD` 源码列中是否存在签名文件（*.sig* 或 *.asc*）。若是，则获取 PKGBUILD 列出的 [validpgpkeys](../../01_安装与包管理/包开发/PKGBUILD.md#validpgpkeys) 公钥之一。更多信息参见 [makepkg#验证签名](../../08_安全与加密/OpenPGP/Makepkg.md#验证签名)。

无论什么情况下，必须牢记恶意代码也可能被签名，因此必须仔细检查 `PKGBUILD`，尤其是可能从仓库之外获取软件代码的部分。

> **提示：**如果您正在更新软件包，您可能需要查看最后一次提交以来的变动。
>
> - 您可以使用 `git show` 来查看最后一次提交之后的变动。
> - 您也可以使用 *vimdiff*：`git difftool @~..@ --tool=vimdiff`。使用 *vimdiff* 的好处是可以查看到所有文件，以及对出现变更部分的提示。

### 构建软件包


手动检查文件后，以普通用户权限执行 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) ，下面是一些有用的参数：

- `-s`/`--syncdeps` 会在构建之前使用 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 自动解析和安装任何依赖项。如果包依赖于其他 AUR 包，则您应先手动安装它们。
- `-i`/`--install` 会在构建成功后安装软件包。这使您可以跳过通常需手动完成的下一步。
- `-r`/`--rmdeps` 会在构建后删除不需要的构建时依赖项。但是，下次更新包时可能需要重新安装这些依赖项。
- `-c`/`--clean` 会在构建后清理临时构建文件，因为它们不再需要。这些文件通常仅在调试构建过程时才需要。
- `--packagelist` 会在无需构建的情况下输出构建出的软件包文件名。

> **提示：**使用 `git clean -dfx` 可以删除所有不被 git 跟踪的文件，从而删除所有以前构建的包文件。

### 安装软件包

现在可以使用 pacman 安装该软件包：

```
# pacman -U package_name-version-architecture.pkg.tar.zst
```

> **注意：**
>
> - 如果您在 `makepkg.conf` 中更改了 `PKGEXT`，则包文件的名称可能会略有不同。
> - 以上示例只是对构建过程的简要总结。**特别**推荐阅读 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 和 [Arch 构建系统](Arch%20构建系统.md)文章以了解更多详情。

### 升级软件包

在包含包的 `PKGBUILD` 的目录中，您必须首先使用命令更新文件：

```
$ git pull
```

然后按照之前的构建和安装说明进行操作。

### 更新软件包

Pacman 不支持 AUR，因此更新您安装的所有包是*您的职责*，而不是 pacman 的。如果官方仓库中的包更新了，您需要重新构建依赖于那些库的 AUR 包。`checkrebuild` 和 [rebuild-detector](https://archlinux.org/packages/?name=rebuild-detector)包 提供的 `rebuild-detector` [钩子](../../01_安装与包管理/包管理器/Pacman.md#钩子)可用于查找需重新构建的软件包。

## 账户状态

### 封禁（Suspension）

作为[软件包维护者](../团队/软件包维护者.md)时，可以对用户配置“封禁”字段，从而挂起目标用户。**当用户被封禁时，他们不能：**

- 登录 [https://aur.archlinux.org](https://aur.archlinux.org)
- 接收通知
- 与 git 界面交互

### 非活动（Inactivity）

当编辑自己的账户或作为*软件包维护者*编辑其他用户的账户时，可以设置“非活动”字段。使用非活动账户有两个原因：

- 在其账户页面上显示某人被标记为非活动的日期
- 根据他们对新提案的不活动情况，生成当前的活跃*软件包维护者*计数

## 反馈

### 在软件包上添加评论

[AUR 网页界面](https://aur.archlinux.org)中有一个评论工具，允许用户就改进提出建议和反馈给 `PKGBUILD` 贡献者。

> **提示：**避免将补丁或 `PKGBUILD` 粘贴到评论部分：它们很快就会过时，最终会不必要地占用大量空间。请将这些文件通过电子邮件发送给维护者，甚至可以使用 [pastebin](../../06_软件与应用/应用程序/应用程序列表.md)。

[Python-Markdown](https://python-markdown.github.io/) 提供了基本的 [Markdown](https://en.wikipedia.org/wiki/Markdown) 语法来格式化评论。

> **注意：**
>
> - 此实现与官方[语法规则](https://daringfireball.net/projects/markdown/syntax)或许会有一些[差异](https://python-markdown.github.io/#differences)。
> - 对软件包 [Git](../../07_开发与工具/版本控制系统/Git.md) 存储库的提交哈希值，以及对 [Flyspray](../../07_开发与工具/问题跟踪系统/Flyspray.md) 工单的引用会被自动转换为链接。
> - 长评论默认折叠，可按需展开。

### 给软件包投票

**所有的** Arch 用户都可以通过 AUR 界面为喜欢的软件包**投票**。所有软件包都有机会被包维护者接手，并收录至 [extra](官方仓库.md) 仓库。投票数是该选拔过程的重要依据之一，因此请尽可能地投票！

注册 [AUR 网站](https://aur.archlinux.org/)账户，在浏览软件包时会看到投票选项。注册后，还可以通过 [aur-auto-vote-git](https://aur.archlinux.org/packages/aur-auto-vote-git/)AUR 在命令行中投票。

此外，如果你设置了 [AUR SSH 身份认证](AUR%20提交准则.md#认证)，可以使用 ssh 密钥直接通过命令行投票，不再需要保存或者输入 AUR 密码：

```
$ ssh aur@aur.archlinux.org vote package_name
```

### 将软件包标记为过期

首先，点击软件包页面的“Flag Out-of-date”（中文版为“标记为过期”），并说明过期的原因，最好包含新版本的发布说明或 [tarball](../../02_系统管理与内核/数据压缩与归档/归档与压缩.md#仅归档) 的链接。

可以通过邮件通知维护人员。如果*两周*后还是没有得到答复，可以发出*遗弃*（orphan）请求。更多信息请参考 [AUR 提交准则#请求](AUR%20提交准则.md#请求)。

> **注意：**[VCS 软件包](../../01_安装与包管理/Arch%20打包准则/VCS%20软件打包准则.md) 在 `pkgver` 变化时并不会过时，所以不要标记它们，且维护者也只会取消标记并无视请求。AUR 维护者不应该仅为升高 `pkgver` 就进行提交。

## 调试包构建过程

1.  在构建任何东西之前，通过[更新](../../01_安装与包管理/包管理器/Pacman.md#升级软件包)确保您的构建环境是最新的。
2.  确保您已安装 [base-devel](https://archlinux.org/packages/?name=base-devel)包。
3.  对 `makepkg` 搭配使用 `-s` 选项，以在开始构建过程之前检查并安装所有依赖项。
4.  试试默认的 [makepkg 配置](https://gitlab.archlinux.org/archlinux/packaging/packages/pacman/-/blob/main/makepkg.conf)。
5.  常见问题请参阅 [Makepkg#问题处理](../../08_安全与加密/OpenPGP/Makepkg.md#问题处理)。

如果您在构建包时遇到问题，请先阅读其 `PKGBUILD` 及其 AUR 页面上的评论。

可能 `PKGBUILD` 本身就有配置问题。如果您无法自行解决，请将其报告给维护者（例如，通过[在 AUR 页面的评论中发布您遇到的错误](#在软件包上添加评论)）。您也可以在 [AUR 问题、讨论和 PKGBUILD 请求论坛](https://bbs.archlinux.org/viewforum.php?id=38)寻求帮助。

问题原因可能并不简单，可能会涉及自定义到 `CFLAGS`、`LDFLAGS` 和 `MAKEFLAGS`。为排除由您的特定系统配置引起的问题，请在[干净的 chroot](../Arch%20开发/创建一个干净的%20chroot.md) 中构建包。如果在干净的 chroot 下构建仍然失败，则问题很可能出在 `PKGBUILD`。

请参阅[创建软件包#检查包的逻辑性](创建软件包.md#检查包的逻辑性)以获得关于使用 [namcap](../Arch%20项目/Namcap.md) 的信息。如果您希望审核 `PKGBUILD`，请将其发布到 [aur-general 邮件列表](https://lists.archlinux.org/mailman3/lists/aur-general.lists.archlinux.org/)以获得[软件包维护者](../团队/软件包维护者.md)和其他 AUR 成员的反馈，或发布到[创建和修改包论坛](https://bbs.archlinux.org/viewforum.php?id=4)。您还可以在 [Libera Chat](https://libera.chat) 上的 #archlinux-aur [IRC 频道](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md)中寻求帮助。

## 提交

用户可以使用 AUR 分享 `PKGBUILD`。参见 [AUR 提交准则](AUR%20提交准则.md)以获得更多细节。

## Web 接口翻译

参阅 AUR 源码中的 [i18n.md](https://gitlab.archlinux.org/archlinux/aurweb/-/blob/master/doc/i18n.md) 获得关于创建和维护 [AUR Web 界面](https://aur.archlinux.org)翻译的详细信息。

## 历史

最初，人们仅将 `PKGBUILD`、所需的补充文件和构建好的包上传到 `ftp://ftp.archlinux.org/incoming` 服务器。包及相关文件会暂存在此，直至一位“受信用户”（现已更名为[软件包维护者](../团队/软件包维护者.md)）看到这个程序并接管维护。

然后，受信用户仓库诞生了。社区中，特定的个人被授权托管他们自己的仓库，以供他人使用。在更灵活、更易用的目标下，AUR 在这个基础上扩展壮大。事实上，AUR 维护者曾一直被称作受信用户（Trusted user，TU），直到更名为软件包维护者。

在 2015 年 6 月 8 日到 8 月 8 日期间，AUR 从 3.5.1 版本迭代到 4.0.0，引入了通过 Git 仓库发布 `PKGBUILD` 的功能。 已存在的包会被丢弃，除非维护者手动地将它们移植到新的基础架构上。

### AUR3 包的 Git 仓库

Github 上的 [AUR 存档](https://github.com/aur-archive)中有一个包含移植时 AUR 3 的所有包的仓库。

或者，采用等价的 [aur-mirror](https://github.com/felixonmars/aur3-mirror/) 仓库。

## 常见问题

### 什么样的软件包能被放到 AUR？

AUR 中的软件包仅是编译脚本，即为 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 构建二进制文件的蓝图。只要你遵循了打包内容的许可条例，基本上符合[规则](AUR%20提交准则.md#提交软件包的规则)的软件包都可以提交。有时候，下载的链接具有 "you may not link" 条款（即不可分发），这时就不能提供下载链接，而是要用程序名称代替，用户需要用其它方式提前获取受限制的源代码。有疑问请及时询问。

### 怎么可以为 AUR 中的包投票？

见 [#给软件包投票](#给软件包投票)。

### 软件包维护者是什么？

参见 [Arch 术语#软件包维护者](Arch%20术语.md#软件包维护者)。

### Arch User Repository 和 extra 仓库有何区别？

Arch User Repository 是储存所有用户提交的 `PKGBUILD` 的地方，软件包需通过 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 手动生成。吸引足够多的社区关注及有软件包维护者支持的 `PKGBUILD` 会被收录进 [extra](官方仓库.md) 仓库（由软件包维护者维护），以二进制软件包形式提供，可以由 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 安装。

### AUR 中的软件包过期了，我该怎么做？

参见 [#将软件包标记为过期](#将软件包标记为过期)。

同时，你可以在本地编辑 `PKGBUILD` 来自行更新软件包。有时更新不需要修改构建流程，只用按需更新 `pkgver` 或 `source` 数组即可。

### `makepkg` 无法构建某个软件包，我该怎么办？

您很可能忘了点啥，参见 [#调试包构建过程](#调试包构建过程)。

### ERROR: One or more PGP signatures could not be verified! 我该怎么办？

最有可能的情况是您没有所需的公钥来验证下载的文件。详情参见 [Makepkg#验证签名](../../08_安全与加密/OpenPGP/Makepkg.md#验证签名)。

### 如何编写 PKGBUILD？

查阅 [AUR 提交准则#提交软件包的规则](AUR%20提交准则.md#提交软件包的规则) 和 [创建软件包](创建软件包.md)。

### 我想提交一个 PKGBUILD，希望别人帮忙检查错误。

您可以通过多种渠道提交您的材料供审核；请看 [#调试包构建过程](#调试包构建过程)。

### PKGBUILD（AUR 软件包）怎样才能被收录到 extra 软件仓库？

一般至少需要 10 票，并且要有软件包维护者愿意维护，否则即便有上千票也不会收录。当然，如果某个软件包维护者愿意维护一个软件包的话，投票数往往不是决定因素。

一些流行的软件包未被收录的原因一般是：

- Arch Linux 的软件仓库中已经有了别的版本。
- 没有再发布的授权许可。
- 那个软件包的功能只和 AUR 相关（例如，是个 [AUR helper](../../06_软件与应用/软件比较/AUR%20助手.md)）

另见[包进入 extra 仓库的规则](../../08_安全与加密/OpenPGP/软件包维护者指导原则.md#软件包进入_extra_仓库的规则)。

### 如何加速编译？

参见 [Makepkg#减少编译时间](../../08_安全与加密/OpenPGP/Makepkg.md#减少编译时间)。

### foo 和 foo-git 的区别是什么？

很多 AUR 软件包都包含稳定版本和开发版本。开发版本一般都会有代表版本控制系统的[后缀](../../01_安装与包管理/Arch%20打包准则/VCS%20软件打包准则.md#包名)。开发版本并不是为日常使用而设计的，不过可能包含新功能或者 bug 修复。由于执行 `makepkg` 时会从软件源获取最新的代码，因此 AUR 上的 `pkgver()` 并不代表上游的最新更改。这些软件包很可能会跳过对 [VCS 源代码](../../01_安装与包管理/Arch%20打包准则/VCS%20软件打包准则.md#VCS_源代码)的完整性效验。

同时请参阅 [系统维护#使用经测试的软件包](../../02_系统管理与内核/系统管理/系统维护.md#使用经测试的软件包)。

### 为啥某个软件包从 AUR 消失了？

可能是有软件包维护者认领了这个软件包，并把它收入到 [extra](官方仓库.md) 仓库中了。

软件包也可能因为不满足[提交软件包的规则](AUR%20提交准则.md#提交软件包的规则)而被删除，您可以在 [aur-requests 存档](https://lists.archlinux.org/archives/list/aur-requests@lists.archlinux.org/)中找到删除原因。

> **注意：**通常被删除软件包的 git 仓库仍被保留，细节请参考 [AUR 提交准则#请求](AUR%20提交准则.md#请求)。

### 我要怎么找出从 AUR 里消失的软件包？

对比 `pacman -Qm` 的输出（即在同步数据库中不存在的软件包清单）和 AUR 上的所有软件包清单：

```
$ comm -23 <(pacman -Qqm | sort) <(curl https://aur.archlinux.org/packages.gz | gzip -cd | sort)
```

### 如何得到包含所有 AUR 包的列表？

- [https://aur.archlinux.org/packages.gz](https://aur.archlinux.org/packages.gz)
- 使用来自 [python3-aur](https://aur.archlinux.org/packages/python3-aur/)AUR 的 `aurpkglist`。
- [新的 AUR 元数据存档](https://lists.archlinux.org/archives/list/aur-general@lists.archlinux.org/thread/D4YC6Y7L4T5VSEONUCLHOX2R4NJKNIDP/)

## 另请参阅

- [AUR 网页界面](https://aur.archlinux.org)
- [AUR 邮件列表](https://lists.archlinux.org/mailman3/lists/aur-general.lists.archlinux.org/)

## 1.18　Arch 是最好的！

*难度：高级 ｜ 篇幅：29.2 KB ｜ 所属：关于 Arch*

将要讲到：历史、代码

**Arch is the best** 项目是一个非常复杂的和精致的，自我提升和令人兴奋的项目（虽然或许有点过度工程），该项目给出了 Arch 的优越性的证明。

## 历史

这个有远见的项目最初是由 Arch 社区的长期成员 [lucke](https://bbs.archlinux.org/profile.php?id=2529) 在 2008 年 4 月设计的，它是一个简单的 shell 脚本，提供了"Arch is the best"的无可辩驳的证明。它是通过一个[论坛帖子](https://bbs.archlinux.org/viewtopic.php?id=47306)向全世界宣布的，从而启发了其他人的思想，他们立即开始将它移植到多种不同的语言中，包括编程语言和口头语言，以便地球上的每一个人都能充分欣赏和受益于这一革命性的发现。

## 代码

"Arch is the best"项目被移植到许多编程语言中。

**1C:Enterprise 7.7/8/8.1/8.2**
: 一种特定于过程域的编译动态类型化编程语言，主要类似于 VisualBasic，在俄罗斯和其他独联体国家广泛使用的"1C:Enterprise"产品中。

```
Предупреждение("Arch is the best!");
```

**ABAP**
: 高级业务应用程序设计语言。

```
REPORT zwhat_is_the_best.
WRITE 'Arch is the best'.
```

**Ada**
: 一种系统关键编程语言。

```
with Ada.Text_IO;
use Ada.Text_IO;
procedure ArchIsTheBest is
begin
   Put_Line("Arch is the best!");
end ArchIsTheBest;
```

**APL**
: 一种编程语言。

```
'Arch is the best!'
```

**AppleScript**
: 由 Apple Inc. 创建的一种脚本语言，自 System 7 起内置于 Classic Mac OS 中，并内置于所有 macOS 版本中。

```
display alert "Arch is the best!"
say "Indeed, Arch is the best."
```

**ArnoldC**
: 基于 Arnold Schwarzenegger 的一行程序设计语言。

```
IT'S SHOWTIME
TALK TO THE HAND "Arch is the best!"
YOU HAVE BEEN TERMINATED
```

**ATS**
: 一种使用依赖类型来提高程序可靠性的函数式编程语言。

```
implement main () = println! "Arch is the best!"
```

**Awk**
: 一种数据驱动的程序设计语言，用于处理基于文本的数据。

```
BEGIN {
   print "Arch is the best!"
}
```

**BASIC**
: 一种脚本语言，是 20 世纪 60 年代最常用的计算机编程语言之一，被认为是学生在使用 FORTRAN 等功能更强大的语言之前学习的第一步。

```
10 PRINT "Arch is the best!"
```

**Batch**
: 一种用于 Windows 的脚本语言，可用于自动执行任务或只是享受一些乐趣。

```
@echo off
echo Arch is the best!
pause
```

**Befunge**
: 被认为是第一种基于 ASCII 的二维通用通用语言（在某种意义上，"you could plausibly write Hunt the Wumpus in it"）。

```
<v"Arch is the best!"0
 <,_@#:
```

**BIRL**
: 类似于 ArnoldC，但是对于 Bambam 的。

```
HORA DO SHOW
    CE QUER VER ESSA PORRA? ("Arch is the best!\n");
    BORA CUMPADE 0;
BIRL
```

**Boo**
: 针对 .NET 和 Mono 的稳定的面向对象的静态类型编程语言，具有受 python 启发的语法，并特别关注通过语言和编译器可扩展性功能（例如宏和自定义编译管道）进行元编程。

```
print "Arch is the best!"
```

**Bourne shell**
: 原始程序，应该与任何 shell 兼容。

```
#!/bin/sh
echo "Arch is the best!"
```

**Bourne shell（备用）**
: 方便将输出传递到您喜爱的 IRC/email/IM 客户端。任何 shell 都能用。

```
#!/bin/sh
yes Arch is the best!
```

**Bourne shell（动态更新）**

```
#!/bin/sh
w3m -dump "https://wiki.archlinux.org/index.php/Arch_is_the_best" | sed -n '/^Translations/,/^Encodings/p'
```

**或**

```
 #!/bin/bash
 curl -s "https://wiki.archlinux.org/index.php?title=Arch_is_the_best&action=raw" | sed -n '/==Translations==/,$p' | sed "s/^\(.*\)$/* \1:/;t;s/^[^=]/  &/"
```

**brainfuck**
: 旨在建立最小编译器并符合图灵完备性的语言，其名字也暗示程式码很难读懂。

```
++>++++++>+++++<+[>[->+<]<->++++++++++<]>>.<[-]>[-<++>]
<----------------.---------------.+++++.<+++[-<++++++++++>]<.
>>+.++++++++++.<<.>>+.------------.---.<<.>>---.
+++.++++++++++++++.+.<<+.[-]++++++++++.
```

**C**
: 注意这个项目中使用的三个空格缩进，很像其他高级的语言使用的缩进。

```
#include <stdio.h>
#include <stdlib.h>
int main(void)
{
   puts("Arch is the best!");
   return EXIT_SUCCESS;
}
```

**C#**
: 旨在成为一种简单、现代、通用、面向对象的编程语言。

```
using System;
public class ArchIsTheBest
{
   static public void Main ()
   {
      Console.WriteLine ("Arch is the best!");
   }
}
```

**C++**
: Arch == Linux++

```
#include <iostream>
#include <cstdlib>
int main ()
{
   std::cout << "Arch is the best!" << std::endl;
   return EXIT_SUCCESS;
}
```

**COBOL**
: 一种简单、轻量级的编程语言。

```
    IDENTIFICATION DIVISION.
    PROGRAM-ID.  TheBest.

    PROCEDURE DIVISION.
        DISPLAY "Arch is the best!".
        STOP RUN.
```

**CoffeeScript**
: 一种可编译为 JavaScript 的编程语言。

```
alert 'Arch is the best!'
```

**Clojure**
: 在 JVM 上运行的 Lisp 方言。

```
(def translations {"english" "Arch is the best!",
                   "german" "Arch ist das Beste!",
                   "australian" "Arch is fair dinkum, mate!",
                   "h4x0r" "arhc 51 7he be57!",
                   "spanish" "¡Arch es el mejor!"})

(defn read-choice []
  (println "\nAvailable languages: ")
  (doall (map #(println (key %)) translations))
  (print "Enter language or Ctrl-c: ") (flush)
  (translations (read-line) :badinput))

(defn arch-is-the-best []
  (loop [choice (read-choice)]
    (case choice
      :badinput (do (print "\nBad input!\n")
                    (recur (read-choice)))
      (do (print "\n" choice "\n")
          (recur (read-choice))))))
```

**或**

```
(def translations {"english" "Arch is the best!",
                   "german" "Arch ist das Beste!",
                   "australian" "Arch is fair dinkum, mate!",
                   "h4x0r" "arhc 51 7he be57!",
                   "spanish" "¡Arch es el mejor!"
                   "street" "Arch iz da shizzle ma nizzle"})
(while 1
  (println "\nPick a language:\n" (map #(key %) translations) "\n language: ")
  (println (translations (read-line) "Not a valid language")))
```

**或**

```
(prn "Arch is the best!")
```

**Common Lisp**
: 在 SBCL 上测试，可以随意添加更多的翻译。

```
#!/usr/bin/sbcl --script
(defparameter *best-list* '((English "Arch is the best!")
            (Chinese "Arch, 她出类拔萃!")
          (German "Arch ist das Beste!")
          (Greek "Το Arch είναι το καλύτερο!")
          (Latin "Arch optimum est!")
          (French "Arch est le meilleur!")))
(defun aitb ()
  (format t "Available languages: ~{~{~@(~a~)~*~}~^, ~}.~%" *best-list*)
  (loop for input = (progn (format t "~&Input the desired language, (or 'quit'): ~%")
                           (force-output)
                           (read-line))
     if (string-equal input "quit")
     do (loop-finish)
     else
     do (let ((language-def
               (assoc input *best-list*
                      :key (lambda (lang) (symbol-name lang))
                      :test #'string-equal)))
          (if language-def
              (format t "~&~A~%" (second language-def))
              (format t "~&Invalid language.~%"))))
  (format t "~&May the Arch be with you!~%"))
(aitb)
```

**Common Lisp（备用）**
: 应该在任何实现上运行（Clisp，Allegro，SBCL ...）

```
(princ "Arch is the best!")
```

**Crystal**
: 一种面向对象的类似 Ruby 的语言。

```
puts "Arch is the best!"
```

**Crystal（通过 web 服务器）**
: 用于一次将消息分发给多个朋友。

```
# For giving the message to your friends
require "http/server"

server = HTTP::Server.new(80) do |context|
  context.response.content_type = "text/plain"
    context.response.print "Arch is the best!"
end

puts "Listening."
server.listen
```

**csh**
: 一个类似 C 的 shell.

```
#!/bin/csh
echo "Arch is the best!"
```

**CSS**
: 一种样式表语言，主要用于设置网页样式。

```
body * {
    display: none;
}

body::before {
    content: "Arch is the best!";
    font-family: monospace;
    font-size: 2.7rem;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}
```

**D**
: 一种 C 风格的语言。有后见之明，以及现代化的便利。

```
 import std.stdio : writeln;
 void main()
 {
     writeln("Arch is the best");
 }
```

**Dart**
: 谷歌的 JavaScript 杀手

```
 main(){
   print('Arch is the best');
 }
```

**Dogescript**
: 友好的 JavaScript

```
 console.loge with '                So Arch'
 console.loge with '     Much Good'
 console.loge with '                          Wow'
```

**Ebuild**
: Gentoo 的构建脚本格式。

```
DESCRIPTION="Arch is the best!"
SRC_URI="https://wiki.archlinux.org/index.php/Arch_is_the_best"

LICENSE="GFDL_1.3"
SLOT="0"
KEYWORDS=""
IUSE=""

DEPEND=""
RDEPEND=""

src_compile() {
einfo "Arch is the best!"
}
```

**Emacs Lisp**
: GNU Emacs 和 XEmacs 文本编辑器使用的 Lisp 编程语言的方言

```
 (message "Arch is the best!")
```

**Emojicode**
: 一种无分隔符，面向对象，命令式，高级的混合语言，以表情符号作为固定点和方法。

```
🏁 🍇
    😀 🔤Arch is the best!🔤❗️
🍉
```

**Elixir**
: 一种动态的功能语言，旨在构建可扩展和可维护的应用程序

```
IO.puts "Arch is the best!"
```

**Erlang**
: 并发的，垃圾收集的编程语言和运行时系统。

```
-module(arch).
-export([is_the_best/0]).
   is_the_best() -> io:fwrite("Arch is the best!\n").
```

**或在进程之间使用消息传递**

```
 -module(arch).
 -export([ultimate_question/0,the_answer/0]).
 the_answer() ->
     receive
         {Client,who_is_the_best} ->
             Client ! {self(),"Arch is the best!"};
         {Client,_} ->
             Client ! {self(),"Taco Taco Taco!"}
     end,
     the_answer().
 ultimate_question() ->
     Pid = spawn(arch,the_answer,[]),
     Pid ! {self(),who_is_the_best},
     receive
         {Pid,Response} -> io:format("~s~n",[Response])
     end.
```

**F#**
: 一种强类型，功能优先的编程语言，用于编写简单的代码来解决复杂的问题。

```
printfn "Arch is the best!"
```

**Factor**
: 基于高级堆栈的语言。

```
"Arch is the best" print
```

**FIM++**
: 可以使用 Java 类的一种冗长，命令式，动态类型和解释性语言。

```
Dear Princess Celestia: Letter About Arch Linux.
Today I learned:
    I wrote "Arch is the best!".
Your faithful student, Twilight Sparkle
```

**Forth**
: 基于堆栈的语言。

```
." Arch is the best" cr -- kiss way
```

**Fortran95**

```
program arch
   print *,"Arch is the best!"
end program arch
```

**Genie**
: 一种新的编程语言，它允许使用更现代的编程样式，同时能够毫不费力地本地创建和使用GObjects。

```
init
 print "Arch is the best"
```

**Gjs**
: GNOME 的 Javascript 绑定。它主要基于 Spidermonkey javascript 引擎和 GObject 自省框架。

```
#!/usr/bin/env gjs
print ('Arch is the best');
```

**Go**
: 由 Google 创建的一种语言，它是 C，C++和 Python 之间的一个可爱的孩子。

```
package main

import "fmt"

func main() {
 fmt.Println("Arch is the best!")
}
```

**Groovy**
: 使用 Java 虚拟机的一种敏捷和动态语言。

```
println 'Arch is the best!'
```

**Haskell**
: IO 是一种简单无问题的语言。

```
main = putStrLn "Arch is the best!"
```

**HTML**
: 一种用于创建和定义网页及其内容的标记语言。

```
<!DOCTYPE html>
<html lang='en'>
   <head>
      <title>Arch is the best!</title>
   </head>
   <body>
       Arch is the best!
   </body>
</html>
```

**Idris**
: 具有依赖类型的通用纯函数式编程语言。Haskell，但更疯狂。

```
module Main

main : IO ()
main = putStrLn "Arch is the best!"
```

**Io**
: 受 Smalltalk，Self，Lua，Lisp，Act1 和 NewtonScript 启发的纯面向对象编程语言。

```
"Arch is the best!" println
```

**Java**
: 这是一种非常便携的语言，几乎可以在任何东西上运行，甚至可以在您的烤面包机上运行！

```
public class ArchIsTheBest {
   public static void main(String[] args) {
       System.out.println("Arch is the best!");
   }
}
```

**JavaScript**
: 也称为 ECMAScript，这是一种基于原型的面向对象的脚本语言。

```
console.log('Arch is the best!');
```

**JavaScript（在网络浏览器中）**

```
alert('Arch is the best!');
```

**Julia**
: 数值计算的新方法。

```
println("Arch is the best!")
```

**Kotlin**
: JetBrains 试图赢得世界统治。

```
fun main() {
   println("Arch is the best!")
}
```

**LilyPond**
: 功能强大的音乐雕刻程序，具有类似 LaTeX 的直观输入语言。

```
\version "2.12.3"
\include "english.ly"
\header { title = "Arch is the best!" }
\score
{
   <<
      \relative c' { c4 e g c \bar "||" }
      \addlyrics   { Arch is the best! }
   >>
}
```

**LOLCODE**
: 为什么不呢？

```
HAI
CAN HAS STDIO?
VISIBLE "ARCH IS TEH PWNZ LOL!"
KTHXBYE
```

**Lua**
: 一种轻量级，可扩展的编程语言。

```
print "Arch is the best!"
```

**Malbolge**
: 为使编程尽可能困难而创建的语言。

```
bCBA@?>=<;:9876543210/.-,+*)('&%$#"!~}|{zyxwvutsrqponmlkjihgfedcba`_^]
\[ZYXWVUTSRQPONMLKJIHGFEDCBA@?>=<;:9y16543210/.-,+*)('&}C#"!~}|{zyxwvu
tsrqponmlkjihgfedcba`_^]\[ZYXWVUTSRQPONMLK-CgGFEDCBA@?>=<;:98x6543210/
.-,+*)('&%$#"!~}|u;yxwpun4rqpRhmf,jihgIe^$ba`_^]\[ZYXQVUTMqQPONMFjJI+A
eEDC%A:^>=<|:981U54t21*/.-&Jk)('&}C#"!aw={z\xwvun4lqpi/mlkjiKaf_%p
```

**Matlab**
: MathWorks开发的专有编程语言。

```
disp('Arch is the best!');
```

**Morpho**
: Morpho 是一种支持过程式、面向对象和函数式编程的多范式编程语言。

```
writeln("Arch is the best!");
```

**Myrddin**
: 一种旨在控制和简化的系统编程语言，具有强大的类型检查，泛型，类型推断，闭包和特征。

```
/* mbld -b aitb aitb.myr */
use std
const main = {
  std.put("Arch is the best!\n")
}
```

**NASM / Yasm (i686)**
: 请注意，该字符串位于 .text 部分中，感觉更好。

```
;nasm -f elf32 arch.asm
;ld -o arch arch.o
;./arch

section .text
global _start
_start:
mov edx,len
mov ecx,msg
mov ebx,1
mov eax,4
int 0x80
xor ebx,ebx
mov eax,1
int 0x80
msg: db "Arch is the best!",10
len equ $-msg
```

**NASM / Yasm (x86_64)**
: 采用了 AMD 的好用的新指令 *syscall*。

```
;nasm -f elf64 arch.asm
;ld -o arch arch.o
;./arch

section .text
global _start
s:
    db 'Arch is the best!',0ah
l equ $-s
_start:
    mov rax,1
    mov rdi,1
    mov rsi,s
    mov rdx,l
    syscall
    mov rax,60
    xor rdi,rdi
    syscall
```

**Nim**
: 可移植的轻量级编程语言。

```
echo "Arch is the best!"
```

**node.js**
: 一个基于 Chrome 的 JavaScript 运行时构建的平台，可使用事件驱动的非阻塞 I/O 模型轻松构建快速，可扩展的网络应用程序，从而使其轻巧高效，非常适合跨分布式设备运行的数据密集型实时应用程序。

```
console.log('Arch is the best!');
```

**node.js（http 服务器）**
: 一个可以发出"Arch is the best!"信息的 node.js 程序。使用 HTTP

```
require('http').createServer((req,res) => {res.writeHead(200, {'Content-Type': 'text/plain'});res.end('Arch is the best!');}).listen(80);
```

**Objective-C**
: 一种反射型，面向对象的编程语言，将 Smalltalk 样式的消息传递添加到 C 编程语言中。

```
NSLog(@"Arch is the best!");
```

**OCaml**
: Caml 编程语言的主要实现。

```
print_endline "Arch is the best!"
```

**Octave**
: 高级解释语言，主要用于数值计算。

```
printf("Arch is the best!\n")
```

**Ook!**
: 将 brainfuck 翻译成了猩猩的语言。

```
Ook. Ook. Ook. Ook. Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook. Ook. Ook. Ook! Ook? Ook. Ook? Ook! Ook? Ook! Ook! Ook. Ook? Ook. Ook. Ook? Ook. Ook? Ook! Ook? Ook. Ook! Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook. Ook? Ook! Ook. Ook? Ook. Ook? Ook! Ook. Ook? Ook. Ook! Ook? Ook! Ook! Ook? Ook! Ook. Ook? Ook! Ook? Ook! Ook! Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook? Ook! Ook? Ook. Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook. Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook? Ook! Ook! Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook? Ook! Ook? Ook. Ook! Ook. Ook. Ook? Ook. Ook? Ook. Ook. Ook! Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook. Ook? Ook. Ook? Ook. Ook! Ook. Ook. Ook? Ook. Ook? Ook. Ook. Ook! Ook. Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook. Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook. Ook? Ook. Ook? Ook. Ook! Ook. Ook. Ook? Ook. Ook? Ook! Ook! Ook! Ook! Ook! Ook! Ook! Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook. Ook. Ook. Ook! Ook. Ook? Ook. Ook? Ook. Ook. Ook. Ook! Ook. Ook! Ook? Ook! Ook! Ook? Ook! Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook! Ook.
```

**Pascal**
: 一种有影响力的命令式和过程式编程语言。

```
program ArchIsTheBest;
begin
  writeln('Arch is the best!');
end.
```

**Pepe**
: 一种使您想大吼大叫的编程语言。[在线运行](https://soaku.github.io/Pepe/#W2YsXy!chH!8gGYa-!d2!d4GH-!ca@K!k!kKXwGUa)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2022-09-17 ⓘ\]

```
# Arch
reeEeeeeeE reeEEEeeEe reeEEeeeEE REeEEeEeee Reee rEeeEeeeee reee

# is t
reeEEeEeeE RrEeEEEeeEE rEeEEEeEee reee

# he
Reee RrEeEEeeEeE rreEe rEEeerEEee reEe

# BEST!
reeEEeeeEe reee reeeEeeeeE
```

**Perl**
: 一种高级通用通用解释型动态编程语言。

```
#!/usr/bin/env perl
print "Arch is the best!\n";
```

**Perl 6**
: Perl 家族的最新成员。

```
#!/usr/bin/env perl6
say 'Arch is the best!';
```

**PHP**
: 通用脚本语言。

```
<?php
echo "Arch is the best!\n";
```

**Pixilang**
: 让我像素化。

```
print("Arch is the best!",0,0,#1897D1)
frame
```

**Pony**
: 面向对象，参与者模型，功能安全的高性能编程语言。

```
actor Main
  new create(env: Env) =>
    env.out.print("Arch is the best!")
```

**Portable GNU assembler**
: `as -o arch.o arch.s && ld -o arch -O0 arch.o`

```
   .section .data
archIsBest:
   .ascii  "Arch is the best!\n"
archIsBest_len:
   .long   . - archIsBest
   .section .text
   .globl _start
_start:
   xorl %ebx, %ebx
   movl $4, %eax
   xorl %ebx, %ebx
   incl %ebx
   leal archIsBest, %ecx
   movl archIsBest_len, %edx
   int $0x80
   xorl %eax, %eax
   incl %eax
   xorl %ebx, %ebx
   int $0x80
```

**PostScript**
: 与打印机通话的语言。

```
%!PS
/monospace 60 selectfont
10 420 moveto
(Arch is the best!) show
showpage
```

**Powershell**
: 基于 .NET 的基于任务的命令行 shell 和脚本语言。

```
Write-Output "Arch is the best!"
```

**Processing**
: 专为电子艺术和视觉设计而构建的开源编程语言和 IDE。

```
println("Arch is the best!");
```

**Prolog**
: 与人工智能和计算语言学相关的通用逻辑编程语言。

```
format('Arch is the best~n',[]).
```

**Python**
: 一种通用的高级编程语言。

```
print('Arch is the best!')
```

**QBASIC**
: 基于 QuickBASIC 的 BASIC 编程语言变体的解释器。

```
PRINT "Arch is the best!"
```

**R**
: 统计计算的语言（还有更多！）。

```
archIsBest <- function() { cat("Arch is the best!\n") }
archIsBest()
```

**Racket**
: Lisp-Scheme 系列中的通用多范式编程语言。

```
#lang racket

(let ([str "Arch is the best!\n"])
    (write-string str)
    (values))
```

**Ruby**
: 一种动态的，反射性的，通用的面向对象的编程语言。

```
#!/usr/bin/ruby -w
puts 'Arch is the best!'
```

**Rust**
: Rust 是一种系统编程语言，运行速度极快，几乎可以防止所有崩溃，并消除了数据争用。

```
fn main() {
    println!("Arch is the best!");
}
```

**Salt**
: Salt 是自动化框架

```
 salt '*' event.fire '{"data":"Arch Is the best!"}' 'arch/best'
```

**Scala**
: 在 JVM 上运行的一种多范例语言。

```
 object ArchIsBest extends App {
     println("Arch is the best!")
 }
```

**Scheme**
: Lisp 的方言。

```
(display "Arch is the best!\n")
```

**Seed**
: 一个库和解释器，通过 GNOME 平台动态地桥接 WebKit JavaScriptCore 引擎。

```
#!/usr/bin/env seed
print ('Arch is the best');
```

**Shakespeare Programming Language**
: 旨在"*make a language with beautiful source code* \[...\]"

```
Arch is the Best.

Arthur, a young man who is the best.
Isabella, a likewise young woman who be.
The Ghost, an undead who is the article.
Beatrice, a young woman who is an adjective.

                    Act I: Setting of the Variables.

                    Scene I: Setting of Isabella and Arthur.

[Enter Arthur and Isabella]

Arthur:
 You are as dirty as the square of the sum of a rotten smelly foul devil
 and a vile lie! You are as bold as the sum of yourself and an evil hog!

Isabella:
 Thou art as big as the square of a cute fair sweet flower! You are as proud
 as the sum of thyself and a cow.

[Exit Arthur]

                    Scene II: Setting of The Ghost.

[Enter The Ghost]

Isabella:
 You art as loving as myself. Thou are as huge as the sum of yourself and twice
 a red old hair. You are as cowardly as the sum of yourself and a fat goat!

[Exit Isabella]

                    Scene III: Setting of Beatrice.

[Enter Beatrice]

The Ghost:
 Thou are as good as the sum of Isabella and a mighty fine rich noble King.

[Exeunt The Ghost and Beatrice]

                    Act II: Printing Arch is the Best.

                    Scene I: Arch.

[Enter Arthur and Beatrice]

Beatrice:
 Speak thy mind! You art as peaceful as the quotient between thyself and the
 clearest Lord.

Arthur:
 Speak your mind!

[Exeunt Arthur and Beatrice]

[Enter Isabella and The Ghost]

The Ghost:
 Thou are as normal as the sum of thyself and a town. Speak thy mind!

Isabella:
 Speak your mind!

[Exit Isabella]

[Enter Arthur]

The Ghost:
 Speak thy mind!

[Exit Arthur]

                    Scene II: is.

[Enter Beatrice]

Beatrice:
 Thou are as old as the sum of yourself and a nose. Speak your mind!

The Ghost:
 You art as pretty as the sum of thyself and a face. Speak your mind.

[Exit The Ghost]

[Enter Arthur]

Beatrice:
 Speak thy mind.

                    Scene III: the.

Arthur:
 You are as blue as the sum of yourself and a hamster. Speak thy mind!

[Exit Arthur]

[Enter The Ghost]

Beatrice:
 You are as prompt as the sum of thyself and a lie! Speak your mind.

[Exit Beatrice]

[Enter Isabella]

The Ghost:
 You art as happy as the sum of thyself and a large moon. Speak thy mind!

[Exit The Ghost]

[Enter Arthur]

Isabella:
 Speak thy mind.

[Exit Arthur]

                    Scene IV: Best.

[Enter Beatrice]

Beatrice:
 Thou are as blue as the sum of the sum of a curse and thyself and a bad
 codpiece. Speak your mind! You art as lovely as the sum of the sum of the
 happiness and yourself and a blossoming flower. Speak thy mind.

Isabella:
 You are as healthy as the sum of yourself and a plague! Speak thy mind.
 Thou art as huge as the sum of thyself and a hero. Speak your mind!

[Exit Beatrice]

[Enter Arthur]

Isabella:
 You art as green as the sum of thyself and a mother. Speak your mind.

[Exeunt]
```

**Shoes**
: 使用 Shoes 作为 GUI 的 Ruby 版本。

```
Shoes.app :width => 135, :height => 30 do
    para "Arch is the Best!"
end
```

**Smalltalk**
: Smalltalk 是一种面向对象的动态类型的反射式编程语言。

```
Transcript show: 'Arch is the best!'.
```

**Solidity**
: 以太坊智能合约的面向对象编程语言。

```
pragma solidity ^0.6.0;

contract ArchIsTheBest {
  function archIsTheBest() external pure returns (string memory) {
    return "Arch is the best!";
  }
}
```

**SQL**
: 结构化查询语言，关系数据库的查询语言

```
SELECT 'Arch is the best!';
SELECT 'Arch is the best!' from dual; -- for Oracle DB
```

**Standard ML**
: 一种具有编译时类型检查和类型推断功能的通用，模块化，功能性编程语言。

```
print "Arch is the best!\n"
```

**Swift**
: Apple Inc. 开发的一种通用，多范式，已编译的编程语言。

```
print("Arch is the best!")
```

**Tcl/Tk**
: 一种脚本语言，通常用于快速原型制作，脚本化应用程序，GUI 和测试。

```
#!/usr/bin/env tclsh
puts "Arch is the best!"
```

**TrumpScript**
: 一种基于特朗普总统话语的脚本语言。

```
say it with me, "Arch is the best!";
america is great.
```

**UEFI**
: 可扩展的固件框架

```
#include <Uefi.h>
EFI_STATUS EFIAPI
ArchIsTheBest (
              IN EFI_HANDLE        ImageHandle,
              IN EFI_SYSTEM_TABLE  *SystemTable
              )
{
   SystemTable -> ConOut-> OutputString(SystemTable->ConOut, L"Arch is the best!\n");
   return EFI_SUCCESS;
}
```

**V**
: 简单，快速，安全，可编译的语言，用于开发可维护的软件。

```
fn main() {
        println('Arch is the best!')
}
```

**Vala**
: Vala 是一种新的编程语言，旨在将现代编程语言功能带给 GNOME 开发人员，而无需施加任何其他运行时要求，并且与使用 C 编写的应用程序和库相比，无需使用其他 ABI。

```
void main(string[] args) {
stdout.printf("\nArch is the best!\n\n");
}
```

**var'aq**
: [战士的编程语言](https://freshmeat.sourceforge.net/projects/varaq)

```
"Arch is the best!" cha'
```

**Verilog**
: 一种硬件描述语言，标准化为 IEEE 1364

```
module top;
  initial $display("Arch is the best!");
endmodule
```

**VHDL**
: VHSIC 硬件描述语言

```
use std.textio.all;

entity top is
end top;

architecture behaviour of top is begin
  process begin
    write (output, String'("Arch is the best!"));
    wait;
  end process;
end behaviour;
```

**VimScript**
: Vim 文本编辑器的脚本语言。

```
echo "Arch is the best!"
```

**Visual Basic**
: Microsoft 为其组件对象模型（COM）编程模型提供的第三代事件驱动编程语言和集成开发环境（IDE）。

```
Module Arch
  Sub Main()
      MsgBox("Arch is the best!")
  End Sub
End Module
```

**wenyan-lang**
: 古代汉语的编程语言。

```
吾有一言。曰「「阿祺，盡善矣。」」。書之。
```

**Wiring（Arduino）**
: 麻省理工学院开发的开源编程语言"处理"为基础。

```
void setup()
{
   Serial.begin(9600);
}
void loop()
{
   Serial.print("Arch is the best!");
}
```

**X11**
: X11 是独立于体系结构的系统，用于显示图形用户界面。

`cc -lX11 arch.c`

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include <X11/Xlib.h>

int main()
{
       Display *d;
       Window w;
       XEvent e;
       int s;

       if (!(d = XOpenDisplay(NULL))) {
               fprintf(stderr, "Couldn't open display, but Arch is the best!\n");
               exit(1);
       }

       s = DefaultScreen(d);
       w = XCreateSimpleWindow(d, RootWindow(d,s), 0, 0, 110, 20, 0,
                               0, WhitePixel(d,s));
       XSelectInput(d, w, ExposureMask | KeyPressMask);
       XMapWindow(d,w);

       while (1) {
               XNextEvent(d, &e);
               if (e.type == Expose) {
                       XDrawString(d, w, DefaultGC(d, s), 5, 15, "Arch is the best!", 17);
               }
       }

       XCloseDisplay(d);
       return 0;
}
```

**Z3**
: Microsoft Research 的定理证明器

```
(define-const arch String "Arch is the best")
(simplify (str.++ arch))
```

**Zig**
: 一种通用的编程语言和工具链，用于维护健壮，最佳和可重用的软件。打算弃用 C。

```
const std = @import("std");

pub fn main() !void {
    std.debug.warn("Arch is the best!\n", .{});
}
```

**Zimbu**
: 一种快速，易学且类似于 JS 的编程语言。

```
FUNC Main() int
  IO.write("Arch is the best!")
  RETURN 0
}
```

**Zsh**
: UNIX 命令解释器（shell），与 ksh 极为相似，但包含许多增强功能。

```
#!/bin/zsh -f
setopt extendedglob
print -- $(tput setaf 2) ${$(<<<${${${(@j: :)${(@s:_:)${:-What_Linux_is_the_best?}}}/* (#b)([A-Z]i)/Arch $match}} tr '?' '!')} $(tput sgr0)
```
