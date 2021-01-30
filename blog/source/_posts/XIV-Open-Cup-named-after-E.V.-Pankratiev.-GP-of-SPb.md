---
title: 	XIV Open Cup named after E.V. Pankratiev. GP of SPb
date:   2019-08-13 16:20:00 +0800
tags: [ACM]
description: XIV Open Cup named after E.V. Pankratiev. GP of SPb
---


## A. Bracket Expression

签到

```C++

#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define N 110
char s[N];
int n, id[N], sta[N], top; 

ll gao(int l, int r) {
    if (l == r + 1) return 1;
    if (l + 1 == r) return 2;
    ll res = 1;
    for (int i = l; i <= r; ) {
        res *= 1ll + gao(i + 1, id[i] - 1);
        i = id[i] + 1;
    }
    return res;
}

int main() {
    freopen("bracket-expression.in", "r", stdin);
    freopen("bracket-expression.out", "w", stdout);
    while (gets(s + 1)) {
        top = 0;
        n = strlen(s + 1);
        if (n == 0) {
            puts("1");
            continue;
        }
        for (int i = 1; i <= n; ++i) {
            if (s[i] == '(') {
                sta[++top] = i;
            } else {
                assert(top);
                int T = sta[top];
                id[T] = i;
                id[i] = T; 
                --top;
            }
        }
        printf("%lld\n", gao(1, n)); 
    }
    return 0;
}

```

## B. Checkers

题意：有$n$台游戏机，每台游戏机有游玩历史，$Alice$从$1−n$循环去玩每个游戏机，每个游戏机可以玩$1$到$2$次。
定义了一条规则判断$Alice$当前跟这个游戏机所玩的是$W$还是$B$。
规则就是拿$Alice$的游戏历史跟机器的游戏历史向上比对，如果找到一条不同的，当前玩的局面就和那次交换。
如果没有找到，$Alice$就可以玩$W$
$Alice$想要尽可能多的玩$W$，最多能玩几次？

思路：爆搜每种状态，复杂度$O(2^n\cdot k)$

```C++

#include <bits/stdc++.h>
using namespace std;

#define N 30

int n, k;
string s[N];
int ans;

//B 1 W 0
int judge(string engin, vector<int> res) {
    for (int i = engin.size() - 1, j = res.size() - 1; i >= 0 && j >= 0; --i, --j) {
        if (engin[i] == 'B' && res[j] == 0) {
            return 1;
        }
        if (engin[i] == 'W' && res[j] == 1) {
            return 0;
        }
    }
    return 0;
}

void DFS(int cur, int pos, int cnt, vector<int> res) {
    if (cur >= k) {
        ans = max(ans, cnt);
        return ;
    }
    for (int i = 1; i + cur <= k && i <= 2; ++i) {
        string tmp = s[pos];
        vector<int> temp = res;
        int tmpcnt = cnt;
        for (int j = 1; j <= i; ++j) {
            int nxt = judge(s[pos], temp);
            temp.push_back(nxt);
            s[pos].push_back(nxt ? 'W' : 'B');
            if (nxt == 0) {
                tmpcnt++;
            }
        }
        DFS(cur + i, pos % n + 1, tmpcnt, temp);
        s[pos] = tmp;
    }
}

int main() {
    freopen("checkers.in", "r", stdin);
    freopen("checkers.out", "w", stdout);
    ios::sync_with_stdio(false);
    cin.tie(0); cout.tie(0);
    while (cin >> n >> k) {
        for (int i = 1; i <= n; ++i) {
            cin >> s[i];
        }
        ans = 0;
        vector<int> res;
        DFS(0, 1, 0, res);
        cout << ans <<"\n";
    }
    return 0;
}

```

## E. Four Prime Numbers

题意：给出一个数，为它能被分解四个素数的方案数

思路：$f[i]$表示用两个素数拼出$i$的方案数，答案为$\sum_{i=0}^{n}f[i]\cdot f[n-i]$

复杂度为$O(\frac{n ^ 2}{log_2^2n})$

```C++

#include <bits/stdc++.h>
using namespace std;

#define ll long long
const int N = 1e5 + 10;
int pri[N], check[N], ptot;
void init() {
    memset(check, 0, sizeof check);
    ptot = 0;
    for (int i = 2; i < N; ++i) {
        if (!check[i]) {
            pri[++ptot] = i;
        }
        for (int j = 1; j <= ptot; ++j) {
            if (1ll * i * pri[j] >= N) break;
            check[i * pri[j]] = 1;
            if (i % pri[j] == 0) {
                break;
            }
        }
    }
}
int n;
ll f[N];

int main() {
    freopen("fourprimes.in", "r", stdin); 
    freopen("fourprimes.out", "w", stdout); 
    init();
    while (scanf("%d", &n) != EOF) {
        memset(f, 0, sizeof f);
        for (int i = 1; i <= ptot; ++i) {
            if (pri[i] + pri[i] <= n) {
                f[pri[i] + pri[i]] += 1;
            }
            for (int j = 1; j < i && pri[i] + pri[j] <= n; ++j) {
                f[pri[i] + pri[j]] += 2;
            }
        }
        ll res = 0;
        for (int i = 1; i <= n; ++i) {
            res += f[i] * f[n - i];
        }
        printf("%lld\n", res);
    }
    return 0;
}

```

