# 第 16 章　Xfce

> Xfce 不是一整块，而是窗口管理器、文件管理器，以及把它们组织成会话的那一层。建议先读《Xfce》把桌面跑起来，这一节必读，配置和排错也集中在这里；再读《Thunar》，日常改动最多的就是文件管理器，自定义动作与卷管理器值得看清；《Xfwm》篇幅最短，只在调窗口行为、快捷键时翻，其余可跳。
> 
> 最容易踩的坑是把「装好了」当成「配好了」：窗口行为归 Xfwm，文件管理器的右键菜单和挂载归 Thunar，两边配置不在同一处，从别处整段抄来的往往只生效一半。启动或显示不对时，先按各节末尾的故障排除顺一遍。

<!-- 本章 3 节，共 63 KB -->

## 16.1　Xfwm

*难度：入门 ｜ 篇幅：5.5 KB ｜ 所属：Xfce*

将要讲到：安装、启动、配置、提示与技巧、故障排除、另见

**Xfwm** 是用于 [Xfce](Xfce.md) 桌面环境的窗口管理器。

## 安装

安装 [xfwm4](https://archlinux.org/packages/?name=xfwm4)包 软件包。

## 启动

使用 [xinit](../Xorg%20命令/Xinit.md) 启动 `xfwm4`。

## 配置

Xfwm 的大多数窗口表现和快捷键设置可以通过 `xfwm4-settings` 修改,高级设置和窗口合成可以通过 `xfwm4-tweaks-settings` 修改,工作区数量和名字可以通过 `xfwm4-workspace-settings` 修改。

### 合成管理器

> **注意：**
>
> - 这个窗口合成器可能导致应用中出现画面撕裂,请见 [#画面撕裂](#画面撕裂)。
> - 窗口合成器自从 Xfwm 4.12 开始默认启用。

要启用或禁用 Xfwm 窗口合成器和修改它的设置,前往*窗口管理器微调*:

```
$ xfwm4-tweaks-settings
```

或者,使用带有 `--compositor` 参数的 *xfconf* 来启用它。例如:

```
~/.xinitrc
```

```
exec xfwm4 --compositor=on
```

```
$ xfconf-query -c xfwm4 -p /general/use_compositing -s true
```

### 窗口卷起

双击标题栏或在窗口菜单中点击*卷起窗口*会让窗口内容消失,只留下标题栏。要使用 `xfconf` 禁用这个功能,运行命令:

```
$ xfconf-query -c xfwm4 -p /general/mousewheel_rollup -s false
```

### 窗口平铺

Xfwm 可以在将窗口移动到屏幕边缘时会自动将其平铺。它通过调整窗口大小以适合屏幕的上半部分来实现自动平铺。要使用 `xfconf` 启用或禁用这个操作,运行命令:

```
$ xfconf-query -c xfwm4 -p /general/tile_on_move -s false
$ xfconf-query -c xfwm4 -p /general/tile_on_move -s true
```

或者,勾选(取消勾选) *Window Manager Tweaks > Accessibility > Automatically tile windows when moving toward the screen edge*。

### Xfce 设置管理器提供的额外设置

安装 [官方软件仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)的 [xfce4-settings](https://archlinux.org/packages/?name=xfce4-settings)包。

> **注意：**安装 [xfce4-settings](https://archlinux.org/packages/?name=xfce4-settings)包 可能会修改一些任务的默认应用。请见 [xdg-open](../Freedesktop.org/Xdg-utils.md) 以设置你想要的默认应用。

### 附加主题

安装 [官方软件仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)的 [xfwm4-themes](https://archlinux.org/packages/?name=xfwm4-themes)包。

安装的主题会在 `xfwm4-settings` 窗口中显示出来。

## 提示与技巧

### 在窗口最大化时隐藏标题栏

前往 `Accessibility` 并勾选 `Hide title of windows when maximized`。

> **注意：**如果你想把当前最大化窗口的标题栏放在面板上,安装 [xfce4-windowck-plugin](https://aur.archlinux.org/packages/xfce4-windowck-plugin/)AUR。

## 故障排除

### 在浏览器中下载的项目没有图标

这可以通过安装[官方软件仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)的 [xfce4-settings](https://archlinux.org/packages/?name=xfce4-settings)包 来修复。

### 工作区数量发生意外更改

请注意 *Xfwm* 给添加和移除工作区分配了快捷键。默认情况分别是 `Alt+Delete` 和 `Alt+Insert`。

如果在登录时工作区的数量会重置,请在启动 Xfwm **之后**修改工作区数量。可以通过 `sleep` 命令来保证启动顺序。

```
~/.xinitrc
```

```
(sleep 3 && xfconf-query -v -c xfwm4 -p /general/workspace_count -s number) &
exec xfwm4
```

或者,通过 [xfce4-session](https://archlinux.org/packages/?name=xfce4-session)包:

```
~/.config/autostart/workspace.desktop
```

```
[Desktop Entry]
Exec=sh -c "sleep 3 && xfconf-query -v -c xfwm4 -p /general/workspace_count -s number"
```

另见: [注销会改变工作区](https://forum.xfce.org/viewtopic.php?id=6056)

### 画面撕裂

如果有画面撕裂现象,你可以试着修改 Xfwm 的 `--vblank` 模式选项(*glx*,*xpresent* 或 *off*),先使用以下命令尝试一下:

```
$ xfwm4 --replace --vblank=glx &
```

要想保存这个设置:

```
$ xfconf-query -c xfwm4 -p /general/vblank_mode -s glx
```

如果你使用 Intel 显卡并且如 [Intel graphics#Tearing](../图形/Intel%20图形处理器.md#Tearing) 中所述在 Xorg 中启用了 *TearFree* 选项,请禁用 *Synchronize drawing to the vertical blank* 选项。

如果这不能修复撕裂现象,请考虑关闭 Xfwm 的窗口合成器并使用替代的[合成管理器](../X%20服务器/Xorg.md#合成)。

### Dock 窗口上方的水平线

Xfwm 可能会错误地渲染一些 Dock 窗口的阴影(例如Plank)。这可能会导致屏幕上有一条水平线。一个解决方案是禁用*设置 > 窗口管理器微调 > 窗口合成*中的*显示 Dock 窗口下的阴影*。

## 另见

- [Xfwm - 介绍](https://docs.xfce.org/xfce/xfwm4/introduction)

## 16.2　Thunar

*难度：进阶 ｜ 篇幅：16.2 KB ｜ 所属：Xfce*

将要讲到：安装、配置、Thunar 卷管理器、自定义动作、提示与技巧、故障排除

来自项目[官网](https://docs.xfce.org/xfce/thunar/start):

: Thunar 是一个用于 Xfce 桌面环境的现代化的文件管理器。Thunar 的设计初衷就是快速且易于使用。其界面简洁直观,默认不包含令人误解或无用的选项。Thunar 快速且响应迅速，具有良好的启动时间和文件夹加载时间。

## 安装

安装 [thunar](https://archlinux.org/packages/?name=thunar)包 软件包。Thunar [xfce4](https://archlinux.org/groups/x86_64/xfce4/)包组 软件包组的一部分,也是 [Xfce](Xfce.md) 桌面环境的默认文件管理器。

### 插件与附件

- **Gnome 虚拟文件系统** — 安装后显示垃圾桶，可移动介质，和远程文件系统（`mtp`/`smb`）。详情请见 [File manager functionality#Mounting](../../06_软件与应用/文件管理器/文件管理器功能.md#Mounting)。

: [https://wiki.gnome.org/Projects/gvfs](https://wiki.gnome.org/Projects/gvfs) || [gvfs](https://archlinux.org/packages/?name=gvfs)包

- **Thuanr 归档插件** — 让你能够使用上下文菜单创建和解压归档文件的插件。它并不会直接创建或解压归档,而是作为其他程序的前端,例如 File Roller ([file-roller](https://archlinux.org/packages/?name=file-roller)包), Ark ([ark](https://archlinux.org/packages/?name=ark)包), Xarchiver ([xarchiver](https://archlinux.org/packages/?name=xarchiver)包) 或 Engrampa ([engrampa](https://archlinux.org/packages/?name=engrampa)包)。是软件包组 [xfce4-goodies](https://archlinux.org/groups/x86_64/xfce4-goodies/)包组 的一部分。

: [https://goodies.xfce.org/projects/thunar-plugins/thunar-archive-plugin](https://goodies.xfce.org/projects/thunar-plugins/thunar-archive-plugin) || [thunar-archive-plugin](https://archlinux.org/packages/?name=thunar-archive-plugin)包

- **Thunar 媒体标签插件** — 让你能够预览媒体文件详细信息的插件。它还带有批量重命名和编辑媒体标签的功能。它支持 ID3 (MP3 文件格式系统) 和 Ogg/Vorbis 标签。是软件包组 [xfce4-goodies](https://archlinux.org/groups/x86_64/xfce4-goodies/)包组 的一部分。

: [https://goodies.xfce.org/projects/thunar-plugins/thunar-media-tags-plugin](https://goodies.xfce.org/projects/thunar-plugins/thunar-media-tags-plugin) || [thunar-media-tags-plugin](https://archlinux.org/packages/?name=thunar-media-tags-plugin)包

- **Thunar 共享插件** — 让你能够无需 root 权限,通过 Thunar ,使用 Samba 快速分享文件夹的插件。另见[如何配置方向](../../03_网络与服务/网络共享/Samba.md#Enable_Usershares)。

: [https://goodies.xfce.org/projects/thunar-plugins/thunar-shares-plugin](https://goodies.xfce.org/projects/thunar-plugins/thunar-shares-plugin) || [thunar-shares-plugin](https://archlinux.org/packages/?name=thunar-shares-plugin)包

- **[Thunar 卷管理器](#Thunar_卷管理器)** — 在 Thunar 中自动管理可移动设备。是软件包组 [xfce4-goodies](https://archlinux.org/groups/x86_64/xfce4-goodies/)包组 的一部分。

: [https://goodies.xfce.org/projects/thunar-plugins/thunar-volman](https://goodies.xfce.org/projects/thunar-plugins/thunar-volman) || [thunar-volman](https://archlinux.org/packages/?name=thunar-volman)包

- **Tumbler** — 生成缩略图的外部程序。还可以安装 [ffmpegthumbnailer](https://archlinux.org/packages/?name=ffmpegthumbnailer)包 来启用视频缩略图。

: [https://gitlab.xfce.org/xfce/tumbler](https://gitlab.xfce.org/xfce/tumbler) || [tumbler](https://archlinux.org/packages/?name=tumbler)包

- **libgsf** — GNOME 结构化文件库是一个用于读写结构化文件格式的实用程序库。如果你需要 odf 缩略图的支持就安装。
: [https://directory.fsf.org/wiki/Libgsf](https://directory.fsf.org/wiki/Libgsf) || [libgsf](https://archlinux.org/packages/?name=libgsf)包

- **RAW thumbnailer** — 一个轻量快速的原始图像缩略图生成器,用于显示原始图像缩略图。
: [https://code.google.com/archive/p/raw-thumbnailer/](https://code.google.com/archive/p/raw-thumbnailer/) || [raw-thumbnailer](https://aur.archlinux.org/packages/raw-thumbnailer/)AUR

- **Extra thumbnailers** — 为 Tumbler 定制的缩略图生成器。
: [https://docs.xfce.org/xfce/tumbler/available_plugins/](https://docs.xfce.org/xfce/tumbler/available_plugins/) || [tumbler-extra-thumbnailers](https://aur.archlinux.org/packages/tumbler-extra-thumbnailers/)AUR

- **Folder thumbnailer** — 通过 Tumbler 支持自定义文件夹缩略图。
: [https://github.com/j-james/thunar-folder-thumbnails](https://github.com/j-james/thunar-folder-thumbnails) || [tumbler-folder-thumbnailer](https://aur.archlinux.org/packages/tumbler-folder-thumbnailer/)AUR

- **STL thumbnailer** — 为 Tumbler 生成 STL 缩略图的支持。
: [https://github.com/j-james/thunar-stl-thumbnails](https://github.com/j-james/thunar-stl-thumbnails) || [tumbler-stl-thumbnailer](https://aur.archlinux.org/packages/tumbler-stl-thumbnailer/)AUR

- **webp-pixbuf-loader** — [gdk-pixbuf2](https://archlinux.org/packages/?name=gdk-pixbuf2)包 的可选依赖，为默认的缩略图生成器提供了生成 webp 缩略图的支持。
: [https://github.com/aruiz/webp-pixbuf-loader](https://github.com/aruiz/webp-pixbuf-loader) || [webp-pixbuf-loader](https://archlinux.org/packages/?name=webp-pixbuf-loader)包

## 配置

要配置按键绑定，请编辑 `~/.config/Thunar/accels.scm` 文件。要配置 Thunar 的隐藏变量，请使用 `xfconf-query -c thunar -l -v`。

## Thunar 卷管理器

若已安装 [gvfs](https://archlinux.org/packages/?name=gvfs)包 和 [thunar-volman](https://archlinux.org/packages/?name=thunar-volman)包，可配置 Thunar 在介质连接时自动执行命令。对于可移动设备,通常遵循 [MTP](../../04_硬件与外设/移动设备/媒体传输协议.md),需要额外安装 [gvfs-mtp](https://archlinux.org/packages/?name=gvfs-mtp)包 软件包。

> **提示：**要想让 Thunar 处理自动挂载,必须在[守护进程模式](#以守护进程模式启动)中启动。

### 配置

它还可以配置为在连接相机和音频播放器时执行某些操作。 在安装插件之后:

1.  启动 Thunar 然后前往 *Edit > Preference*
2.  在 'Advanced' 标签页下,勾选 'Enable Volume Management'
3.  点击配置并勾选下列项目:
    - Mount removable drives when hot-plugged.
    - Mount removable media when inserted.
4.  再做出你想要的设置(请见下面的例子)

这是一个自动用 Amarok 播放音频 CD 的设置例子。

```
 Multimedia - Audio CDs: amarok --cdplay %d
```

## 自定义动作

本节介绍有用的自定义操作,可以通过 `Edit -> Configure custom actions` 访问,存储在 `~/.config/Thunar/uca.xml`。[thunar wiki](https://docs.xfce.org/xfce/thunar/custom-actions) 中列出了更多例子。此外,[这篇](https://duncanlock.net/blog/2013/06/28/useful-thunar-custom-actions/)博客文章提供了全面的自定义操作集合。

### 在此处打开终端

“在此处打开终端” 是安装时的唯一操作。为 [exo](https://archlinux.org/packages/?name=exo)包 配置要使用的终端：

```
~/.config/xfce4/helpers.rc
```

```
TerminalEmulator=terminal
```

| Name | Command | File patterns | Appears if selection contains |
| --- | --- | --- | --- |
| Open Terminal Here | `exo-open --working-directory %f --launch TerminalEmulator` | \* | Directories |

### 搜索文件的目录

要想使用这个动作,你需要安装 [catfish](https://archlinux.org/packages/?name=catfish)包。对于想要有预构建的索引数据库的用户, 可以安装[plocate](https://archlinux.org/packages/?name=plocate)包 和 [zeitgeist](https://archlinux.org/packages/?name=zeitgeist)包 可选依赖。

| Name | Command | File patterns | Appears if selection contains |
| --- | --- | --- | --- |
| Search | `catfish --path=%f` | \* | Directories |

### 扫描病毒

要想使用这个动作,你需要安装 [clamav](https://archlinux.org/packages/?name=clamav)包 和 [clamtk](https://archlinux.org/packages/?name=clamtk)包。

| Name | Command | File patterns | Appears if selection contains |
| --- | --- | --- | --- |
| Scan for virus | `clamtk %F` | \* | Select all |

### 链接到 Dropbox

| Name | Command | File patterns | Appears if selection contains |
| --- | --- | --- | --- |
| Link to Dropbox | `ln -s %f /path/to/DropboxFolder` | \* | Directories, other files |

请注意当使用许多自定义操作将文件和文件夹符号链接到特定位置时,将它们放到上下文菜单中的 `发送到` 目录可以避免上下文菜单变得臃肿。这很容易实现,需要在 `~/.local/share/Thunar/sendto` 中为每个动作创建一个 .desktop 文件。假设我们要将上述 Dropbox 符号链接操作放入 "发送到",创建一个带有下列内容的 `dropbox_folder.desktop` 。新应用的动作会在 Thunar 重启后激活。

```
[Desktop Entry]
Type=Application
Version=1.0
Encoding=UTF-8
Exec=ln -s %f /path/to/DropboxFolder
Icon=/usr/share/icons/dropbox.png
Name=Dropbox
```

## 提示与技巧

### 使用 Thunar 浏览远程位置

因为 Xfce 4.8 (Thunar 1.2) 可以直接在 Thunar 浏览远程位置 (例如 FTP 服务器或 Samba 共享)。要想启用这个功能，确保 [gvfs](https://archlinux.org/packages/?name=gvfs)包 和 [sshfs](https://archlinux.org/packages/?name=sshfs)包（若需要 SMB/CIFS 支持，还需要 [gvfs-smb](https://archlinux.org/packages/?name=gvfs-smb)包）软件包已安装。Thunar 侧边栏可以看到 '网络' 项目,可以在位置对话框中使用以下 URI 方案打开远程位置 (用 `Ctrl+l` 打开):smb://, ftp://, ssh://, sftp://, davs:// 后跟服务器主机名或 IP 地址。

没有用于 [NFS](../../03_网络与服务/网络共享/NFS.md) 共享的 URI 方案,但是如果你正确设置你的 [fstab](../../01_安装与包管理/启动过程/Fstab.md) ,Thunar 可以发出 `mount` 命令。

```
/etc/fstab
```

```
# nas1 server
nas1:/c/home		/media/nas1/home	nfs	noauto,user,_netdev,bg  0 0
```

这里要注意的是 `noauto` 选项,它会在你点击挂载前阻止共享被自动挂载,`user` 选项是被允许挂载和卸载共享的用户,`_netdev` 选项使网络连接成为先决条件,最后 `bg` 选项使挂载操作在后台进行,如果你的服务器需要一些启动时间,这么做将使你在它工作前不必处理超时消息并重新单击。

> **提示：**
>
> - 如果你想要持久存储远程文件系统位置的密码,你必须安装 [GNOME Keyring](https://wiki.archlinux.org/title/GNOME/Keyring)。
> - 为了让 Thunar 显示任意非 root 用户的新设备,可能需要将挂载点设置为 `/media` 的子目录。

### 以守护进程模式启动

Thunar 可以以守护进程模式运行。这有许多优势,包括加快 Thunar 的启动速度,Thunar 在后台运行而且在需要时只打开一个窗口(例如当闪存驱动器插入时),和让 Thunar 处理可移动介质的自动挂载。

确保命令 `thunar --daemon` 在登录时自动运行。详情请见 [Xfce](Xfce.md) 和[自动运行](../../02_系统管理与内核/系统管理/自动启动.md)。

### 缓慢的冷启动问题的解决方案

一些人还有 Thunar 在首次启动时耗时过长的问题。这是因为 gvfs 检查网络,阻止 Thunar 启动直到它完成该操作。要想改善此行为,编辑 `/usr/share/gvfs/mounts/network.mount` ,将 `AutoMount=true` 修改为 `AutoMount=false`。

### 在侧窗格中隐藏快捷方式

在侧窗格中有一个隐藏菜单用于隐藏快捷方式。

在侧窗格中没有快捷方式的地方右键,例如在 DEVICES 标签上。然后会弹出一个菜单,你可以将不想显示出的项目取消勾选。

### 在 Thunar 中分配键盘快捷键

请见 [GTK#键盘快捷键](../../07_开发与工具/控件工具集/GTK.md#键盘快捷键)。

### 显示在 fstab 中定义的分区

默认情况下 Thunar 不会在设备中显示 `/etc/fstab` 中定义的任何分区,除了 root 分区。

我们可以通过向 [fstab](../../01_安装与包管理/启动过程/Fstab.md) 中为想要显示出的分区添加 `x-gvfs-show` 选项。

## 故障排除

### 大型外部驱动器的自动挂载

如果安装了 thunar-volman 和 gvfs,但 Thunar 拒绝挂载大型可移动介质(大小 > 1TB),试着安装一个不同的自动挂载器例如 [udevil](https://aur.archlinux.org/packages/udevil/)AUR 或 [udiskie](https://archlinux.org/packages/?name=udiskie)包。更推荐后者因为它使用 udisks2 而且它与 gvfs 兼容。要想启动带有 udisks2 支持的 udiskie,将下列内容添加到你的自动启动文件:

```
udiskie -2 &
```

### Tumblerd 挂起,占用过多 CPU

Tumblerd 在监视文件系统并在需要制作缩略图时通知系统的服务时可能会陷入循环,请见 [bug 报告](https://bugzilla.xfce.org/show_bug.cgi?id=7384)。下面的脚本是一个阻止这种情况发生的临时解决方案。复制,将它粘贴到一个 *.sh* 文件,将它保存的你的家目录的某个位置,将文件标记为可运行,然后在将它设置为在系统启动时自动运行。

```
#!/bin/bash
period=20
tumblerpath="/usr/lib/*/tumbler-1/tumblerd" # The * here should find the right one, whether 32 and 64-bit
cpu_threshold=50
mem_threshold=20
max_strikes=2                               # max number of above cpu/mem-threshold's in a row
log="/tmp/tumblerd-watcher.log"

if [[ -n "${log}" ]]; then
    cat /dev/null > "${log}"
    exec >"${log}" 2>&1
fi

strikes=0
while sleep "${period}"; do
    while read pid; do
	cpu_usage=$(ps --no-headers -o pcpu -f "${pid}"|cut -f1 -d.)
	mem_usage=$(ps --no-headers -o pmem -f "${pid}"|cut -f1 -d.)

	if [[ $cpu_usage -gt $cpu_threshold ]] || [[ $mem_usage -gt $mem_threshold ]]; then
	    echo "$(date +"%F %T") PID: $pid CPU: $cpu_usage/$cpu_threshold %cpu MEM: $mem_usage/$mem_threshold STRIKES: ${strikes} NPROCS: $(pgrep -c -f ${tumblerpath})"
	    (( strikes++ ))
	    if [[ ${strikes} -ge ${max_strikes} ]]; then
		kill "${pid}"
		echo "$(date +"%F %T") PID: $pid KILLED; NPROCS: $(pgrep -c -f ${tumblerpath})"
		strikes=0
	    fi
	else
	    strikes=0
	fi
    done < <(pgrep -f ${tumblerpath})
done
```

### 垃圾桶/网络 图标随机消失

确保所有 Thunar 示例在 *gvfs* **之后**启动。对于 `thunar --daemon`, 你可以将它包装成等待直到 GVFS 启动:

> **注意：**在 `$PATH` 中 `/usr/local/bin` 应该在 `/usr/bin` 的前面。

```
/usr/local/bin/Thunar
```

```
#!/bin/bash
if [[ $1 == --daemon ]]; then
  until pgrep gvfs >/dev/null; do
    sleep 1
  done
  exec /usr/bin/Thunar "$@"
else
  exec /usr/bin/Thunar "$@"
fi
```

### 未经身份验证挂载文件系统

请见 [File manager functionality#Troubleshooting](../../06_软件与应用/文件管理器/文件管理器功能.md#Troubleshooting)。

### Thunar 新窗口或标签打开速度太慢

可能是因为你在设置为 `XDG_TEMPLATES_DIR` 的目录中有太多文件。请见 [XDG 用户路径](../Freedesktop.org/XDG%20用户目录.md)。

解决方案是将 `XDG_TEMPLATES_DIR` 中文件移到其他地方,或者将 `XDG_TEMPLATES_DIR` 设置为其他目录。

## 另见

- [Thunar](https://docs.xfce.org/xfce/thunar/start) 项目页面
- [Thunar 卷管理器](https://goodies.xfce.org/projects/thunar-plugins/thunar-volman)项目页面
- 这个插件[列表](https://docs.xfce.org/xfce/thunar/start#thunar_plugins)
- 一些设置,比如在标题中显示完整路径,可以通过 `xfconf-query` 启用。详情请见[这个列表](https://docs.xfce.org/xfce/thunar/hidden-settings)。

## 16.3　Xfce

*难度：高级 ｜ 篇幅：40.9 KB ｜ 所属：Xfce*

将要讲到：安装、启动、配置、提示和小技巧、故障排除、另见

[Xfce](https://www.xfce.org) 是一个基于 GTK 3 的轻量级模块化的[桌面环境](../桌面环境/桌面环境.md)。为了提供完整的用户体验，它包含窗口管理器、文件管理器、桌面和面板。

## 安装

安装 [xfce4](https://archlinux.org/groups/x86_64/xfce4/)包组 包组。如果需要的话，还可以安装 [xfce4-goodies](https://archlinux.org/groups/x86_64/xfce4-goodies/)包组 包组。此包组提供了一些额外的插件和一些有用的工具，如 [mousepad](https://archlinux.org/packages/?name=mousepad)包 编辑器。 Xfce 默认使用 [Xfwm](Xfwm.md) 作为窗口管理器。

## 启动

从[显示管理器](../图形用户界面/显示管理器.md)中选择*Xfce Session*，或者添加 `exec startxfce4` 到 [Xinitrc](../Xorg%20命令/Xinit.md) 中。

> **注意：**不要直接调用 `xfce4-session`可执行文件，`startxfce4` 是正确的命令，它会在恰当的时间调用前述可执行文件。

## 配置

Xfce 把配置的选项保存到 [Xfconf](https://docs.xfce.org/xfce/xfconf/start)。有几个方式来修改这些选项：

- 在主菜单中，选择[设置](https://docs.xfce.org/xfce/xfce4-settings/start)和要自定义的类别。类别是通常位于 `/usr/bin/xfce4-*` 和 `/usr/bin/xfdesktop-settings` 中的程序。
- `xfce4-settings-editors` 可以查看和修改所有设置。此处修改的选项会立即生效。使用`xfconf-query`从命令行更改设置；[文档中](https://docs.xfce.org/xfce/xfconf/xfconf-query)有更多的细节。
- 设置保存在 XML 文件中。此文件位于 `~/.config/xfce4/xfconf/xfce-perchannel-xml/`，可以手动修改。但是，此处的修改不会立即生效。

### 菜单

关于使用 Free Desktop 菜单系统的信息，参见 [Xdg-menu](../Freedesktop.org/Xdg-menu.md)。

#### Whisker 菜单

[xfce4-whiskermenu-plugin](https://archlinux.org/packages/?name=xfce4-whiskermenu-plugin)包（包含在 [xfce4-goodies](https://archlinux.org/groups/x86_64/xfce4-goodies/)包组 中）是默认的应用启动器的可选替代品。它可以显示收藏夹列表，通过类别按钮浏览所有已安装的应用程序，并支持模糊搜索。安装完成后，就可以替换掉面板1的第一个项目“应用程序菜单”了（在*设置 > 面板 > 项目*中添加*Whisker 菜单*）。

##### 为 Whisker 菜单设置快捷键

要想设置启动 Whisker 菜单的键盘快捷键,前往*设置 > 键盘*的*应用快捷键*页面。点击*添加*,将命令设置为 `xfce4-popup-whiskermenu` 并分配你想要的键盘快捷键。

#### 编辑菜单

许多图形工具可以用来实现此项需求：

- **MenuLibre** — 一个高级的菜单编辑器，提供了一个清晰、易用的界面。

: [https://launchpad.net/menulibre](https://launchpad.net/menulibre) || [menulibre](https://aur.archlinux.org/packages/menulibre/)AUR

- **Alacarte** — GNOME的菜单编辑器。

: [https://www.gnome.org/](https://www.gnome.org/) || [alacarte](https://archlinux.org/packages/?name=alacarte)包

- **XAME (XFCE Applications Menu Editor)** — 使用 Gambas 编写，专门用于编辑Xfce菜单项的图形工具，在其他环境中没有效果。（已停止开发）

: [http://redsquirrel87.altervista.org/doku.php/xfce-applications-menu-editor](http://redsquirrel87.altervista.org/doku.php/xfce-applications-menu-editor) || [xame](https://aur.archlinux.org/packages/xame/)AUR

或者也可以手动创建 `~/.config/menus/xfce-applications.menu`。下面给出一个示例的配置：

```
<!DOCTYPE Menu PUBLIC "-//freedesktop//DTD Menu 1.0//EN"
  "http://www.freedesktop.org/standards/menu-spec/1.0/menu.dtd">

<Menu>
    <Name>Xfce</Name>
    <MergeFile type="parent">/etc/xdg/menus/xfce-applications.menu</MergeFile>

    <Exclude>
        <Filename>xfce4-run.desktop</Filename>
        <Filename>exo-terminal-emulator.desktop</Filename>
        <Filename>exo-file-manager.desktop</Filename>
        <Filename>exo-mail-reader.desktop</Filename>
        <Filename>exo-web-browser.desktop</Filename>
        <Filename>xfce4-about.desktop</Filename>
        <Filename>xfhelp4.desktop</Filename>
    </Exclude>

    <Layout>
        <Merge type="all"/>
        <Separator/>
        <Menuname>Settings</Menuname>
        <Separator/>
        <Filename>xfce4-session-logout.desktop</Filename>
    </Layout>
</Menu>
```

`<MergeFile>` 标签包含了默认的Xfce菜单。

`<Exclude>` 标签剔除了你不想在菜单中出现的应用程序。尽管此处我们只剔除了一些Xfce的默认快捷方式，但是你也可以剔除 `firefox.desktop` 或其他任何的应用程序。

`<Layout>` 标签定义了菜单的布局。应用程序可以分组放在文件夹中，或以任何我们想要的方式进行组织。在 [Xfce wiki](https://wiki.xfce.org/howto/customize-menu) 有更多的详细信息。

你可以通过编辑 `.desktop` 本身来改变Xfce的菜单。关于如何隐藏项目，参见 [Desktop entries#Hide desktop entries](../../01_安装与包管理/配置文件/桌面项.md#Hide_desktop_entries)。你可以通过改变 `.desktop` 中的 `Categories=` 来编辑应用程序类别。参见 [Desktop entries#File example](../../01_安装与包管理/配置文件/桌面项.md#File_example)。

#### 设置首选应用程序

要想修改打开某种文件的默认应用,使用 [exo-preferred-applications](https://docs.xfce.org/xfce/exo/preferred-applications) 。这会修改 `exo-open` 的行为,它由 resource opener 调用,例如 [xdg-open](../Freedesktop.org/Xdg-utils.md)。

### 桌面

#### 图标文字的透明背景

默认桌面图标的文字是白色背景，可以创建或者修改 `~/.gtkrc-2.0` 来得到不一样的效果：

```
style "xfdesktop-icon-view" {
    XfdesktopIconView::label-alpha = 10
    base[NORMAL] = "#000000"
    base[SELECTED] = "#71B9FF"
    base[ACTIVE] = "#71B9FF"
    fg[NORMAL] = "#fcfcfc"
    fg[SELECTED] = "#ffffff"
    fg[ACTIVE] = "#ffffff"
}
widget_class "*XfdesktopIconView*" style "xfdesktop-icon-view"
```

#### 删除桌面图标

使用如下的命令：

```
$ xfconf-query -c xfce4-desktop -v --create -p /desktop-icons/style -t int -s 0
```

要想恢复桌面上的图标,使用相同的命令,但将数值改为 2。

#### 多显示器连续壁纸

打开 `xfce4-settings-editor` 创建如下的属性：

```
Property: /backdrop/screen0/xinerama-stretch
Type: Boolean
Value: TRUE|1|Enabled
```

#### 关闭窗口的快捷键

Xfce没有关闭窗口的快捷键，当程序假死时，我们可能需要这样的快捷键。

使用 [xorg-xkill](https://archlinux.org/packages/?name=xorg-xkill)包，`xkill` 可以交互关闭窗口。对于当下的激活窗口，使用包 [xdotool](https://archlinux.org/packages/?name=xdotool)包：

```
$ xdotool getwindowfocus windowkill
```

也可以：

```
$ sh -c "xkill -id $(xprop -root -notype | sed -n '/^_NET_ACTIVE_WINDOW/ s/^.*# *\|\,.*$//g p')"
```

添加快捷键，使用**设置 > 键盘**或者使用应用程序，如 [xbindkeys](https://archlinux.org/packages/?name=xbindkeys)包。

### 会话

#### 自动运行

可以在**程序 > 设置 > 设置管理器 > 会话和自启动**中点击**应用程序自启动**，设置与Xfce一起启动的自启动程序。 此处列出了所有自启动的程序。点击**添加**按钮后可以添加自定义的自启动任务，需指定可执行文件的路径。

自启动应用会在 `~/.config/autostart/` 中以 `*name*.desktop` 存储。

或者也可以将要执行的命令（包括设置环境变量）加入 [xinitrc](../Xorg%20命令/Xinit.md)。如果使用[显示管理器](../图形用户界面/显示管理器.md)，则加入 [xprofile](../X%20服务器/Xprofile.md) 。

> **提示：**有时**延迟应用的启动**是很有用的。注意在 *Application > Autostart* 中的设定的命令例如 `sleep 3 && *command*` 不会工作;一个解决方案是使用 `sh -c "sleep 3 && *command*"`的语法

延迟某个应用程序启动有时可能很有用。在**应用程序自启动**中指定类似 `sleep 3 && command` 的命令不会起作用。作为一个解决办法，可以使用如下命令：

```
sh -c "sleep 3 && command"
```

#### 锁定屏幕

*xflock4* 是用于锁定 Xfce 会话的参考 Bash 脚本。

它试图使用 [xfce4-screensaver](https://archlinux.org/packages/?name=xfce4-screensaver)包 (是软件包组 [xfce4-goodies](https://archlinux.org/groups/x86_64/xfce4-goodies/)包组 的一部分),[xscreensaver](https://archlinux.org/packages/?name=xscreensaver)包,[gnome-screensaver](https://aur.archlinux.org/packages/gnome-screensaver/)AUR,[slock](https://archlinux.org/packages/?name=slock)包,[xlockmore](https://archlinux.org/packages/?name=xlockmore)包 中的一个来锁定屏幕。它连续查找相应的二进制文件，如果找不到其中任何一个，则返回代码 1 退出。

[应用程序列表/安全#锁屏](../../06_软件与应用/应用程序/应用程序列表_安全.md#Screen_lockers)包含了对这些锁屏软件和其他流行应用的简短描述。这里列出一些替代锁屏应用,[light-locker](https://archlinux.org/packages/?name=light-locker)包 与 [xfce4-power-manager](https://archlinux.org/packages/?name=xfce4-power-manager)包 集成得特别好。安装之后,Xfce 电源管理的设置会有一个额外的*安全*标签页用于配置 *light-locker*,已存在的*系统睡眠时锁定屏幕*设置也会重定位到此页面下。在这个新的图形界面可以设置是否应在屏幕保护程序活动或系统进入睡眠状态时锁定会话。

要想让 *xflock4* 运行 *light-locker* 或上述五个以外的任意自定义的会话锁定软件,必须在会话的 xfconf 频道中将 `LockCommand` 设置为使用的命令(以下示例中引号内的命令可以针对其他屏幕锁定软件进行相应调整):

```
$ xfconf-query --create -c xfce4-session -p /general/LockCommand -t string -s "light-locker-command --lock"
```

面板的*动作按钮*界面中的锁定按钮只是运行 `/usr/bin/xflock4`。只要 *xflock4* 有用,那么它应该能如预期工作,例如安装原生锁屏软件中的一个,或如上述将自定义锁屏软件配置为与它集成。

#### 睡眠

当请求睡眠时,Xfce 运行带有 `suspend` 选项的 [xfce4-session-logout(1)](https://man.archlinux.org/man/xfce4-session-logout.1) 命令:

```
$ xfce4-session-logout --suspend
```

会话是否系统地锁定为*睡眠*可以通过 xfconf 属性或图形界面进行配置。

要想使用命令行控制此状态:有两个设置,分别位于会话和电源管理的 xfconf 频道的 `LockScreen` 和 `lock-screen-suspend-hibernate`。

如果不想在睡眠时锁定,将它们设置为 `false`:

```
$ xfconf-query -c xfce4-session -p /shutdown/LockScreen -s false
$ xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/lock-screen-suspend-hibernate -s false
```

类似地,将它们设置为 `true` 将会在睡眠时锁定。

这些设置也可以通过图形界面修改:打开*会话与启动*应用,打开或关闭*Advanced > Lock screen before sleep*。

无论何时按下睡眠键,它都会由 Xfce 的电源管理器或 *systemd-logind* 处理。要提高 logind 的优先级,下列 xfconf 设置必须设置为 `true`:

```
$ xfconf-query --create -c xfce4-power-manager -p /xfce4-power-manager/logind-handle-suspend-key -t bool -s true
```

> **注意：**要想查看 *systemd-logind* 在优先于 Xfce 时如何处理事件,查看 [logind.conf(5)](https://man.archlinux.org/man/logind.conf.5)

#### 禁用保存的会话

可以通过下面命令禁用某个用户已保存的会话：

```
$ xfconf-query -c xfce4-session -p /general/SaveOnExit -s false
```

然后进入*应用程序 -> 设置 -> 会话和启动 -> 会话*并点击*清除已保存的会话*按钮来清除所有之前保存的会话。

> **提示：**如果上面命令无法持久生效，可以用下面命令：`xfconf-query -c xfce4-session -p /general/SaveOnExit -n -t bool -s false`

Xfce [kiosk 模式](https://wiki.xfce.org/howto/kiosk_mode)可以用来彻底禁用对话的保存。要禁用对话，创建或者编辑 `/etc/xdg/xfce4/kiosk/kioskrc` 并加入如下内容：

```
[xfce4-session]
SaveSession=NONE
```

如果kiosk模式不起作用，用户可以给会话目录设置只读权限：

```
$ rm ~/.cache/sessions/* && chmod 500 ~/.cache/sessions
```

此操作会防止Xfce保存所有的会话，即使任何别的设置允许保存会话。

#### 使用不同的窗口管理器

> **注意：** 要使更改生效，需要清除保存的会话，并确保在首次注销时禁用会话保存。 一旦选择的窗口管理器正在运行，可以再次启用会话保存。

窗口管理器的设定保存在：

- /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/xfce4-session.xml - 系统设置
- ~/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-session.xml - 用户设置

单个用户的默认窗口管理器可以用*xfconf-query*命令设置：

```
$ xfconf-query -c xfce4-session -p /sessions/Failsafe/Client0_Command -t string -sa xfsettingsd
$ xfconf-query -c xfce4-session -p /sessions/Failsafe/Client1_Command -t string -sa wm_name
```

如果要使用命令行选项启动窗口管理器，请使用以下命令:

```
$ xfconf-query -c xfce4-session -p /sessions/Failsafe/Client0_Command -t string -sa xfsettingsd
 $ xfconf-query -c xfce4-session -p /sessions/Failsafe/Client1_Command -t string -s wm_name -t string -s --wm-option
```

如需更多命令行选项，只需向命令中添加更多 `-t string` 和 `-s **--wm-option**` 参数。

如需更改整个系统的默认窗口管理器，手动编辑上面指定的文件，将*xfwm4*更改为首选窗口管理器，并添加更多 `<value type="string" value="**--wm-option**"/>` 选项（如果需要）。

> **注意：**如果将 `Client0_Command` 设置为 `wm_name` 并将 `Client1_Command` 设置为 `xfsettingsd`,就可以阻止 `xfce4-session-logout` 恢复到默认主题。但还是需要[手动设置光标主题](../X%20服务器/光标主题.md#XDG_规范)。

如需在系统范围内修改默认的窗口管理器,手动编辑上述的文件,将 *xfwm4* 改为你想要的窗口管理器,对于额外的命令行参数,添加更多的 `<valuetype="string" value="*--wm-option*"/>`。

还可以通过使用自启动工具自动启动 `*wm_name* --replace` 或通过在终端中运行 `*wm_name* --replace &` 并确保注销时会话已被保存来修改窗口管理器。请注意,这种方法并没有真正修改默认窗口管理器,而是在登录时替换窗口管理器。请注意如果使用了自启动工具,你应该禁用已保存的会话,因为这可能会导致早默认的窗口管理器启动后,新的窗口管理器还启动两次。

### 更换主题

在 [xfce-look.org](https://www.xfce-look.org) 上有不少 XFCE 的主题。 *Xfwm* 的主题保存在 `/usr/share/themes/*theme_name*/xfwm4`, 在*设置 > 窗口管理器*中可以更改主题。 [GTK](../../07_开发与工具/控件工具集/GTK.md) 主题保存在 `/usr/share/themes/*theme_name*/gtk-2.0` 和 `/usr/share/themes/*theme_name*/gtk-3.0`,可以在*设置 > 外观*中修改 [GTK](../../07_开发与工具/控件工具集/GTK.md) 主题。

如果想要使所有的应用能有一个统一的外观, 参见 [Uniform look for Qt and GTK applications](../../07_开发与工具/控件工具集/统一%20Qt%20和%20GTK%20应用程序的外观.md) 获得更多的信息。

另参见[光标主题](../X%20服务器/光标主题.md), Icons, 和 [Font configuration](../字体/字体配置.md)。

#### 统一 SSD 和 CSD 窗口的外观

对于多数窗口,Xfce 当前使用服务器端装饰(SSD)(请见[窗口装饰](https://en.wikipedia.org/wiki/Window_(computing)#Window_decoration)),大多数情况下由 [Xfwm](Xfwm.md) 提供主题,而[客户端装饰](https://en.wikipedia.org/wiki/Client-side_decoration)(CSD) 以 Xfce 设置、打印、保存和其他对话框的相应程序为主题。

Xfwm SSD 窗口风格可以通过一些方法转变为符合 CSD 窗口的主题,比如手动调整,或在 `/usr/share/themes/*theme_name*/xfwm4` 中手动创建主题,或使用例如 [Xfwm4 Theme Generator](https://github.com/andreldm/xfwm4-theme-generator) 这样"从客户端装饰创建 xfwm4 主题"的工具。

#### 恢复客户端装饰

要想去除 Xfce 的大多数 CSD,安装 [libxfce4ui-nocsd](https://aur.archlinux.org/packages/libxfce4ui-nocsd/)AUR 并进行设置:

```
$ xfconf-query -c xsettings -p /Gtk/DialogsUseHeader -s false
```

这会将保存按钮移到窗口的保存对话框的底部,并对打印对话框也做出相同的改动。Xfce 设置对话框将会恢复成 SSD。但是像 [Catfish](https://docs.xfce.org/apps/catfish/start) 这样的程序还是会有 CSD.

要在所有窗口上强制启用 SSD,试试 [gtk3-nocsd-git](https://aur.archlinux.org/packages/gtk3-nocsd-git/)AUR。请务必阅读[项目页面](https://github.com/ZaWertun/gtk3-nocsd)上的所有说明。

### 声音

#### 声音主题

XFCE4支持 [freedesktop system sounds](https://www.freedesktop.org/wiki/Specifications/sound-theme-spec/)， 但需要配置。

启用声音主题：

1.  安装 [libcanberra](https://archlinux.org/packages/?name=libcanberra)包 以提供 [PulseAudio](../../04_硬件与外设/声音/PulseAudio.md) 支持。
2.  "canberra-gtk-module" 应该在 GTK_MODULES 环境变量 (需要重新登录);
3.  在 设置管理器 → 外观 → 设置 选项卡 勾选 "启用事件声音";
4.  在 设置编辑器（Settings Editor） 设置 "xsettings/Net/SoundThemeName" 为一个在 `/usr/share/sounds/` 下的声音主题;
5.  在混声器里（比如pavucontrol）打开系统声音（System Sounds）。

[sound-theme-freedesktop](https://archlinux.org/packages/?name=sound-theme-freedesktop)包 提供兼容的声音主题，缺少许多需要的事件。另一个更好的选择是 [sound-theme-smooth](https://aur.archlinux.org/packages/sound-theme-smooth/)AUR (上面的 SoundThemeName 设置为 "Smooth")。

#### 键盘音量键

[xfce4-pulseaudio-plugin](https://archlinux.org/packages/?name=xfce4-pulseaudio-plugin)包 提供了一个面板小程序，它支持键盘音量控制和音量提示。或者，可以用不提供面板图标的 [xfce4-volumed-pulse](https://aur.archlinux.org/packages/xfce4-volumed-pulse/)AUR，它还提供键绑定和通知控制。当同时使用 [pasystray](https://archlinux.org/packages/?name=pasystray)包 进行更细微的控制时会很方便。

或者使用 [xfce4-mixer](https://aur.archlinux.org/packages/xfce4-mixer/)AUR，它同样提供面板小程序和键盘快捷键，并支持Alsa。然而，请注意，它是基于已在1.0中放弃的GStreamer 0.10的功能。

在安装面板之后,必须将它添加到任务栏否则键盘快捷键不会工作。

对于不需要特定桌面环境的替代方案,请见 [List of applications#Volume control](../../06_软件与应用/应用程序/应用程序列表.md#Volume_control)。

##### 快捷键

如不使用控制音量键的小程序或守护程序，则可以使用Xfce的键盘设置手动将音量控制命令映射到音量键。对于您正在使用的音响系统，请参阅以下链接到相应命令的部分。

- ALSA: [Advanced Linux Sound Architecture#Keyboard volume control](../../04_硬件与外设/声音/ALSA.md#Keyboard_volume_control).
- PulseAudio: [PulseAudio#键盘音量控制](../../04_硬件与外设/声音/PulseAudio.md#键盘音量控制)
- OSS: [OSS#Keyboard volume control](../../04_硬件与外设/声音/Open%20Sound%20System.md#Keyboard_volume_control).

### 键盘快捷键

键盘快捷键在两个地方设置：*设置 > 窗口管理器 > 键盘*和*设置 > 键盘 > 快捷键*。

### Polkit 验证代理

在安装 [xfce4-session](https://archlinux.org/packages/?name=xfce4-session)包 时，会同时安装 [polkit-gnome](https://archlinux.org/packages/?name=polkit-gnome)包，并随系统自动启动;无需用户干预。更多信息请参见 [Polkit#Authentication agents](../../08_安全与加密/安全/Polkit.md#Authentication_agents)。

Xfce 可用的第三方 Polkit 身份认证代理，参见 [xfce-polkit-git](https://aur.archlinux.org/packages/xfce-polkit-git/)AUR 和 [xfce-polkit](https://aur.archlinux.org/packages/xfce-polkit/)AUR。

### 熄屏

一些程序通常和 Xfce 一起使用来控制显示器消隐和 [DPMS](../../02_系统管理与内核/电源管理/Display%20Power%20Management%20Signaling.md) (显示器节能)设置。接下来将讨论它们。

**Xfce 电源管理器**

*Xfce 电源管理器*控制熄屏和 DPMS 设置。这些设置可以在*电源管理器*的图形界面的*显示*标签页中配置。

请注意当*显示电源管理*被关闭时,DPMS 会被完全禁用,这并不意味着*电源管理器*会停止控制 DPMS。它也不会禁用熄屏。要想将熄屏和 DPMS 都禁用,右键电源管理器的系统托盘图标或左键面板小程序并确保选中标记为"演示模式"的选项。

**XScreenSaver**

如果安装了 [xscreensaver](https://archlinux.org/packages/?name=xscreensaver)包 并且和 Xfce 电源管理器一起运行,控制熄屏和 DPMS 的应用可能不明确,因为它们会抢占同一个设置的控制权。因此在显示器不需要熄屏的情况下(例如看视频时),建议关闭两个应用的熄屏和 DPMS 设置。关于 *XScreenSaver* 设置的更多细节,请见 [XScreenSaver#DPMS and blanking settings](../X%20服务器/XScreenSaver.md#DPMS_and_blanking_settings)。

**xset**

如果上述两个应用的没有运行,熄屏和 DPMS 设置可以使用 *xset* 命令控制,请见 [DPMS#用xset修改DPMS和屏保设定](../../02_系统管理与内核/电源管理/Display%20Power%20Management%20Signaling.md#用xset修改DPMS和屏保设定)。

> **注意：**在某些配置中,存在与熄屏和从熄屏恢复相关的一些问题。请见 。

## 提示和小技巧

### Thunar 和 Xfdesktop 的挂载支持

如果在 Thunar 和桌面中插入的外部驱动器没有显示,而且安装分区显示为已挂载的设备,请安装 [gvfs](https://archlinux.org/packages/?name=gvfs)包。请见 [Udisks#隐藏选中的分区](../../04_硬件与外设/硬件检测和故障排除/Udisks.md#隐藏选中的分区)和 [Thunar#大型外部驱动器的自动挂载](Thunar.md#大型外部驱动器的自动挂载)以获取高级配置选项。

### 屏幕截图

Xfce 有自己的截图工具 [xfce4-screenshooter](https://archlinux.org/packages/?name=xfce4-screenshooter)包。它是 [xfce4-goodies](https://archlinux.org/groups/x86_64/xfce4-goodies/)包组 包组的一部分。

到*应用程序 > 设置 > 键盘*, *应用程序快捷方式*. 添加 `xfce4-screenshooter -f` (或 `-w` 为活动窗口)命令用 `Print` 键截屏。 其他可选参数参见 [xfce4-screenshooter(1)](https://man.archlinux.org/man/xfce4-screenshooter.1)。

此外，也可用其他独立的截图程序如 [scrot](../../06_软件与应用/多媒体/屏幕捕获.md#scrot)。

### 禁用终端 F1 和 F11 快捷方式

XFCE 终端下 F1 和 F11 分别被绑定给了帮助和全屏，给一些程序造成了冲突。要禁用这些快捷方式，创建或修改下面的配置文件然后注销重新登录。F10 可以在设置里更改。

```
~/.config/xfce4/terminal/accels.scm
```

```
(gtk_accel_path "<Actions>/terminal-window/fullscreen" "")
(gtk_accel_path "<Actions>/terminal-window/contents" "")
```

#### 终端的颜色主题和调色板

可以在首选项的外观标签下修改终端主题颜色和调色板。这些色彩可用于多大数控制台程序如[Emacs](../../07_开发与工具/Emacs/Emacs.md)，[Vi](../../07_开发与工具/文本编辑器/Vi.md) 等。 它们的设置单独存储在每个用户的 `~/.config/xfce4/terminal/terminalrc` 文件中。 还有更多主题可供选择。论坛下 [Terminal Colour Scheme Screenshots](https://bbs.archlinux.org/viewtopic.php?id=51818) 有数百的更多终端配色方案。

#### 修改默认颜色主题

XFCE 的 `extra/terminal` 包使用了较暗的颜色使得文字在默认的黑色背景下很难阅读并会使人感到不适，请把以下文字写入到 terminalrc 文件中来使用一个较明亮的颜色主题, 它会在一直在较暗的终端背景下可见。

```
~/.config/xfce4/terminal/terminalrc
```

```
ColorPalette5=#38d0fcaaf3a9
ColorPalette4=#e013a0a1612f
ColorPalette2=#d456a81b7b42
ColorPalette6=#ffff7062ffff
ColorPalette3=#7ffff7bd7fff
ColorPalette13=#82108210ffff
```

### 终端 Tango 颜色主题

用你喜欢的编辑器打开 `~/.config/xfce4/terminal/terminalrc` 并加入下列内容(用下列内容替换原内容):

```
ColorForeground=White
ColorBackground=#323232323232
ColorPalette1=#2e2e34343636
ColorPalette2=#cccc00000000
ColorPalette3=#4e4e9a9a0606
ColorPalette4=#c4c4a0a00000
ColorPalette5=#34346565a4a4
ColorPalette6=#757550507b7b
ColorPalette7=#060698989a9a
ColorPalette8=#d3d3d7d7cfcf
ColorPalette9=#555557575353
ColorPalette10=#efef29292929
ColorPalette11=#8a8ae2e23434
ColorPalette12=#fcfce9e94f4f
ColorPalette13=#72729f9fcfcf
ColorPalette14=#adad7f7fa8a8
ColorPalette15=#3434e2e2e2e2
ColorPalette16=#eeeeeeeeecec
```

### 终端下用鼠标中键打开 URL

升级到 0.8 后鼠标中键的默认行为改成了粘贴到光标。 要改回元行为，修改 `${XDG_CONFIG_HOME}/xfce4/terminal/terminalrc`（默认 `XDG_CONFIG_HOME=${HOME}/.config`）

```
${XDG_CONFIG_HOME}/xfce4/terminal/terminalrc
```

```
[Configuration]
MiscMiddleClickOpensUri=TRUE
```

### 终端中的 env-modules 自动补全

[env-modules](https://aur.archlinux.org/packages/env-modules/)AUR 和 [env-modules-tcl](https://aur.archlinux.org/packages/env-modules-tcl/)AUR 软件包提供了为 login shell 提供了 shell 自动补全。但是 `xfce4-terminal` 默认并不被当作 *login* 会话。要启用环境变量模块的自动补全,在*首选项*中勾选相应的复选框或者在 `~/.config/xfce4/terminal/terminalrc` 中将 `CommandLoginShell` 改为 `TRUE`。

### 颜色管理

Xfce 本身没有颜色管理的功能支持。  查看 ICC profiles 寻找替代。

### 多显示器

Xfce 有着多显示器的支持。可以在*应用 > 设置 > 显示*对话框中配置。

在*高级*标签页中可以保存用于不同显示器的配置并且当连接的显示器更改时自动应用相应的配置。

详情请见官方文档的 [display](https://docs.xfce.org/xfce/xfce4-settings/display) 文章。

或者使用 [arandr](https://archlinux.org/packages/?name=arandr)包 来简单地用 xrandr 格式的命令管理显示器配置,而且可以分配 XFCE 键盘快捷键。

### SSH 代理

默认 Xfce 4.10 会在会话启动时试着按顺序打开 gpg-agent 或 ssh-agent。要禁用的话，运行如下命令：

```
xfconf-query -c xfce4-session -p /startup/ssh-agent/enabled -n -t bool -s false
```

若 gpg-agent 安装了也要启动 ssh-agent 的话运行：

```
xfconf-query -c xfce4-session -p /startup/ssh-agent/type -n -t string -s ssh-agent
```

要使用 [GNOME Keyring](../GNOME/GNOME_Keyring.md)，在*设置*里的*会话和启动*的*高级*页选中*桌面启动时启动 GNOME 服务*。这还会禁止 gpg-agent 和 ssh-agent 的启动。

参见：[https://docs.xfce.org/xfce/xfce4-session/advanced](https://docs.xfce.org/xfce/xfce4-session/advanced)

### 滚动时不获得焦点

在*设置 > 窗口管理器微调 > 辅助功能*下取消*按下任意鼠标按钮时提升窗口*。

### 修改窗口管理器 modifier

默认的 modifier 是 `Alt`。可以用 *xfconf-query*更改。比如说下面的命令会将其改为 `Super`：

```
$ xfconf-query -c xfwm4 -p /general/easy_click -n -t string -s "Super"
```

严格地说，并不支持多 modifier。可是实际可以用 `><` 把多个键分隔起来。比如下面的命令会把 modifier 改为 `Ctrl+Alt`：

```
$ xfconf-query -c xfwm4 -p /general/easy_click -n -t string -s "Ctrl><Alt"
```

### 设置触摸板两指单击为鼠标中键

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**更简单的方法 [Touchpad Synaptics](../../04_硬件与外设/输入设备/Touchpad%20Synaptics.md)（在[Talk:Xfce](https://wiki.archlinuxcn.org/wiki/Talk:Xfce)讨论）

如果你想让触摸板两指单击识别为鼠标中键，创建或更改如下文件：

```
~/.config/xfce4/xfconf/xfce-perchannel-xml/pointers.xml
```

```
<channel name="pointers" version="1.0">
  <property name="SynPS2_Synaptics_TouchPad" type="empty">
    <property name="Properties" type="empty">
      <property name="Synaptics_Tap_Action" type="array">
        <value type="int" value="0"/>
        <value type="int" value="0"/>
        <value type="int" value="0"/>
        <value type="int" value="0"/>
        <value type="int" value="1"/>
        <value type="int" value="2"/>
        <value type="int" value="3"/>
      </property>
    </property>
  </property>
</channel>
```

数组中的2就是鼠标中键。

### 限制亮度划块的最小亮度

在一些显示器下亮度等级设为0后背光会完全关掉。`xfce4-power-manager 1.3.2` 有一个新的隐藏选项可以调节最小亮度。用 xfconf4 添加一个名为 `brightness-slider-min-level` 的整数键，将其改为合适的最小亮度值。

### 添加个人资料图片

要为每个用户添加显示在 whisker 菜单中的资料图片,将一张 96x96 的 PNG 图片放在每个用户的家目录中,并命名为 `.face`。举个例子,PNG 文件 `/home/*bob*/.face` 是用于用户 *bob* 的资料图片。

像 [GIMP](../../06_软件与应用/图片/GIMP.md) 这样的图像编辑软件可以将你喜欢的图像转换到 96x96 分辨率。

### 电源管理器插件标签

Xfconf 的 `int` 类设置 `show-panel-label` 控制电源管理器的标签,它可以被配置成不同的标签格式:设置成 0 (无标签), 1 (百分比), 2 (剩余时间) 或 3 (百分比和剩余时间)。

它也可以通过电源管理器的图形界面中的 *Properties > Show label* 来配置。

### 在快捷键中使用 Windows (Super) 键

Super 键被当作一个附加键,就像 Ctrl 和 Alt,而不是产生按键。将动作分配给它会阻止你在其他快捷键中使用它,因为它在触发你分配给它的其他动作的同时还会触发该动作。

要解决这个问题,并让它在快捷键中更有用,安装应用 [xcape](https://archlinux.org/packages/?name=xcape)包。这个应用让你可以将附加键配置成在按下和松开时充当其他键。

接下来,前往 `设置 > 键盘 > 应用快捷键` 并将一个为使用的按键组合,例如 Alt-F1,分配给应用菜单 (或是任何你希望按下 Super 键时执行的动作)。测试一下是否工作。 接下来,使用 [xcape](https://archlinux.org/packages/?name=xcape)包 将 Alt-F1 分配给 Super 键:

```
$ xcape -e 'Super_L=Alt-L|F1'
```

检查 Super 键是否执行你分配给 Alt-F1 的动作。

如果一切正常,将它设置成一个自启动动作;前往 `设置 > 会话与启动 > 应用自启动`,按下添加按钮并输入上述命令,让它在每次启动 Xfce 都运行(如果 xcape 已经安装了,检查有没有相同的条目被注册)。

现在,你可以在快捷键中随意使用 Super 键。 举个例子:在 `窗口管理器 > 键盘` 中,你可以使用 Super 和上下键来提升或降低窗口。

## 故障排除

### 桌面图标顺序被打乱

在一些情况下（比如打开面板设置对话框时）桌面图标的顺序会被改变。这是因为其顺序是由在 `~/.config/xfce4/desktop/` 下的文件所决定的，而每次改变桌面（添加删除图标或改变位置）就会生成一个新文件，导致了可能的冲突。

要解决这个问题，打开那个目录然后只留下一个正确的配置文件。可以通过其内容来判别到底是哪个文件。里面行数定义为 `row 0`，列数定义为 `col 0`。因而如下的文件内容：

```
[Firefox]
row=3
col=0
```

意为火狐在最左边第四行。

### GTK 主题在多显示器下不正常

一些配置工具会损坏 displays.xml 从而导致*应用程序 > 设置 > 外观*无法工作。要解决问题，删除 `~/.config/xfce4/xfconf/xfce-perchannel-xml/displays.xml` 然后重新设置。

### 右键菜单没有图标

> **注意：**GConf 已被不建议使用，但这个方法还有效。

有时一些程序，包括用 [Qt](../../07_开发与工具/控件工具集/Qt.md) 写的程序的右键菜单没有图标。这个问题只发生在 Xfce 下。运行如下命令:

```
$ gconftool-2 --type boolean --set /desktop/gnome/interface/buttons_have_icons true
$ gconftool-2 --type boolean --set /desktop/gnome/interface/menus_have_icons true
```

### 修改设置不生效

如果你正在运行一个单独的 [Xsettings 守护进程](../../01_安装与包管理/配置文件/Xsettingsd.md),可能会有一些配置不生效。通过移除或注释对应行禁用它然后重启 [Xorg](../X%20服务器/Xorg.md)。

### NVIDIA 和 xfce4-sensors-plugin

要探测 NVIDIA gpu 的温度，需要安装 [libxnvctrl](https://archlinux.org/packages/?name=libxnvctrl)包 并且用 [ABS](../../00_关于与社区/关于%20Arch/Arch%20构建系统.md) 重新编译 [xfce4-sensors-plugin](https://archlinux.org/packages/?name=xfce4-sensors-plugin)包 软件包。或者改安装 [xfce4-sensors-plugin-nvidia](https://aur.archlinux.org/packages/xfce4-sensors-plugin-nvidia/)AUR。

### NVIDIA 显卡和多显示器在启动时黑屏

使用 [NVIDIA](../图形/NVIDIA.md),多显示器和 [NVIDIA/Troubleshooting#Avoid screen tearing](../图形/NVIDIA_故障排除.md#Avoid_screen_tearing) 可能会导致在启动 Xfce 时黑屏。屏幕的位置与文件 `/etc/X11/xorg.conf` 和 `~/.config/xfce4/xfconf/xfce-perchannel-xml/displays.xml` 冲突。删除 `displays.xml` 文件可以修复这个问题。

```
$ rm ~/.config/xfce4/xfconf/xfce-perchannel-xml/displays.xml
```

### 面板小程序挤在左边

添加一个分割符并选中”扩展”属性。 

### 首选应用程序没有效果

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** [xorg-xprop](https://archlinux.org/packages/?name=xorg-xprop)包 should only be needed to activate the [classic fallback](https://github.com/freedesktop/xdg-utils/blob/d11b33ec7f24cfb1546f6b459611d440013bdc72/scripts/xdg-utils-common.in#L318), a "last effort" attempt at detecting Xfce. This should be fixable by setting `XDG_CURRENT_DESKTOP=XFCE` \[sic\], if modern Xfce does not already do so.（在 [Talk:Xfce](https://wiki.archlinuxcn.org/wiki/Talk:Xfce) 中讨论）

大多数程序依赖 [xdg-open](../Freedesktop.org/Xdg-utils.md) 来用首选应用程序打开想要的文件和 URL。

要让 xdg-open 和 xdg-settings 与 Xfce 桌面环境检测和整合，需要安装 [xorg-xprop](https://archlinux.org/packages/?name=xorg-xprop)包 包。

如果不这么做的话，在 exo-preferred-applications 设置的首选应用程序就没有效果。 安装后 *xdg-open* 会检测到你正在运行 Xfce，从而把调用全转交给 *exo-open*。它会正常地使用你的首选应用程序设置。

要确认 xdg-open 是否正常工作，询问 *xdg-settings* 默认浏览器的返回结果：

```
# xdg-settings get default-web-browser
```

如果输出的是：

```
xdg-settings: unknown desktop environment
```

这说明 xdg-open 没有检测出你的桌面环境。原因很可能在没有安装 [xorg-xprop](https://archlinux.org/packages/?name=xorg-xprop)包 包。

### 恢复默认设置

如果出于某些愿意需要恢复默认设置，重命名 `~/.config/xfce4-session/` 和 `~/.config/xfce4/`

```
$ mv ~/.config/xfce4-session/ ~/.config/xfce4-session-bak
$ mv ~/.config/xfce4/ ~/.config/xfce4-bak
```

重新登录后就会起效果。若登录时出现 `Unable to load a failsafe session`，见 [#会话失败](#会话失败)一节。

### 会话失败

包括以下症状：

- 鼠标变成了叉号甚至没有鼠标
- 没有标题栏，无法关闭窗口
- (`xfwm4-settings`) 不起动，报 `These settings cannot work with your current window manager (unknown)`
- [显示管理器](../图形用户界面/显示管理器.md)报错，例如 `No window manager registered on screen 0`。
- `Unable to load a failsafe session`

```
Unable to load a failsafe session.
Unable to determine failsafe session name.  Possible causes: xfconfd is not running (D-Bus setup problem); environment variable $XDG_CONFIG_DIRS is set incorrectly (must include "/etc"), or xfce4-session is installed incorrectly.
```

重启可能会解决问题，但原因也可能在于错误的会话。删除会话目录：

```
$ rm -r ~/.cache/sessions/
```

还有就是保证 `$HOME` 的对应目录是被启动 `xfce4` 的用户所拥有的。见 [Chown](../../07_开发与工具/命令行/文件权限与属性.md)。

### 标题栏字体使 xfce4-title 崩溃

安装 [ttf-droid](https://archlinux.org/packages/?name=ttf-droid)包 和 [ttf-dejavu](https://archlinux.org/packages/?name=ttf-dejavu)包。参见 [FS#44382](https://bugs.archlinux.org/task/44382)。

### 笔记本盖设置没有效果

你可能会发现 Xfce4 电源管理器的合盖设置没有效果，不论什么设置合盖后总是挂起。这是因为默认 logind 而非电源管理器接管了合盖的事件。要更改该行为，运行命令：

```
$ xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/logind-handle-lid-switch -s false
```

> **注意：**有些情况下当你更改合盖动作和挂起时锁定的设置时 `logind-handle-lid-switch` 设置会又变成 true，详见 。你需要再手动把它设成 `logind-handle-lid-switch` false。

### 切换用户动作按钮不可用

*切换用户*动作按钮假设*gdmflexiserver*程序(由 [GDM](../GNOME/GDM.md) 提供)存在。因此如果没有使用 GDM,按钮就不可用,请见 [upstream bug report](https://bugzilla.xfce.org/show_bug.cgi?id=9307) 。

可用的解决方案是在 `/usr/bin` 中创造一个名为 *gdmflexiserver* 的可执行的脚本,该脚本调用[显示管理器](../图形用户界面/显示管理器.md)提供的欢迎程序切换命令。

- 对于 LXDM - [LXDM#自动用户和切换用户](../LXDE/LXDM.md#自动用户和切换用户)。
- 对于 LightDM - [LightDM#User switching](../../06_软件与应用/Canonical/LightDM.md#User_switching)。

### .Xresources 中的宏不起作用

Xfce 使用 `xrdb` 加载 `$HOME/.Xresources` 文件,但是带有 {{ic}-nocpp}} 选项来跳过预处理。对于正常工作的宏,将 `/etc/xdg/xfce4/xinitrc` 复制到 `$HOME/.config/xfce4` 目录,并在产生的文件中移除 `xrdb` 的 `-nocpp` 选项。请见[相关论坛话题](https://bbs.archlinux.org/viewtopic.php?id=230696)。

### 光标主题在登录时不会改变

确保系统范围内的 XDG 光标设置成你想要的光标主题--请见[光标主题#XDG 规范](../X%20服务器/光标主题.md#XDG_规范)。

### Mousepad 编辑器菜单栏不可见

运行下面这条命令使它可见:

```
$ gsettings set org.xfce.mousepad.preferences.windowmenubar-visible true
```

### 垃圾桶图标不可见而且垃圾桶小程序不工作

垃圾桶需要可选依赖 gvfs 才能工作。安装 [gvfs](https://archlinux.org/packages/?name=gvfs)包 并重启系统。

### 桌面变成灰色而且所有桌面图标消失

删除 `~/.cache/sessions`:

```
$ rm -rf ~/.cache/sessions
```

之后重启 Xfce。

### thunar无法解压文件

安装 [xarchiver](https://archlinux.org/packages/?name=xarchiver)包

### Parole无法播放视频

安装 [gst-libav](https://archlinux.org/packages/?name=gst-libav)包

### thunar界面不为中文

通常发生在用户使用[“为图形界面配置中文 locale”](../../00_关于与社区/本地化/简体中文本地化.md)本地化方案出现，在`~/.xprofile` 、`~/.xinitrc` 文件追加

```
dbus-update-activation-environment --systemd LANG
```

## 另见

- [Xfce - 文档](https://docs.xfce.org/)
- [Xfce - Wiki](https://wiki.xfce.org)
- [Xfce - Tour](https://xfce.org/about/tour)
- [Wikipedia:Xfce](https://en.wikipedia.org/wiki/Xfce)
- [Xfce-Look](https://www.xfce-look.org/) - 主题，壁纸等
- [Xfce Wikia](https://xfce.wikia.com/wiki/Main_Page)
