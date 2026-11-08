# 第 1 章　Arch 打包准则

> 本章解决"同一个上游软件，包该叫什么、版本怎么写、依赖怎么填、文件放哪里"这四类规范，以及各语言与构建系统的例外。建议先读同名那节打包准则，把命名、版本号、依赖、关联与软件源过一遍，再读安全那一节；Python、CMake、Go、Rust、Java 按项目挑着看，其余章节等遇上再翻。
> 
> 最容易的误解是把语言专章当成可有可无的附录：KDE 要强制指定 Qt 版本，Electron 要先决定是否复用系统 electron，VCS 包的版本必须由 pkgver() 生成。漏掉这些，编译虽过，包名、依赖与文件位置却不合准则。

<!-- 本章 21 节，共 162 KB -->

## 1.1　PHP 打包准则

*难度：入门 ｜ 篇幅：2.7 KB ｜ 所属：Arch 打包准则*

将要讲到：软件包名称、打包文件的位置、体系架构

本文档介绍了为 PHP 库创建 [PKGBUILD](../包开发/PKGBUILD.md) 的过程。本文档的目标读者是 PHP 库的打包人员。对于 PHP Web 应用程序，请参阅 Web application package guidelines

## 软件包名称

对于模块，软件包名称应以 `php-` 开头，其余名称应通过将库名称转换为小写字母并用连字符分隔来从库名称构造。 例如，与 `File iterator` 相对应的软件包名称将是 `php-file-iterator`。

## 打包文件的位置

PHP 软件包应将文件安装到 `/usr/share/php/` 中。此路径应位于 php.ini include_path 或 open_basedir 指令中，以便能够在 PHP Web 应用程序中包含库文件。

## 体系架构

在大多数情况下，`arch` 数组应包含 `'any'` ，因为大多数 PHP 软件包都是与体系架构无关的。

## 1.2　32 位软件包打包准则

*难度：入门 ｜ 篇幅：3.2 KB ｜ 所属：Arch 打包准则*

将要讲到：软件包命名、变量和参数

