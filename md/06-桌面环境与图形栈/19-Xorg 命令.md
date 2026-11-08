# 第 19 章　Xorg 命令

> 本章处理图形会话的两个入口：怎样把 X 服务器连同窗口管理器或桌面拉起来，以及会话起来后怎样查看、测试并调整分辨率与布局。两节独立，可依次通读：《Xinit》讲安装、配置与用法，是必读主干，技巧和窍门可后翻；《Xrandr》同样必读，测试配置与添加未被检测到的有效分辨率最实用，脚本与在 VNC 上使用 xrandr 按需取用，日常单屏环境可跳过。
> 
> 最容易踩的坑是只做临时改动：xrandr 调通的设置重启即失效，必须照使设置永久生效那一节写进配置；“未被检测到”不等于可以随意编造，能补进去的只有本身有效的分辨率。

<!-- 本章 2 节，共 28 KB -->

## 19.1　Xinit

*难度：进阶 ｜ 篇幅：10.0 KB ｜ 所属：Xorg 命令*

将要讲到：安装、配置、用法、技巧和窍门

摘自 [Wikipedia](https://en.wikipedia.org/wiki/xinit):

: *xinit* 程序允许用户手动启动 [Xorg](../X%20服务器/Xorg.md) 显示服务器。[startx(1)](https://man.archlinux.org/man/startx.1) 脚本是 [xinit(1)](https://man.archlinux.org/man/xinit.1) 的一个前端。

xinit 通常用于启动[窗口管理器](../窗口管理器/窗口管理器.md)或[桌面环境](../桌面环境/桌面环境.md)。虽然您也可以用 *xinit* 在没有窗口管理器的情况下运行 GUI 应用程序，但许多图形应用程序期待一个 [EWMH](https://en.wikipedia.org/wiki/Extended_Window_Manager_Hints) 兼容的窗口管理器。[显示管理器](../图形用户界面/显示管理器.md)一般都依据 [xprofile](../X%20服务器/Xprofile.md) 为您启动 [Xorg](../X%20服务器/Xorg.md)。

## 安装

安装 [xorg-xinit](https://archlinux.org/packages/?name=xorg-xinit)包 包。

## 配置

*xinit* 和 *startx* 需要一个可选的客户端程序参数，见 [#覆盖 xinitrc](#覆盖_xinitrc) 。 如果您没有提供参数，它们会寻找 `~/.xinitrc`，作为一个shell脚本运行，以启动客户程序。

### xinitrc

`~/.xinitrc` 可以方便地在X服务器启动时运行依赖于X的程序并设置环境变量。如果它存在于用户的主目录中，*startx* 和 *xinit* 会执行它。否则 *startx* 将运行默认的`/etc/X11/xinit/xinitrc`。

> **注意：***xinit* 有自己的默认行为，而不是执行该文件。详见 [xinit(1)](https://man.archlinux.org/man/xinit.1)。

这个默认的xinitrc将启动一个包含 [Twm](../堆叠式窗口管理器/Twm.md)、[xorg-xclock](https://archlinux.org/packages/?name=xorg-xclock)包 和 Xterm 的基本环境（假设安装了必要的软件包）。因此，要启动不同的窗口管理器或桌面环境，首先要在主目录下创建一个默认的 `xinitrc` 副本。

```
$ cp /etc/X11/xinit/xinitrc ~/.xinitrc
```

然后[编辑](https://wiki.archlinuxcn.org/wzh/Help:Reading_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#添加、创建、编辑文件)该文件，用想要的命令替换默认程序。记住，在使用 `exec` 的命令之后的行会被忽略。例如，要在后台启动 `xscreensaver`，然后启动 [openbox](../堆叠式窗口管理器/Openbox.md#单独运行)，请使用以下命令。

```
~/.xinitrc
```

```
...
xscreensaver &
exec openbox-session
```

> **注意：**至少要确保 `/etc/X11/xinit/xinitrc` 中的最后一个 `if` 块出现在您的 `~/.xinitrc` 文件中，以确保 `/etc/X11/xinit/xinitrc.d` 中的脚本有源可循。

在窗口管理器之前启动的长期运行的程序，如屏保和墙纸程序，必须自己分叉或通过附加 `&` 符号在后台运行。否则，在执行窗口管理器或桌面环境之前，脚本会停止运行并等待每个程序退出。请注意，有些程序反而不应该被分叉，以避免出现竞争性错误，如 xrdb 的情况。在 `exec` 前缀会将脚本进程替换为窗口管理器进程，这样，即使该进程分叉到后台，X也不会退出。

### xserverrc

`xserverrc` 文件是一个负责启动X服务器的shell脚本。startx "和 "xinit "都会执行 `~/.xserverrc`，如果它存在的话，"startx "会使用 `/etc/X11/xinit/xserverrc`。

为了保持 `logind`的会话权限，并防止通过切换终端绕过屏幕锁定器，[Xorg](../X%20服务器/Xorg.md) 必须在发生登录的同一虚拟终端上启动 。因此建议在 `vt$XDG_VTNR`文件中指定 `~/.xserverrc`。

```
~/.xserverrc
```

```
}文件：
#!/bin/sh

exec /usr/bin/Xorg -nolisten tcp "$@" vt$XDG_VTNR
```

参见 [Xserver(1)](https://man.archlinux.org/man/Xserver.1) 中的所有命令行选项列表。

> **提示：**`-nolisten local` 可以加在 `-nolisten tcp` 之后，以禁用X11的抽象套接字，帮助隔离。 关于这对X11安全的潜在影响，有一个 [简单背景](https://tstarling.com/blog/2016/06/x11-security-isolation/)。

另外，如果您想让X显示在一个独立的控制台，而不是调用服务器的控制台，您可以通过使用 `/usr/lib/systemd/systemd-multi-seat-x` 提供的X服务器包装器实现。为方便起见，可以通过修改 `~/.xserverrc` 设置 "xinit "和 "startx "使用该包装器。

`-keeptty`选项，重新启用将X会话的输出重定向至Xorg日志文件的功能。详见 [Xorg#重定向Xorg会话日志](../X%20服务器/Xorg.md#重定向Xorg会话日志)。}}

## 用法

要以普通用户身份运行 Xorg，请输入：

```
$ startx
```

或者如果配置了 [#xserverrc](#xserverrc)。

```
$ xinit -- :1
```

> **注意：***xinit* 不能处理已启动的另一个X服务器的多个显示。为此，您必须通过附加 `--:*display_number*` 来指定显示，其中 `*display_number*` 为 `1` 或以上。

您选择的窗口管理器（或桌面环境）现在应该可以正确启动了。

要退出X，请运行您的窗口管理器的退出功能（假设它有的话）。如果它缺乏这样的功能，请运行。

```
$ pkill -15 Xorg
```

> **注意：***pkill* 将杀死所有正在运行的X实例。要指定杀死当前虚拟终端上的窗口管理器，请运行：
>
> ```
> $ pkill -15 -t tty"$XDG_VTNR" Xorg
> ```

另见 [signal(7)](https://man.archlinux.org/man/signal.7)。

## 技巧和窍门

### 覆盖 xinitrc

如果您有一个正常工作的 `~/.xinitrc`，但只是想试试其他窗口管理器或桌面环境，您可以通过发出*startx*，并在后面加上窗口管理器的路径来运行它，例如。

```
$ startx /usr/bin/i3
```

如果二进制文件需要参数，它们需要加引号，以便被识别为*startx*的第一个参数的一部分。

```
$ startx "/usr/bin/application --key value "
```

注意，完整的路径是**必须的**。您也可以为 [#xserverrc](#xserverrc) 脚本指定自定义选项，在双破折号 `--` 后加上这些选项。

```
$ startx /usr/bin/enlightenment -- -br +bs -dpi 96
```

另请参见 [startx(1)](https://man.archlinux.org/man/startx.1)。

> **注意：**由于 `/etc/X11/xinit/xinitrc.d/` 下的脚本被跳过，环境变量 `DISPLAY` 可能需要被设置。您可以通过执行`DISPLAY=:*display_number* startx /usr/bin/i3`，在所需的显示器上试用 *i3*。

> **提示：**这可用于启动常规GUI程序，但没有任何基本的窗口管理器功能。参见 [#在没有窗口管理器的情况下启动应用程序](#在没有窗口管理器的情况下启动应用程序) 和 在独立的X显示屏中运行程序。

### 登录时自动启动 X

确保 *startx* 是正确的[配置](#配置)。

在您的 login shell 初始化文件（例如，[Bash](../../07_开发与工具/命令行解释器/Bash.md) 的 `~/.bash_profile` 或 [Zsh](../../07_开发与工具/命令行解释器/Zsh.md) 的 `~/.zprofile`）中放置以下内容。

```
if [ -z "${DISPLAY}" ] && [ "${XDG_VTNR}" -eq 1 ]; then
  exec startx
fi
```

您可以用 `-eq` 替换 `-le 3` 这样的比较（用于 vt1 到 vt3），如果您想在一个以上的虚拟终端上使用图形化登录。

检测虚拟终端的其他条件包括 `"$(tty)" = "/dev/tty1"`，它不允许与 `-le` 比较，以及 `"$(fgconsole 2>/dev/null || echo -1)" -eq 1`，它在串行控制台不起作用。

`exec` 命令确保在X服务器退出、崩溃或被攻击者杀死时，用户被注销。如果您想承担风险，在X会话结束时保持登录状态，请删除 `exec`。

参见 Fish_(简体中文)#登录_fish_时自动起动_X 和 Systemd_(简体中文)/User_(简体中文)#Automatic_login_into_Xorg_without_display_manager。

> **提示：**此方法可与 自动登录到虚拟控制台 相结合。

### 在桌面环境/窗口管理器之间进行切换

如果您经常在不同的桌面环境或窗口管理器之间切换，使用[显示管理器](../图形用户界面/显示管理器.md)或扩展 `~/.xinitrc` 来实现切换是很方便的。

下面的例子展示了如何用一个参数来启动一个特定的桌面环境或窗口管理器。

```
~/.xinitrc
```

```
...

# 这里 Xfce 保持默认
session=${1:-xfce}

case $session in
    i3|i3wm           ) exec i3;;
    kde               ) exec startplasma-x11;;
    xfce|xfce4        ) exec startxfce4;;
    # No known session, try to run it as command
    * ) exec $1;;
esac
```

要传递参数 *session*：

```
$ xinit session
```

或

```
$ startx ~/.xinitrc session
```

### 在没有窗口管理器的情况下启动应用程序

可以在没有窗口管理器的情况下只启动特定的应用程序，尽管这很可能只对全屏模式下的单个应用程序有用。比如说：

```
~/.xinitrc
```

```
...

exec chromium
```

另外，也可以像 [#覆盖 xinitrc](#覆盖_xinitrc) 中描述的那样，直接从命令提示符中调用二进制。

使用这种方法，您需要通过自己的配置文件设置每个应用程序的窗口几何形状（如果可能的话）。

> **提示：**这对启动图形游戏很有用，排除合成器的开销有助于提高游戏的性能。

另请参见[显示管理器#没有窗口管理启动应用程序](../图形用户界面/显示管理器.md#没有窗口管理启动应用程序)。

### 使用 startx 进行输出重定向

参见[Xorg#重定向Xorg会话日志](../X%20服务器/Xorg.md#重定向Xorg会话日志)了解详情。

## 19.2　Xrandr

*难度：进阶 ｜ 篇幅：17.8 KB ｜ 所属：Xorg 命令*

将要讲到：安装、测试配置、添加未被检测到的有效分辨率、使xrandr所更改的分辨率设置永久生效、脚本、在VNC上使用xrandr

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** 原文已有大量更新，请持续更新译文（在 [Talk:Xrandr#](https://wiki.archlinuxcn.org/wiki/Talk:Xrandr) 中讨论）

"xrandr" 是一款官方的 [RandR](https://en.wikipedia.org/wiki/RandR) (*Resize and Rotate*)[维基百科：X 窗口系统](https://en.wikipedia.org/wiki/X_Window_System)扩展配置工具。它可以设置屏幕显示的大小、方向、镜像等。对多显示器的情况，请参考 [Multihead](../X%20服务器/多显示器.md) 页面。

## 安装

安装软件包 [xorg-xrandr](https://archlinux.org/packages/?name=xorg-xrandr)包。

### 图形化操作程序

- **ARandR** — 提供了一个简单的图形化程序给 XrandR。显示器的位置会图形化地展示出来并可以使用拖拽的方式调整。

: [https://christian.amsuess.com/tools/arandr/](https://christian.amsuess.com/tools/arandr/) || [arandr](https://archlinux.org/packages/?name=arandr)包

- **LXRandR** — 是 LXDE 中的屏幕分辨率和外置显示器管理的工具.

: [https://wiki.lxde.org/en/LXRandR](https://wiki.lxde.org/en/LXRandR)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2022-09-23 ⓘ\] || GTK 2: [lxrandr](https://archlinux.org/packages/?name=lxrandr)包, GTK 3: [lxrandr-gtk3](https://archlinux.org/packages/?name=lxrandr-gtk3)包

### 命令行前端

- **xlayoutdisplay** — 检测和排列显示器。可处理：笔记本电脑开合状态，最高有效刷新率，计算并设定当前的 DPI。最适合用在.xinitrc 中，以便于在接入或断开外置显示器或闭合笔记本盖子时引用这些参数

: [https://github.com/alex-courtis/xlayoutdisplay](https://github.com/alex-courtis/xlayoutdisplay) || [xlayoutdisplay](https://aur.archlinux.org/packages/xlayoutdisplay/)AUR

## 测试配置

当没有添加任何选项直接运行时，*xrandr* 列出该系统可用的显示输出设备 (`VGA-1`, `HDMI-1` 等等) 和每一台设备可设置的分辨率，当前分辨率后面带有一个**\***号和一个**+**号:

```
xrandr
```

```
Screen 0: minimum 320 x 200, current 3200 x 1080, maximum 8192 x 8192
VGA-1 disconnected (normal left inverted right x axis y axis)
HDMI-1 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 531mm x 299mm
   1920x1080     59.93 +  60.00*   50.00    59.94
   1920x1080i    60.00    50.00    59.94
   1680x1050     59.88
…
```

> **注意：**如果你的分辨率没有出现在上方， 请看 [#添加未被检测到的有效分辨率](#添加未被检测到的有效分辨率)

你可以使用 *xrandr* 设置不同的分辨率（必须是出现在上面输出列表中的分辨率）：

```
$ xrandr --output HDMI-1 --mode 1920x1080
```

当列表中出现多个刷新率，可以通过 `--rate` 选项改变，一次性设置或者分开设置，例如：

```
$ xrandr --output HDMI-1 --mode 1920x1080 --rate 60
```

如果输出设备已经连接但被禁用，`--auto` 选项会以系统偏好的分辨率（最大分辨率）开启特定的输出设备：

```
$ xrandr --output HDMI-1 --auto
```

还可以用一条命令设置多个输出设备，例如，使用系统偏好的选项关闭 `HDMI-1` 并打开 `HDMI-2`：

```
$ xrandr --output HDMI-1 --off --output HDMI-2 --auto
```

> **注意：**
>
> - 通过 *xrandr* 作出的改变只在本次会话中有效
> - *xrandr* 有很多功能 - 详情请参考 [xrandr(1)](https://man.archlinux.org/man/xrandr.1)。

## 添加未被检测到的有效分辨率

由于出错的硬件或驱动，xrandr 可能并不能检测出您的显示器所有的有效分辨率。不过，我们可以在xrandr里添加所需要的分辨率。 Also, this same procedure can be used to add refresh rates you know are supported, but not enabled by your driver。

首先，运行`gtf`或者`cvt`，查询某分辨率的有效扫描频率。

```
 $ cvt 1280 1024

 # 1280x1024 59.89 Hz (CVT 1.31M4) hsync: 63.67 kHz; pclk: 109.00 MHz
 Modeline "1280x1024_60.00"  109.00  1280 1368 1496 1712  1024 1027 1034 1063 -hsync +vsync
```

然后通过--newmode参数新建一种xrandr模式，输入上面所得到的查询结果，其中Modeline关键词自然需要被省略。

```
   xrandr --newmode "1280x1024_60.00"  109.00  1280 1368 1496 1712  1024 1027 1034 1063 -hsync +vsync
```

新建模式后，我们需要把这模式添加到当前的输出设备（假定为VGA1）上。由于一些参数已经事先设置，只需输入模式名称即可，即1280x1024_60.00。

```
   xrandr --addmode VGA1 1280x1024_60.00
```

最后，再把VGA1的分辨率指定为刚刚添加的新模式。

```
   xrandr --output VGA1 --mode 1280x1024_60.00
```

注意，以上设置同样地只能在当前会话暂时生效。

如果您对所要添加的某分辨率感到不放心，您可以追加新命令“sleep 5”以及一条切换到已有有效分辨率的命令，以保证不会被困在实际无效的分辨率，示例：

```
   xrandr --output VGA1 --mode 1280x1024_60.00 && sleep 5 && xrandr --newmode "1024x768-safe" 65.00 1024 1048 1184 1344 768 771 777 806 -HSync -VSync && xrandr --addmode VGA1 1024x768-safe && xrandr --output VGA1 --mode 1024x768-safe
```

其他输出设备如法炮制：VGA1或DVI-I……

## 使xrandr所更改的分辨率设置永久生效

使xrandr定制永久生效的方案有：

- `xorg.conf`（推荐）
- `.xprofile`
- kdm/gdm

### 在xorg.conf设置分辨率（推荐）

示例：

```
/etc/X11/xorg.conf
```

```
Section "Monitor"
    Identifier      "External DVI"
    Modeline        "1280x1024_60.00"  108.88  1280 1360 1496 1712  1024 1025 1028 1060  -HSync +Vsync
    Option          "PreferredMode" "1280x1024_60.00"
EndSection
Section "Device"
    Identifier      "ATI Technologies, Inc. M22 [Radeon Mobility M300]"
    Driver          "ati"
    Option          "Monitor-DVI-0" "External DVI"
EndSection
Section "Screen"
    Identifier      "Primary Screen"
    Device          "ATI Technologies, Inc. M22 [Radeon Mobility M300]"
    DefaultDepth    24
    SubSection "Display"
        Depth           24
        Modes   "1280x1024" "1024x768" "640x480"
    EndSubSection
EndSection

Section "ServerLayout"
        Identifier      "Default Layout"
        Screen          "Primary Screen"
EndSection
```

关于更多的配置细节，请阅读[Xorg](../X%20服务器/Xorg.md)或[xorg.conf(5)](https://man.archlinux.org/man/xorg.conf.5)。

### 在xprofile设定xrandr命令

请阅读[xprofile](../X%20服务器/Xprofile.md).

这方案具有缺点：如果您使用[Display manager](../图形用户界面/显示管理器.md)的话，那么在启动进程之后很大程度上就会执行失败，最终无法顺利修改分辨率。

### 在KDM/GDM的启动脚本设定xrandr命令

KDM和GDM都具备在X初始化时，会被自动执行的启动脚本。GDM的启动脚本放在`/etc/gdm/`， KDM的则是`/usr/share/config/kdm/Xsetup`，SDDM 的则是在 `/usr/share/sddm/scripts/Xsetup`。您可以把相关的xrandr命令添加到这些启动脚本里。

这些脚本需要root权限及其他系统配置的配合，不过在启动进程里会比xprofile更早生效。

## 脚本

控制第二显示器的开关状态，默认显示器保持开启：

```
~/bin/xdisplay
```

```
#!/bin/bash
#
# This script toggles the extended monitor outputs if something is connected
#

# all available outputs
OUTPUTS=$(xrandr |awk '$2 ~ /connected/ {print $1}')

# your notebook LVDS monitor
DEFAULT_OUTPUT=$(sed -ne 's/.*(LVDS[^ ]*).*/1/p' <<<$OUTPUTS)

# get info from xrandr
XRANDR=`xrandr`

EXECUTE=""

for CURRENT in $OUTPUTS
do
        if [[ $XRANDR == *$CURRENT\ connected*  ]] # is connected
        then
                if [[ $XRANDR == *$CURRENT\ connected\ \(* ]] # is disabled
                then
                        EXECUTE+="--output $CURRENT --auto --above $DEFAULT_OUTPUT "
                else
                        EXECUTE+="--output $CURRENT --off "
                fi
        else # make sure disconnected outputs are off
                EXECUTE+="--output $CURRENT --off "
        fi
done

xrandr --output $DEFAULT_OUTPUT --auto $EXECUTE
```

在显示器之间切换，且只开启其中一个。

```
/usr/local/bin/toggle-display
```

```
#!/bin/bash
#
# toggle-display.sh
#
# Iterates through connected monitors in xrander and switched to the next one
# each time it is run.
#

# get info from xrandr
xStatus=`xrandr`
connectedOutputs=$(echo "$xStatus" | grep " connected" | sed -e "s/\([A-Z0-9]\+\) connected.*/\1/")
activeOutput=$(echo "$xStatus" | grep -e " connected [^(]" | sed -e "s/\([A-Z0-9]\+\) connected.*/\1/")
connected=$(echo $connectedOutputs | wc -w)

# initialize variables
execute="xrandr "
default="xrandr "
i=1
switch=0

for display in $connectedOutputs
do

	# build default configuration
	if [ $i -eq 1 ]
	then
		default=$default"--output $display --auto "
	else
		default=$default"--output $display --off "
	fi

	# build "switching" configuration
	if [ $switch -eq 1 ]
	then
		execute=$execute"--output $display --auto "
		switch=0
	else
		execute=$execute"--output $display --off "
	fi

	# check whether the next output should be switched on
	if [ $display = $activeOutput ]
	then
		switch=1
	fi

	i=$(( $i + 1 ))

done

# check if the default setup needs to be executed then run it
echo "Resulting Configuration:"
if [ -z "$(echo $execute | grep "auto")" ]
then
	echo "Command: $default"
	`$default`
else
	echo "Command: $execute"
	`$execute`
fi
echo -e "\n$(xrandr)"
```

您也可以使用xrr-events (from [AUR](https://github.com/aur-archive/xrr-events-git))，一种负责监听XrandR事件的daemon服务。当某台显示器接通状态发生变动时，就会执行相关脚本。可在man页面进一步查询具体信息。

## 在VNC上使用xrandr

如果您在使用某台支持xrandr的VNC服务器，您可以通过"xrandr -s <width>x<height>"命令实时修改VNC的分辨率。tigervnc就是一种支持xrandr的VNC客户端。

示例：

```
xrandr -s 1920x1200
```

登录VNC之后，如果您在控制台上输入"xrandr"，您将得到列出当前已配置模式的清单。每个模式均可通过xrandr -s选项激活。不过，若您所需要的模式并不在清单中，您可以按照以下来添加它。

示例：不妨想添加的是1024x600（上网本的一种常见分辨率）

首先执行CVT，得到理想分辨率所对应的正确刷新频率。

```
$ cvt 1024 600
```

您会得到如下类似的输出：

```
# 1024x600 59.85 Hz (CVT) hsync: 37.35 kHz; pclk: 49.00 MHz
Modeline "1024x600_60.00"   49.00  1024 1072 1168 1312  600 603 613 624 -hsync +vsync
```

在以下命令使用那些关于刷新频率的输出部分。

```
xrandr --newmode "1024x600"   49.00  1024 1072 1168 1312  600 603 613 624 -hsync +vsync
xrandr --addmode default "1024x600"
```

通过以上流程，输入xrandr -s 1024x600，就可以设置当前分辨率为1024x600，但是这设置只在当前的X会话暂时生效。为确保其模式永久可用，在~/.vnc/xstartup添加以下：

```
xrandr --newmode "1024x600"   49.00  1024 1072 1168 1312  600 603 613 624 -hsync +vsync
xrandr --addmode default "1024x600"r
```

## 疑难排除

### 添加未检测到的分辨率

由于硬件以及驱动程序可能的缺陷，例如，请求的EDID数据块不正确，导致 xrandr 可能不能准确侦测到显示器的分辨率。不过我们可以手工添加期望的分辨率。

首先，运行 `gtf` 或 `cvt` 以获取所需分辨率的**模式行（Modeline）**：

```
$ cvt 1280 1024
```

```
# 1280x1024 59.89 Hz (CVT 1.31M4) hsync: 63.67 kHz; pclk: 109.00 MHz
Modeline "1280x1024_60.00"  109.00  1280 1368 1496 1712  1024 1027 1034 1063 -hsync +vsync
```

> **提示：**For some LCD screens (e.g. Samsung 2343NW, Acer XB280HK), the command `cvt -r` (= with reduced blanking) is to be used.

> **注意：**如果使用了 Intel 的显示驱动程序 [xf86-video-intel](https://archlinux.org/packages/?name=xf86-video-intel)包，期望的分辨率会在 `/var/log/Xorg.0.log` 中与其他特征值一并报告——如果该值不同于 `gtf` or `cvt` 的输出应首选该值。这里给出一个log文件的报告值与 xrandr 使用值的实例：
>
> ```
> [    45.063] (II) intel(0): clock: 241.5 MHz   Image Size:  597 x 336 mm
> [    45.063] (II) intel(0): h_active: 2560  h_sync: 2600  h_sync_end 2632 h_blank_end 2720 h_border: 0
> [    45.063] (II) intel(0): v_active: 1440  v_sync: 1443  v_sync_end 1448 v_blanking: 1481 v_border: 0
> ```
>
> ```
> xrandr --newmode "2560x1440" 241.50 2560 2600 2632 2720 1440 1443 1448 1481 -hsync +vsync
> ```

然后创建一个新的 xandr 模式。注意模式行中那些被忽略的关键字。

```
$ xrandr --newmode "1280x1024_60.00"  109.00  1280 1368 1496 1712  1024 1027 1034 1063 -hsync +vsync
```

创建完毕还需要一个操作步骤，把这个新模式追加到当前显示输出端口（VGA1）。我们只需引用模式的名称，因为参数已经在前面设置好了。

```
$ xrandr --addmode VGA1 1280x1024_60.00
```

现在，我们可以把屏幕分辨率切换为刚刚追加的值：

```
$ xrandr --output VGA1 --mode 1280x1024_60.00
```

注意，上述这些设置仅在本次会话中有效。

如果你不能确保将要测试的分辨率可用，可以在后面附加一个延迟和一个安全分辨率命令行，就像这样：

```
$ xrandr --output VGA1 --mode 1280x1024_60.00 && sleep 5 && xrandr --newmode "1024x768-safe" 65.00 1024 1048 1184 1344 768 771 777 806 -HSync -VSync && xrandr --addmode VGA1 1024x768-safe && xrandr --output VGA1 --mode 1024x768-safe
```

还有，要把 `VGA1` 改为正确的输出端口名。

#### EDID 校验和无效

如果前述方法导致引导期间发生 `*ERROR* EDID checksum is invalid` 错误，参阅[这里](../../02_系统管理与内核/内核/内核级显示模式设置.md#Forcing_modes_and_EDID)和[这里](https://askubuntu.com/questions/201081/how-can-i-make-linux-behave-better-when-edid-is-unavailable).

也许 `xrandr --addmode` 会返回错误 `X Error of failed request: BadMatch`。NVIDIA 用户请参阅 [NVIDIA/Troubleshooting#xrandr BadMatch](../图形/NVIDIA_故障排除.md#xrandr_BadMatch)。`BadMatch` 能指示出无效的 EDID 校验和。要验证确实是这种情况，请以 verbose mode 运行 X 服务（例如：`startx -- -logverbose 6`）然后查阅 Xorg 日志中有关 EDID 错误的信息。

### 纠正电视机分辨率过扫

With a flat panel TV, [w:overscan](https://en.wikipedia.org/wiki/overscan) looks like the picture is "zoomed in" so the edges are cut off.

Check your TV if there is a parameter to change. If not, apply an `underscan` and change border values. The required `underscan vborder` and `underscan hborder` values can be different for you, just check it and change it by more or less.

`$ xrandr --output HDMI-0 --set underscan on --set "underscan vborder" 25 --set "underscan hborder" 40`

### 通过 --transform 纠正电视机过扫

如果`underscan`不可用另一个解决方案是使用 `xrandr --transform *a,b,c,d,e,f,g,h,i*`，它在输出上应用了一个变换矩阵，参考 [xrandr(1) § RandR_version_1.3_options](https://man.archlinux.org/man/xrandr.1#RandR_version_1.3_options)手册页面来了解`transform`的解释.

举个例子，将横坐标缩放`0.8`，纵坐标缩放`1.04`，并将屏幕向右移动35像素，向下移动19像素，参考如下指令。

```
$ xrandr --output HDMI1 --transform 0.80,0,-35,0,1.04,-19,0,0,1
```

### HDMI中的Full RGB支持

当[Intel](../图形/Intel%20图形处理器.md)驱动没有正确设置HDMI输出时可能出现该种问题。这会限制颜色范围到(16-235)通过[Broadcast RGB property](https://patchwork.kernel.org/patch/1972181/)，这导致黑色看起来不够黑，它会变成灰色。

要验证你是不是这种情况:

```
$ xrandr --output HDMI1 --set "Broadcast RGB" "Full"
```

#### 屏幕分辨率在屏幕闪一下之后恢复原状

如果你使用[GNOME](../GNOME/GNOME.md)而且你的显示器没有EDID, 上述[#添加未检测到的分辨率](#添加未检测到的分辨率)可能不会工作，当你使用`xrandr --output`时，屏幕只会闪一下。

你可以试试`~/.config/monitors.xml`,或者把这个文件删除，然后重启计算机。

更好的解释可以参考[这个](https://unix.stackexchange.com/questions/184941/gnome-prevents-high-resolution-vga-without-edid-info-over-vga)文章。

## 参见

- DualScreen Arch wiki page. How to get dual screens with Xrandr
- [https://wiki.ubuntu.com/X/Config/Resolution](https://wiki.ubuntu.com/X/Config/Resolution)
- [Debian Wiki - RandR 1.2 tutorial](https://wiki.debian.org/XStrikeForce/HowToRandR12)
- [https://bbs.archlinux.org/viewtopic.php?pid=652861](https://bbs.archlinux.org/viewtopic.php?pid=652861)
- [https://nouveau.freedesktop.org/Randr12.html](https://nouveau.freedesktop.org/Randr12.html)
- [https://wiki.debian.org/XStrikeForce/HowToRandR12](https://wiki.debian.org/XStrikeForce/HowToRandR12)
- man xrandr
