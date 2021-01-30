---
title: HZNU 2019 winter camp
date:  2019-02-15 16:10:00 +0800
tags: [讲课]
description: HZNU 2019 winter camp
---


## POJ 1330 Nearest Common Ancestors 

题意： 给出一棵树，查询两个点$LCA$

思路：$LCA$模板题

```C++

#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<queue>

using namespace std;

typedef long long ll;
const int maxn = 1e4 + 10;
const int DEG = 20;

struct Edge {
	int to, nxt;
	inline Edge() {}
	inline Edge(int to, int nxt) :to(to), nxt(nxt) {}
}edge[maxn << 1];

int head[maxn], tot;

void Init()
{
	tot = 0;
	memset(head, -1, sizeof head);
}

void addedge(int u, int v)
{
	edge[tot] = Edge(v, head[u]); head[u] = tot++;
	edge[tot] = Edge(u, head[v]); head[v] = tot++;
}

int fa[maxn][DEG];
int deg[maxn];

void BFS(int root)
{
	queue<int>q;
	deg[root] = 0;
	fa[root][0] = root;
	q.push(root);
	while (!q.empty())
	{
		int tmp = q.front();
		q.pop();
		for (int i = 1; i < DEG; ++i)
		{
			fa[tmp][i] = fa[fa[tmp][i - 1]][i - 1];
		}
		for (int i = head[tmp]; i != -1; i = edge[i].nxt)
		{
			int v = edge[i].to;
			if (v == fa[tmp][0]) continue;
			deg[v] = deg[tmp] + 1;
			fa[v][0] = tmp;
			q.push(v);
		}
	}
}

int LCA(int u, int v)
{
	if (deg[u] > deg[v]) swap(u, v);
	int hu = deg[u];
	int hv = deg[v];
	int tu = u, tv = v;
	for (int det = hv - hu, i = 0; det; det >>= 1, ++i)
	{
		if (det & 1)
		{
			tv = fa[tv][i];
		}
	}
	if (tu == tv) return tu;
	for (int i = DEG - 1; i >= 0; --i)
	{
		if (fa[tu][i] == fa[tv][i])
			continue;
		tu = fa[tu][i];
		tv = fa[tv][i];
	}
	return fa[tu][0];
}

int n;
bool flag[maxn];

void RUN()
{
	int t;
	scanf("%d", &t);
	while (t--)
	{
		scanf("%d", &n);
		Init();
		memset(flag, false, sizeof flag);
		for (int i = 1; i < n; ++i)
		{
			int u, v;
			ll w;
			scanf("%d %d", &u, &v);
			addedge(u, v);
			flag[v] = true;
		}
		int root = 0;
		for (int i = 1; i <= n; ++i)
		{
			if (!flag[i])
			{
				root = i;
				break;
			}
		}
		BFS(root);
		int u, v;
		scanf("%d %d", &u, &v);
		printf("%d\n", LCA(u, v));
	}
}

int main()
{
#ifdef LOCAL_JUDGE
	freopen("Text.txt", "r", stdin);
#endif // LOCAL_JUDGE

	RUN();

#ifdef LOCAL_JUDGE
	fclose(stdin);
#endif // LOCAL_JUDGE
	return 0;
}

```

## POJ 1470 Closest Common Ancestors

题意：给出一棵树，然后给出几组询问，记录每个点作为询问中的最近公共祖先的次数并输出，如果次数为0则不输出

思路：$LCA$模板题

