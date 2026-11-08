# 第 7 章　OpenPGP

> 软件包的信任终究是人的信任：包是谁打的，他的 PGP 密钥是什么，这条链又怎样落到 makepkg 的配置里。本章把维护者的规矩与构建的操作接起来：新维护者的待办清单、AUR 与 extra 各自的准则、辞职时的交代事项，以及 makepkg 的配置、使用、优化与问题处理。
> 
> 建议先读《Makepkg》，配置与问题处理必须吃透，优化和小技巧留到需要提速时再翻；《软件包维护者指导原则》只需精读与密钥指纹有关的那一条，待办清单与辞职事项除非要长期维护仓库的包，可直接跳过。最容易踩的坑是拿默认密钥凑合：PGP 密钥不是默认密钥时，必须在配置里写明正确指纹，不然取用的仍是默认密钥而非你指定的那一把；另一个误解是把 AUR 与 extra 的准则当成同一套。

<!-- 本章 2 节，共 49 KB -->

## 7.1　软件包维护者指导原则

*难度：进阶 ｜ 篇幅：19.5 KB ｜ 所属：OpenPGP*

将要讲到：新软件包维护者的 TODO 列表、软件包维护者与 AUR、软件包维护者与 extra，软件包维护准则、Optional、If your PGP key is not the default, specify the right fingerprint:、软件包维护者辞职需要完成的事项

