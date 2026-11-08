# 第 2 章　Arch 项目

> 这一章处理几件读完第零册才碰得到的周边事：旧版本软件包去哪找、官方仓库的包信息怎么在网页上查、打包结果用什么查、以及没有启动介质时怎么靠网络引导装系统。
> 
> 《官方仓库 web 接口》与《网络引导》必读：前者管日常查包，后者按 BIOS、UEFI 对号入座，故障排除等卡住再看。Namcap 读安装与用法就能上手，创建模块留到写规则时。开头讲存档库的两节是同一页中英文两版，读一份就够，别当两份资料。最常踩的坑是把存档页的 mirrorlist 抄进配置——它指向带日期的旧仓库，抄下去等于钉在旧快照上。

<!-- 本章 5 节，共 37 KB -->

## 2.1　Arch Linux 存档库

*难度：进阶 ｜ 篇幅：10.6 KB ｜ 所属：Arch 项目*

将要讲到：位置、目录、常见问题、## Arch Linux repository mirrorlist、Generated on 2042-01-01、Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch

Arch Linux 存档库（**A**rch **L**inux **Archive**，*简称 ala*），以前称为 *Arch Linux 回滚机（Arch Linux Rollback Machine*，*简称 ARM*），保存了*官方仓库快照*、*iso 镜像*和 *引导（Bootstrap）程序包*的历史版本。

**用途**

- 将某个包降级到某个早期版本（最新版本不能用，我需要之前的版本）
- 将所有包恢复到某个指定的历史时刻（所有包都不能用，我要恢复到两个月之前的状态）
- 查找某个历史版本的 ISO 镜像

