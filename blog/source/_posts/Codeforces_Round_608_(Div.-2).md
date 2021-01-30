---
title: Codeforces Round 608 (Div. 2)
date:  2020-02-11 11:10:00 +0800
tags: [Codeforces]
description: Codeforces Round 608 (Div. 2)
---


## A. Suits

水

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-11 10:15:51
 *
 * */

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...)             \
    do {                      \
        cout << #x << " -> "; \
        err(x);               \
    } while (0)

void err() {
    cout << endl;
}

template <class T, class... Ts>
void err(const T& arg, const Ts&... args) {
    cout << arg << ' ';
    err(args...);
}

#define endl "\n"
#define all(A) A.begin(), A.end()
using pII = pair<int, int>;
using ll = long long;
using db = double;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e5 + 10;

int gao1(int a, int b, int c, int d, int e, int f) {
    int res = 0;
    res += min(a, d) * e;
    d -= min(a, d);
    res += min(b, min(c, d)) * f;
    return res;
}

int gao2(int a, int b, int c, int d, int e, int f) {
    int res = 0;
    res += min(b, min(c, d)) * f;
    d -= min(b, min(c, d));
    res += min(a, d) * e;
    return res;
}

int a, b, c, d, e, f;

void RUN() {
    cin >> a >> b >> c >> d >> e >> f;
    cout << max(gao1(a, b, c, d, e, f), gao2(a, b, c, d, e, f)) << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    RUN();
    
    return 0;
}

```

## B. Blocks

题意：

给一个01串，每次可以将$i, i+1$翻转，问能否将其翻转成为全黑全白，输出方案（方案次数$\leq 3\cdot n$）

思路：

尝试全黑和全白，暴力翻转

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-11 10:21:13
 *
 * */

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...)             \
    do {                      \
        cout << #x << " -> "; \
        err(x);               \
    } while (0)

void err() {
    cout << endl;
}

template <class T, class... Ts>
void err(const T& arg, const Ts&... args) {
    cout << arg << ' ';
    err(args...);
}

#define endl "\n"
#define all(A) A.begin(), A.end()
using pII = pair<int, int>;
using ll = long long;
using db = double;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e5 + 10;

int n, a[N];
char s[N];
vector<int> res;

void RUN() {
    cin >> n;
    cin >> (s + 1);
    for (int i = 1; i <= n; ++i) {
        if (s[i] == 'W')
            a[i] = 0;
        else
            a[i] = 1;
    }
    for (int i = 1; i < n; ++i) {
        if (!a[i]) {
            a[i] ^= 1, a[i + 1] ^= 1;
            res.push_back(i);
        }
    }
    if (a[n]) {
        cout << res.size() << endl;
        for (int i = 0, len = res.size(); i < len; ++i) {
            cout << res[i] << " \n"[i==len - 1];
        }
        return;
    }
    for (int i = 1; i < n; ++i) {
        if (a[i]) {
            a[i] ^= 1, a[i + 1] ^= 1;
            res.push_back(i);
        }
    }
    if (!a[n]) {
        cout << res.size() << endl;
        for (int i = 0, len = res.size(); i < len; ++i) {
            cout << res[i] << " \n"[i == len - 1];
        }
        return;
    }
    cout << -1 << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    RUN();
    
    return 0;
}

```

## C. Shawarma Tent

题意：

有一个学校在$(sx,sy)$位置，有$n$个学生，每个人家的坐标在$(x_i,y_i)$

现在想要开一个$shawarma $，如果这个学生可以经过$shawarma $后到达学校依旧是最短距离，那么认定这个学生给$shawarma $带来贡献

求最大的贡献值和坐标

思路：

很显然把$shawarma $开在学校的上下左右是最优的，暴力算一遍取$max$即可

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-11 10:29:54
 *
 * */

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...)             \
    do {                      \
        cout << #x << " -> "; \
        err(x);               \
    } while (0)

void err() {
    cout << endl;
}

template <class T, class... Ts>
void err(const T& arg, const Ts&... args) {
    cout << arg << ' ';
    err(args...);
}

#define endl "\n"
#define all(A) A.begin(), A.end()
using pII = pair<int, int>;
using ll = long long;
using db = double;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e5 + 10;

int n, sx, sy;
int x[N], y[N];
int dir[4][2] = {1, 0, 0, 1, -1, 0, 0, -1};

void RUN() {
    cin >> n >> sx >> sy;
    for (int i = 1; i <= n; ++i) {
        cin >> x[i] >> y[i];
    }
    int Max = -1, _x = 0, _y = 0;
    for (int i = 0; i < 4; ++i) {
        int ex = sx + dir[i][0], ey = sy + dir[i][1];
        int cnt = 0;
        for (int i = 1; i <= n; ++i) {
            if (abs(x[i] - sx) + abs(y[i] - sy) ==
                abs(x[i] - ex) + abs(y[i] - ey) + 1) {
                ++cnt;
            }
        }
        if (cnt > Max) {
            Max = cnt, _x = ex, _y = ey;
        }
    }
    cout << Max << endl;
    cout << _x << " " << _y << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    RUN();
    
    return 0;
}

