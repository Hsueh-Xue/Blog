---
title: 	2011-2012 Summer Petrozavodsk Camp, Warsaw U Contest, 2011-09-11
date:   2019-08-12 20:20:00 +0800
tags: [ACM]
description: 2011-2012 Summer Petrozavodsk Camp, Warsaw U Contest, 2011-09-11
---

## A.Chocolate

题意：给定一个$n\cdot m$的巧克力，上面有个标记的$cell$每次选择一个水平线或者竖直线，沿线切开，如果迟到标记的点则输，问先手输的方案数

思路：显然是一个$Nim$游戏，即求$\sum_{i=0}^{n - 1}\sum_{j=1}^{m-1}i\oplus (n-1-i)\oplus j \oplus(m-1-j)$

打表

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
typedef pair<int, int> pii;

map<pii, ll> mp;

ll gao(ll n, ll m) {
    if (mp.find(pii(n, m)) != mp.end()) return mp[pii(n, m)];
    if (n == 1 || m == 1) {
        if (n % 2 != m % 2) {
            mp[pii(n, m)] = 0;
        } else {
            mp[pii(n, m)] = 1;
        }
    } else if (n % 2 != m % 2) {
        mp[pii(n, m)] = 0ll;
    } else if (n == 1 && m == 1) {
        mp[pii(n, m)] = 1ll;
    } else if (n % 2 == 0) {
        mp[pii(n, m)] = 4ll * gao(n / 2, m / 2);
    } else  {
        mp[pii(n, m)] = 1ll * gao(n / 2, m / 2) + 1ll * gao(n / 2 + 1, m / 2 + 1) + gao(n / 2 + 1, m / 2) + gao(n / 2, m / 2 + 1);
    }
    return mp[pii(n, m)];
}

ll n, m;

int main() {
    freopen("chocolate.in", "r", stdin);
    freopen("chocolate.out", "w", stdout);
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%lld %lld", &n, &m);
        printf("%lld\n", gao(n, m));
    }
    return 0;
}

```

## B. Programming Contest

题意：有$n$个人，$m$道题，每个人有自己能做的题以及做出一道题时间为$r$，比赛持续时间为$t$，求一个方案使得题目最多，罚时最少。

思路：考虑匈牙利不会导致之前匹配的点失配，那么直接做匈牙利，每次减少一道题或者一个人不能在做题，最多做$1000$次

```C++

#include <bits/stdc++.h>

using namespace std;

#define N 510

vector<vector<int> > G;
int linker[N];
bool used[N];
int uN;

bool DFS(int u) {
    for (auto v : G[u]) {
        if (!used[v]) {
            used[v] = true;
            if (linker[v] == -1 || DFS(linker[v])) {
                linker[v] = u;
                return true;
            }
        }
    }
    return false;
}

int n, m, r, t, k;
int Time[N];

int main() {
    freopen("contest.in", "r", stdin);
    freopen("contest.out", "w", stdout);
    while (scanf("%d %d %d %d %d", &n, &m, &r, &t, &k) != EOF) {
        G.clear();
        G.resize(n + 1);
        memset(linker, -1, sizeof linker);
        memset(Time, 0, sizeof Time);
        for (int i = 1, a, b; i <= k; ++i) {
            scanf("%d %d", &a, &b);
            G[a].push_back(b);
        }
        int ans1 = 0, ans2 = 0;
        for (int i = 1; i * r <= t; ++i)  {
            bool F = 0;
            for (int u = 1; u <= n; ++u) {
                memset(used, false, sizeof used);
                if (DFS(u)) {
                    F = 1;
                    ans1++;
                    ans2 += i;
                }
            }
            if (!F) {
                break;
            }
        }
        printf("%d %d\n", ans1, ans2 * r);
        for (int i = 1; i <= m; ++i) {
            if (linker[i] == -1) continue;
            int u = linker[i];
            printf("%d %d %d\n", u, i, Time[u]);
            Time[u] += r;
        }
    }
    return 0;
}

```

## D. Evacuation

题意：有$n$个点，$m$条有向边，$q$次操作，每次删除一个点或者查询$1$到$p$ 的距离

思路：

考虑倒过来就是加边，每次只会修改一个点，将这个点暴力$BFS$，每个点最多修改$n$次，复杂度为$O(n^2)$

```C++

#include <bits/stdc++.h>

using namespace std;

