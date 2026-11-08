# 第 8 章　CPU

> 本章收拢 CPU 在 Arch 上的三件事：微码要不要更新、频率怎么调、风扇怎么转。建议先读《微码》，它的安装、加载与「启动时是否已更新」的验证是所有平台共同的前提；Ryzen 平台再补《AMD Ryzen》，看微码支持、微调与编译内核；随后按需读《CPU 调频》，借用户空间工具、调频驱动与调速器在功耗和噪音之间取舍；《风扇转速控制》篇幅最长，Fancontrol、NBFC 以及 Dell、ThinkPad 的专门做法，只在风扇确实吵人时才需要翻。
> 
> 最容易踩的坑是把「能装」当成「已生效」：微码要用专门的办法确认启动时真的更新过，调频也不是装个工具就完事，得先看调频驱动与调速器是否对得上；笔记本风扇另有厂商专有的路子，通用做法未必奏效。

<!-- 本章 4 节，共 82 KB -->

## 8.1　AMD Ryzen

*难度：进阶 ｜ 篇幅：10.1 KB ｜ 所属：CPU*

将要讲到：启用微码支持、微调 Ryzen、编译内核、疑难解答、另见

## 启用微码支持

安装 [amd-ucode](https://archlinux.org/packages/?name=amd-ucode)包 包以启用微码更新，并在[微码](微码.md)页面的帮助下启用它。这些更新提供了对系统稳定性至关重要的 Bug 修复。尽管它是专有产权的，仍然**强烈建议**使用。

## 微调 Ryzen

### 监视电压、电源和温度

[lm_sensors](https://archlinux.org/packages/?name=lm_sensors)包 可以开箱即用来监视温度。但是电量消耗及电压等更详细的信息需要 [zenpower3-dkms](https://aur.archlinux.org/packages/zenpower3-dkms/)AUR。也有图形用户界面的工具，如 [zenmonitor](https://aur.archlinux.org/packages/zenmonitor/)AUR 和对于 Zen 3 CPU 的 [zenmonitor3-git](https://aur.archlinux.org/packages/zenmonitor3-git/)AUR。

### 电源管理、降压和超频

- **RyzenAdj** — RyzenAdj 是一个用来调节 Ryzen 移动处理器电源管理设置的命令行工具。

: [https://github.com/FlyGoat/RyzenAdj](https://github.com/FlyGoat/RyzenAdj) || [ryzenadj-git](https://aur.archlinux.org/packages/ryzenadj-git/)AUR

- **amdctl** — amdctl 是一个 AMD CPU 的降、超（升）频率和电压的命令行工具，目前支持 AMD CPU 的 10h, 11h, 12h, 15h, 16h, 17h 和 19h 家族。

: [https://github.com/kevinlekiller/amdctl/](https://github.com/kevinlekiller/amdctl/) || [amdctl](https://aur.archlinux.org/packages/amdctl/)AUR

- **ZenStates-Linux** — ZenStates 是一个调节时钟频率和电压的命令行工具。一个详细的设置例子可以在 [Level1Techs 论坛](https://forum.level1techs.com/t/overclock-your-ryzen-cpu-from-linux/126025) 找到。

: [https://github.com/r4m0n/ZenStates-Linux](https://github.com/r4m0n/ZenStates-Linux) || [zenstates-git](https://aur.archlinux.org/packages/zenstates-git/)AUR

- **ryzen_smu** — Ryzen SMU 是一个 Linux 内核驱动程序，它可以让用户访问某些 AMD Ryzen 处理器的 SMU（系统管理单元）。[这](https://github.com/svenlange2/Ryzen-5800x3d-linux-undervolting/) 是一个给 5800x3D 降压的例子。

: [https://gitlab.com/leogx9r/ryzen_smu](https://gitlab.com/leogx9r/ryzen_smu) || [ryzen_smu-dkms-git](https://aur.archlinux.org/packages/ryzen_smu-dkms-git/)AUR

### AMD 3D V-Cache 调度器 (多CCD CPU)

在具有非对称CCD设计的CPU（比如7950X3D, 9950X3D），一个CCD具有3D V-Cache （更大的L3，有利于游戏性能）而另一个则具有更高的频率。 `amd_x3d_vcache` 驱动（6.13以上内核）对外通过sysfs提供对调度器的微调。

首先，在主板的UEFI固件设置中将**CPPC Dynamic Preferred Cores**设定为 `Driver` （对于微星主板： `OC -> Advanced CPU Configuration -> AMD CBS -> CPPC Dynamic Preferred Cores`），否则sysfs的调节不会起作用。

推荐的设置是将系统置为 `frequency` 模式（即默认），系统和后台应用将偏好非X3D的CCD，并将游戏绑定到X3D核心:

```
# echo frequency > /sys/bus/platform/drivers/amd_x3d_vcache/AMDI0101:00/amd_x3d_mode
```

在`frequency` 模式，X3D 核心有**最低**的优先级，可以以此判断哪些是X3D核心：

```
$ grep -r  /sys/devices/system/cpu/cpu*/cpufreq/amd_pstate_prefcore_ranking
```

例如9950X3D的cpu0–7和 cpu16–23为X3D核心。对于**Linux** **原生游戏**，可以通过Steam启动选项将他们绑定到这些核心：

```
taskset -c 0-7,16-23 %command%
```

对于通过**Proton/Wine** **运行的游戏**, 使用 `WINE_CPU_TOPOLOGY` 代替：

```
WINE_CPU_TOPOLOGY=16:0,1,2,3,4,5,6,7,16,17,18,19,20,21,22,23 %command%
```

格式为 `<``线程数>:<逗号分隔的CPU列表>`。

## 编译内核

关于启用 Ryzen 支持，参考 [Gentoo:Ryzen#Kernel](https://wiki.gentoo.org/wiki/Ryzen#Kernel)。[官方支持的内核](../../02_系统管理与内核/内核/内核.md#官方支持的内核)中已默认包含了所需的配置。

## 疑难解答

### 随机重启

如果你遇到了随机重启的问题，请参考 [Gentoo:Ryzen#Random_reboots_with_mce_events](https://wiki.gentoo.org/wiki/Ryzen#Random_reboots_with_mce_events)。Ryzen 5000 系列，特别是 5950X 和 5900X 这两款高端型号，在 Linux 下似乎有一些轻微的不稳定问题，可能与 5.11+ 的内核有关，如[这个内核 bug](https://bugzilla.kernel.org/show_bug.cgi?id=212087) 所示。根据网上的报告，似乎 Windows 默认会让 CPU 运行在更高的电压和更低的峰值频率，而 Linux 内核则相反，这取决于你的硅片抽签结果，可能会导致一些随机的应用程序崩溃或硬件错误，从而导致重启。你可以通过 dmesg 日志来识别这些问题，例如：

```
kernel: mce: [Hardware Error]: Machine check events logged
kernel: mce: [Hardware Error]: CPU 22: Machine Check: 0 Bank 1: bc800800060c0859
kernel: mce: [Hardware Error]: TSC 0 ADDR 7ea8f5b00 MISC d012000000000000 IPID 100b000000000
kernel: mce: [Hardware Error]: PROCESSOR 2:a20f10 TIME 1636645367 SOCKET 0 APIC d microcode a201016
```

CPU ID 和处理器编号可能会不同。要解决这个问题，你需要给你的 CPU 提供更高的电压，以便它在高频运行时保持稳定。最简单的方法是使用 AMD 曲线优化器，这是一个可以通过你的主板 UEFI 访问的功能。进入 UEFI 并设置一个正向的 4 点偏移，这会增加你的 CPU 在高负载时的电压。这会限制超频的潜力，因为需要更好的散热，但它会运行得更稳定。更多细节请查看[这个论坛帖子](https://community.amd.com/t5/processors/ryzen-5900x-system-constantly-crashing-restarting-whea-logger-id/td-p/423321/page/84)。当我对我的 5950X 这样做时，我的处理器稳定了，频率和电压的范围也更接近 Windows 下的情况。

### 系统挂起

在类似 [#随机重启](#随机重启)的情况下，系统可能会在重负载或特定的低负载下挂起，即使在应用了修复措施后也是如此。重置按钮或强制关机不起作用，外设电源关闭，视频输出可能会停止，拔掉电源是唯一的方法。

将频率降低到非超频标准并增加 CPU 或 RAM 的电压可能会有所帮助，但可能无法解决问题。一个潜在的解决方法是更新 UEFI（也被误称为“BIOS 更新”），然后应用 PBO+4 曲线以提高高频下的稳定性。根据硅片抽签结果，某些 CPU 可能需要 +6 或更高的偏移量。

### 屏幕撕裂（APU）

如果你正在使用 [Xorg](../../05_桌面与图形/X%20服务器/Xorg.md) 并遇到了屏幕撕裂的问题，请查看：[AMDGPU#无撕裂渲染](../../05_桌面与图形/图形/AMDGPU.md#无撕裂渲染)。

### 软锁冻结

这个 bug 已经很有名了，目前正在 [bugzilla](https://bugzilla.kernel.org/show_bug.cgi?id=196683) 和 [launchpad](https://bugs.launchpad.net/ubuntu/+source/linux/+bug/1690085) 上讨论。虽然解决方案并不是在所有情况下都一样，但[这个](https://bugs.launchpad.net/linux/+bug/1690085/comments/69)帮助了一些用户。把这个命令 `echo rcu_nocbs=0-$(($(nproc)-1))` 的输出作为一个内核参数添加，其中命令 `nproc` 只是打印出你的 CPU 的线程数。要应用这个选项，你需要一个带 `CONFIG_RCU_NOCB_CPU` 选项编译过的内核（比如 [linux](https://archlinux.org/packages/?name=linux)包）。

导致冻结的另一个原因是电源管理中的 c-states。最大的省电状态 c6 可能会导致问题。在一些情况下，添加内核参数 `processor.max_cstate=5` 会有帮助，但其他用户报告说这个选项没有应用，c6 状态仍然被进入。对他们来说，这个包 [disable-c6-systemd](https://aur.archlinux.org/packages/disable-c6-systemd/)AUR 会有帮助。在使用它之前，需要运行 `modprobe msr` 来激活那个内核模块。

一些带有 Ryzen CPU 的笔记本电脑，比如 HP Envy x360 15-bq100na，可能会遇到 CPU 软锁定，导致系统冻结。可以通过添加内核参数 `idle=nomwait` 来避免。

在一些情况下，内核参数 `pci=nomsi` 可以修复问题。

在其他一些情况下，问题只是硬件不好，保修换一个新的 CPU 可能就能解决你的问题。

### 关机、重启、挂起时卡住

> **注意：**通过更新 UEFI（也被误称为“BIOS 更新”）获取的较新 AGESA 固件可能已经修复了这个问题。

这似乎与 C6 c-state 有关，它在 Linux 中似乎并不受到很好的支持（如果有的话）。

要解决这个问题，进入 UEFI 设置并搜索一个标记为类似于“电源空闲控制”的选项。将其值更改为“典型空闲电流”。请注意，这些名称取决于主板制造商对它们的命名，因此在您的特定情况下可能会有所不同。

其他不太理想的解决方案包括在 UEFI 设置中禁用 c-states 或在内核命令行参数中添加 `processor.max_cstate=1`。

## 另见

- [Gentoo:Ryzen](https://wiki.gentoo.org/wiki/Ryzen)

## 8.2　微码

*难度：进阶 ｜ 篇幅：15.3 KB ｜ 所属：CPU*

将要讲到：安装、加载微码、验证微码在启动时是否已更新、参见、哪些 CPU 接受微码更新、参见

处理器制造商会发布对处理器[微码](https://zh.wikipedia.org/wiki/%E5%BE%AE%E7%A8%8B%E5%BA%8F)的稳定性和安全性更新。这些更新提供了对系统稳定性至关重要的错误修复。如果没有这些更新，则可能会遇到不明原因的崩溃或难以跟踪的意外停机。

使用 AMD 或 Intel CPU 的用户都应该安装微码更新以确保系统稳定性。在虚拟机或容器中，微码更新应在主机上实施，而不是在客户机系统中。

## 安装

要获取更新的微码，根据处理器类型，安装以下其中一个软件包：

- [amd-ucode](https://archlinux.org/packages/?name=amd-ucode)包 用于 AMD 处理器，
- [intel-ucode](https://archlinux.org/packages/?name=intel-ucode)包 用于 Intel 处理器。

[mkinitcpio](../../01_安装与包管理/Initramfs/Mkinitcpio.md) 和 [dracut](../../01_安装与包管理/Initramfs/Dracut.md) 默认会生成[组合的 initramfs 文件](#微码initramfs与主initramfs打包在一个文件中)。微码将在启动时自动加载。[Booster](../../01_安装与包管理/Initramfs/Booster.md) [不支持](https://github.com/anatol/booster/issues/40)生成组合的 initramfs，请参阅[#微码在单独的initramfs文件中](#微码在单独的initramfs文件中)中所需的引导加载程序配置。

## 加载微码

微码更新通常与主板的固件一起发布，并在固件初始化期间应用。由于OEM可能不会及时发布固件更新，且旧系统根本不会获得新的固件更新，Linux内核增加了在启动期间应用CPU微码更新的功能。[Linux微码加载器](https://docs.kernel.org/arch/x86/microcode.html)支持三种加载方法：

1.  **内置微码**可以编译到内核中，然后由早期加载器应用。
2.  **早期加载**在启动的非常早期阶段（在initramfs阶段之前）更新微码，比晚期加载更受推荐。这对于具有严重硬件错误的CPU（如Intel Haswell和Broadwell处理器系列）是强制性的。
3.  **晚期加载**（[可能](https://github.com/torvalds/linux/commit/9407bda845dd19756e276d4f3abc15a20777ba45)是[危险的](https://docs.kernel.org/arch/x86/microcode.html#why-is-late-loading-dangerous)）在启动后更新微码，这可能为时已晚，因为CPU可能已经尝试使用有缺陷的指令。即使已经使用早期加载，晚期加载仍然可以用于应用更新的微码更新，而无需重新启动。

### 早期加载

内核的早期加载器期望在未压缩的CPIO存档（initramfs镜像）中的`/kernel/x86/microcode/GenuineIntel.bin`或`/kernel/x86/microcode/AuthenticAMD.bin`中找到微码更新文件。

早期initramfs镜像可以与主initramfs镜像合并为一个文件，并作为单个initramfs传递给内核（通过引导加载程序的`initrd=`内核命令行选项或在打包为统一内核镜像时），或者它可以作为单独的文件存在，在这种情况下需要使用多个`initrd=`内核命令行选项。在这两种情况下，包含微码的未压缩CPIO存档**必须**放在主initramfs之前。

请注意，由于用户早期启动配置的广泛多样性，微码更新可能不会由Arch的默认配置自动触发。

#### 自定义构建的内核

为了使早期加载在自定义内核中工作，需要将“CPU微码加载支持”编译到内核中，**而不是**编译为模块。这将启用“早期加载微码”提示，应将其设置为`Y`。

```
CONFIG_BLK_DEV_INITRD=Y
CONFIG_MICROCODE=y
CONFIG_MICROCODE_INTEL=Y
CONFIG_MICROCODE_AMD=y
```

#### 微码initramfs与主initramfs打包在一个文件中

未压缩的微码CPIO可以[预置到initramfs中](https://docs.kernel.org/arch/x86/microcode.html#early-load-microcode)并用作单个initramfs文件。此方法优于[#Microcode in a separate initramfs file](#Microcode_in_a_separate_initramfs_file)，因为不需要额外的引导参数配置。

> **提示：**您可以选择将`boot/*-ucode.img`添加到`/etc/pacman.conf`中的[NoExtract](../../01_安装与包管理/包管理器/Pacman.md)，因为微码文件将直接从`/usr/lib/firmware/*-ucode/`中获取。

##### mkinitcpio

为了使[mkinitcpio](../../01_安装与包管理/Initramfs/Mkinitcpio.md)生成包含微码的initramfs文件，请确保`microcode`在`/etc/mkinitcpio.conf`中的`HOOKS`数组中。

如果`autodetect`钩子位于`microcode`之前，则仅包含当前CPU的微码。要包含系统上可以找到的所有CPU微码文件，请将`microcode`钩子移动到`autodetect`之前或完全删除`autodetect`钩子。

生成initramfs时，*mkinitcpio*将显示：

```
-> Early uncompressed CPIO image generation successful
```

您可以使用[lsinitcpio(1)](https://man.archlinux.org/man/lsinitcpio.1)验证initramfs是否包含微码更新文件。例如：

```
# lsinitcpio --early /boot/initramfs-linux.img | grep microcode
```

```
kernel/x86/microcode/
kernel/x86/microcode/AuthenticAMD.bin
```

##### dracut

对于[dracut](../../01_安装与包管理/Initramfs/Dracut.md)，请参见[dracut.conf(5) § DESCRIPTION](https://man.archlinux.org/man/dracut.conf.5#DESCRIPTION)。

#### 微码在单独的initramfs文件中

早期微码更新必须通过在引导加载程序配置文件中添加`/boot/amd-ucode.img`或`/boot/intel-ucode.img`作为**第一个initrd**来启用。这是在正常initrd文件之前。请参阅以下常见引导加载程序的说明。

在以下部分中，将`*cpu_manufacturer*`替换为您的CPU制造商，即`amd`或`intel`。

##### GRUB

*grub-mkconfig*将自动检测微码更新并适当配置[GRUB](../../01_安装与包管理/启动加载程序/GRUB.md)。安装微码包后，通过运行以下命令重新生成GRUB配置以激活加载微码更新：

```
# grub-mkconfig -o /boot/grub/grub.cfg
```

或者，手动管理GRUB配置文件的用户可以添加`/boot/*cpu_manufacturer*-ucode.img`（或`/*cpu_manufacturer*-ucode.img`如果`/boot`是单独的分区）如下：

```
/boot/grub/grub.cfg
```

```
...
echo 'Loading initial ramdisk'
initrd	/boot/cpu_manufacturer-ucode.img /boot/initramfs-linux.img
...
```

为每个菜单项重复此操作。

##### systemd-boot

使用`initrd`选项加载微码，在初始ramdisk之前，如下所示：

```
/boot/loader/entries/entry.conf
```

```
title   Arch Linux
linux   /vmlinuz-linux
initrd  /cpu_manufacturer-ucode.img
initrd  /initramfs-linux.img
...
```

最新的微码`*cpu_manufacturer*-ucode.img`必须在引导时在您的EFI系统分区（ESP）中可用。ESP必须挂载为`/boot`，以便在每次更新[amd-ucode](https://archlinux.org/packages/?name=amd-ucode)包或[intel-ucode](https://archlinux.org/packages/?name=intel-ucode)包时更新微码。否则，请在每次更新微码包时将`/boot/*cpu_manufacturer*-ucode.img`复制到您的ESP。

##### EFISTUB

附加两个`initrd=`选项：

```
initrd=\cpu_manufacturer-ucode.img initrd=\initramfs-linux.img
```

##### rEFInd

> **提示：**以前未指定`initrd`内核参数的用户需要按照[rEFInd#Configuration](../../01_安装与包管理/启动加载程序/REFInd.md#Configuration)中描述的步骤启用传递多个`initrd`参数。

编辑`/boot/refind_linux.conf`中的引导选项，并添加一个`initrd=`选项作为传递的第一个`initrd`参数。使用`initrd=boot\*cpu_manufacturer*-ucode.img`或`initrd=*cpu_manufacturer*-ucode.img`，具体取决于`/boot`中的文件是否在单独分区的根目录中。

微码需要是引导选项列表中声明的第一个initramfs。例如：

```
"Boot using default options"  "root=PARTUUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX rw add_efi_memmap initrd=boot\cpu_manufacturer-ucode.img initrd=boot\initramfs-%v.img"
```

###### 手动引导节

在`*esp*/EFI/refind/refind.conf`中使用[手动节](../../01_安装与包管理/启动加载程序/REFInd.md#For_manual_boot_stanzas)定义内核的用户应添加`initrd=`参数，并在引导分区内指定正确的路径。此参数是选项行的一部分，而不是节的主要部分。例如：

```
options  "root=PARTUUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX rw add_efi_memmap initrd=boot\cpu_manufacturer-ucode.img"
```

##### Syslinux

> **注意：**`*cpu_manufacturer*-ucode.img`和`initramfs-linux.img` initrd文件之间不能有空格。`INITRD`行必须完全如下所示。

多个initrd可以在`/boot/syslinux/syslinux.cfg`中用逗号分隔：

```
LABEL arch
    MENU LABEL Arch Linux
    LINUX ../vmlinuz-linux
    INITRD ../cpu_manufacturer-ucode.img,../initramfs-linux.img
...
```

##### LILO

LILO和可能其他旧的引导加载程序不支持多个initrd镜像。请改用[#Microcode initramfs packed together with the main initramfs in one file](#Microcode_initramfs_packed_together_with_the_main_initramfs_in_one_file)方法。

##### Limine

对于Limine，您只需通过`MODULE_PATH`选项在您的limine.conf文件中添加微码的路径。以下是一个示例：

```
limine.conf
```

```
default_entry: 1
timeout: 3

/Arch
    comment: Arch Linux

    protocol: linux
    kernel_path: boot():/vmlinuz-linux
    kernel_cmdline: root=UUID=c0748521-eca9-4f38-989c-43811b6e39a1 rw loglevel=3
    module_path: boot():/cpu_manufacturer-ucode.img
    module_path: boot():/initramfs-linux.img
```

### 晚期加载

> **警告：**晚期微码加载被认为是危险的，在Linux 5.19及更高版本上使用它将污染内核。

晚期加载微码更新发生在系统启动后。它使用`/usr/lib/firmware/amd-ucode/`和`/usr/lib/firmware/intel-ucode/`中的文件。微码更新文件分别由[amd-ucode](https://archlinux.org/packages/?name=amd-ucode)包和[intel-ucode](https://archlinux.org/packages/?name=intel-ucode)包提供。

晚期加载需要内核构建时启用`CONFIG_MICROCODE_LATE_LOADING=y`，目前Arch [官方支持的内核](../../02_系统管理与内核/内核/内核.md#Officially_supported_kernels)并未启用此选项。

#### 晚期加载微码更新

要手动重新加载微码，例如在更新`/usr/lib/firmware/amd-ucode/`或`/usr/lib/firmware/intel-ucode/`中的微码文件后，运行：

```
# echo 1 > /sys/devices/system/cpu/microcode/reload
```

这允许在不重新启动系统的情况下应用更新的微码更新。

## 验证微码在启动时是否已更新

使用 *journalctl* 检查内核消息，查看微码是否已更新：

```
# journalctl -k --grep='microcode:'
```

每次启动时应该看到类似以下内容，表明微码在早期就已更新：

```
kernel: microcode: Current revision: 0x00000012
kernel: microcode: Updated early from: 0x0000000e
```

特别是对于较新的硬件，可能没有适用于 CPU 的微码更新。

在 AMD 系统上使用延迟加载时，输出将显示重新加载微码前的旧微码版本以及重新加载后的新版本。

## 参见

- [更新微码 – 社区经验](https://flossexperiences.wordpress.com/2013/11/17/updating-microcodes/)
- [关于 Intel 微码更新的笔记 – Ben Hawkes](http://inertiawar.com/microcode/)
- [内核微码加载器 – 内核文档](https://docs.kernel.org/arch/x86/microcode.html)
- [Haswell/Broadwell 中的勘误 – AnandTech](https://www.anandtech.com/show/8376/intel-disables-tsx-instructions-erratum-found-in-haswell-haswelleep-broadwelly)
- [iucode-tool GitLab 项目](https://gitlab.com/iucode-tool/iucode-tool)

## 哪些 CPU 接受微码更新

用户可以查阅 Intel 的仓库或 Gentoo 的 AMD 维基，以查看特定型号是否受支持：

- [Gentoo:AMD microcode#Microcode firmware files](https://wiki.gentoo.org/wiki/AMD_microcode#Microcode_firmware_files)。
- [Intel 的 Github 仓库](https://www.intel.com/content/www/us/en/developer/articles/technical/software-security-guidance/secure-coding/loading-microcode-os.html)。

### 检测可用的微码更新

对于 Intel，可以使用 [iucode_tool(8)](https://man.archlinux.org/man/iucode_tool.8) 来检查 `/usr/lib/firmware/intel-ucode/` 是否包含运行中 CPU 的微码。

1.  安装 [intel-ucode](https://archlinux.org/packages/?name=intel-ucode)包 和 [iucode-tool](https://archlinux.org/packages/?name=iucode-tool)包
2.  加载 `cpuid` 内核模块：

    ```
    # modprobe cpuid
    ```

3.  搜索您的 cpuid：

    ```
    $ iucode_tool -lS /usr/lib/firmware/intel-ucode/
    ```

4.  如果有可用的更新，它应该会显示在“selected microcodes”下面
5.  微码可能已经在您的供应商 BIOS 中，并且不会在 *dmesg* 中显示加载。与当前运行的微码进行比较 `grep microcode /proc/cpuinfo`

对于 AMD，可以手动完成。

1.  找出 CPU 的系列、型号和步进。例如，通过运行以下命令：

    ```
    # journalctl -k --grep='CPU0:'
    ```

    查看输出部分，如 `(family: 0x15, model: 0x10, stepping: 0x1)`。
2.  根据 [amd-ucode README](https://gitlab.com/kernel-firmware/linux-firmware/-/blob/main/amd-ucode/README) 中的列表匹配相应的值。
3.  如果匹配，将[当前运行的微码版本](#Verifying_that_microcode_got_updated_on_boot)与列出的 `Patch` 值进行比较。

> **注意：**您也可以使用 [lscpu(1)](https://man.archlinux.org/man/lscpu.1) 或 `/proc/cpuinfo` 输出来获取系列、型号和步进。但您需要将值转换为十六进制。

## 参见

- [更新微码 – 社区经验](https://flossexperiences.wordpress.com/2013/11/17/updating-microcodes/)
- [关于 Intel 微码更新的笔记 – Ben Hawkes](http://inertiawar.com/microcode/)
- [内核微码加载器 – 内核文档](https://docs.kernel.org/arch/x86/microcode.html)
- [Haswell/Broadwell 中的勘误 – AnandTech](https://www.anandtech.com/show/8376/intel-disables-tsx-instructions-erratum-found-in-haswell-haswelleep-broadwelly)
- [iucode-tool GitLab 项目](https://gitlab.com/iucode-tool/iucode-tool)

## 禁用微码加载器

如果更新的 CPU 微码导致问题，您可能希望暂时禁用微码加载器以允许成功启动并降级软件包。要禁用内核的微码加载器，请指定 `dis_ucode_ldr` [内核参数](../../02_系统管理与内核/内核/内核参数.md)。

## 参见

- [Updating microcodes – Experiences in the community](https://flossexperiences.wordpress.com/2013/11/17/updating-microcodes/)
- [Notes on Intel Microcode updates – Ben Hawkes](http://inertiawar.com/microcode/)
- [Kernel microcode loader – kernel documentation](https://docs.kernel.org/arch/x86/microcode.html)
- [Erratum found in Haswell/Broadwell – AnandTech](https://www.anandtech.com/show/8376/intel-disables-tsx-instructions-erratum-found-in-haswell-haswelleep-broadwelly)
- [iucode-tool GitLab project](https://gitlab.com/iucode-tool/iucode-tool)
- [Intel, AMD, VIA & Freescale CPU Microcode Repositories](https://github.com/platomav/CPUMicrocodes)
- [CPUID dumps](http://users.atw.hu/instlatx64/)

## 8.3　CPU 调频

*难度：高级 ｜ 篇幅：23.8 KB ｜ 所属：CPU*

将要讲到：用户空间工具、调频驱动、调速器、Intel performance and energy bias hint、与 ACPI 事件交互、疑难解答

[CPU 调频](https://docs.kernel.org/admin-guide/pm/cpufreq.html)为操作系统提供了动态调整 CPU 频率的能力，实现节能或提升性能。系统负载、ACPI 事件或者用户空间程序都可以触发 CPU 频率的调整。

Linux 内核通过 *cpufreq* 子系统实现此功能，包含以下两个抽象层：

- [#调速器](#调速器)基于系统需求计算所需的 CPU 频率。
- [#调频驱动](#调频驱动)直接与 CPU 交互，按照调速器的请求调整频率。

系统会自动选择默认的调频驱动程序和调速器，但是为您仍然可以使用[cpupower](#cpupower)、 [acpid](../../02_系统管理与内核/电源管理/Acpid.md)、 [Laptop Mode Tools](../笔记本电脑/Laptop%20Mode%20Tools.md) 或桌面环境提供的 GUI 工具等用户空间应用程序来进行高级配置。

## 用户空间工具

### thermald

[thermald](https://archlinux.org/packages/?name=thermald)包 是一个防止 Intel 平台过热的 [Linux 守护进程](https://01.org/linux-thermal-daemon)。此守护进程会监控平台温度，并通过控制 P-states、T-states 和 Intel power clamp 驱动主动控制温度水平。thermald 也适用于较老的 Intel CPU。如果最新版本的驱动程序不可用，那么守护进程会还原为 x86 MSR (Model Specific Register)，由 Linux“cpufreq 子系统”来控制系统冷却。

默认情况下，它利用 CPU 中的数字温度传感器读取 CPU 温度，在硬件采取激进的降温措施之前将 CPU 的温度控制在允许的范围内。如果 sysfs 中存在表面温度传感器，那么它会尝试将表面温度保持在 45℃ 以下。

在使用 Tiger Lake 架构处理器的笔记本电脑（例如 Dell Latitude 3420）上，此守护程序据说可以[解锁更多性能](https://www.phoronix.com/review/intel-thermald-tgl)。

对应的 systemd 服务是 `thermald.service`，您应该启用并启动此服务。

### i7z

[i7z](https://archlinux.org/packages/?name=i7z)包 是 i7 CPU （也同样适用于 i3、i5、i9 CPU）的报告工具。可以在终端下输入 `i7z` 或者使用图形化工具 `i7z-gui` 来运行该工具。

### turbostat

[turbostat](https://archlinux.org/packages/?name=turbostat)包 可以显示现代 Intel 和 AMD CPU 的频率、功耗、空闲状态和其他统计数据。

### cpupower

[cpupower](https://archlinux.org/packages/?name=cpupower)包 是一组为辅助 CPU 调频而设计的用户空间工具。该软件包并非必须，但强烈建议安装，因为它提供了方便的命令行实用程序，并且内置 [systemd](../../01_安装与包管理/初始化/Systemd.md) 服务，可在启动时更改调频器。

*cpupower* 的配置文件位于 `/etc/default/cpupower`。此配置文件由 `/usr/lib/systemd/scripts/cpupower` 中的 bash 脚本读取，而该脚本由 *systemd* 通过 `cpupower.service` 激活。若要在启动时启用 *cpupower*，请执行：

```
# systemctl enable cpupower.service
```

### cpupower-gui

[cpupower-gui](https://aur.archlinux.org/packages/cpupower-gui/)AUR 是一个图形实用程序，旨在帮助 CPU 调频。该 GUI 基于 [GTK](../../07_开发与工具/控件工具集/GTK.md)，提供与 *cpupower* 相同的选项。*cpupower-gui* 可以更改每个内核的最大 / 最小 CPU 频率和调速器。该应用程序通过 [polkit](../../08_安全与加密/安全/Polkit.md) 获得权限，并允许 `wheel` 用户组中的任何登录用户更改频率和调速器。

### gnome-shell-extension-cpupower

[gnome-shell-extension-cpupower-git](https://aur.archlinux.org/packages/gnome-shell-extension-cpupower-git/)AUR是一个[GNOME](../../05_桌面与图形/GNOME/GNOME.md) shell扩展，可以改变 CPU 的最小/最大的频率，并启用/禁用调频。

### auto-cpufreq

[auto-cpufreq](https://aur.archlinux.org/packages/auto-cpufreq/)AUR 是一个用于Linux的自动CPU速度和功率优化器，它基于对笔记本电脑的电池状态、CPU使用率、CPU温度和系统负载的主动监测。

### power-profiles-daemon

[power-profiles-daemon](https://archlinux.org/packages/?name=power-profiles-daemon)包 中的 *powerprofilesctl* 命令行工具通过 `power-profiles-daemon.service` 处理电源配置文件（如平衡、省电、性能）。GNOME 和 KDE 也提供了切换配置文件的[图形界面](https://gitlab.freedesktop.org/hadess/power-profiles-daemon#how-to-use)；见下文：

- [GNOME#Power modes](../../05_桌面与图形/GNOME/GNOME.md#Power_modes)
- [KDE#Power management](../../05_桌面与图形/KDE/KDE.md#Power_management)

关于使用方法、用例以及与类似项目的比较，请参见该项目的 [README](https://gitlab.freedesktop.org/hadess/power-profiles-daemon#power-profiles-daemon)。

启动/启用 `power-profiles-daemon.service` 。注意，当 *powerprofilesctl* 启动时，它也会尝试启动该服务（见`dbus.service`的[单元状态](../../01_安装与包管理/初始化/Systemd.md#使用单元)）。

> **注意：***power-profiles-daemon* 与其他电源管理服务比如 [TLP](../../02_系统管理与内核/电源管理/TLP.md)， [tuned](https://aur.archlinux.org/packages/tuned/)AUR和 [system76-power](https://aur.archlinux.org/packages/system76-power/)AUR[冲突](https://gitlab.freedesktop.org/hadess/power-profiles-daemon/-/blob/main/data/power-profiles-daemon.service.in#L3)。要在不[卸载](../../01_安装与包管理/包管理器/Pacman.md) *power-profiles-daemon* 的情况下使用上述服务之一（由于它可能存在依赖关系），可以通过 mask 来禁用 `power-profiles-daemon.service`（另见 ，）。

## 调频驱动

调频驱动实现了 CPU 特有的设置调速器指定频率的细节。严格来说，ACPI 标准要求电源性能状态（P-state）从P0开始性能逐渐降低。这种功能在英特尔称为 SpeedStep，在AMD称为 PowerNow!

但在实践中，处理器提供了指定特定频率的方法，而不是局限于调频驱动程序处理的固定 P 状态。

> **注意：**
>
> - 原生 CPU 模块将会自动加载。
> - 对于现代 Intel CPU，将使用 `intel_pstate` 功率驱动程序，而非下列其他驱动程序。此驱动程序的优先级高于其他驱动程序，并编入内核（而非编译为模块）。此驱动程序将自动用于 Sandy Bridge（以及更新的 CPU）。`intel_pstate` 可能会忽略 BIOS P-State 设置，或者通过 `intel_cpufreq` 运行于 "被动模式"。如果使用时遇到问题，可以在内核行加入 `intel_pstate=disable`，这样系统会使用 `acpi_cpufreq` 驱动。
> - 在支持的 CPU 上(Zen 2 及之后型号)，在[内核参数](../../02_系统管理与内核/内核/内核参数.md) 中加入 `amd_pstate=passive` 可以手动启动 `amd_pstate` 。如果内核参数不起作用，可能需要进入主板设置程序中将CPPC和全局C状态控制(global c-state control)打开。主板中的大部分选项默认设置可能是自动（Auto），这往往意味着禁用，必须要手动改成启用（Enabled）才能生效。

*cpupower* 需要相应模块来了解本地 CPU 的限制信息：

| 模块 | 描述 |
| --- | --- |
| intel_pstate | 此驱动程序通过内置调频器，实现面向 Intel Core（SandyBridge 和更新的型号）处理器的调频驱动。 |
| intel_cpufreq | 从内核 5.7 开始，intel_pstate 调频驱动程序为不支持 硬件P 状态管理(HWP) 的 CPU 选择“被动模式”又名 intel_cpufreq，即第 5 代或更早的 Intel Core i处理器。 |
| amd_pstate | 此驱动程序为 AMD Ryzen（某些 Zen 2 和更新版本）处理器实现了带有内部调速器的调频驱动程序。 |
| acpi_cpufreq | 此 CPUFreq 驱动程序可充分利用 ACPI Processor Performance States。此驱动程序也支持 Intel Enhanced SpeedStep（之前由 speedstep-centrino 模块（已废弃）提供支持）。 |
| speedstep_lib | 此 CPUFreq 驱动程序面向支持 Intel SpeedStep 的 CPU（主要包括 Atom 和早于 Pentinum 3 的 CPU）。 |
| powernow_k8 | 面向 K8/K10 Athlon 64/Opteron/Phenom 的 CPUFreq 驱动程序。从 Linux 3.7 开始，对于此系列中的较现代 CPU，将自动使用“acpi_cpufreq”。 |
| pcc_cpufreq | 此驱动程序支持 HP 和 Microsoft 提出的 Processor Clocking Control 接口，在某些 ProLiant 服务器上比较有用。 |
| p4_clockmod | 面向 Intel Pentium 4/Xeon/Celeron 处理器的 CPUFreq 驱动程序，可通过跳频来降低 CPU 温度。（您最好使用 SpeedStep 驱动程序。） |

查看所有可用的模块，运行以下命令：

```
$ ls /usr/lib/modules/$(uname -r)/kernel/drivers/cpufreq/
```

加载合适的模块 (see [Kernel modules](../../02_系统管理与内核/内核/内核模块.md) for details)。一旦合适的 cpufreq 驱动模块被加载成功，就可以通过以下命令查询到 CPU 的信息：

```
$ cpupower frequency-info
```

### 设置最大和最小频率

在罕见的情况下，可能有必要手动设置最大和最小频率。

运行以下命令设置最大时钟频率（*clock_freq* 为时钟频率，单位为：GHz, MHz）：

```
# cpupower frequency-set -u clock_freq
```

运行以下命令设置最小时钟频率：

```
# cpupower frequency-set -d clock_freq
```

运行以下命令设置运行于指定频率：

```
# cpupower frequency-set -f clock_freq
```

> **注意：**
>
> - 仅设置某一核心，添加参数 `-c *core_number*`。
> - The governor，频率的最大值和最小值可以在 `/etc/default/cpupower` 中设置。

或者，也可以手动设置频率：

```
# echo value | tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_max_freq
```

可以在 `/sys/devices/system/cpu/cpu*/cpufreq/scaling_available_frequencies` 或者类似的地方找到取值。 参考 

### 配置超频

在合适的散热条件下，某些处理器支持在短时间内将其频率提高到高于正常最大值的频率。 在 Intel 处理器上，这称为[英特尔睿频加速（英语：Intel Turbo Boost）](https://en.wikipedia.org/wiki/Intel_Turbo_Boost), 在 AMD 处理器上叫做 [Turbo-Core（别名：AMD Core Performance Boost ，缩写CPB）](https://en.wikipedia.org/wiki/AMD_Turbo_Core)。

#### 通过 sysfs 配置 (intel_pstate)

intel_pstate 有一个特定于驱动程序的接口，用于禁止处理器进入 turbo P-States：

```
# echo 1 > /sys/devices/system/cpu/intel_pstate/no_turbo
```

#### 通过 sysfs 配置 (Other scaling drivers)

对于 intel_pstate 以外的缩放驱动程序，如果驱动程序支持超频，系统中应该可以找到 sysfs 属性`/sys/devices/system/cpu/cpufreq/boost`，并可用于禁用/启用超频：

```
# echo 0 > /sys/devices/system/cpu/cpufreq/boost
```

#### 通过 x86_energy_perf_policy 配置

在 Intel 处理器上，[x86_energy_perf_policy](https://archlinux.org/packages/?name=x86_energy_perf_policy)包 也可用于配置 Turbo Boost：

```
# x86_energy_perf_policy --turbo-enable 0
```

## 调速器

调速器（见下表）是预设的 CPU 电源方案，有些是设置为固定频率，有些会根据算法计算出需要的频率。在同一时刻只会有一个会调速器被激活。详见[内核文档](https://docs.kernel.org/admin-guide/pm/cpufreq.html#generic-scaling-governors)。

> **注意：**Each governor is compatible with any scaling driver. However, the `intel_pstate` scaling driver in active mode will bypass the generic CPUFreq governors, providing its own scaling algorithms: `powersave` and `performance`. Although they share names with generic governors, their algorithms work differently: both `intel_pstate` governors provide dynamic scaling similar to the `schedutil` or `ondemand` generic governors. The `performance` algorithm [should give better power saving functionality than the old ondemand governor](https://www.phoronix.com/scan.php?page=news_item&px=MTM3NDQ).

| 调速器 | 描述 |
| --- | --- |
| performance | 运行于最大频率, 数值通过 `/sys/devices/system/cpu/cpu*X*/cpufreq/scaling_max_freq`. |
| powersave | 运行于最小频率，数值值通过 `/sys/devices/system/cpu/cpu*X*/cpufreq/scaling_min_freq` 查看。 |
| userspace | 运行于用户指定的频率，通过 `/sys/devices/system/cpu/cpu*X*/cpufreq/scaling_setspeed` 配置。 |
| ondemand | 按需快速动态调整CPU频率， 一有cpu计算量的任务，就会立即达到最大频率运行，空闲时间增加就降低频率 |
| conservative | 按需快速动态调整CPU频率， 比 ondemand 的调整更保守 |
| schedutil | 基于调度程序调整 CPU 频率 , . |

根据实际硬件，以下的调速器可能被默认启用：

- `powersave` ：Intel 使用 `intel_pstate` 驱动的 CPU(Sandy Bridge 和更新的CPU)。
- `powersave` (for Linux < 5.10) or `schedutil` (since Linux 5.10) for CPUs using the `acpi_cpufreq` driver.

> **警告：**修改默认调速器时，请使用 CPU 监控工具监控温度、电压等指标。

如果需要指定特定的调速器，运行以下命令：

```
# cpupower frequency-set -g governor
```

> **注意：**
>
> - 仅设置某一核心，请在命令的最后跟随以下参数 `-c *core_number*`。
> - 激活某一调速器，需要特定的 [内核模块](../../02_系统管理与内核/内核/内核模块.md) （名为 `cpufreq_*governor*`）正确载入。在 3.4 内核上，这些模块应该已经自动加载。

也可以这样实现：

```
# echo governor | tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

> **提示：**如果需要实时监测 CPU 的频率，运行以下命令：
>
> ```
> $ watch cat /sys/devices/system/cpu/cpu[0-9]*/cpufreq/scaling_cur_freq
> ```

### 调节 ondemand 调速器

详见[内核文档](https://docs.kernel.org/admin-guide/pm/cpufreq.html#ondemand)。

#### 开关阈值

设置到其他值（增加）的步长，执行以下命令：

```
# echo -n percent > /sys/devices/system/cpu/cpufreq/governor/up_threshold
```

设置到其他值（减小）的步长，执行以下命令：

```
# echo -n percent > /sys/devices/system/cpu/cpufreq/governor/down_threshold
```

#### 采样率

采样率决定调速器多久进行一次检查并调整CPU频率。 设置`sampling_down_factor`大于1将通过降低负载评估的消耗，并将CPU保持在最高运行频率而提高性能。`sampling_down_factor` 的可选数值是 1 到 100000。这个可调参数对低CPU频率/负载没有效果。

要获取这个值 (default = 1)，运行：

```
$ cat /sys/devices/system/cpu/cpufreq/ondemand/sampling_down_factor
```

要设置这个值，运行：

```
# echo -n <value> > /sys/devices/system/cpu/cpufreq/ondemand/sampling_down_factor
```

#### 保存设置

要在重启后自动启用设置，通常使用[内核模式选项](../../02_系统管理与内核/内核/内核模块.md)和 [systemd#systemd-tmpfiles - temporary files](../../01_安装与包管理/初始化/Systemd.md#systemd-tmpfiles_-_temporary_files)。

例如要将 up_threshold 设置为 10:

```
/etc/tmpfiles.d/ondemand.conf
```

```
w- /sys/devices/system/cpu/cpufreq/ondemand/up_threshold - - - - 10
```

如果某些特殊情况下会出现时序问题，可以使用 [udev](../硬件检测和故障排除/Udev.md)。创建如下 udev 规则：

```
$ udevadm info -a /sys/devices/cpu
```

```
...
    KERNEL=="cpu"
    SUBSYSTEM=="event_source"
...
```

```
/etc/udev/rules.d/cpu.rules
```

```
KERNEL=="cpu", SUBSYSTEM=="event_source", ACTION=="add", RUN+="/bin/sh -c 'echo performance | tee /sys/devices/system/cpu/cpufreq/policy*/scaling_governor'"
```

```
$ udevadm test /sys/devices/cpu
```

```
...
Reading rules file: /usr/lib/udev/rules.d/99-systemd.rules
Reading rules file: /etc/udev/rules.d/cpu.rules
...
```

要在 *initramfs* 中启用设置，请参考下面例子：[udev#Debug output](../硬件检测和故障排除/Udev.md#Debug_output).

> **提示：**
>
> - Since Linux 5.9, it is possible to set the `cpufreq.default_governor` kernel option.
> - Alternatively, configure the [cpupower](#cpupower) utility and enable its systemd service.

## Intel performance and energy bias hint

The [Intel performance and energy bias hint (EPB)](https://docs.kernel.org/admin-guide/pm/intel_epb.html) is an interface provided by Intel CPUs to allow for user space to specify the desired power-performance tradeoff, on a scale of 0 (highest performance) to 15 (highest energy savings). The EPB register is another layer of performance management functioning independently from frequency scaling. It influences how aggressive P-state and C-state selection will be, and informs internal model-specific decision making that affects energy consumption.

Common values and their aliases, as recognized by sysfs and x86_energy_perf_policy are:

| EPB value | String |
| --- | --- |
| 0 | performance |
| 4 | balance-performance |
| 6 | normal, default |
| 8 | balance-power |
| 15 | power |

### Setting via sysfs

The EPB can be set using a sysfs attribute:

```
# echo epb | tee /sys/devices/system/cpu/cpu*/power/energy_perf_bias
```

### Setting via x86_energy_perf_policy

With [x86_energy_perf_policy](https://archlinux.org/packages/?name=x86_energy_perf_policy)包:

```
# x86_energy_perf_policy epb
```

### Setting via cpupower

With [cpupower](https://archlinux.org/packages/?name=cpupower)包:

```
# cpupower set -b epb_value
```

> **警告：**cpupower does not support the string aliases. If given a string, it will silently set the EPB to 0, corresponding to max performance.

## 与 ACPI 事件交互

用户可以把调速器配置为基于不同的ACPI事件自动切换的形式。例如接入外接电源，或是合上屏幕时。以下是一个简明的例子，但可能有必须通读一遍文章[acpid](../../02_系统管理与内核/电源管理/Acpid.md).

事件是在`/etc/acpi/handler.sh`中定义的。如果[acpid](https://archlinux.org/packages/?name=acpid)包软件包已经安装，这个文件应该已经存在并且设置为可执行。例如，当外接电源拔除时将调速器从`performance`改为`conservative`，而当电源再次接入时将它改回来：

```
/etc/acpi/handler.sh
```

```
[...]

 ac_adapter)
     case "$2" in
         AC*)
             case "$4" in
                 00000000)
                     echo "conservative" >/sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
                     echo -n $minspeed >$setspeed
                     #/etc/laptop-mode/laptop-mode start
                 ;;
                 00000001)
                     echo "performance" >/sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
                     echo -n $maxspeed >$setspeed
                     #/etc/laptop-mode/laptop-mode stop
                 ;;
             esac
         ;;
         *) logger "ACPI action undefined: $2" ;;
     esac
 ;;

[...]
```

## 疑难解答

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** Unverifiable and vague statements, lots of "some"s and "maybe"s. Troubleshooting items need to address concrete problems.（在 [Talk:CPU 调频](https://wiki.archlinuxcn.org/wiki/Talk:CPU_%E8%B0%83%E9%A2%91) 中讨论）

- 一些应用程序，如ntop，对自动频率调整不能很好地响应。在ntop的案例中它可能导致分段错误和大量信息丢失，因为在大量网络数据包突然到达被监控的网络接口时，`on-demand`调速器不能迅速反应，以致当前处理速度满足不了处理这些数据包所需的速度。

- 一些CPU在默认的`on-demand`调速器配置下可能受到比较严重的性能损失（例如flash视频不能平滑地播放，或窗口动画停顿）。为了解决这些问题，完全禁用掉频率调整不如采取更积极的措施——降低每个CPU的*up_threshold* [sysctl](../../02_系统管理与内核/内核/Sysctl.md)变量值。阅读[#调节 ondemand 调速器](#调节_ondemand_调速器)章节以获得更多信息。

- 有时on-demand调速器可能达不到最高频率而只能达到次级频率。这个问题可以通过把max_freq值设置得稍微高于最大频率的方式来解决。例如，如果CPU的频率范围是2.00 GHz到3.00 GHz，把max_freq设置为3.01 GHz就是一个不错的主意。

- [ALSA](../声音/ALSA.md)驱动和有些声音芯片配合工作时，可能导致在调速器改变频率时声音跳跃。改回non-changing调速器应该能够解决这个问题。

### BIOS频率限制

一些CPU/BIOS配置可能导致达不到最高频率或根本无法调高频率。这很可能是因为BIOS告诉操作系统限制频率，结果在`/sys/devices/system/cpu/cpu0/cpufreq/bios_limit`中设置了一个过低的值。

这种情况下需要在BIOS设置中修改指定的配置（频率，发热管理等）。这通常是由于有问题的/过旧的BIOS导致，也可能BIOS有特别的原因要求必须这样。

可能的原因有（假设你的机器是一台笔记本）电池被移除（或快要完全损坏），所以你只能用外接电源。这种情况下如果电源适配器提供的电能太弱，就会满足不了整个系统在峰值所需的电能，而且又没有电池辅助供电，就可能导致数据丢失，数据损坏或最坏的情况下损坏硬件！

不是所有的BIOS都会在这种情况下限制CPU频率，但如IBM/联想 Thinkpad就会。参考thinkwiki以获取更多信息[thinkpad related info on this topic](https://www.thinkwiki.org/wiki/Problem_with_CPU_frequency_scaling).

如果你检查后发现并没有不正确的BIOS设置，而且你也十分清楚自己在做什么以及可能导致的结果，你还可以选择让内核忽略BIOS限制。

> **警告：**请确保你读了并且完全明白上面一节内容。CPU频率限制是你BIOS的一个安全特性，通常情况下你不应该越过它。

一个特殊的参数需要传递给处理器模块。

临时尝试这办法时可以修改`/sys/module/processor/parameters/ignore_ppc`值从`0`到`1`。

要固化这个修改请参考[Kernel module](../../02_系统管理与内核/内核/内核模块.md#Manual_module_handling)或继续阅读本文。 添加`processor.ignore_ppc=1`到内核启动参数或创建

```
/etc/modprobe.d/ignore_ppc.conf
```

```
# 如果你的机器受到错误的BIOS频率限制，这应该会有帮助
options processor ignore_ppc=1
```

## 参阅

- [Linux CPUFreq - kernel documentation](https://docs.kernel.org/cpu-freq/index.html)
- [Reddit post talking about pstate](https://www.reddit.com/r/linux/comments/1hdogn/acpi_cpufreq_or_intel_pstates/)
- [Processor boosting control](https://docs.kernel.org/admin-guide/pm/cpufreq.html#frequency-boost-support)
- [intel_pstate kernel documentation](https://docs.kernel.org/admin-guide/pm/intel_pstate.html)
- [amd-pstate kernel documentation](https://docs.kernel.org/admin-guide/pm/amd-pstate.html)
- [intel_pstate/intel_cpufreq documentation kernel 5.7+](https://linrunner.de/tlp/settings/processor.html)

## 8.4　风扇转速控制

*难度：高级 ｜ 篇幅：32.9 KB ｜ 所属：CPU*

将要讲到：概述、Fancontrol（lm-sensors）、传感器驱动、NBFC（笔记本风扇控制）、Dell 笔记本电脑、联想 ThinkPad 笔记本电脑

CPU 电压下探（英语：[Undervolting CPU](https://wiki.archlinux.org/title/Undervolting_CPU)）-   [CPU 调频](CPU%20调频.md)

> **翻译状态：**
>
> - 本文（或部分内容）译自 [Fan speed control](https://wiki.archlinux.org/title/Fan_speed_control)，最近一次同步于 2024-06-07，若英文版本有所[更改](https://wiki.archlinux.org/title/Fan_speed_control?diff=0&oldid=810101)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/Fan_speed_control_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

风扇控制可以为您的系统带来多种好处，例如在低 CPU 负载时完全停止风扇运行，从而使系统工作更安静并节省电力。

> **警告：**在高系统负载时配置或完全停止风扇可能会导致硬件永久损坏，或者至少导致热限制。

## 概述

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**笔记应该与介绍合并，如果工具有各自部分的话，应该留在相应的部分，其余的应该放在底部的一个“大杂烩”部分。（带适当的标题？）（在[Talk:风扇转速控制](https://wiki.archlinuxcn.org/wiki/Talk:%E9%A3%8E%E6%89%87%E8%BD%AC%E9%80%9F%E6%8E%A7%E5%88%B6)讨论）

> **注意：**笔记本电脑用户应该了解他们的硬件中的冷却系统是如何工作的。一些笔记本电脑有一个风扇同时为 CPU 和 GPU 散热。一些笔记本电脑有 CPU 和 GPU 各一个风扇，但第一个风扇同时为 CPU 和 GPU 散热，而另一个只冷却 CPU。在某些情况下，由于散热架构不兼容（例如，一个风扇同时用于 GPU 和 CPU），您将无法使用 [Fancontrol](#Fancontrol_(lm-sensors)) 脚本。[这里](https://github.com/daringer/asus-fan/issues/47#issue-232063547)有更多关于这个话题的信息。

有多种适用于台式机和笔记本电脑的风扇控制解决方案。根据您的需求：

- [Fancontrol（lm-sensors）](#Fancontrol（lm-sensors）)——一个用 Bash 编写的脚本，用于配置风扇速度。最适合那些通过 [sysfs(5)](https://man.archlinux.org/man/sysfs.5) 提供风扇控制的台式机和笔记本电脑。

- [NBFC（笔记本风扇控制）](#NBFC（笔记本风扇控制）)——一个跨平台的笔记本电脑风扇控制解决方案，用 C# 编写，在 [Mono](../../07_开发与工具/框架/Mono.md) 运行时下工作。最适合最新型号，不受 [Fancontrol](#Fancontrol_(lm-sensors)) 支持的笔记本电脑。

- [NBFC-Linux](https://github.com/nbfc-linux/nbfc-linux)——一个用 C 语言编写的轻量级 NBFC 实现。它不依赖于 Mono 框架，可以作为 [nbfc-linux](https://aur.archlinux.org/packages/nbfc-linux/)AUR 或 [nbfc-linux-git](https://aur.archlinux.org/packages/nbfc-linux-git/)AUR 安装。

- [Dell 笔记本电脑](#Dell_笔记本电脑)——一些 Dell 笔记本电脑的替代风扇控制守护进程。

- [联想 ThinkPad 笔记本电脑](#联想_ThinkPad_笔记本电脑)——一些联想 ThinkPad 笔记本电脑的风扇配置。

- [联想 Legion 笔记本电脑](#联想_Legion_笔记本电脑)——一些联想 Legion 笔记本电脑的风扇速度配置和其他设置。

- [ASUS 笔记本电脑](#ASUS_笔记本电脑)——为一些 ASUS 笔记本电脑配置 [Fancontrol](#Fancontrol_(lm-sensors)) 或手动控制。

- [AMDGPU sysfs 风扇控制](#AMDGPU_sysfs_风扇控制)——控制 [AMDGPU](../../05_桌面与图形/图形/AMDGPU.md) 内核驱动提供的显卡风扇控制。

- [fan2go-git](https://aur.archlinux.org/packages/fan2go-git/)AUR——一个独立于设备路径的 Fancontrol 替代品。

- [MControlCenter](https://github.com/dmitry-s93/MControlCenter)——用于 MSI 笔记本电脑的风扇控制应用程序。

- [fw-ectool-git](https://aur.archlinux.org/packages/fw-ectool-git/)AUR——Framework Laptop（英语：[Framework Laptop](https://wiki.archlinux.org/title/Framework_Laptop)） 的风扇配置。

- [CoolerControl](https://aur.archlinux.org/packages/CoolerControl/)AUR——一个带有 GUI 的风扇控制守护进程，适用于 `sysfs` 和 [liquidctl](https://archlinux.org/packages/?name=liquidctl)包 设备。

## Fancontrol（lm-sensors）

`fancontrol` 是 [lm_sensors](https://archlinux.org/packages/?name=lm_sensors)包 的一部分，可以用来控制 CPU/机箱风扇的速度。

## 传感器驱动

许多较新主板的传感器仍然未受到 Kernel 内建驱动的支持，检查此表 [lm-sensors devices](https://hwmon.wiki.kernel.org/device_support_status) 来确认驱动支持情况。

### lm-sensors

首先需要执行以下命令：

```
# sensors-detect
```

这将检测出所有存在的传感器，并且它们将被用于 `fancontrol`。之后，运行以下命令以检查是否正确检测到了传感器：

```
$ sensors
```

```
coretemp-isa-0000
Adapter: ISA adapter
Core 0:      +29.0°C  (high = +76.0°C, crit = +100.0°C)
...

it8718-isa-0290
Adapter: ISA adapter
Vcc:         +1.14 V  (min =  +0.00 V, max =  +4.08 V)
VTT:         +2.08 V  (min =  +0.00 V, max =  +4.08 V)
+3.3V:       +3.33 V  (min =  +0.00 V, max =  +4.08 V)
NB Vcore:    +0.03 V  (min =  +0.00 V, max =  +4.08 V)
VDRAM:       +2.13 V  (min =  +0.00 V, max =  +4.08 V)
fan1:        690 RPM  (min =   10 RPM)
temp1:       +37.5°C  (low  = +129.5°C, high = +129.5°C)  sensor = thermistor
temp2:       +25.0°C  (low  = +127.0°C, high = +127.0°C)  sensor = thermal diode
```

> **注意：**如果输出没有显示 CPU 风扇的 RPM（每分钟转数）值，可能需要 [#提高传感器的 *fan divisor*](#提高传感器的_''fan_divisor'')。正常情况下，显示风扇转速且高于 0。

### 配置

传感器正确配置后，使用 [pwmconfig(8)](https://man.archlinux.org/man/pwmconfig.8) 来测试和配置风扇速度控制。按照指南操作应该会创建 `/etc/fancontrol`，一个自定义的配置文件。在指南中，如果你直接按回车而不输入任何内容，默认答案会放在括号内。输入 `y` 表示是，`n` 表示否。

```
# pwmconfig
```

> **注意：**一些用户在他们的配置文件中使用 `/sys/class/hwmon/` 路径时可能会遇到问题。`hwmon` 类设备的符号链接指向绝对路径，并且被用来将所有的 `hwmon` 传感器集中到一个目录下，以便于访问。有时，`hwmon` 设备的顺序在重启后会改变，导致 `fancontrol` 停止工作。有关如何解决这个问题的更多信息，请参阅 [#/etc/fancontrol 中设备路径改变](#/etc/fancontrol_中设备路径改变)。

### 提示与技巧

有些用户在以 root 权限运行 `pwmconfig` 后可能想要手动调整配置文件，通常是为了修复某些问题。有关手动调整 `/etc/fancontrol` 配置文件，请参见 [fancontrol(8)](https://man.archlinux.org/man/fancontrol.8) 查看变量定义。

用户可能会像上面在 [#Fancontrol（lm-sensors）](#Fancontrol（lm-sensors）)所提到的那样遇到 *hwmon* 路径问题。有关更多信息，请参见 [#/etc/fancontrol 中设备路径改变](#/etc/fancontrol_中设备路径改变)。

> **提示：**使用 `MAXPWM` 和 `MINPWM` 选项来限制风扇速度范围。详情请参阅 [fancontrol(8)](https://man.archlinux.org/man/fancontrol.8)。

> **注意：**温度和风扇传感器路径也可能改变（通常在内核更新时）（例如 `hwmon0/device/temp1_input` 变为 `hwmon0/temp1_input`）。检查 `fancontrol.service` 的[单元状态](https://wiki.archlinuxcn.org/wzh/Help:%E9%98%85%E8%AF%BB#控制_systemd_单元)以找出哪个路径是问题所在，并相应地更正您的配置文件。

### 运行 Fancontrol

尝试运行 `fancontrol`：

```
 # fancontrol
```

一个正确配置的设置将不会输出错误，并将接管系统风扇的控制。用户在执行此命令后不久应该能听到系统风扇开始运转。也可以通过启动/启用 `fancontrol.service` 来运行 `fancontrol`。

对于非官方的图形用户界面，可以安装 [fancontrol-gui](https://aur.archlinux.org/packages/fancontrol-gui/)AUR 或 [fancontrol-kcm](https://aur.archlinux.org/packages/fancontrol-kcm/)AUR。

#### Fancontrol 在挂起并唤醒后停止工作

不幸的是，`fancontrol` 在挂起后无法正常工作。根据[已报告 bug](https://github.com/lm-sensors/lm-sensors/issues/172)，您将不得不在挂起后重新启动 `fancontrol`。这可以通过 [Systemd 钩子](../../02_系统管理与内核/电源管理/电源管理.md#睡眠钩子)自动实现。

## NBFC（笔记本风扇控制）

> **注意：**NBFC 自 2020 年 3 月 29 日以来就未被维护。新用户配置仍然可以手动创建，然而自那时以来没有添加过预定义配置。存在一些分支添加了新配置，例如 [nbfc-revive](https://github.com/UraniumDonut/nbfc-revive)。

NBFC 是一个跨平台的笔记本电脑风扇控制解决方案。它配备了一个强大的配置系统，可以调整以适应许多不同的笔记本电脑型号，包括一些最新的型号。

### 安装

NBFC 可以通过 [nbfc](https://aur.archlinux.org/packages/nbfc/)AUR 或 [nbfc-git](https://aur.archlinux.org/packages/nbfc-git/)AUR 安装。同时，启动/启用 `nbfc.service`。

### 配置

NBFC 提供了预制的配置文件。您可以在 `/opt/nbfc/Configs/` 目录中找到它们。应用这些配置文件时，请使用没有文件扩展名的确切配置文件名（例如，`some profile.xml` 变为 `"some profile"`）。

检查 NBFC 是否有任何推荐设置：

```
$ nbfc config -r
```

如果您至少有一个型号，请尝试应用此配置文件，并查看风扇速度是如何被控制的。例如：

```
$ nbfc config -a "Asus Zenbook UX430UA"
```

> **注意：**如果您遇到错误 `File Descriptor does not support writing`，请删除 `StagWare.Plugins.ECSysLinux.dll` 并重启`nbfc.service`：
>
> ```
> # mv /opt/nbfc/Plugins/StagWare.Plugins.ECSysLinux.dll /opt/nbfc/Plugins/StagWare.Plugins.ECSysLinux.dll.old
> ```
>
> 如果上述解决方案没有帮助，尝试将 `ec_sys.write_support=1` 添加到[内核参数](../../02_系统管理与内核/内核/内核参数.md)。

如果您没有找到推荐的模型，请前往 [NBFC Git 仓库](https://github.com/hirschmann/nbfc/tree/master/Configs)或在`/opt/nbfc/Configs/`查看是否有来自同一制造商的类似模型可用。例如，在**华硕 Zenbook UX430UQ**上，配置**华硕 Zenbook UX430UA** 运行并不理想（风扇始终完全停止），但**华硕 Zenbook UX410UQ** 运行得非常好。

运行 `nbfc` 以查看所有选项。有关配置的更多信息可在[上游维基](https://github.com/hirschmann/nbfc/wiki/)上找到。

## Dell 笔记本电脑

`i8kutils` 是一个守护进程，用于根据 CPU 温度配置某些戴尔 Inspiron 和 Latitude 笔记本电脑的风扇速度。它使用由 `i8k` 驱动程序提供的 `/proc/i8k` 接口（`dell_smm_hwmon` 的别名）。结果会根据不同的笔记本电脑型号而有所不同。

如果 fancontrol 在您的系统上无法工作，请使用 `ignore_dmi=1` [内核模块参数](../../02_系统管理与内核/内核/内核模块.md#配置模块参数)来加载 `dell_smm_hwmon`。

> **警告：**`i8kutils` BIOS 系统调用在某些系统上会暂时停止内核（在戴尔 9560 上已确认），这可能导致像音频中断这样的副作用，参见 [https://bugzilla.kernel.org/show_bug.cgi?id=201097](https://bugzilla.kernel.org/show_bug.cgi?id=201097)

### 安装

[i8kutils](https://aur.archlinux.org/packages/i8kutils/)AUR 是控制风扇速度的主要包。此外，您可能还想安装以下软件包：

- [acpi](https://archlinux.org/packages/?name=acpi)包——使用 *i8kmon* 必须安装。

- [tcl](https://archlinux.org/packages/?name=tcl)包——必须安装以便将 *i8kmon* 作为后台服务运行（使用 `--daemon` 选项）。

- [tk](https://archlinux.org/packages/?name=tk)包——必须与 [tcl](https://archlinux.org/packages/?name=tcl)包 一起安装，以便作为 X11 桌面小程序运行。

- [dell-bios-fan-control-git](https://aur.archlinux.org/packages/dell-bios-fan-control-git/)AUR——如果您的 BIOS 覆盖了风扇控制，推荐安装。

### 配置

风扇改变转速的温度点可以在配置文件 `/etc/i8kutils/i8kmon.conf` 中进行调整。只支持三种风扇速度（高、低和关闭）。查找类似于以下的部分：

```
set config(0)  {{0 0}  -1  55  -1  55}
set config(1)  {{1 1}  45  75  45  75}
set config(2)  {{2 2}  65 128  65 128}
```

这个示例在 CPU 温度达到 55 摄氏度时以低速启动风扇，在 75 摄氏度时切换到高速。一旦温度下降到 65 摄氏度，风扇将切换回低速，并在 45 摄氏度时完全关闭。

> **提示：**如果在运行带详细输出选项的 `i8kmon` 时注意到状态变化（输出示例：`# (57>=55), state=1, low=45, high=75`）但左右风扇报告状态为 0，可能需要考虑在配置文件中将第一个状态的速度值从默认的 1000 更改为 2000 或更高。

### 作为服务安装

`i8kmon` 可以通过启动/启用 `i8kmon.service` 自动启动。

### BIOS 覆盖风扇控制

一些较新的笔记本电脑拥有 BIOS 风扇控制，这将覆盖操作系统级别的风扇控制。要测试是否是这种情况，请在命令行中以详细模式运行 `i8kmon`，确保 CPU 处于空闲状态，然后观察风扇是否相应地关闭或降低转速。

如果 BIOS 风扇控制确实存在，你可以尝试使用 [dell-bios-fan-control-git](https://aur.archlinux.org/packages/dell-bios-fan-control-git/)AUR：

> **警告：**关闭 BIOS 风扇控制可能会导致硬件损坏。确保你事先正确设置了 `i8kmon`，或者在测试这个程序时让 CPU 保持空闲。

启用 BIOS 风扇控制：

```
# dell-bios-fan-control 1
```

禁用 BIOS 风扇控制：

```
# dell-bios-fan-control 0
```

BIOS 风扇控制可以通过启动/启用 `dell-bios-fan-control.service` 自动禁用。

## 联想 ThinkPad 笔记本电脑

一些风扇控制守护进程包括 [simpfand-git](https://aur.archlinux.org/packages/simpfand-git/)AUR 和 [thinkfan](https://aur.archlinux.org/packages/thinkfan/)AUR（推荐）。

### 安装

安装 [thinkfan](https://aur.archlinux.org/packages/thinkfan/)AUR 或 [thinkfan-git](https://aur.archlinux.org/packages/thinkfan-git/)AUR。可选但推荐安装 [lm_sensors](https://archlinux.org/packages/?name=lm_sensors)包。如果需要，可以使用 [thinkfan-ui](https://aur.archlinux.org/packages/thinkfan-ui/)AUR 提供图形界面。然后查看以下文件：

```
# pacman -Ql thinkfan
```

请注意，[thinkfan](https://aur.archlinux.org/packages/thinkfan/)AUR 包安装了 `/usr/lib/modprobe.d/thinkpad_acpi.conf`，其中包含以下[内核模块参数](../../02_系统管理与内核/内核/内核模块.md#配置模块参数)：

```
options thinkpad_acpi fan_control=1
```

> **注意：**新的 Thinkpad 型号可能需要额外的 `experimental=1` 内核模块参数。因此，检查风扇功能非常重要。

所以风扇控制默认是启用的。

现在，加载模块：

```
# modprobe thinkpad_acpi
# cat /proc/acpi/ibm/fan
```

你应该看到风扇级别默认是 `"auto"`，但你可以通过向同一文件回显一个级别命令来手动控制风扇速度：

```
# echo level 1 > /proc/acpi/ibm/fan
```

<table class="wikitable"><caption>风扇级别</caption><tbody><tr><th>级别</th><th>效果</th></tr><tr><td>0</td><td>关闭</td></tr><tr><td>2</td><td>低速</td></tr><tr><td>4</td><td>中速</td></tr><tr><td>7</td><td>最高速</td></tr><tr><td>auto</td><td>默认 - 自动，风扇转速由 BIOS 控制</td></tr><tr><td>full-speed</td><td>在监控下的最大风扇速度</td></tr><tr><td>disengaged</td><td>甚至更快的速度，控制器不监控风扇速度</td></tr></tbody></table>

`thinkfan` 守护进程将自动执行这些操作。

`7` 并不等同于 `disengaged`。`7` 是最大调节速度（对应 `full-speed`）。而 `disengaged` 是最大非调节速度。详情请见 [ThinkWiki](https://www.thinkwiki.org/wiki/How_to_control_fan_speed#Disengaged_.28full-speed.29_mode)。

最后，启用 `thinkfan.service`。

要配置温度阈值，你需要将示例配置文件（`/usr/share/doc/thinkfan/examples/thinkfan.yaml`）复制到 `/etc/thinkfan.conf`，并根据个人喜好进行修改。此文件指定了要读取哪些传感器，以及使用哪个接口来控制风扇。有些系统有 `/proc/acpi/ibm/fan` 和 `/proc/acpi/ibm/thermal` 可用；在其他系统上，你可能需要指定类似：

```
hwmon: /sys/devices/virtual/thermal/thermal_zone0/temp
```

来使用通用的 `hwmon` 传感器，而不是特定于 thinkpad 的传感器。

配置示例可以在 [Gentoo：风扇转速控制/thinkfan#配置(en)](https://wiki.gentoo.org/wiki/Fan_speed_control/thinkfan#Configuration) 中找到。

### 运行

你可以首先手动以 root 身份运行 thinkfan 来测试你的配置：

```
# thinkfan -n
```

并观察它对其他正在运行的程序的负载水平的反应。

当你正确配置后，启动/启用 `thinkfan.service`。

## 联想 Legion 笔记本电脑

工具 [Lenovo Legion Linux](https://github.com/johnfanv2/LenovoLegionLinux) 允许更改存储在嵌入式控制器中的风扇曲线。它由一个必须编译和加载的内核模块组成。目前没有现成的软件包，而是需要从源代码编译和安装。

然后，可以通过 `hwmon` 接口设置风扇曲线。这可以使用提供的脚本或 Python GUI 来完成。

## ASUS 笔记本电脑

本主题将介绍华硕笔记本电脑上用于 [Fancontrol（lm-sensors）](#Fancontrol（lm-sensors）)的驱动程序配置。

### 内核模块

在配置文件中，我们将使用完整的 `sysfs` 文件路径（例如 `/sys/devices/platform/asus-nb-wmi/hwmon/hwmon[[:print:]]*/pwm1`）。这是因为重启后 `hwmon1` 可能会变成其他任何数字。由于 [Fancontrol (lm-sensors)](#Fancontrol_(lm-sensors)) 是用 [Bash](../../07_开发与工具/命令行解释器/Bash.md) 编写的，所以在配置文件中使用这些路径是完全可以接受的。你可以在 ASUS N550JV#风扇控制（英语：[ASUS N550JV#Fan control](https://wiki.archlinux.org/title/ASUS_N550JV#Fan_control)）找到完整的 `/etc/fancontrol` 配置文件示例。

#### asus-nb-wmi

`asus-nb-wmi` 是一个内核模块，包含在 Linux 内核中，并且在华硕笔记本电脑上会自动加载。它只允许控制单个风扇，如果有第二个风扇，你将无法对其进行控制。请注意，将此模块加入黑名单将阻止键盘背光工作。

以下是控制风扇的命令。检查你是否可以控制你的风扇：

```
# echo 255 > /sys/devices/platform/asus-nb-wmi/hwmon/hwmon[[:print:]]*/pwm1           # 风扇全速 (值: 255)
# echo 0 > /sys/devices/platform/asus-nb-wmi/hwmon/hwmon[[:print:]]*/pwm1             # 风扇停止 (值: 0)
# echo 2 > /sys/devices/platform/asus-nb-wmi/hwmon/hwmon[[:print:]]*/pwm1_enable      # 将风扇模式改为自动
# echo 1 > /sys/devices/platform/asus-nb-wmi/hwmon/hwmon[[:print:]]*/pwm1_enable      # 将风扇模式改为手动
# echo 0 > /sys/devices/platform/asus-asus-nb-wmi/hwmon/hwmon[[:print:]]*/pwm1_enable # 将风扇模式改为全速
```

如果以上命令能够修改风扇速度，那么继续进行 [#使用 pwmconfig 生成配置文件](#使用_pwmconfig_生成配置文件)。

#### asus_fan

`asus_fan` 是一个内核模块，它允许在一些旧的 ASUS 笔记本电脑上同时控制两个风扇。它在最新的型号上不起作用。

安装使用 [DKMS](../../02_系统管理与内核/内核/DKMS.md) 的 [asus-fan-dkms-git](https://aur.archlinux.org/packages/asus-fan-dkms-git/)AUR [内核模块](../../02_系统管理与内核/内核/内核模块.md), 提供 `asus_fan`：

```
# modprobe asus_fan
```

检查你是否能够控制两个风扇：

```
# echo 255 > /sys/devices/platform/asus_fan/hwmon/hwmon[[:print:]]*/pwm1          # CPU 风扇全速 (值: 255)
# echo 0 > /sys/devices/platform/asus_fan/hwmon/hwmon[[:print:]]*/pwm1            # CPU 风扇停止 (值: 0)
# echo 255 > /sys/devices/platform/asus_fan/hwmon/hwmon[[:print:]]*/pwm2          # GPU 风扇全速 (值: 255)
# echo 0 > /sys/devices/platform/asus_fan/hwmon/hwmon[[:print:]]*/pwm2            # GPU 风扇停止 (值: 0)
# echo 2 > /sys/devices/platform/asus_fan/hwmon/hwmon[[:print:]]*/pwm1_enable     # 将 CPU 风扇模式改为自动
# echo 1 > /sys/devices/platform/asus_fan/hwmon/hwmon[[:print:]]*/pwm1_enable     # 将 CPU 风扇模式改为手动
# echo 2 > /sys/devices/platform/asus_fan/hwmon/hwmon[[:print:]]*/pwm2_enable     # 将 GPU 风扇模式改为自动
# echo 1 > /sys/devices/platform/asus_fan/hwmon/hwmon[[:print:]]*/pwm2_enable     # 将 GPU 风扇模式改为手动
# cat /sys/devices/platform/asus_fan/hwmon/hwmon[[:print:]]*/temp1_input          # 显示 GPU 温度 (当 GPU 被禁用/未使用时总是为 0)
```

如果一切正常工作，你可以在启动时[加载内核模块](../../02_系统管理与内核/内核/内核模块.md#自动加载模块)以自动化这一步骤。

### 使用 pwmconfig 生成配置文件

如果在用 `pwmconfig` 生成配置文件时出现错误 `没有工作的风扇传感器，所有读数都是 0`，请首先打开一个控制台并执行：

```
# watch -n 1 "echo 2 > /sys/devices/platform/'kernel_module'/hwmon/hwmon[[:print:]]*/pwm1_enable"
```

如果你使用的是 `asus_fan` 内核模块并且有第二个风扇，在第二个控制台：

```
# watch -n 1 "echo 2 > /sys/devices/platform/'kernel_module'/hwmon/hwmon[[:print:]]*/pwm2_enable"
```

最后，在第三个控制台：

```
# pwmconfig
```

一旦你完成并且配置文件生成后，你应该停止第一个和第二个控制台。继续进行 [#Fancontrol（lm-sensors）](#Fancontrol（lm-sensors）)。生成配置文件后，你可能需要手动将 PWM 值替换为完整的 `sysfs` 路径，因为 `hwmon` 数字值在重启后可能会改变。

## 使用 EC 寄存器的替代方法

如果上述方法对您不起作用，另一种方法是直接向嵌入式控制器（EC）中的特定寄存器写入数据。使用 [EC-Probe 工具](https://github.com/hirschmann/nbfc/wiki/EC-probing-tool)，您可以将风扇模式设置为三种风扇速度模式之一，前提是您的型号在 Windows 中提供了这样的功能。

在 ASUS FX504GD 型号中，将风扇速度设置为三种模式之一使用这些寄存器值：

```
# ec_probe write 0x5e 0x80 # 静音模式
# ec_probe write 0x5e 0x40 # 平衡模式
# ec_probe write 0x5e 0xC0 # 性能模式
```

在这里，我们向负责设置风扇速度模式的寄存器 `0x5e` 写入数据。

如果这些值对您不起作用，请在 Windows 中以监视模式运行 `ec-probe` 工具，并尝试识别在切换风扇速度模式时哪个 EC 寄存器的值发生了变化。

### 设置热限制策略

除了使用 `asus-nb-wmi` 手动控制风扇速度外，还可以设置[热限制策略](https://patchwork.kernel.org/project/platform-driver-x86/patch/20191215142634.13888-1-leonmaxx@gmail.com/)，以实现更激进或更保守的风扇控制策略。可能的数值包括 `0`（默认），`1`（超增压），和 `2`（静音）。

```
# echo 数值 > /sys/devices/platform/asus-nb-wmi/hwmon/hwmon[[:print:]]*/throttle_thermal_policy
```

### 某些 TUF 系列笔记本电脑上的风扇控制模式

在某些华硕 TUF 系列笔记本电脑上，可以使用 `Fn+F5` 更改性能和风扇控制模式。可以通过运行以下命令来查看当前模式：

```
$ cat /sys/devices/platform/asus-nb-wmi/fan_boost_mode
```

当您使用 `Fn+F5` 时，可以观察到值的变化。`0` 表示“普通模式”，`1` 表示“性能模式”，`2` 很可能是“静音模式”。 也可以作为 root 将这些值写入 `fan_boost_mode` 文件，以获得预期效果。

这已在华硕 TUF FX504GE 和 ASUS TUF FX504GD 型号上进行了测试，并发现有效。

您可以使用 [tuf-fan-boost-notification-git](https://aur.archlinux.org/packages/tuf-fan-boost-notification-git/)AUR 在每次风扇速度模式更改时获得通知。

## AMDGPU sysfs 风扇控制

[AMDGPU](../../05_桌面与图形/图形/AMDGPU.md) 内核驱动通过 `sysfs` 中的 `hwmon` 提供显卡风扇控制。

### 手动控制配置

要从自动切换到手动风扇控制，运行以下命令：

```
# echo "1" > /sys/class/drm/card0/device/hwmon/hwmon0/pwm1_enable
```

设置风扇速度，例如50%（100%是255个PWM周期，因此通过将所需风扇速度的百分比乘以2.55来计算）：

```
# echo "128" > /sys/class/drm/card0/device/hwmon/hwmon0/pwm1
```

要重置为自动风扇控制，运行以下命令：

```
# echo "2" > /sys/class/drm/card0/device/hwmon/hwmon0/pwm1_enable
```

> **警告：**由于驱动程序的错误，重置风扇速度到自动可能不起作用，作为解决方法，可能需要重新启动驱动程序。

> **注意：**在 RDNA3 GPU 上，由于固件限制，无法手动调整风扇速度，然而，截至 2023 年 8 月 9 日，正在开发用于调整风扇曲线的接口。

### amdgpu-fan

[amdgpu-fan](https://aur.archlinux.org/packages/amdgpu-fan/)AUR 软件包是一个用 Python 编写的自动化风扇控制器，适用于启用 AMDGPU 的显卡。它使用“速度矩阵”来匹配 GPU 温度与风扇频率，例如：

```
speed_matrix:  # -[温度(*C), 速度(0-100%)]
- [0, 0]
- [40, 30]
- [60, 50]
- [80, 100]
```

通过启动/启用 `amdgpu-fan.service` 来启动风扇控制服务。

### amdfand-bin

[amdfand-bin](https://aur.archlinux.org/packages/amdfand-bin/)AUR 软件包是 [amdgpu-fan](https://aur.archlinux.org/packages/amdgpu-fan/)AUR 的原生替代品。通过启动/启用 `amdfand.service` 来启动风扇控制服务。

对于这个工具，还有图形界面客户端可用：[amdguid-glow-bin](https://aur.archlinux.org/packages/amdguid-glow-bin/)AUR（Xorg）和 [amdguid-wayland-bin](https://aur.archlinux.org/packages/amdguid-wayland-bin/)AUR（Wayland）。在启动客户端之前，您需要启用/启动 `amdgui-helper.service`。

### fancurve 脚本

不仅风扇控制通过 `sysfs` 中的 `hwmon` 提供，GPU 温度读取也同样可用：

```
# cat /sys/class/drm/card0/device/hwmon/hwmon0/temp1_input
```

这将输出 GPU 温度，单位为摄氏度，后跟三个零，例如 `33000` 代表 33°C。

由 grmat 编写的 bash 脚本 [amdgpu-fancontrol](https://github.com/grmat/amdgpu-fancontrol) 提供了一个完全自动的风扇控制，它使用了上述的 `sysfs hwmon` 功能。它还允许用户方便地调整风扇曲线的温度/PWM周期的分配，并通过在脚本顶部提供抽象化的配置字段来设置滞后性。

> **提示：**为了正确工作，脚本至少需要定义三个温度/PWM周期的分配。

出于安全考虑，脚本在关机时会再次将风扇控制设置为自动。这可能会导致风扇加速旋转，可以通过在 `function reset_on_fail` 部分设置 `set_fanmode 1` 来解决这个问题，但这会牺牲一定的安全性。

#### 设置 fancurve 脚本

要启动脚本，建议通过 [systemd](../../01_安装与包管理/初始化/Systemd.md) 初始化系统来进行。这样，脚本的详细输出可以通过 `[journalctl](../../02_系统管理与内核/日志/Systemd_Journal.md)/systemctl status` 来读取。为此，GitHub 仓库中已经包含了一个 `.service` 单元文件。

在休眠后，可能还需要通过 [root-resume.service](../../02_系统管理与内核/电源管理/电源管理_挂起与休眠.md#Sleep_hooks) 重新启动脚本，以使其能够自动正常工作：

```
/etc/systemd/system/root-resume.service
```

```
[Unit]
Description=本地系统恢复动作
After=suspend.target

[Service]
Type=simple
ExecStart=/usr/bin/systemctl restart amdgpu-fancontrol.service

[Install]
WantedBy=suspend.target
```

## 问题解决

### 提高传感器的 *fan divisor*

如果 `sensors` 没有输出 CPU 风扇的 RPM（每分钟转数），可能需要更改 *fan divisor*（风扇除数）。

`sensors` 输出的第一行是主板用于读取温度和电压的芯片组名称。

在 `/etc/sensors.d/` 中创建一个文件：

```
/etc/sensors.d/fan-speed-control.conf
```

```
chip "coretemp-isa-*"
set fanX_div 4
```

将 `*coretemp-isa-*` 替换为芯片组的名称，将 `*X*` 替换为要更改的 CPU 风扇编号。

保存文件，并以 root 用户运行：

```
# sensors -s
```

这将重新加载配置文件。

再次运行 `sensors`，并检查是否有 RPM 读数。如果没有，请将 *fan divisor* 增加到 `8`、`16` 或 `32`。效果可能因人而异。

### /etc/fancontrol 中设备路径改变

位于 `/sys/class/hwmon/` 中的枚举 "hwmon" 符号链接的顺序可能会变化，因为内核模块每次启动时加载的顺序并不一致。这可能会导致 fancontrol 无法正常工作。错误提示是“Configuration appears to be outdated, please run pwmconfig again”。[\[上游 bug\]](https://github.com/lm-sensors/lm-sensors/issues/227)

#### 解决方案

在 `/etc/conf.d/lm_sensors` 中，有两个数组列出了执行 `sensors-detect` 时检测到的所有模块。这些模块将由 fancontrol 加载。如果文件不存在，请以 root 身份运行 `sensors-detect` 并接受默认设置。打开（或创建）`/etc/modules-load.d/modules.conf`。从 `/etc/conf.d/lm_sensors/` 中的两个变量获取所有列出的模块，并将它们放入 `/etc/modules-load.d/modules.conf` 文件中，每行一个模块。这样指定应该可以为模块定义一个加载顺序，这应该可以使 `hwmon` 路径保持在它们的位置，并且在每次启动时不会改变顺序。如果这不起作用，我强烈推荐寻找另一个程序来控制您的风扇。如果您找不到任何程序，那么您可以尝试使用下面的替代解决方案。

#### 替代解决方案：绝对路径

在 `fancontrol` 中使用绝对文件路径默认情况下是不起作用的，因为它的辅助脚本 `pwmconfig` 被编程为只使用 `hwmon` 路径来获取文件。它这样做的方式是检测其配置文件 `/etc/fancontrol` 中提供的 `hwmon` 路径是否发生了变化，并使用变量 `DEVNAME` 和 `DEVPATH` 来确定这种变化。如果您的 `hwmon` 路径不断变化，这将阻止 `fancontrol` 运行，无论您做什么。然而，人们可以绕过这个问题。打开 `/usr/bin/fancontrol`，并注释掉脚本的这部分：

```
if ! ValidateDevices "$DEVPATH" "$DEVNAME"
 then
     echo "Configuration appears to be outdated, please run pwmconfig again" >&2
     exit 1
 fi
```

> **注意：**
>
> - 这样做可能会使 `fancontrol` 写入您在配置文件中给出的文件，无论文件是什么。如果您提供了错误的路径，这可能会损坏文件。确保您使用的是文件的正确路径。
> - 另外要注意的是，在执行此解决方法时，再次使用 `pwmconfig` 创建脚本将覆盖您配置的所有绝对路径。因此，如果需要，最好手动将旧路径更改为新路径，而不是使用 `pwmconfig`。

注释掉这部分应该可以有效地忽略 `hwmon` 验证检查。您也可以在配置文件中忽略变量 `DEVNAME` 和 `DEVPATH`。之后，将其他变量中的所有 `hwmon` 路径替换为其绝对路径。为了更方便，以 root 权限重新运行 `pwmconfig` 以刷新 `hwmon` 设备。配置文件中的 `hwmon` 路径现在应该指向正确的绝对路径。对于每个 `hwmon` 路径，运行以下命令（其中 `*N*` 是 `hwmon` 路径的枚举）：

```
$ readlink -f /sys/class/hwmon/hwmonN/device
```

这将为您提供设备的绝对路径。

例如，`/etc/fancontrol` 文件列出 `FCTEMPS` 如下：

```
FCTEMPS=hwmon2/pwm1=hwmon3/temp1_input
```

执行 `readlink -f /sys/class/hwmon/hwmon3/device` 可以输出例如 `/sys/devices/platform/coretemp.0/`。`cd` 进入此目录。如果您看到一个 `/hwmon/hwmon*N*/` 目录，您必须在您的 `fancontrol` 配置文件中这样做，以替换 `hwmon*N*` 路径。根据前面的例子：

```
# 更改
FCTEMPS=hwmon2/pwm1=hwmon3/temp1_input
# 为
FCTEMPS=hwmon2/pwm1=/sys/devices/platform/coretemp.0/hwmon/[[:print:]]*/temp1_input
```

基本上，您必须将 `hwmon` 路径替换为绝对路径，并与 `/hwmon/[[:print:]]*/` 连接，以便 `bash` 能够捕获随机枚举的 `hwmon` 名称。

如果您没有看到 `/hwmon/hwmon*N*/` 目录，那么您就不必担心这个问题。这意味着温度文件位于设备目录的根目录中。只需将 `hwmon*N*/` 替换为绝对文件路径。例如：

```
# 更改
FCTEMPS=hwmon2/pwm1=hwmon3/temp1_input
# 为
FCTEMPS=hwmon2/pwm1=/sys/devices/platform/coretemp.0/temp1_input
```

在替换了所有路径之后，`fancontrol` 应该可以正常工作。
