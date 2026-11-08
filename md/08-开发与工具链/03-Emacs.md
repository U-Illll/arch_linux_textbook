# 第 3 章　Emacs

> Emacs 装起来容易，用起来才费功夫：它自成一套按键与命令模型。本章把安装、配置、运行、排错与技巧串成一条线，共十节。
> 
> 《Emacs》主页讲安装与配置，《Emacs/使用》讲进入退出与基本编辑，两节是必读主干，按此先后走；要让 Emacs 常驻，再读《Emacs/运行》的守护进程与 systemd 单元。《Emacs/概念》讲屏幕组织、按键与命令，按 C-x、M-x 接不上时回头补；《Emacs/术语》《Emacs/帮助》留作回查，《Emacs/提示与技巧》按需取用，EXWM 是把 Emacs 当窗口管理器，不作此用可跳，《Emacs/疑难解答》等出问题再翻。最常见的坑是照抄别人的配置：镜像源没改、use-package 没装，启动即报无法打开加载文件；守护进程或 gccemacs 与 systemd 单元没理顺，服务起不来；无彩色终端下启动还会撞上 WINDOW_FOREGROUND 报错。

<!-- 本章 10 节，共 103 KB -->

## 3.1　Emacs/术语

*难度：入门 ｜ 篇幅：0.3 KB ｜ 所属：Emacs*

下表主要记录了本维基 [Emacs](Emacs.md) 有关文章采用的术语及其英文。

| 术语 | 英文 |
| --- | --- |
| 帧 | frame |
| 缓冲区 | buffer |
| 帧缓冲区 | frame buffer |
| 模式 | mode |
| 框架 | framework |
| 变音符号 | accent |
| 点 | point |
| 按键；关键字 | Key |

## 3.2　Emacs/帮助

*难度：入门 ｜ 篇幅：1.7 KB ｜ 所属：Emacs*

将要讲到：手册

Emacs 有一个内置教程，点击启动屏幕上的第一个链接，然后从菜单中选择 *Help>Emacs Tutorial*，或者按 `C-h t`，即可进入该教程。

要以英语以外的语言（如**简体中文**）阅读教程，请使用命令 `Alt x`，并输入 `help-with-tutorial-spec-language`。

Emacs 在设计上是自文档化的。因此，我们可以利用大量信息来确定特定命令的名称或其按键绑定。使用 `C-h C-h` 查看所有上下文帮助绑定。

Emacs 还包含一套参考资料卡，对初学者和专家都很有用，请参见 `/usr/share/emacs/*version*/etc/refcards/` 。

## 手册

