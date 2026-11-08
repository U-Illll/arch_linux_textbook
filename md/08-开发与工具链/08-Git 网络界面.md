# 第 8 章　Git 网络界面

> 想把提交历史摆到浏览器里，或者干脆自建一套协作平台，就得在这几个工具里挑一个。四节由轻到重排开：《Gitweb》最轻，只多一道网络服务器的配置；《Gitea》居中，安装、配置、用法、技巧与问题解决俱全；《GitLab》最重，篇幅近半，另多出启动测试与每次更新时的数据库升级；《SonarQube》另成一类，安装、配置、升级、问题解决单列。
> 
> 读法上，《Gitea》必读，自建平台照它走；《Gitweb》可速读；《GitLab》按需展开，功能最全，但要连带照看数据库与后续配置；只想浏览仓库，《SonarQube》可以跳过。最容易踩的坑是把升级当成换个包：《GitLab》每次更新都要单独处理数据库，漏掉这步服务就起不来。同样别以为装上即可访问，《Gitweb》的前置是先把网络服务器配好。

<!-- 本章 4 节，共 51 KB -->

## 8.1　SonarQube

*难度：入门 ｜ 篇幅：4.8 KB ｜ 所属：Git 网络界面*

将要讲到：安装、配置、升级、用法、问题解决、参见

