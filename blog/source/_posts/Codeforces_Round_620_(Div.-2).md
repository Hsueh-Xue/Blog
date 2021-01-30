---
title: Codeforces Round 620 (Div. 2)
date:  2020-02-21 12:10:00 +0800
tags: [Codeforces]
description: Codeforces Round 620 (Div. 2)
---


## A. Two Rabbits

题意：两只$rabbit$，分别在$x,y$，相向而跳，一只跳$x$，一只跳$y$，问能能否整数秒相遇

思路：判断$y-x$能否整除$a+b$

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-21 10:07:03
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
using ll = long long;
using db = double;
using pII = pair<int, int>;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e5 + 10;

ll x, y, a, b;

void RUN() {
    cin >> x >> y >> a >> b;
    if ((y - x) % (a + b)) {
        cout << -1 << endl;
    } else {
        cout << (y - x) / (a + b) << endl;
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    int T;
    cin >> T;
    while (T--) {
        RUN();
    }
    
    return 0;
}

```

## B. Longest Palindrome

题意：给出$n$个长度为$m$的不同的字符串，你可以选任意个并随意拼接，问最长的回文串长度

思路：每次都把$t$和$rev(t)$选入即可

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-21 10:09:38
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
using ll = long long;
using db = double;
using pII = pair<int, int>;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e5 + 10;

int n, m, tot;
string s[N];
map<string, int> mp;
int res[N], mark[N];

void RUN() {
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) {
        cin >> s[i];
        mp[s[i]] = i;
    }
    int mid = -1;
    for (int i = 1; i <= n; ++i) {
        if (mark[i])
            continue;
        string t = s[i];
        reverse(all(t));
        if (t == s[i]) {
            if (mid == -1) {
                mid = i;
                mark[i] = 1;
            }
        } else if (mp.count(t) && !mark[mp[t]]) {
            res[++tot] = i;
            mark[i] = mark[mp[t]] = 1;
        }
    }
    int tmp = tot * 2 * m;
    if (mid != -1)
        tmp += m;
    cout << tmp << endl;
    if (!tmp)
        return;
    string ans = "";
    for (int i = 1; i <= tot; ++i) {
        ans += s[res[i]];
    }
    cout << ans;
    if (mid != -1)
        cout << s[mid];
    reverse(all(ans));
    cout << ans << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    RUN();
    
    return 0;
}

```

## C. Air Conditioner

题意：一个餐馆中有个空调，每分钟可以选择上调 $1$个单位的温度或下调 $1$ 个单位的温度，当然你也可以选择不变，初始的温度为 $m$，有 $n$个食客，每个食客会在 $t_i$ 时间点到达，他所能适应的最低温度是 $l_i$ ，最高温度是 $h_i$ ，他只会在 $t_i$ 时刻逗留。问能否让所有人舒服

思路：按照$t$排序，每次空调的温度会从$ [ l,r ] $
变成$ [l -  (t_i - t_{i-1})  , r +  (t_{i}  - t_{i - 1}) ] $
然后和顾客的$ [ l_i,r_i ] $去$min$看能否满足即可

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-21 10:20:04
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
using ll = long long;
using db = double;
using pII = pair<int, int>;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e5 + 10;

struct node {
    int l, r, t;

    void input() { cin >> t >> l >> r; }
    
    bool operator<(const node& other) const { return t < other.t; }
} a[N];

int n, m;

void RUN() {
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) {
        a[i].input();
    }
    sort(a + 1, a + 1 + n);
    int l = m, r = m;
    for (int i = 1; i <= n; ++i) {
        int diff = a[i].t - a[i - 1].t;
        l -= diff;
        r += diff;
        l = max(l, a[i].l);
        r = min(r, a[i].r);
        if (l > r) {
            cout << "NO" << endl;
            return;
        }
    }
    cout << "YES" << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    int T;
    cin >> T;
    while (T--) {
        RUN();
    }
    
    return 0;
}

