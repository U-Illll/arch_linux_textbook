# 第 12 章　TOMOYO Linux

> TOMOYO Linux 走的是与 SELinux 一类标签式方案不同的强制访问控制路线，本章要解决的是它究竟是什么、为何这样设计，以及怎样真正用起来。只有一节，顺序上建议先读简介，吃透它的定位与基本模型；开发分支、1.x、AKARI 三部分讲的是历史沿革，时间紧可以略过；2.x 是现行主线，要看清它与 1.x 的分界；最后的「使用」是唯一的动手部分，必须读完。最容易踩的坑有两个：一是拿标签式 MAC 的直觉直接套过来，规则与预期不符就断定是配置写错；二是照着 1.x 时代的旧资料去操作 2.x，混淆了两者的差别。

<!-- 本章 1 节，共 14 KB -->

## 12.1　TOMOYO Linux

*难度：进阶 ｜ 篇幅：14.5 KB ｜ 所属：TOMOYO Linux*

将要讲到：简介、开发分支、TOMOYO Linux 1.x、AKARI、TOMOYO Linux 2.x、使用

> **翻译状态：**
>
> - 本文（或部分内容）译自 [TOMOYO Linux](https://wiki.archlinux.org/title/TOMOYO_Linux)，最近一次同步于 2026-06-14，若英文版本有所[更改](https://wiki.archlinux.org/title/TOMOYO_Linux?diff=0&oldid=826786)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/TOMOYO_Linux_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

[TOMOYO Linux](https://tomoyo.sourceforge.net/)是针对 Linux 的一种[强制访问控制（Mandatory Access Control, MAC）](../安全/安全.md#强制访问控制)实现。该项目于 2003 年 3 月启动，并由[NTT Data公司](https://www.nttdata.com/global/en/)资助。TOMOYO Linux 专注于系统行为，允许每个进程声明实现其目的所需的行为和资源。它既能作为系统分析工具使用，也能作为访问限制工具使用。

TOMOYO Linux 的安全目标是提供“既能覆盖绝大多数用户的实际使用需求，又能保持让绝大多数管理员易于使用的强制访问控制(MAC)”。TOMOYO Linux 不仅是面向安全专业人员的工具，也同样适用于普通用户和系统管理员。

有关强制访问控制概念，参见：[zhwp:强制访问控制](https://zh.wikipedia.org/wiki/%E5%BC%BA%E5%88%B6%E8%AE%BF%E9%97%AE%E6%8E%A7%E5%88%B6)

> **注意：**本条目仅作上游用户文档补充说明。有关更多信息，参见[上游用户文档](https://tomoyo.sourceforge.net/documentation.html)

> **提示：**[TOMOYO Linux 2.x](#TOMOYO_Linux_2.x)最终将在功能特性上接近 1.x 分支，但对于追求快速上手的用户而言，2.x 分支更易于安装。[TOMOYO Linux 1.x](#TOMOYO_Linux_1.x) 分支适用于对安全性要求极高的场景，而 [AKARI](#AKARI) 则介于两者之间。

## 简介

TOMOYO Linux 试图构建一个让所有配置都以易于理解的方式预先安排妥当的系统：

- 提前获取在内核生命周期内至少会发生一次的所有访问请求。
- 允许管理员编写仅放行预期内且合规的访问请求策略。

与[AppArmor](../访问控制/AppArmor.md)不同，TOMOYO Linux 旨在保护整个系统免受攻击者利用应用程序漏洞带来的威胁。TOMOYO Linux 应对该威胁的方式是：先在测试环境中记录所有应用的行为，随后在生产环境中强制所有应用只能在这些已记录的范围内运行。

TOMOYO Linux 并不适用于希望直接使用他人提供的现成策略文件的用户。它需要从零开始构建策略，并通过“学习模式(learning mode)”提供辅助，该模式可以针对特定系统自动生成具备必要且充分权限的策略文件。TOMOYO Linux 能够报告 Linux 系统内部正在发生的情况，因此也可以作为系统分析工具使用。它类似于 *strace*，能够报告每个程序正在执行的操作以及访问了哪些文件或网络。

该[对比表](https://web.archive.org/web/20200724003239/http://tomoyo.osdn.jp/wiki-e/?WhatIs#comparison)提供了 TOMOYO Linux与[AppArmor](../访问控制/AppArmor.md)、[SELinux](../../06_软件与应用/红帽/SELinux.md)以及[SMACK](https://en.wikipedia.org/wiki/Smack_(software))的全面对比。

## 开发分支

[TOMOYO Linux 1.x](https://tomoyo.sourceforge.net/1.8/index.html)是最初的开发分支。TOMOYO Linux 于 2005 年 11 月 11 日首次发布。它以可应用于 Linux 内核的补丁形式实现，目前仍在积极开发中。该分支可以与 SELinux、SMACK 和 AppArmor 等其他安全模块并存。

[TOMOYO Linux 2.x](https://tomoyo.sourceforge.net/2.6/index.html)是 Linux 主线内核(Mainline Kernel)的开发分支。2009 年 6 月，TOMOYO 被合并至 Linux 2.6.30 内核版本中，并使用标准的 Linux 安全模块(LSM)钩子(Hooks)。然而，若要将 TOMOYO Linux 的完整 MAC 功能移植到 Linux 内核中，则必须进一步扩展 LSM 钩子。因此，该分支尚未提供与 1.x 开发分支对等的功能。该[图表](https://tomoyo.sourceforge.net/comparison.html)对比了各分支之间的差异。

[AKARI](https://tomoyo.sourceforge.net/akari/1.0/index.html)基于 TOMOYO Linux 1.x 分支，并可以加载内核模块(LKM)的形式实现。因此，在无需针对内核应用补丁并重新编译的前提下，该分支具有更显著的使用优势。该[表格](https://tomoyo.sourceforge.net/akari/comparison.html)提供了 AKARI 与 TOMOYO Linux 1.x 及 2.x 分支的全面对比。

## TOMOYO Linux 1.x

通过应用 *ccs-patch* 补丁的内核来实施 TOMOYO Linux 1.x，以获取该项目所能提供的完整功能。然而，由于必须使用[ccs-patch](https://sourceforge.net/projects/tomoyo/files/ccs-patch/)对内核进行补丁处理并随后重新编译，因此在实施该分支时需要克服多重困难。

必须同时安装打过补丁的内核以及用户空间工具。AUR 中提供了[ccs-tools](https://aur.archlinux.org/packages/ccs-tools/)AUR软件包。

### 初始化配置

首先必须初始化策略：

```
# /usr/lib/ccs/init_policy
```

策略文件会保存在`/etc/css/`目录中，可以通过运行以下命令进行编辑：

```
# ccs-editpolicy
```

## AKARI

### AKARI 的局限性

在纯粹将 TOMOYO Linux 项目用于系统分析的前提下。AKARI 是实现该目标最简便的方法。而在将其用于系统限制的前提下，这是以最小的工作量获取 TOMOYO Linux 1.x 分支绝大部分功能的方法。然而，有下述局限性：

- 其取决于发行版提供的内核版本与具体配置：

```
CONFIG_SECURITY=y [必需]
CONFIG_KALLSYMS=y [必需]
CONFIG_PROC_FS=y [必需]
CONFIG_MODULES=y [必需]
CONFIG_SECURITY_PATH=y [可选：用于支持绝对路径名]
CONFIG_SECURITY_NETWORK=y [可选：用于提供网络限制]
```

- 由于缺少所需的 LSM 钩子，部分高级网络操作的限制功能会受制约或无法使用。
- 无法限制[内核功能(capabilities)](../安全/Capabilities.md)的使用。
- 为了保持内核二进制接口(KABI)不发生改变，每个任务的变量(pre-task variables)被管理在*struct task_struct*之外，这导致其查找速度较慢。不过，由于基于路径名的权限检查所带来的性能下降占主导地位，因此对于典型的最终用户而言，此项性能损耗应该并不明显。

该[表格](https://tomoyo.sourceforge.net/akari/comparison.html)提供了 AKARI 与 TOMOYO Linux 1.x 及 2.x 分支的全面对比。

### 安装

必需同时安装 AKARI 以及用户空间工具。AUR 中提供了[akari](https://aur.archlinux.org/packages/akari/)AUR和[ccs-tools](https://aur.archlinux.org/packages/ccs-tools/)AUR软件包。

若要激活 AKARI，请设置以下[内核参数](../../02_系统管理与内核/内核/内核参数.md)：

```
init=/usr/bin/ccs-init
```

### 初始化配置

首先必需初始化策略：

```
# /usr/lib/ccs/init_policy --module_name=akari
```

策略文件会保存在`/etc/css/`目录中，可以通过运行以下命令进行编辑：

```
# ccs-editpolicy
```

## TOMOYO Linux 2.x

### TOMOYO Linux 2.x 的局限性

TOMOYO Linux 2.x 在 Linux 主线内核中的实现尚未完全结束，但自 2.5.x 版本以来已非常接近 1.x 分支。与 1.x 分支相比，仍有少数特性有待实现。该[图表](https://tomoyo.sourceforge.net/comparison.html)提供了各开发分支之间差异的全面对比。

### 安装

所有[官方支持的内核](../../02_系统管理与内核/内核/内核.md#官方支持的内核)均已内置对 TOMOYO 的支持。

针对自定义内核，请确保设置了以下内核选项：

```
CONFIG_SECURITY=y
CONFIG_SECURITYFS=y
CONFIG_SECURITY_NETWORK=y
CONFIG_SECURITY_PATH=y
CONFIG_SECURITY_TOMOYO=y
CONFIG_SECURITY_TOMOYO_POLICY_LOADER="/usr/bin/tomoyo-init"
CONFIG_SECURITY_TOMOYO_ACTIVATION_TRIGGER="/usr/lib/systemd/systemd"
 CONFIG_LSM="landlock,lockdown,yama,integrity,tomoyo,bpf"
```

安装用户空间工具包的 2.6 分支软件包[tomoyo-tools](https://aur.archlinux.org/packages/tomoyo-tools/)AUR。

### 激活

若要激活 TOMOYO Linux，请设置以下[内核参数](../../02_系统管理与内核/内核/内核参数.md)以启用该 Linux 安全模块(LSM)：

```
lsm=landlock,lockdown,yama,integrity,tomoyo,bpf
```

> **注意：**`lsm=`内核参数用于设置 Linux 安全模块的初始化顺序。内核配置的`lsm=`可以通过`zgrep CONFIG_LSM=/proc/config.gz`确定，当前生效的值可通过`cat /sys/kernel/security/lsm`查看。
>
> - 确保`tomoyo`放在列表第一个“主要(major)”模块的位置有关有效值及其顺序的示例，可以在[security/Kconfig](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/security/Kconfig)中找到。
> - 由于`capability`总是会被自动包含，因此应从`lsm=`中省略

在`/usr/lib/systemd/systemd`执行时，TOMOYO 将从`/etc/tomoyo/policy/current`中加载所有已保存的策略。

接下来，检查激活是否成功。通过[dmesg](../../07_开发与工具/命令行/核心工具.md)应该会看到如下所示（或类似）的输出行：

```
# dmesg | grep -A 1 -B 1 TOMOYO
```

```
[    0.003375] Security Framework initialized
[    0.003387] TOMOYO Linux initialized
[    0.003396] AppArmor: AppArmor disabled by boot time parameter
--
[    6.829798] Calling /usr/bin/tomoyo-init to load policy. Please wait.
[    6.833709] TOMOYO: 2.5.0
[    6.833712] Mandatory Access Control activated.
```

首次使用时，如果需要在计算机关机/重启时将内存中的策略自动保存到文件系统，请创建以下[systemd](../../01_安装与包管理/初始化/Systemd.md)服务单元：

```
/etc/systemd/system/tomoyo-savepolicy.service
```

```
[Unit]
Description=Tomoyo savepolicy

[Service]
Type=oneshot
ExecStart=/bin/true
ExecStop=/usr/bin/tomoyo-savepolicy
StandardInput=tty
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

随后，启用`tomoyo-savepolicy.service`。

### 禁用

从`lsm=`[内核参数](../../02_系统管理与内核/内核/内核参数.md)中移除`tomoyo`，或者完全移除`lsm=`参数。

### 初始化策略

首先必需初始化策略：

```
# /usr/lib/tomoyo/init_policy
```

策略文件会保存在`/etc/tomoyo`目录中，可以通过运行以下命令进行编辑：

```
# tomoyo-editpolicy
```

默认情况下，TOMOYO 启动时会使用*Disabled*（已禁用）配置集（参见下方的配置集表格）。如果现在就要为所有实体启用学习模式，只需要在`etc/tomoyo/policy/current/domain_policy.conf`中`*<kernel>*`命名空间的配置集：

```
<kernel>
use_profile 1
use_group 0
```

在无法确定是否需要如此广泛的学习模式的前提下，直接忽略此步骤即可。稍后可以通过*tomoyo-editpolicy*工具，在*Domain transition editor*（域迁移编辑器）中对任何选定的域（或多个域）按下`S`键来切换配置集。

完成上述配置后，需要重启计算机。

### 日志守护进程

TOMOYO 拥有专属的日志守护进程`tomoyo-auditd`。它对于监控闭源应用程序的行为非常实用。初始化配置文件中包含详实的注释说明，位于`/etc/tomoyo/tools/auditd.cconf`，而日志文件则存放在`/var/log/tomoyo`目录中

若需配合 Systemd 使用该进程，请按照[官方文档](https://tomoyo.sourceforge.net/2.6/chapter-4.html)第 4.6 章所述内容创建`/etc/systemd/system/tomoyo-auditd.service`文件。

## 使用

为能够高效使用 TOMOYO Linux 或 AKARI，必需查阅相关文档：

- [TOMOYO Linux 文档](https://tomoyo.sourceforge.net/documentation.html)
- [AKARI 文档](https://tomoyo.sourceforge.net/akari/documentation.html)

运行策略编辑器以开始编辑。在运行 TOMOYO Linux 1.x 或 AKARI 的前提下，应使用*ccs-tools*：

```
# ccs-editpolicy
```

如果使用 TOMOYO Linux 2.x，则应使用：

```
# tomoyo-editpolicy
```

随着系统的运行，TOMOYO Linux 将创建域(domains)并将其添加到树状结构中。TOMOYO Linux 中的访问分析/限制是通过域来实现的。每个进程都属于一个唯一的域，并且每当进程执行一个程序时，就会迁移到一个不同的域。域的名称是表示进程执行历史的级联字符串表达式。例如，内核所属的域为`*<kernel>*`；由内核调用的`/sbin/init`所属的域名为`*<kernel> /sbin/init*`；如果`/sbin/init`调用了`/etc/rc.d/rc`，则其所属的域名为`*<kernel> /sbin/init /etc/rc.d/rc*`。可以根据需要抑制或初始化域迁移。

可以为每个域分配配置集(profiles)。共有四种默认配置集：

<table class="wikitable"><tbody><tr><td>Disabled（已禁用）</td><td>效果与普通内核相同。</td></tr><tr><td>Learning（学习）</td><td>即使访问请求违反了策略，也不予拒绝，而是将该请求追加到策略中。</td></tr><tr><td>Permissive（宽容）</td><td>即使访问请求违反了策略，也不予拒绝，且不将该请求追加到策略中。</td></tr><tr><td>Enforcing（强制）</td><td>若请求违反策略，予以拒绝，不将该请求追加到策略中。</td></tr></tbody></table>

学习(learning)配置集可用于分析系统或有特定需求的应用程序。一旦确定了某个域的所有预期访问请求，即可在切换到强制(enforcing)配置集之前，根据需要编辑该域的策略。此操作可以从系统启动开始，针对任何及所有域进行设置。

## 参见

- [Wikipedia:Tomoyo Linux](https://en.wikipedia.org/wiki/Tomoyo_Linux)
- [TOMOYO Linux 主页](https://tomoyo.sourceforge.net/)
- [TOMOYO Linux 维基](https://web.archive.org/web/20200422131542/http://tomoyo.osdn.jp/wiki-e/)
- [AKARI 主页](https://tomoyo.sourceforge.net/akari/)
- [AKARI 文档](https://tomoyo.sourceforge.net/akari/documentation.html)
- [AKARI/TOMOYO 功能对比表](https://tomoyo.sourceforge.net/akari/comparison.html)
- [TOMOYO Linux 1.8.x : 官方指南](https://tomoyo.sourceforge.net/1.8/index.html)
- [TOMOYO Linux 2.6.x : 官方指南](https://tomoyo.sourceforge.net/2.6/index.html)
- [TOMOYO Linux 安全目标](https://lwn.net/Articles/263179/)
- [规则示例](https://tomoyo.sourceforge.net/cgi-bin/lxr/source/centos5.5/domain_policy.conf?v=policy-sample)
- [Embedded Linux Wiki上的 TOMOYO Linux 条目](https://elinux.org/TomoyoLinux)
- [来自 PacSec 2007 的演示文稿幻灯片](https://sourceforge.net/projects/tomoyo/files/docs/PacSec2007-ja-demo.pdf)
