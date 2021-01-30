---
title: 最大团 Bron-Kerbosch
date:  2020-02-03 16:10:00 +0800
tags: [知识点]
description: 最大团 Bron-Kerbosch
---


## Bron-Kerbosch

## 1. 作用

Bron-Kerbosch 算法用于计算图中的最大的全连通分量，即计算图的最大团。

## 2. 原理

Bron-Kerbosch 算法的基础形式是一个递归回溯的搜索算法，其通过给定三个集合: $R,P,X$ 来递归的进行搜索

- 初始化集合 $R,X$ 分别为空，集合 $P$ 为所有顶点的集合
- 每次从集合 $P$ 中取顶点 ${v_i}$，当集合中没有顶点时，有两种情况：
  - 集合 $R$ 是最大团，此时集合 $X$ 为空
  - 无最大团，此时回溯
- 对于每一个从集合 $P$ 中取得的顶点 $v_i$，有如下处理
  - 将顶点 ${v_i}$ 加到集合$R$ 中，集合 $P,X$ 与顶点 ${v_i}$ 的邻接顶点集合 $N{v_i}$ 相交，之后递归集合 $R,P,X$
  - 从集合 $P$ 中删除顶点 ${v_i}$，并将顶点 ${v_i}$ 添加到集合$X$ 中
  - 若集合 $P,X$ 都为空，则集合$R$ 即为最大团

总的来看，就是每次从集合 $P$ 中取 $v_i$ 后，再从 $P \cap N{v_i}$集合中取相邻结点，保证集合 $R$ 中任意顶点间都两两相邻

```bash
BronKerbosch1(R,P,X):
    if P and X are both empty:
        report R as a maximal clique
    for each vertex v in P:
        BronKerbosch1(R ⋃ {v}, P ⋂ N(v), X ⋂ N(v))
        P := P \ {v}
        X := X ⋃ {v}
```

## 3. 算法优化

对于基础的算法，由于其递归搜索了所有情况，对其中有些不是最大团的也进行了搜索，效率不高，为了节省时间让算法更快的回溯，可以通过设定关键点来进行搜索。

由于对于任意的最大团，其必须包括顶点${u}$ 或 $N-N{u}$，不然其必然需要通过添加它们来进行扩充，这显然矛盾，所以仅需测试顶点 ${u}$ 以及 $N-N{u}$ 即可。

```bash
BronKerbosch2(R,P,X):
    if P and X are both empty:
        report R as a maximal clique
    choose a pivot vertex u in P ⋃ X
    for each vertex v in P \ N(u):
       BronKerbosch2(R ⋃ {v}, P ⋂ N(v), X ⋂ N(v))
       P := P \ {v}
       X := X ⋃ {v}
```

由于其是通过选择特殊点，来进行最小化递归调用，一定程度上节省了时间，但还可以与降序的方式结合使用，来保证在线性的时间内求子图的最大团

```bash
BronKerbosch3(G):
    P = V(G)
    R = X = empty
    for each vertex v in a degeneracy ordering of G:
        BronKerbosch2(R ⋃ {v}, P ⋂ N(v), X ⋂ N(v))
        P := P \ {v}
        X := X ⋃ {v}
```

## 4. 算法实现

```C++
#include <bits/stdc++.h>

using namespace std;

const int N = 1e2 + 10;

int n, m;
int G[N][N];
int group[N], vis[N], res, cnt[N];

bool gao(int u, int _cnt) {
    for (int i = u + 1; i <= n; ++i) {
        if (cnt[i] + _cnt <= res) {
            return false;
        }
        if (G[u][i]) {
            bool F = true;
            for (int j = 0; j < _cnt; ++j) {
                if (!G[i][vis[j]]) {
                    F = false;
                    break;
                }
            }
            if (!F) continue;
            vis[_cnt] = i;
            if (gao(i, _cnt + 1)) {
                return true;
            }
        }
    }
    if (_cnt > res) {
        for (int i = 0; i < _cnt; ++i) {
            group[i] = vis[i];
        }
        res = _cnt;
        return true;
    }
    return false;
}

void maxclique() {
    res = -1;
    for (int i = n; i >= 1; --i) {
        vis[0] = i;
        gao(i, 1);
        cnt[i] = res;
    }
}

int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d %d", &n, &m);
        memset(G, 0, sizeof G);
        for (int i = 1, u, v; i <= m; ++i) {
            scanf("%d %d", &u, &v);
            G[u][v] = G[v][u] = 1;
        }
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (i == j) {
                    G[i][j] = 0;
                } else {
                    G[i][j] ^= 1;
                }
            }
        }
        maxclique();
        if (res < 0) res = 0;
        printf("%d\n", res);
        for (int i = 0; i < res; ++i) {
            printf("%d ", group[i]);
        }
        putchar(10);
    }
    return 0;
}
```