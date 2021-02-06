---
title: "GPG入门三"
date: 2021-02-06T18:37:38+08:00
lastmod: 2021-02-06T18:37:38+08:00
draft: false
keywords: ["GPG"]
description: "GPG 操作使用入门系列"
tags: ["GPG"]
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

# 前言 

经过前面的两次的学习，各位小伙伴应该能够满足日常使用了。今天我们把剩下的一些知识给讲完，将来会用到的。

# 查看指纹

## 查看主钥的指纹

查看主钥的指纹有很多种方法

```bash
➜  ~ gpg -k
/home/ubuntu/.gnupg/pubring.kbx
-------------------------------
pub   rsa4096 2020-02-03 [SC]
B7C62E9D96B0977DEDFDE399C7BB725A82D22F2F
uid           [ unknown] test1 (test) <test@test.com>
sub   rsa4096 2020-02-03 [E]
sub   rsa4096 2020-02-03 [S] [expires: 2021-02-02]

# 查看完整指纹
➜  ~ gpg --fingerprint
/home/ubuntu/.gnupg/pubring.kbx
-------------------------------
pub   rsa4096 2020-02-03 [SC]
B7C6 2E9D 96B0 977D EDFD  E399 C7BB 725A 82D2 2F2F
uid           [ unknown] test1 (test) <test@test.com>
sub   rsa4096 2020-02-03 [E]
sub   rsa4096 2020-02-03 [S] [expires: 2021-02-02]

➜  ~ gpg --edit-key test1
gpg (GnuPG) 2.2.4; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa4096/C7BB725A82D22F2F
     created: 2020-02-03  expires: never       usage: SC
     trust: unknown       validity: unknown
ssb  rsa4096/C5F257E429558F85
     created: 2020-02-03  expires: never       usage: E
ssb  rsa4096/2D5337CD04EA4233
     created: 2020-02-03  expires: 2021-02-02  usage: S
[ unknown] (1). test1 (test) <test@test.com>

# 可以查完整的指纹
gpg> fpr
pub   rsa4096/C7BB725A82D22F2F 2020-02-03 test1 (test) <test@test.com>
Primary key fingerprint: B7C6 2E9D 96B0 977D EDFD  E399 C7BB 725A 82D2 2F2F
```

这么多，平时用的不多，记住一个或两个就够用了。

## 查看子钥的指纹

```bash
➜  ~ gpg --edit-key test1
gpg> list

sec  rsa4096/C7BB725A82D22F2F
     created: 2020-02-03  expires: never       usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa4096/C5F257E429558F85
     created: 2020-02-03  expires: never       usage: E
ssb  rsa4096/2D5337CD04EA4233
     created: 2020-02-03  expires: 2021-02-02  usage: S
[ultimate] (1). test1 (test) <test@test.com>
```

# 修改密码

为了提高密码的安全性，我们过一段时间需要改一次密码，同一个密码一直使用会有安全隐患。这节我们来看看怎么改密码。

```bash
➜  ~ gpg --edit-key test1
gpg (GnuPG) 2.2.4; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa4096/C7BB725A82D22F2F
created: 2020-02-03  expires: never       usage: SC
trust: ultimate      validity: ultimate
ssb  rsa4096/C5F257E429558F85
created: 2020-02-03  expires: never       usage: E
ssb  rsa4096/2D5337CD04EA4233
created: 2020-02-03  expires: 2021-02-02  usage: S
[ultimate] (1). test1 (test) <test@test.com>

gpg> passwd
```

紧接着输入旧密码，然后再输入新密码，这样就改好了。

# 更改过期时间

我们使用`GPG`，用的很开心，有一天`GPG`提示你有一个密钥快过期了。这时候你可以选择创建一个新的子密钥，或者把这个密钥的过期时间延长。

创建子密钥我们之前学过，现在来看看怎么把子密钥的过期时间延长。

