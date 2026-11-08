# 第 8 章　SSH

> 本章把登录、传输与挂载做成一条不必反复输入口令的通道：OpenSSH 讲客户端、服务端用法和排障，SSH 密钥讲密钥对、公钥分发与 SSH 代理，SSHFS 挂载远端目录，Mosh 管弱网漫游；另有协议背景，以及通过 SSH 远程安装 Arch Linux 的做法。
> 
> 先读 OpenSSH 与 SSH 密钥，这是其余各节的前提；SSHFS、Mosh 与远程装机按需查阅，协议背景可略过。最容易踩的坑是把密钥当口令：生成后忘了把公钥送到远端，或私钥权限、代理没配好，便以为密钥失效；SSHFS 也常只试手动挂载、不试自动挂载与断线重连，重启后才发现目录空了。

<!-- 本章 6 节，共 138 KB -->

## 8.1　Mosh

*难度：入门 ｜ 篇幅：1.8 KB ｜ 所属：SSH*

将要讲到：安装、用法

[Mosh](https://mosh.org/) 是另一种交互式 [SSH](安全外壳协议.md) 终端。它支持漫游和本地回显。它还旨在提高对间歇性和高延迟连接的响应能力。请注意，真彩色支持仅在后者中提供。

## 安装

> **注意：**Mosh 必须同时安装在服务器和客户端上。

安装 [mosh](https://archlinux.org/packages/?name=mosh)包 包，或安装 [mosh-git](https://aur.archlinux.org/packages/mosh-git/)AUR 以获得最新版本。

## 用法

> **注意：**Mosh 设计不允许您访问会话历史记录，请考虑安装终端多路复用器，例如 [tmux](../../07_开发与工具/终端多路复用器/Tmux.md) 或 [GNU Screen](../../06_软件与应用/GNU/GNU%20Screen.md)。

要进行连接，请运行：

```
$ mosh user@server-address
```

发送 ssh 连接选项：

```
$ mosh --ssh="ssh -p 2222" user@server-address
```

您可以使用通常的 [OpenSSH Client Configuration](OpenSSH.md#Configuration) 来使选项永久化。

> **注意：**Mosh 有一个未公开的命令行选项 `--predict=experimental`，它可以更积极地响应本地击键。 对键盘输入的低延迟视觉确认感兴趣的用户可能更喜欢此预测模式。

## 8.2　安全外壳协议

*难度：入门 ｜ 篇幅：3.0 KB ｜ 所属：SSH*

将要讲到：软件、保护、另请参见

根据[维基百科](https://zh.wikipedia.org/wiki/Secure_Shell)：

: Secure Shell（安全外壳，简称 SSH）是一种加密的网络传输协议，可在不安全的网络中为网络服务提供安全的传输环境。SSH 最常见的用途是远程登录系统命令行界面和远程执行命令。

可以使用 SSH 的服务示例包括 [Git](../../07_开发与工具/版本控制系统/Git.md)，[rsync](../../02_系统管理与内核/同步/Rsync.md) 和 X11 转发。始终使用 SSH 的服务是 [SCP 和 SFTP](../FTP/SCP%20与%20SFTP.md)。

默认情况下，SSH 服务器侦听标准 TCP 端口 22。SSH 客户端程序通常用于建立与接受远程连接的 *sshd* 守护程序的连接。 两者都普遍存在于大多数现代操作系统中，包括 macOS，GNU/Linux，Solaris 和 OpenVMS。存在各种复杂程度和完整性的专有，免费软件和开源版本。

## 软件

- **[Dropbear](https://en.wikipedia.org/wiki/Dropbear_(software))** — 轻型 SSH 服务器。命令行 ssh 客户端名为 [dbclient(1)](https://man.archlinux.org/man/dbclient.1)。

: [https://matt.ucc.asn.au/dropbear/dropbear.html](https://matt.ucc.asn.au/dropbear/dropbear.html) || [dropbear](https://archlinux.org/packages/?name=dropbear)包

- **[OpenSSH](OpenSSH.md)** — 用于通过 SSH 协议远程登录的高级连接工具

: [https://www.openssh.com/portable.html](https://www.openssh.com/portable.html) || [openssh](https://archlinux.org/packages/?name=openssh)包

### 仅服务端

- **TinySSH** — 一种简约的 SSH 服务器，仅实现 SSHv2 功能的一部分；只依赖 glibc。

: [https://tinyssh.org/](https://tinyssh.org/) || [tinyssh](https://archlinux.org/packages/?name=tinyssh)包

### 仅客户端

- **PuTTY** — 集成终端的 SSH/Telnet 客户端

: [https://www.chiark.greenend.org.uk/~sgtatham/putty/](https://www.chiark.greenend.org.uk/~sgtatham/putty/) || [putty](https://archlinux.org/packages/?name=putty)包

## 保护

请参阅[安全#SSH](../../08_安全与加密/安全/安全.md#SSH)。

## 另请参见

- 终端复用器（通常通过 SSH 使用）
- [Wikipedia:Comparison of SSH clients](https://en.wikipedia.org/wiki/Comparison_of_SSH_clients)
- [Wikipedia:Comparison of SSH servers](https://en.wikipedia.org/wiki/Comparison_of_SSH_servers)
- [SSH 密钥指纹格式](https://blog.webernetz.net/ssh-key-fingerprints/)

## 8.3　通过 SSH 安装 Arch Linux

*难度：进阶 ｜ 篇幅：8.4 KB ｜ 所属：SSH*

将要讲到：在远程（目标）计算机上、在本地计算机上、在无头服务器上安装

本文介绍如何通过 [SSH](安全外壳协议.md) 连接远程安装 Arch。如果计算机位于远程位置或者希望在安装 Arch 时使用 SSH 客户端的复制/粘贴功能，则可以考虑这种方法。

## 在远程（目标）计算机上

> **注意：**这些步骤需要物理访问计算机。如果计算机位于其他位置，则可能需要与其他人协调。

通过 [Live CD/USB 镜像](../../01_安装与包管理/安装过程/安装指南.md)启动计算机进入 Live Arch 环境：用户将以 root 用户身份登录。

这时，按照[安装指南#连接到互联网](../../01_安装与包管理/安装过程/安装指南.md#连接到互联网)在目标计算机上设置网络。

为 SSH 连接设置 root 密码，因为 Arch 中 root 的默认密码为空：

```
# passwd
```

确认在 `/etc/ssh/sshd_config` 中设置了 `PermitRootLogin yes`。如果没有，请设置并[重新加载](../../01_安装与包管理/初始化/Systemd.md#使用单元)OpenSSH 守护进程 `sshd.service` 使更改生效。

> **注意：**如果目标计算机在 NAT 路由器后面，并且需要从外部访问， 则需要将 SSH 端口（默认为 22）转发到目标计算机的 LAN IP 地址。也可以设置一个到本地（客户端）机器的反向 SSH 隧道，并通过它连接，请参见 [ssh(1)](https://man.archlinux.org/man/ssh.1) 的 `-R` 标志。

## 在本地计算机上

在本地计算机上，使用以下命令通过 SSH 连接到目标计算机：

```
$ ssh -o StrictHostKeyChecking=no -o "UserKnownHostsFile /dev/null" root@目标计算机的_IP_地址
```

> **注意：**`-o StrictHostKeyChecking=no -o "UserKnownHostsFile /dev/null"` 选项将阻止验证 Live 环境的 SSH 主机密钥并将其写入到 `~/.ssh/known_hosts`。如果您以前连接过这一 IP 地址，这将避免 `REMOTE HOST IDENTIFICATION HAS CHANGED` 警告。

从这里可以看到 Live 环境的欢迎信息，并且能够像坐在物理键盘旁一样管理目标计算机。此时，如果只想安装 Arch，请遵循[安装指南](../../01_安装与包管理/安装过程/安装指南.md)。如果要编辑现有的已损坏的 Linux 安装，参见[从现有 Linux 发行版安装 Arch Linux](../../01_安装与包管理/安装过程/从现有%20Linux%20发行版安装%20Arch%20Linux.md)。

> **提示：**考虑在目标计算机上使用 [GNU Screen](../../06_软件与应用/GNU/GNU%20Screen.md) 或 [tmux](../../07_开发与工具/终端多路复用器/Tmux.md)（在 Live 环境中两者都可用）。这样，如果断开连接，还可以重新连接到多路复用器的会话。

## 在无头服务器上安装

> **注意：**这些步骤需要物理访问远程计算机。需要有人插入安装介质并启动服务器。

本节介绍在没有键盘、鼠标和显示器的无头服务器上安装 Arch Linux。这种方法使用一个带有 [cloud-init](../../02_系统管理与内核/虚拟化/Cloud-init.md) NoCloud 配置的额外驱动器来自动配置 [OpenSSH](OpenSSH.md) 授权密钥和可选的 [iwd](../网络配置/Iwd.md) 连接。

### 准备 cloud-init 配置文件

需要两个 [cloud-init](../../02_系统管理与内核/虚拟化/Cloud-init.md) 配置文件：`meta-data` 和 `user-data`。

`meta-data` 文件可以为空：

```
$ printf "" > meta-data
```

`user-data` 将包含相关配置：

```
user-data
```

```
#cloud-config
users:
  - name: root
    ssh_authorized_keys:
      - ssh-ed25519 XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
      - ssh-ed25519 YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY
```

将 `ssh-ed25519 *XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX*` 替换为 [SSH 公钥](SSH%20密钥.md)。要添加多个密钥，只需重复上述语句。

要自动连接到 Wi-Fi 网络，请使用 `write_files:` 语句在正确的目录中创建 [iwd 网络配置文件](../网络配置/Iwd.md#网络配置)。例如：

```
user-data
```

```
#cloud-config
users:
  - name: root
    ssh_authorized_keys:
      - ssh-ed25519 XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
      - ssh-ed25519 YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY

write_files:
- content: |
    [Security]
    PreSharedKey=aafb192ce2da24d8c7805c956136f45dd612103f086034c402ed266355297295
  path: /var/lib/iwd/spaceship.psk

runcmd:
- systemctl restart iwd.service
```

创建了这两个文件之后，需要将其放置在一个标签为 `CIDATA` 的 ISO 9660 或 FAT 卷的驱动器上。

### 使用额外的 FAT 格式驱动器

使用 [FAT](../../02_系统管理与内核/文件系统/FAT.md) 格式的驱动器。将 `meta-data` 和 `user-data` 复制到驱动器并将文件系统的 [LABEL](../../04_硬件与外设/硬件检测和故障排除/块设备持久化命名.md#通过分区标签) 更改为 `CIDATA`。

除了包含官方 ISO 的驱动器外，还需要将这个驱动器连接到无头计算机上。

### 使用额外的 ISO

使用 [libisoburn](https://archlinux.org/packages/?name=libisoburn)包 中的 *xorriso* 创建一个 `cloud-init.iso` 文件：

```
$ xorrisofs -output cloud-init.iso -volid CIDATA -joliet -rational-rock meta-data user-data
```

将 cloud-init 数据介质[刻录](../../02_系统管理与内核/光盘/光盘驱动器.md#刻录)到光盘，如果部署选项允许的话，按原样使用 ISO。

### 使用单个 U 盘

如果将安装映像写入到了 U 盘之类的驱动器，并且驱动器上有足够的空间，就可以创建一个额外的分区来存储 cloud-init 数据。

安装 [dosfstools](https://archlinux.org/packages/?name=dosfstools)包、[mtools](https://archlinux.org/packages/?name=mtools)包 和 [libisoburn](https://archlinux.org/packages/?name=libisoburn)包。

首先创建一个 [LABEL](../../04_硬件与外设/硬件检测和故障排除/块设备持久化命名.md#通过分区标签) 设置为 `CIDATA` 的 [FAT](../../02_系统管理与内核/文件系统/FAT.md) 镜像：

```
$ mkfs.fat -C -n CIDATA cloud-init.img 2048
```

将 `meta-data` 和 `user-data` 文件复制到其根目录：

```
$ mcopy -i cloud-init.img meta-data user-data ::
```

重新打包官方 ISO，将 FAT 镜像作为第三个分区包括进来：

```
$ xorriso -indev archlinux-version-x86_64.iso -outdev archlinux-version-x86_64-with-cidata.iso -append_partition 3 0x0c cloud-init.img -boot_image any replay
```

最后按照 [USB flash installation medium#Using the ISO as is (BIOS and UEFI)](../../01_安装与包管理/安装过程/U%20盘安装介质.md#Using_the_ISO_as_is_(BIOS_and_UEFI)) 使用重新打包的 ISO（`archlinux-*version*-x86_64-with-cidata.iso`）准备 U 盘安装介质。

### 使用单个定制的 ISO

除了上述方法，也使用 [Archiso](../../00_关于与社区/Live%20Arch%20系统/Archiso.md) 创建一个自定义 ISO。这样，无论类型如何，都可以只使用一个驱动器。

以 releng 配置文件作为基础。将 [cloud-init 配置文件](#准备_cloud-init_配置文件)放在 `airootfs/var/lib/cloud/seed/nocloud/` 中，然后构建 ISO。

### 从安装介质启动

完成后，使用适当的方法将安装介质和 cloud-init 数据介质（如果是单独的）部署到无头计算机。

启动无头计算机，从安装介质启动到 Live Arch 环境。等待一分钟左右来让无头计算机启动并连接到网络。

从您现有的计算机（带键盘和显示器）通过 [SSH](安全外壳协议.md) 连接到无头服务器上的 Live Arch 环境，并按照[安装指南](../../01_安装与包管理/安装过程/安装指南.md)完成安装。

> **注意：**显然，启动镜像中的所有 Wi-Fi 和 SSH 配置都需要在实际的 Arch Linux 安装中再次配置，以便在安装后允许 WiFi SSH 访问无头计算机。

## 8.4　SSHFS

*难度：进阶 ｜ 篇幅：12.6 KB ｜ 所属：SSH*

将要讲到：安装、选项、Chrooting、Automounting、Troubleshooting、See also

> **翻译状态：**
>
> - 本文（或部分内容）译自 [SSHFS](https://wiki.archlinux.org/title/SSHFS)，最近一次同步于 2023-03-09，若英文版本有所[更改](https://wiki.archlinux.org/title/SSHFS?diff=0&oldid=768113)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/SSHFS_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** 部分内容未翻译（在 [Talk:SSHFS#](https://wiki.archlinuxcn.org/wiki/Talk:SSHFS) 中讨论）

[SSHFS](https://github.com/libfuse/sshfs) 是一个通过 SSH 挂载基于 FUSE 的文件系统的客户端程序。

> **注意：**开发者已经停止开发此项目，[rclone](https://archlinux.org/packages/?name=rclone)包 提供了相同的挂载功能。

## 安装

安装软件包 [sshfs](https://archlinux.org/packages/?name=sshfs)包。

> **提示：**
>
> - If you often need to mount sshfs filesystems you may be interested in using an sshfs helper, such as [qsshfs](https://aur.archlinux.org/packages/qsshfs/)AUR, sftpman, [sshmnt](https://aur.archlinux.org/packages/sshmnt/)AUR or [fmount.py](https://github.com/lahwaacz/Scripts/blob/master/fmount.py).
> - You may want to use [Google Authenticator](../../06_软件与应用/Google/Google%20身份验证器.md) providing additional security as in two-step authentication.
> - [SSH keys](SSH%20密钥.md) may be used over traditional password authentication.

### 挂载

只能挂载 SSH 用户可以访问的目录，用 *sshfs* 挂载远程目录：

```
$ sshfs [user@]host:[dir] mountpoint [options]
```

示例:

```
$ sshfs myuser@mycomputer:/remote/path /local/path -C -p 9876
```

`-p 9876` 指定端口号，`-C` 启用压缩。

未指定是默认挂载远程用户的主目录，默认的用户名和密码可以通过 `~/.ssh/config` 按主机进行配置，这样可以简化 *sshfs* 命令。详情请参考 [OpenSSH#Client usage](OpenSSH.md#Client_usage)。

需要密码时，SSH 会提示输入，如果不想一直手动输入，可以使用 [SSH keys](SSH%20密钥.md)。

### 卸载

要卸载远程系统:

```
$ fusermount3 -u mountpoint
```

例如:

```
$ fusermount3 -u /local/path
```

## 选项

*sshfs* can automatically convert between local and remote user IDs. Use the `idmap=user` option to translate the UID of the connecting user to the remote user `myuser` (GID remains unchanged):

```
$ sshfs myuser@mycomputer:/remote/path /local/path -o idmap=user
```

If you need more control over UID and GID translation, look at the options `idmap=file`, `uidfile` and `gidfile`.

A complete list of options can be found in [sshfs(1)](https://man.archlinux.org/man/sshfs.1).

## Chrooting

You may want to restrict a specific user to a specific directory on the remote system. This can be done by editing `/etc/ssh/sshd_config`:

```
/etc/ssh/sshd_config
```

```
.....
Match User someuser
       ChrootDirectory /chroot/%u
       ForceCommand internal-sftp
       AllowTcpForwarding no
       X11Forwarding no
.....
```

> **注意：**The chroot directory **must** be owned by root, otherwise you will not be able to connect.

See also [SFTP chroot](../FTP/SFTP%20chroot.md). For more information check the [sshd_config(5)](https://man.archlinux.org/man/sshd_config.5) man page for `Match`, `ChrootDirectory` and `ForceCommand`.

## Automounting

Automounting can happen on boot, or on demand (when accessing the directory). For both, the setup happens in the [fstab](../../01_安装与包管理/启动过程/Fstab.md).

> **注意：**Keep in mind that automounting is done through the root user, therefore you cannot use hosts configured in `.ssh/config` of your normal user.
>
> To let the root user use an SSH key of a normal user, specify its full path in the `IdentityFile` option.
>
> **And most importantly**, use each sshfs mount at least once manually **while root** on the client machine so the host's signature is added to the client's `/root/.ssh/known_hosts` file. This can also be done manually by appending one or more of the the SSH server's public host keys (the `/etc/ssh/ssh_host_*key.pub` files) to `/root/.ssh/known_hosts`.

### On demand


With systemd on-demand mounting is possible using `/etc/fstab` entries.

Example:

```
user@host:/remote/path /local/path  fuse.sshfs noauto,x-systemd.automount,_netdev,users,idmap=user,IdentityFile=/home/user/.ssh/id_rsa,allow_other,reconnect 0 0
```

The important mount options here are *noauto,x-systemd.automount,_netdev*.

- *noauto* tells it not to mount at boot
- *x-systemd.automount* does the on-demand magic
- *_netdev* tells it that it is a network device, not a block device (without it "No such device" errors might happen)

> **注意：**After editing `/etc/fstab`, reload the systemd configuration and the required services, which can be found by running `systemctl list-unit-files --type automount`

### On boot

An example on how to use sshfs to mount a remote filesystem through `/etc/fstab`

```
user@host:/remote/path  /local/path  fuse.sshfs  defaults,_netdev  0  0
```

Take for example the *fstab* line

```
llib@192.168.1.200:/home/llib/FAH  /media/FAH2  fuse.sshfs  defaults,_netdev  0  0
```

The above will work automatically if you are using an SSH key for the user. See [Using SSH Keys](SSH%20密钥.md).

If you want to use sshfs with multiple users, add the following option:

```
user@domain.org:/home/user  /media/user   fuse.sshfs    defaults,allow_other,_netdev    0  0
```

In order to ensure the network is available before trying to mount, it is not only important to set the `_netdev` mount option, but also to add either `--any` or a specific `--interface` to the appropriate [wait-online service](../../01_安装与包管理/初始化/Systemd.md#Running_services_after_the_network_is_up) for your network manager.

### Secure user access

When automounting via [fstab](../../01_安装与包管理/启动过程/Fstab.md), the filesystem will generally be mounted by root. By default, this produces undesireable results if you wish access as an ordinary user and limit access to other users.

An example mountpoint configuration:

```
user@host:/remote/path /local/path  fuse.sshfs noauto,x-systemd.automount,_netdev,user,idmap=user,follow_symlinks,identityfile=/home/user/.ssh/id_rsa,allow_other,default_permissions,uid=USER_ID_N,gid=USER_GID_N 0 0
```

Summary of the relevant options:

- *allow_other* - Allow other users than the mounter (i.e. root) to access the share.
- *default_permissions* - Allow kernel to check permissions, i.e. use the actual permissions on the remote filesystem. This allows prohibiting access to everybody otherwise granted by *allow_other*.
- *uid*, *gid* - set reported ownership of files to given values; *uid* is the numeric user ID of your user, *gid* is the numeric group ID of your user.

## Troubleshooting

### Checklist

Read [OpenSSH#Checklist](OpenSSH.md#Checklist) first. Further issues to check are:

1.  Is your SSH login sending additional information from server's `/etc/issue` file e.g.? This might confuse SSHFS. You should temporarily deactivate server's `/etc/issue` file:

    ```
    $ mv /etc/issue /etc/issue.orig
    ```

2.  Keep in mind that most SSH related troubleshooting articles you will find on the web are **not** systemd related. Often `/etc/fstab` definitions wrongly begin with

    ```
    sshfs#user@host:/mnt/server/directory ... fuse ...
    ```

    instead of using the syntax

    ```
    user@host:/mnt/server/directory ... fuse.sshfs ... x-systemd, ...
    ```

3.  Check that the owner of server's source directory and content is owned by the server's user.

    ```
    $ chown -R user_s: /mnt/servers/directory
    ```

4.  The server's user ID can be different from the client's one. Obviously both user names have to be the same. You just have to care for the client's user IDs. SSHFS will translate the UID for you with the following mount options:

    ```
    uid=USER_C_ID,gid=GROUP_C_ID
    ```

5.  Check that the client's target mount point (directory) is owned by the client user. This directory should have the same user ID as defined in SSHFS's mount options.

    ```
    $ chown -R user_c: /mnt/client/directory
    ```

6.  Check that the client's mount point (directory) is empty. By default you cannot mount SSHFS directories to non-empty directories.

### Connection reset by peer

- If you are trying to access the remote system with a hostname, try using its IP address, as it can be a domain name resolving issue. Make sure you edit `/etc/hosts` with the server details.
- Make sure your user can log into the server (especially when using `AllowUsers`).
- Make sure `Subsystem sftp /usr/lib/ssh/sftp-server` is enabled in `/etc/ssh/sshd_config` on the remote system.
- If you are using a non-default key name and passing it as `-i .ssh/my_key`, this will not work. You have to use `-o IdentityFile=/home/*user*/.ssh/*my_key*`, with the full path to the key.
- If your `/root/.ssh/config/` is a symlink, you will be getting this error as well. See [this serverfault topic](https://serverfault.com/questions/507748/bad-owner-or-permissions-on-root-ssh-config)
- Adding the option `sshfs_debug` (as in `sshfs -o sshfs_debug *user*@*server* ...`) can help in resolving the issue.
- If that does not reveal anything useful, you might also try adding the option `debug`.
- If you are trying to sshfs into a router running DD-WRT or the like, there is a solution [here](https://www.dd-wrt.com/wiki/index.php/SFTP_with_DD-WRT). (Note that the `-osftp_server=/opt/libexec/sftp-server` option can be used to the sshfs command instead of patching dropbear).
- If you see this only on boot, it may be that systemd is attempting to mount prior to a network connection being available. Enabling the appropriate [wait-online service](../../01_安装与包管理/初始化/Systemd.md#Running_services_after_the_network_is_up) for your network manager fixes this.
- Old Forum thread: [sshfs: Connection reset by peer](https://bbs.archlinux.org/viewtopic.php?id=27613).

> **注意：**When providing more than one option for sshfs, they must be comma separated. Like so: `sshfs -o sshfs_debug,IdentityFile=*/path/to/key* *user*@*server* ...`).

### Remote host has disconnected

If you receive this message directly after attempting to use *sshfs*:

- First make sure that the **remote** machine has *sftp* installed! It will not work, if not.
- Then, check that the path of the `Subsystem sftp` in `/etc/ssh/sshd_config` on the remote machine is valid.

### fstab mounting issues

To get verbose debugging output, add the following to the mount options:

```
ssh_command=ssh\040-vv,sshfs_debug,debug
```

> **注意：**Here, `\040` represents a space which fstab uses to separate fields.

To be able to run `mount -av` and see the debug output, remove the following:

```
 noauto,x-systemd.automount
```

### Some directories are empty

sshfs by default does not support symlinks. If those directories happened to be symlinks, use:

```
$ sshfs user@host:/remote/path /local/path -o follow_symlinks
```

### Files not refreshed

If you see old content on remote, consider using option `dir_cache=no`:

```
$ sshfs user@host:/remote/path /local/path -o dir_cache=no
```

### Limited transfer on fast network

If you observe transfer than is lower than your network capabilities and high CPU usage on the party where files are copied from, disable compression (remove `-C` option or set `-o compression=no`).

## See also

- [How to mount chrooted SSH filesystem](https://wiki.gilug.org/index.php/How_to_mount_SFTP_accesses), with special care with owners and permissions questions.

## 8.5　SSH 密钥

*难度：高级 ｜ 篇幅：46.0 KB ｜ 所属：SSH*

将要讲到：背景、生成密钥对、将公钥复制到远程服务器上、SSH 代理、疑难排解、参考

对于使用[公钥加密](https://zh.wikipedia.org/wiki/%E5%85%AC%E5%BC%80%E5%AF%86%E9%92%A5%E5%8A%A0%E5%AF%86)与[质询-应答式认证](https://en.wikipedia.org/wiki/Challenge-response_authentication)的 SSH 服务器，SSH 密钥可以作为证明身份的方法。基于密钥的认证主要优点在于，与密码认证相比，它不易遭受[暴力破解攻击](https://zh.wikipedia.org/wiki/%E8%9B%AE%E5%8A%9B%E6%94%BB%E5%87%BB)，且在服务器被攻破的情况下也不会泄露您的有效凭证（详细信息请参考 [RFC 4251 9.4.4](https://tools.ietf.org/html/rfc4251#section-9.4.4)）。

不仅如此，SSH 密钥认证也会比传统的密码认证更加方便。当与被称作 SSH agent 的程序共用时，SSH 密钥可以让您无需记住或输入每个系统的密码，就能够连接到一个或多个服务器。

基于密钥的认证并非没有缺点，可能也并非适用于一切环境，但在很多情况下可以提供一些有力的优势。对 SSH 密钥如何工作的概略理解会帮助您决定如何以及何时使用它们，以满足您的需求。

本文假定您已经对于[安全外壳协议](安全外壳协议.md)有了基本理解，并安装了 [openssh](https://archlinux.org/packages/?name=openssh)包 软件包。

## 背景

SSH 密钥都是成对生成的，其一称为公钥，另一则称为私钥。私钥只由您所知，必须安全保管。相对地，公钥可以向您想连接的任何 SSH 服务器自由地共享。

如果一个 SSH 服务器在文件中存有您的公钥，并收到了您的连接请求，就会使用您的公钥构建一个质询问题并发送给您。这一质询问题是一条加密信息，必须得到正确应答，服务器才能允许您访问。这条编码信息特别安全是在于，只有私钥持有者才能理解它。公钥可以用来加密信息，但不能用来解密同一条信息。只有您，私钥的持有者，能够正确理解这一质询问题并产生合适的应答。

这一[质询-应答](https://en.wikipedia.org/wiki/Challenge-response_authentication)过程发生在后台，对用户不可见。只要您持有私钥（一般存放在 `~/.ssh/` 目录下），您的 SSH 客户端就应当能够向服务器回复正确的应答。

私钥是受保护的秘密，因此建议以加密形式将其存储在磁盘上。当需要被加密的私钥时，为了解密它，需要先输入密码。虽然这在表面上可能像是您在向 SSH 服务器提供登录密码，但该密码只用于解密本地系统上的私钥。该密码并未通过网络传输。

## 生成密钥对

通过运行 `ssh-keygen` 命令可以生成密钥对，具体“*一般被认为充足*”且应当兼容于几乎所有客户端和服务器的选项请参考 [ssh-keygen(1)](https://man.archlinux.org/man/ssh-keygen.1)：

```
$ ssh-keygen
```

```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/username/.ssh/id_ed25519):
Created directory '/home/username/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/username/.ssh/id_ed25519
Your public key has been saved in /home/username/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:RLy4JBv7jMK5qYhRKwHB3af0rpMKYwE2PBhALCBV3G8 username@hostname
The key's randomart image is:
+--[ED25519 256]--+
|%oooo. ..        |
|== ..o.o.        |
|==  . +o..       |
|+ o o.ooE        |
|...  *.oS        |
| o..o ..         |
|o=.. +o          |
|+o*..+o          |
|+.o+. .          |<br>+----[SHA256]-----+<br>```

[randomart 图像](https://www.cs.berkeley.edu/~dawnsong/papers/randomart.pdf)作为一种简便的密钥指纹视觉辨识方式在 [OpenSSH 5.1](https://www.openssh.com/txt/release-5.1) 中引入。

> **注意：**可以使用 `-a` 开关来指定密码加密的 KDF rounds 数量。

也可以用 `-C` 开关对公钥添加可选的注释栏，从而更容易在 `~/.ssh/known_hosts`、`~/.ssh/authorized_keys` 以及 `ssh-add -L` 输出等处进行辨识。例如：

```
$ ssh-keygen -C "$(whoami)@$(uname -n)-$(date -I)"
```

会添加一条注释，说明是哪个用户何时在哪台机器上创建的密钥。

### 选择认证密钥类别

OpenSSH（对于认证密钥）支持数种签名算法，按照所采用的数学性质可分为两类：

1.  [Ed25519](https://zh.wikipedia.org/wiki/Curve25519) 以及 [ECDSA](https://zh.wikipedia.org/wiki/%E6%A4%AD%E5%9C%86%E6%9B%B2%E7%BA%BF%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D%E7%AE%97%E6%B3%95)，依赖于椭圆曲线[离散对数](https://zh.wikipedia.org/wiki/%E7%A6%BB%E6%95%A3%E5%AF%B9%E6%95%B0)问题（ECDLP）；（[例子](https://www.certicom.com/content/certicom/en/52-the-elliptic-curve-discrete-logarithm-problem.html)）
2.  [RSA](https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E7%AE%97%E6%B3%95)，依赖于对两个大质数之积进行分解的[实际困难度](https://zh.wikipedia.org/wiki/%E6%95%B4%E6%95%B0%E5%88%86%E8%A7%A3#.E9.9A.BE.E5.BA.A6.E4.B8.8E.E5.A4.8D.E6.9D.82.E5.BA.A6)。

[椭圆曲线密码学](https://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography)（ECC）算法是对于公钥密码体制的[较新发展](https://zh.wikipedia.org/wiki/%E6%A4%AD%E5%9C%86%E6%9B%B2%E7%BA%BF%E5%AF%86%E7%A0%81%E5%AD%A6#.E6.AD.B7.E5.8F.B2)。其主要优势之一是[以较小密钥提供同等安全性](https://zh.wikipedia.org/wiki/%E6%A4%AD%E5%9C%86%E6%9B%B2%E7%BA%BF%E5%AF%86%E7%A0%81%E5%AD%A6#Rationale)的能力，使得计算密集操作更少（*也就是*密钥创建、加密及解密更快）且存储及传输的要求更低。

由于安全问题，DSA 密钥已被[废弃](https://archlinux.org/news/openssh-70p1-deprecates-ssh-dss-keys/)，且 SSH 实现正逐渐将其移除。OpenSSH 9.8 构建默认不带 DSA 密钥支持，Dropbear 2022.83 禁用了 DSA 密钥支持，libssh 0.11.0 完全移除了 DSA 密钥支持。因此[密码体制](https://zh.wikipedia.org/wiki/%E5%AF%86%E7%A0%81%E4%BD%93%E5%88%B6)的选择范围就限定在了 RSA 或两种 ECC 之一当中。

[#RSA](#RSA) 密钥会提供最大的可移植性，而 [#Ed25519](#Ed25519) 会提供最佳的安全性，但需要新版本的客户端与服务器。. [#ECDSA](#ECDSA) 兼容性可能比 Ed25519 更好（虽然还是不如 RSA），但存在对于其安全性的怀疑（见下文）。

默认的 Ed25519 会提供最佳安全性和不错的性能。ECDSA 比 Ed25519 更慢，但比 RSA 快，在安全性上存在些疑问（具体参见下文）。RSA 密钥提供了对旧服务器的最佳兼容性，但需要更大的密钥才能提供足够的安全性。

> **注意：**这些密钥只用于认证您的身份；选择更强的密钥并不会在通过 SSH 传输数据时加重 CPU 负担。

> **提示：**为防止 SSH 密钥从设备上外泄，可以将密钥储存在 [FIDO/U2F 实体认证器](../../08_安全与加密/通用第二因素/通用第二因素.md)或[可信平台模块](../../04_硬件与外设/硬件/可信平台模块.md)中。
>
> - 在生成密钥时，可以使用特殊的“安全密钥”类型将 Ed25519 或 ECDSA 密钥储存在 FIDO/U2F 实体认证器上。具体信息请参考 [#FIDO/U2F](#FIDO/U2F)。
> - 可信平台模块支持 ECDSA 和 RSA，因此可以将 SSH 密钥封装在 TPM 内。具体信息请参考 [可信平台模块#SSH](../../04_硬件与外设/硬件/可信平台模块.md#SSH)。

#### Ed25519

[Ed25519](https://ed25519.cr.yp.to/) 在2014年1月被引入 [OpenSSH 6.5](https://www.openssh.com/txt/release-6.5)：“*Ed25519 是一种能够提供比 ECDSA 及 DSA 更佳安全性及良好性能的椭圆曲线签名体系*”。其主要长处在于速度、时间恒定运行时（从而能够对抗侧信道攻击）以及无需晦暗不明的硬编码常数。 关于其工作方式，参见由一位 Mozilla 开发者撰写的[博客文章](https://blog.mozilla.org/warner/2011/11/29/ed25519-keys/)。

它已经在[众多应用及库中](https://zh.wikipedia.org/wiki/EdDSA#.E8.BD.AF.E4.BB.B6)得到实现，并且在 OpenSSH 中是[默认的密钥交换算法](https://www.libssh.org/2013/11/03/openssh-introduces-curve25519-sha256libssh-org-key-exchange/)（与密钥*签名*不同）。

Ed25519 密钥对生成方法如下：

[ssh-keygen(1)](https://man.archlinux.org/man/ssh-keygen.1) 默认使用 Ed25519，因此不需要使用 `-t ed25519` 选项进行指定。使用以下命令生成密钥：

```
$ ssh-keygen
```

所有 Ed25519 密钥都是 256 位，故无需设置密钥尺寸。

注意较老的 SSH 客户端与服务器可能不支持这些密钥。

#### ECDSA

椭圆曲线数字签名算法（ECDSA）是 [OpenSSH 5.7](https://www.openssh.com/txt/release-5.7)（2011-01-24）到 OpenSSH 6.5（2014-01-30）间的首选认证（密钥交换）算法。

对于它的担忧有两种：

1.  *政治上的担忧*：在 NSA（美国国家安全局）被曝在软件、硬件组件及发布的标准中蓄意植入后门之后，由 NIST（美国国家标准技术研究所）产生的曲线的可信性[受到了质疑](https://crypto.stackexchange.com/questions/10263/should-we-trust-the-nist-recommended-ecc-parameters)；密码领域的知名人士对于 NIST 曲线的设计方式[表示](https://www.schneier.com/blog/archives/2013/09/the_nsa_is_brea.html#c1675929) [了](https://safecurves.cr.yp.to/rigid.html) [怀疑](https://www.hyperelliptic.org/tanja/vortraege/20130531.pdf)，并且之前已经有主动的污染[被](https://www.schneier.com/blog/archives/2007/11/the_strange_sto.html) [证实](https://www.scientificamerican.com/article/nsa-nist-encryption-scandal/)。
2.  *技术上的担忧*：对于[正确实现该标准的困难](https://blog.cr.yp.to/20140323-ecdsa.html)以及在实现不够谨慎情况下降低安全性的[迟缓与设计缺陷](https://www.gossamer-threads.com/lists/openssh/dev/57162#57162)。

[libssh curve25519 介绍](https://git.libssh.org/projects/libssh.git/tree/doc/curve25519-sha256@libssh.org.txt#n4)当中很好地总结了上述担忧。尽管政治上的担忧仍受到争议，但有[明确的共识](https://news.ycombinator.com/item?id=7597653)认为 [#Ed25519](#Ed25519) 技术上更优越，因此应当优先采用。

可以使用以下方法生成 ECDSA 密钥对：

```
$ ssh-keygen -t ecdsa
```

ECDSA 密钥支持三种椭圆曲线大小：256，384 和 521 位，默认使用 256 位。如果想要生成更安全的 ECDSA 密钥对，可以使用 `-b` 选项进行指定：

```
$ ssh-keygen -t ecdsa -b 384
```

#### RSA

RSA 提供所有算法当中最佳的兼容性，但提供充足安全性所需的密钥尺寸较大。最低密钥大小为 1024 位，默认为 3072（具体请参考 [ssh-keygen(1)](https://man.archlinux.org/man/ssh-keygen.1)），最大为 16384。

使用以下命令生成 RSA 密钥对：

```
$ ssh-keygen -t rsa
```

如果您想生成较强的 RSA 密钥对（*例如*为了防范先进或未知攻击以及较高级的攻击者），只需为 `-b` 选项指定比默认更高的位值：

```
$ ssh-keygen -t rsa -b 4096
```

需要明白的是，使用更长的密钥存在收益递减。 GnuPG 常见问答记载：“*如果你需要的安全性比 RSA-2048 所提供的更强，应该做的是改用椭圆曲线密码学——而不是接着使用 RSA*。”

另一方面，最新版本的 [NSA Fact Sheet Suite B Cryptography](https://web.archive.org/web/20160305203915/https://www.nsa.gov/ia/programs/suiteb_cryptography/index.shtml) 建议对于 RSA 使用最少 3072 位的模，同时“*为即将到来的抗量子算法迁移（做准备）*”。

#### FIDO/U2F


[OpenSSH 8.2 版本](https://www.openssh.com/txt/release-8.2)添加了 FIDO/[U2F](../../08_安全与加密/通用第二因素/通用第二因素.md) [实体认证器](https://zh.wikipedia.org/wiki/%E5%AE%89%E5%85%A8%E4%BB%A4%E7%89%8C)支持，可以使用上文描述的两种椭圆曲线签名方案。这使得可以将连接的实体令牌作为多重验证方案与私钥搭配使用。

实体令牌需要用到 [libfido2](https://archlinux.org/packages/?name=libfido2)包。

> **注意：**
>
> - 客户端和服务器都需要支持 `ed25519-sk` 和 `ecdsa-sk` 密钥类型。
> - OpenSSH 使用中间件库来与实体令牌通信，其带有支持 USB 令牌的内部中间件。如果要使用其它中间件，可以通过[sshd_config(5) § SecurityKeyProvider](https://man.archlinux.org/man/sshd_config.5#SecurityKeyProvider) 配置项或为 `ssh-keygen` 和 `ssh-add` 配置 `SSH_SK_PROVIDER` 环境变量来实现。

连接兼容的 FIDO 密钥后，使用以下命令生成密钥对：

```
$ ssh-keygen -t ed25519-sk
```

这一步通常需要输入 PIN 码并/或触摸令牌进行确认。除非在生成时使用了 `-O no-touch-required` 命令行选项或服务器指定了 [sshd(8) § no-touch-required](https://man.archlinux.org/man/sshd.8#no-touch-required) `authorized_keys` 选项，否则连接服务器时通常需要触摸令牌进行确认。

要生成不需要触摸确认的密钥，需在生成密钥对时使用 `no-touch-required` 选项，例如：

```
$ ssh-keygen -O no-touch-required -t ed25519-sk
```

> **注意：**并不是所有实体令牌都支持该选项。如果你使用的是 YubiKey，需要 5.2.3 固件版本才能使用 ed25519-sk 类型密钥。

另外，`sshd` 默认会拒绝 `no-touch-required` 密钥。为使用通过该选项生成的密钥，需在 `authorized_keys` 文件内对每个密钥单独进行启用：

```
no-touch-required sk-ssh-ed25519@openssh.com AAAAInN... user@example.com
```

或是在 `/etc/ssh/sshd_config` 中对整个系统进行启用：

```
PubkeyAuthOptions none
```

> **提示：**[GitHub](https://github.com/orgs/community/discussions/10593) 和 [GitLab](https://gitlab.com/gitlab-org/gitlab/-/issues/367682) 不支持 `no-touch-required`。

基于 ECDSA 的密钥对可通过 `ecdsa-sk` 密钥类型生成，但同样会有 [#ECDSA](#ECDSA) 一节提到的问题：

```
$ ssh-keygen -t ecdsa-sk
```

### 选择密钥存储位置以及密码短语

运行 `ssh-keygen` 时，它会询问您希望的私钥文件名称及位置。默认情况下，密钥保存到 `~/.ssh` 目录下，并根据所使用的加密类型命名。为使下文中的示例代码正确工作，建议您接受默认的名称和位置。

当系统向您询问密码短语时，如果您在乎私钥的安全性，请选择难以猜到的密码。更长、更随机的密码一般会更强，当私钥落入贼人之手时更不容易被破解掉。

在没有密码短语的情况下生成私钥也是可能的。虽然也许很方便，但您需要明白随之而来的风险。在没有密码短语的情况下，您的私钥会以未加密形式存储在硬盘上。任何能接触到您私钥文件的人之后都能够在您使用基于密钥认证连接的任何 SSH 服务器面前冒用您的身份。更进一步，没有密码短语，您也必须信任 root 用户，因为他可以绕过文件权限并能够随时访问您未加密的私钥文件。

> **注意：**从前，私钥密码都是以一种不安全的方式编码的：仅一遍 MD5 散列。OpenSSH 6.5 及之后版本支持一种新的、更安全的格式来编码您的私钥。从 [OpenSSH 7.8 版本](https://www.openssh.com/txt/release-7.8)开始默认使用该格式。Ed25519 密钥一直采用新的编码格式。只需按下节所述更改密钥的密码短语即可升级到新格式。

#### 不修改密钥对的情况下修改密码短语

如果不希望使用原本选择的 SSH 密钥密码短语或者必须更换，可以使用 `ssh-keygen` 命令来修改密码短语，而无需改动实际密钥。此法也可用于将密码编码格式改为新标准。

```
$ ssh-keygen -f ~/.ssh/id_rsa -p
```

#### 管理多组密钥对

如果你有多个 SSH 身份，可以在配置文件中通过 `Host` 和 `IdentityFile` 为不同的主机和远程用户使用不同的密钥：

```
~/.ssh/config
```

```
Host SERVER1
   IdentitiesOnly yes
   IdentityFile ~/.ssh/id_rsa_IDENTITY1

Host SERVER2 SERVER3
   IdentitiesOnly yes
   IdentityFile ~/.ssh/id_ed25519_IDENTITY2
```

对于这些选项的完整描述见 [ssh_config(5)](https://man.archlinux.org/man/ssh_config.5)。

#### 在硬件令牌上存储 SSH 密钥

SSH 密钥也可以存储在智能卡或 USB 令牌等安全令牌上。这样做的好处是私钥安全地存储在令牌上，而不是存储在磁盘中。当使用安全令牌时，敏感的私钥也从来不会存在于 PC 的内存当中；密码学操作在令牌自身上进行。密码学令牌还有一点好处是，它不与单台电脑绑定；可以将其从一台电脑上轻松取下，四处携带之后在其他电脑上使用。

硬件令牌的例子可参考：

- [#FIDO/U2F](#FIDO/U2F)
- [YubiKey#SSH notes](../../08_安全与加密/Smartcards/YubiKey.md#SSH_notes) 用于 FIDO/U2F keys 的原生 OpenSSH 支持
- [YubiKey#SSH keys](../../08_安全与加密/Smartcards/YubiKey.md#SSH_keys)
- [可信平台模块#SSH](../../04_硬件与外设/硬件/可信平台模块.md#SSH)

## 将公钥复制到远程服务器上


创建好密钥对之后，您需要将公钥上传到远程服务器上，以便用于 SSH 密钥认证登录。公钥和私钥的文件名相同，只不过公钥文件带有扩展名 `.pub` 而私钥文件名则没有。千万不要将私钥上传，私钥应该保存在本地。

### 简易方式

如果您的公钥文件为 `~/.ssh/id_rsa.pub`，只需要输入命令：

```
$ ssh-copy-id remote-server.org
```

如果您的远程服务器用户名与本地的不同，您需要在远程主机名前以 `@` 隔开指定用户名：

```
$ ssh-copy-id username@remote-server.org
```

如果您的公钥文件名不是默认的 `~/.ssh/id_rsa.pub`，会出现报错 `/usr/bin/ssh-copy-id: ERROR: No identities found`。这种情况下，您需要显式提供公钥的位置：

```
$ ssh-copy-id -i ~/.ssh/id_ed25519.pub username@remote-server.org
```

如果远程服务器监听端口不是默认的 22，请在主机参数中指明：

```
$ ssh-copy-id -i ~/.ssh/id_ed25519.pub -p 221 username@remote-server.org
```

### 手动方式

对于 OpenSSH，默认需要将公钥添加到 `~/.ssh/authorized_keys`。先从复制公钥到远程服务器开始。

```
$ scp ~/.ssh/id_ecdsa.pub username@remote-server.org:
```

以上示例通过 `scp` 将公钥（`id_ecdsa.pub`）复制到您在远程服务器上的主目录。别忘了加上服务器地址末尾的 `:`。并请注意，您的公钥名称可能与此例所示不同。

在远程服务器上，如果还没有 `~/.ssh` 目录，您需要创建一个；然后将您的公钥追加到 `authorized_keys` 文件内。

```
$ ssh username@remote-server.org
username@remote-server.org's password:
$ install -dm700 ~/.ssh
$ cat ~/id_ecdsa.pub >> ~/.ssh/authorized_keys
$ rm ~/id_ecdsa.pub
$ chmod 600 ~/.ssh/authorized_keys
```

上面最后两个命令将公钥文件从服务器上删除，并设置 `authorized_keys` 文件的权限，这样只有作为文件拥有者的您能够读取及写入。

## SSH 代理

如果您的私钥使用密码短语来加密了的话，每一次试图连接到使用公钥认证的 SSH 服务器的时候，您都必须输入密码短语。每次唤起 `ssh` 或 `scp` 时都需要密码短语来解密您的私钥以便认证能够继续。

而 SSH 代理程序能够将您的已解密的私钥缓存起来，并代表您将其提供给 SSH 客户端。这样子，您就只需要将私钥加入 SSH 代理缓存的时候输入一次密码短语就可以了。在经常使用 SSH 连接时，将会提供不少便利。

SSH 代理一般会设置成在登录会话的时候自动启动，并在整个会话中保持运行。有多种代理、前端及配置都能够达成这一效果。本节概述了多种不同的解决方案，能够适应以满足您的特定需求。

### ssh-agent

`ssh-agent` 是 OpenSSH 自带的默认代理。它可以直接使用，或者作为下文所述的几种前端解决方案的后端。`ssh-agent` 运行时会自动 fork 到后台，然后打印出其所需的环境变量，例如：

```
$ ssh-agent
```

```
SSH_AUTH_SOCK=/tmp/ssh-vEGjCM2147/agent.2147; export SSH_AUTH_SOCK;
SSH_AGENT_PID=2148; export SSH_AGENT_PID;
echo Agent pid 2148;
```

要使用这些环境变量，请使用 `eval` 命令来运行它。如果使用的是 `fish` shell，需要转而使用 `ssh-agent -c`。

```
$ eval $(ssh-agent)
```

```
Agent pid 2157
```

`ssh-agent` 运行起来之后，您还需要将您的私钥加入它的缓存。

```
$ ssh-add ~/.ssh/id_ed25519
```

```
Enter passphrase for /home/user/.ssh/id_ed25519:
Identity added: /home/user/.ssh/id_ed25519 (/home/user/.ssh/id_ed25519)
```

如果您的私钥已加密，`ssh-add` 会提示您输入密码短语。一旦您的私钥被成功添加到代理，您不需要再输入密码短语就能够建立 SSH 连接了。

> **提示：**要让包括 `git` 在内的所有 `ssh` 客户端在第一次使用时就把密钥存储在代理中，请将配置 `AddKeysToAgent yes` 添加到 `~/.ssh/config`。其他可能的值有 `confirm`、`ask` 和 `no`（默认）。

为了自动启动代理并确保同一时间只有一个 `ssh-agent` 进程在运行，添加以下内容到您的 `~/.bashrc`：

```
if ! pgrep -u "$USER" ssh-agent > /dev/null; then
    ssh-agent -t 1h > "$XDG_RUNTIME_DIR/ssh-agent.env"
fi
if [[ ! -f "$SSH_AUTH_SOCK" ]]; then
    source "$XDG_RUNTIME_DIR/ssh-agent.env" >/dev/null
fi
```

如果还没有 `ssh-agent` 进程在运行的话，该脚本会自动启动一个，并保存其输出。如果已经有一个在运行了，就会读取缓存的 `ssh-agent` 输出并执行之，从而设置必要的环境变量。解密密钥的存活时间设置为1小时。

本节后面会介绍几种 `ssh-agent` 的前端及其它代理，也能够避免这一问题。

#### 用 systemd user 启动 ssh-agent

如果想要在登录时运行 SSH agent 而不论 X 是否在运行的话，从 9.4p1-3 版本开始 [openssh](https://archlinux.org/packages/?name=openssh)包 提供了 `ssh-agent.service`，可以作为[用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)启用。

然后将[环境变量](../../02_系统管理与内核/系统管理/环境变量.md#按用户) `SSH_AUTH_SOCK` 设为 `$XDG_RUNTIME_DIR/ssh-agent.socket`。

> **注意：**如果您使用 GNOME，该环境变量默认会被覆盖。见 [GNOME/Keyring#禁用钥匙环的守护进程组件](../../05_桌面与图形/GNOME/GNOME_Keyring.md#禁用钥匙环的守护进程组件)。

#### 转发 ssh-agent

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**This is not specific to ssh-agent, e.g. gpg-agent uses the same environment variable: [GnuPG#Forwarding gpg-agent and ssh-agent to remote](../邮件/GnuPG.md#Forwarding_gpg-agent_and_ssh-agent_to_remote)（在[Talk:SSH 密钥](https://wiki.archlinuxcn.org/wiki/Talk:SSH_%E5%AF%86%E9%92%A5)讨论）

When [forwarding](OpenSSH.md#Agent_forwarding) a local `ssh-agent` to remote (e.g., through command-line argument `ssh -A remote` or through `ForwardAgent yes` in the configuration file), it is important for the remote machine not to overwrite the environment variable `SSH_AUTH_SOCK`. So if the remote machine uses a [systemd unit](#Start_ssh-agent_with_systemd_user) shown previously to start the agent, `SSH_AUTH_SOCK` must not be set in the environment when a user is logged in through SSH. Otherwise, the forwarding may fail, and you may see errors (for example: `The agent has no identities`) when checking the existing keys with `ssh-add -l` on the remote machine.

For example, if using bash, the `.bashrc` could be something like:

```
~/.bashrc
```

```
...
if [[ -z "${SSH_CONNECTION}" ]]; then
    export SSH_AUTH_SOCK="$XDG_RUNTIME_DIR/ssh-agent.socket"
fi
...
```

In this way, `SSH_AUTH_SOCK` is only set when the current session is **not** a SSH login. And when this is a SSH session, `SSH_AUTH_SOCK` on the remote machine is then set by the local machine to make the forwarding work.

#### 作为包装程序运行 ssh-agent

[加州大学伯克利分校实验室的这份 ssh-agent 教程](https://upc.lbl.gov/docs/user/sshagent.shtml)还介绍了一种随每个 X 会话启动 `ssh-agent` 的方法。基本用法是，如果您使用命令 `startx` 正常启动 X，您可以改成像这样在前面加上 `ssh-agent`：

```
$ ssh-agent startx
```

您也可以在 `.bash_aliases` 或等同文件中设置别名，就不用再费心了：

```
alias startx='ssh-agent startx'
```

这样做可以避免多个会话中存在多余的 `ssh-agent` 进程。整个 X 会话中只会有一个 `ssh-agent` 在运行。

> **注意：**`ssh-askpass` 需要 `DISPLAY` 或 `WAYLAND_DISPLAY` 环境变量才能正常工作，所以你可能会需要在 `~/.xinitrc` 中运行 `ssh-agent` 以配置 `DISPLAY` 选项，而不是使用 `ssh-agent startx`（如将 `exec ssh-agent dbus-launch i3` 添加到 `~/.xinitrc`）。或者也可以将 `eval $(ssh-agent)` 添加到 `~/.xinitrc` 以使用 `ssh-agent` 作为包装程序运行。

见[下文有关协同使用 x11-ssh-askpass 与 ssh-add 的说明](#Calling_x11-ssh-askpass_with_ssh-add)了解如何立即将密钥添加到 agent。

### OpenPGP card ssh-agent

This ssh-agent specializes on OpenPGP card integration. It uses private keys that are stored in OpenPGP card authentication slots.

> **注意：** When also using [GnuPG](../邮件/GnuPG.md) while running this agent, it is required to [use it with pcscd](../邮件/GnuPG.md#GnuPG_with_pcscd_(PCSC_Lite)) and configure [shared access with pcscd](../邮件/GnuPG.md#Shared_access_with_pcscd).

Install [openpgp-card-ssh-agent](https://archlinux.org/packages/?name=openpgp-card-ssh-agent)包 and enable and start the `openpgp-card-ssh-agent.socket` user unit.

Afterwards add the relevant environment variable for this agent:

```
export SSH_AUTH_SOCK="$XDG_RUNTIME_DIR/openpgp-card/ssh-agent.sock"
```

#### User PIN handling

The user PIN for the OpenPGP card is persisted via an [org.freedesktop.secrets](https://archlinux.org/packages/?q=org.freedesktop.secrets) provider (such as [GNOME Keyring](../../05_桌面与图形/GNOME/GNOME_Keyring.md), KeePassXC or [KDE Wallet](../../05_桌面与图形/KDE/KDE%20Wallet.md)) by default. The PIN storage backend is [configurable and extendable](https://codeberg.org/openpgp-card/state).

The user PIN needs to be persisted only once for each OpenPGP card. Prior to the first SSH connection with this agent, list the available SSH public keys and add their respective card identifiers:

```
$ ssh-add -L
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJUz6VnFprMe33G88Pq8NLw3wnIKOsBg0CDrwFeUVrU6 FFFE:01234567
$ ssh-add -s FFFE:01234567
Enter passphrase for PKCS#11:
```

> **注意：** The prompt mentions *PKCS#11* as it is a hardcoded message in [ssh-add(1)](https://man.archlinux.org/man/ssh-add.1). However, in this context the OpenPGP card user PIN must be entered.

### GnuPG Agent

[gpg-agent](../邮件/GnuPG.md) 模拟了 OpenSSH 的代理协议，必要配置请参考 [GnuPG#SSH 代理](../邮件/GnuPG.md#SSH_代理)。

### Keychain

[Keychain](https://www.funtoo.org/Keychain) 是一个用来方便管理 SSH 密钥对的程序，它能尽最大努力去减少对用户的打扰。实际上，它就是一个 shell 脚本，驱动 *ssh-agent* 或者 *gpg-add* 来工作。一个值得注意的特性是，Keychain 在多个会话中重复使用同一个 *ssh-agent* 进程。这意味着您只需要在机器启动时输入一次密码短语即可。

#### 安装

安装 [keychain](https://archlinux.org/packages/?name=keychain)包 软件包。

#### 配置

> **警告：**直至 2015-09-26 为止，`-Q, --quick` 选项存在预期之外的副作用，导致 *keychain* 在重新登录时切换到新产生的 *ssh-agent* （至少在使用 [GNOME](../../05_桌面与图形/GNOME/GNOME.md) 的系统上如此），使您不得不重新添加所有先前注册的密钥。

将类似以下的行加入到您 [shell](../../07_开发与工具/命令行解释器/命令行解释器.md) 的配置文件中，*例如*若您使用[Bash](../../07_开发与工具/命令行解释器/Bash.md)：

```
~/.bashrc
```

```
eval $(keychain --eval --quiet id_ed25519 id_rsa ~/.keys/my_custom_key)
```

> **注意：**之所以使用 `~/.bashrc` 而非上游建议的 `~/.bash_profile`，是因为在 Arch 上登录与非登录 shell 都会使用它，因此同样适用于文字与图形环境。关于二者之间差异的更多信息见[Bash#调用](../../07_开发与工具/命令行解释器/Bash.md#调用)。

上例当中，

- `--eval` 开关可以输出供 `eval` 命令执行的行；这样可以设置必要的环境变量，让 SSH 客户端能够找到您的 agent。
- `--quiet` 会把输出限制为只包含警告、错误及用户提示。

如例子所示，可在命令行中指定多个密钥。Keychain 默认会在 `~/.ssh/` 目录中寻找密钥对，但对于位于非标准位置的密钥可以使用绝对路径。也可使用 `--confhost` 选项告知 keychain 在 `~/.ssh/config` 中寻找为特定主机定义的 `IdentityFile` 设置，并使用这些路径来定位密钥。

关于如何为其他 shell 设置 *keychain* 的详情请参考 `keychain --help` 或 [keychain(1)](https://man.archlinux.org/man/keychain.1)。

要测试 Keychain，只需打开一个终端模拟器，或注销当前会话再重新登录。它应该会使用 `$SSH_ASKPASS` 中设置的程序或在终端上提示您输入指定私钥的密码短语（如果有）。

因为 Keychain 重新使用之前的登录中的 *ssh-agent* 进程，所以您再次登录或打开新终端时应该就无需输入密码短语了。每当您重启机器时，您才会被要求再次输入密码短语。

#### 提示

- *keychain* 需要公钥文件存放在相应私钥的同一目录下，并带有 `.pub` 扩展名。如私钥为符号链接，公钥需要与符号链接一起存放或与符号链接目标在同一目录下（该功能需要系统上有 `readlink` 命令）。

- 要禁用图形提示，以始终在终端中输入密码短语，请使用 `--nogui` 选项。举例来说，这样可以从密码管理器中复制粘贴长密码。

- 如果您想要等到需要时再提示解锁密钥，而不是一开始就提示，请使用 `--noask` 选项。

> **注意：**Keychain 能够以同样的方式管理 [GPG](../邮件/GnuPG.md) 密钥。它默认只会尝试启动 *ssh-agent*，但使用 `--agents` 选项可以改变这一行为，*例如* `--agents ssh,gpg`。详细信息请参考 [keychain(1)](https://man.archlinux.org/man/keychain.1)。

> **注意：**
>
> - Keychain 能够以同样的方式管理 [GPG](../邮件/GnuPG.md) 密钥。它默认只会尝试启动 *ssh-agent*，但使用 `--agents` 选项可以改变这一行为，*例如* `--agents ssh,gpg`。详细信息请参考 [keychain(1)](https://man.archlinux.org/man/keychain.1)。
> - 根据 [keychain issue 148](https://github.com/funtoo/keychain/issues/148)，在使用 Wayland 时可能需要添加 `--inherit any-once` 参数。

### x11-ssh-askpass

The [x11-ssh-askpass](https://archlinux.org/packages/?name=x11-ssh-askpass)包 package provides a graphical dialog for entering your passhrase when running an X session. *x11-ssh-askpass* depends only on the [libx11](https://archlinux.org/packages/?name=libx11)包 and [libxt](https://archlinux.org/packages/?name=libxt)包 libraries, and the appearance of *x11-ssh-askpass* is customizable. While it can be invoked by the *ssh-add* program, which will then load your decrypted keys into [ssh-agent](#ssh-agent), the following instructions will, instead, configure *x11-ssh-askpass* to be invoked by the aforementioned [Keychain](#Keychain) script.

Install the [keychain](https://archlinux.org/packages/?name=keychain)包 and [x11-ssh-askpass](https://archlinux.org/packages/?name=x11-ssh-askpass)包 packages.

Edit your `~/.xinitrc` file to include the following lines, replacing the name and location of your private key if necessary. Be sure to place these commands **before** the line which invokes your window manager.

```
~/.xinitrc
```

```
keychain ~/.ssh/id_ecdsa
[ -f ~/.keychain/$HOSTNAME-sh ] && . ~/.keychain/$HOSTNAME-sh 2>/dev/null
[ -f ~/.keychain/$HOSTNAME-sh-gpg ] && . ~/.keychain/$HOSTNAME-sh-gpg 2>/dev/null
...
exec openbox-session
```

In the above example, the first line invokes *keychain* and passes the name and location of your private key. If this is not the first time *keychain* was invoked, the following two lines load the contents of `$HOSTNAME-sh` and `$HOSTNAME-sh-gpg`, if they exist. These files store the environment variables of the previous instance of *keychain*.

#### Calling x11-ssh-askpass with ssh-add

The *ssh-add* manual page specifies that, in addition to needing the `DISPLAY` or `WAYLAND_DISPLAY` variable defined, you also need `SSH_ASKPASS` set to the name of your askpass program (in this case *x11-ssh-askpass*). It bears keeping in mind that the default Arch Linux installation places the *x11-ssh-askpass* binary in `/usr/lib/ssh/`, which will not be in most people's `PATH`. This is a little annoying, not only when declaring the `SSH_ASKPASS` variable, but also when theming. You have to specify the full path everywhere. Both inconveniences can be solved simultaneously by symlinking:

```
$ ln -sv /usr/lib/ssh/x11-ssh-askpass ~/bin/ssh-askpass
```

This is assuming that `~/bin` is in your `PATH`. So now in your `.xinitrc`, before calling your window manager, one just needs to export the `SSH_ASKPASS` environment variable:

```
$ export SSH_ASKPASS=ssh-askpass
```

and your [X resources](../../01_安装与包管理/配置文件/X%20资源.md) will contain something like:

```
ssh-askpass*background: #000000
```

Doing it this way works well with [the above method on using *ssh-agent* as a wrapper program](#ssh-agent_as_a_wrapper_program). You start X with `ssh-agent startx` and then add *ssh-add* to your window manager's list of start-up programs.

#### Theming

The appearance of the *x11-ssh-askpass* dialog can be customized by setting its associated [X resources](../../01_安装与包管理/配置文件/X%20资源.md). Some examples are the .ad files at [https://github.com/sigmavirus24/x11-ssh-askpass](https://github.com/sigmavirus24/x11-ssh-askpass). See [x11-ssh-askpass(1)](https://man.archlinux.org/man/x11-ssh-askpass.1) for full details.

#### Alternative passphrase dialogs

There are other passphrase dialog programs which can be used instead of *x11-ssh-askpass*. The following list provides some alternative solutions.

- [seahorse](https://archlinux.org/packages/?name=seahorse)包 (provides `/usr/lib/seahorse/ssh-askpass`) uses the [GTK](../../07_开发与工具/控件工具集/GTK.md) library. .
- [gnome-ssh-askpass3](https://aur.archlinux.org/packages/gnome-ssh-askpass3/)AUR uses the [GTK](../../07_开发与工具/控件工具集/GTK.md) library.
- [ksshaskpass](https://archlinux.org/packages/?name=ksshaskpass)包 uses the [KDE Wallet](../../05_桌面与图形/KDE/KDE%20Wallet.md).
- [openssh-askpass](https://aur.archlinux.org/packages/openssh-askpass/)AUR uses the [Qt5](../../07_开发与工具/控件工具集/Qt.md) library.
- [lxqt-openssh-askpass](https://archlinux.org/packages/?name=lxqt-openssh-askpass)包

### pam_ssh

[pam_ssh](http://pam-ssh.sourceforge.net/) 项目为 SSH 私钥提供了一个[可插拔认证模块](https://zh.wikipedia.org/wiki/%E5%8F%AF%E6%8F%92%E6%8B%94%E8%AE%A4%E8%AF%81%E6%A8%A1%E5%9D%97)（PAM），该模块可以为 SSH 连接提供类似单点登录验证的特性。在认证完成后，pam_ssh 模块会让 ssh-agent 在整个会话期间缓存您已解密的私钥。

要在 tty 模式下使用单点登录特性，需要安装非官方的 [pam_ssh](https://aur.archlinux.org/packages/pam_ssh/)AUR 包。

> **注意：**pam_ssh 2.0 要求所有用于认证的私钥文件必须保存在 `~/.ssh/login-keys.d/` 下。

可以为您的私钥文件创建一个软链接，并放到 `~/.ssh/login-keys.d/`。将以下案例中的 `id_rsa` 替换为你的私钥的名称：

```
$ mkdir ~/.ssh/login-keys.d/
$ cd ~/.ssh/login-keys.d/
$ ln -s ../id_rsa
```

编辑 `/etc/pam.d/login`，将下面例子中高亮加粗的那几行加进去。请注意配置内容的顺序会影响到登录行为，应当按照例子中的来。

> **警告：**PAM 配置错误会导致系统中的所有用户被锁定。因此，在进行任何更改前，你需要大致了解 PAM 配置的工作方式，并确保具有备用的 PAM 配置文件访问手段（如 Arch Live CD），以确保被锁定后仍能撤回更改。可以参阅 IBM developerWorks 的 [这篇文章](https://developer.ibm.com/tutorials/l-pam/) 详细了解一下 PAM 的配置细节。

```
/etc/pam.d/login
```

```
#%PAM-1.0

auth       required     pam_securetty.so
auth       requisite    pam_nologin.so
auth       include      system-local-login
auth       optional     pam_ssh.so        try_first_pass
account    include      system-local-login
session    include      system-local-login
session    optional     pam_ssh.so
```

在上面的例子中，登录认证在起始阶段正常进行，并要求用户输入登录密码。附加到认证栈末尾的额外 `auth` 认证规则会让 pam_ssh 尝试解密 `~/.ssh/login-keys.d` 目录下的所有私钥。`try_first_pass` 选项会被传递给 pam_ssh 模块，要求它尝试使用用户之前输入的密码解密 SSH 私钥。如果用户密码与私钥密码一致，用户就无需再次输入相同的密码。如果两者不同，pam_ssh 模块会在用户输入登录密码后要求输入私钥密码。`optional` 选项可以保证在没有私钥时用户也能正常登录系统。这样，没有 SSH 私钥的用户对 pam_ssh 是无感的。

如果你使用 X11 显示管理器（如 [SLiM](../../05_桌面与图形/显示管理器/SLiM.md) 或 [XDM](../../05_桌面与图形/显示管理器/XDM.md)）等其它方式进行登录，必须仿照如上配置编辑 PAM 配置文件。提供 PAM 支持的软件包通常会将默认配置文件放置在 `/etc/pam.d/` 目录下。

Further details on how to use pam_ssh and a list of its options can be found in the [pam_ssh(8)](https://linux.die.net/man/8/pam_ssh) man page.

#### 使用不同密码解锁 SSH 密钥

If you want to unlock the SSH keys or not depending on whether you use your key's passphrase or the (different!) login password, you can modify `/etc/pam.d/system-auth` to

```
/etc/pam.d/system-auth
```

```
#%PAM-1.0

auth      [success=1 new_authtok_reqd=1 ignore=ignore default=ignore]  pam_unix.so     try_first_pass nullok
auth      required  pam_ssh.so      use_first_pass
auth      optional  pam_permit.so
auth      required  pam_env.so

account   required  pam_unix.so
account   optional  pam_permit.so
account   required  pam_time.so

password  required  pam_unix.so     try_first_pass nullok sha512 shadow
password  optional  pam_permit.so

session   required  pam_limits.so
session   required  pam_unix.so
session   optional  pam_permit.so
session   optional  pam_ssh.so
```

For an explanation, see .

#### pam_ssh 已知问题

pam_ssh 项目并不活跃，其提供的文档也比较少。你需要注意该软件未提及的一些使用限制，比如：

- 2.0 版本前的 pam_ssh 不支持 ECDSA（椭圆曲线）SSH 密钥，需要使用旧版本的必须使用 RSA 密钥。

- pam_ssh 调用的 `ssh-agent` 进程仅限于当前用户登录，无法在多次登录间持久化。如果你在登录期间保留了一个 [GNU Screen](../../06_软件与应用/GNU/GNU%20Screen.md) 会话，会发现重新连接 screen 会话后无法与 ssh-agent 进行通信。这是因为 GNU Screen 环境和其子进程仍在引用调用 screen 时存在的 ssh-agent，但其已随上次登出被关闭。上文提到的 [Keychain](#Keychain) 前端可以在多次登录间保留 ssh-agent 进程，从而避免该问题。

### pam_exec-ssh

As an alternative to [pam_ssh](#pam_ssh) you can use [pam_exec-ssh-git](https://aur.archlinux.org/packages/pam_exec-ssh-git/)AUR. It is a shell script that uses pam_exec. Help for configuration can be found [upstream](https://github.com/x70b1/pam_exec-ssh).

### GNOME Keyring

[GNOME Keyring](../../05_桌面与图形/GNOME/GNOME_Keyring.md) 可用作 ssh-agent 的封装，并提供图形界面和/或密钥自动解锁功能。详情请参考 [GNOME Keyring#SSH密钥](../../05_桌面与图形/GNOME/GNOME_Keyring.md#SSH密钥)。

### 使用 Kwallet 存储 SSH 密钥

For instructions on how to use kwallet to store your SSH keys, see [KDE Wallet#Using the KDE Wallet to store ssh key passphrases](../../05_桌面与图形/KDE/KDE%20Wallet.md#Using_the_KDE_Wallet_to_store_ssh_key_passphrases).

### KeePass2 搭配 KeeAgent 插件

[KeeAgent](https://lechnology.com/software/keeagent/) is a plugin for KeePass that allows SSH keys stored in a KeePass database to be used for SSH authentication by other programs.

- Supports both PuTTY and OpenSSH private key formats.
- Works with native SSH agent on Linux/Mac and with PuTTY on Windows.

See KeePass#Plugin installation in KeePass or install the [keepass-plugin-keeagent](https://archlinux.org/packages/?name=keepass-plugin-keeagent)包 package.

This agent can be used directly, by matching KeeAgent socket: `KeePass -> Tools -> Options -> KeeAgent -> Agent mode socket file -> %XDG_RUNTIME_DIR%/keeagent.socket`\- and environment variable: `export SSH_AUTH_SOCK="$XDG_RUNTIME_DIR"'/keeagent.socket'`.

### KeePassXC

The KeePassXC fork of KeePass [can act as a client for an existing SSH agent](https://keepassxc.org/docs/#faq-ssh-agent-how). SSH keys stored in its database can be automatically (or manually) added to the agent. It is also compatible with KeeAgent's database format.

## 疑难排解

### 密钥被服务器忽略

如果您的 SSH 服务器忽略了您的 SSH 密钥对，您需要检查一下相关文件的权限是否正确。

: 本地机器上：

```
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/key
```

: 服务器上：

```
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/authorized_keys
```

如果这样还不能解决您的问题，您可以试试将 `/etc/ssh/sshd_config` 中的 `StrictModes` 设为 `no`。如果使用 `StrictModes off` 就能够顺利认证的话，说明相关文件的权限还没有改对。

- 确保 `~/.ssh/authorized_keys` 中的密钥正确并只有一行。
- 确保远程设备支持使用的密钥类型：有些服务器不支持 ECDSA 密钥，可以试试 RSA 密钥，具体请参考 [#生成密钥对](#生成密钥对)。
- 你可能会想在连接时启用调试模式并监控输出：

```
# /usr/bin/sshd -d
```

- 如果你的密钥使用其它名字（如 `id_rsa_server`），需要使用 `-i` 选项进行连接：

```
$ ssh -i id_rsa_server user@server
```

### 代理拒绝操作

If your private key requires a password (or, for instance, you have a hardware key with a PIN) but ssh-agent is not provided with one, `ssh` will fail:

```
sign_and_send_pubkey: signing failed for ECDSA-SK user@host from agent: agent refused operation
```

One potential cause for this is ssh-agent being unable to prompt for a password. Ensure that ssh-agent has access to either a display server (via the `DISPLAY` environment variable) or a TTY.

Another cause, if using a hardware authenticator, could be the key malfunctioning or being unplugged.

There is currently an open [bug](https://bugzilla.mindrot.org/show_bug.cgi?id=3572) that triggers with the "agent refused operation" error when using authenticator keys like ED25519-sk and ECDSA-SK that were created with the option `-O verify-required`. To avoid this issue, use the `-o IdentityAgent=none -o IdentitiesOnly=yes` option for the `ssh` command or add it to your `ssh_config` file for the relevant hosts:

```
Host myserver.tld
    IdentityAgent none
    IdentitiesOnly yes
```

## 参考

- OpenSSH 密钥管理：[第一部分](https://www.funtoo.org/OpenSSH_Key_Management,_Part_1)，[第二部分](https://www.funtoo.org/OpenSSH_Key_Management,_Part_2)，[第三部分](https://www.funtoo.org/OpenSSH_Key_Management,_Part_3)
- [Secure Secure Shell](https://stribika.github.io/2015/01/04/secure-secure-shell.html)

## 8.6　OpenSSH

*难度：高级 ｜ 篇幅：66.4 KB ｜ 所属：SSH*

将要讲到：安装、客户端用法、服务端用法、小技巧、排障、参见

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>

[OpenSSH](https://zh.wikipedia.org/wiki/OpenSSH)（OpenBSD Secure Shell）是使用[安全外壳协议](安全外壳协议.md)（SSH），在计算机网络上提供加密通信会话的软件集。它是 SSH Communications Security 专有 SSH 软件的开源替代方案。OpenSSH 是 OpenBSD 项目的一部分，该项目由 Theo de Raadt 领导。

OpenSSH 有时候会与名字相似的 OpenSSL 相混淆；然而，它们的目的不同，开发团队也不同，名字相似只因为两者目标相似。

## 安装

安装 [openssh](https://archlinux.org/packages/?name=openssh)包。

## 客户端用法

连接服务器：

```
$ ssh -p 端口 用户名@服务器地址
```

如果服务器只允许公钥认证，参考 [SSH 密钥](SSH%20密钥.md)。

### 配置


客户端可以配置一些通用的参数和主机信息。所有的参数都可以全局配置或者限制为特定主机，例如：

```
~/.ssh/config
```

```
# 全局选项
User 用户名

# 特定于主机的选项
Host myserver
    Hostname 服务器地址
    Port     端口
```

配置之后，下面的两条命令是等价的：

```
$ ssh -p 端口 用户名@服务器地址
$ ssh myserver
```

参考 [ssh_config(5)](https://man.archlinux.org/man/ssh_config.5) 可以获得更多的信息。

一些参数没有对应的命令行选项，但你可以在命令行中使用 `-o` 指定配置选项。例如 `-oKexAlgorithms=+diffie-hellman-group1-sha1`。

## 服务端用法


`sshd` 是 OpenSSH 服务器守护程序，通过 `/etc/ssh/sshd_config` 配置并由 `sshd.service` 管理。每次更改配置后，请在重新启动服务前在测试模式下运行 `sshd` 以确保它能够干净地启动。有效配置将不会产生输出：

```
# sshd -t
```

### 配置

要仅允许某些用户访问，请添加以下一行：

```
AllowUsers    用户1 用户2
```

要仅允许某些用户组访问：

```
AllowGroups   用户组1 用户组2
```

要添加漂亮的欢迎消息（例如，输出 `/etc/issue` 文件），请配置 `Banner` 选项：

```
Banner /etc/issue
```

*sshdgenkeys* [服务](#管理守护程序)会自动在 `/etc/ssh` 中生成缺失的任何算法的公钥和私钥，即使 `sshd_config` 中的 `HostKeyAlgorithms` 选项只允许部分加密算法。OpenSSH 提供了三种基于 [ed25519、ecdsa 和 rsa](SSH%20密钥.md#选择认证密钥种类) 算法的密钥对。要让 sshd 使用特定的密钥，请指定以下选项：

```
HostKey /etc/ssh/ssh_host_ed25519_key
```

如果服务器要暴露在公网下，建议将默认端口从 22 更改为更高的随机端口，例如：

```
Port 39901
```

> **提示：**
>
> - 要选择尚未分配给常见服务的备用端口，请查看 [TCP 和 UDP 端口号列表](https://zh.wikipedia.org/wiki/TCP/UDP%E7%AB%AF%E5%8F%A3%E5%88%97%E8%A1%A8)，还可以在本地的 `/etc/services` 中查找端口信息。更改 OpenSSH 所使用的端口有助于减少由攻击者使用自动程序尝试通过 SSH 登录你的服务器的情况（但无法彻底解决）。查看 Port knocking 了解相关内容。
> - 建议完全禁用密码登录。这将大大提高安全性，更多信息请参见[#强制公钥认证](#强制公钥认证)。更多推荐的安全方法，请参阅[#保护](#保护)。
> - OpenSSH 可以监听多个端口，只需在配置文件中添加多行 `Port *端口号*`。
> - 可以通过从 `/etc/ssh` 中删除要替换的主机密钥对并以 root 身份运行 `ssh-keygen -A` 来生成新的（或缺失的）主机密钥对。

### 管理守护程序

启动/启用 `sshd.service`。它将保持 SSH 守护程序始终处于活动状态，并为每个传入连接 fork。

> **注意：**因易受到拒绝服务攻击的影响，[openssh](https://archlinux.org/packages/?name=openssh)包 8.0p1-3删除了 `sshd.socket`，更多信息请参考 [FS#62248](https://bugs.archlinux.org/task/62248)。如果在更新到 [openssh](https://archlinux.org/packages/?name=openssh)包 8.0p1-3 时启用了 `sshd.socket`，则 `sshd.socket` 和 `sshd@.service` 单元将被复制到 `/etc/systemd/system/` 并重新启用。这样做只是为了不破坏现有设置；仍然建议用户迁移到 `sshd.service`。

> **警告：**如果你继续使用 `sshd.socket`, 请注意下列问题:
>
> - `sshd.socket` 单元可能会失败 (例如：在内存耗尽的情况下) 并且 `Restart=always` 不能被用于 socket 单元. 点击 [systemd issue 11553](https://github.com/systemd/systemd/issues/11553) 了解更多内容。
> - 使用套接字激活会导致拒绝服务，因为太多的连接会导致拒绝进一步激活服务。见 [FS#62248](https://bugs.archlinux.org/task/62248)。

> **注意：** `sshd.socket` 使得 `ListenAddress` 设置失效，因此它会允许连接任意地址。为了使 `ListenAddress` 设置起作用，你必须编辑 `sshd.socket` 来为 `ListenStream` 指定端口*和*IP（例如 `ListenStream=192.168.1.100:22`）。你还必须在 `[Socket]` 添加 `FreeBind=true`，否则设置 IP 地址会有 `ListenAddress` 设置一样的缺点：如果网络没有及时启动，套接字会启动失败。

> **提示：**当使用套接字激活时，每个连接都会启动一个 `sshd@.service` 的瞬态实例（实例名称会不同）。因此，不论是 `sshd.socket` 还是守护进程常规的 `sshd.service` 都会不被允许在日志中监控连接尝试。可以通过 root 运行 `journalctl -u "sshd@*"` 或者 `journalctl /usr/bin/sshd` 查看 SSH 套接字激活实例的日志。

### 保护

允许通过 SSH 远程登录有利于管理，但可能会威胁服务器的安全。由于 SSH 访问通常是暴力攻击的目标，因此需要适当限制 SSH 访问以防止第三方访问服务器。

[ssh-audit](https://archlinux.org/packages/?name=ssh-audit)包 提供对服务端和客户端配置的自动分析。关于这个主题，还有其他几个很好的指南和工具，例如：

- [Mozilla Infosec 团队的文章](https://wiki.mozilla.org/Security/Guidelines/OpenSSH)
- [SSH Hardening Guides](https://www.ssh-audit.com/hardening_guides.html)

#### 强制公钥认证

如果客户端无法通过公钥进行身份验证，SSH 服务端默认会退回到使用密码进行身份验证，从而允许恶意用户尝试通过[暴力穷举](#防止暴力穷举攻击)密码来获取访问权限。防止这种攻击的最有效方法之一是完全禁用密码登录，并强制使用 [SSH 密钥](SSH%20密钥.md)。这可以通过在守护程序配置文件中设置以下选项来完成：

```
/etc/ssh/sshd_config.d/20-force_publickey_auth.conf
```

```
PasswordAuthentication no
AuthenticationMethods publickey
```

> **警告：**在将此添加到配置之前，请确保所有需要 SSH 访问的帐户都在相应的 `authorized_keys` 文件中设置了公钥身份验证。详细信息请参阅 [SSH 密钥#将公钥复制到远程服务器上](SSH%20密钥.md#将公钥复制到远程服务器上)。

#### 双重身份认证和公钥

SSH 可以被设置成需要多重验证。你可以通过 `AuthenticationMethods` 选项指定使用哪些验证方式，这将同时启用公钥和双重验证。

##### 身份验证提供程序

设置 Google 身份验证器的方法请查看 [Google 身份验证器](../../06_软件与应用/Google/Google%20身份验证器.md)。

对于 [Duo](https://duo.com/)，安装提供 `pam_duo.so` 模块的包 [duo_unix](https://aur.archlinux.org/packages/duo_unix/)AUR 。阅读 [Duo Unix 文档](https://duo.com/docs/duounix)以了解如何设置 Duo 凭据（Integration Key，密钥和 API 主机名）。

##### PAM 设置

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** The distribution defaults to `KbdInteractiveAuthentication no` since . Later, the lexical order of the defaults was adjusted due to [FS#79285](https://bugs.archlinux.org/task/79285) to allow for higher priority user snippets, which would match below `20-pam.conf`. However, a [BBS thread](https://bbs.archlinux.org/viewtopic.php?id=300451) appears to be resolved by ordering custom after `99-archlinux.conf` defaults only.（在 [Talk:OpenSSH](https://wiki.archlinuxcn.org/wiki/Talk:OpenSSH) 中讨论）

要在 OpenSSH 中使用 [PAM](../../02_系统管理与内核/内核/PAM.md)，请编辑下列文件：

```
/etc/ssh/sshd_config.d/20-pam.conf
```

```
KbdInteractiveAuthentication yes
AuthenticationMethods publickey keyboard-interactive:pam
```

然后你可以通过公钥**或**在 PAM 中设置的用户身份验证方法登录。

另一方面，如果您想要根据 PAM 设置的要求使用公钥**和**用户身份验证对用户进行验证，请使用逗号而不是空格来分隔 AuthenticationMethods：

```
/etc/ssh/sshd_config.d/20-pam.conf
```

```
KbdInteractiveAuthentication yes
AuthenticationMethods publickey,keyboard-interactive:pam
```

为了要求同时使用公钥和 pam 身份验证，您可能希望禁用密码登录：

```
/etc/pam.d/sshd
```

```
auth      required  pam_securetty.so     #disable remote root
#Require google authenticator
auth      required  pam_google_authenticator.so
#But not password
#auth      include   system-remote-login
account   include   system-remote-login
password  include   system-remote-login
session   include   system-remote-login
```

#### 防止暴力穷举攻击

暴力攻击的概念非常简单：使用大量随机用户名和密码组合尝试登录网页或类似 SSH 等服务。

请参考 [ufw#连接速率限制](../防火墙/Uncomplicated%20Firewall.md#连接速率限制)，如果使用的是 [iptables](../防火墙/Iptables.md)，请参考 [Simple stateful firewall#Bruteforce attacks](../防火墙/Simple%20stateful%20firewall.md#Bruteforce_attacks)。

从 9.8 版本开始，OpenSSH 引入了类似 [fail2ban](../防火墙/Fail2ban.md) 的基本保护实现：`PerSourcePenalties` 选项默认配置了一个合理值，会对同一地址上的客户端针对各种条件进行惩罚，导致在一段时间内被拒绝连接。

另外，你也可以使用自动化脚本封禁任何尝试暴力穷举登录的对象。

- 只允许可信源地址进行 SSH 连接
- 使用 [fail2ban](../防火墙/Fail2ban.md) 或 [sshguard](../防火墙/Sshguard.md) 自动封禁多次密码登录失败的 IP
- 使用 [pam_shield](https://github.com/h0tw1r3/pam_shield) 封禁一段时间内多次尝试登录的 IP。与 [fail2ban](../防火墙/Fail2ban.md) 或 [sshguard](../防火墙/Sshguard.md) 不同，该程序不会对成功和失败登录请求进行区分。

### 限制root用户登录


It is generally considered bad practice to allow the root user to log in without restraint over SSH. There are two methods by which SSH root access can be restricted for increased security.

##### 拒绝

Sudo selectively provides root rights for actions requiring these without requiring authenticating against the root account. This allows locking the root account against access via SSH and potentially functions as a security measure against brute force attacks, since now an attacker must guess the account name in addition to the password.

SSH can be configured to deny remote logins with the root user by editing the "Authentication" section in the daemon configuration file. Simply set `PermitRootLogin` to `no`:

```
/etc/ssh/sshd_config.d/20-deny_root.conf
```

```
PermitRootLogin no
```

Next, restart the SSH daemon.

You will now be unable to log in through SSH under root, but will still be able to log in with your normal user and use [su](../../08_安全与加密/安全/Su.md) or [sudo](../../08_安全与加密/安全/Sudo.md) to do system administration.

##### 限制

Some automated tasks such as remote, full-system backup require full root access. To allow these in a secure way, instead of disabling root login via SSH, it is possible to only allow root logins for selected commands. This can be achieved by editing `~root/.ssh/authorized_keys`, by prefixing the desired key, e.g. as follows:

```
command="rrsync -ro /" ssh-ed25519 ...
```

This will allow any login with this specific key only to execute the command specified between the quotes.

The increased attack surface created by exposing the root user name at login can be compensated by adding the following to `sshd_config`:

```
PermitRootLogin forced-commands-only
```

This setting will not only restrict the commands which root may execute via SSH, but it will also disable the use of passwords, forcing use of public key authentication for the root account.

A slightly less restrictive alternative will allow any command for root, but makes brute force attacks infeasible by enforcing public key authentication. For this option, set:

```
PermitRootLogin prohibit-password
```

#### 对 authorized_keys 文件加锁

> **警告：**Locking this file only protects against user mistakes and a particular naive in-person attack. It **does not** provide any protection against malicious programs or breaches. Use multi-factor authentication, firewalling and practice defence in depth to prevent breaches in the first place.

If, for whatever reason, you think that the user in question should not be able to add or change existing keys, you can prevent them from manipulating the file.

On the server, make the `authorized_keys` file read-only for the user and deny all other permissions:

```
$ chmod 400 ~/.ssh/authorized_keys
```

To prevent the user from simply changing the permissions back, [set the immutable bit](../../07_开发与工具/命令行/文件权限与属性.md#File_attributes) on the `authorized_keys` file. To prevent the user from renaming the `~/.ssh` directory and creating a new `~/.ssh` directory and `authorized_keys` file, set the immutable bit on the `~/.ssh` directory too. To add or remove keys, you will have to remove the immutable bit from `authorized_keys` and make it writable temporarily.

> **提示：**It is recommended to log changes to any `authorized_keys` file via e.g auditd.

#### SSH certificates

While common SSH keys and manual fingerprint verification may be easy to use with a handful of hosts that are managed by a single administrator, this method of authentication does not scale at all. When a number of servers need to be accessed through SSH by several users, manually verifying ssh public key fingerprints of every host becomes nearly impossible to do securely and reliably.

The solution for this is to use SSH certificates that provide automatic verification of public key identities through a chain of trust that scales significantly better than the default trust-on-first-use approach of SSH. SSH certificates are basically nothing else than normal public SSH keys, but with an additional signature from a trusted certificate authority that verifies the key identity.

##### Create a host certificate authority key for your infrastructure

```
$ ssh-keygen -t ed25519 -f ~/.ssh/ca_host_key -C 'Host certificate authority for *.example.com'
```

The private certificate authority key should be stored securely, ideally on a smartcard or hardware token that prevents key extraction like the Nitrokey or [YubiKey](../../08_安全与加密/Smartcards/YubiKey.md).

##### Sign a server's public SSH host key

Copy the public server key to your local system containing the private certificate authority key to sign it:

```
$ ssh-keygen -h -s ~/.ssh/ca_key -I certLabel -n server01.example.com ./ssh_host_ed25519_key.pub
```

##### Move the new certificate and configure sshd to use it

The generated certificate `ssh_host_ed25519_key-cert.pub` should be copied to the server at `/etc/ssh/`.

```
/etc/ssh/sshd_config.d/20-ed25519_key.conf
```

```
HostCertificate /etc/ssh/ssh_host_ed25519_key-cert.pub
```

##### Configure all clients to trust the certificate authority

```
~/.ssh/known_hosts
```

```
@cert-authority  *.example.com ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKL8gB/pjuff005YNazwMCqJpgsXAbQ3r4VStd/CRKwU Host certificate authority for *.example.com
```

> **警告：** When a server does not provide a certificate for identification, public key authentication is used as fallback by default.

##### SSH user certificates

Depending on the number of users and method of deployment, SSH User keys can also be used with Certificates. For organizations with many ssh users, this is strongly advised to manage User key deployment securely.

The deployment of user certificates works basically the same as for server identities. More details and instructions can be found at [Wikibooks:OpenSSH/Cookbook/Certificate-based Authentication](https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Certificate-based_Authentication).

##### Certificate deployment automation

Automated deployment of SSH certificates can be provided by a number of open source tools. Popular examples are:

- [Ansible - openssh_cert module](https://docs.ansible.com/ansible/latest/collections/community/crypto/openssh_cert_module.html)
- [privacyIDEA - authentication server](https://www.privacyidea.org/)
- [Theo App - authorized keys manager](https://theoapp.readthedocs.io/en/latest/)

#### SSHFP record

The [Secure Shell fingerprint record (SSHFP)](https://en.wikipedia.org/wiki/SSHFP_record) is an optional resource record in the domain name system that associates SSH keys to a host name. It can be used to verify the SSH fingerprint on public servers by using DNSSEC instead of deploying trusted CA certificates, which allows even unmanaged clients to verify the validity of key fingerprints.

##### Generate record entry

To generate the required hexadecimal key fingerprint to be stored in the DNS record, create the hash on the target server.

```
$ ssh-keygen -r host.example.com
```

This will read all available SSH keys for the specified domain and output valid SSHFP records that can then be stored in the DNS entries of the affected domain.

##### Client configuration

In order to automatically retrive and trust SSH key fingerprints stored as SSHFP records, add the following to your ssh client configuration file:

```
~/.ssh/config
```

```
# global options
Match all
    VerifyHostKeyDNS yes
```

If the target host has a valid SSHFP record and this record is verified with a valid DNSSEC signature, the fingerprint is automatically accepted without prompting the user to verify the hosts identity. In case the DNS record is not verified by DNSSEC, the user will be prompted to verify the fingerprint instead.

##### Generate SSHFP records

To determine the SSH fingerprint of a specific domain, use *ssh-keyscan* to retrieve the ssh fingerprints in a valid DNS record format. (Note that by default fingerprints for every available key type is provided as both SHA1 and SHA256.)

```
$ ssh-keyscan -D github.com
```

```
; github.com:22 SSH-2.0-babeld-57ca1323
; github.com:22 SSH-2.0-babeld-57ca1323
github.com IN SSHFP 1 1 6f4c60375018bae0918e37d9162bc15ba40e6365
github.com IN SSHFP 1 2 b8d895ced92c0ac0e171cd2ef5ef01ba3417554a4a6480d331ccc2be3ded0f6b
; github.com:22 SSH-2.0-babeld-57ca1323
github.com IN SSHFP 3 1 3358ab5dd3e306c461c840f7487e93b697e30600
github.com IN SSHFP 3 2 a764003173480b54c96167883adb6b55cf7cfd1d415055aedff2e2c8a8147d03
; github.com:22 SSH-2.0-babeld-57ca1323
github.com IN SSHFP 4 1 e9619e2ed56c2f2a71729db80bacc2ce9ccce8d4
github.com IN SSHFP 4 2 f83898df0bef57a4ee24985ba598ac17fccb0c0d333cc4af1dd92be14bc23aa5
; github.com:22 SSH-2.0-babeld-57ca1323
```

Since the SSHFP record stores the key fingerprints as hexadecimal values while the common output for SSH fingerprints is the base64 encoded SHA256 hash of the public key, it is necessary to convert the record back to the base64 format in order to compare it with values in the known_hosts file or other documentation that commonly stores fingerprints as SHA256.

```
$ echo "SSHFP-fingerprint" | xxd -r -p | base64
```

Example for github.com using the hex value for the sha256 fingerprint of the key type ed25519

```
$ echo "f83898df0bef57a4ee24985ba598ac17fccb0c0d333cc4af1dd92be14bc23aa5" | xxd -r -p | base64
```

```
+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU=
```

Compare with known_hosts entries:

```
$ ssh-keygen -l -f ~/.ssh/known_hosts
```

##### Manually retrieve SSHFP records from DNS

```
$ dig SSHFP targetdomain.tld +short
```

## 小技巧

### 使用加密的 SOCKS 隧道

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**Written like a blog post.（在[Talk:OpenSSH](https://wiki.archlinuxcn.org/wiki/Talk:OpenSSH)讨论）

这对连接到各种不安全无线连接的笔记本电脑用户非常有用。你唯一需要的是在一个比较安全的位置运行的 SSH 服务器，比如你的家或工作地点。使用动态 DNS 服务可能很有用，如[DynDNS](https://dyn.com/dns/)，这样你就不必记住你的 IP 地址。

#### 步骤 1：开始连接

你只需要执行这一条命令就可以开始连接：

```
$ ssh -TND 4711 user@host
```

其中 `*user*` 是你在 `*host*` 上运行的 SSH 服务器的用户名。它将询问你的密码，然后建立连接。`N` 标志禁用了交互式提示，`D` 标志指定了监听的本地端口（如果你愿意，可以选择任何端口号）。{Ic|T}} 标志禁用伪终端分配。

可以添加 verbose（`-v`）标志，这样就可以通过输出验证是否成功连接。

#### 步骤 2 (方法 A)：配置你的浏览器（或其它程序）

上述步骤仅与使用新创建的 SOCKS 隧道的网页浏览器或其他程序结合使用时有用。SSH 现在同时支持 SOCKS v4 和 SOCKS v5，你可以选择它们中的任意一种。

- 对于火狐浏览器：进入*首选项 > 通用*，跳到页面底部并点击网络设置项右边的*设置...*按钮。接下来，在打开的二级窗口中，选中*手动代理配置*并在 SOCKS 主机名框内输入 `localhost`，并在旁边的*端口*项输入端口号（上述例子使用 `4711`）。

: 火狐不会自动通过 SOCKS 隧道发出 DNS 请求，该潜在隐私问题可通过选中页面下方的*使用 SOCKS v5 时代理 DNS 查询*解决。显然，这只在使用 SOCKS v5 而不是 v4 时有效。
: 重启火狐来应用设置。

- 对于 Chromium，可以将 SOCKS 设置为环境变量或通过命令行选项进行配置。例如，可以在 `.bashrc` 中添加以下函数：

```
function secure_chromium {
    port=4711
    export SOCKS_SERVER=localhost:$port
    export SOCKS_VERSION=5
    chromium &
    exit
}
```

或者是

```
function secure_chromium {
    port=4711
    chromium --proxy-server="socks://localhost:$port" &
    exit
}
```

现在打开终端，然后执行：

```
$ secure_chromium
```

就可以使用安全隧道了！

#### 步骤 2（方法 B）：配置本地 TUN 接口

这种方法在前期稍微复杂，但以后无需手动配置每个应用程序来使用 SOCKS 代理，具体原理是建立一个本地 TUN 接口并通过该接口路由流量。

具体方法请参考 VPN over SSH#Set up badvpn and tunnel interface。

### X11 转发

使用 X11 转发可以在本地客户端上显示远程系统运行的 X11 程序图形界面。对于 X11 转发，远程主机不需要安装完整的 X11 环境，但至少要安装 *xauth*。*xauth*是维护 `Xauthority` 配置的工具，该配置被服务端和客户端用于认证 X11 会话（[源](http://xmodulo.com/2012/11/how-to-enable-x11-forwarding-using-ssh.html)）。

> **警告：**X11 转发在安全方面有很强的影响，需至少要阅读 [ssh(1)](https://man.archlinux.org/man/ssh.1)、[sshd_config(5)](https://man.archlinux.org/man/sshd_config.5) 和 [ssh_config(5)](https://man.archlinux.org/man/ssh_config.5) 手册来了解相关信息。另外也请参考[该 StackExchange 提问](https://security.stackexchange.com/questions/14815/security-concerns-with-x11-forwarding)。

#### 设置

##### 服务端

- 安装 [xorg-xauth](https://archlinux.org/packages/?name=xorg-xauth)包
- 在 `/etc/ssh/ssh**d**_config` 中：
    - 设置 `X11Forwarding` 为 *yes*
    - 检查 `AllowTcpForwarding` 和 `X11UseLocalhost` 选项是否已设为 *yes*，并且 `X11DisplayOffset` 设为 *10*（未修改情况下是默认值，参考 [sshd_config(5)](https://man.archlinux.org/man/sshd_config.5)）
- 然后重启 [*sshd* daemon](#守护进程管理).

##### 客户端

- 安装 [xorg-xauth](https://archlinux.org/packages/?name=xorg-xauth)包
- 启用 `ForwardX11` 选项，可以在命令行中使用 `-X` 选项，也可以在[客户端配置文件](#配置)中设置 `ForwardX11` 为 *yes*。

> **提示：**如果 GUI 显示质量不佳或者出现错误，可以启用 `ForwardX11Trusted` 选项（命令行下使用 `-Y`），这会防止 X11 转发受到 [X11 SECURITY extension](https://www.x.org/wiki/Development/Documentation/Security/) 的影响。在操作前，确保你已经看过[警告信息](#X11_转发)。

#### 用法

正常登录到远程机器，如果客户端的配置文件中没有启用 *ForwardX11*，则指定 `-X` 开关：

```
$ ssh -X user@host
```

如果你在试图运行图形应用程序时收到错误，请尝试用 *ForwardX11Trusted* 代替：

```
$ ssh -Y user@host
```

如果出现 `X11 forwarding request failed` 报错，则需要为你的远程机器重新进行设置。一旦 X11 转发请求成功，你可以在远程服务器上启动任何 X 程序，它将被转发到你的本地会话：

```
$ xclock
```

如果出现了 `Can't open display` 报错，表示 `DISPLAY` 设置有误。

对一些应用程序要小心，因为它们会检查本地机器上是否有正在运行的实例。[Firefox](../../06_软件与应用/Mozilla/Firefox.md) 就是一个例子：要么关闭正在运行的 Firefox 实例，要么使用下面的启动参数在本地机器上启动一个远程实例：

```
$ firefox --no-remote
```

如果你在连接时出现“X11 forwarding request failed on channel 0”报错（并且服务器 `/var/log/errors.log` 显示“Failed to allocate internet-domain X11 display socket”），请确保已安装 [xorg-xauth](https://archlinux.org/packages/?name=xorg-xauth)包。如果安装了也还是不行，可以尝试以下方法之一：

- 在*服务端*的 `ssh**d**_config` 中启用 `AddressFamily any` 选项，或者
- 在*服务端*的 `ssh**d**_config` 中将 `AddressFamily` 选项设置为 inet。

将其设置为 inet 可能会解决 Ubuntu 客户端在 IPv4 上的问题。

对于在 SSH 服务器上以另一个用户身份运行 X 应用程序，you need to `xauth add` the authentication line taken from `xauth list` of the SSH logged in user.

> **提示：**关于 `X11 转发` 排障可参考以下链接：
>
> - [https://unix.stackexchange.com/a/12772](https://unix.stackexchange.com/a/12772)
> - [https://unix.stackexchange.com/a/46748](https://unix.stackexchange.com/a/46748)
> - [https://superuser.com/a/805060](https://superuser.com/a/805060)

### 转发其他端口

除了SSH对X11的内置支持外，它还可以通过本地转发或远程转发使用安全隧道传输任何 TCP 连接。

本地转发打开本地计算机上的一个端口，连接将被转发到远程主机，并从那里转发到给定的目的地。通常，转发目的地将与远程主机相同，从而为同一机器提供安全外壳，例如安全 [VNC](../远程服务器/TigerVNC.md) 连接。本地转发会使用到 `-L` 选项，后接 `<tunnel port>:<destination address>:<destination port>` 格式的转发规则。

因此：

```
$ ssh -L 1000:mail.google.com:25 192.168.0.100
```

将使用 SSH 进行登录到 `192.168.0.100` 并打开 shell，同时创建本地 TCP 端口 1000 到 mail.google.com 端口 25 的隧道。连接成功后，发送到 `localhost:1000` 的连接会连到 Gmail 的 SMTP 端口。除非使用了其它手段，否则对于谷歌来说，他们会观察到所有这些连接都是从 `192.168.0.100` 发送出来的（但对于连接传送的数据又是另一回事），而不是 `192.168.0.100`。

类似地:

```
$ ssh -L 2000:192.168.0.100:6001 192.168.0.100
```

将允许连接到 `localhost:2000`，然后数据会被透明发送到远程主机的 6001 端口。The preceding example is useful for VNC connections using the vncserver utility--part of the tightvnc package--which, though very useful, is explicit about its lack of security.

Remote forwarding allows the remote host to connect to an arbitrary host via the SSH tunnel and the local machine, providing a functional reversal of local forwarding, and is useful for situations where, e.g., the remote host has limited connectivity due to firewalling. It is enabled with the `-R` switch and a forwarding specification in the form of `<tunnel port>:<destination address>:<destination port>`.

Thus:

```
$ ssh -R 3000:irc.libera.chat:6667 192.168.0.200
```

will bring up a shell on `192.168.0.200`, and connections from `192.168.0.200` to itself on port 3000 (the remote host's `localhost:3000`) will be sent over the tunnel to the local machine and then on to irc.libera.chat on port 6667, thus, in this example, allowing the use of IRC programs on the remote host to be used, even if port 6667 would normally be blocked to it.

Both local and remote forwarding can be used to provide a secure "gateway", allowing other computers to take advantage of an SSH tunnel, without actually running SSH or the SSH daemon by providing a bind-address for the start of the tunnel as part of the forwarding specification, e.g. `<tunnel address>:<tunnel port>:<destination address>:<destination port>`. The `<tunnel address>` can be any address on the machine at the start of the tunnel. The address `localhost` allows connections via the `localhost` or loopback interface, and an empty address or `*` allow connections via any interface. By default, forwarding is limited to connections from the machine at the "beginning" of the tunnel, i.e. the `<tunnel address>` is set to `localhost`. Local forwarding requires no additional configuration; however, remote forwarding is limited by the remote server's SSH daemon configuration. See the `GatewayPorts` option in [sshd_config(5)](https://man.archlinux.org/man/sshd_config.5) and `-L address` option in [ssh(1)](https://man.archlinux.org/man/ssh.1) for more information about remote forwarding and local forwarding, respectively.

### 跳板机

有时，可能无法直接连接到目标的 SSH 守护程序，因而需要使用一个跳转服务器（或[堡垒服务器](https://en.wikipedia.org/wiki/bastion_host)）。为此，我们试图将两个或更多的 SSH 隧道连接在一起，并假设你的本地密钥对链上的每个服务器都有授权。这可以通过 SSH 代理转发（`-A`）和使用伪终端分配（`-t`）转发你的本地密钥实现：

```
$ ssh -A -t -l user1 bastion1 \
  ssh -A -t -l user2 intermediate2 \
  ssh -A -t -l user3 target
```

这一流程可以使用 ProxyCommand 选项进行自动化：

```
$ ssh -o ProxyCommand="ssh -W %h:%p bastion.example.org" targetserver.example.org
```

一个更简单和更安全的方法是将 ProxyJump 选项结合 `-J` 使用：

```
$ ssh -J user1@bastion1,user2@intermediate2 user3@target
```

`-J` 指令中的多个主机可以用逗号隔开，它们将按照列出的顺序被连接。`user...@` 部分不是必须的，但可以使用。The host specifications for `-J` use the ssh configuration file, so specific per-host options can be set there, if needed.

The main difference between the ProxyCommand and ProxyJump options is that the later does not require a shell on the jumphost. Consequently, this also means that the jumpserver does not require access to the users login credentials or SSH agent forwarding. With the ProxyJump option, the ssh client connects through the jumpserver directly to the target server, establishing an end-to-end encrypted channel between client and target server.

`-J` 标志相当于配置文件中 `ProxyJump` 选项，相关细节请参考 [ssh_config(5)](https://man.archlinux.org/man/ssh_config.5)。

### Reverse SSH through a relay

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**The idea of SSH tunneling is classic, so some references for detailed explanation would be nice. E.g.  which includes other scenarios.（在[Talk:OpenSSH](https://wiki.archlinuxcn.org/wiki/Talk:OpenSSH)讨论）

The idea is that the client connects to the server via another relay while the server is connected to the same relay using a reverse SSH tunnel. This is useful when the server is behind a NAT, and the relay is a publicly accessible SSH server used as a proxy to which the user has access. Therefore, the prerequisite is that the client's keys are authorized against both the relay and the server, and the server needs to be authorized against the relay as well for the reverse SSH connection.

The following configuration example assumes that user1 is the user account used on client, user2 on relay and user3 on server. First, assuming we will use port 2222, the server needs to establish the reverse tunnel with:

```
ssh -R 2222:localhost:22 -N user2@relay
```

Which can also be automated with a startup script, systemd service, [autossh](#Autossh_-_automatically_restarts_SSH_sessions_and_tunnels) or [sidedoor](https://aur.archlinux.org/packages/sidedoor/)AUR.

At the client side, the connection is established with:

```
ssh -t user2@relay ssh user3@localhost -p 2222
```

> **注意：**`ssh *user3*@*relay* -p 2222` would require you to open this port in the firewall of your relay server, as well as to allow connections to this port from other addresses.

The remote command to establish the connection to reverse tunnel can also be defined in relay's `~/.ssh/authorized_keys` by including the `command` field as follows:

```
command="ssh user3@localhost -p 2222" ssh-ed25519 KEY2 user1@client
```

In this case the connection is established with:

```
ssh user2@relay
```

Alternatively, you can add an entry to your ssh configuration that specifies both `RemoteCommand` and `RequestTTY`:

```
~/.ssh/config
```

```
Host jump-destination
    Hostname relay
    User user2
    RemoteCommand ssh user3@localhost -p 2222
    RequestTTY yes
```

Which will reduce connecting to:

```
ssh jump-destination
```

> **注意：**SCP's autocomplete function in client's terminal will not work and even the SCP transfers themselves are not working under some configurations.

### 多路复用

SSH 守护进程通常监听 22 端口。然而，许多公共热点会屏蔽所有不在常规 HTTP/S 端口（80 和 443）的流量，从而阻止了 SSH 连接。最直接解决这个问题的办法是让 `sshd` 额外监听白名单中的一个端口。

```
/etc/ssh/sshd_config
```

```
Port 22
Port 443
```

然而，443 端口很可能已经被提供 HTTPS 内容的网页服务器使用，在这种情况下，可以使用类似 [sslh](https://archlinux.org/packages/?name=sslh)包 的多路复用器，它会监听多路复用端口，并智能地将数据包转发给多个服务。

### SSH 提速

有多个[客户端配置项](#配置)可以全局或为指定的主机提升连接性能。关于这些选项的完整描述请参考 [ssh_config(5)](https://man.archlinux.org/man/ssh_config.5)。

- *使用更快的加密算法*：在带有 AESNI 指令集的现代 CPU 上，使用 `aes128-gcm@openssh.com` 和 `aes256-gcm@openssh.com` 替代 openssh 的默认加密方法（通常是 `chacha20-poly1305@openssh.com`）应可以显著提升性能。在命令行中可以通过 `-c` 选项选择加密算法。如果要将配置持久化，可以在 `~/.ssh/config` 中添加 `Ciphers` 选项，然后按照偏好顺序填入加密算法，例如：

    ```
    Ciphers aes128-gcm@openssh.com,aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr
    ```

- *启用或禁用压缩*：在低速连接上启用压缩可以提升性能，该功能可通过 `Compression yes` 选项或 `-C` 标志进行启用。但是，该选项使用的压缩算法是 [gzip(1)](https://man.archlinux.org/man/gzip.1)，相对较慢，在高速网络上可能会成为瓶颈。为提高性能，在本地或高速网络上应使用 `Compression no` 选项。

- *共享连接*：可以使用以下选项为连到同一主机的所有会话共用一个连接：

```
ControlMaster auto
ControlPersist yes
ControlPath ~/.ssh/sockets/socket-%r@%h:%p
```

: 其中 `~/.ssh/sockets` 可以是任何其他用户不可写的目录。

- `ControlPersist` 指定了在客户端连接关闭后，需要在后台等待新客户端连接的时间。可选值包括：
    - `no`：最后一个客户端断开后立即关闭连接
    - 以秒指定的时间
    - `yes`：始终等待，连接永远不会被自动关闭

- 可以使用 `AddressFamily inet` 选项或 `-4` 标志绕过 IPv6 查找来缩短登录时间。

- 另外，如果有将 SSH 用于 SFTP 或 SCP 的需求，[高性能 SSH/SCP](https://www.psc.edu/index.php/hpn-ssh) 可以通过动态提升 SSH 缓存大小来显著提升传输速度。可以安装 [openssh-hpn](https://aur.archlinux.org/packages/openssh-hpn/)AUR 来使用带有该补丁的 OpenSSH。

### 使用 SSHFS 挂载远程文件系统

关于将 SSH 可访问的远程系统挂载到本地目录的内容请查看 [SSHFS](SSHFS.md) 文章。这样您就可以使用任何工具对挂载的文件执行任何操作（复制、重命名、使用 vim 编辑等）。*sshfs* 一般优于 *shfs*，后者自 2004 年以来一直没有更新。

### 保活

默认情况下，如果 SSH 会话空闲了一段时间，它将自动注销。为了保持会话，如果在一段时间内没有收到数据，客户端可以向服务器发送保持活动的信号，或者如果服务器没有收到客户端的消息，则可以对称地定期发送消息。

- 在**服务端**，`ClientAliveInterval` 以秒为单位设置超时，如果没有从客户端接收到数据，*sshd* 将发送响应请求。默认值为 0（不发送消息）。例如，要每 60 秒从客户端请求一次响应，请在[服务器配置](#配置_2)中设置 `ClientAliveInterval 60` 选项。另见 `ClientAliveCountMax` 和 `TCPKeepAlive` 选项。

- 在 '*客户端*，`ServerAliveInterval` 控制从客户端发送到服务器的响应请求之间的间隔。例如，要请求服务器每 120 秒响应一次，请将 `ServerAliveInterval 120` 选项添加到[客户端配置](#配置)中。另请参见 `ServerAliveCountMax` 和 `TCPKeepAlive` 选项。

> **注意：**为了确保会话保持活动状态，客户机或服务器中只有一个需要发送 keep alive 请求。如果同时控制服务器和客户机，一个合理的选择是只在需要持久会话的客户机上配置 `ServerAliveInterval`，并使其他客户机和服务器保持默认配置。

### Automatically restart SSH tunnels with systemd

[systemd](../../01_安装与包管理/初始化/Systemd.md) can automatically start SSH connections on boot/login *and* restart them when they fail. This makes it a useful tool for maintaining SSH tunnels.

The following service can start an SSH tunnel on login using the connection settings in your [ssh configuration](#Configuration). If the connection closes for any reason, it waits 10 seconds before restarting it:

```
~/.config/systemd/user/tunnel.service
```

```
[Unit]
Description=SSH tunnel to myserver

[Service]
Type=simple
Restart=always
RestartSec=10
ExecStart=/usr/bin/ssh -F %h/.ssh/config -N myserver
```

Then enable and start the [Systemd/User](../../02_系统管理与内核/系统管理/Systemd_用户.md) service. See [#Keep alive](#Keep_alive) for how to prevent the tunnel from timing out. If you wish to start the tunnel on boot, you might want to [rewrite the unit](../../01_安装与包管理/初始化/Systemd.md#Writing_unit_files) as a system service.

### Autossh - automatically restarts SSH sessions and tunnels

When a session or tunnel cannot be kept alive, for example due to bad network conditions causing client disconnections, you can use [autossh](https://archlinux.org/packages/?name=autossh)包 to automatically restart them.

Usage examples:

```
$ autossh -M 0 -o "ServerAliveInterval 45" -o "ServerAliveCountMax 2" username@example.com
```

Combined with [SSHFS](SSHFS.md):

```
$ sshfs -o reconnect,compression=yes,transform_symlinks,ServerAliveInterval=45,ServerAliveCountMax=2,ssh_command='autossh -M 0' username@example.com: /mnt/example
```

Connecting through a SOCKS-proxy set by [Proxy settings](../代理服务器/代理服务器.md):

```
$ autossh -M 0 -o "ServerAliveInterval 45" -o "ServerAliveCountMax 2" -NCD 8080 username@example.com
```

With the `-f` option autossh can be made to run as a background process. Running it this way however means the passphrase cannot be entered interactively.

The session will end once you type `exit` in the session, or the autossh process receives a SIGTERM, SIGINT of SIGKILL signal.

#### Run autossh automatically at boot via systemd

If you want to automatically start autossh, you can create a systemd unit file:

```
/etc/systemd/system/autossh.service
```

```
[Unit]
Description=AutoSSH service for port 2222
After=network.target

[Service]
Environment="AUTOSSH_GATETIME=0"
ExecStart=/usr/bin/autossh -M 0 -NL 2222:localhost:2222 -o TCPKeepAlive=yes foo@bar.com

[Install]
WantedBy=multi-user.target
```

Here `AUTOSSH_GATETIME=0` is an environment variable specifying how long ssh must be up before autossh considers it a successful connection, setting it to 0 autossh also ignores the first run failure of ssh. This may be useful when running autossh at boot. Other environment variables are available at [autossh(1)](https://man.archlinux.org/man/autossh.1). Of course, you can make this unit more complex if necessary (see the systemd documentation for details), and obviously you can use your own options for autossh, but note that the `-f` implying `AUTOSSH_GATETIME=0` does not work with systemd.

Remember to start and/or enable the service afterwards.

You may also need to disable ControlMaster e.g.

```
ExecStart=/usr/bin/autossh -M 0 -o ControlMaster=no -NL 2222:localhost:2222 -o TCPKeepAlive=yes foo@bar.com
```

> **提示：**It is also easy to maintain several autossh processes, to keep several tunnels alive. Just create multiple service files with different names.

### Alternative service should SSH daemon fail

For remote or headless servers which rely exclusively on SSH, a failure to start the SSH daemon (e.g., after a system upgrade) may prevent administration access. [systemd](../../01_安装与包管理/初始化/Systemd.md) offers a simple solution via `OnFailure` option.

Let us suppose the server runs `sshd` and [telnet](../网络/Telnet.md) is the fail-safe alternative of choice. Create a file as follows. Do **not** enable `telnet.socket`!

```
/etc/systemd/system/sshd.service.d/override.conf
```

```
[Unit]
OnFailure=telnet.socket
```

That's it. Telnet is not available when `sshd` is running. Should `sshd` fail to start, a telnet session can be opened for recovery.

### Terminal background color based on host

为了更好地区分在不同主机上的情况，可以设置[基于主机类型的不同背景颜色](https://bryangilbert.com/post/etc/term/dynamic-ssh-terminal-background-colors/)。

这种解决方案仅限于 ZSH。

### Network specific configuration

You can use host configuration specific to the network you are connected to using a `Match exec`.

For example, when using [nmcli(1)](https://man.archlinux.org/man/nmcli.1), and the connection is configured (manually or through DHCP) to use a search-domain:

```
Match exec "nmcli | grep domains: | grep example.com"
  CanonicalDomains example.com
  # Should you use a different username on this network
  #User username
  # Use a different known_hosts file (for private network or synchronisation)
  #UserKnownHostsFile <network>_known_hosts
```

Another example for `Match host ... exec "..."`: Consider that connecting to `internal.example.com` requires a bastion/proxy (via `ProxyJump`) unless you are already connected via VPN. The fragment `!exec "host internal.example.com"` applies only when `internal.example.com` cannot be looked up via DNS. Various alternatives are discussed at .

```
Match host internal.example.com !exec "host internal.example.com"
  ProxyJump bastion.example.com
Host internal.example.com
  User foobar
```

### Private networks hostkeys verification

Because different servers on different networks are likely to share a common private IP address, you might want to handle them differently.

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** The best solution would not need a warning to use something else in practice.（在 [Talk:OpenSSH](https://wiki.archlinuxcn.org/wiki/Talk:OpenSSH) 中讨论）

The best solution is to use the [#Network specific configuration](#Network_specific_configuration) to use a different `UserKnownHostsFile` depending on the network you are on. The second solution, best used as default when you are working on new/prototype networks, would be to simply ignore hostkeys for private networks:

```
Host 10.* 192.168.*.* 172.31.* 172.30.* 172.2?.* 172.1?.*
    # Disable HostKey verification
    # Trust HostKey automatically
    StrictHostKeyChecking no
    # Do not save the HostKey
    UserKnownHostsFile=/dev/null
    # Do not display: "Warning: Permanently Added ..."
    LogLevel Error
```

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** The `known_hosts` file records an IP address even when you use hostname to access the server.（在 [Talk:OpenSSH](https://wiki.archlinuxcn.org/wiki/Talk:OpenSSH) 中讨论）

> **警告：**In a production environment, make sure to either use the hostname to access the host and/or to use network specific known_hosts files.

### 在登录时运行命令

如果你使用的是交互式会话，有多种方法可以在登录时执行一个命令：

- 使用远程主机上的 `authorized_keys` 文件（参见 [sshd(8) § AUTHORIZED_KEYS FILE FORMAT](https://man.archlinux.org/man/sshd.8#AUTHORIZED_KEYS_FILE_FORMAT)）
- 如果服务器启用了 `~/.ssh/rc` 选项，则使用远程主机上的 `PermitUserRC` 选项
- 使用远程主机上的 shell 配置文件，例如 `.bashrc`。

### 转发代理

SSH 代理转发允许你在连接到一个服务器时使用你的本地密钥。[建议](https://security.stackexchange.com/questions/7480/risks-of-ssh-to-an-untrusted-host#7504)只对选定的主机启用代理转发。

```
~/.ssh/config
```

```
Host myserver.com
    ForwardAgent yes
```

接下来，配置 SSH 代理并使用 *ssh-add* 添加你的本地密钥。

如果你现在连接到一个远程服务器，你将能够使用你的本地密钥连接到其他服务。

### 生成新的密钥对

新的服务器私钥可以通过以下方式生成:

1.  删除已有的所有密钥，例如：

    ```
    # rm /etc/ssh/ssh_host_*_key*
    ```

2.  重启 `sshdgenkeys.service` 或者以根用户身份运行 `ssh-keygen -A`

### Run sshd as non-privileged user

You may want to run `sshd` as non-privileged user in containers, or for testing, etc.

Since non-privileged user cannot read host keys in `/etc/ssh`, new host keys must be generated:

```
$ ssh-keygen -q -N "" -t rsa -b 4096 -f /path/to/host/keys/ssh_host_rsa_key
$ ssh-keygen -q -N "" -t ecdsa -f /path/to/host/keys/ssh_host_ecdsa_key
$ ssh-keygen -q -N "" -t ed25519 -f /path/to/host/keys/ssh_host_ed25519_key
```

Create an `sshd_config` file. The example below uses a port higher than 1024, provides a new path to the host keys and disables PAM:

```
/path/to/sshd_config
```

```
Port 2022
HostKey /path/to/host/keys/ssh_host_rsa_key
HostKey /path/to/host/keys/ssh_host_ecdsa_key
HostKey /path/to/host/keys/ssh/ssh_host_ed25519_key
UsePAM no
```

Run *sshd* with the created config. The `-D` flag disables daemon mode and `-e` redirects output to stderr to allow easy monitoring.

```
$ sshd -f /path/to/sshd_config -D -e
```

## 排障

### Checklist

Check these simple issues before you look any further.

1.  The configuration directory `~/.ssh`, its contents should be accessible only by the user (check this on both the client and the server), and the user's home directory should only be writable by the user:

    ```
    $ chmod go-w ~
    $ chmod 700 ~/.ssh
    $ chmod 600 ~/.ssh/*
    $ chown -R $USER ~/.ssh
    ```

2.  Check that the client's public key (e.g. `id_ed25519.pub`) is in `~/.ssh/authorized_keys` on the server.
3.  Check that you did not limit SSH access with `AllowUsers` or `AllowGroups` in the [server config](#Configuration_2).
4.  Check if the user has set a password. Sometimes new users who have not yet logged in to the server do not have a password.
5.  Append `LogLevel DEBUG` to `/etc/ssh/sshd_config`.
6.  Run `journalctl -xe` as root for possible (error) messages.
7.  Restart `sshd` and logout/login on both client and server.

### Connection refused or timeout problem

#### Port forwarding

If you are behind a NAT mode/router (which is likely unless you are on a VPS or publicly addressed host), make sure that your router is forwarding incoming ssh connections to your machine. Find the server's internal IP address with `$ ip addr` and set up your router to forward TCP on your SSH port to that IP. [portforward.com](https://portforward.com) can help with that.

#### Is SSH running and listening?

The ss utility shows all the processes listening to a TCP port with the following command line:

```
$ ss --tcp --listening
```

If the above command do not show the system is listening to the port `ssh`, then SSH is not running: check the [journal](../../02_系统管理与内核/日志/Systemd_Journal.md) for errors etc.

#### Are there firewall rules blocking the connection?

[Iptables](../防火墙/Iptables.md) may be blocking connections on port `22`. Check this with:

```
# iptables -nvL
```

and look for rules that might be dropping packets on the `INPUT` chain. Then, if necessary, unblock the port with a command like:

```
# iptables -I INPUT 1 -p tcp --dport 22 -j ACCEPT
```

For more help configuring firewalls, see firewalls.

#### Is the traffic even getting to your computer?

Start a traffic dump on the computer you are having problems with:

```
# tcpdump -lnn -i any port ssh and tcp-syn
```

This should show some basic information, then wait for any matching traffic to happen before displaying it. Try your connection now. If you do not see any output when you attempt to connect, then something outside of your computer is blocking the traffic (e. g., hardware firewall, NAT router etc.).

#### Your ISP or a third party blocking default port?

> **注意：**Try this step if you **know** you are not running any firewalls and you know you have configured the router for DMZ or have forwarded the port to your computer and it still does not work. Here you will find diagnostic steps and a possible solution.

In some cases, your ISP might block the default port (SSH port 22) so whatever you try (opening ports, hardening the stack, defending against flood attacks, et al) ends up useless. To confirm this, create a server on all interfaces (0.0.0.0) and connect remotely.

If you get an error message comparable to this:

```
ssh: connect to host www.inet.hr port 22: Connection refused
```

That means the port is **not** being blocked by the ISP, but the server does not run SSH on that port (See [security through obscurity](https://en.wikipedia.org/wiki/Security_through_obscurity)).

However, if you get an error message comparable to this:

```
ssh: connect to host 111.222.333.444 port 22: Operation timed out
```

That means that something is rejecting your TCP traffic on port 22. Basically that port is stealth, either by your firewall or 3rd party intervention (like an ISP blocking and/or rejecting incoming traffic on port 22). If you know you are not running any firewall on your computer, and you know that Gremlins are not growing in your routers and switches, then your ISP is blocking the traffic.

To double check, you can run Wireshark on your server and listen to traffic on port 22. Since Wireshark is a Layer 2 Packet Sniffing utility, and TCP/UDP are Layer 3 and above (see [IP Network stack](https://en.wikipedia.org/wiki/Internet_protocol_suite)), if you do not receive anything while connecting remotely, a third party is most likely to be blocking the traffic on that port to your server.

##### Diagnosis

Install either [tcpdump](https://archlinux.org/packages/?name=tcpdump)包 or Wireshark with the [wireshark-cli](https://archlinux.org/packages/?name=wireshark-cli)包 package.

For tcpdump:

```
# tcpdump -ni interface "port 22"
```

For Wireshark:

```
$ tshark -f "tcp port 22" -i interface
```

where `*interface*` is the network interface for a WAN connection (see `ip a` to check). If you are not receiving any packets while trying to connect remotely, you can be very sure that your ISP is blocking the incoming traffic on port 22.

##### Possible solution

The solution is just to use some other port that the ISP is not blocking. Open the `/etc/ssh/sshd_config` and configure the file to use different ports. For example, add:

```
Port 22
Port 1234
```

Also make sure that other "Port" configuration lines in the file are commented out. Just commenting "Port 22" and putting "Port 1234" will not solve the issue because then sshd will only listen on port 1234. Use both lines to run the SSH server on both ports.

Restart the server `sshd.service` and you are almost done. You still have to configure your client(s) to use the other port instead of the default port. There are numerous solutions to that problem, but let us cover two of them here.

#### Read from socket failed: connection reset by peer

Recent versions of OpenSSH sometimes fail with the above error message when connecting to older ssh servers. This can be worked around by setting various [client options](#Configuration) for that host. See [ssh_config(5)](https://man.archlinux.org/man/ssh_config.5) for more information about the following options.

The problem could be the `ecdsa-sha2-nistp*-cert-v01@openssh` elliptical host key algorithms. These can be disabled by setting `HostKeyAlgorithms` to a list excluding those algorithms. On the client side, the `HostKeyAlgorithms` that the client wants to use can also be set by preceding the `HostKeyAlgorithms` list with a `-` to remove the specified algorithms (including wildcards) from the default set (see `ssh_config(5)` man page). You can check the actually used host key algorithm with `ssh -v server_to_connect_to` in the line that contains `kex: host key algorithm:`.

If that does not work, it could be that the list of ciphers is too long. Set the `Ciphers` option to a shorter list (fewer than 80 characters should be enough). Similarly, you can also try shortening the list of `MACs`.

See also the [discussion](https://web.archive.org/web/20161201015151/https://www.gossamer-threads.com/lists/openssh/dev/51339) on the OpenSSH bug forum.

### "\[your shell\]: No such file or directory" / ssh_exchange_identification problem

One possible cause for this is the need of certain SSH clients to find an absolute path (one returned by `whereis -b [your shell]`, for instance) in `$SHELL`, even if the shell's binary is located in one of the `$PATH` entries.

### "Terminal unknown" or "Error opening terminal" error message

If you receive the above errors upon logging in, this means the server does not recognize your terminal. Ncurses applications like nano may fail with the message "Error opening terminal".

The correct solution is to install the client terminal's terminfo file on the server. This tells console programs on the server how to correctly interact with your terminal. You can get info about current terminfo using `$ infocmp` and then find out [which package owns it](../../01_安装与包管理/包管理器/Pacman.md#Querying_package_databases).

If you cannot install it normally, you can copy your terminfo to your home directory on the server:

```
$ ssh myserver mkdir -p  ~/.terminfo/${TERM:0:1}
$ scp /usr/share/terminfo/${TERM:0:1}/$TERM myserver:~/.terminfo/${TERM:0:1}/
```

After logging in and out from the server the problem should be fixed.

#### TERM hack

> **注意：**This should only be used as a last resort.

Alternatively, you can simply set `TERM=xterm` in your environment on the server (e.g. in `.bash_profile`). This will silence the error and allow ncurses applications to run again, but you may experience strange behavior and graphical glitches unless your terminal's control sequences exactly match xterm's.

### Connection closed by x.x.x.x \[preauth\]

If you are seeing this error in your sshd logs, make sure you have set a valid HostKey

```
HostKey /etc/ssh/ssh_host_ed25519_key
```

### subsystem request failed

Since *OpenSSH* 8.8, *scp* uses *SFTP* as the default protocol for data transfers by requesting the subsystem named `sftp`. If you run *scp* in verbose mode, `scp -v`, you can determine which subsystem your client is using (e.g. `Sending subsystem: <subsystem-name>`). Errors such as `subsystem request failed on channel 0` may be fixed by configuring the server's Subsystem settings: [sshd_config(5) § Subsystem](https://man.archlinux.org/man/sshd_config.5#Subsystem). The server configuration should resemble the example below.

```
/etc/ssh/sshd_config
```

```
...
Subsystem subsystem-name /path/to/subsystem-executable
...
```

### id_dsa refused

OpenSSH 7.0 deprecated DSA public keys for security reasons and OpenSSH 9.8 is built without support for DSA keys by default. The first OpenSSH release of 2025 will remove DSA support entirely. For now, if you absolutely must use them, you will need to rebuild [openssh](https://archlinux.org/packages/?name=openssh)包 while passing `--enable-dsa-keys` to `configure`.

### No matching key exchange method found by OpenSSH 7.0

OpenSSH 7.0 deprecated the diffie-hellman-group1-sha1 key algorithm because it is weak and within theoretical range of the so-called Logjam attack (see [https://www.openssh.com/legacy.html](https://www.openssh.com/legacy.html)). If the key algorithm is needed for a particular host, ssh will produce an error message like this:

```
Unable to negotiate with 127.0.0.1: no matching key exchange method found.
Their offer: diffie-hellman-group1-sha1
```

The best resolution for these failures is to upgrade/configure the server to not use deprecated algorithms. If that is not possible, you can force the client to reenable the algorithm with the [client option](#Configuration) `KexAlgorithms +diffie-hellman-group1-sha1`.

### tmux/screen session killed when disconnecting from SSH

If your processes get killed at the end of the session, it is possible that you are using socket activation and it gets killed by [systemd](https://archlinux.org/packages/?name=systemd)包 when it notices that the SSH session process exited. In that case there are two solutions. One is to avoid using socket activation by using `ssh.service` instead of `ssh.socket`. The other is to set `KillMode=process` in the Service section of `ssh@.service`.

The `KillMode=process` setting may also be useful with the classic `ssh.service`, as it avoids killing the SSH session process or the [screen](https://archlinux.org/packages/?name=screen)包 or [tmux](https://archlinux.org/packages/?name=tmux)包 processes when the server gets stopped or restarted.

### SSH session stops responding

SSH responds to [flow control commands](https://en.wikipedia.org/wiki/Software_flow_control) `XON` and `XOFF`. It will freeze/hang/stop responding when you hit `Ctrl+s`. Use `Ctrl+q` to resume your session.

### Broken pipe

If you attempt to create a connection which results in a `Broken pipe` response for `packet_write_wait`, you should reattempt the connection in debug mode and see if the output ends in error:

```
debug3: send packet: type 1
packet_write_wait: Connection to A.B.C.D port 22: Broken pipe
```

The `send packet` line above indicates that the reply packet was never received. So, it follows that this is a *QoS* issue. To decrease the likely-hood of a packet being dropped, set `IPQoS`:

```
/etc/ssh/ssh_config
```

```
Match all
    IPQoS reliability
```

The `reliability` (`0x04`) type-of-service should resolve the issue, as well as `0x00` and `throughput` (`0x08`).

### Terminate unresponsive SSH connection

If a client session is no longer responding and cannot be terminated by instructing the running program (e.g. [shell](../../07_开发与工具/命令行解释器/命令行解释器.md)), you can still terminate the session by pressing `Enter`, `~` and `.` one after another in that order.

The `~` is a pseudo-terminal escape character (see [ssh(1) § ESCAPE CHARACTERS](https://man.archlinux.org/man/ssh.1#ESCAPE_CHARACTERS)), which can be added multiple times depending on the client session to terminate. For example, if you connected from A to B and then from B to C and the session from B to C freezes, you can terminate it by pressing `Enter` and typing `~~.`, which will leave you in a working session on B.

### WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!

If the client warns that the key of an ssh server has changed, you should verify that the newly offered key really belongs to the server operator via an authenticated (not necessarily encrypted) channel. Then remove the old key from the `known_hosts` file with `ssh-keygen -R $SSH_HOST` and accept the new key as if it was a new server.

### Connecting to a remote without the appropriate terminfo entry

When connecting to hosts that do not have a terminfo entry for your terminal, for example, when using a terminal emulator whose terminfo entry is not shipped with [ncurses](https://archlinux.org/packages/?name=ncurses)包 (e.g. [kitty](../../07_开发与工具/终端多路复用器/Kitty.md) and [rxvt-unicode](../../07_开发与工具/终端模拟器/Rxvt-unicode.md)), or when connecting to hosts with a limited terminfo database (e.g. systems running [OpenWrt](https://en.wikipedia.org/wiki/OpenWrt)), various issues will occur with software that relies on [terminfo(5)](https://man.archlinux.org/man/terminfo.5).

A proper solution is to place the appropriate terminfo entry on the host. If that is not feasible, an alternative is to set `TERM` to a value that is both supported by the remote host and compatible with the terminal.

Since OpenSSH 8.7, a custom `TERM` environment variable can be passed to remote hosts with a simple configuration snippet:

```
~/.ssh/config
```

```
Host example.com
  SetEnv TERM=xterm-256color
```

### Connection through jump host fails with "bash: No such file or directory"

If you do not have the `SHELL` environment variable set to a full valid path (on the jump server), connection will fail with an error message simmilar to this one:

```
bash: No such file or directory
kex_exchange_identification: Connection closed by remote host
Connection closed by UNKNOWN port 65535
```

You can simply solve this by setting your `SHELL` to a full path name of a shell that will also be valid on the jump server or by setting a specific `SHELL` variable for each server in your `~/.ssh/config` file.

## 参见

- [Wikibooks:OpenSSH](https://en.wikibooks.org/wiki/OpenSSH)
- [防御暴力 ssh 攻击](https://www.la-samhna.de/library/brutessh.html)
- OpenSSH 密钥管理：[第一部分](https://www.ibm.com/developerworks/library/l-keyc/index.html)在 IBM developerWorks 上, [第二部分](https://www.funtoo.org/OpenSSH_Key_Management,_Part_2)、[第三部分](https://www.funtoo.org/OpenSSH_Key_Management,_Part_3)在 funtoo.org 上
- [Secure Secure Shell](https://stribika.github.io/2015/01/04/secure-secure-shell.html)
