# 第 20 章　Canonical

> 这一章收拢两件来自 Canonical、彼此不相干的事：Bazaar 的安装与使用，以及用 LightDM 接管系统登录。两节篇幅悬殊，顺序应据此安排——LightDM 必读，安装、启用、配置和调整、疑难问题依次走一遍，命令行工具与测试边读边动手核对；Bazaar 只讲安装和使用，可以跳过，需要时当速查翻。
> 
> 最容易踩的坑是把"安装"当成"生效"：包装上不等于登录界面就换了，还要启用 LightDM 服务；改过配置先按测试一节验证，否则等系统起不来再去翻疑难问题，会多绕好几圈。两节彼此独立，不必期待承接关系。

<!-- 本章 2 节，共 33 KB -->

## 20.1　Bazaar

*难度：入门 ｜ 篇幅：1.4 KB ｜ 所属：Canonical*

将要讲到：安装、使用

[Bazaar](https://bazaar.canonical.com/) 是一个版本控制系统，可以帮助您跟踪一段时间内的项目历史记录并与他人轻松地进行协作。

## 安装

安装 [bzr](https://aur.archlinux.org/packages/bzr/)AUR 包。

## 使用

请参阅 [bzr(1)](https://linux.die.net/man/1/bzr)。

### 使用 xinetd 设置 Bazaar 服务器

根据需要添加 `*bzr-usr*` [用户](../../08_安全与加密/安全/用户和用户组.md)。

创建一个存储库：

```
$ bzr init /home/bzr/repo.bzr
$ chown -R bzr_usr /home/bzr/repo.bzr
```

添加 *xinetd* 的配置：

```
service bzr
{
	flags			= REUSE
	socket_type		= stream
	wait			= no
	user			= bzr_usr
	server			= /usr/bin/bzr
	server_args		= serve --inet --directory=/home/bzr/repo.bzr
	env			= HOME=/home/bzr
	log_on_failure		+= USERID
	disable			= no
	cps			= 50 10
	instances		= 60
}
```

## 20.2　LightDM

*难度：高级 ｜ 篇幅：31.3 KB ｜ 所属：Canonical*

将要讲到：安装、启用 LightDM、命令行工具、测试、配置和调整、疑难问题

[LightDM](https://github.com/canonical/lightdm) 是一个跨桌面环境的[显示管理器](../../05_桌面与图形/图形用户界面/显示管理器.md)。它的特点有:

- 跨桌面 - 支持不同的桌面环境。
- 支持多种显示技术(X, Mir, Wayland, ...)。
- 轻量级 - 低内存占用以及高性能。
- 支持访客会话。
- 支持远程登录(传入 - XDMCP, [VNC](../../03_网络与服务/远程服务器/TigerVNC.md), 传出 - XDMCP, [PAM](../../02_系统管理与内核/内核/PAM.md))。
- 完善的测试组件。
- 低代码复杂度。

更多关于LightDM的特性可以在[这里](https://www.freedesktop.org/wiki/Software/LightDM/Design)找到。

## 安装

安装 [lightdm](https://archlinux.org/packages/?name=lightdm)包.

之后跟随[显示管理器#加载显示管理器](../../05_桌面与图形/图形用户界面/显示管理器.md#加载显示管理器)这一章节的指示，配置LightDM作为显示管理器的自启动。

### Greeter

使用 LightDM 可能需要安装一个 greeter。 greeter 是提示用户输入凭据、让用户选择一个会话等的图形界面。在配置了自动登录的情况下，可以不使用 greeter。否则需要安装 [xorg-server](https://archlinux.org/packages/?name=xorg-server)包 和至少一个下面列出的 greeter软件包。

基于GTK的 greeter:

- **LightDM GTK Greeter** — 被 LightDM **默认**使用的 greeter, 除非用户手动设置使用其他 greeter。

: [https://github.com/Xubuntu/lightdm-gtk-greeter](https://github.com/Xubuntu/lightdm-gtk-greeter) || [lightdm-gtk-greeter](https://archlinux.org/packages/?name=lightdm-gtk-greeter)包

- **lightdm-pantheon-greeter** — 来自 ElementaryOS 项目的 greeter。

: [https://github.com/elementary/greeter](https://github.com/elementary/greeter) || [lightdm-pantheon-greeter](https://archlinux.org/packages/?name=lightdm-pantheon-greeter)包

- **slick-greeter** — 比 [lightdm-gtk-greeter](https://archlinux.org/packages/?name=lightdm-gtk-greeter)包 更注重美化的 GTK greeter , 从 [lightdm-unity-greeter](https://aur.archlinux.org/packages/lightdm-unity-greeter/)AUR 分支发展而来, 被 Linux Mint 发行版默认使用。

: [https://github.com/linuxmint/slick-greeter](https://github.com/linuxmint/slick-greeter) || [lightdm-slick-greeter](https://archlinux.org/packages/?name=lightdm-slick-greeter)包

- **LightDM Web Greeter** — 一个以 Webkit2 为主题引擎的 greeter。 **不过该项目已经在2019年5月22日停止开发。**

: [https://github.com/antergos/web-greeter](https://github.com/antergos/web-greeter) || [lightdm-webkit2-greeter](https://archlinux.org/packages/?name=lightdm-webkit2-greeter)包

- **Mini-Greeter** — 一个最小化可配置的单用户 GTK3 greeter。

: [https://github.com/prikhi/lightdm-mini-greeter](https://github.com/prikhi/lightdm-mini-greeter) || [lightdm-mini-greeter](https://aur.archlinux.org/packages/lightdm-mini-greeter/)AUR

- **Nody Greeter** — 一个能以 web 技术创建精美主题的 LightDM greeter。基于 Node.js、 Electron.js 和 NodeGTK。

: [https://github.com/JezerM/nody-greeter](https://github.com/JezerM/nody-greeter) || [nody-greeter](https://aur.archlinux.org/packages/nody-greeter/)AUR

基于Qt的 greeter:

- **Web Greeter** — 一个高颜值的现代化 greeter，使用 PyQtWebEngine 作为主题引擎，是 [lightdm-webkit2-greeter](https://archlinux.org/packages/?name=lightdm-webkit2-greeter)包 的替代。

: [https://github.com/JezerM/web-greeter](https://github.com/JezerM/web-greeter) || [web-greeter](https://aur.archlinux.org/packages/web-greeter/)AUR

- **LightDM KDE Greeter** — 一个由 KDE 开发的 LightDM greeter。

: [https://invent.kde.org/plasma/lightdm-kde-greeter](https://invent.kde.org/plasma/lightdm-kde-greeter) || [lightdm-kde-greeter](https://aur.archlinux.org/packages/lightdm-kde-greeter/)AUR

其他 greeter:

- **lightdm-deepin-greeter** — 由[深度](../../05_桌面与图形/桌面环境/深度桌面环境.md)桌面提供。

: [https://github.com/linuxdeepin/dde-session-shell](https://github.com/linuxdeepin/dde-session-shell) || [deepin-session-shell](https://archlinux.org/packages/?name=deepin-session-shell)包

- **LightDM Elephant Greeter** — 默认情况下在 [cage](https://archlinux.org/packages/?name=cage)包 Wayland 合成器中运行的小而简单的欢迎程序。

: [https://github.com/max-moser/lightdm-elephant-greeter](https://github.com/max-moser/lightdm-elephant-greeter) || [lightdm-elephant-greeter-git](https://aur.archlinux.org/packages/lightdm-elephant-greeter-git/)AUR

[lightdm-webkit2-greeter](https://archlinux.org/packages/?name=lightdm-webkit2-greeter)包 的主题:

- **Litarvan的 LightDM WebKit2 主题** — 现代且功能齐全的 Webkit2 LightDM 主题。

: [https://github.com/Litarvan/lightdm-webkit-theme-litarvan](https://github.com/Litarvan/lightdm-webkit-theme-litarvan) || [lightdm-webkit-theme-litarvan](https://archlinux.org/packages/?name=lightdm-webkit-theme-litarvan)包

- **Aether** — 以 lightdm-webkit2-greeter 为引擎的简洁、流畅、直接的 Arch Linux 主题登录屏幕。

: [https://github.com/NoiSek/Aether](https://github.com/NoiSek/Aether) || [lightdm-webkit-theme-aether](https://aur.archlinux.org/packages/lightdm-webkit-theme-aether/)AUR

[web-greeter](https://aur.archlinux.org/packages/web-greeter/)AUR 与 [nody-greeter](https://aur.archlinux.org/packages/nody-greeter/)AUR 的主题:

- **Shikai** — 一个美观、可定制的 greeter 主题。

: [https://github.com/TheWisker/Shikai](https://github.com/TheWisker/Shikai) || [shikai-theme](https://aur.archlinux.org/packages/shikai-theme/)AUR

可以通过配置文件的 `[Seat:*]` 设置修改默认 greeter:

```
/etc/lightdm/lightdm.conf
```

```
[Seat:*]
...
greeter-session=lightdm-你要用的greeter名-greeter
```

> **注意：**需要注意的一点是：对应 [lightdm-pantheon-greeter](https://archlinux.org/packages/?name=lightdm-pantheon-greeter)包 的配置文件是 `io.elementary.greeter.conf`, 而不是 `lightdm-pantheon-greeter.conf`

检查有哪些 `greeter-session` 可用的一种办法是列出 `/usr/share/xgreeters` 目录中的文件； 每个*.desktop*文件代表一个可用的欢迎程序。 在本例中，`lightdm-gtk-greeter` 和 `lightdm-webkit2-greeter` 显示为可用 greeter ：

```
$ ls -1 /usr/share/xgreeters/
```

```
lightdm-gtk-greeter.desktop
lightdm-webkit2-greeter.desktop
```

## 启用 LightDM

请[使用 systemctl 启用](https://wiki.archlinuxcn.org/wzh/Help:%E9%98%85%E8%AF%BB#控制_systemd_单元) `lightdm.service` 来让 LightDM 开机启动。参考[显示管理器#加载显示管理器](../../05_桌面与图形/图形用户界面/显示管理器.md#加载显示管理器)。

## 命令行工具

LightDM 提供一个命令行工具， `dm-tool`。它可用来锁定当前 Seat，切换会话，等等。这对'极简'窗口管理器和测试非常有用。要列出可用命令，可以运行:

```
$ dm-tool --help
```

### 用户切换

> **警告：**若不使用 [light-locker](#使用_light-locker_锁定屏幕) 或其他兼容 loginctl 的 屏幕锁定程序，执行`dm-tool lock` 或 `dm-tool switch-to-greeter` 将不会锁定会话， 请参阅 [XScreenSaver#从锁屏画面切换登录用户](../../05_桌面与图形/X%20服务器/XScreenSaver.md#从锁屏画面切换登录用户)。

LightDM 的 *dm-tool* 命令能用于允许多个用户在不同的终端上登录。运行下面的命令会发送一个请求锁定当前会话的信号，然后将启动切换到 LightDM 的 greeter 界面，以允许新用户登录系统。

```
$ dm-tool switch-to-greeter
```

## 测试

首先，[安装](../../01_安装与包管理/包管理器/Pacman.md) [xorg-server-xephyr](https://archlinux.org/packages/?name=xorg-server-xephyr)包.

之后，把 LightDM 作为 X 程序启动:

```
$ lightdm --test-mode --debug
```

## 配置和调整

可以通过修改其配置文件 `/etc/lightdm/lightdm.conf` 来配置LightDM。

某些 greeter 拥有自己的配置文件。例如

- [lightdm-gtk-greeter](https://archlinux.org/packages/?name=lightdm-gtk-greeter)包: `/etc/lightdm/lightdm-gtk-greeter.conf` (或者你也可以使用 [lightdm-gtk-greeter-settings](https://archlinux.org/packages/?name=lightdm-gtk-greeter-settings)包 图形化配置程序)，
- [lightdm-webkit2-greeter](https://archlinux.org/packages/?name=lightdm-webkit2-greeter)包: `/etc/lightdm/lightdm-webkit2-greeter.conf`

### X 会话包装器

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[Xprofile](../../05_桌面与图形/X%20服务器/Xprofile.md)。**
>
> **附注：** Duplicated information（在 [Talk:LightDM](https://wiki.archlinuxcn.org/wiki/Talk:LightDM) 中讨论）

如果是从 [xinit](../../05_桌面与图形/Xorg%20命令/Xinit.md) 迁移过来，可以注意到显示服务不是由 shell 启动的。这是因为，与 shell 启动显示服务并继承 shell 的环境相反，LightDM 启动的图形界面并不读取 shell。LightDM 通过运行包装器脚本启动显示服务，最后执行您的图形环境。默认情况下，会运行 `/etc/lightdm/Xsession`。

#### 环境变量

该脚本会按照先后顺序检查并读取 `/etc/profile`、`~/.profile`、`/etc/xprofile` 和 `~/.xprofile`。如果实际使用的 shell 不会读取这些文件，也可以创建一个 `~/.xprofile` 来达到同样的效果。 （在这个例子中，登录 shell 是 [zsh](../../07_开发与工具/命令行解释器/Zsh.md)）

```
~/.xprofile
```

```
#!/bin/sh
[ -f ~/.config/zsh/.zshenv ] && . ~/.config/zsh/.zshenv
```

如果有对显示服务非常重要的 shell 变量（例如 Gtk 或 QT 主题、GNUPG 位置、要覆盖的配置等），这就可以让图形环境可以访问到设定的环境变量，而不必通过登录 shell 来启动。

#### 键盘映射

该脚本使用文件 `/etc/X11/Xkbmap`、`~/.Xkbmap` 中提供的参数运行 Xkbmap。如果没有找到这些文件，它将运行 [xmodmap](../../04_硬件与外设/输入设备/Xmodmap.md) 和 `/etc/X11/Xmodmap`，`~/.Xmodmap`。 如果使用 xkbmap，则使用 cat 解析文件。 下面是一个可以生效的示例：

```
~/.Xkbmap
```

```
-model pc105 -layout us,us,tr -variant ,dvorak,f -option grp:caps_toggle
```

否则，会话将继承X11的系统默认映射。这个映射可以在 xorg 配置文件中定义，手动编辑或使用 `localectl set-x11-keymap` 命令。参见Xorg/键盘配置#设置键盘布局。

#### 在 lightdm-gtk-greeter 中使用多个键盘布局

要使用户能够在登录屏幕上的预定义键盘布局之间切换，请启用下拉菜单并配置布局。使用 [lightdm-gtk-greeter-settings](https://archlinux.org/packages/?name=lightdm-gtk-greeter-settings)包 gui 进行配置或直接编辑配置文件：

```
/etc/lightdm/lightdm-gtk-greeter.conf
```

```
[greeter]
indicators = ~host;~spacer;~clock;~spacer;~layout;~language;~session;~a11y;~power
```

使用 localectl 来设置多个布局, 例如 de 和它的*变体*且以后者为主：

```
# localectl --no-convert set-x11-keymap de,de pc105 neo,
```

请注意末尾的逗号，这意味着第二个 de 的空白变体。

### 更改背景图片/颜色

你可以设置一个十六进制颜色或者一张图片作为背景。一些 greeter 提供了更强大的背景选项，比如说在登录界面切换背景，随机背景功能。

#### GTK greeter

可以使用 [lightdm-gtk-greeter-settings](https://archlinux.org/packages/?name=lightdm-gtk-greeter-settings)包 图形界面进行设置。

如果需要在 greeter 上使用自定义图片，请修改 `/etc/lightdm/lightdm-gtk-greeter.conf` 中的 `background` 变量值。变量位于 `[greeter]` 会话，例如:

```
/etc/lightdm/lightdm-gtk-greeter.conf
```

```
[greeter]
background=/usr/share/pixmaps/black_and_white_photography-wallpaper-1920x1080.jpg
```

> **注意：**建议将 PNG 或 JPG 文件放到 `/usr/share/pixmaps`，因为 LightDM 用户需要有背景文件的读取权限。

可以通过 `[greeter]` 段落的 `theme-name` 变量配置 GTK3 主题、图标主题以及指针主题:

```
/etc/lightdm/lightdm-gtk-greeter.conf
```

```
[greeter]
theme-name = Tela
icon-theme-name = Tela
cursor-theme-name = Tela
cursor-theme-size = 32
font-name = Cantarell 20
```

##### HiDPI 与 4K 的配置

若在 lightdm-gtk-greeter 上直接使用 HiDPI 或 4K 的显示器，其渲染的文本和对话框将会异常的小。可以通过像这样的DPI配置来解决该问题：

```
/etc/lightdm/lightdm-gtk-greeter.conf
```

```
[greeter]
xft-dpi = 192
```

In this case "192" means twice the 96 DPI setting of the screen, which is equivalent to the *2X Scale* setting in other graphic environments. The value can be obtained with `xdpyinfo | grep dots` in Xorg, for example. 在本例中，"192" 代表两倍的 96 DPI 屏幕设定，等价于其他图形环境的 *2X 缩放* 选项。该值可以通过在 Xorg 中运行 `xdpyinfo | grep dots` 获得。

#### Webkit2 greeter

[lightdm-webkit2-greeter](https://archlinux.org/packages/?name=lightdm-webkit2-greeter)包 包允许您在登录屏幕上直接选择背景图像。如果您使用[Material 主题](https://github.com/artur9010/lightdm-webkit-material)，它还提供了每次启动时显示随机图像的选项。默认情况下，图像来源于 `/usr/share/backgrounds`。您可以通过编辑 `lightdm-webkit2-greeter.conf` 来更改背景图像目录。例如：

```
/etc/lightdm/lightdm-webkit2-greeter.conf
```

```
[branding]
background_images = /usr/share/backgrounds
```

> **注意：**存放背景图像的目录必须是LightDM用户能够访问的，所以不应该把它放在 `/home` 目录下。

#### Slick Greeter

使用 [lightdm-settings](https://aur.archlinux.org/packages/lightdm-settings/)AUR 图形界面设置。

### 更改头像

首先确保已安装 [accountsservice](https://archlinux.org/packages/?name=accountsservice)包 软件包，然后如下设置，把 `*username*` 替换为目标用户的登录名。

- 准备一张 96x96 PNG 图像文件 ，把它放到 `/var/lib/AccountsService/icons/*username*.png` 位置。你也可以用不同格式的图像，比如说 JPEG。
- 或者，如果图像目录配置已经指向 `/home/*username*/.face` ，那么把你的图片放到该位置即可，然后跳过下面的步骤
- 编辑或创建 `/var/lib/AccountsService/users/*username*`, 添加如下内容:

```
[User]
Icon=/var/lib/AccountsService/icons/username.png
```

> **注意：**确保所有被创建的文件具有 644 权限，使用 [文件权限与属性#修改权限](../../07_开发与工具/命令行/文件权限与属性.md#修改权限) 来确认权限配置

### Arch 为中心的 64x64 图标来源

[archlinux-artwork](https://aur.archlinux.org/packages/archlinux-artwork/)AUR 软件包包含了一些不错的例子。它们被安装到 `/usr/share/archlinux/icons`, 可如下复制到 `/usr/share/icons/hicolor/64x64/devices`:

```
# find /usr/share/archlinux/icons -name "*64*" -exec cp {} /usr/share/icons/hicolor/64x64/devices \;
```

复制之后，可删除 [archlinux-artwork](https://aur.archlinux.org/packages/archlinux-artwork/)AUR.

### 启用自动登录

编辑 LightDM 配置文件，取消该行的注释，并添加要自动登录的用户名:

```
/etc/lightdm/lightdm.conf
```

```
[Seat:*]
autologin-user=username
```

要让用户登录时不用输入密码，用户必须是 `autologin` 组的成员:

```
# groupadd autologin
# gpasswd -a username autologin
```

LightDM使用自动登录的用户的 `~/.dmrc` 中指定的会话进行登录。要覆盖这个文件，在 `lightdm.conf` 中指定 `autologin-session` 即可：

```
/etc/lightdm/lightdm.conf
```

```
[Seat:*]
autologin-user=username
autologin-session=session
```

可以通过列出 `/usr/share/xsessions/*.desktop` 获取有效会话名称的列表，其中包含X会话，以及通过列出 `/usr/share/wayland-sessions/*.desktop` 获取Wayland会话的列表。

> **注意：**GNOME 用户, 更一般地 gnome-keyring 用户需要把他们的密码环设置一个空白密码以自动禁用。

### 启用无密码交互登录

LightDM 使用 [PAM](../../02_系统管理与内核/内核/PAM.md) 完成登录操作，因此你必须在此之前配置好与LightDM相关的PAM配置

```
/etc/pam.d/lightdm
```

```
#%PAM-1.0
auth        sufficient  pam_succeed_if.so user ingroup nopasswdlogin
auth        include     system-login
...
```

同时你必须是 `nopasswdlogin` 组的成员以完成无密码登录的过程

```
# groupadd -r nopasswdlogin
# gpasswd -a username nopasswdlogin
# gpasswd -a username nopasswdlogin
# gpasswd -a username autologin
```

> **注意：**GNOME用户，以及gnome-keyring用户，可能需要按照前一节末尾的说明启用自动登录。

要创建一个新的用户帐户，可以自动登录，并且可以再次无需密码登录，可以创建一个同时属于这两个组的辅助成员的用户，例如：

```
# useradd -mG autologin,nopasswdlogin -s /bin/bash username
```

### 启用访客会话

> **注意：**启用此功能后，访客用户将可以在您的系统中无需密码访问。

要在LightDM中启用访客会话（无需改变系统配置），你需要至少两样东西：

1.  **guest-account-script**：默认为 `guest-account` ，并且接受两个命令：
    - **add** （创建一个临时的访客系统账户，并返回创建账户的用户名）
    - **remove** *account name* （删除相应的账户）
2.  一个 [**autologin**](#启用自动登录) 组，需要将创建的访客账户添加到其中（参考 `/etc/pam.d/lightdm-autologin`)

有两个 AUR 软件包可以在 lightdm 中启用访客会话：

- [lightdm-guest](https://aur.archlinux.org/packages/lightdm-guest/)AUR 提供了（大部分未修改）上游的 guest-session 脚本，以及 [lightdm](https://archlinux.org/packages/?name=lightdm)包 本身。
- [lightdm-guest-account](https://aur.archlinux.org/packages/lightdm-guest-account/)AUR 只提供了一个精简版本的脚本。

### 隐藏系统和服务用户

为防止系统用户出现在登录界面，安装可选依赖 [accountsservice](https://archlinux.org/packages/?name=accountsservice)包, 或者把这些用户名添加到 `/etc/lightdm/users.conf` 下的 `hidden-users` 里。前者优势在于添加/删除用户时不用更新列表。

### 从 SLiM 迁移

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[显示管理器](../../05_桌面与图形/图形用户界面/显示管理器.md)。**
>
> **附注：** 这并非 LightDM 特有的问题（甚至也并非 SLiM 特有的问题，因为[XDM](../../05_桌面与图形/显示管理器/XDM.md)也使用[xinitrc](../../05_桌面与图形/Xorg%20命令/Xinit.md) ）。或许应该在[显示管理器](../../05_桌面与图形/图形用户界面/显示管理器.md)页面添加一行说明？（在 [Talk:LightDM](https://wiki.archlinuxcn.org/wiki/Talk:LightDM) 中讨论）

把 [xinitrc](../../05_桌面与图形/Xorg%20命令/Xinit.md) 的内容搬到 [xprofile](../../05_桌面与图形/X%20服务器/Xprofile.md), 删除调用[窗口管理器](../../05_桌面与图形/窗口管理器/窗口管理器.md)或[桌面环境](../../05_桌面与图形/桌面环境/桌面环境.md)的部分。

### 使用 ~/.xinitrc 登录

请参考 [显示管理器#运行_~/.xinitrc_会话](../../05_桌面与图形/图形用户界面/显示管理器.md#运行_~/.xinitrc_会话)。

### 默认打开小键盘

安装 [numlockx](https://archlinux.org/packages/?name=numlockx)包, 编辑 `/etc/lightdm/lightdm.conf` 添加以下几行:

```
/etc/lightdm/lightdm.conf
```

```
[Seat:*]
greeter-setup-script=/usr/bin/numlockx on
```

### 默认会话

Lightdm, 像其他 DM 一样，把上次选择的 xsession 存储在 `~/.dmrc`. 更多信息见 [Display manager#Session configuration](../../05_桌面与图形/图形用户界面/显示管理器.md#Session_configuration).

### 修改登录窗口的位置

#### GTK+ greeter

编辑 `/etc/lightdm/lightdm-gtk-greeter.conf` 文件，设置 `position`，这个设置接受 `x` 和 `y` 变量，可以使用绝对值(pixels)或相对值(percent). 每个变量都可以增加一个额外的锚定位置 `start`, `center` 和 `end`，数值间用 comma 分隔.

例如：

```
position=200,start 50%,center
```

### VNC 服务

Lightdm也可以用于通过VNC连接。请确保在服务器端安装了 [tigervnc](https://archlinux.org/packages/?name=tigervnc)包 包，并可选择在客户端PC上安装作为VNC客户端。

在以root身份登录的服务器上设置身份验证密码：

```
# vncpasswd /etc/vncpasswd
```

请按照以下所示编辑LightDM配置文件。 注意， `listen-address` 参数配置了VNC只允许从本地连接。这是为了只允许通过 [SSH 和端口转发](../../03_网络与服务/远程服务器/TigerVNC.md#On_the_client)进行连接。在SSH客户端上，请确保您使用 `localhost:5900` 作为隧道目的；在双栈网络连接上使用 `127.0.0.1:5900` 或 `::1:5900` 是不可靠的。如果您想允许不安全的连接，可以禁用此设置。

```
/etc/lightdm/lightdm.conf
```

```
[VNCServer]
enabled=true
command=Xvnc -rfbauth /etc/vncpasswd
port=5900
listen-address=localhost
width=1024
height=768
depth=24
```

现在按照 [TigerVNC#On the client](../../03_网络与服务/远程服务器/TigerVNC.md#On_the_client) 中描述的方法打开一个 SSH 隧道并连接到本地主机。

> **注意：**如果在打开 VNC 连接时遇到空白屏幕，请尝试使用不同的 LightDM 登录管理器。

### 使用light-locker锁定屏幕

[light-locker](https://archlinux.org/packages/?name=light-locker)包 是一个使用 LightDM 进行用户验证的简单屏幕锁定工具。一旦安装并运行，您可以通过以下方式锁定您的会话：

```
$ light-locker-command -l
```

这需要在您的会话开始时启动 `light-locker`。默认情况下，通过 [XDG Autostart](../../05_桌面与图形/Freedesktop.org/XDG%20Autostart.md) 启动。请参见 [Autostarting](../../02_系统管理与内核/系统管理/自动启动.md) 获取更多选项。

### 多显示器设置

有时候 LightDM 在多显示器设置下无法正确设置监视器分辨率。以下的 Xorg 配置适用于两个显示器：一个大的主屏幕在左侧，和一个较小的次屏幕在其右侧。这个顺序可以被修改和调整。

```
/etc/X11/xorg.conf.d/52-resolution-fix.conf
```

```
Section "Monitor"
    Identifier "DP1"
    Option "PreferredMode" "3840x2160"
    Option "Primary" "1"
EndSection
Section "Monitor"
    Identifier "eDP1"
    Option "PreferredMode" "1920x1080"
    Option "RightOf" "DP1"
EndSection
```

这使得 `/etc/lightdm/lightdm.conf` 中的 `display-setup-script` 调整变得多余。

## 疑难问题

### 自动登录无效

确保 `/etc/lightdm/lightdm.conf` 文件中的 `autologin-user=` 包含正确的值。末尾的空白字符会导致错误。

如果自动登录时出现空白屏幕或登录屏幕立即返回的情况，您可能需要进行以下设置： `logind-check-graphical=true`。

你还可以安装 [lightdm-autologin-greeter-git](https://aur.archlinux.org/packages/lightdm-autologin-greeter-git/)AUR 来实现这个特殊目的。

### 查看当前配置

要查看有效配置，请运行以下命令：

```
$ lightdm --show-config
```

这将显示当前的设置，以及这些设置所读取的配置文件。

### LightDM启动失败，并且屏幕闪烁

如果你一直屏幕闪烁并且启动后没有 lightdm, 确保你已在 lightdm 的配置文件里正确设置了 greeter. 如果你正确设置了 GTK greeter, 确保 `xsessions-directory` (默认是: `/usr/share/xsessions`) 存在并且至少包含一个 .desktop 文件。

如果你上次选择的会话永久失效了，lightdm 启动时也可能有同样问题 (例如上次使用的是 gnome 并删除了 gnome-session 软件包): 最简单的解决方法就是恢复删掉的软件包。另一个可能的解决是:

```
# dbus-send --system --type=method_call --print-reply --dest=org.freedesktop.Accounts /org/freedesktop/Accounts/User1000 org.freedesktop.Accounts.User.SetXSession string:xfce
```

此例为用户 1000 设置默认会话为 "xfce".

### 显示错误语言环境

如果 Lightdm 未正常显示你的语言环境，把你的语言环境添加到 `/etc/environment` (自己酌情更改)

```
 LANG=pt_PT.utf8
```

如果您希望LightDM及其欢迎界面使用与系统区域设置不同的语言，您可以使用[Systemd#Drop-in files](../../01_安装与包管理/初始化/Systemd.md#Drop-in_files) 中的 `Environment=` 选项。

### 使用 GTK greeter 丢失图标

如果你把 [lightdm-gtk-greeter](https://archlinux.org/packages/?name=lightdm-gtk-greeter)包 作为 greeter 并且它把占位符图像显示为图标，确保已安装和正确配置有效的图标主题和主题。检查如下文件:

```
/etc/lightdm/lightdm-gtk-greeter.conf
```

```
[greeter]
theme-name=mate      # this should be the name of a directory under /usr/share/themes/
icon-theme-name=mate # this should be the name of a fully featured icons set directory under /usr/share/icons/
```

### LightDM 在登录提示符处冻结

你会发现当输入正确的用户名和密码尝试登录时 LightDM 冻结，你无法进入桌面。为修复，重新安装 [gdk-pixbuf2](https://archlinux.org/packages/?name=gdk-pixbuf2)包 软件包。参见[这个](https://bbs.archlinux.org/viewtopic.php?id=179031)论坛帖子。

### LightDM 显示在错误的显示器上

如果你使用的多显示器，LightDM 可能会显示在不该出现的那一个上 (例如: 主显示器在左边). 为强制 LightDM 登录界面显示在特定的显示器上，编辑 `/etc/lightdm/lightdm.conf` 更改 *display-setup-script* 参数如下:

```
/etc/lightdm/lightdm.conf
```

```
display-setup-script=xrandr --output HDMI-1 --primary
```

替换 *HDMI-1* 为你的正确的显示器 ID, 可从 **xrandr** 命令输出获取。

另外，如果您使用的是GTK欢迎界面，您可以编辑 `/etc/lightdm/lightdm-gtk-greeter.conf` 文件，并添加 *active-monitor* 参数，如下所示：

```
/etc/lightdm/lightdm-gtk-greeter.conf
```

```
[greeter]
active-monitor=0
```

请将0替换为所需的显示器编号。

### LightDM不显示或仅显示TTY输出

可能系统启动太快了，LightDM 服务在图形驱动加载前就启动了。如果是这样，将下面配置加入 lightdm.conf 文件:

```
/etc/lightdm/lightdm.conf
```

```
[LightDM]
logind-check-graphical=true
```

设置后 LightDM 会等待图形驱动加载完成后再启动 greeters/autostarting 会话。

**在更新的 LightDM 版本中，这是默认设置。**因此，在某些硬件上，可能无法正确检测到图形驱动程序，并且 LightDM 可能永远不会尝试启动登录管理器——即使在系统启动后稳定下来后也是如此。如果发生这种情况，将此设置为 false 将禁用此检查，并强制 LightDM 无论如何都启动登录管理器。

### 在Intel图形上运行的LightDM帧率较低

参见 [Intel graphics#AccelMethod](../../05_桌面与图形/图形/Intel%20图形处理器.md#AccelMethod)。

### Pulseaudio 不自动启动

参见 [PulseAudio#Running](../../04_硬件与外设/声音/PulseAudio.md#Running)。

### 当主目录被加密时，在LightDM显示之前出现了长时间的暂停

有些LightDM主题尝试访问位于HOME目录下的用户头像。如果你的HOME目录被加密了，LightDM就无法访问它，并会卡住。为了防止这种情况发生，你可以选择以下两种方式之一：

- 将您的头像按照 [#更改头像](#更改头像)中的说明进行设置。
- 仅适用于 [lightdm-gtk-greeter](https://archlinux.org/packages/?name=lightdm-gtk-greeter)包：在 `/etc/lightdm/lightdm-gtk-greeter.conf` 文件中设置 `hide-user-image = true`。

### 启动时停在 "\[ OK \] Reached target Graphical Interface." 的位置

如果您修改了 `/etc/nsswitch.conf` 文件，可能会导致用户和组的查找失败。当 `nsswitch.conf` 文件的group行包含了 `ldap` 配置，但没有在`/etc/nslcd.conf` 文件中设置`nss_initgroups_ignoreusers ALLLOCAL` 时，这种情况会发生。

### Wayland会话无法正常工作，并且登录界面中出现重复的GNOME条目

一些入口程序 (例如 [lightdm-webkit2-greeter](https://archlinux.org/packages/?name=lightdm-webkit2-greeter)包) 不支持具有相同名称的两个会话 。要检查重复条目，可以执行以下操作：

```
$ ls -1 /usr/share/wayland-sessions /usr/share/xsessions
```

在 `/usr/share/xsessions` 中重命名重复的条目。例如：

```
# mv /usr/share/xsessions/gnome.desktop /usr/share/xsessions/gnome.desktop.disabled
```

### 登录始终在第一次尝试时出现段错误

按照 [Network 页面](../../03_网络与服务/网络/网络配置.md#Set_the_hostname)中的描述设置主机名。此外，还可以参考 [FS#47694](https://bugs.archlinux.org/task/47694)。

### 遇到无限登录循环的问题

如果你陷入了一个循环，每次你输入正确的用户名和密码后屏幕都变黑，然后又回到登录界面，运行 `rm ~/.Xauthority` (或陷入困境用户的有问题的 `.Xauthority`) 可能会解决这个问题。

另一个原因是这可能是因为您尝试从头开始重新创建 "lightdm.conf" 文件，而您的版本缺少了这行代码：

```
session-wrapper=/etc/lightdm/Xsession
```

在这种情况下，lightdm 尝试使用 "lightdm-session" 作为会话包装器，而这个在 Arch Linux 上并不存在。

如果你的 lightdm.conf 文件包含你指定的 session-wrapper，但 lightdm 日志显示正在使用默认的会话包装器，请确保您的 lightdm.conf 文件在启动期间可用。例如，您可能创建了一个指向home目录中文件的符号链接，但在 LightDM 服务启动之前您的home目录尚未被挂载。在这种情况下，LightDM 将回退到默认的会话包装器。

### Wayland 会话中输入设备无法工作

当你启动了一个 Wayland 会话，输入设备有时可能无法工作，可以物理断开并重新物理连接解决。参见 [LightDM#63](https://github.com/canonical/lightdm/issues/63)。

另外一种解决方法是通过在 `/etc/lightdm/Xsession` 中添加 `sleep 1` 来延迟 Wayland 合成器的启动。参见 [archlinux/packaging/packages/lightdm!4](https://gitlab.archlinux.org/archlinux/packaging/packages/lightdm/-/merge_requests/4) 以获取针对 Arch 软件包的提议解决方案。

## 另见

- [Ubuntu Wiki article](https://wiki.ubuntu.com/LightDM)
- [Gentoo:LightDM](https://wiki.gentoo.org/wiki/LightDM)
- [LightDM on GitHub](https://github.com/CanonicalLtd/lightdm)
