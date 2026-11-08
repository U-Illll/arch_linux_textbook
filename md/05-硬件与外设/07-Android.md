# 第 7 章　Android

> 在 Arch Linux 上接触 Android，无非两种做法：把设备接上来调试，或把 Android 环境搬到本机。本章两头都覆盖：《Android 调试桥》讲安装、使用与排错，《Anbox》与《Waydroid》讲在 Arch 上装 Android 镜像，其中后者一节更详尽，《Android》则收拢同步、应用开发、编译、刷机等零散话题，也讲到在 Arch Linux 运行 Android 应用。
> 
> 建议按《Android 调试桥》→《Waydroid》的顺序读，这两节必读；《Anbox》可只翻安装与网络两部分作对照，《Android》按需查阅，做开发或刷机时再回来。最容易踩的坑是把"装完包"当成终点——Anbox 与 Waydroid 都把准备工作和网络单独成节，跳过常常连不上网；调试桥一节的故障排除也值得先扫一眼。

<!-- 本章 4 节，共 75 KB -->

## 7.1　Android 调试桥

*难度：进阶 ｜ 篇幅：8.7 KB ｜ 所属：Android*

将要讲到：安装、使用、用 ADB 构建的工具、故障排除

[Android 调试桥](https://developer.android.com/studio/command-line/adb)（ADB）是一种命令行工具，可用于安装、卸载和调试应用程序，传输文件和访问设备的 shell。

## 安装

ADB 是平台-工具 [SDK 软件包](Android.md#SDK_packages)和 [android-tools](https://archlinux.org/packages/?name=android-tools)包 软件包的一部分。

## 使用

### 连接设备

> **提示：**
>
> - 对于部分设备，你可能需要先在设备上启用 [MTP](../移动设备/媒体传输协议.md)，ADB 才能正常工作。其他一些设备需要启用 PTP 模式才能工作。
> - 许多设备的 [udev](../硬件检测和故障排除/Udev.md) 规则都包含在 [libmtp](https://archlinux.org/packages/?name=libmtp)包 中，所以如果已安装该包，可能不再需要下面的部分步骤。
> - 确保你的 USB 线能够同时进行充电和数据传输。许多与移动设备捆绑的 USB 线不包含 USB 数据针。

要在 Arch 下通过 ADB 连接实体设备或手机，你必须：

1.  如果希望将设备连接到适当的 `/dev/` 项，可能需要安装 [android-udev](https://archlinux.org/packages/?name=android-udev)包。
2.  将你的 Android 设备通过 USB 插入到电脑。
3.  在你的手机或设备上启用 USB 调试功能：
    - Jelly Bean（4.2）及更新的版本：进入*设置>关于手机*点击*版本号*七次，直到出现一个弹出窗口说你已经成为开发者。在较新的 Android 操作系统版本上，版本号可能在一个名为*软件信息*的菜单中。然后进入*设置>开发者>USB 调试*并启用它。设备会要求允许有特定指纹的电脑进行连接。勾选*永久允许*选项会把 `~/.android/adbkey.pub` 复制到设备的 `/data/misc/adb/adb_keys` 位置中。
    - 较早的版本：这通常是在*设置>应用程序>开发>USB 调试*中进行。检查该选项后重新启动手机，以确保 USB 调试被启用。

如果 [ADB 识别你的设备](#检测设备)（`adb devices` 为 `“device”而不是“unauthorized”`，或者它在 IDE 中可见并可访问，那么你已成功连接到手机。否则请看下面的说明。

### 识别设备标识符

每个 Android 设备都有 USB 制造商/产品标识符。以 HTC Evo 为例：

```
vendor id: 0bb4
product id: 0c8d
```

插入你的设备并执行：

```
$ lsusb
```

```
...
Bus 002 Device 006: ID 0bb4:0c8d High Tech Computer Corp.
...
```

### 添加 udev 规则

如果你的设备没有包含在 [android-udev](https://archlinux.org/packages/?name=android-udev)包（或 [android-udev-git](https://aur.archlinux.org/packages/android-udev-git/)AUR），使用以下模板创建一个自定义的 [udev 规则](../硬件检测和故障排除/Udev.md#udev_规则)，将 `[VENDOR ID]` 和 `[PRODUCT ID]` 替换为[你的设备标识符](#识别设备标识符)。

```
/etc/udev/rules.d/51-android.rules
```

```
SUBSYSTEM=="usb", ATTR{idVendor}=="[VENDOR ID]", MODE="0660", GROUP="adbusers", TAG+="uaccess"
SUBSYSTEM=="usb", ATTR{idVendor}=="[VENDOR ID]", ATTR{idProduct}=="[PRODUCT ID]", SYMLINK+="android_adb"
SUBSYSTEM=="usb", ATTR{idVendor}=="[VENDOR ID]", ATTR{idProduct}=="[PRODUCT ID]", SYMLINK+="android_fastboot"
```

然后[重新加载 udev 规则](../硬件检测和故障排除/Udev.md#加载新规则)。

### 检测设备

设置好 udev 规则后，拔掉你的设备并重新插入。

之后执行：

```
$ adb devices
```

```
List of devices attached
HT07VHL00676    device
```

如果 *adb* 在你把设备插回去之后仍然没有检测到设备，请以 root 身份杀死并重新启动 *adb* 服务，然后再次检查设备。

```
# adb kill-server
# adb start-server
$ adb devices
```

如果 *adb devices* 仍然在你连接的设备显示*未授权*，请确保已授予该设备调试权限。当你物理连接到该设备时，应该出现一个*允许 USB 调试？*的对话框。选择*总是允许...*，然后点击*确定*。如果从未出现过该对话框，请尝试点击*设置*\>*开发人员选项*\>*撤销 USB 调试授权*,然后点击*确定*，并重复本节的步骤。如果你仍然没有看到*允许 USB 调试？*对话框，而且设备被列为未授权，那么进入设备的*开发者选项*，首先取消 *USB 调试*，然后再次勾选。

### 传输文件

现在可以使用 adb 在设备和电脑之间传输文件。一般来说，安卓设备的文件在/sdcard/目录下。

要向设备传输文件，请用：

```
$ adb push what-to-copy where-to-place
```

要从设备传输文件，请用：

```
$ adb pull what-to-pull where-to-place
```

也可参见[#用 ADB 构建的工具](#用_ADB_构建的工具)。

### 备份和恢复

> **注意：*****adb backup/restore*** 已被弃用，可能会在未来被移除。

你也可以用*adb*备份和恢复你的设备。此外，不需要 root 就可以完成这个操作。下面的命令会将你的设备备份到单个文件，可用于后续恢复。

创建备份的命令是：

```
$ adb backup -apk -shared -all -f backupFileName.ab
```

命令参数列表为：

```
adb backup [-f <file>] [-apk|-noapk] [-shared|-noshared] [-all] [-system|nosystem] [<packages...>]
```

然后在设备的显示屏上确认这个操作，并提供一个密码，无论之前是否已经设置备份密码。

恢复以前备份的命令为：

```
$ adb restore backupFileName.ab
```

> **注意：**请记住，恢复操作会将你的设备内容替换成备份内容。

## 用 ADB 构建的工具

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**请使用 [Template:App](https://wiki.archlinuxcn.org/wzh/Template:App) 格式。（在[Talk:Android 调试桥](https://wiki.archlinuxcn.org/wiki/Talk:Android_%E8%B0%83%E8%AF%95%E6%A1%A5)讨论）

- [adbfs-rootless-git](https://aur.archlinux.org/packages/adbfs-rootless-git/)AUR – 挂载一台通过 USB 连接的 Android 设备。
- [adb-sync-git](https://aur.archlinux.org/packages/adb-sync-git/)AUR – 一个通过 ADB 协议在 PC 和 Android 设备之间同步文件的工具。
- [androidscreencast-bin](https://aur.archlinux.org/packages/androidscreencast-bin/)AUR – 通过 ADB 从 PC 查看和控制你的 Android 设备。
- [logcat-color](https://aur.archlinux.org/packages/logcat-color/)AUR – 一种丰富多彩、高度可配置，可替代标准 `adb logcat` 命令。
- [scrcpy](https://archlinux.org/packages/?name=scrcpy)包 – 显示和控制你的 Android 设备。
- [qtscrcpy](https://aur.archlinux.org/packages/qtscrcpy/)AUR – Android 实时显示控制软件。
- [adbmanager-bin](https://aur.archlinux.org/packages/adbmanager-bin/)AUR - 该出现设计用于可视化并简化 ADB 服务器管理及 Android 手机连接。它可以让你监控 adb 服务状态，进行管理并控制已连接的设备。它也可以管理你的手机：使用名称搜索已安装的软件包，安装/删除 APK，截屏，重启（正常重启，重启到 Bootloader 或恢复模式）及关机。对于高阶用户，它同时提供了 Android Shell 终端和 SD 卡管理器。

## 故障排除

### 空设备列表

可能的原因有：

- 未启用 USB 调试。参见[启用 USB 调试](#连接设备)。
- USB 线只提供充电功能，没有数据传输功能。

### 无权限错误

如果设备显示“无权限”标签，它可能与 [android-udev](https://archlinux.org/packages/?name=android-udev)包 收集的制造商/产品标识符不同。

例如，当设备使用定制 ROM，或者从 MTP 模式切换到 USB 连接模式、sideload 和/或 fastboot 模式时，就会发生这种情况。 用 [lsusb](#识别设备标识符) 验证实际设备标识符，并[#添加 udev 规则](#添加_udev_规则)。

### 守护进程出现权限不足报错

如果在运行 *adb* 时出现以下报错：

```
E adb     : usb_libusb.cpp:571 failed to open device: Access denied (insufficient permissions)
```

请检查你是否在 `adbusers` 用户组中。

## 7.2　Anbox

*难度：进阶 ｜ 篇幅：17.2 KB ｜ 所属：Android*

将要讲到：准备工作、安装、安装 Android 镜像、网络、Usage、Tips and tricks

> [![](https://wiki.archlinuxcn.org/wzh/images/9/9c/User-trash-full.svg.png)](https://wiki.archlinuxcn.org/wzh/File:User-trash-full.svg.png)**本文已被弃用。**
>
> **本文所述教程或工具由于已经过时而被弃用，请使用其他替代方案：** [Waydroid](Waydroid.md) 等。 (在[Talk:Anbox](https://wiki.archlinuxcn.org/wiki/Talk:Anbox)讨论)

> [![](https://wiki.archlinuxcn.org/wzh/images/2/26/Tango-preferences-desktop-locale-modified.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale-modified.png)**这篇文章或章节的[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing#Translating)质量不佳。**
>
> **原因：**部分内容可能由机器翻译。（在 [Talk:Anbox#](https://wiki.archlinuxcn.org/wiki/Talk:Anbox) 中讨论）

> **注意：**[Anbox 已经停止开发](https://github.com/anbox/anbox/blob/2102e1be999984dbfb25d1ddeaf42179de071020/README.md)，开发者建议使用 [Waydroid](Waydroid.md) 。

Anbox 是一个可以在 GNU/Linux 发行版上运行 Android App 的一个 [容器](../../08_安全与加密/沙箱/Linux%20容器.md)。

## 准备工作

> **注意：**Anbox 的 dkms 模块在内核版本 ≥5.7 下不会工作。请按照以下说明进行操作。 对于较老的内核请参阅 [较老的内核](#较老的内核)\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Project:%E7%A4%BE%E7%BE%A4%E9%A6%96%E9%A1%B5#损坏的链接)：无效的章节\]。

您需要运行带有 ashmem 和 binder 模块的内核。但是它们不是 Arch Linux 的默认内核 ( linux ) 的一部分，所以您需要安装一个包含这些模块的内核。

您可能还需要配置引导加载程序以使用不同的内核。

您有多种选择：

### 使用 Linux-Zen

[linux-zen](https://archlinux.org/packages/?name=linux-zen)包 内核自带 Anbox 需要的模块。这可能是最简单的方式，因为您不必编译内核并且版本会定期更新。

> **警告：**根据 linux 内核中的这个[commit](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=721412ed3d819e767cac2b06646bf03aa158aaec)，从 5.18 开始的版本中 ashmem 被 memfd 取代。 然而，anbox 开发人员并没有修复他们的软件以使用 memfd，因此 anbox 很可能在一段时间内根本无法与 linux-zen 内核一起工作。

### 编译内核

当然，您也可以自己重新编译 [linux](https://archlinux.org/packages/?name=linux)包 内核。请阅读 [Kernel#编译](../../02_系统管理与内核/内核/内核.md#编译) and [配置模块](#配置模块)。

您还可以从[AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md)里构建一个已经包含特定补丁的内核包, 请参阅 [AUR search "linux+anbox"](https://aur.archlinux.org/packages/?K=linux+anbox).

#### 配置模块

模块同样也可以被编译进内核里 (`y`), 模块里 (`m`), 或者不编译 (`n`).此外，并非配置中的所有组合都是可能的，某些选项将需要其他选项。

下面的的内核配置将把 ashmem 和 binder 编译为模块，而最后一个选项指定在 `/dev/` 加载 binder 模块时在目录中创建三个设备。

```
CONFIG_ASHMEM=m
CONFIG_ANDROID=y
CONFIG_ANDROID_BINDER_IPC=m
CONFIG_ANDROID_BINDERFS=n
CONFIG_ANDROID_BINDER_DEVICES="binder,hwbinder,vndbinder"
```

如果您要从 AUR 构建内核，那么您可以参考下面的步骤:

1.  运行 `makepkg --nobuild`, 它将下载源代码，验证并提取。最后运行 `prepare()` 函数.
2.  编辑位于内核目录底部的 `.config` 文件。
3.  在 `prepare()` 函数的末尾可能是一个命令，它使用来自配置的信息重新生成 makefile，也可能是 `make olddefconfig`. 将它移动到 `build()` 函数内，或自己执行一遍。
4.  运行 `makepkg --noextract`, 它将在 `makepkg --nobuild` 停止的地方继续。

#### 使用 binderfs 进行配置

并不是每个人都对 Linux 中的 binder 模块感到满意。为了解决这些问题，binderfs诞生了。编译内核时必须在新旧两种方式之间进行选择。比如下面的选项就是针对于 binderfs 的。

内核源代码还提供了一个简单的脚本来设置配置选项。它不会进行依赖项检查。当您在 `.config` 文件所在的同一目录中时，执行以下命令:

```
scripts/config --module  CONFIG_ASHMEM
scripts/config --enable  CONFIG_ANDROID
scripts/config --enable  CONFIG_ANDROID_BINDER_IPC
scripts/config --enable  CONFIG_ANDROID_BINDERFS
scripts/config --set-str CONFIG_ANDROID_BINDER_DEVICES ""
```

当您正在从从 AUR 构建内核时，这个脚本很方便，因为只需要在 PKGBUILD 中的正确位置插入这些命令就足够了。

### 启动新内核

请参阅引导加载程序的 wiki 页面了解如何使用新内核引导。您应该在启动 Anbox 之前切换到新的内核。

## 安装

### 加载模块

Anbox 不会自动加载模块，但是如果您在使用 linux-zen，就可以跳过此步骤。

要临时加载，请使用：

```
# modprobe -a binder-linux ashmem-linux
```

要始终在启动时加载它们，可以通过 `systemd-modules-load.service` 文件来实现。 为此，请在 `/etc/modules-load.d/` 创建一个包含以下内容的 anbox.conf文件：

```
/etc/modules-load.d/anbox.conf
```

```
ashmem_linux
binder_linux
```

### 挂载 binderfs

> **注意：** [linux-zen](https://archlinux.org/packages/?name=linux-zen)包 内核必须挂载 binderfs。

如果您的内核正在使用 binderfs，则还有一个步骤要做：挂载 binderfs 文件。

首先，您需要一个挂载点。默认情况下，Anbox 将使用 `/dev/binderfs`。 您可以直接创建该目录，但它会在启动时被删除。所以这里建议使用 [systemd-tmpfiles](../../01_安装与包管理/初始化/Systemd.md) 实现在启动时创建 `/dev/binderfs`。为此，您需要在 `/etc/tmpfiles.d/` 创建一个包含以下内容的文件：

```
/etc/tmpfiles.d/anbox.conf
```

```
d! /dev/binderfs 0755 root root
```

其次您需要挂载 binder 文件系统。 这可以通过：

```
# mount -t binder none /dev/binderfs
```

如果想要在启动时挂载它，只需要在 [fstab](../../01_安装与包管理/启动过程/Fstab.md) 中添加下面这一行。

```
/etc/fstab
```

```
none                         /dev/binderfs binder   nofail  0      0
```

> **警告：**添加 `nofail` 参数后，当您启动没有 binderfs 支持的内核时，您将无法进入恢复 shell。

## 安装 Android 镜像

> **注意：**现在的镜像均已过时 (基于 Android 7.1). 目前，上游没有可用的更新的镜像。另请参阅 Talk:Anbox#Anbox-Images outdated.

选择一个镜像来安装:

- [anbox-image](https://aur.archlinux.org/packages/anbox-image/)AUR (官方镜像)
- [anbox-image-houdini](https://aur.archlinux.org/packages/anbox-image-houdini/)AUR (包含 Houdini)
- [anbox-image-houdini-rooted](https://aur.archlinux.org/packages/anbox-image-houdini-rooted/)AUR (包含 Houdini 和 SuperSU)
- [anbox-image-gapps](https://aur.archlinux.org/packages/anbox-image-gapps/)AUR (包含 Houdini 和 OpenGApps)
- [anbox-image-gapps-rooted](https://aur.archlinux.org/packages/anbox-image-gapps-rooted/)AUR (包含 Houdini, OpenGApps 和 SuperSU)
- 您也可以通过在 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 内搜索 [anbox-image](https://aur.archlinux.org/packages/?K=anbox-image)找到更多的镜像。

> **提示：**
>
> - 通常，在 x86_64 计算机上运行 ARM 应用程序需要 Houdini。
> - 如果您使用 GMS，OpenGApps 是一个不错的选择。

### 安装 Anbox

安装 [anbox-git](https://aur.archlinux.org/packages/anbox-git/)AUR。

然后, start/enable `anbox-container-manager.service`.

恭喜您现在已经完成了使用 Anbox 所需的所有步骤！在您的桌面环境菜单中，您应该会在 Others 类别中找到 anbox。 第一次启动将花费很长的时间，anbox session-manager将会被自动打开。当然，您也可以手动执行 `anbox session-manager` 。如果 anbox 崩溃并且您想报告或修复这个错误，这将**非常**有用。只需启动它，然后等到它崩溃。

还有一个给用户使用的*systemd* unit，可用于在开机时启动 session-manager；如果你想这么干，只需要 start/enable `anbox-session-manager.service`。该 unit 的一个优点是可以在崩溃时找到日志：

```
$ journalctl --user -b -u anbox-session-manager
```

但是请记住，如果您打开一个 Android app 时崩溃崩溃了，它将会再启动一个独立于*systemed*的 session-manager。

## 网络

### 使用 NetworkManager

如果您正在使用 [NetworkManager](../../06_软件与应用/红帽/NetworkManager.md) ，则可以使用它来配置网络。

执行以下命令来创建 bridge connection：

```
$ nmcli con add type bridge ifname anbox0 -- connection.id anbox-net ipv4.method shared ipv4.addresses 192.168.250.1/24
```

- `ifname anbox0` 指定网桥接口名称，在本例中为 `anbox0`. 不要改变这个名称，因为 Anbox 仅会在名为 `anbox0` 的网桥接口。
- `connection.id anbox-net` 当其运行于 [NetworkManager](../../06_软件与应用/红帽/NetworkManager.md)时使用 `anbox-net` 这个名称. 你可随愿更改.
- `ipv4.method shared` 指示 [NetworkManager](../../06_软件与应用/红帽/NetworkManager.md) 来创建一个NAT网络，并根据系统的路由规则对出站数据包进行路由。为此，在 [dnsmasq](https://archlinux.org/packages/?name=dnsmasq)包 包完备. [dnsmasq](../../03_网络与服务/DHCP/Dnsmasq.md) 不需要配置，也不需要作为systemd服务启动, 其将作为NetworkManager的后端使用——如果它不可用，这一步将无提示地失败。. 如果你想让Anbox直接连接一个特定的网络，你也可以让这一个参数和`ipv4.addresses`参数留空，参见 [Network bridge#With NetworkManager](../../03_网络与服务/网络/网桥.md#With_NetworkManager)。如果你想这么做，你还必须把在`anbox-container-manager.service`的容器网络配置修改掉，详见下一个要点。
- `ipv4.addresses 192.168.250.1/24` specifies the default gateway and subnet of the NAT network. If you wish to change this (e.g. to `192.168.42.1/24`) you must also indicate the new subnet to anbox in the `anbox-container-manager.service` using: `--container-network-address=192.168.42.2/24 --container-network-gateway=192.168.42.1`

[NetworkManager](../../06_软件与应用/红帽/NetworkManager.md) 将在每次重新启动时自动设置桥接，因此您只需要执行一次命令。

### Via systemd-networkd

The package [anbox-git](https://aur.archlinux.org/packages/anbox-git/)AUR provides configuration files for `systemd-networkd` in `/usr/lib/systemd/network/` to enable networking in anbox.

Therefore, you can start/enable `systemd-networkd` before starting `anbox-container-manager.service`.

### Via anbox-bridge script

Alternatively you can use the anbox-bridge script [used by the project](https://raw.githubusercontent.com/anbox/anbox/master/scripts/anbox-bridge.sh).

You must execute `anbox-bridge` every time before starting `anbox-container-manager.service` in order to get network working in Anbox. The easiest solution for that is to create a drop-in file for the service.

```
/etc/systemd/system/anbox-container-manager.service.d/enable-anbox-bridge.conf
```

```
[Service]
ExecStartPre=/usr/bin/anbox-bridge start
ExecStopPost=/usr/bin/anbox-bridge stop
```

## Usage

You can run the Android applications on your desktop's launcher on **Other** category.

If you want to use [adb](Android%20调试桥.md) to debug, install [android-tools](https://archlinux.org/packages/?name=android-tools)包. The `anbox session-manager` must already be running when launching it.

```
$ adb shell
```

### 安装应用

除非你用 Houdini 挑选了一个镜像, 否则 Anbox 不支持 ARM 应用. 所以应用必须是 x86_64 架构.

#### 通过 adb

安装 `*/path/to/app.apk*`

```
$ adb install /path/to/app.apk
```

获取已经安装的应用列表

```
$ adb shell pm list packages
```

注意：输出会与 `*package:app.name*` 相似, 此 `*app.name*` 会与Anbox容器内显示的不同。

卸载 `*app.name*`

```
$ adb uninstall app.name
```

如果 `*app.name*` 是一个系统应用

```
$ adb uninstall --user 0 app.name
```

#### 通过应用商店

应用商店可以轻松安装应用，包括 [anbox-image-gapps](https://aur.archlinux.org/packages/anbox-image-gapps/)AUR PlayStore商店.

### Sensor data

Via dbus different sensors can be set. Documentation on that can be found at [dbus.md](https://github.com/anbox/anbox/blob/master/docs/dbus.md).

#### Temperature data

That is the example from the author (PRs [#1522](https://github.com/anbox/anbox/pull/1522) & [#1540](https://github.com/anbox/anbox/pull/1522)):

```
$ dbus-send --session --dest=org.anbox --print-reply /org/anbox org.freedesktop.DBus.Properties.Set string:org.anbox.Sensors string:Temperature variant:double:25.1
```

#### GPS data

(introduced by PR [#1606](https://github.com/anbox/anbox/pull/1606))

GPS sensor data can also be manipulated.

If your PC has a WWAN card, you can use [gpsd](https://archlinux.org/packages/?name=gpsd)包 and the code from the PR to feed Anbox with GPS data. You do not need to have a SIM-Card for GPS.

Otherwise, you can also look at the PR to learn how to feed it fake data with the help of .

### Root shell

With this [script](https://github.com/anbox/anbox/blob/master/scripts/anbox-shell.sh) from the Anbox project one can get a root shell inside the Android container.

It is not part of the [anbox-git](https://aur.archlinux.org/packages/anbox-git/)AUR package, and it also does not use [adb](Android%20调试桥.md).

## Tips and tricks

### Android developer options

Some extra steps need to be done besides unlocking them the same way you do on an android phone. When installing the [android image](#Install_Android_Image)\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Project:%E7%A4%BE%E7%BE%A4%E9%A6%96%E9%A1%B5#损坏的链接)：无效的章节\], some modifications to `products/anbox.xml` are required:

- `<unavailable-feature name="android.hardware.usb.host" />` is the reason why they are not available.
- `<feature name="android.software.backup" />` will be needed too, to avoid a NullPointerException.

(reference: [Github issue #444](https://github.com/anbox/anbox/issues/444))

### Getting debugging information

Obviously, it is helpful to have debugging symbols in the Anbox build. For that, when [compiling Anbox](#Install_Anbox)\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Project:%E7%A4%BE%E7%BE%A4%E9%A6%96%E9%A1%B5#损坏的链接)：无效的章节\], add `options=('!strip')` to the PKGBUILD, as by default they are removed. And, use either `-DCMAKE_BUILD_TYPE=RelWithDebInfo` or `-DCMAKE_BUILD_TYPE=Debug` in the cmake call.

But there is more to it! Anbox uses [backward-cpp](https://github.com/anbox/anbox/tree/master/external/backward-cpp). If you do not delete the build files for Anbox, it will print pretty stack traces when crashing, which point out the places in the source code.

Also see the remarks in [Install Anbox](#Install_Anbox)\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Project:%E7%A4%BE%E7%BE%A4%E9%A6%96%E9%A1%B5#损坏的链接)：无效的章节\].

## Troubleshooting

If you run into issues, take a look at the official Issue Tracker: 

### Old CPUs

Anbox requires support for SSE 4.1/4.2 and SSSE 3, because Android wants that too. Some older CPUs do not provide that, so you probably cannot use Anbox, see: [Anbox Github Issue 499](https://github.com/anbox/anbox/issues/499#issuecomment-399118684).

### Old kernels

Before the kernel modules were mainlined, they were out of tree modules, which needed to be installed seperately from the kernel (Like it is the case for the nvidia kernel modules). They are not compatible with kernel 5.7 or newer.

It is still possible to use that approach with the [linux-lts](https://archlinux.org/packages/?name=linux-lts)包 or other old kernels. The package to install the modules via [DKMS](../../02_系统管理与内核/内核/DKMS.md) is not available in the AUR anymore, but can be retrieved with `git clone https://aur.archlinux.org/anbox-modules-dkms`, or resurected from .

### Secure Boot error

If you get this error message:

```
modprobe: ERROR: could not insert 'ashmem_linux': Operation not permitted
```

[Secure Boot](../../01_安装与包管理/启动过程/UEFI_安全启动.md) is likely blocking the module. You can either disable Secure Boot or sign the ashmem module yourself.

More info can be found in the [Anbox Github Docs](https://github.com/anbox/anbox/blob/master/docs/install.md#on-ubuntu-1904-and-later).

## See also

- [Official Anbox Github Repo](https://github.com/anbox/anbox)
- [Anbox Website](https://anbox.io/)
- [Posting by the main Anbox developer](https://mm.gravedo.de/blog/posts/2020-01-21-taking-the-anbox-journey-to-the-next-level)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2022-09-17 ⓘ\]
- [Explanation about binderfs](https://brauner.github.io/2019/01/09/android-binderfs.html)

## 7.3　Waydroid

*难度：高级 ｜ 篇幅：21.1 KB ｜ 所属：Android*

将要讲到：准备工作、安装、用法、网络、提示和技巧、故障排除

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**需要改进文章风格。（在[Talk:Waydroid](https://wiki.archlinuxcn.org/wiki/Talk:Waydroid)讨论）

[Waydroid](https://waydro.id) 是一种基于容器的、能在常规的 Linux 系统上启动完整 Android 系统的方法。

## 准备工作

### CPU 要求

CPU 要求取决于 CPU 架构。更多信息可参阅[此表格](https://developer.android.com/ndk/guides/abis#sa)。

可以使用 `cat /proc/cpuinfo` 检查是否具备所需的 CPU 指令集。

### GPU 要求

Waydroid 目前最适合在 Intel GPU 上使用（可开箱即用）。

已支持所有 AMD GPU；如果 Waydroid 不工作，您可能需要构建一个新的 Waydroid 映像（在 Radeon 680M 上正常工作），或者尝试以下有关 NVIDIA 的说明。

NVIDIA GPU 目前不被支持。有两种解决方法：

1.  可能的话，切换到集成显卡
2.  使用软件渲染（参见[#软件渲染](#软件渲染)）

### Wayland 会话管理器

Waydroid 只在 [Wayland](../../05_桌面与图形/Wayland/Wayland.md) 会话管理器中工作，因此请确保您处于 Wayland 会话中。

请注意，即使处于 X11 会话中，许多 Wayland 会话管理器也支持嵌套会话，因此可以在 X11 会话中运行 Waydroid（最简单的例子是 [cage](https://archlinux.org/packages/?name=cage)包）。

### 内核模块

> [![](https://wiki.archlinuxcn.org/wzh/images/6/62/Tango-edit-cut.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-cut.png)**这一章节正在考虑移除。**
>
> **原因:** Waydroid 不再需要特定的内核模块，因此也不需要 rust_binder 来使 Waydroid 正常工作。 (在 [Talk:Waydroid](https://wiki.archlinuxcn.org/wiki/Talk:Waydroid) 讨论)

需要运行包含 `rust_binder` 模块的内核，[linux](https://archlinux.org/packages/?name=linux)包、[linux-lts](https://archlinux.org/packages/?name=linux-lts)包、[linux-zen](https://archlinux.org/packages/?name=linux-zen)包 和 [linux-lily](https://github.com/archlinuxcn/repo/tree/master/archlinuxcn/linux-lily)[CNRepo](../../01_安装与包管理/非官方用户仓库/Arch%20Linux%20中文社区仓库.md)均包含该模块。若您使用不同的[内核](../../02_系统管理与内核/内核/内核.md)，则可能需要重新编译或通过 DKMS 安装 。

#### DKMS 模块

若当前内核**未提供** `rust_binder` 时，则需要使用 DKMS 模块。

安装 [binder_linux-dkms](https://aur.archlinux.org/packages/binder_linux-dkms/)AUR 并使用 `devices=binder,hwbinder,vndbinder` 内核选项加载 `binder_linux` [内核模块](../../02_系统管理与内核/内核/内核模块.md)（参见 [bug 报告](https://github.com/waydroid/waydroid/issues/904#issuecomment-1869579022)）。

```
# modprobe binder-linux devices=binder,hwbinder,vndbinder
```

此外，您也可以通过在 `/etc/modules-load.d/` 和 `/etc/modprobe.d/` 创建配置文件来让 `binder_linux` 在启动时被加载（这是可选的，参阅[内核模块](../../02_系统管理与内核/内核/内核模块.md)以获取更多信息）。

```
/etc/modules-load.d/binder_linux.conf
```

```
# Load binder_linux at boot
binder_linux
```

```
/etc/modprobe.d/binder_linux.conf
```

```
# Options for binder_linux
options binder_linux devices=binder,hwbinder,vndbinder
```

您还需要使用 `ibt=off` [内核参数](../../02_系统管理与内核/内核/内核参数.md)来解决 5.18+ 版本内核上的一个问题，参阅 [Segmentation fault when mounting `/dev/binderfs`](https://bbs.archlinux.org/viewtopic.php?id=293566)

#### 构建内核

另外，可以用必要的选项重新编译 [linux](https://archlinux.org/packages/?name=linux)包 内核——或者其他内核版本 (>=5.7)。参见[内核#编译](../../02_系统管理与内核/内核/内核.md#编译)。

当构建一个最小内核时，请遵循以下需求：

- IPv6 支持：若内核没有构建 IPV6，Waydroid 会没有 IPv4 连接。
- Netlink 套接字 （`CONFIG_NF_CT_NETLINK=y`）。
- PSI （`CONFIG_PSI=y`）。
- Loop block 设备 （`CONFIG_BLK_DEV_LOOP=m`）：`loop` 模块必须在 Waydroid 启动前被加载。

当设置编译选项时，有两个选项：*binder* 和 *binderfs*。下面提供了有关这两者的说明。

##### 使用 binder


模块既可以被编译到内核中 (`y`)，也可以被编译到模块中 (`m`)，或者不编译 (`n`) 。此外，配置中的选项不能任意组合，有些选项依赖于其他选项。

以下配置选项会将 binder 编译成模块，与此同时最后一个选项指定了在 binder 模块加载时，将在 `/dev/` 目录下创建 3 个设备。

```
CONFIG_ANDROID=y
CONFIG_ANDROID_BINDER_IPC=m
CONFIG_ANDROID_BINDERFS=n
CONFIG_ANDROID_BINDER_DEVICES="binder,hwbinder,vndbinder"
```

当从 AUR 构建内核时，可以按照以下步骤更新配置：

1.  执行 `makepkg --nobuild`，将会下载源代码、验证并解压源代码包，然后执行 `prepare()` 函数。
2.  编辑位于内核根目录下的 `.config` 文件 (注意文件名里的点)。
3.  在 `prepare()` 函数尾部的命令很可能被用于从配置文件信息重新生成 Makefile (可能是 `make olddefconfig`) 。将这条命令移动到 `build()` 函数里，或者单独执行。
4.  执行 `makepkg --noextract`，将自动从 `makepkg --nobuild` 停下的位置继续执行。

##### 使用 binderfs

已知 binder 内核模块会给部分用户造成一些问题。为了解决这些问题，binderfs 应运而生。编译内核时，必须要在新旧方式之间做出选择。使用以下选项，可以使用 binderfs 来替代 binder。

内核源代码也附带了用于设置配置选项的简单脚本。此脚本不会做依赖检查，就像手动编辑配置文件一样。当与 `.config` 处于相同目录时，可以执行以下命令：

```
$ scripts/config --enable  CONFIG_ANDROID
$ scripts/config --enable  CONFIG_ANDROID_BINDER_IPC
$ scripts/config --enable  CONFIG_ANDROID_BINDERFS
$ scripts/config --set-str CONFIG_ANDROID_BINDER_DEVICES ""
```

当从 AUR 构建内核时，将这几行插入到 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 中的合适位置即可 (通常在 `prepare()` 中) 。

#### 设置 binder 设备

确保已经安装 Waydroid 软件包的最新版本。Waydroid 将自动处理此问题。

## 安装

安装 [waydroid](https://archlinux.org/packages/?name=waydroid)包 软件包。

可以选择通过 AUR 安装 [waydroid-image](https://aur.archlinux.org/packages/waydroid-image/)AUR 或 [waydroid-image-gapps](https://aur.archlinux.org/packages/waydroid-image-gapps/)AUR 来提供所需的 Android 映像。然而，让 Waydroid 自己下载映像是更推荐的做法。

> **提示：**中国用户可能会下载映像极为缓慢，可以通过添加 [archlinuxcn](../../01_安装与包管理/非官方用户仓库/Arch%20Linux%20中文社区仓库.md) 仓库之中国大陆镜像后安装 [waydroid-image](https://github.com/archlinuxcn/repo/tree/master/archlinuxcn/waydroid-image)[CNRepo](../../01_安装与包管理/非官方用户仓库/Arch%20Linux%20中文社区仓库.md)，这可以显著提高下载速度。

在初始化 Waydroid 之后，如果映像不可用，将会自动下载最新的 Android 映像（可能会耗时很久；你也可以自行下载之后将文件放到 `/etc/waydroid-extra/images/`）。

```
# waydroid init
```

初始化支持 GApps 的 Waydroid：

```
# waydroid init -s GAPPS
```

接下来启动/启用 `waydroid-container.service`。

```
# systemctl enable --now waydroid-container
```

Waydroid 现在应该能正常工作了。

## 用法

确保 `waydroid-container.service` 已启动，然后执行：

```
$ waydroid session start
```

Waydroid 会话现在已处于活动状态，以下是一些与 Waydroid 交互的实用命令：

启动 GUI：

```
$ waydroid show-full-ui
```

启动 shell：

```
# waydroid shell
```

安装应用程序：

```
$ waydroid app install $path_to_apk
```

运行应用程序：

```
$ waydroid app launch $package-name
```

> **注意：**`$package_name` 是 list 命令所示的 `packageName`（包名）属性，并非 `Name`（应用名称）属性。

获取应用程序列表：

```
$ waydroid app list
```

## 网络

网络应该开箱即用，否则可能需要确保在执行 `waydroid session start` 之前，内核中已启用[包转发](../../03_网络与服务/网络共享/网络分享.md#启用包转发)并且允许以下规则通过防火墙：

以 [ufw](https://archlinux.org/packages/?name=ufw)包 为例：

- 允许 DNS 流量：
    - ```
        # ufw allow 67
        ```

    - ```
        # ufw allow 53
        ```

- 允许包转发：
    - ```
        # ufw default allow FORWARD
        ```

对于 [firewalld](https://archlinux.org/packages/?name=firewalld)包, 可以使用这些命令：

- DNS：
    - ```
        # firewall-cmd --zone=trusted --add-port=67/udp
        ```

    - ```
        # firewall-cmd --zone=trusted --add-port=53/udp
        ```

- 包转发：
    - ```
        # firewall-cmd --zone=trusted --add-forward
        ```

> **注意：**Waydroid 可能默认使用 nftables 作为流量转发工具，如果没有开启该服务，记得把 `/usr/lib/waydroid/data/scripts/waydroid-net.sh` 文件中的 `LXC_USE_NFT` 设置为 `false`，否则会出现ip分配正确，但是流量发送出去但是转发不回来的现象。

> **注意：**请确保在运行 `waydroid-container.service` 前没有启用 TUN Proxy，避免导致错误的网络设置；在启动后可以启用。

- 将 waydroid 接口添加为可信：
    - ```
        # firewall-cmd --zone=trusted --add-interface=waydroid0
        ```

> **注意：**正常情况下，Waydroid 创建的接口应该自动位于 firewalld 区域 `trusted` 内。如果不是这样，请调整上述命令，或将 `waydroid0` 移动到 `trusted`。为了使重新启动时能保留更改，可能还需要执行以下命令：
>
> ```
> # firewall-cmd --runtime-to-permanent
> ```

## 提示和技巧

### 在桌面环境中整合窗口

Waydroid 默认全屏运行，若您想让 Waydroid 上的应用与您的桌面环境实现窗口整合（即应用拥有独立的窗口而不影响其他 Linux 应用窗口），请先启动 Waydroid 会话：

```
$ waydroid session start
```

然后设定所需属性：

```
$ waydroid prop set persist.waydroid.multi_windows true
```

重启会话：

```
$ waydroid session stop
$ waydroid session start
```

现在应用可以在其独立的窗口中运行了。另请参阅[Waydroid 官方文档](https://docs.waydro.id/usage/install-on-desktops#launch-waydroid-in-multi-window-mode)。

### 软件渲染

确保您已经执行：

```
# waydroid init
```

（参阅[#安装](#安装)章节以获取更多信息）

接下来，向 `waydroid.cfg` 添加以下内容：

```
/var/lib/waydroid/waydroid.cfg
```

```
[properties]
ro.hardware.gralloc=default
ro.hardware.egl=swiftshader
```

然后执行以下命令以应用配置：

```
# waydroid upgrade --offline
```

最后，重启 `waydroid-container.service`。

### 调整视图尺寸

用以下命令将 Waydroid 窗口尺寸调整到您喜欢的大小：

```
$ waydroid prop set persist.waydroid.width 576
$ waydroid prop set persist.waydroid.height 1024
```

然后重启 `waydroid-container.service`。

### USB 控制器

要启用与 USB 控制器（如游戏手柄）的连接，在 Waydroid 会话运行时执行以下命令：

```
$ waydroid prop set persist.waydroid.udev true
```

```
$ waydroid prop set persist.waydroid.uevent true
```

之后，重启 Waydroid 会话。

## 故障排除

如果您遇到了问题，可参见官方的问题跟踪：[Waydroid 问题跟踪](https://github.com/waydroid/waydroid/issues)

### 一般建议

Waydroid 正在快速发展中，因此如果您遇到问题，这里有一系列值得尝试的步骤：

1.  确保 Waydroid 软件包处于最新。
2.  通过执行以下命令，确保已安装最新的 Waydroid 映像：

    ```
    # waydroid upgrade
    ```

3.  重置 Waydroid：停止 `waydroid-container.service`，执行

    ```
    # waydroid init -f
    ```

    并重新启动服务。
4.  可能还需要做一下简单的清理，执行以下命令：

    ```
    # rm -rf /var/lib/waydroid /home/.waydroid
    ```

    ```
    $ rm -rf ~/waydroid ~/.share/waydroid ~/.local/share/applications/*aydroid* ~/.local/share/waydroid
    ```

: : > **提示：**如果您是在 [2021-09](https://github.com/waydroid/waydroid/commit/eef036b3f814a9fc4869ebfe6c9d2bd51fa19f26) 后安装，那么您不必移除 `/home/.waydroid/`。

### 不兼容 ARM 应用

使用 casualsnek 的 [waydroid-script-git](https://aur.archlinux.org/packages/waydroid-script-git/)AUR 或 [waydroid-helper](https://aur.archlinux.org/packages/waydroid-helper/)AUR 安装翻译层。

为提高翻译性能，推荐在 AMD CPU 上使用 libndk，在英特尔 CPU 上使用 libhoudini。然而部分应用仅支持一种翻译层，因此当某个游戏不工作或性能极差时，您可能需要把两个翻译层都试一遍。

*安装 libndk arm 翻译层*

```
# waydroid-extras install libndk
```

*安装 libhoudini arm 翻译层*

```
# waydroid-extras install libhoudini
```

### 旋转的应用程序无法使用

参见 。

按 *F11* 可将当前应用切换到窗口模式。

### 无法启动剪贴板管理服务

安装 [python-pyclip](https://archlinux.org/packages/?name=python-pyclip)包 及其依赖（为 X11 安装 [xclip](https://archlinux.org/packages/?name=xclip)包，为 Wayland 安装 [wl-clipboard](https://archlinux.org/packages/?name=wl-clipboard)包）。

### 无法使用物理键盘

按*左 Alt* 键。

### **dnsmasq: failed to open pidfile /run/waydroid-lxc/dnsmasq.pid: Permission denied**

没有设置 apparmor 规则，在 `/etc/apparmor.d/usr.sbin.dnsmasq` 的末尾添加

```
#/etc/apparmor.d/usr.sbin.dnsmasq

@{run}/waydroid-lxc/ r,
@{run}/waydroid-lxc/* rw,
```

### Waydroid shell 中的命令输出无法访问或命令未找到

在基于 Arch 的发行版上，[使用 lxc-attach 时可能会出现一个 "bug"](../../08_安全与加密/沙箱/Linux%20容器.md#基本用法)，可能会导致 `waydroid shell` 内的命令出现此问题，如 `adbd` 或 `settings`。

一个可能的解决方案是用

```
# lxc-attach -P /var/lib/waydroid/lxc/ -n waydroid --clear-env
```

替代 `# waydroid shell` 。

### WARNING: Service manager /dev/binder has died

参见 [Issue 136](https://github.com/waydroid/waydroid/issues/136)。

启用 [PSI](https://docs.kernel.org/accounting/psi.html)，在[内核参数](../../02_系统管理与内核/内核/内核参数.md)中添加 `psi=1`。

若启用 PSI 未能修复改错误，则很有可能是因为禁用了 ia32 模拟。请尝试执行以下命令以启用 ia32 vdso：

```
# echo 1 > /proc/sys/abi/vsyscall32
```

请注意 [Waydroid 不在 Liquorix 内核上工作](https://github.com/damentz/liquorix-package/commit/2dd0b878dbd27bdc5e796a7bd7384676df9e6764)，因为其使用的某些调度器不兼容 PSI。

### 多 GPU 系统上图形损坏

目前 Waydroid 需要与其窗口混成器运行在同一 GPU 上。有两种方法修复，一种是编辑 `/var/lib/waydroid/lxc/waydroid/config_nodes`，选择合适的 GPU，另一种是让窗口混成器切换 GPU。

可以使用一个[脚本](https://raw.githubusercontent.com/Quackdoc/waydroid-scripts/refs/heads/main/waydroid-choose-gpu.sh)来快速选择 GPU。

### 无网络连接

#### Docker/iptables 转发问题

使用 Docker 时， `iptables` 默认被配置为 `FORWARD DROP` ( 查阅 [https://github.com/docker/for-linux/issues/103](https://github.com/docker/for-linux/issues/103) 以获取更多信息 ) ，根据 Waydroid 官方文档( [https://docs.waydro.id/debugging/networking-issues](https://docs.waydro.id/debugging/networking-issues) )的描述，需要将其更改为 `FORWARD ACCEPT` 。

要修复系统中其他 LXC 容器（如 Waydroid ）的网络连接，可以通过在你的 `/etc/docker/daemon.json` 中添加以下属性来禁用此行为（根据 [https://github.com/docker/for-linux/issues/103#issuecomment-3680138560](https://github.com/docker/for-linux/issues/103#issuecomment-3680138560) ）:

```
/etc/docker/daemon.json
```

```
{
    "ip-forward-no-drop": true
}
```

记得重启 `iptables.service` 和 `docker.service`。

#### Waydroid 需要 IPv6 来工作

确保系统中启用了 IPv6 ，即使你并不使用它，否则 Waydroid 将无法连接网络。[https://bbs.archlinux.org/viewtopic.php?id=301559](https://bbs.archlinux.org/viewtopic.php?id=301559)

### 该设备未通过 Play Protect 认证

参阅[官方文档](https://docs.waydro.id/faq/google-play-certification)。 若您收到该错误，请进入 waydroid shell 并检索 Android ID：

```
# waydroid shell
# ANDROID_RUNTIME_ROOT=/apex/com.android.runtime ANDROID_DATA=/data ANDROID_TZDATA_ROOT=/apex/com.android.tzdata ANDROID_I18N_ROOT=/apex/com.android.i18n sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select * from main where name = \"android_id\";"
```

之后将 ID 输入这个网站：[https://www.google.com/android/uncertified](https://www.google.com/android/uncertified) 稍等几分钟后重启 Waydroid。

### 无法输入中文

默认的安卓系统不包括中文输入法。在容器内部安装一个中文输入法即可。

### Waydroid 挂起

可能是由于音频服务器死机，请参阅 [Issue 576](https://github.com/waydroid/waydroid/issues/576) 和 [Issue 829](https://github.com/waydroid/waydroid/issues/829) 以获取详细信息。

一种方法是运行：

```
# sysctl -w kernel.pid_max=65535
```

要使其持久化，请在 `/etc/sysctl.d/` 创建一个 `.conf` 文件，并向其添加 `kernel.pid_max=65535`。

```
/etc/sysctl.d/99-sysctl.conf
```

```
kernel.pid_max=65535
```

### 应用程序需要未 root 的设备

根据 [Issue 1060](https://github.com/waydroid/waydroid/issues/1060#issuecomment-1732325955)，添加以下内容可以通过 root 检测：

```
/var/lib/waydroid/waydroid.cfg
```

```
[properties]
ro.product.brand=google
ro.product.manufacturer=Google
ro.system.build.product=redfin
ro.product.name=redfin
ro.product.device=redfin
ro.product.model=Pixel 5
ro.system.build.flavor=redfin-user
ro.build.fingerprint=google/redfin/redfin:11/RQ3A.211001.001/eng.electr.20230318.111310:user/release-keys
ro.system.build.description=redfin-user 11 RQ3A.211001.001 eng.electr.20230318.111310 release-keys
ro.bootimage.build.fingerprint=google/redfin/redfin:11/RQ3A.211001.001/eng.electr.20230318.111310:user/release-keys
ro.build.display.id=google/redfin/redfin:11/RQ3A.211001.001/eng.electr.20230318.111310:user/release-keys
ro.build.tags=release-keys
ro.build.description=redfin-user 11 RQ3A.211001.001 eng.electr.20230318.111310 release-keys
ro.vendor.build.fingerprint=google/redfin/redfin:11/RQ3A.211001.001/eng.electr.20230318.111310:user/release-keys
ro.vendor.build.id=RQ3A.211001.001
ro.vendor.build.tags=release-keys
ro.vendor.build.type=user
ro.odm.build.tags=release-keys
```

然后使用以下命令以应用配置：

```
waydroid upgrade --offline
```

### 在 ZFS 文件系统上 Waydroid 卡在“手机正在启动”和开机动画

确保 ZFS 数据集 `${HOME}/.local/share/waydroid` 的 `acltype` 属性设置为了 `posixacl` 。可以通过 `zfs set acltype=posixacl myZpool/targetDataset` 设置该项来让Waydroid在ZFS上工作. Linux 上该项的默认值 `acltype=off` 会导致此问题。

## 另请参阅

- [Waydroid GitHub 仓库](https://github.com/waydroid/waydroid)
- [Waydroid 文档](https://docs.waydro.id/) (English)
- [Waydroid Matrix 用户组](https://matrix.to/#/#waydroid:connolly.tech)
- [Waydroid Telegram 用户组](https://t.me/WayDroid)

## 7.4　Android

*难度：高级 ｜ 篇幅：27.8 KB ｜ 所属：Android*

将要讲到：同步、应用开发、编译、Flashing、在 Arch Linux 运行 Android 应用、故障排除

## 同步

有各种应用程序可以用来传输文件，同步通知等。

### 多合一

- [KDE Connect](../../05_桌面与图形/KDE/KDE.md) ([kdeconnect](https://archlinux.org/packages/?name=kdeconnect)包) – 将安卓设备整合到 KDE/Gnome 桌面，具有通知同步、剪贴板同步、多媒体控制和文件/ URL 共享等功能。

### 同步通知

- [a2ln](https://aur.archlinux.org/packages/a2ln/)AUR – 通过局域网提供通知同步，具有认证、加密等功能。

### 传输文件

- USB数据线
    - 现代安卓设备使用[媒体传输协议](../移动设备/媒体传输协议.md)。
    - 旧设备使用 [USB大容量存储设备](https://zh.wikipedia.org/wiki/USB%E5%A4%A7%E5%AE%B9%E9%87%8F%E5%AD%98%E5%82%A8%E8%AE%BE%E5%A4%87)。
    - [安卓调试桥](Android%20调试桥.md)
- 特殊U盘 / 带适配器的普通U盘
- [蓝牙](../蓝牙/蓝牙.md)
- 与 Android 对应的 Arch Linux 软件
    - 用于传输文件的协议（如[SSH](../../03_网络与服务/SSH/安全外壳协议.md)、FTP、Samba 或 HTTP）的客户端或服务器。
    - 云同步客户端
    - Syncthing
    - [sendanywhere](https://aur.archlinux.org/packages/sendanywhere/)AUR – 跨平台文件共享
    - [qrcp](https://aur.archlinux.org/packages/qrcp/)AUR –通过扫描二维码，使用 wifi 从电脑向移动设备传输文件
    - [localsend](https://aur.archlinux.org/packages/localsend/)AUR – 跨平台文件共享

## 应用开发

官方支持的构建 Android 应用程序的方式是使用 [#Android Studio](#Android_Studio)。

### Android Studio

[Android Studio](https://developer.android.com/studio/index.html) 是基于 [IntelliJ IDEA](https://en.wikipedia.org/wiki/IntelliJ_IDEA) 的官方 Android 开发环境。它为开发和调试提供了集成的 Android 开发者工具。

通过 [android-studio](https://aur.archlinux.org/packages/android-studio/)AUR 进行安装。

Android Studio在主目录下创建了一个`.android` 目录。要重置 Android Studio ，可以删除此目录。

> **注意：**
>
> - 请正确[设置 Java 环境](../../06_软件与应用/Oracle/Java.md)，否则无法启动 android-studio。
> - 如果 Android Studio 显示为一个空白窗口，请尝试 exporting `_JAVA_AWT_WM_NONREPARENTING=1`， 见 [issue #57675](https://code.google.com/p/android/issues/detail?id=57675).

Android Studio 设置向导会安装所需的 [#SDK packages](#SDK_packages) ，默认的 SDK 目录是 `~/Android/Sdk`。

要从命令行构建应用程序 (使用例如 `./gradlew assembleDebug`) 将 [ANDROID_HOME](https://developer.android.com/studio/command-line/variables#android_home) [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)设置为你的SDK位置。

### SDK 软件包

Android SDK 包可以使用 [#Android Studio](#Android_Studio) 的 [SDK Manager](https://developer.android.com/studio/intro/update#sdk-manager) 或 [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager) 命令行工具（Android SDK工具的一部分）直接从上游安装。 一些 Android SDK 包也可以使用 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 包， 它们通常安装在 `/opt/android-sdk/`中。

[必须的软件包](https://developer.android.com/studio/intro/update#recommended) :

| Android SDK Package | SDK-style path | AUR package | AUR dummy | CLI tools |
| --- | --- | --- | --- | --- |
| [Command-Line Tools](https://developer.android.com/studio/releases/cmdline-tools) | tools | [android-sdk-cmdline-tools-latest](https://aur.archlinux.org/packages/android-sdk-cmdline-tools-latest/)AUR | [android-sdk-cmdline-tools-latest-dummy](https://aur.archlinux.org/packages/android-sdk-cmdline-tools-latest-dummy/)AUR | apkanalyzer, avdmanager, lint, retrace, screenshot2, sdkmanager |
| [SDK Build-Tools](https://developer.android.com/studio/releases/build-tools) | build-tools;*version* | [android-sdk-build-tools](https://aur.archlinux.org/packages/android-sdk-build-tools/)AUR | [android-sdk-build-tools-dummy](https://aur.archlinux.org/packages/android-sdk-build-tools-dummy/)AUR | aapt, aapt2, aidl, apksigner, bcc_compat, d8, dexdump, dx, lld, llvm-rs-cc, mainDexClases, split-select, zipalign |
| [SDK Platform-Tools](https://developer.android.com/studio/releases/platform-tools) | platform-tools | [android-sdk-platform-tools](https://aur.archlinux.org/packages/android-sdk-platform-tools/)AUR | [android-sdk-platform-tools-dummy](https://aur.archlinux.org/packages/android-sdk-platform-tools-dummy/)AUR | [adb](Android%20调试桥.md), dmtracedump, e2fsdroid, etc1tool, [#fastboot](#Fastboot), hprof-conv, make_f2fs, make_f2fs_casefold, mke2fs, sload_f2fs, sqlite3, systrace |
| [SDK Platform](https://developer.android.com/studio/releases/platforms) | platforms;android-*level* | [android-platform](https://aur.archlinux.org/packages/android-platform/)AUR, [older versions](https://aur.archlinux.org/packages/?K=android-platform-) | [android-platform-dummy](https://aur.archlinux.org/packages/android-platform-dummy/)AUR (unnecessary) |

[android-tools](https://archlinux.org/packages/?name=android-tools)包 软件包提供了 [adb](Android%20调试桥.md), [#fastboot](#Fastboot)， `e2fsdroid` 和 `mke2fs.android`， SDK Platform-Tools along 提供了 `mkbootimg` 和 `ext2simg`。

> **注意：**
>
> - 由于Android SDK包含32位的二进制文件， 你必须启用 [multilib](../../00_关于与社区/关于%20Arch/官方仓库.md) 仓库。否则你会得到 `error: target not found: lib32-*` 错误信息。
> - 如果你选择直接从上游安装 SDK 包，请安装 *AUR dummy* 列的 AUR 包来拉入所需的依赖。
> - 如果你在试图运行 `sdkmanager` 时得到一个 `java.lang.NoClassDefFoundError` 异常，暂时使用 OpenJDK 8 的JRE，通过安装 [jre8-openjdk](https://archlinux.org/packages/?name=jre8-openjdk)包 并且 [切换 java 环境](../../06_软件与应用/Oracle/Java.md#Switching_between_JVM)。 详见 [Failed to run sdkmanager --list with Java 9](https://stackoverflow.com/questions/47150410/failed-to-run-sdkmanager-list-with-java-9)

#### Android 模拟器

如下方式可以安装 [Android 模拟器](https://developer.android.com/studio/run/emulator)： `emulator` SDK 包、[android-emulator](https://aur.archlinux.org/packages/android-emulator/)AUR 软件包。如果 [android-emulator-dummy](https://aur.archlinux.org/packages/android-emulator-dummy/)AUR 可以作为占位空软件包使用。

运行安卓模拟器需要英特尔或 ARM 系统镜像。可以通过 AUR 安装、 `sdkmanager` 或者 [AVD Manager](https://developer.android.com/studio/run/managing-avds) 进行安装。

#### Making /opt/android-sdk group-writeable

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 请不要这样做。使用另一个软件包管理器 (Android SDK manager) 来管理 pacman 安装的文件是一个坏主意。这将使软件包管理器变得毫无用处。（在 [Talk:Android](https://wiki.archlinuxcn.org/wiki/Talk:Android) 中讨论）

AUR 包将 SDK 安装在 `/opt/android-sdk/`。 这个目录有root [权限](../../07_开发与工具/命令行/文件权限与属性.md)，所以请注意 sdk 管理器正以 root 身份运行。如果你打算以普通用户身份使用它，创建 android-sdk 用户组，并添加你的用户。

```
# groupadd android-sdk
# gpasswd -a <user> android-sdk
```

设置一个访问控制列表，让新创建的组的成员可以写入 android-sdk 文件夹中。由于运行 sdkmanager 也可以创建新的文件，将 ACL 设置为默认 ACL 。默认组项中的 X 意味着 "如果所有者（或其他任何用户）可执行，允许其执行"

```
# setfacl -R -m g:android-sdk:rwx /opt/android-sdk
# setfacl -d -m g:android-sdk:rwX /opt/android-sdk
```

重新登录或以 <user> 身份登录你的终端到新创建的组：

```
$ newgrp android-sdk
```

### 其他的 IDEs

Android Studio 是基于 IntelliJ IDEA 的官方 Android 开发环境。另外，你也可以使用 Netbeans 的 NBAndroid-V2。介绍如下。

#### Netbeans

如果你喜欢使用 Netbeans 作为你的 IDE，并想开发 Android 应用程序，请使用 [NBAndroid-V2](https://github.com/NBANDROIDTEAM/NBANDROID-V2) 。

安装 [android-sdk](https://aur.archlinux.org/packages/android-sdk/)AUR 包 并遵循 [NBANDROID README](https://github.com/NBANDROIDTEAM/NBANDROID-V2/blob/master/README.md) 的指示。

#### Vim / Neovim

可以使用 (Neo)vim 像 IDE 一样为 Android 和 iOS 编写 `flutter` 应用程序。使用 [Vim插件管理器](../../07_开发与工具/文本编辑器/Vim.md#Using_a_plugin_manager)安装 [coc](https://github.com/neoclide/coc.nvim) 。同时安装 [coc-flutter](https://github.com/iamcco/coc-flutter) 扩展，用于代码补全（像在 Android Studio 中），并将代码加载到 Android 模拟器中。

#### Emacs

要使用 Emacs 开发一个移动的 `flutter` 应用程序，正如 [flutter.dev](https://flutter.dev/docs/get-started/editor?tab=emacs) 的官方说明所建议的，安装 [lsp-dart](https://emacs-lsp.github.io/lsp-dart/)。

### 其他工具

#### Marvin

Marvin 是一个帮助初学者建立 Android 开发环境的工具。安装 [marvin_dsc](https://aur.archlinux.org/packages/marvin_dsc/)AUR 可以帮助你设置以下东西：JDK、Android SDK、IDE(s) 和 AVD。

## 编译

Please note that these instructions are based on the [official AOSP build instructions](https://source.android.com/source/building). Other Android-derived systems such as LineageOS will often require extra steps.


### Required packages

> **注意：**Before doing the following steps, the [multilib](../../00_关于与社区/关于%20Arch/官方仓库.md) repository must be enabled in `/etc/pacman.conf`.

To build AOSP 13 you need a TTF font installed (e.g. [ttf-dejavu](https://archlinux.org/packages/?name=ttf-dejavu)包) and the dependencies of the [aosp-devel](https://aur.archlinux.org/packages/aosp-devel/)AUR metapackage.

Additionally, LineageOS (as well as other many Android distributions like ArrowOS,PixelExperience etc) requires the following dependencies of the [lineageos-devel](https://aur.archlinux.org/packages/lineageos-devel/)AUR metapackage.

### Java Development Kit

The [required JDK version](https://source.android.com/source/requirements) depends on the Android version you are building:

- For Android 9 (Pie) and up, Java is included with the Android source and no separate installation is needed.
- For Android 7 and 8 (Nougat and Oreo), OpenJDK 8 is required, which is available with the [jdk8-openjdk](https://archlinux.org/packages/?name=jdk8-openjdk)包 package.

> **注意：**For older Android versions, where Java is not included, Java is expected to be in `/usr/lib/jvm/java-*version*-openjdk-amd64`.
>
> Set JAVA_HOME to avoid this requirement and match the Arch Linux installation path. Example:
>
> ```
> $ export JAVA_HOME=/usr/lib/jvm/java-version-openjdk
> ```
>
> This change will be valid only for the current terminal session.

### Setting up the build environment

Install the [repo](https://archlinux.org/packages/?name=repo)包 package.

Create a directory to build.

```
$ mkdir ~/android
$ cd ~/android
```

> **注意：**Do not build under a directory where you do not have read/write access.

### Downloading the source code

This will clone the repositories. You **only** need to do this the first time you build Android, or if you want to switch branches.

- The `repo` has a `-j` switch that operates similarly to the one used with `make`. Since it controls the number of simultaneous downloads, you should adjust the value depending on downstream network bandwidth.

- You will need to specify a **branch** ([list of branches](https://android.googlesource.com/platform/manifest/+refs)) to check out with the `-b` switch. If you leave the switch out, you will get the so-called **master branch**.

```
$ repo init -u https://android.googlesource.com/platform/manifest -b master
$ repo sync -j4
```

> **注意：**To further decrease sync times, you can use the `-c` switch with the *repo* command as such:
>
> ```
> $ repo sync -j8 -c
> ```
>
> The `-c` switch will only sync the branch which is specified in the manifest, which in turn is determined by the branch specified with the `-b` switch, or the default branch set by the repository maintainer.

Wait a long time. Just the uncompiled source code, along with the `.repo` and `.git` directories that are used to keep track of it, are very large. As of Android 10, at least 250 GB of free disk space is [required](https://source.android.com/setup/build/requirements).

> **注意：**If you want to update your local copy of the Android source, at a later time, simply enter the build directory, load the Virtualenv, and re-sync:
>
> ```
> $ repo sync
> ```

### Building the code

This should do what you need for AOSP:

```
$ source build/envsetup.sh
$ lunch full-eng
$ make -j4
```

If you run **lunch** without arguments, it will ask what build you want to create. Use -j with a number between one and two times number of cores/threads.

The build takes a very long time.

> **注意：**
>
> - Make sure you have enough RAM. Android will use the `/tmp` directory heavily. By default the size of `/tmp` is half the size of your RAM. If it fills up, the build will fail. 4 GiB of RAM or more is recommended. If `/tmp` is not large enough, you can [increase it](../../02_系统管理与内核/文件系统/Tmpfs.md#Examples). Make sure you have the combined RAM and swap space to back it. Alternatively, you can get rid of the tmpfs from [fstab](../../01_安装与包管理/启动过程/Fstab.md) all together.
> - From the [Android Building and Running guide](https://source.android.com/source/building#build-the-code):
>
> : : GNU make can handle parallel tasks with a `-jN` argument, and it is common to use a number of tasks N that is between 1 and 2 times the number of hardware threads on the computer being used for the build. E.g. on a dual-E5520 machine (2 CPUs, 4 cores per CPU, 2 threads per core), the fastest builds are made with commands between `make -j16` and `make -j32`.

### Testing the build

When finished, run/test the final image(s).

```
$ emulator
```

### Creating a flashable Image

To create an image that can be flashed it is necessary to:

```
make -j8 updatepackage
```

This will create a zip image under `**out/target/product/hammerhead**` (hammerhead being the device name) that can be flashed.

## Flashing

In some cases, you want to return to the stock Android after flashing custom ROMs to your Android mobile device. For flashing instructions of your device, please use [XDA forums](https://forum.xda-developers.com/).

### Fastboot

Fastboot (as well as [ADB](Android%20调试桥.md)) is included in the [android-tools](https://archlinux.org/packages/?name=android-tools)包 package.

> **注意：**
>
> - Restoring firmwares using `fastboot` can be quite tricky, but you might want to browse [XDA developers forums](https://www.xda-developers.com/) for a stock firmware, which is mostly a `*.zip` file, but inside of it, comes with the firmware files and `flash-all.sh` script. For example, [Google Nexus](https://developers.google.com/android/nexus/images) firmwares include `flash-all.sh` script or another example could be for OnePlus One - [XDA thread](https://forum.xda-developers.com/oneplus-one/general/guide-return-opo-to-100-stock-t2826541), where you can find firmwares with included `flash-all.sh` script.
> - If you get a `no permissions` error or execution just hangs with `< waiting for any device >` then you need to run `fastboot` as the root user. Alternatively you can install [android-udev](https://archlinux.org/packages/?name=android-udev)包 or the AUR package [android-udev-git](https://aur.archlinux.org/packages/android-udev-git/)AUR and reconnect your device.

### Samsung devices

Samsung devices cannot be flashed using **Fastboot** tool. Alternatives are [Heimdall](#Heimdall) and [Odin](#Odin_(Virtualbox)) (by using Windows and VirtualBox).

#### samloader

To download original Samsung firmware, a platform independent script, [samloader](https://github.com/nlscc/samloader) can be used.

#### Heimdall

[Heimdall](https://glassechidna.com.au/heimdall/) is a cross-platform open-source tool suite used to flash firmware (also known as ROMs) onto Samsung mobile devices and is also known as an alternative to [Odin](https://odindownload.com/). It can be installed as [heimdall](https://archlinux.org/packages/?name=heimdall)包.

The flashing instructions can be found on Heimdall's [GitHub repository](https://github.com/Benjamin-Dobell/Heimdall/tree/master/Linux) or on [XDA forums](https://forum.xda-developers.com/showthread.php?t=1922461).

#### Odin (Virtualbox)

> **注意：**This section only covers preparation and **not** flashing instructions. Search [XDA developers forums](https://www.xda-developers.com) to find flashing instructions for a specific device. For example, [Samsung Galaxy S4](https://forum.xda-developers.com/showthread.php?t=2265477).

It is also possible to restore [firmware (Android)](https://www.sammobile.com/firmwares/) on the Samsung devices using [Odin](https://odindownload.com/), but inside the [VirtualBox](../../06_软件与应用/Oracle/VirtualBox.md).

Arch Linux (host) preparation:

1.  Install [VirtualBox](../../06_软件与应用/Oracle/VirtualBox.md) together with its [extension pack](../../06_软件与应用/Oracle/VirtualBox.md#Extension_pack) and [guest additions](../../06_软件与应用/Oracle/VirtualBox.md#Guest_additions_disc).
2.  Install your preferred, but compatible with Odin, Windows operating system (with VirtualBox guest additions) into a virtual hard drive using VirtualBox.
3.  Open VirtualBox settings of your Windows operating system, navigate to *USB*, then tick (or make sure it is ticked) **Enable USB 2.0 (EHCI) Controller**.
4.  At VirtualBox running Windows operating system, click in the menu bar *Devices > USB Devices*, then click on your Samsung mobile device from the list, which is connected to your computer via USB.

Windows (guest) preparation:

1.  Install [Samsung drivers](https://androidxda.com/download-samsung-usb-drivers).
2.  Install [Odin](https://odindownload.com/).
3.  Download required [Samsung firmware (Android)](https://www.sammobile.com/firmwares/) for your smartphone model.

Check if configuration is working:

1.  Turn your device into Download mode and connect to your Linux machine.
2.  In virtual machine toolbar, select *Devices > USB > ...Samsung...* device.
3.  Open Odin. The white box (a big one at the bottom-left side) named **Message**, should print a line similar to this:

```
<ID:0/003> Added!!
```

which means that your device is visible to Odin & Windows operating system and is ready to be flashed.

## 在 Arch Linux 运行 Android 应用

一些工具致力于在 Arch Linux （或其他发行版）下运行 Android 程序，如下表所示：

- [基于容器](https://en.wikipedia.org/wiki/Containerization_(computing))的技术应当是最流行的。这些技术是在 Android 以外的 Linux 内核中使用 Android 应用最原生的方式，对系统的兼容性、运行性能都是最强大的。其中值得注意的有：
    - [Anbox](Anbox.md) 是 Linux 最流行的 Android 容器技术。它在 Lineageos 中运行 Android 7.1 镜像。
    - [Waydroid](Waydroid.md) 是 Anbox 的一个分支，正在变得流行。它更贴近硬件，也就更加高效。它基于 Lineageos 17.1 （Android 10）镜像，提供安装 Google Play Store 与其他开源应用的选项。在独立窗口中运行 Android 程序，它也提供完整的 Android UI。

- 一些 Chromium 扩展也可运行 Android 应用：
    - **Arc Welder** 是谷歌在 Chrome OS 中测试新应用的扩展，目前已经弃用。
    - [ARChon](https://archon-runtime.github.io/) 是在基于 Chromium 的浏览器中运行 Android 应用的开源扩展。
- 当然也可以运行完整的 Android 模拟器。它的优势是你可以在 x86 平台上运行 arm 应用，劣势则是它的性能不佳。以下是几个例子：
    - [Android studio 内置模拟器](#Android_Emulator)，正如前文所示。
    - [Genymotion](https://www.genymotion.com/) 是 Android 模拟、测试的套装。

> **提示：**如果你想运行基于 x86 的安卓应用，同时愿意另外安装操作系统，你可以使用 [Android-x86](https://www.android-x86.org/)：Android 在 x86 的移植。

## 故障排除

### Android Studio: Android Virtual Devices show 'failed to load'.

确保按 [#Android Studio](#Android_Studio) 中的说明正确设置环境变量 `ANDROID_HOME`。

### Android Studio: 'failed to create the SD card'

如果你试图在 x86_64 Arch下运行AVD（Android Virtual Device）并得到上述错误，请从 [multilib](../../00_关于与社区/关于%20Arch/官方仓库.md) 仓库安装 [lib32-gcc-libs](https://archlinux.org/packages/?name=lib32-gcc-libs)包 软件包。

### Eclipse: During Debugging "Source not found"

最有可能的是，调试器想要跳到 Java 源代码中。由于安卓的源代码并不随 Android SDK 一起提供，这就导致了错误。最好的解决办法是使用步骤过滤器，不要跳到 Java 源代码中。默认情况下，步骤过滤器没有被激活。要激活它们：*Window > Preferences > Java > Debug > Step Filtering*. 考虑将它们全部选中。如果合适，你可以添加 android.\* 包。参见[使用步骤过滤器](https://help.eclipse.org/latest/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fviews%2Fdebug%2Fref-usestepfilters.htm)。

### ValueError: unsupported pickle protocol

一个解决方案是：

```
$ rm ~/.repopickle_.gitconfig
```

如果这不起作用，那就试试这个：

```
$ find /path/to/android-root -name .repopickle_config -delete
```

### libGL error: failed to load driver: swrast OR AVD does not load and no error message displayed

Sometimes, beginning to load an AVD will cause an error message similar to this to be displayed, or the loading process will appear to finish but no AVD will load and no error message will be displayed.

The AVD loads an incorrect version of libstdc++, you can remove the folder libstdc++ from `~/.android-sdk/emulator/lib64` (for 64-bit) or `~/.android-sdk/emulator/lib` (for 32-bit) , e.g.:

```
$ rm -r ~/.android-sdk/emulator/lib64/libstdc++
```

Note that in versions before Android Studio 3.0, this directory was in a different location:

```
$ rm -r ~/Android/Sdk/emulator/lib64/libstdc++
```

Alternatively you can set and export ANDROID_EMULATOR_USE_SYSTEM_LIBS in ~/.profile as:

```
export ANDROID_EMULATOR_USE_SYSTEM_LIBS=1
```

Reference: [Android Studio user guide](https://developer.android.com/studio/command-line/variables.html#studio_jdk)

Fix for the .desktop file might be achieved by using env command, prefixing the Exec line [Desktop entries#Modify environment variables](../../01_安装与包管理/配置文件/桌面项.md#Modify_environment_variables)

```
env ANDROID_EMULATOR_USE_SYSTEM_LIBS=1
```

### sh: glxinfo: command not found

以下是完整的错误：

```
Cannot launch AVD in emulator.
Output:
sh: glxinfo: command not found
sh: glxinfo: command not found
libGL error: unable to load driver: swrast_dri.so
libGL error: failed to load driver: swrast
X Error of failed request:  BadValue (integer parameter out of range for operation)
  Major opcode of failed request:  154 (GLX)
  Minor opcode of failed request:  24 (X_GLXCreateNewContext)
  Value in failed request:  0x0
  Serial number of failed request:  32
  Current serial number in output stream:  33
QObject::~QObject: Timers cannot be stopped from another thread
```

你可以尝试安装 glxinfo([mesa-utils](https://archlinux.org/packages/?name=mesa-utils)包)，但如果你的电脑有足够的能力，你可以简单地使用软件来渲染图形。要做到这一点，进入 *Tools > Android > AVD Manager* ，编辑 AVD（点击铅笔图标），然后选择 *Software* - GLES 2.0 用于 *Emulated Performance > Graphics* 。

### Android Emulator: no keyboard input in xfwm4

In xfwm4, the vertical toolbar buttons window that is on the right of the emulator takes focus from the emulator and consumes keyboard events. ([bug report](https://issuetracker.google.com/issues/37094173))

You can use the workaround described in :

1.  Open the xfwm4 settings.
2.  Switch to the Focus tab.
3.  Change the Focus Model to "Focus follow mouse".
4.  Disable *Automatically raise windows when they receive focus* option below.\\

### Android 模拟器: 在 WM 平铺模式下使用时，窗口晃动和闪动

当使用像 [dwm](../../05_桌面与图形/动态窗口管理器/Dwm.md) 这样的平铺窗口管理器时，安卓模拟器会摇晃和闪烁。你可以使用 [krohnkite issue 72](https://github.com/esjeon/krohnkite/issues/72#issuecomment-613789987) 中描述的解决方法（窗口浮动是由 [dwm](../../05_桌面与图形/动态窗口管理器/Dwm.md) 的 `Alt+f` 引起的）。

### Android Emulator: Segmentation fault (core dumped)

When using [Nouveau](../../05_桌面与图形/图形/Nouveau.md) drivers try to disable gpu hardware acceleration.

In some devices it can only be done by editing `$HOME/.avd/*device_name*.avd/config.ini`.

1.  Set `hw.gpu.enabled=no`
2.  Set `hw.gpu.mode=off`

### Android Emulator: Not launching / qemu-system: address resolution failed

There is an issue where no emulator-window shows up after starting a virtual device in android-studio. If this applies to you, launch the emulator from the console and inspect its output:

```
$ emulator -avd $(emulator -list-avds)
```

If on any line, it says anything similar to:

```
qemu-system-x86_64 : address resolution failed for ::1:46189: Name or service not known
```

you may try disabling IPv6:

```
$ sysctl net.ipv6.conf.all.disable_ipv6=1
```

If this solves the issue and the virtual device shows up in android-studio, you may consider a permanent change:

```
$ echo "net.ipv6.conf.all.disable_ipv6=1" | sudo tee -a /etc/sysctl.d/99-sysctl.conf
```

### adb: sideload connection failed: insufficient permissions for device

如果得到如下错误：

```
adb: sideload connection failed: insufficient permissions for device
See [https://developer.android.com/tools/device.html] for more information
```

或者

```
adb: trying pre-KitKat sideload method...
adb: pre-KitKat sideload connection failed: insufficient permissions for device
See [https://developer.android.com/tools/device.html] for more information
```

尝试重启 adb 服务器，看能否解决这个问题：

```
 $ adb kill-server
 # adb start-server
```

或者，确保已经安装了 Android 的 [udev 规则](../硬件检测和故障排除/Udev.md#About_udev_rules)。 参见 [#Fastboot](#Fastboot).
