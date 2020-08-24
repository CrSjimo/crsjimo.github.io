---
title: ymoi集训-树状数组
date: 2020-08-24 19:15:06
tags:
  - ymoi
  - 树状数组
---

# 树状数组能解决什么问题

树状数组能解决的问题，线段树都能解决。

那么为什么我们需要树状数组呢？

因为树状数组代码段，常数低，空间更小。

~~$SJB$今天问我corn6的B题（一道线段树+二分题）是怎么写出来5.1KiB的代码的。~~

# LOWBIT

~~这个小学二年级就学过了。~~

$$
lowbit(x)=x\&-x
$$

# 单点修改，区间查询

~~这个小学二年级就学过了。~~

# 区间修改，单点查询

用树状数组维护差分数组。

所谓差分数组，就是：

$$
\left\{
    \begin{array}{lr}
    b_1=a_1\\
     b_i=a_i-a_{i-1},2\leq i \leq |a|
    \end{array}
\right.
$$

其中$b$就是$a$的差分数组。

不难发现，$a$数组是$b$数组的前缀和。

所以，对于区间$[l,r]$修改的操作为：

- $update(l,x)$
- $update(r+1,-x)$

单点查询操作为：

- $query(i)$

其中的查询修改操作与单点修改区间查询相同。

# 区间修改，区间求和

$$
\begin{aligned}
&\sum_{i=1}^{r} a_i\\=&\sum_{i=1}^r\sum_{j=1}^i b_j\\=&\sum_{i=1}^r b_i\times(r-i+1)
\\=&(r+1) \times \sum_{i=1}^r b_i -\sum_{i=1}^r b_i\times i
\end{aligned}
$$

~~上边这段公式也是从课件里复制过来的。~~

所以我们要用两个数组维护$\sum b_i$和$\sum i \times b_i$。

核心代码如下：

```C++
void update(int x,int val){
    for(int i=x;i<=n;i+=lowbit(i)){
        f1[i]+=val;
        f2[i]+=1ll*val*x;
    }
}

ll query(int x){
    ll ans=0;
    for(int i=x;i>0;i-=lowbit(i)){
        ans+=f1[i]*(x+1)-f2[i];
    }
    return ans; 
}
```

# 二维树状数组

~~树套树不就好了。~~

```C++
void update(int x,int y,int val){
    for(int i=x;i<=n;i+=lowbit(i)){
        for(int j=y;j<=m;j+=lowbit(j)){
            f[i][j]+=val;
        }
    }
}

ll query(int x,int y){
    ll ans=0;
    for(int i=x;i>0;i-=lowbit(i)){
        for(int j=y;j>0;j-=lowbit(j)){
            ans+=f[i][j];
        }
    }
    return ans;
}
```

# 树状数组上二分

类似于线段树上二分，树状数组上也可以进行二分。

使用**树状数组上二分**（可能需要预先离散化），可以$\mathcal{O}(\log n)$查询第$k$小/大元素，实现类似**平衡树**的效果。

主要思想是：按二进制位从高到低逐渐二分出答案。

图示如下：

![Diagram](https://pic.downk.cc/item/5f43b16b160a154a672ba327.png)

代码（从课件里复制来的）：

```cpp
int kth(int k) {
    int r = 0, t;
    for (int i = LOG_N; i>=0; --i) {
        t = r | 1 << i;     
        if (t <= n && d[t] < k)k -= d[t], r = t;
    }
    return r + 1;
}
```