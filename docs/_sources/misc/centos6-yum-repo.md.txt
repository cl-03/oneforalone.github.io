# CentOS 6 使用国内 yum 源

虽然说 CentOS 已经被收购了，但是国内还是有部分服务器使用的是 CentOS 6 的系统，
这时要想安装一些软件就会有点问题，官网给的源在国内的话速度很慢。解决办法有：

* 自己将 CentOS 的镜像下载到本地，搭建个本地源
* 替换成国内的源

方法一操作简单，这里不做介绍，记录下方法二的操作。经过一番 googling 和尝试，发现国内无论
是中科大还是网易，都没有 CentOS 6 的 yum 源了，最后发现清华大学的源很流畅。以下是
CentOS 6.10 跟换清华大学源的操作。

**注：** 以下操作都是在 `root` 用户下执行的。

## 将官方源给禁掉

```bash
sed -i 's/enable=1/enable=0/g' /etc/yum.repos.d/CentOS-Base.repo
```

## 替换 vault 源

```bash
sed -i 's#vault.centos.org#mirrors.tuna.tsinghua.edu.cn/centos-vault#g' /etc/yum.repos.d/CentOS-Vault.repo
```

## 生成本地缓存

```bash
yum makecache
```

这里会有多个版本的源，如果觉得太多，可以直接将除自己系统对应版本都删掉，只保留系统版本的源。即自行删除 `/etc/yum.repos.d/CentOS-Vault.repo` 中其他版本的源。

That's it.


## Reference

[1]. [CentOS 6 镜像源更换方法](https://support.huaweicloud.com/ecs_faq/ecs_faq_1009.html)