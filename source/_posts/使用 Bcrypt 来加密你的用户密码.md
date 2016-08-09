title: 使用 Bcrypt 来加密你的用户密码
date: 2016-08-09 16:16:20
permalink: use-Bcrypt
category:
tags:
---

Bcrypt 是一个用于密码哈希的加密算法，它基于 Blowfish 加密算法。因其具有非常好的安全性和可用性，它得到越来越多应用的使用

<!--more-->

为了用户密码的安全，一般情况下用户表的密码都是使用不可逆算法加密后存储的。常规的方法如下：

* MD5 加密
* SHA 安全哈希算法
* SHA 加盐（将密码和一个随机字符串混合后再使用 SHA 加密）
* 多次 SHA 加密并且加盐
* ……

下面来简单说一下每一种方法

* 第一种，MD5 算法已经被证实不再安全，如果你的口令长度只有小写字母再加上数字，假设口令的长度是 6 位，那么在目前一台比较新一点的 PC 机上，穷举所有的口令只需要40秒钟。而据我们了解，几乎有 90% 以上的用户只用小写字母和数字来组织其口令，对于 6 位长度的密码只需要最多 40 秒就可以破解了。所以不推荐使用
* 第二种，如果只是使用 SHA 算法将密码加密，其安全性依然不高，使用简单的暴力破解的方式依然可以猜出来密码
* 第三、四种，安全性高，不过操作繁琐并且维护盐的值是一个麻烦事

## Bcrypt 简介
相比于常规的不可逆加密方法，Bcrypt 使用起来要简单得多，它的主要特点：

* 安全：每个密码的盐值都是随机的，并且计算过程经过多次迭代
* 使用简单：Bcrypt 把算法版本、计算次数和 salt（盐值）都放到 hash 值里面去了，所以不用再单独维护盐值了

当然，安全的代价是性能

对于计算机来说，Bcrypt 的计算速度很慢，但是对于用户来说，这个过程不算慢。对于那些想要穷举用户口令的人来说，这会让那些计算机变得如同蜗牛一样。

如果和 MD5 一起来比较的话，加密“cool”的话，Bcrypt 需要 0.3 秒，而 MD5 只需要一微秒（百万分之一秒）。也就是说，前面我们说的那个只需要 40 秒就可以穷举完所有的可能的 MD5 编码的口令的算法，在使用 Bcrypt 下，需要 12 年。


## 使用 Python 的示例
首先安装 `bcrypt ` 这个库

```bash
$ pip install bcrypt
```

快速开始

```python
>>> import bcrypt
>>> password = "mypassword"
>>> # 用一个随机的盐值来加密密码
>>> # bcrypt.gensalt() 还可以接受一个参数来控制它要计算多少次，默认是 12
>>> hashed = bcrypt.hashpw(password, bcrypt.gensalt())
>>> # 下面这个 hashed 就是加密后的值
>>> hashed
'$2b$12$nf3TdEFJZ6AtRA7N9r1f.OANfpFS9Qxtklo4km.LHbuEV7Mu5jJN2'

>>> # 验证明文密码是不是加密后的值
>>> if bcrypt.checkpw(password, hashed):
...     print("It Matches!")
... else:
...     print("It Does not Match :(")
```

更多文档：[https://github.com/pyca/bcrypt](https://github.com/pyca/bcrypt)

## 最后
如果使用不安全的密码，什么都帮不上忙
