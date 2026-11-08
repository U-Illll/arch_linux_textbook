# 第 5 章　Wayland 合成器

> 把「装个桌面」落到具体的合成器，是这一章要解决的事：安装、启动、配置、排错，八节各有可照做的路径与边界。
> 
> 读法不必平均。Sway 与 Hyprland 必读，篇幅最长、生态最全，任选其一跟做即可走通主路径，另一节作对照；想看合成器机制，翻 Weston。Wayfire、MangoWM 篇幅短，浏览即可；Hyprlock 只是 Hyprland 的锁屏工具，不是合成器；Niri 与 Dwl 按场景取阅，后者配置思路不同，不宜照搬。最常踩的坑是把一家的配置套给另一家：语法与生态互不通用，先跑通一个再动第二个。

<!-- 本章 8 节，共 113 KB -->

## 5.1　Wayfire

*难度：入门 ｜ 篇幅：2.1 KB ｜ 所属：Wayland 合成器*

将要讲到：安装、Starting、配置、参见

[Wayfire](https://wayfire.org/) 是一个 3D [wayland](../Wayland/Wayland.md) 混成器，灵感来自于 [Compiz](../堆叠式窗口管理器/Compiz.md)，基于 wlroots 开发。它致力于在不牺牲其外观的前提下创造一个可自定义、可扩展、轻量级的环境。

## 安装

Wayfire 可以由 [wayfire](https://archlinux.org/packages/?name=wayfire)包 安装。安装 [wf-shell](https://archlinux.org/packages/?name=wf-shell)包 以添加任务栏和墙纸，安装 [wcm](https://archlinux.org/packages/?name=wcm)包 以添加 GUI 配置程序。

为了获得更好的使用体验，下列包也被推荐使用：

- [mako](https://archlinux.org/packages/?name=mako)包：通知
- [kanshi](https://archlinux.org/packages/?name=kanshi)包：多显示器支持
- [swayidle](https://archlinux.org/packages/?name=swayidle)包 和 [swaylock](https://archlinux.org/packages/?name=swaylock)包：屏幕锁定

## Starting

Wayfire 可以由一个[显示管理器](../图形用户界面/显示管理器.md)或以下代码启动：

```
$ wayfire
```

要使用特定的控制文件：

```
$ wayfire -c /path/to/config.ini
```

## 配置

下载默认[配置文件](https://github.com/WayfireWM/wayfire/blob/master/wayfire.ini/)，并保存为 `~/.config/wayfire.ini` 以开始配置。如果你安装了 [wcm](https://archlinux.org/packages/?name=wcm)包，那么一切都可以在 GUI 下配置，否则你将不得不用文本编辑器手动编辑配置文件。

## 参见

- [Wayfire 官方 wiki](https://github.com/WayfireWM/wayfire/wiki/Tutorial/)

## 5.2　Hyprlock

*难度：入门 ｜ 篇幅：2.2 KB ｜ 所属：Wayland 合成器*

将要讲到：安装、配置

[Hyprlock](https://wiki.hypr.land/Hypr-Ecosystem/hyprlock/) 是 [Hyprland](Hyprland.md) 的锁屏工具，可高度自定义。

## 安装

安装 [hyprlock](https://archlinux.org/packages/?name=hyprlock)包

## 配置

配置可使用在`~/.config/hypr`的 [hyprlock.conf](https://github.com/hyprwm/hyprlock/blob/main/assets/example.conf) 文件。

> **注意：**Hyprlock不会在安装过程中自动生成一个配置文件，如果缺少配置文件，它将不会运行。

### 组件

Hyprlock 工作在一个基于[组件](https://wiki.hypr.land/Hypr-Ecosystem/hyprlock/#widget-list)的系统。你可以通过如下格式创建组件：

```
~/.config/hypr/hyprlock.conf
```

```
widget {
  property = value
  ...
}
```

> **注意：**多数配置可以放置在hyprlock.conf文件中，如：
>
> ```
> ~/.config/hypr/hyprlock.conf
> ```
>
> ```
> label {
> ...
> }
> label {
> ...
> }
> input-field {
> ...
> }
> ```

#### 示例

设置 Hyprlock 背景：

```
~/.config/hypr/hyprlock.conf
```

```
background {
  monitor = # monitor-agnostic
  path = ~/Pictures/mountain.jpg
  blur_passes = 1
  blur_size = 7
}
```

密码输入框：

```
~/.config/hypr/hyprlock.conf
```

```
input-field {
  monitor =
  size = 20%, 5%
  font_family = Noto Sans
  dots_size = 0.08
}
```

显示时间：

```
~/.config/hypr/hyprlock.conf
```

```
label {
  monitor =
  text = $TIME
  font_size = 100
  position = 0, -80
}
```

在`text`后的`$TIME`可被替换为任意文本，`$USER`，`$FAIL`和`$TIME12`属于[其他](https://wiki.hypr.land/Hypr-Ecosystem/hyprlock/#variable-substitution)。

## 5.3　MangoWM

*难度：入门 ｜ 篇幅：2.8 KB ｜ 所属：Wayland 合成器*

将要讲到：安装、启动、配置、故障排除、参见

[Mango](https://mangowm.github.io) 是一个现代、轻量且高性能的平铺式 Wayland 合成器，基于 [dwl](Dwl.md) 构建 — 专为速度、灵活性以及可定制的桌面体验而设计。

## 安装

安装 [mangowm](https://archlinux.org/packages/?name=mangowm)包 软件包。

## 启动

在显示管理器的菜单中选择 *Mango* 或者在 [tty](../../07_开发与工具/Linux%20控制台/Getty.md) 中运行 `mango`。

### LXQT

参见 [LXQt#Wayland 会话](../桌面环境/LXQt.md#Wayland_会话)

## 配置

系统级配置文件是 `/etc/mango/config.conf`，用户级配置文件是 `~/.config/mango/config.conf`。

### 创建配置文件

首先，运行下面命令来创建接下来需要的目录：

```
$ mkdir -p ~/.config/mango/
```

每次启动时，mango 都会先尝试使用 `~/.config/mango/config.conf` 中包含的所有自定义内容，然后才回退到 `/etc/mango/config.conf`。用户级配置文件不会被默认创建，所以要通过下面命令复制模板文件：

```
$ cp /etc/mango/config.conf ~/.config/mango/
```

### 自动启动

可以在配置文件中添加 `exec_once` 或 `exec` 命令来在启动时运行命令或脚本，例如：

```
exec_once=waybar

exec=bash ~/.config/mango/reload-settings.sh
```

`exec_once` 命令只在 mango 启动时会运行一次，而 `exec` 命令在每一次重加载配置时均会运行。

### 屏幕共享

安装 [xdg-desktop-portal-wlr](https://archlinux.org/packages/?name=xdg-desktop-portal-wlr)包 软件包以启动 [PipeWire](../../06_软件与应用/多媒体/PipeWire.md) 的屏幕共享 若门户未自动启动，尝试手动执行 `/usr/lib/xdg-desktop-portal-wlr`。

## 故障排除

### 修复运行异常的 Java 应用程序

参见 [Java#灰色窗口/应用不随窗口管理器调整大小/菜单自动关闭](../../06_软件与应用/Oracle/Java.md#灰色窗口/应用不随窗口管理器调整大小/菜单自动关闭)。

## 参见

- [bemenu](https://github.com/Cloudef/bemenu) - [dmenu](../Suckless/Dmenu.md) 的一个原生替代方案
- 一个 *mango* 的 [介绍](https://mangowm.github.io/docs/)
- *Mango* 的 [git](../../07_开发与工具/版本控制系统/Git.md) [仓库](https://github.com/mangowm/mango)

## 5.4　Niri

*难度：进阶 ｜ 篇幅：7.4 KB ｜ 所属：Wayland 合成器*

将要讲到：安装、启动、配置、另见

Niri 是一个可滚动的平铺式 [Wayland](../Wayland/Wayland.md) 合成器。与 [Sway](Sway.md) 或 [Hyprland](Hyprland.md) 不同的地方在于，Niri 将窗口排列在一个无限延伸的水平桌面上，你可以向左或向右滚动（当然也可以实现更复杂的布局）。它类似于 [GNOME](../GNOME/GNOME.md) 的 PaperWM 和 [KDE](../KDE/KDE.md) 的 Karousel。

## 安装

可以通过安装 [niri](https://archlinux.org/packages/?name=niri)包 来获取 Niri。

此外，为了获得更好的使用体验，你可能还需要安装以下软件：

- **[fuzzel](https://archlinux.org/packages/?name=fuzzel)**：Niri 的默认应用启动器
- **[mako](https://archlinux.org/packages/?name=mako)**：通知管理器
- **[waybar](https://wiki.archlinux.org/title/Waybar)**：Wayland 状态栏
- **[xdg-desktop-portal-gtk](https://archlinux.org/packages/?name=xdg-desktop-portal-gtk)**、**[xdg-desktop-portal-gnome](https://archlinux.org/packages/?name=xdg-desktop-portal-gnome)**：用于实现屏幕共享功能
- **[alacritty](../../07_开发与工具/终端模拟器/Alacritty.md)**：Niri 的默认终端
- **[swaybg](https://archlinux.org/packages/?name=swaybg)**：设置桌面背景图片
- **[swayidle](https://archlinux.org/packages/?name=swayidle)**、**[swaylock](https://archlinux.org/packages/?name=swaylock)**：用于在空闲时锁定屏幕
- **[xwayland-satellite](https://archlinux.org/packages/?name=xwayland-satellite)**：用于运行 X11 应用程序
- **[udiskie](https://archlinux.org/packages/?name=udiskie)**：用于管理和自动挂载 USB 驱动器

## 启动

Niri 附带了一个可以被[显示管理器](../图形用户界面/显示管理器.md)执行的 [桌面项](../../01_安装与包管理/配置文件/桌面项.md)。在显示管理器中选择它将会启动 `niri-session`。`niri-session` 会把 [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)导入至[systemd](../../01_安装与包管理/初始化/Systemd.md).

你也可以从 [getty](../../07_开发与工具/Linux%20控制台/Getty.md) 执行:

```
niri-session
```

这可以结合[Getty#自动登录到虚拟控制台](../../07_开发与工具/Linux%20控制台/Getty.md#自动登录到虚拟控制台)来实现一个无缝的[启动](../../01_安装与包管理/启动过程/Arch%20的启动流程.md)体验。

## 配置

Niri 从 `~/.config/niri/config.kdl` 读取配置文件。它是一个有多个部分的 KDL 文件。首次启动创建的配置文件通过注释给出了默认的选项设置。然而，随着更新所引入的新选项并不会在用户的配置中所体现，你可能需要查看 [Niri 官方文档](https://yalter.github.io/niri/Configuration%3A-Introduction.html)。

Niri 在配置文件保存时会自动应用更改。无效的配置在自动应用更改时并不会使 Niri 崩溃，而是会保留上一次可工作的状态，并提醒用户配置文件无效。 可以执行 `niri validate` 来在 Niri 会话外检查配置是否有效。

### 键盘布局

要配置键盘布局，编辑 `input/keyboard/xkb` 部分.

例如，假设你想要有一个 `CapsLock` 当作 `Ctrl` 的 "US Int Alt Gr" 布局：

```
~/.config/niri/config.kdl
```

```
input {
    keyboard {
        xkb {
            layout "us"
            variant "altgr-intl"
            options "ctrl:nocaps"
        }
    }
    ...
}
```

### 显示输出

首先执行 `niri msg outputs` 以获取所有 Niri 识别到的输出。然后你可以给每个显示器应用配置。例如设置 HDMI 显示器为 2560x1440 60Hz 并有 1.2 倍缩放，且关闭笔记本显示器，可以这样设置：

```
~/.config/niri/config.kdl
```

```
output "HDMI-A-1" {
    mode "2560x1440@60.000"
    scale 1.2
}

output "eDP-1" {
    off
}
```

#### 使用 kanshi 来动态设置布局

你也可以使用 [kanshi](../Wayland/Kanshi.md) 来动态设置布局，例如当你想要在连接至外部显示器时关闭内置显示器。

### 按键绑定

这一部分允许配置各种 Niri 可用的快捷键。很多已经在初次启动时生成的默认配置中配置好了。它们都可以重新改为其他按键。

请注意 Niri *不* 加载任何默认的绑定。若一个绑定没有在配置文件中指定，那么它就不会生效。“默认”只是在自动生成的配置文件中提供的。因此，在移除它们时请小心。建议使用注释来注释掉不需要的绑定。

按键绑定的设置要通过使用修饰键和`+`号以及在大括号内写上要执行的动作。'spawn' 这个动作会启动指定的程序。例如，你会有这样的按键绑定：`Mod+T` 启动 [alacritty](../../07_开发与工具/终端模拟器/Alacritty.md) 以及 `Mod+D` 启动 [fuzzel](https://archlinux.org/packages/?name=fuzzel)包。默认配置下直接运行 Niri 时 `Mod` 一般是 `Super` 键，当它在其他环境下作为一个窗口运行时则是 `Alt` 键。

```
~/.config/niri/config.kdl
```

```
binds {
    ...
    Mod+T { spawn "alacritty"; }
    Mod+D { spawn "fuzzel"; }
    ...
}
```

注意使用 `spawn` 时所有用空格分开的参数都需要用引号包裹：

```
~/.config/niri/config.kdl
```

```
binds {
    ...
    Mod+Ctrl+semicolon {
        spawn "swaylock" "-c" "121212" "-e" "-f" "-F"
    }
    ...
}
```

#### WASD-式操作

可以配置 Niri 的工作区、窗口切换快捷键类似于游戏中的 WASD 那样：

```
~/.config/niri/config.kdl
```

```
binds {
    ...
    Mod+A { focus-column-left; }
    Mod+S { focus-window-or-workspace-down; }
    Mod+W { focus-window-or-workspace-up; }
    Mod+D { focus-column-right; }
    ...
}
```

注意这可能也需要更改其他的绑定。同时，一些人可能会喜欢在右手侧配置，或是 Vim 式的操作。

### 自动启动

Niri 可以让某些程序随 Niri 一起启动。 例如上面提到的程序，比如 [mako](https://archlinux.org/packages/?name=mako)包, [waybar](https://archlinux.org/packages/?name=waybar)包 和 [swayidle](https://archlinux.org/packages/?name=swayidle)包/[swaylock](https://archlinux.org/packages/?name=swaylock)包 可以这样配置

```
~/.config/niri/config.kdl
```

```
spawn-at-startup "mako"
spawn-at-startup "waybar"
spawn-at-startup "swayidle" "-w" "timeout" "601" "niri msg action power-off-monitors" "timeout" "600" "swaylock -f" "before-sleep" "swaylock -f"
```

注意这些进程被绑定到了 Niri 的会话，使得当 Niri 退出**或**挂起时，它们也会被杀死。为使它们能持续运行，你可能需要通过添加 `"&"` 参数来把它们设置成后台任务。

### XWayland

Niri 不提供 XWayland 支持来运行 X11 应用。它推荐使用 [xwayland-satellite](https://archlinux.org/packages/?name=xwayland-satellite)包 这个外部工具来作为替代。这个包被列到了可选依赖中，安装它之后不需要做额外的配置。

## 另见

- [Niri's own wiki](https://yalter.github.io/niri/)

## 5.5　Dwl

*难度：进阶 ｜ 篇幅：9.4 KB ｜ 所属：Wayland 合成器*

将要讲到：安装、配置、用法、技巧和窍门、故障排除、参见

> **翻译状态：**
>
> - 本文（或部分内容）译自 [dwl](https://wiki.archlinux.org/title/dwl)，最近一次同步于 2026-07-26，若英文版本有所[更改](https://wiki.archlinux.org/title/dwl?diff=0&oldid=868002)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/dwl_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

*dwl* 是一个基于 [wlroots](https://gitlab.freedesktop.org/wlroots/wlroots/) 的 Wayland 合成器，在设计理念上与 [dwm](../动态窗口管理器/Dwm.md) 相似。它精简、快速，并且可以通过社区制作的 [补丁](https://codeberg.org/dwl/dwl-patches) 进行扩展。默认配置下仅提供最基本的窗口管理功能，足以日常使用；若需更多特性，可按需选用社区补丁。

## 安装

*dwl* 可以通过 [dwl](https://aur.archlinux.org/packages/dwl/)AUR 或 [dwl-git](https://aur.archlinux.org/packages/dwl-git/)AUR 软件包安装。请在**构建和安装**之前完成所需的[配置](#配置)修改，参见 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md)。

> **提示：**[上游构建说明](https://codeberg.org/dwl/dwl#building-dwl) 也可参考，但这种方式安装的文件不会被 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 跟踪。

## 配置

*dwl* 通过编辑部分源代码文件（主要是 `config.h`）在编译时进行配置。有关这些设置的详细信息，请参阅附带详细注释的 `config.def.h`。

官方网站提供了许多 [补丁](https://codeberg.org/dwl/dwl-patches)，可以为 *dwl* 添加额外功能。这些补丁主要修改 `dwl.c` 文件，并在适当的地方修改 `config.h` 文件。有关应用补丁的信息，参见文章 [给软件包打补丁](../../01_安装与包管理/包管理/软件包补丁.md)。

### 启用 XWayland

要启用 [Xwayland](../Wayland/Wayland.md) 支持构建 *dwl*，请安装 [xorg-xwayland](https://archlinux.org/packages/?name=xorg-xwayland)包 并将构建文件中的以下行取消注释：

```
/path/to/config.mk
```

```
# 取消注释以构建 XWayland 支持
XWAYLAND = -DXWAYLAND
XLIBS = xcb xcb-icccm
```

## 用法

*dwl* 可以从选择的[显示管理器](../图形用户界面/显示管理器.md)中启动，在其提供的菜单中选择对应的[桌面项](../../01_安装与包管理/配置文件/桌面项.md)。

要从 [getty](../../07_开发与工具/Linux%20控制台/Getty.md) 启动 *dwl*，执行如下命令：

```
$ dwl
```

使用 `-s` 启动的命令，其标准输入（stdin）会被 *dwl* 持续写入当前的布局、窗口标题、app-id 和标签等信息。如果您的命令不需要读取这些标准输入数据，为避免管道阻塞或产生意外错误，请在该命令后追加 `<&-` 以关闭其标准输入，例如：

```
$ dwl -s 'sh /path/to/autostart.sh <&-'
```

如果使用[显示管理器](../图形用户界面/显示管理器.md)，可以将此附加到[桌面项](../../01_安装与包管理/配置文件/桌面项.md)的 `Exec` 行中。

> **注意：**有关 *dwl* 使用的更详细信息，请参阅上游 [指南](https://codeberg.org/dwl/dwl#running-dwl)。

## 技巧和窍门

### 让某些窗口使用浮动布局

对于某些窗口，例如首选项对话框，平铺并不合适——它们应该自由浮动。例如，要让 Firefox 的首选项对话框浮动，请将以下内容添加到你的 rules 数组中：

```
/path/to/config.def.h
```

```
static const Rule rules[] = {
...
{ NULL, "Firefox Preferences", 1 << 8, 1, -1 },
...
};
```

### 屏幕捕获

下面的脚本会首先创建一个目录 `*/path/to/pics*`，如果成功，它会使用[屏幕捕获](../../06_软件与应用/多媒体/屏幕捕获.md)工具截屏并保存为以当前日期为文件名、扩展名为 `.png` 的文件，存放在该新建的目录中：

```
/path/to/scripts/screenshot.sh
```

```
#!/bin/sh
mkdir -p /path/to/pics && grim "/path/to/pics/$(date +'%m-%d-%Y-%H%M%S').png"
```

此外，如果你还想使用选择框截屏而不是截取所有显示器，请创建以下脚本：

```
/path/to/scripts/screenshotsel.sh
```

```
#!/bin/sh
mkdir -p /path/to/pics && grim -g "$(slurp)" "/path/to/pics/$(date +'%m-%d-%Y-%H%M%S').png"
```

如果要截屏并保存到[剪贴板](../../06_软件与应用/软件列表/剪贴板.md)而不是文件系统，请创建以下脚本：

```
/path/to/scripts/screenshotselcopy.sh
```

```
#!/bin/sh
grim -g "$(slurp)" - | wl-copy
```

最后添加以下内容：

```
/path/to/config.def.h
```

```
static const Key keys[] = {
...
{ 0, XKB_KEY_Print, spawn, SHCMD("path/to/scripts/screenshot.sh") },
{ WLR_MODIFIER_SHIFT, XKB_KEY_Print, spawn, SHCMD("path/to/scripts/screenshotsel.sh") },
{ WLR_MODIFIER_CTRL, XKB_KEY_Print, spawn, SHCMD("path/to/scripts/screenshotselcopy.sh") },
...
};
```

这将[屏幕捕获](../../06_软件与应用/多媒体/屏幕捕获.md)映射到 `Print` 键，选择框截屏映射到 `Shift+Print`，而截屏并复制到[剪贴板](../../06_软件与应用/软件列表/剪贴板.md)映射到 `Ctrl+Print`。

> **注意：**
>
> - 如果在 *dwl* 构建中打了层动画补丁，可能[需要](https://github.com/emersion/slurp/issues/84)为 slurp 禁用这些动画，因为 *grim* 最终会同时捕获选择器边框。
> - 以上所有脚本都需要可执行权限，请确保已设置。此外，它们有一些依赖，请安装 [grim](https://archlinux.org/packages/?name=grim)包、[slurp](https://archlinux.org/packages/?name=slurp)包 和 [wl-clipboard](https://archlinux.org/packages/?name=wl-clipboard)包 才能正常工作。

### 调整音量

安装 [pipewire-pulse](https://archlinux.org/packages/?name=pipewire-pulse)包 或 [pulseaudio](https://archlinux.org/packages/?name=pulseaudio)包，然后添加以下音量管理选项：

```
/path/to/config.def.h
```

```
static const char *up_vol[]   = { "pactl", "set-sink-volume", "@DEFAULT_SINK@", "+10%",   NULL };
static const char *down_vol[] = { "pactl", "set-sink-volume", "@DEFAULT_SINK@", "-10%",   NULL };
static const char *mute_vol[] = { "pactl", "set-sink-mute",   "@DEFAULT_SINK@", "toggle", NULL };

static const Key keys[] = {
...
{ 0, XKB_KEY_XF86AudioRaiseVolume, spawn, {.v = up_vol } },
{ 0, XKB_KEY_XF86AudioLowerVolume, spawn, {.v = down_vol } },
{ 0, XKB_KEY_XF86AudioMute, spawn, {.v = mute_vol } },
...
};
```

### 自启动

有一个 [补丁](https://codeberg.org/dwl/dwl-patches/src/branch/main/patches/autostart) 可用，它允许你自启动指定的应用程序。示例如下：

```
/path/to/config.def.h
```

```
/* 自启动 */
static const char *const autostart[] = {
    "wbg", "/path/to/your/image", NULL,
    NULL /* 终止 */
};
```

你可以通过向列表中添加更多条目来指定更多应用程序，每个条目用逗号（,）分隔，后跟 `NULL`。例如，你可以启动状态栏、通知守护进程或任何后台服务。只需确保每个命令及其参数正确引用即可。

### 更好的鼠标大小调整

默认情况下，调整大小时 *dwl* 会将光标移到右下角，通过一个 [补丁](https://codeberg.org/dwl/dwl-patches/src/branch/main/patches/better-resize) 可以改变这一行为。

该补丁允许你配置窗口调整大小行为，使其更灵活，替代默认的跳转到右下角，它提供了多种模式：

- 将光标移动到*选定*的角落。
- 在调整大小时*锁定*光标位置。
- 完全禁用光标跳转。
- 相对角落调整大小，此时光标距离哪个角最近，就使用哪个角进行调整。

该补丁的一个工作配置示例如下：

```
path/to/config.def.h
```

```
/* resize_corner:
 * 0: 左上
 * 1: 右上
 * 2: 左下
 * 3: 右下
 * 4: 距离光标最近的角
 */
static const int resize_corner = 4;
static const int warp_cursor = 0;	/* 1: 跳转到角落, 0: 不跳转 */
static const int lock_cursor = 0;	/* 1: 锁定光标, 0: 不锁定 */
```

这样，调整大小时将选择距离光标最近的角落，光标*不会*跳转，并且光标*不会*锁定自身位置。

## 故障排除

### 修复 Java 应用程序行为异常

参见 [Java#灰色窗口/应用不随窗口管理器调整大小/菜单自动关闭](../../06_软件与应用/Oracle/Java.md#灰色窗口/应用不随窗口管理器调整大小/菜单自动关闭)。

### 修复终端窗口周围的间隙

如果终端窗口外存在桌面空白间隙，很可能是因为终端的字体大小所致。可以调整字体大小直到找到能消除间隙的理想尺寸，或者将 `config.h` 中的 `resizehints` 切换为 *0*。

这会导致 *dwl* 忽略所有客户端窗口的调整大小请求，而不仅仅是终端。此变通方法的缺点是某些终端可能会出现重绘异常，例如显示残影（残留线条）和提前换行等问题。

另外，如果你使用 [st](../Suckless/St.md) 终端模拟器，可以应用 [anysize](https://st.suckless.org/patches/anysize/) 补丁并重新编译 st。

## 参见

- [bemenu](https://github.com/Cloudef/bemenu) - 一个 Wayland 原生的 [dmenu](../Suckless/Dmenu.md) 替代品。
- 一个 *dwl* 的 [介绍视频](https://www.youtube.com/watch?v=OyXAl_EOAO8)。
- *dwl* 的 [git](../../07_开发与工具/版本控制系统/Git.md) [仓库](https://codeberg.org/dwl/dwl)。

## 5.6　Weston

*难度：进阶 ｜ 篇幅：12.2 KB ｜ 所属：Wayland 合成器*

将要讲到：安装、使用、配置、提示与技巧、参考

Weston 是一个开源的、基于[Wayland](../Wayland/Wayland.md) 显示服务器协议的窗口管理器(compositor)，专为正确性、可靠性、可预测性和性能而设计。Weston作为Wayland协议的实现，提供了显示管理器的窗口管理和基本功能 ，包括不限于窗口的创建、移动、缩放、透明度处理、输入事件分发以及多屏幕支持等功能。

## 安装

安装 [weston](https://archlinux.org/packages/?name=weston)包 软件包。

## 使用

要以本机方式（从 TTY）启动 Weston 或在正在运行的 X 会话中运行 Weston：

```
$ weston
```

想了解详细信息和配置，请参阅 [weston(1)](https://man.archlinux.org/man/weston.1) 。

### 示例应用

在 Weston 中内置了原生wayland演示应用，可以运行这些示例程序。要启动终端仿真器，请执行以下命令：

```
$ weston-terminal
```

可移动的花

```
$ weston-flower
```

显示图片

```
$ weston-image image1.jpg image2.jpg...
```

### 快捷键

> **提示：**`Super` (windows按键) 可以自定义, 详细查看 [weston.ini](#Configuration)

<table class="wikitable"><caption><b>Weston 键盘快捷键</b></caption><tbody><tr><th>组合键</th><th>动作</th></tr><tr><td><code>Ctrl+Alt+Backspace</code></td><td>退出 Weston 合成器</td></tr><tr><td><code>Super+Scroll</code> (或 <code>PageUp</code>/<code>PageDown</code>)</td><td>放大/缩小 窗口</td></tr><tr><td><code>Super+Tab</code></td><td>切换窗口</td></tr><tr><td><code>Super+LMB</code></td><td>启动窗口</td></tr><tr><td><code>Super+MMB</code></td><td>旋转窗口</td></tr><tr><td><code>Super+RMB</code></td><td>改变窗口大小</td></tr><tr><td><code>Super+Alt+Scroll</code></td><td>改变窗口透明度</td></tr><tr><td><code>Super+k</code></td><td>强制关闭当前活动窗口</td></tr><tr><td><code>Super+Up/Down</code></td><td>切换到 上一个/下一个 工作区</td></tr><tr><td><code>Super+Shift+Up/Down</code></td><td>移动当前活动窗口到其他工作区</td></tr><tr><td><code>Super+F<i>n</i></code></td><td>切换到工作区<i>n</i> (例如：F2)</td></tr><tr><td><code>Super+s</code></td><td>截屏</td></tr><tr><td><code>Super+r</code></td><td>录制屏幕</td></tr></tbody></table>

## 配置

下面是一个示例配置文件。参见 [weston.ini(5)](https://man.archlinux.org/man/weston.ini.5) 了解更多信息。

```
~/.config/weston.ini
```

```
[core]
# xwayland support
xwayland=true

[libinput]
enable-tap=true

[shell]
#background-image=/usr/share/backgrounds/gnome/Aqua.jpg
background-type=scale-crop
background-color=0xff000000
#background-color=0xff002244
#panel-color=0x90ff0000
panel-color=0x00ffffff
panel-position=bottom
#clock-format=none
#animation=zoom
#startup-animation=none
close-animation=none
focus-animation=dim-layer
#binding-modifier=ctrl
num-workspaces=6
locking=false
cursor-theme=Adwaita
cursor-size=24

# 平板电脑选项
#lockscreen-icon=/usr/share/icons/gnome/256x256/actions/lock.png
#lockscreen=/usr/share/backgrounds/gnome/Garden.jpg
#homescreen=/usr/share/backgrounds/gnome/Blinds.jpg
#animation=fade

# 用于笔记本电脑显示器
[output]
name=LVDS1
mode=preferred
#mode=1680x1050
#transform=rotate-90

#[output]
#name=VGA1
# 下面使用 modeline 设置模式，您可以使用 cvt 工具获取首选分辨率的模式线
#mode=173.00 1920 2048 2248 2576 1080 1083 1088 1120 -hsync +vsync
#transform=flipped

#[output]
#name=X1
#mode=1024x768
#transform=flipped-rotate-270

# 屏幕键盘输入法
#[input-method]
#path=/usr/lib/weston/weston-keyboard

[keyboard]
keymap_rules=evdev
#keymap_layout=us,de
#keymap_variant=colemak,
#keymap_options=grp:shifts_toggle
#keymap_options=caps:ctrl_modifier,shift:both_capslock_cancel
repeat-rate=30
repeat-delay=300

# keymap_options from /usr/share/X11/xkb/rules/base.lst
#numlock-on=true

[terminal]
font=monospace
font-size=18

[launcher]
icon=/usr/share/weston/icon_flower.png
path=/usr/bin/weston-flower

[launcher]
icon=/usr/share/icons/gnome/32x32/apps/utilities-terminal.png
path=/usr/bin/weston-terminal --shell=/usr/bin/bash

#[launcher]
#icon=/usr/share/icons/gnome/32x32/apps/utilities-terminal.png
#path=/usr/bin/gnome-terminal

[launcher]
icon=/usr/share/icons/hicolor/32x32/apps/firefox.png
path=MOZ_ENABLE_WAYLAND=1 /usr/bin/firefox

#[launcher]
#icon=/usr/share/icons/Adwaita/32x32/apps/multimedia-volume-control.png
#path=/usr/bin/st alsamixer -c0
```

最少 `weston.ini` 配置:

```
~/.config/weston.ini
```

```
[core]
xwayland=true

[keyboard]
keymap_layout=us

[launcher]
icon=/usr/share/weston/terminal.png
path=/usr/bin/weston-terminal
```

### 显示器

Weston 的输出与 `xorg.conf` 显示器的输出略有不同：

```
$ ls /sys/class/drm
```

```
card0
card0-VGA-1
card1
card1-DVI-I-1
card1-HDMI-A-1
card1-VGA-2
```

`card0` 是未使用的显卡(内置视频适配器)。附加适配器 `card1` 通过视频线连接到一个 HDMI 和一个 DVI 显示器，因此输出名称为 `HDMI-A-1` 和 `DVI-I-1`。

### Xwayland

请参阅 [Wayland#Xwayland](../Wayland/Wayland.md#Xwayland) 了解可用软件包的详细信息和概述。

设置以下内容以开启 Xwayland 的使用：

```
~/.config/weston.ini
```

```
[core]
xwayland=true
```

> **注意：** 如果尚未配置 X，则可能需要配置键盘映射： [Xorg中的键盘配置](https://wiki.archlinux.org/title/Xorg/Keyboard%20configuration)

### 高 DPI 显示器

对于 [Retina](https://en.wikipedia.org/wiki/Retina_Display) 或 [HiDPI](../图形/HiDPI.md) 的显示器, 使用:

```
~/.config/weston.ini
```

```
[output]
name=...
scale=2
```

### Weston 字体

Weston 使用默认的无衬线字体作为窗口标题栏、时钟等。有关如何更改此字体的说明，请参见[字体配置#设置默认和后备字体](../字体/字体配置.md#设置默认和后备字体)。

### Systemd启动

weston虽没法作为systemd系统服务启动，但可通过[systemd用户](../../02_系统管理与内核/系统管理/Systemd_用户.md)服务进行启动，我们先创建 weston 的[服务](../../01_安装与包管理/初始化/Systemd.md)和 socket [单元](../../01_安装与包管理/初始化/Systemd.md#服务（service）类型)。

#### systemd用户服务启动

创建用户级weston套节字(socket) [单元](../../01_安装与包管理/初始化/Systemd.md#服务（service）类型)，用于监听图形应用

> **提示：**若您没有 `/etc/systemd/user/` 目录的权限，也可在 `$HOME/.config/systemd/user` 目录下进行创建，两者使用效果相同

```
/etc/systemd/user/weston.socket
```

```
[Unit]
Description=Weston, a Wayland compositor
Documentation=man:weston(1) man:weston.ini(5)
Documentation=https://wayland.freedesktop.org/

[Socket]
ListenStream=%t/wayland-0
```

创建用户级weston服务(service)单元

```
/etc/systemd/user/weston.service
```

```
[Unit]
Description=Weston, a Wayland compositor, as a user service
Documentation=man:weston(1) man:weston.ini(5)
Documentation=https://wayland.freedesktop.org/

# 使用 systemd 套接字激活
Requires=weston.socket
After=weston.socket

# 由于我们是图形会话的一部分，因此请确保我们之前已启动
Before=graphical-session.target

[Service]
Type=notify
TimeoutStartSec=60
WatchdogSec=20
# 日志默认输出到journal
#StandardOutput=journal
StandardError=journal
# 确保已创建最少配置 ~/.config/weston.ini
ExecStart=/usr/bin/weston --modules=systemd-notify.so

[Install]
WantedBy=graphical-session.target
```

创建完毕后重新加载 `systemd --user daemon-reload` ，然后在使用 `systemd --user start weston.service` 启动weston

以上的使用需要我们提前登录到系统，并手动运行，若想实现自动启动请参考下面步骤。

#### systemd系统会话管理

一些场合下，我们希望在系统引导后自动启动weston，当然也支持ssh远程登录后手动启动weston，如您不想使用第三方[登录管理器](../图形用户界面/显示管理器.md)，也可使用[systemd](../../01_安装与包管理/初始化/Systemd.md)服务单元来完成该操作，下面使用sysemd服务来创建[用户会话](../../02_系统管理与内核/系统管理/Systemd_用户.md)。

> **注意：**访问`/etc/systemd/system/`要使用root权限,普通用户无法创建

```
/etc/systemd/system/weston-autologin.service
```

```
[Unit]
Description=Weston graphical session

# 确保我们在允许登录后启动。
After=systemd-user-sessions.service

# 如果需要，可以将其作为图形会话的一部分。
#Before=graphical.target

# 不是不需，但以防万一。
#ConditionPathExists=/dev/tty7

[Service]
Type=simple
Environment=XDG_SESSION_TYPE=wayland
ExecStart=/usr/bin/systemctl --wait --user start weston-session.target

# 要运行会话的用户。需要依据实际用户修改！
User=user
Group=user

# 为 Weston 要求的用户设置完整的用户会话。
PAMName=login

# 需要一个虚拟终端。
TTYPath=/dev/tty7
TTYReset=yes
TTYVHangup=yes
TTYVTDisallocate=yes

# 如果不控制 tty，则无法启动。
StandardInput=tty-fail

# 日志输出默认为journal
#StandardOutput=journal
StandardError=journal

# 使用 utmp 记录此用户，让它显示命令 'w' 和 'who'
UtmpIdentifier=tty7
UtmpMode=user

[Install]
WantedBy=graphical.target
```

请确保 `User` 和 `Group` 条目是使用有效的[用户](../../08_安全与加密/安全/用户和用户组.md)。同时还需创一个`weston-session.target`的用户[target](../../01_安装与包管理/初始化/Systemd.md#目标（target）)

```
/etc/systemd/user/weston-session.target
```

```
[Unit]
Description=Weston session

BindsTo=weston-session.target
Before=weston-session.target
```

通知系统重新[加载](../../01_安装与包管理/初始化/Systemd.md#使用单元)配置

```
 $ sudo systemd damon-reload
 $ systemd damon-reload --user
```

最后确保您的默认[target](../../01_安装与包管理/初始化/Systemd.md#目标（target）)使用的是graphical

```
 $ sudo systemctl set-default graphical.target
 $ systemctl --user set-default graphical-session.target
```

## 提示与技巧

### 录像录制

Weston 具有内置的截屏和视频录制功能，可以通过按 `Super`+`r` 组合键来启动和停止。截屏视频保存到 Weston 当前工作目录中的文件 `capture.wcap` 中。WCAP 格式是 Weston 特有的无损视频格式，它只记录帧数的差异。为了能够播放录制的截屏视频，需要将WCAP文件转换为媒体播放器可以理解的格式。首先，将捕获转换为 YUV 像素格式：

```
$ wcap-decode --yuv4mpeg2 capture.wcap > capture.y4m
```

然后，可以使用 [FFmpeg](../../06_软件与应用/视频/FFmpeg.md) 或 [x264](https://archlinux.org/packages/?name=x264)包 将 YUV 文件转码为其他格式（有关详细信息，请参阅 `x264 -h`）。

### 窗口切换

要切换带有 `Super+Space` 而不是 `Super+Tab` 的窗口，请在 `desktop-shell/shell.c` 中将 `KEY_TAB` 更改为 `KEY_SPACE`，然后重新编译 [weston](https://archlinux.org/packages/?name=weston)包。

### 触摸屏幕映射

触摸显示映射可以通过使用 [udev](../../04_硬件与外设/硬件检测和故障排除/Udev.md) 规则来实现。就像 `WL_SEAT` 或 `ID_SEAT` 设备环境变量将设备绑定到指定的席位一样，`WL_OUTPUT` 变量可用于将设备绑定到相应的输出。此变量应与显示器名称匹配。有关显示器命名约定的说明，请参见 [#显示器](#显示器)。

## 参考

- [官方 Weston 项目Gitlab仓库](https://gitlab.freedesktop.org/wayland/weston)
- [启动和运行Weston](https://wayland.pages.freedesktop.org/weston/toc/running-weston.html)

## 5.7　Hyprland

*难度：高级 ｜ 篇幅：33.4 KB ｜ 所属：Wayland 合成器*

将要讲到：安装、配置、使用、Hypr 生态、技巧提示、故障排除

> **翻译状态：**
>
> - 本文（或部分内容）译自 [Hyprland](https://wiki.archlinux.org/title/Hyprland)，最近一次同步于 2026-07-27，若英文版本有所[更改](https://wiki.archlinux.org/title/Hyprland?diff=0&oldid=882352)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/Hyprland_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

[Hyprland](https://hypr.land/) 是一个独立的且使用 C++ 编写的平铺式 [Wayland 合成器](../Wayland/Wayland.md#混成器)。 Hyprland 值得注意的功能包括动态平铺、选项卡窗口、整洁可读的 C++ 代码库，和提供窗口动画的自定义渲染器，窗口圆角，以及透明窗口上的 Dual-Kawase Blur。一般用法和配置在 [Hyprland Wiki](https://wiki.hypr.land/) 中有详细文档介绍。

当你遇到输入法问题时，请查看[故障排除](#故障排除)。

## 安装

### 通过包管理器安装

安装 [hyprland](https://archlinux.org/packages/?name=hyprland)包

### 通过CMake编译安装

首先将 Git 仓库克隆到本地：

```
git clone --recursive https://github.com/hyprwm/Hyprland
```

其次切换到 Git 仓库目录：

```
cd Hyprland
```

最后编译安装：

```
make all && sudo make install
```

自 [#6608](https://github.com/hyprwm/Hyprland/pull/6608) 起，Hyprland 使用 [aquamarine](https://archlinux.org/packages/?name=aquamarine)包 作为自己的渲染后端库。在此之前，它捆绑了它自己的 *wlroots* 版本，与[wlroot-git](https://aur.archlinux.org/packages/wlroot-git/)AUR类似。

> **注意：**
>
> - [NVIDIA](../图形/NVIDIA.md) GPU 用户也应该在尝试启动Hyprland之前确保遵守上游的 NVIDIA 页面 [Hyprland Wiki](https://wiki.hypr.land/Nvidia/)。如果不这么做，可能会导致很多错误，包括可能无法登录、窗口闪烁和 CPU 使用率较高。
> - Hyprland 不支持使用 NVIDIA GPU，很多用户成功了，但如果一些东西出现了错误，那你只能靠你自己来修复了。
> - 由于缺乏`seatd.service`会导致Hyprland无法启动，请确保安装了 [Polkit](../../08_安全与加密/安全/Polkit.md) 软件，或者启动并启用 `seatd.service`。
> - 对于 Vmware/VirtualBox 用户，可能需要开启 3D 加速才可以正常运行 Hyprland

## 配置

> **注意：**在[上游Hyprland Wiki](https://wiki.hypr.land/Configuring/Configuring/) 中详细解释了下文列出的大部分选项（以及更多选项）。自从0.55版本后，Hyprland 已经更换为使用Lua配置，并且弃用了旧的自定义语法。

配置是通过一个单一的`Lua`文件完成的，它通常在`~/.config/hypr/hyprland.lua`。若该文件不存在，*hyprland* 将会自动生成一个示例配置文件。你也可以将你的配置分为多个文件，并且使用`Lua`语言中的`require()`函数加载这些配置文件。默认生成的配置可见`/usr/share/hypr/hyprland.lua`。

`hyprland.lua` 文件包含配置设备（如键盘、鼠标、触控板、显示器）的指令，以及动画、装饰、布局等设置。你可以设置按键绑定、窗口规则以及在启动时执行命令。

在你保存配置文件时，配置都会自动重新加载。你可以使用 `hyprctl reload` 来重载以获得相同的效果。对于某些设置（尤其是输入设置），你可能需要重启你的 Hyprland 会话。

设置也可以使用 *hyprctl* 动态更改设置，但是这些设置不会保存

### 移除最上方的警告

请在`~/.config/hypr/hyprland.lua`中移除或使用`#`注释以下内容:

```
~/.config/hypr/hyprland.lua
```

```
autogenerated = 1
```

### 键盘

#### 按键映射

Hyprland 默认使用 `us` 键盘布局，你可以使用`hl.config()` 函数来更改键盘布局：

```
~/.config/hypr/hyprland.lua
```

```
-- German Colemak layout
hl.config({
    input = {
        kb_layout = "de",
        kb_variant = "colemak",
    }
})
```

查看 [Hyprland Wiki](https://wiki.hypr.land/Configuring/Basics/Variables/#input) 以查看所有可用选项

> **注意：**Hyprland 将会覆盖 [locale](../../00_关于与社区/本地化/Locale.md) 定义，如果你不使用 `us` 布局的键盘则有必要更改按键映射

#### 键入延迟和速率

虽然Xorg用户将习惯于在服务器级别定义此设置，在Wayland上，每个合成器都会自行处理：

```
~/.config/hypr/hyprland.lua
```

```
-- Repeat rate and delay
hl.config({
    input = {
        repeat_rate = 25,
        repeat_delay = 600,
    }
})
```

#### 键盘背光

参见[背光](../../04_硬件与外设/笔记本电脑/背光.md)一文。

你可以在 Hyprland 中使用键盘亮度控制。

你可以尝试在 Hyprland 中控制键盘背光。安装 [brightnessctl](https://archlinux.org/packages/?name=brightnessctl)包 然后添加一个与其有关的按键绑定，随后使用 `SUPER`, `F*X*` 或 `XF86KbdBrightness` 替换 `*keyboard_brightness_**` ，这取决于你的硬件是如何发出[键盘背光](../../04_硬件与外设/键盘配置/键盘背光.md)的。

```
~/.config/hypr/hyprland.lua
```

```
-- Keyboard backlight
hl.bind("keyboard_brightness_up_shortcut", hl.dsp.exec_cmd("brightnessctl -d *::kbd_backlight set +33%"))
hl.bind("keyboard_brightness_down_shortcut", hl.dsp.exec_cmd("brightnessctl -d *::kbd_backlight set 33%-"))
```

此外，你还可以设置更改时弹出[屏幕通知](#屏幕通知)。

#### 媒体按键

在Hyprland上有可能通过使用 `XF86Audio` 键盘按键和一个外部像 [pavucontrol](https://archlinux.org/packages/?name=pavucontrol)包 或者 [pamixer](https://archlinux.org/packages/?name=pamixer)包 和 playerctl 的应用程序使用键盘媒体按键控制媒体（翻译可能不准确，请参照下面英语）

通过 `XF86Audio` 按键在Hyprland中使用键盘的媒体控制键和启动一个像[pavucontrol](https://archlinux.org/packages/?name=pavucontrol)包、[pamixer](https://archlinux.org/packages/?name=pamixer)包和[playerctl](https://archlinux.org/packages/?name=playerctl)包的应用：

```
~/.config/hypr/hyprland.lua
```

```
-- Volume and Media Control
hl.bind("XF86AudioRaiseVolume", hl.dsp.exec_cmd("pamixer -i 5"), { repeating = true })
hl.bind("XF86AudioLowerVolume", hl.dsp.exec_cmd("pamixer -d 5"), { repeating = true })
hl.bind("XF86AudioMicMute", hl.dsp.exec_cmd("pamixer --default-source -m"))
hl.bind("XF86AudioMute", hl.dsp.exec_cmd("pamixer -t"))
hl.bind("XF86AudioPlay", hl.dsp.exec_cmd("playerctl play-pause"))
hl.bind("XF86AudioPause", hl.dsp.exec_cmd("playerctl play-pause"))
hl.bind("XF86AudioNext", hl.dsp.exec_cmd("playerctl next"))
hl.bind("XF86AudioPrev", hl.dsp.exec_cmd("playerctl previous"))
```

当有更改时，也可以触发[屏幕通知](#屏幕通知)

### 鼠标

参见 [Hyprland Wiki](https://wiki.hypr.land/Configuring/Keywords/#per-device-input-configs)。

为了应用鼠标设备的配置选项，请像这样添加一个新类别：

```
~/.config/hypr/hyprland.lua
```

```
device {
    name = 鼠标设备名称
    # 针对鼠标的选项 ...
}
```

通过检查 `hyprctl devices` 的输出，可以轻松获得设备名称。

#### 鼠标加速度/灵敏度

您可以使用下面所示配置控制鼠标加速度/灵敏度：

```
~/.config/hypr/hyprland.lua
```

```
device {
    name = 鼠标设备名称
    sensitivity = -0.5
}
```

将 *\-0.5* 替换为你希望的鼠标加速度/灵敏度。

### 触摸板手势

作为 [Wayland](../Wayland/Wayland.md) 合成器，Hyprland 完全支持触摸板手势（默认是禁用的）。你可以使用`hl.gesture()`函数启用：

```
~/.config/hypr/hyprland.lua
```

```
-- Enable touchpad gestures
hl.gesture({
    fingers = 3,
    direction = "horizontal",
    action = "workspace"
})
```

查看 [Hyprland Wiki](https://wiki.hypr.land/Configuring/Gestures) 来获取所有可用的选项

### 显示设置

#### 屏幕共享

查看屏幕共享。

作为一个兼容wlroots的合成器，Hyprland 可以利用 [xdg-desktop-portal-wlr](https://archlinux.org/packages/?name=xdg-desktop-portal-wlr)包 通过 [XDG 桌面门户](../Freedesktop.org/XDG%20桌面门户.md)在一系列应用程序中启用屏幕录制。

Hyprland 还维护 [xdg-desktop-portal-hyprland](https://archlinux.org/packages/?name=xdg-desktop-portal-hyprland)包，一个支持屏幕共享（包括选区功能）和窗口共享，全局快捷键，并且有一个图形选择其的工具。关于使用该工具在 [Hyprland wiki](https://wiki.hypr.land/Hypr-Ecosystem/xdg-desktop-portal-hyprland) 中有进一步的文档介绍。

值得注意的是，*xdg-desktop-portal-hyprland* 不包括一个文件选择器，用户可以安装 [xdg-desktop-portal-gtk](https://archlinux.org/packages/?name=xdg-desktop-portal-gtk)包。

#### 设置屏幕分辨率

Hyprland 将会尝试去自动检测你的屏幕分辨率。不过在某些情况下它将会失败并默认为故障保护，通常是在存在多个屏幕或你有混合笔记本电脑的情况下。如果屏幕下所有的东西都很大，那么你需要设置你默认的显示器和分辨率

首先使用 *hyprctl* 找到你的默认显示器：

```
$ hyprctl monitors
```

```
Monitor eDP-1 (ID 0):
        1920x1080@144.003006 at 0x0
        description: Chimei Innolux Corporation 0x153C (eDP-1)
        ...
```

然后使用`hl.monitor()`函数配置你的显示器：

```
~/.config/hypr/hyprland.lua
```

```
-- Monitor details
hl.monitor({
    output = "eDP-1",
    mode = "1920x1080@144",
    position = "0x0",
    scale = 1,
})
```

此处的`@144`指的是屏幕的刷新率，`1920x1080`指的是分辨率。请替换为显示器所支持的刷新率和分辨率。

`0x0` 是一个用于多屏幕设置和最终设置的位置偏移，`1` 是屏幕缩放倍数。

更多信息可见[Hyprland Wiki](https://wiki.hypr.land/Configuring/Basics/Monitors/)

#### 设置GUI

[nwg-displays](https://archlinux.org/packages/?name=nwg-displays)包 是管理多显示器的 GUI 软件，支持 Hyprland. 它是nwg-shell的一部分，但并不依赖 nwg-shell，参阅 [nwg-displays github](https://github.com/nwg-piotr/nwg-displays) 以获得更多信息。

#### 亮度

安装 [brightnessctl](https://archlinux.org/packages/?name=brightnessctl)包 并添加以下内容：

```
~/.config/hypr/hyprland.lua
```

```
-- Screen brightness
hl.bind("XF86MonBrightnessUp", hl.dsp.exec_cmd("brightnessctl s +5%"))
hl.bind("XF86MonBrightnessDown", hl.dsp.exec_cmd("brightnessctl s 5%-"))
```

此外，还可以设置更改时弹出[通知](#On_screen_notifications)。

## 使用

**请注意：确保您已正确安装与您主机相匹配的显卡驱动或 openvmtools 并启用3D加速**

### 启动Hyprland

#### 通用 Wayland 会话管理器（uwsm）

[通用Wayland会话管理器](../Wayland/通用Wayland会话管理器.md) 通过systemd 单元文件合成器和与其对应的配置好的软件和守护进程来使你通过*systemctl*控制它们。

当你选择 *hyprland (uwsm-managed)时，*[Display manager](../图形用户界面/显示管理器.md) 会启动一个带有*uwsm*的Hyprland。

> **注意：**其实你选择*Hyprland*也是可以的，只不过pid不同，如果你想为*hyprland(uwsm-managed)*设置变量，请在*uwsm*配置文件夹中创建*env-Hyprland*文件

在[命令行解释器#登录 Shell](../../07_开发与工具/命令行解释器/命令行解释器.md#登录_Shell)使用以下脚本，你也可以在 [getty](../../07_开发与工具/Linux%20控制台/Getty.md) 通过 uwsm 启动Hyprland：

```
if uwsm check may-start && uwsm select; then
    exec systemd-cat -t uwsm_start uwsm start default
fi
```

> **注意：**
>
> - `uwsm check may-start`检查是否可以启动 wayland 会话，尤其是在[登录 shell](../../07_开发与工具/命令行解释器/命令行解释器.md#登录_Shell)运行的情况下。不过，仍应避免在 `.bashrc` 或其他非登录 shell profile 中使用。**请确保这个代码片段放置在你的profile文件最底端**，这是因为执行该代码片段将会屏蔽该文件的剩余部分。
> - Hyprland 不再[推荐](https://github.com/hyprwm/hyprland-wiki/commit/7611480bc4c6a5cae1734cd7d6ea480b55e4c368)启动一个带有*uswm*的会话，因为这被视为实验性的并且是为明白其含义的高级用户和[怪人](https://github.com/hyprwm/Hyprland/discussions/11255#discussioncomment-13938618)使用的。

> **提示：**如果你希望绕过合成器选择菜单，并且直接运行Hyprland，你可以在你的登录 shell中运行如下代码：
>
> ```
> if uwsm check may-start; then
> exec uwsm start hyprland.desktop
> fi
> ```

> **警告：** 如果您选择通过“uwsm”启动 Hyprland，则应相应调整配置。特别是：
>
> - 必须避免使用 `hl.dsp.exit()` 调度器或直接终止 Hyprland 进程，因为这会干扰正常的关机进程。例如，使用 `uwsm stop` 或 `loginctl terminate-user ""` 来终止 Hyprland 并退出用户会话：
>
> ```
> ~/.config/hypr/hyprland.lua
> ```
>
> ```
> hl.bind("SUPER + M", hl.dsp.exec_cmd("uwsm stop"))
> ```
>
> - 不要在 `hyprland.lua` 中配置环境变量，而是使用 “uwsm ”文件 `~/.config/uwsm/env` 来配置 uwsm 管理的所有图形会话（GTK、Qt、xcursor、...）的通用变量。...），以及 `~/.config/uwsm/env-hyprland` 用于 Hyprland 专属环境变量（例如 `HYPR*` 和 `AQ_*` 变量）。这些文件的格式为 `export *KEY*=*VALUE*`，每行不加注释。如果使用多个 GPU，强烈建议将环境变量 `AQ_DRM_DEVICES` 置于 `env-hyprland` 内，以避免与其他合成器发生冲突。
>
> 请阅读[Hyprland Wiki 中的相关页面](https://wiki.hypr.land/Useful-Utilities/Systemd-start/)以了解有关使用 uwsm 配置的更多信息。

#### 终端

使用如下指令在 [Getty](../../07_开发与工具/Linux%20控制台/Getty.md) 中启动 Hyprland：

```
$ start-hyprland
```

#### 显示管理器

虽然从[显示管理器](../图形用户界面/显示管理器.md)中启动不是官方支持的，但是用户们报告成功在 [GDM](../GNOME/GDM.md) 、 [SDDM](../显示管理器/SDDM.md) 和其他的显示管理器中启动。[hyprland](https://archlinux.org/packages/?name=hyprland)包 中包含两个[桌面项](../../01_安装与包管理/配置文件/桌面项.md)，并且所有 Hyprland AUR 包将会自动生成。

两种方法都提供相同的结果，加上或减去一些环境变量和服务。

#### 自动登录

用户可以通过一个[显示管理器](../图形用户界面/显示管理器.md)或着在登录时采用 [Xinit#Autostart X at login](../Xorg%20命令/Xinit.md#Autostart_X_at_login) 中描述的方法进行自动登录

### hyprctl 和 IPC

*hyprctl* 是一个命令行实用程序，随Hyprland一起安装，用于与显示服务器通信。它允许你向服务器发送命令（相当于配置文件中的命令，但语法略有不同）、设置关键词、发送询问和请求信息。请查看[完整文档](https://wiki.hypr.land/Configuring/Using-hyprctl)

Hyprland 还公开了 [2 UNIX 套接字](https://wiki.hypr.land/IPC/)，以便通过代码或命令行实用程序控制和获取有关 Hyprland 的信息。这些套接字会广播焦点变化（窗口、工作区、监视器）、窗口/工作区创建等事件。

*hyprctl* 和 IPC 接口都在脚本中有效使用，对于复杂任务控制 Hyprland。

### 自动启动

在 Lua 配置文件中，你可以在`hyprland.start`事件监听器中执行命令以实现自动启动软件。

> **注意：**在[#配置](#配置)中提到，Hyprland 会自动解析 `hyprland.lua` 文件**每次的更改**。使用`hl.on("hyprland.start", ...)`事件确保了软件和守护进程仅仅在引导后启动一次，防止资源竞争情况或者多个实例在重载后同时运行。

> **提示：**如果你通过通用Wayland会话管理器(uwsm)启动Hyprland，并且你想在开机时运行的软件提供一个systemd单元，那么你可以启用它来在Hyprland启动后自动启动该单元。另外，你也可以将那个软件作为参数传递到`uwsm app`中来使它被*uwsm*管理。例如：
>
> ```
> ~/.config/hypr/hyprland.lua
> ```
>
> ```
> hl.on("hyprland.start", function ()
> hl.exec_cmd("uwsm app -- mycommand --arg1 --arg2")
> end)
> hl.bind("SUPER + E", hl.dsp.exec_cmd("uwsm app -- pcmanfm-qt.desktop"))
> ```

### 设置环境变量

你可以使用`hl.env()`函数在`hyprland.lua`中设置[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)，但是它的语法不同于在Unix shell中使用*env*命令

详见[Hyprland Wiki](https://wiki.hypr.land/Configuring/Advanced-and-Cool/Environment-variables/)。

如果`XDG_MENU_PREFIX`变量未设置，KDE组件软件（如Dolphin、digiKam）可能会展示一个空的 *使用……打开* 菜单。设置这个来匹配一个已安装在`/etc/xdg/menus/`的菜单文件，如：

```
~/.config/hypr/hyprland.lua
```

```
hl.env("XDG_MENU_PREFIX", "gnome-")
```

使用`plasma-`作为`plasma-applications.menu`或者`xfce-`作为`xfce-applications.menu`。然后重构KDE服务缓存：

```
$ kbuildsycoca6 --noincremental
```

## Hypr 生态

> **警告：**以下所提及的部分软件仍在开发，有 bug 都是在意料之中的。因此，完整的说明和示例会被略去，直到它足够稳定以成为稳定版。关于使用说明，参见[上游 Wiki](https://wiki.hypr.land/Hypr-Ecosystem/)。

Hyprland 开发团队正在构建一个专门用于 Hyprland 的软件生态，这些工具将包括调度器，允许使用 hyprctl 而不是依赖脚本来控制它们。

目前，软件生态包括以下软件：

### Hyprpaper

Hyprpaper 是一个壁纸工具，你可以安装 [hyprpaper](https://archlinux.org/packages/?name=hyprpaper)包 来使用它。

### Hyprpicker

Hyprpicker 是一个用于在一个桌面中抓取一个颜色的工具，你可以安装 [hyprpicker](https://archlinux.org/packages/?name=hyprpicker)包 来使用它。该软件不需要其余配置。

### Hypridle

Hypridle 是一个idle管理守护程序，你可以安装 [hypridle](https://archlinux.org/packages/?name=hypridle)包 来使用它。

### Hyprlock

[Hyprlock](Hyprlock.md) 是一个屏幕锁定管理器，你可以安装 [hyprlock](https://archlinux.org/packages/?name=hyprlock)包 来使用它。

### Hyprcursor

Hyprcursor 是一个处理屏幕光标的新格式，它在传统的方式上提升了很多，你可以安装 [hyprcursor](https://archlinux.org/packages/?name=hyprcursor)包 来使用它。

#### Hyprcursor 主题

> **提示：**如果你安装了 hyprcursor 并且没有安装一个主题，它将会使用你之前的光标设置

光标主题可以使用 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 安装，以下是例子：

- [sweet-cursors-hyprcursor-git](https://aur.archlinux.org/packages/sweet-cursors-hyprcursor-git/)AUR

- [nordzy-hyprcursors](https://aur.archlinux.org/packages/nordzy-hyprcursors/)AUR

- [xcursor-pro-hyprcursor](https://aur.archlinux.org/packages/xcursor-pro-hyprcursor/)AUR

- [hyprcursor-dracula-kde-git](https://aur.archlinux.org/packages/hyprcursor-dracula-kde-git/)AUR

将现有的光标主题移植到 Hyprcursor，你可以参照[上游 GitHub 仓库](https://github.com/hyprwm/hyprcursor/tree/main/docs)。

关于设置光标样式，你也可以查阅[光标主题](../X%20服务器/光标主题.md)来设置。

### XDG-Desktop-Portal-Hyprland

Hyprland 自己的实现的[XDG Desktop Portal](../Freedesktop.org/XDG%20桌面门户.md)，与其他基于wlroots的合成器兼容，但是在使用Hyprland时额外提供一些功能。可安装[xdg-desktop-portal-hyprland](https://archlinux.org/packages/?name=xdg-desktop-portal-hyprland)包

### Hyprpolkitagent

Hyprpolkitagent 是一个 polkit 身份验证守护进程。图形用户界面应用程序需要使用它来请求提升权限。安装 [hyprpolkitagent](https://archlinux.org/packages/?name=hyprpolkitagent)包 来使用它。

参见 [Hyprland Wiki](https://wiki.hypr.land/Hypr-Ecosystem/hyprpolkitagent/)。

### Hyprsunset

Hyprsunset 是提供蓝光滤镜的实用工具，通过 [hyprsunset](https://archlinux.org/packages/?name=hyprsunset)包 安装。

### Hyprsysteminfo

Hyprsysteminfo 是一个获取系统信息的软件，与[neofetch](https://aur.archlinux.org/packages/neofetch/)AUR和[fastfetch](https://archlinux.org/packages/?name=fastfetch)包类似。你可以安装[hyprsysteminfo](https://aur.archlinux.org/packages/hyprsysteminfo/)AUR

## 技巧提示

> **注意：**
>
> - 对于以下所有部分，通常有不止一种方法可以达到类似的效果，这里提供的只是一些基本示例。
>
> - 有关替代品的完整列表，请参见[应用程序列表](../../06_软件与应用/应用程序/应用程序列表.md)，Hyprland 的特定列表可在[上游 Wiki](https://wiki.hypr.land/Useful-Utilities/) 找到。

### 文件管理器

在 Hyprland 环境下，如果需要图形化的文件管理功能，则需要一个兼容 Wayland 的外部应用程序。以 [thunar](https://archlinux.org/packages/?name=thunar)包 为例，我们只需为其分配一个快捷键，操作如下：

```
~/.config/hypr/hyprland.lua
```

```
...
hl.bind("SUPER + E", hl.dsp.exec_cmd("thunar"))
...
```

### 应用程序启动器

在 Hyprland 环境下，如果需要启动应用程序，则需要一个兼容 Wayland 的外部应用程序。以 [wofi](https://archlinux.org/packages/?name=wofi)包 软件包为例，我们只需为其分配一个快捷键，操作如下：

```
~/.config/hypr/hyprland.lua
```

```
...
hl.bind("SUPER + F", hl.dsp.exec_cmd("wofi --show drun"))
...
```

### Idle

Hyprland 需要一个外部的 Wayland 兼容的 idle 管理守护进程。最普遍安装的是 [hypridle](https://archlinux.org/packages/?name=hypridle)包 和 [hyprlock](https://archlinux.org/packages/?name=hyprlock)包。你可以使用如下的快捷键来手动锁定屏幕：

```
~/.config/hypr/hyprland.lua
```

```
...
hl.bind("SUPER + L", hl.dsp.exec_cmd("hyprlock"))
...
```

#### 自动锁定屏幕和挂起

创建下面的文件：

```
~/.config/hypr/hyprland.lua
```

```
...
hl.bind("SUPER + L", hl.dsp.exec_cmd("hyprlock"))
...
```

> **提示：**你可以通过修改数值（以秒为单位）来调整无操作时的锁屏超时时间。300 是 5 分钟，600 是 10 分钟等等。

这个文件必须是可执行的，然后运行它：

```
~/.config/hypr/hyprland.lua
```

```
-- enable hypridle at start
hl.on("hyprland.start", function()
    ...
    hl.exec_cmd("hypridle")
    ...
end)
```

#### 在超时期限后使用 DPMS 关闭屏幕

Hyprland 有一个内置的调度器来控制 DPMS 请求，不过不建议使用它作为一个直接按键绑定，这样做的结果是你不能打开重新打开你的屏幕，且需要你重新启动。

更改在上文的文件并将其更改为：

```
~/.config/hypr/hypridle.conf
```

```
general {
    lock_cmd = pidof hyprlock || hyprlock
}

listener {
    timeout = 300
    on-timeout = loginctl lock-session
}

listener {
    timeout = 600
    on-timeout = hyprctl dispatch dpms off
    on-resume = hyprctl dispatch dpms on
}

listener {
    timeout = 900
    on-timeout = systemctl suspend
}
```

### 状态栏

在 Hyprland 环境下，如果需要状态栏，则需要一个兼容 Wayland 的外部应用程序。以 [waybar](https://archlinux.org/packages/?name=waybar)包 软件包为例，操作如下：

```
~/.config/hypr/hyprland.lua
```

```
-- enable status bar at start
hl.on("hyprland.start", function()
    ...
    hl.exec_cmd("waybar")
    ...
end)
```

#### 工作区总揽

[waybar](https://archlinux.org/packages/?name=waybar)包 内置了适于这个功能的完全可自定义模块，支持Hyprland工作区自然地切换。

查看 waybar Wiki 了解详情。

### Polkit 认证

[Polkit](../../08_安全与加密/安全/Polkit.md) 认证需要使用外部身份验证代理。Hyprland 推荐使用[hyprpolkitagent](https://archlinux.org/packages/?name=hyprpolkitagent)包 ，你也可以选择其他的Polkit

调用[hyprpolkitagent](https://archlinux.org/packages/?name=hyprpolkitagent)包：

```
~/.config/hypr/hyprland.lua
```

```
-- enable polkit authentication agent at start
hl.on("hyprland.start", function()
    ...
    hl.exec_cmd("systemctl --user start hyprpolkitagent")
    ...
end)
```

### 桌面壁纸

在 Hyprland 环境下，如果需要管理桌面壁纸，则需要一个兼容 Wayland 的外部应用程序。以 [hyprpaper](https://archlinux.org/packages/?name=hyprpaper)包 软件包为例，操作如下：

```
~/.config/hypr/hyprland.lua
```

```
-- enable hyprpaper at start
hl.on("hyprland.start", function()
    ...
    hl.exec_cmd("hyprpaper")
    ...
end)
```

另外，[hyprpaper](https://archlinux.org/packages/?name=hyprpaper)包也需要一个配置文件来启用，创建一个文件，如下：

```
~/.config/hypr/hyprpaper.conf
```

```
wallpaper {
    monitor = monitor
    path = /home/me/amongus.png
    fit_mode = cover
```

将*monitor*替换为你想设置壁纸的显示器，你可以通过`hyprctl monitors`来获取一个显示器列表。`monitor`也可以是空的，这样就可以应用规则到所有显示器。

#### 随机壁纸脚本

创建以下脚本并确保其可执行：

```
~/.config/hypr/scripts/hyprpaper-random
```

```
#!/usr/bin/env bash

WALLPAPER_DIR="$HOME/.config/hypr/wallpapers/"
CURRENT_WALL=$(hyprctl hyprpaper listloaded)

# Get a random wallpaper that is not the current one
WALLPAPER=$(find "$WALLPAPER_DIR" -type f ! -name "$(basename "$CURRENT_WALL")" | shuf -n 1)

# Apply the selected wallpaper
hyprctl hyprpaper reload ,"$WALLPAPER"
```

接下来创建一个新目录来存储背景图片，类似于 `~/.config/hypr/wallpapers` 的文件夹即可，并在其中放置任何你想要设置为的图片。

最后，当*指定*按键被按下时调用脚本：

```
~/.config/hypr/hyprland.lua
```

```
...
hl.bind("SUPER + R", hl.dsp.exec_cmd("~/.config/hypr/scripts/hyprpaper-random"))
...
```

### 屏幕通知

屏幕上对亮度和音量调整等操作显示通知的功能可以借助外部的通知服务程序来实现。这里我们以 [mako](https://archlinux.org/packages/?name=mako)包 为例。

更多屏幕通知软件请见[桌面通知程序#独立通知](../通知/桌面通知程序.md#独立通知)，参阅[桌面通知程序](../通知/桌面通知程序.md)以获取媒体键通知的进一步说明。

脚本请见[Mako#使用方法#媒体键通知](../桌面环境/Mako.md#使用方法)

然后添加以下内容（或编辑任何现有绑定）：

```
~/.config/hypr/hyprland.lua
```

```
-- Volume
hl.bind("XF86AudioRaiseVolume", hl.dsp.exec_cmd("~/.config/hypr/scripts/volume --inc"))
hl.bind("XF86AudioLowerVolume", hl.dsp.exec_cmd("~/.config/hypr/scripts/volume --dec"))
hl.bind("XF86AudioMicMute", hl.dsp.exec_cmd("~/.config/hypr/scripts/volume --toggle-mic"))
hl.bind("XF86AudioMute", hl.dsp.exec_cmd("~/.config/hypr/scripts/volume --toggle"))
```

#### 屏幕背光通知

脚本请见[Mako#使用方法#屏幕背光通知](../桌面环境/Mako.md#使用方法)

然后添加以下内容（或者编辑任何现有的绑定）：

```
~/.config/hypr/hyprland.lua
```

```
-- Screen brightness
hl.bind("XF86MonBrightnessUp", hl.dsp.exec_cmd("~/.config/hypr/scripts/backlight --inc"))
hl.bind("XF86MonBrightnessDown", hl.dsp.exec_cmd("~/.config/hypr/scripts/backlight --dec"))
```

#### 键盘（Keyboard）语言通知

要运行此脚本， 你需要一个命令行 JSON 处理器 [gojq](https://aur.archlinux.org/packages/gojq/)AUR

脚本请见[Mako#使用方法#键盘（Keyboard）语言通知](../桌面环境/Mako.md#使用方法)

然后添加以下内容（或编辑任何现有的绑定）：

```
~/.config/hypr/hyprland.lua
```

```
hl.device({
  name = "at-translated-set-2-keyboard",
  kb_layout = "us,ru,ua",
  kb_variant = "lang",
  kb_options = "grp:win_space_toggle",
})

-- Language
hl.bind("SUPER + SPACE", hl.dsp.exec_cmd("~/.config/hypr/scripts/lang"))
```

### 电源控制（Power control）

Hyprland 需要一个与 Wayland 兼容的外部应用程序来进行电源控制。 以 [nwg-bar](https://aur.archlinux.org/packages/nwg-bar/)AUR 为例，我们只需按如下方式进行绑定：

```
~/.config/hypr/hyprland.lua
```

```
...
hl.bind("SUPER + ESCAPE", hl.dsp.exec_cmd("nwg-bar"))
...
```

### 剪贴板（Clipboard）

[Wayland](../Wayland/Wayland.md) 剪贴板行为会在关闭复制数据的应用程序时删除数据。其他桌面环境通过使用专用的[剪贴板](../../06_软件与应用/软件列表/剪贴板.md)管理器来解决这一问题，而 Hyprland 上则有多种兼容的选择。更多信息，请参阅[上游 Wiki](https://wiki.hypr.land/Useful-Utilities/Clipboard-Managers/)。

本节将介绍 [cliphist](https://archlinux.org/packages/?name=cliphist)包，因为它支持复制图片和文本：

```
~/.config/hypr/hyprland.lua
```

```
hl.on("hyprland.start", function()
    ...
    hl.exec_cmd("wl-paste --type text --watch cliphist store")
    hl.exec_cmd("wl-paste --type image --watch cliphist store")
    ...
end)
```

然后创建一个绑定，以便在你选择的[应用程序启动器](#应用程序启动器)中调用历史记录：

```
~/.config/hypr/hyprland.lua
```

```
...
hl.bind("SUPER + V", hl.dsp.exec_cmd("cliphist list | wofi --dmenu | cliphist decode | wl-copy"))
...
```

现在按下 `Super+v` 将打开一个包含剪贴板历史列表的 `wofi` 窗口。

### 启用/禁用设备

要启用 / 禁用设备（例如触摸板），首先使用：

```
$ hyprctl devices
```

来获取你的设备名称。 将这些代码行放入你的配置文件中（将替换为上面查询到的你的设备名称）以打开 / 关闭设备：

```
~/.config/hypr/hyprland.lua
```

```
hl.device({
  name = "<device_name>"
  enabled = {true/false}
})
```

要动态地打开 / 关闭设备，请使用 hyprctl：

```
$ hyprctl eval "hl.device({ name = '<device-name>', enabled = {true/false) })"
```

你也可以创建一个快捷键绑定，例如：

```
~/.config/hypr/hyprland.lua
```

```
...
hl.bind("SUPER + T", hl.dsp.exec_cmd([[hyprctl eval "hl.device({ name = 'logitech-g502-hero-gaming-mouse', enabled = false})"]]))

hl.bind("SUPER + SHIFT + T", hl.dsp.exec_cmd([[hyprctl eval "hl.device({ name = 'logitech-g502-hero-gaming-mouse', enabled = true})"]]))
...
```

### 独立的 dconf 配置文件

您可能不想污染其他基于 GTK 的桌面环境的配置，您可以使用一个独立的 `dconf` 配置文件。例如：

声明新的全局 dconf 配置文件：

```
/etc/dconf/profile/hyprland
```

```
user-db:hyprland
```

```
~/.config/hypr/hyprland.lua
```

```
...
hl.env("DCONF_PROFILE", "hyprland")
...
```

现在您可以使用 *gsettings* 而不影响其他桌面环境。

### KDE Plasma 应用不跟随Qt主题

你已经设置了一个Qt主题，但是一些plasma应用可能不会跟随主题。你可以安装[qt6ct-kde](https://aur.archlinux.org/packages/qt6ct-kde/)AUR，并且选择一个KColorScheme或者通过在kdeglobals中添加如下配置以解决：

```
~/.config/kdeglobals
```

```
[UiSettings]
ColorScheme=NameOfTheProfile
```

## 故障排除

### Fcitx5 相关问题

> **提示：**若还未能解决，请重启输入法！

#### 无法输入

当你在任何时候输入中文时出现候选框不在输入框附近（如 QQ 等），而是漂移到 Waybar 的左上角，选择后也无法选词到输入框中，请在 Fcitx5 的配置页面中新建一个分组，选择`键盘-英语（美国）`，然后再设置一个快捷键来轮回切换分组，切换后即可恢复正常

打开 Fcitx5 的配置页面你需要安装 [fcitx5-configtool](https://archlinux.org/packages/?name=fcitx5-configtool)包。

#### 切换英文时出现问题

当你在输入框中切换英文时，在 Waybar 左上角出现了输入法中文状态，且无法在输入框中输入英文，请重新启动 Fcitx5 以临时解决问题。

### JetBrains 软件聚焦问题

JetBrains 软件（PyCharm，IntelliJ）会出现一个奇怪的聚焦问题，如：

- 无法将标签页从标签栏拖动来实现分离，或者另一个标签页堆叠时无法更改焦点，亦或者一个标签页只要被你拖动过当前标签栏就会停止拖动状态。
- 自动弹出的窗口直到鼠标移动时才被聚焦。

通过在Hyprland配置文件中写入以下行以缓解该问题：

```
~/.config/hypr/hyprland.lua
```

```
hl.window_rule({
     match = { xwayland = true },
     no_initial_focus = true,
 })
```

## 参见

- [Hyprland 网站](https://hyprland.org/)
- [官方文档](https://wiki.hypr.land/)
- [Hyprland Github 仓库页面](https://github.com/hyprwm/Hyprland/)
- [Community-maintained 工具、插件和扩展程序列表](https://github.com/hyprland-community/awesome-hyprland)
- [SolDoesTech Github 仓库——Hyprland 快速安装脚本（含 Hyprland 配置文件）](https://github.com/SolDoesTech/HyprV4)

## 5.8　Sway

*难度：高级 ｜ 篇幅：43.2 KB ｜ 所属：Wayland 合成器*

将要讲到：安装、启动、配置、提示和技巧、疑难解答、参见

Sway（缩写自 **S**irCmpwn's **Way**land compositor）是一款专为 [Wayland](../Wayland/Wayland.md) 设计的合成器，旨在与 [i3](../动态窗口管理器/I3.md) 完全兼容。根据[官网](https://swaywm.org)所述：

: Sway 是 Wayland 的合成器，也是 x11 的 i3 窗口管理器的替代品。它可以根据您现有的 i3 配置工作，并支持 i3 的大部分特性以及一些附加功能

若您喜欢视觉效果，也可以试试 [swayfx](https://aur.archlinux.org/packages/swayfx/)AUR，这是一个带有视觉效果的 Sway 分支。

Sway 的另一个分支 [sway-scroll-git](https://aur.archlinux.org/packages/sway-scroll-git/)AUR 具有滚动布局（如同 PaperWM 和 [niri](https://archlinux.org/packages/?name=niri)包）。还支持动画效果、窗口内容缩放和概览模式。

## 安装

安装 [sway](https://archlinux.org/packages/?name=sway)包。由于依赖关系紧密，请在更新 *sway* 时始终更新 *wlroots*。

> **注意：**所有专有显卡驱动程序都[不受支持](https://github.com/swaywm/sway/wiki#nvidia-users)，包括 [NVIDIA](../图形/NVIDIA.md)。在 NVIDIA 驱动程序版本 495 之后，需要启用[内核级显示模式设置](../图形/NVIDIA.md#DRM_内核级显示模式设置)并在运行 sway 时附加 `--unsupported-gpu` 参数。

您还可以安装 [swaylock](https://archlinux.org/packages/?name=swaylock)包、 [swayidle](https://archlinux.org/packages/?name=swayidle)包 和 [swaybg](https://archlinux.org/packages/?name=swaybg)包，用以锁定屏幕、设定空闲管理和设置壁纸。

默认的应用程序启动器为 [wmenu](https://archlinux.org/packages/?name=wmenu)包，默认的[终端模拟器](../../06_软件与应用/应用程序/应用程序列表_工具.md)是 [foot](../../07_开发与工具/终端模拟器/Foot.md)。在启动 *sway* 之前，建议安装它们或在配置文件中设置新的启动器和终端。您可在 Sway wiki 的[迁移指南](https://github.com/swaywm/sway/wiki/i3-Migration-Guide)上找到一些 i3 实用程序的其他 Wayland 兼容替代品。

## 启动

Sway 在启动之前需要访问您的硬件设备（如键盘、鼠标和显卡），这些硬件设备的集合称为 seat，如同 [sd-login(3) § DEFINITION OF TERMS](https://man.archlinux.org/man/sd-login.3#DEFINITION_OF_TERMS) 的 seat 小节所述。

在 Arch Linux 上，Sway 可以使用以下任一方式访问您的 seat。

- [systemd-logind(8)](https://man.archlinux.org/man/systemd-logind.8) 和 [polkit](https://archlinux.org/packages/?name=polkit)包
- [seatd](https://archlinux.org/packages/?name=seatd)包，会作为 [wlroots0.20](https://archlinux.org/packages/?name=wlroots0.20)包的依赖与 Sway 一起安装

若 `polkit` 已安装在您的系统上， Sway 应会自动访问你的 seat。

若 `polkit` 未安装在您的系统上，并且您想使用 `seatd` 来替代，请将您添加到 `seat` [用户组](../../08_安全与加密/安全/用户和用户组.md#用户组管理)并启用/启动 `seatd.service`，然后重新登录。

您可以选择以下方法之一来启动 Sway。

### 手动启动

要启动 Sway，只需在 Linux 终端执行 `sway`。

### 自动启动

与 X 类似，可以通过将以下内容添加到 shell 初始化文件来启动 Sway（请参阅[命令行解释器#登录 Shell](../../07_开发与工具/命令行解释器/命令行解释器.md#登录_Shell)）：

```
if [ -z "$WAYLAND_DISPLAY" ] && [ -n "$XDG_VTNR" ] && [ "$XDG_VTNR" -eq 1 ] ; then
   exec sway
fi
```

有关更多详细信息，请参阅 [Xinit#登录时自动启动 X](../Xorg%20命令/Xinit.md#登录时自动启动_X)。

### 使用显示管理器启动

Sway 会话位于 `/usr/share/wayland-sessions/sway.desktop`。它可以被 [GDM](../GNOME/GDM.md) 和 [SDDM](../显示管理器/SDDM.md) 等现代显示管理器自动识别。

也可以将 [systemd 用户服务](https://github.com/swaywm/sway/wiki/Systemd-integration#running-sway-itself-as-a---user-service)作为一个显示管理器去运行 sway。

您还可以使用基于文本的显示管理器，请参阅[显示管理器#控制台](../图形用户界面/显示管理器.md#控制台)。

## 配置

若您已在使用 i3，则可以将 i3 配置文件复制到 `~/.config/sway/config`，该文件开箱即用。否则，请将位于 `/etc/sway/config` 的示例配置文件复制到 `~/.config/sway/config`。有关配置的信息参见 [sway(5)](https://man.archlinux.org/man/sway.5)。

> **注意：**用户配置文件中应当包含 `include /etc/sway/config.d/*` 一行，以便从引置文件（drop-in files）中读取并应用配置。[sway](https://archlinux.org/packages/?name=sway)包 提供了一个引置文件 `50-systemd-user.conf`，其会将多个环境变量导入 systemd 用户会话和 dbus。一些应用需要这些环境变量以正常运行（如[xdg-desktop-portal-wlr](https://archlinux.org/packages/?name=xdg-desktop-portal-wlr)包）。

### 键盘布局

默认情况下，sway 从 US QWERTY 键盘布局开始。配置多个键盘布局：

```
~/.config/sway/config
```

```
input * {
    xkb_layout "us,de,ru"
    xkb_variant "colemak,,typewriter"
    xkb_options "grp:win_space_toggle,caps:none"
}

input <identifier> xkb_model "pc101"
```

更多细节请参阅 [xkeyboard-config(7)](https://man.archlinux.org/man/xkeyboard-config.7) 与 [sway-input(5)](https://man.archlinux.org/man/sway-input.5)。

在启动 `sway` 时，也可以使用环境变量（`XKB_DEFAULT_LAYOUT`、`XKB_DEFAULT_VARIANT` 等）配置键盘布局。配置选项优先于环境变量。

### 打字延迟和速率

要更改打字延迟和速率，您可以在 `input` 节添加以下行：

```
~/.config/sway/config
```

```
input <identifier> repeat_delay 300
input <identifier> repeat_rate 30
```

### 状态栏

Sway 附带了一个默认状态栏，名为 *swaybar* ，可在纯 [Wayland](../Wayland/Wayland.md) 环境中运行。*swaybar* 可以调用 shell 脚本或其他程序来在状态栏中显示信息。有关详细信息，请参阅 [sway-bar(5)](https://man.archlinux.org/man/sway-bar.5) 与 [swaybar-protocol(7)](https://man.archlinux.org/man/swaybar-protocol.7)。

> **提示：***swaybar* 对[托盘图标](https://github.com/swaywm/sway/issues/3799)的支持并不完整，请考虑使用 [waybar](https://archlinux.org/packages/?name=waybar)包 等替代方案，在 `bar` 节设置 `swaybar_command waybar` 即可启用。

[i3status](../动态窗口管理器/I3.md#i3status) 是在 Wayland 下获取实用的默认状态栏的一个选项，您只需在 sway 配置的末尾添加以下代码段：

```
~/.config/sway/config
```

```
bar {
    status_command i3status
}
```

欲为 i3status 启用色彩输出，请在 i3status 配置中调整以下部分：

```
~/.config/i3status/config
```

```
general {
    colors = true
    interval = 5
}
```

### Output

`sway` 的 `output` 命令可以为不同的显示输出指定详细配置，包括但不限于壁纸、缩放、位置等。如有需要，您可将不同的 output 命令整合到一行，例如：

```
~/.config/sway/config
```

```
output HDMI-A-1 mode 1920x1080 pos 1920 0 bg ~/wallpaper.png stretch
```

output 有多种方式匹配显示输出。可以使用其指定的输出名称，也可以使用 `"*"` 匹配所有显示输出，或是使用显示器的不同名称（由品牌、型号和序列号组成的字符串），例如：

```
~/.config/sway/config
```

```
output "Some Company ABC123 0x00000000" pos 1920 0
```

可以使用以下命令获取输出名称和一些其他信息：

```
$ swaymsg -t get_outputs
```

要深入了解配置和附加选项，请参阅 [sway-output(5)](https://man.archlinux.org/man/sway-output.5)。

#### 壁纸

sway 用专用程序处理壁纸，最简单的例子是 [swaybg](https://archlinux.org/packages/?name=swaybg)包，其可直接由 `sway` 管理。若要运行 `output ... bg` 命令，请安装 [swaybg](https://archlinux.org/packages/?name=swaybg)包。

可以将以下行附加在 sway 配置的任意位置，其会在所有显示器上设置背景图像：

```
~/.config/sway/config
```

```
output "*" bg /path/to/image fill
```

当然，`/path/to/image` 应当被替换为存在的图像路径。

如果只需要纯色背景，可以按如下方式设置：

```
output * bg #000000 solid_color
```

请参阅 [Sway wiki](https://github.com/swaywm/sway/wiki/Useful-add-ons-for-sway#wallpaper) 以了解更多用于管理壁纸的工具。

#### HiDPI

> **注意：**Xwayland 也会被缩放，且会降低分辨率。欲以原始分辨率运行 Xwayland 下的程序，则需禁用缩放。您可使用一些工具临时禁用缩放，如使用 swaymsg：
>
> ```
> swaymsg output "*" scale 1
> ```
>
> 欲恢复到自动设定则设为 `0`。

##### 自动设定

Sway 默认使用整数倍缩放。若满足以下条件：

- 屏幕通过 EDID（ Extended Display Identification Data，外部显示设备标识数据）提供有效的物理显示信息。

- DPI（每英寸像素数）至少为 192。

- 屏幕分辨率的高至少为 1200 像素。

那么 Sway 会使用 2 倍缩放。一些设备（如 Framework Laptop 16）的 DPI 近似（但不是）192，在这种情况下，您可能需要手动配置分数倍缩放。

##### 手动设定

在配置文件里用 `output` 命令设置缩放倍数。可以设置分数倍缩放，不过 HiDPI 屏幕通常为 2。

```
~/.config/sway/config
```

```
output <名称> scale <倍数>
```

可以使用以下命令获知显示器名称：

```
$ swaymsg -t get_outputs
```

#### 撕裂

Sway 默认会同步帧更新（vsync，垂直同步）以使输出画面的每一帧都帧完整而不撕裂，但是会带来一些延迟。在一些使用场景（如游戏）下禁用垂直同步可以使输出更为顺畅：

```
~/.config/sway/config
```

```
output <name> allow_tearing yes
output * max_render_time off
```

### 输入设备

可以调整特定输入设备的配置。例如，若要为所有触摸板启用点击以单击和自然滚动，请使用以下配置：

```
~/.config/sway/config
```

```
input type:touchpad {
    tap enabled
    natural_scroll enabled
}
```

要为特定触摸板设定配置，请使用 `swaymsg -t get_inputs` 获取设备标识符并用其代替上述配置中的 `type:touchpad`。

> **注意：**命令 `swaymsg -t get_inputs` 的输出可能会使用`\`来转义`/`等符号（如 `"2:14:ETPS\/2_Elantech_Touchpad"`），配置时需要删除`\`。

关于更多文档和选项（如加速度或完全禁用输入），请参阅 [sway-input(5)](https://man.archlinux.org/man/sway-input.5)。

若您使用数位板，请参阅 Graphics tablet#Sway（英语：[Graphics tablet#Sway](https://wiki.archlinux.org/title/Graphics_tablet#Sway)）。

#### 触摸屏映射

在多显示器环境中，触摸屏的触摸输入目标可以仅被映射到该触摸屏。

```
~/.config/sway/config
```

```
set $display1      "Dell Inc. DELL P2414H VHVTW542165L"
set $display2      "Dell Inc. DELL P2418HT MYDM775F152L"
set $display2-touch "8146:24835:Melfas_LGD_AIT_Touch_Controller"

input $display2-touch map_to_output $display2
```

### 自定义快捷键

键盘上的[特殊按键](../../04_硬件与外设/键盘配置/键盘输入.md)可用于执行命令，如控制音量、屏幕亮度和媒体播放：

```
~/.config/sway/config
```

```
# 将特定按键绑定到 pactl 以调整 PulseAudio 音量
bindsym --locked XF86AudioRaiseVolume exec pactl set-sink-volume @DEFAULT_SINK@ +5%
bindsym --locked XF86AudioLowerVolume exec pactl set-sink-volume @DEFAULT_SINK@ -5%
bindsym --locked XF86AudioMute exec pactl set-sink-mute @DEFAULT_SINK@ toggle
bindsym --locked XF86AudioMicMute exec pactl set-source-mute @DEFAULT_SOURCE@ toggle

# 将特定按键绑定到 playerctl 以控制媒体
bindsym --locked XF86AudioPlay exec playerctl play-pause
bindsym --locked XF86AudioPause exec playerctl play-pause
bindsym --locked XF86AudioPrev exec playerctl previous
bindsym --locked XF86AudioNext exec playerctl next
bindsym --locked XF86AudioStop exec playerctl stop

# 将特定按键绑定到 brightnessctl 以控制屏幕亮度
bindsym --locked XF86MonBrightnessDown exec brightnessctl set 5%-
bindsym --locked XF86MonBrightnessUp exec brightnessctl set 5%+

# 将特定按键绑定到 grim 以截图
bindsym Print exec grim

# 将特定按键绑定到 wmenu-run 以启动应用程序
bindsym XF86Search exec wmenu-run
```

有关详细信息和替代实用程序，请参阅：

- [PulseAudio#键盘控制音量](../../04_硬件与外设/声音/PulseAudio.md#键盘控制音量)
- [WirePlumber#键盘控制音量](../../06_软件与应用/多媒体/WirePlumber.md#键盘控制音量)
- [ALSA#键盘控制音量](../../04_硬件与外设/声音/ALSA.md#键盘控制音量)
- [背光#背光实用程序](../../04_硬件与外设/笔记本电脑/背光.md#背光实用程序)
- [MPRIS](../../06_软件与应用/多媒体/MPRIS.md)。

> **提示：**`bindsym` 的 `--locked` 参数用于允许在锁屏下执行快捷键。

> **提示：**[wev](https://archlinux.org/packages/?name=wev)包 工具提供类似于 [xorg-xev](https://archlinux.org/packages/?name=xorg-xev)包 的功能，但运行于 Wayland。

> **注意：**Systemd 会处理一些特殊按键，如电源键和盖子打开/关闭事件。这可能会干扰在 sway 中配置的快捷键。请参阅 [loginctl(1)](https://man.archlinux.org/man/loginctl.1) 与 [logind.conf(5)](https://man.archlinux.org/man/logind.conf.5) 以获取有关如何在 [systemd](../../01_安装与包管理/初始化/Systemd.md) 中配置它们的详细信息。

#### 图形指示条

在调整某些百分比值设置（如亮度或音量）时，可以显示一个图形指示条。在 Sway 中提供此工具的一个不错的选择是 [wob](https://archlinux.org/packages/?name=wob)包（或 [wob-git](https://aur.archlinux.org/packages/wob-git/)AUR），其是 Wayland 上 layer-shell 协议的实现，提供了流行的 X 工具 [xob](https://aur.archlinux.org/packages/xob/)AUR 的部分功能。有关使用示例，请参阅[项目网站](https://github.com/francma/wob)。

#### 工作区概览

如果您在工作区开启了很多窗口，导致难以分辨，那么 [sov](https://aur.archlinux.org/packages/sov/)AUR 可以派上用场。它是一个叠加层，可以显示所有工作区的概览，使 sway 导航更容易。其可以显示程序名称、窗口标题，支持多 output 配置。关于详细信息，请参阅[项目网站](https://github.com/milgra/sov)。

### 空闲管理

Sway 有一个名为 [swayidle](https://archlinux.org/packages/?name=swayidle)包 的专用空闲管理守护程序来处理空闲会话。可以用不同的方法来启动和参数化守护程序。最简单的方法是使用 sway 本身的配置。`swayidle` 接受大量参数来配置 `timeout`（超时，又名“空闲”）、`resume`（恢复，超时后从空闲中恢复）、`before-sleep`（睡眠前）等事件，请参阅 [swayidle(1)](https://man.archlinux.org/man/swayidle.1) 了解更多细节和事件的进一步解释。然后，可以为每个事件分配一个操作。要为一个事件分配多个操作，只需重复触发器即可。

以下配置可使 `swayidle` 在 30 分钟后锁定屏幕，并在 5 秒后将其关闭：

```
~/.config/sway/config
```

```
exec swayidle -w \
	timeout 1800 'swaylock -f' \
	timeout 1805 'swaymsg "output * power off"' \
		resume 'swaymsg "output * power on"'
```

要更快地关闭锁定屏幕，例如在10秒后，请为锁定管理器设置进程列表，并相应地执行 `swaymsg "output * power off"` ，如下：

```
timeout 10 'if pgrep -x swaylock; then swaymsg "output * power off"; fi' resume 'if pgrep -x swaylock; then swaymsg "output * power on"; fi'
```

要在屏幕锁定前挂起和暂停播放媒体，请将以下配置添加到 swayidle 命令：

```
before-sleep 'playerctl pause;swaylock -f'
```

> **注意：**Systemd 也处理一些空闲事件，这些事件可能与在 sway 中配置的事件冲突。请参阅 [loginctl(1)](https://man.archlinux.org/man/loginctl.1) 和 [logind.conf(5)](https://man.archlinux.org/man/logind.conf.5) 以获取关于如何配置它们的详细信息。

若您不希望在 Firefox、Chrome 或 VLC 播放视频时触发 swaylock，您可以用 [idlehack-git](https://aur.archlinux.org/packages/idlehack-git/)AUR 监听 dbus 的 screensaver inhibit（阻止屏保）请求并调用 swayidle-inhibit。Firefox、Chrome 和 VLC 等程序会发出该事件以阻止系统空闲。

#### 恢复时屏幕内容短暂显示

使用 `before-sleep swaylock` 可能会使系统从睡眠中恢复时屏幕内容短暂显示。欲缓解该问题，您可使 logind 忽略盖子（lid）事件并改用 Sway 处理盖子和电源按钮事件。

```
/etc/systemd/logind.conf
```

```
[Login]
HandlePowerKey=ignore
HandlePowerKeyLongPress=ignore
HandleSuspendKey=ignore
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore
```

```
~/.config/sway/config
```

```
bindswitch --locked lid:toggle exec swaylock --daemonize && systemctl sleep

exec swayidle -w \
          timeout 60  'if pgrep swaylock; then systemctl sleep; fi' \
          timeout 150 'swaylock --daemonize' \
          timeout 155 'systemctl sleep'
```

使用 `--locked` 选项以在屏幕锁定时合盖休眠系统。若要使笔记本在合盖后依然工作，使用 `unbindswitch lid:toggle`。可以使用 `pgrep swaylock` 以使电脑被意外唤醒后再次休眠。

logind 集成 [bug 较多](https://github.com/swaywm/swayidle/pull/133)且[难以维护](https://github.com/swaywm/swayidle/issues/117)。另见 。

### 悬浮窗口和窗口分配

要启用悬浮窗口或窗口分配，请打开应用程序，然后使用 `app_id`、`class`、`instance` 和 `title` 属性来启用悬浮窗口/窗口分配。以下命令可以列出所有打开的窗口的属性：

```
$ swaymsg -t get_tree
```

欲获取所有打开的窗口的 `app_id`，请执行：

```
$ swaymsg -t get_tree | grep "app_id"
```

仅获取当前选中窗口的 `app_id`，请执行：

```
$ swaymsg -t get_tree | jq -r '..|try select(.focused == true)'
```

X11 窗口没有 `app_id` 属性，要匹配它们，您可以使用 `class`、`window_type`、`window_role` 和/或 `instance` 等属性。请在 `swaymsg -t get_tree` 命令的输出中查找相关属性并为窗口创建足够详细的规则。

```
~/.config/sway/config
```

```
for_window [app_id="galculator"] floating enable
for_window [window_type="dialog"] floating enable
for_window [window_role="dialog"] floating enable
assign [app_id="firefox"] 3
assign [class="^Urxvt$" instance="^htop$"] 9
```

这类似于在[X11](../X%20服务器/Xorg.md)中使用 [xorg-xprop](https://archlinux.org/packages/?name=xorg-xprop)包 查找 `class` 或 `wm_name` 属性。

使用多个显示器时，悬浮的 scratchpad（暂存器）窗口可能会变得很大，覆盖多个显示器。可以使用以下命令将悬浮窗口居中并调整为当前显示器大小的 80%：

```
$ swaymsg move position center; swaymsg resize set 80ppt 80ppt
```

### 剪贴板

参见[剪贴板](../../06_软件与应用/软件列表/剪贴板.md)。

### Xresources

在 Sway 中使用 `~/.Xresources` 需要把它拷贝到`~/.Xdefaults`。

### Xwayland

请参阅 [Wayland#Xwayland](../Wayland/Wayland.md#Xwayland) 以了解详细信息和可用软件包的概述。

默认情况下 Xwayland 处于启用状态。

欲完全禁用 Xwayland 并运行“纯”Wayland 会话，请使用以下配置以禁用 Xwayland：

```
~/.config/sway/config
```

```
xwayland disable
```

若您想一眼知晓哪些窗口使用 Xwayland，可以使用以下配置：

```
~/.config/sway/config
```

```
for_window [shell="xwayland"] title_format "[XWayland] %title"
```

> **注意：**一些程序需要[特殊的环境变量或配置选项](../Wayland/Wayland.md#图形库)才能在原生 Wayland 下运行，而另一些程序（包括大多数专有应用程序）根本不支持 Wayland。目前，建议保持启用 Xwayland，以便使用旧版应用程序。

### 使用另一个 wlroots 渲染器

要使用另一个渲染器如 Vulkan，请参阅 [Wayland#在基于 wlroots 的混成器上使用另一个渲染器](../Wayland/Wayland.md#在基于_wlroots_的混成器上使用另一个渲染器)。

### 程序自启动

参见 [i3#自启动程序](../动态窗口管理器/I3.md#自启动程序)章节，并将配置文件名调整为适用于 Sway 的版本。

## 提示和技巧

### 启动时启用 NumLock

Sway 在启动时默认禁用 `NumLock`。欲使其自动启用，请将键盘的 `xkb_numlock` 输入配置设置为 `enable`。例如，欲使所有键盘如此，请将以下行添加到 Sway 配置：

```
~/.config/sway/config
```

```
input type:keyboard xkb_numlock enabled
```

任何情况下，都可以通过键盘上的相关按键来切换 `NumLock` 状态。

> **提示：**如需，也可以用 `xkb_capslock` 以相同方式设置 `CapsLock`。

> **注意：**用通配符启用这些可能会导致在重新加载 Sway 配置文件时 Firefox 崩溃，参见 [Bugzilla 1652820](https://bugzilla.mozilla.org/show_bug.cgi?id=1652820)，请尝试[指定特定键盘](https://bugzilla.mozilla.org/show_bug.cgi?id=1652820#c31)而非通配符。

### 当前键盘布局

可以按如下方式通过 [jq](https://archlinux.org/packages/?name=jq)包 检索当前键盘布局，其中 `*kbd_identifier*` 需要替换为键盘的标识符：

```
$ swaymsg -t get_inputs | jq -r '.[] | select(.identifier == "kbd_identifier") | .xkb_active_layout_name'
```

### 组合键

组合键（compose key）和其它按键结合使用可用于输入键盘不支持的字符。例如将 `PrintScreen` 设为组合键：

```
$ swaymsg 'input * xkb_options compose:prsc'
```

可在 Xorg/Keyboard configuration#Configuring compose key（英语：[Xorg/Keyboard configuration#Configuring compose key](https://wiki.archlinux.org/title/Xorg/Keyboard_configuration#Configuring_compose_key)） 查看可用的组合。组合也可以在 XCompose 文件（英语：[Xorg/Keyboard configuration#Key combinations](https://wiki.archlinux.org/title/Xorg/Keyboard_configuration#Key_combinations)）中配置，需要重新启动应用程序才能使更改生效。

### 背光切换

若要使用某按键（如 `Pause`）关闭（和打开）显示器，请在 Sway`config` 中绑定以下脚本：

```
#!/bin/sh
read lcd < /tmp/lcd
    if [ "$lcd" -eq "0" ]; then
        swaymsg "output * power on"
        echo 1 > /tmp/lcd
    else
        swaymsg "output * power off"
        echo 0 > /tmp/lcd
    fi
```

也可以直接使用 [toggle](https://github.com/swaywm/sway/pull/6099) 选项，只是使用多显示器时需要明确指定 output：

```
$ swaymsg "output output_name power toggle"
```

### 屏幕捕获和屏幕共享

请查看[屏幕捕获#Wayland](../../06_软件与应用/多媒体/屏幕捕获.md#Wayland).

### 色温调节

请查看[背光#Wayland](../../04_硬件与外设/笔记本电脑/背光.md#Wayland).

### 色彩管理

可以将以下行添加到 Sway 配置以使用色彩管理配置文件：

```
~/.config/sway/config
```

```
output * 色彩管理配置文件 icc /path/to/your/color_profile.icc
```

### 用键盘控制 swaynag

Swaynag 是 sway 附带的默认警告/提示程序，仅支持用户用鼠标与之交互。诸如 [swaynagmode](https://aur.archlinux.org/packages/swaynagmode/)AUR 之类的帮助程序可通过键盘快捷键启用交互。

Swaynagmode 的工作原理是首先启动 swaynag，然后侦听触发操作的信号，例如选择下一步按钮、关闭提示或接受所选按钮。这些信号会被另外的带有控制参数的 swaynagmode 脚本实例（如 `swaynagmode --select right` 和 `swaynagmode --confirm`）发送。

默认情况下，Swaynagmode 在初始化时触发 sway 的 `nag` 模式，在退出时返回 `default`。这样，您就可以在 sway 配置中轻松定义快捷键：

```
~/.config/sway/config
```

```
set $nag exec swaynagmode
mode "nag" {
  bindsym {
    Ctrl+d    mode "default"

    Ctrl+c    $nag --exit
    q         $nag --exit
    Escape    $nag --exit

    Return    $nag --confirm

    Tab       $nag --select prev
    Shift+Tab $nag --select next

    Left      $nag --select next
    Right     $nag --select prev

    Up        $nag --select next
    Down      $nag --select prev
  }
}
```

> **注意：**从 sway 版本 1.2 开始，模式名称区分大小写。

您可将 sway 配置为通过配置命令 `swaynag_command swaynagmode` 使用 swaynagmode。

### 更改光标主题和大小

要设置[光标主题](../X%20服务器/光标主题.md)和大小，请使用以下配置：

```
~/.config/sway/config
```

```
seat seat0 xcursor_theme 光标主题 光标大小
```

其中`*光标主题*`可以设置或替换为特定名称（如 `default`、`Adwaita` 或 `Simple-and-Soft`），以及`*光标大小*`值如 `48`。

要查看二者的值，可以使用 `echo $XCURSOR_SIZE` 和 `echo $XCURSOR_THEME`。

更改配置后需要重新加载 Sway 以使更改生效。

> **注意：**Wayland 光标可由应用程序确定，这意味着应用程序可能不会遵循 `$XCURSOR_SIZE` 和 `$XCURSOR_THEME` 的值。

### 使用 systemd 管理仅用于 Sway 的守护程序

Systemd 提供 `graphical-session.target` 和 `graphical-session-pre.target` [用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)。

Services can be made `PartOf=` either unit to let them run as long as or before, respectively, any graphical session is running *and* terminate them cleanly when exiting it, whether [Xorg](../X%20服务器/Xorg.md) or [Wayland](../Wayland/Wayland.md).

If Sway is the only window manager on a machine, then `graphical-session.target` may be sufficient even for Sway-specific services (like [swayidle](https://archlinux.org/packages/?name=swayidle)包 or [kanshi](../Wayland/Kanshi.md)); but otherwise, a more specific `sway-session.target` file can be used (such as the one provided by [sway-contrib](https://archlinux.org/packages/?name=sway-contrib)包).

Additionally, services started by systemd need to be informed of the necessary environment variables; this is taken care of by a file in `/etc/sway/config.d/` provided by [sway](https://archlinux.org/packages/?name=sway)包, which should be processed if the note at the top of [#Configuration](#Configuration) has been followed. (But those environment variables should also be unset when exiting Sway, otherwise it or other services may fail to start after the first exit.)

All this can be done using [sway-systemd-git](https://aur.archlinux.org/packages/sway-systemd-git/)AUR or [uwsm](https://archlinux.org/packages/?name=uwsm)包 (which also take care of [#Working with systemd-oomd](#Working_with_systemd-oomd)), or manually.

> **注意：**[sway-services-git](https://aur.archlinux.org/packages/sway-services-git/)AUR has unit startup order issues. 

#### 手动配置

将以下行添加到 Sway 的配置文件中（例如，将该行追加到 `~/.config/sway/config`或将新文件添加到 `/etc/sway/config.d/`）：

```
~/.config/sway/config
```

```
...
...
...
exec_always systemctl --user start sway-session.target
```

This ensures that `sway-session.target` is activated whenever Sway starts, and *after* it has created the Wayland socket etc. Make sure to place that line after any commands that affect how systemd services would be started, which generally includes the `include /etc/sway/config.d/*` directive.

Then, arrange for the target to be stopped before Sway exits. Note that `systemctl --user stop sway-session.target` will not work, as the command returns before the services are done shutting down; consider creating a unit that `Conflicts=` with that, and starting it instead .

Finally, link the desired services to `sway-session.target` or `graphical-session.target`:

- If an existing service file exists, you can enable it, or (such as if that would not link to the right unit) run `systemctl --user add-wants *sway-session*.target *foo*.service`.

- You can find an example of creating a brand new service file at [kanshi#Manage kanshi with systemd](../Wayland/Kanshi.md#Manage_kanshi_with_systemd).

### Working with systemd-oomd

[systemd-oomd(8)](https://man.archlinux.org/man/systemd-oomd.8) tracks resource usage by and kills entire [Cgroups](../../02_系统管理与内核/虚拟化/控制组.md) at once, not processes. This means that, by default, a single memory-hungry service will cause the entire Sway session to be terminated.

The solution is to start each application in its own cgroup. Some of the packages recommended in [#Manage Sway-specific daemons with systemd](#Manage_Sway-specific_daemons_with_systemd), like [uwsm](https://archlinux.org/packages/?name=uwsm)包 or [sway-systemd-git](https://aur.archlinux.org/packages/sway-systemd-git/)AUR, already take care of this. But it can also be accomplished manually by ensuring that applications are started via *systemd-run* (see [systemd-run(1)](https://man.archlinux.org/man/systemd-run.1)).

Instead of using a `swaymsg exec -- *foo*` command, create a script:

```
~/.local/share/sway_exec_wrapper
```

```
#!/usr/bin/env sh
exec systemd-run --user --ignore-failure --scope --unit "app-$1-$(uuidgen)" -- "$@"
```

and use `swaymsg exec ~/.local/share/exec_wrapper -- *foo*`.

> **提示：**If [#Manage Sway-specific daemons with systemd](#Manage_Sway-specific_daemons_with_systemd) has been followed, add `-p PartOf=graphical-session.target` to let the application exit cleanly when exiting Sway.

The `--ignore-failure` option is used, because a graphical application's unclean exit should not cause the system state to become "degraded"; `--scope` so that the initial process can fork without the entire application being closed *and* for Sway to assign the first window to the correct workspace. The `--unit` parameter is named according to [systemd's recommendations](https://systemd.io/DESKTOP_ENVIRONMENTS/#xdg-standardization-for-applications).

### 启动后更改屏幕分辨率/布局

您可使用 GUI 程序 [nwg-displays](https://archlinux.org/packages/?name=nwg-displays)包、[wdisplays](https://archlinux.org/packages/?name=wdisplays)包、[swayrandr-git](https://aur.archlinux.org/packages/swayrandr-git/)AUR 或终端程序 [wlr-randr](https://archlinux.org/packages/?name=wlr-randr)包 来更改屏幕分辨率和方向、排列显示器以及设定缩放。

### 创建无外设（Headless）输出

创建与物理 video 接口无关的输出 （`HEADLESS-1`、`HEADLESS-2`等）：

```
$ swaymsg create_output
```

显示新输出的说明：

```
$ swaymsg -pt get_outputs | grep -A 10 HEADLESS
```

使用 `output` 命令配置新的输出，例如：

```
~/.config/sway/config
```

```
output HEADLESS-1 {
pos 1920,0
mode 1280x720@75Hz
}
```

### 将修饰键（modifier）修改为 CapsLock 并保留 Super

要将修饰键更改为 `CapsLock` 并在 US 键盘布局上继续使用 `Super`，请创建 `~/.config/xkb/symbols/custom`，具有以下内容：

```
~/.config/xkb/symbols/custom
```

```
xkb_symbols "basic" {
    include "us"
    name[Group1]= "English (US Custom)";
    key <CAPS> { [ Hyper_L ] };
    modifier_map Mod4 { Hyper_L };
    key <LWIN> { [ Super_L ] };
    modifier_map Mod5 { Super_L };
};
```

对于其他语言，请编辑第二行和第三行，并在 Sway 配置文件开头附加引用该键盘布局：

```
~/.config/sway/config
```

```
input * xkb_layout custom
set $mod Mod4
set $super Mod5
```

### 通过 VNC 远程访问无外设（Headless） Sway 会话

以下示例教程讲述了通过 SSH 启动并通过 VNC 远程访问无外设 Sway 会话。

[wayvnc](https://archlinux.org/packages/?name=wayvnc)包 是一个 VNC 服务器，适用于基于 wlroots 的 Wayland 混成器，可以用于承载无外设 Sway 会话。

要启动一个无外设 Sway 会话，请将 `WLR_BACKENDS` 设为 `headless`，可选将 `WLR_LIBINPUT_NO_DEVICES` 设为 `1`（参见 [wayvnc FAQ](https://github.com/any1/wayvnc/blob/master/FAQ.md)）：

```
$ WLR_BACKENDS=headless WLR_LIBINPUT_NO_DEVICES=1 sway
```

> **提示：**在命令结尾添加 `&` 以使其后台运行。

现在，请运行 `wayvnc` 以启动 VNC 服务器：

```
$ WAYLAND_DISPLAY=wayland-1 wayvnc
```

> **注意：**默认情况下，[wayvnc](https://archlinux.org/packages/?name=wayvnc)包 仅会监听本机 5900 端口（`localhost:5900`），可以另行配置（参见 `man wayvnc`）。您可能会想按照 [OpenSSH#转发其他端口](../../03_网络与服务/SSH/OpenSSH.md#转发其他端口) 所述转发端口。请注意 [wayvnc](https://archlinux.org/packages/?name=wayvnc)包 默认**不加密**且**不验证身份**！

### 输出热插拔钩子

若您想在一个输出连接（或断开连接）时搞点事情，可以阅读 [kanshi](../Wayland/Kanshi.md)。

## 疑难解答

### 应用程序启动器

> **提示：**Sway wiki 包含一个[已知应用程序启动器列表](https://github.com/swaywm/sway/wiki#program-launchers)。

以下[应用程序启动器](../../06_软件与应用/应用程序/应用程序列表_其它.md#应用程序启动器)已知和 Sway 集成良好：

- [wmenu](https://archlinux.org/packages/?name=wmenu)包 or [bemenu](https://archlinux.org/packages/?name=bemenu)包 are largely drop-in replacements for `dmenu`.

- [rofi](../应用程序启动器/Rofi.md) now supports Wayland natively, but [wofi](https://archlinux.org/packages/?name=wofi)包 is still a good alternative.

- [fuzzel](https://archlinux.org/packages/?name=fuzzel)包 is an application launcher similar to rofi's *drun* mode.

- Any `dmenu` replacement can be combined with [j4-dmenu-desktop](https://archlinux.org/packages/?name=j4-dmenu-desktop)包's `--dmenu` option to make an equivalent of [i3-dmenu-desktop(1)](https://man.archlinux.org/man/i3-dmenu-desktop.1).

- [krunner(1)](https://man.archlinux.org/man/krunner.1) (from [plasma-workspace](https://archlinux.org/packages/?name=plasma-workspace)包) can be used stand-alone.

- You can also [build your own](https://github.com/swaywm/sway/issues/1367) with a floating terminal and [fzf](https://archlinux.org/packages/?name=fzf)包.

[dmenu](https://archlinux.org/packages/?name=dmenu)包 and other non-Wayland launchers also function well in Sway, but run under [Xwayland](../Wayland/Wayland.md) and suffer from an issue where they can become unresponsive if focus is moved to a native Wayland window.

If [Xwayland is enabled](#Xwayland) and [bemenu](https://archlinux.org/packages/?name=bemenu)包 does not run under Wayland by default (thus being subject to the above issues), set the `BEMENU_BACKEND=wayland` [environment variable](../../02_系统管理与内核/系统管理/环境变量.md).

### 虚拟化

Sway 可以在 [VirtualBox](../../06_软件与应用/Oracle/VirtualBox.md) 和 [VMware](../../02_系统管理与内核/虚拟机管理/VMware.md) ESXi 中使用。

要使 Sway 在 [QEMU](../../02_系统管理与内核/虚拟机管理/QEMU.md) 中工作，必须以 `-vga qxl` 参数启动QEMU。另见 [QEMU#qxl](../../02_系统管理与内核/虚拟机管理/QEMU.md#qxl)。

#### 无法从 tty 启动 Sway

对于 ESXi， 您需要在 *Hardware Configuration > Video card settings* 下启用 3D 支持。另见 [VMware#在 Intel、Optimus 和 AMD 上启用 3D 图形加速](../../02_系统管理与内核/虚拟机管理/VMware.md#在_Intel、Optimus_和_AMD_上启用_3D_图形加速)。

#### 光标不可见

当使用某些图形驱动程序（例如VMSVGA图形控制器或专有的 [NVIDIA](../图形/NVIDIA.md) 驱动）时，光标可能会变得不可见。可以使用软件光标来解决此问题，具体讨论参见 [Issue 3814](https://github.com/swaywm/sway/issues/3814)：

```
$ export WLR_NO_HARDWARE_CURSORS=1
```

### 未检测到 sway socket

使用 `swaymsg` 时（如 `swaymsg -t get_outputs`）有时会返回以下消息：

```
sway socket not detected.
ERROR: Unable to connect to
```

当在终端多路复用器（如 [GNU Screen](../../06_软件与应用/GNU/GNU%20Screen.md) 或 [tmux](../../07_开发与工具/终端多路复用器/Tmux.md)）内运行时，`swaymsg` 无法连接到 `SWAYSOCK` 中提供的套接字。

要查看当前 `SWAYSOCK` 的值，请执行：

```
$ env | grep -F "SWAYSOCK"
```

```
SWAYSOCK=/run/user/1000/sway-ipc.1000.4981.sock
```

欲解决该问题，您可以尝试连接到一个正在运行的 sway 进程的套接字：

```
$ export SWAYSOCK=/run/user/$(id -u)/sway-ipc.$(id -u).$(pgrep -x sway).sock
```

欲避免此错误，请在终端多路复用器之外运行该命令。

### 无法连接 Wayland 服务器

Tmux 会为每个会话创建环境变量（执行 `tmux show-environment` 以查询），因此，若您想要使用 [tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect) 或 [tmux-continuum](https://github.com/tmux-plugins/tmux-continuum) 重新恢复一个已有的 tmux 会话，或 tmux 服务器在 sway 启动前就在运行，环境变量就会过期。

将以下内容添加到 `.tmux.conf`，之后您可以在恢复会话前使用 `update-environment` 更新环境变量：

```
set-option -g update-environment "DISPLAY WAYLAND_DISPLAY SWAYSOCK SSH_AUTH_SOCK"
```

### 无法获取 socket 路径

从 `swaymsg -t` 命令请求在tty上的消息可能会返回以下消息：

```
Unable to retrieve socket path
```

`SWAYSOCK` 环境变量在启动Sway后设置，因此解决此错误的一个方法是在Sway内部的终端中请求 `swaymsg -t [message]`。

### 按键设置和键盘布局

默认情况下，如果您使用多个键盘布局（如 `input * xkb_layout "us,ru"`），在切换到某个次要布局时可能会导致快捷键失效。

得益于 [Pull 3058](https://github.com/swaywm/sway/pull/3058) ，现在您只需在相应的 `bindsym` 行中添加 `--to-code`，如下：

```
bindsym --to-code {
  $mod+$left focus left
  $mod+$down focus down
  $mod+$up focus up
  $mod+$right focus right
}
```

亦可创建一个变量 `set $mybind bindsym --to-code`，然后将所有的 `bindsym` 替换为 `$mybind`，如下：

```
$mybind $mod+w thing
```

### Java 应用程序

当打开一些基于Java的应用程序（如 JetBrains的 IntelliJ、CLion 或 PyCharm）时，可能会显示空白画面。欲解决该问题，可以将环境变量 `_JAVA_AWT_WM_NONREPARENTING` 设为 `1` 来启动应用程序。

若您从应用程序启动器（如 [rofi](../应用程序启动器/Rofi.md) 或 [dmenu](../Suckless/Dmenu.md)）启动应用程序，则可以修改应用程序的[桌面项](../../01_安装与包管理/配置文件/桌面项.md#编辑环境变量)。

OpenJDK 11 和 Sway 1.5 已经修复了一些 Java 应用程序的问题。然而，对于某些应用程序，需要进行额外的配置才能使用更新版本的 OpenJDK。对于 Android Studio，您需要设置 `STUDIO_JDK=/usr/lib/jvm/java-11-openjdk/`。

JRE 硬编码了一个窗口管理器列表，但该列表不包含 Sway，若您遇到灰色面板、菜单无焦点或窗口尺寸调整错误等问题，可以尝试[伪装成其它窗口管理器](../../06_软件与应用/Oracle/Java.md#伪装成另一个窗口管理器)。

### 边框上的滚动

如果在应用程序边框上使用鼠标滚轮导致Sway崩溃，您可以为 `app_id`（例如Firefox）使用`border none` 来禁用边框。

### “cannot open display” 错误

如果一个程序启动后出现“cannot open display”（无法打开显示器）并崩溃，那么该应用程序很可能是 X11 程序。您可以使用 Xwayland 兼容层在 Wayland 下运行 X11 程序，详见 [#Xwayland](#Xwayland)。

### WINE 中鼠标不工作

运行程序时，WINE 需要设定一个主显示器（primary monitor），但是 Wayland 没有主显示器的概念，这会引发问题（如 “clicks not registering”）。您可以使用 [xrandr](../Xorg%20命令/Xrandr.md) 为 Xwayland 指定主显示器，只需向您的 Sway 配置添加以下内容：

```
~/.config/sway/config
```

```
...
exec_always xrandr --output XWAYLAND0 --primary
...
```

若如此做，因为 Sway 的一个 [bug](https://github.com/swaywm/sway/issues/6651)，您需要将屏幕显示偏移量设为 `0,0`。

需要注意 XWAYLAND0（或其它 XWAYLAND 显示名称）可能不代表您的显示器，其后缀数字也可能在不同会话中变化。可以使用以下配置，其可将第一个 XWAYLAND 显示设定为主显示器：

```
exec_always xrandr --output $(xrandr | grep -m 1 XWAYLAND | awk '{print $1;}') --primary
```

您可按需调整此配置，使用不带参数的 `xrandr` 命令可以查看各

> **注意：**Xwayland 会把新连接的显示器识别为全新的显示器，这包括了关闭后又重新打开的显示器。若您设置的“主显示器”也被关闭后又打开，其将不再被视为主显示器。

### NVIDIA GPU 上游戏闪烁

即便[官方不支持 NVIDIA 之类的专有驱动](https://github.com/swaywm/sway/wiki#nvidia-users)，但依然可以使用，甚至可以用来玩游戏。若如此做，您可能会看到屏幕上半区闪烁，可以尝试将 [wlroots0.19](https://aur.archlinux.org/packages/wlroots0.19/)AUR 替换为 [wlroots-nvidia](https://aur.archlinux.org/packages/wlroots-nvidia/)AUR，也可以手动用补丁编译您自己的版本。

### System freezes when toggling output power on hybrid NVIDIA setups

On hybrid-GPU laptops with an external monitor connected to the NVIDIA GPU, using `swaymsg "output * power off"` (e.g. via [swayidle](https://archlinux.org/packages/?name=swayidle)包) can deadlock `nvidia-modeset`, freezing the whole desktop. Confirm with `journalctl -b -1 -g "blocked for more than"`: a `task sway state:D ... blocked on a semaphore likely last held by task nvidia-modeset` line means this is the cause. Workaround: avoid powering off NVIDIA-attached outputs, e.g. target only the integrated GPU's output or suspend the system instead. See [wlroots#2474](https://github.com/swaywm/wlroots/issues/2474).

### 屏幕捕获/WebRTC 不工作

确保您的配置包含以下内容：

```
include /etc/sway/config.d/*
```

然而，AUR 的部分 -git 软件包不提供 `/etc/sway/config.d`，此时您需要添加：

```
exec dbus-update-activation-environment DISPLAY I3SOCK SWAYSOCK WAYLAND_DISPLAY XDG_CURRENT_DESKTOP=sway
```

## 参见

- [Sway 官方网站](https://swaywm.org)
- [Sway GitHub 项目](https://github.com/swaywm/sway)
- [Sway wiki](https://github.com/swaywm/sway/wiki)
- [Sway 1.0 发布](https://drewdevault.com/2019/03/11/Sway-1.0-released.html)
