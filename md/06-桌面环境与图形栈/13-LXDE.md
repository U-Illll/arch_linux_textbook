# 第 13 章　LXDE

> 本章把轻量桌面栈从登录界面推到日常可用：LXDM 管登录与 greeter、GTK 两种主题，LXDE 管自身的安装与启动，PCManFM 除文件管理外还管桌面。按 LXDM、LXDE、PCManFM 的顺序读：《LXDE》与《PCManFM》必读，前者是地基，后者不读容易把桌面当成可有可无；LXDM 只在需要图形登录界面时精读。
> 
> 最常见的坑是关掉 PCManFM：桌面由它提供，进程一退就没了，得用守护进程模式配开机自启让它常驻。另一误解是 greeter 与 GTK 主题共用一套配置，其实改错位置就不会生效。

<!-- 本章 3 节，共 33 KB -->

## 13.1　LXDM

*难度：进阶 ｜ 篇幅：8.2 KB ｜ 所属：LXDE*

将要讲到：安装、配置、提示和技巧、the theme of greeter、GTK theme、问题处理

LXDM 是轻量级的 [LXDE](LXDE.md) [桌面环境](../桌面环境/桌面环境.md)使用的[显示管理器](../图形用户界面/显示管理器.md)。

LXDM 不支持 XDMCP 协议，要使用 XDMCP，请使用 [LightDM](../../06_软件与应用/Canonical/LightDM.md).

## 安装

