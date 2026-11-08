# 第 23 章　NTP

> 时钟跑偏的麻烦常以别的面目出现：证书校验失败、日志时间戳错乱、跨机任务莫名不同步，追到底才发现是时间在漂。本章要解决的，就是在三套并存的时间同步方案里选一套装好、配好，并让它长期稳定地跑下去。取舍也清楚：Systemd-timesyncd 篇幅最短、配置最少，单机与多数服务器装完即用，属必读且应先读；Chrony 在链路时断时续时收敛更快，还能把同步状态当作网络可用的信号，值得紧接着读；Network Time Protocol daemon 体量与排错篇幅最大，需要精细控制或深入诊断时再翻，普通场景可跳，但排错一节值得留着。
> 
> 最容易踩的坑是以为三者能同时启用：它们争的是同一份职责，装了两套就必须明确关掉多余的那一套，否则配置看似生效，实际并未接管。

<!-- 本章 3 节，共 33 KB -->

## 23.1　Systemd-timesyncd

*难度：进阶 ｜ 篇幅：7.6 KB ｜ 所属：NTP*

将要讲到：配置、使用、参阅

摘自 [systemd 邮件列表](https://lists.freedesktop.org/archives/systemd-devel/2014-May/019537.html)：

: *systemd-timesyncd* 是一个用于跨网络同步系统时钟的守护服务。它实现了一个 SNTP 客户端。与 [chrony](Chrony.md) 或 NTP 参考服务器等 NTP 实现相比，这个服务仅做了客户端实现，摒弃了完整 NTP 实现的复杂性。它只专注于从远程服务器查询然后同步到本地时钟。除非你打算为网络客户端提供 NTP 服务或者连接本地硬件时钟，否则这个简单的 NTP 客户端应该更适合大多数人。守护进程运行只需要最小特权，并且会跟网络服务 networkd 挂钩，仅在网络连接可用时才工作。每次收到一个新的 NTP 同步请求或每 60 秒时，后台服务就把当前时间保存到磁盘，并尽可能在系统启动时修正系统时间，这样处理的目的是为了适应像 Raspberry Pi 和嵌入式设备这种缺少 RTC 的系统，并确保这些系统时单点处理（即使它并不是总是正确的）。如果要使用这个守护进程，需要在安装系统时创建一个新的系统用户和组“systemd-timesync”。

## 配置

*systemd-timesyncd* 启动时会读取 `/etc/systemd/timesyncd.conf` 配置文件，内容如下:

```
/etc/systemd/timesyncd.conf
```

```
[Time]
#NTP=
#FallbackNTP=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org
#...
```

要增加或者更改[时间同步服务器](Network%20Time%20Protocol%20daemon.md#连接到_NTP_服务器), 取消上文中对应的注释，然后将服务器的域名或 IP 以空格间隔开列出。另外，你也可以将配置文件放入 `/etc/systemd/timesyncd.conf.d/*.conf`，详细信息请参考 [timesyncd.conf(5)](https://man.archlinux.org/man/timesyncd.conf.5)。

例如，可以使用 [NTP 项目](https://www.pool.ntp.org/)或者 [Arch 默认](https://gitlab.archlinux.org/archlinux/packaging/packages/ntp/-/commit/343310b9ddd996220de27a2a35a5c1277a0bec9b)（NTP 也有这个站点）提供的任何时间服务器：

```
/etc/systemd/timesyncd.conf 或者 /etc/systemd/timesyncd.conf.d/local.conf
```

```
[Time]
NTP=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org
FallbackNTP=0.pool.ntp.org 1.pool.ntp.org 0.fr.pool.ntp.org
```

要验证配置:

```
$ timedatectl show-timesync --all
```

```
LinkNTPServers=
SystemNTPServers=
FallbackNTPServers=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org
ServerName=0.arch.pool.ntp.org
ServerAddress=103.47.76.177
RootDistanceMaxUSec=5s
PollIntervalMinUSec=32s
PollIntervalMaxUSec=34min 8s
PollIntervalUSec=1min 4s
NTPMessage={ Leap=0, Version=4, Mode=4, Stratum=2, Precision=-21, RootDelay=177.398ms, RootDispersion=142.196ms, Reference=C342F10A, OriginateTimestamp=Mon 2018-07-16 13:53:43 +08, ReceiveTimestamp=Mon 2018-07-16 13:53:43 +08, TransmitTimestamp=Mon 2018-07-16 13:53:43 +08, DestinationTimestamp=Mon 2018-07-16 13:53:43 +08, Ignored=no PacketCount=1, Jitter=0 }
Frequency=22520548
```

另外，也可以使用 [systemd-networkd](../网络管理器/Systemd-networkd.md) *.network* 文件的 `NTP=` 选项或 DHCP 服务器（需在 `[DHCPv4]` 或 `[DHCPv6]` 部分启用 `UseNTP` 选项）指定 NTP 服务器。

NTP 服务器的选择会遵循以下规则:

- [systemd-networkd.service(8)](https://man.archlinux.org/man/systemd-networkd.service.8) 中针对每个接口的配置，或者 DHCP 服务优先。
- `/etc/systemd/timesyncd.conf` 中定义的 NTP 服务器会在运行时被添加到针对每个接口的服务器列表中，守护进程会轮流连接这些服务器直到某个有应答。
- 如果上两步没有取到 NTP 服务器， 那么将使用 `FallbackNTP=` 中定义的服务器。

> **注意：**
>
> - 这个服务每次同步或每 60 秒会写入 `/var/lib/systemd/timesync/clock` 本地文件, 这个目录是硬编码的，不可以更换。
> - 这一写入机制在只读的 root 分区或者尝试减少 SD 卡写入量时可能会造成问题。
> - 写入次数可以通过类似 `PollIntervalMinSec=1d` 和 `SaveIntervalSec=infinity` 这样的配置项降低。

## 使用

### 启用并启动

要启用和启动它，执行：

```
# timedatectl set-ntp true
```

其他方法（如在使用 chroot 时）有启动/启用 `systemd-timesyncd.service`。

### 检查服务

同步过程可能会很慢，请耐心等一会。要检查服务状态，使用:

```
$ timedatectl status
```

```
               Local time: Thu 2015-07-09 18:21:33 CEST
           Universal time: Thu 2015-07-09 16:21:33 UTC
                 RTC time: Thu 2015-07-09 16:21:33
                Time zone: Europe/Amsterdam (CEST, +0200)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

### 查看详细信息

要查看更多的服务信息，请使用:

```
$ timedatectl timesync-status
```

```
       Server: 103.47.76.177 (0.arch.pool.ntp.org)
Poll interval: 2min 8s (min: 32s; max 34min 8s)
         Leap: normal
      Version: 4
      Stratum: 2
    Reference: C342F10A
    Precision: 1us (-21)
Root distance: 231.856ms (max: 5s)
       Offset: -19.428ms
        Delay: 36.717ms
       Jitter: 7.343ms
 Packet count: 2
    Frequency: +267.747ppm
```

### 查看非默认配置

要查看非默认配置选项和文件（即被修改的配置项），使用：

```
$ systemd-analyze cat-config systemd/timesyncd.conf --tldr
```

```
# /etc/systemd/timesyncd.conf
[Time]

# /etc/systemd/timesyncd.conf.d/local.conf
[Time]
NTP=0.nl.pool.ntp.org 1.nl.pool.ntp.org 2.nl.pool.ntp.org 3.nl.pool.ntp.org
RootDistanceMaxSec=0.1
PollIntervalMinSec=1d
PollIntervalMaxSec=4w
SaveIntervalSec=infinity
```

### 查看日志

要查看过去 24 小时的事件记录，使用：

```
# journalctl -u systemd-timesyncd --no-hostname --since "1 day ago"
```

```
Jan 19 15:14:20 systemd: Stopping Network Time Synchronization...
Jan 19 15:14:20 systemd: systemd-timesyncd.service: Deactivated successfully.
Jan 19 15:14:20 systemd: Stopped Network Time Synchronization.
Jan 19 15:14:20 systemd: Starting Network Time Synchronization...
Jan 19 15:14:20 systemd: Started Network Time Synchronization.
Jan 19 15:14:20 systemd-timesyncd: Contacted time server 178.215.228.24:123 (0.nl.pool.ntp.org).
Jan 19 15:14:20 systemd-timesyncd: Initial clock synchronization to Fri 2024-01-19 15:14:20.393865 CET.
```

## 参阅

- [论坛：systemd-timesyncd 没有保持时间同步](https://bbs.archlinux.org/viewtopic.php?id=182600)
- [论坛：使用 systemd-timesync 替代 NTP](https://bbs.archlinux.org/viewtopic.php?id=182172)
- [timesyncd 源码](https://github.com/systemd/systemd/blob/main/src/timesync/timesyncd.c)
- [systemd-timesyncd(8)](https://man.archlinux.org/man/systemd-timesyncd.8)
- [timesyncd.conf(5)](https://man.archlinux.org/man/timesyncd.conf.5)

## 23.2　Chrony

*难度：进阶 ｜ 篇幅：11.7 KB ｜ 所属：NTP*

将要讲到：安装、配置、用法、网络状态通知、参考

[chrony](https://chrony-project.org/) 是一个带有客户端和服务端功能的[网络时间协议（NTP）](../../04_硬件与外设/主板和%20BIOS/系统时间.md#时钟同步)实现，专为不常在线的系统环境设计，还可以跟踪硬件时钟（RTC）漂移。更多信息请参考项目的[功能清单](https://chrony-project.org/comparison.html)页面。

## 安装

安装 [chrony](https://archlinux.org/packages/?name=chrony)包。

## 配置

软件包自带了一个被注释掉的 `/etc/chrony.conf` 配置文件。最简可用配置（使用了 IP 而不是域名）如下：

```
/etc/chrony.conf
```

```
server 1.2.3.4 offline
server 5.6.7.8 offline
server 9.10.11.12 offline
driftfile /var/lib/chrony/drift
rtconutc

rtcsync
```

`rtcsync` 选项会配置 RTC，但不跟踪 RTC 偏移。可选替代为 `rtcfile`，具体信息请参考下方的[#实时时钟](#实时时钟)一节。

另请参考 [项目 FAQ](https://chrony-project.org/documentation.html) 以及 [chronyc(1)](https://man.archlinux.org/man/chronyc.1)，[chrony.conf(5)](https://man.archlinux.org/man/chrony.conf.5) 和 [chronyd(8)](https://man.archlinux.org/man/chronyd.8) 手册。

### NTP 服务器

在 `/etc/chrony.conf` 中定义的第一项就是设备要连接到的服务器地址。 NTP 服务器处于一分层结构中，其中的每一层被称作 *strata*：被认定为独立时钟源的设备作为 *stratum 0* 时间源；直接连接到 *stratum 0* 的设备为 *stratum 1* 时间源；连接到 *stratum 1* 的为 *stratum 2* 时间源，以此类推。

但请注意，服务器的层级并不能作为其准确度或可靠性的依据。通常来说，stratum 2 服务器被用于时间同步；如果你还不知道要连到哪个服务器，那建议使用 [pool.ntp.org](https://www.ntppool.org/en/) 的服务器（[备用链接](https://support.ntp.org/bin/view/Servers/NTPPoolServers)），然后根据你的物理位置选择服务器池。

下一行告知 chrony 从 NTP 池中选择 4 个源（chrony 使用其特定方式处理服务器池，以防止混淆其服务端测偏移跟踪），然后在启动时使用 burst 特性：

```
pool 2.arch.pool.ntp.org iburst maxsources 4
```

#### 离线设备

如果你的设备在启动时不会连到网络，那建议使用其 *offline* 选项。它会让 Chrony 在没有被告知的情况下不要尝试连接到服务器。

```
pool 2.arch.pool.ntp.org iburst maxsources 4 offline
```

#### 使用 NTS 服务器

从 4.0 版本开始 ，chrony 支持使用 [Network Time Security (NTS) 网络时间安全](https://www.networktimesecurity.org/)机制，这是 NTP 的安全加密变种。如需使用该功能，请添加一个启用 NTS 的服务器，然后在结尾添加 `nts`，例如：

```
server time.cloudflare.com iburst nts
```

可以在[这里](https://github.com/jauderho/nts-servers)找到一些支持 NTS 的服务器。

另外，可以通过 `/etc/chrony.conf` 中的 `authselectmode` 选项配置 NTS 策略。

### 实时时钟

在系统启动时，会从硬件实时时钟（RTC）读取初始时间，并用其设定系统时间，然后在 chrony 守护进程启动后的几分钟内进行一次同步。如果硬件时间脱离同步，那初始系统时间将可能会和真实时间相差数分钟。Chrony.conf 有三种不同机制来处理 RTC：

- 第一种是 `rtcsync`，它会简单地定期将当前时间写入到 RTC。这也是 ntpd 选用的方式，但会关闭 RTC 偏移跟踪：这对于间断运行的桌面环境很不利，该环境下会依赖 RTC 进行大量计时工作。
- 第二种是 `rtcautotrim`，它会在 RTC 时间偏移超过一定量后进行复写。该方式可与 `rtcfile` 搭配，它会启用 RTC 误差跟踪。
- 最后一种是不操控 RTC，但会将其误差和偏移记录到 `rtcfile`。RTC 时间会一直有误差，但系统时间会保持正常，因为 chrony 记录了其具体的偏移量。chronyc 下的 `rtctrim` *命令*还是可以按需同步 RTC：

```
# chronyc
chronyc> trimrtc
200 OK
chronyc> quit
```

> **注意：**`rtcsync` 和 `rtcfile` 不能同时使用。另外 `rtcfile` 会阻止如 `hwclock` 和 `timedatectl` 之类的工具访问 RTC，详细信息请参考 [chrony.conf(5) § System clock](https://man.archlinux.org/man/chrony.conf.5#System_clock)。

另外，`rtconutc` 描述了 RTC 是否以 UTC 时间运行。

#### 案例：间断运行的桌面环境

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 该示例需要与[系统时间#自动同步](../../04_硬件与外设/主板和%20BIOS/系统时间.md#自动同步)一致或进行修改。（在 Talk:Chrony#For intermittently running desktops 2 中讨论）

间断运行的桌面环境将需要使用 `rtcfile` 来跟踪 RTC 误差。装有 Arch Linux 连续运行 5 年的设备上 RTC 可累计有 300 秒的误差，如果使用以上的配置，在重启后 chrony 会需要很长一段时间来调整时间偏移。如果改用如下配置：

> **注意：**默认不存在 `/etc/sysconfig` 文件夹，需要手动创建。

```
/etc/sysconfig/chronyd
```

```
OPTIONS='-r -s'
```

```
/etc/chrony.conf
```

```
dumpdir /var/lib/chrony
rtcfile /var/lib/chrony/rtc
```

有意思的是，RTC 还是会有误差，但在每次重启之后，chrony 都会调整 RTC 的累计误差，就算系统刚刚启动，其时间还能和 NTP 接近同步。

RTC 仍留存误差的原因是我们忘了添加 `rtcautotrim` 一行到配置中，它会让 chrony 调整 RTC 时间。如果加上了这一行，那 RTC 和系统时间都将变正确。

### 其它有意思的选项

有用的选项：

- `makestep`：允许 chrony 单次大量调整时间，而不是频繁多次调整。可能会对运行中程序造成影响，但有助于修正大量偏移。`makestep 0.1 3` 可能会更适用于经常离线的系统：只有开头三次调整会是阶跃性的，因此影响仅限于系统启动时段。

精确性：

- `server` 和 `pool`：`xleave` 和 `presend` 可能会在没有兼容性成本的情况下提升精确度。
- `hwtimestamp`：有些网卡会给它的包添加时间戳以测量网络栈的延迟。使用 `hwtimestamp *` 可用启用该功能，对于不支持的网卡将不起任何效果。
- `tempcomp`：跟踪软件时钟偏移（通常由主板晶振温度变化导致）和温度传感器间的关系，适用于要求极致精确度的环境。

## 用法

### 启动 chronyd

启用/启动 `chronyd.service` 和 `chrony-wait.service`。第一个服务会启动守护进程，第二个会让 [systemd](../../01_安装与包管理/初始化/Systemd.md) 向依赖时间同步状态的其它服务发送信号。

> **注意：**
>
> - `systemd-timesyncd.service` 会与 `chronyd` 产生冲突，所以如果你要启用 `chronyd`，需要先将其禁用。

### 告知 chronyd 网络连接已可用

如果你连接到了网络，执行：

```
# chronyc
chronyc> online
200 OK
chronyc> exit
```

你也可以使用 `activity` 选项来查看状态：

```
# chronyc activity
200 OK
3 sources online
0 sources offline
0 sources doing burst (return to online)
0 sources doing burst (return to offline)
0 sources with unknown address
```

Chrony 应该已经连接到配置的时间服务器了，并根据需要更新了你的时钟。使用如下命令告知 chrony 你已经断开网络：

```
# chronyc offline
200 OK

# chronyc activity
200 OK
0 sources online
3 sources offline
0 sources doing burst (return to online)
0 sources doing burst (return to offline)
0 sources with unknown address
```

在线/离线状态可由 [networkmanager](https://archlinux.org/packages/?name=networkmanager)包 和 [connman](https://archlinux.org/packages/?name=connman)包 的调度服务自动处理，详情请参考下文。

### 检查已配置的 NTP 服务器

使用以下命令查看 chrony 正在使用的 NTP 服务器和其精度：

```
$ chronyc -N 'sources -a -v'

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current best, '+' = combined, '-' = not combined,
| /             'x' = may be in error, '~' = too variable, '?' = unusable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \<br>MS Name/IP address         Stratum Poll Reach LastRx Last sample<br>===============================================================================<br>^+ ptbnts1.ptb.de                1   6   377    50    -38us[  -13us] +/- 8723us<br>^* ptbnts2.ptb.de                1   6   377    49  +2061ns[  +27us] +/- 7538us<br>^+ nts.ntp.se                    2   6   377    51   +594us[ +619us] +/-   15ms<br>^+ nts.sth1.ntp.se               2   6   377    51   +655us[ +680us] +/-   15ms<br>^+ nts.sth2.ntp.se               2   6   377    53   +991us[+1016us] +/-   15ms<br>^+ time.cloudflare.com           3   6   377    49  -1250us[-1250us] +/-   10ms<br>```

## 网络状态通知

如果你在 `chrony.conf` 中为你的池配置了 offline 选项，需要将网络状态变更告知 *chrony*。

你可以使用 *chronyc* 来通知 *chrony*，也可以使用你的网络配置管理器对应的调度服务。

### NetworkManager

*chronyd* 可以通过 [NetworkManager 调度脚本](../../06_软件与应用/红帽/NetworkManager.md#Network_services_with_NetworkManager_dispatcher)随网络连接状态进入在线/离线状态。创建一个 dispatcher 来使用上游分发的 NetworkManager 调度脚本：

```
 cp /usr/share/doc/chrony/examples/chrony.nm-dispatcher.onoffline /etc/NetworkManager/dispatcher.d/20-chrony-onoffline.sh
 chmod 700 /etc/NetworkManager/dispatcher.d/20-chrony-onoffline.sh
```

你也可以选择安装 [networkmanager-dispatcher-chrony](https://aur.archlinux.org/packages/networkmanager-dispatcher-chrony/)AUR。

### netctl

从 AUR 安装 [netctl-dispatcher-chrony](https://aur.archlinux.org/packages/netctl-dispatcher-chrony/)AUR。这会为 netctl 添加一个钩子，随任意网络连接自动运行。

### dhcpcd

创建如下钩子：

```
/etc/dhcpcd.exit-hook
```

```
if $if_up; then
	chronyc online
elif $if_down; then
	chronyc offline
fi
```

详情可参考 [dhcpcd-run-hooks(8)](https://man.archlinux.org/man/dhcpcd-run-hooks.8)。

## 参考

- [System time](../../04_硬件与外设/主板和%20BIOS/系统时间.md) (for more information on computer timekeeping)
- [https://chrony-project.org/](https://chrony-project.org/)
- [https://www.ntp.org/](https://www.ntp.org/)
- [https://support.ntp.org/bin/view/Main/WebHome](https://support.ntp.org/bin/view/Main/WebHome)
- [https://www.ntppool.org/en/](https://www.ntppool.org/en/)
- [https://www.eecis.udel.edu/~mills/ntp/html/index.html](https://www.eecis.udel.edu/~mills/ntp/html/index.html)
- [https://www.akadia.com/services/ntp_synchronize.html](https://www.akadia.com/services/ntp_synchronize.html)

## 23.3　Network Time Protocol daemon

*难度：进阶 ｜ 篇幅：14.1 KB ｜ 所属：NTP*

将要讲到：安装、配置、使用、技巧、排错、参见

[Network Time Protocol](https://en.wikipedia.org/wiki/Network_Time_Protocol) （网络时间协议）是 GNU/Linux 系统通过互联网时间服务器同步系统[软件时钟](../../04_硬件与外设/主板和%20BIOS/系统时间.md#系统时钟)的最常见方法。设计时考虑到了各种网络延迟，通过公共网络同步时，误差可以降低到10毫秒以内；通过本地网络同步时，误差可以降低到 1 毫秒。

[NTP 项目](https://support.ntp.org/bin/view/Main/WebHome#The_NTP_Project)提供了一个名为简单 NTP 的参考实现。本文介绍如何设置和运行服务器和客户端 NTP 进程。

## 安装

安装 [ntp](https://archlinux.org/packages/?name=ntp)包 软件包。如果不做任何配置， *ntpd* 默认工作于客户端模式。如果使用 Arch Linux 默认的配置，请跳转到 [#使用](#使用)。作为服务器的配置，请参阅 [#NTP 服务器模式](#NTP_服务器模式)。

## 配置

主要的后台进程是 *ntpd*, 可以通过 `/etc/ntp.conf` 配置。详细信息可以参考手册 [ntp.conf(5)](https://man.archlinux.org/man/ntp.conf.5) 和相关的 `man {ntpd|ntp_auth|ntp_mon|ntp_acc|ntp_clock|ntp_misc}`.

### 连接到 NTP 服务器

NTP 服务器通过一个层级系统进行分类，不同的层级称为 *strata*；独立的时间源为*stratum 0*；直接连接到 *stratum 0* 的设备为 *stratum 1*；直接连接到 *stratum 1* 的源为 *stratum 2*，以此类推。

服务器的 stratum 并不能完全等同于它的精度和可靠度。通常的时间同步都使用 stratum 2 服务器。通过[pool.ntp.org](https://www.pool.ntp.org/) 服务器或[这个链接](https://support.ntp.org/bin/view/Servers/NTPPoolServers)可以选择比较近的服务器池。

下面几行仅仅是例子：

```
/etc/ntp.conf
```

```
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
```

推荐使用`iburst`选项，如果第一次尝试无法建立连接，程序会发送一系列的包。`burst` 选项则总是发送一系列的包，即使第一次也是这样。如果没有明确的允许的话不要使用 *burst* 选项，有可能被封禁。

### NTP 服务器模式

如果建立一个 NTP 服务器，你需要添加 [*local clock*](https://www.ntp.org/ntpfaq/NTP-s-refclk.htm#Q-LOCAL-CLOCK) 作为一个服务器，这样，即便它失去网络连接，它也可以继续为网络提供服务;添加 *local clock* 作为一个 stratum 10 服务器 (使用 *fudge* 命令)这样它就只会在失去连接时使用本地时钟：

```
server 127.127.1.0
fudge  127.127.1.0 stratum 10
```

下一步，定义规则允许客户端连接你的服务(*localhost* 也被认为是一个客户端)。使用 *restrict* 命令；你应该在文件中已经有一行：

```
restrict default nomodify nopeer noquery
```

这限制了每个人做任何修改并阻止每个人请求你的时间服务器状态：`nomodify` 防止重新配置你的ntpd（使用*ntpq* 或 *ntpdc* ），`noquery` 防止从你的nptd（或是*ntpq* 和 *ntpdc*）获取状态数据。

你也能添加其它选项：

```
restrict default kod nomodify notrap nopeer noquery
```

> **注意：**这会允许其他人查询你的时间服务器。你需要添加 `noserve` 来停止提供时间。

"restrict"选项的完整文档可以从 [ntp.conf(5)](https://man.archlinux.org/man/ntp.conf.5) 中查找到。详见 [https://support.ntp.org/bin/view/Support/AccessRestrictions](https://support.ntp.org/bin/view/Support/AccessRestrictions) 。

你需要在这一行之后告诉 *ntpd* 什么可以访问你的服务器；如果你不是在配置一台 NTP 服务器的话，下面一行就足够了。

```
restrict 127.0.0.1
```

如果你想要强制DNS解析到IPv6域名，在IP地址或域名前写上 `-6`（`-4` 则强制使用IPv4域名），例如:

```
restrict -6 default kod nomodify notrap nopeer noquery
restrict -6 ::1    # ::1 is the IPv6 equivalent for 127.0.0.1
```

最后，指定drift文件（它能时刻监控你的时钟的时间漂移）和log文件的位置：

```
driftfile /var/lib/ntp/ntp.drift
logfile /var/log/ntp.log
```

一份基础的配置文件是这样的：

```
/etc/ntp.conf
```

```
server 0.pool.ntp.org iburst
server 1.pool.ntp.org iburst
server 2.pool.ntp.org iburst
server 3.pool.ntp.org iburst

restrict default kod nomodify notrap nopeer noquery
restrict -6 default kod nomodify notrap nopeer noquery

restrict 127.0.0.1
restrict -6 ::1

driftfile /var/lib/ntp/ntp.drift
logfile /var/log/ntp.log
```

> **注意：**定义日志文件不是必须的，但是它对于反馈 *ntpd* 操作是有好处的。

## 使用

软件包默认包含客户端模式的配置，并且使用单独的用户和群组，启动时就会移除 root 权限。如果在终端中启动，请使用 `-u` 选项:

```
# ntpd -u ntp:ntp
```

systemd 服务默认使用 `-u` 选项和 `-g` 选项禁用一个阈值(*panic-gate*). 这样即使 ntp-server 的时间和系统时间的差异超过阈值，依然会同步时间。

> **警告：**使用 panic-gate 的原因是某些后台任务或服务会引起时间跳跃. 如果系统的时间从来没有同步过，请考虑先禁用其他服务再进行同步。

两个服务都依赖系统网络状况，会在检测到网络连接时开始同步。

### 启动时启用 ntpd

启用 `ntpd.service` 服务.

> **注意：**systemd 命令 *timedatectl* 仅可以控制 [systemd-timesyncd](Systemd-timesyncd.md), 用 root 执行 `timedatectl set-ntp 1` 会停止运行中的 `ntpd.service`.

用 *ntpq* 可以查看同步的状态：

```
$ ntpq -p
```

delay, offset 和 jitter 不应该为零，*ntpd* 同步的服务器前有星号，*ntpd* 可能等待很多分钟后才会进行同步，请等 17 分钟 (1024 秒).

### 每次启动同步一次

另一种方式是启用 `ntpdate.service` 服务，每次启动都同步一次(`-q`) 并且是 non-forking (`-n`), 进程不会在后台运行。如果是服务器或者很多天才会重启一次，不建议使用此方式。

如果需要把同步到的时间写入硬件时钟，请按照[这里](../../01_安装与包管理/初始化/Systemd.md#Editing_provided_units)的说明修改服务并启动:

```
/etc/systemd/system/ntpdate.service.d/hwclock.conf
```

```
[Service]
ExecStart=/usr/bin/hwclock -w
```

## 技巧

### 有网络连接的时候启动ntpd

*ntpd* 可以由你的网络管理器启动, 所以ntp这个守护进程只有在计算机有网络连接的时候才会启动.

**Netctl**

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** 英文页面风格不好。添加 `-u` 参数以及[“使用”](#使用)中提及的可选参数，或尽可能使用 systemctl 。（在 [Talk:Network Time Protocol daemon#](https://wiki.archlinuxcn.org/wiki/Talk:Network_Time_Protocol_daemon) 中讨论）

给你的 [netctl](../网络管理器/Netctl.md) 配置文件添加如下这几行:

```
ExecUpPost='/usr/bin/ntpd || true'
ExecDownPre='killall ntpd || true'
```

**NetworkManager**

通过网络管理器的 [dispatcher](../../06_软件与应用/红帽/NetworkManager.md#Network_services_with_NetworkManager_dispatcher) 脚本，可以同网络连接一起启动/终止*ntpd* 守护进程。 安装[networkmanager-dispatcher-ntpd](https://aur.archlinux.org/packages/networkmanager-dispatcher-ntpd/)AUR 预配置包 [#启动时启用 ntpd](#启动时启用_ntpd) 来让网络连接同步ntp启动/终止.

**KDE**

在KDE中，通过右击时间图标选择 *Adjust date/time*，可以使用 NTP (别忘了安装NTP)。 注意, 在配置KDE中配置NTP之前，先要把 ntp 守护进程设置为  disabled 状态. 

### 在GPS中使用NTP

大多数联网的交通工具要通过(共享内存) 方式让 *ntpd* 从GPS中接收时间。 但是, 从 *ntpd* 4.2.8版本开始，一个*更好*的办法出现了----直接与 *gpsd*守护进程交互。这个要先安装 [gpsd](https://archlinux.org/packages/?name=gpsd)包 .

在 `/etc/ntp.conf` 配置文件中添加下面这几行:

```
/etc/ntp.conf
```

```
#=========================================================
#  GPSD native ntpd driver
#=========================================================
# This driver exists from at least ntp version 4.2.8
# Details at
#   https://www.eecis.udel.edu/~mills/ntp/html/drivers/driver46.html
server 127.127.46.0
fudge 127.127.46.0 time1 0.0 time2 0.0 refid GPS
```

只要 *gpsd* 一直起着，ntp就会一直正常运行。 ntp会通过一个本地套接字同 *gpsd* 连接, 搜索 *gpsd* 返回的 "gpsd_json" 对象.

要检验安装的话, 首先要检查 *gpsd* 是不是正常运行:

```
 $ cgps -s
```

然后等个几分钟运行 `ntpq -p`. 这个会显示 *ntpd* 是否已经跟 *gpsd* 建立连接了:

```
$ ntpq -p
```

```
remote           refid            st t when poll reach   delay   offset  jitter
 ==================================================================================
*GPSD_JSON(0)    .GPS.            0 l   55   64  377    0.000    2.556  14.109
```

> **提示：**如果 *reach* 这列是0的话, 这就说明 *ntpd* 还没有同 *gpsd* 建立连接。 等几分钟再试一次。 有时 *ntpd* 要花点时间来跟*gpsd* 建立连接。

### 在 chroot 底下运行

> **注意：**在试图把 *ntpd* 放到chroot目录下面之前，*ntpd* 应该在正常路径下启动 (默认在 Arch Linux 安装路径下)。 因为在相对以root身份运行的进程，chroots的用户相对没有啥权限的。

创建一个新目录（如果还没创建的话） `/etc/systemd/system/ntpd.service.d/`，并添加在里面添加文件 `customexec.conf` ,内容如下:

```
[Service]
ExecStart=
ExecStart=/usr/bin/ntpd -g -i /var/lib/ntp -u ntp:ntp -p /run/ntpd.pid
```

然后, 编辑 `/etc/ntp.conf` 来改变 driftfile 路径来让跟它跟 chroot 路径保持一致, 而不是还用原先的普通路径。修改

```
driftfile       /var/lib/ntp/ntp.drift
```

为

```
driftfile       /ntp.drift
```

通过root创建永久目录和文件，作为一个合适的 chroot 环境来支持 getaddrinfo() :

```
# mkdir /var/lib/ntp/etc /var/lib/ntp/lib /var/lib/ntp/proc
# touch /var/lib/ntp/etc/resolv.conf /var/lib/ntp/etc/services
```

然后通过修改 *fstab* 绑定前面提到的文件:

```
/etc/fstab
```

```
...
#ntpd chroot mounts
/etc/resolv.conf  /var/lib/ntp/etc/resolv.conf none bind 0 0
/etc/services	  /var/lib/ntp/etc/services none bind 0 0
/lib		  /var/lib/ntp/lib none bind 0 0
/proc		  /var/lib/ntp/proc none bind 0 0
```

```
# mount -a
```

最后, 再一次重启 `ntpd` 监控进程。 一旦重启完成你就可以通过检查 `/proc/{PID}/root` 的软连接来判断监控进程是不是在 chrooted 下面工作:

```
# ps -C ntpd | awk '{print $1}' | sed 1d | while read -r PID; do ls -l /proc/$PID/root; done
```

正常应该链接到 `/var/lib/ntp` 而不是 `/`.

不用等一段时间就要确定 driftfile 配置是否工作正常是比较困难的, 因为 *ntpd* 不会经常读写。 如果配置错误的话，log里面会打印一个error; 如果配置正确的话, 时间戳会更新的。 可以通过等一天来判断，如果没有log中没有错误打印，并且log的时间戳还更新了，那么说明配置成功了。

## 排错

### 无法分配请求地址

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** 英文章不正。对需要或必须使用 IPv6 的用户而言，此方案不可行。（在 [Talk:Network Time Protocol daemon#](https://wiki.archlinuxcn.org/wiki/Talk:Network_Time_Protocol_daemon) 中讨论）

如果收到下列*无法分配请求地址*的报错信息：

```
$ journalctl -u ntpd
```

```
ntpd: bind(21) AF_INET6 fe80::6ef0:49ff:fe51:4946%2#123 flags 0x11 failed: Cannot assign requested address
ntpd: unable to create socket on eth0 (5) for fe80::6ef0:49ff:fe51:4946%2#123
ntpd: failed to init interface for address fe80::6ef0:49ff:fe51:4946%2
```

可以禁用 IPv6 解决。做法是：[编辑](../../01_安装与包管理/初始化/Systemd.md#修改现存单元文件) `ntpd.service` 添加 `-4` 参数：

```
[Service]
ExecStart=
ExecStart=/usr/bin/ntpd -g -u ntp:ntp -4
```

## 参见

- [https://www.ntp.org/](https://www.ntp.org/)
- [https://support.ntp.org/](https://support.ntp.org/)
- [https://www.pool.ntp.org/](https://www.pool.ntp.org/)
- [http://www.eecis.udel.edu/~mills/ntp/html/index.html](http://www.eecis.udel.edu/~mills/ntp/html/index.html)
- [https://www.akadia.com/services/ntp_synchronize.html](https://www.akadia.com/services/ntp_synchronize.html)
