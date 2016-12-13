---
layout: post
title: Mac安装老版本的Thrift 
categories: Thrift
tags:
- Thrift
- Mac 
description: Mac安装老版本的Thrift 
---

Thrift是一个常用的RPC框架，现要安装比较老的0.8.0版本。

版本0.8.0的下载地址：

```
http://archive.apache.org/dist/thrift/0.8.0/
```

在安装过程中，在执行./configure时，一直报错。

但是把不用的语言去掉就可以通过：

```
./configure -without-csharp --without-erlang --without-go --without-haskell --without-ruby --without-cpp --without-perl --without-php
```

然后：

```
make && sudo make install
```

执行：

```
thrift -version
```

显示：

```
Thrift version 0.8.0
```

安装成功。
