# 第 28 章　XMPP

> 本章解决在 Arch 上把 XMPP 跑通：服务端 Jabberd2，客户端 Pidgin。Jabberd2 一节很短，只有安装、配置与参见，够搭出最小服务端骨架；Pidgin 一节是主体，安装后是拼写检查、修复声音、浏览器错误、QIP Encoding bug 与 ICQ，多是使用时才暴露的毛病。
> 
> 必读两段：Jabberd2 的配置、Pidgin 的安装，其余按症状查；已自建服务端或只用现成账号的，可跳过 Jabberd2 整节。最易踩的坑是把 Pidgin 一节条目当成必做步骤、逐条改配置——它们各对应一种症状，没出现就别动。

<!-- 本章 2 节，共 20 KB -->

## 28.1　Jabberd2

*难度：入门 ｜ 篇幅：1.7 KB ｜ 所属：XMPP*

将要讲到：安装、配置、参见

[jabberd2](https://jabberd2.org/) 是一个 [XMPP](https://en.wikipedia.org/wiki/XMPP) 服务器，用 C 语言编写，并根据 GNU 通用公共许可作为自由软件许可。它的灵感来自 jabberd14。

## 安装

安装 [jabberd2](https://aur.archlinux.org/packages/jabberd2/)AUR 包。

## 配置

编辑 `/etc/jabberd/c2s.xml` 并将标记 `<id register-enable='mu'>` 的内容更改为您的域。

这行将添加您的用户 ID。（如果您将 `example.com` 放在此处，则您的用户 ID 将类似于 `user@example.com`）如果将通过开放的 Internet（而不是 VPN 或 LAN）访问 jabber 服务，则该名称**应由** DNS 解析为您的服务器。

`register-enable='mu'` 部分允许使用标准的 jabber 客户端注册帐户。

还要在 `sm.xml` 上设置服务器：

```
/etc/jabberd/sm.xml
```

```
<id>mymachine.com</id>
```

### 守护进程

配置 `jabberd.service` 以在开机时启动。

阅读[守护程序](../../01_安装与包管理/初始化/Systemd.md)以获取更多信息。

## 参见

- [Jabberd2 主页](https://jabberd2.org/)

## 28.2　Pidgin

*难度：进阶 ｜ 篇幅：18.1 KB ｜ 所属：XMPP*

将要讲到：安装、拼写检查、修复声音、浏览器错误、QIP Encoding bug、ICQ

**Pidgin** （以前的Gaim）是Linux平台上的一个即时信息客户端，它可以连接到许多流行的即时信息网络，比如Live Messenger, Yahoo, IRC, AIM等等。Pidgin的一个关键特性是它可以同时连接许多即时信息网络。

## 安装

[官方软件仓库](../../00_关于与社区/关于%20Arch/官方仓库.md)提供了 [pidgin](https://archlinux.org/packages/?name=pidgin)包 的安装包。 Notable variants are:

- **Pidgin Light** — 不包含 GStreamer, Tcl/Tk, XScreenSaver, video/voice 支持的 Light Pidgin 版本。

: [https://pidgin.im/](https://pidgin.im/) || [pidgin-light](https://aur.archlinux.org/packages/pidgin-light/)AUR

你可以从[purple-plugin-pack](https://archlinux.org/packages/?name=purple-plugin-pack)包处安装其它的插件。

## 拼写检查

作为依赖Aspell会被默认安装，但为了避免你输入的所有文本都显示为“拼写错误”，你需要安装一个aspell字典（比如：[aspell-en](https://archlinux.org/packages/?name=aspell-en)包）。执行 `pacman -Ss aspell` 列出可用的语言。 如果拼写检查没有工作，尝试单独运行 aspell 去检查安装是否正确，并且不要分开有用的错误信息。执行：

```
$ echo center | aspell -a
```

> **注意：** **switch spell**插件包含在purple-plugin-pack中，它允许你在不同的语言间切换。

## 修复声音

为了修复声音需要安装[gstreamer0.10-good](https://aur.archlinux.org/packages/gstreamer0.10-good/)AUR包。或者，你可以在配置的声音选项卡中把方法（method）切换为‘命令’（'command'），然后使用下面的命令之一：

如果你使用的是[ALSA](../../04_硬件与外设/声音/ALSA.md):

```
aplay %s
```

如果使用的是[OSS](../../04_硬件与外设/声音/Open%20Sound%20System.md):

```
ossplay %s
```

## 浏览器错误

如果在Pidgin中点击网络链接会产生一个关于“试图使用'sensible-browser'打开链接”的错误，试试编辑`~/.purple/prefs.xml`。找到与'sensible-browser'相关的行改为以下的样子：

```
<pref name='command' type='path' value='firefox'/>
```

上面的例子假设你使用[Firefox](../../06_软件与应用/Mozilla/Firefox.md).

## QIP Encoding bug

There is another bug in character encoding when communicating between Pidgin and QIP, which especially affects Czech language, but there are also other languages affected. There are two possible solutions. The better one is to upgrade from QIP to QIP Infimum, second solution is to install and enable plugin from [pidgin-qip-decoder](https://aur.archlinux.org/packages/pidgin-qip-decoder/)AUR\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\] package currently available from [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md).

## ICQ

You can change encoding for ICQ account if encoding in Buddy Information is not correct:

```
Account > your ICQ account > Edit account > Advanced tab
```

Select `Encoding: CP1251` (for Cyrillic).

## IRC

有一辅助功能用于链接 Libera.chat。它应该也可以用于链接其他 IRC 网络，如果你变更其端口和域名。

选择*账户 > 管理账户 > 添加*. 设置以下选项:

```
协议: IRC
用户名: your username
```

选择*好友 > 加入聊天* (或者按下 `Ctrl+m`), 在 'libera.chat' 文本框中选择 *username*@irc.libera.chat, 然后点击 '确定'，或者输入：

```
/join #你的聊天室名称
```

如果要注册你的账户，输入：

```
/msg nickserv register password email-addres
```

按照Email中指示的做，你也可以输入以下内容来获得帮助：

```
/msg nickserv help
/msg nickserv help command
```

最后一部是把你的聊天室加入 '好友'：选择*好友 > 加入聊天*，填入正确的聊天室 (#archlinux)。

## Xfire

Simply install [pidgin-gfire](https://archlinux.org/packages/?name=pidgin-gfire)包\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\] and then add a new account, selecting xfire as protocol.

## Web QQ

直接安装 [pidgin-lwqq](https://archlinux.org/packages/?name=pidgin-lwqq)包\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\] 并创建账户，选择协议为 **webQQ** 。

已知问题：目前无法发送图片

## Facebook XMPP

Since Facebook Chat supports XMPP, you can use Pidgin without extra plugins. See this article for more information: [Facebook Chat Now Available Everywhere](https://blog.facebook.com/blog.php?post=297991732130)

> **注意：**In order to utilise Facebook chat through XMPP and pidgin, you will require a Facebook "username". This is located in *Facebook > Account settings > username* (below real name)

1\. Go to "Accounts" and select "Manage Accounts."

2\. On the Basic tab, enter the following info:

: Protocol: XMPP
: Username: *Your FacebookID* (without e-mail domain, e.g. @yahoo.com, etc)
: Domain: chat.facebook.com (make sure you have not typed any extra space)
: Resource: Pidgin (leave this empty if you get "username@chat.facebook.com/Pidgin Not Authorized" error message)
: Password: *Your Password*
: Local alias: *Your Name*

3\. Click the Advanced tab, then enter the following info:

: Connect port: 5222
: Connect server: chat.facebook.com (make sure you have not typed any extra space)
: (Uncheck the box labeled "Require SSL/TLS")

> **注意：**
>
> - Newer versions of Pidgin do not have a "Require SSL/TLS" box. Instead, select "Use encryption if available" from the Connection Security dropdown in Advanced
> - Changing your Facebook privacy settings to "Turn off all apps" (under Apps and Websites), will disable your ability to send messages via jabber (see [Why cannot Pidgin seem to send Facebook messages](https://developer.pidgin.im/wiki/Protocol%20Specific%20Questions?version=123#WhycantPidginseemtosendFacebookmessages)).
> - You may notice that all Facebook contacts are in a separate group every time you login with your XMPP account even though you moved them to other groups or created meta-contacts. If you want to be able to group contacts and create meta-contacts you can use the plugin [pidgin-xmpp-ignore-groups](https://aur.archlinux.org/packages/pidgin-xmpp-ignore-groups/)AUR (after installing the plugin activate the option *Ignore server-sent groups* on the Advanced tab in your XMPP account settings). It essentially ignores the group data sent by the server roaster and preserves your local changes. It is easier to enable this plugin on your account *before* logging in the first time, so your contacts are put into the default group instead of a group called "Facebook Friends". Enabling it afterward will not move the contacts out of this group.

## Privacy

Pidgin has some privacy rules set by default. Namely, the whole world cannot send you messages; only your contacts or people selected from a list. Adjust this, and other settings through:

```
Tools > Privacy
```

### Pidgin-OTR

This is a plugin that brings Off-The-Record (OTR) messaging to Pidgin. OTR is a cryptographic protocol that will encrypt your instant messages.

First you need to install [pidgin-otr](https://archlinux.org/packages/?name=pidgin-otr)包 from the official repositories. Once this has been done, OTR has been added to Pidgin.

1.  To enable OTR, start Pidgin and go to *Tools > Plugins* or press `Ctrl+u`. Scroll down to the entry entitled "Off-The-Record Messaging". If the checkbox beside it is not checked, check it.
2.  Next, click on the plugin entry and select "Configure plugin" at the bottom. Select which account you wish to generate a key for, then click "Generate". You will have now generated a private key. If you are not sure what the other options do, leave them, the default options will work fine.
3.  The next step is to contact a buddy who also has OTR installed. In the chat window, a new icon should appear to the top right of your text input box. Click on it, and select "Start private conversation". This will start an 'Unverified' session. Unverified sessions are encrypted, but not verified - that is, you have started a private conversation with someone using your buddy's account who has OTR, but who might not be your buddy. The steps for verification of a buddy are beyond the scope of this section; however, they might be added in the future.

### Pidgin-Encryption

[pidgin-encryption](https://aur.archlinux.org/packages/pidgin-encryption/)AUR transparently encrypts your instant messages with RSA encryption. Easy-to-use, but very secure.

You can enable it the same way as Pidgin-OTR.

Now you can open conversation window and new icon should appear beside menu. Press it to enable or disable encryption. Also if you want to make encryption enabled by default right-click on a buddy's name (in your buddy list), and select Turn Auto-Encrypt On. Now, whenever a new conversation window for that buddy is opened, encryption will start out as enabled.

### Pidgin-GPG

Pidgin-GPG transparently encrypt conversations using GPG, and taking advantage of all the features of a pre-existing WoT.

The plugin is available on AUR as [pidgin-gpg](https://aur.archlinux.org/packages/pidgin-gpg/)AUR\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\]. It can be enabled the same way as the previously mentioned ones.

## Sametime protocol

Sametime support is available by installing two packages from [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md):

- [meanwhile](https://aur.archlinux.org/packages/meanwhile/)AUR
- [libpurple-meanwhile](https://aur.archlinux.org/packages/libpurple-meanwhile/)AUR

Previously it was required to rebuild Pidgin to remove the `--disable-meanwhile` flag from compilation, this is no longer needed. Once these two packages are installed the 'Sametime' protocol will be available when creating an account.

## SIP/Simple protocol for Live Communications Server 2003/2005/2007

The [pidgin-sipe](https://aur.archlinux.org/packages/pidgin-sipe/)AUR plugin is available in [official repositories](../../00_关于与社区/关于%20Arch/官方仓库.md).

## Other packages

Arch has other Pidgin-related packages. Here are the most popular (for a thorough list, search the AUR):

- [pidgin-libnotify](https://archlinux.org/packages/?name=pidgin-libnotify)包 - Libnotify support, for theme-consistent notifications
- [guifications](https://archlinux.org/packages/?name=guifications)包\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\] - Toaster-style popup notifications
- [microblog-purple](https://aur.archlinux.org/packages/microblog-purple/)AUR\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\] - Libpurple plug-in supporting microblog services like Twitter
- [pidgin-latex](https://aur.archlinux.org/packages/pidgin-latex/)AUR - A small latex plugin for pidgin. Put math between $$ and have it rendered (recepient also needs to have this installed)

## Skype plugin

Install [skype4pidgin-svn](https://aur.archlinux.org/packages/skype4pidgin-svn/)AUR\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\] from the [AUR](../../00_关于与社区/关于%20Arch/Arch%20用户软件仓库.md).

## Auto logout on suspend

If you suspend your computer pidgin seems to stay connected for about 15 minutes. To prevent message loss, it is needed to set your status offline before suspending or hibernating. The status message will not be changed.

Therefore create a new systemd unit `pidgin-suspend` in `/etc/systemd/system` Take the following snippet and replace *myuser* with your user.

```
[Unit]
Description=Suspend Pidgin
Before=sleep.target
StopWhenUnneeded=yes

[Service]
Type=oneshot
User=myuser
RemainAfterExit=yes
Environment=DISPLAY=:0
ExecStart=-/usr/bin/purple-remote setstatus?status=offline
ExecStop=-/usr/bin/purple-remote setstatus?status=available

[Install]
WantedBy=sleep.target
```

## Troubleshooting

- If Facebook XMPP verification does not work for you, there a package in the aur [pidgin-facebookchat](https://github.com/aur-archive/pidgin-facebookchat) which does not require a unique user name (you may login with an email address)

- The facebookchat plugin will prompt for varification (enter these two words...), if that fails, hit cancel and log onto Facebook with pidgin open, this will configure the plugin's security setting)

### Installing Pidgin after a Carrier installation

If you previously installed [carrier](https://aur.archlinux.org/packages/carrier/)AUR\[[损坏的链接](https://wiki.archlinuxcn.org/wzh/Help:%E6%93%8D%E4%BD%9C%E6%B5%81%E7%A8%8B#修复损坏的包链接)：package not found\] (aka [FunPidgin](http://funpidgin.sourceforge.net/)), follow these steps *before* installing Pidgin:

- Quit Carrier
- Delete your `~/.purple` directory.

> **警告：**This will remove all your user settings for any programs that use libpurple, i.e. Pidgin, Carrier, etc.

```
rm -r ~/.purple
```

- Uninstall **carrier** and **libpurple**.
- Install **pidgin** and **libpurple**.

## History import Kopete to Pidgin

- Install [xalan-c](https://archlinux.org/packages/?name=xalan-c)包 and create `~/bin/history_import_kopete2pidgin.sh` with this code:

```
#!/bin/sh

KOPETE_DIR=~/.kde4/share/apps/kopete/logs
PIDGIN_DIR=~/.purple/logs
CURRENT_DIR=~/bin

cd

if [ ! -d $KOPETE_DIR ];then
    echo "Kopete log directory not found"
    exit 1;
fi

if [ ! -d $PIDGIN_DIR ];then
    echo "Pidgin log directory not found"
    exit 2;
fi

for KOPETE_PROTODIR in $(ls $KOPETE_DIR); do
    PIDGIN_PROTODIR=$(echo $KOPETE_PROTODIR | sed 's/Protocol//' | tr [:upper:] [:lower:])
    for accnum in $(ls $KOPETE_DIR/$KOPETE_PROTODIR); do
        echo "Account number: $accnum"
        for num in $(ls $KOPETE_DIR/$KOPETE_PROTODIR/$accnum); do
            FILENAME=$(Xalan $KOPETE_DIR/$KOPETE_PROTODIR/$accnum/$num $CURRENT_DIR/history_import_kopete2pidgin_filename.xslt)
            if [ $? = 0 ]; then
                echo "$KOPETE_DIR/$KOPETE_PROTODIR/$accnum/$num"
                echo " -> $PIDGIN_DIR/$PIDGIN_PROTODIR/$FILENAME"
                mkdir -p $(dirname $PIDGIN_DIR/$PIDGIN_PROTODIR/$FILENAME)
                Xalan -o $PIDGIN_DIR/$PIDGIN_PROTODIR/$FILENAME $KOPETE_DIR/$KOPETE_PROTODIR/$accnum/$num $CURRENT_DIR/history_import_kopete2pidgin.xslt
            fi
        done
    done
done
```

- Make `~/bin/history_import_kopete2pidgin.sh` executable:

```
chmod +x ~/bin/history_import_kopete2pidgin.sh
```

- Create `~/bin/history_import_kopete2pidgin.xslt` with this code:

```
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:output method="text" indent="no" />

    <xsl:template match="kopete-history">
        <xsl:apply-templates select="msg"/>
    </xsl:template>

    <xsl:template match="msg">
        <xsl:text>(</xsl:text>
        <xsl:value-of select="translate(substring-after(@time,' '),':',',')"/>
        <xsl:text>) </xsl:text>
        <xsl:value-of select="@nick"/>
        <xsl:if test="not(@nick) or @nick = ">
            <xsl:value-of select="@from"/>
        </xsl:if>
        <xsl:text>: </xsl:text>
        <xsl:value-of select="."/>
		<xsl:text>
</xsl:text>
    </xsl:template>
</xsl:stylesheet>
</nowiki>
```

- Create `~/bin/history_import_kopete2pidgin_filename.xslt` with this code:

```
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:output method="text" indent="no" />

    <xsl:template match="kopete-history">
        <xsl:value-of select="head/contact[@type = 'myself']/@contactId"/>
        <xsl:text>/</xsl:text>
        <xsl:value-of select="head/contact[not(@type)]/@contactId"/>
        <xsl:text>/</xsl:text>
        <xsl:value-of select="head/date/@year"/>
        <xsl:text>-</xsl:text>
        <xsl:if test="head/date/@month &lt; 10">0</xsl:if>
        <xsl:value-of select="head/date/@month"/>
        <xsl:text>-</xsl:text>
        <xsl:if test="string-length(substring-before(msg/@time,' ')) &lt; 2">0</xsl:if>
        <xsl:value-of select="translate(msg/@time,' :','.')"/>
        <xsl:text>+0200EET.txt</xsl:text>
    </xsl:template>
</xsl:stylesheet>
```

- Execute the command in the shell:

```
~/bin/history_import_kopete2pidgin.sh
```

## See also

- [Pidgin homepage](https://pidgin.im)
- [History Import Kopete To Pidgin](http://lukav.com/wordpress/2008/03/30/history-import-kopete-to-pidgin)
