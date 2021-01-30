---
title: 2020_HZNU_winter_camp_最短路
date:  2019-11-05 22:10:00 +0800
tags: [讲课]
description: 2020_HZNU_winter_camp_最短路
---


## HDU 1874

```C++
#include <bits/stdc++.h>

using namespace std;

const int N = 210;
const int INF = 0x3f3f3f3f;

int n, m;
int dis[N][N];

void Floyd() {
    for (int k = 0; k < n; ++k) {
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                dis[i][j] = min(dis[i][j], dis[i][k] + dis[k][j]);
            }
        }
    }
}

int main() {
    while (scanf("%d %d", &n, &m) != EOF) {
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                dis[i][j] = INF;
                if (i == j) dis[i][j] = 0;
            }
        }
        for (int i = 1, u, v, w; i <= m; ++i) {
            scanf("%d %d %d", &u, &v, &w);
            w = min(w, dis[u][v]);
            dis[u][v] = dis[v][u] = w;
        }
        Floyd();
        int S, T;
        scanf("%d %d", &S, &T);
        if (dis[S][T] == INF) printf("%d\n", -1);
        else printf("%d\n", dis[S][T]);
    }
    return 0;
}

```

## HDU 1869

```C++

#include <bits/stdc++.h>

using namespace std;

const int N = 210;
const int INF = 0x3f3f3f3f;

int n, m;
int dis[N][N];

void Floyd() {
    for (int k = 0; k < n; ++k) {
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                dis[i][j] = min(dis[i][j], dis[i][k] + dis[k][j]);
            }
        }
    }
}

bool ok() {
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            if (dis[i][j] > 7) {
                return false;
            }
        }
    }
    return true;
}

int main() {
    while (scanf("%d %d", &n, &m) != EOF) {
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                dis[i][j] = INF;
                if (i == j) dis[i][j] = 0;
            }
        }
        for (int i = 1, u, v; i <= m; ++i) {
            scanf("%d %d", &u, &v);
            dis[u][v] = dis[v][u] = 1;
        }
        Floyd();
        puts(ok() ? "Yes" : "No");
    }
    return 0;
}
```

## HDU 2066

```C++

#include <bits/stdc++.h>

using namespace std;

const int N = 1e3 + 10, INF = 0x3f3f3f3f;

struct Edge {
    int to, w;

    Edge() {}
    
    Edge(int to, int w) : to(to), w(w) {}
} edge[N << 1];

int n, m;
int S, D, s[N], d[N];
int dis[N];
vector<Edge> G[N];

struct qnode {
    int u, w;

    qnode() {}
    
    qnode(int u, int w) : u(u), w(w) {}
    
    bool operator<(const qnode &other) const {
        return w > other.w;
    }
};

void Dijkstra(int s) {
    priority_queue<qnode> q;
    memset(dis, INF, sizeof dis);
    dis[s] = 0;
    q.push(qnode(s, 0));
    while (!q.empty()) {
        int u = q.top().u;
        q.pop();
        for (int i = 0, len = G[u].size(); i < len; ++i) {
            int v = G[u][i].to, w = G[u][i].w;
            if (dis[v] > dis[u] + w) {
                dis[v] = dis[u] + w;
                q.push(qnode(v, dis[v]));
            }
        }
    }
}

int main() {
    while (scanf("%d %d %d", &n, &S, &D) != EOF) {
        for (int i = 0; i < N; ++i) G[i].clear();
        for (int i = 1, u, v, w; i <= n; ++i) {
            scanf("%d %d %d", &u, &v, &w);
            G[u].push_back(Edge(v, w));
            G[v].push_back(Edge(u, w));
        }
        for (int i = 1; i <= S; ++i) {
            scanf("%d", s + i);// 0->s[i] s[i]->0
            G[0].push_back(Edge(s[i], 0));
            G[s[i]].push_back(Edge(0, 0));
        }
        Dijkstra(0);
        int res = INF;
        for (int i = 1; i <= D; ++i) {
            scanf("%d", d + i);
            res = min(res, dis[d[i]]);
        }
        printf("%d\n", res);
    }
    return 0;
}

```

## HDU 1217

