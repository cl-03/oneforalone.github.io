(git-with-gpg)=
# Using GPG signing key in git

Date: Tue. May. 3 2022

既然开始使用 GPG 了，那么就贯彻到底咯，git 也用上 GPG 签名好了。

## 初始化环境

### 安装并生成密钥

PGP 的安装和密钥的生成参考 {ref}`gpg-on-mac`  中的步骤。

### 导出公钥

* 列出 GPG keys

```bash
$ gpg2 --list-secret-keys --keyid-format=long
/Users/yuqi/.gnupg/pubring.kbx
------------------------------
sec   rsa4096/3095B650DD23CCAC 2022-04-15 [SC]
uid                 [ultimate] Yuqi Liu <yuqi.lyle@outlook.com>
ssb   rsa4096/A94CB14EEA671E75 2022-04-15 [E]
```

需要的是以 `sec` 开头中 `rsa4096/` 后面的那个序列。当然,
你熟悉 GPG 的话，使用 `uid` 也是 ok 的。甚至于你可以直接
使用 GPG Suit 的 GUI 导出，这个应该不用教了。自己灵活处理
吧。结果就是要导出你生成的密钥对的公钥。

* 导出 pubkey

```bash
$ gpg2 --armor --export pub 3095B650DD23CCAC
```

这里会在终端输出公钥，复制粘贴到 Github 账号中。

## 配置 git

设置签名时使用的 key：

```bash
$ git config --global user.signingkey 3095B650DD23CCAC
```

上面的命令中的 `user.signingkey` 后面接的是你自己的 key id。

因为 macOS 上安装的 GPG Suit 的命令是 `gpg2`，不是 `gpg`，所以
需要指定一下 GPG 的命令：

```bash
$ git config --global gpg.program gpg2
```

如果只是想对单独的 repo 使用 GPG 签名，那么就切换到那个 repo 目录下，
然后执行：

```bash
$ git config commit.gpgsign true
```

但是一般既然用了，那么肯定是喜欢全局都使用，那么就执行：

```bash
$ git config --global commit.gpgsign true
```

反正就是对 git 的配置，要是不想敲命令，就直接编辑
`$HOME/.gitconfig`，至于具体的参数格式，参考官方
文档吧：https://git-scm.com/docs/git-config。

Okay，Done。


## Reference:

1. [Telling Git about your signing key](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key)
2. {ref}`gpg-on-mac`
3. [使用 GPG 签名你的 Commit](https://www.cnblogs.com/xueweihan/p/5430451.html)
