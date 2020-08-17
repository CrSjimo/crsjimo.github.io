---
title: ymoi集训-线段树
date: 2020-08-17 13:47:53
tags: 
 - 线段树 
 - ymoi 
category: 算法竞赛
---

线段树基本概念略。

以下代码中一些宏定义如下：

```C++
#define mid ((l+r)>>1)
#define lc (o<<1)
#define rc (o<<1|1)
#define lcq lc,l,mid
#define rcq rc,mid+1,r
#define oq o,l,r
#define od int o,int l,int r
```

# 线段树能解决什么问题

- 区间/单点修改，区间/单点查询。

- $O(1)$时间完成左右子树状态的合并。

在思考问题的时候，如果题目要求的状态难以合并，可以借助辅助状态来合并。

# 线段树的算法流程

## push_up

将左右子树的状态合并到根节点

例如：区间求和的push_up操作为$SUM_o = SUM_l + SUM_r$

## push_down

更新子树的lazy tags。

例如：区间加法的push down代码

```C++
void push_down(od){
    if(!tree[o].tag)return;
    tree[lc].tag+=tree[o].tag;
    tree[rc].tag+=tree[o].tag;
    tree[lc].sum+=tree[o].tag*(mid-l+1);
    tree[rc].sum+=tree[o].tag*(r-mid);
    tree[o].tag=0;
}
```

## query

区间查询操作

参数：

- o：当前节点
- l：当前区间左端点
- r：当前区间右端点
- ql：要查询的左端点
- qr：要查询的右端点

查询操作如下：

- 如果当前区间是要查询的区间的子区间（**不是相等**），就返回当前区间的值。
- **Push down the lazy tags of current node.**
- 如果要查询的区间与左子树**有交集**，答案加上左子树的查询答案。右子树同理。
- 返回查询答案。

例如，区间求和的查询代码

```C++
ll query(od,int ql,int qr){
    if(l>=ql&&r<=qr){
        return tree[o].sum;
    }
    ll ans=0;
    push_down(oq);
    if(ql<=mid){
        ans+=query(lcq,ql,qr);
    }
    if(qr>mid){
        ans+=query(rcq,ql,qr);
    }
    return ans;
}
```

## update

区间更新操作。

参数：

- o：当前节点
- l：当前区间左端点
- r：当前区间右端点
- ql：要查询的左端点
- qr：要查询的右端点
- val：更新值

更新操作如下：

- 如果当前区间是要查询的区间的子区间（**不是相等**），就返回当前区间的值。**在返回当前区间的值之前，应该把当前区间的lazy tag更新的值里边**。
- **Push down the lazy tags of current node.**
- 如果要更新的区间与左子树**有交集**，就更新左子树。

例如，区间加法的更新操作的代码

```C++
void update(od,int ql,int qr,ll val){
    if(l>=ql&&r<=qr){
        tree[o].tag+=val;
        tree[o].sum+=val*(r-l+1);
        return;
    }
    push_down(oq);
    if(ql<=mid){
        update(lcq,ql,qr,val);
    }
    if(qr>mid){
        update(rcq,ql,qr,val);
    }
    push_up(o);
}
```

## build

建立线段树。

建树操作如下：

- 如果当前已经建立到叶子节点，就把当前节点的值设为初始值并初始化lazy tags。

- 递归建立左右子树。

- push up。

例如：

```C++
void build(od){
    if(l==r){
        tree[o].sum=a[l];
        return;
    }
    build(lc,l,mid);
    build(rc,mid+1,r);
    push_up(o);
}
```

模板：[segtree.cpp](https://github.com/CrSjimo/brush-problem/blob/master/templates/segtree.cpp)