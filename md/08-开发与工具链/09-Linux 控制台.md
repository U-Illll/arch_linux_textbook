# 第 9 章　Linux 控制台

> 虚拟控制台是登录与排障的底层入口，默认配置往往只够登录。这一章处理登录与周边：用 Getty 管理登录和虚拟控制台数量，用键盘配置调整布局、按键延时和重复频率，再往下是字体、光标、HiDPI 与提示音。
> 
> 顺序按 Getty、键盘配置、Linux 控制台推进，前两节必读，否则登录和输入都难保证；Gpm 管控制台内的鼠标，用不上跳过。常见误解：以为自动登录只拨一个开关，实际还要把虚拟控制台一并安排；以为引导消息会自己留在 tty1，其实它和 Getty 争同一块屏幕；以及把按键延时、重复频率的调整当成键盘映射生效。

<!-- 本章 4 节，共 36 KB -->

## 9.1　Gpm

*难度：入门 ｜ 篇幅：3.8 KB ｜ 所属：Linux 控制台*

将要讲到：安装、配置、参见

GPM（General Purpose Mouse，通用鼠标）是为 Linux 虚拟控制台（TTY）提供鼠标支持的守护进程。

## 安装

安装 [gpm](https://archlinux.org/packages/?name=gpm)包 软件包。有关笔记本电脑上的触摸板支持，请参阅[笔记本电脑#触摸板](../../04_硬件与外设/笔记本电脑/笔记本电脑.md#触摸板)。

## 配置

`-m` 参数用于声明要使用的鼠标。`-t` 参数用于定义鼠标类型。要获取 `-t` 选项的可用类型列表，请使用 `-t help` 运行 `gpm`。

```
# gpm -m /dev/input/mice -t help
```

[gpm](https://archlinux.org/packages/?name=gpm)包 软件包需要使用一些参数启动。这些参数可以通过创建文件 `/etc/conf.d/gpm` 记录，也可以在直接运行 *gpm* 时使用。`gpm.service` 默认包含 USB 鼠标的参数（`ExecStart=/usr/bin/gpm -m /dev/input/mice -t imps2`）。

显然，如果需要为其他鼠标类型使用该服务，应该对其进行编辑，最好以 [systemd 友好的方式](../../01_安装与包管理/初始化/Systemd.md#修改现存单元文件)。

- PS/2 鼠标：

    ```
    -m /dev/psaux -t ps2
    ```

- IBM Trackpoint（“小红点”）：

    ```
    -m /dev/input/mice -t ps2
    ```

> **注意：**如果鼠标只有 2 个按钮（无中键），则将 `-2` 传递给 `GPM_ARGS`，第二个按钮将执行粘贴功能。

找到合适的配置后，启动并启用 `gpm.service`。

更多信息见 [gpm(8)](https://man.archlinux.org/man/gpm.8)。

### QEMU 或 VirtualBox

QEMU 和 VirtualBox 默认模拟的鼠标在使用 *gpm* 和 X 时存在严重的定位和点击问题。鼠标的定位与主机失去同步，因此无法在不反复退出并重新进入窗口的情况下悬停于某些区域。点击的位置与光标显示的位置不符。

QEMU 和 VirtualBox 通过提供 USB 平板设备的模拟来解决此问题，该模拟提供了绝对定位功能。（[libvirt](https://archlinux.org/packages/?name=libvirt)包 自动使用这种方式。）

然而，*gpm* 只知道如何以相对定位模式使用模拟的鼠标，因此这些问题依然存在。尝试通过 `-t` 使用其他类型也均无法正确工作。

[gpm-vm](https://aur.archlinux.org/packages/gpm-vm/)AUR 包含了一个已有数年的[拉取请求](https://github.com/telmich/gpm/pull/23)，用于添加 VirtualBox 的 USB 平板支持（在 QEMU 下也能工作），并修改了 `gpm.service` 文件以默认启用它。

你可能需要更改所使用的事件。给 *gpm* 原始的 `-m /dev/input/mice` 不会起作用。默认情况下：

```
/etc/gpm-vm.conf
```

```
event="/dev/input/event2"
```

你可以通过安装 [evtest](https://archlinux.org/packages/?name=evtest)包 并运行以下命令来确定要使用的事件：

```
# evtest
```

```
...
/dev/input/event2:      QEMU QEMU USB Tablet
...
```

如果需要给 *gpm* 添加额外的选项，可以在 `/etc/gpm-vm.conf` 中设置 `additional_args`。

一旦找到合适的配置，启动并启用 `gpm.service`。

## 参见

- [Gentoo:GPM](https://wiki.gentoo.org/wiki/GPM)
- [consolation](https://aur.archlinux.org/packages/consolation/)AUR 基于 libinput 的替代品

## 9.2　Getty

*难度：进阶 ｜ 篇幅：6.3 KB ｜ 所属：Linux 控制台*

将要讲到：安装、添加额外的虚拟控制台、自动登录到虚拟控制台、将引导消息保留在 tty1 上、参考资料

[getty](https://en.wikipedia.org/wiki/getty_(Unix)) 是管理终端线路及其所连终端的程序的通用名称。其目的是保护系统，防止未经授权的访问。通常，每个 getty 进程由 [systemd](../../01_安装与包管理/初始化/Systemd.md) 启动，一个进程管理一条终端线路。

## 安装

*agetty* 是 Arch Linux 中默认的 getty 程序，它是 [util-linux](https://archlinux.org/packages/?name=util-linux)包 包的一部分。它在等待登录时修改 TTY 设置，使得换行符不会转换为 CR-LF，否则会使打印到控制台的消息产生“阶梯效应”。Agetty 管理着虚拟控制台，Arch Linux 中默认提供六个虚拟控制台。一般按 `Ctrl+Alt+F1` 到 `Ctrl+Alt+F6` 来访问它们。

其他可选替代包括：

- **mingetty** — 一个允许自动登录的最小化 getty。

: [mingetty](https://aur.archlinux.org/packages/mingetty/)AUR || [mingetty](https://aur.archlinux.org/packages/mingetty/)AUR

- **fbgetty** — 类似于 mingetty，支持帧缓冲。

: [http://projects.meuh.org/fbgetty/](http://projects.meuh.org/fbgetty/)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2022-09-18 ⓘ\] || [fbgetty](https://aur.archlinux.org/packages/fbgetty/)AUR

- **mgetty** — 在 Unix 下处理调制解调器各个方面功能的程序。

: [http://mgetty.greenie.net/](http://mgetty.greenie.net/) || [mgetty](https://aur.archlinux.org/packages/mgetty/)AUR

## 添加额外的虚拟控制台

打开 `/etc/systemd/logind.conf` 文件并将 **NAutoVTs=6** 设置为你想要在启动时得到的虚拟控制台数量。

如果你想临时获取一个控制台，可以为所需的 TTY 启动一个 getty 服务，执行：

```
$ systemctl start getty@ttyN.service
```

## 自动登录到虚拟控制台

配置自动登录要使用 systemd 的[附加代码片段 (drop-in snippet)](../../01_安装与包管理/初始化/Systemd.md#修改现存单元文件) 功能来重写传递给 *agetty* 的默认参数。

虚拟控制台和串口控制台的配置是不同的。大多数情况下，你应该是想在虚拟控制台下设置自动登录（这种控制台的设备名称为 `tty*N*`，其中 `*N*` 是一个数字）。串口控制台的自动登录配置稍有不同，它们的设备名称类似于 `ttyS*N*`，其中 `*N*` 是一个数字。

### 虚拟控制台

> **注意：**[根据用户报告](https://bbs.archlinux.org/viewtopic.php?id=238576)这种方法可能会影响系统休眠。

要[修改现存单元文件](../../01_安装与包管理/初始化/Systemd.md#修改现存单元文件)，可以手动创建下列附加文件，或执行 `systemctl edit getty@tty1` 并输入附加代码片段 (drop-in snippet) 的内容：

```
/etc/systemd/system/getty@tty1.service.d/override.conf
```

```
[Service]
ExecStart=
ExecStart=-/usr/bin/agetty --autologin username --noclear %I $TERM
```

> **提示：**默认 `getty@.service` 中的 `Type=idle` 选项将会推迟该服务的启动时间，直到所有任务（该单元的前置任务）已经完成，防止启动信息淹没了登录提示符。当 自动启动 X 时，可以通过添加 `Type=simple` 到 [附加代码片段 (drop-in snippet)](../../01_安装与包管理/初始化/Systemd.md#修改现存单元文件) 来立即启动 `getty@tty1.service`，因为此时 init 进程和 *startx* 都被 [屏蔽](../../01_安装与包管理/启动过程/静默启动.md) 了输出，避免残留启动时的信息。

如果你想用 *tty* 而不是 *tty1*，请参阅 [Systemd 常见问题](../../01_安装与包管理/初始化/Systemd_FAQ.md#如何修改默认的_tty_控制台（getty）数量？)。

### 串口控制台

创建以下文件（及目录）：

```
/etc/systemd/system/serial-getty@ttyS0.service.d/autologin.conf
```

```
[Service]
ExecStart=
ExecStart=-/usr/bin/agetty --autologin username -s %I 115200,38400,9600 vt102
```

### Nspawn 控制台

要为 [systemd-nspawn](../../08_安全与加密/沙箱/Systemd-nspawn.md) 容器配置自动登录，需要重写 *console-getty* 服务：

```
/etc/systemd/system/console-getty.service.d/override.conf
```

```
[Service]
ExecStart=
ExecStart=-/sbin/agetty --noclear --autologin username --keep-baud console 115200,38400,9600 $TERM
```

## 将引导消息保留在 tty1 上

默认情况下，Arch 会启动 `getty@tty1` 服务。该服务单元文件已经写入了 `--noclear` 参数，它可以阻止 agetty 清空屏幕。但是 [systemd](../../01_安装与包管理/初始化/Systemd.md) 会在启动该服务之前清空屏幕。要关闭这项特性，请创建 `/etc/systemd/system/getty@tty1.service.d/noclear.conf` 文件：

```
/etc/systemd/system/getty@tty1.service.d/noclear.conf
```

```
[Service]
TTYVTDisallocate=no
```

这将仅改写 TTY1 上的 *agetty* 的 `TTYVTDisallocate` 参数，并保持全局服务文件 `/usr/lib/systemd/system/getty@.service` 不变。可参阅 [Systemd#修改现存单元文件](../../01_安装与包管理/初始化/Systemd.md#修改现存单元文件)。

> **注意：**
>
> - 确保从 [内核参数](../../02_系统管理与内核/内核/内核参数.md) 中移除了 `quiet`。
> - KMS 晚启动可能会造成部分早期启动信息丢失。请参阅 [Kernel mode setting#KMS 早启动](../../02_系统管理与内核/内核/内核级显示模式设置.md#KMS_早启动) 或 [Kernel mode setting#禁用 KMS](../../02_系统管理与内核/内核/内核级显示模式设置.md#禁用_KMS)。

## 参考资料

- [Systemd#更改开机默认启动目标](../../01_安装与包管理/初始化/Systemd.md#更改开机默认启动目标)
- [The TTY demystified](https://www.linusakesson.net/programming/tty/)
- [Wikipedia:Tty (unix)](https://en.wikipedia.org/wiki/Tty_(unix))

## 9.3　Linux 控制台/键盘配置

*难度：进阶 ｜ 篇幅：10.4 KB ｜ 所属：Linux 控制台*

将要讲到：查看键盘设置、键盘映射、修改按键延时和重复频率

用于 [Linux 控制台](Linux%20控制台.md)的键盘映射（keymaps）、控制台字体和控制台映射由 [kbd](https://archlinux.org/packages/?name=kbd)包 包（[systemd](../../01_安装与包管理/初始化/Systemd.md) 的依赖）提供，它还提供了许多管理文本控制台的低级工具。此外，*systemd* 还提供了 *localectl* 工具，可以控制控制台和 Xorg 的系统[区域](../../00_关于与社区/本地化/Locale.md)和键盘布局设置。

## 查看键盘设置

使用 `localectl status` 查看当前的键盘配置。

## 键盘映射

键盘映射文件存储在 `/usr/share/kbd/keymaps/` 目录树中。通常一个键盘映射文件对应一个键盘布局（`include` 语句可用于共享通用的部分，一个键盘映射文件可以包含多个布局，其中一些按键组合用于切换）。更多细节请参见 [keymaps(5)](https://man.archlinux.org/man/keymaps.5)。

### 列出键盘映射

控制台键盘映射的命名约定有些随意，但它们通常基于：

- [语言代码](https://en.wikipedia.org/wiki/ISO_639-1)：语言代码与国家代码相同（例如 `de` 表示德语，`fr` 表示法语）。
- [国家代码](https://en.wikipedia.org/wiki/Country_code)：不同国家使用相同语言的不同变体（例如，`uk` 用于英国英语， `us` 用于美国英语）；国家代码列表也可以在 [wikipedia:ISO 3166-1#Officially assigned code elements](https://en.wikipedia.org/wiki/ISO_3166-1#Officially_assigned_code_elements) 中找到。
- [键盘布局](https://en.wikipedia.org/wiki/Keyboard_layout)：布局与国家或语言无关（例如 `dvorak` 用于 [Dvorak 键盘布局](https://en.wikipedia.org/wiki/Dvorak_Simplified_Keyboard)）。

使用以下命令查看可用键盘映射的列表：

```
$ localectl list-keymaps
```

要搜索键盘映射，请使用以下命令，将 `*search_term*` 替换为您的语言、国家或布局代码：

```
$ localectl list-keymaps | grep -i search_term
```

或者，使用 find：

```
$ find /usr/share/kbd/keymaps/ -type f -name "*search_term*"
```

### Loadkeys

可以只为当前会话设置一个键盘映射。这在测试不同的键盘映射、解决问题等的时候非常有用。

*loadkeys* 工具用于此目的，它由 [systemd](../../01_安装与包管理/初始化/Systemd.md) 在加载 `/etc/vconsole.conf` 中配置的键盘映射时在内部使用。用法很简单：

```
# loadkeys 键盘映射
```

详情请参阅 [loadkeys(1)](https://man.archlinux.org/man/loadkeys.1)。

### 持久化设置

持久键盘映射可以在`/etc/vconsole.conf`中设置，[systemd](../../01_安装与包管理/初始化/Systemd.md) 可以在启动时加载。 `KEYMAP` 变量来明确定义键盘映射。如果此变量是空的或没有设置， `us`键盘映射作为默认值。参见 [vconsole.conf(5)](https://man.archlinux.org/man/vconsole.conf.5) 详细设置。 例如:

```
/etc/vconsole.conf
```

```
KEYMAP=uk
...
```

*localectl* 可以方便设置终端键盘映射。既可以改变在`/etc/vconsole.conf`里 `KEYMAP` 变量值也可以为当前会话设置临时值。

```
# localectl set-keymap --no-convert keymap
```

```
--no-convert 选项可以阻止 localectl 自动的安装最近似的适配改变 Xorg keymap （不会影响xwindows下键盘布局）。参见 localectl(1) 获得更多细节。
```

如果有需求，可以在早期用户空间时，使用 `keymap` 钩子从 `/etc/vconsole.conf` 设置中读取键盘映射 [mkinitcpio hook](../../01_安装与包管理/Initramfs/Mkinitcpio.md#Common_hooks)。

> **提示：**如果将自定义的键盘映射保存在 `/usr/share/kbd/keymaps/` 以外的目录中，在设置`vconsole.conf`或使用上面提到的 *localectl*命令时使用完整的绝对路径。

### 创建自定义键盘映射

当使用终端时,你可以使用热键输出特殊字符。然而我们还可以输出一系列字符和一些转义字符。如果我们输出一系列字符组成命令，之后转义字符换一个新行，这个命令将被执行。

编辑键盘映射文件可以实现上述方法。但是，由于更新系统原因键盘映射文件会被重写，不鼓励编辑上述键盘映射文件。一个更好的解决方案是整合现存的键盘映射文件和个人设置的键盘映射文件，`loadkeys` 命令工具可以做到这一点。

首先，创建一个键盘映射文件，这个键盘映射文件可以存放在任何地方，但是推荐的方法是在`/usr/local`: create里面模拟目录结构：创建/usr`/usr/local/share/kbd/keymaps`目录，编辑edit `/usr/local/share/kbd/keymaps/personal.map`文件。

PS：值得注意的是，这样的个人键盘映射文件对于重新定义已经是默认的键盘映射的按键行为也非常有用:当使用`loadkeys` 指令时，默认的键盘映射和新的指令相冲突时，默认键盘映射被取代，反之则保留。这种方法仅仅会改变在个人键盘映射里面必须被改变的键盘映射。

> **提示：**
>
> - 你也可以编辑一个现存的键盘映射，它在`/usr/share/kbd/keymaps/` 目录里面。键盘映射文件的扩展名是.map.gz，比如 *us.map.gz* 是美国键盘映射。复制这个键盘映射到 `/usr/local/share/kbd/keymaps/personal.map.gz` 并且使用gunzip解压缩之。
>
> - 通过设置 `/etc/vconsole.conf` 文件自定义的个人键盘映射可以持久化。鉴于此，如果你在[mkinitcpio hook](../../01_安装与包管理/Initramfs/Mkinitcpio.md#Common_hooks)中使用的`sd-vconsole` 来代替 `keymap`，你应该把你的自定义键盘映射文件放入`/usr/share/kbd/keymaps/`中。这种方法它的来自 `/usr/share/kbd/keymaps`的依赖关系会被钩子自动的加载到初始内存镜像中去。另一方面，如果你把自定义的键盘映射放在了 `/usr/local/` 下，它的依赖需要在 `mkinitcpio.conf` 文件中 `FILES` 行手工明确加载。

#### 增加映射命令

个人键盘映射包括2种指令。首先，[keycode](../../04_硬件与外设/键盘配置/额外的键盘按键.md) 命令，这和默认键盘映射看到的格式相匹配。这指令把 keycode 和 keysym 联系在一起。keysyms 代表键盘的动作。这个可用的动作包括：输出字符码或转义字符，切换终端和键盘映射，启动机器等许多其他动作。全部的当前活动的键盘映射可以从以下命令获得:

```
# dumpkeys -l
```

大部分的键盘动作都很直观。例如，设置 112键输出“e”，这个命令是：

```
keycode 112  = e
```

设置112输出 euro符号，这个命令是：

```
keycode 112 = euro
```

一些键盘动作不能立即和某个键盘按键联系起来。尤其是键盘动作前缀是大写的F后面是1-3位数字，F1-F246构成大于30的总是空白的。定义一个热键输出一系列字符或其他动作是非常有用：

```
keycode 112 = F70
```

此时F70可以绑定输出特定的字符串：

```
string F70 = "Hello"
```

当112键被按下时，会输出F70的内容。为了在终端执行可以输出的命令，换行符必须附加到命令字符串的后面。例如：输出系统休眠命令[enter a system into hibernation](../../02_系统管理与内核/电源管理/电源管理.md#Suspend_and_hibernate) ，加入下面的键盘映射。

```
string F70 = "sudo systemctl hibernate\n"
```

#### 其他例子

- 对于 emacs 用右侧的 Alt 行使和左侧 Alt 同样的功能，用下面的命令在你的键盘映射里。它将会包含 `/usr/share/kbd/keymaps/i386/include/linux-with-two-alt-keys.inc` ，可以查看它的实现细节。

```
include "linux-with-two-alt-keys"
```

- 切换 Caps_lock 和 Escape 键（为vim），重新映射相应的 keycodes。

```
keycode 1 = Caps_Lock
keycode 58 = Escape
```

- 切换 Caps_lock 作为额外的 Control 键，重新映射相应的 keycodes。

```
keycode 58 = Control
```

- 互换 Caps_lock 和左侧 Control 键，重新映射相应的 keycodes。

```
keycode 29 = Caps_Lock
keycode 58 = Control
```

#### 储存更改

为了使用个人键盘映射，必须使用 *loadkeys*。

```
# loadkeys /usr/local/share/kbd/keymaps/personal.map
```

然而，这个键盘映射只能在当前会话被激活。为了在启动时使用这个键盘映射，需要在 [/etc/vconsole.conf](#持久化设置Persistent_configuration) 中设置`KEYMAP` ，并且明确写出个人键盘映射文件绝对路径。这个文件不必想被 [kbd](https://archlinux.org/packages/?name=kbd)包 提供的官方的键盘映射一样使用gunzip压缩。

KEYMAP=/usr/local/share/kbd/keymaps/personal.map

## 修改按键延时和重复频率

**按键延时**是只长按一个按键多少时间（通常以毫秒为单位）才会开始重复这个按键。开始重复过程后，字符会以一定频率重复（通常以赫兹单位），也就是**重复频率**。这些值可以通过 *kbdrate* 设置。请注意，这些设置需要为控制台和 Xorg 分别配置。

```
# kbdrate [-d delay] [-r rate]
```

例如，要将延时设置为 200 毫秒，将重复频率设置为 30Hz，请使用以下命令：

```
# kbdrate -d 200 -r 30
```

若不指定延时和重复频率，延时和重复频率将被重置为各自的默认值：延时 250ms，重复频率 11Hz：

```
# kbdrate
```

### Systemd 服务

可以使用 systemd 服务设置重复频率。例如：

```
/etc/systemd/system/kbdrate.service
```

```
[Unit]
Description=Keyboard repeat rate in tty.

[Service]
Type=oneshot
RemainAfterExit=yes
StandardInput=tty
StandardOutput=tty
ExecStart=/usr/bin/kbdrate --silent --delay 250 --rate 30

[Install]
WantedBy=multi-user.target
```

然后启动/启用 `kbdrate.service` systemd 服务。

## 9.4　Linux 控制台

*难度：进阶 ｜ 篇幅：15.1 KB ｜ 所属：Linux 控制台*

将要讲到：实现、键盘快捷键、字体、光标外观、HiDPI、声音提示

根据[维基百科](https://en.wikipedia.org/wiki/Linux_console)：

: **Linux 控制台**是 Linux 内核内部的一个系统控制台。Linux 控制台为内核和其他进程提供了一种向用户发送文本输出并接收用户文本输入的方式。用户通常用计算机键盘输入文本，并在计算机显示器上阅读输出的文本。Linux 内核支持虚拟控制台——在逻辑上独立的控制台，但它们访问相同的物理键盘和显示器。

本文介绍了 Linux 控制台的基础知识以及如何配置字体显示。/Keyboard configuration 子页面中描述了键盘配置。对于提供更多功能的替代控制台解决方案（完整的 Unicode 字体、现代图形适配器等），请参阅 [KMSCON](../终端模拟器/KMSCON.md) 或类似项目。

## 实现

与大多数直接与用户交互的服务不同，控制台是在内核中实现的。这与终端仿真软件形成鲜明对比，如 Xterm，它是一个在用户空间实现的普通的应用程序。控制台一直是发布的 Linux 内核的一部分，但在其历史上经历了一些变化，最明显的是过渡到使用 [framebuffer](https://en.wikipedia.org/wiki/Linux_framebuffer) 以及对 [Unicode](https://en.wikipedia.org/wiki/Unicode) 的支持。

虽然对控制台进行了许多改进，但它对旧硬件完全的向后兼容性意味着与图形终端仿真器相比它是受限的。Linux 控制台与图形终端仿真器的主要区别在于：Linux 控制台中的 shell 直接连接到 [TTY](Getty.md) 设备（`/dev/tty*`），而图形终端仿真器中的 shell 则连接到伪 TTY（`/dev/pty*`）。

此外，图形终端仿真器可以拥有比 Linux 控制台更多的功能，包括更丰富的可用字体集、多标签页/窗口、分屏视图、回滚缓冲区/滑块、背景颜色/图像（可选透明）等。其中一些功能可以通过终端多路复用器（如 [Tmux](../终端多路复用器/Tmux.md) 或 [GNU Screen](../../06_软件与应用/GNU/GNU%20Screen.md)）在 Linux 控制台中使用，或通过依赖 ncurses 等库的文本用户界面程序（TUI）实现，例如 [Vim](../文本编辑器/Vim.md)、[nano](../文本编辑器/Nano.md) 或 [Emacs](../Emacs/Emacs.md)。这些功能也可在图形终端仿真器中使用。

### 虚拟控制台

控制台以一系列[虚拟控制台](https://en.wikipedia.org/wiki/Virtual_console)的形式呈现给用户。这些虚拟控制台给人的印象是几个独立的终端在同时运行；每个虚拟控制台可以用不同的用户登录，运行自己的 shell，并有自己的字体设置。每个虚拟控制台都使用一个 `/dev/ttyX` 设备，可以通过按 `Alt+F*x*`（其中 `*x*` 是虚拟控制台编号，从 1 开始）在它们之间切换。`/dev/console` 设备会被自动映射到活动的虚拟控制台。

另请参阅 [chvt(1)](https://man.archlinux.org/man/chvt.1)、[openvt(1)](https://man.archlinux.org/man/openvt.1) 和 [deallocvt(1)](https://man.archlinux.org/man/deallocvt.1)。

### 文本模式

由于 Linux 最初是适用于 PC 硬件的内核，所以控制台是使用标准 IBM [CGA/EGA/VGA](https://en.wikipedia.org/wiki/VGA)图形开发的，当时所有 PC 都支持这种图形。图形在 VGA 文本模式下运行，它提供了一个简单的 16 色 80x25 字符显示。这种传统模式类似于专用文本终端的功能，例如 [DEC VT100](https://en.wikipedia.org/wiki/VT100) 系列。如果系统硬件支持的话，仍可以通过内核参数（`vga=0 nomodeset`）以文本模式启动，但几乎所有的现代发行版（包括 Arch Linux）都使用 framebuffer 控制台来代替。

### Framebuffer 控制台

随着 Linux 被移植到其他非 PC 架构上，需要一个更好的解决方案，因为其他架构不使用兼容 VGA 的图形适配器，而且可能根本不支持文本模式。framebuffer 控制台的实现是为了在所有平台上提供一个标准的控制台，因此无论底层的图形硬件如何，都会呈现相同的 VGA 风格界面。因此，Linux 控制台不是一个终端模拟器，它本身就是一个终端。它使用终端类型 `linux`，并与 VT100 基本兼容。

## 键盘快捷键

| 键盘快捷键 | 描述 |
| --- | --- |
| `Ctrl+Alt+Del` | 重新启动系统（由符号链接 `/usr/lib/systemd/system/ctrl-alt-del.target` 指定） |
| `Alt+F1`、`F2`、`F3`…… | 切换到第 *n* 个虚拟控制台（不同于 [Xorg 和 Wayland](../../04_硬件与外设/键盘/快捷键.md#Xorg_and_Wayland) 中使用的 `Ctrl+Alt+F*n*`） |
| `Alt+左箭头` | 切换到上一个虚拟控制台 |
| `Alt+右箭头` | 切换到下一个虚拟控制台 |
| `Scroll Lock` | 当 Scroll Lock 被激活时，输入/输出被锁定 |
| `Ctrl+c` | 杀死当前任务 |
| `Ctrl+d` | 插入 EOF |
| `Ctrl+z` | 暂停当前任务 |

参见 [console_codes(4)](https://man.archlinux.org/man/console_codes.4)。

## 字体

[Linux 控制台](https://en.wikipedia.org/wiki/Linux_console)默认使用 UTF-8 编码，但由于使用的是标准的兼容 VGA 的 framebuffer，终端字体限定为 256 或 512 个字形。如果字体超出了 256 个字形，那么颜色的数量就会从 16 减少到 8。为了为给定的 Unicode 值分配正确的符号来显示，需要一个特殊的翻译映射，通常称为 *unimap*。现在，大多数控制台字体都内置了 "unimap"；在历史上，它必须被单独加载。

默认情况下，[虚拟控制台](https://en.wikipedia.org/wiki/Virtual_console)使用内核的内置字体，其包含 [CP437](https://en.wikipedia.org/wiki/CP437) 字符集，但这个设置很容易改变。内核提供了约 15 种内置字体可供选择，其中[官方支持的内核](../../02_系统管理与内核/内核/内核.md#Officially_supported_kernels)提供两种：VGA 8x16 字体（`CONFIG_FONT_8x16`）和 Terminus 16x32 字体（`CONFIG_FONT_TER16x32`）。内核根据屏幕分辨率的评估结果选择使用的字体。可以通过[内核参数](../../02_系统管理与内核/内核/内核参数.md)强制使用其他内置字体，例如 `fbcon=font:TER16x32`。

[kbd](https://archlinux.org/packages/?name=kbd)包 包提供改变虚拟控制台字体和字体映射的工具。可用的字体存储在 `/usr/share/kbd/consolefonts/` 目录中，以 *.psfu* 或 *.psfu.gz* 结尾的字体内置 Unicode 翻译映射。

键盘映射是按键和计算机使用的字符的对应关系，位于 `/usr/share/kbd/keymaps/` 的子目录中，详情请参阅 /Keyboard configuration。

> **注意：**替换字体可能会导致需要标准 VGA 样式字体的程序出现问题，例如使用线条绘图图形的程序。

> **提示：**对于用拉丁/希腊字母书写的欧洲语言，可以使用 `eurlatgr` 字体，它包括广泛的拉丁/希腊字母变化以及特殊字符。

### 预览和测试

> **提示：**这里有一个整理过的预览影像数据库：[Linux 控制台字体截图](https://adeverteuil.github.io/linux-console-fonts-screenshots/)。

```
$ showconsolefont
```

显示字体可用字形与字符的列表。

如果指定（`/usr/share/kbd/consolefonts/` 中的）字体名称，`setfont` 将暂时改变字体：

```
$ setfont lat2-16 -m 8859-2
```

字体名称区分大小写。如果不加参数，`setfont` 会将控制台字体重设为默认字体。

因此，要使用一个**小的 8x8** 字体，在安装字体后，使用类似如下所示的命令：

```
$ setfont -h8 /usr/share/kbd/consolefonts/drdos8x8.psfu.gz
```

如果要使用**更大**的字体，Terminus 字体 ([terminus-font](https://archlinux.org/packages/?name=terminus-font)包) 有多种尺寸可供选择，例如 `ter-132b` 就很大。

你也可以添加 `-d` 选项来使用双倍大小。这将使用 64x64 字体：

```
$ setfont -d ter-132n
```

> **提示：**
>
> - 所有更改字体的命令都可以“盲打”。
> - Terminus 字体名称（`ter-*`）的解释见 `/usr/share/terminus-font/README`。

> **注意：***setfont* 只作用于当前正在使用的控制台。其它终端无论活跃与否都不受影响。

### 持久性配置

`/etc/vconsole.conf` 中的 `FONT` 变量可用于在启动时设置字体，这对于所有控制台都具有持久性作用。详情请参见 [vconsole.conf(5)](https://man.archlinux.org/man/vconsole.conf.5)。

要显示*Č, ž, đ, š*或*Ł, ę, ą, ś*之类的字符，请使用 `lat2-16.psfu.gz` 字体：

```
/etc/vconsole.conf
```

```
...
FONT=lat2-16
FONT_MAP=8859-2
```

这代表使用 ISO/IEC 8859 字符的第二部分，尺寸设置为 16。可以使用其它值来更改字体尺寸（如 `lat2-08`）。可以在 [zhwp:ISO/IEC 8859#各种ISO 8859字符集](https://zh.wikipedia.org/wiki/ISO/IEC_8859#.E5.90.84.E7.A7.8DISO_8859.E5.AD.97.E7.AC.A6.E9.9B.86)查询 8859 规范定义的区域。

自 [mkinitcpio v33](https://gitlab.archlinux.org/archlinux/mkinitcpio/mkinitcpio/-/blob/v33/mkinitcpio.conf#L52) 起，`/etc/vconsole.conf` 中指定的字体默认通过 `consolefont` 钩子在早期用户空间自动加载，该钩子将字体添加到 initramfs。详情请参见 [Mkinitcpio#钩子(HOOKS)](../../01_安装与包管理/Initramfs/Mkinitcpio.md#钩子(HOOKS))。

更改 `/etc/vconsole.conf` 后，你可能还需要重启 `systemd-vconsole-setup.service`。

如果启动时字体没有变化，或只暂时变化，则最可能是因为初始化图形驱动时字体被复位，然后控制台切换到 framebuffer。默认情况下，所有树内内核驱动程序都会提前加载。NVIDIA 用户应参阅 [NVIDIA#Early loading](../../05_桌面与图形/图形/NVIDIA.md#Early_loading) 以在应用 `/etc/vconsole.conf` 之前加载图形驱动程序。

## 光标外观

此主题文档较少，建议阅读以下文章：

- [Software cursor for VGA](https://docs.kernel.org/admin-guide/vga-softcursor.html)（VGA 的软件光标）
- [Cursor Appearance in the Linux Console](https://linuxgazette.net/137/anonymous.html)（Linux 控制台中的光标外观）
- [Disable Cursor Blinking on Linux Console](https://web.archive.org/web/20220318101402/https://nutr1t07.github.io/post/disable-cursor-blinking-on-linux-console/)（在 Linux 控制台上禁用光标闪烁）

<table class="wikitable"><caption>光标类型</caption><tbody><tr><th></th><th>硬件</th><th>软件</th></tr><tr><th>形状</th><td><dl><dd>(0) 默认<dl><dd>应用 (2) 下划线</dd></dl></dd><dd>(1) 不可见</dd><dd>(2) 下划线</dd><dd>(3) 下三分之一</dd><dd>(4) 下半部</dd><dd>(5) 下三分之二</dd><dd>(6) 全方块</dd></dl></td><td><dl><dd>(16) 全方块<dl><dd>形状无法更改，但当背景色和前景色相同时，光标实际上不可见</dd></dl></dd></dl></td></tr><tr><th>闪烁</th><td><dl><dd>闪烁</dd></dl></td><td><dl><dd>取决于驱动程序<dl><dd>背景色的最高位可解释为"高亮"或"闪烁"</dd></dl></dd></dl></td></tr><tr><th>颜色</th><td><dl><dd>与控制台文本颜色相同（通常为白色/灰色）<dl><dd>无法单独设置</dd></dl></dd></dl></td><td><dl><dd>可由用户设置</dd></dl></td></tr></tbody></table>

控制台光标可通过**设备属性（DA）**控制函数调整。参数序列**必须**以单个问号开头（尽管 [console_codes(4)](https://man.archlinux.org/man/console_codes.4) 有相反的说法）。

以下是一个全方块、不闪烁、绿色光标（光标下的符号为黑色）的示例：

```
$ printf "\x1b\x5b?16;$((8+4+2+1));$((32+0+8+4+2+1))\x63"
```

也可以使用八进制和字符代替十六进制代码表达：

```
$ printf '\033[?16;15;47c'
```

> **注意：**控制函数（转义序列）的完整指南请参阅标准 [ECMA-48 — Control Functions for Coded Character Sets](https://ecma-international.org/wp-content/uploads/ECMA-48_5th_edition_june_1991.pdf)。

相同的配置可通过[内核参数](../../02_系统管理与内核/内核/内核参数.md)永久应用：

```
vt.cur_default=0x2f0f10
```

> **注意：**转义序列参数（**16, 15, 47**）以十六进制（**10, 0f, 2f**）按**相反顺序**写入。

### 光标大小

第一个参数名为*光标大小*，数字 **16**（内核参数最右侧两位十六进制数字为 **10**）表示"*使用软件光标*"。

> **注意：**你可能需要使用 **48** (32+16)、**80** (64+16) 或 **112** (64+32+16) 代替 **16**。

如果想更改硬件光标形状，使用对应数字（从 **0** 到 **6**，见上表）。

> **注意：***光标大小*是唯一适用于*硬件*光标的参数。

### 切换掩码

第二个参数称为*切换掩码*，用于翻转颜色的对应位。

<table class="wikitable" style="text-align: center;"><caption>字符属性位</caption><tbody><tr><th></th><th colspan="4">背景<p><i>（光标块）</i></p></th><th colspan="4">前景<p><i>（光标下的符号）</i></p></th></tr><tr><th></th><td><b>高亮</b><p>或闪烁</p></td><td><b>红</b></td><td><b>绿</b></td><td><b>蓝</b></td><td><b>高亮</b><p>或闪烁</p></td><td><b>红</b></td><td><b>绿</b></td><td><b>蓝</b></td></tr><tr><th>十进制</th><td>128</td><td>64</td><td>32</td><td>16</td><td>8</td><td>4</td><td>2</td><td>1</td></tr><tr><th>十六进制</th><td>80</td><td>40</td><td>20</td><td>10</td><td>8</td><td>4</td><td>2</td><td>1</td></tr></tbody></table>

在我们的例子中，第二个参数是 **15**（内核参数中间两位十六进制数字为 **0f**），因此所有四个前景（符号）位都将被翻转。最重要的规则是：切换（第二个参数）在设置（第三个参数）之后应用。

> **注意：**如果某个位被第三个参数设置**且**被第二个参数切换——它实际上会被清除。

### 设置掩码

第三个参数称为*设置掩码*。它设置相应的字符属性位。我们在示例中使用 **47**（内核参数最左侧两位十六进制数字为 **2f**），这表示两点：

- (32) 为光标块使用纯绿色
- (8+4+2+1) 设置所有四个前景（符号颜色）位。这些位将被第二个参数切换，因此光标下的符号颜色将为黑色（`0000`）。

## HiDPI

参见 [HiDPI#Linux 控制台](../../05_桌面与图形/图形/HiDPI.md#Linux_控制台)。

## 声音提示

参见 [PC 扬声器#beep](../../04_硬件与外设/声音/PC%20扬声器.md#beep)。

## 参见

- [The TTY demystified – Linus Åkesson](https://www.linusakesson.net/programming/tty/)
