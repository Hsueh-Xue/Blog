---
title: The 2018 ACM-ICPC Asia Beijing Regional Contest
date:  2019-10-14 22:00:00 +0800
tags: [Regional]
description: The 2018 ACM-ICPC Asia Beijing Regional Contest
---

## A. Jin Yong’s Wukong Ranking List

签到

```C++

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...) do { cout << #x << " -> "; err(x); } while (0)

void err() { cout << endl; }

template<class T, class... Ts>
void err(const T &arg, const Ts &... args) {
    cout << arg << ' ';
    err(args...);
}


#define endl "\n"
using ll = long long;
using db = double;

const int N = 5e2 + 10;

string s[N][2];
map<string, int> mp;
int tot;

int getId(const string &str) {
    if (mp.count(str)) return mp[str];
    mp[str] = ++tot;
    return mp[str];
}

vector<vector<int>> G;
int d[N];

bool gao(int m) {
    memset(d, 0, sizeof d);
    G.clear();G.resize(tot + 1);
    for (int i = 1; i <= m; ++i) {
        int u = getId(s[i][0]), v= getId(s[i][1]);
        G[u].push_back(v);
        ++d[v];
    }
    queue<int>q;
    for (int i =1; i <= tot; ++i) {
        if (d[i]) continue;
        q.push(i);
    }
    int cnt = 0;
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        ++cnt;
        for (auto &v : G[u]) {
            if (--d[v] == 0) {
                q.push(v);
            }
        }
    }
    return cnt != tot;
}

int n;

void RUN() {
    while (cin >> n) {
        mp.clear();
        tot = 0;
        for (int i = 1; i <= n; ++i){
            cin >> s[i][0] >> s[i][1];
            getId(s[i][0]);
            getId(s[i][1]);
        }
        bool F = true;
        for (int i = 1; i <= n; ++i) {
            if (gao(i)) {
                cout << s[i][0] << " " << s[i][1] << endl;
                F = false;
                break;
            }
        }
        if (F) cout << "0\n";
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif

    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    
    RUN();

#ifdef LOCAL_JUDGE
    cerr << "Time elapsed: " << 1.0 * clock() / CLOCKS_PER_SEC << " s.\n";
#endif
    return 0;
}

```

## B. Heshen's Account Book

模拟题

$Tips：$

- 注意行末和下一行的连接

```C++

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...) do { cout << #x << " -> "; err(x); } while (0)

void err() { cout << endl; }

template<class T, class... Ts>
void err(const T &arg, const Ts &... args) {
    cout << arg << ' ';
    err(args...);
}

#define endl "\n"
using ll = long long;
using db = double;
using pSI = pair<string, int>;

const int N = 2e5 + 10;

int vis[N], num[N], pos;
string s, t;

bool f(const string &str) {
    int len = str.size();
    if (len == 1) {
        return isdigit(str[0]);
    }
    if (str[0] == '0') return false;
    for (auto &c : str) {
        if (!isdigit(c)) {
            return false;
        }
    }
    return true;
}

int len;

pSI get() {
    pSI tmp = pSI("", -1);
    while (pos < len && t[pos] == ' ') ++pos;
    while (pos < len && t[pos] != ' ') {
        if (tmp.second == -1) tmp.second = vis[pos];
        tmp.first += t[pos];
        ++pos;
    }
    return tmp;
}

void RUN() {
    s = t = "";
    int pre = -1;
    int n = 0;
    while (getline(cin, s)) {
        ++n;
        if (!t.empty() && isdigit(t.end()[-1]) && isdigit(s[0])) {
            t += s;
        } else {
            t += " ";
            t += s;
            ++pre;
        }
        int length = t.size();
        for (int i = pre + 1; i < length; ++i) vis[i] = n;
        pre = length - 1;
    }
    len = t.size();
    pos = 0;
    vector<string> vec;
    while (true) {
        pSI tmp = get();
        if (tmp.second == -1) break;
        if (f(tmp.first)) {
            ++num[tmp.second];
            vec.push_back(tmp.first);
        }
    }
    for (int i = 0, sze = vec.size(); i < sze; ++i) {
        cout << vec[i] << " \n"[i == sze - 1];
    }
    for (int i = 1; i <= n; ++i) {
        cout << num[i] << endl;
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif

    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    
    RUN();

#ifdef LOCAL_JUDGE
    cerr << "Time elapsed: " << 1.0 * clock() / CLOCKS_PER_SEC << " s.\n";
#endif
    return 0;
}

```

