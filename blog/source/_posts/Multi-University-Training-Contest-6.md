---
title: 	 Multi-University-Training-Contest-6
date:   2019-08-08 11:40:00 +0800
tags: [HDU多校]
description: Multi-University-Training-Contest-6
---


## B. Nonsense Time

题意：给定一个序列，刚开始都是冻结状态，每次加入一个数后问$LIS$长度，数据随机

思路：由于数据随机，所以$LIS$长度的期望是$\sqrt{n}$，那么倒过来做，如果删除的数在$LIS$中，则暴力重构$LIS$，最多进行$\sqrt{n}$次，复杂度为$O(\sqrt{n} \cdot n \cdot log_2n)$

```C++

#include <bits/stdc++.h>

using namespace std;

#define N 500010
#define INF 0x3f3f3f3f

int n, a[N], b[N], ans[N], pre[N], nxt[N], used[N], bit[N], f[N], g[N];

void solve() {
    for (int i = nxt[0]; i <= n + 1; i = nxt[i]) {
        used[i] = 0;
        int Max = 0;
        for (int j = a[i]; j; j -= j & -j) {
            if (f[Max] < f[bit[j]]) {
                Max = bit[j];
            }
        }
        f[i] = f[Max] + 1;
        g[i] = Max;
        for (int j = a[i]; j <= n + 2; j += j & -j) {
            if (f[bit[j]] < f[i]) {
                bit[j] = i;
            }
        }
    }
    for (int i = nxt[0]; i <= n + 1; i = nxt[i]) {
        for (int j = a[i]; j <= n + 2; j += j & -j) {
            bit[j] = 0;
        }
    }
    for (int i = n + 1; i; i = g[i]) {
        used[i] = 1;
    }
}

int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        for (int i = 1; i <= n; ++i) {
            scanf("%d", a + i);
            a[i]++;
        }
        a[0] = 1, a[n + 1] = n + 2;
        for (int i = 0; i <= n + 1; ++i) {
            pre[i] = i - 1;
            nxt[i] = i + 1;
            bit[i] = used[i] = 0;
        }
        bit[n + 2] = 0;
        solve();
        for (int i = 1; i <= n; ++i) {
            scanf("%d", b + i);
        }
        for (int i = n; i >= 1; --i) {
            ans[i] = f[n + 1] - 1;
            int p = b[i];
            pre[nxt[p]] = pre[p];
            nxt[pre[p]] = nxt[p];
            if (used[p]) {
                solve();
            }
        }
        for (int i = 1; i <= n; ++i) {
            printf("%d%c", ans[i], " \n"[i == n]);
        }
    }
```

## E. Snowy Smile

题意：给出$n$个点的坐标和权值，问最大子矩阵和。

思路：离散化后为$n\cdot n$的矩形，由于只有$2000$个点，所以枚举上边界，拓展下边界，用线段树维护最大子段和。

