---
title: 	 2019 Multi-University Training Contest 1
date:   2019-07-22 19:40:20 +0800
tags: [HUD多校]
description: 2019 Multi-University Training Contest 1
---

## D. Vacation

题意：有$n+1$辆车，每辆车有自己的长度，距离终点时间，速度。求第$0$辆车到达终点时间。其中不能超车，且车过终点后依旧向前行驶

思路：枚举每个车作为某个连续段的队头，然后取$Max$

```C++
#include <bits/stdc++.h>

using namespace std;
typedef double db;
#define N 100010

int n;
struct node {
    db l, s, v;
} a[N];
db sum;

int main() {
    while (scanf("%d", &n) != EOF) {
        sum = 0;
        for (int i = n; i >= 0; --i) {
            scanf("%lf", &a[i].l);
            sum += a[i].l;
        }
        for (int i = n; i >= 0; --i) {
            scanf("%lf", &a[i].s);
        }
        for (int i = n; i >= 0; --i) {
            scanf("%lf", &a[i].v);
        }
        sum -= a[n].l;
        db ans = 0;
        for (int i = 0; i <= n; ++i) {
            ans = max(ans, (sum + a[i].s) / a[i].v);
            sum -= a[i].l;
        }
        printf("%.10f\n", ans);
    }
    return 0;
}
```

## E. Path

题意：破坏一条路径的代价为路径长度，求破坏所有最短路的最小代价

思路：跑一边最短路后重建图跑最大流

```C++
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
typedef pair<int, int> pii;
#define INFLL 0x3f3f3f3f3f3f3f3f
#define N 20010

int n, m;

struct Dicnic {
    struct Edge {
        int to, nxt;
        ll flow;

        Edge() {}
    
        Edge(int to, int nxt, ll flow) : to(to), nxt(nxt), flow(flow) {}
    } edge[N << 1];
    
    int head[N], tot;
    int dep[N];
    
    void Init() {
        memset(head, -1, sizeof head);
        tot = 0;
    }
    
    void addedge(int u, int v, int w, int rw = 0) {
        edge[tot] = Edge(v, head[u], w);
        head[u] = tot++;
        edge[tot] = Edge(u, head[v], rw);
        head[v] = tot++;
    }
    
    bool BFS() {
        memset(dep, -1, sizeof dep);
        queue<int> q;
        q.push(1);
        dep[1] = 1;
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
        return dep[n] >= 0;
    }
    
    ll DFS(int u, ll f) {
        if (u == n || f == 0) return f;
        ll w, used = 0;
        for (int i = head[u]; ~i; i = edge[i].nxt) {
            if (edge[i].flow && dep[edge[i].to] == dep[u] + 1) {
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
    
    ll solve() {
        ll ans = 0;
        while (BFS()) {
            ans += DFS(1, INFLL);
        }
        return ans;
    }
} dicnic;

struct Edge {
    int u, to, nxt, w;

    Edge() {}
    
    Edge(int u, int to, int nxt, int w) : u(u), to(to), nxt(nxt), w(w) {}

} edge[N << 1];


struct qnode {
    int u;
    ll w;

    qnode() {}
    
    qnode(int u, ll w) : u(u), w(w) {}
    
    bool operator<(const qnode &other) const {
        return w > other.w;
    }
};

int head[N], tot;
ll dis[N];

void Init() {
    memset(dis, 0x3f, sizeof dis);
    memset(head, -1, sizeof head);
    tot = 0;
}

void addedge(int u, int v, ll w) {
    edge[tot] = Edge(u, v, head[u], w);
    head[u] = tot++;
}


void Dijkstra() {
    priority_queue<qnode> q;
    q.push(qnode(1, 0));
    dis[1] = 0ll;
    while (!q.empty()) {
        int u = q.top().u;
        q.pop();
        for (int i = head[u]; ~i; i = edge[i].nxt) {
            int v = edge[i].to;
            ll w = edge[i].w;
            if (dis[v] > dis[u] + w) {
                dis[v] = dis[u] + w;
                q.push(qnode(v, dis[v]));
            }
        }
    }
}


int main() {
//    freopen("input.txt", "r", stdin);
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d %d", &n, &m);
        Init();
        for (int i = 1, u, v, w; i <= m; ++i) {
            scanf("%d %d %d", &u, &v, &w);
            addedge(u, v, w);
        }
        Dijkstra();
        dicnic.Init();
        for (int i = 0; i < tot; ++i) {
            int u = edge[i].u;
            int v = edge[i].to;
            ll w = edge[i].w;
            if (dis[v] == dis[u] + w) {
                dicnic.addedge(u, v, w);
            }
        }
        printf("%lld\n", dicnic.solve());
    }
    return 0;
}
```

## I. String

题意：构造一个子序列，长度为$k$，且各个字母$i$，出现次数在$l_i-r_i$之间

思路：贪心从小到大枚举每一位能否放在答案中，检查放入后剩余后缀串能否满足限制

 ```C++

#include <bits/stdc++.h>

using namespace std;

#define N 100010

int n, k;
char s[N], ans[N];
int cnt[N][26], used[N], l[N], r[N];
vector<int> G[26];

void solve() {
    for (int i = n; i >= 1; --i) {
        for (int j = 0; j < 26; ++j) {
            cnt[i][j] = cnt[i + 1][j];
        }
        cnt[i][s[i] - 'a']++;
    }
    for (int i = 1; i <= n; ++i) {
        G[s[i] - 'a'].push_back(i);
    }
    vector<int>::iterator head[26];
    for (int i = 0; i < 26; ++i) {
        head[i] = G[i].begin();
    }
    int lst = 0;
    for (int i = 1; i <= k; ++i) {
        int flag = 0;
        for (int j = 0; j < 26; ++j) {
            if (used[j] == r[j]) continue;
            if (head[j] == G[j].end()) continue;
            while (head[j] != G[j].end() && *head[j] <= lst) head[j]++;
            if (head[j] == G[j].end()) continue;
            used[j]++;
            int f = 1;
            int now = *head[j], need = 0;
            for (int o = 0; o < 26; ++o) {
                if (cnt[now + 1][o] + used[o] < l[o]) {
                    f = 0;
                    break;
                }
                need += max(l[o] - used[o], 0);
            }
            if (need > k - i) {
                f = 0;
            }
            if (f == 0) {
                used[j]--;
            } else {
                ans[i] = j + 'a';
                flag = 1;
                lst = now;
                break;
            }
        }
        if (flag == 0) {
            puts("-1");
            return;
        }
    }
    ans[k + 1] = 0;
    printf("%s\n", ans + 1);
}

int main() {
//    freopen("input.txt", "r", stdin);
    while (scanf("%s %d", s + 1, &k) != EOF) {
        n = strlen(s + 1);
        for (int i = 0; i < 26; ++i) {
            scanf("%d %d", l + i, r + i);
            G[i].clear();
        }
        memset(used, 0, sizeof used);
        memset(cnt, 0, sizeof cnt);
        solve();
    }
    return 0;
}

```