---
title: ymoi集训-矩阵乘法
date: 2020-08-18 18:28:29
tags:
  - ymoi
  - 矩阵
  - 线性代数
category: 算法竞赛
---

# 基本概念

一个$n \times m$的矩阵可以被看做一个$n$行$m$列的数阵,一般用一个大写字母表示：
$$
A=\begin{bmatrix}1&1&4\\ 5&1&4\end{bmatrix}, B=\begin{bmatrix}1&0\\ 9&8\\ 2&1\\ 6&7\end{bmatrix}
$$
矩阵之间可以进行加减乘除运算，下面研究矩阵的乘法运算。

对于两个矩阵$A$和$B$，只有$A$的行数等于$B$的列数时，才能进行乘法。设A的大小$n \times m$，$B$的大小为$m \times p$为乘法的定义如下：
$$
C_{i,j}=\sum_{k=1}^mA_{i,k}\cdot B_{k,j}(1\leq i \leq n,1\leq j \leq p)
$$
时间复杂度为$O(n^3)$（假设$n$, $m$, $p$同阶）。

矩阵乘法满足结合律，因此我们可以……

# 矩阵快速幂

和实数的快速幂求法完全一样：

```C++
int ksm(int a,int b){
    int ans = 1;
    for(;b;a=(a*a)%MOD,b>>=1){
        if(b&1)ans=(ans*a)%MOD;
    }
    return ans;
}
```

只不过把初值赋成**单位矩阵**即可。

若存在$e\in S$使$\forall x\in S$与$e$进行某种运算后得到这个元素本身，则$e$被称为单位元。

在$\R$中, $1$即为乘法法则下的单位元。

单位矩阵是主对角线为$1$，其余均为$0$的矩阵：
$$
I=\begin{bmatrix}1&0&\cdots&0\\ 0&1&\cdots&0\\ \vdots&\vdots&\ddots&\vdots\\ 0&0&\cdots&1\end{bmatrix}
$$
所以把初值赋为单位矩阵即可。

模板：[matrix_mul.cpp](https://github.com/CrSjimo/brush-problem/blob/master/templates/matrix_mul.cpp)

# 例题

## 1

### 题目

斐波那契数列:$f_1=f_2=1,f_n=f_{n-1}+f_{n-2}(n>2)$

求 $f_n \mod 10^9+7$

$n\leq10^{18}$

### 解法

易证明：
$$
\begin{bmatrix}f_{n-2}&f_{n-1}\end{bmatrix}\cdot\begin{bmatrix}0&1\\ 1&1\end{bmatrix}=\begin{bmatrix}f_{n-1}&f_{n}\end{bmatrix}
$$
其中，将$\begin{bmatrix}0&1\\ 1&1\end{bmatrix}$称为**转移矩阵**，记作$B$。

则有：
$$
\begin{bmatrix}f_{n-1}&f_{n}\end{bmatrix}=\begin{bmatrix}f_{1}&f_{2}\end{bmatrix}\cdot B^{n-2}
$$
~~就是这么神奇。~~

其他的问题解法大致相同，推导转移矩阵就行。~~（但是要怎么推导呢？）~~

## 2

### 题目

定义$fr$数列$F$,$F_1=F_2=1,F_n=F_{n-1}+F_{n-2}+n$

求 $f_n \mod 10^9+7$

$n\leq10^{18}$

### 解法

状态矩阵：$\begin{bmatrix}f_{n-2}&f_{n-1}&1&n-1\end{bmatrix}$

转移矩阵：$\begin{bmatrix}0&1&0&0\\ 1&1&0&0\\ 0&1&1&1\\ 0&1&0&1\end{bmatrix}$

## 3

### 题目

给出一个$n$个结点的无向图, 询问从$S$走到$T$经过$k$条边的路径有多少条。

### 解法

邻接矩阵$G_{u,v}$表示从$u$到$v$经过一条边的路径数，根据矩阵乘法的定义不难看出：$G^k_{u,v}$表示从$u$到$v$经过$k$条边的路径数，

## 4

### 题目

给定一张$T$条边的无向连通图，求从$u$到$v$经过$k$条边的最短路长度。

### 解法

定义一个矩阵新运算：
$$
C_{i,j}=\min_{k=1\rightarrow m}A_{i,k}\cdot B_{k,j}(1\leq i \leq n,1\leq j \leq p)
$$
这个运算类似于矩阵乘法，且满足结合律，可以使用矩阵快速幂。求法与例题3类似。
