# 第 12 章　KDE

> 本章解决 KDE 桌面在 Arch 上的落地问题：把它装起来、启动 Plasma、配置到顺手，并在出故障时能自己查出来。建议以《KDE》为必读主干，安装、启动、配置与疑难解答都集中在这一节，读完再动手；《KDE Wallet》相对独立，通读一遍了解密码库的安装与配置即可，用到时再回看提示与技巧；《Amarok》是音乐播放器专题，与桌面本身关系不大，不做音频用途可以直接跳过。
> 
> 最容易踩的坑是把 Plasma 当作装完即用的成品，跳过配置直接启动，结果往往只得到空白或反复崩溃的桌面。

<!-- 本章 3 节，共 93 KB -->

## 12.1　Amarok

*难度：入门 ｜ 篇幅：5.1 KB ｜ 所属：KDE*

将要讲到：安装、定制、SHOUTcast、Ampache/MP3 流式传输、收藏集数据库、音频 CD 回放

[Amarok](https://amarok.kde.org/) 是一个有着直观的 [Qt](../../07_开发与工具/控件工具集/Qt.md) 界面且适用于 Linux 的音乐播放器和管理工具,它与 [KDE](KDE.md) 集成得很好。

## 安装

安装 [amarok](https://aur.archlinux.org/packages/amarok/)AUR 软件包。

Amarok 现在依赖于 Phonon ,所以你必须为其选择一个工作后端。请见 [KDE#Phonon](KDE.md#Phonon) 。你也许还需要为选择的后端安装一些[编解码器](../../06_软件与应用/多媒体/编解码器与容器.md)。

## 定制

### 与 GNOME 集成

请见[统一 Qt 和 GTK 应用的外观](../../07_开发与工具/控件工具集/统一%20Qt%20和%20GTK%20应用程序的外观.md)来获得主要 GUI 的外观集成。

### 脚本与系统托盘

新的脚本和系统托盘可以直接在 Amarok (*工具 > 脚本管理 > 获取更多脚本*) 中找到或在 [store.kde.org](https://store.kde.org/browse/cat/) 找到。

### Moodbar

Moodbar 是一个能将基本的进度条转换为取决于音轨旋律被划分成多个色块的进度条的功能。

安装 [moodbar](https://aur.archlinux.org/packages/moodbar/)AUR.

然后前往*设置 > 配置 Amarok* 勾选 "在进度中显示 moodbar"。

因为 Amarok 2 **不会**生成 moodfile ,你可以根据[这篇教程](https://userbase.kde.org/Amarok/Manual/Various/Moodbar#Moodbar_File_Generation_Script)自己创造这些文件。

## SHOUTcast

要想获取 SHOUTcast 使用 "SHOUTcast 服务" 脚本。启动 Amarok,前往*工具 > 脚本管理 > 获取更多脚本*,搜索 *SHOUTcast* 安装 *Shoutcast 服务*,重启 Amarok。然后你可以在 "互联网" 菜单中找到它。

另见: [如何使用 Amarok 流式传输到我自己的广播电台?](https://userbase.kde.org/Amarok/Manual/Various/FAQ/en#How_can_I_use_Amarok_to_stream_to_my_own_radio_station.3F),这篇文章推荐使用 [Internet DJ Console](http://idjc.sourceforge.net/),在AUR([idjc](https://aur.archlinux.org/packages/idjc/)AUR)中可用。

## Ampache/MP3 流式传输

如果你正在直接或使用 Ampache 插件流式传输 MP3,并且你正用着 [GStreamer](../../06_软件与应用/多媒体/GStreamer.md) 后端,你就无法在查看音轨。安装需要的包:[phonon-qt4-gstreamer](https://aur.archlinux.org/packages/phonon-qt4-gstreamer/)AUR [phonon-qt5-gstreamer](https://archlinux.org/packages/?name=phonon-qt5-gstreamer)包 [gst-libav](https://archlinux.org/packages/?name=gst-libav)包。 然后前往 Amarok 的*设置 > 配置 Amarok > 回放 > 配置 Phonon>* 的*后端*标签页。将 Gstreamer 设置为首选后端。

## 收藏集数据库

Amarok 2.x 可以使用 Sqlite (默认) 或 MySQL 储存收藏集数据库。有着大量收藏集的用户和有着更高的性能表现要求的用户可能更想使用 MySQL。

### MySQL

对于基础的 MySQL 配置,请参考 [MariaDB](../../06_软件与应用/数据库管理系统/MariaDB.md) 页面。

当 Amarok 使用 MySQL 时,你需要创建一个可以访问数据库的 MySQL 用户。要想实现,输入下列命令:

```
# mysql -p -u root
# CREATE DATABASE amarokdb;
# USE amarokdb;
# GRANT ALL ON amarokdb.* TO amarokuser@localhost IDENTIFIED BY 'password-user';
# FLUSH PRIVILEGES;
# quit
```

这会创建一个名为 'amarokdb' 的数据库和一个名为 'amarokuser' ,有着密码 'password-user',可以从本地主机访问所述数据库的用户。如果你想从另一台电脑连接到你的数据库电脑,将命令更改为:

```
# GRANT ALL ON amarok.* TO amarokuser@'%' IDENTIFIED BY  'password-user';
```

要想将 Amarok 配置为使用 MySQL,进入 Amarok 配置界面,选择数据库并勾选 "使用外部 MySQL 数据库"。输入服务器(如果是本机数据库通常为 "localhost",否则输入远程主机的名字),用户名(在这个例子中是 "amarokuser" )和你选择的密码用户。不要忘记选择你的音乐收藏集的路径。

## 音频 CD 回放

如果你没有使用 KDE 桌面环境, Amarok 可能缺少需要的工具来播放音频 CD 。安装 [audiocd-kio](https://archlinux.org/packages/?name=audiocd-kio)包 来获取这个功能。

## Firefly/Daap 共享

要想让 Daap 共享在 Amarok 中可见,在 Amarok 设置中启用 "DAAP 收藏集" 插件。

安装 [nss-mdns](https://archlinux.org/packages/?name=nss-mdns)包 并且在 `/etc` 像这样填写主机名:

```
hosts: files mdns4_minimal [NOTFOUND=return] nis dns mdns4
```

启动 `avahi-daemon.service`.

## 另见

[应用列表#音频播放器](../../06_软件与应用/应用程序/应用程序列表.md#Audio_players)

## 12.2　KDE Wallet

*难度：进阶 ｜ 篇幅：14.1 KB ｜ 所属：KDE*

将要讲到：安装、配置、提示与技巧、另见

[KDE Wallet Manager](https://utils.kde.org/projects/kwalletmanager/) 是一个用于管理 [KDE](KDE.md) Plasma 系统上密码的工具。使用 KWallet 子系统，用户既可以保存自己的机密信息，也可以访问所有与 KWallet 集成的应用所保存的密码。

钱包（wallet，在 KDE 的术语中有时也称 vault 或 keyring）是一个由用户自定义密码保护的加密卷，用户和/或软件可以在其中保存机密信息（通常是用户在应用中勾选 *“记住账户”* 后的凭据）。这些加密卷既可以由用户手动创建和使用，也可以由某些集成了钱包子系统的软件（*例如*邮件应用或游戏）在后台自动创建和使用。加密卷通常会在用户登录时通过 PAM 模块自动解密（见下文）。

提示：

- 如果你只是想让使用钱包的应用能有一个可用的钱包，建议使用默认名称（*即* `kdewallet`），并让钱包密码与用户密码相同（方便 PAM 使用）。
- 钱包默认以 `.kwl` 扩展名的加密文件形式存放在 `~/.local/share/kwalletd` 目录中。

> **注意：**自 KDE Frameworks 5.97.0 起，KDE Wallet 支持 org.freedesktop.secrets DBus API，libsecret 现在可以通过 Secret Service API 使用它来存储和获取密码及其他机密信息。

## 安装

KDE Wallet 通常随 [KDE](KDE.md) Plasma 桌面环境一起提供。钱包子系统也可以通过 [kwallet](https://archlinux.org/packages/?name=kwallet)包 软件包手动安装。

可选安装 [kwalletmanager](https://archlinux.org/packages/?name=kwalletmanager)包 软件包以获得钱包管理工具。该工具可用于以图形化方式创建和管理 KDE 钱包。

## 配置

### 在 Plasma 6（KF6）上配置 PAM

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 配置 PAM 似乎并非使 KWallet 正常工作所必需。此外也未提及 Plasma Login Manager（SDDM 已不再是首选的显示管理器）。（在 [英文讨论页](https://wiki.archlinux.org/title/Talk:KDE_Wallet#PAM_configuration_seems_not_to_be_necessary) 中讨论）

Plasma 6 使用 `ksecretd` 作为 `kwallet` 的 secret service API。因此 PAM 必须显式指向该守护进程。

在相关的 PAM 文件中添加以下内容（例如视具体配置选择 `system-login`、`sddm` 或 `login`）：

```
auth            optional        pam_kwallet5.so
session         optional        pam_kwallet5.so auto_start kwalletd=/usr/bin/ksecretd
```

如果不指定 `kwalletd=` 参数，`pam_kwallet5.so` 会回退到默认值，尝试使用 `kwallet5`。

根据你的情况编辑 [PAM](../../02_系统管理与内核/内核/PAM.md) 配置：

- 对于 [SDDM](../显示管理器/SDDM.md)：无需进一步修改，因为 `/etc/pam.d/sddm` 中已经包含这些行。
- 对于 [LightDM](../../06_软件与应用/Canonical/LightDM.md)：无需进一步修改，因为 `/etc/pam.d/lightdm` 和 `/etc/pam.d/lightdm-autologin` 中已经包含这些行。
- 对于 [GDM](../GNOME/GDM.md)：相应地编辑 `/etc/pam.d/gdm-password`。
- 对于 [greetd](../显示管理器/Greetd.md)：相应地编辑 `/etc/pam.d/greetd`。
- 若要在 tty 登录时解锁（没有显示管理器，或使用 [greetd-tuigreet](https://archlinux.org/packages/?name=greetd-tuigreet)包 之类），请相应地编辑 `/etc/pam.d/login`。此时需要指定 **force_run** 参数。

```
/etc/pam.d/login
```

```
auth            optional        pam_kwallet5.so
session         optional        pam_kwallet5.so auto_start force_run kwalletd=/usr/bin/ksecretd
```

```
/etc/pam.d/greetd
```

```
#%PAM-1.0

auth       required     pam_securetty.so
auth       requisite    pam_nologin.so
auth       include      system-local-login
auth       optional     pam_kwallet5.so
account    include      system-local-login
session    include      system-local-login
session    optional     pam_kwallet5.so auto_start force_run kwalletd=/usr/bin/ksecretd
```

#### Plasma 5（KF5）——旧版

较旧的 Plasma 版本使用 `kwalletd5`，不需要指定守护进程。

```
/etc/pam.d/login
```

```
auth     optional  pam_kwallet5.so
session  optional  pam_kwallet5.so auto_start
```

### 登录时自动解锁 KDE Wallet

要随登录自动解锁 KDE Wallet，请安装 [kwallet-pam](https://archlinux.org/packages/?name=kwallet-pam)包 以获得兼容 [PAM](../../02_系统管理与内核/内核/PAM.md) 的模块。所选 KWallet 密码必须与当前[用户](../../08_安全与加密/安全/用户和用户组.md)密码相同。

> **注意：**
>
> - [kwallet-pam](https://archlinux.org/packages/?name=kwallet-pam)包 不兼容 [GnuPG](../../03_网络与服务/邮件/GnuPG.md) 密钥，KDE Wallet 必须使用标准的 `blowfish` 加密方式。
> - 使用自动登录时，只有自动登录方式保存了密码，钱包才能被解锁。例如 [pam_autologin](../../08_安全与加密/身份验证/Pam%20autologin.md) 就会保存。
> - 使用指纹阅读器登录时无法解锁钱包。
> - 钱包必须命名为 `kdewallet`（默认名称）。它不会解锁任何其他钱包。
> - 如果使用 [KDE](KDE.md)，建议在 KDE Wallet 设置中关闭 *Close when last application stops using it*，以防钱包在每次使用后（如解锁 [Wi-Fi](../../03_网络与服务/无线网络/网络配置_无线网络配置.md) 密码等）被关闭。
> - 可能需要先删除默认创建的钱包，这样会一并删除所有已保存的条目。
> - 如果 kwallet Migration Assistant 在每次登录后都要求输入密码，请重命名或删除 `~/.kde4/share/apps/kwallet` 文件夹。

> **提示：**另一种做法是使用 KWalletManager 并把 KWallet 密码设为空，这样解锁钱包时就无需输入密码。只需在 *Change Password..* 中两个字段都留空即可。不过这可能导致他人对用户钱包的非预期（读/写）访问。强烈建议在 *Access Control* 中启用 *Prompt when an application accesses a wallet*，以防止对钱包的非预期访问。

#### 使用 Plasma Login Manager 自动登录与 LUKS 加密

把 `/usr/lib/pam.d/plasmalogin-autologin` 复制为 `/etc/pam.d/plasmalogin-autologin`，然后在 pam_kwallet5.so 之前添加 [pam_systemd_loadkey(8)](https://man.archlinux.org/man/pam_systemd_loadkey.8)：

```
/etc/pam.d/plasmalogin-autologin
```

```
-auth       optional    pam_systemd_loadkey.so
-auth       optional    pam_gnome_keyring.so
-auth       optional    pam_kwallet5.so
```

然后向 `plasmalogin.service` 添加以下内容：

```
/etc/systemd/system/plasmalogin.service.d/keyringmode.conf
```

```
[Service]
KeyringMode=inherit
```

#### 使用 SDDM 自动登录与 LUKS 加密

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[#使用 Plasma Login Manager 自动登录与 LUKS 加密](#使用_Plasma_Login_Manager_自动登录与_LUKS_加密)。**
>
> **附注：** 前提条件和配置相同，具体取决于桌面环境使用哪个登录管理器。（在 [Talk:KDE Wallet](https://wiki.archlinuxcn.org/wiki/Talk:KDE_Wallet) 中讨论）

当系统使用 [dm-crypt](../../08_安全与加密/静态数据加密/Dm-crypt.md) 加密时，可以使用解密磁盘的口令来自动解锁 KDE Wallet。配置 [SDDM#自动登录](../显示管理器/SDDM.md#自动登录) 后，钱包依然可以自动解锁。编辑 `/etc/pam.d/sddm-autologin`，加入 [pam_systemd_loadkey(8)](https://man.archlinux.org/man/pam_systemd_loadkey.8)：

```
/etc/pam.d/sddm-autologin
```

```
-auth       optional    pam_systemd_loadkey.so
-auth       optional    pam_gnome_keyring.so
-auth       optional    pam_kwallet5.so
-session    optional    pam_gnome_keyring.so auto_start
-session    optional    pam_kwallet5.so auto_start kwalletd=/usr/bin/ksecretd
```

然后编辑 `sddm.service`，添加：

```
/etc/systemd/system/sddm.service.d/keyringmode.conf
```

```
[Service]
KeyringMode=inherit
```

## 提示与技巧

### 使用 KDE Wallet 存储 SSH 密钥口令

安装 [ksshaskpass](https://archlinux.org/packages/?name=ksshaskpass)包 软件包。

将 `SSH_ASKPASS` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)设为 `ksshaskpass`，并将 `SSH_ASKPASS_REQUIRE` 设为 `prefer`（优先使用 askpass 程序而不是 TTY）。要在每次登录时自动设置，请创建以下 [environment.d(5)](https://man.archlinux.org/man/environment.d.5) 文件：

```
~/.config/environment.d/ssh_askpass.conf
```

```
SSH_ASKPASS=/usr/bin/ksshaskpass
SSH_ASKPASS_REQUIRE=prefer
```

重启会话（即重新登录）以使环境变量生效。

首次尝试使用某个 SSH 密钥时，会提示输入其口令。请务必勾选 *Remember password* 复选框。下次口令将从 KDE Wallet 读取。

### 使用 KDE Wallet 存储 Git 凭据

[Git](../../07_开发与工具/版本控制系统/Git.md) 可以把凭据处理委托给凭据助手。通过把 [ksshaskpass](https://archlinux.org/packages/?name=ksshaskpass)包 用作凭据助手，HTTP/HTTPS 和 SMTP 密码可以安全地保存在 KDE Wallet 中。

安装 [ksshaskpass](https://archlinux.org/packages/?name=ksshaskpass)包 软件包。

通过设置 `GIT_ASKPASS` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)来配置 Git：

```
~/.config/environment.d/git_askpass.conf
```

```
GIT_ASKPASS=/usr/bin/ksshaskpass
```

> **提示：**如果 `SSH_ASKPASS` 环境变量[已设为 ksshaskpass](#使用_KDE_Wallet_存储_SSH_密钥口令)，则无需再额外设置 `GIT_ASKPASS`。

其他替代方案和更多细节见 [gitcredentials(7)](https://man.archlinux.org/man/gitcredentials.7)。

### 存储 GPG 密钥口令

可以使用原生 KDE 窗口提示输入 GPG 密钥口令，并将其保存到 KDE Wallet 中。

[配置](../../03_网络与服务/邮件/GnuPG.md#pinentry) `gpg-agent` 使用 `/usr/bin/pinentry-qt`。

启用 Secret Service 接口。有两种方式：

- 打开 *系统设置 > KDE Wallet*，启用 *Use KWallet for the Secret Service interface*。
- 编辑 KDE Wallet 配置文件：

```
~/.config/kwalletrc
```

```
[org.freedesktop.secrets]
apiEnabled=true
```

关闭钱包再重新打开，这些更改才会生效。你可以使用 [kwalletmanager](https://archlinux.org/packages/?name=kwalletmanager)包 完成，也可以直接向 Qt D-Bus 发送命令：

```
$ qdbus org.kde.kwalletd6 /modules/kwalletd6 closeAllWallets
$ qdbus org.kde.kwalletd6 /modules/kwalletd6 open kdewallet 0 $0
```

### 在 Chromium 和 VSCode 中使用 KDE Wallet

基于 [Chromium](../../06_软件与应用/Google/Chromium.md) 的浏览器内置了钱包集成。基于 [VSCode](../../07_开发与工具/文本编辑器/Visual%20Studio%20Code.md) 的 IDE 依赖同样的 Chromium oscrypt 模块来检测密钥环。 

要启用该功能，请以 `--password-store=kwallet5`、`--password-store=kwallet6` 或 `--password-store=detect` 参数运行程序。 

要永久保留该改动，见 [Chromium#使 flag 持久化](../../06_软件与应用/Google/Chromium.md#使_flag_持久化)（设置 CHROMIUM_USER_FLAGS 不会生效）。

### 从终端查询密码

与其把密码保存在纯文本文件中，你可以在钱包中手动添加新条目，然后用 *kwallet-query* 取回。

例如，你想用 Podman 登录 Docker Hub 仓库，而它支持通过 `--password-stdin` 标志从标准输入读取密码，那么可以用下面的命令登录：

```
$ kwallet-query -r folder_entry wallet_name -f folder_name | podman login docker.io -u dockerhub_username --password-stdin
```

这样，你的密码既不会保存在任何文本文件中，也不会进入终端历史记录文件。

如果要在图形会话之外运行 `kwallet-query`（例如作为无人值守备份脚本的一部分），请设置 `QT_QPA_PLATFORM=offscreen` 环境变量：

```
$ QT_QPA_PLATFORM=offscreen kwallet-query -r folder_entry wallet_name -f folder_name
```

### 在窗口管理器中自动解锁 KWallet

要解锁由登录密码保护的 KWallet，除了配置 [PAM](../../02_系统管理与内核/内核/PAM.md) 之外，还必须在窗口管理器配置文件的[自启动](../../02_系统管理与内核/系统管理/自动启动.md#窗口管理启动时)部分启动 `/usr/lib/pam_kwallet_init`。

### 禁用 KWallet

如果你希望永久禁用 kwallet：

```
~/.config/kwalletrc
```

```
[Wallet]
Enabled=false
```

> **注意：**可以把 KWallet 用作其他钱包管理器的代理，这样 [plasma-nm](https://archlinux.org/packages/?name=plasma-nm)包 之类的应用就不会出问题。参见[此讨论](https://wiki.archlinux.org/title/Talk:KDE_Wallet#KWalletd6_as_proxy_for_KeePassXC_with_Secret_Service)。

### 自动 D-Bus 激活

大多数应用使用 `org.freedesktop.secrets.service` 这个 [D-Bus](../../02_系统管理与内核/系统管理/D-Bus.md) 服务。KWallet 并未开箱即用地提供对应的服务文件。

你可以创建这样的服务文件来实现自动激活：

```
~/.local/share/dbus-1/services/org.freedesktop.secrets.service
```

```
[D-BUS Service]
Name=org.freedesktop.secrets
Exec=/usr/bin/kwalletd6
```

## 另见

- [Wikipedia:KWallet](https://en.wikipedia.org/wiki/KWallet)
- [dennogumi.org: 使用 PAM 解锁 KWallet](https://web.archive.org/web/20250101021426/https://www.dennogumi.org/2014/04/unlocking-kwallet-with-pam//)
- [org.freedesktop.secrets DBus API 初始支持](https://invent.kde.org/frameworks/kwallet/-/merge_requests/11)

## 12.3　KDE

*难度：高级 ｜ 篇幅：73.3 KB ｜ 所属：KDE*

将要讲到：安装、启动 Plasma、配置、应用程序、提示和技巧、疑难解答

[KDE](https://zh.wikipedia.org/wiki/KDE) 是一套由[桌面环境](../桌面环境/桌面环境.md)([KDE Plasma](https://en.wikipedia.org/wiki/KDE_Plasma))、应用程序（[KDE Applications](https://apps.kde.org/)）以及 [Qt](../../07_开发与工具/控件工具集/Qt.md) 附加库（[KDE Frameworks](https://develop.kde.org/products/frameworks/)）构成的软件项目。

## 安装

### Plasma 桌面

安装 [plasma-meta](https://archlinux.org/packages/?name=plasma-meta)包 元软件包或者 [plasma](https://archlinux.org/groups/x86_64/plasma/)包组 组。关于 [plasma-meta](https://archlinux.org/packages/?name=plasma-meta)包 和 [plasma](https://archlinux.org/groups/x86_64/plasma/)包组 两者的不同见[元软件包与软件包组](../../01_安装与包管理/包管理/元软件包和软件包组.md)。若要使用 Plasma 的最小安装，请安装 [plasma-desktop](https://archlinux.org/packages/?name=plasma-desktop)包 包。上游 KDE 有[软件包和安装推荐](https://community.kde.org/Distributions/Packaging_Recommendations)以获得功能齐全的 Plasma 会话。

如果你是 [NVIDIA](../图形/NVIDIA.md) 用户，请确保已启用 [DRM内核级显示模式设置](../图形/NVIDIA.md#DRM内核级显示模式设置)。

### Plasma Mobile

安装 [plasma-mobile](https://aur.archlinux.org/packages/plasma-mobile/)AUR。

### KDE 应用

若要安装 KDE 的全套应用，请安装 [kde-applications-meta](https://archlinux.org/packages/?name=kde-applications-meta)包 元软件包或 [kde-applications](https://archlinux.org/groups/x86_64/kde-applications/)包组 组。若只需要安装特定类别的 KDE 应用（如游戏或教育），请只安装 [kde-applications-meta](https://archlinux.org/packages/?name=kde-applications-meta)包 中与此相关的依赖。请注意只安装kde应用不会安装任何版本的 Plasma 桌面。

### 不稳定版

见 [Official repositories#kde-unstable](../../00_关于与社区/关于%20Arch/官方仓库.md#kde-unstable)。

## 启动 Plasma

从 Plasma 6.4 开始，Wayland 会话已足够成熟，成为[默认且首选的会话](https://invent.kde.org/plasma/plasma-workspace/-/merge_requests/2188)：X11 会话需要单独安装 [plasma-x11-session](https://archlinux.org/packages/?name=plasma-x11-session)包 软件包才能使用 。Xorg 会话目前仍受支持，但[将在 Plasma 6.8 中被移除](https://blogs.kde.org/2025/11/26/going-all-in-on-a-wayland-future/)。更多信息请参阅 [Wayland 已知重要问题](https://community.kde.org/Plasma/Wayland_Known_Significant_Issues) 和 [X11 已知重要问题](https://community.kde.org/Plasma/X11_Known_Significant_Issues) 文档。

Plasma 既可以通过[显示管理器](../图形用户界面/显示管理器.md)启动，也可以从控制台启动。

### 使用显示管理器

> **提示：**推荐使用 [Plasma Login Manager](../显示管理器/Plasma%20Login%20Manager.md) [显示管理器](../图形用户界面/显示管理器.md)。

- 选择 *Plasma (Wayland)* 以在 [Wayland](../Wayland/Wayland.md) 下开启新会话。
- 选择 *Plasma (X11)* 以在 [Xorg](../X%20服务器/Xorg.md) 下开启新会话。
- 选择 *Plasma mobile (Wayland)* 以在 [Wayland](../Wayland/Wayland.md)下开启新的 Plasma mobile 会话。

### 从控制台启动

- 要从控制台启动 Plasma on Wayland 会话，请运行 [`/usr/lib/plasma-dbus-run-session-if-needed /usr/bin/startplasma-wayland`\[2\]](https://invent.kde.org/plasma/plasma-workspace/-/merge_requests/128)。
- 若要使用 "[xinit/startx](../Xorg%20命令/Xinit.md)" 启动 Plasma 桌面，请在 `.xinitrc` 文件中添加 `export DESKTOP_SESSION=plasma` 和 `exec startplasma-x11`，或者直接在控制台运行`startx /usr/bin/startplasma-x11`。若要在登录时开启 Xorg，见[登录时启动 X](../Xorg%20命令/Xinit.md)。

## 配置

KDE应用的大部分配置存储于 `~/.config` 目录下。KDE 主要使用**"系统设置"**调整配置，也可以在终端执行 `systemsettings` 启动它。

### 个性化

#### Plasma 桌面

##### 主题

有不同类型的 KDE 主题，根据它们修改的范围而有所不同：

- [全局主题](https://store.kde.org/browse?cat=121)，全面的包，可以包括 Plasma 主题、应用程序样式、颜色、字体、图标、光标、启动屏幕、SDDM 主题和 Konsole 配色方案。要应用全局主题，可用 `lookandfeeltool` 命令行工具。
- [Plasma 视觉风格](https://store.kde.org/browse?cat=104)， 修改 Plasma 面板和小部件的外观。这些通常有一个推荐的 Kvantum 或 Aurorae 主题来完成外观。
- [应用程序风格](https://store.kde.org/browse?cat=421)，修改程序的外观。
- 使用[主题引擎](../../07_开发与工具/控件工具集/统一%20Qt%20和%20GTK%20应用程序的外观.md#Theme_engines)的应用程序样式，例如 [Kvantum](../../07_开发与工具/控件工具集/统一%20Qt%20和%20GTK%20应用程序的外观.md#Kvantum)、[QtCurve](../../07_开发与工具/控件工具集/Qt.md#Styles_in_Qt_5) 、[QSvgStyle](https://github.com/DexterMagnific/QSvgStyle)  以及 [Aurorae](https://store.kde.org/p/1167275/)。
- [#图标主题](#图标主题)，为应用程序、文件和操作提供图标。

为了便于系统范围内的安装和更新，一些主题在[官方仓库](https://archlinux.org/packages/?sort=&q=kde+theme&maintainer=&flagged=)和 [AUR](https://aur.archlinux.org/packages?O=0&K=kde+theme) 都可用。

全局主题亦可通过*系统设置 > 颜色和主题 > 全局主题 > 获取新全局主题...* 安装。

> **警告：**终端用户提供的全局主题通常没有经过监管。你应该对下载并应用这些全局主题保持极度警惕。这些全局主题可能运行恶意代码并[造成用户数据丢失](https://discuss.kde.org/t/warning-global-themes-and-widgets-created-by-3rd-party-developers-for-plasma-can-and-will-run-arbitrary-code-you-are-encouraged-to-exercise-extreme-caution-when-using-these-products/12714)

###### GTK 应用的外观

> **提示：**若要使 Qt 和 GTK 应用程序主题一致，请阅读[统一 Qt 和 GTK 应用程序的外观](../../07_开发与工具/控件工具集/统一%20Qt%20和%20GTK%20应用程序的外观.md)。

在 GTK 中推荐使用 [breeze-gtk](https://archlinux.org/packages/?name=breeze-gtk)包 主题，这款 GTK 主题模仿了 Plasma 的 Breeze 主题。 安装 [kde-gtk-config](https://archlinux.org/packages/?name=kde-gtk-config)包（该软件是 [plasma](https://archlinux.org/groups/x86_64/plasma/)包组 的一部分），在*系统设置 > 颜色和主题 > 应用风格 > 配置 GNOME/GTK 应用风格*中选择 `Breeze` 并重新登陆即可。


在某些主题下，GTK应用的提示条在白色背景下会显示难以阅读的白色字体内容。要改变GTK2应用的颜色，找到 `.gtkrc-2.0` 文件中的tooltips部分并更改。对于GTK3应用，需要更改 `gtk.css` 和 `settings.ini` 两个文件。

有些GTK2程序如 [vuescan-bin](https://aur.archlinux.org/packages/vuescan-bin/)AUR 在Breeze或Adwaita皮肤的Plasma会话下因“消失的勾选框”问题仍然很难使用。要解决这个问题，安装并在*系统设置 > 颜色和主题 > 应用风格 > 配置 GNOME/GTK 应用风格 > GTK 主题*中选择如 [numix-frost-themes](https://aur.archlinux.org/packages/numix-frost-themes/)AUR 提供的Numix-Frost-Light皮肤，该皮肤风格与Breeze类似。

##### 头像

Plasma和 [SDDM](../显示管理器/SDDM.md) 都会使用存在 `/var/lib/AccountsService/icons/` 的图像作为用户头像。要使用图形界面配置，你可以使用*系统设置 > 用户*。移除与用户名对应的文件可以恢复默认头像。

##### 小部件

[Plasmoids](https://store.kde.org/browse?cat=705) 是 Plasma 桌面 Shell 的小部件，旨在增强桌面功能，可查看 [AUR](https://aur.archlinux.org/packages?K=plasma6-applet)。

Plasmoid 脚本也可以通过在面板或桌面上点击右键，选择*进入编辑模式 > 添加挂件... > 获取新挂件… >下载新 Plasma 挂件*来安装。这将呈现一个 [https://store.kde.org/](https://store.kde.org/) 的前端，您只需点击就可以安装、卸载或更新第三方 Plasmoid 脚本。

##### 系统托盘中的声音小程序

安装 [plasma-pa](https://archlinux.org/packages/?name=plasma-pa)包 或 [kmix](https://archlinux.org/packages/?name=kmix)包 (从程序启动器启动 Kmix)。前者已自动安装，无需另外设置。

> **注意：**若要调整 [音量单次增减的长度](https://bugs.kde.org/show_bug.cgi?id=313579#c28)，请将例如 `VolumePercentageStep=1` （后面的数字代表长度）的一行文字添加到 `~/.kde4/share/config/kmixrc` 的 `[Global]` 部分中。

##### 系统托盘中的网络管理器

如果您使用 [networkmanager](https://archlinux.org/packages/?name=networkmanager)包，那么请安装 [plasma-nm](https://archlinux.org/packages/?name=plasma-nm)包。

##### 禁用面板阴影

因为 Plasma 的面板在其他窗口之上，所以其阴影也会渲染在其他窗口之上。 若要在不影响其他阴影的情况下禁用此行为，安装 [xorg-xprop](https://archlinux.org/packages/?name=xorg-xprop)包 并运行:

```
$ xprop -remove _KDE_NET_WM_SHADOW
```

然后用增大的光标选择面板。 若要自动化此操作，请安装 [xorg-xwininfo](https://archlinux.org/packages/?name=xorg-xwininfo)包 并创建以下脚本：

```
/usr/local/bin/kde-no-shadow
```

```
#!/bin/bash
for WID in $(xwininfo -root -tree | sed '/"plasmashell": ("plasmashell" "plasmashell")/!d; s/^  *\([^ ]*\) .*/\1/g'); do
   xprop -id $WID -remove _KDE_NET_WM_SHADOW
done
```

并添加此脚本的可执行权限。

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 由于脚本启动过早，自动启动可能无法生效。（`sleep 5` 可能有用，但不可靠）（在 [Talk:KDE](https://wiki.archlinuxcn.org/wiki/Talk:KDE) 中讨论）

在*自动启动*的*添加脚本*中添加此脚本，可以使其在登录时启动：

```
$ kcmshell6 autostart
```

##### 显示缩放/高DPI显示

见 [HiDPI#KDE](../图形/HiDPI.md#KDE)。

#### Plasma Mobile

[plasma-phone-settings](https://invent.kde.org/plasma-mobile/plasma-phone-settings)仓库包含了一些能够用于全局(`/etc/xdg`)和/或用户 (`~/.config`) 的推荐设置。

##### 锁屏

`/etc/xdg/kscreenlockerrc`（或 `~/.config/kscreenlockerrc`）可配置登录后立即锁屏。这在使用 [SDDM#自动登录](../显示管理器/SDDM.md#自动登录)功能时会很有用。

```
/etc/xdg/kscreenlockerrc
```

```

[Daemon]

LockOnStart=true

```

##### 虚拟键盘

如果你的设备具备实体键盘，但你想使用虚拟键盘，在[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)里添加 `KWIN_IM_SHOW_ALWAYS=1` 到你的 Wayland 会话中。

若在 Wayland 会话中使用虚拟键盘，请安装 [maliit-keyboard](https://archlinux.org/packages/?name=maliit-keyboard)包并确保*系统设置 > 键盘 > 虚拟键盘*已经启用

若在 X11 会话中使用虚拟键盘，应在[应用程序列表/工具#屏幕键盘](../../06_软件与应用/应用程序/应用程序列表_工具.md#屏幕键盘)中选择合适的包，再手动运行它。

#### 窗口装饰

[窗口装饰](https://store.kde.org/browse/cat/114/)可以在 [AUR](https://aur.archlinux.org/packages?K=kde+window+decoration) 中找到。

可在*系统设置 > 颜色和主题 > 窗口装饰*中设置窗口装饰，也可以直接下载更多主题并一键安装。

#### 图标主题

可在*系统设置 > 颜色和主题 > 图标*中安装或改变图标主题。

> **注意：**虽然所有现代的Linux发行版都共享统一的图标主题格式，但像 [GNOME](../GNOME/GNOME.md)这样的桌面使用更少的图标（特别是在工具栏和菜单中）。为这些桌面开发的主题一般都缺少 Plasma 和 KDE 应用需要的图标。建议安装与 Plasma 兼容的主题。

> **提示：**部分图标主题不从默认图标主题中引入图标，这将导致部分图标缺失。要想从默认图标主题中导入缺失图标，把 `breeze` 添加到 `/usr/share/icon/*theme-name*/index.theme` 的 `Inherits=` 中，比如 `Inherits=breeze,hicolor`。每次更新图标主题时都需要重新使用这个补丁，尝试使用 Pacman hooks 来自动完成这项任务。

#### 空间效率

Plasma Netbook shell（上网本交互界面）已从 Plasma 5 中移除，请阅[此 KDE 论坛帖子](https://forum.kde.org/viewtopic.php%3Ff=289&t=126631.html#p335947)。但您仍然可以通过编辑 `~/.config/kwinrc`，在 `[Windows]` 部分加上 `BorderlessMaximizedWindows=true` 来实现类似的操作。

#### 缩略图生成

若要在桌面和 Dolphin 内为媒体或文档文件生成缩略图，安装 [kdegraphics-thumbnailers](https://archlinux.org/packages/?name=kdegraphics-thumbnailers)包 和 [ffmpegthumbs](https://archlinux.org/packages/?name=ffmpegthumbs)包。

要启用或配置桌面的缩略图功能，右键桌面背景，选择*配置桌面和壁纸* > *图标* > *配置预览插件*进行设置。

在 Dolphin 中，点击窗口右上角的*打开菜单*按钮（或按下 F10），选择*配置* > *配置 Dolphin(C)...* > *界面* > *预览图*进行设置。

### 夜间颜色

Plasma 提供了一种类似 [Redshift](../X%20服务器/Redshift.md) 的特性（在 [Xorg](../X%20服务器/Xorg.md) 和 [Wayland](../Wayland/Wayland.md) 上都可以使用），称为夜间颜色。它使屏幕上的颜色呈现更暖的色调，以在指定的时间里减少眼睛疲劳。 您可以在*系统设置* > *颜色和主题* > *夜间颜色*中启动该功能。

### 打印

> **提示：**使用 [CUPS](../../04_硬件与外设/打印机/CUPS.md) 的 Web 接口进行快速配置。这种方式配置的打印机可以被 KDE 应用使用。

您也可以在*系统设置* > *打印机*中配置打印机。要使用这种配置方式，必须首先安装 [system-config-printer](https://archlinux.org/packages/?name=system-config-printer)包，[print-manager](https://archlinux.org/packages/?name=print-manager)包 和 [cups](https://archlinux.org/packages/?name=cups)包 软件包。请阅 [CUPS配置](../../04_硬件与外设/打印机/CUPS.md#配置)。

### Samba/Windows 支持

Dophin 的共享服务需要 [kdenetwork-filesharing](https://archlinux.org/packages/?name=kdenetwork-filesharing)包 软件包以及 usershares。关于如何配置 usershares（在不启动 `smb.conf` 的情况下），见[建立 Usershare 路径](../../03_网络与服务/网络共享/Samba.md#Enable_Usershares)。重新启动 Samba 后，应无需进一步配置 Dolphin 的共享。

从 Dolphin 访问 Windows 共享是无需额外设置的。使用 `smb://*服务器名或地址*/*共享目录*`路径访问共享文件。

> **提示：**在 Dolphin 命令行中，无授权认证访问 Windows 共享时，用户名以及密码将使用 `*`（星号）表示。

不像 GTK 文件浏览器利用 GVfs 启动程序，如果在 Dolphin 中通过 KIO 打开 Samba 共享的文件，大多数程序中 Plasma 会先把整个文件复制到本地系统（除了 VLC）。 使用基于 GTK 的文件浏览器可以解决此问题，如 [thunar](https://archlinux.org/packages/?name=thunar)包 和 [gvfs-smb](https://archlinux.org/packages/?name=gvfs-smb)包 可以更有效地访问SMB共享（同时要安装 [gnome-keyring](https://archlinux.org/packages/?name=gnome-keyring)包 以保存登录凭证）。

另一种方法则是使用 [cifs-utils](https://archlinux.org/packages/?name=cifs-utils)包 [挂载](../../02_系统管理与内核/文件系统/文件系统.md#Mount_a_file_system) Samba 共享从而让 Plasma 把 SMB 共享当成一个普通的本地文件夹从而正常访问。见[手动挂载](../../03_网络与服务/网络共享/Samba.md#Manual_mounting)和[自动挂载](../../03_网络与服务/网络共享/Samba.md#Automatic_mounting)。

另一种简单的GUI解决方法则是使用 [samba-mounter-git](https://aur.archlinux.org/packages/samba-mounter-git/)AUR。它在*系统设置* > *网络驱动*中提供了基本相同的功能。要注意，在新版 KDE Plasma 中此应用可能会崩溃。

### KDE 桌面活动

[KDE 桌面活动](https://userbase.kde.org/Plasma/zh-cn#.E6.B4.BB.E5.8A.A8)是一种类似于"虚拟桌面"的 Plasma 组件，您可以独立设置特定的活动。 只有在您正在使用这个活动时，这些设定才会生效。

### 电源管理

安装 [powerdevil](https://archlinux.org/packages/?name=powerdevil)包 以使用 KDE 内置的 "**PowerDevil 电源管理**"的节能服务，它可以调整系统的节能配置、屏幕亮度（如果支持）并提供详细的电源报告。

> **提示：**[电源配置文件](https://pointieststick.com/2021/07/23/this-week-in-kde-power-profiles-and-a-more-polished-kickoff/)的集成需要 power-profiles-daemon 可选依赖。

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** 根据下面的注意内容，问题也可能是因为 logind 中 *LidSwitchIgnoreInhibited* 的默认值是 *yes*，见 （在 [Talk:KDE](https://wiki.archlinuxcn.org/wiki/Talk:KDE) 中讨论）

> **注意：**Powerdevil 可能无法[覆盖](../../02_系统管理与内核/电源管理/电源管理.md#电源管理器)所有的 logind 设置(例如笔记本合盖动作). 如果遇到这样的问题，需要修改logind的设置，请参考 [电源管理#ACPI 事件](../../02_系统管理与内核/电源管理/电源管理.md#ACPI_事件)。

### 自动启动

Plasma 可以在启动和关闭时自动启动应用程序并运行shell脚本。若要自动启动应用程序，请进入*系统设置 > 自动启动*并添加您想要的程序或shell脚本。若选择程序，则会创建一个 `.desktop` 文件；选择脚本，则会创建一个用来启动脚本的 `.desktop` 文件。

> **注意：**
>
> - 程序只能在登录时自动启动，而shell脚本也可以在关机和 Plasma 启动前启动。
> - Shell 脚本需要可执行权限才能运行。
> - 之前位于 `~/.config/autostart-scripts/` 的 Shell 脚本会被[自动迁移到 .desktop 文件](https://invent.kde.org/plasma/plasma-workspace/-/merge_requests/736)。

- 将[桌面配置项](../../01_安装与包管理/配置文件/桌面项.md)（即 *.desktop* 文件）放入适当的 [XDG Autostart](../Freedesktop.org/XDG%20Autostart.md) 目录。
- 在以下目录之一放置 Shell 脚本或其符号链接：
    - `~/.config/plasma-workspace/env`: 在 Plasma 启动前启动脚本。
    - `~/.config/plasma-workspace/shutdown`: 在关机时启动脚本。

见[官方文档](https://docs.kde.org/stable5/en/plasma-workspace/kcontrol/autostart/index.html)。

### Phonon

摘自[维基百科](https://en.wikipedia.org/wiki/Phonon_(software))：

: Phonon 是 KDE 的多媒体 API, 提供了多个多媒体框架的抽象，为 KDE 和一些 QT 程序提供多媒体流处理功能。

: Phonon 最初的目的，是让 KDE 和 Qt 软件独立于其他多媒体框架（例如GStreamer或xine），并为其提供一个稳定的 API。

KDE 广泛地将 Phonon 用于音频（例如系统通知或者 KDE 声音应用）和视频（例如 [Dolphin](../../02_系统管理与内核/文件管理/Dolphin.md) 中的视频缩略图）中。它可以使用以下后端：

- [VLC](../../03_网络与服务/串流/VLC%20媒体播放器.md): [phonon-qt6-vlc](https://archlinux.org/packages/?name=phonon-qt6-vlc)包
- [GStreamer](../../06_软件与应用/多媒体/GStreamer.md): [phonon-qt6-gstreamer-git](https://aur.archlinux.org/packages/phonon-qt6-gstreamer-git/)AUR 参见[GStreamer#安装](../../06_软件与应用/多媒体/GStreamer.md#安装)以获取其他编码支持
- [mpv](../../03_网络与服务/串流/Mpv.md): [phonon-qt6-mpv](https://archlinux.org/packages/?name=phonon-qt6-mpv)包

KDE [仅推荐VLC后端](https://community.kde.org/Distributions/Packaging_Recommendations#Non-Plasma_packages)，因为 GStreamer [不再维护](https://invent.kde.org/libraries/phonon-gstreamer/-/issues/1)。

> **注意：**
>
> - 可以同时安装多个后端，并在 *系统设置* > *多媒体* > *后端* 中进行优先级设定。
> - 根据 [KDE 这个帖子](https://forum.kde.org/viewtopic.php%3Ff=250&t=126476.html#p335080), VLC 后端不支持 [ReplayGain](https://en.wikipedia.org/wiki/ReplayGain)。
> - 如果使用 VLC 后端，可能会遇到崩溃问题，如在每次 Plasma 发送语音警告时崩溃等等，详见 。重建 VLC 插件缓存也许能解决问题：
>
> ```
> # /usr/lib/vlc/vlc-cache-gen /usr/lib/vlc/plugins
> ```

### 备份与恢复

Plasma 将个性化配置以配置文件的形式储存在 [XDG_CONFIG_HOME](../../01_安装与包管理/配置文件/XDG%20基本目录.md#Specification) 中。参照 [KDE 的配置文件](https://github.com/shalva97/kde-configuration-files)并选择[恰当的备份恢复方案](https://www.addictivetips.com/ubuntu-linux-tips/backup-kde-plasma-5-desktop-linux/)。

### systemd 启动

自Plasam5.25起，默认启动方式更改为使用 [systemd 用户服务](../../02_系统管理与内核/系统管理/Systemd_用户.md)实例来启动或管理所有 Plasma 服务。可以[关闭该方法并使用基于脚本的启动](https://invent.kde.org/plasma/plasma-workspace/-/wikis/Plasma-and-the-systemd-boot)，使用下列命令（此方法在未来版本中可能会停用）：

```
$ kwriteconfig6 --file startkderc --group General --key systemdBoot false
```

更多关于该方法的实现细节可以参考 "[Edmundson 的博客：Plasma 和 systemd 启动](https://blog.davidedmundson.co.uk/blog/plasma-and-the-systemd-startup/)"。

### 拼写检查

KDE 使用 [sonnet](https://archlinux.org/packages/?name=sonnet)包 提供拼写检查功能。查看它的可选依赖以获取支持的拼写检查。

可在*系统设置->拼写检查*配置拼写检查。

### 使用 NVIDIA 显卡运行 KWin Wayland

见 [https://community.kde.org/Plasma/Wayland/Nvidia](https://community.kde.org/Plasma/Wayland/Nvidia) 。

## 应用程序

KDE项目提供了一套与Plasma桌面集成的应用程序。有关可用应用程序的完整列表，详见 [kde-applications](https://archlinux.org/groups/x86_64/kde-applications/)包组 软件包。另见 [KDE 相关应用页面](https://wiki.archlinuxcn.org/wzh/Category:KDE)。

除了 KDE 应用程序包提供的程序之外，还有许多其他可用于补充 Plasma 的应用程序。其中一些将在下面讨论。

### 系统管理

#### KDE 系统设置中配置终止 Xorg-server

浏览到子菜单： *系统设置* > *键盘* > *高级（标签）*中选中“杀死 X 服务器的按键序列”复选框。

#### KCM

KCM 意为 KDE 配置模块（**KC**onfig **M**odule）。这些模块在系统设置中提供了界面从而帮助您配置系统，或通过命令行（*kcmshell6*）。

- **sddm-kcm** — [SDDM](../显示管理器/SDDM.md) 的 KDE 配置模块

: [https://invent.kde.org/plasma/sddm-kcm](https://invent.kde.org/plasma/sddm-kcm) || [sddm-kcm](https://archlinux.org/packages/?name=sddm-kcm)包

- **kde-gtk-config** — GTK2 和 GTK3 的 KDE 配置器。

: [https://invent.kde.org/plasma/kde-gtk-config](https://invent.kde.org/plasma/kde-gtk-config) || [kde-gtk-config](https://archlinux.org/packages/?name=kde-gtk-config)包

- **wacom tablet** — KDE Wacom 驱动的图形界面。

: [https://www.linux-apps.com/p/1127862/](https://www.linux-apps.com/p/1127862/) || [kcm-wacomtablet](https://archlinux.org/packages/?name=kcm-wacomtablet)包

可在 [linux-apps.com](https://www.linux-apps.com/find?search=kcm) 找到更多的 KCM 。

### 桌面搜索

KDE 使用 [Baloo](../../03_网络与服务/搜索/Baloo.md) 索引与查找文件。

### Web 浏览器

下列浏览器可以与 Plasma 集成：

- **[Konqueror](https://en.wikipedia.org/wiki/Konqueror)** — KDE 项目的一部分, 支持两种渲染引擎 – KHTML 和基于[Chromium](../../06_软件与应用/Google/Chromium.md)的 Qt Web引擎。

: [https://konqueror.org/](https://konqueror.org/) || [konqueror](https://archlinux.org/packages/?name=konqueror)包

- **[Falkon](https://en.wikipedia.org/wiki/Falkon)** — 一个具有 Plasma 集成特性的 Qt 浏览器，前身是 Qupzilla，使用 Qt WebEngine。

: [https://userbase.kde.org/Falkon/](https://userbase.kde.org/Falkon/) || [falkon](https://archlinux.org/packages/?name=falkon)包

- **[Chromium](../../06_软件与应用/Google/Chromium.md)** — Chromium 及它的专有版本 Google Chrome 具有有限的 Plasma 集成。[它们可以使用 KWallet](KDE%20Wallet.md#KDE_Wallet_for_Chrome_and_Chromium) 以及 KDE 窗口 打开/保存。

: [https://www.chromium.org/](https://www.chromium.org/) || [chromium](https://archlinux.org/packages/?name=chromium)包

- **[Firefox](../../06_软件与应用/Mozilla/Firefox.md)** — Firefox 可以通过配置以和 Plasma 更好地集成。参考 [Firefox KDE整合](../../06_软件与应用/Mozilla/Firefox.md#KDE_整合)。

: [https://mozilla.org/firefox](https://mozilla.org/firefox) || [firefox](https://archlinux.org/packages/?name=firefox)包

> **提示：**从 Plasma 5.13 起，你可以通过 Plasma 与 [Firefox](../../06_软件与应用/Mozilla/Firefox.md) 或 [Chromium](../../06_软件与应用/Google/Chromium.md) 的集成功能，从Plasma托盘控制媒体播放、显示下载状态以及在KRunner中找到打开选项。通过安装[plasma-browser-integration](https://archlinux.org/packages/?name=plasma-browser-integration)包和对应浏览器的插件完成。Chrome/Chromium应该已经随包支持，Firefox插件参见[Firefox KDE整合](../../06_软件与应用/Mozilla/Firefox.md#KDE_整合)。

### 个人信息管理 (PIM)

KDE 提供了自有的[个人信息管理](https://en.wikipedia.org/wiki/Personal_information_management) (PIM) 存储，包括电子邮件，联系人，日历等。可以使用 [kde-pim](https://archlinux.org/groups/x86_64/kde-pim/)包组 安装包组或 [kde-pim-meta](https://archlinux.org/packages/?name=kde-pim-meta)包 元软件包安装所有 PIM 软件包。

#### Akonadi

Akonadi 是系统中本地缓存各种来源的 PIM 数据的一种方法，接着这些数据可以被其它的应用使用。这包含了用户的邮件、联系人、日历、事件、刊物、闹钟、笔记等。Akonadi 自身并不存储任何数据：存储格式依赖于数据的性质（例如，联系人可能以 vcard 格式存储）。

安装 [akonadi](https://archlinux.org/packages/?name=akonadi)包. 若需其他插件，安装 [kdepim-addons](https://archlinux.org/packages/?name=kdepim-addons)包。

> **注意：**
>
> - 若要使用除 MariaDB/MySQL 以外的数据库引擎，请在安装 [akonadi](https://archlinux.org/packages/?name=akonadi)包 包时使用以下命令从而跳过 [mariadb](https://archlinux.org/packages/?name=mariadb)包 依赖项的安装:
>
> ```
> # pacman -S akonadi --assume-installed mariadb
> ```
>
> 另见 [FS#32878](https://bugs.archlinux.org/task/32878)。
>
> - 如果 Akonadi 第一次启动找不到 `/usr/bin/mysqld`，它会尝试使用 SQLite 作为后备。

##### MySQL

默认情况下 Akonadi 将使用`/usr/bin/mysqld`（[MariaDB](../../06_软件与应用/数据库管理系统/MariaDB.md) 默认, 参考 [MySQL](../../06_软件与应用/Oracle/MySQL.md) 来寻找替代方案）去运行一个被管理的 MySQL 实例并且将数据存储在 `~/.local/share/akonadi/db_data/`.

###### 系统级 MySQL 实例

Akonadi 支持将系统范围的 [MySQL](../../06_软件与应用/Oracle/MySQL.md) 用于其数据库。

```
~/.config/akonadi/akonadiserverrc
```

```
[%General]
Driver=QMYSQL

[QMYSQL]
Host=
Name=akonadi_username
Options="UNIX_SOCKET=/run/mysqld/mysqld.sock"
StartServer=false
```

##### PostgreSQL

Akonadi 支持使用现有的系统范围 [PostgreSQL](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md) 实例，即 `postgresql.service`，或运行具有用户权限的 PostgreSQL 实例和 `~/.local/share/ 中的数据库akonadi/db_data/`。

###### 每用户 PostgreSQL 实例

安装 [postgresql](https://archlinux.org/packages/?name=postgresql)包 和 [postgresql-old-upgrade](https://archlinux.org/packages/?name=postgresql-old-upgrade)包。

编辑 Akonadi 配置文件，使其具有以下内容：

```
~/.config/akonadi/akonadiserverrc
```

```
[%General]
Driver=QPSQL
```

> **注意：**
>
> - 当 Akonadi 启动时，它将创建 `[QPSQL]` 部分并在其中设置适当的变量。
> - 数据库将被存储在 `~/.local/share/akonadi/db_data/`。

使用 `akonadictl start` 启动 Akonadi，然后使用 `akonadictl status` 检查其状态。

> **注意：**
>
> - 从 [akonadi](https://archlinux.org/packages/?name=akonadi)包 19.08.0-1 开始，当检测到主要的 PostgreSQL 版本升级时，`~/.local/share/akonadi/db_data/` 中的 PostgreSQL 数据库集群将自动升级。
> - 对于以前的 [akonadi](https://archlinux.org/packages/?name=akonadi)包 版本，主要的 PostgreSQL 版本升级将需要手动数据库升级。遵循 [KDE UserBase Wiki 上的更新说明](https://userbase.kde.org/Akonadi/Postgres_update)。确保将 PostgreSQL 二进制文件的路径调整为 [postgresql](https://archlinux.org/packages/?name=postgresql)包 和 [postgresql-old-upgrade](https://archlinux.org/packages/?name=postgresql-old-upgrade)包 使用的路径，请参阅 [PostgreSQL#Upgrading PostgreSQL](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md#Upgrading_PostgreSQL)。

###### 系统范围的 PostgreSQL 实例

这需要一个已经配置并正在运行的 [PostgreSQL](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md)。

为您的用户创建一个 PostgreSQL 用户帐户：

```
[postgres]$ createuser username
```

为 Akonadi 创建一个数据库：

```
[postgres]$ createdb -O username -E UTF8 --locale=C -T template0 akonadi-username
```

对 Akonadi 配置文件做如下编辑：

```
~/.config/akonadi/akonadiserverrc
```

```
[%General]
Driver=QPSQL

[QPSQL]
Host=/run/postgresql
Name=akonadi-username
StartServer=false
```

> **注意：**可以使用 `[QPSQL]` 部分中的选项 `Port=`、`User=`、`Password=` 指定自定义端口、用户名和密码。

使用 `akonadictl start` 启动 Akonadi，并检查其状态：`akonadictl status`。

##### SQLite

编辑 Akonadi 配置文件以匹配以下配置：

```
~/.config/akonadi/akonadiserverrc
```

```
[%General]
Driver=QSQLITE
```

> **注意：**
>
> - 当 Akonadi 启动时，它将创建 `[QSQLITE]` 部分并在其中设置适当的变量。
> - 数据库存储在 `~/.local/share/akonadi/akonadi.db`。

##### 禁用 Akonadi

想要禁用 Akonadi 的用户不需要启动任何依赖它的 KDE 应用程序。有关详细信息，请参阅此[在 KDE 用户库中的部分](https://userbase.kde.org/Akonadi#Disabling_the_Akonadi_subsystem)。

### KDE Connect

[KDE Connect](https://community.kde.org/KDEConnect) 提供了一些功能以将 [Android](../../04_硬件与外设/Android/Android.md) 或 [iOS](../../04_硬件与外设/IOS/IOS.md) 手机与Linux桌面连接：

- 从任何应用向 KDE 共享文件和 URL 或从 KDE 向任何应用共享，无需连线。
- 触摸板模拟：将手机屏幕用作计算机的触摸板。
- 通知同步（4.3+）：从桌面读取您的安卓通知。
- 共享剪贴板：在手机和电脑之间复制粘贴。
- 多媒体远程控制：将手机用作 Linux 媒体播放器的遥控器。
- Wi-Fi 连接：不需要 usb 和蓝牙。
- RSA加密：保证您的信息安全。

您需要同时在电脑和安卓上安装 KDE Connect。PC端上安装 [kdeconnect](https://archlinux.org/packages/?name=kdeconnect)包 软件包，而安卓端可以在 [Google Play](https://play.google.com/store/apps/details?id=org.kde.kdeconnect_tp) 或 [F-Droid](https://f-droid.org/repository/browse/?fdid=org.kde.kdeconnect_tp) 上安装 KDE Connect。如果你想浏览手机上的文件系统，你需要去安装 [sshfs](https://archlinux.org/packages/?name=sshfs)包 同时在手机app上设置文件系统可被访问。对于 iOS，从 [App Store](https://apps.apple.com/app/kde-connect/id1580245991) 安装 KDE Connect。并非 Android 版本的所有功能都可用于 iOS 版本。

要在Plasma Wayland会话上使用远程输入功能，需要安装 [xdg-desktop-portal](https://archlinux.org/packages/?name=xdg-desktop-portal)包。

即使不使用 Plasma 桌面，您也可以使用 KDE Connect。对于 GNOME 用户，可以通过安装 [gnome-shell-extension-gsconnect](https://aur.archlinux.org/packages/gnome-shell-extension-gsconnect/)AUR 获得更好的集成，而不是安装 [kdeconnect](https://archlinux.org/packages/?name=kdeconnect)包。如果要手动启动 KDE Connect 进程, 运行 `/usr/bin/kdeconnectd`。

如果你使用 firewall, 你需要打开 UDP 和 TCP 端口 `1714` 到 `1764`。

有些时候, KDE Connect 不会检测到你的手机。 你可以通过杀死KDE Connect进程 `killall kdeconnectd` 之后在系统设置里打开 kdeconnect 或者运行 `kdeconnect-cli --refresh` 后运行 `kdeconnect-cli -l`。您亦可在 KDE Connect for Android 上使用*配对新设备 > 以 IP 添加设备*（仅大意）。

## 提示和技巧

### 使用不同的窗口管理器

可以使用其它窗口管理器替换 Plasma 自带的 KWin 窗口管理器。这使你能够将 KDE 桌面的功能与[平铺窗口管理器](../窗口管理器/窗口管理器.md#平铺窗口管理器)的特性结合起来，这样做可能会比KWin的平铺脚本的效果更丰富。

Plasma 中的组件选择设置[不再允许更改窗口管理器](https://github.com/KDE/plasma-desktop/commit/2f83a4434a888cd17b03af1f9925cbb054256ade)，但仍然可以通过其它方法替换 KWin 服务。

> **注意：**当您用不包含混成器的窗口管理器（例如 Openbox）替换 Kwin 时，任何桌面特殊效果都会失效（例如窗口透明度）。在这种情况下，请安装并运行其他独立的混成器，如 [Xcompmgr](../X%20服务器/Xcompmgr.md) 或 [picom](../X%20服务器/Picom.md) 。

#### 替换KWin服务

自 KDE 5.25，Plasma默认使用[基于 systemd 的启动](https://blog.davidedmundson.co.uk/blog/plasma-and-the-systemd-startup/)。

要在此启动方式中替换 KWin，首先为当前用户禁用 (mask) `plasma-kwin_x11.service`服务避免 KWin 启动。

接下来创建 (create) 一个新的 systemd 用户单元 ([user unit](../../02_系统管理与内核/系统管理/Systemd_用户.md)) 来启动你选择的窗口管理器：

```
~/.config/systemd/user/plasma-custom-wm.service
```

```
[Install]
WantedBy=plasma-workspace.target

[Unit]
Description=Plasma Custom Window Manager
Before=plasma-workspace.target

[Service]
ExecStart=/path/to/other/wm
Slice=session.slice
Restart=on-failure
```

最后重新扫描 (daemon-reload) 用户单元 ([user unit](../../02_系统管理与内核/系统管理/Systemd_用户.md))，确保 Kwin 服务 `plasma-kwin_x11.service` 已经禁用 (mask)，然后启用 (enable) 新的`plasma-custom-wm.service`窗口管理器服务。

> **提示：**当使用 i3 窗口管理器时，需要手动设置对话窗以浮动模式打开以确保显示正确。更多信息见[i3#Correct handling of floating dialogs](../动态窗口管理器/I3.md#Correct_handling_of_floating_dialogs)。

#### 使用基于脚本的启动和 KDEWM 环境变量

关闭 [#systemd 启动](#systemd_启动)使 Plasma 以基于脚本的方式启动。如果是这种情况你可以在 Plasma 启动前设置 `KDEWM` 环境变量 ([environment variable](../../02_系统管理与内核/系统管理/环境变量.md)) 来切换窗口管理器。

##### 系统全局

如果有 root 权限，您也可以为所有用户在登录界面添加 XSession 选项。

首先，创建如下脚本并赋予执行权限：

```
/usr/local/bin/plasma-i3.sh
```

```
#!/bin/sh
export KDEWM=/usr/bin/i3
/usr/bin/startplasma-x11
```

若使用别的窗口管理器，则替换示例中的 `/usr/bin/i3`，务必保证路径正确。若窗口管理器无法正常启动，用户将退回登录界面。

然后，在 `/usr/share/xsessions/` 下创建如下文件：

```
/usr/share/xsessions/plasma-i3.desktop
```

```
[Desktop Entry]
Type=XSession
Exec=/usr/local/bin/plasma-i3.sh
DesktopNames=KDE
Name=Plasma (i3)
Comment=KDE Plasma with i3 as the WM
```

#### KDE/Openbox 会话

软件包 [openbox](https://archlinux.org/packages/?name=openbox)包 为在plasma下使用 [Openbox](../堆叠式窗口管理器/Openbox.md) 提供了会话. 要使用这个会话，请禁用 [KDE#systemd启动](#systemd启动)，并在 [display manager](../图形用户界面/显示管理器.md) 菜单中选择 *KDE/Openbox* .

若要手动启动会话，请将下面这行添加到您的 [xinit](../Xorg%20命令/Xinit.md) 配置中：

```
~/.xinitrc
```

```
exec openbox-kde-session
```

### KWin 平铺窗口脚本

下列 KWin 扩展能够让 KDE 变得更像一个[平铺窗口管理器](../窗口管理器/窗口管理器.md#平铺窗口管理器)：

- **Polonium** — 一个 Bismuth 的（非官方）继任者。

: [https://github.com/zeroxoneafour/polonium](https://github.com/zeroxoneafour/polonium) Polonium || [kwin-polonium](https://aur.archlinux.org/packages/kwin-polonium/)AUR

- **Kröhnkite** — 受dwm启发的一个动态平铺扩展组件。

: [https://github.com/anametologin/krohnkite](https://github.com/anametologin/krohnkite) || [kwin-scripts-krohnkite](https://aur.archlinux.org/packages/kwin-scripts-krohnkite/)AUR

- **KZones** — 一个模仿微软 PowerToys 和 Windows 11 的 snap layouts 功能的 KWin 脚本。

: [https://github.com/gerritdevriese/kzones](https://github.com/gerritdevriese/kzones) KZones || [kwin-scripts-kzones](https://aur.archlinux.org/packages/kwin-scripts-kzones/)AUR

### 配置显示器分辨率 / 多显示器

若要在 Plasma 中启用分辨率和多显示器管理, 请安装 [kscreen](https://archlinux.org/packages/?name=kscreen)包. 它在*系统设置 > 显示和监视器*中添加了更多选项.

### 配置 ICC 特性文件

在 X11 上，ICC 特性文件由 [colord](https://archlinux.org/packages/?name=colord)包 管理，要在 Plasma 中配置这些文件，可安装 [colord-kde](https://archlinux.org/packages/?name=colord-kde)包。在新增的*系统设置 > 色彩管理*选项中，点击*导入特性文件*可导入 ICC 特性文件.

### HDR

HDR功能目前是实验性的且仅在Wayland会话中支持。相关设置位于*系统设置 > 显示和监视器 > 高动态范围 (HDR) > 启用 HDR*。

关于更多 HDR 显示内容信息参见[HDR 监视器支持](https://wiki.archlinux.org/title/HDR_monitor_support)。Development details about HDR in Plasma can be found on [Xaver Hugl's blog post](https://zamundaaa.github.io/wayland/2023/12/18/update-on-hdr-and-colormanagement-in-plasma.html).



### 禁用使用 Super 键（Windows 键）打开应用程序启动器

若要禁用此功能，需要编辑`kwinrc`配置文件并设置`ModifierOnlyShortcuts`下的`Meta`为空：

```
$XDG_CONFIG_HOME/kwinrc
```

```
[ModifierOnlyShortcuts]
Meta=
```

也可以运行以下命令：

```
$ kwriteconfig6 --file kwinrc --group ModifierOnlyShortcuts --key Meta ""
```

### 在应用程序菜单中禁用书签

安装了Plasma Browser集成后，KDE将在应用程序启动器中显示书签。

要禁用此功能，进入*系统设置 > 搜索 > Plasma 搜索*，取消勾选*书签*

### IBus 集成

[IBus](../输入法/IBus.md)是一个[输入法框架](../输入法/输入法.md#输入法框架)，可以集成到KDE中。有关详细信息，请参阅 [IBus#Integration](../输入法/IBus.md#Integration)。

在 [Wayland](../Wayland/Wayland.md) 上使用 KDE 以提供重音字符和死键支持时，可能需要使用 [IBus](../输入法/IBus.md) 。

### 在 plasma-nm 中启用热点

参见 [NetworkManager#通过Wi-Fi共享网络连接](../../06_软件与应用/红帽/NetworkManager.md#通过Wi-Fi共享网络连接)。

### 恢复以前保存的会话

如果您选择了*系统设置 > 会话 > 桌面会话 > 会话恢复：上次注销时正在打开的应用程序*（默认），ksmserver（KDE 的会话管理器）将在注销时自动将所有打开的应用程序保存/加载到/从 `~/.config/ksmserverrc` 注销/登录。

> **注意：**目前，无法恢复原生 Wayland 窗口。有关当前开发状态​​，请参阅 [Wayland Showstoppers](https://community.kde.org/Plasma/Wayland_Showstoppers)。

### 在 KMail 中接收本地邮件

如果您使用 [Maildir](https://en.wikipedia.org/wiki/Maildir) 格式的 [mail server](../../03_网络与服务/邮件服务器/邮件服务器.md) 设置了本地邮件传递，您可能希望在 KMail 中接收此邮件。为此，您可以重新使用 KMail 的默认接收帐户“本地文件夹”，该帐户将邮件存储在 `~/.local/share/local-mail/` 中。

将 `~/Maildir` 目录（通常发送 Maildir 格式邮件的地方）符号链接到本地文件夹的收件箱：

```
$ ln -s .local/share/local-mail/inbox ~/Maildir
```

或者，添加一个类型为“Maildir”的新接收帐户并将 `~/Maildir` 设置为其目录。

### 为所有用户配置 Plasma

编辑 `/usr/share/plasma` 中的 `config/main.xml` 文件。例如，要为所有用户配置应用程序启动器，请编辑 `/usr/share/plasma/plasmoids/org.kde.plasma.kickoff/contents/config/main.xml`。要防止文件被包更新覆盖，请将文件添加到 [Pacman's NoUpgrade](../../01_安装与包管理/包管理器/Pacman.md#Skip_file_from_being_upgraded)

### 禁用休眠

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[Power management](../../02_系统管理与内核/电源管理/电源管理.md)。**
>
> **附注：** 此不限于 KDE。可将本节作为一个存根链接，指向该节。（在 [Talk:KDE](https://wiki.archlinuxcn.org/wiki/Talk:KDE) 中讨论）

使用 Polkit 策略规则正确禁用休眠功能并将其从菜单中隐藏。

```
/etc/polkit-1/rules.d/99-disable-hibernate.rules
```

```
// Disable hibernate for all users
polkit.addRule(function(action, subject) {
   if ((action.id == "org.freedesktop.login1.hibernate")) {
      return polkit.Result.NO;
   }
});
polkit.addRule(function(action, subject) {
   if ((action.id == "org.freedesktop.login1.hibernate-multiple-sessions")) {
      return polkit.Result.NO;
   }
});
```

或者，添加以下行到在 `/etc/systemd/sleep.conf.d/` 中的一个文件：

```
/etc/systemd/sleep.conf.d/00-disable-hibernation.conf
```

```
[Sleep]

AllowHibernation=no

AllowSuspendThenHibernate=no

AllowHybridSleep=no
```

### 窗口规则

Kwin 能够为特定的窗口/应用程序指定规则。例如，即使应用程序开发人员没有启用窗口标题栏，您也可以强制启用。 您可以设置特定的位置，大小，最小化状态，保持最前/后以及其他规则。

要创建规则，您可以在特定窗口处于焦点时按 `Alt+F3`。 然后，在"更多操作>配置特殊应用程序设置/窗口设置"中，您可以设置所需的属性。 创建的规则列表可从"系统设置>窗口管理>窗口规则"中获取。

### 在固定位置挂载网络共享

KDE的挂载管理器([kio-fuse](https://archlinux.org/packages/?name=kio-fuse)包)默认会将网络共享挂载到`${XDG_RUNTIME_DIR}/kio-fuse-*长度为6个字符的随机字符串*`。

在你的家目录创建一个目录，例如`mnt_kio`：

```
$ mkdir ~/mnt_kio
```

使用一个[#附加配置片段](#附加配置片段)覆盖默认值：

```
~/.config/systemd/user/kio-fuse.service.d/mountpoint.conf
```

```
[Service]
ExecStart=
ExecStart=/usr/lib/kio-fuse -f %h/mnt_kio
```

修改后如果你通过dbus或在Dolphin里打开一些网络共享的文件：

```
$ dbus-send --session --print-reply --type=method_call \
          --dest=org.kde.KIOFuse \
                 /org/kde/KIOFuse \
                 org.kde.KIOFuse.VFS.mountUrl "smb://etcetc"
```

这些目录会挂载到`~/mnt_kio`。

### 本地集成菜单

如果要将菜单栏集成到标题栏, 请从AUR安装 [material-kwin-decoration-git](https://aur.archlinux.org/packages/material-kwin-decoration-git/)AUR, 然后再 系统设置 > 窗口装饰, 选择 'Material' ， 然后添加应用菜单按钮到标题栏 (最好放到从左数第二个的位置)。

### 在 Wayland 上预授权远程控制

Xdg-desktop-portal-kde 支持来自远程桌面会话、虚拟 KVM 切换器、kde-connect、通过 steam-input 等模拟的设备（如游戏手柄）的远程输入、等等。此授权在应用程序或 desktop-portal 重启后会失效，导致“请求远程控制”窗口每次弹出，使得无人值守访问无法实现。

自 Plasma 6.3 版本起，引入了一套权限系统，允许对应用程序进行预授权。目前，该权限 API 仅可通过 flatpak 命令行工具使用，但应用程序无需以 Flatpak 形式运行即可获得预授权。

根据 [the upstream docs](https://develop.kde.org/docs/administration/portal-permissions/) 和 `flatpak-permission-set` 手册页, 你需要确认要授权的应用程序是否设置了应用程序 ID, 如果通过 [KRunner](../应用程序启动器/KRunner.md) 这种启动器启动, 该 ID 通常由 Plasma 设置， 并且一般是在 `/usr/share/applications` 下的 `.desktop` 文件。

例如，要为类似 lan-mouse 这样的虚拟 KVM 切换器进行预授权，可以执行：

```
$ flatpak permission-set kde-authorized remote-desktop de.feschbar.LanMouse yes
```

如果将其作为 systemd 用户单元中的守护进程启动，则应使用该单元的名称：

```
$ flatpak permission-set kde-authorized remote-desktop lan-mouse yes
```

如果你的应用程序未设置 ID，可以将该字段留空：

```
$ flatpak permission-set kde-authorized remote-desktop "" yes
```

## 疑难解答

### 升级到 KDE 6 后 KDE 应用在 GNOME 中启动失败

KDE 6应用默认使用Wayland，在GNOME Wayland（也可能在其他桌面环境/窗口管理器中） 启动失败。通过设置[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)`QT_QPA_PLATFORM=xcb`来解决该问题。

这是一个KDE bugs的解决方法，问题不是来源于Wayland本身。

### 升级到 KDE 6 后图标消失

升级到KDE 6后可能会出现所有KDE图标不显示的问题，但新创建的用户正常显示。

该问题的原因是升级过程主题丢失，因此需要手动重新指定。在*系统设置 > 颜色和主题 > 图标*中再次选择你要使用的图标即可解决。

### 字体过大或比例异常

尝试在*系统设置 > 文字和字体*中强制字体 DPI 为 `**96**` 。

若无效，请尝试按照 [Xorg#手动设置 DPI](../X%20服务器/Xorg.md#手动设置_DPI) 中的说明直接在 Xorg 配置文件中设置 DPI。

### 有关配置的问题

KDE 中许多问题都跟配置相关。

#### Plasma 桌面行为异常

Plasma 故障通常是由不稳定的 **plasma 小部件**（plasmoids）或者 **plasma 主题**引起的。首先寻找最近安装的 plasmoid 或者 Plasma 主题并禁用或者卸载它。

因此，如果您的桌面突然被“锁定”了，很可能是由于安装了有问题的组件造成的。如果您不记得故障发生前您安装了什么小部件（有时它可能是一个不寻常的问题），请通过逐个移除小部件直到问题不再出现。然后您可以卸载这个小部件并提交一份缺陷报告，**若是官方小部件时**到[KDE 缺陷跟踪页](https://bugs.kde.org/)提交一份缺陷报告。如果它不是，您可以在 [https://store.kde.org/](https://store.kde.org/) 上寻找它的条目并告知小部件的开发者您所碰到的问题（以及再现它的详细步骤等）。

如果您找不到问题，也不想丢失*所有*设置，浏览到`~/.config`：

```
$ for j in plasma*; do mv -- "$j" "${j%}.bak"; done
```

这个命令会将**所有**用户中跟 Plasma 有关的设置重命名为 \*.bak （例如 `plasmarc.bak`），并且当重新登录 Plasma 时，将会恢复**默认**设置。若要撤销该操作，请删除.bak文件扩展名。若已有 \*.bak 文件，请先重命名，移动或删除它们。强烈建议您经常备份。 有关可能的方案列表，见[同步和备份程序（英文）](../../02_系统管理与内核/同步/同步和备份程序.md)。

#### 清理缓存以解决升级故障

[此故障](https://bbs.archlinux.org/viewtopic.php?id=135301)可能由旧的缓存导致。有时，升级后旧缓存可能会产生奇怪的、难以调试的行为，例如关不掉的 shell、改变设置时失去响应、以及像 ark 不能解压 rar/zip 文件又或者 amarok 不能识别音乐等各种其它问题。这个办法也能解决 KDE 和 Qt 程序在升级后变得难看的问题。

用以下命令来重建缓存：

```
$ rm ~/.config/Trolltech.conf
$ kbuildsycoca6 --noincremental
```

或者，清空 `~/.cache/` 文件夹内容，但是，这也会清除其他应用程序的缓存：

```
$ rm -rf ~/.cache/*
```

有些情况下清空 `~/.cache/` 文件夹不能解决问题，如果你遇到了类似下文所示的错误：

```
kf.service.sycoca: The menu spec file ( "" ) contains a Layout or DefaultLayout tag without the mandatory Merge tag inside. Please fix it.
```

这可能和一些过期配置文件有关，这种情况移走`~/.config/menus/`文件夹可能会修复问题。另外尝试逐一移走`~/.config/menus/`内的配置文件来确认哪一个文件导致问题出现也是一个好的解决办法。

#### Plasma 桌面不遵从区域设置/语言设置

Plasma 桌面可能使用与您在 KDE 系统设置面板或 `locale.conf` 中设置的设置不同的设置（根据 [Locale#Variables](../../00_关于与社区/本地化/Locale.md#Variables)）。首先要做的是注销并在删除 `~/.config/plasma-localerc` 后登录，如果这不能解决问题，请尝试手动编辑文件。例如，将 `LANG` 变量设置为 `zh_CN.UTF-8` 并将 `LC_MESSAGES` 变量设置为 `en_US.UTF-8`：

```
~/.config/plasma-localerc
```

```
[Formats]
LANG=zh_CN.UTF-8

[Translations]
LANGUAGE=zh_CN:en_US
```

#### 不能在系统设置中更改主题、图标、字体、颜色；大多数图标不显示

确保 `QT_QPA_PLATFORMTHEME` 在[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)未设置，命令 `printenv QT_QPA_PLATFORMTHEME` 输出应该为空。否则，如果您设置了环境（很可能是 qt5ct 或 qt6ct），该变量将在 Qt 应用程序上强制设置 qt5ct/qt6ct，命令 `export QT_QPA_PLATFORMTHEME=` 应该取消设置环境。

一个更简单（更可靠）的解决方案是完全卸载 qt5ct 和 qt6ct。

#### 音量控制、通知或多媒体键不起作用

隐藏系统托盘设置中的某些项(如音频音量、媒体播放器或通知)也会禁用相关功能。隐藏“音频音量”会禁用音量控制键，“媒体播放器”会禁用多媒体键（倒带、停止、暂停），隐藏“通知”会禁用显示通知。

#### 登录界面不会将光标设置同步到 SDDM

登录界面从 `~/.config/kcminputrc` 读取您的光标设置，如果没有此文件，则不会同步任何设置。生成此文件的最简单方法是在*系统设置 > 颜色和主题 > 光标*中更改光标主题，然后将其更改回首选的光标主题。

#### 缺少面板/小部件

崩溃或硬件更改可以修改屏幕编号，即使在单个显示器设置上也是如此。此类事件后面板/小部件可能会丢失，这可以通过在 `~/.config/plasma-org.kde.plasma.desktop-appletsrc` 文件中修复 `lastScreen` 值解决。

### 图形问题

请先确保您已安装了适合您 GPU 的驱动程序。详见 [Xorg#Driver installation](../X%20服务器/Xorg.md#Driver_installation)。如果您的显卡较旧，您可以尝试 [#禁用桌面特效](#禁用桌面特效)或 [#禁用混成](#禁用混成)。

#### 在混合显卡的系统上强制使用独立显卡

[Hybrid graphics](../图形/混合图形技术.md) 是笔记本电脑常用的一种电源管理策略，它使独立显卡（dGPU）在不需要时保持非活动状态，默认使用集成显卡（iGPU）进行基本的桌面渲染，以节省电池电量。

虽然这种方式有助于节能，但可能导致桌面性能不佳，包括动画帧率低下和潜在的图形异常，即使在配备性能足够的独立显卡的系统上也是如此。

强制 KDE Plasma 使用独立显卡，可以显著改善桌面响应速度和视觉质量。

##### 方法 1: DRI_PRIME (开源驱动程序)

对于使用开源图形驱动程序 (Intel + AMDGPU, Intel + Nouveau) 的系统, 可通过 [globally set](../../02_系统管理与内核/系统管理/环境变量.md#Globally) 设置 `DRI_PRIME` 环境变量来指定使用独立显卡：

```
DRI_PRIME=1
```

索引值（0 或 1）取决于你的系统配置。通过运行以下命令来验证哪个索引对应于你的独立显卡：

```
DRI_PRIME=1 glxinfo
```

> **注意：**此方法不适用于 NVIDIA 专有驱动程序。对于 NVIDIA ，请用 [PRIME render offload](../图形/PRIME.md#PRIME_render_offload) 或下面的 Kwin方法

##### 方法 2: KWIN_DRM_DEVICES (KWin专用)

为了直接控制 KWin 的 GPU 选择，创建一个设置 DRM 设备优先级的启动脚本：

```
~/.config/plasma-workspace/env/gpu.sh
```

```
#!/bin/bash

export KWIN_DRM_DEVICES=/dev/dri/card1:/dev/dri/card0
```

要识别你的 DRM 设备卡及其对应的 GPU：

```
for i in /sys/class/drm/card*/device; do

    echo "Card: $(basename $(dirname $i))"

    if [ -f "$i/vendor" ] && [ -f "$i/device" ]; then

        echo "GPU: $(cat $i/vendor) $(cat $i/device)"

    fi

    echo "---"

done
```

在 `KWIN_DRM_DEVICES` 变量中将独立显卡的设备列在前面，以使其获得渲染优先权。

#### 获取 KWin 的当前状态以用于获得帮助和调试

该命令打印出 KWin 当前状态的摘要，包括使用的选项，使用的合成后端以及相关的 OpenGL 驱动程序功能。详见[Martin's blog](https://blog.martin-graesslin.com/blog/2012/03/on-getting-help-for-kwin-and-helping-kwin/)。

```
$ qdbus6 org.kde.KWin /KWin org.kde.KWin.supportInformation
```

#### 禁用桌面特效

Plasma 默认启用了桌面特效，并且不是所有的游戏都会自动禁用它们。您可以通过*系统设置 > 窗口管理 > 桌面特效*禁用桌面特效。您也可以使用 `Alt+Shift+F12` 切换桌面效果。

另外，您也可以在*系统设置 > 窗口管理 > 窗口规则*下创建自定义 KWin 规则，以在某个应用程序/窗口启动时自动禁用/启用某些项。

#### 启用透明功能

如果使用透明背景而不启用混成器，则会收到以下消息：

```
配色方案使用的透明背景在您的桌面上似乎不受支持
```

在*系统设置 > 显示和监视器 > 混成器*中，选中*启动时开启混成器*并重启 Plasma。

#### 禁用混成

在*系统设置 > 显示和监视器 > 混成器*中，取消选中*启动时开启混成器*并重启 Plasma。

#### 启用混成时全屏状态会闪烁

在*系统设置 > 显示和监视器 > 混成器*中，取消选中*允许应用程序阻止混成*，这可能会影响性能。

#### 桌面网格等特效卡顿

为 KWIN 设置环境变量 `QSG_USE_SIMPLE_ANIMATION_DRIVER` 可以减少部分基于 Quick Scene Graphics 的特效的卡顿现象， 只需为运行 KWIN 的服务创建一个置入式配置片段即可：

```
/etc/systemd/user/plasma-kwin_x11.service.d/10-kwin_QSG_SAD.conf
```

```
[Service]

Environment="QSG_USE_SIMPLE_ANIMATION_DRIVER=1"
```

（对于 Wayland 会话，目录名应使用 plasma-kwin_wayland.service.d）

然后重启会话。

另一种方法是设置 `QSG_NO_VSYNC` 而非 `QSG_USE_SIMPLE_ANIMATION_DRIVER`.

#### Plasma 光标有时显示不正确

创建 `~/.local/share/icons/default/`（或 `~/.icons/default`）目录并在其内创建如下文件：

```
~/.local/share/icons/default/index.theme
```

```
[Icon Theme]
Inherits=breeze_cursors
```

如有需要，将 `breeze_cursors` 替换为自定义的光标主题。（光标主题可在 `/usr/share/icons/` 中找到，如`Breeze_Light`）

> **注意：**你必须重新登录以确保设置生效。

Wayland 下，需要安装[xdg-desktop-portal-gtk](https://archlinux.org/packages/?name=xdg-desktop-portal-gtk)包 以保证 GTK/GNOME 应用的光标主题正确。

##### Firefox 和 Thunderbird 没有使用光标主题

[Wayland](../Wayland/Wayland.md) 下，Firefox 和 Thunderbird 会根据 GSettings 来确定光标如何显示。

要将 KDE 的设置应用到GTK应用，安装 [kde-gtk-config](https://archlinux.org/packages/?name=kde-gtk-config)包。

如果不想安装额外的包，也可以手动设置光标主题：

```
$ gsettings set org.gnome.desktop.interface cursor-theme cursor-theme-name
```

#### （例如将鼠标悬停在超链接上时）光标抖动/闪烁

尝试为系统和窗口管理器安装相应的 2D 加速驱动程序。

#### 屏幕分辨率设置不可用

您的 kscreen 本地配置设置可以覆盖 `xorg.conf` 中设置的配置。在 `~/.local/share/kscreen/` 中查找 kscreen 配置文件，并检查分辨率是否设置为显示器不支持的分辨率。

#### 系统托盘上的图标模糊

为了在托盘中添加图标，应用程序经常使用 appindicator 库。如果图标模糊，请检查已安装的 libappindicator 版本。如果只安装了 [libappindicator-gtk2](https://archlinux.org/packages/?name=libappindicator-gtk2)包，那么您可以安装[libappindicator-gtk3](https://archlinux.org/packages/?name=libappindicator-gtk3)包，以尝试获得清晰的图标。

#### 在虚拟机中无法更改屏幕分辨率

当在 [VMware](../../02_系统管理与内核/虚拟机管理/VMware.md)、[VirtualBox](../../06_软件与应用/Oracle/VirtualBox.md) 或 [QEMU](../../02_系统管理与内核/虚拟机管理/QEMU.md) 虚拟机上运行 Plasma 时，kscreen 可能不允许将 Guest 的屏幕分辨率更改为高于 800x600 的分辨率。

解决方法是在 [xorg.conf.d(5)](https://man.archlinux.org/man/xorg.conf.d.5) 中设置 `PreferredMode` 选项，或者尝试在 VM 中使用其他图形适配器。例如，对于VirtualBox，使用 VBoxSVGA 代替 VMSVGA；对于 QEMU，用 Virtio 代替 QXL。详细信息请参见[KDE Bug 407058](https://bugs.kde.org/show_bug.cgi?id=407058)。

#### Dolphin、Kate 等软件启动时卡住很长时间

检查您的用户文件夹(`Documents` 和 `Downloads` 等)是否设为了只读。

#### Spectacle 使用旧的屏幕状态截图

在*系统设置 > 显示和监视器 > 混成器*，更改*保持窗口缩略图*从*只对显示的窗口*到*从不*。如果你正使用 Intel 显卡， 请确认 [xf86-video-intel](https://archlinux.org/packages/?name=xf86-video-intel)包 [没有安装](../图形/Intel%20图形处理器.md#安装)。

#### GTK 应用里，字体渲染很差

参见 [XDG 桌面门户#GTK应用在KDE Plasma中字体渲染很差](../Freedesktop.org/XDG%20桌面门户.md#GTK应用在KDE_Plasma中字体渲染很差).

#### 窗口大小调整不当

您可能会发现某些应用程序的窗口不能正确调整大小，而是调整后的部分是透明的，鼠标点击会被发送到底层窗口。 要纠正这种行为，请将 KDE 的 GTK3 主题改为 oxygen-gtk 以外的其他主题。

#### 在使用 modesetting 或 nouveau 驱动时，老旧 NVIDIA 显卡发生随机性死机

参阅 [Nouveau#Random lockups with kernel error messages](../图形/Nouveau.md#Random_lockups_with_kernel_error_messages).

### 声音问题

> **注意：**首先保证您已经安装了 [alsa-utils](https://archlinux.org/packages/?name=alsa-utils)包。

#### 挂起后没有声音

如果挂起后没有声音并且 Kmix 没有显示应该显示的音频设备，可以尝试重新启动 plasmeshell 和 pulseaudio：

```
$ killall plasmashell
$ systemctl --user restart pulseaudio.service
$ plasmashell
```

某些应用程序也可能需要重新启动才能再次播放声音。

#### 使用 GStreamer Phonon 后端时不能播放 MP3 文件

安装 GStreamer libav 插件（软件包[gst-libav](https://archlinux.org/packages/?name=gst-libav)包）可以解决问题。如果仍然碰到，您可以尝试换一个软件包，例如[phonon-qt6-vlc](https://archlinux.org/packages/?name=phonon-qt6-vlc)包。

然后确保通过*phononsettings*设置了正确的后端。

#### 系统托盘没有音量控制图标并且无法使用快捷键控制音量

检查是否安装[plasma-pa](https://archlinux.org/packages/?name=plasma-pa)包

#### 短时间后声音消失

如果`journalctl -p4 -t pulseaudio`包含`Failed to create sink input: sink is suspended`的信息，尝试注释`/etc/pulse/default.pa`内的行：

```
#load-module module-suspend-on-idle
```

如果问题仍然存在，[plasma-meta](https://archlinux.org/packages/?name=plasma-meta)包 或 [plasma](https://archlinux.org/groups/x86_64/plasma/)包组可能同时安装了[pulseaudio](https://archlinux.org/packages/?name=pulseaudio)包 和 [wireplumber](https://archlinux.org/packages/?name=wireplumber)包 。要修复这个问题，将[pulseaudio](https://archlinux.org/packages/?name=pulseaudio)包 替换为 [pipewire-pulse](https://archlinux.org/packages/?name=pipewire-pulse)包 。 如果想使用 [pulseaudio](https://archlinux.org/packages/?name=pulseaudio)包 ，将 [wireplumber](https://archlinux.org/packages/?name=wireplumber)包 替换为 [pipewire-media-session](https://archlinux.org/packages/?name=pipewire-media-session)包 。参考[PipeWire#PulseAudio_客户端](../../06_软件与应用/多媒体/PipeWire.md#PulseAudio_客户端)和[这个贴子](https://bbs.archlinux.org/viewtopic.php?id=276308)。

### 电源管理

#### 没有挂起/休眠选项

如果您的系统可以使用 systemd 挂起/休眠，但 KDE 中没有这些选项，请确保 [powerdevil](https://archlinux.org/packages/?name=powerdevil)包 已被安装。

#### 没有电源配置方案选项

确保安装了[powerdevil](https://archlinux.org/packages/?name=powerdevil)包 和 [power-profiles-daemon](https://archlinux.org/packages/?name=power-profiles-daemon)包。 运行*powerprofilesctl*并检查驱动。如果是`intel_pstate` 或 `amd_pstate`，那问题应该已经解决，否则参考[CPU_调频#调频驱动](../../04_硬件与外设/CPU/CPU%20调频.md#调频驱动)以启用相关驱动。

### KMail

#### 清理 akonadi 配置来修复 kmail

详见 。

若要备份配置文件，请复制以下文件夹：

```
$ cp -a ~/.local/share/akonadi ~/.local/share/akonadi-old
$ cp -a ~/.config/akonadi ~/.config/akonadi-old
```

#### KMail 的 IMAP 收件箱是空的

对于某些 IMAP 账户，kmail将把收件箱当作一个包含此帐户所有其他文件夹的容器显示。Kmail 不会在收件箱容器中显示消息，而是在所有其他子文件夹中显示消息，见 。若要解决此问题，只需在kmail帐户设置中禁用服务器端订阅即可。

#### KMail中EWS帐户的授权错误

在KMail中设置EWS帐户时，您可能会不断收到关于授权失败的错误，即使是使用有效的凭证。这可能是由于[KWallet](KDE%20Wallet.md)和KMail之间的通讯中断造成的。要解决此问题，请通过qdbus设置密码：

```
$ qdbus6 org.freedesktop.Akonadi.Resource.akonadi_ews_resource_0 /Settings org.kde.Akonadi.Ews.Wallet.setPassword "XXX"
```

### Aggressive QXcbConnection / kscreen.xcb.helper journal logging

见 [Qt#Disable/Change Qt journal logging behaviour](../../07_开发与工具/控件工具集/Qt.md#Disable/Change_Qt_journal_logging_behaviour)。

### KF5/Qt 5应用程序在i3/FVWM/awesome中不显示图标

见 [Qt#Configuration of Qt 5/6 applications under environments other than KDE Plasma](../../07_开发与工具/控件工具集/Qt.md#Configuration_of_Qt_5/6_applications_under_environments_other_than_KDE_Plasma)。

### 保存凭据和持续显示 KWallet 对话框的问题

不建议在用户设置中关闭 KWallet 密码保存系统，因为需要它为每个用户保存加密凭证（如Wi-Fi密码）。关闭它可能会导致 KWallet 对话框持续出现。

如果您嫌每当应用程序想要访问 Kwallet 时需要解锁烦，您可以让登录管理器 SDDM 和 LightDM 在登录时自动解锁 KWallet，见 [KDE Wallet](KDE%20Wallet.md)。第一个钱包需要由 KWallet 生成（而不是"用户生成"），以便用于系统程序凭据。

如果您不希望让钱包凭据在内存内为每个应用打开，可以通过 [kwalletmanager](https://archlinux.org/packages/?name=kwalletmanager)包 在KWallet设置中限制应用程序访问它。

如果您不关心凭证加密，您可以在创建钱包，KWallet 要求输入密码时，将密码留空。这样，应用程序将可以在不解锁钱包的情况下访问密码。

### Discover不显示任何程序

根据你的Plasma/Qt版本，请安装[packagekit-qt6](https://archlinux.org/packages/?name=packagekit-qt6)包 或 [packagekit-qt5](https://archlinux.org/packages/?name=packagekit-qt5)包 以解决问题。

> **警告：**包管理者在[GitHub comment](https://github.com/archlinux/archinstall/issues/1321#issuecomment-1151343223)里做出了声明：“通过packagekit管理系统软件包，从根本上不兼容我们有着大量维护的滚动式发行版，如果用户在重启前没有关注pacman的输出日志或合并pacnew文件，任何更新都有可能导致系统无法启动或处于一个不稳定的状态。”

### Discover无法从Arch仓库获取更新

Discover有时不能释放PackageKit alpm锁，手动删除`/var/lib/PackageKit/alpm/db.lck`文件后更新Discover可解决问题。

### NVIDIA驱动程序的kscreenlocker_greet的CPU使用率很高

如 [KDE 错误 347772](https://bugs.kde.org/show_bug.cgi?id=347772) 中所述，NVIDIA OpenGL 驱动程序和 QML 可能无法与 Qt 5 很好地配合使用。这可能导致 `kscreenlocker_greet`解锁会话后 CPU 使用率高。要解决此问题，请将 `QSG_RENDERER_LOOP` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)设置为 `basic`。

然后用 `killall kscreenlocker_greet` 杀死之前的 greeter 实例。

### 在 ZFS 上运行 Akonadi 时出现操作系统错误 22

如果您的主目录位于 [ZFS](../../02_系统管理与内核/文件系统/ZFS.md) 池中，请创建一个 `~/.config/akonadi/mysql-local.conf` 文件，其内容如下：

```
[mysqld]
innodb_use_native_aio = 0
```

请参阅 [MariaDB#OS error 22 when running on ZFS](../../06_软件与应用/数据库管理系统/MariaDB.md#OS_error_22_when_running_on_ZFS).

### 某些程序的窗口处于非活动状态时无法滚动

这是由GTK3处理鼠标滚动事件处理有问题造成的。一个解决方法是设置[environment variable](../../02_系统管理与内核/系统管理/环境变量.md) `GDK_CORE_DEVICE_EVENTS=1`。但是，这种方法也会破坏触摸板的平滑滚动和触摸屏滚动。

### TeamViewer 很卡

在使用TeamViewer时，如果您使用平滑动画(比如最小化窗口)，它可能会运行得很慢。参见 [#禁用混成](#禁用混成)作为一种解决方案。

### Kmail、Kontact 和 Wayland

Kmail可能会失去响应，显示一个黑色的消息视图或者类似的，通常是在最小化和恢复之后。一个解决方案可能是设置 [environment variable](../../02_系统管理与内核/系统管理/环境变量.md) `QT_QPA_PLATFORM="xcb;wayland"`。

### 解锁小部件 (Plasma ≥ 5.18)

如果您之前锁定了小部件，您可能会发现自己无法再次解锁它们。 您只需运行此命令即可：

```
$ qdbus6 org.kde.plasmashell /PlasmaShell evaluateScript "lockCorona(false)"
```

新的 `Customize Layout` 不需要将它们锁定，但如果想这样做：

```
$ qdbus6 org.kde.plasmashell /PlasmaShell evaluateScript "lockCorona(true)"
```

### 小部件无法运行或出现bug

可安装`plasmoidviewer`包查看输出信息，做出相应处理：

```
yay -S plasmoidviewer-debug
```

```
plasmoidviewer --applet <小部件名称>
```

### KIO以错误的程序打开URL连接

检查HTML, PHP等的文件关联，将其设置为浏览器。KIO缓存文件位于`$HOME/.cache/kioexec`。另参见[xdg-utils#URL scheme handlers](../Freedesktop.org/Xdg-utils.md#URL_scheme_handlers)。

### 在暂停和休眠之前锁定屏幕

在系统设置应用程序中，KDE 提供了从睡眠中醒来后自动锁定屏幕的设置。恢复后，[some users](https://www.reddit.com/r/kde/comments/obnpeb/how_to_lock_system_before_suspend/)报告说屏幕在锁定前短暂显示。要防止这种行为并让 KDE 在挂起之前锁定屏幕，请在 [systemd(1)](https://man.archlinux.org/man/systemd.1) 中以 root 用户创建以下文件来创建挂钩：

```
/usr/lib/systemd/system-sleep/lock_before_suspend.sh
```

```
#!/bin/bash

case $1/$2 in
    pre/*)
        case $2 in
            suspend|hibernate)
                loginctl lock-session
                sleep 1
                ;;
            esac
        ;;
esac
```

为了在设备挂起之前完成`loginctl lock-session`命令，必须使用`sleep`。较低的值可能不足以完成这个命令。

创建文件后，将其设为可执行的。

最后，通过转到*系统设置 > 锁屏*并选中*从休眠恢复时锁定屏幕*复选框，确保启用了 该 KDE 设置。

### X11快捷键在Wayland上冲突

自KDE 5.27起，一些X11软件例如[freerdp](https://archlinux.org/packages/?name=freerdp)包会捕获键盘输入，其它软件像[VMware](../../02_系统管理与内核/虚拟机管理/VMware.md)无法正确捕获。 

[在Xserver](https://gitlab.freedesktop.org/xorg/xserver/-/issues/1332)或在混成器强制捕获快捷键并不合适。 下面的方法解决问题更优雅一些：

- 右键窗口标题栏（例如VMware或Citrix）；
- *More Actions > Configure Special Window Settings...*
- 单击 *Add Property...*并选择*Ignore global shortcuts*。
- 选择 *force* 和 *yes*，应用设置。

### 改变系统设置后没有生效

这可能是因为系统设置无法访问/修改位于家目录的 .config 文件夹。

要解决该问题，你需要改变文件夹的所有者：

```
# chown user:user /home/user/.config
```

`*user*` 代表你在KDE Plasma登陆的用户。如果你的家目录和你登陆的账户不同，请在相应位置更改。

如果上面的办法无效，你可能还需要更改文件夹的权限：

```
# chmod 755 /home/user/.config
```

### 某些应用的 Plasma 6 全局菜单不能使用

即使安装了[appmenu-gtk-module](https://archlinux.org/packages/?name=appmenu-gtk-module)包和[libdbusmenu-glib](https://archlinux.org/packages/?name=libdbusmenu-glib)包，"全局菜单"小部件在一些应用上仍然不能使用。要解决这个问题，安装[plasma5-integration](https://archlinux.org/packages/?name=plasma5-integration)包并重启你的会话。

## 参见

- [KDE 主页](https://www.kde.org/)
- [KDE news](https://dot.kde.org/)
- [KDE Blogs](https://planet.kde.org/)
- [KDE 论坛](https://discuss.kde.org/)
- [KDE Wikis](https://wiki.kde.org/)
- [KDE 缺陷跟踪页](https://bugs.kde.org/)
- [Martin Graesslin's blog](https://blog.martin-graesslin.com/blog/kategorien/kde/)
- [KDE Matrix 房间](https://community.kde.org/Matrix)