```

## D. Shortest and Longest LIS

题意：给出一个长度为$n-1$的字符串，字符串内为大于号和小于号，显然让你随意排列$1-n$个数，使得满足字符串的大小顺序，然后$LIS$长度分别最大和最小

思路：刚开始构造一个$n-1$顺序排列，然后遇到小于号，将连续的小于号部分翻转即可，然后得到长度最小的，同理可以得到长度最大的

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-21 10:24:19
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
using ll = long long;
using db = double;
using pII = pair<int, int>;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e5 + 10;

int n, a[N];
char s[N];

void RUN() {
    cin >> n >> (s + 1);
    for (int i = 1, j = n; i <= n; ++i, --j) {
        a[i] = j;
    }
    for (int i = 1; i < n; ++i) {
        int j = i;
        while (j < n && s[j] == '<')
            ++j;
        reverse(a + i, a + j + 1);
        i = j;
    }
    for (int i = 1; i <= n; ++i) {
        cout << a[i] << " \n"[i == n];
    }
    for (int i = 1; i <= n; ++i) {
        a[i] = i;
    }
    for (int i = 1; i < n; ++i) {
        int j = i;
        while (j < n && s[j] == '>')
            ++j;
        reverse(a + i, a + j + 1);
        i = j;
    }
    for (int i = 1; i <= n; ++i) {
        cout << a[i] << " \n"[i == n];
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    int T;
    cin >> T;
    while (T--) {
        RUN();
    }
    
    return 0;
}

```

## E. 1-Trees and Queries

题意：给定一棵树，以及$q$次查询，每次查询给出$x,y,a,b,k$表示增加一条$x\rightarrow y$的边，问是否存在$a\rightarrow b$的路径，长度为$k$, 每条边可走多次

思路：很显然直接判断$a \rightarrow b,a \rightarrow x \rightarrow y \rightarrow b, a \rightarrow y \rightarrow x \rightarrow b$三种情况即可

（见过的最水的E题了）

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-21 10:33:14
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
using ll = long long;
using db = double;
using pII = pair<int, int>;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e5 + 10, DEG = 20;

int n, q;
vector<vector<int>> G;
int fa[N][DEG], deg[N];

void BFS(int root) {
    queue<int> q;
    deg[root] = 0;
    fa[root][0] = root;
    q.push(root);
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        for (int i = 1; i < DEG; ++i) {
            fa[u][i] = fa[fa[u][i - 1]][i - 1];
        }
        for (auto v : G[u]) {
            if (v == fa[u][0])
                continue;
            deg[v] = deg[u] + 1;
            fa[v][0] = u;
            q.push(v);
        }
    }
}

int LCA(int u, int v) {
    if (deg[u] > deg[v])
        swap(u, v);
    int hu = deg[u], hv = deg[v];
    int tu = u, tv = v;
    for (int det = hv - hu, i = 0; det; det >>= 1, ++i) {
        if (det & 1) {
            tv = fa[tv][i];
        }
    }
    if (tu == tv)
        return tu;
    for (int i = DEG - 1; i >= 0; --i) {
        if (fa[tu][i] == fa[tv][i]) {
            continue;
        }
        tu = fa[tu][i];
        tv = fa[tv][i];
    }
    return fa[tu][0];
}

int dis(int u, int v) {
    int root = LCA(u, v);
    return deg[u] + deg[v] - 2 * deg[root];
}

bool ok(int u, int v, int k) {
    if (dis(u, v) >= k && (dis(u, v) - k) % 2 == 0)
        return true;
    else
        return false;
}

