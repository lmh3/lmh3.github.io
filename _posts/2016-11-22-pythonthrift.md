---
layout: post
title: Python client测试Thrift接口
categories: Thrift
tags:
- Thrift
- Python
description: Python client测试Thrift接口
---

#### 参考文档：
Apache Thrift官方的Python Tutorial: https://thrift.apache.org/tutorial/py

#### 安装Thrift:
公司使用的是0.8.0，这一点需要注意。

#### 生成python版本的目标代码

`thrift -r --gen py service.thrift`

执行完会生成一个gen-py的文件夹。

#### Python client

```
#coding=utf-8
import sys
import glob
sys.path.append('gen-py')
from service import *
 
from thrift import Thrift
from thrift.transport import TSocket
from thrift.transport import TTransport
from thrift.protocol import TBinaryProtocol
 
def test():
        # Make socket
        transport = TSocket.TSocket('172.18.46.202', 9003)
 
        # Buffering is critical. Raw sockets are very slow
        transport = TTransport.TFramedTransport(transport)
 
        # Wrap in a protocol
        protocol = TBinaryProtocol.TBinaryProtocol(transport)
 
        # Create a client to use the protocol encoder
        client = CustomerThriftService.Client(protocol)
 
        # Connect!
        transport.open()
 
        data = client.getBaseInfo(50000959)
        print data
 
        # Close!
        transport.close()
 
if __name__ == '__main__':
    test()
```

#### 执行Python client
`python client.py`