```C++

#include<cstdio>
#include<iostream>
#include<cmath>
#include<cstring>
#include<algorithm>
#include<queue>

using namespace std;

typedef long long ll;
const int maxn = 1e5 + 10;
const int DEG = 20;

struct Edge {
	int to, nxt;
	Edge() {}
	Edge(int to, int nxt) :to(to), nxt(nxt) {}
}edge[maxn << 1];

int head[maxn], tot;

int dis[maxn];

void addedge(int u, int v)
{
	edge[tot] = Edge(v, head[u]);
	head[u] = tot++;
}

void Init()
{
	tot = 0;
	memset(head, -1, sizeof head);
}

int fa[maxn][DEG];
int deg[maxn];

void BFS(int root)
{
	queue<int>q;
	deg[root] = 0;
	fa[root][0] = root;
	q.push(root);
	while (!q.empty())
	{
		int tmp = q.front();
		q.pop();
		for (int i = 1; i < DEG; ++i)
		{
			fa[tmp][i] = fa[fa[tmp][i - 1]][i - 1];
		}
		for (int i = head[tmp]; i != -1; i = edge[i].nxt)
		{
			int v = edge[i].to;
			if (v == fa[tmp][0]) continue;
			deg[v] = deg[tmp] + 1;
			fa[v][0] = tmp;
			q.push(v);
		}
	}
}

int LCA(int u, int v)
{
	if (deg[u] > deg[v]) swap(u, v);
	int hu = deg[u];
	int hv = deg[v];
	int tu = u, tv = v;
	for (int det = hv - hu, i = 0; det; det >>= 1, ++i)
	{
		if (det & 1)
		{
			tv = fa[tv][i];
		}
	}
	if (tu == tv) return tu;
	for (int i = DEG - 1; i >= 0; --i)
	{
		if (fa[tu][i] == fa[tv][i])
			continue;
		tu = fa[tu][i];
		tv = fa[tv][i];
	}
	return fa[tu][0];
}

int n, m;
char str[maxn];
bool flag[maxn];
int arr[maxn];

void RUN()
{
	while (~scanf("%d", &n))
	{
		Init();
		memset(flag, false, sizeof flag);
		memset(arr, 0, sizeof arr);
		for (int i = 1; i <= n; ++i)
		{
			int id, m;
			scanf("%d:(%d)", &id, &m);
			for (int i = 0; i < m; ++i)
			{
				int id2;
				scanf("%d", &id2);
				addedge(id, id2);
				flag[id2] = true;
			}
		}
		int root = 0;
		for (int i = 1; i <= n; ++i)
		{
			if (!flag[i])
			{
				root = i;
				break;
			}
		}
		BFS(root);
		int q;
		scanf("%d", &q);
		while (q--)
		{
			int u, v;
			scanf(" (%d %d)", &u, &v);
			int root = LCA(u, v);
			arr[root]++;
		}
		for (int i = 1; i <= n; ++i)
		{
			if (arr[i])
			{
				printf("%d:%d\n", i, arr[i]);
			}
		}
	}
}

int main()
{
#ifdef LOCAL_JUDGE
	freopen("Text.txt", "r", stdin);
#endif // LOCAL_JUDGE

	RUN();

#ifdef LOCAL_JUDGE
	fclose(stdin);
#endif // LOCAL_JUDGE
	return 0;
}

```

## HDU 2586 How far away ？

题意：给出一棵树，每条边有边权，然后给出几组询问，每组询问需要输出$u, v$之间的最短路径。

思路：

对于一棵树，两个点的路径唯一。

这两个点的路径长度为u到根的路径长度加上v到根的路径长度减去两倍LCA(u, v)到根的路径长度。

即$dis_u+dis_v-2 \cdot dis_{LCA_{u, v}}$。

