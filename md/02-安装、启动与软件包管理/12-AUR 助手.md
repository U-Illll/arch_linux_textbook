# 第 12 章　AUR 助手

> 这一章解决一件事：官方仓库没有、只在 AUR 里的软件，怎么用 yay 装起来，装完又怎么继续管。全章只有《Yay》一节，安装部分交代 yay 从哪儿来、怎么装到位，命令部分列出日常真正用得上的那些。
> 
> 单节即全部内容，没有可跳的分支，两块都建议读完；命令不必背，看过一遍留在手边当速查即可。最容易踩的坑是把"装上了 yay"当成"装好了包"：它只是把下载、构建、安装串成一条命令，结果对不对仍取决于构建脚本和依赖；另一个误解是以为经它装上的软件与官方仓库的包等价，AUR 的内容并没有人替你审过。

<!-- 本章 1 节，共 3 KB -->

## 12.1　Yay

*难度：入门 ｜ 篇幅：3.4 KB ｜ 所属：AUR 助手*

将要讲到：安装、命令

[Yay](https://github.com/Jguer/yay) 是一个适用于Arch Linux的命令行软件，主要用于帮助用户从Arch User Repository([AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md))构建和安装软件包。它可以做的事情包括：

1.  自动解决软件包间的依赖关系。
2.  动态地搜索、编译和构建包。
3.  安装与管理 AUR中的包（ yay 的命令与 pacman 基本一致，例如 yay -S 对应 pacman -S，但 yay 能同时处理官方仓库和 AUR。 ）。

> **警告：**Arch Linux 不对 AUR 助手引发的问题提供支持。您应熟悉[自行构建过程](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md#安装与更新软件包)以准备好解决遇到的问题。

## 安装

在开始之前，请确保您已安装 [base-devel](https://archlinux.org/packages/?name=base-devel)包 [git](https://archlinux.org/packages/?name=git)包

安装base-devel, git：

```
# pacman -S git base-devel
```

克隆 [yay](https://aur.archlinux.org/yay.git) 源码：

```
$ git clone https://aur.archlinux.org/yay.git
```

如果你在中国构建yay，强烈建议你克隆 [yay-bin](https://aur.archlinux.org/yay-bin.git) 而非 [yay](https://aur.archlinux.org/yay.git)。这是由于克隆和构建 `yay-bin` 相比 `yay` 不会遇到被墙问题，克隆 `yay-bin` 构建yay是因为 `yay` 是以Go语言构建的yay，可能会受到被墙影响：

```
$ git clone https://aur.archlinux.org/yay-bin.git
$ cd yay-bin
```

进入yay文件夹：

```
$ cd yay
```

构建 yay 并在构建成功后安装生成的软件包：

```
$ makepkg -si
```

如果您想一次完成所有操作，请使用以下命令：

```
$ sudo pacman -S git base-devel && git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si
```

如果不想编译安装，也可以添加 [\[archlinuxcn\]](../非官方用户仓库/Arch%20Linux%20中文社区仓库.md) 仓库来安装 [yay](https://github.com/archlinuxcn/repo/tree/master/archlinuxcn/yay)[CNRepo](../非官方用户仓库/Arch%20Linux%20中文社区仓库.md)。

## 命令

| 命令 | 描述 |
| --- | --- |
| yay | 升级系统，相当于yay -Syu |
| yay <搜索词> | 显示包安装选择菜单 |
| yay -Bi <目录> | 安装依赖并构建本地PKGBUILD |
| yay -G <AUR Package> | 从ABS或AUR下载PKGBUILD (yay v12.0+) |
| yay -Gp <AUR Package> | 打印ABS或AUR的PKGBUILD到stdout |
| yay -Ps | 打印系统统计信息 |
| yay -Syu --devel | 执行系统升级，但同时检查开发包的更新 |
| yay -Syu --timeupdate | 执行系统升级并使用PKGBUILD修改时间（不是版本号）来确定更新 |
| yay -Wu <AUR Package> | 取消对包的投票 (需要设置AUR_USERNAME和AUR_PASSWORD环境变量) (yay v11.3+) |
| yay -Wv <AUR Package> | 投票支持包 (需要设置AUR_USERNAME和AUR_PASSWORD环境变量) (yay v11.3+) |
| yay -Y --combinedupgrade --save | 使组合升级成为默认模式 |
| yay -Y --gendb | 生成用于开发更新的开发包数据库 |
| yay -Yc | 清理不需要的依赖 |
| yay -S 包名 | 安装软件包 |
| yay -Syu | 升级所有包（含 AUR） |
| yay -Sc | 清理缓存文件 |
| yay -Rns 包名 | 删除软件包和依赖及配置 |
| yay -Rs 包名 | 删除软件包和依赖 |
| yay -R 包名 | 删除软件包 |

更多命令请通过 man yay 查看

如果发现命令介绍有错误请帮忙修改