可以在不同架构的计算机（例如 [x86_64](https://en.wikipedia.org/wiki/x86_64)）上构建和安装老式 [32 位](https://en.wikipedia.org/wiki/32-bit)软件。本文介绍了此类软件包的打包和约定。

## 软件包命名

- 给 [32 位版本](https://aur.archlinux.org/packages/?O=0&K=lib32-)的软件包加上前缀 [lib32-](#lib32)。

- 给使用 [x32 ABI](https://en.wikipedia.org/wiki/x32_ABI) 的[软件包](https://aur.archlinux.org/packages/?O=0&K=-x32)加上后缀 `-x32`。

- [软件包描述](../包开发/PKGBUILD.md#pkgdesc)应与原生版本的相区别，即 `pkgdesc+=" (32-bit)"`。

## 变量和参数

### lib32

在 [PKGBUILD](../包开发/PKGBUILD.md) 中指定这些 [bash](../../07_开发与工具/命令行解释器/Bash.md) 变量来告诉编译器输出 32 位代码：

```
export CFLAGS+=" -m32"
export CXXFLAGS+=" -m32"
export LDFLAGS+=" -m32"
export PKG_CONFIG_PATH='/usr/lib32/pkgconfig'
```

#### 文件放置

确保 lib32 软件包文件与本机软件包文件不冲突，并且包括所有必需的文件，例如特定于架构的包含文件。例如，如果使用 [GNU Autoconf](https://en.wikipedia.org/wiki/Autoconf)构建软件包，则向 `configure` 指定以下内容：

```
--program-suffix="-32" \
--lib{exec,}dir=/usr/lib32 \
--includedir=/usr/include/"$pkgbase"32 \
--build=i686-pc-linux-gnu
```

## 1.3　Free Pascal 打包准则

*难度：入门 ｜ 篇幅：3.6 KB ｜ 所属：Arch 打包准则*

将要讲到：Free Pascal

本页说明如何为使用 [Free Pascal Compiler (FPC)](https://www.freepascal.org/) [PKGBUILD](../包开发/PKGBUILD.md)。当前存在用于构建 Linux 软件的两个选项，以及使用 FPC 交叉编译器在其他目标上构建软件的少数选项：

- [fpc](https://archlinux.org/packages/?name=fpc)包 提供了仅针对您的主机 CPU（仅x86_64）的编译器。

## Free Pascal

### 软件包命名

仅项目名称通常就足够了。但是，在交叉编译的情况下，当从 multilib 以 i686 Linux 为目标时，包的前缀应该是 `fpc32-` ，当以非 Arch Linux 系统为目标时，包的名称应该是 `fpc-*cpu*-*system*-*pkgname*`。

### 有用的代码片段

- 确定 FPC 的版本以及要输出的单元的 CPU 和 OS：

```
_unitdir=`fpc -iSP`-`fpc -iSO`
_fpcver=`fpc -iV`
```

### 打包

制作基于 FPC 的软件包时，请遵守以下规定：

- 始终将 [fpc](https://archlinux.org/packages/?name=fpc)包 添加到 `makedepends` 或 `depends`
- 始终将所有编译单元（\*.a, \*.compiled, \*.o, \*.ppu, \*.res, \*.rst) ）放在 `/usr/lib/fpc/*$_fpcver*/units/*$arch*-linux` 下面。
- 如果安装导入库，请将 `staticlibs` 添加到 `options` 中。

#### 交叉编译

- 始终将上述相应的交叉编译器包（`fpc-*cpu*-*system*-rtl`) ）添加到 `depends`
- 对于非类 Unix 的系统总是添加 `!strip` 到 `options`
- 始终将所有已编译的单元（\*.a, \*.compiled, \*.o, \*.ppu, \*.res, \*.rst) 放在 `/usr/lib/fpc/*$_fpcver*/units/*$_unitdir*`下（或者如果是 multilib，放在 `/usr/lib/fpc/*$_fpcver*/units/i386-linux`）
- 始终使用 `any` （如果是 multilib，则为 `x86_64`）作为体系结构
- 如果安装导入库，请将 `staticlibs` 添加到 `options` 中。

## 1.4　KDE 软件打包准则

*难度：入门 ｜ 篇幅：4.3 KB ｜ 所属：Arch 打包准则*

将要讲到：构建目录、安装所需的前缀参数、构建类型、强制指定 Qt 版本、KF5 软件包命名、图标和 .desktop 文件安装

Arch Linux 上的 [KDE](../../05_桌面与图形/KDE/KDE.md) 软件包符合特定的模式。

## 构建目录

构建 [CMake](https://zh.wikipedia.org/wiki/CMake) 软件包的推荐做法是在项目根目录之外新建一个构建目录，并从该目录运行 cmake。[PKGBUILD](../包开发/PKGBUILD.md) 应如下所示：

```
prepare() {
  mkdir -p build
}

build() {
  cd build
  cmake ../${pkgname}-${pkgver}
}
```

## 安装所需的前缀参数

每个软件包都必须设置 `CMAKE_INSTALL_PREFIX` 变量，但考虑到 KDE 的自定义构建版本，请使用以下参数：

```
-DCMAKE_INSTALL_PREFIX=$(kf5-config --prefix)
```

当软件包被移动到 *extra* 仓库时，上述参数应改为：

```
-DCMAKE_INSTALL_PREFIX=/usr
```

## 构建类型

一般来说不应指定构建类型，这样能使 CMake 接受 `CFLAGS`、`CPPFLAGS` 等环境变量。

## 强制指定 Qt 版本


## KF5 软件包命名

### Plasma 挂件

Plasma 挂件 (之前名为 Plasmoids) 软件包应当被命名为 `plasma5-applets-*widgetname*`，这样能被识别为与 Plasma 5 相关的软件包，且易于与官方软件包区分。

### Runners

Plasma runners 软件包应当被命名为 `plasma5-runners-*runnername*` ，这样能被识别为与 Plasma 5 相关的软件包，且易于与官方软件包区分。

### 服务菜单

服务菜单软件包应当被命名为 `kf5-servicemenus-*servicename*` ，这样能被识别为与 KF5 相关的软件包。

### 主题

Plasma 主题软件包应当被命名为 `plasma5-themes-*themename*` ，这样能被识别为与 Plasma 5 相关的软件包。

## 图标和 .desktop 文件安装

一些 [KDE](../../05_桌面与图形/KDE/KDE.md) 软件在 hicolor 图标主题和 `.desktop` 文件中提供了图标（必须通过 [pacman 钩子](../包管理器/Pacman.md#钩子)安装）。应当避免在 `.install` 中对这些类型的文件使用安装命令，因为这将导致不必要的重复执行。

## 1.5　Haskell 打包准则

*难度：入门 ｜ 篇幅：4.4 KB ｜ 所属：Arch 打包准则*

将要讲到：软件包命名、体系架构、来源、重建顺序、PKGBUILD 库示例

本文档旨在涵盖在Arch上创建优质 Haskell [软件包](../../00_关于与社区/关于%20Arch/创建软件包.md)标准和准则。

在撰写本文之前，请联系 User:Felixonmars

## 软件包命名

对于 Haskell 库，使用 `haskell-*libraryname*`，通常与 [hackage](https://hackage.haskell.org/) 相同。

> **注意：**软件包名称应完全小写。

## 体系架构

参见 [PKGBUILD#arch](../包开发/PKGBUILD.md#arch)。

每个 Haskell 库或程序都依赖于体系结构。

## 来源

Haskell程序或库的首选来源是 [hackage](https://hackage.haskell.org)。[PKGBUILD#source](../包开发/PKGBUILD.md#source) `source=()` 数组应使用以下 URL 模板：

: `https://hackage.haskell.org/packages/archive/$_hkgname/$pkgver/$_hkgname-$pkgver.tar.gz`

请注意，因为 Haskell 软件包通常以 haskell- 为前缀，所以使用自定义 _hkgname 变量而不是 pkgname 。通常可以如下定义此变量：

```
_hkgname=stm-delay
```

## 重建顺序

当 Haskell 库更改时，它的构建标志或被更新，所有依赖包都需要重建。

## PKGBUILD 库示例

打包 Haskell 库与打包 Haskell 程序不同，Arch Linux 中打包的库供打包的 Haskell 程序使用。

```
PKGBUILD
```

```
# Contributor: Your Name <youremail@domain.com>
_hkgname=stm-delay
pkgname=haskell-stm-delay
arch=('x86_64')
url="https://hackage.haskell.org/package/$hkgname"
depends=(ghc-libs)
makedepends=(ghc)
source=("https://hackage.haskell.org/packages/archive/$_hkgname/$pkgver/$_hkgname-$pkgver.tar.gz")

build() {
  cd $_hkgname-$pkgver

  runhaskell Setup configure -O --enable-shared --enable-executable-dynamic --disable-library-vanilla \
    --prefix=/usr --docdir=/usr/share/doc/$pkgname --enable-tests \
    --dynlibdir=/usr/lib --libsubdir=\$compiler/site-local/\$pkgid \
    --ghc-option=-optl-Wl\,-z\,relro\,-z\,now \
    --ghc-option='-pie'

  runhaskell Setup build
  runhaskell Setup register --gen-script
  runhaskell Setup unregister --gen-script
  sed -i -r -e "s|ghc-pkg.*update[^ ]* |&'--force' |" register.sh
  sed -i -r -e "s|ghc-pkg.*unregister[^ ]* |&'--force' |" unregister.sh
}

check() {
  cd $_hkgname-$pkgver
  runhaskell Setup test
}

package() {
  cd $_hkgname-$pkgver

  install -D -m744 register.sh "$pkgdir"/usr/share/haskell/register/$pkgname.sh
  install -D -m744 unregister.sh "$pkgdir"/usr/share/haskell/unregister/$pkgname.sh
  runhaskell Setup copy --destdir="$pkgdir"
  install -D -m644 "LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
  rm -f "${pkgdir}/usr/share/doc/${pkgname}/LICENSE"
}
```

## 1.6　CLR 软件打包准则

*难度：入门 ｜ 篇幅：4.6 KB ｜ 所属：Arch 打包准则*

将要讲到：打包注意、PKGBUILD 示例

本文档定义了在 Arch Linux 下打包 Common Language Runtime （.NET）项目的标准。当前，只有 [Mono](../../07_开发与工具/框架/Mono.md) 能够为多个系统提供可用的，高效的 CLR runtime，并且该标准将反映其用法。请注意，许多 CLR 程序是在考虑到 Microsoft .NET 的情况下开发的，因此，由于 P/Invoke 调用和 Microsoft 数字版权管理（DRM）API 等 .NET 专有的因素，它们可能在 Mono 下运行，也可能无法在 Mono 下运行。因此不会为 Arch Linux 提供可用的软件包。但是，如果与 1.5.6 版（？）或更高版本的 [Wine](../../06_软件与应用/仿真/Wine.md) 结合使用，则您的程序包可能有机会在其下运行。如果是这种情况，请参阅 Wine PKGBUILD Guidelines 以获取更多信息。

## 打包注意

- 始终将 [mono](https://archlinux.org/packages/?name=mono)包 添加到 `depends`
- 始终设置 `arch` 为 `any`。Mono 尚不支持编译（运行）64-bit 程序集。
- 始终添加 `!strip` 到 `options`
- 如果程序包是一个库（DLL），则要将该程序包用作依赖项，请考虑将其安装到 Mono 的全局程序集缓存（GAC）中。
- 如果程序集是预编译的，并且带有程序调试数据库文件（Foo.dll.pdb），请考虑将其转换为：{ic|pdb2mdb Foo.dll}}
- 如果要执行该程序包（EXE），请确保安装到 `/usr/bin` ，使用 shell 脚本以运行它，类似于以下程序：

```
#!/bin/sh
mono foo.exe "$@"
```

### 签名的程序集

如果要将软件包安装到 GAC 中，请确保它具有签名的密钥文件。如果没有，您可以生成一个这样的：`sn -k 1024 Foo.snk`。随后，嵌入密钥文件到组件的最简单方法是拆卸它，是这样的：`monodis Foo.dll --output=Foo.il`。然后，像这样重新组装它：`ilasm /dll /key:Foo.snk Foo.il`

## PKGBUILD 示例

以下示例将尝试涵盖一些最常见的约定和构建系统。

### xbuild

#### 未签名的 DLL

```
# Maintainer: yourname <yourmail>
pkgname=foo
pkgver=1.0
pkgrel=1
pkgdesc="Fantabulous library for .Net"
arch=('any')
url="http://www.foo.bar"
license=('GPL')
depends=('mono')
options=('!strip')
source=("http://www.foo.bar/foobar.tar.gz")
md5sums=('4736ac4f34fd9a41fa0197eac23bbc24')

build() {
  cd "${srcdir}/foobar"

  xbuild Foo.sln

  # if the package is unsigned, do the following:
  cd "/bin/x86/Debug"
  monodis Foo.dll --output=Foo.il
  sn -k 1024 Foo.snk
  ilasm /dll /key:Foo.snk Foo.il
}

package() {
  cd "${srcdir}/foobar/bin/x86/Debug"

  install -Dm644 Foo.dll "$pkgdir/usr/lib/foobar/Foo.dll"
  install -Dm644 Foo.dll.mdb "$pkgdir/usr/lib/foobar/Foo.dll.mdb"

  # Register assembly into Mono's GAC
  gacutil -i Foo.dll -root "$pkgdir/usr/lib"
}
```

## 1.7　命令行解释器（Shell）打包准则

*难度：入门 ｜ 篇幅：4.6 KB ｜ 所属：Arch 打包准则*

将要讲到：安装、Shell 自动补全、Shell 函数或模块

## 安装

Shell 必须在 `/etc/shells` 中列出，以使用户能够[更改默认 Shell](https://wiki.archlinuxcn.org/wiki/命令行解释器#更改默认_Shell)。大部分的 shell 软件包都带有如下安装脚本：

```
post_install() {
    grep -Fqx /bin/shellname /etc/shells || echo /bin/shellname >>/etc/shells
    grep -Fqx /usr/bin/shellname /etc/shells || echo /usr/bin/shellname >>/etc/shells
}

post_upgrade() {
    post_install
}

post_remove() {
    sed -i -r '/^(\/usr)?\/bin\/shellname$/d' etc/shells
}
```

## Shell 自动补全

大部分 shell 都为少部分常用命令内置了一套自动补全，并支持扫描至少一个系统路径下由其它软件包提供的函数。 以下表格列出了软件包的补全文件存放路径和命名规则：

| Shell | 目录 | 文件名 |
| --- | --- | --- |
| [Bash](../../07_开发与工具/命令行解释器/Bash.md) | `/usr/share/bash-completion/completions/` | `binary_name` |
| Elvish | `/usr/share/elvish/lib/` | `binary_name.elv` |
| [Fish](../../07_开发与工具/命令行解释器/Fish.md) | `/usr/share/fish/vendor_functions.d/` | `binary_name.fish` |
| [Zsh](../../07_开发与工具/命令行解释器/Zsh.md) | `/usr/share/zsh/site-functions/` | `_binary_name` |

其它 shell：

- [Nushell](https://wiki.archlinuxcn.org/wiki/Nushell) 自带了部分补全功能，但[暂时](https://github.com/nushell/nushell/issues/11337)**不**支持通过全局目录提供补全。对于提供 Nushell 自动补全函数的软件包，可以将它们打包到 `/usr/share/nushell/autoload/binary_name.nu`，然后使用 `post_install()` 函数输出提示，让用户自行将 `use /path/to/file` 添加到配置文件中。

> **提示：**一般来说，软件包不得在 \*depends\* 或 \*optdepends\* 中包含 shell。就算软件提供了自动补全文件，也不代表它们对于 shell 有依赖关系。一种例外是软件包不提供自身的补全，且 shell 软件包中也不提供其补全，而是由补充集合包（例如 [bash-completion](https://archlinux.org/packages/?name=bash-completion)包 和 [zsh-completions](https://archlinux.org/packages/?name=zsh-completions)包）提供。在这种情况下，需要将它们添加到 `optdepends` 中。

## Shell 函数或模块

| Shell | 目录 | 文件名 |
| --- | --- | --- |
| Elvish | `/usr/share/elvish/lib/` | `module_name.elv` |
| [Fish](../../07_开发与工具/命令行解释器/Fish.md) | `/usr/share/fish/vendor_functions.d/` | `function_name.fish` |
| [Zsh](../../07_开发与工具/命令行解释器/Zsh.md) | `/usr/share/zsh/site-functions/` | `function_name` |

## 1.8　字体打包指南

*难度：入门 ｜ 篇幅：4.6 KB ｜ 所属：Arch 打包准则*

将要讲到：通用指引、打包、示例包、参见

本文档提供为[字体](../../05_桌面与图形/字体/字体.md)撰写 [PKGBUILD](../包开发/PKGBUILD.md) 的建议规范。

## 通用指引

### 包命名

- [TTF](https://zh.wikipedia.org/wiki/TrueType) 字体使用`ttf-`前缀。
- [OTF](https://zh.wikipedia.org/wiki/OpenType) 字体使用`otf-`前缀。

若为[可变字体](https://zh.wikipedia.org/wiki/%E5%8F%AF%E5%8F%98%E5%AD%97%E4%BD%93)，使用`-variable`后缀。

包名应使用全小写。

### 包描述

至少要表明它是一个字体，并注明字体类别：衬线体、非衬线体、等宽等。

### 架构

字体是与架构无关的。使用`arch=(any)`。

### 依赖

字体没有依赖项。但是许多仓库里的包依赖于 [fontconfig](https://archlinux.org/packages/?name=fontconfig)包 和 [xorg-mkfontscale](https://archlinux.org/packages/?name=xorg-mkfontscale)包，这些依赖是以前字体包还需要使用安装脚本更新字体缓存时的历史遗留。现在可以用 [pacman 钩子](../包管理器/Pacman.md#钩子)来完成这项工作。安装上述两个包时，`/usr/share/fonts/`下的所有字体都会被缓存，因此无需再强制使用 fontconfig 或 mkfontscale。

### 提供

许多应用依赖于虚拟包`ttf-font`。若你的字体家族满足[该页面](https://archlinux.org/todo/fix-ttf-font-default-font-setup)的要求，可添加`provides=('ttf-font')`。

### 来源

可按下列顺序查询：

- 官方网站，或某个如 GitHub 的托管平台
- [Google Fonts](https://fonts.google.com): `https://fonts.google.com/download?family=*Name*`
- [Font Squirrel](https://www.fontsquirrel.com)、[Font Library](https://fontlibrary.org)、[FFonts](https://www.ffonts.net)、[1001 Fonts](https://www.1001freefonts.com)、 [DaFont](https://www.dafont.com)

以下来源不推荐：

- [FontSpace](https://www.fontspace.com)（校验码因不明原因变动）

## 打包

下列片段是以 OFL（SIL Open Font License）授权的 OTF 字体的示例片段：

```
license=('OFL')
...
package() {
  install -Dm644 -t "$pkgdir/usr/share/fonts/OTF" "path/to/font/*.otf"
  install -Dm644 -t "$pkgdir/usr/share/licenses/$pkgname" "path/to/license/OFL.txt"
}
```

- [OFL](https://zh.wikipedia.org/wiki/SIL%E5%BC%80%E6%BA%90%E5%AD%97%E4%BD%93%E6%8E%88%E6%9D%83)理论上是个[自定义许可](../包开发/PKGBUILD.md#license)，因此字体包的许可证文件需要安装在`/usr/share/licenses/[包名]`。
- 若有许多个文件，目标目录名可考虑使用`包名`而非`OTF`。

## 示例包

- [ttf-merriweather-sans](https://aur.archlinux.org/packages/ttf-merriweather-sans/)AUR
- [otf-commissioner](https://aur.archlinux.org/packages/otf-commissioner/)AUR - OTF、TTF、可变三种包分离

## 参见

- [论坛讨论](https://bbs.archlinux.org/viewtopic.php?pid=1888912)

## 1.9　R 软件打包准则

*难度：进阶 ｜ 篇幅：6.2 KB ｜ 所属：Arch 打包准则*

将要讲到：软件包命名、软件包版本号、Arch、依赖、来源、构建与打包

本文档涵盖了为 R 软件包编写 [PKGBUILD](../包开发/PKGBUILD.md) 的标准和指南。大多数信息可以通过查看软件包的 `DESCRIPTION` 文件获得。在 R 中运行 `tools::CRAN_package_db()` 可以获得大部分信息。您还可以访问 [CRAN](https://cran.r-project.org/src/contrib/PACKAGES)、[Bioconductor link1](https://bioconductor.org/packages/release/bioc/src/contrib/PACKAGES)与[Bioconductor link2](https://bioconductor.org/packages/release/data/annotation/src/contrib/PACKAGES)，了解所有 R 软件包的信息。

## 软件包命名

软件包应命名为 `r-pkgname`，其中 pkgname 取自 `DESCRIPTION` 文件中的`Package`字段。软件包名称应小写。

## 软件包版本号

从`Version`字段中获取。R 允许软件包在版本号中使用冒号和连字符，但在 PKGBUILD 中不允许。将其转换为句号或下划线。

## Arch

请参见 [PKGBUILD#arch](../包开发/PKGBUILD.md#arch)。如果软件包的 CRAN 网页上有 `NeedsCompilation: yes`，那么它很可能是特定架构的。否则，很可能不是，参见 [PKGBUILD#arch](../包开发/PKGBUILD.md#arch)。

## 依赖

在软件包 `DESCRIPTION` 文件的`Depends`、`Imports`或 `LinkingTo` 字段中列出的 R 软件包应列在[依赖项](../包开发/PKGBUILD.md#depends)下。

`Suggests` 中列出的 R 软件包应列为 optdepends。

有些软件包需要外部工具，这些工具列在 `SystemRequirements` 下。

某些软件包需要[依赖](../包开发/PKGBUILD.md#depends) [gcc-fortran](https://archlinux.org/packages/?name=gcc-fortran)包，但它并不总是列在 `DESCRIPTION` 文件中。

## 来源

CRAN 上的所有 R 软件包都可以在网站 `https://cran.r-project.org/src/contrib/*cranname*_*cranversion*.tar.gz` 上找到，其中 `*cranname*` 是 CRAN 上软件包的名称，`*cranversion*` 是 cran 的版本。

Bioconductor 上可用的 R 软件包可从网站 `https://bioconductor.org/packages/release/bioc/src/contrib/*bcname*_*bcname*.tar.gz` 或 `https://bioconductor.org/packages/release/data/annotation/src/contrib/*bcname*_*bcname*.tar.gz` 上获取，其中 `*bcname*` 是 Bioconductor 上软件包的名称，`*bcver*` 是版本。

## 构建与打包

R 内置支持构建软件包。下面是三个软件源的 `PKGBUILD` 模板： MRAN、CRAN 和 Bioconductor。MRAN 是 CRAN 的快照镜像，使用该模板即使软件包过期也能构建。

### MRAN

```
_cranname=
_cranver=
_updatedate=YYYY-MM-DD
pkgname=r-${_cranname,,}
pkgver=${_cranver//[:-]/.}
pkgrel=1
pkgdesc=""
arch=()
url="https://cran.r-project.org/package=${_cranname}"
license=()
depends=(r)
makedepends=()
optdepends=()
source=("https://cran.microsoft.com/snapshot/${_updatedate}/src/contrib/${_cranname}_${_cranver}.tar.gz")
sha256sums=()

build() {
  R CMD INSTALL ${_cranname}_${_cranver}.tar.gz -l "${srcdir}"
}

package() {
  install -dm0755 "${pkgdir}/usr/lib/R/library"

  cp -a --no-preserve=ownership "${_cranname}" "${pkgdir}/usr/lib/R/library"
}
```

### CRAN

```
_cranname=
_cranver=
pkgname=r-${_cranname,,}
pkgver=${_cranver//[:-]/.}
pkgrel=1
pkgdesc=""
arch=()
url="https://cran.r-project.org/package=${_cranname}"
license=()
depends=(r)
makedepends=()
optdepends=()
source=("https://cran.r-project.org/src/contrib/${_cranname}_${_cranver}.tar.gz")
sha256sums=()

build() {
  R CMD INSTALL ${_cranname}_${_cranver}.tar.gz -l "${srcdir}"
}

package() {
  install -dm0755 "${pkgdir}/usr/lib/R/library"

  cp -a --no-preserve=ownership "${_cranname}" "${pkgdir}/usr/lib/R/library"
}
```

### Bioconductor

```
_bcname=
_bcver=
pkgname=r-${_bcname,,}
pkgver=${_bcver//[:-]/.}
pkgrel=1
pkgdesc=""
arch=()
url="https://bioconductor.org/packages/${_bcname}"
license=()
depends=(r)
makedepends=()
optdepends=()
source=("https://bioconductor.org/packages/release/bioc/src/contrib/${_bcname}_${_bcver}.tar.gz")
# or
# source=("https://bioconductor.org/packages/release/data/annotation/src/contrib/${_bcname}_${_bcver}.tar.gz")
sha256sums=()

build() {
  R CMD INSTALL ${_bcname}_${_bcver}.tar.gz -l "${srcdir}"
}

package() {
  install -dm0755 "${pkgdir}/usr/lib/R/library"

  cp -a --no-preserve=ownership "${_bcname}" "${pkgdir}/usr/lib/R/library"
}
```

## 技巧与窍门

### Bioconductor 存储库

欲轻松访问bioconductor软件包，可以添加 [bioarchlinux](../非官方用户仓库/非官方用户仓库.md#bioarchlinux) 软件源。

## 1.10　Node.js 打包准则

*难度：进阶 ｜ 篇幅：6.7 KB ｜ 所属：Arch 打包准则*

将要讲到：软件包命名、源代码、使用 npm、使用 nvm

本文档包含了给 [Node.js](../../07_开发与工具/开发/Node.js.md) 软件包编写 [PKGBUILD](../包开发/PKGBUILD.md) 的规范与指导。

## 软件包命名

> **提示：**可以用自定义变量 `_pkgname` 代替 `pkgname`，一般这个变量可以这样定义： `_pkgname=${pkgname#nodejs-}`

Node.js 库的软件包名应该以 `nodejs-` 开头。对于独立的应用，只使用程序名。

## 源代码

npm 提供了可靠的命名规范和下载 URL。[PKGBUILD#source](../包开发/PKGBUILD.md#source) `source=()` 列表可以使用下面的 URL 模板：

```
https://registry.npmjs.org/$_pkgname/-/$_pkgname-$pkgver.tgz
```

## 使用 npm

使用 npm 安装时，应当将其添加为构建时依赖。

```
makedepends=('npm')
```

不需要解压压缩包：

```
noextract=("${_pkgname}-${pkgver}.tgz")
```

这是一个最小的 [package](../../00_关于与社区/关于%20Arch/创建软件包.md#package()) 函数：

```
package() {
    npm install -g --prefix "${pkgdir}/usr" "${srcdir}/${_pkgname}-${pkgver}.tgz"

    # npm 会把所有文件的所有权交给构建用户
    # https://bugs.archlinux.org/task/63396
    chown -R root:root "${pkgdir}"
}
```

### 设置临时缓存

当 *npm* 处理 `package.json` 时，npm 会为了构建软件包，下载依赖到默认的缓存目录 `$HOME/.npm`。为了避免污染用户的 home 目录，可以用 `--cache` 选项，将缓存目录临时设为其他目录。

下载依赖到 `${srcdir}/npm-cache`，然后安装它们到打包目录：

```
npm install --cache "${srcdir}/npm-cache"
```

继续正常打包：

```
npm run packager
```

### 软件包包含向 $srcdir/$pkgdir 的引用

很不幸，*npm* 会创建指向 `$srcdir` 和 `$pkgdir` 的引用。这是一个[已知问题](https://github.com/npm/cli/issues/3828)。但你可以手动删除这些引用，反正也不会用到它们。

所有依赖都会在 `_where` 属性里，包含指向 `$pkgdir` 的引用，你通常可以用一点 *sed* 魔法删除它们：

```
find "$pkgdir" -name package.json -print0 | xargs -r -0 sed -i '/_where/d'
```

你的主软件包也会包含一些其他引用，删除它们最简单的办法就删除所有有下划线的属性，但用 *sed* 不是很方便。你可以使用 [jq](https://archlinux.org/packages/?name=jq)包 得到想要的结果：

```
local tmppackage="$(mktemp)"
local pkgjson="$pkgdir/usr/lib/node_modules/$_pkgname/package.json"
jq '.|=with_entries(select(.key|test("_.+")|not))' "$pkgjson" > "$tmppackage"
mv "$tmppackage" "$pkgjson"
chmod 644 "$pkgjson"
```

另一个你会找到对 `$pkgdir` 引用的地方，是软件包的 `man` 属性。如果你不在乎 man 页面（反正它们不会被作为依赖安装），你可以像这样删除它们：

```
find "$pkgdir" -type f -name package.json | while read pkgjson; do
    local tmppackage="$(mktemp)"
    jq 'del(.man)' "$pkgjson" > "$tmppackage"
    mv "$tmppackage" "$pkgjson"
    chmod 644 "$pkgjson"
done
```

一个使用了上面全部三个技巧的例子：[readability-cli](https://aur.archlinux.org/packages/readability-cli/)AUR。

## 使用 nvm

当一个基于 [node.js](../../07_开发与工具/开发/Node.js.md) 的应用需要**构建**或**打包**使用不同的 node.js 版本时，可以使用 [nvm](https://aur.archlinux.org/packages/nvm/)AUR。

> **警告：**这只适用于应用的构建 / 打包时依赖，不影响运行时依赖.

把它加入构建时依赖：

```
makedepends=('npm' 'nvm')
```

[nvm](https://aur.archlinux.org/packages/nvm/)AUR 使用 `NVM_DIR` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)来查找前缀（译注：指 nvm 的安装路径，下同）。如果不在 [nvm](https://aur.archlinux.org/packages/nvm/)AUR 初始化前指定，前缀就会被设置为 `$HOME/.nvm`。

你可以用下面的函数创建自定义前缀，并将用户目录和前缀隔离开：

```
_ensure_local_nvm() {
    # let's be sure we are starting clean
    which nvm >/dev/null 2>&1 && nvm deactivate && nvm unload
    export NVM_DIR="${srcdir}/.nvm"

    # The init script returns 3 if version specified
    # in ./.nvrc is not (yet) installed in $NVM_DIR
    # but nvm itself still gets loaded ok
    source /usr/share/nvm/init-nvm.sh || [[ $? != 1 ]]
}
```

应该在使用 [nvm](https://aur.archlinux.org/packages/nvm/)AUR、[npm](https://archlinux.org/packages/?name=npm)包 或其他基于特定版本 [Node.js](../../07_开发与工具/开发/Node.js.md) 的程序前，调用这个函数。

### 示例 [PKGBUILD](../包开发/PKGBUILD.md) 用法

```
prepare() {
    _ensure_local_nvm
    nvm install 14.15.0
}

build() {
    _ensure_local_nvm
    npm install
}
```

或者，执行不带参数的 `nvm install`，会在当前目录的 `.nvrc` 文件中查找版本字符串。

这个用法的一个例子可以在 [insomnia](https://aur.archlinux.org/packages/insomnia/)AUR 找到。

## 1.11　DKMS 软件包打包准则

*难度：进阶 ｜ 篇幅：6.9 KB ｜ 所属：Arch 打包准则*

将要讲到：包名、依赖、源代码构建位置、打补丁、.install 中模块的自动加载、例子

创建一个新的 [DKMS](../../02_系统管理与内核/内核/DKMS.md) 包时，可以参考下面的指导方针。

## 包名

DKMS 包的命名方式是：原始包名加"*\-dkms*"后缀。

通常在 `$pkgname` 后面使用 `$_pkgname` 记录不包含 "*\-dkms*" 后缀的软件包名 (例如 `_pkgname=${pkgname%-*}`). 这样可以在原始的软件包 PKGBUILD 和 DKMS 编译文件之间保持相似性。

## 依赖

依赖的包应该是原来软件包的基础上，加上 [dkms](https://archlinux.org/packages/?name=dkms)包。这一点很重要，因为它将提供工具和 Hook 文件，在内核更新时重建由 *\-dkms* 软件包提供的内核驱动程序。

不要在 [PKGBUILD](../包开发/PKGBUILD.md) 包含 [linux-headers](https://archlinux.org/packages/?name=linux-headers)包 或任何其他的Linux头文件包。这些头文件已经作为 [dkms](https://archlinux.org/packages/?name=dkms)包 的可选依赖，同时每个内核包都有自己的头文件包，因此在 *\-dkms* 软件包包含这些头文件包是不必要的重复和限制的。

## 源代码构建位置

构建模块所需源代码需要放在（这是DKMS构建模块时使用的默认目录）：

```
/usr/src/PACKAGE_NAME-PACKAGE_VERSION
```

在软件包目录，要包含一个 `dkms.conf` 配置文件，告诉 DKMS 如何编译。这个配置文件需要包含：

- `PACKAGE_NAME` - 实际的项目名称，通常使用 `$_pkgname` （见[#包名](#包名)）或 `$_pkgbase`.
- `PACKAGE_VERSION` - 通常使用 `$pkgver`.

> **注意：**不需要编译内核模块的源文件，也不需要安装它们；由于 [dkms](https://archlinux.org/packages/?name=dkms)包 中的 [pacman#钩子](../包管理器/Pacman.md#钩子)，每当 Linux 内核更新时，这些工作都会自动完成。

## 打补丁

为内核模块源代码打补丁既可以直接在 PKGBUILD 中进行，也可以通过`dkms.conf`来进行。

若通过 `dkms.conf` 打补丁，请确保将补丁安装在 `/usr/src/*PACKAGE_NAME*-*PACKAGE_VERSION*/patches/` 目录下，并为每个补丁添加 `PATCH[*number*]=*patch_filename*` 条目，记得将 `*number*` 替换为以0起始的数字。查阅 [dkms(8) § DKMS.CONF](https://man.archlinux.org/man/dkms.8#DKMS.CONF) 以获取相关信息

## .install 中模块的自动加载

模块的加载和卸载必须由用户自己来执行，设想一下，某个模块可能在加载的时候崩溃。

另外，**不要在 .install 中调用 dkms** ，该操作会由 [dkms](https://archlinux.org/packages/?name=dkms)包 提供的 [Pacman 钩子](../包管理器/Pacman.md#钩子) 自动完成。Pacman 会自动执行 `dkms install` 和 `dkms remove` 钩子。

`dkms install` 会确保过程结束时执行 `depmod`。`dkms install` 依赖 `dkms build` (针对当前内核编译源码)，build 依赖 `dkms add` (添加从 `/var/lib/dkms/<package>/<version>/source` 到 `/usr/src/<package>` 的链接)。

## 例子

这儿有个根据包名字和版本来对`dkms.conf`进行编辑，并安装模块黑名单配置文件的例子。

其它示例请参考[官方软件仓库中的 -dkms 软件包](https://archlinux.org/packages/?q=-dkms)和 [AUR 中的 -dkms 包](https://aur.archlinux.org/packages?K=-dkms)。

### PKGBUILD

```
PKGBUILD
```

```
# Maintainer: foo <foo(at)example(dot)org>
# Contributor: bar <bar(at)example(dot)org>

_pkgbase=example
pkgname=example-dkms
pkgver=1
pkgrel=1
pkgdesc="The Example kernel modules (DKMS)"
arch=('x86_64')
url="https://www.example.org/"
license=('GPL2')
depends=('dkms')
conflicts=("${_pkgbase}")
install=${pkgname}.install
source=("${url}/files/tarball.tar.gz"
        'dkms.conf'
        "${pkgname}.conf"
        'linux-3.14.patch')
md5sums=(use 'updpkgsums')

prepare() {
  cd ${_pkgbase}-${pkgver}

  # Patch
  patch -p1 -i "${srcdir}"/linux-3.14.patch
}

package() {
  # Copy dkms.conf
  install -Dm644 dkms.conf "${pkgdir}"/usr/src/${_pkgbase}-${pkgver}/dkms.conf

  # Set name and version
  sed -e "s/@_PKGBASE@/${_pkgbase}/" \
      -e "s/@PKGVER@/${pkgver}/" \
      -i "${pkgdir}"/usr/src/${_pkgbase}-${pkgver}/dkms.conf

  # Copy sources (including Makefile)
  cp -r ${_pkgbase}/* "${pkgdir}"/usr/src/${_pkgbase}-${pkgver}/

  # Blacklists conflicting module
  install -Dm644 ${pkgname}.conf "${srcdir}/usr/lib/modprobe.d/${pkgname}.conf"
}
```

### dkms.conf

```
dkms.conf
```

```
PACKAGE_NAME="@_PKGBASE@"
PACKAGE_VERSION="@PKGVER@"
MAKE="make --uname_r=$kernelver"
CLEAN="make clean"
BUILT_MODULE_NAME="@_PKGBASE@"
DEST_MODULE_LOCATION="/kernel/drivers/misc"
AUTOINSTALL="yes"
```

### .install

该示例会在安装或升级后显示一条信息，建议用户在不想立即重启系统时，卸载冲突模块（`example-conflicting-module`）并加载该包提供的模块（`example`）。

```
example.install
```

```
post_install() {
  cat<<EOF

Unload and load kernel modules:

  rmmod example-conflicting-module
  modprobe example

EOF
}

post_upgrade() {
  post_install
}
```

### Module blacklist conf

已知模块 `example-conflicting-module` 会与模块 `example` 冲突时，应将其加入模块黑名单：

```
example-dkms.conf
```

```
blacklist example-conflicting-module
```

## 1.12　Go 语言软件打包准则

*难度：进阶 ｜ 篇幅：7.5 KB ｜ 所属：Arch 打包准则*

将要讲到：通用准则、构建、PKGBUILD 范例、软件包示例

本文涵盖了为 [Go](../../07_开发与工具/编程语言/Go.md) 软件包编写 [PKGBUILD](../包开发/PKGBUILD.md) 时须遵循的标准和指引。

## 通用准则

### 命名

如果软件包提供的程序与 Go 生态存在强关联，请使用 `go-*模块名称*`。对于其它应用，请仅使用程序名。

> **注意：**软件包名称只允许使用小写字符。

## 构建

### 依赖

Go 1.11 为 [go 模块](https://github.com/golang/go/wiki/Modules)提供了初步支持，使得上游的 Go 代码可以声明依赖并将其固定到特定项目版本。目前，我们的打包工作利用了这一点来满足 go 模块依赖关系。

#### 上游项目不使用 go 模块

对于不使用 Go 模块的上游代码，可以使用以下变通方法，并请考虑向上游提出 issue：

```
PKGBUILD
```

```
url=https://github.com/upstream_user/upstream_project

prepare() {
  cd "$pkgname-$pkgver"
  go mod init "${url#https://}" # strip https:// from canonical URL
  go mod tidy
}
```

> **注意：**由于模块可能会在软件的各次构建期间出现变化，该方法将导致软件包的构建不具有可复现性。

#### 上游项目使用 go 模块

go 默认会使用 `GOPATH` 来下载和存放 go 模块，并导致用户的 `~/go` 目录大小增加。

为将所有 go 模块保留在构建环境中，可以在准备步骤（prepare）中配置 `GOPATH="${srcdir}"`，然后将 go 模块下载到软件包源码目录（srcdir）中：

```
PKGBUILD
```

```
prepare() {
  cd "${pkgname}-${pkgver}"
  export GOPATH="${srcdir}"
  go mod download -modcacherw
}
```

### 构建标志和选项

Go 不会自动将系统的构建标志（例如 `CFLAGS` 和 `LDFLAGS`）传递给 C 工具链。为使用 [RELRO](https://www.redhat.com/en/blog/hardening-elf-binaries-using-relocation-read-only-relro) 和其它加固标志构建 Go 二进制文件，需要在构建环境中显式指定 `CGO_CFLAGS` 和 `CGO_LDFLAGS` 等相关变量：

```
export CGO_CPPFLAGS="${CPPFLAGS}"
export CGO_CFLAGS="${CFLAGS}"
export CGO_CXXFLAGS="${CXXFLAGS}"
export CGO_LDFLAGS="${LDFLAGS}"
export GOFLAGS="-buildmode=pie -trimpath -ldflags=-linkmode=external -mod=readonly -modcacherw"

# 或者也可以通过命令行选项指定部分标志
go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-linkmode external -extldflags \"${LDFLAGS}\"" \
    .
```

> **注意：**大多数用于 Go 应用的 Makefile 会覆盖掉 `GOFLAGS`，不会将上述标志传递给编译器。如果上游项目使用了 Makefile，则需要通过补丁使其遵循传入的标志，或是直接调用 `go build` 来绕过该文件。

#### 标志功能

- `-buildmode=pie` 会启用 [PIE 编译](https://en.wikipedia.org/wiki/Position-independent_code)，用于加固二进制文件。
- `-trimpath` 用于可复现构建，可以防止嵌入完整构建路径和模块路径。
- `-mod=readonly` 可以保证模块文件不会在 go 的任何操作中被更改。
- `-modcacherw` 不重要，但是可以保证 go 模块会创建一个可写的路径。默认只会创建一个只读的路径。

> **提示：**如果软件的 `modules.txt` 中包含了一个 `vendor` 目录，那可以将 `-mod` 标志修改为 `-mod=vendor`。

> **警告：**包维护者需要确保构建标志被正确传递给了编译器，请务必检查源码的 `Makefile`。

#### 支持调试包

要启用带有源代码和符号查找的调试包，需要对默认构建标志进行一些修改。

- 删除 `-trimpath` 来确保源码路径被写入二进制文件。
- 由于目前的工具不支持压缩的 DWARF 头，因此需要在 `-ldflags` 中包含 `-compressdwarf=false` 来确保工具能解析 DWARF 头。
- 使用 `-linkmode=external`，因为 go 内部的链接器不会把 build-id 嵌入二进制文件。
- 包含 `GOPATH="${srcdir}"` 以使 *makepkg* 能包含所有模块的源码。

使用上述选项应该能生成包含正确的分离符号和源代码，并能被调试器正确读取的调试包。

```
export CGO_CPPFLAGS="${CPPFLAGS}"
export CGO_CFLAGS="${CFLAGS}"
export CGO_CXXFLAGS="${CXXFLAGS}"
export CGO_LDFLAGS="${LDFLAGS}"
export GOPATH="${srcdir}"
export GOFLAGS="-buildmode=pie -mod=readonly -modcacherw"

go build -ldflags "-compressdwarf=false -linkmode external" .
```

### 输出目录

有多种方法可以构建项目中的所有 go 二进制文件。

```
build(){
    cd "$pkgname-$pkgver"
    go build -o output-binary .
}
```

`...` 是一个简写，它会让编译器递归遍历所有目录，并找到所有二进制文件。可以将其搭配输出目录使用以构建所有文件：

```
prepare(){
    cd "$pkgname-$pkgver"
    mkdir -p build
}

build(){
    cd "$pkgname-$pkgver"
    go build -o build ./cmd/...
}
```

## PKGBUILD 范例

```
pkgname=foo
pkgver=0.0.1
pkgrel=1
pkgdesc='Go PKGBUILD Example'
arch=('x86_64')
url="https://example.org/$pkgname"
license=('GPL')
makedepends=('go')
source=("$url/$pkgname-$pkgver.tar.gz")
sha256sums=('1337deadbeef')

prepare(){
  cd "$pkgname-$pkgver"
  mkdir -p build/
}

build() {
  cd "$pkgname-$pkgver"
  export CGO_CPPFLAGS="${CPPFLAGS}"
  export CGO_CFLAGS="${CFLAGS}"
  export CGO_CXXFLAGS="${CXXFLAGS}"
  export CGO_LDFLAGS="${LDFLAGS}"
  export GOFLAGS="-buildmode=pie -trimpath -ldflags=-linkmode=external -mod=readonly -modcacherw"
  go build -o build ./cmd/...
}

check() {
  cd "$pkgname-$pkgver"
  go test ./...
}

package() {
  cd "$pkgname-$pkgver"
  install -Dm755 build/$pkgname "$pkgdir"/usr/bin/$pkgname
}
```

## 软件包示例

- [podman](https://archlinux.org/packages/?name=podman)包
- [k9s](https://archlinux.org/packages/?name=k9s)包
- [helm](https://archlinux.org/packages/?name=helm)包

## 1.13　Electron 打包准则

*难度：进阶 ｜ 篇幅：8.7 KB ｜ 所属：Arch 打包准则*

将要讲到：Using the system electron、Architecture、Directory structure、Getting version of Electron

This document covers standards and guidelines on writing [PKGBUILD](../包开发/PKGBUILD.md)s for [Electron](../../07_开发与工具/框架/Electron.md).

## Using the system electron

Arch Linux provides versioned [electron\*](https://archlinux.org/packages/?q=electron) packages and an [electron](https://archlinux.org/packages/?name=electron)包 metapackage for the latest version. They can be used to run an electron application via a shell script wrapper:

```
#!/bin/sh

exec /usr/bin/electron34 /path/to/appname/ "$@"
```

The `*appname*/` directory, or alternatively a file bundle called `*appname*.asar`, can be found in a prebuilt electron application as the `resources/app/` folder (or `resources/app.asar`). Everything else is just a copy of the electron runtime and can be removed from the final package.

> **注意：**Applications that require `ELECTRON_RUN_AS_NODE=1`, e.g. [Visual Studio Code](../../07_开发与工具/文本编辑器/Visual%20Studio%20Code.md), cannot use `/usr/bin/electron*`. See [code.sh](https://gitlab.archlinux.org/archlinux/packaging/packages/code).

### Editing version on package.json

It is dangerous to edit version of Electron in `package.json` or `package-lock.json` using `sed`. You can use `npm pkg set devDependencies.electron=$(cat /usr/lib/electron*/version)` instead.

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** Editing package.json may not needed and unrelated to building modules correctly.（在 [Talk:Electron 打包准则](https://wiki.archlinuxcn.org/wiki/Talk:Electron_%E6%89%93%E5%8C%85%E5%87%86%E5%88%99) 中讨论）

### Building compiled extensions against the system electron

Some electron applications have compiled native extensions which link to the electron runtime, and must be built using the correct electron version. Since npm/yarn will always build against a private prebuilt copy of electron, patch the electron dependency from `package.json` to reference the same version as the system electron dependency. The build system will download the prebuilt copy it requires, compile the native extensions, and package everything into a final distribution, but this can be pruned during the `package()` step as usual.

Alternatively, you can remove the electron dependency from `package.json` and set the correct environment variables before running npm:

```
export npm_config_target=$(tail /usr/lib/electron/version)
export npm_config_arch=x64
export npm_config_target_arch=x64
export npm_config_disturl=https://electronjs.org/headers[失效链接 2023-10-29 ⓘ]
export npm_config_runtime=electron
export npm_config_build_from_source=true
HOME="$srcdir/.electron-gyp" npm install
```

Set `HOME` to a path inside the `$srcdir` so the build process does not place any files in your real `HOME` directory. Make sure to adjust the path for all further commands that make use of the `.electron-gyp` cache.

(more details [in Electron docs](https://www.electronjs.org/docs/latest/tutorial/using-native-node-modules#using-npm)).

### Using electron-builder with system electron

Many projects use **electron-builder** to build and package the Javascript file and Electron binaries. By default electron-builder downloads the entire electron version that is defined in the package management file (e.g. `package.json`). This might not be desired if you want to use the system electron and save the bandwidth since you are going to throw away the electron binaries anyway. The electron-builder provides the configurations `electronDist` and `electronVersion`, to specify a custom path of Electron and the version the application is packaged for respectively.

Find the electron-builder configuration file (e.g. `electron-builder.json`) and add the following settings:

- `electronDist` to　`/usr/lib/electron34` for [electron34](https://archlinux.org/packages/?name=electron34)包
- `electronVersion` to the contents of `/usr/lib/electron34/version` (without the leading `v` if exists)

Packages that apply this: [rocketchat-desktop](https://aur.archlinux.org/packages/rocketchat-desktop/)AUR [ubports-installer-git](https://aur.archlinux.org/packages/ubports-installer-git/)AUR

[electron-builder configuration](https://www.electron.build/configuration/configuration)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2024-10-12 ⓘ\]

Alternatively you can use the CLI to change/add these settings like this:

```
./node_modules/.bin/electron-builder --linux --x64 --dir $dist -c.electronDist=$electronDist -c.electronVersion=$electronVer
```

Note that you have to specify all these options or it will not work.

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** Current setting still copies Electron from $electronDist . It is expired to add the method to stop it. （在 [Talk:Electron 打包准则](https://wiki.archlinuxcn.org/wiki/Talk:Electron_%E6%89%93%E5%8C%85%E5%87%86%E5%88%99) 中讨论）

## Architecture

See [PKGBUILD#arch](../包开发/PKGBUILD.md#arch).

An Electron package that contains compiled native extensions is architecture-dependent. Otherwise it is most likely architecture-independent.

If the package contains a prebuilt copy of electron, it is always architecture-dependent.

## Directory structure

If the package is architecture-dependent, install the `resources/app/` directory to `/usr/lib/*appname*/`. Otherwise use `/usr/share/*appname*/`.

If the package contains a prebuilt copy of electron, copy the final distribution in its entirety to `/opt/*appname*`.

## Getting version of Electron

Version of Electron could be given by `npm pkg get devDependencies.electron` in the directory containing `package.json` or `package-lock.json`.

Prebuild or Nonfree application hides version of electron from `package.json`, `package-lock.json`, and `version` files. In such case, you may get version by replacing `app` or `app.asar` with `/usr/lib/electron/resources/default_app.asar` and running `*pathto/electron-binary* --version`.

> **注意：**Avoid doing it at PKGBUILD. This should be last resort to make packages with system-wide electron.

## 1.14　Rust 软件打包准则

*难度：进阶 ｜ 篇幅：8.9 KB ｜ 所属：Arch 打包准则*

将要讲到：软件包命名、源码、依赖关系、准备工作、构建、检查

本文档涵盖了为 [Rust](../../07_开发与工具/编程语言/Rust.md) 软件编写 [PKGBUILD](../包开发/PKGBUILD.md) 的标准和指南。

## 软件包命名

打包 [Rust](../../07_开发与工具/编程语言/Rust.md) 项目时，软件包的名称应基本与生成的二进制文件名一致。请注意，打包库单元包 (library crate) 没有任何意义，只有带二进制文件的单元包 (crate) 才需要打包。对于生成多个二进制文件的包，通常使用上游单元包 (crate) 的名称较为合适。在任何情况下软件包的名称都应该小写。

## 源码

大多数 Rust 项目可以从 tarball、源归档文件（例如 GitHub Release 上的源链接）或其他已发布的源构建。另外，许多项目发布在 [crates.io](https://crates.io) 上，该网站给 cargo 提供了稳定的 URL 下载方案。如果需要，[PKGBUILD#source](../包开发/PKGBUILD.md#source) 可使用以下模板：

```
source=("$pkgname-$pkgver.tar.gz::https://static.crates.io/crates/$pkgname/$pkgname-$pkgver.crate")
```

## 依赖关系

虽然有些 Rust 项目有外部依赖，但大多数项目只使用在最终二进制文件中静态链接的 Rust 生态系统库。因此，大多数项目不需要指定很多 `depends` (依赖)，只需要 `makedepends` (构建依赖)。绝大多数 Rust 项目都使用 cargo 依赖管理器构建，它会协调下载库以满足编译时的依赖关系，并执行所有必要的调用，使用真正的 Rust 编译器 `rustc` 进行编译。目前 *cargo* 和 *rustc* 都由 [rust](https://archlinux.org/packages/?name=rust)包 提供，但也有其他方法可同时获取或单独获取这两个包，包括 [rustup](https://archlinux.org/packages/?name=rustup)包。因此，大多数 PKGBUILD 都会调用 *cargo* 工具，应该直接依赖它。

```
makedepends=(cargo)
```

如果项目需要使用开发版 Rust 工具链，请使用：

```
makedepends=(cargo-nightly)
```

## 准备工作

Rust 依赖管理器 cargo 能提前下载构建项目所需的所有库。在 `prepare()` 阶段进行下载，能使后续的 `build()` 和其他阶段完全离线运行。

```
prepare() {
    export RUSTUP_TOOLCHAIN=stable
    cargo fetch --locked --target "$CARCH-unknown-linux-gnu"
}
```

其中：

- `RUSTUP_TOOLCHAIN=stable` 确保默认工具链被设置为稳定版，以防用户改变默认值。当然，如果在上游项目有特殊要求，则应被设置为开发版。此操作可避免在未使用 chroot 环境构建时受用户配置影响。另外，如果上游项目的源代码中有 `rust-toolchain` 文件或 `rust-toolchain.toml` 文件可实现此目的，则无需此步骤。

- `--locked` 确保严格遵守 `Cargo.lock` 文件中指定的版本，防止其更新依赖关系。这对[可重现构建](https://reproducible-builds.org/)很重要。

- `--target "$CARCH-unknown-linux-gnu"` 确保只获取正在构建的特定目标平台所需的依赖项，从而减少下载量（参见 [PKGBUILD#arch](../包开发/PKGBUILD.md#arch) 和 [Rust 平台支持](https://doc.rust-lang.org/nightly/rustc/platform-support.html)）。

> **注意：**在为上游源项目构建 VCS 软件包时，如果该项目在发布周期之间未保持 `Cargo.lock` 文件与 `Cargo.toml` 同步，请在运行 `cargo fetch` 之前添加 `cargo update` 。尽管结果并非完全可重现，但有关构建的其他所有方面都应符合上述内容，因为依赖关系将在构建时得到解决。

## 构建

构建 Rust 软件包。

```
build() {
    export RUSTUP_TOOLCHAIN=stable
    export CARGO_TARGET_DIR=target
    cargo build --frozen --release --all-features
}
```

其中：

- `--release` 确保使用发布模式编译 (默认使用调试模式编译)
- `--frozen` 确保 *cargo* 保持离线状态，只使用 `Cargo.lock` 文件中指定的版本和 `prepare()` 阶段缓存的版本。这在功能上与 `--locked --offline` 一致。这对[可重现构建](https://reproducible-builds.org/)很重要。
- `--all-features` 确保编译时启用软件包的所有特性。另外，如果只需启用指定特性，可使用 `--features *FEATURE1*, *FEATURE2*` 。
- `CARGO_TARGET_DIR=target` 指定输出路径为当前目录下的 *target* 目录，以防未使用 chroot 环境构建时受用户配置影响。

> **注意：**对于 Arch 仓库的软件包来说不需要这两个环境变量，因为 Arch 仓库的软件包总是在 chroot 环境下使用默认设置下构建。但为了方便 AUR 用户，此处仍提到了上述环境变量，因为 AUR 用户可能没有意识到改变用户默认设置和在构建软件包时不使用 chroot 的后果。

## 检查

大多数 Rust 项目提供了运行测试的简单方法。

```
check() {
    export RUSTUP_TOOLCHAIN=stable
    cargo test --frozen --all-features
}
```

还应该检查仓库是否是 [cargo 工作空间](https://doc.rust-lang.org/cargo/reference/workspaces.html)。只需打开 `/Cargo.toml`，检查是否包含 `[workspace]` 部分。如果包含 `[workspace]`，则应当在 `cargo test` 后添加 `--workspace`，以确保所有工作区成员的测试都能运行。

运行测试时应避免使用 `--release` 标志，否则二进制文件将会使用 [*bench* 参数](https://doc.rust-lang.org/cargo/reference/profiles.html#bench)重新编译，并覆盖 `build()` 生成的文件。或者也可以保留 `--release` 标志，但使用不同的 `CARGO_TARGET_DIR`。但需要注意的是，使用发布模式也会启用编译器优化，并禁用一些功能，如整数溢出检查和 `debug_assert!()` 宏，所以理论上可能最终问题会更少。这两种方法都会重新编译依赖项，从而使总编译时间略微增加。

## 打包

Rust 在 `target/release` 目录内构建二进制文件，将二进制文件安装到 `/usr/bin` 很容易。

```
package() {
    install -Dm0755 -t "$pkgdir/usr/bin/" "target/release/$pkgname"
}
```

如果一个软件包在 `/usr/bin` 内有多个可执行文件，可使用 find 命令：

```
package() {
    find target/release \
        -maxdepth 1 \
        -executable \
        -type f \
        -exec install -Dm0755 -t "$pkgdir/usr/bin/" {} +
}
```

### 使用 cargo install 的注意事项

有些软件包需要安装更多的文件，如手册页面或其他必要的文件。如果没有其他方式来安装这些文件，可使用 `cargo install`。在这种情况下不必使用 `build()`，因为即使该软件包已通过 `cargo build` 构建，`cargo install` 仍会强制重新构建。但仍然可使用 `prepare()` 提前获取源代码：

```
package() {
    cd "$pkgname-$pkgver"
    export RUSTUP_TOOLCHAIN=stable
    cargo install --no-track --frozen --all-features --root "$pkgdir/usr/" --path .
}
```

应始终使用 `--no-track` 参数，否则 `cargo install` 将创建不必要的文件，如 `/usr/.crates.toml` 或 `/usr/.crates2.json`。

## 软件包示例

PKGBUILD 样例请参阅软件包页面上的 *Package Actions > Source Files*。

- [cbindgen](https://archlinux.org/packages/?name=cbindgen)包
- [ripgrep](https://archlinux.org/packages/?name=ripgrep)包

## 1.15　Java 打包准则

*难度：进阶 ｜ 篇幅：9.4 KB ｜ 所属：Arch 打包准则*

将要讲到：介绍、典型 Java 应用程序的结构、在 Arch Linux 中打 Java 包

本文档定义了 Arch Linux 下打包 [Java](../../06_软件与应用/Oracle/Java.md) 程序的建议标准。Java 程序是出了名的难以在没有重叠的依赖关系的情况下干净利落地打包。本文档描述了一种处理这种情况的方法。这些指南十分灵活，以涵盖处理 Java 程序时遇到的许多不同情况。

## 介绍

Arch Linux 的打包人员似乎无法就如何处理 Java 包达成一致。官方和非官方软件库以及 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 的 [PKGBUILD](../包开发/PKGBUILD.md) 中使用了各种方法。这包括把所有东西扔到 `/opt` 里，把 Shell 脚本放在 `/usr/bin` 或把配置文件放在 `/etc/profile` 中。其他的则是放在 `/usr/share` 目录中，脚本放在 `/usr/bin` 中。许多做法在系统 `CLASSPATH` 和 `PATH` 中添加了不必要的文件。

## 典型 Java 应用程序的结构

大多数桌面 Java 应用程序结构类似。它们是由一个不依赖系统（但依赖软件包！）的安装程序安装的。这通常会把所有东西都安装在包含 `bin`、`lib`、`jar`、`conf`等子目录的目录下。一般会有一个包含主要可执行类的主 jar 文件。通常还会提供一个 Shell 脚本来运行主类，这样用户就不必直接调用 Java 解释器。这个 Shell 脚本通常相当复杂，因为它需要在不同的发行版中通用，而且经常包括不同系统的特殊情况（例如 Cygwin）。

`lib` 目录通常包含捆绑的 jar 文件来满足 Java 程序的依赖。这使用户安装程序变得简单（其中包含了所有依赖），但却是软件包开发者的噩梦。几个软件包捆绑相同的依赖会浪费空间。这在过去并不是一个大问题，当时桌面 Java 应用程序和库较少，而那些存在的应用程序和库本身往往也非常大。但现在情况不同了……

运行程序所需的其他文件通常与主 jar 文件存放在同一目录或其子目录下。由于 Java 程序不知道它们的类是从哪里加载的，所以通常需要在这个目录中运行（即 Shell 脚本应 `cd` 进入该目录），或者设置一个环境变量来指示这个目录的位置。

## 在 Arch Linux 中打 Java 包

在 Arch 中打包 Java 应用程序将会比现在更费劲。然而，这是值得的，它可以使文件系统更干净，捆绑的依赖更少（随着越来越多的Java 库被重构为单独的包，打包会变得更容易）。在创建 Arch Linux Java 包时，应遵循以下准则：

- 如果 Java 库有一个通用的名字，那么包的名字应该在前面加上 `java-` 以与其他库区分。对于命名独特的包（如 JUnit）、终端用户程序（如 Eclipse）或可以用其他前缀唯一描述的库（如 jakarta-commons-collections 或 apache-ant），则不需要这样做。

- 将所有随程序分发的 jar 文件（而不是其他文件）放在 `/usr/share/java/*程序*`目录下。这包括随程序分发的所有依赖 jar 文件。然而，应该努力将常见的或大型的依赖放到单独的包中。这只有在程序不依赖某个特定版本的库时才能实现。

: 这个规则使得迭代重构依赖关系成为可能。也就是说，软件包和它的所有依赖可以先放在一个目录中。经过测试后，主要的依赖可以一个一个地被重构出来。请注意，有些应用程序在主 jar 文件中包括捆绑的依赖。也就是说，他们将捆绑的依赖解包，并将其包含在主 jar 中。这样的依赖通常非常小，重构它们没有什么意义。

- 如果程序是要由用户运行的，编写一个自定义的 Shell 脚本运行主 jar 文件。这个脚本应该放在 `/usr/bin` 中。库一般不需要 Shell 脚本。从头开始编写 Shell 脚本，不要使用程序捆绑的脚本。删除检查自定义环境（如 Cygwin）的代码，以及试图确定 `JAVA_HOME` 是否已经设置的代码（Arch [不使用](../../06_软件与应用/Oracle/Java.md#安装) `JAVA_HOME`，而是使用 `archlinux-java` 来设置 `/usr/bin/java` 符号连接）。

: 寻找 jar 文件的脚本应该类似这样：
: ```
#!/bin/sh
exec /usr/bin/java -jar '/usr/share/java/程序/程序.jar' "$@"
```

: 寻找单个类文件的应该类似这样：
: ```
#!/bin/sh
exec /usr/bin/java '/usr/share/java/程序/程序类名' "$@"
```

- 使用 `-cp` 选项为 Java 解释器设置 `CLASSPATH`，除非有明确的理由不这样做（即：`CLASSPATH` 用于插件机制）。`CLASSPATH` 应该包括 `/usr/share/java/*程序*`目录下的所有 jar 文件，以及已被重构到其他目录中的依赖库的 jar 文件。可以使用类似以下代码：

: ```
for name in /usr/share/java/程序/*.jar ; do
  CP=$CP:$name
done
CP=$CP:/usr/share/java/dep1/dep1.jar
java -cp $CP 程序.java.MainClass
```

- 确保 Shell 脚本可执行！

- 随软件包一起分发的其他文件应存放在 `/usr/share` 下以该软件包命名的目录中。你可能需要在 Shell 脚本中的 `项目_HOME` 等变量中设置该目录的位置。本准则假设程序希望所有文件都在同一目录下（这是 Java 软件包的标准做法）。如果配置文件更适合放在其他地方（例如，把日志放在 `/var/log` 中），则可以这样做。

: 请记住，在某些系统上 `/usr` 可能被挂载为只读。如果应用程序需要写入共享目录中的文件，它们可能需要被重新定位到`/etc`、`/var` 或用户的主目录。

- 与其他 Arch Linux 软件包的标准一样，如果遵守上述标准需要做大量的工作，则应以其偏好的方式安装软件包，并将生成的目录放在 `/opt` 中。这对于捆绑 JRE、包含自定义版本的依赖或做其他奇怪和痛苦的事情的程序很有用。

### 多个API实现

如果软件包分发了常用的 API 实现（比如 jdbc 驱动），则应该把库放在 `/usr/share/java/*API_名称*` 下。这样，允许用户选择实现的应用程序会知道在哪里寻找它们。这个位置只用于库包本身。如果这样的实现是应用程序分发的一部分，**不要**将这个 jar 文件放在公共位置，而是使用普通的包结构。

### 目录结构示例

为了说明问题，下面是一个假想的名为 `foo` 程序的目录结构示例。由于 `foo` 是一个常见的名字，所以软件包被命名为 `java-foo`，但注意目录结构中并没有体现：

- `/usr/share/java/foo/`
- `/usr/share/java/foo/foo.jar`
- `/usr/share/java/foo/bar.jar`（`java-foo` 包含的依赖）
- `/usr/share/foo/`
- `/usr/share/foo/*.*`（`java-foo` 需要的一些文件）
- `/usr/bin/foo`（可执行的 Shell 脚本）

### 依赖

Java packages might specify `java-runtime` or `java-environment` as dependency, based on what they need. For most packages, `java-runtime` (*Java Runtime Environment*) is what is needed to simply run software written in *Java*. `java-environment` (*Java Development Toolkit*) is needed by packages that will need to compile *Java* source code into bytecode.

更多信息参见 [Java](../../06_软件与应用/Oracle/Java.md)。

## 1.16　交叉编译工具打包准则

*难度：进阶 ｜ 篇幅：9.7 KB ｜ 所属：Arch 打包准则*

将要讲到：重要提示、版本兼容性、构建交叉编译器、软件包命名、放置文件、示例

本页描述了如何打包[交叉编译器](https://zh.wikipedia.org/wiki/%E4%BA%A4%E5%8F%89%E7%BC%96%E8%AF%91%E5%99%A8)工具链。另一种交叉编译的方法是在多种架构上使用 distcc。参见 [Distcc#使用 distcc 交叉编译](../../03_网络与服务/分布式计算/Distcc.md#使用_distcc_交叉编译)。

> **提示：**打包交叉编译器还可使用 [crosstool-ng](https://crosstool-ng.github.io/)，以完全自动化的方式创建自己的工具链。详情请见 [crosstool-ng](https://archlinux.org/packages/?name=crosstool-ng)包。

## 重要提示

受以下软件包启发，本文介绍新的打包方法：

- [mingw-w64-gcc](https://archlinux.org/packages/?name=mingw-w64-gcc)包 以及 `mingw-w64-*` 系列的其他软件包
- [arm-none-eabi-gcc](https://archlinux.org/packages/?name=arm-none-eabi-gcc)包 以及 `arm-none-eabi-*` 系列的其他软件包
- `arm-wince-cegcc-*` 系列的其他软件包

## 版本兼容性

> **警告：**使用不兼容版本的软件包编译工具链必然会失败。一般来说，不同版本之间都不兼容。

建议按照以下策略选择 gcc、binutils、内核和 C 库的兼容版本：

- 总则：
    - gcc 和 binutils 的版本相关，建议使用同时发布的版本；
    - 建议使用最新的内核头文件来编译 libc，但使用 `--enable-kernel`（只保证适用于 glibc，其他 C 库可能约定不同）能强制在旧内核上工作；
- [官方仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)：可能需要额外的修正，但 Arch Linux（或其特定架构的分支）使用的版本很可能可以协同工作；
- 软件文档：所有 GNU 软件都有 `README` 和 `NEWS` 文件，涵盖了依赖关系的最低版本要求等内容；
- 其他发行版：同样也进行交叉编译
- [https://trac.clfs.org](https://trac.clfs.org) 涵盖了构建交叉编译器的必要步骤以及一些依赖的最新版本。

## 构建交叉编译器

构建交叉编译器的一般方法是：

1.  binutils：构建为目标架构链接、处理的 cross-binutils。
2.  头文件：安装目标架构的 C 库和内核头文件。
    1.  参考 [linux-api-headers](https://archlinux.org/packages/?name=linux-api-headers)包，并将 `ARCH=*target-architecture*` 参数传给 *make*
    2.  打包 libc 头文件 ([此处](https://sourceware.org/legacy-ml/crossgcc/2003-06/msg00170.html)描述了 Glibc 的处理方法)
3.  gcc 第 1 阶段：构建基本的（第 1 阶段）gcc 交叉编译器。这将被用来编译 C 库，但是几乎不能编译其他任何东西（因为无法链接不存在的 C 库）。
4.  libc：构建交叉编译的 C 库（使用第 1 阶段的交叉编译器）。
5.  gcc 第 2 阶段：构建完整的（第 2 阶段）C 交叉编译器。

头文件和 libc 的源代码因平台而异。

> **提示：**根据需求的不同，具体操作步骤也不尽相同。例如，如果想创建内核和 glibc 版本与 Arch Linux 系统相同的 "克隆版"，可不构建头文件直接将 `--with-build-sysroot=/` 传给 `configure`。

## 软件包命名

软件包名称*不*应以 `cross-` 开头（之前建议使用前缀 `cross-`，但可能由于包名太长，在正式的软件包中未被采用），而应由软件包名称组成，前缀为 [GNU triplet](https://wiki.debian.org/Multiarch/Tuples)，不含厂商字段或在厂商字段中含 "unknown"。例如：`arm-linux-gnueabihf-gcc`。如果存在更短的命名规则（例如：`mips-gcc`），可以使用但并不建议。

## 放置文件

最新版本的 gcc 和 binutils 对系统根目录和库使用不冲突的路径。可执行文件应放在 `/usr/bin/` 中。为防止冲突，应在所有文件前加上架构名称。

通常来说，`./configure` 应至少含以下参数：

```
_target=your_target
_sysroot=/usr/lib/${_target}
...
./configure \
    --prefix=${_sysroot} \
    --sysroot=${_sysroot} \
    --bindir=/usr/bin
```

其中 `*your_target*` 可自行修改，例如修改为 "i686-pc-mingw32"。

## 示例

此处以 MinGW 的 binutils 的 PKGBUILD 为例。需要注意的是：

- 指定交叉编译环境的根目录
- 使用 `${_pkgname}` 、`${_target}` 和 `${_sysroot}` 能使代码更易读
- 删除重复或冲突的文件

```
# Maintainer: Allan McRae <allan@archlinux.org>

# cross toolchain build order: binutils, headers, gcc (pass 1), w32api, mingwrt, gcc (pass 2)

_target=i686-pc-mingw32
_sysroot=/usr/lib/${_target}

pkgname=${_target}-binutils
_pkgname=binutils
pkgver=2.19.1
pkgrel=1
pkgdesc="MinGW Windows binutils"
arch=('i686' 'x86_64')
url="http://www.gnu.org/software/binutils/"
license=('GPL')
depends=('glibc>=2.10.1' 'zlib')
options=('!libtool' '!distcc' '!ccache')
source=(http://ftp.gnu.org/gnu/${_pkgname}/${_pkgname}-${pkgver}.tar.bz2)
md5sums=('09a8c5821a2dfdbb20665bc0bd680791')

build() {
  cd ${srcdir}/${_pkgname}-${pkgver}
  mkdir binutils-build && cd binutils-build

  ../configure --prefix=${_sysroot} --bindir=/usr/bin \
    --with-sysroot=${_sysroot} \
    --build=$CHOST --host=$CHOST --target=${_target} \
    --with-gcc --with-gnu-as --with-gnu-ld \
    --enable-shared --without-included-gettext \
    --disable-nls --disable-debug --disable-win32-registry
  make
  make DESTDIR=${pkgdir}/ install

  # clean-up cross compiler root
  rm -r ${pkgdir}/${_sysroot}/{info,man}
}
```

> **注意：**在交叉编译工具链构建过程中，每次都从独立的目录中执行 `configure` 和 *make* 命令（所谓的树外编译），并在 [PKGBUILD](../包开发/PKGBUILD.md) 发生变化后删除整个 `src` 目录（即使变化很小）。

## 疑难解答

### 为什么不安装到 /opt？

有以下两个原因：

1.  首先，根据文件系统层次结构标准 (FHS)，这些文件只位于 `/usr` 的某个位置。
2.  其次，安装到 `/opt` 是退而求其次的选择。

### "路径外的可执行文件 (out-of-path executables)" 是什么？

这看起来很奇怪，但能让交叉编译更容易。有时项目的 Makefile 不使用 `CC` 或其他变量，而是直接使用 **gcc** 。在尝试交叉编译这样的项目时，编辑 Makefile 可能会很麻烦。然而，将 `$PATH` 改为优先使用我们的可执行文件即可解决此问题，只需将 `make` 改为执行 `PATH=/usr/*arch*/bin/:$PATH make`。

## 故障排除

### 编译失败但没有明确的提示应该如何解决？

如果是在运行 `configure` 时发生的错误，参见 `$srcdir/*pkgname* -build/config.log`。如果是在编译过程中发生的错误，请向上滚动控制台日志或搜索 "error" 一词。

### \[error message\] 错误意味着什么？

很可能犯了一些不明显的错误：

- 配置标志位过多或过少。尝试使用已证明有效的标志位。
- 依赖关系被破坏。例如，binutils 文件放错位置或丢失，可能导致 gcc 配置过程中出现晦涩难懂的错误信息。
- 未在 `build()` 函数中添加 `export CFLAGS=""`（参见 GCC Bugzilla 中的 [bug 25672](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=25672)）。
- 某些 `--prefix` 或 `--with-sysroot` 参数组合可能要求目录可写。
- 系统根目录缺少内核头文件或 libc 头文件。
- 如果通过搜索引擎仍无法解决，建议立刻放弃当前配置，尝试更稳定、更成熟的配置。

### 为什么文件安装位置会不正确？

执行 `make install` 命令的各种方法会产生不同的结果。例如，有些 make 目标可能不提供 `DESTDIR` 支持，而是要求使用`install_root`。对于 `tooldir`、`prefix` 和其他类似参数也是如此。有时提供参数而不是环境变量，例如

```
./configure CC=arm-elf-gcc
```

而不是

```
CC=arm-elf-gcc ./configure
```

反之亦然，可能会导致不同的结果（通常是因为 configure/make 递归地调用自身）。

## 另请参阅

- [https://wiki.osdev.org/GCC_Cross-Compiler](https://wiki.osdev.org/GCC_Cross-Compiler) (English)

## 1.17　Arch 打包准则/安全

*难度：进阶 ｜ 篇幅：10.3 KB ｜ 所属：Arch 打包准则*

将要讲到：用法、RELRO、Stack canary、NX、PIE、RPATH/RUNPATH

本文描述了适用于 Arch Linux 软件包的打包安全指引。对于 C/C++ 项目，可以为编译器和链接器应用安全加固选项。Arch Linux 默认会启用 [PIE](https://en.wikipedia.org/wiki/Position-independent_code#PIE)、[FORTIFY_SOURCE](https://www.redhat.com/en/blog/enhance-application-security-fortifysource)、栈保护、[nx](https://en.wikipedia.org/wiki/NX_bit) 和 [relro](https://www.redhat.com/en/blog/hardening-elf-binaries-using-relocation-read-only-relro)。

## 用法

可以使用 [checksec](https://archlinux.org/packages/?name=checksec)包 检查强化加固保护选项：

```
$ checksec --file=/usr/bin/cat
```

> **提示：**[Namcap](../../00_关于与社区/Arch%20项目/Namcap.md) 也可以报告本文描述的一些安全问题。

## RELRO

RELRO 是用于加固 ELF 二进制文件和进程的一种通用缓解技术。在程序加载时，链接器需要写入几个 ELF 内存区段，但在将控制权交给程序前，可以将这些区段设为只读，由此防止攻击者覆盖掉部分 ELF 区段。有两种 RELRO 模式：

- 部分 RELRO（`-Wl,-z,relro`）：程序加载后部分区段被设为只读，但 GOT（`.got.plt`）仍为可写。
- 完全 RELRO（`-Wl,-z,now`）：程序加载时解析所有动态符号，使得整个 GOT 都可被设为只读。

如果应用汇报使用了部分 RELRO，需检查构建工具是否使用了我们传递的 LDFLAGS，以及是否允许覆写 LDFLAGS。对于 Go 软件包，需检查构建方法是否将 `build.go` 作为了纯 golang Makefile 替代使用，该方法不允许传递 LDFLAGS。

### Haskell

暂不清楚如何为 Haskell 实现完全 RELRO。

### Go

参见 [Go 语言软件打包准则#Flags and build options](Go%20语言软件打包准则.md#Flags_and_build_options)。

## Stack canary

[Stack canary](https://en.wikipedia.org/wiki/Stack_canary)（又称栈警惕标志）由编译器添加在栈的缓冲区和控制数据之间。如果该已知值被破坏，就代表出现了缓冲区溢出，运行中的程序就会报出段错误（segfaults），以防止执行潜在的错误代码。

[gcc](https://archlinux.org/packages/?name=gcc)包 包默认通过 [\--enable-default-ssp](https://gitlab.archlinux.org/archlinux/packaging/packages/gcc/-/blob/main/PKGBUILD#L91) 编译选项启用了栈保护。

## NX

### C/C++

可执行空间保护会将内存区域标记为非可执行，使得在该区域执行机器码时会出现报错。该功能使用了类似 NX 位（No-eXecute bit，禁止执行位）的硬件功能，有时也会使用软件模拟这些特性。

## PIE

### C/C++

[gcc](https://archlinux.org/packages/?name=gcc)包 包默认通过 [\--enable-default-pie](https://gitlab.archlinux.org/archlinux/packaging/packages/gcc/-/blob/main/PKGBUILD#L90) 选项为 C/C++ 启用了该特性。

### Golang

将以下标志传递给 `go build`：

```
export GOFLAGS='-buildmode=pie'
export CGO_CPPFLAGS="-D_FORTIFY_SOURCE=3"
export CGO_LDFLAGS="-Wl,-z,relro,-z,now"
```

### Haskell

将以下标志传递给 `runhaskell Setup.hs configure`：

```
--ghc-option='-pie'
```

## RPATH/RUNPATH

RUNPATH/RPATH 为包含该参数的对象提供了更多的搜索路径（适用于可执行文件和共享对象）：

```
$ objdump -x /usr/bin/perl | grep -E 'RPATH|RUNPATH'
```

如果 RPATH 的值包含了攻击者可控制的路径，那就有可能通过对应目录下的恶意库执行代码（例如 [CVE-2006-1566](https://nvd.nist.gov/vuln/detail/CVE-2006-1566) 和 [CVE-2005-4280](https://www.cvedetails.com/cve/CVE-2005-4280/)）。更多信息请参考 [Debian:RpathIssue](https://wiki.debian.org/RpathIssue)。

RPATH 是通过向 LDFLAGS 传递类似 `-Wl,-rpath -Wl,/usr/local/lib` 的参数，然后由链接器负责设置的。如果要设置 RUNPATH，请将 `--enable-new-dtags` 添加到链接器标志中。

## FORTIFY

Fortify source 是一个宏，可以为执行内存和字符串操作的函数添加缓冲区溢出保护。它会检测是否有攻击者尝试通过负责更多的字节来溢出缓冲区，并停止程序的运行。该保护项已在默认 `CPPFLAGS` 中启用：

```
makepkg.conf
```

```
CPPFLAGS="-D_FORTIFY_SOURCE=3"
```

更多信息请参考 [makepkg#配置](../../08_安全与加密/OpenPGP/Makepkg.md#配置)。

## systemd 服务

> [![](https://wiki.archlinuxcn.org/wzh/images/f/f0/Tango-go-next.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-go-next.png)**此页面或章节适合移动到 [systemd/Sandboxing](../../08_安全与加密/沙箱/Systemd_沙箱.md)。**
>
> **附注：** 内容主题与 [systemd#应用程序环境沙盒](../初始化/Systemd.md#应用程序环境沙盒) 相同，两者可被汇总到单独页面，草稿可参考 User:NetSysFire/systemd sandboxing。（在 Talk:Security#systemd unit hardening and system.conf tweaks 讨论）

如果上游不提供 [systemd](../初始化/Systemd.md) 服务文件，使得需随软件包附带，请检查是否需要使用以下 systemd 服务加固功能。Systemd 提供并为服务启用了分析安全特性的方法：

```
$ systemd-analyze security reflector.service
```

### 文件访问

可以通过限制文件系统访问来加固服务。

为执行的进程配置一个新的文件系统命名空间，并在内部挂载私有 `/tmp` 和 `var/tmp` 目录，使得其不与命名空间外部的进程共享。该方法适用于会向 `/tmp` 目录写入数据的程序：

```
PrivateTmp=true
```

ProtectSystem 参数对于执行中进程有三种只读挂载方式，其中 “full” 选项会将 `/usr`、`/boot` 和 `/etc` 目录挂载为只读。ProtectHome 会使进程无法访问 `/home`、`/root` 和 `/run/user` 目录：

```
ProtectSystem=strict
ProtectHome=true
```

PrivateDevices 会为执行中的进程配置新的 `/dev` 命名空间，其中只包含类似 `/dev/null`、`/dev/zero` 和 `/dev/random` 的 API 伪设备，不包含任何物理设备、系统内存、系统端口和其它设备。该选项可以防止进程直接向物理设备进行写入，systemd 还会为 `@raw-io` 集添加系统调用过滤器：

```
PrivateDevices=true
```

以下选项会使执行中进程无法修改可通过 `/proc/sys` 和 `/sys` 等路径访问的内核变量。ProtectControlGroups 会使 `/sys/fs/cgroup` 层次结构变为只读：

```
ProtectKernelTunables=true
ProtectControlGroups=true
```

以下方法可禁止访问部分文件路径：

```
InaccessiblePaths=/etc
```

更多信息请参考 [systemd.exec(5)](https://man.archlinux.org/man/systemd.exec.5)。

### 用户

确保执行中的进程和其子进程无法通过 [execve(2)](https://man.archlinux.org/man/execve.2) 获得新权限：

```
NoNewPrivileges=true
```

### 内存

禁止创建同时为可写和可执行的内存映射、将映射修改为可执行和创建可执行共享内存。该方法会将进程沙箱化，防止攻击者向可执行的内存部分写入数据。注意，该方法与使用 [JIT](https://en.wikipedia.org/wiki/JIT_(computing)) 的应用不兼容：

```
MemoryDenyWriteExecute=true
```

### 系统调用

锁定 [personality(2)](https://man.archlinux.org/man/personality.2) 系统调用，使内核执行域无法被修改：

```
LockPersonality=true
```

还可以限制服务的系统调用，以下命令将显示 systemd 可以过滤的系统调用：

```
$ systemd-analyze syscall-filter
```

可以使用预定义组，以下示例为使用推荐的系统服务调用白名单配置起点：

```
SystemCallFilter=@system-service
```

还可以按架构来限制系统调用，例如限制在 64 位设备上执行 32 位二进制文件（即非原生架构二进制文件）：

```
SystemCallArchitectures=native
```

### 网络

如果进程不需要访问网络，可以为进程配置一个新的网络命名空间，并只配置回环接口，以此完全禁用网络访问：

```
PrivateNetwork=true
```

如果需要网络，还可以限制 [socket(2)](https://man.archlinux.org/man/socket.2) 系统调用可使用的地址族类型，以只允许 UNIX socket 为例：

```
RestrictAddressFamilies=AF_UNIX
```

也可以对只需连接到 localhost（本机）或特定 IP 段的情况进行限制，以只允许 localhost 为例：

```
IPAddressAllow=localhost
IPAddressDeny=any
```

更多网络过滤相关的信息请参考 [systemd.resource-control(5)](https://man.archlinux.org/man/systemd.resource-control.5)。

### 其它

为执行的进程配置新的 UTS 命名空间，并禁止修改主机名或域名：

```
ProtectHostname=true
```

## 1.18　VCS 软件打包准则

*难度：进阶 ｜ 篇幅：11.0 KB ｜ 所属：Arch 打包准则*

将要讲到：包名、版本、冲突、提供与依赖、认证与安全、VCS 源代码、pkgver() 函数

[版本控制](https://zh.wikipedia.org/wiki/%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)系统可用于获取常规静态版本软件包的源代码，以及开发分支的最新（主干）版本。

> **提示：**使用 [pacman](https://archlinux.org/packages/?name=pacman)包 软件包提供的 `/usr/share/pacman/PKGBUILD-vcs.proto` 原型文件。

## 包名

除非软件包获取的是特定发布版本，否则应添加后缀 `-bzr`、`-cvs`、`-darcs`、`-git`、`-hg` 或 `-svn` 等。

## 版本

若因修改依赖关系、URL 或源代码导致生成的软件包发生变化，请将 `pkgver` 更新至最新版本。若自上次更新 `PKGBUILD` 以来 `pkgver` 未发生变化，则应递增 `pkgrel` 的值。

建议采用以下版本格式：`*发行（release）*.r*修订（revision）*`，其中 `*修订*` 是唯一标识源代码树的单调递增数字（VCS 修订号即符合此特性）。若无公开发行版本且仓库无标签，可用 0 作为发行版本号，或完全省略 `*发行*` 部分直接使用形如 `r*修订*` 的版本号。若有公开发行版本但仓库未打标签，开发者需通过解析项目文件等方式获取发行版本号。

修订号分隔符（`*修订*` 之前的 `r`）至关重要，可避免上游首次发布版本或使用不同组件数量的版本时出现的问题。例如，若在修订版 `455` 时上游决定发布 `0.1` 版本，修订号分隔符能保持版本单调性：`0.1.r456 > r454`。若无分隔符将导致单调性失效：`0.1.456 < 454`。

> **提示：**可使用 `--holdver` 选项阻止 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 更新 `pkgver`，详见 [makepkg(8)](https://man.archlinux.org/man/makepkg.8)。

## 冲突、提供与依赖

- 明确声明软件包冲突与提供，例如 [fluxbox-git](https://aur.archlinux.org/packages/fluxbox-git/)AUR 应包含：

: ```
conflicts=('fluxbox')
provides=("fluxbox=${pkgver}")
```
: > **注意：**关于版本控制的通用建议（尤其适用于 VCS 软件包），请参阅 [PKGBUILD#provides](../包开发/PKGBUILD.md#provides)。

- `replaces=()` 通常会引起不必要的问题，应避免使用。

- 在 `makedepends=()` 中包含对应的版本控制工具⸺[cvs](https://archlinux.org/packages/?name=cvs)包、[subversion](https://archlinux.org/packages/?name=subversion)包、[git](https://archlinux.org/packages/?name=git)包 等。

## 认证与安全

- 使用 cvsroot 时，优先采用 `anonymous:@` 而非 `anonymous@`，以避免输入空密码；若必须提供密码则使用 `anonymous:password@`。
- 由于源代码并非静态文件，需在 `sha256sums=()` 中通过添加 `'SKIP'` 跳过硬校验。

## VCS 源代码

> **注意：**Arch 官方不支持使用稀疏克隆（sparse clone）或浅克隆（shallow clone），相关讨论参见 [FS#34677](https://bugs.archlinux.org/task/34677) 及[邮件列表](https://lists.archlinux.org/archives/list/aur-requests@lists.archlinux.org/thread/4IM3GOPMSIW4IULKB5DTIY3FAOIQKIN6/#O3OB7N3R2G7QMRBDCPE7SYCYVS7VL5JS)。

VCS 源代码需在 `source` 数组中指定，其处理方式与其他类型源代码相同。makepkg 会将仓库克隆/检出/分支（clone/checkout/branch）到 `$SRCDEST` 目录（若未在 [makepkg.conf(5)](https://man.archlinux.org/man/makepkg.conf.5) 中设置，则默认使用 `$startdir`），然后以特定于各 VCS 的方式将其复制到 `$srcdir`。本地仓库不会被修改，因此无需使用 `-build` 目录。

`source` 数组的通用格式为：

```
source=('[folder::][vcs+]url[#fragment]')
```

- `folder`（可选）——文件夹名称，用于将默认仓库名称更改为更具意义的名称（例如替代 `trunk`），或保留先前源代码路径
- `vcs+`——当 URL 未体现 VCS 类型时需要添加，例如 `git+https://some_repo`
- `url`——远程或本地仓库的 URL
- `#fragment`（可选）——片段，用于拉取特定分支或提交。各 VCS 支持的片段格式请参阅 [PKGBUILD(5) § USING VCS SOURCES](https://man.archlinux.org/man/PKGBUILD.5#USING_VCS_SOURCES)

使用 [Git](../../07_开发与工具/版本控制系统/Git.md) 时的 `source` 数组示例：

```
source=('project_name::git+https://project_url#branch=project_branch')
```

> **警告：**请勿在 `folder` 字段中使用 `pkgver` 变量，因为该变量可能在 `pkgver()` 函数调用时被修改，导致后续函数无法访问已创建的文件夹。

## pkgver() 函数

通过专用的 `pkgver()` 函数可实现自动版本更新。这种方式能更精细地控制版本号，维护者应尽量采用有意义的版本格式。使用该函数时，仍需预先声明更改 PKGBUILD 时最新的 `pkgver` 变量值，makepkg 工具会自动调用 `pkgver()` 并更新该变量。

### Bazaar

```
pkgver() {
  cd "$pkgname"
  printf "r%s" "$(bzr revno)"
}
```

```
r830
```

### Git

基于最后提交可达的最近的带注释的标签生成版本号：

```
pkgver() {
  cd "$pkgname"
  git describe --long --abbrev=7 | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}
```

```
2.0.r6.ga17a017
```

基于最后提交可达的最近的未注释的标签生成版本号：

```
pkgver() {
  cd "$pkgname"
  git describe --long --tags --abbrev=7 | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}
```

```
0.71.r115.gd95ee07
```

若 [git-tag(1)](https://man.archlinux.org/man/git-tag.1) 名称不含连字符，可使用简化 [sed(1)](https://man.archlinux.org/man/sed.1) 表达式 `sed 's/-/.r/;s/-/./'`。

当标签包含前缀（如 `v` 或项目名）时需进行截取：

```
pkgver() {
  cd "$pkgname"
  # 移除Git标签中的'foo-'前缀
  git describe --long --abbrev=7 | sed 's/^foo-//;s/\([^-]*-g\)/r\1/;s/-/./g'
}
```

```
6.1.r3.gd77e105
```

无可用标签时使用历史提交计数：

```
pkgver() {
  cd "$pkgname"
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short=7 HEAD)"
}
```

```
r1142.a17a017
```

省略 [SHA-1](https://zh.wikipedia.org/wiki/SHA-1) 哈希，仅保留版本号与修订号（注意此时若版本号混乱，将无法快速定位到具体修订版本）：

```
git describe --long --abbrev=7 --tags | sed 's/\([^-]*\)-g.*/r\1/;s/-/./g'
```

可组合使用两种方法以支持开始无标签、后期添加标签的仓库（使用了 bash 特性）：

```
pkgver() {
  cd "$pkgname"
  ( set -o pipefail
    git describe --long --abbrev=7 2>/dev/null | sed 's/\([^-]*-g\)/r\1/;s/-/./g' ||
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short=7 HEAD)"
  )
}
```

```
0.9.9.r27.g2b039da  # 存在标签时
r1581.2b039da       # 无标签时回退方案
```

### Mercurial

```
pkgver() {
  cd "$pkgname"
  printf "r%s.%s" "$(hg identify -n)" "$(hg identify -i)"
}
```

```
r2813.75881cc5391e
```

### Subversion

```
pkgver() {
  cd "$pkgname"
  local ver="$(svnversion)"
  printf "r%s" "${ver//[[:alpha:]]}"
}
```

```
r8546
```

> **注意：**应优先采用项目正式发布版本（如果有），而非使用 `0.` 占位符。

### 回退方案

当无法从仓库提取有效版本时，可使用当前日期：

```
pkgver() {
  date +%Y%m%d
}
```

```
20130408
```

> **注意：**此方法无法唯一标识代码状态，应尽量避免使用。

## 提示与技巧

### Git 子模块

Git 子模块的处理需要特别注意。核心思路是将子模块自身的 URL 直接添加到 `sources` 数组，然后在 `prepare()` 阶段进行引用。

下游项目开发者可能不会使用与上游仓库相同的子模块命名。要查看 Git 子模块名称，需访问项目仓库中的 `.gitmodules` 文件。例如，上游开发者命名为 `*lib-dependency*` 的仓库，在下游 `.gitmodules` 中可能注册为 `*libs/libdep*` 子模块。

```
[submodule "libs/libdep"]
  path = libs/libdep
  url = https://example.org/lib-dependency/lib-dependency.git
```

```
source=("git+https://example.org/main-project/main-project.git"
        https://example.org/lib-dependency/lib-dependency.git)

prepare() {
  cd main-project
  git submodule init
  git config submodule.libs/libdep.url "$srcdir/lib-dependency"
  git -c protocol.file.allow=always submodule update
}
```

### Git 大文件存储（LFS）

Git 大文件存储（LFS）需要一些额外配置：

```
makedepends=(... 'git-lfs')
```

```
prepare() {
  git lfs install --local
  git remote add network-origin https://example.org/upstream/lfs/repo
  git lfs fetch network-origin
  git lfs checkout
}
```

这在子模块中使用 LFS 时也可用：

```
prepare() {
  git submodule init
  git config submodule.libs/libdep.url "$srcdir/lib-dependency"
  git -c protocol.file.allow=always submodule update

  git -C libs/libdep lfs install --local
  git -C libs/libdep remote add network-origin https://example.org/upstream/lfs/repo
  git -C libs/libdep lfs fetch network-origin
  git -C libs/libdep lfs checkout
}
```

### Git 校验和

当通过 `git+https://domain.invalid/repository.git#tag=v1.0.0` 引用稳定的 Git 标签或特定提交作为源时，可以在 `PKGBUILD` 中指定校验和。只需使用 `makepkg -g` 或 `updatepkgsums` 生成校验和，操作方式与其他非 Git 源相同。

## 1.19　CMake 软件打包准则

*难度：进阶 ｜ 篇幅：12.9 KB ｜ 所属：Arch 打包准则*

将要讲到：典型用法、CMake 的不良行为、前缀和库安装目录、提示和技巧、模板、范例包

本文描述了为使用 [cmake](https://archlinux.org/packages/?name=cmake)包 的软件编写 [PKGBUILD](../包开发/PKGBUILD.md) 时需遵循的标准与指引。

摘自 [CMake 网站](https://cmake.org)：

: CMake is an open-source, cross-platform family of tools designed to build, test and package software. CMake is used to control the software compilation process using simple platform and compiler independent configuration files, and generate native makefiles and workspaces that can be used in the compiler environment of your choice.

## 典型用法

典型用法是运行 `cmake` 命令，然后执行构建命令。`cmake` 命令通常会设置一些参数，检查所需的依赖关系并创建构建文件，从而使该软件可以通过诸如 `make` 和 `ninja` 的其他工具进行构建。

## CMake 的不良行为

由于其内部的生成构建文件的特性，有时 CMake 可能会以不良方式运行。因此，在为基于 CMake 的软件编写 [PKGBUILD](../包开发/PKGBUILD.md) 时，应注意一些步骤。

### CMake 可能会自动覆盖默认的编译器优化标志

使用 `-DCMAKE_BUILD_TYPE=Release` 选项运行 CMake 是很常见的。一些上游项目甚至无意中在其构建指令中包含此选项，但这会产生不希望的行为。

每个构建类型都会导致 CMake 自动将一组标志附加到 `CFLAGS` 和 `CXXFLAGS`。当使用通用 `Release` 构建类型时，它会自动附加 `-O3` 编译器优化标志，这将覆盖当前为 `-O2`（在 [makepkg 配置文件](../../08_安全与加密/OpenPGP/Makepkg.md#配置)中定义）的默认 Arch Linux 标志。这是不可取的，因为它偏离了 Arch Linux 的目标优化级别。

#### 关于 -O3 的注意事项

使用 `-O3` 不能保证软件的性能会更好，有时甚至会减慢程序的速度。在某些情况下，它也会破坏软件。Arch Linux 开发者选择 `-O2` 作为目标优化级别是经斟酌后的结果，不应进行改动。除非你确切知道自己在做什么，或者如果上游明确告诉或暗示需要 `-O3`，否则应该避免在我们的软件包中使用它。

#### 修复自动优化标志覆盖

由于 CMake 的灵活性，以 100% 完全解决这个问题并不简单。请注意，不存在可适用于所有情况的标准解决方案。本节将讨论可能的解决方案和应遵守的一些要点。

默认的 CMake 构建类型是 `None`，默认情况下它不会向 `CFLAGS` 和 `CXXFLAGS` 附加任何标志，因此只要省略 `CMAKE_BUILD_TYPE` 就默认使用 `None` 类型。但请注意，忽略此选项并不能保证解决问题，因为如果命令行中未设置 `CMAKE_BUILD_TYPE`，许多软件项目会自动将 CMake 文件中的生成类型设置为 `Release` 或其他类型。另外请注意，由于 `None` 构建类型中缺少 `NDEBUG` 定义，软件包结果中可能会有对源文件的引用，并导致 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 输出 `WARNING: Package contains reference to $srcdir` 警告。

由于默认的 `None` 构建类型在默认情况下不会向 `CFLAGS` 和 `CXXFLAGS` 追加任何标志，因此也可以使用 `-DCMAKE_BUILD_TYPE=None` 选项。一般来说，使用 `-DCMAKE_BUILD_TYPE=None` 选项比省略 `CMAKE_BUILD_TYPE` 要好，它默认不会附加任何标志，也很少有软件会为 `None` 构建类型设置不需要的标志。

不幸的是，该问题不是简单使用 `-DCMAKE_BUILD_TYPE=None` 就能解决的。当使用 `None` 构建类型修复 `-O3` 问题时，可能会遇到另一个问题。很多软件项目会在 CMake 文件中为 `Release` 构建类型定义一些必需的编译器标志（例如 `CMAKE_C_FLAGS_RELEASE` 和 `CMAKE_CXX_FLAGS_RELEASE` CMake 变量）。如果使用 `None` 构建类型，在没有这些上游定义标志的情况下编译时，此类软件可能会损坏或出现错误行为。为了确定是否缺少某些标志，需要检查 CMake 文件，或者可以比较 `make VERBOSE=1` 对于 `None` `Release` 构建类型的输出。如果 `None` 构建类型导致某些上游定义的标志丢失，该怎么办？这时存在两种情况：如果你使用了 `Release` 构建类型，那么就可能会使用到不需要的 `-O3` 标志；如果使用 `None` 构建类型，那就会丢失必需的上游定义标志。该问题不存在标准解决方法，需要逐案分析。如果上游为 `Release` 构建类型定义了 `-O2`，则可以使用 `-DCMAKE_BUILD_TYPE=Release` （参见下文）。否则，可以尝试修补 CMake 文件。

一些软件项目在其 CMake 文件中为 `Release` 构建类型硬编码了 `-O2`，因此，如果您确定 `-O2` 是正在使用的优化级别，那么在这种情况下可以安全地设置 `-DCMAKE_BUILD_TYPE=Release`。

> **警告：**
>
> - 使用 `None` 类型时，某些软件可能会崩溃。当使用 `None` 时请测试软件，检查软件是否损坏或缺少功能。
> - 某些软件可能只适用于 `Release` 构建类型。你需要实验和测试软件。

### 验证修复

通过启用生成工具的详细模式，可以验证 CMake 是否正确应用了修复。例如，当使用 `make`（这是 CMake 的默认值）时，可以通过将 `VERBOSE=1` 添加到 `make` 中（比如 `make VERBOSE=1`）来完成。这将使 `make` 能够输出正在执行的编译器命令。然后，您可以运行 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 并检查输出，看看编译器是否使用了 `-D_FORTIFY_SOURCE=2` 和 `-O2` 标志。如果在每个命令行中显示多个优化标志，则该行中的最后一个标志将是编译器使用的标志（这意味着 `-O2` 需要是最后一个优化标志才能生效）。

## 前缀和库安装目录

标准 Arch Linux `/usr` 前缀可以由 `-DCMAKE_INSTALL_PREFIX=/usr` CMake 选项指定。通常需要这样做，因为许多软件默认将文件安装到 `/usr/local` 前缀中。

一些上游项目将 CMake 文件设置为将库安装到 `/usr/lib64` 目录中。如果是这种情况，可以使用 `-DCMAKE_INSTALL_LIBDIR=lib` CMake 选项将库安装目录正确设置为 `/usr/lib`。

## 提示和技巧

### 指定目录

自从 CMake 3.13 版本开始，可以使用 `-B` 来自动创建构建目录。这样可以避免使用单独的 `mkdir` 或 `install` 命令创建生成目录。`-S` 选项指定源目录（即搜索 `CMakeLists.txt` 文件的位置），并避免在执行 `cmake` 之前使用 `cd` 进入源码树。将这两个选项结合在一起，可以快捷指定生成目录和源目录。

构建 CMake 项目一般需要使用很多选项，可以把它们放到构建函数的本地数组中进行指定。这可以避免使用反斜杠将长命令切割为多行，还可以为各选项单独进行注释：

```
PKGBUILD
```

```
build() {
  local cmake_options=(
    -B build
    -S $pkgname-$pkgver
    # Any other options required to build a project may follow
    [other_cmake_options]
  )
  cmake "${cmake_options[@]}"
  cmake --build build
}
```

### 减少可能不需要的输出

`-Wno-dev` 选项将抑制某些警告的输出，这些警告仅适用于编写 `CMakeLists.txt` 文件的上游项目开发人员。删除这些警告将使 CMake 输出更流畅，并减少检查它的负担。一般来说，打包者通常可以被安全地忽略这些警告。

### 从二进制文件中删除不安全的 RPATH 引用

有时生成的二进制文件可能在 `RPATH` 中包含不安全的引用。这可以通过在构建的包上运行 [Namcap](../../00_关于与社区/Arch%20项目/Namcap.md) 来验证，并且这是一个需要修复的安全问题。使用 `CMAKE_SKIP_INSTALL_RPATH=YES` **或** `CMAKE_SKIP_RPATH=YES` CMake 选项很有可能解决此问题。你需要尝试这两个选项，并查看哪一个选项在对应软件中可用（不需要同时使用）。

### 获取所有可用的 CMake 选项

要获取软件项目可用的所有“可见” CMake 选项，请在源码树（主 `CMakeLists.txt` 文件所在位置）中执行 `cmake -LAH`。

如果要保存输出以供以后参考，可以将其重定向到文件：

```
$ cmake -LAH >options.txt 2>&1
```

### 避免在构建时使用 FetchContent 进行下载

CMake 提供了 [FetchContent](https://cmake.org/cmake/help/latest/module/FetchContent.html) 模块，可以在构建时下载额外的资源和子项目。但在理想情况下，所有资源都应在 `sources` 数组中指定，并由 [makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 在构建之前获取。可以通过 [FETCHCONTENT_SOURCE_DIR_<uppercaseName>](https://cmake.org/cmake/help/latest/module/FetchContent.html#variable:FETCHCONTENT_SOURCE_DIR_%3CuppercaseName%3E) 选项指定需获取的文件的路径，另外还可以使用 [FETCHCONTENT_FULLY_DISCONNECTED=ON](https://cmake.org/cmake/help/latest/module/FetchContent.html#variable:FETCHCONTENT_FULLY_DISCONNECTED) 跳过所有构建时的下载，无视任何 `FetchContent` 定义。

#### 示例

假设项目需要获取的资源名为 `foo`：

```
CMakeLists.txt
```

```
FetchContent_Declare(
    foo
    URL https://example.com/foo.tar.gz
    URL_HASH SHA256=cf051bf611a94884ba5e4c2d03932d14e83875c5b77f0fdf55c404cad0e4a6e6
)
FetchContent_MakeAvailable(foo)
```

可以将该资源添加到 `sources` 数组并在生成构建文件时进行声明，而不是在构建时下载：

```
PKGBUILD
```

```
sources=(
    ...
    "https://example.com/foo.tar.gz"
)

sha256sums=(
    ...
    "cf051bf611a94884ba5e4c2d03932d14e83875c5b77f0fdf55c404cad0e4a6e6"
)
```

```
$ cmake -B build -S "$pkgname-$pkgver" -DFETCHCONTENT_FULLY_DISCONNECTED=ON -DFETCHCONTENT_SOURCE_DIR_FOO="$srcdir/foo"
```

## 模板

下面是 `build()` 函数的通用模板，它是基于 CMake 的包的起点。假设软件基于 C 和 C++，且在 CMake 文件中没有为 `Release` 构建类型定义必需编译器标志：

> **注意：**check 函数只在 `CMakeLists.txt` 中使用了 `enable_testing()` 和/或 `add_test()` 功能时可用。

```
PKGBUILD
```

```
build() {
  local cmake_options=(
    -B build
    -S $pkgname-$pkgver
    -W no-dev
    -D CMAKE_BUILD_TYPE=None
    -D CMAKE_INSTALL_PREFIX=/usr
  )
  cmake "${cmake_options[@]}"
  cmake --build build
}

check() {
  local excluded_tests=""
  local ctest_flags=(
    --test-dir build
    # show the stdout and stderr when the test fails
    --output-on-failure
    # execute tests in parallel
    --parallel $(nproc)
    # exclude problematic tests
    --exclude-regex "$excluded_tests"
  )
  ctest "${ctest_flags[@]}"
}

package() {
  DESTDIR="$pkgdir" cmake --install build
}
```

不要忘记将 [cmake](https://archlinux.org/packages/?name=cmake)包 添加到 [makedepends](../包开发/PKGBUILD.md)。

## 范例包

- [i2pd](https://archlinux.org/packages/?name=i2pd)包
- [libmysofa](https://archlinux.org/packages/?name=libmysofa)包

## 另请参见

- [CMake 文档](https://cmake.org/documentation/)
- [最新版本的文档参考](https://cmake.org/cmake/help/latest/)
- [维基百科文章](https://zh.wikipedia.org/wiki/CMake)
- [cmake(1)](https://man.archlinux.org/man/cmake.1)

## 1.20　Python 打包准则

*难度：进阶 ｜ 篇幅：15.1 KB ｜ 所属：Arch 打包准则*

将要讲到：包名、架构、源代码、安装方式、测试、提示和技巧

本文档描述了使用 [PKGBUILD](../包开发/PKGBUILD.md) 来为 [Python](../../07_开发与工具/编程语言/Python.md) 程序进行打包时须遵守的标准和指引。

## 包名

对于 [Python 3](../../07_开发与工具/编程语言/Python.md#安装) 的**库**模块，或与 Python 生态紧密耦合的**应用程序**（如 *pip* 和 *tox*），使用 `python-*模块名或应用程序名*` 命名。对于其他**应用程序**，则仅使用程序原名。

> **注意：**包名应全为小写。

## 架构

参见 [PKGBUILD#arch](../包开发/PKGBUILD.md#arch)。

包含 C 扩展的 Python 软件包与架构相关。反之则通常无关于架构。

使用 [setuptools](https://setuptools.pypa.io/) 构建的软件包通过在 `setup.py` 中定义 `ext_modules` 关键字来声明其使用的 C 扩展。

## 源代码

> **注意：**根据 [RFC0020](https://rfc.archlinux.page/0020-sources-for-python-packaging/)，默认应使用上游提供的源代码压缩包，而非 PyPI 提供的 sdist 压缩包。

PyPI 网站提供的下载链接包含不可预测的哈希值，每次更新软件包时需重新从 PyPI 获取。这导致其不适合直接用于 PKGBUILD。PyPI [提供](https://github.com/pypa/pypi-legacy/issues/438#issuecomment-226940730)了以下稳定方案：[PKGBUILD#source](../包开发/PKGBUILD.md#source) `source=()` 数组应使用以下 URL 模板：

**源代码包：**
: `https://files.pythonhosted.org/packages/source/${_name::1}/${_name//-/_}/${_name//-/_}-$pkgver.tar.gz`
**纯 Python wheel 包**
: `https://files.pythonhosted.org/packages/py2.py3/${_name::1}/$_name/${_name//-/_}-$pkgver-py2.py3-none-any.whl`（同时兼容 Python 2 和 Python 3）
: `https://files.pythonhosted.org/packages/py3/${_name::1}/$_name/${_name//-/_}-$pkgver-py3-none-any.whl` （仅兼容 Python 3）
: 注意分发包名称（distribution name，在 PyPI 注册的名称）可能包含连字符，但在 wheel 文件名中需转换为下划线。
**架构相关的 wheel 包**
: 可通过追加下划线和架构名添加额外数组（如 `source_x86_64=('...')`）。另可使用 `_py=cp310` 避免重复 Python 版本：
: `https://files.pythonhosted.org/packages/$_py/${_name::1}/$_name/${_name//-/_}-$pkgver-$_py-${_py}m-manylinux1_x86_64.whl`

注意使用自定义变量 `**_name**` 代替 `pkgname`，因 Python 软件包通常带有 `python-` 前缀。该变量可通过以下方式定义：

```
_name=${pkgname#python-}
```

## 安装方式

Python 中的包通常使用 Python 专用的包管理器（如 [pip](https://pip.pypa.io/)）进行安装，这类工具会从在线仓库（通常是 [PyPI](https://pypi.org/) —— Python 软件包索引）获取软件包并追踪相关文件。

然而，在使用 `PKGBUILD` 管理 Python 软件包时，需要将 Python 包“安装”到临时位置 `*$pkgdir*/usr/lib/python*<Python 版本>*/site-packages/*$pkgname*`。

对于使用[标准元数据（standard metadata）](https://www.python.org/dev/peps/pep-0517/)在 `pyproject.toml` 中指定了构建后端（build backend）的 Python 包，使用 [python-build](https://archlinux.org/packages/?name=python-build)包 和 [python-installer](https://archlinux.org/packages/?name=python-installer)包 是完成上述“安装”过程最容易的方式。 旧版本软件包可能未声明使用 setuptools，仅提供需手动调用的 `setup.py`。

> **注意：**必须将元数据中的依赖关系声明在 `depends` 数组中，否则相关依赖不会被安装。

### 基于标准（PEP 517）的安装方式

> **提示：**当从上游提供的源代码 tarball 构建时，若项目依赖 git 生成版本字符串，在构建 wheel 前需根据使用的工具将特定的环境变量设为 `$pkgver`：
>
> - [python-flit-core](https://archlinux.org/packages/?name=python-flit-core)包、[python-hatch-vcs](https://archlinux.org/packages/?name=python-hatch-vcs)包 或 [python-setuptools-scm](https://archlinux.org/packages/?name=python-setuptools-scm)包：`SETUPTOOLS_SCM_PRETEND_VERSION`
> - [python-pbr](https://archlinux.org/packages/?name=python-pbr)包：`PBR_VERSION`
> - [python-pdm-backend](https://archlinux.org/packages/?name=python-pdm-backend)包：`PDM_BUILD_SCM_VERSION`

基于标准的工作流程很简单：使用 [python-build](https://archlinux.org/packages/?name=python-build)包 构建一个 wheel，再使用 [python-installer](https://archlinux.org/packages/?name=python-installer)包 将其安装到 `$pkgdir`：

> **注意：**除 [python-build](https://archlinux.org/packages/?name=python-build)包 和 [python-installer](https://archlinux.org/packages/?name=python-installer)包 外，还需将软件包使用的[构建后端（build backend）](https://packaging.python.org/en/latest/tutorials/packaging-projects/#choosing-a-build-backend)添加至 `makedepends`。仓库中所有可用的构建后端均属于 [python-build-backend](https://archlinux.org/groups/x86_64/python-build-backend/)包组。请检查项目的 `pyproject.toml` 文件中 `build-system.build-backend` 配置项的值，该值即为此项目实际使用的构建后端，若未配置则默认使用 [python-setuptools](https://archlinux.org/packages/?name=python-setuptools)包。

```
makedepends=(python-build python-installer python-wheel)

build() {
    cd $_name-$pkgver
    python -m build --wheel --no-isolation
}

package() {
    cd $_name-$pkgver
    python -m installer --destdir="$pkgdir" dist/*.whl
}
```

其中：

- `--wheel` 表示仅构建 wheel 文件，不生成源代码分发包
- `--no-isolation` 表示使用系统已安装的包（含 `depends` 中声明的依赖）进行构建，默认会创建隔离的虚拟环境执行构建
- `--destdir="$pkgdir"` 可防止直接尝试安装到宿主系统（而非软件包目录），避免权限错误
- 可将 `--compile-bytecode=……` 或 `--no-compile-bytecode` 传递给 `installer`，但默认值较为合理，无需手动指定

> **注意：**不建议跳过 `build` 阶段而直接将 `.whl` 文件放入 `source` 数组，应优先采用源代码构建。仅在无法通过源码构建时（例如项目**仅**提供 wheel 源）才应使用此方式。

> **提示：**若软件包为 [VCS 包](VCS%20软件打包准则.md)（`python-……-git`），请在 `prepare` 函数中添加 `git -C "${srcdir}/${pkgname}" clean -dfx` 命令。此操作可清除旧版 wheel 及其他构建产物，避免后续问题。另请参阅 [setuptools](https://github.com/pypa/setuptools/issues/1347) 与 [Poetry](https://github.com/python-poetry/poetry/issues/1329) 的上游 issue。

### 使用 setuptools 或 distutils 的安装方式

若项目中不存在 `pyproject.toml` 文件，或该文件未包含 `[build-system]` 表，则说明项目使用遗留格式，即通过 *setup.py* 文件调用 *setuptools* 或 *distutils.core* 的 `setup` 函数。

此类软件包通常仍可使用上述 [python-build](https://archlinux.org/packages/?name=python-build)包 和 [python-installer](https://archlinux.org/packages/?name=python-installer)包 方法构建安装（推荐方式），但需在 `makedepends` 中添加 [python-setuptools](https://archlinux.org/packages/?name=python-setuptools)包。

通过直接运行 *setup.py* 的旧方式（见下）仍可构建安装，但此方法已弃用，仅建议在 PEP 517 兼容方式因故不可用时作为备选方案。

需注意，使用此方法构建时会在 `package` 阶段输出以下警告：

```
SetuptoolsDeprecationWarning: setup.py install is deprecated.
```

另请注意，Python 3.12 及后续版本的标准库中已移除 *distutils*。对于仍使用 *setup.py* 的项目，必须在 `makedepends` 中添加 [python-setuptools](https://archlinux.org/packages/?name=python-setuptools)包（该软件包提供了自带的 *distutils* 实现）。

```
makedepends=('python-setuptools')

build() {
    cd $_name-$pkgver
    python setup.py build
}

package() {
    cd $_name-$pkgver
    python setup.py install --root="$pkgdir" --optimize=1
}
```

其中：

- `--root="$pkgdir"` 作用同前文 `--destdir`
- `--optimize=1` 预生成优化字节码文件（*.opt-1.pyc*）而非在宿主系统按需生成，以便由 [pacman](../包管理器/Pacman.md) 追踪这些文件
- 添加 `--skip-build` 可以跳过不必要的重复构建步骤（如 `build()` 函数已执行过的构建过程）

若软件包使用 [python-setuptools-scm](https://archlinux.org/packages/?name=python-setuptools-scm)包，构建时可能报错如下：

```
LookupError: setuptools-scm was unable to detect version for /build/python-jsonschema/src/jsonschema-3.2.0.

Make sure you're either building from a fully intact git repository or PyPI tarballs. Most other sources (such as GitHub's tarballs, a git checkout without the .git folder) don't contain the necessary metadata and will not work.
```

需将 `SETUPTOOLS_SCM_PRETEND_VERSION` 环境变量设为 `$pkgver` 以完成构建：

```
export SETUPTOOLS_SCM_PRETEND_VERSION=$pkgver
```

## 测试

> **注意：**
>
> - 避免使用 `tox` 运行测试套件，因其明确设计用于依据从 PyPI 下载的可复现的、固定的配置环境测试，而**不会**测试软件包实际安装后的版本，若使用则将使 *check* 函数失去意义
> - 避免在 **checkdepends** 中添加用于代码检查（lint）、覆盖率或类型检查的 pytest 插件（详见 [#禁用 pytest 附加选项](#禁用_pytest_附加选项)章节）。这些工具会增加引导难度且非发行版打包必需，因其不验证功能正确性
> - 在为测试配置自定义 `PYTHONPATH` 时，避免使用相对路径，否则可能导致问题出现。请使用绝对路径。

大多数提供测试套件的 Python 项目使用 unittest 运行器、nosetests 或 pytest（分别由 [python](https://archlinux.org/packages/?name=python)包、[python-nose](https://archlinux.org/packages/?name=python-nose)包 和 [python-pytest](https://archlinux.org/packages/?name=python-pytest)包 提供），通过检测特定格式的文件/目录名和类/函数名（如含 `test` 的命名）自动发现测试套件。通常直接运行 `nosetests` 或 `pytest` 即可执行测试套件。

```
check(){
    cd $_name-$pkgver

    # 使用内置的 unittest
    python -m unittest discover -vs .

    # 使用 nosetests
    nosetests

    # 使用 pytest
    pytest
}
```

若存在已编译的 C 扩展模块，测试时需通过 `$PYTHONPATH` 指定与当前 Python 主次版本匹配的构建路径以正确加载模块。

```
check(){
    cd $_name-$pkgver
    local python_version=$(python -c 'import sys; print("".join(map(str, sys.version_info[:2])))')

    # 使用内置的 unittest
    PYTHONPATH="$PWD/build/lib.linux-$CARCH-cpython-$python_version" python -m unittest discover -vs .

    # 使用 nosetests
    PYTHONPATH="$PWD/build/lib.linux-$CARCH-cpython-$python_version" nosetests

    # 使用 pytest
    PYTHONPATH="$PWD/build/lib.linux-$CARCH-cpython-$python_version" pytest
}
```

## 提示和技巧

### 获取 Python 版本号

在准备、构建、测试或安装过程中，如需引用系统 Python 的主次版本号（如 `3.9` 或 `3.10`），请勿硬编码版本号，而应通过 Python 解释器动态获取并存储至局部变量：

```
check(){
  local python_version=$(python -c 'import sys; print(".".join(map(str, sys.version_info[:2])))')
  ...
}
```

### 获取 site-packages 路径

在构建、测试或安装过程中，如需引用系统的 `site-packages` 目录，请勿硬编码路径，而应通过 Python 解释器动态获取并存储至局部变量：

```
check(){
  local site_packages=$(python -c "import site; print(site.getsitepackages())")
  ...
}
```

### 避免在 site-package 中放置测试目录

避免在 `site-packages/` 下直接安装名为 `tests/` 的目录（即 `/usr/lib/python*X*.*Y*/site-packages/tests/`），否则可能引发软件包冲突。某些使用 *setuptools* 的 Python 项目存在错误配置，可能将其测试目录作为顶层 Python 包包含。若发现此类问题，请向项目提交 issue 请求修复，例如[此 issue](https://github.com/Lightning-AI/lightning/issues/10335)。

### 禁用 pytest 附加选项

运行 pytest 时，通常应禁用额外插件。特别是代码检查（lint）和覆盖率（coverage）插件在打包场景中可能适得其反，因其行为变更可能导致测试失败。

推荐通过命令行覆盖配置选项（而非修改 pytest 配置文件）来禁用 `addopts` 等参数，以减少维护成本。要清空所有附加选项，可使用：

```
pytest -o addopts=""
```

### 修复 meson-python 的可复现性问题

使用 [meson-python](https://archlinux.org/packages/?name=meson-python)包 作为 PEP 517 构建后端时，其随机生成的构建目录路径会导致[可复现性问题](https://github.com/mesonbuild/meson-python/issues/703)。可通过 `-Cbuild-dir` 参数硬编码构建目录来规避此问题：

```
python -m build --wheel --no-isolation -Cbuild-dir=build
```

### 使用已安装的软件包运行测试

有些软件包需要安装后才能正常测试。在这种情况下（例如 [python-narwhals](https://archlinux.org/packages/?name=python-narwhals)包），可以将构建的软件包安装到*虚拟环境*下，并在该环境中运行测试：

```
python -m venv --system-site-packages test-env
test-env/bin/python -m installer dist/*.whl
test-env/bin/python -P -m pytest
```

## 1.21　Arch 打包准则

*难度：进阶 ｜ 篇幅：17.1 KB ｜ 所属：Arch 打包准则*

将要讲到：打包规则、软件包命名、软件包版本号、软件包依赖、软件包关联、软件源

> **Arch 打包准则**
>
> ---
>
> [32 位](32%20位软件包打包准则.md) – [安全](Arch%20打包准则_安全.md) – [CLR](CLR%20软件打包准则.md) – [CMake](CMake%20软件打包准则.md) – [DKMS](DKMS%20软件包打包准则.md) – [Eclipse](../包开发/Eclipse%20插件打包准则.md) – [Electron](Electron%20打包准则.md) – [Free Pascal](Free%20Pascal%20打包准则.md) – GNOME – [Go](Go%20语言软件打包准则.md) – [Haskell](Haskell%20打包准则.md) – [Java](Java%20打包准则.md) – [交叉编译工具](交叉编译工具打包准则.md) – [KDE](KDE%20软件打包准则.md) – Lisp – Meson – MinGW – [内核模块](../../02_系统管理与内核/内核/内核模块打包指南.md) – [Node.js](Node.js%20打包准则.md) – Nonfree – [OCaml](../包开发/OCaml%20打包准则.md) – Perl – [PHP](PHP%20打包准则.md) – [Python](Python%20打包准则.md) – [R](R%20软件打包准则.md) – Ruby – [Rust](Rust%20软件打包准则.md) – [Shell](命令行解释器（Shell）打包准则.md) – [VCS](VCS%20软件打包准则.md) – Web – [Wine](../../06_软件与应用/仿真/Wine%20打包准则.md) – [字体](字体打包指南.md)

> **翻译状态：**
>
> - 本文（或部分内容）译自 [Arch packaging standards](https://wiki.archlinux.org/title/Arch_packaging_standards)，最近一次同步于 2026-05-02，若英文版本有所[更改](https://wiki.archlinux.org/title/Arch_packaging_standards?diff=0&oldid=872747)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/Arch_packaging_standards_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

在为 [Arch Linux](../../00_关于与社区/关于%20Arch/Arch%20Linux.md) 构建软件包时，您应该遵循以下的**软件包指导原则**，尤其是当打算贡献新软件包至 Arch Linux 时。同时需要阅读 [PKGBUILD(5)](https://man.archlinux.org/man/PKGBUILD.5) 和 [makepkg(8)](https://man.archlinux.org/man/makepkg.8) [man 手册](../../07_开发与工具/命令行/Man%20手册.md)。

本页中列出的重要项将不再于其它软件包指导页中指出，这些指导页将作为以下准则的附加内容。

可在 `/usr/share/pacman/` [目录](https://gitlab.archlinux.org/pacman/pacman/-/tree/master/proto)下的 *.proto* 文件中查看 [PKGBUILD](../包开发/PKGBUILD.md) 示例。

> **注意：**提交到 [Arch 用户软件仓库 (AUR)](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 的软件包需要额外遵守 [AUR 提交准则](../../00_关于与社区/关于%20Arch/AUR%20提交准则.md)。

## 打包规则

- **永远别**将软件包安装至 `/usr/local/`

- **除非非用不可，否则绝对不要在 `PKGBUILD` 中自定义和使用新的变量**，以避免和 *makepkg* 本身的变量**冲突**。

- 在非用不可的情况下，需要**给自定义变量名前加上下划线** (`_`)。例如：

    ```
    _customvariable=
    ```

- 任何情况下都要**避免**使用 `/usr/libexec/`，应该使用 `/usr/lib/${pkgname}/`。

- 包信息文件中的 `packager` 字段可以通过修改 `/etc/makepkg.conf` 文件由编译者进行**自定义**。使用 `~/.makepkg.conf`也可以达到此目的。

- **不要使用 makepkg 子程序**（例如 `error`，`msg`，`msg2`，`plain` 和 `warning`），这些随时都可能会更改。请使用 `printf` 或 `echo` 来输出内容。

- 所有安装过程中所需输出的重要的信息，都要放到 **.install 文件中**。比如说如果某软件包需要扩展的安装步骤才能正常运行，你可以将这些步骤的介绍包含在 .install 文件中。

- **依赖包**是最容易出现错误的地方。请花时间仔细核对，例如对动态可执行文件执行 [ldd(1)](https://man.archlinux.org/man/ldd.1)，`ldd --unused --function-relocs` 和 [readelf(1) § dynamic](https://man.archlinux.org/man/readelf.1#dynamic)，检查脚本需要的工具，查看软件文档等。[namcap](../../00_关于与社区/Arch%20项目/Namcap.md) 可以帮助你，它可以解析 `PKGBUILD` 文件和构建出的软件压缩包，报告权限错误、缺少依赖、过多依赖等常见问题。

- 任何运行该软件包不需要，或者该软件包的通用功能不需要的**可选依赖**不要加入到 **depends** 中。这些信息应该加入 **optdepends** 数组，例如：

: ```
optdepends=('cups: printing support'
            'sane: scanners support'
            'libgphoto2: digital cameras support'
            'alsa-lib: sound support'
            'giflib: GIF images support'
            'libjpeg: JPEG images support'
            'libpng: PNG images support')
```
: 例子取自 [wine](https://archlinux.org/packages/?name=wine)包 软件包。这些信息在安装和升级时会自动打印，所以**不要**将这些信息加入 .install 文件。

- 在填写**软件包描述（description）**时，请不要使用下定义的方式描述包名称。比如说，“Nedit is a text editor for X11” 应当简写为 "A text editor for X11"。顺便注意保持描述在 80 个字符以内.

- 尽量保持 `PKGBUILD` 文件中**每行**不超过100字符。

- 如果可能的话，从 `PKGBUILD` 文件中**去掉空行**（没有设置变量值的行）(如 `provides`、`replaces` 等)

- 通常实践建议按照 [PKGBUILD](../包开发/PKGBUILD.md) 一文中的示例**安排 `PKGBUILD` 各变量顺序**。当然这不是强制性的，这里唯一强制要求的是满足**正确的 Bash 语法**。

- 变量名可能包含空格时，请使用**引号**，例如 `"$pkgdir"` 和 `"$srcdir"`。

- 请确保软件包的**完整性**，确保[校验变量](../包开发/PKGBUILD.md#完整性检验)包含正确的数值，可以通过 `updpkgsums` 工具进行更新。

## 软件包命名

- 软件包应当仅包含**字母或数字**以及 `@`，`.`，`_`，`+`，`-`，不能以 `-` 和 `.` 开头，所有的字母应当保持**小写**。

- 软件包的名称不应该包含上游的主版本号，例如如果上游软件包是 libfoo v2.3.4，不应该命名为 libfoo2。这样在上游发布新的大版本时，就可以保留软件包名和依赖可以保持不变。只有个别软件包是例外，例如图形库 GTK 和 Qt。这些软件升级后，应用程序需要花很长时间和经历才能移植完成，所以系统需要同时安装多个版本，软件包名应该包含大版本号，比如 gtk3、gtk4、qt5、qt6。如果出现大部分软件包都可以同步升级，只有个别软件没有被移植的情况，可以把老的软件包命名为 libfoo1，而新的软件包继续使用 libfoo。

## 软件包版本号

- 软件包[版本号](../包开发/PKGBUILD.md#pkgver)（即 `pkgver`）应当**和作者发行版号保持一致。**
- 如果需要的话，版本号可以包含字母（例如 `2.54BETA32`）。
- **版本号里不能包含连字符**，只能允许字母、数字、下划线、点号。如果上游版本号中出现了连字符，需要将其替换为下划线。

- 软件包[发行号](../包开发/PKGBUILD.md#pkgrel)（即 `pkgrel`）**仅和 Arch 相关**. 这样用户就可以区分不同的编译版本。**发布号从1开始**，软件包被**重新（打包）**发布时 ，**发行号将增加1**。
- 当新版本发布的时候，发行号（release）自动回到1。
- 软件包发布标记和软件包版本标记**遵从同样的规则**。

## 软件包依赖

- **不要依赖 [PKGBUILD#依赖关系](../包开发/PKGBUILD.md#依赖关系) 中的[传递依存关系](https://en.wikipedia.org/wiki/Transitive_dependency)**，因为依赖更新可能会导致关系中断。
- 列出所有直接依赖库，可以通过 [find-libdeps(1)](https://man.archlinux.org/man/find-libdeps.1)（[devtools](https://archlinux.org/packages/?name=devtools)包 的组件）来完成。

## 软件包关联

- 不要将 `$pkgname` 添加到 [PKGBUILD#provides](../包开发/PKGBUILD.md#provides)，软件包无法与自身冲突。
- 在 [PKGBUILD#provides](../包开发/PKGBUILD.md#provides) 中列出所有外部共享库（如 `'libsomething.so'`），可通过 [find-libprovides(1)](https://man.archlinux.org/man/find-libprovides.1)（[devtools](https://archlinux.org/packages/?name=devtools)包 的组件）来完成。

## 软件源

- 尽可能使用 HTTPS 源：压缩包使用 `https://`，git 源使用 `git+https://`
- 尽可能使用 PGP 签名对源进行验证（如果上游对提交进行了签名，但没有对压缩包签名，可能需要使用 git 标签而不是源码压缩包进行构建。）

> [![](https://wiki.archlinuxcn.org/wzh/images/d/d6/Tango-inaccurate.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-inaccurate.png)**本文或本章节的事实准确性存在争议。**
>
> **原因：** commit# is not required in recent pacman as proper checksum is supported for git sources. See. gitea package also has been updated to use below approach（在 [Talk:Arch 打包准则](https://wiki.archlinuxcn.org/wiki/Talk:Arch_%E6%89%93%E5%8C%85%E5%87%86%E5%88%99) 中讨论）

- 使用 git 标签进行构建时，不要使用标签名，要使用 `git rev-parse` 获取到的对象哈希值

: ```
_tag=1234567890123456789012345678901234567890 # git rev-parse "v$pkgver"
source=(git+https://$url.git?signed#tag=$_tag)

pkgver() {
    cd "$pkgname"
    git describe
}
```
: 具体案例可参考 [gitea](https://archlinux.org/packages/?name=gitea)包 包。该操作的原因是，标签可以被强制推送改变指向到的提交，从而导致构建出的包产生变化。强制推送会改变标签的哈希值，从而使用标签的对象哈希值可以确保源的完整性。使用 `pkgver()` 函数可避免在没有更新 `_tag` 的情况下不小心修改掉 `pkgver`。关于 VCS 源格式的更多信息请参考 [VCS 软件打包准则#VCS 源](VCS%20软件打包准则.md#VCS_源)。

- **Do not diminish the security or validity of a package** (e.g. by removing a checksum check or by removing PGP signature verification), because an upstream release is broken or suddenly lacks a certain feature (e.g. PGP signature missing for a new release)
- 源在 `srcdir` 下的名称不能相同（可能需要在下载时进行重命名，例如 `"${pkgname}-${pkgver}.tar.gz::https://${pkgname}.tld/download/${pkgver}.tar.gz"`）
- 避免对如 sourceforge 等平台的链接使用特定镜像源进行下载，因为它们有可能会变得不可用
- 使用 SSH 密钥签名的 Git 对象（例如标签和提交等）可以使用 git 命令进行验证，用法为通过 `gpg.ssh.allowedSignersFile` 指定包含可能的签名密钥的文件。具体用例请参考 。

## 与上游协作

It is considered best-practice to work closely with upstream wherever possible. This entails reporting problems about building and testing a package.

- Report problems to upstream right away.
- Upstream patches wherever possible.
- Add comments with links to relevant (upstream) bug tracker tickets in the [PKGBUILD](../包开发/PKGBUILD.md) (this is particularly important, as it ensures, that other packagers can understand changes and work with a package as well).

It is recommended to track upstream with tools such as [nvchecker](https://archlinux.org/packages/?name=nvchecker)包, [nvrs](https://aur.archlinux.org/packages/nvrs/)AUR or [urlwatch](https://archlinux.org/packages/?name=urlwatch)包 to be informed about new stable releases.

## 目录

- **配置文件**应该放置到 `/etc` 目录。如果有多个配置文件，可以使用**子目录**，以保持 `/etc` 简洁。例如，如果包名是 `*pkg*`，那就应使用 `/etc/*pkg*`（或者其他合适的名称，比如apache使用的就是 `/etc/httpd/`）。

- 软件包文件应该安装在下列**常用目录**：

: <table class="wikitable"><tbody><tr><td><code>/etc</code></td><td><b>系统关键</b>配置文件</td></tr><tr><td><code>/usr/bin</code></td><td>二进制文件</td></tr><tr><td><code>/usr/lib</code></td><td>库</td></tr><tr><td><code>/usr/include</code></td><td>头文件</td></tr><tr><td><code>/usr/lib/<i>pkg</i></code></td><td>模块，插件等</td></tr><tr><td><code>/usr/share/doc/<i>pkg</i></code></td><td>应用程序文档</td></tr><tr><td><code>/usr/share/info</code></td><td>GNU Info 系统文件</td></tr><tr><td><code>/usr/share/licenses/<i>pkg</i></code></td><td>Application licenses</td></tr><tr><td><code>/usr/share/man</code></td><td>手册</td></tr><tr><td><code>/usr/share/<i>pkg</i></code></td><td>程序数据</td></tr><tr><td><code>/var/lib/<i>pkg</i></code></td><td>应用持久数据</td></tr><tr><td><code>/etc/<i>pkg</i></code></td><td><i>pkg</i> 应用的配置文件</td></tr><tr><td><code>/opt/<i>pkg</i></code></td><td>大的独立程序，例如 Java</td></tr></tbody></table>

- 软件包不应该在下面目录添加任何文件:
    - `/bin`
    - `/sbin`
    - `/dev`
    - `/home`
    - `/srv`
    - `/media`
    - `/mnt`
    - `/proc`
    - `/root`
    - `/selinux`
    - `/sys`
    - `/tmp`
    - `/var/tmp`
    - `/run`

## [Makepkg](../../08_安全与加密/OpenPGP/Makepkg.md) 的任务

当您使用 makepkg 构建软件包时，makepkg 会自动执行如下功能:

- 检查软件包的**依赖**和**构建依赖**是否已安装
- 从服务器中**下载源文件**
- **校验**源文件的完整性
- **解压**源文件
- 打上必要的**补丁（patch）**
- **构建**软件并安装到 fake root
- 从可执行文件中**去掉**符号标记
- 从库文件中**去掉**调试标记
- **压缩**手册页和/或 info 页
- 生成**包信息文件**（包含软件包的基本信息）
- **压缩** fake root 成为软件包文件（\*.pkg.tar.xz）
- 生成的软件包文件**保存**在指定的目录中（默认在当前目录）

## 架构

如果该软件包是针对特定架构编译的，那么 `arch` 数组应该包含 `'x86_64'`，否则使用 `'any'` 生成架构无关的包。

## 授权协议

有两种与 Arch 软件包相关的许可证：

### PKGBUILD 的许可证字段

`PKGBUILD` 中的 `license` 字段列出了被打包软件的上游许可证，注意这不是软件打包代码的许可证。该字段必须遵循 [SPDX 许可证格式](https://spdx.org/licenses/)，更多信息请参考 [PKGBUILD#license](../包开发/PKGBUILD.md#license)

### 软件打包代码许可证

软件打包代码的许可证。根据 [RFC40](https://rfc.archlinux.page/0040-license-package-sources/)，Arch Linux 指定了软件打包代码需以 [BSD Zero Clause License](https://en.wikipedia.org/wiki/BSD_licenses#0-clause_license_.28.22BSD_Zero_Clause_License.22.29) (`0BSD`) 许可证进行授权，并参照 [RFC52](https://rfc.archlinux.page/0052-reuse/) 使用 [REUSE](https://reuse.software/) 来执行这一点。

总结一下就是：

1.  在软件包源码的根目录下添加一个 `LICENSE` 文件，其内容与[该文件](https://gitlab.archlinux.org/archlinux/devtools/-/blob/master/data/LICENSE?ref_type=heads)完全一致，即 Arch Linux 用于软件包的 `0BSD` 许可证。
2.  在软件包源码的根目录下添加一个 `REUSE.toml` 文件，可以使用 `pkgctl license setup` 生成一个后在其上进行修改。
3.  确保执行 `pkgctl license check` 命令时不会有任何报错。

如果有其它需要配置授权的文件，你需要为它们选择一个合理的许可证。方法通常很简单：

- 如果文件（例如类似 `launcher.sh` 的启动脚本或类似 `myunit.service` 的 systemd 服务文件）是完全由你或其他 Arch 人员创建的，则需使用 `0BSD` 许可证。

    > **注意：**如果你编写了该补丁，同时也将其提交到了上游，那可以其针对 Arch 将其以 `0BSD` 许可证分发，并在提交到上游时让他们应用自己的许可证。

- 如果文件时从上游获取的（例如类似 `tool.png` 的图标或类似 `fix.patch` 的补丁），那需要应用上游的许可证。

另请参考介绍了 [pkgctl-license(1)](https://man.archlinux.org/man/pkgctl-license.1) 的[这篇 Arch Linux 开发博客文章](https://devblog.archlinux.page/2025/pkgctl-license/)。

## 可复现构建

Arch 正努力使所有软件包具有可复现性。打包人员可通过 [devtools](https://archlinux.org/packages/?name=devtools)包 的 `makerepropkg` 或 [archlinux-repro](https://archlinux.org/packages/?name=archlinux-repro)包 的 `repro` 来检查包是否有可复现性：

```
$ makerepropkg $pkgname-1-1-any.pkg.tar.zst
```

或

```
$ repro -f $pkgname-1-1-any.pkg.tar.zst
```

如果在构建时需要使用时间戳，可以使用 `SOURCE_DATE_EPOCH` 环境变量，具体格式可参考[上游文档](https://reproducible-builds.org/docs/source-date-epoch/)。