```C++


#include <bits/stdc++.h>

using namespace std;

const int N = 50;

int n, m, tot;
map<string, int> mp;
double dis[N][N];

void Floyd() {
    for (int k = 1; k <= n; ++k) {
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= n; ++j) {
                dis[i][j] = max(dis[i][j], dis[i][k] * dis[k][j]);
            }
        }
    }
}

bool ok() {
    for (int i = 1; i <= n; ++i) {
        if (dis[i][i] > 1.0) {
            return true;
        }
    }
    return false;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0), cout.tie(0);
    int cas = 1;
    while (cin >> n, n) {
        cout << "Case " << cas++ << ": ";
        mp.clear();
        tot = 0;
        memset(dis, 0, sizeof dis);
        for (int i = 1; i <= n; ++i) dis[i][i] = 1.0;
        for (int i = 1; i <= n; ++i) {
            string s;
            cin >> s;
            mp[s] = i;
        }
        cin >> m;
        for (int i = 1; i <= m; ++i) {
            string a, b;
            double c;
            cin >> a >> c >> b;
            dis[mp[a]][mp[b]] = c;
        }
        Floyd();
        cout << (ok() ? "Yes" : "No") << endl;
    }
    return 0;
}
```

## HDU 4725

```C++
#include <bits/stdc++.h>

using namespace std;
const int INF = 0x3f3f3f3f;
const int N = 3e5 + 10;

vector<pair<int, int> > E[N];
int dist[N];
int n, m, c;

void Dijkstra() {
    priority_queue<pair<int, int> > q;
    dist[1] = 0;
    q.push(make_pair(-dist[1], 1));
    while (!q.empty()) {
        int now = q.top().second;
        q.pop();
        for (int i = 0; i < E[now].size(); ++i) {
            int v = E[now][i].first;
            if (dist[v] > dist[now] + E[now][i].second) {
                dist[v] = dist[now] + E[now][i].second;
                q.push(make_pair(-dist[v], v));
            }
        }
    }
}

void init() {
    memset(dist, INF, sizeof(dist));
    for (int i = 0; i < N; ++i) {
        E[i].clear();
    }
}

int main() {
    int T, cas = 0;
    scanf("%d", &T);
    while (T--) {
        init();
        scanf("%d %d %d", &n, &m, &c);
        for (int i = 1; i <= n; i++) {
            int temp;
            scanf("%d", &temp);
            E[temp * 2 - 1 + n].push_back(make_pair(i, 0));
            E[i].push_back(make_pair(temp * 2 + n, 0));
        }
        for (int i = 0; i < m; ++i) {
            int x, y, z;
            scanf("%d %d %d", &x, &y, &z);
            E[x].push_back(make_pair(y, z));
            E[y].push_back(make_pair(x, z));
        }
        for (int i = 1; i <= n; ++i) {
            if (i != 1) {
                E[n + 2 * i].push_back(make_pair(n + 2 * i - 3, c));
            }
            if (i != n) {
                E[n + 2 * i].push_back(make_pair(n + 2 * i + 1, c));
            }
        }
        Dijkstra();
        if (dist[n] == INF) dist[n] = -1;
        printf("Case #%d: %d\n", ++cas, dist[n]);
    }
}
```

## HDU 2962

```C++


#include <bits/stdc++.h>

using namespace std;

const int N = 1e3 + 10, INF = 0x3f3f3f3f;

struct Edge {
    int to, h, w;

    Edge() {}
    
    Edge(int to, int h, int w) : to(to), h(h), w(w) {}

};

int n, m;
int dis[N];
vector<Edge> G[N];

struct qnode {
    int u, w;

    qnode() {}
    
    qnode(int u, int w) : u(u), w(w) {}
    
    bool operator<(const qnode &other) const {
        return w > other.w;
    }

};

void Dijkstra(int s, int h) {
    priority_queue<qnode> q;
    memset(dis, INF, sizeof dis);
    dis[s] = 0;
    q.push(qnode(s, 0));
    while (!q.empty()) {
        int u = q.top().u;
        q.pop();
        for (int i = 0, len = G[u].size(); i < len; ++i) {
            if (h > G[u][i].h) continue;
            int v = G[u][i].to, w = G[u][i].w;
            if (dis[v] > dis[u] + w) {
                dis[v] = dis[u] + w;
                q.push(qnode(v, dis[v]));
            }
        }
    }
}

int main() {
    int cas = 0;
    while (scanf("%d %d", &n, &m) != EOF) {
        if (n == 0 && m == 0) break;
        if (cas) puts("");
        cas++;
        printf("Case %d:\n", cas);
        for (int i = 0; i < N; ++i) G[i].clear();
        int Min = INF, Max = 0;
        for (int i = 1, u, v, h, w; i <= m; ++i) {
            scanf("%d %d %d %d", &u, &v, &h, &w);
            if (h == -1) {
                h = INF;
            }
            Min = min(Min, h);
            Max = max(Max, h);
            G[u].push_back(Edge(v, h, w));
            G[v].push_back(Edge(u, h, w));
        }
        int st, ed, limit;
        scanf("%d %d %d", &st, &ed, &limit);
        Max = min(Max, limit);
        Dijkstra(st, 3);
        int l = Min, r = Max, res = -1;
        while (r - l >= 0) {
            int mid = (l + r) >> 1;
            Dijkstra(st, mid);
            if (dis[ed] != INF) {
                l = mid + 1;
                res = mid;
            } else {
                r = mid - 1;
            }
        }
        if (res != -1) {
            Dijkstra(st, res);
            printf("maximum height = %d\n", res);
            printf("length of shortest route = %d\n", dis[ed]);
        } else {
            printf("cannot reach destination\n");
        }
    }
    return 0;
}
```

