---
title: 	 2019 Multi-University Training Contest 3
date:   2019-07-29 21:15:00 +0800
tags: [HUD多校]
description: 2019 Multi-University Training Contest 
---

## B. Blow up the city

题意：给出一个$DAG$，问删除一个点使得$a,b$其中一个点不能到达所有的出度为$0$的点的方案

思路：根据$DAG$维护支配树，那么方案数为$deep[a]+deep[b]-deep[LCA_{a, b}]-1$

```C++

#include <bits/stdc++.h>

using namespace std;

#define N 200010
#define DEG 20

struct Graph {
    struct Edge {
        int to, nxt;

        Edge() {}
    
        Edge(int _to, int _nxt) {
            to = _to;
            nxt = _nxt;
        }
    } edge[N << 1];
    
    int head[N], tot;
    
    void Init() {
        memset(head, -1, sizeof head);
        tot = 0;
    }
    
    void addedge(int u, int v) {
        edge[tot] = Edge(v, head[u]);
        head[u] = tot++;
    }
} G[2];

int n, m, q;
int fa[N][DEG], dep[N], d[N], sze[N];

struct Tree {
    void add(int u, int v) {
        fa[v][0] = u;
        dep[v] = dep[u] + 1;
        for (int i = 1; i < DEG; ++i) {
            fa[v][i] = fa[fa[v][i - 1]][i - 1];
        }
    }

    int LCA(int u, int v) {
        if (!u) return v;
        if (!v) return u;
        if (dep[u] > dep[v]) {
            swap(u, v);
        }
        for (int det = dep[v] - dep[u], i = 0; det; det >>= 1, ++i) {
            if (det & 1) {
                v = fa[v][i];
            }
        }
        if (u == v) return u;
        for (int i = DEG - 1; i >= 0; --i) {
            if (fa[u][i] == fa[v][i]) {
                continue;
            }
            u = fa[u][i];
            v = fa[v][i];
        }
        return fa[u][0];
    }
    
    int GET(int u) {
        int lca = 0;
        for (int i = G[1].head[u]; ~i; i = G[1].edge[i].nxt) {
            lca = LCA(lca, G[1].edge[i].to);
        }
        return lca;
    }
    
    void solve() {
        int S = n + 1;
        add(S, S);
        queue<int> que;
        for (int i = 1; i <= n; ++i) {
            if (d[i] == 0) {
                que.push(i);
                add(S, i);
            }
        }
        while (!que.empty()) {
            int u = que.front();
            que.pop();
            for (int i = G[0].head[u]; ~i; i = G[0].edge[i].nxt) {
                int v = G[0].edge[i].to;
                --d[v];
                if (d[v] == 0) {
                    que.push(v);
                    add(GET(v), v);
                }
            }
        }
    }
} tree;

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d %d", &n, &m);
        G[0].Init(), G[1].Init();
        for (int i = 0; i < n + 2; ++i) {
            dep[i] = 0;
            d[i] = 0;
            sze[i] = 0;
            fa[i][0] = 0;
        }
        for (int i = 1, u, v; i <= m; ++i) {
            scanf("%d %d", &u, &v);
            G[0].addedge(v, u);
            G[1].addedge(u, v);
            ++d[u];
        }
        tree.solve();
        scanf("%d", &q);
        while (q--) {
            int a, b;
            scanf("%d %d", &a, &b);
            printf("%d\n", dep[a] + dep[b] - dep[tree.LCA(a, b)] - 1);
        }
    }
    return 0;
}

```

## D. Distribution of books

题意：将$n$个数分为$k$段，每段起码一个值，$k$段以后可以丢弃，求最小的最大值。

思路：二分答案$x$，$f[i]$表示$i$元素末尾的最大段数，显然$j \rightarrow i$的条件是$sum[i] - sum[j] <= x$，线段树统计这样的$j$的最大$f[j]$。

