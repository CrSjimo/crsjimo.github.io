---
title: 切掉去年CSP-S2D1T2实录
date: 2020-09-26 18:50:03
category: 算法竞赛
tags:
- DP
- 树
---

# 前言

前几天在机房突发奇想想要研究研究去年的D1T2（曾经因为写崩而导致只混到了一个省二的一道题）[【洛谷P5658】](https://www.luogu.com.cn/problem/P5658)[【FAIOJ10191】](http://faioj.brynhild.online/problem/10191)，于是在早自习的时候手推了一下正确的递推式。

# 动态规划设计

首先从3条性质入手：

1. `()` 是合法括号串；
2. 如果 `A` 是合法括号串，则 `(A)` 是合法括号串。
3. 如果 `A`，`B` 是合法括号串，则 `AB` 是合法括号串。

首先看第三条性质，对于串`()`，有1个合法字串，如果在这个串更新为`()()`，则有3个合法字串，也就是说更新的部分对答案的贡献是2，同理`()()()`更新的部分对答案的贡献是3。为什么是这样呢？我们不妨用`()()`更新到`()()()`这个过程为例，新增加的字串是是以新增的合法字串结尾的所有合法字串。所以我们可以得到这样一个递推式：
$$
g[v]=g[top]+1
$$
其中，$top$是当前匹配到的合法字串左括号的前驱。这样，我们就得到了以当前位置结尾的所有合法字串的数量。

所以，当前位置的答案为：
$$
f[v]=f[u]+g[v]
$$
到这里，动态规划的问题就全部结束了，接下来就要思考怎么让DP上树。

# 实现

在上树之前我们先想一下怎么在链上解决。

括号匹配的方法和[暴力](#暴力实现)的实现方法类似，借助一个栈来解决，在栈中记录左括号的前驱节点的编号。如果当前的节点是右括号，就尝试从栈里弹出一个左括号。

```C++
for (int i = 0; i < G[u].size(); i++) {
    int top = -1;
    int v = G[u][i];
    if (s[v] == '(') {
        st.push_back(u);
    } else {
        if (!st.empty()) {
            top = st.back();
            st.pop_back();
            g[v] = g[top] + 1;
        }
    }
    f[v] = f[u] + g[v];
    ans ^= (long long)v * f[v];
    dfs(v);
}
```

但是，在树上这样就显然是行不通的。因为：

1. 如果当前节点为右括号，匹配到的左括号从栈里被弹出之后，回溯时需要将弹出的括号放回栈里。
2. 如果当前节点为左括号，压入栈中之后在回溯是需要弹出。

所以，代码应该改为：

```C++
for (int i = 0; i < G[u].size(); i++) {
    int top = -1;
    int v = G[u][i];
    if (s[v] == '(') {
        st.push_back(u);
    } else {
        if (!st.empty()) {
            top = st.back();
            st.pop_back();
            g[v] = g[top] + 1;
        }
    }
    f[v] = f[u] + g[v];
    ans ^= (long long)v * f[v];
    dfs(v);
    if (st.back() == u) //当前节点是左括号，需要弹出当前节点。
        st.pop_back();
    else if (top != -1) //当前节点是右括号，从栈里弹出了一个左括号，需要将其压回栈中。
        st.push_back(top);
}
```

还有，答案需要开`long long`，不然会爆掉（算一下数据范围就能知道）。

[完整代码](https://github.com/CrSjimo/brush-problem/blob/master/CSPS2D1T2.cpp)

# 暴力实现

给别人讲题，讲到暴力实现。

- 第一层循环：遍历$1 \rightarrow n$
- 第二/三层循环：遍历字串$l, r$
- 第四层循环：检查字串是否合法

检查字串是否合法的方式如下：

```C++
bool check(int l, int r) {
    stack<bool> st;
    for (int i = l; i <= r; i++) {
        if (s[i] == '(') {
            st.push(1);
        } else {
            if (st.empty())
                return false;
            st.pop();
        }
    }
    return st.empty();
}
```

（注：后来一想发现暴力实现似乎并不需要真正地把栈开出来，用一个计数器其实就可以。）

[完整代码](https://github.com/CrSjimo/brush-problem/blob/master/CSPS2D1T2baoli.cpp)

实测暴力做法可以切掉$20pts$。