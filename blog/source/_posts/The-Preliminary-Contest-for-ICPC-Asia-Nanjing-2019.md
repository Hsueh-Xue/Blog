---
title: The Preliminary Contest for ICPC Asia Nanjing 2019
date:   2019-09-02 19:30:00 +0800
tags: [online]
description: The Preliminary Contest for ICPC Asia Nanjing 2019
---


##  D. Robots

题意：给定一个$DAG$，机器人每次等概率的选择一个点走或者停留，每次花费是当前天数，求$1\rightarrow n$的花费期望

思路：$f_u$表示到$u$的期望，那么很显然$f_u = \frac{f_u}{out_u + 1} + \sum f_v + 1$，$g_u$表示花费期望，那么$g_u=\frac{g_u}{out_u+1}+\sum g_v + f_u$。看错题可还行

```C++

#include <bits/stdc++.h>

using namespace std;

#define endl "\n"
using ll = long long;
using db = double;

const int N = 1e5 + 10;

int n, m;
db f[N], g[N];
int d1[N], d2[N], in[N], out[N];
vector<vector<int>> G;

void gao() {
    queue<int> q;
    q.push(n);
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        if (out[u]) {
            f[u] += out[u] + 1;
            f[u] /= out[u];
        }
        for (auto v: G[u]) {
            f[v] += f[u];
            --d1[v];
            if (d1[v] == 0) {
                q.push(v);
            }
        }
    }
    q.push(n);
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        if (out[u]) {
            g[u] += f[u] * (out[u] + 1);
            g[u] /= out[u];
        }
        for (auto v : G[u]) {
            g[v] += g[u];
            --d2[v];
            if (d2[v] == 0) {
                q.push(v);
            }
        }
    }
}

void RUN() {
    int _T;
    scanf("%d", &_T);
    while (_T--) {
        scanf("%d %d", &n, &m);
        G.clear();
        G.resize(n + 1);
        for (int i = 1; i <= n; ++i) {
            f[i] = 0.0;
            g[i] = 0.0;
            d1[i] = 0;
            d2[i] = 0;
            in[i] = 0;
            out[i] = 0;
        }
        for (int i = 1, u, v; i <= m; ++i) {
            scanf("%d %d", &u, &v);
            G[v].push_back(u);
            d1[u]++;
            d2[u]++;
            out[u]++, in[v]++;
        }
        gao();
        printf("%.2f\n", g[1]);
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif

    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    
    RUN();

#ifdef LOCAL_JUDGE
    cerr << "Time elapsed: " << 1.0 * clock() / CLOCKS_PER_SEC << " s.\n";
#endif
    return 0;
}

```