如果你真的想掌握 Emacs，最推荐的文档来源仍然是[官方手册](https://www.gnu.org/software/emacs/documentation.html)：

- Emacs：完整的 Emacs 用户手册。
- Emacs FAQ。
- Emacs Lisp Intro：如果你以前从未使用过任何编程语言，那么就来学习 Emacs Lisp 入门吧。
- Elisp：如果您已经熟悉一种编程语言。

您可以从 [GNU.org](https://www.gnu.org/software/emacs/manual/) 或直接从 Emacs 本身通过内嵌的 *info* 阅读器以 PDF 格式访问它们： `C-h i`。按 `m` 键选择图书。

一些用户喜欢使用 *info* 阅读书籍，因为它有方便的快捷键，段落可根据窗口宽度调整，字体可根据当前屏幕分辨率调整。有些人则认为 *info* 对眼睛的刺激较小。最后，你可以轻松地将书中的内容复制到任何 Emacs 缓冲区，甚至可以直接执行示例中的 Lisp 代码片段。

您可能想阅读 **Info** 书籍以了解更多：`C-h i m info <RET>`。 在 *Info mode* 下按 `?` 可快速查看快捷键列表。

## 3.3　Emacs/软件包

*难度：入门 ｜ 篇幅：3.9 KB ｜ 所属：Emacs*

将要讲到：软件镜像源、use-package

Emacs 的功能可以通过第三方软件包进行扩展。官方支持使用内置的软件包管理器 `package.el`，不过 Emacs 社区成员还编写了其他一些软件包管理器。`package.el` 依靠变量 `package-archives` 查找软件包。默认情况下，这包括 [Emacs Lisp Package Archive (ELPA)](https://elpa.gnu.org/)。`M-x list-packages` 将创建一个缓冲区，列出 Emacs 知道的所有软件包。手册（[(info "(emacs) Packages")](https://www.gnu.org/software/emacs/manual/html_node/emacs/Packages.html) ）包含更多信息。

可以添加第三方软件包存档。其中使用最广泛的是 [MELPA](https://melpa.org/)。

许多流行的扩展以软件包的形式出现在官方软件仓库中，更多的扩展则通过[AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md)提供。这些软件包的名称通常带有 *emacs-* 前缀（例如 [emacs-lua-mode](https://archlinux.org/packages/?name=emacs-lua-mode)包），但也不总是如此（例如 [auctex](https://aur.archlinux.org/packages/auctex/)AUR）。

有些软件包可能需要修改配置文件才能激活，以便在 Emacs 会话中使用其功能。例如，如果安装了 [auctex](https://aur.archlinux.org/packages/auctex/)AUR，则需要在配置文件中添加

```
(load "auctex.el" nil t t)
(load "preview-latex.el" nil t t)
```

到配置文件中。其他软件包会在源代码的注释部分或 README 中告诉你如何激活它们。

## 软件镜像源

中国大陆用户可能会需要 ELPA 镜像站以加速软件包下载。

[校园网联合镜像站](https://mirrors.cernet.edu.cn/list/elpa)列出了国内已知许多 ELPA 镜像。

对于大多数镜像源，需要设置 `package-archives`。比如用[北京外国语大学开源软件镜像站](https://mirrors.bfsu.edu.cn/)提供的 GNU ELPA 和 MELPA 镜像：

```
(setq package-archives '(("gnu"    . "https://mirrors.bfsu.edu.cn/elpa/gnu/")
                         ("nongnu" . "https://mirrors.bfsu.edu.cn/elpa/nongnu/")
                         ("melpa"  . "https://mirrors.bfsu.edu.cn/elpa/melpa/")))
(package-initialize) ;; 您的配置文件里可能已经有了这一行
```

浏览各镜像站帮助页面以获取详细信息。

## use-package

自 [Emacs 29.1](https://www.gnu.org/savannah-checkouts/gnu/emacs/news/NEWS.29.1) 起，**use-package** 已随 Emacs 一起提供。*use-package* 宏允许你以声明的方式在启动文件中设置软件包自定义。它可以帮你处理许多原本需要大量重复模板代码的事情。它可以帮助进行常见的自定义，如绑定键、设置钩子、自定义用户选项和 face、自动加载等。它还能帮助你保持 Emacs 的启动速度，即使你使用了很多（甚至上百个）软件包。

请注意，use-package 并不是软件包管理器。尽管 use-package 具有与 Emacs 软件包管理器接口的实用功能，但它的主要用途是帮助配置和加载软件包，而不是管理软件包的下载、升级和安装。

更多信息，请参阅 Info 手册 “(use-package) Top”（在线阅读：[https://elpa.gnu.org/devel/doc/use-package.html](https://elpa.gnu.org/devel/doc/use-package.html) ）。

## 3.4　Emacs/运行

*难度：入门 ｜ 篇幅：4.9 KB ｜ 所属：Emacs*

将要讲到：无彩色、作为守护进程、作为 systemd 单元

同 [Vim](../文本编辑器/Vim.md)、[Neovim](../文本编辑器/Neovim.md) 一样，在启动 emacs 之前，您应该知道如何关闭它（尤其是在终端中运行时）：使用 `Ctrl+x``Ctrl+c` 键序。

要启动 Emacs，请运行：

```
$ emacs
```

如果 [X11](../../05_桌面与图形/X%20服务器/Xorg.md) 或 [Wayland](../../05_桌面与图形/Wayland/Wayland.md) 图形界面可用，此命令将会启动 Emacs 的图形用户界面。在这种情况下，建议运行以下命令以使其在后台运行：

```
$ emacs &
```

或在纯控制台中使用 Emacs（出于空间原因，菜单通常不会显示，可以按 `F10` 调出菜单。）：

```
$ emacs -nw
```

或在命令行中快速加载（无需 .emacs）和编辑：

```
$ emacs -Q -nw
```

如果安装的是 nox 版本，`emacs` 和 `emacs -nw` 将启相同作用。

还可以提供文件名，以便立即打开该文件：

```
$ emacs filename.txt
```

## 无彩色

默认情况下，Emacs 启动时的颜色主题显示超链接为深蓝色。要在文本终端上启动 Emacs 而不使用任何颜色主题或方案，请执行下列操作：

```
$ emacs -nw --color=no
```

这将使所有文本显示为终端的前景色——通常是黑底白字或白底黑字。

## 作为守护进程

为了避免每次 Emacs 启动时都重新加载 Emacs [配置文件](#配置)，可以将 Emacs 作为[守护进程](../../01_安装与包管理/初始化/Systemd.md)运行：

```
$ emacs --daemon
```

然后，您可以连接到守护进程通过运行：

```
$ emacsclient -nc
```

`-c` 将创建一个新帧缓冲区（如果希望在终端中使用，请使用 `-t`}），并且不会占用终端 `-n` `--no-wait`）。

许多[窗口管理器](../../05_桌面与图形/窗口管理器/窗口管理器.md)、[混成器](../../05_桌面与图形/Wayland/Wayland.md#混成器)以及[桌面环境](../../05_桌面与图形/桌面环境/桌面环境.md)都实现了 [XDG Autostart](../../05_桌面与图形/Freedesktop.org/XDG%20Autostart.md) 规范，您可以利用此特性在[桌面环境](../../05_桌面与图形/桌面环境/桌面环境.md)等启动时[自动运行](../../02_系统管理与内核/系统管理/自动启动.md) `emacs --daemon`。

需要注意的是，有些程序（如 [Mutt](../../03_网络与服务/邮件客户端/Mutt.md) 或 [Git](../版本控制系统/Git.md)，用于提交信息）会等待编辑器结束，因此不能使用 `-n` 参数。 如果你的默认编辑器设置为使用该参数，则必须为这些程序指定替代编辑器（*例如* `emacsclient -a "" -t`）。

## 作为 systemd 单元

[Emacs 26.1](https://lists.gnu.org/archive/html/emacs-devel/2018-05/msg00765.html) 中包含一个 [systemd](../../01_安装与包管理/初始化/Systemd.md) 单元。该单元与 Emacs 一起安装，但必须在安装 Emacs 后作为[用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)（而非全系统单元）启用：

```
$ systemctl --user enable --now emacs
```

服务启动后，Emacs 就准备就绪了。

如果想在 [Wayland](../../05_桌面与图形/Wayland/Wayland.md) 上通过 `emacsclient` 启动图形 emacs 框架，则需要特定的[附加配置片段](../../01_安装与包管理/初始化/Systemd.md)（除非运行的是 [emacs-wayland](https://archlinux.org/packages/?name=emacs-wayland)包），如 [EmacsWiki](https://www.emacswiki.org/emacs/EmacsAsDaemon#h5o-17) 所示。

请注意，systemd 用户单元不会从登录 shell（如 `~/.bash_profile`）继承[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)。更多信息请参见 [Systemd/用户#环境变量](../../02_系统管理与内核/系统管理/Systemd_用户.md#环境变量)。

如果将 emacs 作为[守护进程](../../01_安装与包管理/初始化/Systemd.md)启动，可能需要将 `VISUAL` 和 `EDITOR` 环境变量设置为 `emacsclient`，这样启动编辑器的程序就会使用 emacsclient，而不是启动编辑器的完整实例。使用外部编辑器的程序包括电子邮件程序（用于编辑邮件）、[Git](../版本控制系统/Git.md)（用于编辑提交信息）和 less（`v` 命令用于编辑显示的文件）。请勿使用 `-n`（`--nowait`）选项，因为程序通常希望编辑器退出时编辑已经完成。

还建议更改 Emacs 的任何图形用户界面开始菜单条目（或类似条目、[桌面项](../../01_安装与包管理/配置文件/桌面项.md)），使其指向 emacsclient 而不是 emacs，这样就可以使用 emacs 守护进程而不是启动一个新的 emacs 进程。

## 3.5　EXWM

*难度：入门 ｜ 篇幅：5.7 KB ｜ 所属：Emacs*

将要讲到：安装、配置、嵌入 LXDE、疑难解答、参见

> **翻译状态：**
>
> - 本文（或部分内容）译自 [EXWM](https://wiki.archlinux.org/title/EXWM)，最近一次同步于 2025-02-14，若英文版本有所[更改](https://wiki.archlinux.org/title/EXWM?diff=0&oldid=827061)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/EXWM_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

EXWM 是一款基于 [Emacs](Emacs.md) 的[窗口管理器](../../05_桌面与图形/窗口管理器/窗口管理器.md)。

## 安装

确保已安装 [emacs](https://archlinux.org/packages/?name=emacs)包。还需要 [xorg-xinit](https://archlinux.org/packages/?name=xorg-xinit)包。

在 Emacs 中安装 EXWM： `M-x package-install RET exwm RET`。

编辑 [xinitrc](../../05_桌面与图形/Xorg%20命令/Xinit.md) 并添加：

```
exec emacs
```

在 emacs 初始化（init）文件中添加

```
(require 'exwm)
(require 'exwm-config)
(exwm-config-example)
```

以使用默认设置。如果想使用自己的设置，请使用 `(exwm-enable)` 而不是 `(exwm-config-example)` （也无需 `(require 'exwm-config)`）。

还可以在服务器模式下启动 emacs，并通过命令行启动 EXWM。请参见 [https://github.com/ch11ng/exwm/issues/284](https://github.com/ch11ng/exwm/issues/284) 。

## 配置

EXWM 是一个完整的 X 窗口管理器，因此 Emacs 可以管理浏览器、vlc 等 X 窗口。您可以使用所有正常的 Emacs 窗口命令来控制窗口的位置。在 X 窗口（即非“正常”的 Emacs 缓冲区）中，有些命令会被 EXWM 捕捉，而不会传递给程序。这些键值存储在 `exwm-input-prefix-keys` 中。此外，您也可以通过自定义 `exwm-input-global-keys` 来设置全局命令。如果您不使用自定义功能，而希望在 elisp 中设置 `exwm-input-global-keys` ，请注意可能需要重启 EXWM（并在启用 exwm 之前设置 `exwm-input-global-keys`）。或者，你也可以尝试使用 ["or emacs" blog](https://oremacs.com/2015/01/17/setting-up-ediff/) 中的 `cset` 宏，它可以在不重启 EXWM 的情况下重新定义 `exwm-input-global-keys`。要使用 s-& 作为启动程序（如 firefox）的快捷键，可以这样做：

```
(setq exwm-input-global-keys `(,(kbd "s-&") .
                               (lambda (command)
                                 (interactive (list (read-shell-command "$ ")))
                                 (start-process-shell-command command nil command))))
```

### 多显示器

EXWM 可以通过（可选）`exwm-randr` 软件包处理多显示器问题。在调用 `(exwm-enable)` 之前，您需要安装 [xrandr](../../05_桌面与图形/Xorg%20命令/Xrandr.md) 并在 emacs 配置文件中启用 exwm-randr。您需要调整 “DP-1” 和 “DP-2” 的值，使其与您电脑使用的值一致；在命令行中调用 `xrandr` 不带参数，即可查看可用输出。

```
(require 'exwm-randr)
(setq exwm-randr-workspace-output-plist '(1 "DP-1"))
(add-hook 'exwm-randr-screen-change-hook
          (lambda ()
            (start-process-shell-command
             "xrandr" nil "xrandr --output DP-1 --right-of DP-2 --auto")))
(exwm-randr-enable)
```

### 系统托盘

EXWM 支持系统托盘，但默认情况下并未启用。要启用它，请在 dotemacs 文件中的 `(exwm-enable)` 前添加以下内容：

```
(require 'exwm-systemtray)
(exwm-systemtray-mode 1)
```

之后可能需要调整高度；可以使用 `exwm-systemtray-height` 变量进行调整。

## 嵌入 LXDE

EXWM 可以代替 openbox，让您仍然可以使用 [LXDE](../../05_桌面与图形/LXDE/LXDE.md) 会话管理工具。

在这样做之前，请确保已为 emacs 设置好运行 EXWM 的启动文件（见上文）

*lxsession* 使用`~/.config/lxsession/LXDE/desktop.conf`中定义的[窗口管理器](../../05_桌面与图形/窗口管理器/窗口管理器.md)。(默认情况下使用[Openbox](../../05_桌面与图形/堆叠式窗口管理器/Openbox.md)）。如果该文件不存在，则会在 `/etc/xdg/lxsession/LXDE/desktop.conf` 中搜索。

用 emacs 替换任一文件中的 `openbox-lxde`：

```
[Session]
window_manager=emacs
```

### lxsession-logout

您可以在 emacs 中创建以下函数，以便在 LXDE 会话中干净利落地注销、关机或重启：

```
(defun exwm-logout ()
  (interactive)
  (recentf-save-list)
  (save-some-buffers)
  (start-process-shell-command "logout" nil "lxsession-logout"))
```

该功能会将最近的历史记录存储到磁盘中，提示您保存、丢弃或更改未保存缓冲区中的更改，然后启动注销管理器。你可以将此功能绑定到 emacs 中的任意键上。

## 疑难解答

### 火狐浏览器屏幕撕裂

在某些程序中，特别是 Firefox 中，您可能会遇到屏幕撕裂现象。您可以试试

- 在首选项 > 高级 > 使用平滑滚动中关闭平滑滚动。
- 安装（并激活）[Compton](../../05_桌面与图形/X%20服务器/Picom.md) 或其他合成管理器：[Xorg#合成](../../05_桌面与图形/X%20服务器/Xorg.md#合成)。

### 令人困惑的缓冲区名称

您可能会看到缓冲区名称被命名为 “\*EXWM\*”。EXWM 允许缓冲区自行命名。要允许缓冲区自行命名，请在 dotemacs 中添加以下内容：

```
;; Make buffer name more meaningful
(add-hook 'exwm-update-class-hook
          (lambda ()
          (exwm-workspace-rename-buffer exwm-class-name)))
```

## 参见

- [EXWM 维基](https://github.com/emacs-exwm/exwm/wiki)

## 3.6　Emacs

*难度：进阶 ｜ 篇幅：9.4 KB ｜ 所属：Emacs*

将要讲到：页面、行文与翻译说明、安装、配置、替代品、参见

[![](https://wiki.archlinuxcn.org/wzh/images/f/ff/GNU_Emacs_Teaser.png)](https://wiki.archlinuxcn.org/wzh/File:GNU_Emacs_Teaser.png)

GNU Emacs 基本界面，来源：自由软件基金会

[GNU Emacs](https://www.gnu.org/software/emacs/) 是一个可扩展、可定制、自由的[文本编辑器](../../06_软件与应用/应用程序/应用程序列表_文档.md)，其核心是 Emacs Lisp 解释器，[Emacs Lisp](https://www.gnu.org/software/emacs/manual/html_node/elisp/index.html) 是 Lisp 编程语言的一种[方言](https://wiki.archlinuxcn.org/wzh/Category:Lisp_%E6%96%B9%E8%A8%80)，具有支持文本编辑的扩展功能。

GNU Emacs 的[特性](https://www.gnu.org/software/emacs/index.html#features)包括：

- 内容感知编辑模式，包括语法着色，适用于多种文件类型。可以使用 [Eglot](https://www.gnu.org/software/emacs/manual/eglot.html)[GNU 文档](https://www.gnu.org/manual/manual.html)

、[lsp-bridge](https://github.com/manateelazycat/lsp-bridge) 等包添加[语言服务器](../编程语言/语言服务器协议.md)支持。

- 完整的内置文档，包括针对新用户的教程。以及正在进行的[简体中文翻译](https://codeberg.org/LS-Shandong/GNU-Emacs-Manual-zh)。

- 完全支持几乎所有人类文字的 [Unicode](http://unicode.org/)。

- 可使用 Emacs Lisp 代码或图形界面进行高度[定制](#配置)。

- 除文本编辑功能外，还具有[项目计划器](https://www.gnu.org/software/emacs/manual/org.html)、[邮件和新闻阅读器](https://www.gnu.org/software/emacs/manual/gnus.html)、[调试器界面](http://www.gnu.org/software/emacs/manual/html_node/emacs/Debuggers.html)、[日历](https://www.gnu.org/software/emacs/manual/html_node/emacs/Calendar_002fDiary.html)、[IRC 客户端](https://www.gnu.org/software/emacs/erc.html)[等](https://www.gnu.org/software/emacs/further-information.html)内置多种功能。

- 用于[下载和安装](http://elpa.gnu.org/)扩展程序的打包系统。

- 各种语言的活跃的社区。

## 页面

**Arch Linux 中文维基对 Emacs 有关介绍分为以下几部分：**

- 章节 [#安装](#安装)

- 章节 [#配置](#配置)

- 章节 [#替代品](#替代品)

- 章节 [#参见](#参见)

- 子页面 [/运行](Emacs_运行.md)

- 子页面 [/使用](Emacs_使用.md)

- 子页面 [/提示与技巧](Emacs_提示与技巧.md)

- 子页面 [/疑难解答](Emacs_疑难解答.md)

- 子页面 [/软件包](Emacs_软件包.md)

- 子页面 [/帮助](Emacs_帮助.md)

此外还有编者的 [#行文与翻译说明](#行文与翻译说明)及[术语表](Emacs_术语.md)。

## 行文与翻译说明


### 术语

详见 [/术语](Emacs_术语.md)。

### 翻译说明

> **翻译状态：**
>
> - 本文（或部分内容）译自 [Emacs](https://wiki.archlinux.org/title/Emacs)，最近一次同步于 2025-02-02，若英文版本有所[更改](https://wiki.archlinux.org/title/Emacs?diff=0&oldid=826244)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/Emacs_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

## 安装

安装下列软件包之一：

- [emacs](https://archlinux.org/packages/?name=emacs)包——稳定版，
- [emacs-nox](https://archlinux.org/packages/?name=emacs-nox)包——无图形用户界面；仅在终端模拟器上运行，
- [emacs-wayland](https://archlinux.org/packages/?name=emacs-wayland)包——启用了 PGTK，
- [emacs-lucid](https://aur.archlinux.org/packages/emacs-lucid/)AUR——用 Lucid (Athena) 工具包代替 GTK；少了些 Bug，少了些光彩。该界面现在是 [Emacs Android 端](https://sourceforge.net/projects/android-ports-for-gnu-emacs/)的默认界面。

如果希望 Emacs 拼写检查正常工作，还需要安装 [aspell](https://archlinux.org/packages/?name=aspell)包 和 aspell 语言，如 [aspell-en](https://archlinux.org/packages/?name=aspell-en)包

还可以搜索其他变体。例如，[emacs-git](https://aur.archlinux.org/packages/emacs-git/)AUR 提供了 GNU Emacs 的开发分支。

## 配置

Emacs 的主要特点之一是可扩展性和易于配置。Emacs 有一个内置的自定义引擎。你可以使用 `M-x customize`，它将显示一个自定义选项列表。关于如何使用该界面，请参阅 “Easy Customization” info 节点：`(info "(emacs) Easy Customization")`。你可以只在一个 Emacs 会话中设置自定义选项，也可以将其保存到配置文件中，以便在不同的 Emacs 会话中保存。请注意，如果您选择 *Apply and Save*，自定义界面就会这样做。

Emacs 启动时，通常会尝试从“初始化文件”（简称 “init 文件”）中加载 Lisp 程序。如果该文件存在，它将指定如何为您初始化 Emacs。Emacs 会在 `~/.emacs`、`~/.emacs.el`、`~/.emacs.d/init.el` 或 `~/.config/emacs/init.el` 中查找初始化文件。更多信息，请参见 info 节点 “Init File”： `(info "(emacs) Init File")`

## 替代品

有许多“小型”文本编辑器，至少表面上与 GNU Emacs 类似。下面是其中一些：

- **mg** — 用 C 语言编写的 Emacs 轻量级实现。注意它不支持 UTF-8。

: [https://github.com/hboetes/mg](https://github.com/hboetes/mg) || [mg](https://archlinux.org/packages/?name=mg)包

- **Zile** — “Zile Is Lossy Emacs” 是一个轻量级克隆，编写时尽可能与 Emacs 相似。Zile 不支持 UTF-8。

: [https://www.gnu.org/software/zile/](https://www.gnu.org/software/zile/) || [zile](https://aur.archlinux.org/packages/zile/)AUR

- **uemacs** — Linus Torvalds 定制的 “Micro-emacs” 版本。

: [https://git.kernel.org/pub/scm/editors/uemacs/uemacs.git](https://git.kernel.org/pub/scm/editors/uemacs/uemacs.git) || [uemacs-git](https://aur.archlinux.org/packages/uemacs-git/)AUR

- **emacs-ng** — Emacs 的新方法——包括 TypeScript、线程、异步 I/O 和 WebRender。

: [https://github.com/emacs-ng/emacs-ng](https://github.com/emacs-ng/emacs-ng) || [emacs-ng-git](https://aur.archlinux.org/packages/emacs-ng-git/)AUR

- **qemacs** — “Quick Emacs”，基于 Emacs Lisp 解释器，但代码量较小，用户界面也有所简化。

: [https://bellard.org/qemacs/](https://bellard.org/qemacs/) || [qemacs](https://aur.archlinux.org/packages/qemacs/)AUR

- **sxemacs** — xemacs 的分叉。

: [https://www.sxemacs.org/](https://www.sxemacs.org/) || [sxemacs](https://aur.archlinux.org/packages/sxemacs/)AUR

- **jove** — Jonathan 自己的 Emacs 版本是一个类似 Emacs 的编辑器，但没有 Lisp。

: [https://github.com/jonmacs/jove](https://github.com/jonmacs/jove) || [jove](https://aur.archlinux.org/packages/jove/)AUR

### Emacs 发行版

Emacs “发行版”是 Emacs 软件包和自定义软件的集合。它们比你自己定制 emacs 更容易安装和使用（但却不那么个性化）。

- [Spacemacs](../文本编辑器/Spacemacs.md)——社区驱动的 Emacs 发行版——最好的编辑器既不是 Emacs 也不是 Vim，而是 Emacs **与** Vim！
- [Doom Emacs](https://github.com/doomemacs/doomemacs/)——An Emacs framework for the stubborn martian hacker.为顽固的火星黑客设计的 Emacs 框架。
- [Witchmacs](https://github.com/snackon/Witchmacs/)——最可爱的 Emacs 发行版。
- [Yukimacs](https://github.com/pprobst/yukimacs/)——从头开始构建的 GNU/Emacs 配置（无 Spacemacs 或 Doom Emacs），灵感主要来自 Uncle Dave 的 Emacs 和 Witchmacs。

更多：[Starter Kits](https://www.emacswiki.org/emacs/StarterKits)

## 参见

- [GNU Emacs 主页](https://www.gnu.org/software/emacs/)
- [emacs 手册](https://www.gnu.org/software/emacs/manual/)[GNU 文档](https://www.gnu.org/manual/manual.html)

- [Emacs Wiki](https://www.emacswiki.org/)（英文）
- [Emacs China](https://emacs-china.org/)
- [WikEmacs ——一个可读性更强但不够完整的 Emacs 维基](https://wikemacs.org)（英文）
- [Emacs 及其快捷键的实用介绍](https://www2.lib.uchicago.edu/keith/tcl-course/emacs-tutorial.html)（英文）
- [The Church of Emacs (通过 Google drive)](https://web.archive.org/web/20160506064817/https://d0edfcdc0ccc1cd13cdab5eb986fb92e8660dbef.googledrive.com/host/0B6LMD0u8OhYYZEotN2QyR1hwR1k/) (humor)（英文）
- [官方参考卡](https://www.gnu.org/software/emacs/refcards/pdf/refcard.pdf)（英文）
- [EXWM](EXWM.md)，Emacs X 窗口管理器
- [《Pavinberg's Emacs Book》](https://pavinberg.github.io/emacs-book/zh/)：专业 Emacs 入门
- [Emacs-for-Noobs](https://github.com/LdBeth/Emacs-for-Noobs)

## 3.7　Emacs/疑难解答

*难度：进阶 ｜ 篇幅：9.8 KB ｜ 所属：Emacs*

将要讲到：Emacs 启动失败，提示错误信息 'Undefined color: "WINDOW_FOREGROUND"'、使用 Gccemacs 时 Emacs systemd 服务无法启动、彩色输出问题、在 X 窗口中显示字符的问题、启动缓慢、无法打开加载文件： ...

本文主要讲解了在使用 [Emacs](Emacs.md) 的过程中可能遇到的问题及其解决方案。

## Emacs 启动失败，提示错误信息 'Undefined color: "WINDOW_FOREGROUND"'

您需要安装 [mcpp](https://archlinux.org/packages/?name=mcpp)包 软件包或 [gcc](https://archlinux.org/packages/?name=gcc)包 软件包。*xrdb* 使用 C 预处理器 *cpp* 对 [X 资源](../../01_安装与包管理/配置文件/X%20资源.md)进行预处理。如果系统中未安装 C 预处理器，*xrdb* 会自动跳过运行 C 预处理器，并且符号 WINDOW_FOREGROUND 不会扩展为十六进制颜色代码。

## 使用 Gccemacs 时 Emacs systemd 服务无法启动

使用 Gccemacs（分支 `emacs-native-comp` 或 `pgtk-nativecomp`）并尝试为其启动 systemd 服务时，可能会记录一条提示未找到 `.eln` 文件的错误信息：

```
$ journalctl --user -xb -g eln
```

```
emacs: emacs: ../native-lisp/28.0.50-x86_64-pc-linux-gnu-fc9c33938bf279333039c28064d363e8/lisp-mode-410874a46ab8852855281f021ca61fe7-1738806322de892570d69dfc55b437c2.eln: cannot open shared object file: No such file or directory
```

作为一种解决方法，编辑 `emacs.service`中 \[Service\] 部分的 `WorkingDirectory` 行。

```
emacs.service
```

```
[Service]
...
WorkingDirectory=/usr/lib/emacs/28.0.50/x86_64-pc-linux-gnu/
...
```

> **注意：**必须在可能存在的 `Restart` 参数**之前**定义 `WorkingDirectory` 参数。

## 彩色输出问题

默认情况下，Emacs shell 会显示用于打印颜色的原始转义序列。换句话说，它会显示奇怪的符号来代替所需的彩色输出。

在 `~/.emacs` 中加入以下内容后，问题就迎刃而解了：

```
(add-hook 'shell-mode-hook 'ansi-color-for-comint-mode-on)
```

## 在 X 窗口中显示字符的问题

如果在 X 窗口中启动 emacs 时，主窗口中的所有字符都是带黑边的白框（即在尝试查看未安装正确字体的字符时看到的白框），则需要安装 [xorg-fonts-75dpi](https://archlinux.org/packages/?name=xorg-fonts-75dpi)包 和/或 [xorg-fonts-100dpi](https://archlinux.org/packages/?name=xorg-fonts-100dpi)包 并重启 X 窗口。

## 启动缓慢

> **提示：**为了减少启动时间，用户可以考虑运行 Emacs [#作为 Systemd 单元](#作为_Systemd_单元)。

启动时间慢通常由以下两种情况之一造成。

要确定是哪种情况，请使用以下命令运行 Emacs：

```
$ emacs -q
```

- 错误，尤其是在 `/etc/hosts` 中的错误，往往会导致 Emacs 启动时出现 5 秒以上的延迟。有关信息，请参阅网络配置指南中的"[设置主机名](../../03_网络与服务/网络/网络配置.md#设置主机名)"。
- 您可能需要监控从计算机发送的任何网络数据包（使用 [Wireshark](../../08_安全与加密/安全/Wireshark.md) 等程序），查看是否有任何奇怪的行为。
- 查找原因的一个简单方法是注释掉（即在行前加上";"）`~/.emacs` 中的可疑部分（或 `~/.emacs.d/init.el` (或 `~/.emacs.d/init.el`）中的可疑部分，然后再次启动 Emacs 看看是否有任何变化。请注意，使用 “require” 和 “load” 可能会减慢启动速度，尤其是在使用较大的扩展时。原则上，只有在以下情况下才可使用它们：Emacs 启动后需要，或只为扩展提供“自动加载”功能。否则，请直接使用 “autoload” 函数。例如

    ```
    (require 'anything)
    ```

    你可以使用

    ```
    (autoload 'anything "anything" "Select anything" t)
    ```

## 无法打开加载文件： ...

导致此错误的最常见原因是 “load-path” 变量未包含扩展所在目录的路径。要解决这个问题，可在尝试加载扩展之前将适当的路径添加到要搜索的列表中：

```
 (add-to-list 'load-path "/path/to/directory/")
```

如果尝试使用扩展包，而 Emacs 的配置前缀又不是 `/usr`，则需要更新加载路径。在软件包提供的说明之前，将以下内容放入 `~/.emacs` 中：

```
 (add-to-list 'load-path "/usr/share/emacs/site-lisp")
```

如果手动编译 Emacs，请注意默认前缀为 `/usr/local`。

## 坏掉的变音符号键问题：“<dead-acute> is undefined”

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**文风需要调整。（在[Talk:Emacs/疑难解答](https://wiki.archlinuxcn.org/wiki/Talk:Emacs/%E7%96%91%E9%9A%BE%E8%A7%A3%E7%AD%94)讨论）

在谷歌上搜索这个错误，我们找到了这个链接：[https://lists.gnu.org/archive/html/help-gnu-emacs/2009-05/msg00167.html](https://lists.gnu.org/archive/html/help-gnu-emacs/2009-05/msg00167.html) ，解释了这个问题。使用变音符号键的正常方法无法达到预期效果。如果尝试对 “fiancé” 这样的单词使用变音符号，就会产生上述信息。

解决方法是在启动文件 `~/.emacs` 中加入下面一行：

```
  (require 'iso-transl)
```

不，这不是一个 bug，而是新 Emacs 版本的一项功能。通过阅读邮件列表中的相关信息，我们发现了它 ：

: 因为需要选择 iso-transl 或 iso-acc，所以似乎什么都不会自动加载。这两种输入法似乎都提供了 C-x 8 或 Alt-<accent> 前缀，但你我现在做的只是按一个死键（^、´、\`、~、¨）来输入变音符号，然后再按另一个键来“组成”变音符号。在此过程中没有使用 Alt 键！根据文档，它似乎适用于 8 位编码，因此在 UTF-8 中应该没什么用。这个错误出现时我就报告过，但这个错误似乎被归类为一个功能……也许这只是因为文件是自动加载的，虽然很没用。

## C-M-% 和其他一些绑定在 emacs nox 中不起作用

这是因为终端比 Xorg 更受局限。不过，某些终端可能比其他终端处理更多的绑定。两种解决方案：

- 要么使用图形版本，
- 要么将绑定更改为受支持的绑定。

示例：

```
.emacs
```

```
(global-set-key (kbd "C-M-y") 'query-replace-regexp)
```

## Emacs 挂起

由于 Emacs 的单线程特性，许多操作都会阻塞。这种情况可能有几种。例如，Emacs 可能正在等待你的输入（例如，你在一帧中打开了 minibuffer，却在另一帧中尝试工作）。或者，Emacs 正在运行的代码需要一段时间才能完成。又或者，你遇到了一个错误。有几种方法可以在不杀死 Emacs 进程的情况下解锁 Emacs。

- 试试按 `C-g`。根据 Emacs 的操作，您可能需要按多次。
- 尝试按 `ESC ESC ESC`。
- 在另一个终端运行 `killall -SIGUSR2 emacs`

## Emacs-nox 输出变得凌乱

在终端中工作时，颜色、缩进或任何与输出相关的内容都可能变得疯狂。这（很可能？）是因为 Emacs 在某一时刻发送了一个特殊字符，而该字符可能与当前终端冲突。 如果出现这种情况，你可以执行 `M-x redraw-display`，这将重绘终端显示。如果这个问题经常出现，你可能需要将命令绑定到某个按键上，例如：`M-x redraw-display`

```
(global-set-key (kbd "<f7>") 'redraw-display)
```

在您的 `.emacs` 文件中。

图形化 Emacs 没有这个问题。

## 在 emacs 终端中显示奇怪的转义数字 (utf-8)

将这些值导出到 `.bashrc` 或 `.zshrc` 中：

```
$ ~/.bashrc or ~/.zshrc
```

```
export LANG\='en_US.UTF-8'
export LC_ALL\="en_US.UTF-8"
export TERM\=xterm-256color
```

这可能会导致错误，因为在 Linux 发行版中，正确值使用的是小写 utf（例如 `en_US.utf-8`。

要查看所有可用的本地语言，请使用 `locale -a`。

## 在 tmux 的 emacs 中，Shift + 方向键不起作用

在 [tmux](../终端多路复用器/Tmux.md) 配置中启用 xterm 键：

```
~/.tmux.conf
```

```
setw -g xterm-keys on
```

因为这会破坏其他组合键，所以请在 emacs 配置中加入以下内容。

```
~/.emacs
```

```
(defadvice terminal-init-screen
  ;; The advice is named `tmux', and is run before `terminal-init-screen' runs.
  (before tmux activate)
  ;; Docstring. This describes the advice and is made available inside emacs;
  ;; for example when doing C-h f terminal-init-screen RET
  "Apply xterm keymap, allowing use of keys passed through tmux."
  ;; This is the elisp code that is run before `terminal-init-screen'.
  (if (getenv "TMUX")
    (let ((map (copy-keymap xterm-function-map)))
    (set-keymap-parent map (keymap-parent input-decode-map))
(set-keymap-parent input-decode-map map))))
```

详见 [tmux FAQ](https://github.com/tmux/tmux/wiki/FAQ#how-do-i-make-modified-function-and-arrow-keys-like-c-up-m-pageup-work-inside-tmux)。

## KDE 中不正确的窗口大小调整

KDE 用户可能会发现 Emacs 窗口不能正确调整大小，而是调整后的部分是透明的，鼠标点击会被发送到底层窗口。要纠正这种行为，请将 KDE 的 GTK3 主题改为 oxygen-gtk 以外的其他主题。例如，使用 [gtk3](https://archlinux.org/packages/?name=gtk3)包 附带的 Emacs 主题。

要强制 Emacs 在 KDE 中完全最大化，点击标题栏中的 Emacs 图标，然后选择 *More Actions > Special Window Settings*。然后在 *Size & Position* 选项卡中选择 *Obey geometry restrictions*，在下拉菜单中选择 *Force*，并从右侧的单选按钮中选择 *No*。

## 3.8　Emacs/提示与技巧

*难度：进阶 ｜ 篇幅：11.6 KB ｜ 所属：Emacs*

将要讲到：TRAMP、将 Emacs 用作 git 合并工具、将大写锁定键用作 Control 键、复用 emacs 和 emacsclient、多种配置、局部变量和自定义变量

本文主要讲解了 [Emacs](Emacs.md) 的使用技巧和高效方法，包括各方面的实用提示。

## TRAMP

TRAMP（Transparent Remote Access, Multiple Protocols）是一个扩展名，顾名思义，它可以通过多种协议对远程文件进行透明访问。当提示输入文件名时，输入一个特定的表单就会调用 TRAMP。举几个例子：

以根权限打开 `/etc/hosts` 前提示输入根密码：

```
C-x C-f /sudo::/etc/hosts
```

以“你”的身份通过 [SSH](../../03_网络与服务/SSH/安全外壳协议.md) 连接到 “remotehost”，并打开文件 `~/example.txt`：

```
C-x C-f /ssh:you@remotehost:~/example.txt
```

TRAMP 的路径形式通常为"/\[协议\]:\[\[用户@\]主机\]:<文件>"。

以“你”的身份连接“myhost”，并使用 *sudo* 编辑 `/etc/hosts`：

```
/ssh:you@remotehost|sudo:remotehost:/etc/hosts
```

TRAMP 支持的功能远不止上面的例子。欲了解更多信息，请参阅随 Emacs 一起发布的 TRAMP info 手册。

## 将 Emacs 用作 git 合并工具

[Git](../版本控制系统/Git.md) 默认支持使用 Emacs 的 *Emerge* 模式作为合并工具。不过，你可能更喜欢 *Ediff* 模式。遗憾的是，由于技术原因，git 并不支持这种模式。但还是有办法在调用 emacs 时评估一些 elisp 代码来使用它。

```
.gitconfig
```

```
[mergetool.ediff]
    cmd = emacs --eval \" (progn (defun ediff-write-merge-buffer () (let ((file ediff-merge-store-file)) (set-buffer ediff-buffer-C) (write-region (point-min) (point-max) file) (message \\\"Merge buffer saved in: %s\\\" file) (set-buffer-modified-p nil) (sit-for 1))) (setq ediff-quit-hook 'kill-emacs ediff-quit-merge-hook 'ediff-write-merge-buffer) (ediff-merge-files-with-ancestor \\\"$LOCAL\\\" \\\"$REMOTE\\\" \\\"$BASE\\\" nil \\\"$MERGED\\\"))\"

[merge]
	tool = ediff
```

注意，命令必须在一行中。 在上例中，我们启动了一个新的 Emacs 实例。你可能想使用 emacsclient 更快地启动 Emacs，但不建议这样做，因为 Ediff 调用并不干净：它可能会扰乱你当前的 Emacs 会话。

如果想立即启动，可以使用 `-q` 参数。如果想快速启动 Emacs，同时至少保留部分配置，可以使用：

```
emacs -q -l ~/.emacs-light
```

其中的轻量配置文件只加载 Ediff 所需的内容。

有关此技巧和 Ediff 问题的更多详情，请参阅 [kerneltrap.org](https://web.archive.org/web/20090210145607/http://kerneltrap.org/mailarchive/git/2007/7/1/250424) 和 [stackoverflow](https://stackoverflow.com/questions/1817370/using-ediff-as-git-mergetool)。

## 将大写锁定键用作 Control 键

有些用户喜欢这种行为，以避免出现所谓的 “emacs 小指”。 在图形用户界面桌面（[Xorg](../../05_桌面与图形/X%20服务器/Xorg.md) 或 [Wayland](../../05_桌面与图形/Wayland/Wayland.md)）、终端甚至控制台中实现这一功能的好方法是使用 [keyd](https://archlinux.org/packages/?name=keyd)包。安装此软件包并创建此配置文件：

```
/etc/keyd/main.conf
```

```
[ids]
*

[main]
capslock = overload(control, noop)
```

然后启用/启动 keyd 服务。

这样大写锁定键就会启 Control 键之作用。

## 复用 emacs 和 emacsclient

在同一个 `emacs-session` 中打开新文件，需要使用 `emacsclient`。如果会话存在，`emacs` 命令本身也可以进行封装，以更智能地打开文件。

要启动会话，需要使用 `start-server`。该代码段将在 emacs 的第一个会话中创建服务器。将其添加到 `emacs` 配置文件中。

```
.emacs or .emacs.d/init.el
```

```
(require 'server)
(unless (server-running-p)
  (server-start))
```

Shell 别名方法并不能满足要求，因为你还需要传递变量或启动自己的独立会话。在 shell 的 `.bashrc` 或任何 rc 文件中添加此选项。这样，如果参数被传递，`emacs` 命令就会像 emacsclient 一样运行。

```
function emacs {
    if [[ $# -eq 0 ]]; then
        /usr/bin/emacs # "emacs" is function, will cause recursion
        return
    fi
    args=($*)
    for ((i=0; i <= ${#args}; i++)); do
        local a=${args[i]}
        # NOTE: -c for creating new frame
        if [[ ${a:0:1} == '-' && ${a} != '-c' && ${a} != '--' ]]; then
            /usr/bin/emacs ${args[*]}
            return
        fi
    done
    setsid emacsclient -n -a /usr/bin/emacs ${args[*]}
}
```

如果要在新会话中运行，只需执行 `emacs *file* -`。

## 多种配置

您可以使用多种配置，并告诉 Emacs 加载其中一种或另一种。

例如，让我们定义两种配置文件。

```
.emacs
```

```
(load "~/.emacs.d/main" nil t)
(load "~/.emacs.d/functions" nil t)
(load "~/.emacs.d/modes" nil t)
(load "~/.emacs.d/plugins" nil t)
(load "~/.emacs.d/theme" nil t)
```

这是我们针对[守护进程加载](#作为守护进程)的全部配置。但 *plugins* 文件很大，加载速度也很慢。如果我们想生成一个不需要 *plugins* 功能的新 Emacs 实例，那么从长远来看，每次加载都会很麻烦。

```
.emacs-light
```

```
(load "~/.emacs.d/main" nil t)
(load "~/.emacs.d/functions" nil t)
(load "~/.emacs.d/modes" nil t)
(load "~/.emacs.d/theme" nil t)
```

现在我们用：

```
emacs -q -l ~/.emacs-light
```

您可以创建一个[别名](../../00_关于与社区/建议阅读/建议阅读.md#别名)（如 `emacs-light`），以便调用。

## 局部变量和自定义变量

您可以在配置文件中定义变量，以后可以在本地对文件进行修改。

```
(defcustom my-compiler "gcc" "Some documentation")
```

现在，在任何文件中都可以通过两种方式定义局部变量，详见[手册](https://www.gnu.org/software/emacs/manual/html_node/emacs/Specifying-File-Variables.html)。

- 使用 `M-x add-file-local-variable-prop-line` 可以在开头添加一行注释，类似于：

```
// -*- my-compiler:g++; mode:c++ -*-
```

- 或者使用 `M-x add-file-local-variable` 在文件末尾添加行：

```
// Local Variables:
// my-compiler: g++
// mode: c++
// End:
```

请注意，要使这些值生效，您需要调用 `M-x revert-buffer`。

默认情况下，自定义变量被认为是不安全的。如果你试图打开一个包含重新定义自定义变量的局部变量的文件，Emacs 会要求你确认。

您可以将变量声明为安全变量，从而消除 Emacs 的确认提示。您需要指定一个谓词，任何新值都必须经过该谓词的验证，这样才能被认为是安全的。

```
(defcustom my-compiler "gcc" "Some documentation" :safe 'stringp)
```

在上例中，如果您试图设置字符串以外的内容，Emacs 会认为这是不安全的。

## 自定义色彩和主题

可以使用 *face* 工具轻松定制颜色。

```
(set-face-background  'region                 "color-17")
(set-face-foreground  'region                 "white")
(set-face-bold-p      'font-lock-builtin-face t )
```

您可以让 Emacs 告诉您点所在的 face 的名称。为此，请使用 *customize-face* 功能。该功能会告诉你如何设置颜色、粗体、下划线等。

控制台中的 Emacs 可以处理 256 种颜色，但必须使用合适的终端。例如，URxvt 支持 256 种颜色。你可以使用 *list-colors-display* 查看支持颜色的完整列表。这在很大程度上取决于终端。

参见：

- [https://www.emacswiki.org/emacs/ColorThemes](https://www.emacswiki.org/emacs/ColorThemes)
- [https://www.gnu.org/software/emacs/manual/html_node/emacs/Custom-Themes.html](https://www.gnu.org/software/emacs/manual/html_node/emacs/Custom-Themes.html)

## SyncTeX 支持

Emacs 是一款功能强大的 LaTeX 编辑器。这主要是因为你可以调整或创建最适合自己需要的 LaTeX 模式。

不过，也可能会遇到一些挑战，比如 SyncTeX 支持。首先，你需要确保你的 TeX 发行版支持 SyncTeX。如果你手动安装了 [TeX Live](../TeX/TeX%20Live.md)，可能需要安装 *synctex* 软件包。

```
# umask 022 && tlmgr install synctex
```

SyncTeX 支持与阅读器有关。在此，我们将以 Zathura 为例，因此如果您想使用其他 [PDF](../../06_软件与应用/文件格式/PDF、PS%20与%20DjVu.md) 阅读器，则需要对代码进行调整。

```
(defcustom tex-my-viewer "zathura --fork -s -x \"emacsclient --eval '(progn (switch-to-buffer  (file-name-nondirectory \"'\"'\"%{input}\"'\"'\")) (goto-line %{line}))'\""
  "PDF Viewer for TeX documents. You may want to fork the viewer
so that it detects when the same document is launched twice, and
persists when Emacs gets closed.

Simple command:

  zathura --fork

We can use

  emacsclient --eval '(progn (switch-to-buffer  (file-name-nondirectory \"%{input}\")) (goto-line %{line}))'

to reverse-search a pdf using SyncTeX. Note that the quotes and double-quotes matter and must be escaped appropriately."
:safe 'stringp)
```

在这里，我们定义了自定义变量。如果您使用的是 AucTeX 或 Emacs 默认的 LaTeX 模式，则必须相应地设置查看器。

现在用 Emacs 打开一个 LaTeX 源文件，编译文档，然后启动查看器。Zathura 进程会自动创建。如果按下 `Ctrl+Left click` 键，Emacs 就会将点放在相应的位置。

## systemd 文件的语法高亮显示

您可以使用 [systemd-mode](https://elpa.nongnu.org/nongnu/systemd.html)。

或者，你也可以在 init 文件中添加以下内容，让 emacs 为 systemd 文件（服务、定时器等）着色：

```
 (add-to-list 'auto-mode-alist '("\\.service\\'" . conf-unix-mode))
 (add-to-list 'auto-mode-alist '("\\.timer\\'" . conf-unix-mode))
 (add-to-list 'auto-mode-alist '("\\.target\\'" . conf-unix-mode))
 (add-to-list 'auto-mode-alist '("\\.mount\\'" . conf-unix-mode))
 (add-to-list 'auto-mode-alist '("\\.automount\\'" . conf-unix-mode))
 (add-to-list 'auto-mode-alist '("\\.slice\\'" . conf-unix-mode))
 (add-to-list 'auto-mode-alist '("\\.socket\\'" . conf-unix-mode))
 (add-to-list 'auto-mode-alist '("\\.path\\'" . conf-unix-mode))
 (add-to-list 'auto-mode-alist '("\\.netdev\\'" . conf-unix-mode))
 (add-to-list 'auto-mode-alist '("\\.network\\'" . conf-unix-mode))
 (add-to-list 'auto-mode-alist '("\\.link\\'" . conf-unix-mode))
```

## 为 emacs-nox 提供剪贴板支持

要在 emacs-nox 中使用 [Xorg](../../05_桌面与图形/X%20服务器/Xorg.md) 剪贴板，请安装。[xclip](https://archlinux.org/packages/?name=xclip)包 并在 `~/.emacs` 中添加以下函数：

```
;; use xclip to copy/paste in emacs-nox
(unless window-system
  (when (getenv "DISPLAY")
    (defun xclip-cut-function (text &optional push)
      (with-temp-buffer
	(insert text)
	(call-process-region (point-min) (point-max) "xclip" nil 0 nil "-i" "-selection" "clipboard")))
    (defun xclip-paste-function()
      (let ((xclip-output (shell-command-to-string "xclip -o -selection clipboard")))
	(unless (string= (car kill-ring) xclip-output)
	  xclip-output )))
    (setq interprogram-cut-function 'xclip-cut-function)
    (setq interprogram-paste-function 'xclip-paste-function)
    ))
```

> **提示：**您还可以通过添加以下内容来启用终端鼠标支持：
>
> ```
> ;; xterm mouse support
> (require 'mouse)
> (xterm-mouse-mode t)
> ```
>
> 参见 [mwheel.el](https://www.opensource.apple.com/source/emacs/emacs-51/emacs/lisp/mwheel.el)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2024-11-05 ⓘ\]。

## 3.9　Emacs/概念

*难度：进阶 ｜ 篇幅：19.0 KB ｜ 所属：Emacs*

将要讲到：屏幕组织、用户输入类型、按键、鼠标输入、按键与命令

本文介绍 Emacs 的基本概念。

## 屏幕组织

在图形显示器上，例如在使用 [X](../../05_桌面与图形/X%20服务器/Xorg.md) 窗口系统的 [GNU](../../06_软件与应用/GNU/GNU.md)/Linux 上，Emacs 占用一个图形窗口。在文本终端上，Emacs 占用整个终端屏幕。我们将使用*帧*一词来指 Emacs 占用的图形窗口或终端屏幕。Emacs 在这两种帧上的表现非常相似。通常一开始只有一个帧，但也可以根据需要创建其他帧。

每个帧都由几个不同的区域组成。帧顶部是菜单栏，可以通过一系列菜单访问命令。在图形显示屏上，菜单栏的正下方是*工具栏*，这是一排图标，点击后可执行编辑命令。帧的最下方是回显区，这里显示信息，当 Emacs 要求输入信息时，你也可以在这里输入信息。

帧的主要区域，即*工具栏*（如果有的话）下方和回显区上方，称为*窗口*。因此，在本手册中，我们将在这个意义上使用*“窗口“*一词。图形显示系统通常使用含义不同的“窗口”一词，但如上所述，我们将这些图形窗口称为*“帧”*。

Emacs 窗口是显示*缓冲区*的地方，缓冲区就是你正在编辑或查看的文本或其他图形。在图形显示器上，窗口的一侧有一个滚动条，可以用来滚动缓冲区。窗口的最后一行是模式行。它显示缓冲区中的各种信息，如是否有未保存的更改、正在使用的编辑模式、当前行号等。

启动 Emacs 时，帧中通常只有一个窗口。不过，您可以水平或垂直细分这个窗口，创建多个窗口，每个窗口都可以独立显示一个缓冲区。

在任何时候，一个窗口都是选定窗口。在图形显示屏上，选定窗口显示一个较突出的光标（通常为实心和闪烁）；其他窗口显示一个不太突出的光标（通常为空心方框）。在文本终端上，只有一个光标，显示在选定窗口中。选定窗口中显示的缓冲区称为当前缓冲区，编辑工作就在这里进行。大多数 Emacs 命令都隐式地应用于当前缓冲区；未选中窗口中显示的文本主要供参考。如果在图形显示屏上使用多个帧，则选择特定帧就会选择该帧中的窗口。

### 点

所选窗口中的光标显示大多数编辑命令生效的位置，称为*点*。许多 Emacs 命令会将点移动到缓冲区中的不同位置；例如，在所需位置单击鼠标键 1（通常是*左键*），就可以将点移动到该位置。

默认情况下，所选窗口中的光标绘制为*实心块*，似乎位于一个字符上，但您应该将 point 视为**两个字符之间**；它位于光标下的字符之**前**。例如，如果您的文本是 “`Arch Linux 中社区`”，光标在“`社`”上，那么点就在“`中`”和“`社`”之间。如果在该位置插入字符，结果就是“`Arch Linux 中文社区`”，点位于“`文`”和“`社`”之间。因此，光标仍和以前一样位于“`社`”之上。

### 回显区

帧最下方的一行是回显区。它用于显示各种用途的少量文字。

回显区之所以叫回显区，是因为它的作用之一就是回显，即在输入多字符命令时显示其中的字符。单字符命令不会有回显。多字符命令如果在输入过程中停顿超过一秒，就会产生回显。然后，Emacs 会回显显示该命令到目前为止的所有字符，以提示您输入其余字符。一旦开始回显，命令的其余部分就会在你输入时立即回显。这种行为旨在为自信的用户提供快速响应，同时为犹豫不决的用户提供最大程度的反馈。

当命令无法执行时，回显区还用于显示错误信息。错误信息可能伴有蜂鸣声或屏幕闪烁。

有些命令会在回显区显示提示信息，告诉您该命令执行了什么操作，或为您提供一些特定信息。这些信息与错误信息不同，不会伴有蜂鸣声或闪光。例如，`C-x =`（按住 *Ctrl* 键并键入 *x*，然后放开 *Ctrl* 键并键入 *\=*）会显示一条信息，说明点的字符、它在缓冲区中的位置以及它在窗口中的当前列。耗时较长的命令在运行过程中通常会显示以“...”结尾的信息（有时还会以百分比的形式显示运行进度），并在完成后添加 “done”（已完成）。

回显区的消息保存在一个名为 *\*Messages\** 的特殊缓冲区中。如果错过了屏幕上短暂出现的信息，可以切换到 *\*Messages\** 缓冲区再次查看。*\*Messages\** 缓冲区有一定的行数限制，由变量 `message-log-max` 指定。(我们也没有解释变量；有关变量的更多信息，请参阅变量）。超过这个限制，每当在末尾添加一行新的信息时，就会从开头删除一行。

回显区还用于显示 **minibuffer**，这是一个特殊窗口，您可以在此输入命令参数，如要编辑的文件名。使用微型缓冲区时，回显 区显示的文本以提示字符串开头，活动光标显示在微型缓冲区内，**minibuffer** 暂时被视为选定窗口。您可以通过键入 C-g 退出 **minibuffer**。

### 模式行

每个窗口的底部都有一个模式行，用于描述当前缓冲区中正在发生的事情。当只有一个窗口时，模式行会出现在回显区的正上方；它是帧中倒数第二行。在图形显示屏上，模式行以 3D 方框的形式显示。Emacs 通常还会用不同于未选定窗口的颜色来绘制选定窗口的模式线，以使其更加突出。

模式线中显示的文本格式如下：

```
cs:ch-fr  buf      pos line   (major minor)
```

在文本终端上，该文本后面会有一连串延伸至窗口右边缘的破折号。在图形显示屏上，这些破折号会被省略。

*cs* 字符串及其后的冒号字符描述了当前缓冲区使用的字符集和换行约定。通常，Emacs 会自动为您处理这些设置，但有时掌握这些信息也很有用。

*cs* 描述缓冲区中文本的字符集。如果是破折号（'-'），则表示没有特殊的字符集处理（行结束约定可能除外，下一段将介绍）。*\=* 表示不进行任何转换，通常用于包含非文本数据的文件。其他字符表示各种编码系统，例如'1'表示 ISO Latin-1。

在文本终端上，cs 前面还有两个字符，分别描述键盘输入和终端输出的编码系统。此外，如果使用的是输入法，cs 前面还会有一个字符串，用来标识输入法。

cs 后面的字符通常是冒号。如果显示的字符串不同，则表示文件编码的行尾约定不尽相同。通常，文件中的文本行以换行符分隔，但有时也使用其他两种约定。MS-DOS 惯例使用一个回车符，后面跟一个换行符；在编辑此类文件时，冒号会根据操作系统的不同变为反斜线（'\\'）或'(DOS)'。旧版 Macintosh 系统采用的另一种习惯是使用回车符代替换行符；编辑此类文件时，冒号会变为正斜线（'/'）或'(Mac)'。在某些系统上，对于使用换行符作为行分隔符的文件，Emacs 会显示"(Unix)"而不是冒号。

在为 emacsclient 创建的帧中，下一个字符是"@"。对于作为守护进程运行的 Emacs 进程的帧来说，这种指示很典型。

如果窗口中显示的缓冲区与磁盘上相应文件的内容相同，即缓冲区未修改，则显示两个破折号（'--'）。如果缓冲区已修改，则显示两颗星（'\*\*'）。对于只读缓冲区，如果缓冲区已修改，则显示"%\*"，否则显示"%%"。

ch 后面的字符通常是破折号（'-'）。但是，如果当前缓冲区的默认目录在远程计算机上，则会显示"@"。

fr 给出所选帧的名称。它只出现在文本终端上。初始帧名为 "F1"。

buf 是窗口中显示的缓冲区名称。通常与正在编辑的文件名相同。请参阅使用多个缓冲区。

pos 显示窗口顶部上方或底部下方是否有附加文本。如果缓冲区较小，窗口中可以看到全部缓冲区，则 pos 为"全部"。否则，如果您看到的是缓冲区的开头，则是 "Top"；如果您看到的是缓冲区的结尾，则是 "Bot"；或者是 "nn%"，其中 nn 是窗口顶部以上缓冲区的百分比。使用 "Size Indication"（大小指示）模式，还可以显示缓冲区的大小。

行是字符 "L"，后跟在点处的行号。(打开列号模式，也可以显示当前列号。请参阅可选模式行功能）。

major 是缓冲区中使用的主要模式名称。主要模式是缓冲区的主要编辑模式，如文本模式、Lisp 模式、C 模式等。请参阅主要模式。某些主要模式会在主要模式名称后显示附加信息。例如，编译缓冲区和 Shell 缓冲区会显示子进程的状态。

minor （次要）模式是一些已启用的 minor 模式的列表，它们是在主要模式基础上提供附加功能的可选编辑模式。

有些功能虽然不是真正的 minor 模式，但只要开启，就会与次要模式一起列出。Narrow 表示显示的缓冲区的编辑范围仅限于部分文本。Def 表示当前正在定义键盘宏（请参阅键盘宏）。

此外，如果 Emacs 处于递归编辑层内，模式周围的括号会出现方括号（'\[...\]'）。如果 Emacs 处于另一个递归编辑层中的一个递归编辑层，则会出现双方括号，依此类推。由于递归编辑级别会对 Emacs 产生全局影响，因此每个窗口的模式行中都会出现此类方括号。

您可以更改模式行的外观及其内容格式。请参阅可选模式行功能。此外，模式行对鼠标敏感；单击模式行的不同部分可执行各种命令。此外，将鼠标指针悬停在模式行的鼠标敏感区域上会显示工具提示，其中包含通过单击模式行可以调用的命令信息。

### 菜单栏

每个 Emacs 帧的顶部通常都有一个菜单栏，您可以用它来执行常用操作。无需在此一一列举，因为你可以更容易地看到它们。

在支持鼠标的显示器上，你可以用鼠标从菜单栏中选择命令。菜单项右边的箭头表示它指向一个辅助菜单或子菜单。菜单项末尾的"..."表示该命令在实际执行之前会提示你进一步输入。

菜单栏中的某些命令也有普通的按键绑定；如果有，按键绑定会显示在项目本身之后。要查看某个菜单项的完整命令名称和文档，请键入 C-h k，然后用鼠标以通常的方式选择菜单栏（请参阅 "按键文档"）。

您也可以不使用鼠标，而是按 `F10` 键调用第一个菜单栏项目（运行命令 `menu-bar-open`）。然后可以使用箭头键或 `C-b`、`C-f`（左/右）、`C-p` 和 `C-n`（上/下）浏览菜单。要激活选定的菜单项，请按 `RET`；要取消菜单导航，请按 `C-g` 或 `ESC ESC ESC`。(不过请注意，如果 Emacs 是使用图形用户界面工具包创建的，那么菜单的绘制和控制都是由工具包完成的，取消菜单导航的按键顺序可能与上述描述不同）。

在文本终端上，可以选择访问回显区的菜单栏菜单。为此，请将变量 `tty-menu-open-use-tmm` 设置为**非零值**。然后键入 `F10` 将运行 `tmm-menubar` 命令，而不是下拉菜单。(您也可以键入 `` M-` ``，它将始终调用 *tmm-menubar*。）*tmm-menubar* 可让您用键盘选择菜单项。回显区会出现一个临时选项。您可以使用上下箭头键在菜单中移动到不同的项目，然后键入 `RET` 来选择项目。每个菜单项也由一个字母或数字（通常是项目名称中某个单词的首字母）指定。该字母或数字与项目名称之间用"==>"隔开。您可以键入该项目的字母或数字来选择该项。

## 用户输入类型

GNU Emacs 主要是为使用键盘而设计的。虽然可以使用鼠标通过菜单栏和工具栏发出编辑命令，但效率通常不如键盘。

Emacs 中的键盘输入是基于 ASCII 的重度扩展版本。简单字符，如 "a"、"B"、"3"、"="和空格符（表示为 SPC），可通过键入相应的键来输入。控制字符，如 `RET`、`TAB`、`DEL`、`ESC`、`F1`、`Home` 和 `LEFT`，以及非英语键盘上的某些字符，也可以通过这种方式输入。

Emacs 还能识别使用修改键输入的控制字符。例如，按住 `Ctrl` 键的同时按 a，就可以输入 `Control-a`；我们将其简称为 `C-a`。同样，`Meta-a` 或简称 `M-a` 是按住 `Alt` 键并按 `a` 键输入的。修改键也可用于非字母数字字符，例如 `C-F1` 或 `M-LEFT`。

您还可以使用以 `ESC` 开头的双字符序列输入 `Meta` 字符。因此，输入 `ESC a` 可以输入 `M-a`。输入 `ESC C-a` 可以输入 `C-M-a`（同时按住 `Ctrl` 和 `Alt`，然后按 `a`）。与 `Meta` 不同，`ESC` 是作为一个单独的字符输入的。输入下一个字符时不要按住 `ESC`，而是按下 `ESC` 并松开，然后输入下一个字符。在某些 `Meta` 键无法正常工作的文本终端上，这一功能非常有用。

Emacs 还支持另外 3 个修改键，请参阅修改键。

Emacs 广泛支持使用鼠标按钮、鼠标滚轮和其他指向设备（如触摸板和触摸屏）。

在图形显示器上，窗口管理器可能会阻止某些键盘输入，包括 `M-TAB`、`M-SPC`、`C-M-d` 和 `C-M-l`。如果遇到这种问题，可以自定义窗口管理器，使其不阻塞这些键，或者重新绑定受影响的 Emacs 命令。

简单字符和控制字符以及某些非键盘输入（如鼠标点击）统称为**输入事件**。

## 按键

有些 Emacs 命令只需一个输入事件即可调用，例如 `C-f` 在缓冲区中向前移动一个字符。其他命令需要两个或多个输入事件才能调用，例如 `C-x C-f` 和 `C-x 4 C-f`。

按键序列（简称**按键**）是由一个或多个输入事件组成的序列，作为一个单元是有意义的。如果一个按键序列调用了一个命令，我们就称它为一个*完整按键*；例如，`C-f`、`C-x C-f` 和 `C-x 4 C-f` 都是*完整按键*。如果一个键序的长度不足以调用命令，我们就称它为*前缀键*；从前面的例子中，我们可以看到 `C-x` 和 `C-x 4` 都是*前缀键*。每个按键序列要么是完整按键，要么是前缀按键。

前缀按键与后面的输入事件组合成一个更长的按键序列。例如，`C-x` 是一个前缀键，因此单独键入 `C-x` 并不会调用命令；相反，Emacs 会等待进一步的输入（如果暂停超过一秒，它就会回显 `C-x` 键以提示输入；参见回显区）。`C-x` 与下一个输入事件组合成一个双事件按键序列，它本身可以是一个前缀键（如 `C-x 4`），也可以是一个完整的按键（如 `C-x C-f`）。按键序列的长度没有限制，但在实际应用中很少超过三个或四个输入事件。

您不能在一个完整键上添加输入事件。例如，由于 `C-f` 是一个完整的键，所以 `C-f C-k` 这个双事件序列是两个键序列，而不是一个。

默认情况下，Emacs 的前缀键是`C-c、C-h、C-x、C-x RET、C-x @、C-x a、C-x n、C-x r、C-x t、C-x v、C-x 4、C-x 5、C-x 6、ESC` 和 `M-g`（`F1` 和 `F2` 是 `C-h` 和 `C-x 6` 的别名）。例如，如果删除了 `C-x 4` 的前缀定义，那么 `C-x 4 C-f` 就会成为一个无效的键序。请参阅自定义按键绑定。

在前缀键后输入帮助字符（`C-h` 或 `F1`），会显示以该前缀开头的命令列表。唯一的例外是 `ESC`： `ESC C-h` 等同于 `C-M-h`，后者的功能完全不同。不过，您可以使用 `F1` 显示以 `ESC` 开头的命令列表。

## 鼠标输入

默认情况下，Emacs 支持所有正常的鼠标操作，例如通过单击鼠标左键设置光标，以及通过拖动鼠标指针选择区域。所有鼠标操作都可以用来绑定命令，绑定方式与绑定键盘事件相同（请参阅[#按键](#按键)）。本节概述了如何在 Emacs 中使用鼠标。

单击鼠标左键时，Emacs 会收到一个 **mouse-1** 事件。要查看该事件绑定了什么命令，可以键入 `C-h c`，然后按下鼠标左键。同样，鼠标中键是 **mouse-2**，鼠标右键是 **mouse-3**。如果你使用的是带滚轮的鼠标，滚轮事件通常会绑定到 **wheel-down** 或 **wheel-up**，或者 **mouse-4** 和 **mouse-5**，但这取决于操作系统的配置。

一般来说，传统 [X](../../05_桌面与图形/X%20服务器/Xorg.md) 系统和终端会报告 **mouse-4** 和 **mouse-5**，而所有其他系统都会报告 **wheel-down** 和 **wheel-up**。

有些鼠标还有水平滚轮，触摸板通常也支持水平滚动。这些事件在所有系统中都会报告为 **wheel-left** 和 **wheel-right**，但终端和传统 [X](../../05_桌面与图形/X%20服务器/Xorg.md) 系统除外，在这些系统中报告为 **mouse-6** 和 **mouse-7**。

您还可以将键盘修饰符与鼠标事件结合起来，例如，您可以绑定一个特殊命令，当您按住 *Meta* 键并使用鼠标中键时触发该命令。在这种情况下，事件名称将是 **M-mouse-2**。

在某些系统中，还可以绑定用于处理触摸屏事件的命令。在这种情况下，事件名称为触摸屏更新（*touchscreen-update*）和触摸屏结束（*touchscreen-end*）。

## 按键与命令

本文中有很多段落告诉你特定按键的作用。但 Emacs 并不直接为按键赋予含义。相反，Emacs 将含义分配给已命名的命令，然后通过将按键与命令绑定来赋予按键含义。

每个命令都有一个由程序员选择的名称。名称通常由几个英文单词组成，中间用破折号隔开，例如 *next-line* 或 *forward-word*。在内部，每个命令都是一种特殊的 Lisp 函数，与命令相关的操作通过运行函数来执行。请参阅《Emacs Lisp 参考手册》中的"什么是函数"。

键与命令之间的绑定记录在称为键映射的表中。

当我们说 "C-n 垂直向下移动一行" 时，我们忽略了一个微妙的区别，这个区别在普通使用中无关紧要，但对于 Emacs 的定制却至关重要。命令 *next-line* 是垂直向下移动一行。`C-n` 具有这种效果是因为它绑定了 *next-line*。如果将 `C-n` 与 *forward-word* 命令重新绑定，`C-n` 将向前移动一个字。

在本文中，我们经常把 `C-n` 等按键说成是命令，尽管严格来说，按键是与命令绑定的。通常情况下，我们在提到运行命令的按键后，会在括号内注明真正执行命令的命令名称。例如，我们会说 "命令 C-n（next-line）将点垂直向下移动"，意思是命令 *next-line* 将点垂直向下移动，而按键 `C-n` 通常与之绑定。

既然是讨论自定义，我们就应该介绍一下变量。通常情况下，命令的说明中会说："要改变这一点，请设置变量 mumble-foo"。变量是用来存储数值的名称。本文中记录的大多数变量都是用于自定义的：Emacs 的某些命令或其他部分会检查变量，并根据你设置的值做出不同的行为。在你对自定义变量感兴趣之前，可以忽略有关变量的信息。然后再阅读有关变量的基本信息，这样有关特定变量的信息就会变得有意义。

## 3.10　Emacs/使用

*难度：高级 ｜ 篇幅：36.4 KB ｜ 所属：Emacs*

将要讲到：进入 Emacs、退出 Emacs、基本编辑命令

本文介绍 Emacs 的基础使用。

阅读本文前，请先阅读 [Emacs/概念](Emacs_概念.md)。

## 进入 Emacs

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[Emacs/运行](Emacs_运行.md)。**
>
> **附注：** 内容有重复。（在 [Talk:Emacs/使用](https://wiki.archlinuxcn.org/wiki/Talk:Emacs/%E4%BD%BF%E7%94%A8) 中讨论）

调用 Emacs 的通常方法是使用 [shell](../命令行解释器/命令行解释器.md) 命令 `emacs`。在 GUI 终端上运行 Unix shell 的终端窗口中，可以使用 `emacs &` 在后台运行 Emacs；这样，Emacs 就不会占用终端窗口，因此你可以用它来运行其他 [shell](../命令行解释器/命令行解释器.md) 命令。

Emacs 启动时，初始帧会显示一个名为 *\*GNU Emacs\** 的特殊缓冲区。这个启动界面包含 Emacs 的相关信息，以及对初学者有用的常用任务链接。例如，激活 *Emacs Tutorial* 链接可打开 Emacs 教程；其作用与 `C-h t`（`help-with-tutorial`）命令相同。要激活链接，可以将点移动到该链接上并键入 `RET`，或者用 `mouse-1`（鼠标左键）单击该链接。

使用命令行参数，可以让 Emacs 在启动时立即访问一个或多个文件。例如，`emacs 文本.txt` 启动 [Emacs](Emacs.md) 时，缓冲区会显示文件*文本.txt* 的内容。这一功能主要是为了与其他[文本编辑器](../../06_软件与应用/应用程序/应用程序列表_文档.md)兼容，因为其他编辑器是为了从 [shell](../命令行解释器/命令行解释器.md) 启动进行短时间编辑而设计的。如果以这种方式调用 Emacs，初始帧会被分成两个窗口，一个显示指定文件，另一个显示启动屏幕。

一般来说，每次编辑文件都重新启动 Emacs 既**不必要又浪费**。建议使用 Emacs 的方法是在登录后只启动**一次**，并在同一个 Emacs 会话中完成所有编辑工作。如果这样使用 Emacs，Emacs 会话就会积累一些有价值的上下文，如 *kill ring*、*寄存器*、*撤销历史*和*标记环数据*，这些都会使编辑更加方便。本文稍后将介绍这些功能。

要在 Emacs 运行时从其他程序编辑文件，可以使用 emacsclient 辅助程序在现有的 Emacs 会话中打开文件。

Emacs 还接受其他命令行参数，这些参数会告诉它加载某些 Lisp 文件、在何处放置初始框架等。参见 Emacs 调用的命令行参数。

如果变量 `inhibit-startup-screen` 不为零，则 Emacs **不显示**启动屏幕。在这种情况下，如果在命令行中指定了一个或多个文件，Emacs 会简单地显示这些文件；否则，它会显示一个名为 `*scratch*` 的缓冲区，该缓冲区可用于交互式地评估 Emacs Lisp 表达式。你可以使用自定义工具或编辑初始化文件来设置变量 `inhibit-startup-screen`。

通过将变量 `initial-buffer-choice` 设置为命名文件或目录的字符串，也可以强制 Emacs 在启动时显示文件或目录。`initial-buffer-choice` 的值也可以是一个函数（无参数），它应该返回一个缓冲区，然后显示出来。如果 `initial-buffer-choice` 的值**不是零**，那么如果你在命令行中指定了任何文件，Emacs 仍会访问它们，但最初不会显示它们。

## 退出 Emacs

同 [Vim](../文本编辑器/Vim.md)/[Neovim](../文本编辑器/Neovim.md)、[Helix](../文本编辑器/Helix.md) 等编辑器一样，初学者可能不知如何退出。

**C-x C-c**
: 杀死 Emacs（save-buffers-kill-terminal）。
**C-z**
: 在文本终端上，挂起 Emacs；在图形显示屏上，图标化（或*最小化*）所选帧（挂起帧）。
**系统 [shell](../命令行解释器/命令行解释器.md) 内运行 `killall emacs`**
: 强制终止 Emacs 进程，可以在 Emacs 卡死时使用。

杀死 Emacs 意味着终止 Emacs 程序。为此，请键入 `C-x C-c`（`save-buffers-kill-terminal`）。使用两个字符的键序是为了**防止意外键入**。如果输入 `C-x C-c` 时有任何修改过的文件访问缓冲区，Emacs 会首先保存这些缓冲区。如果没有全部保存，则会再次请求确认，因为未保存的更改将丢失。如果有子进程仍在运行，Emacs 也会要求确认，因为杀死 Emacs 也会杀死这些子进程。

如果将 Emacs 用作服务器，`C-x C-c` 的行为会比较特殊。如果你在客户端框架中输入它，它会关闭客户端连接。请参阅将 Emacs 用作服务器。

Emacs 可以选择在杀死会话时记录某些会话信息，例如当时正在访问的文件。下次启动 Emacs 时就可以使用这些信息。请参阅保存 Emacs 会话。

如果变量 `confirm-kill-emacs` 的值不是零，`C-x C-c` 会假定其值是一个谓词函数，并调用该函数。如果函数调用的结果不是零，会话就会被杀死，否则 Emacs 会继续运行。用来作为 `confirm-kill-emacs` 值的一个方便的函数是 `yes-or-no-p` 函数。`confirm-kill-emacs` 的默认值为 **nil**。

如果变量 `confirm-kill-processes` 的值为 nil，则 `C-x C-c` 在杀死 Emacs 启动的子进程之前不会请求确认。默认值为 **t**。

要进一步自定义 Emacs 退出时发生的情况，请参阅《GNU Emacs Lisp 参考手册》中的杀死 Emacs。

要在不提示保存的情况下杀死 Emacs，请键入 `M-x kill-emacs`。

`C-z` 运行命令 `suspend-frame`。在图形显示屏上，该命令会最小化（或图标化）所选的 Emacs 框架，使其隐藏起来，以便稍后恢复（具体如何隐藏取决于窗口系统）。在文本终端上，`C-z` 命令会暂停 Emacs，暂时停止程序并将控制权交还给父进程（通常是 [shell](../命令行解释器/命令行解释器.md)）；在大多数 shell 中，暂停 Emacs 后可以使用 shell 命令 `%emacs` 恢复 Emacs。

文本终端通常会侦听某些特殊字符，其含义是杀死或暂停正在运行的程序。在 Emacs 中，这种终端功能是关闭的。在 Emacs 中，`C-z` 和 `C-x C-c` 作为按键的含义是受多个操作系统中使用 `C-z` 和 `C-c` 作为停止或杀死程序的字符的启发，但这是它们与操作系统的唯一关系。你可以自定义这些键来运行你选择的任何命令。

## 基本编辑命令

在此，我们将讲解如何输入文本、进行修改并将文本保存到文件中的基础知识。如果您对这些内容不熟悉，建议您首先运行 Emacs 边做边学教程，键入 `C-h t`（`help-with-tutorial`）。

### 插入文本

您可以通过键入相关键来插入普通图形字符（如 "a"、"B"、"3" 和 "="）。这样就可以将字符添加到点的缓冲区中。插入后，点将向前移动，因此点将保持在插入文本之后。参见点。

要结束一行并开始新一行，键入 `RET`（换行）。(`RET` 键在键盘上可能标有 Return、Enter 或有趣的向左箭头，但在本手册中我们将其称为 `RET`，中国人常称其**回车**）。该命令在缓冲区中插入一个换行符，然后根据主要模式缩进。如果点位于行尾，效果是在其后创建一个新的空行并缩进新行；如果点位于行中间，则在该位置分行。要关闭自动缩进功能，可以禁用 "Electric Indent" 模式（请参阅 "缩进的便利功能"），或者键入 C-j，只插入新行，而不进行任何自动缩进。

正如我们在本手册后面所解释的，你可以通过开启次要模式来改变 Emacs 处理文本插入的方式。例如，名为 "Auto Fill" 的次要模式会在行太长时自动分行。名为 "Overwrite" 模式的次要模式会使插入的字符替换（覆盖）现有文本，而不是将其推到右边。请参阅次要模式。

只有图形字符可以通过键入相关的按键来插入；其他按键作为编辑命令，不会自行插入。例如，默认情况下 DEL 运行 delete-backward-char 命令（某些模式将其绑定到不同的命令）；但它不会插入字面意义上的 "DEL "字符（ASCII 字符代码 127）。

要插入一个非图形字符或键盘不支持的字符，首先要输入 C-q（quoted-insert）来引用该字符。使用 C-q 有两种方法：

- C-q 后接任何非图形字符（甚至是 C-g）都会插入该字符。例如，C-q DEL 插入一个字面意义上的 "DEL" 字符。
- C-q 后跟一串八进制数字，插入指定八进制字符代码的字符。您可以使用任意八进制数字；任何非数字都会终止序列。如果终止字符是 RET，则 RET 仅用于终止序列。任何其他非数字都会终止序列，然后作为正常输入，如 C-q 1 0 1 B 插入 "AB"。

: 在普通的非二进制覆写模式下，八进制序列的使用是禁用的，这样可以方便地插入一个数字，而不是用它来覆写。

要使用十进制或十六进制代替八进制，可将变量 read-quoted-char-radix 设置为 10 或 16。如果弧度为 16，字母 a 至 f 就会像数字一样作为字符代码的一部分。大小写将被忽略。

通过以 C-x 8 开头的命令，可以插入一些常见的 Unicode 字符。例如，C-x 8 \[ 插入"'"，它是 Unicode 代码点 U+2018 左单引号标记，有时也称为左单引号 "弯引号 "或 "卷曲引号"。同样，C-x 8 \]、C-x 8 { 和 C-x 8 } 分别插入"'"、""和""。此外，Alt 键的作用类似于 C-x 8（除非后面有 RET）；例如，A-\[ 的作用类似于 C-x 8 \[ 并插入*。要查看哪些字符具有 C-x 8 速记功能，请键入 C-x 8 C-h。*

或者，也可以使用 C-x 8 RET（插入-字符）命令。该命令使用微型缓冲区提示输入字符的 Unicode 名称或码位。如果输入的是名称，命令将提供补全（参见补全）。如果输入码位，应为十六进制数（Unicode 的约定），或具有指定弧度的数字，例如 #o23072（八进制）；参见《Emacs Lisp 参考手册》中的整数基础知识。然后，命令将相应的字符插入缓冲区。

例如，以下命令都插入了相同的字符：

```
C-x 8 RET left single quotation mark RET
C-x 8 RET left sin TAB RET
C-x 8 RET 2018 RET
C-x 8 [
A-[  (if the Alt key works)
`    (in Electric Quote mode)
```

C-q 或 C-x 8 ...的数字参数指定插入字符的份数（参见数字参数）。

作为 C-x 8 的替代方法，可以通过键入 C-u C-x \\ iso-transl RET 选择相应的暂存输入法，然后通过键入 C-x \\ \[ 临时激活该暂存输入法，将插入相同的字符 ' （参见暂存输入法）。

此外，在某些情况下，如果输入使用重音符号和撇号的引号 "like this"，即使没有 C-x 8 命令，也会转换为使用单引号的 "like this "形式。同样，使用双重音符号和撇号键入引号 "like this"，也会转换为使用双引号的 "like this "形式。参见引号。

### 更改点的位置

除了插入字符外，还必须知道如何移动点（请参阅 "点"）。键盘命令 C-f、C-b、C-n 和 C-p 可分别向右、向左、向下和向上移动点。您也可以使用大多数键盘上的方向键移动点： 不过，许多 Emacs 用户发现，使用方向键比使用控制键要慢，因为你需要将手移到键盘上这些键所在的区域。

你也可以点击鼠标左键，将点移动到所点击的位置。Emacs 还提供了多种额外的键盘命令，可以以更复杂的方式移动点。

**C-f**
: Move forward one character (forward-char).
**RIGHT**
: This command (right-char) behaves like C-f, except when point is in a right-to-left paragraph (see Bidirectional Editing).
**C-b**
: Move backward one character (backward-char).
**LEFT**
: This command (left-char) behaves like C-b, except if the current paragraph is right-to-left (see Bidirectional Editing).
**C-n**
**DOWN**
: Move down one screen line (next-line). This command attempts to keep the horizontal position unchanged, so if you start in the middle of one line, you move to the middle of the next.
**C-p**
**UP**
: Move up one screen line (previous-line). This command preserves position within the line, like C-n.
**C-a**
**Home**
: Move to the beginning of the line (move-beginning-of-line).
**C-e**
**End**
: Move to the end of the line (move-end-of-line).
**M-f**
: Move forward one word (forward-word). See Words.
**C-RIGHT**
**M-RIGHT**
: This command (right-word) behaves like M-f, except it moves backward by one word if the current paragraph is right-to-left. See Bidirectional Editing.
**M-b**
: Move backward one word (backward-word). See Words.
**C-LEFT**
**M-LEFT**
: This command (left-word) behaves like M-b, except it moves forward by one word if the current paragraph is right-to-left. See Bidirectional Editing.
**M-r**
: Without moving the text on the screen, reposition point on the left margin of the center-most text line of the window; on subsequent consecutive invocations, move point to the left margin of the top-most line, the bottom-most line, and so forth, in cyclic order (move-to-window-line-top-bottom).
: A numeric argument says which screen line to place point on, counting downward from the top of the window (zero means the top line). A negative argument counts lines up from the bottom (−1 means the bottom line). See Numeric Arguments, for more information on numeric arguments.
**M-<**
: Move to the top of the buffer (beginning-of-buffer). With numeric argument n, move to n/10 of the way from the top. On graphical displays, C-HOME does the same.
**M->**
: Move to the end of the buffer (end-of-buffer). On graphical displays, C-END does the same.
**C-v**
**PageDown**
**next**
: Scroll the display one screen forward, and move point onscreen if necessary (scroll-up-command). See Scrolling.
**M-v**
**PageUp**
**prior**
: Scroll one screen backward, and move point onscreen if necessary (scroll-down-command). See Scrolling.
**M-g c**
: Read a number n and move point to buffer position n. Position 1 is the beginning of the buffer. If point is on or just after a number in the buffer, that is the default for n. Just type RET in the minibuffer to use it. You can also specify n by giving M-g c a numeric prefix argument.
**M-g M-g**
**M-g g**
: Read a number n and move point to the beginning of line number n (goto-line). Line 1 is the beginning of the buffer. If point is on or just after a number in the buffer, that is the default for n. Just type RET in the minibuffer to use it. You can also specify n by giving M-g M-g a numeric prefix argument. See Creating and Selecting Buffers, for the behavior of M-g M-g when you give it a plain prefix argument. Alternatively, you can use the command goto-line-relative to move point to the line relative to the accessible portion of the narrowed buffer.
: goto-line has its own history list (see Minibuffer History). You can have either a single list shared between all buffers (the default) or a separate list for each buffer, by customizing the user option goto-line-history-local.
**M-g TAB**
: Read a number n and move to column n in the current line. Column 0 is the leftmost column. If called with a prefix argument, move to the column number specified by the argument’s numeric value.
**C-x C-n**
: Use the current column of point as the semipermanent goal column (set-goal-column) in the current buffer. When a semipermanent goal column is in effect, C-n, C-p, <prior> and <next> always try to move to this column, or as close as possible to it, after moving vertically. The goal column remains in effect until canceled.
**C-u C-x C-n**
: Cancel the goal column. Henceforth, C-n and C-p try to preserve the horizontal position, as usual.

When a line of text in the buffer is longer than the width of the window, Emacs usually displays it on two or more screen lines, a.k.a. visual lines. For convenience, C-n and C-p move point by screen lines, as do the equivalent keys down and up. You can force these commands to move according to logical lines (i.e., according to the text lines in the buffer) by setting the variable line-move-visual to nil; if a logical line occupies multiple screen lines, the cursor then skips over the additional screen lines. For details, see Continuation Lines. See Variables, for how to set variables such as line-move-visual.

Unlike C-n and C-p, most of the Emacs commands that work on lines work on logical lines. For instance, C-a (move-beginning-of-line) and C-e (move-end-of-line) respectively move to the beginning and end of the logical line. Whenever we encounter commands that work on screen lines, such as C-n and C-p, we will point these out.

When line-move-visual is nil, you can also set the variable track-eol to a non-nil value. Then C-n and C-p, when starting at the end of the logical line, move to the end of the next logical line. Normally, track-eol is nil.

C-n normally stops at the end of the buffer when you use it on the last line in the buffer. However, if you set the variable next-line-add-newlines to a non-nil value, C-n on the last line of a buffer creates an additional line at the end and moves down into it.

### 擦除文本

**DEL**
**BACKSPACE**
: Delete the character before point, or the region if it is active (delete-backward-char).
**Delete**
: Delete the character or grapheme cluster after point, or the region if it is active (delete-forward-char).
**C-d**
: Delete the character after point (delete-char).
**C-k**
: Kill to the end of the line (kill-line).
**M-d**
: Kill forward to the end of the next word (kill-word).
**M-DEL**
**M-BACKSPACE**
: Kill back to the beginning of the previous word (backward-kill-word).

The DEL (delete-backward-char) command removes the character before point, moving the cursor and the characters after it backwards. If point was at the beginning of a line, this deletes the preceding newline, joining this line to the previous one.

If, however, the region is active, DEL instead deletes the text in the region. See The Mark and the Region, for a description of the region.

On most keyboards, DEL is labeled BACKSPACE, but we refer to it as DEL in this manual. (Do not confuse DEL with the Delete key; we will discuss Delete momentarily.) On some text terminals, Emacs may not recognize the DEL key properly. See If DEL Fails to Delete, if you encounter this problem.

The Delete (delete-forward-char) command deletes in the opposite direction: it deletes the character after point, i.e., the character under the cursor. If point was at the end of a line, this joins the following line onto this one. Like DEL, it deletes the text in the region if the region is active (see The Mark and the Region). If the character after point is composed with following characters and displayed as a single display unit, a so-called grapheme cluster representing the entire sequence, Delete deletes the entire sequence in one go. This is in contrast to DEL which always deletes a single character, even if the character is composed.

C-d (delete-char) deletes the character after point, similar to Delete, but regardless of whether the region is active.

See Deletion, for more detailed information about the above deletion commands.

C-k (kill-line) erases (kills) a line at a time. If you type C-k at the beginning or middle of a line, it kills all the text up to the end of the line. If you type C-k at the end of a line, it joins that line with the following line.

See Killing and Moving Text, for more information about C-k and related commands.

### 撤销更改

**C-/**
**C-x u**
**C-_**
: Undo one entry of the undo records—usually, one command worth (undo). (The first key might be unavailable on text-mode displays.)

Emacs records a list of changes made in the buffer text, so you can undo recent changes. This is done using the undo command, which is bound to C-/ (as well as C-x u and C-_). Normally, this command undoes the last change, moving point back to where it was before the change. The undo command applies only to changes in the buffer; you can’t use it to undo cursor motion.

On a terminal that supports the Control modifier on all other keys, the easiest way to invoke undo is with C-/, since that doesn’t need the Shift modifier. On terminals which allow only the ASCII control characters, C-/ does not exist, but for many of them C-/ still works because it actually sends C-_ to Emacs, while many others allow you to omit the Shift modifier when you type C-_ (in effect pressing C--), making that the most convenient way to invoke undo.

Although each editing command usually makes a separate entry in the undo records, very simple commands may be grouped together. Sometimes, an entry may cover just part of a complex command.

If you repeat C-/ (or its aliases), each repetition undoes another, earlier change, back to the limit of the undo information available. If all recorded changes have already been undone, the undo command displays an error message and does nothing.

To learn more about the undo command, see Undo.

### 文件

Text that you insert in an Emacs buffer lasts only as long as the Emacs session. To keep any text permanently, you must put it in a file.

Suppose there is a file named test.emacs in your home directory. To begin editing this file in Emacs, type

```
C-x C-f test.emacs RET
```

Here the file name is given as an argument to the command C-x C-f (find-file). That command uses the minibuffer to read the argument, and you type RET to terminate the argument (see The Minibuffer).

Emacs obeys this command by visiting the file: it creates a buffer, copies the contents of the file into the buffer, and then displays the buffer for editing. If you alter the text, you can save the new text in the file by typing C-x C-s (save-buffer). This copies the altered buffer contents back into the file test.emacs, making them permanent. Until you save, the changed text exists only inside Emacs, and the file test.emacs is unaltered.

To create a file, just visit it with C-x C-f as if it already existed. This creates an empty buffer, in which you can insert the text you want to put in the file. Emacs actually creates the file the first time you save this buffer with C-x C-s.

To learn more about using files in Emacs, see File Handling.

### 帮助

If you forget what a key does, you can find out by typing C-h k (describe-key), followed by the key of interest; for example, C-h k C-n tells you what C-n does.

The prefix key C-h stands for “help”. The key F1 serves as an alias for C-h. Apart from C-h k, there are many other help commands providing different kinds of help.

See Help, for details.

### 空白行

Here are special commands and techniques for inserting and deleting blank lines.

**C-o**
: Insert a blank line after the cursor (open-line).
**C-x C-o**
: Delete all but one of many consecutive blank lines (delete-blank-lines).

We have seen how RET (newline) starts a new line of text. However, it may be easier to see what you are doing if you first make a blank line and then insert the desired text into it. This is easy to do using the key C-o (open-line), which inserts a newline after point but leaves point in front of the newline. After C-o, type the text for the new line.

You can make several blank lines by typing C-o several times, or by giving it a numeric argument specifying how many blank lines to make. See Numeric Arguments, for how. If you have a fill prefix, the C-o command inserts the fill prefix on the new line, if typed at the beginning of a line. See The Fill Prefix.

The easy way to get rid of extra blank lines is with the command C-x C-o (delete-blank-lines). If point lies within a run of several blank lines, C-x C-o deletes all but one of them. If point is on a single blank line, C-x C-o deletes it. If point is on a nonblank line, C-x C-o deletes all following blank lines, if any exists.

### 延续线

Sometimes, a line of text in the buffer—a logical line—is too long to fit in the window, and Emacs displays it as two or more screen lines, or visual lines. This is called line wrapping or continuation, and the long logical line is called a continued line. On a graphical display, Emacs indicates line wrapping with small bent arrows in the left and right window fringes. On a text terminal, Emacs indicates line wrapping by displaying a ‘\\’ character at the right margin.

Most commands that act on lines act on logical lines, not screen lines. For instance, C-k kills a logical line. As described earlier, C-n (next-line) and C-p (previous-line) are special exceptions: they move point down and up, respectively, by one screen line (see Changing the Location of Point).

Emacs can optionally truncate long logical lines instead of continuing them. This means that every logical line occupies a single screen line; if it is longer than the width of the window, the rest of the line is not displayed. On a graphical display, a truncated line is indicated by a small straight arrow in the right fringe; on a text terminal, it is indicated by a ‘$’ character in the right margin. See Line Truncation.

By default, continued lines are wrapped at the right window edge. Since the wrapping may occur in the middle of a word, continued lines can be difficult to read. The usual solution is to break your lines before they get too long, by inserting newlines. If you prefer, you can make Emacs insert a newline automatically when a line gets too long, by using Auto Fill mode. See Filling Text.

Sometimes, you may need to edit files containing many long logical lines, and it may not be practical to break them all up by adding newlines. In that case, you can use Visual Line mode, which enables word wrapping: instead of wrapping long lines exactly at the right window edge, Emacs wraps them at the word boundaries (i.e., space or tab characters) nearest to the right window edge. Visual Line mode also redefines editing commands such as C-a, C-n, and C-k to operate on screen lines rather than logical lines. See Visual Line Mode.

### 光标位置信息

Here are commands to get information about the size and position of parts of the buffer, and to count words and lines.

**M-x what-line**
: Display the line number of point.
**M-x line-number-mode**
**M-x column-number-mode**
: Toggle automatic display of the current line number or column number. See Optional Mode Line Features. If you want to have a line number displayed before each line, see Customization of Display.
**M-=**
: Display the number of lines, sentences, words, and characters that are present in the region (count-words-region). See The Mark and the Region, for information about the region.
**M-x count-words**
: Display the number of lines, sentences, words, and characters that are present in the buffer. If the region is active (see The Mark and the Region), display the numbers for the region instead.
**C-x =**
: Display the character code of character after point, character position of point, and column of point (what-cursor-position).
**M-x hl-line-mode**
: Enable or disable highlighting of the current line. See Displaying the Cursor.
**M-x size-indication-mode**
: Toggle automatic display of the size of the buffer. See Optional Mode Line Features.

M-x what-line displays the current line number in the echo area. This command is usually redundant because the current line number is shown in the mode line (see The Mode Line). However, if you narrow the buffer, the mode line shows the line number relative to the accessible portion (see Narrowing). By contrast, what-line displays both the line number relative to the narrowed region and the line number relative to the whole buffer.

M-= (count-words-region) displays a message reporting the number of lines, sentences, words, and characters in the region (see The Mark and the Region, for an explanation of the region). With a prefix argument, C-u M-=, the command displays a count for the entire buffer.

The command M-x count-words does the same job, but with a different calling convention. It displays a count for the region if the region is active, and for the buffer otherwise.

The command C-x = (what-cursor-position) shows information about the current cursor position and the buffer contents at that position. It displays a line in the echo area that looks like this:

```
Char: c (99, #o143, #x63) point=28062 of 36168 (78%) column=53
```

After ‘Char:’, this shows the character in the buffer at point. The text inside the parenthesis shows the corresponding decimal, octal and hex character codes; for more information about how C-x = displays character information, see Introduction to International Character Sets. After ‘point=’ is the position of point as a character count (the first character in the buffer is position 1, the second character is position 2, and so on). The number after that is the total number of characters in the buffer, and the number in parenthesis expresses the position as a percentage of the total. After ‘column=’ is the horizontal position of point, in columns counting from the left edge of the window.

If the user option what-cursor-show-names is non-nil, the name of the character, as defined by the Unicode Character Database, is shown as well. The part in parentheses would then become:

```
(99, #o143, #x63, LATIN SMALL LETTER C)
```

If the buffer has been narrowed, making some of the text at the beginning and the end temporarily inaccessible, C-x = displays additional text describing the currently accessible range. For example, it might display this:

```
Char: C (67, #o103, #x43) point=252 of 889 (28%) <231-599> column=0
```

where the two extra numbers give the smallest and largest character position that point is allowed to assume. The characters between those two positions are the accessible ones. See Narrowing.

Related, but different feature is display-line-numbers-mode (see Customization of Display).

### 数值参数

In the terminology of mathematics and computing, argument means “data provided to a function or operation”. You can give any Emacs command a numeric argument (also called a prefix argument). Some commands interpret the argument as a repetition count. For example, giving C-f an argument of ten causes it to move point forward by ten characters instead of one. With these commands, no argument is equivalent to an argument of one, and negative arguments cause them to move or act in the opposite direction.

The easiest way to specify a numeric argument is to type a digit and/or a minus sign while holding down the Meta key. For example,

```
M-5 C-n
```

moves down five lines. The keys M-1, M-2, and so on, as well as M--, are bound to commands (digit-argument and negative-argument) that set up an argument for the next command. M-- without digits normally means −1.

If you enter more than one digit, you need not hold down the Meta key for the second and subsequent digits. Thus, to move down fifty lines, type

```
M-5 0 C-n
```

Note that this does not insert five copies of ‘0’ and move down one line, as you might expect—the ‘0’ is treated as part of the prefix argument.

(What if you do want to insert five copies of ‘0’? Type M-5 C-u 0. Here, C-u terminates the prefix argument, so that the next keystroke begins the command that you want to execute. Note that this meaning of C-u applies only to this case. For the usual role of C-u, see below.)

Instead of typing M-1, M-2, and so on, another way to specify a numeric argument is to type C-u (universal-argument) followed by some digits, or (for a negative argument) a minus sign followed by digits. A minus sign without digits normally means −1.

C-u alone has the special meaning of “four times”: it multiplies the argument for the next command by four. C-u C-u multiplies it by sixteen. Thus, C-u C-u C-f moves forward sixteen characters. Other useful combinations are C-u C-n, C-u C-u C-n (move down a good fraction of a screen), C-u C-u C-o (make sixteen blank lines), and C-u C-k (kill four lines).

You can use a numeric argument before a self-inserting character to insert multiple copies of it. This is straightforward when the character is not a digit; for example, C-u 6 4 a inserts 64 copies of the character ‘a’. But this does not work for inserting digits; C-u 6 4 1 specifies an argument of 641. You can separate the argument from the digit to insert with another C-u; for example, C-u 6 4 C-u 1 does insert 64 copies of the character ‘1’.

Some commands care whether there is an argument, but ignore its value. For example, the command M-q (fill-paragraph) fills text; with an argument, it justifies the text as well. (See Filling Text, for more information on M-q.) For these commands, it is enough to specify the argument with a single C-u.

Some commands use the value of the argument as a repeat count but do something special when there is no argument. For example, the command C-k (kill-line) with argument n kills n lines, including their terminating newlines. But C-k with no argument is special: it kills the text up to the next newline, or, if point is right at the end of the line, it kills the newline itself. Thus, two C-k commands with no arguments can kill a nonblank line, just like C-k with an argument of one. (See Killing and Moving Text, for more information on C-k.)

A few commands treat a plain C-u differently from an ordinary argument. A few others may treat an argument of just a minus sign differently from an argument of −1. These unusual cases are described when they come up; they exist to make an individual command more convenient, and they are documented in that command’s documentation string.

We use the term prefix argument to emphasize that you type such arguments before the command, and to distinguish them from minibuffer arguments (see The Minibuffer), which are entered after invoking the command.

On graphical displays, C-0, C-1, etc. act the same as M-0, M-1, etc.

### 重复命令

Many simple commands, such as those invoked with a single key or with M-x command-name RET, can be repeated by invoking them with a numeric argument that serves as a repeat count (see Numeric Arguments). However, if the command you want to repeat prompts for input, or uses a numeric argument in another way, that method won’t work.

The command C-x z (repeat) provides another way to repeat an Emacs command many times. This command repeats the previous Emacs command, whatever that was. Repeating a command uses the same arguments that were used before; it does not read new arguments each time.

To repeat the command more than once, type additional z’s: each z repeats the command one more time. Repetition ends when you type a character other than z or press a mouse button.

For example, suppose you type C-u 2 0 C-d to delete 20 characters. You can repeat that command (including its argument) three additional times, to delete a total of 80 characters, by typing C-x z z z. The first C-x z repeats the command once, and each subsequent z repeats it once again.

You can also activate repeat-mode which allows repeating commands bound to sequences of two or more keys by typing a single character. For example, after typing C-x u (undo, see Undo) to undo the most recent edits, you can undo many more edits by typing u u u…. Similarly, type C-x o o o… instead of C-x o C-x o C-x o… to switch to the window several windows away. This works by entering a transient repeating mode after you type the full key sequence that invokes the command; the single-key shortcuts are shown in the echo area.

Only some commands support repetition in repeat-mode; type M-x describe-repeat-maps RET to see which ones.

The single-character shortcuts enabled by the transient repeating mode do not need to be identical: for example, after typing C-x {, either { or } or ^ or v, or any series that mixes these characters in any order, will resize the selected window in respective ways. Similarly, after M-g n or M-g p, typing any sequence of n and/or p in any mix will repeat next-error and previous-error to navigate in a \*compilation\* or \*grep\* buffer (see Compilation Mode).

Typing any key other than those defined to repeat the previous command exits the transient repeating mode, and then the key you typed is executed normally. You can also define a key which will exit the transient repeating mode without executing the key which caused the exit. To this end, customize the user option repeat-exit-key to name a key; one natural value is RET. Finally, it’s possible to break the repetition chain automatically after some amount of idle time: customize the user option repeat-exit-timeout to specify the idle time in seconds after which this transient repetition mode will be turned off automatically.
