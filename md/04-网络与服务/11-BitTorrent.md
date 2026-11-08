# 第 11 章　BitTorrent

> 本章解决在 Arch Linux 上挑一个 BitTorrent 客户端并长期运行的问题：装哪个、怎么配、怎么挂到终端或 Web 界面。Transmission 与 Aria2 篇幅最厚，覆盖守护程序、命令行与 Web 界面，必读；rTorrent 靠 Screen 挂机，要长期保种再细看；qBittorrent 兼顾图形界面、搜索引擎与主题，适合桌面；Ktorrent 最短，当速查表翻即可跳过。
> 
> 最容易踩的坑是把「装上」当成「能用」：图形界面配置与守护程序配置各算一套，Aria2 要配前端才顺手，rTorrent 得借 Screen 才能退出后继续跑，接成 makepkg 的下载工具也要另行设置。

<!-- 本章 5 节，共 51 KB -->

## 11.1　Ktorrent

*难度：入门 ｜ 篇幅：1.5 KB ｜ 所属：BitTorrent*

将要讲到：安装、问题解决、在命令行中用脚本管理

[KTorrent](https://www.kde.org/applications/internet/ktorrent/) 是 [KDE](../../05_桌面与图形/KDE/KDE.md) 的 BitTorrent 客户端。

## 安装

安装 [ktorrent](https://archlinux.org/packages/?name=ktorrent)包 软件包。

## 问题解决

### 无法看到底部面板的某些工具

要显示 files、seeders、leechers 和 current trackers 的任何信息，安装可选依赖 [geoip](https://archlinux.org/packages/?name=geoip)包。建议在设置中启用 DHT，以避免速度过慢和 seeders 数量过少。

## 在命令行中用脚本管理

虽然 KTorrent 仅是一个 GUI 应用程序，但是幸好它有一个 DBUS 接口，因此您可以使用脚本在命令行（即从 SSH）中对其进行管理。详情请参见 [linuxquestions 论坛回答](https://www.linuxquestions.org/questions/linux-software-2/terminal-commands-for-ktorrent-4175441715/#post4851070)。

## 11.2　QBittorrent

*难度：入门 ｜ 篇幅：5.5 KB ｜ 所属：BitTorrent*

将要讲到：安装、配置、搜索引擎、Web 界面、主题、故障排查

[qBittorrent](https://www.qbittorrent.org/)是一个用[C++](../../07_开发与工具/编程语言/C.md)、[Qt](../../07_开发与工具/控件工具集/Qt.md)和[Python](../../07_开发与工具/编程语言/Python.md)（可选搜索引擎）编写的开源跨平台BitTorrent 客户端，使用[libtorrent-rasterbar](https://archlinux.org/packages/?name=libtorrent-rasterbar)包库。

它速度快，稳定性高，体积轻，支持unicode并提供集成搜索引擎。它支持UPnP端口转发 / NAT-PMP，加密（与Vuze兼容），FAST扩展（主线）和PeX支持（与uTorrent兼容）。

## 安装

在官方仓库中有两个软件包可以用于安装qBittorrent：

- **qBittorrent** — 具有[Qt](../../07_开发与工具/控件工具集/Qt.md)图形界面和[#Web 界面](#Web_界面)。

: [https://github.com/qbittorrent/qBittorrent](https://github.com/qbittorrent/qBittorrent) || [qbittorrent](https://archlinux.org/packages/?name=qbittorrent)包

- **qBittorrent-nox** — 无图形界面的版本(nox -> no X server)。

: [https://github.com/qbittorrent/qBittorrent](https://github.com/qbittorrent/qBittorrent) || [qbittorrent-nox](https://archlinux.org/packages/?name=qbittorrent-nox)包

## 配置

在第一次运行程序时，配置文件会保存在`~/.config/qBittorrent/qBittorrent.conf`中。

### 自启动

如果你安装了[qbittorrent-nox](https://archlinux.org/packages/?name=qbittorrent-nox)包，你将会得到一个systemd单元模板：`qbittorrent-nox@.service`。

如果你[启用、启动](https://wiki.archlinuxcn.org/wzh/Help:%E9%98%85%E8%AF%BB#控制_systemd_单元)`qbittorrent-nox@username.service`，QBittorrent 将以用户 `*username*`运行。参见)。

默认的下载文件夹会是该用户的`Downloads`目录。

> **提示：**
>
> - 如果你把它作为一个可以访问的服务运行，可以[添加一个叫做qbittorrent的用户](../../08_安全与加密/安全/用户和用户组.md#添加登录用户)并让它在那个用户下运行，使得该服务可以在程序退出后重新启动。
> - 要修改设置（例如端口），你可以为它的systemd单元添加一个环境变量（例如端口的变量就是QBT_WEBUI_PORT）。执行`qbittorrent-nox --help`可以查看更多其他的环境变量（此信息在手册中并未给出）。

## 搜索引擎

你可以通过菜单*视图> 搜索引擎*启用可选搜索引擎，这将启用*搜索*标签页。

### 搜索插件

默认的搜索插件可通过下面的操作添加/启用：转到*搜索*标签页，中打开追踪器窗口后通过*搜索插件...*（右下角按钮），并点击*查找更新*。

更多非官方的搜索插件可以在[这里](https://github.com/qbittorrent/search-plugins/wiki/Unofficial-search-plugins)找到。

## Web 界面

### 默认端口

qBittorrent默认会在所有[接口](../网络/网络配置.md#网络接口)上的8080端口监听传入连接。因此，你可以通过`[http://HOST_IP:8080](http://HOST_IP:8080)`访问。

> **注意：**HTTPS默认不启用，所以，`[https://HOST_IP:8080](https://HOST_IP:8080)`将无法访问。

### 默认用户名 & 密码

默认用户名为`admin`，在5.0.0版本之前的默认密码为`adminadmin`。版本5.0.0及以上会生成一个只在控制台可见的随机密码，直到手动保存以防止未经授权的访问。有关此更改的更多信息，请查看[这里](https://github.com/qbittorrent/qBittorrent/pull/19777)。

### 允许无用户名&密码访问

在家庭环境下，通常希望能够无需输入用户名和密码就能访问Web UI。这可以在使用默认用户名和密码登录后在Web UI本身中配置。

或者，为了避免首次登录，可以将以下部分添加到`~/.config/qBittorrent/qBittorrent.conf`中：

```
[Preferences]
WebUI\AuthSubnetWhitelist=192.168.1.0/24
WebUI\AuthSubnetWhitelistEnabled=true
WebUI\UseUPnP=false
```

上述配置项将：

- 允许从192.168.1.x登录的客户端无需输入用户名和密码即可以访问Web UI。
- 为Web UI禁用UPnP，这样Web UI将无法从网络外部访问。

然后，[重载](https://wiki.archlinuxcn.org/wzh/Help:%E9%98%85%E8%AF%BB#控制_systemd_单元) `qbittorrent-nox@*用户名*.service`。

### 反向代理配置

#### nginx

请参考[qbittorrent wiki](https://github.com/qbittorrent/qBittorrent/wiki/NGINX-Reverse-Proxy-for-Web-UI)

## 主题

### 非官方主题

- [如何使用自定义UI主题](https://github.com/qbittorrent/qBittorrent/wiki/How-to-use-custom-UI-themes)
- [已知的qBittorrent主题列表](https://github.com/qbittorrent/qBittorrent/wiki/List-of-known-qBittorrent-themes)

## 故障排查

## 另见

- [qBittorrent官方网站](https://www.qbittorrent.org/)
- [qBittorrent在GitHub的wiki](https://github.com/qbittorrent/qBittorrent/wiki)
- [Wikipedia:qBittorrent](https://en.wikipedia.org/wiki/qBittorrent)

## 11.3　Aria2

*难度：进阶 ｜ 篇幅：13.4 KB ｜ 所属：BitTorrent*

将要讲到：安装、配置、前端、提示与技巧、另请参见

来自 aria2 [主页](https://aria2.github.io/):

: aria2 是一个轻量级多协议多源命令行下载工具，支持 HTTP/HTTPS， FTP，BitTorrent 和 [Metalink](https://en.wikipedia.org/wiki/Metalink)。用户可以通过内置的 [JSON-RPC](https://en.wikipedia.org/wiki/JSON-RPC) 和 [XML-RPC](https://en.wikipedia.org/wiki/XML-RPC) 接口操作 aria2。

## 安装

安装 [aria2](https://archlinux.org/packages/?name=aria2)包 软件包。

要以[守护进程](../../01_安装与包管理/初始化/Systemd.md)运行 aria2，你可以写一个 [systemd 用户单元](#作为守护进程使用_aria2)。

## 配置

### aria2.conf

aria2 默认从 `$XDG_CONFIG_HOME/aria2/aria2.conf` 中寻找全局配置，该行为可以通过 `--conf-path` 选项改变：

- 下面的示例用配置文件 `/file/aria2.rapidshare` 中的选项下载 `aria2.example.rar`：

```
$ aria2c --conf-path=/file/aria2.rapidshare http://rapidshare.com/files/12345678/aria2.example.rar
```

如果存在 `$XDG_CONFIG_HOME/aria2/aria2.conf`，但希望使用 `/file/aria2.rapidshare` 中的选项，需要在命令中加入 `--no-conf` 选项：

- 若不使用默认的配置文件，只使用 `/file/aria2.rapidshare` 中的选项下载 `aria2.example.rar`：

```
$ aria2c --no-conf --conf-path=/file/aria2.rapidshare http://rapidshare.com/files/12345678/aria2.example.rar
```

如果不存在 `$XDG_CONFIG_HOME/aria2/aria2.conf`，并且你想要简化配置管理：

```
$ touch $XDG_CONFIG_HOME/aria2/aria2.conf
```

### 示例 aria2.conf

```
continue
dir=${HOME}/Desktop
file-allocation=none
input-file=${HOME}/.aria2/input.conf
log-level=warn
max-connection-per-server=4
min-split-size=5M
on-download-complete=exit
```

这与执行下面的命令等价：

```
$ aria2c --dir=${HOME}/Desktop --file-allocation=none --input-file=${HOME}/.aria2/input.conf --on-download-complete=exit --log-level=warn FILE
```

> **注意：**上面示例 aria.conf 可能错误地使用了 $HOME 变量，一些用户报告说，使用花括号语法会在 aria2 的工作目录下创建名叫 ${HOME} 的文件夹。因为 bash 会把 $HOME 当成环境变量，这样的文件夹很难访问。目前建议在 aria2.conf 中使用绝对路径。

#### 选项细节

- `continue`: 如果存在下载任务对应的控制文件，自动继续未完成的下载任务。
- `dir=${HOME}/Desktop`: 在 `~/Desktop` 中存储下载文件。
- `file-allocation=none`: 不要在下载开始前预分配磁盘空间（默认值：prealloc1
- `input-file=${HOME}/.aria2/input.conf`: 从 `~/.aria2/input.conf` 中的 URI 下载文件，URI 以 TAB 分割。
- `log-level=warn`: 设置日志级别，只输出警告和错误。（默认值：debug）
- `max-connection-per-server=4`: 设置每个服务器的最大链接数为 4。（默认值：1）
- `min-split-size=5M`: 只在文件大小超过 2\*5MB = 10MB 时分割文件。（默认值： 20M）
- `on-download-complete=exit`: 当下载任务完成时，运行 `exit` 命令并退出 shell。

##### 示例输入文件 #1

从两个分别的地址下载 `aria2-1.10.0.tar.bz2` 到 `~/Desktop`，并合并为 `aria2-1.10.0.tar.bz2`

```
http://aria2.net/files/stable/aria2-1.10.0/aria2-1.10.0.tar.bz2    http://sourceforge.net/projects/aria2/files/stable/aria2-1.10.0/aria2-1.10.0.tar.bz2
```

##### 示例输入文件 #2

下载 `aria2-1.9.5.tar.bz2`，命名为 `aria2.old.tar.bz2` ，保存到 `/file/old`

```
http://aria2.net/files/stable/aria2-1.9.5/aria2-1.9.5.tar.bz2
  dir=/file/old
  out=aria2.old.tar.bz2

```

下载 `aria2-1.10.0.tar.bz2`，命名为 `aria2.new.tar.bz2`，保存到 `~/Desktop`

```
http://aria2.net/files/stable/aria2-1.10.0/aria2-1.10.0.tar.bz2
  out=aria2.new.tar.bz2
```

#### 备注

1 `--file-allocation=falloc`: 推荐在新的文件系统，比如 [ext4](../../02_系统管理与内核/文件系统/Ext4.md) （具有 extents 支持的）、[btrfs](../../02_系统管理与内核/文件系统/Btrfs.md) 或者 [XFS](../../02_系统管理与内核/文件系统/XFS.md) 上，使用这个选项，因为它们几乎可以瞬间分配 GB 级别的大文件。不要在传统的文件系统，比如 ext3 上，使用 falloc，因为预分配会消耗与正常分配相同的时间，还会阻塞 aria2 下载。

> **提示：**要查看完整的配置选项，参见 `aria2c --help=#all` 和 [aria2c(1) § OPTIONS](https://man.archlinux.org/man/aria2c.1#OPTIONS)。

### 示例 aria2.rapidshare

```
http-user=USER_NAME
http-passwd=PASSWORD
allow-overwrite=true
dir=/file/Downloads
file-allocation=falloc
enable-http-pipelining=true
input-file=/file/input.rapidshare
log-level=error
max-connection-per-server=2
summary-interval=120
```

#### 选项细节

- `http-user=USER_NAME`: 设置 HTTP [用户名](https://en.wikipedia.org/wiki/User_(computing))为 USER_NAME。这会影响所有的 [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)。
- `http-passwd=PASSWORD`: 设置 HTTP [密码](https://en.wikipedia.org/wiki/Password)为 PASSWORD。这会影响所有的 URI。
- `allow-overwrite=true`: 如果控制文件不存在，重新下载。（默认值：false）
- `dir=/file/Downloads`: 存储下载文件到 `/file/Downloads`。
- `file-allocation=falloc`: 在下载开始前，调用 [posix_fallocate(3)](https://man.archlinux.org/man/posix_fallocate.3) 分配磁盘空间。（默认值：prealloc）
- `enable-http-pipelining=true`: 启用 [HTTP/1.1 流水线](https://en.wikipedia.org/wiki/HTTP_Pipelining)来降低网络延迟的影响，并减少数据用量。（默认值：false）
- `input-file=/file/input.rapidshare`: 从 `/file/input.rapidshare` 中的 URI 下载文件，URI 以 TAB 分割。
- `log-level=error`: 设置日志级别，只输出错误。（默认值：debug）
- `max-connection-per-server=2`: 设置每台服务器每个文件的最大链接数为 2。（默认值：1）
- `summary-interval=120`: 每 120 秒输出下载进度汇总。（默认值：60）2

#### 备注

- 因为 `aria2.rapidshare` 包含用户名和密码，建议将该文件权限设置为 600 或类似的权限。

```
$ cd /file
$ chmod 600 /file/aria2.rapidshare
$ ls -l
total 128M
-rw------- 1 arch users  167 Aug 20 00:00 aria2.rapidshare
```

2 `summary-interval=0`: 不显示下载进度汇总，可以提高总体性能。日志仍然会按照 `log-level` 选项的配置输出。

> **提示：**示例的配置文件可以用于[Hotfile](http://www.hotfile.com/)，[DepositFiles](https://depositfiles.com/) 等网站。

> **注意：**命令行选项永远优先于配置文件中的选项。

### 示例 aria2.bittorrent

```
bt-seed-unverified
max-overall-upload-limit=1M
max-upload-limit=128K
seed-ratio=5.0
seed-time=240
```

#### 选项细节

- `bt-seed-unverified=false`: 在做种前不检查文件哈希。（默认值：true）
- `max-overall-upload-limit=1M`: 设置全局最大上传速度为 1MB/sec。（默认值：0）
- `max-upload-limit=128K`: 设置每个种子的最大上传速度为 128K/sec。（默认值：0）
- `seed-ratio=5.0`: 下载完成后持续做种直到分享率达到 5.0。（默认值：1.0）
- `seed-time=240`: 下载完成后做种 240 分钟。

> **注意：**如果同时设置了 `seed-ratio` 和 `seed-time`，当至少一个条件满足时就会停止做种。

### 示例 aria2.daemon

这个配置可以用于以服务启动的 aria2。可以与下面的前端一起使用。注意 rpc-user 与 rpc-pass 选项已经被弃用，但许多前端还不支持新的认证系统。记得修改用户名、密码和下载目录。

```
continue
daemon=true
dir=/home/aria2/Downloads
file-allocation=falloc
log-level=warn
max-connection-per-server=4
max-concurrent-downloads=3
max-overall-download-limit=0
min-split-size=5M
enable-http-pipelining=true

enable-rpc=true
rpc-listen-all=true
rpc-user=rpcuser
rpc-passwd=rpcpass
```

## 前端

> **注意：**前端的设置不会影响 `aria2` 本身的配置，不确定不同的前端是否会在提供了自定义配置时，复用 `aria2` 的配置。用户应当确认选择的工具能效实现，并能持久保存配置。（比如 uGet 提供了自己的 `aria2` 命令行，并且会在重启间保存配置）。

### Web UIs

> **注意：**这些前端需要 `aria2c` 以 `--enable-rpc` 选项启动。它们应该在你本地的设备，而不是远程服务器上运行。

- **WebUI** — Aria2 的 HTML 前端。

: [https://github.com/ziahamza/webui-aria2](https://github.com/ziahamza/webui-aria2) || [webui-aria2-git](https://aur.archlinux.org/packages/webui-aria2-git/)AUR

- **aria2rpc** — 用于连接远程 `aria2c` 实例的命令行工具。如果安装了 `aria2c`，该程序可以在 `/usr/share/doc/aria2/xmlrpc/aria2rpc` 下找到。

: [https://github.com/tatsuhiro-t/aria2/blob/master/doc/xmlrpc/aria2rpc](https://github.com/tatsuhiro-t/aria2/blob/master/doc/xmlrpc/aria2rpc) || [aria2](https://archlinux.org/packages/?name=aria2)包

- **AriaNg** — 纯 HTML & JavaScript 实现的aria2前端，也提供桌面客户端。

: [https://github.com/mayswind/AriaNg](https://github.com/mayswind/AriaNg) || [ariang-git](https://aur.archlinux.org/packages/ariang-git/)AUR, [ariang-native-electron-git](https://aur.archlinux.org/packages/ariang-native-electron-git/)AUR

### 其他 UIs

> **注意：**这些前端**不需要** `aria2c` 以 `--enable-rpc` 选项启动。

- **aria2fe** — 一个命令行下载工具 aria2 的 GUI。

: [https://sourceforge.net/projects/aria2fe/](https://sourceforge.net/projects/aria2fe/) || [aria2fe](https://aur.archlinux.org/packages/aria2fe/)AUR

- **Persepolis** — Aria2 下载管理器的图形前端，具有许多功能，支持 HTTP 和 FTP。

: [https://github.com/persepolisdm/](https://github.com/persepolisdm/) || [persepolis](https://archlinux.org/packages/?name=persepolis)包

- **uGet** — 功能丰富的 GTK/CLI 下载管理器，可以通过内置的插件，使用 aria2 作为后端。

: [https://ugetdm.com](https://ugetdm.com) || [uget](https://archlinux.org/packages/?name=uget)包

- **Motrix** — 基于 aria2 的全功能下载管理器，支持 HTTP、FTP、BitTorrent 和 Magnet。

: [https://github.com/agalwood/Motrix](https://github.com/agalwood/Motrix) || [motrix](https://aur.archlinux.org/packages/motrix/)AUR

## 提示与技巧

### 下载但不安装软件包

只需要用下面的命令：

```
# pacman -Sp packages | aria2c -i -
```

`pacman -Sp` 在标准输出里打印软件包的 URL，而不是下载它们，然后用 `|` 管道传给下一个命令。最后，`aria2c -i -` 里的 `-i` 意味着下载文件的 URL 应该从文件里指定。但如果传入了 `-`，就从 stdin 里读取 URL。

### pacman XferCommand

参见 [Pacman/提示和技巧#aria2](../../01_安装与包管理/包管理器/Pacman_提示和技巧.md#aria2).

### 改变 User Agent

一些站点会根据你的 User Agent 过滤请求，由于 aria2 并不出名，使用主流下载器或浏览器的 User Agent 可能会更好。只需要像这样使用 `-U` 选项：

```
$ aria2c -UWget http://some-url-to-download/file.xyz
```

你可以任意指定 User Agent，比如 **\-UMozilla/5.0** 等。

### 在 makepkg 中使用 aria2

你可以使用 aria2 代替 [curl](https://archlinux.org/packages/?name=curl)包 来下载源文件，只需要像下面一样更改 `DLAGENTS` 变量：

```
/etc/makepkg.conf
```

```
[...]
DLAGENTS=('ftp::/usr/bin/aria2c -UWget -s4 -x4 %u -o %o --follow-metalink=mem'
          'http::/usr/bin/aria2c -UWget -s4 -x4 %u -o %o --follow-metalink=mem'
          'https::/usr/bin/aria2c -UWget -s4 -x4 %u -o %o --follow-metalink=mem'
          'rsync::/usr/bin/rsync --no-motd -z %u %o'
          'scp::/usr/bin/scp -C %u %o')
[...]
```

> **注意：**用 `-UWget` 选项改变 user agent 为 Wget，可以防止下载文件时，站点根据不同的 user agent 提供不同的响应。由于 aria2 不是著名的下载器，可能会被站点识别为浏览器而不是下载器，所以使用 Wget 的 user agent 可以在大多数情况下解决问题。使用`--follow-metalink=mem`选项来避免将metalink下载为单独的文件导致校验检查失败。

### 作为守护进程使用 aria2

要使用 aria2 作为守护进程，你可以[写一个systemd用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)。比如：

```
~/.config/systemd/user/aria2cd.service
```

```
[Unit]
Description=aria2 Daemon

[Service]
ExecStart=/usr/bin/aria2c --conf-path=/path/to/conf

[Install]
WantedBy=default.target
```

[#示例 aria2.daemon](#示例_aria2.daemon) 展示了配置文件示例。

### 使用 aria2 做种

要做种下载好的种子，使用 `--check-integrity` 选项：

```
$ aria2c --check-integrity=true --seed-ratio=0.0 --dir="/path/to/iso" "/path/to/torrent/archlinux-2020.03.01-x86_64.iso.torrent"
```

指定 `--seed-ratio=0.0` 选项可以永久做种。

## 另请参见

- [aria2 手册](https://aria2.github.io/manual/en/html/index.html) - 官方网站
- [aria2 用例](https://aria2.github.io/manual/en/html/aria2c.html#example) - 官方网站

## 11.4　RTorrent

*难度：进阶 ｜ 篇幅：14.6 KB ｜ 所属：BitTorrent*

将要讲到：安装、配置、管理、通过Screen使用rTorrent、其他提示、See Also

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** Last updated in 2011（在 [Talk:RTorrent#](https://wiki.archlinuxcn.org/wiki/Talk:RTorrent) 中讨论）

[rTorrent](https://github.com/rakshasa/rtorrent) 是一个非常简洁、优秀、非常轻量的BT客户端. 它使用了 [ncurses](https://en.wikipedia.org/wiki/ncurses) 库以 C++ 编写, 因此它完全基于文本并在终端中运行. 将 rTorrent 用在安装有 [GNU Screen](../../06_软件与应用/GNU/GNU%20Screen.md) 和 [Secure Shell](../SSH/安全外壳协议.md) 的低端系统上作为远程的 BT 客户端是非常理想的。

## 安装

从[官方源](../../00_关于与社区/关于%20Arch/官方仓库.md) [安装](../../01_安装与包管理/包管理器/Pacman.md) [rtorrent](https://archlinux.org/packages/?name=rtorrent)包 包。

你也可以从 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 中安装 [rtorrent-git](https://aur.archlinux.org/packages/rtorrent-git/)AUR 或 [rtorrent-extended](https://aur.archlinux.org/packages/rtorrent-extended/)AUR\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\]。

## 配置

> **注意：**查看关于此部分的 rTorrent Wiki 文章获取更多信息： [Common Tasks in rTorrent for Dummies](https://github.com/rakshasa/rtorrent/wiki/Common-Tasks-in-rTorrent)

在运行 rTorrent 前,首先要创建默认配置文件，该文件可以从 `/usr/share/doc/rtorrent/rtorrent.rc` 上找到，并将其另存为 `.rtorrent.rc` 放到你的主目录中.

```
$ cp /usr/share/doc/rtorrent/rtorrent.rc ~/.rtorrent.rc
```

保存后, 使用你喜爱的文本编辑器打开它并进行有必要的改动. 可以简单地去掉那些你需要使用的选项的注释， 如需更详细的资料,请访问[rTorrent Common Tasks](https://github.com/rakshasa/rtorrent/wiki/Common-Tasks-in-rTorrent)

### 性能优化

> **注意：**查看关于此部分的 rTorrent wiki 文章获得更多信息： [性能优化](https://github.com/rakshasa/rtorrent/wiki/Performance-Tuning)

下面这些配置的值主要取决于你的系统和网络速度. 要寻找最佳配置，请访问下面网站并按照指示操作: [优化 BitTorrent 下载速度](https://torrentfreak.com/optimize-your-BitTorrent-download-speed)

```
# 每个种子所允许的最大最小连接数
#min_peers = 40
max_peers = 52

# 同上，针对的是已经完成的种子 （-1 表示和下载中的种子一致）
#min_peers_seed = 10
max_peers_seed = 52

# 每个种子的最大同时上传数
max_uploads = 8

# 全局的上传与下载速度限制（以 KB 为单位），“0”表示无限制
download_rate = 200
upload_rate = 28
```

`check_hash` 选项将在下载完成或 rTorrent 重新启动时对文件进行 Hash 校验。这将确保你获得/做种的文件没有错误：

```
check_hash = yes
```

### 创建和管理文件

`directory` 选项将决定你的 torrent 数据被保存在哪. 如果你想改变默认保存目录，请务必输入正确的绝对路径; rtorrent 有一个奇怪的 Bug，一些时候它不考虑相对路径(如 ~/torrents):

```
directory = /home/[user]/torrents/
```

`session` 选项将允许 rTorrent 保存你的 torrents 的进度. 一定要创建一个会话目录 `.session` (普通用户运行:`$ mkdir ~/.session`):

```
session = /home/[user]/.session/
```

`schedule` 选项将使 rTorrent 监视一个特定目录中的 .torrent 文件。使用这一选项时一定要小心，这将移动 .torrent 文件到你的会话目录下，并将之重命名为其 Hash 值。 当你看到了一个你想要下载的种子文件，你可以使用你的浏览器将这一种子文件保存到这一监视目录下， rTorrent 将会自动的开始下载这一种子文件的内容。必须确保要监视的目录已经被创建(以普通用户身份执行: mkdir ~/watch):

```
# 监听目录中的新的种子文件，并停止那些已经被删除部分的种子
#schedule = watch_directory,5,5,load_start=./watch/*.torrent
#schedule = untied_directory,5,5,stop_untied=
schedule = watch_directory,5,5,load_start=/home/[user]/watch/*.torrent
schedule = untied_directory,5,5,stop_untied=
schedule = tied_directory,5,5,start_tied=
```

下面的 `schedule` 选项将使 rTorrent 在磁盘空间不足时停止。 这对于[Seedbox](https://en.wikipedia.org/wiki/Seedbox)这种磁盘空间非常有限的设备来说是很有用的。按照你的喜好来改变下面的数值：

```
# 当磁盘空间不足时停止下载
schedule = low_diskspace,5,60,close_low_diskspace=100M
```

### 端口配置

`port_range` 选项将指定选用哪一个端口去侦听。建议使用高于 49152 的端口。虽然 rTorrent 允许使用多个的端口，还是建议使用单个的端口。

```
port_range = 49164-49164
```

Additionally, make sure port forwarding is enabled for the proper port(s) (see: [Port Forward Guides](https://portforward.com/english/routers/port_forwarding/routerindex.htm)).

### 附加设置

`encryption` 选项使能加密功能。 使用加密功能十分重要not only for yourself, but also for your peers in the torrent swarm. 人们也许需要对它们的网络提供商模糊其带宽使用。即使你不需要这些保护，开启加密也不会对你有所负作用。详细信息请见： [Bittorrent Protocol Encryption](https://en.wikipedia.org/wiki/BitTorrent_protocol_encryption)

```
# 加密选项，设为0（默认情况）或下面的任何一个：
# allow_incoming, try_outgoing, require, require_RC4, enable_retry, prefer_plaintext
#
# 如下例中的值将允许将接入连接加密，开始时以非加密方式作为连接的输出方式，
# 如行不通则以加密方式进行重试，在加密握手后，优先选择将纯文本以 RC4 加密
#
# encryption = allow_incoming,enable_retry,prefer_plaintext
encryption = allow_incoming,try_outgoing,enable_retry
```

下面的选项用以加入 DHT 支持。如果你使用了 public trackers，你可能希望使能 DHT 以获得更多的连接。如果你仅仅使用了私有的连接，**请不要使能 DHT**，因为这将降低你的速度，并可能造成一些隐密的危险。一些私有连接甚至会就使用 DHT 而向你发出警告。

```
# 使能对 trackerless torrents 或当所有的 tracker 都停止情况下的 DHT 支持。
# 可以被设置为 "disable" （完全禁止 DHT），“off“（不启动 DHT）， "auto"（按需要启
# 动或停止 DHT）或者 "on" (即时开启 DHT)。
# 默认设置为 "off"。要使 DHT 工作，会话目录必须被定义。
#
# dht = auto

# UDP 使用 UHT的端口
#
# dht_port = 6881

# 使能 peer 的交换 (对那些没有被标记为私有的 torrent)
#
# peer_exchange = yes
```

> **注意：**See the rTorrent wiki article on this subject for more information: [Using DHT](https://github.com/rakshasa/rtorrent/wiki/Using-DHT)

## 管理

| Cmd | Action |
| --- | --- |
| Ctrl-q | Quit application |
| Left | Returns to the previous screen |
| Right | Goes to the next screen |
| Backspace | Adds the specified \*.torrent |

rTorrent依靠用户输入的专有的快捷键.可在官方网站上查看完整的: [rTorrent用户指南](https://github.com/rakshasa/rtorrent/wiki/User-Guide)

> **注意：**快速敲击 `Ctrl-q` 两次会使得 rTorrent 关闭，而不会等待向连接的 tracker 发送停止声明。

这里是一些最基础的快速参考：

- Control-q : closes rTorrent, done twice makes the program shutdown without waiting to send stopping information to the trackers.
- Left arrow : returns to the previous screen.
- Right arrow : goes to the next screen.
- a|s|d : increase global upload throttle about 1|5|50 KB/s
- A|S|D : increase global download throttle about 1|5|50 KB/s
- z|x|c : decrease global upload throttle about 1|5|50 KB/s
- Z|X|C : decrease global download throttle about 1|5|50 KB/s
- Control-S : starts download
- Control-D : stops an active download, removes a stopped download.
- \+ or - : changes the download priority of selected torrent.
- Backspace : adds the specified .torrent. After pressing this button write full path or URL of .torrent file. You can use Tab and other tricks from bash.

### Redundant mapping

`Ctrl-s` is often used for terminal control to stop screen output while `Ctrl-q` is used to start it. These mappings may interfere with rTorrent. Check to see if these terminal options are bound to a mapping:

```
$ stty -a
```

```
...
swtch = <undef>; start = ^Q; stop = ^S; susp = ^Z; rprnt = ^R; werase = ^W; lnext = ^V;
...
```

To remove the mappings, change the terminal characteristics to undefine the aforementioned special characters (i.e. `stop` and `start`):

```
# stty stop undef
# stty start undef
```

To remove these mappings automatically at startup you may add the two preceding commands to your `~/.bashrc` file.

## 通过Screen使用rTorrent

Screen 是允许CLI应用程序在后台运行并支持X运行的一个程序.

安装:

```
pacman -S screen
```

然后复制screenrc到你的用户主目录:

```
cp /etc/screenrc ~/.screenrc
```

要总是通过Screen来运行rTorrent,在.screenrc文件中添加以下内容:

```
screen -t rtorrent rtorrent
```

要运行screen和rTorrent, 只要简单的在终端运行*screen*就可以了. Control-a followed by d will detach screen, and running *screen -r* will open screen again.

### On a remote machine

Most setups of rTorrent on a remote machine involve using Screen. Supposing you have a detached Screen session with rtorrent on the remote machine (and the option to [SSH](../SSH/安全外壳协议.md) into it), you can gain access to it using:

```
/usr/bin/ssh -t -p <ssh port on remote machine> <user>@<remote machine> screen -RD
```

If you want immediate access on startup, you will need to [upload a key](https://web.archive.org/web/20090612232350/http://bloggerdigest.blogspot.com/2006/11/ssh-auto-login-or-passwordless-login.html) from your machine to remote host (so you will not be prompted for a password) and setup a terminal to run the command above. An inittab example using [rungetty](https://aur.archlinux.org/packages/rungetty/)AUR\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\] on virtual console 4:

```
sam:45:respawn:/sbin/rungetty tty4 -u <local user> -- /usr/bin/ssh -t -p <ssh port on remote machine> <remote user>@<remote machine> screen -RD
```

### Screen后台运行rTorrent


I use this on my home server to run rtorrent w/ screen as a daemon. With the username *rtorrent* Just create an *rtorrent* file in your */etc/rc.d/* and add the following code.

```
#!/bin/bash

. /etc/rc.conf
. /etc/rc.d/functions

case "$1" in
  start)
    stat_busy "Starting rtorrent"
    su rtorrent -c 'screen -d -m rtorrent' &> /dev/null
    if [ $? -gt 0 ]; then
      stat_fail
    else
      add_daemon rtorrent
      stat_done
    fi
    ;;
  stop)
    stat_busy "Stopping rtorrent"
    killall -w -s 2 /usr/bin/rtorrent &> /dev/null
    if [ $? -gt 0 ]; then
      stat_fail
    else
      rm_daemon rtorrent
      stat_done
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
exit 0
```

On a remote computer I use the following script to connect to the server's daemon process:

```
ssh -t rtorrent@192.168.1.10 'screen -r'
```

## 其他提示

- To use rTorrent with a tracker that uses https, do the following as root:

```
cd /etc/ssl/certs
wget --no-check-certificate https://web.archive.org/web/20160505025153/https://www.geotrust.com/resources/root_certificates/certificates/Equifax_Secure_Global_eBusiness_CA-1.cer
mv Equifax_Secure_Global_eBusiness_CA-1.cer Equifax_Secure_Global_eBusiness_CA-1.pem
c_rehash
```

And from now on run rTorrent with:

```
rtorrent -o http_capath=/etc/ssl/certs
```

Be sure to change .screenrc to reflect this change if you use screen:

```
screen -t rtorrent rtorrent -o http_capath=/etc/ssl/certs
```

- To create .torrent files, I recommend [mktorrent](https://archlinux.org/packages/?name=mktorrent)包 for commandline interface or [RubyTorrent](https://web.archive.org/web/20090706140402/http://benclarke.ca/rubytorrent/) for GUI.

### Send Text Message Upon Torrent Completion Using GMail

Cell phone providers allow you to "email" your phone:

```
Verizon: 10digitphonenumber@vtext.com
Former AT&T customers: 10digitphonenumber@mmode.com
Sprint: 10digitphonenumber@messaging.sprintpcs.com
T-Mobile: 10digitphonenumber@tmomail.net
Nextel: 10digitphonenumber@messaging.nextel.com
Cingular: 10digitphonenumber@cingularme.com
Virgin Mobile: 10digitphonenumber@vmobl.com
Alltel: 10digitphonenumber@alltelmessage.com OR
10digitphonenumber@message.alltel.com
CellularOne: 10digitphonenumber@mobile.celloneusa.com
Omnipoint: 10digitphonenumber@omnipointpcs.com
Qwest: 10digitphonenumber@qwestmp.com
```

If you have Verizon, your cell phone's "email" is 5551234567@vtext.com

- Install **Heirloom's mailx** program:

```
pacman -S mailx-heirloom
```

- Clear the /etc/nail.rc file and enter:

```
set smtp=smtp.gmail.com:587
set smtp-use-starttls
set ssl-verify=ignore
set ssl-auth=login
set smtp-auth-user=USERNAME@gmail.com
set smtp-auth-password=PASSWORD
```

Now to send the text, we must pipe a message to the mailx program.

- Make a bash script (/path/to/mail.sh):

```
echo "Torrent Done" | mailx 5551234567@vtext.com
```

- And finally, add the important ~/.rtorrent.rc line:

```
on_finished = move_complete,"execute=/path/to/mail.sh"
```

**Note:** The "move complete" will not affect anything. I could not get it to work without it.

## See Also

Screen Tips

A Detailed Intro to Bittorrent including definition of terms [terms](https://web.archive.org/web/20101121074832/http://www.dessent.net/btfaq/#terms)

## 11.5　Transmission

*难度：进阶 ｜ 篇幅：16.2 KB ｜ 所属：BitTorrent*

将要讲到：安装、图形界面的配置、Transmission 守护程序和命令行、通知、Web 界面、作为makepkg DLAGENT的用法

[Transmission](https://www.transmissionbt.com/) 是一个轻量级、跨平台的 BitTorrent 客户端。

## 安装

有多种可选的软件包：

- [transmission-cli](https://archlinux.org/packages/?name=transmission-cli)包 - 包括命令行工具、守护程序和 [#Web 界面](#Web_界面)。
- [transmission-gtk](https://archlinux.org/packages/?name=transmission-gtk)包 - [GTK](../../07_开发与工具/控件工具集/GTK.md)\+ 4 图形界面。
- [transmission-qt](https://archlinux.org/packages/?name=transmission-qt)包 - [Qt](../../07_开发与工具/控件工具集/Qt.md) 6 图形界面。
- [tremc](https://aur.archlinux.org/packages/tremc/)AUR 或 [tremc-git](https://aur.archlinux.org/packages/tremc-git/)AUR - 用于守护程序的 Curses 界面。
- [stig](https://aur.archlinux.org/packages/stig/)AUR 或 [stig-git](https://aur.archlinux.org/packages/stig-git/)AUR – 用于守护程序的 Curses 界面。
- [transmission-remote-gtk](https://archlinux.org/packages/?name=transmission-remote-gtk)包 – 用于守护程序的 GTK+ 3 界面。

## 图形界面的配置

两种带 GUI 的版本 *transmission-gtk* 和 *transmission-qt* 都可以在没有后端守护程序的情况下独立运行。

GUI 版本都被配置为开箱即用，但用户可能仍需要更改一些设置。GUI 配置文件的默认路径是 `~/.config/transmission`。

在 Transmission 的 Github 上，有一份各种选项如何配置的教程： [https://github.com/transmission/transmission/blob/main/docs/Editing-Configuration-Files.md](https://github.com/transmission/transmission/blob/main/docs/Editing-Configuration-Files.md).

## Transmission 守护程序和命令行

*transmission-cli* 支持的命令包括：

: *transmission-daemon*: 启动守护程序。
: *transmission-remote*: 调用本地或远程守护程序的[命令行界面](https://en.wikipedia.org/wiki/Command-line_interface)，后面跟着希望守护程序运行的命令。
: *transmission-show*: 返回指定种子文件的信息。
: *transmission-create*: 创建一个新的种子文件。
: *transmission-edit*: 新增、删除或替换一个 tracker 服务器的 announce URL。
: *transmission-cli*: ([已弃用](https://github.com/transmission/transmission/commit/950387ab5a443629598f93c057f41150707866ab)) 启动一个非守护程序的本地 *transmission* 实例，用于手动下载一个种子。
: *tremc*: (需要 [tremc-git](https://aur.archlinux.org/packages/tremc-git/)AUR) 为本地或远程守护程序启动一个 [curses](https://en.wikipedia.org/wiki/curses_(programming_library)) 界面。

### 守护程序的启动与停止

就像 [#选择一个用户](#选择一个用户)里面的说明那样，transmission 的守护程序可以按以下方式运行：

- 作为 *transmission* 用户运行，只要[用 systemd](../../01_安装与包管理/初始化/Systemd.md#使用单元) start/enable `transmission.service` 服务即可。

: 你可以按照 [#选择一个用户](#选择一个用户)里的说明更改默认用户。

- 用你自己的账户，只要在账户下运行：

    ```
    $ transmission-daemon
    ```

启动守护程序将会创建一个初始的 *transmission* 配置文件。详情参阅 [#配置守护程序](#配置守护程序)。

守护程序可以这样来停止：

```
$ pkill -3 transmission-daemon
```

另一种停止 transmission 的方法是使用 *transmission-remote* 命令：

```
$ transmission-remote --exit
```

### 减少多余的日志

运行 transmission-daemon 可能会产生许多不需要的日志条目。只要用一个小脚本包装一下再启动就可以过滤日志输出。以下示例可以提供一些启发：

```
transwrap.sh
```

```
#!/bin/zsh
killall transmission-daemon 2> /dev/null
transmission-daemon --foreground --log-info 2>&1 | while read line; do
	echo $line |
		grep -v "announcer.c:\|platform.c:\|announce done (tr-dht.c:" |
		grep -v "Saved.*variant.c:" |
		while read line; do
			echo $line | grep -q "Queued for verification (verify.c:" &&
				notify-send --app-name="Transmission Started" "${line#* * }"
			echo $line | grep -q "changed from .Incomplete. to .Complete." &&
				notify-send --app-name="Transmission Complete" "${line#* * }"
			echo $line | systemd-cat --identifier="TransWrap" --priority=5
		done 2>&1 > /dev/null
	done&disown
```

### 仅当连接到网络时运行

#### Netctl

你可能只想在连接某些网络时运行 transmission。下面的脚本会先检查连接是否在授权的网络列表中，是的话再继续启动 transmission-daemon。

```
/etc/netctl/hooks/90-transmission.sh
```

```
#!/bin/bash

# The SSIDs for which we enable this.
declare -A ssids=(
    ["network_1"]=y
    ["network_2"]=y
)

if [[ ${ssids[$SSID]} ]]; then
    case $ACTION in
        CONNECT|REESTABLISHED)
            # Need to wait, otherwise doesn't seem to bind to 9091.
            sleep 30
            systemctl start transmission
            ;;
        *)
            systemctl stop transmission
            ;;
    esac
fi
```

#### 选择一个用户

选择你想如何运行 `transmission`：

- 作为独立用户运行，默认用户是 `transmission`（推荐，因为安全性更高）。

默认情况下，*transmission* 会创建名为 `transmission` 的用户和组（它的主目录的文件位于 `/var/lib/transmission/`），并且用这个“账户”运行。这是一种安全措施，因为这样 *transmission* 及其下载的文件将无法访问 `/var/lib/transmission/` 目录之外的文件。配置、操作和访问下载的文件需要使用 “root” 权限完成（比如用 [sudo](../../08_安全与加密/安全/Sudo.md)）。

- 在你自己的账户下运行。设置这种方式需要[重写](../../01_安装与包管理/初始化/Systemd.md#修改现存单元文件)已提供的服务文件并在其中指定你的用户名：

```
/etc/systemd/system/transmission.service.d/username.conf
```

```
[Service]
User=your_username
```

### 配置守护程序

[启动守护程序](#守护程序的启动与停止)来创建缺省的配置文件。

- 如果用 `transmission` 这个用户来运行 Transmission，配置文件会存放在 `/var/lib/transmission/.config/transmission-daemon/settings.json`。

- 如果用你自己的账户运行 Transmission，配置文件会存放在 `~/.config/transmission-daemon/settings.json`。

使用一个 Transmission 客户端或者用自带的 web 界面（在支持的浏览器中用 [http://localhost:9091](http://localhost:9091) 来访问）可以自定义守护程序。

在 Transmission 的网站上，有一份各种选项如何配置的教程：[https://github.com/transmission/transmission/blob/main/docs/Editing-Configuration-Files.md](https://github.com/transmission/transmission/blob/main/docs/Editing-Configuration-Files.md)

> **注意：**
>
> - 如果你想用一个文本编辑器手动编辑配置文件，请先 [停止守护程序](#守护程序的启动与停止)；否则当它停止时将会覆盖配置文件。
> - 另一种方式是，可以用 SIGHUP 信号指示守护程序重新加载其配置，运行 `` kill -s SIGHUP `pidof transmission-daemon` `` 即可。

一个给那些用 `transmission` 账户运行的用户的建议是，用适当的权限创建一个共享的下载目录，这样就能同时允许 `transmission` 账户和其他账户访问下载文件，也能相应地更新配置文件。例如：

```
# mkdir /mnt/data/torrents
# chown -R facade:transmission /mnt/data/torrents
# chmod -R 775 /mnt/data/torrents
```

现在 `/mnt/data/torrents` 目录可供系统账户 `facade` 和 `transmission` 用户所属的 `transmission` 组访问。非常不鼓励使这个目录可读写（即不要将目录 *chmod* 到 *777*），而是应该将个别用户/组的部分权限授予部分目录。

> **注意：**如果 `/mnt/data/torrents` 位于一个可移除的设备上，比如这是一个 `/etc/fstab` 条目且带有 `nofail` 选项，那么 Transmission 会报告说没有找到文件。要解决这个问题，可以在 `/etc/systemd/system/transmission.service.d/transmission.conf` 文件的 `[Unit]` 一节中加入 `RequiresMountsFor=/mnt/data/torrents`。

另一种方法是把你的账户加入 `transmission` 用户组 (`#usermod -a -G transmission yourusername`)，然后修改 `/var/lib/transmission` 和 `/var/lib/transmission/Downloads` 目录的权限设置，以允许 `transmission` 组成员对这两个目录的 `rwx` 访问权。

#### Host 白名单

如果你想用远程主机的主机名来访问位于该主机上的 Transmission 守护程序，你需要把主机名加入 `settings.json` 文件中的 `rpc-host-whitelist` 字段中。 否则你会在连接主机时收到一条 "421 Misdirected Request" 错误。

如果你用该服务器的 IP 地址来访问守护程序，这一步可以省略。

#### 监视目录

如果你想*自动添加某个文件夹中的 .torrent 文件*，但是配置文件中的 `watch-dir` 和 `watch-dir-enabled` 选项都不起作用，可以带 `-c /path/to/watch/dir` 参数启动 transmission 守护程序。

如果你在用 systemd，按照 [systemd#修改现存单元文件](../../01_安装与包管理/初始化/Systemd.md#修改现存单元文件)里面的描述编辑 `transmission.service` 单元文件。

#### 启用 IPv6

默认情况下，守护程序仅监听 IPv4 连接。要同时监听 IPv6 连接，把 `settings.json` 里的 `rpc-bind-address` 选项改为 `"::"`。

#### CLI 使用示范

如果你想删除所有已完成的种子，可以用下面的命令（用你自己的用户名和密码）：

```
# transmission-remote -n 'username:password' -l | grep 100% | awk '{print $1}'| paste -d, -s | xargs -i transmission-remote -t {} -r
```

为已下载的文件做种：

```
# transmission-remote [HOST] --torrent=example.torrent -a example.torrent --verify --download-dir=/dir/to/folder --start
```

## 通知

停止 transmission-daemon 并在`.config/transmission-daemon/settings.json` 中添加以下内容：

```
   "script-torrent-added-enabled": true,
   "script-torrent-added-filename": "path/to/transmission-handler.sh",
   "script-torrent-done-enabled": true,
   "script-torrent-done-filename": "path/to/transmission-handler.sh",
```

```
transmission-handler.sh
```

```
#!/bin/bash

percentage=$(transmission-remote -t $TR_TORRENT_ID -l | awk -v ID="$TR_TORRENT_ID" '$0 ~ ID{print $2}')

if [ $percentage != "100%" ]
then
     notify-send --app-name="Transmission Started" "Transmission: started $TR_TORRENT_NAME"
else
     notify-send --app-name="Transmission Complete" "Transmission: downloaded $TR_TORRENT_NAME"
fi
```

## Web 界面

### GUI 方式

当 Transmission 安装完毕后就可以轻易架起 web 界面。你只需要单击 *edit* 菜单，选择 *preferences*。单击 *Remote* 选项卡并启用 *Allow remote access*。

此处你可以修改默认的监听端口 9091。

单击 *Use authentication* 并填写用户名和密码就可以启用身份认证。

要提高安全性，可以启用 ***Only allow these IP addresses*** 限制来自任意 IP 的访问。

现在你可以单击 *Open web client* （用你的默认浏览器打开）或手动访问 `http://*TARGET_IP_ADDRESS*:*PORT*` 来启动 web 界面。

如果没有修改监听端口，默认是 9091。在这种情况下访问链接是 `[http://localhost:9091](http://localhost:9091)`。

> **注意：**记住，必须安装 [transmission-cli](https://archlinux.org/packages/?name=transmission-cli)包。

### CLI 方式

设置 web 界面甚至不需要借助图形界面，因为守护程序提供了类似的选项。你无需指定任何参数即可访问 web 界面，可参阅 [#守护程序的启动与停止](#守护程序的启动与停止)。

不过，你仍然可以指定在前一节中看到的所有选项：

```
$ transmission-daemon --auth --username arch --password linux --port 9091 --allowed "127.0.0.1"
```

等同于

```
$ transmission-daemon -t -u arch -v linux -p 9091 -a "127.0.0.1"
```

> **注意：**如果你设置了用户名和密码，那么所有后续对`transmission-remote`的命令都需要使用`--auth username:password`进行身份验证。请参阅：[transmission-remote commands are erroring with Unauthorized user](https://stackoverflow.com/a/29712044)

### 可选的网页 UI 主题

如果觉得 web app 的默认用户界面过时、不好看，试试这个简单CSS主题： [https://git.eigenlab.org/sbiego/transmission-web-soft-theme](https://git.eigenlab.org/sbiego/transmission-web-soft-theme)

另一个完整替代方案，可以和默认界面共存： [https://github.com/ronggang/transmission-web-control](https://github.com/ronggang/transmission-web-control)

## 作为makepkg DLAGENT的用法

Transmission可以连同[transmission-dlagent](https://aur.archlinux.org/packages/transmission-dlagent/)AUR下载代理一起，作为[makepkg](../../08_安全与加密/OpenPGP/Makepkg.md)的磁链下载代理。

磁链URI需要将其前缀从`magnet:?`更改为`magnet://`。

## 故障排除

### 无法使用网络连接至守护程序

在 `network.service` 初始化后守护程序就会开始运行。但是，如果启用了 `dhcpcd` 服务而不是特定设备的服务（比如 `dhcpcd@enp1s0.service`），可能 Transmission 会因为启动过早而无法绑定到相应的网络接口。于是 web 界面无法访问。一种可能的解决方案是在 systemd 单元的[配置文件](../../01_安装与包管理/初始化/Systemd.md#修改现存单元文件)里增加 `Requires` 行。

```
/etc/systemd/system/transmission.service.d/fixdep.conf
```

```
[Unit]
Requires=network.target
```

### 无法显示 Web 界面

```
404: Not Found

Couldn't find Transmission's web interface files!

Users: to tell Transmission where to look, set the TRANSMISSION_WEB_HOME environment variable to the folder where the web interface's index.html is located.

Package Builders: to set a custom default at compile time, #define PACKAGE_DATA_DIR in libtransmission/platform.c or tweak tr_getClutchDir () by hand.
```

即使你用图形界面，也需要安装 [transmission-cli](https://archlinux.org/packages/?name=transmission-cli)包 来支持 web 界面。

### 接收/发送缓冲区设置失败

在启动时 Transmission 可能会在日志中显示如下的消息：

```
UDP Failed to set receive buffer: requested 4194304, got 425984
UDP Failed to set send buffer: requested 1048576, got 425984
```

通过使用[sysctl](../../02_系统管理与内核/内核/Sysctl.md)设置 `net.core.rmem_max=4194304` 和 `net.core.wmem_max=1048576` 可以解决。

### transmission-remote频繁超时或网络界面无响应

Transmission并未异步处理磁盘IO，因此在进行大量写入时，可能会变得无响应，详情参见。建议将Transmission的下载目录放在更快的存储设备上，如SSD而非HDD。

### transmission-gtk缺少托盘图标

GTK 4版本的托盘图标[目前无法工作](https://github.com/transmission/transmission/discussions/5065)。有一个选择是使用GTK 3版本的[transmission-gtk3](https://aur.archlinux.org/packages/transmission-gtk3/)AUR。

### 401: 未授权

在为web界面设置用户名和密码后，使用`transmission-remote`时可能会出现以下错误：

```
< h1>401: Unauthorized\Unauthorized User
```

这种情况是因为设置了用户名和密码。此后，所有来自`transmission-remote`的命令都必须经过身份验证。详情参见：[transmission-remote命令因未授权的用户而出错](https://stackoverflow.com/a/29712044)

## 更多信息

- [Transmission wiki (old)](https://trac.transmissionbt.com/wiki)
- [Transmission documentation on GitHub](https://github.com/transmission/transmission/blob/main/docs/README.md)
- [Headless Usage](https://github.com/transmission/transmission/blob/main/docs/Headless-Usage.md)
