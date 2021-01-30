---
title: Multi-University-Training-Contest-8
date: 2019-08-14 21:00:00 +0800
tags: [HDU多校]
description: Multi-University-Training-Contest-8
---


## C. Acesrc and Good Numbers

题意：$f(d, n)$表示$1\cdots n$中有几个$d$，定义一个$good \; number$为$f(d, k)=k$，现在给一个$x, d$问不超过$x$的最大$good \; number$的$k$

思路：很显然这种数不多，就可以快乐的打表了…

## F. Acesrc and Travel

```C++

```

## H. Andy and Maze

题意：一张$n$个点，$m$条边的无向图，问经过$k$个点的最大代价。

思路：搜索…如果当前值加上剩下路径条数$\times Max$依旧比$ans$小，则$return$

```C++

#include <bits/stdc++.h>

using namespace std;

typedef pair<int, int> pii;

#define N 10010

int n, m, k, ans, Max, vis[N];
vector<vector<pii>> G;

void DFS(int u, int cur, int v) {
    if (cur == k) {
        ans = max(ans, v);
        return;
    }
    if (v + (k - cur) * Max <= ans) {
        return;
    }
    for (auto it : G[u]) {
        if (vis[it.first]) {
            continue;
        }
        vis[it.first] = 1;
        DFS(it.first, cur + 1, v + it.second);
        vis[it.first] = 0;
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d %d %d", &n, &m, &k);
        G.clear();
        G.resize(n + 1);
        for (int i = 1; i <= n; ++i) {
            vis[i] = 0;
        }
        Max = -1;
        for (int i = 1, u, v, w; i <= m; ++i) {
            scanf("%d %d %d", &u, &v, &w);
            G[u].emplace_back(v, w);
            G[v].emplace_back(u, w);
            Max = max(Max, w);
        }
        ans = -1;
        for (int i = 1; i <= n; ++i) {
            vis[i] = 1;
            DFS(i, 1, 0);
            vis[i] = 0;
        }
        if (ans == -1) {
            puts("impossible");
        } else {
            printf("%d\n", ans);
        }
    }
    return 0;
}

```

## I. Calabash and Landlord

题意：给定两个矩形，问将二维平面分成几部分

思路：$BFS$...

```C++

#include <bits/stdc++.h>

using namespace std;

#define N 10
#define INF 0x3f3f3f3f

struct node {
    int l, t, r, b;
    int x[2], y[2];

    void input() {
        scanf("%d %d %d %d", x, y, x + 1, y + 1);
    }
    
    bool in(int _x, int _y) {
        return x[0] < _x && _x < x[1] && y[0] < _y && _y < y[1];
    }
} a[N * N];

struct Hash {
    int cnt, a[N];

    void Init() {
        cnt = 0;
    }
    
    void add(int x) {
        a[++cnt] = x;
    }
    
    void work() {
        sort(a + 1, a + 1 + cnt);
        cnt = unique(a + 1, a + 1 + cnt) - a - 1;
    }
    
    int get(int x) {
        return lower_bound(a + 1, a + 1 + cnt, x) - a;
    }
} hx, hy;

int vis[N][N];
int dir[4][2] = {1, 0, 0, 1, -1, 0,0, -1};

bool check(int x, int y, int dx, int dy) {
    if (x == dx) {
        for (int i = 0; i < 2; ++i) {
            if ( x == a[i].x[0] || x == a[i].x[1]) {
                if (a[i].y[0] <= y && dy <= a[i].y[1]) {
                    return false;
                }
            }
        }
    } else {
        for (int i = 0; i < 2; ++i) {
            if (y == a[i].y[0] || y == a[i].y[1]) {
                if (a[i].x[0] <= x && dx <= a[i].x[1]) {
                    return false;
                }
            }
        }
    }
    return true;
}

bool judge(int x, int y, int d) {
    if (d == 0) {
        return check(x + 1, y, x + 1, y + 1);
    } else if (d == 1) {
        return check(x, y + 1, x + 1, y + 1);
    } else if (d == 2) {
        return check(x, y, x, y + 1);
    } else {
        return check(x, y, x + 1, y);
    }
}

void DFS(int x, int y) {
    vis[x][y] = 1;
    for (int i = 0; i < 4; ++i) {
        int dx = x + dir[i][0];
        int dy = y + dir[i][1];
        if (dx >= 1 && dx < hx.cnt && dy >= 1 && dy < hy.cnt) {
            if (!vis[dx][dy] && judge(x, y, i)) {
                DFS(dx, dy);
            }
        }
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--) {
        a[0].input();
        a[1].input();
        hx.Init(), hy.Init();
        hx.add(-INF), hx.add(INF);
        hx.add(a[0].x[0]), hx.add(a[0].x[1]);
        hx.add(a[1].x[0]), hx.add(a[1].x[1]);
        hy.add(-INF), hy.add(INF);
        hy.add(a[0].y[0]), hy.add(a[0].y[1]);
        hy.add(a[1].y[0]), hy.add(a[1].y[1]);
        hx.work(), hy.work();
        for (int i = 0; i < 2; ++i) {
            a[i].x[0] = hx.get(a[i].x[0]);
            a[i].x[1] = hx.get(a[i].x[1]);
            a[i].y[0] = hy.get(a[i].y[0]);
            a[i].y[1] = hy.get(a[i].y[1]);
        }
        memset(vis, 0, sizeof vis);
        int res = 0;
        for (int i = 1; i < hx.cnt; ++i) {
            for (int j = 1; j < hy.cnt; ++j) {
                if (!vis[i][j]) {
                    DFS(i, j);
                    res++;
                }
            }
        }
        printf("%d\n", res);
    }
    return 0;
}

```

## J.Quailty and CCPC

签到

```C++

#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
int n, d;
struct node {
    char s[10];
    int p, t;
    node() {}
    void scan() {
        scanf("%s%d%d", s, &p, &t);
    }
    bool operator < (const node &other) const {
        if (p != other.p) return p > other.p;
        return t < other.t;
    }
}a[N];

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &d);
        for (int i = 1; i <= n; ++i) {
            a[i].scan();
        }
        sort(a + 1, a + 1 + n);
        if (n * d % 10 == 5) {
            int t = n * d / 10 + 1;
            puts(a[t].s);
        } else {
            puts("Quailty is very great");
        }
    }
    return 0;
}

```

## K. Roundgod and Milk Tea

题意：$n$个班，每个班有$a_i$个人，$b_i$个牛奶，每个班的人不能和自己班的牛奶，问最多有多少人喝到牛奶。

思路：枚举…

```C++

#include <bits/stdc++.h>

using namespace std;

#define ll long long
const int N = 1e6 + 10;
int n;
ll a[N][2];

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        ll sum = 0;
        for (int i = 1; i <= n; ++i) {
            scanf("%lld%lld", a[i], a[i] + 1);
            sum += a[i][1];
        }
        ll res = 0;
        for (int i = 1; i <= n; ++i) {
            ll now = sum - a[i][1];
            if (res > a[i][1]) {
                now -= res - a[i][1];
            }
            res += min(now, a[i][0]);
        }
        printf("%lld\n", res);
    }
    return 0;
}

```