## HDU 4857

```C++
#include <bits/stdc++.h>

using namespace std;

const int N = 3e4 + 10;

int n, m;
int in[N];
vector<vector<int>> G;
vector<int> ans;

void topsort() {
    priority_queue<int> q;
    for (int i = 1; i <= n; ++i) {
        if (in[i] == 0) {
            q.push(i);
        }
    }
    while (!q.empty()) {
        int u = q.top();
        q.pop();
        ans.push_back(u);
        for (auto v : G[u]) {
            --in[v];
            if (in[v] == 0) {
                q.push(v);
            }
        }
    }
}

int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d %d", &n, &m);
        for (int i = 1; i <= n; ++i) in[i] = 0;
        ans.clear();
        G.clear();
        G.resize(n + 1);
        for (int i = 1, u, v; i <= m; ++i) {
            scanf("%d %d", &u, &v);
            in[u]++;
            G[v].push_back(u);
        }
        topsort();
        for (int i = ans.size() - 1; i >= 0; --i) {
            printf("%d%c", ans[i], " \n"[i == 0]);
        }
    }
    return 0;
}
```

## HYSBZ 2456
```C++

#include<stdio.h>

int main() 
{
	int n;
	while (~scanf("%d", &n))
	{
		int ans = 0, tot = 0;
		for (int i = 1, num; i <= n; ++i)
		{
			scanf("%d", &num);
			if (!tot)
			{
				ans = num;
				tot++;
			}
			else if (num == ans) ++tot;
			else --tot;
		}
		printf("%d\n", ans);
	}
	return 0;
}
```

## HihoCoder 1081

```C++
#include <bits/stdc++.h>

using namespace std;

const int N = 3e4 + 10, INF = 0x3f3f3f3f;

struct Edge {
    int to, w;

    Edge() {}
    
    Edge(int to, int w) : to(to), w(w) {}
};

int n, m, S, T;
int dis[N];
vector<Edge> G[N];

struct qnode {
    int u, w;

    qnode() {}
    
    qnode(int u, int w) : u(u), w(w) {}
    
    bool operator<(const qnode &other) const {
        return w > other.w;
    }
};

void Dijkstra(int s) {
    priority_queue<qnode> q;
    memset(dis, INF, sizeof dis);
    dis[s] = 0;
    q.push(qnode(s, 0));
    while (!q.empty()) {
        int u = q.top().u;
        q.pop();
        for (int i = 0, len = G[u].size(); i < len; ++i) {
            int v = G[u][i].to, w = G[u][i].w;
            if (dis[v] > dis[u] + w) {
                dis[v] = dis[u] + w;
                q.push(qnode(v, dis[v]));
            }
        }
    }
}

int main() {
    while (scanf("%d %d %d %d", &n, &m, &S, &T) != EOF) {
        for (int i = 0; i < N; ++i) {
            G[i].clear();
        }
        for (int i = 1, u, v, w; i <= m; ++i) {
            scanf("%d %d %d", &u, &v, &w);
            G[u].push_back(Edge(v, w));
            G[v].push_back(Edge(u, w));
        }
        Dijkstra(S);
        printf("%d\n", dis[T]);
    }
    return 0;
}
```

## HihoCoder 1089

```C++
#include <bits/stdc++.h>

using namespace std;

const int N = 1e2 + 10, INF = 0x3f3f3f3f;

int n, m;
int dis[N][N];

void Floyd() {
    for (int k = 1; k <= n; ++k) {
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= n; ++j) {
                dis[i][j] = min(dis[i][j], dis[i][k] + dis[k][j]);
            }
        }
    }
}

int main() {
    while (scanf("%d %d", &n, &m) != EOF) {
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (i == j) dis[i][j] = 0;
                else dis[i][j] = INF;
            }
        }
        for (int i = 1, u, v, w; i <= m; ++i) {
            scanf("%d %d %d", &u, &v, &w);
            w = min(dis[u][v], w);
            dis[u][v] = dis[v][u] = w;
        }
        Floyd();
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= n; ++j) {
                printf("%d%c", dis[i][j], " \n"[j == n]);
            }
        }
    }
    return 0;
}
```