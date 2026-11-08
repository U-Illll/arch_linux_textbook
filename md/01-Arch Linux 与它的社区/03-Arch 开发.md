# 第 3 章　Arch 开发

> 本章要把"想帮忙"变成"能动手"：Bug Day 与 AUR 扫除日说清何时何地与谁协作，创建一个干净的 chroot 解决本地验证环境，漏洞报告准则则管住从发现到追踪全程。建议先读漏洞报告准则和创建一个干净的 chroot：前者决定你提交的内容能否被接手，后者决定结论是否可信，两节都是必读。Bug Day 篇幅最短，可略过；AUR 扫除日多是模板与名单分工，暂不参与可跳过。
> 
> 最易误解的是 chroot，以为装好能跑就算干净，其实它隔离的是本机已有的依赖与设置；其次是发现问题就直奔新建报告，开始之前那一步最常被跳过。

<!-- 本章 4 节，共 29 KB -->

## 3.1　Bug Day

*难度：入门 ｜ 篇幅：1.2 KB ｜ 所属：Arch 开发*

将要讲到：什么时候？、哪里？、谁？

Bug Day 是致力于提高对我们溢出的 [bug tracker](https://bugs.archlinux.org/) 认识的一天。今天的目标是使社区参与尝试帮助我们消除尽可能多的错误。

## 什么时候？

每当开发人员感到无聊时，就会发起 Bug Day。

## 哪里？

错误本身记录在 [bug tracker](https://bugs.archlinux.org) 中。

有关错误的讨论应在 [IRC](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md) 上的 #archlinux-bugs 中进行。我们将设法在 Bug Day 期间至少让一位开发人员活跃。

## 谁？

允许，邀请和鼓励**所有人**参加 Bug Day。

## 3.2　AUR 扫除日

*难度：入门 ｜ 篇幅：4.6 KB ｜ 所属：Arch 开发*

将要讲到：关于、模板、About、Template (Package list)、Template (TU)

## 关于

AUR 扫除日（AUR Cleanup Day）是隔年（奇数年）9 月 20 号进行一次的活动。

[AUR](../关于%20Arch/Arch%20用户软件仓库.md) 上存在大量可清理的过时软件包。你可以使用以下模板，将建议清理的软件包提交到公告邮件中提供的协作文档中，也可以将它们提交到 [aur-general 邮件列表](https://lists.archlinux.org/mailman3/lists/aur-general.lists.archlinux.org/)，或是直接发起[请求](../关于%20Arch/AUR%20提交准则.md#请求)。[软件包维护者](../团队/软件包维护者.md)们会共同决定要被移除的软件包清单。

请加入 [#archlinux-aur](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md) 进行协作和探讨。

## 模板

```
# AUR Cleanup Day September 20th 20XY

## About

AUR Cleanup Day is a bi-yearly (odd years) event on the 20th of September.

The [AUR](https://wiki.archlinux.org/title/Arch_User_Repository) has a large number of obsolete packages which could use cleaning up. Post suggestions of packages below, submit them to the [aur-general mailing list](https://lists.archlinux.org/listinfo/aur-general), or file [requests](https://wiki.archlinux.org/title/AUR_submission_guidelines#Requests) directly. [Package Maintainers](https://wiki.archlinux.org/title/Package_Maintainers) will get together and confirm which packages should be removed.

Join [#archlinux-aur](https://wiki.archlinux.org/title/Arch_IRC_channels) to collaborate and chat.

## Template (Package list)

### Candidates

Check for the package in the sorted lists below before adding.

- [PKGNAME](https://aur.archlinux.org/packages/PKGNAME) - Reason

#### Informative heading

- [PKGBASE](https://aur.archlinux.org/pkgbase/PKGBASE) - Reason

### Possible reasons

- Does not work anymore

- Deprecated by [PKGNAME](https://aur.archlinux.org/packages/PKGNAME)

- Obsoleted by [PKGNAME](https://aur.archlinux.org/packages/PKGNAME)

- Replaced by [PKGNAME](https://aur.archlinux.org/packages/PKGNAME)

- Duplicate of [PKGNAME](https://aur.archlinux.org/packages/PKGNAME)

- Project page and sources are not available

- "Dead" project; it's very old and does not work with the latest versions of its dependencies

- "Dead" project and too old to be useful

- Project page and sources are not available

- Not needed anymore (it's for old PKGNAME), broken source link

- Old dev-version (CVS/SVN/etc), PKGNAME project uses Git now

- Is it needed in Arch Linux?

- Broken links (see comment by USERNAME)

- Outdated for a long time

- This one should be renamed

- Deprecated by upstream

- Outdated, orphaned

- Broken links, too old, not maintained

- Included in extra/PKGNAME

- Already included in core/PKGNAME

- Old beta version, broken source link, replaced by [PKGNAME](https://aur.archlinux.org/packages/PKGNAME)

- Maintainer wrote that it may be deleted

- Dropped upstream

- Depends on [PKGNAME](https://aur.archlinux.org/packages/PKGNAME) and has not been updated for N years...

- Development discontinued, project page deleted and current version does not work

- Too old to be useful, broken source link -- hm, what is USERNAME's Opinion?

- This package is no longer needed (see comments)

## Template (TU)

**For editing by TUs only!**

### Packages to Remove

In extra:

- [PKGNAME](https://aur.archlinux.org/packages/PKGNAME) - Replaced by PKGNAME

### Packages to Keep

- [PKGNAME](https://aur.archlinux.org/packages/PKGNAME) - Reason

#### Possible reasons

- It is for compiling old stuff, let's keep it, does not harm

- Seems to be actively maintained

- Still useful, because ...

- So this package should maybe be orphaned, no obvious reason to delete it

- Needed by PKGNAME

- Package has new and active maintainer

- Seems to be down sometimes, but sometimes it works again. Package is maintained and has votes

- Should be rewritten, not dropped

- I orphaned the package

- It's not dead, it only needs some changes to work again

- Package builds with little tweaking

- Builds fine with a little patch in pkgrel N
```

## 3.3　创建一个干净的 chroot

*难度：进阶 ｜ 篇幅：8.7 KB ｜ 所属：Arch 开发*

将要讲到：使用 clean chroot 的意义、便捷方式、传统方法、处理大规模重构、小提示和小技巧

## 使用 clean chroot 的意义

clean chroot 可以直译为 干净/洁净的 [chroot](../../02_系统管理与内核/系统恢复/Chroot.md)。在 clean chroot 环境中构建可以避免软件包依赖缺失问题。不然可能会出现意外的动态链接或是在 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 依赖数组 `depends` 中出现未声明的依赖。此外，它还允许用户参与 [core-testing](../关于%20Arch/官方仓库.md#core-testing) 或 [extra-testing](../关于%20Arch/官方仓库.md#extra-testing) 测试仓库情况下，为稳定仓库 ([core](../关于%20Arch/官方仓库.md#core)、[extra](../关于%20Arch/官方仓库.md#extra)) 构建软件包。

## 便捷方式

为了快速在 clean chroot 环境中构建软件包而无需复杂配置，你可以使用 [devtools](https://archlinux.org/packages/?name=devtools)包 中的辅助脚本来帮助你。

这些辅助脚本应在 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 的同一目录中调用，就像使用 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 时一样。例如，`extra-x86_64-build` 将会自动从 `/var/lib/archbuild` 的 clean chroot 模板中创建 chroot 环境，更新它，并为 [extra](../关于%20Arch/官方仓库.md#extra) 仓库构建软件包。对于 [multilib](../关于%20Arch/官方仓库.md#multilib) 构建则使用无架构参数的 `multilib-build`。请参考下表了解不同目标仓库和架构对应的构建脚本。

`-c`参数用于重置chroot模板，这在环境损坏时非常有用。在建立全新的 clean chroot 时不需要此参数。

> **注意：**
>
> - *[core](../关于%20Arch/官方仓库.md#core)* 仓库被省略，因为这些软件包必须先通过 *[core-testing](../关于%20Arch/官方仓库.md#core-testing)* 仓库测试才能进入正式仓库
> - 如果目标是为本地使用构建 *[core](../关于%20Arch/官方仓库.md#core)* 仓库的软件包，建议直接使用稳定仓库而非测试仓库。此时可以直接使用`extra`构建脚本

| 目标仓库 | 架构 | 构建脚本 | 使用的 Pacman 配置文件 |
| --- | --- | --- | --- |
| extra | x86_64 | extra-x86_64-build | /usr/share/devtools/pacman.conf.d/extra.conf |
| core-testing / extra-testing | x86_64 | extra-testing-x86_64-build | /usr/share/devtools/pacman.conf.d/extra-testing.conf |
| core-staging / extra-staging | x86_64 | extra-staging-x86_64-build | /usr/share/devtools/pacman.conf.d/extra-staging.conf |
| multilib | x86_64 | multilib-build | /usr/share/devtools/pacman.conf.d/multilib.conf |
| multilib-testing | x86_64 | multilib-testing-build | /usr/share/devtools/pacman.conf.d/multilib-testing.conf |
| multilib-staging | x86_64 | multilib-staging-build | /usr/share/devtools/pacman.conf.d/multilib-staging.conf |

> **提示：**[pkgctl-build(1)](https://man.archlinux.org/man/pkgctl-build.1)会自动选择正确的构建脚本在 clean chroot 中构建

## 传统方法

### 设置 chroot 环境

[devtools](https://archlinux.org/packages/?name=devtools)包 提供了用于创建 clean chroot 并在其中构建软件包的功能，请确保您已安装该软件包。

之后，为创建 clean chroot，请创建一个新文件夹用于存放 chroot，比如 `$HOME/chroot/`：

```
$ mkdir ~/chroot
```

之后定义 `CHROOT` 变量:

```
$ CHROOT=$HOME/chroot
```

现在就可以开始创建 clean chroot 环境了：

> **注意：**chroot 需要 `root` 文件夹的访问权限，因为 `$CHROOT` 目录需要复制其中的必要文件来创建一个干净的工作环境

```
$ mkarchroot $CHROOT/root base-devel
```

> **注意：**
>
> - 若您经常使用 clean chroot 构建软件包，在 `$HOME/.bashrc` 里添加 export 命令以引入 `CHROOT` 变量是一个不错的选择
>
> - 在 [btrfs](../../02_系统管理与内核/文件系统/Btrfs.md) 文件系统中，chroot 会以分卷（subvolume）的形式被创建，因此您必须以 root 用户的身份运行 `btrfs subvolume delete $CHROOT/root` 才能删除这个分卷。

所有相关信息，比如打包者名称、makeflags 等都在 `~/.makepkg.conf` 中，毋庸置疑，您需要编辑 `~/.makepkg.conf` 以确保其被正常构建。别忘了修改 `$CHROOT/root/etc/pacman.d/mirrorlist` 以更换[镜像源](../关于%20Arch/镜像源.md)。

如果需要，您也可以在 `$CHROOT/root/etc/pacman.conf` 中启用 [testing](../关于%20Arch/官方仓库.md) 软件仓库以在 chroot 中获取测试版本的软件包。

> **注意：**`~` 和 `$HOME` 变量在"makechrootpkg"构建脚本中会被自动定义为 `/root/`

#### 自定义 pacman.conf

或者，提供一个自定义的 `pacman.conf` 和 `makepkg.conf`，其中包含以下内容：

```
$ mkarchroot -C <pacman.conf> -M <makepkg.conf> $CHROOT/root base-devel
```

> **警告：**在首次创建 clean chroot 时使用自定义的 `pacman.conf` 或 `makepkg.conf` 可能会导致对 chroot 环境进行预期外的自定义调整。*请谨慎使用。*

### 在 chroot 里构建

首先，请确保 base chroot (`$CHROOT/root`) 是最新的：

```
$ arch-nspawn $CHROOT/root pacman -Syu
```

其次，在包含其 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 的目录中调用 `makechrootpkg` 来构建软件包：

```
$ makechrootpkg -c -r $CHROOT
```

> **注意：**传递 `-c` 参数给 `makechrootpkg` 可确保在构建前清理工作 chroot (`$CHROOT/$USER`)。

#### 预装必要依赖包

要在 `$CHROOT/root/pacman.conf` 中启用的仓库无法提供所需依赖的情况下构建软件包，请使用 `-I *package*` 预先将它们安装到工作 chroot 中：

```
$ makechrootpkg -c -r $CHROOT -I build-dependency-1.0-1-x86_64.pkg.tar.xz -I required-package-2.0-2-x86_64.pkg.tar.xz
```

#### 向 makepkg 传递参数

要向 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md#Usage) 传递参数，请在 [end-of-options marker (选项结束符)](https://wiki.bash-hackers.org/dict/terms/end_of_options) 之后列出它们；例如，若要强制执行 `check()`：

```
$ makechrootpkg -c -r $CHROOT -- --check
```

## 处理大规模重构

处理大型重构最简洁的方法是使用 *staging* 仓库。先针对 *extra* 构建第一个软件包并推送到 *staging* 仓库。然后将后续所有软件包都基于 *staging* 重新构建，并一同推送到那里。

如果无法使用 *staging* 仓库，也可以通过如下命令，使用自定义软件包进行构建：

```
# extra-x86_64-build -- -I ~/packages/foobar/foobar-2-1-any.pkg.tar.xz
```

你可以通过多次使用 `-I` 参数来指定安装多个软件包。

处理大规模重构 (Major Rebuild) 有一种更简单但也相对“不够干净 (dirtier)”的方法：将所有已构建的软件包直接安装到 chroot 环境中，且全程不对其进行清理。使用以下命令构建第一个软件包：

```
# extra-x86_64-build
```

使用以下命令构建后续的所有软件包：

```
# makechrootpkg -n -r /var/lib/archbuild/extra-x86_64
```

运行 namcap (即 `-n`参数) 意味着会将软件包安装到 chroot 中。`*-build`脚本默认也会执行此操作。

## 小提示和小技巧

### 在tmpfs中构建

若系统拥有足够内存，可为 [devtools](https://archlinux.org/packages/?name=devtools)包 构建脚本指定 [tmpfs](../../02_系统管理与内核/文件系统/Tmpfs.md) 文件系统。

```
# mount --mkdir -t tmpfs -o defaults,size=20G tmpfs /mnt/chroots/arch
# extra-x86_64-build -c -r /mnt/chroots/arch
```

## 3.4　漏洞报告准则

*难度：进阶 ｜ 篇幅：14.4 KB ｜ 所属：Arch 开发*

将要讲到：开始之前、新建一个 Bug 报告、追踪 Bug

在 [Arch Linux 问题跟踪系统](https://bugs.archlinux.org/)报告问题是[帮助社区](../关于%20Arch/参与贡献.md)的一种方式。然而，质量不高的问题报告却会起反作用，浪费开发者的时间。此文档将像那些愿意帮助社区的人给出有效报告问题和解决问题的指南。

参见 Simon Tatham 的[如何有效的报告问题](https://www.chiark.greenend.org.uk/~sgtatham/bugs.html)。

## 开始之前

准备详细规范的问题报告并不难，但是需要报告者付出精力。**报告前的工作才是最重要的。**不幸的是，很少有人花时间做好这个工作。

下面的步骤将指导您准备问题报告。

### 查找重复问题

当你遇到问题或要求一个新功能时，极有可能有人也遇到了同样的问题，产生同样的想法。一个问题报告可能已经存在。这时，不要创建重复问题，请[跟踪原有问题](#追踪_Bug)。

全面查找已有信息，包括：

- [Arch Linux 论坛](https://bbs.archlinux.org/): 论坛通常是用户寻求帮助和分享解决方案的第一处。就算当时问题可能没有解决，各位用户追加的信息和讨论能帮助你找到正确的方向。
- [Arch Linux Bugtracker](https://bugs.archlinux.org/) （Arch Linux Bug 管理系统）: 问题可能已经由其他的用户提交给了开发者。重复的问题没有什么帮助，会被直接关闭。在高级中选择全部状态,可以同时搜索已解决和未解决的问题；问题标题可能不带要搜索的关键字，请在高级中选择'搜索详情' 和 '搜索评论' 。
- 在[Google](https://www.google.com) 或者您常用的搜索引擎中搜索: 试着搜索程序名称、版本和相关的错误消息（如果有的话）。
- **上游**论坛,邮件列表和 Bug 回报系统: 如果 Arch Linux 不是造成 Bug 的原因，这个 Bug 应该提交到上游（而不是 Arch Linux）。试着搜索最近关闭的 Bug 报告和其它正在进行中的报告，你所遇到的 Bug 可能已经在上游被修复了。
- **其它发行版的论坛**: 自由软件社区很宏大，有想法的不只有 Archer ! 考虑一下搜索其它发行版的论坛，例如 [Gentoo Forums](https://forums.gentoo.org/), [FedoraForum.org](http://forums.fedoraforum.org/), 和 [Ubuntu Forums](https://ubuntuforums.org/)。

### 上游还是 Arch？

Arch Linux 是一个 GNU/Linux *发行版* . Arch 开发者们和 Trusted User 们负责从各处编译,打包并分发软件.**上游**意味着*来源* – 就是 Arch Linux 所分发的软件的来源.例如 FireFox 浏览器是由 Mozilla 开发的.

**如果 Arch 不是造成 Bug 的主要原因,把 Bug 报告提交到 Arch 开发者并不能解决问题.**

通过向上游提交 Bug 报告,你不只帮助到了 Arch Linux 用户和开发者,同时也是在帮助自由软件社区的其他用户们.这样其它发行版的用户也能从中获得解决方案.

如果你从上游获得了相关的信息,也许可以把它们发送到 Arch Linux Bugtracker ,这样 Arch 开发者和用户们便能从中了解一些信息.

所以 Arch Linux 负责什么?

- **Arch Linux 项目**: [pacman](../../01_安装与包管理/包管理器/Pacman.md), [AUR](../关于%20Arch/Arch%20用户软件仓库.md), [mkinitcpio](../../01_安装与包管理/Initramfs/Mkinitcpio.md), Arch Linux 网站. 不知道这个包是不是 Arch 的项目? 查找项目信息 (`pacman -Qi *package_name*` 或者通过网站) 并从中寻找上游网址 .

> **注意：**Report issues for [Arch Linux software projects](../关于%20Arch/参与贡献.md#Software_projects) in the project's respective issue tracker on [gitlab.archlinux.org](https://gitlab.archlinux.org/) or [GitHub](https://github.com/archlinux) instead of bugs.archlinux.org.

- **打包**: 打包包含从上游获取源代码,用相关的选项编译,确认它能在 Arch 上正确安装与运行等环节. Arch 的 打包环节不包含增加新功能或是修复 Bug ,这些是上游开发者的工作.

如果某个 Bug/Feature 不是 Arch 的原因,请把它提交给上游.另参阅[有些不是 Bug 的原因](#有些不该提交_Bug_报告的原因).

### Bug 或者 Feature？

**bug**
: 某些该工作的地方没有按开发者的预期工作.
**feature**
: something which software does or would do if somebody coded it.

#### 有些不该提交 Bug 报告的原因

- 你想要一个还没有的功能（这是功能请求）。
- 已经提交给上游的 Bug
- 上游已经修复但是 Arch 还没更新软件包。
- **某个软件包过期了**，这种情况请使用 [Arch Linux 网站上的](https://archlinux.org/packages/) **标记这个包已过期**按钮.
- 某个软件包没使用来自其它发行版或其它社区的补丁,**补丁应该提交到上游**.
- 某个软件包中的**非核心功能**没有启用.这种情况应该提交功能请求.
- 某个软件包没有包含 [freedesktop](https://www.freedesktop.org) 相关文件,例如 **.desktop 文件**或是**图标**.只有在上游提供了这些文件但软件包中没有使用的情况下才是个 Bug .如果该上游没有提供这些文件,那么应该向上游提交功能请求.

#### 有些不是 Feature 的原因

- 这是个 Bug ......
- **某个软件包过期了**，这种情况请使用 [Arch Linux 网站上的](https://archlinux.org/packages/) **标记这个包已过期**按钮.
- 某个软件包没使用来自其它发行版或其它社区的补丁,**补丁应该提交到上游**.

### 收集有用的信息

这是一个关于你的 Bug 报告中应该包含的信息的列表:

- **软件包的具体版本**,类似于"最新的","今天的","\[Extra\]中的"这样的描述毫无意义.
- 软件包所使用的库 (可以在 PKGBUILD 中的 *depends* 中找到 ),它们可能和问题相关.如果你不知道确切的信息,那就等别人来问......
- 如果遇到了硬件问题,记得提供内核版本和硬件生产厂家。
- 这个功能**是否有正常工作过** .如果有的话,什么时间停止工作的.
- 如果有的话,提供其它**相关的日志**这些信息可能会帮助找到问题所在:
    - Systemd 日志.如果使用[syslog-ng](https://archlinux.org/packages/?name=syslog-ng)包, `/var/log/messages` 包含内核和硬件相关问题的信息.
    - `/var/log/Xorg.0.log` , `/var/log/Xorg.2.log` 或其他 Xorg 日志包含显示相关的问题的信息.
    - 通过 **verbose** 或是 **debug** 模式在终端运行你的程序并收集其输出 (如果有的话,请参阅你的程序提供的文档) . 对于母语不是英语的用户,你也许需要在终端中设置相应的环境变量 (例如`export LC_ALL="C"`) 以使程序以英文输出.这样更多的人便可以读懂它.例如在终端下以 `--verbose` 选项运行 *foobar*:

```
$ export LC_ALL="C"
$ foobar --verbose
```

这样改变环境变量只对当前终端生效.

如果需要提供大量的文本,例如 dmesg 的输出或是 Xorg 日志,最好把它们保存到文件中并随着 Bug 报告提供. **但是请保证提供的文件和 Bug 相关**.

- **复现 Bug 的方法**.这很重要,因为这样可以帮助其他人测试 Bug 是否修复和寻找潜在的修复方案.

- **堆栈跟踪** .包含程序调用过程的列表,这可以帮助定位问题的位置(特别是那些导致程序崩溃的问题).[调试/获取跟踪数据](../../01_安装与包管理/包开发/调试_获取跟踪信息.md)包含了使用[gdb](https://archlinux.org/packages/?name=gdb)包 (The GNU Debugger) 生成堆栈跟踪的详细信息.

## 新建一个 Bug 报告

在你收集到需要的信息并且确定是个 Bug 或者功能请求以后,你就可以提交 Bug 报告或者功能请求了.

### 创建账户

首先在 [Arch 的 Bug 回报系统](https://bugs.archlinux.org/)上注册一个账户,像 Wiki 和论坛一样简单.

### 各个项目

如果认为某个 Bug 或功能请求确实和 Arch 相关,应该将其放置在正确的项目里.在创建报告时从左侧选择一个正确的项目:

- **Arch Linux** - *testing*, *core*, 或 *extra* 中的软件包的问题. 也包含 Arch Linux 的文档,网站(除了 AUR) 和安全问题.
- **Arch User Repository (AUR)** - AUR 网站代码和服务器相关.记住并不包含访问AUR中软件包的第三方程序.
- **Community Packages** - *community* 中的软件包 . 这并不包含 *unsupported* (AUR) 中的软件包.
- **Release Engineering** - 所有和发行相关的问题 (iso等......)

对于 AUR 上软件包的问题,请使用 AUR 网站上的评论功能提交给 AUR 上该软件包的维护者.

### 摘要

请编写一个简洁且有描述性的摘要,下面是一些建议:

- **不要写成** "*pkgname* is broken after the last update" 这样的,这不光没描述性而且没人知道 Arch 中的 "after last update" 是啥时候.
- 用方括号表示软件包名称,例如 "**\[*pkgname*\]** 3.0.5-1 breaks copy-paste feature" .合理的命名可以帮助开发者搜索和排列报告.
- 别太长,因为太长的内容会在列表中被截断.

> **提示：**[Flyspray](../../07_开发与工具/问题跟踪系统/Flyspray.md) will try to parse URLs containing `@` (e.g. links to mailing list archives) as email address links and fail miserably. To post such URLs, make sure to URL-encode `@` as `%40`.

### 严重性

选择一个*严重性等级*不会帮助更快的修复 Bug .这只是让关键的问题易于搜索和被开发者标记.

Here is a general usage of severities: 常见的严重等级有这几种:

- **Critical**(严重),导致系统崩溃或启动失败,可能影响到其他用户的问题.或是对外服务的安全漏洞.
- **High**(高) - 程序的主要功能不工作,或是不那么严重的安全问题等等.
- **Medium**(中等) - 程序的非主要功能不工作,不遵循 UNIX 规范等等.
- **Low**(低) - 程序的可选功能(插件或是编译时选项)不工作.
- **Very Low**(很低) - 翻译或是文档错误,偶尔会是功能请求.

### 包括相关信息

这也许是 Bug 报告中最困难的一个阶段,前面的[收集有用的信息](#收集有用的信息)一节应该包含了需要的信息;如果不知道要提供什么的话,记得"多多益善".

参见 Simon Tatham 的[如何有效的报告问题](https://www.chiark.greenend.org.uk/~sgtatham/bugs.html)。

不过开发者可能会要求你提供更多信息,幸运的是在交谈过几次以后你应该会知道该提供哪些信息了.

短小的信息可以包含在你的报告里,长的信息请使用附件功能随附在报告里.

## 追踪 Bug

**不要认为你提交完 Bug 报告就结束了**! 开发者和其他用户可能会询问你细节或是提供一些方法.没有持续的反馈会让 Bug 报告有始无终,同时也会惹恼用户和开发者们.

### 投票与监视

你可以为同样影响到你的 Bug **vote** (投票).投票会让开发者察觉有多少人受到了这个 Bug 的影响.但是如果你遇到了同样的问题,提交相关的信息是比投票更快的解决方法.

**Watching** (监视/跟踪) 一个 Bug 很重要,你会收到关于你跟踪的 Bug 的进展的邮件.如果你提交了一个报告,请确保选中了 "Notify me whenever this task changes" (在 Task 改变时通知我) 选项.

### 回答追加的问题

其它人花时间阅读报告并尝试帮助你修复问题,你也应该尽所能向其他人提供你能做到的帮助.收不到回应不仅可能会让你的 Bug 难以修复,还会让帮助你的人失去信心.

**如果其他人需要更多信息,请尝试提供给他们.也请你尝试一下可能的解决方案。**.

开发者会关闭那些有新回复而提交 Bug 的人超过几周或是一个月没有回复的 Bug 报告。

### 软件新版本发布时更新问题报告

有时某个 Bug 之在某些版本中出现，在新版本中已经修复，遇到这种情况的话记得在 Bug 报告中提出来然后提交一个关闭请求.

### 关闭已经解决的问题

有些时候用户可能自己解决了自己遇到的 Bug ,遇到这种情况的话记得在 Bug 报告中提出来,**附上解决方案的参考链接**,然后提交一个关闭请求.

### 问题的状态

一个 Bug 可能有下面这些状态:

- **Unconfirmed**(未确认) - 这是默认状态,表示还没人确认或是复现这个 Bug.

- **New**(新的/已确认) - 表示这个 Bug 已经确认,但还没关联到相关软件的负责人.通常是因为还没确认哪个软件引发的 Bug 的缘故.

- **Assigned**(已分配) - 这个 Bug 已经分配到与之关联的开发者.不过这不意味着只有这个开发者会帮忙解决这个 Bug .这只是表示开发者会 关注这个Bug,例如检查任何的修补,发表解决方案和在需要时关闭 Bug 报告.所以不要直接和这个被分配的开发者联系......

- **Researching**(研究中) - 有人正在寻求解决方案. 这通常表示这个 Bug 可能呢个需要更多经验丰富的用户帮忙解决.

- **Waiting on Response**(等待回复) 和 **Requires testing**(需要测试) - 有人提供了更多信息或是提供了一个可能的解决方法,但是需要反馈.**Watching** (监视/跟踪) 一个 Bug 很重要,因为你可能会被开发者要求提供更详细的信息.

- **A task closure has been requested**(已提交关闭请求) - 这不是最终状态,但你也许会发现某些 Bug 报告是这样的状态.和它关联的开发者会决定是否关闭它.

- **Closed**(已关闭) - 可能因为这不是个 Bug [有些不该提交 Bug 报告的原因](#有些不该提交_Bug_报告的原因)或者已经被解决.

像开发者和 TU 这样的人负责管理和更新各个 Bug 的状态.
