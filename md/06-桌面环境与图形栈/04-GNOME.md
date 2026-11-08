# 第 4 章　GNOME

> 本章解决的是把装好的 Arch 推到能天天用的 GNOME 桌面：会话的安装、导航与配置，登录管理器 GDM 如何接下启动，文件管理与钥匙环、Gedit、Web、Flashback。
> 
> 先读《GNOME》，导航与配置别跳；再读《GDM》，装完会话不等于登录就位；《GNOME/文件》次之。存 SSH 密钥才读《GNOME/Keyring》，《提示与技巧》备查；Gedit、Web 可略过，Flashback 想回传统面板才装。最常踩的坑是照旧名找组件：多已改名，按旧称检索会扑空，先看“遗留名称”。

<!-- 本章 8 节，共 134 KB -->

## 4.1　GNOME/Gedit

*难度：入门 ｜ 篇幅：1.2 KB ｜ 所属：GNOME*

将要讲到：安装、配置、另见

[gedit](https://en.wikipedia.org/wiki/gedit)是GNOME的通用文本编辑器。

## 安装

安装[gedit](https://archlinux.org/packages/?name=gedit)包软件包。

若要使用额外功能，请安装[gedit-plugins](https://archlinux.org/packages/?name=gedit-plugins)包软件包。

Gedit有许多拼写检查词典可供使用，见[Language checking](../../06_软件与应用/词典/语言检查.md)。

## 配置

### 不在新的一行结束文档

若想确保gedit不会在新的一行结束文档，请执行以下操作：

```
$ gsettings set org.gnome.gedit.preferences.editor ensure-trailing-newline false
```

### 为编辑过的文件保存备份

若有需要，gedit可以为编辑过的文件创建备份副本，副本的名称和内容将与编辑前的原始文件的内容相同，但名称后面添加了“~”后缀。例如文件`file1`的副本名称为`file1~`。备份文件默认隐藏。

若要开启此功能，请访问gedit的首选项（GNOME Shell用户可以在gedit的全局菜单中找到）。在首选项中点击“编辑器”标签栏，勾选“在保存前创建备份文件”选项。

## 另见

- [Apps/Gedit - GNOME Wiki!](https://wiki.gnome.org/Apps/Gedit)

## 4.2　GNOME/Web

*难度：入门 ｜ 篇幅：4.3 KB ｜ 所属：GNOME*

将要讲到：安装、配置、参见

Web是[GNOME](GNOME.md)的默认网络浏览器。Web 提供了一个简单而简约的界面来访问互联网。虽然它主要是为 GNOME 开发的，但 Web 在其他[桌面环境](../桌面环境/桌面环境.md)中也能正常工作。

> **注意：**在3.4版本之前，Web 被称为 [Epiphany](https://wiki.gnome.org/Apps/Web)。该应用程序被赋予新的描述性名称，每种支持的语言都有一个。*Epiphany*这个名字仍然用在许多地方，如可执行文件名、一些软件包名、一些桌面条目和一些 GSettings 纲要。

## 安装

Web 能以 [epiphany](https://archlinux.org/packages/?name=epiphany)包 包安装。如果您想保存登录密码，请安装[gnome-keyring](https://archlinux.org/packages/?name=gnome-keyring)包。

## 配置

### 广告屏蔽

它是默认启用的，您可以通过解除勾选在*首选项*中的*阻止广告*禁用它。EasyList 是默认的阻止列表。所有列表都会定期刷新。

要获取当前启用的过滤器列表：

```
$ gsettings get org.gnome.Epiphany content-filters
```

可以按照 [https://gitlab.com/eyeo/filterlists/contentblockerlists](https://gitlab.com/eyeo/filterlists/contentblockerlists) 中的示例使用 JSON 格式的资源修改过滤器：

```
$ gsettings set org.gnome.Epiphany content-filters "['https://gitlab.com/eyeo/filterlists/contentblockerlists/-/raw/master/easylist_min_content_blocker.json', 'https://gitlab.com/eyeo/filterlists/contentblockerlists/-/raw/master/easylist+easylistchina-minified.json']"
```

> **提示：**[dconf-editor](https://archlinux.org/packages/?name=dconf-editor)包 可用作 GUI 替代品。

### 跟踪预防

Web 包括“智能跟踪器预防”，可以在“首选项”中启用。

### 火狐同步

Web 允许使用[火狐同步](https://www.mozilla.org/en-US/firefox/sync/)来同步书签、历史记录、密码和打开的标签。它可以在“导入和导出”下拉菜单中进行配置。

### Web 应用

Web 可以从网站创建 Web 应用程序并将它们添加到桌面菜单。要配置和删除它们，请在地址栏中输入 `about:applications`。

### 自定义样式表

Web 支持自定义样式表，您可以在**首选项**中的**外观**下启用。

使用下面的示例根据 Adwaita 深色变体设置新的标签页布局和颜色：

```
~/.config/epiphany/user-stylesheet.css
```

```
#overview {
  background-color: #2E3436 !important;
  max-width: 100% !important;
  max-height: 100% !important;
  position: fixed !important;
}

#overview .overview-title {
  color: white !important;
}
```

### 字体

Web 不会检查 GNOME 字体设置，但会检查 [Font configuration](../字体/字体配置.md)。

### 视频

有关所需的插件安装，请参阅 [GStreamer](../../06_软件与应用/多媒体/GStreamer.md)。

要启用硬件加速视频解码，请参阅 [GStreamer#Hardware video acceleration](../../06_软件与应用/多媒体/GStreamer.md#Hardware_video_acceleration) 和 [#Hardware 加速合成](#Hardware_加速合成)\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Project:%E7%A4%BE%E7%BE%A4%E9%A6%96%E9%A1%B5#损坏的链接)：无效的章节\]。

### 硬件加速合成

默认情况下，仅在需要（按需）显示 3D 变换时才使用硬件加速合成。

强制启用硬件加速合成：

```
$ gsettings set org.gnome.Epiphany.web:/ hardware-acceleration-policy 'always'
```

### 代理配置

Web 不尊重 socks_proxy，相反，您可以将 http_proxy 设置为 `socks:// URL` ：

```
export http_proxy=socks://127.0.0.1:1080
```

更多信息：[代理服务器#环境变量](../../03_网络与服务/代理服务器/代理服务器.md#Environment_variables)

## 参见

- [应用程序/网络 - GNOME Wiki！](https://wiki.gnome.org/Apps/Web)

## 4.3　GNOME/Flashback

*难度：入门 ｜ 篇幅：5.4 KB ｜ 所属：GNOME*

将要讲到：安装、启动、配置、提示与技巧、参见

> **翻译状态：**
>
> - 本文（或部分内容）译自 [GNOME/Flashback](https://wiki.archlinux.org/title/GNOME/Flashback)，最近一次同步于 2024-11-16，若英文版本有所[更改](https://wiki.archlinux.org/title/GNOME/Flashback?diff=0&oldid=820780)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/GNOME_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)/Flashback_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

[GNOME Flashback](https://wiki.gnome.org/Projects/GnomeFlashback)（之前称为“GNOME Fallback”）是一个为 GNOME 3 提供的外壳。桌面布局和底层技术与 GNOME 2 相似。它完全不使用 3D 加速，因此通常比启用了 llvmpipe 的 GNOME Shell 更加快速且对 CPU 的占用更低。

## 安装

GNOME Flashback 可以从 [gnome-flashback](https://archlinux.org/packages/?name=gnome-flashback)包 安装。建议同时安装其可选依赖项，以获得更完整的桌面环境。

您还可以安装以下提供额外 GNOME 面板小程序的包：

- **GNOME Applets** — 小型 GNOME 面板应用程序

: [https://wiki.gnome.org/Projects/GnomeApplets](https://wiki.gnome.org/Projects/GnomeApplets) || [gnome-applets](https://archlinux.org/packages/?name=gnome-applets)包

- **Pomodoro Applet** — 用于计时番茄工作法的 GNOME 面板小程序

: [https://github.com/stump/pomodoro-applet](https://github.com/stump/pomodoro-applet) || [pomodoro-applet](https://aur.archlinux.org/packages/pomodoro-applet/)AUR

- **Sensors Applet** — 显示硬件传感器读数（包括 CPU 温度、风扇速度和电压读数）的 GNOME Flashback 面板小程序

: [https://sensors-applet.sourceforge.net/](https://sensors-applet.sourceforge.net/) || [sensors-applet](https://archlinux.org/packages/?name=sensors-applet)包

建议安装 [gnome](https://archlinux.org/groups/x86_64/gnome/)包组，其中包含标准 GNOME 体验所需的应用程序。

## 启动

### 图形化登录

在所选[显示管理器](../图形用户界面/显示管理器.md)的菜单中选择“GNOME Flashback (Metacity)”。

希望使用 [Compiz](../堆叠式窗口管理器/Compiz.md) 的用户应选择“GNOME Flashback (Compiz)”。

### 手动启动

- 对于 **GNOME Flashback (Metacity)** 会话，在 `~/.xinitrc` 文件中添加以下内容：

    ```
    export XDG_CURRENT_DESKTOP=GNOME-Flashback:GNOME
    exec gnome-session --session=gnome-flashback-metacity
    ```

- 对于 **GNOME Flashback (Compiz)** 会话，在 `~/.xinitrc` 文件中添加以下内容：

    ```
    export XDG_CURRENT_DESKTOP=GNOME-Flashback:GNOME
    exec gnome-session --session=gnome-flashback-compiz
    ```

编辑 `.xinitrc` 后，可以使用 *startx* 启动 GNOME Flashback。详情请参见 [xinitrc](../Xorg%20命令/Xinit.md)。

## 配置

GNOME Flashback 与 GNOME 共享大部分设置。更多详情请参见 [GNOME#配置](GNOME.md#配置)。

### 自定义 GNOME 面板

- 要配置面板，按住 `Alt` 键，然后在空白区域右键单击面板。
- 要移动面板上的小程序，按住 `Alt` 键，并使用中键拖动它。

> **注意：**如果 `Alt+右键单击`组合无法使用，请尝试使用 `Super+Alt+右键单击`。

### 替代窗口管理器

您可以通过创建一个自定义的 GNOME 会话来使用替代的[窗口管理器](../窗口管理器/窗口管理器.md)，该会话应包含以下组件：

```
RequiredComponents=gnome-flashback-init;gnome-flashback;gnome-panel;窗口管理器;gnome-settings-daemon;nautilus-classic;
```

其中**窗口管理器**是您希望使用的窗口管理器。请参见 [GNOME/提示与技巧#自定义 GNOME 会话](GNOME_提示与技巧.md#自定义_GNOME_会话)。

另请参见[这篇文章](https://web.archive.org/web/20190808234014/http://makandracards.com/makandra/1367-running-the-awesome-window-manager-within-gnome)，了解如何在 GNOME 中运行 awesome 窗口管理器。

## 提示与技巧

### 面板速度设置

**隐藏/显示延迟**

要调整启用自动隐藏时面板消失或重新出现的时间，请执行以下命令：

```
$ gsettings set org.gnome.gnome-panel.toplevel:/org/gnome/gnome-panel/layout/toplevels/panel/ hide-delay time
$ gsettings set org.gnome.gnome-panel.toplevel:/org/gnome/gnome-panel/layout/toplevels/panel/ unhide-delay time
```

其中 *panel* 为 *top-panel* 或 *bottom-panel*，*time* 为毫秒值，例如 300。

**动画速度**

要设置面板动画的速度，请执行以下命令：

```
$ gsettings set org.gnome.gnome-panel.toplevel:/org/gnome/gnome-panel/layout/toplevels/panel/ animation-speed value
```

其中 *panel* 为 *top-panel* 或 *bottom-panel*，*value* 为 `"'fast'"`, `"'medium'"` 或 `"'slow'"`。

### 替换应用程序菜单图标

> **注意：**此更改将在更新您的图标主题包时被覆盖。

将 `/usr/share/icons/*icon-theme*/16x16/places/start-here.png` 替换为您自己的图标（其中 `*icon-theme*` 为您的图标主题的名称）。

完成更改后，重新启动 GNOME 面板： `gnome-panel --replace`。

## 参见

- [GnomeFlashback - GNOME Wiki](https://wiki.gnome.org/Projects/GnomeFlashback)

## 4.4　GNOME/文件

*难度：高级 ｜ 篇幅：20.0 KB ｜ 所属：GNOME*

将要讲到：安装、配置、提示与技巧、疑难解答

**文件**是[GNOME](https://wiki.gnome.org/)的默认文件管理器。文件的目的是提供一种管理文件和应用的简化方法。可扩展以通过SFTP、WebDAV、NFS等不同协议实现网络文件访问。

> **注意：**在3.6版本之前，文件叫做[Nautilus](https://wiki.gnome.org/Apps/Nautilus)，后来则对每种语言做了新的描述性名称。*Nautilus*这个名字仍然用在许多地方，如可执行文件名、软件包名、桌面条目和GSettings Schema等。

## 安装

安装 [nautilus](https://archlinux.org/packages/?name=nautilus)包 软件包，此软件包是 [gnome](https://archlinux.org/groups/x86_64/gnome/)包组 组的一部分。见[文件管理器功能#额外功能](../../06_软件与应用/文件管理器/文件管理器功能.md#额外功能)。

> **注意：**“文件”不依赖 [gnome-shell](https://archlinux.org/packages/?name=gnome-shell)包，只需要 [gnome-desktop](https://archlinux.org/packages/?name=gnome-desktop)包。

### 扩展

#### 网络共享与虚拟文件系统

通过安装 [gvfs](https://archlinux.org/packages/?name=gvfs)包 及各种 `gvfs-*` 软件包，可以添加对多种基于网络的文件系统（例如 SMB、NFS、WebDAV、Nextcloud）以及部分移动设备（Android MTP、Apple AFC）的支持。

有关更多信息及其他受支持的虚拟文件系统，请参见 [文件管理器功能](../../06_软件与应用/文件管理器/文件管理器功能.md)。

#### 其他示例

> **注意：**用 [Python](../../07_开发与工具/编程语言/Python.md) 编写的扩展需要 Nautilus 扩展 API 的 Python 绑定（[nautilus-python](https://archlinux.org/packages/?name=nautilus-python)包 包）。

- **Actions For Nautilus** — 允许在文件选择右键菜单中添加任意操作的扩展。（使用 Python 编写）

: [https://github.com/bassmanitram/actions-for-nautilus](https://github.com/bassmanitram/actions-for-nautilus) || [actions-for-nautilus-git](https://aur.archlinux.org/packages/actions-for-nautilus-git/)AUR

- **Folder Color** — 用于选择文件夹颜色的文件浏览器扩展。（使用 Python 编写）

: [https://github.com/costales/folder-color](https://github.com/costales/folder-color) || [folder-color-nautilus](https://aur.archlinux.org/packages/folder-color-nautilus/)AUR
: > **提示：**此扩展仅适用于包含额外彩色图标的图标主题，例如：
> [numix-icon-theme-git](https://aur.archlinux.org/packages/numix-icon-theme-git/)AUR、[vibrancy-colors](https://aur.archlinux.org/packages/vibrancy-colors/)AUR、[humanity-icon-theme](https://aur.archlinux.org/packages/humanity-icon-theme/)AUR、[mint-x-icons](https://aur.archlinux.org/packages/mint-x-icons/)AUR。

- **Nautilus Admin** — 在菜单中添加“以管理员身份打开”或“以管理员身份编辑”选项。（使用 Python 编写）

: [https://github.com/MacTavishAO/nautilus-admin-gtk4](https://github.com/MacTavishAO/nautilus-admin-gtk4) || [nautilus-admin-gtk4](https://aur.archlinux.org/packages/nautilus-admin-gtk4/)AUR

- **Nautilus Annotations** — 为文件和目录添加注释。（使用 C 编写）

: [https://gitlab.gnome.org/madmurphy/nautilus-annotations/](https://gitlab.gnome.org/madmurphy/nautilus-annotations/) || [nautilus-annotations](https://aur.archlinux.org/packages/nautilus-annotations/)AUR

- **Nautilus Bluetooth** — 在菜单中添加“通过蓝牙发送”选项。（使用 C 编写）

: [https://gitlab.gnome.org/madmurphy/nautilus-bluetooth/](https://gitlab.gnome.org/madmurphy/nautilus-bluetooth/) || [nautilus-bluetooth](https://aur.archlinux.org/packages/nautilus-bluetooth/)AUR

- **Nautilus Checksums** — 在 Nautilus 的属性窗口中添加校验和信息。（使用 C 编写）

: [https://gitlab.gnome.org/madmurphy/nautilus-checksums/](https://gitlab.gnome.org/madmurphy/nautilus-checksums/) || [nautilus-checksums](https://aur.archlinux.org/packages/nautilus-checksums/)AUR

- **Nautilus Compare** — Nautilus 的右键菜单文件差异比较扩展。它在 Nautilus 右键菜单中提供“比较”、“与 ~/foo/bar 比较”和“稍后比较”选项，默认使用 `meld` 进行比较。（使用 Python 编写）

: [https://launchpad.net/nautilus-compare](https://launchpad.net/nautilus-compare) || [nautilus-compare](https://aur.archlinux.org/packages/nautilus-compare/)AUR

- **Nautilus Hide** — 在菜单中添加“隐藏”/“取消隐藏”选项。（使用 C 编写）

: [https://gitlab.gnome.org/madmurphy/nautilus-hide/](https://gitlab.gnome.org/madmurphy/nautilus-hide/) || [nautilus-hide](https://aur.archlinux.org/packages/nautilus-hide/)AUR

- **Nautilus Image Converter** — 调整图像大小或旋转图像。（使用 C 编写）

: [https://gitlab.gnome.org/coreyberla/nautilus-image-converter](https://gitlab.gnome.org/coreyberla/nautilus-image-converter) || [nautilus-image-converter](https://archlinux.org/packages/?name=nautilus-image-converter)包

- **Nautilus Launch** — 允许通过右键菜单运行可执行文件和启动器的 Nautilus 扩展。（使用 C 编写）

: [https://gitlab.gnome.org/madmurphy/nautilus-launch](https://gitlab.gnome.org/madmurphy/nautilus-launch) || [nautilus-launch](https://aur.archlinux.org/packages/nautilus-launch/)AUR

- **Nautilus Open Any Terminal** — 在右键菜单中添加使用其他终端模拟器打开的选项的 Nautilus 扩展。

: [https://github.com/Stunkymonkey/nautilus-open-any-terminal](https://github.com/Stunkymonkey/nautilus-open-any-terminal) || [nautilus-open-any-terminal](https://aur.archlinux.org/packages/nautilus-open-any-terminal/)AUR

- **Nautilus Metadata Editor** — 为以下 MIME 类型提供简单元数据编辑器的 Nautilus 扩展：`audio/x-mp3`、`audio/x-flac`、`audio/x-vorbis+ogg`、`audio/x-speex+ogg`、`audio/x-musepack`、`audio/x-wavpack`、`audio/x-tta`、`audio/x-aiff`、`audio/m4a`、`video/mp4`、`video/x-ms-asf`。（使用 C 和 Vala 编写）

: [https://gitlab.com/nvlgit/nautilus-metadata-editor-extension](https://gitlab.com/nvlgit/nautilus-metadata-editor-extension) || [nautilus-metadata-editor](https://aur.archlinux.org/packages/nautilus-metadata-editor/)AUR

- **Nautilus Share** — 通过 Samba 共享文件夹的 Nautilus 扩展。（使用 C 编写）

: [https://gitlab.gnome.org/coreyberla/nautilus-share](https://gitlab.gnome.org/coreyberla/nautilus-share) || [nautilus-share](https://archlinux.org/packages/?name=nautilus-share)包

- **Seahorse Nautilus** — 为 GNOME 文件管理器提供 PGP 加密和签名功能。（使用 C 编写）

: [https://gitlab.gnome.org/GNOME/seahorse-nautilus](https://gitlab.gnome.org/GNOME/seahorse-nautilus) || [seahorse-nautilus](https://aur.archlinux.org/packages/seahorse-nautilus/)AUR
: > **警告：***seahorse-nautilus* 的上游代码仓库已归档，并且由于存在尚未修复的错误，上游明确警告不要使用该扩展。

- **[Sushi](https://en.wikipedia.org/wiki/GNOME_sushi)** — Nautilus 的快速文件预览工具，属于 [gnome](https://archlinux.org/groups/x86_64/gnome/)包组 软件包组。

: [https://gitlab.gnome.org/GNOME/sushi](https://gitlab.gnome.org/GNOME/sushi) || [sushi](https://archlinux.org/packages/?name=sushi)包

> **提示：**若要编写新的扩展，辅助脚本 [nextgen](https://aur.archlinux.org/packages/nextgen/)AUR 可以让您轻松地设置 **GNOME**文件的新扩展项目（默认使用C语言）。

### 集成

有些应用自带与**GNOME/文件**集成的功能。

- **Eiciel** — GNOME file ACL editor — The application includes an "**Eiciel Nautilus**" extension that adds graphical [ACL](../../08_安全与加密/访问控制/访问控制列表.md) editor into the file properties window (written in C++)

: [https://rofi.roger-ferrer.org/eiciel/](https://rofi.roger-ferrer.org/eiciel/) || [eiciel](https://aur.archlinux.org/packages/eiciel/)AUR

- **File Roller** — An application for browsing archives — The application includes a "**Nautilus FileRoller**" extension (written in C)

: [https://wiki.gnome.org/Apps/FileRoller](https://wiki.gnome.org/Apps/FileRoller) || [file-roller](https://archlinux.org/packages/?name=file-roller)包

- **GNOME Terminal** — The GNOME Terminal Emulator — The application includes a "**Terminal Nautilus**" extension (written in C)

: [https://wiki.gnome.org/Apps/Terminal](https://wiki.gnome.org/Apps/Terminal) || [gnome-terminal](https://archlinux.org/packages/?name=gnome-terminal)包

- **Tilix** — A tiling terminal emulator for GNU/Linux using GTK+ 3 — The application includes an extension for Nautilus which adds an "Open in Tilix" option to the context menu (written in Python)

: [https://github.com/gnunn1/tilix](https://github.com/gnunn1/tilix) || [tilix](https://aur.archlinux.org/packages/tilix/)AUR
: > **注意：**“Open in Tilix”扩展将 [nautilus-python](https://archlinux.org/packages/?name=nautilus-python)包 软件包标记为可选依赖，但必须安装才能使用。

#### 依赖非自由软件的集成

一些 **GNOME/文件**的集成功能虽然免费/自由，但可能依赖于非自由软件，例如：

- **Code Nautilus** — Nautilus extension to open files and directories in [Visual Studio Code](../../07_开发与工具/文本编辑器/Visual%20Studio%20Code.md) (written in Python)

: [https://github.com/cra0zy/code-nautilus](https://github.com/cra0zy/code-nautilus) || [code-nautilus-git](https://aur.archlinux.org/packages/code-nautilus-git/)AUR

- **JetBrains Nautilus** — Nautilus extension to open files and directories in JetBrains Toolbox installed products (written in Python)

: [https://github.com/encounter/jetbrains-nautilus](https://github.com/encounter/jetbrains-nautilus) || [jetbrains-nautilus-git](https://aur.archlinux.org/packages/jetbrains-nautilus-git/)AUR

- **Nautilus Code** — Nautilus extension which adds right-click menu items to open current folder in [Visual Studio Code](../../07_开发与工具/文本编辑器/Visual%20Studio%20Code.md) or GNOME Builder (written in C)

: [https://github.com/realmazharhussain/nautilus-code](https://github.com/realmazharhussain/nautilus-code) || [nautilus-code](https://aur.archlinux.org/packages/nautilus-code/)AUR

## 配置

文件很容易进行图形化配置，但并非所有选项都能在首选项中找到。更多选项可以使用*dconf-editor*在`org.gnome.nautilus`中找到。

> **注意：**如果在GNOME桌面环境之外使用文件，则必须确保`/usr/lib/gsd-xsettings`正在运行，否则dconf设置不会在文件中应用。

### 改变默认项目视图

设置`default-folder-viewer`变量可以改变项目的默认视图，如设为列表视图：

```
$ gsettings set org.gnome.nautilus.preferences default-folder-viewer 'list-view'
```

### 按类型排序

若要默认用类型排序所有文件夹中的文件：

```
$ gsettings set org.gnome.nautilus.preferences default-sort-order 'type'
```

### 始终显示文本文件路径

文件工具栏默认显示用于路径导航的按钮栏界面。若要用**键盘**输入路径，则需按`Ctrl+l`显示文本路径栏。

若要始终以文本路径栏显示，可以使用以下*gsettings*命令：

```
$ gsettings set org.gnome.nautilus.preferences always-use-location-entry true
```

> **注意：**改变此设置后将无法再显示按钮栏，只有把值设为**false**时才能同时使用两种路径栏。

## 提示与技巧

### 缩略图

见[文件管理器功能#缩略图预览](../../06_软件与应用/文件管理器/文件管理器功能.md#缩略图预览)。

> **注意：**在[linux-hardened](https://archlinux.org/packages/?name=linux-hardened)包中，缩略图会生成失败（所有缩略图都在`~/.cache/thumbnails/fail/`目录中），这是由于非特权用户的命名空间因为安全原因在该内核上默认禁用。Nautilus使用`bwrap` (由[bubblewrap](https://archlinux.org/packages/?name=bubblewrap)包提供)对缩略图进行沙盒处理。尽管存在安全隐患，你仍然可以选择[启用非特权用户命名空间](../../08_安全与加密/安全/安全.md#禁用非特权用户命名空间)。有关更多信息，请参见 [FS#63316](https://bugs.archlinux.org/task/63316)。

有时会无法显示视频缩略图。若要解决此问题（正如[No video thumbnails on nautilus](https://bbs.archlinux.org/viewtopic.php?id=168626)），必须安装[ffmpegthumbnailer](https://archlinux.org/packages/?name=ffmpegthumbnailer)包、[gst-libav](https://archlinux.org/packages/?name=gst-libav)包、[gst-plugins-ugly](https://archlinux.org/packages/?name=gst-plugins-ugly)包并移除`~/.cache/thumbnails/fail/`里的内容。

### 用右键菜单创建新文档

若要使用此选项，请在您的主文件夹中创建一个`~/Templates/`（模板）文件夹，并通过终端`touch ~/Templates/new`或任何文件管理器在该文件夹中放置一个空文件，然后重新启动文件。

在非英语系统中，模板目录的名称可能不同。可以用`xdg-user-dir TEMPLATES`找到实际目录。

可以在`~/.config/user-dirs.dirs`文件中配置模板目录。

```
XDG_TEMPLATES_DIR="$HOME/some/path"
```

### 隐藏文件

GNOME文件和大部分文件管理器一样，默认隐藏名称以“.”开头的文件。

GNOME 文件管理器还会隐藏名称列在同一目录下的 `.hidden` 文件中的文件（每行一个文件名）。可以使用 [nautilus-hide](https://aur.archlinux.org/packages/nautilus-hide/)AUR 扩展，方便在此类 `.hidden` 文件中添加或删除条目。

### 在Tilix中打开当前路径

如果你使用 [tilix](https://aur.archlinux.org/packages/tilix/)AUR 终端，可以通过安装其可选依赖 [nautilus-python](https://archlinux.org/packages/?name=nautilus-python)包，在 GNOME 文件管理器的右键菜单中添加“在 Tilix 中打开”选项。

### 将文件夹加入书签

若要将文件夹添加到你的书签中，只需在Nautilus中打开该文件夹，并按`Ctrl+d`。请注意，书签列表与其它基于GNOME的图形化文件管理器（如Nemo）共享，因此在其中增减的文件夹也会影响到其它文件管理器中的书签。

### 自定义脚本

位于`~/.local/share/nautilus/scripts`的脚本可以运行于文件的右键关联菜单。

The context menu can also be organized into subfolders, e.g. `~/.local/share/nautilus/scripts/images` and `~/.local/share/nautilus/scripts/music`.

Scripts have access to the following environment variables:

```
NAUTILUS_SCRIPT_SELECTED_FILE_PATHS
NAUTILUS_SCRIPT_SELECTED_URIS
NAUTILUS_SCRIPT_CURRENT_URI
NAUTILUS_SCRIPT_WINDOW_GEOMETRY
```

一些脚本示例:

```
~/.local/share/nautilus/scripts/open-terminal-here
```

```
#!/bin/sh
gnome-terminal
```

```
~/.local/share/nautilus/scripts/remove-extension
```

```
#!/bin/sh
echo "$NAUTILUS_SCRIPT_SELECTED_FILE_PATHS" | while read -r filename; do
    mv -n "$filename" "${filename%.*}"
done
```

> **注意：**Make sure the scripts are marked as executable. You may have to restart nautilus with `nautilus -q` for them to show up.

#### Keybinds

Keybinds to execute scripts can be assigned in the `~/.config/nautilus/scripts-accels` file:

```
; Example Keybinds
; Modifiers: <Control> <Alt> <Shift>
F4 open-terminal-here
<Alt>x remove-extension
```

> **注意：**You cannot overwrite any pre-existing nautilus keybinds.

## 疑难解答

### 文件已不再是默认文件管理器

这可能是因为重置了路径的文件关联，安装[anjuta](https://aur.archlinux.org/packages/anjuta/)AUR或者[visual-studio-code-bin](https://aur.archlinux.org/packages/visual-studio-code-bin/)AUR往往会造成这种情况。

若要解决此问题，打开文件，右键点击一个文件夹，然后选择*使用其它程序打开 > 文件 > 选择*，就会把路径的关联设回文件。

若没有解决此问题，见[文件管理器功能#未在文件管理器中打开目录](../../06_软件与应用/文件管理器/文件管理器功能.md#未在文件管理器中打开目录)。

### 每次复制操作后都会卡几秒

如果系统中安装了[kdeconnect](https://archlinux.org/packages/?name=kdeconnect)包，问题可能来自其文件共享模块，停用文件共享即可。

### 共享文件夹的 Windows 计算机未显示在网络视图中

> **提示：**有关技术细节，请参见 [Samba#Windows 1709 及更高版本无法在“网络”视图中发现 Samba 服务器](../../03_网络与服务/网络共享/Samba.md#Windows_1709_及更高版本无法在“网络”视图中发现_Samba_服务器)。

要启用 [WSD 支持](https://gitlab.gnome.org/GNOME/gvfs/-/issues/506)，请安装 [gvfs-wsdd](https://archlinux.org/packages/?name=gvfs-wsdd)包，使 GNOME 文件管理器能够在网络视图中发现较新版本的 Windows 计算机。

### 挂载选项中缺少 WebDAV

安装 [gvfs-dnssd](https://archlinux.org/packages/?name=gvfs-dnssd)包 并重新启动 Nautilus。

### 未安装 gnome-console 时“作为程序运行”无法正常工作

右键菜单中的“作为程序运行”选项被硬编码为使用 [gnome-console](https://archlinux.org/packages/?name=gnome-console)包。如果你使用其他终端模拟器，且系统中未安装默认的 GNOME 终端，则该菜单选项不会执行任何操作。

由于该选项只是直接执行 `/usr/bin/kgx`（[gnome-console](https://archlinux.org/packages/?name=gnome-console)包 的可执行文件），因此可以创建一个名为 `kgx` 的脚本（如果你的终端支持相同的命令行参数，也可以创建符号链接），并将其放置在 `/usr/bin/` 中，从而让“作为程序运行”右键菜单选项使用你指定的终端。

以下示例展示如何使用 [wezterm-git](https://aur.archlinux.org/packages/wezterm-git/)AUR 作为“作为程序运行”功能的终端：

```
/usr/bin/kgx
```

```
#!/bin/sh

wezterm -e $@
```

> **注意：**子程序执行结束后，[gnome-console](https://archlinux.org/packages/?name=gnome-console)包 会保持窗口打开并显示输出，直到用户手动关闭。而其他终端模拟器（例如 wezterm）会在程序退出后立即关闭窗口。可以在上述脚本末尾添加类似 `read -n 1 -s -r -p "Press any key to continue"` 的命令，使终端等待用户按下任意键后再关闭。

### 在任意终端中打开目录

可以通过右键菜单中的“打开方式...”选项，在终端模拟器中打开目录。但终端对应的 XDG 桌面条目必须包含适当的 `Exec` 和 `MimeType` 字段值。

以下示例展示如何为 Alacritty 终端模拟器创建额外的桌面条目，使其能够被“打开方式...”菜单识别。

```
~/.local/share/applications/alacritty-directory.desktop
```

```
[Desktop Entry]
Type=Application

# `working-directory` 参数需要提供一个值
Exec=alacritty --working-directory %f
# MimeType 包含特殊类型 `inode/directory;`
MimeType=inode/directory;

Icon=Alacritty
Terminal=false
Categories=System;TerminalEmulator;
Name=Open in Alacritty
GenericName=Terminal
StartupNotify=true
StartupWMClass=Alacritty
```

运行 `update-desktop-database ~/.local/share/applications/` 以应用更改。

如果终端应用程序意外成为默认文件管理器，可以使用 `xdg-mime default org.gnome.Nautilus.desktop inode/directory` 命令修复。

请检查你所使用的终端应用程序的命令行参数和桌面条目文件，并据此调整上述示例。

## 4.5　GNOME/Keyring

*难度：高级 ｜ 篇幅：21.7 KB ｜ 所属：GNOME*

将要讲到：安全性、安装、用图形界面管理、使用钥匙环、SSH 密钥、提示与技巧

[GNOME Keyring](https://wiki.gnome.org/Projects/GnomeKeyring) 是“GNOME 中一组用于存储机密、密码、密钥、证书，并将其提供给应用程序使用的组件”。

它提供了 [org.freedesktop.secrets](https://www.freedesktop.org/wiki/Specifications/secret-storage-spec/)，这是一个 API，允许客户端应用借助运行在用户登录会话中的服务来安全地存储机密。

> **注意：**自 [GNOME 51](https://release.gnome.org/51/) 起，默认的密钥存储由 Oo7（英语：[Oo7](https://wiki.archlinux.org/title/Oo7)） 提供。

## 安全性

### 防御恶意应用

过去曾报告过一个关于 GNOME/Keyring API 行为的安全问题（即 [CVE-2018-19358](https://nvd.nist.gov/vuln/detail/CVE-2018-19358)）。只要钥匙环处于解锁状态，任何应用都能[轻易读取任何机密](https://github.com/sungjungk/keyring_crack)。而如果用户已登录，那么登录/默认钥匙环就是解锁的。现有的 D-Bus 防护机制（涉及 busconfig 和 policy XML 元素）默认并未启用，而且就算启用也很容易被绕过。

GNOME 项目[不认同](https://gitlab.gnome.org/GNOME/gnome-keyring/-/issues/5#note_1876550)这份漏洞报告，因为按照他们声明的安全模型，不应允许不可信的应用与 secret service 通信。

通过行为规范的沙箱（例如未添加额外权限的 Flatpak）运行的应用，对会话总线只有经过过滤的访问权限。

### 会话锁定时钥匙环并未锁定

会话锁定时，钥匙环并不会自动锁定。 这意味着密码仍留在内存中，从而使系统暴露于 [DMA 攻击](https://en.wikipedia.org/wiki/DMA_attack)之下。

## 安装

[gnome-keyring](https://archlinux.org/packages/?name=gnome-keyring)包 是 [gnome](https://archlinux.org/groups/x86_64/gnome/)包组 组的成员，因此在运行 GNOME 的系统上通常已经存在。在其他情况下，也可以单独安装该软件包。还应安装 [libsecret](https://archlinux.org/packages/?name=libsecret)包，以便让其他应用访问你的钥匙环。虽然 [libgnome-keyring](https://aur.archlinux.org/packages/libgnome-keyring/)AUR 已被弃用（并由 *libsecret* 取代），但某些应用可能仍然需要它。

gnome-keyring-daemon 会在登录时通过 systemd 用户服务自动启动。它也可以按需通过套接字启动。

与 GNOME Keyring 相关的其他实用工具包括：

- **secret-tool** — 从命令行访问 GNOME Keyring（以及任何其他实现了 [DBus Secret Service API](https://specifications.freedesktop.org/secret-service-spec/latest/) 的服务）。

: [https://wiki.gnome.org/Projects/Libsecret](https://wiki.gnome.org/Projects/Libsecret) || [libsecret](https://archlinux.org/packages/?name=libsecret)包

- **lssecret** — 使用 *libsecret* 列出所有机密条目（例如 GNOME Keyring）。

: [https://gitlab.com/GrantMoyer/lssecret](https://gitlab.com/GrantMoyer/lssecret) || [lssecret-git](https://aur.archlinux.org/packages/lssecret-git/)AUR

## 用图形界面管理

你可以使用 Seahorse 管理 GNOME Keyring 的内容；请安装 [seahorse](https://archlinux.org/packages/?name=seahorse)包 软件包。

钥匙环的密码（例如默认钥匙环 “Login”）可以修改，甚至可以移除。更多信息见 GNOME 帮助中的 [创建新钥匙环](https://help.gnome.org/seahorse/keyring-create.html) 和 [更新钥匙环密码](https://help.gnome.org/seahorse/keyring-update-password.html)。

## 使用钥匙环

[PAM](../../02_系统管理与内核/内核/PAM.md) 模块 *pam_gnome_keyring.so* 会部分初始化 GNOME Keyring，并在该过程中解锁 *login* 钥匙环。gnome-keyring-daemon 会通过 systemd 用户服务自动启动。

### PAM 步骤

> **注意：**要在**不使用自动登录**的情况下实现自动解锁，用户账户的密码应与 *login* 钥匙环的密码相同。参见[#用户密码变化时自动更改钥匙环密码](#用户密码变化时自动更改钥匙环密码)。

> **提示：**
>
> - 要在自动登录的情况下实现自动解锁，可以把钥匙环的密码设为空。请注意，这种情况下钥匙环的内容将以未加密形式存储。
> - 另一种做法是，如果使用 GDM 和 LUKS，当钥匙环密码与 LUKS 密码一致时，GDM 可以解锁你的钥匙环。为此，你需要在 [mkinitcpio.conf 中使用 systemd 初始化](../../01_安装与包管理/Initramfs/Mkinitcpio.md#钩子（HOOKS）)，并设置[相应的内核参数](../../08_安全与加密/静态数据加密/Dm-crypt_系统配置.md#使用systemd-cryptsetup-generator)，同时应确保系统的实时时钟设为 UTC 而非本地时间（见 ）。更多细节见 。
> - 如果你希望跳过 PAM 这一步，那就必须手动或以其他方式解锁钥匙环。参见[#在桌面环境（KDE、GNOME、XFCE……）之外使用 gnome-keyring-daemon](#在桌面环境（KDE、GNOME、XFCE……）之外使用_gnome-keyring-daemon)和 [GnomeKeyring wiki](https://wiki.gnome.org/Projects/GnomeKeyring/RunningDaemon)。

使用显示管理器时，多数情况下钥匙环开箱即用。[GDM](GDM.md)、[LightDM](../../06_软件与应用/Canonical/LightDM.md)、[LXDM](../LXDE/LXDM.md) 和 [SDDM](../显示管理器/SDDM.md) 已经带有必要的 PAM 配置。对于不会自动解锁钥匙环的显示管理器，请编辑对应的文件，而不是下文提到的 `/etc/pam.d/login`。

使用基于控制台的登录方式时（即不使用显示管理器），请编辑 `/etc/pam.d/login`：

在 `auth` 部分的末尾添加 `auth optional pam_gnome_keyring.so`，并在 `session` 部分的末尾添加 `session optional pam_gnome_keyring.so auto_start`。

```
/etc/pam.d/login
```

```
#%PAM-1.0

auth       required     pam_securetty.so
auth       requisite    pam_nologin.so
auth       include      system-local-login
auth       optional     pam_gnome_keyring.so
account    include      system-local-login
session    include      system-local-login
session    optional     pam_gnome_keyring.so auto_start
```

> **注意：**如果使用 [greetd](../显示管理器/Greetd.md) 登录管理器，需要修改的文件是 `/etc/pam.d/greetd`，而不是 `/etc/pam.d/login`。

### 使用指纹登录时解锁

使用 [fprintd](../../08_安全与加密/指纹识别/Fprint.md) 进行指纹认证时不会设置 `PAM_AUTHTOK`：读取器返回的是判定结果，而不是密码。因此 *pam_gnome_keyring.so* 没有可用于解锁 *login* 钥匙环的东西，第一个请求机密的程序便会提示输入密码 。任何不产生账户密码的认证方式都是如此。

可以考虑的做法：

- 在会话的首次提示中输入账户密码。此后该会话余下的时间里钥匙环都保持解锁。
- 按[#PAM 步骤](#PAM_步骤)中所述，把 *login* 钥匙环的密码设为空。此时钥匙环内容将以未加密形式存储。
- 用一个在 `pam_gnome_keyring.so` 之前运行的 PAM 模块把 `PAM_AUTHTOK` 设为钥匙环密码。该密码存放在哪里、由什么保护，正是这类实现之间的区别所在。

## SSH 密钥

GNOME Keyring 可以充当 [ssh-agent](../../03_网络与服务/SSH/SSH%20密钥.md#ssh-agent) 的包装层。在这种模式下，每次需要解锁 SSH 密钥时，它都会显示一个图形化密码输入对话框。该对话框带有一个记住所输入密码的复选框，若勾选，那么只要你的登录钥匙环处于解锁状态，今后就能完全无密码地使用该密钥。

自 [1:46](https://gitlab.gnome.org/GNOME/gnome-keyring/-/commit/25c5a1982467802fa12c6852b03c57924553ba73) 版起，gnome-keyring-daemon 的构建中默认禁用 SSH 功能。该功能已被[迁移](https://gitlab.gnome.org/GNOME/gcr/-/merge_requests/67)到 `/usr/lib/gcr-ssh-agent`，它是 [gcr-4](https://archlinux.org/packages/?name=gcr-4)包 软件包的一部分。

### 配置 gcr

启用并启动 `gcr-ssh-agent.socket` 这个 systemd [用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)。

该单元启动后，会创建套接字文件 `$XDG_RUNTIME_DIR/gcr/ssh`。

如果 `gcr-ssh-agent.socket` 单元处于活动状态，则无需手动配置 `SSH_AUTH_SOCK` 环境变量。当用户注销并重新登录时，`SSH_AUTH_SOCK` 环境变量的值会被设为 `$XDG_RUNTIME_DIR/gcr/ssh`。

### 使用

你可以运行

```
$ ssh-add -L
```

来列出正在运行的 agent 中已加载的 SSH 密钥。这有助于确认你启动了正确的服务，并正确设置了 `SSH_AUTH_SOCK`。

要把口令永久保存到钥匙环中，请使用 [seahorse](https://archlinux.org/packages/?name=seahorse)包 软件包提供的 ssh-askpass：

```
$ /usr/lib/seahorse/ssh-askpass my_key
```

> **提示：**在某些情况下（例如你既没有使用登录管理器，也没有使用桌面环境），另一种做法是使用 Seahorse 的 *secret-tool* [来存储口令](https://bbs.archlinux.org/viewtopic.php?pid=2217761#p2217761)。

要从其他目录手动添加 SSH 密钥：

```
$ ssh-add ~/.private/id_ed25519
Enter passphrase for ~/.private/id_ed25519:
```

> **注意：**你必须把对应的 *.pub* 文件放在与私钥相同的目录中（示例中为 `~/.ssh/id_ed25519.pub`）。另外，请确保公钥文件名为私钥文件名加上 *.pub*（例如 `my_key.pub`）。

要禁用所有手动添加的密钥：

```
$ ssh-add -D
```

### 禁用

如果你希望改用其他 SSH agent（例如直接使用 [ssh-agent](../../03_网络与服务/SSH/SSH%20密钥.md#ssh-agent)，或使用 [gpg-agent](../../03_网络与服务/邮件/GnuPG.md)），最好禁用 GNOME Keyring 的 ssh-agent 包装层。这样做并非严格必需，因为每个 agent 监听的套接字不同，可以用 `SSH_AUTH_SOCK` 在它们之间选择，但这能让排查问题更容易。请注意，GNOME 的实现不支持许多脚本特性，例如 BatchMode 。

要禁用 gcr-ssh-agent，请确保 `gcr-ssh-agent.socket` 和 `gcr-ssh-agent.service` 在 systemd 中都已禁用并停止。

## 提示与技巧

### 与应用程序集成

- [Chromium](../../06_软件与应用/Google/Chromium.md#强制用某一个密码存储)

### 锁定钥匙环

```
$ dbus-send --session --dest=org.freedesktop.secrets \
   --type=method_call  \
   /org/freedesktop/secrets \
   org.freedesktop.Secret.Service.Lock \
   array:objpath:/org/freedesktop/secrets/collection/login
```

该命令执行一次 [D-Bus](../../02_系统管理与内核/系统管理/D-Bus.md) 方法调用，以锁定 *login* 钥匙环。或者，如果你想使用图形界面，也可以用 Seahorse 锁定钥匙环。

### 清空口令

```
$ gnome-keyring-daemon -r -d
```

该命令会启动 gnome-keyring-daemon，并关闭此前已经在运行的实例。

### Git 集成

当你通过 HTTPS 推送时，GNOME keyring 与 [Git](../../07_开发与工具/版本控制系统/Git.md) 配合使用会很有用。[libsecret](https://archlinux.org/packages/?name=libsecret)包 软件包[需要安装](#安装)，该功能才可用。

配置 Git 使用 *libsecret* 助手：

```
$ git config --global credential.helper /usr/lib/git-core/git-credential-libsecret
```

下次运行 `git push` 时，如果你的钥匙环尚未解锁，系统会要求你解锁它。

### GnuPG 集成

有几个使用 GnuPG 的应用需要设置 `pinentry-program`。设置以下内容即可使用 GNOME 3 pinentry，让 GNOME Keyring 来管理口令提示。

```
~/.gnupg/gpg-agent.conf
```

```
pinentry-program /usr/bin/pinentry-gnome3
```

另一种做法是[强制 GPG 使用 loopback](../../03_网络与服务/邮件/GnuPG.md#Unattended_passphrase)，这样应该就能在应用内输入口令。

### 重命名钥匙环

钥匙环的显示名称（即 Seahorse 和 `file` 命令中显示的名称）可以通过\[更改未加密钥匙环文件中 display-name 的值来修改\]。钥匙环通常存放在 `~/.local/share/keyrings/` 中，扩展名为 *.keyring*。

### 用户密码变化时自动更改钥匙环密码

> **注意：**这只影响 *login* 钥匙环。

向 `/etc/pam.d/passwd` 中追加 `password optional pam_gnome_keyring.so`：

```
/etc/pam.d/passwd
```

```
...
password	optional	pam_gnome_keyring.so
```

### 在桌面环境（KDE、GNOME、XFCE……）之外使用 gnome-keyring-daemon

#### 启动

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 至少 [xinit](../Xorg%20命令/Xinit.md) 和 [SDDM](../显示管理器/SDDM.md) 都会执行 `/etc/X11/xinit/xinitrc.d/` 中的所有脚本，而 [sway](../Wayland%20合成器/Sway.md) 提供了 `/etc/sway/config.d/50-systemd-user.conf`，所以问题并不在于“位于桌面环境之外”。如果 gnome-keyring 需要 [XDG Autostart](../Freedesktop.org/XDG%20Autostart.md)，安装/配置章节里就应当说明这一点。（在 [arch:Talk:GNOME/Keyring#Launching gnome-keyring-daemon outside desktop environments (KDE,_GNOME,_XFCE,_...)](https://wiki.archlinux.org/title/Talk:GNOME/Keyring#Launching_gnome-keyring-daemon_outside_desktop_environments_.28KDE.2C_GNOME.2C_XFCE.2C_....29) 中讨论）

如果你使用 sway、i3，或任何不会执行

- `/etc/xdg/autostart/gnome-keyring-*.desktop`
- `/etc/X11/xinit/xinitrc.d/50-systemd-user.sh`

的窗口管理器，那么你的窗口管理器需要在启动时执行以下命令。这些命令不必按特定顺序执行。

```
dbus-update-activation-environment DISPLAY XAUTHORITY WAYLAND_DISPLAY
```

或者

```
dbus-update-activation-environment --all
```

该命令会把环境变量从窗口管理器传递给会话 dbus。若不执行，就无法通过 DBus 触发图形化的提示框。例如，Seahorse 的密码提示就依赖于此。

之所以需要这样做，是因为会话 dbus 在图形环境启动之前就已经启动，因此会话 dbus 并不知道你处在哪个图形环境中。必须有某个东西把描述图形环境的环境变量传给会话 dbus，让它了解当前的图形环境。

```
gnome-keyring-daemon --start --components=secrets
```

登录期间，PAM 会启动 `gnome-keyring-daemon --login`，它负责用登录密码让 gnome-keyring 保持解锁状态。如果 `gnome-keyring-daemon --login` 在几分钟内没有连接到会话 dbus，`gnome-keyring-daemon --login` 就会退出。如果在窗口管理器中针对会话 dbus 启动了 `gnome-keyring-daemon --start ...`，那么 `gnome-keyring-daemon --login` 就会连接到会话 dbus。如果你的登录会话没有在 `gnome-keyring-daemon --login` 退出前启动 `gnome-keyring-daemon --start ...`，你也可以在 `gnome-keyring-daemon --login` 退出之前，先运行任何使用 gnome-keyring 或 secret service API 的程序。

#### GNOME Keyring XDG 门户

GNOME Keyring 提供一个 XDG 门户后端（例如可供通过 [Flatpak](../../07_开发与工具/开发/Flatpak.md) 沙箱化的应用使用）。要使其在 GNOME 之外工作，必须配置为使用它。有几种做法。

##### 配置用户级的 Secrets 门户

避免修改系统文件的方法是，在 `$XDG_CONFIG_HOME/xdg-desktop-portal/$XDG_CURRENT_DESKTOP-portals.conf` 中把 `gnome-keyring` 配置为首选的 `Secrets` 门户。例如，要把 [sway](../Wayland%20合成器/Sway.md) 配置为使用 `gnome-keyring` 作为 `org.freedesktop.impl.portal.Secret` 的首选门户：

```
~/.config/xdg-desktop-portal/sway-portals.conf
```

```
[preferred]
default=wlr;gtk
org.freedesktop.impl.portal.Secret=gnome-keyring
```

##### 修改 gnome-keyring.portal

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 会修改 `/usr/share` 中的软件包文件，pacman 升级时会被覆盖（在 [Talk:GNOME/Keyring](https://wiki.archlinuxcn.org/wiki/Talk:GNOME/Keyring) 中讨论）

另一种方法是修改 `UseIn` 键，把你的桌面环境加入 `/usr/share/xdg-desktop-portal/portals/gnome-keyring.portal` 配置文件。例如，要加入 [sway](../Wayland%20合成器/Sway.md)：

```
/usr/share/xdg-desktop-portal/portals/gnome-keyring.portal
```

```
[portal]
DBusName=org.freedesktop.secrets
Interfaces=org.freedesktop.impl.portal.Secret
UseIn=gnome;sway
```

关于 XDG 桌面门户后端的更多信息，见 [XDG 桌面门户#后端](../Freedesktop.org/XDG%20桌面门户.md#后端)。

## 故障排除

### 密码没有被记住

如果你在登录后被要求输入密码，并且发现密码没有被保存，那么你可能需要创建/设置一个默认钥匙环。要使用 Seahorse（也称“密码和密钥”）完成此操作，见 GNOME 帮助中的 [创建新钥匙环](https://help.gnome.org/users/seahorse/stable/keyring-create.html.en) 和 [更改默认钥匙环](https://help.gnome.org/users/seahorse/stable/keyring-change-default.html.en)。

### 重置钥匙环

如果你收到以下错误信息：“The password you use to login to your computer no longer matches that of your login keyring”，你需要[更改登录钥匙环密码](#用图形界面管理)。

或者，你可以从 `~/.local/share/keyrings/` 中删除 `login.keyring` 和 `user.keystore` 文件。请注意，这会永久删除所有已保存的密钥。删除这些文件后，只需注销再重新登录即可。

### Unable to locate daemon control file

登录后，[日志](../../02_系统管理与内核/日志/Systemd_Journal.md)中可能会出现以下错误：

```
gkr-pam: unable to locate daemon control file
```

如果没有其他相关问题，这条消息“可以安全地忽略” 。

### No such secret collection at path: /

如果你尝试用 Seahorse 添加新钥匙环，可能会因以下原因收到此错误：

- `~/.local/share/keyrings/` 目录不存在。若缺失请创建它。
- 使用了自定义的 `~/.xinitrc`。添加以下一行可能可以解决 

```
~/.xinitrc
```

```
source /etc/X11/xinit/xinitrc.d/50-systemd-user.sh
```

### 终端提示 “discover_other_daemon: 1”

这是因为 *gnome-keyring-daemon* 被第二次启动了。既然该守护进程已经随附了 systemd 服务，就无需再以其他方式启动它。所以请确保从 `.zshenv`、`.bash_profile`、`.xinitrc`、`config.fish` 或类似文件中删除启动命令。或者，你也可以禁用 `gnome-keyring-daemon.service` 和 `gnome-keyring-daemon.socket` 这些[用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)。

### 钥匙环初始化不正确

这有几种症状：

- SSH 或 Git 之类的程序在等待钥匙环提供密码时挂起，最终超时并报出 “agent refused operation” 之类的错误。
- Seahorse 不显示任何钥匙环，而且手动创建一个名为 “login” 的钥匙环似乎不起作用。
- Seahorse 的输出中出现以下错误信息：

    ```
    couldn't load all secret collections: No such secret collection at path: /org/freedesktop/secrets/collection/login
    ```

要解决此问题，请执行以下操作：

1.  重启 `gnome-keyring-daemon.service` 这个 systemd [用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)。这应当能正确初始化 *login* 钥匙环。
2.  如果使用的话，重启 `gcr-ssh-agent.service` 这个 systemd [用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)。其他 agent 可能也需要重启。

如果这样仍无法解决问题，可以考虑[重置钥匙环](#重置钥匙环)。

### SSH agent 不可用

症状：

```
$ ssh-add -L
```

```
Error connecting to agent: Connection refused
```

请确保 `gcr-ssh-agent.socket` [用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)已被启用并启动，并且 `gnome-keyring-daemon` 的套接字和服务[用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)也已启用并启动：

> **提示：**你可以对 `{service,socket}` 使用 shell 的花括号展开。

### No Process Capabilities, Insecure Memory Might Get Used

如果看到以下错误 `gnome-keyring-daemon: no process capabilities, insecure memory might get used`，这意味着钥匙环可能会被保存到交换文件/分区中。这是一个安全问题，因为他人可能在没有适当授权的情况下读取并提取这些机密。解决方法是让内核不要这样做，但 `gnome-keyring-daemon` 需要具备相应的权限才行。

运行以下命令，赋予 `gnome-keyring-daemon` 适当的内存锁定权限：

```
# setcap cap_ipc_lock=+ep /usr/bin/gnome-keyring-daemon
```

## 参见

- [https://help.gnome.org/users/seahorse/stable/](https://help.gnome.org/users/seahorse/stable/)
- [GNOME Wiki 页面（已归档）](https://wiki.gnome.org/Projects/GnomeKeyring)
- [提供 org.freedesktop.secrets 的软件包](https://archlinux.org/packages/?q=org.freedesktop.secrets)

## 4.6　GNOME/提示与技巧

*难度：高级 ｜ 篇幅：23.8 KB ｜ 所属：GNOME*

将要讲到：键盘、磁盘、隐藏菜单中的应用程序、屏幕录像、截屏、注销延迟

## 键盘

### 登录时启用NumLock

请参阅[启动时打开数字锁定键#GNOME](../../04_硬件与外设/键盘/启动时打开数字锁定键.md#GNOME)

### 热键替代

许多热键可以通过 GNOME 设置进行更改。例如，要重新启用显示桌面快捷键：

**设置 > 键盘 > 自定义快捷键 > 导航 > 隐藏所有正常窗口**

然而，某些热键无法通过设置直接更改。为了更改这些热键，请使用 *dconf-editor* 或 *gsettings*。一个特别值得注意的例子是快捷键 `` Alt+` ``（在美国键盘布局中是Tab键上方的键）。在 GNOME Shell 中，它预配置为在应用程序的窗口之间循环切换，但它也是 [Emacs](../../07_开发与工具/Emacs/Emacs.md) 编辑器中常用的快捷键。可以通过使用上述工具修改 `org.gnome.desktop.wm.keybindings` 中的 *switch-group* 键来进行更改。

### XkbOptions 键盘选项

使用 **dconf-editor**, 导航至 `xkb-options` 键，位于 `org.gnome.desktop.input-sources` 模式下，并将所需的 XkbOptions（例如 *caps:swapescape*）添加到列表中。

有关所有 XkbOptions，请参阅 `/usr/share/X11/xkb/rules/xorg`，以及 `/usr/share/X11/xkb/symbols/*` 的相关描述。

### 取消绑定 Super 键

默认情况下，`Super` 键将打开 GNOME Shell 概览模式。你可以通过运行以下命令来取消绑定此键：

```
$ gsettings set org.gnome.mutter overlay-key ''
```

### 修改 Nautilus 热键

自 3.15 版本起，不再使用 **accel** 文件，但可以通过使用 [python-nautilus](https://archlinux.org/packages/?name=python-nautilus)包 来重新绑定热键。安装该软件包并添加以下文件：

```
~/.local/share/nautilus-python/extensions/modify_keybindings.py
```

```
import os, gi
gi.require_version('Nautilus', '3.0')
from gi.repository import GObject, Nautilus, Gtk, Gio, GLib

def rebind():
    app = Gtk.Application.get_default()
    # 在以下链接中查找 open_accels 和 nautilus_application_set_accelerators：
    #   https://github.com/GNOME/nautilus/blob/master/src/nautilus-files-view.c
    app.set_accels_for_action( "win.back", ["<alt>Left", "BackSpace"] )

    # 如果你想弄清楚哪个热键属于哪个操作，可以试试这个：
    # print(f'Alt+Left is: {app.get_actions_for_accel("<alt>Left")}')

class BackspaceBack(GObject.GObject, Nautilus.LocationWidgetProvider):
    def __init__(self):
        pass

    def get_widget(self, uri, window):
        rebind()
        return None
```

重启 Nautilus：

```
$ nautilus -q; nautilus
```

## 磁盘

GNOME 提供了一款磁盘工具，用于操作存储驱动器的设置。以下是一些功能：

- **启用写入缓存**是大多数硬盘提供的功能。数据会在选定的时间进行缓存和分配，以提高系统性能。你很可能已经默认启用了此功能（不是通过磁盘工具）。可以使用 `hdparm -W /dev/sdX` 检查。

: **设置 > 驱动器设置 > 写入缓存 > *启用***
: > **警告：**此性能提升可能会导致在突然断电的情况下丢失数据，请在启用写入缓存之前考虑这一点。

- **用户会话默认设置**启用时，可以使用 `/etc/fstab` 中的自定义选项，禁用时则会自动添加 GPT 驱动器和分区的默认和推荐挂载选项。

: **分区设置 > 编辑挂载选项 > 自动挂载选项 > *启用***
: > **警告：**此设置会删除相关的 [fstab](../../01_安装与包管理/启动过程/Fstab.md) 条目。

## 隐藏菜单中的应用程序

> **提示：**
>
> - 桌面条目可以通过编辑 *.desktop* 文件本身来隐藏。请参阅[桌面项#隐藏桌面项](../../01_安装与包管理/配置文件/桌面项.md#隐藏桌面项)。
> - [Menulibre](https://aur.archlinux.org/packages/Menulibre/)AUR 提供了一个无需 GNOME 依赖的菜单编辑器。

使用**主菜单**应用程序（由 [alacarte](https://archlinux.org/packages/?name=alacarte)包 软件包提供）隐藏你不想在菜单中显示的任何应用程序。

## 屏幕录像

GNOME 提供内建的屏幕录像功能，使用 `Ctrl+Shift+Alt+r` 快捷键组合。在顶部栏右侧的系统状态区旁会显示一个红色圆圈，表示正在录制。录制完成后，名为 `Screencast from %d%u-%c.webm` 的文件会保存在 `视频` 目录中。

> **注意：**录制的文件名可能会根据你的系统语言进行翻译。

为了使用屏幕录像功能，需安装一些 [gst-plugin 软件包](https://archlinux.org/packages/?q=gst-plugin)。例如，[screencast pipeline](https://gitlab.gnome.org/GNOME/gnome-shell/-/blob/main/js/dbusServices/screencast/screencastService.js#L26)（屏幕录像管道）依赖于 [vp8enc](https://gstreamer.freedesktop.org/documentation/vpx/vp8enc.html?gi-language=c#vp8enc-page) 和 [webmmux](https://gstreamer.freedesktop.org/documentation/matroska/webmmux.html?gi-language=c) 元素，来自 [gst-plugins-good](https://archlinux.org/packages/?name=gst-plugins-good)包。如果尝试录制时出现“pipewiresrc”模块缺失的错误，安装 [gst-plugin-pipewire](https://archlinux.org/packages/?name=gst-plugin-pipewire)包。

> **提示：**使用 [EasyScreenCast](https://github.com/EasyScreenCast/EasyScreenCast) 来便捷地“配置桌面录制的各种设置”（包括自定义上述的 [GStreamer](../../06_软件与应用/多媒体/GStreamer.md) 管道）。EasyScreenCast 也可以作为 [gnome-shell-extension-easyscreencast](https://aur.archlinux.org/packages/gnome-shell-extension-easyscreencast/)AUR 安装。

默认情况下，屏幕录像的最大时长为 30 秒。可以通过以下命令更改：

```
$ gsettings set org.gnome.settings-daemon.plugins.media-keys max-screencast-length length_in_seconds
```

将 `*length_in_seconds*` 设置为 `0` 以取消时长限制（根据 `max-screencast-length` 的描述）。

## 截屏

默认情况下，[gnome-screenshot](https://archlinux.org/packages/?name=gnome-screenshot)包 将图像保存到上次保存的目录，你可以查询该目录：

```
$ gsettings get org.gnome.gnome-screenshot last-save-directory
```

你可以设置一个自动保存目录，例如，自动将截图保存到 `*user*` 的桌面目录：

```
$ gsettings set org.gnome.gnome-screenshot auto-save-directory file:///home/user/Desktop
```

查看 [gnome-screenshot(1)](https://man.archlinux.org/man/gnome-screenshot.1) 手册页面了解更多选项。

## 注销延迟

要消除注销时的默认 60 秒延迟：

```
$ gsettings set org.gnome.SessionManager logout-prompt false
```

## 更改动画速度

GNOME shell 的动画速度可以通过“减速因子”来配置。大于 1.0 会减慢动画速度，介于 0.0 和 1.0 之间则加速动画。

### 设置减速因子

要暂时设置，打开 Looking Glass，按 **Alt-F2** 输入 `lg`，然后运行，例如，若要加速动画：

```
St.Settings.get().slow_down_factor = 0.4
```

或者使用 [gnome-shell-extension-impatience-git](https://aur.archlinux.org/packages/gnome-shell-extension-impatience-git/)AUR

可以通过环境变量 `GNOME_SHELL_SLOWDOWN_FACTOR` 永久设置减速因子，例如：

```
/etc/environment
```

```
GNOME_SHELL_SLOWDOWN_FACTOR=0.4
```

### 禁用动画

可以通过图形界面禁用动画，在**设置 > 辅助功能 > 视觉 > 减少动画**选项中切换。

## Retina (HiDPI) 显示支持

GNOME 在 3.10 版本中引入了 HiDPI 支持。如果显示器未通过 EDID 提供正确的屏幕尺寸，可能导致 UI 元素显示比例不正确。作为解决方法，你可以打开 *dconf-editor*，找到 `scaling-factor` 键，位于 `org.gnome.desktop.interface` 下。将其设置为 `1` 以获得标准比例。

更多信息请参阅 [HiDPI](../图形/HiDPI.md)。

## 密码和密钥（PGP 密钥）

你可以使用密码和密钥程序 [seahorse](https://archlinux.org/packages/?name=seahorse)包 创建 PGP 密钥，它是 [GnuPG](../../03_网络与服务/邮件/GnuPG.md) 的前端，并将其作为依赖项安装。未来可能会用到这个密钥（例如用于加密文件）。按照以下步骤创建密钥（此过程可能需要约 10 分钟）：

**文件 > 新建 > PGP 密钥 > 姓名 > 邮箱 > 默认设置 > 密码短语**。

## 终端

### 更改默认终端大小

可以在你的配置文件的首选项中调整新终端的默认大小。选择菜单中的*首选项*，然后在*配置文件*下选择你的配置文件，访问设置以更改初始终端大小。

### 新终端采用当前目录

新终端默认打开在 `$HOME` 目录。你可以通过将 `source /etc/profile.d/vte.sh` 添加到你的 shell 配置文件来配置终端以[采用当前工作目录](https://unix.stackexchange.com/questions/93476/gnome-terminal-keep-track-of-directory-in-new-tab)。

### 填充终端

要为终端添加填充（在窗口边缘和终端内容之间创建一个小的不可见边框），创建以下文件：

```
~/.config/gtk-3.0/gtk.css
```

```
vte-terminal,
terminal-window {
    padding: 10px 10px 10px 10px;
    -vte-terminal-inner-border: 10px 10px 10px 10px;
}
```

### 禁用闪烁光标

在 GNOME 3.8 及以上版本中禁用闪烁光标使用以下命令：

```
$ gsettings set org.gnome.desktop.interface cursor-blink false
```

只在终端中禁用闪烁光标使用：

```
$ gsettings set org.gnome.Terminal.Legacy.Profile:/org/gnome/terminal/legacy/profiles:/:$(gsettings get org.gnome.Terminal.ProfilesList default | tr -d \')/ cursor-blink-mode off
```

请注意，`gnome-settings-daemon` 必须正在运行，才能使这些和其他设置更改在 GNOME 应用程序中生效——参见 [GNOME#配置](GNOME.md#配置)。

### 禁用关闭终端时的确认窗口

当尝试关闭终端窗口并且已登录为 root 用户时，终端总是会显示确认窗口。要避免这种情况，请执行以下命令：

```
$ gsettings set org.gnome.Terminal.Legacy.Settings confirm-close false
```

### 颜色调色板

终端支持将其颜色调色板更改为你喜欢的样式。只需进入**首选项**，选择你的配置文件，然后编辑颜色调色板。

#### 终端配色方案

安装 [gogh-git](https://aur.archlinux.org/packages/gogh-git/)AUR 包，它提供了一套为 GNOME 终端制作的自定义配色方案。你可以在[选择一个或多个配色方案](https://gogh-co.github.io/Gogh/)后运行 `gogh` 并输入你选择的配色方案的编号。

> **警告：**安装配色方案时，切勿一次安装所有配色方案，因为它们会很难删除。请选择单个配色方案进行安装。

安装后，进入终端的**首选项**，点击**颜色**标签，选择你安装的配色方案名称。你会看到名称旁边有一个小箭头，点击它并选择**设置为默认**。

从这里开始，你可以进一步配置。如果某些颜色你不喜欢，可以轻松更改。

要删除一个配色方案，如果该配色方案是你的默认方案，首先将另一个方案设置为默认。然后选择该配色方案的名称并点击**删除**。

## 鼠标中键


默认情况下，GNOME 3 禁用中键鼠标按钮模拟，无论 [Xorg](../X%20服务器/Xorg.md) 设置（**Emulate3Buttons**）如何。要启用中键鼠标按钮模拟，请使用以下命令：

```
$ gsettings set org.gnome.settings-daemon.peripherals.mouse middle-button-enabled true
```

## 启用按钮和菜单图标

从 GTK 3.10 开始，GSettings 键“menus-have-icons”已被弃用。可以通过设置以下覆盖项来启用按钮和菜单中的图标：

```
$ gsettings set org.gnome.settings-daemon.plugins.xsettings overrides "{'Gtk/ButtonImages': <1>, 'Gtk/MenuImages': <1>}"
```

## 使用自定义颜色和渐变作为桌面背景

要使用自定义颜色和渐变作为桌面背景，你首先需要设置一个透明的图片或者一个不存在的图片作为桌面背景。例如，以下命令将设置一个不存在的图片作为背景：

```
$ gsettings set org.gnome.desktop.background picture-uri none
```

此时，桌面背景应为纯色——默认的颜色设置为深蓝色。

若要使用其他纯色，只需更改主色设置：

```
$ gsettings set org.gnome.desktop.background primary-color <my color>
```

其中 <my color> 是一个十六进制值（例如 *ffffff* 表示白色）。

对于渐变色，你还需要更改次色设置 `org.gnome.desktop.background secondary-color` 并选择一种着色类型。例如，如果你想要一个水平渐变，执行以下命令：

```
$ gsettings set org.gnome.desktop.background color-shading-type horizontal
```

如果你使用透明的图片作为背景，可以通过以下命令设置其不透明度：

```
$ gsettings set org.gnome.desktop.background picture-opacity <value>
```

其中 value 是一个 1 到 100 之间的数字（100 表示最大不透明度）。

## 背景过渡

GNOME 可以在特定时间间隔内在不同的壁纸之间过渡。可以通过创建一个 XML 文件，指定要使用的图片和时间间隔来实现。有关如何创建此类文件的更多信息，请参见以下[文章](https://www.linuxjournal.com/content/create-custom-transitioning-background-your-gnome-228-desktop)。

或者，有许多工具可以自动化此过程：

- **mkwlppr** — 该脚本创建 XML 文件，可以作为 GNOME 的动态壁纸，引用多个壁纸。

: [https://pastebin.com/019G2rCy](https://pastebin.com/019G2rCy)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2023-07-30 ⓘ\] || [mkwlppr](https://pastebin.com/019G2rCy)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2023-07-30 ⓘ\]

## 自定义 GNOME 会话

可以创建自定义的 GNOME 会话，使用 GNOME 会话管理器但启动不同的组件集（例如使用 [Openbox](../堆叠式窗口管理器/Openbox.md) 和 [tint2](../桌面环境/Tint2.md) 代替 GNOME Shell）。

自定义 GNOME 会话需要两个文件：一个会话文件，位于 `/usr/share/gnome-session/sessions/`，定义要启动的组件；一个[桌面项](../../01_安装与包管理/配置文件/桌面项.md)，位于 `/usr/share/xsessions`，由[显示管理器](../图形用户界面/显示管理器.md)读取。以下是一个会话文件的示例：

```
/usr/share/gnome-session/sessions/gnome-openbox.session
```

```
[GNOME Session]
Name=GNOME Openbox
RequiredComponents=openbox;tint2;gnome-settings-daemon;
```

以及一个桌面文件的示例：

```
/usr/share/xsessions/gnome-openbox.desktop
```

```
[Desktop Entry]
Name=GNOME Openbox
Exec=gnome-session --session=gnome-openbox
```

> **注意：**GNOME 会话会调用每个要启动组件的 *.desktop* 文件。如果你希望启动的组件没有提供 *.desktop* 文件，你必须在 `/usr/local/share/applications` 等目录中创建一个合适的[桌面项](../../01_安装与包管理/配置文件/桌面项.md)。

## 将特定网址重定向到特定的网页浏览器

这是如何使用 [Chromium](../../06_软件与应用/Google/Chromium.md) 来处理某些类型的网址，同时将 [Firefox](../../06_软件与应用/Mozilla/Firefox.md) 保持为所有其他任务的默认浏览器。

确保 [pcre](https://archlinux.org/packages/?name=pcre)包 已安装，以使用 *pcregrep*。

设置自定义 *xdg-open*：

```
/usr/local/bin/xdg-open
```

```
#!/bin/bash
DOMAIN_LIST_FILE=~/'domains.txt'
OTHER_BROWSER='/usr/bin/chromium-browser'
BROWSER_OPTIONS='' # Optional, for command line options passed to browser
XDG_OPEN='/usr/bin/xdg-open'
DEFAULT_BROWSER='/usr/bin/firefox'

if echo "$1" | pcregrep -q '^https?://'; then
    matching=0
    while read domain; do
	if echo "$1" | pcregrep -q "^https?://${domain}"; then
	    matching=1
	    break
	fi
    done < "$DOMAIN_LIST_FILE"

    if [[ $matching -eq 1 ]]; then
	"$OTHER_BROWSER" $BROWSER_OPTIONS ${*}
	exit 0
    fi

    "$DEFAULT_BROWSER" ${*}
    exit 0
else
    "$XDG_OPEN" ${*}
fi
```

配置域名以重定向到 *Chromium*：

```
$HOME/domains.txt
```

```
stackexchange.com
stackoverflow.com
superuser.com
www.youtube.com
github.com
```

将 *xdg-open web* 设置为桌面应用程序：

```
$HOME/.local/share/applications/xdg-open-web.desktop
```

```
[Desktop Entry]
Version=1.0
Name=xdg-open web
GenericName=Web Browser
Exec=xdg-open %u
Terminal=false
Type=Application
MimeType=text/html;text/xml;application/xhtml+xml;application/vnd.mozilla.xul+xml;text/mml;x-scheme-handler/http;x-scheme-handler/https;
StartupNotify=true
Categories=Network;WebBrowser;
Keywords=web;browser;internet;
Actions=new-window;new-private-window;
```

```
$ update-desktop-database $HOME/.local/share/applications/
```

在 GNOME 设置中将 *xdg-open web* 设置为默认 Web 应用程序：转到 **GNOME 设置 > 默认应用程序**并将 **Web** 设置为 *xdg-open web*。

## 从视频缩略图中移除电影孔/胶片条纹效果（Nautilus）

自 Gnome 3.12 起，Nautilus（文件管理器）在视频缩略图上叠加了电影孔/胶片条纹效果。要移除或覆盖此效果，可以使用环境变量 `G_RESOURCE_OVERLAYS` 引用已编译资源的路径（在此实例中为 `filmholes.png`），并指定相关叠加层的路径。此环境变量自 GLib 2.50 起可用，早于此版本将无效。

从 Nautilus 中提取 `filmholes.png`：

```
$ gresource extract /usr/bin/nautilus /org/gnome/nautilus/icons/filmholes.png > filmholes.png
```

使用您喜欢的编辑器编辑 `filmholes.png`，移除图像中的胶片效果，保留透明度和尺寸不变，然后覆盖提取的图像。

将提取的图像复制或移动到所需位置，例如 `/usr/share/icons/`，并编辑 `~/.profile`，添加以下导出，并根据需要将 `/usr/share/icons/` 更改为您放置文件的位置：

```
export G_RESOURCE_OVERLAYS=/org/gnome/nautilus/icons/filmholes.png=/usr/share/icons/filmholes.png
```

如果已经安装了 [ffmpegthumbnailer](https://archlinux.org/packages/?name=ffmpegthumbnailer)包 作为其他文件管理器的依赖项，该文件管理器可能会生成缩略图，则应修改 `/usr/share/thumbnailers/ffmpegthumbnailer.thumbnailer` 中的 `Exec` 行，移除 `-f` 标志。

为确保没有已嵌入胶片效果的缩略图，删除缩略图缓存：

```
$ rm -r ~/.cache/thumbnails
```

注销并重新登录到您的会话，您应该不再在 Nautilus 中看到视频缩略图上的胶片孔/胶片条纹效果。

## GNOME 软件与 pacman 集成

[packagekit](https://archlinux.org/packages/?name=packagekit)包 集成功能以前通过名为 *gnome-software-packagekit-plugin* 的软件包提供，但已被[自愿禁用](https://gitlab.archlinux.org/archlinux/packaging/packages/gnome-software/-/commit/df4431b3707e05832e49b9b9c3164ac53e0e3a6f)，并且被[认为不再受支持](https://github.com/archlinux/archinstall/issues/1321#issuecomment-1151343223)。

## 增加音量至超过 100%

您可以通过运行以下命令来允许音量过度放大：

```
$ gsettings set org.gnome.desktop.sound allow-volume-above-100-percent true
```

或者，安装扩展 [Volume Mixer](https://extensions.gnome.org/extension/858/volume-mixer/)。然后，使用鼠标在顶部面板的音量图标上滚动，以增加超过 100% 的音量。

或者，打开 GNOME 调整工具并切换**常规 > 过度放大**。

## 按更小步进调整音量

默认情况下，按键盘的音量键会将音量调整 6%。如果需要更小的步进，可以在按音量键时按住 `Shift`，此时音量将以 2% 的步进调整。

此外，从 GNOME 3.36 开始，现在可以通过 dconf 设置直接调整音量步进。例如，要将音量步进设置为 2%，请执行以下命令：

```
$ gsettings set org.gnome.settings-daemon.plugins.media-keys volume-step 2
```

## 在顶部面板图标旁显示音量百分比

安装扩展 [Sound percentage](https://github.com/maoschanz/sound-percentage-gs-extension)，以在顶部面板的音量图标旁边显示当前输出音量级别。

## 启动时使用独立 GPU

安装 [switcheroo-control](https://archlinux.org/packages/?name=switcheroo-control)包 或 [switcheroo-control-git](https://aur.archlinux.org/packages/switcheroo-control-git/)AUR 并启动/启用 `switcheroo-control.service`。

## 窗口列表

如果您喜欢在底部显示任务列表，但不喜欢此扩展的默认黑色，可以首先复制其目录：

```
$ cp -r /usr/share/gnome-shell/extensions/window-list@gnome-shell-extensions.gcampax.github.com/ ~/.local/share/gnome-shell/extensions/
```

然后按您的喜好编辑 CSS。例如，要使窗口列表透明，请将 `stylesheet.css` 编辑如下：

```
.bottom-panel {
  background: transparent;
}
...
.window-button > StWidget,
.window-picker-toggle > StWidget {
  color: #bbb;
  background-color: transparent;
  ...
```

## 导航

要模仿 Windows 切换窗口的行为，首先禁用默认设置，该设置将窗口切换限制为当前工作区内的窗口：

```
$ gsettings set org.gnome.shell.app-switcher current-workspace-only false
```

然后，将 `Alt+Tab` 和 `Alt+Shift+Tab` 绑定为在窗口之间切换，而不是在应用程序之间切换：

```
$ gsettings set org.gnome.desktop.wm.keybindings switch-windows "['<Alt>Tab']"
$ gsettings set org.gnome.desktop.wm.keybindings switch-windows-backward "['<Shift><Alt>Tab']"
```

此外，您可以重新绑定应用程序之间的切换（此示例使用 `Super` 而不是默认的 `Alt`）：

```
$ gsettings set org.gnome.desktop.wm.keybindings switch-applications "['<Super>Tab']"
$ gsettings set org.gnome.desktop.wm.keybindings switch-applications-backward "['<Shift><Super>Tab']"
```

## 图像查看器

安装 [libheif](https://archlinux.org/packages/?name=libheif)包 以添加对 HEIC 图像文件格式的支持。图像查看器使用的 `gdk-pixbuf2` 库将 `libheif` 列为可选依赖项。

## 将 dconf（gsettings）数据库存储为文本文件

默认情况下，dconf 将其配置存储在位于 `$XDG_CONFIG_HOME/dconf/user` 的二进制数据库 blob 中。如果您的主目录存储在 NFS 中，您将 dotfiles 存储在版本控制中，或出于其他原因，可以通过配置 *dconf 配置文件*来覆盖此默认设置。有关创建和使用配置文件的详细信息，请参见 [dconf(7) § PROFILES](https://man.archlinux.org/man/dconf.7#PROFILES)。

在更改系统范围的默认设置之前，先将每个用户现有的 dconf 数据库转储为名为 `user.txt` 的文本文件。似乎无法使用其他扩展名。假设 `XDG_CONFIG_HOME` 的默认值，可以使用以下命令完成此操作：

```
$ dconf dump / > ~/.config/dconf/user.txt
```

完成后，以 root 用户身份创建默认 dconf 配置文件。

```
/etc/dconf/profile/user
```

```
service-db:keyfile/user
```

注销并重新登录，确认更改 dconf 设置时会更改基于文本的 `user.txt` 文件，而不是删除二进制 `user` 文件之前的旧二进制数据库。

此设置应该只会带来一些最小的额外资源使用。Dconf 仍然在临时 `XDG_RUNTIME_DIR` 目录中使用二进制数据库，但必须在桌面启动时重新创建它。它还必须保持 `user.txt` 文件的最新状态，并监视文本文件的更改。

## 4.7　GDM

*难度：高级 ｜ 篇幅：26.8 KB ｜ 所属：GNOME*

将要讲到：安装、启动、配置、疑难解答、另见

来自[GDM - GNOME显示管理器](https://wiki.gnome.org/Projects/GDM)：“GNOME显示管理器（GDM）是一个管理图形显示服务并处理图形用户登录的程序。

[显示管理器](../图形用户界面/显示管理器.md)为[X Window System](../X%20服务器/Xorg.md)和[Wayland](../Wayland/Wayland.md)提供图形登录提示。

## 安装

可通过安装[gdm](https://archlinux.org/packages/?name=gdm)包来安装GDM，或作为[gnome](https://archlinux.org/groups/x86_64/gnome/)包组的一部分安装。

## 启动

若要开机启动GDM，[启用](../../01_安装与包管理/初始化/Systemd.md#使用单元)`gdm.service`。

### 自动启动应用

欲使程序于登录后自动启动，请参照 [Autostarting#On desktop environment startup](../../02_系统管理与内核/系统管理/自动启动.md#On_desktop_environment_startup) 中适用您的桌面环境的指示。

## 配置

> **注意：**下面列出的大多数配置选项都可以使用 [gdm-settings](https://aur.archlinux.org/packages/gdm-settings/)AUR GUI 应用程序轻松设置。

### 登录页面背景图片

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 配置不是持久的，更改在 GNOME Shell 更新后会被撤销。建议：重写以使用“用户主题”GNOME 扩展。（在 [Talk:GDM](https://wiki.archlinuxcn.org/wiki/Talk:GDM) 中讨论）

> **注意：**
>
> - 自GNOME 3.16开始，GNOME Shell主题被存储为二进制文件（gresource）。
> - 在 [gnome-shell](https://archlinux.org/packages/?name=gnome-shell)包 的后续升级中，这个变动会被重写。

首先，您需要将现有的 GNOME Shell 主题解压到您的主目录中的一个目录中。 您可以使用以下脚本执行此操作：

```
extractgst.sh
```

```
#!/bin/sh
gst=/usr/share/gnome-shell/gnome-shell-theme.gresource
workdir=${HOME}/shell-theme

for r in `gresource list $gst`; do
	r=${r#\/org\/gnome\/shell/}
	if [ ! -d $workdir/${r%/*} ]; then
	  mkdir -p $workdir/${r%/*}
	fi
done

for r in `gresource list $gst`; do
        gresource extract $gst $r >$workdir/${r#\/org\/gnome\/shell/}
done
```

主题文件应当已提取到创建的目录中。现在将您想要的背景图像复制到此目录。

接下来，您需要在目录中创建一个文件，内容如下：

```
gnome-shell-theme.gresource.xml
```

```
<?xml version="1.0" encoding="UTF-8"?>
<gresources>
  <gresource prefix="/org/gnome/shell/theme">
    <file>calendar-today.svg</file>
    <file>calendar-today-light.svg</file>
    <file>checkbox.svg</file>
    <file>checkbox-focused.svg</file>
    <file>checkbox-off-focused-light.svg</file>
    <file>checkbox-off-focused.svg</file>
    <file>checkbox-off-light.svg</file>
    <file>checkbox-off.svg</file>
    <file>gnome-shell.css</file>
    <file>gnome-shell-high-contrast.css</file>
    <file>gnome-shell-start.svg</file>
    <file>pad-osd.css</file>
    <file>process-working.svg</file>
    <file>toggle-off.svg</file>
    <file>toggle-off-hc.svg</file>
    <file>toggle-off-light.svg</file>
    <file>toggle-on.svg</file>
    <file>toggle-on-hc.svg</file>
    <file>toggle-on-light.svg</file>
    <file>workspace-placeholder.svg</file>
    <file>filename</file>
  </gresource>
</gresources>
```

将 **filename** 替换为背景图像的文件名，或移除此行后用16位色彩值替代。

现在，打开 `gnome-shell.css` 文件并更改 `#lockDialogGroup` 定义如下：

```
#lockDialogGroup {
  background: url(filename);
  background-size: widthpx heightpx;
  background-repeat: no-repeat;
}
```

将 `background-size` 设置为GDM使用的分辨率, 这可能不一定是图像的分辨率。显示分辨率列表见[Display resolution](https://en.wikipedia.org/wiki/Display_resolution#Computer_monitors)。同样，将 **filename** 替换为背景图像的文件名。

如果您仅想改变背景颜色，请调整 `#lockDialogGroup` 的定义，如下：

```
#lockDialogGroup {
  background-color: #color;
}
```

这里 **color** 是作为背景的十六进制编码的新颜色。

最后，使用以下命令编译主题：

```
$ glib-compile-resources gnome-shell-theme.gresource.xml
```

然后将生成的 `gnome-shell-theme.gresource` 文件复制到 `/usr/share/gnome-shell` 目录下。

然后重启 `gdm.service` (注意：重启而不是退出) ，它就会使用您想要的背景图片了。

更多信息见[此贴](https://bbs.archlinux.org/viewtopic.php?id=197036)。一个自动执行以上步骤的 shell 脚本在 [DimaZirix's github repository](https://github.com/DimaZirix/fedora-gdm-wallpaper) 上可用。

### dconf配置

一些 GDM 设置存储在 dconf 数据库中。可以通过将 *keyfiles* 添加到 `/etc/dconf/db/gdm.d` 目录，然后以root身份运行 `dconf update` 重新编译GDM数据库来配置它们，或者通过登录系统上的GDM用户并使用"gsettings" 命令直接更改设置。需要一个 GDM 配置文件 - 这必须手动创建，因为它不再向上游发送，见下文：

```
/etc/dconf/profile/gdm
```

```
user-db:user
system-db:gdm
file-db:/usr/share/gdm/greeter-dconf-defaults
```

对于后一种方法，您可以用以下命令尝试登入 GDM 用户：

```
# machinectl shell gdm@ /bin/bash
```

#### 登录页面的logo

创建以下密钥文件：

```
/etc/dconf/db/gdm.d/02-logo
```

```
[org/gnome/login-screen]
logo='/path/to/logo.png'
```

然后重新编译 GDM 数据库。

或者，作为 GDM 用户执行以下操作来更改 logo：

```
[gdm]$ dbus-launch gsettings set org.gnome.login-screen logo '/path/to/logo.png'
```

要禁用 logo，可以将值设置为空字符串：

```
[gdm]$ dbus-launch gsettings set org.gnome.login-screen logo ''
```

#### 更改光标主题

GDM会忽略 [GNOME](GNOME.md) 光标主题设置，也忽略根据 [XDG_规范](../X%20服务器/光标主题.md#XDG_规范)设置的光标主题。若要更改GDM使用的光标，请创建以下密钥文件：

```
/etc/dconf/db/gdm.d/10-cursor-settings
```

```
[org/gnome/desktop/interface]
cursor-theme='theme-name'
```

然后重新编译GDM database，或作为GDM用户执行以下操作：

```
[gdm]$ dbus-launch gsettings set org.gnome.desktop.interface cursor-theme 'theme-name'
```

#### 更改图标主题

可以使用相同的方法来更改图标主题。 创建以下密钥文件：

```
/etc/dconf/db/gdm.d/11-icon-settings
```

```
[org/gnome/desktop/interface]
icon-theme='theme-name'
```

然后，重新编译 GDM 数据库。 或者，执行以下命令临时作为 GDM 用户更改图标主题：

```
[gdm]$ dbus-launch gsettings set org.gnome.desktop.interface icon-theme 'theme-name'
```

#### 在登录页面显示大字体

单击屏幕右上角的辅助功能图标（一个白色圆圈，中间有一个人的轮廓），然后选中*大号文本*选项。

要设置特定的缩放比例，可以创建以下密钥文件（以1.25为例）：

```
/etc/dconf/db/gdm.d/03-scaling
```

```
[org/gnome/desktop/interface]
text-scaling-factor='1.25'
```

然后重新编译GDM database，或作为GDM用户执行以下操作：

```
[gdm]$ dbus-launch gsettings set org.gnome.desktop.interface text-scaling-factor '1.25'
```

#### 关闭声音

此调整禁用在登录屏幕上（通过键盘）调整系统音量时听到的声音反馈。

创建以下密钥文件：

```
/etc/dconf/db/gdm.d/04-sound
```

```
[org/gnome/desktop/sound]
event-sounds=false
```

然后重新编译GDM database，或作为GDM用户执行以下操作：

```
[gdm]$ dbus-launch gsettings set org.gnome.desktop.sound event-sounds 'false'
```

#### 更改电源按钮行为

> **注意：**
>
> - 对于电源键的设置 [logind 设置](../../02_系统管理与内核/电源管理/电源管理.md#ACPI_事件) 已被 GNOME Settings Daemon 覆盖。 
> - 自从 GDM 3.18起，电源键不能被设置为交互式 (*interactive*). 
> - 在某些情况下，这条设置会被忽略，而采用硬编码产生的的默认值。

> **警告：**请注意， [acpid](../../02_系统管理与内核/电源管理/Acpid.md) 守护进程也会控制“电源键”和“休眠键”相关事件。同时运行两套系统可能导致意外的结果。

创建如下的 keyfile:

```
/etc/dconf/db/gdm.d/05-power
```

```
[org/gnome/settings-daemon/plugins/power]
power-button-action='action'
```

然后重新编译GDM database，或作为GDM用户并执行以下操作：

```
[gdm]$ dbus-launch gsettings set org.gnome.settings-daemon.plugins.power power-button-action 'action'
```

这里 *action* 可以是 `nothing`, `suspend` 或 `hibernate` 其中之一。

#### 开启触摸板轻触以点击

GDM（和GNOME）中默认关闭轻触以点击，但可以使用dconf设置开启它。

> **注意：**如果想要在X下这么做，必须先正确配置X服务器的访问权限，见 [#配置X服务器的访问权限](#配置X服务器的访问权限)。

若要启用轻触以点击，创建以下keyfile：

```
/etc/dconf/db/gdm.d/06-tap-to-click
```

```
[org/gnome/desktop/peripherals/touchpad]
tap-to-click=true
```

然后重新编译GDM database，或作为GDM用户执行以下操作：

```
[gdm]$ dbus-launch gsettings set org.gnome.desktop.peripherals.touchpad tap-to-click 'true'
```

#### 开启或关闭无障碍菜单


若要禁用或启用无障碍菜单，创建以下 keyfile：

```
/etc/dconf/db/gdm.d/07-accessibility
```

```
[org/gnome/desktop/interface]
toolkit-accessibility='boolean'
```

然后重新编译GDM database，或作为GDM用户执行以下操作：

```
[gdm]$ dbus-launch gsettings set org.gnome.desktop.interface toolkit-accessibility 'boolean'
```

当key的值为`false`时，无障碍菜单默认关闭；`true`则开启。

#### 在 GDM 上启用夜间模式

欲启用[夜间模式](GNOME.md#夜间模式)，运行

```
[gdm]$ dbus-launch gsettings set org.gnome.settings-daemon.plugins.color night-light-enabled true
```

### 键盘布局

GDM 需要 `/etc/vconsole.conf` 中设置 `XKBLAYOUT` 参数；如果没有设置，它将默认使用标准的 `us`（qwerty）布局，即不会遵循 `KEYMAP` 中设置的值。

一种普遍适用的方法是使用 `localectl --no-convert set-x11-keymap [keymap]`：详情参见 Keyboard configuration in Xorg#Setting keyboard layout。

> **警告：**使用 `localectl set-x11-keymap` 而不带 `--no-convert` 也会改变 `KEYMAP=` 的值，如果使用包含重音字符的密码短语加密整个系统，可能会导致系统无法启动。参见 [systemd issue #34967](https://github.com/systemd/systemd/issues/34967)。

### 更改语言

系统语言将应用于 GDM。如果系统有多个用户，则可以为 GDM 设置与系统语言不同的语言。在这种情况下，首先请确保安装了 [gnome-control-center](https://archlinux.org/packages/?name=gnome-control-center)包。然后，启动 *gnome-control-center* 并选择“区域和语言”。在标题栏中，选中*登录屏幕*切换按钮。最后，单击*语言*并从列表中选择您的语言。系统将提示您输入 root 密码。请注意，除非系统上存在多个用户，否则标题栏中的“登录屏幕”按钮将不可见 。

> **提示：**添加 2 种不同的输入语言后，注销。然后选择您的默认语言，GDM 将在第二个选项被删除后记住您的选择。

### 用户与登录

#### 自动登录

> **警告：**不要尝试对由 [systemd-homed](../../08_安全与加密/安全/用户和用户组.md#用户组管理) 管理的用户执行此操作。 这是目前 [未解决](https://github.com/systemd/systemd/issues/20844) 的漏洞，并且会使 GDM 崩溃。

将以下内容添加至`/etc/gdm/custom.conf`以开启自动登录（将*username*替换为您的用户名）：

```
/etc/gdm/custom.conf
```

```
# Enable automatic login for user
[daemon]
AutomaticLogin=username
AutomaticLoginEnable=True
```

> **提示：**如果在添加这些行后 GDM 失败，请从 TTY 中将它们注释掉。

或延迟自动登录：

```
/etc/gdm/custom.conf
```

```
[daemon]
TimedLoginEnable=true
TimedLogin=username
TimedLoginDelay=1
```

您可以设置用于自动登录的会话（将 `gnome-xorg` 替换为所需的会话）：

```
/var/lib/AccountsService/users/username
```

```
XSession=gnome-xorg
```

#### 免密登录

如果您想绕过 GDM 中的密码提示，只需在 `/etc/pam.d/gdm-password` 的第一行添加以下行：

```
auth sufficient pam_succeed_if.so user ingroup nopasswdlogin
```

然后，将 `nopasswdlogin` 组加入您的系统。详见[用户组](../../08_安全与加密/安全/用户和用户组.md#用户组管理)以获得组的描述和组管理相关命令 。

现在，把您的用户加入 `nopasswdlogin` 组，然后您只需点击您的用户名以登录。

> **警告：**
>
> - **不要**对**root**账户这么做。
> - 您将无法在使用 GDM 登录时更改会话类型。 如果您想更改默认会话类型，您首先需要从 `nopasswdlogin` 组中删除您的用户。

#### 禁用指纹登录

当使用[指纹](../../08_安全与加密/指纹识别/Fprint.md)登录时，它不会解锁密钥环，因此您仍然会被提示输入密钥环密码。您可能希望禁用登录并保留指纹以解锁会话。为此，只需为 GDM 用户禁用指纹。

临时以 GDM 用户身份执行以下命令并更改此设置：

```
[gdm]$ dbus-launch gsettings set org.gnome.login-screen enable-fingerprint-authentication false
```

#### 多个会话的无密码关闭

GDM 使用 polkit 和 logind 来获得关机权限。 当多个用户登录时，您可以通过设置以关闭系统：

```
/etc/polkit-1/localauthority.conf.d/org.freedesktop.logind.policy
```

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE policyconfig PUBLIC
 "-//freedesktop//DTD PolicyKit Policy Configuration 1.0//EN"
 "http://www.freedesktop.org/software/polkit/policyconfig-1.dtd">

<!--
Policy definitions for logind
 -->

<policyconfig>

  <action id="org.freedesktop.login1.power-off-multiple-sessions">
    <description>Shutdown the system when multiple users are logged in</description>
    <message>System policy prevents shutting down the system when other users are logged in</message>
    <defaults>
      <allow_inactive>yes</allow_inactive>
      <allow_active>yes</allow_active>
    </defaults>
  </action>

</policyconfig>
```

您可以找到所有可用的 logind 选项（比如，reboot-multiple-sessions，即重启多用户会话）在 [org.freedesktop.login1(5)](https://man.archlinux.org/man/org.freedesktop.login1.5) 中。

#### 在GDM中开启root登录

我们并不建议您以 root 登录，但如果必要，请编辑 `/etc/pam.d/gdm-password` 并在行 `auth required pam_deny.so` 前添加以下行：

```
/etc/pam.d/gdm-password
```

```
auth            sufficient      pam_succeed_if.so uid eq 0 quiet
```

这个文件应当看起来如下：

```
/etc/pam.d/gdm-password
```

```
...
auth            sufficient      pam_succeed_if.so uid eq 0 quiet
auth            sufficient      pam_succeed_if.so uid >= 1000 quiet
auth            required        pam_deny.so
...
```

重启 GDM 后，您可以用 root 身份登录。

#### 在登录列表中隐藏用户

gdm 用户列表的用户由 \[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2022-09-17 ⓘ\] 收集。 它会自动隐藏系统用户（UID < 1000）。 要从登录列表中隐藏普通用户，请创建或编辑一个以用户命名的文件，并将其保存在 `/var/lib/AccountsService/users/` 中，随之隐藏该文件。文件至少应包含：

```
/var/lib/AccountsService/users/username
```

```
[User]
SystemAccount=true
```

#### 通过 RDP 的远程登录

> **注意：**当前只能创建无显示器的远程会话。

> **注意：**一次只能存在一个图形化会话。若另一个图形化会话存在，尝试创建新图形化会话会杀死它。

##### 通过图形化接口

可在 Gnome 设置程序中找到“系统>远程桌面>远程登录”来图形化地配置远程登录。

##### 通过终端接口

> **注意：**要生效，Gnome 远程桌面同时要求 TLS 证书和凭证。除非预先设置过，否则两者都不存在，需要手动设置。

要显示当前状态和凭证，可采用以下命令：

```
# grdctl --system status --show-credentials
```

要设置凭证：

```
# grdctl --system rdp set-credentials rdp_login rdp_password
```

要生成新的 TLS 密钥和证书：

```
# winpr-makecert3 -rdp -path /etc/gnome-remote-desktop -n rdp-tls
```

若省略了 `-n rdp-tls` 部分，就会使用 *hostname* 作为名字。

要设置新的 TLS 密钥和证书：

```
# grdctl --system rdp set-tls-key /etc/gnome-remote-desktop/rdp-tls.key
# grdctl --system rdp set-tls-cert /etc/gnome-remote-desktop/rdp-tls.crt
```

最终，启用远程登录：

```
# grdctl --system rdp enable
```

### 设置默认显示器

一些[桌面环境](../桌面环境/桌面环境.md)将显示设置保存在 `~/.config/monitors.xml`。*xrandr* 命令基于该文件内容生成信息。在GDM中类似内容保存在`/var/lib/gdm/.config/monitors.xml`。

若有显示器设置（例如朝向，缩放，主屏幕等等）信息记录于`~/.config/monitors.xml`中，并且想要让这些设置用于GDM：

```
# cp ~/.config/monitors.xml /var/lib/gdm/.config/
```

要在每次启动时自动重新配置显示器设置，为 `gdm.service` 使用 [Systemd#附加配置片段](../../01_安装与包管理/初始化/Systemd.md#附加配置片段)：

```
/etc/systemd/system/gdm.service.d/override.conf
```

```
[Service]
ExecStartPre=/bin/cp /home/user/.config/monitors.xml /var/lib/gdm/.config/monitors.xml
```

`monitors.xml`文件中屏幕旋转和缩放的部分内容如下：

```
<monitors version="2">
  <configuration>
    <logicalmonitor>
      ...
      <scale>2</scale>
      ...
      <transform>
        <rotation>right</rotation>
        <flipped>no</flipped>
      </transform>
      ...
    </logicalmonitor>
  </configuration>
</monitors>
```

必须登出才能使更改生效，因为GDM不遵守`xorg.conf`。

> **注意：**
>
> - 如果在Wayland下使用GDM，则必须使用在Wayland下创建的`monitors.xml`。详见[GDM bug 224](https://gitlab.gnome.org/GNOME/gdm/issues/224)。也可以强制让GDM [#使用Xorg后端](#使用Xorg后端)，并使用在Xorg下创建的`monitors.xml`。
> - 如果您使用 [fractional scaling (分数缩放)](../图形/HiDPI.md#Fractional_scaling)，您需要为用户 `gdm` 启用它：
>
> ```
> [gdm]$ dbus-launch gsettings set org.gnome.mutter experimental-features "['scale-monitor-framebuffer']"
> ```

### 配置X服务器的访问权限

您可以使用 `xhost` 命令来配置 X 服务器的访问权限。

例如，要授予 GDM 访问 X 服务器的权限，请使用以下命令：

```
# xhost +SI:localuser:gdm
```

## 疑难解答

### Wayland 与 NVIDIA 专有驱动

要在 GDM 中使用带有 [NVIDIA](../图形/NVIDIA.md) 驱动程序的 Wayland，您必须满足以下三个条件：

- 启用 [DRM KMS](../图形/NVIDIA.md#DRM_内核级显示模式设置)；

- [配置 Wayland](../Wayland/Wayland.md#系统需求)；

- 遵循 NVIDIA/Tips and tricks#Preserve video memory after suspend。

从 GDM 42 和 NVIDIA 驱动程序 510 开始，GDM 默认为 Wayland。 对于较旧的 NVIDIA 驱动程序（版本 470 和 510 之间），GDM 具有依赖于芯片组的 [udev 规则](https://gitlab.gnome.org/GNOME/gdm/-/blob/main/data/61-gdm.rules.in)使用 Xorg 而不是 Wayland。 要强制启用 Wayland，**不**遵循上述三个常规步骤，请通过创建以下符号链接来覆盖这些规则：

```
# ln -s /dev/null /etc/udev/rules.d/61-gdm.rules
```

> **注意：**此命令之所以有效，是因为 `/etc/udev/rules.d/` 中的规则会覆盖 `/usr/lib/udev/rules.d/` 中的规则（参见 [Udev#About udev rules](../../04_硬件与外设/硬件检测和故障排除/Udev.md#About_udev_rules)）。 建议修改 `/etc/` 中的文件，而非 `/usr/` 中的文件，也是因为 `/etc/` 中的文件由 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 管理。

如果出现黑屏而不是 GDM，请尝试在计算机的 BIOS 设置中禁用集成显卡。

一些情况下，GNOME 无法启动，将控制权转回 GDM，最终导致登录页面重复出现。您可以尝试设置以下环境变量，参考[BBS#2126478](https://bbs.archlinux.org/viewtopic.php?pid=2126478)：

```
/etc/environment
```

```
MUTTER_DEBUG_KMS_THREAD_TYPE=user
```

### 注销失败

如果 GDM 在启动时正常启动，但在多次尝试注销后失败，请尝试将此行添加到 `/etc/gdm/custom.conf` 的守护程序部分：

```
GdmXserverTimeout=60
```

### 无 root 的 Xorg

见[Xorg#没有 root 权限的 Xorg](../X%20服务器/Xorg.md#没有_root_权限的_Xorg)。

### 使用Xorg后端

默认使用 [Wayland](../Wayland/Wayland.md) 后端，只有在 Wayland 后端无法启动时才使用 [Xorg](../X%20服务器/Xorg.md) 后端。在以下情况下，您可能希望使用 Xorg 后端：

- GDM [崩溃](https://bbs.archlinux.org/viewtopic.php?pid=1869534#p1869534)

要默认使用 Xorg 后端，请在 `/etc/gdm/custom.conf` 中将以下行取消注释：

```
#WaylandEnable=false
```

### 没有完全卸载 GDM

删除 [gdm](https://archlinux.org/packages/?name=gdm)包 后，[systemd](../../01_安装与包管理/初始化/Systemd.md) 可能会报告以下内容：

```
user 'gdm': directory '/var/lib/gdm' does not exist
```

要删除此警告，请以 root 身份登录并[删除私有用户](../../08_安全与加密/安全/用户和用户组.md#其他用户管理示例) `gdm` 然后[删除组](../../08_安全与加密/安全/用户和用户组.md#用户组管理) `gdm`：

验证 `gdm` 是否已通过具有 root 权限的 `pwck` 和 `grpck` 成功删除。 解决这个问题 ，您可能需要仔细检查[无主文件](../../01_安装与包管理/包管理器/Pacman_提示和技巧.md#Identify_files_not_owned_by_any_package)是否保留 *gdm*。

### GDM自动挂起（GNOME 3.28）

GDM 使用单独的 dconf 数据库来控制电源管理。要应用用户的电源设置，请将它们复制到 GDM 的 dconf 数据库：

```
$ IFS=$'\n'; for x in $(sudo -u username gsettings list-recursively org.gnome.settings-daemon.plugins.power); do eval "sudo -u gdm dbus-launch gsettings set $x"; done; unset IFS
```

这里 `*username*` 是您的用户名。

欲仅禁用 AC 上的自动挂起，运行：

```
[gdm]$ dbus-launch gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'nothing'
```

（要同时禁用电池自动挂起，请使用 `battery` 而不是 `ac` 运行命令。）

重新启动 GDM 以激活您的更改。

### GDM 忽略了 Wayland，默认使用 X.Org

Wayland 需要运行内核模式设置 (KMS) 才能工作，并且在某些机器上 GDM 进程比 KMS 更早启动，导致 GDM 无法看到 Wayland 并且只能使用 X.Org。 这可能会导致您的日志中显示如下消息：

```
gnome-shell: Failed to open gpu '/dev/dri/card0': GDBus.Error:org.freedesktop.DBus.Error.AccessDenied: Operation not permitted
gnome-shell: Failed to create backend: No GPUs found
systemd: org.gnome.Shell@wayland.service: Failed with result 'protocol'.
systemd: Failed to start GNOME Shell on Wayland.
```

或者，同样的问题可能会导致 GDM 不出现或监视器仅显示 TTY 输出。

您可以通过[尽早启动 KMS（缺失中文翻译）](../../02_系统管理与内核/内核/内核级显示模式设置.md#Early_KMS_start)来解决这个问题。您可能还希望仅验证在 GDM 配置中启用了 Wayland,（[见上文](#使用Xorg后端)）。

此外，如果您使用 [NVIDIA](../图形/NVIDIA.md) 驱动程序，Wayland 会话可能会被 [udev](../../04_硬件与外设/硬件检测和故障排除/Udev.md) 规则阻止（请参阅 [GNOME#Wayland 会话](GNOME.md#Wayland_会话)下的注释）。 这可能会导致如下消息：

```
systemd: Condition check resulted in GNOME Shell on Wayland being skipped.
systemd: org.gnome.Shell@wayland.service: Skipped due to 'exec-condition'.
systemd: org.gnome.Shell@wayland.service: Control process exited, code=exited, status=2/INVALIDARGUMENT
```

请参阅 [#Wayland与NVIDIA专有驱动](#Wayland与NVIDIA专有驱动)作为解决方法。

### 当存在 NVIDIA (e)GPU 时，AMD 或 Intel GPU 上出现黑屏

起初，在没有 NVIDIA 设备的情况下，GDM 可以在 Wayland 上正常启动和工作。但一旦插入 NVIDIA eGPU（或由于其他原因加载 `nvidia` 模块），GDM 就会停止工作。该问题的一个典型表现是在注销和 GDM 重新启动时出现黑屏，并在 GDM 的日志中显示以下消息（通过以 root 身份运行 `journalctl -u gdm -b` 访问）：

```
Gdm: Child process -<some PID> was already dead.
```

解决方法同[上所描述](#GDM_忽略了_Wayland，默认使用_X.Org): 防止 `/usr/lib/gdm-disable-wayland` 在 `nvidia` 模块加载时运行。

请注意，一旦 `/usr/lib/gdm-disable-wayland` 运行，Wayland 上的 GDM 将不再工作。这是因为 `WaylandEnable=false` 已写入 `/run/gdm/custom.conf`，它会覆盖 `/etc/gdm/custom.conf`。要在不重新启动系统的情况下解决此问题，请删除 `/run/gdm/custom.conf`，然后重新启动 GDM。

### 无法启用 GDM

参见 [systemd/FAQ#Failure to enable unit due to preexisting symlink](../../01_安装与包管理/初始化/Systemd_FAQ.md#Failure_to_enable_unit_due_to_preexisting_symlink)。

## 另见

- [GDM 参考手册](https://help.gnome.org/admin/gdm/stable/index.html.en)

## 4.8　GNOME

*难度：高级 ｜ 篇幅：30.6 KB ｜ 所属：GNOME*

将要讲到：安装、运行 GNOME、导航、遗留名称、配置、参见

[GNOME](https://www.gnome.org/)（读音为/(ɡ)noʊm/）是一个追求简单易用的[桌面环境](../桌面环境/桌面环境.md)。它由 [GNOME项目](https://zh.wikipedia.org/wiki/GNOME%E8%A8%88%E5%8A%83)设计，并且完全由自由开源的软件组成。GNOME默认使用 [Wayland](../Wayland/Wayland.md) 作为显示服务器，并同时提供多个对话可供选择：

- **GNOME**是在[Wayland](../Wayland/Wayland.md)上运行GNOME Shell的默认选项。传统的X应用使用Xwayland运行。
- **GNOME Classic**通过[特定的扩展和数值](https://web.archive.org/web/20190503163814/http://www.worldofgnome.org/welcome-to-gnome-3-8-flintstones-mode/)，提供了“[传统的桌面体验](https://help.gnome.org/users/gnome-help/stable/gnome-classic.html)”（类似GNOME2的界面）。因此，它是GNOME Shell的定制样式，而非真正的不同模式。

## 安装

有两个软件组可用：

- [gnome](https://archlinux.org/groups/x86_64/gnome/)包组 包含基本的桌面环境和一些集成良好的[应用](https://wiki.gnome.org/Apps)
- [gnome-circle](https://archlinux.org/groups/x86_64/gnome-circle/)包组 包含多种[格外应用](https://apps.gnome.org/#circle)，极大的拓展了Gnome生态。
- [gnome-extra](https://archlinux.org/groups/x86_64/gnome-extra/)包组 包含部分[开发工具](https://apps.gnome.org/#development)，以及其他适合Gnome的应用与游戏。

GNOME 的基础桌面环境由 [Mutter](https://zh.wikipedia.org/wiki/Mutter_(software)) 窗口管理器的插件 [GNOME Shell](https://zh.wikipedia.org/wiki/GNOME_Shell) 组成。可以用 [gnome-shell](https://archlinux.org/packages/?name=gnome-shell)包 单独安装它。

> **注意：***mutter* 是桌面的混成器。它利用硬件图形加速减少屏幕的混乱。GNOME 会话管理器会自动检测显卡驱动是否能够运行 GNOME Shell，如果不行则用 *llvmpipe* 软件渲染。

也可以使用不稳定版本，见[官方软件仓库#gnome-unstable](../../00_关于与社区/关于%20Arch/官方仓库.md#gnome-unstable)。

## 运行 GNOME

GNOME 可以使用[显示管理器](../图形用户界面/显示管理器.md)以图形方式启动，也可以从控制台手动启动（可能会缺少某些功能）。[gnome](https://archlinux.org/groups/x86_64/gnome/)包组的显示管理器是[GDM](GDM.md)。

> **注意：**GDM提供了GNOME的锁屏功能（及其它）支持。如果没有使用GDM启动GNOME，则需要使用其它屏幕锁定器。见[List of applications/Security#Screen lockers](../../06_软件与应用/应用程序/应用程序列表_安全.md#Screen_lockers)。

### 图形界面启动

如果安装了[gnome](https://archlinux.org/groups/x86_64/gnome/)包组并希望GNOME在下次启动时自动启动, 请启用 `gdm.service`，随后可以在显示管理器的会话菜单中选择想要使用的会话：*GNOME*、*GNOME Classic* （仅在[gnome-shell-extensions](https://archlinux.org/packages/?name=gnome-shell-extensions)包 已安装时显示）。

如果想立即启动 GNOME 以避免重启，可以从一个没有被图形占用的 tty 上启动 `gdm.service`。

### 手动启动

> **注意：**仍然需要X服务器用于运行尚未移植到[Wayland](../Wayland/Wayland.md)的应用，详见[Wayland#XWayland](../Wayland/Wayland.md#XWayland)。使用Qt等特定图形库的应用，可以通过强制设置环境变量使用Wayland。详见[Wayland#GUI libraries](../Wayland/Wayland.md#GUI_libraries)。

#### 会话类型

Gnome会话继承了systemd的会话类型。当会话开始时，systemd的会话类型在会话启动时由`XDG_SESSION_TYPE`环境变量决定，并在启动后只能由该会话上的控制器决定。详见 [Github](https://github.com/systemd/systemd/issues/14489)。

因此仅仅在登录后设置`XDG_SESSION_TYPE` 并不起作用。因此应创建一个systemd drop-in 文件给getty设置环境变量：

```
/etc/systemd/system/getty@tty1.service.d/wayland.conf
```

```
[Service]
Environment=XDG_SESSION_TYPE=wayland
```

重新加载后检查会话类型：

```
$ loginctl session-status
```

#### 启动会话

在正确设置会话类型后，可以通过以下命令手动启动会话：

```
$ gnome-session
```

由于直接运行`gnome-shell --wayland`缺乏会话管理，所以并不推荐。

请注意，手动调用Gnome**不需要**`gdm`（因此也不需要附带的`gdm.service`），因此对于安装了Gnome最小安装的用户来说，可以根据个人喜好，选择一些包含在更广泛的`gnome`组中的软件包。

若要在 tty1 登录时启动，将以下内容添加到 `.bash_profile` 中：

```
gnome-session --no-reexec
```

Firefox和Qt应用不遵守`XDG_SESSION_TYPE`，所以最好给它们加上变量：

```
if -z $DISPLAY && $(tty) == /dev/tty1 && $XDG_SESSION_TYPE == wayland ; then
  MOZ_ENABLE_WAYLAND=1 QT_QPA_PLATFORM=wayland exec gnome-session --no-reexec
fi
```

### Wayland中的GNOME应用

在使用 *GNOME* 会话时，GNOME 应用将使用 Wayland 运行。出于调试需要, [https://docs.gtk.org/gtk3/running.html](https://docs.gtk.org/gtk3/running.html) 和 [https://docs.gtk.org/gtk4/running.html](https://docs.gtk.org/gtk4/running.html) 列出的选项和环境变量。

## 导航

[GNOME Shell cheat sheet](https://wiki.gnome.org/Projects/GnomeShell/CheatSheet) 中解释了如何高效地使用 GNOME shell，它展示了 GNOME shell 的特色和快捷键，包括切换任务，使用键盘，窗口控制，面板，概览模式等等。以下是部分常用的快捷键：

- `Super+m`：显示消息列表
- `Super+a`：显示应用菜单
- `Alt+Tab`：切换当前使用的应用
- `` Alt+` `` （美式键盘`Tab`上面的按键）：切换正在前台使用的应用的窗口
- `Alt+F2`，然后输入 `r` 或 `restart`：在图形界面出问题时重启界面（仅用于X/传统模式，不适用于Wayland模式）。

如需改变默认配置，使其更类似Windows，见 [GNOME/提示与技巧#导航](GNOME_提示与技巧.md#导航)。

更多快捷键见[键盘导航](https://help.gnome.org/users/gnome-help/stable/keyboard-nav.html)。

## 遗留名称

> **注意：**一些GNOME应用在文档和对话框中的名称已经更改，但执行文件名称却没有。下面表格列出了一些这样的应用。

> **提示：**在搜索栏中搜索应用的遗留名称将成功找到对应的应用，例如搜索 *nautilus* 会出现 *文件*。

| 当前 | 遗留 |
| --- | --- |
| [文件](GNOME_文件.md) | Nautilus |
| [Web](GNOME_Web.md) | Epiphany |
| 视频 | Totem |
| 主菜单 | Alacarte |
| 文档查看器 | Evince |
| 磁盘使用情况分析器 | Baobab |
| 图像查看器 | EoG (Eye of GNOME) |
| [密码和密钥](GNOME_Keyring.md) | Seahorse |
| 翻译编辑器 | Gtranslator |

## 配置

GNOME系统设置面板（*gnome-control-center*）和GNOME应用使用[dconf](https://en.wikipedia.org/wiki/Dconf)配置系统存储设置。

您可以使用[gsettings(1)](https://man.archlinux.org/man/gsettings.1)命令行工具直接访问 dconf 数据库。这也可以让您修改用户界面不公开的设置。命令行工具 [dconf(1)](https://man.archlinux.org/man/dconf.1) 可以直接修改底层数据库，跳过验证过程。

直到GNOME 3.24，设置由GNOME设置进程应用（位于`/usr/lib/gnome-settings-daemon/gnome-settings-daemon`），其也可以在GNOME会话之外运行。

然而GNOME 3.24用几个相互独立的设置插件（`/usr/lib/gnome-settings-daemon/gsd-*`，后来移动到`/usr/lib/gsd-*`）取代了 GNOME 设置进程。这些插件通过 `/etc/xdg/autostart` (匹配org.gnome.SettingsDaemon.\*.desktop) 下的桌面文件进行控制。若要在 GNOME 会话之外运行这些插件，您需要复制或编辑相应的[桌面条目](../../01_安装与包管理/配置文件/桌面项.md)到 `~/.config/autostart`。

配置通常是用户特定的，本文将不介绍如何为多个用户创建配置模板。

### GNOME 系统设置

#### 色彩

`colord` 守护进程会读取显示器的 EDID 信息并提取出合适的色彩配置内容。大多数情况下，色彩配置都是正确的，不需要额外设置；但是对于某些偏差情况或使用较旧的显示器时，可以把色彩配置文件放在 `~/.local/share/icc/` 下并被指向。

#### 夜间模式

GNOME 内置了类似于 [Redshift](../X%20服务器/Redshift.md) 的蓝光过滤功能。夜间模式可以在设置面板中启动及自定义启动时间。此外，夜间模式的色温可以使用以下[dconf](https://archlinux.org/packages/?name=dconf)包设置进行调整，5000是一个示例值：

```
$ gsettings set org.gnome.settings-daemon.plugins.color night-light-temperature 5000
```

> **提示：**若要在Wayland中调整白天的色温，请安装[Night Light Slider扩展](https://extensions.gnome.org/extension/1276/night-light-slider/)。

> **注意：**在NVIDIA显卡上，夜间颜色只工作在版本545.29.02及之后的驱动上

#### 日期与时间

如果系统已有配置好的[网络时间协议 守护进程](../../03_网络与服务/NTP/Network%20Time%20Protocol%20daemon.md)，它同样会对 GNOME 起作用。如果需要，同步设置可以在菜单内设为手动控制。

GNOME支持自动选择时区，可在系统设置里的*日期和时间*选项中开启。前提是已开启定位服务 (见设置中的*隐私*选项）。

> **注意：**由于Mozilla定位服务的退役，自动选择时区可能不再工作。见 . 解决方案见 

若要在顶栏内显示日期，请运行：

```
$ gsettings set org.gnome.desktop.interface clock-show-date true
```

另外，若要在顶栏的日历中显示周数，请运行：

```
$ gsettings set org.gnome.shell.calendar show-weekdate true
```

#### 默认应用程序

首次安装 GNOME 时，您可能会发现某些格式由错误的应用处理，比如视频被*totem*打开而不是以前使用的 [VLC](../../03_网络与服务/串流/VLC%20媒体播放器.md)。某些格式关联可以使用系统设置中的*默认应用程序*进行调整。

有关其它协议和方法，请参阅[默认应用程序](../../02_系统管理与内核/系统管理/默认应用程序.md)进行配置。

#### 鼠标和触摸板

大多数触摸板设置可以使用系统设置中的*鼠标和触摸板*进行调整。

根据您的设备，其它配置可能可用，但不会显示在默认界面内，例如不同的触摸板点击方法：

```
$ gsettings range org.gnome.desktop.peripherals.touchpad click-method
```

```
enum
'default'
'none'
'areas'
'fingers'
```

手动设置:

```
$ gsettings set org.gnome.desktop.peripherals.touchpad click-method 'fingers'
```

或使用[gnome-tweaks](https://archlinux.org/packages/?name=gnome-tweaks)包设置。

> **注意：**GNOME 不支持 [synaptics](../../04_硬件与外设/输入设备/Touchpad%20Synaptics.md) 并默认使用 [libinput](../../04_硬件与外设/输入设备/Libinput.md)。参考 [这个缺陷报告](https://bugzilla.gnome.org/show_bug.cgi?id=764257#c12)。

##### 使用鼠标更改窗口大小

默认情况下，您可以使用鼠标移动窗口，方法是按住 Super，单击并按住鼠标左键并拖动鼠标。

此外，您可以通过按住 Super、单击并按住鼠标右键并拖动鼠标来启用鼠标调整窗口大小：

```
$ gsettings set org.gnome.desktop.wm.preferences resize-with-right-button true
```

如果您不喜欢 Super 键，还可以将修饰键更改为其他键，例如 Alt 或 Ctrl：

```
$ gsettings set org.gnome.desktop.wm.preferences mouse-button-modifier "'<Alt>'"
```

#### 网络

[NetworkManager](../../06_软件与应用/红帽/NetworkManager.md) 是GNOME项目中控制网络设置的工具。如果尚未安装，则安装[networkmanager](https://archlinux.org/packages/?name=networkmanager)包软件包并启用`NetworkManager.service` 。

虽然可以使用任何其它[网络管理器](../../03_网络与服务/网络/网络配置.md)，但 NetworkManager 可以使用整合到桌面环境的网络设置和状态指示器（ [network-manager-applet](https://archlinux.org/packages/?name=network-manager-applet)包，不必要安装 ）。

> **注意：**用[networkmanager](https://archlinux.org/packages/?name=networkmanager)包的*nmtui*设置隐藏的无线网络不会自动连接。您需要在GNOME设置中创建一个新配置文件，以恢复该网络的自动连接功能。

#### 在线帐户

部分在线账户，比如 ownCloud，需要安装 [gvfs-goa](https://archlinux.org/packages/?name=gvfs-goa)包 和[gvfs-dnssd](https://archlinux.org/packages/?name=gvfs-dnssd)包以在 GNOME 应用比如 [GNOME 文件](GNOME_文件.md)以及 GNOME 文档中发挥全部功能。

详见[在线账户](https://help.gnome.org/users/gnome-help/stable/accounts.html)。

#### 搜索

GNOME shell在按下`Super`键并开始输入时会启动搜索。[localsearch](https://archlinux.org/packages/?name=localsearch)包作为[gnome](https://archlinux.org/groups/x86_64/gnome/)包组中[nautilus](https://archlinux.org/packages/?name=nautilus)包的依赖被默认安装。它提供一个应用和数据的索引数据库。它可以被设置中“搜索”菜单项配置。它在用户登录时自动被gnome-session启动。

localsearch 并不自动递归用户的家目录中所有的目录,所以你可能需要在 搜索 > 搜索位置 中添加自定义的路径。创建一个名为 `.nomedia` 的空文件以将某个目录从索引中排除。

可通过 `localsearch status` 查看当前索引状态。搜索内容可以在命令行中被`localsearch searc``h`查看，被`localsearch tag`编辑，以及被重置。见 `localsearch help` 或在线寻求参考。

tinysparql-sql被用作索引数据库。如果需要，数据库可以被直接查询。

#### 设备安全

GNOME 43 在设置中添加了一个新的设备安全面板。该功能依赖[fwupd](../../04_硬件与外设/硬件/Fwupd.md)工作。见 

### 高级设置

如上文所述，改变[GTK](../../07_开发与工具/控件工具集/GTK.md)主题或[窗口管理器](../窗口管理器/窗口管理器.md)主题等选项，并不会出现在GNOME设置（gnome-control-center）里。想要修改这些设置可以使用GNOME Tweaks （[gnome-tweaks](https://archlinux.org/packages/?name=gnome-tweaks)包），这是一个展示了许多这类设置的图形化工具。

（存储在 DConf 数据库中的）GNOME设置也可以使用[dconf-editor(1)](https://man.archlinux.org/man/dconf-editor.1)（一个图形化的DConf配置工具）或[gsettings](https://developer.gnome.org/gio/stable/GSettings.html)命令行工具进行配置。GNOME Tweaks 只能用于改变GUI。注意，你不一定总能看到下文描述的所有设置。

#### 拓展

拓展的目录位于 [https://extensions.gnome.org](https://extensions.gnome.org)。拓展可通过 [archlinux官方仓库](https://archlinux.org/packages/?q=gnome-shell-extension) (少数), [AUR](https://aur.archlinux.org/packages?K=gnome-shell-extension)或者[网页](https://extensions.gnome.org)安装。

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 下方的“注意”推荐手动管理用户文件，因为更“简单”，但并没有解释为什么。（在 [Talk:GNOME](https://wiki.archlinuxcn.org/wiki/Talk:GNOME) 中讨论）

> **注意：**\* 通过浏览器安装拓展仅对当前用户生效，并需要用户手动管理升级。这是比较简单的方法。
>
> - -   此外，如果你想要从浏览器安装拓展，你需要安装 [gnome-browser-connector](https://archlinux.org/packages/?name=gnome-browser-connector)包。如果从官方仓库或者AUR安装，则不需要。
> - 从AUR（或者官方仓库）安装拓展是他们对整个系统生效（并且在使用 [AUR 助手](../../06_软件与应用/软件比较/AUR%20助手.md)时自动更新）。

已安装的拓展可以通过 *gnome-extensions-app*在图形界面、在命令行使用[gnome-extensions(1)](https://man.archlinux.org/man/gnome-extensions.1)或在浏览器中通过网页配置、启用或禁用。在浏览器中，如需安装和激活拓展，请点击右上角的ON，然后点击弹出窗口的Install（如果该拓展并没有被安装）。已经安装的拓展可在 [https://extensions.gnome.org/local/](https://extensions.gnome.org/local/)查看并检查更新。

[gnome-shell-extensions](https://archlinux.org/packages/?name=gnome-shell-extensions)包 提供了一些GNOME官方维护的非常有用的拓展。

[extension-manager](https://archlinux.org/packages/?name=extension-manager)包 是一个用于在操作系统或用户范围内安装、拆卸、启用、禁用拓展的图形工具。安装前请仔细确认[已知问题列表](https://github.com/mjakeman/extension-manager/labels/bug)。

启用一个拓展（默认禁用）：

```
$ gsettings set org.gnome.shell disable-user-extensions false
```

列出当前启用的拓展：

```
$ gsettings get org.gnome.shell enabled-extensions
```

上述命令可能会列出已删除的扩展。要仅列出已启用和安装的扩展，请改用 gnome-extensions：

```
$ gnome-extensions list --enabled
```

关于 GNOME shell拓展的更多信息，详见 [https://extensions.gnome.org/about/](https://extensions.gnome.org/about/).

#### 外观

##### 主题

> **注意：**从[Gnome 42](https://release.gnome.org/42/)开始，许多默认的Gnome应用程序使用GTK 4和libadwaita。这些应用程序目前不支持通过gsettings或[gnome-tweaks](https://archlinux.org/packages/?name=gnome-tweaks)包改变主题，只能通过系统设置的外观选项来调整配置。若要设置Adwaita或Adwaita-dark以外的GTK主题，见[GTK#Themes](../../07_开发与工具/控件工具集/GTK.md#Themes)。

GNOME默认使用Adwaita。若要只将Adwaita-dark应用于GTK 2应用，请使用以下符号链接：

```
$ ln -s /usr/share/themes/Adwaita-dark ~/.themes/Adwaita
```

> **注意：**Adwaita-dark主题由[gnome-themes-extra](https://archlinux.org/packages/?name=gnome-themes-extra)包提供，因此GNOME的最小安装可能不会包含此主题。

若要选择新的主题，（将它们移到适当的目录中，并）使用GNOME Tweaks或下面的GSettings命令。

对于GTK主题：

```
$ gsettings set org.gnome.desktop.interface gtk-theme theme-name
```

对于图标主题：

```
$ gsettings set org.gnome.desktop.interface icon-theme theme-name
```

> **注意：**窗口管理器的主题会跟随GTK主题。使用`org.gnome.desktop.wm.preferences theme`的方法已被废弃和忽略。

见[GTK#Themes](../../07_开发与工具/控件工具集/GTK.md#Themes)和Icons#Manually.

###### 标题栏按钮排序

设置 GNOME 窗口管理器顺序 (Mutter, Metacity):

```
$ gsettings set org.gnome.desktop.wm.preferences button-layout ':minimize,maximize,close'
```

> **提示：**冒号表示窗口标题栏按钮出现的方向

##### GNOME Shell主题

GNOME Shell本身的主题是可配置的。首先确认您已安装[gnome-shell-extensions](https://archlinux.org/packages/?name=gnome-shell-extensions)包软件包以应用Shell主题。然后通过GNOME Tweaks或通过[GNOME Shell Extensions](https://extensions.gnome.org) 网站启用“User Themes”扩展。Shel主题可以通过使用GNOME Tweaks软件加载并选用。

[AUR](https://aur.archlinux.org/packages?O=0&K=gnome-shell-theme&do_Search=Go&PP=50&SB=v&SO=d)里有大量可用的GNOME Shell主题。Shell主题也可在[gnome-look.org](https://gnome-look.org/)里下载。

##### AppIndicators/顶部菜单栏图标

AppIndicators 对于监控和控制后台程序很有用，其对应的软件包是 [gnome-shell-extension-appindicator](https://archlinux.org/packages/?name=gnome-shell-extension-appindicator)包 or [gnome-shell-extension-appindicator-git](https://aur.archlinux.org/packages/gnome-shell-extension-appindicator-git/)AUR。安装后重启 [GNOME Shell](#Navigation) ，然后在 GNOME 扩展程序中启用 AppIndicator 扩展，或者运行 `$ gnome-extensions enable $(gnome-extensions list | grep -m 1 appindicatorsupport)`来以命令行方式启用它。

##### Shell动画速度

GNOME shell 动画可以被提速、降速或禁用。见[GNOME/Tips and tricks#Change animation speed](GNOME_提示与技巧.md#Change_animation_speed).

##### Shell 模糊效果

Blur my Shell 是一个给概览模式页面、Shell本身和其他部分应用添加模效果的拓展。安装 [gnome-shell-extension-blur-my-shell](https://aur.archlinux.org/packages/gnome-shell-extension-blur-my-shell/)AUR ，或者[gnome-shell-extension-blur-my-shell-git](https://aur.archlinux.org/packages/gnome-shell-extension-blur-my-shell-git/)AUR 。该拓展具有高度可自定义性，并且你可以自由选择对哪些应用施加模糊效果。

##### 更好的 Alt-Tab

GNOME中默认的 Alt-Tab功能非常简单，并不展示selected windows的概览。你可以在设置应用中将 Alt-Tab快捷键从 “切换应用” 改为 “切换窗口”以展示窗口概览。

你也可以使用Coverflow Alt-Tab。这是一个拓展Alt-Tab行为的拓展，添加了一些特性使在应用程序之间切换更简单、更好看。 安装[gnome-shell-extension-coverflow-alt-tab-git](https://aur.archlinux.org/packages/gnome-shell-extension-coverflow-alt-tab-git/)AUR。你可以根据喜好配置该拓展。

注意：`` Super-` ``默认提供“切换窗口”功能。

#### 自启动

GNOME提供[XDG Autostart](../Freedesktop.org/XDG%20Autostart.md)。

[gnome-tweaks](https://archlinux.org/packages/?name=gnome-tweaks)包 程序可以用来管理自启动项。

> **提示：**如果Tweaks中自启动应用选项下加号按钮为灰色不可用，尝试在终端下通过`gnome-tweaks`命令启动Tweaks。见[此贴](https://bbs.archlinux.org/viewtopic.php?pid=1413631#p1413631)。

#### 桌面

##### Dash to Dock

To move the dash out of the overview and turn it into a dock to以便于启动和切换应用, 安装 [gnome-shell-extension-dash-to-dock](https://aur.archlinux.org/packages/gnome-shell-extension-dash-to-dock/)AUR.

##### 启动到概览模式

从GNOME 40开始，桌面会直接启动到概览模式，而不是桌面（如以前的版本）。若要模仿经典的行为，可以安装[No overview at start-up](https://extensions.gnome.org/extension/4099/no-overview/)插件。

如果你在使用[gnome-shell-extension-dash-to-dock](https://aur.archlinux.org/packages/gnome-shell-extension-dash-to-dock/)AUR，你也可以在gsettings中禁用该行为。

```
$ gsettings set org.gnome.shell.extensions.dash-to-dock disable-overview-on-startup true
```

详见[此讨论](https://discourse.gnome.org/t/gnome-40-login-is-to-the-activities-overview-mode-how-do-you-disable-this/5783)。

#### 剪贴板历史

不像其他桌面环境，GNOME 并没有内建的管理剪贴板历史的工具。你可以使用拓展。安装 [gnome-shell-extension-clipboard-indicator](https://aur.archlinux.org/packages/gnome-shell-extension-clipboard-indicator/)AUR。

#### 天气

以展示选定地点的天气，比如状态、风速、气压等信息，请安装 [gnome-shell-extension-openweather](https://aur.archlinux.org/packages/gnome-shell-extension-openweather/)AUR。天气信息实时更新。

#### 音频输入输出选择

> [![](https://wiki.archlinuxcn.org/wzh/images/6/62/Tango-edit-cut.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-cut.png)**这一章节正在考虑移除。**
>
> **原因:** 该软件目前最高只支持到Gnome 43。 (在 [Talk:GNOME](https://wiki.archlinuxcn.org/wiki/Talk:GNOME) 讨论)

默认情况下，如果你想要选择音频输入输出设备或改变麦克风的音量，你需要打开GNOME控制中心进行配置。你可以安装[gnome-shell-extension-sound-output-device-chooser](https://aur.archlinux.org/packages/gnome-shell-extension-sound-output-device-chooser/)AUR 或者 [gnome-shell-extension-sound-output-device-chooser-git](https://aur.archlinux.org/packages/gnome-shell-extension-sound-output-device-chooser-git/)AUR以更方便地控制他们。你可以在安装后进行更多的配置。

#### 字体

> **提示：**如果您把"Scaling factor"调至1.00以上的某值，辅助功能菜单将自动启用

GNOME可以设置窗体标题，界面（应用），文档及等宽字体。查看Tweaks下的字体选项卡以获得相关选项。

对于字体渲染来说，RGBA可能适合更多的显示器类型，如果字体看起来过分拥挤，可以将字体渲染调至“Slight”或“None”。

#### 输入法

GNOME集成了的通过[IBus](../输入法/IBus.md)的输入法, 只有[ibus](https://archlinux.org/packages/?name=ibus)包和添加想要的输入法引擎 (例如:[ibus-libpinyin](https://archlinux.org/packages/?name=ibus-libpinyin)包 for Intelligent Pinyin) 需要安装，安装后，输入法引擎可以加入GNOME的区域和语言设置键盘布局。

#### 非主流键盘布局

如果您使用的是像Neo2这样使用多层/修饰键的替代键盘布局，您可能需要转到 GNOME。设置 (*gnome-control-center*)中的 *Keyboard > Type Special Characters* 将 *Alternate Characters Key* 从 *右 Alt* 改为其他键，以便它可以作为键盘布局的本地修饰键使用。将其设置为 *Left Alt* 会阻止 *Alt+Tab* 键盘快捷键，因此请小心您所更改的内容。 如果没有进行这个更改，您的左 *Mod3* 键可能会起作用，但右侧的一个 (*AltGr*) 键可能不会起作用。（截至2021-05-18）

#### 电源

当您使用笔记本时，可能想修改以下设置，包括控制闲置、按下电源按钮和盖子关闭时的行为。

```
$ gsettings set org.gnome.settings-daemon.plugins.power button-power hibernate
$ gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-timeout 3600
$ gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type hibernate
$ gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-battery-timeout 1800
$ gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-battery-type hibernate
$ gsettings set org.gnome.desktop.lockdown disable-lock-screen true
```

如需在合上盖子后依然保持显示器开启：

```
$ gsettings set org.gnome.settings-daemon.plugins.xrandr default-monitors-setup do-nothing
```

GNOME 3.24中不建议使用以下设置：

```
org.gnome.settings-daemon.plugins.power button-hibernate
org.gnome.settings-daemon.plugins.power button-power
org.gnome.settings-daemon.plugins.power button-sleep
org.gnome.settings-daemon.plugins.power button-suspend
org.gnome.settings-daemon.plugins.power critical-battery-action
```

##### 笔记本合盖时不挂起

The settings panel of GNOME does not provide an option for the user to change the action triggered when the laptop lid is closed. To change the lid switch action system-wide, edit the systemd settings in `/etc/systemd/logind.conf`. To turn off suspend on lid close, set `HandleLidSwitch=ignore`, as described in [Power management#ACPI events](../../02_系统管理与内核/电源管理/电源管理.md#ACPI_events).

##### 修改电池电量严重不足时的行为

设置面板不提供对电池电量严重不足行为的设置。这些设置也从dconf中移除。不过它们现在由upower管理。按需编辑`/etc/UPower/Upower.conf`中upower设置。

```
/etc/UPower/UPower.conf
```

```
PercentageLow=10
PercentageCritical=3
PercentageAction=2
CriticalPowerAction=HybridSleep
```

##### 电源模式

安装 [gnome-control-center](https://archlinux.org/packages/?name=gnome-control-center)包 的可选依赖 power-profiles-daemon 以支持电源配置文件。 由于 *gnome-shell* 和 GNOME 设置都请求在启动时激活 `power-profiles-daemon` 服务，所以不需要特意将其启动/启用。

当服务激活时，可以通过 GNOME 设置的*电源*选项和系统菜单管理电源配置文件。

#### 录屏

内建的截屏工具没有默认录屏选项。安装 [gnome-shell](https://archlinux.org/packages/?name=gnome-shell)包的可选依赖 [gst-plugin-pipewire](https://archlinux.org/packages/?name=gst-plugin-pipewire)包 以启用录屏功能。

### 使用不同的窗口管理器

GNOME Shell 不支持更改[窗口管理器](../窗口管理器/窗口管理器.md), 但是 [GNOME Flashback](GNOME_Flashback.md) 提供使用 Metacity 和 [Compiz](../堆叠式窗口管理器/Compiz.md) 的session。此外，可以通过[自定义 GNOME sessions](GNOME_提示与技巧.md#Custom_GNOME_sessions) 来使用别的组件。

将 GNOME Shell替换为不同的 Wayland compositor 将会导致[gnome-control-center](https://archlinux.org/packages/?name=gnome-control-center)包 （GNOME 设置）的部分设置显示错误。*gnome-control-center* 依然可以正常工作，但因为 [mutter](https://archlinux.org/packages/?name=mutter)包 （GNOME Shell） 无法用于提供这些设置以填充这些区域，他们将不再起效或无法准确反映你的设置。这些设置包括蓝牙，显示以及鼠标/触控板等。

## 参见

- [官方网站](https://www.gnome.org/)
- [Contributing to GNOME, feature requests, bugs, code](https://blogs.gnome.org/tbernard/2021/06/15/community-power-2/)
- [GNOME-shell 扩展](https://extensions.gnome.org/)
- 主题、图标和壁纸：
    - [GNOME Art](https://art.gnome.org/)
    - [GNOME 外观](https://www.gnome-look.org/)
- GTK/GNOME 程序：
    - [GNOME 文件管理](https://www.gnomefiles.org/)
    - [GNOME 项目列表](https://www.gnome.org/projects/)
- [自定义 GNOME Shell](https://blog.fpmurphy.com/2011/03/customizing-the-gnome-3-shell.html)
- GNOME 代码和镜像:
    - [GNOME GitLab](https://gitlab.gnome.org/)
    - [GNOME Github 镜像](https://github.com/GNOME)
