# 第 8 章　GNU

> 本章面对的是 GNU名下那一大批软件：名字相近，职责却分属内核、基本系统组件、工具链等层。必读篇幅最大的《GNU》，先看清组件分布再决定读哪节；做开发绕不开《GNU Compiler Collection》，长期挂终端任务则读《GNU Screen》。《GNU/文档》只涉及文档格式，《GNU Guix》《GNU Radio》各属独立体系，用不到这些的可跳过。最容易踩的坑是把 GNU 当成一个发行版或一个软件包——它是横跨多层的集合，安装卸载都得按单个组件分别处理，Guix 的构建与卸载尤其如此。

<!-- 本章 6 节，共 49 KB -->

## 8.1　GNU/文档

*难度：入门 ｜ 篇幅：0.9 KB ｜ 所属：GNU*

将要讲到：Texinfo

## Texinfo

GNU 软件以 [Texinfo](https://www.gnu.org/software/texinfo/) 排版规则来编排文档。您可以使用 `info` 程序查阅 info 文档，它来自 [texinfo](https://archlinux.org/packages/?name=texinfo)包 软件包。

大部分 GNU 软件都提供了 [man 手册](../../07_开发与工具/命令行/Man%20手册.md)， 但 info 文档内容往往更加全面（但普遍缺少中文译文）。若要浏览 info 文档，只需输入：

```
$ info 页面名称
```

## 8.2　GNU Radio

*难度：入门 ｜ 篇幅：2.1 KB ｜ 所属：GNU*

将要讲到：安装、疑难解答

[GNU Radio](https://wiki.gnuradio.org/index.php/Main_Page) 是一个提供了信号处理模块来实现软件无线电的开源且免费的SDK。它可以与低成本易获取的射频硬件来组成软件定义无线电（software-defined radios），同时在没有硬件的情况下也可以作为模拟环境来使用，这个套件被广泛的应用于爱好者，学术与商业环境中来协助无线通讯研究与设置无线电系统。

## 安装

安装 [gnuradio](https://archlinux.org/packages/?name=gnuradio)包 可获取最新稳定版 GNU Radio。

最新的安装包为 [gnuradio-git](https://aur.archlinux.org/packages/gnuradio-git/)AUR，在某种特定情况中 VOLK 需要分别从 [libvolk-git](https://aur.archlinux.org/packages/libvolk-git/)AUR 中构建。

如果需要 `gnuradio-companion`，只需要安装 [gnuradio-companion](https://archlinux.org/packages/?name=gnuradio-companion)包，这个包将会安装包括 GNU Radio 和它的一些依赖包。

另外一个热门包是 [gnuradio-osmosdr](https://archlinux.org/packages/?name=gnuradio-osmosdr)包，这个包提供了很多常见的 SDR 设备的 GRC 源模块（Funcube Dongle、RTL-SDR（英语：[RTL-SDR](https://wiki.archlinux.org/title/RTL-SDR)）、USRP、OsmoSDR、BladeRF 和 HackRF）。

## 疑难解答

### TypeError: in method 'source_sptr_set_gain_mode', argument 2 of type 'bool'

如果使用（osmocom）RTL-SDR 源，你可能会看到这个错误。解决方法是将 Gain Mode 手动设置成 `True` 或 `False`。

## 8.3　GNU Compiler Collection

*难度：入门 ｜ 篇幅：3.9 KB ｜ 所属：GNU*

将要讲到：安装、参见

[GNU Compiler Collection](https://en.wikipedia.org/wiki/GNU_Compiler_Collection)（**GCC**，《GNU 编译器集合》）是 [GNU 工具链](GNU.md#工具链)的一部分，并包括 [C](../../07_开发与工具/编程语言/C.md) 和 [C++](../../07_开发与工具/编程语言/C.md) 的前端。

## 安装

安装 [gcc](https://archlinux.org/packages/?name=gcc)包 包。

其他可用的前端有：

- [gcc-ada](https://archlinux.org/packages/?name=gcc-ada)包 [Ada](https://wiki.archlinux.org/title/Ada) 前端
- [gcc-d](https://archlinux.org/packages/?name=gcc-d)包 [D](https://wiki.archlinux.org/title/D) 前端
- [gcc-fortran](https://archlinux.org/packages/?name=gcc-fortran)包 [Fortran](../../07_开发与工具/编程语言/Fortran.md) 前端
- [gcc-go](https://archlinux.org/packages/?name=gcc-go)包 [Go](../../07_开发与工具/编程语言/Go.md) 前端
- [gcc-m2](https://archlinux.org/packages/?name=gcc-m2)包 [Modula-2](https://en.wikipedia.org/wiki/Modula-2)前端
- [gcc-objc](https://archlinux.org/packages/?name=gcc-objc)包 [Objective-C](https://en.wikipedia.org/wiki/Objective-C) 前端
- [gcc-rust](https://archlinux.org/packages/?name=gcc-rust)包 [Rust](../../07_开发与工具/编程语言/Rust.md)前端

### 旧版本

旧版本的 GCC 适用于以下情况：完成历史探究需求、编译一些无法在当前版本上编译的旧项目，或者是需要测试项目的兼容性：

- GCC 4.3: [gcc43](https://aur.archlinux.org/packages/gcc43/)AUR
- GCC 4.4: [gcc44](https://aur.archlinux.org/packages/gcc44/)AUR
- GCC 4.5: [gcc45](https://aur.archlinux.org/packages/gcc45/)AUR
- GCC 4.6: [gcc46](https://aur.archlinux.org/packages/gcc46/)AUR
- GCC 4.7: [gcc47](https://aur.archlinux.org/packages/gcc47/)AUR
- GCC 4.8: [gcc48](https://aur.archlinux.org/packages/gcc48/)AUR
- GCC 4.9: [gcc49](https://aur.archlinux.org/packages/gcc49/)AUR
- GCC 5: [gcc5](https://aur.archlinux.org/packages/gcc5/)AUR
- GCC 6: [gcc6](https://aur.archlinux.org/packages/gcc6/)AUR
- GCC 7: [gcc7](https://aur.archlinux.org/packages/gcc7/)AUR
- GCC 8: [gcc8](https://aur.archlinux.org/packages/gcc8/)AUR
- GCC 9: [gcc9](https://aur.archlinux.org/packages/gcc9/)AUR
- GCC 10: [gcc10](https://aur.archlinux.org/packages/gcc10/)AUR
- GCC 11: [gcc11](https://aur.archlinux.org/packages/gcc11/)AUR
- GCC 12: [gcc12](https://aur.archlinux.org/packages/gcc12/)AUR
- GCC 13: [gcc13](https://archlinux.org/packages/?name=gcc13)包

旧版本 GCC 的其他前端可以通过搜索 `gcc<*去掉点的版本号*>` 在[官方软件仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)和 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 中找到，例如搜索 `gcc9` 查找 GCC 9 前端。

> **提示：**可通过设置 `CC`（用于 C 语言）和 `CXX`（用于 C++）[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)，在使用 make 或 cmake 等构建工具时指定特定版本的 GCC。例如：
>
> ```
> $ export CC=gcc-12 CXX=g++-12
> ```

## 参见

- [信息手册](https://gcc.gnu.org/onlinedocs/gcc/)
- [官方网站](https://gcc.gnu.org/)
- [gcc(1)](https://man.archlinux.org/man/gcc.1)

## 8.4　GNU Guix

*难度：进阶 ｜ 篇幅：6.5 KB ｜ 所属：GNU*

将要讲到：安装、在 /tmp 外构建软件包、卸载 Guix、参见

[GNU Guix](https://guix.gnu.org) 是一个提供事务性、可复现性以及按用户管理的软件包管理器。

虽然 *Guix* 可以独立使用，并提供一个完整的 [GNU](GNU.md) 发行版以及内核，但你也可以将 *Guix* 软件包管理器安装在 [Arch Linux](../../00_关于与社区/关于%20Arch/Arch%20Linux.md) 上，从而在使用更传统且成熟的类 Unix 系统作为基础的同时，让用户能够使用 *Guix*。

有关 *Guix* 提供的按用户管理的软件包命令的详细信息，请参阅 [Guix 手册](https://guix.gnu.org/manual/en/)。

> **注意：***Guix* 并不是 [Arch Linux 的官方包管理器](../../01_安装与包管理/包管理器/Pacman.md)。

## 安装

在 Arch Linux 上，你可以使用 AUR 或按照《Guix Manual》中的说明手动安装 Guix。 使用 AUR 安装的好处是，pacman 可以识别软件包和 `/usr` 文件树中的额外文件。但与其他 AUR 软件包不同的是，卸载软件包并不会解除整个 Guix 安装。 由于 Guix 本身就是一个软件包管理器，而且它还可以自我更新，因此您仍需手动卸载通过 Guix 安装的文件（无论您安装的是 AUR 软件包还是手动安装）。 因此，更新一次 Guix 后，AUR 的优势就会变成劣势，因为 `/usr` 文件树中会出现许多不必要的文件，这些文件是 Guix AUR 软件包的一部分，但 Guix 不再使用它们。因此，请考虑使用手动安装。

### 手动安装

关于手动安装，请参阅 Guix 手册[安装章节](https://guix.gnu.org/manual/zh-cn/html_node/An-Zhuang-.html)。 最简单的方法是使用其中链接的 shell 安装脚本。安装程序也可以从 AUR 安装，如 [guix-installer](https://aur.archlinux.org/packages/guix-installer/)AUR。

截至 2021 年 12 月，该脚本会将文件安装到以下位置：

- `/gnu/store`、`/var/guix`（Guix store）
- `/usr/local/share/info`、`/usr/local/bin`，（仅符号链接）
- `/root/.config/guix`（指向当前配置文件的符号链接）
- `/etc/guix/acl`，（替代服务器的密钥）
- `/etc/profile.d/guix.sh`，（设置环境变量，将当前的 Guix 配置文件放在 PATH 的首位）
- `/etc/bash_completion.d/guix`、`/usr/share/zsh/site-functions/_guix`、`/usr/share/fish/vendor_completions.d/guix.fish`（[Bash](../../07_开发与工具/命令行解释器/Bash.md)、[Zsh](../../07_开发与工具/命令行解释器/Zsh.md) 和 [Fish](../../07_开发与工具/命令行解释器/Fish.md) 的 shell 补全）

此外，它还安装并启用了名为 `guix-daemon.service` 的 systemd 服务，并创建了用户 `guixbuilder01`...`guixbuilder10` 和组 `guixbuild`。

现在启动一个新的登录 shell（或者重启机器），就可以开始使用 Guix 了：

```
$ guix install glibc-locales
```

### AUR 软件包安装

> **注意：**
>
> - 目前，如果 `/bin/sh` 不是指向 bash 的链接，编译检查就会失败，这在默认的 Arch 安装中不是问题。
> - 自 13.05.2018 起，如果 [BUILDDIR 环境变量](../../08_安全与加密/OpenPGP/Makepkg.md#使用内存文件系统进行编译)指向 tmpfs 挂载，则在 makepkg 编译过程中 *guix-environment-container* 测试会失败。

GNU Guix 可在 AUR 中以 [guix](https://aur.archlinux.org/packages/guix/)AUR 的形式获取。如 `PKGBUILD` 所述，需要先添加 Guix 发行者的 PGP 密钥。

Guix 通过使用无特权的构建用户账户来运行构建过程，从而提高了构建的可重复性（reproducible）。因此，如果想同时编译 `*n*` 个软件包（例如同时为多个用户提供服务），就应该创建 `*n*` 个编译用户账户，因为 Guix 应该能够同时编译。下面的命令是按照 [Guix 手册](https://www.gnu.org/software/guix/manual/html_node/Build-Environment-Setup.html#Build-Environment-Setup)中描述的方法执行的：

```
# groupadd --system guixbuild
# uncomment and type e.g.  10  for   n below  -->  have ten users
# for i in `seq -w 1 n`;
  do
    useradd -g guixbuild -G guixbuild           \
            -d /var/empty -s `which nologin`    \
            -c "Guix build user $i" --system    \
            guixbuilder$i;
  done
```

启用/启动 `guix-daemon.service`。

您可能希望授权 Guix 使用[Guix 官方替代服务器](https://ci.guix.gnu.org)（'替代'）下载和使用二进制软件包：

```
# guix archive --authorize < /usr/share/guix/ci.guix.gnu.org.pub
```

## 在 /tmp 外构建软件包

如果 `/tmp` 没有提供足够的空间，可能需要扩展单元文件，以使用不同的 `TMPDIR` 进行构建（详情请参见 [Guix 手册](https://www.gnu.org/software/guix/manual/html_node/Build-Environment-Setup.html#Build-Environment-Setup)）。要使用 `*/tmpdir*` 代替 `/tmp` 进行编译，编辑 `guix-daemon.service` 添加以下几行：

```
[Service]
Environment=TMPDIR=/tmpdir
```

## 卸载 Guix

停止并禁用 `guix-daemon.service`，必要时停止并禁用 `gnu-store.mount`。 如果将 Guix 作为 AUR 软件包安装，则使用[pacman](../../01_安装与包管理/包管理器/Pacman.md)移除 Guix。

如果存在，移除 `/etc/systemd/system/guix-daemon.service`，`/etc/systemd/system/guix-daemon.service.d`，`/etc/systemd/system/guix-daemon.service.wants`，和`/etc/profile.d/guix.sh`。

现在移除所有 Guix 构建用户及其组：

```
# for i in `seq -w 1 n`; do userdel guixbuilder$i; done
# groupdel guixbuild
```

然后删除 Guix store `/gnu` 以及 `/var/guix` 和 `/var/log/guix`。 删除 `/usr/local/share/info` 和 `/usr/local/bin` 中的过期符号链接。 同时删除 `/etc/guix/acl` 和 Guix 特有的 shell 完成文件。

## 参见

- [官方主页](https://guix.gnu.org/)
- [中文社区](https://guixcn.codeberg.page/)及 [Codeberg 主页](https://codeberg.org/guixcn)
- [官方 Codeberg](https://codeberg.org/guix)
- [Hilton Chain 关于 Guix 的一些文章](https://ultrarare.space/search/?keyword=Guix)
- 视频 [Linux 头脑风暴第十二期，GNU GuixLinux 探秘-哔哩哔哩](https://bilibili.com/video/BV1DT4y1N7Hz)

## 8.5　GNU Screen

*难度：进阶 ｜ 篇幅：12.7 KB ｜ 所属：GNU*

将要讲到：安装、用法、提示与技巧、故障和修复、另请参阅

> [![](https://wiki.archlinuxcn.org/wzh/images/f/f0/Tango-go-next.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-go-next.png)**此页面或章节适合移动到 screen。**
>
> **附注：** 我希望用 GNU Screen 内容替换掉 screen，并将 GNU Screen 作为 screen 的一个重定向。参见 Category talk:Disambiguation pages#Remove category。（在 ArchWiki talk:Requests#Screen 讨论）

> **翻译状态：**
>
> - 本文（或部分内容）译自 [GNU Screen](https://wiki.archlinux.org/title/GNU_Screen)，最近一次同步于 2026-8-01，若英文版本有所[更改](https://wiki.archlinux.org/title/GNU_Screen?diff=0&oldid=867431)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/GNU_Screen_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

[GNU Screen](https://www.gnu.org/software/screen/) 是一个全屏窗口管理器，它在多个进程（通常是交互式 shell）之间复用一个物理终端。Screen 中运行的程序在其窗口当前不可见时仍可继续运行，甚至在整个屏幕会话从用户终端分离时也是如此。

有关功能的描述，参阅官方概述 [GNU Screen 手册](https://www.gnu.org/software/screen/manual/screen.html#Overview)。

## 安装

安装 [screen](https://archlinux.org/packages/?name=screen)包 软件包。

## 用法

输入命令时按下 "转义键 "`Ctrl+a`，然后进行键绑定。

有些用户认为默认的转义键 `Ctrl+a` 不方便。可以按照 [#更改转义键](#更改转义键)中的说明，将转义键更改为其他键。

### 常用命令

- `Ctrl+a` `?` 显示命令及其默认值
- `Ctrl+a` `:` 进入屏幕命令提示符
- `Ctrl+a` `"` 窗口列表
- `Ctrl+a` `0` 打开窗口 0
- `Ctrl+a` `A` 命名当前窗口
- `Ctrl+a` `a` 发送到 `Ctrl+a` 当前窗口
- `Ctrl+a` `c` 创建一个新窗口（带 shell）
- `Ctrl+a` `S` 将当前区域水平拆分为两个区域
- `Ctrl+a` `|` 将当前区域垂直拆分为两个区域
- `Ctrl+a` `tab` 将输入焦点切换到下一个区域
- `Ctrl+a` `Ctrl+a` 在当前区域和上一个区域之间切换
- `Ctrl+a` `Esc` 进入复制模式（使用 Enter 选择文本范围）
- `Ctrl+a` `]` 粘贴文本
- `Ctrl+a` `Q` 关闭除当前区域之外的所有区域
- `Ctrl+a` `X` 关闭当前区域
- `Ctrl+a` `d` 脱离当前 Screen 会话，让其继续运行。使用 `screen -r` 恢复

### 命令提示符命令

- `Ctrl+a` `:quit` 关闭所有窗口并关闭 Screen 会话
- `Ctrl+a` `:source ~/.screenrc` 重载 screenrc 配置文件（也可使用 `/etc/screenrc`）

### 命名会话

要创建命名会话，请使用以下命令运行 Screen：

```
$ screen -S session_name
```

要（重新）命名现有会话，请在 Screen 运行时运行以下命令：

`Ctrl+a` `:sessionname *session_name*`

打印标识 Screen 会话的 *pid.tty.host* 字符串列表：

```
$ screen -list
```

要附加到已命名的 Screen 会话，请运行此命令：

```
$ screen -x session_name
```

或

```
$ screen -r session_name
```

### 自定义 Screen

您可以根据您的个人偏好修改 Screen 的默认设置在一个包含了要在启动时运行的命令的 `.screenrc` 文件里，或者在命令模式下修改（如 `Ctrl+a` `:vbell off`）。

## 提示与技巧

### 使用 systemd 自启动

此服务将会为指定用户自启动 Screen（如 `systemctl enable screen@florian`）。将此当作一个系统服务运行是重要的，因为 [systemd --user](../../02_系统管理与内核/系统管理/Systemd_用户.md) 的实例无法保证运行，当用户最后一个会话关闭时，实例将被终止。

```
/etc/systemd/system/screen@.service
```

```
[Unit]
Description=screen
After=network.target

[Service]
Type=simple
User=%i
ExecStart=/usr/bin/screen -DmS autoscreen
ExecStop=/usr/bin/screen -S autoscreen -X quit

[Install]
WantedBy=multi-user.target
```

### 更改转义键

修改默认的转义键是个好想法，不仅因为 “a” 通常使用左小拇指来打，而且因为 `Ctrl+a` 被绑定了常用命令 `beginning-of-line` 在 [GNU Readline](../../07_开发与工具/命令行/Readline.md) 和类 [Bash](../../07_开发与工具/命令行解释器/Bash.md) 的 Shell 里。

转义键可以在 `~/.screenrc` 中使用 `escape` 项来更改，或者给 `screen` 传入 `-e` 选项。

例如，如果您发现您很少使用 `Ctrl+j` 组合键在您的 Shell 和编辑器中，您就可以用 `escape ^Jj` 把转义键设定为 `Ctrl+j`。第二个 “j” 表示，您可以通过依次按下 `Ctrl+j` 和 `j` 的按键序列，向终端发送一个原始的 `Ctrl+j` 信号。对于 [Dvorak](../../04_硬件与外设/键盘配置/Dvorak.md) 键盘布局的用户，`Ctrl+t`（`escape ^Tt`）可能会更方便。

比较少见的包括 ` escape `` ` 可以把转义键设定为 `` ` ``，或者 `escape ^^^` 可以将其设定为 `Ctrl+^`。

在 Screen 的文档中，转义键也被叫作“指令字符”（command character）。

### 以窗口1启动

默认情况下，第一个窗口是0。如果您更希望从窗口1启动而不是窗口0, 请在您的配置文件中添加以下内容：

```
~/.screenrc
```

```
bind c screen 1
bind ^c screen 1
bind 0 select 10
screen 1
```

### 嵌套 Screen 会话

在一个嵌套的 Screen 会话当中很可能被卡住。一个很现实场景：当您从一个 Screen 会话中打开一个 SSH 会话时，在这个 SSH 会话下又开了一个 Screen会话。默认情况下，外面启动的 Screen 会话会先接受 `Ctrl+a` 命令. 要给内部的 Screen 会话发送命令，请使用 `Ctrl+a` `a`，接着输入您的命令。例如：

- `Ctrl+a` `a` `d` 脱离内部的 Screen 会话。
- `Ctrl+a` `a` `K` 终止内部的 Screen 会话。

### 在每个 Shell 中启动 Screen

对于 Bash 和 Zsh，在您的 `.bashrc` 或 `.zshrc` 中设定命令别名之前请加入以下片段：

```
~/.bashrc or ~/.zshrc
```

```
if [[ -z "$STY" ]]; then
   screen -xRR session_name
fi
```

### 使用256色


默认情况下，Screen 使用8色的终端模拟器。为了显示更多颜色，您需要一个支持更多颜色的终端并设定一个正确的 [term](https://aperiodic.net/screen/commands:term) 值。这将会使用 [terminfo](https://en.wikipedia.org/wiki/Terminfo) 来指明[ANSI 转义码](https://en.wikipedia.org/wiki/ANSI_escape_code)将会怎么解析。在 terminfo 数据库结构中必须存在一个条目，[ncurses](https://archlinux.org/packages/?name=ncurses)包 提供了许多常用项存放在 `/usr/share/terminfo/` 中。

先试一下通用值：

```
~/.screenrc
```

```
term screen-256color
```

如果这不起效，请根据您的终端尝试以下设置。当使用基于 xterm 的终端时：

```
~/.screenrc
```

```
term xterm-256color
```

当使用 [rxvt-unicode](../../07_开发与工具/终端模拟器/Rxvt-unicode.md) 时：

```
~/.screenrc
```

```
term rxvt-unicode-256color
```

> **注意：**`/usr/share/terminfo/r/rxvt-unicode-256color` is provided by [rxvt-unicode-terminfo](https://archlinux.org/packages/?name=rxvt-unicode-terminfo)包, which is installed as a dependency of [rxvt-unicode](https://archlinux.org/packages/?name=rxvt-unicode)包。然而，如果您使用 [SSH](../../03_网络与服务/SSH/安全外壳协议.md) 登入您的服务器并在其中运行了 *screen* 命令，此处的 terminfo 文件可能会在服务器上无效。对于这种情况建议复制 `/usr/share/terminfo/r/rxvt-unicode-256color` 到服务器上，它可以被存为 `~/.terminfo/`。

若以上方案实在行不通，请尝试使用 [termcapinfo](https://aperiodic.net/screen/commands:termcapinfo) 设定：

```
~/.screenrc
```

```
attrcolor b ".I"    # allow bold colors - necessary for some reason
termcapinfo xterm 'Co#256:AB=\E[48;5;%dm:AF=\E[38;5;%dm'   # tell screen how to set colors. AB = background, AF=foreground
defbce on    # use current bg color for erased chars
```

### 带有更多信息的状态栏

默认的状态栏可能有些欠缺。您可能会觉得这种样式会更有用：

```
~/.screenrc
```

```
truecolor on
hardstatus off
hardstatus alwayslastline '%{#00ff00}[ %H ][%{#ffffff}%= %{7}%?%-Lw%?%{1;0}%{1}(%{15}%n%f%t%?(%u)%?%{1;0}%{1})%{7}%?%+Lw%? %=%{#00ff00}][ %{#00a5ff}%{6}%Y-%m-%d %{#ffffff}%{7}%0c%{#00ff00} ]'
```

顶部状态栏：

```
~/.screenrc
```

```
hardstatus firstline
```

### 关掉欢迎信息

```
~/.screenrc
```

```
startup_message off
```

### 将您的固定状态行作为 urxvt|xterm|aterm 的动态窗口标题

这非常简单，只需要将您现在的 `hardstatus` 行切换为 `caption` 行，并相应地编辑：

```
~/.screenrc
```

```
termcapinfo rxvt* 'hs:ts=\E]2;:fs=\007:ds=\E]2;\007'
hardstatus string "screen (%n: %t)"
caption always '%{= 7;0}%Y-%m-%d %c %-Lw%{+b 2;0}[%n %t]%{-}%+Lw%=%{-}'
```

这将会使您的终端模拟器的标题显示为类似 `screen (0 bash)` 这种效果。选项 caption 提供日期、当前时间以及丰富您的 Screen 窗口。

### 使用 X 的滚动机制

要滚动 Screen 的缓冲区可以使用 `Ctrl+a` `[`。然而，这非常的不方便。要使用如 xterm 或者 Konsole 的滚动条，请添加以下内容 ：

```
~/.screenrc
```

```
termcapinfo xterm*|rxvt*|kterm*|Eterm* ti@:te@
```

### 将现有的程序添加进 Screen 中

如果您在 Screen 外运行了一个程序，但是现在想将此放在 Screen 中运行，您可以使用 **reptyr** 来将当前的 TTY 重新分配进 Screen 中。

安装 [reptyr](https://archlinux.org/packages/?name=reptyr)包 软件包。

获得对应进程的 PID （您可以使用 `ps ax` 命令获取）。现在只需要把对应的 PID 作为 reptyr 的参数即可将进程放入 Screen 中。

```
$ reptyr pid
```

### 为 Screen 设定一个单独的 bash 提示符

如果您希望在 Screen 会话下另外有一个不同的 bash 提示符，在您的 `.bashrc` 中添加以下内容：

```
if [ -z $STY ]
then
        PS1="YOUR REGULAR PROMPT"
else
        PS1="YOUR SCREEN PROMPT"
fi
```

### 关掉视觉响铃

通过如下设置，Screen 会使用声音响铃而不是视觉响铃。

```
~/.screenrc
```

```
vbell off
```

### 去掉垂直和水平条

去掉垂直条：

```
$ ~/.screenrc
```

```
rendition so =00
```

要去掉水平条，需要将前景和背景设成默认（d）并显示空白（" "）：

```
~/.screenrc
```

```
caption string "%{03} "
```

如果这不起效，尝试换作 `caption string "%{00} "`。要将默认 caption 设成黑白，请使用 `caption string "%{00}%3n %t"`。

## 故障和修复

### 修复编辑器文本残留问题

在屏幕中打开文本编辑器（如 [nano](../../07_开发与工具/文本编辑器/Nano.md)），然后关闭它时，文本可能会在终端中保持可见。要解决这个问题，请执行以下操作：

```
~/.screenrc
```

```
altscreen on
```

### 修复窗口列表中名称列仅显示 "bash "的问题


向 `~/.screenrc` 添加以下内容：

```
~/.screenrc
```

```
windowlist string "%4n %h%=%f"
```

## 另请参阅

- [Wikipedia:GNU Screen](https://en.wikipedia.org/wiki/GNU_Screen)
- [GNU Screen 用户手册](https://www.gnu.org/software/screen/manual/screen.html)
- [Gentoo:Screen](https://wiki.gentoo.org/wiki/Screen)
- [Arch Forums - 带屏幕截图的 .screenrc 配置](https://bbs.archlinux.org/viewtopic.php?id=55618)
- [Arch Forums - 关于 urxvt 的 256 色问题](https://bbs.archlinux.org/viewtopic.php?id=50647)

## 8.6　GNU

*难度：高级 ｜ 篇幅：23.1 KB ｜ 所属：GNU*

将要讲到：内核、Texinfo、基本系统组件、工具链、其他软件、参见

摘自 [GNU 系统概览](https://www.gnu.org/gnu/gnu-history.html):

: GNU 系统是一套向上兼容 Unix 的完全自由的操作系统。GNU 代表“GNU's Not Unix”。它的发音是[带有 g 音的单音节](https://www.gnu.org/gnu/pronunciation.html)。[理查德·斯托曼](https://www.stallman.org/)在 1983 年 9 月做出了 GNU 工程的[初始声明](https://www.gnu.org/gnu/initial-announcement.html)。接着在 1985 年 3 月又发表了更长的版本，叫做 [GNU 宣言](https://www.gnu.org/gnu/manifesto.html)。它被翻译成多种其他语言。

因为 GNU 自己的 [Hurd](https://www.gnu.org/software/hurd/hurd.html) 内核尚不成熟，GNU 一般使用 Linux 内核。[Arch Linux](../../00_关于与社区/关于%20Arch/Arch%20Linux.md) 就是这类 GNU/Linux 发行版之一，使用了包括 [Bash](../../07_开发与工具/命令行解释器/Bash.md) 终端、GNU [核心工具](../../07_开发与工具/命令行/核心工具.md)、GNU [工具链](#工具链)在内的各种工具和库。此页面只列出部分[官方仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)或 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 中存在的 GNU 软件包，而不会列出所有的[近 400 个](https://www.gnu.org/software/software.html#allgnupkgs)软件包。而且，Arch Linux（包括[官方仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)和 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md)）并没有打包**全部**的 GNU 软件。

> **提示：**为了方便排序，本文列表使用各软件包简称而非全称。比如，*GNU GRUB* 会写作 *GRUB*。

## 内核

摘自 [GNU Linux-libre 项目网站](https://www.fsfla.org/ikiwiki/selibre/linux-libre/)：

: 由 Linus Torvalds 等人开发和发布的 Linux 内核包含非自由软件，即不尊重你的[基本自由](https://www.fsfla.org/ikiwiki/about/what-is-free-software.en.html)的软件，而且它还诱使你安装额外的非自由软件。即使据称从 4.14 版开始将所有固件转移到一个单独的项目中，Torvalds 先生发布的 Linux 所谓“源代码”仍然包含伪装成源代码的非自由固件。

: GNU Linux-libre 是一个维护和发布 100% 自由 Linux 发行版的项目，适合在[自由系统](https://www.gnu.org/distros/free-system-distribution-guidelines.zh-cn.html)中使用，删除不含源代码、源代码被混淆或掩盖、使用非自由软件许可、不允许更改软件使其按照你的意愿运行、诱导或要求你安装额外的非自由软件的软件。

GNU Linux-libre 可通过安装 [linux-libre](https://github.com/archlinuxcn/repo/tree/master/archlinuxcn/linux-libre)[CNRepo](../../01_安装与包管理/非官方用户仓库/Arch%20Linux%20中文社区仓库.md) 或 [linux-libre](https://aur.archlinux.org/packages/linux-libre/)AUR 获取。

为 Linux Libre 内核构建模块的头文件和脚本可通过 [linux-libre-headers](https://github.com/archlinuxcn/repo/tree/master/archlinuxcn/linux-libre-headers)[CNRepo](../../01_安装与包管理/非官方用户仓库/Arch%20Linux%20中文社区仓库.md) 或 [linux-libre-headers](https://aur.archlinux.org/packages/linux-libre-headers/)AUR 安装。

Linux Libre 内核文档通过安装 [linux-libre-docs](https://github.com/archlinuxcn/repo/tree/master/archlinuxcn/linux-libre-docs)[CNRepo](../../01_安装与包管理/非官方用户仓库/Arch%20Linux%20中文社区仓库.md) 或 [linux-libre-docs](https://aur.archlinux.org/packages/linux-libre-docs/)AUR 获取。

## Texinfo

参见 [GNU/文档#Texinfo](GNU_文档.md#Texinfo)。

## 基本系统组件

- **[Bash](../../07_开发与工具/命令行解释器/Bash.md)** — Bash 是一种与其他 shell 兼容的 shell，它合并了许多 korn shell（ksh）和 C shell（csh）的特性。

: [https://www.gnu.org/software/bash/](https://www.gnu.org/software/bash/) || [bash](https://archlinux.org/packages/?name=bash)包

- **[核心工具](../../07_开发与工具/命令行/核心工具.md)** — 核心工具提供了 GNU 系统最基本的文件、shell 和文本操作工具。

: [https://www.gnu.org/software/coreutils/](https://www.gnu.org/software/coreutils/) || [coreutils](https://archlinux.org/packages/?name=coreutils)包、[tar](https://archlinux.org/packages/?name=tar)包、[less](https://archlinux.org/packages/?name=less)包、[findutils](https://archlinux.org/packages/?name=findutils)包、[diffutils](https://archlinux.org/packages/?name=diffutils)包、[grep](https://archlinux.org/packages/?name=grep)包、[sed](https://archlinux.org/packages/?name=sed)包、[gawk](https://archlinux.org/packages/?name=gawk)包

- **[GRUB](../../01_安装与包管理/启动加载程序/GRUB.md)** — GRUB 是 GNU 项目的引导程序。

: [https://www.gnu.org/software/grub/](https://www.gnu.org/software/grub/) || [grub](https://archlinux.org/packages/?name=grub)包

- **[gzip](https://en.wikipedia.org/wiki/gzip)** — gzip 既是一种文件格式，又是一种[压缩和解压](../../02_系统管理与内核/数据压缩与归档/归档与压缩.md)工具。

: [https://www.gnu.org/software/gzip/](https://www.gnu.org/software/gzip/) || [gzip](https://archlinux.org/packages/?name=gzip)包

- **[tar](../../02_系统管理与内核/数据压缩与归档/归档与压缩.md)** — tar 提供了创建和解压 tar 压缩包的功能，也提供一些其它功能。

: [https://www.gnu.org/software/tar/](https://www.gnu.org/software/tar/) || [tar](https://archlinux.org/packages/?name=tar)包

## 工具链

大部分 [GNU toolchain](https://en.wikipedia.org/wiki/GNU_toolchain) 中的工具都是 [base-devel](https://archlinux.org/packages/?name=base-devel)包 软件包的依赖, 除了 *glibc* （[base](https://archlinux.org/packages/?name=base)包 的依赖）和 GDB。

- **[Binutils](https://en.wikipedia.org/wiki/GNU_Binutils)** — 一组用来汇编和操作二进制和模板文件的程序。包括 [ld](https://en.wikipedia.org/wiki/GNU_linker)。

: [https://www.gnu.org/software/binutils/](https://www.gnu.org/software/binutils/) || [binutils](https://archlinux.org/packages/?name=binutils)包

- **[Bison](https://en.wikipedia.org/wiki/GNU_Bison)** — GNU 通用解析器生成器。

: [https://www.gnu.org/software/bison/bison.html](https://www.gnu.org/software/bison/bison.html) || [bison](https://archlinux.org/packages/?name=bison)包

- **[GCC](GNU%20Compiler%20Collection.md)** — GNU 编译器集合-C 和 C++ 前端。

: [https://gcc.gnu.org/](https://gcc.gnu.org/) || [gcc](https://archlinux.org/packages/?name=gcc)包

- **[GDB](https://en.wikipedia.org/wiki/GNU_Debugger)** — GNU 调试器。

: [https://www.gnu.org/software/gdb/](https://www.gnu.org/software/gdb/) || [gdb](https://archlinux.org/packages/?name=gdb)包

- **[glibc](https://en.wikipedia.org/wiki/GNU_C_Library)** — GNU 的 C 库实现 library。

: [https://www.gnu.org/software/libc/](https://www.gnu.org/software/libc/) || [glibc](https://archlinux.org/packages/?name=glibc)包 （[base](https://archlinux.org/packages/?name=base)包 的依赖）

- **[GNU m4](https://en.wikipedia.org/wiki/GNU_m4)** — GNU 宏处理器。

: [https://www.gnu.org/software/m4/](https://www.gnu.org/software/m4/) || [m4](https://archlinux.org/packages/?name=m4)包

- **[make](https://en.wikipedia.org/wiki/Make_(software))** — GNU make 工具，用于维护程序组。

: [https://www.gnu.org/software/make](https://www.gnu.org/software/make) || [make](https://archlinux.org/packages/?name=make)包

### 构建系统

来自[维基百科](https://en.wikipedia.org/wiki/GNU_build_system):

: GNU 构建系统，也被叫做自动工具，是一套用来帮助让源码包能移植到类 Unix 系统的编程工具

- **[Autoconf](https://en.wikipedia.org/wiki/Autoconf)** — 用来自动设置源码的工具。

: [https://www.gnu.org/software/autoconf](https://www.gnu.org/software/autoconf) || [autoconf](https://archlinux.org/packages/?name=autoconf)包

- **[Automake](https://en.wikipedia.org/wiki/Automake)** — 自动创建 make 文件的工具。

: [https://www.gnu.org/software/automake](https://www.gnu.org/software/automake) || [automake](https://archlinux.org/packages/?name=automake)包

- **[GNU Libtool](https://en.wikipedia.org/wiki/Libtool)** — 支持脚本的通用库。

: [https://www.gnu.org/software/libtool](https://www.gnu.org/software/libtool) || [libtool](https://archlinux.org/packages/?name=libtool)包

## 其他软件


> **注意：**[gnuplot](https://archlinux.org/packages/?name=gnuplot)包 不是 GNU 软件。[Gnuplot](http://www.gnuplot.info/) 既不是由 FSF 编写，也不是由 FSF 维护。它曾一度由 FSF 发布，但现在已不再如此。整个 Gnuplot 不在 GNU 通用公共许可证 (GPL) 的覆盖范围内。

在[官方仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)和 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 中可以找到其它可选 GNU 工具：

- **a2ps** — 一个 Any to PostScript 过滤器。

: [https://www.gnu.org/software/a2ps/](https://www.gnu.org/software/a2ps/) || [a2ps](https://archlinux.org/packages/?name=a2ps)包

- **acct** — 监控进程活动的工具

: [https://www.gnu.org/software/acct/](https://www.gnu.org/software/acct/) || [acct](https://aur.archlinux.org/packages/acct/)AUR

- **adns** — 先进、易用、支持异步的 DNS 客户端库和工具。

: [https://www.gnu.org/software/adns/](https://www.gnu.org/software/adns/) || [adns](https://archlinux.org/packages/?name=adns)包

- **alive** — 一个定期 ping 程序。

: [https://www.gnu.org/software/alive/](https://www.gnu.org/software/alive/) || [alive](https://aur.archlinux.org/packages/alive/)AUR

- **Anastasis** — 是一个自由软件协议和实施方案，允许用户将核心机密安全地存放在一组开放的托管服务提供商处，并在原始副本丢失时恢复这些机密。

: [https://www.gnu.org/software/anastasis/](https://www.gnu.org/software/anastasis/) || [anastasis](https://aur.archlinux.org/packages/anastasis/)AUR

- **anubis** — SMTP 消息提交守护进程。

: [https://www.gnu.org/software/anubis/](https://www.gnu.org/software/anubis/) || [anubis](https://aur.archlinux.org/packages/anubis/)AUR

- **apl** — 编程语言 APL 的自由解释器。

: [https://www.gnu.org/software/apl/](https://www.gnu.org/software/apl/) || [gnu-apl](https://aur.archlinux.org/packages/gnu-apl/)AUR

- **Archimedes** — 半导体器件模拟。

: [https://www.gnu.org/software/archimedes/](https://www.gnu.org/software/archimedes/) || [archimedes](https://aur.archlinux.org/packages/archimedes/)AUR

- **[Artanis](../../03_网络与服务/网络服务器/Artanis.md)** — 一个用 Guile Scheme 编写的网络应用程序框架（WAF）。

: [https://www.gnu.org/software/artanis/](https://www.gnu.org/software/artanis/) || [artanis](https://aur.archlinux.org/packages/artanis/)AUR

- **[Aspell](../词典/语言检查.md)** — 拼写检查器。

: [http://aspell.net](http://aspell.net) || [aspell](https://archlinux.org/packages/?name=aspell)包

- **AUCTeX** — 在 Emacs 中制作 TeX 文档的集成环境。

: [https://www.gnu.org/software/auctex/](https://www.gnu.org/software/auctex/) || [auctex](https://aur.archlinux.org/packages/auctex/)AUR

- **barcode** — 将文本字符串转换为打印条形码的工具。

: [https://www.gnu.org/software/barcode/](https://www.gnu.org/software/barcode/) || [barcode](https://archlinux.org/packages/?name=barcode)包

- **[bc](https://en.wikipedia.org/wiki/bc_(programming_language)#GNU_bc)** — 一种任意精度计算器语言。

: [https://www.gnu.org/software/bc](https://www.gnu.org/software/bc) || [bc](https://archlinux.org/packages/?name=bc)包

- **c-graph** — 展示工程系统和信号分析所依据的卷积理论。

: [https://www.gnu.org/software/c-graph/](https://www.gnu.org/software/c-graph/) || [c-graph](https://aur.archlinux.org/packages/c-graph/)AUR

- **ccd2cue** — 将 CloneCD 提示表（.ccd）转换为兼容性较差的 CDRWIN 提示表（.cue）。

: [https://www.gnu.org/software/ccd2cue/](https://www.gnu.org/software/ccd2cue/) || [ccd2cue](https://aur.archlinux.org/packages/ccd2cue/)AUR

- **ccRTP** — IETF 的实时传输协议 RTP 的实现。

: [https://www.gnu.org/software/ccrtp/](https://www.gnu.org/software/ccrtp/) || [ccrtp](https://aur.archlinux.org/packages/ccrtp/)AUR

- **Cflow** — C 程序流分析器。

: [https://www.gnu.org/software/cflow/](https://www.gnu.org/software/cflow/) || [cflow](https://aur.archlinux.org/packages/cflow/)AUR

- **Cgicc** — 简化 CGI 应用程序创建的 C++ 库。

: [https://www.gnu.org/software/cgicc/](https://www.gnu.org/software/cgicc/) || [cgicc](https://aur.archlinux.org/packages/cgicc/)AUR

- **Chess** — 国际象棋前端引擎，在终端上与计算机下棋。

: [https://www.gnu.org/software/chess/chess.html](https://www.gnu.org/software/chess/chess.html) || [gnuchess](https://archlinux.org/packages/?name=gnuchess)包

- **Classpath** — Sun 专有的核心 Java 类库的自由替代。

: [https://www.gnu.org/software/classpath/](https://www.gnu.org/software/classpath/) || [classpath](https://aur.archlinux.org/packages/classpath/)AUR

- **Clisp** — ANSI Common Lisp 解释器、编译器和调试器。

: [https://www.gnu.org/software/clisp/](https://www.gnu.org/software/clisp/) || [clisp](https://archlinux.org/packages/?name=clisp)包

- **Complexity** — 测量 C 代码的复杂性。

: [https://www.gnu.org/software/complexity/](https://www.gnu.org/software/complexity/) || [complexity](https://aur.archlinux.org/packages/complexity/)AUR

- **Cppi** — GNU Cppi 对 C 预处理器指令进行缩进，以反映其嵌套情况，并进行其他规范化处理。

: [https://www.gnu.org/software/cppi/](https://www.gnu.org/software/cppi/) || [cppi](https://aur.archlinux.org/packages/cppi/)AUR

- **CSSC** — Unix SCCS 工具套件的克隆版。

: [https://www.gnu.org/software/cssc/](https://www.gnu.org/software/cssc/) || [cssc](https://aur.archlinux.org/packages/cssc/)AUR

- **Cursynth** — GNU ncurses 终端合成器。

: [https://gnu.org/software/cursynth](https://gnu.org/software/cursynth) || [cursynth-git](https://aur.archlinux.org/packages/cursynth-git/)AUR

- **Datamash** — 对输入的文本数据文件执行基本的数字、文本和统计操作。

: [https://www.gnu.org/software/datamash](https://www.gnu.org/software/datamash) || [datamash](https://archlinux.org/packages/?name=datamash)包

- **DDD** — 命令行调试器的图形前端，如 GDB、JDB、ydb、perl 调试器...

: [https://www.gnu.org/software/ddd/](https://www.gnu.org/software/ddd/) || [ddd](https://aur.archlinux.org/packages/ddd/)AUR

- **ddrescue** — 一个数据恢复工具。

: [https://www.gnu.org/software/ddrescue](https://www.gnu.org/software/ddrescue) || [ddrescue](https://archlinux.org/packages/?name=ddrescue)包

- **DejaGnu** — 测试其他程序的框架。

: [https://www.gnu.org/software/dejagnu/](https://www.gnu.org/software/dejagnu/) || [dejagnu](https://archlinux.org/packages/?name=dejagnu)包

- **Denemo** — 乐谱编辑器。

: [https://www.denemo.org/](https://www.denemo.org/) || [denemo](https://aur.archlinux.org/packages/denemo/)AUR

- **Dia** — 基于 GTK+ 的图表创建程序。

: [https://wiki.gnome.org/Apps/Dia](https://wiki.gnome.org/Apps/Dia) || [dia](https://aur.archlinux.org/packages/dia/)AUR

- **Dico** — GNU 词典服务器。

: [https://www.gnu.org/software/dico/](https://www.gnu.org/software/dico/) || [dico](https://aur.archlinux.org/packages/dico/)AUR

- **Diction** — 用于识别文章中的生词和常见误用短语。

: [https://www.gnu.org/software/diction/](https://www.gnu.org/software/diction/) || [diction](https://aur.archlinux.org/packages/diction/)AUR

- **Direvent** — 监控目录事件（如创建、删除或修改文件）的守护进程。

: [https://www.gnu.org/software/direvent/](https://www.gnu.org/software/direvent/) || [direvent](https://aur.archlinux.org/packages/direvent/)AUR

- **Ed** — 符合 POSIX 标准的行式文本编辑器。

: [https://www.gnu.org/software/ed/](https://www.gnu.org/software/ed/) || [ed](https://archlinux.org/packages/?name=ed)包

- **[Emacs](../../07_开发与工具/Emacs/Emacs.md)** — 一款可扩展、可定制、自文档化的文本编辑器。

: [https://www.gnu.org/software/emacs](https://www.gnu.org/software/emacs) || [emacs](https://archlinux.org/packages/?name=emacs)包 或 [emacs-nox](https://archlinux.org/packages/?name=emacs-nox)包

- **[FreeFont](https://en.wikipedia.org/wiki/GNU_FreeFont)** — 免费的可缩放轮廓字体系列。

: [https://www.gnu.org/software/freefont](https://www.gnu.org/software/freefont) || [gnu-free-fonts](https://archlinux.org/packages/?name=gnu-free-fonts)包

- **gcal** — 在终端中输出日历。

: [https://www.gnu.org/software/gcal/](https://www.gnu.org/software/gcal/) || [gcal](https://aur.archlinux.org/packages/gcal/)AUR

- **[Ghostscript](https://en.wikipedia.org/wiki/Ghostscript)** — PostScript 和 PDF 的解释器。提供 [gs(1)](https://man.archlinux.org/man/gs.1) 命令行界面，另请参阅 `/usr/share/doc/ghostscript/*/Use.htm`（[在线阅读](https://ghostscript.readthedocs.io/en/latest/Use.html)），以及许多封装脚本，如 *ps2pdf* 和 *pdf2ps*。

: [https://ghostscript.com/](https://ghostscript.com/) || [ghostscript](https://archlinux.org/packages/?name=ghostscript)包

- **[GIMP](../图片/GIMP.md)** — 一款图片编辑器。

: [https://www.gimp.org](https://www.gimp.org) || [gimp](https://archlinux.org/packages/?name=gimp)包

- **[GnuCash](https://en.wikipedia.org/wiki/GnuCash)** — 一个会计程序。

: [https://www.gnucash.org/](https://www.gnucash.org/) || [gnucash](https://archlinux.org/packages/?name=gnucash)包

- **GNU Go** — 一个玩围棋的自由软件。

: [https://www.gnu.org/software/gnugo/](https://www.gnu.org/software/gnugo/) || [gnugo](https://archlinux.org/packages/?name=gnugo)包

- **[GNU Guix](GNU%20Guix.md)** — 一个独立的软件包管理器。

: [https://guix.gnu.org/](https://guix.gnu.org/) || [guix](https://aur.archlinux.org/packages/guix/)AUR 或 [guix-installer](https://aur.archlinux.org/packages/guix-installer/)AUR

- **[Gnumeric](../办公/Gnumeric.md)** — 一款表格处理软件。

: [http://www.gnumeric.org](http://www.gnumeric.org) || [gnumeric](https://archlinux.org/packages/?name=gnumeric)包

- **[GnuPG](../../03_网络与服务/邮件/GnuPG.md)** — 一款 OpenPGP 实现。

: [https://www.gnupg.org](https://www.gnupg.org) || [gnupg](https://archlinux.org/packages/?name=gnupg)包

- **Hello** — 打印 “Hello World” 等内容。

: [https://www.gnu.org/software/hello/](https://www.gnu.org/software/hello/) || [hello](https://aur.archlinux.org/packages/hello/)AUR

- **[Indent](https://en.wikipedia.org/wiki/Indent_(Unix)#GNU_Indent)** — 一款 C 语言代码格式化工具。

: [https://www.gnu.org/software/indent](https://www.gnu.org/software/indent) || [indent](https://archlinux.org/packages/?name=indent)包

- **Jami** — 一款分布式会议软件。

: [https://jami.net](https://jami.net) || [jami-qt](https://archlinux.org/packages/?name=jami-qt)包, [jami-daemon](https://archlinux.org/packages/?name=jami-daemon)包

- **[LilyPond](../音乐/LilyPond.md)** — 一款基于文本输入的制谱软件。

: [https://lilypond.org](https://lilypond.org) || [lilypond](https://archlinux.org/packages/?name=lilypond)包

- **[Mailman](../../03_网络与服务/邮件/Mailman.md)** — 一款用来管理电子邮件讨论和邮件列表的软件。

: [https://www.list.org](https://www.list.org) || [mailman3](https://archlinux.org/packages/?name=mailman3)包

- **[Midnight Commander](../文件管理器/Midnight%20Commander.md)** — 一款双窗格终端文件管理器。

: [https://midnight-commander.org](https://midnight-commander.org) || [mc](https://archlinux.org/packages/?name=mc)包

- **[nano](../../07_开发与工具/文本编辑器/Nano.md)** — 一个命令行文本编辑器。

: [https://www.nano-editor.org](https://www.nano-editor.org) || [nano](https://archlinux.org/packages/?name=nano)包

- **[Ocrad](https://en.wikipedia.org/wiki/Ocrad)** — 一款OCR(光学字符识别)文字识别的实用程序和库。

: [https://www.gnu.org/software/ocrad](https://www.gnu.org/software/ocrad) || [ocrad](https://archlinux.org/packages/?name=ocrad)包

- **[Octave](../数据分析/Octave.md)** — 一种科学编程语言。

: [https://octave.org](https://octave.org) || [octave](https://archlinux.org/packages/?name=octave)包

- **[Parted](../../02_系统管理与内核/系统恢复/Parted.md)** — 一个分区管理器。

: [https://www.gnu.org/software/parted](https://www.gnu.org/software/parted) || [parted](https://archlinux.org/packages/?name=parted)包

- **[plotutils](https://en.wikipedia.org/wiki/plotutils)** — 一套用于绘图的工具和库。

: [https://www.gnu.org/software/plotutils](https://www.gnu.org/software/plotutils) || [plotutils](https://archlinux.org/packages/?name=plotutils)包

- **[Readline](../../07_开发与工具/命令行/Readline.md)** — 一个用于命令行界面的行编辑库。

: [https://tiswww.cwru.edu/php/chet/readline/rltop.html](https://tiswww.cwru.edu/php/chet/readline/rltop.html) || [readline](https://archlinux.org/packages/?name=readline)包

- **[Screen](GNU%20Screen.md)** — 一个终端多路复用器。

: [https://www.gnu.org/software/screen](https://www.gnu.org/software/screen) || [screen](https://archlinux.org/packages/?name=screen)包

- **Stow** — 在同一目录树中管理多个软件的安装。

: [https://www.gnu.org/software/stow](https://www.gnu.org/software/stow) || [stow](https://archlinux.org/packages/?name=stow)包

- **[Units](https://en.wikipedia.org/wiki/GNU_Units)** — 在不同单位之间进行转换。

: [https://www.gnu.org/software/units](https://www.gnu.org/software/units) || [units](https://aur.archlinux.org/packages/units/)AUR

- **[Wget](../../03_网络与服务/下载工具/Wget.md)** — 一款网络下载工具。

: [https://www.gnu.org/software/wget](https://www.gnu.org/software/wget) || [wget](https://archlinux.org/packages/?name=wget)包

- **Zile** — emacs 的轻量级克隆。

: [https://www.gnu.org/software/zile](https://www.gnu.org/software/zile) || [zile](https://aur.archlinux.org/packages/zile/)AUR

## 参见

- [https://www.gnu.org/](https://www.gnu.org/)
- [The GNU Manifesto](https://www.gnu.org/gnu/manifesto)
- [Wikipedia:List of GNU packages](https://en.wikipedia.org/wiki/List_of_GNU_packages)
- [GNU 软件包基本信息](https://directory.fsf.org/wiki/GNU/)
- [Arch Hurd Project](https://archhurd.org/) 致力于将 *Arch Linux* 移植到 Hurd 内核（目前维护不积极）。
- Mu Lei 所著《[Hurd, seL4, thoughts](https://nalaginrut.com/archives/2019/12/11/hurd%2c%20sel4%2c%20thoughts)》
