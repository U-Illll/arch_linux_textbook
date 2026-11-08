# 第 9 章　VPN

> 本章解决的是让一台机器既能充当 VPN 服务端供别人接入，也能作为客户端连出去：装软件、备好密钥或证书、配好路由、放行防火墙、启动服务，每一步都得对得上。建议先读《OpenVPN》与《WireGuard》：一个从内核配置、自建 PKI 走到三层 IP 路由，一个从密钥对走到服务端配置与疑难解答，读透其一其余可自行迁移；《Linux 容器/使用 VPN》在容器网络里必读；《StrongSwan》重在证书与多种 VPN 模式，按需查阅；《PPTP server》与《PPTP Client》最短，只讲安装、配置、建立与断开连接，快读即可。
> 
> 最容易踩的坑是把装上软件当成已经连通，或把服务端与客户端的配置混在一起：证书与 Secrets 对不上、路由没配、防火墙没放行、容器里还要单独配防火墙，症状常常只是连不上。

<!-- 本章 6 节，共 80 KB -->

## 9.1　PPTP server

*难度：入门 ｜ 篇幅：3.6 KB ｜ 所属：VPN*

将要讲到：安装、配置、启动

点对点隧道协议(PPTP)是一种实现虚拟专用网(VPN)的方法。PPTP 在TCP之上使用一个控制通道和 GRE 隧道操作加密 PPP 数据包。

本条目说明如何在 Arch 中创建 PPTP 服务器。

## 安装

