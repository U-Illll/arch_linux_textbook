# 第 37 章　多播DNS

> 多播 DNS 让同一局域网内的主机不必架设域名服务器，也能互相发现、按名字访问。本章处理的就是这类地址解析问题：怎样装好 Avahi，让本机正常收发查询，再把本机提供的服务广播出去，让别的机器看得见。
> 
> 全章只有《Avahi》一节，顺序读完即可，没有可跳的部分；末尾的「参见」只作延伸线索，第一遍可以略过。最容易踩的坑是以为装上 Avahi 就万事大吉——它负责的是发现，不等于服务本身就能用；排错时也要先把链路和名字解析两边都确认通了，再去查服务声明，而不是一上来反复重装。

<!-- 本章 1 节，共 14 KB -->

## 37.1　Avahi

*难度：进阶 ｜ 篇幅：13.5 KB ｜ 所属：多播DNS*

将要讲到：安装、使用 Avahi、添加服务、故障排除、参见

摘自 [维基百科的 Avahi 条目](https://en.wikipedia.org/wiki/Avahi_(software))：

: [Avahi](https://avahi.org/) 是一种自由的[零配置网络](https://en.wikipedia.org/wiki/Zero-configuration_networking)（zeroconf）实现，其中包括用于通过组播 DNS/DNS-SD 发现服务的系统。它允许程序在无需特别配置的情况下，发布和发现局域网上运行的服务与主机。例如，连接到网络后，你便可以立即发现可用的打印机、可访问的文件以及可以与之通信的其他用户。Avahi 采用 GNU 宽通用公共许可证（LGPL）授权。

## 安装

安装 [avahi](https://archlinux.org/packages/?name=avahi)包 软件包，并启用 `avahi-daemon.service`；如果希望使用[套接字激活](../../01_安装与包管理/初始化/Systemd.md)，也可以启用 `avahi-daemon.socket`。

> **注意：**[systemd-resolved](../网络/Systemd-resolved.md) 内置了 mDNS 服务。在使用 Avahi 之前，请确保已[禁用 systemd-resolved 的组播 DNS 解析器/响应器](../网络/Systemd-resolved.md#mDNS)，或者直接禁用 `systemd-resolved.service`。

## 使用 Avahi

### 主机名解析

Avahi 使用“*hostname*.local”命名方式提供本地主机名解析。要启用此功能，请安装 [nss-mdns](https://archlinux.org/packages/?name=nss-mdns)包 软件包，并启动/启用 `avahi-daemon.service`。

接下来编辑 `/etc/nsswitch.conf`，修改 `hosts` 行，在 `resolve` 和 `dns` 之前加入 `mdns_minimal [NOTFOUND=return]`：

```
hosts: mymachines mdns_minimal [NOTFOUND=return] resolve [!UNAVAIL=return] files myhostname dns
```

> **注意：**
>
> - 如果解析 `.local` 主机名时速度较慢（或者你不希望使用 IPv6），可以尝试分别用 `mdns4_minimal` 和 `mdns4` 替换 `mdns_minimal` 和 `mdns`。
> - 上述配置使 `nss-mdns` 成为 `.local` 域名的权威解析模块，但以下情况除外：单播 DNS 服务器会响应针对顶级域名 `local` 的 `SOA` 查询，或者查询的名称包含两个以上的标签。参见 `nss-mdns` 的[启用说明](https://github.com/lathiat/nss-mdns/blob/master/README.md#activation)。
> - 即使关闭了 mDNS 支持，[systemd-resolved](../网络/Systemd-resolved.md) 仍会响应这些查询。参见 [#systemd-resolved 导致 nss-mdns 无法工作](#systemd-resolved_导致_nss-mdns_无法工作)。
> - 有关 NSS 配置的更多信息，请参阅 [nss-mymachines(8)](https://man.archlinux.org/man/nss-mymachines.8) 和 [Avahi 文档](https://github.com/lathiat/nss-mdns/blob/master/README.md#activation)。

#### 为自定义顶级域名配置 mDNS

`mdns_minimal` 模块仅处理针对 `.local` 顶级域名（TLD）的查询。注意 `[NOTFOUND=return]`：它表示如果 `mdns_minimal` 无法找到 `*.local` 主机，就不会继续通过 `dns`、`myhostname` 等模块进行查找。

如果希望 Avahi 支持其他顶级域名，需要：

- 将 `mdns_minimal [NOTFOUND=return]` 替换为完整的 `mdns` 模块。此外还有仅支持 IPv4 或 IPv6 的模块 `mdns[46](_minimal)`。
- 在 `/etc/avahi/avahi-daemon.conf` 中将 `domain-name` 配置为所需的域名。
- 在 `/etc/mdns.allow` 中将 Avahi 使用的自定义顶级域名加入允许列表。

#### 工具

Avahi 提供了多个实用工具，可帮助发现网络中运行的服务。例如，执行以下命令来发现局域网内的服务：

```
$ avahi-browse --all --ignore-local --resolve --terminate
```

如果该命令没有输出，很可能是防火墙阻止了 mDNS 流量。

如果只是想通过 mDNS 查询将 `.local` 主机名解析为 IP 地址（类似于 *dig* 或 *nslookup*），可以使用：

```
$ avahi-resolve-host-name some-host-name.local
```

注意，`getent hosts` 命令既可以执行 DNS 查询，也可以执行 mDNS 查询。

Avahi 零配置网络浏览器 `avahi-discover` 可以显示网络上的各类服务。使用该工具需要安装 Avahi 的可选依赖 [gtk3](https://archlinux.org/packages/?name=gtk3)包、[python-dbus](https://archlinux.org/packages/?name=python-dbus)包 和 [python-gobject](https://archlinux.org/packages/?name=python-gobject)包。此外，还可以分别使用 `bssh` 和 `bvnc` 浏览 SSH 和 VNC 服务器。

### 防火墙

如果正在使用防火墙，请确保开放 UDP `5353` 端口。

### 链路本地（Bonjour/Zeroconf）聊天

Avahi 可用于在 Linux 上提供 Bonjour 协议支持。有关支持 Bonjour 协议的客户端列表，请参阅 [即时通信客户端比较](https://en.wikipedia.org/wiki/Comparison_of_instant_messaging_clients)或[应用程序列表/互联网#即时通讯客户端](../网络应用程序/应用程序列表_互联网.md#即时通讯客户端)。

### 获取 IPv4LL IP 地址

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[dhcpcd](../网络配置/Dhcpcd.md)。**
>
> **附注：** 应将此内容合并到主页面（在 [Talk:Avahi](https://wiki.archlinuxcn.org/wiki/Talk:Avahi) 中讨论）

如果 [dhcpcd](../网络配置/Dhcpcd.md) 客户端无法通过 DHCP 获取地址，可以尝试获取 IPv4LL 地址。默认情况下，此功能处于禁用状态。要启用它，请将 `noipv4ll` 一行注释掉：

```
/etc/dhcpcd.conf
```

```
...
#noipv4ll
...
```

或者运行 `avahi-autoipd`：

```
# avahi-autoipd -D
```

## 添加服务

Avahi 会发布 `/etc/avahi/services` 目录中 `*.service` 文件所定义的服务。该目录中的文件必须可供 `avahi` 用户或用户组读取。

如果想发布的服务没有对应的 `*.service` 文件，可以很方便地自行创建。 例如，假设你想发布一项运行在本机 TCP `17` 端口、符合 [RFC 865](https://tools.ietf.org/html/rfc865) 规范的每日名言（QOTD）服务。

首先需要确定 `<type>`。[avahi.service(5)](https://man.archlinux.org/man/avahi.service.5) 指出，其值应为“该服务的 DNS-SD 服务类型，例如 '_http._tcp'”。由于 [DNS-SD 注册表已于 2010 年合并到 IANA 注册表](https://www.dns-sd.org/ServiceTypes.html)，因此可以在 [IANA 注册表](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml)或 `/etc/services` 文件中查找服务名称。该服务在注册表中的名称为 `qotd`。由于 QOTD 使用 TCP，因此其服务类型为 `_qotd._tcp`，端口为 `17`（依据 IANA 注册表和 RFC 865）。

于是可以创建以下服务文件：

```
qotd.service
```

```
<?xml version="1.0" standalone='no'?><!--*-nxml-*-->
<!DOCTYPE service-group SYSTEM "avahi-service.dtd">

<service-group>

  <name replace-wildcards="yes">%h</name>

  <service>
    <type>_qotd._tcp</type>
    <port>17</port>
  </service>

</service-group>
```

对于更复杂的场景，例如发布运行在其他服务器上的服务、使用 DNS 子类型等，请参阅 [avahi.service(5)](https://man.archlinux.org/man/avahi.service.5)。

需要注意，Avahi 不支持在 <type> 字段中使用任意字符串，只能使用 [Avahi 服务数据库](https://github.com/lathiat/avahi/blob/master/service-type-database/service-types)中已知的值。如果希望注册自定义服务类型，可能需要编辑数据库定义、构建更新后的版本，并将其分发到各台主机。

### SSH

Avahi 附带了一个用于发布 SSH 服务器的示例服务文件。要启用它，请执行：

```
# cp /usr/share/doc/avahi/ssh.service /etc/avahi/services/
```

### 文件共享

#### NFS

如果已经配置了 [NFS](../网络共享/NFS.md) 共享，可以通过 Avahi，让支持 Zeroconf 的浏览器（例如 KDE 上的 Konqueror、macOS 上的 Finder）或 [GNOME/Files](../../05_桌面与图形/GNOME/GNOME_文件.md) 等文件管理器自动挂载这些共享。

在 `/etc/avahi/services` 中创建一个包含以下内容的 `.service` 文件：

```
/etc/avahi/services/nfs_Zephyrus_Music.service
```

```
<?xml version="1.0" standalone='no'?>
<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
<service-group>
  <name replace-wildcards="yes">NFS Music Share on %h</name>
  <service>
    <type>_nfs._tcp</type>
    <port>2049</port>
    <txt-record>path=/data/shared/Music</txt-record>
  </service>
</service-group>
```

如果在 `/etc/exports` 中设置了 *insecure* 选项，上述端口就是正确的；否则需要更改端口（注意，macOS 客户端需要 *insecure* 选项）。路径应为导出的共享目录或其子目录。由于某些原因，Leopard 移除了自动挂载功能，不过仍有[一个可用的脚本](http://www.macosxhints.com/article.php?story=20071116042238744)。该脚本基于[这篇帖子](https://web.archive.org/web/20110113180402/https://ubuntuforums.org/showthread.php?p=4387032#post4387032#post4387032)。

#### Samba

如果服务器和客户端上都运行了 Avahi 守护进程，客户端的文件管理器应当能够自动发现服务器。

#### Vsftpd

还可以自动发现普通的 FTP 服务器，例如 vsftpd。安装 [vsftpd](https://archlinux.org/packages/?name=vsftpd)包 软件包，并根据个人需要调整 vsftpd 的配置（参见 [vsftpd.conf(5)](https://man.archlinux.org/man/vsftpd.conf.5)）。

在 `/etc/avahi/services` 中创建一个包含以下内容的 `.service` 文件：

```
/etc/avahi/services/ftp.service
```

```
<?xml version="1.0" standalone='no'?>
<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
<service-group>
  <name>FTP file sharing</name>
  <service>
    <type>_ftp._tcp</type>
    <port>21</port>
  </service>
</service-group>
```

此时 Avahi 应当开始发布该 FTP 服务器。你应该能够从局域网中另一台计算机的文件管理器中找到它。客户端可能还需要启用[#主机名解析](#主机名解析)功能。

## 故障排除

### 主机名末尾不断追加递增的数字

如果没有[禁用 systemd-resolved 的组播 DNS 解析器](../网络/Systemd-resolved.md#mDNS)，就可能出现这种情况。此外，这也是一个由主机名竞态条件引起的[已知问题](https://github.com/lathiat/avahi/issues/117)。一种可能的解决方法是[禁用 IPv6](https://github.com/lathiat/avahi/issues/117#issuecomment-302849130)，以尝试避免竞态条件。如果存在多个网络接口，可以[使用 allow-interfaces](https://github.com/lathiat/avahi/issues/117#issuecomment-401225716) 将 Avahi 限制在单个接口上。另一种可能的解决方法是[禁用缓存](https://github.com/lathiat/avahi/issues/117#issuecomment-442201162)，从而阻止 Avahi 检查主机名冲突，但这也会使 Avahi 无法执行查询。

### systemd-resolved 导致 nss-mdns 无法工作

只有当 `/etc/resolv.conf` 中列出的 DNS 服务器对“local”域名的 SOA 查询返回 `NXDOMAIN` 时，*nss-mdns* 才能正常工作。

首先检查当前配置的 DNS 服务器是否会对“local”域名的 SOA 查询返回 `NXDOMAIN`。例如：

```
$ host -t SOA local
```

如果 DNS 服务器返回 `NXDOMAIN`，就不需要执行下面的步骤。 即使使用 [systemd-resolved](../网络/Systemd-resolved.md)，Avahi 也应能正常发现网络中的资源。

在旧版本的 [systemd-resolved](../网络/Systemd-resolved.md) 中，[resolved.conf(5)](https://man.archlinux.org/man/resolved.conf.5) 中的全局选项 `MulticastDNS=no` 会使其针对“local”域名返回与 Avahi 不兼容的响应代码，导致 Avahi 无法正确发现网络资源（如打印机）。详见 [systemd 问题 21659](https://github.com/systemd/systemd/issues/21659)。

不过，如果上述 DNS 查询没有对“local”域名返回 `NXDOMAIN`，可以使用完整的 `mdns` NSS 模块替代 `mdns_minimal`，并创建 `/etc/mdns.allow`，仅允许查询“local”域名。例如：

```
/etc/nsswitch.conf
```

```
hosts: mymachines mdns [NOTFOUND=return] resolve [!UNAVAIL=return] files myhostname dns
```

```
/etc/mdns.allow
```

```
.local.
.local
```

> **注意：**上述方法会破坏 [mtr](https://archlinux.org/packages/?name=mtr)包 和 [traceroute](https://archlinux.org/packages/?name=traceroute)包 等网络工具的反向 DNS 查询。这些程序会尝试通过 Avahi 进行反向解析，在超时后也不会回退到其他 DNS 服务。该问题似乎类似于 [Debian 问题 433943](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=433943)；遗憾的是，其中建议使用 `mdns_minimal` 而不是 `mdns`。

### Avahi 套接字出现 ECONNREFUSED（连接被拒绝）错误

如果 Avahi 实例能够正常启动和运行，但 NSS 似乎没有将请求转发给 mDNS，可能是套接字 `/run/avahi-daemon/socket` 处于卡住的状态。例如，可以通过 strace 验证。在这种情况下，可能需要同时重启 `avahi-daemon.service` 和 `avahi-daemon.socket`，才能恢复正常工作。

## 参见

- [Avahi](https://avahi.org/) — 项目官方网站
- [维基百科：Avahi（软件）](https://en.wikipedia.org/wiki/Avahi_(software))
- [iTunes（包含 Bonjour）](https://www.apple.com/itunes/download/) — 在 Windows 上启用 Zeroconf
- [http://www.zeroconf.org/](http://www.zeroconf.org/)
