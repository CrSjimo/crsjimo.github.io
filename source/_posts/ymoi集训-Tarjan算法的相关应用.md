---
title: Tarjan算法的相关应用
date: 2020-08-21 16:34:12
tags: 
  - ymoi
  - Tarjan算法
  - 强连通分量
  - 点双连通分量
  - 边双连通分量
category: 算法竞赛
---
# 强连通分量

定义略。

用Tarjan算法来求解强连通分量，在此之前，先明确这两个定义：

- 返祖边：由一个点指向其祖先的边。

- 横插边： 由一个节点指向非祖先节点的边。（只有有向图会出现）

这两种边都是dfs树的非树边。

定义这两个数组：

- $dfn$: dfs序。

- $low$: **不经横插边**所能到达的点中最小的$dfn$。

```C++
if(!dfn[v]){
    tarjan(v);
    low[u]=min(low[u],low[v]);
}else if(ins[v]){ // 判断不是横插边，即当前子节点v在栈中。
    low[u]=min(low[u],dfn[v]);
}
```

不难发现，对于某个节点$u$，$low_u$表示其深度最小的祖先的$dfn$。这样一来，当某个点的$dfn$等于$low$时，就说明找到了新的强连通分量。用一个栈来维护强连通分量里的节点，具体操作如下：

- 更新$dfn$和$low$的初值。

- 当前节点进栈。

- 遍历当前节点的子节点$v$：

    - 如果$v$未被访问，说明在dfs树上$v$在$u$的子树中，对$v$继续dfs。用$low_v$更新$low_u$。

    - 如果$v$被访问且在栈中，说明$u\rightarrow v$是一条返祖边。用$dfn_v$更新$low_u$。

- 执行完上述操作后，如果$u$的$dfn$和$low$相等，说明找到了一个新的强连通分量，则：

    - 将栈中$u$及其之后的所有节点出栈。

    - 标记强连通分量。

代码：

```C++
void tarjan(int u){
    dfn[u]=low[u]=++T;
    ins[u]=true;
    s.push(u);
    for(int i=0;i<G[u].size();i++){
        int v=G[u][i];
        if(!dfn[v]){
            tarjan(v);
            low[u]=min(low[u],low[v]);
        }else if(ins[v]){
            low[u]=min(low[u],dfn[v]);
        }
    }
    if(dfn[u]==low[u]){
        int v;
        cnt++;
        do{
            v=s.top();s.pop();ins[v]=false;
            color[v]=cnt;
        }while(u!=v);
    }
}
```

# 缩点

由于强连通分量内的点可以互相到达，可以将强连通分量缩成一个点，这样可以得到一个有向无环图。

缩点的流程如下：

- 遍历所有节点$u=1\rightarrow n$：

    - 遍历$u$的所有子节点$v$：

        - 如果$u$和$v$不属于同一个强连通分量，就从$u$所在的强连通分量向$v$所在的强连通分量连边。

代码：

```C++
void suodian(){
    for(int u=1;u<=n;u++){
        c_siz[color[u]]+=w[u];
        for(int i=0;i<G[u].size();i++){
            int v=G[u][i];
            if(color[u]==color[v])continue;
            Gs[color[u]].push_back(color[v]);
        }
    }
}
```

# 割点

在无向图中，对于一个点，如果删除这个点及其连边后，连通块数量增加，那么这就是一个割点。

判断一个点是割点的充分条件如下：

- 若$u$为根，$u$有两个及以上子节点。

- 若$u$不为根，在dfs树中$u$的子树中$\exists v, low_v\geq dfn_u$。

代码：

```C++
void tarjan(int u,int fa){ //由于是无向图，需要记录父亲节点
    dfn[u]=low[u]=++T;
    for(int i=0;i<G[u].size();i++){
        int v=G[u][i];
        if(v==fa)continue;
        if(!dfn[v]){
            tarjan(v,u);
            low[u]=min(low[u],low[v]);
            if(low[v]>=dfn[u]&&fa){
                cut[u]=true;
            }
        }else{
            low[u]=min(low[u],dfn[v]);
        }
    }
    if((!fa) && G[u].size()>=2)cut[u]=true;
}
```

# 割边

定义类似割点

一条边$u\rightarrow v$，当且仅当$low_v\geq \dfn_u$。

# 点双连通分量

不存在割点的连通子图。