# 第 10 章　Freedesktop.org

> 换桌面后，文件放哪、由谁打开、登录时谁自启，都不随桌面走。本章讲 freedesktop.org 这一层：用户目录定路径，MIME 应用程序定打开方式，门户替受限程序要权限，另两节管菜单。
> 
> 先读《XDG 用户目录》，路径不定，后面无处落脚；再读《XDG MIME 应用程序》与《XDG 桌面门户》，排查最细。《Xdg-utils》当速查，《Xdg-menu》只理菜单时用，《XDG Autostart》读目录约定即可。最易误解是把它们当某桌面的附属功能：规范跨桌面通用，换桌面才露馅；门户缺后端不报错，静默失效。

<!-- 本章 6 节，共 57 KB -->

## 10.1　XDG Autostart

*难度：入门 ｜ 篇幅：2.2 KB ｜ 所属：Freedesktop.org*

将要讲到：先决条件、目录

[XDG Autostart 规范](https://specifications.freedesktop.org/autostart-spec/autostart-spec-latest.html)定义了一种通过将其放置在特定[#目录](#目录)中来在[桌面环境](../桌面环境/桌面环境.md)启动和可移动介质安装中[自动启动](../../02_系统管理与内核/系统管理/自动启动.md)普通[桌面项](../../01_安装与包管理/配置文件/桌面项.md)的方法。

## 先决条件

您需要使用支持它的[桌面环境](../桌面环境/桌面环境.md)或专用实现，例如：

- [dex](https://archlinux.org/packages/?name=dex)包
- [dapper](https://aur.archlinux.org/packages/dapper/)AUR
- [fbautostart](https://aur.archlinux.org/packages/fbautostart/)AUR
- [sandsmark-xdg-autostart-launcher-git](https://aur.archlinux.org/packages/sandsmark-xdg-autostart-launcher-git/)AUR
- [systemd-xdg-autostart-generator](https://systemd.io/DESKTOP_ENVIRONMENTS/#xdg-autostart-integration)

## 目录

按优先顺序排列的自动启动目录为：

- 用户特定：`$XDG_CONFIG_HOME/autostart`（默认为 `~/.config/autostart`）
- 系统范围：`$XDG_CONFIG_DIRS/autostart`（默认为 `/etc/xdg/autostart`）

系统范围的桌面配置项可以由具有相同文件名的用户特定配置项覆盖。

> **提示：**要禁用系统范围的条目，请创建一个包含 `Hidden=true` 的替代配置项。

有关更多详细信息，请阅读[规范](https://specifications.freedesktop.org/autostart-spec/autostart-spec-latest.html)。

## 10.2　XDG 用户目录

*难度：入门 ｜ 篇幅：4.7 KB ｜ 所属：Freedesktop.org*

将要讲到：安装、创建默认目录、创建自定义目录、查询配置好的目录

译自 [freedesktop.org](https://www.freedesktop.org/wiki/Software/xdg-user-dirs/)：

: *xdg-user-dirs* 是一个帮助管理用户常用目录（如桌面文件夹和音乐文件夹）的工具。它还处理这些文件夹名称的本地化（即翻译）。

: 其工作原理是在登录阶段的早期运行 [xdg-user-dirs-update(1)](https://man.archlinux.org/man/xdg-user-dirs-update.1) 程序。该程序会读取一个配置文件和一组默认目录，然后在用户的主目录中创建这些目录的本地化版本，并在 `$XDG_CONFIG_HOME/user-dirs.dirs`（`XDG_CONFIG_HOME` 默认为 `~/.config`）中设置一个配置文件，供应用程序读取以找到这些目录。

大多数[文件管理器](../../06_软件与应用/文件管理器/文件管理器功能.md)会使用特殊图标标识 XDG 用户目录。

## 安装

安装 [xdg-user-dirs](https://archlinux.org/packages/?name=xdg-user-dirs)包。

> **注意：**某些[桌面环境](../桌面环境/桌面环境.md)可能已默认安装，例如 [GNOME](../GNOME/GNOME.md)。

## 创建默认目录

可以用以下命令在 `$HOME` 下创建一整套默认的经本地化的用户目录：

```
$ xdg-user-dirs-update
```

> **提示：**可以使用 `LC_ALL=C.UTF-8 xdg-user-dirs-update --force` 命令强制创建英文命名的目录。

运行后该命令还会自动地：

- 创建一个本地的 `~/.config/user-dirs.dirs` 配置文件：应用程序通过他来查找使用特定帐号指定的用户目录。
- 创建一个本地的 `~/.config/user-dirs.locale` 配置文件：根据使用的 locale 指定语言。

用户服务 `xdg-user-dirs.service` 也将默认安装并启用，以便在每次登录会话开始时运行此命令，保持您的目录是最新的。

## 创建自定义目录

本地 `~/.config/user-dirs.dirs` 和全局 `/etc/xdg/user-dirs.defaults` 配置文件使用以下环境变量格式指向用户目录：`XDG_DIRNAME_DIR="$HOME/directory_name"`。一个示例配置文件可能如下所示（这些都是默认目录）：

```
~/.config/user-dirs.dirs
```

```
XDG_DESKTOP_DIR="$HOME/桌面"
XDG_DOCUMENTS_DIR="$HOME/文档"
XDG_DOWNLOAD_DIR="$HOME/下载"
XDG_MUSIC_DIR="$HOME/音乐"
XDG_PICTURES_DIR="$HOME/图片"
XDG_PROJECTS_DIR="$HOME/项目"
XDG_PUBLICSHARE_DIR="$HOME/公共"
XDG_TEMPLATES_DIR="$HOME/模板"
XDG_VIDEOS_DIR="$HOME/视频"
```

因为 [xdg-user-dirs](https://archlinux.org/packages/?name=xdg-user-dirs)包 会参照本地配置文件来了解正确的用户目录，所以可以自定义。比如若将 `~/.config/user-dirs.dirs` 下 `XDG_DOWNLOAD_DIR` 变量设为了 `$HOME/Internet`，那么任何参照了该变量的程序都会改用这个目录。

> **注意：**和其他的配置文件一样，本地设定覆盖全局设定。另外自定义的目录要自己创建。

或者也可以用命令行修改默认目录。下列命令会产生和上面一样的效果：

```
$ xdg-user-dirs-update --set DOWNLOAD ~/Internet
```

## 查询配置好的目录

一旦设置完成，任何用户目录都可以通过 [xdg-user-dirs](https://archlinux.org/packages/?name=xdg-user-dirs)包 查看。例如，以下命令将显示 `Templates` 目录的位置，该目录当然对应于本地配置文件中的 `XDG_TEMPLATES_DIR` 变量：

```
$ xdg-user-dir TEMPLATES
```

> **警告：**`xdg-user-dir` 不应从未经检查的输入中获取其参数，因为它会将参数直接传递给 `eval`，而不进行任何有效性检查，代码行如下：
>
> ```
> eval echo \${XDG_${1}_DIR:-$HOME}
> ```
>
> 这意味着 `xdg-user-dir` 会从未经清理的输入中执行任意代码。除非[上游](https://gitlab.freedesktop.org/xdg/xdg-user-dirs/-/issues/42)修复了这个不安全的实现，否则 `xdg-user-dir` 应仅在使用硬编码或经过严格审核的参数时使用。

## 10.3　Xdg-utils

*难度：进阶 ｜ 篇幅：10.5 KB ｜ 所属：Freedesktop.org*

将要讲到：安装、使用、提示与技巧

[xdg-utils](https://www.freedesktop.org/wiki/Software/xdg-utils/) 提供了官方工具，用于管理 [XDG MIME 应用程序](XDG%20MIME%20应用程序.md)。

- [xdg-desktop-menu(1)](https://man.archlinux.org/man/xdg-desktop-menu.1) - 安装桌面菜单项
- [xdg-desktop-icon(1)](https://man.archlinux.org/man/xdg-desktop-icon.1) - 将[桌面项](../../01_安装与包管理/配置文件/桌面项.md)复制到用户的桌面
- [xdg-email(1)](https://man.archlinux.org/man/xdg-email.1) - 在用户首选的邮件客户端中编写新电子邮件，可能会预填入主题和其他信息
- [xdg-icon-resource(1)](https://man.archlinux.org/man/xdg-icon-resource.1) - 安装图标资源
- [xdg-mime(1)](https://man.archlinux.org/man/xdg-mime.1) - 查询和安装 MIME 类型和关联
- [xdg-open(1)](https://man.archlinux.org/man/xdg-open.1) - 在用户首选应用程序中打开文件或 URI
- [xdg-screensaver(1)](https://man.archlinux.org/man/xdg-screensaver.1) - 启用、禁用或暂停屏幕保护程序
- [xdg-settings(1)](https://man.archlinux.org/man/xdg-settings.1) - 获取或设置默认的 Web 浏览器和 URL 处理程序

## 安装

安装 [xdg-utils](https://archlinux.org/packages/?name=xdg-utils)包 包。

## 使用

### 环境变量

> **注意：**正常运行的桌面环境应该会自动设置这些变量。本节仅在理解 *xdg-utils* 的工作原理时需要，即用于故障排除。

*xdg-utils* 尝试通过调用其提供的专用程序来与您的[桌面环境](../桌面环境/桌面环境.md)集成，在适用的情况下。当前环境的评估如下：

- 如果设置了标准化的 [XDG_CURRENT_DESKTOP](../../02_系统管理与内核/系统管理/环境变量.md) 环境变量，并且该值属于已识别的桌面环境，则使用相应的值。
- 如果存在任何经典回退或特定环境变量，例如 KDE 的 `KDE_FULL_SESSION`，则使用相应的值。
- 如果设置了遗留的 [DESKTOP_SESSION](../../02_系统管理与内核/系统管理/环境变量.md#Examples) 环境变量，并且该值属于已识别的桌面环境，则使用相应的值。

在此过程中，如果找到任何匹配项，则 [DE](../../02_系统管理与内核/系统管理/环境变量.md#Examples) 变量会被内部覆盖为检测到的桌面环境的标准化值。因此，`DE` 既是一个遗留的环境变量，也是 *xdg-utils* 的内部状态变量。例如，如果 `XDG_CURRENT_DESKTOP` 是 `KDE`，*xdg-utils* 会将 `DE` 设置为 `kde`。如果没有找到匹配项，则会使用任何已存在的 `DE` 值， 这样 `XDG_CURRENT_DESKTOP=KDE` 就等于 `XDG_CURRENT_DESKTOP` 未设置并且 `DE=kde`。此实现细节值得注意，因为它导致**如果桌面环境已被检测到，预设的 `DE` 会被忽略**。

*xdg-utils* 识别的环境变量值如下：

| 桌面环境 | `XDG_CURRENT_DESKTOP` | `DE` | `DESKTOP_SESSION` |
| --- | --- | --- | --- |
| – | `X-Generic`1 | `generic` | – |
| [Cinnamon](../桌面环境/Cinnamon.md) | `Cinnamon`, `X-Cinnamon` | `cinnamon` | – |
| [Deepin](../桌面环境/深度桌面环境.md) | `Deepin`, `DEEPIN`, `deepin`1 | `deepin` | – |
| [Enlightenment](../桌面环境/Enlightenment.md) | `ENLIGHTENMENT`1 | `enlightenment` | – |
| [GNOME](../GNOME/GNOME.md) | `GNOME`2 | `gnome` | `gnome` |
| [GNOME Flashback](../GNOME/GNOME_Flashback.md) | `GNOME-Flashback`, `GNOME-Flashback:GNOME`2 | `gnome` | `gnome` |
| [KDE Plasma](../KDE/KDE.md) | `KDE` | `kde` | – |
| [LXDE](../LXDE/LXDE.md) | `LXDE` | `lxde` | `LXDE` |
| [LXQt](../桌面环境/LXQt.md) | `LXQt` | `lxqt` | – |
| [MATE](../桌面环境/MATE.md) | `MATE` | `mate` | `mate` |
| [Xfce](../Xfce/Xfce.md) | `XFCE` | `xfce` | `xfce`, `xfce4`, `Xfce Session` |

1.  这不是一个已在 freedesktop.org 上[注册](https://specifications.freedesktop.org/menu-spec/latest/onlyshowin-registry.html)的环境。
2.  GNOME 变体，包括 *GNOME Classic* 模式，都被 *xdg-utils* 视为相同的。

请注意，这仅仅是 *xdg-utils* 提供的脚本能够*检测*的列表。脚本仍会在以下条件下执行通用的、与环境无关的操作：

- 请求了通用例程，使用 `XDG_CURRENT_DESKTOP=X-Generic` 或 `DE=generic`。
- 环境检测失败。所有相关的环境变量都无法识别或未设置，而经典回退也没有揭示任何信息。
- 执行了特定环境的操作，但失败了，例如由于缺少程序。

### xdg-mime

[xdg-mime(1)](https://man.archlinux.org/man/xdg-mime.1) 是一个用于直接查询和修改默认 MIME 应用程序的脚本。它在其他脚本中使用，例如 *xdg-open*，并且还是一个有用的故障排除工具。

确定文件的 MIME 类型：

```
$ xdg-mime query filetype photo.jpeg
image/jpeg
```

确定 MIME 类型的默认应用程序：

```
$ xdg-mime query default image/jpeg
gimp.desktop
```

更改 MIME 类型的默认应用程序：

```
$ xdg-mime default feh.desktop image/jpeg
```

将文件管理器设置为默认文件管理器（例如 -Thunar）：

```
$ xdg-mime default thunar.desktop inode/directory
```

调试 MIME 类型的默认应用程序：

```
$ env XDG_UTILS_DEBUG_LEVEL=10  xdg-mime query default text/html
Checking /home/user/.config/mimeapps.list
Checking /home/user/.local/share/applications/defaults.list and /home/user/.local/share/applications/mimeinfo.cache
Checking /usr/local/share/applications/defaults.list and /usr/local/share/applications/mimeinfo.cache
Checking /usr/share/applications/defaults.list and /usr/share/applications/mimeinfo.cache
qutebrowser.desktop
```

当需要确定文件的 MIME 类型时，*xdg-mime* 会尝试使用适合桌面环境的程序：

| 桌面环境 | 程序 | 包 |
| --- | --- | --- |
| [Cinnamon](../桌面环境/Cinnamon.md) | `gio` | [glib2](https://archlinux.org/packages/?name=glib2)包 |
| [GNOME](../GNOME/GNOME.md) |
| [GNOME Flashback](../GNOME/GNOME_Flashback.md) |
| [LXDE](../LXDE/LXDE.md) |
| [MATE](../桌面环境/MATE.md) |
| [Xfce](../Xfce/Xfce.md) |
| [Deepin](../桌面环境/深度桌面环境.md) | – | – |
| [Enlightenment](../桌面环境/Enlightenment.md) |
| [LXQt](../桌面环境/LXQt.md) |
| [KDE Plasma](../KDE/KDE.md) | `kmimetypefinder` | [kde-cli-tools](https://archlinux.org/packages/?name=kde-cli-tools)包 |

在通用情况下，*xdg-mime* 将：

- 如果存在，委托给 [mimetype](../../02_系统管理与内核/系统管理/默认应用程序.md#perl-file-mimeinfo)。需要安装 [perl-file-mimeinfo](https://archlinux.org/packages/?name=perl-file-mimeinfo)包 包。
- 如果存在，委托给 [file](https://archlinux.org/packages/?name=file)包。

### xdg-open

[xdg-open(1)](https://man.archlinux.org/man/xdg-open.1) 是一个[资源打开器](../../02_系统管理与内核/系统管理/默认应用程序.md)，被许多应用程序使用，遵循 XDG MIME 应用程序标准，并尽可能与系统的桌面环境集成。

如果检测到桌面环境，将调用其提供的处理程序 :

| 桌面环境 | 程序 | 包 |
| --- | --- | --- |
| [Cinnamon](../桌面环境/Cinnamon.md) | `gio` | [glib2](https://archlinux.org/packages/?name=glib2)包 |
| [GNOME](../GNOME/GNOME.md) |
| [GNOME Flashback](../GNOME/GNOME_Flashback.md) |
| [MATE](../桌面环境/MATE.md) |
| [Deepin](../桌面环境/深度桌面环境.md) | `dde-open` | [deepin-api](https://archlinux.org/packages/?name=deepin-api)包 |
| [Enlightenment](../桌面环境/Enlightenment.md) | `enlightenment_open` | [enlightenment](https://archlinux.org/packages/?name=enlightenment)包 |
| [KDE Plasma](../KDE/KDE.md) | `kde-open`1 | [kde-cli-tools](https://archlinux.org/packages/?name=kde-cli-tools)包 |
| [LXDE](../LXDE/LXDE.md) | `pcmanfm` | [pcmanfm](https://archlinux.org/packages/?name=pcmanfm)包 |
| [LXQt](../桌面环境/LXQt.md) | – | – |
| [Xfce](../Xfce/Xfce.md) | `exo-open`2 | [exo](https://archlinux.org/packages/?name=exo)包 |

1.  如果 `KDE_SESSION_VERSION` 未设置，则会使用 `kfmclient` 来自 [konqueror](https://archlinux.org/packages/?name=konqueror)包。不过，KDE Plasma 应该始终设置此变量。
2.  还会尝试使用来自 [glib2](https://archlinux.org/packages/?name=glib2)包 的 `gio`。

在通用情况下，*xdg-open* 将：

- 查询 [#xdg-mime](#xdg-mime) 以获取与资源关联的默认桌面条目，解析桌面条目，并执行其命令。
- 如果存在，委托给 [run-mailcap](../../02_系统管理与内核/系统管理/默认应用程序.md#run-mailcap)。需要安装 [run-mailcap](https://aur.archlinux.org/packages/run-mailcap/)AUR 包。
- 如果存在，委托给 [mimeopen](../../02_系统管理与内核/系统管理/默认应用程序.md#perl-file-mimeinfo)。需要安装 [perl-file-mimeinfo](https://archlinux.org/packages/?name=perl-file-mimeinfo)包 包。

> **提示：**要查看 *xdg-open* 将使用哪个后端，请设置 `XDG_UTILS_DEBUG_LEVEL=3`。

由于 *xdg-mime* 依赖于 [perl-file-mimeinfo](https://archlinux.org/packages/?name=perl-file-mimeinfo)包 包来实现 [XDG MIME 应用程序](XDG%20MIME%20应用程序.md)标准，如果您没有使用桌面环境，应该安装 [perl-file-mimeinfo](https://archlinux.org/packages/?name=perl-file-mimeinfo)包，或考虑使用其他[资源打开器](../../02_系统管理与内核/系统管理/默认应用程序.md)。

### xdg-settings

请参见 [xdg-settings(1)](https://man.archlinux.org/man/xdg-settings.1)。

设置所有 Web MIME 类型使用单个应用程序的快捷方式：

```
$ xdg-settings set default-web-browser firefox.desktop
```

设置 URL 方案的默认应用程序的快捷方式：

```
$ xdg-settings set default-url-scheme-handler irc xchat.desktop
```

## 提示与技巧

### URL 方案处理程序

要设置 URL 方案的默认应用程序，您可能还需要更改 `x-scheme-handler/*` MIME 类型的默认应用程序：

```
$ xdg-mime default firefox.desktop x-scheme-handler/https x-scheme-handler/http
```

## 10.4　Xdg-menu

*难度：进阶 ｜ 篇幅：11.7 KB ｜ 所属：Freedesktop.org*

将要讲到：安装、菜单层级结构、配置、使用、示例、使用 xdg_menu

xdg-menu是一个用于为以下窗口管理器生成[XDG桌面菜单](https://specifications.freedesktop.org/menu-spec/menu-spec-latest.html)的工具：

- [awesome](../动态窗口管理器/Awesome.md)
- [Blackbox](../堆叠式窗口管理器/Blackbox.md)
- [Fluxbox](../堆叠式窗口管理器/Fluxbox.md)
- [FVWM](https://wiki.archlinux.org/title/FVWM)
- [IceWM](../堆叠式窗口管理器/IceWM.md)
- [Openbox](../堆叠式窗口管理器/Openbox.md)
- [twm](../堆叠式窗口管理器/Twm.md)
- [Window Maker](https://wiki.archlinux.org/title/Window_Maker)

KDE, Gnome, Xfce，Enlightenment已经兼容XDG。

## 安装

安装[archlinux-xdg-menu](https://archlinux.org/packages/?name=archlinux-xdg-menu)包包。

## 菜单层级结构

- 应用
    - 辅助功能
    - 附件
    - 开发
    - 教育
    - 游戏
    - 图形
    - 互联网
    - 多媒体
    - 办公
    - 其他
    - 科学
    - 设置
    - 系统

## 配置

Xdg_menu依赖三类信息来生成菜单：一个根菜单（即通常通过命令行传入的 XML 菜单模板）、上次运行时缓存的信息，以及一系列配置文件。

- 你可以在`/etc/xdg/menus`中找到一些XML菜单模板.
- 如果更改 xdg_menu 的代码以更改菜单布局，请确保删除`~/.xdg_menu_cache`中的所有内容，否则你可能将花费数小时试图弄明白为什么对Perl脚本所做的修改没有生效。
- 各个应用程序的配置文件位于`/usr/share/applications`。

其他配置文件目录可以在`/usr/share`下找到。在大多数情况下，你无需改动这些文件。不过，如果你希望调整菜单的布局，可以通过修改菜单模板以进行小幅调整。若需大幅改动，则需要直接修改Xdg_menu的Perl脚本本身。

如果你发现某些应用程序未显示在菜单中，或者其名称显示异常，那么你需要检查 `/usr/share/applications` 目录下的相应 `.desktop` 文件，并参考[桌面入口规范](https://specifications.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html)。

### 从其他目录添加桌面入口

默认情况下，Xdg-menu会加载那些将桌面入口（desktop entry）安装到`/usr/share/applications`的应用程序。若要将桌面入口安装在用户目录（例如`~/.local/share/applications`）中的应用程序也加入菜单的菜单中，需编辑`/etc/xdg/menus/arch applications.menu`文件，并为相关目录添加`<AppDir>`标签，如下所示：

```
/etc/xdg/menus/arch-applications.menu
```

```
<Menu>

  <Name>Applications</Name>
  <Directory>Arch-Applications.directory</Directory>
  <DefaultAppDirs/>
  <AppDir>/home/username/.local/share/applications</AppDir>
  <DefaultDirectoryDirs/>
  <DefaultMergeDirs/>
  ...
```

## 使用

### xdg_menu

```
xdg_menu [--format <format>] [--desktop <desktop>]
         [--charset <charset>] [--language <language>]
	 [--root-menu <root-menu>] [--die-on-error]
	 [--fullmenu] [--help]

	format - output format
	         possible formats: twm, WindowMaker, fvwm2, icewm, ion3
	                           blackbox, fluxbox, openbox,
				   xfce4, openbox3, openbox3-pipe, awesome
				   readable
		 default: WindowMaker

 	fullmenu  - output a full menu and not only a submenu

	desktop - desktop name for NotShowIn and OnlyShowIn
		 default: the same as format

	charset - output charset
		 default: <locale>

	language - output language
		 default: <locale>

	root-menu - location of root menu file
		 default: /opt/gnome/etc/xdg/menus/applications.menu

	die-on-error - abort execution on any error,
		 default: try to continue

	verbose - print debugging information

	help - print this text
```

### update-menus

**update-menus** 可根据 XDG 数据更新窗口管理器（WM）的菜单，并可配置为自动执行此操作。

该脚本是xdg_menu的一个封装，依赖于 `/etc/update-menus.conf`配置文件

要使用它，你需要安装 [archlinux-xdg-menu](https://archlinux.org/packages/?name=archlinux-xdg-menu)包 (其中包含xdg_menu)

在`/etc/update-menus.conf` 文件中，你需要从支持的窗口管理器列表中选择需要为其生成菜单的WM。配置文件中允许使用#添加注释。

所有生成的菜单都会存放在`/var/cache/xdg-menu/`目录下。有关具体窗口管理器的配置示例，请参阅本页的“[WM特定示例](https://wiki.archlinux.org/title/Xdg-menu#Examples)”部分。

## 示例

### Awesome窗口管理器

#### 使用 xdg_menu ：

```
$ xdg_menu --format awesome > ~/.config/awesome/xdg_menu.lua
```

编辑 rc.lua ，按如下方式更改

- 添加一行require语句，以加载你生成的xdg_menu.lua文件
- 在你的awful.menu对象中添加一个菜单项，调用xgd_menu生成的应用程序菜单

```
...
xdg_menu = require("xdg_menu")
...

...
mymainmenu = awful.menu({ items = { { "awesome", myawesomemenu, beautiful.awesome_icon },
                                    { "Applications", xdg_menu },
                                    { "open terminal", terminal }
                                  }
                        })
...
```

### IceWM

## 使用 xdg_menu

```
$ xdg_menu --format icewm --fullmenu --root-menu /etc/xdg/menus/arch-applications.menu >>~/.icewm/programs
```

#### 使用 update-menus

- 在 /etc/update-menus.conf 取消对icewm的注释
- 使用root用户运行 update-menus 命令
- 将 ~/.icewm/programs 符号链接到 /var/cache/xdg-menu/icewm/programs

### Ion3

#### 使用 xdg_menu

```
$ xdg_menu --format ion3  --root-menu /etc/xdg/menus/arch-applications.menu >~/.ion3/default-session--0/_xdg-menu.lua
```

之后，将 cfg_menus.lua 更改为包含 _xdg-menu.lua 文件，并将菜单添加到主菜单中。例如：

```
...

dopath("_xdg-menu")

-- Main menu
defmenu("mainmenu", {
    submenu("XDG Menu",         "<NAME-OF-FIRST-MENU-IN-_xdg-menu.lua-FILE>"),
    submenu("Programs",         "appmenu"),
    menuentry("Lock screen",    "ioncore.exec_on(_, 'xlock')"),
    menuentry("Help",           "mod_query.query_man(_)"),
    menuentry("About Ion",      "mod_query.show_about_ion(_)"),
    submenu("Styles",           "stylemenu"),
    submenu("Session",          "sessionmenu"),
})

...
```

#### 使用update-menus

- 在 /etc/update-menus.conf 取消对ion3的注释
- 使用root用户运行 update-menus 命令
- 将 cfg_menus.lua 更改为包含 xdg-menu.lua 文件，并将菜单添加到主菜单中

例如:

```
...

dopath("/var/cache/xdg-menu/ion3/xdg-menu.lua")

-- Main menu
defmenu("mainmenu", {
    submenu("XDG Menu",         "<NAME-OF-FIRST-MENU-IN-xdg-menu.lua-FILE>"),
    submenu("Programs",         "appmenu"),
    menuentry("Lock screen",    "ioncore.exec_on(_, 'xlock')"),
    menuentry("Help",           "mod_query.query_man(_)"),
    menuentry("About Ion",      "mod_query.show_about_ion(_)"),
    submenu("Styles",           "stylemenu"),
    submenu("Session",          "sessionmenu"),
})

...
```

### FluxBox

#### 使用 xdg_menu

```
$ xdg_menu --format fluxbox  --root-menu /etc/xdg/menus/arch-applications.menu >~/.fluxbox/my-menu
```

将菜单文件更改为包含生成的菜单。

例如添加行:

```
[include] (my-menu)
```

#### 使用 update-menus

- 在 /etc/update-menu.conf 中取消对fluxbox的注释
- 使用root用户运行 update-menus
- 将菜单文件更改为包含生成的菜单

例如添加行:

```
[include] (/var/cache/xdg-menu/fluxbox/boxrc)
```

### OpenBox

#### 使用 xdg_menu

生成菜单:

```
$ xdg_menu --format openbox3 --root-menu /etc/xdg/menus/arch-applications.menu > xdg-menu.xml
```

然后手动将其添加到 menu.xml 中。例如，将 xdg-menu.xml 放入 menu.xml 并添加：

```
<menu id="Applications" />
```

进入根菜单。

#### 作为管道菜单

使用 xdg_open 作为管道菜单，您可以获得额外的好处，即在安装新应用程序时，菜单会自动更新。

在 menu.xml 中的根菜单标记之间添加以下内容:

```
<menu id="applications" label="Applications" execute="xdg_menu --format openbox3-pipe --root-menu /etc/xdg/menus/arch-applications.menu" />
```

一个非常基本的例子:

```
<?xml version="1.0" encoding="UTF-8"?>

<openbox_menu xmlns="http://openbox.org/3.4/menu">

<menu id="root-menu" label="Openbox 3">
  <menu id="applications" label="Applications" execute="xdg_menu --format openbox3-pipe --root-menu /etc/xdg/menus/arch-applications.menu" />
  <separator />
  <item label="Log Out">
    <action name="Exit">
      <prompt>yes</prompt>
    </action>
  </item>
</menu>

</openbox_menu>
```

#### 使用 update-menus

- 在 /etc/update-menu.conf 中取消对openbox的注释
- 以root用户运行 update-menus
- 将 menu.xml 文件更改为包含生成的菜单

例如，在根菜单中添加以下内容:

```
<menu id="xdg-menu" label="XDG Menu" execute="cat /var/cache/xdg-menu/openbox/menu.xml"/>
```

### Twm

#### 使用 xdg_menu

使用

```
$ xdg_menu --format twm --root-menu /etc/xdg/menus/arch-applications.menu >my-twm-menu
```

并手动添加到 twmrc 中。对于具有m4预处理的twm衍生物，如vtwm或ctwm，可通过添加

```
sinclude(`/PATH/TO/my-twm-menu')
```

到 \*twmrc.

#### 使用 update-menus

- 在 /etc/update-menu.conf 中取消对twm的注释
- 添加到 /etc/X11/twm/system.twmrc 文件应用程序菜单中 (添加以下这行:

```
 "apps"          f.menu "Applications"
```

)到菜单中

- 使用root用户运行 update-menus
- 运行 twm -f /var/cache/xdg-menu/twm/twmrc

(您还需要将其他自定义项添加到 /etc/X11/twm/system.twmrc)

### WindowMaker

#### 使用 xdg_menu

使用

```
$ xdg_menu --format WindowMaker --root-menu /etc/xdg/menus/arch-applications.menu >my-wm-menu
```

然后添加

```
#include "my-wm-menu"
```

到你的 WindowMaker 菜单文件。

您还可以使用WPrefs“应用程序菜单定义”，并将xdg命令作为参数添加到“生成的子菜单”对象中。

#### 使用 update-menus

- 在 /etc/update-menus.conf 取消对 WindowMaker 的注释
- 用root用户运行 update-menus
- 添加

```
#include "/var/cache/xdg-menu/WindowMaker/wmrc"
```

到你的菜单文件

### Fvwm2

#### 使用 xdg_menu

生成菜单

```
$ xdg_menu --format fvwm2 --root-menu /etc/xdg/menus/arch-applications.menu >fvwm2-menu
```

然后把菜单添加到根菜单

```
read fvwm2-menu

AddToMenu MenuFvwmRoot  "Root Menu"             Title
+ "&0. XDG Menu"          Popup xdg_menu
```

#### 使用 update-menus

- 在 /etc/update-menus.conf 取消对 fvwm2 的注释
- 使用root用户运行 update-menus
- 修改你的 .fvwm2rc 以使其包含创建的菜单，示例:

```
AddToMenu MenuFvwmRoot  "Root Menu"             Title
+ "&0. XDG Menu"          Popup xdg_menu
```

```
read /var/cache/xdg-menu/fvwm2/fvwm2rc
```

### BlackBox

#### 使用 xdg_menu

```
$ xdg_menu --format blackbox  --root-menu /etc/xdg/menus/arch-applications.menu >my-menu
```

修改你的菜单文件以使其包含创建的菜单

例如添加:

```
[include] (my-menu)
```

#### 使用 update-menus

- 在 /etc/update-menus.conf 取消 blackbox 的注释
- 使用root用户运行 update-menus
- 更改你的菜单文件以使其包含创建的菜单

例如添加:

```
[include] (/var/cache/xdg-menu/blackbox/boxrc)
```

## 另请参见

- Sawfish#Menus
- [https://github.com/gapan/xdgmenumaker](https://github.com/gapan/xdgmenumaker)

## 10.5　XDG 桌面门户

*难度：进阶 ｜ 篇幅：13.8 KB ｜ 所属：Freedesktop.org*

将要讲到：安装、后端、配置、疑难解答、参见

引自 [Flatpak 文档](https://docs.flatpak.org/zh-cn/latest/desktop-integration.html#portals)：

: 门户是用于安全访问沙箱外资源的框架。沙箱内程序可以使用的特性包括：检测网络状态；使用文件选择器来打开文件；打开 URI；阻止设备睡眠、休眠或关机；打印；发送邮件；显示通知；截取屏幕或录屏［……］

门户（portals）系统最初专为 [Flatpak](../../07_开发与工具/开发/Flatpak.md) 应用程序设计，但任何应用程序均可借助门户访问跨桌面环境和 UI 框架的统一功能。这一功能得到了广泛使用，例如通过 [PipeWire](../../06_软件与应用/多媒体/PipeWire.md) 在 [Wayland](../Wayland/Wayland.md) 上实现[屏幕共享](../../06_软件与应用/多媒体/PipeWire.md#WebRTC_屏幕共享)，或让 [Firefox](../../06_软件与应用/Mozilla/Firefox.md) 采用与当前桌面环境相同 UI 框架的[文件打开/保存对话框](../../06_软件与应用/Mozilla/Firefox.md#KDE_集成)。

## 安装

安装 [xdg-desktop-portal](https://archlinux.org/packages/?name=xdg-desktop-portal)包 以及至少一个 [#后端](#后端)，该软件包包含一个 [systemd/用户](../../02_系统管理与内核/系统管理/Systemd_用户.md)服务，通过 [D-Bus](../../02_系统管理与内核/系统管理/D-Bus.md) 自动触发启动。

## 后端

当一个软件通过 XDG 桌面门户发起请求时，[xdg-desktop-portal](https://archlinux.org/packages/?name=xdg-desktop-portal)包 会将这个请求转发至可用的后端实现，由后端实现适合桌面环境的 UI，并访问特定于桌面环境的 API 处理诸如打开 URI、录制屏幕之类的请求。后端可以安装多个，例如使用 [Sway](../Wayland%20合成器/Sway.md) 可以安装 [xdg-desktop-portal-wlr](https://archlinux.org/packages/?name=xdg-desktop-portal-wlr)包 支持屏幕共享，并同时安装 [xdg-desktop-portal-gtk](https://archlinux.org/packages/?name=xdg-desktop-portal-gtk)包 作为后备，处理 [xdg-desktop-portal-wlr](https://archlinux.org/packages/?name=xdg-desktop-portal-wlr)包 未实现的接口。

门户后端配置储存在 `/usr/share/xdg-desktop-portal/portals/*.portal` 中。门户后端配置文件包含其支持的接口和桌面环境。

### 所有可用后端及其接口支持情况

| 后端 | 支持的桌面环境 | 使用的 UI 框架 | 接口支持情况 |
| --- | --- | --- | --- |
| 对话框 | 账户信息 | 应用程序选择器 | 后台活动 | 剪贴板 | 桌面启动项 | 电子邮件 | 文件选择器 | 全局快捷键 | 用户会话管理 | 输入设备捕获 | 系统通知 | 打印 | 远程桌面 | 屏幕录制 | 截屏 | 密钥 | 系统设置 | 壁纸 |
| [xdg-desktop-portal-cosmic](https://archlinux.org/packages/?name=xdg-desktop-portal-cosmic)包 | [COSMIC](../桌面环境/COSMIC.md) | iced | 是 | 否 | 否 | 否 | 否 | 否 | 否 | 是 | 否 | 否 | 否 | 否 | 否 | 否 | 是 | 是 | 否 | 是 | 否 |
| [xdg-desktop-portal-dde](https://archlinux.org/packages/?name=xdg-desktop-portal-dde)包 | [深度桌面环境](../桌面环境/深度桌面环境.md) | [Qt](../../07_开发与工具/控件工具集/Qt.md) 5 | 是 | 是 | 是 | 是 | 否 | 否 | 否 | 是 | 是 | 是 | 否 | 是 | 否 | 否 | 否 | 是 | 是 | 是 | 是 |
| [xdg-desktop-portal-gnome](https://archlinux.org/packages/?name=xdg-desktop-portal-gnome)包 | [GNOME](../GNOME/GNOME.md) | [GTK](../../07_开发与工具/控件工具集/GTK.md) 4 | 是 | 是 | 是 | 是 | 是 | 是 | 否 | 是 | 否 | 否 | 是 | 是 | 是 | 是 | 是 | 是 | 否 | 是 | 是 |
| [xdg-desktop-portal-gtk](https://archlinux.org/packages/?name=xdg-desktop-portal-gtk)包 | （通用） | [GTK](../../07_开发与工具/控件工具集/GTK.md) 3 | 是 | 是 | 是 | 否 | 否 | 是 | 是 | 是 | 否 | 是 | 否 | 是 | 是 | 否 | 否 | 否 | 否 | 是 | 否 |
| [xdg-desktop-portal-hyprland](https://archlinux.org/packages/?name=xdg-desktop-portal-hyprland)包 | [Hyprland](../Wayland%20合成器/Hyprland.md)1 | [Qt](../../07_开发与工具/控件工具集/Qt.md) 6 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 是 | 否 | 是 | 否 | 否 | 否 | 是 | 是 | 否 | 否 | 否 |
| [xdg-desktop-portal-kde](https://archlinux.org/packages/?name=xdg-desktop-portal-kde)包 | [KDE Plasma](../KDE/KDE.md) | [Qt](../../07_开发与工具/控件工具集/Qt.md) 6 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 是 | 否 | 是 | 否 |
| [xdg-desktop-portal-liri-git](https://aur.archlinux.org/packages/xdg-desktop-portal-liri-git/)AUR | [Liri](../桌面环境/Liri.md) | [Qt](../../07_开发与工具/控件工具集/Qt.md) 5 | 是 | 是 | 是 | 是 | 否 | 否 | 是 | 是 | 否 | 是 | 否 | 是 | 是 | 否 | 是 | 是 | 否 | 是 | 是 |
| [xdg-desktop-portal-lxqt](https://archlinux.org/packages/?name=xdg-desktop-portal-lxqt)包 | [LXQt](../桌面环境/LXQt.md) | [Qt](../../07_开发与工具/控件工具集/Qt.md) 6 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 是 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 |
| [xdg-desktop-portal-td](https://aur.archlinux.org/packages/xdg-desktop-portal-td/)AUR | theDesk | [Qt](../../07_开发与工具/控件工具集/Qt.md) 6 | 是 | 是 | 否 | 否 | 否 | 否 | 否 | 是 | 否 | 否 | 否 | 是 | 否 | 否 | 否 | 是 | 否 | 是 | 否 |
| [xdg-desktop-portal-wlr](https://archlinux.org/packages/?name=xdg-desktop-portal-wlr)包 | wlroots | – | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 否 | 是 | 是 | 否 | 否 | 否 |
| [xdg-desktop-portal-xapp](https://archlinux.org/packages/?name=xdg-desktop-portal-xapp)包 | [Cinnamon](../桌面环境/Cinnamon.md)2 | – | 否 | 否 | 否 | 是 | 否 | 否 | 否 | 否 | 否 | 是 | 否 | 否 | 否 | 否 | 否 | 是 | 否 | 是 | 是 |

1.  适用于所有基于 wlroots 的混成器，但提供针对 [Hyprland](../Wayland%20合成器/Hyprland.md) 的额外功能，例如可在截屏或屏幕录制时共享单个窗口。
2.  其也为 [MATE](../桌面环境/MATE.md) 和 [Xfce](../Xfce/Xfce.md) 提供部分支持。

除此以外，以下后端跨桌面环境实现了特定接口：

- [darkman](https://archlinux.org/packages/?name=darkman)包 实现了系统设置接口（仅颜色方案设置）。
- [gnome-keyring](https://archlinux.org/packages/?name=gnome-keyring)包 实现了密钥接口。
- [kwallet](https://archlinux.org/packages/?name=kwallet)包 实现了密钥接口。
- [pikeru](https://aur.archlinux.org/packages/pikeru/)AUR 使用自己的 GUI 实现了文件选择器接口。
- [xdg-desktop-portal-shana](https://aur.archlinux.org/packages/xdg-desktop-portal-shana/)AUR 通过将请求重定向至 GNOME/GTK/KDE/LXQt 后端实现了文件选择器接口。
- [xdg-desktop-portal-termfilechooser-git](https://aur.archlinux.org/packages/xdg-desktop-portal-termfilechooser-git/)AUR 使用[终端文件选择器](../../06_软件与应用/应用程序/应用程序列表_工具.md#控制台)实现了文件选择器接口。

## 配置

当收到请求时，`xdg-desktop-portal` 将会使用位于 `/usr/share/xdg-desktop-portal/*桌面环境*-portals.conf` 的配置文件，其中*桌面环境*由[环境变量](../../02_系统管理与内核/系统管理/环境变量.md) `XDG_CURRENT_DESKTOP` 决定。配置文件由桌面环境提供，决定应当使用的后端。

如果需要自定义配置，或者使用的桌面环境并没有提供默认配置，可以创建门户配置文件 `$XDG_CONFIG_HOME/xdg-desktop-portal/portals.conf` 以决定全局或者特定接口使用的后端。如果使用多个桌面环境，也可以创建并配置特定桌面环境的配置文件 `$XDG_CONFIG_HOME/xdg-desktop-portal/*桌面环境*-portals.conf`。

例如，如果正在使用的桌面环境没有门户后端，希望使用[xdg-desktop-portal-gtk](https://archlinux.org/packages/?name=xdg-desktop-portal-gtk)包 作为通用后备后端，同时通过 [xdg-desktop-portal-lxqt](https://archlinux.org/packages/?name=xdg-desktop-portal-lxqt)包 使用 LXQt 文件选择器，可以使用以下配置：

```
~/.config/xdg-desktop-portal/portals.conf
```

```
[preferred]
default=gtk
org.freedesktop.impl.portal.FileChooser=lxqt
```

参见 [portals.conf(5)](https://man.archlinux.org/man/portals.conf.5)。

### 桌面环境伪装

某些情况下（例如独立使用[窗口管理器](../窗口管理器/窗口管理器.md)时），可能需要让 `xdg-desktop-portal` 以为正在使用特定的桌面环境。可以在 `xdg-desktop-portal.service` [用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)中使用[附加配置片段](../../01_安装与包管理/初始化/Systemd.md)添加环境变量 [XDG_CURRENT_DESKTOP](../../02_系统管理与内核/系统管理/环境变量.md)。例如，可以指定使用 KDE 配置的后端：

```
~/.config/systemd/user/xdg-desktop-portal.service.d/override.conf
```

```
[Service]
Environment="XDG_CURRENT_DESKTOP=KDE"
```

## 疑难解答

### 门户无法启动

要使 [xdg-desktop-portal-wlr](https://archlinux.org/packages/?name=xdg-desktop-portal-wlr)包 和 [xdg-desktop-portal-hyprland](https://archlinux.org/packages/?name=xdg-desktop-portal-hyprland)包 正常工作，必须在 [systemd 用户会话](../../02_系统管理与内核/系统管理/Systemd_用户.md#环境变量)中设置 `XDG_CURRENT_DESKTOP` 和 `WAYLAND_DISPLAY` 环境变量。

`XDG_CURRENT_DESKTOP` 需要设置为混成器名称，例如 `XDG_CURRENT_DESKTOP=sway`。`WAYLAND_DISPLAY` 则由混成器自动设置。这两个变量通常会在混成器启动时自动设置，但如果 systemd 用户会话先于混成器启动，混成器启动时需要导入这两个变量。

> **注意：**[Hyprland](../Wayland%20合成器/Hyprland.md) 不会自动设置 `XDG_CURRENT_DESKTOP` 环境变量，需要手动配置。请参阅 [Hyprland Wiki](https://wiki.hyprland.org/Configuring/Environment-variables/#xdg-specifications)。

使用 `systemctl --user show-environment` 检查这些变量是否已设置。如果未设置，请在启动混成器时运行以下命令（例如，将其写入混成器的配置文件），将这些环境变量导入到 systemd 用户会话和 [D-Bus](../../02_系统管理与内核/系统管理/D-Bus.md) 中：

```
$ systemctl --user import-environment WAYLAND_DISPLAY XDG_CURRENT_DESKTOP
$ dbus-update-activation-environment --systemd WAYLAND_DISPLAY XDG_CURRENT_DESKTOP=混成器名称
```

> **注意：**这些环境变量需要在混成器启动时设置，通常通过混成器自身的配置完成，请参见混成器的文档说明。

> **提示：**[Sway](../Wayland%20合成器/Sway.md) 提供了一个可自动完成此操作的插入文件，请参阅 [Sway#配置](../Wayland%20合成器/Sway.md#配置)。

更多详情请参阅  和 。

### 在多显示器环境下使用 xdg-desktop-portal-wlr

`xdg-desktop-portal-wlr` 需要一个外部选择器来选择要共享的显示器。默认按顺序查找 [slurp](https://archlinux.org/packages/?name=slurp)包、[wofi](https://archlinux.org/packages/?name=wofi)包 和 [bemenu](https://archlinux.org/packages/?name=bemenu)包。若使用 slurp，收到屏幕共享请求后，鼠标指针会变成十字形，需要点击要共享的屏幕。若使用 wofi 或 bemenu，会出现一个可用显示器的菜单供选择。如果没有任何选择器可用，`xdg-desktop-portal-wlr` 会回退到找到的第一个显示器。详见 [xdg-desktop-portal-wlr(5) § SCREENCAST OPTIONS](https://man.archlinux.org/man/xdg-desktop-portal-wlr.5#SCREENCAST_OPTIONS)。

### KDE Plasma 下 GTK 应用程序字体渲染不佳

部分 GTK 应用程序在 Plasma 环境中需要安装 [xdg-desktop-portal-gtk](https://archlinux.org/packages/?name=xdg-desktop-portal-gtk)包 才能正确渲染字体。请安装该软件包，然后运行：

```
$ /usr/lib/xdg-desktop-portal --replace
```

### GTK（可能还有其它的）文件选择器无法工作

如果应用程序通过 [Xwayland](../Wayland/Wayland.md) 运行（最简单的检查方法是运行 `xeyes`，然后将鼠标指针移至应用程序窗口内，若程序运行在 [Xwayland](../Wayland/Wayland.md) 下，其中眼睛会看向鼠标指针），那么 `xdg-desktop-portal-gtk` 会按需启动，但在选择文件后可能没有任何反应。这种情况下，为 `xdg-desktop-portal-gtk` 的环境添加 `DISPLAY=:0` 可能会解决问题。要实现此操作，可以按照前文（[#门户无法启动](#门户无法启动)）关于 `import-environment` 的说明进行设置，或者直接编辑 `xdg-desktop-portal-gtk` 的 systemd 用户单元文件。此外，也可以尝试通过配置开启应用程序的原生 [Wayland](../Wayland/Wayland.md) 支持（例如[对于 Electron 程序](../Wayland/Wayland.md#Electron)）。

## 参见

- [项目主页](https://flatpak.github.io/xdg-desktop-portal/)
- [门户文档](https://flatpak.github.io/xdg-desktop-portal/docs/)：列出了应用程序和后端可使用的所有接口。

## 10.6　XDG MIME 应用程序

*难度：进阶 ｜ 篇幅：13.9 KB ｜ 所属：Freedesktop.org*

将要讲到：共享 MIME 数据库、mimeapps.list、工具、故障排除

> **翻译状态：**
>
> - 本文（或部分内容）译自 [XDG MIME Applications](https://wiki.archlinux.org/title/XDG_MIME_Applications)，最近一次同步于 2026-07-20，若英文版本有所[更改](https://wiki.archlinux.org/title/XDG_MIME_Applications?diff=0&oldid=875640)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/XDG_MIME_Applications_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

[XDG MIME 应用程序规范](https://specifications.freedesktop.org/mime-apps-spec/latest/)基于[#共享 MIME 数据库](#共享_MIME_数据库)和[桌面项](../../01_安装与包管理/配置文件/桌面项.md)，提供[默认应用程序](../../02_系统管理与内核/系统管理/默认应用程序.md)。

1.  应用程序通过[桌面项](../../01_安装与包管理/配置文件/桌面项.md)描述它们能够处理哪些 MIME 类型。
2.  [desktop-file-utils](https://archlinux.org/packages/?name=desktop-file-utils)包 注册了一个 [pacman 钩子](../../01_安装与包管理/包管理器/Pacman.md)，用于构建由桌面项处理的 MIME 类型的缓存数据库，详见 [update-desktop-database(1)](https://man.archlinux.org/man/update-desktop-database.1)。
3.  应用程序可以通过将 XML 文件放置在 `/usr/share/mime/packages/` 中来安装新的 MIME 类型。
4.  [shared-mime-info](https://archlinux.org/packages/?name=shared-mime-info)包 注册了一个 [pacman 钩子](../../01_安装与包管理/包管理器/Pacman.md)，用于构建共享 MIME 信息数据库缓存，详见 [update-mime-database(1)](https://man.archlinux.org/man/update-mime-database.1)。
5.  [桌面环境](../桌面环境/桌面环境.md)和用户可以通过 [mimeapps.list](#mimeapps.list) 文件更改默认应用程序并添加或删除 MIME 类型与应用程序的关联。

## 共享 MIME 数据库

[XDG 共享 MIME 信息数据库规范](https://specifications.freedesktop.org/shared-mime-info-spec/latest/)促进了桌面环境之间的共享 MIME 数据库，并允许应用程序轻松地在系统范围内注册新的 MIME 类型。

该数据库是由软件包在 `/usr/share/mime/packages/` 中安装的 XML 文件构建的，使用来自 [shared-mime-info](https://archlinux.org/packages/?name=shared-mime-info)包 的工具。

`/usr/share/mime/` 中的文件不应直接编辑，但可以在 `~/.local/share/mime/` 树中为每个用户维护单独的数据库。

“URI 方案处理 \[...\] 通过处理 `x-scheme-handler/foo` MIME 类型的应用程序来进行，其中 foo 是相关的 URI 方案。”

### 新的 MIME 类型


此示例定义了一个新的 MIME 类型 `application/x-foobar`，并将其分配给任何以 *.foo* 结尾的文件。只需创建以下文件：

```
~/.local/share/mime/packages/application-x-foobar.xml
```

```
<?xml version="1.0" encoding="UTF-8"?>
<mime-info xmlns="http://www.freedesktop.org/standards/shared-mime-info">
    <mime-type type="application/x-foobar">
        <comment>foo 文件</comment>
        <icon name="application-x-foobar"/>
        <glob-deleteall/>
        <glob pattern="*.foo"/>
    </mime-type>
</mime-info>
```

然后更新 MIME 数据库：

```
$ update-mime-database ~/.local/share/mime
```

当然，如果没有桌面条目与 MIME 类型关联，这不会产生任何效果。您可能需要创建新的[桌面项](../../01_安装与包管理/配置文件/桌面项.md)或修改 [mimeapps.list](#mimeapps.list)。

## mimeapps.list

[XDG 标准](https://specifications.freedesktop.org/mime-apps-spec/latest/)是配置桌面环境最常用的方式。每个 MIME 类型的默认应用程序存储在 `mimeapps.list` 文件中，该文件可以存储在多个位置。它们按照以下顺序搜索，前面的关联优先于后面的关联：

| 路径 | 用途 |
| --- | --- |
| `~/.config/mimeapps.list` | 用户覆盖 |
| `/etc/xdg/mimeapps.list` | 系统范围的覆盖 |
| `~/.local/share/applications/mimeapps.list` | **（已废弃）**用户覆盖 |
| `/usr/local/share/applications/mimeapps.list`<br>`/usr/share/applications/mimeapps.list` | 分发提供的默认值 |

此外，还可以在名为 `*桌面环境*-mimeapps.list` 的文件中定义[桌面环境](../桌面环境/桌面环境.md)特定的默认应用程序，其中 `*桌面环境*` 是桌面环境的名称（来自 `XDG_CURRENT_DESKTOP` 环境变量）。例如，`/etc/xdg/xfce-mimeapps.list` 定义了 [Xfce](../Xfce/Xfce.md) 的系统范围默认应用程序覆盖。这些特定桌面的覆盖优先于相应的非特定桌面文件。例如，`/etc/xdg/xfce-mimeapps.list` 优先于 `/etc/xdg/mimeapps.list`，但仍会被 `~/.config/mimeapps.list` 覆盖。

> **提示：**虽然已废弃，但许多应用程序仍然读写 `~/.local/share/applications/mimeapps.list`。为了简化维护，可以将其符号链接到 `~/.config/mimeapps.list`：
>
> ```
> $ ln -s ~/.config/mimeapps.list ~/.local/share/applications/mimeapps.list
> ```

> **注意：**您还可能会在这些位置找到名为 `defaults.list` 的文件。此文件类似于 `mimeapps.list`，但仅列出默认应用程序（而不是已添加/删除的关联）。它现在已废弃，应手动与 `mimeapps.list` 合并。

要发现所有被扫描的文件，可以通过设置环境变量 `XDG_UTILS_DEBUG_LEVEL=2` 启用调试模式：例如，`xdg-mime query default *类型*` 命令将打印它搜索 MIME 信息的每个配置文件。

### 格式

考虑以下示例：

```
mimeapps.list
```

```
[Added Associations]
image/jpeg=bar.desktop;baz.desktop
video/H264=bar.desktop
[Removed Associations]
video/H264=baz.desktop
[Default Applications]
image/jpeg=foo.desktop
```

每个部分将一个或多个桌面条目分配给 MIME 类型。

- **Added Associations** 表示应用程序支持打开该 MIME 类型。例如，`bar.desktop` 和 `baz.desktop` 可以打开 JPEG 图像。这可能会影响您在文件浏览器中右键点击文件时看到的应用程序列表。
- **Removed Associations** 表示应用程序**不**支持该 MIME 类型。例如，`baz.desktop` 无法打开 H.264 视频。
- **Default Applications** 表示该应用程序应为打开该 MIME 类型的默认选择。例如，JPEG 图像应使用 `foo.desktop` 打开。这隐式地在应用程序和 MIME 类型之间添加了一个关联。如果有多个应用程序，它们会按顺序尝试。

每个部分都是可选的，如果不需要可以省略。

## 工具

虽然可以通过直接编辑 [mimeapps.list](#mimeapps.list) 和[#共享 MIME 数据库](#共享_MIME_数据库)来配置默认应用程序和 MIME 类型，但有许多工具可以简化这一过程。这些工具也很重要，因为应用程序可能将文件的打开委托给这些工具，而不是自己尝试实现 MIME 类型标准。

如果您使用的是[桌面环境](../桌面环境/桌面环境.md)，应首先检查它是否提供了自己的工具。应该优先使用这些工具，而不是其他替代方案。

官方的 [xdg-utils](Xdg-utils.md) 包含用于根据 XDG 标准管理 MIME 类型和默认应用程序的工具（[xdg-mime](Xdg-utils.md#xdg-mime)）。最重要的是，它提供了 [xdg-open](Xdg-utils.md)，许多应用程序用它来用默认应用程序打开文件。

### lsdesktopf

[lsdesktopf](https://aur.archlinux.org/packages/lsdesktopf/)AUR 提供了几种搜索 MIME 数据库和桌面 MIME 条目的方法。

例如，要查看系统中所有与 MIME 类型 `video` 相关联的 *.desktop* 文件的 MIME 扩展名，可以使用 `lsdesktopf --gm -gx video`，或要在 XML 数据库文件中搜索，请使用 `lsdesktopf --gdx -gx video`。要快速了解有多少个且哪些 *.desktop* 文件可以与某个 MIME 类型关联，请使用 `lsdesktopf --gen-mimeapps`。要查看 XML 数据库文件中的所有文件名扩展名，可以使用 `lsdesktopf --gdx -gfx`。

### selectdefaultapplication

[selectdefaultapplication-git](https://aur.archlinux.org/packages/selectdefaultapplication-git/)AUR 是一个 GUI 应用程序，列出了所有支持各种 MIME 类型的应用程序，并允许您快速将其设置为所有或一些它支持的 MIME 类型的默认应用程序（通过修改 `mimeapps.list`）。

它还显示了“可读”的名称和文件扩展名，因此您不需要记住 MIME 类型的名称。

### mime-tui

[mime-tui](https://aur.archlinux.org/packages/mime-tui/)AUR 是一个 TUI（文本用户界面）应用程序，它允许用户通过两种方式之一来编辑 mimetype 关联（通过修改 `mimeapps.list`）：一种是按应用程序编辑（此应用程序应支持哪些 mimetype 类型？），另一种是按 mimetype 编辑（此 mimetype 类型应与哪些应用程序关联？）。此外，它还能标记与已卸载应用程序关联的 mimetype 类型。

## 故障排除

如果文件未通过您期望的默认应用程序打开，可能有多个原因。您可能需要检查每种情况。

### 缺少桌面条目

需要一个[桌面项](../../01_安装与包管理/配置文件/桌面项.md)来将应用程序与 MIME 类型关联。确保存在这样的条目，并且可以用它来（手动）打开文件。

### 缺少关联

如果应用程序的桌面条目没有在其 `MimeType` 键下指定 MIME 类型，它在需要打开该类型时将不会被考虑。编辑 [mimeapps.list](#mimeapps.list)，将桌面文件与 MIME 类型关联起来。

### 非默认应用程序

如果桌面条目已与 MIME 类型关联，可能只是没有设置为默认值。编辑 [mimeapps.list](#mimeapps.list)，将其设置为默认关联。

### 非标准关联

应用程序可以忽略或仅部分实现 XDG 标准。检查是否使用了已废弃的文件，如 `~/.local/share/applications/mimeapps.list` 和 `~/.local/share/applications/defaults.list`。如果您尝试从其他应用程序（如 Web 浏览器或文件管理器）打开文件，请检查该应用程序是否有自己的方法来选择默认应用程序。

### .desktop 文件中影响应用程序启动的变量


支持该规范的桌面环境和文件管理器根据 *.desktop* 文件中的定义启动程序。参见[桌面项#应用程序项](../../01_安装与包管理/配置文件/桌面项.md#应用程序项)。

通常不需要配置打包的 *.desktop* 文件，但它可能并非没有缺陷。即使一个包含必要 MIME 类型描述的应用程序在 *.desktop* 文件中的 `MimeType` 变量被用于关联，它也可能无法正确启动，根本无法启动，或者启动时未能打开文件。

例如，这可能发生在 `Exec` 变量缺少打开文件所需的内部选项，或缺少如何在菜单中显示应用程序的配置。`Exec` 变量通常以 `%` 开头；有关当前支持的选项，请参见 [exec-variables](https://specifications.freedesktop.org/desktop-entry-spec/latest/exec-variables.html)。

以下表格列出了影响应用程序启动的 *.desktop* 文件中主要的变量条目，如果它与 MIME 类型关联。

| 变量名称 | 示例 1 内容 | 示例 2 内容 | 描述 |
| --- | --- | --- | --- |
| DBusActivatable | DBusActivatable=true | DBusActivatable=false | 应用程序与 [D-Bus](https://www.freedesktop.org/wiki/Software/dbus/) 交互。<br>另见配置： [D-Bus](https://specifications.freedesktop.org/desktop-entry-spec/latest/dbus.html)。 |
| MimeType | MimeType=application/vnd.oasis.opendocument.text | MimeType=application/vnd.sun.xml.math | 应用程序支持的 MIME 类型列表 |
| StartupWMClass | StartupWMClass=google-chrome | StartupWMClass=xpad | 将窗口与拥有该窗口的应用程序关联 |
| Terminal | Terminal=true | Terminal=false | 在默认终端中启动 |

### 空的 MIME 关联 / 在 KDE 中打开菜单

> [![](https://wiki.archlinuxcn.org/wzh/images/f/f0/Tango-go-next.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-go-next.png)**此页面或章节适合移动到 [KDE#疑难解答](../KDE/KDE.md#疑难解答)。**
>
> **附注：** KDE 特有的故障排除。（在 [Talk:XDG MIME 应用程序](https://wiki.archlinuxcn.org/wiki/Talk:XDG_MIME_%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F) 讨论）

安装 archlinux-xdg-menu 并运行 `XDG_MENU_PREFIX=arch- kbuildsycoca6`，或将 `export XDG_MENU_PREFIX=plasma-` 添加到 `.xinitrc`。
