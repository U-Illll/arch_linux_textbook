# 第 21 章　Xorg commands

> Xcalc 是 Xorg 自带的最小图形客户端，这一章只做三件事：装上它、按习惯配好、说清它能验证什么。它解决的问题很小：图形栈刚搭好、心里没底时，用一个依赖极少、行为好判断的程序，看显示连接与窗口能否起来。整章只有《Xcalc》一节，无可跳过之处，按安装、配置、参见的顺序读即可；只想确认图形环境跑得通，读完安装就够了。
> 
> 最容易踩的坑是当成能干活的计算器——它只管演示与验证，功能精度都不值得依赖。其次是配置改完看不到变化：设置要等客户端重启才读取，不重开等于没改。还有显示环境没连上时它启动即失败，报错很像没装好，先查图形环境再怀疑安装。

<!-- 本章 1 节，共 5 KB -->

## 21.1　Xcalc

*难度：入门 ｜ 篇幅：4.7 KB ｜ 所属：Xorg commands*

将要讲到：安装、配置、参见

**xcalc** 是 [X Window System](../X%20服务器/Xorg.md) 的[科学计算器](https://en.wikipedia.org/wiki/Scientific_calculator)。

## 安装

安装 [xorg-xcalc](https://archlinux.org/packages/?name=xorg-xcalc)包 软件包。

## 配置

你可以在 [X resources](../../01_安装与包管理/配置文件/X%20资源.md) 文件中设置几个选项，让 xcalc 看起来比默认的白底黑字更好看。每个按钮的位置、标签、功能和颜色都可以单独更改。更多信息，请参见 [xcalc(1) § CUSTOMIZATION](https://man.archlinux.org/man/xcalc.1#CUSTOMIZATION)。

### 主题

xcalc 有一个内置主题，模仿 [Texas Instruments TI-30](https://en.wikipedia.org/wiki/TI-30) 的外观。如何启用该主题，请参见 [xcalc(1) § COLORS](https://man.archlinux.org/man/xcalc.1#COLORS)。

如果你只是想要一些更漂亮的颜色，可以试试这个主题。

```
~/.Xresources
```

```
XCalc.geometry:                        200x275
XCalc.stipple:                         true
XCalc.ti.Command.background:           #777777
XCalc.ti.Command.foreground:           White
XCalc.ti.bevel.background:             #111111
XCalc.ti.bevel.screen.DEC.background:  #000000
XCalc.ti.bevel.screen.DEC.foreground:  LightSeaGreen
XCalc.ti.bevel.screen.DEG.background:  #000000
XCalc.ti.bevel.screen.DEG.foreground:  LightSeaGreen
XCalc.ti.bevel.screen.GRAD.background: #000000
XCalc.ti.bevel.screen.GRAD.foreground: LightSeaGreen
XCalc.ti.bevel.screen.HEX.background:  #000000
XCalc.ti.bevel.screen.HEX.foreground:  LightSeaGreen
XCalc.ti.bevel.screen.INV.background:  #000000
XCalc.ti.bevel.screen.INV.foreground:  Red
XCalc.ti.bevel.screen.LCD.background:  #000000
XCalc.ti.bevel.screen.LCD.foreground:  LightSeaGreen
XCalc.ti.bevel.screen.LCD.shadowWidth: 0
XCalc.ti.bevel.screen.M.background:    #000000
XCalc.ti.bevel.screen.M.foreground:    LightSeaGreen
XCalc.ti.bevel.screen.OCT.background:  #000000
XCalc.ti.bevel.screen.OCT.foreground:  LightSeaGreen
XCalc.ti.bevel.screen.P.background:    #000000
XCalc.ti.bevel.screen.P.foreground:    Yellow
XCalc.ti.bevel.screen.RAD.background:  #000000
XCalc.ti.bevel.screen.RAD.foreground:  LightSeaGreen
XCalc.ti.bevel.screen.background:      #000000
XCalc.ti.button4.background:           Orange3
XCalc.ti.button5.background:           Red4
XCalc.ti.button23.background:          #611161
XCalc.ti.button24.background:          #611161
XCalc.ti.button35.background:          #611111
XCalc.ti.button37.background:          #222262
XCalc.ti.button38.background:          #222262
XCalc.ti.button39.background:          #222262
XCalc.ti.button40.background:          #722222
XCalc.ti.button42.background:          #333373
XCalc.ti.button43.background:          #333373
XCalc.ti.button44.background:          #333373
XCalc.ti.button45.background:          #833333
XCalc.ti.button47.background:          #444484
XCalc.ti.button48.background:          #444484
XCalc.ti.button49.background:          #444484
XCalc.ti.button50.background:          #944444
XCalc.ti.button52.background:          #555595
XCalc.ti.button53.background:          #555595
XCalc.ti.button54.background:          #555595
XCalc.ti.button55.background:          #a55555
```

### 更改分部标签

如果您愿意，可以将分隔符改为正斜线。

```
XCalc.ti.button35.label: /
```

### 桌面项

xcalc 不会显示在应用程序菜单中，因为它没有[桌面项](../../01_安装与包管理/配置文件/桌面项.md)。要让它出现在菜单中，可以添加一个桌面项。

```
~/.local/share/applications/xcalc.desktop
```

```
[Desktop Entry]
Name=XCalc
GenericName=Scientific Calculator
Comment=Scientific calculator for X
Exec=xcalc
Icon=accessories-calculator
Terminal=false
Type=Application
StartupNotify=true
Keywords=calculator;scientific;
Categories=Utility;Calculator;
```

在 *Utility > Calculator*应用程序列表中，菜单项将显示为 *XCalc*。

## 参见

- [更改 xcalc 上的颜色](https://bbs.archlinux.org/viewtopic.php?id=85918)
- [Xcalc 主题的神奇之处](https://forums.bunsenlabs.org/viewtopic.php?id=3007)
- [xcalc 打扮得花枝招展！](https://oldforum.puppylinux.com/viewtopic.php?t=83837)