[软件包维护者](../../00_关于与社区/团队/软件包维护者.md)是负责使 AUR 正常工作的 Arch Linux 工作人员。他们负责维护热门软件包（[并在必要时与上游项目沟通或者向上游项目提交补丁](https://lists.archlinux.org/archives/list/aur-general@lists.archlinux.org/message/SI2IZPY4YVB3ACEVJWFR3NCOZSM4HSEV/)），并且参与管理事务的表决。新的软件包维护者由现任包维护者从活跃的社区成员中民主选举产生，包维护者是唯一有权决定 AUR 发展方向的成员群体。

软件包维护者们根据[软件包维护者细则](https://package-maintainer-bylaws.aur.archlinux.org/)来管理社区。

## 新软件包维护者的 TODO 列表

1.  仔细阅读本页面
2.  阅读[软件包维护者细则](https://package-maintainer-bylaws.aur.archlinux.org/)
3.  确认自己在 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 的账户信息是最新的
4.  让一个推荐人在 AUR 中给您软件包维护者状态
5.  提醒 [bureaucrat](https://wiki.archlinux.org/title/ArchWiki:Access_levels_and_roles#Access_levels) 将你的维基账号添加到 ⧼group-archpackager⧽ 组
6.  提醒 [BBS 管理员](https://bbs.archlinux.org/userlist.php?username=&show_group=1&sort_by=username&sort_dir=ASC&search=Submit)更改你在论坛上的账户
7.  让推荐人提供 #archlinux-staff 和 #archlinux-packaging 密码，并加入频道（非必须，但有助于团队成员相互认识并合作）
    - 如果需要 bouncer，请找 heftig 申请加入 [Matrix](../../03_网络与服务/网络电话/Matrix.md)
    - 如果需要 `@archlinux/trusteduser/*username*`，请连续群组联络人 [group contacts](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md#Libera_Chat_group_contacts)
    - 如果想要 `@archlinux/package-maintainer/*username*` cloak，请找[群组联系人](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md#Libera_Chat_group_contacts)进行协助
8.  让一个赞助者在[基础设施仓库问题追踪](https://gitlab.archlinux.org/archlinux/infrastructure/-/issues)中使用 `Onboarding` 模板创建工单，并提供如下信息：
    - 一个 SSH 公钥，如果还没有，参考 [SSH 密钥#生成密钥对](../../03_网络与服务/SSH/SSH%20密钥.md#生成密钥对)生成一个。
    - SSO 帐号要使用的用户名，此名称也会用来创建 `@archlinux.org` 邮箱地址。
    - 全名
    - 个人邮箱地址和对应的有效 PGP 公钥 ID，该信息将用于提供链接到 SSO 账户的开发者界面（archweb）初始密码
    - 是否将你的私人或（尚未创建的）`*username*@archlinux.org` 邮箱地址用在非公开邮件清单，并允许发布到 [arch-dev-public](https://lists.archlinux.org/mailman3/lists/arch-dev-public.lists.archlinux.org/) 邮件清单
9.  按照 [arch:DeveloperWiki:Staff Services#Email](https://wiki.archlinux.org/title/DeveloperWiki:Staff_Services#Email) 配置 `@archlinux.org` 邮箱的密码
10.  参考[添加新打包人密钥的流程](https://md.archlinux.org/1bUNqnNsRCCM4wSc35uQdw#)创建用于软件包签名的新 PGP 密钥对（使用新的 `*username*@archlinux.org` 邮箱地址作为 uid）
11.  让一位推荐人在 [archlinux-keyring 代码库问题追踪页](https://gitlab.archlinux.org/archlinux/archlinux-keyring/-/issues)使用 `New Packager Key` 模板创建工单，以使你的 PGP 密钥受至少三个主密钥持有者签名
12.  安装 [devtools](https://archlinux.org/packages/?name=devtools)包 软件包
13.  为 `repos.archlinux.org` [配置你的 ssh 私钥](../../00_关于与社区/关于%20Arch/AUR%20提交准则.md#认证)
14.  获得权限后，ssh 到 `*yourname*@repos.archlinux.org`
15.  开始你的贡献吧！

### 初级维护者

Since the ratification of [RFC 0014](https://rfc.archlinux.page/0014-merge-package-repos/), new Package Maintainers will be marked as "junior" for their first two months of packaging. During this time, the new Package Maintainer may only push to the *extra-testing* repository. Your sponsors can review your packages as-needed and move them to *extra*.

## 软件包维护者与 AUR

软件包维护者应检查被提交到 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 中的软件包是否含有恶意代码，以及 PKGBUILD 是否符合标准。80% 左右的 PKGBUILD 都非常简单，包维护者团队可以很快地检查其可用性以及安全性。

软件包维护者也应该检查 PKGBUILD 的一些小错误，提出修正和改进建议。软件包维护者应该努力确认所有的软件包是否遵循了 Arch 打包指导准则/标准，并与其他打包者分享他们的技能和经验来提升 archlinux 的总体软件打包质量。

另外，也建议软件包维护者撰写文档来记录一些推荐做法。

### Rewriting git history

In some cases rewriting the history of an AUR repository is required, for example when a user inadvertently uses their legal name in a published commit. This can be automated with [git-filter-branch(1)](https://man.archlinux.org/man/git-filter-branch.1).

To force push the new history, forward the `AUR_OVERWRITE=1` environment variable to [git-push(1)](https://man.archlinux.org/man/git-push.1).

In detail this includes adding `SendEnv AUR_OVERWRITE` to your AUR SSH config and setting the env var on your push command: `AUR_OVERWRITE=1 git push --force`. See  for details.

> **警告：**It is recommended to create a backup of the repository before rewriting history.

**Modify committer or author identity**

Install [git-filter-repo](https://archlinux.org/packages/?name=git-filter-repo)包 and run:

```
$ git-filter-repo --name-callback 'return name.replace(b"Old name", b"New name")' --email-callback 'return email.replace(b"old@email.com", b"new@email.com")'
```

> **提示：**If the username contains special characters you may need to encode the strings `name.replace("Bás Ssze".encode("utf-8"), b"newname")'`

Alternatively, use `git filter-branch --env-filter` with the `GIT_AUTHOR_NAME`, `GIT_AUTHOR_EMAIL`, `GIT_COMMITTER_NAME` and `GIT_COMMITTER_EMAIL` [environment variables](../../02_系统管理与内核/系统管理/环境变量.md). For example:

```
git filter-branch --env-filter '
if test "$GIT_AUTHOR_EMAIL" = "lepetit@prince.com"; then
  GIT_AUTHOR_EMAIL=user@users.noreply.github.com
fi
if test "$GIT_AUTHOR_NAME" = "Antoine de Saint-Exupéry"; then
  GIT_AUTHOR_NAME=user
fi'
```

> **注意：**[git-log(1)](https://man.archlinux.org/man/git-log.1) only displays the git *author* by default. Use `git log --pretty=fuller` to display the *author* and *committer*.

### Handling AUR requests


Package Maintainers should periodically check the [requests](https://aur.archlinux.org/requests) filed on the AUR. For that there are some generic rules what to check for each request type:

**Orphan request**

- check if the request is older then 14 days (the date column turns red in the overview) (you cannot accept it before that anyway)
- check if there was no update to the package itself (commit or release) done in the past 14 days
- check if there was no comment from the AUR package maintainer done in the past 14 days

If all of the above points are true then you can accept the Orphan Request.

## 软件包维护者与 extra，软件包维护准则

### 软件包进入 \[extra\] 仓库的规则

- 软件包不能存在于任何 Arch Linux 仓库中。你还需要确保没有其他打包者同时提交该软件包。检查 AUR 的包评论，阅读 [aur-general](https://lists.archlinux.org/mailman3/lists/aur-general.lists.archlinux.org/) 中新文章的标题，[grep](https://en.wikipedia.org/wiki/Grep) [git-log(1)](https://man.archlinux.org/man/git-log.1) 日志，并向私密打包 [IRC 频道](../../03_网络与服务/互联网中继聊天/Arch%20的%20IRC%20频道.md)发送消息。
- [Pacman 封装](../../06_软件与应用/软件比较/AUR%20助手.md#Pacman_封装)是一个特例，不管投票数有多高，都不会被批准进入。如果是 [AUR 助手](../../06_软件与应用/软件比较/AUR%20助手.md)，可以写封邮件到 `arch-dev-public` 进行提议，并尊重团队成员提出的反对意见。

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** The following rule is arbitrary and not enforced in practice. The next two points need to be adjusted as well.（在 [Talk:软件包维护者指导原则](https://wiki.archlinuxcn.org/wiki/Talk:%E8%BD%AF%E4%BB%B6%E5%8C%85%E7%BB%B4%E6%8A%A4%E8%80%85%E6%8C%87%E5%AF%BC%E5%8E%9F%E5%88%99) 中讨论）

- 只有“流行”的软件包才能进入仓库（指 [pkgstats](https://pkgstats.archlinux.de/packages) 有 1% 使用数或者 AUR 中 10 票以上
- 自动略过该规则的软件包有：
    - i18n 包
    - 辅助功能软件包
    - 驱动程序
    - 满足“流行”定义的软件包的依赖包，包括 makedeps 和 optdeps
    - 属于同一集合并一起发布的软件包，前提是这一集合的某些软件包满足“流行”定义
- 任何不属于上述例外的其它软件包都需首先在 aur-general 邮件列表中提出申请，并解释成为例外的原因（例如重命名的软件包、新软件包等）。软件包进入 *extra* 仓库需要其他三名软件包维护者同意。维护大量“不流行”软件包的软件包维护者的申请更有可能被拒绝。
- 强烈建议软件包维护者移除 *extra* 仓库中他们维护的低使用率软件包。该项非强制，但在有人接手前离职维护者的软件包有可能会被过滤掉
- 软件包从 AUR 提升到其它仓库时，应该将 **pkgrel** 的数值加 *1*（即设为 *n + 1*），这样已安装软件包的用户可以从官方仓库继续收到自动更新。还有一个好处就是避免 **pkgrel** 重置为 *1* 时用户收到本地软件包比仓库更新的警告
- 所有官方软件包的构建脚本都以 0BSD 许可证提供。如果 AUR 包的构建脚本没有显式[以 0BSD 进行许可](../../00_关于与社区/关于%20Arch/AUR%20提交准则.md#提交软件包的规则)，则需要进行重写

### 访问并更新仓库

参考[软件包维护者指引](https://wiki.archlinux.org/title/DeveloperWiki:HOWTO_Be_A_Packager)。

### 停止维护软件包

如果一个软件包维护者不想再维护一个软件包了，需要在 AUR 邮件列表中发出一个通知，以便其他的软件包维护者可以接手。即使没有人接手，软件包维护者也可以停止维护，但应避免放弃大量软件包（即不应接手超出能力范围的包数量）。如果一个软件包已经过时或不再被使用，那么可以将其完全移除。

如果一个软件包已经被完全移除，可以将其重新上传到 AUR，使得普通用户可以替软件包维护者进行维护。

### 将软件包从 AUR 移到 extra

遵循[打包者指引](https://wiki.archlinux.org/title/DeveloperWiki:HOWTO_Be_A_Packager#Adding_a_new_package)中将软件包添加到 *extra* 的正常步骤即可，但记得将对应的包从 AUR 中移除！

### 将软件包从 extra 移至 AUR

按照[打包者指引](https://wiki.archlinux.org/title/DeveloperWiki:HOWTO_Be_A_Packager#Removing_a_package)移除软件包，并将源代码上传到 AUR 即可。

### 将软件包从 extra-testing 移至 extra

Move the package from the *extra-testing* to the *extra* repository using the instructions in the [packager guide](https://wiki.archlinux.org/title/DeveloperWiki:HOWTO_Be_A_Packager#Moving_a_package_between_repositories).

### 在 build.archlinux.org 上远程编译

> **警告：**The following procedures defeats the Web Of Trust model: a user with root access to PKGBUILD.com could alter the package and/or the signature before it gets published.

软件包维护者和开发者可以 SSH 到 build.archlinux.org，并使用 devtools 编译软件包。 和本地编译相比，这样做有如下好处：

- 编译快，网速快
- 环境仅需要配置一次
- 本地系统可以不是 Arch Linux

The process is similar to that of a local setup with devtools. Your GnuPG private is required for signing but you do not want to upload it for obvious security reasons. As such, you will need to forward the GnuPG agent socket from your local machine to the server: this will allow you to sign packages on the build server without communicating your key. This also means that we need to disable the agent on the server before we can run anything.

First, connect to build.archlinux.org and disable

```
$ ssh build.archlinux.org
$ systemctl --user mask gpg-agent.service
```

Make sure gpg-agent is not running (`systemctl --user stop gpg-agent.service`). At this point, make sure that no sockets exist in the folder pointed by `gpgconf --list-dir socketdir`. If they do, remove them or log out and in again. If you have a custom $GNUPGHOME (eg. to move it to `~/.config/gnupg`), you will need to unset that, as it is not possible in gnupg to set the homedir without setting the socketdir. On build.archlinux.org, `StreamLocalBindUnlink yes` is set in `sshd_config`, therefore removing the sockets manually on logout is not necessary.

While the PGP private keys remain on your local machine, the public keys **must** be on the build server. Export your public ring to the build server, e.g. from you local machine

```
$ scp ~/.gnupg/pubring.gpg build.archlinux.org:~/.gnupg/pubring.gpg
```

SSH is required to checkout and commit to the Git repository. You can either set up a new SSH key pair on the server (it is highly discouraged to put your local private key on a server for security reasons) or reuse your local keys via socket forwarding. If you opt for the latter, make sure to disable ssh-agent on the build server if you had enabled it previously (it is not running by default).

Configure you build environment on the build server:

```
~/.makepkg.conf
```

```
PACKAGER="John Doe <john@doe.example>"
## Optional
PKGDEST="/home/johndoe/packages"
SRCDEST="/home/johndoe/sources"
SRCPKGDEST="/home/johndoe/srcpackages"
LOGDEST="/home/johndoe/logs"
## If your PGP key is not the default, specify the right fingerprint:
GPGKEY="ABCD1234..."
```

> **警告：**Forwarding your gpg-agent socket to a remote machine makes it possible for anyone with root access to that system to use your unlocked GPG credentials. To circumvent this issue, we need to disable passphrase caching.

Disable passphrase caching with the following settings:

```
gpg-agent.conf
```

```
default-cache-ttl 0
max-cache-ttl 0
```

Because we will want to keep our usual GPG agent running with its current settings, we are going to run another GPG agent dedicated to the task at hand. Create a `~/.gnupg-archlinux` folder and symlink everything from `~/.gnupg` there, except `~/.gnupg/gpg-agent.conf`. Configure the new GPG agent:

```
~/.gnupg-archlinux
```

```
extra-socket /home/doe/.gnupg-archlinux/S.gpg-agent.extra
default-cache-ttl 0
max-cache-ttl 0
pinentry-program /usr/bin/pinentry-gtk-2
```

The `gpg-agent-extra.socket` will be forwarded to build.archlinux.org.

Start the dedicated agent with

```
$ gpg-agent --homedir ~/.gnupg-archlinux --daemon
```

Connect with:

```
$ ssh -R REMOTE_SSH_AUTH_SOCK:$SSH_AUTH_SOCK -R /run/user/REMOTE_UID/gnupg/S.gpg-agent:/home/doe/.gnupg-archlinux/S.gpg-agent.extra build.archlinux.org
```

or, if using GnuPG as your SSH agent:

```
$ ssh -R /run/user/REMOTE_UID/gnupg/S.gpg-agent.ssh:/run/user/LOCAL_UID/gnupg/S.gpg-agent.ssh -R /run/user/REMOTE_UID/gnupg/S.gpg-agent:/home/doe/.gnupg-archlinux/S.gpg-agent.extra build.archlinux.org
```

Replace `*REMOTE_UID*` and `*LOCAL_UID*` by your user identifier as returned by `id -u` on the build server and locally, respectively. If using ssh-agent, replace `*REMOTE_SSH_AUTH_SOCK*` by the path to the SSH socket on the remote host (it can be anything).

You can make the forwarding permanent for that host. For instance with gpg-agent.ssh:

```
~/.ssh/config
```

```
Host build.archlinux.org
  RemoteForward /run/user/REMOTE_UID/gnupg/S.gpg-agent /run/user/%i/gnupg/S.gpg-agent.extra
  RemoteForward /run/user/REMOTE_UID/gnupg/S.gpg-agent.ssh /run/user/%i/gnupg/S.gpg-agent.ssh
```

Again, replace `*REMOTE_UID*` with the user UID on the build server.

From then on, the procedure should be exactly the same as a local build:

```
$ ssh build.archlinux.org
$ pkgctl repo clone existing-package
$ ...
```

> **注意：**pinentry-curses might not work with socket forwarding. If it fails for you, try using a different pinentry.

## 软件包维护者辞职需要完成的事项

当一个软件包维护者辞去自己的职务时，需要完成以下清单。如果仅辞去软件包维护者，但仍是开发者，就不适用以下步骤。

1.  此软件包维护者的所有包都需要被重新签名（重新打包）。软件包维护者编译的软件包可以通过下面 URL 从 Archweb 查询 [https://archlinux.org/packages/?sort=&q=&packager=$packager&flagged=](https://archlinux.org/packages/?sort=&q=&packager=$packager&flagged=) ，将 packager 替换成包维护者在 Archweb 的用户名即可。
2.  Archweb 需要禁用此维护者的帐号，并将其添加到 'Retired Package maintainers' 组。需从 'Package Maintainers' 移除此软件包维护者，并收回所有软件仓库权限。
3.  从服务器上禁用此帐号的 shell 访问权限（尤其是 repos.archlinux.org, pkgbuild.com）。
4.  移除此用户的 GPG key，并向仓库提交新的 archlinux-keyring 软件包。在 keyring 项目中创建 bug 工单，删除辞职人员的密钥。
5.  将包维护者的 AUR 帐号移出 Package Maintainer 组。
6.  [bureaucrat](https://wiki.archlinux.org/title/ArchWiki:Access_levels_and_roles#Access_levels) 应将维护者的维基账户移出 ⧼group-archpackager⧽ 组。
7.  [BBS 管理员](https://bbs.archlinux.org/userlist.php?username=&show_group=1&sort_by=username&sort_dir=ASC&search=Submit)需要修改包维护者的论坛帐号。

## 7.2　Makepkg

*难度：高级 ｜ 篇幅：29.5 KB ｜ 所属：OpenPGP*

将要讲到：配置、使用、优化、小技巧、问题处理、参阅

> **翻译状态：**
>
> - 本文（或部分内容）译自 [makepkg](https://wiki.archlinux.org/title/makepkg)，最近一次同步于 2026-01-21，若英文版本有所[更改](https://wiki.archlinux.org/title/makepkg?diff=0&oldid=861950)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/makepkg_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

[makepkg](https://gitlab.archlinux.org/pacman/pacman/blob/master/scripts/makepkg.sh.in) 是一个软件包自动化构建脚本，使用时需要一个具备构建条件的 [Unix](https://en.wikipedia.org/wiki/Unix) 环境和一个 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 文件。

*makepkg* 是由 [pacman](https://archlinux.org/packages/?name=pacman)包 包提供的。

## 配置

全局配置位于 `/etc/makepkg.conf`，还可以通过 `$XDG_CONFIG_HOME/pacman/makepkg.conf` 或 `~/.makepkg.conf` 进行用户特定配置。你还可以添加 `/etc/makepkg.conf.d/makepkg.conf` 文件进行系统全局配置更改。在构建软件包前，建议先检查 makepkg 配置。

> **提示：**[devtools](https://archlinux.org/packages/?name=devtools)包 用于[从干净的 chroot 构建软件包](https://wiki.archlinux.org/title/DeveloperWiki:Building_in_a_clean_chroot)的助手脚本会使用 `/usr/share/devtools/makepkg.conf.d/*arch*.conf` 配置文件。

更多信息请参考 [makepkg.conf(5)](https://man.archlinux.org/man/makepkg.conf.5)。

### 打包人信息

每个软件包都会有元数据信息，其中就包含 *packager*。默认情况下，用户自己打包的软件标记为 `Unknown Packager`。如果多个用户会在系统上编译，或者需要发布软件包给其他人，最好提供真实的联系人。打包人信息可以通过 `makepkg.conf` 中的 `PACKAGER` 变量进行设置。

要检查已安装软件包的打包人：

```
$ pacman -Qi package
```

```
...
Packager       : John Doe <john@doe.com>
...
```

要自动化签名过程，请同时在 `makepkg.conf` 中设置 `GPGKEY` 变量.

### 包输出

*makepkg* 默认会在工作目录创建软件包，并把源代码下载到 `src/` 目录。可以配置到自定义的路径，比如将所有构建出的软件包放到 `~/build/packages/` 目录，所有源文件放到 `~/build/sources/` 目录下。

根据需求不同，可以配置 `makepkg.conf` 下的这些变量：

- `PKGDEST` - 软件包构建输出目录
- `SRCDEST` - 用于存放[源数据](../../01_安装与包管理/包开发/PKGBUILD.md#source)的目录（在指向其它位置的情况下，软链接将被放置到 `src/` 目录下）
- `SRCPKGDEST` - 源代码包构建输出目录（通过 `makepkg -S` 构建）

> **提示：**可以参考 [pacman#清理软件包缓存](../../01_安装与包管理/包管理器/Pacman.md#清理软件包缓存) 用命令（例如 `paccache -c ~/build/packages/`）清理 `PKGDEST` 目录

你还可以[#在包目录下使用相对路径](#在包目录下使用相对路径)。

### 验证签名

> **注意：***makepkg* 中的签名验证并不使用 pacman 的密钥环, 而是使用用户的密钥。

如果签名文件是以 *.sig* 或 *.asc* 形式作为 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 源码数组的一部分提供，*makepkg* 会自动[验证](../../03_网络与服务/邮件/GnuPG.md#验证签名)软件包。如果用户未提供需要的签名公钥，*makepkg* 会停止安装过程并提示用户说无法验证 PGP 密钥。

如果缺少软件包所需的公钥，那么 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 很可能带有 [validpgpkeys](../../01_安装与包管理/包开发/PKGBUILD.md#validpgpkeys) 项，其中包含了所需的密钥 ID。你可以[手动进行导入](../../03_网络与服务/邮件/GnuPG.md#导入公共密钥)，也可以在[在公钥服务器上](../../03_网络与服务/邮件/GnuPG.md#使用公钥服务器)进行查找，然后导入。运行 *makepkg* 时使用 `--skippgpcheck` 选项可以临时禁用签名检查。

## 使用

继续之前，确保已安装了 [base-devel](https://archlinux.org/groups/x86_64/base-devel/)包组 [软件包组](../../01_安装与包管理/包管理/元软件包和软件包组.md)。属于这个组的软件包**不会**被要求列在 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 文件的构建时依赖（*makedepends*）中。

> **注意：**
>
> - 确保为传入 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 的命令正确配置了 [sudo](../安全/Sudo.md)。另外，可以通过 [makepkg.conf(5)](https://man.archlinux.org/man/makepkg.conf.5) 配置文件的 `PACMAN_AUTH` 参数指定不同的认证命令。
> - [不允许](https://lists.archlinux.org/archives/list/pacman-dev@lists.archlinux.org/message/ZLRXMUGXULDHLTGSRYVGVWG3BVATV3OY/)以根用户（root）身份运行 `makepkg`。 原因除了 `PKGBUILD` 可能包含恶意代码外，以 root 身份构建软件一般都被认为是不安全的。 无法使用普通用户时需以 [nobody 用户](http://allanmcrae.com/2015/01/replacing-makepkg-asroot/)的身份运行 `makepkg`（例如 `runuser -u nobody makepkg`）。

要构建软件包，用户必须首先创建一个 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 文件或编译脚本（在[创建软件包](../../00_关于与社区/关于%20Arch/创建软件包.md)中有详细描述），或者从 [Arch 构建系统](../../00_关于与社区/关于%20Arch/Arch%20构建系统.md)*（ABS）*、[AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 或其他来源获取。获取到 `PKGBUILD` 后，切换到该文件所在文件夹中，然后执行以下命令构建软件包：

```
$ makepkg
```

如果需要的依赖不满足，*makepkg* 会输出一个警告然后失败。想要编译软件包并自动安装必须的依赖，只需添加 `-s`/`--syncdeps` 参数：

```
$ makepkg --syncdeps
```

如果添加了 `-r`/`--rmdeps` 选项，*makepkg* 会在结束后删除不再需要的编译依赖。如果需要持续编译软件包，请考虑定期[删除未使用软件包](../../01_安装与包管理/包管理器/Pacman_提示和技巧.md#删除未使用的软件包（孤立软件包）)。

> **注意：**
>
> - 这些依赖必须在已配置的软件源之中存在，具体信息请参考 [pacman#软件仓库](../../01_安装与包管理/包管理器/Pacman.md#软件仓库)。另外，用户也可以在编译前手动安装需要的依赖：(`pacman -S --asdeps *dep1* *dep2*`)。
> - 在安装依赖包时，只使用全局值，即不会使用拆分包打包功能中的任何覆盖值。

在满足所有依赖并成功编译软件包后，一个软件包文件（`*pkgname*-*pkgver*.pkg.tar.zst`）将会被创建在工作目录下。如需安装，请使用 `-i`/`--install` 参数（与 `pacman -U *pkgname*-*pkgver*.pkg.tar.zst` 相同）：

```
$ makepkg --install
```

要清理残余的文件和目录（如解压到 `$srcdir` 的文件），请使用 `-c`/`--clean` 选项。这对于在使用同一个文件夹多次编译同一个软件包或者升级软件包版本时很有用。它可以防止过期或残余的文件被呈递到新的构建任务中：

```
$ makepkg --clean
```

更多信息请阅读 [makepkg(8)](https://man.archlinux.org/man/makepkg.8)。

## 优化

默认选项与 [devtools](https://archlinux.org/packages/?name=devtools)包 为[官方仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)构建软件包使用的选项一致。因此，用户可以通过调整以下选项来适应本地环境，以获得或多或少的收益。

### 优化编译结果

通过启用针对主机的编译器优化，可以提高打包后软件的性能。缺点是，为特定处理器架构编译的二进制文件无法在其他机器上正常运行。在 x86_64 机器上，重新构建官方软件包通常不会有显著的性能提升，因此不值得为此投入时间。

不过，使用 “非标准” 编译器标志很容易降低性能。许多编译器优化仅在某些情况下有用，不应对软件包随意应用。除非有评测数据证明某项优化更快，否则很有可能会导致性能下降！Gentoo 的 [GCC 优化](https://wiki.gentoo.org/wiki/GCC_optimization)和[安全的 CFLAGS](https://wiki.gentoo.org/wiki/Safe_CFLAGS) 文章提供了有关编译器优化的更多信息。

#### C 和 C++

传递到 C/C++ 编译器（例如 [gcc](https://archlinux.org/packages/?name=gcc)包 或 [clang](https://archlinux.org/packages/?name=clang)包）的选项是通过 `CFLAGS`、`CXXFLAGS` 和 `CPPFLAGS` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)传入的。在使用 Arch 构建系统时，*makepkg* 会将 `makepkg.conf` 中的配置项作为这些环境变量进行传递。默认值会生成通用的二进制文件，可安装在各种机器上。

> **注意：**
>
> - 记住，不是所有的构建系统都会使用 `makepkg.conf` 中设置的变量。例如，*cmake* 不会遵循 `CPPFLAGS` 预处理器选项环境变量。因此，很多 [PKGBUILD](../../01_安装与包管理/包开发/PKGBUILD.md) 都包含了针对打包软件所使用的构建系统而设置的选项。
> - 源码提供的 `Makefile` 或编译命令行中指定的参数优先级更高，并有可能会取代掉 `makepkg.conf` 中的配置。

GCC 可以自动检测架构，并应用安全的架构特定优化项。要使用该特性，首先需要移除所有 `-march` 和 `-mtune` 标志，然后添加 `-march=native`，例如：

```
/etc/makepkg.conf
```

```
CFLAGS="-march=native -O2 -pipe ..."
CXXFLAGS="${CFLAGS} ..."
```

要查看该操作会启用的选项，执行：

```
$ gcc -march=native -v -Q --help=target
```

> **注意：**如果没有指定 `-march=native`，`-Q --help=target` 将**不会**起作用。具体启用的选项需要编译后才能知道。详细步骤请参考。

进一步优化需要启用对内存占用和编译时间有较大负面影响的选项，做法为将优化级别从 `-O2` 设为 `-O3`。使用该选项一般都能提升二进制文件的性能，但具体效果取决于文件本身。更多信息请参考 [Gentoo:GCC optimization#-O](https://wiki.gentoo.org/wiki/GCC_optimization#-O) 和 [GCC 优化选项页](https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html#Options-That-Control-Optimization)。

#### Rust

从 `pacman` 5.2.2 开始，`makepkg.conf` 还包含了对 `RUSTFLAGS` 环境变量的覆写，用于替换 Rust 编译器的标志。通过在 `RUSTFLAGS` 中添加 `-C target-cpu=native`，Rust 编译器还能检测并应用架构特定优化项：

```
/etc/makepkg.conf.d/rust.conf
```

```
RUSTFLAGS="-C force-frame-pointers=yes -C target-cpu=native"
```

查看该操作会已启用的 CPU 特性：

```
$ rustc -C target-cpu=native --print cfg
```

在不包含 `-C target-cpu=native` 的情况下运行 `--print cfg` 将输出默认配置。

下面为一些可以添加到 `RUSTFLAGS` 的优化参数：

- `-C opt-level=3`：可以根据需要将该值更改为 `3`、`s` 或 `z`。针对分发的构建默认使用 `opt-level=3`。
- `-C codegen-units=*n*`：选择小于 `16` 的 `*n*` 值可以优化二进制文件，但会增加构建耗时。
- `-C link-arg=-z -C link-arg=pack-relative-relocs` 可以降低二进制文件的大小。

更多信息请参考 [Rust 编译器文档](https://doc.rust-lang.org/rustc/codegen-options/index.html)。

### 减少编译时间

#### 并行编译

[make](https://archlinux.org/packages/?name=make)包 编译系统使用 `MAKEFLAGS` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)指定 *make* 的额外选项。这个值也可以在 `makepkg.conf` 文件中进行设置。

使用多核/多处理器系统的用户可以设定同时运行的任务数。可以用 [nproc(1)](https://man.archlinux.org/man/nproc.1) 获得可用处理器的个数，例如：`MAKEFLAGS="--jobs=$(nproc)"`。

有些 `PKGBUILD` 会强制使用 `-j1`，因为某些版本会产生冲突或者软件包本身并不支持。如果出现软件包因为此原因无法编译，请在确认错误是由 `MAKEFLAGS` 引起的前提下，在 bug 跟踪系统中进行[报告](../../00_关于与社区/Arch%20开发/漏洞报告准则.md)（如果是 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 包，则向包维护者报告）。

完整可用选项请参考 [make(1)](https://man.archlinux.org/man/make.1)。

#### 使用内存文件系统进行编译

编译过程需要大量的读写操作，要处理很多小文件。将工作目录移动到 [tmpfs](../../02_系统管理与内核/文件系统/Tmpfs.md) 可能会减少编译时间。

使用 `BUILDDIR` 变量可以临时将 *makepkg* 的编译目录设置到现有 tmpfs，例如：

```
$ BUILDDIR=/tmp/makepkg makepkg
```

将 `makepkg.conf` 中的 `BUILDDIR` 选项取消注释可以永久变更编译目录，具体位于 `/etc/makepkg.conf` 文件末尾的 `BUILD ENVIRONMENT` 一节。设置此变量为 `BUILDDIR=/tmp/makepkg` 可以利用 Arch 默认的 `/tmp` 临时文件系统。

> **注意：**
>
> - 在 [tmpfs](../../02_系统管理与内核/文件系统/Tmpfs.md) 中构建大型软件包时可能会内存不足。
> - 挂载 [tmpfs](../../02_系统管理与内核/文件系统/Tmpfs.md) 目录时不能使用 `noexec` 选项，否则编译命令无法执行。
> - 在 [tmpfs](../../02_系统管理与内核/文件系统/Tmpfs.md) 中构建好的包重启后会消失，设置 [PKGDEST](#包输出) 选项可以将构建结果保存到其它目录。

#### 使用编译缓存

[ccache](../../01_安装与包管理/包开发/Ccache.md) 可以将编译结果缓存起来供下次编译使用，减少编译时间。

#### 使用 mold 链接器

[mold](https://archlinux.org/packages/?name=mold)包 是 [ld](../../06_软件与应用/GNU/GNU%20Compiler%20Collection.md)/[lld](../../07_开发与工具/开发/LLVM.md) 链接器的直接替代，据称其速度稍微较快。

要使用 *mold*，需添加 `-fuse-ld=mold` 到 `LDFLAGS`，例如：

```
/etc/makepkg.conf
```

```
LDFLAGS="... -fuse-ld=mold"
```

要向 *mold* 添加额外选项，可以将它们添加到 `LDFLAGS` 中，例如：

```
/etc/makepkg.conf
```

```
LDFLAGS="... -fuse-ld=mold -Wl,--separate-debug-file"
```

要为 Rust 软件包使用 *mold*，可以将 `-C link-arg=-fuse-ld=mold` 添加到 `RUSTFLAGS` 中，例如：

```
/etc/makepkg.conf.d/rust.conf
```

```
RUSTFLAGS="... -C link-arg=-fuse-ld=mold"
```

#### 禁用调试包和 LTO

包含在 [2024 年 2 月 pacman 6.0.2-9 版本](https://gitlab.archlinux.org/archlinux/packaging/packages/pacman/-/blob/6.0.2-9/makepkg.conf?ref_type=tags)的 [90bf367e 号提交](https://gitlab.archlinux.org/archlinux/packaging/packages/pacman/-/commit/90bf367e61b4f77f8351d0412be3d0c4ddadb85a)默认启用了 `debug` 和 `lto` 选项。

构建调试包能让官方仓库为用户提供更多的问题排查工具（[archlinux/packaging/packages/pacman#23#note_173528](https://gitlab.archlinux.org/archlinux/packaging/packages/pacman/-/issues/23#note_173528)），但在自行构建软件包时，调试包不是必需的，而且会减慢构建进程。参见 [archlinux/packaging/packages/pacman#23#note_173782](https://gitlab.archlinux.org/archlinux/packaging/packages/pacman/-/issues/23#note_173782)。

[链接时优化](https://en.wikipedia.org/wiki/Link-time_optimization)可以生成更优化的二进制文件，但会增加构建耗时（[archlinux/packaging/packages/pacman#23#note_173678](https://gitlab.archlinux.org/archlinux/packaging/packages/pacman/-/issues/23#note_173678)），不一定是一个理想的权衡。

如需禁用这些选项，可以在 `OPTIONS=()` 数组中的这些选项前直接添加 `!` 符号，例如：`OPTIONS=(...!debug !lto...)`

### 压缩

#### 使用其它压缩算法

为了加快打包和安装速度，您可以更改 `PKGEXT`，代价是生成的软件包文件更大。

例如，以下命令可以跳过压缩步骤，使得安装时无需解压：

```
$ PKGEXT='.pkg.tar' makepkg
```

另一个例子使用 lz4 算法，可以提升速度：

```
$ PKGEXT='.pkg.tar.lz4' makepkg
```

要使这些设置之一永久生效，请在 `/etc/makepkg.conf` 中设置 `PKGEXT` 。

#### 在压缩时使用多个 CPU 核心

[zstd](https://archlinux.org/packages/?name=zstd)包 支持[对称多处理（SMP）](https://en.wikipedia.org/wiki/Symmetric_multiprocessing)，通过添加 `-T`/`--threads` 标志可以加速压缩。默认情况下，`/etc/makepkg.conf` 中的 `COMPRESSZST` 数组包含了 `-T0` 标志，使得 *zstd* 尽可能使用多的物理 CPU 核心来压缩软件包。可以通过 `--auto-threads=logical` 标志让 *zstd* 根据逻辑 CPU 核心数进一步提升调用数：

```
COMPRESSZST=(zstd -c -T0 --auto-threads=logical -)
```

[lz4](https://archlinux.org/packages/?name=lz4)包 和 [xz](https://archlinux.org/packages/?name=xz)包 默认使用多线程，不需要对 `/etc/makepkg.conf` 进行修改。

[pigz](https://archlinux.org/packages/?name=pigz)包 是 [gzip](https://archlinux.org/packages/?name=gzip)包 的一个替代、并行实现，它默认使用所有可用的CPU核心（可以使用 `-p`/`--processes` 标志来使用较少的核心）：

```
COMPRESSGZ=(pigz -c -f -n)
```

[pbzip2](https://archlinux.org/packages/?name=pbzip2)包 是 [bzip2](https://archlinux.org/packages/?name=bzip2)包 的一个替代、并行实现，它也默认使用所有可用的CPU核心。可以使用 `-p#` 标志来使用较少的核心（注意：`-p` 和核心数之间没有空格）：

```
COMPRESSBZ2=(pbzip2 -c -f)
```

[lbzip2](https://archlinux.org/packages/?name=lbzip2)包 是 [bzip2](https://archlinux.org/packages/?name=bzip2)包 的另一个替代、并行实现，它也默认使用所有可用的CPU核心。可以使用 `-n` 标志来使用较少的核心。

```
COMPRESSBZ2=(lbzip2 -c -f)
```

[plzip](https://aur.archlinux.org/packages/plzip/)AUR 是 [lzip](https://archlinux.org/packages/?name=lzip)包 的一个多线程实现，它也默认使用所有可用的 CPU 核心。可以使用 `-n`/`--threads` 标志来使用较少的核心。

```
COMPRESSLZ=(plzip -c -f)
```

#### 修改压缩等级

有几种压缩算法（包括 zstd 和 xz）支持设定压缩等级，以在速度、内存占用和压缩效率间进行取舍。

## 小技巧

### 减少下载和解压时间

#### 设定源文件位置

特别是在编译 [VCS 软件包](../../01_安装与包管理/Arch%20打包准则/VCS%20软件打包准则.md)时，使用 `SRCDEST` 可以缩短重新构建时获取和解压源代码的时间。

#### 自定义 DLAGENTS

可以使用支持并行下载的下载器，以 [axel](https://archlinux.org/packages/?name=axel)包 为例：

```
/etc/makepkg.conf
```

```
DLAGENTS=('file::/bin/curl -gqC - -o %o %u'
          'ftp::/bin/axel -n 2 -o %o %u'
          'http::/bin/axel -n 2 -o %o %u'
          'https::/bin/axel -n 2 -o %o %u')
```

另请参考 Nonfree applications package guidelines#Custom DLAGENTS。

### 生成新校验和

安装 [pacman-contrib](https://archlinux.org/packages/?name=pacman-contrib)包，然后在 PKGBUILD 文件所在目录中执行以下命令来生成新校验和：

```
$ updpkgsums
```

`updpkgsums` 使用 `makepkg --geninteg` 生成校验和，查看[此论坛](https://bbs.archlinux.org/viewtopic.php?pid=1933763#p1933763)了解详情

也可以用如 `sha256sum` 命令生成校验和并手动加入到 `sha256sums` 数组中。

### 从本地源文件生成

如果您想对源代码进行更改，可以使用`-o`、`--nobuild`下载源代码，而无需构建软件包，仅进行下载和解压缩文件选项。

```
$ makepkg -o
```

您现在可以对源代码进行更改，然后使用`-e`，`--noextract` 不提取源文件（使用现有的$srcdir/ dir）选项构建软件包。使用`-f`选项覆盖已经生成的和现有的包。

```
$ makepkg -ef
```

### 显示具有特定打包程序的包

[expac](https://archlinux.org/packages/?name=expac)包 是一个pacman数据库提取工具。此命令显示系统上安装的所有打包程序名为*packagername*的包：

```
$ expac "%n %p" | grep "packagername" | column -t
```

这将显示系统上安装的所有软件包， `/etc/makepkg` 变量 `PACKAGER`仅显示在中定义的存储库中的包 `/etc/pacman.conf`。

```
$ . /etc/makepkg.conf; grep -xvFf <(pacman -Qqm) <(expac "%n\t%p" | grep "$PACKAGER$" | cut -f1)
```

### 在 64 位系统上构建 32 位软件包

参考[32位软件包打包准则](../../01_安装与包管理/Arch%20打包准则/32%20位软件包打包准则.md)。

### 软件包签名托管

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[GnuPG#Unattended_passphrase](../../03_网络与服务/邮件/GnuPG.md#Unattended_passphrase)。**
>
> **附注：** 非 *makepkg* 特定。（在 [Talk:Makepkg](https://wiki.archlinuxcn.org/wiki/Talk:Makepkg) 中讨论）


在使用类似 Jenkins 的自动构建环境时，用户可能无法为用于软件签名的 gpg 私钥提供密码。另外，不建议在没有密码保护的系统上存放 GPG 私钥。

使用 *makepkg* 创建的 zst 软件包可以在创建后通过如下命令进行签名：

```
$ gpg --detach-sign --pinentry-mode loopback --passphrase --passphrase-fd 0 --output NewlyBuilt.pkg.tar.zst.sig --sign NewlyBuilt.pkg.tar.zst
```

其中 GPG 密码由你选择的自动化套件安全地提供和隐藏。

生成的 `zst` 和 `sig` 文件可被需要有效签名的 [pacman](../../01_安装与包管理/包管理器/Pacman.md) 客户端和使用 `repo-add --sign` 创建的软件仓库引用。

### 磁力链接 URI

使用 [transmission-dlagent](https://aur.archlinux.org/packages/transmission-dlagent/)AUR 下载器可以支持在 `source` 字段使用磁力链接 URI（前缀为 `magnet://`）。

### Running makepkg in a systemd control group

If the package you are building takes too many resources to build with your default *make* flags, which are otherwise set properly for most packages, you can try running it in its own control group. [makepkg-cg](https://aur.archlinux.org/packages/makepkg-cg/)AUR is a wrapper for *makepkg* that achieved this via systemd control groups (see [systemd.resource-control(5)](https://man.archlinux.org/man/systemd.resource-control.5)).

### Running with idle scheduling policy

Package build process can lead to high CPU utilization, especially in case of [#Parallel compilation](#Parallel_compilation). Under heavy CPU load, the system can issue a significant slowdown up to becoming unusable, even with the highest [nice(1)](https://man.archlinux.org/man/nice.1) value. User interface and foreground applications may stutter or even became unresponsive.

This can be worked around by changing the scheduling policy to `SCHED_IDLE` before running *makepkg*. It ensures that package building process does not interfere with regular tasks and only utilizes remaining unused CPU time.

From [sched(7) § SCHED_IDLE: Scheduling very low priority jobs](https://man.archlinux.org/man/sched.7#SCHED_IDLE:_Scheduling_very_low_priority_jobs):

: This policy is intended for running jobs at extremely low priority (lower even than a +19 nice value with the `SCHED_OTHER` or `SCHED_BATCH` policies).

The `SCHED_IDLE` policy can be set by running [chrt(1)](https://man.archlinux.org/man/chrt.1) command with the `-i` flag, specifying priority 0 (the only valid option for `SCHED_IDLE`) and specifying the PID of the current shell.

For most shells:

```
$ chrt -iap 0 $$
```

> **提示：**You can apply this command for every build by placing it into `makepkg.conf`.

For the [fish](../../07_开发与工具/命令行解释器/Fish.md) shell, where `$$` is not set:

```
$ chrt -iap 0 %self
```

### 在包目录下使用相对路径

Instead of using absolute paths for the [package output options](#Package_output), you can also configure relative paths inside each package directory.

> **注意：**The following options might cause problems with some [AUR helpers](../../06_软件与应用/软件比较/AUR%20助手.md), as they might use `makepkg.conf` in a context where `$startdir` is not defined. So be careful.

For example, you can define target paths in your `makepkg.conf` file as follows. The `$startdir` variable refers to the directory where a `PKGBUILD` is located when you build a package.

```
PKGDEST="$startdir/build/packages/"
SRCDEST="$startdir/build/sources/"
SRCPKGDEST="$startdir/build/srcpackages/"
LOGDEST="$startdir/logs/"
```

This will result in:

- Built packages will be stored in: `"package directory"/build/packages/`
- All downloaded source files will be stored in: `"package directory"/build/sources/`
- Built source packages will be stored in: `"package directory"/build/srcpackages/`
- All logs will be stored in: `"package directory"/logs/`

*makepkg* will still create `src/` and `pkg/` directories as usual, so this is expected behaviour.

## 问题处理

### Specifying install directory for QMAKE based packages

The makefile generated by *qmake* uses the environment variable `INSTALL_ROOT` to specify where the program should be installed. Thus this package function should work:

```
PKGBUILD
```

```
...
package() {
	cd "$srcdir/${pkgname%-git}"
	make INSTALL_ROOT="$pkgdir" install
}
...
```

Note, that qmake also has to be configured appropriately. For example put this in the corresponding *.pro* file:

```
YourProject.pro
```

```
...
target.path = /usr/local/bin
INSTALLS += target
...
```

### WARNING: Package contains reference to $srcdir

由于某种原因，有时 `$pkgdir` 或 `$srcdir` 中的字面字符串会进入到软件包中的文件。

可以在 *makepkg* 构建文件夹中执行以下命令进行检测：

```
$ grep -R "$PWD/src" pkg/
```

一个可能是 C/++ 代码使用了 `__FILE__` 宏并将完整路径传递给了编译器。

Dotnet 二进制文件有时也会在默认配置文件中包含 `.pdb` 文件的完整路径。

### Makepkg fails to download dependencies when behind proxy

When *makepkg* calls dependencies, it calls pacman to install the packages, which requires administrative privileges via *sudo*. However, *sudo* does not pass any [environment variables](../../02_系统管理与内核/系统管理/环境变量.md) to the privileged environment, and as well excludes the proxy-related variables ftp_proxy, http_proxy, https_proxy, and no_proxy.

In order to have *makepkg* working behind a proxy, invoke one of the following methods.

#### Enable proxy by setting its URL in XferCommand

The XferCommand can be set to use the desired proxy URL in `/etc/pacman.conf`. Add or uncomment the following line in `pacman.conf`:

```
/etc/pacman.conf
```

```
...
XferCommand = /usr/bin/curl --proxy http://username:password@proxy.proxyhost.com:80 --location --continue-at - --fail --output %o %u
...
```

#### Enable proxy via sudoer's env_keep

Alternatively, one may want to use sudoer's `env_keep` option, which enables preserving given variables the privileged environment. See [Pacman#pacman does not honor proxy settings](../../01_安装与包管理/包管理器/Pacman.md#pacman_does_not_honor_proxy_settings) for more details.

### Makepkg fails, but make succeeds

If something successfully compiles using *make*, but fails through *makepkg*, it is almost certainly because `/etc/makepkg.conf` sets an incompatible compilation variable. Try adding these flags to the PKGBUILD `options` array:

`!buildflags`, to prevent its default `CPPFLAGS`, `CFLAGS`, `CXXFLAGS`, and `LDFLAGS`.

`!makeflags`, to prevent its default `MAKEFLAGS`.

`!debug`, to prevent its default `DEBUG_CFLAGS`, and `DEBUG_CXXFLAGS`, in case the PKGBUILD is a debug build.

If any of these fix the problem, this could warrant an upstream bug report assuming the offending flag has been identified.

## 参阅

- [makepkg(8)](https://man.archlinux.org/man/makepkg.8)
- [makepkg.conf(5)](https://man.archlinux.org/man/makepkg.conf.5)
- [Makepkg 流程概览](https://gist.github.com/Earnestly/bebad057f40a662b5cc3)
- [makepkg 源码](https://gitlab.archlinux.org/pacman/pacman/blob/master/scripts/makepkg.sh.in)