软件包仅会保存几年时间，之后会被移动到 [archive.org](https://en.wikipedia.org/wiki/archive.org) 的 Arch Linux Historical Archive.

## 位置

Arch Linux 存档库目前位于 [https://archive.archlinux.org/](https://archive.archlinux.org/) 和[全球镜像](https://gitlab.archlinux.org/archlinux/infrastructure/-/blob/master/docs/servers.md#archive-mirrors).

[这里](https://github.com/seblu/archivetools)的源代码可以帮助您架设自己的存档库服务器。

## 目录

**存档库**分为下列三个主目录：

```
├── iso
├── packages
└── repos
```

### /repos

[repos](https://archive.archlinux.org/repos) 这个目录包含官方仓库镜像的每日快照，按下例结构组织：

```
repos
├── 2013
│   ├── 08
│   │   └── 31
│   │       ├── community
│   │       ├── community-staging
│   │       ├── community-testing
│   │       ├── core
│   │       ├── extra
│   │       ├── gnome-unstable
│   │       ├── kde-unstable
│   │       ├── lastsync
│   │       ├── multilib
│   │       ├── multilib-staging
│   │       ├── multilib-testing
│   │       ├── pool
│   │       ├── staging
│   │       └── testing
│   ├── 09
│   │   ├── 01
│   │   ├── 02
│   │   ├── ...
│   │   ├── 21
│   │   └── 22
│   ├── 10
│   │   ├── 01
│   │   ├── 02
│   │   ├── ...
│   │
│   ├── 11
│   └── 12
├── 2014
│   ├── 01
│   │   ├── 01
│   │   ├── 02
│   │   ├── ...
│   │
│   ├── 02
│   ├── 03
│   ├── ...
│   └── 09
│       ├── 01
│       ├── ...
│       └── 28
├── last
├── month
└── week
```

注意: 最下面的三个特定目录（**last**、**week** 和 **month**）分别链接到**已同步的最新仓库版本**、**本周星期一版本**和**本月一日版本**。

### /packages

[packages](https://archive.archlinux.org/packages) 这个目录包含每个包的所有版本及其相应的数字签名。每个包一个目录，按首字母排序。

```
├── packages
│   ├── a
│   │   ├── awesome
│   │   │   ├── awesome-3.5.0-1-i686.pkg.tar.xz
│   │   │   ├── awesome-3.5.0-1-i686.pkg.tar.xz.sig
│   │   │   ├── awesome-3.5.0-1-x86_64.pkg.tar.xz
│   │   │   ├── awesome-3.5.0-1-x86_64.pkg.tar.xz.sig
│   │   │   ├── awesome-3.5.1-1-i686.pkg.tar.xz
│   │   │   ├── awesome-3.5.1-1-i686.pkg.tar.xz.sig
│   │   │   ├── ...
│   │   │
│   │   ├── ...
│   │   ├── awstats
│   │   └── axel
│   │
│   ├── b
│   ├── ...
│   └── z
```

你可以使用“魔法目录”[.all](https://archive.archlinux.org/packages/.all) 按包名访问所有包。这是一个没有子目录的结构。

```
├── packages
│   ├── .all
│   │   ├── awesome-3.5.1-1-i686.pkg.tar.xz
│   │   ├── ...
│   │   ├── zsh-5.0.2-3-i686.pkg.tar.xz
│   │   ├── zsh-5.0.2-4-i686.pkg.tar.xz
│   │   └── ...
```

可以下载一个压缩的索引文件，包含完整的软件包列表 [index.0.xz](https://archive.archlinux.org/packages/.all/index.0.xz).

```
$ curl https://archive.archlinux.org/packages/.all/index.0.xz | unxz
```

```
0ad-a14-1-i686
0ad-a14-1-x86_64
0ad-a14-2-i686
...
zziplib-0.13.62-1-x86_64
zziplib-0.13.62-2-i686
zziplib-0.13.62-2-x86_64
```

### /iso

[iso](https://archive.archlinux.org/iso) 目录按发布日期，保存官方 ISO 镜像和启动压缩包。

```
├── 2014.09.03
├── 2014.10.01
├── 2014.11.01
├── 2014.12.01
├── 2015.07.01
├── 2015.08.01
├── 2015.09.01
└── 2015.10.01
    ├── arch
    ├── archlinux-2015.10.01-dual.iso
    ├── archlinux-2015.10.01-dual.iso.sig
    ├── archlinux-2015.10.01-dual.iso.torrent
    ├── archlinux-bootstrap-2015.10.01-i686.tar.gz
    ├── archlinux-bootstrap-2015.10.01-i686.tar.gz.sig
    ├── archlinux-bootstrap-2015.10.01-x86_64.tar.gz
    ├── archlinux-bootstrap-2015.10.01-x86_64.tar.gz.sig
    ├── md5sums.txt
    └── sha1sums.txt
```

> **提示：**从 2022.10.01 开始 Arch Linux Archive 加入了 BT 种子(非磁力链). 所有种子文件可以从[发布页面](https://archlinux.org/releng/releases/) 下载。

## 常见问题

### 如何降级某个包

在 [/packages](#/packages) 中找到需要的软件包，用 pacman 获取并安装:

```
# pacman -U https://archive.archlinux.org/packages/path/packagename.pkg.tar.xz
```

pacman 会自动下载 *.sig* 文件并根据 `/etc/pacman.conf` 设置进行校验。

或者手动下载并通过 `pacman -U` 安装.

[软件包降级#自动化](../../01_安装与包管理/包管理/降级软件包.md#自动化)包含了可以简化这个过程的工具。

### 如何恢复所有包到指定日期

如果想恢复所有包到指定日期（比如2014年3月30日），你必须如下例所示编辑 `/etc/pacman.conf`，从而让 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 保持在这个时间点并且直接使用指定的服务器：

```
/etc/pacman.conf
```

```
[core]
SigLevel = PackageRequired
Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch

[extra]
SigLevel = PackageRequired
Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch

[community]
SigLevel = PackageRequired
Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch
```

或者如下例编辑 `/etc/pacman.d/mirrorlist`：

```
/etc/pacman.d/mirrorlist
```

```
##
## Arch Linux repository mirrorlist
## Generated on 2042-01-01
##
Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch
```

然后同步包数据库以强制降级：

```
# pacman -Syyuu
```

如果抱 PGP 签名错误，软件包损坏(corrupted/invalid packages),请先尝试更新 [archlinux-keyring](https://archlinux.org/packages/?name=archlinux-keyring)包 和 [ca-certificates](https://archlinux.org/packages/?name=ca-certificates)包. Alternatively, you can decide to temporarily [disable signature checking](../../01_安装与包管理/包管理器/Pacman_软件包签名.md#Disabling_signature_checking) altogether.

> **注意：**混用归档和更新镜像很不安全。万一降级失败，可能使用的是上游软件包，会出现软件包的 epoch 和系统其它软件不一致的现象。

## 历史存档

维护 Arch Linux Archive 需要很多资源，所以需要定期删除老软件包。

删除前软件包会被上传到 archive.org 上的 ["Arch Linux Historical Archive"](https://archive.org/details/archlinuxarchive).

此仓库并没有提供某个时间的快照，而是提供了 `archive.archlinux.org` 重定向，老软件包会被重定向到 `archive.org`。除了下载速度慢，用户侧使用上差别不大。

### 从 Historical Archive 查找软件包

**Arch Linux Historical Archive** 包含所有软件包的索引: [https://archive.org/details/archlinuxarchive](https://archive.org/details/archlinuxarchive)

可以通过 **identifier** 获取软件包，格式是 `archlinux_pkg_*sanitized_package_name*`.

要得到一个**规范化**的软件包名，将软件包名称中的 `@`, `+` 和 `.` 都替换为下划线 `_`.

例如 [lucene++](https://archlinux.org/packages/?name=lucene%2B%2B)包 的软件包名称是 `archlinux_pkg_lucene__`。

通过如下页面查找软件包： [https://archive.org/details/archlinux_pkg_lucene__](https://archive.org/details/archlinux_pkg_lucene__).

通过 [archive.org Python 客户端](https://github.com/jjjake/internetarchive)查找:

```
$ ia search subject:"archlinux package" subject:'mysql'
```

```
{"identifier": "archlinux_pkg_ejabberd-mod_mysql"}
{"identifier": "archlinux_pkg_ejabberd-mod_mysql-svn"}
{"identifier": "archlinux_pkg_gambas3-gb-db-mysql"}
{"identifier": "archlinux_pkg_gambas3-gb-mysql"}
{"identifier": "archlinux_pkg_libgda-mysql"}
```

### 从 Historical Archive 下载软件包

所有软件包的版本可以通过如下下载页面获取: [https://archive.org/download/archlinux_pkg_lucene__](https://archive.org/download/archlinux_pkg_lucene__).

要通过 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 下载安装:

```
# pacman -U https://archive.org/download/archlinux_pkg_cjdns/cjdns-16.1-3-x86_64.pkg.tar.xz
```

pacman 的 `RemoteFileSigLevel` 选项控制校验方式，注意依赖关系需要自己解决。

通过 \[[https://gi](https://gi)

- Moved to [archive.archlinux.org](https://archive.archlinux.org) on 2015-12-19.

- Old packages from 2013-2016 uploaded to [archive.org](https://archive.org/details/archlinuxarchive) on 2018-06-05.thub.com/jjjake/internetarchive archive.org Python 客户端\]也能进行下载。

下载某个指定版本：

```
$ ia download archlinux_pkg_cjdns cjdns-16.1-3-x86_64.pkg.tar.xz{,.sig}
```

下载某个软件包的所有 x86_64 版本：

```
$ ia download archlinux_pkg_cjdns --glob="*x86_64.pkg.tar.xz*"
```

## 历史

- 最早的 ARM （*Archlinux 回滚机*） 已于 2013-08-18 关闭。
- [seblu.net 新站点](https://seblu.net)已于 2013-08-31 上线。
- 2015-10-13 旧站关闭，同时启用新 URL 并导入一个新软件 [agetpkg-git](https://aur.archlinux.org/packages/agetpkg-git/)AUR 。
- 2015-12-19 迁移至 [archive.archlinux.org](https://archive.archlinux.org)。
- 从 2018-06-05 开始 2013-2016 的老软件包上传到了 [archive.org](https://archive.org/details/archlinuxarchive) on .

## 2.2　Arch Linux Archive

*难度：进阶 ｜ 篇幅：10.8 KB ｜ 所属：Arch 项目*

将要讲到：位置、目录、常见问题、## Arch Linux repository mirrorlist、Generated on 2042-01-01、Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch

Arch Linux 存档库（**A**rch **L**inux **Archive**，*简称 ala*），以前称为 *Arch Linux 回滚机（Arch Linux Rollback Machine*，*简称 ARM*），保存了*官方仓库快照*、*iso 镜像*和*引导程序包*的历史版本。

**用途**

- 将某个包降级到某个早期版本（最新版本不能用，我需要之前的版本）
- 将所有包恢复到某个指定的历史时刻（所有包都不能用，我要恢复到两个月之前的状态）
- 查找某个历史版本的 ISO 镜像

软件包仅会保存几年时间，之后会被移动到 [archive.org](https://en.wikipedia.org/wiki/archive.org) 的 Arch Linux Historical Archive.

## 位置

Arch Linux 存档库目前位于 [https://archive.archlinux.org/](https://archive.archlinux.org/) 和[全球镜像](https://gitlab.archlinux.org/archlinux/infrastructure/-/blob/master/docs/servers.md#archive-mirrors).

[这里](https://github.com/seblu/archivetools)的源代码可以帮助您架设自己的存档库服务器。

## 目录

**存档库**分为下列三个主目录：

```
├── iso
├── packages
└── repos
```

### /repos

[repos](https://archive.archlinux.org/repos) 这个目录包含官方仓库镜像的每日快照，按下例结构组织：

```
repos
├── 2013
│   ├── 08
│   │   └── 31
│   │       ├── community
│   │       ├── community-staging
│   │       ├── community-testing
│   │       ├── core
│   │       ├── extra
│   │       ├── gnome-unstable
│   │       ├── kde-unstable
│   │       ├── lastsync
│   │       ├── multilib
│   │       ├── multilib-staging
│   │       ├── multilib-testing
│   │       ├── pool
│   │       ├── staging
│   │       └── testing
│   ├── 09
│   │   ├── 01
│   │   ├── 02
│   │   ├── ...
│   │   ├── 21
│   │   └── 22
│   ├── 10
│   │   ├── 01
│   │   ├── 02
│   │   ├── ...
│   │
│   ├── 11
│   └── 12
├── 2014
│   ├── 01
│   │   ├── 01
│   │   ├── 02
│   │   ├── ...
│   │
│   ├── 02
│   ├── 03
│   ├── ...
│   └── 09
│       ├── 01
│       ├── ...
│       └── 28
├── last
├── month
└── week
```

注意: 最下面的三个特定目录（**last**、**week** 和 **month**）分别链接到**已同步的最新仓库版本**、**本周星期一版本**和**本月一日版本**。

### /packages

[packages](https://archive.archlinux.org/packages) 这个目录包含每个包的所有版本及其相应的数字签名。每个包一个目录，按首字母排序。

```
├── packages
│   ├── a
│   │   ├── awesome
│   │   │   ├── awesome-3.5.0-1-i686.pkg.tar.xz
│   │   │   ├── awesome-3.5.0-1-i686.pkg.tar.xz.sig
│   │   │   ├── awesome-3.5.0-1-x86_64.pkg.tar.xz
│   │   │   ├── awesome-3.5.0-1-x86_64.pkg.tar.xz.sig
│   │   │   ├── awesome-3.5.1-1-i686.pkg.tar.xz
│   │   │   ├── awesome-3.5.1-1-i686.pkg.tar.xz.sig
│   │   │   ├── ...
│   │   │
│   │   ├── ...
│   │   ├── awstats
│   │   └── axel
│   │
│   ├── b
│   ├── ...
│   └── z
```

你可以使用“魔法目录”[.all](https://archive.archlinux.org/packages/.all) 按包名访问所有包。这是一个没有子目录的结构。

```
├── packages
│   ├── .all
│   │   ├── awesome-3.5.1-1-i686.pkg.tar.xz
│   │   ├── ...
│   │   ├── zsh-5.0.2-3-i686.pkg.tar.xz
│   │   ├── zsh-5.0.2-4-i686.pkg.tar.xz
│   │   └── ...
```

可以下载一个压缩的索引文件，包含完整的软件包列表 [index.0.xz](https://archive.archlinux.org/packages/.all/index.0.xz).

```
$ curl https://archive.archlinux.org/packages/.all/index.0.xz | unxz
```

```
0ad-a14-1-i686
0ad-a14-1-x86_64
0ad-a14-2-i686
...
zziplib-0.13.62-1-x86_64
zziplib-0.13.62-2-i686
zziplib-0.13.62-2-x86_64
```

### /iso

[iso](https://archive.archlinux.org/iso) 目录按发布日期，保存官方 ISO 镜像和启动压缩包。

```
├── 2014.09.03
├── 2014.10.01
├── 2014.11.01
├── 2014.12.01
├── 2015.07.01
├── 2015.08.01
├── 2015.09.01
└── 2015.10.01
    ├── arch
    ├── archlinux-2015.10.01-dual.iso
    ├── archlinux-2015.10.01-dual.iso.sig
    ├── archlinux-2015.10.01-dual.iso.torrent
    ├── archlinux-bootstrap-2015.10.01-i686.tar.gz
    ├── archlinux-bootstrap-2015.10.01-i686.tar.gz.sig
    ├── archlinux-bootstrap-2015.10.01-x86_64.tar.gz
    ├── archlinux-bootstrap-2015.10.01-x86_64.tar.gz.sig
    ├── md5sums.txt
    └── sha1sums.txt
```

> **提示：**从 2022.10.01 开始 Arch Linux Archive 加入了 BT 种子(非磁力链). 所有种子文件可以从[发布页面](https://archlinux.org/releng/releases/) 下载。

## 常见问题

### 如何降级某个包

在 [/packages](#/packages) 中找到需要的软件包，用 pacman 获取并安装:

```
# pacman -U https://archive.archlinux.org/packages/path/packagename.pkg.tar.xz
```

pacman 会自动下载 *.sig* 文件并根据 `/etc/pacman.conf` 设置进行校验。

或者手动下载并通过 `pacman -U` 安装.

[软件包降级#自动化](../../01_安装与包管理/包管理/降级软件包.md#自动化)包含了可以简化这个过程的工具。

### 如何恢复所有包到指定日期

如果想恢复所有包到指定日期（比如2014年3月30日），你必须如下例所示编辑 `/etc/pacman.conf`，从而让 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 保持在这个时间点并且直接使用指定的服务器：

```
/etc/pacman.conf
```

```
[core]
SigLevel = PackageRequired
Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch

[extra]
SigLevel = PackageRequired
Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch

[community]
SigLevel = PackageRequired
Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch
```

或者如下例编辑 `/etc/pacman.d/mirrorlist`：

```
/etc/pacman.d/mirrorlist
```

```
##
## Arch Linux repository mirrorlist
## Generated on 2042-01-01
##
Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch
```

然后同步包数据库以强制降级：

```
# pacman -Syyuu
```

如果报 PGP 签名错误，软件包损坏（corrupted/invalid packages），请先尝试更新 [archlinux-keyring](https://archlinux.org/packages/?name=archlinux-keyring)包 和 [ca-certificates](https://archlinux.org/packages/?name=ca-certificates)包。或者，您可以决定临时完全[禁用签名检查](../../01_安装与包管理/包管理器/Pacman_软件包签名.md#Disabling_signature_checking)。

> **注意：**混用归档和更新镜像很不安全。万一降级失败，可能使用的是上游软件包，会出现软件包的 epoch 和系统其它软件不一致的现象。

## 历史存档

维护 Arch Linux Archive 需要很多资源，所以需要定期删除老软件包。

删除前软件包会被上传到 archive.org 上的 ["Arch Linux Historical Archive"](https://archive.org/details/archlinuxarchive).

此仓库并没有提供某个时间的快照，而是提供了 `archive.archlinux.org` 重定向，老软件包会被重定向到 `archive.org`。除了下载速度慢，用户侧使用上差别不大。

### 从 Historical Archive 查找软件包

**Arch Linux Historical Archive** 包含所有软件包的索引: [https://archive.org/details/archlinuxarchive](https://archive.org/details/archlinuxarchive)

可以通过 **identifier** 获取软件包，格式是 `archlinux_pkg_*sanitized_package_name*`.

要得到一个**规范化**的软件包名，将软件包名称中的 `@`, `+` 和 `.` 都替换为下划线 `_`.

例如 [lucene++](https://archlinux.org/packages/?name=lucene%2B%2B)包 的软件包名称是 `archlinux_pkg_lucene__`。

通过如下页面查找软件包： [https://archive.org/details/archlinux_pkg_lucene__](https://archive.org/details/archlinux_pkg_lucene__).

通过 [archive.org Python 客户端](https://github.com/jjjake/internetarchive)查找:

```
$ ia search subject:"archlinux package" subject:'mysql'
```

```
{"identifier": "archlinux_pkg_ejabberd-mod_mysql"}
{"identifier": "archlinux_pkg_ejabberd-mod_mysql-svn"}
{"identifier": "archlinux_pkg_gambas3-gb-db-mysql"}
{"identifier": "archlinux_pkg_gambas3-gb-mysql"}
{"identifier": "archlinux_pkg_libgda-mysql"}
```

### 从 Historical Archive 下载软件包

所有软件包的版本可以通过如下下载页面获取: [https://archive.org/download/archlinux_pkg_lucene__](https://archive.org/download/archlinux_pkg_lucene__).

要通过 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 下载安装:

```
# pacman -U https://archive.org/download/archlinux_pkg_cjdns/cjdns-16.1-3-x86_64.pkg.tar.xz
```

pacman 的 `RemoteFileSigLevel` 选项控制校验方式，注意依赖关系需要自己解决。

通过 [archive.org Python 客户端](https://github.com/jjjake/internetarchive)也能进行下载。

下载某个指定版本：

```
$ ia download archlinux_pkg_cjdns cjdns-16.1-3-x86_64.pkg.tar.xz{,.sig}
```

下载某个软件包的所有 x86_64 版本：

```
$ ia download archlinux_pkg_cjdns --glob="*x86_64.pkg.tar.xz*"
```

## 历史

- 最早的 ARM （*Archlinux 回滚机*） 已于 2013-08-18 关闭。
- [seblu.net 新站点](https://seblu.net)已于 2013-08-31 上线。
- 2015-10-13 旧站关闭，同时启用新 URL 并导入一个新软件 [agetpkg-git](https://aur.archlinux.org/packages/agetpkg-git/)AUR 。
- 2015-12-19 迁移至 [archive.archlinux.org](https://archive.archlinux.org)。
- 从 2018-06-05 开始 2013-2016 的老软件包上传到了 [archive.org](https://archive.org/details/archlinuxarchive)。

## 2.3　官方仓库 web 接口

*难度：入门 ｜ 篇幅：3.6 KB ｜ 所属：Arch 项目*

将要讲到：软件包信息、软件包搜索、参见

本文提供了官方仓库网页界面的文档，通过该界面可以查询官方仓库并获取 JSON 格式的结果。

## 软件包信息

基础链接: `https://www.archlinux.org/packages/`

### 软件包细节

链接格式: `/*仓库*/*架构*/*软件包*/json`

例子: [https://archlinux.org/packages/core/x86_64/coreutils/json/](https://archlinux.org/packages/core/x86_64/coreutils/json/)

### 文件列表

链接格式: `/*仓库*/*架构*/*软件包*/files/json`

例子: [https://archlinux.org/packages/core/x86_64/coreutils/files/json/](https://archlinux.org/packages/core/x86_64/coreutils/files/json/)

## 软件包搜索

该界面支持除了`sort`以外的和[HTML Search Form](https://archlinux.org/packages/)一样的查询格式。

基础链接: `https://www.archlinux.org/packages/search/json`

### 通过名称或描述搜索

参数: `q`

例子: [https://archlinux.org/packages/search/json/?q=pacman](https://archlinux.org/packages/search/json/?q=pacman)

### 通过准确的名称搜索

参数: `name`

例子: [https://archlinux.org/packages/search/json/?name=coreutils](https://archlinux.org/packages/search/json/?name=coreutils)

### 通过描述搜索

参数: `desc`

例子: [https://archlinux.org/packages/search/json/?desc=pacman](https://archlinux.org/packages/search/json/?desc=pacman)

### 通过仓库筛选

可以多次使用这个参数以在多个仓库中搜索(但是请注意,如果完全省略它会在所有仓库中搜索)。

参数: `repo`

可用值: `Core`, `Core-Testing`, `Extra`, `Extra-Testing`, `Multilib`, `Multilib-Testing`

例子: [https://archlinux.org/packages/search/json/?q=cursor&repo=Core&repo=Extra](https://archlinux.org/packages/search/json/?q=cursor&repo=Core&repo=Extra)

### 通过架构筛选

可以多次使用这个参数以在多个架构的软件包中搜索(但是请注意,如果完全省略它会在所有架构的软件包中搜索)。

参数: `arch`

可用值: `any`, `x86_64`

例子: [https://archlinux.org/packages/search/json/?q=cursor&arch=any&arch=x86_64](https://archlinux.org/packages/search/json/?q=cursor&arch=any&arch=x86_64)

### 通过维护者搜索

参数: `maintainer`

例子: [https://archlinux.org/packages/search/json/?repo=Extra&maintainer=orphan](https://archlinux.org/packages/search/json/?repo=Extra&maintainer=orphan)

### 通过打包者搜索

参数: `packager`

### 通过标记状态搜索

参数: `flagged`

可用值: `Flagged`, `Not+Flagged`

例子: [https://archlinux.org/packages/search/json/?arch=x86_64&flagged=Flagged](https://archlinux.org/packages/search/json/?arch=x86_64&flagged=Flagged)

## 参见

- [Forum thread](https://bbs.archlinux.org/viewtopic.php?id=170892)
- Initial feature request: [FS#13026](https://bugs.archlinux.org/task/13026)
- [Kittypack: A silly little tool to poke archlinux.org/packages for info](https://github.com/MrElendig/kittypack)

## 2.4　Namcap

*难度：入门 ｜ 篇幅：4.7 KB ｜ 所属：Arch 项目*

将要讲到：安装、用法、创建模块

[namcap](https://gitlab.archlinux.org/pacman/namcap) 是用于检查二进制软件包和源代码 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 中常见的打包错误的一个 [Arch Linux](../关于%20Arch/Arch%20Linux.md) [工具](https://wiki.archlinuxcn.org/wzh/Category:Arch_%E9%A1%B9%E7%9B%AE)。

## 安装

安装 [namcap](https://archlinux.org/packages/?name=namcap)包。

## 用法

对某一文件（可为 `PKGBUILD` 或包二进制文件如 `*包名*.tar.zst`）运行 namcap：

```
$ namcap 文件路径
```

使用 `--info`（`-i`）标志运行 namcap 以输出额外信息：

```
$ namcap --info 文件路径
```

namcap 使用一种“标签”系统来分类输出。标签分为三种类型：“错误”（error，表示为 `E`）、“警告”（warning，表示为 `W`）和“信息”（informational，表示为 `I`）。错误十分重要，必须立即修复，其通常有关安全性不充分、缺少许可证或权限问题。

通常，namcap 会打印出一个人类可读的说明（可能包含修复问题的建议）。要使输出更容易由程序解析，向 namcap 传递 `--machine-readable`（`-m`）标志。

“标签文件” `/usr/share/namcap/namcap-tags`（[在线源](https://gitlab.archlinux.org/pacman/namcap/-/blob/master/namcap-tags)）指定了与代码中使用的带连字符标签相对应的人类可读描述（以 `#` 开头的行被视为注释）：

```
# 注释
machine-parsable-tag %s :: 标签 %s 的人类可读描述
```

> **注意：**
>
> - `::`（双冒号）用于分隔机器可读的标签和人类可读的描述。
> - `%s` 是一个格式说明符（format specifier），详见[#创建模块](#创建模块)。

更多信息请参阅 [namcap(1)](https://man.archlinux.org/man/namcap.1)、[README](https://gitlab.archlinux.org/pacman/namcap#namcap) 和 [NEWS](https://gitlab.archlinux.org/pacman/namcap/blob/master/NEWS)。

## 创建模块

namcap 主程序（`namcap.py`）接受一个包文件或 `PKGBUILD` 作为参数，并创建一个 pkginfo 对象，然后将其传递给在 `__tarball__` 和 `__pkgbuild__` 中定义的一系列规则。自定义的模块需要添加到适当的数组中：

- `__tarball__` 定义处理二进制包的规则，
- `__pkgbuild__` 定义处理 `PKGBUILD` 的规则。

一个示例的 namcap 模块如下所示：

```
namcap/url.py
```

```
import pacman

class package:
    def short_name(self):
        return "url"

    def long_name(self):
        return "Verifies url is included in a PKGBUILD"

    def prereq(self):
        return ""

    def analyze(self, pkginfo, tar):
        ret = [[], [], []]
        if not hasattr(pkginfo, 'url'):
            ret.append(("missing-url", ()))
        return ret

    def type(self):
        return "pkgbuild"
```

每个 namcap 模块必须具有以下方法：

- `short_name(self)`⸺返回一个模块短名称的字符串。通常，这与模块文件的 [basename(1)](https://man.archlinux.org/man/basename.1)（去除扩展名的文件名）相同。

- `long_name(self)`⸺返回一个包含模块简明描述的字符串。此描述会在使用 `namcap --rules=*规则列表*` 列出所有规则时使用。

- `prereq(self)`⸺返回一个包含模块正常运行所需先决条件的字符串。对于处理 `PKGBUILD` 的模块，通常是空字符串（`""`）；对于处理包文件的模块，则是`"tar"`。如果在进一步处理之前需要将包内容提取到临时目录，则应指定`"extract"`。

- `analyze(self, pkginfo, tar)`⸺返回一个包含三个子列表的列表，三个子列表分别对应“错误”、“警告”和“信息”标签。每个标签列表的成员应该是一个元组，包含两个部分：标签的短形式（上文提到的带连字符标签，可带有适当格式说明符（format specifier，如 `%s`））以及格式说明符所对应的参数元组。

- `type(self)`⸺对于处理 `PKGBUILD` 的模块，返回 `"pkgbuild"`；对于处理二进制包文件的模块，返回 `"tarball"`。

## 2.5　网络引导

*难度：进阶 ｜ 篇幅：7.2 KB ｜ 所属：Arch 项目*

将要讲到：BIOS引导、UEFI引导、故障排除

网络引导映像是小型 （< 1 MiB） 映像，可用于在系统启动时即时下载最新的 Arch Linux 版本。无需更新网络引导映像，最新版本将自动可用。网络引导镜像可以从 [Arch Linux Netboot](https://archlinux.org/releng/netboot/) 页面下载。

> **注意：**您需要足够的内存（可能是 1.5GiB 甚至更多）来存储和运行实时系统，否则您可能会在启动时遇到内核崩溃。

## BIOS引导

若要在基于 BIOS 引导的计算机上使用网络引导，需要 `ipxe.lkrn` 或 `ipxe.pxe` 映像。

### 使用 ipxe.lkrn

`ipxe.lkrn` 映像可以像 Linux 内核一样引导。任何 Linux [引导加载程序](../../01_安装与包管理/启动过程/Arch%20的启动流程.md)（如 [GRUB](../../01_安装与包管理/启动加载程序/GRUB.md) 或 [Syslinux](../../01_安装与包管理/启动加载程序/Syslinux.md)）都可用于从硬盘驱动器、CD 或 USB 驱动器加载它。例如，Syslinux wiki 提供了在可引导介质上安装 和配置 Syslinux 的说明。

您可以通过以下步骤制作启动 `ipxe.lkrn`的闪存驱动器：

- 使用 [lsblk](../../02_系统管理与内核/系统管理/设备文件.md) 找出您的设备路径。让我们假设它是 `/dev/sdc` 。
- 在设备上创建MBR分区表。
- 使用 [FAT32](../../02_系统管理与内核/文件系统/FAT.md)文件系统创建主分区并将其标记为活动。
- 挂载分区，在那里创建 `boot/syslinux` 目录并复制 `ipxe.lkrn` 到该 `boot` 目录。

```
# mount /dev/sdc /mnt
# mkdir -p /mnt/boot/syslinux
# cp ipxe.lkrn /mnt/boot
```

- 创建 syslinux 配置

```
/mnt/boot/syslinux/syslinux.cfg
```

```
DEFAULT arch_netboot
   SAY Booting Arch over the network.
LABEL arch_netboot
   KERNEL /boot/ipxe.lkrn
```

- 卸载分区

```
# umount /mnt
```

- 安装 Syslinux MBR 和 Syslinux 本身

```
# dd bs=440 count=1 conv=notrunc if=/usr/lib/syslinux/bios/mbr.bin of=/dev/sdc
# syslinux --directory /boot/syslinux/ --install /dev/sdc1
```

- 现在，您应该能够使用ipxe.lkrn启动USB盘。

或者，您也可以通过运行以下命令来尝试使用 [QEMU](../../02_系统管理与内核/虚拟机管理/QEMU.md) 的映像：

```
$ qemu-system-x86_64 -enable-kvm -m 2G -kernel ipxe.lkrn
```

### 使用 ipxe.pxe

`ipxe.pxe` 映像是 PXE 映像。它可以从现有的 PXE 环境进行链式加载。这允许配置一个 DHCP 服务器，以便从网络引导将始终引导到 Arch Linux 网络引导。 或者，您也可以从现有的 pxe 加载器（如 pxelinux）链式加载它。这是一个菜单条目示例：

```
LABEL arch_netboot_chain
  COM32 pxechn.c32
  APPEND ipxe.a56af4e6a9a9.pxe
```

要使此示例正常工作，您必须将 pxechn.c32 复制到 pxelinux.0 所在的目录中。

## UEFI引导

[ipxe-arch.efi](https://archlinux.org/releng/netboot/) 镜像可用于在 UEFI 模式下启动 Arch Linux 网络引导。仅支持 64 位 UEFI。可以通过 [efibootmgr](../../01_安装与包管理/启动过程/UEFI.md) 将映像添加为引导选项，从引导管理器（如 [systemd-boot](../../01_安装与包管理/启动加载程序/Systemd-boot.md) 或 [rEFInd](../../01_安装与包管理/启动加载程序/REFInd.md)）启动，或直接从 [UEFI shell](../../01_安装与包管理/启动过程/UEFI.md) 启动。您也可以从 UEFI 系统上的独立 U 盘启动它。

### 使用 efibootmgr 安装

首先安装 [efibootmgr](https://archlinux.org/packages/?name=efibootmgr)包 软件包。假设您的 [EFI 系统分区](../../01_安装与包管理/启动过程/EFI%20系统分区.md)（ESP） 位于 `*/dev/sdd1*` 并挂载在 `*esp*` 下，您应该按如下方式移动它 - 让我们也给它一个更友好的名称：

```
# mkdir esp/EFI/arch_netboot
# mv ipxe.*.efi esp/EFI/arch_netboot/arch_netboot.efi
```

然后，您可以按如下方式创建启动项：

```
# efibootmgr --create --disk /dev/sdd --part 1 --loader /EFI/arch_netboot/arch_netboot.efi --label "Arch Linux Netboot" --unicode
```

### 从U盘启动

如果要从 USB 闪存驱动器启动 Netboot EFI 二进制文件，请将其复制到[FAT](../../02_系统管理与内核/文件系统/FAT.md)格式分区上的默认/回退启动路径 (`/EFI/BOOT/BOOTx64.EFI`)。然后，UEFI 系统应自动加载映像。


#### 在GNU/Linux里

例如，假设 `/dev/sd*X*` 作为闪存驱动器，按如下方式准备 USB 闪存驱动器：

1.  如果尚未完成，请在设备上 `/dev/sd*X*` 创建[分区表](../../02_系统管理与内核/系统恢复/分区.md)，并在设备上创建[分区](../../02_系统管理与内核/系统恢复/分区.md) (`/dev/sd*Xn*`)。

    > **提示：**提示：最兼容的设置是将 MBR 分区表与类型 `0c` 为“W95 FAT32 （LBA）”的单个活动（可引导）主分区一起使用

2.  如果尚未完成，请将分区格式化为 [FAT32](../../02_系统管理与内核/文件系统/FAT.md)：

    ```
    # mkfs.fat -F 32 /dev/sdXn
    ```

3.  [挂载](../../02_系统管理与内核/文件系统/文件系统.md)文件系统：

    ```
    # mount /dev/sdXn /mnt
    ```

4.  在挂载的文件系统上创建一个 `/EFI/BOOT` 目录：

    ```
    # mkdir /mnt/EFI /mnt/EFI/BOOT
    ```

5.  复制 `pxe-arch.efi` 到 x64 UEFI 的默认引导路径：

    ```
    # cp pxe-arch.efi /mnt/EFI/BOOT/BOOTx64.EFI
    ```

6.  [卸载](../../01_安装与包管理/包管理器/Pacman.md)文件系统。

> **注意：**您需要[禁用安全启动](../../01_安装与包管理/启动过程/UEFI_安全启动.md#Disabling_Secure_Boot)才能启动。即使使用自定义密钥对 Netboot EFI 二进制文件进行签名，也不会对从镜像下载的内核进行签名。

## 故障排除

### 引导 EFI 二进制文件给出“device error”

如果引导 EFI 二进制文件导致 `Failed to execute ... device error` ，请确保在 UEFI 设置中启用了网络堆栈。它可能需要初始化网络接口控制器 （NIC） 选项 ROM，因此此外还要查找“板载 LAN 引导 ROM”、“启动 PXE OpROM 策略”或类似设置。
