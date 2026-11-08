# 第 10 章　FUSE

> 本章处理非原生文件系统的落地：FUSE 是底座，Gitfs 把 Git 仓库挂成目录，NTFS-3G 负责 NTFS 分区的挂载、格式化与容量调整。《FUSE》必读，先弄清卸载与现成的 FUSE 文件系统清单；《NTFS-3G》必读，它覆盖从安装、手动挂载、格式化到调整分区大小的全链路；《Gitfs》篇幅最短，只处理 NTFS 数据时可整节跳过。最容易踩的坑是轻视卸载：它排在清单之前，挂载成功只是开始，残留挂载点才是麻烦；另一个误解是格式化与调整分区大小能顺手完成，动手前先看疑难解答。

<!-- 本章 3 节，共 25 KB -->

## 10.1　Gitfs

*难度：入门 ｜ 篇幅：2.7 KB ｜ 所属：FUSE*

将要讲到：安装、用法、问题解决、参见

摘自 [gitfs](https://www.presslabs.org/gitfs/docs/):

: gitfs 是一个与 git 完全集成的 FUSE 文件系统。你可以在本地挂载远程仓库的分支，随后对文件所做的任何修改都会自动提交到远程仓库。
: 您可以挂载任何版本库，您所做的所有改动都会自动转化为提交。gitfs 还会通过模拟每次提交的快照，显示您当前工作分支的历史。
: gitfs 对于那些需要跟踪所有文件，但又无法将所有文件整理成文件提交的地方非常有用。一个用于 git 仓库的 FUSE 文件系统，带有本地缓存。

## 安装

安装 [gitfs](https://aur.archlinux.org/packages/gitfs/)AUR。

## 用法

例如，通过 gitfs，用户可以将远程 git 仓库挂载为FUSE文件系统：

```
$ gitfs https://example.com/repository.git /mount/directory
```

请参阅 [options](https://www.presslabs.org/gitfs/docs/arguments/) 文档。

## 问题解决

### 对 /var/lib/gitfs 的写访问权限

`/var/lib/gitfs` 需要存在，但不是自动创建。此外，如果想以普通用户身份挂载 gitfs，请确保该用户可写：

```
# mkdir /var/lib/gitfs
# chown username:users /var/lib/gitfs
```

### 对 pygit2 的写访问权限

如果以普通用户身份运行，首次运行时 gitfs 会尝试进行自我检查，但会失败。要解决这个问题，请以根用户身份运行一次。您不需要实际挂载任何东西。以 root 身份显示帮助信息即可：

```
# gitfs -h
```

### 与 ssh 密钥一起使用的选项

Gitfs（以及它所依赖的 pygit2）似乎正在进行大量开发，选项也在不断变化。 虽然[官方文档](https://www.presslabs.org/gitfs/docs/arguments/)说可以使用 `-o key=` 选项来更改密钥，但 AUR 的 0.4.1-1 版本却要求使用 `-o ssh_key=`。 注意，如果密钥受密码保护，gitfs 不会要求输入密码。它只会返回错误信息：

```
_pygit2.GitError: Failed to authenticate SSH session: Callback returned error
```

建议为此创建一个单独的密钥，方法是发出

```
ssh-keygen
/home/user/.ssh/gitfs_rsa
<empty passphrase>
<empty passphrase again>
```

## 参见

- [git worktree](https://git-scm.com/docs/git-worktree)

## 10.2　FUSE

*难度：进阶 ｜ 篇幅：7.5 KB ｜ 所属：FUSE*

将要讲到：卸载、FUSE 文件系统列表、另请参见

[用户空间中的文件系统 (Filesystem in Userspace)](https://en.wikipedia.org/wiki/Filesystem_in_Userspace) (FUSE) 是一种用于类 Unix 操作系统的机制，它使非特权用户可以创建自己的文件系统而无需编辑内核代码。这是通过在用户空间中运行文件系统代码来实现的，而 FUSE 内核模块仅提供了到实际内核接口的“桥梁”。

## 卸载

可以使用 [fuse3](https://archlinux.org/packages/?name=fuse3)包 提供的 [fusermount3(1)](https://man.archlinux.org/man/fusermount3.1) 或 [fuse2](https://archlinux.org/packages/?name=fuse2)包 提供的 [fusermount(1)](https://man.archlinux.org/man/fusermount.1) 卸载 FUSE 文件系统。例如：

```
$ fusermount3 -u mountpoint
```

## FUSE 文件系统列表

- **adbfs-rootless** — 挂载通过 USB 连接的 Android 设备。

: [https://github.com/spion/adbfs-rootless](https://github.com/spion/adbfs-rootless) || [adbfs-rootless-git](https://aur.archlinux.org/packages/adbfs-rootless-git/)AUR

- **apfs-fuse** — 用于 APFS（Apple 文件系统 (Apple File System)）的 FUSE 驱动程序。

: [https://github.com/sgan81/apfs-fuse](https://github.com/sgan81/apfs-fuse) || [apfs-fuse-git](https://aur.archlinux.org/packages/apfs-fuse-git/)AUR

- **astreamfs** — 异步流式 fuse 文件系统。(A(synchronous) Stream(ing) (fuse) F(ile)S(ystem))

: [https://gitlab.com/BylonAkila/astreamfs/tree/master](https://gitlab.com/BylonAkila/astreamfs/tree/master) || [astreamfs-git](https://aur.archlinux.org/packages/astreamfs-git/)AUR

- **AVFS** — 虚拟文件系统，可浏览压缩文件（档案）。

: [https://avf.sourceforge.net](https://avf.sourceforge.net) || [avfs](https://archlinux.org/packages/?name=avfs)包

- **[CurlFtpFS](../../03_网络与服务/FTP/CurlFtpFS.md)** — 基于 FUSE 和 libcurl 的用于访问 FTP 主机的文件系统。

: [https://curlftpfs.sourceforge.net/](https://curlftpfs.sourceforge.net/) || [curlftpfs](https://archlinux.org/packages/?name=curlftpfs)包

- **[davfs2](../../03_网络与服务/WebDAV/Davfs2.md)** — 文件系统驱动程序，可让您挂载 WebDAV 文件夹。

: [https://savannah.nongnu.org/projects/davfs2](https://savannah.nongnu.org/projects/davfs2) || [davfs2](https://aur.archlinux.org/packages/davfs2/)AUR

- **EncFS** — 用户空间可堆叠加密文件系统。

: [https://vgough.github.io/encfs/](https://vgough.github.io/encfs/) || [encfs](https://archlinux.org/packages/?name=encfs)包

- **fuse-archive** — 将存档或压缩文件作为只读 FUSE 文件系统提供服务。

: [https://github.com/google/fuse-archive](https://github.com/google/fuse-archive) || [fuse-archive](https://aur.archlinux.org/packages/fuse-archive/)AUR

- **fuseiso** — 以普通用户身份挂载 ISO。

: [https://sourceforge.net/projects/fuseiso/](https://sourceforge.net/projects/fuseiso/) || [fuseiso](https://archlinux.org/packages/?name=fuseiso)包

- **GDriveFS** — 用于 Google Drive 的创新型 FUSE 包装器。

: [https://github.com/dsoprea/GDriveFS](https://github.com/dsoprea/GDriveFS) || [gdrivefs](https://aur.archlinux.org/packages/gdrivefs/)AUR

- **[gitfs](Gitfs.md)** — gitfs 是与 git 完全集成的 FUSE 文件系统。

: [https://www.presslabs.com/gitfs/](https://www.presslabs.com/gitfs/) || [gitfs](https://aur.archlinux.org/packages/gitfs/)AUR

- **gocryptfs** — gocryptfs 是一个用户空间可堆叠的加密文件系统。

: [https://nuetzlich.net/gocryptfs/](https://nuetzlich.net/gocryptfs/) || [gocryptfs](https://archlinux.org/packages/?name=gocryptfs)包

- **google-drive-ocamlfuse** — 使用 OCaml 编写的 Google Drive 支持的基于 FUSE 的文件系统。

: [https://astrada.github.io/google-drive-ocamlfuse/](https://astrada.github.io/google-drive-ocamlfuse/) || [google-drive-ocamlfuse](https://aur.archlinux.org/packages/google-drive-ocamlfuse/)AUR

- **gphotofs** — 将相机作为文件系统挂载的 FUSE 模块。

: [http://www.gphoto.org/proj/gphotofs/](http://www.gphoto.org/proj/gphotofs/) || [gphotofs](https://aur.archlinux.org/packages/gphotofs/)AUR

- **HubicFuse** — 用于访问 HubiC 云存储的 FUSE 文件系统。

: [https://github.com/TurboGit/hubicfuse](https://github.com/TurboGit/hubicfuse) || [hubicfuse](https://aur.archlinux.org/packages/hubicfuse/)AUR

- **iFuse** — 用于访问 iPhone 或 iPod Touch 内容的 FUSE 文件系统。

: [https://libimobiledevice.org/](https://libimobiledevice.org/) || [ifuse](https://archlinux.org/packages/?name=ifuse)包

- **[NTFS-3G](NTFS-3G.md)** — 具有扩展功能的 NTFS 驱动程序。

: [https://github.com/tuxera/ntfs-3g](https://github.com/tuxera/ntfs-3g) || [ntfs-3g](https://archlinux.org/packages/?name=ntfs-3g)包

- **s3fs** — 由 Amazon S3 支持的基于 FUSE 的文件系统。

: [https://github.com/s3fs-fuse/s3fs-fuse](https://github.com/s3fs-fuse/s3fs-fuse) || [s3fs-fuse](https://archlinux.org/packages/?name=s3fs-fuse)包

- **splitviewfuse** — 将文件拆分/合并为段的目录视图。

: [https://github.com/seiferma/splitviewfuse](https://github.com/seiferma/splitviewfuse) || [splitviewfuse](https://aur.archlinux.org/packages/splitviewfuse/)AUR\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\]

- **[SSHFS](../../03_网络与服务/SSH/SSHFS.md)** — 基于 FUSE 的文件系统客户端，用于通过 SSH 挂载目录。

: [https://github.com/libfuse/sshfs](https://github.com/libfuse/sshfs) || [sshfs](https://archlinux.org/packages/?name=sshfs)包

- **TMSU** — 一种命令行工具，用于标记文件并通过虚拟文件系统访问它们。

: [https://tmsu.org/](https://tmsu.org/) || [tmsu](https://aur.archlinux.org/packages/tmsu/)AUR

- **vdfuse** — 挂载 VirtualBox 磁盘映像 (VDI/VMDK/VHD)。

: [https://github.com/muflone/virtualbox-includes](https://github.com/muflone/virtualbox-includes) || [vdfuse](https://aur.archlinux.org/packages/vdfuse/)AUR

- **vramfs** — 基于 VRAM 的文件系统。

: [https://github.com/Overv/vramfs](https://github.com/Overv/vramfs) || [vramfs-git](https://aur.archlinux.org/packages/vramfs-git/)AUR

- **wimmount** — 挂载 Windows 映像格式 (WIM) 映像。

: [https://wimlib.net/](https://wimlib.net/) || [wimlib](https://archlinux.org/packages/?name=wimlib)包

- **xbfuse** — 挂载 Xbox (360) 的 ISO。

: [https://multimedia.cx/xbfuse/](https://multimedia.cx/xbfuse/) || [xbfuse-git](https://aur.archlinux.org/packages/xbfuse-git/)AUR

- **xmlfs** — 将 XML 文件表示为易于访问的目录结构。

: [https://github.com/halhen/xmlfs](https://github.com/halhen/xmlfs) || [xmlfs](https://aur.archlinux.org/packages/xmlfs/)AUR

- [媒体传输协议#FUSE 文件系统](../../04_硬件与外设/移动设备/媒体传输协议.md#FUSE_文件系统)

## 另请参见

- [Wikipedia:Filesystem in Userspace#Applications](https://en.wikipedia.org/wiki/Filesystem_in_Userspace#Applications)

## 10.3　NTFS-3G

*难度：进阶 ｜ 篇幅：14.7 KB ｜ 所属：FUSE*

将要讲到：安装、手动挂载、格式化、配置、调整NTFS分区大小、疑难解答

[NTFS-3G](https://github.com/tuxera/ntfs-3g) 是[微软 NTFS](https://en.wikipedia.org/wiki/NTFS) 的一个开源实现，同时支持读和写。NTFS-3G 开发者使用 FUSE 文件系统辅助开发并帮助实现可移植性。

## 安装

安装下列的软件包：

- [ntfs-3g](https://archlinux.org/packages/?name=ntfs-3g)包—用于挂载NTFS分区的FUSE驱动。
- [ntfsprogs](https://archlinux.org/packages/?name=ntfsprogs)包\-用户空间工具，用于执行[#格式化](#格式化)等操作。

## 手动挂载

有两种手动挂载 NTFS 分区的方法。传统方法是：

```
# mount /dev/你的_NTFS_分区 /mount/point
```

在 Arch 中不需要显式指定挂载类型 `ntfs-3g`。*mount* 命令默认会使用 `/usr/bin/mount.ntfs`（在安装了 ntfs-3g 软件包之后被符号连接到 `/usr/bin/ntfs-3g`）。

第二种方法是直接调用 `ntfs-3g`：

```
# ntfs-3g /dev/你的_NTFS_分区 /mount/point
```

可用选项参见 [ntfs-3g(8)](https://man.archlinux.org/man/ntfs-3g.8)。

## 格式化

> **警告：**与往常一样，仔细检查设备路径。

```
# mkfs.ntfs -Q -L 卷标 /dev/sdXY
```

> **注意：**`-Q` 通过不向驱动器填充零且不检查坏扇区来加速格式化。

## 配置

你的 NTFS 分区可以被配置成自动挂载，或者预先配置好来安装你想要的方式挂载。配置可以在文件系统配置文件 [fstab](../../01_安装与包管理/启动过程/Fstab.md) 中指定或者使用 udev 规则。

### 默认配置

使用默认配置会在启动时挂载 NTFS 分区。使用这种方法，如果挂载位置的父文件夹有合适的用户或组[权限](../../08_安全与加密/安全/用户和用户组.md)，用户或组就可以读写这个分区。

把以下内容写入`/etc/fstab`:

```
# <file system>  <dir>  <type>  <options>  <dump>  <pass>
/dev/NTFS-part  /mnt/windows  ntfs-3g   defaults		  0       0
```

### Linux权限兼容

Linux系统通常将目录的权限设为755，将文件的权限设为644。如果您经常使用NTFS分区，建议保留这些权限。下面的示例将上述权限分配给普通用户：

```
# 安装具有 linux 兼容权限的内部 Windows 分区，即权限755用于目录（dmask=022）和权限644用于文件（fmask=133）
/dev/NTFS-partition  /mnt/windows  ntfs-3g uid=username,gid=users,dmask=022,fmask=133 0 0
```

或者，如果Windows上的权限对你很重要，你可以使用 [ntfsusermap(8)](https://man.archlinux.org/man/ntfsusermap.8) 命令将 Windows 用户 映射到 Linux 用户上。ntfs-3g将会处理权限的翻译。

> **提示：**你可能希望将多个 NTFS SID 映射到同一个 Linux UID，例如存在 Syncthing 的 `SyncthingServiceAcct` 服务账户时。从 Windows 到 Linux 的映射很简单，但从 Linux 到 Windows 时，你需要确保由 Linux 用户创建的文件显示为属于你的 Windows 用户，而不是任何服务账户。
>
> 首先，你需要知道目标 Windows 用户的 SID。你可以使用 `ntfsusermap` 向导根据文件路径推断 SID，也可以在 Windows 系统上执行 `wmic useraccount get`，获取完整且准确的用户列表。然后，调整 `UserMapping` 文件中的条目顺序，确保 Windows 用户的 SID 位于服务账户的 SID 之前。

### 允许组/用户

在`/etc/fstab`中，您还可以指定其他选项，如允许访问（读取）分区的用户。例如，您允许`users`组中的人员具有访问权限：

```
/dev/NTFS-partition  /mnt/windows  ntfs-3g   gid=users,umask=0022    0       0
```

默认情况下, 上述命令仅为root用户启用写支持。若要为其他用户启用，必须显示指定应授予写入权限的用户。使用`uid`参数加您的用户名以启用用户写支持：

```
/dev/NTFS-partition  /mnt/windows  ntfs-3g   uid=username,gid=groupid,umask=0022    0       0
```

如果您在一个单用户计算机上运行，您可能希望自己拥有该文件系统并授予所有可能的权限：

```
/dev/NTFS-partition  /mnt/windows  ntfs-3g   uid=username,gid=groupid    0       0
```

### 基本的 ntfs-3g 选项

对大多数人来说，上面的设置已经足够了。这是一些其他的对于不同的Linux文件系统的通用选项。完整列表参见[ntfs-3g(8) § OPTIONS](https://man.archlinux.org/man/ntfs-3g.8#OPTIONS)

**[umask](../../08_安全与加密/安全/Umask.md)**
: umask 是一个嵌入的 shell 命令，可以自动设置新创建的文件的权限。对于 Arch Linux，对于 root 和 user 默认的 umask 是 0022。设为 0022 将使新目录有目录权限755，新文件有权限644。你可以在[这里](https://www.cyberciti.biz/tips/understanding-linux-unix-umask-value-usage.html)查看更多关于 umask 权限的信息：。
**fmask and dmask**
: 与 `umask` 类似但是分别定义的是文件和目录的权限。
**noauto**
: 如果设置了 `noauto`，`/etc/fstab` 中的 NTFS 条目不会在启动时自动挂载。
**uid**
: 用户 id 号码。这允许指定用户具有完全的访问权限。你的uid可以用 `id` 命令获得。

下面的选项只对ntfs-3g有用：

**windows_names**
: 阻止创建拥有不被Windows允许的名字的文件、目录以及扩展属性的。

### 允许用户挂载

默认情况下，如果*ntfs-3g*在挂载一个[块设备](../系统管理/设备文件.md)，*ntfs-3g*需要 root 权限运行, 即使在`/etc/fstab`中有`user`选项。有关详细信息，请参阅 [ntfs-3g-faq](https://github.com/tuxera/ntfs-3g/wiki/NTFS-3G-FAQ)。即使在fstab里面有了`user`选项，ntfs-3g仍然需要root权限。

> **注意：**
>
> - [ntfs-3g](https://archlinux.org/packages/?name=ntfs-3g)包包没有内置FUSE支持。使用[ABS](../../00_关于与社区/关于%20Arch/Arch%20构建系统.md)重新构建包以启用FUSE支持。
> - 完整的解释是， "user" and "users" work via a setuid `mount` not dropping its setuid privilege so that the block device can be used without root. However, ntfs-3g has a hard-coded restriction in ntfs-3g that bails on setuid if an external libfuse is used.
> - 并没有好的技术原因 for not allowing setuid for external FUSE besides a mistrust of the library. [This patch](https://github.com/AOSC-Dev/ntfs-3g/commit/c918fb79f9f340bce1a19dacf4b720d19922450d) removes the said restriction.
> - 卸载权限似乎存在问题，因此如果需要卸载文件系统，则仍需要 root 权限。您还可以使用`fusermount -u /mnt/*mountpoint*`来卸载文件系统并避免使用root权限。此外, 如果在`/etc/fstab`中使用`*users*`（复数）而不是`user`选项，您可以使用`mount`和`umount`命令装卸载文件系统。

对于非块设备，比如普通的镜像，命令行下的 *ntfs-3g* 应该可以在普通用户权限下照常使用，因为当无法与内核直接交互时，the underlying FUSE calls 会重定向到 setuid-root *fusermount* 。

## 调整NTFS分区大小

> **注意：**对重要数据请提前做好备份！

大多数已购买的系统已经有[Windows](https://en.wikipedia.org/wiki/Windows)安装在其上，有些人希望在进行 Arch Linux 安装时不要完全擦除它。因此，在某些方面，调整现有 Windows 分区的大小以为 Linux 分区腾出空间是很有用的。这经常通过[Live CD](https://en.wikipedia.org/wiki/Live_CD)或可引导的USB闪存驱动器完成。

对于Live CD，典型的创建过程是下载ISO文件，刻录到CD，然后从它启动。[InfraRecorder](http://infrarecorder.org/)是一个免费（通过GPL3）的Windows上的CD/DVD刻录应用程序，这是很合适的方法。如果您想要使用可引导的USB驱动器，请参阅[USB flash installation media](../../01_安装与包管理/安装过程/U%20盘安装介质.md)中创建可引导的USB驱动器的方法。

有许多可引导的CD/USB映像可用。此列表不是详尽无遗的, 但是是个很好的开始：

- **[GParted](https://en.wikipedia.org/wiki/GParted)** — 为x86计算机设计的小型GNU/Linux发行版。允许你使用最新版GParted应用的所有功能。不包括额外的软件包System Rescue CD，并且磁盘加密可能不受支持。

: [http://gparted.sourceforge.net/](http://gparted.sourceforge.net/) || [gparted](https://archlinux.org/packages/?name=gparted)包

- **[Parted Magic](https://en.wikipedia.org/wiki/Parted_Magic)** — [gparted](https://archlinux.org/packages/?name=gparted)包。使用分区编辑器可以调整大小、复制和移动分区。您可以增长或收缩您的 C: 驱动器，为新操作系统创建空间。尝试从丢失的分区中进行数据抢救。

: [https://partedmagic.com/](https://partedmagic.com/) ||

请注意, 调整 NTFS 分区大小的重要程序包括 ntfs-3g 和类似于 (G)parted 或 fdisk 的实用程序，由[util-linux](https://archlinux.org/packages/?name=util-linux)包包提供。除非您是高级用户，否则最好使用像 GParted 这样的工具来执行任何调整大小操作，以尽量减少由于用户错误而导致数据丢失的可能性。

如果您的系统上已经安装了 Arch Linux，并且只想调整现有 NTFS 分区的大小，则可以使用 parted 和 ntfs-3g 包来完成。或者，在安装 [GParted](../系统恢复/Parted.md) 包后, 可以使用 GParted GUI，其本质是调用 [ntfsresize(8)](https://man.archlinux.org/man/ntfsresize.8) 命令

## 疑难解答

### 不支持的重解析点

若您在挂载一个Windows 10的NTFS文件系统，并且读取其中的文件和文件夹时出现以下情形：

1.  出现链接到“不支持的重解析点”（unsupported reparse point）的损坏的符号链接，或
2.  出现错误信息：`cannot access *some_file*: Input/output error` （此时`Could not load plugin /usr/lib64/ntfs-3g/ntfs-plugin-80000017.so: Success`在[日志](../日志/Systemd_Journal.md)中)

其原因是NTFS-3G默认不支持某些类型的[重解析点](https://en.wikipedia.org/wiki/NTFS_reparse_point)。一些插件可用于部分特性的兼容性，如：

- 系统压缩：也称为“Compact OS”，该功能提供了一种比 NTFS 旧有的 LZ77 压缩更强、针对可执行文件优化的压缩方式。可以使用 [ntfs-3g-system-compression-git](https://aur.archlinux.org/packages/ntfs-3g-system-compression-git/)AUR 插件获得只读支持，或者在 Windows 中运行 `compact.exe /CompactOS:never` 来禁用此功能。
- 重复数据删除文件：这是 Windows Server 2012 引入的一项功能，提供块级离线重复数据删除。目前尚未在 AUR 中打包。
- OneDrive 文件：OneDrive 文件在 Windows 中存储于特殊卷中。[ntfs-3g-onedrive-bin](https://aur.archlinux.org/packages/ntfs-3g-onedrive-bin/)AUR 插件*仅*允许对标记为“在本地可用”的文件进行读写访问。

有关更多详细信息，请参阅[此页面](https://github.com/tuxera/ntfs-3g/wiki/Junctions-Points,-Symbolic-Links-and-Reparse-Points#other-types-of-reparse-points)；有关下载资源，请参阅 [archive.org 存档页面](https://web.archive.org/web/20210709190909/https://jp-andre.pagesperso-orange.fr/advanced-ntfs-3g.html)。

### 损坏的NTFS文件系统

如果NTFS文件系统有错，ntfs-3g会以只读方式挂载它。要修复NTFS系统，启动Windows并使用它的磁盘检查程序，chkdsk。考虑到 ntfsfix 只能修复一些错误，如果失败，chkdsk 可能会成功。

想要修复 NTFS 文件系统，该设备必须已经被卸载。例如，想要修复 `/dev/sda2` 中的 NTFS 文件系统：

```
# umount /dev/sda2
# ntfsfix /dev/sda2
Mounting volume... OK
Processing of $MFT and $MFTMirr completed successfully.
NTFS volume version is 3.1.
NTFS partition /dev/sda2 was processed successfully.
# mount /dev/sda2
```

如果顺利的话，该分区已经可以写入了。

### WIndows分区下的中文文件乱码

见[简体中文本地化#Windows 分区下的中文文件名乱码](../../00_关于与社区/本地化/简体中文本地化.md#Windows_分区下的中文文件名乱码)

### 元数据保存在Windows中，拒绝挂载

当与 Windows 8 或 10双引导时,试图挂载一个可见的Windows可能会出现如下错误:

```
The disk contains an unclean file system (0, 0).
Metadata kept in Windows cache, refused to mount.
Failed to mount '/dev/sdc1': Operation not permitted
The NTFS partition is in an unsafe state. Please resume and shutdown
Windows fully (no hibernation or fast restarting), or mount the volume
read-only with the 'ro' mount option.
```

问题是因为Windows 8中引入"快速启动"特性。启用快速启动后，所有分区的元数据的一部分被还原到它们在以前关闭的状态。因此，在 Linux 上所做的更改可能会丢失。这会发生在任何选择"关闭"或"休眠" NTFS 分区的Windows 8 或 10 下。然而，通过选择"重新启动"关闭 Windows 是安全的。

要启用对其他操作系统的系统分区写入，请确保禁用快速重启。通过以管理员身份执行命令:

```
powercfg /h off
```

你可以在*控制面板 >硬件与声音> 电源选项 > 系统设置 > 当电源键按下时做什么*， 去掉勾选*启用快速启动*

#### 删除Windows休眠元数据

作为一个以上干净关机方法的替代方法，有一个办法可以彻底删除休眠后保存的 NTFS 元数据。这个方法只适用于当你不能或不想启动至 Windows，并希望它完全关闭。这个办法是在使用 [ntfs-3g](https://archlinux.org/packages/?name=ntfs-3g)包 提供的 *ntfsfix*。

```
# ntfsfix /dev/你的NTFS分区
```

> **警告：**这个方法意味着已保存的 Windows 会话将彻底丢失。使用该选项后果自负。

### 挂载失败

如果你按本指南内容操作也无法挂载你的 NTFS 分区，可以尝试一下在 `fstab` 中的所有 ntfs 分区里加上 [UUID](../../04_硬件与外设/硬件检测和故障排除/块设备持久化命名.md)。参见[示例](../../01_安装与包管理/启动过程/Fstab.md#文件系统_UUID).

### Windows上挂载失败

Windows 不会识别一个没有相应的分区类型的NTFS分区。创建一个与 Windows 共用的NTFS分区时的常见陷阱就是忘记设置分区类型为 NTFS。 见 [fdisk](../系统恢复/Fdisk.md) 或者[分区工具](../系统恢复/分区.md#分区工具)。

## 参见

- [ntfs-3g(8)](https://man.archlinux.org/man/ntfs-3g.8)
