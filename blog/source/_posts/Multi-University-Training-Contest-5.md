---
title: Multi-University-Training-Contest-5
date: 2019-08-06 11:40:00 +0800
tags: [HDU多校]
description: Multi-University-Training-Contest-5
---

## B. three arrays

题意：给出两个序列$a, b$,生成序列$c$,其中$c_i=a_i\oplus b_i$，要求重新排列$a, b$，使得$c$字典序最小

思路：建立两颗$Trie$树，两指针分别在两颗$Trie$树上走，尽可能保证异或后为$0$，走$n$边后排序。对于$(0,0),(1,1)$谁先走，两对数只可能是不相同的两个数，所以顺序无所谓。

```C++

#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define N 200010 
#define pii pair <int, int>
#define fi first
#define se second
int n, a[N], b[N], res[N];

struct Trie {
    struct node {
        int son[2];
        int tot;
        node() {
            memset(son, -1, sizeof son);
            tot = 0;
        }
    }t[N * 32];
    int cnt;
    void init() {
        cnt = 1;
        t[1] = node();
    }
    void insert(int x, int y) {
        int root = 1;
        for (int i = 30; i >= 0; --i) {
            int &nx = t[root].son[(x >> i) & 1];
            if (nx == -1) {
                nx = ++cnt;
                t[nx] = node();
            }
            root = nx;
            t[root].tot += y;
        }
    }
    void del(int x, int y) {
        int root = 1;
        for (int i = 30; i >= 0; --i) {
            int nx = t[root].son[(x >> i) & 1];
            if (nx == -1) {
                return;    
            }
            t[nx].tot -= y;
            root = nx;
        }
    }
    int query(int x) {
        int root = 1;
        ll res = 0;
        for (int i = 30; i >= 0; --i) {
            int f = (x >> i) & 1;
            int nx = t[root].son[f];
            if (nx == -1 || t[nx].tot == 0) {
                res |= (1 << i);
                nx = t[root].son[!f];
            }
            root = nx;
        }
        return res; 
    }
}trie[2];

int Move[4][2] = {
    0, 0,
    1, 1,
    0, 1,
    1, 0
};

int query() {
    int num[2] = {0, 0};
    int it[2] = {1, 1};
    int nx[2];
    for (int i = 30; i >= 0; --i) {
        for (int j = 0; j < 4; ++j) {
            nx[0] = trie[0].t[it[0]].son[Move[j][0]];
            nx[1] = trie[1].t[it[1]].son[Move[j][1]];
            if (nx[0] != -1 && nx[1] != -1 && trie[0].t[nx[0]].tot > 0 && trie[1].t[nx[1]].tot > 0) {
                if (Move[j][0]) num[0] |= (1 << i);
                if (Move[j][1]) num[1] |= (1 << i);
                it[0] = nx[0];
                it[1] = nx[1];
                break;
            }
        }
    }
    trie[0].del(num[0], 1);
    trie[1].del(num[1], 1);
    return num[0] ^ num[1];
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        trie[0].init(); trie[1].init();
        for (int i = 1; i <= n; ++i) {
            scanf("%d", a + i);
            trie[0].insert(a[i], 1);
        }
        for (int i = 1; i <= n; ++i) {
            scanf("%d", b + i);
            trie[1].insert(b[i], 1);
        }
        for (int i = 1; i <= n; ++i) {
            res[i] = query();
        }
        sort(res + 1, res + 1 + n);
        for (int i = 1; i <= n; ++i)
            printf("%d%c", res[i], " \n"[i == n]);
    }
    return 0;
}

```

## D. equation

题意：给出序列$a, b$以及整数$C$，求所有实数$x$使得$\sum_{i=1}^n\vert a_i\cdot x+b_i\vert=C$

思路：根据每个绝对值取值为$0$可以将$x$划分为$n+1$个区间，在每个区间里都是一个线性方程，直接求解判断即可，注意无穷解的时候

