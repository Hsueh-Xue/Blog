---
title: 	Codeforces Round 578 (Div. 2)
date:   2019-08-14 09:50:00 +0800
tags: [Codeforces]
description: Codeforces Round 578 (Div. 2)
---

## A. Hotelier

签到

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 100010

int n;
string s;
int a[10];

void solve() {
    memset(a, 0, sizeof a);
    for (char i : s) {
        if (i == 'L') {
            for (int j = 0; j < 10; ++j) {
                if (!a[j]) {
                    a[j] = 1;
                    break;
                }
            }
        } else if (i == 'R') {
            for (int j = 9; j >= 0; --j) {
                if (!a[j]) {
                    a[j] = 1;
                    break;
                }
            }
        } else {
            a[i - '0'] = 0;
        }
    }
    for (int i = 0; i < 10; ++i) {
        cout << a[i];
    }
    cout << endl;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    while (cin >> n) {
        cin >> s;
        solve();
    }
    return 0;
}

```

## B.Block Adventure

题意：有$n$个房子，每个房子有一个高度$h_i$，从$i$跳到$i+1$的条件是$\vert h_i-h_{i+1}\vert\leq k$

同时刚开始有$m$个积木，可以选择使用或者拆房子。

问能否到达$n$

签到

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 100010

int n, m, k;
ll a[N];

bool solve() {
    for (int i = 1; i < n; ++i) {
        if (a[i] < a[i + 1]) {
            if (a[i + 1] - a[i] > k) {
                if (a[i + 1] - a[i] - k > m) {
                    return false;
                } else {
                    m -= a[i + 1] - a[i] - k;
                }
            } else {
                m += min(k - (a[i + 1] - a[i]), a[i]);
            }
        } else {
            m += a[i] - a[i + 1] + min(1ll * k, a[i + 1]);
        }
    }
    return true;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    int T;
    cin >> T;
    while (T--) {
        cin >> n >> m >> k;
        for (int i = 1; i <= n; ++i) {
            cin >> a[i];
        }
        cout << (solve() ? "YES\n" : "NO\n");
    }
    return 0;
}

```

## C. Round Corridor

题意：有一个圆盘，外面分成$n$个扇形，里面$m$个扇形，问两个坐标能否到达对方。

思路：计算$gcd(n, m)$那么如果$x$在外面区域，那么它属于$\frac{x-1}{\frac{n}{gcd(n, m)}}$，在里面属于$\frac{x-1}{\frac{m}{gcd(n, m)}}$

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 100010

ll n, m, q, sx, sy, ex, ey;

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    while (cin >> n >> m >> q) {
        ll G = __gcd(m, n);
        n /= G, m /= G;
        while (q--) {
            cin >> sx >> sy >> ex >> ey;
            int res1 = 0, res2 = 0;
            if (sx == 1) {
                res1 = (sy - 1) / n;
            } else {
                res1 = (sy - 1) / m;
            }
            if (ex == 1) {
                res2 = (ey - 1) / n;
            } else {
                res2 = (ey - 1) / m;
            }
            cout << (res1 == res2 ? "YES" : "NO") << endl;
        }
    }
    return 0;
}

```

## D.White Lines

题意：有一个$n\cdot n$的矩形，里面有$W、B$，在只能染色一块$k\cdot k$的正方形的时候，只有$W$的行和只有$W$的列的最大值。

思路：处理第$i$行的出现$B$的左端点$L$右端点$R$，当$R-L+1\geq k$的时候，那么$k\cdot k$正方形的左端点出现在$(max(1, i-k+1)， max（1， R - k + 1）), (i, L)$范围内才能将这一行染色。同样道理处理每一列，然后求二维矩阵前缀和

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 2010
#define INF 0x3f3f3f3f

int n, k;
char s[N][N];
int a[N][N];

void add(int x1, int y1, int x2, int y2) {
    a[x1][y1]++;
    a[x1][y2 + 1]--;
    a[x2 + 1][y1]--;
    a[x2 + 1][y2 + 1]++;
}

int solve() {
    memset(a, 0, sizeof a);
    for (int i = 1; i <= n; ++i) {
        int L = INF, R = -INF;
        for (int j = 1; j <= n; ++j) {
            if (s[i][j] == 'B') {
                L = min(L, j);
                R = max(R, j);
            }
        }
        if (L != INF) {
            if (R - L + 1 <= k) {
                add(max(1, i - k + 1), max(1, R - k + 1), i, L);
            }
        } else {
            add(1, 1, n, n);
        }
    }
    for (int j = 1; j <= n; ++j) {
        int L = INF, R = -INF;
        for (int i = 1; i <= n; ++i) {
            if (s[i][j] == 'B') {
                L = min(L, i);
                R = max(R, i);
            }
        }
        if (L != INF) {
            if (R - L + 1 <= k) {
                add(max(1, R - k + 1), max(1, j - k + 1), L, j);
            }
        } else {
            add(1, 1, n, n);
        }
    }
    int res = 0;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++j) {
            a[i][j] += a[i - 1][j] + a[i][j - 1] - a[i - 1][j - 1];
            res = max(res, a[i][j]);
        }
    }
    return res;

}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    while (cin >> n >> k) {
        for (int i = 1; i <= n; ++i) {
            cin >> (s[i] + 1);
        }
        cout << solve() << endl;
    }
    return 0;
}

```

