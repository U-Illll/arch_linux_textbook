# 第 10 章　GPD

> GPD 这几台小机器出厂预装的不是 Arch，机身小，装完系统只是开头，显示、键盘、休眠还得逐项调。本章按型号分开：认参数、装系统、补配置，一台走通即可对照另两台。
> 
> 先读《GPD MicroPC》，参数、安装、配置三节最短最全，正好当模板；《GPD Pocket》篇幅最长，参数与安装之后那节已知问题必读，末尾《参见》可跳过；《GPD Win》只讲修补与安装指南，务必装系统前翻，顺序反了要重来。最易犯的错是把一台做法套到另一台：同厂三型，参数不同，配置也不同；其次是先装再补，把修补当成事后补救。

<!-- 本章 3 节，共 19 KB -->

## 10.1　GPD MicroPC

*难度：入门 ｜ 篇幅：2.1 KB ｜ 所属：GPD*

将要讲到：参数、安装、配置

本文是关于[GPD MicroPC](http://softwincn.com/gpd_micropc)的说明。

## 参数

- 显示：6英寸 720x1280 （没错，侧过去了）
- CPU：Intel Gemini Lake N4100 4x 1.10GHz
- 内存：8GB LPDDR4-2133
- 存储：128GB M2-2242 SATA固态硬盘（可更换）
- 电池：6200mAh
- WiFi：Intel Dual Band Wireless-AC 3165
- LAN：Realtek RTL8168
- 音频：Intel 8086:3198
- 输入设备：QWERTY键盘、3个鼠标按键、触摸板、电源按钮、CPU风扇实体开关、复位键
- 接口：3个USB 3 type A、1个HDMI、1个USB 3 type C、1个microSDXC读卡器、1个RJ45以太网口、1个DB9（RS232）串口、1个3.5mm耳机插孔

USB-C接口用于充电，支持PD 2.0但也兼容5V USB充电器

## 安装

请确保安装映像Linux内核至少为5.1版本。

## 配置

### 内核模块

为使启动时键盘能够工作，将电池（battery）加入到预加载的模块中：

```
/etc/mkinitcpio.conf
```

```
...
MODULES=(battery)
...
```

### 启动时的屏幕旋转

为使启动时屏幕方向正确，将fbcon=rotate:1加入引导加载器配置：

```
/boot/loader/entries/arch.conf
```

```
...
options root=/dev/mapper/crypt cryptdevice=UUID=000ccc23-4223-0ccc-4223-deadbeaf2342:btrfs rw fbcon=rotate:1
...
```

### wayland

屏幕位于DSI-1，被旋转了90°，所以需要进行配置：

```
~/.config/sway/config
```

```
...
# configure display
# get the names of your outputs by: swaymsg -t get_outputs
output DSI-1 resolution 720x1280 transform 90
...
```

## 10.2　GPD Win

*难度：进阶 ｜ 篇幅：6.9 KB ｜ 所属：GPD*

将要讲到：修补、安装指南

[GPD Win](http://softwincn.com/gpd_win_2)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2022-09-17 ⓘ\]是一款小巧的（5.5英寸屏幕）手持设备。

## 修补

**没有在下面提及的功能应当是直接可用的。**

### 内置Wi-Fi

在[Linux Bug 185661](https://bugzilla.kernel.org/show_bug.cgi?id=185661)得到解决之前，需要进行修补才能使内置Wi-Fi工作。目前的解决办法是从[此处](https://chromium.googlesource.com/chromiumos/third_party/linux-firmware/+/f151f016b4fe656399f199e28cabf8d658bcb52b/brcm/brcmfmac4356-pcie.txt)获取 **brcmfmac4356-pcie.txt**（中国大陆地区访问可能需要代理工具），然后将其放在**/lib/firmware/brcm**，再重新加载**brcmfmac**模块。

> **注意：**网页右下角的“txt”按钮不会下载正确的文件。你需要手动将内容复制粘贴至brcmfmac4356-pcie.txt。

在Arch Linux安装程序中让内置Wi-Fi工作，最简单的方法是：在Windows 10下将上述文件下载到C:\\。然后在安装程序中进行以下操作：

创建一个目录并挂载Windows 10分区（**将下面的mmcblk0p2换成你的Windows 10分区，可以通过运行lsblk找到**）

```
mkdir windows
mount /dev/mmcblk0p2 windows
```

复制文件

```
cp windows/brcmfmac4356-pcie.txt /lib/firmware/brcm
```

重新加载模块

```
modprobe -r brcmfmac
modprobe brcmfmac
```

连接到Wi-Fi

```
wifi-menu
```

> **注意：**如果Wi-Fi看起来不能用，可能需要在BIOS中进行一项附加设置。重启并在设备启动同时按住Del键，在BIOS画面中进入芯片组（Chipset）选项卡，选择南桥（South Bridge），之后是LPSS & SCC配置（LPSS & SCC Configuration），将SCC SDIO支持（SCC SDIO Support）切换到PCI模式（PCI Mode）。保存BIOS更改，回到复制文件步骤再试一次。

### 电池监控

从内核4.12开始直接可用。更早版本的内核需要[Hans de Goede的带补丁内核](https://hansdegoede.livejournal.com/17445.html)，他的内核也会修复开机后插电源线不充电以及只能以500mA慢速充电的问题。

### 旋转X会话

由于该设备使用了一块手机屏幕，显示画面需要旋转才能正常。从内核4.9.2开始，旋转X会话（通过[xrandr](../../05_桌面与图形/Xorg%20命令/Xrandr.md)手动设置或在桌面环境的设置中操作）直接可用。（注：不同批次设备之间可能存在差异，原作者的GPD Win运行4.9.11-1内核未能解决屏幕旋转问题）

如果使用GNOME时发现所有显示内容被过度放大，运行以下命令恢复缩放：

```
$ gsettings set org.gnome.desktop.interface scaling-factor 1
```

### 旋转触摸屏

从内核4.9.2开始，旋转X会话就会自动使触摸屏旋转（迄今为止，只测试过gnome-control-center）。

> **注意：**Gnome会应用正确的触摸屏方向，即使在使用arandr等第三方应用程序改变显示方向时也是如此。而截至2018-02-16，XFCE并不会自动旋转触摸坐标系。

如果你的系统没有自动旋转触摸屏，运行以下命令可能会有用，但结果似乎因人而异。

```
xinput set-prop 'Goodix Capacitive TouchScreen' 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1
```

### 修复在使用全核时的偶发崩溃问题

该CPU的睿频是由软件控制的，而Linux核心（4.4）无法将CPU频率降到足够低的程度。CPU会一直运行在2.4Ghz或接近的频率，即使全部4个核心活跃也是如此，最终会导致CPU崩溃。禁用睿频功能（BIOS中的“Turbo Mode”）可修复此问题。更多信息见[此处](https://www.reddit.com/r/gpdwin/comments/5o2m1v/solved_linux_44_crashing_when_using_all_4_cores/)。

### 声音

在使用最新内核以及[linux-lts](https://archlinux.org/packages/?name=linux-lts)包内核的情况下直接可用。耳机插孔可用，但目前需要[Hans de Goede的带补丁内核](https://hansdegoede.livejournal.com/17445.html)。

### 存储卡读卡器

在使用最新内核以及[linux-lts](https://archlinux.org/packages/?name=linux-lts)包内核的情况下直接可用。

### 实体电源与音量键

可用，但目前需要[Hans de Goede的带补丁内核](https://hansdegoede.livejournal.com/17445.html)。

### 调整屏幕亮度

从内核4.14开始直接可用。更早版本的内核需要[Hans de Goede的带补丁内核](https://hansdegoede.livejournal.com/17445.html)。

### 挂起、恢复以及开盖时唤醒

从内核4.14开始直接可用。更早版本的内核需要[Hans de Goede的带补丁内核](https://hansdegoede.livejournal.com/17445.html)。

## 安装指南

### 启动安装器

重启时按住**Del**或**Esc**键以进入BIOS。在BIOS画面中，修改启动顺序将U盘设为优先，或者选择单次从U盘启动。

在Arch Linux引导选项画面，高亮选中第一项，按**e**键编辑引导选项并添加**“fbcon=rotate:1”**使画面能够转到正确的方向。

### 格式化并挂载分区，以与Windows 10共存

使用Windows中的“磁盘管理”或类似工具缩小Windows 10分区，并使用空闲空间为Arch Linux建立一个新分区。

运行lsblk来列出分区，注意以下分区的编号：

- （**X**） Windows引导加载器，一个100MB的分区
- （**Y**） Windows 10分区
- （**Z**） 新的Linux分区

格式化并挂载新的Linux分区

```
mkfs.ext4 /dev/mmcblk0pZ
mount /dev/mmcblk0pZ /mnt
```

创建boot目录并挂载Windows引导加载器分区

```
mkdir /mnt/boot
mount /dev/mmcblk0pX /mnt/boot
```

### 安装Arch Linux

安装基本系统

```
pacstrap -i /mnt base base-devel
```

将Wi-Fi补丁复制到新安装的系统

```
cp /lib/firmware/brcm/brcmfmac4356-pcie.txt /mnt/lib/firmware/brcm
```

接下来按照[通常安装指南](../../01_安装与包管理/安装过程/安装指南.md#配置系统)进行，到安装引导程序时再按照以下说明操作。

### 安装引导程序

安装引导程序

```
bootctl install
```

创建并填写**/boot/loader/entries/arch.conf**

> **注意：**如果使用[linux-lts](https://archlinux.org/packages/?name=linux-lts)包内核，请将**/vmlinuz-linux**改成**/vmlinuz-linux-lts**，**/initramfs-linux.img**改成**/initramfs-linux-lts.img**

```
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options fbcon=rotate:1 root=/dev/mmcblk0pZ rw
```

**GPD Win上Arch Linux的安装到此完成。**

## 10.3　GPD Pocket

*难度：进阶 ｜ 篇幅：10.3 KB ｜ 所属：GPD*

将要讲到：参数、安装、配置、已知问题、参见

本文是关于[GPD Pocket](http://softwincn.com/gpdpocket2)的说明。

## 参数

- 显示屏: 7英寸IPS 1920x1200
- CPU: Intel Atom X7-Z8750
- RAM: 8GB LPDDR3-1600
- 存储: 128GB eMMC SSD（不可更换）
- 电池: 7000mAh
- WiFi: Broadcom 4356 802.11ac
- 蓝牙: Broadcom 2045
- 音频: Realtek ALC5645
- 接口: 1 x USB 3 type A, 1 x MicroHDMI, 1 x USB 3 type C, 1 x 3.5mm 耳机插孔

## 安装

### 自动

可以从[这里](https://github.com/sigboe/GPD-ArchISO/releases)下载预先打好补丁的ISO映像。

### 手动

由于WiFi在默认配置下无法工作，需要首先解决WiFi问题(见[#WiFi](#WiFi))，或使用受支持的USB外置以太网/WiFi网卡。

## 配置

### 自动

安装期间请将以下内容加入/etc/pacman.conf：

```
/etc/pacman.conf
```

```
...
[gpd-pocket-arch]
SigLevel = Never
Server = https://github.com/joshskidmore/gpd-pocket-arch/raw/master
...
```

运行以下命令，安装GPD Pocket正常运行Arch所需要的更改：

`# pacman -Syu gpd-pocket-support`

由于alsa-lib的补丁是可选依赖，必须手动安装它才能让音频工作：

`# pacman -S gpd-pocket-alsa-lib`

### 手动

#### WiFi

安装软件包[gpd-pocket-support-bcm4356-git](https://aur.archlinux.org/packages/gpd-pocket-support-bcm4356-git/)AUR并重新加载WiFi内核模块:

```
# modprobe -r brcmfmac
# modprobe brcmfmac
```

#### 背光与KMS

修改`/etc/mkinitcpio.conf`如下，以通过KMS早启动来启用背光控制：

```
/etc/mkinitcpio.conf
```

```
...
MODULES=(pwm_lpss pwm_lpss_platform i915)
...
```

#### Wayland

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** Wayland和Xorg节都提到旋转触摸屏，但似乎Wayland节只提到了旋转触控输入，Xorg节只提到了旋转图形界面显示，故需要进一步核实，看是否两项操作都需要进行，不论图形后端为何。（在 [Talk:GPD Pocket](https://wiki.archlinuxcn.org/wiki/Talk:GPD_Pocket) 中讨论）

##### 基本配置

创建`/etc/udev/rules.d/99-goodix-touch.rules`文件，并在其中填入如下内容以旋转触摸屏：

```
ACTION=="add|change", KERNEL=="event[0-9]*", ATTRS{name}=="Goodix Capacitive TouchScreen", ENV{LIBINPUT_CALIBRATION_MATRIX}="0 1 0 -1 0 1"
```

##### 右键模拟滚动

与Xorg中可以通过标准Xorg配置文件启用右键模拟滚动不同，在Wayland中此类配置应当是由混成器提供的；但不幸的是，一些混成器（如GNOME Wayland）并没有正确地提供这些配置接口。不过相应的功能仍然可以通过`libinput`实现。由于这些混成器通常加载`/etc/profile.d`，可以使用`LD_PRELOAD`挂钩至`libinput`从而强制应用这些配置。

该方法的示例实现见[此处](https://github.com/PeterCxy/scroll-emulation)。

#### Xorg

##### 基本配置

创建`/etc/X11/xorg.conf.d/30-monitor.conf`以旋转触摸屏：

> **注意：**标识符（Identifier）可能因你所选择的显示驱动而不同（`DSI-1` (modesetting)或`DSI1` (xf86-video-intel))

```
/etc/X11/xorg.conf.d/30-monitor.conf
```

```
Section "Monitor"
  Identifier "DSI-1"
  Option     "Rotate" "right"
EndSection
```

##### Gnome与GDM

编辑`~/.config/monitors.xml`（默认状态下可能不存在该文件）：

```
~/.config/monitors.xml
```

```
<monitors version="2">
  <configuration>
    <logicalmonitor>
      <x>0</x>
      <y>0</y>
      <scale>2</scale>
      <primary>yes</primary>
      <transform>
        <rotation>right</rotation>
        <flipped>no</flipped>
      </transform>
      <monitor>
        <monitorspec>
          <connector>DSI-1</connector>
          <vendor>unknown</vendor>
          <product>unknown</product>
          <serial>unknown</serial>
        </monitorspec>
        <mode>
          <width>1200</width>
          <height>1920</height>
          <rate>60.384620666503906</rate>
        </mode>
      </monitor>
    </logicalmonitor>
  </configuration>
</monitors>
```

这样即可设置正确的旋转方向（`<rotation>right</rotation>`）以及缩放因子2（`<scale>2</scale>`）。关于非整数倍缩放，见[HiDPI#GNOME](../../05_桌面与图形/图形/HiDPI.md#GNOME)。

对于[GDM](../../05_桌面与图形/GNOME/GDM.md)，将以上的`~/.config/monitors.xml`文件复制到`/var/lib/gdm/.config/monitors.xml`以设置正确的旋转方向。

##### KDE

在*系统设置* > *显示和监控*中，将*方向*改为*90°顺时针*，并调整*缩放显示*至合适尺寸。

##### 右键模拟滚动

创建`/etc/X11/xorg.conf.d/50-trackpoint.conf`从而实现按住右键来滚动屏幕：

```
/etc/X11/xorg.conf.d/50-trackpoint.conf
```

```
Section "InputClass"
  Identifier      "GPD trackpoint"
  MatchProduct    "SINO WEALTH Gaming Keyboard"
  MatchIsPointer  "on"
  Driver          "libinput"
  Option          "MiddleEmulation" "1"
  Option          "ScrollButton" "3"
  Option          "ScrollMethod" "button"
EndSection
```

##### SDDM

为了改变DPI使文字可读，将以下行加入`/usr/share/sddm/scripts/Xsetup`：

```
/usr/share/sddm/scripts/Xsetup
```

```
# Set DPI
xrandr --dpi 168"
```

##### 触摸屏手势

安装[touchegg](https://aur.archlinux.org/packages/touchegg/)AUR，然后编辑`/usr/share/touchegg/touchegg.conf`中的下列行：

```
/usr/share/touchegg/touchegg.conf
```

```
...
<action type="SCROLL">SPEED=7:INVERTED=true</action>
...
```

创建`/etc/X11/xinit/xinitrc.d/01_touchegg`：

```
/etc/X11/xinit/xinitrc.d/01_touchegg
```

```
...
#!/bin/bash

# starts touchegg application
PREFIX="$HOME/.config/touchegg/.run"
mkdir -p "$PREFIX"
PIDFILE="$PREFIX/touchegg.$USER$DISPLAY.pid"
LOCK="$PREFIX/touchegg.$USER$DISPLAY.lock"

start_touchegg() {
        (
                flock -n 9 || exit 1
                touchegg 2>/dev/null >/dev/null &
                PID=$!
                echo "$!" >"$PIDFILE"
                wait $PID
        ) 9>"$LOCK"
}

start_touchegg &
```

对`/etc/X11/xinit/xinitrc.d/01_touchegg`设置权限：

```
# chmod 0755 /etc/X11/xinit/xinitrc.d/01_touchegg
```

##### 利用Pocket 2上的“半/全”键

出厂状态下`半/全`被设定为发送`` ` ``符号及按键码49，导致其与原本的`` `/~ ``键形成重复。为了把它映射到更有用的功能，请前往[GPD2 Firmware](https://www.gpd.hk/gpdp2firmware)然后下载"GPD Pocket 2 Keyboard Firmware (Japan)"（中国大陆地区下载可能需使用代理工具）。此固件必须在Windows 10操作系统（设备原装的）下刷入。由于更新期间键盘和鼠标无法使用，建议使用USB鼠标。

更新完成后`半/全`会发送按键码49及`` ` ``符号，而`` `/~ ``键会发送按键码132。创建`~/.Xmodmap`文件以进行修正：

```
~/.Xmodmap
```

```
keycode  132 = grave asciitilde
keycode  49  = XF86Launch1 NoSymbol
```

现在进行测试：

```
$ xmodmap ~/.Xmodmap
```

更多信息请参阅[Xmodmap](../输入设备/Xmodmap.md)

#### 风扇

使用最新内核时风扇应当直接可以工作。

> **注意：**如果你的风扇有问题，未能按期望的方式运转，请尝试以下方法：

```
# modprobe -r gpd-pocket-fan
# modprobe gpd-pocket-fan temp_limits=40000,40001,40002
```

当以上操作完成之后——应当能够在40℃时听到风扇起转，如果听到了咔哒声——关闭设备电源，卸下后盖，之后用手轻轻转几圈风扇。然后重新装上后盖并开机，登录之后再次运行上面的命令。部分设备似乎存在问题，风扇一段时间没有通电之后无法正确地启动。

一旦完成了以上步骤并且风扇工作正常，应当重启系统或重新加载风扇内核模块，从而将温度阈值恢复到默认：

```
# modprobe -r gpd-pocket-fan
# modprobe gpd-pocket-fan
```

> **注意：**默认状态下，连接交流电源时风扇会一直转动。要更改这一行为，请在[内核参数](../../02_系统管理与内核/内核/内核参数.md)中添加`gpd-pocket-fan.speed_on_ac=0`。

#### 节能

安装[tlp](https://archlinux.org/packages/?name=tlp)包，然后编辑`/etc/default/tlp`中的下列行：

```
/etc/default/tlp
```

```
...
# improve disk IO
DISK_DEVICES="mmcblk0"
DISK_IOSCHED="deadline"
...
# disable wifi power saving mode (wifi speed drops MASSIVELY!)
WIFI_PWR_ON_AC=off
WIFI_PWR_ON_BAT=off
...
```

#### PulseAudio

将下列行加入`/etc/pulse/default.pa`：

```
/etc/pulse/default.pa
```

```
set-card-profile alsa_card.platform-cht-bsw-rt5645 HiFi
set-default-sink alsa_output.platform-cht-bsw-rt5645.HiFi__hw_chtrt5645_0__sink
set-sink-port alsa_output.platform-cht-bsw-rt5645.HiFi__hw_chtrt5645_0__sink [Out] Speaker
```

编辑`/etc/pulse/daemon.conf`以关闭实时调度：

```
/etc/pulse/daemon.conf
```

```
realtime-scheduling = no
```

#### 充电控制

可以控制充电电流、充电终止电压及一些其他设定。

更多信息及示例脚本见[这篇reddit帖子](https://reddit.com/r/GPDPocket/comments/cfyekh/limit_battery_charge/eudbwto)（中国大陆地区访问可能需使用代理工具）。

## 已知问题

### USB-C电源状态

USB-C电源状态在内核版本4.14-15下不工作。Hans的内核有修复此问题的补丁。

### systemd-gpt-auto-generator failed to dissect

由于[此原因](https://github.com/systemd/systemd/issues/5806)，启动时会出现报错信息：

`systemd-gpt-auto-generator: Failed to dissect: Input/output error`.

向启动引导器添加以下启动参数，可避免该信息出现。

```
systemd.gpt_auto=0
```

## 参见

- [https://github.com/njkli/gpd-pocket/](https://github.com/njkli/gpd-pocket/)
- [https://hansdegoede.livejournal.com/](https://hansdegoede.livejournal.com/)
