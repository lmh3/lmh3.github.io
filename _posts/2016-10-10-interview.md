---
layout: post
title: 面试题 
categories: 面试 
tags:
- 数据结构
- 算法 
description: 面试题 
---

### 二叉树

#### 基础

1. 判断二叉树是否平衡二叉树
2. 求二叉树中相距最远的两个节点之间的距离
3. 二叉树的广度遍历、逐层打印二叉树节点数据、只打印某层节点数据
4. 计算二叉树高度的非递归实现
5. 求二叉树的镜像
6. 二叉树前序、中序、后序遍历的非递归实现

#### 中等

1. 将二叉查找树转为有序的双链表
2. 连接二叉树同一层上的结点
3. 指定二叉树，给定两节点求其最近共同父节点
4. 在二叉树中找出和（叶子到根节点路径上的所有节点的数据和）为指定值的所有路径。

### 算法题

1. [字符串编辑距离](https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/05.02.md)
2. [交替字符串](https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/05.04.md)
3. [字符串全排列](https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/01.06.md)


### 数据库

1. 索引有哪些分类？创建索引因注意哪些问题？
2. join有何算法？如何实现的？
3. 事务有哪些隔离级别？如何防止幻读？
4. 为什么InnoDB中表的主键最好要自增？
5. datetime、timestamp的区别，如何存储IP地址，如何进行翻页？核心概念
6. ACID，以及如何确保ACID ,数据库隔离级别，以及每种隔离实现原理；
7. 常见存储（sas、ssd、flash、fusion）的理解，并且mysql读写在不同存储的性能对比情况；
8. 描述你所知道的的mysql的存储引擎，以及之间的差异性
9. mysql锁分类，及锁的实现方式
10. SQL执行计划理解、执行计划三要素、索引的理解、为啥索引是b+tree而不是平衡二叉树？

### 操作系统

1. 进程和线程有何区别？
2. 进程间通信有何方法？
3. 寄存器和主存如何同步？会造成开发中的什么问题？
4. 进程调度，有哪些状态，调度算法
5. 死锁

### 计算机网络

1. http/tcp/ip属于网络哪一层？为何要分层？
2. http get post有何区别？为何要这样设计？各在什么场景下使用？
3. http协议如何表示请求成功？如何控制客户端行为？如何表示请求完成？
4. 描述一下一次HTTP请求从请求到返回的过程，越详细越深入越好？

### Linux能力

1. Swap的原理（相关参数）
2. 使用shell输出max、min、avg值
3. 查看CPU、内存、硬盘、网络
4. kill -9、cpu load的作用
5. 磁盘满了找出最大的文件
6. awk sed


### 实现实例

<span id = "levenshtein">字符串编辑距离：</span>

```
int EditDistance(char *pSource, char *pTarget)
{
    int srcLength = strlen(pSource);
    int targetLength = strlen(pTarget);
    int i, j;
    //dp[i][j]表示源串source[0-i)和目标串target[0-j)的编辑距离
    //边界dp[i][0] = i，dp[0][j] = j  
    for (i = 1; i <= srcLength; ++i)
    {
        dp[i][0] = i;
    }
    for (j = 1; j <= targetLength; ++j)
    {
        dp[0][j] = j;
    }
    for (i = 1; i <= srcLength; ++i)
    {
        for (j = 1; j <= targetLength; ++j)
        {
            if (pSource[i - 1] == pTarget[j - 1])
            {
                dp[i][j] = dp[i - 1][j - 1];
            }
            else
            {
                dp[i][j] = 1 + min(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    return dp[srcLength][targetLength];
}
```

<span id = "replacestr">交替字符串：</span>

```
public boolean IsInterleave(String s1, String 2, String 3){
    int n = s1.length(), m = s2.length(), s = s3.length();

    //如果长度不一致，则s3不可能由s1和s2交错组成
    if (n + m != s)
        return false;

    boolean[][]dp = new boolean[n + 1][m + 1];

    //在初始化边界时，我们认为空串可以由空串组成，因此dp[0][0]赋值为true。
    dp[0][0] = true;

    for (int i = 0; i < n + 1; i++){
        for (int j = 0; j < m + 1; j++){
            if ( dp[i][j] || (i - 1 >= 0 && dp[i - 1][j] == true &&
                //取s1字符
                s1.charAT(i - 1) == s3.charAT(i + j - 1)) ||

                (j - 1 >= 0 && dp[i][j - 1] == true &&
                //取s2字符
                s2.charAT(j - 1) == s3.charAT(i + j - 1)) )

                dp[i][j] = true;
            else
                dp[i][j] = false;
        }
    }
    return dp[n][m]
}
```