```bash
➜  ~ gpg --edit-key test1
gpg (GnuPG) 2.2.4; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa4096/C7BB725A82D22F2F
     created: 2020-02-03  expires: 2022-02-03  usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa4096/C5F257E429558F85
     created: 2020-02-03  expires: never       usage: E
ssb  rsa4096/2D5337CD04EA4233
     created: 2020-02-03  expires: 2021-02-02  usage: S
[ultimate] (1). test1 (test) <test@test.com>

# 选择第二个key，选中后这个key后面会有一个 *
gpg> key 2

sec  rsa4096/C7BB725A82D22F2F
     created: 2020-02-03  expires: 2022-02-03  usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa4096/C5F257E429558F85
     created: 2020-02-03  expires: never       usage: E
ssb* rsa4096/2D5337CD04EA4233
     created: 2020-02-03  expires: 2021-02-02  usage: S
[ultimate] (1). test1 (test) <test@test.com>

gpg> expire
Changing expiration time for a subkey.
Please specify how long the key should be valid.
0 = key does not expire
<n>  = key expires in n days
<n>w = key expires in n weeks
<n>m = key expires in n months
<n>y = key expires in n years
Key is valid for? (0) 2y # 改为 2 年
Key expires at Thu 03 Feb 2022 03:57:10 PM CST
Is this correct? (y/N) y

sec  rsa4096/C7BB725A82D22F2F
     created: 2020-02-03  expires: 2022-02-03  usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa4096/C5F257E429558F85
     created: 2020-02-03  expires: never       usage: E
ssb* rsa4096/2D5337CD04EA4233
     created: 2020-02-03  expires: 2022-02-03  usage: S
[ultimate] (1). test1 (test) <test@test.com>

gpg> save
```

在上面的操作中，我们把`2D5337CD04EA4233`的过期时间从`2021-02-02`改为了`2020-02-03`。

# 签名验证

当我们在导入别人的公钥的时候，我们使用这个公钥进行验证签名时会得到一个警告，说本地密钥库不信任该公钥。如下所示

```bash
➜  ~ gpg --verify test.txt.sig
gpg: assuming signed data in'test.txt'
gpg: Signature made Tue 04 Feb 2020 12:22:22 PM CST
gpg:                using RSA key 88F1EAD03818FBC41B8241E63B15A852EB0A8251
gpg:                issuer "test2@test.com"
gpg: Good signature from "test2 (test2) <test2@testcom>" [unknown]
# 警告
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: A7DB 6BFF 3543 FCFD 6D06  87DA 7092 DDB1 4E3B A740
Subkey fingerprint: 88F1 EAD0 3818 FBC4 1B82  41E6 3B15 A852 EB0A 8251
```

为了避免安全隐患，在使用别人的公钥之前，我们需要用我们自己的密钥对别人的公钥进行签名认证，这样才能确保这个公钥确实是某个人的，而不是其他人篡改过的。在进行签名的时候，要向他人确认这个公钥的指纹是不是真的是他的，如果是才能给这个公钥签名。

```bash
➜  ~ gpg --edit-key test2
gpg> sign

pub  rsa4096/7092DDB14E3BA740
created: 2020-01-06  expires: never       usage: SC
trust: unknown       validity: unknown
Primary key fingerprint: A7DB 6BFF 3543 FCFD 6D06  87DA 7092 DDB1 4E3B A740

test2 (test2) <test2@test.com>

Are you sure that you want to sign this key with your
key "test1 (test) <test@test.com>" (C7BB725A82D22F2F)

Really sign? (y/N) y

gpg> save
➜  ~ gpg --verify test.txt.sig
gpg: assuming signed data in'test.txt'
gpg: Signature made Tue 04 Feb 2020 12:22:22 PM CST
gpg:                using RSA key 88F1EAD03818FBC41B8241E63B15A852EB0A8251
gpg:                issuer "test2@test.com"
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   1  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: depth: 1  valid:   1  signed:   0  trust: 1-, 0q, 0n, 0m, 0f, 0u
gpg: Good signature from "test2 (test) <test2@test.com>" [full]
```

