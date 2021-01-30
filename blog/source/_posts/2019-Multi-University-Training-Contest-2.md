---
title: 	 2019 Multi-University Training Contest 2
date:   2019-07-24 20:15:00 +0800
tags: [HDU多校]
description:  2019 Multi-University Training Contest 2
---

## B. Beauty Of Unimodal Sequence

## E. Everything Is Generated In Equal Probability

题意：给定一个$N$，等概率从$N$中选取一个$n$，等概率生成一个$n$的序列，然后执行伪代码，求逆序对期望

思路：打表得$\frac{n^ 2-1}{9}$

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

const ll p = 998244353;

ll qpow(ll x, ll n) {
    ll res = 1;
    while (n) {
        if (n & 1) {
            res = res * x % p;
        }
        x = x * x % p;
        n >>= 1;
    }
    return res;
}

ll n;

int main() {
    ll inv = qpow(9, p - 2);
    while (scanf("%lld", &n) != EOF) {
        ll ans = (n * n - 1) % p * inv % p;
        printf("%lld\n", ans);
    }
    return 0;
}

```

## H. Harmonious Army

题意：有$n$个士兵，每个士兵两种身份$Mage\;or\;Warrior$，有$m$对关系，如果是两个$Warriors$，则属性增加$a$，如果是两个$Mages$，属性增加$c$，否则增加$b(b=\frac{a}{4}+\frac{c}{2})$，问最大属性值。

思路：将问题转换成，有$m$对关系，如果是两个$Warriors$，则属性较少$b+c$，如果是两个$Mages$，属性增加$a+b$，否则增加$a+c$，问最大属性值。

那么建立如下网络流，可以得到 $ a = b =（A + B）/ 2， c = d = (C + B) / 2, e = -B + (A + C) / 2$ 

![1008](/upload/2019 Multi-University Training Contest 2/1008.png)

答案为总的$a+b+c$减去最小割

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
typedef double db;

#define INFLL 0x3f3f3f3f3f3f3f3f
#define N 510
#define M 100010

struct Dicnic {
    struct Edge {
        int to, nxt;
        db flow;

        Edge() {}
    
        Edge(int to, int nxt, db flow) : to(to), nxt(nxt), flow(flow) {}
    } edge[M << 1];
    
    int head[N], dep[N];
    int S, T, tot;
    
    void Init() {
        memset(head, -1, sizeof head);
        tot = 0;
    }
    
    void addedge(int u, int v, db w, db rw = 0) {
        edge[tot] = Edge(v, head[u], w);
        head[u] = tot++;
        edge[tot] = Edge(u, head[v], rw);
        head[v] = tot++;
    }
    
    bool BFS() {
        memset(dep, -1, sizeof dep);
        queue<int> q;
        q.push(S);
        dep[S] = 1;
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            for (int i = head[u]; ~i; i = edge[i].nxt) {
                if (edge[i].flow && dep[edge[i].to] == -1) {
                    dep[edge[i].to] = dep[u] + 1;
                    q.push(edge[i].to);
                }
            }
        }
        return dep[T] >= 0;
    }
    
    db DFS(int u, db f) {
        if (u == T || f == 0) return f;
        db w, used = 0;
        for (int i = head[u]; ~i; i = edge[i].nxt) {
            if (edge[i].flow > 0 && dep[edge[i].to] == dep[u] + 1) {
                w = DFS(edge[i].to, min(f - used, edge[i].flow));
                edge[i].flow -= w;
                edge[i ^ 1].flow += w;
                used += w;
                if (used == f) return f;
            }
        }
        if (!used) dep[u] = -1;
        return used;
    }
    
    db solve() {
        db ans = 0;
        while (BFS()) {
            ans += DFS(S, INFLL);
        }
        return ans;
    }
} dicnic;

int n, m;
int u, v, a, b, c;
ll w1[N], w2[N];

int main() {
    while (scanf("%d %d", &n, &m) != EOF) {
        memset(w1, 0, sizeof w1);
        memset(w2, 0, sizeof w2);
        dicnic.Init();
        dicnic.S = 0, dicnic.T = n + 1;
        ll ans = 0;
        for (int i = 1; i <= m; ++i) {
            scanf("%d %d %d %d %d", &u, &v, &a, &b, &c);
            w1[u] += a + b;
            w1[v] += a + b;
            w2[u] += b + c;
            w2[v] += b + c;
            dicnic.addedge(u, v, -b * 1.0 + a / 2.0 + c / 2.0);
            dicnic.addedge(v, u, -b * 1.0 + a / 2.0 + c / 2.0);
            ans += a + b + c;
        }
        for (int i = 1; i <= n; ++i) {
            dicnic.addedge(dicnic.S, i, w1[i] / 2.0);
            dicnic.addedge(i, dicnic.T, w2[i] / 2.0);
        }
        ans = (ll) round(ans - dicnic.solve());
        printf("%lld\n", ans);
    }
    return 0;
}

```