```C++

#include <bits/stdc++.h>
using namespace std;

namespace IO
{
    const int S=(1<<20)+5;
    //Input Correlation
    char buf[S],*H,*T;
    inline char Get()
    {
        if(H==T) T=(H=buf)+fread(buf,1,S,stdin);
        if(H==T) return -1;return *H++;
    }
    inline int read()
    {
        int x=0,fg=1;char c=Get();
        while(!isdigit(c)&&c!='-') c=Get();
        if(c=='-') fg=-1,c=Get();
        while(isdigit(c)) x=x*10+c-'0',c=Get();
        return x*fg;
    }
    inline void reads(char *s)
    {
        char c=Get();int tot=0;
        while(c<'a'||c>'z') c=Get();
        while(c>='a'&&c<='z') s[++tot]=c,c=Get();
        s[++tot]='\0';
    }
    //Output Correlation
    char obuf[S],*oS=obuf,*oT=oS+S-1,c,qu[55];int qr;
    inline void flush(){fwrite(obuf,1,oS-obuf,stdout);oS=obuf;}
    inline void putc(char x){*oS++ =x;if(oS==oT) flush();}
    template <class I>inline void print(I x)
    {
        if(!x) putc('0');
        if(x<0) putc('-'),x=-x;
        while(x) qu[++qr]=x%10+'0',x/=10;
        while(qr) putc(qu[qr--]);
    }
    inline void prints(const char *s)
    {
        int len=strlen(s);
        for(int i=0;i<len;i++) putc(s[i]);
        putc('\n');
    }
    inline void printd(int d,double x)
    {
        long long t=(long long)floor(x);
        print(t);putc('.');x-=t;
        while(d--)
        {
            double y=x*10;x*=10;
            int c=(int)floor(y);
            putc(c+'0');x-=floor(y);
        }
    }
}
using namespace IO;

#define ll long long
#define INF 0x3f3f3f3f
#define N 400010
int n, k;
ll a[N], b[N]; 

void Hash(ll *b) {
    sort(b + 1, b + 1 + b[0]);
    b[0] = unique(b + 1, b + 1 + b[0]) - b - 1;
}

int get(ll x) {
    return lower_bound(b + 1, b + 1 + b[0], x) - b; 
}

struct SEG {
    struct node {
        int Max;
        node() {
            Max = -INF;
        }
        node operator + (const node &other) const {
            node res = node();
            res.Max = max(Max, other.Max);
            return res;
        }
    }t[N << 2];
    void build(int id, int l, int r) {
        t[id] = node();
        if (l == r) return;
        int mid = (l + r) >> 1;
        build(id << 1, l, mid);
        build(id << 1 | 1, mid + 1, r);
    }
    void update(int id, int l, int r, int pos, int x) {
        if (l == r) {
            t[id].Max = max(t[id].Max, x);
            return;
        }
        int mid = (l + r) >> 1;
        if (pos <= mid) update(id << 1, l, mid, pos, x);
        else update(id << 1 | 1, mid + 1, r, pos, x);
        t[id] = t[id << 1] + t[id << 1 | 1];
    }
    int query(int id, int l, int r, int ql, int qr) {
        if (ql > qr) return -INF;
        if (l >= ql && r <= qr) {
            return t[id].Max;
        }
        int mid = (l + r) >> 1;
        int res = -INF;
        if (ql <= mid) res = max(res, query(id << 1, l, mid, ql, qr));
        if (qr > mid) res = max(res, query(id << 1 | 1, mid + 1, r, ql, qr));
        return res;
    }
}seg;

bool check(ll x) {
    b[0] = 0;
    b[++b[0]] = 0;
    for (int i = 1; i <= n; ++i) {
        b[++b[0]] = a[i];
        b[++b[0]] = a[i] - x;
    }    
    Hash(b);
    seg.build(1, 1, b[0]);
    seg.update(1, 1, b[0], get(0), 0);
    for (int i = 1; i <= n; ++i) {
        int f = seg.query(1, 1, b[0], get(a[i] - x), b[0]) + 1;
        if (f >= k) return 1;
        seg.update(1, 1, b[0], get(a[i]), f);
    }
    return 0;
}

int main() {
    int T; T = read();
    while (T--) {
        n = read(); k = read();
        for (int i = 1; i <= n; ++i) {
            a[i] = read();
            a[i] += a[i - 1];
        }
        ll l = -2e15, r = 1e9, res = -1;
        while (r - l >= 0) {
            ll mid = (l + r) >> 1;
            if (check(mid)) {
                r = mid - 1;
                res = mid;
            } else {
                l = mid + 1;
            }
        }
        printf("%lld\n", res);
    }
    return 0;
}

```

## F. Fansblog

题意：找一个最大的素数$Q(Q < P)$，问$Q!\%P$

思路：已知$(P-1)! \%P=P-1$，暴力找到$Q$，通过逆元得到$Q!\%P$。