```C++

#include<bits/stdc++.h>
using namespace std;

const int maxn = 1e5 + 10;
const int DEG = 20;

int dis[maxn];

struct Edge {
	int  to;
	int next;
	int w;
	Edge() {}
	Edge(int to, int next, int w) :to(to), next(next), w(w) {}
}edge[maxn << 1];

int head[maxn];
int tot;

void addedge(int u, int v, int w)
{
	edge[tot] = Edge(v, head[u], w);
	head[u] = tot++;
}

void init()
{
	tot = 0;
	memset(head, -1, sizeof head);
	memset(dis, 0x3f, sizeof dis);
}

int fa[maxn][DEG];
int deg[maxn];

void BFS(int root)
{
	queue<int>q;
	deg[root] = 0;
	fa[root][0] = root;
	q.push(root);
	dis[root] = 0;
	while (!q.empty())
	{
		int tmp = q.front();
		q.pop();
		for (int i = 1; i < DEG; ++i)
		{
			fa[tmp][i] = fa[fa[tmp][i - 1]][i - 1];
		}
		for (int i = head[tmp]; i != -1; i = edge[i].next)
		{
			int v = edge[i].to;
			if (v == fa[tmp][0]) continue;
			dis[v] = dis[tmp] + edge[i].w;
			deg[v] = deg[tmp] + 1;
			fa[v][0] = tmp;
			q.push(v);
		}
	}
}

int LCA(int u, int v)
{
	if (deg[u] > deg[v])
		swap(u, v);
	int hu = deg[u], hv = deg[v];
	int tu = u;
	int tv = v;
	for (int det = hv - hu, i = 0; det; det >>= 1, ++i)
	{
		if (det & 1)
		{
			tv = fa[tv][i];
		}
	}
	if (tu == tv) return tu;
	for (int i = DEG - 1; i >= 0; --i)
	{
		if (fa[tu][i] == fa[tv][i])
			continue;
		tu = fa[tu][i];
		tv = fa[tv][i];
	}
	return fa[tu][0];
}

void RUN()
{
	int t;
	scanf("%d", &t);
	while (t--)
	{
		init();
		int n, q;
		scanf("%d %d", &n, &q);
		for (int i = 1; i < n; ++i)
		{
			int u, v, w;
			scanf("%d %d %d", &u, &v, &w);
			addedge(u, v, w);
			addedge(v, u, w);
		}
		BFS(1);
		while (q--)
		{
			int u, v;
			scanf("%d %d", &u, &v);
			int root = LCA(u, v);
			int ans = dis[u] + dis[v] - 2 * dis[root];
			printf("%d\n", ans);
		}
	}
}

int main()
{
#ifdef LOCAL_JUDGE
	freopen("Text.txt", "r", stdin);
#endif // LOCAL_JUDGE

	RUN();

#ifdef LOCAL_JUDGE
	fclose(stdin);
#endif // LOCAL_JUDGE
	return 0;
}

```

## HDU 2874 Connections between cities

题意：给出一张图，保证无环，但是不保证连通，求出两个点的最短距离，如果不连通就输出$"Not \quad connected"$。

思路：

由于保证无环，那么给出的图就是一颗或者多颗树。

那么可以用并查集来维护两点是否在一个集合中，也就是一棵树中。

对于在一棵树上的$u,v$两点，距离就是$dis_u+dis_v-2 \cdot dis_{LCA_{u, v}}$。

