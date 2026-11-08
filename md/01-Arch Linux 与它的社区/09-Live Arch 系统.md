# 第 9 章　Live Arch 系统

> 没有网络时怎么装出一套可用的 Arch，怎样把配好的系统压成一张 ISO，是本章要解决的两个问题。《离线安装》必读：从准备本地仓库、挂载与配置到 pacstrap 与软件包的离线安装，是一条不依赖镜像站的完整链路。《Archiso》篇幅更长，初读只需跟住准备自定义配置文件、构建 ISO、使用与 QEMU 测试这条主线，技巧提示可留到改包时再翻。
> 
> 最容易踩的坑在本地仓库：只堆软件包却漏了依赖或仓库元数据，pacstrap 第一步就断。也有人以为有了 archiso 就不必再用 pacstrap，其实两者不在同一层。

<!-- 本章 2 节，共 33 KB -->

## 9.1　离线安装

*难度：进阶 ｜ 篇幅：6.5 KB ｜ 所属：Live Arch 系统*

将要讲到：准备本地仓库、挂载和配置、Pacstrap、软件包的离线安装、另见

此文提供在无互联网连接时安装 Arch Linux 的指南。要达到此目的，需要另一有互联网连接的系统。

首先，按照[安装指南](../../01_安装与包管理/安装过程/安装指南.md)，跳过[安装指南-连接到互联网](../../01_安装与包管理/安装过程/安装指南.md#Connect_to_the_internet)部分，直接跳到[安装指南#安装必需的软件包](../../01_安装与包管理/安装过程/安装指南.md#安装必需的软件包)步骤。

> **警告：**从 ISO 解压根文件系统镜像 (`airootfs.sfs`) 或复制 live 环境根文件系统不是受支持的方法。

## 准备本地仓库

从 [Pacman/Tips and tricks#Installing packages from a CD/DVD or USB stick](../../01_安装与包管理/包管理器/Pacman_提示和技巧.md#Installing_packages_from_a_CD/DVD_or_USB_stick) 获取有关在单独的主机安装上准备具有必要文件的本地仓库的说明。

至少，对于正常运行的系统，建议使用以下软件包：

```
# pacman -Syw --cachedir . --dbpath /tmp/blankdb base base-devel linux linux-firmware mkinitcpio vim
```

创建您的自定义离线仓库

```
# repo-add ./custom.db.tar.gz ./*[^sig]
```

## 挂载和配置


准备好仓库后，将外部媒体连接到新安装，并将其挂载到新创建的根文件系统上：

```
# mount --mkdir /dev/sdxy /mnt/repo
```

编辑您的 archiso `/etc/pacman.conf` 并添加一个新部分：

```
/etc/pacman.conf
```

```
[custom]
SigLevel = Optional
Server = file:///mnt/repo/
```

注释掉 `[core]` 和 `[extra]` 以使 pacman 在默认仓库上不失败。

## Pacstrap

您现在可以继续将本地可用的软件包打包到新安装中：

```
# pacstrap -K /mnt base base-devel linux linux-firmware mkinitcpio vim
```

## 软件包的离线安装

### 从文件安装

当离线安装仅是临时的，但需要在可访问互联网前安装自定义的包，参见 [pacman#其它命令](../../01_安装与包管理/包管理器/Pacman.md#其它命令)以了解安装本地包的方法。

[Shell](../../07_开发与工具/命令行解释器/命令行解释器.md) 的[通配符](https://en.wikipedia.org/wiki/Glob_(programming))可用于立即安装：

```
# pacman -U /package/folder/*.tar.zst
```

### 离线缓存

您可以把指定文件置于 `/var/lib/pacman/sync` 以及 `/var/cache/pacman/pkg` 以便 `pacman` 认为其拥有搜索、更新和安装之一切所需。下文方法基于：。

步骤：

1.  用可访问互联网的电脑（在线电脑）下载最新包的数据库；
2.  传输至离线电脑（目标电脑）；
3.  生成离线电脑所需更新的包列表；
4.  用在线电脑下载包及其签名；
5.  传输至离线电脑的 pacman 缓存；
6.  安装更新。

> **提示：**若您的默认仓库不是 core，extra 或 multilib，您应检查您的 `/etc/pacman.conf` 文件。

下列脚本会下载最新包数据库，若需要，可修改 `MIRROR` 至[镜像状态列表](https://archlinux.org/mirrors/status/)之任一。

```
download_databases.sh
```

```
#!/bin/sh

ARCH="x86_64"
MIRROR="https://mirrors.kernel.org/archlinux/"

wget "${MIRROR}/core/os/${ARCH}/core.db"
wget "${MIRROR}/extra/os/${ARCH}/extra.db"
wget "${MIRROR}/multilib/os/${ARCH}/multilib.db"

# and possibly -uncomment- (if customized in /etc/pacman.conf or pacman.conf.d):

#wget "${MIRROR}/core-testing/os/${ARCH}/core-testing.db"
#wget "${MIRROR}/extra-testing/os/${ARCH}/extra-testing.db"
#wget "${MIRROR}/multilib-testing/os/${ARCH}/multilib-testing.db"

# and -additionaly- debug and staging packages.
```

转化为可执行文件并运行。您将得到多个 *.db* 文件。

下列步骤将传输 *.db* 文件至离线电脑，使您可更新包列表（效果与在线的 `pacman -Sy` 相同），然后生成需更新包的列表。

```
# cp *.db /var/lib/pacman/sync/
# pacman -Sup --noconfirm > pkglist
```

> **注意：**确保您启用了至少一个 `/etc/pacman.d/mirrorlist` 定义的服务器。否则您将得到一个误导性的错误信息：`error: no database for package: package-name`.

您还需下载相应签名，所以请准备下载签名的列表：

```
# sed -e 's/\.zst$/.zst.sig/' ../pkglist > ../siglist
```

将这两个列表传输至在线电脑，并在空目录里下载列出的包：

```
# wget -nv -i ../pkglist
# wget -nv -i ../siglist
```

> **提示：**当使用 [cygwin](https://www.cygwin.com) 或其他 Windows 环境下载包时，文件名可能会损坏，因为 Windows 默认文件名不许有一些字符，如冒号。若要避免损坏（cygwin 环境下，因为其命名限制不同），使用 `wget --restrict-file-names=unix`.

将所有 `*.pkg.tar.zst` 和 `*.pkg.tar.zst.sig` 文件带回离线电脑，置于 `/var/cache/pacman/pkg` 并最后运行：

```
# pacman -Su
```

### 本地仓库

如果预计新系统将保持离线或物理隔离，应配置为仅期望本地仓库。

#### 完善仓库

在 chroot 到您的新安装后，编辑新 `/etc/pacman.conf` 使之与旧者一致 （但无 `/mnt` 前缀）：

```
/etc/pacman.conf
```

```
[custom]
SigLevel = Optional
Server = file:///repo/
```

注释掉其他所有仓库，继续配置新系统。

从此对离线系统的任何更新都可以通过挂载本地仓库的最新副本至 `/repo`，再运行 pacman 命令。

## 另见

- [离线阅读](https://wiki.archlinuxcn.org/wzh/Help:%E6%B5%8F%E8%A7%88#离线浏览)：离线浏览 Arch Wiki.

## 9.2　Archiso

*难度：高级 ｜ 篇幅：26.6 KB ｜ 所属：Live Arch 系统*

将要讲到：安装、准备自定义配置文件、构建 ISO 映像、使用 ISO、在 QEMU 中测试 ISO、技巧提示

> **翻译状态：**
>
> - 本文（或部分内容）译自 [archiso](https://wiki.archlinux.org/title/archiso)，最近一次同步于 2026-06-25，若英文版本有所[更改](https://wiki.archlinux.org/title/archiso?diff=0&oldid=880026)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/archiso_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

[Archiso](https://gitlab.archlinux.org/archlinux/archiso) 是一个高度可定制的工具，用于构建 Arch Linux live CD/USB ISO 映像、netboot 文件、bootstrap tar 包。[官方映像](https://archlinux.org/download/)是用 archiso 构建的，包含[这些软件包](https://gitlab.archlinux.org/archlinux/archiso/-/blob/master/configs/releng/packages.x86_64)。它可以用作救援系统、Linux 安装程序或其他系统的基础。这篇 Wiki 文章介绍了如何安装 archiso，以及如何配置它以控制生成的 ISO 映像的各个方面，例如随附的软件包和文件。 技术需求和构建步骤可以在[官方项目文档](https://gitlab.archlinux.org/archlinux/archiso/tree/master/docs)中找到。 archiso 通过许多 bash 脚本实现。 archiso 的核心组件是 *mkarchiso* 命令。 它的选项记录在 `mkarchiso -h` 中，这里不作介绍。

## 安装

安装 [archiso](https://archlinux.org/packages/?name=archiso)包。

## 准备自定义配置文件

> **注意：**要深入了解配置文件，可参阅 archiso 项目的 [README.profile.rst](https://gitlab.archlinux.org/archlinux/archiso/-/blob/master/docs/README.profile.rst)。

Archiso 带有两个配置文件，**releng** 和 **baseline**。

- **releng** 用于创建正式的每月安装ISO。它可以作为创建自定义ISO映像的起点。

- **baseline** 是一种最低限度的配置，它只包括从介质启动 Live 环境所需的最低限度的软件包。

要构建配置文件的未修改版本，请参阅 [#构建 ISO 映像](#构建_ISO_映像)。否则，如果您希望调整或自定义 archiso 的已发布配置文件之一，请将其从 `/usr/share/archiso/configs/*profile-name*/` 复制到一个可写目录，并使用您的名称选择。例如：

```
$ cp -r /usr/share/archiso/configs/releng/ archlive
```

现在，根据所选的配置文件和您的目标进入不同的章节。

### 配置文件结构

Archiso 配置文件包含定义生成的 ISO 映像的配置。配置文件结构记录在 `/usr/share/doc/archiso/README.profile.rst`。

### 选择包

编辑 `packages.x86_64` 以选择要在 Live 系统映像上安装的软件包，逐行列出软件包。

#### 自定义本地仓库

要添加不在标准 Arch 仓库中的包（例如来自 [AUR](../关于%20Arch/Arch%20用户软件仓库.md) 或采用 [ABS](../关于%20Arch/Arch%20构建系统.md) 定制的包），请建立一个[自建本地仓库](../../01_安装与包管理/包管理器/Pacman_提示和技巧.md)并将您的自定义包添加到其中。然后将您的仓库添加到 `pacman.conf`，如下所示：

```
archlive/pacman.conf
```

```
...
[customrepo]
SigLevel = Optional TrustAll
Server = file:///path/to/customrepo
...
```

> **注意：**
>
> - `pacman.conf` 中的顺序很重要。要为您的自定义存储库提供最高优先级，请将其置于其他存储库条目之上。
> - 这个 `pacman.conf` 仅用于在构建映像，而不会在 live 环境中有效。要在 live 环境里使用，请参考 [#向映像中添加仓库](#向映像中添加仓库)
> - 确保 *mkarchiso* 过程（包括 chroot）可以访问仓库，应将仓库放在合适的目录下（如 `/tmp`），以确保映像构建时正确读取仓库。
> - mkarchiso 会寻找名为 `custom` 的仓库，因此在创建仓库时，应运行：
>
> ```
> $ repo-add /path/to/custom.db.tar.zst /path/to/package-1.0-1-x86_64.pkg.tar.zst
> ```

#### 安装 multilib 中的软件包

要从 [Multilib](../关于%20Arch/官方仓库.md) 资源库中安装软件包，仅需在`pacman.conf`中取消注释如下的相关行以启用 *multilib*：

```
[multilib]
Include = /etc/pacman.d/mirrorlist
```

### 向映像里添加文件

Airootfs 目录用作映像上 Live 系统的[根目录](https://en.wikipedia.org/wiki/Root_directory) (`/`) 的起点。在安装包之前，它的所有内容都将被复制到工作目录中。

将任何自定义文件和/或目录放在 `airootfs/` 下的所需位置。例如，如果您在当前系统上自定义了 nftables 规则，希望在 Live 映像上沿用，请按如下方式复制它们：

```
$ cp -r /etc/nftables.conf archlive/airootfs/etc
```

同样，对于驻留在层次结构中某处的特殊配置文件也需要格外小心。使用 [mkdir(1)](https://man.archlinux.org/man/mkdir.1) 可以轻松地创建目录结构的缺失部分。

> **提示：**要将文件添加到安装用户的主目录，请将其放在 `*archlive*/airootfs/root/` 中。要将文件添加到所有其他用户的主目录，请将其放在 `*archlive*/airootfs/etc/skel/`。

> **注意：**与软件包提供的冲突的自定义文件将被覆盖，除非软件包将它们指定为 [备份文件](../../01_安装与包管理/包管理器/Pacman_pacnew%20与%20pacsave.md#Package_backup_files)。

默认情况下，[权限](../../07_开发与工具/命令行/文件权限与属性.md)将是 `644` （对于文件）和 `755`（对于目录）。所有这些都将归根用户所有。要为特定文件和/或文件夹设置不同的权限或所有权，请使用 `profiledef.sh` 中的 `file_permissions` 关联列表。有关详细信息，请参阅 [README.profile.rst](https://gitlab.archlinux.org/archlinux/archiso/-/blob/master/docs/README.profile.rst)。

### 向映像中添加仓库

要在 live 环境中添加一个可用的仓库，请创建一个[适当修改](../../01_安装与包管理/包管理器/Pacman.md#仓库和镜像)的`pacman.conf`，并将其置于`*archlive*/airootfs/etc/`下。

若仓库也使用密钥，将密钥放置在 `*archlive*/airootfs/usr/share/pacman/keyrings/` 下。密钥文件名须以 `.gpg` 结尾。并且，密钥必须被信任。可以在同目录下创建 GnuPG 导出的信任文件，来实现这一点。文件名须以 `-trusted` 结尾。文件的第一部分是密钥指纹，第二部分是信任信息。您可以参考 `/usr/share/pacman/keyrings/archlinux-trusted` 作为实例。

#### ArchZFS 实例

实例中文件如下：

```
airootfs
├── etc
│   ├── pacman.conf
│   └── pacman.d
│       └── archzfs_mirrorlist
└── usr
    └── share
        └── pacman
            └── keyrings
                ├── archzfs.gpg
                └── archzfs-trusted
```

```
airootfs/etc/pacman.conf
```

```
...
[archzfs]
Include = /etc/pacman.d/archzfs_mirrorlist
...
```

```
airootfs/etc/pacman.d/archzfs_mirrorlist
```

```
Server = https://github.com/archzfs/archzfs/releases/download/experimental
```

```
airootfs/usr/share/pacman/keyrings/archzfs-trusted
```

```
3A9917BF0DED5C13F69AC68FABEC0A1208037BE9:4:
```

导入并信任此密钥（在映像构建期间，或在启动之后的设置脚本中）：

```
$ curl -Lo archzfs.gpg 'https://github.com/archzfs/archzfs-keyring/raw/master/keyring/packager/archzfs/3A9917BF0DED5C13F69AC68FABEC0A1208037BE9/3A9917BF0DED5C13F69AC68FABEC0A1208037BE9.asc'
# pacman-key --add archzfs.gpg
# pacman-key --lsign-key 3A9917BF0DED5C13F69AC68FABEC0A1208037BE9
```

### 内核

尽管两个 Archiso 包含的配置文件都只有[linux](https://archlinux.org/packages/?name=linux)包，但可以使映像包含其他甚至多个[内核](../../02_系统管理与内核/内核/内核.md)。

首先，编辑 `packages.x86_64` 以包含您想要的内核包名称。当 *mkarchiso* 运行时，它将包括所有 `*work_dir*/airootfs/boot/vmlinuz-*` 和 `*work_dir*/boot/initramfs-*.img` ISO 中的文件（以及用于 UEFI 引导的 FAT 映像中）。

[mkinitcpio](../../01_安装与包管理/Initramfs/Mkinitcpio.md) 默认情况下预设将构建后备 initramfs 映像。对于 ISO，主 initramfs 映像通常不包含 `autodetect` 挂钩，因此不需要额外的备用映像。为防止创建后备 initramfs 映像，使其不占用空间或减慢构建过程，请在 `*archlive*/airootfs/etc/mkinitcpio.d/*pkgbase*.preset` 中放置自定义预设。例如，对于 [linux-lts](https://archlinux.org/packages/?name=linux-lts)包：

```
archlive/airootfs/etc/mkinitcpio.d/linux-lts.preset
```

```
PRESETS=('archiso')

ALL_kver='/boot/vmlinuz-linux-lts'
ALL_config='/etc/mkinitcpio.conf'

archiso_image="/boot/initramfs-linux-lts.img"
```

最后，创建[引导加载程序配置](#引导加载程序)文件来启动内核。

### 引导加载程序

Archiso 支持 [syslinux](../../01_安装与包管理/启动加载程序/Syslinux.md) 用于 BIOS 引导和 [systemd-boot](../../01_安装与包管理/启动加载程序/Systemd-boot.md) 或 [GRUB](../../01_安装与包管理/启动加载程序/GRUB.md) 用于 UEFI 引导。有关其配置语法的信息，请参阅[引导加载程序](../../01_安装与包管理/启动过程/Arch%20的启动流程.md)的文章。

> **提示：**
>
> - 当使用 El Torito 刻录到光盘时，或使用 [Isohybrid](https://wiki.syslinux.org/wiki/index.php?title=Isohybrid) 写入硬盘（或 USB 闪存驱动器或类似驱动器）时，*releng'* 配置文件默认构建到支持 BIOS 和 UEFI 启动的 ISO 中。
> - 由于 isolinux 的模块化特性，您可以使用许多插件，因为所有 *.c32* 文件都已复制并可供您使用。看看 [官方 syslinux 站点](https://wiki.syslinux.org/wiki/index.php/SYSLINUX) 和 [configs/releng/syslinux archiso git repo](https://gitlab.archlinux.org/archlinux/archiso/-/tree/master/)。使用所述插件，可以制作具有视觉吸引力的复杂菜单。参见 。

mkarchiso 期望 [GRUB](../../01_安装与包管理/启动加载程序/GRUB.md) 配置位于 `grub` 目录中，[systemd-boot](../../01_安装与包管理/启动加载程序/Systemd-boot.md) 配置位于 `efiboot` 目录中，而 [syslinux](../../01_安装与包管理/启动加载程序/Syslinux.md) 配置位于 `syslinux` 目录中。

#### UEFI 安全启动

如果要使 archiso 在启用 UEFI 安全启动的环境下可启动，则必须使用签名过的[引导加载程序](../../01_安装与包管理/启动过程/Arch%20的启动流程.md)。您可以按照 [安全启动#引导安装介质](../../01_安装与包管理/启动过程/UEFI_安全启动.md#引导安装介质) 中的说明进行操作。

### systemd 单元

要为 Live 环境 启用 systemd 服务/套接字/计时器，您需要像 `systemctl enable` 那样手动创建符号链接。

例如，要启用包含 `WantedBy=multi-user.target` 的 `gpm.service`，请运行：

```
$ mkdir -p archlive/airootfs/etc/systemd/system/multi-user.target.wants
$ ln -s /usr/lib/systemd/system/gpm.service archlive/airootfs/etc/systemd/system/multi-user.target.wants/
```

可以通过读取 systemd 单元找到所需的符号链接，或者如果您安装了服务，则通过 启用 它并观察 systemctl 输出。

#### 登录管理器

通过启用登录管理器的 [systemd](../../01_安装与包管理/初始化/Systemd.md) 服务来在启动时 Start X。如果您不知道要启用哪个 *.service*，您可以轻松找出是否在您构建 ISO 的系统上使用相同的程序。只需使用：

```
$ ls -l /etc/systemd/system/display-manager.service
```

现在在 `*archlive*/airootfs/etc/systemd/system/` 中创建相同的符号链接。对于 LXDM：

```
$ ln -s /usr/lib/systemd/system/lxdm.service archlive/airootfs/etc/systemd/system/display-manager.service
```

这将在您的 Live 系统上的系统启动时启用 LXDM。

### 改变自动登录

Getty 的自动登录配置位于 `airootfs/etc/systemd/system/getty@tty1.service.d/autologin.conf`。

您可以修改这个文件来更改自动登录用户：

```
[Service]
ExecStart=
ExecStart=-/sbin/agetty --noreset --noclear --autologin username - ${TERM}
```

或者干脆删除 `autologin.conf` 来禁用自动登录。

如果使用串行控制台，请创建 `airootfs/etc/systemd/system/serial-getty@ttyS0.service.d/autologin.conf` 并添加以下内容：

```
[Service]
ExecStart=
ExecStart=-/sbin/agetty --noreset --noclear --autologin root --keep-baud 115200,57600,38400,9600 - ${TERM}
```

### 用户和密码

要创建在 Live 环境中可用的 [用户](../../08_安全与加密/安全/用户和用户组.md)，您必须手动编辑 `*archlive*/airootfs/etc/passwd`、`*archlive*/airootfs/etc/shadow`、`*archlive*/airootfs/etc/group` 和 `*archlive*/airootfs/etc/gshadow`。

> **注意：**如果这些文件存在，它们必须包含 root 用户和组。

例如，添加用户 `archie`。按照 [passwd(5)](https://man.archlinux.org/man/passwd.5) 语法将它们添加到 `*archlive*/airootfs/etc/passwd` ：

```
archlive/airootfs/etc/passwd
```

```
root:x:0:0:root:/root:/usr/bin/zsh
archie:x:1000:1000::/home/archie:/usr/bin/zsh
```

> **注意：**`passwd` 文件须以换行符结尾。

按 [shadow(5)](https://man.archlinux.org/man/shadow.5) 的语法将用户添加到 `*archlive*/airootfs/etc/shadow`。若要为用户设置密码，请使用 `openssl passwd -6` 生成密码哈希值，并将其添加到文件中。例如：

```
archlive/airootfs/etc/shadow
```

```
root::14871::::::
archie:$6$randomsalt$cij4/pJREFQV/NgAgh9YyBIoCRRNq2jp5l8lbnE5aLggJnzIRmNVlogAg8N6hEEecLwXHtMQIl2NX2HlDqhCU1:14871::::::
```

您也可以将密码字段留空。这意味着，该用户无需密码即可登录。

根据 [group(5)](https://man.archlinux.org/man/group.5) 将用户的组和他们将加入的组添加到 `*archlive*/airootfs/etc/group`。例如：

```
archlive/airootfs/etc/group
```

```
root:x:0:root
adm:x:4:archie
wheel:x:10:archie
uucp:x:14:archie
archie:x:1000:
```

根据 [gshadow(5)](https://man.archlinux.org/man/gshadow.5) 创建相应的 `*archlive*/airootfs/etc/gshadow`：

```
archlive/airootfs/etc/gshadow
```

```
root:!*::root
archie:!*::
```

确保 `/etc/shadow` 和 `/etc/gshadow` 具有正确的权限：

```
archlive/profiledef.sh
```

```
...
file_permissions=(
  ...
  ["/etc/shadow"]="0:0:0400"
  ["/etc/gshadow"]="0:0:0400"
)
```

软件包安装后，*mkarchiso* 将为 `*archlive*/airootfs/etc/passwd` 中列出的用户创建所有指定的主目录并复制 `*work_directory*/x86_64/ airootfs/etc/skel/*` 给他们。复制的文件将具有适当的用户和组归属。

### 键映射

要修改 Live 系统控制台上的默认键映射，可添加含有所需键映射的 `vconsole.conf`。细节以及更多选项，参见 [Linux_控制台/键盘配置](../../07_开发与工具/Linux%20控制台/Linux%20控制台_键盘配置.md) 和 [vconsole.conf(5)](https://man.archlinux.org/man/vconsole.conf.5)。

例如，要将控制台和 X11 的默认键映射修改为德语（`de-latin1`），创建 `*archlive*/airootfs/etc/vconsole.conf`，使其带有下列内容：

```
archlive/airootfs/etc/vconsole.conf
```

```
KEYMAP=de-latin1
XKBLAYOUT=de
```

### 区域设置

要修改 Live 系统的 [Locale](../本地化/Locale.md)，您须先启用并生成区域化文件。为此，须添加含有所需区域设置的自定义 `locale.gen`，并在 Live 系统中运行 `locale-gen`。由此，也可同理修改系统默认区域设置。

例如，要启用 `de_DE.UTF-8 UTF-8` 和 `en_US.UTF-8 UTF-8`，可创建 `*archlive*/airootfs/etc/locale.gen`，使其带有下列内容（细节参阅 [Locale#生成区域设置](../本地化/Locale.md#生成区域设置)）：

```
archlive/airootfs/etc/locale.gen
```

```
de_DE.UTF-8 UTF-8
en_US.UTF-8 UTF-8
```

随后，您需要在 Live 系统中运行 `locale-gen`。要达成这点，可使用 [Pacman 挂钩](../../01_安装与包管理/包管理器/Pacman.md)，并随后借另一挂钩将其自动移除。创建 `*archlive*/airootfs/etc/pacman.d/hooks/locale-gen.hook`，使其带有下列内容（细节参见 [Pacman#Hooks](../../01_安装与包管理/包管理器/Pacman.md#Hooks) and [alpm-hooks(5)](https://man.archlinux.org/man/alpm-hooks.5)）：

```
archlive/airootfs/etc/pacman.d/hooks/locale-gen.hook
```

```
# remove from airootfs!
[Trigger]
Operation = Install
Operation = Upgrade
Type = Package
Target = glibc

[Action]
Description = Generating localisation files...
When = PostTransaction
Depends = glibc
Exec = /usr/bin/locale-gen
```

之后是可选操作：要修改 Live 系统的默认区域设置，可添加自定义的 `locale.conf`。例如，要将默认区域设置改为 `de_DE.UTF-8`，创建 `*archlive*/airootfs/etc/locale.conf`，使其带有下列内容（细节参见 [Locale#变量](../本地化/Locale.md#变量) 和 [locale.conf(5)](https://man.archlinux.org/man/locale.conf.5)）：

### 更改 ISO 中使用的分发名称

首先将文件 `/etc/os-release` 复制到 rootfs 的 `etc/` 文件夹中。然后，相应地编辑文件。您还可以更改 GRUB 和 syslinux 内部的名称。

## 构建 ISO 映像

通过运行以下命令构建一个 ISO 映像，然后您可以将其刻录到 CD 或 USB：

```
# mkarchiso -v -r -w /tmp/archiso-tmp -o /path/to/out_dir /path/to/profile/
```

- `-w` 指定工作目录。如果未指定该选项，则默认为当前目录下的 `work`。若内存允许，更好的选择是将工作目录安置在 [tmpfs](../../02_系统管理与内核/文件系统/Tmpfs.md) 下（如上所示）以加速此过程。
- `-r` 在 ISO 构建成功之后删除工作目录（若工作目录由 *mkarchiso* 创建）。
- `-o` 指定将放置构建的 ISO 映像的目录。如果未指定该选项，则默认为当前目录中的 `out`。
- 需要注意的是配置文件 `profiledef.sh` 在运行 mkarchiso 时不能指定，只能指定文件的路径。

将 `*/path/to/profile/*` 替换为您的自定义配置文件的路径，或者如果您正在构建一个 `/usr/share/archiso/configs/releng/`未修改的配置文件。

运行时，脚本将下载并安装您指定到 `*work_directory*/x86_64/airootfs` 的包，创建内核和初始化映像，应用您的自定义选项，最后将 ISO 构建到输出目录中。

> **提示：**若遇到“无法下载\*.db.sig文件”的错误，尝试添加‘’--skip-signature‘’参数。

### 删除工作目录

> **警告：**如果 *mkarchiso* 被中断，运行 [findmnt(8)](https://man.archlinux.org/man/findmnt.8) 以确保在删除之前没有挂载绑定 - 否则，**你可能会丢失数据**（例如安装在 `/run/media/*user*/*label*` 的外部设备绑定在 `work/x86_64/airootfs/run/media/*user*/*label*` 在构建过程中）。

临时文件被复制到工作目录中。如果 *mkarchiso* 运行带有 `-r` 选项，成功构建 ISO 后，它会删除工作目录（先前未存在的话）。或者，也可手动删除：

```
# rm -rf /path/to/work_dir
```

## 使用 ISO

有关各种选项，请参见 [Installation guide#准备安装介质](../../01_安装与包管理/安装过程/安装指南.md#准备安装介质)。

## 在 QEMU 中测试 ISO

安装 可选依赖项 [qemu-desktop](https://archlinux.org/packages/?name=qemu-desktop)包 和 [edk2-ovmf](https://archlinux.org/packages/?name=edk2-ovmf)包。

使用便捷脚本 `run_archiso` 使用 [QEMU](../../02_系统管理与内核/虚拟机管理/QEMU.md) 运行构建的映像。

```
$ run_archiso -i /path/to/archlinux-yyyy.mm.dd-x86_64.iso
```

虚拟机也可以使用 UEFI 仿真运行：

```
$ run_archiso -u -i /path/to/archlinux-yyyy.mm.dd-x86_64.iso
```

> **提示：**除了 VNC/SDL 屏幕之外，脚本还会将来自主机端口 60022 的 TCP 连接传达给虚拟机端口 22，即虚拟机 SSH 守护进程所监听的端口。可与[#准备 ISO 以通过 SSH 进行安装](#准备_ISO_以通过_SSH_进行安装)搭配使用。

## 技巧提示

### ISO 构建过程中运行设置命令

创建自定义配置文件时，对于部分改动，你需要在创建 ISO 之前，在 Live 系统环境中运行设置命令。例如，如果你要启用额外的区域设置，则须在 Live 系统中运行 `locale-gen`，以生成必要的区域化文件。

要在创建 ISO 之前运行设置命令，可在 `*archlive*/airootfs/etc/pacman.d/hooks/` 下，添加一个 [Pacman 挂钩](../../01_安装与包管理/包管理器/Pacman.md) 。如何创建自定义 Pacman 挂钩的细节，参见 [Pacman#钩子](../../01_安装与包管理/包管理器/Pacman.md#钩子) 与 [alpm-hooks(5)](https://man.archlinux.org/man/alpm-hooks.5)。示例又见 [#区域设置](#区域设置)。

设置挂钩往往只需要在 ISO 构建过程中运行一次，故而在 **releng** 配置文件中包含一个 Pacman 挂钩，用于移除带有 `"remove from airootfs"` 标记的所有 Pacman 挂钩。因此，如果你不想让你的 Pacman 挂钩同样作用于正在运行的 Live 系统，可在挂钩后追加：

```
# remove from airootfs!
```

### 准备 ISO 以通过 SSH 进行安装

> **注意：**自从 `archlinux-2021.02.01-x86_64.iso`，提供[cloud-init 支持](https://gitlab.archlinux.org/archlinux/archiso/-/tree/bd2b861aa39167e4fc658a354071b95fbd050c0f/configs/releng/airootfs/etc/systemd/system/cloud-init.target.wants)，`sshd.service`是[默认启用的](https://gitlab.archlinux.org/archlinux/archiso/-/blob/bd2b861aa39167e4fc658a354071b95fbd050c0f/configs/releng/airootfs/etc/systemd/system/multi-user.target.wants/sshd.service)。

要 [通过 SSH 安装 Arch Linux](../../03_网络与服务/SSH/通过%20SSH%20安装%20Arch%20Linux.md) 而不与系统进行任何交互，必须将 SSH 公钥放在 `authorized_keys` 中。

添加 SSH 密钥可以手动完成（在此处解释），也可以通过 [cloud-init](../../03_网络与服务/SSH/通过%20SSH%20安装%20Arch%20Linux.md#Installation_on_a_headless_server)。

要手动添加密钥，首先 [复制 archiso 的 releng 配置文件](#准备自定义配置文件) 到可写目录。以下示例使用 `archlive`。

```
$ cp -r /usr/share/archiso/configs/profile/ archlive
```

在将用于登录的用户的主目录中创建一个 `.ssh` 目录。以下示例将使用 root 用户。

```
$ mkdir archlive/airootfs/root/.ssh
```

将用于登录的 SSH 公钥添加到 `authorized_keys`：

```
$ cat ~/.ssh/key1.pub >> archlive/airootfs/root/.ssh/authorized_keys
$ cat ~/.ssh/key2.pub >> archlive/airootfs/root/.ssh/authorized_keys
```

为 `.ssh` 目录和 `authorized_keys` 文件设置正确的 [权限](../../07_开发与工具/命令行/文件权限与属性.md) 和所有权：

```
archlive/profiledef.sh
```

```
...
file_permissions=(
  ...
  ["/root"]="0:0:0750"
  ["/root/.ssh"]="0:0:0700"
  ["/root/.ssh/authorized_keys"]="0:0:0600"
)
```

最终 [构建 ISO 映像](#构建_ISO_映像)。启动 ISO 后，[OpenSSH](../../03_网络与服务/SSH/OpenSSH.md) 将启动，并且可以使用相应的 SSH 私钥登录。

### 使用 iwd 自动连接到 Wi-Fi 网络

在配置文件的 `airootfs` 目录中创建 `/var/lib/iwd/` 并设置正确的权限：

```
$ mkdir -p archlive/airootfs/var/lib/iwd
```

```
archlive/profiledef.sh
```

```
...
file_permissions=(
  ...
  ["/var/lib/iwd"]="0:0:0700"
)
```

按照 [iwd#Network configuration](../../03_网络与服务/网络配置/Iwd.md#Network_configuration) 和 [iwd.network(5)](https://man.archlinux.org/man/iwd.network.5) 中的说明为您的 Wi-Fi 网络创建网络配置文件。

将配置文件保存在 `*archlive*/airootfs/var/lib/iwd/` 中。

### 调整根文件系统的大小

在 live 环境中，例如在需要使用 [DKMS](../../02_系统管理与内核/内核/DKMS.md) 模块的硬件上安装软件包时，根文件系统的默认大小可能不允许包的下载和安装。

> **提示：**选中大小的理由请参见 [BBS#210389](https://bbs.archlinux.org/viewtopic.php?pid=1628972#p1628972)，历史细节请参见 [FS#45618](https://bugs.archlinux.org/task/45618)。

当在 live 环境中下载文件或安装软件包时，它将表现为以下错误信息：

```
error: partition / too full: 63256 blocks needed, 61450 blocks free
error: not enough free disk space
error: failed to commit transaction (not enough free disk space)
Errors occurred: no packages were upgraded.
```

您亦可通过运行以下命令以动态调整根分区大小：

```
# mount -o remount,size=SIZE /run/archiso/cowspace
```

关于 `*SIZE*` 可能的参数，请参见 [tmpfs(5) § size](https://man.archlinux.org/man/tmpfs.5#size)。

要在[引导加载程序](../../01_安装与包管理/启动过程/Arch%20的启动流程.md)阶段调整大小（按 `e` 或 `Tab`），请使用以下引导选项：

```
cow_spacesize=SIZE
```

要在{ic|grub/\*.cfg}}

- `syslinux/*.cfg`

您可以通过运行以下命令检查文件系统的大小：

```
$ df -h
```

查看 mkinitcpio-archiso 启动参数于[此](https://gitlab.archlinux.org/mkinitcpio/mkinitcpio-archiso/blob/master/docs/README.bootparams)

### Google Compute Engine 映像

Google Compute Engine 兼容的 `releng` 压缩映像以 [archlinux-gce](https://aur.archlinux.org/packages/archlinux-gce/)AUR 的形式提供。

### Libvirt 虚拟机配置

运行 `releng` 映像的 [libvirt](../../02_系统管理与内核/虚拟化/Libvirt.md) 配置可用作 [archlinux-libvirt](https://aur.archlinux.org/packages/archlinux-libvirt/)AUR。

## 疑难解答

### 窗口管理器冻结

如果您想在 Live CD 中使用[窗口管理器](../../05_桌面与图形/窗口管理器/窗口管理器.md)，则必须添加必要且正确的[显卡驱动](../../05_桌面与图形/图形/图形处理器.md)，否则 WM 可能会在加载时冻结。

## 参见

- [Archiso 项目主页](https://gitlab.archlinux.org/archlinux/archiso)
- [官方档案](https://gitlab.archlinux.org/archlinux/archiso/-/tree/master/docs)
- [Arch Linux 发布工程邮件列表](https://lists.archlinux.org/mailman3/lists/arch-releng.lists.archlinux.org/)
- #archlinux-releng——Arch Linux 发布工程 IRC 频道
- [archiso-manager——用于构建官方每月 ISO 的工具](https://github.com/pierres/archiso-manager)

构建映像阶段调整大小，请将引导选项添加至：

- `efiboot/loader/entries/*.cfg`
- `grub/*.cfg`
- `syslinux/*.cfg`

您可以通过运行以下命令检查文件系统的大小：

```
$ df -h
```

查看 mkinitcpio-archiso 启动参数于[此](https://gitlab.archlinux.org/mkinitcpio/mkinitcpio-archiso/blob/master/docs/README.bootparams)