[安装](https://wiki.archlinuxcn.org/wzh/Help:%E9%98%85%E8%AF%BB#官方软件包) [GTK+](../../07_开发与工具/控件工具集/GTK.md) 2版本的[lxdm](https://archlinux.org/packages/?name=lxdm)包 软件包 或 [GTK+](../../07_开发与工具/控件工具集/GTK.md) 3版本的[lxdm-gtk3](https://archlinux.org/packages/?name=lxdm-gtk3)包软件包 。

启用 [systemd](../../01_安装与包管理/初始化/Systemd.md) 服务 `lxdm.service`。

## 配置

> **警告：****lxdm.conf** 中必须包含语言选择控制，请设置 **lang=1** 否则 LXDM 会不停循环启动，无法载入会话。

LXDM 的配置文件都位于 `/etc/lxdm`。主配置文件是 `lxdm.conf`，注释非常详细。`Xsession` 是系统 X 会话配置文件，一般不需要修改。目录中的其他文件都是 shell脚本，在 LXDM 发生相应事件时运行：

1.  `LoginReady`: 在 LXDM 准备显示登录窗口时以 root 权限运行。
2.  `PreLogin`: 用户登录前以 root 权限运行。
3.  `PostLogin`: 用户登录后以登录的用户运行。
4.  `PostLogout`: 用户注销后以用户权限运行。
5.  `PreReboot`: 通过 LXDM 重启时以 root 运行。
6.  `PreShutdown`: 通过 LXDM关机时以 root 运行。

### 默认会话

默认会话可以全局配置，也可以在用户级别配置。用户设置优先于全局设置。

#### 全局设置

要修改 LXDM 的默认会话或桌面环境，请编辑 `/etc/lxdm/lxdm.conf` 把会话行改为如下：

```
session=/usr/bin/startlxde
```

例如 [Xfce](../Xfce/Xfce.md):

```
session=/usr/bin/startxfce4
```

例如 [Openbox](../堆叠式窗口管理器/Openbox.md):

```
session=/usr/bin/openbox-session
```

例如 [GNOME](../GNOME/GNOME.md):

```
session=/usr/bin/gnome-session
```

例如 [LXQt](../桌面环境/LXQt.md):

```
session=/usr/bin/xfwm4
```

在使用的主题无会话选择框或者自动登录有问题时，这个设置很有用。

#### 各个用户设置

要定义独立用户的会话，请编辑 `~/.dmrc` 并定义会话。

例如：用户1要用 xfce4，用户2要用cinnamon,用户3要用GNOME:

用户1的设置：

```
[Desktop]
Session=xfce
```

用户2的设置：

```
[Desktop]
Session=cinnamon
```

用户3的设置：

```
[Desktop]
Session=gnome
```

可用如下命令显示已安装的会话列表

```
$ ls /usr/share/xsessions/
```

### 自动登录

如果要免密自动登录一个用户，找到 `/etc/lxdm/lxdm.conf` 中的:

```
#autologin=dgod
```

取消前面的注释，并将dgod改为想要免密登录用户名。

### 上次的登录选项

以前使用的 LXDM 选项可以在以下位置找到：

```
/var/lib/lxdm/lxdm.conf
```

```
[base]
last_session=/usr/share/xsessions/LXDE.desktop
last_lang=sv_SE.UTF-8
last_langs=sv_SE.UTF-8 fa_IR.UTF-8 en_US.UTF-8
```

> **注意：**卸载 LXDM 时，此文件不会自动删除。如果要删除 LXDM 选项的所有痕迹，则必须手动删除它。

## 提示和技巧

### 添加表情图标

一个96x96像素的图像（jpg或png格式）可以选择性地在每个用户上替代默认图标进行显示。只需将目标图像复制或使用符号链接到 `$HOME/.face`。[gnome-control-center](https://archlinux.org/packages/?name=gnome-control-center)包 包提供了一些适用于lxdm屏幕的默认图标。 在安装该软件包后，在 `/usr/share/pixmaps/faces` 目录下查找。

> **注意：**
>
> - 用户不需要保留 [gnome-control-center](https://archlinux.org/packages/?name=gnome-control-center)包 包来使用这些图像。只需安装它，将它们复制到其他地方，然后卸载它即可。
>
> - 用户的目录应该对其他人具有r-x权限，而.face文件对其他人应该有r--权限。但显然，这会带来安全和访问方面的影响，因为现在任何人都可以浏览你的主目录。
>
> - 一个名为 `lxdm-config` 的图形化工具随lxdm一起提供，可以用于在主目录中放置 `.face` 文件，以及其他配置文件。

### 同时登录和切换用户

LXDM 可以让多个用户同时登录到不同 ttys，下面的命令可以登录另一个用户，而不需要注销当前用户会话：

```
$ lxdm -c USER_SWITCH
```

> **注意：**用USER_SWITCH登录新用户时，使用的是tty7的下一个tty。使用USER_SWITCH 命令登录用户1后，用户2正常登录，用户2会在tty7,，用户1将会位于 tty1。

### 主题

LXDM 主题位于 `/usr/share/lxdm/themes`.

LXDM 仅提供了一个主题 Industrial. 要显示主题背景文件 `wave.svg`，请安装软件包 [librsvg](https://archlinux.org/packages/?name=librsvg)包.

[lxdm-themes](https://aur.archlinux.org/packages/lxdm-themes/)AUR 提供了 6 个额外的主题：Archlinux, ArchlinuxFull, ArchlinuxTop, Arch-Dark, Arch-Stripes 和 IndustrialArch. [lxdm-git](https://aur.archlinux.org/packages/lxdm-git/)AUR 也提供了 ArchStripes 和 ArchDark(名字改了一下以避免冲突).

主题文件通过 `/etc/lxdm/lxdm.conf` 配置:

```
## the theme of greeter
theme=theme_name
```

要让 LXDM 使用 GTK 主题(位于 `/usr/share/themes`)，在配置文件中设置：

```
## GTK theme
gtk_theme=gtk_theme_name
```

### 高级会话配置

用户登录后，LXDM 会按下面顺序引用全部文件：

1.  `/etc/profile`
2.  `~/.profile`
3.  `/etc/xprofile`
4.  `~/.xprofile`

这些文件可以设置会话的环境变量，启动必须的服务例如 ssh-agent。详情请参考 [Xprofile](../X%20服务器/Xprofile.md)。

LXDM **不会**引用 `~/.xinitrc`，所以如果需要从使用这些文件的显示管理器迁移到 LXDM，需要将设置移动到其它文件，例如 `~/.xprofile`. LXDM 也不会引用 `~/.bash_profile`。

如果还想使用 `~/.xinitrc`，可以在 `/etc/lxdm/PostLogin` 中加入：

```
source ~/.xinitrc
```

LXDM 也会使用 .[Xresource](../../01_安装与包管理/配置文件/X%20资源.md), .Xkbmap, 和 .[Xmodmap](../../04_硬件与外设/输入设备/Xmodmap.md)。LXDM 系统配置和用户配置的详细状况可以参考 `/etc/lxdm/Xsession`。

## 问题处理

### 白闪

当使用默认的LXDM主题 `theme=Industrial` 和一个暗色背景图片（例如 `bg=/usr/share/backgrounds/img.png`）时，LXDM启动之前可能会出现短暂的明亮闪光。 这是由于所选 [GTK+](../../07_开发与工具/控件工具集/GTK.md) 主题的 `bg_color:` 属性引起的。 为了避免这种情况，请将 `gtk_theme=Adwaita` 更改为 `gtk_theme=Adwaita-dark` 或其他暗色主题。

### 注销问题

如果您在使用 lxdm 时无法注销（例如卡住、显示冻结等），请尝试取消 `/etc/lxdm/lxdm.conf` 中的 reset=1 选项的注释，以便在每次注销时刷新 xserver

## 13.2　LXDE

*难度：进阶 ｜ 篇幅：10.7 KB ｜ 所属：LXDE*

将要讲到：安装、运行 LXDE、小提示、故障排除、相关资源

摘自 [LXDE.org | LXDE官方主页](https://lxde.org/):

: *"Lightweight X11 Desktop Environment" 是一种极快且节能的桌面环境。由一个国际开发者社区维护，它拥有漂亮的界面、多语言支持、标准键盘快捷键，以及诸如选项卡式文件浏览等额外功能。LXDE比其他环境使用更少的CPU和RAM。它专为具有低硬件规格的云计算机而设计，例如网书、移动设备（如MIDs）或老旧计算机。*

## 安装

使用LXDE最少需要安装 [lxde-common](https://archlinux.org/packages/?name=lxde-common)包, [lxsession](https://archlinux.org/packages/?name=lxsession)包, [openbox](https://archlinux.org/packages/?name=openbox)包(或者其他窗口管理器）。[lxde](https://archlinux.org/groups/x86_64/lxde/)包组包含完整的桌面。

### GTK+ 3 版本

你可以安装 [lxde-gtk3](https://archlinux.org/groups/x86_64/lxde-gtk3/)包组 软件包组来试用这个尚在实验阶段的版本.

它几乎可以正常工作，但是可能会有一些问题，包括： [gpicview](https://sourceforge.net/p/lxde/bugs/769/), [lxappearance-obconf](https://sourceforge.net/p/lxde/bugs/768/), [lxlauncher](https://sourceforge.net/p/lxde/bugs/803/) 和 [lxpanel](https://sourceforge.net/p/lxde/bugs/773/).

## 运行 LXDE

### 显示管理器

[LXDM](LXDM.md)是LXDE的默认显示管理器，并作为[lxde](https://archlinux.org/groups/x86_64/lxde/)包组组的一部分。另请参见[Display manager](../图形用户界面/显示管理器.md)。

### 命令行

使用 "startx"，你需要在你的[xinitrc](../Xorg%20命令/Xinit.md)文件最后添加

```
~/.xinitrc
```

```
exec startlxde
```

另见Start X at login

## 小提示

### 应用程序配置项

应用程序菜单是通过解析放在`/usr/share/applications/`和`~/.local/share/applications/`中的`.desktop`文件实现的。要添加或编辑菜单中的项，请见[desktop entries](../../01_安装与包管理/配置文件/桌面项.md)。可在[AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md)中找到第三方的菜单编辑器（如[lxmed](https://aur.archlinux.org/packages/lxmed/)AUR）。同时也有官方的菜单编辑器，如[alacarte](https://archlinux.org/packages/?name=alacarte)包（gnome）和[mozo](https://archlinux.org/packages/?name=mozo)包（mate）等。

### 程序自动开启

应用程序可以用以下方式设置自动打开：

- 使用`.desktop`文件

LXDE 工具 [XDG Autostart](../Freedesktop.org/XDG%20Autostart.md)

- 使用LXsession

`~/.config/lxsession/LXDE/autostart`中的每一行表示要执行的命令。如果一行以`@`开头，并且其后的命令崩溃，则该命令将自动重新执行。例如：

```
~/.config/lxsession/LXDE/autostart
```

```
@lxterminal
@leafpad
```

> **注意：**这些命令*不*以"&"结尾。

也有一个全局自启动文件，位于`/etc/xdg/lxsession/LXDE/autostart`。

> **注意：**如果两个文件都存在，LXsession只执行v0.4.9之后的本地文件

### 鼠标键盘动作

鼠标和键绑定（即键盘快捷键）是使用Openbox实现的。LXDE用户应该参考 [Openbox 维基](http://openbox.org/wiki/Help:Bindings)来编辑 `~/.config/openbox/lxde-rc.xml`.

[obkey](https://aur.archlinux.org/packages/obkey/)AUR 包提供了一个可选的图形用户界面，用于编辑键位绑定。默认情况下，它编辑 `rc.xml`，但您可以按照以下方式将其定向到LXDE配置文件中：

```
$ obkey ~/.config/openbox/lxde-rc.xml
```

请参见  来获取更多信息

### 光标

[lxappearance](https://archlinux.org/packages/?name=lxappearance)包 包是一个图形化工具，用于设置 [GTK](../../07_开发与工具/控件工具集/GTK.md) 的外观和感觉，包括光标主题。使用LXAppearance配置的设置将被写入到 `~/.gtkrc-2.0`, `~/.config/gtk-3.0/settings.ini` 和 `~/.icons/default/index.theme` 文件中。请参见 [Cursor themes](../X%20服务器/光标主题.md)。

### 任务栏时间

您可以右键单击面板上的数字时钟小插件，并使用strftime格式设置它显示当前时间的方式。请参见 [strftime(3)](https://man.archlinux.org/man/strftime.3)。

### 字体设置

[lxappearance-obconf](https://archlinux.org/packages/?name=lxappearance-obconf)包 用于配置 [Openbox](../堆叠式窗口管理器/Openbox.md) 设置。此外，还可以参考 [Font configuration](../字体/字体配置.md)。

### 键盘布局

[lxpanel](https://archlinux.org/packages/?name=lxpanel)包 包括一个键盘布局小程序。 请参阅 Keyboard configuration in Xorg 以获得通用指令，以及 [#程序自动开启](#程序自动开启)以在LXDE中自动启动 *setxkbmap*。

### 锁屏

LXDE本身不提供屏幕锁的功能。请参见 [List of applications/Security#Screen lockers](../../06_软件与应用/应用程序/应用程序列表_安全.md#Screen_lockers) 和 [#程序自动开启](#程序自动开启)相关说明来启动它们。

*Screen Lock* 图标执行的脚本（位于 `/usr/bin/lxlock`）会搜索一些众所周知的屏幕锁定程序，并使用找到的第一个程序锁定屏幕。请参见 GitHub 上的 [lxlock](https://github.com/lxde/lxsession/blob/master/lxlock/lxlock)。

`/etc/xdg/lxsession/LXDE/autostart` 文件（位于 [lxde-common](https://archlinux.org/packages/?name=lxde-common)包 包中）列出了 [XScreenSaver](../X%20服务器/XScreenSaver.md)，它将自动启动。

请参阅 [DPMS](../../02_系统管理与内核/电源管理/Display%20Power%20Management%20Signaling.md)，了解如何在没有外部程序的情况下控制屏幕保护。

### LXPanel中的图标

LXpanel 使用的默认图标存储在 `/usr/share/pixmaps/` 中，任何自定义图标也应该保存在这里。

要更改应用程序的默认图标，请参考 [Desktop entries#Icons](../../01_安装与包管理/配置文件/桌面项.md#Icons)。

### LXPanel中的菜单

面板的菜单可以在 `/etc/xdg/menus/lxde-applications.menu` 中进行配置，遵循 [xdg-menu](../Freedesktop.org/Xdg-menu.md) 格式，以便与其他会话（特别是 [MATE](../桌面环境/MATE.md)）中的应用程序配合使用，以增加 LXDE 缺乏的某些功能。

### 使用不同的窗口管理器

LXsession 使用 `~/.config/lxsession/LXDE/desktop.conf` 文件中定义的 [window manager](../窗口管理器/窗口管理器.md) （默认为 [Openbox](../堆叠式窗口管理器/Openbox.md)）。 如果该文件不存在，则会在 `/etc/xdg/lxsession/LXDE/desktop.conf` 中进行搜索。

将文件中的 `openbox-lxde` 替换为您选择的窗口管理器：

对于 metacity：

```
window_manager=metacity
```

对于 compiz：

```
window_manager=compiz
```

对于 [EXWM](../../07_开发与工具/Emacs/EXWM.md#嵌入_LXDE)：

```
window_manager=emacs
```

或者按照 [#程序自动开启](#程序自动开启)部分中定义的方法使用 `*WM* --replace`，其中 *WM* 是要启动的窗口管理器可执行文件的名称。 这意味着每次登录时都会首先启动 *openbox* ，然后立即被替换。 请注意，Openbox和LXDE不共享相同的 `rc.xml` 此键盘快捷键可能会有所不同。请参见 xbindkeys。

### 使用多个窗口管理器

LXDE默认情况下不启用混合，这会导致屏幕撕裂问题。如果您对此不满意，您可以牺牲更多的资源来启用窗口混合，要么安装混合管理器，要么将lxde替换为其他支持混合的窗口管理器。

## 故障排除

### 一些程序不能运行

最近LXDE转向gtk3引入了一些错误，可能导致分段错误并阻止应用程序启动（尤其是自定义外观设置，也就是lxappearance）。在此问题完全修复之前，您可以尝试使用JWM来绕过gtk3的分段错误：

- 安装 JWM

```
$ sudo pacman -S jwm
```

- 将LXDE替换为jwm, 打开终端并运行：

```
$ jwm
```

- 这可能会导致一些小问题，但现在您应该能够正常打开segfault应用程序了
- 完成后，请重新登录以刷新LXDE桌面

### 带有中文字符的NTFS

对于具有NTFS文件系统的存储设备，您需要安装 [NTFS-3G](../../02_系统管理与内核/FUSE/NTFS-3G.md) 软件包。通常情况下，PCManFM与NTFS文件系统兼容良好，但是，对于使用NTFS的用户来说，存在一个问题，即如果您在NTFS文件系统上有包含非拉丁字符 （例如中文字符）的文件或目录， 当打开（或自动挂载）NTFS卷时，这些文件或目录的名称可能会消失。这是因为 lxsession mount-helper 未能正确解析策略和区域设置选项。对此有一个解决方法：

创建一个新的 Bash 脚本，将其命名为 `/usr/local/bin/mount.ntfs-3g` 并包含以下内容：

```
#!/bin/bash
/usr/bin/ntfs-3g $1 $2 -o locale=en_US.UTF-8
```

然后将其设置为可执行：

```
# chmod +x /usr/local/bin/mount.ntfs-3g
```

### LXPanel崩溃问题解决方法

使用某些 [GTK](../../07_开发与工具/控件工具集/GTK.md) 主题时，启动 *lxpanel* 可能会导致以下错误：

```
lxpanel: cairo-scaled-font.c:459: _cairo_scaled_glyph_page_destroy: Assertion `!scaled_font->cache_frozen' failed.
```

在这种情况下，安装 [ttf-dejavu](https://archlinux.org/packages/?name=ttf-dejavu)包。

如果在浏览特定的Unicode网页时，lxpanel崩溃，请安装 [ttf-droid](https://archlinux.org/packages/?name=ttf-droid)包。

### LXPanel任务栏图标大小设置

正在运行的应用程序的图标与 *Panel Settings* > *Geometry* 设置的 *Icon size* 不匹配，而是小4px， 这会导致其中一些图标模糊。为了在任务栏中显示清晰的32px图标，需要将 *Icon size* 设置为36px，但这样做会使其他活动面板小工具的图标变模糊。为了解决这个问题，可以创建额外的面板，并通过在 *Panel Settings* > *Geometry* 中调整对齐和边距，使它们共同形成一个连续的面板。

### 在LXTerminal中实现虚拟透明效果

最新版本的 [VTE terminal widget library](https://wiki.gnome.org/Apps/Terminal/VTE) 要求使用一个合成窗口管理器才能实现背景透明效果。而未维护的、遗留的 GTK 2 版本的 VTE 则采用伪透明技术，终端将显示桌面背景图像。如果你更喜欢伪透明效果，可以使用 [lxterminal-gtk2](https://aur.archlinux.org/packages/lxterminal-gtk2/)AUR 软件包安装 GTK 2 版本的 LXTerminal。

### 光标太小

高分辨率屏显示光标过小，可以在[Gnome-look](https://www.gnome-look.org/)里找有36px或48px的光标主题文件并下载，之后在自定义外观里的光标下安装并选择使用。

## 相关资源

- [LXDE项目](http://lxde.sourceforge.net)
- [最新组件](https://sourceforge.net/project/showfiles.php?group_id=180858)
- [PCMan文件管理器](https://sourceforge.net/project/showfiles.php?group_id=156956)

## 13.3　PCManFM

*难度：进阶 ｜ 篇幅：14.5 KB ｜ 所属：LXDE*

将要讲到：安装、桌面管理、守护进程模式、开机自启、其他特性和功能、提示与技巧

[PCManFM](https://en.wikipedia.org/wiki/PCManFM) 是一个非常快速和轻量级的文件管理器，也是 [LXDE](LXDE.md) 的标准文件管理器。它使用 [GTK](../../07_开发与工具/控件工具集/GTK.md) 作为其 UI，并使用 [GVFS](../../06_软件与应用/文件管理器/文件管理器功能.md) （在 GNOME 的 [GIO](https://en.wikipedia.org/wiki/GIO_(Software)) 库）提供虚拟文件系统功能，例如文件回收站功能和挂载远程文件系统的功能。

[PCManFM-Qt](https://github.com/lxqt/pcmanfm-qt)是使用[Qt](../../07_开发与工具/控件工具集/Qt.md)的版本，是\[LXQt\]\] 的标准文件管理器。尽管使用 Qt作为UI工具包，但PCManFM-Qt保留了[GVFS](../../06_软件与应用/文件管理器/文件管理器功能.md)，而非使用KDE的 [KIO](https://en.wikipedia.org/wiki/KIO) 。从本质上讲，这两个文件管理器都与桌面环境无关。

## 安装

安装下列软件包之一：

- [GTK 2](../../07_开发与工具/控件工具集/GTK.md): [pcmanfm](https://archlinux.org/packages/?name=pcmanfm)包
- [GTK 3](../../07_开发与工具/控件工具集/GTK.md): [pcmanfm-gtk3](https://archlinux.org/packages/?name=pcmanfm-gtk3)包
- [Qt6](../../07_开发与工具/控件工具集/Qt.md): [pcmanfm-qt](https://archlinux.org/packages/?name=pcmanfm-qt)包

可选组件：

- [gvfs](https://archlinux.org/packages/?name=gvfs)包：提供回收站功能
- [udisks](../../04_硬件与外设/硬件检测和故障排除/Udisks.md)：远程文件系统的挂载支持
- [gvfs-smb](https://archlinux.org/packages/?name=gvfs-smb)包 支持SMB/CIFS

### 开发版本

[AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md)中提供开发版本:

- GTK 2: [pcmanfm-git](https://aur.archlinux.org/packages/pcmanfm-git/)AUR
- Qt6: [pcmanfm-qt-git](https://aur.archlinux.org/packages/pcmanfm-qt-git/)AUR

## 桌面管理

如果要用PCManFM进行桌面管理，比如设置壁纸和显示桌面图标，使用这个命令：

```
$ pcmanfm --desktop
```

窗口管理器提供的桌面菜单会被PCManFM提供的桌面菜单所替换。如果要还原，在桌面首选项的高级选项卡中勾选`点击桌面时显示窗口管理器提供的菜单`选项。

### 桌面首选项

如果使用窗口管理器提供的桌面菜单，请输入以下命令以随时设置或修改桌面首选项：

```
$ pcmanfm --desktop-pref
```

可以考虑给这条命令绑定快捷键或添加到原生桌面菜单中以方便使用。

### 新建图标

文件可以直接拖放到桌面上。至于应用程序快捷方式，需要把它们的`.desktop`文件**复制**到`~/Desktop`文件夹；不能拖放`.desktop`文件，否则就会是移动而不是复制，这会导致这个应用从应用启动器中消失。如果用命令行就应该是这样：

```
$ cp /usr/share/applications/name-of-application.desktop ~/Desktop
```

例如，下面的命令为 [lxterminal](https://archlinux.org/packages/?name=lxterminal)包 创建了一个桌面快捷方式：

```
$ cp /usr/share/applications/lxterminal.desktop ~/Desktop
```

使用 [XDG user directories](../Freedesktop.org/XDG%20用户目录.md) 程序能创建自己的目录，无需再配置`$HOME` 环境变量。

## 守护进程模式

如果你想在后台运行PCManFM ( 比如说要自动挂载移动硬盘等可移动介质)，使用：

```
$ pcmanfm --daemon-mode
```

一次只能有一个 PCManFM 实例作为守护进程运行。

如果自动挂载失败，请参见 [udisks](../../04_硬件与外设/硬件检测和故障排除/Udisks.md).

## 开机自启

PCManFM 可以被[自启](../../02_系统管理与内核/系统管理/自动启动.md#On_Xorg_startup)为一个[守护进程](../../01_安装与包管理/初始化/Systemd.md)或用于管理桌面。

## 其他特性和功能

经验不足的用户应该意识到，单独的文件管理器 ，尤其是当安装一个独立窗口管理器（如 [Openbox](../堆叠式窗口管理器/Openbox.md)）将无法提供完整桌面环境（如 [Xfce](../Xfce/Xfce.md)和[KDE](../KDE/KDE.md) ）的用户所习惯的特性和功能。有关详细信息，请查看[文件管理器功能](../../06_软件与应用/文件管理器/文件管理器功能.md)一文。

## 提示与技巧

### 获取其他文件类型缩略图

查看[文件管理功能# PCManFM获取其他文件类型缩略图](../../06_软件与应用/文件管理器/文件管理器功能.md#Use_PCManFM_to_get_thumbnails_for_other_file_types).

### 设置终端模拟器

在 *Edit > Preferences > Advanced* 里面的 *Tools > Open Current Folder in Terminal*，你可以配置 PCManFM 调用的终端模拟器。

### 集成压缩包管理器

可以在 *Edit > Preferences > Advanced* 中设置集成的压缩包管理器。目前 PCManFM和PCManFM-Qt都支持 [file-roller](https://archlinux.org/packages/?name=file-roller)包, [xarchiver](https://archlinux.org/packages/?name=xarchiver)包 (或者 [xarchiver-gtk2](https://archlinux.org/packages/?name=xarchiver-gtk2)包), [engrampa](https://archlinux.org/packages/?name=engrampa)包, [ark](https://archlinux.org/packages/?name=ark)包  . PCManFM-Qt 还支持 [lxqt-archiver](https://archlinux.org/packages/?name=lxqt-archiver)包，这是 LXQt 的默认选择。

### 将自定义项目添加到上下文菜单

PCManFM 支持[桌面文件规范扩展](https://web.archive.org/web/20180627170128/http://www.nautilus-actions.org/?q=node/377) (DES-EMA)，它允许您将任意项目添加到文件和目录的上下文菜单中。要添加你自己的项目，请创建（如果不存在）`~/.local/share/file-manager/actions/`文件夹并向其中添加`.desktop` 文件：

```
~/.local/share/file-manager/actions/action.desktop
```

```
[Desktop Entry]
Type=Action
Profiles=profile_id
Name=Action name in English
Name[cc]=Action name in Language
Icon=Icon name
# Example: Icon=text-editor

[X-Action-Profile profile_id]
MimeTypes=MIME-types (semicolon separated)
Exec=command and arguments
# Example: Exec=gedit %f
```

你可以通过列出其id（以分号分隔），将一个或多个配置文件绑定到单个操作。配置文件允许你指定要为哪些文件类型执行哪些命令，因此同一操作可以根据所选文件类型运行不同的命令。除了特定的 MIME 类型（例如：`text/plain`文本文件），你可以使用以下常规类型：

- `all/allfiles` --文件；
- `inode/directory` -- 文件夹
- `all/all` -- 文件和文件夹。

> **注意：**可以使用 pkexec 以 root 身份运行命令，但直接调用它不起作用，您需要创建一个中间脚本，如 [LXDE 的 wiki](https://web.archive.org/web/20220428194115/https://wiki.lxde.org/en/PCManFM#pkexec_method)中所述。

### “创建新的...”模板

模板文件保存在 `~/Templates` ，点击*文件>新建...*可以选择相应的模板。默认的模板是“创建文件夹”和“创建空白文件”。

### 缩略图

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 使用现代 SSD 时，PCManFM 可以毫无问题地呈现包含数千个媒体文件的目录的缩略图。（在 [Talk:PCManFM](https://wiki.archlinuxcn.org/wiki/Talk:PCManFM) 中讨论）

和其他一些文件管理器（例如 Nautilus）一样，PCManFM 将加载文件夹中所有图像的预览。为了不滥用 HDD，请将文件夹中的图像数量保持在 100 个之内。

## 故障排除

### “打开方式”对话框窗口为空

如果您在打开方式对话框中没有看到任何可供选择的应用程序，那么你可以试着卸载 [gnome-menus](https://archlinux.org/packages/?name=gnome-menus)包 然后安装 [lxmenu-data](https://archlinux.org/packages/?name=lxmenu-data)包。此外，设置如下环境变量：

- `XDG_MENU_PREFIX=lxde-`
- `XDG_CURRENT_DESKTOP=LXDE`

### 列表中没有 "应用"

删掉 `$HOME/.cache/menus` 文件夹里的东西，然后重新运行 PCManFM。

PCManFM 需要设置`XDG_MENU_PREFIX`环境变量。变量的值应与`/etc/xdg/menus/`文件夹中文件的开头部分匹配。

参考 [#“打开方式”对话框窗口为空](#“打开方式”对话框窗口为空).

参考获取更多信息 以及Linux Mint论坛的（特别推荐）

### 无图标

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**当建议编辑配置文件时使用[Template:hc](https://wiki.archlinuxcn.org/wzh/Template:Hc)（在[Talk:PCManFM](https://wiki.archlinuxcn.org/wiki/Talk:PCManFM)讨论）

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 我们是否应该建议编辑文件管理器的全局 gtk 配置?（在 [Talk:PCManFM](https://wiki.archlinuxcn.org/wiki/Talk:PCManFM) 中讨论）


如果你用的是[窗口管理器](../窗口管理器/窗口管理器.md)而不是[桌面环境](../桌面环境/桌面环境.md)，而文件夹和文件没有图标，你需要指定 GTK 图标主题。

例如，你安装了 [oxygen-icons](https://archlinux.org/packages/?name=oxygen-icons)包，在 `~/.gtkrc-2.0` **或者** `/etc/gtk-2.0/gtkrc` 里添加这一行：

```
gtk-icon-theme-name = "oxygen"
```

> **注意：**重启 PCManFM 才能生效。

或者，用一个不同的主题（gnome，hicolor和 locolor 三个主题除外）。用下面这个命令查看安装了的图标主题：

```
$ ls ~/.icons/ /usr/share/icons/
```

如果看着都不爽，那就用这个命令查看所有可以安装的图标主题，选一个来安装：

```
$ pacman -Ss icon-theme
```

> **提示：**如果想要有个图形界面，安装 [lxappearance](https://archlinux.org/packages/?name=lxappearance)包 并用它来设置图标主题。

### 鼠标按钮不能触发 "上一/下一 文件夹" 功能

用 Xbindkeys 来修复这个功能。

安装 [xbindkeys](https://archlinux.org/packages/?name=xbindkeys)包、[xvkbd](https://aur.archlinux.org/packages/xvkbd/)AUR，在 `~/.xbindkeysrc` 里添加以下内容：

```
~/.xbindkeysrc
```

```
# Sample .xbindkeysrc for a G9x mouse.
"/usr/bin/xvkbd -text '\[Alt_L]\[Left]'"
 b:8
"/usr/bin/xvkbd -text '\[Alt_L]\[Right]'"
 b:9
```

按键代码可以通过 [xorg-xev](https://archlinux.org/packages/?name=xorg-xev)包 获取。

最后在 `~/.xinitrc` 里添加以下内容来在登录时触发 *xbindkeys*。

```
xbindkeys &
```

### \--desktop 参数不生效或使X-server崩溃

确保你有 `~/.config/pcmanfm` 文件夹的所有权和写权限。

通过使用 `--desktop-pref` 参数或者修改 `~/.config/pcmanfm/default/pcmanfm.config` 来设置桌面壁纸来解决问题。

### 终端模拟器的高级配置没有保存

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 为什么 libfm 的配置文件默认没有权限（在 [Talk:PCManFM](https://wiki.archlinuxcn.org/wiki/Talk:PCManFM) 中讨论）

请设置 libfm 配置文件的权限：

```
$ chmod -R 755 ~/.config/libfm
$ chmod 644 ~/.config/libfm/libfm.conf
```

### 记住文件排序设置

在 *View > Sort Files* 里可以设置文件排序，但是如果要让 PCManFM 记住这个设置，需要打开 *Edit > Preferences* 然后再关掉，这样会让当前的sort_type 和 sort_by 的值写入 `~/.config/pcmanfm/LXDE/pcmanfm.conf` 文件。

### 挂载设备时候提醒 "Not authorized"

在 `/etc/polkit-1/rules.d/00-mount-internal.rules` 文件里添加这个 [polkit](../../08_安全与加密/安全/Polkit.md) 规则：

```
/etc/polkit-1/rules.d/00-mount-internal.rules
```

```
polkit.addRule(function(action, subject) {
    if ((action.id == "org.freedesktop.udisks2.filesystem-mount-system" &&
       subject.local && subject.active && subject.isInGroup("storage")))
       {
          return polkit.Result.YES;
       }
 });
```

并且把你的用户添加到 `storage` [用户组](../../08_安全与加密/安全/用户和用户组.md)里：

```
# usermod -aG storage username
```

### 不支持操作

如果您忘记安装可选的依赖项 [gvfs](https://archlinux.org/packages/?name=gvfs)包，请先检查，否则请参阅[会话权限](../../02_系统管理与内核/系统恢复/常规故障排除.md#会话权限)一文。

### 系统重新启动时忘记密码

如果合适，安装一个 keyring 应用程序，比如 [GNOME/Keyring](../GNOME/GNOME_Keyring.md)，[KDE Wallet](../KDE/KDE%20Wallet.md) 或者 [lxqt_wallet](https://aur.archlinux.org/packages/lxqt_wallet/)AUR 用于 网络共享或[SSH agent](../../03_网络与服务/SSH/SSH%20密钥.md#SSH_agent)。
