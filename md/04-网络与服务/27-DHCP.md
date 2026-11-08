# 第 27 章　DHCP

> 主机开机就要拿到地址、网关和域名服务器，逐台手写既麻烦又容易冲突，本章就是把这件事自动化。Dhcpd 篇幅短，安装、配置、用法一条线讲完，必读，读完即可拉起一份最小可用配置；Dnsmasq 篇幅长，多出提示和技巧一节，不必顺读，配置够用就先动手，技巧按场景挑看，赶时间可跳过，遇问题再翻。
> 
> 最容易踩的坑是让两套服务在同一子网同时发地址，客户端认到哪台全凭运气，表现为时好时坏的断网；也别把 Dnsmasq 当成只会发地址的纯 DHCP 服务，它还兼管名字解析，只盯着地址池，常常地址发对了名字仍然不通。

<!-- 本章 2 节，共 19 KB -->

## 27.1　Dhcpd

*难度：入门 ｜ 篇幅：3.9 KB ｜ 所属：DHCP*

将要讲到：安装、配置、用法、参阅

[dhcpd](https://www.isc.org/dhcp) 是老版本的 ISC DHCP 服务器。

> **注意：**
>
> - *dhcpd* 不再受维护。[Internet Systems Consortium](https://www.isc.org/)（ISC，互联网系统联盟）推荐 [Kea](https://wiki.archlinux.org/title/Kea) 作为其 [官方替代品](https://www.isc.org/kea/)。
> - *dhcpd* (DHCP **(服务器)** 守护进程) 不是 [dhcpcd](../网络配置/Dhcpcd.md) (DHCP **客户端** 守护进程).

## 安装

安装 [dhcp](https://archlinux.org/packages/?name=dhcp)包 包。

## 配置

为你想让 *dhcpd* 监听的接口分配静态 IPv4 地址（此处为 `eth0`）。指定的子网不应与其他接口的子网重叠：

```
# ip link set up dev eth0
# ip addr add 139.96.30.100/24 dev eth0 # arbitrary address
```

> **提示：**
>
> - 要在启动时分配静态 IP 地址，参见[网络配置#静态 IP 地址](../网络/网络配置.md#静态_IP_地址)。
> - [RFC 1918](https://tools.ietf.org/html/rfc1918) 子网是为私有网络保留的，并不会与互联网上的主机冲突。

默认配置文件 `dhcpd.conf` 文件包含许多注释的例子，复制一份该文件：

```
# mv /etc/dhcpd.conf /etc/dhcpd.conf.example
```

欲仅监听子网 `139.96.30.0/24`，可创建最简配置文件如下：

```
/etc/dhcpd.conf
```

```
option domain-name-servers 8.8.8.8, 8.8.4.4;
option subnet-mask 255.255.255.0;
option routers 139.96.30.100;
subnet 139.96.30.0 netmask 255.255.255.0 {
  range 139.96.30.150 139.96.30.250;
}
```

配置文件使用的选项如下：

`domain-name-servers` 包含提供给客户的 DNS 服务器地址。此处以谷歌公共 DNS 服务器为例。如果你已经将你自己的 DNS 服务器配置在一台本地机器上，则应在子网中的指定其地址（如 `139.96.30.0/24`）。

`subnet-mask` 和 `routers` 定义子网掩码和子网上可用路由器的列表；`routers` 也定义了提供给客户端的默认网关。对于小型网络，您往往能把 `255.255.255.0` 用作掩码，并为运行 *dhcpd* 的机器指定 IP 地址（此处为 `139.96.30.100`）。

`subnet` 为各子网单独设置选项，作用于 *dhcpd* 所监听的网络接口。此处，我们定义一段可用 IP 地址用作一个子网 `139.96.30.0/24`（在单个接口 `eth0`）。

完整选项列表，参见 [dhcpd.conf(5)](https://man.archlinux.org/man/dhcpd.conf.5)。

> **注意：**尽管 [dhcpd(8)](https://man.archlinux.org/man/dhcpd.8) 给出了相反的说法，但是接口必须含有在配置文件中声明的子网，才会被 *dhcpd* 监听。

### PXE

[PXE](../../01_安装与包管理/启动过程/PXE.md)（预启动执行环境）的配置通过以下两个选项完成：

```
/etc/dhcpd.conf
```

```
next-server 192.168.0.2;
filename "/pxelinux.0";
```

`next-server` 是 [TFTP](https://wiki.archlinux.org/title/TFTP) 服务器的 IP 地址，而 `filename` 是用于启动的映像。

该段配置既可置于整个 `subnet` 中，亦可仅置于某个 `host` 定义中。

## 用法

*dhcpd* 包括两个单元文件：`dhcpd4.service` 与 `dhcpd6.service`，可用于控制守护进程。它们会分别为 IPv4 和 IPv6 在**所有** [网络接口](https://wiki.archlinux.org/title/Network_interfaces) 上启动守护进程。

## 参阅

- [ISC DHCP 文档](https://kb.isc.org/docs/aa-00333)

## 27.2　Dnsmasq

*难度：进阶 ｜ 篇幅：15.2 KB ｜ 所属：DHCP*

将要讲到：安装、配置、提示和技巧、参阅

[dnsmasq](https://www.thekelleys.org.uk/dnsmasq/doc.html) 提供 [DNS 服务器](https://en.wikipedia.org/wiki/Name_server)、支持 [DHCPv6](https://en.wikipedia.org/wiki/DHCPv6) 和 [PXE](https://en.wikipedia.org/wiki/Preboot_Execution_Environment) 的 [DHCP 服务器](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol)、[TFTP 服务器](https://en.wikipedia.org/wiki/Trivial_File_Transfer_Protocol)。它设计为轻量且占用空间小，适用于资源受限的路由器和防火墙。还可以将 dnsmasq 配置为 DNS 缓存查询，以提高对以前访问过站点的 DNS 查找速度。

## 安装

安装 [dnsmasq](https://archlinux.org/packages/?name=dnsmasq)包 软件包，然后启动/启用 `dnsmasq.service`。

还需要重新启动网络，以便 DHCP 客户端可以创建新的 `/etc/resolv.conf`。

## 配置

要配置 dnsmasq，需要编辑 `/etc/dnsmasq.conf`。该文件包含选项的注释。有关全部可用选项，请参阅 [dnsmasq(8)](https://man.archlinux.org/man/dnsmasq.8)。

> **注意：**dnsmasq 的默认配置启用 DNS 服务器。如果不使用的话，需要显式设置 `port=0` 来禁用它。
>
> 如果 dnsmasq 不用作本地 DNS 解析程序，还需要 编辑 `dnsmasq.service`，使其不唤起 `nss-lookup.target`：
>
> ```
> /etc/systemd/system/dnsmasq.service.d/no-nss-lookup-target.conf
> ```
>
> ```
> [Unit]
> Wants=
> ```

> **提示：**要检查配置文件语法，请执行：
>
> ```
> $ dnsmasq --test
> ```

### DNS 服务器

要在单台计算机上将 dnsmasq 设置为 DNS 缓存守护程序，请指定 `listen-address` 指令，添加本地主机 IP 地址：

```
listen-address=::1,127.0.0.1
```

使用此计算机在其 LAN IP 地址上侦听网络上的其他计算机，建议使用静态 LAN IP。例如：

```
listen-address=::1,127.0.0.1,192.168.1.1
```

或者，可以指定网络接口：

```
interface=enp5s0
```

使用 `cache-size=*size*` 设置缓存域名的数量（默认值为 `150`）：

```
cache-size=10000
```

要验证 DNSSEC，请加载 [dnsmasq](https://archlinux.org/packages/?name=dnsmasq)包 软件包提供的 DNSSEC 信任锚，并设置选项 `dnssec`：

```
conf-file=/usr/share/dnsmasq/trust-anchors.conf
dnssec
```

要了解更多选项，请参阅 [dnsmasq(8)](https://man.archlinux.org/man/dnsmasq.8)。

#### DNS 地址文件和转发

配置 dnsmasq 后，需要将本地主机地址添加为 `/etc/resolv.conf` 中的唯一名称服务器。这会导致所有查询都发送到 dnsmasq。

由于 dnsmasq 是存根解析器而不是递归解析器，因此必须设置转发到外部 DNS 服务器。可以通过 openresolv 自动完成或在 dnsmasq 的配置中手动指定 DNS 服务器完成。

##### openresolv

如果网络管理器支持 *resolvconf* 而不是直接更改 `/etc/resolv.conf`，可以使用 openresolv [生成 dnsmasq 的配置文件](https://roy.marples.name/projects/openresolv/configuration/)。

编辑 `/etc/resolvconf.conf` 并将 loopback 地址添加为名称服务器，然后配置 openresolv 输出 dnsmasq 配置：

```
/etc/resolvconf.conf
```

```
# 使用本地名称服务器
name_servers="::1 127.0.0.1"
resolv_conf_options="trust-ad"

# 输出 dnsmasq 扩展配置和解析文件
dnsmasq_conf=/etc/dnsmasq-conf.conf
dnsmasq_resolv=/etc/dnsmasq-resolv.conf
```

运行 `resolvconf -u` 创建配置文件。如果文件不存在，则 `dnsmasq.service` 无法启动。

编辑 dnsmasq 的配置文件使用 openresolv 生成的配置 ：

```
# 读取 openresolv 生成的配置文件
conf-file=/etc/dnsmasq-conf.conf
resolv-file=/etc/dnsmasq-resolv.conf
```

##### 手动转发

首先，必须将本地主机地址设置为 `/etc/resolv.conf` 中的唯一名称服务器：

```
/etc/resolv.conf
```

```
nameserver ::1
nameserver 127.0.0.1
options trust-ad
```

确保 `/etc/resolv.conf` 不被修改，详述见 [Domain name resolution#Overwriting of /etc/resolv.conf](../DNS/域名解析.md#Overwriting_of_/etc/resolv.conf)。

或者，可以配置 [NetworkManager](../../06_软件与应用/红帽/NetworkManager.md) 自动生成特定连接的 `/etc/resolv.conf` 文件，使用以下命令：

```
$ nmcli connection modify 'connection-name' ipv4.dns 127.0.0.1
$ nmcli connection modify 'connection-name' ipv4.dns-options trust-ad
$ nmcli connection modify 'connection-name' ipv4.ignore-auto-dns yes
$ nmcli connection modify 'connection-name' ipv6.dns ::1
$ nmcli connection modify 'connection-name' ipv6.dns-options trust-ad
$ nmcli connection modify 'connection-name' ipv6.ignore-auto-dns yes
```

然后重启 `NetworkManager.service`。

然后，必须在 dnsmasq 的配置文件中设置 `server=*server_address*` 指定上游 DNS 服务器地址。还要添加 `no-resolv`，以便 dnsmasq 不会非必要地读取只包含本地主机地址的 `/etc/resolv.conf`。

```
/etc/dnsmasq.conf
```

```
[...]
no-resolv

# 示例：Google 的名称服务器
server=8.8.8.8
server=8.8.4.4
```

现在，DNS 查询将使用 dnsmasq 解析，仅在缓存查询无结果时才会从外部服务器查询。

#### 添加自定域

可以通过以下方式为路由器分配域名：

```
address=/router/192.168.1.1
```

或者，继续为（本地）网络中的主机添加自定域：

```
local=/lan/
domain=lan
```

在此示例中，可以 ping （在 `/etc/hosts` 文件中定义的）主机/设备为 `*hostname*.lan`。

取消注释 `expand-hosts` 将自定域添加到主机条目：

```
expand-hosts
```

如果没有此设置，则必须将域添加到 `/etc/hosts` 的条目中。

#### 测试

要执行查找速度测试，请选择自 dnsmasq 启动以来未访问过的网站（*drill* 是 [ldns](https://archlinux.org/packages/?name=ldns)包 软件包的一部分）：

```
$ drill archlinux.org | grep "Query time"
```

再次运行该命令将使用缓存的 DNS IP，如果正确设置了 dnsmasq，则查找时间会缩短：

```
$ drill archlinux.org | grep "Query time"
```

```
;; Query time: 18 msec
```

```
$ drill archlinux.org | grep "Query time"
```

```
;; Query time: 2 msec
```

若要测试 DNSSEC 验证是否正常工作，请参阅 DNSSEC#Testing。

### DHCP 服务器


默认情况下，dnsmasq 关闭了 DHCP 功能，如要使用则必须将其打开。以下是重要的设置：

```
# 仅侦听路由器的 LAN NIC。这样会将 tcp/udp 端口 53 开放给本地主机，并将 udp 端口 67 开放给全世界：
interface=enp0s0

# dnsmasq 将向全世界开放 tcp/udp 端口 53 和 udp 端口 67，以帮助动态接口（分配动态 IP）。
# dnsmasq 将丢弃全部请求，但某些人可能希望关闭它并由内核处理。
# 如果您有其他 dnsmasq 实例在运行（例如由于 libvirtd），您可能也需要此选项。
bind-interfaces

# 设置域名（可选）
domain=example.org

# 设置默认网关
dhcp-option=3,0.0.0.0

# 设置要公布的 DNS 服务器
dhcp-option=6,0.0.0.0

# 如果 dnsmasq 服务器同时也为网络执行路由，则可以使用选项 121 推出静态路由。
# x.x.x.x 是目标 LAN，yy 是 CIDR 表示法（通常为 /24），z.z.z.z 是执行路由的主机。
dhcp-option=121,x.x.x.x/yy,z.z.z.z

# 提供给 LAN PC 的 IP 动态范围和租赁时间。
# 建议首先将租赁时间设置为 5m，以便测试一切正常之后再设置持久记录。
# 这里的地址范围必须位于分配给虚拟接口的地址范围内。
dhcp-range=192.168.111.50,192.168.111.100,12h

# 提供 IPv6 DHCP 租约，使用网络接口作为前缀构建范围
dhcp-range=::f,::ff,constructor:enp0s0

# 如果要让 dnsmasq 将固定 IP 分配给某些客户端，请绑定 LAN 计算机的 NIC MAC 地址：
dhcp-host=aa:bb:cc:dd:ee:ff,192.168.111.50
dhcp-host=aa:bb:cc:ff:dd:ee,192.168.111.51
```

更多选项请参阅 [dnsmasq(8)](https://man.archlinux.org/man/dnsmasq.8)。

#### Proxy DHCP

如果网络上已经运行了 DHCP 服务器并且您希望与其互操作，可以将 dnsmasq 设置为“代理 DHCP”，因此仅向客户端提供 [#PXE 服务器](#PXE_服务器)特定信息。此模式仅适用于 IPv4。使用以下语法，提供现有 DHCP 服务器地址：

```
dhcp-range=192.168.0.1,proxy
```

#### 测试

从连接到运行 dnsmasq 的计算机上，将其配置为使用 DHCP 自动分配 IP 地址，然后尝试正常登录网络。

如果您检查服务器上的 `/var/lib/misc/dnsmasq.leases` 文件，您应该能够看到租约。

### TFTP 服务器

dnsmasq 内置了 TFTP 服务器。

要使用它，请为 TFTP 创建一个根目录（例如 `/srv/tftp`）以放置可传输文件。

```
enable-tftp
tftp-root=/srv/tftp
```

为了增加安全性，建议使用 dnsmasq 的 TFTP 安全模式。在安全模式下，只有 `dnsmasq` 用户拥有的文件才会通过 TFTP 提供。您需要 [chown](../../07_开发与工具/命令行/文件权限与属性.md) TFTP 根目录及其中的所有文件为 `dnsmasq` 用户才能使用此功能。

```
tftp-secure
```

有关更多选项，请参阅 [dnsmasq(8)](https://man.archlinux.org/man/dnsmasq.8)。

### PXE 服务器

PXE 需要 DHCP 和 TFTP 服务器；两者都可以由 dnsmasq 提供。要设置 PXE 服务器，请按照以下步骤操作：

1.  在 dnsmasq 配置文件中设置 [#TFTP 服务器](#TFTP_服务器)和 [#DHCP 服务器](#DHCP_服务器)（完整 DHCP 或代理模式），
2.  复制并配置 PXE 兼容的引导加载程序（例如 [PXELINUX](../../01_安装与包管理/启动加载程序/Syslinux.md)）到 TFTP 根目录，
3.  在 dnsmasq 配置文件中启用 PXE：

要简单发送一个文件：

```
dhcp-boot=lpxelinux.0
```

要根据客户端架构发送文件：

```
pxe-service=x86PC,"PXELINUX (BIOS)",bios/lpxelinux
pxe-service=X86-64_EFI,"PXELINUX (EFI)",efi64/syslinux.efi
```

> **注意：**
>
> - 文件路径相对于 TFTP 根路径
> - 如果文件有 *.0* 后缀，您必须在 `pxe-service` 选项中排除后缀

如果 `pxe-service` 无法识别架构（特别是对于基于 UEFI 的客户端），可以使用 `dhcp-match` 和 `dhcp-boot` 的组合。有关更多 `client-arch` 编号，请参阅 [RFC 4578 2.1](https://tools.ietf.org/html/rfc4578#section-2.1) 以用于 dhcp 引导协议。

```
dhcp-match=set:efi-x86_64,option:client-arch,7
dhcp-match=set:efi-x86_64,option:client-arch,9
dhcp-match=set:efi-x86,option:client-arch,6
dhcp-match=set:bios,option:client-arch,0
dhcp-boot=tag:efi-x86_64,efi64/syslinux.efi
dhcp-boot=tag:efi-x86,efi32/syslinux.efi
dhcp-boot=tag:bios,bios/lpxelinux.0
```

有关更多选项，请参阅 [dnsmasq(8)](https://man.archlinux.org/man/dnsmasq.8)。

其余部分取决于[引导加载程序](../../01_安装与包管理/启动过程/Arch%20的启动流程.md)。

## 提示和技巧

### 防止 OpenDNS 重定向 Google 查询

要防止 OpenDNS 将所有 Google 查询重定向到其自己的搜索服务器，请添加到 `/etc/dnsmasq.conf`：

```
server=/www.google.com/<ISP DNS IP>
```

### 覆盖地址

在某些情况下，例如操作强制门户时，将特定域名解析为硬编码的地址集可能很有用。这可以通过 `address` 配置完成：

```
address=/example.com/1.2.3.4
```

此外，可以通过使用特殊通配符为所有未从 `/etc/hosts` 或 DHCP 回答的域名返回特定地址：

```
address=/#/1.2.3.4
```

### 多个实例

如果我们希望每个接口运行两个或更多 dnsmasq 服务器。

#### 静态

要静态执行此操作，每个接口使用一个服务器，请使用 `interface` 和 `bind-interfaces` 选项。这将强制启动第二个 dnsmasq。

#### 动态

在这种情况下，我们可以排除每个接口并绑定任何其他接口：

```
except-interface=lo
bind-dynamic
```

> **注意：**这是 [libvirt](../../02_系统管理与内核/虚拟化/Libvirt.md) 中的默认设置。

### 域名阻止列表

要阻止域名，即使用 NXDOMAIN 回答查询，请使用 `address` 选项而不指定 IP 地址：

```
address=/blocked.example/
address=/anotherblocked.example/
```

> **注意：**与 `/etc/hosts` 文件不同，dnsmasq 将阻止这些域名及其所有子域名，例如 *subdomain.blocked.example*。

还支持通配符。在模式的开头添加 `*`：

```
# 阻止 blocked.example 和 anotherblocked.example 及其所有子域名
address=/*blocked.example/

# 阻止像 mail.google.com 这样的子域名，但不阻止 google.com
address=/*.google.com/
```

可以使用 `#` 作为服务器地址来解除阻止某些特定子域名：

```
# 阻止 google.com 及其所有子域名，除了 mail.google.com。
address=/google.com/
server=/mail.google.com/#
```

> **注意：**
>
> - 选项 `address=/example.com/` 和 `server=/example.com/` 是等效的。两者都将使用 NXDOMAIN 回答查询。
> - 选项 `address=/example.com/#` 和 `server=/example.com/#` 不等效。
> - `address=/example.com/#` 将使用 NULL 地址（IPv6 为 0.0.0.0 或 ::）回答域名的查询。
> - `server=/example.com/#` 将域名的查询发送到标准配置的服务器。
> - 模式 `/example.com/` 和 `/.example.com/` 是等效的。两者都将匹配 *example.com* 及其所有子域名。

为了方便使用，将阻止列表放在单独的文件中，例如 `/etc/dnsmasq.d/blocklist.conf`，并从 `/etc/dnsmasq.conf` 加载它，使用 `conf-file=/etc/dnsmasq.d/blocklist.conf` 或 `conf-dir=/etc/dnsmasq.d/,*.conf`。

> **提示：**
>
> - 可以在 [OpenWrt 的 adblock 包的 README](https://github.com/openwrt/packages/blob/master/net/adblock/files/README.md) 中找到阻止列表的潜在来源列表。
> - 可以使用 `addn-hosts=hosts.txt` 选项使用 hosts 文件阻止列表，或者可以使用此 awk 命令将其转换为 dnsmasq 阻止列表：`awk '/^[^#]/ { print "address=/"$2"/"$1"" }' hosts.txt`。

### 查看缓存统计信息

可以使用 chaos 请求查询缓存统计信息，使用 [ldns](https://archlinux.org/packages/?name=ldns)包 软件包中的 `drill` 实用程序：

```
$ drill misses.bind TXT CH
$ drill hits.bind TXT CH
```

输出将分别包含缓存未命中和命中的数量：

```
;; ANSWER SECTION:
misses.bind.    0       CH      TXT     "411"
```

其他选项包括 `cachesize.bind`、`insertions.bind`、`evictions.bind`、`auth.bind` 和 `servers.bind`。

## 参阅

- [使用 dnsmasq 的缓存名称服务器，以及其他一些提示和技巧。](https://www.g-loaded.eu/2010/09/18/caching-nameserver-using-dnsmasq/)
