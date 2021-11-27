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

## Mac OS 命令行打开终端

有时候看电影里电脑特效很酷，动不动就自动打开一大堆 terminal，今天闲着无聊 google 了一下，下面是 MacOS 的：

```zsh
osascript -e 'tell app "Terminal"
    do script "echo hello"
end tell'
```

Linux 的话：

```bash
# terminal -e command
xterm -e ls # 使用  xterm
gnome-terminal -x ls # 使用 gnome-terminal
```