```C++

#include <bits/stdc++.h>

using namespace std;

#define ll long long
#define pii pair <int, int>
#define fi first
#define se second
#define N 2010
int n;
vector<vector<pii>> vec;

struct Hash {
    int a[N], cnt;

    void init() {
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

struct node {
    int x, y, w;

    node() {}
    
    void scan() {
        scanf("%d%d%d", &x, &y, &w);
        hx.add(x);
        hy.add(y);
    }
} a[N];

struct SEG {
    struct node {
        //0表示前缀最大  1表示后缀最大 2表示区间最大和 sum表示区间和
        ll Max[3], sum;

        node() {
            Max[0] = Max[1] = Max[2] = sum = 0;
        }
    
        void add(ll w) {
            sum += w;
            Max[0] = Max[1] = Max[2] = max(0ll, sum);
        }
    
        node operator+(const node &other) const {
            node res = node();
            res.Max[0] = max(Max[0], sum + other.Max[0]);
            res.Max[1] = max(other.Max[1], Max[1] + other.sum);
            res.Max[2] = max(Max[2], other.Max[2]);
            res.Max[2] = max(res.Max[2], Max[1] + other.Max[0]);
            res.sum = sum + other.sum;
            return res;
        }
    } t[N << 2];
    
    void build(int id, int l, int r) {
        t[id] = node();
        if (l == r) return;
        int mid = (l + r) >> 1;
        build(id << 1, l, mid);
        build(id << 1 | 1, mid + 1, r);
    }
    
    void update(int id, int l, int r, int pos, ll w) {
        if (l == r) {
            t[id].add(w);
            return;
        }
        int mid = (l + r) >> 1;
        if (pos <= mid) update(id << 1, l, mid, pos, w);
        else update(id << 1 | 1, mid + 1, r, pos, w);
        t[id] = t[id << 1] + t[id << 1 | 1];
    }
} seg;

int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        hx.init();
        hy.init();
        scanf("%d", &n);
        for (int i = 1; i <= n; ++i) a[i].scan();
        hx.work();
        hy.work();
        vec.clear();
        vec.resize(hx.cnt + 10);
        for (int i = 1; i <= n; ++i) {
            vec[hx.get(a[i].x)].push_back(pii(hy.get(a[i].y), a[i].w));
        }
        ll res = 0;
        for (int i = 1; i <= hx.cnt; ++i) {
            seg.build(1, 1, hy.cnt);
            for (int j = i; j <= hx.cnt; ++j) {
                for (auto it : vec[j]) {
                    seg.update(1, 1, hy.cnt, it.fi, it.se);
                }
                res = max(res, seg.t[1].Max[2]);
            }
        }
        printf("%lld\n", res);
    }
```

## F. Faraway

题意：有$n$个士兵以及一个$target$，$target$对于每个士兵满足$(\vert x_i-x_e\vert + \vert y_i- y_e\vert) mod \; k_i=t_i$，问方案数。

思路：对于每个士兵，合法的$target$是以菱形扩散的，每个士兵可以将图划分为四个部分，每个部分都是不断循环的。那么$n$个士兵将图划分为$(n+1)\cdot (n+1)$个部分，对于每个部分都是一个以$lcm\cdot lcm$的小矩阵不断填充的大矩阵，分别做即可。

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 256

int LCM(int a, int b) {
    return a * b / __gcd(a, b);
}

struct node {
    int x, y, k, t;

    node() {}
    
    node(int _x, int _y, int _k, int _t) {
        x = _x;
        y = _y;
        k = _k;
        t = _t;
    }
    
    void input() {
        scanf("%d %d %d %d", &x, &y, &k, &t);
    }
    
    bool clac(int _x, int _y) {
        return (abs(x - _x) + abs(y - _y)) % k == t;
    }
} a[N];

int n, m;
int lcm;
int mp[N][N];
int X[N], Y[N];

ll solve(int sx, int sy, int ex, int ey, int up = 0, int right = 0) {
    for (int i = 0; i < lcm; ++i) {
        for (int j = 0; j < lcm; ++j) {
            int cnt = 0;
            for (int o = 1; o <= n; ++o) {
                cnt += a[o].clac(sx + i, sy + j);
            }
            mp[i][j] = (cnt == n);
            if (i > 0) {
                mp[i][j] += mp[i - 1][j];
            }
            if (j > 0) {
                mp[i][j] += mp[i][j - 1];
            }
            if (i > 0 && j > 0) {
                mp[i][j] -= mp[i - 1][j - 1];
            }
        }
    }
    ll dx = ex - sx + right, dy = ey - sy + up;
    ll t1 = (dx - 1) / lcm, t2 = (dy - 1) / lcm;
    ll res = 0;
    res += t1 * t2 * mp[lcm - 1][lcm - 1];
    int eey = dy - t2 * lcm;
    res += mp[lcm - 1][eey - 1] * t1;
    int eex = dx - t1 * lcm;
    res += mp[eex - 1][lcm - 1] * t2;
    res += mp[eex - 1][eey - 1];
    return res;
}