```C++

#include<bits/stdc++.h>

using namespace std;

typedef long long ll;

const int maxn = 1e5 + 10;
const int DEG = 20;

int n, m, q;
int Fa[maxn];

int dis[maxn];

int find(int x)
{
	return x == Fa[x] ? Fa[x] : Fa[x] = find(Fa[x]);
}

void mix(int x, int y)
{
	x = find(x);
	y = find(y);
	if (x != y)
	{
		Fa[x] = y;
	}
}

struct Edge {
	int  to;
	int next;
	int w;
	Edge() {}
	Edge(int to, int next, int w) :to(to), next(next), w(w) {}
}edge[maxn << 1];

int head[maxn];
int tot;

void addedge(int u, int v, int w)
{
	edge[tot] = Edge(v, head[u], w);
	head[u] = tot++;
}

void init()
{
	tot = 0;
	memset(head, -1, sizeof head);
	memset(dis, 0, sizeof dis);
	for (int i = 1; i <= n; ++i)
	{
		Fa[i] = i;
	}
}

int fa[maxn][DEG];
int deg[maxn];

void BFS()
{
	queue<int>q;
	for (int i = 1; i <= n; ++i)
	{
		if (find(i) == i)
		{
			deg[i] = 0;
			fa[i][0] = i;
			dis[i] = 0;
			q.push(i);
		}
	}
	while (!q.empty())
	{
		int tmp = q.front();
		q.pop();
		for (int i = 1; i < DEG; ++i)
		{
			fa[tmp][i] = fa[fa[tmp][i - 1]][i - 1];
		}
		for (int i = head[tmp]; i != -1; i = edge[i].next)
		{
			int v = edge[i].to;
			if (v == fa[tmp][0]) continue;
			dis[v] = dis[tmp] + edge[i].w;
			deg[v] = deg[tmp] + 1;
			fa[v][0] = tmp;
			q.push(v);
		}
	}
}

int LCA(int u, int v)
{
	if (deg[u] > deg[v])
		swap(u, v);
	int hu = deg[u], hv = deg[v];
	int tu = u;
	int tv = v;
	for (int det = hv - hu, i = 0; det; det >>= 1, ++i)
	{
		if (det & 1)
		{
			tv = fa[tv][i];
		}
	}
	if (tu == tv) return tu;
	for (int i = DEG - 1; i >= 0; --i)
	{
		if (fa[tu][i] == fa[tv][i])
			continue;
		tu = fa[tu][i];
		tv = fa[tv][i];
	}
	return fa[tu][0];
}

void solve()
{
	int u, v;
	scanf("%d %d", &u, &v);
	int rootu = find(u);
	int rootv = find(v);
	if (rootu != rootv)
	{
		puts("Not connected");
		return;
	}
	int root = LCA(u, v);
	int ans = dis[u] + dis[v] - 2 * dis[root];
	printf("%d\n", ans);
}

void RUN()
{
	while (~scanf("%d %d %d", &n, &m, &q))
	{
		init();
		for (int i = 0; i < m; ++i)
		{
			int u, v, w;
			scanf("%d %d %d", &u, &v, &w);
			addedge(u, v, w);
			addedge(v, u, w);
			mix(u, v);
		}
		BFS();
		while (q--)
		{
			solve();
		}
	}
}

int main()
{
#ifdef LOCAL_JUDGE
	freopen("Text.txt", "r", stdin);
#endif // LOCAL_JUDGE

	RUN();

#ifdef LOCAL_JUDGE
	fclose(stdin);
#endif // LOCAL_JUDGE
	return 0;
}

```

## POJ - 3417 Network 

题意：给出一棵树和一些非树边，你可以选择破坏一条树边以及一条非树边，问有多少种方式可以破坏这棵树。

思路：

对于每条非树边$u-v$都可以形成一个$u-LCA_{u, v}-v-u$的环，如果破坏这个这个环则只能选择环上一个树边以及新加入的非树边。

如果某条树边被多个环覆盖那么无法通过破坏这条边以及非树边来破坏整棵树。

如果某条树边没有被环覆盖那么可以通过破坏这条树边以及任意一条非树边来破坏整棵树。

最后可以通过$LCA$以及树上差分(前缀和？)来优化求出每条树边被多少个环覆盖。