void RUN() {
    cin >> n;
    G.resize(n + 1);
    for (int i = 1, u, v; i < n; ++i) {
        cin >> u >> v;
        G[u].push_back(v);
        G[v].push_back(u);
    }
    BFS(1);
    cin >> q;
    for (int _q = 1, x, y, a, b, k; _q <= q; ++_q) {
        cin >> x >> y >> a >> b >> k;
        // a->b
        int tmp = dis(a, b);
        if (tmp <= k && (tmp - k) % 2 == 0) {
            cout << "YES" << endl;
            continue;
        }
        // a->x->y->b
        tmp = dis(a, x) + dis(y, b) + 1;
        if (tmp <= k && (tmp - k) % 2 == 0) {
            cout << "YES" << endl;
            continue;
        }
        // a->y->x->b
        tmp = dis(a, y) + dis(x, b) + 1;
        if (tmp <= k && (tmp - k) % 2 == 0) {
            cout << "YES" << endl;
            continue;
        }
        cout << "NO" << endl;
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    RUN();
    
    return 0;
}

```

## F1. Animal Observation (easy version)

题意：Gildong 计划拍摄森林中的野生动物们。森林被划分为 $m$ 个地区，依次编号为 $1$ 到 $m$，他的拍摄计划持续 $n$ 天。

每一天，他会选择森林中连续的 $k$ 个地区，并且录一段长为 $2$ 天的录像。（如果是最后一天，那就录一段长度为$1$ 天的录像）这样所有在这两天之内在这 $k$ 个地区中出现过的动物都会被拍摄到。

他知道未来 $n$ 天内每一天每一个地区会出现多少野生动物。他想拍摄下尽可能多的野生动物。注意如果一个动物被拍摄了两次，那么只会被计算一次。

你的任务是求出拍摄到的动物数量的最大值。

思路：见F2

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-21 11:57:51
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
using ll = long long;
using db = double;
using pII = pair<int, int>;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e4 + 10, M = 100;

int n, m, k;
ll a[M][N], s[M][N];
ll f[N];

struct SEG {
    ll t[N << 2];

    void build(int id, int l, int r) {
        t[id] = -INFLL;
        if (l == r) {
            return;
        }
        int mid = (l + r) >> 1;
        build(id << 1, l, mid);
        build(id << 1 | 1, mid + 1, r);
    }
    
    void modify(int id, int l, int r, int pos, ll v) {
        if (l == r) {
            t[id] = v;
            return;
        }
        int mid = (l + r) >> 1;
        if (pos <= mid)
            modify(id << 1, l, mid, pos, v);
        else
            modify(id << 1 | 1, mid + 1, r, pos, v);
        t[id] = max(t[id << 1], t[id << 1 | 1]);
    }
    
    ll query(int id, int l, int r, int ql, int qr) {
        if (ql > qr)
            return 0;
        if (l >= ql && r <= qr) {
            return t[id];
        }
        int mid = (l + r) >> 1;
        ll res = -INFLL;
        if (ql <= mid)
            res = max(res, query(id << 1, l, mid, ql, qr));
        if (qr > mid)
            res = max(res, query(id << 1 | 1, mid + 1, r, ql, qr));
        return res;
    }
} seg[3];  // f, x, y

void RUN() {
    cin >> n >> m >> k;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            cin >> a[i][j];
            s[i][j] = s[i][j - 1] + a[i][j];
        }
    }
    seg[0].build(1, 1, m);
    seg[1].build(1, 1, m);
    seg[2].build(1, 1, m);
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j + k - 1 <= m; ++j) {
            f[j] = max(seg[0].query(1, 1, m, 1, j - k),
                       seg[0].query(1, 1, m, j + k, m));
            f[j] = max(f[j], seg[1].query(1, 1, m, j - k + 1, j) + s[i][j - 1]);
            f[j] = max(f[j],
                       seg[2].query(1, 1, m, j + 1, j + k) - s[i][j + k - 1]);
            f[j] = max(f[j], 0ll);
        }
        for (int j = 1; j + k - 1 <= m; ++j) {
            f[j] += s[i][j + k - 1] - s[i][j - 1] + s[i + 1][j + k - 1] -
                    s[i + 1][j - 1];
            seg[0].modify(1, 1, m, j, f[j]);
            seg[1].modify(1, 1, m, j, f[j] - s[i + 1][j + k - 1]);
            seg[2].modify(1, 1, m, j, f[j] + s[i + 1][j - 1]);
        }
    }
    cout << seg[0].t[1] << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    RUN();
    
    return 0;
}

```

## F2. Animal Observation (hard version)

题意：Gildong 计划拍摄森林中的野生动物们。森林被划分为 $m$ 个地区，依次编号为 $1$ 到 $m$，他的拍摄计划持续 $n$ 天。

每一天，他会选择森林中连续的 $k$ 个地区，并且录一段长为 $2$ 天的录像。（如果是最后一天，那就录一段长度为$1$ 天的录像）这样所有在这两天之内在这 $k$ 个地区中出现过的动物都会被拍摄到。

他知道未来 $n$ 天内每一天每一个地区会出现多少野生动物。他想拍摄下尽可能多的野生动物。注意如果一个动物被拍摄了两次，那么只会被计算一次。

你的任务是求出拍摄到的动物数量的最大值。



思路：$f[i][j]$表示第$i$天拍$[j,j+k-1]$的区间的前$i$天的最大值，那么
$$
\begin{equation*}
\begin{aligned}
	&g[i][j] = 
	\left\{
	\begin{aligned}
	& f[i-1][l]-(S[i][l+k-1]-S[i][j-1]) \quad &if \ l \in [j-k, j]\\
	& f[i-1][l]-(S[i][j+k-1]-S[i][l-1]) \quad &if \ l \in [j, j+k]\\
	& f[i-1][l] &otherwise
	\end{aligned}
	\right.\\
	&f[i][j] = g[i][j] + S[i][j+k-1] - S[i][j-1] + S[i + 1][j+k-1] - S[i + 1][j-1]
\end{aligned}
\end{equation*}
$$
所以可以用$segment \ tree$维护三种情况

```C++

/*
 *
 * Author: Hsueh-
 * Date: 2020-02-21 11:41:34
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
using ll = long long;
using db = double;
using pII = pair<int, int>;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const int N = 2e4 + 10, M = 100;

int n, m, k;
ll a[M][N], s[M][N];
ll f[N];

struct SEG {
    ll t[N << 2];

    void build(int id, int l, int r) {
        t[id] = -INFLL;
        if (l == r) {
            return;
        }
        int mid = (l + r) >> 1;
        build(id << 1, l, mid);
        build(id << 1 | 1, mid + 1, r);
    }
    
    void modify(int id, int l, int r, int pos, ll v) {
        if (l == r) {
            t[id] = v;
            return;
        }
        int mid = (l + r) >> 1;
        if (pos <= mid)
            modify(id << 1, l, mid, pos, v);
        else
            modify(id << 1 | 1, mid + 1, r, pos, v);
        t[id] = max(t[id << 1], t[id << 1 | 1]);
    }
    
    ll query(int id, int l, int r, int ql, int qr) {
        if (ql > qr)
            return 0;
        if (l >= ql && r <= qr) {
            return t[id];
        }
        int mid = (l + r) >> 1;
        ll res = -INFLL;
        if (ql <= mid)
            res = max(res, query(id << 1, l, mid, ql, qr));
        if (qr > mid)
            res = max(res, query(id << 1 | 1, mid + 1, r, ql, qr));
        return res;
    }
} seg[3];  // f, x, y

void RUN() {
    cin >> n >> m >> k;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            cin >> a[i][j];
            s[i][j] = s[i][j - 1] + a[i][j];
        }
    }
    seg[0].build(1, 1, m);
    seg[1].build(1, 1, m);
    seg[2].build(1, 1, m);
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j + k - 1 <= m; ++j) {
            f[j] = max(seg[0].query(1, 1, m, 1, j - k),
                       seg[0].query(1, 1, m, j + k, m));
            f[j] = max(f[j], seg[1].query(1, 1, m, j - k + 1, j) + s[i][j - 1]);
            f[j] = max(f[j],
                       seg[2].query(1, 1, m, j + 1, j + k) - s[i][j + k - 1]);
            f[j] = max(f[j], 0ll);
        }
        for (int j = 1; j + k - 1 <= m; ++j) {
            f[j] += s[i][j + k - 1] - s[i][j - 1] + s[i + 1][j + k - 1] -
                    s[i + 1][j - 1];
            seg[0].modify(1, 1, m, j, f[j]);
            seg[1].modify(1, 1, m, j, f[j] - s[i + 1][j + k - 1]);
            seg[2].modify(1, 1, m, j, f[j] + s[i + 1][j - 1]);
        }
    }
    cout << seg[0].t[1] << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    cout << fixed << setprecision(20);

    RUN();
    
    return 0;
}

```