## E.Compress Words

题意：给出$n$个字符串拼接，选择前面的后缀和后面的前缀的最长部分去掉一个，求拼接后的字符串

思路：$KMP$模板题…失配位置就是我们需要求的拼接长度，注意每次只需要做前后字符串最短长度部分即可。

```C++

#include <bits/stdc++.h>

using namespace std;

#define N 1000010

int nxt[N];

void get_next(string x) {
    nxt[0] = nxt[1] = 0;
    for (int i = 1, len = x.size(); i < len; ++i) {
        int j = nxt[i];
        while (j && x[i] != x[j])
            j = nxt[j];
        nxt[i + 1] = (x[j] == x[i]) ? j + 1 : 0;
    }
}

int kmp(const string &s, string p) {
    get_next(p);
    int j = 0;
    for (char i : s) {
        while (j && p[j] != i)
            j = nxt[j];
        if (p[j] == i)
            ++j;
    }
    return j;
}

int n;
string ans, p, tmp;

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    while (cin >> n) {
        for (int i = 1; i <= n; ++i) {
            if (i == 1) {
                cin >> ans;
                continue;
            }
            cin >> p;
            int len = min(ans.size(), p.size());
            tmp.clear();
            for (int j = ans.size() - len, sze = ans.size(); j < sze; ++j) {
                tmp += ans[j];
            }
            len = kmp(tmp, p);
            for (int j = len, sze = p.size(); j < sze; ++j) {
                ans += p[j];
            }
        }
        cout << ans << endl;
    }
    return 0;
}

```

## F. Graph Traveler

题意：有$n$个点，每个点有一个$k, m$，一个人刚开始有一个权值$c$，如果他走到$x$点，那么他的$c$会变成$c+k_x$，他下一步走向$G[x][c\% m_x]$，有$q$次查询，每次给出$x, c$，问有多少个点是不断经过的。

思路：注意到$m$取值只有$[1,10]$，那么一个点只有$2520(ps:lcm(1\cdots 10))$种本质不同的$c$，记忆化即可…

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 1010
#define M 2560
#define INF 0x3f3f3f3f

const int p = 2520;

int n, m, k[N], q;
vector<vector<int> > G;
int f[N * M];

int gao(int x, int y) {
    int now = x * p + y;
    if (f[now] != -1) {
        return f[now];
    }
    set<int> se;
    stack<int> st;
    se.insert(now);
    st.push(now);
    while (true) {
        x = now / p, y = now % p;
        int ny = ((y + k[x]) % p + p) % p;
        int nx = G[x][ny % G[x].size()];
        int nxt = nx * p + ny;
        if (f[nxt] != -1) {
            while (!st.empty()) {
                f[st.top()] = f[nxt];
                st.pop();
            }
            return f[nxt];
        }
        if (se.find(nxt) != se.end()) {
            set<int> tmp;
            while (true) {
                int Top = st.top();
                tmp.insert(Top / p);
                st.pop();
                if (Top == nxt) {
                    break;
                }
            }
            for (auto it : se) {
                f[it] = tmp.size();
            }
            return tmp.size();
        }
        now = nxt;
        st.push(now);
        se.insert(now);
    }
    return 0;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    while (cin >> n) {
        G.clear();
        G.resize(n + 1);
        for (int i = 1; i <= n; ++i) {
            cin >> k[i];
        }
        for (int i = 1; i <= n; ++i) {
            cin >> m;
            G[i].resize(m);
            for (auto &it : G[i]) {
                cin >> it;
            }
        }
        memset(f, -1, sizeof f);
        cin >> q;
        while (q--) {
            int x, y;
            cin >> x >> y;
            y = (y % p + p) % p;
            cout << gao(x, y) << "\n";
        }
    }
    return 0;
}

```