```

## D. Portals

题意：

有$n$个城堡，刚开始你有$k$个军队，有$m$条路径

你现在按照$1 - n$的顺序攻打城堡

每个城堡你需要至少$a_i$个军队，但是攻打城堡不耗费军队，攻打后你可以招$b_i$个军队，同时每个城堡有$c_i$的重要性

当你派至少一个军队留在城堡的时候算占领

占领只有当你刚攻打城堡的时候可以占领或者通过$m$条路径占领

$m$条有向路径，$u>v$

问最后占领的城堡的最大重要性

如果不能攻打所有城堡输出$-1$

思路：

首先判断$-1$

其次对每个城堡维护一个$Max_i$，表示最远的可以通过路径可以占领的城堡下标

对每个城堡按照$(c_i,i)$排序，一个城堡的重要性有价值只有$Max_i+1-n$的城堡在少一个士兵的情况下依旧可以攻打，然后贪心即可

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-11 10:35:29
 *
 * */

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...)             \
    do {                      \
        cout << #x << " -> "; \
        err(x);               \
    } while (0)

void err() {
    cout << endl;
}

template <class T, class... Ts>
void err(const T& arg, const Ts&... args) {
    cout << arg << ' ';
    err(args...);
}

#define endl "\n"
#define all(A) A.begin(), A.end()
using pII = pair<int, int>;
using ll = long long;
using db = double;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e5 + 10;

struct node {
    int a, b, c;

    void input() { cin >> a >> b >> c; }
} a[N];

int n, m, k;
int Max[N], remind[N];

void RUN() {
    cin >> n >> m >> k;
    for (int i = 1; i <= n; ++i) {
        a[i].input();
        Max[i] = i;
    }
    for (int i = 1, u, v; i <= m; ++i) {
        cin >> u >> v;
        Max[v] = max(Max[v], u);
    }
    remind[1] = k;
    for (int i = 1; i <= n; ++i) {
        if (remind[i] < a[i].a) {
            cout << "-1" << endl;
            return;
        }
        remind[i + 1] = remind[i] + a[i].b;
    }
    priority_queue<pII> pq;
    for (int i = 1; i <= n; ++i) {
        pq.push(pII(a[i].c, i));
    }
    int res = 0;
    while (!pq.empty()) {
        pII t = pq.top();
        pq.pop();
        bool F = true;
        for (int i = Max[t.second] + 1; i <= n + 1; ++i) {
            if (remind[i] - 1 < a[i].a) {
                F = false;
                break;
            }
        }
        if (F) {
            res += t.first;
            for (int i = Max[t.second] + 1; i <= n + 1; ++i) {
                remind[i]--;
            }
        }
    }
    cout << res << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    RUN();
    
    return 0;
}

```

## E. Common Number

题意：

定义
$$
f(x)=
\begin{equation}  
\left\{  
             \begin{array}{**lr**}  
             \frac{x}{2}  \quad & if \; x \; is \; even \\
             x-1 \quad & otherwise
             \end{array}  
\right.  
\end{equation}  
$$
定义$path\{x\}$为通过一系列计算得到的结果

求最大的$y$满足$|\{x|1\leq x \leq n, y \in path\{x\}|\geq k$

思路：

很显然这是一棵树

其中$2x$的父亲是$x$，$2x+1$的父亲是$2x$

所以就变成了最大的数，它的子树大小$\geq k$

那么很显然可以二分

我们分奇偶进行计算

偶数的时候要算上偶数加一的子树大小

对于一个数计算他的子树大小可以让他不断乘2计算

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-11 11:13:58
 *
 * */

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...)             \
    do {                      \
        cout << #x << " -> "; \
        err(x);               \
    } while (0)

void err() {
    cout << endl;
}

template <class T, class... Ts>
void err(const T& arg, const Ts&... args) {
    cout << arg << ' ';
    err(args...);
}

#define endl "\n"
#define all(A) A.begin(), A.end()
using pII = pair<int, int>;
using ll = long long;
using db = double;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e5 + 10;

ll n, k;

ll f(ll x) {
    ll res = 0, t = 1;
    while (x <= n) {
        res += min(t, n - x + 1);
        x <<= 1, t <<= 1;
    }
    return res;
}

void RUN() {
    cin >> n >> k;
    ll res = -1;
    ll l = 1, r = n / 2, x = -1;
    while (r - l >= 0) {
        ll mid = (l + r) >> 1;
        if (f(mid << 1) + f(mid << 1 | 1) >= k) {
            l = mid + 1;
            x = mid << 1;
        } else {
            r = mid - 1;
        }
    }
    res = max(res, x);
    l = 1, r = (n + 1) / 2, x = -1;
    while (r - l >= 0) {
        ll mid = (l + r) >> 1;
        if (f(2 * mid - 1) >= k) {
            l = mid + 1;
            x = 2 * mid - 1;
        } else {
            r = mid - 1;
        }
    }
    cout << max(res, x) << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    RUN();
    
    return 0;
}

```