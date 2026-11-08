# 第 15 章　Wayland

> 图形会话最终要落到协议栈上。Wayland 一节篇幅最长，从系统需求、混成器、显示管理器讲到 Xwayland、图形库与输入法，讲的是协议怎么支撑起整个图形会话，应当先读，读到能分清混成器与显示管理器的分工为止。Kanshi 与通用Wayland会话管理器各只几页，前者管多显示器布局的声明式配置，后者管会话启动与进程管理，放在后面当速查即可；时间紧时只扫安装与配置，疑难解答留到动手时翻。最容易踩的坑是把混成器当显示管理器来配，或 Xwayland 缺位时先怀疑驱动——多半是配置层级错位。

<!-- 本章 3 节，共 50 KB -->

## 15.1　Kanshi

*难度：入门 ｜ 篇幅：4.9 KB ｜ 所属：Wayland*

将要讲到：安装、配置、使用、提示和技巧、疑难解答、另请参阅

[kanshi](https://gitlab.freedesktop.org/emersion/kanshi) 可以在热插拔时自动启用和禁用显示输出的配置文件。例如，当有扩展屏幕时，kanshi 可以自动关闭笔记本内置屏幕。

这是 [autorandr](https://github.com/phillipberndt/autorandr) 等工具在 Wayland 上的等效替代品。kanshi 可在支持 wlr-output-management 协议的 Wayland 混成器上使用

## 安装

安装正式版本 [kanshi](https://archlinux.org/packages/?name=kanshi)包 或开发版本 [kanshi-git](https://aur.archlinux.org/packages/kanshi-git/)AUR。

## 配置

### 基础配置

创建 kanshi 配置文件：

```
~/.config/kanshi/config
```

```
profile {
	output LVDS-1 disable
	output "Some Company ASDF 4242" mode 1600x900 position 0,0
}

profile {
	output LVDS-1 enable scale 2
}
```

每个配置（profile）都由括号分隔，其包含几个输出指令（语法类似 [sway-output(5)](https://man.archlinux.org/man/sway-output.5)）。当一个配置所列的所有输出（output）都已连接时，该配置就会启用。

> **提示：**可以使用 `swaymsg -t get_outputs` 命令获取输出名称列表和其它信息。

### 进阶配置

为了易于管理多种配置*（如“仅笔记本内置屏幕”、“在家连接一个扩展屏”、“工作时连接两个扩展屏”等配置）*，您可定义配置里使用的输出的默认行为并为其分配别名。默认行为会应用到所有提到该输出的配置中。更多信息参见 [kanshi(5)](https://man.archlinux.org/man/kanshi.5)。

```
~/.config/kanshi/config
```

```
output "Dell Inc. DELL S2721DGF G52TR83" {
  mode 2560x1440@165.08
  position 1280,0
  scale 2
  alias $HOME_1
}

output "LG Display 0x058B Unknown" {
  mode 2560x1440@59.99800
  position 0,0
  scale 2
  alias $INTERNAL
}

profile home_1 {
  output $INTERNAL disable
  output $HOME_1 enable
}
```

注意 *$INTERNAL* 输出的名称的第三段是 *UNKNOWN*。这是因为 kanshi 需要三个字段（制造商，型号，序列号）都被填充，如果缺少一个则需要使用 *UNKNOWN* 代替。

您可使用 [hyprctl monitors](https://wiki.hyprland.org/Configuring/Using-hyprctl/) 等命令查询这些字段，其会输出这三个字段：

```
hyprctl monitors
```

```
make: LG Display
model: 0x058B
serial:
```

您还可以让您的工作流与 kanshi 进一步集成，使用 *exec* 以在配置启用时执行命令：

```
~/.config/kanshi/config
```

```
profile home_1 {
  output $INTERNAL disable
  output $HOME_1 enable
  exec uwsm app -- $HOME/.config/hypr/scripts/move-workspaces.sh "LG Display 0x058B"
}
```

## 使用

执行命令：

```
$ kanshi
```

自动用法参见[#使用 systemd 管理 kanshi](#使用_systemd_管理_kanshi)。

## 提示和技巧

### 使用 systemd 管理 kanshi

按照 [Sway#使用 systemd 管理仅用于 Sway 的守护程序](../Wayland%20合成器/Sway.md#使用_systemd_管理仅用于_Sway_的守护程序)创建并启动 `~/.config/systemd/user/sway-session.target`（如果尚未创建）。

创建 `kanshi.service` 文件：

```
~/.config/systemd/user/kanshi.service
```

```
[Unit]
Description=Dynamic output configuration for Wayland compositors
Documentation=https://gitlab.freedesktop.org/emersion/kanshi
BindsTo=sway-session.target

[Service]
Type=simple
ExecStart=/usr/bin/kanshi

[Install]
WantedBy=sway-session.target
```

启用该[用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)。其仅会在 Sway 运行时激活并在 Sway 停止时停用。

## 疑难解答

### 唤醒后外置屏幕工作区编号从 2 开始

若您参照[上文](#配置)配置，当第二块屏幕连接到计算机时，第一块屏幕被禁用，第二块屏幕从 1 开始编号工作区，您首次接入屏幕或开机后应当是这种状况。但从睡眠唤醒后，这个数字可能会变成 2。欲解决，只需将第一个工作区移动到第二块屏幕，向 kanshi 配置文件中添加配置命令：

```
~/.config/kanshi/config
```

```
profile {
	output LVDS-1 disable
	output "Some Company ASDF 4242" mode 1600x900 position 0,0
	# 以下这条就是添加的命令（别忘了更新输出名称）：
	exec swaymsg workspace 1, move workspace to HDMI-A-1
}

profile {
	output LVDS-1 enable scale 2
}
```

## 另请参阅

- [源码和文档](https://gitlab.freedesktop.org/emersion/kanshi)

## 15.2　通用Wayland会话管理器

*难度：进阶 ｜ 篇幅：6.7 KB ｜ 所属：Wayland*

将要讲到：安装、配置、使用、技巧提示、另见

[通用Wayland会话管理器](https://github.com/Vladimir-csp/uwsm) (**uwsm**) 运行时会将独立 [Wayland混成器](Wayland.md#混成器)变成一组[systemd](../../01_安装与包管理/初始化/Systemd.md) 单元。这提供一个包括环境的稳定的会话管理，并且支持[XDG Autostart](../Freedesktop.org/XDG%20Autostart.md)，与登录会话双向连接，并且可以完全关闭。

> **注意：**强烈建议使用[dbus-broker](../../02_系统管理与内核/系统管理/D-Bus.md#dbus-broker)作为D-Bus守护进程的实现。因为它会重复利用systemd激活环境，而不是启动一个独立的环境。这个简化的环境管理可以有恰当的清理，也支持[reference implementation](../../02_系统管理与内核/系统管理/D-Bus.md#Reference_implementation)，但是不允许取消设置一个变量，所以更高效的清理方式是将其设置为空字符。唯一可能的清理办法是使用`loginctl terminate-user ""`来将D-Bus守护进程引用的环境分离

## 安装

安装 [uwsm](https://archlinux.org/packages/?name=uwsm)包（或者你也可以安装[uwsm-git](https://aur.archlinux.org/packages/uwsm-git/)AUR的Git版本以获取`master`分支上的最新提交）

## 配置

### 服务启动通知和混成器设置的变量

> **注意：**如果被管理的混成器已经将 `WAYLAND_DISPLAY`（和其他有用的环境变量）添加到 systemd 激活的环境中，那么你可以跳过这部分，并且无需使用 `uwsm finalize`。

为了找到当前的混成器，一个Wayland软件作为systemd服务运行需要 `WAYLAND_DISPLAY` 环境变量（如果它们预期是在[Xwayland](Wayland.md)中运行则是 `DISPLAY`）。因此，一旦混成器设置了这个和其他有用的环境变量，都应该被放进 systemd/dbus 激活环境中。

`uwsm finalize` 命令会根据 `UWSM_FINALIZE_VARNAMES` 列表（使用空白字符 (white-space) 作为分隔符），将 `WAYLAND_DISPLAY`、`DISPLAY` 和其他环境变量加入到激活环境。建议在混成器就绪后执行该命令。

如果在激活环境中需要其他由混成器设置的变量，可以将变量名称作为参数传递给 `uwsm finalize` 或将其放入 `UWSM_FINALIZE_VARNAMES` 中的使用空白字符分隔的列表中。例如：

```
exec uwsm finalize VAR1 VAR2 ...
export UWSM_FINALIZE_VARNAMES=VAR1 VAR2 ...
```

### 环境变量

所有来源于*uwsm*的环境变量都设置在`${XDG_CONFIG_HOME}/uwsm/env`并且可用于在这个会话中所有的被管理混成器和图形化软件。

如果你想仅仅为一个指定的*混成器*（以及对应图形会话中的图形应用程序）设置一些环境变量，那么请将变量放在`${XDG_CONFIG_HOME}/uwsm/env-*compositor*`

这个文件的模板如下：

```
~/.config/uwsm/env
```

```
export KEY1=VAR1
export KEY2=VAR2
export KEY3=VAR3
...
```

## 使用

### 启动

> **注意：**如果在`uwsm start`中的环境变量被成功使用，那么环境预加载器不再生效POSIX shell配置。

*uwsm* 可以从TTY或者[显示管理器](../图形用户界面/显示管理器.md)启动。

#### 从TTY启动

将如下代码添加进`~/.profile`：

```
if uwsm check may-start && uwsm select; then
  exec uwsm start default
fi
```

如果你想始终启动指定*混成器*，将上述代码替换为：

```
if uwsm check may-start; then
  exec uwsm start compositor.desktop
fi
```

#### 从显示管理器中启动

你可以创建一个自定义[桌面项](../../01_安装与包管理/配置文件/桌面项.md)以通过*uwsm*启动你的混成器：

```
/usr/share/wayland-sessions/my-compositor-uwsm.desktop
```

```
[Desktop Entry]
Name=My compositor (with UWSM)
Comment=My cool compositor, UWSM session

# either full command line with metadata and executable
Exec=uwsm start -N "My compositor" -D mycompositor:mylib -C "My cool compositor" -- my-compositor

# or a reference to another entry
Exec=uwsm start -- my-compositor.desktop

DesktopNames=mycompositor;mylib
Type=Application
```

### 终止会话

如果你想终止当前*uwsm*会话，那么你可以使用`loginctl terminate-user ""`（关闭整个用户会话）或`uwsm stop`（如果uwsm取代了[登录shell](../../07_开发与工具/命令行解释器/命令行解释器.md#登录_Shell)，那么它会在`uwsm start`后启动代码或终止用户会话）

> **注意：**请不要使用混成器的原生退出机制或者直接杀死其进程。这将会使混成器从所有客户端之下强制抽除，并干扰有序的单元停用流程。

## 技巧提示

### 软件与自动启动

默认情况下，uwsm 通过在`session.slice`中的一个自定义systemd服务来启动混成器。很多Wayland混成器允许你在混成器服务中启动其他软件，然而这将会消耗混成器资源，甚至会干扰通知sockets

要将应用程序作为独立的 systemd 范围单元 (scope units) 启动，你可以使用 `uwsm app`，它既可以启动可执行文件：

```
uwsm app -- /my/program/path
```

也可以启动[桌面项](../../01_安装与包管理/配置文件/桌面项.md)：

```
uwsm app -- myprogram.desktop
```

uwsm 默认会将启动的范围单元放置在 `app-graphical.slice`。如果你想要将它们放置在 `background-graphical.slice` 或 `session-graphical.slice`，应该分别使用 `-s b` 和 `-s s`：

```
uwsm app -s b -- background-app.desktop
```

#### 替代

你可以使用以下方案作为替代，比起`uwsm app`（运行一个Python脚本）会更快：

- uwsm 的 `uwsm-app` 脚本，与 uwsm 的应用守护进程进行通信。
- [app2unit-git](https://aur.archlinux.org/packages/app2unit-git/)AUR，一个shell脚本。你可以通过设置`APP2UNIT_SLICES`环境变量为如下值来让它作为`uwsm app`的替代方案：

    ```
    APP2UNIT_SLICES='a=app-graphical.slice b=background-graphical.slice s=session-graphical.slice'
    ```

- [runapp](https://aur.archlinux.org/packages/runapp/)AUR，由C++编写，但是少了些许功能。

## 另见

- [uwsm(1)](https://man.archlinux.org/man/uwsm.1)

## 15.3　Wayland

*难度：高级 ｜ 篇幅：38.2 KB ｜ 所属：Wayland*

将要讲到：系统需求、混成器、显示管理器、Xwayland、图形库、输入法

[Wayland](https://wayland.freedesktop.org/) 是一个显示服务器协议，已普遍作为 [X 窗口系统](../X%20服务器/Xorg.md)的继任者    。可参见 [Wikipedia 上的 Wayland 与 Xorg 的对比](https://en.wikipedia.org/wiki/Wayland_(display_server_protocol)#Differences_between_Wayland_and_X)。

由于使用 Wayland 协议的显示服务器也可作为[混成窗口管理器](https://en.wikipedia.org/wiki/Compositing_window_manager)，因此被称为**混成器**（compositor）。你可以在[#混成器](#混成器)中找到相关信息。

为了兼容原生 [X11](../X%20服务器/Xorg.md) 应用程序，使其在 Wayland 中无缝运行，可以使用 [#Xwayland](#Xwayland)，它在 Wayland 中提供了 X 服务器。

## 系统需求

和 [Xorg](../X%20服务器/Xorg.md) 不同的是，Wayland 本身只是一个协议，没有一个通用的“显示服务器”可供安装。需要安装的是兼容的 GPU 驱动（本节）和混成器（下一节）或将混成器内置其中的[桌面环境](../桌面环境/桌面环境.md)（例如 [GNOME](../GNOME/GNOME.md) 或 [Plasma](../KDE/KDE.md)）。大多数 Wayland 混成器只能在使用[内核级显示模式设置](../../02_系统管理与内核/内核/内核级显示模式设置.md)的系统上工作。

GPU 驱动与 Wayland 混成器必须支持相同的缓冲区 API (Buffer API) 才能够互相兼容。现在主要有两种 API：[GBM](https://en.wikipedia.org/wiki/Generic_Buffer_Management) 和 [EGLStreams](https://www.phoronix.com/scan.php?page=news_item&px=XDC2016-Device-Memory-API)。

| 缓冲区 API | GPU 驱动支持 | Wayland 混成器支持 |
| --- | --- | --- |
| GBM | 除版本号低于 495 的 [NVIDIA](../图形/NVIDIA.md) 驱动，均支持 | 全部 |
| EGLStreams | [NVIDIA](../图形/NVIDIA.md) | [GNOME](../GNOME/GNOME.md) |

: \* 版本 ≥ 495 的 NVIDIA 驱动同时支持 EGLStreams 和 GBM。

自 NVIDIA 在版本 495 引入 GBM 支持以来，许多混成器 (包括 Mutter 和 KWin) 都开始默认使用 GBM 。通常认为 GBM 更好，有更为广泛的支持，而以前仅支持 EGLStreams 是因为之前无法在 Wayland 下通过专有驱动程序来使用 NVIDIA GPU。此外，在 NVIDIA 支持 GBM 后，KWin 放弃了对 EGLStreams 的支持

如果您使用的是流行的桌面环境/混成器，GPU 也受 NVIDIA 支持，那么很可能已经在使用 GBM 后端了。要检查是否使用 GBM 后端，请执行 `journalctl -b 0 --grep "renderer for"`。要强制使用 GBM 后端，请设置以下[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)：

```
GBM_BACKEND=nvidia-drm
__GLX_VENDOR_LIBRARY_NAME=nvidia
```

## 混成器

**堆叠式**、**平铺式**和**动态**的区别参见[窗口管理器#类型](../窗口管理器/窗口管理器.md#类型)。

### 堆叠式

- **hikari** — 基于 wlroots 并受 [cwm](../../06_软件与应用/OpenBSD/Cwm.md) 启发开发，在 FreeBSD 上开发很活跃，但也支持 Linux。

: [https://hikari.acmelabs.space/](https://hikari.acmelabs.space/) || [hikari](https://aur.archlinux.org/packages/hikari/)AUR

- **KDE [KWin](https://en.wikipedia.org/wiki/KWin)** — 请参阅 [KDE#启动 Plasma](../KDE/KDE.md#启动_Plasma)。

: [https://userbase.kde.org/KWin](https://userbase.kde.org/KWin) || [kwin](https://archlinux.org/packages/?name=kwin)包

- **labwc（英语：[labwc](https://wiki.archlinux.org/title/labwc)）** — 基于 wlroots 并受 Openbox 启发的混成器。

: [https://github.com/labwc/labwc](https://github.com/labwc/labwc) || [labwc](https://archlinux.org/packages/?name=labwc)包

- **[Mutter](https://en.wikipedia.org/wiki/Mutter_(software))** — 请参阅 [GNOME#运行 GNOME](../GNOME/GNOME.md#运行_GNOME)。

: [https://gitlab.gnome.org/GNOME/mutter](https://gitlab.gnome.org/GNOME/mutter) || [mutter](https://archlinux.org/packages/?name=mutter)包

- **waybox** — 一个 \*box 风格（极简主义）的 Wayland 混成器，主要基于 Openbox 设计。

: [https://github.com/wizbright/waybox](https://github.com/wizbright/waybox) || [waybox](https://aur.archlinux.org/packages/waybox/)AUR

- **[wayfire](../Wayland%20合成器/Wayfire.md)** — 3D 混成器，受 [Compiz](../堆叠式窗口管理器/Compiz.md) 启发并基于 wlroots 开发。

: [https://wayfire.org/](https://wayfire.org/) || [wayfire](https://aur.archlinux.org/packages/wayfire/)AUR

- **[Weston](../Wayland%20合成器/Weston.md)** — Wayland 混成器，设计注重正确性、可靠性、可预测性和性能。

: [https://gitlab.freedesktop.org/wayland/weston](https://gitlab.freedesktop.org/wayland/weston) || [weston](https://archlinux.org/packages/?name=weston)包

- **wio** — 基于 wlroots 的混成器，致力于复刻 Plan 9 的 Rio 桌面的外观和质感。

: [https://gitlab.com/Rubo/wio](https://gitlab.com/Rubo/wio) || [wio-wl](https://aur.archlinux.org/packages/wio-wl/)AUR

- **wlmaker** — 基于 wlroots 的混成器，受 Window Maker（英语：[Window Maker](https://wiki.archlinux.org/title/Window_Maker)） 启发。

: [https://phkaeser.github.io/wlmaker/](https://phkaeser.github.io/wlmaker/) || [wlmaker](https://aur.archlinux.org/packages/wlmaker/)AUR

- **woodland** — 一个极简轻量级的基于 wlroots 的堆叠式混成器，适用于 Wayland，受 wayfire 和 TinyWl 启发。

: [https://github.com/DiogenesN/woodland](https://github.com/DiogenesN/woodland) || [woodland](https://aur.archlinux.org/packages/woodland/)AUR

### 平铺式

- **Cagebreak（英语：[Cagebreak](https://wiki.archlinux.org/title/Cagebreak)）** — 基于 Cage，受 [Ratpoison](../平铺式窗口管理器/Ratpoison.md) 启发。

: [https://github.com/project-repo/cagebreak](https://github.com/project-repo/cagebreak) || [cagebreak](https://aur.archlinux.org/packages/cagebreak/)AUR

- **jay** — 一个使用 Rust 编写的 Wayland 混成器，具有类似于 i3 的默认外观。Jay 可以使用一个声明式的 TOML 文件或一个注入到混成器中的共享数据库配置。

: [https://github.com/mahkoh/jay](https://github.com/mahkoh/jay) || [jay](https://aur.archlinux.org/packages/jay/)AUR

- **IonWL** — 手动平铺混成器。使用全面的 Python API 开发，遵循 Ion3 的设计。

: [https://codeberg.org/ideasman42/IonWL](https://codeberg.org/ideasman42/IonWL) || [ionwl-git](https://aur.archlinux.org/packages/ionwl-git/)AUR

- **miracle-wm** — 基于 Mir 的 Wayland 混成器，风格类似 i3 和 sway，旨在比两者更炫目且功能更丰富，如 swayfx。

: [https://github.com/miracle-wm-org/miracle-wm](https://github.com/miracle-wm-org/miracle-wm) || [miracle-wm](https://aur.archlinux.org/packages/miracle-wm/)AUR

- **[niri](../Wayland%20合成器/Niri.md)** — 可滚动的平铺式 Wayland 混成器。

: [https://github.com/YaLTeR/niri/](https://github.com/YaLTeR/niri/) || [niri](https://archlinux.org/packages/?name=niri)包

- **[Qtile](../平铺式窗口管理器/Qtile.md)** — 功能齐全、可定制性强的平铺式 Xorg 窗口管理器和 Wayland 混成器，使用 Python 开发、配置。

: [https://github.com/qtile/qtile](https://github.com/qtile/qtile) || [qtile](https://archlinux.org/packages/?name=qtile)包

- **[Sway](../Wayland%20合成器/Sway.md)** — 基于 wlroots、与 [i3](../动态窗口管理器/I3.md) 兼容的 Wayland 混成器。

: [https://github.com/swaywm/sway](https://github.com/swaywm/sway) || [sway](https://archlinux.org/packages/?name=sway)包

- **SwayFx** — [Sway](../Wayland%20合成器/Sway.md)，但带有视觉效果！

: [https://github.com/WillPower3309/swayfx](https://github.com/WillPower3309/swayfx) || [swayfx](https://aur.archlinux.org/packages/swayfx/)AUR

- **Velox** — 基于 swc 的简单窗口管理器，受 dwm 和 [xmonad](../平铺式窗口管理器/Xmonad.md) 启发。

: [https://github.com/michaelforney/velox](https://github.com/michaelforney/velox) || [velox-git](https://aur.archlinux.org/packages/velox-git/)AUR

### 动态

- **cosmic-comp** — [COSMIC](../桌面环境/COSMIC.md) 桌面环境的混成器。

: [https://github.com/pop-os/cosmic-comp](https://github.com/pop-os/cosmic-comp) || [cosmic-comp](https://archlinux.org/packages/?name=cosmic-comp)包

- **cwc（英语：[cwc](https://wiki.archlinux.org/title/cwc)）** — 基于 wlroots、类似[awesome](../动态窗口管理器/Awesome.md)的 Wayland 混成器

: [https://cudiph.github.io/cwc/apidoc/](https://cudiph.github.io/cwc/apidoc/) || [cwc](https://aur.archlinux.org/packages/cwc/)AUR

- **[dwl](../Wayland%20合成器/Dwl.md)** — 基于 wlroots、类似 [dwm](../动态窗口管理器/Dwm.md) 的 Wayland 混成器。

: [https://codeberg.org/dwl/dwl](https://codeberg.org/dwl/dwl) || [dwl](https://aur.archlinux.org/packages/dwl/)AUR

- **[Hyprland](../Wayland%20合成器/Hyprland.md)** — 不以牺牲外观为代价的动态平铺式 Wayland 混成器。

: [https://hypr.land](https://hypr.land) || [hyprland](https://archlinux.org/packages/?name=hyprland)包

- **japokwm** — 基于 wlroots 创建布局的动态平铺式 Wayland 混成器。

: [https://github.com/werererer/japokwm](https://github.com/werererer/japokwm) || [japokwm-git](https://aur.archlinux.org/packages/japokwm-git/)AUR

- **Mangowc（英语：[Mangowc](https://wiki.archlinux.org/title/Mangowc)）** — 基于 [dwl](../Wayland%20合成器/Dwl.md) 的混成器，提供标准配置文件、可选的滚动布局以及视觉特效支持。

: [https://github.com/DreamMaoMao/mangowc](https://github.com/DreamMaoMao/mangowc) || [mangowc](https://aur.archlinux.org/packages/mangowc/)AUR

- **River Classic（英语：[River Classic](https://wiki.archlinux.org/title/River_Classic)）** — 受 dwm 和 [bspwm](../平铺式窗口管理器/Bspwm.md) 启发的动态平铺式 Wayland 混成器。

: [https://codeberg.org/river/river-classic](https://codeberg.org/river/river-classic) || [river-classic](https://archlinux.org/packages/?name=river-classic)包

- **pinnacle-comp** — A Smithay-based Wayland compositor, inspired by AwesomeWM and configured in Lua or Rust

: [https://github.com/pinnacle-comp/pinnacle](https://github.com/pinnacle-comp/pinnacle) || [pinnacle-comp](https://aur.archlinux.org/packages/pinnacle-comp/)AUR

### 其它

- **Cage** — 像自助终端那样，全屏显示单个应用程序。

: [https://www.hjdskes.nl/projects/cage/](https://www.hjdskes.nl/projects/cage/) || [cage](https://archlinux.org/packages/?name=cage)包

- **GNOME Kiosk** — 基于 Mutter 的混成器，适用于固定用途或单应用部署，如墙面显示器和销售终端（kiosk）。

: [https://gitlab.gnome.org/GNOME/gnome-kiosk](https://gitlab.gnome.org/GNOME/gnome-kiosk) || [gnome-kiosk](https://aur.archlinux.org/packages/gnome-kiosk/)AUR

- **phoc** — 适用于移动设备的基于 wlroots 的轻量混成器。

: [https://gitlab.gnome.org/World/Phosh/phoc](https://gitlab.gnome.org/World/Phosh/phoc) || [phoc](https://archlinux.org/packages/?name=phoc)包

- **Wayback** — X11 兼容层，允许使用 Wayland 组件运行完整的 X11 桌面环境。实验性，开发处于早期阶段。

: [https://wayback.freedesktop.org/](https://wayback.freedesktop.org/) || [wayback-x11](https://aur.archlinux.org/packages/wayback-x11/)AUR

- **River（英语：[River](https://wiki.archlinux.org/title/River)）** — 一个非整体的 Wayland 混成器。和其他的 Wayland 混成器不同，它不把混成器和窗口管理器结合在一个程序中。相反，用户可以选择使用任何遵循 river-window-management-v1 协议的窗口管理器。

: [https://isaacfreund.com/software/river/](https://isaacfreund.com/software/river/) || [river](https://archlinux.org/packages/?name=river)包

上述某些混成器支持从[显示管理器](../图形用户界面/显示管理器.md)启动。请查看 `/usr/share/wayland-sessions/*混成器*.desktop` 文件来了解如何启动显示上述混成器。

## 显示管理器

下面列出的显示管理器均支持启动 Wayland 混成器。

| 名称 | 运行于 | 描述 |
| --- | --- | --- |
| [atrium](https://aur.archlinux.org/packages/atrium/)AUR | Wayland | 具有第一类多 seat 支持的显示管理器。 |
| [emptty](https://archlinux.org/packages/?name=emptty)包 | tty | 简单的 TTY CLI 显示管理器。 |
| [plasma-login-manager-git](https://aur.archlinux.org/packages/plasma-login-manager-git/)AUR | Wayland | KDE目前正在活跃开发的显示管理器。 |
| [GDM](../GNOME/GDM.md) | Wayland | [GNOME](../GNOME/GNOME.md) 的显示管理器。 |
| [greetd](../显示管理器/Greetd.md) | Wayland/Xorg/tty<br>参考 [Greetd#Greeters](../显示管理器/Greetd.md#Greeters)。

 | 极简且使用灵活的登录守护程序。 |
| [lemurs](https://archlinux.org/packages/?name=lemurs)包 | tty | 用 Rust 编写的 TUI 显示管理器。 |
| [lidm](https://aur.archlinux.org/packages/lidm/)AUR | tty | 用C语言开发的全彩可定制的TUI 显示管理器. |
| [LightDM](../../06_软件与应用/Canonical/LightDM.md) | Xorg | 跨桌面显示管理器。 |
| [Ly](../显示管理器/Ly.md) | tty | 用 Zig 语言编写的 TUI 显示管理器。 |
| [SDDM](../显示管理器/SDDM.md) | Wayland/Xorg | 基于 QML 开发的显示管理器。 |
| [tbsm](https://aur.archlinux.org/packages/tbsm/)AUR | tty | 用纯 Bash 编写的简单 CLI 会话启动器。 |
| [uwsm](通用Wayland会话管理器.md) | tty | 管理独立混成器的会话和 XDG 自启的管理器。提供一个TUI 菜单，但也可以与其他显示管理器一起使用。 |

## Xwayland

[Xwayland(1)](https://man.archlinux.org/man/Xwayland.1) 是一个在 Wayland 下运行的 X 服务器，以兼容尚未支持 Wayland 的原生 [X11](../X%20服务器/Xorg.md) 应用程序。要使用它，请安装 [xorg-xwayland](https://archlinux.org/packages/?name=xorg-xwayland)包。

Xwayland 通过混成器启动，因此请参见所使用混成器的文档了解其兼容性和启动方式。

默认情况下，Xwayland 使用 [rootless](../X%20服务器/Xorg.md#没有_root_权限的_Xorg) 模式运行 X 服务器。

> **注意：**
>
> - 安全性：Xwayland 是一个 X 服务器，因此不具备 Wayland 的安全特性。
> - 性能：大部分情况下，Xwayland 的性能与 X11 [几乎相同](https://openbenchmarking.org/result/2202053-NE-NVIDIARTX35)。
> - 兼容性：Xwayland 并不完全向后兼容 X11。某些应用程序在 Xwayland 下可能无法正常工作。

### Wayback

[Wayback](https://wayback.freedesktop.org/)（[wayback-x11](https://aur.archlinux.org/packages/wayback-x11/)AUR、[wayback-x11-git](https://aur.archlinux.org/packages/wayback-x11-git/)AUR）是一个 X11 兼容层，允许使用 Wayland 组件运行完整的 X11 桌面环境，旨在最终取代 Xorg，从而减轻 X11 应用程序的维护负担。

### NVIDIA 驱动

> **注意：**470 版本之前的 NVIDIA 驱动（例如 [nvidia-390xx-dkms](https://aur.archlinux.org/packages/nvidia-390xx-dkms/)AUR）不支持硬件加速的 Xwayland，导致非 Wayland 原生应用程序在 Wayland 会话中性能不佳。

需要启用 [DRM KMS](../图形/NVIDIA.md#DRM_内核模式设置)。有关您的显示管理器（例如 [GDM](../GNOME/GDM.md#Wayland_和专有_NVIDIA_驱动)）的更多信息，请参阅[官方文档](https://download.nvidia.com/XFree86/Linux-x86_64/515.48.07/README/xwayland.html)。

### 检测应用程序是否通过 Xwayland 运行

使用 [xorg-xeyes](https://archlinux.org/packages/?name=xorg-xeyes)包 的 `xeyes` 可以确定应用程序是否通过 Xwayland 运行，如果鼠标指针在 Xwayland 程序上，xeyes 窗口中的眼珠会转向鼠标指针。

或使用 [xorg-xwininfo](https://archlinux.org/packages/?name=xorg-xwininfo)包 的 `xwininfo`。如果鼠标指针在 Xwayland 程序上，鼠标指针会变成“＋”。此时点击窗口，它会输出窗口信息并终止。

或使用 [xorg-xlsclients](https://archlinux.org/packages/?name=xorg-xlsclients)包 的 `xlsclients`。运行 `xlsclients -l` 可列出所有正运行在 Xwayland 的应用程序。

或者也可以使用 [extramaus](https://aur.archlinux.org/packages/extramaus/)AUR。如果鼠标指针在 Xwayland 程序上，红色指针会随之移动。

> **提示：**对于 KDE Plasma，还可以使用 [KWin 调试控制台](../KDE/KDE.md#KWin_debug_console)检查窗口。

## 图形库

### GTK

[gtk3](https://archlinux.org/packages/?name=gtk3)包 和 [gtk4](https://archlinux.org/packages/?name=gtk4)包 包已经提供了 Wayland 支持。GTK 默认使用 Wayland 后端，但是可以通过修改环境变量为 `GDK_BACKEND=x11` 覆盖默认设置切换到 Xwayland。

关于主题问题，请参阅 [GTK#Wayland_后端](../../07_开发与工具/控件工具集/GTK.md#Wayland_后端)。

### Qt

要在 [Qt](../../07_开发与工具/控件工具集/Qt.md) 5 中启用 Wayland 支持，请安装 [qt5-wayland](https://archlinux.org/packages/?name=qt5-wayland)包。Qt 5 应用程序就会在 Wayland 会话下运行。

虽然通常不需要，但若要显式使用 Wayland 插件运行 Qt 应用程序 ，请使用 `-platform wayland` 或 `QT_QPA_PLATFORM=wayland` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)。

要在 Wayland 会话中强制使用 [X11](../X%20服务器/Xorg.md)，请使用 `QT_QPA_PLATFORM=xcb`。

这对于某些不使用系统 Qt 实现的应用可能是必需的。`QT_QPA_PLATFORM="wayland;xcb"` 允许 Qt 在 Wayland 不可用时使用 xcb (X11) 插件 。

在某些混成器，例如sway，Qt 应用原生运行可能会有功能缺失。例如， KeepassXC 将无法最小化到托盘。在运行前安装qt5ct并设置`QT_QPA_PLATFORMTHEME=qt5ct`即可解决此问题。

由于 [Qt WebEngine 在 Wayland 上使用分数缩放时出现错误尺寸和文本渲染问题](https://bugreports.qt.io/browse/QTBUG-113574)的 Qt WebEngine 错误，使用 Qt WebEngine 的应用程序（例如 [Calibre](https://bugs.launchpad.net/calibre/+bug/2018658)）可能会显示锯齿字体。一个解决方法是启动应用程序时设置 `QT_SCALE_FACTOR_ROUNDING_POLICY=RoundPreferFloor`，这可以防止应用程序窗口进行分数缩放。

### Clutter

Clutter 工具包有 Wayland 后端支持，可以作为 Wayland 客户端运行。此后端支持已在 [clutter](https://aur.archlinux.org/packages/clutter/)AUR 中启用。

要在 Wayland 上运行 Clutter 应用，请设置 `CLUTTER_BACKEND=wayland` 环境变量。

### SDL

在 [SDL](../../07_开发与工具/开发/SDL.md)3 中，如果混成器支持 [fifo-v1 协议](https://wayland.app/protocols/fifo-v1)，则默认使用 Wayland 。否则，会先尝试 X11。可以将 `SDL_VIDEO_DRIVER` 环境变量设置为 `x11` 或 `wayland` 来强制使用其一（或者，使用优先级较低的下述 SDL2 环境变量）。

[sdl2-compat](https://archlinux.org/packages/?name=sdl2-compat)包 遵循上述 SDL3 规则，但有[特定应用程序的例外](https://github.com/libsdl-org/sdl2-compat/blob/8b85f95bbcc3411c78c1f2cbd62b23b7f96093c2/src/sdl2_compat.c#L504-L538)。至于 SDL2 本身（例如 [sdl2](https://aur.archlinux.org/packages/sdl2/)AUR），请设置 `SDL_VIDEODRIVER=wayland`。`SDL_VIDEODRIVER="wayland,x11"` 允许 SDL2 在 Wayland 不可用时使用 X11 。可能还需要安装 [libdecor](https://archlinux.org/packages/?name=libdecor)包 才能启用窗口装饰（例如在 GNOME 上）。

参见[官方说明](https://wiki.libsdl.org/SDL3/README-wayland)。

### GLFW

[glfw](https://archlinux.org/packages/?name=glfw)包 支持 Wayland，如果[环境变量](../../02_系统管理与内核/系统管理/环境变量.md) `XDG_SESSION_TYPE` 设置为 `wayland`，并且应用程序开发人员没有设置特定的后端，则使用 Wayland 后端。

查看[源代码](https://github.com/glfw/glfw/blob/3.4/src/platform.c#L87-L99)以获取更多信息。

### GLEW

如果 [glew-wayland-git](https://aur.archlinux.org/packages/glew-wayland-git/)AUR 包无法与所需的 GLEW 应用程序一起使用，则可以使用 [glew](https://archlinux.org/packages/?name=glew)包 和 Xwayland。参见 [FS#62713](https://bugs.archlinux.org/task/62713)。

### EFL

Enlightenment 已[完全支持 Wayland](https://www.enlightenment.org/about-wayland)。

要在 Wayland 上运行基于 EFL 的应用程序，请设置 `ELM_DISPLAY=wl`。

### winit

winit 是 Rust 语言中的窗口处理库。默认使用 Wayland 后端，但可以通过修改环境变量将其切换到 Xwayland：

- 在 0.29.2 版本之前，设置 `WINIT_UNIX_BACKEND=x11`
- 对于 0.29.2 及更高版本，取消设置 `WAYLAND_DISPLAY`，这将强制回退到使用 `DISPLAY` 变量的 X11 

### Electron

> **注意：**在 [Plasma](../KDE/KDE.md) 中，某些 Electron 应用程序可能在窗口上使用错误的图标（默认的 Wayland 图标），而在任务栏上使用正确的图标。使用特殊应用程序/窗口规则强制指定“桌面文件名”（即 `.desktop` 文件的文件名）可以绕过此问题。

使用命令行标志或环境变量可启用 Electron 的 Wayland 支持。

#### 命令行标志

> **注意：**某些应用程序不会将标志转发给 Electron，需要应用程序开发者提供解决方案。

关于启用 Electron Wayland 支持的命令行标志，请参见 [Chromium#原生 Wayland 上运行](../../06_软件与应用/Google/Chromium.md#原生_Wayland_上运行)。但需要注意，命令行标志 `--ozone-platform-hint=auto` 自 Electron 38 起已不再有效。

这些标志可以手动传递或[#通过配置文件添加](#通过配置文件添加)。通过[编辑 .desktop 文件](../../01_安装与包管理/配置文件/桌面项.md#编辑_.desktop_文件)可以每次通过桌面项启动时传递。

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 旧版本的 Electron 需要 `--enable-features=WebRTCPipeWireCapturer`，但它从哪个版本开始默认启用？此外，非自由软件捆绑的 Electron 的默认行为可能是错误的。（在 [en:Talk:Wayland](https://wiki.archlinux.org/title/Talk:Wayland) 中讨论）

Electron 默认启用通过 PipeWire 进行的 WebRTC 屏幕捕获。该捕获基于 [xdg-desktop-portal](https://archlinux.org/packages/?name=xdg-desktop-portal)包。

顶部栏缺失的问题可以通过使用 `--enable-features=WaylandWindowDecorations` 来解决。这在 [GNOME](../GNOME/GNOME.md) 下通常是必需的（自 [electron17](https://github.com/electron/electron/pull/29618) 起支持）。

##### 通过配置文件添加

Electron 读取 `~/.config/electron*XX*-flags.conf`（*XX* 为 Electron 版本号）文件添加到命令行参数（一行一个），如果版本特定的文件不存在，则回退到全版本的 `~/.config/electron-flags.conf`。

例如，将上述参数写入共享配置文件：

```
~/.config/electron-flags.conf
```

```
--enable-features=WaylandWindowDecorations
--ozone-platform-hint=auto
```

> **注意：**这些配置文件仅对官方仓库的 Electron 软件包和使用这些 Electron 包的应用程序有效。对于自带 Electron 的软件包（如 [slack-desktop](https://aur.archlinux.org/packages/slack-desktop/)AUR）来说，这些配置文件是无效的。不过在某些情况下也有替代品，如 [slack-electron](https://aur.archlinux.org/packages/slack-electron/)AUR。

#### 环境变量

使用 Electron 28 至 37 版本的应用程序可以使用[环境变量](../../02_系统管理与内核/系统管理/环境变量.md) `ELECTRON_OZONE_PLATFORM_HINT`，将其设置为 `auto` 或 `wayland`。

如果同时指定，命令行标志优先。

### Java

[Java](../../06_软件与应用/Oracle/Java.md) 平台的开源实现 OpenJDK 尚未原生支持 Wayland。在 [Wakefield](https://openjdk.java.net/projects/wakefield/) 项目（旨在在 OpenJDK 中实现 Wayland）可用之前，可以使用 Xwayland。

参见 [Debian:Wayland#Java Programs (supported since OpenJDK 16?)](https://wiki.debian.org/Wayland#Java_Programs_.28supported_since_OpenJDK_16.3F.29)：

: 从 OpenJDK 16 开始，JRE 可以动态加载 GTK3（支持 Wayland），根据此[讨论](https://stackoverflow.com/questions/39197208/java-gui-support-on-wayland)，这似乎是支持的。
: 可以设置 `_JAVA_AWT_WM_NONREPARENTING` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)为 "1" 以修复应用程序启动时出现空白屏幕的问题。

由于 Xwayland 没有与 Wayland 完全的功能对等，[WLToolkit](https://wiki.openjdk.org/display/wakefield/Pure+Wayland+toolkit+prototype) 可以在 Wakefield 未准备好时填补空白。可以通过 `-Dawt.toolkit.name=WLToolkit` 激活。一些程序支持它。

## 输入法

### Fcitx 5

参见 [Fcitx 5#Wayland](../输入法/Fcitx%205.md#Wayland)。

### IBus

参见 。

## 提示与技巧

### 自动化

- [ydotool](https://github.com/ReimuNotMoe/ydotool) ([ydotool](https://archlinux.org/packages/?name=ydotool)包) - 通用的命令行自动化工具 (不限于 Wayland)。启用/启动 `ydotool.service` [用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)。参见 [ydotoold(8)](https://man.archlinux.org/man/ydotoold.8)，[ydotool(1)](https://man.archlinux.org/man/ydotool.1)。
- [wtype](https://github.com/atx/wtype) ([wtype](https://archlinux.org/packages/?name=wtype)包) - 适用 Wayland 的 xdotool type。参见 [wtype(1)](https://man.archlinux.org/man/wtype.1)。
- [keyboard](https://github.com/boppreh/keyboard) - 可在 Windows 和 Linux 上稳定工作、实验性支持 OS X 的 Python 库。另请参见[鼠标 (mouse)](https://github.com/boppreh/mouse) 库。
- [wlrctl](https://git.sr.ht/~brocellous/wlrctl) ([wlrctl](https://aur.archlinux.org/packages/wlrctl/)AUR) - 用于各种 wlroots 扩展的命令行工具（支持 foreign-toplevel-management、virtual-keyboard、virtual-pointer）。

### 重新映射键盘或鼠标按键

参见[输入重映射工具](../../04_硬件与外设/键盘配置/输入重映射工具.md)。

### 录制

参见[屏幕捕获#屏幕录制](../../06_软件与应用/多媒体/屏幕捕获.md#屏幕录制)和[屏幕捕获#通过X11应用程序录制Wayland窗口](../../06_软件与应用/多媒体/屏幕捕获.md#通过X11应用程序录制Wayland窗口)。

### 应用程序关闭后保留剪贴板内容

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[剪贴板](../../06_软件与应用/软件列表/剪贴板.md)。**
>
> **附注：** 这是 Xorg 上的标准行为。还有许多其他剪贴板管理器。（在 [Talk:Wayland](https://wiki.archlinuxcn.org/wiki/Talk:Wayland) 中讨论）

由于 Wayland 的设计理念，剪贴板数据存储在源客户端的内存中。当客户端关闭时，剪贴板数据将丢失。您可以使用 [wl-clip-persist](https://archlinux.org/packages/?name=wl-clip-persist)包 来解决这个问题，它在后台运行以读取剪贴板数据并将其存储在自己的内存中，与源客户端分离。

### 将混成器作为 systemd 服务自动启动

> **提示：**[通用Wayland会话管理器](通用Wayland会话管理器.md)可自动为混成器生成 systemd 单元，此外还可用于[将图形应用程序与 systemd 集成](通用Wayland会话管理器.md#通用Wayland会话管理器#软件与自动启动)。

如果不想使用[显示管理器](../图形用户界面/显示管理器.md)或 [shell](../../07_开发与工具/命令行解释器/命令行解释器.md)，可以通过 [systemd](../../01_安装与包管理/初始化/Systemd.md) 自动启动 Wayland 混成器。调整 `ExecStart` 行启动指定混成器。以下是 [KDE Plasma](../KDE/KDE.md) 的示例:

```
/etc/systemd/system/wayland-compositor.service
```

```
[Unit]
After=graphical.target systemd-user-sessions.service modprobe@drm.service
Conflicts=getty@tty1.service
[Service]
User=username
WorkingDirectory=~
PAMName=login
TTYPath=/dev/tty1
UnsetEnvironment=TERM
StandardOutput=journal
ExecStart=/usr/lib/plasma-dbus-run-session-if-needed /usr/bin/startplasma-wayland
[Install]
WantedBy=graphical.target
```

### 在基于 wlroots 的混成器上使用另一个渲染器

您可以通过为基于 wlroots 的混成器设定 `WLR_RENDERER` 环境变量以使用另一个 [wlroots 渲染器](https://gitlab.freedesktop.org/wlroots/wlroots/-/tree/master/render)如 vulkan。请参阅 [wlroots 文档](https://gitlab.freedesktop.org/wlroots/wlroots/-/blob/master/docs/env_vars.md)以查找可用的渲染器。

## 故障排除

### 颜色校正

请参阅[背光#色彩校正](../../04_硬件与外设/笔记本电脑/背光.md#色彩校正)。

### 慢动作、图形显示故障和崩溃

Gnome-shell 用户从 X 切换到 Wayland 时可能会遇到此问题。根本原因之一是可能用户自己为基于 Xorg 的 gnome-shell 设置了 `CLUTTER_PAINT=disable-clipped-redraws:disable-culling` 变量。只需尝试将变量从 `/etc/environment` 或其它 rc 文件中移除，即可查看是否一切恢复正常。

### 远程显示

- [sway](../Wayland%20合成器/Sway.md) 使用的[wlroots0.18](https://archlinux.org/packages/?name=wlroots0.18)包 和[wlroots0.19](https://archlinux.org/packages/?name=wlroots0.19)包 从 0.10 版本开始通过 [wayvnc](https://archlinux.org/packages/?name=wayvnc)包 提供了一个 VNC 后端。RDP 后端则已被移除。
- 目前 [mutter](https://archlinux.org/packages/?name=mutter)包 在编译时就启用了远程桌面功能，详情请参阅  和 [gnome-remote-desktop](https://archlinux.org/packages/?name=gnome-remote-desktop)包。
- [krfb](https://archlinux.org/packages/?name=krfb)包 为 [kwin](https://archlinux.org/packages/?name=kwin)包 提供了一个 VNC 服务器。`krfb-virtualmonitor` 可用于将另一台设备设置为额外的显示器。
- 在 2013 年 Weston 合并了对 FreeRDP 的支持，可通过编译标志 (compile flag) 启用。[weston](https://archlinux.org/packages/?name=weston)包 自 6.0.0 版本开始启用了 FreeRDP。
- [waypipe](https://archlinux.org/packages/?name=waypipe)包 (或 [waypipe-git](https://aur.archlinux.org/packages/waypipe-git/)AUR) 是适用于 Wayland 应用的透明代理，可通过封装的命令在 [SSH](../../03_网络与服务/SSH/安全外壳协议.md) 上运行。
    - 以下是一个在 Plasma 下启动远程 KDE kcalc 的示例：

: : ```
$ waypipe ssh example.local env QT_QPA_PLATFORM=wayland QT_QPA_PLATFORMTHEME=KDE dbus-launch kcalc
```

### 游戏、远程桌面和虚拟机窗口中的输入捕获问题

与 Xorg 不同的是，Wayland 并不允许独占输入设备捕获 (也被称为主动捕获或显式捕获，比如[键盘](https://tronche.com/gui/x/xlib/input/XGrabKeyboard.html)、[鼠标](https://tronche.com/gui/x/xlib/input/XGrabPointer.html)等设备)。Wayland 依赖混成器传递键盘快捷键，并将指针设备限制在应用窗口中。

输入捕获方式的变化破坏了当前应用程序的行为，意味着：

- 热键组合和修饰符输入会被混成器捕获，并且不会发送到远程桌面和虚拟机窗口中。
- 鼠标指针将不会被限制在应用程序的窗口中，这可能会导致视差效应，即虚拟机或远程桌面的窗口内鼠标指针的位置与主机的鼠标指针发生偏差。

Wayland 通过为 Wayland 和 Xwayland 添加协议扩展来解决此问题。为此 Wayland 混成器需要添加对这些扩展的支持。如果是本地 Wayland 客户端，其使用的部件工具集 (widget toolkits，比如 GTK，Qt) 需要支持这些插件；如果没有使用部件工具集，则需要支持应用程序本身。如果是 Xorg 应用程序，则不需要改变应用程序或者部件工具集，因为 Xwayland 的支持就足够了。

[wayland-protocols](https://archlinux.org/packages/?name=wayland-protocols)包 中已经包含了这些扩展, 并由 [xorg-xwayland](https://archlinux.org/packages/?name=xorg-xwayland)包 支持。

相关的扩展有：

- [Xwayland 键盘捕获协议](https://gitlab.freedesktop.org/wayland/wayland-protocols/-/blob/main/unstable/xwayland-keyboard-grab/xwayland-keyboard-grab-unstable-v1.xml)
- [混成器快捷键禁止协议](https://gitlab.freedesktop.org/wayland/wayland-protocols/-/blob/main/unstable/keyboard-shortcuts-inhibit/keyboard-shortcuts-inhibit-unstable-v1.xml)
- [相对指针协议](https://gitlab.freedesktop.org/wayland/wayland-protocols/-/blob/main/unstable/relative-pointer/relative-pointer-unstable-v1.xml)
- [指针约束协议](https://gitlab.freedesktop.org/wayland/wayland-protocols/-/blob/main/unstable/pointer-constraints/pointer-constraints-unstable-v1.xml)

支持的 Wayland 混成器有：

- Mutter，[GNOME](../GNOME/GNOME.md) 的混成器 (窗口管理器) ，[从 3.28 版本](https://bugzilla.gnome.org/show_bug.cgi?id=783342)开始支持
- wlroots 支持相对指针协议和指针约束协议
- Kwin
    - [KDE#X11快捷键在Wayland上冲突](../KDE/KDE.md#X11快捷键在Wayland上冲突)
    - [键盘快捷键禁止](https://invent.kde.org/plasma/kwin/-/blob/master/src/wayland/keyboard_shortcuts_inhibit_v1_interface.cpp)

支持的部件工具集有：

- GTK，从 3.22.18 版本开始支持。

### GTK 主题没有生效

请查看 [https://github.com/swaywm/sway/wiki/GTK-3-settings-on-Wayland](https://github.com/swaywm/sway/wiki/GTK-3-settings-on-Wayland).

### 避免加载 NVIDIA 模块

在 Wayland 混成器（如 [sway](../Wayland%20合成器/Sway.md)）启动之前，将 `__EGL_VENDOR_LIBRARY_FILENAMES=/usr/share/glvnd/egl_vendor.d/50_mesa.json` 添加到系统[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)中。

### 放大/表面缩放

屏幕放大问题尚未解决，2022 年中合并了一个拉取请求[提供了 wp-surface-scale 协议](https://gitlab.freedesktop.org/wayland/wayland-protocols/-/merge_requests/145)。

### Wayland 卡顿/延迟（自内核 6.11.2，AMD）

在此问题在未来的内核版本中修复之前，一个临时解决方案是在 cmdline 中添加 `amdgpu.dcdebugmask=0x400`。

参见：[https://community.frame.work/t/wayland-lag-stuttering-since-kernel-6-11-2/59422](https://community.frame.work/t/wayland-lag-stuttering-since-kernel-6-11-2/59422)

### 切换工作区/虚拟桌面时游戏/应用程序被挂起


当切换工作区/虚拟桌面或使用 `Alt+Tab` 时，游戏（以及可能的其他图形应用程序）会被挂起，进入某种奇怪的状态，并（部分）停止运行。这包括 VRR 应用程序和开启了 VSync 的应用程序，但可能不仅限于此。症状包括音频（部分）中断、游戏无法进行、ping 大幅上升或掉线，但仅在游戏窗口未处于焦点时才会出现。

某些游戏可能可以通过切换到窗口模式来规避此问题，但有些则不行。这在需要大量使用网页浏览、文档和第三方工具的更复杂游戏中极其烦人，或者当游戏因某种原因被中断时也是如此。

可能的解决方法包括设置环境变量 `MESA_VK_WSI_PRESENT_MODE=immediate` 和/或 `vk_xwayland_wait_ready=false`，但设置这些会破坏所有 VSync 或 VRR 实现。

## 另见

- [Wayland 在线文档](https://wayland.freedesktop.org/docs/html/)（英文）
- [官方仓库](https://gitlab.freedesktop.org/wayland)（英文）
- [Fedora：如何排查 Wayland 问题](https://fedoraproject.org/wiki/How_to_debug_Wayland_problems)（英文）
- [我们现在是 Wayland 了！](https://wearewaylandnow.com/)（英文）- "Wayland 可用了吗？"的更新版本
- [Wayland 项目汇总](https://awesomeopensource.com/projects/wayland)（英文）
- [光标主题](../X%20服务器/光标主题.md)
- [Arch Linux 论坛讨论](https://bbs.archlinux.org/viewtopic.php?id=107499)（英文）（页面将持续关注 Wayland 信息，如有兴趣请留意）
- [i3 迁移指南 - i3 上常用 X11 应用的 Wayland 替代品](https://github.com/swaywm/sway/wiki/i3-Migration-Guide#common-x11-apps-used-on-i3-with-wayland-alternatives)（英文）
- [Wayland Explorer - 阅读 Wayland 文档的更好方法](https://wayland.app/protocols/)（英文）
- [如何判断应用程序是否在使用 Xwayland](https://askubuntu.com/questions/1393618/how-can-i-tell-if-an-application-is-using-xwayland)（英文）