```C++

#include<cstdio>
#include<iostream>
#include<cmath>
#include<cstring>
#include<algorithm>
#include<queue>

using namespace std;

typedef long long ll;
const int maxn = 1e6 + 10;
const int DEG = 20;

struct Edge {
	int to, nxt;
	Edge() {}
	Edge(int to, int nxt) :to(to), nxt(nxt) {}
}edge[maxn << 1];

int head[maxn], tot;

int dis[maxn];

void addedge(int u, int v)
{
	edge[tot] = Edge(v, head[u]);
	head[u] = tot++;
}

void init()
{
	memset(head, -1, sizeof head);
	tot = 0;
}

int fa[maxn][DEG];
int deg[maxn];

void BFS(int root)
{
	queue<int>q;
	deg[root] = 0;
	fa[root][0] = root;
	q.push(root);
	while (!q.empty())
	{
		int tmp = q.front();
		q.pop();
		for (int i = 1; i < DEG; ++i)
		{
			fa[tmp][i] = fa[fa[tmp][i - 1]][i - 1];
		}
		for (int i = head[tmp]; i != -1; i = edge[i].nxt)
		{
			int v = edge[i].to;
			if (v == fa[tmp][0]) continue;
			deg[v] = deg[tmp] + 1;
			fa[v][0] = tmp;
			q.push(v);
		}
	}
}

int LCA(int u, int v)
{
	if (deg[u] > deg[v]) swap(u, v);
	int hu = deg[u];
	int hv = deg[v];
	int tu = u, tv = v;
	for (int det = hv - hu, i = 0; det; det >>= 1, ++i)
	{
		if (det & 1)
		{
			tv = fa[tv][i];
		}
	}
	if (tu == tv) return tu;
	for (int i = DEG - 1; i >= 0; --i)
	{
		if (fa[tu][i] == fa[tv][i])
			continue;
		tu = fa[tu][i];
		tv = fa[tv][i];
	}
	return fa[tu][0];
}

int n, m;
ll arr[maxn];

void DFS(int u, int pre)
{
	for (int i = head[u]; ~i; i = edge[i].nxt)
	{
		int v = edge[i].to;
		if (v == pre) continue;
		DFS(v, u);
		arr[u] += arr[v];
	}
}

void RUN()
{
	while (~scanf("%d %d", &n, &m))
	{
		init();
		memset(arr, 0, sizeof arr);
		for (int i = 1; i < n; ++i)
		{
			int u, v;
			scanf("%d %d", &u, &v);
			addedge(u, v);
			addedge(v, u);
		}
		BFS(1);
		for (int i = 1; i <= m; ++i)
		{
			int u, v;
			scanf("%d %d", &u, &v);
			arr[u]++;
			arr[v]++;
			arr[LCA(u, v)] -= 2;
		}
		DFS(1, -1);
		ll ans = 0;
		for (int i = 2; i <= n; ++i)
		{
			if (!arr[i])
			{
				ans += m;
			}
			else if (arr[i] == 1)
			{
				ans++;
			}
		}
		printf("%lld\n", ans);
	}
}

int main()
{
#ifdef LOCAL_JUDGE
	freopen("Text.txt", "r", stdin);
#endif // LOCAL_JUDGE

	RUN();

#ifdef LOCAL_JUDGE
	fclose(stdin);
#endif // LOCAL_JUDGE

	return 0;
}

```

## UVALive - 8196 Imperial roads

题意：给出一张图，然后询问给出一条边，求有这条边的最小生成树的权值和。

思路：

先求最小生成树，然后询问的边如果在最小生成树里面那么就是原来的最小生成树的权值和。

否则在原来的最小生成树里面的加入一条边，形成个环，然后去掉这个环里面除了加入的边之外的边权最大的边即可。