## D. Frog and Portal

构造

一个数可以被拆解成若干个斐波那契数，那么构造一种传送门方案使得走法是若干个斐波那契数累加即可

```C++

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...) do { cout << #x << " -> "; err(x); } while (0)

void err() { cout << endl; }

template<class T, class... Ts>
void err(const T &arg, const Ts &... args) {
    cout << arg << ' ';
    err(args...);
}


#define endl "\n"
using ll = long long;
using db = double;

const int N = 2e2 + 10;

ll m;
ll f[N];
int a[N];

void RUN() {
    f[0] = f[1] = 1;
    for (int i = 2; i <= 50; ++i) {
        f[i] = f[i - 1] + f[i - 2];
    }
    while (scanf("%lld", &m) != EOF) {
        if (m == 0) {
            puts("2\n1 1\n2 1");
            continue;
        }
        *a = 0;
        for (int i = 50; i >= 0; --i) {
            if(m >= f[i]) {
                a[++*a] = i;
                m -= f[i];
            }
        }
        printf("%d\n", *a + 1);
        for (int i = 1; i <= *a; ++i) {
            printf("%d %d\n", 2 * i - 1, 200 - a[i]);
        }
        printf("%d %d\n", 2 * *a, 2 * *a);
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif

//    ios::sync_with_stdio(false);
//    cin.tie(nullptr), cout.tie(nullptr);

    RUN();

#ifdef LOCAL_JUDGE
    cerr << "Time elapsed: " << 1.0 * clock() / CLOCKS_PER_SEC << " s.\n";
#endif
    return 0;
}

```

## H. Approximate Matching

题意：给出一个长度为$n$个长度模式串，问有多少个长度为$m$文本串使得$Approximate Matching($找到一个最多一个字符不一样的子串$)$

思路：构建$ACAM$，在$ACAM$上$dp$

$dp[i][j]$表示文本串的$i$个字符在$trie$的$j$节点转移即可

```C++

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...) do { cout <<  #x << " -> "; err(x); } while (0)

void err() { cout << endl; }

template<class T, class... Ts>
void err(const T &arg, const Ts &... args) {
    cout << arg << ' ';
    err(args...);
}


#define endl "\n"
using ll = long long;
using db = double;

const int N = 1e4 + 10;

int n, m;
char str[N];
ll f[50][N];

struct ACAM {
    struct node {
        int nx[2], fail;
        int cnt;

        node() {
            memset(nx, -1, sizeof nx);
            cnt = 0;
        }
    } t[N];
    
    int root, tot;
    
    int newnode() {
        ++tot;
        t[tot] = node();
        return tot;
    }
    
    void Init() {
        tot = 0;
        root = newnode();
    }
    
    void insert(char *s) {
        int len = strlen(s);
        int now = root;
        for (int i = 0; i < len; ++i) {
            if (t[now].nx[s[i] - '0'] == -1)
                t[now].nx[s[i] - '0'] = newnode();
            now = t[now].nx[s[i] - '0'];
        }
        ++t[now].cnt;
    }
    
    void build() {
        queue<int> q;
        t[root].fail = root;
        for (int i = 0; i < 2; ++i) {
            if (t[root].nx[i] == -1) {
                t[root].nx[i] = root;
            } else {
                t[t[root].nx[i]].fail = root;
                q.push(t[root].nx[i]);
            }
        }
        while (!q.empty()) {
            int now = q.front();
            q.pop();
            for (int i = 0; i < 2; ++i) {
                if (t[now].nx[i] == -1) {
                    t[now].nx[i] = t[t[now].fail].nx[i];
                } else {
                    t[t[now].nx[i]].fail = t[t[now].fail].nx[i];
                    q.push(t[now].nx[i]);
                }
            }
        }
    }
    
    ll gao() {
        for (int i = 0; i <= m; ++i)
            for (int j = 0; j <= tot; ++j)
                f[i][j] = 0;
        f[0][root] = 1;
        for (int i = 0; i < m; ++i) {
            for (int j = 1; j <= tot; ++j) {
                if (t[j].cnt > 0) continue;
                for (int k = 0; k < 2; ++k) {
                    if (t[j].nx[k] != -1) {
                        f[i + 1][t[j].nx[k]] += f[i][j];
                        continue;
                    }
                }
            }
        }
        ll res = 1ll << m;
        for (int i = 1; i <= tot; ++i) {
            if (t[i].cnt) continue;
            res -= f[m][i];
        }
        return res;
    }
} acam;

void RUN() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d %d", &n, &m);
        scanf("%s", str);
        acam.Init();
        acam.insert(str);
        for (int i = 0; i < n; ++i) {
            str[i] = ((str[i] - '0') ^ 1) + '0';
            acam.insert(str);
            str[i] = ((str[i] - '0') ^ 1) + '0';
        }
        acam.build();
        printf("%lld\n", acam.gao());
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif

//    ios::sync_with_stdio(false);
//    cin.tie(nullptr), cout.tie(nullptr);

    RUN();

#ifdef LOCAL_JUDGE
//    cerr << "Time elapsed: " << 1.0 * clock() / CLOCKS_PER_SEC << " s.\n";
#endif
    return 0;
}

```

