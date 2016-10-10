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

1. [字符串编辑距离](#levenshtein)
2. [交替字符串](#replacestr)

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