```C++

#include <bits/stdc++.h>
using namespace std;

#define ll long long
ll p;
const int C = 2307;
const int S = 10;
mt19937_64 rd(time(0));
ll gcd(ll a, ll b) {
    return b ? gcd(b, a % b) : a;
}
ll mul(ll a, ll b, ll p) {
    return (a * b - (ll)(a / (long double)p * b + 1e-3) * p + p) % p;
}
ll qmod(ll base, ll n, ll p) {
    ll res = 1;
    base %= p;
    while (n) {
        if (n & 1) {
            res = mul(res, base, p);
        }
        base = mul(base, base, p);
        n >>= 1;
    }
    return res;
}
bool check(ll a, ll n) {
    ll m = n - 1, x, y;
    int j = 0;
    while (!(m & 1)) {
        m >>= 1;
        ++j;
    }
    x = qmod(a, m, n);
    for (int i = 1; i <= j; x = y, ++i) {
        y = mul(x, x, n);
        if (y == 1 && x != 1 && x != n - 1) {
            return 1;
        }
    }
    return y != 1;
}
bool miller_rabin(ll n) {
    if (n < 2) {
        return 0;
    } else if (n == 2) {
        return 1;
    } else if (! (n & 1)) {
        return 0;
    }
    for (int i = 0; i < S; ++i) {
        if (check(rd() % (n - 1) + 1, n)) {
            return 0;
        }
    }
    return 1;
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%lld", &p);
        ll n;
        for (ll i = p - 1; ; --i) {
            if (miller_rabin(i)) {
                n = i;
                break;
            }
        }
        ll res = p - 1;
        for (ll i = p - 1; i > n; --i) {
            res = mul(res, qmod(i, p - 2, p), p);
        }
        printf("%lld\n", res);
    }
    return 0;
}

```



## G. Find the answer

题意：对于每个$i$，可以修改$W_k(1\leq k < i)=0$，问最小的修改数满足$\sum_{j=1}^iW_j\leq m$

思路：权值线段树上二分即可。

```C++
#include <bits/stdc++.h>
using namespace std;

namespace IO
{
    const int S=(1<<20)+5;
    //Input Correlation
    char buf[S],*H,*T;
    inline char Get()
    {
        if(H==T) T=(H=buf)+fread(buf,1,S,stdin);
        if(H==T) return -1;return *H++;
    }
    inline int read()
    {
        int x=0,fg=1;char c=Get();
        while(!isdigit(c)&&c!='-') c=Get();
        if(c=='-') fg=-1,c=Get();
        while(isdigit(c)) x=x*10+c-'0',c=Get();
        return x*fg;
    }
    inline void reads(char *s)
    {
        char c=Get();int tot=0;
        while(c<'a'||c>'z') c=Get();
        while(c>='a'&&c<='z') s[++tot]=c,c=Get();
        s[++tot]='\0';
    }
    //Output Correlation
    char obuf[S],*oS=obuf,*oT=oS+S-1,c,qu[55];int qr;
    inline void flush(){fwrite(obuf,1,oS-obuf,stdout);oS=obuf;}
    inline void putc(char x){*oS++ =x;if(oS==oT) flush();}
    template <class I>inline void print(I x)
    {
        if(!x) putc('0');
        if(x<0) putc('-'),x=-x;
        while(x) qu[++qr]=x%10+'0',x/=10;
        while(qr) putc(qu[qr--]);
    }
    inline void prints(const char *s)
    {
        int len=strlen(s);
        for(int i=0;i<len;i++) putc(s[i]);
        putc('\n');
    }
    inline void printd(int d,double x)
    {
        long long t=(long long)floor(x);
        print(t);putc('.');x-=t;
        while(d--)
        {
            double y=x*10;x*=10;
            int c=(int)floor(y);
            putc(c+'0');x-=floor(y);
        }
    }
}
using namespace IO;

#define ll long long
#define N 200010
int n, m, a[N], b[N]; 
struct SEG {
    struct node {
        ll sum; int cnt;
        node() {
            sum = cnt = 0;
        }
        node (ll sum, int cnt) : sum(sum), cnt(cnt) {}
        node operator + (const node &other) const {
            node res = node();
            res.sum = sum + other.sum;
            res.cnt = cnt + other.cnt;
            return res;
        }
    }t[N << 2];
    void build(int id, int l, int r) {
        t[id] = node();
        if (l == r) return;
        int mid = (l + r) >> 1;
        build(id << 1, l, mid);
        build(id << 1 | 1, mid + 1, r);
    }
    void update(int id, int l, int r, int x) {
        if (l == r) {
            t[id].sum += b[l];
            ++t[id].cnt;
            return;
        }
        int mid = (l + r) >> 1;
        if (x <= mid) update(id << 1, l, mid, x);
        else update(id << 1 | 1, mid + 1, r, x);
        t[id] = t[id << 1] + t[id << 1 | 1];
    }
    int query(int id, int l, int r, ll need) {
        if (need <= 0) return 0;
        if (l == r) {
            return need / b[l] + (need % b[l] != 0);
        }
        int mid = (l + r) >> 1;
        if (t[id << 1 | 1].sum >= need) 
            return query(id << 1 | 1, mid + 1, r, need);
        else
            return t[id << 1 | 1].cnt + query(id << 1, l, mid, need - t[id << 1 | 1].sum);
    }
}seg;

void Hash(int *b) {
    sort(b + 1, b + 1 + b[0]);
    b[0] = unique(b + 1, b + 1 + b[0]) - b - 1;
}

int get(int x) {
    return lower_bound(b + 1, b + 1 + b[0], x) - b;
}

int main() {
    int T; T = read();
    while (T--) {
        n = read(); m = read();
        b[0] = 0;
        for (int i = 1; i <= n; ++i) a[i] = read(), b[++b[0]] = a[i];
        Hash(b);
        seg.build(1, 1, b[0]);
        ll sum = 0;
        for (int i = 1; i <= n; ++i) {
            sum += a[i];
            print(seg.query(1, 1, b[0], sum - m));
            putc(' ');
            seg.update(1, 1, b[0], get(a[i]));
        }
        putc('\n');
    }
    flush();
    return 0;
}
//

```