#define N 1010
#define M 200010
const int INF = 0x3f3f3f3f;
int e[M][3], res[M];
vector <vector<int>> G;
struct qnode {
    //0加边 1 询问
    int op, p;
    qnode() {}
    qnode(int op, int p) : op(op), p(p) {}
}que[M];
int n, m, q;
int dis[N];

void BFS(int u) {
    queue <int> qu;
    qu.push(u);
    while (!qu.empty()) {
        u = qu.front(); qu.pop();
        for (auto v : G[u]) {
            if (dis[u] + 1 < dis[v]) {
                dis[v] = dis[u] + 1;
                qu.push(v);
            }
        }   
    }
}

int main() {
    freopen("evacuation.in", "r", stdin);
    freopen("evacuation.out", "w", stdout);
    while (scanf("%d%d%d", &n, &m, &q) != EOF) {
        G.clear(); G.resize(n + 1);
        memset(dis, 0x3f, sizeof dis);
        dis[1] = 0;
        for (int i = 1; i <= m; ++i) {
            scanf("%d%d", &e[i][0], &e[i][1]);
            e[i][2] = 0;
        }
        for (int i = 1; i <= q; ++i) {
            char op[10]; int p;
            scanf("%s %d", op, &p);
            que[i] = qnode(op[0] == 'U' ? 0 : 1, p);
            if (op[0] == 'U') {
                e[p][2] = 1;
            } 
        }
        for (int i = 1; i <= m; ++i) if (e[i][2] == 0) {
            G[e[i][0]].push_back(e[i][1]);
        //  G[e[i][1]].push_back(e[i][0]);
        }
        BFS(1);
        for (int i = q; i >= 1; --i) {
            if (que[i].op == 0) {
                int x = e[que[i].p][0], y = e[que[i].p][1];
                G[x].push_back(y);
                if (dis[x] + 1 < dis[y]) {
                    dis[y] = dis[x] + 1;
                    BFS(y);
                } 
            } else {
                res[i] = dis[que[i].p];
            }
        }
        for (int i = 1; i <= q; ++i) {
            if (que[i].op == 1) {
                printf("%d\n", res[i] >= INF ? -1 : res[i]);
            }
        }
    }   
    return 0;
}

```

## E. Inspection

题意：有$n$个点的树

- 选定一个点$S$
- 从$S$出发访问每个节点，访问完回到$S$，一次的访问时间为路径长度，最后一次访问可以补回来
- 连续的两次不能走同一条路
- 输出最小时间

思路：经典树形$dp$ 

```C++

#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pii pair <int, int>
#define fi first
#define se second
const int N = 1e6 + 10;
int n;
vector <vector<int>> G;
ll f[N], g[N], res[N];
pii mf[N][2];
int sze[N], fa[N], mg[N]; 
void DFS(int u) { 
    sze[u] = 1; 
    f[u] = 0; 
    mf[u][0] = pii(0, u);
    mf[u][1] = pii(-1, -1);
    for (auto v : G[u]) if (v != fa[u]) {
        fa[v] = u;
        DFS(v);
        sze[u] += sze[v];
        f[u] += f[v] + sze[v]; 
        if (mf[v][0].fi + 1 > mf[u][0].fi) {
            mf[u][1] = mf[u][0];
            mf[u][0] = mf[v][0];
            ++mf[u][0].fi;
            mf[u][0].se = v;
        } else if (mf[v][0].fi + 1 > mf[u][1].fi) {
            mf[u][1] = mf[v][0];
            ++mf[u][1].fi;
            mf[u][1].se = v;
        }
    }
}
void DFS2(int u) {
    for (auto v : G[u]) if (v != fa[u]) {
        g[v] = 0;
        g[v] += n - sze[v];
        g[v] += g[u];
        g[v] += f[u] - sze[v] - f[v];
        mg[v] = mg[u] + 1; 
        if (mf[u][0].se == v) {
            mg[v] = max(mg[v], mf[u][1].fi + 1);
        } else {
            mg[v] = max(mg[v], mf[u][0].fi + 1);
        }
        DFS2(v); 
    }
}