[安装](../../01_安装与包管理/包管理器/Pacman.md)位于[官方软件仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)的软件包[pptpd](https://archlinux.org/packages/?name=pptpd)包。

## 配置

然后编辑文件`/etc/pptpd.conf`

```
/etc/pptpd.conf
```

```
option /etc/ppp/pptpd-options
localip 172.16.36.1
remoteip 172.16.36.2-254
```

接着编辑文件`/etc/ppp/pptpd-options`

```
/etc/ppp/pptpd-options
```

```
name pptpd
refuse-pap
refuse-chap
refuse-mschap
require-mschap-v2
require-mppe-128
proxyarp
lock
nobsdcomp
novj
novjccomp
nologfd
ms-dns 8.8.8.8
ms-dns 8.8.4.4
```

在 `/etc/ppp/chap-secrets`中添加用户名和密码：

```
/etc/ppp/chap-secrets
```

```
<username>     pptpd     <password>   *
```

在 `/etc/sysctl.d/99-sysctl.conf`中启用IP转发：

```
/etc/sysctl.d/99-sysctl.conf
```

```
net.ipv4.ip_forward=1
```

应用 sysctl.conf 修改：

```
# sysctl --system
```

### iptables 防火墙配置

配置 iptables 设置，允许 PPTP 客户端访问：

```
iptables -A INPUT -i ppp+ -j ACCEPT
iptables -A OUTPUT -o ppp+ -j ACCEPT

iptables -A INPUT -p tcp --dport 1723 -j ACCEPT
iptables -A INPUT -p 47 -j ACCEPT
iptables -A OUTPUT -p 47 -j ACCEPT

iptables -F FORWARD
iptables -A FORWARD -j ACCEPT

iptables -A POSTROUTING -t nat -o eth0 -j MASQUERADE
iptables -A POSTROUTING -t nat -o ppp+ -j MASQUERADE
```

使用下面命令保存新的 iptables 规则：

```
# rc.d save iptables
```

systemd 用户需要使用：

```
# iptables-save > /etc/iptables/iptables.rules
```

阅读 [Iptables](../防火墙/Iptables.md) 条目获得更多信息。

### ufw 防火墙配置

在 `/etc/default/ufw`中，修改默认的转发规则

```
/etc/default/ufw
```

```
DEFAULT_FORWARD_POLICY=”ACCEPT”
```

修改`/etc/ufw/before.rules`，添加如下配置到 \*filter 之前

```
/etc/ufw/before.rules
```

```
# nat Table rules
*nat
:POSTROUTING ACCEPT [0:0]

# Allow traffic from clients to eth0
-A POSTROUTING -s 172.16.36.0/24 -o eth0 -j MASQUERADE

# don.t delete the .COMMIT. line or these nat table rules won.t be processed
COMMIT
```

代开端口 1723：

```
ufw allow 1723
```

重启ufw

```
ufw disable
ufw enable
```

## 启动

pptpd 软件包已经提供了 service 文件.

启动服务：

```
# systemctl start pptpd.service
```

开机自动启动：

```
# systemctl enable pptpd.service
```

## 9.2　PPTP Client

*难度：进阶 ｜ 篇幅：6.3 KB ｜ 所属：VPN*

将要讲到：安装PPTPClient、配置、创建你的连接、断开连接、把一个VPN连接配为默认启动、注意

> [![](https://wiki.archlinuxcn.org/wzh/images/2/26/Tango-preferences-desktop-locale-modified.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale-modified.png)**这篇文章或章节的[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing#Translating)质量不佳。**
>
> **原因：**Last updated in 2011, out of sync with English page（在 [Talk:PPTP Client#](https://wiki.archlinuxcn.org/wiki/Talk:PPTP_Client) 中讨论）

pptpclient是一个实现Microsoft PPTP协议的程序。因此它能够被用来接入另一个Microsoft VPN网络，比如学校和单位。

## 安装PPTPClient

pptpclient由安装包pptpclient提供，运行下列命令可以安装：

```
# pacman -S pptpclient
```

## 配置

你需要从网络管理员获取以下信息来配置pptpclient:

- VPN服务器的ip或者域名
- VPN隧道名称
- Windows域（不是所有网络都需要）
- VPN用户名
- VPN密码

### 编辑配置文件

用你称手的编辑器打开/etc/ppp/options.pptp。这个文件为你的VPN连接启用了一系列默认安全设置。如果你连接时候出现问题，你可以自定义配置。你的options.pptp文件最少需要包含以下内容：

```
lock
noauth
nobsdcomp
nodeflate
```

### 编辑密码文件

下一步，打开或者创建/etc/ppp/chap-secrets。我们将在这个文件里面存储你的密码，记得修改权限让除root之外所有用户不能访问它。这个文件的格式如下：

```
<DOMAIN>\\<USERNAME> PPTP <PASSWORD> *
```

如果你的服务器不要求域，则配置如下：

```
<USERNAME> PPTP <PASSWORD> *
```

替换掉上文中范例中的占位符。注意，如果你的密码包含特殊字符，比如“$”，你需要用双引号把它们包起来。

### 命名你的VPN隧道

用你称手的编辑器创建类似/etc/ppp/peers/<TUNNEL>的文件，把<TUNNEL>这里替换成你的VPN连接名。这个文件设置之后看起来如下：

```
pty "pptp <SERVER> --nolaunchpppd"
name <DOMAIN>\\<USERNAME>
remotename PPTP
require-mppe-128
file /etc/ppp/options.pptp
ipparam <TUNNEL>
```

> **注意：**跟刚才一样，如果你的连接不要求域，忽略范例中的"<DOMAIN>\\\\"

> **注意：**PPTP远程主机使用Chap-Secrets文件中的<PASSWORD>

<SERVER>是VPN服务器的地址，<DOMAIN>是你所属的域，<USERNAME>是你将要用来连接服务器的用户名，<TUNNEL>是连接的名称。

> **注意：**如果你不需要使用MPPE，你应当从/etc/ppp/options.pptp中移除require-mppe-128这个选项

## 创建你的连接

用root执行以下命令来确保配置是正确的：

```
# pon $TUNNEL debug dump logfd 2 nodetach
```

如果一切都配置好了，pon命令应当不会自动结束。一旦你感觉差不多OK了，就可以终止这个命令。

> **注意：**另一个用来确保配置正确的命令是ifconfig -a，看看里面时候有一个名叫ppp0的新驱动，并且还是可用的

执行以下命令来连接VPN隧道：

```
# pon <TUNNEL>
```

<TUNNEL>是你之前命名过的VPN隧道名称。注意使用root命令执行。

### 配置路由

一旦你成功连接上VPN，你就可以和VPN服务器交互了。当然在此之前，咱们需要添加一个新的路由到你的路由表，从而可以接入远程网络。

> **注意：**根据你的环境配置，你可能需要每次都重复添加路由信息

你可以阅读[PPTP Routing Howto](http://pptpclient.sourceforge.net/routing.phtml)来获得更多如何添加路由的信息，里面还有很多范例。

#### 选择路由

对我来说，只有传输到VPN网络的数据包才应该走VPN连接，所以我添加如下路由条目：

```
# ip route add 192.168.10.0/24 dev ppp0
```

这将路由所有目的地址为191.168.10.xxx的数据到VPN连接。

#### 配置为默认路由

如果你想要所有数据从VPN连接走，下面这条命令包你爽：

```
# ip route add default dev ppp0
```

> **注意：**所有数据从VPN连接走的话会比正常连接慢一些

#### 使用ip-up.d设置默认路由

> **注意：**/etc/ppp/ip-up.d/下所有脚本将在VPN连接建立的时候自动执行

```
#!/bin/bash

# This script is called with the following arguments:
# Arg Name
# $1 接口名字
# $2 The tty
# $3 连接速度
# $4 本地ip地址
# $5 Peer IP number
# $6 可选``ipparam 的参数

route add default gw $4
```

把这个脚本命名为01-routes.sh,然后放在/etc/ppp/ip-up.d/下面。

## 断开连接

下面这条命令用来断开VPN连接：

```
# poff <TUNNEL>
```

<TUNNEL>是你VPN连接的名称。

## 把一个VPN连接配为默认启动

你可以在rc.d创建一个快捷命令来实现自动在后台连接VPN网络。

> **注意：**和平常一样，<TUNNEL>是你隧道的名字，<ROUTING COMMAND>是你加入路由表的命令。

```
#!/bin/bash

. /etc/rc.conf
. /etc/rc.d/functions

DAEMON=<TUNNEL>-vpn
ARGS=

[ -r /etc/conf.d/$DAEMON ] && . /etc/conf.d/$DAEMON

case "$1" in
 start)
   stat_busy "Starting $DAEMON"
   pon <TUNNEL> updetach persist &> /dev/null && <ROUTING COMMAND> &>/dev/null
   if [ $? = 0 ]; then
     add_daemon $DAEMON
     stat_done
   else
     stat_fail
     exit 1
   fi
   ;;
 stop)
   stat_busy "Stopping $DAEMON"
   poff MST &>/dev/null
   if [ $? = 0 ]; then
     rm_daemon $DAEMON
     stat_done
   else
     stat_fail
     exit 1
   fi
   ;;
 restart)
   $0 stop
   sleep 1
   $0 start
   ;;
 *)
   echo "usage: $0 {start|stop|restart}"
esac
```

注意，我们可以使用updetach和persist这两个附加命令在pon上。updetach保证pon阻塞知道连接被建立。另外一个命令persist保证网络自动重练。如果需要开机自动启动，则添加@<TUNNEL>-vpn到rc.conf的DAEMONS中去。

## 注意

你可以在[pptpclient website](http://pptpclient.sourceforge.net/)查到更多关于pptpclient的配置信息。Ubuntu的帮助手册也有一些帮助你配置的信息。这些范例能够很轻松的稍加变换从而添加到daemons中去，从而帮助你自动化运行。

## 9.3　Linux 容器/使用 VPN

*难度：进阶 ｜ 篇幅：9.5 KB ｜ 所属：VPN*

将要讲到：配置容器、服务器模式的OpenVPN、客户端模式的 OpenVPN、for OpenVPN、WireGuard、容器内的防火墙配置

本文介绍如何设置 [Linux_容器](../../08_安全与加密/沙箱/Linux%20容器.md)来运行多个带有“终止开关”的 VPN 协议，以实现安全/隐私的互联网使用。 与使用 [VirtualBox](../../06_软件与应用/Oracle/VirtualBox.md) 或者 [QEMU](../../02_系统管理与内核/虚拟机管理/QEMU.md) 等全套的虚拟化相比， 这样做提供了更小的资源开销和在低功耗设备上运行等明显优势。

## 配置容器

需要对 [Linux_容器](../../08_安全与加密/沙箱/Linux%20容器.md)进行基本配置和了解。 本文假设读者已经设置了可运行的基本LXC。

## 服务器模式的OpenVPN

本小节详细介绍了在容器中提供 OpenVPN 服务所需的一些额外设置。 想要使用给出的 OpenVPN 配置文件的用户无需阅读本小节。

### 主机设置

1.  主机操作系统需要网桥设置才能允许容器运行。请参考 [Linux_容器#宿主机网络配置](../../08_安全与加密/沙箱/Linux%20容器.md#宿主机网络配置)。
2.  需要启用数据包转发。请参考[网络分享#启用包转发](../网络共享/网络分享.md#启用包转发)。
3.  尽管不是必须的，但强烈建议使用防火墙。

## 客户端模式的 OpenVPN

需要修改容器的配置才能使用 OpenVPN，如下所示：

```
/var/lib/lxc/playtime/config
```

```
...

## for OpenVPN
lxc.mount.entry = /dev/net dev/net none bind,create=dir
lxc.cgroup2.devices.allow = c 10:200 rwm
```

安装 [openvpn](https://archlinux.org/packages/?name=openvpn)包. 如果使用容器连接到第三方 VPN 提供商，只需将配置文件 `foo.conf` 放在 `/etc/openvpn/client/foo.conf` 即可。 To v要验证容器内的 OpenVPN 功能，请启动 `openvpn-client@foo.service` 来开启OpenVPN。一旦对当前配置感到满意，启用服务来实现系统启动后自动开启。

有关其他用例和设置，请参阅 [OpenVPN](OpenVPN.md)。

> **注意：**在“非特权”容器中运行 OpenVPN 的用户需要创建一个自定义 systemd 单元才能在容器中启动它。在[替换单元文件](../../01_安装与包管理/初始化/Systemd.md#替换单元文件)中注释掉以下开头的行：`LimitNPROC...`

## WireGuard

安装[wireguard-tools](https://archlinux.org/packages/?name=wireguard-tools)包。用户将拥有由第三方 VPN 服务提供的 WireGuard 配置，或者自己设置好的WireGuard 对应的配置。 如果使用容器连接到 VPN 提供商， 只需将配置文件 `foo.conf` 放入 `/etc/wireguard/` 。

要验证容器内的 WireGuard 功能，请通过启动 `wg-quick@foo.service` 来开启WireGuard。一旦对当前配置感到满意，启用服务来实现系统启动后自动开启。

有关其他用例和设置，请参阅 [WireGuard](WireGuard.md)。

## 容器内的防火墙配置

*强烈*建议在容器内运行正确配置的防火墙。容器内防火墙的作用有两个：

1.  提供“终止开关”，以便在 VPN 连接失败时维护隐私。
2.  把不想要东西拒之门外。

本指南使用易于配置的 [ufw](https://archlinux.org/packages/?name=ufw)包，当然也可以使用其他防火墙。

> **提示：**要完全重置 ufw 的配置文件，使用reset: `ufw reset`.

设置“终止开关”的方法是通过设置一个拒绝政策然后只允许VPN设备上的特定服务和流量。这样，如果该设备的连接中断时不会出现本地政策回退。

> **注意：**下面显示的方法的一个限制是 VPN 配置文件不得使用 www.myvpn.com 等域名，它们需要使用相应的 IP 地址。如上所述，当 VPN 未连接时，容器 DNS 解析将被禁用。因此，为了连接，必须提供数字 IP。

编辑 `/etc/default/ufw` 并将 DEFAULT_OUTPUT_POLICY 从 "ACCEPT" 更改为 "DROP":

```
/etc/default/ufw
```

```
DEFAULT_OUTPUT_POLICY="DROP"
```

> **注意：**以下命令调用 `ufw` 需要以root用户执行； 按照 wiki 标准命令的前缀“#”符号已被省略，以允许干净地复制/粘贴到终端中。

设置拒绝政策：

```
ufw default deny outgoing
ufw default deny incoming
```

（可选）添加例如`/etc/ufw/applications.d/custom`等文件中定义的任何预定义或自定义规则：

```
ufw allow ssh
ufw allow from my-custom-app1
ufw allow from my-custom-app2
```

（可选）进一步限制来自内部 LAN IP 范围甚至单个 IP 地址的访问：

```
ufw allow from 192.168.1.0/24
```

WireGuard 用户将拥有一个与相应配置文件，比如`/etc/wireguard/foo.conf`，同名的接口，而 OpenVPN 用户很可能使用`tun0`. 在下面的命令中，将“foo”替换为 WireGuard 配置文件的名称（省略 .conf 后缀），或者如果使用 OpenVPN，则将“foo”替换为 tun0 或正在使用的任何设备：

```
ufw allow out on foo from any to any
```

最后，打开 VPN 提供商的 IP 地址和端口，并定义相对应的协议。在下面的行中，需要考虑三个变量：

- “xxx”代表 WireGuard 的 peer 或者 OpenVPN 的服务器的IP地址。它将在 VPN 提供商提供的相应配置文件中。
- “yyy”代表要进行通信的端口。同样，这将位于配置文件中。
- “zzz” 代表要使用的协议，可以从 udp 或 tcp 中选择。请注意，WireGuard 仅支持 udp，而 OpenVPN 两者都支持。

```
ufw allow out to xxx port yyy proto zzz
```

> **注意：**如果预计使用多个服务器，请对 VPN 提供商定义的每个 IP 地址 (xxx) 重复此操作。

启动 ufw 并启用 `ufw.service` 以实现开机启动。

### 在配置文件中使用 VPN 域名的一个非正式方法

如果需要在 VPN 配置文件中使用域名，主机上的 shell 脚本可以将其预先解析为数字 IP，然后通过将该 IP 地址存储在写在容器中文件的一个变量来将该 IP 地址传递给容器。该文件又可以由修改后的 VPN systemd 服务读取。 它可以用, 但不正规。

编辑两个变量以匹配相对应的容器名称和服务器名称：

#### 在主机上

安装 [bind](https://archlinux.org/packages/?name=bind)包 (dig 需要)并创建以下脚本：

```
/path/to/container-start.sh
```

```
#!/bin/bash
# this script should be called as root
container=foo
server=www.myvpnserver.org

if ! systemctl is-active lxc@"$container" &>/dev/null; then
  ToUse=$(dig +short "$server")
  [[ -d /var/lib/lxc/$container/rootfs/etc/conf.d ]] || mkdir -p /var/lib/lxc/$container/rootfs/etc/conf.d
  echo "SERVER=$ToUse" > /var/lib/lxc/$container/rootfs/etc/conf.d/server.hack.txt
  systemctl start lxc@"$container"
fi
```

从现在开始，调用该脚本来启动容器。它将使用 dig 从域名中获取 IP 地址，然后启动容器。

#### 从容器内部

修改启动 VPN 的 systemd 服务并创建一个框架配置文件，该配置文件可以使用我们刚刚创建的脚本创建的`/var/lib/lxc/$container/rootfs/etc/conf.d/server.hack.txt` 中定义的 IP 地址进行修改。

要制作框架配置文件，只需将使用的配置文件重命名为另一个名称即可。

例如使用 WireGuard：

```
mv /etc/wireguard/foo.conf /etc/wireguard/foo.skel
```

现在编辑 `/etc/wireguard/foo.skel` 以将 **Endpoint = www.myvpnserver.org** 替换为**@@@**，例如：

```
Endpoint = @@@:51820
```

或者如果使用 OpenVPN：

```
mv /etc/openvpn/client/foo.conf /etc/openvpn/client/foo.skel
```

编辑 `/etc/openvpn/client/foo.skel` 以将远程**www.myvpnserver.org**替换为**@@@**，例如：

```
remote @@@
```

最后，创建一个[附加配置片段](../../01_安装与包管理/初始化/Systemd.md#附加配置片段), 以读取 IP 并将其替换为实际配置文件。

使用 WireGuard 的示例：

```
/etc/systemd/system/wg-quick@foo.service.d/override.conf
```

```
[Service]
EnvironmentFile=-/etc/conf.d/server.hack.txt
ExecStartPre=/bin/bash -ac "sed s/@@@/$SERVER/ </etc/wireguard/foo.skel >/etc/wireguard/foo.conf"
```

使用 OpenVPN 的示例：

```
/etc/systemd/system/openvpn-client@foo.service.d/override.conf
```

```
[Service]
EnvironmentFile=-/etc/conf.d/server.hack.txt
ExecStartPre=/bin/bash -ac "sed s/@@@/$SERVER/ </etc/openvpn/client/foo.skel >/etc/openvpn/client/foo.conf"
```

## 测试服务

在正在运行的容器内 (通过 ssh 或者 `lxc-attach -n playtime`) 通过将浏览器导出到主机的计算机 X server 来测试设置:

```
$ DISPLAY=:0 firefox
```

> **提示：**通过 ssh 连接需要允许本地显示接受连接。执行此操作 `xhost +SI:localuser:yourusername` 然后通过 ssh 连接到容器。

结果应该是主机的 X server 中出现一个标题为"Mozilla Firefox (playtime)" 的 Firefox 窗口。许多网站可用于验证 IP 地址和 DNS 的状态，例如 [ipleak dot net](https://ipleak.net).

此时，仅应显示与配置文件中定义的条目相对应的 DNS 条目。

## 9.4　StrongSwan

*难度：进阶 ｜ 篇幅：12.0 KB ｜ 所属：VPN*

将要讲到：安装、证书、不同的 VPN 模式、Secrets、Networking、Troubleshooting

IPSec 是一个加密和认证标准，可以组建安全虚拟专用网路(VPN).

Linux kernel 已经原生支持这个标准，但是用户需要配置加密密钥。IPSec VPN 使用 [IKE](https://en.wikipedia.org/wiki/Internet_Key_Exchange) 协议自动协商密钥的交换，交换方式包括认证、提前共享或同时使用。

通常用服务器上的一个后台进程实现，[strongSwan](https://strongswan.org/) 是一个完全支持 IKEv1 和 IKEv2 的 IKE 后台进程。大部分现代的客户端都支持，包括 Linux, Windows 7, Apple iOS, Mac OSX, FreeBSD and BlackBerry OS.

## 安装

安装软件包 [strongswan](https://archlinux.org/packages/?name=strongswan)包。

## 证书

第一步是生成 X.509 认证，包括一个认证机构(CA)证书，一个服务器证书和至少一个客户端证书。

### 认证机构

首先创建一个自己签名的根 CA 证书:

生成 4096 位 RSA 私钥 `strongswanKey.pem`:

```
$ cd /etc/ipsec.d/
$ ipsec pki --gen --type rsa --size 4096 --outform pem > private/strongswanKey.pem
$ chmod 600 private/strongswanKey.pem
```

生成自签名的 CA 证书 `strongswanCert.pem`，期限是 10 年(3650天),用 PEM 编码格式保存：

```
$ ipsec pki --self --ca --lifetime 3650 --in private/strongswanKey.pem --type rsa --dn "C=CH, O=strongSwan, CN=strongSwan Root CA" \
	   --outform pem > cacerts/strongswanCert.pem
```

可以根据需要修改 Distinguished Name (DN)，country (C), organization (O), and common name (CN)。

通过下面命令查看新生成的证书：

```
$ ipsec pki --print --in cacerts/strongswanCert.pem
```

输出:

```
cert:      X509
subject:  "C=CH, O=strongSwan, CN=strongSwan Root CA"
issuer:   "C=CH, O=strongSwan, CN=strongSwan Root CA"
validity:  not before Nov 22 11:55:41 2013, ok
           not after  Nov 20 11:55:41 2023, ok (expires in 3649 days)
serial:    65:39:93:df:a0:f8:40:03
flags:     CA CRLSign self-signed
authkeyId: 45:30:11:da:a4:0e:0b:0a:a3:41:a5:81:41:ab:d8:04:7a:40:6c:c0
subjkeyId: 45:30:11:da:a4:0e:0b:0a:a3:41:a5:81:41:ab:d8:04:7a:40:6c:c0
pubkey:    RSA 4096 bits
keyid:     dc:15:91:95:04:07:a5:13:69:5f:77:65:26:d7:02:3f:60:ec:73:c8
subjkey:   45:30:11:da:a4:0e:0b:0a:a3:41:a5:81:41:ab:d8:04:7a:40:6c:c0
```

> **警告：**CA 的私钥 `/etc/ipsec.d/private/strongswanKey.pem` 必须保存在安全位置，最好放在一个没有网络访问的签名专用服务器。如果此密钥泄露，整个公钥系统就土崩瓦解了。

### 主机证书

此证书被用来认证 VPN 服务器，运行下面命令：

生成 2048 位 RSA 私钥：

```
$ cd /etc/ipsec.d/
$ ipsec pki --gen --type rsa --size 2048 --outform pem > private/vpnHostKey.pem
$ chmod 600 private/vpnHostKey.pem
```

导出公钥并生成被 CA 签名的 `vpnHostCert.pem`，证书有效期是两年(730天),使用完整域名 `vpn.example.com` 作为身份

```
$ ipsec pki --pub --in private/vpnHostKey.pem --type rsa | \
	 ipsec pki --issue --lifetime 730 \
	  --cacert cacerts/strongswanCert.pem \
	  --cakey private/strongswanKey.pem \
	  --dn "C=CH, O=strongSwan, CN=vpn.example.com" \
	  --san vpn.example.com \
	  --flag serverAuth --flag ikeIntermediate \
	  --outform pem > certs/vpnHostCert.pem
```

后面的客户端连接配置中，会使用 VPN 服务器的域名或 IP 地址，必须将其加入 Distinguished Name 或 Alternative Name (line11), 最好都包含。请注意把 vpn.example.com 修改为 VPN 的主机名 - 否则客户端和服务器的连接会失败。

> **注意：**如果要使用 Win7 内置的 VPN 客户端，必须按上面例子在主机证书中添加 `serverAuth` 标记。OS X 10.7.3 和更早的版本需要 `ikeIntermediate` 标记。这些标记不影响其它功能，可以放心使用。

查看新生成的证书：

```
$ ipsec pki --print --in certs/vpnHostCert.pem
```

Output:

```
cert:      X509
subject:  "C=CH, O=strongSwan, CN=vpn.example.com"
issuer:   "C=CH, O=strongSwan, CN=strongSwan Root CA"
validity:  not before Nov 22 21:16:51 2013, ok
           not after  Nov 22 21:16:51 2015, ok (expires in 729 days)
serial:    0c:05:d7:d5:57:0e:d9:48
altNames:  vpn.zeitgeist.se
flags:     serverAuth iKEIntermediate
authkeyId: 9b:57:35:fb:cd:9e:2d:20:37:1d:61:4c:e7:c4:5b:5e:dc:64:ad:fc
subjkeyId: 5f:12:c2:06:ee:2b:1e:cc:5f:78:54:ff:f0:f3:7b:a0:2b:c0:b4:d6
pubkey:    RSA 2048 bits
keyid:     6f:a7:99:60:27:27:09:96:02:c1:b9:d9:7d:c1:b0:10:e3:e1:d5:45
subjkey:   5f:12:c2:06:ee:2b:1e:cc:5f:78:54:ff:f0:f3:7b:a0:2b:c0:b4:d6
```

### 客户端证书

客户端需要个人证书才能使用 VPN. 和生成主机证书类似，只是这里用客户端的电子邮件而不是主机名:

生成 2048 位 RSA 私钥:

```
$ cd /etc/ipsec.d/
$ ipsec pki --gen --type rsa --size 2048 --outform pem > private/ClientKey.pem
$ chmod 600 private/ClientKey.pem
```

导出公钥并用 CA 签名，证书时间是两年。

```
$ ipsec pki --pub --in private/ClientKey.pem --type rsa | \
	 ipsec pki --issue --lifetime 730 \
	  --cacert cacerts/strongswanCert.pem \
	  --cakey private/strongswanKey.pem \
	  --dn "C=CH, O=strongSwan, CN=myself@example.com" \
	  --san myself@example.com \
	  --outform pem > certs/ClientCert.pem
```

将所有证书和密钥用密码打包成 PKCS#12 文件，以方便客户端使用。

```
$ openssl pkcs12 -export -inkey private/ClientKey.pem \
	  -in certs/ClientCert.pem -name "My own VPN client certificate" \
	  -certfile cacerts/strongswanCert.pem \
	  -caname "strongSwan Root CA" \
	  -out Client.p12
```

## 不同的 VPN 模式

最简单的配置方式是用隧道模式运行 IPSec。

### IPSec 隧道模式

可以在 `/etc/ipsec.conf` 找到 VPN 配置，下面配置包含了基本 VPN 服务器需要的选项：

```
/etc/ipsec.conf
```

```
# ipsec.conf - strongSwan IPsec configuration file
config setup

  # By default only one client can connect at the same time with an identical
  # certificate and/or password combination. Enable this option to disable
  # this behavior.
  # uniqueids=never

  # Slightly more verbose logging. Very useful for debugging.
  charondebug="cfg 2, dmn 2, ike 2, net 2"

# Default configuration options, used below if an option is not specified.
# See: https://wiki.strongswan.org/projects/strongswan/wiki/ConnSection
conn %default

  # Use IKEv2 by default
  keyexchange=ikev2

  # Prefer modern cipher suites that allow PFS (Perfect Forward Secrecy)
  ike=aes128-sha256-ecp256,aes256-sha384-ecp384,aes128-sha256-modp2048,aes128-sha1-modp2048,aes256-sha384-modp4096,aes256-sha256-modp4096,aes256-sha1-modp4096,aes128-sha256-modp1536,aes128-sha1-modp1536,aes256-sha384-modp2048,aes256-sha256-modp2048,aes256-sha1-modp2048,aes128-sha256-modp1024,aes128-sha1-modp1024,aes256-sha384-modp1536,aes256-sha256-modp1536,aes256-sha1-modp1536,aes256-sha384-modp1024,aes256-sha256-modp1024,aes256-sha1-modp1024!
  esp=aes128gcm16-ecp256,aes256gcm16-ecp384,aes128-sha256-ecp256,aes256-sha384-ecp384,aes128-sha256-modp2048,aes128-sha1-modp2048,aes256-sha384-modp4096,aes256-sha256-modp4096,aes256-sha1-modp4096,aes128-sha256-modp1536,aes128-sha1-modp1536,aes256-sha384-modp2048,aes256-sha256-modp2048,aes256-sha1-modp2048,aes128-sha256-modp1024,aes128-sha1-modp1024,aes256-sha384-modp1536,aes256-sha256-modp1536,aes256-sha1-modp1536,aes256-sha384-modp1024,aes256-sha256-modp1024,aes256-sha1-modp1024,aes128gcm16,aes256gcm16,aes128-sha256,aes128-sha1,aes256-sha384,aes256-sha256,aes256-sha1!

  # Dead Peer Discovery
  dpdaction=clear
  dpddelay=300s

  # Do not renegotiate a connection if it is about to expire
  rekey=no

  # Server side
  left=%any
  leftsubnet=0.0.0.0/0
  leftcert=vpnHostCert.pem

  # Client side
  right=%any
  rightdns=8.8.8.8,8.8.4.4
  rightsourceip=%dhcp

# IKEv2: Newer version of the IKE protocol
conn IPSec-IKEv2
  keyexchange=ikev2
  auto=add

# IKEv2-EAP
conn IPSec-IKEv2-EAP
  also="IPSec-IKEv2"
  rightauth=eap-mschapv2
  rightsendcert=never
  eap_identity=%any

# IKEv1 (Cisco-compatible version)
conn CiscoIPSec
  keyexchange=ikev1
  # forceencaps=yes
  rightauth=pubkey
  rightauth2=xauth
  auto=add
```

### IPSec in transport mode


### IPSec/L2TP

L2TP/IPsec VPN client setup 页面包含了如何建立一个连接到 IPSec/L2TP 服务器的客户端。这种 IPSec VPN 和纯 IPSec 相比，可以将隧道用于非 IP 包，缺点是需要运行一个额外的 L2TP 后台进程。


## Secrets

strongSwan 需要知道哪些客户端是允许连接到 VPN 的，通过 `/etc/ipsec.secrets` 文件进行配置：

```
/etc/ipsec.secrets
```

```
# This file holds shared secrets or RSA private keys for authentication.

# RSA private key for this host, authenticating it to any other host
# which knows the public part.  Suitable public keys, for ipsec.conf, DNS,
# or configuration of other implementations, can be extracted conveniently
# with "ipsec showhostkey".

: RSA ClientKey.pem
user1 : EAP "topsecretpassword"
user2 : XAUTH "evenmoretopsecretpassword"
```

当 strongSwan 运行时，如果编辑了 /etc/ipsec.secrets，需要重新加载：

```
$ ipsec rereadsecrets
```

## Networking

需要执行下面设备让 VPN 服务器正常的进行 VPN 隧道传输：

```
/etc/sysctl.d/10-net-forward.conf
```

```
# VPN
net.ipv4.ip_forward = 1
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0
```

上面的 VPN 配置用 DHCP 自动给客户端分配 IP 地址，所以需要一个 DHCP 服务，如果这个服务是运行在 strongSwan 相同的服务器上，需要编辑 `/etc/strongswan.d/charon/dhcp.conf`:

```
/etc/strongswan.d/charon/dhcp.conf
```

```
dhcp {
 force_server_address = yes
 server = 192.168.0.255
}
```

在防火墙中开放如下协议：

- ESP (Encrypted Secure Payload): Standard IPSec traffic
- UDP 4500: IPSec traffic in "NAT Traversal" mode
- UDP 500: Key exchanges (IKE)

最后，start 并 enable `strongswan` 服务。

### 在容器中运行 Strongswan

要在容器中运行 `strongswan`，比如在 [systemd-nspawn](../../08_安全与加密/沙箱/Systemd-nspawn.md) 中，需要类似下面的服务文件：

```
/etc/systemd/system/systemd-nspawn@.service.d/override.conf}
```

```
[Service]
ExecStart=
ExecStart=/usr/bin/systemd-nspawn --quiet --keep-unit --boot --link-journal=try-guest --settings=override --machine=%I --capability=CAP_NET_ADMIN --network-veth
```

## Troubleshooting


## 参阅

- [strongSwan 5: How to create your own VPN](https://www.zeitgeist.se/2013/11/22/strongswan-howto-create-your-own-vpn/)——初始版本的参考，获得作者授权。

## 9.5　WireGuard

*难度：进阶 ｜ 篇幅：18.7 KB ｜ 所属：VPN*

将要讲到：安装、使用、配置一个 VPN 服务器、使用技巧、疑难解答、参见

> [![](https://wiki.archlinuxcn.org/wzh/images/2/26/Tango-preferences-desktop-locale-modified.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale-modified.png)**这篇文章或章节的[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing#Translating)质量不佳。**
>
> **原因：**翻译已经过期，请阅读英文页面中的内容。（在 [Talk:WireGuard#](https://wiki.archlinuxcn.org/wiki/Talk:WireGuard) 中讨论）

引用自 [WireGuard](https://www.wireguard.com/) 项目主页：

: WireGuard 是一种极其简单但快速且现代的 VPN，它利用了最先进的加密技术。它的目标是比 IPsec 更快、更简单、更精简和更有用，同时避免令人头疼的问题。其旨在提供比 OpenVPN 更高的性能。WireGuard 被设计为在嵌入式接口和超级计算机等上运行的通用 VPN，适用于许多不同的环境。虽然最初仅支持 Linux，但现在可以跨平台（Windows、macOS、BSD、iOS、Android）广泛部署。

本文中的主要概念在 [WireGuard](https://www.wireguard.com/) 项目主页上有粗略的介绍。2019 年底以后，[Linux 内核](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e7096c131e5161fa3b8e52a650d7719d2857adfd)中包含 WireGuard。

## 安装

安装 [wireguard-tools](https://archlinux.org/packages/?name=wireguard-tools)包，其提供了用户空间实用程序。

另外，如果 Peer 公钥可用，也可以借助其他网络管理器的 WireGuard 支持。详见[#持久化配置](#持久化配置)。

### 图形客户端

- **wireguird** — 一个运行在Linux上的GTK GUI WireGuard客户端

: [https://github.com/UnnoTed/wireguird](https://github.com/UnnoTed/wireguird) || [wireguird](https://aur.archlinux.org/packages/wireguird/)AUR

- **wireguard-gui** — 一个使用nextauri制作的Wireguard Linux图形化客户端

: [https://github.com/0xle0ne/wireguard-gui](https://github.com/0xle0ne/wireguard-gui) || [wireguard-gui-bin](https://aur.archlinux.org/packages/wireguard-gui-bin/)AUR

### 命令行工具

- **wg_tool** — 用于管理服务器和用户 wireguard 配置的工具。

: [https://github.com/gene-git/wg_tool](https://github.com/gene-git/wg_tool) || [wg_tool](https://aur.archlinux.org/packages/wg_tool/)AUR

- **wg-client** — 一个同时具有图形化界面和命令行的Linux客户端

: [https://github.com/gene-git/wg-client](https://github.com/gene-git/wg-client) || [wg-client](https://aur.archlinux.org/packages/wg-client/)AUR

- **wg2nd** — 将 WireGuard 配置从 [wg-quick(8)](https://man.archlinux.org/man/wg-quick.8) 格式转换为 [systemd-networkd](../网络管理器/Systemd-networkd.md) 兼容的配置的工具。

: [https://git.flu0r1ne.net/wg2nd/about](https://git.flu0r1ne.net/wg2nd/about) || [wg2nd](https://aur.archlinux.org/packages/wg2nd/)AUR

## 使用

以下命令展示了如何在两个对等节点之间建立一条使用以下设置的基本的隧道：

|  | 外部（公共）地址 | 内部 IP 地址 | 端口 |
| --- | --- | --- | --- |
| 域名 | IPv4 地址 | IPv6 地址 | IPv4 地址 | IPv6 地址 |
| 对等节点 A |  | 198.51.100.101 | 2001:db8:a85b:70a:ffd4:ec1b:4650:a001 | 10.0.0.1/24 | fdc9:281f:04d7:9ee9::1/64 | UDP/51871 |
| 对等节点 B | peer-b.example | 203.0.113.102 | 2001:db8:40f0:147a:80ad:3e88:f8e9:b002 | 10.0.0.2/24 | fdc9:281f:04d7:9ee9::2/64 | UDP/51902 |
| 对等节点 C |  | *动态（不固定）* | *动态（不固定）* | 10.0.0.3/24 | fdc9:281f:04d7:9ee9::3/64 | UDP/51993 |

> **提示：**对等节点可使用相同的 UDP 端口。

外部地址应该已经存在。例如，如果 ICMP 回显请求未被阻止，则对等节点 A 应该能够通过 ping 通对等点 B 的公共 IP 地址，反之亦然。

内部地址将是使用 [ip(8)](https://man.archlinux.org/man/ip.8) 实用程序手动创建或通过网络管理软件创建的新地址，这些地址将在新的 WireGuard 网络内部使用。以下示例使用 10.0.0.0/24 和 fdc9:281f:04d7:9ee9::/64 作为内部网络。 IP 地址中的 `/24` 和 `/64` 是 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation)。

### 生成密钥

为每个对等节点创建公钥和私钥。如果要连接很多节点，可以考虑 vanity keypair 个性化 Base64 编码的公钥字符串，参见[#Vanity keys](#Vanity_keys)。

生成私钥：

```
$ (umask 0077; wg genkey > peer_A.key)
```

> **注意：**建议仅允许所有者读写。以上命令使用子 shell 临时改变 [umask](../../08_安全与加密/安全/Umask.md)，从而确保只有所有者可访问（有读写权限）。

生成公钥：

```
$ wg pubkey < peer_A.key > peer_A.pub
```

或者，一次性生成公钥和私钥：

```
$ wg genkey | (umask 0077 && tee peer_A.key) | wg pubkey > peer_A.pub
```

还可以生成预共享密钥，以添加一层额外的对称密钥加密技术，将其混合到现有的公钥加密技术中，以实现后量子抵抗。应为每个对等节点对生成预共享密钥，且不应重复使用。例如，三个互连的对等节点 A、B 和 C 将需要三个单独的预共享密钥，每个对等节点对一个。

使用以下命令为每个对等节点对生成预共享密钥（确保也使用 `umask 0077`）：

```
$ wg genpsk > peer_A-peer_B.psk
$ wg genpsk > peer_A-peer_C.psk
$ wg genpsk > peer_B-peer_C.psk
```

#### Vanity keys

Currently, WireGuard does not support comments or attaching human-memorable names to keys. This makes identifying the key's owner difficult particularly when multiple keys are in use. One solution is to generate a public key that contains some familiar characters (perhaps the first few letters of the owner's name or of the hostname etc.), [wireguard-vanity-address](https://aur.archlinux.org/packages/wireguard-vanity-address/)AUR does this.

For example:

```
$ wireguard-vanity-address --in 8 leslie
```

```
searching for 'leslie' in pubkey[0..10], one of every 214748364 keys should match
one core runs at 2.69e6 keys/s, CPU cores available: 16
est yield: 5.0 seconds per key, 200.10e-3 keys/s
hit Ctrl-C to stop
private wEoVMj92P+E3fQXVf9IixWJqpCqcnP/4OfvrB1g3zmY=  public LEsliEny+aMcWcRbh8Qf414XsQHSBOAFk3TaEk/aSD0=
private EAOwlGGqpHVbZ9ehaCspdBJt+lkMcCfkwiA5T5a4JFs=  public VlesLiEB5BFd//OD2ILKXviolfz+hodG6uZ+XjoalC8=
private UDWG4VWI+RzAGzNSnlC+0X4d3nk9goWPs/NRC5tX524=  public 9lESlieIFOlJFV6dG7Omao2WS+amWgshDdBYn8ahRjo=
```

> [![](https://wiki.archlinuxcn.org/wzh/images/2/26/Tango-preferences-desktop-locale-modified.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale-modified.png)**这篇文章或章节的[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing#Translating)质量不佳。**
>
> **原因：**以下翻译已经过期，请阅读英文页面中的内容。（在 [Talk:WireGuard#](https://wiki.archlinuxcn.org/wiki/Talk:WireGuard) 中讨论）

在 peer 上生成公钥和私钥：

```
$ wg genkey | tee privatekey | wg pubkey > publickey
```

下面的指令会演示如何以表中的配置建立一条两个 peer 之间的隧道

|  | Peer A | Peer B |
| --- | --- | --- |
| 公网地址 | 10.10.10.1/24 | 10.10.10.2/24 |
| 内网地址 | 10.0.0.1/24 | 10.0.0.2/24 |
| wireguard 监听端口 | UDP/4857 | UDP/3981 |

Peer 应当已经拥有公网地址。例如，peer A 应当能够通过 `ping 10.10.10.2` ping 通 peer B，反之亦然。内部地址是由下文 `ip` 命令创建的新地址，并且将在 WireGuard 网络中共享。IP地址中 `/24` 的含义详见 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation)

### Peer A 配置

这个 peer 将监听 UDP 端口 48574，通过将 peer B 的公钥与其内部和外部 IP 地址关联，接受来自 peer B 的连接。

```
# ip link add dev wg0 type wireguard
# ip link set dev wg0 mtu 1420
# ip addr add 10.0.0.1/24 dev wg0
# wg set wg0 listen-port 4857 private-key ./privatekey
# wg set wg0 peer [Peer B public key] persistent-keepalive 25 allowed-ips 10.0.0.2/32 endpoint 10.10.10.2:3981
# ip link set wg0 up
```

`[Peer B public key]` 的格式应当如同 `EsnHH9m6RthHSs+sd9uM6eCHe/mMVFaRh93GYadDDnM=`。`allowed-ips` 是 peer A 能够向之发送流量的地址列表。`allowed-ips 0.0.0.0/0` 将允许向任意地址发送流量。

### Peer B 配置

如同 Peer A，只不过 wireguard 守护监听 UDP 端口 39814 并且只接受 peer A 的连接

```
# ip link add dev wg0 type wireguard
# ip link set dev wg0 mtu 1420
# ip addr add 10.0.0.2/24 dev wg0
# wg set wg0 listen-port 3981 private-key ./privatekey
# wg set wg0 peer [Peer A public key] persistent-keepalive 25 allowed-ips 10.0.0.1/32 endpoint 10.10.10.1:4857
# ip link set wg0 up
```

### 基本检查

不带任何参数使用 wg 命令可以快速查看当前的配置。

例如，当 peer A 配置好之后，我们可以看见它的身份和与之关联的 peers。

```
 peer-a$ wg
 interface: wg0
   public key: UguPyBThx/+xMXeTbRYkKlP0Wh/QZT3vTLPOVaaXTD8=
   private key: (hidden)
   listening port: 4857

 peer: 9jalV3EEBnVXahro0pRMQ+cHlmjE33Slo9tddzCVtCw=
   endpoint: 10.10.10.2:3981
   allowed ips: 10.0.0.2/32
```

此时我们可以 ping 通隧道的另一端：

```
 peer-a$ ping 10.0.0.2
```

### 配置持久化

配置可以通过 `showconf` 来保存

```
# wg showconf wg0 > /etc/wireguard/wg0.conf
# wg setconf wg0 /etc/wireguard/wg0.conf
```

### 示例 peer 配置

```
/etc/wireguard/wg0.conf
```

```
[Interface]
PrivateKey = [CLIENT PRIVATE KEY]
MTU = 1420

[Peer]
PublicKey = [SERVER PUBLICKEY]
AllowedIPs = 10.0.0.0/24, 10.123.45.0/24, 1234:4567:89ab::/48
Endpoint = [SERVER ENDPOINT]:5182
PersistentKeepalive = 25
```

## 配置一个 VPN 服务器

WireGuard 自带一个快速创建和销毁 VPN 服务器的工具，`wg-quick`。注意这里使用的配置文件不是一个能被 `wg setconf` 有效的配置文件，并且你可能至少要把 `eth0` 改成你实际使用的。

### 服务器

```
/etc/wireguard/wg0server.conf
```

```
[Interface]
Address = 10.0.0.1/24  # This is the virtual IP address, with the subnet mask we will use for the VPN
PostUp   = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
ListenPort = 5182
PrivateKey = [SERVER PRIVATE KEY]
MTU = 1420

[Peer]
PublicKey = [CLIENT PUBLIC KEY]
AllowedIPs = 10.0.0.2/32  # 这表示客户端只有一个 IP。
```

要使 iptables 规则生效，启用 IPv4 转发：

```
# sysctl net.ipv4.ip_forward=1
```

永久保留这项改变，向 `/etc/sysctl.d/99-sysctl.conf` 添加 `net.ipv4.ip_forward = 1`。

使用 `wg-quick up wg0server` 启用 Interface，`wg-quick down wg0server` 用以关闭

### 客户端 (转发所有流量)

```
/etc/wireguard/wg0.conf
```

```
[Interface]
Address = 10.0.0.2/24  # The client IP from wg0server.conf with the same subnet mask
PrivateKey = [CLIENT PRIVATE KEY]
DNS = 10.0.0.1
MTU = 1420

[Peer]
PublicKey = [SERVER PUBLICKEY]
AllowedIPs = 0.0.0.0/0, ::0/0
Endpoint = [SERVER ENDPOINT]:5182
PersistentKeepalive = 25
```

使用 `wg-quick up wg0` 来启用 Interface, 使用 `wg-quick down wg0` 来关闭。

使用 `systemctl enable wg-quick@wg0` 来自动启动。

如果你使用 [NetworkManager](../../06_软件与应用/红帽/NetworkManager.md), 可能有必要启用 NetworkManager-wait-online.service `systemctl enable NetworkManager-wait-online.service`

或者你使用的是 systemd-networkd, 启用 systemd-networkd-wait-online.service `systemctl enable systemd-networkd-wait-online.service`

等待所有设备就绪再尝试 WireGuard 连接

## 使用技巧

### 以加密的形式存储私钥

以加密的形式存储私钥可能是可以实现的，例如通过使用 [pass](https://archlinux.org/packages/?name=pass)包。只需将配置文件中\[Interface\]下的 PrivateKey 一行替换为。

```
 PostUp = wg set %i private-key <(su user -c "export PASSWORD_STORE_DIR=/path/to/your/store/; pass WireGuard/private-keys/%i")
```

where user is your username. See the \`wg-quick(8)\` man page for more details.

将其中的user替换成你的用户名。更多细节请参见 \`wg-quick(8)\` man 页面。

## 疑难解答

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>

### 路由定期被重置

[NetworkManager](../../06_软件与应用/红帽/NetworkManager.md) 用户应确保其[未管理](../../06_软件与应用/红帽/NetworkManager.md#忽略特定设备) WireGuard 接口。例如，创建以下配置文件：

```
/etc/NetworkManager/conf.d/unmanaged.conf
```

```
[keyfile]
unmanaged-devices=type:wireguard
```

### DNS 无法解析

When tunneling all traffic through a WireGuard interface, the connection can become seemingly lost after a while or upon new connection. This could be caused by a [network manager](../网络/网络配置.md) or DHCP client overwriting `/etc/resolv.conf`.

By default *wg-quick* uses *resolvconf* to register new DNS entries (from the `DNS` keyword in the configuration file). This will cause issues with [network manager](../网络/网络配置.md)s and DHCP clients that do not use *resolvconf*, as they will overwrite `/etc/resolv.conf` thus removing the DNS servers added by wg-quick.

The solution is to use networking software that supports resolvconf.

> **注意：**Users of [systemd-resolved](../网络/Systemd-resolved.md) should make sure that [systemd-resolvconf](https://archlinux.org/packages/?name=systemd-resolvconf)包 is installed.

Users of [NetworkManager](../../06_软件与应用/红帽/NetworkManager.md) should know that it does not use resolvconf by default. It is recommended to use [systemd-resolved](../网络/Systemd-resolved.md). If this is undesirable, install [openresolv](https://archlinux.org/packages/?name=openresolv)包 and configure NetworkManager to use it: [NetworkManager#Use openresolv](../../06_软件与应用/红帽/NetworkManager.md#Use_openresolv).

### MTU 过低

Due to too low MTU (lower than 1280), wg-quick may have failed to create the WireGuard interface. This can be solved by setting the MTU value in WireGuard configuration in Interface section on client.

```
foo.config
```

```
[Interface]
Address = 10.200.200.2/24
MTU = 1420
PrivateKey = PEER_FOO_PRIVATE_KEY
DNS = 10.200.200.1
```

### 密钥长度或格式错误

To avoid the following error, put the key value in the configuration file and not the path to the key file.

```
# wg-quick up wg0
```

```
[#] ip link add wg0 type wireguard
[#] wg setconf wg0 /dev/fd/63
Key is not the correct length or format: `/path/example.key'
Configuration parsing error
[#] ip link delete dev wg0
```

### 无法在 NAT 或防火墙后建立持久连接

By default, WireGuard peers remain silent while they do not need to communicate, so peers located behind a NAT and/or firewall may be unreachable from other peers until they reach out to other peers themselves (or the connection may time out). Adding `PersistentKeepalive = 25` to the `[Peer]` settings of a peer located behind a NAT and/or firewall can ensure that the connection remains open.

To temporarily set the persistent-keepalive setting via command line, run the following command:

```
# wg set wg0 peer public_key persistent-keepalive 25
```

### 循环路由

Adding the endpoint IP to the allowed IPs list, the kernel will attempt to send handshakes to said device binding, rather than using the original route. This results in failed handshake attempts.

As a workaround, the correct route to the endpoint needs to be manually added using

```
# ip route add endpoint_ip via gateway dev network_interface
```

E.g. for peer B from above in a standard LAN setup:

```
# ip route add 203.0.113.102 via 192.168.0.1 dev eth0
```

To make this route persistent, the command can be added as `PostUp = ip route ...` to the `[Interface]` section of `wg0.conf`. However, on certain setups (e.g. using `wg-quick@.service` in combination with NetworkManager) this might fail on resume. Furthermore, this only works for a static network setup and fails if gateways or devices change (e.g. using Ethernet or Wi-Fi on a laptop).

Using NetworkManager, a more flexible solution is to start WireGuard using a dispatcher script. As root, create

```
/etc/NetworkManager/dispatcher.d/50-wg0.sh
```

```
#!/bin/sh
case $2 in
  up)
    wg-quick up wg0
    ip route add <endpoint ip> via $IP4_GATEWAY dev $DEVICE_IP_IFACE
    ;;
  pre-down)
    wg-quick down wg0
    ;;
esac
```

If not already running, start and enable `NetworkManager-dispatcher.service`. Also make sure that NetworkManager is not managing routes for `wg0`, see [#Routes are periodically reset](#Routes_are_periodically_reset).

### 使用 systemd-networkd 时睡眠导致连接丢失

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[systemd-networkd](../网络管理器/Systemd-networkd.md)。**
>
> **附注：** Might reach more people on the dedicated page.（在 [Talk:WireGuard#Merge proposal of Connection lost after sleep using systemd-networkd](https://wiki.archlinuxcn.org/wzh/Talk:WireGuard#Merge_proposal_of_Connection_lost_after_sleep_using_systemd-networkd) 中讨论）

[systemd](../../01_安装与包管理/初始化/Systemd.md) version 253 introduced a change in how network interfaces are reconfigured when resuming from a suspended state. In doing so, network connections managed by [systemd-networkd](../网络管理器/Systemd-networkd.md) will lose connection to the wireguard interface. Unless a kill switch is configured, this risks exposing the public IP address after resuming from suspend. To fix this, uncomment and change the value to `no` for `ManageForeignRoutingPolicyRules` in `/etc/systemd/networkd.conf`. 

## 参见

- [Wikipedia:WireGuard](https://en.wikipedia.org/wiki/WireGuard)
- [Jason Donenfeld 的演讲](https://www.wireguard.com/presentations/)
- [邮件列表](https://lists.zx2c4.com/mailman/listinfo/wireguard)
- [非官方 WireGuard 文档](https://docs.sweeting.me/s/wireguard)
- [Debian:Wireguard](https://wiki.debian.org/Wireguard)

## 9.6　OpenVPN

*难度：高级 ｜ 篇幅：30.3 KB ｜ 所属：VPN*

将要讲到：安装、内核配置、连接到第三方提供的 VPN、从头开始创建公钥基础设施 (PKI)、一个基本的三层 IP 路由配置、启动 OpenVPN

> [![](https://wiki.archlinuxcn.org/wzh/images/2/26/Tango-preferences-desktop-locale-modified.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale-modified.png)**这篇文章或章节的[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing#Translating)质量不佳。**
>
> **原因：**Still out of date, even after the 2021-12-01 update, e.g. this page still references `rc.conf`（在 [Talk:OpenVPN#](https://wiki.archlinuxcn.org/wiki/Talk:OpenVPN) 中讨论）

本文介绍了 [OpenVPN](https://openvpn.net) 的基本的安装与配置过程，适用于个人使用与小型商业使用。要了解更多信息，请访问官方网站 [HOWTO](https://openvpn.net/index.php/open-source/documentation/howto.html)、[OpenVPN 2.4 手册页](https://community.openvpn.net/openvpn/wiki/Openvpn24ManPage)以及 [Manual](https://openvpn.net/index.php/open-source/documentation/manuals.html)。OpenVPN 是一个健壮的、高度灵活的 [VPN](https://en.wikipedia.org/wiki/VPN) 守护进程。它支持 [SSL/TLS](https://en.wikipedia.org/wiki/SSL/TLS) 安全、[以太网桥接](https://en.wikipedia.org/wiki/Bridging_(networking))、经由[代理](https://en.wikipedia.org/wiki/Proxy_server)的 [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) 或 [UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol) [隧道](https://en.wikipedia.org/wiki/Tunneling_protocol)和 [NAT](https://en.wikipedia.org/wiki/Network_address_translation)。另外，它也支持动态 IP 地址以及 [DHCP](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol)，可伸缩性足以支持数百或数千用户的使用场景，同时可移植至大多数主流操作系统平台上。

OpenVPN 与 [OpenSSL](https://www.openssl.org) 库紧密相关，并由此获得许多加密功能。它除了支持使用[预共享密钥](https://en.wikipedia.org/wiki/Pre-shared_key)（静态密钥模式）或使用非对称加密的[公钥](https://en.wikipedia.org/wiki/Public_key)（[SSL/TLS](https://en.wikipedia.org/wiki/SSL/TLS) 模式）这两种传统加密方式之外。还支持不加密的 TCP/UDP 隧道。

OpenVPN 配合大多数平台上都有的 [TUN/TAP](https://en.wikipedia.org/wiki/TUN/TAP) 虚拟网络接口一起使用。总体而言，它旨在提供 [IPSec](https://en.wikipedia.org/wiki/IpSec) 的许多关键功能，但占用空间相对较小。OpenVPN 由 James Yonan 编写，并在 [GNU 通用公共许可证 (GPL)](https://en.wikipedia.org/wiki/GNU_General_Public_License) 下发布。

## 安装

[安装](../../01_安装与包管理/包管理器/Pacman.md) [openvpn](https://archlinux.org/packages/?name=openvpn)包，它同时支持服务器和客户端模式。

可用的前端：

- **NetworkManager OpenVPN** — OpenVPN 的 NetworkManager VPN 插件。

: [https://wiki.gnome.org/Projects/NetworkManager/VPN](https://wiki.gnome.org/Projects/NetworkManager/VPN) || [networkmanager-openvpn](https://archlinux.org/packages/?name=networkmanager-openvpn)包

- **QOpenVPN** — 用 PyQt 为基于 systemd 的发行版编写的简单 OpenVPN GUI。

: [https://github.com/xmikos/qopenvpn](https://github.com/xmikos/qopenvpn) || [qopenvpn](https://archlinux.org/packages/?name=qopenvpn)包

- **eOVPN** — 用于连接、管理和更新 OpenVPN 配置的应用程序。

: [https://github.com/jkotra/eOVPN](https://github.com/jkotra/eOVPN) || [eovpn](https://aur.archlinux.org/packages/eovpn/)AUR

## 内核配置

OpenVPN 需要 TUN/TAP 的支持，默认内核已经进行了正确的配置。自定义的内核需要启用 `tun` 模块。

```
Kernel config file
```

```
 Device Drivers
  --> Network device support
    [M] Universal TUN/TAP device driver support
```

详情参阅 [Kernel modules](../../02_系统管理与内核/内核/内核模块.md)

## 连接到第三方提供的 VPN

要连接到第三方提供的 VPN 服务，可以忽略以下大部分内容，尤其是在服务器设置方面。 从 [#客户端配置文件](#客户端配置文件)开始，然后跳到 [#启动 OpenVPN](#启动_OpenVPN)。 应使用提供商证书和说明，请参阅 Category:VPN providers 以获取可适用于其他提供商的示例。 Linux Containers 中的 OpenVPN 客户端也有一般适用的说明，同时它更进一步，将 OpenVPN 客户端进程隔离到容器中。

> **注意：**大多数免费的 VPN 提供商会（通常只）提供 [PPTP](PPTP%20server.md), 它更容易设置和配置，但 [不安全](http://poptop.sourceforge.net/dox/protocol-security.phtml)。

## 从头开始创建公钥基础设施 (PKI)

在设置 OpenVPN 服务器时，用户需要创建一个[公开密钥基础建设 (PKI)](https://en.wikipedia.org/wiki/Public_Key_Infrastructure)，这在 Easy-RSA 文章中有详细说明。 按照单独文章中的步骤创建所需的证书、私钥和相关文件后，此时 `/etc/openvpn/server` 中应该有 5 个文件：

```
ca.crt
dh.pem
servername.crt
servername.key
ta.key
```

或者，从 OpenVPN 2.4 开始，可以使用 Easy-RSA 使用椭圆曲线生成证书和密钥。 有关详细信息，请参阅 OpenVPN 文档。

## 一个基本的三层 IP 路由配置

> **注意：**除非另有明确说明，本文的其余部分均假设基本的第 3 层 IP 路由配置。

OpenVPN 是一个非常通用的软件，可以进行多种配置，实际上机器既可以是服务器，也可以是客户端。

随着 v2.4 的发布，服务器配置存储在 `/etc/openvpn/server` 中，客户端配置存储在 `/etc/openvpn/client` 中，每种模式都有自己的systemd 单元，即 `openvpn-client@.service` 和 `openvpn-server@.service`。

### 示例配置

OpenVPN 软件包附带了一系列用于不同目的的示例配置文件。示例服务器和客户端配置文件是基本 OpenVPN 设置的理想起点，具有以下功能：

- 使用 [Public Key Infrastructure (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 进行身份验证。
- 使用虚拟 TUN 网络接口（OSI 第 3 层 IP 路由）创建 VPN。
- 监听 UDP 端口 1194 上的客户端连接（OpenVPN 的官方 IANA 端口号 ）。
- 将虚拟地址分配给来自 10.8.0.0/24 子网的连接客户端。

有关更高级的配置，请参阅 [openvpn(8)](https://man.archlinux.org/man/openvpn.8) 手册页和 [OpenVPN 文档](https://openvpn.net/index.php/open-source/documentation)。

### 服务器配置文件

> **注意：**请注意，如果服务器位于防火墙或 NAT 转换路由器后面，则必须将 OpenVPN 端口转发到服务器。

将示例服务器配置文件 `/usr/share/openvpn/examples/server.conf` 复制到 `/etc/openvpn/server/server.conf`。

编辑文件，至少进行以下更改：

```
/etc/openvpn/server/server.conf
```

```
ca ca.crt
cert servername.crt
key servername.key
dh dh.pem

tls-crypt ta.key # Replaces tls-auth ta.key 0

user nobody
group nobody
```

如果使用带有椭圆曲线的 TLS，请指定 `dh none` 和 `ecdh-curve secp521r1`。使用椭圆曲线时不使用 DH 参数文件。从 OpenVPN 2.4.8 开始，需要在服务器配置中指定椭圆曲线的类型。否则服务器将无法识别曲线类型并可能使用不兼容的曲线类型，从而导致身份验证错误。

#### 强化服务器

如果优先考虑安全性，则建议进行其他配置，包括：限制服务器使用强密码/身份验证方法和（可选）将启用的 TLS 密码集限制为较新的密码。从 OpenVPN 2.4 开始，服务器和客户端将在 TLS 模式下自动协商 **AES-256-GCM**。

将以下内容添加到 `/etc/openvpn/server/server.conf`：

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**使用说明属于维基文本，不属于代码块。避免诸如“边缘安全性”之类的主观术语。（在[Talk:OpenVPN](https://wiki.archlinuxcn.org/wiki/Talk:OpenVPN)讨论）

最安全

```
/etc/openvpn/server/server.conf
```

```
cipher AES-256-GCM
auth SHA512
tls-version-min 1.3
#Uncomment  tls-cipher to limit possible negotiation options to the strongest ciphers, doing so it's no longer possible to generate certs with current easyrsa, more information
#tls-cipher TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256
```

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**兼容性选项不适合强化部分。（在[Talk:OpenVPN](https://wiki.archlinuxcn.org/wiki/Talk:OpenVPN)讨论）

与大多数设备的兼容性

```
/etc/openvpn/server/server.conf
```

```
cipher AES-256-GCM
auth SHA512
tls-version-min 1.2
tls-cipher TLS-DHE-RSA-WITH-AES-256-GCM-SHA384:TLS-DHE-RSA-WITH-AES-128-GCM-SHA256:TLS-DHE-RSA-WITH-AES-256-CBC-SHA:TLS-DHE-RSA-WITH-CAMELLIA-256-CBC-SHA:TLS-DHE-RSA-WITH-AES-128-CBC-SHA:TLS-DHE-RSA-WITH-CAMELLIA-128-CBC-SHA
```

> **注意：**
>
> - .ovpn 客户端配置文件“必须”包含匹配的密码和身份验证行才能正常工作（至少对于 iOS 和 Android 客户端）。
> - 错误地使用 `tls-cipher` 可能会导致调试连接困难，并且可能没有必要。有关更多信息，请参阅 [OpenVPN 的社区维基](https://community.openvpn.net/openvpn/wiki/Hardening#Useof--tls-cipher)。

#### 启用压缩

上游不建议启用压缩；这样做会向服务器打开所谓的 VORACLE 攻击向量。请参阅[这篇](https://community.openvpn.net/openvpn/wiki/VORACLE)文章。

#### 非标准端口和/或协议

一般建议使用 OpenVPN over UDP，因为 [TCP over TCP 不是一个好主意](http://sites.inka.de/bigred/devel/tcp-tcp.html)。

某些网络可能不允许默认端口和/或协议上的 OpenVPN 连接。规避这一点的一种策略是模拟 HTTPS 流量，这很可能是畅通无阻的。

为此，请将 `/etc/openvpn/server/server.conf` 配置为：

```
/etc/openvpn/server/server.conf
```

```
port 443
proto tcp
```

> **注意：**.ovpn 客户端配置文件 **必须**包含匹配的端口和原型线才能正常工作！

#### 在物理机的不同端口上运行多个 OpenVPN 实例

可以在同一台机器上运行多个并发的 OpenVPN 实例。每个服务器都需要在 `/etc/openvpn/server/` 中定义为单独的 .conf 文件。至少，并行服务器需要在不同的端口上运行。一个简单的设置将流量连接到一个单独的 IP 池。更高级的设置超出了本指南的范围。

考虑这个例子，运行 2 个并发服务器，一个端口 443/udp，另一个端口 80/tcp。

首先修改创建的 `/etc/openvpn/server/server.conf` 如下：

```
/etc/openvpn/server/server.conf
```

```
port 443
proto udp
server 10.8.0.0 255.255.255.0
```

现在复制它并修改副本以在 80/tcp 上运行：

```
/etc/openvpn/server/server2.conf
```

```
port 80
proto tcp
server 10.8.1.0 255.255.255.0
```

请务必在防火墙中设置相应的条目，请参阅[#防火墙配置](#防火墙配置)中的相关部分。

### 客户端配置文件

将示例客户端配置文件 `/usr/share/openvpn/examples/client.conf` 复制到 `/etc/openvpn/client/`。

编辑以下内容：

- `remote` 指令反映服务器的 [Fully Qualified Domain Name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)、主机名（客户端已知）或其 IP 地址。
- 取消注释 `user` 和 `group` 指令以删除权限。
- `ca`、`cert` 和 `key` 参数反映密钥和证书的路径和名称。
- 启用 TLS HMAC 握手保护（`--tls-crypt` 或 `--tls-auth`）。

```
/etc/openvpn/client/client.conf
```

```
client
remote elmer.acmecorp.org 1194

user nobody
group nobody
ca ca.crt
cert client.crt
key client.key

tls-crypt ta.key # Replaces tls-auth ta.key 1
```

#### 以非特权用户身份运行

在配置文件中使用选项 `user nobody` 和 `group nobody` 会使*OpenVPN*在建立连接后放弃其 `root` 权限。缺点是在 VPN 断开连接后，守护进程无法再次删除其设置的网络路由。如果想要在没有 VPN 连接的情况下限制传输流量，那么延迟路由可能被认为是有益的。但是，也可能发生 OpenVPN 服务器在隧道运行时向路由推送更新的情况。具有删除权限的客户端将无法执行更新并退出并出现错误。

由于似乎需要手动操作来管理路由，因此选项 `user nobody` 和 `group nobody` 可能看起来不受欢迎。然而，根据设置，有不同的方法来处理这些情况：

- 对于单元的错误，一个简单的方法是edit它并在`[Service]`部分添加一个`Restart=on-failure`。但是，仅此一项不会删除任何过时的路由，因此可能会发生重新启动的隧道未正确路由的情况。
- 包中包含`/usr/lib/openvpn/plugins/openvpn-plugin-down-root.so`，可以用来让*openvpn*fork一个只有root权限的进程从主进程接收到关闭信号时执行自定义脚本的任务，该进程正在处理具有删除权限的隧道（另请参阅其 [README](https://community.openvpn.net/openvpn/browser/plugin/down-root/README?rev=d02a86d37bed69ee3fb63d08913623a86c88da15)）。

下面链接的 OpenVPN HowTo 更进一步，创建了一个专用的非特权用户/组，而不是现有的 `nobody`。这样做的好处是避免了在守护进程之间共享用户时的潜在风险：

- [OpenVPN HowTo](https://openvpn.net/index.php/open-source/documentation/howto.html#security) 解释了如何创建非特权用户模式和包装脚本以自动恢复路由的另一种方法。
- 可以让 OpenVPN 首先以非特权用户身份启动，而无需以 root 身份运行，请参阅[这个 OpenVPN wiki](https://community.openvpn.net/openvpn/wiki/UnprivilegedUser)（howto）。 howto 假设存在 System V init，而不是 [Systemd](../../01_安装与包管理/初始化/Systemd.md) 并且不包括 `--up`/`--down` 脚本的处理 - 那些应该被处理与 *ip* 命令相同的方式，额外注意访问权限。
- 也可以从无特权的 podman 容器中运行 OpenVPN，请参阅[这部分 OpenVPN HowTo](https://community.openvpn.net/openvpn/wiki/UnprivilegedUser#RunOpenVPNwithinunprivilegedpodmancontainer)

> **提示：**[#openvpn-unroot](#openvpn-unroot) 描述了一种自动化上述设置的工具。

### 将证书转换为加密的 .p12 格式

某些软件只会读取存储在密码加密的 .p12 文件中的 VPN 证书。这些可以使用以下命令生成：

```
# openssl pkcs12 -export -inkey keys/bugs.key -in keys/bugs.crt -certfile keys/ca.crt -out keys/bugs.p12
```

### 测试 OpenVPN 配置

在服务器上（以 root 用户）运行 `openvpn /etc/openvpn/server/server.conf`，在客户端（以 root 用户）运行 `openvpn /etc/openvpn/client/client.conf`。示例输出应类似于以下内容：

```
# openvpn /etc/openvpn/server/server.conf
```

```
Wed Dec 28 14:41:26 2011 OpenVPN 2.2.1 x86_64-unknown-linux-gnu [SSL] [LZO2] [EPOLL] [eurephia] built on Aug 13 2011
Wed Dec 28 14:41:26 2011 NOTE: OpenVPN 2.1 requires '--script-security 2' or higher to call user-defined scripts or executables
Wed Dec 28 14:41:26 2011 Diffie-Hellman initialized with 2048 bit key
...
Wed Dec 28 14:41:54 2011 bugs/95.126.136.73:48904 MULTI: primary virtual IP for bugs/95.126.136.73:48904: 10.8.0.6
Wed Dec 28 14:41:57 2011 bugs/95.126.136.73:48904 PUSH: Received control message: 'PUSH_REQUEST'
Wed Dec 28 14:41:57 2011 bugs/95.126.136.73:48904 SENT CONTROL [bugs]: 'PUSH_REPLY,route 10.8.0.1,topology net30,ping 10,ping-restart 120,ifconfig 10.8.0.6 10.8.0.5' (status=1)
```

```
# openvpn /etc/openvpn/client/client.conf
```

```
Wed Dec 28 14:41:50 2011 OpenVPN 2.2.1 i686-pc-linux-gnu [SSL] [LZO2] [EPOLL] [eurephia] built on Aug 13 2011
Wed Dec 28 14:41:50 2011 NOTE: OpenVPN 2.1 requires '--script-security 2' or higher to call user-defined scripts or executables
...
Wed Dec 28 14:41:57 2011 GID set to nobody
Wed Dec 28 14:41:57 2011 UID set to nobody
Wed Dec 28 14:41:57 2011 Initialization Sequence Completed
```

[查找IP地址](../网络/网络配置.md#IP_地址)分配给服务器上的tunX接口，从客户端ping。

[查找IP地址](../网络/网络配置.md#IP_地址)分配给客户端的tunX接口，从服务器ping。

> **注意：**如果使用防火墙，请确保 TUN 设备上的 IP 数据包没有被阻止。

### 使用 Fragment 和 MSS 配置 MTU

如果在通过 OpenVPN 使用（远程）服务时遇到问题（例如网页浏览、DNS、[NFS](../网络共享/NFS.md)），则可能需要手动设置 MTU 值。

以下消息可能指示应调整 MTU 值：

```
read UDPv4 [EMSGSIZE Path-MTU=1407]: Message too long (code=90)
```

为了获得最大段大小 (MSS)，客户端需要沿着到服务器的路径发现最小的 MTU。为了做到这一点 ping 服务器并禁用碎片，然后指定最大数据包大小 ：

```
# ping -M do -s 1500 -c 1 example.com
```

每次将 1500 值减 10，直到 ping 成功。

> **注意：**不支持“片段”指令的客户端（例如 OpenELEC，[应用程序](https://docs.openvpn.net/connecting/connecting-to-access-server-with-apple-ios/faq-regarding-openvpn-connect-ios/)) 无法连接到使用 `fragment` 指令的服务器。请参阅 `mtu-test` 作为替代解决方案。

更新客户端配置以使用成功的 MTU 值，例如：

```
/etc/openvpn/client/client.conf
```

```
remote example.com 1194

tun-mtu 1400
mssfix 1360
```

可能会指示 OpenVPN 每次在客户端连接时测试 MTU。请耐心等待，因为客户端可能不会通知正在运行的测试，并且连接在完成之前可能会显示为无效。以下将增加大约 3 分钟的 OpenVPN 启动时间。建议配置片段大小，除非客户端将通过许多不同的网络进行连接并且瓶颈不在服务器端：

```
/etc/openvpn/client/client.conf
```

```
remote example.com 1194

mtu-test
```

### IPv6

#### 通过 IPv6 连接到服务器

从 OpenVPN 2.4 开始，当仅使用 `proto udp` 或 `proto tcp` 时，OpenVPN 将使用操作系统定义的 `AF_INET`，在大多数情况下，这将仅是 IPv4。要同时使用 IPv4 和 IPv6，请使用 `proto udp6` 或 `proto tcp6`。要仅强制执行 IPv4，请使用 `proto udp4` 或 `proto tcp4`。在较旧的 OpenVPN 版本上，一个服务器实例只能支持 IPv4 或 IPv6。

#### 在隧道内提供 IPv6

为了在隧道内提供 IPv6，请将 IPv6 前缀路由到 OpenVPN 服务器。要么在网关上设置静态路由（如果分配了静态块），要么使用 DHCPv6 客户端通过 DHCPv6 前缀委派获取前缀（详情请参阅[IPv6 Prefix delegation](../网络/IPv6.md#Prefix_delegation_(DHCPv6-PD))）。还可以考虑使用来自地址块 fc00::/7 的唯一本地地址。这两种方法各有优缺点：

- 许多 ISP 仅提供动态更改的 IPv6 前缀。 OpenVPN 不支持前缀更改，因此每次更改前缀时都要更改 server.conf（也许可以通过脚本自动实现）。
- ULA 地址不会路由到 Internet，并且设置 NAT 不像使用 IPv4 那样简单。这意味着不能通过隧道路由整个流量。那些希望通过 IPv6 连接两个站点而不需要通过隧道连接到 Internet 的人可能希望使用 ULA 地址来轻松。

或者，NDP 代理应该可以工作。请参阅[这个 StackExchange 帖子](https://unix.stackexchange.com/questions/136211/routing-public-ipv6-traffic-through-openvpn-tunnel)。

收到前缀（建议使用 /64）后，将以下内容附加到 server.conf：

```
server-ipv6 2001:db8:0:123::/64
```

这是相当于 OpenVPN 的默认 10.8.0.0/24 网络的 IPv6，需要从 DHCPv6 客户端获取。或者使用例如 fd00:1234::/64。

那些想要将路由推送到家庭网络（192.168.1.0/24 等效）的人，还需要附加：

```
push "route-ipv6 2001:db8:0:abc::/64"
```

OpenVPN 尚不包括 DHCPv6，因此没有方法例如通过 IPv6 推送 DNS 服务器。这需要通过 IPv4 来完成。 [OpenVPN Wiki](https://community.openvpn.net/openvpn/wiki/IPv6) 提供了一些其他配置选项。

## 启动 OpenVPN

### 手动启动

### systemd 服务配置

### 让 NetworkManager 开始连接

### NetworkManager-native VPN 配置

#### 图形界面配置

#### CLI 配置

#### 与连接同步状态

#### 故障排除

###### 没有证书密码

## 通过服务器路由客户端流量

### 防火墙配置

#### 防火墙

#### ufw

#### iptables

### 如果 VPN 出现故障，防止泄漏

#### ufw

#### vpnfailsafe

## 第 3 层 IPv4 路由

### 路由 LAN 的先决条件

#### 路由表

### 将服务器 LAN 连接到客户端

### 将客户端 LAN 连接到服务器

### 连接客户端和服务器 LANs

### 连接客户端和客户端局域网

## 域名解析

### pull-resolv-conf 自定义脚本

### update-resolv-conf 自定义脚本

### update-systemd-resolved 自定义脚本

### 使用 NetworkManager 覆盖 DNS 服务器

## 第 2 层以太网桥接

## 配置生成器

### ovpngen

### openvpn-unroot

## 故障排除

### 客户端守护进程在挂起后没有重新连接

### 一段时间不活动后连接断开

### PID 文件不存在

### 路由配置失败， systemd-networkd

### tls-crypt 解包错误：数据包太短

## 另见

## 准备证书和密钥数据

现在要创建所需的证书和密钥，可以在任何机器上完成，即使没有联网也可以进行。 设置证书和密钥生成脚本的默认值。编辑 `/etc/openvpn/easy-rsa/vars`，设置 KEY_COUNTRY, KEY_PROVINCE, KEY_CITY, KEY_ORG 和 KEY_EMAIL 参数(不要留空任何参数)，然后导出环境变量。

```
# source ./vars
```

清理之前的密钥：

```
# ./clean-all
```

build-ca 脚本创建了 certificate authority (CA) ca.key，密钥认证机器需要这个密钥。服务器和客户端需要 ca.crt 证书。

```
# ./build-ca
```

`build-key-server` 为服务器创建一个证书和密钥对。使用中不要输入简单的密码或公司名。

```
# ./build-key-server <server-name>
```

`build-dh` 脚本创建服务器需要的 Diffie-Hellman pem 文件。

```
# ./build-dh
```

`build-key` 脚本创建客户端证书和密钥对。可以生成任意多个以给不同的客户端使用。只要保证客户端名 <client> 是唯一的。如果要用密码认证客户端，请使用 `build-key-pass` 脚本。

```
# ./build-key <client1>
# ./build-key <client2>
```

生成的文件都保存在 `/etc/openvpn/easy-rsa/keys`。如果有错误，可以通过运行 `clean-all` 脚本，然后从头开始。注意这将删除之前生成的证书和密钥。

```
# ./clean-all
```

最后一步是将所有需要的文件通过安全通道放到正确的机器上。`ca.crt` 需要放到所有服务器和客户端。`server.crt`, `server.key` 和 `dh{n}.pem` 文件放到服务器， `client.crt` 和 `client.key` 文件放到客户端。

## 配置服务器

### 复制默认服务器配置文件

```
# cp /usr/share/openvpn/examples/server.conf /etc/openvpn/openvpn.conf
```

### 使用 PAM 和密码认证

```
port 1194
proto udp
dev tap
ca /etc/openvpn/easy-rsa/keys/ca.crt
cert /etc/openvpn/easy-rsa/keys/<MYSERVER>.crt
key /etc/openvpn/easy-rsa/keys/<MYSERVER>.key
dh /etc/openvpn/easy-rsa/keys/dh1024.pem
server 192.168.56.0 255.255.255.0
ifconfig-pool-persist ipp.txt
;learn-address ./script
client-to-client
;duplicate-cn
keepalive 10 120
;tls-auth ta.key 0
comp-lzo
;max-clients 100
;user nobody
;group nobody
persist-key
persist-tun
status /var/log/openvpn-status.log
verb 3
client-cert-not-required
username-as-common-name
plugin /usr/lib/openvpn/openvpn-auth-pam.so login
```

### 使用证书认证

```
port 1194
proto tcp
dev tun0

ca /etc/openvpn/easy-rsa/keys/ca.crt
cert /etc/openvpn/easy-rsa/keys/<MYSERVER>.crt
key /etc/openvpn/easy-rsa/keys/<MYSERVER>.key
dh /etc/openvpn/easy-rsa/keys/dh1024.pem

server 10.8.0.0 255.255.255.0
ifconfig-pool-persist ipp.txt
keepalive 10 120
comp-lzo
user nobody
group nobody
persist-key
persist-tun
status /var/log/openvpn-status.log
verb 3

log-append /var/log/openvpn
status /tmp/vpn.status 10
```

### 通过服务器路由

将下面内容写入服务器的 `openvpn.conf` 配置文件，"192.168.1.1" 修改为外部 DNS IP 地址。

```
push "dhcp-option DNS 192.168.1.1"
push "redirect-gateway def1"
```

使用 iptable 进行 NAT 转发：

```
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE
```

如果运行在 OpenVZ VPS 环境，参见 :

```
iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o venet0 -j SNAT --to (venet0 ip)
```

如果一切正常，保存修改，编辑 `/etc/conf.d/iptables` 设置 IPTABLES_FORWARD=1

```
/etc/rc.d/iptables save
```

## 客户端配置

配置客户端的 .conf 文件

### 使用密码认证

```
client
dev tap
proto udp
remote <address> 1194
resolv-retry infinite
nobind
persist-tun
comp-lzo
verb 3
auth-user-pass passwd
ca ca.crt
```

被 `auth-user-pass` 引用的 `passwd` 文件必须包含如下两行:

- 第一行 - username
- 第二行 - password

### 证书验证

```
client
remote <MYSERVER> 1194
dev tun0
proto tcp
resolv-retry infinite
nobind
persist-key
persist-tun
verb 2
ca ca.crt
cert client1.crt
key client1.key
comp-lzo
```

将`ca.crt`, `client1.crt` 和 `client1.key`复制到远程计算机：

安装隧道模块:

```
 # sudo modprobe tun
```

要让 **tun** 模块自动启动，请将其加入 `/etc/rc.conf` 的 Modules 行。

### DNS

系统使用的 DNS 服务器定义在`/etc/resolv.conf`。通常此文件由控制系统网络的模块(Wicd, NetworkManager 等)维护。然而，如果希望通过远程服务器解析地址，OpenVPN 需要修改这个文件。

安装 **openresolv**软件包，它可以实现多个程序互不影响的修改 `resolv.conf`。安装后通过重启网络连接，保证 resolv.conf 是由 "resolvconf" 创建而且 DNS 解析工作正常。openresolv 不需要配置，它会自动检测和使用网络系统。

然后将如下脚本保存到`/usr/share/openvpn/update-resolv-conf`:

```
#!/bin/bash
#
# Parses DHCP options from openvpn to update resolv.conf
# To use set as 'up' and 'down' script in your openvpn *.conf:
# up /etc/openvpn/update-resolv-conf
# down /etc/openvpn/update-resolv-conf
#
# Used snippets of resolvconf script by Thomas Hood <jdthood@yahoo.co.uk>
# and Chris Hanson
# Licensed under the GNU GPL.  See /usr/share/common-licenses/GPL.
#
# 05/2006 chlauber@bnc.ch
#
# Example envs set from openvpn:
# foreign_option_1='dhcp-option DNS 193.43.27.132'
# foreign_option_2='dhcp-option DNS 193.43.27.133'
# foreign_option_3='dhcp-option DOMAIN be.bnc.ch'

[ -x /usr/sbin/resolvconf ] || exit 0

case $script_type in

up)
   for optionname in ${!foreign_option_*} ; do
      option="${!optionname}"
      echo $option
      part1=$(echo "$option" | cut -d " " -f 1)
      if [ "$part1" == "dhcp-option" ] ; then
         part2=$(echo "$option" | cut -d " " -f 2)
         part3=$(echo "$option" | cut -d " " -f 3)
         if [ "$part2" == "DNS" ] ; then
            IF_DNS_NAMESERVERS="$IF_DNS_NAMESERVERS $part3"
         fi
         if [ "$part2" == "DOMAIN" ] ; then
            IF_DNS_SEARCH="$part3"
         fi
      fi
   done
   R=""
   if [ "$IF_DNS_SEARCH" ] ; then
           R="${R}search $IF_DNS_SEARCH
"
   fi
   for NS in $IF_DNS_NAMESERVERS ; do
           R="${R}nameserver $NS
"
   done
   echo -n "$R" | /usr/sbin/resolvconf -a "${dev}.inet"
   ;;
down)
   /usr/sbin/resolvconf -d "${dev}.inet"
   ;;
esac
```

设置脚本可执行属性：

```
$ chmod +x /usr/share/openvpn/update-resolv-conf
```

然后将下面内容加入 OpenVPN 客户端的配置文件：

```
script-security 2
up /usr/share/openvpn/update-resolv-conf
down /usr/share/openvpn/update-resolv-conf
```

现在再启动 OpenVPN 连接，就能发现 `resolv.conf` 文件已经更新，关闭连接后恢复正常。

## 启动 OpenVPN

### 手动启动

如需对 VPN 连接进行调试，可以以 root 身份手动运行 `openvpn /etc/openvpn/client.conf` 以启动客户端守护程序。服务器端同样可以如此启动，只需替换为服务器端的配置文件（例，`openvpn /etc/openvpn/server.conf`）。

### systemd 服务配置

若需在系统启动时自动启动 OpenVPN，对服务器端与客户端，都可以采用在对应机器上启用 `openvpn@*<configuration>*.service` 的方式配置。

例如，如果客户端配置文件是 `/etc/openvpn/client.conf`，则服务名称应为 `openvpn@client.service`。或者，如果服务器端配置文件是 `/etc/openvpn/server.conf`，则服务名称应为 `openvpn@server.service`。

### 让 NetworkManager 启动连接

在客户端，您可能并不需要一直运行 VPN 隧道，或者您仅仅想要为特定的 NetworkManager 连接建立隧道。要实现这一点，您可以向 `/etc/NetworkManager/dispatcher.d/` 添加脚本。在下列示例中，"Provider" 是 NetworkManager 连接的名称：

```
/etc/NetworkManager/dispatcher.d/10-openvpn
```

```
#!/bin/sh
case "$2" in
  up)
    if [ "$CONNECTION_ID" = "Provider" ]; then
      systemctl start openvpn@client
    fi
  ;;
  down)
    systemctl stop openvpn@client
  ;;
esac
```

请查看 [NetworkManager#Network services with NetworkManager dispatcher](../../06_软件与应用/红帽/NetworkManager.md#Network_services_with_NetworkManager_dispatcher) 以了解详情。

### Gnome 配置

如果您想要经由 Gnome 内置的网络配置来连接到一个 OpenVPN 服务器，请按照如下步骤进行配置： 首先，安装 `networkmanager-openvpn`。 然后，在设置菜单选中网络设置，在VPN点击+新建连接。从这里你可以选择OpenVPN并手动输入设置，或者您也可以选择导入[配置文件](#客户端配置)，如果您已经有一个的话。如果您按照上述说明，配置文件会在`/etc/openvpn/client.conf`。打开开关就可以连接VPN。

## 参见

- [OpenVPN 官方站点](https://openvpn.net/index.php/open-source.html)