## J. Just Skip The Problem

签到

```C++
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

const ll p = (ll) 1e6 + 3;

#define N 1000010

int n;
ll f[N];

void Init() {
    f[0] = 1;
    for (int i = 1; i < N; ++i) {
        f[i] = f[i - 1] * i % p;
    }
}

int main() {
    Init();
    while (scanf("%d", &n) != EOF) {
        printf("%lld\n", f[min(1ll * n, p)]);
    }
    return 0;
}

```

## K. Keen On Everything But Triangle

题意：问区间$[L,R]$内能组成的三角形最大周长

思路：线段树维护区间内最大的$50$个数，求三角形周长。

```C++

#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define N 100010 
#define INF 0x3f3f3f3f
int n, q, a[N];

struct SEG {
    struct node {
        int a[55]; 
        node() {
            a[0] = 0;
        }
        void add(int x) {
            a[++a[0]] = x;
        }
        node operator + (const node &other) const {
            node res = node();
            int it = 1, it2 = 1;
            while (res.a[0] <= 50 && (it <= a[0] || it2 <= other.a[0])) {
                if (it > a[0]) {
                    res.add(other.a[it2]);
                    ++it2;
                } else if (it2 > other.a[0]) {
                    res.add(a[it]);
                    ++it;
                } else {
                    if (a[it] > other.a[it2]) {
                        res.add(a[it]);
                        ++it;
                    } else {
                        res.add(other.a[it2]);
                        ++it2;
                    }
                }
            }
            return res;
        }
    }t[N << 2], res;
    void build(int id, int l, int r) {
        if (l == r) {
            t[id] = node();
            t[id].add(a[l]);
            return;
        }
        int mid = (l + r) >> 1;
        build(id << 1, l, mid);
        build(id << 1 | 1, mid + 1, r);
        t[id] = t[id << 1] + t[id << 1 | 1];
    }
    void query(int id, int l, int r, int ql, int qr) {
        if (l >= ql && r <= qr) {
            res = res + t[id];
            return;
        }
        int mid = (l + r) >> 1;
        if (ql <= mid) query(id << 1, l, mid, ql, qr);
        if (qr > mid) query(id << 1 | 1, mid + 1, r, ql, qr);
    }
}seg;

int main() {
    while (scanf("%d%d", &n, &q) != EOF) {
        for (int i = 1; i <= n; ++i) scanf("%d", a + i);
        seg.build(1, 1, n);
        int l, r;
        while (q--) {
            scanf("%d%d", &l, &r);
            ll res = -1;
            seg.res = SEG::node();
            seg.query(1, 1, n, l, r);
            int sze = seg.res.a[0];
            for (int i = 1; i <= sze - 2; ++i) {
                ll A = seg.res.a[i], B = seg.res.a[i + 1], C = seg.res.a[i + 2];
                if (A < B + C) {
                    res = A + B + C;
                    break;
                }
            }
            printf("%lld\n", res);
        }
    }
    return 0;
}

```

## L. Longest Subarray

```C++

```