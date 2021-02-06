---
title: "GPG入门二"
date: 2021-02-06T18:37:34+08:00
lastmod: 2021-02-06T18:37:34+08:00
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

上一篇文章我们了解了怎么使用 `GPG` 进行加密、解密、签名。

这篇来讲讲密钥的导入导出以及子密钥的使用。

# 主钥与子钥

我们使用 `GPG` 的目的就是为了保证一些重要的隐私文件不会被别人轻松获取到文件的内容，所以要确保在使用 `GPG` 的过程中， `GPG` 本身不会成为一个隐患。

`GPG` 一个好的做法是在一台不能联网的机器上创建好密钥，并且把私钥保存好放在一个非常安全的地方。

之后我们需要创建子密钥，子密钥用来进行加密或者签名。而主密钥仅仅用来管理子密钥，注意只有主私钥才能创建新的子密钥。这样做的好处就是，一旦子密钥泄露了，我们只需要撤销子密钥就行了，而不会影响主密钥。

# 查看

在创建子钥之前我们先来看看已有的公钥和私钥

```bash
# 查看公钥
➜  ~ gpg -k
/home/test/.gnupg/pubring.kbx
-----------------------------
pub   rsa2048 2020-02-02 [SC] [expires: 2022-02-01]
      B7C62E9D96B0977DEDFDE399C7BB725A82D22F2F
uid           [ultimate] test1 <test1@test.com>
sub   rsa2048 2020-02-02 [E] [expires: 2022-02-01]

# 查看私钥 K 是大写
➜  ~ gpg -K
/home/test/.gnupg/pubring.kbx
-----------------------------
pub   rsa2048 2020-02-02 [SC] [expires: 2022-02-01]
      B7C62E9D96B0977DEDFDE399C7BB725A82D22F2F
uid           [ultimate] test1 <test1@test.com>
ssb   rsa2048 2020-02-02 [E] [expires: 2022-02-01]
```

从上面可以看到，我们在生成密钥的时候，默认就会帮我们生成一对子钥。默认是用来加密的。

公钥和私钥是成对存在的。

在上面的输出中有一些符号具有特殊含义，我们来看一下

| 名词 | 解释                             |
| :--- | :------------------------------- |
| uid  | 拥有者信息，姓名和邮箱           |
| pub  | public key 公钥                  |
| sub  | public subkey 子公钥             |
| sec  | secret key 私钥                  |
| ssb  | secret subkey 子私钥             |
| S    | sign 表示可以用来签名            |
| C    | Certify 表示可以用于认证其他密钥 |
| E    | Encrypt 表示可以用来加密         |
| A    | Authentication 表示可以用来认证  |

# 创建子钥

我们已经有了一个加密的 `subkey` ，接下来我们来创建一个用于签名的 `subkey`

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
[ultimate] (1). test1 (test) <test@test.com>

# 添加 subkey
gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
# 使用 RSA 算法
Your selection? 4
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
         <n>  = key expires in n days
         <n>w = key expires in n weeks
         <n>m = key expires in n months
         <n>y = key expires in n years
# 有效期为 1 年
Key is valid for? (0) 1y
Key expires at Tue 02 Feb 2021 02:20:17 PM CST
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
                   disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  rsa4096/C7BB725A82D22F2F
     created: 2020-02-03  expires: never       usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa4096/C5F257E429558F85
     created: 2020-02-03  expires: never       usage: E
ssb  rsa4096/2D5337CD04EA4233
     created: 2020-02-03  expires: 2021-02-02  usage: S
[ultimate] (1). test1 (test) <test@test.com>

gpg> q
Save changes? (y/N) y
```

# 导出

## 导出公钥

在进行网络传输之前，我们用别人的公钥进行加密，他拿到加密文件之后就可以用私钥进行解密。

如果别人想要给我们发送一个加密文件的话，我们就需要把我们的公钥给他，所以我们需要导出公钥。

```bash
➜  ~ gpg -a --export test1
-----BEGIN PGP PUBLIC KEY BLOCK-----

