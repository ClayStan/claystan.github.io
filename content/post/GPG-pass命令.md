---
title: "GPG Pass命令"
date: 2021-02-06T18:38:22+08:00
lastmod: 2021-02-06T18:38:22+08:00
draft: false
keywords: ["GPG","pass"]
description: "GPG 关于 pass 命令的使用"
tags: ["GPG","pass"]
categories: ["GPG"]
author: "Clay Stan"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---

<!---->

## 前言 

前面我们讲的 `GPG` 的相关内容，对于大多数人来可能用的不多。今天我们就来实际的使用一下，用它来管理你的密码。

密码是我们日常都会用到的，而且使用很多，每个网站都有不同的账号和不同的密码。如果你使用同样的密码，那就非常危险了，一旦某个密码泄漏了，其他网站也会跟着遭殃。定期修改密码还是非常有必要的。

## pass 介绍

`pass` 是一个符合 `Unix` 哲学的命令行工具，它被设计成用来管理密码，并且是开源的，如果你不放心，可以去查看代码，或者改成你需要的。不要看到命令行就怕了，它有很多客户端，可以满足大多数人使用。

类似 `pass` 的产品有我们常见的 `1Password` 、 `LastPass` 等。`1Password` 是个收费的软件， `LastPass` 虽然也有免费的版本，但是密码放在别人那里总是不放心。毕竟之前也出过幺蛾子。如果你有使用这些产品，也可以非常方便导入到 `pass` 中。

`pass` 使用 `GPG` 非对称加密，这就是我们选择它的原因。

## pass 安装

照例我们先来安装 `pass`

### macOS

```bash
brew install pass
```

### Arch / Manjaro

```bash
sudo pacman -S pass
```

### Debian

``````bash
sudo apt install pass
``````



## 初始化 pass

在初始化之前，先要确保你已经用 `GPG` 创建过密钥了，如果没有可以查看前几篇文章。

```bash
➜  ~ pass init test1 # test1 是用来加密的 GPG id
mkdir: created directory '/home/test/.password-store/'
Password store initialized for test1
```

## 添加密码

初始化好了密码之后，我们就可以把已经存在密码添加到 pass 中了。

```bash
➜  ~ pass insert Email/test.com
mkdir: created directory '/home/test/.password-store/Email'
Enter password for Email/test.com:
Retype password for Email/test.com:
[master b331dff] Add given password for Email/test.com to store.
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 Email/test.com.gpg
```

除了把已经注册过的网站添加到 `pass` 中，我们还可以用 `pass` 来生成密码。用 `pass` 生成密码可以避免所有的密码都使用同一个。

```bash
➜  ~ pass generate Email/test1.com 15
[master e5be6b2] Add generated password for Email/test1.com.
1 file changed, 0 insertions(+), 0 deletions(-)
create mode 100644 Email/test1.com.gpg
The generated password for Email/test1.com is:
o6oQ\!}P$]sOU<l
```

后面的 `15` 表示密码的位数。

## 查看密码

```bash
➜  ~ pass Email/test1.com
o6oQ\!}P$]sOU<l
# 拷贝密码到剪切板
➜  ~ pass -c Email/test.com
Copied Email/test.com to clipboard. Will clear in 45 seconds.
```

## 修改密码

```bash
➜  ~ pass insert Email/test.com
An entry already exists for Email/test.com. Overwrite it? [y/N] y
Enter password for Email/test.com:
Retype password for Email/test.com:
[master 0d99622] Add given password for Email/test.com to store.
 1 file changed, 0 insertions(+), 0 deletions(-)
 rewrite Email/test.com.gpg (100%)
➜  ~ pass generate Email/test1.com 15
An entry already exists for Email/test1.com. Overwrite it? [y/N] y
[master eceb081] Add generated password for Email/test1.com.
 1 file changed, 0 insertions(+), 0 deletions(-)
 rewrite Email/test1.com.gpg (100%)
The generated password for Email/test1.com is:
Y<`ZO,'VFx8|U[_
```

## 删除密码

```bash
➜  ~ pass rm Email/test1.com
Are you sure you would like to delete Email/test1.com? [y/N] y
removed '/home/test/.password-store/Email/test1.com.gpg'
[master 5ed43a8] Remove Email/test1.com from store.
 1 file changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 Email/test1.com.gpg
```

## 移动端

由于 `pass` 是个开源的产品，所有有很多大牛基于这个开发出了各种客户端，这里留下 `Android` 和 `iOS` 的地址，它们使用起来非常简单。



iOS

https://apps.apple.com/cn/app/pass-password-store/id1205820573?l=en

### Android

https://f-droid.org/packages/com.zeapo.pwdstore/

## 多端同步

我们现在日常每个人都可能又多个设备，所以我们需要在多个设备间进行同步。传统的方式就是通过 `U盘` 进行拷贝，这样做非常局限，不是每部手机都有 `USB` 。

为了解决这个问题 `pass` 结合 `Git` 用来同步密码。

```bash
# 只需要在已有的项目上输入如下命令
➜  ~ pass git init
Initialized empty Git repository in /home/test/.password-store/.git/
[master (root-commit) f41a601] Add current contents of password store.
 1 file changed, 1 insertion(+)
 create mode 100644 .gpg-id
[master d0c06cb] Configure git repository for gpg file diff.
 1 file changed, 1 insertion(+)
 create mode 100644 .gitattributes
```

每次操作， `pass` 都会用 `Git` 帮我们生成一次 `commit` 。

我们用 `Git` 去管理 `pass` 但是还没有配置 `Git` 的仓库地址，如过你没有可以使用 `Github` 或者自己搭一个 `Gitlab` 甚至可以使用 `Gitee` 。配置的命令如下

```bash
pass git remote add origin https://github.com/xxx/xxx.git
```

添加好之后我们就需要把本地的密码推送到服务器了

```bash
pass git push
```

当我们在另外一台设备上使用的时候，可以通过下面命令来把密码下载到本地

```bash
pass git pull
```

这些放到 `Git` 中的文件都是已经加过密的，只要你不把私钥泄漏，还是相当安全的。

参考资料

[1] Pass: The Standard Unix Password Manager: https://www.passwordstore.org/



本文来源于 微信公众号  "devbins"  所分享内容