int main() {
    freopen("inspection.in", "r", stdin);
    freopen("inspection.out", "w", stdout);
    while (scanf("%d", &n) != EOF) {
        G.clear(); G.resize(n + 1);
        for (int i = 1, u, v; i < n; ++i) {
            scanf("%d%d", &u, &v);
            G[u].push_back(v);
            G[v].push_back(u);
        }
        g[1] = 0;
        mg[1] = 0;
        DFS(1); 
        DFS2(1);
        for (int u = 1; u <= n; ++u) {
            int Max = 0, num;
            for (auto v : G[u]) if (v != fa[u]) {
                if (sze[v] > Max) {
                    Max = sze[v];
                    num = mf[v][0].fi + 1;
                }
            }
            if (n - sze[u] > Max) {
                Max = max(Max, n - sze[u]);
                num = mg[u];
            }
            if (Max > n - 1 - Max + 1) {
                res[u] = -1;
            } else {
                res[u] = 2ll * (f[u] + g[u]);
                if (Max == n - 1 - Max + 1) {
                    res[u] -= num;
                } else {
                    res[u] -= max(mf[u][0].fi, mg[u]);
                }
            }
        }
        for (int i = 1; i <= n; ++i)
            printf("%lld\n", res[i]);
    }
    return 0;
}

```

## F. Meteors

题意：有$n$个城市，$m$个地点，每个地点属于一个城市，接下来有$q$次操作，每次操作是的$[L,R]$区间内的点下$a_i$雨，每个城市有一个目标降雨量$p_i$，为最早到达目标的操作次数

思路：经典整体二分

```C++

#include <bits/stdc++.h>
using namespace std;

#define ll long long
const int N = 3e5 + 10;
int n, m, q, res[N];
ll tar[N];
struct tnode {
    int l, r;
    ll a;
    tnode() {}  
    tnode(int l, int r, ll a) : l(l), r(r), a(a) {}
    void scan() {
        scanf("%d%d%lld", &l, &r, &a); 
    }
}arr[N];
struct qnode {
    int id; ll v;
    qnode() {}
    qnode(int id, ll v) : id(id), v(v) {}
}que[N], ql[N], qr[N]; 
vector <vector<int>> vec;

struct BIT {
    ll a[N];
    void init() {
        memset(a, 0, sizeof a); 
    }
    void update(int x, ll v) {
        for (; x < N; x += x & -x) {
            a[x] += v;
        }
    }
    ll query(int x) {
        ll res = 0;
        for (; x > 0; x -= x & -x) {
            res += a[x];
        }
        return res;
    }
    void update(int l, int r, ll v) {
        if (l > r) {
            update(l, v);
            update(m + 1, -v);
            update(1, v);
            update(r + 1, -v);
        } else {
            update(l, v);
            update(r + 1, -v);
        }
    }
}bit; 

void gao(int L, int R, int l, int r) {
    if (L > R) return;
//  cout << L << " " << R << " " << l << " " << r << endl;
    if (l == r) {
    //  cout << l << endl;
        for (int i = L; i <= R; ++i) {
            res[que[i].id] = l; 
        }   
        return;
    }
    int mid = (l + r) >> 1;
    for (int i = l; i <= mid; ++i) {
        bit.update(arr[i].l, arr[i].r, arr[i].a);
    }
    int posl = 0, posr = 0;
    for (int i = L; i <= R; ++i) {
        ll tot = 0;
        for (auto it : vec[que[i].id]) {
            tot += bit.query(it);
            if (tot > que[i].v) break;
        }
    //  cout << que[i].id << " " << tot << endl;
        if (tot < que[i].v) {
            que[i].v -= tot;
            qr[++posr] = que[i];
        } else {
            ql[++posl] = que[i];
        }
    }
    for (int i = 1; i <= posl; ++i) que[L + i - 1] = ql[i];
    for (int i = 1; i <= posr; ++i) que[L + posl + i - 1] = qr[i];
    for (int i = l; i <= mid; ++i) bit.update(arr[i].l, arr[i].r, -arr[i].a);
    gao(L, L + posl - 1, l, mid);
    gao(L + posl, R, mid + 1, r);
}

void out(int x) {
    if (x == q + 1) puts("NIE");
    else {
        printf("%d\n", x);  
    }
}

int main() {
    freopen("meteors.in", "r", stdin); 
    freopen("meteors.out", "w", stdout); 
    while (scanf("%d%d", &n, &m) != EOF) { 
        vec.clear(); vec.resize(n + 1);
        bit.init(); 
        for (int i = 1; i <= n; ++i) res[i] = q + 1;
        for (int i = 1, x; i <= m; ++i) {
            scanf("%d", &x);
            vec[x].push_back(i); 
        }
        for (int i = 1; i <= n; ++i) scanf("%lld", tar + i);
        scanf("%d", &q);
        for (int i = 1; i <= q; ++i) {
            arr[i].scan();
        }
        arr[q + 1] = tnode(1, m, 2000000000ll);
        for (int i = 1; i <= n; ++i) {
            que[i] = qnode(i, tar[i]);
        }
        gao(1, n, 1, q + 1);
        for (int i = 1; i <= n; ++i)
            out(res[i]);
    }
    return 0;
}