```C++

#include<bits/stdc++.h>

using namespace std;

typedef long long ll;
const int maxn = 2e5 + 10;
const int DEG = 20;
int n, m;


struct node {
    int u, v;
    ll w;

    node() {}
    
    node(int u, int v, ll w) : u(u), v(v), w(w) {}
    
    inline bool operator<(const node &b) const {
        return w < b.w;
    }
};


struct Edge {
    int to;
    int nxt;
    ll w;

    Edge() {}
    
    Edge(int to, int nxt, ll w) : to(to), nxt(nxt), w(w) {}
} edge[maxn << 1];


int dis[maxn][DEG];
bool inMST[maxn << 1];
int head[maxn];
int tot;
int father[maxn];
vector<node> G;
int cnt;
map<pair<int, int>, int> mp;

void addedge(int u, int v, ll w) {
    edge[tot] = Edge(v, head[u], w);
    head[u] = tot++;
    edge[tot] = Edge(u, head[v], w);
    head[v] = tot++;
}

void Init() {
    G.clear();
    cnt = 0;
    tot = 0;
    memset(dis, 0, sizeof dis);
    memset(head, -1, sizeof head);
    for (int i = 1; i <= n; ++i) {
        father[i] = i;
    }
}


int find(int x) {
    return x == father[x] ? father[x] : father[x] = find(father[x]);
}


void mix(int x, int y) {
    x = find(x);
    y = find(y);
    if (x != y) {
        father[x] = y;
    }
}

bool same(int x, int y) {
    return find(x) == find(y);
}


ll MST() {
    mp.clear();


    ll res = 0;
    
    sort(G.begin(), G.end());
    memset(inMST,false, sizeof inMST);
    for (auto it: G) {
        int u = it.u;
        int v = it.v;
        mp[make_pair(v, u)] =cnt;
        mp[make_pair(u, v)] = cnt++;
    }
    for (auto it: G) {
        int u = it.u;
        int v = it.v;
        if (same(u, v))
            continue;
        mix(u, v);
        inMST[mp[make_pair(u, v)]] = true;
        addedge(u, v, it.w);
        res += it.w;
    }
    return res;
}

int fa[maxn][DEG];
int deg[maxn];

void BFS(int root) {
    queue<int> q;
    deg[root] = 0;
    fa[root][0] =root;
    q.push(root);
    while (!q.empty()) {
        int tmp = q.front();
        q.pop();
        for (int i = 1;i < DEG;++i) {
            dis[tmp][i] =max(dis[tmp][i - 1], dis[fa[tmp][i - 1]][i - 1]);
            fa[tmp][i] = fa[fa[tmp][i - 1]][i - 1];
        }
        for (int i = head[tmp];~i;i = edge[i].nxt) {
            int v = edge[i].to;
            if (v == fa[tmp][0]) continue;
            deg[v] = deg[tmp] + 1;
            fa[v][0] =tmp;
            int id = mp[make_pair(tmp, v)];
            dis[v][0] = G[id].w;
            q.push(v);
        }
    }
}


int LCA(int u, int v) {
    int res = 0;
    if (deg[u] > deg[v])swap(u, v);
    int hu = deg[u], hv = deg[v];
    int tu = u, tv = v;
    for (int det = hv - hu, i = 0;det;det >>= 1, ++i) {
        if (det & 1) {
            res = max(res, dis[tv][i]);
            tv = fa[tv][i];
        }
    }
    if (tu == tv)return res;
    for (int i = DEG - 1;i >= 0; --i) {
        if (fa[tu][i] == fa[tv][i]) continue;
        res = max(res, max(dis[tu][i], dis[tv][i]));
        tu = fa[tu][i];
        tv = fa[tv][i];
    }
    res = max(res, max(dis[tu][0], dis[tv][0]));
    return res;
}


void RUN() {
    while (~scanf("%d %d", &n, &m)) {
        Init();
        for (int i = 1;i <=m;++i) {
            int u, v;
            ll w;
            scanf("%d %d %lld", &u, &v, &w);
            G.push_back(node(u, v, w));
        }
        ll ans = MST();
        BFS(1);
        int q;
        scanf("%d", &q);
        while (q--) {
            int u, v;
            scanf("%d %d", &u, &v);
            int id = mp[make_pair(u, v)];
            ll w = G[id].w;
            if (inMST[id]) {
                printf("%lld\n", ans);
            } else {
                ll res = LCA(u, v);
                printf("%lld\n", ans + w - res);
            }
        }
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("Text.txt", "r", stdin);
#endif // LOCAL_JUDGE
    
    RUN();

#ifdef LOCAL_JUDGE

    fclose(stdin);
#endif // LOCAL_JUDGE
    return 0;
}

```

## HDU - 4547 CD操作

题意：

思路：

从$u-v$的操作数为$dis_u-dis_{LCA{u, v}}$