```C++

#include <bits/stdc++.h>
using namespace std;

#define ll long long
ll gcd(ll a, ll b) {
    return b ? gcd(b, a % b) : a; 
}
struct frac{
    ll x,y;
    void sim() {
        ll g = gcd(abs(x), abs(y));
        x /= g;
        y /= g;
        if (x * y == 0) {
            x = 0;
            y = 1;
        }
        if (x * y < 0) {
            x = -abs(x);
            y = abs(y);
        } else {
            x = abs(x);
            y = abs(y);
        }
    }
    frac() {}
    frac(ll x, ll y) : x(x), y(y) {sim();}
    frac operator+(const frac &u){
        ll p, q; 
        p = x * u.y + y * u.x;
        q = u.y * y;
        ll d = gcd(p, q);
        p /= d; q /= d;
        return (frac){p, q};
    }
    frac operator-(const frac &u){
        ll p, q;
        p = x * u.y - y * u.x;
        q = u.y * y;
        ll d = gcd(p, q);
        p /= d; q /= d;
        return (frac){p, q};
    }
    frac operator*(const frac &u){
        ll p, q;
        p = u.x * x;
        q = u.y * y;
        ll d = gcd(p, q); 
        p /= d; q /= d;
        return (frac){p, q};
    }
    frac operator/(const frac &u){
        ll p, q;
        p = u.y * x;
        q = u.x * y;
        ll d = gcd(p,q);
        p /= d; q /= d;
        return (frac){p,q};
    }
    bool operator < (const frac &other) const {
        return x * other.y < y * other.x;
    }
    bool operator != (const frac &other) const {
        return x * other.y != y * other.x;
    }
    bool operator <= (const frac &other) const {
        return x * other.y <= y * other.x;
    }
    bool operator >= (const frac &other) const {
        return x * other.y >= y * other.x;
    }
    bool operator == (const frac &other) const {
        return x * other.y == y * other.x;
    }
    void sqr() {
        *this = (*this) * (*this);
    }
    void print(){
        sim();
        if (x * y < 0) putchar('-');
        printf("%lld/%lld", abs(x), abs(y));
    }
};

const int N = 2e5 + 10;
int n;
ll C;
struct node {
    int a, b;
    frac x;
    void scan() {
        scanf("%d%d", &a, &b);
        x = frac(-b, a); 
    }
    bool operator < (const node &other) const {
        return x < other.x;
    }
}a[N];
frac res[N << 1]; int cnt = 0; 

void solve() {
    cnt = 0;
    ll suma[2] = {0, 0}, sumb[2] = {0, 0};
    for (int i = 1; i <= n; ++i) {
        suma[1] += a[i].a;
        sumb[1] += a[i].b;
    }
    for (int i = 1, j; i < n; i = j + 1) {
        for (j = i; j < n; ++j) {
            if (a[i].x != a[j + 1].x) break;
        }
        j = min(j, n - 1);
        for (int k = i; k <= j; ++k) {
            suma[1] -= a[k].a;
            sumb[1] -= a[k].b;
            suma[0] += a[k].a;
            sumb[0] += a[k].b;
        }
        if (suma[0] - suma[1] == 0 && sumb[0] - sumb[1] == C) {
            puts("-1");
            return;
        }  
        if (suma[1] == suma[0]) continue;
        frac x = frac(C - sumb[0] + sumb[1], suma[0] - suma[1]);
        if (x >= a[i].x) {
            if (j + 1 < n) {
                if (x < a[j + 1].x) {
                    res[++cnt] = x;
                }
            } else {
                res[++cnt] = x;
            }
        }
    }
    sort(res + 1, res + 1 + cnt);
    cnt = unique(res + 1, res + 1 + cnt) - res - 1;
    printf("%d", cnt);
    for (int i = 1; i <= cnt; ++i) {
        putchar(' ');
        res[i].print();
    }
    puts("");
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%lld", &n, &C);
        for (int i = 1; i <= n; ++i) a[i].scan();
        ++n; a[n].a = a[n].b = 0; a[n].x = frac(-2e9, 1);
        sort(a + 1, a + 1 + n);
        ++n; a[n].a = a[n].b = 0; a[n].x = frac(2e9, 1);
        solve();    
    }
    return 0;    
}
//

```

## E. permutation 1

题意：构造一个长度为$n$的排列，使得差分排列是第$k$小

思路：枚举每一位放什么，并且记录后续有多少个值，贪心构造

```C++

#include <bits/stdc++.h>
using namespace std;

#define N 50
#define ll long long
int n, k, a[N], vis[N];
ll fac[N];

bool check(int x, int now) {
    for (int i = 1; i <= n; ++i) vis[i] = 0;
    vis[now] = 1;
    for (int i = 1; i <= x; ++i) {
        int nx = now + a[i];
        if (nx < 1 || nx > n) return 0;
        if (vis[nx]) return 0;
        vis[nx] = 1;
        now = nx;
    }
    return 1;
}

ll work(int x) {
    ll res = 0;
    for (int i = 1; i <= n; ++i) {
        if (check(x, i)) {
            res += fac[n - x - 1];
        }
    }
    return res;
}

void out() {
    for (int i = 1; i <= n; ++i) {
        if (check(n - 1, i)) {
            vector <int> vec;
            int now = i;
            vec.push_back(now);
            for (int j = 1; j < n; ++j) {
                now += a[j];
                vec.push_back(now);
            }
            for (int j = 0; j < n; ++j)
                printf("%d%c", vec[j], " \n"[j == n - 1]);
            return;
        }
    }
}

int main() {
    fac[0] = 1;
    for (int i = 1; i <= 20; ++i)
        fac[i] = fac[i - 1] * i;
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &k);
        for (int i = 1; i < n; ++i) { 
            ll tot = 0, now = 0;
            for (int j = -n + 1; j < n; ++j) {
                a[i] = j; 
                now = work(i);
                if (tot + now >= k) {
                    k -= tot;
                    break;  
                } else {
                    tot += now;
                }
            }    
        }
        out();
    }
    return 0;
}

```