```

## G. Party

题意：给定一个有$\frac{2n}{3}$大小的团，求出$\frac{n}{3}$使得这些点是团

思路：感性理解

假设都是团，枚举每个假设是团的点，如果这个点和一个你假设的点不相连，那么将这两个点都删除，每次只会删除两个不是团中的点或者一个团中的点以及一个不是团中的点，那么最多误删$\frac{n}{3}$个点，剩下的就是团中的点。

思路二

每次选取度最小的点删除，直到所有点度数相同，感性理解即可

```C++

#include <bits/stdc++.h>

using namespace std;

#define N 3010

int n, m;
int vis[N];
int G[N][N];

int main() {
    freopen("party.in", "r", stdin);
    freopen("party.out", "w", stdout);
    while (scanf("%d %d", &n, &m) != EOF) {
        memset(vis, 0, sizeof vis);
        memset(G, 0, sizeof G);
        for (int i = 1, u, v; i <= m; ++i) {
            scanf("%d %d", &u, &v);
            G[u][v] = G[v][u] = 1;
        }
        for (int i = 1; i <= n; ++i) {
            if (vis[i]) continue;
            for (int j = i + 1; j <= n; ++j) {
                if (!vis[j] && !G[i][j]) {
                    vis[i] = vis[j] = 1;
                    break;
                }
            }
        }
        vector<int> res;
        for (int i = 1; i <= n; ++i) {
            if (!vis[i]) {
                res.push_back(i);
            }
        }
        for (int i = 0; i < n / 3; ++i) {
            printf("%d%c", res[i], " \n"[i == n / 3 - 1]);
        }
    }
    return 0;
}

```

## I. Sticks

题意：有$k$ 个颜色，每个颜色有$n_i$个棍子，问能否有方案是的三根不同颜色的棍子拼成三角形

思路：排序后枚举一根棍子，往前找最大的不同颜色的棍子，往后找最小的不同颜色的棍子，$check$。考虑到如果$n$很大，那么很显然这很快就能找到方案。

可以考虑不合法方案，那就是一个斐波那契数列，只有$50$就会超过$1e9$。

```C++

#include <bits/stdc++.h>
using namespace std;

#define pii pair <int, int>
#define fi first
#define se second
const int N = 1e6 + 10;
int n, m;
vector <vector<int>> vec; 
pii a[N];
int Max[55];

void solve() {
    sort(a + 1, a + 1 + m);
    memset(Max, 0, sizeof Max); 
    set <int> se[55];
    for (int i = 1; i <= m; ++i) {
        se[a[i].se].insert(a[i].fi); 
    }
    for (int i = 1; i <= m; ++i) {
        se[a[i].se].erase(a[i].fi);
        vector <pii> b;
        for (int j = 1; j <= n; ++j) {
            if (!se[j].empty()) {
                b.push_back(pii(*se[j].begin(), j)); 
            } 
        }
        sort(b.begin(), b.end(), [](pii x, pii y) {
            return x.fi < y.fi;         
        });
        for (int j = 1; j <= n; ++j) if (j != a[i].se) {
            int x, y, z; y = a[i].fi; 
            if (Max[j] != 0) {
                x = Max[j];
                for (auto &it : b) {
                    if (it.se != a[i].se && it.se != j) {
                        z = it.fi;
                        if (x + y > z) {
                            cout << a[i].se << " " << a[i].fi << " " << j << " " << x << " " << it.se << " " << it.fi << "\n";
                            return;
                        }
                        break;
                    }
                }
            }
        }
        Max[a[i].se] = max(Max[a[i].se], a[i].fi);
    }
    cout << "NIE\n";
}

int main() {
    freopen("sticks.in", "r", stdin);
    freopen("sticks.out", "w", stdout);
    ios::sync_with_stdio(false);
    cin.tie(nullptr); cout.tie(nullptr);
    while (cin >> n) {
        m = 0;
        vec.clear(); vec.resize(n + 1); 
        for (int i = 1, num; i <= n; ++i) {
            cin >> num;
            vec[i].resize(num);
            for (auto &it : vec[i]) cin >> it;
            sort(vec[i].begin(), vec[i].end());
            vec[i].erase(unique(vec[i].begin(), vec[i].end()), vec[i].end());   
            for (auto &it : vec[i]) {
                a[++m] = pii(it, i); 
            }
        } 
        solve();
    }
    return 0;
}

```