```C++

#include<bits/stdc++.h>

using namespace std;

typedef long long ll;
const int maxn = 1e5 + 10;
const int DEG = 20;

struct Edge {
	int to, nxt;
	inline Edge() {}
	inline Edge(int to, int nxt) :to(to), nxt(nxt) {}
}edge[maxn << 1];

int head[maxn], tot;

map<string, int>mp;
int dis[maxn];

inline void addedge(int u, int v)
{
	edge[tot] = Edge(v, head[u]);
	head[u] = tot++;
}

inline void init()
{
	memset(head, -1, sizeof head);
	tot = 0;
}

int fa[maxn][DEG];
int deg[maxn];

inline void BFS(int root)
{
	queue<int>q;
	deg[root] = 0;
	dis[root] = 0;
	fa[root][0] = root;
	q.push(root);
	while (!q.empty())
	{
		int tmp = q.front();
		q.pop();
		for (int i = 1; i < DEG; ++i)
		{
			fa[tmp][i] = fa[fa[tmp][i - 1]][i - 1];
		}
		for (int i = head[tmp]; i != -1; i = edge[i].nxt)
		{
			int v = edge[i].to;
			if (v == fa[tmp][0]) continue;
			deg[v] = deg[tmp] + 1;
			dis[v] = dis[tmp] + 1;
			fa[v][0] = tmp;
			q.push(v);
		}
	}
}

inline int LCA(int u, int v)
{
	if (deg[u] > deg[v]) swap(u, v);
	int hu = deg[u];
	int hv = deg[v];
	int tu = u, tv = v;
	for (int det = hv - hu, i = 0; det; det >>= 1, ++i)
	{
		if (det & 1)
		{
			tv = fa[tv][i];
		}
	}
	if (tu == tv) return tu;
	for (int i = DEG - 1; i >= 0; --i)
	{
		if (fa[tu][i] == fa[tv][i])
			continue;
		tu = fa[tu][i];
		tv = fa[tv][i];
	}
	return fa[tu][0];
}

int n, m;
bool flag[maxn];

inline void RUN()
{
	int t;
	cin >> t;
	while (t--)
	{
		cin >> n >> m;
		init();
		mp.clear();
		int cnt = 1;
		memset(flag, false, sizeof flag);
		for (int i = 1; i < n; ++i)
		{
			string s1, s2;
			cin >> s1 >> s2;
			if (mp[s1] == 0)
			{
				mp[s1] = cnt++;
			}
			if (mp[s2] == 0)
			{
				mp[s2] = cnt++;
			}
			addedge(mp[s2], mp[s1]);
			addedge(mp[s1], mp[s2]);
			flag[mp[s1]] = true;
		}
		int root = 0;
		for (int i = 1; i < cnt; ++i)
		{
			if (!flag[i])
			{
				root = i;
				break;
			}
		}
		BFS(root);
		while (m--)
		{
			string s1, s2;
			cin >> s1 >> s2;
			int u = mp[s1];
			int v = mp[s2];
			int tmp = LCA(u, v);
			int ans = dis[u] - dis[tmp];
			if (tmp != v)
			{
				ans++;
			}
			cout << ans << endl;
		}
	}
}

int main()
{
#ifdef LOCAL_JUDGE
	freopen("Text.txt", "r", stdin);
#endif // LOCAL_JUDGE

	RUN();

#ifdef LOCAL_JUDGE
	fclose(stdin);
#endif // LOCAL_JUDGE

	return 0;
}

```

## ZOJ - 4048 Red Black Tree

题意：有一个树，上面有红点和黑点，有边权，每个点的花费定义为它到离它最近的红点的距离，每次询问给出一些点，能够将这棵树中的一个黑点变为红点，使得这些点中的最大花费最小

思路：

二分答案，符合条件的点不管，将不符合条件的点LCA求出来，变红，然后算距离

ST表求LCA可直接过，复杂度为$O(n \cdot log_n)$。

倍增求LCA复杂度为$O(n \cdot log_n \cdot log_n)$。

倍增求LCA可先根据深度排序，当有两个不符合的点上方的红点不同时则不能通过修改一个点使得所有不符合的点都产生改变，从而剪枝。

