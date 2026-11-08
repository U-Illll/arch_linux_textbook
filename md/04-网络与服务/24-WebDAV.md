# 第 24 章　WebDAV

> WebDAV 把远端目录当成文件系统来读写，但服务端、认证、客户端三处任一环节没通都用不成。建议先读《WebDAV》，它篇幅最长，服务器、客户端、认证与排错都在这里，是必读的主干；再读《Davfs2》，讲把 WebDAV 资源挂载到本地，并附提示与常见问题，日常最实用，也建议读；《AgenDAV》只讲安装，最短，可以先跳，需要时再翻。
> 
> 最容易踩的坑是把挂载成功当成配置完成：davfs2 只是客户端，认证或权限不通时，挂载会失败或只落到只读，此时该查的是服务端那一侧，而不是继续折腾挂载参数。

<!-- 本章 3 节，共 19 KB -->

## 24.1　AgenDAV

*难度：入门 ｜ 篇幅：1.8 KB ｜ 所属：WebDAV*

将要讲到：安装

[AgenDAV](https://agendav.org/) 是一个用 PHP 编写的开源多语言 CalDAV Web 应用程序，具有丰富的 AJAX 界面和共享日历支持。

## 安装

安装 [agendav](https://aur.archlinux.org/packages/agendav/)AUR 包。

### 数据库

您必须向 AgenDAV 提供一个 SQL 数据库。 这是一个 PostgreSQL 示例。

根据 [PostgreSQL](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md) 文章安装 PostgreSQL。创建一个 `agendav` 用户和数据库：

```
# createuser agendav
# createdb -O agendav agendav
```

### 配置

设置数据库后，必须手动填充它：

```
# psql -U agendav agendav < /usr/share/webapps/agendav/sql/pgsql.schema.sql
# bash /usr/share/webapps/agendav/bin/agendavcli dbupdate
```

确保在 `php.ini` 中启用 `extension=pgsql`（或使用的任何数据库）和 `extension=iconv` 扩展。

根据您的喜好编辑配置文件 `/etc/webapps/agendav/{config,caldav,database}.php`。

通过 apache：`/etc/webapps/agendav/apache.example.conf`，nginx/php-fpm：`/etc/webapps/agendav/nginx.example.conf` 或其他一些 Web 服务器来服务该应用程序。

## 24.2　Davfs2

*难度：进阶 ｜ 篇幅：6.1 KB ｜ 所属：WebDAV*

将要讲到：安装 davfs2、挂载 WebDAV 资源、提示与技巧、常见问题、参见

> **翻译状态：**
>
> - 本文（或部分内容）译自 [Davfs2](https://wiki.archlinux.org/title/Davfs2)，最近一次同步于 2026-03-09，若英文版本有所[更改](https://wiki.archlinux.org/title/Davfs2?diff=0&oldid=868468)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/Davfs2_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

[davfs2](https://savannah.nongnu.org/projects/davfs2) 是一个用于[挂载](../../02_系统管理与内核/文件系统/文件系统.md) [WebDAV](WebDAV.md) 资源的 Linux 文件系统。WebDAV 是 HTTP/1.1 的一个扩展，用于远程协作共享网络文件资源。

## 安装 davfs2

安装软件包 [davfs2](https://archlinux.org/packages/?name=davfs2)包。

## 挂载 WebDAV 资源

### 配置和挂载参数

**配置文件**分为系统级的 `/etc/davfs2/davfs2.conf` 和用户级的 `~/.davfs2/davfs2.conf`。普通用户调用时，会同时读取两个配置文件，但用户配置的优先级高于系统配置。可以对常规、WebDAV 相关、缓存相关、调试等内容进行配置，具体语法请参见 [davfs2.conf(5)](https://man.archlinux.org/man/davfs2.conf.5)。

**挂载参数**可以用于定义配置文件路径、文件系统的所有者和用户组，以及其他访问权限相关的选项。要查询所有可用的参数，执行以下命令：

```
$ mount.davfs -h
```

也可参见 [mount.davfs(8)](https://man.archlinux.org/man/mount.davfs.8) 了解详细。

### 使用命令行

要挂载 WebDAV 资源，请使用 `mount`，而非直接使用 `mount.davfs`。

```
# mount -t davfs http(s)://address:<port>/path /mount/point
```

> **注意：**只有在系统级配置文件中定义的 `dav_group` 用户组内的普通成员才能挂载 davfs2 文件系统。确保系统级配置文件中的 `dav_group` 已经被启用并被指定了可用的用户组。

### 使用 systemd

要使用[Systemd 挂载](../../01_安装与包管理/初始化/Systemd.md#systemd.mount_-_挂载)：

```
/etc/systemd/system/mnt-webdav-service.mount
```

```
[Unit]
Description=Mount WebDAV Service
After=network-online.target
Wants=network-online.target

[Mount]
What=http(s)://address:<port>/path
Where=/mnt/webdav/service
Options=uid=1000,file_mode=0664,dir_mode=2775,grpid
Type=davfs
TimeoutSec=15

[Install]
WantedBy=multi-user.target
```

也可以创建一个 systemd 自动挂载单元来设置超时时间。

```
/etc/systemd/system/mnt-webdav-service.automount
```

```
[Unit]
Description=Mount WebDAV Service

[Automount]
Where=/mnt/webdav/service
TimeoutIdleSec=300

[Install]
WantedBy=remote-fs.target
```

要了解更多关于 systemd 挂载单元的内容，参见 [Fstab#通过_systemd_自动挂载](../../01_安装与包管理/启动过程/Fstab.md#通过_systemd_自动挂载)。

### 使用 fstab

要定义如何将 WebDAV 资源挂载在文件系统上，请添加一个以下格式的 [fstab](../../01_安装与包管理/启动过程/Fstab.md) 条目：

```
/etc/fstab
```

```
https://webdav.example/path /mnt/webdav davfs rw,user,uid=username,noauto 0 0
```

其中，*username* 是所挂载的文件系统的所有者，可以是数字 ID 或是用户名。仅有 *root* 用户能以与挂载用户不同的 UID 进行挂载。挂载参数 `_netdev` 可以用于自动挂载网络驱动器。

## 提示与技巧

### 保存认证信息

创建一个密钥文件以保存 WebDAV 服务的认证信息。对于普通用户，使用 `~/.davfs2/secrets`；对于 *root* 用户，使用 `/etc/davfs2/secrets`：

```
/etc/davfs2/secrets
```

```
https://webdav.example/path davusername davpassword
```

请确保密钥文件的[权限](../../07_开发与工具/命令行/文件权限与属性.md)正确。对于 *root* 用户：

```
# chmod 600 /etc/davfs2/secrets
# chown root:root /etc/davfs2/secrets
```

对于普通用户：

```
$ chmod 600 ~/.davfs2/secrets
```

## 常见问题

### 无法创建/复制文件，同时可能卡住

如果无法创建/复制文件，同时可能卡住，请编辑[#配置和挂载参数](#配置和挂载参数)，将 `use_locks 0` 加入到配置中。默认配置 `1` 会在打开和写入文件时在服务器上加锁。

### 密码和密钥文件

如果密码中含有如 `\` 或 `"` 这样的特殊字符，请为其添加转义符 `\`。

### 网络已连通但域名解析失败

有时 `network-online.target` 已经成功但 WebDAV 服务器的域名仍未被解析。可以手动等待域名解析成功。

首先，配置好[#使用 fstab](#使用_fstab)，然后在 15 秒重复使用 ping 测试是否能访问服务器。访问成功时，再进行挂载：

```
/etc/systemd/system/mnt-webdav-service.service
```

```
[Unit]
Description=Mount WebDAV Service
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
ExecStart=bash -c 'for i in {1..15}; do if ping -c 1 mywebdav.server.url; then mount /path/to/mountpoint; break; else sleep 1; fi; done'
ExecStop=umount /path/to/mountpoint
RemainAfterExit=true

[Install]
WantedBy=default.target
```

建议将其配置为[用户单元](../../02_系统管理与内核/系统管理/Systemd_用户.md)。

### 挂载 WebDAV 资源时报错 'different mount options in /etc/fstab'

可能是以下原因导致：

1.  WebDAV 资源及其挂载点已经在 /etc/fstab 中定义
2.  挂载点已经被作为一个参数传递给 'mount' 命令（即使挂载点与 /etc/fstab 中相同）

因此，请不要在挂载时传递挂载点，而是自动使用 /etc/fstab 中的值。例如：

```
$ mount -t davfs https://mywebdav mymountpoint # 错误
与 /etc/fstab 中的挂载参数不同
$ mount -t davfs https://mywebdav # 正确
```

## 参见

- [https://ajclarkson.co.uk/blog/auto-mount-webdav-raspberry-pi/](https://ajclarkson.co.uk/blog/auto-mount-webdav-raspberry-pi/)\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2025-03-15 ⓘ\]

## 24.3　WebDAV

*难度：进阶 ｜ 篇幅：10.8 KB ｜ 所属：WebDAV*

将要讲到：服务器、Client、Authentication、Troubleshooting

[WebDAV](https://en.wikipedia.org/wiki/WebDAV) （*Web Distributed Authoring and Versioning*）是 HTTP/1.1 的扩展，因此可以作为一个协议考虑。它包含了一系列概念和由此产生的扩展方法，用以允许通过 HTTP/1.1 读写。WebDAV 不使用 [NFS](../网络共享/NFS.md) 或 [SMB](../网络共享/Samba.md)，而是通过HTTP传输文件。

本指南目的是通过 [web 服务器](../网络应用程序/应用程序列表_互联网.md#网络服务器)配置简单的WebDAV。

## 服务器

### Apache

下载 [Apache HTTP 服务器](../网络服务器/Apache%20HTTP%20服务器.md)。

取消 DAV 和 auth_digest 这两个模块的注释：

```
LoadModule dav_module modules/mod_dav.so
LoadModule dav_fs_module modules/mod_dav_fs.so
LoadModule dav_lock_module modules/mod_dav_lock.so
LoadModule auth_digest_module modules/mod_auth_digest.so
```

取消注释 `conf/extra/httpd-dav.conf` 文件中的行：

```
# Distributed authoring and versioning (WebDAV)
Include conf/extra/httpd-dav.conf
```

检查文件 `/etc/httpd/conf/extra/httpd-dav.conf` 中的行：

```
DAVLockDB /etc/httpd/var/DavLock
```

确保你把上面的行放在了其它指令的外面，比如在 `DocumentRoot` 定义中：

如果你希望进行干净的部署，可以考虑使用 `/srv/dav` 结构来替代默认的 `/etc/httpd/uploads`。（与原文有出入，请对照[英文页面](https://wiki.archlinux.org/title/WebDAV)阅读）

接下来，检查 `/etc/httpd/conf/extra/httpd-dav.conf` 中别名相关的设置（同样要放在其它指令的外面）:

```

DavLockDB "/etc/httpd/var/DavLock"

Alias /uploads "/etc/httpd/uploads"

<Directory "/etc/httpd/uploads">
    Dav On

    AuthType Digest
    AuthName DAV-upload
    # You can use the htdigest program to create the password database:
    #   htdigest -c "/etc/httpd/user.passwd" DAV-upload admin
    AuthUserFile "/etc/httpd/user.passwd"
    AuthDigestProvider file

    # Allow universal read-access, but writes are restricted
    # to the admin user.
    <RequireAny>
        # require that these methods are used (PROPFIND allows directory listing) ...
        Require method GET POST OPTIONS PROPFIND
        # or that the user is admin (f.e. PUT is required to write a file, MKCOL for folders)
        Require user admin

        # -- Notes ---
        # more info on methods in the webdav rfc: http://www.webdav.org/specs/rfc4918.html
        # POST treated as PUT: https://datatracker.ietf.org/doc/html/rfc5995

    </RequireAny>
</Directory>
```

创建目录：

```
# mkdir -p /etc/httpd/var
```

检查 DavLockDB 目录的权限并确保Web服务器[用户](../../08_安全与加密/安全/用户和用户组.md) `http` 有权写入此目录：

```
# chown -R http:http /etc/httpd/var
# mkdir -p /etc/httpd/uploads
# chown -R http:http /etc/httpd/uploads
```

### nginx

安装 [nginx-mainline](https://archlinux.org/packages/?name=nginx-mainline)包 （[nginx](../网络服务器/Nginx.md)的Mainline版本）和 [nginx-mainline-mod-dav-ext](https://aur.archlinux.org/packages/nginx-mainline-mod-dav-ext/)AUR。

在 `/etc/nginx/nginx.conf` 文件的头部以及其它块的外面添加下面的行：

```
load_module /usr/lib/nginx/modules/ngx_http_dav_ext_module.so;
```

在 `server` 块中为WebDAV添加新的 `location` ，例如：

```
location /dav {
    root   /srv/http;

    dav_methods PUT DELETE MKCOL COPY MOVE;
    dav_ext_methods PROPFIND OPTIONS;

    # Adjust as desired:
    dav_access user:rw group:rw all:r;
    client_max_body_size 0;
    create_full_put_path on;
    client_body_temp_path /srv/client-temp;
    autoindex on;

    allow 192.168.178.0/24;
    deny all;
}
```

上面的例子需要 `/srv/http/dav` 目录和 `/srv/client-temp` 目录存在。

你也许希望通过使用绑定挂载来让其它目录也能通过WebDAV访问。

### rclone

安装 [rclone](https://archlinux.org/packages/?name=rclone)包 。它支持[使用 webdav](https://rclone.org/commands/rclone_serve_webdav/) 导出远程或本地目录。

在不使用任何认证的情况下提供 `/srv/http` 目录下的内容：

```
$ rclone serve webdav /srv/http
```

### Caddy

安装 [caddy-webdav-git](https://aur.archlinux.org/packages/caddy-webdav-git/)AUR 或者先安装 [xcaddy-bin](https://aur.archlinux.org/packages/xcaddy-bin/)AUR 再使用 WebDAV 模块构建 [Caddy](../网络服务器/Caddy.md) ：

```
$ xcaddy build --with github.com/mholt/caddy-webdav
```

若要使用80端口以 `dav` 路径提供 `/srv/webdav` 目录下的内容，将下面的内容添加到 [Caddyfile](../网络服务器/Caddy.md#Configuration) ：

```
:80 {
    rewrite /dav /dav/
    webdav /dav/* {
       root /srv/webdav
       prefix /dav
    }
    file_server
}
```

[运行 Caddy](../网络服务器/Caddy.md#用法) ：

```
$ caddy run
```

## Client

### Cadaver

Install the [cadaver](https://archlinux.org/packages/?name=cadaver)包 package.

After installation, test the WebDAV server:

```
$ cadaver http://localhost/dav
dav:/dav/> mkcol test
Creating `test': succeeded.
dav:/dav/> ls
Listing collection `/dav/': succeeded.
Coll: test
```

### Dolphin

To create a permanent WebDAV folder in [Dolphin](../../02_系统管理与内核/文件管理/Dolphin.md) select *Network* in the *remotes* section of the places sidebar, then press the *Add Network Folder* button. The network folder wizard will appear. Select *WebFolder (webdav)*, and fill in the subsequent form.

Alternately just click the path bar and then enter the url with *webdav://* protocol specifier.

### Nautilus

Install the [gvfs](https://archlinux.org/packages/?name=gvfs)包 and [gvfs-dnssd](https://archlinux.org/packages/?name=gvfs-dnssd)包 packages.

In [Nautilus](../../05_桌面与图形/GNOME/GNOME_文件.md) choose "connect to server" and enter the address with `dav://` or `davs://` protocol specified:

```
dav://127.0.0.1/dav
```

> **注意：**If you get a "HTTP Error: Moved permanently" with `dav://`, try to use `davs://` as the protocol instead.

### rclone

[rclone](https://archlinux.org/packages/?name=rclone)包 is a command line tool that lets you sync to/from, or [mount](https://rclone.org/commands/rclone_mount/) (with many caching options), remote file systems including WebDAV.

### Thunar

Install the [gvfs](https://archlinux.org/packages/?name=gvfs)包 and [gvfs-dnssd](https://archlinux.org/packages/?name=gvfs-dnssd)包 packages.

In [Thunar](../../05_桌面与图形/Xfce/Thunar.md) press `Ctrl+l` and enter the address with *dav* or *davs* protocol specified:

```
davs://webdav.yandex.ru
```

## Authentication

There are numerous different protocols you can use:

- plain
- digest
- others

### Apache

Using [htdigest(1)](https://man.archlinux.org/man/htdigest.1) (remove the `-c` option if the file exists):

```
# htdigest -c /etc/httpd/conf/passwd WebDAV username
```

> **注意：**Make sure digest authentication is enabled in `httpd.conf` by the presence of this entry: `LoadModule auth_digest_module modules/mod_auth_digest.so`

Using plain [htpasswd(1)](https://man.archlinux.org/man/htpasswd.1) (remove the `-c` option if the file exists):

```
# htpasswd -c /etc/httpd/conf/passwd username
```

Next, `httpd.conf` must be edited to enable authentication. One method would be to require the user `foo` for everything:

```
<Directory "/home/httpd/html/dav">
  DAV On
  AllowOverride None
  Options Indexes FollowSymLinks
  AuthType Digest # substitute "Basic" for "Digest" if you used htpasswd above
  AuthName "WebDAV"
  AuthUserFile /etc/httpd/conf/passwd
  Require user foo
</Directory>
```

> **注意：**`AuthName` must match the realm name passed when using the `htdigest` command for digest authentication. For basic/plain authentication, this line may be removed. Also, make sure that the `AuthUserFile` path matches that used with the `htdigest` or `htpasswd` commands above.

If you want to permit everybody to read, you could use this in your httpd.conf

```
<Directory "/home/httpd/html/dav">
  DAV On
  AllowOverride None
  Options Indexes FollowSymLinks
  AuthType Digest # substitute "Basic" for "Digest" if you used htpasswd above
  AuthName "WebDAV"
  AuthUserFile /etc/httpd/conf/passwd
  Require all granted
  <LimitExcept GET HEAD OPTIONS PROPFIND>
    Require user foo
  </LimitExcept>
</Directory>
```

Do not forget to restart `httpd.service` after making changes.

> **注意：**If you get an 405 error with Apache, add `DirectoryIndex disabled` to your `Directory` section.

## Troubleshooting

### Some file explorers cannot edit directories in nginx WebDAV

nginx WebDAV requires a directory path ends with a slash (`/`), but some file explorers does not append a `/` at the end of the path.

This can be worked-around, by either removing the corresponding checking [code](https://github.com/nginx/nginx/blob/master/src/http/modules/ngx_http_dav_module.c) and recompile it, or by appending the following code in a nginx `server` block to add `/` at the end of a request, if needed:

```
# The configuration was based on: https://nworm.icu/post/nginx-webdav-dolphin-deken/
# if the request method is MKCOL or is to a directory, add / at the end of the request if it was missing
if ($request_method = MKCOL) {
    rewrite ^(.*[^/])$ $1/ break;
}
if (-d $request_filename) {
    rewrite ^(.*[^/])$ $1/ break;
}

# if the request method is copy or move a directory, add / at the end of the request if it was missing
set $is_copy_or_move 0;
set $is_dir 0;
if (-d $request_filename) {
    set $is_dir 1;
}
if ($request_method = COPY) {
    set $is_copy_or_move 1;
}
if ($request_method = MOVE) {
    set $is_copy_or_move 1;
}
set $is_rewrite "${is_dir}${is_copy_or_move}";
if ($is_rewrite = 11) {
    rewrite ^(.*[^/])$ $1/ break;
}
```