现在警告消失了。

这里有一点要注意，签名是使用私钥签的。因为你的公钥是公开的，所以别人有你的公钥，如果使用公钥签名的话，任何人只要有你的公钥都可以代替你签名，签名也就没有意义了。

# 信任密钥

同样的当我们把密钥从一台电脑导出，然后从另一台电脑导入，在对文件进行加密时，也是会碰到不确定这个密钥是否属于本人的警告。

```bash
➜  ~ gpg -a -o trust.txt -r test1 -e test.txt
gpg: C5F257E429558F85: There is no assurance this key belongs to the named user
sub  rsa4096/C5F257E429558F85 2020-02-03 test1 (test) <test@test.com>
Primary key fingerprint: B7C6 2E9D 96B0 977D EDFD  E399 C7BB 725A 82D2 2F2F
Subkey fingerprint: B923 473E 2D23 9E97 BF4C  AA31 C5F2 57E4 2955 8F85

It is NOT certain that the key belongs to the person named
in the user ID.  If you *really* know what you are doing,
   you may answer the next question with yes.

   Use this key anyway? (y/N) y
```

为了消除这个警告，我们需要对导入的密钥进行信任

```bash
➜  ~ gpg --edit-key test1
gpg (GnuPG) 2.2.4; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa4096/C7BB725A82D22F2F
     created: 2020-02-03  expires: never       usage: SC
     trust: unknown       validity: unknown
ssb  rsa4096/C5F257E429558F85
     created: 2020-02-03  expires: never       usage: E
ssb  rsa4096/2D5337CD04EA4233
     created: 2020-02-03  expires: 2021-02-02  usage: S
[ unknown] (1). test1 (test) <test@test.com>
gpg> trust
sec  rsa4096/C7BB725A82D22F2F
     created: 2020-02-03  expires: never       usage: SC
     trust: unknown       validity: unknown
ssb  rsa4096/C5F257E429558F85
     created: 2020-02-03  expires: never       usage: E
ssb  rsa4096/2D5337CD04EA4233
     created: 2020-02-03  expires: 2021-02-02  usage: S
[ unknown] (1). test1 (test) <test@test.com>

Please decide how far you trust this user to correctly verify other users keys
(by looking at passports, checking fingerprints from different sources, etc.)

  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu
# 可以选择 1-5，信任级别从低到高 
Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y

sec  rsa4096/C7BB725A82D22F2F
     created: 2020-02-03  expires: never       usage: SC
     trust: ultimate      validity: unknown
ssb  rsa4096/C5F257E429558F85
     created: 2020-02-03  expires: never       usage: E
ssb  rsa4096/2D5337CD04EA4233
     created: 2020-02-03  expires: 2021-02-02  usage: S
[ unknown] (1). test1 (test) <test@test.com>
Please note that the shown key validity is not necessarily correct
unless you restart the program.

gpg> save
Key not changed so no update needed.
```

当我们再进行加密时，这个警告就消失了。

# 发布公钥到服务器

我们和别人交换公钥通常会把公钥直接发给对方，并告诉他指纹，让他导入之后进行验证，这样我们之间是通过社交来进行验证的。不需要第三方的认证机构来进行先认证。

除了上面这种方式还可以把公钥发送到公钥服务器，比如MIT PGP Key ServerOpenPGP Keyserver等，这样别人就可以通过这个服务器下载到你的公钥。导入之后最好还是需要进行验证一下指纹，确保安全。

```bash
➜  ~ gpg --send-keys test1 --keyserver pgp.mit.edu
```

参考资料：

[1]https://zhuanlan.zhihu.com/p/21267738



本文来源于 微信公众号  "devbins"  所分享内容