mQINBF43lckBEAC5ocC6xzvvI8Djetp/UnotdfVkXlLFSTCf6RlaNOPV1UcKPp2H
N0R4UYqLQbTjMsk59kmDEz+xJNbDFWygTSNjK9B46SjOy7QMSl5r+jJMrvPkz7mV
...
r4N5jIjDOP3nWVaVhJiD99KG6ZBVbIkZC3+w+ioF/U1GhlU3vfvvJa+QKiLyTj10
Vd4ImRX3fFquDwqyUQlxNLS9rdhUdVQndvQCmGq18tsdCZ3ZDI+/tZeEhTm86EGL
Mp+VgpXO5w5Yjpb30iT0eGpGOaElif0Vod4ogto=
=8Qzo
-----END PGP PUBLIC KEY BLOCK-----
```

这样的方式会把公钥直接输出到控制台，如果我们想导出到文件中可以添加 `-o` 参数来导出到 `pub.txt` 中。

```bash
➜  ~ gpg -o pub.txt -a --export test1
```

导出特定的公钥可以使用 `gpg -o pubsig.txt -a --export 2D5337CD04EA4233!` 末尾的 `!` 不要忘记。

## 导出私钥

为了安全起见，我们的主私钥应该存放在安全的地方，其他时候应该使用子私密钥。所以我们需要把私钥也导出，然后删掉本地的私钥。导出的私钥包含了公钥

```bash
➜  ~ gpg -o sec.txt -a --export-secret-keys test1
cat sec.txt
-----BEGIN PGP PRIVATE KEY BLOCK-----

lQdGBF43lckBEAC5ocC6xzvvI8Djetp/UnotdfVkXlLFSTCf6RlaNOPV1UcKPp2H
N0R4UYqLQbTjMsk59kmDEz+xJNbDFWygTSNjK9B46SjOy7QMSl5r+jJMrvPkz7mV
...
dVQndvQCmGq18tsdCZ3ZDI+/tZeEhTm86EGLMp+VgpXO5w5Yjpb30iT0eGpGOaEl
if0Vod4ogto=
=FBLL
-----END PGP PRIVATE KEY BLOCK-----
```

## 导出子私钥

```bash
➜  ~ gpg -o subkeys.txt -a --export-secret-subkeys test1
cat subkeys.txt
-----BEGIN PGP PRIVATE KEY BLOCK-----

lQIVBF43lckBEAC5ocC6xzvvI8Djetp/UnotdfVkXlLFSTCf6RlaNOPV1UcKPp2H
N0R4UYqLQbTjMsk59kmDEz+xJNbDFWygTSNjK9B46SjOy7QMSl5r+jJMrvPkz7mV
...
fFquDwqyUQlxNLS9rdhUdVQndvQCmGq18tsdCZ3ZDI+/tZeEhTm86EGLMp+VgpXO
5w5Yjpb30iT0eGpGOaElif0Vod4ogto=
=Bb/l
-----END PGP PRIVATE KEY BLOCK-----
```

上面这种方式会把所有的子私钥都导出，如果想导出特定的可以用如下方法。

```bash
# 导出子私密钥 ID 最后面有一个 ! 不要忘记
➜  ~ gpg -o secsig.txt -a --export-secret-subkey 2D5337CD04EA4233!
cat secsig.txt
-----BEGIN PGP PRIVATE KEY BLOCK-----