int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d %d", &n, &m);
        X[0] = 0, Y[0] = 0;
        X[++X[0]] = 0, X[++X[0]] = m;
        Y[++Y[0]] = 0, Y[++Y[0]] = m;
        for (int i = 1; i <= n; ++i) {
            a[i].input();
            X[++X[0]] = a[i].x;
            Y[++Y[0]] = a[i].y;
        }
        lcm = a[1].k;
        for (int i = 1; i <= n; ++i) {
            lcm = LCM(lcm, a[i].k);
        }
        sort(X + 1, X + 1 + X[0]);
        sort(Y + 1, Y + 1 + Y[0]);
        X[0] = unique(X + 1, X + 1 + X[0]) - X - 1;
        Y[0] = unique(Y + 1, Y + 1 + Y[0]) - Y - 1;
        ll ans = 0;
        for (int i = 1; i < X[0]; ++i) {
            for (int j = 1; j < Y[0]; ++j) {
                ans += solve(X[i], Y[j], X[i + 1], Y[j + 1], j == Y[0] - 1, i == X[0] - 1);
            }
        }
        printf("%lld\n", ans);
    }
```

## H. TDL

题意：$f(n, m)$表示第$m$个和$n$互质的数$x(x>n)$，现在给定$m, k$，问最小的$n$满足$(f(n, m) - n)\oplus n=k$

思路：$f(n-m)-n$表示的是第$m$个与$n$互质的数，那么枚举$f(n-m)-n$，可以通过$f(n, m)-n=n\oplus k$得到$n$，暴力$check$即可。

```C++

#include <bits/stdc++.h>

using namespace std;

#define ll long long
#define INFLL 0x3f3f3f3f3f3f3f3f

ll gcd(ll a, ll b) {
    return b ? gcd(b, a % b) : a;
}

ll k;
int m;

int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%lld%d", &k, &m);
        ll res = INFLL;
        for (int i = 1; i <= 800; ++i) {
            ll n = k ^i;
            if (__gcd(1ll * i, n) != 1) continue;
            int cur = 0;
            for (int j = 1; j <= i; ++j) {
                cur += (__gcd(j + n, n) == 1);
                if (cur > m) break;
            }
            if (cur == m) {
                res = min(res, n);
            }
        }
        if (res == INFLL) res = -1;
        printf("%lld\n", res);
    }
```   

## L. Stay Real

题意：给出一个小根堆，两个人轮流取，只能取没有儿子节点的点，两人都想最大化收益，问最终收益。

思路：由于是小根堆，所以儿子节点权值大于父亲节点，贪心即可。

```C++

#include <bits/stdc++.h>

using namespace std;

#define ll long long
#define N 200100
#define pii pair <int, int>
#define fi first
#define se second
int n, a[N];
int vis[N];

int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        for (int i = 1; i <= n * 2 + 10; ++i) vis[i] = 0;
        for (int i = 1; i <= n; ++i) scanf("%d", a + i), vis[i] = 1;
        ll res[2] = {0, 0};
        priority_queue<pii > pq;
        for (int i = n; i >= 1; --i) {
            if (vis[n * 2] == 0 && vis[n * 2 + 1] == 0) {
                pq.emplace(a[i], i);
            }
        }
        int turn = 0;
        while (!pq.empty()) {
            pii it = pq.top();
            pq.pop();
            if (vis[it.se] == 0) continue;
            res[turn] += it.fi;
            turn ^= 1;
            vis[it.se] = 0;
            int fa = it.se / 2;
            if (fa && vis[fa * 2] == 0 && vis[fa * 2 + 1] == 0) {
                pq.push(pii(a[fa], fa));
            }
        }
        printf("%lld %lld\n", res[0], res[1]);
    }
```   

