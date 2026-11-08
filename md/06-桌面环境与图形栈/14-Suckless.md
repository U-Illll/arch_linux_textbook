# 第 14 章　Suckless

> 本章处理桌面里最容易被套件包办的三件事：锁屏、菜单、终端。Slock、Dmenu、St 同属 suckless，体积小、依赖少，代价是默认状态近乎空白，能拿到多少功能取决于配置读到什么程度。建议先读 St：篇幅最长，除配置外还专列故障排除，问题多集中于此；再读 Dmenu，安装与设置过一遍，延伸与其他资源按需取用；Slock 最短，扫一遍用法与提示即可，其余可跳。最容易踩的坑是把三者当成装完即用的成品，遇到异常应先回到安装与配置两节逐项核对，而不是先去别处找现成模板。

<!-- 本章 3 节，共 13 KB -->

## 14.1　Slock

*难度：入门 ｜ 篇幅：2.9 KB ｜ 所属：Suckless*

将要讲到：安装、配置、用法、提示与窍门、参见

Slock，或 "Simple X display locker"，是用于 [X](../X%20服务器/Xorg.md) 的显示锁定器，其目标是最小、快速和轻量。。

## 安装

安装 the [slock](https://archlinux.org/packages/?name=slock)包 或者 [slock-git](https://aur.archlinux.org/packages/slock-git/)AUR 软件包。

## 配置

配置可通过编辑`config.h`头文件，然后重新编译软件包来完成。配置完成后，您应该[创建软件包](../../00_关于与社区/关于%20Arch/创建软件包.md)。

## 用法

只需运行 `slock` 即可锁定屏幕。您还可以提供一个参数，以便在屏幕锁定后运行：

```
$ slock cmd [arg ...]
```

要解锁屏幕，只需输入密码即可。

## 提示与窍门

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** 您可以参与翻译本节。（在 [Talk:Slock#](https://wiki.archlinuxcn.org/wiki/Talk:Slock) 中讨论）

### 挂起时锁定

创建以下服务，关闭显示器并锁定屏幕。

```
/etc/systemd/system/slock@.service
```

```
[Unit]
Description=Lock X session using slock for user %i
Before=sleep.target

[Service]
User=%i
Environment=DISPLAY=:0
ExecStartPre=/usr/bin/xset dpms force suspend
ExecStart=/usr/bin/slock

[Install]
WantedBy=sleep.target
```

启用`slock@*user*.service`systemd 单元，使其对用户名 *user* 生效。

### Block VT switching and prevent killing X

*slock* recommends blocking VT switching so that the screen lock cannot be bypassed. For the same reason, *slock* recommends preventing users from killing the X server. See [Xorg#Block TTY access](../X%20服务器/Xorg.md#Block_TTY_access) and [Xorg#Prevent a user from killing X](../X%20服务器/Xorg.md#Prevent_a_user_from_killing_X).

## 参见

- [官方主页](https://tools.suckless.org/slock/)

## 14.2　Dmenu

*难度：入门 ｜ 篇幅：3.0 KB ｜ 所属：Suckless*

将要讲到：安装、设置、延伸、其他资源

dmenu是一个X下的快速、轻量级的软件启动器，它从stdin读取任意文本，并创建一个菜单，每一行都有一个菜单项。 然后，用户可以通过方向键或键入名称的一部分来选择一个项目，该行就会被输出到stdout。 dmenu_run是 dmenu 发行版附带的包装器，可将其用作应用程序启动器。

## 安装

安装 [dmenu](https://archlinux.org/packages/?name=dmenu)包，或者是开发者版本的 [dmenu-git](https://aur.archlinux.org/packages/dmenu-git/)AUR。

通过以下命令来运行 *dmenu*：

```
$ dmenu_run
```

## 设置

现在你可以将此命令关联到一个快捷键，很多窗口管理器和桌面环境都有设置工具可以做到，用 xbindkeys 也可以做到，想得到更详细的信息参见 Hotkeys 条目。

### 显示自定义项目

自定义项目可以通过用换行符(*\\n*)来分隔并输入到 *dmenu* 来让它们显示出来，比如：

```
$ echo -e "第一个\n第二个\n第三个" | dmenu
```

### 手动添加项目

*dmenu* 会在你的 `$PATH` 路径下的目录里查找可执行文件并生成菜单项。如果要修改 `$PATH`，参见 [environment variables](../../02_系统管理与内核/系统管理/环境变量.md)。

### 字体

dmenu 4.6 起 XFT 字体渲染默认启用（[4.6 发行注记](https://lists.suckless.org/dev/1511/27503.html)）。[fontconfig](https://archlinux.org/packages/?name=fontconfig)包的[font.conf 语法](https://www.freedesktop.org/software/fontconfig/fontconfig-user.html)会被使用。

### shell 别名支持

*dmenu* 不支持 [shell 别名](../../07_开发与工具/命令行解释器/Bash.md#Aliases). 为了让 *dmenu* 能够识别别名，安装 *dmenu-recent-aliases*（已从AUR移除） 包然后运行 `dmenu_run_aliases`。 别名必须要在 `~/.bash_aliases` 或者 `~/.zsh_aliases` 文件里面来让 *dmenu_run_aliases* 识别出来。

## 延伸

```
$ pacman -Ql dmenu | grep bin
dmenu /usr/bin/dmenu
dmenu /usr/bin/dmenu_path
dmenu /usr/bin/dmenu_run
```

可见/usr/bin/下有三个文件，其中**dmenu_path**和**dmenu_run**是两个shell脚本，真正的执行/显示部分都由**dmenu**完成，其中**dmenu_path**用于列出$PATH里的可执行文件，每个文件名一行，然后通过“|”传递给**dmenu**，具体语法可从**dmenu_run**里找到。 你也可以在终端里执行：

```
$ echo | dmenu
```

然后输入任意字串，这个字串就会被显示在终端里，这其实才是dmenu的核心功能。

由此配合其他工具可以完成其他任务，比如运行：

```
$ notify-send "`exec $(echo | dmenu)`"
```

你可以试着在运行后输入date，之后系统就会借助notify-send弹出日期提示。

## 其他资源

- [dmenu](https://tools.suckless.org/dmenu) - dmenu官方网站
- [Yeganesh](http://dmwit.com/yeganesh) - dmenu的一个前段处理器，可以按照使用频率进行排序
- [LinuxTOY](https://linuxtoy.org/archives/dmenu.html) - dmenu运行后的效果图

## 14.3　St

*难度：进阶 ｜ 篇幅：7.6 KB ｜ 所属：Suckless*

将要讲到：安装、配置、故障排除、请见

[st](https://st.suckless.org/) 是一个简单的由 [suckless](https://suckless.org/) 制作的用于 [Xorg](../X%20服务器/Xorg.md) 上的终端实现。它被用作 xterm 或 [urxvt](../../07_开发与工具/终端模拟器/Rxvt-unicode.md) 的轻量替代。它当前支持 256 颜色,真颜色,大多数 VT10X 转义序列,UTF-8,X11 复制/粘贴,抗锯齿字体,备用字体,调整大小,快捷键和线描。

## 安装

安装 [st](https://aur.archlinux.org/packages/st/)AUR 软件包 或 开发版本的 [st-git](https://aur.archlinux.org/packages/st-git/)AUR 软件包。

- 在 [Wayland](../Wayland/Wayland.md) 上, *st* 使用 Xwayland;如果你想要一个类似的终端来避免 Xwayland 的内存占用,请考虑使用 [wterm-git](https://aur.archlinux.org/packages/wterm-git/)AUR。

## 配置

*st* 是通过它在编译时被复制的 `config.h` 文件来配置的。默认配置保存在源代码中的 `config.def.h`。考虑制作你自己的 `config.h` 和 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md)。

### Shell

要想修改 *st* 的默认 shell ,编辑这一行:

```
static char *shell = "/bin/sh";
```

或将你想要的 shell 作为最后一个参数启动 *st* :

```
$ st options fish
```

### 终端

要想修改终端的类型, 编辑这一行:

```
static char *termname = "st-256color";
```

*st* 会将 `TERM` 变量的值设置为 `termname`。

### 字体

根据你的喜好编辑下面这一行:

```
static char *font = "Liberation Mono:pixelsize=12:antialias=false:autohint=false";
```

你也可以在命令行中传递使用的字体:

```
$ st -f "Liberation Mono:size=12"
$ st -f 'Liberation Mono-12'
```

字体的名字可以用 `fc-list` 命令显示。

### 光标

默认的光标指针为难以找到的 `XC_xterm`。要想改为你的光标主题的普通光标,编辑下面这一行:

```
static unsigned int mouseshape = XC_left_ptr;
```

### 颜色

编辑下列行来设置前景,背景和光标颜色:

```
unsigned int defaultfg = 7;
unsigned int defaultbg = 0;
static unsigned int defaultcs = 256;
```

这些值引用配置文件中的 `*colorname[]` 数组。你可以使用默认的颜色或在 `#rrggbb` 中添加你自己的颜色:

```
static const char *colorname[] = {
   	/* 8 normal colors */
       "black",
       "red3",
       "green3",
       "yellow3",
       "blue2",
       "magenta3",
       "cyan3",
       "gray90",

       /* 8 bright colors */
       "gray50",
       "red",
       "green",
       "yellow",
       "#5c5cff",
       "magenta",
       "cyan",
       "white",

        = 0,

       /* more colors can be added after 255 to use with DefaultXX */
       "#cccccc",
       "#eeeeee",
       "#111111",
 };

/*
 * Default colors (colorname index)
 * foreground, background, cursor
 */
unsigned int defaultfg = 257;
unsigned int defaultbg = 258;
static unsigned int defaultcs = 256;
```

有便于创建调色板的工具。举个例子 [terminal.sexy](https://terminal.sexy) 有一套预制的颜色并且可以直接导出为 *st* 的格式(请见 [issue 22 的评论](https://github.com/stayradiated/terminal.sexy/issues/22#issuecomment-430629424))。

有 Solarized 颜色主题的补丁。请见 [https://st.suckless.org/patches/solarized/](https://st.suckless.org/patches/solarized/) 来安装。

### 补丁

[suckless 网站](https://st.suckless.org/patches/alpha/)有许多补丁。要想使用补丁,下载 [diff](https://www.gnu.org/software/diffutils/manual/html_node/Invoking-diff.html) 并且使用 `patch -i patch.diff` 命令来安装补丁。这会修改默认的配置文件 `config.def.h`;如果你正在制作你自己的 `config.h`,将你的配置从 `config.h` 复制到一份 `config.def.h` 的副本中并且将它重命名为 `config.h`,然后执行 `make clean install` 命令。

### Desktop 条目

要想用在[桌面环境](../桌面环境/桌面环境.md)优美的字体(例如 [adobe-source-code-pro-fonts](https://archlinux.org/packages/?name=adobe-source-code-pro-fonts)包)简化 *st*的启动,你可以创建一个 [Desktop 条目](../../01_安装与包管理/配置文件/桌面项.md):

```
~/.local/share/applications/st.desktop
```

```
[Desktop Entry]
Name=Simple Terminal
GenericName=Terminal
Comment=Suckless terminal emulator for X
Exec=st -t "Simple Terminal" -f "Source Code Pro:style=Semibold:size=12"
Terminal=false
Type=Application
Encoding=UTF-8
Icon=utilities-terminal
Categories=System;TerminalEmulator;
Keywords=shell;prompt;command;commandline;cmd;
```

菜单条目会在*系统工具*应用列表中以*Simple Terminal*出现。

## 故障排除

### 键盘

将下列内容添加到 `~/.inputrc` 或 `/etc/inputrc` 如果 {{ic}Delete}} 不能在某些应用里正常工作。

```
set enable-keypad on
```

如果以上操作后还不能在某些使用 bash 的应用,例如 IPython ,中正常工作,相反,关闭 `enable-keypad` 并且将下列内容加入到你的 `~/.bashrc`,就如在[st FAQ](https://git.suckless.org/st/file/FAQ.html#l55)提到的做:

```
printf '\033[?1h\033=' >/dev/tty
```

### Vim

#### *vim* 中非字母的文字的背景颜色不会被填充

试着在你的 `config.h` 设置 `termname` 的值为 `st-256color` 然后重新编译。并且不要在你的 shell 中设置 `TERM` 变量,至少不要设置成 `st-256color` ,因为似乎会造成问题。

另一个也许更好的解决方法,是在 `.vimrc` 文件中加入下列内容:

```
if &term =~ '256color'
    " disable Background Color Erase (BCE) so that color schemes
    " render properly when inside 256-color tmux and GNU screen.
    " see also https://sunaku.github.io/vim-256color-bce.html
    set t_ut=
endif
```

#### 256颜色和真颜色在 tmux 或其他终端复用器中不工作

首先,确保你在你的 `~/.bashrc` 没有设置且没有导出 `TERM` 的值,正如在这篇[帖子](https://bbs.archlinux.org/viewtopic.php?pid=1755862#p1755862)中提到的。

> **注意：**请不要显式设置 `TERM` 变量。通过在你的 `tmux.conf` 设置 `default-terminal` 来正确地设置。

接着,确保你使用的 `vim` 版本为 **`>=7.4.1799`**, 该版本加入了 `termguicolors`。

最后,在 `~/.vimrc` 中添加下列内容:

```
set t_8f=^[[38;2;%lu;%lu;%lum        " set foreground color
set t_8b=^[[48;2;%lu;%lu;%lum        " set background color
colorscheme Tomorrow-Night-Eighties
set t_Co=256                         " Enable 256 colors
set termguicolors                    " Enable GUI colors for the terminal to get truecolor
```

> **注意：**`^[` 是 (`Esc`) 的转义字符,它为每个 `t_8f` 和 `t_8b` 的值添加前缀。它是一个单独的字符,可以在 `vim` 中打出来。在 **INSERT** 模式中,按下 `Ctrl+v` 然后按 `Esc`。你还会保持在 **INSERT** 模式;再次按下 `Esc` 来回到 **NORMAL** 模式。
>
> > **提示：**建议在设置 `colorscheme`, `t_Co` 和 `termguicolors` 之前设置 `t_8f` 和 `t_8b`。
>
> 要想获得更多信息,请见 `vim` 中的 `:help`: `xterm-true-color`, `t_8f`, `t_8b`。

## 请见

- [主页](https://st.suckless.org/)
- [常见问题](https://git.suckless.org/st/plain/FAQ)
- [官方 git 仓库](https://git.suckless.org/st/)
