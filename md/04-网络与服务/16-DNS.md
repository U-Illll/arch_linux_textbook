# 第 16 章　DNS

> 域名解析配错一处，依赖网络的命令就会一起失效。本章讲清解析链：《域名解析》讲 NSS 与 glibc 解析器如何判定查询，是其余各节的地基，必须精读；《Unbound》最长，覆盖递归、缓存与排错，也必读；《BIND》只在自建权威或配 DNSSEC 时再看；《Cloudflared》最短，按需查阅即可。
> 
> 最常见的坑是跳过解析链直改上游：查询先经 NSS 和 glibc 解析器判定，服务在跑却解析不了，错在这一环而非 DNS 服务器。其次是混淆权威与递归：BIND 只管权威，Unbound 只管递归，角色配错比地址配错更隐蔽。

<!-- 本章 4 节，共 52 KB -->

## 16.1　Cloudflared

*难度：入门 ｜ 篇幅：3.1 KB ｜ 所属：DNS*

将要讲到：安装、用法、检查、上游节点、参阅

[Cloudflared](https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy/) 可被用于运行本地 [DNS over HTTPS](域名解析.md)（DoH）服务器，也就是存根解析器。

## 安装

安装 [cloudflared](https://archlinux.org/packages/?name=cloudflared)包。

## 用法

运行 `cloudflared proxy-dns` 来启动 DNS over HTTPS 代理服务器。

使用 `--address` 和 `--port` 选项来指定 cloudflared 监听的地址和端口，它们的默认值分别为 `localhost` 和 `53`。详细的可用命令行选项清单可在[这里](https://fig.io/manual/cloudflared/proxy-dns)查看。

你可以创建一个 systemd [单元文件](../../01_安装与包管理/初始化/Systemd.md#编写单元文件)，例如：

```
/etc/systemd/system/cloudflared.service
```

```
[Unit]
Description=DNS over HTTPS proxy client
Wants=network-online.target nss-lookup.target
Before=nss-lookup.target

[Service]
AmbientCapabilities=CAP_NET_BIND_SERVICE
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
DynamicUser=yes
ExecStart=/usr/bin/cloudflared proxy-dns --port 54

[Install]
WantedBy=multi-user.target
```

> **注意：**Extra 包不提供服务单元文件。参见 [archlinux/packaging/packages/cloudflared#1](https://gitlab.archlinux.org/archlinux/packaging/packages/cloudflared/-/issues/1)。

启动服务后，你可以使用 [drill(1)](https://man.archlinux.org/man/drill.1)（由 [ldns](https://archlinux.org/packages/?name=ldns)包 提供）来确认它是否在正常工作。

```
$ drill archlinux.org @127.0.0.1 -p 54
```

## 检查

通过 [1.1.1.1/help](https://1.1.1.1/help) 来检查浏览器是否在使用 *Cloudflare DoH*。

## 上游节点

默认情况下 cloudflared 会使用 `https://1.1.1.1/dns-query` 和 `https://1.0.0.1/dns-query`，即 [Cloudflare DoH 服务器](https://en.wikipedia.org/wiki/1.1.1.1)的地址作为上游 URL。

你可以通过 `--upstream` 选项来指定不同的上游节点 URL。

> **注意：**请使用你信任的 DNS 解析服务器，可参考[域名解析#第三方 DNS 服务](域名解析.md#第三方_DNS_服务).

## 参阅

- [官方指引](https://developers.cloudflare.com/1.1.1.1/encryption/dns-over-https/dns-over-https-client/)
- [命令行选项](https://fig.io/manual/cloudflared/proxy-dns)
- [Running a DNS over HTTPS Client - Cloudflare Resolver](https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy/)
- [隆重推出家庭版 1.1.1.1](https://blog.cloudflare.com/zh-cn/introducing-1-1-1-1-for-families/)

## 16.2　BIND

*难度：进阶 ｜ 篇幅：8.0 KB ｜ 所属：DNS*

将要讲到：安装、配置、权威 DNS 服务器、配置 DNSSEC、自动监听新的网络接口、在 chroot 环境运行 BIND

伯克利互联网名称服务 (Berkeley Internet Name Daemon，简称 [BIND](https://www.isc.org/downloads/bind/)) 是 DNS 协议的一个参考实现。

> **注意：**开发 BIND 的组织在发现安全漏洞之后，会先通知付费客户，四天以后才会通知 Linux 发行版和大众。

## 安装

[安装](../../01_安装与包管理/包管理器/Pacman.md)软件包 [bind](https://archlinux.org/packages/?name=bind)包。

要使用 BIND 提供系统 DNS 服务，修改 [resolv.conf](域名解析.md)，将`nameserver 127.0.0.1`放到最前面。

开始/启用 `named.service` 服务。

## 配置

BIND 的配置文件是 `/etc/named.conf`. `named.conf` man 手册页介绍了所有选项。

Reload the `named.service` unit 以应用配置变更.

### 只允许本地访问

如果希望只允许本地网络访问，编辑 `/etc/named.conf` 并将这行配置加入到 **options** 区域。

```
listen-on { 127.0.0.1; };
```

### DNS 转发

要将 DNS 请求请求转发到上游 DNS 服务器（例如说您的 ISP 的服务器，或者 Google、OpenNIC 等知名的服务）。将下面字段加入配置文件的 options 中。.

```
 forwarders { 8.8.8.8; 8.8.4.4; };
```

不要忘记重启 `named.service` 服务。

## 权威 DNS 服务器

以下为一个设置权威域的简单教程。在这个示例中，我们的权威域名为 "domain.tld"。

更详尽的教程参见 [Two-in-one DNS server with BIND9](https://www.howtoforge.com/two_in_one_dns_bind9_views).

### 1\. 创建一个 zonefile

```
# nano /var/named/domain.tld.zone
```

```
$TTL 7200
; domain.tld
@       IN      SOA     ns01.domain.tld. postmaster.domain.tld. (
                                        2007011601 ; Serial
                                        28800      ; Refresh
                                        1800       ; Retry
                                        604800     ; Expire - 1 week
                                        86400 )    ; Minimum
                IN      NS      ns01
                IN      NS      ns02
ns01            IN      A       0.0.0.0
ns02            IN      A       0.0.0.0
localhost       IN      A       127.0.0.1
@               IN      MX 10   mail
imap            IN      CNAME   mail
smtp            IN      CNAME   mail
@               IN      A       0.0.0.0
www             IN      A       0.0.0.0
mail            IN      A       0.0.0.0
@               IN      TXT     "v=spf1 mx"
```

$TTL 定义了默认的 TTL (time-to-live), 单位为秒。在这个例子中，默认 TTL 为 2 小时。

每次修改 zonefile 的时候，都需要将 Serial (序列号) 加一再重启 **named**。只有当新的 Serial 比最后传输的域的序列号大的时候，从服务器才会请求传输新的域。

### 2\. 配置主服务器

将您的 zone 文件加到 `/etc/named.conf`:

```
zone "domain.tld" IN {
        type master;
        file "domain.tld.zone";
        allow-update { none; };
        notify no;
};
```

重新加载 `named.service` 服务。

### 3\. 将其设置为默认 DNS 服务器

如果您自己已经在运行 DNS 服务器的话，可以考虑同时将其用来处理 DNS 查询请求。服务器必须支持 **recursive** (递归) 查询。为了防止 [DNS 放大攻击](https://www.us-cert.gov/ncas/alerts/TA13-088A)，许多 DNS 解析程序都默认禁用了递归功能。Arch 的默认 `/etc/named.conf` 配置只允许本机地址使用递归：

```
allow-recursion { 127.0.0.1; };
```

[resolv.conf](域名解析.md) 配置文件必须包含 127.0.0.1 地址以使用您的 DNS 服务器。 参见 [Domain name resolution#Overwriting of /etc/resolv.conf](域名解析.md#Overwriting_of_/etc/resolv.conf) 以了解确保这个文件不会被覆盖的方法。

如果您想为您的局域网提供 DNS 服务的话（例如 192.168.0 IP 段），您必须将对应的 IP 段加入到 `/etc/named.conf` 中：

```
allow-recursion { 192.168.0.0/24; 127.0.0.1; };
```

## 配置 DNSSEC

- [https://www.dnssec.net/practical-documents](https://www.dnssec.net/practical-documents)
    - [https://www.cymru.com/Documents/secure-bind-template.html](https://www.cymru.com/Documents/secure-bind-template.html) **(configuration template!)**
    - [https://www.bind9.net/manuals](https://www.bind9.net/manuals)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2021-11-07 ⓘ\]
    - [https://www.bind9.net/BIND-FAQ](https://www.bind9.net/BIND-FAQ)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2021-11-07 ⓘ\]
- [https://web.archive.org/web/20100528023716/http://blog.techscrawl.com:80/2009/01/13/enabling-dnssec-on-bind/](https://web.archive.org/web/20100528023716/http://blog.techscrawl.com:80/2009/01/13/enabling-dnssec-on-bind/)
- Or use an external mechanisms such as OpenDNSSEC (fully-automatic key rollover)

## 自动监听新的网络接口

BIND 会每个每隔几个小时扫描新的网络接口并停止在已经不再不存在的上监听。如果您想修改这个时间的话，可以在 `/etc/named.conf` 中增加这个项：

```
interface-interval <扫描间隔>;
```

最大间隔为 28 天 (40230 分钟)。

如果需要禁用这个功能的话，可以将时间值设置为 **0**。

最后，请重启服务。

## 在 chroot 环境运行 BIND

在 \[chroot\] 环境运行可以提高安全性。

### 创建 Jail House

首先，我们需要创建一个 jail。我们可以使用 `/srv/named`, 并将相关文件都放到里面去。

```
 mkdir -p /srv/named/{dev,etc,usr/lib/engines,var/{run,log,named}}
 # Copy over required system files
 cp -av /etc/{localtime,named.conf} /srv/named/etc/
 cp -av /usr/lib/engines-1.1/* /srv/named/usr/lib/engines/
 cp -dfprv /var/named /srv/named/var/
 # Set up required dev nodes
 mknod /srv/named/dev/null c 1 3
 mknod /srv/named/dev/random c 1 8
 # Set Ownership of the files
 chown -R named:named /srv/named
```

这些步骤可以配置 jail 的文件系统。

### 服务文件

接下来我们需要创建服务文件 (service file)，以强制 BIND 在 chroot 环境中启动。

```
 cp -av /usr/lib/systemd/system/named.service /etc/systemd/system/named-chroot.service
```

我们需要修改 service 启动 BIND 的方法。

```
/etc/systemd/system/named-chroot.service
```

```
  ExecStart=/usr/bin/named -4 -f -u named -t "/srv/named"
```

}

最后，重新加载 systemd `systemctl daemon-reload`。然后，启动 `named-chroot.service`。

## 参见

- [BIND 9 Administrator Reference Manual](https://www.isc.org/downloads/bind/doc/)
- [BIND 9 DNS Administration Reference Book](http://www.reedmedia.net/books/bind-dns/)
- [DNS and BIND by Cricket Liu and Paul Albitz](https://www.oreilly.com/library/view/dns-and-bind/0596100574)
- [Pro DNS and BIND](http://www.netwidget.net/books/apress/dns/intro.html)
- [Internet Systems Consortium, Inc. (ISC)](https://www.isc.org/)
- [DNS Glossary](http://www.menandmice.com/knowledgehub/dnsglossary)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2021-11-07 ⓘ\]
- [Archived mailing list discussion on BIND's future](https://lists.archlinux.org/archives/list/arch-dev-public@lists.archlinux.org/thread/RR3TKD5ZWTYKQHQBFPHTHDLN3JKUOOWV/)

## 16.3　Unbound

*难度：进阶 ｜ 篇幅：18.6 KB ｜ 所属：DNS*

将要讲到：安装、配置、使用、提示与技巧、疑难解答、参阅

[Unbound](https://unbound.net/) 是一个具有验证，递归和缓存等功能的 DNS 解析器。根据[Wikipedia](https://en.wikipedia.org/wiki/Unbound_(DNS_Server))：

: Unbound has supplanted the Berkeley Internet Name Domain ([BIND](BIND.md)) as the default, base-system name server in several open source projects, where it is perceived as smaller, more modern, and more secure for most applications.

## 安装

安装 [unbound](https://archlinux.org/packages/?name=unbound)包 软件包。 此外, [expat](https://archlinux.org/packages/?name=expat)包 是使用DNSSEC验证请求所必须的。

## 配置

默认配置已经位于`/etc/unbound/unbound.conf`文件中。此外，`/etc/unbound/unbound.conf.example`文件包含了其他的可配置设置项，并以注释的形式给出了示范设置。以下章节重点解释和默认配置文件不同的设置项。如需了解更多细节，参见[unbound.conf(5)](https://man.archlinux.org/man/unbound.conf.5)。

除非特别声明，这一节列出的选项都是放置在配置文件的`server`节中，类似这样：

```
/etc/unbound/unbound.conf
```

```
server:
  ...
  setting: value
  ...
```

> **注意：**请确保你的配置文件中已经设置了`do-daemonize: no`,否则`unbound.service`会无法启动.

### 本地DNS服务器

如果你想要使用*unbound*作为本地DNS服务器，请把[resolv.conf](域名解析.md)中的域名服务器（nameserver）设置到回环地址`::1`和`127.0.0.1`。你可能想要让你的配置[Domain name resolution#覆盖 /etc/resolv.conf](域名解析.md#覆盖_/etc/resolv.conf)。

> **提示：**实现这个目的的一个简便方法是安装openresolv，然后取消文件`/etc/resolvconf.conf`中包含`name_servers="::1 127.0.0.1"`的那一行的注释。然后运行`resolvconf -u`来重新生成`/etc/resolv.conf`。

要了解如何测试设置项，参见[Domain name resolution#Lookup utilities](域名解析.md#Lookup_utilities)。

在把[resolv.conf](域名解析.md)中的设置更改为持久化设置后，请特别注意检查正在使用的服务器是`::1`或`127.0.0.1`。

你还需要对“unbound”进行设置，以使它[#转发查询](#转发查询)到你所选择的DNS服务器。

### 访问控制

你可以通过IP地址来指定响应请求的端口。默认监听的是*localhost*。

为了在所有端口上监听，使用以下配置：

```
interface: 0.0.0.0
```

为了通过IP地址来控制可以访问服务器的系统，使用`access-control`选项：

```
access-control: subnet action
```

例如:

```
access-control: 192.168.1.0/24 allow
```

*action*可以是`deny` (drop message), `refuse` (polite error reply), `allow` (recursive ok), or `allow_snoop` (recursive and nonrecursive ok)中的任意一个。默认除了localhost之外的所有东西都会被拒绝。

### 使用DNS over TLS进行转发

为了使用这个功能，你需要设置`tls-cert-bundle`选项来指定本地系统的根证书认证包，以使得unbound可以转发TLS请求并指定允许DNS over TLS的服务器数量。

对每个服务器你都需要用 @ 来指定连接的端口，同时你也要用 # 来指明它的域名是什么。虽然它看起来像注释，the hashtag name allows for the TLS authentication name to be set for stub-zones and with `unbound-control forward control` command。在 @ 和 # 之间不应该有空格。

```
/etc/unbound/unbound.conf
```

```
...
server:
...
	tls-cert-bundle: /etc/ssl/certs/ca-certificates.crt
...
forward-zone:
        name: "."
        forward-tls-upstream: yes
        forward-addr: 1.1.1.1@853#cloudflare-dns.com
```

### 根域名服务器

为了查询一个没有被缓存成地址的主机，解释器需要从服务器树的根开始、对根服务器进行请求来知道去哪里找到目标地址的顶级域名。Unbound内置了一些根节点，但是推荐你提供一个根节点文件给它以免内置的过于老旧。

首先，告诉*unbound*使用`root.hints`文件：

```
root-hints: root.hints
```

然后把你的*root hints*文件放进*unbound*的配置文件夹。实现这个目标最简单的方法是运行下面的命令：

```
# curl -o /etc/unbound/root.hints https://www.internic.net/domain/named.cache
```

建议每六个月更新一次`root.hints`来保持根服务器列表是最新的。你可以手动完成这个任务，也可以使用[Systemd/Timers](../../02_系统管理与内核/系统管理/Systemd_定时器.md)。详情参见[#根域名服务器与systemd timer](#根域名服务器与systemd_timer)。

### DNSSEC验证


为了使用DNSSEC验证，你需要在`server:`节中添加以下设置来告诉*unbound*服务器根证书文件的位置：

```
/etc/unbound/unbound.conf
```

```
trust-anchor-file: trusted-key.key
```

`/etc/unbound/trusted-key.key`是从依赖项[dnssec-anchors](https://archlinux.org/packages/?name=dnssec-anchors)包所提供的的`/etc/trusted-key.key`复制而来的，它的[PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md)按照[unbound-anchor(8)](https://man.archlinux.org/man/unbound-anchor.8)生成了`/etc/trusted-key.key`。

如果总的[#转发查询](#转发查询)设置到了不支持DNSSEC的DNS服务器，那么请确保已经把这些DNS服务器注释掉，否则DNS请求会失败。DNSSEC验证只会在被请求的DNS服务器支持它的时候成功完成。

> **注意：**如果使用DNSSEC，在地址被缓存之前DNS查询时间将会显著增加。

#### 测试DNSSEC

为了测试DNSSEC是否工作，在starting `unbound.service`之后：

```
$ unbound-host -C /etc/unbound/unbound.conf -v sigok.verteiltesysteme.net
```

得到的回应应该是附带`(secure)`字样的ip地址。

```
$ unbound-host -C /etc/unbound/unbound.conf -v sigfail.verteiltesysteme.net
```

这次的回应应该包含`(BOGUS (security failure))`字样。

另外你也可以使用“drill”来测试：

```
$ drill sigok.verteiltesysteme.net
$ drill sigfail.verteiltesysteme.net
```

第一个命令应该返回`NOERROR`的`rcode`；而第二个命令应该返回`SERVFAIL`的`rcode`。

### 转发查询

如果你只想转发请求到外部的DNS服务器，请跳到[#转发所有其余的请求](#转发所有其余的请求)。

#### 允许本地网络使用DNS

##### 使用openresolv

如果你的网络管理器支持openresolv，你可以通过设置来使它提供本地DNS服务器、使用unbound来查询域名。 

```
/etc/resolvconf.conf
```

```
...
private_interfaces="*"

# Write out unbound configuration file
unbound_conf=/etc/unbound/resolvconf.conf
```

运行`resolvconf -u`来生成文件。

配置unbound读取openresolv生成的文件并允许回应[private IP address ranges](https://en.wikipedia.org/wiki/Private_network)：

```
/etc/unbound/unbound.conf
```

```
include: "/etc/unbound/resolvconf.conf"
...
server:
...
   private-domain: "intranet"
   private-domain: "internal"
   private-domain: "private"
   private-domain: "corp"
   private-domain: "home"
   private-domain: "lan"

   unblock-lan-zones: yes
   insecure-lan-zones: yes
   ...
```

另外你可能想要对私有DNS域名空间禁用DNSSEC：

```
/etc/unbound/unbound.conf
```

```

...
server:
...
   domain-insecure: "intranet"
   domain-insecure: "internal"
   domain-insecure: "private"
   domain-insecure: "corp"
   domain-insecure: "home"
   domain-insecure: "lan"
...
```

##### 手动指定DNS服务器

如果你有一个需要DNS请求的本地网络，同时你想要把请求都转发给一个本地的DNS服务器，那么你需要添加这一行：

```
private-address: 本地子网/子网掩码
```

例如:

```
private-address: 10.0.0.0/24
```

> **注意：**你可以使用私有地址来防止DNS劫持攻击。为了达到这个目的，你可能需要允许RFC1918网络(10.0.0.0/8 172.16.0.0/12 192.168.0.0/16 169.254.0.0/16 fd00::/8 fe80::/10)。 Unbound的未来版本可能会默认启用这个功能。

###### 包含本地DNS服务器

为了包含一个本地DNS服务器，以用于转发和反代本地地址，类似下面的一组配置是必要的（请把下面的10.0.0.1替换为本地网络中提供DNS服务的服务器的地址）：

```
local-zone: "10.in-addr.arpa." transparent
```

上面这一行对于让反向查询正常工作是非常重要的。

```
forward-zone:
name: "mynetwork.com."
forward-addr: 10.0.0.1
forward-zone:
local-data: "1.0.0.127.in-addr.arpa. 10800 IN PTR localhost."
```

> **注意：**转发空间和根空间之间的区别是，根空间只有在直接连接到一个authoritative DNS服务器的时候才能正常工作。如果一个[BIND](BIND.md)DNS服务器提供authoritative DNS，那么这个特性对于来自于它的请求有用——但是如果你在把请求指向一个*unbound*服务器（内部查询都被转发到另一个DNS服务器），那么把这个指向定义为该机器上的根空间是不会起作用的。在这种情况下，你必须把它定义为上面所说的转发空间，因为转发空间可以通过链式查询去到别的DNS服务器上，亦即转发空间可以把请求指向递归DNS服务器。这个区别是很重要的，因为如果你不恰当地使用根空间，你不会得到能够说明问题的错误信息。

你可以通过下面的配置来设定localhost的前向和反向查询：

```
local-zone: "localhost." static
local-data: "localhost. 10800 IN NS localhost."
local-data: "localhost. 10800 IN SOA localhost. nobody.invalid. 1 3600 1200 604800 10800"
local-data: "localhost. 10800 IN A 127.0.0.1"
local-zone: "127.in-addr.arpa." static
local-data: "127.in-addr.arpa. 10800 IN NS localhost."
local-data: "127.in-addr.arpa. 10800 IN SOA localhost. nobody.invalid. 2 3600 1200 604800 10800"
local-data: "1.0.0.127.in-addr.arpa. 10800 IN PTR localhost."
```

#### 转发所有其余的请求

##### 使用openresolv

如果你的网络管理器支持openresolv，你可以通过配置使它提供上游DNS服务器给unbound。 

```
/etc/resolvconf.conf
```

```
...
# Write out unbound configuration file
unbound_conf=/etc/unbound/resolvconf.conf
```

运行`resolvconf -u`来生成文件。

最后配置unound读取openresolv生成的文件：

```
include: "/etc/unbound/resolvconf.conf"
```

##### 手动指定DNS服务器

为了使本地机器之外的、本地网络外部的默认转发区域使用指定的服务器，请在配置文件中添加一个名字是`.`的转发区域。在这个例子里，所有的请求都被转发到谷歌的DNS服务器：

```
forward-zone:
  name: "."
  forward-addr: 8.8.8.8
  forward-addr: 8.8.4.4
```

## 使用

### 启动unbound

Start/enable `unbound.service` systemd服务。

### 远程控制unbound

*unbound*安装的时候自带了`unbound-control`工具，利用这个工具我们可以远程控制unbound服务器。它和[pdnsd](https://archlinux.org/packages/?name=pdnsd)包的pdnsd-ctl命令很类似。

#### 配置unbound-control

在能够使用它之前你需要做下面的事情：

1) 首先，运行：

```
# unbound-control-setup
```

来为你的服务器和客户端生成一个self-signed的证书和private key。生成的文件位于`/etc/unbound`文件夹。

2) 然后，把下面的内容放进`/etc/unbound/unbound.conf`文件。`control-enable: yes`是一定要有的，其余的内容可以按照所需进行调整。

```
remote-control:
   # Enable remote control with unbound-control(8) here.
   # 用unbound-control-setup生成的keys and certificates进行配置。
   control-enable: yes
   # 设定监听哪个地址.
   # give 0.0.0.0 and ::0 to listen to all interfaces.
   control-interface: 127.0.0.1
   # 远程控制用的端口.
   control-port: 8953
   # unbound server key file.
   server-key-file: "/etc/unbound/unbound_server.key"
   # unbound server certificate file.
   server-cert-file: "/etc/unbound/unbound_server.pem"
   # unbound-control key file.
   control-key-file: "/etc/unbound/unbound_control.key"
   # unbound-control certificate file.
   control-cert-file: "/etc/unbound/unbound_control.pem"
```

#### 使用unbound-control

下面是*unbound-control*可以使用的一部分命令：

- 不重置数据的情况下查看统计数据

```
 # unbound-control stats_noreset
```

- 把cache dump到stdout

```
 # unbound-control dump_cache
```

- 清空cache并且重新加载配置

```
 # unbound-control reload
```

请参考[unbound-control(8)](https://man.archlinux.org/man/unbound-control.8)来了解*unbound-control*支持的操作。

## 提示与技巧

### 域名黑名单

你可以打开这个网页[adservers](https://pgl.yoyo.org/adservers/serverlist.php?hostformat=unbound&showintro=0&startdate%5Bday%5D=&startdate%5Bmonth%5D=&startdate%5Byear%5D=&mimetype=plaintext)，把它的内容保存到`/etc/unbound/adservers`，然后把下面的配置直接添加到unbound配置文件里就可以了：

```
/etc/unbound/unbound.conf
```

```
server:
...
  include: /etc/unbound/adservers
```

> **提示：**
>
> - 为了在查询这些hosts的时候返回OK状态指示，你可以更改默认的127.0.0.1重定向，改成重定向到你所控制的服务器并让那台服务器返回空的204回应，参考
> - 如果需要把其他格式的hosts文件转换成unbound的格式的，请运行这个命令:
>
> ```
> $ grep '^0\.0\.0\.0' hostsfile | awk '{print "local-zone: \""$2"\" always_nxdomain"}' > /etc/unbound/adservers
> ```

### 添加一个authoritative DNS服务器

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 同时运行两个DNS服务器并不一定比只运行一个提供所有功能的DNS服务器更安全。 （在 [Talk:Unbound](https://wiki.archlinuxcn.org/wiki/Talk:Unbound) 中讨论）

对于想要在一台机器上同时两个DNS服务器（一个是提供验证、递归、缓存功能的DNS服务器，另一个是authoritative DNS服务器）的用户来说，参考NSD的维基页面可能会有所帮助。那个页面提供了一个示范配置。一个服务器专门响应authoritative DNS请求，另一个服务器提供验证、递归、缓存等DNS功能，这样会比一个服务器提供所有功能会更安全。很多用户已经在使用Bind作为DNS服务器，而针对从Bind变成Bind和NSD协同工作的过程的帮助在NSD页面有提供。

### WAN facing DNS

通过更改配置文件和服务器所监听的接口（地址）来允许来自本地网络之外的机器的DNS请求进入本地网络（LAN）内的某台特定机器，这个想法是可行的。这个功能对于公开的网站服务器和邮件服务器是非常有用的。这个在bind上已经实现了多年的技术，通过正确配置防火墙机器上的端口转发——转发这些请求到正确的机器上——也可以在unbound上实现。

### 根域名服务器与systemd timer

下面是一个systemd服务和timer的示例文件，它用来每隔一个月更新一次`root.hints`，所用的方法与[#根域名服务器](#根域名服务器)中的相同：

```
/etc/systemd/system/roothints.service
```

```
[Unit]
Description=Update root hints for unbound
After=network.target

[Service]
ExecStart=/usr/bin/curl -o /etc/unbound/root.hints https://www.internic.net/domain/named.cache
```

```
/etc/systemd/system/roothints.timer
```

```
[Unit]
Description=Run root.hints monthly

[Timer]
OnCalendar=monthly
Persistent=true

[Install]
WantedBy=timers.target
```

最后Start/enable `roothints.timer` systemd timer就可以了。

## 疑难解答

### 有关num-threads的问题

`unbound.conf`的man page提到：

```
     outgoing-range: <number>
           Number of ports to open. This number of file  descriptors  can  be  opened  per thread.
```

网上的一些人建议`num-threads`这个参数应该设置成你的CPU的核心数量。示范配置文件`unbound.conf.example`里关于这个选项只有下面这两行：

```
       # number of threads to create. 1 disables threading.
       # num-threads: 1
```

但是人为地把`num-threads`提高到比`1`就一定会造成*unbound*在启动的时候在log里写一个warning提示说exceeding the number of file descriptors。实际上对于大多数在小型网络或是单机上运行unbound的用户来说，通过让`num-threads`超过`1`来得到性能提升是徒劳的。如果你一定要这么做，那么请参考[official documentation](https://nlnetlabs.nl/documentation/unbound/howto-optimise/)。下面这条经验法则应该对你有所帮助：

: *Set `num-threads` equal to the number of CPU cores on the system. E.g. for 4 CPUs with 2 cores each, use 8.*

把`outgoing-range`设置得尽可能大，参考上面的链接来突破总数是`1024`这个限制。这样就会使得unbound可以同时为更多客户端提供服务。1个核心设置`950`，2个核心设置`450`，四个核心设置`200`。`num-queries-per-thread`最好设置成`outgoing-range`的一半。

因为`outgoing-range`是有限制的，同时`num-queries-per-thread`也因此受到了限制，所以最好在编译的时候带上[libevent](https://archlinux.org/packages/?name=libevent)包，这样就不会有`1024`限制了。如果你有一个高负荷DNS服务器使得你不得不这样编译，你需要从源码编译unbound而不是直接安装[unbound](https://archlinux.org/packages/?name=unbound)包。

## 参阅

- [Fedora change to Unbound](https://fedoraproject.org/wiki/Changes/Default_Local_DNS_Resolver)
- [Block hosts that contain advertisements](https://github.com/jodrell/unbound-block-hosts/)

## 16.4　域名解析

*难度：高级 ｜ 篇幅：22.5 KB ｜ 所属：DNS*

将要讲到：名称服务转换（NSS）、Glibc 解析器、查找实用工具、解析器性能、隐私与安全、第三方 DNS 服务

通常，一个[域名](https://en.wikipedia.org/wiki/Domain_name)代表了一个 IP 地址，并在[域名系统](https://en.wikipedia.org/wiki/Domain_Name_System)（Domain Name System，DNS）中与其关联。本文将介绍如何配置域名解析和解析域名。

## 名称服务转换（NSS）

[名称服务转换](https://en.wikipedia.org/wiki/Name_Service_Switch)（Name Service Switch，NSS）工具是 GNU C 库（[glibc](https://archlinux.org/packages/?name=glibc)包）的一部分，支持 [getaddrinfo(3)](https://man.archlinux.org/man/getaddrinfo.3) API，用于解析域名。NSS 允许系统数据库由单独的服务提供，其搜索顺序可由管理员在 [nsswitch.conf(5)](https://man.archlinux.org/man/nsswitch.conf.5) 中配置。负责响应域名解析的数据库是 *hosts* 数据库，glibc 为此提供了下列服务：

- *files*（文件）：读取 `/etc/hosts` 文件，参见 [hosts(5)](https://man.archlinux.org/man/hosts.5)
- *dns*：读取 `/etc/resolv.conf` 的 [glibc 解析器](#Glibc_解析器)，参见 [resolv.conf(5)](https://man.archlinux.org/man/resolv.conf.5)

[systemd](../../01_安装与包管理/初始化/Systemd.md) 为主机名解析提供了三项 NSS 服务：

- [nss-resolve(8)](https://man.archlinux.org/man/nss-resolve.8)——缓存 DNS 存根解析器，[systemd-resolved](../网络/Systemd-resolved.md) 一文中有描述
- [nss-myhostname(8)](https://man.archlinux.org/man/nss-myhostname.8)——无需编辑 `/etc/hosts` 即可提供本地主机名解析，[网络配置#Local hostname resolution](../网络/网络配置.md#Local_hostname_resolution) 一文中有描述
- [nss-mymachines(8)](https://man.archlinux.org/man/nss-mymachines.8)——为本地 [systemd-machined(8)](https://man.archlinux.org/man/systemd-machined.8) 容器的名称提供本地主机名解析

### 使用 NSS 解析域名

NSS 数据库可使用 [getent(1)](https://man.archlinux.org/man/getent.1) 查询。可以使用以下命令通过 NSS 解析域名：

```
$ getent ahosts domain_name（域名）
```

> **注意：**虽然大多数程序使用 NSS 解析域名，但是有些程序可能会直接读取 `/etc/resolv.conf` 和/或 `/etc/hosts`。参见 [网络配置#局域网主机名解析](../网络/网络配置.md#局域网主机名解析)。

## Glibc 解析器

### 覆盖 /etc/resolv.conf

[网络管理器](../网络/网络配置.md)们会有意覆盖 `/etc/resolv.conf`，具体细节请参见相应文章章节：

- [dhcpcd#/etc/resolv.conf](../网络配置/Dhcpcd.md#/etc/resolv.conf)
- [Netctl#/etc/resolv.conf](../网络管理器/Netctl.md#/etc/resolv.conf)
- [NetworkManager#/etc/resolv.conf](../../06_软件与应用/红帽/NetworkManager.md#/etc/resolv.conf)
- [ConnMan#/etc/resolv.conf](../网络管理器/ConnMan.md#/etc/resolv.conf)

要防止程序覆盖 `/etc/resolv.conf`，还可以通过设置不可变文件属性来为其建立写保护：

```
# chattr +i /etc/resolv.conf
```

> **提示：**如果想要多个进程写入 `/etc/resolv.conf`，可以使用 resolvconf。

#### 使用 nmcli 的替代方案

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[NetworkManager#/etc/resolv.conf](../../06_软件与应用/红帽/NetworkManager.md#/etc/resolv.conf)。**
>
> **附注：** NetworkManager 有专门的章节来介绍这一主题。（在 [Talk:域名解析](https://wiki.archlinuxcn.org/wiki/Talk:%E5%9F%9F%E5%90%8D%E8%A7%A3%E6%9E%90) 中讨论）

如果使用 [NetworkManager](../../06_软件与应用/红帽/NetworkManager.md)，可以使用 [nmcli(1)](https://man.archlinux.org/man/nmcli.1) 为 `/etc/resolv.conf` 设置持久选项。将下面的*Wired*改为连接名称。例如

```
# nmcli con mod Wired +ipv4.dns-options 'rotate,single-request,timeout:1'
```

有关更多选项，请参阅手册 [nmcli(1)](https://man.archlinux.org/man/nmcli.1)、[nm-settings-nmcli(5)](https://man.archlinux.org/man/nm-settings-nmcli.5) 和 [resolv.conf(5)](https://man.archlinux.org/man/resolv.conf.5)。

### 限制查找时间

如果您遇到了非常长时间查找主机名的情况（可能是在 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 里或者在浏览网页时），定义一个小的超时时间通常能对问题有所帮助，在此之后系统会使用备用域名服务器（nameserver）。为此，请在 `/etc/resolv.conf` 中添加以下内容。

```
/etc/resolv.conf
```

```
options timeout:1
```

### IPv6 主机名查找延迟

如果在解析主机名时遇到 5 秒延迟的情况，原因可能是 DNS 服务器/防火墙行为不当，只对并行的 A 和 AAAA 请求给出了一个回应。 可以在 `/etc/resolv.conf` 中设置以下选项来修复问题：

```
/etc/resolv.conf
```

```
options single-request
```

### 本地域名

为了能够使用本地机器名称的主机名，而无需使用格式完全合格的域名，请在 `/etc/resolv.conf` 中添加一行本地域名，如：

```
/etc/resolv.conf
```

```
search example.org
```

这样当使用 *ssh* 命令时，可以把 `mainmachine1.example.org` 这样的本地主机简单地称为 `mainmachine1`，不过使用 [drill](#查找实用工具) 命令时仍需要格式完全合格的域名以便查找。

## 查找实用工具

要查询特定的 DNS 服务器和 DNS/DNSSEC 记录，可以使用专用 DNS 查询实用工具。这些工具自身实现了 DNS 功能，不使用 [NSS](#名称服务转换（NSS）)。

[ldns](https://archlinux.org/packages/?name=ldns)包 软件包提供了 [drill(1)](https://man.archlinux.org/man/drill.1) 命令，是一个旨在检索 DNS 信息的工具。

例如，要用 *drill* 查询特定名称服务器（nameserver）获取一个域名的 TXT 记录，可执行：

```
$ drill @nameserver TXT domain（域名）
```

除非指定了一个 DNS 服务器，否则 *drill* 将使用 `/etc/resolv.conf` 中定义的名称服务器。

> **提示：**一些 DNS 服务器附带它们自己的 DNS 查找实用工具。例如：
>
> - [knot](https://archlinux.org/packages/?name=knot)包 软件包提供了 [khost(1)](https://man.archlinux.org/man/khost.1) 和 [kdig(1)](https://man.archlinux.org/man/kdig.1) 命令。
> - [Unbound](Unbound.md) 有 [unbound-host(1)](https://man.archlinux.org/man/unbound-host.1)。
> - [BIND](BIND.md) 有 [dig(1)](https://man.archlinux.org/man/dig.1)、[host(1)](https://man.archlinux.org/man/host.1)、[nslookup(1)](https://man.archlinux.org/man/nslookup.1) 和一堆 `dnssec-` 工具。
> - [systemd-resolved](../网络/Systemd-resolved.md) 有 [resolvectl(1)](https://man.archlinux.org/man/resolvectl.1)，它提供了用于DNS 查找的`query`子命令。

## 解析器性能

Glibc 不缓存查询结果。要实现本地缓存，请使用 [systemd-resolved](../网络/Systemd-resolved.md) 或者配置一个本地缓存 [DNS 服务器](#DNS_服务器)将其用作服务器，方法是在 `/etc/resolv.conf`（如果使用 openresolv，则在 `/etc/resolvconf.conf`）中设置 `127.0.0.1` 和 `::1` 为名称服务器。

> **提示：**
>
> - *drill* 或 *dig* [查找实用工具](#查找实用工具)能报告查询时间。
> - 路由器通常将自己的缓存解析器设置为网络的 DNS 服务器，从而为整个网络提供 DNS 缓存。
> - 如果切换到下一个 DNS 服务器的时间过长，可以尝试[减少超时时间](#限制查找时间)。

## 隐私与安全

传统的 DNS 协议是没有加密的，并且不会考虑传输的机密性、完整性和身份验证问题，所以如果您在使用不受信任的网络或者有恶意行为的网络服务供应商（ISP），您的 DNS 查询可能会被窃听并响应[他人的操纵](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)。此外，DNS 服务器可进行 [DNS 劫持](https://en.wikipedia.org/wiki/DNS_hijacking)。

您需要相信您的 DNS 服务器处理您的查询时能够保密。网络服务供应商和[第三方](#第三方_DNS_服务)会提供 DNS 服务器。 另外您也可以运营自己的[递归名称服务器](#DNS_服务器)，但这需要花费更多精力。 如果您在不受信任的网络中使用 DHCP 客户端，请一定要设置静态名称服务器，以避免使用和受制于任意 DNS 服务器。 为了保证您与远程 DNS 服务器的通信安全，您可以使用加密协议，例如 [DNS over TLS](https://en.wikipedia.org/wiki/DNS_over_TLS)（[RFC 7858](https://tools.ietf.org/html/rfc7858)）、[DNS over HTTPS](https://en.wikipedia.org/wiki/DNS_over_HTTPS)（[RFC 8484](https://tools.ietf.org/html/rfc8484)）或 [DNSCrypt](https://en.wikipedia.org/wiki/DNSCrypt)，前提是上游服务器和您的解析器都支持该协议。 另一种方法是使用专用软件来加密和解密通信，例如 stunnel。 要验证响应是否来自权威的名称服务器，您可以验证 DNSSEC 记录，前提是上游服务器和您的[解析器](#DNS_服务器)都支持它。

虽然可以使用加密 DNS 解析器，但浏览器在请求网站证书时仍会泄漏[服务器名称指示（Server Name Indication, SNI）](https://en.wikipedia.org/wiki/Server_Name_Indication)中的域名。可以使用 [Wireshark](../../08_安全与加密/安全/Wireshark.md) 过滤器 `tls.handshake.extensions_server_name_len > 0` 或者下面的命令检查这种泄漏。建议的解决方案是使用[ECH (Encrypted Client Hello)](https://en.wikipedia.org/wiki/Server_Name_Indication#Encrypted_Client_Hello)，这是 TLS 1.3 协议的扩展。

```
# tshark -p -Tfields -e tls.handshake.extensions_server_name -Y 'tls.handshake.extensions_server_name_len>0'
```

### 应用级 DNS

值得注意的是，一些客户端软件，例如主流的网页浏览器，都在开始实现 DNS over HTTPS 功能。虽然一般来说，提供加密查询可以视为一种善举，但这也同时意味着软件可以绕过系统解析器配置，另辟蹊径进行查询。

[Firefox](../../06_软件与应用/Mozilla/Firefox.md) 提供了[配置选项](https://support.mozilla.org/en-US/kb/firefox-dns-over-https#w_manually-enabling-and-disabling-dns-over-https)来启用或禁用 DNS over HTTPS 和选择 DNS 服务器。Mozilla 设立了[可信递归解析器 (TRR)](../../06_软件与应用/网页浏览器/Firefox_隐私.md#禁用/强制使用“受信递归解析器”) 计划，并提供了默认提供商的透明度信息。这里要注意，Firefox 支持并自动为 TRR 提供商启用 ECH，请参阅 [Firefox/隐私#加密客户端问候](../../06_软件与应用/网页浏览器/Firefox_隐私.md#加密客户端问候)。

[Chromium](../../06_软件与应用/Google/Chromium.md) 会检查用户的系统解析器，如果得知系统解析器地址也提供 DNS over HTTPS 支持，则启用 DNS over HTTPS 功能。请参见[这篇博文](https://blog.chromium.org/2020/05/a-safer-and-more-private-browsing-DoH.html)获取更多信息，了解如何禁用 DNS over HTTPS。

Mozilla [提议](https://support.mozilla.org/en-US/kb/configuring-networks-disable-dns-over-https)，如果系统解析器无法解析域名 `use-application-dns.net`，就普遍禁用应用级 DNS。目前，这一功能仅在 Firefox 中实现了。

### 遗忘式 DNS

遗忘式 DNS（Oblivious DNS，ODNS；[RFC:9230](https://tools.ietf.org/html/rfc9230)）是一套解决了若干 DNS 关切隐私问题的系统。参见 [Cloudflare 的文章（英文）](https://blog.cloudflare.com/oblivious-dns/)获取更多信息。

## 第三方 DNS 服务

> **注意：**在使用第三方 DNS 服务前，请检查其隐私政策，了解其如何处理用户数据。用户数据是有价值的，可以出售给其他各方。

现在有各种[第三方 DNS 服务](https://en.wikipedia.org/wiki/Public_recursive_name_server#List_of_public_DNS_service_operators)可用，其中有一些还有专用软件：

- **[cloudflared](Cloudflared.md)** — Clondflare DNS over HTTPS 的 DNS 客户端

: [https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy](https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy) || [cloudflared](https://archlinux.org/packages/?name=cloudflared)包

- **dingo** — Google DNS over HTTPS 的 DNS 客户端

: [https://github.com/pforemski/dingo](https://github.com/pforemski/dingo) || [dingo-git](https://aur.archlinux.org/packages/dingo-git/)AUR

- **opennic-up** — 可以自动更新为反应最迅速的 OpenNIC DNS 服务器

: [https://github.com/kewlfft/opennic-up](https://github.com/kewlfft/opennic-up) || [opennic-up](https://aur.archlinux.org/packages/opennic-up/)AUR

- **nextdns** — NextDNS 的 DNS-over-HTTPS 命令行客户端

: [https://github.com/nextdns/nextdns](https://github.com/nextdns/nextdns) || [nextdns](https://aur.archlinux.org/packages/nextdns/)AUR

可以使用 [dnsperftest](https://github.com/cleanbrowsing/dnsperftest) 从您所在位置来测试主流 DNS 解析器们的性能。[dnsperf.com](https://www.dnsperf.com/#!dns-resolvers) 提供了全球 DNS 服务供应商的基准。

## DNS 服务器

DNS 服务器有[权威式](https://en.wikipedia.org/wiki/Authoritative_name_server)和[递归式](https://en.wikipedia.org/wiki/Name_server#Recursive_query)。如果两个都不是，则称为**根解析器**，只将所有查询转发到其他的递归名称服务器。根解析器典型用途是在本地主机或网络上引入 DNS 缓存机制。请注意，同样的功能可以通过技术完全成熟的名称服务器来实现。本小节我们比较了可用的 DNS 服务器，如需更详细的比较，请参考[维基百科：DNS 服务器软件比较](https://en.wikipedia.org/wiki/Comparison_of_DNS_server_software)。


| 名称 | 软件包 | 功能兼容性 | resolvconf | 受支持的协议 |
| --- | --- | --- | --- | --- |
| [权威式](https://en.wikipedia.org/wiki/Authoritative_name_server) | [递归式](https://en.wikipedia.org/wiki/Name_server#Recursive_query) | [缓存式](https://en.wikipedia.org/wiki/Name_server#Caching_name_server) |  [验证](https://en.wikipedia.org/wiki/Domain_Name_System_Security_Extensions#The_lookup_procedure)<br>DNSSEC | [DNS](https://en.wikipedia.org/wiki/Domain_Name_System) | [DNSCrypt](https://en.wikipedia.org/wiki/DNSCrypt) | [DNS over TLS](https://en.wikipedia.org/wiki/DNS_over_TLS) | [DNS over HTTPS](https://en.wikipedia.org/wiki/DNS_over_HTTPS) | [DNS over QUIC](https://datatracker.ietf.org/doc/html/rfc9250) |
| [BIND](BIND.md) | [bind](https://archlinux.org/packages/?name=bind)包 | 是 | 是 | 是 | 是 | 是 | 是 | 否 | 服务器1 | 服务器 | [否](https://gitlab.isc.org/isc-projects/bind9/-/issues/3635) |
| CoreDNS | [coredns](https://aur.archlinux.org/packages/coredns/)AUR | 是 | 否 | 是 | 否 | 否 | 是 | 否 | 是 | 是 | [否](https://github.com/coredns/coredns/issues/5583) |
| [DNS-over-HTTPS](../网络/DNS-over-HTTPS.md) | [dns-over-https](https://archlinux.org/packages/?name=dns-over-https)包 | 否 | 否 | 否 | 否 | ? | ? | 否 | [否](https://github.com/m13253/dns-over-https/issues/104) | 是 | [否](https://github.com/m13253/dns-over-https/issues/129) |
| [Deadwood](https://maradns.samiam.org/deadwood/doc/Deadwood.html) ([MaraDNS](https://en.wikipedia.org/wiki/MaraDNS) recursor) | [maradns](https://aur.archlinux.org/packages/maradns/)AUR | 否 | 是 | 是 | 否 | 否 | 是 | 否 | 否 | 否 | 否 |
| [dnscrypt-proxy](../../08_安全与加密/加密/Dnscrypt-proxy.md) | [dnscrypt-proxy](https://archlinux.org/packages/?name=dnscrypt-proxy)包 | 否 | 否 | 是 | 否 | 否 | 服务器 | 解析器 | [否](https://github.com/DNSCrypt/dnscrypt-proxy/issues/1124) | 是 | ? |
| [dnsmasq](../DHCP/Dnsmasq.md) | [dnsmasq](https://archlinux.org/packages/?name=dnsmasq)包 | 部分2 | 否 | 是 | 是 | 是 | 否 | 否 | [否](https://lists.thekelleys.org.uk/pipermail/dnsmasq-discuss/2018q2/012131.html) | 否 | 否 |
| [dnsproxy](https://github.com/AdguardTeam/dnsproxy) | [dnsproxy](https://aur.archlinux.org/packages/dnsproxy/)AUR | 否 | 否 | 是 | [否](https://github.com/AdguardTeam/dnsproxy/issues/10) | 否 | 是 | 是 | 是 | 是 | 是 |
| Knot Resolver | [knot-resolver](https://archlinux.org/packages/?name=knot-resolver)包 | 否 | 是 | 是 | 是 | 否 | 是 | 否 | 是 | [服务器](https://knot-resolver.readthedocs.io/en/stable/daemon-bindings-net_tlssrv.html#dns-over-https-doh) | [否](https://gitlab.nic.cz/knot/knot-resolver/-/issues/548) |
| pdnsd | [pdnsd](https://archlinux.org/packages/?name=pdnsd)包 | 部分2 | 是 | 永久 | 否 | 是 | 是 | 否 | 否 | 否 | 否 |
| [PowerDNS Recursor](https://en.wikipedia.org/wiki/PowerDNS#Recursor) | [powerdns-recursor](https://archlinux.org/packages/?name=powerdns-recursor)包 | 否 | 是 | 是 | 是 | 是 | 是 | 否 | [部分](https://github.com/PowerDNS/pdns/pull/10338) | 否 | ? |
| Rescached | [rescached-git](https://aur.archlinux.org/packages/rescached-git/)AUR | 否 | 否 | 是 | 否 | [是](https://github.com/shuLhan/rescached-go#integration-with-openresolv) | 是 | 否 | 是 | 是 | 否 |
| [RouteDNS](https://github.com/folbricht/routedns) | [routedns-git](https://aur.archlinux.org/packages/routedns-git/)AUR | 否 | 否 | 是3 | [否](https://github.com/folbricht/routedns/issues/123) | 否 | 是 | 否 | 是 | 是 | 是 |
| [SmartDNS](https://github.com/pymumu/smartdns) | [smartdns](https://archlinux.org/packages/?name=smartdns)包 | 否 | 否 | 是 | [否](https://github.com/pymumu/smartdns/issues/19) | ? | 是 | 否 | 解析器 | 解析器 | 否 |
| Stubby | [stubby](https://archlinux.org/packages/?name=stubby)包 | 否 | 否 | 否 | 是 | 否 | 服务器 | 否 | 解析器 | [否](https://github.com/getdnsapi/stubby/issues/278) | [否](https://github.com/getdnsapi/stubby/issues/288) |
| [systemd-resolved](../网络/Systemd-resolved.md) | [systemd](https://archlinux.org/packages/?name=systemd)包 | 否 | 否 | 是 | 是 | 是 | 解析器和[有限的服务器](https://github.com/systemd/systemd/issues/4621#issuecomment-260050033) | 否 | 解析器 | [否](https://github.com/systemd/systemd/issues/8639) | [否](https://github.com/systemd/systemd/issues/23770) |
| [Unbound](Unbound.md) | [unbound](https://archlinux.org/packages/?name=unbound)包 | 部分 | 是 | 是3 | 是 | 是 | 是 | 服务器 | 是 | [服务器](https://github.com/NLnetLabs/unbound/issues/308) | [否](https://github.com/NLnetLabs/unbound/issues/743) |

1.  BIND 可以为 DNS over TLS 和 DNS over HTTPS 提供服务（请参阅 [tls{}](https://downloads.isc.org/isc/bind9/9.18.0/doc/arm/html/reference.html#tls) 和[listen-on](https://downloads.isc.org/isc/bind9/9.18.0/doc/arm/html/reference.html#interfaces))），但尚无法将查询转发到 DNS over TLS/DNS over HTTPS 上游。 *dig* 工具可以通过 DNS over TLS 和 DNS over HTTPS（使用 `+tls` 和 `+https` 选项）进行查询，但[没有任何证书检查](https://gitlab.isc.org/isc-projects/bind9/-/issues/3163)。
2.  根据[维基百科](https://en.wikipedia.org/wiki/Comparison_of_DNS_server_software#cite_note-masqauth-32)：对权威式名称服务器功能支持有限，是旨在为内部网络使用而不是公共互联网使用。
3.  支持使用 Redis 后端进行持久缓存。

### 仅权威式服务器

| 名称 | 软件包 |  DNSSEC | 地理负载均衡 |
| --- | --- | --- | --- |
| gdnsd | [gdnsd](https://archlinux.org/packages/?name=gdnsd)包 | 否 | 是 |
| [Knot DNS](https://en.wikipedia.org/wiki/Knot_DNS) | [knot](https://archlinux.org/packages/?name=knot)包 | 是 | [是](https://www.knot-dns.cz/docs/latest/singlehtml/#geoip-geography-based-responses) |
| [MaraDNS](https://en.wikipedia.org/wiki/MaraDNS) | [maradns](https://aur.archlinux.org/packages/maradns/)AUR | 否 | ？ |
| NSD | [nsd](https://archlinux.org/packages/?name=nsd)包 | 是 | 否 |
| PowerDNS | [powerdns](https://archlinux.org/packages/?name=powerdns)包 | 是 | 是 |

### 条件转发

在查询特定域名时，可以使用特定的 DNS 解析器。这在连接到虚拟专用网络（VPN）时特别有用，这样，对 VPN 网络的查询由 VPN 的 DNS 解析，而对普通互联网的查询仍由标准 DNS 解析器解析（分流）。这也可以用于本地网络中。

要实现这个目的，您需要使用[本地解析器](#DNS_服务器)，因为 glibc 并不支持这样做。

在动态环境中（笔记本和某种程度的台式机），需要根据所连接的网络来配置解析器。最好的方式是使用 openresolv，因为它支持多个订阅器。一些[网络管理器](../网络/网络配置.md)会通过 openresolv 或者直接通过配置解析器来支持此功能。NetworkManager [支持条件转发而无需 openresolv](../../06_软件与应用/红帽/NetworkManager.md#DNS_caching_and_conditional_forwarding)。

> **注意：**虽然您可以使用其他条件进行转发（比如源 IP 地址），但是“条件转发”一名似乎更用于描述“域名查询”这一条件。

## 另请参阅

- [Linux 网络管理员指南（英文）](https://www.tldp.org/LDP/nag2/x-087-2-resolv.html)
- [Debian 手册（英文）](https://www.debian.org/doc/manuals/debian-handbook/sect.hostname-name-service.en.html#sect.name-resolution)
- [RFC:7706](https://tools.ietf.org/html/rfc7706) - 通过在回环上运行一个根服务器来减少对根服务器的访问时间（Decreasing Access Time to Root Servers by Running One on Loopback，英文）
- [域名系统概览（英文）](http://linux-ip.net/pages/diagrams.html#domain-name-system-overview) - DNS 有关图表
- 备用 DNS 服务
- [What does DNS stand for](https://www.copahost.com/blog/what-does-dns-stand-for/)