## F. Set Intersection

打表

```C++

#include <bits/stdc++.h>

using namespace std;

int n, l, m;

int main() {
    freopen("intset.in", "r", stdin);
    freopen("intset.out", "w", stdout);
    while (scanf("%d %d %d", &n, &l, &m) != EOF) {
        int k = round(1.0 * l * m / n);
        printf("%d\n", k);
    }
    return 0;
}

```

## G. Medals

题意：有$n$个运动员，每个运动员只能参加一个项目，一个项目只能由一个运动员参加。
现在知道哪些运动员去参加哪些项目可以获得什么类型的奖牌。
现在要求给出一种方案，要求保证第一种类型的奖牌数量最多的情况下再保证第二种类型的奖牌数量最多，以此类推

思路：将第$i$种奖牌权值赋为$1001^{10-i}$

跑费用流。

为了保证费用最大，因此每次只有在$dis[T]<0$的时候才能增广

```C++

#include <bits/stdc++.h>

using namespace std;

typedef __int128 ll;

const int MAXN = 10000;
const int MAXM = 1000000;

struct Edge{
    int to, nxt, cap;
    ll cost;
    
    Edge() {}
    
    Edge (int  _to, int _nxt, int _cap, ll _cost) {
        to = _to;
        nxt = _nxt;
        cap = _cap;
        cost = _cost;
    }
} edge[MAXM << 1];

int n;
int head[MAXN], tot;
int pre[MAXN], pe[MAXN];
ll dis[MAXN];
bool vis[MAXN];
int num[MAXN];
ll prize[MAXN];
ll INF = 1LL << 60;

void out(ll x) {
    if (x) {
        out(x / 10);
        printf("%d", (int)(x % 10));
    }
}

void Init() {
    tot = 0;
    memset(head, -1, sizeof head);
}

void addedge(int u,int v, int cap, ll cost) {
    edge[tot] = Edge(v, head[u], cap, cost); head[u] = tot++;
    edge[tot] = Edge(u, head[v], 0, -cost); head[v] = tot++;
}

bool SPFA(int S, int T) {
    queue<int> q;
    for (int i = 0; i <= T; ++i) {
        dis[i] = INF;
        pre[i] = -1;
        vis[i] = false;
    }
    dis[S] = 0;
    vis[S] = true;
    q.push(S);
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        vis[u] = false;
        for (int i = head[u]; ~i; i = edge[i].nxt) {
            int v = edge[i].to;
            if (edge[i].cap && dis[v] > dis[u] + edge[i].cost) {
                dis[v] = dis[u] + edge[i].cost;
                pre[v] = u;
                pe[v] = i;
                if (!vis[v]) {
                    vis[v] = true;
                    q.push(v);
                }
            }
        }
    }
    return dis[T] < 0;
}

void solve(int S, int T) {
    ll cost = 0;
    while (SPFA(S, T)) {
        cost -= dis[T];
        for (int i = T; i != S; i = pre[i]) {
            edge[pe[i]].cap--;
            edge[pe[i] ^ 1].cap++;
        }
    }
    for (int i = 10; i >= 1; --i) {
        num[i] = cost % 1001;
        cost /= 1001;
    }
    for (int i = 1; i <= 10; ++i) {
        printf("%d%c", num[i], " \n"[i == 10]);
    }
    for (int i = 1; i <= n; ++i) {
        int res = 0;
        for (int j = head[i]; ~j; j = edge[j].nxt) {
            if (edge[j].to != S && (!edge[j].cap)) {
                res = edge[j].to - n;
                break;
            }
        }
        printf("%d%c", res, " \n"[i == n]);
    }
}

int main() {
    freopen("medals.in", "r", stdin);
    freopen("medals.out", "w", stdout);
    INF = INF * INF;
    prize[0] = 1ll;
    for (int i = 1; i <= 11; ++i) {
        prize[i] = prize[i - 1] * 1001;
    }
    while (scanf("%d", &n) != EOF) {
        Init();
        for (int i = 1, k, a, b; i <= n; ++i) {
            scanf("%d", &k);
            for (int j = 1; j <= k; ++j) {
                scanf("%d %d", &a, &b);
                addedge(i, a + n, 1, -prize[10 - b]);
            }
        }
        int S = 0, T = n + 1001;
        for (int i = 1; i <= n; ++i) {
            addedge(S, i, 1, 0);
        }
        for (int i = n + 1; i < T; ++i) {
            addedge(i, T, 1, 0);
        }
        solve(S, T);
    }
    return 0;
}

```