> **翻译状态：**
>
> - 本文（或部分内容）译自 [SonarQube](https://wiki.archlinux.org/title/SonarQube)，最近一次同步于 2024-8-1，若英文版本有所[更改](https://wiki.archlinux.org/title/SonarQube?diff=0&oldid=813648)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/SonarQube_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

[SonarQube](https://www.sonarsource.com/products/sonarqube/) 是用于改进代码的代码质量工具，以 LGPL3 许可发布。

## 安装

安装 [sonarqube-bin](https://aur.archlinux.org/packages/sonarqube-bin/)AUR 软件包。

SonarQube 默认使用 H2 数据库（不推荐）。 或需要使用数据库后端，支持以下数据库：

- [MariaDB](../../06_软件与应用/数据库管理系统/MariaDB.md)/[MySQL](../../06_软件与应用/Oracle/MySQL.md)
- [PostgreSQL](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md)
- Oracle
- MSSQL

## 配置

用户配置文件位于 `/etc/webapps/sonarqube/sonar.properties` 中。

更多配置示例请参阅 [SonarQube docs](https://docs.sonarsource.com/sonarqube/latest/analyzing-source-code/analysis-parameters)。

### PostgreSQL

[安装](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md#安装)并[配置](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md#初始化配置) [PostgreSQL](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md)。

SonarQube 似乎只支持 TCP Socket 。

#### 使用 TCP 套接字

连接 postgresql：

```
[postgres]$ psql
```

以 `postgres` 用户身份连接服务器时创建新用户（系统会提示输入新用户密码）：

```
postgres=# CREATE USER sonarqube WITH PASSWORD password;
```

创建 Gitea 数据库，由 `gitea` 用户拥有：

```
postgres=# CREATE DATABASE sonarqube OWNER sonarqube;
postgres=#GRANT ALL PRIVILEGES ON DATABASE sonarqube TO sonarqube;
```

[配置 PostgreSQL，使其可从远程主机访问](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md#Configure_PostgreSQL_to_be_accessible_from_remote_hosts)

验证其工作状态：

```
$ psql --host=ip_address --dbname=sonarqube --username=sonarqube --password
```

通过更新 `sonar.properties` 配置 SonarQube：

```
/etc/webapps/sonarqube/sonar.properties
```

```
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube
sonar.jdbc.username=sonarqube
sonar.jdbc.password=password
sonar.web.javaOpts=-Xmx512m -Xms128m -XX:+HeapDumpOnOutOfMemoryError
sonar.web.javaAdditionalOpts=-server
sonar.web.host=0.0.0.0
sonar.web.port=9000
sonar.log.level=INFO
sonar.path.logs=logs
```

## 升级

升级 SonarQube 后，请按照以下步骤操作：

- 更新 `sonar.properties` 文件以匹配新版本。
- SonarQube 运行后，会出现一个页面提示您升级。详细说明请访问[升级向导](https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/upgrade-the-server/upgrade/)。
- 导航至 SonarQube 服务器的设置页面 `https://*ip*:*port*/setup` 并点击升级按钮。
- 升级过程完成后，将显示正常的登录页面。

## 用法

启动/启用 `sonarqube.service`，网络接口应在 `http://localhost:9000` 上监听。

默认证书为 `admin/admin`。

> **注意：**
>
> - 您可能需要配置一个反向代理进行远程访问，例如 [nginx](../../03_网络与服务/网络服务器/Nginx.md)。
> - 如果不想让 Sonarqube 监听所有接口，请在 `/etc/webapps/sonarqube/sonar.properties` 中将 `sonar.web.host` 设置为 127.0.0.1 等。

### 与 maven 一起使用

```
$ mvn clean verify sonar:sonar -Dsonar.projectKey=project key -Dsonar.projectName=project -Dsonar.host.url=http://localhost:9000 -Dsonar.token=token
```

> **注意：***project key*、*project* 和 *token* 的值可在网络界面创建。

### 与 sonar-scanner 一起使用

有一个软件包 [sonar-scanner](https://aur.archlinux.org/packages/sonar-scanner/)AUR。用法如下

```
$ /opt/sonar-scanner/bin/sonar-scanner -Dsonar.projectKey=project key -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.token=token
```

> **注意：***project key* 和 *token* 的值可在网络界面创建。

## 问题解决

您可以查看单元状态或 [journal](../../02_系统管理与内核/日志/Systemd_Journal.md)。 还有 sonarqube 日志：

```
# /var/log/sonarqube/
```

如果 sonarqube 未激活，其中一个日志将包含错误信息。

## 参见

- [Sonarqube Documentation](https://docs.sonarsource.com/sonarqube/latest/)
- [sonarqube 安装手册](https://www.atlantic.net/dedicated-server-hosting/how-to-install-sonarqube-on-arch-linux/)

## 8.2　Gitweb

*难度：入门 ｜ 篇幅：5.8 KB ｜ 所属：Git 网络界面*

将要讲到：安装、配置网络服务器、配置 Gitweb、参见

Gitweb 是 [Git](../版本控制系统/Git.md) 自带的默认网络界面。

[Gitweb](https://git-scm.com/docs/gitweb) 实际上原生支持 FCGI，因此无需将其包装成 CGI 脚本。

## 安装

要安装 Gitweb，首先要安装 [git](https://archlinux.org/packages/?name=git)包 和[网络服务器](../../03_网络与服务/网络应用程序/应用程序列表_互联网.md)。如果想快速测试，请参阅 `git instaweb` 的帮助。否则，如果你想要一个全面的设置，请继续阅读。

以下所有示例都需要安装 [perl-cgi](https://archlinux.org/packages/?name=perl-cgi)包 软件包。（[FS#45431](https://bugs.archlinux.org/task/45431)）。

## 配置网络服务器

### Apache

将以下内容添加到 `/etc/httpd/conf/httpd.conf` 的末尾：

```
Alias /gitweb "/usr/share/gitweb"
<Directory "/usr/share/gitweb">
   DirectoryIndex gitweb.cgi
   Options ExecCGI
   Require all granted
   <Files gitweb.cgi>
   SetHandler cgi-script
   </Files>
   SetEnv  GITWEB_CONFIG  /etc/gitweb.conf
</Directory>
```

或者将其添加到另一个文件中，如 `etc/httpd/conf/extra/gitweb.conf` 然后在 `httpd.conf` 末尾添加以下内容：

```
# gitweb configuration
Include conf/extra/gitweb.conf
```

如果 Apache 拒绝显示 Gitweb，而是打印 perl 脚本的纯源代码，则很可能是 Apache 的配置不允许执行 CGI。请确保 `httpd.conf` 中的以下内容未被注释：

```
<IfModule !mpm_prefork_module>
   LoadModule cgid_module modules/mod_cgid.so
</IfModule>
<IfModule mpm_prefork_module>
   LoadModule cgi_module modules/mod_cgi.so
</IfModule>
```

然后重启 `httpd.service` 来应用这些更改。有关使用 Apache 执行 CGI 的更多详情，请参阅 [https://httpd.apache.org/docs/2.4/howto/cgi.html。](https://httpd.apache.org/docs/2.4/howto/cgi.html。)

### Lighttpd

在 `/etc/lighttpd/lighttpd.conf` 中添加以下内容：

```
server.modules += ( "mod_alias", "mod_cgi", "mod_redirect", "mod_setenv" )
url.redirect += ( "^/gitweb$" => "/gitweb/" )
alias.url += ( "/gitweb/" => "/usr/share/gitweb/" )
$HTTP["url"] =~ "^/gitweb/" {
       setenv.add-environment = (
               "GITWEB_CONFIG" => "/etc/gitweb.conf",
               "PATH" => env.PATH
       )
       cgi.assign = ( ".cgi" => "" )
       server.indexfiles = ( "gitweb.cgi" )
}
```

然后重启 `lighttpd.service` 来应用这些更改。 您可能还需要在 `mimetype.assign` 行添加 `".css" => "text/css"` 以便 GitWeb 正常显示。

### Nginx

将此位置添加到 nginx 配置中（可能需要更改位置）：

```
/etc/nginx/nginx.conf
```

```
location /gitweb.cgi {
    include fastcgi_params;
    gzip off;
    fastcgi_param   SCRIPT_FILENAME  /usr/share/gitweb/gitweb.cgi;
    fastcgi_param   GITWEB_CONFIG  /etc/gitweb.conf;
    fastcgi_pass    unix:/run/fcgiwrap.sock;
}

location / {
    root /usr/share/gitweb;
    index gitweb.cgi;
}
```

如果您遵循 [Nginx#CGI 实现](../../03_网络与服务/网络服务器/Nginx.md#CGI_实现)，请尝试将`include fastcgi_params;`替换为`include fastcgi.conf;`。

## 配置 Gitweb

所有配置选项的列表，请参见 [gitweb.conf(5)](https://man.archlinux.org/man/gitweb.conf.5) 。

### 基本配置

打开（如果不存在，则创建）文件 `/etc/gitweb.conf`，并将此内容放入其中：

```
/etc/gitweb.conf
```

```
# The directories where your projects are. Must not end with a slash.
our $projectroot = "/path/to/your/repositories";

# Base URLs for links displayed in the web interface.
our @git_base_url_list = qw(git://<your_server> http://git@<your_server>);
```

要启用 "blame" 视图（显示源文件中每一行的作者），请添加以下一行：

```
$feature{'blame'}{'default'} = ;
```

配置完成后，重启网络服务器。

### 添加仓库

要添加版本库，请进入版本库文件夹，像这样创建版本库：

```
$ mkdir my_repository.git
$ git init --bare my_repository.git/
$ cd my_repository.git/
$ touch git-daemon-export-ok
$ echo "Short project's description" > description
```

接下来打开 `config` 文件并添加以下内容：

```
config
```

```
[gitweb]
owner = Your Name
```

这将填写 Gitweb 中的 "Owner" 字段。这不是必填项。

这里假定您希望将此仓库作为 "central" 仓库，将您的提交推送至此，因此 git-daemon-export-ok 和 --bare 的作用是将开销降至最低，并允许在此仓库上使用 git 守护进程。

这就是创建版本库的全部过程。现在你可以在 [http://localhost/gitweb](http://localhost/gitweb) 上看到它了（假设一切顺利）。由于 Gitweb CGI 脚本只需读取版本库文件夹，因此无需为新版本库重启 Apache。

### 语法高亮

要在 Gitweb 上启用语法高亮，必须先安装 [highlight](https://archlinux.org/packages/?name=highlight)包 软件包：

安装 highlight 后，只需在 `gitweb.conf` 中添加此行即可：

```
$feature{'highlight'}{'default'} = ;
```

保存文件后，高亮显示就会启用。

## 参见

- [How To Install A Public Git Repository On A Debian Server](https://www.howtoforge.com/how-to-install-a-public-git-repository-on-a-debian-server)——HowtoForge 页面用作本文的主要资料来源。

## 8.3　Gitea

*难度：进阶 ｜ 篇幅：13.3 KB ｜ 所属：Git 网络界面*

将要讲到：安装、配置、用法、技巧和窍门、问题解决、参见

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** 需要翻译。（在 [Talk:Gitea#](https://wiki.archlinuxcn.org/wiki/Talk:Gitea) 中讨论）

> **翻译状态：**
>
> - 本文（或部分内容）译自 [Gitea](https://wiki.archlinux.org/title/Gitea)，最近一次同步于 2024-04-12，若英文版本有所[更改](https://wiki.archlinux.org/title/Gitea?diff=0&oldid=808158)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/Gitea_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

[Gitea](https://gitea.io/) 是 Gogs 的社区管理分支，Gogs 是用 Go 编写并在 MIT 许可下发布的轻量级代码托管解决方案。

## 安装

安装 [gitea](https://archlinux.org/packages/?name=gitea)包 或 [gitea-git](https://aur.archlinux.org/packages/gitea-git/)AUR 软件包。还有一个 gitea fork [forgejo](https://archlinux.org/packages/?name=forgejo)包 软件包。

Gitea 需要使用数据库后端，支持以下数据库：

- [MariaDB](../../06_软件与应用/数据库管理系统/MariaDB.md)/[MySQL](../../06_软件与应用/Oracle/MySQL.md)
- [PostgreSQL](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md)
- [SQLite](../../06_软件与应用/关系型数据库管理系统/SQLite.md)
- MSSQL

## 配置

用户配置文件位于 `/etc/gitea/app.ini`。

有关更多配置示例，参见 [Gitea 文档](https://docs.gitea.io/en-us/customizing-gitea/)。

### PostgreSQL

[安装](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md#Installation)并[配置](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md#Initial_configuration) [PostgreSQL](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md).

在 TCP 或 UNIX 套接字之间进行选择，然后跳转到相应的部分。

> **注意：**当 Gitea 和 PostgreSQL 在同一台机器上时，您应该使用 Unix 套接字，因为它更快、更安全。

#### 用 TCP socket

以 `postgres` 用户身份连接服务器时创建新用户（系统会提示输入新用户的密码）：

```
[postgres]$ createuser -P gitea
```

创建用户 `gitea` 拥有的 Gitea 数据库：

```
[postgres]$ createdb -O gitea gitea
```

[PostgreSQL#Configure PostgreSQL to be accessible from remote hosts](../../06_软件与应用/关系型数据库管理系统/PostgreSQL.md#Configure_PostgreSQL_to_be_accessible_from_remote_hosts)

验证它是否有效：

```
$ psql --host=ip_address --dbname=gitea --username=gitea --password
```

通过首次运行安装程序或更新 `app.ini` 配置 Gitea：

```
/etc/gitea/app.ini
```

```
DB_TYPE             = postgres
HOST                = hostadress:port
NAME                = gitea
USER                = gitea
; Use PASSWD = `your password` for quoting if you use special characters in the password.
PASSWD              = password
```

#### 用 Unix socket

以 `postgres` 用户身份连接服务器时创建新用户（系统会提示输入新用户的密码）：

```
[postgres]$ createuser gitea
```

创建用户 `gitea` 拥有的 Gitea 数据库：

```
[postgres]$ createdb -O gitea gitea
```

通过将以下行添加到 `/var/lib/postgres/data/pg_hba.conf` 来设置 Unix 套接字：

```
/var/lib/postgres/data/pg_hba.conf
```

```
local    gitea           gitea           peer
```

重新启动 `postgresql.service`.

验证它是否有效：

```
[gitea]$ psql --dbname=gitea --username=gitea
```

通过首次运行安装程序或更新 `app.ini` 配置 Gitea：

```
/etc/gitea/app.ini
```

```
DB_TYPE             = postgres
HOST                = /run/postgresql/
NAME                = gitea
USER                = gitea
PASSWD              =
```

### MariaDB/MySQL

> **注意：**使用`/var/run/mysqld/mysqld.sock` 作为监听参数，即可启用 MySQL 套接字支持。

以下是设置[MariaDB](../../06_软件与应用/数据库管理系统/MariaDB.md)的示例，设置所需的密码：

```
$ mysql -u root -p
```

```
mysql> CREATE DATABASE `gitea` DEFAULT CHARACTER SET `utf8mb4` COLLATE `utf8mb4_unicode_ci`;
mysql> CREATE USER `gitea`@'localhost' IDENTIFIED BY 'password';
mysql> GRANT ALL PRIVILEGES ON `gitea`.* TO `gitea`@`localhost`;
mysql> FLUSH PRIVILEGES;
mysql> \q
```

尝试使用新用户连接到新数据库：

```
$ mysql -u gitea -p -D gitea
```

通过首次运行安装程序或更新 `app.ini` 配置MariaDB：

```
/etc/gitea/app.ini
```

```
DB_TYPE  = mysql
HOST     = 127.0.0.1:3306 ; or /var/run/mysqld/mysqld.sock
NAME     = gitea
USER     = gitea
PASSWD   = password
```

## 用法

启动/启用 `gitea.service`, 网络接口应在 `http://localhost:3000` 上监听。

首次运行 Gitea 时，应该会重定向到 `http://localhost:3000/install`。

> **注意：**
>
> - 您可能希望配置反向代理以进行远程访问，例如 [nginx](#nginx).
> - 如果希望 Gitea 监听所有接口，请在 `/etc/gitea/app.ini` 中设置 `HTTP_ADDR = 0.0.0.0`。

## 技巧和窍门

### 本地 shell 客户端 (tea)

通过 [tea](https://archlinux.org/packages/?name=tea)包，您可以使用 gitea 的官方客户端。更多信息请访问 [https://gitea.com/gitea/tea](https://gitea.com/gitea/tea) 。

### 启用 SSH 支持

确保 [SSH](../../03_网络与服务/SSH/安全外壳协议.md) 已正确配置并运行。

#### 设置域名

您可能希望设置`SSH_DOMAIN`, 例如

```
/etc/gitea/app.ini
```

```
SSH_DOMAIN                 = git.domain.tld
```

> **注意：** 如果将 `PROTOCOL` 设置为 `unix` ，则需要取消设置 `LOCAL_ROOT_URL` 或将其设置为 `http://unix/`。参见 [comment](https://github.com/go-gitea/gitea/issues/3741#issuecomment-377334757)。

#### 配置 SSH

默认情况下，Gitea 将以用户 `gitea` 的身份运行；该账户也将用于 ssh 版本库访问。要使 ssh 访问正常，必须启用 PAM。或者，也可以解锁服务账户。

```
/etc/ssh/sshd_config
```

```
...
UsePAM yes
...
```

如果在 [SSH 配置](../../03_网络与服务/SSH/OpenSSH.md#Configuration_2)中使用 `AllowUsers`，请在其中添加 `AllowUsers gitea`，例如：

```
/etc/ssh/sshd_config
```

```
...
AllowUsers archie gitea
...
```

如果使用 `sshd.service`，请重新启动该服务（如果使用 `sshd.socket`，则无需重启）。

### 禁用 HTTP 协议

默认情况下，已启用通过 HTTP 协议与版本库交互的功能。 如果使用[SSH](../../03_网络与服务/SSH/安全外壳协议.md)，可以将 `DISABLE_HTTP_GIT` 设置为 `true`，禁用 HTTP 支持。

### 绑定受限端口

如果使用内置 SSH 服务器并希望 Gitea 将其绑定到 22 端口，或希望直接将 Gitea 网络服务器绑定到 80/443 端口（即在不使用代理的设置中），则需要添加 [drop-in systemd unit override](../../01_安装与包管理/初始化/Systemd.md#Drop-in_files)：

```
/etc/systemd/system/gitea.service.d/override.conf
```

```
[Service]
AmbientCapabilities=CAP_NET_BIND_SERVICE
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
PrivateUsers=false
```

### 启用 Dark 主题

在*ui*部分，可以将 `DEFAULT_THEME` 设置为 `arc-green`，使网页界面使用深色背景。

### 自定义 Gitea 的外观

更多详情，请参阅 Gitea 文档。

gitea 的外观可使用[Go](../编程语言/Go.md)模板进行高度定制。 首先，创建 `/var/lib/gitea/custom` 目录。 可通过编辑 `/var/lib/gitea/custom/templates` 中的文件覆盖模板。 默认模板可在 `templates` 目录下的 [Gitea 源代码](https://github.com/go-gitea/gitea)中找到。例如，要自定义主页，可将源代码中的 `templates/home.tmpl` 复制到 `/var/lib/gitea/custom/templates/home.tmpl` 并根据需要编辑模板。

此外，还可以通过创建以下文件更改徽标和图标： `/var/lib/gitea/custom/public/img/logo.svg`和 `/var/lib/gitea/custom/public/img/favicon.svg`

更改这些文件后，需要重新启动 Gitea。

### 配置反向代理

有关更多信息和示例，请参阅 Gitea 文档网站  上的反向代理部分。

#### nginx

The following is an example of using [nginx](../../03_网络与服务/网络服务器/Nginx.md#Managing_server_entries) as reverse proxy for Gitea over unix socket (you need to provide the SSL certificate):

```
/etc/nginx/servers-available/gitea.conf
```

```
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name git.domain.tld;

    ssl_certificate /path/to/fullchain.pem;
    ssl_certificate_key /path/to/privkey.pem;

    location / {
        client_max_body_size 512M;
        proxy_pass http://unix:/run/gitea/gitea.socket;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Update the `[server]` and `[session]` section of `app.ini`:

```
/etc/gitea/app.ini
```

```
[server]
PROTOCOL                   = unix
DOMAIN                     = git.domain.tld
ROOT_URL                   = https://git.domain.tld
HTTP_ADDR                  = /run/gitea/gitea.socket
LOCAL_ROOT_URL             =

[session]
COOKIE_SECURE              = true
```

> **注意：**You do not need to activate any SSL certificate options in `/etc/gitea/app.ini`.

#### Apache HTTP Server

The following is an example of using the [Apache HTTP Server](../../03_网络与服务/网络服务器/Apache%20HTTP%20服务器.md) as reverse proxy for Gitea over unix socket. To forward `domain.tld` to the gitea server, use

```
/etc/httpd/conf/httpd.conf
```

```
ProxyPreserveHost On
ProxyRequests off
AllowEncodedSlashes NoDecode
Proxypass / unix:/run/gitea/gitea.socket|http://domain.tld nocanon
ProxypassReverse / unix:/run/gitea/gitea.socket|http://domain.tld nocanon
```

where `domain.tld` should be replaced by your domain name (this entry is only passed as a header to the proxy, and does not seem to matter for this setup).

```
/etc/gitea/app.ini
```

```
[server]
PROTOCOL                   = unix
DOMAIN                     = domain.tld
ROOT_URL                   = https://domain.tld
HTTP_ADDR                  = /run/gitea/gitea.socket
LOCAL_ROOT_URL             =
```

To forward a subpath such as `domain.tld/git` to the gitea server, use

```
/etc/httpd/conf/httpd.conf
```

```
<Proxy *>
  Order allow,deny
  Allow from all
</Proxy>
AllowEncodedSlashes NoDecode
Proxypass /git unix:/run/gitea/gitea.socket|http://domain.tld nocanon
ProxypassReverse /git unix:/run/gitea/gitea.socket|http://domain.tld nocanon
```

```
/etc/gitea/app.ini
```

```
[server]
PROTOCOL                   = unix
DOMAIN                     = domain.tld
ROOT_URL                   = https://git.domain.tld
HTTP_ADDR                  = /run/gitea/gitea.socket
LOCAL_ROOT_URL             =
```

### Setup for custom data directory

As of now, you cannot use a custom path like `/srv/gitea` as your server home, since the shipped `gitea.service` unit file marks everything read-only.

To enable these custom paths, create a [drop-in snippet](../../01_安装与包管理/初始化/Systemd.md) with your server home directory as a new `ReadWriteDirectories` directive:

```
/etc/systemd/system/gitea.service.d/data-directory.conf
```

```
[Service]
ReadWriteDirectories=/srv/gitea
```

Then do a daemon-reload and restart `gitea.service` for the changes to take effect.

## 问题解决

### 升级到 1.5.0 后启动数据库时出现错误

升级到 1.5.0 后可能会出现问题。服务无法启动，日志中出现以下错误：

```
/var/log/gitea/gitea.log
```

```
2018/08/21 16:11:12 [...itea/routers/init.go:60 GlobalInit()] [E] Failed to initialize ORM engine: migrate: do migrate: Sync2: Error 1071: Specified key was too long; max key length is 767 bytes
```

要解决这个问题，请在 MySQL/MariaDB 服务器上以 \`root\` 用户身份运行以下命令

```
$ mysql -u root -p
```

```
MariaDB> set global innodb_large_prefix = `ON`;
```

gitea should stop complaining about key size and startup properly.

### Service failing with permission denied

如果手动创建 `gitea` 用户，并将其主页文件夹设置为 `/home/gitea`，gitea 服务将无法启动，并输出类似于 ：

```
Sep 04 04:44:32 systemd: gitea.service: Failed with result 'exit-code'.
Sep 04 04:44:32 systemd: gitea.service: Main process exited, code=exited, status=200/CHDIR
Sep 04 04:44:32 (gitea): gitea.service: Failed at step CHDIR spawning /usr/bin/gitea: Permission denied
Sep 04 04:44:32 (gitea): gitea.service: Changing to the requested working directory failed: Permission denied
```

该服务需要将用户的主文件夹作为 gitea 的主文件夹，默认文件夹为 `/var/lib/gitea` ：

```
$ usermod -d /var/lib/gitea gitea
```

## 参见

- [Gitea Documentation](https://docs.gitea.io/)

## 8.4　GitLab

*难度：高级 ｜ 篇幅：26.8 KB ｜ 所属：Git 网络界面*

将要讲到：安装、配置、开始并测试Gitlab、每次更新时升级数据库、更多配置、有用的建议

> [![](https://wiki.archlinuxcn.org/wzh/images/d/dc/Tango-preferences-desktop-locale.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-preferences-desktop-locale.png)**本文或本节需要[翻译](https://wiki.archlinuxcn.org/wzh/Project:Contributing_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#翻译)。要贡献翻译，请访问[简体中文翻译团队](https://wiki.archlinuxcn.org/wzh/Project:%E7%BF%BB%E8%AF%91%E5%9B%A2%E9%98%9F)。**
>
> **附注：** No updates since 2018（在 [Talk:GitLab#](https://wiki.archlinuxcn.org/wiki/Talk:GitLab) 中讨论）

> **翻译状态：**
>
> - 本文（或部分内容）译自 [Gitlab](https://wiki.archlinux.org/title/Gitlab)，最近一次同步于 2024-2-23，若英文版本有所[更改](https://wiki.archlinux.org/title/Gitlab?diff=0&oldid=800947)，则您可以帮助同步与[翻译](https://wiki.archlinuxcn.org/wzh/Help:%E7%BF%BB%E8%AF%91)更改的内容。
> - 您可以在 [ArchWiki 的对应页面](https://wiki.archlinux.org/title/Gitlab_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)?action=history)找到本文翻译的**原始**修订历史。
> - 本文可能与英文原文存在出入。

这是来自 [Gitlab的主页](https://about.gitlab.com/):

: GitLab 提供 git 仓储管理、代码查阅、问题追踪、动态监控和维基. 企业在本地安装Gitlab并用LDAP和活动目录服务器连接以进行安全的身份验证和授权。单个GitLab服务器可以处理超过25,000个用户，但也可以使用多个活动服务器创建高可用性设置。

你可以在 [GitLab.com](https://gitlab.com/)找到实时版本的例子.

## 安装

> **注意：**这篇文章优先覆盖非https安装和配置Gitlab. 如果需要，查阅[#更多配置](#更多配置) 来设置SSL.

Gitlab需要 Redis 和数据库后端. 如果你计划在同一个机器运行的话, 首先得安装 PostgreSQL (简体中文).

Install [gitlab](https://archlinux.org/packages/?name=gitlab)包 包.

为了接受邮件通知, 一个邮件服务器必须被安装和配置. 查阅 [Category:Mail server](https://wiki.archlinuxcn.org/wzh/Category:Mail_server) 获取更多细节.

## 配置

### 初步说明

Gitlab包括多种组件, 可查阅[架构概览页](https://docs.gitlab.com/ce/development/architecture.html).

[gitlab](https://archlinux.org/packages/?name=gitlab)包 包会以更接近标准Linux公约的方式安装Gitlab:

| 描述 | [GitLab's Official](https://github.com/gitlabhq/gitlabhq/blob/master/doc/install/installation.md) | [gitlab](https://archlinux.org/packages/?name=gitlab)包 |
| --- | --- | --- |
| 配置文件 GitShell | `/home/git/gitlab-shell/config.yml` | `/etc/webapps/gitlab-shell/config.yml` |
| 配置文件 GitLab | `/home/git/gitlab/config/gitlab.yml` | `/etc/webapps/gitlab/gitlab.yml` |
| 用户 (家目录) | `git` (`/home/git`) | `gitlab` (`/var/lib/gitlab`) |

> **提示：**如果你熟悉 [Arch Build System](../../00_关于与社区/关于%20Arch/Arch%20构建系统.md) 你可以编辑PKGBUILD和相关文件来改变gitlab的家目录到你喜欢的位置.

### GitLab

编辑 `/etc/webapps/gitlab/gitlab.yml` 并至少设置下面的参数:

> **注意：** `hostname` 和 `port` 被用于 `git clone [http://hostname:port](http://hostname:port)` 的例子中.

**Hostname:** 在 `gitlab:` 区里设置 `host:` - 把 `localhost` 换成 `yourdomain.com` (**注意:** 不要加 '[http://'](http://') 或者后面的斜线) - 到你的完全合格的域名.

**Port:** `port:` 可能会让你很困扰. 这不是Gitlab服务器运行的端口(unicorn) ; 这是用户通过浏览器初次连接的端口. 基本上, 如果你有意让用户通过他们的浏览器访问 'yourdomain.com' , 而且不用附加端口号, 把 `port:` 设成 `80`. 如果你想让用户输入比如 'yourdomain.com:3425' 这样的东西到浏览器, 那么你就得设置 `port:` 成 `3425`. 你也会得**设置你的网络服务器**来监听那个端口.

**Timezone (时区，可选):** `time_zone:` 参数是可选的, 但是它可能对强制Gitlab应用的区域有用.

最后设置正确的 [permissions](../命令行/文件权限与属性.md) 到 *uploads*（上传） 目录:

```
# chmod 700 /var/lib/gitlab/uploads
```

### 为Puma自定义端口

GitLab Puma 是处理大多数用户请求的主要组件. 默认的, Unix套接字 /run/gitlab/gitlab.socket，可以通过 `/etc/webapps/gitlab/puma.rb` 文件来改变:

```
/etc/webapps/gitlab/puma.rb
```

```
bind 'unix:///run/gitlab/gitlab.socket'
bind 'tcp://127.0.0.1:8080'
```

如果 Puma 地址被改变, 其它与Puma交流的组件的配置也需要被更新:

- 对于 GitLab Shell, 在 `/etc/webapps/gitlab-shell/config.yml`更新 `gitlab_url` 变量.

: > **提示：**根据注释里的配置文件, UNIX 套接字能通过 URL转义斜杠(/) (即. `http+unix://%2Frun%2Fgitlab%2Fgitlab.socket` 对应默认的 `/run/gitlab/gitlab.socket`). 额外的, 未转义斜杠能用来指定 [相关URL根路径](https://docs.gitlab.com/ce/install/relative_url.html) (例如. `/gitlab`).

- 对于 GitLab Workhorse, edit `gitlab-workhorse.service` 并更新 `-authBackend` 选项.

### 机密字符串

确定 `/etc/webapps/gitlab/secret` 和 `/etc/webapps/gitlab-shell/secret` 文件包含了些什么. 它们的内容应该被保密因为它们会被用作生成认证令牌等.

比如, 能够通过下面的命令生成随机字符串:

```
# hexdump -v -n 64 -e '1/1 "%02x"' /dev/urandom > /etc/webapps/gitlab/secret
# chmod 640 /etc/webapps/gitlab/secret
```

```
# hexdump -v -n 64 -e '1/1 "%02x"' /dev/urandom > /etc/webapps/gitlab-shell/secret
# chmod 640 /etc/webapps/gitlab-shell/secret
```

同时需要填写以下新的机密字符串

```
/etc/webapps/gitlab/secrets.yml
```

```
production:
  secret_key_base: secret
  db_key_base: secret
  otp_key_base: secret
  openid_connect_signing_key: secret
```

### Redis

为了提供足够的性能，你可能需要一个缓存数据库. 安装并配置一个redis实例, 注意专用于通过套接字监听的部分.

- 添加 `gitlab` 用户到 `redis` [user group](../../08_安全与加密/安全/用户和用户组.md).

- 更新配置文件:

```
/etc/webapps/gitlab/resque.yml
```

```
development:
  url: unix:/run/redis/redis.sock
test:
  url: unix:/run/redis/redis.sock
production:
  url: unix:/run/redis/redis.sock
```

```
/etc/webapps/gitlab-shell/config.yml
```

```
# Redis settings used for pushing commit notices to gitlab
redis:
  bin: /usr/bin/redis-cli
  host: 127.0.0.1
  port: 6379
  # pass: redispass # Allows you to specify the password for Redis
  database: 5 # Use different database, default up to 16
  socket: /run/redis/redis.sock # uncomment this line
  namespace: resque:gitlab
```

### 数据库后端

在Gitlab运行之前需要一个\[PostgreSQL\]数据库.

#### PostgreSQL

登录 PostgreSQL 并创造 `gitlabhq_production` 数据库和它的用户一起. 记得改变 `your_username_here` 和 `your_password_here` 到你的真正的值:

```
# psql -d template1
```

```
template1=# CREATE USER your_username_here WITH PASSWORD 'your_password_here';
template1=# ALTER USER your_username_here SUPERUSER;
template1=# CREATE DATABASE gitlabhq_production OWNER your_username_here;
template1=# \q
```

> **注意：**创建超级用户的目的是Gitlab在视图变得"智能"并安装扩展(并不仅仅在它的命名区间创建).并且它只被Postgresql的超级用户允许.

用新用户连接到新数据库来确定它有用:

```
# psql -d gitlabhq_production
```

打开新的 `/etc/webapps/gitlab/database.yml` 并设置 `username:` 和 `password:`的值. 比如:

```
/etc/webapps/gitlab/database.yml
```

```
#
# PRODUCTION
#
production:
  main:
    adapter: postgresql
    encoding: unicode
    database: gitlabhq_production
    username: your_username_here
    password: "your_password_here"
    # host: localhost
    # port: 5432
    socket: /run/postgresql/.s.PGSQL.5432
  ci:
    adapter: postgresql
    encoding: unicode
    database: gitlabhq_production
    database_tasks: false
    username: your_username_here
    password: "your_password_here"
    # host: localhost
    # port: 5432
    socket: /run/postgresql/.s.PGSQL.5432
...
```

对于我们的目的 (除非你知道你在干什么),你不必担心配置列在 `/etc/webapps/gitlab/database.yml`的其它数据库. 我们只需要设置生产的数据库来让Gitlab工作.

> **注意：**注意：自 GitLab 15.9 起，数据库.yml 中只有 main: 部分的做法已被淘汰。在 GitLab 17.0 及更高版本中，你必须在 database.yml 中包含两个部分：main: 和 ci:. ci:必须与 main: 连接到同一个数据库。

### 防火墙

如果你想通过[Iptables (简体中文)](../../03_网络与服务/防火墙/Iptables.md) 防火墙给予Gitlab安装过程直接权限, 你可能需要调整端口和网络地址:

```
# iptables -A tcp_inbound -p TCP -s 192.168.1.0/24 --destination-port 80 -j ACCEPT
```

启用 API-access:

```
# iptables -A tcp_inbound -p TCP -s 192.168.1.0/24 --destination-port 8080 -j ACCEPT
```

如果你在一个路由器后面, 别忘了转发这个端口到Gitlab服务器端口, 如果你想运行 WAN-access的话.

### 初始化Gitlab数据库

在初始化数据库之前开启 Redis 服务器和 `gitlab-gitaly.service` .

初始化数据库并激活更多特性:

```
$ cd /usr/share/webapps/gitlab
$ sudo -u gitlab $(cat environment | xargs) bundle exec rake gitlab:setup GITLAB_ROOT_PASSWORD=yourpassword GITLAB_ROOT_EMAIL=youremail GITLAB_LICENSE_FILE=/path/to/license
```

最后运行下面的命令来检查你的安装:

```
$ sudo -u gitlab $(cat environment | xargs) bundle exec rake gitlab:env:info
$ sudo -u gitlab $(cat environment | xargs) bundle exec rake gitlab:check
```

> **注意：**
>
> - *gitlab:env:info* 和 *gitlab:check* 命令会显示一个和Git相关的严重的错误. 这是正常的.
> - *gitlab:check* 会显示缺少初始化脚本. 这也没什么好担心的, 因为 [systemd](../../01_安装与包管理/初始化/Systemd.md) 服务文件会被相应使用 (而这Gitlab无法识别).

### 调整修改位

(如果用户和组所有权没有正确配置的话Gitlab检查不会通过)

```
# chmod -R ug+rwX,o-rwx /var/lib/gitlab/repositories/
# chmod -R ug-s /var/lib/gitlab/repositories
# find /var/lib/gitlab/repositories/ -type d -print0 | xargs -0 chmod g+s
```

## 开始并测试Gitlab

确定 PostgreSQL (简体中文) 和 Redis 运行和设置正确.

然后 start/enable `gitlab.target`.

现在你可以通过访问 [http://localhost:8080](http://localhost:8080) 或者 http://yourdomain.com来测试你的Gitlab\[[失效链接](https://zh.wikipedia.org/wiki/Wikipedia:%E5%A4%B1%E6%95%88%E9%93%BE%E6%8E%A5) 2020-08-02 ⓘ\], 你可能会被提示创建密码:

```
username: root
password: You'll be prompted to create one on your first visit.
```

查阅[#故障排除](#故障排除)和在 `/usr/share/webapps/gitlab/log/` 目录下的日志文件来 排除故障.

## 每次更新时升级数据库

### 手动操作

在更新 [gitlab](https://archlinux.org/packages/?name=gitlab)包 包后, 需要升级数据库:

```
$ cd /usr/share/webapps/gitlab
$ sudo -u gitlab $(cat environment | xargs) bundle exec rake db:migrate
```

之后, 之后重载并重启Gitlab相关服务: `gitlab-sidekiq.service`, `gitlab-puma.service`, `gitlab-workhorse.service` 和 `gitlab-gitaly.service`

### 自动操作

你可以创建 pacman 钩子来自动升级 GitLab 软件包更新的数据库。创建以下三个文件，不要忘记使 shell 脚本可执行：

```
/etc/pacman.d/hooks/05-gitlab-pre.hook
```

```
[Trigger]
Operation = Upgrade
Type = Package
Target = gitlab

[Action]
Description = Stopping gitlab services
When = PreTransaction
Exec = /usr/bin/systemctl stop gitlab-gitaly.service gitlab-mailroom.service gitlab-puma.service gitlab-sidekiq.service gitlab-workhorse.service
```

```
/etc/pacman.d/hooks/99-gitlab-post.hook
```

```
[Trigger]
Operation = Upgrade
Type = Package
Target = gitlab

[Action]
Description = Migrating GitLab database and starting services
When = PostTransaction
Exec = /etc/pacman.d/scripts/gitlab-migrate-database.sh
```

```
/etc/pacman.d/scripts/gitlab-migrate-database.sh
```

```
#!/bin/sh

cd "/usr/share/webapps/gitlab"
sudo -u gitlab $(cat environment | xargs) bundle exec rake db:migrate

# The hook runs after 30-systemd-daemon-reload.hook so another systemctl daemon-reload is not necessary.
systemctl start gitlab.target
```

## 更多配置

### 基本的 SSH

在完成了基本安装后, 为用户设置ssh权限. [安全外壳协议](../../03_网络与服务/SSH/安全外壳协议.md)的配置会在下面描述. [其它SSH客户端和服务器](../../03_网络与服务/SSH/安全外壳协议.md#Implementations)需要不同的调整.

获取添加用户SSH秘钥的建议，过程在[GitLab](https://docs.gitlab.com/ee/ssh/) 网站里描述的很好了. 你可以在 `/var/lib/gitlab/log/gitlab-shell.log`检查管理员日志来确认用户SSH秘钥被正确提交了. 在这些事件之后, GitLab 添加这些秘钥到*authorized_keys* 文件里，它在 `/var/lib/gitlab/.ssh/authorized_keys`.

测试秘钥的常见方法 (比如: `$ ssh -T git@*YOUR_SERVER*`) 需要一点额外配置才能正常工作. 在 `/etc/webapps/gitlab/gitlab.yml` (默认用户: `gitlab`)里配置的用户必须添加到服务器的sshd配置文件,除此之外还有几个其它改变 :

```
/etc/ssh/sshd_config
```

```
PubkeyAuthentication   yes
AuthorizedKeysFile     %h/.ssh/authorized_keys
```

更新配置文件之后, 重启ssh守护进程:

```
# systemctl restart sshd
```

测试用户SSH米哟啊 (可选添加 -v 来查看额外信息):

```
$ ssh -T gitlab@YOUR_SERVER
```

### 自定义SSH连接

如果你在一个非标准端口运行SSH，你必须改变Gitlab用户的SSH配置:

```
/var/lib/gitlab/.ssh/config
```

```
host localhost      # Give your setup a name (here: override localhost)
user gitlab         # Your remote git user
port 2222           # Your port number
hostname 127.0.0.1; # Your server name or IP
```

你还必须在 `/etc/webapps/gitlab/gitlab.yml` 文件里改变相应的选项 (比如. ssh_user, ssh_host, admin_uri) .

### HTTPS/SSL

#### 改变Gitlab配置

修改 `/etc/webapps/gitlab/shell.yml` 那样到你的Gitlab站点的URL就会以 `https://`开头. 修改 `/etc/webapps/gitlab/gitlab.yml` 那样 `https:` 设置就会被设为 `true`.

查阅 [Apache HTTP Server#TLS](../../03_网络与服务/网络服务器/Apache%20HTTP%20服务器.md#TLS) 和 Let’s Encrypt.

#### Let's Encrypt(让我们加密吧)

验证你的URL, Let's Encrypt的过程会试图用像 `https://gitlab.*YOUR_SERVER_FQDN*/.well-known/acme-challenge/*A_LONG_ID*`的东西连接你的Gitlab服务器. 但是, 因为Gitlab配置, 每个到 `gitlab.*YOUR_SERVER_FQDN*` 的请求会被重定向到一个代理 (gitlab-workhorse) 而它无法处理这个URL.

为了绕过这个问题, 你可以使用 Let's Encrypt的webroot配置, 在 `/srv/http/letsencrypt/`设置webroot.

除此之外, 强迫到Gitlab的Let's Encrypt请求重定向到这个webroot可通过添加下面的:

```
/etc/http/conf/extra/gitlab.conf
```

```
Alias "/.well-known"  "/srv/http/letsencrypt/.well-known"
RewriteCond   %{REQUEST_URI}  !/\.well-known/.*
```

### Web服务器配置

如果你想把Gitlab集成进一个运行的服务器而不是用它的内置http服务器Unicorn，那么按照这些说明操作.

#### Node.js

你可以轻松在443端口设置http代理来代理到8080端口的Gitlab程序的流量，通过为Node.js使用http-master. 在你创建你的域名的 OpenSSL 秘钥并获取你的CA 证书 (或自己设置的)后, 然后去 [https://github.com/CodeCharmLtd/http-master](https://github.com/CodeCharmLtd/http-master) 来学习使用https代理到Gitlab的请求多容易. http-master 建立在 [node-http-proxy](https://github.com/nodejitsu/node-http-proxy)之上.

#### Nginx

查阅 [Nginx#Configuration](../../03_网络与服务/网络服务器/Nginx.md#Configuration) 获取基本的 *nginx* 配置信息和 [Nginx#TLS](../../03_网络与服务/网络服务器/Nginx.md#TLS) 来启用 HTTPS. 在这个部分的例子假设服务器区块是用 [Nginx#Managing server entries](../../03_网络与服务/网络服务器/Nginx.md#Managing_server_entries)管理的.

创建和编辑基于下面代码的配置. 查阅 [upstream GitLab repository](https://gitlab.com/gitlab-org/gitlab-ce/tree/master/lib/support/nginx)获取更多例子.

```
/etc/nginx/servers-available/gitlab
```

```
upstream gitlab-workhorse {
  server unix:/run/gitlab/gitlab-workhorse.socket fail_timeout=0;
}

server {
  listen 80;
  #listen 443 ssl; # uncomment to enable ssl
  server_name example.com

  #ssl_certificate ssl/example.com.crt;
  #ssl_certificate_key ssl/example.com.key;

  location / {
      # unlimited upload size in nginx (so the setting in GitLab applies)
      client_max_body_size 0;

      # proxy timeout should match the timeout value set in /etc/webapps/gitlab/unicorn.rb
      proxy_read_timeout 60;
      proxy_connect_timeout 60;
      proxy_redirect off;

      proxy_set_header Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-Ssl on;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;

      proxy_pass http://gitlab-workhorse;
  }

  error_page 404 /404.html;
  error_page 422 /422.html;
  error_page 500 /500.html;
  error_page 502 /502.html;
  error_page 503 /503.html;
  location ~ ^/(404|422|500|502|503)\.html$ {
    root /usr/share/webapps/gitlab/public;
    internal;
  }
}
```

#### Apache

安装并配置 Apache HTTP Server (简体中文). 你可以使用这些 [upstream recipes](https://gitlab.com/gitlab-org/gitlab-recipes/tree/master/web-server/apache) 来开始Gitlab虚拟主机的配置文件.

对于SSL配置查阅 [Apache HTTP Server#TLS](../../03_网络与服务/网络服务器/Apache%20HTTP%20服务器.md#TLS). 如果你不需要它, 移除它. 注意到SSL虚拟主机需要特定IP而不是通用IP. 同样如果你为Unicorn设置了自定义端口, 不要忘了在 `BalanceMember` 行也设置它.

### Gitlab-workhorse

> [![](https://wiki.archlinuxcn.org/wzh/images/6/62/Tango-edit-cut.png)](https://wiki.archlinuxcn.org/wzh/File:Tango-edit-cut.png)**这一章节正在考虑移除。**
>
> **原因:** [gitlab-workhorse](https://archlinux.org/packages/?name=gitlab-workhorse)包 组件现在是有必要的. 默认设置能应对普遍的情况所以这个部分能被移除. (在 [Talk:GitLab](https://wiki.archlinuxcn.org/wiki/Talk:GitLab) 讨论)


Gitlab8.0以后使用分开的http服务器[gitlab-workhorse](https://archlinux.org/packages/?name=gitlab-workhorse)包来应对大量的http请求比如 Git 推送/拉取. 如果你想使用这个而不是 SSH, 安装 [gitlab-workhorse](https://archlinux.org/packages/?name=gitlab-workhorse)包 包, 启用 `gitlab-workhorse.service` 并为它配置web服务器. [gitlab-workhorse](https://archlinux.org/packages/?name=gitlab-workhorse)包 优先级应高于 `gitlab-unicorn` 根据 GitLab 的建议: [https://gitlab.com/gitlab-org/gitlab-ce/issues/22528#note_16036216](https://gitlab.com/gitlab-org/gitlab-ce/issues/22528#note_16036216)

> **注意：**Unicorn还是需要所以不要disable或者stop它 `gitlab-unicorn.service`.

默认 [gitlab-workhorse](https://archlinux.org/packages/?name=gitlab-workhorse)包 监听 `/run/gitlab/gitlab-workhorse.socket`. 你可以 edit `gitlab-workhorse.service` 并改变参数 `-listenAddr` 来让它监听一个地址, 例如 `-listenAddr 127.0.0.1:8181`. 如果你监听一个地址的话你也要把网络类型设置为 `-listenNetwork tcp`

当你使用nginx时记得修改你的nginx配置文件. 从gitlab-unicorn 转换到 gitlab-workhorse 相应的编辑以下两个设置

```
/etc/nginx/servers-available/gitlab
```

```
upstream gitlab {
   server unix:/run/gitlab/gitlab-workhorse.socket fail_timeout=0;
}

...

      proxy_pass http://unix:/run/gitlab/gitlab-workhorse.socket;
  }
}
```

## 有用的建议

### 隐藏选项

到Gitlab的家目录:

```
# cd /usr/share/webapps/gitlab
```

并运行:

```
# rake -T | grep gitlab
```

```
rake gitlab:app:check                         # GITLAB | Check the configuration of the GitLab Rails app
rake gitlab:backup:create                     # GITLAB | Create a backup of the GitLab system
rake gitlab:backup:restore                    # GITLAB | Restore a previously created backup
rake gitlab:check                             # GITLAB | Check the configuration of GitLab and its environment
rake gitlab:cleanup:block_removed_ldap_users  # GITLAB | Cleanup | Block users that have been removed in LDAP
rake gitlab:cleanup:dirs                      # GITLAB | Cleanup | Clean namespaces
rake gitlab:cleanup:repos                     # GITLAB | Cleanup | Clean repositories
rake gitlab:env:check                         # GITLAB | Check the configuration of the environment
rake gitlab:env:info                          # GITLAB | Show information about GitLab and its environment
rake gitlab:generate_docs                     # GITLAB | Generate sdocs for project
rake gitlab:gitlab_shell:check                # GITLAB | Check the configuration of GitLab Shell
rake gitlab:import:all_users_to_all_groups    # GITLAB | Add all users to all groups (admin users are added as owners)
rake gitlab:import:all_users_to_all_projects  # GITLAB | Add all users to all projects (admin users are added as masters)
rake gitlab:import:repos                      # GITLAB | Import bare repositories from gitlab_shell -> repos_path into GitLab project instance
rake gitlab:import:user_to_groups[email]      # GITLAB | Add a specific user to all groups (as a developer)
rake gitlab:import:user_to_projects[email]    # GITLAB | Add a specific user to all projects (as a developer)
rake gitlab:satellites:create                 # GITLAB | Create satellite repos
rake gitlab:setup                             # GITLAB | Setup production application
rake gitlab:shell:build_missing_projects      # GITLAB | Build missing projects
rake gitlab:shell:install[tag,repo]           # GITLAB | Install or upgrade gitlab-shell
rake gitlab:shell:setup                       # GITLAB | Setup gitlab-shell
rake gitlab:sidekiq:check                     # GITLAB | Check the configuration of Sidekiq
rake gitlab:test                              # GITLAB | Run all tests
rake gitlab:web_hook:add                      # GITLAB | Adds a web hook to the projects
rake gitlab:web_hook:list                     # GITLAB | List web hooks
rake gitlab:web_hook:rm                       # GITLAB | Remove a web hook from the projects
rake setup                                    # GITLAB | Setup gitlab db
```

### 备份和恢复

创建Gitlab系统的备份:

```
# sudo -u gitlab -H rake RAILS_ENV=production gitlab:backup:create
```

从之前创建的备份文件恢复 `/home/gitlab/gitlab/tmp/backups/20130125_11h35_1359131740_gitlab_backup.tar`:

```
# sudo -u gitlab -H rake RAILS_ENV=production gitlab:backup:restore BACKUP=/home/gitlab/gitlab/tmp/backups/20130125_11h35_1359131740
```

> **注意：** 备份文件夹在 `config/gitlab.yml`设置. GitLab 备份和恢复记录在 [这里](https://github.com/gitlabhq/gitlabhq/blob/master/doc/raketasks/backup_restore.md).

### 通过SMTP从Gitlab发送邮件

你可能想用 gmail (或者其它邮件服务) 从你的Gitlab寄邮件. 这能避免在Gitlab服务器上安装邮件守护进程的需要.

根据你的邮件服务器设置调整 `smtp_settings.rb` :

```
/usr/share/webapps/gitlab/config/initializers/smtp_settings.rb
```

```
if Rails.env.production?
  Gitlab::Application.config.action_mailer.delivery_method = :smtp

  ActionMailer::Base.delivery_method = :smtp
  ActionMailer::Base.smtp_settings = {
    address:              'smtp.gmail.com',
    port:                 587,
    domain:               'gmail.com',
    user_name:            'username@gmail.com',
    password:             'application password',
    authentication:       'plain',
    enable_starttls_auto: true
  }
end
```

Gmail 会拒绝这样接受的邮件 (并会邮寄给你一个邮件它拒绝了). 你需要关闭安全认证 (按照拒绝邮件中的链接) 来解决这个问题. 更安全的办法是为username@gmail.com开启双因素认证并为这个配置文件设置应用密码.

## 故障排除

### HTTPS不是绿色 (gravatar不使用 https)

Redis 缓存了 gravatar 图像, 如果你用http拜访 GitLab, 那么启用 https, gravatar 会加载不安全的图像. 你可以清楚这些缓存通过执行以下命令

```
cd /usr/share/webapps/gitlab
RAILS_ENV=production bundle-2.3 exec rake cache:clear
```

以Gitlab用户的身份.

### 更新后的错误

从AUR更新安装包后, 数据库迁移和资源更新有时候会失败. 这些步骤可以解决这些问题, 如果简单的开关机不行的话.

首先, 移动到Gitlab安装目录.

```
# cd /usr/share/webapps/gitlab
```

如果每个Gitlab页面都给了一个500页面, 那么数据库迁移和资源可能太陈旧了. 如果不是的话, 跳过这步.

```
# su - gitlab -s /bin/sh -c "cd '/usr/share/webapps/gitlab'; bundle-2.3 exec rake db:migrate RAILS_ENV=production"
```

如果Gitlab一直在等待布置完成的话, 那么资源可能没有被重新编译.

```
# su - gitlab -s /bin/sh -c "cd '/usr/share/webapps/gitlab'; bundle-2.3 exec rake gitlab:assets:clean gitlab:assets:compile cache:clear RAILS_ENV=production"
```

最后, 重启Gitlab服务再测试你的网站.

```
# systemctl restart gitlab-unicorn gitlab-sidekiq gitlab-workhorse
```

### Gitlab-Unicorn 无法访问非默认的从库目录

如果自定义的仓库存储目录被设置在 `/home`, 在`gitlab-unicorn.service`禁用 `ProtectHome=true` 参数 (查阅 [Systemd#Drop-in files](../../01_安装与包管理/初始化/Systemd.md#Drop-in_files) 和 [github.com上的相关论坛帖子](https://forum.gitlab.com/t/cannot-change-repositores-location/9634/2)).

### 无法连接到 Gitaly

有时候, Gitaly服务无法开始, 让gitlab无法连接到gitlay. 解决办法很简单:

```
# systemctl start gitlab-gitaly
```

## 也可查阅

- [Official installation documentation](https://docs.gitlab.com/ce/install/installation.html)
- [GitLab recipes with further documentation on running it with several web servers](https://gitlab.com/gitlab-org/gitlab-recipes)
- [GitLab source code](https://gitlab.com/gitlab-org/gitlab-ce)
