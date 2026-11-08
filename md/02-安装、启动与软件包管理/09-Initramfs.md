# 第 9 章　Initramfs

> 本章解决内核到根文件系统之间的那段空白：内核要靠早期用户空间装载模块、挂载根分区，弄错一步就停在半路。必读《Mkinitcpio》，从安装、创建映像到排错都在其中；再读《Mkinitcpio/极简 initramfs》，弄清 udev 要求、.preset 作用与模块筛选。《Booster》《Dracut》是替代路线，不换方案可以跳过。
> 
> 最常见的坑是改了 mkinitcpio.conf 却不重新生成映像，改动不会自动生效；只编辑 .preset 而不看实际映像也会误判。更险的是随手删模块，挂不上根分区系统就起不来。

<!-- 本章 4 节，共 69 KB -->

## 9.1　Booster

*难度：进阶 ｜ 篇幅：7.6 KB ｜ 所属：Initramfs*

将要讲到：安装、配置、引导加载程序配置、问题解决、参见

> **翻译状态：**
>
> - 本文（或部分内容）译自 [Booster](https://wiki.archlinux.org/title/Booster)，最近一次同步于 2024-6-9，若英文版本有所[更改](https://wiki.archlinux.org/title/Booster?diff=0&oldid=810174)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/Booster_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** 需要翻译。（在 [Talk:Booster#](https://wiki.archlinuxcn.org/wiki/Talk:Booster) 中讨论）

[Booster](https://github.com/anatol/booster) 是一个快速的[initramfs](../启动过程/Arch%20的启动流程.md)生成器，类似于[mkinitcpio](Mkinitcpio.md)和[dracut](Dracut.md)。 Booster 的灵感来源于["distri" 项目](https://michael.stapelberg.ch/posts/2020-01-21-initramfs-from-scratch-golang/)，旨在创建小而快的启动映像。

Booster 提供了 `/usr/bin/booster` 用户空间工具，以生成 initramfs 映像。生成的映像默认位于 `/boot/`。

## 安装

安装 [booster](https://archlinux.org/packages/?name=booster)包。软件包安装钩子会生成 initramfs 镜像，每个已安装的内核生成一个镜像（例如 [linux](https://archlinux.org/packages/?name=linux)包, {[linux-lts](https://archlinux.org/packages/?name=linux-lts)包）。镜像文件位于 `/boot` 目录下：

```
$ ls -lh /boot/booster*
```

```
-rwxr-xr-x 1 root root 4.0M Dec 16 16:20 /boot/booster-linux.img
```

也可以手动创建镜像：

```
$ booster build mybooster.img
```

## 配置

Booster 配置文件位于 `/etc/booster.yaml`。如果文件为空，那么booster将会使用默认配置 (host-specific images, no network)。配置详见 [booster(1) § CONFIG FILE](https://man.archlinux.org/man/booster.1#CONFIG_FILE)。

### 早期加载内核模块

有时，一些内核模块需要在 initramfs 阶段加载。

例如，当你需要加载 `nvidia` 模块时，可以在配置文件中采取以下设置：

```
/etc/booster.yaml
```

```
modules_force_load: nvidia
```

然后[#重新生成 booster 镜像](#重新生成_booster_镜像)。

### 加密

Booster supports [LUKS](../../08_安全与加密/静态数据加密/Dm-crypt.md) based full-disk encryption out-of-the-box like Clevis. The generator does not need any extra configuration. Yet, for the initramfs you need to append information about the LUKS partition where the root resides. This is done with either `rd.luks.uuid=*LUKSUUID*` or `rd.luks.name=*LUKSUUID*=*LUKSNAME*` [kernel parameter](../../02_系统管理与内核/内核/内核参数.md) that need to be specified in the boot loader configuration file. `*LUKSUUID*` specifies the [UUID](../../04_硬件与外设/硬件检测和故障排除/块设备持久化命名.md) of the encrypted LUKS partition that needs to be unlocked by Booster. The [booster(1) § UUID parameters](https://man.archlinux.org/man/booster.1#UUID_parameters) manual recommends that the UUID does not contain any quotes. `*LUKSNAME*` specifies name of the unlocked partition (as in `/dev/mapper/*LUKSNAME*`). See [booster(1) § BOOT TIME KERNEL PARAMETERS](https://man.archlinux.org/man/booster.1#BOOT_TIME_KERNEL_PARAMETERS) for related options.

No image rebuild is required. Once the boot loader configuration is done, reboot the computer. After that you will see a `Enter passphrase for *YOURROOT*:` prompt at boot time asking for a password for the encrypted root partition.

#### systemd style binding

Booster also supports partitions [bound with systemd](https://0pointer.net/blog/unlocking-luks2-volumes-with-tpm2-fido2-pkcs11-security-hardware-on-systemd-248.html) such as `systemd-fido2` and `systemd-tpm2`.

If you use \`systemd-fido2\` then please install [libfido2](https://archlinux.org/packages/?name=libfido2)包 package and add fido2-assert to the image using following configuration:

```
/etc/booster.yaml
```

```
extra_files: fido2-assert
```

[Regenerate the booster images](#Regenerate_booster_images). Booster will detect this configuration during boot and use the present YubiKey to unlock the drive.

> **注意：**Before commit 1b65577, it has been reported Booster may not load some necessary kernel modules early enough to unlock encrypted partitions with a FIDO2 key. In this case, add the following modules to be forcibly loaded:

```
/etc/booster.yaml
```

```
modules_force_load: usbhid,hid_sensor_hub
extra_files: fido2-assert
```

### 重新生成 booster 镜像

完成 Booster 配置后，需要更新 `/boot` 中的 Booster 镜像。可以使用 `booster build booster-foo.img` 手动生成镜像，也可以使用便捷脚本 `/usr/lib/booster/regenerate_images` 遍历所有已安装的内核，为每个内核生成 Booster 镜像。

## 引导加载程序配置

生成映像后，就该配置[引导加载程序](../启动过程/Arch%20的启动流程.md)了。

### rEFInd

如果配置已经依赖于[自动检测](../启动加载程序/REFInd.md#对于_rEFInd_自动检测到的内核)\]，则无需额外更改配置。[rEFInd](../启动加载程序/REFInd.md) 支持 [initrd 文件命名为 booster\*](https://sourceforge.net/p/refind/code/ci/778878f7f30f68b7fa0282074e5dd8fc5894a212/)。

如果在[refind.conf](../启动加载程序/REFInd.md#refind_linux.conf)或 [手动启动项](../启动加载程序/REFInd.md#手动启动项)中手动指定 initramfs 路径，请确保使用正确的文件名。例如，`**booster**-linux.img` 而不是 `**initramfs**-linux.img`。

### systemd-boot

要使用 [systemd-boot](../启动加载程序/Systemd-boot.md) 启用新的 initramfs 映像，只需像这样创建一个新的引导加载器条目：

```
/boot/loader/entries/booster.conf
```

```
title Arch Linux with booster
linux /vmlinuz-linux
initrd /booster-linux.img
options root=UUID=08f83949-bcbb-47bb-bc17-089aaa59e17e rw
```

根文件系统在 `UUID=08f83949-bcbb-47bb-bc17-089aaa59e17e`。运行 `blkid /dev/*ROOTDEVICE*` 查找您的根设备 UUID。

## 问题解决

### 调试

如果 Booster 出现问题，不能按预期运行，请启用调试信息输出，以提供更多信息说明发生了什么：

- 对于生成器，有一个 `-debug` 命令行参数： `booster -debug`。
- 对于 init，有一个 `boost.debug` [内核参数](../../02_系统管理与内核/内核/内核参数.md)。

如果您认为这是 Booster 本身的问题，那么[请在 GitHub 上提交ticket](https://github.com/anatol/booster/issues)。

### Booster生成器出现 "too many open files"错误

如果启用了 `strip` 和 `universal`，并出现类似 `/usr/lib/modules/glue_helper.ko: pipe2: too many open files` 的错误，则需要增加每个进程的打开文件限制。请参见Limits.conf#nofile。

## 参见

- [上游 README](https://github.com/anatol/booster/blob/master/README.md)

## 9.2　Mkinitcpio/极简 initramfs

*难度：进阶 ｜ 篇幅：8.6 KB ｜ 所属：Initramfs*

将要讲到：Udev 要求、编辑 .preset 文件、找出需要的模块、初步编辑 mkinitcpio.conf、初步测试、整理模块

本文介绍如何为具有特定、已知和静态硬件配置的系统创建精简、最小的 initramfs。 Falconindy (Dave Reisner) 的 [Optimizing Bootup With mkinitcpio](http://blog.falconindy.com/articles/optmizing-bootup-with-mkinitcpio.html) 详细阐述了该过程。

## Udev 要求

创建自己的 initramfs 映像的一大优点是可以移除 `udev` 钩子。这个钩子在 initramfs 中占了相当大一块空间（使用 LZ4 和 LZOP 压缩时约为 700-800 KiB，其他算法较小）。因此它不仅导致启动时加载耗时更长（需要解压更多的数据），而且初始化其自身时也要额外的时间。**但是，仍有部分操作需要** `udev`，包括解析 UUID、LABEL、PARTUUID 和 PARTLABEL 标识符（[workaround hook without-udev](https://unix.stackexchange.com/questions/352381/how-to-boot-into-root-btrfs-file-system-with-minimal-initramfs-without-udev-hook/352932#answer-352932)）以及包含 `root` 分区的 LVM 和 mdadm 设备。 若您不确定是否需要 `udev`，请继续按照本页上的说明操作，直到 [#初步测试](#初步测试)章节。如果在没有 `udev` 的时候出了问题，请重新启用此钩子，再试一次。

另请注意，虽然大部分键盘（AT，PS/2，USB）不需要 `udev` 钩子也能正常工作，但使用罗技统一接收器（Logitech Unified Receiver）的 USB 设备需要。此时，您可以在所有映像中包含 `udev` 或依赖 `fallback` 映像正常工作。

如果您确实需要 `udev`，最小化映像的努力可能就白费了。映像大小也许能减小约 600 KiB，但是启动时间不会有显著改变。在此情况继续下去，不失为一次学习机会。

## 编辑 .preset 文件

在 Falconidy 的教程中，他编辑 `/etc/mkinitcpio.conf` 并运行 `mkinitcpio -g` 来创建测试 initramfs 映像，使系统上已知正常启动的 initramfs 映像保持不变。 但是，如果您事后盲目运行 `mkinitcpio -P`，甚至 `fallback` 映像也会被删除。

准备自行创建 initramfs 的更安全方法是修改 `/etc/mkinitcpio.d` 中的 `.preset` 文件。以下的示例設定將以最小的 initramfs 映像取代`default`，並依照 The Arch Way 的方式建立一個新的正常映像文件。 如果出现问题，您仍可以使用 `normal` 或 `fallback` 映像。 完成后，您可以从配置中删除 `normal_*` 行并删除 `initramfs-linux*-normal.img` 文件。

```
...

PRESETS=('default' 'normal' 'fallback')
...

default_options="-S udev,block,mdadm_udev,filesystems,keyboard,fsck,consolefont"
...

#normal_config="/etc/mkinitcpio.conf"
normal_image="/boot/initramfs-linux-normal.img"
#normal_options=""
...
```

`mdadm_udev` 和 `consolefont`钩子不在 Arch Linux 默认的配置中，在 `*_options` 行的 `-S` 参数中包含无关的钩子不会导致错误。

## 找出需要的模块

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** 需要翻译（在 [Talk:Mkinitcpio/极简 initramfs#](https://wiki.archlinuxcn.org/wiki/Talk:Mkinitcpio/%E6%9E%81%E7%AE%80_initramfs) 中讨论）

找出机器需要的模块的最快方法是重启电脑，使用 `fallback` 映像并通过引导加载程序（boot loader）添加 `break=postmount` 内核参数。于是在根文件系统挂载好后便可进入命令行界面。

重启电脑后，通过以下命令来获取需要的模块：

```
lsmod | awk 'NF==3{print $1}'
```

> **注意：**`awk` 命令會返回每一行的第一個欄位（使用 `{print $1`），但只限於那些剛好有三個欄位的行，這是通过 `NF==3` 來強制篩選的。 模組依賴關係會包含第四個欄位，用來顯示是由哪個模組引入這個依賴，因此這些行會因為有第四個欄位而被過濾掉。Arch 的 `mkinitcpio` 會處理那些正當包含在 `MODULES=()`、`FILES=()` 和 `BINARIES=()` 陣列中的依賴項目。

保存已加载模块的清单，输入 `exit` 继续启动机器。

另一种方式是安装 [hwdetect](https://archlinux.org/packages/?name=hwdetect)包 来帮助确定所需的模块。虽然此软件包已停止维护，它仍可提供有价值的信息。另外，请参阅[内核模块](../../02_系统管理与内核/内核/内核模块.md)以开始使用本机工具。

## 初步编辑 mkinitcpio.conf

编辑 `/etc/mkinitcpio.conf` 修改 `MODULES=` 数组。值得注意 `/etc/mkinitcpio.conf` 作为脚本调用，可以使用 bash 脚本构建 MODULES 数组。

```
MODULES=()   # filesystems
MODULES+=()  # storage
MODULES+=()  # keyboard
MODULES+=()  # miscellaneous
```

把模块添加到配置中最后的 `miscellaneous` 行中，整理时再将模块移至合适的行去。

若需要对 `root` 分区设备和在 `/etc/fstab` 的**任何其他挂载点**进行文件系统检查操作，请按下文配置：

- 对于 ext\[2|3|4\] 设备：

```
BINARIES=(fsck fsck.ext[2|3|4] e2fsck)
```

- 对于 vfat (UEFI boot) 设备：

```
BINARIES=(fsck fsck.vfat dosfsck)
```

- 对于 btrfs 单盘设备：

```
BINARIES=(fsck fsck.btrfs btrfsck)
```

- 对于 btrfs 多盘设备：

```
BINARIES=(fsck fsck.btrfs btrfs btrfsck)
```

- 对于 xfs 设备：

```
BINARIES=(fsck fsck.xfs xfs_repair)
```

> **注意：**
>
> - 上述示例中的第三个选项都是可选的，但除去它们将导致无法修复损坏的文件系统，此时需要从另一个 initramfs 启动。
> - 鼓励添加其他文件系统的条目。

## 初步测试

编辑 `/etc/mkinitcpio.conf` 并运行 `mkinitcpio -P` 重建所有 initramfs 镜像。 然后重启。

**如果不需要 `udev`**，第一次启动应该会成功。 如果出现故障（例如，Arch 找不到根分区或键盘失灵），则需要返回并从`default_options`行的`-S`参数中删除`udev`，然后再试一次。 如果需要使用 `udev`，请注意启动时间不会有明显改善，继续尝试只能作为学习经验。

## 整理模块

现在您已经有了一个已知正常的可启动 initramfs，是时候进一步精简 initramfs 了。通常是一次删除几个模块，重建 initramfs 映像，然后重新启动以查看一切是否仍然正常。若有功能异常，请使用 `fallback` initramfs 映像重启并重新添加已删除的模块，直到再次恢复正常。多次重复，直到只剩下所需的模块。 这可能是相对乏味的过程，因此提供了以下列表，以便减少前期工作。

> **注意：**以下仅作示例参考，并不意味着适合每台设备。

### 文件系统模块

> **注意：**只需要对应的模块：启动时需要检查文件系统的 `root` 设备和在 `/etc/fstab` 中的其他设备

- `ext[2,3,4]`
- `xfs`
- `jfs`
- `reiserfs`

### 存储设备模块

- `sd_mod` 用于所有 SCSI、SATA 和 PATA（IDE）设备
- `ahci` 用于现代 AHCI 控制器上的 SATA 设备
- `nvme` 和 `nvme_core` 用于 NVMe（M.2、PCI-E）设备
- `sata_*` 用于 IDE 模式控制器上的 SATA 设备
- `pata_*` 用于 PATA（IDE）设备
- `ehci_pci` 和 `usb_storage` 用于 USB 存储设备
- `virtio_blk` 和 `virtio_pci` 用于使用 VirtIO 进行存储的 QEMU/KVM 虚拟机

### 键盘模块

- `atkbd` 用于 AT 和 PS/2 键盘，以及 QEMU/KVM 中的模拟键盘。
- `hid_generic`、`ohci_pci`和 `usbhid` 用于普通 USB 键盘。
- `hid_logitech_dj`、`uhci_hcd` 和 `usbhid` 适用于使用罗技统一接收器的罗技 USB 键盘（**需要** {ic|udev}} **钩子**）。

## 收尾工作

将 initramfs 压缩到最小后，移除（或注释掉）`.preset` 文件中的 `normal_*` 行，并移除 `/boot` 中的 `initramfs-linux*-normal.img` 文件。

## 9.3　Dracut

*难度：进阶 ｜ 篇幅：13.6 KB ｜ 所属：Initramfs*

将要讲到：安装、用法、进阶配置、小提示、排障、参考

[dracut](https://dracut-ng.github.io) 会为内核创建初始映像，用于预载访问根文件系统所需的块设备模块（如 IDE、SCSI 或 [RAID](../../02_系统管理与内核/存储虚拟化/RAID.md)）。安装 [linux](https://archlinux.org/packages/?name=linux)包 时，可以在 [mkinitcpio](Mkinitcpio.md) 和 *dracut* 之间进行选择。Fedora、RHEL、Gentoo 和 Debian 等系统都使用 *dracut*，而Arch 默认使用[mkinitcpio](Mkinitcpio.md)。

你可以[在此](https://dracut-ng.github.io)查看 *dracut* 的完整项目文档。

## 安装

安装 [dracut](https://archlinux.org/packages/?name=dracut)包，你也可以安装 [dracut-git](https://aur.archlinux.org/packages/dracut-git/)AUR 使用最新的开发版本。

> **提示：**如果 *dracut* **测试后功能一切正常**，你可以[卸载](../包管理器/Pacman.md) [mkinitcpio](https://archlinux.org/packages/?name=mkinitcpio)包。

## 用法

`dracut` 的用法非常简单，而且通常就算在非标准环境下也不需要用户进行配置（如[在 LUKS 上配置 LVM](../../08_安全与加密/静态数据加密/Dm-crypt_加密整个系统.md#在LUKS上配置LVM)）。

使用如下命令为当前运行的内核生成 initramfs：

```
# dracut --hostonly --no-hostonly-cmdline --add-confdir no-network /boot/initramfs-linux.img
```

为了永久启用仅主机（hostonly）模式以便无需在命令行中进行指定，你可以在 dracut 的配置中添加：

```
/etc/dracut.conf.d/hostonly.conf
```

```
hostonly="yes"
hostonly_cmdline="no"
```

[dracut-git](https://aur.archlinux.org/packages/dracut-git/)AUR 已默认启用仅主机模式。

> **注意：**在某些情况下，特别是在首次安装系统的时候，无法使用上述命令。在这种情况下可以改用：
>
> ```
> # dracut -f --regenerate-all
> ```

使用如下命令生成后备 initramfs：

```
# dracut /boot/initramfs-linux-fallback.img
```

`/boot/initramfs-linux.img` 指代输出的镜像文件。如果你使用了其它内核，请考虑更改文件名。例如，对于 [linux-lts](https://archlinux.org/packages/?name=linux-lts)包 内核，输出文件应使用 `/boot/initramfs-linux-lts.img`。不过，只要[引导加载程序](../启动过程/Arch%20的启动流程.md)的配置使用了相同的文件名，就可以随意命名这些文件。

> **注意：**创建出的文件内嵌所有已安装的[微码](../../04_硬件与外设/CPU/微码.md)映像。

### 额外选项

`--force` 选项会覆写现有映像文件。

`--kver` 选项用于指定要使用的内核。该选项的输入需要匹配 `/usr/lib/modules` 目录下文件夹之一的名称。

其它选项可参考 [dracut(8)](https://man.archlinux.org/man/dracut.8)。

## 进阶配置

要注意的是，initial ramdisk 阶段有两种执行各种任务的不同方式：

***基于 Shell（bash/busybox/dash）*的 initial ramdisk：该方式会启动一个初始化脚本，然后扫描 initial ramdisk 的文件系统来查找要执行的 dracut 脚本。**

***基于 systemd（默认）*的 initial ramdisk：systemd 在 initial ramdisk 阶段时就已启动。具体要执行的任务由标准的 systemd 单元文件指定，相关信息可参考 [systemd 启动流程](https://www.freedesktop.org/software/systemd/man/latest/bootup.html)。**

这两种方式的主要区别在于 systemd dracut 模块的存在与否。详细信息请参考 [#dracut 模块](#dracut_模块)。

`dracut` 可通过直接传入命令行参数进行配置（参考 [dracut(8) § OPTIONS](https://man.archlinux.org/man/dracut.8#OPTIONS)）。如果你希望执行 `dracut` 命令时始终带上特定参数，可以在 `/etc/dracut.conf.d/` 目录下的 `.conf` 文件中进行指定。例如：

```
/etc/dracut.conf.d/myflags.conf
```

```
hostonly="yes"
compress="lz4"
add_drivers+=" i915 "
omit_dracutmodules+=" systemd network "
```

更多配置选项可参考 [dracut.conf(5)](https://man.archlinux.org/man/dracut.conf.5)。各个选项的完整说明可参考 [dracut(8)](https://man.archlinux.org/man/dracut.8)。在下文中会对部分常用选项进行说明。

### dracut 模块

*dracut* 使用模块化流程构建 initramfs（参考 [dracut.modules(7)](https://man.archlinux.org/man/dracut.modules.7)）。*dracut* 的所有内置模块位于 `/lib/dracut/modules.d`，可通过 `dracut --list-modules` 命令列出。更多模块可通过其它软件包提供（如 [dracut-sshd-git](https://aur.archlinux.org/packages/dracut-sshd-git/)AUR）。遗憾的是，*dracut* 的内置模块缺乏文档说明，尽管它们的名称通常不言自明。

部分模块默认被激活/禁用，可通过 `--add`/`--omit` 选项分别激活或禁用，也可以在配置文件中使用 `add_dracutmodules+=""` 或 `omit_dracutmodules+=""` 持久化更改：

```
/etc/dracut.conf.d/myflags.conf
```

```
# ...
add_dracutmodules+=" dracut modules to activate "
omit_dracutmodules+=" dracut modules to deactivate "
# ...
```

dracut 模块文档请参考[上游文档](https://dracut-ng.github.io/dracut-ng/modules/core.html)。

大多数 dracut 模块都依赖于其它 dracut 模块。例如，蓝牙 dracut 模块就依赖于 dbus dracut 模块。

#### TPM2

要使用 *systemd* 通过 [systemd-cryptenroll](../../08_安全与加密/静态数据加密/Systemd-cryptenroll.md#Trusted_Platform_Module) 调用 TPM2 解锁 *luks2* 加密卷的功能，需要安装 [tpm2-tools](https://archlinux.org/packages/?name=tpm2-tools)包 并启用 `tpm2-tss` *dracut* 模块。

### 早期内核模块加载

可以通过 `--force_drivers` 命令行选项或 `force_drivers+=""` 配置项启用 dracut 早期加载功能（在 initramfs 阶段通过 `modprobe` 加载）。例如：

```
/etc/dracut.conf.d/myflags.conf
```

```
# ...
force_drivers+=" nvidia nvidia_modeset nvidia_uvm nvidia_drm "
# ...
```

### 内核命令行参数

内核命令行选项可放置在 `/etc/dracut.conf.d/` 的 *.conf* 文件内，并通过 `kernel_cmdline=` 选项进行配置。Dracut 会自动读取配置，然后创建并写入到 initramfs `/etc/cmdline.d/` 目录下的 `01-default.conf` 文件中。举个例子，你的内核命令行选项文件内容可能如下：

```
/etc/dracut.conf.d/cmdline.conf
```

```
kernel_cmdline="rd.luks.uuid=luks-f6c738f3-ee64-4633-b6b0-eceddb1bb010 rd.lvm.lv=arch/root rd.lvm.lv=arch/swap root=/dev/arch/root rootfstype=ext4 rootflags=rw,relatime"
```

#### 其它说明

不需要为 `dracut` 指定根块设备。参考 [dracut.cmdline(7)](https://man.archlinux.org/man/dracut.cmdline.7)：

: 内核使用的根设备从来都是在启动配置文件的内核命令行选项中指定的。

不过，提前设置一些参数可能会很有用，而且还可以启用一些其它功能，如提示输入额外命令行参数。所有选项请参见 [dracut.cmdline(7)](https://man.archlinux.org/man/dracut.cmdline.7)。下面是一些配置选项示例：

- 从交换分区恢复：`resume=UUID=80895b78-7312-45bc-afe5-58eb4b579422`
- 提示输入额外内核命令行参数：{ic|1=rd.cmdline=ask}}
- 在设定了 `quiet` 的前提下输出更多信息：`rd.info`

### 统一内核映像

*dracut* 可以通过 `--uefi` 命令行参数或 `uefi="yes"` 配置项生成[统一内核映像](../启动加载程序/统一内核映像.md)。

## 小提示

### 查看生成映像的信息

你可以查看生成的映像的信息，并输出到单页上：

```
# lsinitrd /path/to/initramfs_or_uefi_image | less
```

该命令会列出生成映像时传入到 `dracut` 的参数、包含的 `dracut` 模块以及包含的所有文件。

### 修改压缩软件

要减少压缩生成映像所消耗的时间，可以更换使用的压缩软件。

> **警告：**请确保你的内核编译了所选软件对应的解压支持，否则系统将无法启动。你还需要安装所选压缩程序的软件包。

只需添加下列任意一行（不能多选）到 [dracut 配置文件](#进阶配置)中：

```
compress="cat"
compress="gzip"
compress="bzip2"
compress="lzma"
compress="xz"
compress="lzo"
compress="lz4"
compress="zstd"
```

默认使用的是 [gzip](https://archlinux.org/packages/?name=gzip)包。选择 `compress="cat"` 将不会压缩 initramfs。

你也可以使用非官方支持的压缩软件：

```
compress="program"
```

### 性能考虑

有些方法可以优化启动和生成 initramfs 的性能：

- 理解并配置最快的压缩方式。如果内核模块已经被压缩过，可能在生成 initramfs 时就不需要再次进行压缩。

- 理解在 initramfs 中添加 systemd 可能造成的影响。如果它会降低性能，就将其移除；如果会提升性能，就纳入进来。

- 在使用写时复制文件系统时，考虑使用 dracut-cpio。具体适用性请参考 `--enhanced-cpio` 选项。

- 减少 initramfs 中嵌入的内核模块和 dracut 模块的数量。例如：如果安装了 [nfs-utils](https://archlinux.org/packages/?name=nfs-utils)包，但不依赖其进行启动，就需要显式移除 nfs dracut 模块，否则在默认配置下生成的 initramfs 会启用网络启动 - 详细信息请参考 [https://github.com/dracut-ng/dracut-ng/pull/297](https://github.com/dracut-ng/dracut-ng/pull/297) 。

- 考虑使用 [busybox](https://archlinux.org/packages/?name=busybox)包 dracut 模块替换掉 bash。

- 考虑使用 `hostonly` 和 `hostonly_mode=strict`。

### 升级内核时生成新 initramfs

[linux](https://archlinux.org/packages/?name=linux)包 和 [dracut-git](https://aur.archlinux.org/packages/dracut-git/)AUR 包自带了用于在内核更新后自动生成新 initramfs 映像的 pacman 钩子。

> **提示：**
>
> - [dracut-ukify](https://aur.archlinux.org/packages/dracut-ukify/)AUR 软件包是使用 [systemd-ukify](https://archlinux.org/packages/?name=systemd-ukify)包 生成[统一内核映像](../启动加载程序/统一内核映像.md)的现代方法。与以下方法不同，你可以对整个内核映像[签名](../启动过程/UEFI_安全启动.md)，[包括 initramfs](https://0pointer.net/blog/brave-new-trusted-boot-world.html)。需要在 dracut 配置中使用 `uefi_secureboot_cert` 和 `uefi_secureboot_key` 选项（[dracut.conf(5)](https://man.archlinux.org/man/dracut.conf.5)）。
> - 另外，如果你希望 initramfs 映像也是 EFI 可执行文件（即 `*esp*/EFI/Linux/linux-*kernel*-*machine_id*-*build_id*.efi`），也可以使用 [dracut-uefi-hook](https://aur.archlinux.org/packages/dracut-uefi-hook/)AUR。在该目录下的 EFI 二进制文件会被 [systemd-boot](../启动加载程序/Systemd-boot.md) 自动检测到，因此不需要在 `/boot/loader/loader.conf` 中额外创建条目。

你还需要通过卸载 [mkinitcpio](https://archlinux.org/packages/?name=mkinitcpio)包 或使用以下命令来阻止 [mkinitcpio](Mkinitcpio.md) 创建和移除 initramfs 映像：

```
# ln -sf /dev/null /etc/pacman.d/hooks/90-mkinitcpio-install.hook
# ln -sf /dev/null /etc/pacman.d/hooks/60-mkinitcpio-remove.hook
```

### 蓝牙键盘支持

如果检测到了蓝牙键盘，dracut 会自动启用蓝牙模块。但 dracut 需要处于 hostonly 模式才能自动发现蓝牙键盘。

### Limine boot loader support

The [limine-dracut-support](https://aur.archlinux.org/packages/limine-dracut-support/)AUR package utilizes [limine-entry-tool](https://gitlab.com/Zesko/limine-entry-tool) with dracut and pacman hooks to automate the installation and removal of kernels and boot entries in the Limine [boot loader](../启动过程/Arch%20的启动流程.md). See Limine#Boot entry automation for more information.

## 排障

### 休眠

如果从休眠中恢复无效，你可能需要配置 `dracut` 以包含 `resume` 模块。[添加一个配置文件](#dracut_模块)：

```
/etc/dracut.conf.d/resume-from-hibernate.conf
```

```
add_dracutmodules+=" resume "
```

如果适用于你的系统，你可能也需要看下[从加密交换分区恢复指南](../../08_安全与加密/静态数据加密/Dm-crypt_交换分区加密.md#Using_a_swap_partition)以及 [dracut 特定指南](../../08_安全与加密/静态数据加密/Dm-crypt_交换分区加密.md#dracut)。

### LVM / 软 RAID / LUKS

如果内核无法自动发现并挂载 LVM / 软 RAID / LUKS 块设备，你可以加上以下内核命令行选项重新生成 initramfs：

```
rd.auto rd.lvm=1 rd.dm=1 rd.md=1 rd.luks=1
```

### A stop job is running for "brltty"

If you have issues booting or very long shutdown processes while the system waits for `brltty`, add the following to the dracut configuration line:

```
omit_dracutmodules+=" brltty "
```

Alternatively, uninstall [brltty](https://archlinux.org/packages/?name=brltty)包 if it is not needed.

### No usable keyslot is available

```
Cannot use whirlpool hash for keyslot encryption.
Keyslot open failed.
No usable keyslot is available.
```

A failure to boot with a message similar to the above typically will only require the user to include the `crypt` module via `add_dracutmodules`.

## 参考

- [Wikipedia:dracut (software)](https://en.wikipedia.org/wiki/dracut_(software))
- [Gentoo:Dracut](https://wiki.gentoo.org/wiki/Dracut)

## 9.4　Mkinitcpio

*难度：高级 ｜ 篇幅：39.1 KB ｜ 所属：Initramfs*

将要讲到：安装、创建和启用映像、配置、运行时配置、提示与技巧、疑难解答

[mkinitcpio](https://gitlab.archlinux.org/archlinux/mkinitcpio/mkinitcpio) 是用于创建 initramfs 映像的 bash 脚本。有关对 initramfs 的一般性介绍，请参阅 [Arch 的启动流程#initramfs](../启动过程/Arch%20的启动流程.md#initramfs)。

需要注意的是，存在两种截然的不同方式用于执行[早期用户空间](../启动过程/Arch%20的启动流程.md#早期用户空间)阶段的各项任务：

**基于 systemd 的 initramfs（版本 40 起默认）**

: systemd 在早期用户空间阶段开始时即被启动。需要执行的任务由常规的 systemd 单元文件决定。参见 [systemd 启动流程](https://www.freedesktop.org/software/systemd/man/latest/bootup.html)。

: **优点**：

- 与 systemd 生态系统的其余部分紧密集成，带来更一致、流畅的启动流程。
- 能更有效地并行化某些启动任务，在某些场景下可能缩短整体启动时间。
- 提供更全面的功能集，例如 [systemd-cryptsetup-generator](../../08_安全与加密/静态数据加密/Dm-crypt_系统配置.md#使用systemd-cryptsetup-generator) 与 [GPT 分区自动挂载](../初始化/Systemd.md#GPT分区自动挂载)功能。
: **缺点**：

- 依赖较多，生成映像较大。由于包含 systemd 二进制文件及其依赖项，通常会导致 initramfs 增大，可能会稍稍延长启动时间。

**基于 BusyBox 的 initramfs**

: 启动一个 init 脚本，该脚本随后扫描 initramfs 查找需要执行的脚本（即本文中的“运行时钩子”）。

: **优点**：

- 轻量级，依赖较少，生成映像较小。

具体采用哪种方案取决于 `/etc/mkinitcpio.conf` 的 `HOOKS` 数组中是否包含 `systemd` 钩子。详见 [#常用钩子](#常用钩子)。

mkinitcpio 由 Arch Linux 开发者及社区贡献者共同开发。参见[公开 Git 仓库](https://gitlab.archlinux.org/archlinux/mkinitcpio/mkinitcpio)。

## 安装

安装软件包 [mkinitcpio](https://archlinux.org/packages/?name=mkinitcpio)包。这个软件包是 [linux](https://archlinux.org/packages/?name=linux)包 等软件包的依赖，应该已经自动被安装了。高级用户可以从 [mkinitcpio-git](https://aur.archlinux.org/packages/mkinitcpio-git/)AUR 获取 mkinitcpio 的最新开发版本。

> **注意：**若要使用 git 开发版本，强烈建议同时加入 [arch-projects](https://lists.archlinux.org/mailman3/lists/arch-projects.lists.archlinux.org/) 邮件列表！

## 创建和启用映像

### 自动匹配生成

每次安装或升级内核时，一 [pacman 钩子](../包管理器/Pacman.md#钩子)会自动在 `/etc/mkinitcpio.d/` 生成 .preset 文件。例如，`linux.preset` 文件是官方稳定内核 [linux](https://archlinux.org/packages/?name=linux)包 生成的。.preset 文件本质上是一个创建 initramfs 映像所需信息的列表，避免手动指定各项参数及输出文件位置。

可自动创建两个映像：

- default（默认）映像，根据 [#配置](#配置)中的指令创建。
- fallback（后备）映像，与上述类似，但不使用 [autodetect 钩子](#常见钩子)，因此包含支持大多数硬件的完整模块集。

默认仅创建 default 映像，fallback 映像必须[显式启用](#生成_fallback_initramfs_映像)。

创建预设文件后，该 pacman 钩子会调用 `mkinitcpio`，利用 .preset 文件中提供的信息生成映像。

> **注意：**.preset 文件用于在内核更新后自动重新生成 initramfs，编辑时请务必小心。

### 手动生成

手动运行脚本，参照[mkinitcpio(8)](https://man.archlinux.org/man/mkinitcpio.8) 帮助页面获得指引。尤其是当利用内核包生成（重新生成）预设时，使用`-p`/`--preset`选项按照预设生成映像。例如 为 [linux](https://archlinux.org/packages/?name=linux)包 包生成映像，使用这个命令：

```
# mkinitcpio -p linux
```

利用`-P`/`--allpresets`选项，根据所有现存的预设文件生成映像。常见的用法是当全局设置[#配置](#配置)变化后用此法重新生成全部的内存映像：

```
# mkinitcpio -P
```

可以按照不同的配置文件创建任意数量的内存映像。想要引导加载的映像必须在相应的[引导加载程序](../启动过程/Arch%20的启动流程.md#引导加载程序)配置中明确写出。

### 自定义生成

可以使用相应的配置文件产生一个映像。例如，接下来会根据`/etc/mkinitcpio-custom.conf`里指令生成初始化内存映像，并且将后者保存在 `/boot/initramfs-custom.img`。

```
# mkinitcpio --config /etc/mkinitcpio-custom.conf --generate /boot/initramfs-custom.img
```

如果为一个不是当前运行的内核创建映像，增加内核版本号到命令行。已安装的内核版本号可以在`/usr/lib/modules/`中找到，对于每个内核语法结构和`uname -r`命令输出结果一致。

```
# mkinitcpio --generate /boot/initramfs-custom2.img --kernel 5.7.12-arch1-1
```

### 统一内核映像

mkinitcpio 既可自行构建[统一内核映像](../启动加载程序/统一内核映像.md)（UKI），也可通过 [systemd-ukify](https://archlinux.org/packages/?name=systemd-ukify)包 构建。如果系统中未安装 [systemd-ukify](https://archlinux.org/packages/?name=systemd-ukify)包 或使用 `--no-ukify` 参数明确禁用它，mkinitcpio 将使用自有实现，但此时将无法使用 ukify 提供的高级功能。

关于构建 UKI 的详细信息，请参阅[统一内核映像](../启动加载程序/统一内核映像.md)。

## 配置

**mkinitcpio**的主配置文件是`/etc/mkinitcpio.conf`。也支持附加配置文件（Drop-in），如`/etc/mkinitcpio.conf.d/mkhooks.conf`（当使用 `-c`/`--config` 选项和/或使用了含 `ALL_config` 的预设时无效）。此外，内核软件包的预配置文件位于`/etc/mkinitcpio.d`（例如：`/etc/mkinitcpio.d/linux.preset`）。

用户可以编辑配置文件中的七个变量，参见[mkinitcpio.conf(5) § VARIABLES](https://man.archlinux.org/man/mkinitcpio.conf.5#VARIABLES)获得更多细节：

**`MODULES`**
: 在钩子扩展运行前需要加载的内核模块。
**`BINARIES`**
: 内存盘映像中包含的额外的二进制文件（一般是可执行的命令）。
**`FILES`**
: 内存盘映像中包含的其他文件。
**`HOOKS`**
: 要执行的钩子扩展，它是在生成映像时执行的脚本。
**`COMPRESSION`**
: 内存盘映像的压缩方式。
**`COMPRESSION_OPTIONS`**
: 传递给压缩工具的额外参数，强烈不建议使用，*mkinitcpio* 可以自动根据压缩算法传递需要的参数(例如对 xz 传递 `--check=crc32` to xz), 手动设置了错误的参数可能导致系统无法启动。
**`MODULES_DECOMPRESS`**
: 解压可加载内核模块和固件文件或保持未解压原样。

> **注意：**
>
> - 一些系统需要的钩子像 **lvm2**, **mdadm_udev**, and **encrypt** 默认 **不是开启的** 。参见 [#钩子（HOOKS）](#钩子（HOOKS）) 仔细遵循指示。
> - 存在多个硬盘控制器使用相同的节点名字，但是拥有不同的内核模块（例如，两个SCSI/SATA控制器或2个IDE控制器）应该使用 持久化设备命名[persistent block device naming](../../04_硬件与外设/硬件检测和故障排除/块设备持久化命名.md) 保证正确的设备被挂载。否则在两次系统启动时根系统的位置可能改变导致内核崩溃。

### 模块（MODULES）

`MODULES`数组指定了启动过程最开始时就要加载的模块。

如果模块名称后加上一个`?`问号，那么即使系统无法找到该模块也不会报错。对于自己编译的内核，其中内置了某些模块，在这些模块在钩子或配置文件中显式的列出来时，该功能可能有用。

> **注意：**
>
> - 如果使用树外文件系统，且需要在早期用户空间挂载时（比如作为根文件系统），对应模块（如 **reiser4**）**必须**加入 `MODULES` 数组中。
> - 当使用 **encrypt** 或 **sd-encrypt** 钩子时,目标系统和运行*mkinitcpio*系统不同时，键盘模块和/或启动系统期间需要解锁LUKS设备的文件系统模块加入 `MODULES` 数组。例如，如果需要使用位于一个ext2系统的钥匙文件，但是运行*mkinitcpio*的系统中ext2文件系统没有被挂载，增加`ext2`。参见 [dm-crypt/System configuration#cryptkey](https://wiki.archlinux.org/title/Dm-crypt/System_configuration#cryptkey) 获得更多细节。
> - 如果使用USB3接口的键盘，希望用它来解锁LUKS设备。增加`usbhid xhci_hcd`。
> - 如果使用显示器连接扩展坞，可能需要加入你的显卡模块使初始化输出信息可见(例如，为大多数英特尔显卡加入 `i915` )。

### 二进制文件和普通文件（BINARIES、FILES）

这两个选项允许用户添加任何文件到映像中。`BINARIES`、`FILES`数组在钩子运行之前指定了要加入内存盘映像的文件，可以覆盖钩子扩展提供的文件。`BINARIES`中的二进制文件会自动放入一个标准的`PATH`路径，而且会自动加入可执行文件依赖的函数库。`FILES`中的文件则不进行上述处理，*as-is*直接原样放入映像。例如：

```
FILES=(/etc/modprobe.d/modprobe.conf)
BINARIES=(kexec)
```

注意 `BINARIES` 和 `FILES` 是 [Bash](../../07_开发与工具/命令行解释器/Bash.md)数组，配置支持多个选项，用空格隔开。

### 钩子（HOOKS）

`HOOKS`设置是此文件中最重要的设置。钩子是一系列的小脚本，描述哪些东西需要加入到映像里面。有些钩子还包含了运行组件，可以提供额外的行特定动作，像启动服务、构建存储栈的块设备等。钩子按照配置文件中`HOOKS`项中给出的顺序执行。

默认的`HOOKS`可以满足大部分简单的单硬盘系统。对于堆栈的或多块设备像 [LVM](../../02_系统管理与内核/存储虚拟化/LVM.md), [RAID](../../02_系统管理与内核/存储虚拟化/RAID.md), or [dm-crypt](../../08_安全与加密/静态数据加密/Dm-crypt.md) 等复杂根分区系统，请查看相应的 Wiki 页面，进行必要的进一步设置。

#### 构建时钩子

构建时钩子位于`/usr/lib/initcpio/install`，自定义的构建时钩子可能位于`/etc/initcpio/install/`。这些文件会在运行 mkinitcpio 时被包含进脚本，应该提供如下两个函数：`build` 和 `help`。`build` 函数描述了需要加入映像的模块、文件和二进制文件。mkinitcpio(8) 中的记录的 API 帮助添加这些项目。`help` 函数在钩子执行后输出描述信息。

列出可用的钩子列表：

```
$ mkinitcpio -L
```

使用 mkinitcpio 的 `-H`/`--hookhelp` 选项输出对于某一钩子的帮助。例如：

```
$ mkinitcpio -H udev
```

#### 运行时钩子

运行时钩子可以在`/usr/lib/initcpio/hooks`中找到自定义的运行时钩子在`/etc/initcpio/hooks/`。 对于任何运行时挂钩，都应该始终有一个同名的构建钩子，它会调用`add_runscript`将运行时挂钩添加到映像中。 这些文件是由早期用户空间中的BusyBox ash shell调用。除清理钩子以外（清理钩子是倒序执行的），它们始终将按照`HOOKS`设置中列出的顺序运行。 运行时钩子可能包含以下函数：

`run_earlyhook`: 一旦挂载了API文件系统并且已经解析了内核命令行，便会运行这个函数。 通常，从此处启动早期引导过程所需的额外守护程序（例如udev，它从早期引导过程的开始就被需要）。

`run_hook`: 这个函数在早期钩子挂接后不久便运行。 这是最常见的钩子特点，应在此处进行诸如堆叠块设备的组装之类的操作。

`run_latehook`: 此名称的函数在挂载根设备后运行。 应当谨慎地将其用于根设备的进一步设置，或用于挂载其他文件系统，例如`/usr`。

`run_cleanuphook`: 该名称的函数将尽可能晚地运行，并且以与配置文件的`HOOKS`数组中列出函数的相反顺序来运行。 这些钩子应该用于所有的最终清理操作，例如关闭由早期钩子启动的所有守护程序。

> **注意：**运行时钩子仅仅被BusyBox的init使用。**systemd** 钩子触发一个 systemd 基础的初始化, 它不需要运行任何运行时钩子，而是使用systemd单元。

#### 映像后处理钩子

映像后处理钩子（post hook）是位于 `/usr/lib/initcpio/post/`（软件包提供）和 `/etc/initcpio/post/`（自定义）目录下的可执行文件或 shell 脚本。这些文件会在（重新）生成映像后执行，以执行签名等附加任务。

每个可执行文件会按以下顺序接收参数：

1.  使用的**内核**（某些情况下可能为空）；
2.  生成的**initramfs 映像**位置；
3.  （可选）生成的**统一内核映像**位置。

此外，还会设置以下环境变量：`KERNELVERSION` 表示完整内核版本，`KERNELDESTINATION` 表示内核要想正常启动应位于的默认位置。

#### 常用钩子

下面是一个常见钩子和它们如何影响映像的创建以及运行时的次序。注意这个表格是不完整的，因为一些包会提供一些自定义的钩子。


| BusyBox init | systemd init | [构建时钩子](#构建时钩子) | [运行时钩子](#运行时钩子)（仅BusyBox init） |
| --- | --- | --- | --- |
| **base** | 设置所有初始目录并安装基础工具和库。除非清楚自己在做什么，否则请始终将此钩子置于首位，因为如果没有 **systemd** 钩子，该钩子是必需的，提供关键的 BusyBox init 程序。<br>当使用 **systemd** 钩子时，此钩子可选，仅提供一个 BusyBox 恢复 shell。启用 **base** 后，在[内核参数](../../02_系统管理与内核/内核/内核参数.md)中临时添加 `SYSTEMD_SULOGIN_FORCE=1` 使用该 shell。

 | – |
| **udev** | **systemd** | 增加 udevd, udevadm,和一小套udev规则进映像。 | 从内核中启动udev守护进程和uevents过程; 创建设备节点。因为不需要用户清晰的明确出必须设备这是简化的引导过程，推荐使用。 |
| **usr** | 增加支持`/usr` 在独立分区。参见[#/usr 放到单独分区](#/usr_放到单独分区) 获得更多细节。 | 在挂载真正根分区后挂载 `/usr` 分区 。 |
| **resume** | 添加`lzo`和`lz4`内核模块，以允许在使用编译时默认值以外的休眠映像压缩算法时恢复。添加 [systemd-hibernate-resume(8)](https://man.archlinux.org/man/systemd-hibernate-resume.8) 二进制文件以支持从通过 `HibernateLocation` UEFI 变量指定的休眠映像恢复。 | 试图从"suspend to disk" 状态重新开始。 参见 [休眠](../../02_系统管理与内核/电源管理/电源管理_挂起与休眠.md#休眠) 做进一步设置。 |
| **btrfs** | – | 设置必备模块能够让[Btrfs](../../02_系统管理与内核/文件系统/Btrfs.md) 支持Btrfs的多设备分区。需要安装 [btrfs-progs](https://archlinux.org/packages/?name=btrfs-progs)包 开启功能。Btrfs在单设备时此钩子不需要（`filesystems`足矣）。 | 当**udev** 钩子或**systemd**不存在时，运行 `btrfs device scan` 组装多设备Btrfs根文件系统。安装[btrfs-progs](https://archlinux.org/packages/?name=btrfs-progs)包 包使用此钩子。 |
| **autodetect** | 系统扫描后创建设备白名单从而减少内存映像的尺寸。确定校验进入的模块正确性和完整性。为了自动侦测发挥作用，必须放在其他子系统钩子之前。放在'autodetect'钩子之前的钩子将会被完整安装。 | – |
| **microcode** | 在未压缩的 initramfs 映像前添加适用于 Intel 和 AMD 处理器的早期[微码](../../04_硬件与外设/CPU/微码.md)更新文件。使用`/usr/lib/firmware/amd-ucode`和`/usr/lib/firmware/intel-ucode`（若可用），否则从`/boot/amd-ucode.img`和`/boot/intel-ucode.img`提取。

若在此之前 **autodetect** 钩子已调用，则只会添加对应处理器的微码更新文件。

此挂钩已经取代了弃用的`--microcode`参数和在预设文件中的`microcode`选项。这使得启动配置中微码的`initrd`参数行可以删除了，因为它们已被打包进主要的 initramfs 映像中。

 | – |
| **modconf** | 从`/etc/modprobe.d/`和`/usr/lib/modprobe.d/`中包含探测模块配置文件。 | – |
| **kms** | 为 [KMS 早启动](../../02_系统管理与内核/内核/内核级显示模式设置.md#KMS_早启动)添加 GPU 模块。另外还添加了某些笔记本电脑液晶面板中内置的隐私屏幕所需的模块。 | – |
| **keyboard** | 为键盘设备增加必要的模块。如果USB或串口键盘在早期用户空间起作用加入此钩子（可以输入密码或和shell交互）。有一个副作用，一些非键盘的输入设备也可能加进来，但这并不可靠。此钩子代替老的 *usbinput*钩子。

> **注意：**使用不同的硬件配置启动的系统（例如笔记本电脑连接外置键盘和内置键盘或[headless systems](https://en.wikipedia.org/wiki/Headless_computer)）时，此钩子需要放在**autodetect**之前才能在引导中使用键盘，例如使用`encrypt`钩子解密加密设备。

 | – |
| **keymap** | **sd-vconsole** | 自`/etc/vconsole.conf`加入特定的 [键盘映射keymap(s)](../../07_开发与工具/Linux%20控制台/Linux%20控制台_键盘配置.md#Persistent_configuration) 到映像。在早期用户空间如果使用[系统加密](../../08_安全与加密/静态数据加密/Dm-crypt_加密整个系统.md), 尤其全盘加密, 此钩子放在`encrypt`钩子之前。 | 在早期用户空间从 `/etc/vconsole.conf` 加载指定的键盘映射。 |
| **consolefont** | 自`/etc/vconsole.conf`中加入特定的 [终端字体](../../07_开发与工具/Linux%20控制台/Linux%20控制台.md#Persistent_configuration) 到映像。 | 在早期用户空间自`/etc/vconsole.conf`里，使用特定的终端字体。 |
| **block** | 加载块设备模块。若在 `autodetect` 挂钩后调用此挂钩，之添加系统中使用的块设备的模块。模块`ahci`、`sd_mod`、`usb_storage`、`uas`、`mmc_block`、`nvme`、`virtio_scsi`和`virtio_blk` 会无条件地添加 | – |
| **net** | *未实现* | 增加必要网络设备模块。必须安装 [mkinitcpio-nfs-utils](https://archlinux.org/packages/?name=mkinitcpio-nfs-utils)包 包, 参见 [#使用 net](#使用_net) | 支持NFS为基础的根文件系统。 |
| **dmraid** | *?* | 支持fakeRAID根设备。必须安装 [dmraid](https://archlinux.org/packages/?name=dmraid)包 包。注意如果你的控制器支持，更好选择是[mdadm](../../02_系统管理与内核/存储虚拟化/RAID.md) 联合 **mdadm_udev**钩子支持fakeRAID根设备。参见 [#使用 RAID 磁盘阵列](#使用_RAID_磁盘阵列) 。 | 使用`dmraid`定位组装fakeRAID块设备。 |
| **mdadm_udev** | 通过udev支持组装RAID阵列。必须安装 [mdadm](https://archlinux.org/packages/?name=mdadm)包 包。若在FakeRAID使用这个钩子推荐将 `mdmon` 加入 `BINARIES`. 参见 [#使用 RAID 磁盘阵列](#使用_RAID_磁盘阵列) 。 | – |
| **encrypt** | **sd-encrypt** | 增加`dm_crypt` 内核模块 和 `cryptsetup` 工具到映像。 必须安装[cryptsetup](https://archlinux.org/packages/?name=cryptsetup)包包。

> **注意：**启动时反复确认*keyboard*钩子放在解密设备前面。和/或当使用钥匙文件解锁时，存放钥匙文件的文件系统加入[#模块（MODULES）](#模块（MODULES）) 里。

 | 侦测和解锁加密根系统。参见 [#运行时配置](#运行时配置) 。

**sd-encrypt** 参见 [dm-crypt/系统配置#使用systemd-cryptsetup-generator](../../08_安全与加密/静态数据加密/Dm-crypt_系统配置.md#使用systemd-cryptsetup-generator)。

 |
| **lvm2** | 加入设备映射内核模块和`lvm` 工具到映像。 必须安装[lvm2](https://archlinux.org/packages/?name=lvm2)包 包。 如果根系统在[LVM](../../02_系统管理与内核/存储虚拟化/LVM.md)这是必须的钩子。 | – |
| **fsck** | 增加fsck二进制文件和特定系统帮助文件，允许fsck在挂载前处理根系统（和/usr分区如果有）。如果放在**autodetect**钩子前仅仅加载的定的帮助文件到根系统。强烈推荐使用此钩子，独立`/usr`分区需要它。强烈建议如果使用的此钩子同时要加入必要模块保证键盘在早期用户空间使用。

使用这个钩子需要在[内核参数](../../02_系统管理与内核/内核/内核参数.md)加入 `rw` 参数（[讨论](https://bbs.archlinux.org/viewtopic.php?pid=1307895#p1307895)）。参见 [fsck#启动时检查](../../02_系统管理与内核/文件系统/Fsck.md#启动时检查)。

 | – |
| **filesystems** | 除非在`MODULES`中加入了文件系统，不然此钩子是必须的。 | – |
| **acpi_override** | 从 `/usr/initcpio/acpi_override/` 和 `/etc/initcpio/acpi_override/` 添加 ACPI 机器语言文件（*.aml*）到早期未压缩的 initramfs 映像中，使得内核可以在启动早期重载 ACPI 表（比如 [DSDT](../../02_系统管理与内核/电源管理/DSDT.md)） | – |

### 压缩方式(COMPRESSION)

内核支持几种initramfs的压缩方式 - [gzip](https://archlinux.org/packages/?name=gzip)包, [bzip2](https://archlinux.org/packages/?name=bzip2)包, lzma, [xz](https://archlinux.org/packages/?name=xz)包, [lzo](https://archlinux.org/packages/?name=lzo)包, [lz4](https://archlinux.org/packages/?name=lz4)包 and [zstd](https://archlinux.org/packages/?name=zstd)包。mkinitcpio默认使用zstd压缩映像，注意zstd运行的多线程模式（使用了`-T0`选项，以侦测到内核数量作为线程数量。）

系统中的 `mkinitcpio.conf` 已经注释掉了各种 `COMPRESSION` 选项，要使用某个压缩方式，请取消前面的注释，并确认安装了相应的压缩工具包。不指定 `COMPRESSION` 选项会使用 zstd 压缩的 initramfs 文件。想要创建一个未压缩的映像，在配置中指定`COMPRESSION=cat` 或者在命令行使用 `-z cat`。

> **提示：**lz4 在高压缩率模式(`-9`)下映像有2.5的压缩比率，以付出最慢的单线程压缩速度为代价，可以获得最快的解压缩速度。lzo使用轻度较好的压缩模式后仍然有较快的解压速度。 zstd 提供多样解决方案, 通过参数使用多线程压缩方式和大范围压缩水平，参见 [zstd(1) § Operation modifiers](https://man.archlinux.org/man/zstd.1#Operation_modifiers)。 xz在其高压缩预设(`-9`)中以大约5的缩减因子实现了最小的体积，但代价是解压速度要慢得多。

### 压缩选项(COMPRESSION_OPTIONS)

还有一些额外的标准可以传递到 `COMPRESSION` 指定的程序，例如：

```
COMPRESSION_OPTIONS=(-9)
```

这个选项可以留空， *mkinitcpio* 会确保任何支持的压缩方法有必要的标志来产生一个可以工作的映像。

> **警告：**使用错误的参数选项可能导致**内核不能引导**，因为错误参数导致内核不能解压生成的压缩档。

使用默认的 zstd 压缩时，`--long`选项在节省自定义内核的空间（特别是在使用 EFI 系统分区作为`/boot`时的[双系统](../启动过程/Arch%20+%20Windows%20双系统.md)设置）非常有效。但是，内存有限的系统可能无法使用此选项解压缩 initramfs。还可能需要`-v` 选项来查看 initramfs 生成期间的详细信息。例如：

```
COMPRESSION="zstd"
COMPRESSION_OPTIONS=(-v -5 --long)
```

通过使用 xz 的 `-9e` 压缩级别并解压可加载内核模块和固件，可以实现最高（但最慢）的压缩：

```
COMPRESSION="xz"
COMPRESSION_OPTIONS=(-9e)
MODULES_DECOMPRESS="yes"
```

### 模块解压缩(MODULES_DECOMPRESS)

`MODULES_DECOMPRESS` 控制内核模块与固件文件是否在 initramfs 创建时解压缩。默认为 `no`。

Arch 使用 19 级 zstd 压缩[内核](../../02_系统管理与内核/内核/内核.md)模块和 [linux-firmware](https://archlinux.org/packages/?name=linux-firmware)包。 当为 initramfs 使用更高的压缩等级，设置`MODULES_DECOMPRESS="yes"` 可以更大幅度地减少 initramfs 的大小。这会导致在早期启动阶段更高的内存和 CPU 占用，对于内存有限、CPU 性能不足的系统有影响，因为内核将花费更多的时间来解压缩整个 initramfs 映像，而不是加载单个模块和固件时解压缩它们。

> **提示：**在 initramfs 生成过程接近结束时，所有剩余的 *.bz2*、*.gz*、*.lz4*、*.lzma*、*.lzo*、*.xz* 和 *.zst* 文件被移动到早期未压缩的 initramfs 映像以避免双重压缩。

## 运行时配置


运行时配置选项可以通过内核命令行传递到 `init` 以及某些钩子。内核命令行参数通常是由启动引导器提供。下面讨论的参数可以附加到内核命令行以改变默认行为。参见 [Arch 启动过程](../启动过程/Arch%20的启动流程.md)和[内核参数](../../02_系统管理与内核/内核/内核参数.md)以获取更多信息。

### 从基本钩子启动

**`root=`**
: 这是内核命令行中指定的最重要的参数，因为它决定了哪一个设备会被挂载为你的正确的 root 设备。mkinitcpio 允许不同的格式，详见[Persistent_block_device_naming#Kernel_parameters](../../04_硬件与外设/硬件检测和故障排除/块设备持久化命名.md#Kernel_parameters)

> **注意：**下面的选项会改变 `init` 在 initramfs 环境中的默认行为。参见 `/lib/initcpio/init` 获取更多信息。但当启用`systemd`钩子时，这些参数不起作用，因为来自`base`钩子的 `init` 被取代了。

**`break`**
: 如果`break` 或者 `break=premount` 被指定，`init` 会暂停启动过程（在加载钩子之后，但是在挂载根文件系统之前）然后启动一个交互式终端，一般用来排障。如果指定为`break=postmount`，这个终端会在根目录挂载后启动。退出这个 shell 之后可以继续正常的启动流程。

**`disablehooks=`**
: 在运行时可以通过添加 `disablehooks=hook1{,hook2,...}` 禁用某些钩子。例如

```
disablehooks=resume
```

**`earlymodules=`**
: 改变模块加载的顺序。可以通过 `earlymodules=mod1{,mod2,...}` 指定一些模块提前加载。 (例如，这可以用来确保多个网络接口的正确顺序。)

参见: [常规故障排除#系统启动问题](../../02_系统管理与内核/系统恢复/常规故障排除.md#系统启动问题)和 [mkinitcpio(8)](https://man.archlinux.org/man/mkinitcpio.8)。

### 使用 RAID 磁盘阵列

参见 [RAID#Configure mkinitcpio](../../02_系统管理与内核/存储虚拟化/RAID.md#Configure_mkinitcpio)。

### 使用 net

> **警告：**NFSv4 仍然不被支持 [FS#28287](https://bugs.archlinux.org/task/28287)。

**必须的包**

`net` 需要 [mkinitcpio-nfs-utils](https://archlinux.org/packages/?name=mkinitcpio-nfs-utils)包 包。

**内核参数**

```
内核文档包含最新的信息和清晰的说明。
```

**ip=**

这个参数告诉内核怎样生成设备IP地址和怎样安装路由表。它最多可选9个参数： `ip=<client-ip>:<server-ip>:<gw-ip>:<netmask>:<hostname>:<device>:<autoconf>:<dns0-ip>:<dns1-ip>:<ntp0-ip>`。

如果这些参数在内核命令行缺失，所有区域将被认为是空白，则根据 [kernel documentation](https://docs.kernel.org/admin-guide/nfs/nfsroot.html) 使用默认参数。通常这就意味这内核利用自动配置表来配置。

`<autoconf>` 参数单独作为 `ip` 的值出现 (没有上面提及的任一参数)。如果值是 `ip=off` 或 `ip=none`, 将不会执行自动配置，否则会执行自动配置。最常见的是`ip=dhcp`.

获取参数具体说明，参见 [kernel documentation](https://docs.kernel.org/admin-guide/nfs/nfsroot.html).

*例子：*

```
 ip=127.0.0.1:::::lo:none  --> 启用 loopback 接口。
 ip=192.168.1.1:::::eth2:none --> 启用静态 eth2 接口。
 ip=:::::eth0:dhcp --> 为 eth0 启用 dhcp。
```

> **注意：**请确保使用内核设备命名 (例如 *eth0*) 作为 `<device>` 参数, 不要使用 [网络配置#Network interfaces](../../03_网络与服务/网络/网络配置.md#Network_interfaces) 命名 (例如 *enp2s0*) 因为这个不起作用。

**BOOTIF=** 使用多个网卡的时候，此参数可以指定要使用网卡的 Mac 地址。在接口号可能变化或与 IPAPPEND 2 、IPAPPEND 3 选项共用时比较有用。默认使用 eth0.

示例：

```
BOOTIF=01-A1-B2-C3-D4-E5-F6  # 注意前缀加"01-" 并且字母要大写。
```

**nfsroot=**

如果命令行中没有给出 `nfsroot` 参数，那么默认的 `/tftpboot/%s` 会被使用。

```
 nfsroot=[<server-ip>:]<root-dir>[,<nfs-options>]
```

运行`mkinitcpio -H net` 查看参数详细解释。

### 使用 lvm

如果你的根分区在 [LVM](../../02_系统管理与内核/存储虚拟化/LVM.md)文件系统中, 参见 [Install Arch Linux on LVM#Adding mkinitcpio hooks](https://wiki.archlinux.org/title/Install_Arch_Linux_on_LVM#Adding_mkinitcpio_hooks).

### 使用加密根目录

如果使用[加密根目录](https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system)，请参考 [Dm-crypt/System configuration#mkinitcpio](https://wiki.archlinux.org/title/Dm-crypt/System_configuration#mkinitcpio)。

### /usr 放到单独分区

如果将 /usr 放在单独分区，必须满足：

- 添加 `fsck` 钩子, `/usr`在`/etc/fstab`分区表中的`passno` 参数设置为`2` ，以便启动时检查该分区。作为常规推荐 如果想要`/usr`分区可以在启动时被检查，以上设置是中肯的。没有这个钩子 `/usr` 永远不能被检查（fsck）。

- 如果没使用systemd钩子，请添加`usr`钩子。这样将会在挂载根系统后挂载`/usr` 分区。

## 提示与技巧

### 生成 fallback initramfs 映像

自 mkinitcpio 版本 40 起，默认不生成 fallback initramfs 映像。若需要启用：

- 调整 `/etc/mkinitcpio.d/` 目录下相应的 **.preset** 文件：
    - 注释掉 `PRESETS=('default')` 行，并取消注释 `PRESETS=('default' 'fallback')` 行
    - 取消注释 `fallback_image="/boot/initramfs-linux-fallback.img"`
    - 取消注释 `fallback_options="-S autodetect"`
- 重新生成 initramfs。
- 更新[引导加载程序](../启动过程/Arch%20的启动流程.md)配置。

> **警告：**缺少后备 initramfs 可能会使您在默认 initramfs 启动失败时，失去另一个启动系统的选项。请务必手头准备一个可启动的安装介质以备救援之用。

## 疑难解答

### 解压缩映像

如果你对 initrd 映像的内容感到好奇，你可以解压缩它然后看看里面的文件。

initrd 映像是一个 SVR4 CPIO 压缩包，通过 `find` 和 `bsdcpio` 命令生成，利用可被内核理解的压缩方式被有选择的压缩，参考[#压缩方式(COMPRESSION)](#压缩方式(COMPRESSION))。

mkinitcpio 包含了一个叫做 [lsinitcpio(1)](https://man.archlinux.org/man/lsinitcpio.1) 的工具，可以列出和/或解压缩出 initramfs 映像的内容。

你可以用下面的命令列出映像中的文件：

```
# lsinitcpio /boot/initramfs-linux.img
```

然后全部解压缩到当前文件夹：

```
# lsinitcpio -x /boot/initramfs-linux.img
```

你也可以用更对人类友好的方式列出映像中的重要的部分：

```
# lsinitcpio -a /boot/initramfs-linux.img
```

### 重新压缩解压之后修改过的映像

激活`/usr/bin/mkinitcpio`脚本的 `build_image` 功能使用此参数：

```
build_image outfile compression
```

将会产生一个拥有`build_image`功能的新脚本，用上面的参数运行它（*outfile*输出文件名字，*compression*压缩工具），将会把当前目录下的内容压缩到一个名字为`*outfile*`的新文件里。

> **警告：**建议在运行上面的命令产生一个新的`/boot/initramfs-linux.img`之前把现有的映像重命名作为备份，以便情况不妙时有后悔药吃。如果因为失误导致系统无法启动，你需要用fallback映像或者boot CD启动，然后运行mkinitcpio来overwrite掉之前做的更改，或者你自己修好它然后重新压缩映像。

### "/dev must be mounted" when it already is

*mkinitcpio*使用这个检测通过判断`/dev/fd/` 是否存在来判断`/dev`是否被挂载。如果其他一切看起来都很好，可以通过以下方式手动“创建”：

```
# ln -s /proc/self/fd /dev/
```

(显然, `/proc` 必须被安装. *mkinitcpio* 无论如何要求这样,指示它要检测的下一件事。)

### Possibly missing firmware for module XXXX

当内核更新后，initramfs映像被重新构建时，你可能得到以下警告：

```
==> WARNING: Possibly missing firmware for module: wd719x
==> WARNING: Possibly missing firmware for module: aic94xx
==> WARNING: Possibly missing firmware for module: xhci_pci
```

如果产生 *default*映像时出现这些或相近的信息，那么按照警告要求安装额外的被需要的固件。绝大部分固件通过安装安装 [linux-firmware](https://archlinux.org/packages/?name=linux-firmware)包包获得。其他提供固件的包可以查看下表或在[官方仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)或 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 中搜索固件模块名字。

除此以外，警告信息只出现在 *fallback* 映像生成时，可以按照下面的建议来：

- 如果不需要使用缺失模块的硬件，可以安全地忽略这些警告。
- 如果想要消除这些警告，安装缺失的固件，元包 [mkinitcpio-firmware](https://aur.archlinux.org/packages/mkinitcpio-firmware/)AUR 包括绝大部分可选的固件。也可以手动安装需要的包：

: | 模块 | 包 |
| --- | --- |
| aic94xx | [aic94xx-firmware](https://aur.archlinux.org/packages/aic94xx-firmware/)AUR |
| ast | [ast-firmware](https://aur.archlinux.org/packages/ast-firmware/)AUR |
| bfa | [linux-firmware-qlogic](https://archlinux.org/packages/?name=linux-firmware-qlogic)包 |
| bnx2x | [linux-firmware-broadcom](https://archlinux.org/packages/?name=linux-firmware-broadcom)包 |
| liquidio | [linux-firmware-liquidio](https://archlinux.org/packages/?name=linux-firmware-liquidio)包 |
| mlxsw_spectrum | [linux-firmware-mellanox](https://archlinux.org/packages/?name=linux-firmware-mellanox)包 |
| nfp | [linux-firmware-nfp](https://archlinux.org/packages/?name=linux-firmware-nfp)包 |
| qat_420xx | [linux-firmware-intel](https://archlinux.org/packages/?name=linux-firmware-intel)包 |
| qed | [linux-firmware-qlogic](https://archlinux.org/packages/?name=linux-firmware-qlogic)包 |
| qla1280 | [linux-firmware-qlogic](https://archlinux.org/packages/?name=linux-firmware-qlogic)包 |
| qla2xxx | [linux-firmware-qlogic](https://archlinux.org/packages/?name=linux-firmware-qlogic)包 |
| wd719x | [wd719x-firmware](https://aur.archlinux.org/packages/wd719x-firmware/)AUR |
| xhci_pci<br>xhci_pci_renesas | [upd72020x-fw](https://aur.archlinux.org/packages/upd72020x-fw/)AUR |

如果想去掉警告，但不想浪费磁盘空间在不需要的固件包上，可以禁用 [#生成 fallback initramfs 映像](#生成_fallback_initramfs_映像)。

对于不可用的固件，也可以通过创建占位文件来抑制警告，例如：

```
# echo "设备不可用" > /usr/lib/firmware/qat_420xx.bin
# echo "设备不可用" > /usr/lib/firmware/qat_420xx_mmp.bin
```

### 没有发现 PS/2 控制器

在一些主板（主要是早期的但是也有一些新的主板）， i8042 控制器不能被自动侦测到。这很罕见，但是一些人确定没有键盘使用。 可以提前侦测到这种情况。PS/2 接口得到 `i8042: PNP: No PS/2 controller found. Probing ports directly` 信息，加 **atkbd** 到 `MODULES` 数组。

### 标准急救过程

使用不正确的初始化内存映像盘经常出现不可引导。因此遵守以下的系统急救过程：

#### 在一台机器引导成功，但在另一台引导失败

在主要映像 initramfs 扫描 `/sys` 时 *mkinitcpio*的 `autodetect` 钩子 过滤掉了 [kernel modules](../../02_系统管理与内核/内核/内核模块.md) ，这些模块在启动被调用。 如果转移`/boot` 目录到其他机器，在早期用户空间造成启动失败，原因是新的硬件由于缺失内核模块不能被侦测到。注意USB 2.0 和 3.0 需要不同的模块。

修正这个缺陷，首先试着从引导记录[bootloader](../启动过程/Arch%20的启动流程.md) 中选择 [fallback](#创建和启用映像) 映像 ，因为这个映像不使用`autodetect`过滤模块。 一旦引导成功，在新机器上运行*mkinitcpio*用正确的模块重建主要映像。如果fallback映像失败，试着用 Arch Linux live CD/USB，使用 arch-chroot 进入系统中, 在新机器上运行 *mkinitcpio* 。最后的手段，手工增加模块到\[#MODULES|手动\]\]映像中。

### 无法访问控制台，root 用户被锁定

`systemd` 钩子[会禁用 root 用户](https://gitlab.archlinux.org/archlinux/packaging/packages/systemd/-/commit/292cdf8a2f7dd7c6c7d91d2b59617391935c837c)。要启用紧急 shell，请临时在[内核参数](../../02_系统管理与内核/内核/内核参数.md)中添加 `SYSTEMD_SULOGIN_FORCE=1`。

或者，也可以使用 [initcpio-hook-shadowcopy](https://aur.archlinux.org/packages/initcpio-hook-shadowcopy/)AUR。安装后，在 `/etc/mkinitcpio.conf` 中将 `shadowcopy` 钩子置于 `systemd` 之后，并使用 `mkinitcpio -P` 重新生成 initramfs。更多文档请参见其 [GitHub 仓库](https://github.com/iTrooz/initcpio-hook-shadowcopy)。

## 另见

- Linux Kernel documentation on [initramfs, "What is rootfs?"](https://docs.kernel.org/filesystems/ramfs-rootfs-initramfs.html#what-is-rootfs)
- Linux Kernel documentation on [initrd](https://docs.kernel.org/admin-guide/initrd.html)
- Wikipedia article on [initrd](https://en.wikipedia.org/wiki/initrd)