## I. Palindromes

题意：询问第$k$大的回文数字

思路：打表找规律

```C++

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...) do { cout << #x << " -> "; err(x); } while (0)

void err() { cout << endl; }

template<class T, class... Ts>
void err(const T &arg, const Ts &... args) {
    cout << arg << ' ';
    err(args...);
}


#define endl "\n"
using ll = long long;
using db = double;

const int N = 1e6 + 10;

char str[N];

void RUN() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%s", str + 1);
        int len = strlen(str + 1);
        if (len == 1) {
            printf("%c\n", str[1] - 1);
            continue;
        }
        if (str[1] > '1') {
            str[1]--;
            printf("%s", str + 1);
            for (int i = len - 1; i >= 1; --i) {
                putchar(str[i]);
            }
        } else {
            if (str[2] == '0') {
                for (int i = 1; i < len; ++i)
                    str[i] = str[i + 1];
                str[1] = '9';
                str[len] = 0; len--;
                printf("%s", str + 1);
                reverse(str + 1, str + len);
                str[len] = 0;
                printf("%s", str + 1);
            } else {
                for (int i = 1; i < len; ++i)
                    str[i] = str[i + 1];
                str[len] = 0; len--;
                printf("%s", str + 1);
                reverse(str + 1, str + len + 1);
                printf("%s", str + 1);
            }
        }
        puts("");
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif

//    ios::sync_with_stdio(false);
//    cin.tie(nullptr), cout.tie(nullptr);

    RUN();

#ifdef LOCAL_JUDGE
    cerr << "Time elapsed: " << 1.0 * clock() / CLOCKS_PER_SEC << " s.\n";
#endif
    return 0;
}

```

## J. Rikka with Triangles

题意：给出$n$个点，求所有锐角三角形的面积和。$n\leq2000$

思路：枚举每个点进行极角排序，双指针枚举计算所有三角形面积$rea1$以及直角三角形和钝角三角形面积和$res2$那么答案为$\frac{res1 - 2 \times res2}{3}$

