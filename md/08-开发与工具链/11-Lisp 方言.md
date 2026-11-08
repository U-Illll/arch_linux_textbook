# 第 11 章　Lisp 方言

> 本章解决 Lisp 在 Arch 上真正跑起来的问题：选哪门方言、用哪种实现、运行时怎么装、依赖怎么管、REPL 与开发环境怎么配，而不是讲语言本身。
> 
> 建议先读 Common Lisp：篇幅最长，安装、依赖管理、开发环境、提示与疑难解答俱全，最值得完整读完。Scheme 讲实现选型、包管理与开发，宜通读；Clojure 只有安装与 REPL 两项，走一遍即可。最常见的误解是把 Lisp 当成"一种语言一个实现"——Scheme 单列实现一节，正说明要先挑实现再谈包管理；包管理与依赖管理也分属两节，经验不能跨方言平移。疑难解答只出现在 Common Lisp 一节，另两门方言遇坑需回看各自的开发部分。

<!-- 本章 3 节，共 16 KB -->

## 11.1　Clojure

*难度：入门 ｜ 篇幅：1.6 KB ｜ 所属：Lisp 方言*

将要讲到：安装、REPL、参见

[Clojure](https://clojure.org/) 是 Lisp 的方言，它是一种针对 [Java](../../06_软件与应用/Oracle/Java.md) 虚拟机，[CLR](https://clojure.org/about/clojureclr) 和 [JavaScript](https://clojurescript.org/) 的动态，功能齐全的通用编程语言。

## 安装

安装 [clojure](https://archlinux.org/packages/?name=clojure)包 包，或安装 [clojure-git](https://aur.archlinux.org/packages/clojure-git/)AUR 开发版本。

## REPL

要运行 REPL，请从 [leiningen](https://archlinux.org/packages/?name=leiningen)包 安装 [leiningen](https://leiningen.org/)。然后在终端运行

```
lein repl
```

### m2 仓库路径

要配置本地 m2 仓库路径，请修改 profiles.clj：

```
{:user {:local-repo #=(eval (str (System/getenv "XDG_CACHE_HOME") "/m2"))
        :repositories  {"local" {:url #=(eval (str "file://" (System/getenv "XDG_DATA_HOME") "/m2"))
                                 :releases {:checksum :ignore}}}
        }}
```

## 参见

- [Wikipedia:Clojure](https://en.wikipedia.org/wiki/Clojure)

## 11.2　Scheme

*难度：入门 ｜ 篇幅：4.8 KB ｜ 所属：Lisp 方言*

将要讲到：实现、包管理、开发、参见

根据 [Scheme.org](https://www.scheme.org)：

: Scheme 是 [Lisp 语言家族](https://wiki.archlinuxcn.org/wzh/Category:Lisp_%E6%96%B9%E8%A8%80)中的经典编程语言。它强调[函数式编程](https://en.wikipedia.org/wiki/Functional_programming)和[领域特定语言（DSL）](https://en.wikipedia.org/wiki/Metaprogramming)，同时也能够适应其他编程风格。Scheme 以简洁而极简的设计著称，是最长寿且研究最为深入的[动态编程语言](https://en.wikipedia.org/wiki/Dynamic_programming_language)之一，拥有众多快速且可移植的实现方案。

## 实现

- **Bigloo** — 快速的 Scheme 编译器

: [https://www-sop.inria.fr/mimosa/fp/Bigloo/](https://www-sop.inria.fr/mimosa/fp/Bigloo/) || [bigloo](https://archlinux.org/packages/?name=bigloo)包

- **Chez** — Chez Scheme 是针对 Revised^6 Report on Scheme (R6RS) 语言的编译器和运行时系统，具有许多扩展。

: [https://cisco.github.io/ChezScheme/](https://cisco.github.io/ChezScheme/) || [chez-scheme](https://aur.archlinux.org/packages/chez-scheme/)AUR

- **CHICKEN** — 功能丰富的 R5RS/R7RS 编译器和解释器

: [https://call-cc.org/](https://call-cc.org/) || [chicken](https://archlinux.org/packages/?name=chicken)包

- **Gambit Scheme** — 高效率的 Scheme 实现

: [https://gambitscheme.org/](https://gambitscheme.org/) || [gambit-c](https://aur.archlinux.org/packages/gambit-c/)AUR

- **Gauche** — R7RS Scheme 实现（包含 gosh）

: [https://practical-scheme.net/gauche/](https://practical-scheme.net/gauche/) || [gauche](https://aur.archlinux.org/packages/gauche/)AUR

- **GNU Guile** — 用 C 编写的可移植、可嵌入 Scheme 实现

: [https://www.gnu.org/software/guile/](https://www.gnu.org/software/guile/) || [guile](https://archlinux.org/packages/?name=guile)包、[guile2.0](https://aur.archlinux.org/packages/guile2.0/)AUR、[guile1.8](https://aur.archlinux.org/packages/guile1.8/)AUR

- **Kawa** — 针对 JVM 的 Scheme 实现和框架

: [https://www.gnu.org/software/kawa/](https://www.gnu.org/software/kawa/) || [kawa](https://aur.archlinux.org/packages/kawa/)AUR

- **Larceny** — Larceny 是 Scheme 编程语言的一种简单有效的实现。

: [https://larcenists.org/](https://larcenists.org/) || [larceny-bin](https://aur.archlinux.org/packages/larceny-bin/)AUR

- **MIT/GNU Scheme** — MIT/GNU Scheme

: [https://www.gnu.org/software/mit-scheme/](https://www.gnu.org/software/mit-scheme/) || [mit-scheme](https://aur.archlinux.org/packages/mit-scheme/)AUR

- **Racket** — 使用 DrRacket IDE 的执行各种任务的语言。前身为 PLT Scheme。

: [https://racket-lang.org/](https://racket-lang.org/) || [racket](https://archlinux.org/packages/?name=racket)包、[racket-minimal](https://archlinux.org/packages/?name=racket-minimal)包

- **Scheme48** — 实验性的 Scheme 解释器。

: [https://s48.org/](https://s48.org/) || [scheme48](https://aur.archlinux.org/packages/scheme48/)AUR

## 包管理

[Akku.scm](https://akkuscm.org/) 是 Scheme 语言的一个包管理器。可通过安装 [akku](https://aur.archlinux.org/packages/akku/)AUR 获取。

## 开发

### 学习

- Harold Abelson、Gerald Jay Sussman 与 Julie Sussman 所著的[《计算机程序的构造与解释（第二版）》](https://sarabander.github.io/sicp/)（1996）（另提供 [EPUB3](https://github.com/sarabander/sicp-epub/blob/master/sicp.epub?raw=true) 和 [PDF](https://github.com/sarabander/sicp-pdf/raw/master/sicp.pdf) 格式版本）
- [Programming Languages: Application and Interpretation（PLAI）](https://www.plai.org/)，[lotuc](https://github.com/lotuc) 和 [MrMathematica](https://github.com/mrmathematica) 提供了[简体中文版本](http://lotuc.org/PLAI-cn/)。

### 语言服务器

[Scheme-langserver](https://codeberg.org/ufo5260987423/scheme-langserver) 是 Scheme 语言的语言服务器。可通过 [Akku.scm](#Akku.scm) 获取。

参见 [Emacs-China 上的讨论](https://emacs-china.org/t/scheme-langserver/28125)。

## 参见

- [Scheme.org](https://www.scheme.org/)
- [Scheme 语言标准](https://standards.scheme.org/)
- [Scheme 实现提案（SRFI）](https://srfi.schemers.org/)
- [Scheme 与 Common Lisp 的对比](https://docs.scheme.org/guide/common-lisp/)

## 11.3　Common Lisp

*难度：进阶 ｜ 篇幅：9.6 KB ｜ 所属：Lisp 方言*

将要讲到：安装、依赖管理、开发环境、提示、疑难解答、参见

[Common Lisp](https://common-lisp.net/) 是一种高度动态的多范例语言，强调交互性和性能。完全标准化后，其有许多独立的实现可供选择。

## 安装

[sbcl](https://archlinux.org/packages/?name=sbcl)包 ([http://www.sbcl.org/](http://www.sbcl.org/)) 是最受欢迎的 FOSS 实现，总体跨软件生态[兼容度](https://portability.cl/)最高。其编译器可生成原生的机器码。该项目每月发布更新。

配置文件是 `~/.sbclrc`，使用 Lisp 语言编写。但是，基于您选择的依赖管理策略，您可能用不上它。

### 替代实现

有许多可用的替代实现。除下述外，有两大商用 Lisp 实现：Allegro 和 LispWorks，但它们附有严格的许可条款，也未打包到 Arch Linux 生态中。

#### 活跃

这些功能与现代工具可无缝配合，可用于大型项目。

- **[ABCL](https://en.wikipedia.org/wiki/Common_Lisp#List_of_implementations)** — Armed Bear Common Lisp：在 Java 虚拟机上运行。

: [https://common-lisp.net/project/armedbear/](https://common-lisp.net/project/armedbear/) || [abcl](https://aur.archlinux.org/packages/abcl/)AUR

- **[CCL](https://en.wikipedia.org/wiki/Clozure_CL)** — Clozure Common Lisp：基于 Open Machintosh Common Lisp，以极快的编译速度闻名。

: [https://ccl.clozure.com/](https://ccl.clozure.com/) || [ccl](https://aur.archlinux.org/packages/ccl/)AUR

- **[Clasp](https://en.wikipedia.org/wiki/Common_Lisp#List_of_implementations)** — Clasp：一种较新的 Common Lisp 实现，可与 C++ 库互操作，并借助 LLVM 编译至机器码。

: [https://clasp-developers.github.io/](https://clasp-developers.github.io/) || [clasp-cl](https://aur.archlinux.org/packages/clasp-cl/)AUR

- **[ECL](https://en.wikipedia.org/wiki/Embeddable_Common_Lisp)** — Embeddable Common Lisp：可编译至 C 代码，从而提供良好的 C 集成和嵌入性。

: [https://common-lisp.net/project/ecl/](https://common-lisp.net/project/ecl/) || [ecl](https://archlinux.org/packages/?name=ecl)包

#### 历史

尽管有相应软件包，但库和工具的兼容性令人怀疑，而且有些实现已不再受到积极维护。

- **[CLISP](https://en.wikipedia.org/wiki/CLISP)** — ANSI Common Lisp 解释器、编译器及调试器：提供良好的 C 集成和嵌入性。

: [https://www.gnu.org/software/clisp/](https://www.gnu.org/software/clisp/) || [clisp](https://archlinux.org/packages/?name=clisp)包

- **[CMUCL](https://en.wikipedia.org/wiki/CMU_Common_Lisp)** — CMU Common Lisp：一个仅 POSIX 的实现，最初在卡内基梅隆大学开发。

: [https://gitlab.common-lisp.net/cmucl/cmucl/-/wikis/home](https://gitlab.common-lisp.net/cmucl/cmucl/-/wikis/home) || [cmucl](https://archlinux.org/packages/?name=cmucl)包

- **[GCL](https://en.wikipedia.org/wiki/GNU_Common_Lisp)** — GNU Common Lisp：80 年代 Kyoto Common Lisp 的后代，ECL 的兄弟。

: [https://www.gnu.org/software/gcl/](https://www.gnu.org/software/gcl/) || [gcl](https://aur.archlinux.org/packages/gcl/)AUR

- **MKCL** — Mankai Common Lisp：也是 Kyoto Common Lisp 的后代。

: [https://mkcl.common-lisp.dev/](https://mkcl.common-lisp.dev/) || [mkcl-git](https://aur.archlinux.org/packages/mkcl-git/)AUR

## 依赖管理

### Quicklisp

[Quicklisp](https://www.quicklisp.org/beta/) 是这样一个系统：获取库，并将其加载到 Common Lisp 程序中。默认情况下，本机所有程序共用单一全局软件包缓存。而且，Quicklisp 从一个保守更新的仓库中提取软件包，该仓库也叫做 Quicklisp（可能引起混淆）。

安装 [quicklisp](https://archlinux.org/packages/?name=quicklisp)包 包后，可通过以下方式，将其作为一个特定编译器注册：

```
> sbcl --load /usr/share/quicklisp/quicklisp.lisp
* (quicklisp-quickstart:install)
* (ql:add-to-init-file)
```

在此之后，可以在以后所有的 REPL 会话中使用 `(ql:quickload "foo")` 来加载一个依赖项，必要时会下载该依赖项。

要更新通过 Quicklisp 安装的所有包，在 REPL 中运行以下命令：

```
(ql:update-all-dists)
```

> **注意：**一般来说，Quicklisp 假定您创建的所有 Common Lisp 项目都在 `~/common-lisp/` 目录下。

#### Ultralisp

[Ultralisp](https://ultralisp.org/) 是一个替代 Quicklisp 的仓库，提供所有包的滚动更新，原则上与 Github 最新发布保持一致。

要将其注册，在 REPL 中运行：

```
(ql-dist:install-dist "http://dist.ultralisp.org/" :prompt nil)
```

如果您 `ql:quickload` 的包在 Ultralisp 中有较新版本，该包将从 Ultralisp 而非 Quicklisp 加载。

### Qlot

或者，若您需要项目本地依赖，可以采用 [Qlot](https://github.com/fukamachi/qlot) ([qlot](https://aur.archlinux.org/packages/qlot/)AUR)。

安装后，项目仓库可以通过以下命令初始化：

```
qlot init
```

#### 自定义依赖

使用 Qlot、所有自定义依赖的位置都记录在 `qlfile` 中。例如，要声明使用 Ultralisp，只需添加：

```
dist http://dist.ultralisp.org
```

或指定本地依赖：

```
local foobar /home/you/code/common-lisp/foobar
```

参见 Qlot 的 README 以了解更多选项。

要安装已经声明的依赖，运行：

```
qlot install
```

#### 启用 REPL

要在当前 Qlot 环境中加载 REPL，请运行：

```
qlot exec sbcl
```

使用 Sly/Slime 的 Emacs 用户，考虑这样配置编辑器内 REPL 的启动方式：

```
(setq sly-default-lisp 'qlot-sbcl
      sly-lisp-implementations '((qlot-sbcl ("qlot" "exec" "sbcl") :coding-system utf-8-unix)))
```

Slime 类似，进行必要的调整即可。

## 开发环境

### Emacs

Common Lisp 开发常常通过 Emacs 完成：借助 slime 或较新的 [sly](https://joaotavora.github.io/sly)。两者都被社区广泛采用且使用方法类似。

### Lem

[Lem](https://lem-project.github.io/) ([lem-editor-git](https://aur.archlinux.org/packages/lem-editor-git/)AUR) 是一个较新的 Emacs 风格编辑器，但是完全由 Common Lisp 编写和配置。其拥有终端和图形化前端，并支持许多语言。

## 提示

### 管理 Init 文件

库的作者经常使用多个编译器实现来测试他们的代码。但是，每个实现都有各自不同的 init 文件，例如 `.sbclrc` 或 `.eclrc`。但这些文件的内容往往是相同的：与其为每个编译器手写这些文件，不如创建一个主文件，并将其他文件链接到该文件。例如，如果您考虑 `.sbclrc` 成为您的 "主配置"，然后：

```
ln -s /home/you/.sbclrc .eclrc
ln -s /home/you/.sbclrc .abclrc
```

等等。这些主要 init 文件是：

```
~/.sbclrc          # for SBCL
~/.abclrc          # for ABCL
~/.ccl-init.lisp   # for CCL
~/.clasprc         # for CLASP
~/.eclrc           # for ECL
~/.clisprc.lisp    # for CLISP
~/.cmucl-init.lisp # for CMUCL
~/.mkclrc          # for MKCL
~/.clinit.cl       # for Allegro
```

## 疑难解答

### Quicklisp 不能加载本地项目

`(ql:quickload "...")` 用于加载外部依赖项和本地项目。但若本地项目不位于 `~/common-lisp/`，将无法加载。项目甚至可能被从在线平台上删除，如果已经发布。

实际上，Quicklisp 只是获取和整理软件包。它内部使用 ASDF 构建系统，以实际加载这些包。也可以通过 `(asdf：load-system "foo")`，默认情况下，ASDF 只在 `~/common-lisp/` 中寻找本地项目。

虽然可以配置，但更简单的方法是使用符号链接：

```
ln -s /home/you/code/common-lisp/ common-lisp
```

> **注意：**如果使用 Qlot，则不需要上述符号链接。只要您执行 `qlot install`，然后 `(asdf：load-package "foo")` 即可。

### 什么是项目、系统、软件包？

本文其余部分使用的“包”一词是其他编程语言通常使用的“库 ”的同义词。不过，虽然 Common Lisp 在 90 年代中期就已标准化，但其最早的形式却是在 80 年代出现的，因此涉及项目管理的术语也有所不同。实质上：

- 项目：一组系统。有时在其他语言中被称为“命名空间”。
- 系统：一组包。既可代表库，也可代表 “可执行文件”。
- 软件包：一组函数和类型定义。在其他语言中通常称为“模块”，但可跨多个文件。

如您所见，在其他地方通常被称为库的东西，在 Common Lisp 中被称为“系统”。因此有 `asdf:load-system` 的命名。[参见此处](https://codeberg.org/fosskers/cl-transducers/src/commit/5df2f4b7c66ea4bd8f607e6900f9bdedd7e58061/transducers.asd)以获得相互依赖、多系统项目的一个例子。

## 参见

- [The Common Lisp 使用手册](https://lispcookbook.github.io/cl-cookbook/)：许多问题的解决方法。
- [社区规范](https://cl-community-spec.github.io/pages/index.html)：在线 stdlib 文档。
- [Awesome CL](https://github.com/CodyReichert/awesome-cl)：生态概览。
- [Quickdocs](https://quickdocs.org/)：Quicklisp 文档查询。
- [Portability](https://portability.cl/)：编译器实现的兼容性列表。
- [Wikipedia:Common Lisp](https://en.wikipedia.org/wiki/Common_Lisp)
- [Cliki：Common Lisp 维基](https://cliki.net)