lQIVBF43lckBEAC5ocC6xzvvI8Djetp/UnotdfVkXlLFSTCf6RlaNOPV1UcKPp2H
...
l4SFObzoQYsyn5WClc7nDliOlvfSJPR4akY5oSWJ/RWh3iiC2g==
=dKB1
-----END PGP PRIVATE KEY BLOCK-----
```

# 导入

## 导入公钥

```bash
➜  ~ gpg --import pub.txt
gpg: directory '/home/test/.gnupg' created
gpg: keybox '/home/test/.gnupg/pubring.kbx' created
gpg: /home/ubuntu/.gnupg/trustdb.gpg: trustdb created
gpg: key C7BB725A82D22F2F: public key "test1 (test) <test@test.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
# 查看公钥
➜  ~ gpg -k
/home/ubuntu/.gnupg/pubring.kbx
-------------------------------
pub   rsa4096 2020-02-03 [SC]
B7C62E9D96B0977DEDFDE399C7BB725A82D22F2F
uid           [ unknown] test1 (test) <test@test.com>
sub   rsa4096 2020-02-03 [E]
sub   rsa4096 2020-02-03 [S] [expires: 2021-02-02]
# 查看私钥
➜  ~ gpg -K
# 没有输出
```

可以看到只有公钥导进来了，私钥没有被导进来

## 导入私钥

```bash
➜  ~ gpg --import secsig.txt
gpg: key C7BB725A82D22F2F: "test1 (test) <test@test.com>" not changed
gpg: To migrate 'secring.gpg', with each smartcard, run: gpg --card-status
gpg: key C7BB725A82D22F2F: secret key imported
gpg: Total number processed: 1
gpg:              unchanged: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1
# 查看私钥
➜  ~ gpg -K
/home/ubuntu/.gnupg/pubring.kbx
-------------------------------
sec#  rsa4096 2020-02-03 [SC]
B7C62E9D96B0977DEDFDE399C7BB725A82D22F2F
uid           [ unknown] test1 (test) <test@test.com>
ssb#  rsa4096 2020-02-03 [E]
ssb   rsa4096 2020-02-03 [S] [expires: 2021-02-02]
```

这次我们导入了签名用的子私密钥，所以可以查看到。另外在主私密钥前面有一个 `#` 这个表示这个密钥不在本地，也就是不存在。接下来我们把主密钥也导进来。

```bash
➜  ~ gpg --import sec.txt
gpg: key C7BB725A82D22F2F: "test1 (test) <test@test.com>" not changed
gpg: key C7BB725A82D22F2F: secret key imported
gpg: Total number processed: 1
gpg:              unchanged: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1
gpg:  secret keys unchanged: 1
# 查看私钥
➜  ~ gpg -K
/home/ubuntu/.gnupg/pubring.kbx
-------------------------------
sec   rsa4096 2020-02-03 [SC]
B7C62E9D96B0977DEDFDE399C7BB725A82D22F2F
uid           [ unknown] test1 (test) <test@test.com>
ssb   rsa4096 2020-02-03 [E]
ssb   rsa4096 2020-02-03 [S] [expires: 2021-02-02]
```

这次就能看到主私密钥被导入进来了，前面的 `#` 没有了。

# 删除

## 删除公钥

```bash
➜  ~ gpg --delete-key test1
gpg (GnuPG) 2.2.4; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

pub  rsa4096/C7BB725A82D22F2F 2020-02-03 test1 (test) <test@test.com>

Delete this key from the keyring? (y/N) y
```

这样会把主公钥删除，要想删除所有的可以使用 `gpg --delete-keys test1`

## 删除私钥

```bash
➜  ~ gpg --delete-secret-key test1
gpg (GnuPG) 2.2.4; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.


sec  rsa4096/C7BB725A82D22F2F 2020-02-03 test1 (test) <test@test.com>

Delete this key from the keyring? (y/N) y
This is a secret key! - really delete? (y/N) y
```

同样的这样只会把主私钥删除，要想删除所有的可以使用 `gpg --delete-secret-keys test1`

# 为什么不用私钥加密

因为公钥是公开的，所有人都可以知道，用私钥加密后，公钥可以解密。这样等于大家都知道了，加密也就没有意义了。



本文来源于 微信公众号  "devbins"  所分享内容