## F. string matching

题意：询问$\forall i \in [0, len - 1]$中$s[i \cdots len - 1]$与$s[0 \cdots len - 1]$的暴力求$lcp$过程需要比较多少次。

思路：$ExKmp$

```C++

#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define N 1000010
char s[N], t[N];

struct ExKMP {
    int Next[N];
    int extend[N];
    //下标从1开始
    void get_Next(char *s) {
        int lens = strlen(s + 1), p = 1, pos;
        //Next[1]要特殊考虑
        Next[1] = lens;
        while (p + 1 <= lens && s[p] == s[p + 1]) ++p;
        //Next[2]初始化
        Next[pos = 2] = p - 1;
        for (int i = 3; i <= lens; ++i) {
            int len = Next[i - pos + 1];
            //第一种情况
            if (len + i < p + 1) Next[i] = len;
            //第二种情况
            else {
                //找到对于子串最靠后已经匹配的位置
                int j = max(p - i + 1, 0);
                //暴力匹配
                while (i + j <= lens && s[j + 1] == s[i + j]) ++j;
                p = i + (Next[pos = i] = j) - 1; 
            }
        }
    }

    void work(char *s, char *t) {
        get_Next(t);
        int lens = strlen(s + 1), lent = strlen(t + 1), p = 1, pos;
        while (p <= lent && s[p] == t[p]) ++p;
        p = extend[pos = 1] = p - 1;
        for (int i = 2; i <= lens; ++i) {
            int len = Next[i - pos + 1];
            if (len + i < p + 1) extend[i] = len;
            else {
                int j = max(p - i + 1, 0);
                while (i + j <= lens && j <= lent && t[j + 1] == s[i + j]) ++j;
                p = i + (extend[pos = i] = j) - 1;
            }
        }
    }
}exkmp;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%s", s + 1); 
        int len = strlen(s + 1);
        for (int i = 1; i <= len; ++i) t[i] = s[i]; t[len + 1] = 0;
        exkmp.work(s, t);
        ll res = 0;
        for (int i = 2; i <= len; ++i) {
            ll t = exkmp.extend[i];
            if (len - i + 1 > t) ++t;
            res += t;    
        }    
        printf("%lld\n", res);
    }
    return 0;
}
//

```

## G. permutation 2

题意：问有多少$n$个数的排列满足下列要求：
- $p_1 = x$
- $p_n = y$
- $\forall i \in [1, n - 1]$都满足$|p_i - p_{i + 1}| \leq 2$

思路:最终序列必定是$x,x-2\cdots x-1,x+1 \cdots y-1,y+1\cdots y+2,y $我们假设$x-2\cdots x-1$的排列有$p1$种，$y+1\cdots y+2$有$p_2$种，$x+1\cdots y-1$有$p_3$种，很显然，$p_1,p_2$均只有$1$种，对于$p_3$通过暴力打表发现满足序列$f[n]=f[n-1]+f[n-3]$其中$n$为长度，然后特判即可

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
const ll p = 998244353;

#define N 100010

int n, x, y;
ll a[N];

void Init() {
    a[0] = a[1] = a[2] = 1;
    for (int i = 3; i < N; ++i) {
        a[i] = (a[i - 1] + a[i - 3]) % p;
    }
}

int main() {
    Init();
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d %d %d", &n, &x, &y);
        if (x == 1 && y == n) {
            printf("%lld\n", a[y - x]);
            continue;
        }
        if (x == 1 || y == n) {
            printf("%lld\n", a[y - x - 1]);
            continue;
        }
        if (y - x != 1) {
            printf("%lld\n", a[y - x - 2]);
        } else {
            if (x == 1) {
                puts("1");
            } else {
                puts("0");
            }
        }
    }
    return 0;
}

