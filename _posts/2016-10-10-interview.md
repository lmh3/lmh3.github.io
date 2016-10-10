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
7. [如何查看一个端口被什么进程占用？](#netstat)
8. [文件系统软连接和硬连接的区别](#link) 
9. [写脚本实现，可以用shell、perl等。在目录/tmp下找到100个以abc开头的文件，然后把这些文件的第一行保存到文件new中。](#findFile)



### 实现示例

<span id = "levenshtein">字符串编辑距离：</span>

{% highlight Java %}

public static int getLevenshteinDistance(String s, String t) {
    if (s == null || t == null) {
        throw new IllegalArgumentException("Strings must not be null");
    }
    int n = s.length(); // length of s
    int m = t.length(); // length of t

    if (n == 0) {
        return m;
    } else if (m == 0) {
        return n;
    }

    if (n > m) {
        // swap the input strings to consume less memory
        String tmp = s;
        s = t;
        t = tmp;
        n = m;
        m = t.length();
    }

    int p[] = new int[n+1]; //'previous' cost array, horizontally
    int d[] = new int[n+1]; // cost array, horizontally
    int _d[]; //placeholder to assist in swapping p and d

    // indexes into strings s and t
    int i; // iterates through s
    int j; // iterates through t

    char t_j; // jth character of t

    int cost; // cost

    for (i = 0; i<=n; i++) {
        p[i] = i;
    }

    for (j = 1; j<=m; j++) {
        t_j = t.charAt(j-1);
        d[0] = j;

        for (i=1; i<=n; i++) {
            cost = s.charAt(i-1)==t_j ? 0 : 1;
            // minimum of cell to the left+1, to the top+1, diagonally left and up +cost
            d[i] = Math.min(Math.min(d[i-1]+1, p[i]+1),  p[i-1]+cost);
        }

        // copy current distance counts to 'previous row' distance counts
        _d = p;
        p = d;
        d = _d;
    }

    // our last action in the above loop was to switch d and p, so p now
    // actually has the most recent cost counts
    return p[n];
}
{% endhighlight %}

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

<span id = "findFile">文件查找整合：</span>

```
参考答案1：
#!/bin/sh 
for filename in `find /tmp -type f -name "abc*"|head -n 100` ;do 
sed -n '1p' $filename>>new 
done
参考答案2：
find /tmp -type f -name “abc*” | head -n 100 | xargs head -q -n 1 >> new
```

<span id = "link">软硬链接区别：</span>

```
硬连接：硬连接指通过索引节点来进行连接, 类似文件别名。
  硬连接的作用是允许一个文件拥有多个有效路径名，删除源文件不影响硬连接
软连接：另外一种连接称之为符号连接，类似于快捷方程式，包含的有另一文
  件的位置信息，删除源文件软件连也无法访问了
```

<span id = "netstat">查看端口号：</span>


```
netstat -tunlp|grep 端口号
```