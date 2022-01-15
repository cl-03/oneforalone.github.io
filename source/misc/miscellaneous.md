# 杂记

本文记录了一些简单的问题和解决方法。

---

## jekyll markdown 中用 latex

近期看了会微积分，想在博客中记录一下一些通用的微积分公式，在本地 vs code 中预览完全没问题，
推到 Github 上发现并没有渲染 $LATEX$ 的格式。查了一下发现是需要在 Jekyll 中添加个插件：

在 `_config.yml` 文件中添加一行: `markdown: kramdown` 。然后到自己的 includes 中的
被所有的 layout 文件都 include 了的文件（我的是 `header.html`）中添加下面的 js 代码：

```html
<!-- add latex support -->

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({
        tex2jax: {
        skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
        inlineMath: [['$','$']]
        }
    });
</script>
```


## Git clone error

最近在服务器上拉 Github 代码时发现拉不下来，报了一下错误

```bash
fatal: unable to access 'https://github.com/xxx.git/': gnutls_handshake() failed: The TLS connection was non-properly terminated.
```

检查一番发现是服务器配置了代理，但代理挂了

### 查看当前代理

* 方法一

```bash
git config --global http.proxy
```

* 方法二

```bash
env | grep -I proxy
```

### 取消代理

* 方法一

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

* 方式二

```bash
export http_proxy=""
export https_proxy=""
export HTTP_PROXY=""
export HTTPS_PROXY=""
```

* 方式三

```bash
unset http_proxy
unset ftp_proxy
unset all_proxy
unset https_proxy
unset no_proxy
```


## CentOS 6.10 安装 Adobe Reader

Recently, one of my former colleagues want to install adobe reader on their
CentOS 6.10 server, so, here is the tutorial：

```bash
wget http://ardownload.adobe.com/pub/adobe/reader/unix/9.x/9.5.5/enu/AdbeRdr9.5.5-1_i486linux_enu.rpm

yum install -y atk-1.30.0-1.el6.i686 fontconfig-2.8.0-5.el6.i686 \
  gdk-pixbuf2-2.24.1-6.el6_7.i686 gtk2-2.24.23-9.el6.i686 \
  libidn-1.18-2.el6.i686 pango-1.28.1-11.el6.i686 \
  libxml2-2.7.6-21.el6_8.1.i686
```

## Ubuntu 18.04 apt certificate error

最近在使用 Ubuntu 18.04 安装软件是突然遇到个证书错误：

```
Certificate verification failed: The certificate is NOT trusted. The certificate issuer is unknown.  Could not handshake: Error in the certificate verification.
```

查了下文档，发现是因为服务器走了代理，但因特殊情况又不能关掉代理，那么怎么办呢？直接不进行证书认证就好了。

```bash
sudo -i
echo "Acquire::https { Verify-Peer \"false\" }" >> /etc/apt/apt.conf.d/99verify-peer.conf
```

Okay, Problem Sovled.

### Reference:

1. [StackExchange: apt-get update failed...](https://askubuntu.com/questions/1095266/apt-get-update-failed-because-certificate-verification-failed-because-handshake)
2. [Ubuntu Manpage: apt-transport-https](http://manpages.ubuntu.com/manpages/bionic/man1/apt-transport-https.1.html)


## CentOS 7 配置 bond

有时候在安装系统时忘了配置 bond，需要用命令手动配置，理论上来说，只需要创建好对应的配置文件应该就可以，
但是那配置文件，根本记不住。google 一下，发现可以使用 `nmcli` 来配置。

```bash
nmcli connection add type bond ifname bond0 mode 4
nmcli connection add type bond-slave ifname enps0f0 master bond0
nmcli connection add type bond-slave ifname enps0f1 master bond0
```

然后在根据自己的需求配置对应的 bond 参数以及网络信息。至于其背后详细的原理，请参考 [bond技术分析](https://cloud.tencent.com/developer/article/1087312?from=15425)


### Reference

[1]. [centos7.x网卡bond配置](https://www.cnblogs.com/liwanggui/p/6807212.html)


## CentOS 7 root 忘记密码

通常来说，系统的 root 密码应该是被记录下来的，不会被忘记。但是就是有不一般的情况。
客户直接将服务器寄过来，然后就让我们去检查配置。伞兵客户。没办法，只能强制将 root
的密码修改咯。 直接参考博客——[CentOS 7 root用户密码忘记，找回密码方法](https://blog.csdn.net/qq_43518645/article/details/105098090)
进行修改。

为了防止 CSDN 访问出现异常，加个简单的记录吧：

1. 按 `e` 修改系统启动项
2. 将以 `linux16` 开头的一行中末尾处的 `ro` 修改成 `rw init=sysroot/bin/sh`
3. 按 `Ctrl+X` 后会直接进入系统
4. 将 `/sysroot` 指定为 `/` ：`chroot /sysroot`
5. 修改密码： `passwd`
6. 更新系统信息：`touch /.autorelabel`
7. 退出 `chroot`：`exit`
8. 重启服务器：`reoobt`

注：如果机器使用了 UEFI 启动，需要先进入 BIOS 关闭 UEFI 然后再启动，不然看不到
系统的选择界面。 UEFI 启动加载太快了，根本反应不过来就进入系统了。


## Cron 任务执行不成功

突然发现配置了一个 cron 任务的脚本，没有得到预期的结果，查看日志发现任务确实执行了。
然后查了下资料发现 cron 的命令是以 sh 执行的，而我用的是 bash 的语法。Well，改了
后就执行成功了。其实就是 sh 中没有 `source` 命令，只有 `.` 命令。

Emmm…… 之后再找找资料详细的对比下 systemd 的 timer 和 crontab 吧。

### Reference

[1]. https://unix.stackexchange.com/questions/278564/cron-vs-systemd-timers

[2]. https://askubuntu.com/questions/752240/crontab-syntax-multiple-commands