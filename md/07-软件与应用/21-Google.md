# 第 21 章　Google

> 本章处理两件独立的事：把 Google 身份验证器接入系统认证，以及装出可用的 Chromium。前者从安装讲到设置插入式验证模块（PAM）、生成密钥文件、配置两步验证器并测试，并说明密钥文件放哪；后者覆盖安装、配置、提示和技巧、疑难解答与参考。要做认证必读《Google 身份验证器》，密钥文件与测试不可跳；只装浏览器的直接进《Chromium》，按安装、配置走，有问题再查疑难解答，提示和技巧可跳读。
> 
> 最常踩的坑：密钥文件不记存储位置，丢了就重建不了验证；另一误解是以为两节须成套安装，其实按需选读即可。

<!-- 本章 2 节，共 58 KB -->

## 21.1　Google 身份验证器

*难度：进阶 ｜ 篇幅：10.4 KB ｜ 所属：Google*

将要讲到：安装、设置插入式验证模块(**P**luggable **A**uthentication **M**odules)、生成密钥文件、设置两步验证器、测试、存储位置

[Google Authenticator](https://github.com/google/google-authenticator) 使用一次性密码(**O**ne-**t**ime **P**asscodes)([OTP](https://en.wikipedia.org/wiki/One-time_pad))进行两步验证。iOS、Android 和 Blackberry 上都提供了 OTP 生成器应用。两步验证的机制集成在Linux的 [PAM](../../02_系统管理与内核/内核/PAM.md) 系统中。此指南显示了此两步验证机制的安装与配置。

## 安装

安装 [libpam-google-authenticator](https://archlinux.org/packages/?name=libpam-google-authenticator)包 软件包或开发者版本 [google-authenticator-libpam-git](https://aur.archlinux.org/packages/google-authenticator-libpam-git/)AUR。

## 设置插入式验证模块(**P**luggable **A**uthentication **M**odules)

> **警告：**若通过 [SSH](../../03_网络与服务/SSH/安全外壳协议.md) 进行 Google Authenticator 的所有配置，在完成所有配置并测试正常之前，请勿关闭 SSH 会话，否则可能会无法登录。此外，最好在激活 PAM 之前生成密钥文件。

通常远程登录才需要设置两步验证。对应的PAM的配置在文件`/etc/pam.d/sshd`内。如果想全局使用谷歌两步身份验证，请**小心**的修改`/etc/pam.d/system-auth`，以免锁定自己从而不能登录。在本指南中，我们将在本地会话中编辑 SSH 登录配置文件`/etc/pam.d/sshd`，这样就算操作出错，也不会影响您的登录(但不是必须的)。

要同时输入 Unix 密码**与**两步验证码登录，请在 `/etc/pam.d/sshd`文件的system-remote-login行之上添加`pam_google_authenticator.so`：

```
 auth            required        pam_google_authenticator.so
 auth            include         system-remote-login
 account         include         system-remote-login
 password        include         system-remote-login
 session         include         system-remote-login
```

这样将会首先询问两步验证码，验证成功后才会询问 Unix 密码。交换`pam_google_authenticator.so`与 system-remote-login 两行会改变验证顺序。

> **警告：**只有生成密钥文件(见下)的用户才会被允许SSH登录。

要允许使用 Unix 密码**或**两步验证码登录，请修改:

```
 auth            sufficient      pam_google_authenticator.so
```

在文件`/etc/ssh/**sshd_config**`内开启质疑-应答认证(challenge-response authentication)：

```
 ChallengeResponseAuthentication yes
```

最后[重新加载](../../01_安装与包管理/初始化/Systemd.md#使用单元) `sshd` 服务。

> **警告：**如果设置使用密钥登录并[禁止密码登录](../../03_网络与服务/SSH/OpenSSH.md#强制公钥认证), OpenSSH 会忽略如上所有的配置。但是在 OpenSSH 6.2 版本以后，允许使用基于密钥和两步验证的验证。请参阅 [SSH的双因素验证与公钥](../../03_网络与服务/SSH/OpenSSH.md#双因素验证与公钥)\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Project:%E7%A4%BE%E7%BE%A4%E9%A6%96%E9%A1%B5#损坏的链接)：无效的章节\]。

### 只对在本地网络外的登录启用两步验证

有时,我们只希望对不在本地网络内发起的 SSH 连接启用两步验证。此时，建立一个文件(比如`/etc/security/access-local.conf`)，然后仿照下面的例子配置你想要跳过两步验证的网络地址：

```
# only allow from local IP range
+ : ALL : 192.168.20.0/24
# Additional network: VPN tunnel ip range (in case you have one)
+ : ALL : 10.8.0.0/24
+ : ALL : LOCAL
- : ALL : ALL
```

然后编辑你的`/etc/pam.d/sshd`，添加这一行:

```
#%PAM-1.0
#auth     required  pam_securetty.so     #disable remote root
auth [success=1 default=ignore] pam_access.so accessfile=/etc/security/access-local.conf
auth      required  pam_google_authenticator.so
auth      include   system-remote-login
account   include   system-remote-login
password  include   system-remote-login
session   include   system-remote-login
```

## 生成密钥文件

> **提示：**[安装](https://wiki.archlinuxcn.org/wzh/Help:Reading#安装软件包) [qrencode](https://archlinux.org/packages/?name=qrencode)包 以在屏幕上生成可以扫描的二维码。扫描二维码以自动配置两步验证器。

每一个想要使用两步验证的用户需要在其用户目录生成一个密钥文件，使用命令*google-authenticator*来完成：

```
   $ google-authenticator
   Do you want authentication tokens to be time-based (y/n) y
   <这里是自动生成的二维码>
   Your new secret key is: ZVZG5UZU4D7MY4DH          (验证器配置密钥)
   Your verification code is 269371                  (输入验证器生成的验证码)
   Your emergency scratch codes are:                 (备用令牌码)
     70058954
     97277505
     99684896
     56514332
     82717798

   Do you want me to update your "/home/username/.google_authenticator" file (y/n) y
   (是否重新生成登录配置文件？)

   Do you want to disallow multiple uses of the same authentication
   token? This restricts you to one login about every 30s, but it increases
   your chances to notice or even prevent man-in-the-middle attacks (y/n) y
   (是否拒绝多次重复使用相同的令牌？这将限制你每30s仅能登录一次，但会提醒/阻止中间人攻击。)

   By default, tokens are good for 30 seconds and in order to compensate for
   possible time-skew between the client and the server, we allow an extra
   token before and after the current time. If you experience problems with poor
   time synchronization, you can increase the window from its default
   size of 1:30min to about 4min. Do you want to do so (y/n) n
   (是否将窗口时间由1分30秒增加到约4分钟？这将缓解时间同步问题。)

   If the computer that you are logging into is not hardened against brute-force
   login attempts, you can enable rate-limiting for the authentication module.
   By default, this limits attackers to no more than 3 login attempts every 30s.
   Do you want to enable rate-limiting (y/n) y
   (是否启用此模块的登录频率限制，登录者将会被限制为最多在30秒内登录3次。)
```

建议您将**备用令牌码**保存在安全的地方(打印出来并放在一个安全的位置)，因为当丢失手机(即你的两步验证器)或其他原因不能使用两步验证器时，只能使用**备用令牌码**登录。它们同时也被保存在`~/.google_authenticator`，你可以在登录后随时查阅。

## 设置两步验证器

在你的手机上安装两步验证器软件。例如：

- **FreeOTP** : [Android(Play商店)](https://play.google.com/store/apps/details?id=org.fedorahosted.freeotp)/[iOS](https://itunes.apple.com/es/app/freeotp-authenticator/id872559395).
- **Google Authenticator** [Android(Play商店)](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2)/[iOS](https://itunes.apple.com/es/app/google-authenticator/id388497605).

在软件中创建一个新验证，输入密钥(如例子中的'ZVZG5UZU4D7MY4DH')或扫描二维码来导入密钥，并依照屏幕提示输入验证码。

软件现在应该会显示一个每30秒更新的验证码。

## 测试

从另一台设备和/或另一个终端连接到完成了上述配置的主机:

```
$ ssh hostname
 login as: <username>
 Verification code: <令牌码/备用令牌码>
 Password: <password>
$
```

## 存储位置

如果想要改变密钥存储位置，请使用`--secret`参数:

```
$ google-authenticator --secret="/PATH_FOLDER/USERNAME"
```

然后更改`/etc/pam.d/sshd`内的路径配置:

```
/etc/pam.d/sshd
```

```
auth required pam_google_authenticator.so user=root secret=/PATH_FOLDER/${USER}
```

`user=root` 用于强制PAM使用root用户权限来搜索文件。

另外请注意，密钥文件的所有者是root，生成文件的用户只能读取文件(chmod: `400`)。

```
$ chown root.root /PATH_FILE/SECRET_KEY_FILES
  chmod 400 /PATH_FILE/SECRET_KEY_FILES
```

## 用于桌面登录

谷歌两步认证插件可以同时用于控制台与 GNOME 桌面登录。只需要在文件 `/etc/pam.d/login` 或 `/etc/pam.d/gdm-password` 内加入

```
   auth required pam_google_authenticator.so
```

## 生成两步验证代码

如果你在其他的系统也配置了Google Authenticator，那么如果手机丢失(即你的两步验证器)你就无法登录这些系统了。使用额外的两步验证代码生成方法有时很有帮助。

### 代码管理器

一个可以显示、生成、储存、管理两步验证代码的脚本可以从 [gashell](https://aur.archlinux.org/packages/gashell/)AUR 安装。此外，还有可替代的 auther 程序 [auther-git](https://aur.archlinux.org/packages/auther-git/)AUR。

### 命令行

生成两步验证代码最简单的方法是使用`oath-tool`。可以从 [oath-toolkit](https://archlinux.org/packages/?name=oath-toolkit)包 安装, 并这样使用:

```
oathtool --totp -b ABC123
```

其中， `ABC123` 是配置密钥。

在绝大多数有足够的用户权限的Android系统上，Google Authenticator的数据库可以被拷贝出设备并直接访问。这是一个 [SQLite](../关系型数据库管理系统/SQLite.md)3 数据库。你可以用以下的shell脚本读取Google Authenticator的数据库并对每一个储存的密钥生成动态的两步验证码。

```
google-authenticator.sh
```

```
#!/bin/sh

# This is the path to the Google Authenticator app file.  It's typically located
# in /data under Android.  Copy it to your PC in a safe location and specify the
# path to it here.
DB="/path/to/com.google.android.apps.authenticator/databases/databases"

sqlite3 "$DB" 'SELECT email,secret FROM accounts;' | while read A
do
        NAME=`echo "$A" | cut -d '|' -f 1`
        KEY=`echo "$A" | cut -d '|' -f 2`
        CODE=`oathtool --totp -b "$KEY"`
        echo -e "\e[1;32m$CODE\e[0m - \e[1;33m$NAME\e[0m"
done
```

## 21.2　Chromium

*难度：高级 ｜ 篇幅：47.3 KB ｜ 所属：Google*

将要讲到：安装、配置、提示和技巧、疑难解答、参考

[Chromium](https://en.wikipedia.org/wiki/Chromium_(web_browser)) 是一款来自 "The Chromium Project" 的开源图形网络浏览器，基于 [Blink](https://en.wikipedia.org/wiki/Blink_(web_engine)) 渲染引擎。它也是商业软件 Google Chrome 浏览器得以组成的基础。

在[这里](https://chromium.googlesource.com/chromium/src/+/master/docs/chromium_browser_vs_google_chrome.md)您可以看到 Google Chrome 与 Chromium 浏览器的区别。此外，还有一点重要的不同：

- 2021年3月2日发布的 Chromium 89 及其以后版本不再支持 Google 账户同步功能。

> **注意：**目前，可以通过 [使用 Chrome 的 OAuth2 凭证](https://gist.github.com/foutrelis/14e339596b89813aa9c37fd1b4e5d9d5)或者[申请一个属于自己的凭证](https://www.chromium.org/developers/how-tos/api-keys)来恢复同步功能，但是请注意，这不一定是一个长期的解决方案。
>
> 长期来讲，最好考虑使用 [xbrowsersync](https://www.xbrowsersync.org/) 来同步书签数据。

在[应用程序列表/互联网#基于 Blink](../../03_网络与服务/网络应用程序/应用程序列表_互联网.md#基于_Blink) 您可以看到基于 Chromium 的其它浏览器。

## 安装

要安装 Chromium，有以下几种安装包可选:

- [chromium](https://archlinux.org/packages/?name=chromium)包——稳定版本

- [chromium-snapshot-bin](https://aur.archlinux.org/packages/chromium-snapshot-bin/)AUR——Nightly 每日构建版本

Google Chrome 软件包:

- [google-chrome](https://aur.archlinux.org/packages/google-chrome/)AUR——稳定版本
- [google-chrome-beta](https://aur.archlinux.org/packages/google-chrome-beta/)AUR——Beta 测试版本
- [google-chrome-dev](https://aur.archlinux.org/packages/google-chrome-dev/)AUR——Dev 开发版本
- [google-chrome-canary](https://aur.archlinux.org/packages/google-chrome-canary/)AUR——Canary 发行版本

> **注意：**根据 [Chromium 隐私页面](https://www.chromium.org/Home/chromium-privacy)，"在 Chromium 浏览器的代码中与 Google 有关的特性，编译之后的二进制程序是遵循 [Google Privacy Policy](https://www.google.com/policies/privacy/) 的。"如果不希望 Chromium 浏览器代码中存在与 Google 集成的部分，请参阅[应用程序列表/互联网#注重隐私的 chromium 分支](../../03_网络与服务/网络应用程序/应用程序列表_互联网.md#注重隐私的_chromium_分支)。

## 配置

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[#提示和技巧](#提示和技巧)。**
>
> **附注：** 本节大部分内容应当划分到[#提示和技巧](#提示和技巧)，另有一些应当划分到[#故障排除](#故障排除)。（在 [Talk:Chromium](https://wiki.archlinuxcn.org/wiki/Talk:Chromium) 中讨论）

### 设置为默认浏览器

要将 Chromium 设置成默认浏览器或设置下载文件的打开方式，请参阅[默认应用程序](../../02_系统管理与内核/系统管理/默认应用程序.md)。

### 证书

Chromium 使用 [Network Security Services](../../08_安全与加密/TLS/Network%20Security%20Services.md) 进行证书管理。可以在 `chrome://settings/certificates` 中管理证书。

### 使 flag 持久化

> **注意：**文件 `chromium-flags.conf` 和附带的自定义启动脚本是特定于 Arch Linux 软件仓库所打的 [chromium](https://archlinux.org/packages/?name=chromium)包 包的。对于 [google-chrome](https://aur.archlinux.org/packages/google-chrome/)AUR 和 [google-chrome-dev](https://aur.archlinux.org/packages/google-chrome-dev/)AUR，请用 `chrome-flags.conf` 和 `chrome-dev-flags.conf` 代替。

您可以将 flag 写入 `chromium-flags.conf` 文件，放到 `$HOME/.config/`（或 `$XDG_CONFIG_HOME`，需要配置环境变量）或全局的 `/etc` 目录下。

无需使用特殊语法，这些 flag 就能像终端参数一样被 Chromium 识别和使用。

- 参数按空格分割，并遵循 shell 引用规则，但不会做进一步的解析。
- 假如文件中有不正确的引用，则会引发 Fatal Error。
- 为了便于阅读，可以将 flag 置于单独的行中。
- 以井号（#）开头的行不会被读取. (这条规则只被 [chromium](https://archlinux.org/packages/?name=chromium)包 启动脚本所支持，所以在使用 [google-chrome](https://aur.archlinux.org/packages/google-chrome/)AUR 软件包时其实是没有这条规则的)

以下是 `chromium-flags.conf` 文件的一个例子，它定义了一个 `--start-maximized --incognito` 的 flag：

```
~/.config/chromium-flags.conf
```

```
# This line will be ignored.
--start-maximized
--incognito
```

### 强制使用 GPU 加速

至少从 Chromium 110 开始，大多数系统默认启用 GPU 加速。如果您的系统配置与[阻止列表](https://chromium.googlesource.com/chromium/src/gpu/+/master/config/software_rendering_list.json)匹配，则可能必须将以下 flag 附加到[持久配置](#使flags持久化)：

> **警告：**禁用渲染黑名单可能会导致程序不稳定，在 `chrome://gpu` 可以看到对应的 bug report 的细节。

```
~/.config/chromium-flags.conf
```

```
--ignore-gpu-blocklist
--enable-zero-copy
```

### 视频硬件解码

> **注意：**
>
> - Arch 和 Chromium 都不对这个特性作官方的支持，参见。但是，官方软件仓库中的 [chromium](https://archlinux.org/packages/?name=chromium)包 已经编译有 VA-API 支持，如果遇到问题您可以在[这个论坛帖子](https://bbs.archlinux.org/viewtopic.php?id=244031)中寻求帮助。
>
> - 从 Chromium 122 版本开始不再需要额外的VA-API包。在原生 Wayland 后端下使用官方存储库中的 [chromium](https://archlinux.org/packages/?name=chromium)包 包时 VA-API 就已经工作。
>
> - Chromium 116 放弃了对使用 [libva-intel-driver](https://archlinux.org/packages/?name=libva-intel-driver)包 的英特尔集成显卡的支持。要使 h264 加速正常工作，需要安装 [libva-intel-driver-irql](https://aur.archlinux.org/packages/libva-intel-driver-irql/)AUR。
>
> - 在 AMD GPU 设备上，VA-API 不能开箱即用，需要 mesa>= 24.1 并启用 Vulkan。这可能会导致 X11/XWayland 下的 WebGL 出现问题。从版本 125.0.6422.141-1 起，Vulkan 和 [Wayland](#原生_Wayland_上运行) 可一起使用。
>
> - 当尝试在 `chromium-flags.conf` 中找到正确的 flag 组合时，请注意此文件最多应仅包含一行 `--enable-features` 和 `--disable-features`。多个特性之间必须用逗号连接。

如果要为 Chromium 启用 VA-API 支持：

如果您已经通过检查 `vainfo` 的输出（参考[硬件视频加速#检验 VA-API](../../05_桌面与图形/图形/硬件视频加速.md#检验_VA-API)）确认 VA-API 支持有效，则可以先单独尝试以下标志：

```
~/.config/chromium-flags.conf
```

```
--enable-features=AcceleratedVideoDecodeLinuxGL
```

> **注意：**
>
> - 此外，以下标志可以在使用 EGL/Wayland 时提高性能：
>
> `--enable-features=AcceleratedVideoDecodeLinuxZeroCopyGL`.
>
> - 131 版本前的 Chromium 应当使用 `--enable-features=VaapiVideoDecodeLinuxGL`。

否则，请继续阅读。

欲在 Chromium 中启用加速编码：

- 启用 `AcceleratedVideoEncoder` 特性。如：`--enable-features=AcceleratedVideoDecodeLinuxGL,AcceleratedVideoEncoder`。详情参见 和。

欲启用VA-API支持：

- 为您的显卡安装正确的 VA-API 驱动，并且确认 VA-API 已经被启用并且正常工作，详见[硬件视频加速](../../05_桌面与图形/图形/硬件视频加速.md)。NVIDIA 专有驱动，必须安装 [libva-nvidia-driver](https://archlinux.org/packages/?name=libva-nvidia-driver)包，并且除以上特性外还要启用`VaapiOnNvidiaGPUs` 特性。

- 启用 `VaapiVideoDecoder` 特性，对于 ANGLE GL 渲染器和 [libva-intel-driver](https://archlinux.org/packages/?name=libva-intel-driver)包 生效。

- 当使用 ANGLE 时，Chromium 会强制使用旧的 i965 驱动，不支持 [intel-media-driver](https://archlinux.org/packages/?name=intel-media-driver)包。一个解决方案是[手动配置 VA-API](../../05_桌面与图形/图形/硬件视频加速.md#配置_VA-API)。详见。
- 如果想在 Xorg 上使用系统提供的 GL 渲染器，使用 `--use-gl=egl`。使用Chrome 112时可能不再需要这么参数，使用AMD GPU时这个参数可能会破坏 GPU 加速
- 如果VA-API仍不生效，请尝试`--enable-features=VaapiIgnoreDriverChecks` 或`--disable-features=UseChromeOSDirectVideoDecoder` 标志。
- 如果在X11和旧显卡上VA-API仍不生效，可以设置[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)`LIBVA_DRI3_DISABLE=1`。

#### Vulkan

使用 Vulkan 时，以下标志是必需的，并且在 Chromium 126 和 Mesa 24.1 上可能也足够了：

```
~/.config/chromium-flags.conf
```

```
--enable-features=VaapiVideoDecoder,VaapiIgnoreDriverChecks,Vulkan,DefaultANGLEVulkan,VulkanFromANGLE
```

无需上面提到的任何其他标志。

#### 小提示


去检查使用VA-API驱动程序支持的编解码器是否可播放视频( *vainfo* 会告诉您支持哪些编解码器，但 Chromium 仅支持 VP9 和 h264):

- 通过按下`Ctrl+Shift+I`或在右击菜单中选择检查按钮打开开发工具
- 添加媒体选项卡：*汉堡菜单>更多工具>媒体*
- 在新打开的媒体选项卡中，查看视频解码器的硬件解码器状态

请测试足够大的视频。从版本86开始，桌面版 Chromium [将仅加速大于720p](https://bugs.chromium.org/p/chromium/issues/detail?id=684792)的视频。

VP8/VP9 硬件解码不可用时，想要减少观看YouTube时的CPU占用，请使用 [h264ify](https://chrome.google.com/webstore/detail/h264ify/aleakchihdccplidncghkekgioiakgal)，[enhanced-h264ify](https://chrome.google.com/webstore/detail/enhanced-h264ify/omkfmpieigblcllmkgbflkikinpkodlk)或者[Not yet, AV1](https://chrome.google.com/webstore/detail/not-yet-av1/dcmllfkiihingappljlkffafnlhdpbai) 扩展。

在某些系统上 （尤其是在 Xwayland 上），您可能需要[#强制使用 GPU 加速](#强制使用_GPU_加速)。仅需`--ignore-gpu-blocklist` 一个参数就达到目的。


您可能需要禁用 Skia 渲染器，因为它目前不兼容视频解码加速`--disable-features=UseSkiaRenderer`。

### KDE integration

要集成到[Plasma](../../05_桌面与图形/KDE/KDE.md)中，请安装[plasma-browser-integration](https://archlinux.org/packages/?name=plasma-browser-integration)包。有关更多详细信息，请参阅[KDE Plasma 浏览器集成](https://community.kde.org/Plasma/Browser_Integration)。

### PDF 阅读插件

Chromium 和 Google Chrome 内置了*Chromium PDF Viewer* 插件。如果您不想用这个插件，在`chrome://settings/content/pdfDocuments`勾选*你下载的PDF插件*。

### XWayland 上运行

如果您使用的是 NVIDIA 的专有驱动程序，则在 XWayland 上运行 Chromium 可能会导致 GPU 进程偶尔崩溃。要防止 GPU 进程崩溃，请添加以下标志：

```
--use-angle=vulkan
--use-cmd-decoder=passthrough
```

> **注意：** 这并不能防止所有与 XWayland 相关GPU进程的崩溃。

### 原生 Wayland 上运行

从97版本开始，可以使用以下标志启用 chromium 中的原生 Wayland 支持:

```
--ozone-platform-hint=auto
```

如果这不起作用，例如在[Weston](../../05_桌面与图形/Wayland%20合成器/Weston.md)下的使用106版本，请使用：

```
--ozone-platform=wayland
```

请参阅[#使 flag 持久化](#使_flag_持久化)进行配置。也可通过浏览器 flag 页面（chrome://flags）设置。

这将在 wayland 会话中选择自动 wayland Ozone 后端，因此，即使您在 X11 和 Wayland 之间频繁切换，也可以使用单个`.desktop`文件。

> **注意：**在浏览器标志菜单中更改“ozone-platform-hint”时，浏览器会提供一个“重新启动”按钮。不要使用这个，因为浏览器仍将在更改标志之前的平台中重新启动。您需要关闭浏览器，然后将其打开。

此外，如果您在使用[输入法时遇到问题](https://bugs.chromium.org/p/chromium/issues/detail?id=1422087)，您可能还想强制使用更新的 GTK：

```
--gtk-version=4
```

如果`AltGr`/`Compose` 键停止工作，添加此解决方法可能会修复它：

```
--disable-gtk-ime
```

若你使用fcitx5，在采用上述标志后仍无法正常使用。尝试使用`--enable-wayland-ime`标识代替`--gtk-version=4`。

```
--enable-wayland-ime --wayland-text-input-version=3
```

> **注意：**只有在实现了`text_input_v1` 协议的情况下，启用`--enable-wayland-ime`标志才有效。已知实现该协议的合成器有：Weston，Kwin、Wayfire、Hyprland。

#### 用于导航的触摸板手势

要启用双指滑动来回浏览历史记录，请使用以下标志：

```
--ozone-platform-hint=auto --enable-features=TouchpadOverscrollHistoryNavigation
```

#### 强制设备比例因子

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[HiDPI#Chromium / Google Chrome](../../05_桌面与图形/图形/HiDPI.md#Chromium_/_Google_Chrome)。**
>
> **附注：** 同一主题.（在 [Talk:Chromium](https://wiki.archlinuxcn.org/wiki/Talk:Chromium) 中讨论）

在原生Wayland上强制使用比例因子，用以下flag :

```
--force-device-scale-factor=1.33 --gtk-version=4 --enable-features=WaylandPerSurfaceScale,WaylandUiScale
```

## 提示和技巧

除非明确说明，否则以下提示和技巧应该适用于 Chromium 和 Chrome。

### 浏览体验

#### chrome:// URLs

可以通过 Chrome URLs访问到许多调整。请参阅 chrome://chrome-urls 获取完整的列表。

- **chrome://flags** - 访问实验性功能，例如 WebGL 和使用 GPU 渲染网页等。
- **chrome://extensions** - 查看、启用和禁用当前使用的 Chromium 扩展。
- **chrome://gpu** - 各GPU选项状态。
- **chrome://sandbox** -沙盒状态指示。
- **chrome://version** -显示版本和调节 `/usr/bin/chromium` 激活的开关。

[此处](https://peter.sh/experiments/chromium-command-line-switches/)提供了一直在更新的 Chromium 命令行参数开关的完整列表。

#### Chromium 任务管理器

Shift+ESC 可调出浏览器任务管理器，其中可以查看内存、CPU 和网络使用情况。

#### Chromium 覆盖/重写 Preferences 文件

如果您启用了与 Google 帐户同步，Chromium 将覆盖对 `~/.config/chromium/Default/Preferences` 目录下 Preferences 文件的任何直接编辑。解决方法，使用 `--disable-sync-preferences` 参数启动 Chromium。

```
$ chromium --disable-sync-preferences
```

如果您是在登录桌面环境时后台启动 Chromium，请确保您的桌面环境使用的命令是：

```
$ chromium --disable-sync-preferences --no-startup-window
```

#### 搜索引擎

要使 [wiki.archlinux.org](https://wiki.archlinux.org)和 [wikipedia.org](https://en.wikipedia.org)等网站的首次搜索更方便，在设置 > 搜索下，点击管理搜索引擎。 在那里，编辑 Wikipedia条目并将其关键字更改为w（或您喜欢的其他关键字）。现在，从地址栏在 Wikipedia搜索“Arch Linux”，只需输入“**w arch linux**” 即可。

> **注意：**在 URL 栏中输入内容时，会自动使用 Google 搜索。也可以用关键字前缀触发

#### 临时文件系统

##### 临时文件系统中的缓存

> **注意：**Chromium 将其缓存与其浏览器配置文件目录分开存储。

限制 Chromium 将其缓存写入物理磁盘，可以通过 `--disk-cache-dir` 定义一个替代位置 ：

```
$ chromium --disk-cache-dir="$XDG_RUNTIME_DIR/chromium-cache"
```

缓存应是临时的，在重启或硬锁定后不会保存。另一种选择是在 `/etc/fstab`中设置空间:

```
/etc/fstab
```

```
tmpfs /home/username/.cache tmpfs noatime,nodev,nosuid,size=400M 0 0
```

或者，创建符号链接 `/tmp`。在运行命令之前，请确保删除 Chromium 的缓存文件夹：

```
$ ln -s /tmp /home/username/.cache/chromium
```

##### 临时文件系统中的配置文件

将浏览器配置文件移动到[临时文件系统](https://en.wikipedia.org/wiki/Tmpfs)，包括 `/tmp`，或者 `/dev/shm`，可以改进应用程序响应，因为整个配置文件都存储在内存中。

使用主动配置文件管理工具（如 [profile-sync-daemon](https://archlinux.org/packages/?name=profile-sync-daemon)包）以获得最大的可靠性和易用性。它可以通过符号链接或绑定挂载的形式，同步`浏览器配置文件目录`到内存中。参考 [Profile-sync-daemon](../网页浏览器/Profile-sync-daemon.md)。

#### 启动新的浏览器实例

当您启动浏览器时，它首先检查使用相同数据目录的另一个实例是否已经在运行。如果有，则新窗口与旧实例关联。如果要启动浏览器的独立实例，则必须使用 `--user-data-dir` 参数指定单独的目录：

```
$ chromium --user-data-dir=/path/to/some/directory
```

> **注意：**用户数据的默认位置是 `~/.config/chromium/`。

#### 使用 torrent 客户端直接打开 \*.torrent 文件和磁力链接

默认情况下，Chromium 会直接下载 `*.torrent`文件，您需要单击屏幕左下角的通知，使用默认的 torrent 客户端打开文件。这可以通过以下方法避免：

- 下载一个 `*.torrent` 文件。
- 右键单击屏幕左下角显示的通知。
- 勾选"*始终打开此类型的文件*" 选择框。

请参阅 [xdg-open](../../05_桌面与图形/Freedesktop.org/Xdg-utils.md)更改默认关联。

#### 触摸滚动触摸屏设备

您可能需要指定要使用的触摸设备。 `xinput list`找到您的触摸屏设备，然后使用参数 `--touch-devices=**x**`启动 Chromium，其中“x”是您设备的 ID。

> **注意：**如果设备被指定为从属指针，则使用此选项可能不起作用，请改用主指针的 ID。

#### 减少内存使用

默认情况下，Chromium 对访问过的每个网站实例都使用单独的操作系统进程  但是，您可以在启动 Chromium 时指定命令行开关来修改此行为。

例如，要为所有网站的实例共享一个进程，请执行以下操作：

```
$ chromium --process-per-site
```

要使用单个进程模式：

```
$ chromium --single-process
```

> **警告：**不建议使用单进程模式，因为不安全，并且还可能包含其他模式中不存在的错误

此外，您可以使用扩展（例如 [Tab Suspender](https://chrome.google.com/webstore/detail/tab-suspender/fiabciakcmgepblmdkmemdbbkilneeeh?hl=en)和[OneTab](https://chrome.google.com/webstore/detail/onetab/chphlpgkkbolifaimnlloiipkdnihall?hl=en)）暂停或存储非活动选项卡。

#### 用户代理

可以在 Chromium 的基本实例开始时通过 `--user-agent="[string]"`参数来任意修改 User Agent

#### DOM Distiller

Chromium 具有与 Firefox 类似的阅读器模式。在本例中，它被称为 DOM Distiller，它是一个[开源项目](https://github.com/chromium/dom-distiller)。它默认是禁用的，但可以使用`chrome://flags/#enable-reader-mode` 标志启用，您也可以将其[持久化](#Making_flags_persistent)。DOM Distiller 不仅通过提炼页面内容来提供更好的阅读体验，它还简化了打印页面。即使已从打印对话框中删除了后一个复选框选项，您仍然可以打印提取的页面，这基本上具有相同的效果。

启用该标志后，当 Chromium 认为您正在访问的网站可以提炼时，您会在地址栏中找您一个新的“进入阅读器模式”菜单项和相应的图标。

#### 强制使用特定 GPU

在多 GPU 系统中，Chromium 会自动检测应该使用哪个 GPU 进行渲染（核显或独显）。这在 99% 的时间有效，除非一种情形：如果选择了不可用的 GPU（例如，启用了 VFIO GPU 直通的系统上的独立显卡），他将不可用 。`chrome://gpu`会报无法初始化 GPU 进程。在**驱动程序**信息下方的同一页面上，将显示多个 GPU（GPU0, GPU1, ...）。无法以用户友好的方式在它们之间切换，但您可以读取那里存在的设备/供应商 ID，并用以下参数配置Chromium 来使用特定 GPU：

```
$ chromium --gpu-testing-vendor-id=0x8086 --gpu-testing-device-id=0x1912
```

其中 `0x8086` 和 `0x1912` 替换为您要使用的 GPU 的 ID（如 `chrome://gpu`页面上所示）。

#### 从 Firefox 导入书签

为了简化过渡，您可以将书签从 [Firefox](../Mozilla/Firefox.md)导入到 Chromium 中。

将 Chromium 导航到 `chrome://settings/importData`。

如果您的计算机上已经安装了 Firefox，您可以直接从 Firefox 导入书签以及许多其他内容。

确保已选择**Mozilla Firefox**。可选，您可以在此处取消选中一些不需要的项目。点击**导入**然后点击**完成**就可以了。

> **注意：**如果您尚未在 Chromium 中创建任何书签，书签将显示在您的书签栏中。如果您已经有书签，书签将位于“从 Firefox 导入”新文件夹中。

如果您从另一台 PC 导入书签，则必须先从 Firefox 导出书签。

在火狐中：`Ctrl+Shift+o` >导入和备份 > 导出书签到 HTML文件。

程序几乎相同。您需要前往 `chrome://settings/importData`。但这一次，在下拉菜单中，选择 **HTML 书签文件**并单击**选择文件**按钮上传所需的书签文件。

#### 启用系统通知

进入 `chrome://flags#enable-system-notifications` 并选择启用。

#### 启用鼠标中键自动滚动

自动滚动仍然是一个实验性功能。如果 Chromium 或基于 Chromium 的浏览器不是开发版本，并且正在 Linux 环境中运行，则默认情况下会禁用它。

要启用此功能，请使用 `--enable-features=MiddleClickAutoscroll`标志启动浏览器。如果您想使选项持久化，参考 [#使flags持久化](#使flags持久化)。

> **注意：**
>
> - 虽然设置`--enable-blink-features`与`--enable-features`的工作方式相同 ，但使用前者浏览器可能会显示警告（稳定性和安全性将受到影响），说明这是一个不受支持的标记。
> - 作为替代方案，您可以从 Chrome Web Store 添加具有类似行为的扩展（如 [WHEELY](https://chromewebstore.google.com/detail/wheely-wheel-scroll-for-l/kkmfljfnlmppiaoijkfaejgkhccokpdn)）。

> **提示：**另外一个选择时[安装](../../01_安装与包管理/安装过程/安装指南.md) [chromium-extension-autoscroll](https://aur.archlinux.org/packages/chromium-extension-autoscroll/)AUR，但不建议这样做，因为它是一个过时的包，不是官方的。请谨慎使用。

#### U2F 身份验证

安装 [libfido2](https://archlinux.org/packages/?name=libfido2)包 库。这提供了**启用以用户身份对[U2F](../../08_安全与加密/通用第二因素/通用第二因素.md) 密钥访问**所需的 udev 规则。默认情况下，U2F 密钥只能由 root 访问，如果没有这些规则，Chromium 将报错。

#### 主题

您可以让 Chromium 使用您当前的 GTK 主题来做浏览器菜单和控件。在`chrome://settings/appearance`使用GTK。

#### 深色模式

从 Chromium 114 开始，[XDG桌面门户](../../05_桌面与图形/Freedesktop.org/XDG%20桌面门户.md)用于自动确定用户的首选外观([issue](https://bugs.chromium.org/p/chromium/issues/detail?id=998903))，从而将暗模式启用与用户的 GTK 主题分离。这个偏好将应用于`CSS中的首选配色方案`，`JavaScript`,`设置`和`开发者工具`。

更改首选外观的方法取决于您的 XDG 桌面门户后端。例如，许多桌面环境在其外观设置中都有一个开关。或者例如当用[xdg-desktop-portal-gtk](https://archlinux.org/packages/?name=xdg-desktop-portal-gtk)包时，用以下命令将 Preferred Mode 设置为`prefer-light`，`prefer-dark`或`default` ：

```
$ dconf write /org/gnome/desktop/interface/color-scheme \'prefer-dark\'
```

您可以用[dbus](https://archlinux.org/packages/?name=dbus)包 包中的`dbus-send`命令查询当前首选外观([文档](https://flatpak.github.io/xdg-desktop-portal/#gdbus-org.freedesktop.portal.Settings))：

```
$ dbus-send --session --print-reply=literal --dest=org.freedesktop.portal.Desktop /org/freedesktop/portal/desktop org.freedesktop.portal.Settings.Read string:org.freedesktop.appearance string:color-scheme | tr -s ' ' | cut -d ' ' -f 5
```

- **0**：无偏好
- **1**：偏好深色外观
- **2**：偏好浅色外观

##### Chromium 114之前版本

要启用深色模式并启用深色主题（通常用于隐身模式）将以下标志 追加到[持久化配置](#使flags持久化):

```
~/.config/chromium-flags.conf
```

```
--force-dark-mode
--enable-features=WebUIDarkMode
```

#### 启用侧面板

可以通过`chrome://flags`启用侧面板。您可以启用或禁用**侧面板**，并更改选项，例如**侧面板边框**和**侧面板拖放**。

### 配置文件维护

Chromium 使用 [SQLite](../关系型数据库管理系统/SQLite.md) 数据库来管理历史记录和收藏。随着时间的推移，Sqlite 数据库变得碎片化，到处都是空白空间。但是，由于没有管理进程来检查和优化数据库，因此这些因素最终会导致性能下降。从这些数据库中对未使用的空间进行碎片整理，可以改进启动和其他一些与书签和历史记录相关的任务。

[profile-cleaner](https://archlinux.org/packages/?name=profile-cleaner)包 和 [browser-vacuum](https://aur.archlinux.org/packages/browser-vacuum/)AUR 就是做这个的。

### 安全

#### 禁用 JIT

以降低性能为代价，您可以禁用 JavaScript 到机器码的即时编译。它负责[JS 引擎中大约一半的安全漏洞](https://microsoftedge.github.io/edgevr/posts/Super-Duper-Secure-Mode/)。使用标志`--js-flags=--jitless`.

#### WebRTC

WebRTC是一个通信协议，依赖于JavaScript，可能会从VPN背后泄露一个人的真实IP地址和硬件哈希值。虽然某些软件可能会阻止泄漏脚本运行，但为了安全起见，直接阻止此协议也可能是一个好办法。截至 2016 年 10 月，无法在桌面版 Chromium 上禁用 WebRTC，有一些扩展可用于禁用本地 IP 地址泄漏，其中一个就是这个[扩展](https://chrome.google.com/webstore/detail/webrtc-network-limiter/npeicpdbkakmehahjeeohfdhnlpdklia)。

可以通过 [https://browserleaks.com/webrtc](https://browserleaks.com/webrtc) 测试 WebRTC。

> **警告：**即使可以防止IP泄漏，Chromium 仍然会发送您的唯一哈希值，并且无法阻止这种情况。在 [https://www.browserleaks.com/webrtc#webrtc-disable](https://www.browserleaks.com/webrtc#webrtc-disable) 查看更多

#### SSL 证书

Chromium 没有 SSL 证书管理器。它依赖于 NSS 共享数据库`~/.pki/nssdb`。为了将 SSL 证书添加到数据库，用户必须使用 shell。

##### 为自签名证书添加 CAcert 证书

获取CAcert并创建一个`nssdb`（如果尚不存在）。为此，请先安装 [nss](https://archlinux.org/packages/?name=nss)包软件包，然后完成以下步骤：

```
$ mkdir -p $HOME/.pki/nssdb
$ cd $HOME/.pki/nssdb
$ certutil -N -d sql:.
```

```
$ curl -k -o "cacert-root.crt" "http://www.cacert.org/certs/root.crt"
$ curl -k -o "cacert-class3.crt" "http://www.cacert.org/certs/class3.crt"
$ certutil -d sql:$HOME/.pki/nssdb -A -t TC -n "CAcert.org" -i cacert-root.crt
$ certutil -d sql:$HOME/.pki/nssdb -A -t TC -n "CAcert.org Class 3" -i cacert-class3.crt
```

> **注意：**如果没有密码，用户需要为数据库创建密码.

现在，用户可以手动导入自签名证书。

##### 示例 1：使用shell脚本将证书与TomatoUSB隔离

下面是一个简单的脚本，它将提取证书并将其添加到用户的`nssdb`:

```
#!/bin/sh
#
# usage:  import-cert.sh remote.host.name [port]
#
REMHOST=$1
REMPORT=${2:-443}
exec 6>&1
exec > $REMHOST
echo | openssl s_client -connect ${REMHOST}:${REMPORT} 2>&1 |sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'
certutil -d sql:$HOME/.pki/nssdb -A -t "P,," -n "$REMHOST" -i $REMHOST
exec 1>&6 6>&-
```

用法在注释行中公布。

引用：

- [https://web.archive.org/web/20180718193807/https://blog.avirtualhome.com/adding-ssl-certificates-to-google-chrome-linux-ubuntu](https://web.archive.org/web/20180718193807/https://blog.avirtualhome.com/adding-ssl-certificates-to-google-chrome-linux-ubuntu)
- [https://chromium.googlesource.com/chromium/src/+/master/docs/linux/cert_management.md](https://chromium.googlesource.com/chromium/src/+/master/docs/linux/cert_management.md)

##### 示例 2：使用Firefox将证书与TomatoUSB隔离

[firefox](https://archlinux.org/packages/?name=firefox)包浏览器可用于将证书保存到文件中，以便手动导入到数据库中。

使用 Firefox：

1.  浏览到目标 URL。
2.  在看到“此连接不受信任”警告屏幕时，点击：*我了解风险> 添加异常*
3.  点击：视图>详情 > 导出，将证书保存到临时位置(本例中是`/tmp/easy.pem`）。

现在导入证书以在 Chromium 中使用：

```
$ certutil -d sql:$HOME/.pki/nssdb -A -t TC -n "easy" -i /tmp/easy.pem
```

> **注意：**调整名称以匹配证书。 在上面的示例中，“easy” 是证书的名称。

引用：

- [https://sahissam.blogspot.com/2012/06/new-ssl-certificates-for-tomatousb-and.html](https://sahissam.blogspot.com/2012/06/new-ssl-certificates-for-tomatousb-and.html)

#### Canvas 指纹识别

Canvas 指纹识别是一种技术，它允许网站通过在渲染到 HTML5 Canvas 时检测差异来识别用户。此信息可以通过`--disable-reading-from-canvas`标志设置为无法访问。

要确认此操作有效，请运行[此测试](https://panopticlick.eff.org)，并确保在完整结果中将 “hash of canvas fingerprint” 报告为待定。

> **注意：**
>
> - 某些扩展需要从 canvas 中读取，通过设置`--disable-reading-from-canvas`来破坏。
> - 如果没有canvas 读取，YouTube 播放器或 Google 地图无法正常工作。（参考[Qutebrowser issue 5345](https://github.com/qutebrowser/qutebrowser/issues/5345)，[BBS#255958](https://bbs.archlinux.org/viewtopic.php?id=255958)，[BBS#276425](https://bbs.archlinux.org/viewtopic.php?id=276425)）。

#### 隐私扩展

参见[浏览器扩展#隐私](../网页浏览器/浏览器扩展.md#隐私)。

> **提示：**安装太多扩展可能会占用工具栏中的大量空间。那些无论怎么都不会交互的扩展可以通过右键单击扩展程序并选择*取消固定*来隐藏 。

#### 请勿跟踪

要启用[请勿跟踪](https://en.wikipedia.org/wiki/Do_Not_Track)，访问设置，滚动到`高级`选项，并在 `隐私和安全` 下勾选`发送包含您浏览流量的"请勿跟踪" 请求`。

#### 强制用某一个密码存储

Chromium 使用一个密码库存储来存储您的密码，并使用 Chromium安全存储密钥加密 Cookie 值。

默认情况下，Chromium 会自动发现要使用那个密码库，这可能会导致您在切换到其他桌面环境或窗口管理器时显然丢失密码和 cookie。

您可以强制 Chromium 使用特定的密码库，方法是使用标志`--password-store`（值为下列之一）启动它 :

- `gnome-libsecret`，通过[libsecret](https://gitlab.gnome.org/GNOME/libsecret)使用 [GNOME/Keyring](../../05_桌面与图形/GNOME/GNOME_Keyring.md)。
- `kwallet5`，使用[KDE Wallet](../../05_桌面与图形/KDE/KDE%20Wallet.md) 5
- `kwallet6`，使用 [KDE Wallet](../../05_桌面与图形/KDE/KDE%20Wallet.md) 6
- `basic`，将密码和 Cookie 的加密密钥以纯文本形式保存在文件`Login Data`中。
- `detect`，默认的自动发现行为。

例如，要强制 Chromium 在另一个桌面或 WM 中使用 Gnome Keyring，使用`--password-store=gnome-libsecret`标志,参考 [#flags持久化](#flags持久化)使其持久化。

使用其他桌面环境的密码库时，您可能还希望自动解锁它。参考 [GNOME/Keyring#Using the keyring](../../05_桌面与图形/GNOME/GNOME_Keyring.md#Using_the_keyring) 和 [KDE Wallet#Unlock KDE Wallet automatically on login](../../05_桌面与图形/KDE/KDE%20Wallet.md#Unlock_KDE_Wallet_automatically_on_login).

#### 启用混合的后量子密钥交换

Chromium 从 155 版开始支持 TLS 1.3 的混合后量子密钥交换[X25519Kyber768](https://www.ietf.org/archive/id/draft-tls-westerbaan-xyber768d00-02.html)。。默认情况下，此功能处于禁用状态，但可以使用标志`chrome://flags/#enable-tls13-kyber` 启用。

### 将任何网站作为本机应用程序打开

使用[渐进式Web应用程序](https://developer.chrome.com/blog/getting-started-pwa/)的方式，你可以在无选项卡窗口中打开任何网站：

```
$ chromium --app=https://archlinux.org/
```

您需要使用正确的完整url。

## 疑难解答

### 字体

> **注意：**由于沙箱的原因，Chromium 没有完全集成 fontconfig/GTK/Pango/X/etc。了解更多信息，参考[Linux Technical FAQ](https://dev.chromium.org/developers/linux-technical-faq)。

#### 标签字体过大

Chromium 将按照[GTK#配置](../../07_开发与工具/控件工具集/GTK.md#配置)描述进行GTK设置。配置后，Chromium 将使用`gtk-font-name`设置选项卡（这可能与窗口字体大小不匹配）。 要覆盖这些设置，请使用`--force-device-scale-factor=1.0`。

自 Chrome Refresh 2023 成为默认字体以来，使用 Cantarell 字体的 GNOME 用户可能会注意到选项卡标题中的一些字符（如小写 g）被截断了 参考[issue on chromium.org](https://issues.chromium.org/issues/40934082).

在这个问题修复之前，一个解决方法是[配置](../../05_桌面与图形/字体/字体配置.md#设置默认和后备字体)另外一种字体代替Cantarell，例如：

```
~/.config/fontconfig/conf.d/10-chromium-font.conf
```

```
<match target="pattern">
    <test name="prgname" compare="eq">
        <string>chromium</string>
    </test>
    <test qual="any" name="family">
         <string>Cantarell</string>
    </test>
    <edit name="family" mode="assign" binding="strong">
        <string>Ubuntu</string>
    </edit>
</match>
```

仅当进程名称匹配`chromium`时，此配置才适用，您可以用为 Google Chrome用`chrome`。

### WebGL

您的显卡可能已被 Chromium 列入黑名单。参考[#强制使用 GPU 加速](#强制使用_GPU_加速)。

如果您将 Chromium 与[Bumblebee](../../05_桌面与图形/图形/Bumblebee.md)一起使用，WebGL 可能会因 GPU 沙箱而崩溃。在这种情况下，您可以使用`optirun chromium --disable-gpu-sandbox`禁用 GPU 沙箱。

有关WebGL支持的调试信息，请访问`chrome://gpu/`。

Chromium 可能会在您的用户个人资料中保存有关 GPU 的错误数据（例如：如果您在`使用Optimus的Nvidia卡`和 `Intel卡`之间使用切换时，即使您没使用 `Nvidia卡`或 `primusrun/optirun`，它也会在`chrome://gpu`显示 Nvidia 卡)。使用其他用户目录运行，例如： `chromium --user-data-dir=$(mktemp -d)` 可能会解决这个问题。对于持久性解决方案，您可以通过删除`~/.config/chromium/Local\ State`重置 GPU 信息。

### 不正确的 HiDPI 渲染

Chromium 将自动缩放以适应 [HiDPI](../../05_桌面与图形/图形/HiDPI.md) 显示，但是，这可能会导致渲染的 GUI 不正确。

此标志`--force-device-scale-factor=1`可用于覆盖自动缩放因子。

当 在[#原生 Wayland 上运行](#原生_Wayland_上运行)时，Chromium 将根据配置的缩放来自动缩放每个显示器。

### 每次使用 GNOME Keyring 启动时都会提示密码

参考[GNOME/Keyring#密码没被记住](../../05_桌面与图形/GNOME/GNOME_Keyring.md#密码没被记住)。

### 除密码外，所有内容都能同步

如果仅密码不能同步 (您可以在`chrome://sync-internals/`检查一下)删除配置文件中的登录数据：

```
$ rm ~/.config/chromium/Default/Login\ Data*
```

详情参考[Google Chrome帮助论坛](https://support.google.com/chrome/thread/9947763?hl=en&msgid=23687608)

### 在桌面环境之间切换时丢失 Cookie 和密码

如果启动 Chromium时，在终端中看到该消息`Failed to decrypt token for service AccountId-*`，它可能会尝试使用错误的密码存储后端。当您在桌面环境之间切换时，可能会发生这种情况。

参考[#强制用某一个密码存储](#强制用某一个密码存储)。

### 启用Google同步后挂起启动

尝试使用`--password-store=basic` 或其他合适的密码存储启动 Chrome。

参考[#强制用某一个密码存储](#强制用某一个密码存储)。

### Chromium 每次启动时都要求设置为默认浏览器

如果您使用的是 KDE，并且曾经将 Firefox 设置为默认浏览(通过点击 Firefox 中的按钮)，您可能会发现 Chromium 每次启动时都要求将其设置为默认浏览器，即使您点击" 设为默认值" 按钮。

Chromium 会通过运行 `xdg-settings check default-web-browser chromium.desktop` 来检查此状态。如果输出为“no”，则它不会将自己视为默认浏览器。该脚本 `xdg-settings` 会检查以下 MIME 关联，并期望所有关联都是`chromium.desktop`:

```
x-scheme-handler/http
x-scheme-handler/https
text/html
```

欲修复，在*系统设置 > 应用 >默认应用 >网络浏览器*中选择 Chromium，然后设置 `text/html` 的 MIME 关联:

```
$ xdg-mime default chromium.desktop text/html
```

最后，[更新 MIME 数据库](../../05_桌面与图形/Freedesktop.org/XDG%20MIME%20应用程序.md#新的_MIME_类型):

```
$ update-mime-database ~/.local/share/mime
```

### 登录 Google 时出现“此浏览器或应用程序可能不安全”错误

截至 2020.04.20 年，如果用标志`--remote-debugging-port=9222`来运行 chromium 进行 Web 开发，则无法登录您的 Google 帐户。暂时禁用此标志就可以重新登录。

### 尽管使用刷新率更高的显示器，但 Chromium 渲染速度为 60 FPS

[这里](https://bugs.chromium.org/p/chromium/issues/detail?id=1200167)是关于一般问题的上游错误报告，其中可能包含一些额外的解决方法，[这里](https://bugs.chromium.org/p/chromium/issues/detail?id=1138080)是一个关于混合刷新率的姊妹问题。

#### 混合刷新率

> **提示：**Wayland 后端可能不存在此问题，需要测试。

当使用具有混合刷新率（例如 60Hz 和 144Hz）的显示器时，Chromium 可能会针对较低的 Hz 显示器进行渲染。 此问题有合适的解决方法，添加下面的标志到[持久化配置](#使flags持久化):

```
~/.config/chromium-flags.conf
```

```
--use-gl=egl
--ignore-gpu-blocklist
--enable-gpu-rasterization
```

假设您的合成器也以144 FPS刷新，这配置应该可使Chromium在144Hz显示器上使用时以144 FPS运行。 请记住，由于[FS#67035](https://bugs.archlinux.org/task/67035)，它可能会有点不稳定，但它比卡在 60 FPS 要好得多。

#### 在 Wayland 后端运行

似乎有特定的Wayland合成器的问题触发了这个问题。无论设置如何，Plasma 5 似乎都只在 60Hz 上渲染，但 Plasma 6（rc1，在撰写本文时）使 Chromium 在高刷新率下完美运行。

解决方法可能是在所有其他方法都失败时切换到 XWayland 后端

### Chromium 滚动速度慢

在基于chromium 和 electron 的应用程序中，在日常使用中鼠标滚动可能太慢了。以下是一些解决方案。

给[Libinput#调节滚轮滚动速度](../../04_硬件与外设/输入设备/Libinput.md#调节滚轮滚动速度)注入 `libinput_event_pointer_get_axis_value`函数会提供改变缩放因子的接口。这不是应用程序级别的注入，因此需要一个额外的脚本来调整特定的应用程序的缩放因子。请注意，当比例因子足够大时，在 chromium 的小高度开发人员工具上滚动可能会太快。

IMWheel通过多次重复 X 滚轮按钮事件来增加滚动距离。但是，chromium 将真实滚动和重复滚动假定为两个事件。它们之间有一个很小但明显的延迟，因此鼠标滚轮滚动一次会导致两次页面跳转。此外，触摸板滚动需要格外小心。

[Linux Scroll Speed Fix](https://chrome.google.com/webstore/detail/linux-scroll-speed-fix/mlboohjioameadaedfjcpemcaangkkbp)和 [SmoothScroll](https://chrome.google.com/webstore/detail/smoothscroll/nbokbjkabcmbfdlbddjidfmibcpneigj)是两个支持滚动距离修改的 Chromium 扩展。在网页中滚轮滚动时，将找到当前焦点节点最近的可滚动父节点，然后，即使已经到底部，也将对其调用一个具有给定像素距离的滚动方法。因此，一旦您滚动到文本编辑器或任何可滚动元素中，除了移动鼠标之外，您永远无法从其中滚动出来。

### 视频可以加载但无法播放


这可能是 PulseAudio 问题。请参阅建议的修复方法PulseAudio/Troubleshooting#Browsers (firefox) load videos but do no play.

### 由于数据库损坏，密码未保存

存储的密码数据库可能会损坏并需要重建。这样做会破坏其中的所有数据/丢失存储的密码。

从终端启动 chromium 并查找类似：

```
[472531:472565:1207/055404.688559:ERROR:login_database.cc(1048)] Password decryption failed，encryption_result is 2
```

退出 chromium，然后删除这三个数据库文件。`~/.config/chromium/Default/Login Data*`

再次启动 chromium 应该会重新创建它们。

### 光标在 KDE Wayland 上不正确

参考 [KDE#Plasma 光标有时显示不正确](../../05_桌面与图形/KDE/KDE.md#Plasma_光标有时显示不正确).

### Wayland 下的 Chromium 透明

因为一个[bug](https://issues.chromium.org/issues/329678163)，Chromium 124 必须明确地以`--ozone-platform=wayland`命令行标志启动。

### Wayland 硬件加速缓冲区句柄为空错误

因为一个 [bug](https://issues.chromium.org/issues/331796411),从终端启动时，您可能会在日志中看到以下内容，尤其是在 Wayland 上启用硬件加速的情况下：

```
[333310:333425:0919/121130.103852:ERROR:gpu_channel.cc(502)] Buffer Handle is null.
[333341:18:0919/121130.104000:ERROR:shared_image_interface_proxy.cc(134)] Buffer handle is null. Not creating a mailbox from it.
[333310:333425:0919/121130.137149:ERROR:gbm_pixmap_wayland.cc(82)] Cannot create bo with format= YUV_420_BIPLANAR and usage=SCANOUT_CPU_READ_WRITE
```

目前的解决方法是添加此标志：

```
~/.config/chromium-flags.conf
```

```
--disable-gpu-memory-buffer-video-frames
```

### 没有声音服务器就没有可用的音频

Chromium 不支持。[ALSA#直接寻址硬件](../../04_硬件与外设/声音/ALSA.md#直接寻址硬件).

如页面所示，设置输出设备`pcm.dmixer` 和`pcm.dsnooper`，并使用`-alsa-output-device=pcm.dmixer -alsa-input-device=pcm.dsnooper`标志。

### 启动时出现Gnome“全局快捷方式”菜单

由于定义全局快捷方式的扩展（例如 obsidian web clipper），启动时出现了gnome “全局快捷方式”。这在 [https://github.com/brave/brave-browser/issues/44886](https://github.com/brave/brave-browser/issues/44886) 中有所描述，并且可以通过添加此标志来修复：

```
~/.config/chromium-flags.conf
```

```
--disable-features=GlobalShortcutsPortal
```

### Compose键不起作用：无法使用键盘键入特殊字符

由于一个bug,“Compose”键在最新版本的 chromium 中不起作用。当用户尝试在浏览器的任意位置输入特殊字符（例如“@”或元音变音）时，这一点变得很明显。使用Compose键的特殊组合键（例如，“ALT GR”）适用于除 chromium 之外的所有应用程序。此问题很可能与 gtk 有关，无法通过在 Wayland 和 X11 之间切换来解决。这在 [https://issues.chromium.org/issues/327158031](https://issues.chromium.org/issues/327158031) 有所描述，并且可以通过添加此标志来修复：

```
~/.config/chromium-flags.conf
```

```
--disable-gtk-ime
```

## 参考

- [Chromium主页](https://www.chromium.org/)
- [Google Chrome版本笔记](https://chromereleases.googleblog.com/)
- [Chrome web store](https://chrome.google.com/webstore/)
- [Chromium和Google Chrome的不同](https://en.wikipedia.org/wiki/Chromium_(web_browser)#Differences_from_Google_Chrome)
- [Chromium命令行开关的列表](https://peter.sh/experiments/chromium-command-line-switches/)
- [Profile-sync-daemon](../网页浏览器/Profile-sync-daemon.md) - 将 Chromium 配置文件保存在 tmpfs 中和同步到硬盘的一个系统服务
- [Tmpfs](../../02_系统管理与内核/文件系统/Tmpfs.md) - `/etc/fstab`中的临时文件系统
- [tmpfs 官方内核文档](https://docs.kernel.org/filesystems/tmpfs.html)
