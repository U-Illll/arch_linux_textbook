# 第 5 章　TLS

> TLS 在 Arch 上不是一个库，而是几个并存的实现，本章要解决的是分清各自负责什么、该装哪一个，以及证书与配置放哪里。《OpenSSL》一节的体量超过其余三节之和，安装、配置、用法与排错俱全；《Network Security Services》次之；《GnuTLS》与《Mbed TLS》各只有安装与基本用法。
> 
> 建议先读《OpenSSL》，四节里只有它需要单独配置，把配置位置和排错手法拿下，必读；《Network Security Services》次之，需要时再精读；《GnuTLS》《Mbed TLS》查到即可，手上没有对应软件栈就跳过。最容易踩的坑是把这四者当成可以互相顶替的替代品，以为装哪个都行：它们并存于同一台机器，装上一个并不等于要用它的软件就用上了它，也不能把《OpenSSL》的配置与排错当成另外三节也有的内容。

<!-- 本章 4 节，共 18 KB -->

## 5.1　Mbed TLS

*难度：入门 ｜ 篇幅：1.8 KB ｜ 所属：TLS*

将要讲到：安装、用法、参见

根据 [Wikipedia](https://en.wikipedia.org/wiki/mbed_TLS):

: **mbed TLS**（以前的 **PolarSSL**）是 TLS 和 SSL 协议以及相应的加密算法和所需支持代码的实现。它具有 Apache License 版本 2.0 和 GPLv2 的双重许可。网站上的声明是 mbed TLS 旨在“易于理解，使用，集成和扩展”。

## 安装

安装 [mbedtls](https://archlinux.org/packages/?name=mbedtls)包 软件包。

## 用法

命令名称以“mbedtls_”开头，关于用法示例，请参见[知识库](https://tls.mbed.org/kb)。

### 生成 RSA 私钥

```
$ mbedtls_gen_key rsa_keysize=keysize filename=filename
```

### 生成证书签名请求

```
$ mbedtls_cert_req filename=private_key subject_name=subject output_file=filename
```

[相关方法](https://tls.mbed.org/kb/how-to/generate-a-certificate-request-csr)

### 生成自签名证书

```
$ mbedtls_cert_write selfsign=1 issuer_key=private_key issuer_name=subject not_before=YYYYMMDDHHMMSS not_after=YYYYMMDDHHMMSS is_ca=1 max_pathlen=0 output_file=file
```

[相关方法](https://tls.mbed.org/kb/how-to/generate-a-self-signed-certificate)

## 参见

- [官方网站](https://tls.mbed.org/)
- [API 文档](https://tls.mbed.org/api/)

## 5.2　GnuTLS

*难度：入门 ｜ 篇幅：2.0 KB ｜ 所属：TLS*

将要讲到：安装、用法、另请参见

根据 [Wikipedia](https://en.wikipedia.org/wiki/GnuTLS):

: **GnuTLS** (the **GNU Transport Layer Security Library**) is a free software implementation of the TLS, SSL and DTLS protocols. It offers an application programming interface (API) for applications to enable secure communication over the network transport layer, as well as interfaces to access X.509, PKCS #12, OpenPGP and other structures.

## 安装

安装 [gnutls](https://archlinux.org/packages/?name=gnutls)包 包。

要与 [Apache HTTP Server](../../03_网络与服务/网络服务器/Apache%20HTTP%20服务器.md) 集成，请安装 mod_gnutls。

## 用法

有关以下各节中使用的命令，请参见 [certtool(1)](https://man.archlinux.org/man/certtool.1)；有关 API 文档，请参见[信息](https://www.gnutls.org/manual/html_node/index.html)文档。

### 生成 RSA 私钥

```
$ certtool -p --rsa --bits=keysize
```

### 生成证书签名请求

```
$ certtool -q --load-privkey private_key --outfile file
```

### 生成自签名证书

```
$ certtool -s --load-privkey private_key --outfile file
```

## 另请参见

- [官方网站](https://www.gnutls.org/)

## 5.3　Network Security Services

*难度：入门 ｜ 篇幅：4.4 KB ｜ 所属：TLS*

将要讲到：安装、用法、参见

[Network Security Services](https://en.wikipedia.org/wiki/Network_Security_Services)(*NSS*)是一组旨在支持跨平台开发具有安全功能的客户端和服务器应用程序的库。

使用NSS构建的应用程序可以支持[SSL](https://en.wikipedia.org/wiki/SSL) v2和v3、TLS、[PKCS #5](https://en.wikipedia.org/wiki/PKCS_5)、[PKCS #7](https://en.wikipedia.org/wiki/PKCS_7)、[PKCS #11](https://en.wikipedia.org/wiki/PKCS_11)、[PKCS #12](https://en.wikipedia.org/wiki/PKCS_12)、[S/MIME](https://en.wikipedia.org/wiki/S/MIME)、[X.509](https://en.wikipedia.org/wiki/X.509) v3证书以及其他安全标准。

许多软件包都需要NSS，包括，例如，[Chromium](../../06_软件与应用/Google/Chromium.md)和[Firefox](../../06_软件与应用/Mozilla/Firefox.md)。

## 安装

安装 [nss](https://archlinux.org/packages/?name=nss)包包。

## 用法

使用NSS提供的*certutil*工具来管理你的证书。

### 列出证书数据库

要获得所有证书的列表。

```
$ certutil -d sql:$HOME/.pki/nssdb -L
```

要获得证书的详细信息。

```
$ certutil -d sql:$HOME/.pki/nssdb -L -n certificate_nickname
```

### 生成一个RSA私钥

```
$ certutil -G -d database_directory -g keysize -n nickname
```

### 生成一个证书签名请求

```
$ certutil -S -s subject -n nickname -x -t C,C,C -o file
```

### 生成一个自签名的证书

```
$ certutil -S -s subject -n nickname -x -t C,C,C -o file
```

### 导入证书

要添加一个证书，请指定`-A`选项。

```
$ certutil -d sql:$HOME/.pki/nssdb -A -t "'TRUSTARGS'" -n certificate_nickname -i /path/to/cert/filename
```

`TRUSTARGS`是由零个或多个字母组成的三个字符串，用逗号分隔，例如，`"TCu,Cu,Tuw"`。它们定义了证书在SSL、电子邮件和对象签名中应如何被信任，在[certutil documentation](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/Tools/certutil)或[Meena's blog post](https://meenavyas.medium.com/about-trust-flags-of-certificates-in-nss-database-that-can-be-modified-by-certutil-67e4f33a6d0f)中对信任标志进行了解释。

要为SSL客户认证添加个人证书和私钥，请使用以下命令。

```
$ pk12util -d sql:$HOME/.pki/nssdb -i /path/to/PKCS12/cert/filename.p12
```

这将导入一个存储在PKCS #12文件中的个人证书和私钥。个人证书的`TRUSTARGS`将被设置为`"u,u,u"`。

### 编辑证书

使用`-M`选项调用*certutil*来编辑证书。例如，要编辑`TRUSTARGS`。

```
$ certutil -d sql:$HOME/.pki/nssdb -M -t "'TRUSTARGS'" -n certificate_nickname
```

### 删除证书

使用`-D`选项来删除证书。

```
$ certutil -d sql:$HOME/.pki/nssdb -D -n certificate_nickname
```

### 添加一个受信任的CA证书

[chromium](https://archlinux.org/packages/?name=chromium)包、[firefox](https://archlinux.org/packages/?name=firefox)包、[thunderbird](https://archlinux.org/packages/?name=thunderbird)包、[evolution](https://archlinux.org/packages/?name=evolution)包和[seamonkey](https://archlinux.org/packages/?name=seamonkey)包使用NSS检索可信的CA证书。

[nss](https://archlinux.org/packages/?name=nss)包已经集成[p11-kit](https://archlinux.org/packages/?name=p11-kit)包，它将使用system-wide自动安装所有证书。但如果你更喜欢分发 "纯净" NSS，可以将证书安装到你自己的浏览器配置文件中。

```
certutil -d database -A -i myCA.cert -n "Honest Achmed's CA" -t C, 。
```

[chromium](https://archlinux.org/packages/?name=chromium)包和[evolution](https://archlinux.org/packages/?name=evolution)包使用一个位于`-d "sql:$HOME/.pki/nssdb"`的 "共享式" 数据库。

对于[firefox](https://archlinux.org/packages/?name=firefox)包、[thunderbird](https://archlinux.org/packages/?name=thunderbird)包和[seamonkey](https://archlinux.org/packages/?name=seamonkey)包，要制定浏览器专用的配置文件目录（例如`-d ~/.mozilla/firefox/ov6jazas.default`）。

## 参见

- [网络安全服务 - Mozilla](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS)
- [使用证书数据库工具--Mozilla](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/tools/NSS_Tools_certutil#Using_the_Certificate_Database_Tool)
- [管理NSS数据库中的证书信任标志 - Meena Vyas, Oracle](https://blogs.oracle.com/meena/about-trust-flags-of-certificates-in-nss-database-that-can-be-modified-by-certutil)

## 5.4　OpenSSL

*难度：进阶 ｜ 篇幅：10.1 KB ｜ 所属：TLS*

将要讲到：安装、配置、用法、Troubleshooting、See also

> **翻译状态：**
>
> - 本文（或部分内容）译自 [OpenSSL](https://wiki.archlinux.org/title/OpenSSL)，最近一次同步于 2023-06-01，若英文版本有所[更改](https://wiki.archlinux.org/title/OpenSSL?diff=0&oldid=780045)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/OpenSSL_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** 请完成翻译。大部分内容尚未翻译。（在 [Talk:OpenSSL#](https://wiki.archlinuxcn.org/wiki/Talk:OpenSSL) 中讨论）

[OpenSSL](https://www.openssl.org)是 SSL 和 TLS 协议的开源实现，旨在尽可能灵活。OpenSSL 支持各种平台，包括 BSD、Linux、OpenVMS、Solaris 和 Windows。

> **警告：**2015 年 5 月发布的对 OpenSSL 协议使用情况的合作研究显示 SSL 连接存在重大风险 “Logjam”。可在 [https://weakdh.org/](https://weakdh.org/) 查看结果，在 [https://weakdh.org/sysadmin.html](https://weakdh.org/sysadmin.html) 查看建议的服务端配置。

## 安装

Arch Linux 默认安装 [openssl](https://archlinux.org/packages/?name=openssl)包（作为 [coreutils](https://archlinux.org/packages/?name=coreutils)包 的依赖）。

有许多 OpenSSL 库绑定可供开发者使用：

- [python-pyopenssl](https://archlinux.org/packages/?name=python-pyopenssl)包
- [perl-net-ssleay](https://archlinux.org/packages/?name=perl-net-ssleay)包
- [lua-sec](https://archlinux.org/packages/?name=lua-sec)包、[lua52-sec](https://archlinux.org/packages/?name=lua52-sec)包 和 [lua51-sec](https://archlinux.org/packages/?name=lua51-sec)包
- [haskell-hsopenssl](https://archlinux.org/packages/?name=haskell-hsopenssl)包
- [haskell-openssl-streams](https://archlinux.org/packages/?name=haskell-openssl-streams)包

## 配置

在 Arch Linux 中 `OPENSSLDIR` 为 `/etc/ssl`。

OpenSSL 配置文件通常位于 `/etc/ssl/openssl.cnf`，乍一看可能很复杂。注意在赋值中可以展开变量，这与 Shell 脚本的工作方式很相似。配置文件格式的详细解释，请见 [config(5ssl)](https://man.archlinux.org/man/config.5ssl)。

### req 部分

> [![](https://wiki.archlinuxcn.org/wzh/images/c/c9/Merge-arrows-2.png)](https://wiki.archlinuxcn.org/wzh/File:Merge-arrows-2.png)**本文或本章节可能需要合并到[#生成证书签名请求](#生成证书签名请求)。**
>
> **附注：** 主题相同。（在 \[\[[en:Talk:OpenSSL#Plan](https://wiki.archlinux.org/title/Talk:OpenSSL#Plan)\]\] 中讨论）

与生成密钥、请求和自签名证书有关的设置。

The req section is responsible for the DN prompts. A general misconception is the *Common Name* (CN) prompt, which suggests that it should have the user's proper name as a value. End-user certificates need to have the **machine hostname** as CN, whereas CA should *not* have a valid TLD, so that there is no chance that, between the possible combinations of certified end-users' CN and the CA certificate's, there is a match that could be misinterpreted by some software as meaning that the end-user certificate is self-signed. 某些 CA 证书甚至没有 CN，例如 [Equifax](https://www.equifax.com)：

```
$ openssl x509 -subject -noout < /etc/ssl/certs/Equifax_Secure_CA.pem
```

```
subject= /C=US/O=Equifax/OU=Equifax Secure Certificate Authority
```

## 用法

请先阅读 Transport Layer Security#Obtaining a certificate。

### 生成 Curve25519 私钥

```
$ openssl genpkey -algorithm x25519 -out 文件名
```

### 生成 ECDSA 私钥

```
$ openssl genpkey -algorithm EC -pkeyopt ec_paramgen_curve:P-256 -out 文件名
```

### 生成 RSA 私钥

使用（根据 [openssl(1ssl)](https://man.archlinux.org/man/openssl.1ssl)，代替 *genrsa* 的）[genpkey(1ssl)](https://man.archlinux.org/man/genpkey.1ssl)：

```
$ openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:私钥大小 -out 文件名
```

如果需要加密密钥，使用 `-aes-256-cbc` 选项。

### 生成证书签名请求

使用 [req(1ssl)](https://man.archlinux.org/man/req.1ssl)：

```
$ openssl req -new -sha256 -key 私钥 -out 文件名
```

### 显示证书签名请求

证书签名请求以编码形式存储。要以人类可读的形式查看请求：

```
$ openssl req -noout -text -in 文件名
```

### 生成自签名证书


```
$ openssl req -key private_key -x509 -new -days days -out filename
```

### Generate a self-signed certificate with private key in a single command

You can combine the above command in OpenSSL into a single command which might be convenient in some cases:

```
$ openssl req -x509 -newkey rsa:4096 -days days -keyout key_filename -out cert_filename
```

### Generate Diffie–Hellman parameters

See [Diffie–Hellman key exchange](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) for more information.

Current [best practice](https://wiki.mozilla.org/Security/Server_Side_TLS) is to use one of the standard DH groups from [RFC:7919](https://tools.ietf.org/html/rfc7919), eg. [ffdhe2048](https://ssl-config.mozilla.org/ffdhe2048.txt).

Alternatively you can generate a random group of your own:

```
$ openssl dhparam -out filename 2048
```

> **提示：**To speed up generating, especially when not on high-end hardware, add the `-dsaparam` option .

### 显示证书信息

```
$ openssl x509 -text -in 证书文件名
```

### 显示证书指纹

```
$ openssl x509 -noout -in 证书文件名 -fingerprint -digest
```

`*-digest*` is optional and one of `-md5`, `-sha1`, `-sha256`, or `-sha512`. See "-digest" in [x509(1ssl) § Input, Output, and General Purpose Options](https://man.archlinux.org/man/x509.1ssl#Input,_Output,_and_General_Purpose_Options) for when the digest is unspecified.

### 转换证书格式

Use `openssl x509` to convert certificates from binary (DER) format to PEM format (the text format with `BEGIN CERTIFICATE` headers)：

```
$ openssl x509 -inform DER < myCA.crt > myCA_pem.crt
```

### Use third-party providers

OpenSSL 3 introduced providers as a new concept for OpenSSL plugability. It is possible to use algorithms not included in OpenSSL without having to recompile it. For example, to test the [NIST Post-Quantum Cryptography](https://csrc.nist.gov/projects/post-quantum-cryptography) algorithms, you can install the [Open Quantum Safe](https://openquantumsafe.org/) provider [oqsprovider](https://aur.archlinux.org/packages/oqsprovider/)AUR. As an example, you can generate a quantum-safe self-signed certificate with private key using one of the variants of the [Dilithium](https://pq-crystals.org/dilithium/index.shtml) signature algorithm:

```
$ openssl req -provider oqsprovider -x509 -newkey dilithium3 -days days -keyout key -out cert
```

## Troubleshooting

### "bad decrypt" while decrypting

OpenSSL 1.1.0 changed the default digest algorithm for the dgst and enc commands from MD5 to SHA256. 

Therefore if a file has been encrypted using OpenSSL 1.0.2 or older, trying to decrypt it with an up to date version may result in an error like:

```
error:06065064:digital envelope routines:EVP_DecryptFinal_ex:bad decrypt:crypto/evp/evp_enc.c:540
```

Supplying the `-md md5` option should solve the issue:

```
$ openssl enc -d -md md5 -in encrypted -out decrypted
```

### Python 3.10 and "ca md too weak" errors

In Python 3.10 by default there is a hardcoded list of allowed OpenSSL ciphers. Some of the less secure, like MD5, have been disabled at the `ssl` module level, ignoring the system-wide configuration of OpenSSL. It results sometimes in strange errors on older certificates, sometimes even when establishing `https` connections, like:

```
requests.exceptions.SSLError: HTTPSConnectionPool(host='a.kind.of.example.com', port=443): Max retries exceeded with url: / (Caused by SSLError(SSLError(398, '[SSL: CA_MD_TOO_WEAK] ca md too weak (_ssl.c:3862)')))
```

To make Python follow the system configuration, you may have to rebuild it, adding `--with-ssl-default-suites=openssl` parameter to `./configure`. The issue has been also reported as [FS#73549](https://bugs.archlinux.org/task/73549).

## See also

- [维基百科的 OpenSSL 页面](https://zh.wikipedia.org/wiki/OpenSSL)，包含背景信息。
- [OpenSSL](https://www.openssl.org) 项目页面。
- [FreeBSD Handbook](https://www.freebsd.org/doc/en/books/handbook/openssl.html)
- [Step-by-step guide to create a signed SSL certificate](https://www.akadia.com/services/ssh_test_certificate.html)
- [OpenSSL Certificate Authority](https://jamielinux.com/docs/openssl-certificate-authority/): A guide demonstrating how to act as your own certificate authority.
- [Bulletproof SSL and TLS](https://www.feistyduck.com/books/bulletproof-ssl-and-tls/bulletproof-ssl-and-tls-introduction.pdf) by Ivan Ristić, a more formal introduction to SSL/TLS