## I. K Subsequence

题意：选择$k$段不相交的上升序列，使得权值和最大

思路：$Dijkstra$优化费用流

```C++
#include <bits/stdc++.h>

using namespace std;

#define INF 0x3f3f3f3f
#define N 4010

namespace IO {
    const int S = (1 << 20) + 5;
    //Input Correlation
    char buf[S], *H, *T;

    inline char Get() {
        if (H == T) T = (H = buf) + fread(buf, 1, S, stdin);
        if (H == T) return -1;
        return *H++;
    }
    
    inline int read() {
        int x = 0, fg = 1;
        char c = Get();
        while (!isdigit(c) && c != '-') c = Get();
        if (c == '-') fg = -1, c = Get();
        while (isdigit(c)) x = x * 10 + c - '0', c = Get();
        return x * fg;
    }
    
    inline void reads(char *s) {
        char c = Get();
        int tot = 0;
        while (c < 'a' || c > 'z') c = Get();
        while (c >= 'a' && c <= 'z') s[++tot] = c, c = Get();
        s[++tot] = '\0';
    }
    
    //Output Correlation
    char obuf[S], *oS = obuf, *oT = oS + S - 1, c, qu[55];
    int qr;
    
    inline void flush() {
        fwrite(obuf, 1, oS - obuf, stdout);
        oS = obuf;
    }
    
    inline void putc(char x) {
        *oS++ = x;
        if (oS == oT) flush();
    }
    
    template<class I>
    inline void print(I x) {
        if (!x) putc('0');
        if (x < 0) putc('-'), x = -x;
        while (x) qu[++qr] = x % 10 + '0', x /= 10;
        while (qr) putc(qu[qr--]);
    }
    
    inline void prints(const char *s) {
        int len = strlen(s);
        for (int i = 0; i < len; i++) putc(s[i]);
        putc('\n');
    }
    
    inline void printd(int d, double x) {
        long long t = (long long) floor(x);
        print(t);
        putc('.');
        x -= t;
        while (d--) {
            double y = x * 10;
            x *= 10;
            int c = (int) floor(y);
            putc(c + '0');
            x -= floor(y);
        }
    }
}
using namespace IO;

struct edge {
    int to, capacity, cost, rev;

    edge() {}
    
    edge(int to, int _capacity, int _cost, int _rev) : to(to), capacity(_capacity), cost(_cost), rev(_rev) {}
};

struct Min_Cost_Max_Flow {
    int V, H[N + 5], dis[N + 5], PreV[N + 5], PreE[N + 5];
    vector<edge> G[N + 5];

    //调用前初始化
    void Init(int n) {
        V = n;
        for (int i = 0; i <= V; ++i)G[i].clear();
    }
    
    //加边
    void Add_Edge(int from, int to, int cap, int cost) {
        G[from].push_back(edge(to, cap, cost, G[to].size()));
        G[to].push_back(edge(from, 0, -cost, G[from].size() - 1));
    }
    
    //flow是自己传进去的变量，就是最后的最大流，返回的是最小费用
    int Min_cost_max_flow(int s, int t, int f, int &flow) {
        int res = 0;
        fill(H, H + 1 + V, 0);
        while (f) {
            priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> q;
            fill(dis, dis + 1 + V, INF);
            dis[s] = 0;
            q.push(pair<int, int>(0, s));
            while (!q.empty()) {
                pair<int, int> now = q.top();
                q.pop();
                int v = now.second;
                if (dis[v] < now.first)continue;
                for (int i = 0; i < G[v].size(); ++i) {
                    edge &e = G[v][i];
                    if (e.capacity > 0 && dis[e.to] > dis[v] + e.cost + H[v] - H[e.to]) {
                        dis[e.to] = dis[v] + e.cost + H[v] - H[e.to];
                        PreV[e.to] = v;
                        PreE[e.to] = i;
                        q.push(pair<int, int>(dis[e.to], e.to));
                    }
                }
            }
            if (dis[t] == INF)break;
            for (int i = 0; i <= V; ++i)H[i] += dis[i];
            int d = f;
            for (int v = t; v != s; v = PreV[v])d = min(d, G[PreV[v]][PreE[v]].capacity);
            f -= d;
            flow += d;
            res += d * H[t];
            for (int v = t; v != s; v = PreV[v]) {
                edge &e = G[PreV[v]][PreE[v]];
                e.capacity -= d;
                G[v][e.rev].capacity += d;
            }
        }
        return res;
    }
    
    int Max_cost_max_flow(int s, int t, int f, int &flow) {
        int res = 0;
        fill(H, H + 1 + V, 0);
        while (f) {
            priority_queue<pair<int, int>> q;
            fill(dis, dis + 1 + V, -INF);
            dis[s] = 0;
            q.push(pair<int, int>(0, s));
            while (!q.empty()) {
                pair<int, int> now = q.top();
                q.pop();
                int v = now.second;
                if (dis[v] > now.first)continue;
                for (int i = 0; i < G[v].size(); ++i) {
                    edge &e = G[v][i];
                    if (e.capacity > 0 && dis[e.to] < dis[v] + e.cost + H[v] - H[e.to]) {
                        dis[e.to] = dis[v] + e.cost + H[v] - H[e.to];
                        PreV[e.to] = v;
                        PreE[e.to] = i;
                        q.push(pair<int, int>(dis[e.to], e.to));
                    }
                }
            }
            if (dis[t] == -INF)break;
            for (int i = 0; i <= V; ++i)H[i] += dis[i];
            int d = f;
            for (int v = t; v != s; v = PreV[v])d = min(d, G[PreV[v]][PreE[v]].capacity);
            f -= d;
            flow += d;
            res += d * H[t];
            for (int v = t; v != s; v = PreV[v]) {
                edge &e = G[PreV[v]][PreE[v]];
                e.capacity -= d;
                G[v][e.rev].capacity += d;
            }
        }
        return res;
    }
} MCMF;

int n, k, s1, s2, t, a[N], flow;

int main() {
//    freopen("input.txt", "r", stdin);
    int cas = read();
    while (cas--) {
        n = read(), k = read();
        s1 = 0, s2 = 1, t = 2 * n + 2;
        MCMF.Init(t);
        MCMF.Add_Edge(s1, s2, k, 0);
        for (int i = 1; i <= n; ++i) {
            a[i] = read();
            MCMF.Add_Edge(s2, i << 1, 1, 0);
            MCMF.Add_Edge(i << 1 | 1, t, 1, 0);
            MCMF.Add_Edge(i << 1, i << 1 | 1, 1, -a[i]);
        }
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j < i; ++j) {
                if (a[i] >= a[j]) {
                    MCMF.Add_Edge(j << 1 | 1, i << 1, 1, 0);
                }
            }
        }
        print(-MCMF.Min_cost_max_flow(s1, t, INF, flow));
        putc('\n');
    }
    flush();
    return 0;
}

```