```

## H. line symmetric

题意：给出一个简单多边形，问至多修改一个点，满足整个简单多边形是边对称简单多边形

思路：暴力枚举每对相邻，间隔一个的点对的中垂线最为对称点，$O(n)$的去$check$.

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 2010

int n;

int sgn(ll x) {
    if (x == 0) return 0;
    else return x > 0 ? 1 : -1;
}

struct Point {
    ll x, y;

    Point() {}
    
    Point(ll _x, ll _y) {
        x = _x;
        y = _y;
    }
    
    void input() {
        scanf("%lld %lld", &x, &y);
        x <<= 1;
        y <<= 1;
    }
    
    Point operator+(const Point &other) const {
        return {x + other.x, y + other.y};
    }
    
    Point operator-(const Point &other) const {
        return {x - other.x, y - other.y};
    }
    
    ll operator*(const Point &other) const {
        return x * other.x + y * other.y;
    }
    
    Point operator/(const ll &other) const {
        return {x / other, y / other};
    }
    
    ll operator^(const Point &other) const {
        return x * other.y - y * other.x;
    }
} P[N];

bool Add(Point p[2], Point V[2], int num[2], int pos[2], int &now, Point p1, Point p2, int i) {
    if (now == 0) {
        p[now] = (p1 + p2) / 2;
        V[now++] = {p2.y - p1.y, p1.x - p2.x};
        num[0] = 1;
        pos[0] = i;
    } else {
        if (((((p1 + p2) / 2) - p[0]) ^ V[0]) != 0 || ((p1 - p2) * V[0]) ||
            (Point(p2.y - p1.y, p1.x - p2.x) * V[0]) < 0) {
            if (now == 1) {
                p[now] = (p1 + p2) / 2;
                V[now++] = {p2.y - p1.y, p1.x - p2.x};
                num[1] = 1;
                pos[1] = i;
            } else {
                if (((((p1 + p2) / 2) - p[1]) ^ V[1]) != 0 || ((p1 - p2) * V[1]) ||
                    (Point(p2.y - p1.y, p1.x - p2.x) * V[1]) < 0) {
                    return false;
                } else {
                    num[1]++;
                }
            }
        } else {
            num[0]++;
        }
    }
    return true;
}

bool check1(Point p[]) {
    Point X[2], V[2];
    int num[2] = {0, 0}, pos[2] = {0, 0}, now = 0, i;
    for (i = 1; i < n - i; ++i) {
        if (!Add(X, V, num, pos, now, p[i], p[n - i], i)) {
            return false;
        }
    }
    if (now == 1) {
        int cnt = 0;
        if ((p[0] - X[0]) ^ V[0]) {
            ++cnt;
        }
        if (i == n - i) {
            if ((p[i] - X[0]) ^ V[0]) {
                ++cnt;
            }
        }
        return cnt < 2;
    }
    int cnt = 0;
    for (int o = 0; o < 2; ++o) {
        if (num[o] != 1) continue;
        cnt = 0;
        if ((p[0] - X[o ^ 1]) ^ V[o ^ 1]) {
            cnt++;
        }
        if (i == n - i) {
            if ((p[i] - X[o ^ 1]) ^ V[o ^ 1]) {
                ++cnt;
            }
        }
        if (cnt == 0) {
            int j = (pos[o] == 1) ? 2 : 1;
            if ((sgn((p[j] - X[o ^ 1]) ^ V[o ^ 1]) == sgn((p[pos[o]] - X[o ^ 1]) ^ V[o ^ 1])) ||
                sgn((p[n - j] - X[o ^ 1]) ^ V[o ^ 1]) == sgn((p[n - pos[o]] - X[o ^ 1]) ^ V[o ^ 1])) {
                return true;
            }
        }
    }
    return false;
}

bool check2(Point p[]) {
    Point X[2], V[2];
    int num[2] = {0, 0}, pos[2] = {0, 0}, now = 0, i;
    for (i = 0; i < n - 1 - i; ++i) {
        if (!Add(X, V, num, pos, now, p[i], p[n - 1 - i], i)) {
            return false;
        }
    }
    if (now == 1) {
        return true;
    }
    for (int o = 0; o < 2; ++o) {
        if (num[o] == 1) {
            int j = (pos[o] == 0) ? 1 : 0;
            if (sgn((p[j] - X[o]) ^ V[o]) == sgn((p[pos[o ^ 1]] - X[o]) ^ V[o]) ||
                sgn((p[n - 1 - j] - X[o]) ^ V[o]) == sgn((p[n - 1 - pos[o ^ 1]] - X[o]) ^ V[o])) {
                return true;
            }
        }
    }
    return false;
}

bool solve() {
    for (int i = 0; i < n; ++i) {
        if (check1(P + i)) {
            return true;
        }
    }
    if (n % 2 == 0) {
        for (int i = 0; i < n; ++i) {
            if (check2(P + i)) {
                return true;
            }
        }
    }
    return false;
}

int main() {
//    freopen("input.txt", "r", stdin)
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        for (int i = 0; i < n; ++i) {
            P[i].input();
            P[i + n] = P[i];
        }
        if (n < 5) {
            puts("Y");
            continue;
        }
        puts(solve() ? "Y" : "N");
    }
    return 0;
}

```