```C++

#include<bits/stdc++.h>

using namespace std;

typedef long long ll;

const int INF = 0x3f3f3f3f;
const ll INFLL = 0x3f3f3f3f3f3f3f3f;
const ll MOD = (int)1e9 + 7;
const int maxn = (int)1e5 + 10;

const int DEG = 20;

struct Edge {
	int to, nxt;
	ll w;
	Edge() {}
	Edge(int to, int nxt, ll w) :to(to), nxt(nxt), w(w) {}
}edge[maxn << 1];

int head[maxn], tot;
int red[maxn];

void addedge(int u, int v, ll w)
{
	edge[tot] = Edge(v, head[u], w); head[u] = tot++;
}

void Init(int n)
{
	for (int i = 1; i <= n; ++i)
	{
		red[i] = 0;
		head[i] = -1;
	}
	tot = 0;
}

ll dis[maxn];
int fa[maxn][DEG];
int deg[maxn];
int pre[maxn];

void BFS(int root)
{
	queue<int>q;
	dis[root] = 0;
	deg[root] = 0;
	fa[root][0] = root;
	pre[root] = root;
	q.push(root);
	while (!q.empty())
	{
		int tmp = q.front();
		q.pop();
		for (int i = 1; i < DEG; ++i)
		{
			fa[tmp][i] = fa[fa[tmp][i - 1]][i - 1];
		}
		for (int i = head[tmp]; ~i; i = edge[i].nxt)
		{
			int v = edge[i].to;
			ll w = edge[i].w;
			if (v == fa[tmp][0]) continue;
			deg[v] = deg[tmp] + 1;
			fa[v][0] = tmp;
			if (red[v])
			{
				pre[v] = v;
			}
			else
			{
				pre[v] = pre[tmp];
			}
			if (red[v])
			{
				dis[v] = 0;
			}
			else
			{
				dis[v] = dis[tmp] + w;
			}
			q.push(v);
		}
	}
}

int LCA(int u, int v)
{
	if (deg[u] > deg[v]) swap(u, v);
	int hu = deg[u], hv = deg[v];
	int tu = u, tv = v;
	for (int det = hv - hu, i = 0; det; det >>= 1, ++i)
	{
		if (det & 1)
		{
			tv = fa[tv][i];
		}
	}
	if (tu == tv) return tu;
	for (int i = DEG - 1; i >= 0; --i)
	{
		if (fa[tu][i] == fa[tv][i]) continue;
		tu = fa[tu][i], tv = fa[tv][i];
	}
	return fa[tu][0];
}

int n, m, q, k;
int arr[maxn];

bool cmp(int a, int b)
{
	return dis[a] > dis[b];
}

bool check(ll mid)
{
	int root = arr[1];
	int cnt = 0;
	for (int i = 1; i <= k; ++i)
	{
		if (dis[arr[i]] > mid)
		{
			if (pre[root] != pre[arr[i]]) return false;
			root = LCA(root, arr[i]);
			cnt++;
		}
	}
	if (cnt == 1 || cnt == 0) return true;
	for (int i = 1; i <= k; ++i)
	{
		if (dis[arr[i]] > mid)
		{
			if (dis[arr[i]] - dis[root] > mid) return false;
		}
	}
	return true;
}

void RUN()
{
	int t;
	scanf("%d", &t);
	while (t--)
	{
		scanf("%d %d %d", &n, &m, &q);
		Init(n);
		for (int i = 1; i <= m; ++i)
		{
			int u;
			scanf("%d", &u);
			red[u] = 1;
		}
		for (int i = 1; i < n; ++i)
		{
			int u, v;
			ll w;
			scanf("%d %d %lld", &u, &v, &w);
			addedge(u, v, w);
			addedge(v, u, w);
		}
		BFS(1);
		while (q--)
		{
			scanf("%d", &k);
			for (int i = 1; i <= k; ++i)
			{
				scanf("%d", arr + i);
			}
			if (k == 1)
			{
				puts("0");
				continue;
			}
			sort(arr + 1, arr + 1 + k, cmp);
			ll l = 0;
			ll r = INFLL;
			ll ans = 0;
			while (r - l >= 0)
			{
				ll mid = (r + l) >> 1;
				if (check(mid))
				{
					r = mid - 1;
					ans = mid;
				}
				else
				{
					l = mid + 1;
				}
			}
			printf("%lld\n", ans);
		}
	}
}

int main()
{
#ifdef LOCAL_JUDGE
	freopen("Text.txt", "r", stdin);
#endif // LOCAL_JUDGE

	RUN();

#ifdef LOCAL_JUDGE
	fclose(stdin);
#endif // LOCAL_JUDGE
	return 0;
}

```