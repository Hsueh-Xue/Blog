---
title: 前缀和优化建图2-SAT
date:  2019-10-25 22:10:00 +0800
tags: [知识点]
description: 前缀和优化建图2-SAT
---


## BZOJ #3495. PA2010 Riddle

### 题目描述

有$n$个城镇被分成了$k$个郡，有m条连接城镇的无向边。

要求给每个郡选择一个城镇作为首都，满足每条边至少有一个端点是首都。

### 思路

每个城市只有是或者不是两个状态，很显然的$2-SAT$，其中$M$条边$(u, v)$很显然建边$u'\rightarrow v, v'\rightarrow u$

对于每个郡只有一个城镇是首都

我们考虑前缀和。

首先新增$n$个点，对于$i$对应的新的节点为$i+n$。那么我们的$i+n$表示当前郡的前$i$个是否应有城市是否为首都，那么如果$i-1+n$选了，$i+n$必定选了，$i+n$没选，$i-1+n$必定没选。同时如果$i$选了，那么$i-1+n$必定没选，如果$i - 1 + n$选了，$i$一定没选

我们令$u1=i-1,u2=i,v1=i-1+n,v2=i+n$

那么需要建边

- $v1\rightarrow v2$
- $v2'\rightarrow v1'$
- $u2\rightarrow v1'$
- $v1 \rightarrow u2'$

### 代码

```C++

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...) do { cout << #x << " -> "; err(x); } while (0)

void err() { cout << endl; }

template<class T, class... Ts>
void err(const T &arg, const Ts &... args) {
    cout << arg << ' ';
    err(args...);
}

#define endl "\n"
using ll = long long;
using db = double;

const int N = 4e6 + 10;

struct Edge {
    int to, nxt;

    Edge() {}
    
    Edge(int to, int nxt) : to(to), nxt(nxt) {}
} edge[N << 1];

int head[N], tot;

void Init() {
    memset(head, -1, sizeof head);
    tot = 0;
}

void addedge(int u, int v) {
    edge[tot] = Edge(v, head[u]);
    head[u] = tot++;
}

int Low[N], DFN[N], Stack[N], Belong[N];
int Index, top;
int scc;
bool Instack[N];
int num[N];

void Tarjan(int u) {
    int v;
    Low[u] = DFN[u] = ++Index;
    Stack[top++] = u;
    Instack[u] = true;
    for (int i = head[u]; ~i; i = edge[i].nxt) {
        v = edge[i].to;
        if (!DFN[v]) {
            Tarjan(v);
            if (Low[u] > Low[v]) Low[u] = Low[v];
        } else if (Instack[v] && Low[u] > DFN[v]) {
            Low[u] = DFN[v];
        }
    }
    if (Low[u] == DFN[u]) {
        scc++;
        do {
            v = Stack[--top];
            Instack[v] = false;
            Belong[v] = scc;
            num[scc]++;
        } while (u != v);
    }
}

bool solve(int n) {
    memset(DFN, 0, sizeof DFN);
    memset(Instack, false, sizeof Instack);
    memset(num, 0, sizeof num);
    Index = scc = top = 0;
    for (int i = 0; i < n; ++i) {
        if (!DFN[i]) {
            Tarjan(i);
        }
    }
    for (int i = 0; i < n; i += 2) {
        if (Belong[i] == Belong[i ^ 1]) {
            return false;
        }
    }
    return true;
}

int n, m, k;
int a[N];

void RUN() {
    while (scanf("%d %d %d", &n, &m, &k) != EOF) {
        Init();
        for (int i = 1, x, y; i <= m; ++i) {
            scanf("%d %d", &x, &y);
            --x, --y;
            addedge(x << 1 ^ 1, y << 1);
            addedge(y << 1 ^ 1, x << 1);
        }
        for (int i = 0; i < n; ++i) {
            int x = i, y = i + n;
            addedge(x << 1, y << 1);
            addedge(y << 1 ^ 1, x << 1 ^ 1);
        }
        for (int i = 1, w; i <= k; ++i) {
            scanf("%d", &w);
            for (int j = 1; j <= w; ++j) {
                scanf("%d", a + j);
                --a[j];
            }
            for (int j = 2; j <= w; ++j) {
                int x = a[j - 1], y = a[j];
                addedge((x + n) << 1, (y + n) << 1);
                addedge(((y + n) << 1) ^ 1, ((x + n) << 1) ^ 1);
                addedge(y << 1, (x + n) << 1 ^ 1);
                addedge((x + n) << 1, y << 1 ^ 1);
            }
        }
        puts(solve(4 * n) ? "TAK" : "NIE");
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif

//    ios::sync_with_stdio(false);
//    cin.tie(nullptr), cout.tie(nullptr);

    RUN();

#ifdef LOCAL_JUDGE
    cerr << "Time elapsed: " << 1.0 * clock() / CLOCKS_PER_SEC << " s.\n";
#endif
    return 0;
}

```