```C++

#include <bits/stdc++.h>

using namespace std;

#define dbg(x...) do { cout << "\033[32;1m" << #x << " -> "; err(x); } while (0)

void err() { cout << "\033[39;0m" << endl; }

template<class T, class... Ts>
void err(const T &arg, const Ts &... args) {
    cout << arg << ' ';
    err(args...);
}


#define endl "\n"
using ll = long long;
using db = double;
using LL = __int128;

const int N = 5e3 + 10;
const ll mod = 998244353;

template<class T>
inline bool read(T &ret) {
    char c;
    int sgn;
    if (c = getchar(), c == EOF) return false;
    while (c != '-' && !isdigit(c)) c = getchar();
    sgn = (c == '-') ? -1 : 1;
    ret = (c == '-') ? 0 : (c - '0');
    while (c = getchar(), isdigit(c)) ret = ret * 10 + (c - '0');
    ret *= sgn;
    return true;
}

ll qpow(ll x, ll n) {
    ll res = 1;
    while (n) {
        if (n & 1) {
            res = res * x % mod;
        }
        x = x * x % mod;
        n >>= 1;
    }
    return res;
}

int n;

struct Point {
    LL x, y;

    Point() {}

    Point(LL x, LL y) : x(x), y(y) {}

    Point operator-(const Point &other) const {
        return {x - other.x, y - other.y};
    }

    Point operator+(const Point &other) const {
        return {x + other.x, y + other.y};
    }

    LL operator^(const Point &other) const {
        return x * other.y - y * other.x;
    }

    bool operator<(const Point &other) const {
        if (y < 0 && other.y > 0) return true;
        if (y > 0 && other.y < 0) return false;
        if (y == 0 && other.y == 0) {
            return (x < 0 && other.x > 0);
        } else {
            return ((*this) ^ other) > 0;
        }
    }
} p[N], qrr[N];

LL sumx[N], sumy[N];

LL gao() {
    LL res1 = 0, res2 = 0;
    for (int i = 1; i <= n; ++i) {
        int cnt = 0;
        for (int j = 1; j <= n; ++j) {
            if (i == j) continue;
            qrr[++cnt] = p[j] - p[i];
        }
        sort(qrr + 1, qrr + 1 + cnt);
        for (int j = 1; j <= cnt; ++j) {
            qrr[j + cnt] = qrr[j];
        }
        sumx[0] = sumy[0] = 0;
        cnt <<= 1;
        for (int j = 1; j <= cnt; ++j) {
            sumx[j] = (sumx[j - 1] + qrr[j].x) % mod;
            sumy[j] = (sumy[j - 1] + qrr[j].y) % mod;
        }
        int q = 1, s = 1, t = 1;
        for (int j = 1; j < n; ++j) {
            q = max(q, j);
            Point _90 = Point(-qrr[j].y, qrr[j].x);
            Point _180 = Point(-qrr[j].x, -qrr[j].y);
            while (q < cnt && (qrr[q + 1] ^ qrr[j]) == 0) ++q;
            while (s < cnt && (s < q || ((qrr[s + 1] ^ _90) > 0 && (qrr[s + 1] ^ qrr[j]) <= 0))) ++s;
            while (t < cnt && (t < s || ((qrr[t + 1] ^ _180) > 0 && (qrr[t + 1] ^ _90) <= 0))) ++t;
            res1 = (res1 + (sumy[s] - sumy[q] + mod) % mod * (qrr[j].x % mod) % mod -
                    (sumx[s] - sumx[q] + mod) % mod * (qrr[j].y) % mod + mod) % mod;
            res2 = (res2 + (sumy[t] - sumy[s] + mod) % mod * (qrr[j].x % mod) % mod -
                    (sumx[t] - sumx[s] + mod) % mod * (qrr[j].y) % mod + mod) % mod;
        }
    }
    return ((res1 - res2 * 2 % mod + mod) % mod * qpow(3, mod - 2) % mod) % mod;
}

void RUN() {
    int T;
    read(T);
    while (T--) {
        read(n);
        for (int i = 1; i <= n; ++i) {
            read(p[i].x), read(p[i].y);
        }
        ll res = gao();
        printf("%lld\n", res);
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif

//    ios::sync_with_stdio(false);
//    cin.tie(nullptr), cout.tie(nullptr);

    RUN();

#ifdef LOCAL_JUDGE
//    cerr << "Time elapsed: " << 1.0 * clock() / CLOCKS_PER_SEC << " s.\n";
#endif
    return 0;
}

```