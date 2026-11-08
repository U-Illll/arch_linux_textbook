# 第 14 章　Oracle

> MySQL、Java、VirtualBox 三套同属 Oracle 的软件，本章解决它们在 Arch 上的安装与使用：数据库部署与程序访问、多个 JVM 切换、虚拟机搭建与虚拟磁盘管理。
> 
> 建议先读 Java，篇幅最长；多个 JVM 并存只能靠 archlinux-java 切换，而软件包不支持它，切换便不生效，这是最易忽略的前提。再读 MySQL 处理程序访问，VirtualBox 可按需跳读，只做虚拟机实验就只看安装与故障排除。另一个误解是把在 Arch 里装 VirtualBox 与在 VirtualBox 里装 Arch 混为一谈。

<!-- 本章 3 节，共 135 KB -->

## 14.1　MySQL

*难度：入门 ｜ 篇幅：3.4 KB ｜ 所属：Oracle*

将要讲到：安装、图形工具、控制台工具、程序访问

[MySQL](https://en.wikipedia.org/wiki/MySQL) 是 一个广泛使用的多线程，多用户 SQL 数据库，由 Oracle 公司开发。

## 安装

Arch Linux 默认推荐使用 [MariaDB](../数据库管理系统/MariaDB.md)，这是 MySQL 的一个社区维护分支，旨在实现“无缝替换”。Oracle 的 MySQL [已被移至](https://archlinux.org/news/mariadb-replaces-mysql-in-repositories/) [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md)（Arch User Repository）包名为[mysql](https://aur.archlinux.org/packages/mysql/)AUR。另一个旨在完全兼容的分支是 [Percona Server](https://en.wikipedia.org/wiki/Percona_Server_for_MySQL)，可从 [percona-server](https://archlinux.org/packages/?name=percona-server)包 获得。

Percona 也有 Oracle 的 [InnoDB](https://en.wikipedia.org/wiki/InnoDB) 存储引擎的分支，称为 [XtraDB](https://en.wikipedia.org/wiki/XtraDB)。木卡 [MariaDB](../数据库管理系统/MariaDB.md) 和 Percona Server 都使用此分支。

## 图形工具

- **[phpMyAdmin](../../03_网络与服务/网络管理界面/PhpMyAdmin.md)** — MySQL Web 界面，用 PHP 编写。

: [https://www.phpmyadmin.net](https://www.phpmyadmin.net) || [phpmyadmin](https://archlinux.org/packages/?name=phpmyadmin)包

- **[MySQL Workbench](https://en.wikipedia.org/wiki/MySQL_Workbench)** — 适用于数据库架构师，开发人员和 DBA 的统一可视工具。 由 Oracle 开发，不能保证与[MariaDB](../数据库管理系统/MariaDB.md)一起使用。

: [https://www.mysql.com/products/workbench/](https://www.mysql.com/products/workbench/) || [mysql-workbench](https://archlinux.org/packages/?name=mysql-workbench)包

有关支持多个 DBMS 的工具，请参阅[应用程序列表/文档#数据库工具](../应用程序/应用程序列表_文档.md#数据库工具)。

## 控制台工具

- **MyCLI** — MySQL 的终端客户端，具有自动完成功能和语法突出显示功能。

: [https://www.mycli.net](https://www.mycli.net) || [mycli](https://aur.archlinux.org/packages/mycli/)AUR

## 程序访问

- [JDBC and MySQL](https://wiki.archlinux.org/title/JDBC_and_MySQL)
- [PHP#MySQL/MariaDB](../../07_开发与工具/编程语言/PHP.md#MySQL/MariaDB)
- [Python](../../07_开发与工具/编程语言/Python.md)
    - [python-mysqlclient](https://archlinux.org/packages/?name=python-mysqlclient)包
    - [python-mysql-connector](https://archlinux.org/packages/?name=python-mysql-connector)包
- [C++](../../07_开发与工具/编程语言/C.md)
    - [mysql++](https://archlinux.org/packages/?name=mysql%2B%2B)包
- [Ruby](https://wiki.archlinux.org/title/Ruby)
    - [ruby-mysql2](https://aur.archlinux.org/packages/ruby-mysql2/)AUR
- [Perl](https://wiki.archlinux.org/title/Perl)
    - [perl-dbd-mysql](https://archlinux.org/packages/?name=perl-dbd-mysql)包

## 14.2　Java

*难度：高级 ｜ 篇幅：32.8 KB ｜ 所属：Oracle*

将要讲到：安装、在 JVM 之间切换、软件包支持 archlinux-java 的先决条件、提示和技巧、故障排除

摘自维基百科中的 [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) 词条:

: Java是一种编程语言，最初由Sun Microsystems开发，并于1995年作为Sun Microsystems的Java平台的核心组件发布。它有很多语法来自C和C++，但对象模型更简洁，底层组件更少。Java的应用一般会编译成能在任何Java虚拟机([JVM](https://en.wikipedia.org/wiki/Java_virtual_machine))而不是特定架构上运行的字节码。

[Arch Linux](../../00_关于与社区/关于%20Arch/Arch%20Linux.md) 官方支持开源的 [OpenJDK](https://openjdk.java.net/) [版本](https://en.wikipedia.org/wiki/Java_version_history#Release_table) 8、11、17 、 21和25——*长期支持* (LTS) 版本，以及 26——最新发布的版本。所有这些JVM可以并存，并能够通过帮助脚本 *archlinux-java*（随 [java-runtime-common](https://archlinux.org/packages/?name=java-runtime-common)包 包安装）切换。在 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 中也有一些不受官方支持的Java环境。

## 安装

> **注意：**
>
> - Arch Linux 官方仅支持 [OpenJDK](#OpenJDK) 实现。
> - 安装完成后，Java 环境需要被 [shell](../../07_开发与工具/命令行解释器/命令行解释器.md) (`$PATH` 变量) 识别。可以通过在命令行中使用 `source` 命令读取 `/etc/profile`，注销并重新登录 [桌面环境](../../05_桌面与图形/桌面环境/桌面环境.md)，或者重启系统来做到这点。

两个*常见的*包分别作为依赖被拉取，分别是 [java-runtime-common](https://archlinux.org/packages/?name=java-runtime-common)包（包含 *Java 运行环境*的公共文件）和 [java-environment-common](https://archlinux.org/packages/?name=java-environment-common)包（包含 *Java 开发工具包*的公共文件）。

提供的 `/etc/profile.d/jre.sh` 和 `/etc/profile.d/jre.csh` 文件指向由 *archlinux-java* 帮助脚本设置的链接 `/usr/lib/jvm/default/bin`。

> **提示：**`/usr/lib/jvm/default` 和 `/usr/lib/jvm/default-runtime` 符号链接应**始终**使用 `archlinux-java` 进行编辑，该工具用于显示并指向一个可用的默认 Java 运行环境。

大多数 Java 安装的可执行文件通过直接链接在 `/usr/bin/` 中提供，而其他可执行文件则在 `$PATH` 指向的位置处。

Arch 不使用 `JAVA_HOME`。如果一个程序显式要求 `JAVA_HOME`，请设为 `JAVA_HOME=/usr/lib/jvm/default`。

### OpenJDK

[OpenJDK](https://en.wikipedia.org/wiki/OpenJDK)是*Java平台标准版*（Java SE）的开源实现，也是官方的参考实现。有几个OpenJDK构建的分发，如[Adoptium](https://adoptium.net)（以前称为AdoptOpenJDK）和[Amazon Corretto](https://aws.amazon.com/corretto/)。Arch Linux的OpenJDK包由[上游的OpenJDK源代码](https://hg.openjdk.java.net/)构建。

**Headless JRE**
: Java的最小运行环境 - 执行非GUI的Java程序所需。
**Full JRE**
: 完全的Java运行环境 - 执行Java GUI程序所需。
**JDK**
: [Java Development Kit](https://en.wikipedia.org/wiki/Java_Development_Kit) - Java开发所需。

JDK、full JRE 和 headless JRE 互相冲突，因为较小的包是其子集：

- JDK 冲突并提供 full JRE，
- full JRE 冲突并提供 headless JRE。

| 版本 | Headless JRE | Full JRE | JDK | 文献 | 源码 |
| --- | --- | --- | --- | --- | --- |
| [OpenJDK 26](https://openjdk.java.net/projects/jdk/26/) | [jre-openjdk-headless](https://archlinux.org/packages/?name=jre-openjdk-headless)包 | [jre-openjdk](https://archlinux.org/packages/?name=jre-openjdk)包 | [jdk-openjdk](https://archlinux.org/packages/?name=jdk-openjdk)包 | [openjdk-doc](https://archlinux.org/packages/?name=openjdk-doc)包 | [openjdk-src](https://archlinux.org/packages/?name=openjdk-src)包 |
| [OpenJDK 25](https://openjdk.java.net/projects/jdk/25/) | [jre25-openjdk-headless](https://archlinux.org/packages/?name=jre25-openjdk-headless)包 | [jre25-openjdk](https://archlinux.org/packages/?name=jre25-openjdk)包 | [jdk25-openjdk](https://archlinux.org/packages/?name=jdk25-openjdk)包 | [openjdk25-doc](https://archlinux.org/packages/?name=openjdk25-doc)包 | [openjdk25-src](https://archlinux.org/packages/?name=openjdk25-src)包 |
| [OpenJDK 21](https://openjdk.java.net/projects/jdk/21/) | [jre21-openjdk-headless](https://archlinux.org/packages/?name=jre21-openjdk-headless)包 | [jre21-openjdk](https://archlinux.org/packages/?name=jre21-openjdk)包 | [jdk21-openjdk](https://archlinux.org/packages/?name=jdk21-openjdk)包 | [openjdk21-doc](https://archlinux.org/packages/?name=openjdk21-doc)包 | [openjdk21-src](https://archlinux.org/packages/?name=openjdk21-src)包 |
| [OpenJDK 17](https://openjdk.java.net/projects/jdk/17/) | [jre17-openjdk-headless](https://archlinux.org/packages/?name=jre17-openjdk-headless)包 | [jre17-openjdk](https://archlinux.org/packages/?name=jre17-openjdk)包 | [jdk17-openjdk](https://archlinux.org/packages/?name=jdk17-openjdk)包 | [openjdk17-doc](https://archlinux.org/packages/?name=openjdk17-doc)包 | [openjdk17-src](https://archlinux.org/packages/?name=openjdk17-src)包 |
| [OpenJDK 11](https://openjdk.java.net/projects/jdk/11/) | [jre11-openjdk-headless](https://archlinux.org/packages/?name=jre11-openjdk-headless)包 | [jre11-openjdk](https://archlinux.org/packages/?name=jre11-openjdk)包 | [jdk11-openjdk](https://archlinux.org/packages/?name=jdk11-openjdk)包 | [openjdk11-doc](https://archlinux.org/packages/?name=openjdk11-doc)包 | [openjdk11-src](https://archlinux.org/packages/?name=openjdk11-src)包 |
| [OpenJDK 8](https://openjdk.java.net/projects/jdk8/) | [jre8-openjdk-headless](https://archlinux.org/packages/?name=jre8-openjdk-headless)包 | [jre8-openjdk](https://archlinux.org/packages/?name=jre8-openjdk)包 | [jdk8-openjdk](https://archlinux.org/packages/?name=jdk8-openjdk)包 | [openjdk8-doc](https://archlinux.org/packages/?name=openjdk8-doc)包 | [openjdk8-src](https://archlinux.org/packages/?name=openjdk8-src)包 |

**IcedTea-Web** — Java Web Start 和已弃用的 Java 浏览器插件。

: [https://icedtea.classpath.org/download/icedtea-web-docs/1.8/html/en/icedtea-web.html](https://icedtea.classpath.org/download/icedtea-web-docs/1.8/html/en/icedtea-web.html) || [icedtea-web](https://archlinux.org/packages/?name=icedtea-web)包

**OpenJDK EA** — Oracle 最新的 OpenJDK 开发版Early-Access构建版本。

: [https://jdk.java.net](https://jdk.java.net) || [java-openjdk-ea-bin](https://aur.archlinux.org/packages/java-openjdk-ea-bin/)AUR

**OpenJDK GA** — Oracle 最新的 OpenJDK 通用可用版本发布构建。

: [https://jdk.java.net](https://jdk.java.net) || [java-openjdk-bin](https://aur.archlinux.org/packages/java-openjdk-bin/)AUR

**OpenJDK Wakefield** — JDK 在 Linux 上对 Wayland 桌面环境（WIP）支持。

: [https://openjdk.org/projects/wakefield/](https://openjdk.org/projects/wakefield/) || [jdk-openjdk-wakefield](https://aur.archlinux.org/packages/jdk-openjdk-wakefield/)AUR

### OpenJFX

[OpenJFX](https://wiki.openjdk.java.net/display/OpenJFX/Main) 是 [JavaFX](https://en.wikipedia.org/wiki/JavaFX) 的开源实现。如果您使用的是 Oracle JDK，则[无需安装](https://wiki.openjdk.java.net/display/OpenJFX/Repositories+and+Releases)此软件包。此软件包只适用于 Java 的开源实现（OpenJDK 项目）及其衍生产品的用户。

| 版本 | 运行环境与开发工具 | 文档 | 源码 |
| --- | --- | --- | --- |
| [OpenJFX 28](https://wiki.openjdk.java.net/display/OpenJFX/Main) | [java-openjfx](https://aur.archlinux.org/packages/java-openjfx/)AUR | [java-openjfx-doc](https://aur.archlinux.org/packages/java-openjfx-doc/)AUR | [java-openjfx-src](https://aur.archlinux.org/packages/java-openjfx-src/)AUR |
| [OpenJFX 21](https://wiki.openjdk.java.net/display/OpenJFX/Main) | [java21-openjfx](https://aur.archlinux.org/packages/java21-openjfx/)AUR | [java21-openjfx-doc](https://aur.archlinux.org/packages/java21-openjfx-doc/)AUR | [java21-openjfx-src](https://aur.archlinux.org/packages/java21-openjfx-src/)AUR |
| [OpenJFX 17](https://wiki.openjdk.java.net/display/OpenJFX/Main) | [java17-openjfx](https://aur.archlinux.org/packages/java17-openjfx/)AUR | [java17-openjfx-doc](https://aur.archlinux.org/packages/java17-openjfx-doc/)AUR | [java17-openjfx-src](https://aur.archlinux.org/packages/java17-openjfx-src/)AUR |
| [OpenJFX 11](https://wiki.openjdk.java.net/display/OpenJFX/Main) | [java11-openjfx](https://aur.archlinux.org/packages/java11-openjfx/)AUR | [java11-openjfx-doc](https://aur.archlinux.org/packages/java11-openjfx-doc/)AUR | [java11-openjfx-src](https://aur.archlinux.org/packages/java11-openjfx-src/)AUR |
| [OpenJFX 8](https://wiki.openjdk.java.net/display/OpenJFX/Main) | [java8-openjfx](https://aur.archlinux.org/packages/java8-openjfx/)AUR | [java8-openjfx-doc](https://aur.archlinux.org/packages/java8-openjfx-doc/)AUR | [java8-openjfx-src](https://aur.archlinux.org/packages/java8-openjfx-src/)AUR |

### 其他实现

- **AWS Corretto** — Amazon Web Services 的 OpenJDK 发行版。

: [https://aws.amazon.com/corretto/](https://aws.amazon.com/corretto/) || [amazon-corretto-8](https://aur.archlinux.org/packages/amazon-corretto-8/)AUR [amazon-corretto-11](https://aur.archlinux.org/packages/amazon-corretto-11/)AUR [amazon-corretto-17](https://aur.archlinux.org/packages/amazon-corretto-17/)AUR [amazon-corretto-21-bin](https://aur.archlinux.org/packages/amazon-corretto-21-bin/)AUR

- **Azul JDK** — Azul 的 JDK 实现。请注意，Azul Zulu Builds of OpenJDK 是开源的，而 Azul Zulu Prime Builds of OpenJDK 是商业产品，免费用于开发和评估。

: [https://www.azul.com/downloads/](https://www.azul.com/downloads/) ||
: Zulu: [zulu-8-bin](https://aur.archlinux.org/packages/zulu-8-bin/)AUR [zulu-11-bin](https://aur.archlinux.org/packages/zulu-11-bin/)AUR [zulu-17-bin](https://aur.archlinux.org/packages/zulu-17-bin/)AUR [zulu-21-bin](https://aur.archlinux.org/packages/zulu-21-bin/)AUR
: Zulu Prime: [zing-8-bin](https://aur.archlinux.org/packages/zing-8-bin/)AUR [jdk17-zulu-prime-bin](https://aur.archlinux.org/packages/jdk17-zulu-prime-bin/)AUR [zing-21-bin](https://aur.archlinux.org/packages/zing-21-bin/)AUR

- **Eclipse Adoptium/Temurin** — Eclipse 的 JRE/JDK 实现，基于 Hotspot JVM（前身为 AdoptOpenJDK）。请注意，JRE 被称为 Eclipse Temurin。

: [https://adoptium.net/](https://adoptium.net/) || [jdk-temurin](https://aur.archlinux.org/packages/jdk-temurin/)AUR [jdk17-temurin](https://aur.archlinux.org/packages/jdk17-temurin/)AUR [jdk11-temurin](https://aur.archlinux.org/packages/jdk11-temurin/)AUR

- **IBM Certified** — IBM Semeru Runtime Certified Edition。

: [https://www.ibm.com/semeru-runtimes/downloads](https://www.ibm.com/semeru-runtimes/downloads) || [jdk11-j9-bin](https://aur.archlinux.org/packages/jdk11-j9-bin/)AUR

- **IBM J9** — IBM 的 JRE 实现，使用 OpenJ9 贡献。

: [https://www.ibm.com/support/pages/java-sdk-downloads](https://www.ibm.com/support/pages/java-sdk-downloads) || [jdk8-j9-bin](https://aur.archlinux.org/packages/jdk8-j9-bin/)AUR [jdk7-j9-bin](https://aur.archlinux.org/packages/jdk7-j9-bin/)AUR [jdk7r1-j9-bin](https://aur.archlinux.org/packages/jdk7r1-j9-bin/)AUR

- **Liberica JDK** — BellSoft 的 Liberica JDK 实现。

: [https://bell-sw.com/libericajdk/](https://bell-sw.com/libericajdk/) || [liberica-jre-8-full-bin](https://aur.archlinux.org/packages/liberica-jre-8-full-bin/)AUR [liberica-jdk-8-full-bin](https://aur.archlinux.org/packages/liberica-jdk-8-full-bin/)AUR [liberica-jre-11-bin](https://aur.archlinux.org/packages/liberica-jre-11-bin/)AUR [liberica-jre-11-full-bin](https://aur.archlinux.org/packages/liberica-jre-11-full-bin/)AUR [liberica-jdk-11-bin](https://aur.archlinux.org/packages/liberica-jdk-11-bin/)AUR [liberica-jdk-11-full-bin](https://aur.archlinux.org/packages/liberica-jdk-11-full-bin/)AUR [liberica-jdk-17-full-bin](https://aur.archlinux.org/packages/liberica-jdk-17-full-bin/)AUR [liberica-jdk-21-full-bin](https://aur.archlinux.org/packages/liberica-jdk-21-full-bin/)AUR

- **Microsoft OpenJDK** — Microsoft 的 OpenJDK 发行版。

: [https://www.microsoft.com/openjdk/](https://www.microsoft.com/openjdk/) || [microsoft-openjdk-11-bin](https://aur.archlinux.org/packages/microsoft-openjdk-11-bin/)AUR [microsoft-openjdk-17-bin](https://aur.archlinux.org/packages/microsoft-openjdk-17-bin/)AUR [microsoft-openjdk-21-bin](https://aur.archlinux.org/packages/microsoft-openjdk-21-bin/)AUR

- **OpenJ9** — Eclipse 的 JRE/JDK 实现，基于 J9 JVM，由 IBM 贡献。

: [https://www.eclipse.org/openj9/](https://www.eclipse.org/openj9/) || [jdk-openj9-bin](https://aur.archlinux.org/packages/jdk-openj9-bin/)AUR [jdk17-openj9-bin](https://aur.archlinux.org/packages/jdk17-openj9-bin/)AUR [jdk11-openj9-bin](https://aur.archlinux.org/packages/jdk11-openj9-bin/)AUR [jdk8-openj9-bin](https://aur.archlinux.org/packages/jdk8-openj9-bin/)AUR

- **Oracle JDK** — Oracle 的商业许可的 OpenJDK 构建。请注意，某些版本只能通过手动下载获得，这需要签署 OTN 协议并创建 Oracle 账户。

: [https://www.oracle.com/java/technologies/downloads/](https://www.oracle.com/java/technologies/downloads/) ||
: JRE: [jre](https://aur.archlinux.org/packages/jre/)AUR [jre-lts](https://aur.archlinux.org/packages/jre-lts/)AUR [jre11](https://aur.archlinux.org/packages/jre11/)AUR [jre8](https://aur.archlinux.org/packages/jre8/)AUR [jre7](https://aur.archlinux.org/packages/jre7/)AUR
: JDK: [jdk](https://aur.archlinux.org/packages/jdk/)AUR [jdk-lts](https://aur.archlinux.org/packages/jdk-lts/)AUR [jdk11](https://aur.archlinux.org/packages/jdk11/)AUR [jdk8](https://aur.archlinux.org/packages/jdk8/)AUR [jdk7](https://aur.archlinux.org/packages/jdk7/)AUR

> **注意：**Oracle JDK 的 32 位版本可以通过前缀 `bin32-` 找到，例如 [bin32-jre](https://aur.archlinux.org/packages/bin32-jre/)AUR。它们使用 [java32-runtime-common](https://aur.archlinux.org/packages/java32-runtime-common/)AUR，其功能与 [java-runtime-common](https://archlinux.org/packages/?name=java-runtime-common)包 相同，但添加了 `32` 的后缀，例如 `java32`。[java32-environment-common](https://aur.archlinux.org/packages/java32-environment-common/)AUR 也是同理，只有 32 位的 JDK 包使用它。

### 开发工具

对于集成开发环境，见[List of applications/Utilities#Integrated development environments](../应用程序/应用程序列表_工具.md#Integrated_development_environments) 和 [Java IDEs](../应用程序/应用程序列表_工具.md#Java_IDEs) 子分区。

为了阻止逆向工程，可以使用[proguard](https://aur.archlinux.org/packages/proguard/)AUR等混淆器。

#### 反编译器

- **CFR** — Java decompiler, supporting modern features of Java 9, 10 and beyond.

: [https://www.benf.org/other/cfr/](https://www.benf.org/other/cfr/) || [cfr](https://archlinux.org/packages/?name=cfr)包

- **Fernflower** — Analytical decompiler for Java, developed as part of IntelliJ IDEA.

: [https://github.com/JetBrains/intellij-community/tree/master/plugins/java-decompiler/engine](https://github.com/JetBrains/intellij-community/tree/master/plugins/java-decompiler/engine) || [fernflower-git](https://aur.archlinux.org/packages/fernflower-git/)AUR

- **Vineflower** — Java decompiler forked from Fernflower, aiming to improve code quality. Also available as an IntelliJ IDEA plugin.

: [https://github.com/Vineflower/vineflower](https://github.com/Vineflower/vineflower) || [vineflower](https://aur.archlinux.org/packages/vineflower/)AUR

- **Krakatau** — Java decompiler, assembler, and disassembler.

: [https://github.com/Storyyeller/Krakatau](https://github.com/Storyyeller/Krakatau) || [krakatau-git](https://aur.archlinux.org/packages/krakatau-git/)AUR

- **Procyon decompiler** — Experimental Java decompiler, inspired by ILSpy and Mono.Cecil.

: [https://bitbucket.org/mstrobel/procyon/wiki/Java%20Decompiler](https://bitbucket.org/mstrobel/procyon/wiki/Java%20Decompiler) || [procyon-decompiler](https://archlinux.org/packages/?name=procyon-decompiler)包

- **Java Decompiler (JD-Core)** — Popular Java decompiler providing a GUI (see JD-GUI) and supporting Java 1-10.

: [https://java-decompiler.github.io/](https://java-decompiler.github.io/) || [jd-core-java](https://aur.archlinux.org/packages/jd-core-java/)AUR

- **Jadx** — Android DEX to Java decompiler with an optional GUI (see Jadx-GUI).

: [https://github.com/skylot/jadx](https://github.com/skylot/jadx) || [jadx](https://archlinux.org/packages/?name=jadx)包

- **[JAD](https://en.wikipedia.org/wiki/JAD_(software))** — Unmaintained Java decompiler (last release 2006).

: [https://varaneckas.com/jad](https://varaneckas.com/jad) || [jad](https://archlinux.org/packages/?name=jad)包

#### GUI前端

- **Bytecode Viewer** — Java reverse engineering suite, including a decompiler, editor and debugger; Frontend for CFR/Fernflower/Procyon.

: [https://bytecodeviewer.com](https://bytecodeviewer.com) || [bytecode-viewer](https://aur.archlinux.org/packages/bytecode-viewer/)AUR

- **Recaf** — An easy to use modern Java bytecode editor that abstracts away the complexities of Java programs; Frontend for CFR/Fernflower/Procyon.

: [https://www.coley.software/Recaf/](https://www.coley.software/Recaf/) || [recaf-bin](https://aur.archlinux.org/packages/recaf-bin/)AUR

- **Java Decompiler (JD-GUI)** — Popular Java decompiler providing a GUI and supporting Java 1-10; Frontend for JD-Core.

: [https://java-decompiler.github.io/](https://java-decompiler.github.io/) || [jd-gui](https://aur.archlinux.org/packages/jd-gui/)AUR

- **Jadx-GUI** — Android APK DEX to Java decompiler with an optional GUI; Frontend for Jadx.

: [https://github.com/skylot/jadx](https://github.com/skylot/jadx) || [jadx](https://archlinux.org/packages/?name=jadx)包

- **Luyten** — An Open Source Java Decompiler Gui; Frontend for Procyon.

: [https://github.com/deathmarine/Luyten](https://github.com/deathmarine/Luyten) || [luyten](https://aur.archlinux.org/packages/luyten/)AUR\== 在JVM间切换 ==

## 在 JVM 之间切换

帮助脚本 *archlinux-java* (软件包: [java-runtime-common](https://archlinux.org/packages/?name=java-runtime-common)包) 提供了如下功能:

```
archlinux-java <COMMAND>

COMMAND:
status          列出已安装的 Java 环境及当前启用的环境
get             返回设置为默认的 Java 环境的短名称
set <JAVA_ENV>  强制将 <JAVA_ENV> 设置为默认
unset           取消当前默认的 Java 环境
fix             修复无效/损坏的默认 Java 环境配置
```

### 列出兼容的安装了的Java环境

```
$ archlinux-java status
```

例如:

```
$ archlinux-java status
Available Java environments:
  java-11-openjdk (default)
  java-8-openjdk/jre
```

这里的`(default)`表示目前默认使用`java-11-openjdk`，Java和其他二进制文件的调用都将依赖于此Java安装。前面的输出中也显示，这里只安装了OpenJDK 8的JRE部分。

### 改变默认Java环境

```
# archlinux-java set <JAVA_ENV_NAME>
```

例如:

```
# archlinux-java set java-8-openjdk/jre
```

> **提示：**若要看到可用的`<JAVA_ENV_NAME>`名称，请使用`archlinux-java status`。

注意，`archlinux-java` 不会允许您设置无效的Java环境。在前面的例子中，只安装了[jre8-openjdk](https://archlinux.org/packages/?name=jre8-openjdk)包，而**没有**安装[jdk8-openjdk](https://archlinux.org/packages/?name=jdk8-openjdk)包，所以设置`java-8-openjdk`将会失败：

```
# archlinux-java set java-8-openjdk
```

```
'/usr/lib/jvm/java-8-openjdk' is not a valid Java environment path
```

### 取消设置的默认Java环境

无需取消Java环境的设置，因为提供环境的软件包通常会考虑到这一点。但若想这样做，只需使用`unset`命令：

```
# archlinux-java unset
```

### 解决默认Java环境的问题

如果设置了一个无效的Java环境链接，尝试调用`archlinux-java fix`命令以修复它。还要注意，如果没有设置默认的Java环境，它将寻找有效的环境并尝试设置。它会优先考虑官方支持的软件包"OpenJDK 8"。

```
# archlinux-java fix
```

### 运行非默认Java版本的程序

如果想用另一个版本的Java启动一个程序（例如系统同时安装了Java 18——默认版本——和11，而您要使用Java 11），可以用一个shell脚本包装应用程序，在本地改变Java的默认路径：

```
#!/bin/sh

export PATH="/usr/lib/jvm/java-11-openjdk/bin/:$PATH"
exec /path/to/application "$@"
```

对于[systemd](../../01_安装与包管理/初始化/Systemd.md)服务，您可以在[drop-in file](../../01_安装与包管理/初始化/Systemd.md)中附加`JAVA_HOME`到环境变量：

```
/etc/systemd/system/unit.d/override.conf
```

```
[Service]
Environment=JAVA_HOME=/usr/lib/jvm/java-11-openjdk
```

## 软件包支持 archlinux-java 的先决条件

> **注意：**这条信息同样适用于 `archlinux32-java` 的32位Java包，如果它们的包或者可执行名字里有 `32` ，都可适用。

这个分区的信息针对愿意提供包作为备份JVM给 [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md) 的贡献者，并且能够用 `archlinux-java` 集成Arch Linux JVM方案（即：与 `archlinux-java` 兼容）。如果要这样的话，这些包应该：

- 把所有文件放在 `/usr/lib/jvm/java-${JAVA_MAJOR_VERSION}-${VENDOR_NAME`}。
- 确认所有的 [java-runtime-common](https://archlinux.org/packages/?name=java-runtime-common)包 和 [java-environment-common](https://archlinux.org/packages/?name=java-environment-common)包 提供的可执行链接在相关包里都可用。
- 把所有链接从 `/usr/bin` 移动到可执行文件里，除非这些链接不属于 [java-runtime-common](https://archlinux.org/packages/?name=java-runtime-common)包 和 [java-environment-common](https://archlinux.org/packages/?name=java-environment-common)包。
- 用 `-${VENDOR_NAME}${JAVA_MAJOR_VERSION}` 的格式给手册页添加后缀以防止冲突（查阅 [jre8-openjdk](https://archlinux.org/packages/?name=jre8-openjdk)包 文件列表，它的手册页用 `-openjdk8` 做后缀）。
- 不要定义任何[冲突](../../01_安装与包管理/包开发/PKGBUILD.md#conflicts)和[替代](../../01_安装与包管理/包开发/PKGBUILD.md#replaces)，用其他的JDK，`java-runtime`，`java-runtime-headless` 和 `java-environment`。
- 在*安装函数*里使用 `archlinux-java` 脚本以将Java环境设置为默认**如果没有其他可用的Java环境准备设置的话**（即：这些包**不应该强制**被装为默认）。查阅[官方支持的Java环境包源码](https://gitlab.archlinux.org/archlinux/packaging/packages/java-openjdk)做例子。

同时也要注意：

- 包需要的**任何**Java环境都应声明依赖，和通常一样在 `java-runtime`、`java-runtime-headless` 或 `java-environment` 里声明。
- 包如果需要**特定的Java提供商**，应该在相关包里声明依赖。
- OpenJDK 包现在应该声明 `provides="java-runtime-openjdk=${pkgver}"` 等。这能让第三方的包在没有特定版本要求的OpenJDK里声明依赖。

## 提示和技巧

> **注意：**本节中的建议适用于所有使用明确安装（外部）的Java运行环境的应用程序。有些应用程序捆绑了自己（私有）的运行环境，或使用自己的GUI、字体渲染等机制，以下内容不一定完全适用于这种情况。某些应用程序（如远程JetBrains IDE或代码反编译器）在更改Swing平台、Gtk版本或渲染管道的设置后，将无法启动或运行不正确。

大多数Java应用的行为都可以通过向Java运行时提供预定义变量来控制。从[这个论坛帖子](https://bbs.archlinux.org/viewtopic.php?id=72892)来看，一种方法是在`~/.bash_profile`中添加以下一行 (或在`/etc/profile.d/jre.sh`添加来影响那些不通过`~/.bash_profile`运行的程序）：

```
export _JAVA_OPTIONS="-D<option 1> -D<option 2>..."
```

例如，使用系统抗锯齿字体并使swing使用GTK的外观与体验（look and feel）：

```
export _JAVA_OPTIONS='-Dawt.useSystemAAFontSettings=on -Dswing.aatext=true -Dswing.defaultlaf=com.sun.java.swing.plaf.gtk.GTKLookAndFeel'
```

存在三个这样的变量，在下表中解释的选项优先考虑。

<table class="wikitable"><tbody><tr><td>JAVA_TOOL_OPTIONS</td><td>影响应用程序以及javac、jshell等工具。</td></tr><tr><td>JDK_JAVA_OPTIONS</td><td>影响通过java命令启动的一切应用程序，需要Java 9。</td></tr><tr><td>（命令行选项）</td><td>在 "class name"参数前指定的参数是Java选项。</td></tr><tr><td>_JAVA_OPTIONS</td><td>旧方法，影响应用程序和工具。</td></tr></tbody></table>

### 更好的字体渲染

开源和闭源的Java实现都有不合适的抗锯齿字体实现。这可以通过以下办法来解决: `-Dawt.useSystemAAFontSettings=on`，`-Dswing.aatext=true`

详见[Java Runtime Environment fonts](../../05_桌面与图形/字体/Java%20运行环境字体配置.md)。

### 禁止命令行里的 'Picked up JDK_JAVA_OPTIONS' 消息

设置 JDK_JAVA_OPTIONS 环境变量会使Java（openjdk）向stderr写出以下形式的信息：'Picked up JDK_JAVA_OPTIONS=...'。为了禁止终端中显示这些信息，可以在`~/.bashrc`中取消设置环境变量，并alias java，将这些选项传递为命令行参数：

```
_SILENT_JAVA_OPTIONS="$_JAVA_OPTIONS"
unset _JAVA_OPTIONS
alias java='java "$_SILENT_JAVA_OPTIONS"'
```

非交互式的Shell，如Java程序的启动脚本，（通常）不读取`~/.bashrc`，但仍从其父进程中继承了导出的变量（而父进程又在某个时间从读取了`~/.bash_profile`的登录Shell中继承了它）。

至于这种情况，一般会在`~/.bashrc`的开头放一个声明，以避免读取文件。这样变量就会传递到通过桌面菜单启动的程序，如果是交互式Shell，则会使用alias来代替（这则不能用于脚本）。

### GTK LookAndFeel（外观与体验）

如果你的Java程序看起来很丑，你可能想为swing组件设置默认外观与体验：

```
swing.defaultlaf=com.sun.java.swing.plaf.gtk.GTKLookAndFeel
```

一些Java程序坚持用跨平台的金属风格外观与体验。在这些情况下，你可以通过设置下面的属性强制这些app用GTK外观和外观:

```
swing.crossplatformlaf=com.sun.java.swing.plaf.gtk.GTKLookAndFeel
```

### HiDPI

根据GUI框架的不同，[HiDPI#Java applications](../../05_桌面与图形/图形/HiDPI.md#Java_applications)可以使用不同的方法启用。

## 故障排除

### MySQL

由于JDBC-drivers经常使用URL中的端口来建立与数据库的连接，它被认为是 “远程”的（即MySQL不会按照其默认设置监听该端口），尽管它们可能运行在同一台主机上，因此，若要使用JDBC和MySQL，应按照[MariaDB#Grant remote access](../数据库管理系统/MariaDB.md#Grant_remote_access)中的说明，启用对MySQL的远程访问。

### IntelliJ IDEA

如果在设置JDK的时候选择了系统的JDK，同时碰到了错误提示`The selected directory is not a valid home for JDK`，此时应重新安装另一个JDK包，并在IDEA设置中选择它。

### 伪装成另一个窗口管理器

可以使用[suckless.org](https://tools.suckless.org/x/wmname)中的[wmname](https://archlinux.org/packages/?name=wmname)包来使JVM相信其正运行于其它窗口管理器。这也许能解决在[Awesome](../../05_桌面与图形/动态窗口管理器/Awesome.md)或[Dwm](../../05_桌面与图形/动态窗口管理器/Dwm.md)或[Ratpoison](../../05_桌面与图形/平铺式窗口管理器/Ratpoison.md)等窗口管理器中出现的Java GUI渲染问题。这种做法能够有效，是因为JVM包含了一个已知的、non-re-parenting窗口管理器的硬编码列表。为了达到最大的讽刺效果，一些用户喜欢伪装成`LG3D`，这是由Sun用Java编写的non-re-parenting窗口管理器[Project Looking Glass](https://en.wikipedia.org/wiki/Project_Looking_Glass)。尝试设置`compiz`、`Metacity`或`LG3D`：

```
$ wmname 窗口管理器名称
```

运行了这条命令后，必须重启有问题的程序。

另外，可以使用[javaagent JavaMatePatch](https://github.com/zheludkovm/JavaMatePatch)，它是在[MATE](../../05_桌面与图形/桌面环境/MATE.md)中设置WM名称并解决Java Swing应用程序在全屏启动时工作不正常的bug的工具。在Java选项中添加`-javaagent:JavaMatePatch-1.0.0-SNAPSHOT.jar=*窗口管理器名称*`来使用它。

### 字体难以辨认

除了下面[#更好的字体渲染](#更好的字体渲染)中的建议，有些字体可能依然难以辨认。使用微软的字体或许能有所改善，安装[ttf-ms-fonts](https://aur.archlinux.org/packages/ttf-ms-fonts/)AUR即可。

### 某些应用没有文字

如果某些应用完全没有文字，使用[FS#40871](https://bugs.archlinux.org/task/40871)中建议的[#提示和技巧](#提示和技巧)下的选项可能会有所帮助。

### 灰色窗口/应用不随窗口管理器调整大小/菜单自动关闭

标准的Java GUI工具包有一个non-re-parenting窗口管理器的硬编码列表，如果使用不在该列表中的窗口管理器，在运行某些Java应用时可能会有问题。最常见的问题之一是Java应用渲染成了一个灰色盒子而不是GUI。另一个问题是菜单能够响应点击，但马上又会关闭。

以下内容也许有所帮助：

- 见[#伪装成另一个窗口管理器](#伪装成另一个窗口管理器)。
- 设置`_JAVA_AWT_WM_NONREPARENTING=1` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)。
- 对于较新的版本，设置`AWT_TOOLKIT=MToolkit` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md)。
- 对于[xmonad](../../05_桌面与图形/平铺式窗口管理器/Xmonad.md)，使用[SetWMName](https://wiki.haskell.org/Xmonad/Frequently_asked_questions#Using_SetWMName)。然而，当同时使用`XMonad.Hooks.EwmhDesktops`时，其效果可能会被取消。这种情况下，在`LogHook`中添加`>> setWMName "LG3D"`可能会有帮助。

更多信息，请参阅[Java应用程序问题，Applet java控制台](https://wiki.haskell.org/Xmonad/Frequently_asked_questions#Problems_with_Java_applications.2C_Applet_java_console)。

### 调试JavaFX应用时系统卡住

如果调试JavaFX应用时系统卡住了，可以尝试提供JVM选项`-Dsun.awt.disablegrab=true`。

见。

### JavaFX's MediaPlayer constructor throws an exception

从JavaFX的声音模块中创建MediaPlayer类的实例可能会产生以下异常（Oracle JDK和OpenJDK都是如此）：

```
... (i.e. FXMLLoader construction exceptions) ...
Caused by: MediaException: UNKNOWN : com.sun.media.jfxmedia.MediaException: Could not create player! : com.sun.media.jfxmedia.MediaException: Could not create player!
 at javafx.scene.media.MediaException.exceptionToMediaException(MediaException.java:146)
 at javafx.scene.media.MediaPlayer.init(MediaPlayer.java:511)
 at javafx.scene.media.MediaPlayer.<init>(MediaPlayer.java:414)
 at <constructor call>
...
```

这是因为JavaFX和Arch Linux仓库中的[ffmpeg](https://archlinux.org/packages/?name=ffmpeg)包构建不兼容。

安装[ffmpeg-compat-55](https://aur.archlinux.org/packages/ffmpeg-compat-55/)AUR以解决问题。如果[ffmpeg-compat-55](https://aur.archlinux.org/packages/ffmpeg-compat-55/)AUR无法构建，可以尝试安装[ffmpeg3.4](https://aur.archlinux.org/packages/ffmpeg3.4/)AUR。

见。

### Java程序无法打开外部链接

如果Java应用无法将链接打开到浏览器等应用，请安装[gvfs](https://archlinux.org/packages/?name=gvfs)包，因为`Desktop.Action.BROWSE`办法依赖于它。见。

如果应用程序打印错误消息`java.lang.UnsupportedOperationException: The BROWSE action is not supported on the current platform!`，这通常是此问题的明确指示。

### Error initializing QuantumRenderer: no suitable pipeline found

可能的问题和解决方法：

- 没有GTK2。安装[gtk2](https://archlinux.org/packages/?name=gtk2)包。
- 没有OpenJFX。安装[java-openjfx](https://aur.archlinux.org/packages/java-openjfx/)AUR。

## 14.3　VirtualBox

*难度：高级 ｜ 篇幅：98.8 KB ｜ 所属：Oracle*

将要讲到：在 Arch 里安装 VirtualBox、在 VirtualBox 中安装 Arch Linux、虚拟磁盘管理、使用技巧、故障排除、参阅

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>

[VirtualBox](https://zh.wikipedia.org/wiki/VirtualBox) 是运行于现有操作系统之上的[虚拟机监视器](https://zh.wikipedia.org/wiki/Hypervisor)，用途是在特制环境（即[虚拟机](https://zh.wikipedia.org/wiki/%E8%99%9A%E6%8B%9F%E6%9C%BA)）里运行操作系统。[VirtualBox](https://www.virtualbox.org) 处于活跃开发状态，时常会引入新功能。VirtualBox 支持使用 [Qt](../../07_开发与工具/控件工具集/Qt.md) 图形界面，以及在[无界面](https://zh.wikipedia.org/wiki/%E6%97%A0%E5%A4%B4%E8%BD%AF%E4%BB%B6)模式下搭配 [SDL](https://zh.wikipedia.org/wiki/SDL) 命令行工具运行和管理虚拟机。

为了实现某些主-客户机间的整合功能，例如共享目录与剪贴板、显卡加速渲染、无缝窗口整合，VirtualBox 为部分操作系统提供了[客户机插件](#客户机插件)。

更多信息请参考 [官方文档](https://docs.oracle.com/en/virtualization/virtualbox/)。

## 在 Arch 里安装 VirtualBox

参考以下步骤在 Arch 主机系统中安装 VirtualBox。

### 安装基本软件包

安装 [virtualbox](https://archlinux.org/packages/?name=virtualbox)包，并安装内核模块 [virtualbox-host-dkms](https://archlinux.org/packages/?name=virtualbox-host-dkms)包。

为了编译 [virtualbox-host-dkms](https://archlinux.org/packages/?name=virtualbox-host-dkms)包 提供的 VirtualBox 内核模块，还需要安装你使用的内核所对应的头文件包（如 [linux-rt](https://archlinux.org/packages/?name=linux-rt)包 需安装 [linux-rt-headers](https://archlinux.org/packages/?name=linux-rt-headers)包）。 当 VirtualBox 或内核更新的时候，[DKMS](../../02_系统管理与内核/内核/DKMS.md) 的 [pacman 钩子](../../01_安装与包管理/包管理器/Pacman.md)会自动重新编译内核模块。

### 模块签名

如果你的 Linux 内核是自行编译的，并启用了 `CONFIG_MODULE_SIG_FORCE` 选项，那么你需要用编译内核时所使用的密钥为所有模块签名。

可以用根用户（root）身份执行以下命令来为模块进行签名：

```
# find "/lib/modules/$(uname -r)/" '(' -name 'vboxdrv.ko*' -o -name 'vboxnetadp.ko*' -o -name 'vboxnetflt.ko*' ')' -exec /lib/modules/$(uname -r)/build/scripts/sign-file sha256 /lib/modules/$(uname -r)/build/certs/signing_key.pem /lib/modules/$(uname -r)/build/certs/signing_key.x509 {} ';'
```

> **注意：**哈希算法不必与配置时选择的算法相匹配，但必须已被编译进内核。

If you experience an error such as the following:

```
At main.c:171:
- SSL error:FFFFFFFF80000002:system library::No such file or directory: crypto/bio/bss_file.c:67
- SSL error:10000080:BIO routines::no such file: crypto/bio/bss_file.c:75
sign-file: certs/signing_key.pem
```

Then run the command `cd /lib/modules/$(uname -r)/build` to navigate to your kernel tree folder and check if the `certs` folder actually has a `signing_key.pem` file. If not, create a file somewhere on your system (does not have to be in the kernel tree folder) named `x509.genkey` with the following contents (based on [Gentoo:Signed kernel module support#Building the kernel with proper keys](https://wiki.gentoo.org/wiki/Signed_kernel_module_support#Building_the_kernel_with_proper_keys)):

```
[ req ]
default_bits = 4096
distinguished_name = req_distinguished_name
prompt = no
string_mask = utf8only
x509_extensions = myexts

[ req_distinguished_name ]
CN = Modules

[ myexts ]
basicConstraints=critical,CA:FALSE
keyUsage=digitalSignature
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid
```

Then run `openssl req -new -nodes -utf8 -sha512 -days 36500 -batch -x509 -config x509.genkey -outform DER -out signing_key.x509 -keyout signing_key.pem` in the directory you created the `x509.genkey` file and move the resulting files to the `certs` directory in the kernel tree folder, then run `mokutil --import signing_key.x509` as root. You should then be able to rerun the signing command without an error.

If this still does not work, try updating your kernel to a newer version that has signing files already available (most kernel packages should), or if you are [compiling your own](../../02_系统管理与内核/内核/内核.md#Compilation), make sure that you copy the `src/(kernel version)/certs/signing_key.x509` and `src/(kernel version)/certs/signing_key.pem` from the folder you are building the kernel with to the `/lib/modules/$(uname -r)/build/certs` directory after you have built the kernel and are running it.

### 加载 VirtualBox 内核模块

[virtualbox-host-modules-arch](https://archlinux.org/packages/?name=virtualbox-host-modules-arch)包 和 [virtualbox-host-dkms](https://archlinux.org/packages/?name=virtualbox-host-dkms)包 使用 `systemd-modules-load.service` 在启动时自动加载 VirtualBox 的内核模块。若要在安装之后立即加载模块，可以手动加载一次，或者干脆重启。可以从 `/usr/lib/modules-load.d/virtualbox-host-modules-arch.conf`，`/usr/lib/modules-load.d/virtualbox-host-modules-lts.conf` 或 `/usr/lib/modules-load.d/virtualbox-host-dkms.conf` 中查看加载的模块列表。

> **注意：**如果希望启动时不自动加载 VirtualBox 模块，需要将默认的 `/usr/lib/modules-load.d/virtualbox-host-modules-arch.conf`，`/usr/lib/modules-load.d/virtualbox-host-modules-lts.conf` 或 `/usr/lib/modules-load.d/virtualbox-host-dkms.conf` 配置文件屏蔽掉，具体做法是在 `/etc/modules-load.d/` 目录里创建同名的空文件，或是创建同名并链接到 `/dev/null` 的符号链接。

在 VirtualBox 所使用的[内核模块](../../02_系统管理与内核/内核/内核模块.md)中，只有 `vboxdrv` 是必须的。该模块必须在虚拟机运行之前加载。

手动加载模块的命令是：

```
# modprobe vboxdrv
```

以下模块仅用于高级功能：

- `vboxnetadp` 和 `vboxnetflt`：这两个模块在使用[桥接网络](https://www.virtualbox.org/manual/ch06.html#network_bridged)或[仅主机网络](https://www.virtualbox.org/manual/ch06.html#network_hostonly)功能时，都是需要的。具体来说，`vboxnetadp` 模块用于在 VirtualBox 全局配置里创建主机虚拟网卡；`vboxnetflt` 模块会在启动使用了该网卡的虚拟机时用到。

> **注意：**如果在 VirtualBox 内核模块运行期间你更新了模块（所属的软件包），为了使用新版本，你需要手动重新加载这些模块。在 root 权限下运行 `vboxreload` 即可重新加载。

### 从客机系统访问主机 USB 设备

要让虚拟机访问主机的 USB 端口，需将允许使用该功能的用户添加到 `vboxusers` [用户组](../../08_安全与加密/安全/用户和用户组.md)中。

### 客户机插件

建议在运行 VirtualBox 的主机系统上安装 [virtualbox-guest-iso](https://archlinux.org/packages/?name=virtualbox-guest-iso)包 软件包。这个包里有个 `.iso` 镜像文件，用来为 Arch 之外的客户机系统安装插件。镜像文件的位置在 `/usr/lib/virtualbox/additions/VBoxGuestAdditions.iso`，手动在虚拟机的虚拟光驱里加载这个文件之后，即可在客户机里安装插件。对于 Arch Linux 客户机，请一并参考 [/在虚拟机中安装 Arch Linux#安装客户机插件](../../02_系统管理与内核/虚拟机管理/VirtualBox_在虚拟机中安装%20Arch%20Linux.md#安装客户机插件)

### 扩展包

*Oracle VM VirtualBox Extension Pack* 为虚拟机提供了[额外功能](https://www.virtualbox.org/manual/ch01.html#intro-installing)。但它并不是以自由软件协议发布的，*仅供个人使用*。这些扩展包可以从 [virtualbox-ext-oracle](https://aur.archlinux.org/packages/virtualbox-ext-oracle/)AUR 安装，也可以从 [seblu](../../01_安装与包管理/非官方用户仓库/非官方用户仓库.md#seblu) 仓库安装编译好的版本。

如果你喜欢使用传统的手动方法来安装扩展包，可以手动下载扩展包，然后通过 GUI 进行安装（*File > Tools > Extension Pack Manager*），也可以在命令行中执行 `VBoxManage extpack install *<.vbox-extpack>*`。确保你有类似 [Polkit](../../08_安全与加密/安全/Polkit.md) 的工具来在安装时为 VirtualBox 授予 [root 权限](https://www.virtualbox.org/ticket/8473)。

You can also install the [extension pack](https://www.virtualbox.org/wiki/Downloads) without using Polkit via the following command:

```
# vboxmanage extpack install path-to-extension-pack
```

One of the non-free extension pack features is support for the Remote Desktop Protocol (RDP). This part of functionality can also be obtained with the open source *VNC Extension Pack*, by installing the [virtualbox-ext-vnc](https://archlinux.org/packages/?name=virtualbox-ext-vnc)包 package.

### 前端

VirtualBox 自带四个前端：

- 如果你想通过常规 GUI 使用 VirtualBox，使用 `VirtualBox` 命令来启动 VirtualBox。
- 如果你想在命令行下启动与管理 VirtualBox，可以使用 `VBoxSDL` 命令。从 VBoxSDL 启动的虚拟机，其窗口仅包含虚拟机的画面，没有菜单或是其他控制项。
- 如果你想在使用 VirtualBox 不使用任何 GUI（例如在服务器上），使用 `VBoxHeadless` 命令。这时可以通过 VRDP 扩展来远程访问虚拟机的图形界面。
- If you want to remotely manage virtual machines, the *VirtualBox web service* (`vboxwebsrv`) provides the server side backend. It can be used with RemoteBox (GUI) or phpVirtualBox (WebUI).

Refer to the [VirtualBox manual](https://www.virtualbox.org/manual) to learn how to create virtual machines.

> **警告：**If you intend to store virtual disk images on a [Btrfs](../../02_系统管理与内核/文件系统/Btrfs.md) file system, before creating any images, you should consider disabling [copy-on-write](../../02_系统管理与内核/文件系统/Btrfs.md#Copy-on-Write_(CoW)) for the destination directory of these images.

A security feature in Wayland (i.e. when using GDM) disallows VirtualBox to grab all keyboard input. This is annoying when you want to pass window manager shortcuts to your guest operating system. It can be bypassed by whitelisting VirtualBox:

```
$ gsettings get org.gnome.mutter.wayland xwayland-grab-access-rules
$ gsettings set org.gnome.mutter.wayland xwayland-grab-access-rules "['VirtualBox Machine']"
```

The first command will show if any other applications are already whitelisted. If so, add *VirtualBox Machine* to that list, rather than having it as the only one.

## 在 VirtualBox 中安装 Arch Linux

请参阅 [/在虚拟机中安装 Arch Linux](../../02_系统管理与内核/虚拟机管理/VirtualBox_在虚拟机中安装%20Arch%20Linux.md)。

## 虚拟磁盘管理

另请参阅 [#Import/export VirtualBox virtual machines from/to other hypervisors](#Import/export_VirtualBox_virtual_machines_from/to_other_hypervisors)。

### VirtualBox 支持的格式

VirtualBox 支持下列虚拟硬盘格式：

- **VDI**: Virtual Disk Image 格式是 VirtualBox 新建虚拟机时默认选用的格式，也是 VirtualBox 自有的开放容器格式。

- **VMDK**: Virtual Machine Disk 最初是由 VMware 为其产品研发的格式。该格式技术设计文档最初是闭源的，而现在已经开源，在 VirtualBox 里完全可用。这种格式有个功能是：把一个虚拟机的虚拟硬盘分割成多个 2GB 大小的文件。如果你要把虚拟机镜像放在不支持大文件的文件系统（例如 FAT32）上，那么这个功能就非常有用。在其它的虚拟磁盘格式里，能做到同样功能的只有 Parallels 的 HDD。

- **VHD**: Virtual Hard Disk 是 Microsoft 为 Windows Virtual PC 与 Hyper-V 研发的格式。如果你想把虚拟机部署到这些平台上，那么你只能用这种格式。

: > **提示：**Windows 7 开始可以直接挂载 VHD 文件进行读写，不需要额外安装软件。

- **VHDX** (只读): 这是由 Microsoft 研发的 Virtual Hard Disk 格式的加强版。于 2012-09-04 与 Hyper-V 3.0 同步发布，二者都是 Windows Server 2012 的功能。该加强版的改进包括性能优化（源于区块对齐），支持大区块单位，还有应对断电的磁盘日志。VirtualBox [支持该格式的只读访问](https://www.virtualbox.org/manual/ch15.html#idp63002176)。

- **HDD** (V2): HDD 格式是由 Parallels Inc 研发的，由他们的虚拟机方案（如 Parallels Desktop for Mac）所使用。该格式的新版（v3 和 v4）由于缺少文档，又是专有格式，未能被 VirtualBox 支持。

    > **注意：**关于该格式“仅支持 V2 版”的说法目前有争议。VirtualBox 官方手册 [声称只支持 V2 版](https://www.virtualbox.org/manual/ch05.html#vdidetails)，但 Wikipedia 上的说法是 [V1 版也能正常工作](https://en.wikipedia.org/wiki/Comparison_of_platform_virtual_machines#Image_type_compatibility)。欢迎你对 V1 的支持状况做测试验证。

- **QED**: QEMU Enhanced Disk 是旧版 QEMU 使用的格式。QEMU 也是一个开源免费的虚拟机监视器。该格式设计于 2010 年，目的是要比 QCOW2 等格式更优秀。这种格式支持的功能包括全异步 I/O，数据高度完整性，文件备份及稀疏文件。VirtualBox 支持 QED 格式只是为了兼容由旧版 QEMU 创建的虚拟机。

- **QCOW**: QEMU Copy On Write 是 QEMU 现有版本支持的格式。QCOW 支持基于 zlib 实现的透明压缩与加密（加密功能有缺陷，不推荐使用）。QCOW 包括两个版本：QCOW 与 QCOW2。QCOW2 取代了旧版。[VirtualBox 完全支持旧版 QCOW](https://www.virtualbox.org/manual/ch15.html#idp63002176)。QCOW2 包含两个修订版：QCOW2 0.10 和 QCOW2 1.1（QEMU 新建的虚拟机默认使用 1.1）。然而 VirtualBox 并不支持 QCOW2。

- **OVF**: Open Virtualization Format 是设计用于解决不同虚拟机监视器间的虚拟机互操作性和分发性的开放格式。VirtualBox 支持该格式的所有修订版，具体的支持方式是 [VBoxManage import/export 命令](https://www.virtualbox.org/manual/ch08.html#idp55423424)，但也有[部分功能受限](https://www.virtualbox.org/manual/ch14.html#KnownProblems)。

- **RAW**: 该模式下虚拟硬盘会直接暴露到物理硬盘上，不以任何容器格式进行封装。VirtualBox 对此有多种支持方案：将 RAW 磁盘[转换成上述某种格式](https://www.virtualbox.org/manual/ch08.html#idp59139136)；[将物理盘复制到 RAW 格式](https://www.virtualbox.org/manual/ch08.html#vboxmanage-clonevdi)；直接挂载一个[指向物理盘或 RAW 格式文件](https://www.virtualbox.org/manual/ch09.html#idp57804112)的 VMDK 文件。

### 转换虚拟磁盘文件格式

[VBoxManage clonehd](https://www.virtualbox.org/manual/ch08.html#vboxmanage-clonevdi) 这个命令可以实现 VDI, VMDK, VHD 与 RAW 格式间的互转：

```
$ VBoxManage clonehd inputfile outputfile --format outputformat
```

以 VDI 转成 VMDK 为例：

```
$ VBoxManage clonehd source.vdi destination.vmdk --format VMDK
```

#### QCOW

VirtualBox 不支持 [QEMU](../../02_系统管理与内核/虚拟机管理/QEMU.md) 的 QCOW2 格式。若要让 VirtualBox 使用 QCOW2 格式的文件，你只能使用类似 [qemu-img](https://archlinux.org/packages/?name=qemu-img)包 的工具将其转换成已支持的格式。*qemu-img* 可以实现 QCOW 格式与 VDI, VMDK, VHDX, RAW 等其它格式间的互转。具体支持的格式可以通过运行 `qemu-img --help` 命令查看。

该命令的一般形式是：

```
$ qemu-img convert -O output_fmt inputfile outputfile
```

以 QCOW2 转成 VDI 为例：

```
$ qemu-img convert -O vdi source.qcow2 destination.vdi
```

> **提示：**转换时加上 `-p` 参数可以实时查看转换进度

QCOW2 有两个修订版：0.10 和 1.1，用 `-o compat=*revision*` 参数可以指定具体版本。

### 在主机挂载虚拟硬盘

#### VDI

只能挂载固定大小的 VDI 镜像（又名静态镜像），挂载动态镜像较为困难。

首先要拿到 VDI 里数据分区的偏移量 `offData`：

```
$ VBoxManage internalcommands dumphdinfo storage.vdi | grep "offData"
```

然后再加上 `32256`（例如 69632 + 32256 = 101888），然后用这个命令来挂载：

```
# mount -t ext4 -o rw,noatime,noexec,loop,offset=101888 storage.vdi /mntpoint/
```

For VDI disks with more partitions you can also use `losetup`:

```
# losetup -o $offData -Pf
```

After this you should find the partitions under `/dev/loop*` (e.g. `/dev/loop0p1`). Then you can mount them as usual (e.g. `mount mount /dev/loop0p1 /mnt/`).

另一个办法是用 [mount.vdi](https://github.com/pld-linux/VirtualBox/blob/master/mount.vdi) 脚本来完成挂载。首先要把脚本安装到 `/usr/bin/`，然后：

```
# mount -t vdi -o fstype=ext4,rw,noatime,noexec vdi_file_location /mnt/
```

还可以使用 [qemu-img](https://archlinux.org/packages/?name=qemu-img)包 提供的 nbd 内核模块和 *qemu-nbd* 工具：

```
# modprobe nbd max_part=16
# qemu-nbd -c /dev/nbd0 storage.vdi
# mount /dev/nbd0p1 /mnt/dir/
```

需要卸载镜像时执行：

```
# umount /mnt/dir/
# qemu-nbd -d /dev/nbd0
```

如果未能生成分区节点，试试运行命令：`partprobe /dev/nbd0`。另外，VDI 分区还可以直接用这个命令来映射到节点：`qemu-nbd -P 1 -c /dev/nbd0 *storage*.vdi`。

#### VHD

与 VDI 类似，可以使用 [QEMU](../../02_系统管理与内核/虚拟机管理/QEMU.md) 的 nbd 模块挂载 VHD 镜像：

```
# modprobe nbd
# qemu-nbd -c /dev/nbd0 storage.vhd
# mount /dev/nbd0p1 /mnt
```

需要卸载镜像时执行：

```
# umount /mnt
# qemu-nbd -d /dev/nbd0
```

### 压实虚拟硬盘

只有 *.vdi* 格式的虚拟磁盘文件可以压实，具体操作步骤如下：

启动虚拟机，手动删除无用文件，或者使用类似 [bleachbit](https://archlinux.org/packages/?name=bleachbit)包 的自动清理工具，[该工具也有 Windows 版](https://www.bleachbit.org/download/windows)。

下一步要用零字节来填充可用空间。这有如下的可行方案：

- 如果你已经在用 Bleachbit 了，在 GUI 菜单里选择 *System > Free disk space*，或者在命令行执行：`bleachbit -c system.free_disk_space`；
- 在类 UNIX 系统里，可以使用 `dd` 或 [dcfldd](https://aur.archlinux.org/packages/dcfldd/)AUR（参阅[这里](https://superuser.com/a/355322)来了解两者的区别）：

: ```
# dcfldd if=/dev/zero of=/fillfile bs=4M
```
: 当 `fillfile` 文件快占满分区时，会出现这样的错误信息：`1280 blocks (5120Mb) written.dcfldd:: No space left on device`。这意味着所有的可用空间与未保留区块都已经被零字节填满了。因为 ext 类系统会为 root 用户默认保留一部分硬盘空间（见 `mkfs.ext4` 手册页对 `-m` 参数的解释，或者用 `tune2fs -l` 命令来查看具体为 root 保留了多少空间），所以运行这一命令时需要有 root 权限。
: 前面一步操作完成后，手动把 `*fillfile*` 删掉。

- 在 Windows 系统里有两种办法：
    - 由 [Sysinternals Suite](https://technet.microsoft.com/en-us/sysinternals/bb842062.aspx) 提供的 `sdelete` 命令，用法是 `sdelete -s -z *c:*`。在虚拟机里的每一个分区都要执行一遍（当然 *c:* 这个参数要对应地改成各个分区的盘符）；
    - 如果你喜欢脚本，可以用这个 [PowerShell 实现的方案](https://blog.whatsupduck.net/2012/03/powershell-alternative-to-sdelete.html)，但依然要每个分区执行一次：

: : ```
PS> ./Write-ZeroesToFreeSpace.ps1 -Root c:\ -PercentFree 0
```
: > **注意：**该脚本需要在有管理员权限的 PowerShell 环境才能运行。默认的 PowerShell 配置下无法运行任何脚本，需要把执行策略至少调整到 `RemoteSigned`，而不能是 `Restricted`。用 `Get-ExecutionPolicy` 命令可以看到当前的执行策略，用 `Set-ExecutionPolicy RemoteSigned` 可以设置想要的执行策略。

完成这一步之后，将虚拟机关机。

下一次启动虚拟机时，推荐先进行文件系统检查：

- 在类 UNIX 系统上可以手动运行 `fsck` 来检查；
    - 在 GNU/Linux 系统上（包括 Arch）可以通过[内核启动参数](../../02_系统管理与内核/文件系统/Fsck.md#强制检查)在系统启动时强制执行硬盘检查；
- 在 Windows 系统上：
    - 可以使用 `chkdsk *c:* /F`，其中 `*c:*` 可以替换成所有你希望检查的盘符；
    - 或者从[这里](https://archive.org/details/ChkDskAll)下载 `FsckDskAll`。这和前面的 `chkdsk` 基本一样，只是不必手动为每个分区执行一遍了。

接下来通过 [VBoxManage modifyhd](https://www.virtualbox.org/manual/ch08.html#vboxmanage-modifyvdi) 移除 *.vdi* 文件中的零即可完成压实过程：

```
$ VBoxManage modifyhd your_disk.vdi --compact
```

> **注意：**
>
> - 如果你的虚拟机有保存过快照，那么每个 *.vdi* 文件都要单独执行一遍压实操作。
> - This concept of writing zeroes and compacting/reclaiming/cleanup space only works if there is no encryption on the virtual disk.
> - On Windows you can check whether BitLocker is enabled using the `manage-bde -status` command and disable it with `manage-bde -off c:`, where `c:` refers to the encrypted drive. After that, repeat the steps mentioned above.

#### TRIM

VirtualBox offers simulation of TRIM in VDI files via an experimental "discard" attachment option. This option is undocumented and can be accessed by [commandline or .vbox file editing](https://superuser.com/a/728469/). When enabled, TRIM commands from the guest operating system causes the corresponding part of the VDI file to be compacted away.

> **警告：**Using this option without Host I/O Cache is known to cause lockups.

### 扩充虚拟硬盘容量

#### 一般方法

如果你在创建虚拟机时给虚拟硬盘分配的容量太小了，VirtualBox 推荐的扩容方案是使用 [VBoxManage modifyhd](https://www.virtualbox.org/manual/ch08.html#vboxmanage-modifyvdi) 这个命令。然而这个命令只支持 VDI 和 VHD 这两种格式的动态分配容量版本。如果你想为固定大小的虚拟磁盘扩容，下面的办法适用于 Windows 或类 UNIX 系统的虚拟机。

首先创建一个新的虚拟磁盘：

```
$ VBoxManage createmedium disk -filename new.vdi --size 10000
```

`--size` 参数的值的单位是 MiB，在例子里 10000 MiB ~= 10 GiB，*new.vdi* 是新创建的镜像文件。

> **注意：**该命令默认会创建*标准*（即动态分配）版本的文件格式，如果想创建固定大小的镜像，需要追加参数 `--variant Fixed`。

接下来要把旧镜像的内容复制到新镜像里去，这一步骤可能会花些时间：

```
$ VBoxManage clonemedium disk old.vdi new.vdi --existing
```

卸载旧硬盘镜像，挂载新镜像，下面命令中的斜体字部分需要按照你的使用环境来换成真实的值：

```
$ VBoxManage storageattach virtual_machine_name --storagectl SATA --port 0 --medium none
$ VBoxManage storageattach virtual_machine_name --storagectl SATA --port 0 --medium new.vdi --type hdd
```

在上面的命令中，若要获知存储控制器的名字与端口号，可以使用命令：`VBoxManage showvminfo *virtual_machine_name*`。这会打印出如下的输出（斜体标注的信息是有用的）：

```
[...]
Storage Controller Name (0):            IDE
Storage Controller Type (0):            PIIX4
Storage Controller Instance Number (0): 0
Storage Controller Max Port Count (0):  2
Storage Controller Port Count (0):      2
Storage Controller Bootable (0):        on
Storage Controller Name (1):            SATA
Storage Controller Type (1):            IntelAhci
Storage Controller Instance Number (1): 0
Storage Controller Max Port Count (1):  30
Storage Controller Port Count (1):      1
Storage Controller Bootable (1):        on
IDE (1, 0): Empty
SATA (0, 0): /home/wget/IT/Virtual_machines/GNU_Linux_distributions/ArchLinux_x64_EFI/Snapshots/{6bb17af7-e8a2-4bbf-baac-fbba05ebd704}.vdi (UUID: 6bb17af7-e8a2-4bbf-baac-fbba05ebd704)
[...]
```

下载 [GParted Live 镜像](https://gparted.org/download.php)，在虚拟机里将其挂载到虚拟光驱中，启动虚拟机，调整分区大小 / 位置，卸载镜像并重启。这样扩容就完成了。

> **注意：**如果虚拟盘使用了 GPT 分区表，扩容会导致 GPT 备份头不再位于硬盘末尾。GParted 会询问是否要将其修复，此时两次询问都要点 *Fix*。如果是 MBR 分区，就不存在这一问题。

最后，从 VirtualBox 里注销旧的镜像文件，并把它删掉：

```
$ VBoxManage closemedium disk old.vdi
$ rm old.vdi
```

#### 扩容 VDI 硬盘

如果你的虚拟硬盘是 VDI 格式的，请使用：

```
$ VBoxManage modifymedium disk your_virtual_disk.vdi --resize the_new_size
```

然后回到上面的 Gparted 步骤，继续完成扩容操作。

### 修改 .vbox 文件来替换虚拟硬盘

如果你觉得编辑 *XML* 文件比用 GUI 或 `VBoxManage` 更方便，那可以通过在虚拟机的 *.vbox* 配置文件中替换 GUID、文件路径和格式来替换或添加虚拟硬盘：

```
ArchLinux_vm.vbox
```

```
<HardDisk uuid="{670157e5-8bd4-4f7b-8b96-9ee412a712b5}" location="ArchLinux_vm.vdi" format="VDI" type="Normal"/>
```

然后找到 `<StorageController>` 的子元素 `<AttachedDevice>`，把 GUID 属性改成新镜像文件的值：

```
ArchLinux_vm.vbox
```

```
<AttachedDevice type="HardDisk" port="0" device="0">
  <Image uuid="{670157e5-8bd4-4f7b-8b96-9ee412a712b5}"/>
</AttachedDevice>
```

> **注意：**如果你还不知道新镜像文件的 GUID 值，可以用命令 `VBoxManage showhdinfo *file*` 来查看。如果你用 `VBoxManage clonehd` 命令来处理过虚拟盘，那么在复制或转换过程完成时，也会打印出 GUID 值。随便写一个 GUID 上去是不行的，必须与[镜像文件里的 UUID 值](https://www.virtualbox.org/manual/ch05.html#cloningvdis)相对应才行。

#### 在 Linux 宿主机和其它系统间迁移

*.vbox* 配置文件把虚拟盘和快照文件的位置记录在 `<HardDisks> .... </HardDisks>` 标签里。如果新宿主系统存放虚拟机文件的路径与旧宿主系统不同，你可以手动修改路径值；如果 *.vbox* 文件与虚拟盘和快照文件夹位于相同目录，也可以使用以下脚本，它会将修改后的新配置打印到标准输出。

```
#!/bin/sh
NewPath="${PWD}/"
Snapshots="Snapshots/"
Filename="$1"

 awk -v SetPath="$NewPath" -v SnapPath="$Snapshots" '{if(index($0,"<HardDisk uuid=") != 0){A=$3;split(A,B,"=");
L=B;
 gsub(/\"/,"",L);
  sub(/^.*\//,"",L);
  sub(/^.*\\/,"",L);
 if(index($3,"{") != 0){SnapS=SnapPath}else{SnapS=""};
  print $1" "$2" location="\"SetPath SnapS L"\" "$4" "$5}
else print $0}' "$Filename"
```

> **注意：**
>
> - 如果你想把虚拟机迁移到 Windows 宿主系统上去，文件路径里应该使用 `\` 而不是 `/`。
> - 这个脚本判断快照的逻辑是：文件名是否包含 `{`。
> - 为了在新宿主机上运行起来，首先得在管理界面注册：点选菜单项 **Machine -> Add...** 或者按快捷键 `Ctrl+a` ，然后找到 *.vbox* 配置文件。也可以用命令行：`VBoxManage registervm *filename*.vbox`

### 复制虚拟盘并为其分配新 UUID

VirtualBox 广泛应用了 UUID。每个虚拟机和虚拟机的各个虚拟盘必须有不同的 UUID，在启动虚拟机时，VirtualBox 会跟踪虚拟机实例的所有 UUID。用 [VBoxManage list](https://www.virtualbox.org/manual/ch08.html#vboxmanage-list) 命令可以列出 VirtualBox 管理的所有资源。

如果你想复制一台虚拟机，仅复制虚拟盘镜像文件是不够的。你还得给复制出的新镜像文件分配一个新的 UUID。否则在同一个 VirtualBox 的环境里无法同时注册两个具有相同 UUID 的镜像文件。

下面这个命令可以用来为虚拟盘分配新 UUID：

```
$ VBoxManage internalcommands sethduuid /path/to/disk.vdi
```

> **提示：**用 [VBoxManage clonehd](https://www.virtualbox.org/manual/ch08.html#vboxmanage-clonevdi) 可以一次性完成复制内容与分配新 UUID

> **注意：**上述命令可以用于任意 [VirtualBox 所支持的镜像格式](#VirtualBox_支持的格式)。

## 使用技巧

### Import/export VirtualBox virtual machines from/to other hypervisors

If you plan to use your virtual machine on another hypervisor or want to import in VirtualBox a virtual machine created with another hypervisor, you might be interested in reading the following steps.

#### Remove additions

Guest additions are available in most hypervisor solutions: VirtualBox comes with the [Guest Additions](#Guest_additions), VMware with the VMware Tools, Parallels with the Parallels Tools, etc. These additional components are designed to be installed inside a virtual machine after the guest operating system has been installed. They consist of device drivers and system applications that optimize the guest operating system for better performance and usability [by providing these features](https://www.virtualbox.org/manual/ch04.html).

If you have installed the additions to your virtual machine, please uninstall them first. Your guest, especially if it is using an operating system from the Windows family, might behave weirdly, crash or even might not boot at all if you are still using the specific drivers in another hypervisor.

#### Use the right virtual disk format

This step will depend on the ability to convert the virtual disk image directly or not.

##### Automatic tools

Some companies provide tools which offer the ability to create virtual machines from a Windows or GNU/Linux operating system located either in a virtual machine or even in a native installation. With such a product, you do not need to apply this and the following steps and can stop reading here.

- *[Parallels Transporter](https://www.parallels.com/products/transporter)* which is non free, is a product from Parallels Inc. This solution basically consists in an piece of software called *agent* that will be installed in the guest you want to import/convert. Then, Parallels Transporter, *which only works on OS X*, will create a virtual machine from that *agent* which is contacted either by USB or Ethernet network.
- *[VMware vCenter Converter](https://www.vmware.com/products/converter/)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2024-07-30 ⓘ\]* which is free upon registration on the VMware website, works nearly the same way as Parallels Transporter, but the piece of software that will gather the data to create the virtual machine only works on a Windows platform.

##### Manual conversion

First, familiarize yourself with the [formats supported by VirtualBox](#Formats_supported_by_VirtualBox) and [those supported by third-party hypervisors](https://en.wikipedia.org/wiki/Comparison_of_platform_virtual_machines#Image_type_compatibility).

- Importing or exporting a virtual machine from/to a VMware solution is not a problem at all if you use the VMDK or OVF disk format, otherwise converting [VMDK to VDI and VDI to VMDK](#Disk_image_format_conversion) is possible and the aforementioned VMware vCenter Converter tool is available.
- Importing or exporting from/to QEMU is not a problem neither: some QEMU formats are supported directly by VirtualBox and conversion between [QCOW2 to VDI and VDI to QCOW2](#QCOW) is still available if needed.
- Importing or exporting from/to Parallels hypervisor is the hardest way: Parallels does only support its own HDD format (even the standard and portable OVF format is not supported!).

: -   To export your virtual machine to Parallels, you will need to use the Parallels Transporter tool described above.
- To import your virtual machine to VirtualBox, you will need to use the VMware vCenter Converter described above to convert the virtual machine to the VMware format first. Then, apply the solution to migrate from VMware.

#### Create the virtual machine configuration for your hypervisor

Each hypervisor have their own virtual machine configuration file: *.vbox* for VirtualBox, *.vmx* for VMware, a `config.pvs` file located in the virtual machine bundle (*.pvm* file), etc. You will have thus to recreate a new virtual machine in your new destination hypervisor and specify its hardware configuration as close as possible as your initial virtual machine.

Pay a close attention to the firmware interface (BIOS or UEFI) used to install the guest operating system. While an option is available to choose between these 2 interfaces on VirtualBox and on Parallels solutions, on VMware, you will have to add manually the following line to your *.vmx* file.

```
ArchLinux_vm.vmx
```

```
firmware = "efi"
```

Finally, ask your hypervisor to use the existing virtual disk you have converted and launch the virtual machine.

> **提示：**
>
> - On VirtualBox, if you do not want to browse the whole GUI to find the right location to add your new virtual drive device, you can [Replace a virtual disk manually from the .vbox file](#Replace_a_virtual_disk_manually_from_the_.vbox_file), or use the `VBoxManage storageattach` described in [#Increasing the size of VDI disks](#Increasing_the_size_of_VDI_disks) or in the [VirtualBox manual page](https://www.virtualbox.org/manual/ch08.html#vboxmanage-storageattach).
> - Similarly, in VMware products, you can replace the location of the current virtual disk location by adapting the *.vmdk* file location in your *.vmx* configuration file.

### Virtual machine launch management

#### Starting virtual machines with a service (autostart)

Find hereafter the implementation details of a systemd service that will be used to consider a virtual machine as a service.

```
/etc/systemd/system/vboxvmservice@.service
```

```
[Unit]
Description=VBox Virtual Machine %i Service
Requires=systemd-modules-load.service
After=systemd-modules-load.service

[Service]
User=username
Group=vboxusers
ExecStart=/usr/bin/VBoxManage startvm %i --type startmode
ExecStop=/usr/bin/VBoxManage controlvm %i stopmode
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

> **注意：**
>
> - Replace `*username*` with a user that is a member of the `vboxusers` group. Make sure the user chosen is the same user that will create/import virtual machines, otherwise the user will not see the virtual machine appliances.
> - Replace `*startmode*` with a virtual machine frontend type, usually `gui`, `headless` or `separate`
> - Replace `*stopmode*` with desired state switch, usually `savestate` or `acpipowerbutton`
> - If you have multiple virtual machines managed by systemd and they are not stopping properly, try to add `KillMode=none` and `TimeoutStopSec=40` at the end of `[Service]` section.

Enable the `vboxvmservice@*your_virtual_machine_name*` systemd unit in order to launch the virtual machine at next boot. To launch it directly, simply start the systemd unit.

VirtualBox 4.2 introduces [a new way](https://lifeofageekadmin.com/how-to-set-your-virtualbox-vm-to-automatically-startup/) for UNIX-like systems to have virtual machines started automatically, other than using a systemd service.

#### Starting virtual machines with a keyboard shortcut

It can be useful to start virtual machines directly with a keyboard shortcut instead of using the VirtualBox interface (GUI or CLI). For that, you can simply define key bindings in `.xbindkeysrc`. Please refer to Xbindkeys for more details.

Example, using the `Fn` key of a laptop with an unused battery key (`F3` on the computer used in this example):

```
"VBoxManage startvm 'Windows 7'"
m:0x0 + c:244
XF86Battery
```

> **注意：**If you have a space in the name of your virtual machine, then enclose it with single apostrophes like made in the example just above.

### Use specific device in the virtual machine

#### Using USB webcam / microphone

> **注意：**You will need to have [extension pack](#Extension_pack) installed before following the steps below.

1.  Make sure the virtual machine is not running and your webcam / microphone is not being used.
2.  Bring up the main VirtualBox window and go to settings for Arch machine. Go to USB section.
3.  Make sure *Enable USB Controller* is selected. Also make sure that *Enable USB 2.0 (EHCI) Controller* is selected too.
4.  Click the *Add filter from device* button (the cable with the *+* icon).
5.  Select your USB webcam/microphone device from the list.
6.  Now click OK and start your virtual machine.

> **注意：**If your Microphone does not show up in the "Add filter from device" menu, try the USB 3.0 and 1.1 options instead (In Step 3).

#### Detecting web-cams and other USB devices

> **注意：**This will not do much if you are running a Linux/Unix operating system inside of your virtual machine, as most do not have autodetection features.

If the device that you are looking for does not show up on any of the menus in the section above and you have tried all three USB controller options, boot up your virtual machine three separate times. Once using the USB 1.1 controller, another using the USB 2.0 controller, etc. Leave the virtual machine running for at least 5 minutes after startup. Sometimes Windows will autodetect the device for you. Be sure you filter any devices that are not a keyboard or a mouse so they do not start up at boot. This ensures that Windows will detect the device at start-up.

### Access a guest server

To access [Apache server](https://en.wikipedia.org/wiki/Apache_HTTP_Server) on a Virtual Machine from the host machine **only**, simply execute the following lines on the host:

```
$ VBoxManage setextradata GuestName "VBoxInternal/Devices/pcnet/0/LUN#0/Config/Apache/HostPort" 8888
$ VBoxManage setextradata GuestName "VBoxInternal/Devices/pcnet/0/LUN#0/Config/Apache/GuestPort" 80
$ VBoxManage setextradata GuestName "VBoxInternal/Devices/pcnet/0/LUN#0/Config/Apache/Protocol" TCP
```

where `*8888*` is the port the host should listen on and `*80*` is the port the virtual machine will send Apache's signal on.

To use a port lower than 1024 on the host machine, changes need to be made to the firewall on that host machine. This can also be set up to work with SSH or any other services by changing "Apache" to the corresponding service and ports.

> **注意：**`pcnet` refers to the network card of the virtual machine. If you use an Intel card in your virtual machine settings, change `pcnet` to `e1000`.

To communicate between the VirtualBox guest and host using ssh, the server port must be forwarded under Settings > Network. When connecting from the client/host, connect to the IP address of the client/host machine, as opposed to the connection of the other machine. This is because the connection will be made over a virtual adapter.

### D3D acceleration in Windows guests

Recent versions of VirtualBox have support for accelerating OpenGL inside guests. This can be enabled with a simple checkbox in the machine's settings, right below where video ram is set, and installing the VirtualBox [guest additions](#Guest_additions). However, most Windows games use Direct3D (part of DirectX), not OpenGL, and are thus not helped by this method. However, it is possible to gain accelerated Direct3D in your Windows guests by borrowing the d3d libraries from Wine, which translate d3d calls into OpenGL, which is then accelerated. These libraries are now part of VirtualBox guest additions.

After enabling OpenGL acceleration as described above, reboot the guest into safe mode (press F8 before the Windows screen appears but after the VirtualBox screen disappears), and install VirtualBox [guest additions](#Guest_additions), during install enable checkbox *Direct3D support*. Reboot back to normal mode and you should have accelerated Direct3D.

> **注意：**
>
> - This hack may or may not work for some games depending on what hardware checks they make and what parts of D3D they use.
> - This was tested on Windows XP, 7 and 8.1. If method does not work on your Windows version please add data here.

### VirtualBox on a USB key

When using VirtualBox on a USB key, for example to start an installed machine with an ISO image, you will manually have to create VMKDs from the existing drives. However, once the new VMDKs are saved and you move on to another machine, you may experience problems launching an appropriate machine again. To get rid of this issue, you can use the following script to launch VirtualBox. This script will clean up and unregister old VMDK files and it will create new, proper VMDKs for you:

> [![](https://wiki.archlinuxcn.org/wzh/images/8/87/Tango-edit-clear.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-clear.png)**本文或本章节的语言、语法或风格需要改进。参考：[帮助:风格](https://wiki.archlinuxcn.org/wzh/Help:%E9%A3%8E%E6%A0%BC)**
>
> **原因：**The following script parses the output of ls, which is [very brittle](https://github.com/koalaman/shellcheck/wiki/SC2010) and [known to break](https://github.com/koalaman/shellcheck/wiki/SC2012).（在[Talk:VirtualBox](https://wiki.archlinuxcn.org/wiki/Talk:VirtualBox)讨论）

```
#!/bin/sh
# Erase old VMDK entries
rm ~/.VirtualBox/*.vmdk

# Clean up VBox-Registry
sed -i '/sd/d' ~/.VirtualBox/VirtualBox.xml

# Remove old harddisks from existing machines
find ~/.VirtualBox/Machines -name \*.xml | while read -r file; do
  line=$(grep -e "type\=\"HardDisk\"" -n "$file" | cut -d ':' -f 1)
  if [ -n "$line" ]; then
    sed -i "${line}"d "$file"
    sed -i "${line}"d "$file"
    sed -i "${line}"d "$file"
  fi
  sed -i "/rg/d" "$file"
done

# Delete prev-files created by VirtualBox
find ~/.VirtualBox/Machines -name \*-prev -exec rm '{}' \;

# Recreate VMDKs
ls -l /dev/disk/by-uuid | cut -d ' ' -f 9,11 | while read -r ln; do
  if [ -n "$ln" ]; then
    uuid=$(echo "$ln" | cut -d ' ' -f 1)
    device=$(echo "$ln" | cut -d ' ' -f 2 | cut -d '/' -f 3 | cut -b 1-3)

    # determine whether drive is mounted already
    checkstr1=$(mount | grep "$uuid")
    checkstr2=$(mount | grep "$device")
    checkstr3=$(ls ~/.VirtualBox/*.vmdk | grep "$device")
    if [ -z "$checkstr1" ] && [ -z "$checkstr2" ] && [ -z "$checkstr3" ]; then
      VBoxManage internalcommands createrawvmdk -filename ~/.VirtualBox/"$device".vmdk -rawdisk /dev/"$device" -register
    fi
  fi
done

# Start VirtualBox
VirtualBox
```

Note that your user has to be added to the "disk" group to create VMDKs out of existing drives.

### Run a native Arch Linux installation inside VirtualBox

If you have a dual boot system between Arch Linux and another operating system, it can become tedious to switch back and forth if you need to work in both. You may also experience performance or compatibility issues when using a virtual machine, which can impact your ability to do certain tasks.

This guide will let you reuse, in a virtual machine, your native Arch Linux installation when you are running your second operating system. This way, you keep the ability to run each operating system natively, but have the option to run your Arch Linux installation inside a virtual machine.

#### Make sure you have a persistent naming scheme

Depending on your hard drive setup, device files representing your hard drives may appear differently when you will run your Arch Linux installation natively or in virtual machine. This problem occurs when using [FakeRAID](../../02_系统管理与内核/存储虚拟化/RAID.md#Implementation) for example. The fake RAID device will be mapped in `/dev/mapper/` when you run your GNU/Linux distribution natively, while the devices are still accessible separately. However, in your virtual machine, it can appear without any mapping in `/dev/sdaX` for example, because the drivers controlling the fake RAID in your host operating system (e.g. Windows) are abstracting the fake RAID device.

To circumvent this problem, we will need to use an addressing scheme that is persistent to both systems. This can be achieved using [UUID](../../04_硬件与外设/硬件检测和故障排除/块设备持久化命名.md)s. Make sure your [boot loader](../../01_安装与包管理/启动过程/Arch%20的启动流程.md) and [fstab](../../01_安装与包管理/启动过程/Fstab.md) file is using UUIDs, otherwise fix this issue. Read [fstab](../../01_安装与包管理/启动过程/Fstab.md) and [Persistent block device naming](../../04_硬件与外设/硬件检测和故障排除/块设备持久化命名.md).

> **警告：**
>
> - Make sure your host partition is only accessible in read only from your Arch Linux virtual machine, this will avoid risk of corruptions if you were to corrupt that host partition by writing on it due to lack of attention.
> - You should NEVER allow VirtualBox to boot from the entry of your second operating system, which, as a reminder, is used as the host for this virtual machine! Take thus a special care especially if your default boot loader/boot manager entry is your other operating system. Give a more important timeout or put it below in the order of preferences.

#### Make sure your mkinitcpio image is correct

Make sure your [mkinitcpio](../../01_安装与包管理/Initramfs/Mkinitcpio.md) configuration uses the [HOOK](../../01_安装与包管理/Initramfs/Mkinitcpio.md#HOOKS) `block`:

```
/etc/mkinitcpio.conf
```

```
...
HOOKS=(base udev autodetect microcode modconf kms keyboard keymap consolefont block filesystems fsck)
...
```

If it is not present, add it and [regenerate the initramfs](../../01_安装与包管理/Initramfs/Mkinitcpio.md).

#### Create a virtual machine configuration to boot from the physical drive

##### Create a raw disk VMDK image

Now, we need to create a new virtual machine which will use a [RAW disk](https://www.virtualbox.org/manual/ch09.html#rawdisk) as virtual drive, for that we will use a ~ 1Kio VMDK file which will be mapped to a physical disk. Unfortunately, VirtualBox does not have this option in the GUI, so we will have to use the console and use an internal command of `VBoxManage`.

Boot the host which will use the Arch Linux virtual machine. The command will need to be adapted according to the host you have.

**On a GNU/Linux host**

There are 3 ways to achieve this: login as root, changing the access right of the device with `chmod`, adding your user to the `disk` group. The latter way is the more elegant, let us proceed that way:

```
# gpasswd -a your_user disk
```

Apply the new group settings with:

```
$ newgrp
```

Now, you can use the command:

```
$ VBoxManage internalcommands createrawvmdk -filename /path/to/file.vmdk -rawdisk /dev/sdb -register
```

Adapt the above command to your need, especially the path and filename of the VMDK location and the raw disk location to map which contain your Arch Linux installation.

**On a Windows host**

Open a command prompt must be run as administrator.

> **提示：**On Windows, open your start menu/start screen, type `cmd`, and type `Ctrl+Shift+Enter`, this is a shortcut to execute the selected program with admin rights.

On Windows, as the disk filename convention is different from UNIX, use this command to determine what drives you have in your Windows system and their location:

```
# wmic diskdrive get name,size,model
```

```
Model                               Name                Size
WDC WD40EZRX-00SPEB0 ATA Device     \\.\PHYSICALDRIVE1  4000783933440
KINGSTON SVP100S296G ATA Device     \\.\PHYSICALDRIVE0  96024821760
Hitachi HDT721010SLA360 ATA Device  \\.\PHYSICALDRIVE2  1000202273280
Innostor Ext. HDD USB Device        \\.\PHYSICALDRIVE3  1000202273280
```

In this example, as the Windows convention is `\\.\PhysicalDriveX` where X is a number from 0, `\\.\PHYSICALDRIVE1` could be analogous to `/dev/sdb` from the Linux disk terminology.

To use the `VBoxManage` command on Windows, you can either, change the current directory to your VirtualBox installation folder first with `cd C:\Program Files\Oracle\VirtualBox\`

```
# .\VBoxManage.exe internalcommands createrawvmdk -filename C:\file.vmdk -rawdisk \\.\PHYSICALDRIVE1
```

or use the absolute path name:

```
# "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" internalcommands createrawvmdk -filename C:\file.vmdk -rawdisk \\.\PHYSICALDRIVE1
```

**On another operating system host**

There are other limitations regarding the aforementioned command when used in other operating systems like OS X, please thus [read carefully the manual page](https://www.virtualbox.org/manual/ch09.html#rawdisk), if you are concerned.

##### Create the virtual machine configuration file

> **注意：**
>
> - To make use of the VBoxManage command on Windows, you need to change the current directory to your VirtualBox installation folder first: `cd C:\Program Files\Oracle\VirtualBox\`.
> - Windows makes use of backslashes instead of slashes, please replace all slashes `/` occurrences by backslashes `\` in the commands that follow when you will use them.

After, we need to create a new machine (replace the *virtual_machine_name* to your convenience) and register it with VirtualBox.

```
$ VBoxManage createvm -name virtual_machine_name -register
```

Then, the newly raw disk needs to be attached to the machine. This will depend if your computer or actually the root of your native Arch Linux installation is on an IDE or a SATA controller.

If you need an IDE controller:

```
$ VBoxManage storagectl virtual_machine_name --name "IDE Controller" --add ide
$ VBoxManage storageattach virtual_machine_name --storagectl "IDE Controller" --port 0 --device 0 --type hdd --medium /path/to/file.vmdk
```

otherwise:

```
$ VBoxManage storagectl virtual_machine_name --name "SATA Controller" --add sata
$ VBoxManage storageattach virtual_machine_name --storagectl "SATA Controller" --port 0 --device 0 --type hdd --medium /path/to/file.vmdk
```

While you continue using the CLI, it is recommended to use the VirtualBox GUI, to personalise the virtual machine configuration. Indeed, you must specify its hardware configuration as close as possible as your native machine: turning on the 3D acceleration, increasing video memory, setting the network interface, etc.

Finally, you may want to seamlessly integrate your Arch Linux with your host operating system and allow copy pasting between both operating systems. Please refer to [VirtualBox/Install Arch Linux as a guest#Install the Guest Additions](../../02_系统管理与内核/虚拟机管理/VirtualBox_在虚拟机中安装%20Arch%20Linux.md#Install_the_Guest_Additions) for that, since this Arch Linux virtual machine is basically an Arch Linux guest.

> **警告：**For [Xorg](../../05_桌面与图形/X%20服务器/Xorg.md) to work in natively and in the virtual machine, since obviously it will be using different drivers, it is best if there is no `/etc/X11/xorg.conf`, so Xorg will pick up everything it needs on the fly. However, if you really do need your own Xorg configuration, maybe is it worth to set your default systemd target to `multi-user.target` with `systemctl isolate graphical.target` as root (more details at [systemd#Targets](../../01_安装与包管理/初始化/Systemd.md#Targets) and [systemd#Change current target](../../01_安装与包管理/初始化/Systemd.md#Change_current_target)). In that way, the graphical interface is disabled (i.e. Xorg is not launched) and after you logged in, you can `startx`} manually with a custom `xorg.conf`.

### Install a native Arch Linux system from VirtualBox

In some cases it may be useful to install a native Arch Linux system while running another operating system: one way to accomplish this is to perform the installation through VirtualBox on a [raw disk](https://www.virtualbox.org/manual/ch09.html#rawdisk). If the existing operating system is Linux based, you may want to consider following [Install from existing Linux](../../01_安装与包管理/安装过程/从现有%20Linux%20发行版安装%20Arch%20Linux.md) instead.

This scenario is very similar to [#Run a native Arch Linux installation inside VirtualBox](#Run_a_native_Arch_Linux_installation_inside_VirtualBox), but will follow those steps in a different order: start by [#Create a raw disk VMDK image](#Create_a_raw_disk_VMDK_image), then [#Create the virtual machine configuration file](#Create_the_virtual_machine_configuration_file).

Now, you should have a working virtual machine configuration whose virtual VMDK disk is tied to a real disk. The installation process is exactly the same as the steps described in [VirtualBox/Install Arch Linux as a guest](../../02_系统管理与内核/虚拟机管理/VirtualBox_在虚拟机中安装%20Arch%20Linux.md), but [#Make sure you have a persistent naming scheme](#Make_sure_you_have_a_persistent_naming_scheme) and [#Make sure your mkinitcpio image is correct](#Make_sure_your_mkinitcpio_image_is_correct).

> **警告：**
>
> - For BIOS systems and MBR disks, do not install a boot loader inside your virtual machine, this will not work since the MBR is not linked to the MBR of your real machine and your virtual disk is only mapped to a real partition without the MBR.
> - For UEFI systems without [CSM](https://en.wikipedia.org/wiki/Compatibility_Support_Module#CSM) and GPT disks, the installation will not work at all since:
> - the [EFI system partition](../../01_安装与包管理/启动过程/EFI%20系统分区.md) is not mapped to your virtual disk and Arch Linux requires to have the Linux kernel on it to boot as an EFI application (see EFI boot stub for details);
> - and the efivars, if you are installing Arch Linux using the EFI mode brought by VirtualBox, are not the one of your real system: the bootmanager entries will hence not be registered.
> - This is why, it is recommended to create your partitions in a native installation first, otherwize the partitions will not be taken into consideration in your MBR/GPT partition table.

After completing the installation, boot your computer natively with an GNU/Linux installation media (whether it be Arch Linux or not), [chroot](../../01_安装与包管理/安装过程/安装指南.md#Chroot) into your installed Arch Linux installation and install and configure a [boot loader](../../01_安装与包管理/启动过程/Arch%20的启动流程.md).

### Install MacOS guest

Before starting the virtual machine, run the following commands on the host machine :

```
$ VBoxManage modifyvm "MyMacVM" --cpuid-set 00000001 000106e5 00100800 0098e3fd bfebfbff
$ VBoxManage setextradata "MyMacVM" "VBoxInternal/Devices/efi/0/Config/DmiSystemProduct" "iMac11,3"
$ VBoxManage setextradata "MyMacVM" "VBoxInternal/Devices/efi/0/Config/DmiSystemVersion" "1.0"
$ VBoxManage setextradata "MyMacVM" "VBoxInternal/Devices/efi/0/Config/DmiBoardProduct" "Iloveapple"
$ VBoxManage setextradata "MyMacVM" "VBoxInternal/Devices/smc/0/Config/DeviceKey" "ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc"
$ VBoxManage setextradata "MyMacVM" "VBoxInternal/Devices/smc/0/Config/GetKeyFromRealSMC" 1
$ VBoxManage setextradata "MyMacVM" VBoxInternal2/EfiGopMode 4
```

If you use an AMD processor and the first boot gets stuck, you also have to run

```
$ VBoxManage modifyvm "MyMacVM" --cpu-profile "Intel Core i7-6700K"
```

#### No keyboard/mouse input when attempting to install Mojave

If you are attempting to install Mojave, after doing the aforementioned steps, the installer will load up but you might not be able to send keyboard or mouse input. The reason seems to be that Mojave no longer supports the USB 1.1 controllers and in order to fix the issue you need to emulating USB 3.0. To do that first install the [extension pack](#Extension_pack).

Then go to *Machine > Settings > USB* and select *USB 3.0*. Input should work from this point onwards.

#### UEFI interactive shell after restart

If the installer is unable to properly format the bootable drive during installation and you end up in an UEFI shell, enter the following:

1.  Type `exit` at the UEFI prompt
2.  Select *Boot Maintenance Manager*
3.  Select *Boot From File*

You will now be brought to couple of obscure PCI paths. The first one is the one that you just attempted to boot from and it did not work. The second (or third) one should be the one with the MacOS recovery partition that you need to load to continue the installation. Click the second Entry. If it is empty, press `Esc` to go back and select the third entry. Once you get one with folders click though the folders. It should be something like *macOS Install Data > Locked Files > Boot Files > boot.efi*. Once you click enter on the *boot.efi* you should boot into the MacOS installer and resume installation. Note that some of the subdirectories might be missing. Remember that you need to get to a `boot.efi`.

### Move a native Windows installation to a virtual machine

If you want to migrate an existing native Windows installation to a virtual machine which will be used with VirtualBox on GNU/Linux, this use case is for you. This section only covers native Windows installation using the MSDOS/Intel partition scheme. Your Windows installation must reside on the first MBR partition for this operation to success. Operation for other partitions are available but have been untested (see [#Known limitations](#Known_limitations) for details).

> **警告：**If you are using an OEM version of Windows, this process is unauthorized by the end user license license. Indeed, the OEM license typically states the Windows install is tied with the hardware together. Transferring a Windows install to a virtual machine removes this link. Make thus sure you have a full Windows install or a volume license model before continuing. If you have a full Windows license but the latter is not coming in volume, nor as a special license for several PCs, this means you will have to remove the native installation after the transfer operation has been achieved.

A couple of tasks are required to be done inside your native Windows installation first, then on your GNU/Linux host.

#### Tasks on Windows

The first three following points comes from [this outdated VirtualBox wiki page](https://www.virtualbox.org/wiki/Migrate_Windows#HAL), but are updated here.

- Remove IDE/ATA controllers checks (Windows XP only): Windows memorize the IDE/ATA drive controllers it has been installed on and will not boot if it detects these have changed. The solution proposed by Microsoft is to reuse the same controller or use one of the same serial, which is impossible to achieve since we are using a Virtual Machine. [MergeIDE](https://www.virtualbox.org/wiki/Migrate_Windows#HardDiskSupport), a German tool, developped upon another other solution proposed by Microsoft can be used. That solution basically consists in taking all IDE/ATA controller drivers supported by Windows XP from the initial driver archive (the location is hard coded, or specify it as the first argument to the *.bat* script), installing them and registering them with the regedit database.
- Use the right type of Hardware Abstraction Layer (old 32 bits Windows versions): Microsoft ships 3 default versions: `Hal.dll` (Standard PC), `Halacpi.dll` (ACPI HAL) and `Halaacpi.dll` (ACPI HAL with IO APIC). Your Windows install could come installed with the first or the second version. In that way, please [disable the *Enable IO/APIC* VirtualBox extended feature](https://www.virtualbox.org/manual/ch08.html#idp56927888).
- Disable any AGP device driver (only outdated Windows versions): If you have the files `agp440.sys` or `intelppm.sys` inside the `C:\Windows\SYSTEM32\drivers\` directory, remove it. As VirtualBox uses a PCI virtual graphics card, this can cause problems when this AGP driver is used.
- Create a Windows recovery disk: In the following steps, if things turn bad, you will need to repair your Windows installation. Make sure you have an install media at hand, or create one with *Create a recovery disk* from Vista SP1, *Create a system repair disc* on Windows 7 or *Create a recovery drive* on Windows 8.x).

#### Using Disk2vhd to clone Windows partition

Boot into Windows, clean up the installation (with [CCleaner](https://www.piriform.com/ccleaner) for example), use [disk2vhd](https://technet.microsoft.com/en-us/library/ee656415.aspx) tool to create a VHD image. Include a reserved system partition (if present) and the actual Windows partition (usually disk C:). The size of Disk2vhd-created image will be the sum of the actual files on the partition (used space), not the size of a whole partition. If all goes well, the image should just boot in a virtual machine and you will not have to go through the hassle with MBR and Windows boot loader, as in the case of cloning an entire partition.

#### Tasks on GNU/Linux

> **提示：**Skip the partition-related parts if you created VHD image with [Disk2vhd](#Using_Disk2vhd_to_clone_Windows_partition).

- Reduce the native Windows partition size to the size Windows actually needs with `ntfsresize` available from [ntfs-3g](https://archlinux.org/packages/?name=ntfs-3g)包. The size you will specify will be the same size of the VDI that will be created in the next step. If this size is too low, you may break your Windows install and the latter might not boot at all.

: Use the `--no-action` option first to run a test:
: ```
# ntfsresize --no-action --size 52Gi /dev/sda1
```

: If only the previous test succeeded, execute this command again, but this time without the aforementioned test flag.

- Install VirtualBox on your GNU/Linux host (see [#Installation steps for Arch Linux hosts](#Installation_steps_for_Arch_Linux_hosts) if your host is Arch Linux).

- Create the Windows disk image from the beginning of the drive to the end of the first partition where is located your Windows installation. Copying from the beginning of the disk is necessary because the MBR space at the beginning of the drive needs to be on the virtual drive along with the Windows partition. In this example two following partitions `sda2` and `sda3`will be later removed from the partition table and the MBR boot loader will be updated.

: ```
# sectnum=$(( $(cat /sys/block/sda/sda1/start) + $(cat /sys/block/sda/sda1/size) ))
```
: Using `cat /sys/block/*sda/sda1*/size` will output the number of total sectors of the first partition of the disk `sda`. Adapt where necessary.

: ```
# dd if=/dev/sda bs=512 count=$sectnum | VBoxManage convertfromraw stdin windows.vdi $(( $sectnum * 512 ))
```
: We need to display the size in byte, `$(( $sectnum * 512 ))` will convert the sector numbers to bytes.

- Since you created your disk image as root, set the right ownership to the virtual disk image:

    ```
    # chown your_user:your_group windows.vdi
    ```

- Create your virtual machine configuration file and use the virtual disk created previously as the main virtual hard disk.

- Try to boot your Windows virtual machine, it may just work. First though remove and repair disks from the boot process as it may interfere (and likely will) booting into safe-mode.

- Attempt to boot your Windows virtual machine in safe mode (press the F8 key before the Windows logo shows up)... if running into boot issues, read [#Fix MBR and Microsoft boot loader](#Fix_MBR_and_Microsoft_boot_loader). In safe-mode, drivers will be installed likely by the Windows plug-and-play detection mechanism [view](https://i.imgur.com/hh1RrSp.png). Additionally, install the VirtualBox [Guest Additions](#Guest_additions) via the menu *Devices* > *Insert Guest Additions CD image...*. If a new disk dialog does not appear, navigate to the CD drive and start the installer manually.

- You should finally have a working Windows virtual machine. Do not forget to read the [#Known limitations](#Known_limitations).

- Performance tip: according to [VirtualBox manual](https://www.virtualbox.org/manual/ch05.html#harddiskcontrollers), SATA controller has a better performance than IDE. If you cannot boot Windows off virtual SATA controller right away, it is probably due to the lack of SATA drivers. Attach virtual disk to IDE controller, create an empty SATA controller and boot the virtual machine - Windows should automatically install SATA drivers for the controller. You can then shutdown the virtual machine, detach virtual disk from IDE controller and attach it to SATA controller instead.

#### Fix MBR and Microsoft boot loader

If your Windows virtual machine refuses to boot, you may need to apply the following modifications to your virtual machine.

- Boot a GNU/Live live distribution inside your virtual machine before Windows starts up.

- Remove other partitions entries from the virtual disk MBR. Indeed, since we copied the MBR and only the Windows partition, the entries of the other partitions are still present in the MBR, but the partitions are not available anymore. Use `fdisk` to achieve this for example.

: ```
fdisk ''/dev/sda''
Command (m for help): a
Partition number (''1-3'', default ''3''): ''1''
```

- Write the updated partition table to the disk (this will recreate the MBR) using the `m` command inside `fdisk`.

- Use [testdisk](https://archlinux.org/packages/?name=testdisk)包 (see [here](https://www.cgsecurity.org/index.html?testdisk.html) for details) to add a generic MBR:

: ```
# testdisk > Disk /dev/sda... > [Proceed] > [Intel] Intel/PC partition > [MBR Code] Write TestDisk MBR to first sector > Write a new copy of MBR code to first sector? (Y/n) > Y > Write a new copy of MBR code, confirm? (Y/N) > A new copy of MBR code has been written. You have to reboot for the change to take effect. > [OK]
```

- With the new MBR and updated partition table, your Windows virtual machine should be able to boot. If you are still encountering issues, boot your Windows recovery disk from on of the previous step, and inside your Windows RE environment, execute the commands [described here](https://support.microsoft.com/kb/927392).

#### Known limitations

- Your virtual machine can sometimes hang and overrun your RAM, this can be caused by conflicting drivers still installed inside your Windows virtual machine. Good luck to find them!
- Additional software expecting a given driver beneath may either not be disabled/uninstalled or needs to be uninstalled first as the drivers that are no longer available.
- Your Windows installation must reside on the first partition for the above process to work. If this requirement is not met, the process might be achieved too, but this had not been tested. This will require either copying the MBR and editing in hexadecimal see [VirtualBox: booting cloned disk](https://superuser.com/questions/237782/virtualbox-booting-cloned-disk/253417#253417) or will require to fix the partition table [manually](https://gparted.org/h2-fix-msdos-pt.php) or by repairing Windows with the recovery disk you created in a previous step. Let us consider our Windows installation on the second partition; we will copy the MBR, then the second partition where to the disk image. `VBoxManage convertfromraw` needs the total number of bytes that will be written: calculated thanks to the size of the MBR (the start of the first partition) plus the size of the second (Windows) partition. `{ dd if=/dev/sda bs=512 count=$(cat /sys/block/sda/sda1/start) ; dd if=/dev/sda2 bs=512 count=$(cat /sys/block/sda/sda2/size) ; } | VBoxManage convertfromraw stdin windows.vdi $(( ($(cat /sys/block/sda/sda1/start) + $(cat /sys/block/sda/sda2/size)) * 512 ))`.

### Run a native Windows installation inside VirtualBox

> **注意：**The technique outlined in this section only applies to [UEFI](../../01_安装与包管理/启动过程/UEFI.md) systems.

In some cases, it is useful to be able to [dual boot with Windows](../../01_安装与包管理/启动过程/Arch%20+%20Windows%20双系统.md) *and* access the partition in a virtual machine. This process is significantly different from [#Move a native Windows installation to a virtual machine](#Move_a_native_Windows_installation_to_a_virtual_machine) in several ways:

- The Windows partition is not copied to a virtual disk image. Instead, a raw VMDK file is created;
- Changes in the virtual machine will be mirrored in the partition, and vice versa;
- OEM licenses should still be satisfied, since the Windows partition still boots directly on the hardware.

> **警告：**Some of the commands used here can corrupt either the Windows partition, the Arch Linux partition, or both. Use extreme caution when executing commands, and double check that they are being run in the correct shell. It would be a good idea to have a backup of the entire drive ready before beginning this process.

> **注意：**Before proceeding be sure to have access to a Windows installation media (such as the [Windows 11 ISO](https://www.microsoft.com/en-us/software-download/windows11)).

#### Creating the virtual machine

A VirtualBox virtual machine must be manually created. As of now do not add any storage device any disk to the virtual machine, it will be done manually later.

Configure the virtual machine with the following settings (settings panel can be opened by clicking the "Settings" button in the main toolbar):

- View: System:
    - Tab: Motherboard:
        - mark *Enable I/O APIC*;
        - mark *Enable EFI*;
        - mark *Hardware Clock in UTC Time* if is your case.
    - Tab: Processor:
        - mark *Enable PAE/NX*;
        - mark *Enable Nested VT-x/AMD-V*;
    - Tab: Acceleration:
        - Choose the paravirtualization interface *Hyper-V* from the drop down menu;
        - mark *Enable Nested Paging*.

Optionally you can enable also the following settings:

- View: Display
    - Tab: Screen
        - mark *Enable 3D Acceleration*. Note that it could cause glitches.

> **注意：**The *Hyper-V* setting is not required in order for the system to operate correctly, but it may help avoid licensing issues.

#### Creating virtual machine disks

To access the Windows partitions, create a [raw VMDK file](https://www.virtualbox.org/manual/ch09.html#rawdisk-access-disk-partitions) pointing to the relevant Windows partitions (root privileges are required to read disk partition table):

```
# VBoxManage createmedium disk -filename VM_DIRECTORY/windows.vmdk --format=VMDK --variant RawDisk --property RawDrive=DISK --property Partitions=RESERVED_PARTITION_NUMBER,BASIC_DATA_PARTITION_NUMBER
```

Replace capitalized placeholder strings as follow:

- `*VM_DIRECTORY*` with the path of the virtual machine folder (usually a subdirectory of `~/VirtualBox VMs`;
- `*DISK*` must be replaced with the block device containing all the Windows partitions (e.g.: `/dev/sda` or `/dev/nvme0n1`);
- `*RESERVED_PARTITION_NUMBER*` must be replaced with the number of partition labeled "Microsoft reserved partition" (e.g.: if the partition is the `/dev/sda2` the number will be `2`);
- `*BASIC_DATA_PARTITION_NUMBER*` must be replaced with the partition containing the Windows installation (e.g.: if the partition is the `/dev/sda3` the number will be `3`);

Example:

```
# VBoxManage createmedium disk -filename "/home/user/VirtualBox VMs/windows.vmdk" --format=VMDK --variant RawDisk --property RawDrive=/dev/nvme0n1 --property Partitions=2,3
```

The command will also create an extra file inside the virtual machine folder, "windows-pt.vmdk", that will be just ignored.

> **注意：**`windows.vmdk` must be re-created if the partition table is changed.

> **提示：**
>
> Partition numbers can be found also by running this command and looking at the MIN column:
>
> ```
> lsblk --output NAME,PARTLABEL,FSTYPE,MAJ:MIN,SIZE
> ```
>
> ```
> NAME        PARTLABEL                    FSTYPE UUID                                 MAJ:MIN   SIZE
> nvme0n1                                                                              259:0   931,5G
> ├─nvme0n1p1 EFI system partition         vfat   90DC-A6B3                            259:1     100M
> ├─nvme0n1p2 Microsoft reserved partition                                             259:2      16M
> ├─nvme0n1p3 Basic data partition         ntfs   D2A2A104A2A0EE63                     259:3     200G
> ...
> ```

Now change the virtual disk owner to give access the user and group running VirtualBox.

```
# chown VIRTUALBOX_RUNNING_USER:VIRTUALBOX_RUNNING_GROUP VM_DIRECTORY/windows.vmdk VM_DIRECTORY/windows-pt.vmdk
```

Replace `*VIRTUALBOX_RUNNING_USER*` and `*VIRTUALBOX_RUNNING_GROUP*` with the user and the group that will run VirtualBox, which most likely will be your user.

#### Allowing VirtualBox to read physical partitions

VirtualBox must have [raw disk access](https://www.virtualbox.org/manual/ch09.html#rawdisk) in order to run a Windows partition. Normally, this would require VirtualBox to be run with full root privileges, but more elegant options are available.

##### Higher security option: using a dedicated group for the Windows partitions

Here [udev](../../04_硬件与外设/硬件检测和故障排除/Udev.md) is configured to restrict the access to partitions Windows partitions to the *vboxusers* group, and then the user running VirtualBox is added to the group.

Assigning the disks to the *vboxusers* group can be done automatically by creating the following file:

```
/etc/udev/rules.d/99-vbox.rules
```

```
#
# Rules to give VirtualBox users raw access to Windows partitions
#

# Microsoft Reserved partition
SUBSYSTEM=="block", ENV{ID_PART_ENTRY_TYPE}=="e3c9e316-0b5c-4db8-817d-f92df00215ae", GROUP="vboxusers"

# Windows partition
SUBSYSTEM=="block", ENV{ID_PART_ENTRY_TYPE}=="ebd0a0a2-b9e5-4433-87c0-68b6b72699c7", GROUP="vboxusers"

#
# Rules to give VirtualBox users raw access to Windows disk
#

# sdb
ENV{ID_PART_TABLE_UUID}=="WINDOWS_DISK_ID_PART_TABLE_UUID", GROUP="vboxusers"
```

`WINDOWS_DISK_ID_PART_TABLE_UUID` must be replaced with the value obtained from `udevadm info /dev/WINDOWS_DISK` (replace `WINDOWS_DISK` with the disk containing Windows partitions). The UUIDs in these rules correspond to particular [GPT partition types](https://en.wikipedia.org/wiki/GUID_Partition_Table#Partition_type_GUIDs) while the other capitalized strings are supposed to be written that way, so those does not have to be replaced.

Then the user running VirtualBox must be added to the *vboxusers* group. This can be done with the following command:

```
# usermod -aG vboxusers VIRTUALBOX_RUNNING_USER
```

Replace `VIRTUALBOX_RUNNING_USER` and with the user that will run VirtualBox, which most likely will be your user.

##### Lower security option: using 'disk' group

To be able to add the VMDK file in VirtualBox Virtual Media Manager without running VirtualBox as root, the user running VirtualBox need to be in `vboxusers` and `disk` groups.

```
# usermod -aG disk,vboxusers VIRTUALBOX_RUNNING_USER
```

Replace `VIRTUALBOX_RUNNING_USER` and with the user that will run VirtualBox, which most likely will be your user.

> **警告：**Be aware of the potential security implications of this edit, as you are giving your user account full read-write access all storage devices owned by the disk group.

#### Setting up a separate EFI system partition

Virtual machine EFI boot files will refer to different disks than the ones in the physical EFI system partition, so VirtualBox must not make use of the latter but instead of an EFI system partition inside a dedicated virtual disk. This disk can be created with the following command:

```
$ VBoxManage createmedium disk --filename VM_DIRECTORY/esp.vmdk --size 512 --format VMDK
```

Replace `VM_DIRECTORY` with the folder containing the virtual machine being built.

#### Adding virtual disks to the virtual machine

Configure the virtual machine storage devices (Settings panel - Storage) as following:

- add `esp.vmdk` as a SATA hard disk attached to the "SATA Port 0";
- add `windows.vmdk` as a SATA hard disk attached to the "SATA Port 1";
- mount Windows installation iso into the virtual optical drive .

> **注意：**
>
> - for adding a SATA hard disk use the second button on the right of the "Controller: SATA" device;
> - the virtual optical drive should already be there as "Optical Drive".

#### Configuring the virtual UEFI firmware and creating Windows boot files

Now start the virtual machine and it should automatically boot from Windows installation disk. After choosing the installation locales click on the "Repair your computer" link, then choose "Troubleshoot" and then " Command Prompt" in order to launch a command prompt from the install media.

Enter the following commands to create a new GPT table in the esp.vmdk disk and install the Windows boot loader onto it using configuration from the existing Windows partition:

Open Diskpart:

```
X:\ diskpart
```

List all disks identified by the system:

```
DISKPART> list disk
```

The `esp.vmkd` disk should be labeled as `disk 0` due to the fact that was attached to the SATA port 0, ~512 MiB in size and unpartitioned. The `windows.vmdk` disk should be labeled as `disk 1`; note that the column "Size" displays the disk size, not the partition one.

Select the esp.vmdk disk:

```
DISKPART> select Disk 0
```

Now create a GPT partition table, an EFI system partition, big as the whole disk, and assign to it a label and drive letter:

```
DISKPART> clean
DISKPART> convert gpt
DISKPART> create partition efi size=500
DISKPART> format quick fs=fat32 label="System"
DISKPART> assign letter="S"
```

Check that the partition has been correctly created:

```
DISKPART> list volume
```

Our newly created EFI system partition will be labeled as "SYSTEM" with letter as "S".

Take note of the Windows installation volume letter because it will be used in next steps. Usually its `D` but it could be different: you can infer it from its label and its size. The size is the same as the Windows installation partition size on your physical hard disk.

Exit diskpart:

```
DISKPART> exit
```

Install the Windows boot loader into the EFI system partition.

```
D:
cd Windows\System32
bcdboot D:\Windows /s S: /f UEFI
```

Now close the command prompt, power off the virtual machine and detach the Windows installation disk (from "Preferences > Devices" remove the optical disk). The virtual machine should now boot from the newly installed boot partition and load the physical Windows installation. It may show some UEFI related errors on the top of the virtual machine window and the first boot may take a while, but if everything has been done correctly you will be able to access your windows installation.

### Run an entire physical disk in VirtualBox

> **注意：**You may refer to VirtualBox official documentation [9.8.1. Using a Raw Host Hard Disk From a Guest](https://www.virtualbox.org/manual/ch09.html#rawdisk).

This works the same way as [#Run a native Windows installation inside VirtualBox](#Run_a_native_Windows_installation_inside_VirtualBox) but the vmdk will contain the entire disk rather than one partition, and so you will not need to create a separate ESP or MBR partition as the one in the physical disk will be used.

Create the raw disk:

```
# VBoxManage internalcommands createrawvmdk -filename /path/to/file.vmdk -rawdisk /dev/sdb
```

Then follow the same method as in [#Run a native Windows installation inside VirtualBox](#Run_a_native_Windows_installation_inside_VirtualBox) for the configuration and virtual disk attachement.

### Set guest starting resolution

Typically after installing Guest Additions, a fullscreen Arch guest running X will be set to the optimal resolution for your display; however, the virtual console's framebuffer will be set to a standard, often smaller, resolution detected from VirtualBox's custom VESA driver.

To use the virtual consoles at optimal resolution, Arch needs to recognize that resolution as valid, which in turn requires VirtualBox to pass this information along to the guest OS.

First, check if your desired resolution is not already recognized by running the command ([hwinfo](https://archlinux.org/packages/?name=hwinfo)包 need to be installed):

```
hwinfo --framebuffer
```

If the optimal resolution does not show up, then you will need to run the `VBoxManage` tool on the host machine and add "extra resolutions" to your virtual machine (on a Windows host, go to the VirtualBox installation directory to find `VBoxManage.exe`). For example:

```
$ VBoxManage setextradata "Arch Linux" "CustomVideoMode1" "1360x768x24"
```

The parameters "Arch Linux" and "1360x768x24" in the example above should be replaced with your virtual machine name and the desired framebuffer resolution. Incidentally, this command allows for defining up to 16 extra resolutions ("CustomVideoMode1" through "CustomVideoMode16"). Recommended resolutions are 1280x720, 1920x1080, 2048x1080, 2560x1440, 3840x2160, 1280x800, 1280x1024, 1440x900, 1600x900.

Afterwards, restart the virtual machine and run `hwinfo --framebuffer` once more to verify that the new resolutions have been recognized by your guest system (which does not guarantee they will all work, depending on your hardware limitations).

> **注意：**As of VirtualBox 5.2, `hwinfo --framebuffer` might not show any output, but you should still be able to set a custom resolution following this procedure.

Finally, add a `video=*resolution*` [kernel parameter](../../02_系统管理与内核/内核/内核参数.md) to set the framebuffer to the new resolution, for example:

```
video=1360x768
```

Additionally you may want to configure your [boot loader](../../01_安装与包管理/启动过程/Arch%20的启动流程.md) to use the same resolution. If you use GRUB, see [GRUB/Tips and tricks#Setting the framebuffer resolution](../../01_安装与包管理/启动加载程序/GRUB_技巧和窍门.md#Setting_the_framebuffer_resolution).

> **注意：**Neither the kernel parameter `vga` nor the boot loader's resolution settings (e.g. GRUB's `GRUB_GFXPAYLOAD_LINUX`) will fix the framebuffer, since they are overriden by virtue of Kernel Mode Setting. The framebuffer resolution must be set by the kernel parameter `video` as described above.

### SSH from host to guest

The network tab of the virtual machine settings contains, in "Advanced", a tool to create port forwarding. It is possible to use it to forward the Guest ssh port `22` to a Host port, e.g. `3022` :

```
user@host$ ssh -p 3022 $USER@localhost
```

will establish a connection from Host to Guest.

#### SSHFS as alternative to shared folders

Using this port forwarding and [SSHFS](../../03_网络与服务/SSH/SSHFS.md) it is straightforward to mount the Guest filesystem onto the Host one:

```
user@host$ sshfs -p 3022 $USER@localhost:$HOME ~/shared_folder
```

and then transfer files between both.

### Specify Host-Only Network

For security reasons, the VirtualBox network driver limits the usable subnet ranges. This will cause `E_ACCESSDENIED` errors when changing the virtual network adapter settings. You can change the allowed list of networks by adding them to `/etc/vbox/networks.conf`.

For example:

```
     * 10.0.0.0/8 192.168.0.0/16
     * 2001:db8:1234::/48
```

> **注意：**The spaces and asterisk are important.

For IPv6 it is best practice to generate a new, randomized [ULA prefix](https://datatracker.ietf.org/doc/html/rfc4193).

Do not forget to reload the driver and restart VirtualBox after changing `/etc/vbox/networks.conf`:

```
# modprobe -r vboxnetadp vboxnetflt
# modprobe vboxnetadp vboxnetflt
```

## 故障排除

### 鼠标键盘都锁死在虚拟机里了

这是因为你的虚拟机捕获了键盘与鼠标的输入。只要按下右 `Ctrl` 键即可让输入焦点回到宿主系统。

如果想要不按切换键就能让鼠标在宿主机与虚拟机之间无缝切换，需要在客户机中安装[客户机插件](#客户机插件)。如果你的虚拟机系统是 Arch Linux，请参考 [/在虚拟机中安装 Arch Linux#安装客户机插件](../../02_系统管理与内核/虚拟机管理/VirtualBox_在虚拟机中安装%20Arch%20Linux.md#安装客户机插件)，其他系统请参阅 VirtualBox 的官方帮助文档。

### 无法新建 64 位虚拟机

如果新建虚拟机时看不到 64 位系统选项，需先确保已在 BIOS 中启用 CPU 虚拟化技术（通常称作 `VT-x`）。

如果你的宿主系统是 Windows，那么你得禁用 Hyper-V 功能，因为 Hyper-V 会妨碍 VirtualBox 使用 VT-x。

### VirtualBox 图形管理界面和主机 GTK 主题样式不匹配

如需修改类似 VirtualBox 的 Qt 应用的外观，请参考[统一 Qt 和 GTK 应用程序的外观](../../07_开发与工具/控件工具集/统一%20Qt%20和%20GTK%20应用程序的外观.md)。

### 无法向虚拟机键入 Ctrl+Alt+Fn

如果你在虚拟机安装了某个 Linux 发行版，有时会需要按 `Ctrl+Alt+F2` 进入它的 TTY 界面，或者按 `Ctrl+Alt+Backspace` 来退出 X 会话。如果你的宿主系统也是 Linux，这些组合键默认会首先被宿主系统捕获。正确的做法是按 *Host Key*（默认是右 `Ctrl`）加 `F2` 来向虚拟机键入 `Ctrl+Alt+F2`。

### USB 功能不可用

在宿主系统里使用虚拟机的用户需被加入到 `vboxusers` 用户组。如果想要支持 USB 2 设备，还要安装[扩展包](#扩展包)。此后在虚拟机的设置里即可开启 USB 2 支持，并能通过过滤规则来允许客体系统访问指定的 USB 设备。

如果用 root 身份运行 `VBoxManage list usbhost` 命令也没有列出任何 USB 设备，需要确认一下 `/etc/udev/rules.d/` 目录里没有遗留的 VirtualBox 4.x 的 udev 规则。VirtualBox 5.0 起会把 udev 规则文件安装到 `/usr/lib/udev/rules.d/` 目录。用 `pacman -Qo /usr/lib/udev/rules.d/60-vboxdrv.rules` 命令可以查看这些 udev 文件是否已经过期。

有时某些旧 Linux 宿主系统无法自动检测到 USB 子系统，就会出现这个错误：`Could not load the Host USB Proxy service: VERR_NOT_FOUND`。或者可能让宿主机也识别不了 USB 磁盘，[哪怕用户已经加入了 **vboxusers** 用户组](https://bbs.archlinux.org/viewtopic.php?id=121377)。出现这类问题的原因是 VirtualBox 从 3.0.8 版本开始，从 *usbfs* 转向使用 *sysfs*。如果宿主系统不支持这一改动，你可以在 shell 的启动脚本（举例：如果在用 *bash* 的话，就修改 `~/.bashrc` 文件）里声明这一环境变量，让 VirtualBox 回退到旧的行为：

```
~/.bashrc
```

```
VBOX_USB=usbfs
```

然后确保这行代码对环境变量的修改生效：重新登录，手动加载该文件，启动一个新 shell 会话，或者干脆重启。

另外，还应确保用户加入到了 `storage` 用户组。

### USB 调制解调器在宿主系统不可用

如果你的客户机正在使用 USB 调制解调器，杀掉虚拟机进程可能会造成它在宿主系统里不可用。杀掉并重启余下的 `VBoxSVC` 进程应该可以解决这一问题。

### USB device crashes guest

If attaching a USB device to the guest causes a crash or any other erroneous behavior, try switching the USB controller from USB 2 (EHCI) to USB 3 (xHCI) or vice versa.

### 虚拟机启动时宿主系统卡死

一般来说，这类问题都是在 VirtualBox 或 Linux 内核版本更新之后出现。降级回到前一个版本就有可能解决问题。

### 模拟信号麦克风不可用

如果从模拟信号麦克风输入的音频信号在宿主机可用，但在客户机里不可用，可以试试在宿主机上安装 [PulseAudio](../../04_硬件与外设/声音/PulseAudio.md) 之类的[音频服务器](../../04_硬件与外设/音频/音频系统.md#音频服务器)。

如果装了 [PulseAudio](../../04_硬件与外设/声音/PulseAudio.md) 之后麦克风还是不能用，在菜单项 *VirtualBox > Machine > Settings > Audio* 里把 *Host Audio Driver* 的值改成 *ALSA Audio Driver* 也许有用。

### 映像转换成的 ISO 文件有问题

有些映像文件格式无法可靠地转换到 ISO 格式。例如：[ccd2iso](https://archlinux.org/packages/?name=ccd2iso)包 在转换时会忽略 .ccd 和 .sub 文件里的信息，最终产出的 ISO 镜像里的文件就可能出现损坏。

为了避免这种情况，可以使用 [CDemu](../../02_系统管理与内核/光盘/CDemu.md) 或类似软件在 Linux 客户机里挂载光盘映像。

### 仅主机网卡创建失败

确保所需的内核模块都已成功加载。详见 [#加载 VirtualBox 内核模块](#加载_VirtualBox_内核模块)。

如果还是不行，点击 *文件 > 主机网络管理器*，点击*创建*进行手动创建。

### 插入模块失败

如果你在加载模块时遇到如下错误信息：

```
Failed to insert 'vboxdrv': Required key not available
```

将[模块签名](#模块签名)，或者在内核配置中禁用 `CONFIG_MODULE_SIG_FORCE`。

### VBOX_E_INVALID_OBJECT_STATE (0x80BB0007)

如果虚拟机没有正常退出，就可能出现这个错误。试试下面这个命令：

```
$ VBoxManage controlvm virtual_machine_name poweroff
```

### NS_ERROR_FAILURE 且菜单项缺失

在没有更新[扩展包](#扩展包)，导致其与新版 VirtualBox 不兼容时就会出现该报错。

如果在创建虚拟机时选择了 *QCOW*/*QCOW2*/*QED* 虚拟盘格式，有时也可能会出现这个错误。

如果初次启动虚拟机时遇到下面的错误消息：

```
Failed to open a session for the virtual machine debian.
Could not open the medium '/home/.../VirtualBox VMs/debian/debian.qcow'.
QCow: Reading the L1 table for image '/home/.../VirtualBox VMs/debian/debian.qcow' failed (VERR_EOF).
VD: error VERR_EOF opening image file '/home/.../VirtualBox VMs/debian/debian.qcow' (VERR_EOF).

Result Code:
NS_ERROR_FAILURE (0x80004005)
Component:
Medium
```

退出 VirtualBox，把新虚拟机的文件都删除，并且在 VirtualBox 的配置文件中找到 `MachineRegistry` 元素，从这里删除你所创建的格式错误的虚拟机（一般是其最后一个子元素）：

```
~/.config/VirtualBox/VirtualBox.xml
```

```
...
<MachineRegistry>
  <MachineEntry uuid="{00000000-0000-0000-0000-000000000000}" src="/home/void/VirtualBox VMs/debian/debian.vbox"/>
  <MachineEntry uuid="{00000000-0000-0000-0000-000000000000}" src="/home/void/VirtualBox VMs/ubuntu/ubuntu.vbox"/>
  <MachineEntry uuid="{00000000-0000-0000-0000-000000000000}" src="/home/void/VirtualBox VMs/lastvmcausingproblems/lastvmcausingproblems.qcow"/>
</MachineRegistry>
...
```

### 缺少硬件虚拟化导致 OpenBSD 不稳定

据称在其他的虚拟机平台上，OpenBSD 可以不依赖虚拟化指令集（VT-X，AMD-V）正常运行，VirtualBox 上的 OpenBSD 则不然。具体现象是出现大量的分段错误（segfault）。在启动虚拟机时加上 *\-norawr0* 参数[可能有效](https://www.virtualbox.org/ticket/3947)，例如：

```
$ VBoxSDL -norawr0 -vm name_of_OpenBSD_virtual_machine
```

### Windows: "The specified path does not exist. Check the path and then try again."

当在 Windows 客户机里运行位于共享目录里 *.exe* 程序，而这一程序又需要管理员权限时，就可能出现这一错误信息。

一个解决办法是把文件复制到虚拟硬盘上再运行。或者直接从 [UNC 路径](https://en.wikipedia.org/wiki/Uniform_Naming_Convention) (`\\vboxsvr`) 运行程序。详见 。

### Windows 8.x 出现错误代码 0x000000C4

如果你在创建虚拟机时选择了 Win 8 系统，但在启动时还是遇到这一错误，可以尝试启用 `CMPXCHG16B` CPU 指令集。命令如下：

```
$ vboxmanage setextradata virtual_machine_name VBoxInternal/CPUM/CMPXCHG16B 1
```

### Windows 8, 8.1 或 10 无法安装、启动或报错 "ERR_DISK_FULL"

修改虚拟机设置，在菜单项 *Settings > Storage > Controller:SATA* 里勾选 "Use Host I/O Cache"。

### WinXP: 颜色深度不得多于 16 位

如果只显示 16 位色，桌面图标看起来会模糊 / 结块。但是将色深调高时，系统可能会将桌面分辨率限制得较低，或者根本不允许改变色深。解决办法是在 Windows XP 虚拟机里运行 `regedit`，并写入这一项注册表值：

If you are running at 16-bit color depth, then the icons may appear fuzzy/choppy. However, upon attempting to change the color depth to a higher level, the system may restrict you to a lower resolution or simply not enable you to change the depth at all. To fix this, run `regedit` in Windows and add the following key to the Windows XP virtual machine's registry:

```
[HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services]
"ColorDepth"=dword:00000004
```

然后再去“桌面属性”窗口里修改色深。如果还是不见效，强制让屏幕重绘（比如按 `Host+f` 进入全屏模式）试试。

### Windows: 开启 3D 加速后屏幕闪烁

VirtualBox > 4.3.14 存在问题，会导致启用 3D 加速的 Windows 虚拟机出现闪烁。r120678 版本引入了一个补丁，可以参考如下通过[环境变量](../../02_系统管理与内核/系统管理/环境变量.md)添加设置：

```
$ CR_RENDER_FORCE_PRESENT_MAIN_THREAD=0 VirtualBox
```

请确保没有 VirtualBox 服务还在运行，并参考[VirtualBox bug 13653](https://www.virtualbox.org/ticket/13653)。

### 无法在 Wayland 上启动 VirtualBox：段错误

该问题是由于 Qt 检测到了 Wayland（例如 `XDG_SESSION_TYPE=wayland`），而 VirtualBox 暂不支持 Wayland。请参考 [FS#58761](https://bugs.archlinux.org/task/58761) 和 [上游 bug 工单](https://www.virtualbox.org/ticket/18679)。

可以通过设置 `QT_QPA_PLATFORM=xcb` [环境变量](../../02_系统管理与内核/系统管理/环境变量.md) 来禁用 Qt 平台检测，并强制使用 X11。

为避免影响其它在 Wayland 中可以正常使用的 Qt 应用，应只在启动 VirtualBox 时配置 `QT_QPA_PLATFORM=xcb` 环境变量。

如果 VirtualBox 是通过[桌面项](../../01_安装与包管理/配置文件/桌面项.md)启动的，请参考[桌面项#编辑环境变量](../../01_安装与包管理/配置文件/桌面项.md#编辑环境变量)将 `Exec=VirtualBox ...` 开头的一行修改为 `Exec=env QT_QPA_PLATFORM=xcb VirtualBox ...`。如果是从 shell 命令行启动的，可以将 `virtualbox` 别名（[Bash#别名](../../07_开发与工具/命令行解释器/Bash.md#别名)）设为 `env QT_QPA_PLATFORM=xcb virtualbox`。

> **注意：**如果你在 Wayland 下有鼠标和键盘相关的问题，也可以试试上述配置。

### Random freezing in guests with Intel GPU

With Intel CPU and graphics, allocating more processors for the guest can lower render performance, thus cause random freezing. Allocating less processors can help.

### Unable to view desktop in fullscreen mode

Disable the Mini Toolbar by selecting *Machine > Settings*, select the *User Interface* tab and uncheck the *Mini Toolbar* checkbox

### Random crashes with Windows 10 guest operating system with Intel Tiger Lake chipset

Disable split lock detection by adding `split_lock_detect=off` to the [kernel parameter](../../02_系统管理与内核/内核/内核参数.md)s.

Details are described in VirtualBox's [Ticket #20180](https://www.virtualbox.org/ticket/20180#comment:8).

### Failed to save the settings when enabling Secure Boot

In VirtualBox 7.0.0, enabling Secure Boot in a virtual machine that was created in a previous VirtualBox version will fail with a nondescript error ([FS#76234](https://bugs.archlinux.org/task/76234)):

```
Failed to save the settings.
```

The solution is to click the *Reset Keys to Default* button right below the *Enable Secure Boot* checkbox.

### Failed to start VirtualBox machine after using Android Studio emulator

KVM and VirtualBox kernel modules can be loaded but not used simultaneously. Android Studio emulator is a QEMU emulator, which uses KVM for acceleration. So Android Studio emulator and VirtualBox machine (if hardware acceleration is enabled) cannot run at the same time. We have to use one after the other stopped completely.

Sometimes, VirtualBox kernel module can still be used unexpectedly by some process, and keep all VirtualBox machines failing to start, the error message on VirtualBox GUI is "A critical error has occurred".

At this time, we can check and reload VirtualBox kernel modules using `vboxreload` as root. If it saying some modules is still be in use, you need to manually kill related process and rerun the command.

### 3D Acceleration is not working

- Make sure guest additions are installed on guest, and the host modules are installed on the host
- Make sure the guest additions and host kernel modules versions match
    - host: `modinfo vboxdrv | grep '^version:'`
    - guest: open logs of your VM, find "Guest Additions information report"
- Make sure vulkan is installed and [working](../../07_开发与工具/开发/Vulkan.md#Verification) on the host

### VirtualBox UI elements are improperly rendered with Kvantum installed

On some configurations of Kvantum ([kvantum](https://archlinux.org/packages/?name=kvantum)包) with third party themes, some UI elements such as toolbars and menus are rendered black or improperly. This seems to be limited to translucent windows being enabled. See Kvantum's issue [#418](https://github.com/tsujan/Kvantum/issues/418).

To fix this behavior, do one of:

- Disable *Translucent windows* under section *Configure Active Theme > Compositing & General Look* of *Kvantum Manager*.
- Add `VirtualBox,VirtualBoxVM` in *Kvantum Manager*, to the *Configure Active Theme > Compositing & General Look > Opaque apps:* menu field.
    - This makes an exception for virtualbox windows to be ignored.

### VirtualBox is taking exclusive control of an audio device, preventing PipeWire from accessing it while the virtual machine is running

By default, VirtualBox should auto-select the best audio driver. However, on PipeWire systems this often falls back to ALSA (see [Pipewire issue](https://gitlab.freedesktop.org/pipewire/pipewire/-/issues/1672)).

It could cause journal records like these:

```
pipewire: spa.audioadapter: params Spa:Enum:ParamId:EnumFormat: 1:0 (convert format) Device or reso>
pipewire: pw.node: (alsa_output.pci-0000_00_1f.3-platform-skl_hda_dsp_generic.HiFi__Speaker__sink-6>
pipewire: spa.alsa: '_ucm0001.hw:sofhdadsp': playback open failed: Device or resource busy
```

The solution is to configure VirtualBox to use the PulseAudio backend (which PipeWire will handle [via pipewire-pulse](../多媒体/PipeWire.md#PulseAudio_clients)):

```
$ VBoxManage modifyvm "Your_virtual_machine_name" --audio-driver pulse --audio-controller hda
```

## 参阅

- [User Guide](https://www.virtualbox.org/manual/UserManual.html) on a single HTML page