## H. Reachability

题意：给出一张$n$个点的图，每次操作增加或删去边，输出可达矩阵的值。

思路：$bitsset$优化跑闭包

```C++

#include <bits/stdc++.h>
using namespace std;

#define N 510
#define uint unsigned int
int n, q;
uint A[N], B[N];
bitset <N> G[N], H[N];

uint gao() {
    for (int i = 1; i <= n; ++i) {
        H[i] = G[i];
    }
    for (int k = 1; k <= n; ++k) {
        for (int i = 1; i <= n; ++i) {
            if (H[i][k]) {
                H[i] |= H[k];
            }
        }
    }
    uint res = 0;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (i != j && H[i][j]) {
                res += A[i - 1] * B[j - 1];
            } 
        }
    }
    return res;
}

int main() {
    freopen("reachability.in", "r", stdin);
    freopen("reachability.out", "w", stdout);
    A[0] = B[0] = 1;
    scanf("%d%d%u%u", &n, &q, A + 1, B + 1);
    for (int i = 1; i <= n; ++i) {
        A[i] = A[i - 1] * A[1];
        B[i] = B[i - 1] * B[1];
    }
    for (int i = 1; i <= n; ++i) G[i].reset();
    char op1[5], op2[5]; int v, a[N];
    for (int _q = 1; _q <= q; ++_q) {
        scanf("%s%s%d%d", op1, op2, &v, a);
        for (int i = 1; i <= a[0]; ++i) {
            scanf("%d", a + i);
            if (op2[0] == 'o') {
                G[v][a[i]] = G[v][a[i]] ^ 1;
            } else {
                G[a[i]][v] = G[a[i]][v] ^ 1;
            }
        }
        printf("%u\n", gao());
    }
    return 0;
}

```

## J. Snakes on the Stone

题意：有若干条蛇躺在平面上，要求蛇与蛇之间不打结，每个点最多有两条蛇在那里交叉，问如果安排交叉点哪条蛇在上，哪条蛇在下使得所有蛇不交叉。

思路：感性理解一下，第一次经过交叉点向下，第二次向上。

```C++

#include <bits/stdc++.h>
using namespace std;

#define pii pair <int, int>
#define fi first
#define se second
const int N = 100;
vector <vector<pii>> vec;
int n, cnt[N][N], vis[N][N];

int main() {
    freopen("snakes2.in", "r", stdin);
    freopen("snakes2.out", "w", stdout);
    ios::sync_with_stdio(false);
    cin.tie(0); cout.tie(0);
    while (cin >> n) {
        vec.clear(); vec.resize(n + 1);
        memset(cnt, 0, sizeof cnt);
        memset(vis, 0, sizeof vis);
        for (int i = 1, m; i <= n; ++i) {
            cin >> m;
            vec[i].resize(m);
            for (auto &it : vec[i]) {
                cin >> it.fi >> it.se;
                ++cnt[it.fi][it.se];
            }  
        }
        for (int i = 1; i <= n; ++i) {
            for (auto it : vec[i]) {
                if (cnt[it.fi][it.se] >= 2) {
                    cout << (vis[it.fi][it.se] ? "+" : "-");
                    ++vis[it.fi][it.se]; 
                }
            }
            cout << "\n";
        }
    }
    return 0;
}#include <bits/stdc++.h>
using namespace std;

#define pii pair <int, int>
#define fi first
#define se second
const int N = 100;
vector <vector<pii>> vec;
int n, cnt[N][N], vis[N][N];

int main() {
    freopen("snakes2.in", "r", stdin);
    freopen("snakes2.out", "w", stdout);
    ios::sync_with_stdio(false);
    cin.tie(0); cout.tie(0);
    while (cin >> n) {
        vec.clear(); vec.resize(n + 1);
        memset(cnt, 0, sizeof cnt);
        memset(vis, 0, sizeof vis);
        for (int i = 1, m; i <= n; ++i) {
            cin >> m;
            vec[i].resize(m);
            for (auto &it : vec[i]) {
                cin >> it.fi >> it.se;
                ++cnt[it.fi][it.se];
            }  
        }
        for (int i = 1; i <= n; ++i) {
            for (auto it : vec[i]) {
                if (cnt[it.fi][it.se] >= 2) {
                    cout << (vis[it.fi][it.se] ? "+" : "-");
                    ++vis[it.fi][it.se]; 
                }
            }
            cout << "\n";
        }
    }
    return 0;
}

```