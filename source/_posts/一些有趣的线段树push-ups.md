---
title: 一些有趣的线段树push-ups
date: 2020-08-17 18:33:28
tags: 
  - ymoi
  - 线段树
category: 算法竞赛
---

# 1

## 题目

$n$个数$a_{1\cdots n}$排成一列,支持两种操作:
`1 x y k` 将$a_{x\cdots y}$加上$k$
`2 x y` 查询$\sum_{i=x}^{y}a_i^2$

$n, q\leq1\times10^5$

## 操作

维护区间和，区间平方和。

如果把$a_{x\cdots y}$中的每一个数加上$k$的话，$sum(x,y)'=sum(x,y)+k\times(y-x+1)$，$sum^2(x,y)'=sum^2(x,y)+2sum(x,y)'+(y-x+1)k^2$

# 2

## 题目

$n$个数$a_{1\cdots n}$排成一列,支持两种操作:
`1 x y k` 将$a_{x\cdots y}$加上$k$
`2 x y` 查询$\sum_{i=x}^{y}\sin a_i$

$n, q\leq1\times10^5$

## 操作

~~高中数学书告诉我们：~~$\sin(x+y)=\sin x\cdot\cos y+\sin y\cdot\cos x$

维护区间正弦和，余弦和

$S(x,y)'=S(x,y)\cdot\cos k+C(x,y)\cdot\sin k$

$C(x,y)'=C(x,y)\cdot\cos k-S(x,y)\cdot\sin k$

# 3

## 题目

有$n$个Gate,每个Gate拥有符号`|` `&` `^`中的一种,并写有数字$k_i$
当一个数字通过Gate时,即会发生Gate上写有的符号 $k_i$的运算

要求支持两种操作:
1 修改某个Gate
2 询问数$x$依次通过所有Gate后得到的数是多少

$n, q\leq1\times10^5$


## 操作

按二进制位考虑。

对于每一个区间，记录第$i$位为$0$或$1$时，运算后会变为什么。

对于第$i$位：

$o(i,0)=rs(i,ls(i,0))$

$o(i,1)=rs(i,ls(i,1))$

# 4

## 题目

有n个函数$f_{1\cdots n}$, 初始$f_i(x)=k_ix+b_i$, 支持两种操作:

`1 i k b` 将$f_i(x)$修改为$kx+b$
`2 l r x` 求$f_r(f_{r-1}(\cdots f_{l+1}(f_l(x))\cdots))$  

## 操作

对于线段树上的结点,若其左子树为函数$f(x)=ax+b$, 右子树为函数$g(x)=cx+d$

则该结点的函数为$h(x)=g(f(x))=c(ax+b)+d=(ac)x+(bc+d)$

# 5

## 题目

$n$个数的序列,支持两种操作: 
`1 x y` 令$a_x=y$  
`2 l r k` 询问在区间$[l,r]$中选取$k$个数的期望乘积

$n, q\leq1\times10^5, k\leq 10$

## 操作

~~啊，期望~~

用$Ans_k$表示选择$k$个数的期望乘积。

先枚举选的个数$i$，再枚举左子树选的个数$j$

`Ans[i]=l_Ans[0]*r_Ans[i]+...+l_Ans[j]*
r_Ans[i-j]+...+l_Ans[i]*r_Ans[0]`

~~不过暂时还没搞懂为什么~~
