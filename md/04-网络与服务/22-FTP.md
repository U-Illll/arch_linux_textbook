# 第 22 章　FTP

> 把远端目录当本地文件夹用，和在不受信任的网络上安全搬运文件，是两件事。本章分开处理：CurlFtpFS 把 FTP 服务器挂成本地目录，SCP 与 SFTP 负责加密传输，SFTP chroot 负责收紧权限。建议按顺序读，前两节必读：第一节含 root 与普通用户两种挂载、连接加密服务器与乱码处理，第二节是日常最常用的命令。SFTP chroot 偏进阶，只在给外部账号开放受限传输权限时才细读，否则可跳。
> 
> 最易踩的坑是协议关系：SFTP 与 SCP 都建构在 SSH 之上，chroot 改的是 sshd 配置而非 FTP 服务；两种挂载身份的目录路径不同，照抄对方命令常直接失败；乱码要先查挂载选项与服务器编码，而不是反复重挂。

<!-- 本章 3 节，共 20 KB -->

## 22.1　CurlFtpFS

*难度：入门 ｜ 篇幅：3.9 KB ｜ 所属：FTP*

将要讲到：安装、Mount FTP folder as root、Mount FTP folder as normal user、Connect to encrypted server、乱码

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** Most of this article has not been translated（在 [Talk:CurlFtpFS#](https://wiki.archlinuxcn.org/wiki/Talk:CurlFtpFS) 中讨论）

> **注意：**As of February 2015, curlftpfs is reported to be extremely slow, see for example [a Ubuntu bug report](https://bugs.launchpad.net/ubuntu/+source/curlftpfs/+bug/1267749) and a [stackoverflow.com question](https://stackoverflow.com/questions/24360479/ftp-with-curlftpfs-is-extremely-slow-to-the-point-it-is-impossible-to-work-with).

[CurlFtpFS](http://curlftpfs.sourceforge.net/) is a filesystem for accessing FTP hosts based on [FUSE](../../02_系统管理与内核/FUSE/FUSE.md) and libcurl.

## 安装

安装软件包 [curlftpfs](https://archlinux.org/packages/?name=curlftpfs)包。

有必要的话，加载fuse内核模块

```
# modprobe fuse
```

## Mount FTP folder as root

Create the mount point and then mount the FTP folder.

```
# mkdir /mnt/ftp
# curlftpfs ftp.yourserver.com /mnt/ftp/ -o user=username:password
```

If you want to give other (regular) users access right, use the `allow_other` option:

```
# curlftpfs ftp.yourserver.com /mnt/ftp/ -o user=username:password,allow_other
```

Do not add space after the comma or the `allow_other` argument will not be recognized.

To use FTP in active mode add the option 'ftp_port=-':

```
# curlftpfs ftp.yourserver.com /mnt/ftp/ -o user=username:password,allow_other,ftp_port=-
```

You can add this line to /etc/fstab to mount automatically.

```
curlftpfs#USER:PASSWORD@ftp.domain.org /mnt/mydomainorg fuse auto,user,uid=1000,allow_other,_netdev 0 0
```

> **提示：**You can use codepage="*string*" when having problems with non-US English characters on servers that do not support UTF8, e.g. codepage="iso8859-1"

To prevent the password to be shown in the process list, create a `.netrc` file in the home directory of the user running curlftpfs and `chmod 600` with the following content:

```
machine ftp.yourserver.com
login username
password mypassword
```

## Mount FTP folder as normal user

You can also mount as normal user (always use the `.netrc` file for the credentials and ssl encryption!):

```
$ mkdir ~/my-server
$ curlftpfs -o ssl,utf8 ftp://my-server.tld/ ~/my-server
```

if the answer is

```
Error connecting to ftp: QUOT command failed with 500
```

then the server does not support the `utf8` option. Leave it out and all will be fine.

> **提示：**If need be try setting the encoding with for example -o codepage="iso8859-1"

To unmount:

```
$ fusermount -u ~/my-server
```

## Connect to encrypted server

In its default settings, CurlFtpFS will authenticate in cleartext when connecting to a non encrypted connection port. If the remote server is configured to refuse non encrypted authentification method / force encrypted authentification, CurlFtpFS will return a

```
# Error connecting to ftp: Access denied: 530
```

To authenticate to the ftp server using explicit encrypted authentification, you must specify the ssl or tsl option.

```
# curlftpfs ftp.yourserver.com /mnt/ftp/ -o ssl,user=username:password
```

If your server uses a self-generated certificate not thrusted by your computer, you can specify to ignore it

```
# curlftpfs ftp.yourserver.com /mnt/ftp/ -o ssl,no_verify_peer,no_verify_hostname,user=username:password
```

An implicit tsl mode is also available. For more details, check the manual page.

## 乱码

如果中文出现乱码，你可能需要指定编码：

```
sudo curlftpfs -o codepage=gbk -o rw,allow_other ftp://username:password@ftp.yourserver.com /mnt/ftp
```

## 22.2　SCP 与 SFTP

*难度：进阶 ｜ 篇幅：7.3 KB ｜ 所属：FTP*

将要讲到：Secure file transfer protocol (SFTP)、Secure file transfer protocol (SFTP) with a chroot jail、Secure copy protocol (SCP)

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[SFTP chroot](SFTP%20chroot.md)。**
>
> **附注：** Instructions seem to be the same as in [SFTP chroot](SFTP%20chroot.md) and has more content.（在 Talk:SCP 与 SFTP#Incorrect 'Considered for redirection' banner? 中讨论）

> **翻译状态：**
>
> - 本文（或部分内容）译自 [SCP and SFTP](https://wiki.archlinux.org/title/SCP_and_SFTP)，最近一次同步于 2024-09-05，若英文版本有所[更改](https://wiki.archlinux.org/title/SCP_and_SFTP?diff=0&oldid=815869)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/SCP_and_SFTP_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

The [Secure copy (SCP)](https://en.wikipedia.org/wiki/Secure_copy) is a protocol to transfer files via a [Secure Shell](../SSH/安全外壳协议.md) connection. The [SSH file transfer protocol (SFTP)](https://en.wikipedia.org/wiki/SSH_file_transfer_protocol) is a related protocol, also relying on a secure shell back-end. Both protocols allow secure file transfers, encrypting passwords and transferred data. The SFTP protocol, however, features additional capabilities like, for example, resuming broken transfers or remote file manipulation like deletion.

## Secure file transfer protocol (SFTP)

Install and configure [OpenSSH](../SSH/OpenSSH.md). Once running, SFTP is available by default.

Access files with the *sftp* program or [SSHFS](../SSH/SSHFS.md). Many standard FTP programs should work as well.

## Secure file transfer protocol (SFTP) with a chroot jail

Sysadmins can jail a subset of users to a chroot jail using [openssh](https://archlinux.org/packages/?name=openssh)包 thus restricting their access to a particular directory tree. This can be useful to simply share some files without granting full system access or shell access. Users with this type of setup may use SFTP clients such as [filezilla](https://archlinux.org/packages/?name=filezilla)包 to put/get files in the chroot jail.

### Setup the filesystem

Create a jail directory:

```
# mkdir -p /var/lib/jail
```

Optionally, bind mount the filesystem to be shared to this directory. In this example, `/mnt/data/share` is to be used. It is owned by root and has octal permissions of 755.

```
# mount -o bind /mnt/data/share /var/lib/jail
```

> **提示：**Consider adding an entry to `/etc/fstab` to make the bind mount survive a reboot.

### Create an unprivileged user

Create the share user and setup a good password:

```
# useradd -g sshusers -d /var/lib/jail foo
# passwd foo
```

### Setup OpenSSH

Add the following to the end of `/etc/ssh/sshd_config` to enable the share and to enforce the restrictions:

```
/etc/ssh/sshd_config
```

```
...
 Match group sshusers
  ChrootDirectory %h
  X11Forwarding no
  AllowTcpForwarding no
  PasswordAuthentication yes
  ForceCommand internal-sftp
```

Restart `sshd.service` to re-read the configuration file. See [SFTP chroot](SFTP%20chroot.md) to configure the keys correctly when using chroot or it will get permission denied.

Test that in fact, the restrictions are enforced by attempting an ssh connection via the shell. The ssh server should return a polite notice of the setup:

```
$ ssh foo@someserver.com
```

```
foo@someserver.com's password:
This service allows sftp connections only.
Connection to someserver.com closed.
```

## Secure copy protocol (SCP)

Install, configure and start [OpenSSH](../SSH/OpenSSH.md). It contains the *scp* utility to transfer files.

More features are available by installing additional packages, for example [rssh](https://aur.archlinux.org/packages/rssh/)AUR or [scponly](https://archlinux.org/packages/?name=scponly)包 described below.

> **注意：**Since OpenSSH 9.0 the *scp* utility uses the SFTP protocol by default. The `-O` option must be used to use the legacy SCP protocol.

> **警告：**The scp protocol is outdated, inflexible and not readily fixed. Its authors recommend the use of more modern protocols like sftp and rsync for file transfer instead.

### General Usage

#### Linux to Linux

Copy file from a remote host to local host SCP example:

```
$ scp username@from_host:file.txt /local/directory/
```

Copy file from local host to a remote host SCP example:

```
$ scp file.txt username@to_host:/remote/directory/
```

Copy directory from a remote host to local host SCP example:

```
$ scp -r username@from_host:/remote/directory/  /local/directory/
```

Copy directory from local host to a remote host SCP example:

```
$ scp -r /local/directory/ username@to_host:/remote/directory/
```

Copy file from remote host to remote host SCP example:

```
$ scp username@from_host:/remote/directory/file.txt username@to_host:/remote/directory/
```

#### Linux to Windows

Use a Windows program such as [WinSCP](https://winscp.net/eng/download.php)

### Scponly

[Scponly](https://github.com/scponly/scponly/wiki) is a limited shell for allowing users scp/sftp access and only scp/sftp access. Additionally, one can setup *scponly* to chroot the user into a particular directory increasing the level of security.

install [scponly](https://archlinux.org/packages/?name=scponly)包.

For existing users, simply set the user's shell to scponly:

```
# usermod -s /usr/bin/scponly username
```

#### Adding a chroot jail

The package comes with a script to create a chroot. To use it, run:

```
# /usr/share/doc/scponly/setup_chroot.sh
```

- Provide answers
- Check that `/path/to/chroot` has `root:root` owner and `r-x` for others
- Change the shell for selected user to `/usr/bin/scponlyc`
- sftp-server may require some libnss modules such as libnss_files. Copy them to chroot's `/lib` path.

#### Uploads to Chroot jail root dir

For security reasons the directory set as the chroot directory must be owned by root with only root having write access to it otherwise sftp/ssh connections will be denied. This of course means regular users cannot upload files to the root directory. In order to get around this while not compromising security you can create a folder inside the chroot directory which the regular user or group has write access to, e.g:

```
# cd /var/lib/jail
# mkdir uploads
# chown :sshusers uploads
# chmod 730 uploads
```

> **注意：**This will only allow users of group "sshusers" to upload to (but not list the contents of) the "uploads" directory. Use `chmod 770` to allow sshusers to view contents.

Some applications utilizing SFTP do not allow input of sub-directories when performing operations (e.g. uploading files), and will attempt to upload files to the chroot base directory (which will be denied). In order to force these applications to use a specific sub-directory you can append the following to the "ForceCommand" option:

```
/etc/ssh/sshd_config
```

```
...
 Match group sshusers
  ...
  ForceCommand internal-sftp -d /uploads
```

Users on connect will then have their start directory change to the specified sub-directory (remember to restart the sshd server).

## 22.3　SFTP chroot

*难度：进阶 ｜ 篇幅：8.4 KB ｜ 所属：FTP*

将要讲到：安装、配置、技巧提示、SFTP 的替代品、参见

[OpenSSH](../SSH/OpenSSH.md) 4.9+ 包含一个用于 SFTP 的内置 chroot，但需要对普通安装进行一些调整。

## 安装

安装并配置 [OpenSSH](../SSH/OpenSSH.md)。运行后，确保 `sftp-server` 已正确设置：

```
/etc/ssh/sshd_config
```

```
Subsystem sftp /usr/lib/ssh/sftp-server
```

使用 *sftp* 或 [SSHFS](../SSH/SSHFS.md) 访问文件。许多标准的[FTP 客户端](../网络应用程序/应用程序列表_互联网.md#FTP_clients)也可能有用。

## 配置

### 设置文件系统

> **注意：**
>
> - 读者可以自行选择文件访问方案。例如，选择性地为一块传入（可写）空间和/或一块只读空间创建一个子文件夹。这不需要在 `/srv/ssh/jail`之下直接完成——它可以在 live 绑定挂载的磁盘分区完成。
> - 也可以 chroot 到 `/home` 目录从而跳过绑定，但是目标用户家目录应归属根：
>
> ```
> # chown root:root /home/<username>
> # chmod 0755 /home/<username>
> ```

绑定挂载要共享的 live 文件系统到此目录。此例使用 `/mnt/data/share`，由[用户](../../08_安全与加密/安全/用户和用户组.md) `root` 拥有并具有 `755 的八进制 [权限](../../07_开发与工具/命令行/文件权限与属性.md)` ：

```
# chown root:root /mnt/data/share
# chmod 755 /mnt/data/share
# mkdir -p /srv/ssh/jail
# mount -o bind /mnt/data/share /srv/ssh/jail
```

将以下添加到 [fstab](../../01_安装与包管理/启动过程/Fstab.md) 以使绑定挂载在重新启动后不失效：

```
/mnt/data/share /srv/ssh/jail  none   bind   0   0
```

### 创建一个无特权用户

> **注意：**您无需创建一个组，可以使用 `Match User` 以代替 `Match Group`。

创建 `sftponly` [用户组](../../08_安全与加密/安全/用户和用户组.md):

```
# groupadd sftponly
```

创建一个使用 *sftponly* 作为主要组的[用户](../../08_安全与加密/安全/用户和用户组.md)并且 [shell](../../07_开发与工具/命令行解释器/命令行解释器.md) 登录访问被拒:

```
# useradd -g sftponly -s /usr/bin/nologin -d /srv/ssh/jail username
```

设置一个（复杂的）密码以防止 `account is locked` 错误（即使使用密钥验证也可能出现）：

```
# passwd username
```

### 配置 OpenSSH

> **注意：**您可能想使用 `Match User` 而不是 `Match Group`，如上一步所述。

```
/etc/ssh/sshd_config
```

```
Subsystem sftp /usr/lib/ssh/sftp-server

Match Group sftponly
  ChrootDirectory %h
  ForceCommand internal-sftp
  AllowTcpForwarding no
  X11Forwarding no
  PasswordAuthentication no
```

重启 `sshd.service` 以应用更改。

#### 为 authorized_keys 修复路径

> **提示：**在`(pre)auth`错误的情况下，在客户端和服务器上使用 OpenSSH 的 [调试模式](../SSH/SSH%20密钥.md#Key_ignored_by_the_server)。

应用*AuthorizedKeysFile*的标准路径时，已 chroot 的用户 [SSH 密钥](../SSH/SSH%20密钥.md)验证会失败。欲修复此问题，附加一个 *AuthorizedKeysFile* 上的属于根的目录到 `/etc/openssh/sshd_config`，以 `/etc/ssh/authorized_keys` 为例:

```
/etc/ssh/sshd_config
```

```
AuthorizedKeysFile /etc/ssh/authorized_keys/%u .ssh/authorized_keys
PermitRootLogin no
PasswordAuthentication no
PermitEmptyPasswords no
Subsystem sftp /usr/lib/ssh/sftp-server
```

创建 *authorized_keys* 文件夹，在客户端生成一个 [SSH 密钥](../SSH/SSH%20密钥.md#Choosing_the_key_location_and_passphrase)，[复制](../SSH/SSH%20密钥.md#Manual_method)服务器密钥 `/etc/ssh/authorized_keys`（或任何其他首选方法）的内容并[设置正确的权限](../SSH/SSH%20密钥.md#Key_ignored_by_the_server):

```
# mkdir /etc/ssh/authorized_keys
# chown root:root /etc/ssh/authorized_keys
# chmod 755 /etc/ssh/authorized_keys
# echo 'ssh-rsa <key> <username@host>' >> /etc/ssh/authorized_keys/username
# chmod 644 /etc/ssh/authorized_keys/username
```

重启 `sshd.service`.

## 技巧提示

### 写权限

[绑定](#设置文件系统)需要路径完全归属 `root`，但文件和/或子文件夹不需要。 以下示例中，[用户](../../08_安全与加密/安全/用户和用户组.md) *www-demo* 以 `/srv/ssh/www/demo` 作为 jail 目录:

```
# mkdir /srv/ssh/www/demo/public_html
# chown www-demo:sftponly /srv/ssh/www/demo/public_html
# chmod 755 /srv/ssh/www/demo/public_html
```

用户现在应可在此目录中创建文件/子目录。欲详细信息，请参阅[文件权限和属性](../../07_开发与工具/命令行/文件权限与属性.md)。

### 仅允许上传

欲仅允许通过 sftp 上传并禁止下载文件，修改 `ForceCommand internal-sftp` 行：

```
/etc/ssh/sshd_config
```

```

Match Group sftponly

  ForceCommand internal-sftp -u 0666 -p realpath,open,write,close,lstat

```

### 日志

用户无法访问 `/dev/log`。当用户连入并尝试下载该文件，即可通过于进程上运行 `strace` 以见此。

#### 创建子目录

在 `ChrootDirectory` 中创建子目录 `dev`，例如：

```
# mkdir /usr/local/chroot/user/dev
# chmod 755 /usr/local/chroot/user/dev
```

现在您应于 `/usr/local/chroot/user/dev/log` 创建被 openssh 接受的套接字。您可以直接将该套接字与 `/dev/log` 或 `/run/systemd/journal/dev-log`（若您使用 journald）绑定或用 `syslog-ng`/`rsyslog` 创建。

#### 绑定 journald

```
# touch /usr/local/chroot/user/dev/log
# mount --bind /run/systemd/journal/dev-log /usr/local/chroot/user/dev/log
```

#### Syslog-ng 配置

添加新源到 `/etc/syslog-ng/syslog-ng.conf` 日志的并添加配置，例如修改段：

```
source src {
  unix-dgram("/dev/log");
  internal();
  file("/proc/kmsg");
};
```

成为：

```
source src {
  unix-dgram("/dev/log");
  internal();
  file("/proc/kmsg");
  unix-dgram("/usr/local/chroot/theuser/dev/log");
};
```

并附加：

```
#sftp configuration
destination sftp { file("/var/log/sftp.log"); };
filter f_sftp { program("internal-sftp"); };
log { source(src); filter(f_sftp); destination(sftp); };
```

（可选）如果您想将 SSH 消息类似地记录到其自身的文件中：

```
#sshd configuration
destination ssh { file("/var/log/ssh.log"); };
filter f_ssh { program("sshd"); };
log { source(src); filter(f_ssh); destination(ssh); };
```

（来自 Syslog-ng#Move log to another file）

#### OpenSSH 配置

编辑 `/etc/ssh/sshd_config` 以替换所有 `internal-sftp` 的实例为 `internal-sftp -f AUTH -l VERBOSE`。

#### 重启服务

重启 `syslog-ng` 与 `sshd` 服务。

`/usr/local/chroot/theuser/dev/log` 现应存在。

## SFTP 的替代品

### 安全复制协议 (SCP)

[openssh](https://archlinux.org/packages/?name=openssh)包 提供 *scp* 命令来传输文件。SCP 可能比使用 SFTP 更快 .

安装 [rssh](https://aur.archlinux.org/packages/rssh/)AUR 或 [scponly](https://archlinux.org/packages/?name=scponly)包 作为替代 shell 解决方案。

#### 只使用 SCP (Scponly)

安装 [scponly](https://archlinux.org/packages/?name=scponly)包.

对于现有用户，只需将用户的 shell 设置为 scponly：

```
# usermod -s /usr/bin/scponly username
```

参见 [Scponly 维基](https://github.com/scponly/scponly/wiki)以获得更多细节。

#### 添加 chroot jail

该软件包附带一个用于创建 chroot 的脚本。要使用它，请运行：

```
# /usr/share/doc/scponly/setup_chroot.sh
```

- 提供答案。
- 核实 `/path/to/chroot` 属于 `root:root` 和有对于其他用户的 `r-x`。
- 将所选用户的 shell 更改为 `/usr/bin/scponlyc`。
- sftp 服务器 可能请求一些 libnss 模块例如 libnss_files。将它们复制到 chroot 的 `/lib` 路径。

## 参见

- [https://www.minstrel.org.uk/papers/sftp/](https://www.minstrel.org.uk/papers/sftp/)
- [sshd_config(5)](https://man.archlinux.org/man/sshd_config.5)
